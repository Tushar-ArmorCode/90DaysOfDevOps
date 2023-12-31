# 퍼징 Fuzzing

'퍼즈 테스트'라고도 하는 퍼징은 컴퓨터 프로그램에 유효하지 않거나 예상치 못한 임의의 데이터를 입력으로 제공하는 소프트웨어 테스트 기법입니다.
퍼징의 목표는 프로그램이 충돌하거나 의도하지 않은 동작을 나타내어 프로그램의 보안 취약점 및 기타 버그를 식별하는 것입니다.

퍼징은 수동으로 수행하거나 테스트 라이브러리/프레임워크를 사용하여 입력을 만들어서 수행할 수 있습니다.

퍼징을 더 잘 이해하기 위해 다음 코드를 예로 들어보겠습니다:

```go
func DontPanic(s string) {
    if len(s) == 4 {
        if s[0] == 'f' {
            if s[1] == 'u' {
                if s[2] == 'z' {
                    if s[3] == 'z' {
                        panic("error: wrong input")
                    }
                }
            }
        }
    }
}
```

이것은 '문자열'을 유일한 인수로 받아들이는 Go 함수입니다.

함수를 살펴보면, 제공된 입력이 '퍼즈'라는 단어인 경우 한 가지 조건에서만 함수가 'panic'(예: crash)을 일으키는 것처럼 보입니다.

물론 이 함수는 매우 간단하며 보기만 해도 그 동작을 알 수 있습니다.
그러나 더 복잡한 시스템에서는 이러한 실패 지점이 명확하지 않을 수 있으며, 이를 테스트하거나 단위 테스트 케이스를 작성하는 사람이 놓칠 수 있습니다.

이때 퍼징이 유용합니다.

Go 퍼징 라이브러리(Go 1.18부터 표준 언어 라이브러리의 일부)는 테스트 케이스에 대한 많은 입력을 생성한 다음 커버리지와 결과에 따라 어떤 입력이 "흥미로운지"를 결정합니다.이 함수에 대한 퍼즈 테스트를 작성하면 다음과 같은 일이 발생합니다:

1. 퍼징 라이브러리가 작은 문자열부터 시작하여 크기가 커지는 무작위 문자열을 제공하기 시작합니다.
2. 라이브러리가 길이 4의 문자열을 제공하면 테스트 범위의 변경을 감지하고(`if (len(s) == 4)`가 이제 `true`가 됨) 이 길이의 입력을 계속 생성합니다.
3. 라이브러리가 `f`로 시작하는 길이 4의 문자열을 제공하면 테스트 커버리지의 또 다른 변경 사항(`if s[0] == "f"`는 이제 `true`)을 감지하고 `f`로 시작하는 입력을 계속 생성합니다.
4.  `u`와 이중 `z`에 대해서도 같은 일이 반복됩니다.
5. `fuzz`를 입력으로 제공하면 함수가 패닉 상태에 빠지고 테스트가 실패합니다.
6. __퍼즈__에 성공했습니다!

퍼징의 또 다른 좋은 방법은 코드 충돌을 일으킨 입력을 저장하고 매번 실행하여 퍼징을 통해 발견한 원래 오류가 다시는 코드에 도입되지 않도록 하는 것입니다.

이 역시 퍼징 프레임워크의 특징일 수 있습니다.

대부분의 퍼징 라이브러리에서는 테스트하려는 특정 값을 추가할 수 있습니다.
이렇게 하면 우리가 이미 알고 있는 "흥미로운" 값을 라이브러리에 표시하여 생성된 값을 모델링할 수 있으므로 라이브러리에도 도움이 됩니다.

## 퍼징만으로는 충분하지 않은 경우

퍼징은 유용한 기술이지만 도움이 되지 않는 상황도 있습니다.

예를 들어, 코드에 실패하는 입력이 너무 구체적이어서 도움이 되는 단서가 없는 경우 퍼징 라이브러리가 이를 추측하지 못할 수 있습니다.

이전 단락의 예제 코드를 다음과 같이 변경해 보겠습니다.

```go
func DontPanic(s input) {
    if (len(s) == 4) && s[0] == 'f' && s[1] == 'u' && s[2] == 'z' && s[3] == 'z' {
        panic("error")
    }
}
```

또는 

```go
func DontPanic(s input) {
    if s == "fuzz" {
        panic("error")
    }
}
```

이런 경우 퍼징은 아무런 단서 없이 정확한 문자열 `퍼즈`를 생성할 가능성이 적기 때문에 도움이 되지 않습니다.
그리고 이전 사례에서 코드 커버리지 변경을 트리거했던 입력(크기 4의 문자열, `z`로 시작하는 크기 4의 문자열 등)은 이제 코드 커버리지를 트리거하지 않습니다. (이전 예제에서는 `if` 체크가 5개인데 비해 지금은 하나만 있기 때문입니다)

따라서 퍼징은 코드의 이상 징후와 코너 케이스를 감지하는 좋은 방법이지만 100% 올바른 코드를 위한 만병통치약은 아니라는 점을 이해하는 것이 중요합니다.

## 실제 예제

Go에서 퍼징을 직접 해보고 싶으시다면 [해당 주제에 대한 예제가 있는 내 리포지토리](https://github.com/asankov/go-fuzzing-101/tree/v1)를 확인하세요.

여기에는 이 글에서 사용한 예제와 실패를 트리거하는 퍼즈 테스트, 그리고 테스트를 직접 실행하는 방법에 대한 지침이 포함되어 있습니다.

## 리소스

- <https://en.wikipedia.org/wiki/Fuzzing>
- [Fuzzing in Go by Valentin Deleplace, Devoxx Belgium 2022](https://www.youtube.com/watch?v=Zlf3s4EjnFU)
- [Write applications faster and securely with Go by Cody Oss, Go Day 2022](https://www.youtube.com/watch?v=aw7lFSFGKZs)

[17일차](day17.md)에 뵙겠습니다. 
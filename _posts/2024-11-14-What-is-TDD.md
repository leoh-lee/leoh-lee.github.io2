---
title: "What is TDD"
date: "2024-02-07 07:43:54"
categories: [TDD]
tags: [TDD]
---

참조: [https://mangkyu.tistory.com/182](https://mangkyu.tistory.com/182)

## 단위 테스트

단위 테스트(Unit Test)는 하나의 모듈을 기준으로 진행되는 가장 작은 단위의 테스트이다. 여기서 모듈은 자바에서는 객체의 메서드, 타입스크립트에서는 함수일 것이다.

개발할 때 단위 테스트를 작성하는 데에는 여러가지 이유가 있다.

몇 가지 추려보자면

1. 테스트 코드를 믿고 과감하고 안정적인 리팩터링이 가능하다.
2. 개발 및 테스팅 시간과 비용이 절감한다.
3. 코드 수정, 추가, 삭제 시에도 빠르게 작성하고 검증해볼 수 있다.
4. 테스트 코드가 없으면 통합테스트 (화면에서 실행하는 실제 테스트)로 확인해야하는데, 이 경우 버그가 발생할 확률이 너무너무 높다.

정도가 있다.

### 좋은 단위테스트의 특징
단위테스트를 작성해야하는 이유가 많은 만큼 꼭 작성하는 것이 좋다. 그러면 아무렇게나 막 작성하면 될까? 그건 아니다. 로버트 C. 마틴 의 클린코드에서는 다음과 같은 좋은 단위테스트에는 4가지 특징이 있다고 한다.

#### FIRST

1. **Fast**: 테스트는 빠르게 수행되어야 한다.
2. **Isolation**: 각각의 테스트는 서로 의존하지 않고 독립적으로 수행되어야 한다.
3. **Repeatable**: 어느 환경에서도 반복 가능해야 한다.
4. **Self-Validating**: 테스트는 스스로 검증되어야 한다. 성공, 실패 등으로 나타내는 것을 말한다.
5. **Timely**: 테스트는 적시에 작성되어야 한다.

즉, 테스트는 빠르고 독립적으로 어느 환경에서든 반복해서 테스트할 수 있어야하고, 스스로 테스트의 성공여부를 검증할 수 있어야한다.

조금 신경써서 봐야할 부분이 Timely 이다. 테스트는 적시에 작성되어야 한다는데 적시가 언제일까?  
클린코드, 리팩터링 책에 따르면 테스트는 프로덕션 코드를 작성하기 직전에 작성해야 한다고 한다.

프로덕션 코드를 작성하기 직전에 테스트 코드를 작성하는 것. 그것이 바로 **TDD(Test Driven Development)** 이다.

---

## 왜 TDD를 하는가?

개인적으로 TDD를 처음 접했을 때는 개발 시간만 늘어나는 번거로운 작업이라 생각했다. 하지만 실무에서 테스트 코드 없이 도박성 개발을 반복하다보니 테스트 코드의 중요성을 뼈저리게 느끼는 중이다.

토이프로젝트 등으로 TDD로 개발을 해보며 내가 느낀 TDD의 장점은 다음과 같다.

### 1. 내가 뭘 구현해야하는 지 쉽게 알 수 있다

```reasonml
    @Test
    public void 요청에null값이있어서게시글추가실패() {
        //given
        BoardRequest boardRequest = BoardRequest.builder()
                .content("hello").writer("nimoh").category("free").build();
        //when
        final BoardException result = assertThrows(BoardException.class, ()->boardService.save(boardRequest));

        //then
        assertThat(result.getErrorResult()).isEqualTo(BoardErrorResult.REQUEST_VALUE_INVALID);
    }

    @Test
    public void 게시글추가성공() {
        //given

        doReturn(board()).when(boardRepository).save(ArgumentMatchers.any(Board.class));
        //when
        BoardDetailResponse result = boardService.save(boardRequest());
        //then
        assertThat(result.getTitle()).isEqualTo("test");
    }
```

어떤 메서드에 대한 단위테스트인 지, 어떤 테스트인 지 먼저 정하고 개발하기 때문에 TODO를 인지하고 개발하기 좋다. 예시코드의 경우 게시글을 추가하는 단위테스트이다. 게시글 추가 성공, 예외상황에 대한 예외처리 개발을 해야한 다는 것을 알 수 있다.

### 2. 리팩터링이 매우 수월하다.
마틴 파울러의 리팩터링2 책에서도 비슷한 얘기가 언급되어있는데, 테스트 코드 없이 리팩터링하는 것은 매우 위험한 것이다. 생각없이 리팩터링하다보니 코드를 다 날려야하는 경우도 종종 있었다. 테스트 코드를 작성해보니 실제 구현 코드를 아무리 막 리팩터링해도 믿는 구석(?)이 있으니 실수를 발견하는데에 시간이 매우 절약되었다.

### 3. 재밌다.

프론트엔드는 구현한 코드가 어떻게 출력되는 지 바로 확인할 수 있기 때문에 시각적인 즐거움과 피드백이 있다. 그에 반해 백엔드는 검은화면에 하얀 글자밖에 없다. JUnit5 등의 테스트 라이브러리를 사용해서 테스트 코드를 작성하면 테스트 할 때마다 실패하면 빨간색, 성공하면 초록색으로 성공여부가 표시된다. 테스트를 작성하고 실행시킬 때 그 긴장감과 성공했을 때의 짜릿함은 백엔드 작업에 뺼 수 없는 재미이다.

### 4. 테스트를 나중에 작성하기 귀찮다.
나중에 테스트 코드 작성해야지~ 하고 프로덕션 코드를 먼저 작성하면 90% 확률로 테스트코드 작성 안한다.

### 5. 프로덕션 코드가 깔끔하고 객체지향적이다

기능을 구현하는 최소한의 테스트로 개발하기 때문에 실제 프로덕션 코드에 불필요한 코드가 줄어 깔끔해진다.  
그리고 TDD를 할 때 아직 만들어지지 않은 객체를 참조하는 경우가 많다. 때문에, 객체간의 메시지를 위주로 구성할 수 있다.
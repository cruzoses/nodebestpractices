# 에러 세부 사항은 클라이언트로부터 숨겨라

### 한문단 설명
서버 파일 경로, 사용하고 있는 써드 파티 모듈, 그리고 공격자에 의해 부당하게 이용될 수 있는 애플리케이션의 내부 워크플로우 같은 민감한 애플리케이션 세부 사항의 노출 위험 때문에 상용에서 클라이언트의 애플리케이션 세부 사항이 노출되는 것은 피해야 한다. Express는 앱에서 만날 수 있는 모든 에러들을 관리하는 내장된 에러 핸들러를 가지고 있다. 이 기본 에러 핸들링 미들웨어 함수는 미들웨어 함수 스택 끝에 추가된다. `next()`에 에러를 전달하고 사용자 지정 에러 핸들러에서 처리하지 않을 경우, 내장된 Express 에러 핸들러로부터 처리된다. 에러는 스택 추적(stack trace)과 함께 클라이언트에 작성돼야 한다. 이 행동은 `NODE_ENV`가 `development`로 설정될 때 사실이 될 것이다. 그러나 `NODE_ENV`가 `production`으로 설정될 때, 스택 추적이 작성되지 않고 HTTP 응답 코드만 작성된다.

### 코드 예시: Express 에러 핸들러
```javascript
// 상용 에러 핸들러
// 사용자에게 누출된 스택 추적 없음
app.use((err, req, res, next) => {
    res.status(err.status || 500);
    res.render('error', {
        message: err.message,
        error: {}
    });
});
```

### 추가적인 자원
🔗 [Express.js 에러 핸들링 문서](https://expressjs.com/en/guide/error-handling.html)

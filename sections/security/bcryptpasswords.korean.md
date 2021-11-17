# 암호를 처리할 때는 Node.js의 암호 라이브러리 대신 Bcrypt를 써라

### 한 문단 설명

유저 비밀번호를 저장할 때, Node.js의 암호 모듈 대신 [bcrypt npm module](https://www.npmjs.com/package/bcrypt)가 제공하는 bcrypt와 같은 적응 해시 알고리즘의 사용을 권장한다. `Math.randon()`은 이것을 예측 가능하다는 점 때문에 어떠한 경우에도 비밀번호 혹은 토큰 생성 시에 사용되어선 안된다.

`bcrypt`를 사용할 때 보안 해쉬의 제공목적으로 수많은 'rounds'들이 특정될 수 있기 때문에 bcrypt 모듈 혹은 비슷한 것이 자바스크립트 implementation 대신 사용되어야 한다. 이것은 워크팩터(work factor)나 데이터 처리의 목적이 되는 'rounds'의 개수를 정해준다. 더 많은 해싱 rounds는 더 안전한 해쉬(CPU시간을 대가로 지불하긴 하지만)로 이어진다. 해싱 rounds의 도입은 해커들이 한번의 시도에 걸리는 시간을 늘려 비밀번호 탈취작업을 느리게 함으로써 무차별공격 요인이 현저히 감소되었음을 의미한다.

### 코드 예시

```javascript
try {
  // 10개의 해시태그 rounds를 이용해 안전한 비밀번호를 비동기적으로 생성한다
  const hash = await bcrypt.hash("myPassword", 10);
  // 유저 기록에 보안해쉬를 저장한다.

  // 비밀번호 입력값과 저장된 해시를 비교한다.
  const match = await bcrypt.compare("somePassword", hash);
  if (match) {
    // 비밀번호 일치 시
  } else {
    // 비밀번호 불일치 시
  }
} catch {
  logger.error("could not hash password.");
}
```

### 다른 블로거들이 말하길

다음의 블로그로 부터 [Max McCarty](https://dzone.com/articles/nodejs-and-password-storage-with-bcrypt):

> ... 올바른 해싱 알고리즘을 사용하는 것만의 문제가 아니다. 나는 올바른 툴이 어떻게 중요한 재료인 "시간"을 비밀번호 해싱 알고리즘의 일부로 포함함하는지와 이것이 무차별공격을 통해 비밀번호를 탈취하려는 공격자에게 어떤것의 의미하는 지에 대해 줄기차게 말해왔다.
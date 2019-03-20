# CleanCode

## 2장 의미있는 이름
- 의도를 분명히 밝혀라 

## 3장 함수 
- 작게 만들어라 42
- 함수는 한 가지를 해야 한다 44
- 코드는 위에서 아래로 이야기 처럼 읽혀야 좋다. 46
- 길고 서술적인 이름이 > 짧고 어려운 이름 보다 좋다 49
- 함수에서 이상적인 인수 개수는 0, 그다음 1, 2,3, 4개 이상은 이유가 필요함 50

함수에서 인수 1개를 넘기는 이유로 가장 흔한 경우 2가지 
1. 인수에 질문을 던지는 경우 `boolean fileExists("myFile")`
2. 인수를 뭔가로 변환해 결과를 반환하는 경우 `InputStream fileOpen("myFile")` 은 Strin형을 반환

- 플래그 인수는 추하다 52
- 함수 이름에 키워드를 추가하는 형식 - assertEquals() 보다 assertExpectedEqualsActual(expected, actual) 이 좋다.ㄱ
- 함수는 뭔가를 수행하거나 뭔가에 답하거나 둘 중 하나만 해야함 56
- try/catch 블록 뽑아내기 

```java
public void delete(Page page){
  try{
    deletePageAndAllReferences(page);
  }catch(Exception e){
    logError(e)
  }
}
```

```java
private void deletePageAndAllReferences(Page page) throws Exception {
  deletePage(page);
  registry.deleteReference(page.name);
  configKeys.deleteKey(page.name.makeKey());
}

private void logError(Exception e){
  logger.log(e.getMessage());
}
```

- 오류 코드 대신 예외를 사용 한다.

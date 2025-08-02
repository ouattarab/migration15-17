@Test
void testGetAggreg8UserId_Success() throws Exception {
    UserRequest request = UserRequest.builder().email("test@example.com").build();

    String tokenJson = "{\"token\":\"abc123\"}";
    String userJson = "{\"id\":\"user-123\"}";

    Token token = new Token();
    token.setToken("abc123");

    UserResponse userResponse = new UserResponse();
    userResponse.setId("user-123");

    Aggreg8 aggreg8 = Mockito.spy(new Aggreg8());

    // Spies sur getApiExt
    Mockito.doReturn(tokenJson).when(aggreg8)
           .getApiExt(any(), eq(Aggreg8Constants.AGGREG8_URL_GET_TOKEN), any(), any());
    Mockito.doReturn(userJson).when(aggreg8)
           .getApiExt(any(), eq(Aggreg8Constants.AGGREG8_URL_GET_USER), any(), any());

    // Simule le parsing JSON
    ObjectMapper objectMapper = Mockito.mock(ObjectMapper.class);
    Mockito.doReturn("{\"email\":\"test@example.com\"}")
           .when(objectMapper).writeValueAsString(any());
    Mockito.doReturn(token).when(objectMapper).readValue(tokenJson, Token.class);
    Mockito.doReturn(userResponse).when(objectMapper).readValue(userJson, UserResponse.class);

    // Injection manuelle
    Field field = Aggreg8.class.getDeclaredField("objectMapper");
    field.setAccessible(true);
    field.set(aggreg8, objectMapper);

    String result = aggreg8.getAggreg8UserId(request);
    assertEquals("user-123", result);
}
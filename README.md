@SpringBootTest
class Aggreg8IntegrationTest {

    @Autowired
    private Aggreg8 aggreg8;

    @MockBean
    private ReferentialService refService;

    @MockBean
    private RestTemplate restTemplate;

    @MockBean
    private ObjectMapper objectMapper;

    @Test
    void testGetExternalWebCourseTokenAndId_success() throws Exception {
        // 1. Préparation des données
        UserFlowInitAddBankRequest request = new UserFlowInitAddBankRequest();
        String requestJson = "{\"some\":\"value\"}";
        String responseJson = "{\"token\":\"abc123\", \"userFlowId\":\"id123\"}";

        UserFlowInitAddBankResponse expectedResponse = new UserFlowInitAddBankResponse();
        expectedResponse.setToken("abc123");
        expectedResponse.setUserFlowId("id123");

        // 2. Mocks pour initConfigAggreg8()
        when(refService.getReferentialValue(any(), eq(Aggregg8Constants.AGGREG8_SECRET_REF), anyInt()))
            .thenReturn(new ReferentialValue("dummy-secret"));

        when(refService.getReferentialValue(any(), eq(Aggregg8Constants.AGGREG8_DOMAIN_REF), anyInt()))
            .thenReturn(new ReferentialValue("https://fake-url.com"));

        when(refService.getReferentialValue(any(), eq(Aggregg8Constants.AGGREG8_VS_ACTIVATED), anyInt()))
            .thenReturn(new ReferentialValue("1"));

        // ... autres appels à getReferentialValue si nécessaire ...

        // 3. Mock de la sérialisation ObjectMapper
        when(objectMapper.writeValueAsString(request)).thenReturn(requestJson);

        // 4. Mock de l'appel API externe
        when(restTemplate.exchange(
                anyString(),
                eq(HttpMethod.POST),
                any(HttpEntity.class),
                eq(String.class)
        )).thenReturn(new ResponseEntity<>(responseJson, HttpStatus.OK));

        // 5. Mock de la désérialisation de la réponse
        when(objectMapper.readValue(responseJson, UserFlowInitAddBankResponse.class))
            .thenReturn(expectedResponse);

        // 6. Appel de la méthode testée
        UserFlowInitAddBankResponse result = aggreg8.getExternalWebCourseTokenAndId(request);

        // 7. Vérifications
        assertNotNull(result);
        assertEquals("abc123", result.getToken());
        assertEquals("id123", result.getUserFlowId());
    }
}
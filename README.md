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
    void getExternalWebCourseTokenAndId_success() throws Exception {
        // 1. Préparation des données d'entrée et sortie
        UserFlowInitAddBankRequest request = new UserFlowInitAddBankRequest();
        String requestJson = "{\"some\":\"value\"}";
        String responseJson = "{\"token\":\"abc123\", \"userFlowId\":\"id123\"}";

        UserFlowInitAddBankResponse expectedResponse = new UserFlowInitAddBankResponse();
        expectedResponse.setToken("abc123");
        expectedResponse.setUserFlowId("id123");

        // 2. Mocks pour les valeurs de configuration (initConfigAggreg8)
        Object mockSecret = Mockito.mock(Object.class);
        Mockito.when(mockSecret.getRefLibTwo()).thenReturn("dummy-secret");
        Mockito.when(refService.getReferentialValue(any(), eq(Aggregg8Constants.AGGREG8_SECRET_REF), anyInt()))
               .thenReturn(mockSecret);

        Object mockDomain = Mockito.mock(Object.class);
        Mockito.when(mockDomain.getRefLibTwo()).thenReturn("https://fake-url.com");
        Mockito.when(refService.getReferentialValue(any(), eq(Aggregg8Constants.AGGREG8_DOMAIN_REF), anyInt()))
               .thenReturn(mockDomain);

        Object mockActivated = Mockito.mock(Object.class);
        Mockito.when(mockActivated.getRefLibTwo()).thenReturn("1");
        Mockito.when(refService.getReferentialValue(any(), eq(Aggregg8Constants.AGGREG8_VS_ACTIVATED), anyInt()))
               .thenReturn(mockActivated);

        // 3. Mock de la sérialisation de l'objet request
        Mockito.when(objectMapper.writeValueAsString(request)).thenReturn(requestJson);

        // 4. Mock de l'appel HTTP simulé via restTemplate
        Mockito.when(restTemplate.exchange(
                anyString(),
                eq(HttpMethod.POST),
                any(HttpEntity.class),
                eq(String.class)
        )).thenReturn(new ResponseEntity<>(responseJson, HttpStatus.OK));

        // 5. Mock de la désérialisation de la réponse JSON
        Mockito.when(objectMapper.readValue(responseJson, UserFlowInitAddBankResponse.class))
               .thenReturn(expectedResponse);

        // 6. Appel de la méthode testée
        UserFlowInitAddBankResponse result = aggreg8.getExternalWebCourseTokenAndId(request);

        // 7. Vérifications
        assertNotNull(result);
        assertEquals("abc123", result.getToken());
        assertEquals("id123", result.getUserFlowId());
    }
}
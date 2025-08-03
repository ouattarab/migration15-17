@SpringBootTest
class Aggreg8PrepareRedirectionUrlTest {

    @Autowired
    private Aggreg8 aggreg8;

    @MockBean
    private IApiOrchestratorService apiOrchestratorService;

    // Spy uniquement si tu veux mocker les méthodes internes
    private Aggreg8 spyAggreg8;

    @BeforeEach
    void setup() {
        spyAggreg8 = Mockito.spy(aggreg8);
    }

    @Test
    void testPrepareRedirectionUrl_Success() throws Exception {
        // Given
        PrepareRedirectionUrlDto dto = mock(PrepareRedirectionUrlDto.class);
        when(dto.getBankCode()).thenReturn("BANK123");
        when(dto.getEmail()).thenReturn("test@example.com");
        when(dto.getIssuerUrl()).thenReturn("http://issuer.com");
        when(dto.getWebCourseId()).thenReturn("WEB123");

        // Simuler getBank
        BankAggreg8ResponseDto bankDto = new BankAggreg8ResponseDto();
        bankDto.set_id("BANK123");
        doReturn(bankDto).when(spyAggreg8).getBank("BANK123");

        // Simuler getAggreg8UserId
        doReturn("externalUser123").when(spyAggreg8)
            .getAggreg8UserId(argThat(req -> "test@example.com".equals(req.getEmail())));

        // Simuler réponse A8
        UserFlowInitAddBankResponse mockedResponse = mock(UserFlowInitAddBankResponse.class);
        when(mockedResponse.getToken()).thenReturn("mock-token");
        when(mockedResponse.getUserFlowId()).thenReturn("flow-123");

        doReturn(mockedResponse).when(spyAggreg8).getExternalWebCourseTokenAndId(any());

        // Simuler URL générée
        doReturn("http://redirect.local/mock-token").when(spyAggreg8).generateRedirectionUrl("mock-token");

        // When
        String result = spyAggreg8.prepareRedirectionUrl(dto);

        // Then
        assertEquals("http://redirect.local/mock-token", result);

        verify(apiOrchestratorService).saveExternalCustomerIdWithEmail(
            eq(Aggreg8Constants.AGGREG8_PROVIDER_ID),
            eq("externalUser123"),
            eq("test@example.com"),
            eq("WEB123"),
            eq("flow-123")
        );
    }
}
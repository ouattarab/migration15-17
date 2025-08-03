@ExtendWith(MockitoExtension.class)
class Aggreg8Test {

    @InjectMocks
    private Aggreg8 aggreg8;

    @Mock
    private IReferentialService refService;

    @Mock
    private IApiOrchestratorService apiOrchestratorService;

    @Mock
    private IAispSettingsService aispSettingsService;

    @BeforeEach
    void setUp() {
        // important pour ne pas lancer initConfigAggreg8
        aggreg8.setTestInProgress(true);
    }

    @Test
    void testGetBanks_success() throws Exception {
        // Given : un token JSON en retour d'API
        String tokenJson = "{\"token\": \"mocked-token-value\"}";
        String banksJson = "{\"_id\": \"BANK123\"}";

        // Simuler getApiExt : 1er appel retourne le token, 2e appel retourne la réponse avec banque
        Mockito.when(aggreg8.getApiExt(
                anyString(), eq(Aggreg8Constants.AGGREG8_TOKEN), isNull(), isNull(), any()))
            .thenReturn(tokenJson);

        Mockito.when(aggreg8.getApiExt(
                anyString(), eq(Aggreg8Constants.AGGREG8_URL_GET_BANKS), isNull(), isNull(), any()))
            .thenReturn(banksJson);

        // When
        BankAggreg8ResponseDto result = aggreg8.getBanks();

        // Then
        assertNotNull(result);
        assertEquals("BANK123", result.get_id());
    }
}
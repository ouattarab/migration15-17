@SpringBootTest
public class Aggreg8Test {

    @Autowired
    private Aggreg8 aggreg8;

    @MockBean
    private IReferentialService referentialService;

    @MockBean
    private ExternalBankAccountDetailsRepository externalBankAccountDetailsRepository;

    @MockBean
    private ExternalBankAccountActionRepository externalBankAccountActionRepository;

    // etc. selon tes dépendances...

    // ✅ Cas 1 : banque trouvée
    @Test
    public void testGetBank_Success() throws Exception {
        // Given
        String bankId = "bank123";

        BankAggreg8ResponseDto dto = new BankAggreg8ResponseDto();
        dto.set_id("bank123");

        Aggreg8 aggreg8Spy = Mockito.spy(aggreg8); // nécessaire juste pour override getBanks()
        Mockito.doReturn(new BankAggreg8ResponseDto[]{dto}).when(aggreg8Spy).getBanks();

        // When
        BankAggreg8ResponseDto result = aggreg8Spy.getBank(bankId);

        // Then
        assertNotNull(result);
        assertEquals("bank123", result.get_id());
    }

    // ❌ Cas 2 : banque non trouvée → exception
    @Test
    public void testGetBank_NotFound() throws Exception {
        // Given
        String bankId = "not-found";

        BankAggreg8ResponseDto dto = new BankAggreg8ResponseDto();
        dto.set_id("another-id");

        Aggreg8 aggreg8Spy = Mockito.spy(aggreg8);
        Mockito.doReturn(new BankAggreg8ResponseDto[]{dto}).when(aggreg8Spy).getBanks();

        // When / Then
        FunctionalException ex = assertThrows(
            FunctionalException.class,
            () -> aggreg8Spy.getBank(bankId)
        );
        assertEquals(Aggreg8ErrorConstants.ERROR_AGGREG8_BANK_NOT_FOUND, ex.getCode());
    }
}
@ExtendWith(MockitoExtension.class)
class Aggreg8Test {

    @InjectMocks
    private Aggreg8 aggreg8;

    @Mock
    private SomeDependency dependency; // à adapter si getBanks() dépend d'un service

    // Simulation de la méthode getBanks() via un spy
    @Spy
    @InjectMocks
    private Aggreg8 spyAggreg8;

    @Test
    void getBank_shouldReturnMatchingBank_whenBankExists() throws Exception {
        // given
        String bankId = "123";
        BankAggreg8ResponseDto bank1 = new BankAggreg8ResponseDto();
        bank1.set_id("123");

        BankAggreg8ResponseDto bank2 = new BankAggreg8ResponseDto();
        bank2.set_id("456");

        BankAggreg8ResponseDto[] banks = new BankAggreg8ResponseDto[]{bank1, bank2};

        Mockito.doReturn(banks).when(spyAggreg8).getBanks();

        // when
        BankAggreg8ResponseDto result = spyAggreg8.getBank(bankId);

        // then
        assertNotNull(result);
        assertEquals("123", result.get_id());
    }

    @Test
    void getBank_shouldThrowFunctionalException_whenBankNotFound() throws Exception {
        // given
        String bankId = "999";
        BankAggreg8ResponseDto bank1 = new BankAggreg8ResponseDto();
        bank1.set_id("123");

        BankAggreg8ResponseDto[] banks = new BankAggreg8ResponseDto[]{bank1};

        Mockito.doReturn(banks).when(spyAggreg8).getBanks();

        // when + then
        FunctionalException exception = assertThrows(
            FunctionalException.class,
            () -> spyAggreg8.getBank(bankId)
        );

        assertEquals(Aggregg8ErrorConstants.ERROR_AGGREG8_BANK_NOT_FOUND, exception.getMessage());
    }
}
@Test
public void testGetBanks_success() throws Exception {
    // Given : un token JSON en retour de l'API
    String tokenJson = "{\"token\": \"mocked-token-value\"}";
    String banksJson = "[{\"_id\": \"BANK123\"}]";

    // Simuler getReferentialValue (mock de refService)
    Mockito.when(referentialService.getReferentialValue(
            eq(ReferentielEntreprise.class),
            eq(Aggreg8Constants.AGGREG8_GRP_REF),
            anyInt(),
            anyInt()))
        .thenReturn(() -> "dummy"); // ou un mock qui a getRefLibTwo()

    // Même chose pour toutes les autres propriétés attendues dans initConfigAggreg8()
    Mockito.when(referentialService.getReferentialValue(
            eq(ReferentielEntreprise.class),
            eq(Aggreg8Constants.AGGREG8_PRIVATE_KEY_REF),
            anyInt(),
            anyInt()))
        .thenReturn(() -> "privateKey");

    Mockito.when(referentialService.getReferentialValue(
            eq(ReferentielEntreprise.class),
            eq(Aggreg8Constants.AGGREG8_APP_ID_REF),
            anyInt(),
            anyInt()))
        .thenReturn(() -> "appId");

    Mockito.when(referentialService.getReferentialValue(
            eq(ReferentielEntreprise.class),
            eq(Aggreg8Constants.AGGREG8_SECRET_REF),
            anyInt(),
            anyInt()))
        .thenReturn(() -> "secret");

    Mockito.when(referentialService.getReferentialValue(
            eq(ReferentielEntreprise.class),
            eq(Aggreg8Constants.AGGREG8_DOMAIN_REF),
            anyInt(),
            anyInt()))
        .thenReturn(() -> "domain");

    Mockito.when(referentialService.getReferentialValue(
            eq(ReferentielEntreprise.class),
            eq(Aggreg8Constants.AGGREG8_VS_ACTIVATED),
            anyInt(),
            anyInt()))
        .thenReturn(() -> "1"); // Pour isEnabledAggreg8VS

    // Simuler getApiExt : 1er appel retourne le token, 2e appel retourne la réponse avec banques
    Mockito.when(aggreg8.getApiExt(
            anyString(), eq(Aggreg8Constants.AGGREG8_TOKEN), isNull(), isNull(), any(HttpHeaders.class)))
        .thenReturn(tokenJson);

    Mockito.when(aggreg8.getApiExt(
            anyString(), eq(Aggreg8Constants.AGGREG8_URL_GET_BANKS), isNull(), isNull(), any(HttpHeaders.class)))
        .thenReturn(banksJson);

    // When
    BankAggreg8ResponseDto[] result = aggreg8.getBanks();

    // Then
    assertNotNull(result);
    assertEquals("BANK123", result[0].get_id()); // ou result[0].getId() si Lombok génère getId()
}
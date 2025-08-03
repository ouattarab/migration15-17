@Test
public void testGetBanks_success() throws Exception {
    // Given
    String tokenJson = "{\"token\":\"mocked-token-value\"}";
    String banksJson = "[{\"_id\":\"BANK123\"}]";

    // Mock retour de getApiExt pour le token
    Mockito.when(aggreg8.getApiExt(
            Mockito.anyString(),
            eq(Aggreg8Constants.AGGREG8_TOKEN),
            Mockito.isNull(),
            Mockito.isNull(),
            Mockito.any()
    )).thenReturn(tokenJson);

    // Mock retour de getApiExt pour les banques
    Mockito.when(aggreg8.getApiExt(
            Mockito.anyString(),
            eq(Aggreg8Constants.AGGREG8_URL_GET_BANKS),
            Mockito.isNull(),
            Mockito.isNull(),
            Mockito.any()
    )).thenReturn(banksJson);

    // Mock les valeurs référentielles nécessaires pour initConfigAggreg8()
    ReferentielEntreprise mockPrivateKey = Mockito.mock(ReferentielEntreprise.class);
    Mockito.when(mockPrivateKey.getRefLibTwo()).thenReturn("private-key");

    ReferentielEntreprise mockAppId = Mockito.mock(ReferentielEntreprise.class);
    Mockito.when(mockAppId.getRefLibTwo()).thenReturn("app-id");

    ReferentielEntreprise mockSecret = Mockito.mock(ReferentielEntreprise.class);
    Mockito.when(mockSecret.getRefLibTwo()).thenReturn("secret");

    ReferentielEntreprise mockDomain = Mockito.mock(ReferentielEntreprise.class);
    Mockito.when(mockDomain.getRefLibTwo()).thenReturn("domain");

    ReferentielEntreprise mockActivated = Mockito.mock(ReferentielEntreprise.class);
    Mockito.when(mockActivated.getRefLibTwo()).thenReturn("1");

    Mockito.when(referentialService.getReferentialValue(eq(ReferentielEntreprise.class), eq(Aggreg8Constants.AGGREG8_PRIVATE_KEY_REF), anyInt(), anyInt())).thenReturn(mockPrivateKey);
    Mockito.when(referentialService.getReferentialValue(eq(ReferentielEntreprise.class), eq(Aggreg8Constants.AGGREG8_APP_ID_REF), anyInt(), anyInt())).thenReturn(mockAppId);
    Mockito.when(referentialService.getReferentialValue(eq(ReferentielEntreprise.class), eq(Aggreg8Constants.AGGREG8_SECRET_REF), anyInt(), anyInt())).thenReturn(mockSecret);
    Mockito.when(referentialService.getReferentialValue(eq(ReferentielEntreprise.class), eq(Aggreg8Constants.AGGREG8_DOMAIN_REF), anyInt(), anyInt())).thenReturn(mockDomain);
    Mockito.when(referentialService.getReferentialValue(eq(ReferentielEntreprise.class), eq(Aggreg8Constants.AGGREG8_VS_ACTIVATED), anyInt(), anyInt())).thenReturn(mockActivated);

    // Act
    BankAggreg8ResponseDto[] result = aggreg8.getBanks();

    // Assert
    assertNotNull(result);
    assertEquals(1, result.length);
    assertEquals("BANK123", result[0].get_id());
}
@ExtendWith(MockitoExtension.class)
class Aggre8Test {

    @InjectMocks
    private Aggre8 aggre8;

    @Mock
    private ReferentialService refService;

    @Mock
    private ObjectMapper objectMapper;

    @Mock
    private UserResponse mockResponse;

    @BeforeEach
    void setUp() throws Exception {
        // setup si besoin
        ReflectionTestUtils.setField(aggre8, "testInProgress", false);
        ReflectionTestUtils.setField(aggre8, "aggre8AppId", "dummyAppId");
        ReflectionTestUtils.setField(aggre8, "aggre8Domain", "dummyDomain");
        ReflectionTestUtils.setField(aggre8, "isEnabledAggre8V5", true);
    }

    @Test
    void shouldReturnUserId_whenApiReturnsValidResponse() throws Exception {
        // Arrange
        UserRequest request = new UserRequest();
        String jsonString = "{}";
        String apiResponse = "{\"id\":\"USER123\"}";

        when(objectMapper.writeValueAsString(any())).thenReturn(jsonString);
        when(aggre8.getApiExt(anyString(), anyString(), any(), any(), anyString(), any()))
            .thenReturn(apiResponse);
        when(objectMapper.readValue(eq(apiResponse), eq(UserResponse.class))).thenReturn(mockResponse);
        when(mockResponse.get_id()).thenReturn("USER123");
        when(StringUtils.isNotBlank("USER123")).thenReturn(true); // facultatif si tu veux mocker static

        // Act
        String result = aggre8.getAggre8UserId(request);

        // Assert
        assertEquals("USER123", result);
    }

    @Test
    void shouldThrowException_whenJsonProcessingFails() throws Exception {
        // Arrange
        UserRequest request = new UserRequest();
        when(objectMapper.writeValueAsString(any()))
            .thenThrow(new JsonProcessingException("error") {});

        // Act + Assert
        assertThrows(TechnicalException.class, () -> aggre8.getAggre8UserId(request));
    }
}

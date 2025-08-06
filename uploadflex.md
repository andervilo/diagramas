```mermaid
sequenceDiagram
    participant Client
    participant QuotationGroupUploadController
    participant QuotationGroupUploadService
    participant FileManagementService
    participant UserService
    participant QuotationGroupService
    participant QuotationUploadRepository
    participant AsyncUtils
    participant QuotationRelayClient

    Client->>QuotationGroupUploadController: POST /v1/quotation-group/upload
    Note over Client,QuotationGroupUploadController: Headers: country, requestTraceId, authorization<br>Body: quotationName, vendorId, file (multipart/form-data)
    
    QuotationGroupUploadController->>QuotationGroupUploadService: upload(QuotationUploadRequestDTO)
    
    Note over QuotationGroupUploadService: Log: "Initiating Quotation Group Upload Service - upload()"
    
    QuotationGroupUploadService->>QuotationGroupUploadService: getUploadResponse(quotationUploadRequestDTO)
    
    QuotationGroupUploadService->>FileManagementService: uploadFile(FLEX_QUOTATION_GROUP_UPLOAD, FLEX_QUOTATION_GROUP_UPLOAD, renamedFile)
    FileManagementService-->>QuotationGroupUploadService: FileManagementResponse
    
    Note over QuotationGroupUploadService: Log: "Successfully uploaded file to File Management Service"
    
    QuotationGroupUploadService->>UserService: getUserInfo(PROJECTIONS)
    UserService-->>QuotationGroupUploadService: UserInfoResponse
    
    Note over QuotationGroupUploadService: Log: "Successfully retrieved user info for the current user"
    Note over QuotationGroupUploadService: Log: "Grouping data by CNPJ from the uploaded file"
    
    QuotationGroupUploadService->>QuotationGroupUploadService: parseXlsxData(file)
    QuotationGroupUploadService->>QuotationGroupUploadService: getGroupByCnpj(quotationUploadRequestDTO)
    
    QuotationGroupUploadService->>QuotationGroupUploadService: getQuotationGroupEntity(quotationUploadRequestDTO, userInfo, uploadResponse)
    
    QuotationGroupUploadService->>QuotationGroupService: saveQuotationGroup(quotationGroup)
    QuotationGroupService-->>QuotationGroupUploadService: saved QuotationGroupEntity
    
    QuotationGroupUploadService->>QuotationGroupUploadService: getQuotationUploadEntity(quotationGroup, uploadResponse)
    
    QuotationGroupUploadService->>QuotationUploadRepository: save(quotationUpload)
    QuotationUploadRepository-->>QuotationGroupUploadService: saved QuotationUploadEntity
    
    loop For each CNPJ in groupByCnpj
        QuotationGroupUploadService->>QuotationGroupUploadService: getItemDtos(xlsxDataList)
        QuotationGroupUploadService->>QuotationGroupUploadService: getQuotationRelayProcessRequest(cnpj, quotationGroup, items)
        QuotationGroupUploadService->>AsyncUtils: sendFutureQuotationRelayRequest(List.of(quotationRelayProcessRequest))
        AsyncUtils->>QuotationRelayClient: postQuotationGroup(request)
        QuotationRelayClient-->>AsyncUtils: void
        AsyncUtils-->>QuotationGroupUploadService: CompletableFuture<Void>
    end
    
    QuotationGroupUploadService->>QuotationGroupUploadService: CompletableFuture.allOf(futures).join()
    
    QuotationGroupUploadService->>QuotationGroupUploadService: Build QuotationUploadResponseDTO
    
    QuotationGroupUploadService-->>QuotationGroupUploadController: QuotationUploadResponseDTO
    
    QuotationGroupUploadController-->>Client: ResponseEntity<QuotationUploadResponseDTO>
    Note over QuotationGroupUploadController,Client: HTTP 201 Created
  ```

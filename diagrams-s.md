# Sequence Diagrams for SNISB Backend Controllers

## AcompanharBarragemResource

### POST /acompanhar-barragem/salvar

```mermaid
sequenceDiagram
    participant Client
    participant AcompanharBarragemResource
    participant AcompanharBarragemService
    participant AcompanharBarragemRepository
    participant Database

    Client->>AcompanharBarragemResource: POST /acompanhar-barragem/salvar (AcompanharBarragemDTO)
    Note over AcompanharBarragemResource: Validates request body
    AcompanharBarragemResource->>AcompanharBarragemService: salvar(dto)
    AcompanharBarragemService->>AcompanharBarragemService: Create AcompanharBarragem entity
    AcompanharBarragemService->>AcompanharBarragemService: Set current timestamp
    AcompanharBarragemService->>AcompanharBarragemRepository: save(acompanharBarragem)
    AcompanharBarragemRepository->>Database: INSERT INTO SNBTB_ACOMPANHAR_BARRAGEM
    Database-->>AcompanharBarragemRepository: Return saved entity
    AcompanharBarragemRepository-->>AcompanharBarragemService: Return saved entity
    AcompanharBarragemService-->>AcompanharBarragemResource: Return saved entity
    AcompanharBarragemResource-->>Client: Return HTTP 200 with entity
    
    Note over AcompanharBarragemResource: Error handling
    AcompanharBarragemResource-->>Client: Return HTTP 400 with error message if exception occurs
```

### GET /acompanhar-barragem/excluir/{idBarragem}/{email}

```mermaid
sequenceDiagram
    participant Client
    participant AcompanharBarragemResource
    participant AcompanharBarragemService
    participant AcompanharBarragemRepository
    participant Database

    Client->>AcompanharBarragemResource: GET /acompanhar-barragem/excluir/{idBarragem}/{email}
    AcompanharBarragemResource->>AcompanharBarragemService: excluir(idBarragem, email)
    AcompanharBarragemService->>AcompanharBarragemRepository: findByIdBarragemAndEmail(idBarragem, email)
    AcompanharBarragemRepository->>Database: SELECT * FROM SNBTB_ACOMPANHAR_BARRAGEM WHERE ACB_BAR_CD = :idBarragem AND ACB_DS_EMAIL = :email
    Database-->>AcompanharBarragemRepository: Return entity
    AcompanharBarragemRepository-->>AcompanharBarragemService: Return entity
    AcompanharBarragemService->>AcompanharBarragemRepository: delete(entity)
    AcompanharBarragemRepository->>Database: DELETE FROM SNBTB_ACOMPANHAR_BARRAGEM WHERE ACB_CD = :id
    Database-->>AcompanharBarragemRepository: Confirm deletion
    AcompanharBarragemRepository-->>AcompanharBarragemService: Return
    AcompanharBarragemService-->>AcompanharBarragemResource: Return
    AcompanharBarragemResource-->>Client: Return HTTP 200 with success message
    
    Note over AcompanharBarragemResource: Error handling
    AcompanharBarragemResource-->>Client: Return HTTP 400 with error message if exception occurs
```

## ArquivoPSBResource

### POST /arquivo-psb/salvar

```mermaid
sequenceDiagram
    participant Client
    participant ArquivoPSBResource
    participant ArquivoPSBService
    participant ArquivoPSBRepository
    participant Database

    Client->>ArquivoPSBResource: POST /arquivo-psb/salvar (ArquivoPSBDTO)
    Note over ArquivoPSBResource: Validates request body
    ArquivoPSBResource->>ArquivoPSBService: salvar(arquivoPSBDTO)
    ArquivoPSBService->>ArquivoPSBService: Create ArquivoPSB entity
    ArquivoPSBService->>ArquivoPSBRepository: save(arquivoPSB)
    ArquivoPSBRepository->>Database: INSERT INTO SNBTB_ARQUIVOPSB
    Database-->>ArquivoPSBRepository: Return saved entity
    ArquivoPSBRepository-->>ArquivoPSBService: Return saved entity
    ArquivoPSBService-->>ArquivoPSBResource: Return saved entity
    ArquivoPSBResource-->>Client: Return HTTP 200 with entity
```

## BarragemAcessoResource

### POST /barragem-acesso/{id}

```mermaid
sequenceDiagram
    participant Client
    participant BarragemAcessoResource
    participant BarragemAcessoService
    participant BarragemAcessoRepository
    participant Database

    Client->>BarragemAcessoResource: POST /barragem-acesso/{id}
    BarragemAcessoResource->>BarragemAcessoResource: Extract IP from request
    BarragemAcessoResource->>BarragemAcessoService: contabilizarAcesso(id, ip)
    BarragemAcessoService->>BarragemAcessoRepository: buscarPorIdBarragemAndIp(id, ip)
    BarragemAcessoRepository->>Database: SELECT * FROM SNBTB_BARRAGEM_ACESSO WHERE BMO_BAR_CD = :id AND BMO_NU_IP = :ip
    Database-->>BarragemAcessoRepository: Return most recent access record (if exists)
    BarragemAcessoRepository-->>BarragemAcessoService: Return Optional<BarragemAcesso>
    
    alt No existing record or last access > 1 hour ago
        BarragemAcessoService->>BarragemAcessoService: Create new BarragemAcesso entity
        BarragemAcessoService->>BarragemAcessoRepository: save(barragemAcesso)
        BarragemAcessoRepository->>Database: INSERT INTO SNBTB_BARRAGEM_ACESSO
        Database-->>BarragemAcessoRepository: Confirm save
        BarragemAcessoRepository-->>BarragemAcessoService: Return
    end
    
    BarragemAcessoService-->>BarragemAcessoResource: Return
    BarragemAcessoResource-->>Client: Return HTTP 200 OK
```

## CapacitacaoResource

### POST /capacitacao/salvar

```mermaid
sequenceDiagram
    participant Client
    participant CapacitacaoResource
    participant CapacitacaoService
    participant CapacitacaoRepository
    participant Database

    Client->>CapacitacaoResource: POST /capacitacao/salvar (CapacitacaoDTO)
    Note over CapacitacaoResource: Validates request body
    CapacitacaoResource->>CapacitacaoService: salvar(capacitacaoDTO)
    CapacitacaoService->>CapacitacaoService: Create Capacitacao entity
    CapacitacaoService->>CapacitacaoRepository: save(capacitacao)
    CapacitacaoRepository->>Database: INSERT INTO capacitacao table
    Database-->>CapacitacaoRepository: Return saved entity
    CapacitacaoRepository-->>CapacitacaoService: Return saved entity
    CapacitacaoService-->>CapacitacaoResource: Return saved entity
    CapacitacaoResource-->>Client: Return HTTP 200 with entity
```

### PUT /capacitacao/alterar

```mermaid
sequenceDiagram
    participant Client
    participant CapacitacaoResource
    participant CapacitacaoService
    participant CapacitacaoRepository
    participant Database

    Client->>CapacitacaoResource: PUT /capacitacao/alterar (CapacitacaoDTO)
    Note over CapacitacaoResource: Validates request body
    CapacitacaoResource->>CapacitacaoService: salvar(capacitacaoDTO)
    CapacitacaoService->>CapacitacaoService: Update Capacitacao entity
    CapacitacaoService->>CapacitacaoRepository: save(capacitacao)
    CapacitacaoRepository->>Database: UPDATE capacitacao table
    Database-->>CapacitacaoRepository: Return updated entity
    CapacitacaoRepository-->>CapacitacaoService: Return updated entity
    CapacitacaoService-->>CapacitacaoResource: Return updated entity
    CapacitacaoResource-->>Client: Return HTTP 200 with entity
```

### GET /capacitacao/listarTodos/{idCategoriaCapacitacao}

```mermaid
sequenceDiagram
    participant Client
    participant CapacitacaoResource
    participant CapacitacaoService
    participant CapacitacaoRepository
    participant Database

    Client->>CapacitacaoResource: GET /capacitacao/listarTodos/{idCategoriaCapacitacao}
    CapacitacaoResource->>CapacitacaoService: listarCapacitacoes(idCategoriaCapacitacao)
    CapacitacaoService->>CapacitacaoRepository: findByIdCategoriaCapacitacao(idCategoriaCapacitacao)
    CapacitacaoRepository->>Database: SELECT * FROM capacitacao table WHERE categoria_id = :idCategoriaCapacitacao
    Database-->>CapacitacaoRepository: Return list of entities
    CapacitacaoRepository-->>CapacitacaoService: Return list of entities
    CapacitacaoService-->>CapacitacaoResource: Return list of entities
    CapacitacaoResource-->>Client: Return HTTP 200 with list
```

### GET /capacitacao/listar

```mermaid
sequenceDiagram
    participant Client
    participant CapacitacaoResource
    participant CapacitacaoService
    participant CapacitacaoRepository
    participant Database

    Client->>CapacitacaoResource: GET /capacitacao/listar (CapacitacaoFilter, Pageable)
    CapacitacaoResource->>CapacitacaoService: consultar(filter, pageable)
    CapacitacaoService->>CapacitacaoRepository: findAll(specification, pageable)
    CapacitacaoRepository->>Database: SELECT * FROM capacitacao table with filters and pagination
    Database-->>CapacitacaoRepository: Return page of entities
    CapacitacaoRepository-->>CapacitacaoService: Return page of entities
    CapacitacaoService-->>CapacitacaoResource: Return page of entities
    CapacitacaoResource-->>Client: Return HTTP 200 with page
```

### DELETE /capacitacao/excluir/{idCapacitacao}

```mermaid
sequenceDiagram
    participant Client
    participant CapacitacaoResource
    participant CapacitacaoService
    participant CapacitacaoRepository
    participant Database

    Client->>CapacitacaoResource: DELETE /capacitacao/excluir/{idCapacitacao}
    CapacitacaoResource->>CapacitacaoService: excluir(idCapacitacao)
    CapacitacaoService->>CapacitacaoRepository: deleteById(idCapacitacao)
    CapacitacaoRepository->>Database: DELETE FROM capacitacao table WHERE id = :idCapacitacao
    Database-->>CapacitacaoRepository: Confirm deletion
    CapacitacaoRepository-->>CapacitacaoService: Return
    CapacitacaoService-->>CapacitacaoResource: Return
    CapacitacaoResource-->>Client: Return HTTP 200 with success message
    
    Note over CapacitacaoResource: Error handling
    CapacitacaoResource-->>Client: Return HTTP 400 with error message if exception occurs
```

## DocumentoSegurancaBarragemResource

### GET /documento-seguranca-barragem/pesquisar

```mermaid
sequenceDiagram
    participant Client
    participant DocumentoSegurancaBarragemResource
    participant DocumentoSegurancaBarragemService
    participant DocumentoSegurancaBarragemRepository
    participant Database

    Client->>DocumentoSegurancaBarragemResource: GET /documento-seguranca-barragem/pesquisar (PesquisaDocumentoSeguracaBarragemFilter)
    DocumentoSegurancaBarragemResource->>DocumentoSegurancaBarragemService: listarDocumentos(filter)
    DocumentoSegurancaBarragemService->>DocumentoSegurancaBarragemRepository: findByFilter(filter)
    DocumentoSegurancaBarragemRepository->>Database: SELECT * FROM documento_seguranca_barragem table with filters
    Database-->>DocumentoSegurancaBarragemRepository: Return list of entities
    DocumentoSegurancaBarragemRepository-->>DocumentoSegurancaBarragemService: Return list of entities
    DocumentoSegurancaBarragemService-->>DocumentoSegurancaBarragemResource: Return list of entities
    DocumentoSegurancaBarragemResource-->>Client: Return HTTP 200 with list
```

### GET /documento-seguranca-barragem/listar

```mermaid
sequenceDiagram
    participant Client
    participant DocumentoSegurancaBarragemResource
    participant DocumentoSegurancaBarragemService
    participant DocumentoSegurancaBarragemRepository
    participant Database

    Client->>DocumentoSegurancaBarragemResource: GET /documento-seguranca-barragem/listar (DocumentoSeguracaBarragemFilter, Pageable)
    DocumentoSegurancaBarragemResource->>DocumentoSegurancaBarragemService: consultar(filter, pageable)
    DocumentoSegurancaBarragemService->>DocumentoSegurancaBarragemRepository: findAll(specification, pageable)
    DocumentoSegurancaBarragemRepository->>Database: SELECT * FROM documento_seguranca_barragem table with filters and pagination
    Database-->>DocumentoSegurancaBarragemRepository: Return page of entities
    DocumentoSegurancaBarragemRepository-->>DocumentoSegurancaBarragemService: Return page of entities
    DocumentoSegurancaBarragemService-->>DocumentoSegurancaBarragemResource: Return page of entities
    DocumentoSegurancaBarragemResource-->>Client: Return HTTP 200 with page
    
    Note over DocumentoSegurancaBarragemResource: Error handling
    DocumentoSegurancaBarragemResource-->>Client: Return HTTP 400 with error message if exception occurs
```

### DELETE /documento-seguranca-barragem/excluir/{idDocumentoBarragem}

```mermaid
sequenceDiagram
    participant Client
    participant DocumentoSegurancaBarragemResource
    participant DocumentoSegurancaBarragemService
    participant DocumentoSegurancaBarragemRepository
    participant Database

    Client->>DocumentoSegurancaBarragemResource: DELETE /documento-seguranca-barragem/excluir/{idDocumentoBarragem}
    DocumentoSegurancaBarragemResource->>DocumentoSegurancaBarragemService: excluir(idDocumentoBarragem)
    DocumentoSegurancaBarragemService->>DocumentoSegurancaBarragemRepository: deleteById(idDocumentoBarragem)
    DocumentoSegurancaBarragemRepository->>Database: DELETE FROM documento_seguranca_barragem table WHERE id = :idDocumentoBarragem
    Database-->>DocumentoSegurancaBarragemRepository: Confirm deletion
    DocumentoSegurancaBarragemRepository-->>DocumentoSegurancaBarragemService: Return
    DocumentoSegurancaBarragemService-->>DocumentoSegurancaBarragemResource: Return
    DocumentoSegurancaBarragemResource-->>Client: Return HTTP 200 with success message
    
    Note over DocumentoSegurancaBarragemResource: Error handling
    DocumentoSegurancaBarragemResource-->>Client: Return HTTP 400 with error message if exception occurs
```

### POST /documento-seguranca-barragem/salvar

```mermaid
sequenceDiagram
    participant Client
    participant DocumentoSegurancaBarragemResource
    participant DocumentoSegurancaBarragemService
    participant DocumentoSegurancaBarragemRepository
    participant Database

    Client->>DocumentoSegurancaBarragemResource: POST /documento-seguranca-barragem/salvar (DocumentoSegurancaBarragemDTO)
    Note over DocumentoSegurancaBarragemResource: Validates request body
    DocumentoSegurancaBarragemResource->>DocumentoSegurancaBarragemService: salvar(documentoDTO)
    DocumentoSegurancaBarragemService->>DocumentoSegurancaBarragemService: Create DocumentoSegurancaBarragem entity
    DocumentoSegurancaBarragemService->>DocumentoSegurancaBarragemRepository: save(documentoSegurancaBarragem)
    DocumentoSegurancaBarragemRepository->>Database: INSERT INTO documento_seguranca_barragem table
    Database-->>DocumentoSegurancaBarragemRepository: Return saved entity
    DocumentoSegurancaBarragemRepository-->>DocumentoSegurancaBarragemService: Return saved entity
    DocumentoSegurancaBarragemService-->>DocumentoSegurancaBarragemResource: Return saved entity
    DocumentoSegurancaBarragemResource-->>Client: Return HTTP 200 with entity
```

## DominioResource

### GET /dominio/listarTiposMensagem

```mermaid
sequenceDiagram
    participant Client
    participant DominioResource
    participant DominioService
    participant Repository
    participant Database

    Client->>DominioResource: GET /dominio/listarTiposMensagem
    DominioResource->>DominioService: listarTiposMensagem()
    DominioService->>Repository: findTiposMensagem()
    Repository->>Database: SELECT * FROM tipos_mensagem table
    Database-->>Repository: Return list of entities
    Repository-->>DominioService: Return list of entities
    DominioService-->>DominioResource: Return list of DTOs
    DominioResource-->>Client: Return HTTP 200 with list
    
    Note over DominioResource: Error handling
    DominioResource-->>Client: Return HTTP 400 with error message if exception occurs
```

### GET /dominio/listarTiposBarragem

```mermaid
sequenceDiagram
    participant Client
    participant DominioResource
    participant DominioService
    participant Repository
    participant Database

    Client->>DominioResource: GET /dominio/listarTiposBarragem
    DominioResource->>DominioService: listarTiposBarragem()
    DominioService->>Repository: findTiposBarragem()
    Repository->>Database: SELECT * FROM tipos_barragem table
    Database-->>Repository: Return list of entities
    Repository-->>DominioService: Return list of entities
    DominioService-->>DominioResource: Return list of DTOs
    DominioResource-->>Client: Return HTTP 200 with list
    
    Note over DominioResource: Error handling
    DominioResource-->>Client: Return HTTP 400 with error message if exception occurs
```

## FaleConoscoResource

### POST /fale-conosco/salvar

```mermaid
sequenceDiagram
    participant Client
    participant FaleConoscoResource
    participant FaleConoscoService
    participant FaleConoscoRepository
    participant Database

    Client->>FaleConoscoResource: POST /fale-conosco/salvar (FaleConoscoDTO)
    Note over FaleConoscoResource: Validates request body
    FaleConoscoResource->>FaleConoscoService: salvar(dto)
    FaleConoscoService->>FaleConoscoService: Create FaleConosco entity
    FaleConoscoService->>FaleConoscoRepository: save(faleConosco)
    FaleConoscoRepository->>Database: INSERT INTO fale_conosco table
    Database-->>FaleConoscoRepository: Return saved entity
    FaleConoscoRepository-->>FaleConoscoService: Return saved entity
    FaleConoscoService-->>FaleConoscoResource: Return saved entity ID
    FaleConoscoResource-->>Client: Return HTTP 200 with ID
    
    Note over FaleConoscoResource: Error handling
    FaleConoscoResource-->>Client: Return HTTP 400 with error message if exception occurs
```

### DELETE /fale-conosco/excluir/{idFaleConosco}

```mermaid
sequenceDiagram
    participant Client
    participant FaleConoscoResource
    participant FaleConoscoService
    participant FaleConoscoRepository
    participant Database

    Client->>FaleConoscoResource: DELETE /fale-conosco/excluir/{idFaleConosco}
    FaleConoscoResource->>FaleConoscoService: excluir(idFaleConosco)
    FaleConoscoService->>FaleConoscoRepository: deleteById(idFaleConosco)
    FaleConoscoRepository->>Database: DELETE FROM fale_conosco table WHERE id = :idFaleConosco
    Database-->>FaleConoscoRepository: Confirm deletion
    FaleConoscoRepository-->>FaleConoscoService: Return
    FaleConoscoService-->>FaleConoscoResource: Return
    FaleConoscoResource-->>Client: Return HTTP 200 with success message
    
    Note over FaleConoscoResource: Error handling
    FaleConoscoResource-->>Client: Return HTTP 400 with error message if exception occurs
```

## FileResource

### POST /file/upload/{idEntidade}/{idTipoFormulario}

```mermaid
sequenceDiagram
    participant Client
    participant FileResource
    participant FileService
    participant FileSystem

    Client->>FileResource: POST /file/upload/{idEntidade}/{idTipoFormulario} (MultipartFile)
    FileResource->>FileService: salvarArquivo(idEntidade, idTipoFormulario, arquivo)
    FileService->>FileService: Generate file name and path
    FileService->>FileSystem: Save file to disk
    FileSystem-->>FileService: Confirm save
    FileService-->>FileResource: Return
    FileResource-->>Client: Return HTTP 200 OK
    
    Note over FileResource: Error handling
    FileResource-->>Client: Return HTTP 400 with error message if exception occurs
```

### GET /file/download/{idEntidade}/{idTipoFormulario}/{nomeArquivo}

```mermaid
sequenceDiagram
    participant Client
    participant FileResource
    participant FileService
    participant FileSystem

    Client->>FileResource: GET /file/download/{idEntidade}/{idTipoFormulario}/{nomeArquivo}
    FileResource->>FileService: recuperarArquivo(idEntidade, idTipoFormulario, nomeArquivo)
    FileService->>FileSystem: Load file from disk
    FileSystem-->>FileService: Return file as Resource
    FileService-->>FileResource: Return Resource
    FileResource->>FileResource: Set content type and headers
    FileResource-->>Client: Return HTTP 200 with file
```

## SecurityResource

### GET /security/currentuser/profiles

```mermaid
sequenceDiagram
    participant Client
    participant SecurityResource
    participant Authentication

    Client->>SecurityResource: GET /security/currentuser/profiles
    SecurityResource->>Authentication: getAuthorities()
    Authentication-->>SecurityResource: Return Collection<GrantedAuthority>
    SecurityResource-->>Client: Return HTTP 200 with authorities
```

## UserResource

### GET /userinfo

```mermaid
sequenceDiagram
    participant Client
    participant UserResource
    participant Authentication
    participant UserDetails

    Client->>UserResource: GET /userinfo
    UserResource->>Authentication: getPrincipal()
    Authentication-->>UserResource: Return UserDetails
    UserResource->>UserResource: Convert to UserInfoDTO
    UserResource-->>Client: Return HTTP 200 with UserInfoDTO
```

## BarragemResource

### GET /consultar-barragens/buscar-mais-acessadas/

```mermaid
sequenceDiagram
    participant Client
    participant BarragemResource
    participant BarragemService
    participant Repository
    participant Database

    Client->>BarragemResource: GET /consultar-barragens/buscar-mais-acessadas/
    BarragemResource->>BarragemService: buscarMaisAcessadas()
    BarragemService->>Repository: findMostAccessed()
    Repository->>Database: SELECT * FROM barragem_acesso with aggregation
    Database-->>Repository: Return list of results
    Repository-->>BarragemService: Return list of results
    BarragemService-->>BarragemResource: Return list of MaisAcessosDTO
    BarragemResource-->>Client: Return HTTP 200 with list
```

### GET /consultar-barragens/detalhe-barragem/{id}

```mermaid
sequenceDiagram
    participant Client
    participant BarragemResource
    participant BarragemService
    participant Repository
    participant Database

    Client->>BarragemResource: GET /consultar-barragens/detalhe-barragem/{id}
    BarragemResource->>BarragemResource: Extract IP from request
    BarragemResource->>BarragemService: buscarDetalheDTO(id, ip)
    BarragemService->>Repository: findById(id)
    Repository->>Database: SELECT * FROM barragem WHERE id = :id
    Database-->>Repository: Return entity
    Repository-->>BarragemService: Return entity
    BarragemService->>BarragemService: Convert to BarragemDetalheDTO
    BarragemService-->>BarragemResource: Return BarragemDetalheDTO
    BarragemResource-->>Client: Return HTTP 200 with DTO
```

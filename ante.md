```mermaid
flowchart TD  
    A[Cliente realiza compra\ncom cartão] --> B[Lojista envia transação\npara Credenciadora]  
    B --> C[Credenciadora processa e envia\npara Bandeira (Arranjo)]  
    C --> D[Bandeira repassa pedido\nao Banco Emissor]  
    D -->|Verifica saldo/limite| E{Autorização aprovada?}  
    E -- Não --> F[Transação negada\n(cliente informado)]  
    E -- Sim --> G[Autorização enviada\nà Bandeira]  
    G --> H[Bandeira envia autorização\nà Credenciadora]  
    H --> I[Credenciadora confirma\ntransação ao Lojista]  
    I --> J[Cliente recebe produto/serviço]  
  
    %% liquidação posterior  
    J --> K[Após prazo acordado\n(ex.: D+30)]  
    K --> L[Banco Emissor envia valores\nà Bandeira]  
    L --> M[Bandeira repassa valores\nà Credenciadora]  
    M --> N[Credenciadora deposita\nvalor líquido ao Banco do Lojista]  
    N --> O[Lojista recebe valor\nem conta corrente]  
  
    %% antecipação opcional  
    M -.-> P[Se lojista pedir antecipação:\nCredenciadora antecipa parte\ncom deságio/taxa]
```

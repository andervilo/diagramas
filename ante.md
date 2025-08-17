```mermaid
flowchart TD
    A[Cliente realiza compra<br/>com cartão] --> B[Lojista envia transação<br/>para Credenciadora]
    B --> C[Credenciadora processa e envia<br/>para Bandeira (Arranjo)]
    C --> D[Bandeira repassa pedido<br/>ao Banco Emissor]
    D -->|Verifica saldo/limite| E{Autorização aprovada?}
    E -- Não --> F[Transação negada<br/>(cliente informado)]
    E -- Sim --> G[Autorização enviada<br/>à Bandeira]
    G --> H[Bandeira envia autorização<br/>à Credenciadora]
    H --> I[Credenciadora confirma<br/>transação ao Lojista]
    I --> J[Cliente recebe produto/serviço]

    %% liquidação posterior
    J --> K[Após prazo acordado<br/>(ex.: D+30)]
    K --> L[Banco Emissor envia valores<br/>à Bandeira]
    L --> M[Bandeira repassa valores<br/>à Credenciadora]
    M --> N[Credenciadora deposita<br/>valor líquido ao Banco do Lojista]
    N --> O[Lojista recebe valor<br/>em conta corrente]

    %% antecipação opcional
    M --.-> P[Se lojista pedir antecipação:<br/>Credenciadora antecipa parte<br/>com deságio/taxa]
```

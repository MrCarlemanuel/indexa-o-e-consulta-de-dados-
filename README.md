# README - Azure Search Service Deployment

## Descrição

Este projeto contém arquivos de configuração para implantar um **Azure Search Service** usando modelos ARM (Azure Resource Manager). Os arquivos incluem:

1. **template.json**: Define a estrutura e os recursos do Azure Search Service a serem implantados.
2. **parameters.json**: Fornece os parâmetros necessários para a implantação, como o nome do serviço de busca.

## Pré-requisitos

- Uma assinatura ativa do Azure.
- Azure CLI ou PowerShell instalado e configurado.
- Permissões para implantar recursos no Azure (por exemplo, "Contribuidor" no grupo de recursos).

---

## Como Implantar

### 1. **Preparar o Arquivo de Parâmetros**
   - Abra `parameters.json` e substitua `null` pelo nome desejado para o serviço de busca. Exemplo:
     ```json
     "searchServices_azureservicesearch_name": {
         "value": "meu-servico-de-busca"
     }
     ```
   - O nome deve ser globalmente único no domínio `.search.windows.net`.

### 2. **Implantar via Azure CLI**
   Execute o seguinte comando no terminal:
   ```sh
   az deployment group create \
     --resource-group <NomeDoGrupoDeRecursos> \
     --template-file template.json \
     --parameters parameters.json
   ```

### 3. **Implantar via PowerShell**
   Execute o seguinte comando:
   ```powershell
   New-AzResourceGroupDeployment `
     -ResourceGroupName <NomeDoGrupoDeRecursos> `
     -TemplateFile template.json `
     -TemplateParameterFile parameters.json
   ```

---

## Configurações do Azure Search Service

O serviço será implantado com as seguintes propriedades padrão (definidas em `template.json`):

- **Localização**: `Central US`
- **SKU**: `basic` (adequado para ambientes de desenvolvimento/teste)
- **Réplicas**: `1`
- **Partições**: `1`
- **Acesso Público**: Habilitado (`publicNetworkAccess: "Enabled"`)
- **Autenticação**: Chave de API habilitada (`authOptions: { "apiKeyOnly": {} }`)
- **Semântica de Busca**: Camada gratuita (`semanticSearch: "free"`)

---

## Personalização

### Modificar o Template
   - Altere `template.json` para ajustar propriedades como:
     - `sku.name` (ex.: `standard` para produção).
     - `replicaCount` e `partitionCount` para escalar o serviço.
     - `location` para outra região do Azure.

### Adicionar Regras de Rede
   - Para restringir acesso por IP, adicione entradas em `networkRuleSet.ipRules`.

---

## Exemplo de Uso Pós-Implantação

Após a implantação, você pode acessar o serviço via:
- **Endpoint**: `https://<nome-do-servico>.search.windows.net`
- **Chave de API**: Gerada automaticamente (consulte no portal do Azure).

```sh
curl -H "Content-Type: application/json" \
     -H "api-key: <SUA_CHAVE_DE_API>" \
     -X GET "https://<nome-do-servico>.search.windows.net/indexes?api-version=2025-02-01-preview"
```

---

## Observações

- O template usa a versão de API `2025-02-01-preview`, que inclui recursos como `semanticSearch`.
- Para ambientes de produção, considere habilitar `encryptionWithCmk` e revisar `networkRuleSet`.

📌 **Documentação Oficial**: [Azure Search Service](https://learn.microsoft.com/en-us/azure/search/)

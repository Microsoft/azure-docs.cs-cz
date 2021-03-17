---
title: Konfigurace klíčů spravovaných zákazníkem pro Azure API pro FHIR
description: Využijte vlastní klíčovou funkci podporovanou v rozhraní Azure API pro FHIR prostřednictvím Cosmos DB
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: ginle
ms.openlocfilehash: 08b5f63f1fffc2369eff620ca1937f298c2d9ca5
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018304"
---
# <a name="configure-customer-managed-keys-at-rest"></a>Konfigurace klíčů spravovaných zákazníkem v klidovém rozmezí

Když vytvoříte nový účet Azure API pro FHIR, vaše data se ve výchozím nastavení šifrují pomocí klíčů spravovaných Microsoftem. Nyní můžete přidat druhou vrstvu šifrování dat pomocí vlastního klíče, který vyberete a budete spravovat sami.

V Azure se to obvykle provádí pomocí šifrovacího klíče v Azure Key Vault zákazníka. Azure SQL, Azure Storage a Cosmos DB jsou příklady, které tuto funkci poskytují ještě dnes. Rozhraní Azure API pro FHIR využívá tuto podporu od Cosmos DB. Při vytváření účtu budete mít možnost zadat identifikátor URI Azure Key Vault klíče. Tento klíč se předává do Cosmos DB, když se zřídí účet databáze. Když se vytvoří požadavek FHIR, Cosmos DB načte klíč a použije ho k zašifrování/dešifrování dat. Chcete-li začít, můžete se podívat na následující odkazy:

- [Registrace poskytovatele prostředků Azure Cosmos DB pro předplatné Azure](../../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Konfigurace instance Azure Key Vault](../../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
- [Přidání zásady přístupu do instance Azure Key Vault](../../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)
- [Vygenerovat klíč v Azure Key Vault](../../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

## <a name="using-azure-portal"></a>Pomocí webu Azure Portal

Při vytváření služby Azure API pro účet FHIR na Azure Portal se na kartě Další nastavení zobrazí možnost konfigurace šifrování dat v části nastavení databáze. Ve výchozím nastavení bude zvolena možnost klíč spravovaný službou. 

Klíč můžete zvolit z modulu pro výběr klíče:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="Výběr":::

Můžete také zadat svůj Azure Key Vault klíč tak, že vyberete možnost klíč spravovaný zákazníkem. Identifikátor URI klíče můžete zadat tady:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Vytvoření rozhraní API Azure pro FHIR":::

U stávajících účtů FHIR můžete v okně "databáze" zobrazit možnost šifrování klíče (služba) (klíč spravovaný zákazníkem), a to jak je uvedeno níže. Možnost konfigurace se po zvolení nedá změnit. Svůj klíč ale můžete upravit a aktualizovat.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Databáze":::

Kromě toho můžete vytvořit novou verzi zadaného klíče, po jejímž uplynutí budou data zašifrovaná s novou verzí bez přerušení služby. Přístup k tomuto klíči můžete odebrat také tak, že odeberete přístup k datům. Pokud je klíč zakázán, dotazy budou mít za následek chybu. Pokud je klíč znovu povolený, budou dotazy znovu úspěšné.




## <a name="using-azure-powershell"></a>Použití Azure Powershell

Pomocí identifikátoru URI Azure Key Vault klíče můžete nakonfigurovat CMK pomocí PowerShellu spuštěním příkazu PowerShellu níže:

```powershell
New-AzHealthcareApisService
    -Name "myService"
    -Kind "fhir-R4"
    -ResourceGroupName "myResourceGroup"
    -Location "westus2"
    -CosmosKeyVaultKeyUri "https://<my-vault>.vault.azure.net/keys/<my-key>"
```

## <a name="using-azure-cli"></a>Použití Azure CLI

Stejně jako u metody PowerShellu můžete nakonfigurovat CMK předáním identifikátoru URI Azure Key Vault klíče pod `key-vault-key-uri` parametrem a spuštěním příkazu CLI níže: 

```azurecli-interactive
az healthcareapis service create
    --resource-group "myResourceGroup"
    --resource-name "myResourceName"
    --kind "fhir-R4"
    --location "westus2"
    --cosmos-db-configuration key-vault-key-uri="https://<my-vault>.vault.azure.net/keys/<my-key>"

```
## <a name="using-azure-resource-manager-template"></a>Použití šablony Azure Resource Manager

Pomocí identifikátoru URI Azure Key Vault klíče můžete nakonfigurovat CMK předáním pod vlastností **keyVaultKeyUri** v objektu **Properties (vlastnosti** ).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "services_myService_name": {
            "defaultValue": "myService",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.HealthcareApis/services",
            "apiVersion": "2020-03-30",
            "name": "[parameters('services_myService_name')]",
            "location": "westus2",
            "kind": "fhir-R4",
            "properties": {
                "accessPolicies": [],
                "cosmosDbConfiguration": {
                    "offerThroughput": 400,
                    "keyVaultKeyUri": "https://<my-vault>.vault.azure.net/keys/<my-key>"
                },
                "authenticationConfiguration": {
                    "authority": "https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db47",
                    "audience": "[concat('https://', parameters('services_myService_name'), '.azurehealthcareapis.com')]",
                    "smartProxyEnabled": false
                },
                "corsConfiguration": {
                    "origins": [],
                    "headers": [],
                    "methods": [],
                    "maxAge": 0,
                    "allowCredentials": false
                }
            }
        }
    ]
}
```

A šablonu můžete nasadit pomocí následujícího skriptu prostředí PowerShell:

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se seznámili s postupem konfigurace klíčů spravovaných zákazníkem v klidovém formátu pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku a šablony Správce prostředků. Další otázky, které byste mohli mít, najdete v části Nejčastější dotazy Azure Cosmos DB: 
 
>[!div class="nextstepaction"]
>[Cosmos DB: jak nastavit CMK](../../cosmos-db/how-to-setup-cmk.md#frequently-asked-questions)
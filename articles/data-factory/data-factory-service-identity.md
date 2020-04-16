---
title: Spravovaná identita pro Data Factory
description: Přečtěte si o spravované identitě pro Azure Data Factory.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jingwang
ms.openlocfilehash: d47450f3252074d3bae8df97766bf8858fca5972
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416582"
---
# <a name="managed-identity-for-data-factory"></a>Spravovaná identita pro Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek vám pomůže pochopit, co je spravovaná identita pro Data Factory (dříve označované jako identita spravované služby/MSI) a jak to funguje.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Přehled

Při vytváření datové továrny lze vytvořit spravovanou identitu spolu s vytvářením z výroby. Spravovaná identita je spravovaná aplikace registrovaná ve službě Azure Active Directory a představuje tuto konkrétní datovou továrnu.

Spravovaná identita pro Data Factory přináší výhody následujícífunkce:

- [Ukládací přihlašovací údaje v trezoru klíčů Azure](store-credentials-in-key-vault.md), v takovém případě se pro ověřování azure key vault používá identita spravovaná z datové hodu.
- Konektory včetně [azure blob storage](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure SQL Database](connector-azure-sql-database.md)a Azure SQL Data [Warehouse](connector-azure-sql-data-warehouse.md).
- [Webová aktivita](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>Generovat spravovanou identitu

Spravovaná identita pro data factory se generuje takto:

- Při vytváření dat prostřednictvím **portálu Azure nebo PowerShellu**se spravovaná identita bude vždy vytvářet automaticky.
- Při vytváření dat prostřednictvím **sady SDK**bude spravovaná identita vytvořena pouze v případě, že v objektu factory pro vytvoření zadáte "Identity = new FactoryIdentity()". Viz příklad v [rychlém startu rozhraní .NET - vytvoření datové továrny](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Při vytváření dat prostřednictvím **rozhraní REST API**bude spravovaná identita vytvořena pouze v případě, že v textu požadavku zadáte oddíl "identity". Viz příklad v [rest quickstart - vytvoření datové továrny](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Pokud zjistíte, že vaše data factory nemá spravovanou identitu přidruženou po načtení instrukce [spravované identity,](#retrieve-managed-identity) můžete explicitně vygenerovat ji aktualizací datové továrny s iniciátorem identity programově:

- [Generování spravované identity pomocí PowerShellu](#generate-managed-identity-using-powershell)
- [Generování spravované identity pomocí rozhraní REST API](#generate-managed-identity-using-rest-api)
- [Generování spravované identity pomocí šablony Azure Resource Manageru](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Generování spravované identity pomocí sady SDK](#generate-managed-identity-using-sdk)

>[!NOTE]
>- Spravovanou identitu nelze změnit. Aktualizace datové továrny, která již má spravovanou identitu, nebude mít žádný dopad, spravovaná identita zůstane nezměněna.
>- Pokud aktualizujete datovou továrnu, která již má spravovanou identitu bez zadání parametru "identity" v objektu factory nebo bez zadání části "identity" v těle požadavku REST, zobrazí se chyba.
>- Když odstraníte datovou továrnu, přidružená spravovaná identita bude odstraněna spolu.

### <a name="generate-managed-identity-using-powershell"></a>Generování spravované identity pomocí PowerShellu

Znovu zavolat příkaz **Set-AzDataFactoryV2** a pak se zobrazí nově vygenerovaná pole Identity:

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>Generování spravované identity pomocí rozhraní REST API

Volání níže API s oddílem "identity" v těle požadavku:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Tělo požadavku**: přidejte "identita": { "type": "SystemAssigned" }.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Odpověď**: spravovaná identita je vytvořena automaticky a část "identita" je odpovídajícím způsobem naplněna.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Generování spravované identity pomocí šablony Azure Resource Manageru

**Šablona**: přidejte "identitu": { "type": "SystemAssigned" }.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-managed-identity-using-sdk"></a>Generování spravované identity pomocí sady SDK

Volání create_or_update funkce datové továrny s Identity=new FactoryIdentity(). Ukázkový kód pomocí rozhraní .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Načtení spravované identity

Spravovanou identitu můžete načíst z webu Azure Portal nebo programově. V následujících částech jsou uvedeny některé ukázky.

>[!TIP]
> Pokud nevidíte spravovanou identitu, [vygenerujte spravovanou identitu](#generate-managed-identity) aktualizací továrny.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Načtení spravované identity pomocí portálu Azure

Informace o spravované identitě najdete na webu Azure Portal -> vaší datové továrně -> Vlastnosti.

- ID spravovaného objektu identity
- Klient spravované identity
- ID aplikace spravované identity

Informace o spravované identitě se zobrazí také při vytváření propojené služby, která podporuje ověřování spravované identity, jako je Azure Blob, Azure Data Lake Storage, Azure Key Vault atd.

Při udělování oprávnění vyhledejte tuto identitu pomocí ID objektu nebo názvu datové továrny (jako název spravované identity).

### <a name="retrieve-managed-identity-using-powershell"></a>Načtení spravované identity pomocí PowerShellu

ID objektu zabezpečení spravované identity a ID klienta budou vráceny, když získáte konkrétní datové továrny takto. Použití **PrincipalId** udělit přístup:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

ID aplikace můžete získat zkopírováním nad id jistiny a spuštěním pod příkazem Azure Active Directory s id jistiny jako parametrem.

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Další kroky
Podívejte se na následující témata, která zavádějí kdy a jak používat identitu spravovanou z továrny na data:

- [Přihlašovací údaje pro ukládání v trezoru klíčů Azure](store-credentials-in-key-vault.md)
- [Kopírování dat z úložiště Azure Data Lake Store nebo do něj pomocí spravovaných identit pro ověřování prostředků Azure](connector-azure-data-lake-store.md)

Další informace o spravovaných identitách pro prostředky Azure najdete v článku [Přehled spravovaných identit pro prostředky Azure,](/azure/active-directory/managed-identities-azure-resources/overview) na které je založená identita spravovaná z datové továrny. 

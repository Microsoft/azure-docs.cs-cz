---
title: Spravovaná identita pro Data Factory
description: Přečtěte si o spravované identitě pro Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: jingwang
ms.openlocfilehash: e0d3b551265a480a700f374ddfcf89dd4d93333f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389157"
---
# <a name="managed-identity-for-data-factory"></a>Spravovaná identita pro Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek vám pomůže pochopit, co je spravovaná identita pro Data Factory (dřív označovaná jako Identita spravované služby/MSI) a jak funguje.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Přehled

Při vytváření datové továrny je možné vytvořit spravovanou identitu společně s vytvářením továrny. Spravovaná identita je spravovaná aplikace zaregistrovaná pro Azure Active Directory, která představuje konkrétní objekt pro vytváření dat.

Spravovaná identita pro Data Factory přináší následující funkce:

- [Ukládat přihlašovací údaje v Azure Key Vault](store-credentials-in-key-vault.md). v takovém případě se pro ověřování Azure Key Vault používá spravovaná identita Data Factory.
- Konektory, včetně služby [Azure Blob Storage](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure SQL Database](connector-azure-sql-database.md)a [Azure synapse Analytics](connector-azure-sql-data-warehouse.md).
- [Webové aktivity](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>Generovat spravovanou identitu

Spravovaná identita pro Data Factory je generována takto:

- Při vytváření datové továrny prostřednictvím **Azure Portal nebo PowerShellu** se spravovaná identita vždycky automaticky vytvoří.
- Při vytváření datové továrny prostřednictvím **sady SDK** se spravovaná identita vytvoří jenom v případě, že při vytváření objektu factory zadáte identity = New FactoryIdentity (). Viz příklad v tématu [rychlý Start .NET – vytvoření datové továrny](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Při vytváření datové továrny prostřednictvím **REST API** se spravovaná identita vytvoří jenom v případě, že v textu žádosti zadáte část identita. Viz příklad v [rychlém startu REST – vytvořit datovou továrnu](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Pokud zjistíte, že objekt pro vytváření dat nemá přidruženu spravovanou identitu po [načtení spravované instrukce identity](#retrieve-managed-identity) , můžete ji explicitně vygenerovat tak, že programově aktualizujete datovou továrnu pomocí iniciátoru identity.

- [Generování spravované identity pomocí PowerShellu](#generate-managed-identity-using-powershell)
- [Generování spravované identity pomocí REST API](#generate-managed-identity-using-rest-api)
- [Generování spravované identity pomocí šablony Azure Resource Manager](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Generování spravované identity pomocí sady SDK](#generate-managed-identity-using-sdk)

>[!NOTE]
>- Spravovanou identitu nejde upravit. Aktualizace datové továrny, která už má spravovanou identitu, nebude mít žádný dopad, spravovaná identita zůstane beze změny.
>- Pokud aktualizujete datovou továrnu, která už má spravovanou identitu bez zadání parametru identity v objektu factory, nebo bez zadání části Identita v textu žádosti REST, zobrazí se chyba.
>- Při odstranění objektu pro vytváření dat bude přidružená spravovaná identita odstraněna společně.

### <a name="generate-managed-identity-using-powershell"></a>Generování spravované identity pomocí PowerShellu

Příkaz **set-AzDataFactoryV2** , zobrazí se nově vygenerovaná pole identity:

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

### <a name="generate-managed-identity-using-rest-api"></a>Generování spravované identity pomocí REST API

V textu žádosti volejte pod rozhraním API část identita:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Text žádosti**: přidejte "identity": {"Type": "SystemAssigned"}.

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

**Odpověď**: spravovaná identita se vytvoří automaticky a v části Identita se odpovídajícím způsobem naplní.

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

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Generování spravované identity pomocí šablony Azure Resource Manager

**Šablona**: přidejte "identity": {"Type": "SystemAssigned"}.

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

Zavolejte funkci Data Factory create_or_update funkce s identitou = New FactoryIdentity (). Ukázkový kód pomocí .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Načtení spravované identity

Spravovanou identitu můžete načíst z Azure Portal nebo programově. V následujících částech jsou uvedeny některé ukázky.

>[!TIP]
> Pokud nevidíte spravovanou identitu, [vygenerujte spravovanou identitu](#generate-managed-identity) aktualizací vaší továrny.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Načtení spravované identity pomocí Azure Portal

Informace o spravované identitě najdete v Azure Portal > vašich vlastností Data Factory – >.

- ID spravovaného objektu identity
- Tenant spravované identity
- ID aplikace spravované identity

Informace o spravované identitě se také zobrazí při vytváření propojené služby, která podporuje spravované ověřování identity, jako je Azure Blob, Azure Data Lake Storage, Azure Key Vault atd.

Při udělování oprávnění použijte ID objektu nebo název datové továrny (jako název spravované identity) k vyhledání této identity.

### <a name="retrieve-managed-identity-using-powershell"></a>Načtení spravované identity pomocí PowerShellu

ID objektu spravované identity a ID tenanta se vrátí, když získáte konkrétní objekt pro vytváření dat následujícím způsobem. Pomocí **PrincipalId** udělte přístup:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

ID aplikace můžete získat zkopírováním výše uvedeného ID objektu zabezpečení a následným Azure Active Directory spuštěním příkazu s ID objektu zabezpečení jako parametrem.

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

### <a name="retrieve-managed-identity-using-rest-api"></a>Načtení spravované identity pomocí REST API

ID objektu spravované identity a ID tenanta se vrátí, když získáte konkrétní objekt pro vytváření dat následujícím způsobem.

Volání pod rozhraním API v žádosti:

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}?api-version=2018-06-01
```

**Odpověď**: zobrazí se odpověď, jak je znázorněno v následujícím příkladu. V části Identita se odpovídajícím způsobem naplní.

```json
{
    "name":"<dataFactoryName>",
    "identity":{
        "type":"SystemAssigned",
        "principalId":"554cff9e-XXXX-XXXX-XXXX-90c7d9ff2ead",
        "tenantId":"72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{
        "provisioningState":"Succeeded",
        "createTime":"2020-02-12T02:22:50.2384387Z",
        "version":"2018-06-01",
        "factoryStatistics":{
            "totalResourceCount":0,
            "maxAllowedResourceCount":0,
            "factorySizeInGbUnits":0,
            "maxAllowedFactorySizeInGbUnits":0
        }
    },
    "eTag":"\"03006b40-XXXX-XXXX-XXXX-5e43617a0000\"",
    "location":"<region>",
    "tags":{

    }
}
```

> [!TIP] 
> Pokud chcete načíst spravovanou identitu ze šablony ARM, přidejte do něj část s **výstupy** ve formátu ARM:

```json
{
    "outputs":{
        "managedIdentityObjectId":{
            "type":"string",
            "value":"[reference(resourceId('Microsoft.DataFactory/factories', parameters('<dataFactoryName>')), '2018-06-01', 'Full').identity.principalId]"
        }
    }
}
```

## <a name="next-steps"></a>Další kroky
Podívejte se na následující témata, která obsahují informace o tom, kdy a jak používat spravovanou identitu pro datovou továrnu:

- [Ukládat přihlašovací údaje v Azure Key Vault](store-credentials-in-key-vault.md)
- [Kopírování dat z/do Azure Data Lake Store používáním spravovaných identit pro ověřování prostředků Azure](connector-azure-data-lake-store.md)

V tématu [Přehled spravovaných identit pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) najdete další informace o spravovaných identitách pro prostředky Azure, na kterých je založena identita spravované datovou továrnou.
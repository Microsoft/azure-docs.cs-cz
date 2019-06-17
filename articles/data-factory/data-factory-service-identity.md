---
title: Identita spravované služby Data Factory | Dokumentace Microsoftu
description: Další informace o spravovaných identit pro službu Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: jingwang
ms.openlocfilehash: 3c1bb38eb12ce77d172257706cd458cebda4bd8c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66153434"
---
# <a name="managed-identity-for-data-factory"></a>Spravovaná identita pro Data Factory

Tento článek vám pomůže pochopit, co je spravovaná identita pro objekt pro vytváření dat (dříve označované jako Identity spravované služby nebo MSI) a jak to funguje.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Přehled

Při vytváření datové továrny, je možné vytvořit spravovanou identitu spolu s vytvoření objektu factory. Spravovaná identita je spravovaná aplikace zaregistrované k Azure Active Directory a představuje této konkrétní datové továrně.

Spravovaná identita služby Data Factory výhody následujících funkcí:

- [Store přihlašovacích údajů ve službě Azure Key Vault](store-credentials-in-key-vault.md), v takovém případě data factory spravovaná identita se používá pro ověřování služby Azure Key Vault.
- Konektorů, včetně [úložiště objektů Blob v Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure SQL Database](connector-azure-sql-database.md), a [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md).
- [Webová aktivita](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>Generovat spravovanou identitu

Spravovaná identita služby Data Factory generováno následujícím způsobem:

- Při vytváření služby data factory prostřednictvím **webu Azure portal nebo Powershellu**spravované identity vždy vytvoří automaticky.
- Při vytváření služby data factory prostřednictvím **SDK**spravované identity vytvoří pouze v případě, že zadáte "Identity = nové FactoryIdentity()" v objektu factory pro vytváření. Viz příklad v [.NET rychlý start – vytvoření datové továrny](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Při vytváření služby data factory prostřednictvím **rozhraní REST API**spravované identity vytvoří pouze v případě, že zadáte část "identity" v textu požadavku. Viz příklad v [REST rychlý start – vytvoření objektu pro vytváření dat](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Pokud nenajdete své datové továrny nemá spravovanou identitu spojené následující [načíst spravovanou identitu](#retrieve-managed-identity) instrukce, můžete explicitně vygenerovat ji prostřednictvím kódu programu aktualizuje objekt pro vytváření dat s identita iniciátora:

- [Generovat spravovanou identitu pomocí Powershellu](#generate-managed-identity-using-powershell)
- [Generovat spravovanou identitu pomocí rozhraní REST API](#generate-managed-identity-using-rest-api)
- [Generovat spravovanou identitu pomocí šablony Azure Resource Manageru](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Generovat spravovanou identitu pomocí sady SDK](#generate-managed-identity-using-sdk)

>[!NOTE]
>- Spravovaná identita nemůže být upraven. Aktualizují se data factory, která už máte spravovaná identita nebude mít žádný vliv, spravovanou identitu, zůstane beze změny.
>- Při aktualizaci služby data factory, který už máte spravované identity bez zadání parametru "identity" v objektu factory nebo bez udání oddíl "identity" v textu požadavku REST, obdržíte chybu.
>- Když odstraníte datovou továrnu, přidružené spravovanou identitu se odstraní společně.

### <a name="generate-managed-identity-using-powershell"></a>Generovat spravovanou identitu pomocí Powershellu

Volání **Set-AzDataFactoryV2** příkaz znovu, pak naleznete v tématu "Identity" polí generovaných nově:

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

### <a name="generate-managed-identity-using-rest-api"></a>Generovat spravovanou identitu pomocí rozhraní REST API

Zavolat následující rozhraní API s oddílem "identity" v textu požadavku:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Text žádosti**: přidání "identity": {"type": "SystemAssigned"}.

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

**Odpověď**: spravovaná identita je automaticky vytvořen a odpovídajícím způsobem naplní oddíl "identity".

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

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Generovat spravovanou identitu pomocí šablony Azure Resource Manageru

**Šablona**: přidání "identity": {"type": "SystemAssigned"}.

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

### <a name="generate-managed-identity-using-sdk"></a>Generovat spravovanou identitu pomocí sady SDK

Volání funkce create_or_update objekt pro vytváření dat s identitou = nové FactoryIdentity(). Ukázkový kód pomocí rozhraní .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Načíst spravovaná identita

Můžete načíst spravovaná identita z webu Azure portal nebo prostřednictvím kódu programu. V následujících částech se dozvíte některé ukázky.

>[!TIP]
> Pokud nevidíte spravovanou identitu [generovat spravovanou identitu](#generate-managed-identity) aktualizací svým objektem pro vytváření.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Načíst spravovanou identitu pomocí webu Azure portal

Můžete najít informace o spravovaných identit z webu Azure portal -> datové továrny -> vlastnosti:

- Spravované Identity ID objektu
- Tenant spravované Identity
- **Spravované ID aplikace Identity** > zkopírujte tuto hodnotu

![Načíst spravovaná identita](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-managed-identity-using-powershell"></a>Načíst spravovanou identitu pomocí Powershellu

Spravovaná identita ID instančního objektu a ID tenanta bude vrácen, pokud dostanete konkrétní datové továrny následujícím způsobem:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Zkopírujte ID instančního objektu a potom spusťte následující příkaz Azure Active Directory s ID objektu zabezpečení jako parametr zobrazíte **ApplicationId**, který použijete k udělení přístupu:

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Další postup
Naleznete v následujících tématech, které zavést, kdy a jak se identita spravované služby data factory používat:

- [Store přihlašovacích údajů ve službě Azure Key Vault](store-credentials-in-key-vault.md)
- [Kopírování dat z/do Azure Data Lake Store pomocí spravované identity pro ověřování prostředků Azure](connector-azure-data-lake-store.md)

Zobrazit [spravovaných identit pro prostředky Azure – přehled](/azure/active-directory/managed-identities-azure-resources/overview) pro další informace o spravovaných identit pro prostředky Azure, které objekt pro vytváření dat se identita spravované je založena na. 

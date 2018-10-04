---
title: Identita služby Azure Data Factory | Dokumentace Microsoftu
description: Další informace o identita služeb datové továrny ve službě Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: jingwang
ms.openlocfilehash: db0bc0cb64c0b6d7df9319c8d2c5850a27e767a1
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249209"
---
# <a name="azure-data-factory-service-identity"></a>Identita služby Azure Data Factory

Tento článek vám pomůže pochopit co je identita služeb datové továrny a jak to funguje.

## <a name="overview"></a>Přehled

Při vytváření datové továrny, je možné vytvořit identitu služby spolu s vytvoření objektu factory. Identita služby je spravovaná aplikace zaregistrované k Azure Active Directory a představuje této konkrétní datové továrně.

Identita služeb datové továrny výhody následujících funkcí:

- [Store přihlašovacích údajů ve službě Azure Key Vault](store-credentials-in-key-vault.md), v takovém případě se identita služby data factory používá pro ověřování služby Azure Key Vault.
- Konektorů, včetně [úložiště objektů Blob v Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure SQL Database](connector-azure-sql-database.md), a [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md).

## <a name="generate-service-identity"></a>Vygenerovat identitu služby

Identita služeb datové továrny generováno následujícím způsobem:

- Při vytváření služby data factory prostřednictvím **webu Azure portal nebo Powershellu**, identita služby vždy vytvoří automaticky.
- Při vytváření služby data factory prostřednictvím **SDK**, identita služby se vytvoří pouze v případě, že zadáte "Identity = nové FactoryIdentity()" v objektu factory pro vytváření. Viz příklad v [.NET rychlý start – vytvoření datové továrny](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Při vytváření služby data factory prostřednictvím **rozhraní REST API**, identita služby se vytvoří pouze v případě, že zadáte část "identity" v textu požadavku. Viz příklad v [REST rychlý start – vytvoření objektu pro vytváření dat](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Pokud nenajdete své datové továrny nemá identitu služby přidružené následující [načíst identitu služby](#retrieve-service-identity) instrukce, můžete explicitně vygenerovat ji prostřednictvím kódu programu aktualizuje objekt pro vytváření dat s identita iniciátora:

- [Vygenerovat identitu služby pomocí Powershellu](#generate-service-identity-using-powershell)
- [Vygenerovat identitu služby pomocí rozhraní REST API](#generate-service-identity-using-rest-api)
- [Vygenerovat identitu služby pomocí sady SDK](#generate-service-identity-using-sdk)

>[!NOTE]
>- Identita služby se nedá upravit. Aktualizují se data factory, která už máte identitu služby nebude mít žádný vliv, identita služby, zůstane beze změny.
>- Při aktualizaci služby data factory, který už máte identitu služby bez zadání parametru "identity" v objektu factory nebo bez udání oddíl "identity" v textu požadavku REST, obdržíte chybu.
>- Když odstraníte datovou továrnu, identity přidružené služby se odstraní společně.

### <a name="generate-service-identity-using-powershell"></a>Vygenerovat identitu služby pomocí Powershellu

Volání **Set-AzureRmDataFactoryV2** příkaz znovu, pak naleznete v tématu "Identity" polí generovaných nově:

```powershell
PS C:\WINDOWS\system32> Set-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-service-identity-using-rest-api"></a>Vygenerovat identitu služby pomocí rozhraní REST API

Zavolat následující rozhraní API s oddílem "identity" v textu požadavku:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2017-09-01-preview
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

**Odpověď**: identita služby se vytvoří automaticky a odpovídajícím způsobem naplní oddíl "identity".

```json
{
    "name": "ADFV2DemoFactory",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2017-09-01-preview"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "EastUS"
}
```

### <a name="generate-service-identity-using-sdk"></a>Vygenerovat identitu služby pomocí sady SDK

Volání funkce create_or_update objekt pro vytváření dat s identitou = nové FactoryIdentity(). Ukázkový kód pomocí rozhraní .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-service-identity"></a>Načíst identitu služby

Můžete načíst identitu služby z webu Azure portal nebo prostřednictvím kódu programu. V následujících částech se dozvíte některé ukázky.

>[!TIP]
> Pokud nevidíte identitu služby [vygenerovat identitu služby](#generate-service-identity) aktualizací svým objektem pro vytváření.

### <a name="retrieve-service-identity-using-azure-portal"></a>Načíst identitu služby pomocí webu Azure portal

Můžete najít informace o identitě služby z webu Azure portal -> datové továrny -> Nastavení -> vlastnosti:

- ID IDENTITY SLUŽBY
- TENANT IDENTITY SLUŽBY
- **ID aplikace IDENTITY služby** > zkopírujte tuto hodnotu

![Načíst identitu služby](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-service-identity-using-powershell"></a>Načíst identitu služby pomocí Powershellu

Identita služby ID instančního objektu a ID tenanta bude vrácen, pokud dostanete konkrétní datové továrny následujícím způsobem:

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Zkopírujte ID instančního objektu a potom spusťte následující příkaz Azure Active Directory s ID objektu zabezpečení jako parametr zobrazíte **ApplicationId**, který použijete k udělení přístupu:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Další postup
Naleznete v následujících tématech, které představují, kdy a jak používat identita služeb datové továrny:

- [Store přihlašovacích údajů ve službě Azure Key Vault](store-credentials-in-key-vault.md)
- [Kopírování dat z/do Azure Data Lake Store pomocí spravované identity pro ověřování prostředků Azure](connector-azure-data-lake-store.md)

Naleznete v tématu [spravovaných identit pro prostředky Azure – přehled](~/articles/active-directory/msi-overview.md) pro další informace o spravovaných identit pro prostředky Azure, je na základě které identita služeb datové továrny. 
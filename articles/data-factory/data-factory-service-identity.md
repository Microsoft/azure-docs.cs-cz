---
title: Identita služby Azure Data Factory | Microsoft Docs
description: Další informace o identitu služby data factory v Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: jingwang
ms.openlocfilehash: ba965b0610525f66c24dd6d5bad3fd113a6b6758
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050861"
---
# <a name="azure-data-factory-service-identity"></a>Identita služby Azure Data Factory

Tento článek vám pomůže pochopit co je identita služby data factory a jak to funguje.

## <a name="overview"></a>Přehled

Při vytváření objekt pro vytváření dat, lze vytvořit identitu služby společně s vytvoření objektu pro vytváření. Identita služby je spravované aplikaci zaregistrovat do Azure Directory aktivity a představuje této konkrétní datové továrně.

Identita služby data factory výhody následující dvě funkce:

- [Uložení přihlašovacích údajů v Azure Key Vault](store-credentials-in-key-vault.md), v takovém případě se identita služby data factory slouží k ověřování Azure Key Vault.
- [Kopírování dat z/do Azure Data Lake Store](connector-azure-data-lake-store.md), v takovém případě identita služby data factory můžete využít jako jeden z podporovaných typů ověřování Data Lake Store.

## <a name="generate-service-identity"></a>Generovat identita služby

Identita služby data factory je vygenerována následujícím způsobem:

- Při vytváření objektu pro vytváření dat prostřednictvím **portál Azure nebo PowerShell**, identita služby vždy se vytvoří automaticky.
- Při vytváření objektu pro vytváření dat prostřednictvím **SDK**, identita služby se vytvoří pouze v případě, že zadáte "Identity = nové FactoryIdentity()" v objektu factory pro vytváření. Najdete v příkladu v [.NET rychlý start - vytváření dat](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Při vytváření objektu pro vytváření dat prostřednictvím **REST API**, identita služby se vytvoří pouze v případě, že zadáte části "identity" v textu žádosti. Najdete v příkladu v [REST rychlý start - vytvořit objekt pro vytváření dat](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Pokud můžete najít objekt pro vytváření dat nemá identity služby přidružené následující [načíst identitu služby](#retrieve-service-identity) instrukce, můžete explicitně vygenerovat jeden tím, že prostřednictvím kódu programu aktualizuje objekt pro vytváření dat s iniciátor identity:

- [Generovat identita služby pomocí prostředí PowerShell](#generate-service-identity-using-powershell)
- [Generovat identita služby pomocí rozhraní REST API](#generate-service-identity-using-rest-api)
- [Generovat identita služby pomocí sady SDK](#generate-service-identity-using-sdk)

>[!NOTE]
>- Identita služby nemůže být upraven. Aktualizace služby data factory, který už máte identity služby nebude mít žádný vliv, identita služby je udržováno beze změny.
>- Pokud aktualizujete objekt pro vytváření dat, které už máte identity služby bez zadání parametru "identity" v objektu factory nebo bez zadání oddíl "identity" v textu požadavku REST, zobrazí se chyba.
>- Pokud odstraníte objekt pro vytváření dat, přidružená služba identity se odstraní společně.

### <a name="generate-service-identity-using-powershell"></a>Generovat identita služby pomocí prostředí PowerShell

Volání **Set-AzureRmDataFactoryV2** příkaz znovu, pak najdete v části "Identity" polí nově generován:

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

### <a name="generate-service-identity-using-rest-api"></a>Generovat identita služby pomocí rozhraní REST API

Volání níže rozhraní API s část "identity" v textu požadavku:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2017-09-01-preview
```

**Text žádosti**: přidání "identity": {"typ": "SystemAssigned"}.

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

**Odpověď**: identita služby se vytvoří automaticky a oddíl "identity" je vyplněný, odpovídajícím způsobem.

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

### <a name="generate-service-identity-using-sdk"></a>Generovat identita služby pomocí sady SDK

Volání funkce create_or_update objekt pro vytváření dat s identitou = nové FactoryIdentity(). Ukázkový kód pomocí rozhraní .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-service-identity"></a>Načtení identita služby

Můžete načíst identitu služby z portálu Azure nebo prostřednictvím kódu programu. Následující části vysvětlují, některá ukázky.

>[!TIP]
> Pokud nevidíte identita služby [generovat identita služby](#generate-service-identity) aktualizací vašeho objektu pro vytváření.

### <a name="retrieve-service-identity-using-azure-portal"></a>Načtení identita služby pomocí portálu Azure

-> Informace o identitě služby z portálu Azure můžete najít datovou továrnu -> Nastavení -> vlastnosti:

- ID IDENTITY SLUŽBY
- SLUŽBA IDENTITY KLIENTA
- **ID aplikace IDENTITY služby** > zkopírujte tuto hodnotu

![Načtení identita služby](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-service-identity-using-powershell"></a>Načtení identita služby pomocí prostředí PowerShell

Identita služby ID objektu zabezpečení a ID klienta bude vrácen, pokud objekt pro vytváření konkrétních dat získáte následujícím způsobem:

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Zkopírujte ID objektu zabezpečení a pak spusťte následující příkaz služby Azure Active Directory s ID objektu zabezpečení jako parametr získat **ApplicationId**, který můžete použít k udělení přístupu:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Další postup
Najdete v následujících tématech, které zavést kdy a jak používat identitu služby data factory:

- [Uložení přihlašovacích údajů v Azure Key Vault](store-credentials-in-key-vault.md)
- [Kopírování dat z/do Azure Data Lake Store pomocí ověření identity spravované služby](connector-azure-data-lake-store.md)

V tématu [MSI přehled](~/articles/active-directory/msi-overview.md) pro další informace o identita spravované služby, je na základě které identita služby data factory. 
---
title: Vytvoření zón a sad záznamů DNS pomocí sady .NET SDK
titleSuffix: Azure DNS
description: V této cestě výuky Začněte vytvářet zóny a sady záznamů DNS v Azure DNS pomocí sady .NET SDK.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: rohink
ms.custom: devx-track-csharp
ms.openlocfilehash: 8e116096afbd01af4914be49d5675881724d5069
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965591"
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Vytvoření zón a sad záznamů DNS pomocí sady .NET SDK

K automatizaci operací můžete vytvářet, odstraňovat nebo aktualizovat zóny DNS, sady záznamů a záznamy pomocí sady DNS SDK s knihovnou správy DNS rozhraní .NET. Úplný projekt sady Visual Studio je k dispozici [zde.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Vytvoření hlavního účtu služby

Programový přístup k prostředkům Azure se obvykle uděluje prostřednictvím vyhrazeného účtu, a ne pomocí vašich uživatelských přihlašovacích údajů. Tyto vyhrazené účty se nazývají účty instančního objektu. Chcete-li použít vzorový projekt sady Azure DNS SDK, musíte nejprve vytvořit instanční účet služby a přiřadit mu správná oprávnění.

1. Podle [těchto pokynů](../active-directory/develop/howto-authenticate-service-principal-powershell.md) vytvořte účet instančního objektu (ukázkový projekt Azure DNS SDK předpokládá ověřování na základě hesla).
2. Vytvořte skupinu prostředků ([podle toho, jak](../azure-resource-manager/templates/deploy-portal.md)).
3. Pomocí Azure RBAC udělte skupině prostředků oprávnění Přispěvatel zóny DNS hlavního účtu služby ([tady je postup](../role-based-access-control/role-assignments-portal.md)).
4. Pokud používáte ukázkový projekt sady Azure DNS SDK, upravte soubor program. cs následujícím způsobem:

   * Vložte správné hodnoty pro `tenantId` , `clientId` (označované také jako ID účtu), `secret` (heslo k hlavnímu účtu služby) a `subscriptionId` jak se používá v kroku 1.
   * Zadejte název skupiny prostředků zvolený v kroku 2.
   * Zadejte název zóny DNS dle vašeho výběru.

## <a name="nuget-packages-and-namespace-declarations"></a>Balíčky NuGet a deklarace oboru názvů

Chcete-li použít sadu Azure DNS .NET SDK, je nutné nainstalovat balíček NuGet **knihovny pro správu Azure DNS** a další požadované balíčky Azure.

1. V **aplikaci Visual Studio** otevřete projekt nebo nový projekt.
2. Přejít na **nástroje** **>** **Správce balíčků NuGet** **>** **Spravovat balíčky NuGet pro řešení...**
3. Klikněte na tlačítko **Procházet**, zaškrtněte políčko **zahrnout předběžné verze** a do vyhledávacího pole zadejte **Microsoft. Azure. Management. DNS** .
4. Vyberte balíček a kliknutím na **instalovat** ho přidejte do projektu aplikace Visual Studio.
5. Zopakováním výše uvedeného postupu nainstalujete také následující balíčky: **Microsoft. REST. ClientRuntime. Azure. Authentication** a **Microsoft. Azure. Management. ResourceManager**.

## <a name="add-namespace-declarations"></a>Přidání deklarací oboru názvů

Přidejte následující deklarace oboru názvů

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>Inicializace klienta správy DNS

`DnsManagementClient`Obsahuje metody a vlastnosti, které jsou nezbytné pro správu zón a sad záznamů DNS.  Následující kód se přihlásí do hlavního účtu služby a vytvoří `DnsManagementClient` objekt.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>Vytvoření nebo aktualizace zóny DNS

Chcete-li vytvořit zónu DNS, je nejprve vytvořen objekt "zóna", který bude obsahovat parametry zóny DNS. Vzhledem k tomu, že zóny DNS nejsou propojené s konkrétní oblastí, umístění je nastavené na globální. V tomto příkladu je do zóny přidána také [Azure Resource Manager ' tag '](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) .

Aby bylo možné zónu v Azure DNS skutečně vytvořit nebo aktualizovat, bude do metody předána objekt zóny obsahující parametry zóny `DnsManagementClient.Zones.CreateOrUpdateAsyc` .

> [!NOTE]
> DnsManagementClient podporuje tři režimy provozu: synchronní (' CreateOrUpdate '), asynchronní (' CreateOrUpdateAsync ') nebo asynchronní s přístupem k odpovědi HTTP (' CreateOrUpdateWithHttpMessagesAsync ').  V závislosti na potřebách vaší aplikace můžete zvolit některý z těchto režimů.

Azure DNS podporuje optimistickou souběžnost s názvem [ETAGs](./dns-getstarted-powershell.md). V tomto příkladu zadáním "*" v hlavičce "If-None-Match" ukáže Azure DNS vytvořit zónu DNS, pokud neexistuje.  Pokud v dané skupině prostředků již existuje zóna se zadaným názvem, volání se nezdařila.

```cs
// Create zone parameters
var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"

// Create an Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
dnsZoneParams.Tags = new Dictionary<string, string>();
dnsZoneParams.Tags.Add("dept", "finance");

// Create the actual zone.
// Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
// Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
// Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");
```

## <a name="create-dns-record-sets-and-records"></a>Vytváření záznamů a sad záznamů DNS

Záznamy DNS se spravují jako sada záznamů. Sada záznamů je sada záznamů se stejným názvem a typem záznamu v rámci zóny.  Název sady záznamů je relativní vzhledem k názvu zóny, nikoli plně kvalifikovanému názvu DNS.

Chcete-li vytvořit nebo aktualizovat sadu záznamů, je vytvořen a předán objekt parametrů "sada záznamů" `DnsManagementClient.RecordSets.CreateOrUpdateAsync` . Stejně jako u zón DNS existují tři režimy operací: synchronní (' CreateOrUpdate '), Asynchronous (' CreateOrUpdateAsync ') nebo asynchronní s přístupem k odpovědi HTTP (' CreateOrUpdateWithHttpMessagesAsync ').

Stejně jako u zón DNS zahrnuje operace se sadami záznamů podporu optimistické souběžnosti.  V tomto příkladu, protože nejsou zadány žádné If-Match a If-None-Match, je sada záznamů vždy vytvořena.  Toto volání přepíše všechny existující sady záznamů se stejným názvem a typem záznamu v této zóně DNS.

```cs
// Create record set parameters
var recordSetParams = new RecordSet();
recordSetParams.TTL = 3600;

// Add records to the record set parameter object.  In this case, we'll add a record of type 'A'
recordSetParams.ARecords = new List<ARecord>();
recordSetParams.ARecords.Add(new ARecord("1.2.3.4"));

// Add metadata to the record set.  Similar to Azure Resource Manager tags, this is optional and you can add multiple metadata name/value pairs
recordSetParams.Metadata = new Dictionary<string, string>();
recordSetParams.Metadata.Add("user", "Mary");

// Create the actual record set in Azure DNS
// Note: no ETAG checks specified, will overwrite existing record set if one exists
var recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSetParams);
```

## <a name="get-zones-and-record-sets"></a>Získat zóny a sady záznamů

`DnsManagementClient.Zones.Get`Metody a `DnsManagementClient.RecordSets.Get` načítají jednotlivé zóny a sady záznamů v uvedeném pořadí. Sady záznamů jsou označeny jejich typem, názvem a zónou a skupinou prostředků, ve které existují. Zóny se identifikují podle jejich názvu a skupiny prostředků, ve kterých existují.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Aktualizace existující sady záznamů

Chcete-li aktualizovat existující sadu záznamů DNS, nejdříve načtěte sadu záznamů, aktualizujte obsah sady záznamů a pak tuto změnu odešlete.  V tomto příkladu zadáte "ETag" ze načtené sady záznamů v parametru If-Match. Volání se nezdařilo, pokud souběžná operace změnila sadu záznamů do té doby.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Seznam zón a sad záznamů

K vypsání zón použijte metody *DnsManagementClient. Zones. list...* , které podporují výpis všech zón v dané skupině prostředků nebo všech zónách v daném předplatném Azure (mezi skupinami prostředků.) Chcete-li vypsat sady záznamů, použijte *DnsManagementClient. Recordsets. list...* Methods, které podporují buď výpis všech sad záznamů v dané zóně, nebo pouze sady záznamů konkrétního typu.

Poznámka při výpisu zón a sad záznamů, které mohou být na stránkování.  Následující příklad ukazuje, jak iterovat stránky výsledků. (Uměle malá velikost stránky ' 2 ' se používá k vynucení stránkování; v praxi by tento parametr měl být vynechán a byla použita výchozí velikost stránky.)

```cs
// Note: in this demo, we'll use a very small page size (2 record sets) to demonstrate paging
// In practice, to improve performance you would use a large page size or just use the system default
int recordSets = 0;
var page = await dnsClient.RecordSets.ListAllInResourceGroupAsync(resourceGroupName, zoneName, "2");
recordSets += page.Count();

while (page.NextPageLink != null)
{
    page = await dnsClient.RecordSets.ListAllInResourceGroupNextAsync(page.NextPageLink);
    recordSets += page.Count();
}
```

## <a name="next-steps"></a>Další kroky

Stáhněte si [ukázkový projekt sady Azure DNS .NET SDK](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), který obsahuje další příklady použití sady Azure DNS .NET SDK, včetně příkladů pro jiné typy záznamů DNS.
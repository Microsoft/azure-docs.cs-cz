---
title: Vytvoření zón DNS a sad záznamů pomocí sady .NET SDK
titleSuffix: Azure DNS
description: V tomto studijním programu můžete začít vytvářet zóny DNS a sady záznamů v Azure DNS pomocí sady .NET SDK.
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
ms.openlocfilehash: c497209e456ff838786edaa19e46ebc5c1858d5f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938863"
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Vytvoření zón DNS a sad záznamů pomocí sady .NET SDK

Operace můžete automatizovat a vytvářet, odstraňovat nebo aktualizovat zóny DNS, sady záznamů a záznamy pomocí sady DNS SDK s knihovnou správy DNS .NET. Celý projekt sady Visual Studio je k dispozici [zde.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Vytvoření účtu instančního objektu

Programový přístup k prostředkům Azure se obvykle uděluje prostřednictvím vyhrazeného účtu, nikoli pomocí vašich vlastních uživatelských přihlašovacích údajů. Tyto vyhrazené účty se nazývají "instanční objekt" účty. Chcete-li použít ukázkový projekt sady Azure DNS SDK, musíte nejprve vytvořit účet instančního objektu a přiřadit mu správná oprávnění.

1. Podle [těchto pokynů](../active-directory/develop/howto-authenticate-service-principal-powershell.md) vytvořte účet instančního objektu (ukázkový projekt sady Azure DNS SDK předpokládá ověřování na základě hesla.)
2. Vytvořte skupinu prostředků[(tady je postup).](../azure-resource-manager/templates/deploy-portal.md)
3. Pomocí Azure RBAC udělit hlavního účtu služby 'DNS Zone Přispěvatel' oprávnění skupiny prostředků[(zde je návod](../role-based-access-control/role-assignments-portal.md).)
4. Pokud používáte ukázkový projekt sady Azure DNS SDK, upravte soubor program.cs následujícím způsobem:

   * Vložte správné hodnoty `tenantId` `clientId` pro , (označované `secret` také jako ID `subscriptionId` účtu), (heslo účtu instančního objektu) a jak je použito v kroku 1.
   * Zadejte název skupiny prostředků zvolený v kroku 2.
   * Zadejte název zóny DNS podle svého výběru.

## <a name="nuget-packages-and-namespace-declarations"></a>NuGet balíčky a deklarace oboru názvů

Chcete-li použít Azure DNS .NET SDK, musíte nainstalovat balíček **Azure DNS Management Library** NuGet a další požadované balíčky Azure.

1. V **sadě Visual Studio**otevřete projekt nebo nový projekt.
2. Přejít na **nástroje** **>** **NuGet Správce** **>** balíčků **Spravovat NuGet balíčky pro řešení...**.
3. Klikněte na **Procházet**, povolte zaškrtávací políčko **Zahrnout předběžnou verzi** a do vyhledávacího pole zadejte **Microsoft.Azure.Management.Dns.**
4. Vyberte balíček a kliknutím na **Nainstalovat** ho přidejte do projektu sady Visual Studio.
5. Postupem uvedeného postupu nainstalujte také následující balíčky: **Microsoft.Rest.ClientRuntime.Azure.Authentication** a **Microsoft.Azure.Management.ResourceManager**.

## <a name="add-namespace-declarations"></a>Přidání deklarací oboru názvů

Přidání následujících deklarací oboru názvů

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>Inicializovat klienta správy DNS

Obsahuje `DnsManagementClient` metody a vlastnosti potřebné pro správu zón DNS a sad záznamů.  Následující kód se přihlásí k účtu instančního objektu `DnsManagementClient` a vytvoří objekt.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>Vytvoření nebo aktualizace zóny DNS

Chcete-li vytvořit zónu DNS, nejprve je vytvořen objekt "Zóna", který obsahuje parametry zóny DNS. Vzhledem k tomu, že zóny DNS nejsou propojeny s určitou oblastí, je umístění nastaveno na globální. V tomto příkladu je do zóny přidána také [značka Správce prostředků Azure.](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)

Chcete-li skutečně vytvořit nebo aktualizovat zónu v Azure DNS, objekt `DnsManagementClient.Zones.CreateOrUpdateAsyc` zóny obsahující parametry zóny je předán metodě.

> [!NOTE]
> DnsManagementClient podporuje tři režimy operace: synchronní ('CreateOrUpdate'), asynchronní ('CreateOrUpdateAsync') nebo asynchronní s přístupem k odpovědi HTTP ('CreateOrUpdateWithHttpMessagesAsync').  Můžete zvolit některý z těchto režimů, v závislosti na potřebách aplikace.

Azure DNS podporuje optimistickou souběžnost, nazvanou [Etags](dns-getstarted-create-dnszone.md). V tomto příkladu zadání "*" pro hlavičku If-None-Match říká Azure DNS k vytvoření zóny DNS, pokud ještě neexistuje.  Volání se nezdaří, pokud zóna s daným názvem již v dané skupině prostředků existuje.

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

## <a name="create-dns-record-sets-and-records"></a>Vytvoření sad a záznamů záznamů DNS

Záznamy DNS jsou spravovány jako sada záznamů. Sada záznamů je sada záznamů se stejným názvem a typem záznamu v rámci zóny.  Název sady záznamů je relativní vzhledem k názvu zóny, nikoli k plně kvalifikovanému názvu DNS.

Chcete-li vytvořit nebo aktualizovat sadu záznamů, je vytvořen objekt `DnsManagementClient.RecordSets.CreateOrUpdateAsync`parametrů "RecordSet" a předán . Stejně jako u zón DNS existují tři režimy operace: synchronní ('CreateOrUpdate'), asynchronní ('CreateOrUpdateAsync') nebo asynchronní s přístupem k odpovědi HTTP ('CreateOrUpdateWithHttpMessagesAsync').

Stejně jako u zón DNS zahrnují operace v sadách záznamů podporu optimistické souběžnosti.  V tomto příkladu vzhledem k tomu, že nejsou zadány "If-Match" ani If-None-Match, je vždy vytvořena sada záznamů.  Toto volání přepíše všechny existující sady záznamů se stejným názvem a typem záznamu v této zóně DNS.

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

## <a name="get-zones-and-record-sets"></a>Získání zón a sad záznamů

Metody `DnsManagementClient.Zones.Get` `DnsManagementClient.RecordSets.Get` a načítají jednotlivé zóny a sady záznamů. Sady záznamů jsou označeny jejich typem, názvem a zónou a skupinou prostředků, ve které existují. Zóny jsou označeny svým názvem a skupinou prostředků, ve které existují.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Aktualizace existující sady záznamů

Chcete-li aktualizovat existující sadu záznamů DNS, nejprve ji načtěte, potom aktualizujte obsah sady záznamů a odešlete změnu.  V tomto příkladu určíme "Etag" z načteného záznamu nastaveného v parametru If-Match. Volání se nezdaří, pokud souběžná operace mezitím upravila nastavený záznam.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Seznam zón a sad záznamů

Chcete-li vypsat zóny, použijte metody *DnsManagementClient.Zones.List...,* které podporují výpis všech zón v dané skupině prostředků nebo všechny zóny v daném předplatném Azure (napříč skupinami prostředků.) Chcete-li seznam sad záznamů, použijte metody *DnsManagementClient.RecordSets.List...,* které podporují buď seznam všech sad záznamů v dané zóně, nebo pouze ty sady záznamů určitého typu.

Všimněte si, že při výpisu zón a sad záznamů mohou být výsledky stránkovány.  Následující příklad ukazuje, jak iterate prostřednictvím stránek výsledků. (Uměle malá velikost stránky '2' se používá k vynucení stránkování; v praxi by měl být tento parametr vynechán a použita výchozí velikost stránky.)

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

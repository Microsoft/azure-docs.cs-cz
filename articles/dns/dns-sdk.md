---
title: Vytvoření zóny DNS a sady záznamů v Azure DNS pomocí sady .NET SDK | Microsoft Docs
description: Postup vytvoření zóny DNS a sady záznamů v Azure DNS pomocí .NET SDK.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: kumud
ms.openlocfilehash: 645bf755cc3cb4036104145765196fc97039c551
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
ms.locfileid: "32772292"
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Vytvoření zóny DNS a sad záznamů pomocí sady .NET SDK

Operace vytvoření, odstranění nebo aktualizaci zóny, sady záznamů a záznamů DNS pomocí sady SDK DNS Knihovna správy DNS .NET můžete automatizovat. Úplný projekt Visual Studio je k dispozici [sem.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Vytvořit hlavní účet služby

Obvykle je programový přístup k prostředkům Azure udělit prostřednictvím vyhrazený účet, nikoli pověření uživatele. Tyto vyhrazené účty se označují jako účty instanční objekt. Pokud chcete používat Azure DNS SDK ukázkový projekt, musíte nejprve vytvořit hlavní účet služby a přiřadit jí správná oprávnění.

1. Postupujte podle [tyto pokyny](../azure-resource-manager/resource-group-authenticate-service-principal.md) k vytvoření účtu objektu služby (ukázkový projekt Azure DNS SDK předpokládá ověřování založené na heslech.)
2. Vytvoření skupiny prostředků ([tady je způsob](../azure-resource-manager/resource-group-template-deploy-portal.md)).
3. Udělte hlavní účet služby oprávnění, Přispěvatel zóny DNS, do skupiny prostředků pomocí Azure RBAC ([tady je způsob](../role-based-access-control/role-assignments-portal.md).)
4. Pokud používáte Azure DNS SDK ukázkový projekt, upravte soubor 'program.cs' následujícím způsobem:

   * Vložte správné hodnoty tenantId, clientId (také označované jako ID účtu), tajný klíč (heslo hlavní účet služby) a ID předplatného jako použít v kroku 1.
   * Zadejte název skupiny prostředků vybrané v kroku 2.
   * Zadejte název zóny DNS podle svého výběru.

## <a name="nuget-packages-and-namespace-declarations"></a>Balíčky NuGet a deklarace oboru názvů

Pokud chcete používat .NET SDK služby Azure DNS, musíte nainstalovat **Knihovna správy Azure DNS** balíček NuGet a další požadované balíčky Azure.

1. V **Visual Studio**, otevřete projekt nebo nový projekt.
2. Přejděte na **nástroje** **>** **Správce balíčků NuGet** **>** **spravovat balíčky NuGet pro řešení...** .
3. Klikněte na tlačítko **Procházet**, povolte **zahrnout předběžné verze** zaškrtávací políčko a typ **Microsoft.Azure.Management.Dns** do vyhledávacího pole.
4. Vyberte balíček a klikněte na tlačítko **nainstalovat** tím ho přidáte do projektu sady Visual Studio.
5. Opakujte tento postup výše a také instalace následujících balíčků: **Microsoft.Rest.ClientRuntime.Azure.Authentication** a **Microsoft.Azure.Management.ResourceManager**.

## <a name="add-namespace-declarations"></a>Přidání deklarací oboru názvů

Přidejte následující deklarace oborů názvů

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>Inicializovat správy klienta DNS

*DnsManagementClient* obsahuje metody a vlastnosti, které jsou nezbytné pro správu zón DNS a sady záznamů.  Následující kód připojí k hlavní účet služby a vytvoří objekt DnsManagementClient.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>Vytvořit nebo aktualizovat zónu DNS

Pokud chcete vytvořit zónu DNS, nejdřív "Zóna" je vytvořen objekt obsahovat parametry zóny DNS. Protože zóny DNS nejsou propojeny v určité oblasti, umístění se nastaví 'globální'. V tomto příkladu [Azure Resource Manager 'značka'](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) je taky přidaný do zóny.

Ve skutečnosti vytvořit nebo aktualizovat zónu v Azure DNS, je předaný objekt zóny obsahující parametry zóny *DnsManagementClient.Zones.CreateOrUpdateAsyc* metoda.

> [!NOTE]
> DnsManagementClient podporuje tři režimy činnosti: synchronní ('CreateOrUpdate'), asynchronní ('CreateOrUpdateAsync'), nebo asynchronní s přístupem do odpovědi HTTP (CreateOrUpdateWithHttpMessagesAsync).  Můžete použít některý z těchto režimů, v závislosti na potřebách vaší aplikace.

Azure DNS podporuje optimistickou metodu souběžného, nazývá [značky etag binárním rozsáhlým](dns-getstarted-create-dnszone.md). V tomto příkladu zadání "*" "If-None-Match' Hlavička uvádí Azure DNS pro vytvoření zóny DNS, pokud ještě neexistuje.  Volání selže, pokud zónu s daným názvem již existuje ve skupině pro daný prostředek.

```cs
// Create zone parameters
var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"

// Create a Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
dnsZoneParams.Tags = new Dictionary<string, string>();
dnsZoneParams.Tags.Add("dept", "finance");

// Create the actual zone.
// Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
// Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
// Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");
```

## <a name="create-dns-record-sets-and-records"></a>Vytvoření sady záznamů DNS a záznamů

Záznamy DNS jsou spravovány jako sadu záznamů. Sada záznamů je sada záznamů se stejným názvem a typ záznamu v rámci zóny.  Název sady záznamů je relativní vzhledem ke název zóny, není plně kvalifikovaný název DNS.

Pokud chcete vytvořit nebo aktualizovat sadu záznamů, se vytvoří a předaný objekt parametry "Sady záznamů" *DnsManagementClient.RecordSets.CreateOrUpdateAsync*. Jako s zóny DNS, existují tři režimy operace: synchronní ('CreateOrUpdate'), asynchronní ('CreateOrUpdateAsync'), nebo asynchronní s přístupem do odpovědi HTTP (CreateOrUpdateWithHttpMessagesAsync).

Stejně jako u zóny DNS operací na sady záznamů zahrnují podporu pro optimistickou metodu souběžného.  V tomto příkladu protože nejsou zadány 'If-Match' ani 'If-None-Match', sada záznamů je vytvořen vždy.  Toto volání přepíše všechny existující sady záznamů s stejný název a typ záznamu v této zóně DNS.

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

*DnsManagementClient.Zones.Get* a *DnsManagementClient.RecordSets.Get* metody načtení jednotlivých zóny a sady záznamů v uvedeném pořadí. Sady záznamů jsou identifikovány jejich typu, názvu a skupině zóny a prostředků, které existují v. Zóny jsou identifikovány jejich název a skupina prostředků, které existují v.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Aktualizovat existující sady záznamů

Chcete-li aktualizovat existující sady záznamů DNS, nejdřív načíst sadu záznamů a pak aktualizovat obsah sady záznamů, pak odešlete změny.  V tomto příkladu určíme, Etag, ze sady načtené záznam v parametru 'If-Match'. Volání selže, pokud souběžnou operací změnil mezitím sady záznamů.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Seznam zón a sady záznamů

Chcete-li seznam zón, použijte *DnsManagementClient.Zones.List...*  metody, které podporují výpis buď zón v dané skupiny prostředků nebo všech zón v daném předplatném Azure (v rámci skupiny prostředků.) Chcete-li seznam sad záznamů, použijte *DnsManagementClient.RecordSets.List...*  metody, které podporují výpis všech sad záznamů v dané zóně nebo pouze tyto sady záznamů určitého typu.

Poznamenejte si při výpisu zóny a může čísla stránek vložena sady záznamů, které výsledků.  Následující příklad ukazuje, jak k iteraci v rámci stránkách s výsledky. (Velikostí stránky uměle malý (2) slouží k vynucení stránkování; v praxi by tento parametr vynechán a použít výchozí velikost stránky.)

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

## <a name="next-steps"></a>Další postup

Stažení [.NET SDK služby Azure DNS ukázkového projektu](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), který obsahuje další příklady použití DNS .NET SDK služby Azure, včetně příkladů pro jiné typy záznamů DNS.

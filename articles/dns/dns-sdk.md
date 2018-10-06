---
title: Vytvoření zóny DNS a sad záznamů v DNS Azure pomocí sady .NET SDK | Dokumentace Microsoftu
description: Postup vytvoření sady záznamů a zón DNS v Azure DNS pomocí sady .NET SDK.
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: victorh
ms.openlocfilehash: 7acc0fa4c3654c96ac0f8f1baed7ea5b7b306376
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2018
ms.locfileid: "48829765"
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Vytvoření zóny DNS a sad záznamů pomocí sady .NET SDK

Můžete automatizovat operací vytvořit, odstranit nebo aktualizovat záznamy, sady záznamů a zón DNS pomocí sady SDK služby DNS s knihovnou rozhraní .NET pro správu DNS. Úplný projekt aplikace Visual Studio je k dispozici [tady.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Vytvořit instanční objekt

Programový přístup k prostředkům Azure, obvykle je poskytnuto prostřednictvím vyhrazený účet, nikoli pověření uživatele. Tyto vyhrazené účty se označují jako objekt zabezpečení účtů. Pokud chcete použít sadu SDK Azure DNS ukázkového projektu, musíte nejprve vytvořit instanční objekt a přiřaďte ho správná oprávnění.

1. Postupujte podle [tyto pokyny](../azure-resource-manager/resource-group-authenticate-service-principal.md) k vytvoření instančního objektu účtu služby (sada SDK pro Azure DNS ukázkového projektu předpokládá ověřování pomocí hesla.)
2. Vytvořte skupinu prostředků ([tady je způsob](../azure-resource-manager/resource-group-template-deploy-portal.md)).
3. Použít pro udělení hlavní účet služby oprávnění "Přispěvatel zóny DNS" do skupiny prostředků Azure ([tady je způsob](../role-based-access-control/role-assignments-portal.md).)
4. Pokud používáte sadu SDK Azure DNS ukázkového projektu, upravte soubor "program.cs" následujícím způsobem:

   * Vložit správné hodnoty `tenantId`, `clientId` (také označované jako ID účtu) `secret` (heslo instančního objektu účtu služby) a `subscriptionId` použili v kroku 1.
   * Zadejte název skupiny prostředků vybrané v kroku 2.
   * Zadejte název zóny DNS podle vašeho výběru.

## <a name="nuget-packages-and-namespace-declarations"></a>Balíčky NuGet a deklarace oboru názvů

Pokud chcete použít .NET SDK služby Azure DNS, je potřeba nainstalovat **Azure DNS Management Library** balíčku NuGet a další požadované balíčky Azure.

1. V **sady Visual Studio**, otevřete projekt nebo nový projekt.
2. Přejděte na **nástroje** **>** **Správce balíčků NuGet** **>** **spravovat balíčky NuGet pro Řešení...** .
3. Klikněte na tlačítko **Procházet**, povolte **zahrnout předběžné verze** zaškrtávací políčko a typ **Microsoft.Azure.Management.Dns** do vyhledávacího pole.
4. Vyberte balíček a klikněte na tlačítko **nainstalovat** ho přidat do projektu sady Visual Studio.
5. Postupujte stejně jako výše a také nainstalujte následující balíčky: **Microsoft.Rest.ClientRuntime.Azure.Authentication** a **Microsoft.Azure.Management.ResourceManager**.

## <a name="add-namespace-declarations"></a>Přidání deklarací oboru názvů

Přidejte následující deklarace oboru názvů

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>Inicializace klienta správy DNS

`DnsManagementClient` Obsahuje metody a vlastnosti, které jsou nezbytné pro správu sady záznamů a zón DNS.  Následující kód do hlavní účet služby přihlásí a vytvoří `DnsManagementClient` objektu.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>Vytvoření nebo aktualizaci zóny DNS

Vytvoření zóny DNS, nejdřív "Zóny" je vytvořen objekt obsahovat parametry zóny DNS. Protože zóny DNS nejsou připojeny na konkrétní oblasti, umístění se nastaví "globální". V tomto příkladu [Azure Resource Manageru 'značky'](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) je taky přidaný do zóny.

Ve skutečnosti vytvořit nebo aktualizovat zónu v Azure DNS, je předán objekt zóny obsahuje parametry zóny `DnsManagementClient.Zones.CreateOrUpdateAsyc` metody.

> [!NOTE]
> DnsManagementClient podporuje tři režimy činnosti: synchronní ("CreateOrUpdate"), asynchronní ("CreateOrUpdateAsync"), nebo asynchronní s přístupem k odpovědi protokolu HTTP (CreateOrUpdateWithHttpMessagesAsync).  Můžete vybrat kteroukoli z těchto režimů, v závislosti na potřebách aplikace.

Azure DNS podporuje optimistické řízení souběžnosti, volá [značek etag](dns-getstarted-create-dnszone.md). V tomto příkladu zadáte "*" "If-None-Match' záhlaví zobrazuje Azure DNS k vytvoření zóny DNS, pokud ještě neexistuje.  Volání selže, pokud zóna s daným názvem již existuje v dané skupině prostředků.

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

## <a name="create-dns-record-sets-and-records"></a>Vytvořte záznamy a sadami záznamů DNS

Záznamy DNS se spravují jako sady záznamů. Sady záznamů je sada záznamů se stejným názvem a typ záznamu v rámci zóny.  Název sady záznamů je relativní vzhledem k názvu zóny, ne plně kvalifikovaný název DNS.

Vytvořit nebo aktualizovat sadu záznamů, objekt parametry "Sady záznamů" vytvořen a předán `DnsManagementClient.RecordSets.CreateOrUpdateAsync`. Jak se zónami DNS existují tři režimy operace: synchronní ("CreateOrUpdate"), asynchronní ("CreateOrUpdateAsync"), nebo asynchronní s přístupem k odpovědi protokolu HTTP (CreateOrUpdateWithHttpMessagesAsync).

Stejně jako u zóny DNS, operace v sadách záznamů zahrnují podporu pro optimistického řízení souběžnosti.  V tomto příkladu protože nejsou zadány "If-Match" ani "If-None-Match", sady záznamů je vytvořen vždy.  Toto volání přepíše všechny existující sady záznamů se stejným názvem a typ záznamu v této zóny DNS.

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

## <a name="get-zones-and-record-sets"></a>Získejte zón a sad záznamů

`DnsManagementClient.Zones.Get` a `DnsManagementClient.RecordSets.Get` metody načtení jednotlivých zón a sad záznamů v uvedeném pořadí. Sady záznamů jsou označeny podle jejich typu, názvu a zóny a skupinu prostředků, ve kterém existují. Zóny jsou označeny podle jejich názvu a skupinu prostředků, ve kterém existují.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Aktualizovat existující sady záznamů

K aktualizaci existující sady záznamů DNS, nejdřív načíst sady záznamů a potom aktualizovat obsah sady záznamů, potom odešlete změny.  V tomto příkladu zadáme "Etag" ze sady záznamů načtených v parametru "If-Match". Volání selže, pokud souběžnou operací změnil mezitím sady záznamů.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Výpis zón a sad záznamů

Seznam zón, použijte *DnsManagementClient.Zones.List...*  metody, které podporují výpis buď všechny zóny v dané skupiny prostředků nebo všechny zóny v rámci daného předplatného Azure (v rámci skupiny prostředků.) Seznam sad záznamů, použijte *DnsManagementClient.RecordSets.List...*  metody, které podporují výpis všech sad záznamů v dané oblasti nebo pouze tyto sady záznamů určitého typu.

Mějte na paměti při výpisu zóny a může být stránkované sady záznamů, které výsledky.  Následující příklad ukazuje, jak k iteraci v rámci stránky výsledků. (Stránka uměle malé množství '2' slouží k vynucení stránkování; v praxi by měl být tento parametr vynechán a použít výchozí velikost stránky.)

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

Stáhněte si [.NET SDK pro Azure DNS ukázkového projektu](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), který obsahuje další příklady, jak používat Azure DNS .NET SDK, včetně příkladů, pro další typy záznamů DNS.

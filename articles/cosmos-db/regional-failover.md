---
title: Místní převzetí služeb při selhání v Azure Cosmos DB | Microsoft Docs
description: Další informace o tom, jak ruční a automatické převzetí služeb při selhání funguje s Azure Cosmos DB.
services: cosmos-db
documentationcenter: ''
author: arramac
manager: kfile
editor: ''
ms.assetid: 446e2580-ff49-4485-8e53-ae34e08d997f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4a221a0d4f9ef6b6b32ed9b684939b9f277e2084
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="automatic-regional-failover-for-business-continuity-in-azure-cosmos-db"></a>Automatické regionální převzetí služeb při selhání pro kontinuitu podnikových procesů v Azure Cosmos DB
Azure Cosmos DB zjednodušuje globální distribuci dat tím, že nabídka plně spravované, [účty databáze více oblast](distribute-data-globally.md) , poskytovat jasné kompromisy mezi konzistencí, dostupnosti a výkonu, všechny odpovídající záruky. Účty cosmos DB nabízí vysokou dostupnost, jednu číslici ms latenci, [dobře definované úrovně konzistence](consistency-levels.md), transparentní regionální převzetí služeb při selhání s více funkci rozhraní API a schopnost Elasticky škálovat propustnost a úložiště napříč zeměkouli. 

Cosmos DB podporuje obě explicitní a zásad řízené převzetí služeb při selhání, umožňují řídit chování systému začátku do konce v případě selhání. V tomto článku se podíváme na to:

* Jak fungují ruční převzetí služeb při selhání v systému Cosmos DB?
* Jak pracovní automatické převzetí služeb při selhání v systému Cosmos DB a co se stane, když data center přejde dolů?
* Jak můžete použít ruční převzetí služeb při selhání v architekturách aplikací?

Můžete si také přečíst o místní převzetí služeb při selhání v tomto videu pomocí Azure manažer programu DB Cosmos Andrew Liu, která demonstruje globální distribuční funkce včetně regionální převzetí služeb při selhání.

>[!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]
>

## <a id="ConfigureMultiRegionApplications"></a>Konfigurace aplikací s více oblast
Před jsme ponořte do režimy převzetí služeb při selhání, podíváme se na konfiguraci aplikace a využívat výhod dostupnost v několika oblastech být odolné při krátkodobém regionální převzetí služeb při selhání.

* Nejdřív Nasaďte aplikaci do několika oblastí
* Aby se zajistilo přístup s nízkou latencí z každé oblasti vaše aplikace je nasazená, nakonfigurujte příslušné [seznam upřednostňovaných oblasti](https://msdn.microsoft.com/library/microsoft.azure.documents.client.connectionpolicy.preferredlocations.aspx#P:Microsoft.Azure.Documents.Client.ConnectionPolicy.PreferredLocations) pro každou oblast prostřednictvím jeden z podporovaných sad SDK.

Následující fragment kódu ukazuje, jak k chybě při inicializaci aplikace určené pro více oblastí. Tady, účet Azure Cosmos DB `contoso.documents.azure.com` je nakonfigurovaný se dvěma oblastmi - západní USA a Severní Evropa. 

* Aplikace je nasazená v oblasti západní USA (například pomocí služby Azure App Services) 
* Nakonfigurované s `West US` jako první upřednostňovaná oblast pro s nízkou latencí čte
* Nakonfigurované s `North Europe` jako druhý upřednostňovaná oblast (pro vysokou dostupnost při místní selhání)

V rozhraní SQL API tato konfigurace vypadá následující fragment kódu:

```cs
ConnectionPolicy usConnectionPolicy = new ConnectionPolicy 
{ 
    ConnectionMode = ConnectionMode.Direct,
    ConnectionProtocol = Protocol.Tcp
};

usConnectionPolicy.PreferredLocations.Add(LocationNames.WestUS);
usConnectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

DocumentClient usClient = new DocumentClient(
    new Uri("https://contosodb.documents.azure.com"),
    "memf7qfF89n6KL9vcb7rIQl6tfgZsRt5gY5dh3BIjesarJanYIcg2Edn9uPOUIVwgkAugOb2zUdCR2h0PTtMrA==",
    usConnectionPolicy);
```

Aplikace je nasazená taky v oblasti Severní Evropa s pořadím oblastí upřednostňované vrátit zpět. To znamená oblasti Severní Evropa je nejprve určené pro čtení s nízkou latencí. Potom oblast západní USA je zadán jako druhý upřednostňovaná oblast pro zajištění vysoké dostupnosti při selhání místní.

Následující diagram architektury ukazuje k nasazení aplikace s více oblast konfigurovaným Cosmos databáze a aplikace byla k dispozici v čtyři Azure zeměpisné oblasti.  

![Globálně distribuované nasazení s Azure Cosmos DB](./media/regional-failover/app-deployment.png)

Teď se podíváme, jak služba Cosmos DB zpracovává místní selhání prostřednictvím automatické převzetí služeb při selhání. 

## <a id="AutomaticFailovers"></a>Automatické převzetí služeb při selhání
V výjimečná událost Azure regionální výpadku nebo výpadku datového centra Cosmos DB automaticky aktivuje převzetí služeb při selhání všech účtů Cosmos DB s přítomnosti v ovlivněných oblasti. 

**Co se stane, pokud má čtení oblast výpadku?**

Cosmos DB účty s čtení oblastí v jednom z ovlivněných oblasti jsou automaticky odpojen od jejich oblast zápisu a označeny offline. Sady SDK DB Cosmos implementovat regionální zjišťování protokol, který umožňuje, aby automaticky rozpoznat, kdy je k dispozici v oblasti a přesměrování číst volání další oblasti k dispozici v seznamu upřednostňovaná oblast. Pokud žádná z oblasti v seznamu upřednostňovaná oblast není k dispozici, volání automaticky vrátit k aktuální oblasti zápisu. Zpracování místní převzetí služeb při selhání se nevyžadují žádné změny v kódu aplikace. Během celého tohoto procesu dál záruku konzistence se berou v úvahu Cosmos DB.

![Chyby čtení oblasti v Azure Cosmos DB](./media/regional-failover/read-region-failures.png)

Jakmile ovlivněný oblast obnoví z se výpadek, se automaticky obnoví všechny dotčené účty Cosmos DB v oblasti službou. Cosmos DB účty, které měl čtení oblast v oblasti ovlivněných se pak automaticky synchronizovat s aktuální oblast zápisu a zapněte online. Sady SDK DB Cosmos zjišťovat dostupnost nové oblasti a vyhodnotit, zda je oblast, měl by být vybrán jako aktuální čtení oblast na základě seznamu upřednostňovaná oblast konfigurovat tak, že aplikace. Další čtení zprávy jsou přesměrovány do oblasti obnovená bez nutnosti změny kódu aplikace.

**Co se stane, pokud oblast zápisu má výpadek?**

Pokud ovlivněných oblast je aktuální oblasti zápis a automatické převzetí služeb při selhání je povolen pro účet Azure Cosmos DB, pak oblast je automaticky označeny jako offline. Oblast alternativní se poté vyzval jako oblasti zápisu pro ovlivněné účet Azure Cosmos DB. Můžete povolit automatické převzetí služeb při selhání a plnou kontrolu nad pořadí výběru oblast pro vaše účty Azure Cosmos DB prostřednictvím portálu Azure nebo [prostřednictvím kódu programu](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_FailoverPriorityChange). 

![Priorit převzetí služeb při selhání pro Azure Cosmos DB](./media/regional-failover/failover-priorities.png)

Při automatické převzetí služeb při selhání Azure Cosmos DB automaticky zvolí další zápisu oblast pro daný účet Azure Cosmos DB podle pořadí priority zadané. Aplikace můžete použít vlastnost WriteEndpoint DocumentClient třídy ke zjištění změn v oblasti zápisu.

![Oblast chyby zápisu v Azure Cosmos DB](./media/regional-failover/write-region-failures.png)

Jakmile ovlivněný oblast obnoví z se výpadek, se automaticky obnoví všechny dotčené účty Cosmos DB v oblasti službou. 

* Data obsažená v oblasti předchozí zápisu, který nebyl replikován číst oblasti během se výpadek je publikován jako konflikt informačního kanálu. Aplikace můžete číst informační kanál ke konfliktu, řešení konfliktů na základě logiky konkrétní aplikace a zpětný zápis aktualizovaná data do účtu Azure Cosmos DB podle potřeby. 
* Předchozí oblast zápisu je znovu vytvořen jako čtení oblasti a vrátí do režimu online automaticky. 
* Můžete změnit konfiguraci čtení oblast, která se vrátí do režimu online automaticky jako oblasti zápisu provedením ruční převzetí služeb při selhání prostřednictvím portálu Azure nebo [prostřednictvím kódu programu](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_CreateOrUpdate).

Následující fragment kódu ukazuje, jak zpracovat konflikty po oblasti ovlivněných obnoví z se výpadek.

```cs
string conflictsFeedContinuationToken = null;
do
{
    FeedResponse<Conflict> conflictsFeed = client.ReadConflictFeedAsync(collectionLink,
        new FeedOptions { RequestContinuation = conflictsFeedContinuationToken }).Result;

    foreach (Conflict conflict in conflictsFeed)
    {
        Document doc = conflict.GetResource<Document>();
        Console.WriteLine("Conflict record ResourceId = {0} ResourceType= {1}", conflict.ResourceId, conflict.ResourceType);

        // Perform application specific logic to process the conflict record / resource
    }

    conflictsFeedContinuationToken = conflictsFeed.ResponseContinuation;
} while (conflictsFeedContinuationToken != null);
```

## <a id="ManualFailovers"></a>Ruční převzetí služeb při selhání

Kromě automatické převzetí služeb při selhání aktuální oblasti zápisu daného účtu Cosmos DB můžete ručně změnit dynamicky na jednu z existujících čtení oblasti. Ruční převzetí služeb při selhání lze inicializovat prostřednictvím portálu Azure nebo [prostřednictvím kódu programu](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_CreateOrUpdate). 

Ruční převzetí služeb při selhání zkontrolujte **nula ztráty dat** a **nula dostupnosti** ztrátě a řádně stav zápisu přenos ze starého zapisovat oblast novým pro zadaný účet Cosmos DB. Jako v automatické převzetí služeb při selhání, sada SDK DB Cosmos automaticky zpracuje zápisu oblast změny během ruční převzetí služeb při selhání a zajistí, že volání automaticky přesměrováni na novou oblast zápisu. V aplikaci Správa převzetí služeb při selhání se nevyžadují žádné změny kódu nebo konfigurace. 

![Ruční převzetí služeb při selhání v Azure Cosmos DB](./media/regional-failover/manual-failovers.png)

Jsou některé běžné scénáře, kde může být užitečné ruční převzetí služeb při selhání:

**Postupujte podle modelu hodiny**: Pokud vaše aplikace vzory předvídatelný přenosů dat na základě času dne, můžete změnit stav zápisu pravidelně Nejaktivnější geografické oblasti na základě času dne.

**Aktualizace služby**: určité nasazení globálně distribuované aplikace může zahrnovat přesměrování spojnic provoz na jiné oblasti prostřednictvím Správce provozu během jejich aktualizace plánované služby. Nasazení takové aplikace teď můžete použít ruční převzetí služeb při selhání Pokud chcete zachovat stav zápisu oblast, kde existuje bude active provozu během časového období aktualizace služby.

**Kontinuita podnikových procesů a zotavení po havárii (BCDR) a vysokou dostupnost a zotavení po havárii (HADR) cvičení**: většinu aplikací enterprise zahrnují testy kontinuity obchodních jako součást procesu jejich vývoj a verzi. Důležitým krokem při certifikace dodržování předpisů a záruční dostupnost služeb v případě místní výpadky je často BCDR a HADR testování. Můžete otestovat připravenosti BCDR vaší aplikace, které používají Cosmos DB pro úložiště podle aktivován ruční převzetí služeb při selhání účtu Cosmos DB nebo přidávání a odebírání oblast dynamicky.

V tomto článku jsme přečetli jak ruční a automatické převzetí služeb při selhání práci v Cosmos DB, a jak můžete nakonfigurovat účty Cosmos DB a globálně dostupnou aplikací. S využitím podpory Cosmos DB globální replikace, můžete zlepšit latenci začátku do konce a ujistěte se, že jsou vysoce dostupné i v případě selhání oblast. 

## <a id="NextSteps"></a>Další kroky
* Další informace o tom, jak Cosmos DB podporuje [globální distribuční](distribute-data-globally.md)
* Další informace o [globální konzistence s Azure Cosmos DB](consistency-levels.md)
* Vývoj s více oblastí pomocí Azure Cosmos DB [rozhraní SQL API](tutorial-global-distribution-sql-api.md)
* Naučte se vytvářet [více oblast zapisovače architektury](multi-region-writers.md) s Azure Cosmos DB


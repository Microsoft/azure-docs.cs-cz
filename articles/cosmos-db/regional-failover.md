---
title: Regionální převzetí služeb při selhání ve službě Azure Cosmos DB | Dokumentace Microsoftu
description: Další informace o tom, jak ručního a automatického převzetí služeb při selhání funguje s Azure Cosmos DB.
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: govindk
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 697be3a1eb07b2f2650f3dd94fd835b9431aec6b
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "42055230"
---
# <a name="automatic-regional-failover-for-business-continuity-in-azure-cosmos-db"></a>Automatické regionální převzetí služeb při selhání pro zajištění kontinuity ve službě Azure Cosmos DB
Azure Cosmos DB zjednodušuje globální distribuce dat tím, že nabízí plně spravované [účty databáze ve více oblastech](distribute-data-globally.md) , které poskytují jasné kompromisy mezi konzistencí, dostupností a výkonem, a to vše s odpovídající záruky. Účty služby cosmos DB nabízí vysokou dostupnost, latenci ms latence v řádu, [jasně definovaných úrovní konzistence](consistency-levels.md), transparentní regionální převzetí služeb při selhání s rozhraními API pro vícenásobné navádění a umožňuje Elasticky škálovat propustnost a úložiště v rámci celém světě. 

Cosmos DB podporuje obě explicitní a zásady řízené převzetí služeb při selhání, které umožňují řídit chování systému začátku do konce v případě selhání. V tomto článku se podíváme na:

* Jak fungují ruční převzetí služeb při selhání ve službě Cosmos DB?
* Jak pracovní automatické převzetí služeb při selhání ve službě Cosmos DB a co se stane, když datové centrum přejde dolů?
* Jak můžete použít ruční převzetí služeb při selhání v aplikačních architektur?

Můžete také informace o regionálních převzetí služeb při selhání v tomto videu službou Azure Cosmos DB programový manažer Andrew Liu, která ukazuje funkce globální distribuce, včetně regionální převzetí služeb při selhání.

>[!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]
>

## <a id="ConfigureMultiRegionApplications"></a>Konfigurace aplikací ve více oblastech
Předtím, než se začneme zabývat režimy převzetí služeb při selhání, podíváme se na konfiguraci aplikace využívat dostupnost ve více oblastech a odolné proti chybám i v případě regionálních převzetí služeb při selhání.

* Nejdřív Nasaďte aplikaci v několika oblastech
* Pro zajištění nízké latence přístupu z každé oblasti máte aplikaci nasazenu, nakonfigurujte odpovídající [preferované oblasti seznamu](https://msdn.microsoft.com/library/microsoft.azure.documents.client.connectionpolicy.preferredlocations.aspx#P:Microsoft.Azure.Documents.Client.ConnectionPolicy.PreferredLocations) pro každou oblast některou z podporovaných sad SDK.

Následující fragment kódu ukazuje způsob inicializace aplikace v několika oblastech. Tady, účet služby Azure Cosmos DB `contoso.documents.azure.com` nakonfigurovaná se dvěma oblastmi - západní USA a Severní Evropa. 

* Aplikace je nasazená v oblasti USA – západ (například pomocí služby Azure App Services) 
* Nakonfigurovaným rozhraním `West US` jako první preferovaná oblast pro zajištění nízké latence čtení
* Nakonfigurovaným rozhraním `North Europe` jako druhý preferované oblasti (pro zajištění vysoké dostupnosti případě oblastních selhání)

Tato konfigurace bude vypadat jako následující fragment kódu v rozhraní SQL API:

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
    "<Fill your Cosmos DB account's AuthorizationKey>",
    usConnectionPolicy);
```

Aplikace je nasazená také v oblasti Severní Evropa s pořadím preferované oblasti vrátit zpět. To znamená oblasti Severní Evropa určena nejprve pro zajištění nízké latence čtení. Potom oblast západní USA je zadaný jako druhý preferovaná oblast pro zajištění vysoké dostupnosti případě oblastních selhání.

Následující diagram architektury ukazuje nasazení aplikace v několika oblastech, kde Cosmos DB a aplikace jsou nakonfigurované jako k dispozici ve čtyřech geografických oblastí Azure.  

![Nasazení globálně distribuovaných aplikací pomocí služby Azure Cosmos DB](./media/regional-failover/app-deployment.png)

Teď se podívejme se na způsob, jakým zpracovává regionálních selhání prostřednictvím automatického převzetí služeb při selhání služby Cosmos DB. 

## <a id="AutomaticFailovers"></a>Automatické převzetí služeb při selhání
V nepravděpodobném případě regionálního výpadku Azure nebo výpadku datového centra Cosmos DB automaticky aktivuje převzetí služeb při selhání všech účtů služby Cosmos DB činné v ovlivněné oblasti. 

**Co se stane, pokud má k výpadku oblasti čtení?**

Účty služby cosmos DB s oblastí čtení v oblastech ovlivněné jsou automaticky odpojí z oblasti pro zápis a označena offline. Cosmos DB SDK implementaci protokolu regionální zjišťování, která umožňuje, aby automaticky rozpoznat, kdy je k dispozici oblast a přesměrování čtení volání další dostupné oblasti v seznamu preferované oblasti. Pokud žádná z oblasti v seznamu preferované oblasti není k dispozici, volání automaticky vrátit do aktuální oblasti pro zápis. Nevyžaduje žádné změny v kódu aplikace zpracovávat regionální převzetí služeb při selhání. Během tohoto procesu celý záruky konzistence dál respektovat Cosmos DB.

![Selhání oblasti pro čtení ve službě Azure Cosmos DB](./media/regional-failover/read-region-failures.png)

Jakmile ovlivněný oblasti zotaví výpadek, služba se automaticky obnoví všechny ovlivněné účty služby Cosmos DB v oblasti. Účty služby cosmos DB, ke které došlo v oblasti ovlivněné oblasti čtení se pak automaticky synchronizovat s aktuální oblasti pro zápis a zapněte online. Cosmos DB SDK zjišťovat dostupnost nové oblasti a vyhodnotit, jestli oblast, měl by být vybrán jako aktuální oblasti pro čtení na základě seznamu preferované oblasti nakonfiguroval aplikaci. Další čtení se přesměrují na obnoveném oblasti nevyžaduje žádné změny kódu aplikace.

**Co se stane, pokud má k výpadku oblasti pro zápis?**

Pokud ovlivněné oblasti je aktuální oblasti pro zápis a automatické převzetí služeb při selhání je povolena pro účet služby Azure Cosmos DB, klikněte v oblasti je automaticky označen jako offline. Alternativní oblast je pak podporován jako oblast pro zápis pro ovlivněné účtu Azure Cosmos DB. Můžete povolit automatické převzetí služeb při selhání a plně řídit pořadí oblast výběru účtů služby Azure Cosmos DB pomocí webu Azure portal nebo [programově](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_FailoverPriorityChange). 

![Priority převzetí služeb při selhání pro službu Azure Cosmos DB](./media/regional-failover/failover-priorities.png)

Během automatického převzetí služeb při selhání služby Azure Cosmos DB automaticky zvolí další oblasti pro daný účet služby Azure Cosmos DB v pořadí priority zadané pro zápis. Aplikace můžete použít vlastnost WriteEndpoint třídy DocumentClient zjišťovat změny v oblasti pro zápis.

![Selhání oblasti zápisu ve službě Azure Cosmos DB](./media/regional-failover/write-region-failures.png)

Jakmile ovlivněný oblasti zotaví výpadek, služba se automaticky obnoví všechny ovlivněné účty služby Cosmos DB v oblasti. 

* Data v předchozí oblasti pro zápis, který se replikuje do oblasti čtení během výpadku je publikován konflikt informačního kanálu. Aplikace může číst informační kanál ke konfliktu, řešení konfliktů na základě konkrétní aplikace logiky a zapsat aktualizovaná data zpět do účtu Azure Cosmos DB podle potřeby. 
* Předchozí oblasti pro zápis je znovu vytvořen jako oblasti čtení a opět nepřipojí online automaticky. 
* Můžete změnit konfiguraci oblastí čtení, který se vrátil zpátky do online režimu automaticky jako oblast zápisu provedením ruční převzetí služeb při selhání prostřednictvím portálu Azure portal nebo [programově](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_CreateOrUpdate).

Následující fragment kódu ukazuje, jak zpracovat konflikty po ovlivněné oblasti zotaví výpadek.

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

Kromě automatického převzetí služeb při selhání aktuální oblasti pro zápis z daného účtu služby Cosmos DB můžete ručně změnit dynamicky na jednu z existujících oblastí pro čtení. Ruční převzetí služeb při selhání můžete spuštěných prostřednictvím webu Azure portal nebo [programově](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_CreateOrUpdate). 

Ruční převzetí služeb při selhání zkontrolujte **nulové ztráty dat** a **nula dostupnosti** ztráty a řádně stav zápisu přenosu ze starého oblasti pro zápis do nové pro zadaný účet služby Cosmos DB. Stejně jako v automatické převzetí služeb při selhání, Cosmos DB SDK automaticky zpracovává změny oblast zápisu během ruční převzetí služeb při selhání a zajistí, že volání se automaticky přesměrují do nové oblasti pro zápis. Nevyžaduje žádné změny kódu nebo provedení konfigurace v aplikaci pro správu převzetí služeb při selhání. 

![Ruční převzetí služeb při selhání ve službě Azure Cosmos DB](./media/regional-failover/manual-failovers.png)

Zde jsou některé běžné scénáře, kde ruční převzetí služeb při selhání může být užitečné:

**Postupujte podle modelu hodinových**: Pokud vaše aplikace vzory předvídatelný provoz na základě času dne, můžete změnit stav zápisu pravidelně Nejaktivnější geografických oblastí na základě času dne.

**Aktualizace služby**: určité nasazení globálně distribuované aplikace může zahrnovat přesměrování provozu do jiné oblasti pomocí traffic Manageru během jejich aktualizace plánované služby. Nasazení těchto aplikací teď můžete použít ruční převzetí služeb při selhání zachovat stav zápisu do oblasti tam, kde se chystají se aktivní provoz během časového intervalu pro aktualizaci služby.

**Kontinuita podnikových procesů a zotavení po havárii (BCDR) a vysokou dostupnost a zotavení po havárii (HADR) cvičení**: Většina podnikových aplikací patří obchodní kontinuity podnikových procesů testy jako součást procesu vývoje a vydávání verzí. Důležitým krokem při certifikací dodržování předpisů a záruční dostupnost služeb v případě místních výpadků je často BCDR a HADR testování. Můžete otestovat BCDR připravenost aplikací, které používají služby Cosmos DB pro úložiště aktivaci ručního převzetí služeb při selhání z účtu služby Cosmos DB a/nebo přidáváním a odebíráním oblast dynamicky.

V tomto článku jsme se zaměřili na jak ručního a automatického převzetí služeb při selhání práce ve službě Cosmos DB a jak nakonfigurovat účty služby Cosmos DB a globální dostupnost aplikací. S využitím služby Cosmos DB globální replikace podpory, můžete zlepšit latenci začátku do konce a ověřte, že jsou s vysokou dostupností i v případě selhání oblasti. 

## <a id="NextSteps"></a>Další kroky
* Další informace o tom, jak služby Cosmos DB podporuje [globální distribuce](distribute-data-globally.md)
* Další informace o [globální soudržnosti pomocí služby Azure Cosmos DB](consistency-levels.md)
* Vývoj s využitím více oblastí pomocí služby Azure Cosmos DB [rozhraní SQL API](tutorial-global-distribution-sql-api.md)
* Další informace o vytváření [zapisovače ve více oblastech architektury](multi-region-writers.md) pomocí služby Azure Cosmos DB


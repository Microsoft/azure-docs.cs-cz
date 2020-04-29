---
title: Ukládání, Správa a uchovávání dat aplikací v cloudu pomocí Visual Studio App Center a služeb Azure
description: Přečtěte si o službách, jako je Visual Studio App Center, které umožňují ukládat, spravovat a uchovávat data mobilní aplikace v cloudu.
author: codemillmatt
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 166847325fa9094136f1c2a143f1751420f05f66
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240915"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Ukládání, synchronizace a dotazování dat mobilní aplikace z cloudu
Bez ohledu na to, jaký druh aplikace sestavíte, budete pravděpodobně generovat a zpracovávat data. Uživatelé vaší aplikace mají vysoké očekávání. Chtějí, aby aplikace fungovala rychle a hladce, a to za všech okolností. Většina aplikací funguje i napříč více zařízeními. K aplikaci můžete přistupovat z počítače nebo mobilního zařízení. Tuto aplikaci může používat více uživatelů současně a sdílet data se očekáváním, že budou mít přístup k datům v reálném čase.

Uživatelé vaší aplikace nebudou mít vždycky připojení k Internetu. Aplikace jsou navržené a očekává se pracovat s připojením k Internetu nebo bez něj. Vývojáři musí zvolit správné řešení pro ukládání a synchronizaci svých dat do cloudu, aby poskytovali Skvělé prostředí pro zákazníky ve svých aplikacích.

Společnost Microsoft poskytuje celou řadu služeb, které eliminují nutnost vystavovat servery, vybírat svou databázi nebo si dělat starosti se škálováním a zabezpečením, aby poskytovala co možná hodně možností. Tyto služby poskytují skvělé vývojové prostředí, které umožňuje ukládat data aplikací do cloudu pomocí rozhraní API SQL nebo NoSQL. Můžete také synchronizovat data na všech zařízeních a povolit aplikaci, aby fungovala s nebo bez síťového připojení, která vám pomůžou vytvářet škálovatelné a robustní aplikace.

Pomocí následujících služeb můžete spravovat a ukládat data mobilní aplikace v cloudu.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Data](/appcenter/data/) jsou služba pro správu dat. Umožňuje aplikacím spravovat, uchovávat a synchronizovat data aplikací v cloudu napříč různými zařízeními a platformami v online a offline scénářích. Data, která jsou postavená na Azure Cosmos DB, App Center škálují jako vaši uživatelskou základnu a rozroste počet aplikací. Zajišťuje nízkou latenci, vysokou dostupnost a vysokou škálovatelnost pro všechna vaše data.

**Klíčové funkce**
- Snadné zřizování nové databáze Azure Cosmos DB nebo existující databáze Azure Cosmos DB z portálu Visual Studio App Center.
- Podpora databáze NoSQL, která umožňuje snadno ukládat, synchronizovat a dotazovat data aplikací.
- Tato služba je postavená na Azure Cosmos DB, která poskytuje většinu klíčových funkcí nabízených v rámci Azure Cosmos DB a umožňuje globálně škálovat, aby splňovaly vaše obchodní potřeby.
- Možnosti online a offline synchronizace pro synchronizaci dat napříč zařízeními.
- Sady SDK mobilních klientů, pomocí kterých lze snadno spravovat data privátních aplikací.
- Podpora platforem pro iOS, Android, Xamarin a reakci na nativní.

**Odkazy**
- [Zaregistrujte se Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Začínáme s App Centermi daty](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) je globálně distribuovaná databázová služba pro více modelů. Můžete ji použít k sestavování aplikací globálním-Scale. Díky Azure Cosmos DB můžete elasticky a nezávisle škálovat propustnost a úložiště napříč libovolným počtem oblastí Azure po celém světě. Pomocí vašich oblíbených ploch rozhraní API můžete využít rychlý přístup k datům s jedním číslem a milisekundami. Mezi tyto povrchy patří SQL, MongoDB, Cassandra, Tables nebo Gremlin. Azure Cosmos DB jednoznačně poskytuje kompletní smlouvy o úrovni služeb (SLA) pro propustnost, latenci, dostupnost a konzistenci.

**Klíčové funkce**
- Podporuje široké spektrum rozhraní API, mezi které patří rozhraní API SQL (Core), rozhraní API Cassandra, rozhraní API MongoDB, rozhraní Gremlin API a rozhraní API pro tabulky.
- Globální distribuce klíč replikuje vaše data všude, kde jsou vaši uživatelé. Vaši uživatelé můžou pracovat s replikou dat, která jsou pro ně nejblíže.
- Žádná Správa schématu ani indexu, protože databázový stroj je plně nezávislá schématu.
- Všudypřítomný regionální přítomnost, protože je Azure Cosmos DB k dispozici ve všech oblastech Azure po celém světě, což zahrnuje 54 + oblasti ve veřejném cloudu.
- Přesně definované, vícenásobné volby konzistence, protože protokol replikace s více hlavními servery Azure Cosmos DB je pečlivě navržený tak, aby nabízel pět jasně definovaných voleb konzistence. Tato pět možností jsou silná, ohraničená neaktuálnost, relace, konzistentní předpona a případné.
- 99,999% dostupnost pro čtení i zápis.
- Prostřednictvím kódu programu (nebo prostřednictvím Azure Portal) se vyvolají místní převzetí služeb při selhání pro Azure Cosmos DB účet, aby se zajistilo, že je vaše aplikace navržená tak, aby odolala regionální
- Garantujeme nízkou latenci v 99 percentilu po celém světě.

**Odkazy**
- [portál Azure](https://portal.azure.com) 
- [Dokumentace k Azure Cosmos DB](/azure/cosmos-db/introduction)

## <a name="azure-sql-database"></a>Azure SQL Database
 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) je spravovaná služba relační databáze pro obecné účely. Můžete ho použít k vytvoření vysoce dostupné a vysoce výkonné vrstvy úložiště dat pro aplikace a řešení v cloudu Azure.

**Klíčové funkce**
- **Modely a nástroje elastické databáze:** V případě elastické databáze mohou vývojáři vytvořit fond prostředků mezi skupinami databází pro škálování. Chcete-li spravovat tyto prostředky administrativně, odešlete skript jako úlohu. Databáze SQL pak provede skript napříč databázemi.
- **Vysoký výkon:** Aplikace s vysokou propustností můžou využívat nejnovější verzi. Nabízí o 25% vyšší výkon databáze Premium.
- **Zálohy, replikace a vysoká dostupnost:** Integrovaná replikace a smlouva SLA založená na Microsoftu na úrovni databáze poskytují kontinuitu aplikací a ochranu před závažnými událostmi. Aktivní geografická replikace umožňuje nakonfigurovat převzetí služeb při selhání a samoobslužné obnovení, které poskytuje úplnou kontrolu nad "neobnovením". Obnovení dat je dostupné ze záloh dat až do 35 dnů.
- **Téměř nulová Údržba:** Automatický software je součástí služby. Integrované systémové repliky pomůžou zajistit ochranu dat, dobu provozu databáze a stabilitu systému. Systémové repliky se automaticky přesunou na nové počítače. Po selhání se zřídí jako staré.
- **Zabezpečení:** Azure SQL Database nabízí portfolio funkcí zabezpečení, které splňují zásady dodržování předpisů v organizacích nebo oborech.
    - Auditování poskytuje vývojářům možnost provádět úlohy související s dodržováním předpisů a získat poznatky o aktivitách.
    - Vývojáři a IT můžou implementovat zásady na úrovni databáze, abyste omezili přístup k citlivým datům pomocí zabezpečení na úrovni řádků, dynamického maskování dat a transparentního šifrování dat pro Azure SQL Database.
    - Služba Key Cloud Auditors ověřuje Azure SQL Database v rámci rozsahu klíčových certifikátů a schválení dodržování předpisů Azure, například HIPAA BAA, ISO/IEC 27001:2005, FedRAMP a vzorové klauzule EU.

**Odkazy**
- [portál Azure](https://portal.azure.com) 
- [Dokumentace k Azure SQL Database](/azure/sql-database/) 

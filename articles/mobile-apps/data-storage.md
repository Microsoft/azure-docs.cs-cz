---
title: Ukládání, správa a uchovávání dat aplikací v cloudu pomocí Visual Studio App Center a služeb Azure
description: Přečtěte si o službách, jako je Visual Studio App Center, které vám umožní ukládat, spravovat a uchovávat data mobilních aplikací v cloudu.
author: codemillmatt
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 166847325fa9094136f1c2a143f1751420f05f66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240915"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Ukládání, synchronizace a dotazování dat mobilních aplikací z cloudu
Bez ohledu na to, jaký druh aplikace vytvoříte, budete pravděpodobně generovat a zpracovávat data. Uživatelé vaší aplikace mají vysoká očekávání. Chtějí, aby aplikace fungovala rychle a hladce, za všech okolností. Většina aplikací také funguje na více zařízeních. K aplikaci můžete přistupovat z počítače nebo mobilního zařízení. Více uživatelů může používat aplikaci současně a sdílet data s očekáváním získání okamžitého a real-time přístup k datům.

Uživatelé aplikace nebudou mít vždy připojení k internetu. Aplikace jsou navrženy a očekává se, že budou pracovat s připojením k internetu nebo bez něj. Vývojáři musí zvolit správné řešení pro ukládání a synchronizaci dat do cloudu, aby poskytli skvělé prostředí pro zákazníky pro jejich aplikaci.

Společnost Microsoft poskytuje celou řadu služeb, které eliminují potřebu svítit servery, vybrat databázi nebo se starat o škálování nebo zabezpečení, aby poskytovaly co nejefektivnější možnosti. Tyto služby poskytují skvělé vývojářské prostředí, které vám umožní ukládat data aplikací v cloudu pomocí SQL nebo NoSQL API. Můžete také synchronizovat data na všech zařízeních a povolit aplikaci pracovat se síťovým připojením nebo bez něj, což vám pomůže vytvářet škálovatelné a robustní aplikace.

Ke správě a ukládání dat mobilních aplikací v cloudu použijte následující služby.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[Data Centra aplikací](/appcenter/data/) je služba pro správu dat. Umožňuje aplikacím spravovat, uchovávat a synchronizovat data aplikací v cloudu na různých zařízeních a platformách v online i offline scénářích. Data centra aplikací, postavená na Azure Cosmos DB, se škáluje podle toho, jak se vaše uživatelská základna a počet aplikací zvětšují. Zajišťuje nízkou latenci, vysokou dostupnost a vysokou škálovatelnost pro všechna data.

**Klíčové funkce**
- Snadné zřizování do nové databáze Azure Cosmos DB nebo existující databáze Azure Cosmos DB z portálu Visual Studio App Center.
- Podpora databáze NoSQL pro snadné ukládání, synchronizaci a dotazování na data aplikací.
- Tato služba, postavená na Azure Cosmos DB, dědí většinu klíčových funkcí nabízených službou Azure Cosmos DB a může globálně škálovat podle vašich obchodních potřeb.
- Možnosti online a offline synchronizace pro synchronizaci dat mezi zařízeními.
- Sady SDK pro mobilní klienty, které můžete použít ke snadné správě dat soukromých aplikací.
- Podpora platformy pro iOS, Android, Xamarin a React Native.

**Odkazy**
- [Registrace pomocí Centra aplikací Visual Studia](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Začínáme s daty Centra aplikací](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) je globálně distribuovaná databázová služba s více modely. Můžete ji použít k vytváření aplikací v měřítku planety. S Azure Cosmos DB můžete elasticky a nezávisle škálovat propustnost a úložiště v libovolném počtu oblastí Azure po celém světě. Můžete využít rychlý, jednociferný milisekundový přístup k datům pomocí oblíbených povrchů rozhraní API. Tyto povrchy zahrnují SQL, MongoDB, Cassandra, tabulky nebo Gremlin. Azure Cosmos DB jedinečně poskytuje komplexní smlouvy o úrovni služeb (SLA) pro propustnost, latenci, dostupnost a konzistenci.

**Klíčové funkce**
- Podporuje širokou škálu rozhraní API, která zahrnuje rozhraní API SQL (Core), rozhraní API Cassandra, rozhraní API MongoDB, rozhraní GREMLIN API a rozhraní TABLE API.
- Globální distribuce na klíč replikuje vaše data, ať jsou vaši uživatelé kdekoli. Uživatelé mohou pracovat s replikou dat, která jsou jim nejblíže.
- Žádné správu schématu nebo indexu, protože databázový stroj je plně agnostik schématu.
- Všudypřítomná místní přítomnost, protože Azure Cosmos DB je dostupná ve všech oblastech Azure po celém světě, která zahrnuje více než 54 oblastí ve veřejném cloudu.
- Přesně definované, více možností konzistence, protože Azure Cosmos DB multi-master replikační protokol je pečlivě navržen tak, aby nabídnout pět dobře definované možnosti konzistence. Těchto pět možností je silných, ohraničených neaktuálnost, relace, konzistentní předpona a eventuální.
- 99.999% dostupnost pro čtení i zápisy.
- Programově (nebo prostřednictvím portálu Azure) vyvolat místní převzetí služeb při selhání vašeho účtu Azure Cosmos DB k zajištění, že vaše aplikace je navržen tak, aby vydržely regionální havárii.
- Zaručená nízká latence na 99.

**Odkazy**
- [Portál Azure](https://portal.azure.com) 
- [Dokumentace azure cosmos db](/azure/cosmos-db/introduction)

## <a name="azure-sql-database"></a>Azure SQL Database
 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) je služba spravovaná pro obecné účely relační databáze. Můžete ji použít k vytvoření vysoce dostupné a vysoce výkonné vrstvy úložiště dat pro aplikace a řešení v cloudu Azure.

**Klíčové funkce**
- **Elastické databázové modely a nástroje:** S elastickou databází mohou vývojáři sdružovat prostředky mezi skupinou databází pro škálování. Chcete-li tyto prostředky spravovat administrativně, odešlete skript jako úlohu. Databáze SQL pak provede skript napříč databázemi.
- **Vysoký výkon:** Aplikace s vysokou propustností mohou využívat nejnovější verzi. Poskytuje o 25 % vyšší výkon prémiové databáze.
- **Zálohy, replikace a vysoká dostupnost:** Integrovaná replikace a zprostředkovatelská záloha microsoftu na úrovni databáze zajišťují kontinuitu aplikací a ochranu před katastrofickými událostmi. Aktivní geografická replikace umožňuje konfigurovat převzetí služeb při selhání a samoobslužné obnovení, které poskytují úplnou kontrolu nad obnovením jejosu. Obnova dat je k dispozici od zálohování dat až 35 dní.
- **Téměř nulová údržba:** Součástí služby je automatický software. Vestavěné systémové repliky pomáhají zajistit vlastní ochranu dat, dobu dostupnosti databáze a stabilitu systému. Systémové repliky jsou automaticky přesunuty do nových počítačů. Jsou zásobovány za běhu, protože staré selhávají.
- **Zabezpečení:** Azure SQL Database nabízí portfolio funkcí zabezpečení, které splňují zásady dodržování předpisů organizace nebo zásady dodržování předpisů nařízené v odvětví:
    - Auditování poskytuje vývojářům možnost provádět úlohy související s dodržováním předpisů a získávat znalosti o aktivitách.
    - Vývojáři a IT mohou implementovat zásady na úrovni databáze, které pomáhají omezit přístup k citlivým datům pomocí zabezpečení na úrovni řádků, dynamického maskování dat a transparentního šifrování dat pro Azure SQL Database.
    - Azure SQL Database je ověřována klíčovými auditory cloudu jako součást rozsahu klíčových certifikací a schválení dodržování předpisů Azure, jako jsou HIPAA BAA, ISO/IEC 27001:2005, FedRAMP a doložky modelu EU.

**Odkazy**
- [Portál Azure](https://portal.azure.com) 
- [Dokumentace k Azure SQL Database](/azure/sql-database/) 

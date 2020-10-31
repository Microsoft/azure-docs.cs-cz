---
title: Vytvoření kontejnerů a databází Azure Cosmos v režimu automatického škálování.
description: Přečtěte si o výhodách, případech použití a o tom, jak zřídit databáze a kontejnery Azure Cosmos v režimu automatického škálování.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: 78fbdbb91925bbe9954cc7a17e194de9c78c6389
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097341"
---
# <a name="create-azure-cosmos-containers-and-databases-with-autoscale-throughput"></a>Vytvoření kontejnerů a databází Azure Cosmos s využitím propustnosti automatického škálování
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB umožňuje nastavit standardní (ruční) nebo automatické škálování zřízené propustnosti databází a kontejnerů. Tento článek popisuje výhody a případy použití automatického škálování zřízené propustnosti. 

Zajištěná propustnost pro automatické škálování je vhodná pro klíčové úlohy, které mají proměnlivé nebo nepředvídatelné vzory přenosů dat, a vyžadují SLA s vysokým výkonem a škálováním. 

Díky automatickému škálování Azure Cosmos DB **automaticky a okamžitě škálovat propustnost (ru/s)** databáze nebo kontejneru na základě využití, aniž by to ovlivnilo dostupnost, latenci, propustnost nebo výkon zátěže. 

## <a name="benefits-of-autoscale"></a>Výhody automatického škálování

Databáze Azure Cosmos a kontejnery, které jsou nakonfigurované s zajištěnou propustností automatického škálování, mají následující výhody:

* **Jednoduché:** Automatické škálování odstraňuje složitost správy RU/s vlastními skriptováními nebo ručním škálováním kapacity. 

* **Škálovatelné:** Databáze a kontejnery automaticky škálují zřízenou propustnost podle potřeby. Nedošlo k přerušení připojení klientů, aplikací nebo vlivu na Azure Cosmos DB SLA.

* **Nákladově efektivní:** Automatické škálování pomáhá optimalizovat využití RU/s a náklady díky horizontálnímu škálování, pokud se nepoužívá. Platíte jenom za prostředky, které vaše úlohy potřebují po hodinách. Po všech hodinách v měsíci, pokud nastavíte automatické škálování max. RU/s (tmax) a použijete celou Tmaxu na 66% z hodin nebo méně, ušetříte s automatické škálováním. Další informace najdete v článku [postup výběru mezi standardním (ručním) a zajištěným zajištěním propustnosti v rámci automatického škálování](how-to-choose-offer.md) .

* **Vysoce dostupné:** Databáze a kontejnery využívající automatické škálování používají stejný globálně distribuovaný a vysoce dostupný Azure Cosmos DB back-end odolný proti chybám k zajištění odolnosti dat a vysoké dostupnosti.

## <a name="use-cases-of-autoscale"></a>Případy použití automatického škálování

Mezi případy použití automatického škálování patří:

* **Proměnné nebo nepředvídatelné úlohy:** Když vaše úlohy mají proměnlivé nebo nepředvídatelné špičky využití, automatické škálování pomáhá automaticky škálovat směrem nahoru a dolů na základě využití. Příklady zahrnují maloobchodní weby, které mají různé vzory přenosů v závislosti na sezónnostu. Úlohy IOT, které mají v průběhu dne špičky v různou dobu; obchodní aplikace, které vidí špičku použití několikrát za měsíc nebo rok a další. Díky automatickému škálování už nemusíte ručně zřizovat kapacitu nebo průměrnou kapacitu. 

* **Nové aplikace:** Pokud vyvíjíte novou aplikaci a nejste si jistí, jak propustnost (RU/s) potřebujete, automatické škálování usnadňuje začátek. Můžete začít s vstupním bodem automatického škálování 400 – 4000 RU/s, monitorovat své využití a zjistit správné RU/s v průběhu času.

* **Zřídka používané aplikace:** Pokud máte aplikaci, která se používá jenom pár hodin denně, týdně nebo měsíčně, jako je například aplikace s nízkým objemem nebo web nebo blogový web, automatické škálování přizpůsobuje kapacitu pro zpracování špičkového využití a při překročení kapacity rozškáluje. 

* **Vývojové a testovací úlohy:** Pokud vy nebo váš tým používáte databáze a kontejnery Azure Cosmos během pracovní doby, ale nepotřebujete je při nocích nebo víkendech, automatické škálování pomáhá ušetřit náklady snížením úrovně na minimum, pokud se nepoužívá. 

* **Plánované provozní úlohy a dotazy:** Pokud máte sérii plánovaných požadavků, operací nebo dotazů, které chcete spustit během nečinných období, můžete to provést snadno pomocí automatického škálování. Pokud potřebujete úlohu spustit, bude se propustnost automaticky škálovat podle potřeby a později se může škálovat. 

Sestavování vlastního řešení na tyto problémy nejen vyžaduje mimořádně dlouhou dobu, ale také zavádí složitost v konfiguraci nebo kódu vaší aplikace. Automatické škálování umožní výše uvedené scénáře vykonatelné z tohoto pole a odstraňuje nutnost vlastního nebo ručního škálování kapacity. 

## <a name="how-autoscale-provisioned-throughput-works"></a>Jak funguje zajištěná propustnost prostřednictvím automatického škálování

Při konfiguraci kontejnerů a databází pomocí automatického škálování určíte maximální požadovanou propustnost `Tmax` . Azure Cosmos DB škáluje propustnost `T` `0.1*Tmax <= T <= Tmax` . Pokud například nastavíte maximální propustnost na 20 000 RU/s, propustnost se bude škálovat mezi 2000 až 20 000 RU/s. Vzhledem k tomu, že škálování je automatické a okamžité, můžete v jakémkoli bodě v čase spotřebovávat až do zřízené `Tmax` bez prodlevy. 

Každou hodinu se vám bude účtovat nejvyšší propustnost, `T` kterou systém během hodiny škáluje.

Vstupním bodem pro maximální propustnost pro automatické škálování `Tmax` začíná 4000 ru/s, který se škáluje mezi 400-4000 ru/s. Můžete nastavit `Tmax` v přírůstcích po 1000 ru/s a hodnotu kdykoli změnit.  

## <a name="enable-autoscale-on-existing-resources"></a>Povolit automatické škálování u stávajících prostředků

Pomocí [Azure Portal](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container) můžete povolit automatické škálování existující databáze nebo kontejneru. Můžete kdykoli přepínat mezi zajištěnou propustností automatického škálování a standardní (ruční). Další informace najdete v této [dokumentaci](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work) . V současné době můžete pro všechna rozhraní API použít Azure Portal jenom k povolení automatického škálování u stávajících prostředků.

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a> Omezení propustnosti a úložiště pro automatické škálování

V případě jakékoli hodnoty `Tmax` může databáze nebo kontejner uchovávat celkem `0.01 * Tmax GB` . Po dosažení této velikosti úložiště se maximální počet RU/s automaticky zvýší v závislosti na nové hodnotě úložiště bez jakéhokoli dopadu na vaši aplikaci. 

Například pokud začnete s maximálním počtem 50 000 RU/s (škálování mezi 5 000 a 50 000 RU/s), můžete uložit až 500 GB dat. Pokud překročíte 500 GB, například na 600 GB úložiště, nový maximální počet RU/s bude 60 000 (škálování mezi 6 000 a 60 000 RU/s).

Pokud s automatickým škálováním využijete propustnost na úrovni databáze, prvních 25 kontejnerů může sdílet maximální počet 4 000 RU/s automatického škálování (škálování mezi 400 a 4 000 RU/s), dokud nepřekročíte 40 GB úložiště. Další informace najdete v této [dokumentaci](autoscale-faq.md#can-i-change-the-max-rus-on-the-database-or-container) .

## <a name="comparison--containers-configured-with-manual-vs-autoscale-throughput"></a>Porovnání – kontejnery nakonfigurované s ručním a nepropustností automatického škálování
Další podrobnosti najdete v této [dokumentaci](how-to-choose-offer.md) k výběru mezi standardní (ruční) a propustností automatického škálování.  

|| Kontejnery se standardní (ruční) propustností  | Kontejnery s propustností automatického škálování |
|---------|---------|---------|
| **Zřízená propustnost (RU/s)** | Ručně zřízené. | Automatické a okamžité škálování na základě vzorců využití úloh. |
| **Frekvence – omezení požadavků/operací (429)**  | Může nastat, pokud spotřeba překročí zřízenou kapacitu. | Neproběhne, pokud použijete RU/s v rámci rozsahu propustnosti automatického škálování, který jste nastavili.    |
| **Plánování kapacity** |  Musíte provést plánování kapacity a zajistit přesnou propustnost, kterou potřebujete. |    Systém se automaticky postará o plánování kapacity a správu kapacity. |
| **Ceny** | Platíte za ručně zřízené RU/s za hodinu pomocí [standardu (ruční) ru/s za hodinovou sazbu](https://azure.microsoft.com/pricing/details/cosmos-db/). | Platíte za hodinu pro nejvyšší úroveň RU/s. systém bude škálovat až do hodiny. <br/><br/> U účtů oblastí s jedním zápisem platíte za použití RU/s za každou hodinu, a to pomocí [automatického ŠKÁLOVÁNÍ ru/s za hodinovou sazbu](https://azure.microsoft.com/pricing/details/cosmos-db/). <br/><br/>U účtů s více oblastmi zápisu se neúčtují žádné další poplatky za automatické škálování. Platíte za propustnost, která se používá každou hodinu, a to za použití stejného [zápisu ru/s ve více oblastech za hodinovou sazbu](https://azure.microsoft.com/pricing/details/cosmos-db/). |
| **Nejvhodnější pro typy úloh** |  Předvídatelné a stabilní úlohy|   Nepředvídatelné a proměnlivé úlohy  |

## <a name="next-steps"></a>Další kroky

* Přečtěte si [Nejčastější dotazy k automatickému škálování](autoscale-faq.md).
* Naučte se [vybírat mezi ruční a propustností automatického škálování](how-to-choose-offer.md).
* Naučte se [zřídit propustnost automatického škálování v databázi nebo kontejneru Azure Cosmos](how-to-provision-autoscale-throughput.md).
* Přečtěte si další informace o [dělení](partitioning-overview.md) Azure Cosmos DB.



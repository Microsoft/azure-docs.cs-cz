---
title: Vysvětlení vašeho vyúčtování Azure Cosmos DB
description: Tento článek vysvětluje, jak vysvětlení vašeho vyúčtování služeb Azure Cosmos DB s příklady.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 5df11eb4e1b759d66200c09cabc8752532d0873c
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54042151"
---
# <a name="understanding-your-azure-cosmos-db-bill"></a>Vysvětlení vašeho vyúčtování Azure Cosmos DB

Jako plně spravované nativně cloudové databázové služby služby Azure Cosmos DB zjednodušuje fakturaci tím jenom za zřízenou propustnost a využité úložiště. Neexistují žádné další licenční poplatky za, hardware, náklady na nástroje, nebo zařízení nákladů ve srovnání s místní nebo hostovaný IaaS alternativy. Pokud zvažujete vícenásobného oblasti funkce služby Azure Cosmos DB, databázová služba poskytuje významné snížení nákladů ve srovnání s existující místní nebo řešení IaaS.

Pomocí služby Azure Cosmos DB se účtují po hodinách podle zřízené propustnosti a spotřebovaného úložiště. Zřízená propustnost je jednotka pro fakturaci 100 RU/s za hodinu, se vám účtovat za $0.008 za hodinu, za předpokladu, že standardní veřejné ceny najdete v článku [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/). Spotřebované úložiště se fakturuje $0,25 na 1 GB úložiště za měsíc, najdete v článku [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/). 

Tento článek používá několik příkladů vám pomůžou pochopit podrobnosti, které se zobrazí v měsíční faktuře. Odlišná čísla v příkladech může být jiný, pokud máte jinou velikost, pokud se span napříč několika oblastmi nebo aplikace spuštěné pro jinou dobu víc než měsíc zřízenou propustnost kontejnery Azure Cosmos.

## <a name="billing-examples"></a>Příklady vyúčtování

### <a name="billing-example---throughput-on-a-container-full-month"></a>Příklad fakturace – propustnosti kontejneru (celý měsíc)

* Předpokládejme, nakonfigurujte propustnost 1 000 RU/s na kontejner a existuje po dobu 24 hodin * 30 dnů pro měsíc = 720 celkový počet hodin.  

* 1 000 RU/s je 10 jednotek služby 100 RU/s za hodinu za každou hodinu existuje kontejnery (to znamená, 1 000 nebo 100 = 10). 

* Vynásobí 10 jednotky za hodinu náklady 0.008 $ (za 100 RU/s za hodinu) = 0,08 USD za hodinu. 

* Vynásobí 0,08 USD za hodinu podle počtu hodin v měsíci se rovná $0,08 * 24 hodin * 30denní = 57.60 $ za měsíc.  

* Celkové měsíční faktuře bude zobrazit 7200 jednotky (100 ru), které stojí 57.60 $.

### <a name="billing-example---throughput-on-a-container-partial-month"></a>Příklad fakturace – propustnosti kontejneru (část měsíce)

* Předpokládejme, že se nám vytvořit kontejner se zřízenou propustností 2 500 RU/s. Kontejner se nachází za 24 hodin za měsíc (například jsme odstranit, je 24 hodin poté, co jsme vytvořili).  

* Uvidíme 600 jednotky na faktuře uvedeno (2 500 RU/s / 100 RU/s/jednotku * 24 hodin). Náklady budou 4.80 $ (600 jednotky * $0.008 za jednotku).

* $4.80 bude celkový účet za měsíc.

### <a name="billing-rate-if-storage-size-changes"></a>Hodinová sazba, pokud se změní velikost úložiště

Kapacita úložiště se účtuje v jednotkách, které odpovídají maximální hodinové velikosti uložených dat (v GB) za měsíc. Když třeba využijete 100 GB úložiště v první půlce měsíce, ale v druhé půlce 50 GB, bude se vám za tento měsíc účtovat ekvivalent 75 GB úložiště.

### <a name="billing-rate-when-container-or-a-set-of-containers-are-active-for-less-than-an-hour"></a>Fakturační sazby, pokud kontejner nebo sadu kontejnerů jsou aktivní kratší dobu než hodinu

Můžete se účtuje paušální sazba za každou hodinu kontejner nebo databáze, bez ohledu na využití nebo pokud kontejner nebo databáze aktivní kratší dobu než hodinu. Například pokud vytvoříte kontejner nebo databázi a 5 minutách ji odstraníte, vaše vyúčtování bude obsahovat jednu hodinu.

### <a name="billing-rate-when-throughput-on-a-container-or-database-scales-updown"></a>Hodinová sazba při škálování propustnosti na kontejner nebo v databázi směrem nahoru nebo dolů

Je-li zvýšit zřízenou propustnost v 9:30 Upgradujete z 400 RU/s na 1 000 RU/s a pak snížit zřízenou propustnost v 10:45 Downgradujete zpátky na 400 RU/s, vám bude účtovat dvě hodiny kolekce 1 000 RU/s. 

Pokud zvýšíte zřízená propustnost pro kontejner nebo sadu kontejnerů v 9:30 Upgradujete z 100 tisíc RU/s na 200 tisíc RU/s a pak nižší zřízenou propustnost v 10:45 Downgradujete zpátky na 100 tisíc RU/s, vám bude účtovat dvě hodiny kolekce 200 tisíc RU/s.

### <a name="billing-example-multiple-containers-each-with-dedicated-provisioned-throughput-mode"></a>Příklad fakturace: více kontejnerů, každý s režimem vyhrazené zřízená propustnost

* Pokud vytvoříte účet Azure Cosmos v oblasti východní USA 2 se dvěma kontejnery se zřízenou propustností 500 RU/s a 700 RU/s, v uvedeném pořadí, by měl vaše celková zřízená propustnost bude 1 200 RU/s.  

* Účtovalo by se vám 1 200/100 * $0.008 = 0,096 USD / hod. 

* Pokud potřebujete změnit propustnost, zvýšili jste jste kapacitu jednotlivých kontejnerů o 500 RU/s také vytvořili nový neomezený kontejner s 20 000 RU/s, vaše celková zřízená kapacita bude 22 200 RU/s (1 000 RU/s + 1 200 RU/s + 20, 000RU za sekundu).  

* Vaše faktura se pak změní na: $0.008 x 222 = $1.776 za hodinu. 

* V daném měsíci 720 hodin (24 hodin * 30denní), pokud 500 hodin zřízena propustnost 1 200 RU/s a pro zbývající hodiny 220 zřízená propustnost 22 200 RU/s, ukazuje, váš měsíční účet: 500 x 0,096 USD za hodinu + 220 x 1.776 $/ hodinu = $438.72 / měsíc.

![Vyhrazená propustnost vyúčtování příklad](./media/understand-your-bill/bill-example1.png)

### <a name="billing-example-containers-with-shared-throughput-mode"></a>Příklad fakturace: kontejnery s režimem sdílený propustnost

* Pokud vytvoříte účet Azure Cosmos v oblasti východní USA 2 pomocí dvou databází Azure Cosmos (se sadou kontejnery propustnosti na úrovni databáze, sdílení) se zřízenou propustností 50 tis. RU/s a 70 tis. RU/s, respektive, budete mít celkem zřízené propustnost 120 tis. RU/s.  

* Účtovalo by se vám 1200 x $0.008 = $9.60 za hodinu. 

* Pokud potřebujete změnit propustnost a zvýšit každý databáze zřízená propustnost 10 tis. RU/s pro každou databázi a přidat nový kontejner první databáze s režimem Vyhrazená propustnost 15 tis. RU/s k vaší databázi sdílené propustnost vaší Celková zřízená kapacita bude 155 tis. RU/s (60 tis. RU/s + 80 tis. RU/s + 15 tis. RU/s).  

* Vaše faktura se pak změní na: 1,550 * $0.008 = $12.40 za hodinu.  

* 720 hodin za měsíc Pokud 300 hodin zřízená propustnost bylo 120 tis. RU/s a po dobu zbývajících hodin 420 zřízená propustnost 155 tis. RU/s, vaše měsíční faktuře bude: 300 × 9.60 $/ hodina + 420 x 12.40 $/ hod. = 2,880 $ + $5,208 = $8,088 / měsíc. 

![Sdílené příklad vyúčtování propustnost](./media/understand-your-bill/bill-example2.png)

## <a name="billing-examples-with-geo-replication-and-multi-master"></a>Příklady vyúčtování s geografickou replikaci a více hlavních databází  

Je můžete přidávat nebo odebírat oblastí Azure kdekoli ve světě ke svému účtu databáze Azure Cosmos DB v každém okamžiku. Ve všech oblastech Azure, které jsou přidružené k účtu databáze Azure Cosmos se vyhradí propustnost, kterou jste nakonfigurovali pro různé databáze Azure Cosmos DB a kontejnery. Pokud součet zřízená propustnost (RU/s) nakonfigurované ve všech databázích a kontejnerů ve vaší databázi Azure Cosmos (zřízený za hodinu) účet je T a počtu oblastí Azure spojené s vaším účtem databáze je N, pak součet zřízená propustnost pro příslušné hodiny pro vaše Azure Cosmos je rovna T x N RU/s a (b), které jsou nakonfigurované všechny oblasti, která dokáže zpracovávat zápisy databázový účet, (a) nakonfigurovaný s oblastí zápisu jednoho je rovna T x (N + 1) RU/s , v uvedeném pořadí. Zřízená propustnost (oblasti pro zápis jeden) stojí 0.008 $za hodinu za 100 RU/s a zřízená propustnost s využitím více oblastí zapisovatelný (multimasterovou config) stojí $0,016 / za hodinu za 100 RU/s (viz [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/)). Ať už jeho jeden zápis oblasti nebo více oblastí zápisu, služby Azure Cosmos DB umožňuje číst data z libovolné oblasti.

### <a name="billing-example-multi-region-azure-cosmos-account-single-region-writes"></a>Příklad fakturace: účet služby Azure Cosmos ve více oblastech, zapíše jedné oblasti

Předpokládejme, že máte kontejner služby Azure Cosmos v oblasti západní USA. Kontejneru se vytvoří s propustností 10 tisíc RU/s a 1 TB dat uložit tento měsíc. Předpokládejme, že přidáte tři oblasti (východní USA, Severní Evropa a východní Asie) ke svému účtu Azure Cosmos, každá má stejné úložiště a propustnost. Vaše celkové měsíční faktuře bude možné (za předpokladu, že 30 dní v měsíci). Váš účet by měl vypadat takto: 

|**Položka** |**Využití (měsíc)** |**Frekvence** |**Měsíční náklady** |
|---------|---------|---------|-------|
|Faktura za propustnost za kontejner v oblasti Západní USA      | 10 tisíc RU/s * 24 * 30    |0.008 za 100 RU/s za hodinu   |$576|
|Faktura za propustnost za 3 další oblasti: Východní USA, Severní Evropa a Východní Asie       | 3 * 10 tisíc RU/s * 24 * 30    |0.008 za 100 RU/s za hodinu  |$1,728|
|Faktura za úložiště za kontejner v oblasti Západní USA      | 250 GB    |$ 0,25/GB  |$62.50|
|Faktura za úložiště za 3 další oblasti: Východní USA, Severní Evropa a Východní Asie      | 3 * 250 GB    |$ 0,25/GB  |$187.50|
|**Celkem**     |     |  |**$2,554**|

*Předpokládejme také, že je výchozí přenos 100 GB dat každý měsíc z kontejneru v oblasti západní USA k replikaci dat do USA – východ, Severní Evropa a východní Asie. Bude se vám účtovat pro výchozí přenos dat podle sazby za datové přenosy.*

### <a name="billing-example-multi-region-azure-cosmos-account-multi-region-writes"></a>Příklad fakturace: účet služby Azure Cosmos ve více oblastech, zapíše více oblastí

Předpokládejme, že vytvoříte kontejner služby Azure Cosmos v oblasti západní USA. Kontejneru se vytvoří s propustností 10 tisíc RU/s a 1 TB dat uložit tento měsíc. Předpokládejme, že přidáte tři oblasti (východní USA, Severní Evropa a východní Asie), každá má stejné úložiště a propustnost a má možnost zapisovat do kontejnerů ve všech oblastech, které jsou spojené s vaším účtem Azure Cosmos. Celkové měsíční faktuře bude možné (za předpokladu, že 30 dní v měsíci) následujícím způsobem:

|**Položka** |**Využití (měsíc)**|**Frekvence** |**Měsíční náklady** |
|---------|---------|---------|-------|
|Faktura za propustnost za kontejner v oblasti západní USA (všechny oblasti jsou zapisovatelné)       | 10 tisíc RU/s * 24 * 30    |0,016 $ za 100 RU/s za hodinu    |$1,152 |
|Faktura za propustnost za 3 Další oblasti: východní USA, Severní Evropa a východní Asie (všechny oblasti jsou zapisovatelné)        | (3 + 1) * 10 tis. RU/s * 24 * 30    |0,016 $ za 100 RU/s za hodinu   |$4,608 |
|Faktura za úložiště za kontejner v oblasti Západní USA      | 250 GB    |$ 0,25/GB  |$62.50|
|Faktura za úložiště za 3 další oblasti: Východní USA, Severní Evropa a Východní Asie      | 3 * 250 GB    |$ 0,25/GB  |$187.50|
|**Celkem**     |     |  |**$6,010**|

*Předpokládejme také, že je výchozí přenos 100 GB dat každý měsíc z kontejneru v oblasti západní USA k replikaci dat do USA – východ, Severní Evropa a východní Asie. Bude se vám účtovat pro výchozí přenos dat podle sazby za datové přenosy.*

### <a name="billing-example-azure-cosmos-account-with-multi-master-database-level-throughput-including-dedicated-throughput-mode-for-some-containers"></a>Příklad fakturace: Účet Azure Cosmos s několika hlavními uzly, včetně databáze úrovně propustnosti vyhrazené režimu propustnosti pro některé kontejnery

Zvažte následující příklad, kde máme účet více oblastí Azure Cosmos kde všechny oblasti jsou zapisovatelné (multimasterovou config). Pro zjednodušení budeme předpokládat velikost úložiště zůstává konstantní a nelze změnit a ho tady vynecháte jednodušší zajistit v příkladu. Zřízená propustnost během měsíce různorodé následujícím způsobem (předpokládá 30 dnů nebo hodin 720): 

[0-100 hodin]:  

* Jsme vytvořili účet Azure Cosmos tři oblasti (západní USA, východní USA, Severní Evropa), kde můžou zapisovat všechny oblasti 

* Jsme vytvořili databázi (D1) s sdílené propustností 10 tisíc RU/s 

* Jsme vytvořili databázi (D2) s propustností sdílené 30tis. RU/s a  

* Jsme vytvořili kontejner (C1) s vyhrazenou propustností 20 tisíc RU/s 

[101 – 200 h]:  

* Škálovat nahoru databáze (D1) do 50 tis. RU/s 

* Škálovat nahoru databáze (D2) na 70 tis. RU/s  

* Doporučujeme odstranit kontejner (C1)  

[201-300 hodin]:  

* Jsme vytvořili kontejner (C1), znovu s vyhrazenou propustností 20 tis. RU/s 

[301 400 hodin]:  

* Jednu z oblastí jsme odebrali z účtu Azure Cosmos (# zapisovatelné oblastí je nyní 2) 

* Škálovat dolů databáze (D1) na 10 tis. RU/s 

* Škálovat nahoru databáze (D2) do 80 tis. RU/s  

* Doporučujeme odstranit kontejner (C1) 

[hodin 401-500]:  

* Škálovat dolů databáze (D2) na 10 tis. RU/s  

* Jsme vytvořili kontejner (C1), znovu s vyhrazenou propustností 20 tis. RU/s 

[501-700 hodin]:  

* Škálovat nahoru databáze (D1) do 20 tisíc RU/s  

* Škálovat nahoru databáze (D2) pro 100 tisíc RU/s  

* Doporučujeme odstranit kontejner (C1)  

[701 720 h]:  

* Škálovat dolů databáze (D2) do 50 tis. RU/s  

Vizuální změny v vaše celková zřízená propustnost během 720 hodin za měsíc můžete vidět na následujícím obrázku: 

![Příklad reálném životě](./media/understand-your-bill/bill-example3.png)

Celkové měsíční faktuře bude možné (za předpokladu, že 30 dnů nebo 720 hodin za měsíc) se vypočítá následovně:

|**hodiny**  |**RU/s** |**Položka** |**Využití (hodiny)** |**Náklady** |
|---------|---------|---------|-------|-------|
|[0-100] |D1:10K <br/>D2:30K <br/>C1:20K |Faktura za propustnost za kontejner v oblasti západní USA (všechny oblasti jsou zapisovatelné)  | `D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 30 K RU/sec/100 * $0.016 * 100 hours = $480` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |960 $  |
| | |Faktura za propustnost za 2 dalších oblastech: Východní USA, Severní Evropa (všechny oblasti jsou zapisovatelné)  |`(2 + 1) * (60 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2,880  |
|[101 – 200] |D1:50K <br/>D2:70K <br/>C1:-- |Faktura za propustnost za kontejner v oblasti západní USA (všechny oblasti jsou zapisovatelné)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` |$1920  |
| | |Faktura za propustnost za 2 dalších oblastech: Východní USA, Severní Evropa (všechny oblasti jsou zapisovatelné)  |`(2 + 1) * (120 K RU/sec /100 * $0.016) * 100 hours = $5,760`  |$5,760  |
|[201-300]  |D1:50K <br/>D2:70K <br/>C1:20K |Faktura za propustnost za kontejner v oblasti západní USA (všechny oblasti jsou zapisovatelné)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` <br/>"C1: 20 tisíc RU/sec/100 * $0,016 * 100 hodin = $320 |2,240 $"  |
| | |Faktura za propustnost za 2 dalších oblastech: Východní USA, Severní Evropa (všechny oblasti jsou zapisovatelné)  |`(2 + 1) * (140 K RU/sec /100 * $0.016-) * 100 hours = $6,720` |$6,720 |
|[301 400] |D1:10K <br/>D2:80K <br/>C1:-- |Faktura za propustnost za kontejner v oblasti západní USA (všechny oblasti jsou zapisovatelné)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 80 K RU/sec/100 * $0.016 * 100 hours = $1,280`  |$ 1 440   |
| | |Faktura za propustnost za 2 dalších oblastech: Východní USA, Severní Evropa (všechny oblasti jsou zapisovatelné)  |`(1 + 1) * (90 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2,880  |
|[401-500] |D1:10K <br>D2:10K <br>C1:20K |Faktura za propustnost za kontejner v oblasti západní USA (všechny oblasti jsou zapisovatelné)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`D2: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |640 $  |
| | |Faktura za propustnost za 2 dalších oblastech: Východní USA, Severní Evropa (všechny oblasti jsou zapisovatelné)  |`(1 + 1) * (40 K RU/sec /100 * $0.016) * 100 hours = $1,280`  |$1,280  |
|[501-700] |D1:20K <br>D2:100K <br>C1:-- |Faktura za propustnost za kontejner v oblasti západní USA (všechny oblasti jsou zapisovatelné)  |`D1: 20 K RU/sec/100 * $0.016 * 200 hours = $640` <br>`D2: 100 K RU/sec/100 * $0.016 * 200 hours = $3,200` |$3,840  |
| | |Faktura za propustnost za 2 dalších oblastech: Východní USA, Severní Evropa (všechny oblasti jsou zapisovatelné)  |`(1 + 1) * (120 K RU/sec /100 * $0.016) * 200 hours = $1,280`  |$7,680  |
|[701 – 720] |D1:20K <br/>D2:50K <br/>C1:-- |Faktura za propustnost za kontejner v oblasti západní USA (všechny oblasti jsou zapisovatelné)  |`D1: 20 K RU/sec/100 *$0.016 * 20 hours = $64` <br/>`D2: 50 K RU/sec/100 *$0.016 * 20 hours = $160` |$224  |
| | |Faktura za propustnost za 2 dalších oblastech: Východní USA, Severní Evropa (všechny oblasti jsou zapisovatelné)  |`(1 + 1) * (70 K RU/sec /100 * $0.016) * 20 hours = $448`  |$224  |
|| |**Celkové měsíční náklady**  | |**$38,688**   |

## <a name="proactively-estimating-your-monthly-bill"></a>Proaktivně odhadnout svoje měsíční náklady  

Uvažujme jiný příklad, ve které chcete proaktivně přibližnou výši dřív, než v měsíci. Chcete-li odhadnout vyúčtování následujícím způsobem:

|**Náklady na úložiště** | |
|----|----|
|Záznam Průměrná velikost (KB) |1 |
|Počet záznamů  |100 000 000  |
|Celková velikost úložiště (GB)  |100 |
|Měsíční náklady za GB  |$0,25  |
|Očekávané měsíční náklady na úložiště   |$25,00  |

<br>

|**Náklady na propustnost** | | | |
|----|----|----|----|
|Typ operace| Počet požadavků za sekundu| Střední RU nebo žádosti| RU potřeby|
|Zápis| 100 | 5 | 500|
|Čtení| 400| 1| 400|

Celkový počet RU za sekundu: 500 + 400 = 900 hodinové náklady: 900/100 * $0.008 = $0.072 očekávané měsíční poplatek za propustnost (za předpokladu, že nabídka 31 dní): $0.072 * 24 * 31 = $53.57

**Celkové měsíční náklady**

Celkové měsíční náklady na = měsíční poplatek za úložiště a měsíční poplatek za propustnost celkové měsíční náklady = 25,00 $ + $53.57 = $78.57

*Ceny se mohou lišit podle oblasti. Aktuální ceny, najdete v článku [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).*

## <a name="billing-with-azure-cosmos-db-reserved-capacity"></a>Fakturace pomocí služby Azure Cosmos DB rezervované kapacity

Azure Cosmos DB vyhrazené kapacity můžete zakoupit zřízená propustnost předem (rezervované kapacity nebo rezervace), který lze použít pro všechny databáze Azure Cosmos DB a kontejnery (pro rozhraní API nebo datový model) napříč všemi oblastmi Azure. Vzhledem k tomu, že zřízená propustnost ceny se liší podle oblasti, pomáhá si můžete představit jako peněžního kreditu, který jste si koupili slevou, která může být odebírány z zřízená propustnost v příslušných ceny v jednotlivých oblastech záložní kapacitu. Řekněme například, že máte Azure Cosmos účtu s jedním kontejnerem zajišťována službou 50 tis. RU/s a globálně replikovat dvě oblasti: USA – východ a Japonsko – východ. Pokud zvolíte možnost s průběžnými platbami, zaplatíte:  

* v oblasti východní USA: pro 50 tis. RU/s rychlostí 0.008 $ za 100 RU/s v dané oblasti 

* v oblasti Japonsko – východ: pro 50 tis. RU/s rychlostí 0.009 $ za 100 RU/s v dané oblasti

Vaše Celková fakturovaná částka (bez rezervované kapacity) by (předpokládá 30 dnů nebo hodin 720): 

|**Oblast**| **Hodinovou cenu za 100 RU/s**|**Jednotky (RU/s)**|**Vyúčtované množství (hodiny)**| **Vyúčtované množství (měsíčně)**|
|----|----|----|----|----|
|USA – východ|$0.008 |50 TISÍC|4 $|$2,880 |
|Japonsko – východ|$0.009 |50 TISÍC| $4.50 |$3,240 |
|Celkem|||$8.50|$6,120 |

Pojďme se podívat, že jste zakoupili rezervované kapacity místo. Můžete si koupit rezervované kapacity pro 100 tisíc RU/s za cenu 56,064 po dobu jednoho roku (na 20 % sleva) nebo 6.40 $ za hodinu. Podívejte se na cenách záložní kapacitu [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/)).  

* Náklady na propustnost (průběžné platby): 100 000 RU/sec/100 * $0.008/hodina * 8760 hodin za rok = $70,080 

* Náklady na propustnost (s rezervované kapacity) $70,080 slevou na 20 % = $56,064 

Efektivně pořízeného je kredit ve výši na 8 USD za hodinu za 100 tisíc RU/s využitím ceníku v oblasti východní USA, za cenu 6.40 $ za hodinu. Pak můžete nakreslit z tuto rezervaci předplacené propustnost podle počtu hodin za zřízenou propustnost kapacitu v libovolné globální oblasti Azure v příslušné místní ceny seznam nastavení pro vaše předplatné. V tomto příkladu, kde zřídíte 50 tis. RU/s každou v USA – východ a Japonsko – východ, budete moct nakreslit 8.00 $ za zřízenou propustnost za hodinu a budou účtovat Nadlimitní 0,50 USD za hodinu (nebo $360/ měsíc). 

|**Oblast**| **Hodinovou cenu za 100 RU/s**|**Jednotky (RU/s)**| **Vyúčtované množství (hodiny)**| **Vyúčtované množství (měsíčně)**|
|----|----|----|----|----|
|USA – východ|$0.008 |50 TISÍC|4 $|$2,880 |
|Japonsko – východ|$0.009 |50 TISÍC| $4.50 |$3,240 |
|||Průběžné platby|$8.50|6120 $|
|Zakoupená záložní kapacita|0.0064 (20 % sleva) |100 RU/s nebo předem zakoupené kapacitě na 8 USD |-na 8 USD|-5,760 $ |
|Částka před slevou|||0,50 USD |360 $ |

## <a name="next-steps"></a>Další kroky

Potom můžete přejít k další informace o optimalizaci nákladů ve službě Azure Cosmos DB najdete v následujících článcích:

* Další informace o [jak Cosmos DB cenový model je nákladově efektivní pro zákazníky](total-cost-ownership.md)
* Další informace o [optimalizace pro vývoj a testování](optimize-dev-test.md)
* Další informace o [optimalizace propustnosti náklady](optimize-cost-throughput.md)
* Další informace o [optimalizovat náklady na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizovat náklady na operace čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizovat náklady na dotazy](optimize-cost-queries.md)
* Další informace o [optimalizovat náklady na účty ve více oblastech Azure Cosmos](optimize-cost-regions.md)

---
title: Princip Azure Cosmos DB faktury
description: V tomto článku se dozvíte, jak porozumět vašemu Azure Cosmos DB faktury s příklady.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.reviewer: sngun
ms.openlocfilehash: 7b26c88bcbd303f00dc6163dd0323b07cbb83dcf
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93073821"
---
# <a name="understand-your-azure-cosmos-db-bill"></a>Vysvětlení informací na faktuře za službu Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Jako plně spravovaná cloudová databázová služba Azure Cosmos DB zjednodušuje fakturaci tím, že se účtuje jenom za vaše databázové operace a využité úložiště. V porovnání s místními nebo IaaS alternativami nejsou žádné další licenční poplatky, náklady na nástroj ani náklady na zařízení. Když posuzujete možnosti více oblastí Azure Cosmos DB, databázová služba poskytuje výrazné snížení nákladů ve srovnání se stávajícími místními nebo IaaS řešeními.

- **Databázové operace** : způsob, jakým se vám účtují operace s databází, závisí na typu účtu Azure Cosmos, který používáte.

  - **Zajištěná propustnost** : za každou hodinu se účtuje po hodinách maximální zajištěné propustnosti v přírůstcích po 100 ru/s.
  - Bez **serveru** : účtuje se každou hodinu za celkovou částku jednotek žádostí spotřebovaných operacemi vaší databáze.

- **Úložiště** : za určitou hodinu se účtuje paušální sazba za celkovou velikost úložiště (v GB) spotřebované Vašimi daty a indexy.

Nejnovější informace o cenách najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/) .

Tento článek vám na několika příkladech pomůže porozumět podrobným informacím na měsíční faktuře. Čísla uvedená v příkladech se můžou lišit, pokud vaše kontejnery Azure Cosmos mají jinou zřízenou propustnost, pokud pokrývají více oblastí nebo pokud jsou spuštěné po jinou dobu v měsíci. Všechny příklady v tomto článku vypočítávají fakturaci na základě informací o cenách, které jsou uvedené na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).

> [!NOTE]
> Fakturuje se za každou část hodinové hodiny, ne pro dobu 60 minut. Všechny příklady zobrazené v tomto dokumentu jsou založené na ceně za účet Azure Cosmos nasazený v oblasti mimo státní správu v USA. Ceny a kalkulace se liší v závislosti na oblasti, kterou používáte, na [stránce s cenami Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) najdete nejnovější informace o cenách.

## <a name="billing-examples"></a>Příklady fakturace

### <a name="billing-example---provisioned-throughput-on-a-container-full-month"></a>Příklad fakturace – zřízená propustnost na kontejneru (celý měsíc)

* Řekněme, že nakonfigurujete propustnost 1 000 RU/s na kontejneru a existuje 24 hodin × 30 dní pro měsíc = celkem 720 hodin.  

* 1 000 RU/s je 10 jednotek 100 RU/s za hodinu pro každou hodinu existence kontejnerů (tj. 1000/100 = 10). 

* Vynásobení 10 jednotek za hodinu náklady na $0,008 (za 100 RU/s za hodinu) = $0,08 za hodinu. 

* Vynásobení $0,08 za hodinu číslem hodiny v měsíci se rovná $0,08 × 24 hodin × 30 dnů = $57,60 po měsíci.  

* Celková měsíční vyúčtování bude zobrazovat 7 200 jednotek (z 100 ru), což bude finančně $57,60.

### <a name="billing-example---provisioned-throughput-on-a-container-partial-month"></a>Příklad fakturace – zřízená propustnost na kontejneru (částečný měsíc)

* Řekněme, že vytvoříme kontejner s zřízenou propustností 2 500 RU/s. Kontejner je po dobu 24 hodin za měsíc (například odstraníme 24 hodin po jeho vytvoření).  

* Pak uvidíme 600 jednotek na faktuře (2 500 RU/s/100 RU/s/jednotku × 24 hodin). Náklady budou $4,80 (600 jednotek × $0.008/Unit).

* Celková faktura za měsíc bude $4,80.

### <a name="billing-example---serverless-container"></a>Příklad fakturace – kontejner bez serveru

* Řekněme, že vytvoříme kontejner bez serveru. 

* Za měsíc vydáváme požadavky na databáze, které spotřebovávají celkem 500 000 jednotek žádostí. Náklady budou $0,125 (500 000 × 0,25/milion).

* Celková faktura za měsíc bude $0,125.

### <a name="billing-rate-if-storage-size-changes"></a>Fakturační sazba v případě změny velikosti úložiště

Kapacita úložiště se účtuje v jednotkách, které mají maximální hodinové množství uložených dat (v GB) za měsíční období. Pokud jste například využili 100 GB úložiště na polovinu měsíce a 50 GB v druhé polovině měsíce, bude se vám za tento měsíc účtovat 75 ekvivalent GB úložiště.

### <a name="billing-rate-when-container-or-a-set-of-containers-are-active-for-less-than-an-hour"></a>Fakturační sazba, pokud je kontejner nebo sada kontejnerů aktivní kratší dobu než 1 hodina

Za každou hodinu existence kontejneru nebo databáze se účtuje paušální sazba, bez ohledu na využití nebo na to, jestli je kontejner nebo databáze aktivní kratší dobu než hodinu. Pokud třeba vytvoříte kontejner nebo databázi a za 5 minut ji odstraníte, bude vaše faktura obsahovat jednu hodinu.

### <a name="billing-rate-when-provisioned-throughput-on-a-container-or-database-scales-updown"></a>Fakturační sazba, při které se zřízená propustnost na kontejneru nebo databáze škáluje nahoru/dolů

Pokud zvýšíte zřízenou propustnost v 9:30.400 RU/s na 1 000 RU/s a pak nižší zřízenou propustnost při 10:45 až do 400 RU/s, bude se vám účtovat dvě hodiny od 1 000 RU/s. 

Pokud zvýšíte zřízenou propustnost pro kontejner nebo sadu kontejnerů v 9:30 od 100-K RU/sec po 200-K RU/s a pak nižší zřízenou propustnost při 10:45 až do 100-K RU/s, bude se vám účtovat dvě hodiny od 200 do RU/s.

### <a name="billing-example-multiple-containers-each-with-dedicated-provisioned-throughput-mode"></a>Příklad fakturace: několik kontejnerů, z nichž každý má vyhrazený zřízený režim propustnosti.

* Pokud vytvoříte účet Azure Cosmos v Východní USA 2 se dvěma kontejnery s zřízenou propustností 500 RU/s a 700 RU/s, měli byste mít celkovou zřízenou propustnost 1 200 RU/s.  

* Bude se vám účtovat 1200/100 × $0,008 = $0.096/hod. 

* Pokud se vaše požadavky na vaši propustnost změnily a zvýšili jste kapacitu kontejneru o 500 RU/s a zároveň vytváříte nový neomezený kontejner s 20 000 RU/s, bude celková zřízená kapacita 22 200 RU/s (1 000 RU/s + 1 200 RU/s + 20, navíc jste vytvořili/s).  

* Vaše vyúčtování se pak změní na: $0,008 × 222 = $1.776/hod. 

* Za měsíc 720 hodin (24 hodin × 30 dní), pokud po dobu 500 hodin zřídila 1 200 propustnost RU/s a 220 zbývající hodin zajištěné propustnosti byl 22 200 RU/s, vaše měsíční faktura vám ukáže: 500 x $0.096/Hour + 220 × $1.776/Hour = $438.72/měsíc.

:::image type="content" source="./media/understand-your-bill/bill-example1.png" alt-text="Příklad vyhrazené faktury za propustnost":::

### <a name="billing-example-containers-with-shared-provisioned-throughput-mode"></a>Příklad fakturace: kontejnery se sdíleným (zřízeným) režimem propustnosti

* Pokud vytvoříte účet Azure Cosmos ve službě Východní USA 2 se dvěma databázemi Azure Cosmos (se sadou kontejnerů, které sdílí propustnost na úrovni databáze) se stanovenou propustností 50-K RU/s a 70-K RU/s, v uvedeném pořadí byste měli celkovou zřízenou propustnost 120 KB/s.  

* Bude se vám účtovat 1200 × $0,008 = $9.60/hod. 

* Pokud se vaše požadavky na vaši propustnost změnily a zvýšili jste každou zajištěnou propustnost každé databáze o 10 000 RU/s pro každou databázi, a přidáte nový kontejner do první databáze s vyhrazeným režimem propustnosti 15-K RU/s do vaší sdílené databáze propustnosti, vaše celková zřízená kapacita bude 155-K RU/s (60 K RU/s + 80 K RU/s + 15 K RU/s).  

* Vaše vyúčtování se pak změní na: 1 550 × $0,008 = $12.40/hod.  

* Za měsíc 720 hodin, pokud po dobu 300 hodin zřídila 120 propustnost-K RU/s a 420 zbývající hodin zajištěné propustnosti byly 155-K RU/s, bude vaše měsíční vyúčtování obsahovat: 300 x $9.60/Hour + 420 × $12.40/Hour = $2 880 + $5 208 = $8088/měsíc. 

:::image type="content" source="./media/understand-your-bill/bill-example2.png" alt-text="Příklad vyhrazené faktury za propustnost":::

## <a name="billing-examples-with-geo-replication-and-multi-region-writes"></a>Příklady účtování s použitím geografických replikací a zápisů ve více oblastech  

Oblasti Azure kdekoli na světě můžete kdykoli přidat nebo odebrat do svého účtu databáze Azure Cosmos. Propustnost, kterou jste nakonfigurovali pro různé databáze a kontejnery Azure Cosmos, se vyhrazuje v každé oblasti Azure přidružené k vašemu účtu databáze Azure Cosmos. Pokud je součet zřízené propustnosti (RU/s) nakonfigurovaný napříč všemi databázemi a kontejnery v rámci účtu databáze Azure Cosmos (zřízený za hodinu) a počet oblastí Azure přidružených k vašemu databázovému účtu je N, celková zajištěná propustnost pro danou hodinu pro účet databáze Azure Cosmos (a) nakonfigurovaná s jednou oblastí zápisu se rovná T x N RU/s a (b) nakonfigurovaným se všemi oblastmi, které jsou schopné zpracovávat zápisy, se rovná T x (N + 1) RU/s. Zajištěná propustnost (jedna oblast zápisu) cost $0.008/Hour za 100 RU/s a zřízená propustnost s několika oblastmi s možností zápisu (konfigurace zápisů s více oblastmi) náklady $0,016/za hodinu za 100 RU/s (viz [stránku s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/)). Bez ohledu na to, jestli má jedna oblast pro zápis nebo do více oblastí pro zápis, Azure Cosmos DB umožňuje číst data z libovolné oblasti.

### <a name="billing-example-multi-region-azure-cosmos-account-single-region-writes"></a>Příklad fakturace: účet Azure Cosmos s více oblastmi, zápisy v jedné oblasti

Předpokládejme, že máte v Západní USA kontejner Azure Cosmos. Kontejner se vytvoří s propustností 10 000 RU/s a do tohoto měsíce uložíte 1 TB dat. Řekněme, že do svého účtu Azure Cosmos přidáte tři oblasti (Východní USA, Severní Evropa a Východní Asie), každé se stejným úložištěm a propustností. Vaše celková měsíční faktura bude (za za měsíc 30 dní). Vaše vyúčtování bude vypadat takto: 

|**Položka** |**Využití (měsíc)** |**Rychlost** |**Měsíční náklady** |
|---------|---------|---------|-------|
|Faktura za propustnost pro kontejner v Západní USA      | 10 000 RU/s * 24 * 30    |$0,008 za 100 RU/s za hodinu   |$576|
|Faktura za propustnost pro 3 další oblasti – Východní USA, Severní Evropa a Východní Asie       | 3 × 10 000 RU/s * 24 * 30    |$0,008 za 100 RU/s za hodinu  |$1 728|
|Faktura za úložiště pro kontejner v Západní USA      | 250 GB    |0,25/GB  |$62,50|
|Faktura za úložiště pro 3 další oblasti – Východní USA, Severní Evropa a Východní Asie      | 3 × 250 GB    |0,25/GB  |$187,50|
|**Celkem**     |     |  |**$2 554**|

*Také předpokládáme, že každý měsíc 100 GB dat z kontejneru v Západní USA pro replikaci dat do Východní USA, Severní Evropa a Východní Asie. Účtují se za výstup podle sazeb za přenos dat.*

### <a name="billing-example-multi-region-azure-cosmos-account-multi-region-writes"></a>Příklad fakturace: účet Azure Cosmos ve více oblastech, zápisy ve více oblastech

Řekněme, že v Západní USA vytvoříte kontejner Azure Cosmos. Kontejner se vytvoří s propustností 10 000 RU/s a do tohoto měsíce uložíte 1 TB dat. Předpokládejme, že přidáte tři oblasti (Východní USA, Severní Evropa a Východní Asie), každou se stejným úložištěm a propustností a chcete mít možnost zapisovat do kontejnerů ve všech oblastech přidružených k vašemu účtu Azure Cosmos. Vaše celková měsíční faktura bude (za 30 dní za měsíc), a to takto:

|**Položka** |**Využití (měsíc)**|**Rychlost** |**Měsíční náklady** |
|---------|---------|---------|-------|
|Faktura za propustnost pro kontejner v Západní USA (všechny oblasti jsou zapisovatelné)       | 10 000 RU/s * 24 * 30    |$0,016 za 100 RU/s za hodinu    |$1 152 |
|Faktura za propustnost pro 3 další oblasti – Východní USA, Severní Evropa a Východní Asie (všechny oblasti jsou zapisovatelné)        | (3 + 1) × 10 000 RU/s * 24 * 30    |$0,016 za 100 RU/s za hodinu   |$4 608 |
|Faktura za úložiště pro kontejner v Západní USA      | 250 GB    |0,25/GB  |$62,50|
|Faktura za úložiště pro 3 další oblasti – Východní USA, Severní Evropa a Východní Asie      | 3 × 250 GB    |0,25/GB  |$187,50|
|**Celkem**     |     |  |**$6 010**|

*Také předpokládáme, že každý měsíc 100 GB dat z kontejneru v Západní USA pro replikaci dat do Východní USA, Severní Evropa a Východní Asie. Účtují se za výstup podle sazeb za přenos dat.*

### <a name="billing-example-azure-cosmos-account-with-multi-region-writes-database-level-throughput-including-dedicated-throughput-mode-for-some-containers"></a>Příklad fakturace: účet Azure Cosmos s zápisy ve více oblastech, propustnost na úrovni databáze včetně vyhrazeného režimu propustnosti pro některé kontejnery

Pojďme vzít v úvahu následující příklad, kde máme účet Azure Cosmos s více oblastmi, kde jsou všechny oblasti zapisovatelné (konfigurace více oblastí zápisu). V zájmu jednoduchosti předpokládáme, že velikost úložiště zůstane konstantní a nemění se a vynechá se tady, aby byl příklad jednodušší. Zřízená propustnost v měsíci je rozlišena takto (za 30 dní nebo 720 hodin): 

[0-100 hodin]:  

* Vytvořili jsme tři oblasti účet Azure Cosmos (Západní USA, Východní USA, Severní Evropa), kde se dají zapisovat do všech oblastí. 

* Vytvořili jsme databázi (D1) se sdílenou propustností 10 000 RU/s. 

* Vytvořili jsme databázi (D2) se sdílenou propustností 30-K RU/s.  

* Vytvořili jsme kontejner (C1) s vyhrazenou propustností 20 K RU/s. 

[101-200 hodin]:  

* Provedli jsme škálování databáze (D1) na 50 K RU/s. 

* Škálovat jsme databázi (D2) na 70 K RU/s.  

* Odstranili jsme kontejner (C1).  

[201-300 hodin]:  

* Vytvořili jsme kontejner (C1) znovu s vyhrazenou propustností 20 K RU/s. 

[301-400 hodin]:  

* Odebrali jsme jednu z oblastí z účtu Azure Cosmos (počet oblastí s možností zápisu je teď 2). 

* Škálovat jsme dolů databázi (D1) na 10 000 RU/s. 

* Škálovat jsme databázi (D2) na 80 K RU/s.  

* Znovu jsme odstranili kontejner (C1). 

[401-500 hodin]:  

* Velikost databáze (D2) se škáluje na 10 000 RU/s.  

* Vytvořili jsme kontejner (C1) znovu s vyhrazenou propustností 20 K RU/s. 

[501-700 hodin]:  

* Škálovat jsme databázi (D1) na 20 K RU/s.  

* Škálovat jsme databázi (D2) na 100 K RU/s.  

* Znovu jsme odstranili kontejner (C1).  

[701-720 hodin]:  

* Škálovaná databáze (D2) se škáluje na 50 K RU/s.  

Vizuálně na následujícím obrázku vidíte změny v celkové zřízené propustnosti během 720 hodin v měsíci: 

:::image type="content" source="./media/understand-your-bill/bill-example3.png" alt-text="Příklad vyhrazené faktury za propustnost":::

Celková fakturovaná částka bude (za za 30 dní/720 hodin za měsíc) se vypočítává takto:

|**Hodiny**  |**RU/s** |**Položka** |**Využití (každou hodinu)** |**Náklady** |
|---------|---------|---------|-------|-------|
|[0-100] |D1:10000 <br/>D2:30 TISÍC <br/>C1:20 TISÍC |Faktura za propustnost pro kontejner v Západní USA (všechny oblasti jsou zapisovatelné)  | `D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 30 K RU/sec/100 * $0.016 * 100 hours = $480` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$960  |
| | |Faktura za propustnost pro 2 další oblasti: Východní USA, Severní Evropa (všechny oblasti jsou zapisovatelné)  |`(2 + 1) * (60 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2 880  |
|[101-200] |D1:50 TIS <br/>D2:70K <br/>C1:-- |Faktura za propustnost pro kontejner v Západní USA (všechny oblasti jsou zapisovatelné)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` |$1920  |
| | |Faktura za propustnost pro 2 další oblasti: Východní USA, Severní Evropa (všechny oblasti jsou zapisovatelné)  |`(2 + 1) * (120 K RU/sec /100 * $0.016) * 100 hours = $5,760`  |$5 760  |
|[201-300]  |D1:50 TIS <br/>D2:70K <br/>C1:20 TISÍC |Faktura za propustnost pro kontejner v Západní USA (všechny oblasti jsou zapisovatelné)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$2 240  |
| | |Faktura za propustnost pro 2 další oblasti: Východní USA, Severní Evropa (všechny oblasti jsou zapisovatelné)  |`(2 + 1) * (140 K RU/sec /100 * $0.016-) * 100 hours = $6,720` |$6 720 |
|[301-400] |D1:10000 <br/>D2:80K <br/>C1:-- |Faktura za propustnost pro kontejner v Západní USA (všechny oblasti jsou zapisovatelné)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 80 K RU/sec/100 * $0.016 * 100 hours = $1,280`  |$1 440   |
| | |Faktura za propustnost pro 2 další oblasti: Východní USA, Severní Evropa (všechny oblasti jsou zapisovatelné)  |`(1 + 1) * (90 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2 880  |
|[401-500] |D1:10000 <br>D2:10 000 <br>C1:20 TISÍC |Faktura za propustnost pro kontejner v Západní USA (všechny oblasti jsou zapisovatelné)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`D2: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$640  |
| | |Faktura za propustnost pro 2 další oblasti: Východní USA, Severní Evropa (všechny oblasti jsou zapisovatelné)  |`(1 + 1) * (40 K RU/sec /100 * $0.016) * 100 hours = $1,280`  |$1 280  |
|[501-700] |D1:20 TISÍC <br>D2:100 TISÍC <br>C1:-- |Faktura za propustnost pro kontejner v Západní USA (všechny oblasti jsou zapisovatelné)  |`D1: 20 K RU/sec/100 * $0.016 * 200 hours = $640` <br>`D2: 100 K RU/sec/100 * $0.016 * 200 hours = $3,200` |$3 840  |
| | |Faktura za propustnost pro 2 další oblasti: Východní USA, Severní Evropa (všechny oblasti jsou zapisovatelné)  |`(1 + 1) * (120 K RU/sec /100 * $0.016) * 200 hours = $1,280`  |$7 680  |
|[701-720] |D1:20 TISÍC <br/>D2:50 TIS <br/>C1:-- |Faktura za propustnost pro kontejner v Západní USA (všechny oblasti jsou zapisovatelné)  |`D1: 20 K RU/sec/100 *$0.016 * 20 hours = $64` <br/>`D2: 50 K RU/sec/100 *$0.016 * 20 hours = $160` |$224  |
| | |Faktura za propustnost pro 2 další oblasti: Východní USA, Severní Evropa (všechny oblasti jsou zapisovatelné)  |`(1 + 1) * (70 K RU/sec /100 * $0.016) * 20 hours = $448`  |$224  |
|| |**Celkové měsíční náklady**  | |**$38 688**   |

## <a name="billing-examples-with-free-tier-accounts"></a>Příklady fakturace s účty bezplatné úrovně
U Azure Cosmos DB úrovně Free získáte k bezplatnému účtu první 400 RU/s a 5 GB úložiště, které se použije na úrovni účtu. Všechna RU/s a úložiště nad 400 RU/s a 5 GB se budou účtovat podle běžných sazeb za ceny na stránce s cenami. Na faktuře se vám nezobrazí poplatky za bezplatné 400 ru/s a 5 GB, a to nad rámec toho, co je zahrnuto do úrovně Free/s a úložiště. 400 RU/s se vztahuje na jakýkoliv typ pro zápisy v rámci RU/s zřízené propustnost, automatické škálování a více oblastí.  

### <a name="billing-example---container-or-database-with-provisioned-throughput"></a>Příklad fakturace – kontejner nebo databáze s zřízenou propustností
- Řekněme, že vytvoříme databázi nebo kontejner v účtu bezplatné úrovně s 400 RU/s a 5 GB úložiště.
- Faktura za tento prostředek se nezobrazí. Vaše hodinové a měsíční náklady budou $0.
- Teď Předpokládejme ve stejném účtu, přidáme další databázi nebo kontejner s 1000 RU/s a 10 GB úložiště.
- Ve vyúčtování se nyní zobrazí poplatek za 1000 RU/s a 10 GB úložiště. 

### <a name="billing-example---container-with-autoscale-throughput"></a>Příklad fakturace – kontejner s propustností automatického škálování
- Řekněme, že v účtu bezplatné úrovně se vytvoří kontejner s povoleným autoškálováním s maximálním počtem RU/s 4000 RU/s. Tento prostředek se automaticky škáluje mezi 400 RU/s-4000 RU/s. 
- Předpokládejme ve hodinu 1 až hod. 10 je zdrojem minimálně 400 RU/s. Během hodiny 11 se prostředek škáluje až 1000 RU/s a pak se za hodinu vrátí do 400 RU/s.
- V hodinách 1 až 10 se vám bude účtovat $0 pro propustnost, protože 400 RU/s se týkalo úrovně Free. 
- Ve hodiny 11 se vám bude účtovat efektivní 1000 RU/s-400 RU/s = 600 RU/s, protože toto je nejvyšší RU/s v hodinách. Po celou hodinu bude mít 6 jednotek 100 RU/s, takže celkové náklady na propustnost za hodinu budou 6 jednotek × $0,012 = $0,072. 
- Jakékoli úložiště nad rámec prvních 5 GB se bude účtovat za běžné sazby za úložiště. 

### <a name="billing-example---multi-region-single-write-region-account"></a>Příklad fakturace – účet s jednou oblastí zápisu s jednou oblastí
- Řekněme, že v účtu bezplatné úrovně se vytvoří databáze nebo kontejner s 1200 RU/s a 10 GB úložiště. Replikuje účet na 3 oblasti a máme jeden účet pro zápis do regionu.
- V celkovém případě bez bezplatné úrovně se vám účtuje 3 × 1200 RU/s = 3600 RU/s a 3 * 10 GB = 30 GB úložiště.
- Po odebrání 400 RU/s a 5 GB úložiště se vám bude účtovat efektivní 3200 RU/s (32 jednotek) zřízené propustnosti v rámci jedné oblasti zápisu a 25 GB úložiště.
- Měsíční náklady na RU/s by byly: 32 jednotek × $0,008 × 24 hodin × 31 dnů = $190,46. Měsíční náklady na úložiště by byly: 25 GB × 0,25/GB = $6,25. Celkové náklady by byly $190,46 + $6,25 = $196,71.
- Poznámka: Pokud se Jednotková cena pro RU/s nebo úložiště liší v oblastech, úroveň Free 400 RU/s a 5 GB bude odpovídat sazbám oblasti, ve které byl účet vytvořen.

### <a name="billing-example---multi-region-account-with-multiple-write-regions"></a>Příklad fakturace – více oblastí, účet s více oblastmi zápisu

Tento příklad odráží [ceny pro zápisy ve více oblastech](https://azure.microsoft.com/pricing/details/cosmos-db/) pro účty vytvořené od 1. prosince 2019. 
- Řekněme, že v účtu bezplatné úrovně se vytvoří databáze nebo kontejner s 1200 RU/s a 10 GB úložiště. Replikuje účet do 3 oblastí a máme k dispozici více oblastí pro zápis. 
- V celkovém případě bez bezplatné úrovně se vám účtuje 3 × 1200 RU/s = 3600 RU/s a 3 * 10 GB = 30 GB úložiště.
- Po odebrání 400 RU/s a 5 GB úložiště se vám bude účtovat efektivní 3200 RU/s (32 jednotek) zřízené propustnosti v rámci více oblastí zápisu a 25 GB úložiště.
- Měsíční náklady na RU/s by byly: 32 jednotek × $0,016 × 24 hodin × 31 dnů = $380,93. Měsíční náklady na úložiště by byly: 25 GB × 0,25/GB = $6,25. Celkové náklady by byly $380,93 + $6,25 = $387,18.

## <a name="proactively-estimating-your-monthly-bill"></a>Proaktivní odhadování měsíčních faktur  

Podívejme se na jiný příklad, kde chcete proaktivně odhadnout fakturu před koncem měsíce. Vyúčtování můžete odhadnout následujícím způsobem:

**Náklady na úložiště**

* Průměrná velikost záznamu (KB) = 1 
* Počet záznamů = 100 000 000 
* Celková velikost úložiště (GB) = 100 
* Měsíční náklady na GB = $0,25 
* Očekávané měsíční náklady za úložiště = $25,00 

**Náklady na propustnost**

|Typ operace| Počet požadavků za sekundu| Průměrný RU/žádost| Požadováno ru|
|----|----|----|----|
|Zápis| 100 | 5 | 500|
|Číst| 400| 1| 400|

Celkem RU/s: 500 + 400 = 900 hodinových nákladů: 900/100 * $0,008 = $0,072 očekávané měsíční náklady na propustnost (předpokládá se 31 dní): $0,072 * 24 * 31 = $53,57

**Celkové měsíční náklady**

Celkové měsíční náklady = měsíční náklady na úložiště a měsíční náklady za celkovou propustnost za celkové měsíční náklady = $25,00 + $53,57 = $78,57

*Ceny se můžou v jednotlivých oblastech lišit. Aktuální ceny najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).*

## <a name="billing-with-azure-cosmos-db-reserved-capacity"></a>Fakturace s Azure Cosmos DB rezervovanou kapacitou

Azure Cosmos DB Rezervovaná kapacita vám umožní koupit zřízenou propustnost předem (Rezervovaná kapacita nebo rezervace), kterou je možné použít pro všechny databáze a kontejnery Azure Cosmos (pro jakékoli rozhraní API nebo datový model) napříč všemi oblastmi Azure. Vzhledem k tomu, že se cena zřízené propustnosti liší v závislosti na oblasti, pomůže vám rezervovat rezervovanou kapacitu jako peněžní kredit, který jste zakoupili na základě slevy, kterou je možné vyřadit z hlediska zřízené propustnosti podle příslušné ceny v jednotlivých oblastech. Řekněme například, že máte účet Azure Cosmos s jediným kontejnerem zřízeným 50-K RU/s a globálně replikovanými dvěma oblastmi – Východní USA a Japonsko – východ. Pokud zvolíte možnost průběžné platby, platíte:  

* v Východní USA: 50-K RU/s rychlostí $0,008 za 100 RU/s v této oblasti 

* v Japonsku – východ: pro 50-K RU/s sazba $0,009 za 100 RU/s v této oblasti

Celkový počet faktur (bez rezervované kapacity) by byl (za 30 dní nebo 720 hodin): 

|**Oblast**| **Hodinová cena za 100 RU/s**|**Jednotky (RU/s)**|**Fakturovaná částka (každou hodinu)**| **Fakturovaná částka (měsíčně)**|
|----|----|----|----|----|
|East US|$0,008 |50 K|$4|$2 880 |
|Japan East|$0,009 |50 K| $4,50 |$3 240 |
|Celkem|||$8,50|$6 120 |

Pojďme si vzít v úvahu, že jste místo toho koupili rezervovanou kapacitu. Rezervovanou kapacitu můžete zakoupit 100 – K RU/s za cenu $56 064 po dobu jednoho roku (s 20% slevou) nebo $6,40 za hodinu. Podívejte se na [stránku s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/)na ceny rezervovaných kapacit.  

* Náklady na propustnost (průběžné platby): 100 000 RU/s/100 * $0.008/Hour × 8760 hodin za rok = $70 080 

* Náklady na propustnost (s rezervovanou kapacitou) $70 080 se slevou 20% = $56 064 

Z toho, co jste efektivně koupili, je kredit $8 za hodinu, za 100 K RU/s pomocí ceníkové ceny v Východní USA za cenu $6,40 za hodinu. Pak můžete vykreslit z této předem placené rezervace propustnosti na každou hodinu v rámci zřízené kapacity propustnosti v libovolné globální oblasti Azure na základě příslušné regionální ceny v seznamu pro vaše předplatné. V tomto příkladu, kdy zřizujete 50 K RU/s, každé v Východní USA a Japonsko – východ, budete moci vykreslit $8,00 na míru zřízené propustnosti za hodinu a bude se vám účtovat překročení limitu $0,50 za hodinu (nebo $360 za měsíc). 

|**Oblast**| **Hodinová cena za 100 RU/s**|**Jednotky (RU/s)**| **Fakturovaná částka (každou hodinu)**| **Fakturovaná částka (měsíčně)**|
|----|----|----|----|----|
|East US|$0,008 |50 K|$4|$2 880 |
|Japan East|$0,009 |50 K| $4,50 |$3 240 |
|||Průběžné platby|$8,50|$6120|
|Zakoupená Rezervovaná kapacita|$0,0064 (20% sleva) |předem zakoupená kapacita 100 RU/s nebo $8 |-$8|-$5 760 |
|Čistý účet|||$0,50 |$360 |

## <a name="next-steps"></a>Další kroky

Dále si můžete přečíst další informace o optimalizaci nákladů v Azure Cosmos DB s využitím následujících článků:

* Přečtěte si další informace o [tom, jak je cenový model Cosmos DB pro zákazníky nákladově efektivní](total-cost-ownership.md) .
* Další informace o [optimalizaci pro vývoj a testování](optimize-dev-test.md)
* Další informace o [optimalizaci nákladů na propustnost](optimize-cost-throughput.md)
* Další informace o [optimalizaci nákladů na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizaci nákladů na čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizaci nákladů na dotazy](./optimize-cost-reads-writes.md)
* Další informace o [optimalizaci nákladů na účty Azure Cosmos ve více oblastech](optimize-cost-regions.md)
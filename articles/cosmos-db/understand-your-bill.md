---
title: Principy faktury azure cosmos DB
description: Tento článek vysvětluje, jak porozumět vaší účtu Azure Cosmos DB s některými příklady.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: 5954c8eda370c0734985c47cfff6d073f5d76d17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258018"
---
# <a name="understand-your-azure-cosmos-db-bill"></a>Vysvětlení informací na faktuře za službu Azure Cosmos DB

Jako plně spravovaná databázová služba nativní pro cloud zjednodušuje Azure Cosmos DB fakturaci tím, že účtuje jenom za zřízenou propustnost a spotřebované úložiště. Neexistují žádné další licenční poplatky, hardware, náklady na služby nebo náklady na zařízení ve srovnání s místními alternativami nebo alternativami hostovanými v IaaS. Když vezmete v úvahu možnosti více oblastí Azure Cosmos DB, databázová služba poskytuje podstatné snížení nákladů ve srovnání s existujícími místními řešeními nebo řešeními IaaS.

S Azure Cosmos DB se vám účtují každou hodinu na základě zřízené propustnosti a spotřebovaného úložiště. Pro zřízenou propustnost je jednotka pro fakturaci 100 RU/s za hodinu, účtovaná za 0,008 USD za hodinu za předpokladu standardních veřejných cen, viz [stránka Ceny](https://azure.microsoft.com/pricing/details/cosmos-db/). U spotřebovaného úložiště se vám bude účtovat 0,25 USD za 1 GB úložiště za měsíc, viz [stránka Ceny](https://azure.microsoft.com/pricing/details/cosmos-db/). 

Tento článek vám na několika příkladech pomůže porozumět podrobným informacím na měsíční faktuře. Čísla uvedená v příkladech se můžou lišit, pokud vaše kontejnery Azure Cosmos mají jinou zřízenou propustnost, pokud pokrývají více oblastí nebo pokud jsou spuštěné po jinou dobu v měsíci.

> [!NOTE]
> Fakturace se účtuje pro jakoukoli část hodiny na zeď, nikoli za dobu 60 minut.

## <a name="billing-examples"></a>Příklady fakturace

### <a name="billing-example---throughput-on-a-container-full-month"></a>Příklad fakturace – propustnost kontejneru (celý měsíc)

* Předpokládejme, že nakonfigurujete propustnost 1 000 RU/s v kontejneru a existuje po dobu 24 hodin * 30 dní pro měsíc = celkem 720 hodin.  

* 1 000 RU/s je 10 jednotek 100 RU/s za hodinu za každou hodinu, kdy kontejnery existují (to znamená 1 000/100 = 10). 

* Vynásobením 10 jednotek za hodinu náklady na $0.008 (za 100 RU/s za hodinu) = $0.08 za hodinu. 

* Vynásobením $0.08 za hodinu počtem hodin v měsíci se rovná $0.08 * 24 hodin * 30 dní = $57.60 za měsíc.  

* Celkový měsíční účet bude zobrazovat 7 200 jednotek (ze 100 RU), což bude stát 57,60 USD.

### <a name="billing-example---throughput-on-a-container-partial-month"></a>Příklad fakturace – propustnost kontejneru (částečný měsíc)

* Předpokládejme, že vytvoříme kontejner s zřízenou propustností 2 500 RU/s. Kontejner žije 24 hodin v měsíci (například ho odstraníme 24 hodin po jeho vytvoření).  

* Pak uvidíme 600 jednotek na účtu (2,500 RU / sec / 100 RU / sec / jednotka * 24 hodin). Cena bude $4.80 (600 jednotek * $0.008/unit).

* Celkový účet za měsíc bude 4,80 dolarů.

### <a name="billing-rate-if-storage-size-changes"></a>Fakturační sazba, pokud se změní velikost úložiště

Kapacita úložiště se účtuje v jednotkách maximálního hodinového množství dat uložených v GB za měsíční období. Pokud jste například využili 100 GB úložiště pro polovinu měsíce a 50 GB pro druhou polovinu měsíce, bude se vám během tohoto měsíce účtovat ekvivalent 75 GB úložiště.

### <a name="billing-rate-when-container-or-a-set-of-containers-are-active-for-less-than-an-hour"></a>Fakturační sazba, pokud jsou kontejner nebo sada kontejnerů aktivní méně než hodinu

Účtuje se vám paušální sazba za každou hodinu, kdy kontejner nebo databáze existuje, bez ohledu na použití nebo pokud je kontejner nebo databáze aktivní méně než hodinu. Pokud například vytvoříte kontejner nebo databázi a odstraníte ji o 5 minut později, bude faktura obsahovat jednu hodinu.

### <a name="billing-rate-when-throughput-on-a-container-or-database-scales-updown"></a>Fakturační sazba při propustnosti kontejneru nebo databáze se zvětšuje a zvětšuje

Pokud zvýšíte zřízenou propustnost v 9:30 z 400 RU/s na 1 000 RU/s a pak snížíte zřízenou propustnost v 10:45 zpět na 400 RU/s, bude se vám účtovat dvě hodiny 1 000 RU/s. 

Pokud zvýšíte zřízenou propustnost pro kontejner nebo sadu kontejnerů v 9:30 z 100 K RU/sec na 200 K RU/s a pak snížíte zřízenou propustnost v 10:45 zpět na 100 K RU/s, bude se vám účtovat dvě hodiny 200 K RU/s.

### <a name="billing-example-multiple-containers-each-with-dedicated-provisioned-throughput-mode"></a>Příklad fakturace: více kontejnerů, každý s vyhrazeným režimem zřízené propustnosti

* Pokud vytvoříte účet Azure Cosmos ve východní USA 2 se dvěma kontejnery s zřízenou propustností 500 RU/s a 700 RU/s, měli byste celkovou zřízenou propustnost 1 200 RU/s.  

* Bude vám účtováno 1 200/100 * 0,008 USD = 0,096 USD/hod. 

* Pokud se vaše propustnost změnila a zvýšila jste kapacitu každého kontejneru o 500 RU/s a zároveň jste vytvořili nový neomezený kontejner s 20 000 RU/s, celková zřízená kapacita by byla 22 200 RU/s (1 000 RU/s + 1 200 RU/s + 20 000 RU/s).  

* Váš účet by se pak změnil na: $0.008 x 222 = $1.776/hour. 

* V měsíci 720 hodin (24 hodin * 30 dní), pokud po dobu 500 hodin zřízená propustnost byla 1 200 RU/s a pro zbývajících 220 hodin zřízená propustnost byla 22.200 RU/s, vaše měsíční účet ukazuje: 500 x $0.096/hour + 220 x $1.776/hour = $438.72/month.

![Příklad vyhrazeného účtu propustnost](./media/understand-your-bill/bill-example1.png)

### <a name="billing-example-containers-with-shared-throughput-mode"></a>Příklad fakturace: kontejnery s režimem sdílené propustnosti

* Pokud vytvoříte účet Azure Cosmos ve východní USA 2 se dvěma databázemi Azure Cosmos (se sadou kontejnerů sdílejících propustnost na úrovni databáze) s zřízenou propustností 50 K RU/s a 70 K RU/s, měli byste mít celkovou zřízenou propustnost 120 K RU/s.  

* Bude vám účtováno 1200 x 0,008 USD = 9,60 USD/hod. 

* Pokud se vaše potřeby propustnosti změnily a zvýšili jste zřízenou propustnost každé databáze o 10 kB RU/s pro každou databázi a přidáte nový kontejner do první databáze s vyhrazeným režimem propustnosti 15 K RU/s do databáze sdílené propustnosti, celková zřízená kapacita by byla 155 K RU/s (60 K RU/sec + 80 K RU/sec + 15 K RU/sec).  

* Váš účet by se pak změnil na: 1,550 * $0.008 = $12.40/hour.  

* V měsíci 720 hodin, pokud po dobu 300 hodin zřízená propustnost byla 120-K RU/s a pro zbývajících 420 hodin zřízená propustnost byla 155-K RU/sec, váš měsíční účet zobrazí: 300 x $9.60/hour + 420 x $12.40/hour = $2,880 + $5,208 = $8,088/month. 

![Příklad účtu sdílené propustnosti](./media/understand-your-bill/bill-example2.png)

## <a name="billing-examples-with-geo-replication-and-multi-master"></a>Příklady fakturace s geografickou replikací a vícehlavním serverem  

Oblasti Azure můžete do svého databázového účtu Azure Cosmos kdykoli přidat nebo odebrat na libovolné místo na světě. Propustnost, kterou jste nakonfigurovali pro různé databáze a kontejnery Azure Cosmos, bude vyhrazena v každé oblasti Azure přidružené k vašemu databázovému účtu Azure Cosmos. Pokud je součet zřízené propustnosti (RU/s) nakonfigurované ve všech databázích a kontejnerech v rámci vašeho databázového účtu Azure Cosmos (zřízeného za hodinu) T a počet oblastí Azure přidružených k vašemu databázovému účtu je N, pak celková zřízená propustnost pro danou hodinu pro váš databázový účet Azure Cosmos, (a) nakonfigurovaná s jednou oblastí zápisu se rovná T x N RU/s a (b) nakonfigurovaná se všemi oblastmi schopnými zpracování zápisů se rovná Ru/s T x (N+1). Zřízená propustnost (oblast jednoho zápisu) stojí $0.008/hour za 100 RU/s a zřízená propustnost s více zapisovatelnými oblastmi (konfigurace více hlavních částí) stojí $0.016/za hodinu za 100 RU/s (viz [stránka Ceny](https://azure.microsoft.com/pricing/details/cosmos-db/)). Ať už jeho oblast jednoho zápisu nebo více oblastí zápisu, Azure Cosmos DB umožňuje číst data z libovolné oblasti.

### <a name="billing-example-multi-region-azure-cosmos-account-single-region-writes"></a>Příklad fakturace: účet Azure Cosmos s více oblastmi, zápisy v jedné oblasti

Předpokládejme, že máte kontejner Azure Cosmos v západní USA. Kontejner je vytvořen s propustností 10 K RU/s a tento měsíc uložíte 1 TB dat. Předpokládejme, že do svého účtu Azure Cosmos přidáte tři oblasti (východní USA, severní Evropa a východní Asie), z nichž každá má stejné úložiště a propustnost. Celkový měsíční účet bude (za předpokladu, že 30 dní v měsíci). Váš účet by byl následující: 

|**Položka** |**Použití (měsíc)** |**Sazba** |**Měsíční náklady** |
|---------|---------|---------|-------|
|Účet za propustnost pro kontejner v západní USA      | 10K RU/s * 24 * 30    |$0.008 za 100 RU/s za hodinu   |576 USD|
|Účet za propustnost pro další 3 oblasti – východní USA, severní Evropa a východní Asie       | 3 * 10K RU/s * 24 * 30    |$0.008 za 100 RU/s za hodinu  |$1,728|
|Účet za skladování kontejneru v západní USA      | 250 GB    |$0.25/GB  |62,50 dolarů|
|Účet za úložiště pro další 3 oblasti – východní USA, severní Evropa a východní Asie      | 3 * 250 GB    |$0.25/GB  |187,50 dolarů|
|**Celkem**     |     |  |**2 554 USD**|

*Předpokládejme také, že každý měsíc odezníte 100 GB dat z kontejneru v západní CHUsa a replikujete data do východní USA, severní Evropy a východní Asie. Poplatky za odchozí přenos se vám účtují podle rychlosti přenosu dat.*

### <a name="billing-example-multi-region-azure-cosmos-account-multi-region-writes"></a>Příklad fakturace: účet Azure Cosmos s více oblastmi, zápisy ve více oblastech

Předpokládejme, že vytvoříte kontejner Azure Cosmos v západní USA. Kontejner je vytvořen s propustností 10 K RU/s a tento měsíc uložíte 1 TB dat. Předpokládejme, že přidáte tři oblasti (východní USA, severní Evropa a východní Asie), každý se stejným úložištěm a propustností a chcete mít možnost zapisovat do kontejnerů ve všech oblastech přidružených k vašemu účtu Azure Cosmos. Celkový měsíční účet bude (za předpokladu, že 30 dní v měsíci) bude následující:

|**Položka** |**Použití (měsíc)**|**Sazba** |**Měsíční náklady** |
|---------|---------|---------|-------|
|Účet za propustnost pro kontejner v západní USA (všechny oblasti jsou zapisovatelné)       | 10K RU/s * 24 * 30    |$0.016 za 100 RU/s za hodinu    |$1,152 |
|Účet za propustnost pro další 3 oblasti – východní USA, severní Evropa a východní Asie (všechny oblasti jsou zapisovatelné)        | (3 + 1) * 10K RU/s * 24 * 30    |$0.016 za 100 RU/s za hodinu   |4 608 USD |
|Účet za skladování kontejneru v západní USA      | 250 GB    |$0.25/GB  |62,50 dolarů|
|Účet za úložiště pro další 3 oblasti – východní USA, severní Evropa a východní Asie      | 3 * 250 GB    |$0.25/GB  |187,50 dolarů|
|**Celkem**     |     |  |**6 010 USD**|

*Předpokládejme také, že každý měsíc odezníte 100 GB dat z kontejneru v západní CHUsa a replikujete data do východní USA, severní Evropy a východní Asie. Poplatky za odchozí přenos se vám účtují podle rychlosti přenosu dat.*

### <a name="billing-example-azure-cosmos-account-with-multi-master-database-level-throughput-including-dedicated-throughput-mode-for-some-containers"></a>Příklad fakturace: Účet Azure Cosmos s propustností na úrovni více hlavních objektů databáze včetně vyhrazeného režimu propustnosti pro některé kontejnery

Podívejme se na následující příklad, kde máme účet Azure Cosmos s více oblastmi, kde jsou všechny oblasti zapisovatelné (konfigurace s více servery). Pro jednoduchost budeme předpokládat, velikost úložiště zůstane konstantní a nemění a vynechat zde zachovat příklad jednodušší. Zřízená propustnost během měsíce se měnila takto (za předpokladu, že 30 dní nebo 720 hodin): 

[0-100 hodin]:  

* Vytvořili jsme účet Azure Cosmos se třemi oblastmi (Západní USA, Východní USA, Severní Evropa), kde jsou všechny oblasti zapisovatelné. 

* Vytvořili jsme databázi (D1) se sdílenou propustností 10 K RU/s 

* Vytvořili jsme databázi (D2) se sdílenou propustností 30-K RU/sec a  

* Vytvořili jsme kontejner (C1) s vyhrazenou propustností 20 K RU/s 

[101-200 hodin]:  

* Zvětšili jsme databázi (D1) na 50 K RU/s 

* Zvětšili jsme databázi (D2) na 70 K RU/s  

* Kontejner jsme smazali (C1)  

[201-300 hodin]:  

* Znovu jsme vytvořili kontejner (C1) s vyhrazenou propustností 20 K RU/s 

[301-400 hodin]:  

* Z účtu Azure Cosmos jsme odebrali jednu z oblastí (počet zapisovatelných oblastí je teď 2). 

* Zmenšené databáze (D1) na 10K RU/s 

* Zvětšili jsme databázi (D2) na 80 K RU/s  

* Kontejner jsme znovu smazali (C1) 

[401-500 hodin]:  

* Zmenšené databáze (D2) na 10K RU/s  

* Znovu jsme vytvořili kontejner (C1) s vyhrazenou propustností 20 K RU/s 

[501-700 hodin]:  

* Zvětšili jsme databázi (D1) na 20 K RU/s  

* Zvětšili jsme databázi (D2) na 100 K RU/s  

* Kontejner jsme znovu smazali (C1)  

[701-720 hodin]:  

* Zmenšené databáze (D2) na 50 K RU/s  

Vizuálně jsou změny celkové uzdy za 720 hodin za měsíc zobrazeny na obrázku níže: 

![Příklad reálného života](./media/understand-your-bill/bill-example3.png)

Celkový měsíční účet bude (za předpokladu, že 30 dní / 720 hodin v měsíci) bude vypočítán takto:

|**Hodiny**  |**ŽP/s** |**Položka** |**Použití (každou hodinu)** |**Náklady** |
|---------|---------|---------|-------|-------|
|[0-100] |D1:10K <br/>D2:30K <br/>C1:20K |Účet za propustnost pro kontejner v západní USA (všechny oblasti jsou zapisovatelné)  | `D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 30 K RU/sec/100 * $0.016 * 100 hours = $480` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |960 dolarů  |
| | |Účet za propustnost pro další 2 oblasti: Východní USA, Severní Evropa (všechny regiony jsou zapisovatelné)  |`(2 + 1) * (60 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |2 880 USD  |
|[101-200] |D1:50K <br/>D2:70K <br/>C1: -- |Účet za propustnost pro kontejner v západní USA (všechny oblasti jsou zapisovatelné)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` |1920 dolarů  |
| | |Účet za propustnost pro další 2 oblasti: Východní USA, Severní Evropa (všechny regiony jsou zapisovatelné)  |`(2 + 1) * (120 K RU/sec /100 * $0.016) * 100 hours = $5,760`  |5 760 USD  |
|[201-300]  |D1:50K <br/>D2:70K <br/>C1:20K |Účet za propustnost pro kontejner v západní USA (všechny oblasti jsou zapisovatelné)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |2 240 USD  |
| | |Účet za propustnost pro další 2 oblasti: Východní USA, Severní Evropa (všechny regiony jsou zapisovatelné)  |`(2 + 1) * (140 K RU/sec /100 * $0.016-) * 100 hours = $6,720` |6 720 KČ |
|[301-400] |D1:10K <br/>D2:80K <br/>C1: -- |Účet za propustnost pro kontejner v západní USA (všechny oblasti jsou zapisovatelné)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 80 K RU/sec/100 * $0.016 * 100 hours = $1,280`  |1 440 USD   |
| | |Účet za propustnost pro další 2 oblasti: Východní USA, Severní Evropa (všechny regiony jsou zapisovatelné)  |`(1 + 1) * (90 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |2 880 USD  |
|[401-500] |D1:10K <br>D2:10K <br>C1:20K |Účet za propustnost pro kontejner v západní USA (všechny oblasti jsou zapisovatelné)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`D2: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |640 dolarů  |
| | |Účet za propustnost pro další 2 oblasti: Východní USA, Severní Evropa (všechny regiony jsou zapisovatelné)  |`(1 + 1) * (40 K RU/sec /100 * $0.016) * 100 hours = $1,280`  |1 280 USD  |
|[501-700] |D1:20K <br>D2:100K <br>C1: -- |Účet za propustnost pro kontejner v západní USA (všechny oblasti jsou zapisovatelné)  |`D1: 20 K RU/sec/100 * $0.016 * 200 hours = $640` <br>`D2: 100 K RU/sec/100 * $0.016 * 200 hours = $3,200` |3 840 USD  |
| | |Účet za propustnost pro další 2 oblasti: Východní USA, Severní Evropa (všechny regiony jsou zapisovatelné)  |`(1 + 1) * (120 K RU/sec /100 * $0.016) * 200 hours = $1,280`  |7 680 USD  |
|[701-720] |D1:20K <br/>D2:50K <br/>C1: -- |Účet za propustnost pro kontejner v západní USA (všechny oblasti jsou zapisovatelné)  |`D1: 20 K RU/sec/100 *$0.016 * 20 hours = $64` <br/>`D2: 50 K RU/sec/100 *$0.016 * 20 hours = $160` |224 USD  |
| | |Účet za propustnost pro další 2 oblasti: Východní USA, Severní Evropa (všechny regiony jsou zapisovatelné)  |`(1 + 1) * (70 K RU/sec /100 * $0.016) * 20 hours = $448`  |224 USD  |
|| |**Celkové měsíční náklady**  | |**38 688 USD**   |

## <a name="billing-examples-with-free-tier-accounts"></a>Příklady fakturace s bezplatnými účty úrovní
S bezplatnou vrstvou Azure Cosmos DB získáte první 400 RU/s a 5 GB úložiště ve vašem účtu zdarma, které se použijí na úrovni účtu. Veškeré Ru/s a úložiště nad 400 RU/s a 5 GB se budou účtovat podle běžných cen podle cenových stránek. Na účtu se nezobrazí poplatek ani řádková položka za bezplatné 400 Ru/s a 5 GB, pouze RU/s a úložiště nad rámec toho, na co se vztahuje bezplatná úroveň. 400 RU/s se vztahuje na všechny typy RU/s – zřízená propustnost, autopilot (náhled) a multi-master.  

### <a name="billing-example---container-or-database-with-provisioned-throughput"></a>Příklad fakturace – kontejner nebo databáze se zřízenou propustností
- Předpokládejme, že vytvoříme databázi nebo kontejner na účtu volné úrovně s 400 RU/s a 5 GB úložiště.
- Na vyúčtování se nezobrazí žádný poplatek za tento zdroj. Hodinové a měsíční náklady budou 0 USD.
- Nyní předpokládejme, že ve stejném účtu přidáme další databázi nebo kontejner s 1000 RU/s a 10 GB úložiště.
- Váš účet nyní zobrazí poplatek za 1000 RU/s a 10 GB úložiště. 

### <a name="billing-example---container-or-database-with-autopilot-throughput-preview"></a>Příklad fakturace – kontejner nebo databáze s propustností autopilota (náhled)
- Předpokládejme, že na bezplatném účtu úrovně vytvoříme databázi nebo kontejner s povoleným autopilotem s maximálním RU/s 4000 RU/s. Tento zdroj bude automaticky škálovat mezi 400 RU/s - 4000 RU/s. 
- Předpokládejme, že v hodině 1 až hodinu 10, zdroj je na minimálně 400 RU/s. Během hodiny 11 se zdroj vyšpřuje až na 1000 RU/s a během hodiny se vrátí na 400 RU/s.
- V hodinách 1 až 10 vám bude účtováno 0 USD za propustnost, protože 400 RU/s bylo pokryto úrovní volného. 
- V hodině 11 vám bude účtováno efektivní 1000 RU/s - 400 RU/s = 600 RU/s, protože se jedná o nejvyšší Ru/s za hodinu. To bude 6 jednotek 100 RU/s za hodinu, takže celkové náklady na propustnost za hodinu budou 6 jednotek * $0.012 = $0.072. 
- Jakékoli úložiště nad rámec prvních 5 GB se bude účtovat za běžné sazby za úložiště. 

### <a name="billing-example---multi-region-single-write-region-account"></a>Příklad fakturace – účet oblasti pro více oblastí, jeden zápis
- Předpokládejme, že v účtu volné úrovně vytvoříme databázi nebo kontejner s 1200 RU/s a 10 GB úložiště. Replikujeme účet do 3 oblastí a máme účet s jedním hlavním serverem (jeden oblast zápisu).
- Celkem bez volné úrovně by chomčat 3 * 1200 RU /s = 3600 RU/s a 3 * 10 GB = 30 GB úložiště.
- S bezplatnou slevou úrovně, po odebrání 400 RU/s a 5 GB úložiště, se nám bude účtovat efektivní 3200 RU/s (32 jednotek) zřízené propustnosti při sazbě oblasti jednoho zápisu a 25 GB úložiště.
- Měsíční náklady pro RU/s by byly: 32 jednotek * $0.008 * 24 hodin * 31 dní = 190,46 USD. Měsíční náklady na úložiště by byly: 25 GB * 0,25 / GB = 6,25 USD. Celkové náklady by byly $190.46 + $6.25 = $196.71.
- Poznámka: Pokud se jednotková cena za Ru/s nebo úložiště v regionech liší, bude volná úroveň 400 RU/s a 5 GB odrážet sazby regionu, ve které byl účet vytvořen.

### <a name="billing-example---multi-region-multi-master-multiple-write-region-account"></a>Příklad fakturace – účet s více oblastmi, více hlavních (více oblastí zápisu)

Tento příklad odráží [ceny s více kmenovými prvky](https://azure.microsoft.com/pricing/details/cosmos-db/) pro účty vytvořené po 1. 
- Předpokládejme, že v účtu volné úrovně vytvoříme databázi nebo kontejner s 1200 RU/s a 10 GB úložiště. Replikujeme účet do 3 oblastí a máme účet s více kmenovými (více oblastí zápisu). 
- Celkem bez volné úrovně by chomčat 3 * 1200 RU /s = 3600 RU/s a 3 * 10 GB = 30 GB úložiště.
- S bezplatnou slevou úrovně, po odebrání 400 RU/s a 5 GB úložiště, se nám bude účtovat efektivní 3200 RU/s (32 jednotek) zřízené propustnosti při sazbě vícenásobné oblasti zápisu a 25 GB úložiště.
- Měsíční náklady pro RU/s by byly: 32 jednotek * $0.016 * 24 hodin * 31 dní = $380.93. Měsíční náklady na úložiště by byly: 25 GB * 0,25 / GB = 6,25 USD. Celkové náklady by byly $380.93 + $6.25 = $387.18.

## <a name="proactively-estimating-your-monthly-bill"></a>Proaktivní odhad měsíčního vyúčtování  

Podívejme se na další příklad, kde chcete proaktivně odhadnout svou fakturu před koncem měsíce. Fakturu můžete odhadnout takto:

|**Náklady na úložiště** | |
|----|----|
|Velikost rekordu (KB) |1 |
|Počet záznamů  |100,000,000  |
|Celkové úložiště (GB)  |100 |
|Měsíční náklady na GB  |$0,25  |
|Očekávané měsíční náklady na úložiště   |25,00 dolarů  |

<br>

|**Náklady na propustnost** | | | |
|----|----|----|----|
|Typ operace| Požadavky/s| Vzdusičná ŽP/žádost| Je zapotřebí ru|
|Zápis| 100 | 5 | 500|
|Čtení| 400| 1| 400|

Celkem RU/s: 500 + 400 = 900 Hodinové náklady: 900/100 * $0.008 = $0.072 Očekávané měsíční náklady na propustnost (za předpokladu 31 dnů): $0.072 * 24 * 31 = $53.57

**Celkové měsíční náklady**

Celkové měsíční náklady = měsíční náklady na úložiště + měsíční náklady na celkové měsíční náklady = $25.00 + $53.57 = $78.57

*Ceny se mohou v jednotlivých oblastech lišit. Aktuální ceny najdete na [stránce Ceny](https://azure.microsoft.com/pricing/details/cosmos-db/).*

## <a name="billing-with-azure-cosmos-db-reserved-capacity"></a>Fakturace s rezervovanou kapacitou Azure Cosmos DB

Rezervovaná kapacita Azure Cosmos DB umožňuje předem zakoupit zřízenou propustnost (rezervovanou kapacitu nebo rezervaci), kterou lze použít pro všechny databáze a kontejnery Azure Cosmos (pro libovolné rozhraní API nebo datový model) ve všech oblastech Azure. Vzhledem k tomu, že zřízené propustnosti cena se liší v závislosti na oblasti, pomáhá myslet rezervované kapacity jako peněžní úvěr, který jste zakoupili se slevou, které lze čerpat z pro zřízené propustnosti za příslušnou cenu v jednotlivých oblastech. Řekněme například, že máte účet Azure Cosmos s jedním kontejnerem zřízeným 50 K RU/s a globálně replikované dvě oblasti – východní USA a Japonsko – východ. Pokud zvolíte možnost průběžných plateb, zaplatíte:  

* ve východní chudince USA: pro 50-K RU/s ve výši 0,008 USD za 100 RU/s v tomto regionu 

* v Japonsku – východ: pro 50-K RU/s ve výši 0,009 USD za 100 RU/s v tomto regionu

Celková faktura (bez rezervované kapacity) by byla (za předpokladu, že 30 dní nebo 720 hodin): 

|**Oblasti**| **Hodinová cena za 100 RU/s**|**Jednotky (ŽP/jednotky)**|**Fakturovaná částka (každou hodinu)**| **Fakturovaná částka (měsíčně)**|
|----|----|----|----|----|
|USA – východ|$0.008 |50 K|400 Kč|2 880 USD |
|Japonsko – východ|$0.009 |50 K| 4,50 dolarů |3 240 USD |
|Celkem|||8,50 dolarů|6 120 USD |

Vezměme si, že jste si místo toho koupili rezervovanou kapacitu. Můžete si koupit rezervovanou kapacitu za 100-K RU/sec za cenu $56,064 na jeden rok (s 20% slevou) nebo $ 6.40 za hodinu. Ceny rezervované kapacity najdete na [stránce Ceny](https://azure.microsoft.com/pricing/details/cosmos-db/)).  

* Náklady na propustnost (průběžně platby): 100 000 RU/s/100 * 0,008 USD/hod * 8760 hodin za rok = 70 080 USD 

* Náklady na propustnost (s rezervovanou kapacitou) $70,080 slevy na 20% = $56,064 

Co jste skutečně zakoupili, je úvěr ve výši $ 8 za hodinu, za 100 K RU / sec pomocí ceníkové ceny ve východní USA, za cenu $ 6.40 za hodinu. Pak můžete čerpat z této předplacené rezervace propustnosti na hodinovém základě pro zřízenou kapacitu propustnosti v libovolné globální oblasti Azure v příslušných místních ceníkových cen stanovených pro vaše předplatné. V tomto příkladu, kde zřídíte 50 K RU/s každý v usa a Japonsko východ, budete moci čerpat 8,00 dolarů v hodnotě zřízené propustnost za hodinu a bude účtována nadhodnost 0,50 dolarů za hodinu (nebo $360/měsíc). 

|**Oblasti**| **Hodinová cena za 100 RU/s**|**Jednotky (ŽP/jednotky)**| **Fakturovaná částka (každou hodinu)**| **Fakturovaná částka (měsíčně)**|
|----|----|----|----|----|
|USA – východ|$0.008 |50 K|400 Kč|2 880 USD |
|Japonsko – východ|$0.009 |50 K| 4,50 dolarů |3 240 USD |
|||Pay-as-you-go|8,50 dolarů|6120 dolarů|
|Zakoupená rezervovaná kapacita|$0.0064 (20% sleva) |Předzakoupení kapacity 100 RU/s nebo 8 USD |-8 USD|-5 760 USD |
|Čistý účet|||$0,50 |360 dolarů |

## <a name="next-steps"></a>Další kroky

Dále můžete pokračovat v informacích o optimalizaci nákladů v Azure Cosmos DB s následujícími články:

* Další informace o [tom, jak je cenový model Cosmos DB pro zákazníky nákladově efektivní](total-cost-ownership.md)
* Další informace o [optimalizaci pro vývoj a testování](optimize-dev-test.md)
* Další informace o [optimalizaci nákladů na propustnost](optimize-cost-throughput.md)
* Další informace o [optimalizaci nákladů na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizaci nákladů na čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizaci nákladů na dotazy](optimize-cost-queries.md)
* Další informace o [optimalizaci nákladů na účty Azure Cosmos s více oblastmi](optimize-cost-regions.md)

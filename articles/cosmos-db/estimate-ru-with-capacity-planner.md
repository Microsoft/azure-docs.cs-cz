---
title: Odhad nákladů pomocí plánovače kapacity Azure Cosmos DB
description: Plánovač kapacity Azure Cosmos DB umožňuje odhadnout požadovanou propustnost (RU/s) a náklady na vaše úlohy. Tento článek popisuje, jak pomocí nové verze plánovače kapacit y odhadnout požadovanou propustnost a náklady.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dech
ms.openlocfilehash: f10ace47f774e31b586f7736f5fb8e5dfea0c948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68707627"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>Odhad ru/s pomocí plánovače kapacity Azure Cosmos DB

Konfigurace databází a kontejnerů Služby Azure Cosmos se správným množstvím zřízené propustnosti nebo [jednotek požadavků (RU/s)](request-units.md)pro vaše úlohy je nezbytná pro optimalizaci nákladů a výkonu. Tento článek popisuje, jak pomocí [plánovačkapacity](https://cosmos.azure.com/capacitycalculator/) Azure Cosmos DB získat odhad požadované RU/s a náklady na vaše úlohy. 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>Jak odhadnout propustnost a náklady pomocí plánovače kapacity Služby Azure Cosmos DB

Plánovač kapacity lze použít ve dvou režimech.

|**Mode**  |**Popis**  |
|---------|---------|
|Basic|Poskytuje rychlý, vysoký-úrovni RU / s odhad nákladů. Tento režim předpokládá výchozí nastavení Azure Cosmos DB pro zásady indexování, konzistenci a další parametry. <br/><br/>Použijte základní režim pro rychlý odhad na vysoké úrovni, když vyhodnocujete potenciální úlohu, která se má spouštět v Azure Cosmos DB.|
|Upřesnit|Poskytuje podrobnější ru/s a odhad nákladů s možností vyladit další nastavení – zásady indexování, úroveň konzistence a další parametry, které ovlivňují náklady a propustnost. <br/><br/>Použijte rozšířený režim, když odhadujete RU/s pro nový projekt nebo chcete podrobnější odhad. |


## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a>Odhad zřízené propustnosti a nákladů pomocí základního režimu
Chcete-li získat rychlý odhad úlohy pomocí základního režimu, přejděte na [plánovač kapacity](https://cosmos.azure.com/capacitycalculator/). Zadejte následující parametry na základě vašeho pracovního vytížení: 

|**Vstup**  |**Popis**  |
|---------|---------|
|Počet regionů|Azure Cosmos DB je k dispozici ve všech oblastech Azure. Vyberte počet oblastí požadovaných pro vaši pracovní zátěž. K účtu Cosmos můžete přidružit libovolný počet oblastí. Další podrobnosti najdete v [tématu globální distribuce](distribute-data-globally.md) v Azure Cosmos DB.|
|Zápisy ve více oblastech|Pokud povolíte [zápisy ve více oblastech](distribute-data-globally.md#key-benefits-of-global-distribution), aplikace může číst a zapisovat do libovolné oblasti Azure. Pokud zakážete zápisy ve více oblastech, aplikace může zapisovat data do jedné oblasti. <br/><br/> Povolte zápisy ve více oblastech, pokud očekáváte, že budete mít aktivní aktivní úlohu, která vyžaduje zápisy s nízkou latencí v různých oblastech. Například zatížení IOT, který zapisuje data do databáze na vysokých objemech v různých oblastech. <br/><br/> Víceoblastí zápisů zaručuje 99,999% dostupnost čtení a zápisu. Zápisy ve více oblastech vyžadují větší propustnost ve srovnání s oblastmi jednoho zápisu. Další informace naleznete v tématu, [jak se ru liší pro jeden a více oblastí zápisu](optimize-cost-regions.md) článku.|
|Celková uložená data (podle oblasti)|Celková odhadovaná data uložená v GB v jedné oblasti.|
|Velikost položky|Odhadovaná velikost datové položky (např. dokumentu) v rozsahu od 1 KB do 2 MB. |
|Čtení/s na oblast|Počet očekávaných čtení za sekundu. |
|Zápisy/s na oblast|Počet zápisů očekávaných za sekundu. |

Po vyplnění požadovaných podrobností vyberte **Vypočítat**. Karta **Odhad nákladů** zobrazuje celkové náklady na úložiště a zřízenou propustnost. Na této kartě můžete rozbalit odkaz **Zobrazit podrobnosti** a získat tak rozdělení propustnost požadované pro požadavky na čtení a zápis. Pokaždé, když změníte hodnotu libovolného pole, vyberte **Vypočítat,** chcete-li přepočítat odhadované náklady. 

![Základní režim plánovače kapacity](./media/estimate-ru-with-capacity-planner/basic-mode.png)

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a>Odhad zřízené propustnosti a nákladů pomocí rozšířeného režimu

Rozšířený režim umožňuje poskytnout další nastavení, která mají vliv na odhad RU/s. Chcete-li použít tuto možnost, přejděte na [plánovač kapacity](https://cosmos.azure.com/capacitycalculator/) a přihlaste se k nástroji pomocí účtu, který používáte pro Azure. Možnost přihlášení je k dispozici v pravém rohu. 

Po přihlášení se zobrazí další pole ve srovnání s poli v základním režimu. Zadejte další parametry na základě vaší úlohy. 

|**Vstup**  |**Popis**  |
|---------|---------|
|rozhraní API|Azure Cosmos DB je služba s více modely a více rozhraníapi. Pro nové úlohy vyberte rozhraní API SQL (Core). |
|Počet regionů|Azure Cosmos DB je k dispozici ve všech oblastech Azure. Vyberte počet oblastí požadovaných pro vaši pracovní zátěž. K účtu Cosmos můžete přidružit libovolný počet oblastí. Další podrobnosti najdete v [tématu globální distribuce](distribute-data-globally.md) v Azure Cosmos DB.|
|Zápisy ve více oblastech|Pokud povolíte [zápisy ve více oblastech](distribute-data-globally.md#key-benefits-of-global-distribution), aplikace může číst a zapisovat do libovolné oblasti Azure. Pokud zakážete zápisy ve více oblastech, aplikace může zapisovat data do jedné oblasti. <br/><br/> Povolte zápisy ve více oblastech, pokud očekáváte, že budete mít aktivní aktivní úlohu, která vyžaduje zápisy s nízkou latencí v různých oblastech. Například zatížení IOT, který zapisuje data do databáze na vysokých objemech v různých oblastech. <br/><br/> Víceoblastí zápisů zaručuje 99,999% dostupnost čtení a zápisu. Zápisy ve více oblastech vyžadují větší propustnost ve srovnání s oblastmi jednoho zápisu. Další informace naleznete v tématu, [jak se ru liší pro jeden a více oblastí zápisu](optimize-cost-regions.md) článku.|
|Výchozí konzistence|Azure Cosmos DB podporuje 5 úrovní konzistence, aby vývojáři mohli vyvážit kompromis mezi konzistencí, dostupností a kompromisy latence. Další informace naleznete v článku [úrovně konzistence.](consistency-levels.md) <br/><br/> Ve výchozím nastavení Azure Cosmos DB používá konzistenci relace, která zaručuje možnost číst vlastní zápisy v relaci. <br/><br/> Volba silné nebo ohraničené neaktuálnost bude vyžadovat dvojnásobek požadované RU/s pro čtení, ve srovnání s relace, konzistentní předpona a případné konzistence. Silná konzistence s zápisy ve více oblastech není podporována a automaticky se stane výchozí pro zápisy v jedné oblasti se silnou konzistencí. |
|Zásady indexování|Ve výchozím nastavení Azure Cosmos DB [indexuje všechny vlastnosti](index-policy.md) ve všech položkách pro flexibilní a efektivní dotazy (mapuje na zásady **automatickéindexování).** <br/><br/> Pokud zvolíte **vypnuto**, žádná z vlastností se neindexuje. Výsledkem je nejnižší poplatek žP za zápisy. Vyberte **zásadu vypnutí,** pokud očekáváte pouze [bod čtení](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet) (vyhledávání hodnot klíče) a/nebo zápisy a žádné dotazy. <br/><br/> Vlastní zásady indexování umožňuje zahrnout nebo vyloučit určité vlastnosti z indexu pro nižší propustnost zápisu a úložiště. Další informace naleznete v článcích [zásad indexování](index-overview.md) a [ukázkových zásad indexování.](how-to-manage-indexing-policy.md#indexing-policy-examples)|
|Celková uložená data (podle oblasti)|Celková odhadovaná data uložená v GB v jedné oblasti.|
|Režim pracovního vytížení|Pokud je objem pracovního vytížení konstantní, vyberte **možnost Stabilní.** <br/><br/> Pokud se objem pracovního vytížení v průběhu času mění, vyberte možnost **Proměnná.**  Například během určitého dne nebo měsíce. <br/><br/> Pokud zvolíte možnost proměnné pracovní vytížení, jsou k dispozici následující nastavení:<ul><li>Procento času ve špičce: Procento času v měsíci, kdy vaše úloha vyžaduje propustnost ve špičce (nejvyšší). <br/><br/> Pokud máte například pracovní vytížení, které má vysokou aktivitu během pracovní doby 9:00 – 18:00 ve všední den, pak procento času ve špičce je: 45 hodin ve špičce / 730 hodin / měsíc = ~6 %.<br/><br/></li><li>Čtení/s na oblast ve špičce – počet čtení očekávaných za sekundu ve špičce.</li><li>Zápisy/s na oblast ve špičce – počet zápisů očekávaných za sekundu ve špičce.</li><li>Čtení/s na oblast mimo špičku – počet čtení očekávaných za sekundu během mimo špičku.</li><li>Zápisy/s na oblast mimo vrchol – počet zápisů očekávaných za sekundu během mimo špičku.</li></ul>S intervaly špičky a mimo špičku můžete optimalizovat náklady [programovým škálováním zřízené propustnosti](set-throughput.md#update-throughput-on-a-database-or-a-container) nahoru a dolů.|
|Velikost položky|Velikost datové položky (např. dokumentu) v rozsahu od 1 KB do 2 MB. <br/><br/>Můžete také **nahrát ukázkový dokument (JSON)** pro přesnější odhad.<br/><br/>Pokud vaše úloha obsahuje více typů položek (s různým obsahem JSON) ve stejném kontejneru, můžete nahrát více dokumentů JSON a získat odhad. Pomocí tlačítka **Přidat novou položku** přidejte více ukázkových dokumentů JSON.|

Pomocí tlačítka **Uložit odhad** můžete také stáhnout soubor CSV obsahující aktuální odhad. 

![Pokročilý režim plánovače kapacity](./media/estimate-ru-with-capacity-planner/advanced-mode.png)

Ceny uvedené v plánovači kapacity Azure Cosmos DB jsou odhady založené na veřejných cenových sazbách za propustnost a úložiště. Všechny ceny jsou uvedeny v amerických dolarech. Na stránce [s cenami Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) najdete všechny sazby podle oblasti.  

## <a name="estimating-throughput-for-queries"></a>Odhad propustnosti pro dotazy

Kalkulačka kapacity Azure Cosmos předpokládá čtení bodů (čtení jedné položky, například dokumentu, podle ID a hodnoty klíče oddílu) a zápisy pro úlohu. Chcete-li odhadnout propustnost potřebnou pro dotazy, spusťte dotaz na reprezentativní datové sady v kontejneru Cosmos a [získat poplatek RU](find-request-unit-charge.md). Vynásobte žP poplatek počtem dotazů, které očekáváte spuštění za sekundu, abyste získali celkový požadovaný ŽP/s. 

Například pokud vaše úloha ``SELECT * FROM c WHERE c.id = 'Alice'`` vyžaduje dotaz, který je spuštěn 100 krát za sekundu a žP poplatek dotazu je 10 RU, budete potřebovat 100 dotaz / sec * 10 RU / dotaz = 1000 RU/s celkem sloužit tyto požadavky. Přidejte tyto Ru/s do Ru/s požadované pro všechny čtení nebo zápisy, ke které dochází ve vaší pracovní zátěži.

## <a name="next-steps"></a>Další kroky

* Další informace o [cenovém modelu Azure Cosmos DB](how-pricing-works.md).
* Vytvořte nový [účet Cosmos, databázi a kontejner](create-cosmosdb-resources-portal.md).
* Zjistěte, jak optimalizovat náklady na [zřízenou propustnost](optimize-cost-throughput.md).
* Přečtěte si, jak [optimalizovat náklady s rezervovanou kapacitou](cosmos-db-reserved-capacity.md).


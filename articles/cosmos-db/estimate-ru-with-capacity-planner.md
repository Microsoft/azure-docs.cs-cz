---
title: Odhad nákladů pomocí plánovače kapacity Azure Cosmos DB
description: Plánovač kapacity Azure Cosmos DB umožňuje odhadnout propustnost (RU/s) požadované a náklady na vaše zatížení. Tento článek popisuje, jak používat novou verzi plánovače kapacity k odhadu propustnosti a potřebných nákladů.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/30/2019
ms.author: dech
ms.openlocfilehash: 829fb28577fbb89c031a3b972d3ee5e04c935e9f
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434997"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>Odhad RU/s pomocí plánovače kapacity Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Konfigurace databází a kontejnerů Azure Cosmos s využitím správného množství zřízené propustnosti nebo [jednotek žádostí (ru/s)](request-units.md)pro vaše zatížení je základem pro optimalizaci nákladů a výkonu. Tento článek popisuje, jak pomocí služby Azure Cosmos DB [Capacity Planner](https://cosmos.azure.com/capacitycalculator/) získat odhad požadovaných ru/s a nákladů na vaše zatížení. 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>Jak odhadnout propustnost a náklady pomocí plánovače kapacity Azure Cosmos DB

Plánovač kapacity se dá použít ve dvou režimech.

|**Režim**  |**Popis**  |
|---------|---------|
|Basic|Poskytuje rychlý, vysoce na vysoké úrovni RU/s a odhad nákladů. Tento režim předpokládá výchozí nastavení Azure Cosmos DB pro indexování zásad, konzistence a dalších parametrů. <br/><br/>Základní režim můžete použít pro rychlý odhad na nejvyšší úrovni při vyhodnocování potenciálního zatížení pro spuštění na Azure Cosmos DB.|
|Pokročilý|Poskytuje podrobnější informace o RU/s a odhad nákladů s možností optimalizace dalších nastavení – zásad indexování, úrovně konzistence a dalších parametrů, které mají vliv na náklady a propustnost. <br/><br/>Pokročilý režim použijte při odhadování RU/s pro nový projekt nebo chcete podrobnější odhad. |


## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a>Odhad zajištěné propustnosti a nákladů pomocí základního režimu
Pokud chcete získat rychlý odhad vašich úloh pomocí režimu Basic, přejděte do [plánovače kapacity](https://cosmos.azure.com/capacitycalculator/). Na základě vašeho zatížení zadejte následující parametry: 

|**Vstup**  |**Popis**  |
|---------|---------|
|Počet oblastí|Azure Cosmos DB je k dispozici ve všech oblastech Azure. Vyberte počet oblastí vyžadovaných pro vaše zatížení. K vašemu účtu Cosmos můžete přidružit libovolný počet oblastí. Další podrobnosti najdete v tématu [globální distribuce](distribute-data-globally.md) v Azure Cosmos DB.|
|Zápisy ve více oblastech|Pokud povolíte [zápisy ve více oblastech](distribute-data-globally.md#key-benefits-of-global-distribution), může vaše aplikace číst a zapisovat do libovolné oblasti Azure. Pokud zakážete zápisy ve více oblastech, může vaše aplikace zapisovat data do jedné oblasti. <br/><br/> Povolte zápisy ve více oblastech, pokud očekáváte, že budete mít aktivní aktivní úlohu, která vyžaduje zápis s nízkou latencí v různých oblastech. Například úloha IOT, která zapisuje data do databáze na vysoce svazcích v různých oblastech. <br/><br/> Zápisy ve více oblastech garantuje 99,999% dostupnost čtení a zápisu. Zápisy ve více oblastech vyžadují při porovnání s jednou oblastí zápisu větší propustnost. Další informace najdete v článku [o tom, jak se ru liší od jednoduchých článků a oblastí vícenásobného zápisu](optimize-cost-regions.md) .|
|Celkem uložených dat (na oblast)|Celková odhadovaná data uložená v GB v jedné oblasti.|
|Velikost položky|Odhadovaná velikost datové položky (např. dokumentu) v rozsahu od 1 KB do 2 MB. |
|Počet čtení za sekundu na oblast|Počet očekávaných čtení za sekundu |
|Počet zápisů za sekundu na oblast|Počet očekávaných zápisů za sekundu |

Po vyplnění požadovaných podrobností vyberte **Vypočítat**. Karta **odhad nákladů** zobrazuje celkové náklady na úložiště a zřízenou propustnost. Kliknutím na odkaz **Zobrazit podrobnosti** na této kartě můžete získat rozpis propustnosti požadované pro požadavky na čtení a zápis. Pokaždé, když změníte hodnotu libovolného pole, vyberte **Vypočítat** a přepočítejte odhadované náklady. 

:::image type="content" source="./media/estimate-ru-with-capacity-planner/basic-mode.png" alt-text="Základní režim plánovače kapacity":::

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a>Odhad zajištěné propustnosti a nákladů pomocí rozšířeného režimu

Rozšířený režim vám umožní poskytnout další nastavení, která mají vliv na odhad RU/s. Pokud chcete použít tuto možnost, přejděte do [plánovače kapacity](https://cosmos.azure.com/capacitycalculator/) a přihlaste se k nástroji pomocí účtu, který používáte pro Azure. Možnost přihlášení je k dispozici v pravém horním rohu. 

Po přihlášení uvidíte další pole v porovnání s poli v základním režimu. Zadejte další parametry založené na vašich úlohách. 

|**Vstup**  |**Popis**  |
|---------|---------|
|Rozhraní API|Azure Cosmos DB je více modelů a služba multi-API. Pro nové úlohy vyberte rozhraní API SQL (Core). |
|Počet oblastí|Azure Cosmos DB je k dispozici ve všech oblastech Azure. Vyberte počet oblastí vyžadovaných pro vaše zatížení. K vašemu účtu Cosmos můžete přidružit libovolný počet oblastí. Další podrobnosti najdete v tématu [globální distribuce](distribute-data-globally.md) v Azure Cosmos DB.|
|Zápisy ve více oblastech|Pokud povolíte [zápisy ve více oblastech](distribute-data-globally.md#key-benefits-of-global-distribution), může vaše aplikace číst a zapisovat do libovolné oblasti Azure. Pokud zakážete zápisy ve více oblastech, může vaše aplikace zapisovat data do jedné oblasti. <br/><br/> Povolte zápisy ve více oblastech, pokud očekáváte, že budete mít aktivní aktivní úlohu, která vyžaduje zápis s nízkou latencí v různých oblastech. Například úloha IOT, která zapisuje data do databáze na vysoce svazcích v různých oblastech. <br/><br/> Zápisy ve více oblastech garantuje 99,999% dostupnost čtení a zápisu. Zápisy ve více oblastech vyžadují při porovnání s jednou oblastí zápisu větší propustnost. Další informace najdete v článku [o tom, jak se ru liší od jednoduchých článků a oblastí vícenásobného zápisu](optimize-cost-regions.md) .|
|Výchozí konzistence|Azure Cosmos DB podporuje 5 úrovní konzistence a umožňuje vývojářům vyrovnávat kompromisy mezi konzistencí, dostupností a kompromisy při latenci. Další informace najdete v článku o [úrovních konzistence](consistency-levels.md) . <br/><br/> Ve výchozím nastavení Azure Cosmos DB používá konzistenci relací, což zaručuje možnost číst vlastní zápisy v relaci. <br/><br/> Výběr silné nebo ohraničené neaktuálnosti bude vyžadovat zdvojnásobení požadovaných RU/s pro čtení, ve srovnání s relací, konzistentní předponou a konečnou konzistenci. Silná konzistence s zápisy ve více oblastech není podporovaná a automaticky se ve výchozím nastavení zapisuje zápisy do jedné oblasti s silnou konzistencí. |
|Zásada indexování|Ve výchozím nastavení Azure Cosmos DB [indexuje všechny vlastnosti](index-policy.md) ve všech položkách pro flexibilní a efektivní dotazy (mapuje se na zásady **automatického** indexování). <br/><br/> Pokud zvolíte **vypnuto**, žádná z vlastností nebude indexována. Výsledkem je nejnižší poplatek za zápis. Pokud očekáváte, že chcete provádět jenom [čtení bodů](/dotnet/api/microsoft.azure.cosmos.container.readitemasync) (vyhledávání hodnot klíčů) a/nebo zápisy a žádné dotazy, vyberte **vypnout** zásadu. <br/><br/> Vlastní zásady indexování umožňují zahrnout nebo vyloučit konkrétní vlastnosti z indexu pro nižší propustnost zápisu a úložiště. Další informace najdete v článcích o [zásadách indexování](index-overview.md) a [ukázkových zásadách indexování](how-to-manage-indexing-policy.md#indexing-policy-examples) .|
|Celkem uložených dat (na oblast)|Celková odhadovaná data uložená v GB v jedné oblasti.|
|Režim zatížení|Pokud je váš objem úloh konstantní, vyberte možnost **ustáleno** . <br/><br/> Pokud se objem úloh mění v čase, vyberte možnost **Proměnná** .  Například během určitého dne nebo v měsíci. <br/><br/> Pokud zvolíte možnost proměnné úlohy, jsou k dispozici následující nastavení:<ul><li>Procento času ve špičce: procento času v měsíci, ve kterém vaše zatížení vyžaduje nejvyšší propustnost (nejvyšší). <br/><br/> Pokud máte například úlohu s vysokou aktivitou během 9:00 – 18:00 v pracovní době, pak je procento času ve špičce: 45 hodin ve špičce/730 hodin/měsíc = ~ 6%.<br/><br/></li><li>Počet čtení za sekundu na oblast ve špičce – počet očekávaných čtení za sekundu ve špičce</li><li>Počet zápisů za sekundu na oblast ve špičce – počet zápisů očekávaných za sekundu ve špičce.</li><li>Počet čtení/s na oblast mimo špičku – počet operací čtení očekávaných za sekundu v době mimo špičku.</li><li>Počet zápisů za sekundu v oblasti mimo špičku – počet očekávaných zápisů za sekundu v době mimo špičku.</li></ul>V intervalech špičky a mimo špičku můžete své náklady optimalizovat díky [programovému škálování zřízené propustnosti](set-throughput.md#update-throughput-on-a-database-or-a-container) nahoru a dolů.|
|Velikost položky|Velikost datové položky (např. dokumentu) od 1 KB do 2 MB. <br/><br/>Můžete také **načíst ukázkový dokument (JSON)** pro přesnější odhad.<br/><br/>Pokud vaše úloha obsahuje více typů položek (s jiným obsahem JSON) ve stejném kontejneru, můžete nahrát několik dokumentů JSON a získat odhad. K přidání více ukázkových dokumentů JSON použijte tlačítko **Přidat novou položku** .|

K stažení souboru CSV obsahujícího aktuální odhad můžete použít také tlačítko **Uložit odhad** . 

:::image type="content" source="./media/estimate-ru-with-capacity-planner/advanced-mode.png" alt-text="Rozšířený režim plánovače kapacity":::

Ceny uvedené v Plánovači kapacity Azure Cosmos DB jsou odhadované na základě sazeb za veřejné ceny pro propustnost a úložiště. Všechny ceny jsou uvedeny v amerických dolarech. Pokud chcete zobrazit všechny sazby podle oblasti, přečtěte si [stránku s cenami Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) .  

## <a name="estimating-throughput-for-queries"></a>Odhad propustnosti pro dotazy

Kalkulačka kapacity Azure Cosmos předpokládá čtení bodů (čtení jedné položky, třeba dokumentu, podle ID a hodnoty klíče oddílu) a zápisy pro úlohu. Pokud chcete odhadnout propustnost potřebnou pro dotazy, spusťte dotaz na zástupce datové sady v kontejneru Cosmos a [Získejte poplatek za ru](find-request-unit-charge.md). Vynásobte poplatek za RU počtem dotazů, které předpokládáte za běhu za sekundu, abyste získali celkové požadavky na RU/s. 

Například pokud vaše úloha vyžaduje dotaz, ``SELECT * FROM c WHERE c.id = 'Alice'`` který běží 100 krát za sekundu, a poplatek za dotaz je 10 ru, budete pro poskytování těchto požadavků potřebovat 100 dotaz/s * 10 ru/Query = 1000 ru/s. Přidejte tyto RU/s na RU/s potřebné pro jakékoli čtení nebo zápisy, které se ve vašem zatížení děje.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [cenovém modelu Azure Cosmos DB](how-pricing-works.md).
* Vytvořte nový [účet Cosmos, databázi a kontejner](create-cosmosdb-resources-portal.md).
* Naučte se [optimalizovat zřízené náklady na propustnost](optimize-cost-throughput.md).
* Naučte se [optimalizovat náklady pomocí rezervované kapacity](cosmos-db-reserved-capacity.md).
---
title: Migrace dat do SQL Data Warehouse | Dokumentace Microsoftu
description: Tipy pro migraci dat do služby Azure SQL Data Warehouse pro vývoj řešení.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: a09037f2e33d87446696dd11477c4e8b45af4187
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474169"
---
# <a name="migrate-your-data"></a>Migrace dat
Data můžete přesunout z různých zdrojů do služby SQL Data Warehouse pomocí různých nástrojů.  Kopírovat ADF, SSIS a bcp můžete použít k dosažení tohoto cíle. Ale jako objem data zvýšení byste uvažovat o rozdělení proces migrace dat do kroků. To nabízí příležitosti k optimalizaci každý krok, výkon i odolnost zajistit hladký průběh data migrace.

Tento článek popisuje první jednoduché migrační scénáře ADF kopírovat, SSIS a bcp. To potom prozkoumat trochu hlouběji jak se dá optimalizovat migrace.

## <a name="azure-data-factory-adf-copy"></a>Azure Data Factory (ADF) kopírování
[Kopírování ADF] [ ADF Copy] je součástí [Azure Data Factory][Azure Data Factory]. Kopírování ADF můžete exportovat data do plochých souborů, které se nacházejí na místní úložiště, vzdálené plochých souborů uložených ve službě Azure blob storage nebo přímo do SQL Data Warehouse.

Pokud vaše data se spustí v ploché soubory, pak musíte nejdřív přeneste do Azure storage blobu před zahájením zatížení do SQL Data Warehouse. Jakmile se data se přenesou do Azure blob storage můžete používat [ADF kopírování] [ ADF Copy] znovu k nabízení dat do SQL Data Warehouse.

PolyBase nabízí také možnost vysoce výkonné pro načítání dat. Nicméně to znamená pomocí dvou nástrojů místo jednoho. Pokud potřebujete nejlepšího výkonu dosáhnete pak pomocí PolyBase. Pokud chcete, aby zkušenost jediného nástroje (a data nejsou masivní) ADF je odpověď.


> 
> 

Přejděte na následující článek pro některé skvělé [ukázky ADF] [ukázky ADF].

## <a name="integration-services"></a>Integrační služby
Integration Services (SSIS) je výkonný a flexibilní extrakce, transformace a načítání (ETL) nástroj, který podporuje komplexní pracovní postupy, transformace dat a několik možností, jak načítá data. Pomocí služby SSIS jednoduše přenést data do Azure nebo v rámci širší migrace.

> [!NOTE]
> Služby SSIS, můžete exportovat na UTF-8 bez značky pořadí bajtů v souboru. Ke konfiguraci, musíte nejdřív použijte odvozené sloupec převést data znak v toku dat použít znakovou stránku 65001 UTF-8. Po převedení sloupce zapsat data do plochých souborů cílový adaptér, zajištění, že 65001 také byla vybrána jako znakovou stránku souboru.
> 
> 

SSIS připojuje k SQL Data Warehouse, stejně jako se bude připojovat k nasazení systému SQL Server. Připojení bude však nutné používat Správce připojení ADO.NET. Můžete také byste měli věnovat pozornost "použít příkaz bulk insert-li k dispozici" Konfigurace nastavení pro maximalizaci propustnosti. Najdete [ADO.NET cílový adaptér] [ ADO.NET destination adapter] článku se dozvíte další informace o této vlastnosti

> [!NOTE]
> Připojení k Azure SQL Data Warehouse pomocí OLEDB není podporováno.
> 
> 

Kromě toho je vždy možnost, že balíček může selhat v důsledku omezení šířky pásma nebo sítě problémy. Návrh balíčků tak, že lze obnovit bodě selhání, bez opakování pracovat, dokončeno před selháním.

Další informace naleznete [dokumentaci ke službě SSIS][SSIS documentation].

## <a name="bcp"></a>bcp
Nástroj BCP představuje nástroj příkazového řádku, který je navržený pro export a import dat plochého souboru. Některé transformace mohou probíhat během exportu data. Provádět jednoduché transformace pomocí dotazu vybírat a transformovat data. Po exportu, plochých souborů je pak možné načíst přímo do cílové databáze SQL Data Warehouse.

> [!NOTE]
> Často je vhodné zapouzdřit transformace používá při exportu dat v zobrazení ve zdrojovém systému. Tím se zajistí, že se uchovávají logiku a proces je opakovatelné.
> 
> 

Výhody bcp jsou:

* Jednoduchost. BCP příkazy se dají snadno sestavit a spustit
* Proces znovu spustitelná zatížení. Jednou exportované zatížení může být libovolný počet pokusů o spuštění

Omezení nástroje bcp jsou:

* BCP spolupracuje s tabulkovými pouze plochých souborů. Nefunguje pro soubory, jako jsou xml nebo JSON
* Funkce pro transformaci dat jsou omezeny na fázi exportu a jsou jednoduché ze své podstaty
* BCP nebyl upraven bude robustní při načítání dat přes internet. Nestability sítě může způsobit chybu zatížení.
* využívá BCP schématu byla vyžadována jeho přítomnost v cílové databázi před načtením

Další informace najdete v tématu [načtení dat do SQL Data Warehouse pomocí bcp][Use bcp to load data into SQL Data Warehouse].

## <a name="optimizing-data-migration"></a>Optimalizace migrace dat
Proces migrace dat SQLDW můžete efektivně rozdělené do tří samostatných kroků:

1. Export zdroje dat.
2. Přenos dat do Azure
3. Načtení do cílové databáze SQLDW

Každý krok lze jednotlivě optimalizovat vytvořit robustní, znovu spustitelná a odolná proti selháním migrace proces, který maximalizuje výkon v každém kroku.

## <a name="optimizing-data-load"></a>Optimalizace načítání dat
Hledání v těchto na chvíli; v opačném pořadí je nejrychlejší způsob, jak načíst data pomocí PolyBase. Optimalizace pro proces načítání PolyBase umístí požadavky v předchozích krocích, je nejvhodnější pro lepší vysvětlení předem. Jsou to tyto:

1. Kódování souborů dat
2. Formát datových souborů
3. Umístění dat souborů

### <a name="encoding"></a>Kódování
PolyBase se vyžaduje datové soubory UTF-8 nebo UTF-16FE. 



### <a name="format-of-data-files"></a>Formát datových souborů
PolyBase zmocňuje ukončovací znak pevné řádku \n nebo znaku nového řádku. Datové soubory musí odpovídat tomuto standardu. Nejsou k dispozici nějaká omezení ukončovací znaky řetězce nebo sloupec.

Budete muset definovat každý sloupec v souboru jako součást vaší externí tabulky v PolyBase. Ujistěte se, že se všechny sloupce exportované vyžadují a že typy odpovídají povinné standardy.

Přejděte zpět [migrujte svoje schéma] najdete podrobnosti o podporovaných datových typů.

### <a name="location-of-data-files"></a>Umístění dat souborů
SQL Data Warehouse použije PolyBase k načítání dat z Azure Blob Storage výhradně. V důsledku toho data musí byly nejprve převedeny do úložiště objektů blob.

## <a name="optimizing-data-transfer"></a>Optimalizace přenos dat
Jedna z částí nejpomalejší migrace dat je přenos dat do Azure. Nejen šířky pásma sítě může být problém, ale také spolehlivost sítě může vážně bránit pokroku. Ve výchozím nastavení migrace dat do Azure je tak, aby byly rozumně pravděpodobně pravděpodobnost výskytu chyb přenosu přes internet. Tyto chyby však může vyžadovat opětovné odeslání, ať už zcela nebo zčásti data.

Naštěstí máte několik možností, jak ke zlepšení rychlosti a odolnosti tohoto procesu:

### <a name="expressrouteexpressroute"></a>[ExpressRoute][ExpressRoute]
Možná budete chtít zvážit použití [ExpressRoute] [ ExpressRoute] urychlit přenos. [ExpressRoute] [ ExpressRoute] vám poskytne vytvořené soukromé připojení k Azure tak připojení neprochází přes veřejný internet. Toto je v žádném smyslu povinný krok. Ale to se při odesílání dat do Azure z místního zlepšit propustnost nebo společném umístění.

Mezi výhody používání [ExpressRoute] [ ExpressRoute] jsou:

1. Zvýšení spolehlivosti
2. Vyšší rychlost sítě
3. Nižší latence sítě
4. vyšší úroveň zabezpečení sítě

[ExpressRoute] [ ExpressRoute] je přínosné pro řadu scénářů, ne jenom migrace.

Zajímá vás to? Další informace a ceny prosím najdete [dokumentace ke službě ExpressRoute][ExpressRoute documentation].

### <a name="azure-import-and-export-service"></a>Azure Import a Export služby
Azure umožňuje importovat a exportovat služby je proces přenosu dat určené pro velké (GB ++), kterým masivní (TB ++) přenosů dat do Azure. Zahrnuje zápisu dat na disky a přesouvání do datového centra Azure. Obsah disku se pak načtou do objektů BLOB Azure Storage za vás.

Souhrnný přehled procesu exportu importovat vypadá takto:

1. Konfigurace kontejneru úložiště objektů Blob v Azure pro příjem dat
2. Export dat do místního úložiště
3. Zkopírovat data do 3,5 SATA II/III pevných disků v nástroji [Azure Import/Export]
4. Vytvořit úlohu importu pomocí Azure Import a Export službu poskytující soubory deníku vytvořený nástrojem [Azure Import/Export]
5. Disky odešlete vaše jmenovaný datového centra Azure
6. Vaše data se přenáší do kontejneru úložiště objektů Blob v Azure
7. Načtení dat do SQLDW při použití technologie PolyBase

### <a name="azcopyazcopy-utility"></a>[AZCopy][AZCopy] nástroje
[AZCopy][AZCopy] nástroj je skvělým nástrojem k získávají se vaše data přenášená do objektů BLOB Azure Storage. Je určená pro malé (MB ++) na velmi velké přenosů dat (GB ++). [AZCopy] byl navržen tak, aby poskytují dobrou odolné propustnost při přenosu dat do Azure a proto je skvělou volbou pro krok převodu dat. Jednou přenášená data pomocí PolyBase do SQL Data Warehouse můžete načíst. AZCopy taky můžete začlenit do vašich balíčků služby SSIS pomocí úlohu "Spustit proces".

Použití nástroje AZCopy je nejprve potřeba stáhnout a nainstalovat ho. Je [produkční verzi] [ production version] a [verze preview] [ preview version] k dispozici.

Pokud chcete nahrát soubor ze systému souborů budete potřebovat podobná té následující příkaz:

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

Může být uveden proces vysoké úrovně:

1. Konfigurovat kontejner objektů blob úložiště Azure pro příjem dat
2. Export dat do místního úložiště
3. AZCopy vaše data v kontejneru úložiště objektů Blob v Azure
4. Načtení dat do SQL Data Warehouse pomocí PolyBase

Úplnou dokumentaci k dispozici: [AZCopy][AZCopy].

## <a name="optimizing-data-export"></a>Optimalizace export dat
Kromě zajištění, že export odpovídá požadavky stanovené polybase můžete také vyhledat exportovat data, která mají vylepšit proces dál optimalizovat.



### <a name="data-compression"></a>Komprese dat
PolyBase čte data komprimovaná gzip. Pokud je to možné, aby se komprimoval data souborů gzip bude minimalizovat objem dat se vloženo přes síť.

### <a name="multiple-files"></a>Více souborů
Rozdělení velké tabulky do několika souborů nejenom pomáhá zlepšit rychlost exportu, také pomáhá s re-startability přenos a celkové správy dat jednou ve službě Azure blob storage. Jedním z mnoha nabízí užitečné funkce PolyBase je, že bude číst všechny soubory ve složce a ji považovat za jednu tabulku. Proto je vhodné izolovat soubory pro každou tabulku do samostatné složky.

Technologie PolyBase také podporoval funkci označovanou jako "procházení složky rekurzivní". Tuto funkci můžete použít k dalšímu vylepšení organizace data exportovaná zlepšit vaše data správy.

Další informace o načítání dat pomocí PolyBase, naleznete v tématu [použití PolyBase k načítání dat do SQL Data Warehouse][Use PolyBase to load data into SQL Data Warehouse].

## <a name="next-steps"></a>Další postup
Další informace o migraci najdete v tématu [migrace řešení do SQL Data Warehouse][Migrate your solution to SQL Data Warehouse].
Další tipy pro vývoj najdete v části [přehled vývoje][development overview].

<!--Image references-->

<!--Article references-->
[AzCopy]: ../storage/common/storage-use-azcopy.md
[ADF Copy]: ../data-factory/copy-activity-overview.md 
[ADF Copy examples]: ../data-factory/quickstart-create-data-factory-dot-net.md
[development overview]: sql-data-warehouse-overview-develop.md
[migrujte svoje schéma]: sql-data-warehouse-migrate-schema.md
[Migrate your solution to SQL Data Warehouse]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Use bcp to load data into SQL Data Warehouse]: /sql/tools/bcp-utility
[Use PolyBase to load data into SQL Data Warehouse]: load-data-wideworldimportersdw.md


<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory]: http://azure.microsoft.com/services/data-factory/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[ExpressRoute documentation]: http://azure.microsoft.com/documentation/services/expressroute/

[production version]: http://aka.ms/downloadazcopy/
[preview version]: http://aka.ms/downloadazcopypr/
[ADO.NET destination adapter]: https://msdn.microsoft.com/library/bb934041.aspx
[SSIS documentation]: https://msdn.microsoft.com/library/ms141026.aspx

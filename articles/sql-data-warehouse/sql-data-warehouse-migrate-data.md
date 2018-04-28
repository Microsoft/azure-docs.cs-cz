---
title: Migrace dat do SQL Data Warehouse | Microsoft Docs
description: Tipy pro migraci dat do Azure SQL Data Warehouse na vývoj řešení.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: ca467ae5fbe784399e4e046c47c920ff7dec638e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="migrate-your-data"></a>Migrace dat
Data lze přesunout z různých zdrojů do SQL Data Warehouse pomocí různých nástrojů.  Kopírování ADF, SSIS a bcp můžete použít k dosažení tohoto cíle. Jako velikost dat zvyšuje však měli myslíte o rozdělení proces migrace dat do kroků. To nabízí možnost optimalizovat každý krok pro výkon i pro odolnost k zajištění smooth dat migrace.

Tento článek popisuje nejprve jednoduché migrační scénáře ADF Copy, SSIS a bcp. Následně vypadá trochu hlouběji do jak lze optimalizovat migrace.

## <a name="azure-data-factory-adf-copy"></a>Azure Data Factory (ADF) kopie
[Kopírování ADF] [ ADF Copy] je součástí [Azure Data Factory][Azure Data Factory]. Kopírování ADF můžete exportovat data do plochých souborů, které se nacházejí v místním úložišti, do vzdáleného plochých souborů uchovávat v Azure blob storage nebo přímo do SQL Data Warehouse.

Pokud vaše data se spustí v plochých souborů, pak budete nejprve muset před zahájením zatížení ho přenést do objektu blob úložiště Azure do SQL Data Warehouse. Jakmile se přenáší data do úložiště objektů blob v Azure můžete použít [ADF kopie] [ ADF Copy] znovu tak, aby nabízel data do SQL Data Warehouse.

PolyBase také nabízí možnost vysoce výkonné načítání data. Ale, která znamená, pomocí dvou nástrojů místo jeden. Pokud třeba nejlepší výkon použijte PolyBase. Pokud chcete prostředí jednotný nástroj (a data nejsou masivní) ADF je vaše odpověď.


> 
> 

Přejděte přes v následujícím článku pro některé dobře [ADF ukázky][ADF samples].

## <a name="integration-services"></a>Integrační služby
Integration Services (SSIS) je výkonný a flexibilní extrahovat transformace a načítání (ETL) nástroj, který podporuje komplexní pracovní postupy, transformaci dat a několik možností načítání data. Pomocí služby SSIS pouze k přenosu dat do Azure nebo v rámci širší migrace.

> [!NOTE]
> SSIS můžete exportovat do UTF-8 bez značka pořadí bajtů v souboru. Ke konfiguraci to musíte nejdřív komponentu odvozených sloupců použijte k převedení dat znak v toku dat pomocí 65001 kódu stránky UTF-8. Jakmile sloupce, které byly převedeny, zapsat data do cílové adaptér plochý soubor zajistíte, že 65001 také byla vybrána jako znaková stránka pro soubor.
> 
> 

SSIS se připojuje k SQL Data Warehouse stejně, jako by se připojit k nasazení systému SQL Server. Připojení však bude muset používat Správce připojení ADO.NET. Můžete také dbát na nakonfigurovat "použijte příkaz bulk insert Pokud je k dispozici" nastavení Maximalizovat propustnost. Podrobnosti najdete [ADO.NET cílový adaptér] [ ADO.NET destination adapter] článku Další informace o této vlastnosti

> [!NOTE]
> Připojení k Azure SQL Data Warehouse pomocí OLEDB není podporováno.
> 
> 

Kromě toho je vždy možnost, že balíček se pravděpodobně nezdaří z důvodu omezení nebo síťové problémy. Návrh balíčky, takže se můžete pokračovat v místě selhání bez opakovaného fungovat dokončení před selháním.

Další informace naleznete [SSIS dokumentaci][SSIS documentation].

## <a name="bcp"></a>bcp
BCP je nástroj příkazového řádku, která je určená pro export a import dat plochý soubor. Některé transformace může probíhat během exportu dat. K provedení jednoduché transformace pomocí dotazu a vyberte transformovat data. Po exportu plochých souborů pak dají načíst přímo do cílové databáze SQL Data Warehouse.

> [!NOTE]
> Často je vhodné pro zapouzdření transformace použít při exportu dat v zobrazení ve zdrojovém systému. To zajišťuje, že se uchovávají logiku a proces opakovatelných.
> 
> 

Výhody bcp jsou:

* Jednoduchost. příkazy BCP jsou jednoduché sestavení a spuštění
* Znovu spustit zatížení proces. Jednou exportovaný zatížení může být provedeny v libovolném počtu

Omezení bcp jsou:

* BCP pracuje s tabulce pouze plochých souborů. Nefunguje s soubory, jako jsou xml nebo JSON
* Funkce transformace dat jsou omezeny na fázi exportu a jsou jednoduché ve své podstatě
* BCP nebyla přizpůsobena být robustní při načítání dat přes internet. Nestability sítě může způsobit chybu zatížení.
* BCP spoléhá na schéma se nachází v cílové databázi před zatížení

Další informace najdete v tématu [pomocí bcp k načtení dat do SQL Data Warehouse][Use bcp to load data into SQL Data Warehouse].

## <a name="optimizing-data-migration"></a>Optimalizace migrace dat
Proces migrace dat SQLDW můžete efektivně rozdělit na tři samostatné kroky:

1. Export zdroje dat
2. Přenos dat do Azure
3. Načtení do cílové databáze SQLDW

Každý krok může jednotlivě optimalizovaný na vytváření robustní, znovu spustit a odolné migrace proces, který maximalizuje výkon při každém kroku.

## <a name="optimizing-data-load"></a>Optimalizace načtení dat
Prohlížení tyto v obráceném pořadí na chvíli; nejrychlejší způsob, jak načíst data je prostřednictvím PolyBase. Optimalizace pro proces zatížení PolyBase umístí požadavky na předchozí kroky proto je vhodné pro lepší vysvětlení předem. Jsou to tyto:

1. Kódování dat souborů
2. Formátu dat souborů
3. Umístění datových souborů

### <a name="encoding"></a>Encoding
PolyBase vyžaduje datové soubory ve formátu UTF-8 nebo UTF-16FE. 



### <a name="format-of-data-files"></a>Formátu dat souborů
PolyBase vyžaduje pevnou řádek ukončovací \n nebo nový řádek. Tento standard musí odpovídat datové soubory. Nejsou k dispozici žádné omezení konců řetězec nebo sloupec.

Je nutné definovat všechny sloupce v souboru v rámci vaší externí tabulky v PolyBase. Ujistěte se, že všechny sloupce exportovaný jsou povinné a že typy v souladu s požadované standardy.

Naleznete zpět [migrovat schéma] najdete v článku o podporované datové typy.

### <a name="location-of-data-files"></a>Umístění datových souborů
SQL Data Warehouse PolyBase používá k načtení dat z Azure Blob Storage výhradně. V důsledku toho data musí nejprve přenesení do úložiště objektů blob.

## <a name="optimizing-data-transfer"></a>Optimalizace přenos dat
Jedna z částí nejpomalejší migrace dat je přenos dat do Azure. Pouze šířku pásma sítě může být problém, ale také spolehlivost sítě může být vážnou překážkou průběh. Ve výchozím nastavení migraci dat do Azure se tak pravděpodobnost chyby přenosu, k nimž jsou to bude přiměřeně pravděpodobně přes internet. Tyto chyby však může vyžadovat opětovné odeslání celé nebo částečně data.

Naštěstí máte několik možností k dosažení vyšší rychlosti a odolnost proti tohoto procesu:

### <a name="expressrouteexpressroute"></a>[ExpressRoute][ExpressRoute]
Možná budete chtít zvážit použití [ExpressRoute] [ ExpressRoute] pro urychlení přenosu. [ExpressRoute] [ ExpressRoute] vám poskytne vytvořené privátní připojení do Azure, připojení nepřenášejí prostřednictvím veřejného Internetu. Toto je rozhodně není povinný krok. Ale ho bude při předání dat do Azure z místního zlepšit propustnost nebo společném umístění.

Výhody použití [ExpressRoute] [ ExpressRoute] jsou:

1. Větší spolehlivost
2. Vyšší rychlost sítě
3. Nižší latenci sítě
4. vyšší zabezpečení sítě

[ExpressRoute] [ ExpressRoute] je výhodné pro různé scénáře; nejen migrace.

Zajímá vás to? Další informace a ceny prosím naleznete [dokumentace ExpressRoute][ExpressRoute documentation].

### <a name="azure-import-and-export-service"></a>Azure Import a Export služby
Azure Import a Export služby je proces přenosu dat pro velké (GB ++) umožňuje masivní (TB ++) přenosů dat do Azure. Se týká zápisu dat na disky a přesouvání je datové centrum Azure. Obsah disku se pak načíst do Azure Storage Blobs vaším jménem.

Souhrnné zobrazení procesu importu export vypadá takto:

1. Konfigurace kontejner úložiště objektů Blob Azure přijmout data
2. Exportovat data do místního úložiště
3. Zkopírovat data do 3,5 SATA II/III pevných disků v nástroji [Azure Import/Export]
4. Vytvoření úlohy importu pomocí Azure Import a Export služba poskytující soubory deníku vytvořený nástrojem [Azure Import/Export]
5. Dodávat disky určenou Azure datové centrum
6. Vaše data se přenáší do vašeho kontejneru Azure Blob Storage
7. Načíst data do SQLDW pomocí PolyBase

### <a name="azcopyazcopy-utility"></a>[AZCopy][AZCopy] nástroj
[AZCopy][AZCopy] nástroj je skvělý nástroj pro získávání dat přenesených do objektů BLOB služby Azure Storage. Pro velmi velký přenos dat (GB ++) je určený pro malé (MB ++). [AZCopy] byl navržen tak, aby poskytují dobrý odolné propustnost při přenosu dat do Azure a tak je je služba skvělou volbou pro krok přenos dat. Jednou přenášená můžete načíst data do SQL Data Warehouse pomocí PolyBase. AZCopy taky můžete začlenit do vaší služby SSIS balíčky pomocí "Spustit proces" úlohy.

Použití nástroje AZCopy nejprve musíte stáhnout a nainstalovat ji. Je [produkční verzi] [ production version] a [verze preview] [ preview version] k dispozici.

Nahrát soubor ze systému souborů budete potřebovat podobná té následující příkaz:

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

Proces vysoké úrovně souhrn může být:

1. Konfigurace kontejner objektu blob úložiště Azure pro příjem dat
2. Exportovat data do místního úložiště
3. AZCopy vaše data v kontejneru Azure Blob Storage
4. Načítání dat do SQL Data Warehouse pomocí PolyBase

Úplné dokumentaci k dispozici: [AZCopy][AZCopy].

## <a name="optimizing-data-export"></a>Optimalizace export dat
Kromě zajištění, že export splňuje požadavky nastíněny polybase můžete také hledat optimalizovat export data, která mají vylepšit další proces.



### <a name="data-compression"></a>Komprese dat
PolyBase čte data komprimované gzip. Pokud budete moci komprese dat, aby se soubory gzip se minimalizuje množství dat, které se instaluje přes síť.

### <a name="multiple-files"></a>Více souborů
Rozdělení rozsáhlé tabulky do několika souborů nejen pomáhá zvýšit rychlost exportu, také pomáhá s re-startability přenos a celkové možnosti správy dat jednou v Azure blob storage. Jedním z mnoha dobrý funkcí PolyBase je, že bude číst všechny soubory do složky a s nimi zacházet jako jedna tabulka. Proto je vhodné izolovat soubory pro každou tabulku do vlastní složky.

PolyBase také podporuje funkci označuje jako "rekurzivní složky traversal". Tato funkce slouží k dalšímu vylepšení útoků organizace data exportovaná ke zlepšení vaší správy dat.

Další informace o načtení dat pomocí funkce PolyBase, najdete v části [PolyBase používá k načtení dat do SQL Data Warehouse][Use PolyBase to load data into SQL Data Warehouse].

## <a name="next-steps"></a>Další postup
Další informace o migraci najdete v tématu [migrace vašeho řešení do SQL Data Warehouse][Migrate your solution to SQL Data Warehouse].
Další tipy pro vývoj, najdete v části [přehled vývoje][development overview].

<!--Image references-->

<!--Article references-->
[AZCopy]: ../storage/common/storage-use-azcopy.md
[ADF Copy]: ../data-factory/v1/data-factory-data-movement-activities.md 
[ADF samples]: ../data-factory/v1/data-factory-samples.md
[ADF Copy examples]: ../data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio.md
[development overview]: sql-data-warehouse-overview-develop.md
[migrovat schéma]: sql-data-warehouse-migrate-schema.md
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

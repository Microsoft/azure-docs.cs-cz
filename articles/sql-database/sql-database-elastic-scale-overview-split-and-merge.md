---
title: Přesun dat mezi instancemi cloudu databáze | Microsoft Docs
description: Vysvětluje, jak pracovat s horizontálních oddílů a přesun dat prostřednictvím samoobslužné hostované služby pomocí rozhraní API pro elastické databáze.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: c70279bd52f7b0b0e0cbc27742eca93d9af5e630
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Přesun dat mezi cloudovými databázemi s horizontálním navýšením kapacity
Pokud jste Software jako služba developer a najednou aplikace zde nevyskytlo strávíte vyžádání, budete muset zohlednit růst. Proto můžete přidat další databáze (horizontálních oddílů). Jak opětovné distribuci dat do nové databáze a to bez přerušení integritu dat? Použití **nástroji pro sloučení rozdělení** pro přesun dat z omezené databází do nové databáze.  

Nástroj pro rozdělení sloučení běží jako Azure webové služby. Správce nebo vývojáře používá nástroj pro přesun shardlets (data z horizontálního oddílu) mezi různých databází (horizontálních oddílů). Nástroj používá horizontálního oddílu mapy správu zachovat metadata databáze služby, a zajistit konzistentní mapování.

![Přehled][1]

## <a name="download"></a>Ke stažení
[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Dokumentace
1. [Kurz nástroje elastické databáze rozdělení sloučení](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
2. [Konfigurace zabezpečení rozdělení sloučení](sql-database-elastic-scale-split-merge-security-configuration.md)
3. [Aspekty zabezpečení rozdělení sloučení](sql-database-elastic-scale-split-merge-security-configuration.md)
4. [Správa mapování horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md)
5. [Migrace existujících databází pro horizontální navýšení kapacity](sql-database-elastic-convert-to-use-elastic-tools.md)
6. [Nástroje elastické databáze](sql-database-elastic-scale-introduction.md)
7. [Glosář nástroje elastické databáze](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Proč používat nástroj pro rozdělení sloučení?
**Flexibilita**

Aplikace potřebují k roztahování flexibilně hranicemi služby jedné databáze Azure SQL DB. Pomocí nástroje pro přesun dat v závislosti na potřebě nové databáze a přitom zachovat integritu.

**Rozdělení růst** 

Je potřeba zvýšit celkové kapacity pro zpracování výrazný růst. Uděláte to tak vytvořte další kapacitu horizontálního dělení dat a rozděluje mezi přírůstkově více databází, dokud se splnění požadavků na kapacitu. Toto je typickým příkladem 'rozdělení' funkce. 

**Sloučení zmenšení**

Kapacita musí zmenšit z důvodu sezónní povahy firmy. Tento nástroj umožňuje snižovat do menšího počtu jednotek škálování, když obchodní zpomalí. Funkce 'sloučení' ve službě rozdělení sloučení elastické škálování popisuje tento požadavek. 

**Spravovat hotspotům přesunutím shardlets**

S více klienty na databázi přidělení shardlets k horizontálních oddílů může vést k kapacity kritická místa na některé horizontálních oddílů. To vyžaduje znovu přidělování shardlets nebo přesunutím zaneprázdněn shardlets do nové nebo méně využívané horizontálních oddílů. 

## <a name="concepts--key-features"></a>Koncepty & klíčové funkce
**Zákazník hostované služby**

Rozdělení sloučení je dodávána jako zákazník hostované služby. Musíte nasadit a hostitelem služby v rámci vašeho předplatného Microsoft Azure. Balíček, který si stáhnout z NuGet obsahuje šablonu konfigurace k dokončení s informacemi pro konkrétní nasazení. Najdete v článku [rozdělení sloučení kurzu](sql-database-elastic-scale-configure-deploy-split-and-merge.md) podrobnosti. Vzhledem k tomu, že služba bude spuštěna ve vašem předplatném Azure, můžete řídit a nakonfigurovat většinu aspektů zabezpečení služby. Výchozí šablona obsahuje možnosti pro konfiguraci protokolu SSL, ověřování na základě certifikátu klienta, šifrování pro uložené přihlašovací údaje, DoS ochrana a omezení IP adres. Další informace o aspektech zabezpečení najdete v následujícím dokumentu [konfigurace zabezpečení rozdělení sloučení](sql-database-elastic-scale-split-merge-security-configuration.md).

Výchozí hodnota nasadit service běží s jeden pracovní proces a jednu webovou roli. Každý virtuální počítač A1 velikost používá v Azure Cloud Services. Když tato nastavení nelze upravit, při nasazení balíčku, je možné je změnit po úspěšné nasazení v rámci spuštěné cloudové služby (prostřednictvím portálu Azure). Všimněte si, že role pracovního procesu musí být nakonfigurované více než jednu instanci technických důvodů. 

**Mapa integrace horizontálního oddílu**

Rozdělení sloučení služba komunikuje s mapy horizontálního oddílu aplikace. Pokud používáte službu rozdělení sloučení k rozdělení nebo sloučení rozsahy nebo přesunout shardlets mezi horizontálních oddílů, služba automaticky udržuje mapy horizontálního oddílu aktuální. Uděláte to tak, služba připojuje k databázi horizontálního oddílu mapa správce aplikace a udržuje rozsahy a mapování průběh rozdělení nebo sloučení nebo přesunutí požadavky. Tím se zajistí, že mapy horizontálního oddílu vždy uvede aktuální zobrazení při operace sloučení rozdělení se děje. Rozdělení, sloučení a shardlet přesun operace jsou implementované přesunutím dávky shardlets z horizontálního oddílu zdrojové do cílové horizontálního oddílu. Během operace přesunu shardlet shardlets vztahují aktuální dávku jsou označeny jako offline v mapě horizontálního oddílu a nejsou k dispozici pro závislé na data směrování připojení pomocí **OpenConnectionForKey** rozhraní API. 

**Konzistentní shardlet připojení**

Při spuštění přesun dat pro novou dávku shardlets všechny zadané mapování horizontálních závislé na data směrování připojení k horizontálního oddílu ukládání shardlet budou borné a následné připojení z mapování horizontálních rozhraní API pro tyto shardlets blokují přesun dat v průběhu aby se zabránilo nekonzistence. Připojení k jiné shardlets na stejné ID horizontálního oddílu také získat ukončeny, ale bude úspěšné znovu okamžitě při opakování. Po přesunutí dávky shardlets jsou označeny online znovu pro cíl horizontálních a zdrojová data se odebral ze zdroje horizontálního oddílu. Služba projde tyto kroky pro každou dávku, dokud všechny shardlets byly přesunuty. To povede k několik operací kill připojení v průběhu operace dokončit rozdělení nebo sloučení nebo přesunutí.  

**Správa shardlet dostupnosti**

Omezení připojení ukončení aktuální dávku shardlets, jak je popsáno výše omezuje oboru nedostupnosti na jednu dávku shardlets najednou. Toto je upřednostňovaný přes přístup kde dokončení horizontálního oddílu by zůstat offline pro všechny její shardlets v průběhu operace rozdělení nebo sloučení. Velikost dávky, definován jako počet jedinečných shardlets přesunout současně, je parametr konfigurace. Může být definováno pro každou operaci rozdělení a merge v závislosti na potřebách dostupnost a výkon aplikace. Všimněte si, že rozsah, který se zamykají v mapě horizontálního oddílu může být větší než zadaná velikost dávky. To je proto službu vybere velikost rozsah tak, aby skutečný počet horizontálního dělení hodnoty klíče v datech přibližně odpovídá velikost dávky. To je důležité si pamatovat konkrétně pro klíče řídce vyplněná horizontálního dělení. 

**Metadata úložiště**

Služba rozdělení sloučení používá databázi k udržování její stav a udržování protokolů při zpracování požadavku. Uživatel vytvoří tato databáze v rámci svého předplatného a poskytuje připojovací řetězec pro ho v konfiguračním souboru pro nasazení služby. Správci z organizace uživatele taky můžou připojovat k této databázi můžete zhodnotit průběh žádosti a prozkoumat podrobné informace o potenciální chyby.

**Sledování horizontálního dělení**

Službu rozdělení sloučení rozlišuje (1) horizontálně dělené tabulky, (2) referenční tabulky a (3) normální tabulky. Sémantika rozdělení nebo sloučení nebo přesunutí operace závisí na typu tabulky používá a jsou definovány takto: 

* **Horizontálně dělené tabulky**: operace přesunutí, sloučení a rozdělení přesunout shardlets ze zdrojového do cílového horizontálního oddílu. Po úspěšném dokončení celkové požadavku těchto shardlets již neexistují ve zdroji. Všimněte si, že cílové tabulky musí existovat v cílové horizontálního oddílu a nesmí obsahovat data v rozsahu cílového před zpracování operace. 
* **Referenční tabulky**: referenční tabulky, rozdělení, sloučení a přesunout operations zkopírovat data ze zdrojové do cílové horizontálního oddílu. Upozorňujeme však, že žádné změny dojít na horizontálních cíl pro danou tabulku, pokud všechny řádky, se již nachází v této tabulce na cíli. V tabulce musí být prázdná pro všechny operace referenční tabulky kopie zpracovat.
* **Ostatní tabulky**: ostatní tabulky mohou existovat na buď zdroj nebo cíl operace rozdělení a sloučení. Službu rozdělení sloučení ignoruje tyto tabulky pro všechny přesuny dat nebo operace kopírování. Upozorňujeme však, že může narušovat tyto operace v případě omezení.

Informace na odkaz oproti horizontálně dělené tabulky jsou poskytovány **SchemaInfo** rozhraní API na mapě horizontálního oddílu. Následující příklad ukazuje použití tato rozhraní API na danou horizontálních mapy manager objekt smm: 

    // Create the schema annotations 
    SchemaInfo schemaInfo = new SchemaInfo(); 

    // Reference tables 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region")); 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation")); 

    // Sharded tables 
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY")); 
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY")); 

    // Publish 
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo); 

Region' tabulky' a 'výhod, jsou definovány jako referenční tabulky a budou zkopírovány s operacemi rozdělení nebo sloučení nebo přesunutí. "zákazník" a "objednávky, pak jsou definovány jako horizontálně dělené tabulky. C_CUSTKEY a O_CUSTKEY slouží jako klíč horizontálního dělení. 

**Referenční integrita**

Službu rozdělení sloučení analyzuje závislosti mezi tabulkami a příprava operací pro přesun referenční tabulky a shardlets pomocí relace cizích klíčů primární klíč. Obecně platí referenční tabulky se zkopírují první v pořadí závislostí, pak shardlets zkopírují v pořadí podle jejich závislosti v rámci každé dávky. To je nezbytné, aby Primárníklíč-Cizíklíč omezení na cílové horizontálního oddílu se uplatní jako dorazí nová data. 

**Konzistence mapy horizontálního oddílu a případné dokončení**

V případě selhání službu rozdělení sloučení obnoví operations po všech výpadku a cílem je dokončete všechny v žádostech o průběhu. Však může existovat neopravitelné situacích, například horizontálního oddílu cíl je ztrátě nebo zneužití nad rámec opravit. Za těchto okolností může stále některé shardlets, který se má přesunout na horizontálních zdroje. Službu zajistí, že shardlet mapování jsou aktualizovány pouze po potřebná data byl úspěšně zkopírován do cílové. Shardlets se odstraní pouze ve zdroji, po všech svých dat byl zkopírován do cílové a odpovídající mapování bylo úspěšně aktualizováno. Operace odstranění se odehrává na pozadí při rozsahu již v cílové horizontálních online. Službu rozdělení sloučení vždy zajišťuje správnost mapování uložené v mapě horizontálního oddílu.

## <a name="the-split-merge-user-interface"></a>Rozdělení sloučení uživatelského rozhraní
Balíček služby rozdělení sloučení zahrnuje roli pracovního procesu a webové role. Webové role se používá k odesílání žádostí o rozdělení sloučení interaktivní způsobem. Hlavní součásti uživatelského rozhraní jsou následující:

* Typ operace: Typ operace je přepínač, který určuje typ operace provést pomocí služby pro tento požadavek. Můžete si vybrat mezi rozdělení, sloučení a přesuňte scénáře. Můžete také zrušit dříve odeslaný operaci. Můžete použít rozdělení, sloučení a přesunout požadavky pro rozsah horizontálního oddílu mapy. Seznam horizontálních mapuje pouze operace přesunutí podpory.
* Mapování horizontálních: V další části Parametry žádosti se týkají informací o mapy horizontálního oddílu a hostování mapu horizontálního oddílu databáze. Konkrétně je třeba zadat název serveru Azure SQL Database a hostování shardmap, pověření pro připojení k databázi mapy horizontálního oddílu a nakonec název mapování horizontálních databáze. Operaci v současné době přijímá pouze jedinou sadu pověření. Tyto přihlašovací údaje musí mít dostatečná oprávnění k provedení změny mapy horizontálního oddílu také tak, aby data uživatele na horizontálních oddílů.
* Rozsah zdrojových (rozdělení a sloučení): operace rozdělení a sloučení zpracovává rozsah pomocí klíče vysoké a nízké. Pokud chcete zadat operace s hodnotou klíče bez vazby vysoké, zaškrtnutím políčka "vysoká hodnota klíče je maximální" a vysokou klíčové pole ponechte prázdné. Rozsah hodnoty klíče, které zadáte, není potřeba přesně odpovídají mapování a jeho hranice na mapě horizontálního oddílu. Pokud nezadáte vůbec žádné hranice rozsahu službu automaticky odvození nejbližší rozsahu. Můžete použít skript prostředí PowerShell GetMappings.ps1 načíst aktuální mapování v mapě dané horizontálního oddílu.
* Rozdělení zdroj chování (rozdělení): pro rozdělení operace definovat bodem rozdělení zdrojový rozsah. To provedete zadáním horizontálního dělení klíč, kam chcete rozdělit. Použijte přepínač zadejte, zda chcete dolní části rozsahu (s výjimkou klíče rozdělení) přesunout, nebo jestli chcete horní části přesunout (včetně klíče rozdělení).
* Zdroj Shardlet (přesunout): přesunout operace se liší od rozdělení nebo sloučení operací, jako nevyžadují rozsahu popisují zdroji. Zdroj pro přesun je jednoduše identifikována horizontálního dělení hodnota klíče, který chcete přesunout.
* Cíl horizontálního oddílu (rozdělení): po zadané informace ve zdroji operaci rozdělení, je třeba definovat, kam chcete data zkopírovat tím, že poskytuje název serveru a databáze Azure SQL Db pro cíl.
* Cílový rozsah (sloučení): operace sloučení přesunout shardlets do existující horizontálního oddílu. Tím, že poskytuje rozsah hranice existující rozsahu, který chcete sloučit s identifikujete existující horizontálního oddílu.
* Velikost dávky: Velikost dávky řídí počet shardlets, která přejde do režimu offline v době při přesunu dat. Toto je celočíselná hodnota, kde můžete použít nižší hodnoty po citlivé dlouhé doby výpadku pro shardlets. Vyšší hodnoty zvýší čas, který je daný shardlet offline však může zvýšit výkon.
* Id operace (zrušit): Pokud máte probíhající operace, která již nepotřebujete, můžete tuto operace zrušíte tím, že poskytuje jeho ID operace v tomto poli. ID operace můžete načíst z tabulky stav žádosti (viz oddíl 8.1) nebo z výstupu ve webovém prohlížeči, kde jste odeslali žádost.

## <a name="requirements-and-limitations"></a>Požadavky a omezení
Aktuální implementace služby rozdělení sloučení podléhá následující požadavky a omezení: 

* Horizontálních oddílů je třeba existovat a být zaregistrovaný v mapě horizontálního oddílu, před provedením operace sloučení rozdělení na těchto horizontálních oddílů. 
* Služba nevytváří tabulky a všechny další objekty databáze automaticky v rámci jeho operací. To znamená, že schéma pro všechny horizontálně dělené tabulky a referenční tabulky musí existovat v cílové horizontálních před všechny operace rozdělení nebo sloučení nebo přesunutí. Horizontálně dělené tabulky konkrétně musí být v rozsahu, kde jsou nové shardlets přidávaného operací rozdělení nebo sloučení nebo přesunutí prázdné. Jinak se operace nezdaří Kontrola konzistence počáteční na cílové horizontálního oddílu. Všimněte si, že odkaz, který data se zkopírují, pouze pokud odkaz je tabulka prázdná a že nejsou žádné záruky konzistence s ohledem na ostatní souběžných operací zápisu na referenční tabulky. Doporučujeme toto: při spuštění operace rozdělení nebo sloučení, žádné jiné operace zápisu provést změny referenční tabulky.
* Služba závisí na řádek identity vytvořit jedinečný index nebo klíč, který obsahuje klíč horizontálního dělení ke zlepšení výkonu a spolehlivosti pro velké shardlets. To umožňuje služby pro přesun dat na i podrobnější než právě horizontálního dělení hodnota klíče. To pomáhá snížit maximální množství místa protokolu a zámky, které jsou požadovány při operaci. Zvažte vytvoření jedinečný index nebo primární klíč, včetně horizontálního dělení klíče v dané tabulce, pokud chcete použít tuto tabulku s žádostmi o rozdělení nebo sloučení nebo přesunutí. Z důvodů výkonu musí být klíč horizontálního dělení počáteční sloupec v klíč nebo index.
* Během zpracování žádosti může být některá data shardlet existuje zdrojové a cílové horizontálního oddílu. To je nezbytné k ochraně proti selhání během pohybu shardlet. Integrace rozdělení sloučení s mapy horizontálního oddílu zajišťuje, že připojení prostřednictvím data závislé směrování rozhraní API **OpenConnectionForKey** metoda na mapě horizontálního oddílu nejsou vidět všechny nekonzistentní zprostředkující stavy. Ale při připojení ke zdroji nebo horizontálních oddílů cíl bez použití **OpenConnectionForKey** metoda, nekonzistentní zprostředkující stavy může být viditelné, pokud se děje rozdělení nebo sloučení nebo přesunutí požadavky. Tato připojení může zobrazovat částečné nebo duplicitní výsledky v závislosti na načasování nebo komplikovaného skládání architektury základní připojení. Toto omezení teď zahrnuje připojení vytvořená elastické škálování Shard více – dotazy.
* Databázi metadat pro službu rozdělení sloučení nesmí sdílet mezi různé role. Například role rozdělení sloučení služby spuštěné v pracovní musí odkazovat na jiné metadata databáze než roli produkční.

## <a name="billing"></a>Fakturace
Službu rozdělení sloučení běží jako cloudová služba v rámci vašeho předplatného Microsoft Azure. Proto platí poplatky za cloudové služby pro vaše instance služby. Pokud provádíte často rozdělení nebo sloučení nebo přesunutí operace, doporučujeme že odstranit rozdělení sloučení cloudové služby. Která šetří náklady pro spuštění nebo nasazena instance služby cloudu. Můžete znovu nasadit a spustit konfiguraci snadno spustitelného kdykoli budete potřebovat k provádění operací rozdělení nebo sloučení. 

## <a name="monitoring"></a>Monitorování
### <a name="status-tables"></a>Stav tabulky
Poskytuje službu sloučení rozdělení **stavem** tabulky v databázi metadat úložiště pro monitorování dokončené a probíhající požadavků. V tabulce jsou uvedeny řádek pro každý požadavek sloučení rozdělení byla odeslána na tuto instanci službu rozdělení sloučení. Nabízí následující informace pro každý požadavek:

* **Časové razítko**: čas a datum, když požadavek.
* **OperationId**: A identifikátor GUID, který jedinečně identifikuje požadavek. Tento požadavek lze také zrušte operaci, i když je stále probíhají.
* **Stav**: aktuální stav žádosti. Probíhající požadavky také vypíše aktuální fáze, ve kterém se nachází daná žádost.
* **CancelRequest**: Příznak určující, zda požadavek byl zrušen.
* **Průběh**: procento odhad dokončení operace. Hodnota 50 značí, že operace je přibližně 50 % dokončení.
* **Podrobnosti o**: hodnota ve formátu XML, která poskytuje podrobnější sestava průběhu. Sestava průběhu jsou pravidelně aktualizovány sady řádků se zkopírují ze zdroje k cíli. V případě selhání nebo výjimky tento sloupec také obsahuje podrobnější informace o selhání.

### <a name="azure-diagnostics"></a>Diagnostika Azure
Služba rozdělení sloučení používá Azure Diagnostics podle Azure SDK 2.5 pro monitorování a Diagnostika. Jak je popsáno v tomto poli se řídit konfigurace diagnostiky: [povolení diagnostiky Azure Cloud Services a virtuálních počítačů](../cloud-services/cloud-services-dotnet-diagnostics.md). Balíček ke stažení obsahuje dvě konfigurace diagnostiky – jeden pro webovou roli a jeden pro roli pracovního procesu. Tyto konfigurace diagnostiky pro službu, postupujte podle pokynů od [základy cloudové služby ve službě Microsoft Azure](https://code.msdn.microsoft.com/windowsazure/Cloud-Service-Fundamentals-4ca72649). Obsahuje definice pro čítače výkonu, protokoly služby IIS, protokoly událostí systému Windows a protokoly událostí aplikace rozdělení sloučení protokolu. 

## <a name="deploy-diagnostics"></a>Nasazení diagnostiky
Chcete-li povolit monitorování a Diagnostika pomocí konfigurace diagnostiky pro webové a pracovní role poskytovaný balíček NuGet, spusťte následující příkazy pomocí Azure PowerShell: 

    $storage_name = "<YourAzureStorageAccount>" 

    $key = "<YourAzureStorageAccountKey" 

    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key  


    $config_path = "<YourFilePath>\SplitMergeWebContent.diagnostics.xml" 

    $service_name = "<YourCloudServiceName>" 

    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWeb" 


    $config_path = "<YourFilePath>\SplitMergeWorkerContent.diagnostics.xml" 

    $service_name = "<YourCloudServiceName>" 

    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWorker" 

Můžete najít další informace o tom, jak nakonfigurovat a nasadit nastavení diagnostiky zde: [povolení diagnostiky Azure Cloud Services a virtuálních počítačů](../cloud-services/cloud-services-dotnet-diagnostics.md). 

## <a name="retrieve-diagnostics"></a>Načtení diagnostiky
Z Průzkumníka serveru Visual Studia v Azure části stromu Průzkumníka serveru můžete snadný přístup k vaší diagnostiky. Otevřete Visual Studio instance a v řádku nabídek klikněte na zobrazení a Průzkumníka serveru. Klikněte na ikonu Azure pro připojení k předplatnému Azure. Potom přejděte na Azure -> úložiště -> <your storage account> -> WADLogsTable-tabulky >. Další informace najdete v tématu [procházení prostředků úložiště pomocí Průzkumníka serveru](http://msdn.microsoft.com/library/azure/ff683677.aspx). 

![WADLogsTable][2]

WADLogsTable zvýrazněných v předchozí obrázek obsahuje podrobné události z protokolu aplikace služby rozdělení sloučení. Všimněte si, že výchozí konfigurace staženého balíčku byla zaměřena produkční nasazení. Interval, ve kterém jsou vyžádány protokoly a čítače od instance služby je proto velké (5 minut). Pro testovací a vývojové snižte interval úpravou nastavení diagnostiky webu nebo role pracovního procesu vašim potřebám. Klikněte pravým tlačítkem na roli v Průzkumníka serveru Visual Studia (viz výše) a upravte časový interval přenosu v dialogu pro nastavení konfigurace diagnostiky: 

![Konfigurace][3]

## <a name="performance"></a>Výkon
Obecně platí je lepší výkon očekávat od tím vyšší další původce úrovně služeb ve službě Azure SQL Database. Vyšší vstupně-výstupní operace, procesoru a paměti přidělení pro vyšší úrovně služeb využívat hromadné kopírování a odstraňte operace, které používá služba rozdělení sloučení. Z tohoto důvodu zvýšit úroveň služby jen pro tyto databáze definované, omezenou dobu.

Služba také provede ověření dotazů v rámci jeho běžných operací. Tyto dotazy ověření zkontrolujte neočekávané přítomnosti dat v rozsahu cílového a zajistilo, že všechny operace rozdělení nebo sloučení nebo přesunutí spuštění z konzistentním stavu. Všechny tyto dotazy fungovat přes horizontálního dělení rozsahy klíčů definované oboru operace a velikost dávky k dispozici jako součást definice žádosti. Tyto dotazy se nejlépe provést, pokud indexu je přítomen, který má klíč horizontálního dělení jako počáteční sloupec. 

Kromě toho jedinečnosti vlastnost klíčem horizontálního dělení jako počáteční sloupec povolí službu na používání optimalizované přístup, který omezuje spotřeby prostředků z hlediska místa protokolu a paměti. Tato vlastnost jedinečnosti je potřeba přesunout velikostí velkých objemů dat (obvykle vyšší než 1GB). 

## <a name="how-to-upgrade"></a>Postup upgradu
1. Postupujte podle kroků v [nasazení služby rozdělení sloučení](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
2. Změňte cloudu konfigurační soubor služby pro nasazení rozdělení sloučení tak, aby odrážely novou parametry konfigurace. Nový požadovaný parametr je informace o certifikát použitý k šifrování. Snadný způsob, jak to udělat, je k porovnání nový soubor šablony konfigurace z stahování proti vaší stávající konfigurace. Ujistěte se, že přidáte nastavení "DataEncryptionPrimaryCertificateThumbprint" a "DataEncryptionPrimary" pro webové a roli pracovního procesu.
3. Před nasazením aktualizace do Azure, ověřte, že všechny operace právě probíhající rozdělení sloučení byl dokončen. Snadno to provedete pomocí dotazu na stav žádosti a PendingWorkflows tabulky v databázi metadat sloučení rozdělení pro probíhající požadavky.
4. Aktualizujte stávající nasazení cloudové služby pro rozdělení sloučení ve vašem předplatném Azure nový balíček a konfiguračním souboru aktualizované služby.

Není nutné zřídit novou databázi metadat pro rozdělení sloučení k upgradu. Nové verze automaticky upgraduje vaše stávající databáze metadat na novou verzi. 

## <a name="best-practices--troubleshooting"></a>Osvědčené postupy a řešení potíží
* Definování testovacím klientem a vykonávat vaše nejdůležitější rozdělení nebo sloučení nebo přesunutí operace s testovacím klientem napříč několika horizontálních oddílů. Zkontrolujte, že všechna metadata správně definován v mapě horizontálního oddílu a že operace není v rozporu omezení nebo cizí klíče.
* Zachovat testovacím klientem velikost dat nad maximální velikost dat klienta služby největší zajistit nejsou zjištění velikost dat souvisejících s problémy. To vám usnadní vyhodnocení horní hranice v době potřebné k přesunutí jednoho klienta. 
* Ujistěte se, že schéma umožňuje odstranění. Rozdělení sloučení služba vyžaduje, aby možnost po dat byl úspěšně zkopírován do cílové odebrání dat ze zdroje horizontálního oddílu. Například **odstranit aktivační události** můžete zabránit odstranění dat na zdroji a může způsobit selhání operací.
* Klíč horizontálního dělení by měl být počáteční sloupec ve vaší primární klíč nebo jedinečný index definice. Který zajišťuje nejlepší výkon pro ověření dotazů rozdělení nebo sloučení a skutečná data přesouvání a odstranění operací, které pracují vždy se rozsahy klíčů horizontálního dělení.
* Společně umístit služby rozdělení sloučení v centru oblasti a data, kde jsou umístěny databáze. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png


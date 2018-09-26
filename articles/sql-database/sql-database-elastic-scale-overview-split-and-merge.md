---
title: Přesun dat mezi databázemi s horizontálním navýšením kapacity | Dokumentace Microsoftu
description: Vysvětluje, jak pracovat s horizontálními oddíly a přesun dat prostřednictvím v místním prostředí služby, použití služby elastic database rozhraní API.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-scale
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 518d7659df603ed0fcab4aebf5f35c3b92e75ccf
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162624"
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Přesun dat mezi cloudovými databázemi s horizontálním navýšením kapacity
Pokud jste vývojář softwaru a náhle vaší aplikace při vyžádání obrovské, potřebujete s ohledem růst. Proto můžete přidat další databáze (horizontální oddíly). Jak distribuovat data do nové databáze bez narušení integrity dat? Použití **nástroj split-merge** pro přesun dat z databáze s omezením do nové databáze.  

Nástroj split-merge běží jako webová služba Azure. Správce nebo vývojáře používá nástroj pro přesun shardletů (data z horizontálního oddílu) mezi různými databázemi. (horizontální oddíly). Nástroj používá ke správě databáze metadat služby a zajištění konzistentní mapování správy mapování horizontálních oddílů.

![Přehled][1]

## <a name="download"></a>Ke stažení
[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Dokumentace
1. [Kurz nástroj elastic database dělení a slučování](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
2. [Konfigurace zabezpečení dělení a slučování](sql-database-elastic-scale-split-merge-security-configuration.md)
3. [Důležité informace o zabezpečení dělení a slučování](sql-database-elastic-scale-split-merge-security-configuration.md)
4. [Správa mapování horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md)
5. [Migrace existujících databází pro horizontální navýšení kapacity](sql-database-elastic-convert-to-use-elastic-tools.md)
6. [Nástroje pro elastické databáze](sql-database-elastic-scale-introduction.md)
7. [Glosář nástrojů elastické databáze](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Proč používat nástroj split-merge?
**Flexibilita**

Aplikace potřebují flexibilně roztáhnout nad rámec jednoho databáze Azure SQL Database. Pomocí nástroje pro přesun dat podle potřeby do nové databáze při zachování integrity.

**Rozdělí na růst** 

Je potřeba zvýšit celkové kapacity pro zpracování prudkého růstu. Uděláte to tak, vytvořte další kapacitu pro horizontální dělení dat a jejich distribuci napříč postupně více databází až do splnění potřeb kapacity. Toto je typickým příkladem funkce "rozdělit". 

**Sloučení zmenšit**

Vzhledem k povaze sezónní firma potřebuje zmenšit kapacity. Nástroj umožňuje vertikálně snížit kapacitu do menšího počtu jednotek škálování, když firmy zpomalí. Tento požadavek se věnuje "slučování" funkce služby dělení a slučování elastické škálování. 

**Správa hotspotů přesunutím shardletů**

S více tenanty na databázi přidělení shardletů do horizontálních oddílů může vést k kritické body kapacitu na některých horizontálních oddílů. To vyžaduje opětovné přidělení shardletů nebo při přenosech zaneprázdněný shardletů do nové nebo méně využívaných horizontálních oddílů. 

## <a name="concepts--key-features"></a>Koncepty a klíčové funkce
**Zákazník hostované služby**

Dělení a slučování je dodávána jako zákazník hostované služby. Musíte nasadit a hostitelem služby ve vašem předplatném Microsoft Azure. Balíček, který můžete stáhnout z NuGet obsahuje šablonu konfigurace dokončete informace pro konkrétní nasazení. Zobrazit [dělení a slučování kurzu](sql-database-elastic-scale-configure-deploy-split-and-merge.md) podrobnosti. Služba běží ve vašem předplatném Azure, můžete řídit a konfigurovat většinu aspektů zabezpečení služby. Výchozí šablona zahrnuje možnosti konfigurace protokolu SSL, ověřování pomocí certifikátu klienta, šifrování pro uložené přihlašovací údaje, DoS zabezpečení a omezení IP adres. Další informace o aspektech zabezpečení najdete v následujícím dokumentu [konfigurace zabezpečení dělení a slučování](sql-database-elastic-scale-split-merge-security-configuration.md).

Výchozí hodnota nasazení spouští se služba s jeden pracovní proces a jednu webovou roli. Každý virtuální počítač A1 velikost používá ve službě Azure Cloud Services. Když tato nastavení nelze změnit, když nasazujete balíček, můžete je změnit po úspěšném nasazení ve spuštěné cloudové služby (na webu Azure portal). Všimněte si, že role pracovního procesu musí být nakonfigurované pro více než jednu instanci z technických důvodů. 

**Integrace mapy horizontálních oddílů**

Služby dělení a slučování komunikuje s mapy horizontálních oddílů aplikace. Při použití služby dělení a slučování a rozdělit nebo sloučit tak rozsahy nebo přesunout shardletů mezi horizontálními oddíly, služba automaticky udržuje mapy horizontálních oddílů aktuální. Uděláte to tak, služba se připojí k databáze správce mapování horizontálních oddílů aplikace a udržuje rozsahy a mapování jako průběh požadavků rozdělení/sloučení/move. Tím se zajistí, že mapa horizontálních oddílů vždy představuje aktuální zobrazení při dělení a slučování operací se děje. Rozdělit, sloučení a shardletem operace přesunu se implementují přesunutím dávku shardletů z horizontálních oddílů zdrojové do cílové horizontálního oddílu. Během operace přesunu shardletu shardletů v souladu s aktuální dávku se označí jako offline v mapě horizontálních oddílů a nejsou k dispozici pro připojení směrování závislé na datech pomocí **OpenConnectionForKey** rozhraní API. 

**Konzistentní shardletu připojení**

Při přesunu dat spuštění nové dávky shardlety, závislé na datech směrování připojení k ukládání horizontálního oddílu jsou ukončeny shardletu a následné připojení z mapy horizontálních oddílů, které rozhraní API na shardlety jsou zablokované při přesouvání dat k dispozici žádné mapy horizontálních oddílů v průběhu předejdete tak nekonzistencím. Připojení k jiné shardlety ve stejném horizontálním oddílu také získat ukončeny, ale proběhne úspěšně znovu okamžitě při opakování. Po přesunutí dávky shardlety jsou označeny za cíl horizontálních oddílů online a zdroj data se odeberou ze zdroje horizontálních oddílů. Tato služba bude tyto kroky pro každou dávku, dokud se přesunuly taky všechny shardletů. To povede k několika operací ukončit připojení v průběhu dokončení operace rozdělení/sloučení/move.  

**Správa dostupnosti shardletu**

Omezení připojení ukončuje do aktuální dávky shardlety, jak je popsáno výše omezuje rozsah nedostupnost na jednu dávku shardletů najednou. To je upřednostňována před metodiky kde kompletní horizontálních oddílů zůstane offline z důvodu jeho shardletů v průběhu operace dělené tunelové propojení nebo sloučení. Velikost dávky, definovaná jako počet různých shardletů přesunout najednou, je parametr konfigurace. Lze ji definovat pro každou operaci rozdělení a sloučení v závislosti na potřebách dostupnosti a výkonu vaší aplikace. Všimněte si, že se rozsah, který se zamykají mapy horizontálních oddílů může být větší než zadaná velikost dávky. Je to proto, že služba vybere rozsah velikost tak, aby skutečný počet hodnot klíče horizontálního dělení dat zhruba odpovídá velikosti dávky. To je důležité si pamatovat, zejména pro klíče řídce naplněných horizontálního dělení. 

**Metadata úložiště**

Služby dělení a slučování používá databázi k údržbě stavu a pro protokoly během zpracování požadavku. Uživatel vytvoří databázi v rámci svého předplatného a obsahuje připojovací řetězec pro něj v konfiguračním souboru pro nasazení služby. Správce z organizace uživatele můžete také připojení k této databázi kontrolovat jeho průběh požadavku a prozkoumat podrobné informace o potenciálních selhání.

**Sledování horizontálního dělení**

Služby dělení a slučování rozlišuje mezi (1) horizontálně dělené tabulky, (2) referenční tabulky a (3) normální tabulky. Sémantika operace rozdělení/sloučení/move závisí na typu tabulka použitá a jsou definovány takto: 

* **Horizontálně dělené tabulky**: rozdělení, sloučení a operací přesunu přesunout shardletů ze zdroje do cílového horizontálního oddílu. Po úspěšném dokončení celkový požadavek tyto shardletů už nejsou k dispozici ve zdroji. Mějte na paměti, že cílové tabulky musí existovat v cílové horizontálního oddílu a nesmí obsahovat data v cílovém rozsahu před zpracování operace. 
* **Referenční tabulky**: pro referenční tabulky, rozdělení, sloučení a přesunout operace zkopírovat data ze zdroje do cílového horizontálního oddílu. Všimněte si však, že žádné změny dojít na cíl horizontálního oddílu pro dané tabulky všechny řádky už existuje v této tabulce na cíli. V tabulce musí být prázdná pro libovolnou operaci kopírování tabulky odkaz zpracovat.
* **Další tabulky**: jiné tabulky může být k dispozici na zdroj nebo cíl operace rozdělení a sloučení. Služby dělení a slučování ignoruje tyto tabulky pro přesun dat nebo operace kopírování. Mějte na paměti, ale může narušovat tyto operace v případě omezení.

Poskytuje informace o odkazu vs. horizontálně dělené tabulky **položka SchemaInfo** rozhraní API na mapě horizontálních oddílů. Následující příklad ukazuje použití těchto rozhraní API na daném horizontálním oddílu mapování správce objekt smm: 

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

Tabulky "oblasti" a "národ" jsou definované jako referenční tabulky a zkopírují se operace rozdělení/sloučení/move. "zákazník" a "orders" pak jsou definovány jako horizontálně dělené tabulky. C_CUSTKEY a O_CUSTKEY slouží jako klíč horizontálního dělení. 

**Referenční integritu**

Služby dělení a slučování analyzuje závislosti mezi tabulkami a používá vztahy cizího klíče primární klíč do fáze operace pro přesunutí referenční tabulky a shardletů. Obecně platí referenční tabulky jsou nejprve zkopírovány v pořadí závislosti, pak shardletů jsou zkopírovány v pořadí podle jejich závislosti v rámci každé dávky. To je nezbytné, aby PK cizího klíče omezení na cílové horizontálních oddílů jsou zachované příchodu nových dat. 

**Konzistence mapy horizontálních oddílů a konečný výsledek dokončení**

V případě chyb služby dělení a slučování obnoví operace po jakémkoli výpadku a má za cíl dokončete všechny v průběhu požadavků. Však mohou nastat neopravitelné situace, například při horizontálních oddílů cílové dojde ke ztrátě nebo dojde k ohrožení bezpečnosti nad rámec opravit. Za těchto okolností může i nadále některé shardlety, které se měly přesunout jsou umístěné na zdroje horizontálních oddílů. Služba zajišťuje shardletu mapování se aktualizují po potřebných dat byly úspěšně zkopírovány do cíle. Shardletů jsou na zdrojovém odstraněno pouze tehdy, až všechna svoje data zkopírovala do cíle a odpovídající mapování bylo úspěšně aktualizováno. Operace odstranění probíhá na pozadí, zatímco rozsahu již v cílové horizontálních oddílů online. Služby dělení a slučování vždy zajistí správnost mapování uložené v mapě horizontálních oddílů.

## <a name="the-split-merge-user-interface"></a>Dělení a slučování uživatelského rozhraní
Balíček služby dělení a slučování obsahuje roli pracovního procesu a webové role. Webová role se používá k odesílání žádostí o dělení a slučování interaktivní způsobem. Hlavní součásti uživatelského rozhraní jsou následující:

* Typ operace: Typ operace je přepínač, který určuje typ operace prováděné službou pro tento požadavek. Můžete si vybrat mezi rozdělení, sloučení a přesunout scénáře. Můžete také zrušit dříve odeslané operace. Můžete použít rozdělení, sloučení a přesunout požadavků pro oblast mapy horizontálních oddílů. Seznam horizontálních oddílů mapuje pouze podporu operací přesunutí.
* Mapy horizontálních oddílů: Parametry požadavku v další části najdete informace o mapy horizontálních oddílů a hostování mapy horizontálních oddílů databáze. Zejména musíte zadat název serveru Azure SQL Database a databázi hostování shardmap, přihlašovací údaje pro připojení k databázi mapování horizontálních oddílů a nakonec název mapy horizontálních oddílů. V současné době operaci přijímá pouze jedinou sadu přihlašovacích údajů. Tyto přihlašovací údaje musí mít dostatečná oprávnění k provedení změny mapy horizontálních oddílů, stejně jako uživatelská data na horizontální oddíly.
* Zdrojový rozsah (rozdělení a sloučení): operace rozdělení a sloučení zpracovává rozsah pomocí jeho nízký klíč a vysoký klíč. K určení operace s hodnotou klíče bez vazby vysoké, zaškrtněte políčko "vysoká hodnota klíče je maximální počet" a vysoký klíč pole nechat prázdné. Rozsah hodnoty klíče, které určíte tak, aby přesně odpovídaly mapování a jeho hranice v mapě horizontálních oddílů není nutné. Pokud nezadáte vůbec žádné hranice rozsahu služby automaticky odvodit nejbližší oblast. Můžete použít skript prostředí PowerShell GetMappings.ps1 načíst aktuální mapování v daném horizontálním oddílu mapování.
* Rozdělit zdroje chování (rozdělení): operace rozdělení definovat bod tak, aby rozdělení zdrojové oblasti. To provedete tím, že poskytuje klíč horizontálního dělení, kde chcete rozdělit. Použijte přepínač určit, jestli chcete dolní části oblasti (s výjimkou klíče rozdělení) přesunout nebo zda se má přesunout (včetně klíče rozdělení) horní části.
* Zdroj Shardletu (přesunout): Přesunutí operací se liší od operace dělené tunelové propojení nebo sloučení jako nevyžadují rozsah popsat zdroje. Zdroj pro přesunutí jednoduše je identifikován hodnotě klíče horizontálního dělení, který chcete přesunout.
* Cíl horizontálních oddílů (rozdělení): Po doplnění informací na zdroji operaci dělení, budete muset určit, kde se mají data, která mají být zkopírovány do zadáním názvu serveru a databáze Azure SQL Db pro cíl.
* Cílový rozsah (sloučení): operace sloučení přesunout shardletů do existující horizontálního oddílu. Tím, že poskytuje typ hranic rozsah existujícího rozsahu, který chcete sloučit s identifikujete existující horizontálních oddílů.
* Velikost dávky: Velikost dávky řídí počet shardletech, které se během přesouvání dat přejdou do režimu offline. To je celočíselná hodnota, kde můžete použít nižší hodnoty po citlivé na dlouhé uvědomí pro shardletů. Vyšší hodnoty zvýší čas, který je daný shardletu offline ale může zlepšit výkon.
* Id operace (zrušit): Pokud máte probíhající operace, která už je nepotřebujete, můžete operaci zrušit zadáním jeho ID operace v tomto poli. ID operace můžete načíst z tabulky stav požadavku (viz část 8.1) nebo z výstupu ve webovém prohlížeči, kde danou žádost odeslal.

## <a name="requirements-and-limitations"></a>Požadavky a omezení
Aktuální implementace služby dělení a slučování je v souladu s následujícími požadavky a omezení: 

* Horizontální oddíly musí existovat a být registrován v mapě horizontálních oddílů, před provedením operace dělení a slučování v těchto horizontálních oddílech. 
* Služba nevytváří tabulek nebo ostatních databázových objektů automaticky jako součást jeho operace. To znamená, že schéma pro všechny horizontálně dělené tabulky a referenční tabulky musí existovat na horizontální oddíl cílové kontrolovala rozdělení/sloučení/move. Horizontálně dělené tabulky konkrétně musí být v rozsahu, kde mají přidal operace rozdělení/sloučení/přesunutí nové shardletů prázdné. Operace se nezdaří, jinak počáteční konzistence na cíl horizontálních oddílů. Všimněte si také tento odkaz, který data zkopírována pouze pokud odkaz je tabulka prázdná a, že neexistují žádné záruky konzistence s ohledem na jiných souběžných operací zápisu na referenční tabulky. Toto doporučení uvádíme: při spuštění operace dělení a slučování, žádné jiné operace zápisu měnit referenční tabulky.
* Služba závisí na řádek identity stanovené jedinečném indexu nebo klíče, který obsahuje klíč horizontálního dělení ke zlepšení výkonu a spolehlivosti pro velké shardletů. To umožňuje služba pro přesun dat na i na nižší členitosti, než právě horizontálního dělení hodnotu klíče. To pomáhá snížit maximální množství místa protokolu a zámků, které jsou potřeba při operaci. Zvažte vytvoření jedinečného indexu nebo v primárním klíči, včetně klíč horizontálního dělení v příslušné tabulce, pokud chcete použít danou tabulku s žádostmi o rozdělení/sloučení/move. Z důvodů výkonu klíč horizontálního dělení by měl být počáteční sloupec v klíč nebo index.
* V průběhu zpracování požadavku. některá data shardletu mohou být k dispozici zdroj i cíl horizontálních oddílů. To je nezbytné k zajištění ochrany před selháními během přesunu shardletu. Integrace dělení a slučování s mapováním horizontálních oddílů, který zajišťuje připojení prostřednictvím data závislé směrování rozhraní API s využitím **OpenConnectionForKey** metodu na mapě horizontálních oddílů nezobrazí žádné nekonzistentní průběžných stavů. Ale při připojení ke zdrojové nebo cílové horizontální oddíly bez použití **OpenConnectionForKey** metody nekonzistentní průběžných stavů může být viditelné, pokud jsou požadavky na rozdělení/sloučení/move děje. Tato připojení může ukazovat částečné nebo duplicitní výsledky v závislosti na načasování nebo horizontální oddíl podkladové připojení. Toto omezení zahrnuje aktuálně připojení vytvořená elastické škálování Shard více – dotazy.
* Databáze metadat služby dělení a slučování nesmí být sdílena mezi různé role. Například role služby dělení a slučování spuštěné v testovacím prostředí musí odkazovat na databázi rozdílná metadata než produkční úlohy.

## <a name="billing"></a>Fakturace
Služby dělení a slučování běží jako cloudová služba v rámci vašeho předplatného Microsoft Azure. Proto platí poplatky za cloudové služby k vaší instanci služby. Pokud provádíte často operace rozdělení/sloučení/move, doporučujeme že odstranit dělení a slučování cloudové služby. Která šetří náklady na spuštění nebo nasazené instance cloudové služby. Můžete znovu nasadit a spustit konfiguraci snadno spustitelný, kdykoli potřebujete k provádění operací dělené tunelové propojení nebo sloučení. 

## <a name="monitoring"></a>Monitorování
### <a name="status-tables"></a>Stav tabulky
Poskytuje služby dělení a slučování **stavem** tabulky v databázi úložiště metadat pro sledování požadavků dokončené a probíhající. V tabulce řádek pro každý požadavek dělení a slučování, který se odeslal k této instanci služby dělení a slučování. Poskytuje následující informace pro každý požadavek:

* **Časové razítko**: čas a datum zahájení žádosti.
* **ID operace**: A identifikátor GUID, který jednoznačně identifikuje požadavek. Tento požadavek lze také zrušit operaci, i když je stále probíhají.
* **Stav**: aktuální stav žádosti. Probíhající požadavky také vypíše aktuální fáze, ve kterém je požadavek.
* **CancelRequest**: Příznak, který označuje, zda požadavek byl zrušen.
* **Průběh**: Odhad procenta dokončení operace. Hodnota 50 znamená, že je operace dokončena přibližně 50 %.
* **Podrobnosti o**: hodnota ve formátu XML, která poskytuje podrobnější sestavu průběh. Sestava průběhu se pravidelně aktualizovat, protože sady řádků se zkopírují ze zdroje do cíle. V případě chyby nebo výjimky tento sloupec obsahuje také podrobné informace o selhání.

### <a name="azure-diagnostics"></a>Diagnostika Azure
Služby dělení a slučování používá Azure Diagnostics založené na Azure SDK 2.5 pro monitorování a diagnostiku. Konfigurace diagnostiky můžete řídit, jak je popsáno zde: [povolení diagnostiky v Azure Cloud Services a Virtual Machines](../cloud-services/cloud-services-dotnet-diagnostics.md). Balíček ke stažení obsahuje dvě konfigurace diagnostiky – jeden pro webovou roli a jeden pro roli pracovního procesu. Tyto konfigurace diagnostiky služby postupujte podle pokynů od [Cloud Service Fundamentals v Microsoft Azure](https://code.msdn.microsoft.com/windowsazure/Cloud-Service-Fundamentals-4ca72649). Obsahuje definice a čítače výkonu, protokoly IIS, protokoly událostí Windows a protokoly událostí aplikace dělení a slučování protokolu. 

## <a name="deploy-diagnostics"></a>Nasazení diagnostiky
Pokud chcete povolit monitorováním a diagnostikou pomocí konfigurace diagnostiky pro webové a pracovní role poskytovaný balíček NuGet, spusťte následující příkazy pomocí Azure Powershellu: 

    $storage_name = "<YourAzureStorageAccount>" 

    $key = "<YourAzureStorageAccountKey" 

    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key  


    $config_path = "<YourFilePath>\SplitMergeWebContent.diagnostics.xml" 

    $service_name = "<YourCloudServiceName>" 

    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWeb" 


    $config_path = "<YourFilePath>\SplitMergeWorkerContent.diagnostics.xml" 

    $service_name = "<YourCloudServiceName>" 

    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWorker" 

Můžete najít další informace o tom, jak nakonfigurovat a nasadit nastavení diagnostiky tady: [povolení diagnostiky v Azure Cloud Services a Virtual Machines](../cloud-services/cloud-services-dotnet-diagnostics.md). 

## <a name="retrieve-diagnostics"></a>Získání diagnostiky
Diagnostické snadno přístupná z Průzkumníka serveru Visual Studia v Azure části stromu Průzkumníka serveru. Otevřete instanci aplikace Visual Studio a v řádku nabídek klikněte na zobrazení a Průzkumníka serveru. Klikněte na ikonu Azure pro připojení k vašemu předplatnému Azure. Přejděte na Azure Storage -> -> <your storage account> -> WADLogsTable-tabulky >. Další informace najdete v tématu [procházení prostředků úložiště pomocí Průzkumníku serveru](http://msdn.microsoft.com/library/azure/ff683677.aspx). 

![WADLogsTable][2]

WADLogsTable zvýrazněný na obrázku výše obsahuje podrobné události z protokolu aplikace služby dělení a slučování. Všimněte si, že výchozí konfiguraci staženého balíčku je zaměřený na produkční nasazení. Proto interval, ve kterém se berou protokoly a čítače z instance služby je velké (5 minut). Pro vývoj a testování snižte interval úpravou nastavení diagnostiky webu nebo role pracovního procesu podle vašich potřeb. Klikněte pravým tlačítkem myši na roli v Průzkumníku serveru Visual Studia (viz výše) a potom upravte přenos období v dialogovém okně pro konfiguraci nastavení diagnostiky: 

![Konfigurace][3]

## <a name="performance"></a>Výkon
Obecně platí lepšího výkonu se dá očekávat od vyšší, další výkonné úrovně služeb ve službě Azure SQL Database. Vyšší přidělení vstupně-výstupních operací, využití procesoru a paměti pro vyšší úrovně výhody hromadného kopírování operace a odstranění, které používá služby dělení a slučování. Z tohoto důvodu zvýšit úroveň služby pouze pro tyto databáze definované, omezenou dobu.

Služba také provádí ověření dotazů v rámci normálního provozu. Tyto dotazy ověření zkontrolujte neočekávané přítomnost data v cílovém rozsahu a ujistěte se, že všechny operace rozdělení/sloučení/move začne v konzistentním stavu. Všechny tyto dotazy pracovat s objemnějšími rozsahy klíčů horizontálního dělení určené rozsah operace a velikost dávky poskytnutého jako součást definice požadavku. Tyto dotazy si vede nejlépe při indexu je k dispozici, který má klíč horizontálního dělení jako počáteční sloupec. 

Kromě toho vlastnost jedinečnost klíče horizontálního dělení jako počáteční sloupec vám umožní službu, aby používala optimalizované přístup, který omezuje využití prostředků z hlediska místa protokolu a paměti. Tato vlastnost jedinečnost je potřeba přesunout velké objemy dat (obvykle výše 1GB). 

## <a name="how-to-upgrade"></a>Postup upgradu
1. Postupujte podle kroků v [nasazení služby dělení a slučování](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
2. Změňte váš konfigurační soubor cloudové služby pro dělení a slučování nasazení tak, aby odrážela nové parametry konfigurace. Nový požadovaný parametr jsou informace o certifikát použitý k šifrování. Snadný způsob, jak to provést, je k porovnání nový soubor šablony konfigurace z položky ke stažení pro existující konfiguraci. Ujistěte se, že přidáte nastavení "DataEncryptionPrimaryCertificateThumbprint" a "DataEncryptionPrimary" pro web a role pracovního procesu.
3. Před nasazením aktualizací do Azure, ujistěte se, že všechny operace právě probíhající dělení a slučování dokončení. Můžete snadno to provedete pomocí dotazu na stav žádosti a PendingWorkflows tabulky v databázi dělení a slučování metadat pro probíhající požadavky.
4. Aktualizujte vaše stávající nasazení cloudové služby pro dělení a slučování ve vašem předplatném Azure nový balíček a konfigurační soubor služby aktualizované.

Nemusíte zřizovat nové databáze metadat pro dělení a slučování k upgradu. Nová verze automaticky upgraduje existující databázi metadat na novou verzi. 

## <a name="best-practices--troubleshooting"></a>Osvědčené postupy a řešení potíží
* Definice testovacího tenanta přestal pracovat a vaše nejdůležitější operace rozdělení/sloučení/move s testovacím klientem mezi několika horizontálními oddíly. Ujistěte se, že všechna metadata správně definován v mapě horizontálních oddílů a že operace, nebudou porušovat omezení nebo cizí klíče.
* Zachovat testovacího tenanta velikost dat nad maximální velikost dat tenanta největší zajistit nejsou zjištění velikost dat týkající se problémů. To vám pomůže vyhodnotit horní mez na čas potřebný k pohyb jednoho tenanta. 
* Ujistěte se, že schéma umožňuje odstranění. Dělení a slučování služba vyžaduje, aby možnost odebrat data z horizontálního oddílu zdroj po dat byly úspěšně zkopírovány do cíle. Například **odstranit aktivační události** můžete zabránit odstranění ve zdroji dat služby a může způsobit selhání operací.
* Klíč horizontálního dělení by měly být počáteční sloupec primárního klíče nebo jedinečného indexu definice. Která zajistí nejlepší výkon pro dotazy ověření dělené tunelové propojení nebo sloučení a skutečná data operace přesunu a odstranění, které pracují vždy se rozsahy klíčů horizontálního dělení.
* Společné umístění vaší služby dělení a slučování v centru oblasti a dat, kde jsou umístěné vaše databáze. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png


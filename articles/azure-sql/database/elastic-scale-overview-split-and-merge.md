---
title: Přesun dat mezi cloudovými databázemi s horizontálním navýšením kapacity
description: Vysvětluje, jak manipulovat s horizontálních oddílů a přesouvat data prostřednictvím samoobslužné služby pomocí rozhraní API elastické databáze.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 5a646ffe1d306d7ea13da002715d5bd9b907107b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793462"
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Přesun dat mezi cloudovými databázemi s horizontálním navýšením kapacity
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Pokud jste software jako vývojář služby a náhle nebudete mít k potřebnou poptávku, je potřeba růst. Takže přidáte další databáze (horizontálních oddílů). Jak znovu distribuovat data do nových databází bez přerušení integrity dat? K přesunu dat z omezených databází do nových databází použijte **Nástroj pro dělení k paralelnímu sloučení** .  

Nástroj pro dělení a slučování se spouští jako webová služba Azure. Správce nebo vývojář používá nástroj k přesunu shardlety (data z horizontálních oddílů) mezi různými databázemi (horizontálních oddílů). Nástroj používá správu map horizontálních oddílů k údržbě databáze metadat služby a zajišťuje konzistentní mapování.

![Přehled][1]

## <a name="download"></a>Stáhnout

[Microsoft. Azure. SqlDatabase. ElasticScale. Service. SplitMerge](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Dokumentace

1. [Kurz pro dělení a slučování elastické databáze](elastic-scale-configure-deploy-split-and-merge.md)
2. [Konfigurace zabezpečení dělení a slučování](elastic-scale-split-merge-security-configuration.md)
3. [Předpoklady zabezpečení dělení a slučování](elastic-scale-split-merge-security-configuration.md)
4. [Správa mapování horizontálních oddílů](elastic-scale-shard-map-management.md)
5. [Migrace existujících databází pro horizontální navýšení kapacity](elastic-convert-to-use-elastic-tools.md)
6. [Nástroje elastické databáze](elastic-scale-introduction.md)
7. [Glosář nástrojů elastické databáze](elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Proč používat nástroj pro dělení a slučování

- **Umožněn**

  Aplikace musí být pružně roztaženy nad limity izolované databáze v Azure SQL Database. Pomocí tohoto nástroje můžete přesouvat data podle potřeby do nových databází a přitom zachovat integritu.

- **Rozdělit na růst**

  Chcete-li zvýšit celkovou kapacitu pro zpracování výbušného nárůstu, vytvořte další kapacitu tím, že horizontálního dělení data a distribuujete je napříč přírůstkově více databázemi až do splnění potřeb kapacity. Toto je hlavní příklad funkce **Split** .

- **Sloučit do zmenšení**

  Nároky na kapacitu se zmenšují z důvodu sezónní povahy podniku. Nástroj umožňuje horizontální snížení kapacity v menším množství jednotek škálování v případě, že je čas společnosti pomalý. Tento požadavek se zabývá funkcí Merge ve službě elastického škálování Split-Merge.

- **Správa aktivních bodů přesunutím shardlety**

  Díky více klientům na databázi může přidělení shardlety a horizontálních oddílů vést k kritickým místům kapacity u některých horizontálních oddílů. To vyžaduje opětovné přidělení shardlety nebo přesunutí zaneprázdněného shardletyu na nové nebo méně využité horizontálních oddílů.

## <a name="concepts--key-features"></a>Koncepty & klíčových funkcí

- **Služby hostované zákazníky**

  Rozdělené sloučení se doručuje jako služba hostovaná zákazníkem. Službu musíte nasadit a hostovat v rámci předplatného Microsoft Azure. Balíček, který stáhnete ze sady NuGet, obsahuje šablonu konfigurace, která se dokončí s informacemi pro konkrétní nasazení. Podrobnosti najdete v [kurzu rozdělení na sloučení](elastic-scale-configure-deploy-split-and-merge.md) . Vzhledem k tomu, že služba běží ve vašem předplatném Azure, můžete řídit a konfigurovat většinu aspektů zabezpečení služby. Výchozí šablona obsahuje možnosti konfigurace TLS, ověřování klientů založených na certifikátech, šifrování uložených přihlašovacích údajů, ochrana systému DoS a omezení IP adres. Další informace o aspektech zabezpečení najdete v následující dokumentu [Konfigurace zabezpečení s rozděleným sloučením](elastic-scale-split-merge-security-configuration.md).

  Výchozí nasazená služba se spouští s jedním pracovním procesem a jednou webovou rolí. Každá z nich používá velikost virtuálního počítače a1 v Azure Cloud Services. I když při nasazování balíčku tato nastavení nemůžete změnit, můžete je po úspěšném nasazení v běžící cloudové službě změnit (prostřednictvím Azure Portal). Role pracovního procesu nesmí být nakonfigurovaná na více než jednu instanci z technických důvodů.

- **Integrace map horizontálních oddílů**

  Služba rozdělení a sloučení komunikuje s mapou horizontálních oddílů aplikace. Když použijete službu dělení a slučování k rozdělení nebo sloučení rozsahů nebo přesunete shardlety mezi horizontálních oddílů, služba automaticky udržuje mapu horizontálních oddílů v aktuálním stavu. V takovém případě se služba připojí k databázi správce mapy horizontálních oddílů aplikace a udržuje rozsahy a mapování jako průběh požadavků na rozdělení, sloučení a přesunutí. Tím se zajistí, že mapa horizontálních oddílů vždy prezentuje aktuální zobrazení při přechodu k operacím dělení na data. Operace rozdělení, sloučení a shardletu přesunu jsou implementovány přesunutím dávky shardlety ze zdrojového horizontálních oddílů do cílového horizontálních oddílů. Během operace přesunu shardletu se shardlety v závislosti na aktuální dávce označí jako offline v mapě horizontálních oddílů a nejsou k dispozici pro připojení směrování závislá na datech pomocí rozhraní **OpenConnectionForKey** API.

- **Konzistentní připojení shardletu**

  Po zahájení přesunu dat na novou dávku shardlety se všechna horizontálních oddílů mapa, která poskytuje směrování závislá na datech do horizontálních oddílů ukládání shardletu, zablokovala a následná připojení z rozhraní API mapy horizontálních oddílů do shardlety jsou zablokovaná, zatímco probíhá přesun dat, aby se předešlo nekonzistencím. Připojení k ostatním shardlety na stejném horizontálních oddílů se taky zastaví, ale po opakování se to bude zdařit okamžitě. Po přesunutí dávky jsou shardlety znovu označeny online pro cílový horizontálních oddílů a zdrojová data budou odstraněna ze zdrojového horizontálních oddílů. Služba projde tyto kroky pro každou dávku, dokud nebudou všechny shardlety přesunuty. To bude mít za následek několik operací ukončování připojení v průběhu operace dokončení rozdělení, sloučení nebo přesunutí.  

- **Správa dostupnosti shardletu**

  Omezení usmrcení připojení k aktuální dávce shardlety, jak je popsáno výše, omezuje rozsah nedostupnosti na jednu dávku shardlety v jednom okamžiku. To je upřednostňováno nad přístupem, kde kompletní horizontálních oddílů zůstane offline pro všechny své shardlety v průběhu operace rozdělení nebo sloučení. Velikost dávky definovaná jako počet jedinečných shardlety, která se mají přesunout najednou, je parametr konfigurace. Dá se definovat pro každou operaci rozdělení a sloučení v závislosti na potřebách dostupnosti a výkonu aplikace. Všimněte si, že rozsah, který je uzamčen v mapě horizontálních oddílů, může být větší než zadaná velikost dávky. Důvodem je to, že služba vybírá velikost rozsahu tak, aby skutečný počet hodnot horizontálního dělení klíčů v datech přibližně odpovídal velikosti dávky. To je důležité pamatovat zejména pro zhuštěné horizontálního dělení klíče.

- **Úložiště metadat**

  Služba rozdělení a sloučení používá databázi k údržbě jejího stavu a k udržování protokolů během zpracování žádosti. Uživatel tuto databázi vytvoří ve svém předplatném a poskytne připojovací řetězec v konfiguračním souboru pro nasazení služby. Správci z organizace uživatele se také mohou připojit k této databázi a zkontrolovat pokrok v žádosti a prozkoumat podrobné informace týkající se potenciálních selhání.

- **Horizontálního dělení – povědomí**

  Služba rozdělení a sloučení rozlišuje mezi (1) tabulkami horizontálně dělené, (2) referenčními tabulkami a (3) normálními tabulkami. Sémantika operace rozdělení, sloučení nebo přesunutí závisí na typu použité tabulky a jsou definovány následujícím způsobem:

  - **Tabulky horizontálně dělené**

    Operace rozdělení, sloučení a přesunu přesunou shardlety ze zdroje do cílového horizontálních oddílů. Po úspěšném dokončení celkové žádosti nejsou tyto shardlety nadále přítomny ve zdroji. Všimněte si, že cílové tabulky musí existovat na cílovém horizontálních oddílů a nesmí obsahovat data v cílovém rozsahu před zpracováním operace.

  - **Referenční tabulky**

    V případě referenčních tabulek kopírují operace rozdělení, sloučení a přesunutí data ze zdroje do cílového horizontálních oddílů. Všimněte si však, že v cílovém horizontálních oddílů pro danou tabulku nedochází k žádným změnám, pokud v této tabulce v cíli již existuje nějaký řádek. Tabulka musí být prázdná pro všechny operace kopírování referenční tabulky, aby se mohla zpracovat.

  - **Další tabulky**

    Další tabulky mohou být k dispozici buď na zdroji, nebo v cíli operace rozdělení a sloučení. Služba dělení a slučování ignoruje tyto tabulky pro jakékoli operace přesunu nebo kopírování dat. Upozorňujeme však, že tyto operace mohou v případě omezení narušovat.

    Informace o referenčních tabulkách vs. horizontálně dělené jsou poskytovány `SchemaInfo` rozhraními API na mapě horizontálních oddílů. Následující příklad ilustruje použití těchto rozhraní API na daném objektu Správce map horizontálních oddílů:

    ```csharp
    // Create the schema annotations
    SchemaInfo schemaInfo = new SchemaInfo();

    // reference tables
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region"));
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation"));

    // sharded tables
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY"));
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY"));

    // publish
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo);
    ```

    Tabulky "region" a "země" jsou definovány jako referenční tabulky a budou zkopírovány pomocí operací rozdělit/sloučit/přesunout. "Customer" a "Orders" jsou definovány jako tabulky horizontálně dělené. `C_CUSTKEY` a `O_CUSTKEY` slouží jako horizontálního dělení klíč.

- **Referenční integrita**

  Služba rozdělení a sloučení analyzuje závislosti mezi tabulkami a používá vztahy primárního klíče cizího klíče pro přípravu operací pro přesun referenčních tabulek a shardlety. Obecně se referenční tabulky zkopírují jako první v pořadí závislostí a shardlety se zkopírují v pořadí podle jejich závislosti v rámci každé dávky. To je nezbytné, aby při přijetí nových dat byly dodrženy omezení CK-PK na cílovém horizontálních oddílů.

- **Konzistence map horizontálních oddílů a následné dokončení**

  V případě selhání služba rozdělení a sloučení pokračuje v operacích po jakémkoli výpadku a zaměřuje na dokončení všech probíhajících žádostí. Mohou však nastat neobnovitelné situace, například, když dojde ke ztrátě nebo zabezpečení cílového horizontálních oddílů po nápravě. Za těchto okolností se může stát, že některé shardlety, které by se mohly přesunout, budou i nadále uloženy na zdrojovém horizontálních oddílů. Služba zajišťuje, aby se mapování shardletu aktualizovalo až po úspěšném zkopírování potřebných dat do cíle. Shardlety se odstraní jenom na zdroji, jakmile se všechna data zkopírují do cíle a odpovídajícím způsobem se úspěšně aktualizovala příslušná mapování. Operace odstranění probíhá na pozadí, zatímco rozsah je již online na cílovém horizontálních oddílů. Služba dělené sloučení vždy zajišťuje správnost mapování uložených v mapě horizontálních oddílů.

## <a name="the-split-merge-user-interface"></a>Uživatelské rozhraní dělení a slučování

Balíček služby pro dělení a slučování zahrnuje roli pracovního procesu a webovou roli. Webová role slouží k odeslání požadavků dílčího sloučení interaktivním způsobem. Hlavní součásti uživatelského rozhraní jsou následující:

- **Typ operace**

  Typ operace je přepínač, který řídí druh operace prováděné službou pro tuto žádost. Můžete si vybrat mezi scénáři rozdělení, sloučení a přesunutí. Můžete také zrušit dříve odeslanou operaci. Můžete použít žádosti o rozdělení, sloučení a přesun pro rozsah mapy horizontálních oddílů. List horizontálních oddílů Maps podporuje pouze operace přesunutí.

- **Mapa horizontálních oddílů**

  Další oddíl parametrů požadavku obsahuje informace o mapě horizontálních oddílů a databázi hostující vaši mapu horizontálních oddílů. Konkrétně musíte zadat název serveru a databáze hostující rozhraní shardmap, přihlašovací údaje pro připojení k databázi map horizontálních oddílů a nakonec název mapy horizontálních oddílů. V současné době tato operace akceptuje jenom jednu sadu přihlašovacích údajů. Tyto přihlašovací údaje musí mít dostatečná oprávnění k provádění změn v mapě horizontálních oddílů a také k uživatelským datům na horizontálních oddílů.

- **Zdrojový rozsah (rozdělení a sloučení)**

  Operace rozdělení a sloučení zpracuje rozsah pomocí jeho nízkého a horního klíče. Chcete-li zadat operaci s neohraničenou hodnotou klíče s vysokým klíčem, zaškrtněte políčko vysoká hodnota klíče Max a nechejte pole horní klíč prázdné. Hodnoty klíče rozsahu, které určíte, nemusejí přesně odpovídat mapování a jeho hranicím v mapě horizontálních oddílů. Pokud nezadáte žádné hranice rozsahu na všech službách, odsadí nejbližší rozsah automaticky. K načtení aktuálních mapování v dané mapě horizontálních oddílů můžete použít skript prostředí PowerShell GetMappings.ps1.

- **Chování rozdělení zdroje (rozdělit)**

  Pro rozdělené operace definujte bod pro rozdělení zdrojového rozsahu. Provedete to tak, že zadáte horizontálního dělení klíč, ve kterém chcete rozdělení provést. Použijte přepínač určete, zda chcete přesunout dolní část rozsahu (kromě rozděleného klíče), nebo zda chcete přesunout horní část (včetně rozděleného klíče).

- **Shardletu zdroje (přesunout)**

  Operace přesunu se liší od operací rozdělení nebo sloučení, protože nevyžadují rozsah pro popis zdroje. Zdroj pro přesun je jednoduše identifikovaný hodnotou horizontálního dělení klíče, kterou plánujete přesunout.

- **Cílový horizontálních oddílů (rozdělit)**

  Jakmile zadáte informace o zdroji operace rozdělení, je třeba definovat, kam chcete data zkopírovat, zadáním názvu serveru a databáze pro cíl.

- **Cílový rozsah (sloučení)**

  Operace sloučení přesunou shardlety k existujícímu horizontálních oddílů. Existující horizontálních oddílů identifikujete tak, že zadáte hranice rozsahu pro existující rozsah, se kterým chcete sloučení.

- **Velikost dávky**

  Velikost dávky řídí počet shardlety, které se během přesunu dat vrátí do offline režimu. Toto je celočíselná hodnota, kde můžete použít menší hodnoty, pokud budete rozlišovat na dlouhou dobu výpadku shardlety. Větší hodnoty prodlouží dobu, po kterou je daný shardletu offline, ale může zvýšit výkon.

- **ID operace (zrušit)**

  Pokud máte probíhající operaci, která už nepotřebujete, můžete operaci zrušit tím, že v tomto poli zadáte její ID operace. ID operace můžete načíst z tabulky stav žádosti (viz oddíl 8,1) nebo z výstupu ve webovém prohlížeči, kam jste odeslali žádost.

## <a name="requirements-and-limitations"></a>Požadavky a omezení

Aktuální implementace služby rozdělení a sloučení podléhá následujícím požadavkům a omezením:

- Horizontálních oddílů musí existovat a musí být registrována v mapě horizontálních oddílů předtím, než je možné provést operaci dělení a sloučení u těchto horizontálních oddílů.
- Služba nevytváří v rámci svých operací automaticky tabulky ani žádné jiné databázové objekty. To znamená, že schéma pro všechny tabulky horizontálně dělené a referenční tabulky musí existovat na cílovém horizontálních oddílů před jakoukoli operací Split/Merge/Move. Tabulky horizontálně dělené musí být v rozsahu prázdné, aby bylo přidáno nové shardlety pomocí operace rozdělení/sloučení nebo přesunutí. V opačném případě operace neproběhne při prvotní kontrole konzistence na cílovém horizontálních oddílů. Všimněte si také, že referenční data jsou zkopírována pouze v případě, že je referenční tabulka prázdná a že neexistují žádné záruky konzistence s ohledem na jiné souběžné operace zápisu v referenčních tabulkách. Doporučujeme toto: při spouštění operací rozdělení/sloučení neprovádí žádné jiné operace zápisu změny v referenčních tabulkách.
- Služba spoléhá na identitu řádku vytvořenou jedinečným indexem nebo klíčem, který obsahuje klíč horizontálního dělení pro zlepšení výkonu a spolehlivosti pro velké shardletyy. Díky tomu může služba přesouvat data s ještě jemnější členitosti než jenom hodnota horizontálního dělení klíče. To pomáhá snižovat maximální množství místa v protokolu a zámků, které jsou požadovány během operace. Pokud chcete použít tuto tabulku s požadavky na rozdělení/sloučení/přesun, zvažte vytvoření jedinečného indexu nebo primárního klíče, včetně klíče horizontálního dělení v dané tabulce. Z důvodu výkonu by měl být klíč horizontálního dělení jako první sloupec v klíči nebo v indexu.
- V průběhu zpracování žádosti můžou být některá shardletu data přítomná jak na zdrojovém, tak na cílovém horizontálních oddílů. To je nezbytné k ochraně před chybami při shardletu pohybu. Integrace rozděleného sloučení s mapou horizontálních oddílů zajišťuje, že připojení prostřednictvím rozhraní API směrování závislých na datech pomocí metody **OpenConnectionForKey** na mapě horizontálních oddílů neobsahují žádné nekonzistentní přechodné stavy. Při připojení ke zdroji nebo cílové horizontálních oddílů bez použití metody **OpenConnectionForKey** se ale můžou zobrazit nekonzistentní přechodné stavy, když se provedou žádosti o rozdělení/sloučení nebo přesunutí. Tato připojení mohou zobrazovat částečné nebo duplicitní výsledky v závislosti na časování nebo horizontálních oddílůu, který je pro připojení podkladové. Toto omezení v současné době zahrnuje připojení vytvořená horizontálních oddílůmi dotazy elastického škálování.
- Databáze metadat pro službu dělení a slučování nesmí být sdílená mezi různými rolemi. Například role služby dělení a slučování běžící v přípravě musí odkazovat na jinou databázi metadat než produkční role.

## <a name="billing"></a>Fakturace

Služba dělení a slučování běží jako cloudová služba v rámci předplatného Microsoft Azure. Proto se poplatky za Cloud Services vztahují na vaši instanci služby. Pokud neprovádíte často operace rozdělení, sloučení nebo přesunutí, doporučujeme odstranit cloudovou službu pro dělení a slučování. Který šetří náklady na provoz nebo nasazení instancí cloudové služby. Můžete znovu nasadit a spustit spustitelný konfiguraci, kdykoli budete potřebovat provést operace rozdělení nebo sloučení.

## <a name="monitoring"></a>Monitorování

### <a name="status-tables"></a>Tabulky stavu

Služba rozdělení a sloučení poskytuje tabulku **stavem žádosti** v databázi úložiště metadat pro monitorování dokončených a probíhajících požadavků. Tabulka obsahuje řádek pro každou žádost o dělené sloučení, která byla odeslána do této instance služby dělení a slučování. Pro každý požadavek poskytuje tyto informace:

- **Časové razítko**

  Čas a datum spuštění žádosti.

- **OperationId**

  Identifikátor GUID, který jedinečně identifikuje požadavek. Tuto žádost lze také použít k zrušení operace, dokud stále probíhá.

- **Stav**

  Aktuální stav žádosti. V případě průběžných žádostí také uvádí aktuální fázi, ve které je žádost.

- **CancelRequest**

  Příznak, který označuje, zda byla žádost zrušena.

- **Průběh**

  Procentuální odhad dokončení operace. Hodnota 50 znamená, že operace je přibližně 50% dokončeno.

- **Podrobnosti**

  Hodnota XML, která poskytuje podrobnější sestavu průběhu. Zpráva o průběhu se pravidelně aktualizuje, protože sady řádků se zkopírují ze zdroje do cíle. V případě selhání nebo výjimek obsahuje tento sloupec také podrobnější informace o selhání.

### <a name="azure-diagnostics"></a>Azure Diagnostics

Služba dělení a slučování používá pro monitorování a diagnostiku Azure Diagnostics založenou na sadě Azure SDK 2,5. Konfiguraci diagnostiky můžete řídit, jak je vysvětleno zde: [Povolení diagnostiky v Azure Cloud Services a Virtual Machines](../../cloud-services/cloud-services-dotnet-diagnostics.md). Balíček ke stažení obsahuje dvě konfigurace diagnostiky – jeden pro webovou roli a jeden pro roli pracovního procesu. Obsahuje definice pro protokolování čítačů výkonu, protokolů služby IIS, protokolů událostí systému Windows a protokolů událostí aplikací pro dělené sloučení.

## <a name="deploy-diagnostics"></a>Nasadit diagnostiku

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager modul je stále podporován, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

Pokud chcete povolit monitorování a diagnostiku pomocí diagnostické konfigurace pro webové a pracovní role poskytované balíčkem NuGet, spusťte následující příkazy pomocí Azure PowerShell:

```powershell
$storageName = "<azureStorageAccount>"
$key = "<azureStorageAccountKey"
$storageContext = New-AzStorageContext -StorageAccountName $storageName -StorageAccountKey $key
$configPath = "<filePath>\SplitMergeWebContent.diagnostics.xml"
$serviceName = "<cloudServiceName>"

Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext `
    -DiagnosticsConfigurationPath $configPath -ServiceName $serviceName `
    -Slot Production -Role "SplitMergeWeb"

Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext `
    -DiagnosticsConfigurationPath $configPath -ServiceName $serviceName `
    -Slot Production -Role "SplitMergeWorker"
```

Další informace o tom, jak nakonfigurovat a nasadit nastavení diagnostiky, najdete tady: [Povolení diagnostiky v Azure Cloud Services a Virtual Machines](../../cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="retrieve-diagnostics"></a>Načíst diagnostiku

K diagnostice můžete snadno přistupovat ze sady Visual Studio Průzkumník serveru v části Azure stromu Průzkumník serveru. Otevřete instanci sady Visual Studio a v řádku nabídek klikněte na tlačítko Zobrazit a Průzkumník serveru. Kliknutím na ikonu Azure se připojíte k vašemu předplatnému Azure. Pak přejděte do tabulky Azure-> úložiště-> `<your storage account>` -> Tables – > WADLogsTable. Další informace najdete v tématu [Průzkumník serveru](/previous-versions/x603htbk(v=vs.140)).

![WADLogsTable][2]

WADLogsTable zvýrazněný na obrázku výše obsahuje podrobné události z aplikačního protokolu služby dělené sloučení. Všimněte si, že výchozí konfigurace staženého balíčku je zaměřená na produkční nasazení. Proto je interval, ve kterém jsou protokoly a čítače z instancí služby načítány, velký (5 minut). V případě testování a vývoje snižte interval úpravou nastavení diagnostiky webu nebo role pracovního procesu podle vašich potřeb. Klikněte pravým tlačítkem na roli v Průzkumník serveru sady Visual Studio (viz výše) a upravte dobu přenosu v dialogovém okně pro nastavení konfigurace diagnostiky:

![Konfigurace][3]

## <a name="performance"></a>Výkon

Obecně platí, že vyšší výkon se očekává z vyšších, více výkonných úrovní služeb. Zvýšení vstupně-výstupních operací a přidělení paměti pro vyšší úrovně služeb přináší výhody hromadného kopírování a odstraňování operací, které používá služba pro dělené sloučení. Z tohoto důvodu zvyšte úroveň služby jenom pro tyto databáze na definované a omezené časové období.

Služba také provádí ověřovací dotazy jako součást běžných operací. Tyto ověřovací dotazy kontrolují neočekávanou přítomnost dat v cílovém rozsahu a zajišťují, že všechny operace rozdělení, sloučení a přesunu začnou být v konzistentním stavu. Tyto dotazy budou mít veškerou práci nad horizontálního dělení klíčovými rozsahy definovanými oborem operace a velikostí dávky poskytnutou v rámci definice požadavku. Tyto dotazy jsou vykonatelné nejlépe, když je přítomen index, který má horizontálního dělení klíč jako počáteční sloupec.

Vlastnost Unique s klíčem horizontálního dělení jako provedený sloupec navíc umožní službě používat optimalizovaný přístup, který omezuje spotřebu prostředků z hlediska místa v protokolu a paměti. Tato vlastnost jedinečnosti je nutná k přesunutí velkých velikostí dat (obvykle nad 1 GB).

## <a name="how-to-upgrade"></a>Postup upgradu

1. Postupujte podle kroků v části [nasazení služby dělení a slučování](elastic-scale-configure-deploy-split-and-merge.md).
2. Změňte konfigurační soubor cloudové služby pro nasazení rozděleného sloučení tak, aby odrážel nové parametry konfigurace. Nový požadovaný parametr je informace o certifikátu použitém k šifrování. To lze snadno provést tak, že porovnáte nový soubor šablony konfigurace od stažení proti vaší stávající konfiguraci. Nezapomeňte přidat nastavení "DataEncryptionPrimaryCertificateThumbprint" a "DataEncryptionPrimary" pro web i roli pracovního procesu.
3. Před nasazením aktualizace do Azure zajistěte, aby byly dokončeny všechny aktuálně spuštěné operace dělení na sloučení. Můžete to snadno provést dotazování tabulek stavem žádosti a PendingWorkflows v databázi s metadaty pro dělené sloučení pro probíhající požadavky.
4. Aktualizujte stávající nasazení cloudové služby pro rozdělené sloučení v předplatném Azure pomocí nového balíčku a aktualizovaného konfiguračního souboru služby.

Nemusíte zřizovat novou databázi metadat pro rozdělení a sloučení pro upgrade. Nová verze bude automaticky upgradovat stávající databázi metadat na novou verzi.

## <a name="best-practices--troubleshooting"></a>Osvědčené postupy a řešení potíží

- Definujte testovacího tenanta a vyzkoušejte své nejdůležitější operace rozdělení, sloučení nebo přesunutí s testovacím klientem napříč několika horizontálních oddílů. Zajistěte, aby byla v mapě horizontálních oddílů správně definovaná všechna metadata a aby operace neporušila omezení nebo cizí klíče.
- Udržujte velikost testovacích dat tenanta nad maximální velikostí dat vašeho největšího tenanta, abyste se ujistili, že se nesetkáte s problémy souvisejícími s velikostí dat. To vám pomůže vyhodnotit horní mez v době, kdy je potřeba přesunout jednoho tenanta kolem.
- Ujistěte se, že vaše schéma umožňuje odstranění. Služba rozdělení a sloučení vyžaduje možnost odebrání dat ze zdrojového horizontálních oddílů, jakmile se data úspěšně zkopírují do cíle. Například **triggery Delete** můžou zabránit službě ve odstranění dat na zdroji a můžou způsobit selhání operací.
- Klíč horizontálního dělení by měl být počátečním sloupcem v definici primárního klíče nebo jedinečného indexu. Který zajišťuje nejlepší výkon pro dotazy na rozdělené nebo slučovací ověřování a pro skutečné operace přesunu a odstranění dat, které vždy pracují s rozsahy horizontálního děleních klíčů.
- Společné umístění službu pro dělení a slučování v oblasti a datovém centru, kde jsou umístěny vaše databáze.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/elastic-scale-overview-split-and-merge/diagnostics-config.png
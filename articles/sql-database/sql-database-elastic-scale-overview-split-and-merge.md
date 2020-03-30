---
title: Přesun dat mezi cloudovými databázemi s horizontálním navýšením kapacity
description: Vysvětluje, jak manipulovat s úlomky a přesouvat data prostřednictvím samoobslužné služby pomocí elastických databázových api.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 8b0db4a1e55b53165e40e176834d66b62926e24b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74421552"
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Přesun dat mezi cloudovými databázemi s horizontálním navýšením kapacity

Pokud jste vývojář softwaru jako služba a najednou vaše aplikace prochází obrovskou poptávkou, musíte růst přizpůsobit. Takže přidáte další databáze (střepy). Jak redistribuovat data do nových databází bez narušení integrity dat? Pomocí **nástroje rozdělení sloučení přesuňte** data z omezených databází do nových databází.  

Nástroj pro rozdělení sloučení se spustí jako webová služba Azure. Správce nebo vývojář používá nástroj k přesunutí shardlets (data z úlomku) mezi různými databázemi (střepy). Nástroj používá správu mapy sypkých modulů k udržování databáze metadat služby a k zajištění konzistentního mapování.

![Přehled][1]

## <a name="download"></a>Stáhnout

[Microsoft.Azure.sqldatabase.ElasticScale.Service.SplitMerge](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Dokumentace

1. [Kurz nástroje rozdělení sloučení elastické databáze](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
2. [Konfigurace zabezpečení rozdělení sloučení](sql-database-elastic-scale-split-merge-security-configuration.md)
3. [Důležité informace o zabezpečení rozdělení sloučení](sql-database-elastic-scale-split-merge-security-configuration.md)
4. [Správa mapování horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md)
5. [Migrace existujících databází pro horizontální navýšení kapacity](sql-database-elastic-convert-to-use-elastic-tools.md)
6. [Nástroje pro elastické databáze](sql-database-elastic-scale-introduction.md)
7. [Glosář nástrojů elastické databáze](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Proč používat nástroj rozdělení sloučení

- **Flexibilita**

  Aplikace se musí flexibilně roztáhnout za hranice jedné databáze Azure SQL DB. Pomocí nástroje můžete přesunout data podle potřeby do nových databází při zachování integrity.

- **Rozdělit růst**

  Chcete-li zvýšit celkovou kapacitu pro zpracování explozivního růstu, vytvořte další kapacitu rozdělením dat a jejich distribucí mezi postupně více databází, dokud nebudou splněny potřeby kapacity. Toto je ukázkový příklad **funkce rozdělení.**

- **Sloučit, aby se zmenšilo**

  Potřeby kapacity se zmenšují kvůli sezónní povaze podniku. Nástroj umožňuje škálovat na méně jednotek škálování, když se podnikání zpomaluje. Funkce "sloučení" ve službě rozdělení sloučení elastického měřítka pokrývá tento požadavek.

- **Správa aktivních oblastí přesunutím shardlets**

  S více klienty na databázi přidělení shardlets na úlomky může vést k kritické body kapacity na některé úlomky. To vyžaduje opětovné přidělení shardlets nebo přesunutí zaneprázdněný shardlets na nové nebo méně využité střepy.

## <a name="concepts--key-features"></a>Koncepty & klíčové vlastnosti

- **Služby hostované zákazníky**

  Rozdělení sloučení je dodávánjako služba hostovaná zákazníkem. Službu musíte nasadit a hostovat v předplatném Microsoft Azure. Balíček, který stáhnete z NuGet obsahuje konfigurační šablonu pro dokončení s informacemi pro konkrétní nasazení. Podrobnosti najdete v [kurzu rozdělení sloučení.](sql-database-elastic-scale-configure-deploy-split-and-merge.md) Vzhledem k tomu, že služba běží ve vašem předplatném Azure, můžete řídit a konfigurovat většinu aspektů zabezpečení služby. Výchozí šablona obsahuje možnosti konfigurace protokolu SSL, ověřování klientů na základě certifikátů, šifrování pro uložená pověření, ochranu DoS a omezení IP. Další informace o aspektech zabezpečení naleznete v následující [konfiguraci zabezpečení rozdělení sloučení](sql-database-elastic-scale-split-merge-security-configuration.md)dokumentů .

  Výchozí nasazená služba je spuštěna s jedním pracovníkem a jednou webovou rolí. Každý používá velikost virtuálního počítače A1 v Cloudových službách Azure. I když nelze změnit tato nastavení při nasazování balíčku, můžete je změnit po úspěšném nasazení v běžící cloudové službě (prostřednictvím portálu Azure). Všimněte si, že role pracovníka nesmí být nakonfigurována pro více než jednu instanci z technických důvodů.

- **Integrace mapy střepů**

  Služba rozdělení sloučení spolupracuje s mapou střepů aplikace. Při použití služby rozdělení sloučení rozdělit nebo sloučit rozsahy nebo přesunout shardlety mezi střepy, služba automaticky udržuje mapování střepů aktuální. Chcete-li tak učinit, služba se připojí k databázi správce mapy susi aplikace a udržuje rozsahy a mapování jako průběh požadavků na rozdělení/sloučení/přesunutí. Tím je zajištěno, že mapa skládek vždy představuje aktuální zobrazení při rozdělení operace sloučení probíhají. Operace přesunu rozdělení, sloučení a shardletu jsou implementovány přesunutím dávky shardlets ze zdrojového štřepu do cílového střetu. Během operace pohybu shardlet shardlets podléhají aktuální dávka jsou označeny jako offline v mapě střepu a nejsou k dispozici pro připojení směrování závislé na datech pomocí **rozhraní OPENConnectionForKey** API.

- **Konzistentní připojení shardletu**

  Při spuštění přesunu dat pro novou dávku shardlets, všechny shard-map za předpokladu, že směrování závislé na datech soddíl ukládající shardlet jsou usmrceny a následná připojení z rozhraní API mapy svižků jsou blokovány, zatímco přesun dat je s cílem zabránit nesrovnalostem. Připojení k jiným shardlets na stejném úlomku bude také zabít, ale bude úspěšné znovu okamžitě na opakování. Po přesunutí dávky jsou shardlety znovu označeny online pro cílový úlomek a zdrojová data jsou odebrána ze zdrojového oddílu. Služba prochází těmito kroky pro každou dávku, dokud nebyly přesunuty všechny shardlety. To povede k několika operacím usmrcení připojení v průběhu celé operace rozdělení/sloučení/přesunutí.  

- **Správa dostupnosti shardlet**

  Omezení připojení usmrcování na aktuální dávku shardlets, jak je popsáno výše omezuje rozsah nedostupnosti na jednu dávku shardlets najednou. To je upřednostňováno před přístupem, kde by úplný oddíl oddílu zůstal offline pro všechny jeho shardlety v průběhu operace rozdělení nebo sloučení. Velikost dávky, definované jako počet různých shardlets přesunout najednou, je parametr konfigurace. Lze definovat pro každou operaci rozdělení a sloučení v závislosti na dostupnosti a výkonu aplikace. Všimněte si, že rozsah, který je uzamčen v mapě střepu může být větší než zadaná velikost dávky. Důvodem je, že služba vybere velikost rozsahu tak, aby skutečný počet hodnot klíče s ráždí v datech přibližně odpovídá velikosti dávky. To je důležité mít na paměti, zejména pro řídce osídlené klíče nálože.

- **Úložiště metadat**

  Služba rozdělení sloučení používá databázi k udržení svého stavu a k uchovávaní protokolů během zpracování požadavků. Uživatel vytvoří tuto databázi ve svém předplatném a poskytne připojovací řetězec pro něj v konfiguračním souboru pro nasazení služby. Správci z organizace uživatele se také mohou připojit k této databázi, aby zkontrolovali průběh požadavku a prozkoumali podrobné informace týkající se potenciálních selhání.

- **Sharding-povědomí**

  Služba rozdělení sloučení rozlišuje mezi (1) tabulky s rozdělením, (2) referenční tabulky a (3) normální tabulky. Sémantika operace rozdělení/sloučení/přesunutí závisí na typu použité tabulky a je definována takto:

  - **Tabulky s tažné tabulky s tisovými**

    Operace rozdělení, sloučení a přesunutí přesunout shardlety ze zdroje do cílového úlomek. Po úspěšném dokončení celkové požadavku tyto shardlety již nejsou k dispozici ve zdroji. Všimněte si, že cílové tabulky musí existovat na cílovém šiřidla a nesmí obsahovat data v cílové oblasti před zpracováním operace.

  - **Referenční tabulky**

    Pro referenční tabulky operace rozdělení, sloučení a přesunutí zkopírují data ze zdroje do cílového oddílu. Všimněte si však, že žádné změny dojít na cílový šiř pro danou tabulku, pokud některý řádek je již k dispozici v této tabulce na cíl. Tabulka musí být prázdná pro všechny operace kopírování referenční tabulky, aby byla zpracována.

  - **Ostatní tabulky**

    Ostatní tabulky mohou být k dispozici na zdroj nebo cíl operace rozdělení a sloučení. Služba rozdělení sloučení ignoruje tyto tabulky pro všechny operace přesunu dat nebo kopírování. Všimněte si však, že mohou zasahovat do těchto operací v případě omezení.

    Informace o odkaz vs. rozdělení tabulky jsou `SchemaInfo` poskytovány rozhraní API na mapě oddílu. Následující příklad ilustruje použití těchto rozhraní API na daný objekt správce mapy svižného oddílu:

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

    Tabulky "region" a "nation" jsou definovány jako referenční tabulky a budou zkopírovány operacemi rozdělení/sloučení/přesunutí. 'zákazník' a 'objednávky' zase jsou definovány jako střežnice tabulky. `C_CUSTKEY`a `O_CUSTKEY` slouží jako klíč s tvrdoštitou.

- **Referenční integrita**

  Služba rozdělení sloučení analyzuje závislosti mezi tabulkami a používá vztahy cizího klíče a primárního klíče k vytvoření operací pro přesunutí referenčních tabulek a shardlets. Obecně platí, že referenční tabulky jsou zkopírovány nejprve v pořadí závislostí, potom shardlety jsou zkopírovány v pořadí jejich závislostí v rámci každé dávky. To je nezbytné, aby fk-pk omezení na cílový oddíl jsou dodrženy jako nová data dorazí.

- **Konzistence mapy úložného oddílu a případné dokončení**

  V případě selhání služba rozdělení sloučení obnoví operace po výpadku a jeho cílem je dokončit všechny probíhající požadavky. Mohou však existovat neopravitelné situace, například při ztrátě nebo ohrožení zabezpečení cílového úlomku. Za těchto okolností některé shardlety, které měly být přesunuty může i nadále nacházet na zdrojové mřížce. Služba zajišťuje, že mapování shardletu jsou aktualizovány až po úspěšném zkopírování potřebných dat do cíle. Shardlety jsou odstraněny ve zdroji pouze po všechna jejich data byla zkopírována do cíle a odpovídající mapování byly úspěšně aktualizovány. Operace odstranění probíhá na pozadí, zatímco rozsah je již online na cílovéhorizontálního oddílu. Služba rozdělení sloučení vždy zajišťuje správnost mapování uložených v mapě střepů.

## <a name="the-split-merge-user-interface"></a>Uživatelské rozhraní rozdělené sloučit

Balíček služby rozdělené hromadné korespondence obsahuje roli pracovního procesu a webovou roli. Webová role se používá k interaktivnímu odesílání žádostí o rozdělení sloučení. Hlavní součásti uživatelského rozhraní jsou následující:

- **Typ operace**

  Typ operace je přepínací tlačítko, které řídí druh operace prováděné službou pro tento požadavek. Můžete si vybrat mezi scénářerozdělení, sloučení a přesunutí. Můžete také zrušit dříve odeslanou operaci. Můžete použít požadavky na rozdělení, sloučení a přesunutí map úlomků rozsahu. Seznam map šmejdů podporuje pouze operace přesunutí.

- **Mapa úlomků**

  Další část parametrů požadavku zahrnuje informace o mapě střepů a databázi hostující mapu svižného objektu. Zejména je třeba zadat název serveru Azure SQL Database a databáze hostující shardmap, přihlašovací údaje pro připojení k databázi mapy střepů a nakonec název mapy střepů. V současné době operace přijímá pouze jednu sadu pověření. Tato pověření musí mít dostatečná oprávnění k provádění změn mapy úlomků a také uživatelských dat na úlomcích.

- **Rozsah zdroje (rozdělení a sloučení)**

  Operace rozdělení a sloučení zpracovává rozsah pomocí nízkého a vysokého klíče. Chcete-li určit operaci s neohraničenou vysokou hodnotou klíče, zaškrtněte políčko "Vysoká hodnota je max" a pole vysokého klíče ponechte prázdné. Hodnoty klíče rozsahu, které zadáte, nemusí přesně odpovídat mapování a jeho hranice v mapě oddílu. Pokud nezadáte žádné hranice rozsahu vůbec služba odvodí nejbližší rozsah pro vás automaticky. Skript Prostředí PowerShell GetMappings.ps1 můžete použít k načtení aktuálního mapování v dané mapě šitrů.

- **Chování rozděleného zdroje (rozdělení)**

  Pro rozdělené operace definujte bod pro rozdělení zdrojové oblasti. To provést poskytnutím klíče srážlivých užitků, kde chcete provést rozdělení. Pomocí přepínacího tlačítka určete, zda se má přesunout spodní část rozsahu (kromě rozdělovacího klíče) nebo zda se má horní část přesunout (včetně rozdělovacího klíče).

- **Zdroj Shardlet (přesunout)**

  Operace přesunutí se liší od operací rozdělení nebo sloučení, protože nevyžadují rozsah k popisu zdroje. Zdroj pro přesunutí je jednoduše identifikován hodnotou klíče s ráždí, kterou plánujete přesunout.

- **Cílový úlomek (rozdělení)**

  Jakmile zadáte informace o zdroji operace rozdělení, musíte definovat, kam chcete data zkopírovat poskytnutím serveru Azure SQL Db a názvu databáze pro cíl.

- **Cílový rozsah (sloučení)**

  Operace sloučení přesunout shardlety do existujícího šněrového oddílu. Existující úlomek identifikujete poskytnutím hranic rozsahu existující oblasti, se kterou chcete sloučit.

- **Velikost dávky**

  Velikost dávky určuje počet shardlets, které přejdou do režimu offline v době, během přesunu dat. Toto je celá hodnota, kde můžete použít menší hodnoty, když jste citliví na dlouhé období prostojů pro shardlety. Větší hodnoty zvýší čas, který daný shardlet je offline, ale může zlepšit výkon.

- **ID operace (zrušit)**

  Pokud máte probíhající operaci, která již není potřeba, můžete operaci zrušit poskytnutím Jejího ID operace v tomto poli. ID operace můžete načíst z tabulky stavu požadavku (viz oddíl 8.1) nebo z výstupu ve webovém prohlížeči, kde jste žádost odeslali.

## <a name="requirements-and-limitations"></a>Požadavky a omezení

Současná implementace služby split-merge podléhá následujícím požadavkům a omezením:

- Oddíly musí existovat a být registrovány v mapě střeppřed zahájením rozdělení před rozdělení množiny lze provést.
- Služba nevytváří tabulky nebo jiné databázové objekty automaticky jako součást svých operací. To znamená, že schéma pro všechny rozdělené tabulky a referenční tabulky musí existovat na cílovém oddílu před jakoukoli operací rozdělení/sloučení/přesunutí. Zejména tabulky s rozdělením jsou nutné být prázdné v rozsahu, kde mají být přidány nové shardlety operací rozdělení/sloučení/přesunutí. V opačném případě operace se nezdaří počáteční kontrola konzistence na cílový šiř. Všimněte si také, že referenční data jsou zkopírována pouze v případě, že referenční tabulka je prázdná a že neexistují žádné záruky konzistence s ohledem na jiné souběžné operace zápisu v referenčních tabulkách. Doporučujeme toto: při spuštění operací rozdělení/sloučení žádné jiné operace zápisu provádět změny v referenčních tabulkách.
- Služba závisí na identitě řádku vytvořené jedinečným indexem nebo klíčem, který obsahuje klíč pro sharding pro zlepšení výkonu a spolehlivosti pro velké shardlety. To umožňuje službě přesunout data s ještě jemnější rozlišovací schopnost než pouze hodnota klíče s ráždí. To pomáhá snížit maximální množství místa protokolu a zámky, které jsou požadovány během operace. Zvažte vytvoření jedinečného indexu nebo primárního klíče včetně klíče pro rozdělení v dané tabulce, pokud chcete tuto tabulku použít s požadavky na rozdělení/sloučení/přesunutí. Z důvodů výkonu by měl být klíč s ráždí úvodním sloupcem v klíči nebo indexu.
- V průběhu zpracování požadavků mohou být některá data shardlet k dispozici ve zdroji i v cílovém úlomku. To je nezbytné k ochraně před poruchami během pohybu shardletu. Integrace rozdělení sloučení s mapou střepu zajišťuje, že připojení prostřednictvím rozhraní API směrování závislých na datech pomocí metody **OpenConnectionForKey** na mapě střepu neuvidí žádné nekonzistentní mezilehlé stavy. Však při připojování ke zdroji nebo cílové šřepy bez použití **OpenConnectionForKey** metoda, nekonzistentní mezilehlé stavy mohou být viditelné při rozdělení/ sloučení/přesunutí požadavky probíhají. Tato připojení mohou zobrazit částečné nebo duplicitní výsledky v závislosti na časování nebo úlomek základní připojení. Toto omezení aktuálně zahrnuje připojení provedené elastické škálování více horizontálních oddílů-dotazy.
- Databáze metadat pro službu rozdělení sloučení nesmí být sdílena mezi různými rolemi. Například role služby rozdělení sloučení spuštěné v pracovní min. musí ukazovat na jinou databázi metadat než produkční roli.

## <a name="billing"></a>Fakturace

Služba rozdělení sloučení se ve vašem předplatném Microsoft Azure spouští jako cloudová služba. Proto se na vaši instanci služby vztahují poplatky za cloudové služby. Pokud často neprovádíte operace rozdělení/sloučení/přesunutí, doporučujeme odstranit cloudovou službu s ložené. To šetří náklady na spouštění nebo nasazované instance cloudových služeb. Můžete znovu nasadit a spustit snadno spustitelné konfigurace kdykoli budete potřebovat provést operace rozdělení nebo sloučení.

## <a name="monitoring"></a>Monitorování

### <a name="status-tables"></a>Stavové tabulky

Služba rozdělení sloučení poskytuje tabulku **RequestStatus** v databázi úložiště metadat pro sledování dokončených a průběžných požadavků. Tabulka obsahuje seznam řádků pro každý požadavek na rozdělení sloučení, který byl odeslán do této instance služby rozdělení sloučení. Pro každou žádost poskytuje tyto informace:

- **Časové razítko**

  Čas a datum, kdy byl požadavek spuštěn.

- **Operační id**

  Identifikátor GUID, který jednoznačně identifikuje požadavek. Tento požadavek lze také zrušit operaci, zatímco stále probíhá.

- **Stav**

  Aktuální stav požadavku. U probíhajících požadavků také uvádí aktuální fázi, ve které je požadavek.

- **Zrušit požadavek**

  Příznak, který označuje, zda byl požadavek zrušen.

- **Pokroku**

  Procentuální odhad dokončení operace. Hodnota 50 označuje, že operace je přibližně 50 % dokončena.

- **Podrobnosti**

  Hodnota XML, která poskytuje podrobnější zprávu o průběhu. Sestava průběhu je pravidelně aktualizována, protože sady řádků jsou zkopírovány ze zdroje do cíle. V případě selhání nebo výjimek obsahuje tento sloupec také podrobnější informace o selhání.

### <a name="azure-diagnostics"></a>Azure Diagnostics

Služba rozdělení sloučení používá Azure Diagnostics na základě Azure SDK 2.5 pro monitorování a diagnostiku. Řídíte konfiguraci diagnostiky, jak je vysvětleno zde: [Povolení diagnostiky v Cloudových službách Azure a virtuálních počítačích](../cloud-services/cloud-services-dotnet-diagnostics.md). Balíček ke stažení obsahuje dvě konfigurace diagnostiky - jednu pro webovou roli a jednu pro roli pracovního procesu. Obsahuje definice protokolovat čítače výkonu, protokoly služby IIS, protokoly událostí systému Windows a protokoly událostí rozdělené sloučit.

## <a name="deploy-diagnostics"></a>Nasazení diagnostiky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Tyto rutiny naleznete v tématu [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické.

Chcete-li povolit monitorování a diagnostiku pomocí diagnostické konfigurace pro webové a pracovní role poskytované balíčkem NuGet, spusťte pomocí Azure PowerShellu následující příkazy:

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

Další informace o konfiguraci a nasazení nastavení diagnostiky najdete tady: [Povolení diagnostiky v Cloudových službách Azure a virtuálních počítačích](../cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="retrieve-diagnostics"></a>Načíst diagnostiku

K diagnostice můžete snadno přistupovat z Průzkumníka serveru Visual Studio v části Azure ve stromu Průzkumníkserveru. Otevřete instanci sady Visual Studio a na řádku nabídek klikněte na Zobrazení a Průzkumník serveru. Kliknutím na ikonu Azure se připojte ke svému předplatnému Azure. Pak přejděte na Azure `<your storage account>` -> Storage -> -> tabulky -> WADLogsTable. Další informace naleznete v [tématu Průzkumník serveru](https://msdn.microsoft.com/library/x603htbk.aspx).

![Tabulka WADLogsTable][2]

WadLogsTable zvýrazněné na obrázku výše obsahuje podrobné události z protokolu aplikací služby split-merge. Všimněte si, že výchozí konfigurace staženého balíčku je zaměřena na nasazení v produkčním prostředí. Proto je velký interval, ve kterém jsou protokoly a čítače vyžádáno z instancí služby (5 minut). Pro testování a vývoj snižte interval úpravou nastavení diagnostiky webu nebo role pracovního procesu podle vašich potřeb. Klikněte pravým tlačítkem myši na roli v Průzkumníkovi serveru sady Visual Studio (viz výše) a potom upravte dobu přenosu v dialogovém okně pro nastavení konfigurace diagnostiky:

![Konfigurace][3]

## <a name="performance"></a>Výkon

Obecně platí, že lepší výkon lze očekávat od vyšší, výkonnější úrovně služeb v Azure SQL Database. Vyšší přidělení vstupně-up, procesor a paměť pro vyšší úrovně služby prospěch hromadné kopírování a odstranění operace, které používá služba rozdělení sloučení. Z tohoto důvodu zvýšit úroveň služby pouze pro tyto databáze pro definované, omezené časové období.

Služba také provádí ověřovací dotazy jako součást svých běžných operací. Tyto ověřovací dotazy kontrolují neočekávanou přítomnost dat v cílovém rozsahu a zajišťují, že všechny operace rozdělení/sloučení/přesunutí začínají z konzistentního stavu. Tyto dotazy všechny práce přes rozsahy klíčů s ráží definované rozsahy operace a velikost dávky poskytované jako součást definice požadavku. Tyto dotazy provádět nejlépe, pokud je k dispozici index, který má klíč srážlivých položek jako úvodní sloupec.

Kromě toho vlastnost jedinečnosti s klíčem srážlivosti jako úvodní sloupec umožní službě používat optimalizovaný přístup, který omezuje spotřebu prostředků z hlediska prostoru protokolu a paměti. Tato vlastnost jedinečnosti je vyžadována k přesunutí velkých velikostí dat (obvykle nad 1 GB).

## <a name="how-to-upgrade"></a>Jak upgradovat

1. Postupujte podle pokynů v [části Nasazení služby rozdělení sloučení](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
2. Změňte konfigurační soubor cloudové služby pro nasazení rozdělené hromadné korespondence tak, aby odrážel nové parametry konfigurace. Novým povinným parametrem jsou informace o certifikátu použitém pro šifrování. Snadný způsob, jak to udělat, je porovnat nový soubor šablony konfigurace ze stahování proti vaší stávající konfiguraci. Ujistěte se, že přidáte nastavení pro "DataEncryptionPrimaryCertificateThumbprint" a "DataEncryptionPrimary" pro web i roli pracovního procesu.
3. Před nasazením aktualizace do Azure se ujistěte, že všechny aktuálně spuštěné operace rozdělení sloučení byly dokončeny. To lze snadno provést dotazem na tabulky RequestStatus a PendingWorkflows v databázi metadat rozdělení sloučení pro průběžné požadavky.
4. Aktualizujte stávající nasazení cloudové služby pro rozdělení sloučení v předplatném Azure s novým balíčkem a aktualizovaným konfiguračním souborem služby.

Není nutné zřídit novou databázi metadat pro rozdělení sloučení k upgradu. Nová verze automaticky inovuje stávající databázi metadat na novou verzi.

## <a name="best-practices--troubleshooting"></a>Osvědčené postupy a řešení potíží

- Definujte testovacího klienta a prováďte nejdůležitější operace rozdělení/sloučení/přesunutí s testovacím tenantem napříč několika oddíly. Ujistěte se, že všechna metadata jsou definována správně v mapě oddílu a že operace neporušují omezení nebo cizí klíče.
- Udržujte velikost dat testovacího klienta nad maximální velikostí dat největšího klienta, abyste se ujistili, že se nesetkáváte s problémy souvisejícími s velikostí dat. To vám pomůže posoudit horní mez na čas potřebný k přesunutí jednoho klienta.
- Ujistěte se, že schéma umožňuje odstranění. Služba rozdělení sloučení vyžaduje možnost odebrat data ze zdrojového oddílu, jakmile byla data úspěšně zkopírována do cíle. Například **odstranění aktivačních událostí** může zabránit službě odstranění dat ve zdroji a může způsobit selhání operací.
- Klíč s ráždí by měl být úvodní sloupec v definici primárního klíče nebo jedinečného indexu. To zajišťuje nejlepší výkon pro dotazy ověření rozdělení nebo sloučení a pro skutečné operace přesunu a odstranění dat, které vždy pracují na rozsahech klíčů horizontálního oddílu.
- Služba rozdělení sloučení se můžete shromažďovat v oblasti a datovém centru, kde se nacházejí databáze.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png

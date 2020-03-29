---
title: Integrace Azure Data Exploreru s Azure Data Factory
description: V tomto tématu integrujte Azure Data Explorer s Azure Data Factory a použijte aktivity kopírování, vyhledávání a příkazů.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: bb08cf4db45a378b35a8245eadd56a2ab3e48bab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293619"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>Integrace Azure Data Exploreru s Azure Data Factory

[Azure Data Factory](/azure/data-factory/) (ADF) je cloudová služba integrace dat, která umožňuje integrovat různá úložiště dat a provádět aktivity s daty. ADF umožňuje vytvářet pracovní postupy řízené daty pro orchestraci a automatizaci pohybu dat a transformace dat. Azure Data Explorer je jedno z [podporovaných úložišť dat](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) v Azure Data Factory. 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Aktivity Azure Data Factory pro Azure Data Explorer

Pro uživatele Azure Data Exploreru jsou k dispozici různé integrace s Azure Data Factory:

### <a name="copy-activity"></a>Aktivita kopírování
 
Aktivita kopírování Azure Data Factory se používá k přenosu dat mezi úložišti dat. Azure Data Explorer je podporovaný jako zdroj, kde se data zkopírují z Průzkumníka dat Azure do libovolného podporovaného úložiště dat a jímky, kde se data zkopírují z libovolného podporovaného úložiště dat do Azure Data Exploreru. Další informace najdete v [tématu kopírování dat do nebo z Azure Data Explorer pomocí Azure Data Factory](/azure/data-factory/connector-azure-data-explorer). a pro podrobný návod zobrazit [data zatížení z Azure Data Factory do Průzkumníka dat Azure](data-factory-load-data.md).
Azure Data Explorer je podporovaný Azure IR (Integration Runtime), který se používá při kopírování dat v rámci Azure a infračerveného zařízení hostovaného sami, které se používají při kopírování dat z/do datových úložišť umístěných místně nebo v síti s řízením přístupu, jako je virtuální síť Azure. Další informace naleznete v tématu [které infračervené](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use)
 
> [!TIP]
> Při použití aktivity kopírování a vytvoření **propojené služby** nebo **datové sady**vyberte Průzkumník dat **Azure (Kusto) a** ne staré úložiště dat **Kusto**.  

### <a name="lookup-activity"></a>Vyhledávací aktivita
 
Vyhledávací aktivita se používá pro provádění dotazů v Průzkumníku dat Azure. Výsledek dotazu bude vrácen jako výstup aktivity vyhledávání a lze jej použít v další aktivitě v kanálu, jak je popsáno v [dokumentaci k vyhledávání adf](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity).  
Kromě limitu velikosti odpovědi 5 000 řádků a 2 MB má aktivita také časový limit dotazu 1 hodinu.

### <a name="command-activity"></a>Aktivita příkazu

Aktivita příkazu umožňuje provádění [řídicích příkazů](/azure/kusto/concepts/#control-commands)Průzkumníka dat Azure . Na rozdíl od dotazů mohou řídicí příkazy potenciálně upravovat data nebo metadata. Některé příkazy ovládacího prvku jsou cílené na ingestování dat `.ingest`do `.set-or-append`Průzkumníka dat Azure, pomocí příkazů, jako jsou `.export`nebo ) nebo kopírování dat z Průzkumníka dat Azure do externích úložišť dat pomocí příkazů, jako je .
Podrobný návod k aktivitě příkazu najdete v [tématu spuštění příkazů řízení Azure Data Explorer pomocí aktivity příkazů Azure Data Factory](data-factory-command-activity.md).  Použití příkazu ovládacího prvku ke kopírování dat může být někdy rychlejší a levnější možností než aktivita kopírování. Chcete-li určit, kdy se má použít aktivita příkazu versus aktivita kopírování, [přečtěte si informace mezi aktivitami kopírování a příkazů při kopírování dat](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data).

### <a name="copy-in-bulk-from-a-database-template"></a>Hromadná kopírování ze šablony databáze

[Hromadné kopírování z databáze do Průzkumníka dat Azure pomocí šablony Azure Data Factory](data-factory-template.md) je předdefinovaný kanál Azure Data Factory. Šablona se používá k vytvoření mnoha kanálů na databázi nebo na tabulku pro rychlejší kopírování dat. 

### <a name="mapping-data-flows"></a>Toky dat mapování 

[Toky dat mapování Azure Data Factory](/azure/data-factory/concepts-data-flow-overview) jsou vizuálně navržené transformace dat, které umožňují datovým inženýrům vyvíjet logiku grafické transformace dat bez psaní kódu. Pokud chcete vytvořit tok dat a ingestovat data do Průzkumníka dat Azure, použijte následující metodu:

1. Vytvořte [tok dat mapování](/azure/data-factory/data-flow-create).
1. [Exportujte data do objektu Blob Azure](/azure/data-factory/data-flow-sink). 
1. Definujte [aktivitu](/azure/data-explorer/ingest-data-event-grid) kopírování Event Grid nebo [ADF](/azure/data-explorer/data-factory-load-data) pro ingestování dat do Průzkumníka dat Azure.

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>Výběr mezi aktivitami příkazu Kopírovat a Azure Data Exploreru při kopírování dat 

Tato část vám pomůže při výběru správné aktivity pro vaše potřeby kopírování dat.

Při kopírování dat z nebo do Azure Data Explorer, existují dvě dostupné možnosti v Azure Data Factory:
* Kopírovat aktivitu.
* Aktivita příkazu Azure Data Explorer, která spouští jeden z příkazů ovládacího prvku, které přenášejí data v Průzkumníku dat Azure. 

### <a name="copy-data-from-azure-data-explorer"></a>Kopírování dat z Průzkumníka dat Azure
  
Data z Průzkumníka dat Azure můžete zkopírovat pomocí aktivity kopírování nebo příkazu. [`.export`](/azure/kusto/management/data-export/) Příkaz `.export` provede dotaz a potom exportuje výsledky dotazu. 

V následující tabulce najdete porovnání aktivity kopírování a `.export` příkazpro kopírování dat z Průzkumníka dat Azure.

| | Aktivita kopírování | Příkaz .export |
|---|---|---|
| **Popis toku** | ADF provede dotaz na Kusto, zpracuje výsledek a odešle jej do cílového úložiště dat. <br>**(ADX > úložiště dat > jímky)** | ADF odešle příkaz ovládacího `.export` prvku do Průzkumníka dat Azure, který příkaz provede a odešle data přímo do cílového úložiště dat. <br>(**Úložiště dat > jímky ADX)** |
| **Podporovaná úložiště cílových dat** | Široká škála [podporovaných úložišť dat](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLSv2, Objekt blob Azure, databáze SQL |
| **Výkon** | Centralizované | <ul><li>Distribuované (výchozí), export dat z více uzlů současně</li><li>Rychlejší a cogs efektivní.</li></ul> |
| **Limity serveru** | [Omezení dotazů](/azure/kusto/concepts/querylimits) lze rozšířit/zakázat. Ve výchozím nastavení obsahují dotazy ADF: <ul><li>Limit velikosti 500 000 záznamů nebo 64 MB.</li><li>Časový limit 10 minut.</li><li>`noTruncation`nastavena na hodnotu false.</li></ul> | Ve výchozím nastavení rozšiřuje nebo zakáže omezení dotazů: <ul><li>Omezení velikosti jsou zakázána.</li><li>Časový čas serveru je prodloužena na 1 hodinu.</li><li>`MaxMemoryConsumptionPerIterator`a `MaxMemoryConsumptionPerQueryPerNode` jsou rozšířeny na max (5 GB, TotalPhysicalMemory/2).</li></ul>

> [!TIP]
> Pokud je cíl kopírování jedním z úložišť dat podporovaných `.export` příkazem a pokud žádná z `.export` funkcí aktivity kopírování není pro vaše potřeby klíčová, vyberte příkaz.

### <a name="copying-data-to-azure-data-explorer"></a>Kopírování dat do Průzkumníka dat Azure

Data do Průzkumníka dat Azure můžete zkopírovat pomocí příkazů aktivity kopírování nebo`.set-or-append` `.set-or-replace`ingestování, jako je`.ingest` [například ingestování z dotazu](/azure/kusto/management/data-ingestion/ingest-from-query) ( , , `.set` `.replace)`, a [ingestování z úložiště](/azure/kusto/management/data-ingestion/ingest-from-storage) ( ). 

V následující tabulce najdete porovnání aktivity kopírování a příkazy pro ingestování pro kopírování dat do Průzkumníka dat Azure.

| | Aktivita kopírování | Ingestování z dotazu<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | Ingestování z úložiště <br> `.ingest` |
|---|---|---|---|
| **Popis toku** | ADF získá data ze zdrojového úložiště dat, převede je do tabulkového formátu a provádí požadované změny mapování schématu. ADF pak nahraje data do objektů BLOB Azure, rozdělí je na bloky dat a pak stáhne objekty BLOB, aby je ingestoval do tabulky ADX. <br> **(Zdrojové úložiště dat > objektů BLOB adf > Azure > ADX)** | Tyto příkazy lze spustit `.show` dotaz nebo příkaz a ingestovat výsledky dotazu do tabulky (**ADX > ADX**). | Tento příkaz ingestuje data do tabulky "tahání" data z jednoho nebo více artefaktů úložiště cloudu. |
| **Podporovaná úložiště zdrojových dat** |  [různé možnosti](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS Gen 2, Azure Blob, SQL (pomocí sql_request plugin), Cosmos (pomocí cosmosdb_sql_request plugin) a jakékoli jiné úložiště dat, které poskytuje HTTP nebo Python api. | Souborový systém, úložiště objektů blob Azure, ADLS Gen 1, ADLS Gen 2 |
| **Výkon** | Ingestování jsou zařazeny do fronty a spravovány, což zajišťuje malé velikosti ingestování a zajišťuje vysokou dostupnost tím, že poskytuje vyrovnávání zatížení, opakování a zpracování chyb. | <ul><li>Tyto příkazy nebyly navrženy pro import dat s velkým objemem.</li><li>Funguje podle očekávání a levnější. Ale pro produkční scénáře a pokud jsou velké přenosové rychlosti a velikosti dat, použijte aktivitu Copy.</li></ul> |
| **Limity serveru** | <ul><li>Bez omezení velikosti.</li><li>Maximální časový limit: 1 hodina na poživatek blob. |<ul><li>V části dotazu je pouze limit velikosti, který lze `noTruncation=true`přeskočit zadáním .</li><li>Maximální časový limit: 1 hodina.</li></ul> | <ul><li>Bez omezení velikosti.</li><li>Maximální časový limit: 1 hodina.</li></ul>|

> [!TIP]
> * Při kopírování dat z ADF do `ingest from query` Průzkumníka dat Azure použijte příkazy.  
> * U velkých datových sad (>1 GB) použijte aktivitu Kopírování.  

## <a name="required-permissions"></a>Požadovaná oprávnění

V následující tabulce jsou uvedena požadovaná oprávnění pro různé kroky integrace s Azure Data Factory.

| Krok | Operace | Minimální úroveň oprávnění | Poznámky |
|---|---|---|---|
| **Vytvoření propojené služby** | Navigace v databázi | *prohlížeč databáze* <br>Přihlášený uživatel používající adf by měl být oprávněn číst metadata databáze. | Uživatel může zadat název databáze ručně. |
| | Testovat připojení | *databázový monitor* nebo *tabulkový nebo tabulkový* <br>Instanční objekt by měl `.show` být autorizován ke spuštění příkazů na úrovni databáze nebo k ingestování na úrovni tabulky. | <ul><li>TestConnection ověří připojení ke clusteru a nikoli k databázi. Může být úspěšná i v případě, že databáze neexistuje.</li><li>Oprávnění správce tabulky nejsou dostatečná.</li></ul>|
| **Vytvoření datové sady** | Navigace v tabulce | *monitor databáze* <br>Přihlášený uživatel používající adf musí být oprávněn `.show` provádět příkazy na úrovni databáze. | Uživatel může zadat název tabulky ručně.|
| **Vytvoření datové sady** nebo **aktivity kopírování** | Náhled dat | *prohlížeč databáze* <br>Instanční objekt musí být autorizován ke čtení metadat databáze. | | 
|   | Schéma importu | *prohlížeč databáze* <br>Instanční objekt musí být autorizován ke čtení metadat databáze. | Pokud je adx zdrojem tabulkové kopie, adf importuje schéma automaticky, i když uživatel neimportoval schéma explicitně. |
| **ADX jako jímka** | Vytvoření mapování sloupců názvů | *monitor databáze* <br>Instanční objekt musí být `.show` autorizován ke spuštění příkazů na úrovni databáze. | <ul><li>Všechny povinné operace budou fungovat s *tabulka ingestor*.</li><li> Některé volitelné operace mohou selhat.</li></ul> |
|   | <ul><li>Vytvoření mapování CSV v tabulce</li><li>Přetažení mapování</li></ul>| *tabulka ingestátor* nebo *správce databáze* <br>Instanční objekt musí být oprávněn provádět změny v tabulce. | |
|   | Ingestace dat | *tabulka ingestátor* nebo *správce databáze* <br>Instanční objekt musí být oprávněn provádět změny v tabulce. | | 
| **ADX jako zdroj** | Spustit dotaz | *prohlížeč databáze* <br>Instanční objekt musí být autorizován ke čtení metadat databáze. | |
| **Kusto, příkaz** | | Podle úrovně oprávnění každého příkazu. |

## <a name="performance"></a>Výkon 

Pokud je zdrojem Průzkumník dat Azure a použijete aktivitu vyhledávání, kopírování nebo příkazu, která obsahuje dotaz, kde naleznete [doporučené postupy dotazu](/azure/kusto/query/best-practices) pro informace o výkonu a [dokumentaci ADF pro aktivitu kopírování](/azure/data-factory/copy-activity-performance).
  
Tato část se zabývá použití kopírování aktivity, kde Azure Data Explorer je jímky. Odhadovaná propustnost pro jímky Průzkumníka dat Azure je 11–13 Mb/s. V následující tabulce jsou podrobně uvedeny parametry ovlivňující výkon jímky Průzkumníka dat Azure.

| Parametr | Poznámky |
|---|---|
| **Geografická blízkost komponent** | Umístěte všechny součásti do stejné oblasti:<ul><li>úložiště dat zdrojového a jímanového.</li><li>Za běhu integrace adf.</li><li>Cluster ADX.</li></ul>Ujistěte se, že alespoň váš integrační runtime je ve stejné oblasti jako cluster ADX. |
| **Počet DIU** | 1 virtuální ho na každé 4 DIU používané ADF. <br>Zvýšení DIU pomůže pouze v případě, že váš zdroj je úložiště založené na souborech s více soubory. Každý virtuální virtuální virtuální ms pak zpracuje jiný soubor paralelně. Proto kopírování jednoho velkého souboru bude mít vyšší latenci než kopírování více menších souborů.|
|**Částka a skladová položka clusteru ADX** | Vysoký počet uzlů ADX zvýší dobu zpracování při požití.|
| **Paralelnost** | Chcete-li zkopírovat velmi velké množství dat z databáze, rozdělte data a pak použijte smyčku ForEach, která paralelně kopíruje každý oddíl, nebo použijte [hromadnou kopii z databáze do šablony Průzkumníka dat Azure](data-factory-template.md). Poznámka: **Nastavení** > **Stupeň paralelismu** v aktivitě kopírování není relevantní pro ADX. |
| **Složitost zpracování dat** | Latence se liší podle formátu zdrojového souboru, mapování sloupců a komprese.|
| **Virtuální počítač s runtime integrace** | <ul><li>Pro kopírování Azure nelze změnit virtuální počítače ADF a jednotky soupočítače počítače.</li><li> Pro on-prem do Azure kopie, zjistěte, že virtuální počítač hostující vaše vlastní hostované infračervené ovládání je dostatečně silný.</li></ul>|

## <a name="tips-and-common-pitfalls"></a>Tipy a běžné úskalí

### <a name="monitor-activity-progress"></a>Sledovat průběh aktivity

* Při sledování průběhu *aktivity data písemné* vlastnost může být mnohem větší než *data čtení* vlastnost, protože *data čtení* se vypočítá podle binární velikost souboru, zatímco *data zapsána* se vypočítá podle velikosti v paměti, po data deserializována a dekomprimované.

* Při sledování průběhu aktivity uvidíte, že data se zapisují do jímky Průzkumníka dat Azure. Při dotazování na tabulku Průzkumníka dat Azure uvidíte, že data nedorazila. Důvodem je, že existují dvě fáze při kopírování do Průzkumníka dat Azure. 
    * První fáze přečte zdrojová data, rozdělí je na 900 MB bloků a odešle každý blok do objektu Blob Azure. První fázi vidí zobrazení průběhu aktivity ADF. 
    * Druhá fáze začíná po nahrání všech dat do objektů BLOB Azure. Uzly modulu Azure Data Explorer stáhnout objekty BLOB a ingestovat data do tabulky jímky. Data se pak zobrazí v tabulce Průzkumníka dat Azure.

### <a name="failure-to-ingest-csv-files-due-to-improper-escaping"></a>Selhání při požití souborů CSV z důvodu nesprávného úniku

Azure Data Explorer očekává, že soubory CSV zarovnají s [RFC 4180](https://www.ietf.org/rfc/rfc4180.txt).
Očekává:
* Pole obsahující znaky, které vyžadují únik (například " a nové řádky) by měla začínat a končit **znakem "** bez mezer. Všechny **"** znaky *uvnitř* pole jsou uvozeny pomocí dvojitého **znaku** (**""**). Například _"Hello, ""World"""_ je platný soubor CSV s jedním záznamem s jedním sloupcem nebo polem s obsahem _Hello, "World"_.
* Všechny záznamy v souboru musí mít stejný počet sloupců a polí.

Azure Data Factory umožňuje znak zpětného lomítka (escape). Pokud vygenerujete soubor CSV se znakem zpětného lomítka pomocí Azure Data Factory, ingestování souboru do Průzkumníka dat Azure se nezdaří.

#### <a name="example"></a>Příklad

Následující textové hodnoty: Dobrý den, "Svět"<br/>
ABC DEF<br/>
"ABC\D"EF<br/>
"ABC DEF<br/>

By se měl objevit ve správném souboru CSV takto: "Hello, ""World"""<br/>
"ABC DEF"<br/>
"""ABC DEF"<br/>
""ABC\D""EF"<br/>

Pomocí výchozího řídicího znaku (zpětné lomítko) nebude s Průzkumníkem \"dat\"Azure fungovat následující soubor CSV: "Hello, World "<br/>
"ABC DEF"<br/>
"\"ABC DEF"<br/>
\""ABC\D\"EF"<br/>

### <a name="nested-json-objects"></a>Vnořené objekty JSON

Při kopírování souboru JSON do Průzkumníka dat Azure si uvědomte, že:
* Pole nejsou podporována.
* Pokud vaše struktura JSON obsahuje datové typy objektů, Azure Data Factory srovná podřízené položky objektu a pokusí se mapovat každou podřízenou položku do jiného sloupce v tabulce Průzkumníka dat Azure. Pokud chcete, aby se celá položka objektu mapovala na jeden sloupec v Průzkumníku dat Azure:
    * Ingestuje celý řádek JSON do jednoho dynamického sloupce v Průzkumníku dat Azure.
    * Ručně upravte definici kanálu pomocí editoru JSON azure data factory. V **mapování**
       * Odeberte více mapování, která byla vytvořena pro každou podřízenou položku, a přidejte jedno mapování, které mapuje typ objektu do sloupce tabulky.
       * Za uzavírací hranatá závorka přidejte čárku následovanou:<br/>
       `"mapComplexValuesToString": true`.

### <a name="specify-additionalproperties-when-copying-to-azure-data-explorer"></a>Zadejte vlastnosti AdditionalProperties při kopírování do Průzkumníka dat Azure

> [!NOTE]
> Tato funkce je v současné době k dispozici ruční úpravou datové části JSON. 

Přidejte jeden řádek pod část "dřez" aktivity kopírování takto:

```json
"sink": {
    "type": "AzureDataExplorerSink",
    "additionalProperties": "{\"tags\":\"[\\\"drop-by:account_FiscalYearID_2020\\\"]\"}"
},
```

Únik hodnoty může být ošidný. Jako referenci použijte následující fragment kódu:

```csharp
static void Main(string[] args)
{
       Dictionary<string, string> additionalProperties = new Dictionary<string, string>();
       additionalProperties.Add("ignoreFirstRecord", "false");
       additionalProperties.Add("csvMappingReference", "Table1_mapping_1");
       IEnumerable<string> ingestIfNotExists = new List<string> { "Part0001" };
       additionalProperties.Add("ingestIfNotExists", JsonConvert.SerializeObject(ingestIfNotExists));
       IEnumerable<string> tags = new List<string> { "ingest-by:Part0001", "ingest-by:IngestedByTest" };
       additionalProperties.Add("tags", JsonConvert.SerializeObject(tags));
       var additionalPropertiesForPayload = JsonConvert.SerializeObject(additionalProperties);
       Console.WriteLine(additionalPropertiesForPayload);
       Console.ReadLine();
}
```

Vytištěná hodnota:

```json
{"ignoreFirstRecord":"false","csvMappingReference":"Table1_mapping_1","ingestIfNotExists":"[\"Part0001\"]","tags":"[\"ingest-by:Part0001\",\"ingest-by:IngestedByTest\"]"}
```

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak [zkopírovat data do Azure Data Exploreru pomocí Azure Data Factory](data-factory-load-data.md).
* Přečtěte si o používání [šablony Azure Data Factory pro hromadné kopírování z databáze do Průzkumníka dat Azure](data-factory-template.md).
* Přečtěte si o použití [aktivity příkazů Azure Data Factory ke spuštění příkazů řízení Azure Data Explorer](data-factory-command-activity.md).
* Přečtěte si o [dotazech Azure Data Explorer](/azure/data-explorer/web-query-data) pro dotazování dat.




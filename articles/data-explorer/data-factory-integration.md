---
title: Integrace se službou Azure Průzkumník dat s Azure Data Factory
description: V tomto tématu Integrujte Azure Průzkumník dat s Azure Data Factory a použijte aktivity kopírování, vyhledávání a příkazů.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: bb08cf4db45a378b35a8245eadd56a2ab3e48bab
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293619"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>Integrace Azure Průzkumník dat s využitím Azure Data Factory

[Azure Data Factory](/azure/data-factory/) (ADF) je cloudová služba pro integraci dat, která umožňuje integrovat různá úložiště dat a provádět aktivity s daty. ADF umožňuje vytvářet pracovní postupy řízené daty pro orchestraci a automatizaci přesunu dat a transformaci dat. Azure Průzkumník dat je jedním z [podporovaných úložišť dat](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) v Azure Data Factory. 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Aktivity Azure Data Factory pro Azure Průzkumník dat

Pro uživatele Azure Průzkumník dat jsou k dispozici různé integrace s Azure Data Factory:

### <a name="copy-activity"></a>Aktivita kopírování
 
Aktivita kopírování Azure Data Factory slouží k přenosu dat mezi úložišti dat. Azure Průzkumník dat se podporuje jako zdroj, kde se data zkopírují z Azure Průzkumník dat do libovolného podporovaného úložiště dat a jímky, kde se data zkopírují z libovolného podporovaného úložiště dat do Azure Průzkumník dat. Další informace najdete v tématu [kopírování dat do nebo z Azure Průzkumník dat pomocí Azure Data Factory](/azure/data-factory/connector-azure-data-explorer). podrobný návod najdete v tématu [načtení dat z Azure Data Factory do Azure Průzkumník dat](data-factory-load-data.md).
Azure Průzkumník dat podporuje aplikace Azure IR (Integration Runtime), která se používá při kopírování dat v Azure, a v místním prostředí IR, které se používá při kopírování dat z nebo do úložišť dat umístěných místně nebo v síti s řízením přístupu, jako je například Virtual Network Azure. Další informace najdete v tématu [který IR použít](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use) .
 
> [!TIP]
> Když použijete aktivitu kopírování a vytvoříte **propojenou službu** nebo **datovou sadu**, vyberte úložiště dat **Azure Průzkumník dat (Kusto)** , a ne staré **Kusto**úložiště dat.  

### <a name="lookup-activity"></a>Aktivita vyhledávání
 
Aktivita vyhledávání se používá ke spouštění dotazů v Azure Průzkumník dat. Výsledek dotazu bude vrácen jako výstup aktivity vyhledávání a lze jej použít v další aktivitě v kanálu, jak je popsáno v [dokumentaci pro vyhledávání ADF](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity).  
Kromě omezení velikosti odpovědi 5 000 řádků a 2 MB má tato aktivita také časový limit dotazu 1 hodina.

### <a name="command-activity"></a>Aktivita příkazu

Aktivita příkazu umožňuje spuštění [příkazů pro řízení](/azure/kusto/concepts/#control-commands)Průzkumník dat Azure. Na rozdíl od dotazů mohou příkazy pro řízení upravovat data nebo metadata. Některé příkazy ovládacích prvků jsou zaměřené na ingestování dat do Azure Průzkumník dat, použití příkazů, jako je `.ingest`nebo `.set-or-append`), nebo kopírování dat ze Průzkumník dat Azure do externích úložišť dat pomocí příkazů, jako je `.export`.
Podrobný návod k aktivitě příkazu najdete v tématu [použití aktivity příkazu Azure Data Factory ke spuštění příkazů řízení Azure Průzkumník dat](data-factory-command-activity.md).  Použití řídicího příkazu ke kopírování dat může být někdy rychlejší a levnější než aktivita kopírování. Pokud chcete zjistit, kdy se má použít aktivita příkazu versus aktivita kopírování, přečtěte si téma [Výběr mezi aktivitami kopírování a příkazem při kopírování dat](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data).

### <a name="copy-in-bulk-from-a-database-template"></a>Hromadné kopírování ze šablony databáze

[Hromadné kopírování z databáze do Azure Průzkumník dat pomocí šablony Azure Data Factory](data-factory-template.md) je předdefinovaný Azure Data Factory kanál. Šablona se používá k vytvoření mnoha kanálů pro každou databázi nebo pro každou tabulku pro rychlejší kopírování dat. 

### <a name="mapping-data-flows"></a>Toky dat mapování 

[Azure Data Factory mapování datových toků](/azure/data-factory/concepts-data-flow-overview) jsou vizuálně navržené transformace dat, které umožňují datovým technikům vyvíjet logiku transformace grafických dat bez psaní kódu. Pokud chcete vytvořit tok dat a ingestovat data do Azure Průzkumník dat, použijte tuto metodu:

1. Vytvořte [tok dat mapování](/azure/data-factory/data-flow-create).
1. [Exportujte data do objektu blob Azure](/azure/data-factory/data-flow-sink). 
1. Definujte [Event Grid](/azure/data-explorer/ingest-data-event-grid) nebo [aktivitu kopírování ADF](/azure/data-explorer/data-factory-load-data) k ingestování dat do Azure Průzkumník dat.

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>Při kopírování dat vybrat mezi aktivitami kopírování a příkazu Azure Průzkumník dat 

Tato část vám pomůže vybrat správnou aktivitu pro potřeby kopírování dat.

Při kopírování dat z nebo do Azure Průzkumník dat existují dvě dostupné možnosti v Azure Data Factory:
* Aktivita kopírování.
* Aktivita příkazu Azure Průzkumník dat, která spustí jeden z řídicích příkazů, které přenášejí data v Azure Průzkumník dat. 

### <a name="copy-data-from-azure-data-explorer"></a>Kopírování dat z Azure Průzkumník dat
  
Data z Azure Průzkumník dat můžete kopírovat pomocí aktivity kopírování nebo příkazu [`.export`](/azure/kusto/management/data-export/) . Příkaz `.export` spustí dotaz a pak vyexportuje výsledky dotazu. 

V následující tabulce najdete porovnání aktivity kopírování a příkazu `.export` pro kopírování dat z Azure Průzkumník dat.

| | Aktivita kopírování | . Export – příkaz |
|---|---|---|
| **Popis toku** | ADF spustí dotaz na Kusto, zpracuje výsledek a odešle ho do cílového úložiště dat. <br>(**ADX > ADF > úložiště dat jímky**) | ADF pošle příkaz `.export` ovládacího prvku do Azure Průzkumník dat, který provede příkaz a pošle data přímo do cílového úložiště dat. <br>(**Úložiště dat jímky ADX >** ) |
| **Podporovaná cílová úložiště dat** | Širokou škálu [podporovaných úložišť dat](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLSv2, Azure Blob, SQL Database |
| **Výkon** | Centralizované | <ul><li>Distribuované (výchozí), souběžně exportují data z několika uzlů</li><li>Rychlejší a lepší spotřebu.</li></ul> |
| **Omezení serveru** | [Omezení dotazu](/azure/kusto/concepts/querylimits) lze rozšířit nebo zakázat. Dotazy ADF standardně obsahují: <ul><li>Omezení velikosti 500 000 záznamů nebo 64 MB.</li><li>Časový limit 10 minut.</li><li>`noTruncation` nastavit na hodnotu false.</li></ul> | Ve výchozím nastavení rozšiřuje nebo zakazuje omezení dotazů: <ul><li>Omezení velikosti jsou zakázaná.</li><li>Časový limit serveru je prodloužen na 1 hodinu.</li><li>`MaxMemoryConsumptionPerIterator` a `MaxMemoryConsumptionPerQueryPerNode` jsou rozšířené na maximum (5 GB, TotalPhysicalMemory/2).</li></ul>

> [!TIP]
> Pokud je cíl kopírování jedním z úložišť dat podporovaných příkazem `.export` a pokud žádné z funkcí aktivity kopírování není pro vaše potřeby zásadní, vyberte příkaz `.export`.

### <a name="copying-data-to-azure-data-explorer"></a>Kopírování dat do Azure Průzkumník dat

Data do Azure Průzkumník dat můžete kopírovat pomocí příkazů kopírování nebo příkazů pro přijímání, jako je ingestování [z dotazu](/azure/kusto/management/data-ingestion/ingest-from-query) (`.set-or-append`, `.set-or-replace`, `.set`, `.replace)`a ingestování [z úložiště](/azure/kusto/management/data-ingestion/ingest-from-storage) (`.ingest`). 

V následující tabulce najdete srovnání aktivity kopírování a příkazů pro přijímání dat pro kopírování dat do Azure Průzkumník dat.

| | Aktivita kopírování | Ingestování z dotazu<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | Ingestování z úložiště <br> `.ingest` |
|---|---|---|---|
| **Popis toku** | ADF získá data ze zdrojového úložiště dat, převede je do tabulkového formátu a provede požadované změny mapování schématu. ADF pak nahraje data do objektů blob Azure, rozdělí je do bloků dat a pak stáhne objekty blob, aby je ingestal do tabulky ADX. <br> (**Zdrojové úložiště dat > ADF > objektů blob Azure > ADX**) | Tyto příkazy mohou spustit dotaz nebo `.show` příkaz a ingestovat výsledky dotazu do tabulky (**ADX > ADX**). | Tento příkaz ingestuje data do tabulky tím, že "narazí" data z jednoho nebo více artefaktů cloudového úložiště. |
| **Podporovaná zdrojová úložiště dat** |  [různé možnosti](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS Gen 2, Azure Blob, SQL (pomocí modulu plug-in sql_request), Cosmos (pomocí modulu plug-in cosmosdb_sql_request) a jakékoli jiné úložiště dat, které poskytuje rozhraní API pro HTTP nebo Python. | Systém souborů, Azure Blob Storage, ADLS Gen 1, ADLS Gen 2 |
| **Výkon** | Ingestování se zařazuje do fronty a spravují, což zajišťuje ingestování v malých velikostech a zajišťuje vysokou dostupnost tím, že poskytuje vyrovnávání zatížení, opakované pokusy a zpracování chyb. | <ul><li>Tyto příkazy nejsou navržené pro import dat s velkým objemem dat.</li><li>Funguje podle očekávání a levnějšího. Ale v produkčních scénářích a v případě velkých objemů provozu a velikosti dat použijte aktivitu kopírování.</li></ul> |
| **Omezení serveru** | <ul><li>Bez omezení velikosti.</li><li>Maximální časový limit: 1 hodina na ingestný objekt BLOB. |<ul><li>V části dotazu je jenom omezení velikosti, které se dá přeskočit zadáním `noTruncation=true`.</li><li>Maximální časový limit: 1 hodina</li></ul> | <ul><li>Bez omezení velikosti.</li><li>Maximální časový limit: 1 hodina</li></ul>|

> [!TIP]
> * Při kopírování dat z ADF do Azure Průzkumník dat použijte příkazy `ingest from query`.  
> * U velkých datových sad (> 1 GB) použijte aktivitu kopírování.  

## <a name="required-permissions"></a>Požadovaná oprávnění

Následující tabulka uvádí požadovaná oprávnění pro různé kroky v integraci s Azure Data Factory.

| Krok | Operace | Minimální úroveň oprávnění | Poznámky |
|---|---|---|---|
| **Vytvoření propojené služby** | Navigace v databázi | *prohlížeč databáze* <br>Přihlášený uživatel pomocí ADF by měl mít oprávnění ke čtení metadat databáze. | Uživatel může název databáze zadat ručně. |
| | Test připojení | *monitorování databáze* nebo ingestování *tabulek* <br>Instanční objekt by měl mít autorizaci pro spouštění `.show`ch příkazů nebo na úrovni tabulky na úrovni databáze. | <ul><li>TestConnection ověří připojení ke clusteru, a ne k databázi. Může to být úspěšné i v případě, že databáze neexistuje.</li><li>Oprávnění správce tabulky nejsou dostatečná.</li></ul>|
| **Vytvoření datové sady** | Navigace v tabulce | *monitorování databáze* <br>Přihlášený uživatel pomocí ADF musí mít autorizaci k provádění příkazů `.show` na úrovni databáze. | Uživatel může zadat název tabulky ručně.|
| **Vytvoření datové sady** nebo **aktivity kopírování** | Náhled dat | *prohlížeč databáze* <br>Instanční objekt musí mít oprávnění ke čtení metadat databáze. | | 
|   | Importovat schéma | *prohlížeč databáze* <br>Instanční objekt musí mít oprávnění ke čtení metadat databáze. | Když je ADX zdrojem tabulkového a tabulkového kopírování, ADF automaticky importuje schéma, i když uživatel neimportuje schéma explicitně. |
| **ADX jako jímka** | Vytvořit mapování sloupce podle názvu | *monitorování databáze* <br>Instanční objekt musí mít autorizaci k provádění příkazů `.show` úrovně databáze. | <ul><li>Všechny povinné operace budou fungovat s ingestování *tabulek*.</li><li> Některé volitelné operace mohou selhat.</li></ul> |
|   | <ul><li>Vytvoření mapování sdíleného svazku clusteru v tabulce</li><li>Zrušení mapování</li></ul>| ingestování *tabulek* nebo *správce databáze* <br>Instanční objekt musí mít autorizaci k provádění změn v tabulce. | |
|   | Ingestace dat | ingestování *tabulek* nebo *správce databáze* <br>Instanční objekt musí mít autorizaci k provádění změn v tabulce. | | 
| **ADX as source** | Spustit dotaz | *prohlížeč databáze* <br>Instanční objekt musí mít oprávnění ke čtení metadat databáze. | |
| **Kusto – příkaz** | | Podle úrovně oprávnění jednotlivých příkazů. |

## <a name="performance"></a>Výkon 

Pokud je Azure Průzkumník dat zdrojem a používáte aktivitu vyhledávání, kopírování nebo příkazu, která obsahuje dotaz, kde najdete informace o [osvědčených postupech](/azure/kusto/query/best-practices) pro informace o výkonu a dokumentaci k [ADF pro aktivitu kopírování](/azure/data-factory/copy-activity-performance).
  
V této části se řeší použití aktivity kopírování, kde je Azure Průzkumník dat jímka. Odhadovaná propustnost pro jímku služby Azure Průzkumník dat je 11-13 MB/s. Následující tabulka popisuje parametry, které ovlivňují výkon jímky Průzkumník dat služby Azure.

| Parametr | Poznámky |
|---|---|
| **Geografické okolí součástí** | Všechny komponenty umístěte do stejné oblasti:<ul><li>zdrojová data a úložiště dat jímky.</li><li>Prostředí Integration runtime ADF.</li><li>Váš cluster ADX.</li></ul>Ujistěte se, že alespoň váš modul runtime integrace je ve stejné oblasti jako cluster ADX. |
| **Počet DIUs** | 1 virtuální počítač pro každé 4 DIUs, kterou používá ADF. <br>Zvýšení DIUs vám pomůže jenom v případě, že váš zdroj ukládá soubor s více soubory. Každý virtuální počítač potom zpracuje jiný soubor paralelně. Proto bude mít kopírování jednoho velkého souboru větší latenci než kopírování více menších souborů.|
|**Množství a SKU clusteru ADX** | Vysoký počet uzlů ADX zvýší dobu zpracování příjmu.|
| **Paralelismu** | Chcete-li zkopírovat velmi velké množství dat z databáze, vytvořte oddíly dat a pak použijte smyčku ForEach, která každý oddíl zkopíruje paralelně, nebo použijte [šablonu hromadného kopírování z databáze do služby Azure Průzkumník dat](data-factory-template.md). Poznámka: **nastavení** > **stupeň paralelismu** v aktivitě kopírování není relevantní pro ADX. |
| **Složitost zpracování dat** | Latence se liší podle formátu zdrojového souboru, mapování sloupců a komprese.|
| **Virtuální počítač, na kterém běží prostředí Integration runtime** | <ul><li>Pro Azure kopírování, virtuální počítače ADF a SKU počítačů se nedají změnit.</li><li> V případě Prem do Azure Copy určete, že virtuální počítač hostující vaše místní prostředí IR je dostatečně silný.</li></ul>|

## <a name="tips-and-common-pitfalls"></a>Tipy a běžné nástrah

### <a name="monitor-activity-progress"></a>Průběh aktivity monitorování

* Když sledujete průběh aktivity, vlastnost *zapsaná data* může být mnohem větší než vlastnost *čtení* dat, protože *čtení dat* se počítá podle velikosti binárního souboru, zatímco *zapsaná* data se počítají podle velikosti v paměti, a to po deserializaci a dekomprimaci dat.

* Když sledujete průběh aktivity, vidíte, že se data zapisují do jímky Azure Průzkumník dat. Při dotazování tabulky Azure Průzkumník dat vidíte, že data nebyla doručena. Důvodem je to, že při kopírování do Azure Průzkumník dat existují dvě fáze. 
    * První fáze přečte zdrojová data, rozdělí je na 900 MB bloků dat a nahraje jednotlivé bloky do objektu blob Azure. První fáze se zobrazuje v zobrazení průběh aktivity ADF. 
    * Druhá fáze začíná, jakmile se všechna data nahrají do objektů blob Azure. Uzly modulu Azure Průzkumník dat stáhnou objekty BLOB a ingestují data do tabulky jímky. Data se pak zobrazují v tabulce Azure Průzkumník dat.

### <a name="failure-to-ingest-csv-files-due-to-improper-escaping"></a>Kvůli nesprávnému uvozovacímu souboru se nepodařilo ingestovat soubory CSV.

Azure Průzkumník dat očekává, že soubory CSV zarovnají [specifikaci RFC 4180](https://www.ietf.org/rfc/rfc4180.txt).
Očekává se:
* Pole, která obsahují znaky, které vyžadují uvozovací znaky (například "a nové řádky), by měly začínat a končit znakem **"** bez prázdných znaků. Všechny **"** znaky *v* poli jsou uvozeny pomocí dvojitého **"** znaku ( **""** ). Například _"Hello" "World"_ "je platný soubor CSV s jedním záznamem s jedním sloupcem nebo polem s obsahem _Hello" World "_ .
* Všechny záznamy v souboru musí mít stejný počet sloupců a polí.

Azure Data Factory umožňuje znak zpětného lomítka (Escape). Pokud vygenerujete soubor CSV s znakem zpětného lomítka pomocí Azure Data Factory, ingestování souboru do Azure Průzkumník dat se nezdaří.

#### <a name="example"></a>Příklad:

Následující textové hodnoty: Hello, "World"<br/>
ABC – DEF<br/>
"ABC\D" EF<br/>
"ABC DEF<br/>

Měl by se zobrazit ve správném souboru CSV následujícím způsobem: Hello, World (svět).<br/>
"ABC DEF"<br/>
"" ABC DEF "<br/>
"" "ABC\D" "EF"<br/>

Když použijete výchozí řídicí znak (zpětné lomítko), následující soubor CSV nebude fungovat s Azure Průzkumník dat: "Hello, \"World\"".<br/>
"ABC DEF"<br/>
"\"ABC DEF"<br/>
"\"ABC\D\"EF"<br/>

### <a name="nested-json-objects"></a>Vnořené objekty JSON

Při kopírování souboru JSON do Azure Průzkumník dat mějte na paměti, že:
* Pole nejsou podporována.
* Pokud vaše struktura JSON obsahuje datové typy objektů, Azure Data Factory provede sloučení podřízených položek objektu a pokusí se namapovat každou podřízenou položku na jiný sloupec v tabulce Azure Průzkumník dat. Pokud chcete, aby byla celá položka objektu namapována na jeden sloupec v Azure Průzkumník dat:
    * Ingestuje celý řádek JSON do jednoho dynamického sloupce v Azure Průzkumník dat.
    * Ručně upravte definici kanálu pomocí editoru JSON Azure Data Factory. V **mapování**
       * Odeberte vícenásobná mapování, která byla vytvořena pro každou podřízenou položku, a přidejte jedno mapování, které mapuje typ objektu na sloupec tabulky.
       * Za pravou hranatou závorku Přidejte čárku, za kterou následuje:<br/>
       `"mapComplexValuesToString": true`.

### <a name="specify-additionalproperties-when-copying-to-azure-data-explorer"></a>Zadejte AdditionalProperties při kopírování do Azure Průzkumník dat

> [!NOTE]
> Tato funkce je aktuálně dostupná ruční úpravou datové části JSON. 

V části jímka aktivity kopírování přidejte jeden řádek následujícím způsobem:

```json
"sink": {
    "type": "AzureDataExplorerSink",
    "additionalProperties": "{\"tags\":\"[\\\"drop-by:account_FiscalYearID_2020\\\"]\"}"
},
```

Uvozovací znaky hodnoty mohou být obtížné. Jako referenci použijte následující fragment kódu:

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

* Naučte se, jak [Kopírovat data do Azure Průzkumník dat pomocí Azure Data Factory](data-factory-load-data.md).
* Přečtěte si o použití [šablony Azure Data Factory pro hromadné kopírování z databáze do Azure Průzkumník dat](data-factory-template.md).
* Přečtěte si, jak používat [aktivitu příkazu Azure Data Factory ke spouštění příkazů řízení Azure Průzkumník dat](data-factory-command-activity.md).
* Přečtěte si o dotazech na [Azure Průzkumník dat](/azure/data-explorer/web-query-data) pro dotazování na data.




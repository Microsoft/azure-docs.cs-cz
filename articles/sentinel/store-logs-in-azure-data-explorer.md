---
title: Integrace Azure Průzkumník dat pro dlouhodobé uchovávání protokolů | Microsoft Docs
description: Odešlete protokoly Sentinel Azure do Azure Průzkumník dat pro dlouhodobé uchovávání, aby se snížily náklady na úložiště dat.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/21/2021
ms.author: bagol
ms.openlocfilehash: c7d9ef58d4bb15afe59c6734ce887c2cc3c07c26
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836175"
---
# <a name="integrate-azure-data-explorer-for-long-term-log-retention"></a>Integrace Azure Průzkumník dat pro dlouhodobé uchovávání protokolů

<!--Info not included:>

Script - can't xref out to a private github repo from docs
-->

Ve výchozím nastavení se protokoly ingestované do Azure Sentinel ukládají do Azure Monitor Log Analytics. Tento článek vysvětluje, jak snížit náklady na uchování v Azure Sentinel tím, že je posíláte do Azure Průzkumník dat (ADX) pro dlouhodobé uchovávání.

Ukládání protokolů v ADX snižuje náklady a přitom si zachovává schopnost dotazovat se na data a je zvlášť užitečné, když vaše data roste. Například zatímco data zabezpečení mohou v průběhu času přijít o hodnotu, může být nutné zachovat protokoly pro zákonné požadavky nebo spustit pravidelné šetření na starší data.

## <a name="about-azure-data-explorer"></a>Informace o službě Azure Data Explorer

ADX je platforma pro analýzu velkých objemů dat, která je vysoce optimalizovaná pro protokoly a analýzu dat. Vzhledem k tomu, že ADX jako svůj dotazovací jazyk používá KQL (Kusto Query Language), je vhodná alternativa pro úložiště dat Sentinel Azure. Použití ADX pro úložiště dat umožňuje spouštění dotazů napříč platformami a vizualizaci dat napříč ADX a službou Azure Sentinel.

Další informace najdete v [dokumentaci k](/azure/data-explorer/) ADX a na [blogu](https://azure.microsoft.com/en-us/blog/tag/azure-data-explorer/).

### <a name="when-to-integrate-with-adx"></a>Kdy se dá integrovat s ADX

Azure Sentinel poskytuje úplné možnosti SIEM a společnosti, rychlé nasazení a konfiguraci a také pokročilé integrované funkce zabezpečení pro SOC týmy. Hodnota ukládání dat zabezpečení v Azure Sentinel se ale může po několika měsících vyřadit, jakmile SOC uživatelé nepotřebují přístup k nim, jak často mají přístup k novějším datům.

Pokud potřebujete přístup jen ke konkrétním tabulkám, třeba k pravidelným vyšetřováním nebo auditům, můžete zvážit, že zachování dat ve službě Azure Sentinel již není nákladově efektivní. V tuto chvíli doporučujeme ukládat data do ADX, což snižuje náklady, ale pořád vám umožní prozkoumat pomocí stejných KQL dotazů, které spouštíte v rámci Azure Sentinel.

K datům v ADX můžete přistupovat přímo z Azure Sentinel pomocí [funkce proxy serveru služby Log Analytics ADX](//azure/azure-monitor/logs/azure-monitor-data-explorer-proxy). Provedete to tak, že v hledání v protokolu nebo v sešitech použijete dotazy pro různé clustery. 

> [!IMPORTANT]
> Základní možnosti SIEM, včetně analytických pravidel, UEBA a grafu šetření, nepodporují data uložená v ADX.
>

> [!NOTE]
> Integrace s ADX vám také umožní mít v datech kontrolu a členitost. Další informace najdete v tématu věnovaném [hledisku návrhu](#design-considerations).
> 
## <a name="send-data-directly-to-azure-sentinel-and-adx-in-parallel"></a>Paralelní odesílání dat do Azure Sentinel a ADX

Možná budete chtít zachovat veškerá data *s hodnotou zabezpečení* ve službě Azure Sentinel pro použití při detekcích, vyšetřování incidentů, loveckí hrozeb, UEBA a tak dále. Udržování těchto dat v Azure Sentinel hlavně přináší uživatele SOC (Security Operations Center), kde to obvykle stačí 3-12 měsíců úložiště.

Můžete také nakonfigurovat všechna vaše data, *bez ohledu na její hodnotu zabezpečení,* která se mají odeslat do ADX ve stejnou dobu, kde ji můžete ukládat déle. Při odesílání dat do služby Azure Sentinel i ADX zároveň dojde k nějaké duplicitě, úspora nákladů může být významná, protože snížíte náklady na uchování v rámci Azure Sentinel.

> [!TIP]
> Tato možnost také umožňuje korelovat data rozložená mezi úložišti dat, například k obohacení dat zabezpečení uložených v Azure Sentinel s provozními nebo dlouhodobými daty uloženými v ADX. Další informace najdete v tématu [dotazování služby Azure Průzkumník dat mezi prostředky pomocí Azure monitor](/azure/azure-monitor/logs/azure-monitor-data-explorer-proxy).
>

Následující obrázek ukazuje, jak můžete uchovávat všechna vaše data v ADX a přitom odesílat pouze data zabezpečení do Azure Sentinel pro každodenní použití.

:::image type="content" source="media/store-logs-in-adx/store-data-in-sentinel-and-adx-in-parallel.png" alt-text="Ukládejte data v ADX a v Azure Sentinel paralelně.":::

Další informace o implementaci této možnosti architektury najdete v tématu [monitorování služby Azure Průzkumník dat](/azure/architecture/solution-ideas/articles/monitor-azure-data-explorer).

## <a name="export-data-from-log-analytics-into-adx"></a>Export dat z Log Analytics do ADX

Místo odeslání dat přímo do ADX se můžete rozhodnout exportovat data z Log Analytics do ADX prostřednictvím centra událostí ADX nebo Azure Data Factory.

### <a name="data-export-architecture"></a>Architektura exportu dat

Následující obrázek znázorňuje ukázkový tok exportovaných dat prostřednictvím kanálu ingestování Azure Monitor. Vaše data jsou ve výchozím nastavení směrována na Log Analytics, ale můžete ji také nakonfigurovat pro export do Azure Storage účtu nebo centra událostí.

:::image type="content" source="media/store-logs-in-adx/export-data-from-azure-monitor.png" alt-text="Exportujte data z Azure Monitor architektury.":::

Při konfiguraci pravidel exportu dat vyberte typy protokolů, které chcete exportovat. Po nakonfigurování se nová data přicházející do koncového bodu ingestování Log Analytics a cílení na váš pracovní prostor pro vybrané tabulky vyexportují do svého účtu úložiště nebo centra událostí.

Při konfiguraci dat pro export si pamatujte na následující skutečnosti:

|Aspekty  | Podrobnosti |
|---------|---------|
|**Rozsah exportovaných dat**     |  Po nakonfigurování exportu pro konkrétní tabulku se exportují všechna data odesílaná do této tabulky bez výjimky. Export filtrované podmnožiny dat nebo omezení exportu na konkrétní události není podporován.       |
|**Požadavky na umístění**     |   Pracovní prostor Azure Monitor/Azure Sentinel a cílové umístění (účet Azure Storage nebo centrum událostí) se musí nacházet ve stejné geografické oblasti.      |
|**Podporované tabulky**     | Pro export nejsou podporovány všechny tabulky, například vlastní tabulky protokolů, které nejsou podporovány. <br><br>Další informace najdete v tématu [Export dat Log Analytics pracovního prostoru v Azure monitor](/azure/azure-monitor/logs/logs-data-export) a [seznam podporovaných tabulek](/azure/azure-monitor/logs/logs-data-export#supported-tables).         |
|     |         |

### <a name="data-export-methods-and-procedures"></a>Metody a postupy exportu dat

Pomocí jednoho z následujících postupů exportujte data z Azure Sentinel do ADX:

- **Prostřednictvím centra událostí ADX**. Exportujte data z Log Analytics do centra událostí, kde je můžete ingestovat do ADX. Tato metoda ukládá některá data (prvních X měsíců) do Azure Sentinel a ADX.

- **Prostřednictvím Azure Storage a Azure Data Factory**. Exportujte data z Log Analytics do Azure Blob Storage a pak Azure Data Factory se používá ke spuštění periodické úlohy kopírování, aby se data dále exportovali do ADX. Tato metoda umožňuje kopírovat data z Azure Data Factory jenom v případě, že se blíží limitu uchování v Azure Sentinel/Log Analytics a vyhnout se duplicitám.

### <a name="adx-event-hub"></a>[Centrum událostí ADX](#tab/adx-event-hub)

Tato část popisuje, jak exportovat data služby Azure Sentinel z Log Analytics do centra událostí, kde je můžete ingestovat do ADX. Podobně jako při [posílání dat přímo do Azure Sentinel a ADX](#send-data-directly-to-azure-sentinel-and-adx-in-parallel)zahrnuje tato metoda duplikaci dat, protože data se streamují do ADX při jejich doručování do Log Analytics.

Následující obrázek znázorňuje ukázkový tok exportovaných dat do centra událostí, ze kterého se ingestuje do ADX.

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-event-hub.png" alt-text="Exportujte data do ADX prostřednictvím centra událostí ADX.":::

Architektura zobrazená na předchozím obrázku nabízí úplné prostředí Azure Sentinel SIEM, včetně správy incidentů, vizuálních vyšetřování, loveckých hrozeb, pokročilých vizualizací, UEBA a dalších, pro data, ke kterým musí být často přistupovaná data, a to každých *X* měsíců. Tato architektura zároveň umožňuje dotazovat se na dlouhodobá data tím, že se k nim přistupuje přímo v ADX nebo prostřednictvím služby Azure Sentinel, a to díky funkci proxy ADX. Dotazy na dlouhodobé úložiště dat v ADX se dají přenést bez jakýchkoli změn z Azure Sentinel na ADX.

> [!TIP]
> Další informace o tomto postupu najdete v tématu [kurz: ingestování a dotazování na data monitorování v Azure Průzkumník dat](/azure/data-explorer/ingest-data-no-code).
>

**Export dat do ADX prostřednictvím centra událostí**:

1. **Nakonfigurujte Log Analytics exportovat data do centra událostí**. Další informace najdete v tématu [Export dat z pracovního prostoru Log Analytics v Azure monitor](/azure/azure-monitor/platform/logs-data-export).

1. **Vytvořte cluster ADX a databázi**. Další informace naleznete v tématu:

    - [Vytvoření clusteru a databáze Azure Průzkumník dat](/azure/data-explorer/create-cluster-database-portal)
    - [Výběr správné skladové SKU služby COMPUTE pro cluster Průzkumník dat Azure](/azure/data-explorer/manage-cluster-choose-sku)

1. **Vytvořte cílové tabulky**. Nezpracovaná data se nejdřív ingestují do mezilehlé tabulky, kde se nezpracované data ukládají, zpracovávají a rozšiřují.

    Zásada aktualizace, která je podobná funkci použité pro všechna nová data, se používá k ingestování rozšířených dat do finální tabulky, která má stejné schéma jako původní tabulka v rámci Azure Sentinel.

    Nastavte uchování v nezpracované tabulce na **0** dní. Data jsou uložena pouze v správně formátované tabulce a po transformaci se odstraní v nezpracované tabulce.

    Další informace najdete v tématu ingestování [a dotazování na data monitorování v Azure Průzkumník dat](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. <a name="mapping"></a>**Vytvořit mapování tabulky** Namapujte tabulky JSON, abyste definovali způsob, jakým se v tabulce nezpracované události najdou záznamy, jak se nacházejí v centru událostí. Další informace najdete v tématu [Vytvoření zásad aktualizace pro data metrik a log](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. **Vytvořte zásadu aktualizace a připojte ji k tabulce nezpracované záznamy**. V tomto kroku vytvořte funkci, která se nazývá zásada aktualizace, a připojte ji k cílové tabulce, aby se data po dobu příjmu transformoval.

    > [!NOTE]
    > Tento krok je vyžadován pouze v případě, že chcete mít tabulky dat v ADX se stejným schématem a formátem jako v rámci Azure Sentinel.
    >

    Další informace najdete v tématu [připojení centra událostí k Azure Průzkumník dat](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs).

1. **Vytvoří datové připojení mezi centrem událostí a tabulkou nezpracovaná data v ADX**. Nakonfigurujte ADX s podrobnostmi o tom, jak exportovat data do centra událostí.

    Postupujte podle pokynů v [dokumentaci k Azure Průzkumník dat](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs) a zadejte následující podrobnosti:

    - **Cíl**. Zadejte konkrétní tabulku s nezpracovanými daty.
    - **Formát**: `.json`Jako formát tabulky zadejte.
    - **Mapování, které má být použito**. Zadejte tabulku mapování vytvořenou v [kroku 4](#mapping) výše.


1. **Upravte uchování cílové tabulky**. [Výchozí zásady uchovávání informací v Azure Průzkumník dat](/azure/data-explorer/kusto/management/retentionpolicy) můžou být mnohem delší, než potřebujete.

    Pomocí následujícího příkazu aktualizujte Zásady uchovávání informací na jeden rok:

    ```kusto
    .alter-merge table <tableName> policy retention softdelete = 365d recoverability = disabled
    ```
### <a name="azure-storage--azure-data-factory"></a>[Azure Storage/Azure Data Factory](#tab/azure-storage-azure-data-factory)

Tato část popisuje, jak exportovat data služby Azure Sentinel z Log Analytics do Azure Storage, kde Azure Data Factory může spustit běžnou úlohu pro export dat do ADX.

Pomocí Azure Storage a Azure Data Factory můžete kopírovat data z Azure Storage jenom v případě, že se blíží limitu uchování v Azure Sentinel/Log Analytics. Neexistují žádné duplicity dat a ADX se používají *jenom* pro přístup k datům, která jsou starší než limit uchování v Azure Sentinel.

> [!TIP]
> I když je architektura pro používání Azure Storage a Azure Data Factory pro vaše starší verze dat složitější, může tato metoda nabídnout větší úspory nákladů.
>
Následující obrázek znázorňuje ukázkový tok exportovaných dat do Azure Storage, ze kterého Azure Data Factory spouští běžnou úlohu k dalšímu exportu do ADX.

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-azure-storage-azure-data-factory.png" alt-text="Exportujte data do ADX prostřednictvím Azure Storage a Azure Data Factory.":::

**Export dat do ADX prostřednictvím Azure Storage a Azure Data Factory**:

1. **Nakonfigurujte Log Analytics exportovat data do centra událostí**. Další informace najdete v tématu [Export dat z pracovního prostoru Log Analytics v Azure monitor](/azure/azure-monitor/logs/logs-data-export?tabs=portal#enable-data-export).

1. **Vytvořte cluster ADX a databázi**. Další informace naleznete v tématu:

    - [Vytvoření clusteru a databáze Azure Průzkumník dat](/azure/data-explorer/create-cluster-database-portal)
    - [Výběr správné skladové SKU služby COMPUTE pro cluster Průzkumník dat Azure](/azure/data-explorer/manage-cluster-choose-sku)

1. **Vytvořte cílové tabulky**. Nezpracovaná data se nejdřív ingestují do mezilehlé tabulky, kde se nezpracované data ukládají, zpracovávají a rozšiřují.

    Zásada aktualizace, která je podobná funkci použité pro všechna nová data, se používá k ingestování rozšířených dat do finální tabulky, která má stejné schéma jako původní tabulka v rámci Azure Sentinel.

    Nastavte uchování v nezpracované tabulce na **0** dní. Data jsou uložena pouze v správně formátované tabulce a po transformaci se odstraní v nezpracované tabulce.

    Další informace najdete v tématu ingestování [a dotazování na data monitorování v Azure Průzkumník dat](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. <a name="mapping"></a>**Vytvořit mapování tabulky** Namapujte tabulky JSON, abyste definovali způsob, jakým se v tabulce nezpracované události najdou záznamy, jak se nacházejí v centru událostí. Další informace najdete v tématu [Vytvoření zásad aktualizace pro data metrik a log](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. **Vytvořte zásadu aktualizace a připojte ji k tabulce nezpracované záznamy**. V tomto kroku vytvořte funkci, která se nazývá zásada aktualizace, a připojte ji k cílové tabulce, aby se data po dobu příjmu transformoval.

    > [!NOTE]
    > Tento krok je vyžadován pouze v případě, že chcete mít tabulky dat v ADX se stejným schématem a formátem jako v rámci Azure Sentinel.
    >

    Další informace najdete v tématu [připojení centra událostí k Azure Průzkumník dat](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs).

1. **Vytvoří datové připojení mezi centrem událostí a tabulkou nezpracovaná data v ADX**. Nakonfigurujte ADX s podrobnostmi o tom, jak exportovat data do centra událostí.

    Postupujte podle pokynů v [dokumentaci k Azure Průzkumník dat](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs) a zadejte následující podrobnosti:

    - **Cíl**. Zadejte konkrétní tabulku s nezpracovanými daty.
    - **Formát**: `.json`Jako formát tabulky zadejte.
    - **Mapování, které má být použito**. Zadejte tabulku mapování vytvořenou v [kroku 4](#mapping) výše.

1. **Nastavení Azure Data Factoryho kanálu**:

    - Vytvořte propojené služby pro Azure Storage a Azure Průzkumník dat. Další informace naleznete v tématu:

        - [Kopírování a transformace dat v úložišti objektů BLOB v Azure pomocí Azure Data Factory](/azure/data-factory/connector-azure-blob-storage)
        - [Kopírování dat do nebo z Azure Průzkumník dat pomocí Azure Data Factory](/azure/data-factory/connector-azure-data-explorer).

    - Vytvoří datovou sadu z Azure Storage. Další informace najdete v tématu [datové sady v Azure Data Factory](/azure/data-factory/concepts-datasets-linked-services).

    - Vytvořte datový kanál s operací kopírování na základě vlastností **LastModifiedDate** .

        Další informace najdete v tématu [kopírování nových a změněných souborů pomocí **LastModifiedDate** s Azure Data Factory](/azure/data-factory/solution-template-copy-new-files-lastmodifieddate).

---

## <a name="design-considerations"></a>Na co dát pozor při navrhování

Při ukládání dat Sentinel Azure v ADX Vezměte v úvahu tyto prvky:

|Aspekty  |Description  |
|---------|---------|
|**Velikost clusteru a SKU**     | Pečlivě Naplánujte počet uzlů a SKU virtuálního počítače ve vašem clusteru. Tyto faktory určují množství výkonu zpracování a velikost horké mezipaměti (SSD a paměť). Čím větší je mezipaměť, tím více dat budete moci dotazovat s vyšším výkonem. <br><br>Doporučujeme, abyste navštívili [kalkulačku pro změny velikosti ADX](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html), kde můžete hrát s různými konfiguracemi a zobrazit výsledné náklady. <br><br>ADX má také schopnost automatického škálování, která v závislosti na zatížení clusteru usnadňuje inteligentní rozhodování o přidávání a odebírání uzlů podle potřeby. Další informace najdete v tématu [Správa horizontálního škálování clusteru (horizontální navýšení kapacity) v Azure Průzkumník dat, aby se vešlo na měnící se požadavky](/azure/data-explorer/manage-cluster-horizontal-scaling).      |
|**Hot/studená mezipaměť**     | ADX poskytuje kontrolu nad datovými tabulkami, které jsou v Hot cache, a rychleji vrací výsledky. Pokud máte v clusteru ADX velké objemy dat, možná budete chtít rozdělit tabulky na měsíc, abyste měli větší členitost na data, která jsou přítomna v Hot cache. <br><br>Další informace najdete v tématu [zásady mezipaměti (Hot a studená mezipaměť)](/azure/data-explorer/kusto/management/cachepolicy) .       |
|**Uchování**     |   V ADX můžete nakonfigurovat, kdy se data odstraňují z databáze nebo z jednotlivé tabulky, což je také důležitou součástí omezení nákladů na úložiště. <br><br> Další informace najdete v tématu [zásady uchovávání informací](/azure/data-explorer/kusto/management/retentionpolicy).       |
|**Zabezpečení**     |  Několik nastavení ADX vám může pomáhat chránit vaše data, jako je Správa identit, šifrování a tak dále. Pro řízení přístupu na základě role (RBAC) je možné ADX nakonfigurovat tak, aby omezil přístup k databázím, tabulkám nebo i řádkům v tabulce. Další informace najdete v tématu [zabezpečení v Azure Průzkumník dat](/azure/data-explorer/security) a [zabezpečení na úrovni řádků](/azure/data-explorer/kusto/management/rowlevelsecuritypolicy).|
|**Sdílení údajů**     |   ADX umožňuje zpřístupnění částí dat ostatním stranám, jako jsou partneři nebo dodavatelé, a dokonce i nákup dat od jiných stran. Další informace najdete v tématu [použití sdílené složky Azure ke sdílení dat s využitím azure Průzkumník dat](/azure/data-explorer/data-share).      |
| **Další cenové komponenty** | Vezměte v úvahu další cenové komponenty následujících metod: <br><br>**Export dat prostřednictvím centra událostí ADX**: <br>-Log Analytics náklady na export dat, které se účtují za vyvezené GB. <br>– Náklady centra událostí se účtují podle jednotek propustnosti.  <br><br>**Export dat prostřednictvím Azure Storage a Azure Data Factory**: <br>-Log Analytics exportu dat a účtuje se na základě exportovaných GB. <br>– Azure Storage, které účtují GB uložené. <br>-Azure Data Factory se účtuje na základě kopie aktivit.
|     |         |

## <a name="next-steps"></a>Další kroky

Bez ohledu na to, kam vaše data ukládáte, můžete pokračovat v lovu a zkoumání pomocí Azure Sentinel.

Další informace naleznete v tématu:

- [Kurz: zkoumání incidentů pomocí služby Azure Sentinel](tutorial-investigate-cases.md)
- [Loven pro hrozby s Sentinel Azure](hunting.md)

---
title: Systémové funkce v protokolech Azure Monitor
description: Zápis vlastních dotazů do protokolů Azure Monitor pomocí systémových funkcí
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 1d26adfd2bd1a3fc1506a334b4b661b66172192d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510493"
---
# <a name="system-functions-on-azure-monitor-logs"></a>Systémové funkce v protokolech Azure Monitor

Azure Backup poskytuje sadu funkcí nazývaných systémové funkce nebo funkce řešení, které jsou ve výchozím nastavení dostupné v pracovních prostorech Log Analytics (LA).
 
Tyto funkce pracují s daty v [nezpracovaných Azure Backup tabulkách](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model) v La a vracejí formátovaná data, která vám pomůžou snadno načíst informace o všech entitách souvisejících s zálohováním pomocí jednoduchých dotazů. Uživatelé mohou předat parametry těmto funkcím k filtrování dat vrácených těmito funkcemi. 

Doporučuje se používat systémové funkce pro dotazování zálohovaných dat v pracovních prostorech LA pro vytváření vlastních sestav, protože poskytují řadu výhod, jak je popsáno v následující části.

## <a name="benefits-of-using-system-functions"></a>Výhody používání systémových funkcí

* **Jednodušší dotazy**: použití funkcí vám pomůže snížit počet spojení potřebných v dotazech. Ve výchozím nastavení funkce vrací "Sloučená" schémata, která obsahují všechny informace týkající se dané entity (instance zálohování, úlohy, trezoru atd.). Pokud například potřebujete získat seznam úspěšných úloh zálohování podle názvu zálohované položky a přidruženého kontejneru, jednoduché volání funkce **_AzureBackup_getJobs ()** vám poskytne všechny tyto informace pro každou úlohu. Na druhé straně dotazování nezpracovaných tabulek přímo vyžaduje, abyste provedli více spojení mezi tabulkami [AddonAzureBackupJobs](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model#addonazurebackupjobs) a [CoreAzureBackup](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model#coreazurebackup) .

* **Plynulý přechod ze starší verze diagnostické události**: pomocí systémových funkcí můžete hladce plynule přejít ze [starší diagnostické události](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#legacy-event) (AzureBackupReport v režimu AzureDiagnostics) na [události specifické pro prostředky](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users). Všechny systémové funkce, které poskytuje Azure Backup, vám umožní určit parametr, který vám umožní vybrat, jestli se má tato funkce dotazovat na data jenom z tabulek specifických pro prostředky, nebo dotazovat data ze starší tabulky i tabulek specifických pro prostředky (s odstraněním duplicit záznamů).
    * Pokud jste úspěšně migrovali do tabulek specifických pro prostředky, můžete se rozhodnout pro vyloučení starší tabulky z dotazu pomocí funkce.
    * Pokud v tuto chvíli provádíte migraci a máte nějaká data v starších tabulkách, které potřebujete pro účely analýzy, můžete si vybrat, jestli se má zahrnout starší tabulka. Když je přechod dokončený a už nepotřebujete data z tabulky starší verze, můžete jednoduše aktualizovat hodnotu parametru předaného funkci ve vašich dotazech, aby se vyloučila starší tabulka.
    * Pokud stále používáte jenom starší tabulku, funkce budou fungovat i v případě, že se rozhodnete zahrnout starší verzi tabulky přes stejný parametr. Doporučuje se ale v nejbližší době [Přepnout na tabulky specifické pro prostředky](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace) .

* **Omezuje možnost přerušení vlastních dotazů**: Pokud Azure Backup zavádí vylepšení schématu podkladových tabulek La, aby vyhovovala budoucím scénářům vytváření sestav, definice funkcí se také aktualizuje, aby se zohlednily změny schématu. Proto pokud používáte systémové funkce pro vytváření vlastních dotazů, dotazy nebudou přerušit, i když jsou v podkladovém schématu tabulky změny.

> [!NOTE]
> Systémové funkce jsou spravovány společností Microsoft a jejich definice není možné upravovat uživateli. Pokud budete potřebovat upravitelné funkce, můžete v nástroji LA vytvořit [uložené funkce](https://docs.microsoft.com/azure/azure-monitor/logs/functions) .

## <a name="types-of-system-functions-offered-by-azure-backup"></a>Typy systémových funkcí, které nabízí Azure Backup

* **Základní funkce**: Jedná se o funkce, které vám pomůžou při dotazování kterékoli z klíčových Azure Backup entit, jako jsou instance záloh, trezory, zásady, úlohy a fakturační entity. Například funkce **_AzureBackup_getBackupInstances** vrátí seznam všech instancí zálohování, které ve vašem prostředí existují, k poslednímu dokončenému dni (UTC). Parametry a vrácené schéma pro každou z těchto základních funkcí jsou shrnuty níže v tomto článku.

* **Funkce trendu**: Jedná se o funkce, které vracejí historické záznamy pro vaše entity související se zálohou (například instance služby Backup, fakturační skupiny), a umožňují získat informace o každodenních, týdenních a měsíčních trendech na klíčové metriky (například počet, využité úložiště) vztahující se k těmto entitám. Parametry a vrácené schéma pro každou z těchto funkcí trendu jsou shrnuté dál v tomto článku.

> [!NOTE]
> V současné době systémové funkce vrací data až k poslednímu dokončenému dni (v UTC). Data pro aktuální částečný den se nevrátí. Takže pokud chcete načíst záznamy pro aktuální den, budete muset použít tabulky RAW LA.


## <a name="list-of-system-functions"></a>Seznam systémových funkcí

### <a name="core-functions"></a>Základní funkce

#### <a name="_azurebackup_getvaults"></a>_AzureBackup_GetVaults ()

Tato funkce vrátí seznam všech trezorů Recovery Services ve vašem prostředí Azure, které jsou přidružené k pracovnímu prostoru LA.

**Parametry**

| **Název parametru** | **Popis** | **Požadováno?** | **Příklad hodnoty** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Tento parametr spolu s parametrem RangeEnd použijte pouze v případě, že potřebujete načíst všechny záznamy související s trezorem v časovém období z RangeStart na RangeEnd. Ve výchozím nastavení mají hodnoty RangeStart a RangeEnd hodnotu null, což způsobí, že funkce načte jenom poslední záznam pro každý trezor. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Tento parametr použijte spolu s parametrem RangeStart pouze v případě, že potřebujete načíst všechny záznamy související s trezorem v časovém období z RangeStart do RangeEnd. Ve výchozím nastavení mají hodnoty RangeStart a RangeEnd hodnotu null, což způsobí, že funkce načte jenom poslední záznam pro každý trezor. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Tento parametr použijte k filtrování výstupu funkce pro určitou sadu předplatných, kde existují zálohovaná data. Zadáním čárkami odděleného seznamu ID předplatných můžete načíst jenom ty trezory, které jsou v zadaných předplatných. Ve výchozím nastavení je hodnota tohoto parametru "*", což způsobí, že funkce vyhledá záznamy ve všech předplatných. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Tento parametr použijte k filtrování výstupu funkce pro určitou sadu oblastí, kde data záloh existují. Zadání seznamu oblastí oddělených čárkami jako parametru této funkce vám pomůže načíst pouze trezory, které jsou v určených oblastech. Ve výchozím nastavení je hodnota tohoto parametru "*", což způsobí, že funkce vyhledá záznamy ve všech oblastech. | N | "eastus, westus"|
| VaultList    |Pomocí tohoto parametru můžete filtrovat výstup funkce pro určitou sadu trezorů. Zadání čárkami oddělených názvů trezorů jako parametru této funkce vám pomůže načíst záznamy týkající se pouze zadaných trezorů. Ve výchozím nastavení je hodnota tohoto parametru "*", což způsobí, že funkce vyhledá záznamy ve všech trezorech. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Pomocí tohoto parametru můžete filtrovat výstup funkce na záznamy týkající se konkrétního typu trezoru. V současné době je jediným podporovaným typem trezoru "Microsoft. RecoveryServices/trezory", což je výchozí hodnota tohoto parametru. | N | Microsoft. RecoveryServices/trezory|
| ExcludeLegacyEvent  | Pomocí tohoto parametru můžete zvolit, jestli se má dotazovat na data v dřívější tabulce AzureDiagnostics nebo ne. Pokud hodnota tohoto parametru je false, funkce se dotazuje na data z tabulky AzureDiagnostics i z tabulek specifických pro prostředky. Pokud je hodnota tohoto parametru true, funkce dotazuje data pouze z tabulek specifických pro prostředky. Výchozí hodnota je true (pravda). | N | true |

**Vrácená pole**

| **Název pole** | **Popis** |
| -------------- | --------------- |
| Identifikátor | Primární klíč označuje jedinečné ID trezoru. |
| Id | ID Azure Resource Manager (ARM) trezoru |
| Name | Název trezoru |
| SubscriptionId | ID předplatného, ve kterém existuje trezor |
| Umístění | Umístění, ve kterém existuje trezor |
| VaultStore_StorageReplicationType | Typ replikace úložiště přidružený k trezoru |
| Značky | Značky trezoru |
| TimeGenerated | Časové razítko záznamu |
| Typ |  Typ trezoru, který je Microsoft. RecoveryServices/trezory|

#### <a name="_azurebackup_getpolicies"></a>_AzureBackup_GetPolicies ()

Tato funkce vrátí seznam zásad zálohování používaných ve vašem prostředí Azure spolu s podrobnými informacemi o každé zásadě, jako je typ zdroje dat, typ replikace úložiště a tak dále.

**Parametry**

| **Název parametru** | **Popis** | **Požadováno?** | **Příklad hodnoty** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Tento parametr použijte spolu s parametrem RangeStart pouze v případě, že potřebujete načíst všechny záznamy související s zásadami v časovém období z RangeStart do RangeEnd. Ve výchozím nastavení mají hodnoty RangeStart a RangeEnd hodnotu null, což způsobí, že funkce načte jenom poslední záznam pro každou zásadu. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Tento parametr použijte spolu s parametrem RangeStart pouze v případě, že potřebujete načíst všechny záznamy související s zásadami v časovém období od RangeStart do RangeEnd. Ve výchozím nastavení mají hodnoty RangeStart a RangeEnd hodnotu null, což způsobí, že funkce načte jenom poslední záznam pro každou zásadu. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Tento parametr použijte k filtrování výstupu funkce pro určitou sadu předplatných, kde existují zálohovaná data. Zadáním čárkami odděleného seznamu ID předplatných můžete načíst jenom ty zásady, které jsou v zadaných předplatných. Ve výchozím nastavení je hodnota tohoto parametru "*", což způsobí, že funkce vyhledá záznamy ve všech předplatných. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Tento parametr použijte k filtrování výstupu funkce pro určitou sadu oblastí, kde data záloh existují. Zadání čárkami odděleného seznamu oblastí jako parametru pro tuto funkci vám pomůže načíst pouze ty zásady, které jsou v určených oblastech. Ve výchozím nastavení je hodnota tohoto parametru "*", což způsobí, že funkce vyhledá záznamy ve všech oblastech. | N | "eastus, westus"|
| VaultList    |Pomocí tohoto parametru můžete filtrovat výstup funkce pro určitou sadu trezorů. Zadání čárkami oddělených názvů trezorů jako parametru této funkce vám pomůže načíst záznamy zásad, které se vztahují pouze na zadané trezory. Ve výchozím nastavení je hodnota tohoto parametru "*", což způsobí, že funkce hledá záznamy zásad napříč všemi trezory. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Pomocí tohoto parametru můžete filtrovat výstup funkce na záznamy týkající se konkrétního typu trezoru. V současné době je jediným podporovaným typem trezoru "Microsoft. RecoveryServices/trezory", což je výchozí hodnota tohoto parametru. | N | Microsoft. RecoveryServices/trezory|
| ExcludeLegacyEvent  | Pomocí tohoto parametru můžete zvolit, jestli se má dotazovat na data v dřívější tabulce AzureDiagnostics nebo ne. Pokud hodnota tohoto parametru je false, funkce se dotazuje na data z tabulky AzureDiagnostics i z tabulek specifických pro prostředky. Pokud je hodnota tohoto parametru true, funkce dotazuje data pouze z tabulek specifických pro prostředky. Výchozí hodnota je true (pravda). | N | true |
| BackupSolutionList | Pomocí tohoto parametru můžete filtrovat výstup funkce pro určitou sadu zálohovacích řešení používaných ve vašem prostředí Azure. Pokud například zadáte "zálohování virtuálních počítačů Azure, SQL v zálohování virtuálního počítače Azure, DPM" jako hodnotu tohoto parametru, funkce vrátí jenom záznamy, které souvisejí s položkami zálohovanými pomocí zálohování virtuálních počítačů Azure, SQL ve službě Azure VM Backup nebo DPM do Azure Backup. Ve výchozím nastavení je hodnota tohoto parametru "*", což znamená, že funkce vrací záznamy související se všemi zálohovacími řešeními, které jsou podporovány v sestavách zálohování (podporované hodnoty jsou "zálohování virtuálních počítačů Azure", "SQL ve službě zálohování virtuálních počítačů Azure", "SAP HANA v zálohování virtuálních počítačů Azure", "Azure Storage (soubory Azure) Backup", "Azure Backup Agent", "DPM", "Azure Backup Server" nebo čárkami oddělená kombinace kterékoli z těchto hodnot). | N | "Zálohování virtuálních počítačů Azure, SQL v zálohování virtuálních počítačů Azure, DPM, Azure Backup Agent" |

**Vrácená pole**

| **Název pole** | **Popis** |
| -------------- | --------------- |
| Identifikátor | Primární klíč označuje jedinečné ID zásady. |
| Id | ID Azure Resource Manager (ARM) zásad |
| Name | Název zásady |
| Řešení zálohování | Řešení zálohování, ke kterému je zásada přidružená Například zálohování virtuálních počítačů Azure, SQL v zálohování virtuálních počítačů Azure atd. |
| TimeGenerated | Časové razítko záznamu |
| VaultUniqueId | Cizí klíč, který odkazuje na trezor přidružený k zásadě |
| VaultResourceId | ID Azure Resource Manager (ARM) úložiště přidruženého k zásadě |
| VaultName | Název trezoru přidruženého k zásadě |
| VaultTags | Značky trezoru přidruženého k zásadě |
| VaultLocation | Umístění trezoru přidruženého k zásadě |
| VaultSubscriptionId | ID předplatného trezoru přidruženého k zásadě |
| VaultStore_StorageReplicationType | Typ replikace úložiště pro trezor přidružený k zásadě |
| VaultType | Typ trezoru, který je Microsoft. RecoveryServices/trezory |
| ExtendedProperties | Další vlastnosti zásady |

#### <a name="_azurebackup_getjobs"></a>_AzureBackup_GetJobs ()

Tato funkce vrátí seznam všech úloh zálohování a obnovení, které byly aktivovány v určitém časovém rozsahu, spolu s podrobnými informacemi o každé úloze, jako je například stav úlohy, doba trvání úlohy, přenesená data atd.

**Parametry**

| **Název parametru** | **Popis** | **Požadováno?** | **Příklad hodnoty** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Tento parametr spolu s parametrem RangeEnd slouží k načtení seznamu všech úloh, které byly zahájeny v časovém období z RangeStart na RangeEnd. | Y | "2021-03-03 00:00:00" |
| RangeEnd     | Pomocí tohoto parametru spolu s parametrem RangeStart načtěte seznam všech úloh, které byly zahájeny v časovém období z RangeStart do RangeEnd. | Y |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Tento parametr použijte k filtrování výstupu funkce pro určitou sadu předplatných, kde existují zálohovaná data. Zadáním čárkami odděleného seznamu ID předplatných můžete načíst jenom úlohy, které jsou přidružené k trezorům v zadaných předplatných. Ve výchozím nastavení je hodnota tohoto parametru "*", což způsobí, že funkce vyhledá záznamy ve všech předplatných. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Tento parametr použijte k filtrování výstupu funkce pro určitou sadu oblastí, kde data záloh existují. Zadání seznamu oblastí oddělených čárkami jako parametru této funkce vám pomůže načíst pouze úlohy, které jsou přidruženy k trezorům v určených oblastech. Ve výchozím nastavení je hodnota tohoto parametru "*", což způsobí, že funkce vyhledá záznamy ve všech oblastech. | N | "eastus, westus"|
| VaultList    | Pomocí tohoto parametru můžete filtrovat výstup funkce pro určitou sadu trezorů. Zadání čárkami oddělených názvů trezorů jako parametru této funkce vám pomůže načíst úlohy, které se týkají jenom určených trezorů. Ve výchozím nastavení je hodnota tohoto parametru "*", což způsobí, že funkce vyhledává úlohy ve všech trezorech. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Pomocí tohoto parametru můžete filtrovat výstup funkce na záznamy týkající se konkrétního typu trezoru. V současné době je jediným podporovaným typem trezoru "Microsoft. RecoveryServices/trezory", což je výchozí hodnota tohoto parametru. | N | Microsoft. RecoveryServices/trezory|
| ExcludeLegacyEvent  | Pomocí tohoto parametru můžete zvolit, jestli se má dotazovat na data v dřívější tabulce AzureDiagnostics nebo ne. Pokud hodnota tohoto parametru je false, funkce se dotazuje na data z tabulky AzureDiagnostics i z tabulek specifických pro prostředky. Pokud je hodnota tohoto parametru true, funkce dotazuje data pouze z tabulek specifických pro prostředky. Výchozí hodnota je true (pravda). | N | true |
| BackupSolutionList | Pomocí tohoto parametru můžete filtrovat výstup funkce pro určitou sadu zálohovacích řešení používaných ve vašem prostředí Azure. Pokud například zadáte "zálohování virtuálních počítačů Azure, SQL v zálohování virtuálního počítače Azure, DPM" jako hodnotu tohoto parametru, funkce vrátí jenom záznamy, které souvisejí s položkami zálohovanými pomocí zálohování virtuálních počítačů Azure, SQL ve službě Azure VM Backup nebo DPM do Azure Backup. Ve výchozím nastavení je hodnota tohoto parametru "*", což znamená, že funkce vrací záznamy související se všemi zálohovacími řešeními, které jsou podporovány v sestavách zálohování (podporované hodnoty jsou "zálohování virtuálních počítačů Azure", "SQL ve službě zálohování virtuálních počítačů Azure", "SAP HANA v zálohování virtuálních počítačů Azure", "Azure Storage (soubory Azure) Backup", "Azure Backup Agent", "DPM", "Azure Backup Server" nebo čárkami oddělená kombinace kterékoli z těchto hodnot). | N | "Zálohování virtuálních počítačů Azure, SQL v zálohování virtuálních počítačů Azure, DPM, Azure Backup Agent" |
| JobOperationList | Pomocí tohoto parametru můžete filtrovat výstup funkce pro určitý typ úlohy. Například zálohování nebo obnovení. Ve výchozím nastavení je hodnota tohoto parametru "*", což umožňuje hledat úlohy zálohování i obnovení. | N | Zálohovat | 
| JobStatusList | Pomocí tohoto parametru můžete filtrovat výstup funkce pro určitý stav úlohy. Například dokončeno, chyba a tak dále. Ve výchozím nastavení je hodnota tohoto parametru "*", což způsobí, že funkce vyhledá všechny úlohy bez ohledu na stav. | N | "Selhání, CompletedWithWarnings" |
| JobFailureCodeList | Pomocí tohoto parametru můžete filtrovat výstup funkce pro konkrétní kód chyby. Ve výchozím nastavení je hodnota tohoto parametru "*", což způsobí, že funkce vyhledá všechny úlohy bez ohledu na kód chyby. | N | Nástup
| DatasourceSetName | Pomocí tohoto parametru můžete filtrovat výstup funkce na konkrétní nadřazený prostředek. Pokud chcete například vrátit SQL do instancí zálohování virtuálních počítačů Azure patřící do virtuálního počítače "TestVM", zadejte _TestVM_ jako hodnotu tohoto parametru. Ve výchozím nastavení je tato hodnota "*", která umožňuje hledat záznamy ve všech instancích zálohování. | N | TestVM |
| BackupInstanceName | Pomocí tohoto parametru můžete vyhledat úlohy v určité instanci zálohování podle názvu. Ve výchozím nastavení je tato hodnota "*", která umožňuje hledat záznamy ve všech instancích zálohování. | N | TestVM |
| ExcludeLog | Tento parametr použijte k vyloučení úloh protokolu vrácených funkcí funkce (pomáhá při výkonu dotazů). Ve výchozím nastavení je hodnota tohoto parametru true, což způsobí, že funkce vyloučí úlohy protokolu. | N | true


**Vrácená pole**

| **Název pole** | **Popis** |
| -------------- | --------------- |
| Identifikátor | Primární klíč označuje jedinečné ID úlohy. |
| OperationCategory | Kategorie prováděné operace. Například Backup, Restore |
| Operace | Podrobnosti prováděné operace. Například protokol (pro zálohování protokolu)|
| Status | Stav úlohy Například dokončeno, chyba, CompletedWithWarnings |
| ErrorTitle | Kód chyby úlohy |
| StartTime | Datum a čas spuštění úlohy |
| DurationInSecs | Doba trvání úlohy v sekundách |
| DataTransferredInMBs | Data přenesená úlohou v MB |
| RestoreJobRPDateTime | Datum a čas vytvoření obnoveného bodu obnovení |
| RestoreJobRPLocation | Umístění, kam se uložil bod obnovení, který se má obnovit |
| BackupInstanceUniqueId | Cizí klíč, který odkazuje na instanci zálohování přidruženou k úloze |
| BackupInstanceId | ID Azure Resource Manager (ARM) instance zálohy přidružené k úloze |
| BackupInstanceFriendlyName | Název instance zálohy přidružené k úloze |
| DatasourceResourceId | ID Azure Resource Manager (ARM) podkladového zdroje dat přidruženého k úloze Například Azure Resource Manager (ARM) ID virtuálního počítače |
| DatasourceFriendlyName | Popisný název podkladového zdroje dat přidruženého k úloze |
| DatasourceType | Typ zdroje dat přidruženého k úloze Například Microsoft. COMPUTE/virtualMachines. |
| BackupSolution | Řešení zálohování, ke kterému je úloha přidružená Například zálohování virtuálních počítačů Azure, SQL v zálohování virtuálních počítačů Azure atd. |
| DatasourceSetResourceId | ID Azure Resource Manager (ARM) nadřazeného prostředku zdroje dat (kdykoli je to možné). Například pro SQL ve zdroji dat virtuálního počítače Azure bude toto pole obsahovat ID Azure Resource Manager (ARM) virtuálního počítače, ve kterém SQL Database existuje. |
| DatasourceSetType | Typ nadřazeného prostředku zdroje dat (kdykoli je to možné). Například pro SAP HANA ve zdroji dat virtuálního počítače Azure bude toto pole Microsoft. COMPUTE/virtualMachines, protože nadřazeným prostředkem je virtuální počítač Azure. |
| VaultResourceId | ID Azure Resource Manager (ARM) úložiště, které je přidružené k úloze |
| VaultUniqueId | Cizí klíč, který odkazuje na trezor přidružený k úloze |
| VaultName | Název trezoru přidruženého k úloze |
| VaultTags | Značky trezoru přidruženého k úloze |
| VaultSubscriptionId | ID předplatného trezoru přidruženého k úloze |
| VaultLocation | Umístění trezoru přidruženého k úloze |
| VaultStore_StorageReplicationType | Typ replikace úložiště pro trezor přidružený k úloze |
| VaultType | Typ trezoru, který je Microsoft. RecoveryServices/trezory |
| TimeGenerated | Časové razítko záznamu |

#### <a name="_azurebackup_getbackupinstances"></a>_AzureBackup_GetBackupInstances ()

Tato funkce vrátí seznam instancí zálohy přidružených k vašim trezorům Recovery Services společně s podrobnými informacemi o jednotlivých instancích zálohy, jako je například spotřeba cloudového úložiště, přidružená zásada atd.

**Parametry**

| **Název parametru** | **Popis** | **Požadováno?** | **Příklad hodnoty** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Tento parametr použijte spolu s parametrem RangeEnd pouze v případě, že potřebujete načíst všechny záznamy související s instancí zálohování za časové období z RangeStart do RangeEnd. Ve výchozím nastavení mají hodnoty RangeStart a RangeEnd hodnotu null, což způsobí, že funkce načte jenom poslední záznam pro každou instanci zálohování. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Tento parametr použijte spolu s parametrem RangeStart pouze v případě, že potřebujete načíst všechny záznamy související s instancí zálohování za časové období z RangeStart do RangeEnd. Ve výchozím nastavení mají hodnoty RangeStart a RangeEnd hodnotu null, což způsobí, že funkce načte jenom poslední záznam pro každou instanci zálohování. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Tento parametr použijte k filtrování výstupu funkce pro určitou sadu předplatných, kde existují zálohovaná data. Zadáním čárkami odděleného seznamu ID předplatných můžete načíst jenom ty instance zálohy, které jsou v zadaných předplatných. Ve výchozím nastavení je hodnota tohoto parametru "*", což způsobí, že funkce vyhledá záznamy ve všech předplatných. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Tento parametr použijte k filtrování výstupu funkce pro určitou sadu oblastí, kde data záloh existují. Zadání čárkami odděleného seznamu oblastí jako parametru pro tuto funkci vám pomůže načíst pouze ty instance zálohy, které jsou v určených oblastech. Ve výchozím nastavení je hodnota tohoto parametru "*", což způsobí, že funkce vyhledá záznamy ve všech oblastech. | N | "eastus, westus"|
| VaultList    |Pomocí tohoto parametru můžete filtrovat výstup funkce pro určitou sadu trezorů. Zadání čárkami oddělených názvů trezorů jako parametru této funkce vám pomůže načíst záznamy instancí zálohy, které se vztahují pouze na zadané trezory. Ve výchozím nastavení je hodnota tohoto parametru "*", což způsobí, že funkce hledá záznamy instancí zálohy napříč všemi trezory. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Pomocí tohoto parametru můžete filtrovat výstup funkce na záznamy týkající se konkrétního typu trezoru. V současné době je jediným podporovaným typem trezoru "Microsoft. RecoveryServices/trezory", což je výchozí hodnota tohoto parametru. | N | Microsoft. RecoveryServices/trezory|
| ExcludeLegacyEvent  | Pomocí tohoto parametru můžete zvolit, jestli se má dotazovat na data v dřívější tabulce AzureDiagnostics nebo ne. Pokud hodnota tohoto parametru je false, funkce se dotazuje na data z tabulky AzureDiagnostics i z tabulek specifických pro prostředky. Pokud je hodnota tohoto parametru true, funkce dotazuje data pouze z tabulek specifických pro prostředky. Výchozí hodnota je true (pravda). | N | true |
| BackupSolutionList | Pomocí tohoto parametru můžete filtrovat výstup funkce pro určitou sadu zálohovacích řešení používaných ve vašem prostředí Azure. Pokud například zadáte "zálohování virtuálních počítačů Azure, SQL v zálohování virtuálního počítače Azure, DPM" jako hodnotu tohoto parametru, funkce vrátí jenom záznamy, které souvisejí s položkami zálohovanými pomocí zálohování virtuálních počítačů Azure, SQL ve službě Azure VM Backup nebo DPM do Azure Backup. Ve výchozím nastavení je hodnota tohoto parametru "*", což znamená, že funkce vrací záznamy související se všemi zálohovacími řešeními, které jsou podporovány v sestavách zálohování (podporované hodnoty jsou "zálohování virtuálních počítačů Azure", "SQL ve službě zálohování virtuálních počítačů Azure", "SAP HANA v zálohování virtuálních počítačů Azure", "Azure Storage (soubory Azure) Backup", "Azure Backup Agent", "DPM", "Azure Backup Server" nebo čárkami oddělená kombinace kterékoli z těchto hodnot). | N | "Zálohování virtuálních počítačů Azure, SQL v zálohování virtuálních počítačů Azure, DPM, Azure Backup Agent" |
| ProtectionInfoList | Pomocí tohoto parametru můžete vybrat, jestli se mají zahrnout jenom ty instance služby Backup, které jsou aktivně chráněné, nebo taky zahrnout tyto instance, pro které se ochrana zastavila, a instance, pro které se čeká na počáteční zálohování. Podporované hodnoty jsou "protected", "ProtectionStopped", "InitialBackupPending" nebo čárkami oddělené kombinace kterékoli z těchto hodnot. Ve výchozím nastavení je tato hodnota "*", což způsobí, že funkce vyhledá všechny instance služby Backup bez ohledu na podrobnosti o ochraně. | N | Proti |
| DatasourceSetName | Pomocí tohoto parametru můžete filtrovat výstup funkce na konkrétní nadřazený prostředek. Pokud chcete například vrátit SQL do instancí zálohování virtuálních počítačů Azure patřící do virtuálního počítače "TestVM", zadejte _TestVM_ jako hodnotu tohoto parametru. Ve výchozím nastavení je tato hodnota "*", která umožňuje hledat záznamy ve všech instancích zálohování. | N | TestVM |
| BackupInstanceName | Pomocí tohoto parametru můžete vyhledat konkrétní instanci zálohování podle názvu. Ve výchozím nastavení je tato hodnota "*", což způsobí, že funkce vyhledá všechny instance služby Backup. | N | TestVM |
| DisplayAllFields | Pomocí tohoto parametru lze zvolit, zda má být načtena pouze podmnožina polí vrácených funkcí. Pokud hodnota tohoto parametru je false, funkce eliminuje informace týkající se úložiště a bodu uchovávání informací z výstupu funkce. To je užitečné, pokud tuto funkci používáte jako zprostředkující krok ve větším dotazu a potřebujete optimalizovat výkon dotazu odstraněním sloupců, které nepotřebujete k analýze. Ve výchozím nastavení je hodnota tohoto parametru true, což způsobí, že funkce vrátí všechna pole související s instancí zálohy. | N | true |

**Vrácená pole**

| **Název pole** | **Popis** |
| -------------- | --------------- |
| Identifikátor | Primární klíč označuje jedinečné ID instance zálohy. |
| Id | ID Azure Resource Manager (ARM) instance zálohy |
| FriendlyName | Popisný název instance zálohování |
| ProtectionInfo | Informace o nastavení ochrany instance zálohování Například nakonfigurovaná ochrana, zastavená ochrana, počáteční zálohování čeká na vyřízení. |
| LatestRecoveryPoint | Datum a čas posledního bodu obnovení přidruženého k instanci zálohování |
| OldestRecoveryPoint | Datum a čas nejstaršího bodu obnovení přidruženého k instanci zálohování |
| SourceSizeInMBs | Velikost front-endu instance zálohy v MB |
| VaultStore_StorageConsumptionInMBs | Celkové cloudové úložiště spotřebované instancí zálohování v trezoru – úroveň Standard |
| DataSourceFriendlyName | Popisný název zdroje dat odpovídající instanci zálohy |
| BackupSolution | Řešení zálohování, ke kterému je instance zálohy přidružená. Například zálohování virtuálních počítačů Azure, SQL v zálohování virtuálních počítačů Azure atd. |
| DatasourceType | Typ zdroje dat odpovídající instanci zálohy Například Microsoft. COMPUTE/virtualMachines. |
| DatasourceResourceId | Azure Resource Manager (ARM) ID podkladového zdroje dat odpovídající instanci zálohy. Například Azure Resource Manager (ARM) ID virtuálního počítače |
| DatasourceSetFriendlyName | Popisný název nadřazeného prostředku zdroje dat (kdykoli je to možné). Například pro SQL ve zdroji dat virtuálního počítače Azure bude toto pole obsahovat název virtuálního počítače, ve kterém SQL Database existuje. |
| DatasourceSetResourceId | ID Azure Resource Manager (ARM) nadřazeného prostředku zdroje dat (kdykoli je to možné). Například pro SQL ve zdroji dat virtuálního počítače Azure bude toto pole obsahovat ID Azure Resource Manager (ARM) virtuálního počítače, ve kterém SQL Database existuje. |
| DatasourceSetType | Typ nadřazeného prostředku zdroje dat (kdykoli je to možné). Například pro SAP HANA ve zdroji dat virtuálního počítače Azure bude toto pole Microsoft. COMPUTE/virtualMachines, protože nadřazeným prostředkem je virtuální počítač Azure. |
| PolicyName | Název zásady přidružené k instanci zálohování |
| PolicyUniqueId | Cizí klíč, který odkazuje na zásadu přidruženou k instanci zálohování  |
| PolicyId | ID Azure Resource Manager (ARM) zásad přidružených k instanci zálohy |
| VaultResourceId | ID Azure Resource Manager (ARM) úložiště přidruženého k instanci zálohování |
| VaultUniqueId | Cizí klíč, který odkazuje na trezor přidružený k instanci zálohování |
| VaultName | Název trezoru přidruženého k instanci zálohování |
| VaultTags | Značky trezoru přidruženého k instanci zálohování |
| VaultSubscriptionId | ID předplatného trezoru přidruženého k instanci zálohování |
| VaultLocation | Umístění trezoru přidruženého k instanci zálohování |
| VaultStore_StorageReplicationType | Typ replikace úložiště pro trezor přidružený k instanci zálohování |
| VaultType | Typ trezoru, který je Microsoft. RecoveryServices/trezory |
| TimeGenerated | Časové razítko záznamu |

#### <a name="_azurebackup_getbillinggroups"></a>_AzureBackup_GetBillingGroups ()

Tato funkce vrátí seznam všech fakturačních entit souvisejících se zálohou (fakturačních skupin) spolu s informacemi o komponentách fakturace klíčů, jako je velikost front-endu a celkové cloudové úložiště.

**Parametry**

| **Název parametru** | **Popis** | **Požadováno?** | **Příklad hodnoty** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Tento parametr použijte spolu s parametrem RangeEnd jenom v případě, že potřebujete načíst všechny záznamy související s fakturační skupinou za časové období z RangeStart do RangeEnd. Ve výchozím nastavení mají hodnoty RangeStart a RangeEnd hodnotu null, což způsobí, že funkce načte jenom poslední záznam pro každou fakturační skupinu. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Tento parametr použijte spolu s parametrem RangeStart pouze v případě, že potřebujete načíst záznamy související s fakturačními skupinami v časovém období od RangeStart do RangeEnd. Ve výchozím nastavení mají hodnoty RangeStart a RangeEnd hodnotu null, což způsobí, že funkce načte jenom poslední záznam pro každou fakturační skupinu. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Tento parametr použijte k filtrování výstupu funkce pro určitou sadu předplatných, kde existují zálohovaná data. Zadáním čárkami odděleného seznamu ID předplatných můžete načíst jenom ty fakturační skupiny, které jsou v zadaných předplatných. Ve výchozím nastavení je hodnota tohoto parametru "*", což způsobí, že funkce vyhledá záznamy ve všech předplatných. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Tento parametr použijte k filtrování výstupu funkce pro určitou sadu oblastí, kde data záloh existují. Zadání čárkami odděleného seznamu oblastí jako parametru pro tuto funkci vám pomůže načíst pouze ty fakturační skupiny, které jsou v určených oblastech. Ve výchozím nastavení je hodnota tohoto parametru "*", což způsobí, že funkce vyhledá záznamy ve všech oblastech. | N | "eastus, westus"|
| VaultList    |Pomocí tohoto parametru můžete filtrovat výstup funkce pro určitou sadu trezorů. Zadání čárkami oddělených názvů trezorů jako parametru této funkce vám pomůže načíst záznamy instancí zálohy, které se vztahují pouze na zadané trezory. Ve výchozím nastavení je hodnota tohoto parametru "*", což způsobí, že funkce vyhledá záznamy fakturačních skupin ve všech trezorech. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Pomocí tohoto parametru můžete filtrovat výstup funkce na záznamy týkající se konkrétního typu trezoru. V současné době je jediným podporovaným typem trezoru "Microsoft. RecoveryServices/trezory", což je výchozí hodnota tohoto parametru. | N | Microsoft. RecoveryServices/trezory|
| ExcludeLegacyEvent  | Pomocí tohoto parametru můžete zvolit, jestli se má dotazovat na data v dřívější tabulce AzureDiagnostics nebo ne. Pokud hodnota tohoto parametru je false, funkce se dotazuje na data z tabulky AzureDiagnostics i z tabulek specifických pro prostředky. Pokud je hodnota tohoto parametru true, funkce dotazuje data pouze z tabulek specifických pro prostředky. Výchozí hodnota je true (pravda). | N | true |
| BackupSolutionList | Pomocí tohoto parametru můžete filtrovat výstup funkce pro určitou sadu zálohovacích řešení používaných ve vašem prostředí Azure. Pokud například zadáte "zálohování virtuálních počítačů Azure, SQL v zálohování virtuálního počítače Azure, DPM" jako hodnotu tohoto parametru, funkce vrátí jenom záznamy, které souvisejí s položkami zálohovanými pomocí zálohování virtuálních počítačů Azure, SQL ve službě Azure VM Backup nebo DPM do Azure Backup. Ve výchozím nastavení je hodnota tohoto parametru "*", což znamená, že funkce vrací záznamy související se všemi zálohovacími řešeními, které jsou podporovány v sestavách zálohování (podporované hodnoty jsou "zálohování virtuálních počítačů Azure", "SQL ve službě zálohování virtuálních počítačů Azure", "SAP HANA v zálohování virtuálních počítačů Azure", "Azure Storage (soubory Azure) Backup", "Azure Backup Agent", "DPM", "Azure Backup Server" nebo čárkami oddělená kombinace kterékoli z těchto hodnot). | N | "Zálohování virtuálních počítačů Azure, SQL v zálohování virtuálních počítačů Azure, DPM, Azure Backup Agent" |
| BillingGroupName | Pomocí tohoto parametru můžete vyhledat konkrétní fakturační skupinu podle názvu. Ve výchozím nastavení je tato hodnota "*", která umožňuje hledat ve všech fakturačních skupinách. | N | TestVM |

**Vrácená pole**

| **Název pole** | **Popis** |
| -------------- | --------------- |
| Identifikátor | Primární klíč označuje jedinečné ID fakturační skupiny. |
| FriendlyName | Popisný název skupiny fakturace |
| Name | Název fakturační skupiny |
| Typ | Typ fakturační skupiny Například ProtectedContainer nebo BackupItem |
| SourceSizeInMBs | Velikost front-endu ve fakturační skupině v MB |
| VaultStore_StorageConsumptionInMBs | Celková velikost cloudového úložiště spotřebovaného fakturační skupinou v trezoru – úroveň Standard |
| BackupSolution | Řešení zálohování, ke kterému je přidružená fakturační skupina Například zálohování virtuálních počítačů Azure, SQL v zálohování virtuálních počítačů Azure atd. |
| VaultResourceId | ID Azure Resource Manager (ARM) úložiště přidruženého ke skupině fakturace |
| VaultUniqueId | Cizí klíč, který odkazuje na trezor přidružený ke skupině fakturace |
| VaultName | Název trezoru, který je přidružený ke skupině fakturace |
| VaultTags | Značky trezoru přidružené ke skupině fakturace |
| VaultSubscriptionId | ID předplatného trezoru přidruženého ke skupině fakturace |
| VaultLocation | Umístění trezoru přidruženého ke skupině fakturace |
| VaultStore_StorageReplicationType | Typ replikace úložiště pro trezor přidružený ke skupině fakturace |
| VaultType | Typ trezoru, který je Microsoft. RecoveryServices/trezory |
| TimeGenerated | Časové razítko záznamu |
| ExtendedProperties | Další vlastnosti fakturační skupiny |

### <a name="trend-functions"></a>Funkce trendu

#### <a name="_azurebackup_getbackupinstancestrends"></a>_AzureBackup_GetBackupInstancesTrends ()

Tato funkce vrátí historické záznamy pro každou instanci zálohování, což vám umožní zobrazit klíčové denní, týdenní a měsíční trendy související s počtem instancí zálohování a spotřebou úložiště na více úrovních členitosti.

**Parametry**

| **Název parametru** | **Popis** | **Požadováno?** | **Příklad hodnoty** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Použijte tento parametr spolu s parametrem RangeEnd k načtení všech záznamů souvisejících s instancí zálohování za časové období z RangeStart na RangeEnd. | Y | "2021-03-03 00:00:00" |
| RangeEnd     | Pomocí tohoto parametru spolu s parametrem RangeStart načtěte všechny záznamy související s instancemi zálohy v časovém období z RangeStart do RangeEnd. | Y |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Tento parametr použijte k filtrování výstupu funkce pro určitou sadu předplatných, kde existují zálohovaná data. Zadáním čárkami odděleného seznamu ID předplatných můžete načíst jenom ty instance zálohy, které jsou v zadaných předplatných. Ve výchozím nastavení je hodnota tohoto parametru "*", což způsobí, že funkce vyhledá záznamy ve všech předplatných. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Tento parametr použijte k filtrování výstupu funkce pro určitou sadu oblastí, kde data záloh existují. Zadání čárkami odděleného seznamu oblastí jako parametru pro tuto funkci vám pomůže načíst pouze ty instance zálohy, které jsou v určených oblastech. Ve výchozím nastavení je hodnota tohoto parametru "*", což způsobí, že funkce vyhledá záznamy ve všech oblastech. | N | "eastus, westus"|
| VaultList    |Pomocí tohoto parametru můžete filtrovat výstup funkce pro určitou sadu trezorů. Zadání čárkami oddělených názvů trezorů jako parametru této funkce vám pomůže načíst záznamy instancí zálohy, které se vztahují pouze na zadané trezory. Ve výchozím nastavení je hodnota tohoto parametru "*", což způsobí, že funkce hledá záznamy instancí zálohy napříč všemi trezory. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Pomocí tohoto parametru můžete filtrovat výstup funkce na záznamy týkající se konkrétního typu trezoru. V současné době je jediným podporovaným typem trezoru "Microsoft. RecoveryServices/trezory", což je výchozí hodnota tohoto parametru. | N | Microsoft. RecoveryServices/trezory|
| ExcludeLegacyEvent  | Pomocí tohoto parametru můžete zvolit, jestli se má dotazovat na data v dřívější tabulce AzureDiagnostics nebo ne. Pokud hodnota tohoto parametru je false, funkce se dotazuje na data z tabulky AzureDiagnostics i z tabulek specifických pro prostředky. Pokud je hodnota tohoto parametru true, funkce dotazuje data pouze z tabulek specifických pro prostředky. Výchozí hodnota je true (pravda). | N | true |
| BackupSolutionList | Pomocí tohoto parametru můžete filtrovat výstup funkce pro určitou sadu zálohovacích řešení používaných ve vašem prostředí Azure. Pokud například zadáte "zálohování virtuálních počítačů Azure, SQL v zálohování virtuálního počítače Azure, DPM" jako hodnotu tohoto parametru, funkce vrátí jenom záznamy, které souvisejí s položkami zálohovanými pomocí zálohování virtuálních počítačů Azure, SQL ve službě Azure VM Backup nebo DPM do Azure Backup. Ve výchozím nastavení je hodnota tohoto parametru "*", což znamená, že funkce vrací záznamy související se všemi zálohovacími řešeními, které jsou podporovány v sestavách zálohování (podporované hodnoty jsou "zálohování virtuálních počítačů Azure", "SQL ve službě zálohování virtuálních počítačů Azure", "SAP HANA v zálohování virtuálních počítačů Azure", "Azure Storage (soubory Azure) Backup", "Azure Backup Agent", "DPM", "Azure Backup Server" nebo čárkami oddělená kombinace kterékoli z těchto hodnot). | N | "Zálohování virtuálních počítačů Azure, SQL v zálohování virtuálních počítačů Azure, DPM, Azure Backup Agent" |
| ProtectionInfoList | Pomocí tohoto parametru můžete vybrat, jestli se mají zahrnout jenom ty instance služby Backup, které jsou aktivně chráněné, nebo taky zahrnout tyto instance, pro které se ochrana zastavila, a instance, pro které se čeká na počáteční zálohování. Podporované hodnoty jsou "protected", "ProtectionStopped", "InitialBackupPending" nebo čárkami oddělené kombinace kterékoli z těchto hodnot. Ve výchozím nastavení je tato hodnota "*", což způsobí, že funkce vyhledá všechny instance služby Backup bez ohledu na podrobnosti o ochraně. | N | Proti |
| DatasourceSetName | Pomocí tohoto parametru můžete filtrovat výstup funkce na konkrétní nadřazený prostředek. Pokud chcete například vrátit SQL do instancí zálohování virtuálních počítačů Azure patřící do virtuálního počítače "TestVM", zadejte _TestVM_ jako hodnotu tohoto parametru. Ve výchozím nastavení je tato hodnota "*", která umožňuje hledat záznamy ve všech instancích zálohování. | N | TestVM |
| BackupInstanceName | Pomocí tohoto parametru můžete vyhledat konkrétní instanci zálohování podle názvu. Ve výchozím nastavení je tato hodnota "*", což způsobí, že funkce vyhledá všechny instance služby Backup. | N | TestVM |
| DisplayAllFields | Pomocí tohoto parametru lze zvolit, zda má být načtena pouze podmnožina polí vrácených funkcí. Pokud hodnota tohoto parametru je false, funkce eliminuje informace týkající se úložiště a bodu uchovávání informací z výstupu funkce. To je užitečné, pokud tuto funkci používáte jako zprostředkující krok ve větším dotazu a potřebujete optimalizovat výkon dotazu odstraněním sloupců, které nepotřebujete k analýze. Ve výchozím nastavení je hodnota tohoto parametru true, což způsobí, že funkce vrátí všechna pole související s instancí zálohy. | N | true |
| AggregationType | Pomocí tohoto parametru můžete zadat časové rozlišení, při kterém se mají data načíst. Pokud je hodnota tohoto parametru "Day", funkce vrátí záznam za instanci zálohy za den, což vám umožní analyzovat denní trendy spotřeby úložiště a počtu instancí zálohy. Pokud je hodnota tohoto parametru "týdně", funkce vrátí záznam za instanci zálohy za týden, což vám umožní analyzovat týdenní trendy. Podobně můžete zadat "měsíční" pro analýzu měsíčních trendů. Výchozí hodnota je "denně". Pokud zobrazujete data v rámci větších časových rozsahů, doporučujeme použít "týdně" nebo "měsíčně" pro lepší výkon dotazů a snadné analýzy trendů. | N | Délka |

**Vrácená pole**

| **Název pole** | **Popis** |
| -------------- | --------------- |
| Identifikátor | Primární klíč označuje jedinečné ID instance zálohy. |
| Id | ID Azure Resource Manager (ARM) instance zálohy |
| FriendlyName | Popisný název instance zálohování |
| ProtectionInfo | Informace o nastavení ochrany instance zálohování Například nakonfigurovaná ochrana, zastavená ochrana, počáteční zálohování čeká na vyřízení. |
| LatestRecoveryPoint | Datum a čas posledního bodu obnovení přidruženého k instanci zálohování |
| OldestRecoveryPoint | Datum a čas nejstaršího bodu obnovení přidruženého k instanci zálohování |
| SourceSizeInMBs | Velikost front-endu instance zálohy v MB |
| VaultStore_StorageConsumptionInMBs | Celkové cloudové úložiště spotřebované instancí zálohování v trezoru – úroveň Standard |
| DataSourceFriendlyName | Popisný název zdroje dat odpovídající instanci zálohy |
| BackupSolution | Řešení zálohování, ke kterému je instance zálohy přidružená. Například zálohování virtuálních počítačů Azure, SQL v zálohování virtuálních počítačů Azure atd. |
| DatasourceType | Typ zdroje dat odpovídající instanci zálohy Například Microsoft. COMPUTE/virtualMachines. |
| DatasourceResourceId | Azure Resource Manager (ARM) ID podkladového zdroje dat odpovídající instanci zálohy. Například Azure Resource Manager (ARM) ID virtuálního počítače |
| DatasourceSetFriendlyName | Popisný název nadřazeného prostředku zdroje dat (kdykoli je to možné). Například pro SQL ve zdroji dat virtuálního počítače Azure bude toto pole obsahovat název virtuálního počítače, ve kterém SQL Database existuje. |
| DatasourceSetResourceId | ID Azure Resource Manager (ARM) nadřazeného prostředku zdroje dat (kdykoli je to možné). Například pro SQL ve zdroji dat virtuálního počítače Azure bude toto pole obsahovat ID Azure Resource Manager (ARM) virtuálního počítače, ve kterém SQL Database existuje. |
| DatasourceSetType | Typ nadřazeného prostředku zdroje dat (kdykoli je to možné). Například pro SAP HANA ve zdroji dat virtuálního počítače Azure bude toto pole Microsoft. COMPUTE/virtualMachines, protože nadřazeným prostředkem je virtuální počítač Azure. |
| PolicyName | Název zásady přidružené k instanci zálohování |
| PolicyUniqueId | Cizí klíč, který odkazuje na zásadu přidruženou k instanci zálohování  |
| PolicyId | ID Azure Resource Manager (ARM) zásad přidružených k instanci zálohy |
| VaultResourceId | ID Azure Resource Manager (ARM) úložiště přidruženého k instanci zálohování |
| VaultUniqueId | Cizí klíč, který odkazuje na trezor přidružený k instanci zálohování |
| VaultName | Název trezoru přidruženého k instanci zálohování |
| VaultTags | Značky trezoru přidruženého k instanci zálohování |
| VaultSubscriptionId | ID předplatného trezoru přidruženého k instanci zálohování |
| VaultLocation | Umístění trezoru přidruženého k instanci zálohování |
| VaultStore_StorageReplicationType | Typ replikace úložiště pro trezor přidružený k instanci zálohování |
| VaultType | Typ trezoru, který je Microsoft. RecoveryServices/trezory |
| TimeGenerated | Časové razítko záznamu |

#### <a name="_azurebackup_getbillinggroupstrends"></a>_AzureBackup_GetBillingGroupsTrends ()

Tato funkce vrací historické záznamy pro každou entitu fakturace, což vám umožní zobrazit klíčové denní, týdenní a měsíční trendy související s velikostí front-endu a spotřebou úložiště na několika úrovních členitosti.

**Parametry**

| **Název parametru** | **Popis** | **Požadováno?** | **Příklad hodnoty** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Pomocí tohoto parametru spolu s parametrem RangeEnd můžete načíst všechny záznamy související s fakturační skupinou za časové období z RangeStart do RangeEnd. | Y | "2021-03-03 00:00:00" |
| RangeEnd     | Pomocí tohoto parametru spolu s parametrem RangeStart načtěte všechny záznamy související s fakturační skupinou za časové období z RangeStart do RangeEnd. | Y |"2021-03-10 00:00:00"|
| VaultSubscriptionList  | Tento parametr použijte k filtrování výstupu funkce pro určitou sadu předplatných, kde existují zálohovaná data. Zadáním čárkami odděleného seznamu ID předplatných můžete načíst jenom ty fakturační skupiny, které jsou v zadaných předplatných. Ve výchozím nastavení je hodnota tohoto parametru "*", což způsobí, že funkce vyhledá záznamy ve všech předplatných. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Tento parametr použijte k filtrování výstupu funkce pro určitou sadu oblastí, kde data záloh existují. Zadání čárkami odděleného seznamu oblastí jako parametru pro tuto funkci vám pomůže načíst pouze ty fakturační skupiny, které jsou v určených oblastech. Ve výchozím nastavení je hodnota tohoto parametru "*", což způsobí, že funkce vyhledá záznamy ve všech oblastech. | N | "eastus, westus"|
| VaultList    |Pomocí tohoto parametru můžete filtrovat výstup funkce pro určitou sadu trezorů. Zadání čárkami oddělených názvů trezorů jako parametru této funkce vám pomůže načíst záznamy instancí zálohy, které se vztahují pouze na zadané trezory. Ve výchozím nastavení je hodnota tohoto parametru "*", což způsobí, že funkce vyhledá záznamy fakturačních skupin ve všech trezorech. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Pomocí tohoto parametru můžete filtrovat výstup funkce na záznamy týkající se konkrétního typu trezoru. V současné době je jediným podporovaným typem trezoru "Microsoft. RecoveryServices/trezory", což je výchozí hodnota tohoto parametru. | N | Microsoft. RecoveryServices/trezory|
| ExcludeLegacyEvent  | Pomocí tohoto parametru můžete zvolit, jestli se má dotazovat na data v dřívější tabulce AzureDiagnostics nebo ne. Pokud hodnota tohoto parametru je false, funkce se dotazuje na data z tabulky AzureDiagnostics i z tabulek specifických pro prostředky. Pokud je hodnota tohoto parametru true, funkce dotazuje data pouze z tabulek specifických pro prostředky. Výchozí hodnota je true (pravda). | N | true |
| BackupSolutionList | Pomocí tohoto parametru můžete filtrovat výstup funkce pro určitou sadu zálohovacích řešení používaných ve vašem prostředí Azure. Pokud například zadáte "zálohování virtuálních počítačů Azure, SQL v zálohování virtuálního počítače Azure, DPM" jako hodnotu tohoto parametru, funkce vrátí jenom záznamy, které souvisejí s položkami zálohovanými pomocí zálohování virtuálních počítačů Azure, SQL ve službě Azure VM Backup nebo DPM do Azure Backup. Ve výchozím nastavení je hodnota tohoto parametru "*", což znamená, že funkce vrací záznamy související se všemi zálohovacími řešeními, které jsou podporovány v sestavách zálohování (podporované hodnoty jsou "zálohování virtuálních počítačů Azure", "SQL ve službě zálohování virtuálních počítačů Azure", "SAP HANA v zálohování virtuálních počítačů Azure", "Azure Storage (soubory Azure) Backup", "Azure Backup Agent", "DPM", "Azure Backup Server" nebo čárkami oddělená kombinace kterékoli z těchto hodnot). | N | "Zálohování virtuálních počítačů Azure, SQL v zálohování virtuálních počítačů Azure, DPM, Azure Backup Agent" |
| BillingGroupName | Pomocí tohoto parametru můžete vyhledat konkrétní fakturační skupinu podle názvu. Ve výchozím nastavení je tato hodnota "*", která umožňuje hledat ve všech fakturačních skupinách. | N | TestVM |
| AggregationType | Pomocí tohoto parametru můžete zadat časové rozlišení, při kterém se mají data načíst. Pokud je hodnota tohoto parametru "Day", funkce vrátí záznam na fakturační skupinu za den, což vám umožní analyzovat každodenní trendy využití úložiště a velikost front-endu. Pokud je hodnota tohoto parametru "týdně", funkce vrátí záznam za instanci zálohy za týden, což vám umožní analyzovat týdenní trendy. Podobně můžete zadat "měsíční" pro analýzu měsíčních trendů. Výchozí hodnota je "denně". Pokud zobrazujete data v rámci větších časových rozsahů, doporučujeme použít "týdně" nebo "měsíčně" pro lepší výkon dotazů a snadné analýzy trendů. | N | Délka |

**Vrácená pole**

| **Název pole** | **Popis** |
| -------------- | --------------- |
| Identifikátor | Primární klíč označuje jedinečné ID fakturační skupiny. |
| FriendlyName | Popisný název skupiny fakturace |
| Name | Název fakturační skupiny |
| Typ | Typ fakturační skupiny Například ProtectedContainer nebo BackupItem |
| SourceSizeInMBs | Velikost front-endu ve fakturační skupině v MB |
| VaultStore_StorageConsumptionInMBs | Celková velikost cloudového úložiště spotřebovaného fakturační skupinou v trezoru – úroveň Standard |
| BackupSolution | Řešení zálohování, ke kterému je přidružená fakturační skupina Například zálohování virtuálních počítačů Azure, SQL v zálohování virtuálních počítačů Azure atd. |
| VaultResourceId | ID Azure Resource Manager (ARM) úložiště přidruženého ke skupině fakturace |
| VaultUniqueId | Cizí klíč, který odkazuje na trezor přidružený ke skupině fakturace |
| VaultName | Název trezoru, který je přidružený ke skupině fakturace |
| VaultTags | Značky trezoru přidružené ke skupině fakturace |
| VaultSubscriptionId | ID předplatného trezoru přidruženého ke skupině fakturace |
| VaultLocation | Umístění trezoru přidruženého ke skupině fakturace |
| VaultStore_StorageReplicationType | Typ replikace úložiště pro trezor přidružený ke skupině fakturace |
| VaultType | Typ trezoru, který je Microsoft. RecoveryServices/trezory |
| TimeGenerated | Časové razítko záznamu |
| ExtendedProperties | Další vlastnosti fakturační skupiny |

## <a name="sample-queries"></a>Ukázkové dotazy

Níže najdete několik ukázkových dotazů, které vám pomůžou začít používat systémové funkce.

- Všechny neúspěšné úlohy zálohování virtuálních počítačů Azure v zadaném časovém rozsahu

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06") //call function with RangeStart and RangeEnd parameters set, and other parameters with default value
    | where BackupSolution=="Azure Virtual Machine Backup" and Status=="Failed"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- Všechny úlohy zálohování protokolu SQL v daném časovém rozsahu

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06","*","*","*","*",true,"*","*","*","*","*","*",false) //call function with RangeStart and RangeEnd parameters set, ExcludeLog parameter as false, and other parameters with default value
    | where BackupSolution=="SQL in Azure VM Backup" and Operation=="Log"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- Týdenní trend úložiště zálohování spotřebovaného pro virtuální počítač "TestVM"

    ````Kusto
    _AzureBackup_GetBackupInstancesTrends("2021-01-01", "2021-03-06","*","*","*","*",false,"*","*","*","*",true, "Weekly") //call function with RangeStart and RangeEnd parameters set, AggregationType parameter as Weekly, and other parameters with default value
    | where BackupSolution == "Azure Virtual Machine Backup"
    | where FriendlyName == "testvm"
    | project TimeGenerated, VaultStore_StorageConsumptionInMBs
    | render timechart 
    ````

## <a name="next-steps"></a>Další kroky
[Další informace o sestavách zálohování](https://docs.microsoft.com/azure/backup/configure-reports)

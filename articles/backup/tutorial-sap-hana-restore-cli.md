---
title: Kurz – obnovení služby SAP HANA DB v Azure pomocí rozhraní příkazového řádku
description: V tomto kurzu se dozvíte, jak obnovit SAP HANA databáze běžící na virtuálním počítači Azure z trezoru služby Azure Backup Recovery Services pomocí Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0e524bfe090f0d67b76c13e876f44e83986aeb9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91334799"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Kurz: obnovení databází SAP HANA ve virtuálním počítači Azure pomocí Azure CLI

Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo prostřednictvím skriptů. Tato dokumentace podrobně popisuje, jak obnovit zálohovanou SAP HANA databázi na virtuálním počítači Azure pomocí Azure CLI. Tyto kroky můžete provést také pomocí [Azure Portal](./sap-hana-db-restore.md).

Pomocí [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) spustit příkazy rozhraní příkazového řádku.

Na konci tohoto kurzu budete mít tyto možnosti:

> [!div class="checklist"]
>
> * Zobrazit body obnovení pro zálohovanou databázi
> * Obnovení databáze

V tomto kurzu se předpokládá, že máte databázi SAP HANA běžící na virtuálním počítači Azure, který je zálohovaný pomocí Azure Backup. Pokud jste použili [zálohu databáze SAP HANA v Azure pomocí](tutorial-sap-hana-backup-cli.md) rozhraní příkazového řádku k zálohování databáze SAP HANA, budete používat následující prostředky:

* Skupina prostředků s názvem *saphanaResourceGroup*
* Trezor s názvem *saphanaVault*
* chráněný kontejner s názvem *VMAppContainer; COMPUTE; saphanaResourceGroup; saphanaVM*
* Zálohovaná databáze/položka s názvem *saphanadatabase; hxe; hxe*
* prostředky v oblasti *westus2*

## <a name="view-restore-points-for-a-backed-up-database"></a>Zobrazit body obnovení pro zálohovanou databázi

Chcete-li zobrazit seznam všech bodů obnovení pro databázi, použijte rutinu [AZ Backup RecoveryPoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) následujícím způsobem:

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

Seznam bodů obnovení bude vypadat takto:

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

Jak vidíte, výše uvedený seznam obsahuje tři body obnovení: jednu pro úplnou, rozdílovou a zálohu protokolu.

>[!NOTE]
>Počáteční a koncový bod každého nepoškozeného řetězce zálohy protokolu můžete zobrazit také pomocí rutiny [AZ Backup RecoveryPoint show-log-řetěz](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) .

## <a name="prerequisites-to-restore-a-database"></a>Předpoklady pro obnovení databáze

Před obnovením databáze se ujistěte, že jsou splněné následující předpoklady:

* Databázi můžete obnovit pouze do instance SAP HANA, která je ve stejné oblasti.
* Cílová instance musí být zaregistrovaná ve stejném trezoru jako zdroj.
* Azure Backup nemůže na jednom virtuálním počítači identifikovat dvě různé instance SAP HANA. Proto není možné obnovit data z jedné instance do druhé na stejném virtuálním počítači.

## <a name="restore-a-database"></a>Obnovení databáze

Azure Backup může obnovit databáze SAP HANA, které běží na virtuálních počítačích Azure, takto:

* Obnoví konkrétní datum nebo čas (za sekundu) pomocí záloh protokolu. Azure Backup automaticky určuje odpovídající úplné a rozdílové zálohy a řetěz záloh protokolů, které jsou nutné k obnovení na základě vybraného času.
* Obnovení do konkrétního úplného nebo rozdílového zálohování pro obnovení do konkrétního bodu obnovení.

Chcete-li obnovit databázi, použijte rutinu [AZ Restore-azurewl](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) , která vyžaduje objekt konfigurace obnovení jako jeden ze vstupů. Tento objekt se dá vygenerovat pomocí rutiny [AZ Backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az-backup-recoveryconfig-show) . Objekt konfigurace obnovení obsahuje všechny podrobnosti k provedení obnovení. Jedním z nich je režim obnovení – **OriginalWorkloadRestore** nebo **AlternateWorkloadRestore**.

>[!NOTE]
> **OriginalWorkloadRestore** – obnovte data do stejné instance SAP HANA jako původní zdroj. Tato možnost přepíše původní databázi. <br>
> **AlternateWorkloadRestore** – obnovte databázi do alternativního umístění a zachovejte původní zdrojovou databázi.

## <a name="restore-to-alternate-location"></a>Obnovit do alternativního umístění

Chcete-li obnovit databázi do alternativního umístění, použijte jako režim obnovení **AlternateWorkloadRestore** . Pak je nutné zvolit bod obnovení, který může být buď předchozí bod v čase, nebo libovolným z předchozích bodů obnovení.

V tomto kurzu obnovíte předchozí bod obnovení. [Zobrazte seznam bodů obnovení](#view-restore-points-for-a-backed-up-database) pro databázi a vyberte bod, do kterého chcete obnovit. V tomto kurzu se použije bod obnovení s názvem *7660777527047692711*.

Pomocí výše uvedeného názvu bodu obnovení a režimu obnovení vytvoříme objekt konfigurace obnovení pomocí rutiny [AZ Backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az-backup-recoveryconfig-show) . Pojďme se podívat, jak každý ze zbývajících parametrů v této rutině znamená:

* **--target-Item-Name** Jedná se o název, který obnovená databáze použije. V tomto případě jsme použili název *restored_database*.
* **--target-Server-Name** Jedná se o název serveru SAP HANA, který se úspěšně zaregistroval do trezoru Recovery Services a nachází se ve stejné oblasti jako databáze, která se má obnovit. Pro tento kurz obnovíme databázi na stejný SAP HANA Server, který jsme chránili, s názvem *hxehost*.
* **--target-Server-Type** Pro obnovení databází SAP HANA je třeba použít **HANAInstance** .

```azurecli-interactive

az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode AlternateWorkloadRestore \
    --rp-name 7660777527047692711 \
    --target-item-name restored_database \
    --target-server-name hxehost \
    --target-server-type HANAInstance \
    --workload-type SAPHANA \
    --output json
```

Odpověď na výše uvedený dotaz bude objekt konfigurace obnovení, který vypadá přibližně takto:

```output
{"restore_mode": "AlternateLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "7660777527047692711", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}
```

Nyní obnovte databázi spuštěním rutiny [AZ Restore-azurewl](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) . Pokud chcete tento příkaz použít, zadáte výše uvedený výstup JSON, který se uloží do souboru s názvem *recoveryconfig.js*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

Výstup bude vypadat nějak takto:

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

Odpověď vám poskytne název úlohy. Tento název úlohy můžete použít ke sledování stavu úlohy pomocí rutiny [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) .

## <a name="restore-and-overwrite"></a>Obnovit a přepsat

K obnovení do původního umístění použijeme **OrignialWorkloadRestore** jako režim obnovení. Pak je nutné zvolit bod obnovení, který může být buď předchozí bod v čase, nebo libovolným z předchozích bodů obnovení.

Pro tento kurz si pro obnovení zvolíme předchozí bod v čase "28-11-2019-09:53:00". Tento bod obnovení můžete zadat v následujících formátech: dd-mm-rrrr, dd-mm-rrrr-hh: mm: ss. Chcete-li zvolit platný bod v čase obnovení do, použijte rutinu [AZ Backup RecoveryPoint show-log-řetěz](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) , která uvádí intervaly nepřerušených záloh řetězů protokolů.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

Odpověď na výše uvedený dotaz bude objekt konfigurace obnovení, který vypadá takto:

```output
{"restore_mode": "OriginalLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "DefaultRangeRecoveryPoint", "log_point_in_time": "28-11-2019-09:53:00", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}"
```

Nyní obnovte databázi spuštěním rutiny [AZ Restore-azurewl](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) . Pokud chcete tento příkaz použít, zadáte výše uvedený výstup JSON, který se uloží do souboru s názvem *recoveryconfig.js*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

Výstup bude vypadat nějak takto:

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

Odpověď vám poskytne název úlohy. Tento název úlohy můžete použít ke sledování stavu úlohy pomocí rutiny [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) .

## <a name="restore-as-files"></a>Obnovit jako soubory

K obnovení zálohovaných dat jako souborů místo databáze použijeme **RestoreAsFiles** jako režim obnovení. Pak zvolte bod obnovení, který může být buď předchozí bod v čase, nebo kterýkoli z předchozích bodů obnovení. Jakmile jsou soubory v zadané cestě, můžete tyto soubory převést na libovolný SAP HANA počítač, ve kterém je chcete obnovit jako databázi. Vzhledem k tomu, že můžete tyto soubory přesunout na libovolný počítač, teď můžete data obnovit v rámci předplatných a oblastí.

Pro tento kurz si vybereme předchozí bod obnovení, který se má `28-11-2019-09:53:00` obnovit, a umístění pro výpis záložních souborů jako `/home/saphana/restoreasfiles` na stejném serveru SAP HANA. Tento bod obnovení můžete zadat v jednom z následujících formátů: **dd-mm-rrrr** nebo **dd-mm-rrrr-hh: mm: SS**. Chcete-li zvolit platný bod v čase obnovení do, použijte rutinu [AZ Backup RecoveryPoint show-log-řetěz](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) , která uvádí intervaly nepřerušených záloh řetězů protokolů.

Pomocí rutiny [AZ Backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az-backup-recoveryconfig-show) vytvořte objekt konfigurace obnovení a použijte přitom výše uvedený název bodu obnovení a režim obnovení. Pojďme se podívat, jak každý ze zbývajících parametrů v této rutině znamená:

* **--target-kontejner-Name** Jedná se o název serveru SAP HANA, který se úspěšně zaregistroval do trezoru Recovery Services a nachází se ve stejné oblasti jako databáze, která se má obnovit. Pro tento kurz obnovíme databázi jako soubory na stejný SAP HANA Server, který jsme chránili, s názvem *hxehost*.
* **--RP – název** Pro obnovení bodu v čase bude název bodu obnovení **DefaultRangeRecoveryPoint** .

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode RestoreAsFiles \
    --log-point-in-time 28-11-2019-09:53:00 \
    --rp-name DefaultRangeRecoveryPoint \
    --target-container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --filepath /home/saphana/restoreasfiles \
    --output json
```

Odpověď na výše uvedený dotaz bude objekt konfigurace obnovení, který vypadá takto:

```output
{
  "alternate_directory_paths": null,
  "container_id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.RecoveryServices/vaults/SAPHANAVault/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SAPHANA;hanamachine",
  "container_uri": "VMAppContainer;compute;saphana;hanamachine",
  "database_name": null,
  "filepath": "/home/",
  "item_type": "SAPHana",
  "item_uri": "SAPHanaDatabase;hxe;hxe",
  "log_point_in_time": "04-07-2020-09:53:00",
  "recovery_mode": "FileRecovery",
  "recovery_point_id": "DefaultRangeRecoveryPoint",
  "restore_mode": "AlternateLocation",
  "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/hanamachine"
}
```

Nyní k obnovení databáze jako souborů spusťte rutinu [AZ Restore-azurewl](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) . Pokud chcete tento příkaz použít, zadáte výstup JSON, který je uvedený výše, který se uloží do souboru s názvem *recoveryconfig.js*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output json
```

Výstup bude vypadat nějak takto:

```output
{
  "eTag": null,
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/SAPHANARESOURCEGROUP/providers/Microsoft.RecoveryServices/vaults/SAPHANAVault/backupJobs/608e737e-c001-47ca-8c37-57d909c8a704",
  "location": null,
  "name": "608e737e-c001-47ca-8c37-57d909c8a704",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "7ddd3c3a-c0eb-11ea-a5f8-54ee75ec272a",
    "backupManagementType": "AzureWorkload",
    "duration": "0:00:01.781847",
    "endTime": null,
    "entityFriendlyName": "HXE [hxehost]",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {
        "Job Type": "Restore as files"
      },
      "tasksList": [
        {
          "status": "InProgress",
          "taskId": "Transfer data from vault"
        }
      ]
    },
    "jobType": "AzureWorkloadJob",
    "operation": "Restore",
    "startTime": "2020-07-08T07:20:29.336434+00:00",
    "status": "InProgress",
    "workloadType": "SAPHanaDatabase"
  },
  "resourceGroup": "saphanaResourceGroup",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

Odpověď vám poskytne název úlohy. Tento název úlohy můžete použít ke sledování stavu úlohy pomocí rutiny [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) .

Soubory, které jsou dumpingové do cílového kontejneru, jsou:

* Soubory zálohy databáze
* Soubory katalogu
* Soubory metadat JSON (pro každý záložní soubor, který je součástí)

Cesta ke sdílené složce v síti, nebo cesta připojené sdílené složky Azure, pokud je zadaná jako cílová cesta, umožňuje snazší přístup k těmto souborům jiným počítačům ve stejné síti nebo se stejnou sdílenou složkou Azure, která je v nich namontovaná.

>[!NOTE]
>Pokud chcete obnovit záložní soubory databáze ve sdílené složce Azure připojené k cílovému zaregistrovanému virtuálnímu počítači, ujistěte se, že má kořenový účet oprávnění ke čtení a zápisu ve sdílené složce Azure.

V závislosti na typu vybraného bodu obnovení (**bodu v čase** nebo **úplném & rozdílu**) uvidíte v cílové cestě jednu nebo více složek. Jedna z složek s názvem `Data_<date and time of restore>` obsahuje úplné a rozdílové zálohy a další složka s názvem `Log` obsahuje zálohy protokolu.

Přesuňte obnovené soubory na Server SAP HANA, kde je chcete obnovit jako databázi. Pak postupujte podle těchto kroků a obnovte databázi:

1. Nastavte oprávnění pro složku nebo adresář, ve kterém se záložní soubory ukládají, pomocí následujícího příkazu:

    ```bash
    chown -R <SID>adm:sapsys <directory>
    ```

1. Spusťte další sadu příkazů jako `<SID>adm`

    ```bash
    su - <sid>adm
    ```

1. Vygenerujte soubor katalogu pro obnovení. Extrahujte **BackupId** ze souboru metadat JSON pro úplnou zálohu, která bude později použita v rámci operace obnovení. Zajistěte, aby úplné zálohy a zápisy protokolů byly v různých složkách a odstranily soubory katalogu a soubory metadat JSON v těchto složkách.

    ```bash
    hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
    ```

    V příkazu výše:

    * `<DataFileDir>` – Složka obsahující úplné zálohy
    * `<LogFilesDir>` – Složka, která obsahuje zálohy protokolu
    * `<PathToPlaceCatalogFile>` – Složka, ve které se musí umístit vygenerovaný katalogový soubor

1. Obnovte pomocí nově vygenerovaného souboru katalogu přes HANA Studio nebo spusťte dotaz HDBSQL Restore s tímto nově vygenerovaným katalogem. Dotazy na HDBSQL jsou uvedeny níže:

    * Obnovení k určitému bodu v čase:

        Pokud vytváříte novou obnovenou databázi, spusťte příkaz HDBSQL a vytvořte novou databázi `<DatabaseName>` a potom zastavte databázi pro obnovení. Pokud však obnovujete pouze existující databázi, zastavte databázi spuštěním příkazu HDBSQL.

        Pak spusťte následující příkaz pro obnovení databáze:

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>` – Název nové databáze nebo existující databáze, kterou chcete obnovit.
        * `<Timestamp>` – Přesné časové razítko obnovení bodu v čase
        * `<DatabaseName@HostName>` – Název databáze, jejíž zálohování se používá k obnovení, a název **hostitelského** /SAP HANA serveru, na kterém je umístěna Tato databáze. `USING SOURCE <DatabaseName@HostName>`Možnost určuje, že zálohování dat (používané pro obnovení) je databáze s jiným identifikátorem SID nebo názvem, než je cílový SAP HANA počítač. Proto není nutné zadávat pro obnovení na stejném serveru HANA, ze kterého se provádí zálohování.
        * `<PathToGeneratedCatalogInStep3>` -Cesta k souboru katalogu vygenerovanému v **kroku 3**
        * `<DataFileDir>` – Složka obsahující úplné zálohy
        * `<LogFilesDir>` – Složka, která obsahuje zálohy protokolu
        * `<BackupIdFromJsonFile>` – **BackupId** extrahovaný v **kroku 3**

    * Postup obnovení do konkrétního úplného nebo rozdílového zálohování:

        Pokud vytváříte novou obnovenou databázi, spusťte příkaz HDBSQL a vytvořte novou databázi `<DatabaseName>` a potom zastavte databázi pro obnovení. Pokud však obnovujete pouze existující databázi, zastavte databázi spuštěním příkazu HDBSQL:

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>` – název nové databáze nebo existující databáze, kterou chcete obnovit.
        * `<Timestamp>` – přesné časové razítko obnovení bodu v čase
        * `<DatabaseName@HostName>` – název databáze, jejíž zálohování se používá k obnovení, a název **hostitelského** /SAP HANA serveru, na kterém se nachází tato databáze. `USING SOURCE <DatabaseName@HostName>`Možnost určuje, že zálohování dat (používané pro obnovení) je databáze s jiným identifikátorem SID nebo názvem, než je cílový SAP HANA počítač. Proto není nutné zadávat pro obnovení na stejném serveru HANA, ze kterého se provádí zálohování.
        * `<PathToGeneratedCatalogInStep3>` – Cesta k souboru katalogu vygenerovanému v **kroku 3**
        * `<DataFileDir>` – Složka obsahující úplné zálohy
        * `<LogFilesDir>` – Složka, která obsahuje zálohy protokolu
        * `<BackupIdFromJsonFile>` – **BackupId** extrahovaný v **kroku 3**

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak spravovat SAP HANA databází zálohovaných pomocí Azure CLI, najdete v kurzu [Správa databáze SAP HANA na virtuálním počítači Azure pomocí](tutorial-sap-hana-backup-cli.md) rozhraní PŘÍKAZového řádku.

* Informace o tom, jak obnovit databázi SAP HANA běžící na virtuálním počítači Azure pomocí Azure Portal, najdete [v tématu obnovení databází SAP HANA na virtuálních počítačích Azure](./sap-hana-db-restore.md) .

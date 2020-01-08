---
title: Kurz – obnovení služby SAP HANA DB v Azure pomocí rozhraní příkazového řádku
description: V tomto kurzu se dozvíte, jak obnovit SAP HANA databáze běžící na virtuálním počítači Azure z trezoru služby Azure Backup Recovery Services pomocí Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 6dbe0c4382b648506d853feb281c70a8e8401595
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472069"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Kurz: obnovení databází SAP HANA ve virtuálním počítači Azure pomocí Azure CLI

Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo prostřednictvím skriptů. Tato dokumentace podrobně popisuje, jak obnovit zálohovanou SAP HANA databázi na virtuálním počítači Azure pomocí Azure CLI. Tyto kroky můžete provést také pomocí [Azure Portal](https://docs.microsoft.com/azure/backup/sap-hana-db-restore).

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

Chcete-li zobrazit seznam všech bodů obnovení pro databázi, použijte rutinu [AZ Backup RecoveryPoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) následujícím způsobem:

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
>Počáteční a koncový bod každého nepoškozeného řetězce zálohy protokolu můžete zobrazit také pomocí rutiny [AZ Backup RecoveryPoint show-log-řetěz](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) .

## <a name="prerequisites-to-restore-a-database"></a>Předpoklady pro obnovení databáze

Před obnovením databáze se ujistěte, že jsou splněné následující předpoklady:

* Databázi můžete obnovit pouze do instance SAP HANA, která je ve stejné oblasti.
* Cílová instance musí být zaregistrovaná ve stejném trezoru jako zdroj.
* Azure Backup nemůže na jednom virtuálním počítači identifikovat dvě různé instance SAP HANA. Proto není možné obnovit data z jedné instance do druhé na stejném virtuálním počítači.

## <a name="restore-a-database"></a>Obnovení databáze

Azure Backup může obnovit databáze SAP HANA, které běží na virtuálních počítačích Azure, takto:

* Obnoví konkrétní datum nebo čas (za sekundu) pomocí záloh protokolu. Azure Backup automaticky určuje odpovídající úplné a rozdílové zálohy a řetěz záloh protokolů, které jsou nutné k obnovení na základě vybraného času.
* Obnovení do konkrétního úplného nebo rozdílového zálohování pro obnovení do konkrétního bodu obnovení.

Chcete-li obnovit databázi, použijte rutinu [AZ Restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) , která vyžaduje objekt konfigurace obnovení jako jeden ze vstupů. Tento objekt se dá vygenerovat pomocí rutiny [AZ Backup recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) . Objekt konfigurace obnovení obsahuje všechny podrobnosti k provedení obnovení. Jedním z nich je režim obnovení – **OriginalWorkloadRestore** nebo **AlternateWorkloadRestore**.

>[!NOTE]
> **OriginalWorkloadRestore** – obnovte data do stejné instance SAP HANA jako původní zdroj. Tato možnost přepíše původní databázi. <br>
> **AlternateWorkloadRestore** – obnovte databázi do alternativního umístění a zachovejte původní zdrojovou databázi.

## <a name="restore-to-alternate-location"></a>Obnovit do alternativního umístění

Chcete-li obnovit databázi do alternativního umístění, použijte jako režim obnovení **AlternateWorkloadRestore** . Pak je nutné zvolit bod obnovení, který může být buď předchozí bod v čase, nebo libovolným z předchozích bodů obnovení.

V tomto kurzu obnovíte předchozí bod obnovení. [Zobrazte seznam bodů obnovení](#view-restore-points-for-a-backed-up-database) pro databázi a vyberte bod, do kterého chcete obnovit. V tomto kurzu se použije bod obnovení s názvem *7660777527047692711*.

Pomocí výše uvedeného názvu bodu obnovení a režimu obnovení vytvoříme objekt konfigurace obnovení pomocí rutiny [AZ Backup recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) . Pojďme se podívat, jak každý ze zbývajících parametrů v této rutině znamená:

* **--target-Item-Name** Jedná se o název, který obnovená databáze použije. V tomto případě jsme použili název *restored_database*.
* **--target-Server-Name** Jedná se o název serveru SAP HANA, který se úspěšně zaregistroval v trezoru služby Recovery Services a nachází se ve stejné oblasti jako databáze, která se má obnovit. Pro tento kurz obnovíme databázi na stejný SAP HANA Server, který jsme chránili, s názvem *hxehost*.
* **--target-Server-Type** Pro obnovení databází SAP HANA je třeba použít **SapHanaDatabase** .

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
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

Nyní obnovte databázi spuštěním rutiny [AZ Restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) . Chcete-li použít tento příkaz, zadáte výše uvedený výstup JSON, který bude uložen do souboru s názvem *recoveryconfig. JSON*.

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

Odpověď vám poskytne název úlohy. Tento název úlohy můžete použít ke sledování stavu úlohy pomocí rutiny [AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="restore-and-overwrite"></a>Obnovit a přepsat

K obnovení do původního umístění použijeme **OrignialWorkloadRestore** jako režim obnovení. Pak je nutné zvolit bod obnovení, který může být buď předchozí bod v čase, nebo libovolným z předchozích bodů obnovení.

Pro tento kurz si pro obnovení zvolíme předchozí bod v čase "28-11-2019-09:53:00". Tento bod obnovení můžete zadat v následujících formátech: dd-mm-rrrr, dd-mm-rrrr-hh: mm: ss. Chcete-li zvolit platný bod v čase obnovení do, použijte rutinu [AZ Backup RecoveryPoint show-log-řetěz](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) , která uvádí intervaly nepřerušených záloh řetězů protokolů.

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
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

Nyní obnovte databázi spuštěním rutiny [AZ Restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) . Chcete-li použít tento příkaz, zadáte výše uvedený výstup JSON, který bude uložen do souboru s názvem *recoveryconfig. JSON*.

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

Odpověď vám poskytne název úlohy. Tento název úlohy můžete použít ke sledování stavu úlohy pomocí rutiny [AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak spravovat SAP HANA databází zálohovaných pomocí Azure CLI, najdete v kurzu [Správa databáze SAP HANA na virtuálním počítači Azure pomocí](tutorial-sap-hana-backup-cli.md) rozhraní PŘÍKAZového řádku.

* Informace o tom, jak obnovit databázi SAP HANA běžící na virtuálním počítači Azure pomocí Azure Portal, najdete [v tématu obnovení databází SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore) .

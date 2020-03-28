---
title: Kurz – obnovení DATABÁZE SAP HANA v Azure pomocí příkazového příkazového příkazu
description: V tomto kurzu se dozvíte, jak obnovit databáze SAP HANA spuštěné na virtuálním počítači Azure z trezoru služby Azure Backup Recovery Services pomocí rozhraní příkazového řádku Azure.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 6dbe0c4382b648506d853feb281c70a8e8401595
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75472069"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Kurz: Obnovení databází SAP HANA v virtuálním počítači Azure pomocí azure cli

Azure CLI se používá k vytváření a správě prostředků Azure z příkazového řádku nebo prostřednictvím skriptů. Tato dokumentace podrobně popisuje, jak obnovit zálohovanou databázi SAP HANA na virtuálním počítači Azure – pomocí azure CLI. Tyto kroky můžete provést také pomocí [portálu Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore).

Ke spuštění příkazů příkazového příkazu cli použijte [Azure Cloud Shell.](tutorial-sap-hana-backup-cli.md)

Na konci tohoto kurzu budete moci:

> [!div class="checklist"]
>
> * Zobrazení bodů obnovení zálohované databáze
> * Obnovení databáze

Tento kurz předpokládá, že máte databázi SAP HANA spuštěnou na virtuálním počítači Azure, která je zálohována pomocí azure backup. Pokud jste k zálohování databáze SAP HANA v Azure použili [zálohování](tutorial-sap-hana-backup-cli.md) databáze SAP HANA, použijete následující prostředky:

* skupina prostředků s názvem *saphanaResourceGroup*
* trezor s názvem *saphanaVault*
* chráněný kontejner s názvem *VMAppContainer; Výpočetní prostředky;saphanaResourceGroup;saphanaVM*
* zálohovaná databáze/položka s názvem *saphanadatabase;hxe;hxe*
* zdroje v regionu *westus2*

## <a name="view-restore-points-for-a-backed-up-database"></a>Zobrazení bodů obnovení zálohované databáze

Chcete-li zobrazit seznam všech bodů obnovení pro databázi, použijte rutinu [seznamu az zálohování recoverypoint](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) následujícím způsobem:

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

Jak můžete vidět, výše uvedený seznam obsahuje tři body obnovení: jeden pro úplné, rozdílové a log zálohování.

>[!NOTE]
>Můžete také zobrazit počáteční a koncový bod každého nepřerušeného řetězce zálohování protokolu pomocí rutiny [az zálohování recoverypoint show-log-chain.](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain)

## <a name="prerequisites-to-restore-a-database"></a>Předpoklady pro obnovení databáze

Před obnovením databáze se ujistěte, že jsou splněny následující požadavky:

* Databázi můžete obnovit pouze do instance SAP HANA, která je ve stejné oblasti.
* Cílová instance musí být registrována ve stejném trezoru jako zdroj.
* Azure Backup nemůže identifikovat dvě různé instance SAP HANA na stejném virtuálním počítači. Proto obnovení dat z jedné instance do druhé na stejném virtuálním počítači není možné.

## <a name="restore-a-database"></a>Obnovení databáze

Azure Backup můžete obnovit databáze SAP HANA, které běží na virtuálních počítačích Azure takto:

* Obnovení na určité datum nebo čas (na druhý) pomocí záloh protokolu. Azure Backup automaticky určuje příslušné úplné, rozdílové zálohy a řetězec záloh protokolu, které jsou nutné k obnovení na základě vybraného času.
* Obnovení na konkrétní úplné nebo rozdílové zálohy obnovit do určitého bodu obnovení.

Chcete-li obnovit databázi, použijte rutinu [az obnovení azurewl,](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) která vyžaduje jako jeden ze vstupů objekt konfigurace obnovení. Tento objekt lze vygenerovat pomocí rutiny [az backup recoveryconfig show.](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) Recovery config objekt obsahuje všechny podrobnosti k provedení obnovení. Jedním z nich je režim obnovení – **OriginalWorkloadRestore** nebo **AlternateWorkloadRestore**.

>[!NOTE]
> **OriginalWorkloadRestore** - Obnovení dat do stejné instance SAP HANA jako původní zdroj. Tato možnost přepíše původní databázi. <br>
> **AlternateWorkloadRestore** - Obnovení databáze do alternativního umístění a zachovat původní zdrojové databáze.

## <a name="restore-to-alternate-location"></a>Obnovit do alternativního umístění

Chcete-li obnovit databázi do alternativního umístění, použijte jako režim obnovení funkci **AlternateWorkloadRestore.** Potom je nutné zvolit bod obnovení, který může být buď předchozí bod v čase nebo některý z předchozích bodů obnovení.

V tomto kurzu obnovíte předchozí bod obnovení. [Zobrazte seznam bodů obnovení](#view-restore-points-for-a-backed-up-database) pro databázi a zvolte bod, do kterého chcete obnovit. Tento kurz bude používat bod obnovení s názvem *7660777527047692711*.

Pomocí výše uvedeného názvu bodu obnovení a režimu obnovení vytvoříme objekt konfigurace obnovení pomocí rutiny [az backup recoveryconfig show.](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) Podívejme se na to, co každý ze zbývajících parametrů v této rutině znamená:

* **--target-item-name** Toto je název, který bude používat obnovená databáze. V tomto případě jsme použili název *restored_database*.
* **--target-server-name** Toto je název serveru SAP HANA, který je úspěšně zaregistrován do trezoru služeb obnovení a leží ve stejné oblasti jako databáze, která má být obnovena. V tomto kurzu obnovíme databázi na stejný server SAP HANA, který jsme chránili, s názvem *hxehost*.
* **--target-server-type** Pro obnovení databází SAP HANA **saphana databáze** musí být použit.

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

Odpověď na výše uvedený dotaz bude konfigurační objekt obnovení, který vypadá podobně:

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

Nyní obnovit databázi spusťte [rutinu az obnovit azurewl.](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) Chcete-li použít tento příkaz, zadáme výše uvedený výstup json, který je uložen do souboru s názvem *recoveryconfig.json*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

Výstup bude vypadat takto:

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

Odpověď vám dá název úlohy. Tento název úlohy lze použít ke sledování stavu úlohy pomocí rutiny [az zálohování práce](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) show.

## <a name="restore-and-overwrite"></a>Obnovení a přepsání

Chcete-li obnovit do původního umístění, budeme používat **OrignialWorkloadRestore** jako režim obnovení. Potom je nutné zvolit bod obnovení, který může být buď předchozí bod v čase nebo některý z předchozích bodů obnovení.

Pro účely tohoto kurzu vybereme předchozí bod v čase "28-11-2019-09:53:00", který chcete obnovit. Tento bod obnovení můžete zadat v následujících formátech: dd-mm-yyyy, dd-mm-yyyy-hh:mm:ss. Chcete-li zvolit platný bod v čase obnovit, použijte [az záloha recoverypoint show-log-chain](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) rutina, která uvádí intervaly nepřerušené zálohy řetězu protokolu.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

Odpověď na výše uvedený dotaz bude konfigurační objekt obnovení, který vypadá takto:

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

Nyní obnovit databázi spusťte [rutinu az obnovit azurewl.](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) Chcete-li použít tento příkaz, zadáme výše uvedený výstup json, který je uložen do souboru s názvem *recoveryconfig.json*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

Výstup bude vypadat takto:

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

Odpověď vám dá název úlohy. Tento název úlohy lze použít ke sledování stavu úlohy pomocí rutiny [az zálohování úlohy.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="next-steps"></a>Další kroky

* Chcete-li se dozvědět, jak spravovat databáze SAP HANA, které jsou zálohovány pomocí Azure CLI, pokračujte v kurzu [Spravovat databázi SAP HANA v virtuálním počítači Azure pomocí příkazového příkazu k příkazu](tutorial-sap-hana-backup-cli.md)

* Informace o tom, jak obnovit databázi SAP HANA spuštěnou ve virtuálním počítači Azure pomocí portálu Azure, najdete v části [Obnovení databází SAP HANA na virtuálních počítačích Azure.](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)

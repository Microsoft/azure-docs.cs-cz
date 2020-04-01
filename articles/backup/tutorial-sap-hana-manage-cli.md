---
title: 'Kurz: Správa zálohovaného systému SAP HANA DB pomocí cli'
description: V tomto kurzu se dozvíte, jak spravovat zálohované databáze SAP HANA spuštěné na virtuálním počítači Azure pomocí azure cli.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 2c088c27a678a4541cbba3c4c43c9cd830c60ff0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238738"
---
# <a name="tutorial-manage-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Kurz: Správa databází SAP HANA v virtuálním počítači Azure pomocí azure cli

Azure CLI se používá k vytváření a správě prostředků Azure z příkazového řádku nebo prostřednictvím skriptů. Tato dokumentace podrobně popisuje, jak spravovat zálohovanou databázi SAP HANA na virtuálním počítači Azure – to vše pomocí azure CLI. Tyto kroky můžete provést také pomocí [portálu Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-manage).

Ke spuštění příkazů příkazového příkazu cli použijte [Azure Cloud Shell.](tutorial-sap-hana-backup-cli.md)

Na konci tohoto kurzu budete moci:

> [!div class="checklist"]
>
> * Sledování úloh zálohování a obnovení
> * Ochrana nových databází přidaných do instance SAP HANA
> * Změna zásady
> * Zastavení ochrany
> * Obnovit ochranu

Pokud jste k zálohování databáze SAP HANA v Azure použili [zálohování](tutorial-sap-hana-backup-cli.md) databáze SAP HANA, použijete následující prostředky:

* skupina prostředků s názvem *saphanaResourceGroup*
* trezor s názvem *saphanaVault*
* chráněný kontejner s názvem *VMAppContainer; Výpočetní prostředky;saphanaResourceGroup;saphanaVM*
* zálohovaná databáze/položka s názvem *saphanadatabase;hxe;hxe*
* zdroje v regionu *westus2*

Azure CLI usnadňuje správu databáze SAP HANA spuštěné na virtuálním počítači Azure, který je zálohovaný pomocí Azure Backup. Tento kurz podrobně popisuje každý operace správy.

## <a name="monitor-backup-and-restore-jobs"></a>Sledování úloh zálohování a obnovení

Chcete-li sledovat dokončené nebo aktuálně spuštěné úlohy (zálohování nebo obnovení), použijte rutinu [seznamu úloh zálohování az.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) ClI také umožňuje [pozastavit aktuálně spuštěnou úlohu](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) nebo [počkat, až bude úloha dokončena](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait).

```azurecli-interactive
az backup job list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --output table
```

Výstup bude vypadat zhruba takto:

```output
Name                                  Operation              Status      Item Name       Start Time UTC
------------------------------------  ---------------        ---------   ----------      -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup        Completed   hxe             2019-12-03T03:09:210831+00:00  
ccdb4dce-8b15-47c5-8c46-b0985352238f  Backup (Full)          Completed   hxe [hxehost]   2019-12-01T10:30:58.867489+00:00
4980af91-1090-49a6-ab96-13bc905a5282  Backup (Differential)  Completed   hxe [hxehost]   2019-12-01T10:36:00.563909+00:00
F7c68818-039f-4a0f-8d73-e0747e68a813  Restore (Log)          Completed   hxe [hxehost]   2019-12-03T05:44:51.081607+00:00
```

## <a name="change-policy"></a>Změnit zásady

Chcete-li změnit zásady, které jsou základem konfigurace zálohování SAP HANA, použijte rutinu [sady zásad zálohování az.](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-set) Parametr name v této rutině odkazuje na položku zálohování, jejíž zásady chceme změnit. V tomto kurzu nahradíme zásady naší databáze SAP HANA *saphanadatabase;hxe;hxe* novou zásadou *newsaphanaPolicy*. Nové zásady lze vytvořit pomocí [rutiny vytvoření zásad zálohování az.](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create)

```azurecli-interactive
az backup item set policy --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name newsaphanaPolicy \
    --name saphanadatabase;hxe;hxe \
```

Výstup by měl vypadat nějak takto:

```output
Name                                  Resource Group
------------------------------------- --------------
cb110094-9b15-4c55-ad45-6899200eb8dd  SAPHANA
```

## <a name="protect-new-databases-added-to-an-sap-hana-instance"></a>Ochrana nových databází přidaných do instance SAP HANA

[Registrace instance SAP HANA s trezorem služeb obnovení](tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance) automaticky zjišťuje všechny databáze v této instanci.

Však v případech, kdy jsou nové databáze přidány do instance SAP HANA [později, použijte az záloha chránitelné položky inicializovat](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-initialize) rutinu. Tato rutina zjišťuje nové přidané databáze.

```azurecli-interactive
az backup protectable-item initialize --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --workload-type SAPHANA
```

Potom pomocí rutiny [seznamu az zálohování chránitelných položek](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-list) seznam seznam všech databází, které byly zjištěny na vaší instanci SAP HANA. Tento seznam však vylučuje databáze, na kterých již byla nakonfigurována záloha. Po zjištění zálohované databáze se podívejte [na povolit zálohování v databázi SAP HANA](tutorial-sap-hana-backup-cli.md#enable-backup-on-sap-hana-database).

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Nová databáze, kterou chcete zálohovat, se zobrazí v tomto seznamu, který bude vypadat takto:

```output
Name                            Protectable Item Type    ParentName    ServerName    IsProtected
---------------------------     ----------------------   ------------  -----------   ------------
saphanasystem;hxe               SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb    SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;newhxe      SAPHanaDatabase          HXE           hxehost       NotProtected
```

## <a name="stop-protection-for-an-sap-hana-database"></a>Zastavení ochrany databáze SAP HANA

Ochranu databáze SAP HANA můžete ukončit několika způsoby:

* Zastavte všechny budoucí úlohy zálohování a odstraňte všechny body obnovení.
* Zastavte všechny budoucí úlohy zálohování a ponechte body obnovení beze změny.

Pokud se rozhodnete ponechat body obnovení, mějte na paměti tyto podrobnosti:

* Všechny body obnovení zůstanou neporušené navždy, veškeré prořezávání se zastaví při zastavení ochrany s uchováním dat.
* Bude se vám účtovat chráněná instance a spotřebované úložiště.
* Pokud odstraníte zdroj dat bez zastavení zálohování, nové zálohy se nezdaří.

Podívejme se na každý ze způsobů, jak ochranu zastavit podrobněji.

### <a name="stop-protection-with-retain-data"></a>Zastavení ochrany se zachováním dat

Chcete-li ochranu uchovávat, použijte rutinu [az backup protection.](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable)

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --output table
```

Výstup by měl vypadat nějak takto:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Chcete-li zkontrolovat stav této operace, použijte rutinu [az zálohování úlohy.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

### <a name="stop-protection-without-retain-data"></a>Zastavení ochrany bez uchování dat

Chcete-li ochranu zastavit bez uchování dat, použijte rutinu [az backup protection.](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable)

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --delete-backup-data true \
    --output table
```

Výstup by měl vypadat nějak takto:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Chcete-li zkontrolovat stav této operace, použijte rutinu [az zálohování úlohy.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="resume-protection"></a>Obnovit ochranu

Když zastavíte ochranu databáze SAP HANA s uchováním dat, můžete později obnovit ochranu. Pokud zálohovaná data neuchováte, nebudete moci ochranu obnovit.

Chcete-li obnovit ochranu, použijte rutinu [obnovení zálohování az.](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume)

```azurecli-interactive
az backup protection resume --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name saphanaPolicy \
    --output table
```

Výstup by měl vypadat nějak takto:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
b2a7f108-1020-4529-870f-6c4c43e2bb9e  saphanaResourceGroup
```

Chcete-li zkontrolovat stav této operace, použijte rutinu [az zálohování úlohy.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak zálohovat databázi SAP HANA spuštěnou na virtuálním počítači Azure pomocí portálu Azure, najdete v části [Zálohování databází SAP HANA na virtuálních počítačích Azure.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)

* Informace o tom, jak spravovat zálohovanou databázi SAP HANA spuštěnou na virtuálním počítači Azure pomocí portálu Azure, najdete v části [Správa zálohovaných databází SAP HANA na virtuálním počítači Azure.](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)

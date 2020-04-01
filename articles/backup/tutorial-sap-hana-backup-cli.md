---
title: Kurz – zálohování SAP HANA DB v Azure pomocí rozhraní příkazového řádku
description: V tomto kurzu se dozvíte, jak zálohovat databáze SAP HANA spuštěné na virtuálním počítači Azure do trezoru služby Azure Backup Recovery Services pomocí rozhraní příkazového řádku Azure.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: cdc8a8fb09a086a2b9212c21d071f267991fa275
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78206618"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Kurz: Zálohování databází SAP HANA v virtuálním počítači Azure pomocí azure cli

Azure CLI se používá k vytváření a správě prostředků Azure z příkazového řádku nebo prostřednictvím skriptů. Tato dokumentace podrobně popisuje, jak zálohovat databázi SAP HANA a aktivovat zálohy na vyžádání – to vše pomocí rozhraní příkazového řádku Azure. Tyto kroky můžete provést také pomocí [portálu Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

Tento dokument předpokládá, že už máte databázi SAP HANA nainstalovanou na virtuálním počítači Azure. (Můžete také [vytvořit virtuální počítač pomocí Azure CLI).](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli) Na konci tohoto kurzu budete moci:

> [!div class="checklist"]
>
> * Vytvoření trezoru služby Recovery Services
> * Registrace instance SAP HANA a zjištění databází na ní
> * Povolení zálohování v databázi SAP HANA
> * Spuštění zálohy na vyžádání

Podívejte se na [scénáře, které v současné době podporujeme](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support) pro SAP HANA.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Chcete-li nainstalovat a používat příkaz cli místně, musíte spustit Azure CLI verze xx.xxx.x nebo novější. Verzi rozhraní příkazového řádku zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru služby Recovery Services

Trezor služby Recovery Services je logický kontejner, který ukládá záložní data pro každý chráněný prostředek, jako jsou virtuální počítače Azure nebo úlohy spuštěné na virtuálních počítačích Azure – jako jsou databáze SQL nebo HANA. Úloha zálohování pro chráněný prostředek při spuštění vytvoří uvnitř trezoru služby Recovery Services bod obnovení. Pomocí některého z těchto bodů obnovení pak můžete obnovit data k danému bodu v čase.

Vytvořte trezor služby Recovery Services pomocí příkazu [az backup vault create](https://docs.microsoft.com/cli/azure/backup/vault#az-backup-vault-create). Zadejte stejnou skupinu prostředků a umístění, jako má virtuální počítač, který chcete chránit. Naučte se, jak vytvořit virtuální počítač pomocí Azure CLI s tímto [rychlým startem virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

V tomto kurzu budeme používat následující:

* skupina prostředků s názvem *saphanaResourceGroup*
* Virtuální ho s názvem *saphanaVM*
* zdrojů v lokalitě *westus2.*

Vytvoříme trezor s názvem *saphanaVault*.

```azurecli-interactive
az backup vault create --resource-group saphanaResourceGroup \
    --name saphanaVault \
    --location westus2
```

Ve výchozím nastavení je trezor služby Recovery Services nastavený pro geograficky redundantní úložiště. Geograficky redundantní úložiště zajišťuje replikaci vašich zálohovaných dat do sekundární oblasti Azure, která je od primární oblasti vzdálená stovky kilometrů. Pokud je potřeba změnit nastavení redundance úložiště, použijte rutinu [nastavení vlastností zálohy az.](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set)

```azurecli
az backup vault backup-properties set \
    --name saphanaVault  \
    --resource-group saphanaResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

Chcete-li zjistit, zda byl váš trezor úspěšně vytvořen, použijte rutinu [seznamu az zálohování.](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-list) Zobrazí se následující odpověď:

```output
Location   Name             ResourceGroup
---------  ---------------  -------------  
westus2    saphanaVault     saphanaResourceGroup
```

## <a name="register-and-protect-the-sap-hana-instance"></a>Registrace a ochrana instance SAP HANA

Pro sap HANA instance (virtuální počítač s SAP HANA nainstalované na něm) zjistit služby Azure, musí být skript [předběžné registrace](https://aka.ms/scriptforpermsonhana) spuštěna na počítači SAP HANA. Ujistěte se, že jsou [splněny](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#prerequisites) všechny požadavky před spuštěním skriptu. Další informace o tom, co skript dělá, naleznete v části [Co dělá skript předběžné registrace.](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)

Po spuštění skriptu sap hana instance lze zaregistrovat s trezoru služeb obnovení jsme vytvořili dříve. Chcete-li instanci zaregistrovat, použijte rutinu [registru kontejneru zálohování az.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-register) *VMResourceId* je ID prostředku virtuálního virtuálního uživatele, který jste vytvořili k instalaci SAP HANA.

```azurecli-interactive
az backup container register --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --location westus2 \
    --workload-type SAPHANA \
    --backup-management-type AzureWorkload \
    --resource-id VMResourceId
```

>[!NOTE]
>Pokud virtuální hotel není ve stejné skupině prostředků jako trezor, pak *saphanaResourceGroup* odkazuje na skupinu prostředků, kde byl vytvořen trezor.

Registrace instance SAP HANA automaticky zjišťuje všechny své aktuální databáze. Chcete-li však zjistit všechny nové databáze, které mohou být přidány v budoucnu naleznete v zjišťování nových databází přidaných do části registrované instance [SAP HANA.](tutorial-sap-hana-manage-cli.md#protect-new-databases-added-to-an-sap-hana-instance)

Chcete-li zkontrolovat, zda je instance SAP HANA úspěšně zaregistrována v úložišti, použijte rutinu [seznamu kontejnerů zálohování az.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) Zobrazí se následující odpověď:

```output
Name                                                    Friendly Name    Resource Group        Type           Registration Status
------------------------------------------------------  --------------   --------------------  ---------      ----------------------
VMAppContainer;Compute;saphanaResourceGroup;saphanaVM   saphanaVM        saphanaResourceGroup  AzureWorkload  Registered
```

>[!NOTE]
> Sloupec "název" ve výše uvedeném výstupu odkazuje na název kontejneru. Tento název kontejneru bude použit v dalších částech k povolení zálohování a jejich aktivaci. Což v tomto případě je *VMAppContainer; Výpočetní;saphanaResourceGroup;saphanaVM*.

## <a name="enable-backup-on-sap-hana-database"></a>Povolení zálohování v databázi SAP HANA

Rutina [seznamu az zálohování,](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-list) která je chráněna, obsahuje všechny databáze zjištěné v instanci SAP HANA, které jste zaregistrovali v předchozím kroku.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Měli byste najít databázi, kterou chcete zálohovat v tomto seznamu, který bude vypadat takto:

```output
Name                           Protectable Item Type    ParentName    ServerName    IsProtected
-----------------------------  ----------------------   ------------  -----------   ------------
saphanasystem;hxe              SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb   SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;hxe        SAPHanaDatabase          HXE           hxehost       NotProtected
```

Jak můžete vidět z výše uvedeného výstupu, SID systému SAP HANA je HXE. V tomto kurzu nakonfigurujeme zálohování pro *databázi saphanadatabase;hxe;hxe,* která je umístěna na serveru *hxehost.*

K ochraně a konfiguraci zálohování v databázi, jeden po druhém, používáme [az zálohování ochrany povolit pro azurewl](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurewl) rutina. Zadejte název zásady, kterou chcete použít. Chcete-li vytvořit zásadu pomocí rozhraní příkazového řádku, použijte rutinu [vytvoření zásad zálohování az.](https://docs.microsoft.com//cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) V tomto kurzu budeme používat zásady *sapahanaPolicy.*

```azurecli-interactive
az backup protection enable-for-azurewl --resource-group saphanaResourceGroup \
    --policy-name saphanaPolicy \
    --protectable-item-name saphanadatabase;hxe;hxe  \
    --protectable-item-type SAPHANADatabse \
    --server-name hxehost \
    --workload-type SAPHANA \
    --output table
```

Můžete zkontrolovat, zda je výše uvedená konfigurace zálohování dokončena pomocí rutiny [seznamu úloh zálohování az.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) Výstup se zobrazí takto:

```output
Name                                  Operation         Status     Item Name   Start Time UTC
------------------------------------  ---------------   ---------  ----------  -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup   Completed  hxe         2019-12-03T03:09:210831+00:00  
```

Rutina [seznamu úloh zálohování az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) obsahuje seznam všech úloh zálohování (plánovaných nebo na vyžádání), které byly spuštěny nebo jsou aktuálně spuštěny v chráněné databázi, kromě dalších operací, jako je registr, konfigurace zálohování, odstranění záložních dat atd.

## <a name="trigger-an-on-demand-backup"></a>Spuštění zálohy na vyžádání

Zatímco výše uvedená část podrobně popisuje, jak nakonfigurovat naplánované zálohování, tato část hovoří o aktivaci zálohy na vyžádání. Chcete-li to provést, používáme [az zálohování zálohy-nyní](https://docs.microsoft.com/cli/azure/backup/protection#az-backup-protection-backup-now) rutina.

>[!NOTE]
> Zásady uchovávání informací zálohy na vyžádání je určena základní zásady uchovávání informací pro databázi.

```azurecli-interactive
az backup protection backup-now --resource-group saphanaResourceGroup \
    --item-name saphanadatabase;hxe;hxe \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --backup-type Full
    --retain-until 01-01-2040
    --output table
```

Výstup se zobrazí takto:

```output
Name                                  ResourceGroup
------------------------------------  -------------
e0f15dae-7cac-4475-a833-f52c50e5b6c3  saphanaResourceGroup
```

Odpověď vám dá název úlohy. Tento název úlohy lze použít ke sledování stavu úlohy pomocí rutiny [az zálohování úlohy.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

>[!NOTE]
>Kromě plánování úplné nebo rozdílové zálohy mohou být také aktuálně spuštěny ručně. Zálohy protokolů jsou automaticky spuštěny a spravovány sap hana interně.
>
> Přírůstkové zálohy nejsou aktuálně podporovány službou Azure Backup.

## <a name="next-steps"></a>Další kroky

* Chcete-li zjistit, jak obnovit databázi SAP HANA v virtuálním počítači Azure pomocí příkazového příkazu k příkazu příkazu k řidítku, pokračujte v kurzu – [obnovení databáze SAP HANA v virtuálním počítači Azure pomocí příkazového příkazu k příkazu](tutorial-sap-hana-restore-cli.md)

* Informace o tom, jak zálohovat databázi SAP HANA spuštěnou ve virtuálním počítači Azure pomocí portálu Azure, najdete v části [Zálohování databází SAP HANA na virtuálních počítačích Azure.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)

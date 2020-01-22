---
title: Zálohování sdílených složek Azure pomocí Azure CLI
description: Naučte se používat Azure CLI k zálohování sdílených složek Azure v trezoru Recovery Services.
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: 10ae6d88e8a40ba4175e664ed338f0a02be7a7ca
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294484"
---
# <a name="back-up-azure-file-shares-with-cli"></a>Zálohování sdílených složek Azure pomocí rozhraní příkazového řádku

Rozhraní příkazového řádku Azure (CLI) poskytuje prostředí příkazového řádku pro správu prostředků Azure. Je skvělým nástrojem pro vytváření vlastních automatizace pro používání prostředků Azure. Tento článek podrobně popisuje, jak zálohovat sdílené složky Azure pomocí Azure CLI. K provedení těchto kroků můžete také využít [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) nebo [Azure Portal](backup-afs.md).

Na konci tohoto kurzu se dozvíte, jak pomocí Azure CLI provádět následující operace:

* Vytvoření trezoru služby Recovery Services
* Povolit zálohování sdílených složek Azure
* Aktivace zálohování na vyžádání pro sdílené složky

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud chcete rozhraní příkazového řádku nainstalovat a používat místně, musíte použít Azure CLI verze 2.0.18 nebo novější. Chcete-li zjistit verzi rozhraní příkazového řádku, `run az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Recovery Services

Trezor služby Recovery Services je entita, která poskytuje konsolidovanou možnost zobrazení a správy napříč všemi zálohovanými položkami. Úloha zálohování pro chráněný prostředek při spuštění vytvoří uvnitř trezoru služby Recovery Services bod obnovení. Pomocí některého z těchto bodů obnovení pak můžete obnovit data k danému bodu v čase.

Pomocí těchto kroků vytvořte Trezor služby Recovery Services:

1. Trezor se umístí do skupiny prostředků. Pokud nemáte existující skupinu prostředků, vytvořte ji pomocí [AZ Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) . V tomto kurzu vytvoříme novou skupinu prostředků *azurefiles* v oblasti východní USA.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

2. Pomocí rutiny [AZ Backup trezor Create](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-create) vytvořte Trezor. Zadejte stejné umístění pro trezor, které bylo použito pro skupinu prostředků.

    Následující příklad vytvoří trezor služby Recovery Services s názvem *azurefilesvault* v oblasti východní USA.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

3. Zadejte typ redundance, který se použije pro úložiště trezoru. Můžete použít [místně redundantní úložiště](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs) nebo [geograficky redundantní úložiště](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

    Následující příklad nastaví možnost redundance úložiště pro *azurefilesvault* na geograficky **redundantní** pomocí rutiny [AZ Backup trezor-Properties set](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) .

    ```azurecli-interactive
    az backup vault backup-properties set --name azurefilesvault --resource-group azurefiles --backup-storage-redundancy Georedundant
    ```

    Pokud chcete zjistit, jestli se trezor úspěšně vytvořil, můžete k získání podrobností o vašem trezoru použít rutinu [AZ Backup trezor show](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-show) . Následující příklad zobrazí podrobnosti o *azurefilesvault* , které jsme vytvořili v předchozích krocích.

    ```azurecli-interactive
    az backup vault show --name azurefilesvault --resource-group azurefiles --output table
    ```

    Výstup bude podobný následující reakci:

    ```output
    Location     Name               ResourceGroup
    ----------   ---------------    ---------------
    eastus       azurefilesvault    azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Povolit zálohování sdílených složek Azure

V této části se předpokládá, že už máte sdílenou složku Azure, pro kterou chcete nakonfigurovat zálohování. Pokud ho nemáte, vytvořte sdílenou složku Azure pomocí příkazu [AZ Storage Share Create](https://docs.microsoft.com/cli/azure/storage/share?view=azure-cli-latest#az-storage-share-create) .

Pokud chcete povolit zálohování sdílených složek, je potřeba vytvořit zásadu ochrany, která definuje, kdy se úloha zálohování spustí a jak dlouho se mají ukládat body obnovení. Zásadu zálohování můžete vytvořit pomocí rutiny [AZ Backup Policy Create](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) .

V následujícím příkladu se používá rutina [AZ Backup Protection Enable-for-azurefileshare](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurefileshare) , která umožňuje zálohování sdílené složky *azurefiles* v účtu úložiště *afsaccount* pomocí zásad zálohování s *plánem 1* :

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

Atribut **Name** ve výstupu odpovídá názvu úlohy, kterou vytvořila služba zálohování pro operaci **Povolení zálohování** . Chcete-li sledovat stav úlohy, použijte rutinu [AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Aktivace zálohování na vyžádání pro sdílení souborů

Pokud chcete pro sdílenou složku aktivovat zálohování na vyžádání, místo abyste čekali, až zásady zálohování spustí úlohu v naplánovaném čase, použijte rutinu [AZ Backup Protection Backup-Now](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-backup-now) .

Chcete-li aktivovat zálohování na vyžádání, je nutné zadat následující parametry:

* **--Container-Name** je název účtu úložiště, který hostuje sdílenou složku. Pokud chcete načíst **název** nebo **popisný název** svého kontejneru, použijte příkaz [AZ Backup Container list](https://docs.microsoft.com/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--Item-Name** je název sdílené složky, pro kterou chcete aktivovat zálohování na vyžádání. Pokud chcete načíst **název** nebo **popisný název** zálohované položky, použijte příkaz [AZ Backup Item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .
* **--zached – dokud** neurčíte, do kdy se má bod obnovení zachovat, do něj bude zachováno datum. Hodnota by měla být nastavena ve formátu času UTC (dd-mm-rrrr).

Následující příklad aktivuje zálohování na vyžádání pro sdílenou složku *azuresfiles* v účtu úložiště *afsaccount* s uchováním do *20-01-2020*.

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

Atribut **Name** ve výstupu odpovídá názvu úlohy, kterou vytvořila služba zálohování pro operaci zálohování na vyžádání. Chcete-li sledovat stav úlohy, použijte rutinu [AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak [obnovit sdílené složky Azure pomocí](restore-afs-cli.md) rozhraní PŘÍKAZového řádku
* Naučte se [Spravovat Azure File Share ackups pomocí](manage-afs-backup-cli.md) rozhraní PŘÍKAZového řádku.

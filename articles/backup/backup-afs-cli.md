---
title: Zálohování sdílených složek Azure pomocí azure cli
description: Zjistěte, jak pomocí azure cli zálohovat sdílené složky Azure v trezoru služby Recovery Services
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ff1d8c6245521d2d0262b0440177d65713058742
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844037"
---
# <a name="back-up-azure-file-shares-with-cli"></a>Zálohování sdílených složek Azure pomocí příkazového příkazového příkazu

Rozhraní příkazového řádku Azure (CLI) poskytuje prostředí příkazového řádku pro správu prostředků Azure. Je to skvělý nástroj pro vytváření vlastní automatizace pro použití prostředků Azure. Tento článek podrobně popisuje, jak zálohovat sdílené složky Azure pomocí azure CLI. K provedení těchto kroků můžete také využít [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) nebo [Azure Portal](backup-afs.md).

Na konci tohoto kurzu se dozvíte, jak provádět operace pod azure CLI:

* Vytvoření trezoru služby Recovery Services
* Povolení zálohování pro sdílené složky Azure
* Aktivace zálohy na vyžádání pro sdílené složky souborů

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud chcete rozhraní příkazového řádku nainstalovat a používat místně, musíte použít Azure CLI verze 2.0.18 nebo novější. Chcete-li najít verzi `run az --version`cli, . Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru služby Recovery Services

Trezor služby obnovení je entita, která poskytuje konsolidované zobrazení a možnosti správy ve všech položkách zálohování. Úloha zálohování pro chráněný prostředek při spuštění vytvoří uvnitř trezoru služby Recovery Services bod obnovení. Pomocí některého z těchto bodů obnovení pak můžete obnovit data k danému bodu v čase.

Chcete-li vytvořit trezor služeb pro obnovení, postupujte takto:

1. Úschovna je umístěna ve skupině prostředků. Pokud nemáte existující skupinu prostředků, vytvořte novou skupinu s [vytvořením skupiny az](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) . V tomto kurzu vytvoříme nové soubory *azureskupiny* skupiny prostředků v oblasti USA – východ.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

2. K vytvoření trezoru použijte rutinu [vytvoření az zálohy.](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-create) Zadejte stejné umístění pro úschovnu, jaké bylo použito pro skupinu prostředků.

    Následující příklad vytvoří trezor služeb obnovení s názvem *azurefilesvault* v oblasti USA – východ.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

3. Zadejte typ redundance, který se má použít pro úložiště trezoru. Můžete použít [místně redundantní úložiště](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs) nebo [geograficky redundantní úložiště](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

    Následující příklad nastaví možnost redundance úložiště pro *azurefilesvault* na **Georedundant** pomocí [rutiny sady vlastností zálohy az](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) vault.

    ```azurecli-interactive
    az backup vault backup-properties set --name azurefilesvault --resource-group azurefiles --backup-storage-redundancy Georedundant
    ```

    Chcete-li zkontrolovat, zda je úschovna úspěšně vytvořena, můžete pomocí rutiny [az backup vault zobrazit](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-show) podrobnosti o úschovně. Následující příklad zobrazuje podrobnosti o *azurefilesvault* jsme vytvořili v krocích výše.

    ```azurecli-interactive
    az backup vault show --name azurefilesvault --resource-group azurefiles --output table
    ```

    Výstup bude podobný následující odpovědi:

    ```output
    Location     Name               ResourceGroup
    ----------   ---------------    ---------------
    eastus       azurefilesvault    azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Povolení zálohování pro sdílené složky Azure

Tato část předpokládá, že už máte sdílenou složku Azure, pro kterou chcete nakonfigurovat zálohování. Pokud ho nemáte, vytvořte sdílenou složku Azure pomocí příkazu [az storage share create](https://docs.microsoft.com/cli/azure/storage/share?view=azure-cli-latest#az-storage-share-create) .

Chcete-li povolit zálohování sdílených složek, je třeba vytvořit zásadu ochrany, která definuje, kdy je úloha zálohování spuštěna a jak dlouho jsou uloženy body obnovení. Zásadu zálohování můžete vytvořit pomocí rutiny [vytvoření zásad zálohování az.](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create)

Následující příklad používá rutinu [az zálohování enable-for-azurefileshare](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurefileshare) k povolení zálohování pro sdílenou složku *azurefiles* v účtu úložiště *afsaccount* pomocí zásad zálohování *plánu 1:*

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

Name **Name** Atribut ve výstupu odpovídá názvu úlohy, která je vytvořena službou zálohování pro operaci **povolit zálohování.** Chcete-li sledovat stav úlohy, použijte rutinu [az zálohování úlohy](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) show.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Aktivace zálohy na vyžádání pro sdílenou složku

Pokud chcete aktivovat zálohu na vyžádání pro sdílenou složku namísto čekání na spuštění zásady zálohování v naplánovaný čas, použijte rutinu [az zálohování pro zálohování nyní.](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-backup-now)

Chcete-li spustit zálohu na vyžádání, je třeba definovat následující parametry:

* **--název kontejneru** je název účtu úložiště, který je hostitelem sdílené složky. Chcete-li načíst **název** nebo **popisný název** kontejneru, použijte příkaz [az backup container list.](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--název položky** je název sdílené složky, pro kterou chcete aktivovat zálohu na vyžádání. Chcete-li načíst **název** nebo **popisný název** zálohované položky, použijte příkaz [az backup item.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)
* **--retain-until** určuje datum, do kdy chcete zachovat bod obnovení. Hodnota by měla být nastavena ve formátu času UTC (dd-mm-yyyy).

Následující příklad aktivuje zálohování na vyžádání pro *azuresfiles* fileshare v účtu úložiště *afsaccount* s uchováním do *20-01-2020*.

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

Name **Name** Atribut ve výstupu odpovídá názvu úlohy, která je vytvořena službou zálohování pro operaci zálohování na vyžádání. Chcete-li sledovat stav úlohy, použijte rutinu [az zálohování úlohy.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [obnovit sdílené složky Azure pomocí příkazového příkazu](restore-afs-cli.md)
* Přečtěte si, jak [spravovat sdílení souborů Azure pomocí příkazového příkazu](manage-afs-backup-cli.md)

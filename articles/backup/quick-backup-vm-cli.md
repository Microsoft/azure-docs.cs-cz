---
title: Rychlý start Azure – Zálohování virtuálního počítače pomocí Azure CLI
description: Zjistěte, jak zálohovat virtuální počítače pomocí Azure CLI.
services: backup
author: markgalioto
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 8/3/2018
ms.author: markgal
ms.custom: mvc
ms.openlocfilehash: e378c385d3ea98fd43937558d00d3d13accb636b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984955"
---
# <a name="back-up-a-virtual-machine-in-azure-with-the-cli"></a>Zálohování virtuálního počítače v Azure pomocí rozhraní příkazového řádku
Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. Svá data můžete chránit prováděním záloh v pravidelných intervalech. Azure Backup vytváří body obnovení, které je možné uchovávat v geograficky redundantních trezorech obnovení. Tento článek podrobně popisuje, jak zálohovat virtuální počítač v Azure pomocí Azure CLI. K provedení těchto kroků můžete také využít [Azure PowerShell](quick-backup-vm-powershell.md) nebo [Azure Portal](quick-backup-vm-portal.md).

V tomto rychlém startu se povolí zálohování na existujícím virtuálním počítači Azure. Pokud potřebujete vytvořit virtuální počítač, můžete [vytvořit virtuální počítač pomocí Azure CLI](../virtual-machines/linux/quick-create-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud chcete rozhraní příkazového řádku nainstalovat a používat místně, musíte použít Azure CLI verze 2.0.18 nebo novější. Verzi rozhraní příkazového řádku zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 


## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru služby Recovery Services
Trezor služby Recovery Services je logický kontejner, který uchovává zálohovaná data pro každý chráněný prostředek, například virtuální počítače Azure. Úloha zálohování pro chráněný prostředek při spuštění vytvoří uvnitř trezoru služby Recovery Services bod obnovení. Pomocí některého z těchto bodů obnovení pak můžete obnovit data k danému bodu v čase.

Vytvořte trezor služby Recovery Services pomocí příkazu [az backup vault create](https://docs.microsoft.com/cli/azure/backup/vault#az-backup-vault-create). Zadejte stejnou skupinu prostředků a umístění, jako má virtuální počítač, který chcete chránit. Pokud jste použili [rychlý úvod k virtuálním počítačům](../virtual-machines/linux/quick-create-cli.md), pak jste vytvořili:

- skupinu prostředků *myResourceGroup*,
- virtuální počítač *myVM*,
- prostředky v umístění *eastus*.

```azurecli-interactive 
az backup vault create --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
    --location eastus
```

Ve výchozím nastavení je trezor služby Recovery Services nastavený pro geograficky redundantní úložiště. Geograficky redundantní úložiště zajišťuje replikaci vašich zálohovaných dat do sekundární oblasti Azure, která je od primární oblasti vzdálená stovky kilometrů.


## <a name="enable-backup-for-an-azure-vm"></a>Povolení zálohování pro virtuální počítač Azure
Vytvořte zásadu ochrany, která bude definovat, kdy se spouští úloha zálohování a jak dlouho se uchovávají body obnovení. Výchozí zásada ochrany spouští úlohu zálohování každý den a uchovává body obnovení po dobu 30 dnů. Tyto výchozí hodnoty zásady můžete použít k rychlému zajištění ochrany vašeho virtuálního počítače. Pokud chcete pro virtuální počítač povolit ochranu zálohováním, použijte příkaz [az backup protection enable-for-vm](https://docs.microsoft.com/cli/azure/backup/protection#az-backup-protection-enable-for-vm). Zadejte skupinu prostředků a virtuální počítač, který chcete chránit, a pak zásadu, která se má použít:

```azurecli-interactive 
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

> [!NOTE]
Pokud se virtuální počítač nenachází ve stejné skupině prostředků jako trezor, potom skupina myResourceGroup odkazuje na skupinu prostředků, ve které byl trezor vytvořen. Místo názvu virtuálního počítače uveďte ID virtuálního počítače, jak je uvedeno níže.

```azurecli-interactive 
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm $(az vm show -g VMResourceGroup -n MyVm --query id) \
    --policy-name DefaultPolicy
```

## <a name="start-a-backup-job"></a>Spuštění úlohy zálohování
Pokud chcete spustit zálohování ihned a nečekat na spuštění úlohy výchozí zásadou v naplánovaném čase, použijte příkaz [az backup protection backup-now](https://docs.microsoft.com/cli/azure/backup/protection#az-backup-protection-backup-now). Tato první úloha zálohování vytvoří úplný bod obnovení. Každá úloha zálohování po tomto prvotním zálohování vytváří přírůstkové body obnovení. Přírůstkové body obnovení jsou efektivní z hlediska úložiště a času, protože přenášejí pouze změny provedené od posledního zálohování.

Pro zálohování virtuálního počítače se používají následující parametry:

- `--container-name` je název vašeho virtuálního počítače.
- `--item-name` je název vašeho virtuálního počítače.
- Hodnota `--retain-until` by měla být nastavená na poslední datum požadované dostupnosti bodu obnovení ve formátu času UTC (**dd-mm-yyyy**).

Následující příklad zálohuje virtuální počítač *myVM* a nastaví vypršení platnosti bodu obnovení na 18. října 2017:

```azurecli-interactive 
az backup protection backup-now \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --retain-until 18-10-2017
```


## <a name="monitor-the-backup-job"></a>Monitorování úlohy zálohování
Pokud chcete monitorovat stav úloh zálohování, použijte příkaz [az backup job list](https://docs.microsoft.com/cli/azure/backup/job#az-backup-job-list):

```azurecli-interactive 
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

Výstup bude vypadat podobně jako v následujícím příkladu, který ukazuje, že je úloha zálohování ve stavu *InProgress* (Probíhající):

```
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
a0a8e5e6  Backup           InProgress  myvm         2017-09-19T03:09:21  0:00:48.718366
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Když se *Status* (Stav) úlohy zálohovaní změní na *Completed* (Dokončeno), váš virtuální počítač je chráněný službou Recovery Services a má uložený úplný bod obnovení.


## <a name="clean-up-deployment"></a>Vyčištění nasazení
Pokud už ochrana virtuálního počítače není potřeba, můžete ji vypnout, odebrat body obnovení a trezor služby Recovery Services a pak odstranit skupinu prostředků a související prostředky virtuálního počítače. Pokud jste použili existující virtuální počítač, můžete vynechat poslední příkaz [az group delete](/cli/azure/group?view=azure-cli-latest#az-group-delete), aby se zachovala skupina prostředků a virtuální počítač.

Pokud chcete vyzkoušet kurz služby Backup, který vysvětluje postup obnovení dat virtuálního počítače, přejděte na [Další kroky](#next-steps). 

```azurecli-interactive 
az backup protection disable \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --delete-backup-data true
az backup vault delete \
    --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste vytvořili trezor služby Recovery Services, povolili ochranu virtuálního počítače a vytvořili prvotní bod obnovení. Pokud se chcete o službách Azure Backup a Recovery Services dozvědět více, pokračujte ke kurzům.

> [!div class="nextstepaction"]
> [Zálohování několika virtuálních počítačů Azure](./tutorial-backup-vm-at-scale.md)

---
title: Rychlý Start – zálohování virtuálního počítače pomocí Azure CLI
description: V tomto rychlém startu se dozvíte, jak vytvořit trezor Recovery Services, povolit ochranu na virtuálním počítači a vytvořit počáteční bod obnovení pomocí Azure CLI.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/31/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 1a1b11d517fdfea0aa3a0f553b63276bc20f90be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98805461"
---
# <a name="back-up-a-virtual-machine-in-azure-with-the-azure-cli"></a>Zálohování virtuálního počítače v Azure pomocí Azure CLI

Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. Svá data můžete chránit prováděním záloh v pravidelných intervalech. Azure Backup vytváří body obnovení, které je možné uchovávat v geograficky redundantních trezorech obnovení. Tento článek podrobně popisuje, jak zálohovat virtuální počítač v Azure pomocí Azure CLI. K provedení těchto kroků můžete také využít [Azure PowerShell](quick-backup-vm-powershell.md) nebo [Azure Portal](quick-backup-vm-portal.md).

V tomto rychlém startu se povolí zálohování na existujícím virtuálním počítači Azure. Pokud potřebujete vytvořit virtuální počítač, můžete [vytvořit virtuální počítač pomocí Azure CLI](../virtual-machines/linux/quick-create-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - V tomto rychlém startu se vyžaduje verze Azure CLI 2.0.18 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Trezor služby Recovery Services je logický kontejner, který uchovává zálohovaná data pro každý chráněný prostředek, například virtuální počítače Azure. Úloha zálohování pro chráněný prostředek při spuštění vytvoří uvnitř trezoru služby Recovery Services bod obnovení. Pomocí některého z těchto bodů obnovení pak můžete obnovit data k danému bodu v čase.

Vytvořte trezor služby Recovery Services pomocí příkazu [az backup vault create](/cli/azure/backup/vault#az-backup-vault-create). Zadejte stejnou skupinu prostředků a umístění, jako má virtuální počítač, který chcete chránit. Pokud jste použili [rychlý úvod k virtuálním počítačům](../virtual-machines/linux/quick-create-cli.md), pak jste vytvořili:

- skupinu prostředků *myResourceGroup*,
- virtuální počítač *myVM*,
- prostředky v umístění *eastus*.

```azurecli-interactive
az backup vault create --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
    --location eastus
```

Ve výchozím nastavení je trezor služby Recovery Services nastavený pro geograficky redundantní úložiště. Služba Geo-Redundant Storage zajišťuje replikaci zálohovaných dat do sekundární oblasti Azure, která je od primární oblasti vzdálena stovky mil. Pokud je potřeba upravit nastavení redundance úložiště, použijte příkaz [AZ Backup trezor Backup-Properties set](/cli/azure/backup/vault/backup-properties#az-backup-vault-backup-properties-set) rutina.

```azurecli
az backup vault backup-properties set \
    --name myRecoveryServicesVault  \
    --resource-group myResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

## <a name="enable-backup-for-an-azure-vm"></a>Povolení zálohování pro virtuální počítač Azure

Vytvořte zásadu ochrany, která bude definovat, kdy se spouští úloha zálohování a jak dlouho se uchovávají body obnovení. Výchozí zásada ochrany spouští úlohu zálohování každý den a uchovává body obnovení po dobu 30 dnů. Tyto výchozí hodnoty zásady můžete použít k rychlému zajištění ochrany vašeho virtuálního počítače. Pokud chcete pro virtuální počítač povolit ochranu zálohováním, použijte příkaz [az backup protection enable-for-vm](/cli/azure/backup/protection#az-backup-protection-enable-for-vm). Zadejte skupinu prostředků a virtuální počítač, který chcete chránit, a pak zásadu, která se má použít:

```azurecli-interactive
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

> [!NOTE]
> Pokud virtuální počítač není ve stejné skupině prostředků jako úložiště, pak myResourceGroup odkazuje na skupinu prostředků, ve které se vytvořil trezor. Místo názvu virtuálního počítače uveďte ID virtuálního počítače, jak je uvedeno níže.

```azurecli-interactive
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm $(az vm show -g VMResourceGroup -n MyVm --query id | tr -d '"') \
    --policy-name DefaultPolicy
```

> [!IMPORTANT]
> Při použití rozhraní příkazového řádku pro povolení zálohování několika virtuálních počítačů najednou zajistěte, aby k jedné zásadě nedošlo k více než 100 virtuálním počítačům. Toto je [doporučený postup](./backup-azure-vm-backup-faq.yml#is-there-a-limit-on-number-of-vms-that-can-be-associated-with-the-same-backup-policy). V současné době klient PowerShellu explicitně neblokuje, pokud existuje více než 100 virtuálních počítačů, ale tato kontrolní rutina se plánuje do budoucna přidat.

## <a name="start-a-backup-job"></a>Spuštění úlohy zálohování

Pokud chcete spustit zálohování ihned a nečekat na spuštění úlohy výchozí zásadou v naplánovaném čase, použijte příkaz [az backup protection backup-now](/cli/azure/backup/protection#az-backup-protection-backup-now). Tato první úloha zálohování vytvoří úplný bod obnovení. Každá úloha zálohování po tomto prvotním zálohování vytváří přírůstkové body obnovení. Přírůstkové body obnovení jsou efektivní z hlediska úložiště a času, protože přenášejí pouze změny provedené od posledního zálohování.

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

Pokud chcete monitorovat stav úloh zálohování, použijte příkaz [az backup job list](/cli/azure/backup/job#az-backup-job-list):

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

Výstup bude vypadat podobně jako v následujícím příkladu, který ukazuje, že je úloha zálohování ve stavu *InProgress* (Probíhající):

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
a0a8e5e6  Backup           InProgress  myvm         2017-09-19T03:09:21  0:00:48.718366
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Když se *Status* (Stav) úlohy zálohovaní změní na *Completed* (Dokončeno), váš virtuální počítač je chráněný službou Recovery Services a má uložený úplný bod obnovení.

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Pokud už ochrana virtuálního počítače není potřeba, můžete ji vypnout, odebrat body obnovení a trezor služby Recovery Services a pak odstranit skupinu prostředků a související prostředky virtuálního počítače. Pokud jste použili existující virtuální počítač, můžete vynechat poslední příkaz [az group delete](/cli/azure/group#az-group-delete), aby se zachovala skupina prostředků a virtuální počítač.

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

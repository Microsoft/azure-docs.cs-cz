---
title: Zakázání nebo odebrání agenta zřizování
description: Přečtěte si, jak zakázat nebo odebrat agenta zřizování na virtuálních počítačích a bitových kopiích systému Linux.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 0fea82c376a178de0be8ede6c0393e1de21de614
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98675800"
---
# <a name="disable-or-remove-the-linux-agent-from-vms-and-images"></a>Zakázání nebo odebrání agenta pro Linux z virtuálních počítačů a imagí

Před odebráním agenta pro Linux musíte porozumět tomu, co po odebrání agenta pro Linux nebude moct virtuální počítač dělat.

[Rozšíření](../extensions/overview.md) virtuálních počítačů Azure jsou malé aplikace, které poskytují konfiguraci po nasazení a úlohy automatizace na virtuálních počítačích Azure. rozšíření se instalují a spravují na základě plochy ovládacího prvku Azure. Je to úloha [agenta Azure Linux](../extensions/agent-linux.md) ke zpracování příkazů rozšíření platformy a zajištění správného stavu rozšíření v rámci virtuálního počítače.

Platforma Azure je hostitelem mnoha rozšíření, která jsou v rozsahu od konfigurace virtuálních počítačů, monitorování, zabezpečení a aplikace nástrojů. Existuje velký výběr první a rozšíření od jiných výrobců. Příklady klíčových scénářů, které se používají pro tyto přípony:
* Podpora služeb Azure First stran, jako jsou Azure Backup, monitorování, šifrování disků, zabezpečení, replikace webů a další.
* Nastavení SSH/hesla
* Konfigurace virtuálních počítačů – spouští se vlastní skripty, instalují se Puppet agenti atd.
* Produkty třetích stran, jako jsou třeba produkty AV, nástroje pro ohrožení zabezpečení virtuálních počítačů, nástroje pro monitorování virtuálních počítačů a aplikací.
* Rozšíření lze seskupit pomocí nového nasazení virtuálního počítače. Můžou být třeba součástí rozsáhlejšího nasazení, konfigurace aplikací v zřizování virtuálních počítačů nebo spouštění pro všechny podporované systémy ovládanými rozšířeními po nasazení.

## <a name="disabling-extension-processing"></a>Zákaz zpracování rozšíření

Existuje několik způsobů, jak zakázat zpracování rozšíření v závislosti na vašich potřebách, ale před pokračováním **musíte** odebrat všechna rozšíření nasazená na virtuální počítač, například pomocí Azure CLI, můžete [vypsat](/cli/azure/vm/extension#az-vm-extension-list) a [Odstranit](/cli/azure/vm/extension#az-vm-extension-delete):

```azurecli
az vm extension delete -g MyResourceGroup --vm-name MyVm -n extension_name
```
> [!Note]
> 
> Pokud to neuděláte výše, platforma se pokusí odeslat konfiguraci rozšíření a časový limit po 40min.

### <a name="disable-at-the-control-plane"></a>Zakázat na řídicí rovině
Pokud si nejste jistí, jestli budete potřebovat rozšíření v budoucnu, můžete nechat nainstalovaného agenta pro Linux na virtuálním počítači a pak zakázat možnost zpracování rozšíření z platformy. Tato možnost je k dispozici v `Microsoft.Compute` rozhraní API verze `2018-06-01` nebo vyšší a nemá závislost na nainstalované verzi agenta pro Linux.

```azurecli
az vm update -g <resourceGroup> -n <vmName> --set osProfile.allowExtensionOperations=false
```
Toto zpracování rozšíření můžete snadno znovu povolit z platformy pomocí výše uvedeného příkazu, ale nastavte na hodnotu true.

## <a name="remove-the-linux-agent-from-a-running-vm"></a>Odebrání agenta pro Linux z běžícího virtuálního počítače

Ujistěte se, že jste z virtuálního počítače **odebrali** všechna existující rozšíření, a to podle výše.

### <a name="step-1-remove-the-azure-linux-agent"></a>Krok 1: odebrání agenta Azure Linux

Pokud pouze odeberete agenta pro Linux a nikoli přidružené artefakty konfigurace, můžete ho později znovu nainstalovat. Pro odebrání agenta Azure Linux spusťte jednu z následujících možností jako kořenovou složku:

#### <a name="for-ubuntu-1804"></a>Pro Ubuntu >= 18,04
```bash
apt -y remove walinuxagent
```

#### <a name="for-redhat--77"></a>Pro RedHat >= 7,7
```bash
yum -y remove WALinuxAgent
```

#### <a name="for-suse"></a>Pro SUSE
```bash
zypper --non-interactive remove python-azure-agent
```

### <a name="step-2-optional-remove-the-azure-linux-agent-artifacts"></a>Krok 2: (volitelné) odeberte artefakty agenta Azure Linux.
> [!IMPORTANT] 
>
> Můžete odebrat všechny přidružené artefakty agenta pro Linux, ale to znamená, že ho nebude možné znovu nainstalovat později. Proto se důrazně doporučuje zvážit nejprve zakázání agenta pro Linux, odebrání agenta pro Linux pomocí výše uvedeného. 

Pokud víte, že nebudete znovu instalovat agenta pro Linux, můžete spustit následující:

#### <a name="for-ubuntu-1804"></a>Pro Ubuntu >= 18,04
```bash
apt -y purge walinuxagent
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-redhat--77"></a>Pro RedHat >= 7,7
```bash
yum -y remove WALinuxAgent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-suse"></a>Pro SUSE
```bash
zypper --non-interactive remove python-azure-agent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

## <a name="preparing-an-image-without-the-linux-agent"></a>Příprava bitové kopie bez agenta pro Linux
Máte-li bitovou kopii, která již obsahuje Cloud-init a chcete odebrat agenta pro Linux, ale přesto zřídit pomocí Cloud-init, spusťte kroky v kroku 2 (a volitelně krok 3) jako kořen k odebrání agenta Azure Linux a potom následujícím postupem odeberte konfiguraci Cloud-init a data z mezipaměti a připravte virtuální počítač na vytvoření vlastní image.

```bash
cloud-init clean --logs --seed 
```

## <a name="deprovision-and-create-an-image"></a>Zrušení zřízení a vytvoření image
Agent pro Linux má možnost vyčistit některá z existujících metadat imagí. krok "waagent-disvision + User", ale po jeho odebrání, budete muset provést následující akce, jako například níže, a z něj odebrat všechna další citlivá data.

- Odebrat všechny existující klíče hostitele SSH

   ```bash
   rm /etc/ssh/ssh_host_*key*
   ```
- Odstranit účet správce

   ```bash
   touch /var/run/utmp
   userdel -f -r <admin_user_account>
   ```
- Odstranění kořenového hesla

   ```bash
   passwd -d root
   ```

Po dokončení výše uvedeného můžete vytvořit vlastní image pomocí Azure CLI.


**Vytvoření běžné spravované image**
```azurecli
az vm deallocate -g <resource_group> -n <vm_name>
az vm generalize -g <resource_group> -n <vm_name>
az image create -g <resource_group> -n <image_name> --source <vm_name>
```

**Vytvoření verze image v galerii sdílených imagí**

```azurecli
az sig image-version create \
    -g $sigResourceGroup 
    --gallery-name $sigName 
    --gallery-image-definition $imageDefName 
    --gallery-image-version 1.0.0 
    --managed-image /subscriptions/00000000-0000-0000-0000-00000000xxxx/resourceGroups/imageGroups/providers/images/MyManagedImage
```
### <a name="creating-a-vm-from-an-image-that-does-not-contain-a-linux-agent"></a>Vytvoření virtuálního počítače z image, která neobsahuje agenta pro Linux
Když vytváříte virtuální počítač z image bez agenta pro Linux, musíte zajistit, aby konfigurace nasazení virtuálního počítače nepodporovala rozšíření na tomto virtuálním počítači.

> [!NOTE] 
> 
> Pokud to neuděláte výše, platforma se pokusí odeslat konfiguraci rozšíření a časový limit po 40min.

Pokud chcete nasadit virtuální počítač s zakázanými rozšířeními, můžete použít Azure CLI s [agentem--Enable-agent](/cli/azure/vm#az-vm-create).

```azurecli
az vm create \
    --resource-group $resourceGroup \
    --name $prodVmName \
    --image RedHat:RHEL:8.1-ci:latest \
    --admin-username azadmin \
    --ssh-key-value "$sshPubkeyPath" \
    --enable-agent false
```

Alternativně můžete to provést pomocí šablon Azure Resource Manager (ARM), a to nastavením `"provisionVMAgent": false,` .

```json
"osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "provisionVMAgent": false,
        "ssh": {
            "publicKeys": [
                {
                    "path": "[concat('/home/', parameters('adminUsername'), '/.ssh/authorized_keys')]",
                    "keyData": "[parameters('adminPublicKey')]"
```

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [zřizování Linux](provisioning.md).

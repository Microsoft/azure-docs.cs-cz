---
title: Vlastní data a Virtual Machines Azure
description: Podrobnosti o používání vlastních dat a Cloud-Init v Azure Virtual Machines
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: 2924caaac5fb8c512100d9e897f7f153af9a3b3e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87284910"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Vlastní data a Cloud-Init v Azure Virtual Machines

Do virtuálního počítače s Microsoft Azure může být potřeba vložit skript nebo jiná metadata v době zřizování.  V jiných cloudech se tento koncept často označuje jako data uživatelů.  V Microsoft Azure máme podobnou funkci nazvanou vlastní data. 

Vlastní data jsou k dispozici pouze pro virtuální počítač při prvním spuštění/počáteční instalaci, zavoláme toto "zřizování". Zřizování je proces, při kterém se k virtuálnímu počítači zpřístupní parametry pro vytvoření virtuálního počítače (například název hostitele, uživatelské jméno, heslo, certifikáty, vlastní data, klíče atd.) a Agent zřizování je zpracuje, jako je například [Agent pro Linux](./extensions/agent-linux.md) a [Cloud-init](./linux/using-cloud-init.md#troubleshooting-cloud-init). 


## <a name="passing-custom-data-to-the-vm"></a>Předávání vlastních dat virtuálnímu počítači
Chcete-li použít vlastní data, je nutné nejprve kódovat obsah Base64 předtím, než jej předáte do rozhraní API, pokud nepoužíváte nástroj CLI, který pro vás provádí převod, například AZ CLI. Velikost nesmí překročit 64 KB.

V rozhraní příkazového řádku můžete předat vlastní data jako soubor a bude převedeno na base64.
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

V Azure Resource Manager (ARM) existuje [funkce Base64](../azure-resource-manager/templates/template-functions-string.md#base64).

```json
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2019-07-01",
"location": "[parameters('location')]",
"dependsOn": [
..],
"variables": {
        "customDataBase64": "[base64(parameters('stringData'))]"
    },
"properties": {
..
    "osProfile": {
        "computerName": "[parameters('virtualMachineName')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "customData": "[variables('customDataBase64')]"
        },
```

## <a name="processing-custom-data"></a>Zpracování vlastních dat
Zřizovací agenti nainstalovanou na virtuálním počítači prochází s platformou a umísťují do systému souborů. 

### <a name="windows"></a>Windows
Vlastní data jsou umístěna do *%systemdrive%\AzureData\CustomData.bin* jako binární soubor, ale není zpracována. Pokud chcete tento soubor zpracovat, budete si muset vytvořit vlastní image a napsat kód pro zpracování CustomData. bin.

### <a name="linux"></a>Linux  
V operačním systému Linux jsou vlastní data do virtuálního počítače předána prostřednictvím souboru ovf-env.xml, který se během zřizování zkopíruje do adresáře */var/lib/waagent* .  Novější verze agenta Microsoft Azure Linux také zkopírují data zakódovaná ve formátu base64 do */var/lib/waagent/CustomData* i pro pohodlí.

Azure v současné době podporuje dva agenty zřizování:
* Agent pro Linux – ve výchozím nastavení agent nezpracovává vlastní data, budete muset vytvořit vlastní image s povoleným povolením. Relevantní nastavení podle [dokumentace](https://github.com/Azure/WALinuxAgent#configuration) :
    * Zřizování. DecodeCustomData
    * Provisioning.ExecuteCustomData

Když povolíte vlastní data a spustíte skript, dojde k zpoždění vytváření sestav virtuálních počítačů, které je připravené, nebo úspěšného zřizování, dokud se skript nedokončí. Pokud skript překročí celkovou dobu zřizování virtuálního počítače 40 minut, vytvoření virtuálního počítače se nezdaří. Poznámka: Pokud se skript nepodaří spustit nebo dojde k chybám během provádění, nepovažuje se za závažné selhání zřizování, budete muset vytvořit cestu oznámení, která vás upozorní na stav dokončení daného skriptu.

Pokud chcete řešit potíže s vlastním prováděním dat, přečtěte si */var/log/waagent.log* .

* Cloud-init – ve výchozím nastavení budou ve výchozím nastavení zpracovávat vlastní data. Cloud-init akceptuje [více formátů](https://cloudinit.readthedocs.io/en/latest/topics/format.html) vlastních dat, jako je konfigurace Cloud-init, skriptů atd. Podobně jako u agenta pro Linux, kdy Cloud-init zpracovává vlastní data. Pokud dojde k chybám při provádění zpracování konfigurace nebo skriptů, nepovažuje se za závažné selhání zřizování a budete muset vytvořit cestu oznámení, která vás upozorní na stav dokončení daného skriptu. U agenta pro Linux ale Cloud-init nečeká na dokončení konfigurace uživatelských dat uživatelů, než se dohlásí k platformě, kterou je virtuální počítač připravený. Další informace o cloud-init v Azure najdete v [dokumentaci](./linux/using-cloud-init.md).


Řešení potíží s vlastním prováděním dat najdete v [dokumentaci k](./linux/using-cloud-init.md#troubleshooting-cloud-init)řešení problémů.


## <a name="faq"></a>Časté otázky
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>Můžu aktualizovat vlastní data po vytvoření virtuálního počítače?
Pro jednotlivé virtuální počítače se vlastní data v modelu virtuálních počítačů nedají aktualizovat, ale pro VMSS můžete aktualizovat vlastní data VMSS prostřednictvím [REST API](/rest/api/compute/virtualmachinescalesets/update) (neplatí pro PS nebo AZ CLI Clients). Když aktualizujete vlastní data v modelu VMSS:
* Existující instance v VMSS nezískají aktualizované vlastní data, až do doby, než se obnoví z image.
* Existující instance v VMSS, které jsou upgradovány, nebudou mít aktualizované vlastní data.
* Nové instance získají nová vlastní data.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>Můžu na vlastní data umístit citlivé hodnoty?
**Nedoporučujeme** ukládat citlivá data do vlastních dat. Další informace najdete v tématu [osvědčené postupy zabezpečení a šifrování Azure](../security/fundamentals/data-encryption-best-practices.md).


### <a name="is-custom-data-made-available-in-imds"></a>Jsou vlastní data zpřístupněná v IMDS?
Ne, tato funkce není momentálně k dispozici.

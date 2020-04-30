---
title: Vlastní data a Virtual Machines Azure
description: Podrobnosti o používání vlastních dat a aplikace Cloud-init v Azure Virtual Machines
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: article
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: 9497e665d024b583c261ade3e6fb5393a9322ce0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759135"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Vlastní data a Cloud-init v Azure Virtual Machines

## <a name="what-is-custom-data"></a>Co jsou vlastní data?

Zákazníci často žádají o vložení skriptu nebo dalších metadat do virtuálního počítače s Microsoft Azure v době zřízení.  V jiných cloudech se tento koncept často označuje jako data uživatelů.  V Microsoft Azure máme podobnou funkci nazvanou vlastní data. 

Vlastní data jsou k dispozici pouze pro virtuální počítač při prvním spuštění/počáteční instalaci, zavoláme toto "zřizování". Zřizování je proces, při kterém se k virtuálnímu počítači zpřístupní parametry pro vytvoření virtuálního počítače (například název hostitele, uživatelské jméno, heslo, certifikáty, vlastní data, klíče atd.) a Agent zřizování je zpracuje, jako je například [Agent pro Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) a [Cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init). 


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

V Azure Resource Manager (ARM) existuje [funkce Base64](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-functions-string#base64).

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
V operačním systému Linux se vlastní data předávají do virtuálního počítače prostřednictvím souboru OVF-env. XML, který se během zřizování zkopíruje do adresáře */var/lib/waagent* .  Novější verze agenta Microsoft Azure Linux také zkopírují data zakódovaná ve formátu base64 do */var/lib/waagent/CustomData* i pro pohodlí.

Azure v současné době podporuje dva agenty zřizování:
* Agent pro Linux – ve výchozím nastavení agent nezpracovává vlastní data, budete muset vytvořit vlastní image s povoleným povolením. Relevantní nastavení podle [dokumentace](https://github.com/Azure/WALinuxAgent#configuration) :
    * Zřizování. DecodeCustomData
    * Zřizování. ExecuteCustomData

Když povolíte vlastní data a spustíte skript, dojde k zpoždění vytváření sestav virtuálních počítačů, které je připravené, nebo úspěšného zřizování, dokud se skript nedokončí. Pokud skript překročí celkovou dobu zřizování virtuálního počítače 40 minut, vytvoření virtuálního počítače se nezdaří. Poznámka: Pokud se skript nepodaří spustit nebo dojde k chybám během provádění, nepovažuje se za závažné selhání zřizování, budete muset vytvořit cestu oznámení, která vás upozorní na stav dokončení daného skriptu.

Pokud chcete řešit potíže s vlastním prováděním dat, přečtěte si */var/log/waagent.log* .

* Cloud-init – ve výchozím nastavení budou ve výchozím nastavení zpracovávat vlastní data. Cloud-init akceptuje [více formátů](https://cloudinit.readthedocs.io/en/latest/topics/format.html) vlastních dat, jako je konfigurace Cloud-init, skriptů atd. Podobně jako u agenta pro Linux, kdy Cloud-init zpracovává vlastní data. Pokud dojde k chybám při provádění zpracování konfigurace nebo skriptů, nepovažuje se za závažné selhání zřizování a budete muset vytvořit cestu oznámení, která vás upozorní na stav dokončení daného skriptu. U agenta pro Linux ale Cloud-init nečeká na dokončení konfigurace uživatelských dat uživatelů, než se dohlásí k platformě, kterou je virtuální počítač připravený. Další informace o cloud-init v Azure najdete v [dokumentaci](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).


Řešení potíží s vlastním prováděním dat najdete v [dokumentaci k](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init)řešení problémů.


## <a name="faq"></a>Nejčastější dotazy
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>Můžu aktualizovat vlastní data po vytvoření virtuálního počítače?
Pro jednotlivé virtuální počítače se vlastní data v modelu virtuálních počítačů nedají aktualizovat, ale pro VMSS můžete aktualizovat vlastní data VMSS prostřednictvím REST API (neplatí pro PS nebo AZ CLI Clients). Když aktualizujete vlastní data v modelu VMSS:
* Existující instance v VMSS nezískají aktualizované vlastní data, až do doby, než se obnoví z image.
* Existující instance v VMSS, které jsou upgradovány, nebudou mít aktualizované vlastní data.
* Nové instance získají nová vlastní data.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>Můžu na vlastní data umístit citlivé hodnoty?
**Nedoporučujeme** ukládat citlivá data do vlastních dat. Další informace najdete v tématu [osvědčené postupy zabezpečení a šifrování Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices).


### <a name="is-custom-data-made-available-in-imds"></a>Jsou vlastní data zpřístupněná v IMDS?
Ne, tato funkce není momentálně k dispozici.

---
title: Vlastní data a virtuální počítače Azure
description: Podrobnosti o používání vlastních dat a Cloud-Init na virtuálních počítačích Azure
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: article
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: aadac082e90a19d1a185dd7e6181a490adb70a10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109625"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Vlastní data a Cloud-Init na virtuálních počítačích Azure

## <a name="what-is-custom-data"></a>Co jsou vlastní data?

Zákazníci se často ptají, jak můžou v době zřizování vložit skript nebo jiná metadata do virtuálního počítače Microsoft Azure.  V jiných cloudech se tento koncept často označuje jako uživatelská data.  V Microsoft Azure máme podobnou funkci nazvanou vlastní data. 

Vlastní data se k dispozici pouze pro virtuální počítač během prvního spuštění nebo počáteční nastavení, nazýváme to "zřizování". Zřizování je proces, kde jsou parametry vytvoření virtuálního počítače (například název hostitele, uživatelské jméno, heslo, certifikáty, vlastní data, klíče atd.) zpřístupněny virtuálnímu počítači a zřizovací agent je zpracovává, jako je [Linux Agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) a [cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init). 


## <a name="passing-custom-data-to-the-vm"></a>Předávání vlastních dat virtuálnímu virtuálnímu soudu
Chcete-li použít vlastní data, musíte base64 nejprve zakódovat obsah před předáním do rozhraní API, pokud nepoužíváte nástroj rozhraní rozhraní se konbýt, který provede převod za vás, jako je například Rozhraní se křižovatelovým nastavením rozhraní AZ. Velikost nesmí překročit 64 KB.

V cli můžete předat vlastní data jako soubor a budou převedeny na base64.
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

Ve Správci prostředků Azure (ARM) je [funkce base64](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-functions-string#base64).

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
        "customDataBase64": "[variables('customData')]"
        },
```

## <a name="processing-custom-data"></a>Zpracování vlastních dat
Zřizovací agenti nainstalované na virtuálních počítačích zpracovávají propojení s platformou a umísťují ji do systému souborů. 

### <a name="windows"></a>Windows
Vlastní data jsou umístěna v *%SYSTEMDRIVE%\AzureData\CustomData.bin* jako binární soubor, ale nejsou zpracována. Pokud chcete tento soubor zpracovat, budete muset vytvořit vlastní bitovou kopii a napsat kód pro zpracování souboru CustomData.bin.

### <a name="linux"></a>Linux  
V operačních systémech Linux jsou vlastní data předávána virtuálnímu počítači prostřednictvím souboru ovf-env.xml, který se během zřizování zkopíruje do adresáře */var/lib/waagent.*  Novější verze Microsoft Azure Linux Agent také zkopírují base64 kódovaná data na */var/lib/waagent/CustomData* také pro pohodlí.

Azure aktuálně podporuje dva agenty zřizování:
* Agent Linuxu - Ve výchozím nastavení agent nebude zpracovávat vlastní data, budete muset vytvořit vlastní bitovou kopii s povolenou. Příslušná nastavení podle [dokumentace](https://github.com/Azure/WALinuxAgent#configuration) jsou:
    * Provisioning.DecodeCustomData
    * Provisioning.ExecuteCustomData

Když povolíte vlastní data a spustíte skript, zpozdí sestavu virtuálního virtuálního realitu, která je připravená, nebo že zřizování proběhlo úspěšně, dokud skript nedokončí. Pokud skript překročí celkový časový limit zřizování virtuálních montovny 40 minut, vytvoření virtuálního mísy se nezdaří. Všimněte si, že pokud se skript nepodaří spustit nebo chyby během provádění, není považován za závažné selhání zřizování, budete muset vytvořit cestu oznámení vás upozorní na stav dokončení skriptu.

Chcete-li vyřešit potíže s vlastním spuštěním dat, zkontrolujte */var/log/waagent.log*

* cloud-init - Ve výchozím nastavení bude ve výchozím nastavení zpracovávat vlastní data, cloud-init přijímá [více formátů](https://cloudinit.readthedocs.io/en/latest/topics/format.html) vlastních dat, jako je konfigurace cloud-init, skripty atd. Podobně jako U Linux Agent, když cloud-init zpracovává vlastní data. Pokud dojde k chybám během provádění zpracování konfigurace nebo skripty, není považovánza závažné selhání zřizování a budete muset vytvořit cestu oznámení vás upozorní na stav dokončení skriptu. Nicméně, odlišné od Linux Agent, cloud-init nečeká na vlastní konfigurace dat uživatele k dokončení před hlášením na platformě, která je připravena virtuální počítač. Další informace o cloud-init v Azure, přečtěte si [dokumentaci](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).


Chcete-li vyřešit potíže s vlastním spuštěním dat, přečtěte si [dokumentaci](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init)k řešení potíží .


## <a name="faq"></a>Nejčastější dotazy
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>Můžu aktualizovat vlastní data po vytvoření virtuálního virtuálního soudu?
Pro jednotlivé virtuální počítače vlastní data v modelu virtuálního počítači nelze aktualizovat, ale pro VMSS můžete aktualizovat vlastní data VMSS prostřednictvím rozhraní REST API (neplatí pro klienty PS nebo AZ CLI). Při aktualizaci vlastních dat v modelu VMSS:
* Existující instance ve VMSS nezíská aktualizovaná vlastní data, pouze dokud nejsou reimaged.
* Existující instance ve Službě vyupgradované služby VMSS nezískají aktualizovaná vlastní data.
* Nové instance obdrží nová vlastní data.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>Je možné umístit citlivé hodnoty do vlastních dat?
Doporučujeme **neukládat** citlivá data do vlastních dat. Další informace najdete v [tématu Azure Security and encryption best practices](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices).


### <a name="is-custom-data-made-available-in-imds"></a>Jsou v imds k dispozici vlastní data?
Ne, tato funkce není momentálně k dispozici.

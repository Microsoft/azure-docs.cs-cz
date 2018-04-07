---
title: Přesuňte soubory do a z virtuálních počítačů Linux Azure s spojovací bod služby | Microsoft Docs
description: Bezpečně přesunout soubory do a z virtuálního počítače s Linuxem v Azure pomocí spojovací bod služby a dvojici klíčů SSH.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: danlep
ms.openlocfilehash: 0231e402848e617a46ca70470ba4d3272ace59f7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Přesunutí souborů do a z virtuálního počítače s Linuxem pomocí spojovací bod služby

Tento článek ukazuje, jak k přesunutí souborů z pracovní stanice až virtuální počítač Azure Linux nebo Linux virtuální počítač Azure na pracovní stanici, pomocí zabezpečené kopírování (SCP). Přesouvání souborů mezi pracovní stanice a virtuální počítač s Linuxem, rychle a bezpečně, je důležité pro správu infrastruktury Azure. 

V tomto článku budete potřebovat Linux virtuálních počítačů nasadit v Azure pomocí [SSH soubory veřejného a privátního klíče](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Budete také potřebovat klientem spojovací bod služby v místním počítači. Je postavená na SSH a součástí výchozí prostředí Bash většina Linux a počítače Mac a některé součásti pro Windows.

## <a name="quick-commands"></a>Rychlé příkazy

Zkopírujte soubor až virtuálního počítače s Linuxem

```bash
scp file azureuser@azurehost:directory/targetfile
```

Zkopírujte soubor dolů z virtuálního počítače s Linuxem

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Podrobný postup

Jako příklady, jsme přesunout soubor konfigurace Azure až virtuálního počítače s Linuxem a vyžadování dolů adresář souboru protokolu, jak pomocí spojovací bod služby a SSH klíče.   

## <a name="ssh-key-pair-authentication"></a>Ověřování pár klíčů SSH

Spojovací bod služby používá SSH pro přenosové vrstvy. SSH zpracovává ověřování na cílovém hostiteli a přesune soubor v tunelu šifrované ve výchozím nastavení provádí pomocí protokolu SSH. Pro ověřování SSH lze použít uživatelských jmen a hesel. Veřejné a soukromé klíče ověřování SSH jsou však doporučujeme jako osvědčený postup zabezpečení. Po ověření připojení SSH spojovací bod služby poté zahájí kopírování souboru. Pomocí správně nakonfigurovaných `~/.ssh/config` a veřejného a privátního klíče SSH, spojovací bod služby připojení lze navázat jen pomocí názvu serveru (nebo IP adresa). Pokud máte pouze jeden klíč SSH, spojovací bod služby hledá v `~/.ssh/` adresář a používá je ve výchozím nastavení k přihlášení k virtuálnímu počítači.

Další informace o konfiguraci vašeho `~/.ssh/config` a veřejné a soukromé klíče SSH, najdete v části [vytvořit SSH klíče](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="scp-a-file-to-a-linux-vm"></a>Spojovací bod služby soubor, který chcete virtuální počítač s Linuxem

V prvním příkladu jsme zkopírujte soubor konfigurace Azure až Linux virtuálního počítače, který se používá k nasazení automatizace. Protože tento soubor obsahuje přihlašovací údaje Azure API, které obsahují tajné klíče, je důležité zabezpečení. Šifrované tunelového propojení SSH poskytované chrání obsah souboru.

Následující příkaz zkopíruje místní *.azure/config* souborů na virtuální počítač Azure s plně kvalifikovaný název domény *myserver.eastus.cloudapp.azure.com*. Uživatelské jméno správce ve virtuálním počítači Azure je *azureuser*. Soubor je zaměřena na */home/azureuser/* adresáře. Nahraďte vlastními hodnotami v tomto příkazu.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>Spojovací bod služby adresáře z virtuálního počítače s Linuxem

V tomto příkladu jsme zkopírujte adresář souborů protokolu z virtuálního počítače s Linuxem na pracovní stanici. Soubor protokolu může nebo nemusí obsahovat citlivá nebo tajný data. Použití spojovací bod služby zajišťuje ale, že jsou šifrovaná obsah souborů protokolu. Použití spojovací bod služby k přenosu souborů je nejjednodušší způsob, jak získat adresář protokolu a souborů na pracovní stanici při zároveň zabezpečené.

Následující příkaz zkopíruje soubory v */home/azureuser/logs/* adresář na virtuálním počítači Azure do místní TMP adresáře:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

`-r` Rozhraní příkazového řádku příznak dá pokyn spojovací bod služby k rekurzivnímu kopírování souborů a adresářů z bodu adresáře uvedené v příkazu.  Také Všimněte si, že je podobná syntaxe příkazového řádku `cp` zkopírujte příkaz.

## <a name="next-steps"></a>Další postup

* [Spravovat uživatele, SSH a zkontrolujte nebo opravte disky na virtuálních počítačích Azure Linux pomocí rozšíření VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
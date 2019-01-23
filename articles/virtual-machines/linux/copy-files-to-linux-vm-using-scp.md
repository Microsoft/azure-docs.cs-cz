---
title: Přesuňte soubory do a z Azure s Linuxem pomocí spojovacího bodu služby | Dokumentace Microsoftu
description: Bezpečně přesunout soubory do a z virtuálního počítače s Linuxem v Azure pomocí spojovacího bodu služby a pár klíčů SSH.
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
ms.component: disks
ms.openlocfilehash: 1f186e410718ce30f48602ce907afb6206597638
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54465764"
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Přesuňte soubory do a z virtuálního počítače s Linuxem pomocí spojovacího bodu služby

Tento článek ukazuje, jak přesunout soubory z pracovní stanice až Linuxový virtuální počítač Azure nebo z Azure virtuálního počítače s Linuxem na pracovní stanici, pomocí Secure Copy (SCP). Přesouvání souborů mezi pracovní stanice a virtuálního počítače s Linuxem, rychle a bezpečně, je velmi důležité pro správu infrastruktury Azure. 

Pro účely tohoto článku budete potřebovat systém Linux virtuálního počítače nasazeného v Azure s využitím [soubory veřejného a privátního klíče SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Budete také potřebovat klientem spojovací bod služby pro místní počítač. Je nástavbou SSH a součástí výchozí prostředí Bash většina počítačů se systémy Linux a Mac a některé součásti pro Windows.

## <a name="quick-commands"></a>Rychlé příkazy

Zkopírujte soubor do virtuálního počítače s Linuxem

```bash
scp file azureuser@azurehost:directory/targetfile
```

Kopírování souboru mimo provoz z virtuálního počítače s Linuxem

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Podrobný postup

Jako příklady, pustíme Azure konfigurační soubor do virtuálního počítače s Linuxem a přetáhnout adresář souboru protokolu, obě používají klíče spojovací bod služby a SSH.   

## <a name="ssh-key-pair-authentication"></a>Ověřování pomocí páru klíčů SSH

Spojovací bod služby používá SSH pro přenosovou vrstvu. SSH zpracovávat ověřování na cílovém hostiteli a přesune soubor do šifrovaného tunelu přes SSH k dispozici ve výchozím nastavení. Pro ověřování SSH je možné uživatelských jmen a hesel. Veřejné a soukromé ověřování pomocí klíče SSH jsou však doporučujeme jako osvědčený postup zabezpečení. Po ověření připojení SSH spojovací bod služby, potom začne, kopírování souboru. Pomocí správně nakonfigurovaných `~/.ssh/config` a veřejného a privátního klíče SSH, připojení spojovací bod služby lze vytvořit pouze pomocí názvu serveru (nebo IP adresa). Pokud máte jenom jeden klíč SSH, spojovací bod služby hledá v `~/.ssh/` adresáře a to ve výchozím nastavení používá k přihlášení k virtuálnímu počítači.

Další informace o konfiguraci vašeho `~/.ssh/config` a veřejného a privátního klíče SSH, naleznete v tématu [vytváření klíčů SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="scp-a-file-to-a-linux-vm"></a>Spojovací bod služby souborů do virtuálního počítače s Linuxem

První příklad jsme zkopírujte si soubor konfigurace Azure až do virtuálního počítače s Linuxem, který se používá k nasazení služby automation. Tento soubor obsahuje přihlašovací údaje rozhraní API služby Azure, které obsahují tajné kódy, je důležité zabezpečení. Šifrovaného tunelu SSH poskytované chrání obsah souboru.

Následující příkaz zkopíruje místní *.azure/config* souboru na Virtuálním počítači Azure s plně kvalifikovaný název domény *myserver.eastus.cloudapp.azure.com*. Uživatelské jméno správce pro virtuální počítač Azure je *azureuser*. Soubor je zacílený na */home/azureuser/* adresáře. Nahraďte vlastními hodnotami v tomto příkazu.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>Spojovací bod služby adresáře z virtuálního počítače s Linuxem

V tomto příkladu jsme kopírování adresáře souborů protokolu z virtuálního počítače s Linuxem na pracovní stanici. Soubor protokolu může nebo nemusí obsahovat citlivá nebo skrytou data. Pomocí však spojovací bod služby zajistíte, že se šifrují na obsah souborů protokolu. Použití spojovacího bodu služby k přenosu souborů je nejjednodušší způsob, jak získat adresář protokolu a soubory do pracovní stanice a také byla zabezpečené přitom.

Následující příkaz zkopíruje soubory */home/azureuser/logs/* adresář na virtuálním počítači Azure do místní TMP adresáře:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

`-r` Rozhraní příkazového řádku příznak nastaví spojovací bod služby se rekurzivně kopírovat soubory a adresáře z bodu adresář uvedený v příkazu.  Všimněte si také, že se podobá syntaxi příkazového řádku `cp` zkopírujte příkaz.

## <a name="next-steps"></a>Další postup

* [Spravovat uživatele, SSH a kontrola nebo opravte disků v Azure s Linuxem pomocí rozšíření VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
---
title: Přesunutí souborů do virtuálních počítačů Azure Linux a z nich pomocí SCP
description: Bezpečně přesouvat soubory do a z virtuálního počítače SM Linuxu v Azure pomocí SCP a dvojice klíčů SSH.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.topic: article
ms.date: 07/12/2017
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: a0837790b70de42073338bf085ee0f3976b866f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969605"
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Přesunutí souborů do a z virtuálního počítače s Linuxem pomocí SCP

Tento článek ukazuje, jak přesunout soubory z vaší pracovní stanice do virtuálního počítače Azure Linux nebo z virtuálního počítače Azure Linux u vaší pracovní stanice pomocí zabezpečené kopie (SCP). Rychlé a bezpečné přesouvání souborů mezi pracovní stanicí a virtuálním počítačem s Linuxem je pro správu infrastruktury Azure důležité. 

Pro tento článek potřebujete virtuální počítač s Linuxem nasazený v Azure pomocí [souborů s veřejným a soukromým klíčem SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Potřebujete také klienta SCP pro místní počítač. Je postaven na vrcholu SSH a je součástí výchozího bash shellu většiny počítačů Linux a Mac a některých prostředí Windows.

## <a name="quick-commands"></a>Rychlé příkazy

Kopírování souboru do virtuálního počítače s Linuxem

```bash
scp file azureuser@azurehost:directory/targetfile
```

Kopírování souboru z virtuálního počítače s Linuxem

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Podrobný postup

Jako příklady přesuneme konfigurační soubor Azure do virtuálního počítače SB Linux a stáhneme adresář souboru protokolu, a to jak pomocí klíčů SCP, tak SSH.   

## <a name="ssh-key-pair-authentication"></a>Ověřování dvojice klíčů SSH

SCP používá SSH pro transportní vrstvu. SSH zpracovává ověřování na cílovém hostiteli a přesune soubor v šifrovaném tunelu poskytovaném ve výchozím nastavení s SSH. Pro ověřování SSH lze použít uživatelská jména a hesla. Ověřování veřejného a soukromého klíče SSH se však doporučuje jako osvědčený postup zabezpečení. Jakmile SSH ověří připojení, SCP pak začne kopírování souboru. Pomocí správně `~/.ssh/config` nakonfigurované a SSH veřejné a soukromé klíče, připojení SCP lze navázat pouze pomocí názvu serveru (nebo IP adresu). Pokud máte jenom jeden klíč SSH, `~/.ssh/` SCP hledá v adresáři a používá jej ve výchozím nastavení k přihlášení k virtuálnímu virtuálnímu soudu.

Další informace o konfiguraci veřejných `~/.ssh/config` a soukromých klíčů SSH naleznete v [tématu Vytvoření klíčů SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="scp-a-file-to-a-linux-vm"></a>SCP soubor do virtuálního počítače s Linuxem

Pro první příklad zkopírujeme konfigurační soubor Azure až do virtuálního počítače s Linuxem, který se používá k nasazení automatizace. Vzhledem k tomu, že tento soubor obsahuje přihlašovací údaje rozhraní API Azure, které obsahují tajné klíče, zabezpečení je důležité. Šifrované tunelové propojení poskytované SSH chrání obsah souboru.

Následující příkaz zkopíruje místní soubor *.azure/config* do virtuálního počítače Azure s *myserver.eastus.cloudapp.azure.com*v oblasti přímých znalostí. Uživatelské jméno správce na virtuálním počítači Azure je *azureuser*. Soubor je zacílen na adresář */home/azureuser/.* V tomto příkazu nahraďte své vlastní hodnoty.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>SCP adresář z virtuálního počítače s Linuxem

V tomto příkladu zkopírujeme adresář souborů protokolu z virtuálního počítače s Linuxem až do pracovní stanice. Soubor protokolu může nebo nemusí obsahovat citlivá nebo tajná data. Použití protokolu SCP však zajišťuje, že obsah souborů protokolu je zašifrován. Použití SCP k přenosu souborů je nejjednodušší způsob, jak získat adresář protokolu a soubory dolů do pracovní stanice a zároveň je bezpečný.

Následující příkaz zkopíruje soubory v adresáři */home/azureuser/logs/* na virtuálním počítači Azure do místního adresáře /tmp:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

Příznak `-r` instruuje SCP, aby rekurzivně zkopíroval soubory a adresáře z bodu adresáře uvedeného v příkazu.  Všimněte si také, že syntaxe `cp` příkazového řádku je podobná příkazu copy.

## <a name="next-steps"></a>Další kroky

* [Správa uživatelů, SSH a kontrola nebo oprava disků na virtuálních počítačích Azure Linux pomocí rozšíření VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

---
title: Problémy s používáním rozšíření virtuálních počítačů v Pythonu 3 – Virtual Machines systémech Linux Azure s podporou
description: Přečtěte si o používání rozšíření virtuálních počítačů v systémech Linux 3 s podporou Pythonu.
services: virtual-machines
documentationcenter: ''
author: v-miegge
ms.author: jparrel
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/25/2020
ms.assetid: 3cd520fd-eaf7-4ef9-b4d3-4827057e5028
ms.openlocfilehash: d38b41bc47c01b01fcbb1b05238396dca6d8eb4e
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558640"
---
# <a name="issues-using-vm-extensions-in-python-3-enabled-linux-azure-virtual-machines-systems"></a>Problémy s používáním rozšíření virtuálních počítačů v Pythonu 3 – Virtual Machines systémech Linux Azure s podporou

> [!NOTE]
> Společnost Microsoft podporuje uživatelům, aby v systémech přijali **Python 3. x** , pokud vaše úlohy nevyžadují podporu **Python 2. x** . Příklady tohoto požadavku mohou zahrnovat starší verze skriptů pro správu nebo rozšíření, jako je například **Azure Disk Encryption** a **Azure monitor**.
>
> Před instalací **Python 2. x** v produkčním prostředí Zvažte otázku dlouhodobé podpory Python 2. x, zejména jejich schopnosti přijímat aktualizace zabezpečení. Jako produkty, včetně některých uvedených rozšíření, aktualizujte pomocí podpory **python 3,8** , byste měli přestat používat Python 2. x.

Některé distribuce systému Linux přešly do Pythonu 3,8 a zcela odebraly starší verze `/usr/bin/python` vstupního bodu pro Python. Tento přechod se týká předem připraveného automatického nasazení určitých rozšíření virtuálních počítačů (VM) s těmito dvěma podmínkami:

- Rozšíření, která stále přecházejí do podpory Python 3. x
- Rozšíření, která používají starší `/usr/bin/python` vstupní bod

Uživatelé s distribucí pro Linux, kteří přešli na **Python 3. x** , musí `/usr/bin/python` před pokusem o nasazení těchto rozšíření na své virtuální počítače ověřit existenci staršího bodu. V opačném případě může nasazení rozšíření selhat. 

- Mezi schválené distribuce systému Linux, které se týkají, patří **Ubuntu Server 20,04 LTS** a **Ubuntu pro 20,04 LTS**.

- Ovlivněná rozšíření virtuálních počítačů zahrnují **Azure Disk Encryption**, **Log Analytics**, **přístup k virtuálním** počítačům (používaný pro resetování hesla) a **diagnostiku hostů** (používá se pro další čítače výkonu).

Místní upgrady, jako je například upgrade z **Ubuntu 18,04 LTS** na **Ubuntu 20,04 LTS**, by měly zachovávat `/usr/bin/python` symlink a zůstat neovlivněné.

## <a name="resolution"></a>Řešení

Před nasazením rozšíření ve scénářích, které jsou popsané dříve v souhrnu, zvažte Tato obecná doporučení:

1. Před nasazením rozšíření znovu nasaďte `/usr/bin/python` symlink pomocí metody dodávané dodavatelem pro distribuci systému Linux.

   - Například pro **Python 2,7** použijte: `sudo apt update && sudo apt install python-is-python2`

1. Toto doporučení je pro zákazníky Azure a nepodporuje se v Azure Stack:

   - Pokud jste už nasadili instanci, která se tomuto problému projeví, spusťte pomocí příkazu spustit příkaz v okně virtuálního počítače výše uvedené příkazy. Rozšíření příkazu Spustit samotné není ovlivněno přechodem na Python 3,8.

1. Pokud nasazujete novou instanci a potřebujete nastavit rozšíření při zřizování, použijte data uživatele **Cloud-init** k instalaci balíčků uvedených výše.

   Například pro Python 2,7:

   ```python
   # create cloud-init config
   cat > cloudinitConfig.json <<EOF
   #cloud-config
   package_update: true
    
   runcmd:
   - sudo apt update
   - sudo apt install python-is-python2 
   EOF

   # create VM
   az vm create \
       --resource-group <resourceGroupName> \
       --name <vmName> \
       --image <Ubuntu 20.04 Image URN> \
       --admin-username azadmin \
       --ssh-key-value "<sshPubKey>" \
       --custom-data ./cloudinitConfig.json
   ```

1. Pokud Správci zásad vaší organizace určí, že rozšíření by se na virtuálních počítačích neměla nasadit, můžete podporu rozšíření v době zřizování zakázat:

   - REST API

     Zakázání a povolení rozšíření, když můžete nasadit virtuální počítač s touto vlastností:

     ```python
       "osProfile": {
         "allowExtensionOperations": false
       },
     ```

## <a name="next-steps"></a>Další kroky

Další informace najdete [v dalších základních změnách systému od verze 18,04 LTS-Python 3 ve výchozím nastavení](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes#Python3_by_default) .

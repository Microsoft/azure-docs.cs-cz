---
title: Resetovat heslo pro virtuální počítač s Linuxem a klíč SSH z rozhraní příkazového řádku | Microsoft Docs
description: Jak používat rozšíření VMAccess z rozhraní příkazového řádku Azure (CLI) k obnovení virtuálního počítače s Linuxem heslo nebo klíč SSH, opravit konfiguraci SSH a kontrola konzistence disku
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: d975eb70-5ff1-40d1-a634-8dd2646dcd17
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: cynthn
ms.openlocfilehash: c36498d2f4fef506dc7047fe91666aceec73c13d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-reset-a-linux-vm-password-or-ssh-key-fix-the-ssh-configuration-and-check-disk-consistency-using-the-vmaccess-extension"></a>Jak obnovit virtuální počítač s Linuxem heslo nebo klíč SSH, opravit konfiguraci SSH a kontrola konzistence disku pomocí rozšíření VMAccess
Pokud se nemůžete připojit k virtuální počítač s Linuxem v Azure z důvodu zapomenuté heslo, nesprávného klíče Secure Shell (SSH), nebo problém s konfigurací SSH, používají rozšíření VMAccessForLinux s Azure CLI resetovat heslo nebo klíč SSH, opravte SSH Konfigurace a kontrolu konzistence disku. 

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Zjistěte, jak [provést tento postup pomocí modelu Resource Manageru](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess).

Pomocí Azure CLI, můžete použít **sadu rozšíření virtuálního počítače azure** příkaz vaše rozhraní příkazového řádku (Bash, terminálu, příkazového řádku) přístup k příkazům. Spustit **sadu rozšíření virtuálního počítače azure nápovědy** pro použití podrobné rozšíření.

Pomocí rozhraní příkazového řádku Azure lze provádět následující úlohy:

* [Resetování hesla](#pwresetcli)
* [Resetovat klíč SSH](#sshkeyresetcli)
* [Resetovat heslo a klíč SSH](#resetbothcli)
* [Vytvořit nový uživatelský účet sudo](#createnewsudocli)
* [Resetování konfigurace SSH](#sshconfigresetcli)
* [Odstranit uživatele](#deletecli)
* [Zobrazuje stav rozšíření VMAccess](#statuscli)
* [Kontrola konzistence přidaných disků](#checkdisk)
* [Opravte přidaných disků na virtuálním počítačům s Linuxem](#repairdisk)

## <a name="prerequisites"></a>Požadavky
Musíte provést následující akce:

* Budete muset [nainstalovat Azure CLI](../../../cli-install-nodejs.md) a [připojení k vašemu předplatnému](/cli/azure/authenticate-azure-cli) používat prostředky Azure, které jsou spojené s vaším účtem.
* Nastavte do správného režimu pro model nasazení classic pomocí následujícího příkazu na příkazovém řádku:
    ``` 
        azure config mode asm
    ```
* Pokud chcete obnovit některý mají nové heslo nebo sadu klíče SSH. Pokud chcete resetovat konfiguraci SSH tyto nepotřebujete.

## <a name="pwresetcli"></a>Resetování hesla
1. Vytvořte soubor do místního počítače s názvem PrivateConf.json se tyto řádky. Nahraďte **uživatelské_jméno** a **myP@ssW0rd** s vlastní uživatelské jméno a heslo a nastavit vlastní datum vypršení platnosti.

    ```   
        {
        "username":"myUserName",
        "password":"myP@ssW0rd",
        "expiration":"2020-01-01"
        }
    ```
        
2. Spusťte tento příkaz, nahraďte název virtuálního počítače pro **Můjvp**.

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json
    ```

## <a name="sshkeyresetcli"></a>Resetovat klíč SSH
1. Vytvořte soubor s názvem PrivateConf.json se tyto obsah. Nahraďte **uživatelské_jméno** a **mySSHKey** hodnoty nahraďte svými vlastními informacemi.

    ```   
        {
        "username":"myUserName",
        "ssh_key":"mySSHKey"
        }
    ```
2. Spusťte tento příkaz, nahraďte název virtuálního počítače pro **Můjvp**.
   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="resetbothcli"></a>Resetovat heslo a klíč SSH
1. Vytvořte soubor s názvem PrivateConf.json se tyto obsah. Nahraďte **uživatelské_jméno**, **mySSHKey** a **myP@ssW0rd** hodnoty nahraďte svými vlastními informacemi.

    ``` 
        {
        "username":"myUserName",
        "ssh_key":"mySSHKey",
        "password":"myP@ssW0rd"
        }
    ```

2. Spusťte tento příkaz, nahraďte název virtuálního počítače pro **Můjvp**.

    ```   
        azure vm extension set MyVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

## <a name="createnewsudocli"></a>Vytvořit nový uživatelský účet sudo

Pokud jste zapomněli svoje uživatelské jméno, můžete vytvořit nový s autoritou sudo VMAccess. V takovém případě existující uživatelské jméno a heslo se nezmění.

Pokud chcete vytvořit nového uživatele sudo s přístupem heslo, použijte skript v [resetování hesla](#pwresetcli) a zadejte nové uživatelské jméno.

Pokud chcete vytvořit nového uživatele sudo s přístupem klíče SSH, použijte skript v [resetovat klíč SSH](#sshkeyresetcli) a zadejte nové uživatelské jméno.

Můžete také použít [resetovat heslo a klíč SSH](#resetbothcli) k vytvoření nového uživatele s heslem a přístup ke klíčům SSH.

## <a name="sshconfigresetcli"></a>Resetování konfigurace SSH
Pokud konfiguraci SSH je ve stavu nežádoucí, můžete také ztratit přístup k virtuálnímu počítači. Můžete použít rozšíření VMAccess k resetování konfigurace do výchozího stavu. Uděláte to tak, stačí nastavit klíč "reset_ssh" na "True". Rozšíření restartujte SSH server, otevřít port SSH na vašem virtuálním počítači a obnovit výchozí hodnoty v konfiguraci SSH. Uživatelský účet (název, hesla nebo klíče SSH) nebudou změněny.

> [!NOTE]
> Konfigurační soubor SSH, který získá resetovat se nachází v /etc/ssh/sshd_config.
> 
> 

1. Vytvořte soubor s názvem PrivateConf.json tohoto obsahu.

    ```   
        {
        "reset_ssh":"True"
        }
    ```

2. Spusťte tento příkaz, nahraďte název virtuálního počítače pro **Můjvp**. 

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

## <a name="deletecli"></a>Odstranit uživatele
Pokud chcete odstranit účet uživatele bez přihlásíte do virtuálního počítače přímo, můžete tento skript.

1. Vytvořte soubor s názvem PrivateConf.json tohoto obsahu, nahraďte uživatelské jméno pro odebrání **removeUserName**. 

    ```   
        {
        "remove_user":"removeUserName"
        }
    ```

2. Spusťte tento příkaz, nahraďte název virtuálního počítače pro **Můjvp**. 

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

## <a name="statuscli"></a>Zobrazuje stav rozšíření VMAccess
Chcete-li zobrazit stav rozšíření VMAccess, spusťte tento příkaz.

```
        azure vm extension get
```

## <a name='checkdisk'></a>Kontrola konzistence přidaných disků
Pokud chcete spustit fsck na všech discích ve virtuálním počítači Linux, musíte provést následující akce:

1. Vytvořte soubor s názvem PublicConf.json tohoto obsahu. Zkontrolujte, zda že disk trvá logická hodnota označuje, jestli chcete zkontrolovat disky připojené k virtuálnímu počítači, nebo ne. 

    ```   
        {   
        "check_disk": "true"
        }
    ```

2. Spusťte tento příkaz k provedení, nahraďte název virtuálního počítače pro **Můjvp**.

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json 
    ```

## <a name='repairdisk'></a>Oprava disky
K opravě disky, které nejsou připojení nebo připojení chyby konfigurace, resetujte konfiguraci připojení na virtuální počítač Linux pomocí rozšíření VMAccess. Nahraďte název disku pro **myDisk**.

1. Vytvořte soubor s názvem PublicConf.json tohoto obsahu. 

    ```   
        {
        "repair_disk":"true",
        "disk_name":"myDisk"
        }
    ```

2. Spusťte tento příkaz k provedení, nahraďte název virtuálního počítače pro **Můjvp**.

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json
    ```

## <a name="next-steps"></a>Další postup
* Pokud chcete resetovat heslo nebo klíč SSH pomocí rutin prostředí Azure PowerShell nebo šablon Azure Resource Manageru, opravte SSH konfigurace a kontrolu konzistence disku, najdete [VMAccess rozšíření dokumentaci na Githubu](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess). 
* Můžete také [portál Azure](https://portal.azure.com) resetovat heslo nebo klíč SSH virtuálních počítačů Linux nasazené v modelu nasazení classic. Nemůžete použít aktuálně portálu proveďte to pro virtuální počítač s Linuxem nasazené v modelu nasazení Resource Manager.
* V tématu [o rozšíření virtuálního počítače a funkcích](../extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Další informace o použití rozšíření virtuálního počítače pro virtuální počítače Azure.


---
title: Použít soubory Azure s Linuxem | Microsoft Docs
description: Zjistěte, jak připojit sdílenou složku Azure prostřednictvím protokolu SMB v systému Linux.
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tamram
ms.assetid: 6edc37ce-698f-4d50-8fc1-591ad456175d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2018
ms.author: renash
ms.openlocfilehash: 667f385e4f157a5e1b9fcaf47b25619eafa8e9e3
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738240"
---
# <a name="use-azure-files-with-linux"></a>Použít soubory Azure s Linuxem
Služba [Soubory Azure](storage-files-introduction.md) je snadno použitelný cloudový systém souborů od Microsoftu. Sdílené složky Azure může být připojen v distribucí Linux pomocí [klient jádra SMB](https://wiki.samba.org/index.php/LinuxCIFS). Tento článek ukazuje dva způsoby, jak připojit sdílenou složku Azure: na vyžádání pomocí `mount` příkazů a na spouštění pomocí vytváření položku v `/etc/fstab`.

> [!NOTE]  
> Aby bylo možné připojit sdílenou složku Azure mimo oblast Azure, které je umístěn v, jako jsou místní nebo v jiné oblasti Azure, operačního systému musí podporovat funkci šifrování protokolu SMB 3.0.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Požadavky pro připojení Azure sdílené složky pro systémy Linux a cifs utils balíčku
* **Vyberte distribuce systému Linux, která může mít tento cifs utils balíček nainstalován.**  
    Následující Linuxových distribucích jsou k dispozici pro použití v galerii Azure:

    * Ubuntu Server 14.04 +
    * RHEL 7 +
    * CentOS 7 +
    * Debian 8 +
    * openSUSE 13.2 +
    * SUSE Linux Enterprise Server 12

* <a id="install-cifs-utils"></a>**Je nainstalován balíček cifs utils.**  
    Balíček cifs utils můžete nainstalovat pomocí Správce balíčků na distribučním Linux podle svého výběru. 

    Na **Ubuntu** a **na základě Debian** distribuce, použijte `apt-get` Správce balíčků:

    ```bash
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    Na **RHEL** a **CentOS**, použijte `yum` Správce balíčků:

    ```bash
    sudo yum install cifs-utils
    ```

    Na **openSUSE**, použijte `zypper` Správce balíčků:

    ```bash
    sudo zypper install cifs-utils
    ```

    Na další distribuce pomocí příslušné package manager nebo [zkompilovat ze zdroje](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).

* <a id="smb-client-reqs"></a>**Pochopte požadavky klientů SMB.**  
    Soubory Azure může být připojen buď prostřednictvím protokolu SMB 2.1 a protokolu SMB 3.0. Pro připojení pocházející z klienti místně nebo v jiných oblastech Azure Azure Files odmítnou SMB 2.1 (nebo bez šifrování protokolu SMB 3.0). Pokud *zabezpečení přenosu požadované* je povolená pro účet úložiště Azure Files bude povolit připojení pouze pomocí šifrování protokolu SMB 3.0.
    
    Podpora šifrování protokolu SMB 3.0 byla zavedena v systému Linux verze jádra 4.11 a byla přeneseny zpět ke starším verzím jádra pro oblíbené distribuce systému Linux. V době publikování tohoto dokumentu tuto funkci podporovat následující distribuce z Galerie Azure:

    - Ubuntu Server 16.04 +
    - openSUSE 42.3 +
    - SUSE Linux Enterprise Server 12 SP3 +
    
    Pokud zde není uveden distribuční Linux, můžete zkontrolovat, naleznete v části verze jádra Linux pomocí následujícího příkazu:

    ```bash
    uname -r
    ```

* **Rozhodněte o oprávnění souboru nebo adresáře připojenou složku**: V příkladech níže oprávnění `0777` se používá ke čtení, zápisu a oprávnění ke všem uživatelům. Nahraďte ji s jinými [oprávnění chmod](https://en.wikipedia.org/wiki/Chmod) podle potřeby. 

* **Název účtu úložiště**: připojit sdílenou složku Azure, potřebujete název účtu úložiště.

* **Klíč účtu úložiště**: připojit sdílenou složku Azure, je nutné klíč primární (nebo sekundární) úložiště. Klíče SAS aktuálně nejsou pro připojení podporovány.

* **Ujistěte se, je otevřený port 445**: SMB komunikuje přes port TCP 445 - zkontrolujte, pokud chcete zobrazit, pokud brána firewall neblokuje TCP porty 445 z klientského počítače.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Připojení Azure file sdílenou složku na vyžádání pomocí `mount`
1. **[Nainstalovat balíček cifs utils pro Linux distribuční](#install-cifs-utils)**.

2. **Vytvořte složku pro přípojného bodu**: složku pro přípojný bod lze vytvořit kdekoli v systému souborů, ale je běžné konvence na vytvořte ji v části `/mnt` složky. Příklad:

    ```bash
    mkdir /mnt/MyAzureFileShare
    ```

3. **Použijte příkaz připojení připojit sdílenou složkou Azure**: Nezapomeňte nahradit `<storage-account-name>`, `<share-name>`, `<smb-version>`, `<storage-account-key>`, a `<mount-point>` odpovídající informace pro vaše prostředí. Pokud distribuční Linux podporuje protokol SMB 3.0 se šifrováním (najdete v části [SMB pochopit požadavky na klienta](#smb-client-reqs) informace), použijte `3.0` pro `<smb-version>`. U Linuxových distribucích, které nepodporují pomocí šifrování protokolu SMB 3.0, použijte `2.1` pro `<smb-version>`. Všimněte si, že sdílenou složku Azure může být připojen pouze mimo oblast Azure (včetně místně nebo v jiné oblasti Azure) s protokolem SMB 3.0. 

    ```bash
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Po dokončení pomocí Azure sdílené složky, můžete použít `sudo umount <mount-point>` o odpojení sdílené složky.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Vytvořit bod trvalé připojení pro Azure sdílené složky `/etc/fstab`
1. **[Nainstalovat balíček cifs utils pro Linux distribuční](#install-cifs-utils)**.

2. **Vytvořte složku pro přípojného bodu**: složku pro přípojný bod lze vytvořit kdekoli v systému souborů, ale je běžné konvence na vytvořte ji v části `/mnt` složky. Bez ohledu na to vytvoříte, mějte na paměti absolutní cestu ke složce. Například následující příkaz vytvoří novou složku s `/mnt` (cesta je absolutní cestu).

    ```bash
    sudo mkdir /mnt/MyAzureFileShare
    ```

3. **Vytvořte soubor přihlašovacích údajů k ukládání uživatelského jména (název účtu úložiště) a heslo (klíč účtu úložiště) pro sdílené složky.** Nezapomeňte nahradit `<storage-account-name>` a `<storage-account-key>` odpovídající informace pro vaše prostředí. 

    ```bash
    if [ -d "/etc/smbcredentials" ]; then
        sudo mkdir /etc/smbcredentials
    fi

    if [ ! -f "/etc/smbcredentials/<storage-account-name>.cred" ]; then
        sudo bash -c 'echo "username=<storage-account-name>" >> /etc/smbcredentials/<storage-account-name>.cred'
        sudo bash -c 'echo "password=<storage-account-key>" >> /etc/smbcredentials/<storage-account-name>.cred'
    fi
    ```

4. **Změna oprávnění u souboru přihlašovacích údajů, pouze kořenové můžou číst nebo upravit soubor heslo.** Vzhledem k tomu, že klíč účtu úložiště je v podstatě nadtypem správce heslo pro účet úložiště, nastavení oprávnění u souboru, ke kterým tak, aby se jenom kořenový přístup je důležité, aby nižší oprávnění uživatelé nemůžou načtení klíč účtu úložiště.   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage-account-name>.cred
    ```

5. **Použijte následující příkaz pro připojení následující řádek do `/etc/fstab`** : Nezapomeňte nahradit `<storage-account-name>`, `<share-name>`, `<smb-version>`, a `<mount-point>` odpovídající informace pro vaše prostředí. Pokud distribuční Linux podporuje protokol SMB 3.0 se šifrováním (najdete v části [SMB pochopit požadavky na klienta](#smb-client-reqs) informace), použijte `3.0` pro `<smb-version>`. U Linuxových distribucích, které nepodporují pomocí šifrování protokolu SMB 3.0, použijte `2.1` pro `<smb-version>`. Všimněte si, že sdílenou složku Azure může být připojen pouze mimo oblast Azure (včetně místně nebo v jiné oblasti Azure) s protokolem SMB 3.0. 

    ```bash
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> <mount-point> cifs nofail,vers=<smb-version>,credentials=/etc/smbcredentials/<storage-account-key>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> Můžete použít `sudo mount -a` připojit sdílenou složkou Azure po dokončení úprav `/etc/fstab` místo restartování.

## <a name="feedback"></a>Váš názor
Linux uživatele, chceme slyšet váš názor!

Soubory Azure pro skupiny uživatelů Linux poskytuje fórum můžete sdílet zpětnou vazbu, jak vyhodnotit a přijmout soubor úložiště v systému Linux. E-mailu [Azure soubory Linux uživatelé](mailto:azurefileslinuxusers@microsoft.com) o připojení ke skupině uživatelů.

## <a name="next-steps"></a>Další postup
Další informace o službě Soubory Azure najdete na těchto odkazech.
* [Úvod do Azure soubory](storage-files-introduction.md)
* [Plánování nasazení služby Soubory Azure](storage-files-planning.md)
* [Nejčastější dotazy](../storage-files-faq.md)
* [Řešení potíží](storage-troubleshoot-linux-file-connection-problems.md)
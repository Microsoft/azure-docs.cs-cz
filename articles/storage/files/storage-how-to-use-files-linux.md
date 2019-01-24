---
title: Použití služby soubory Azure s Linuxem | Dokumentace Microsoftu
description: Informace o připojení sdílené složky Azure přes protokol SMB v systému Linux.
services: storage
author: RenaShahMSFT
ms.service: storage
ms.topic: article
ms.date: 03/29/2018
ms.author: renash
ms.component: files
ms.openlocfilehash: df701c6b3131686d5b3b4c093b23de2f6d22bdbc
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54827461"
---
# <a name="use-azure-files-with-linux"></a>Použití služby Soubory Azure s Linuxem
Služba [Soubory Azure](storage-files-introduction.md) je snadno použitelný cloudový systém souborů od Microsoftu. Sdílené složky Azure je možné připojit v Linuxových distribucích pomocí [klient SMB jádra](https://wiki.samba.org/index.php/LinuxCIFS). Tento článek ukazuje dva způsoby připojení sdílené složky Azure: na vyžádání pomocí `mount` příkazů a na spouštění tak, že vytvoříte položku v `/etc/fstab`.

> [!NOTE]  
> Aby bylo možné připojit sdílenou složku Azure mimo oblast, která je hostovaná, například v místním prostředí nebo v jiné oblasti Azure, operační systém musí podporovat funkci šifrování protokolu SMB 3.0.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Požadavky pro připojení sdílené složky Azure se systémy Linux a cifs utils balíčku
<a id="smb-client-reqs"></a>
* **Můžete si vyberte Linuxová distribuce vytvořená tak, aby odpovídala vašim potřebám připojení.**  
      Služba soubory Azure je možné připojit přes protokol SMB 2.1, tak i SMB 3.0. Pro připojení pocházející z klientů na místní nebo v jiných oblastech Azure musíte použít protokol SMB 3.0; Služba soubory Azure, bude taková SMB 2.1 (nebo bez šifrování protokolu SMB 3.0). Sdílená složka Azure při práci z virtuálního počítače v rámci stejné oblasti Azure, může přístup sdílené složky pomocí protokolu SMB 2.1, pokud a pouze tehdy, pokud *vyžadovat zabezpečený přenos* je zakázaná pro účet úložiště, který hostuje sdílenou složku Azure. Doporučujeme vždy vyžaduje zabezpečený přenos a používání pouze protokolu SMB 3.0 pomocí šifrování.
    
    Podpora šifrování protokolu SMB 3.0 byla zavedena v systému Linux verze jádra 4.11 a jestli se přeneseny zpět do starší verze jádra pro oblíbené distribuce Linuxu. V době publikování tohoto dokumentu následujících distribucích z Galerie Azure podporují možnost připojení zadaný v záhlaví tabulky. 

* **Minimální doporučená verze s odpovídající možností připojení (verze protokolu SMB 2.1 vs verze protokolu SMB 3.0)**    
    
    |   | SMB 2.1 <br>(Připojení na virtuálních počítačích v rámci stejné oblasti Azure) | SMB 3.0 <br>(Připojení z na místě a mezi oblastmi) |
    | --- | :---: | :---: |
    | Ubuntu Server | 14.04+ | 16.04+ |
    | RHEL | 7+ | 7.5+ |
    | CentOS | 7+ |  7.5+ |
    | Debian | 8+ |   |
    | openSUSE | 13.2+ | 42.3+ |
    | SUSE Linux Enterprise Server | 12 | 12 SP3+ |
    
    Pokud tu není uvedený vaší distribuci Linuxu, můžete zkontrolovat verzi jádra systému Linux pomocí následujícího příkazu:    

   ```bash
   uname -r
   ```    

* <a id="install-cifs-utils"></a>**Je nainstalován balíček cifs utils.**  
    Balíček cifs utils můžete nainstalovat pomocí Správce balíčků na distribucí Linuxu podle vašeho výběru. 

    Na **Ubuntu** a **založených na Debian** distribucí, použijte `apt-get` Správce balíčků:

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

    Na jiné distribuce, použijte odpovídající balíček správce nebo [zkompilovat ze zdroje](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)
    
* **Při rozhodování o oprávnění adresářů a souborů připojené sdílené složky**: V příkladech níže oprávnění `0777` je slouží k pojmenování pro čtení, zápisu a spouštěcích oprávnění pro všechny uživatele. Nahradit ho s jinými [chmod oprávnění](https://en.wikipedia.org/wiki/Chmod) podle potřeby. 

* **Název účtu úložiště**: Připojte sdílenou složku Azure, budete potřebovat název účtu úložiště.

* **Klíč účtu úložiště**: Připojte sdílenou složku Azure, musíte primární (nebo sekundární) klíč úložiště. Klíče SAS aktuálně nejsou pro připojení podporovány.

* **Zkontrolujte, jestli je port 445 otevřený**: Protokol SMB komunikuje přes protokol TCP 445 – zkontrolujte, že brána firewall neblokuje port TCP 445 z klientského počítače.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Připojení Azure file sdílené složky na vyžádání pomocí `mount`
1. **[Nainstalovat balíček cifs utils pro vaši Linuxovou distribuci](#install-cifs-utils)**.

2. **Vytvořte složku k přípojnému bodu**: Složka pro bod připojení je vytvořit kdekoli v systému souborů, ale je běžné konvence k vytvoření tohoto pod `/mnt` složky. Příklad:

    ```bash
    mkdir /mnt/MyAzureFileShare
    ```

3. **Připojení sdílené složky Azure pomocí příkazu připojení**: Nezapomeňte nahradit `<storage-account-name>`, `<share-name>`, `<smb-version>`, `<storage-account-key>`, a `<mount-point>` odpovídajícími informacemi pro vaše prostředí. Pokud vaší distribuci Linuxu podporuje šifrování protokolu SMB 3.0 (naleznete v tématu [SMB pochopit požadavky na klienta](#smb-client-reqs) Další informace), použijte `3.0` pro `<smb-version>`. Pro distribuce Linuxu, které nepodporují šifrování protokolu SMB 3.0, použijte `2.1` pro `<smb-version>`. Všimněte si, že sdílené složky Azure je možné připojit pouze mimo oblast Azure (včetně místní nebo v jiné oblasti Azure) s protokolem SMB 3.0. 

    ```bash
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Po dokončení pomocí sdílené složky Azure, můžete použít `sudo umount <mount-point>` odpojit sdílenou složku.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Vytvořit bod trvalé připojení pro sdílenou složku Azure pomocí `/etc/fstab`
1. **[Nainstalovat balíček cifs utils pro vaši Linuxovou distribuci](#install-cifs-utils)**.

2. **Vytvořte složku k přípojnému bodu**: Složka pro bod připojení je vytvořit kdekoli v systému souborů, ale je běžné konvence k vytvoření tohoto pod `/mnt` složky. Bez ohledu na to vytvoříte, mějte na paměti absolutní cestu ke složce. Například následující příkaz vytvoří novou složku s `/mnt` (cesta je absolutní cesta).

    ```bash
    sudo mkdir /mnt/MyAzureFileShare
    ```

3. **Vytvořte soubor přihlašovacích údajů ukládat uživatelské jméno (název účtu úložiště) a heslo (klíč účtu úložiště) pro sdílenou složku.** Nezapomeňte nahradit `<storage-account-name>` a `<storage-account-key>` odpovídajícími informacemi pro vaše prostředí. 

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
        sudo mkdir /etc/smbcredentials
    fi

    if [ ! -f "/etc/smbcredentials/<storage-account-name>.cred" ]; then
        sudo bash -c 'echo "username=<storage-account-name>" >> /etc/smbcredentials/<storage-account-name>.cred'
        sudo bash -c 'echo "password=<storage-account-key>" >> /etc/smbcredentials/<storage-account-name>.cred'
    fi
    ```

4. **Změna oprávnění u soubor s přihlašovacími údaji, tak jenom kořenový může číst nebo upravovat soubor hesla.** Klíč účtu úložiště je v podstatě velmi správce heslo pro účet úložiště, nastavení oprávnění pro soubor, který se dostanete tak, aby se pouze hlavní je důležité, aby nižší oprávnění uživatele nelze načíst klíč účtu úložiště.   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage-account-name>.cred
    ```

5. **Použijte následující příkaz pro přidání následující řádek, který `/etc/fstab`** : Nezapomeňte nahradit `<storage-account-name>`, `<share-name>`, `<smb-version>`, a `<mount-point>` odpovídajícími informacemi pro vaše prostředí. Pokud vaší distribuci Linuxu podporuje šifrování protokolu SMB 3.0 (naleznete v tématu [SMB pochopit požadavky na klienta](#smb-client-reqs) Další informace), použijte `3.0` pro `<smb-version>`. Pro distribuce Linuxu, které nepodporují šifrování protokolu SMB 3.0, použijte `2.1` pro `<smb-version>`. Všimněte si, že sdílené složky Azure je možné připojit pouze mimo oblast Azure (včetně místní nebo v jiné oblasti Azure) s protokolem SMB 3.0. 

    ```bash
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> <mount-point> cifs nofail,vers=<smb-version>,credentials=/etc/smbcredentials/<storage-account-name>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> Můžete použít `sudo mount -a` připojit sdílenou složku Azure po úpravě `/etc/fstab` místo restartování.

## <a name="feedback"></a>Váš názor
Uživatelé Linuxu, chceme znát váš názor!

Soubory Azure pro skupinu uživatelů Linuxu poskytuje diskusní fórum pro můžete sdílet zpětnou vazbu, jak vyhodnotit a přijmout úložiště souborů v Linuxu. E-mailu [uživatelů Linuxu soubory Azure](mailto:azurefileslinuxusers@microsoft.com) připojení ke skupině uživatelů.

## <a name="next-steps"></a>Další postup
Další informace o službě Soubory Azure najdete na těchto odkazech.
* [Úvod do služby soubory Azure](storage-files-introduction.md)
* [Plánování nasazení služby Soubory Azure](storage-files-planning.md)
* [Nejčastější dotazy](../storage-files-faq.md)
* [Řešení potíží](storage-troubleshoot-linux-file-connection-problems.md)

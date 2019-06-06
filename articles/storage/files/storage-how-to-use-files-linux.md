---
title: Použití služby soubory Azure s Linuxem | Dokumentace Microsoftu
description: Informace o připojení sdílené složky Azure přes protokol SMB v systému Linux.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 03/29/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 73ed98bf950f7c9f52e2b8eeb431fe4b36bfe324
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427922"
---
# <a name="use-azure-files-with-linux"></a>Použití služby Soubory Azure s Linuxem

Služba [Soubory Azure](storage-files-introduction.md) je snadno použitelný cloudový systém souborů od Microsoftu. Sdílené složky Azure je možné připojit v Linuxových distribucích pomocí [klient SMB jádra](https://wiki.samba.org/index.php/LinuxCIFS). Tento článek ukazuje dva způsoby připojení sdílené složky Azure: na vyžádání pomocí `mount` příkazů a na spouštění tak, že vytvoříte položku v `/etc/fstab`.

> [!NOTE]  
> Aby bylo možné připojit sdílenou složku Azure mimo oblast, která je hostovaná, například v místním prostředí nebo v jiné oblasti Azure, operační systém musí podporovat funkci šifrování protokolu SMB 3.0.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Požadavky pro připojení sdílené složky Azure se systémy Linux a cifs utils balíčku
<a id="smb-client-reqs"></a>

* **Existující služby Azure storage souborů a sdílenou složku**: K dokončení tohoto článku, musíte mít účet úložiště, sdílenou složku. Pokud jste jednu ještě nevytvořili, přečtěte si téma jednu z našich šablon rychlý start k tomuto tématu: [Vytvoření sdílené složky – rozhraní příkazového řádku](storage-how-to-use-files-cli.md).

* **Název účtu úložiště a klíč** budete potřebovat název účtu úložiště a klíč k dokončení tohoto článku. Pokud jste vytvořili, jeden v rámci rychlého startu CLI, byste už měli mít je, v opačném případě najdete rychlý start rozhraní příkazového řádku, který byl propojen dříve, aby bylo možné, přečtěte si, jak načíst klíč účtu úložiště.

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

* **Zkontrolujte, jestli je port 445 otevřený**: Protokol SMB komunikuje přes protokol TCP 445 – zkontrolujte, že brána firewall neblokuje port TCP 445 z klientského počítače.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Připojení Azure file sdílené složky na vyžádání pomocí `mount`

1. **[Nainstalovat balíček cifs utils pro vaši Linuxovou distribuci](#install-cifs-utils)** .

1. **Vytvořte složku k přípojnému bodu**: Složka pro bod připojení je vytvořit kdekoli v systému souborů, ale je běžné konvence k vytvoření tohoto v nové složce. Například následující příkaz vytvoří nový adresář, nahraďte **< název_účtu_úložiště >** a **< file_share_name >** odpovídajícími informacemi pro vaše prostředí:

    ```bash
    mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **Připojení sdílené složky Azure pomocí příkazu připojení**: Nezapomeňte nahradit **< název_účtu_úložiště >** , **< název_sdílené_složky >** , **< smb_version >** , **< klíč_účtu_úložiště >** , a **< mount_point >** odpovídajícími informacemi pro vaše prostředí. Pokud vaší distribuci Linuxu podporuje šifrování protokolu SMB 3.0 (naleznete v tématu [SMB pochopit požadavky na klienta](#smb-client-reqs) Další informace), použijte **3.0** pro **< smb_version >** . Pro distribuce Linuxu, které nepodporují šifrování protokolu SMB 3.0, použijte **2.1** pro **< smb_version >** . Je pouze možné připojit sdílenou složku Azure mimo oblast Azure (včetně místní nebo v jiné oblasti Azure) s protokolem SMB 3.0. 

    ```bash
    sudo mount -t cifs //<storage_account_name>.file.core.windows.net/<share_name> <mount_point> -o vers=<smb_version>,username=<storage_account_name>,password=<storage_account_key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Po dokončení pomocí sdílené složky Azure, můžete použít `sudo umount <mount_point>` odpojit sdílenou složku.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Vytvořit bod trvalé připojení pro sdílenou složku Azure pomocí `/etc/fstab`

1. **[Nainstalovat balíček cifs utils pro vaši Linuxovou distribuci](#install-cifs-utils)** .

1. **Vytvořte složku k přípojnému bodu**: Složka pro bod připojení je vytvořit kdekoli v systému souborů, ale je běžné konvence k vytvoření tohoto v nové složce. Bez ohledu na to vytvoříte, mějte na paměti absolutní cestu ke složce. Například následující příkaz vytvoří nový adresář, nahraďte **< název_účtu_úložiště >** a **< file_share_name >** odpovídajícími informacemi pro vaše prostředí.

    ```bash
    sudo mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **Vytvořte soubor přihlašovacích údajů ukládat uživatelské jméno (název účtu úložiště) a heslo (klíč účtu úložiště) pro sdílenou složku.** Nahraďte **< název_účtu_úložiště >** a **< klíč_účtu_úložiště >** odpovídajícími informacemi pro vaše prostředí.

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
    fi
    if [ ! -f "/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred" ]; then
    sudo bash -c 'echo "username=<STORAGE ACCOUNT NAME>" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    sudo bash -c 'echo "password=7wRbLU5ea4mgc<DRIVE LETTER>PIpUCNcuG9gk2W4S2tv7p0cTm62wXTK<DRIVE LETTER>CgJlBJPKYc4VMnwhyQd<DRIVE LETTER>UT<DRIVE LETTER>yR5/RtEHyT/EHtg2Q==" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    fi
    ```

1. **Změna oprávnění u soubor s přihlašovacími údaji, tak jenom kořenový může číst nebo upravovat soubor hesla.** Klíč účtu úložiště je v podstatě velmi správce heslo pro účet úložiště, nastavení oprávnění pro soubor, který se dostanete tak, aby se pouze hlavní je důležité, aby nižší oprávnění uživatele nelze načíst klíč účtu úložiště.   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage_account_name>.cred
    ```

1. **Použijte následující příkaz pro přidání následující řádek, který `/etc/fstab`** : Nezapomeňte nahradit **< název_účtu_úložiště >** , **< název_sdílené_složky >** , **< smb_version >** , a **< mount_point >** odpovídajícími informacemi pro vaše prostředí. Pokud vaší distribuci Linuxu podporuje šifrování protokolu SMB 3.0 (naleznete v tématu [SMB pochopit požadavky na klienta](#smb-client-reqs) Další informace), použijte **3.0** pro **< smb_version >** . Pro distribuce Linuxu, které nepodporují šifrování protokolu SMB 3.0, použijte **2.1** pro **< smb_version >** . Je pouze možné připojit sdílenou složku Azure mimo oblast Azure (včetně místní nebo v jiné oblasti Azure) s protokolem SMB 3.0.

    ```bash
    sudo bash -c 'echo "//<STORAGE ACCOUNT NAME>.file.core.windows.net/<FILE SHARE NAME> /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME> cifs nofail,vers=3.0,credentials=/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'

    sudo mount /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME>
    ```

> [!Note]  
> Můžete použít `sudo mount -a` připojit sdílenou složku Azure po úpravě `/etc/fstab` místo restartování.

## <a name="feedback"></a>Váš názor

Uživatelé Linuxu, chceme znát váš názor!

Soubory Azure pro skupinu uživatelů Linuxu poskytuje diskusní fórum pro můžete sdílet zpětnou vazbu, jak vyhodnotit a přijmout úložiště souborů v Linuxu. E-mailu [uživatelů Linuxu soubory Azure](mailto:azurefileslinuxusers@microsoft.com) připojení ke skupině uživatelů.

## <a name="next-steps"></a>Další postup

Další informace o službě Soubory Azure najdete na těchto odkazech:

* [Plánování nasazení služby Soubory Azure](storage-files-planning.md)
* [Nejčastější dotazy](../storage-files-faq.md)
* [Odstraňování potíží](storage-troubleshoot-linux-file-connection-problems.md)

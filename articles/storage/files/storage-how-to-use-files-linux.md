---
title: Použití souborů Azure se systémem Linux | Microsoft Docs
description: Zjistěte, jak připojit sdílenou složku Azure přes protokol SMB v systému Linux.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 03/29/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 06df5d403ba10489ea9a36a79a94f4b94782e4ef
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501330"
---
# <a name="use-azure-files-with-linux"></a>Použití služby Soubory Azure s Linuxem

Služba [Soubory Azure](storage-files-introduction.md) je snadno použitelný cloudový systém souborů od Microsoftu. Sdílené složky Azure je možné připojit v rámci distribucí systému Linux pomocí [klienta jádra protokolu SMB](https://wiki.samba.org/index.php/LinuxCIFS). Tento článek ukazuje dva způsoby, jak připojit sdílenou složku Azure: na vyžádání pomocí `mount` příkazu a po spuštění vytvořením položky v. `/etc/fstab`

> [!NOTE]  
> Aby bylo možné připojit sdílenou složku Azure mimo oblast, ve které je hostovaná, například v místním prostředí nebo v jiné oblasti Azure, operační systém musí podporovat funkce šifrování SMB 3,0.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Požadavky pro připojení sdílené složky Azure se systémem Linux a balíčkem CIFS-util
<a id="smb-client-reqs"></a>

* **Existující účet úložiště Azure a sdílená složka**: K dokončení tohoto článku potřebujete účet úložiště a sdílení souborů. Pokud jste ho ještě nevytvořili, přečtěte si některé z našich rychlých startů v předmětu: [Vytvoření sdílených složek – rozhraní příkazového řádku](storage-how-to-use-files-cli.md)

* **Název a klíč vašeho účtu úložiště** K dokončení tohoto článku budete potřebovat název a klíč účtu úložiště. Pokud jste ho vytvořili pomocí příkazu k rychlému startu pro rozhraní příkazového řádku, můžete ho už mít, jinak se můžete obrátit na rychlý Start CLI, který byl dříve propojen, aby se zjistilo, jak načíst klíč účtu úložiště.

* **Vyberte distribuci Linux, aby vyhovovala vašim potřebám připojení.**  
      Soubory Azure je možné připojit buď prostřednictvím protokolu SMB 2,1 a SMB 3,0. U připojení přicházejících z místních klientů nebo v jiných oblastech Azure je nutné použít protokol SMB 3,0; Soubory Azure odmítnou protokol SMB 2,1 (nebo SMB 3,0 bez šifrování). Pokud ke sdílené složce Azure přistupujete z virtuálního počítače ve stejné oblasti Azure, můžete ke sdílené složce přistupovat pomocí protokolu SMB 2,1, pokud je to jenom v případě, že je pro účet úložiště hostující sdílenou složku Azure zakázaný *požadovaný zabezpečený přenos* . Vždycky doporučujeme, abyste vyžadovali zabezpečený přenos a používali jenom SMB 3,0 se šifrováním.

    Podpora šifrování protokolu SMB 3,0 byla představena v jádru Linux verze 4,11 a byla pro oblíbená distribuce systému Linux znovu naportovaná na starší verze jádra. V době publikace tohoto dokumentu následující distribuce z Galerie Azure podporuje možnost připojení určenou v záhlavích tabulky. 

### <a name="minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30"></a>Minimální doporučené verze s odpovídajícími možnostmi připojení (SMB verze 2,1 vs SMB verze 3,0)

|   | SMB 2.1 <br>(Připojení k virtuálním počítačům ve stejné oblasti Azure) | SMB 3.0 <br>(Připojení z místního prostředí a mezi oblastmi) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04 + | 16.04 + |
| RHEL | 7 + | 7.5+ |
| CentOS | 7 + |  7.5+ |
| Debian | 8+ |   |
| openSUSE | 13.2 + | 42.3 + |
| SUSE Linux Enterprise Server | 12 | 12 SP3+ |

Pokud zde není uvedená distribuce systému Linux, můžete zjistit verzi jádra systému Linux pomocí následujícího příkazu:

```bash
uname -r
```

* <a id="install-cifs-utils"></a>**Balíček CIFS-utils se nainstaluje.**  
    Balíček CIFS-utils se dá nainstalovat pomocí Správce balíčků na distribuci v systému Linux podle vašeho výběru. 

    V distribucích založených na **Ubuntu** a **Debian** použijte `apt-get` správce balíčků:

    ```bash
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    V **RHEL** a **CentOS**použijte `yum` správce balíčků:

    ```bash
    sudo yum install cifs-utils
    ```

    V **openSUSE**použijte `zypper` správce balíčků:

    ```bash
    sudo zypper install cifs-utils
    ```

    V ostatních distribucích použijte příslušného správce balíčků nebo [zkompilujte ze zdroje](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download) .

* **Určete oprávnění k adresáři nebo souboru připojené sdílené složky**: V níže uvedených příkladech se oprávnění `0777` používá k udělení oprávnění ke čtení, zápisu a spouštění pro všechny uživatele. V případě potřeby ho můžete nahradit dalšími [chmod oprávněními](https://en.wikipedia.org/wiki/Chmod) , i když to znamená, že bude potenciálně omezovat přístup. Pokud použijete jiná oprávnění, měli byste zvážit také použití identifikátorů UID a GID, aby bylo možné zachovat přístup pro místní uživatele a skupiny podle vašeho výběru.

> [!NOTE]
> Pokud explicitně nepřiřadíte oprávnění k adresáři a souboru pomocí dir_mode a file_mode, budou standardně 0755.

* **Ujistěte se, že je port 445 otevřený**: Protokol SMB komunikuje přes protokol TCP 445 – zkontrolujte, že brána firewall neblokuje port TCP 445 z klientského počítače.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Připojení sdílené složky Azure na vyžádání pomocí`mount`

1. **[Nainstalujte balíček CIFS-utils pro distribuci systému Linux](#install-cifs-utils)** .

1. **Vytvořte složku pro přípojný bod**: Složku pro přípojný bod lze vytvořit kdekoli v systému souborů, ale je to společná konvence pro vytvoření této nové složky. Například následující příkaz vytvoří nový adresář a nahradí **< storage_account_name >** a **< file_share_name >** s příslušnými informacemi pro vaše prostředí:

    ```bash
    mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **Připojte sdílenou složku Azure pomocí příkazu připojit**: Nezapomeňte nahradit **< storage_account_name >** , **< název_sdílené_složky >** , **< smb_version >** , **< storage_account_key >** a **< mount_point >** s příslušnými informacemi pro váš hlediska. Pokud vaše distribuce systému Linux podporuje protokol SMB 3,0 s šifrováním (Další informace najdete v tématu [Vysvětlení požadavků klienta SMB](#smb-client-reqs) ), použijte **3,0** pro **< smb_version >** . U distribucí systému Linux, které nepodporují protokol SMB 3,0 se šifrováním, použijte **2,1** pro **< smb_version >** . Sdílenou složku Azure je možné připojit pouze mimo oblast Azure (včetně místních nebo v jiné oblasti Azure) s protokolem SMB 3,0. Pokud chcete, můžete změnit oprávnění adresáře a souboru připojené sdílené složky, ale to by znamenalo omezení přístupu.

    ```bash
    sudo mount -t cifs //<storage_account_name>.file.core.windows.net/<share_name> <mount_point> -o vers=<smb_version>,username=<storage_account_name>,password=<storage_account_key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Až budete s použitím sdílené složky Azure hotovi, můžete ji použít `sudo umount <mount_point>` k odpojení sdílené složky.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Vytvoření trvalého přípojného bodu pro sdílenou složku Azure s`/etc/fstab`

1. **[Nainstalujte balíček CIFS-utils pro distribuci systému Linux](#install-cifs-utils)** .

1. **Vytvořte složku pro přípojný bod**: Složku pro přípojný bod lze vytvořit kdekoli v systému souborů, ale je to společná konvence pro vytvoření této nové složky. Bez ohledu na to, kde to vytvoříte, si poznamenejte absolutní cestu ke složce. Například následující příkaz vytvoří nový adresář a nahradí **< storage_account_name >** a **< file_share_name >** s příslušnými informacemi pro vaše prostředí.

    ```bash
    sudo mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **Vytvořte soubor přihlašovacích údajů pro uložení uživatelského jména (název účtu úložiště) a heslo (klíč účtu úložiště) pro sdílenou složku.** Nahraďte **< storage_account_name >** a **< storage_account_key >** odpovídajícími informacemi pro vaše prostředí.

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
    fi
    if [ ! -f "/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred" ]; then
    sudo bash -c 'echo "username=<STORAGE ACCOUNT NAME>" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    sudo bash -c 'echo "password=7wRbLU5ea4mgc<DRIVE LETTER>PIpUCNcuG9gk2W4S2tv7p0cTm62wXTK<DRIVE LETTER>CgJlBJPKYc4VMnwhyQd<DRIVE LETTER>UT<DRIVE LETTER>yR5/RtEHyT/EHtg2Q==" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    fi
    ```

1. **Změňte oprávnění k souboru přihlašovacích údajů, aby bylo možné číst nebo upravovat soubor hesla jenom rootem.** Vzhledem k tomu, že klíč účtu úložiště je v podstatě heslem správce účtu úložiště, je třeba nastavit oprávnění k souboru tak, aby přístup k hlavnímu adresáři měl jenom kořenový, aby uživatelé nižších oprávnění nemohli získat klíč účtu úložiště.   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage_account_name>.cred
    ```

1. Následující **příkaz použijte k připojení `/etc/fstab`následujícího řádku k** : Nezapomeňte nahradit **< storage_account_name >** , **< název_sdílené_složky >** , **< smb_version >** a **< mount_point >** s příslušnými informacemi pro vaše prostředí. Pokud vaše distribuce systému Linux podporuje protokol SMB 3,0 s šifrováním (Další informace najdete v tématu [Vysvětlení požadavků klienta SMB](#smb-client-reqs) ), použijte **3,0** pro **< smb_version >** . U distribucí systému Linux, které nepodporují protokol SMB 3,0 se šifrováním, použijte **2,1** pro **< smb_version >** . Sdílenou složku Azure je možné připojit pouze mimo oblast Azure (včetně místních nebo v jiné oblasti Azure) s protokolem SMB 3,0.

    ```bash
    sudo bash -c 'echo "//<STORAGE ACCOUNT NAME>.file.core.windows.net/<FILE SHARE NAME> /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME> cifs _netdev,nofail,vers=3.0,credentials=/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'

    sudo mount /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME>
    ```

> [!Note]  
> Pomocí `sudo mount -a` nástroje můžete po úpravě `/etc/fstab` připojit sdílenou složku Azure, nikoli restartování.

## <a name="feedback"></a>Zpětná vazba

Uživatelé systému Linux, chceme vás od vás.

Skupina uživatelé služby soubory Azure pro Linux poskytuje fórum pro sdílení zpětné vazby při vyhodnocování a přijímání úložiště souborů na platformě Linux. Zapojte [uživatele systému Azure soubory Linux](mailto:azurefileslinuxusers@microsoft.com) , aby se připojili ke skupině uživatelů.

## <a name="next-steps"></a>Další kroky

Další informace o službě Soubory Azure najdete na těchto odkazech:

* [Plánování nasazení služby Soubory Azure](storage-files-planning.md)
* [Nejčastější dotazy](../storage-files-faq.md)
* [Odstraňování potíží](storage-troubleshoot-linux-file-connection-problems.md)

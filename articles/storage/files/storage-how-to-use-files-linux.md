---
title: Použití souborů Azure se systémem Linux | Microsoft Docs
description: Zjistěte, jak připojit sdílenou složku Azure přes protokol SMB v systému Linux.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b2469a2b5819b3011f919a2b483933bb030eed70
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925257"
---
# <a name="use-azure-files-with-linux"></a>Použití služby Soubory Azure s Linuxem
Služba [Soubory Azure](storage-files-introduction.md) je snadno použitelný cloudový systém souborů od Microsoftu. Sdílené složky Azure je možné připojit v rámci distribucí systému Linux pomocí [klienta jádra protokolu SMB](https://wiki.samba.org/index.php/LinuxCIFS). Tento článek ukazuje dva způsoby, jak připojit sdílenou složku Azure: na vyžádání pomocí příkazu `mount` a spuštění po vytvoření položky v `/etc/fstab`.

Doporučený způsob, jak připojit sdílenou složku Azure v systému Linux, je použití protokolu SMB 3,0. Ve výchozím nastavení služba soubory Azure vyžaduje šifrování při přenosu, který podporuje jenom SMB 3,0. Soubory Azure také podporují protokol SMB 2,1, který nepodporuje šifrování při přenosu, ale sdílené složky Azure se službou SMB 2,1 nemůžete z jiných oblastí Azure nebo z místního prostředí připojit z důvodů zabezpečení. Pokud vaše aplikace konkrétně nevyžaduje protokol SMB 2,1, existuje málo důvodů, proč byste ji měli použít od většiny oblíbených, nedávno vydaných distribucí Linux podporuje SMB 3,0:  

| | SMB 2.1 <br>(Připojení k virtuálním počítačům ve stejné oblasti Azure) | SMB 3.0 <br>(Připojení z místního prostředí a mezi oblastmi) |
| --- | :---: | :---: |
| Ubuntu | 14.04 + | 16.04 + |
| Red Hat Enterprise Linux (RHEL) | 7 + | 7.5+ |
| CentOS | 7 + |  7.5+ |
| Debian | 8+ | 10+ |
| openSUSE | 13.2 + | 42.3+ |
| SUSE Linux Enterprise Server | 12+ | 12 SP3+ |

Pokud používáte distribuci systému Linux, která není uvedená v předchozí tabulce, můžete zjistit, jestli vaše distribuce systému Linux podporuje protokol SMB 3,0 se šifrováním, a to kontrolou verze jádra systému Linux. SMB 3,0 se šifrováním bylo přidáno do jádra Linux verze 4,11. Příkaz `uname` vrátí verzi operačního systému Linux, která se používá:

```bash
uname -r
```

## <a name="prerequisites"></a>Předpoklady
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**Ujistěte se, že je nainstalovaný balíček CIFS-util.**  
    Balíček CIFS-utils se dá nainstalovat pomocí Správce balíčků na distribuci v systému Linux podle vašeho výběru. 

    V distribucích **založených** na **Ubuntu** a Debian použijte Správce balíčků `apt`:

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    V **Fedora** **Red Hat Enterprise Linux 8 +** a **CentOS 8 +** použijte Správce balíčků `dnf`:

    ```bash
    sudo dnf install cifs-utils
    ```

    Ve starších verzích **Red Hat Enterprise Linux** a **CentOS**použijte Správce balíčků `yum`:

    ```bash
    sudo yum install cifs-utils 
    ```

    V **openSUSE**použijte správce balíčků `zypper`:

    ```bash
    sudo zypper install cifs-utils
    ```

    V ostatních distribucích použijte příslušného správce balíčků nebo [zkompilujte ze zdroje](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download) .

* **Nejnovější verze rozhraní příkazového řádku Azure (CLI).** Další informace o tom, jak nainstalovat rozhraní příkazového řádku Azure, najdete v tématu [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a výběr operačního systému. Pokud upřednostňujete použití modulu Azure PowerShell v prostředí PowerShell 6 +, můžete si nicméně níže uvedené pokyny předkládat Azure CLI.

* **Ujistěte se, že je otevřený port 445**: SMB komunikuje přes port TCP 445 – zkontrolujte, jestli brána firewall neblokuje porty TCP 445 z klientského počítače.  Nahraďte **< > vaší-Resource-Group** a **< účtu úložiště >**
    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $smbPath | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    Pokud bylo připojení úspěšné, měla by se zobrazit něco podobného jako u následujícího výstupu:

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    Pokud nemůžete otevřít port 445 ve vaší podnikové síti nebo pokud ho zabrání poskytovatel internetových služeb, můžete použít připojení k síti VPN nebo ExpressRoute k tomu, abyste mohli pracovat s portem 445. Další informace najdete v tématu [požadavky na síť pro přímý přístup ke sdílení souborů Azure](storage-files-networking-overview.md).

## <a name="mounting-azure-file-share"></a>Připojení sdílené složky Azure
Pokud chcete použít sdílenou složku Azure s distribucí systému Linux, musíte vytvořit adresář, který bude sloužit jako přípojný bod pro sdílenou složku Azure. Přípojný bod se dá vytvořit kdekoli na svém systému Linux, ale je to obvyklá konvence, kterou můžete vytvořit v rámci/mnt. Za přípojný bod použijete příkaz `mount` pro přístup ke sdílené složce Azure.

V případě potřeby můžete stejnou sdílenou složku Azure připojit k několika přípojným bodům.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Připojení sdílené složky Azure na vyžádání pomocí `mount`
1. **Vytvořte složku pro přípojný bod**: nahraďte `<your-resource-group>`, `<your-storage-account>`a `<your-file-share>` odpovídajícími informacemi pro vaše prostředí:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Připojte sdílenou složku Azure pomocí příkazu připojit**. V následujícím příkladu má místní systém Linux oprávnění k souborům a složkám standardně 0755, což znamená čtení, zápis a spouštění pro vlastníka (na základě souboru/adresáře Linux Owner), čtení a spouštění pro uživatele ve skupině vlastník a pro ostatní v systému. K nastavení ID uživatele a ID skupiny pro připojení můžete použít možnosti připojení `uid` a `gid`. Pomocí `dir_mode` a `file_mode` můžete také nastavit vlastní oprávnění podle potřeby. Další informace o tom, jak nastavit oprávnění, najdete v tématu [Číselná notace pro UNIX](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) v Wikipedii. 

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')

    sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
    ```

    > [!Note]  
    > Výše uvedený příkaz Mount se připojuje pomocí protokolu SMB 3,0. Pokud distribuce systému Linux nepodporuje protokol SMB 3,0 s šifrováním, nebo pokud podporuje pouze protokol SMB 2,1, můžete se připojit pouze k virtuálnímu počítači Azure ve stejné oblasti jako účet úložiště. Pokud chcete sdílenou složku Azure připojit k distribuci v systému Linux, která nepodporuje protokol SMB 3,0 se šifrováním, budete muset [zakázat šifrování při přenosu pro účet úložiště](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Po dokončení používání sdílené složky Azure můžete k odpojení sdílené složky použít `sudo umount $mntPath`.

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Vytvořte trvalý přípojný bod pro sdílenou složku Azure pomocí `/etc/fstab`
1. **Vytvoření složky pro přípojný bod**: složku pro přípojný bod lze vytvořit kdekoli v systému souborů, ale je to obvyklá konvence, kterou můžete vytvořit v rámci/mnt. Například následující příkaz vytvoří nový adresář, nahradí `<your-resource-group>`, `<your-storage-account>`a `<your-file-share>` odpovídající informace pro vaše prostředí:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Vytvořte soubor přihlašovacích údajů pro uložení uživatelského jména (název účtu úložiště) a heslo (klíč účtu úložiště) pro sdílenou složku.** 

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
        sudo mkdir "/etc/smbcredentials"
    fi

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')
    
    smbCredentialFile="/etc/smbcredentials/$storageAccountName.cred"
    if [ ! -f $smbCredentialFile ]; then
        echo "username=$storageAccountName" | sudo tee $smbCredentialFile > /dev/null
        echo "password=$storageAccountKey" | sudo tee -a $smbCredentialFile > /dev/null
    else 
        echo "The credential file $smbCredentialFile already exists, and was not modified."
    fi
    ```

1. **Změňte oprávnění k souboru přihlašovacích údajů, aby bylo možné číst nebo upravovat soubor hesla jenom rootem.** Vzhledem k tomu, že klíč účtu úložiště je v podstatě heslem správce účtu úložiště, je třeba nastavit oprávnění k souboru tak, aby přístup k hlavnímu adresáři měl jenom kořenový, aby uživatelé nižších oprávnění nemohli získat klíč účtu úložiště.   

    ```bash
    sudo chmod 600 $smbCredentialFile
    ```

1. **Pomocí následujícího příkazu přidejte do `/etc/fstab`následující řádek** : v následujícím příkladu má místní soubor operačního systému Linux výchozí oprávnění 0755, což znamená čtení, zápis a spouštění pro vlastníka (na základě souboru/adresáře Linux Owner), čtení a spouštění pro uživatele ve skupině vlastník a čtení a spouštění pro ostatní systémy. K nastavení ID uživatele a ID skupiny pro připojení můžete použít možnosti připojení `uid` a `gid`. Pomocí `dir_mode` a `file_mode` můžete také nastavit vlastní oprávnění podle potřeby. Další informace o tom, jak nastavit oprávnění, najdete v tématu [Číselná notace pro UNIX](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) v Wikipedii.

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    if [ -z "$(grep $smbPath\ $mntPath /etc/fstab)" ]; then
        echo "$smbPath $mntPath cifs nofail,vers=3.0,credentials=$smbCredentialFile,serverino" | sudo tee -a /etc/fstab > /dev/null
    else
        echo "/etc/fstab was not modified to avoid conflicting entries as this Azure file share was already present. You may want to double check /etc/fstab to ensure the configuration is as desired."
    fi

    sudo mount -a
    ```
    
    > [!Note]  
    > Výše uvedený příkaz Mount se připojuje pomocí protokolu SMB 3,0. Pokud distribuce systému Linux nepodporuje protokol SMB 3,0 s šifrováním, nebo pokud podporuje pouze protokol SMB 2,1, můžete se připojit pouze k virtuálnímu počítači Azure ve stejné oblasti jako účet úložiště. Pokud chcete sdílenou složku Azure připojit k distribuci v systému Linux, která nepodporuje protokol SMB 3,0 se šifrováním, budete muset [zakázat šifrování při přenosu pro účet úložiště](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="securing-linux"></a>Zabezpečení systému Linux
Aby bylo možné připojit sdílenou složku Azure v systému Linux, musí být port 445 přístupný. Řada organizací port 445 blokuje kvůli bezpečnostním rizikům spojeným s protokolem SMB 1. SMB 1, označované taky jako CIFS (Common Internet File System), je starší protokol systému souborů, který je součástí mnoha distribucí pro Linux. Protokol SMB 1 je zastaralý, neefektivní a hlavně nezabezpečený protokol. Dobrá zpráva je, že soubory Azure nepodporují protokol SMB 1 a počínaje jádrem Linux verze 4,18, Linux umožňuje zakázat protokol SMB 1. Předtím, než použijete sdílené složky SMB v produkčním prostředí, [důrazně doporučujeme](https://aka.ms/stopusingsmb1) zakázat protokol SMB 1 v klientech se systémem Linux.

Od verze Linux kernel 4,18 se modul jádra SMB, který se pro starší důvody navolal `cifs`, vystaví nový parametr modulu (často se označuje jako *parametr* o různé externí dokumentaci), která se nazývá `disable_legacy_dialects`. I když se zavedlo v jádře jádra 4,18, někteří dodavatelé tuto změnu nastavili na starší jádra, kterou podporují. V následující tabulce najdete informace o dostupnosti tohoto parametru modulu u běžných distribucí systému Linux.

| Distribuce | Může zakázat protokol SMB 1. |
|--------------|-------------------|
| Ubuntu 14.04 – 16.04 | Ne |
| Ubuntu 18.04 | Ano |
| Ubuntu 19.04 + | Ano |
| Debian 8-9 | Ne |
| Debian 10 + | Ano |
| Fedora 29 + | Ano |
| CentOS 7 | Ne | 
| CentOS 8 + | Ano |
| Red Hat Enterprise Linux 6. x-7. x | Ne |
| Red Hat Enterprise Linux 8 + | Ano |
| openSUSE, přestupné 15,0 | Ne |
| openSUSE přestupné 15.1 + | Ano |
| openSUSE Tumbleweed | Ano |
| SUSE Linux Enterprise 11. x-12. x | Ne |
| SUSE Linux Enterprise 15 | Ne |
| SUSE Linux Enterprise 15,1 | Ne |

Pomocí následujícího příkazu můžete zjistit, jestli vaše distribuce systému Linux podporuje parametr `disable_legacy_dialects` modul.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

Tento příkaz by měl mít výstup následující zprávy:

```Output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

Než protokol SMB 1 zakážete, musíte se ujistit, že modul SMB není v systému aktuálně načtený (k tomu dojde automaticky v případě, že jste připojili sdílenou složku SMB). Můžete to provést pomocí následujícího příkazu, který by neměl být vypsán, pokud není načten protokol SMB:

```bash
lsmod | grep cifs
```

Chcete-li uvolnit modul, nejprve odpojte všechny sdílené složky SMB (pomocí příkazu `umount`, jak je popsáno výše). Všechny připojené sdílené složky SMB v systému můžete identifikovat pomocí následujícího příkazu:

```bash
mount | grep cifs
```

Jakmile odpojíte všechny sdílené složky SMB, je možné modul uvolnit. Můžete to provést pomocí příkazu `modprobe`:

```bash
sudo modprobe -r cifs
```

Modul můžete ručně načíst pomocí protokolu SMB 1 uvolněného pomocí příkazu `modprobe`:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Nakonec můžete ověřit, že se modul SMB načetl s parametrem, a to tak, že se podíváte na načtené parametry v `/sys/module/cifs/parameters`:

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Chcete-li trvale zakázat protokol SMB 1 v distribucích založených na Ubuntu a Debian, je nutné vytvořit nový soubor (Pokud ještě nemáte vlastní možnosti pro jiné moduly) s názvem `/etc/modprobe.d/local.conf` s nastavením. Můžete to provést pomocí následujícího příkazu:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

To, jestli se to osvědčilo, můžete ověřit tak, že načtete modul SMB:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="feedback"></a>Váš názor
Uživatelé systému Linux, chceme vás od vás.

Skupina uživatelé služby soubory Azure pro Linux poskytuje fórum pro sdílení zpětné vazby při vyhodnocování a přijímání úložiště souborů na platformě Linux. Zapojte [uživatele systému Azure soubory Linux](mailto:azurefiles@microsoft.com) , aby se připojili ke skupině uživatelů.

## <a name="next-steps"></a>Další kroky
Další informace o službě Soubory Azure najdete na těchto odkazech:

* [Plánování nasazení služby Soubory Azure](storage-files-planning.md)
* [Nejčastější dotazy](../storage-files-faq.md)
* [Řešení potíží](storage-troubleshoot-linux-file-connection-problems.md)

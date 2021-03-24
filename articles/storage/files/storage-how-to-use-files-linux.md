---
title: Použití souborů Azure se systémem Linux | Microsoft Docs
description: Zjistěte, jak připojit sdílenou složku Azure přes protokol SMB v systému Linux. Podívejte se na seznam požadavků. Projděte si otázky zabezpečení protokolu SMB pro klienty se systémem Linux.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4ace5620bf98b06956c294a12b6b08881422e718
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952333"
---
# <a name="use-azure-files-with-linux"></a>Použití služby Soubory Azure s Linuxem
Služba [Soubory Azure](storage-files-introduction.md) je snadno použitelný cloudový systém souborů od Microsoftu. Sdílené složky Azure je možné připojit v rámci distribucí systému Linux pomocí [klienta jádra protokolu SMB](https://wiki.samba.org/index.php/LinuxCIFS). Tento článek ukazuje dva způsoby, jak připojit sdílenou složku Azure: na vyžádání pomocí `mount` příkazu a po spuštění vytvořením položky v `/etc/fstab` .

Doporučený způsob, jak připojit sdílenou složku Azure v systému Linux, je použití protokolu SMB 3,0. Ve výchozím nastavení služba soubory Azure vyžaduje šifrování při přenosu, který podporuje jenom SMB 3,0. Soubory Azure také podporují protokol SMB 2,1, který nepodporuje šifrování při přenosu, ale sdílené složky Azure se službou SMB 2,1 nemůžete z jiných oblastí Azure nebo z místního prostředí připojit z důvodů zabezpečení. Pokud vaše aplikace konkrétně nevyžaduje protokol SMB 2,1, existuje málo důvodů, proč byste ji měli použít od většiny oblíbených, nedávno vydaných distribucí Linux podporuje SMB 3,0:  

| Distribuce systému Linux | SMB 2.1 <br>(Připojení k virtuálním počítačům ve stejné oblasti Azure) | SMB 3.0 <br>(Připojení z místního prostředí a mezi oblastmi) |
| --- | :---: | :---: |
| Ubuntu | 14.04 + | 16.04 + |
| Red Hat Enterprise Linux (RHEL) | 7 + | 7.5 + |
| CentOS | 7 + |  7.5 + |
| Debian | 8 + | 10+ |
| openSUSE | 13.2 + | 42.3 + |
| SUSE Linux Enterprise Server | 12+ | 12 SP2 + |

Pokud používáte distribuci systému Linux, která není uvedená v předchozí tabulce, můžete zjistit, jestli vaše distribuce systému Linux podporuje protokol SMB 3,0 se šifrováním, a to kontrolou verze jádra systému Linux. SMB 3,0 se šifrováním bylo přidáno do jádra Linux verze 4,11. `uname`Příkaz vrátí verzi používaného jádra systému Linux:

```bash
uname -r
```

## <a name="prerequisites"></a>Předpoklady
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**Ujistěte se, že je nainstalovaný balíček CIFS-util.**  
    Balíček CIFS-utils se dá nainstalovat pomocí Správce balíčků na distribuci v systému Linux podle vašeho výběru. 

    V distribucích **založených** na **Ubuntu** a Debian použijte `apt` Správce balíčků:

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    V **Fedora** **Red Hat Enterprise Linux 8 +** a **CentOS 8 +** použijte `dnf` Správce balíčků:

    ```bash
    sudo dnf install cifs-utils
    ```

    Ve starších verzích **Red Hat Enterprise Linux** a **CentOS** použijte `yum` Správce balíčků:

    ```bash
    sudo yum install cifs-utils 
    ```

    V **openSUSE** použijte `zypper` Správce balíčků:

    ```bash
    sudo zypper install cifs-utils
    ```

    V ostatních distribucích použijte příslušného správce balíčků nebo [zkompilujte ze zdroje](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download) .

* **Nejnovější verze rozhraní příkazového řádku Azure (CLI).** Další informace o tom, jak nainstalovat rozhraní příkazového řádku Azure, najdete v tématu [instalace rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) a výběr operačního systému. Pokud upřednostňujete použití modulu Azure PowerShell v prostředí PowerShell 6 +, můžete si nicméně níže uvedené pokyny předkládat Azure CLI.

* **Ujistěte se, že je otevřený port 445**: SMB komunikuje přes port TCP 445 – zkontrolujte, jestli brána firewall neblokuje porty TCP 445 z klientského počítače.  Nahraďte `<your-resource-group>` a `<your-storage-account>` pak spusťte následující skript:
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

    ```ouput
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    Pokud nemůžete otevřít port 445 ve vaší podnikové síti nebo pokud ho zabrání poskytovatel internetových služeb, můžete použít připojení k síti VPN nebo ExpressRoute k tomu, abyste mohli pracovat s portem 445. Další informace najdete v tématu [požadavky na síť pro přímý přístup ke sdílení souborů Azure](storage-files-networking-overview.md)..

## <a name="mounting-azure-file-share"></a>Připojení sdílené složky Azure
Pokud chcete použít sdílenou složku Azure s distribucí systému Linux, musíte vytvořit adresář, který bude sloužit jako přípojný bod pro sdílenou složku Azure. Přípojný bod se dá vytvořit kdekoli na svém systému Linux, ale je to obvyklá konvence, kterou můžete vytvořit v rámci/Mount.. Za přípojný bod použijete `mount` příkaz pro přístup ke sdílené složce Azure.

V případě potřeby můžete stejnou sdílenou složku Azure připojit k několika přípojným bodům.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Připojení sdílené složky Azure na vyžádání pomocí `mount`
1. **Vytvořte složku pro přípojný bod**: Nahraďte `<your-resource-group>` , `<your-storage-account>` a `<your-file-share>` příslušnými informacemi pro vaše prostředí:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mount/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Připojte sdílenou složku Azure pomocí příkazu připojit**. V následujícím příkladu má místní systém Linux oprávnění k souborům a složkám standardně 0755, což znamená čtení, zápis a spouštění pro vlastníka (na základě souboru/adresáře Linux Owner), čtení a spouštění pro uživatele ve skupině vlastník a pro ostatní v systému. `uid` `gid` K nastavení ID uživatele a ID skupiny pro připojení můžete použít možnosti a připojení. Můžete také použít `dir_mode` a `file_mode` k nastavení vlastních oprávnění podle potřeby. Další informace o tom, jak nastavit oprávnění, najdete v tématu [Číselná notace pro UNIX](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) v Wikipedii. 

    ```bash
    # This command assumes you have logged in with az login
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

Až budete s použitím sdílené složky Azure hotovi, můžete ji použít `sudo umount $mntPath` k odpojení sdílené složky.

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Vytvoření trvalého přípojného bodu pro sdílenou složku Azure s `/etc/fstab`
1. **Vytvoření složky pro přípojný bod**: složku pro přípojný bod lze vytvořit kdekoli v systému souborů, ale je to obvyklá konvence, kterou můžete vytvořit v rámci/Mount.. Například následující příkaz vytvoří nový adresář, nahradí `<your-resource-group>` , `<your-storage-account>` a `<your-file-share>` s příslušnými informacemi pro vaše prostředí:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mount/$storageAccountName/$fileShareName"

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

1. Následující **příkaz použijte k připojení následujícího řádku k `/etc/fstab`**: v následujícím příkladu má místní soubory a složky pro linux výchozí hodnotu 0755, což znamená čtení, zápis a spouštění pro vlastníka (na základě souboru/adresáře Linux Owner), čtení a spouštění pro uživatele ve skupině vlastník a pro ostatní v systému. `uid` `gid` K nastavení ID uživatele a ID skupiny pro připojení můžete použít možnosti a připojení. Můžete také použít `dir_mode` a `file_mode` k nastavení vlastních oprávnění podle potřeby. Další informace o tom, jak nastavit oprávnění, najdete v tématu [Číselná notace pro UNIX](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) v Wikipedii.

    ```bash
    # This command assumes you have logged in with az login
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

### <a name="using-autofs-to-automatically-mount-the-azure-file-shares"></a>Automatické připojení sdílených složek Azure pomocí AutoFS

1. **Ujistěte se, že je nainstalovaný balíček autofs.**  

    Balíček AutoFS se dá nainstalovat pomocí Správce balíčků na distribuci v systému Linux podle vašeho výběru. 

    V distribucích **založených** na **Ubuntu** a Debian použijte `apt` Správce balíčků:
    ```bash
    sudo apt update
    sudo apt install autofs
    ```
    V **Fedora** **Red Hat Enterprise Linux 8 +** a **CentOS 8 +** použijte `dnf` Správce balíčků:
    ```bash
    sudo dnf install autofs
    ```
    Ve starších verzích **Red Hat Enterprise Linux** a **CentOS** použijte `yum` Správce balíčků:
    ```bash
    sudo yum install autofs 
    ```
    V **openSUSE** použijte `zypper` Správce balíčků:
    ```bash
    sudo zypper install autofs
    ```
2. **Vytvořte přípojný bod pro sdílené složky**:
   ```bash
    sudo mkdir /fileshares
    ```
3. **Crete nový vlastní konfigurační soubor AutoFS**
    ```bash
    sudo vi /etc/auto.fileshares
    ```
4. **Přidat následující položky do/etc/auto.fileshares**
   ```bash
   echo "$fileShareName -fstype=cifs,credentials=$smbCredentialFile :$smbPath"" > /etc/auto.fileshares
   ```
5. **Přidejte následující položku do/etc/auto.Master**
   ```bash
   /fileshares /etc/auto.fileshares --timeout=60
   ```
6. **Restartovat AutoFS**
    ```bash
    sudo systemctl restart autofs
    ```
7.  **Přístup ke složce určené pro sdílenou složku**
    ```bash
    cd /fileshares/$filesharename
    ```
## <a name="securing-linux"></a>Zabezpečení systému Linux
Aby bylo možné připojit sdílenou složku Azure v systému Linux, musí být port 445 přístupný. Řada organizací port 445 blokuje kvůli bezpečnostním rizikům spojeným s protokolem SMB 1. SMB 1, označované taky jako CIFS (Common Internet File System), je starší protokol systému souborů, který je součástí mnoha distribucí pro Linux. Protokol SMB 1 je zastaralý, neefektivní a hlavně nezabezpečený protokol. Dobrá zpráva je, že soubory Azure nepodporují protokol SMB 1 a počínaje jádrem Linux verze 4,18, Linux umožňuje zakázat protokol SMB 1. Předtím, než použijete sdílené složky SMB v produkčním prostředí, [důrazně doporučujeme](https://aka.ms/stopusingsmb1) zakázat protokol SMB 1 v klientech se systémem Linux.

Od verze Linux kernel 4,18 se modul jádra SMB, který se volá `cifs` z původních důvodů, vystaví nový parametr modulu (často se označuje jako *parametr* různými externími dokumenty) `disable_legacy_dialects` . I když se zavedlo v jádře jádra 4,18, někteří dodavatelé tuto změnu nastavili na starší jádra, kterou podporují. V následující tabulce najdete informace o dostupnosti tohoto parametru modulu u běžných distribucí systému Linux.

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

Pomocí následujícího příkazu můžete zjistit, jestli vaše distribuce systému Linux podporuje `disable_legacy_dialects` parametr Module.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

Tento příkaz by měl mít výstup následující zprávy:

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

Než protokol SMB 1 zakážete, musíte se ujistit, že modul SMB není v systému aktuálně načtený (k tomu dojde automaticky v případě, že jste připojili sdílenou složku SMB). Můžete to provést pomocí následujícího příkazu, který by neměl být vypsán, pokud není načten protokol SMB:

```bash
lsmod | grep cifs
```

Chcete-li uvolnit modul, nejprve odpojte všechny sdílené složky SMB (pomocí `umount` příkazu popsaného výše). Všechny připojené sdílené složky SMB v systému můžete identifikovat pomocí následujícího příkazu:

```bash
mount | grep cifs
```

Jakmile odpojíte všechny sdílené složky SMB, je možné modul uvolnit. Můžete to provést pomocí příkazu `modprobe`:

```bash
sudo modprobe -r cifs
```

Modul můžete ručně načíst pomocí protokolu SMB 1 uvolněného pomocí `modprobe` příkazu:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Nakonec můžete ověřit, že se modul SMB načetl s parametrem, a to tak, že si prohlédněte načtené parametry v `/sys/module/cifs/parameters` :

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Chcete-li trvale zakázat protokol SMB 1 v distribucích založených na Ubuntu a Debian, je nutné vytvořit nový soubor (Pokud ještě nemáte vlastní možnosti pro jiné moduly) volané `/etc/modprobe.d/local.conf` pomocí tohoto nastavení. Můžete to provést pomocí následujícího příkazu:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

To, jestli se to osvědčilo, můžete ověřit tak, že načtete modul SMB:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="next-steps"></a>Další kroky
Další informace o službě Soubory Azure najdete na těchto odkazech:

* [Plánování nasazení Azure Files](storage-files-planning.md)
* [Nejčastější dotazy](./storage-files-faq.md)
* [Řešení potíží](storage-troubleshoot-linux-file-connection-problems.md)
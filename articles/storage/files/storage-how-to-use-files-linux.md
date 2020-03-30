---
title: Používání souborů Azure s Linuxem | Dokumenty společnosti Microsoft
description: Přečtěte si, jak připojit sdílenou složku Azure přes SMB na Linuxu.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2dc78c25c2cf63a510b9451c8d694795cd8a91eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060941"
---
# <a name="use-azure-files-with-linux"></a>Použití služby Soubory Azure s Linuxem
Služba [Soubory Azure](storage-files-introduction.md) je snadno použitelný cloudový systém souborů od Microsoftu. Sdílené složky Azure lze připojit v linuxových distribucích pomocí [klienta jádra SMB](https://wiki.samba.org/index.php/LinuxCIFS). Tento článek ukazuje dva způsoby připojení sdílené složky `mount` Azure: on-demand s `/etc/fstab`příkazem a na startu vytvořením položky v .

Doporučený způsob připojení sdílené složky Azure na Linuxu používá SMB 3.0. Ve výchozím nastavení vyžadují soubory Azure šifrování při přenosu, které podporuje jenom SMB 3.0. Azure Files také podporuje SMB 2.1, který nepodporuje šifrování při přenosu, ale nemusí připojit sdílené složky Azure s SMB 2.1 z jiné oblasti Azure nebo místní z bezpečnostních důvodů. Pokud vaše aplikace výslovně vyžaduje SMB 2.1, není mnoho důvodů, proč ji používat, protože nejoblíbenější, nedávno vydané distribuce Linuxu podporují SMB 3.0:  

| | SMB 2.1 <br>(Připojení na virtuálních počítačích ve stejné oblasti Azure) | SMB 3.0 <br>(Montuje z místních a mezioblastových) |
| --- | :---: | :---: |
| Ubuntu | 14.04+ | 16.04+ |
| Red Hat Enterprise Linux (RHEL) | 7+ | 7,5+ |
| CentOS | 7+ |  7,5+ |
| Debian | 8+ | 10+ |
| openSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12+ | 12 SP3+ |

Pokud používáte linuxovou distribuci, která není uvedena ve výše uvedené tabulce, můžete zkontrolovat, zda vaše linuxová distribuce podporuje SMB 3.0 s šifrováním kontrolou verze jádra Linuxu. SMB 3.0 s šifrováním byl přidán do jádra Linuxu verze 4.11. Příkaz `uname` vrátí verzi linuxového jádra v provozu:

```bash
uname -r
```

## <a name="prerequisites"></a>Požadavky
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**Ujistěte se, že je nainstalován balíček cifs-utils.**  
    Balíček cifs-utils lze nainstalovat pomocí správce balíčků na linuxové distribuci podle vašeho výběru. 

    Na distribucích založených na `apt` **Ubuntu** a **Debianu** použijte správce balíčků:

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    Na **Fedoři**, **Red Hat Enterprise Linux 8+** a `dnf` **CentOS 8 +** použijte správce balíčků:

    ```bash
    sudo dnf install cifs-utils
    ```

    Ve starších verzích **Red Hat Enterprise Linux** a **CentOS**použijte správce `yum` balíčků:

    ```bash
    sudo yum install cifs-utils 
    ```

    Na **openSUSE**použijte `zypper` správce balíčků:

    ```bash
    sudo zypper install cifs-utils
    ```

    Na jiných distribucích použijte příslušný správce balíčků nebo [kompilujte ze zdroje](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **Nejnovější verze rozhraní Příkazového řádku Azure (CLI).** Další informace o tom, jak nainstalovat příkazové příkazové příkazy KAD Azure, najdete [v tématu Instalace příkazového příkazového příkazu Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a vyberte operační systém. Pokud dáváte přednost použití modulu Azure PowerShell v PowerShellu 6+, můžete, ale níže uvedené pokyny jsou uvedeny pro rozhraní příkazového příkazového příkazu Konady Azure.

* **Ujistěte se, že je otevřený port 445**: SMB komunikuje přes port TCP 445 - zkontrolujte, zda brána firewall neblokuje porty TCP 445 z klientského počítače.  **Nahraďte<>skupiny prostředků** a<>svého účtu **úložiště**
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

    Pokud bylo připojení úspěšné, měli byste vidět něco podobného následujícímu výstupu:

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    Pokud se vám nedaří otevřít port 445 v podnikové síti nebo vám to poskytovatel služby Internetu zablokoval, můžete k obejití portu 445 použít připojení VPN nebo expressroute. Další informace najdete [v tématu Aspekty sítě pro přímý přístup ke sdílené složce Azure](storage-files-networking-overview.md)..

## <a name="mounting-azure-file-share"></a>Připojení sdílené složky Azure
Chcete-li použít sdílenou složku Azure s distribucí Linuxu, musíte vytvořit adresář, který bude sloužit jako přípojný bod pro sdílenou složku Azure. Přípojný bod lze vytvořit kdekoli v systému Linux, ale je běžné konvence k vytvoření pod /mnt. Po přípojné místo, pomocí příkazu `mount` pro přístup ke sdílené složce Azure.

V případě potřeby můžete připojit stejnou sdílenou složku Azure do více přípojkových bodů.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Připojení sdílené složky Azure na vyžádání`mount`
1. **Vytvořte složku pro přípojný bod**: Nahradit `<your-resource-group>`, `<your-storage-account>`a `<your-file-share>` s příslušnými informacemi pro vaše prostředí:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Pomocí příkazu mount připojte sdílenou složku Azure**. V níže uvedeném příkladu je místní linuxová oprávnění k souborům a složkám výchozí 0755, což znamená číst, psát a spouštět pro vlastníka (na základě vlastníka souboru/adresáře Linuxu), číst a spouštět pro uživatele ve skupině vlastníků a číst a spouštět pro ostatní v systému. Pomocí možností `uid` a `gid` připojení můžete nastavit ID uživatele a ID skupiny pro připojení. Můžete také `dir_mode` použít `file_mode` a nastavit vlastní oprávnění podle potřeby. Další informace o nastavení oprávnění naleznete v [unixovém číselném zápisu](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) na Wikipedii. 

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
    > Výše uvedený příkaz mount se připojí s SMB 3.0. Pokud vaše distribuce Linuxu nepodporuje SMB 3.0 s šifrováním nebo pokud podporuje jenom SMB 2.1, můžete připojit pouze z virtuálního počítače Azure ve stejné oblasti jako účet úložiště. Chcete-li připojit sdílenou složku Azure na distribuci Linuxu, která nepodporuje SMB 3.0 s šifrováním, budete muset [zakázat šifrování při přenosu pro účet úložiště](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Po dokončení používání sdílené složky Azure, `sudo umount $mntPath` můžete použít k odpojení sdílené složky.

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Vytvoření trvalého přípojného bodu pro sdílenou složku Azure`/etc/fstab`
1. **Vytvoření složky pro přípojný bod**: Složku pro přípojný bod lze vytvořit kdekoli v systému souborů, ale je běžné, že je to vytvořit pod /mnt. Následující příkaz například vytvoří nový adresář, nahrazuje `<your-resource-group>` `<your-storage-account>`, a `<your-file-share>` s příslušnými informacemi pro vaše prostředí:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Vytvořte soubor pověření pro uložení uživatelského jména (název účtu úložiště) a hesla (klíč účtu úložiště) pro sdílenou složku.** 

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

1. **Změňte oprávnění k souboru pověření, aby soubor s heslem mohl číst nebo upravovat pouze kořenový adresář.** Vzhledem k tomu, že klíč účtu úložiště je v podstatě heslo super správce pro účet úložiště, nastavení oprávnění k souboru tak, aby pouze root přístup je důležité, aby uživatelé s nižšími oprávněními nelze načíst klíč účtu úložiště.   

    ```bash
    sudo chmod 600 $smbCredentialFile
    ```

1. **Pomocí následujícího příkazu připojíte `/etc/fstab`následující řádek k **: V následujícím příkladu je místní linuxový soubor a oprávnění ke složkám výchozí 0755, což znamená číst, psát a spouštět pro vlastníka (na základě vlastníka souboru/adresáře Linuxu), číst a spouštět pro uživatele ve skupině vlastníků a číst a spouštět pro ostatní v systému. Pomocí možností `uid` a `gid` připojení můžete nastavit ID uživatele a ID skupiny pro připojení. Můžete také `dir_mode` použít `file_mode` a nastavit vlastní oprávnění podle potřeby. Další informace o nastavení oprávnění naleznete v [unixovém číselném zápisu](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) na Wikipedii.

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
    > Výše uvedený příkaz mount se připojí s SMB 3.0. Pokud vaše distribuce Linuxu nepodporuje SMB 3.0 s šifrováním nebo pokud podporuje jenom SMB 2.1, můžete připojit pouze z virtuálního počítače Azure ve stejné oblasti jako účet úložiště. Chcete-li připojit sdílenou složku Azure na distribuci Linuxu, která nepodporuje SMB 3.0 s šifrováním, budete muset [zakázat šifrování při přenosu pro účet úložiště](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="securing-linux"></a>Zabezpečení Linuxu
Aby bylo možné připojit sdílené složky Azure na Linuxu, port 445 musí být přístupné. Řada organizací port 445 blokuje kvůli bezpečnostním rizikům spojeným s protokolem SMB 1. SMB 1, také známý jako CIFS (Common Internet File System), je starší souborový systémový protokol, který je součástí mnoha distribucí Linuxu. Protokol SMB 1 je zastaralý, neefektivní a hlavně nezabezpečený protokol. Dobrou zprávou je, že Soubory Azure nepodporuje SMB 1 a počínaje linuxovým jádrem verze 4.18, Linux umožňuje zakázat SMB 1. Vždy [důrazně doporučujeme](https://aka.ms/stopusingsmb1) zakázat SMB 1 na klienty Linuxu před použitím sdílených složek SMB v produkčním prostředí.

Počínaje linuxovým jádrem 4.18 modul jádra SMB, nazývaný `cifs` ze starších důvodů, vystavuje nový parametr modulu (často označovaný jako *parm* různými externími dokumenty), nazývaný `disable_legacy_dialects`. Ačkoli byl zaveden v linuxovém jádře 4.18, někteří dodavatelé tuto změnu backportovali na starší jádra, která podporují. Pro větší pohodlí následující tabulka podrobně popisuje dostupnost tohoto parametru modulu na běžných distribucích Linuxu.

| Distribuce | Může zakázat SMB 1 |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | Ne |
| Ubuntu 18.04 | Ano |
| Ubuntu 19.04+ | Ano |
| Debian 8-9 | Ne |
| Debian 10+ | Ano |
| Fedora 29+ | Ano |
| CentOS 7 | Ne | 
| Centos 8+ | Ano |
| Red Hat Enterprise Linux 6.x-7.x | Ne |
| Red Hat Enterprise Linux 8+ | Ano |
| openSUSE Skok 15,0 | Ne |
| openSUSE Leap 15.1+ | Ano |
| openSUSE Tumbleweed | Ano |
| SUSE Linux Enterprise 11.x-12.x | Ne |
| SUSE Linux Enterprise 15 | Ne |
| SUSE Linux Enterprise 15.1 | Ne |

Můžete zkontrolovat, zda vaše linuxová `disable_legacy_dialects` distribuce podporuje parametr modulu pomocí následujícího příkazu.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

Tento příkaz by měl výstup následující zprávy:

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

Před zakázáním SMB 1, musíte zkontrolovat, zda modul SMB není aktuálně načten ve vašem systému (to se stane automaticky, pokud jste namontovali sdílenou složku SMB). Můžete to provést pomocí následujícího příkazu, který by měl výstup nic, pokud SMB není načten:

```bash
lsmod | grep cifs
```

Chcete-li modul uvolnit, nejprve odpojte `umount` všechny sdílené složky SMB (pomocí příkazu, jak je popsáno výše). Všechny připojené sdílené složky SMB v systému můžete identifikovat pomocí následujícího příkazu:

```bash
mount | grep cifs
```

Jakmile odpojíte všechny sdílené složky SMB, je bezpečné uvolnit modul. Můžete to provést pomocí příkazu `modprobe`:

```bash
sudo modprobe -r cifs
```

Modul můžete ručně načíst pomocí protokolu SMB `modprobe` 1, který je uvolněn pomocí příkazu:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Nakonec můžete zkontrolovat, zda byl modul SMB načten parametrem, a `/sys/module/cifs/parameters`to tak, že se podíváte na načtené parametry v :

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Chcete-li trvale zakázat SMB 1 na distribucích založených na Ubuntu a Debianu, musíte vytvořit nový `/etc/modprobe.d/local.conf` soubor (pokud ještě nemáte vlastní možnosti pro jiné moduly) volané s nastavením. To lze provést pomocí následujícího příkazu:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

Můžete ověřit, že to fungovalo načtením modulu SMB:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="feedback"></a>Váš názor
Uživatelé Linuxu, chceme slyšet od vás!

Skupina uživatelů Azure Files for Linux poskytuje fórum pro sdílení zpětné vazby při vyhodnocování a přijímání úložiště souborů na Linuxu. Pošlete e-mail [emitujte uživatele Azure Files Linux,](mailto:azurefiles@microsoft.com) aby se připojili ke skupině uživatelů.

## <a name="next-steps"></a>Další kroky
Další informace o službě Soubory Azure najdete na těchto odkazech:

* [Plánování nasazení služby Soubory Azure](storage-files-planning.md)
* [Nejčastější dotazy](../storage-files-faq.md)
* [Řešení potíží](storage-troubleshoot-linux-file-connection-problems.md)

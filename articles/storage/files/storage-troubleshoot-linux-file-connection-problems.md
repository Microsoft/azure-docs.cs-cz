---
title: Poradce při potížích se soubory Azure v Linuxu | Dokumenty společnosti Microsoft
description: Řešení problémů se soubory Azure v Linuxu
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 95e220102cba290664a32cb6bbebef881ae4ffde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159485"
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>Poradce při potížích se soubory Azure v Linuxu

Tento článek uvádí běžné problémy, které souvisejí se soubory Azure při připojení z klientů Linuxu. Poskytuje také možné příčiny a řešení těchto problémů. 

Kromě kroků řešení potíží v tomto článku můžete pomocí [azFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089) zajistit, že klient Linux má správné požadavky. AzFileDiagnostics automatizuje detekci většiny příznaků uvedených v tomto článku. Pomáhá nastavit prostředí tak, aby získalo optimální výkon. Tyto informace najdete také v [Poradci při potížích se sdílenými složkami Azure Files](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares). Poradce při potížích poskytuje kroky, které vám pomohou s problémy s připojením, mapováním a montáží sdílených složek Azure Files.

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Nelze se připojit ke sdílené složce Azure nebo ji připojit.

### <a name="cause"></a>Příčina

Běžné příčiny tohoto problému jsou:

- Používáte nekompatibilního distribučního klienta Linuxu. Doporučujeme, abyste se k sdílení souborů Azure používali pomocí následujících distribucí Linuxu:

|   | SMB 2.1 <br>(Připojení na virtuálních počítačích ve stejné oblasti Azure) | SMB 3.0 <br>(Přípojky z místních a mezioblastí) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04+ | 16.04+ |
| RHEL | 7+ | 7,5+ |
| CentOS | 7+ |  7,5+ |
| Debian | 8+ |   |
| openSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12 | 12 SP3+ |

- Nástroje CIFS (cifs-utils) nejsou v klientovi nainstalovány.
- Minimální verze SMB/CIFS, 2.1, není nainstalována v klientovi.
- Šifrování SMB 3.0 není v klientovi podporováno. Předchozí tabulka obsahuje seznam distribucí Linuxu, které podporují připojení z místní a mezioblasti pomocí šifrování. Ostatní distribuce vyžadují jádro verze 4.11 nebo novější.
- Pokoušíte se připojit k účtu úložiště přes port TCP 445, který není podporován.
- Pokoušíte se připojit ke sdílené složce Azure ze virtuálního počítače Azure a virtuální počítač není ve stejné oblasti jako účet úložiště.
- Pokud je v účtu úložiště povoleno nastavení [Vyžadováno zabezpečený přenos,]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) soubory Azure povolí jenom připojení, která používají SMB 3.0 s šifrováním.

### <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, použijte [nástroj pro odstraňování potíží pro chyby při připojování souborů Azure v Linuxu](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). Tento nástroj:

* Pomáhá ověřit klientské prostředí.
* Detekuje nekompatibilní konfiguraci klienta, která by způsobila selhání přístupu pro soubory Azure.
* Poskytuje normativní pokyny pro samofixaci.
* Shromažďuje diagnostické stopy.

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>"Mount error(13): Permission denied" při připojení sdílené složky Azure

### <a name="cause-1-unencrypted-communication-channel"></a>Příčina 1: Nešifrovaný komunikační kanál

Z bezpečnostních důvodů se připojení ke sdíleným složkám Azure blokují, když komunikační kanál není šifrovaný a když k pokusu o připojení nedošlo ze stejného datacentra, ve kterém se sdílená složka Azure nachází. Nešifrovaná připojení ze stejného datacentra se můžou blokovat také v případě, že je pro účet úložiště povolené nastavení [Vyžadovat zabezpečený přenos](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer). Šifrovaný komunikační kanál je k dispozici pouze v případě, že klientský operační systém uživatele podporuje šifrování protokolu SMB.

Další informace najdete v části [Požadavky na připojení sdílené složky Azure v Linuxu s využitím balíčku cifs-utils](storage-how-to-use-files-linux.md#prerequisites). 

### <a name="solution-for-cause-1"></a>Řešení 1. příčiny

1. Připojte se z klienta, který podporuje šifrování SMB nebo se připojte z virtuálního počítače ve stejném datovém centru jako účet úložiště Azure, který se používá pro sdílenou složku Azure.
2. Ověřte, zda je v účtu úložiště zakázáno [nastavení Požadováno zabezpečeného přenosu,](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) pokud klient nepodporuje šifrování SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Příčina 2: V účtu úložiště jsou povolena pravidla virtuální sítě nebo brány firewall. 

Pokud jsou pro účet úložiště nakonfigurovaná pravidla virtuální sítě nebo brány firewall, síťovému provozu se odepře přístup, dokud se nepovolí přístup z virtuální sítě nebo IP adresy klienta.

### <a name="solution-for-cause-2"></a>Řešení 2. příčiny

Ověřte, že jsou pro účet úložiště správně nakonfigurovaná pravidla brány firewall a virtuální sítě. Pokud chcete otestovat, jestli problém způsobují pravidla brány firewall nebo virtuální sítě, dočasně změňte nastavení pro účet úložiště na **Povolit přístup ze všech sítí**. Další informace najdete v tématu [Konfigurace virtuálních sítí a bran firewall Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>"[oprávnění odepřeno] Disková kvóta byla překročena" při pokusu o otevření souboru

V Linuxu se zobrazí chybová zpráva, která se podobá následující:

**\<Byl překročen název souboru> [permission denied] Disk quota**

### <a name="cause"></a>Příčina

Dosáhli jste horní hranice souběžných otevřených popisovačů, které jsou povoleny pro soubor.

V jednom souboru je kvóta 2 000 otevřených popisovačů. Pokud máte 2 000 otevřených popisovačů, zobrazí se chybová zpráva, která říká, že je dosaženo kvóty.

### <a name="solution"></a>Řešení

Snižte počet souběžných otevřených popisovačů zavřením některých popisovačů a opakujte operaci.

Chcete-li zobrazit otevřené popisovače pro sdílenou složku, adresář nebo soubor, použijte rutinu [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell.  

Chcete-li zavřít otevřené popisovače pro sdílenou složku, adresář nebo soubor, použijte rutinu [Prostředí PowerShell Close-AzStorageFileHandle.](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle)

> [!Note]  
> Rutiny Get-AzStorageFileHandle a Close-AzStorageFileHandle jsou součástí modulu Az PowerShell verze 2.4 nebo novější. Informace o instalaci nejnovějšího modulu Az PowerShell najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Pomalé kopírování souborů do a z Azure Files v Linuxu

- Pokud nemáte konkrétní požadavek na minimální velikost vstupně-va, doporučujeme použít 1 MiB jako velikost vstupně-v.v.,Pro optimální výkon.
- Použijte správnou metodu kopírování:
    - Pro jakýkoli přenos mezi dvěma sdílenými složkami použijte [AzCopy.](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
    - Použití cp nebo dd s paralelní min. může zlepšit rychlost kopírování, počet podprocesů závisí na případu použití a zatížení. Následující příklady používají šest: 
    - cp příklad (cp použije výchozí velikost bloku systému souborů `find * -type f | parallel --will-cite -j 6 cp {} /mntpremium/ &`jako velikost bloku): .
    - dd příklad (tento příkaz explicitně nastaví velikost bloku na 1 MiB):`find * -type f | parallel --will-cite-j 6 dd if={} of=/mnt/share/{} bs=1M`
    - Open source nástroje třetích stran, jako jsou:
        - [GNU Paralelní](https://www.gnu.org/software/parallel/).
        - [Fpart](https://github.com/martymac/fpart) - Seřadí soubory a zabalí je do oddílů.
        - [Fpsync](https://github.com/martymac/fpart/blob/master/tools/fpsync) - Používá Fpart a nástroj pro kopírování k vytvoření více instancí pro migraci dat z src_dir do dst_url.
        - [Multi](https://github.com/pkolano/mutil) - Multi-threaded cp a md5sum založené na GNU coreutils.
- Nastavení velikosti souboru předem, namísto provedení každý zápis rozšíření zápisu, pomáhá zlepšit rychlost kopírování ve scénářích, kde je známa velikost souboru. Pokud je třeba se vyhnout rozšíření zápisů, můžete `truncate - size <size><file>` pomocí příkazu nastavit velikost cílového souboru. Poté příkaz `dd if=<source> of=<target> bs=1M conv=notrunc`zkopíruje zdrojový soubor, aniž by bylo třeba opakovaně aktualizovat velikost cílového souboru. Můžete například nastavit velikost cílového souboru pro každý soubor, který chcete zkopírovat (předpokládejme, že je sdílená složka připojena pod /mnt/share):
    - `$ for i in `` find * -type f``; do truncate --size ``stat -c%s $i`` /mnt/share/$i; done`
    - a pak - kopírovat soubory bez rozšíření zápisů paralelně:`$find * -type f | parallel -j6 dd if={} of =/mnt/share/{} bs=1M conv=notrunc`

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>"Mount error(115): Operace právě probíhá" při připojení souborů Azure pomocí SMB 3.0

### <a name="cause"></a>Příčina

Některé distribuce Linuxu ještě nepodporují funkce šifrování v protokolu SMB 3.0. Uživatelům se při pokusu o připojení služby Azure Files pomocí protokolu SMB 3.0 může zobrazit chybová zpráva 115 kvůli chybějící funkci. Protokol SMB 3.0 s úplným šifrováním se podporuje pouze při použití Ubuntu 16.04 nebo novější verze.

### <a name="solution"></a>Řešení

Funkce šifrování pro protokol SMB 3.0 pro Linux se zavedla v jádru verze 4.11. Tato funkce umožňuje připojení sdílené složky Azure z místního prostředí nebo z jiné oblasti Azure. Některé linuxové distribuce mohou mít backported změny z jádra 4.11 na starší verze linuxového jádra, které udržují. Chcete-li pomoci při určování, zda vaše verze Linuxu podporuje SMB 3.0 s šifrováním, poraďte se s [pomocí Souborů Azure s Linuxem](storage-how-to-use-files-linux.md). 

Pokud váš linuxový klient SMB nepodporuje šifrování, připojte službu Azure Files pomocí protokolu SMB 2.1 z virtuálního počítače Azure s Linuxem, který je ve stejném datacentru jako sdílená složka. Ověřte, že je pro účet úložiště zakázané nastavení [Vyžadovat zabezpečený přenos]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer). 

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Při pokusu o přístup ke sdílené složce Azure nebo jeho odstranění došlo k chybě "Žádný přístup"  
Při pokusu o přístup nebo odstranění sdílené složky Azure na portálu se může zobrazit následující chyba:

Bez přístupu  
Kód chyby: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Příčina 1: V účtu úložiště jsou povolena pravidla virtuální sítě nebo brány firewall.

### <a name="solution-for-cause-1"></a>Řešení 1. příčiny

Ověřte, že jsou pro účet úložiště správně nakonfigurovaná pravidla brány firewall a virtuální sítě. Pokud chcete otestovat, jestli problém způsobují pravidla brány firewall nebo virtuální sítě, dočasně změňte nastavení pro účet úložiště na **Povolit přístup ze všech sítí**. Další informace najdete v tématu [Konfigurace virtuálních sítí a bran firewall Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Příčina 2: Váš uživatelský účet nemá přístup k účtu úložiště

### <a name="solution-for-cause-2"></a>Řešení 2. příčiny

Přejděte na účet úložiště, kde se nachází sdílená složka Azure, klikněte na **řízení přístupu (IAM)** a ověřte, že váš uživatelský účet má přístup k účtu úložiště. Další informace najdete v tématu [Jak zabezpečit účet úložiště pomocí řízení přístupu na základě rolí (RBAC).](https://docs.microsoft.com/azure/storage/blobs/security-recommendations#data-protection)

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Nejde odstranit soubor nebo adresář ve sdílené složce Azure

### <a name="cause"></a>Příčina
K tomuto problému obvykle dochází, pokud soubor nebo adresář má otevřený popisovač. 

### <a name="solution"></a>Řešení

Pokud klienti SMB zavřeli všechny otevřené popisovače a problém přetrvává, proveďte následující kroky:

- Pomocí rutiny [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell zobrazte otevřené popisovače.

- Pomocí rutiny [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell zavřete otevřené popisovače. 

> [!Note]  
> Rutiny Get-AzStorageFileHandle a Close-AzStorageFileHandle jsou součástí modulu Az PowerShell verze 2.4 nebo novější. Informace o instalaci nejnovějšího modulu Az PowerShell najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Pomalý výkon sdílené složky Azure připojené k virtuálnímu počítači s Linuxem

### <a name="cause-1-caching"></a>Příčina 1: Ukládání do mezipaměti

Jednou z možných příčin pomalého výkonu je zakázání ukládání do mezipaměti. Ukládání do mezipaměti může být užitečné, pokud k souboru přistupujete opakovaně, jinak může být režie. Před zakázáním mezipaměti zkontrolujte, zda používáte mezipaměť.

### <a name="solution-for-cause-1"></a>Řešení 1. příčiny

Chcete-li zkontrolovat, zda je ukládání do mezipaměti zakázáno, vyhledejte **položku cache=.**

**Cache=none** označuje, že ukládání do mezipaměti je zakázáno. Znovu připojte sdílenou složku pomocí výchozího příkazu mount nebo explicitním přidáním **možnosti cache=strict** do příkazu mount, abyste zajistili, že je povolen výchozí režim ukládání do mezipaměti nebo "striktní" režim ukládání do mezipaměti.

V některých případech může možnost **připojení serverino** způsobit spuštění příkazu **ls** stat proti každé položce adresáře. Toto chování má za následek snížení výkonu při výpisu velkého adresáře. Můžete zkontrolovat možnosti připojení v **/ etc / fstab** vstupu:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Můžete také zkontrolovat, zda jsou správné možnosti používány spuštěním **příkazu sudo mount | grep cifs** a kontrolou jeho výstupu. Následuje příklad výstupu:

```
//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)
```

Pokud **cache=strict** nebo **serverino** možnost není k dispozici, odpojte a připojte soubory Azure znovu spuštěním příkazu připojit z [dokumentace](../storage-how-to-use-files-linux.md). Poté znovu zkontrolujte, zda má položka **/etc/fstab** správné možnosti.

### <a name="cause-2-throttling"></a>Příčina 2: Škrcení

Je možné, že dochází k omezení a vaše požadavky jsou odesílány do fronty. Můžete to ověřit pomocí [metrik azure storage v Azure Monitoru](../common/storage-metrics-in-azure-monitor.md).

### <a name="solution-for-cause-2"></a>Řešení 2. příčiny

Ujistěte se, že vaše aplikace je v rámci [cílů škálování Souborů Azure](storage-files-scale-targets.md#azure-files-scale-targets).

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Časová razítka byla ztracena při kopírování souborů z Windows do Linuxu

Na platformách Linux/Unix příkaz **cp -p** selže, pokud různí uživatelé vlastní soubor 1 a soubor 2.

### <a name="cause"></a>Příčina

Síla příznak **f** v COPYFILE má za následek provádění **cp -p -f** na Unixu. Tento příkaz také nezachová časové razítko souboru, který nevlastníte.

### <a name="workaround"></a>Alternativní řešení

Pro kopírování souborů použijte uživatele účtu úložiště:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: nelze&lt;získat&gt;přístup k ' cestě ': Chyba vstupu a výstupu

Při pokusu o seznam souborů ve sdílené složce Azure pomocí příkazu ls příkaz přestane reagovat při výpisu souborů. Zobrazí se následující chyba:

**ls: nelze&lt;získat&gt;přístup' cesta ': Vstupní/výstupní chyba**


### <a name="solution"></a>Řešení
Upgradujte linuxové jádro na následující verze, které mají opravu tohoto problému:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Všechny verze, které jsou větší nebo rovno 4.13

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Nelze vytvořit symbolické odkazy - ln: Nepodařilo se vytvořit symbolický odkaz 't': Operace není podporována

### <a name="cause"></a>Příčina
Ve výchozím nastavení připojení sdílených složek Azure na Linuxu pomocí CIFS neumožňuje podporu pro symbolické odkazy (symbolické odkazy). Zobrazí se chyba, jako je tato:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Řešení
Klient CIFS linuxu nepodporuje vytváření symbolických odkazů ve stylu Windows přes protokol SMB 2 nebo 3. V současné době linuxový klient podporuje jiný styl symbolických odkazů s názvem [Minshall + francouzské symbolické odkazy](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) pro obě operace pro vytváření a sledování. Zákazníci, kteří potřebují symbolické odkazy, mohou použít možnost připojení "mfsymlinks". Doporučujeme "mfsymlinks", protože je to také formát, který používají Macy.

Chcete-li používat symbolické odkazy, přidejte na konec příkazu připojení CIFS následující:

```
,mfsymlinks
```

Takže příkaz vypadá jako:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

Poté můžete vytvořit symbolické odkazy, jak je navrženo na [wiki](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>"Chyba připojení(112): Hostitel je mimo rozsah z důvodu časového limitu opětovného připojení

K chybě připojení 112 dochází v klientovi s Linuxem při dlouhodobé nečinnosti klienta. Po delší době nečinnosti se klient odpojí a vyprší platnost připojení.  

### <a name="cause"></a>Příčina

Připojení může být nečinné z následujících důvodů:

-   Selhání síťové komunikace, která brání opětovnému navázání připojení k serveru přes protokol TCP, při použití výchozí možnosti dočasného připojení
-   Nedávné opravy opětovného připojení, které nejsou ve starších jádrech

### <a name="solution"></a>Řešení

Tento problém s opětovným připojením v jádru Linuxu je teď opravený v rámci následujících změn:

- [Oprava opětovného připojení, aby se neodkládalo opětovné připojení k relaci protokolu SMB 3 dlouho po opětovném připojení k socketu](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
- [Volání služby Echo okamžitě po opětovném připojení k socketu](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
- [CIFS: Oprava možného poškození paměti během opětovného připojení](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
- [CIFS: Oprava možného dvojitého zamykání mutexu během opětovného připojení (pro jádro v4.9 a novější)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

Tyto změny se však ještě nemusely přenést do všech distribucí Linuxu. Pokud používáte oblíbenou distribuci Linuxu, můžete zkontrolovat [použití souborů Azure s Linuxem](storage-how-to-use-files-linux.md) a zjistit, která verze vaší distribuce má potřebné změny jádra.

### <a name="workaround"></a>Alternativní řešení

Tento problém můžete obejít tím, že zadáte trvalé připojení. Trvalé připojení přinutí klienta, aby počkal na navázání nebo explicitní přerušení připojení. S jeho využitím můžete zabránit chybám způsobeným vypršením časového limitu sítě. Toto alternativní řešení však může způsobit nekonečné čekání. Buďte připraveni v případě potřeby zastavit připojení.

Pokud nemůžete upgradovat na nejnovější verze jádra, můžete tento problém obejít tím, že ve sdílené složce Azure budete uchovávat soubor, do kterého budete každých maximálně 30 sekund zapisovat. Musí se jednat o operaci zápisu, jako je přepsání vytvořeného nebo upraveného data souboru. Jinak můžete dostávat výsledky uložené v mezipaměti a vaše operace nemusí aktivovat opětovné připojení.

## <a name="cifs-vfs-error--22-on-ioctl-to-get-interface-list-when-you-mount-an-azure-file-share-by-using-smb-30"></a>"CIFS VFS: error -22 on ioctl to get interface list" při připojení sdílené složky Azure pomocí SMB 3.0

### <a name="cause"></a>Příčina
Tato chyba je zaznamenána, protože soubory Azure [aktuálně nepodporují vícekanálový soubor SMB](https://docs.microsoft.com/rest/api/storageservices/features-not-supported-by-the-azure-file-service).

### <a name="solution"></a>Řešení
Tato chyba může být ignorována.

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Obraťte se na podporu.

Pokud stále potřebujete pomoc, obraťte se na [podporu,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) abyste problém rychle vyřešili.

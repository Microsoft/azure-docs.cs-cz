---
title: Řešení potíží s Azure Files v Linuxu | Dokumentace Microsoftu
description: Řešení potíží se soubory Azure v Linuxu
services: storage
author: jeffpatt24
tags: storage
ms.service: storage
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffpatt
ms.component: files
ms.openlocfilehash: d5dd2e2943d78291fc9c4903c15fb4d3767edbea
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52442008"
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>Řešení potíží s Azure Files v Linuxu

Tento článek uvádí běžné problémy, které se vztahují k Microsoft Azure Files, když se připojíte z klientů Linux. Poskytuje také možné příčiny a řešení těchto problémů. 

Kromě použijte kroky v tomto článku, můžete použít [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089) zajistit správné požadavky klienta pro Linux. AzFileDiagnostics automatizuje detekce většinu z příznaků uvedených v tomto článku. Umožňuje nastavit prostředí, abyste získali optimální výkon. Můžete také najít tyto informace [Azure sdíleným složkám Poradce při potížích](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares). Poradce při potížích se vysvětluje, jak vám pomůžou se potíže s připojením, mapování a připojování sdílených složek Azure.

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>"[bylo odepřeno oprávnění]: překročena kvóta disku" při pokusu o otevření souboru

V systému Linux obdržíte chybovou zprávu, která vypadá přibližně takto:

**<filename> [oprávnění byla odepřena.] Byla překročena kvóta disku**

### <a name="cause"></a>Příčina

Bylo dosaženo horní limit počtu souběžných otevřených popisovačů, které jsou povoleny pro soubor.

### <a name="solution"></a>Řešení

Snižte počet souběžných otevřených popisovačů ukončením některé obslužné rutiny a pak zkuste operaci zopakovat. Další informace najdete v tématu [kontrolní seznam výkonu a škálovatelnosti Microsoft Azure Storage](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Zpomalit kopírování souborů do a z Azure Files v Linuxu

- Pokud nemáte konkrétní požadavek minimální velikost vstupně-výstupních operací, doporučujeme použít 1 MiB jako velikost vstupně-výstupní operace pro zajištění optimálního výkonu.
- Pokud znáte konečné velikosti souboru, který jste rozšířit pomocí zápisy a software nebude vyzkoušejte problémy s kompatibilitou při nepsaná tail na tento soubor obsahuje nulami, nastavte velikost souboru předem místo provedení při každém zápisu rozšiřování zápisu.
- Použijte metodu pravé kopie:
    - Použití [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) pro všechny přenosy mezi dvěma sdílenými složkami souborů.
    - Použití [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) mezi sdílené složky na místním počítači.

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>"Připojit error(112): Hostitel nepracuje" z důvodu vypršení časového limitu opětovné připojení

"112" připojení dojde k chybě na straně klienta pro Linux klienta byl nečinný po dlouhou dobu. Po delší dobu nečinné klient neodpojí a připojení vyprší časový limit.  

### <a name="cause"></a>Příčina

Z následujících důvodů může být připojení nečinné:

-   Chybám v komunikaci sítě, které brání obnovujete připojení protokolu TCP serveru při použití možnosti výchozí "text soft" připojení
-   Nejnovější opravy opětovné připojení, které nejsou k dispozici ve starší jádra

### <a name="solution"></a>Řešení

Tento problém opětovným připojením v linuxového jádra je opravená jako součást následující změny:

- [Oprava znovu připojit k není odložit smb3 relace znovu připojit po obnovení připojení soketu](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
- [Volání služby echo okamžitě po obnovení připojení soketu](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
- [CIFS: Oprava poškození možné paměti při volání metody reconnect](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
- [CIFS: Opravte je to možné double uzamčení vzájemně vyloučeného přístupu při volání metody reconnect (pro jádra v4.9 a novější)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

Však nemusí být tyto změny přenést ještě do Linuxových distribucí. Tato oprava a ostatní opravy opětovné připojení jsou v následujících oblíbených Linuxových jádrech: 4.4.40 4.8.16 a 4.9.1. Tato oprava můžete získat díky upgradu na některou z těchto verzí doporučené jádra.

### <a name="workaround"></a>Alternativní řešení

Tento problém můžete vyřešit tak, že zadáte pevné připojení. Pevné připojení vynutí klient čekat, dokud se naváže spojení nebo dokud explicitně je přerušeno. Můžete ji chcete-li zabránit chybám z důvodu vypršení časových limitů sítě. Toto řešení však může způsobit neomezené čekání. Buďte připraveni zastavit připojení podle potřeby.

Pokud nelze upgradovat na nejnovější verze jádra, můžete tento problém vyřešit tím, že soubor do sdílené složky Azure, který píšete na každých 30 sekund nebo méně. Toto musí být operace zápisu, jako je například přepisování vytvořené nebo upravené datum na tento soubor. V opačném případě se mohou zobrazovat výsledky uložené v mezipaměti, a operace nemusí aktivovat obnovení připojení.

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>"Připojit error(115): nyní probíhá operace" Když připojíte soubory Azure pomocí protokolu SMB 3.0

### <a name="cause"></a>Příčina

Některých Linuxových distribucích zatím nepodporují funkce šifrování v SMB 3.0. Uživatelům může zobrazit zpráva "115" Chyba, pokud pokusu o připojení služby soubory Azure pomocí protokolu SMB 3.0 z důvodu chybějící funkce. Úplné šifrování protokolu SMB 3.0 je podporována pouze v případě, že používáte Ubuntu 16.04 nebo novější.

### <a name="solution"></a>Řešení

Funkce šifrování protokolu SMB 3.0 pro Linux byla zavedena v 4.11 jádra. Tato funkce umožňuje připojení sdílené složky Azure v místním nebo z jiné oblasti Azure. V době publikování tato funkce byla přeneseny zpět do č. 17.04 Ubuntu a Ubuntu 16.10. 

Pokud klient Linux SMB nepodporuje šifrování, připojení Azure souborů pomocí protokolu SMB 2.1 z virtuálního počítače Linux Azure, který je ve stejném datacentru jako sdílené. Ověřte, že [vyžadovat zabezpečený přenos]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) je zakázáno v účtu úložiště. 

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Nízký výkon na sdílenou složku Azure připojit na virtuálním počítači s Linuxem

### <a name="cause"></a>Příčina

Jednou možnou příčinou nízkého výkonu je zakázáno ukládání do mezipaměti.

### <a name="solution"></a>Řešení

Chcete-li zkontrolovat, zda je zakázáno ukládání do mezipaměti, vyhledejte **mezipaměti =** položka. 

**Mezipaměti = none** označuje, že je zakázáno ukládání do mezipaměti. Připojte sdílenou složku s použitím výchozí příkaz připojení nebo tak, že explicitně přidáte **mezipaměti = striktní** je povolena možnost připojení příkaz, kterým zajistíte, že ukládání do mezipaměti výchozí nebo "přísné" režim ukládání do mezipaměti.

V některých případech **serverino** možností připojení může způsobit, že **ls** příkaz ke spuštění stat – pro každý záznam adresáře. Toto chování způsobí snížení výkonu už nabízíte velké objemy adresáře. Možnosti připojení můžete zkontrolovat ve vaší **/etc/fstab** položky:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Můžete také zkontrolovat, zda se právě využívají správné možnosti pro spuštění **sudo připojení | grep cifs** příkazu a zkontrolujete jeho výstup. Následuje příklad výstupu:

`//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)`

Pokud **mezipaměti = striktní** nebo **serverino** možnost je k dispozici, odpojte a znovu připojit soubory Azure pomocí příkazu připojení z [dokumentaci](../storage-how-to-use-files-linux.md). Potom spusťte opětovnou kontrolu, která **/etc/fstab** položka má správné možnosti.

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Časová razítka bylo ztraceno v kopírování souborů z Windows do systému Linux

Na platformách Linux/Unix **cp -p** příkaz selže, pokud různí uživatelé vlastní soubor 1 a 2 souboru.

### <a name="cause"></a>Příčina

Příznak force **f** v COPYFILE výsledkem provádění **cp -p -f** v systému Unix. Tento příkaz také selže zachovat časové razítko souboru, kterou nevlastníte.

### <a name="workaround"></a>Alternativní řešení

Použijte uživatelský účet úložiště pro kopírování souborů:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Nelze se připojit k nebo připojení sdílené složky Azure

### <a name="cause"></a>Příčina

Mezi běžné příčiny tohoto problému patří:


- Používáte klientem nekompatibilní distribuce Linuxu. Doporučujeme použít následující Linuxových distribucí pro připojení sdílené složky Azure:

    |   | SMB 2.1 <br>(Připojení na virtuálních počítačích v rámci stejné oblasti Azure) | SMB 3.0 <br>(Připojení z místního a mezi oblastmi) |
    | --- | :---: | :---: |
    | Ubuntu Server | 14.04 + | 16.04 + |
    | RHEL | 7 + | 7.5+ |
    | CentOS | 7 + |  7.5+ |
    | Debian | 8+ |   |
    | openSUSE | 13.2 + | 42.3 + |
    | SUSE Linux Enterprise Server | 12 | 12 SP3 + |

- V klientském počítači nejsou nainstalované nástroje CIFS (cfs utils).
- Minimální verze protokolu SMB/CIFS, 2.1, není nainstalována na straně klienta.
- Šifrování SMB 3.0 se nepodporuje na straně klienta. Šifrování SMB 3.0 je k dispozici v Ubuntu 16.4 a novějších verzích, společně s operačním systémem SUSE 12.3 a novějších verzích. Ostatní distribuce vyžadují jádra 4.11 a novějších verzích.
- Pokoušíte se připojit k účtu úložiště přes port TCP 445, což není podporováno.
- Pokoušíte se připojit ke sdílené složky Azure z virtuálního počítače Azure a virtuální počítač není ve stejné oblasti jako účet úložiště.
- Pokud [vyžadovat zabezpečený přenos]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) pro účet úložiště je povolené nastavení, soubory Azure vám umožní pouze připojení využívající šifrování protokolu SMB 3.0.

### <a name="solution"></a>Řešení

Chcete-li problém vyřešit, použijte [řešení potíží s nástrojem pro soubory Azure chyby připojení v Linuxu](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). Tento nástroj:

* Umožňuje ověření klienta, spouštění prostředí.
* Zjistí nekompatibilní klienta konfigurace, která může způsobit selhání přístupu pro soubory Azure.
* Poskytuje doporučený postup na místním řešení.
* Shromažďuje trasování diagnostiky.

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: Nelze získat přístup k '&lt;cesta&gt;": Chyba vstupu/výstupu

Při pokusu o seznam souborů ve sdílené složky Azure pomocí příkazu ls, přestane reagovat při výpisu souborů příkazu. Zobrazí následující chyba:

**ls: Nelze získat přístup k '&lt;cesta&gt;": Chyba vstupu/výstupu**


### <a name="solution"></a>Řešení
Jádro Linuxu upgradujte následující verze, které mají opravu tohoto problému:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Všechny verze, které jsou větší než nebo rovna hodnotě 4.13

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Nelze vytvořit symbolické odkazy - ln: se nepovedlo vytvořit symbolický odkaz, 't': operace není podporována

### <a name="cause"></a>Příčina
Ve výchozím nastavení nemá připojení sdílené složky Azure v Linuxu pomocí CIFS povolení podpory pro symbolické odkazy (symbolických odkazů). Zobrazí se chyba takto:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Řešení
Klient Linux CIFS nepodporuje vytváření stylu Windows symbolické odkazy přes protokol SMB 2 nebo 3 protokolu. V současné době podporuje klient Linux jiný styl symbolické odkazy, které volá [Minshall + francouzština symbolických odkazů](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) pro obě vytvořit a sledovat operace. Zákazníci, kteří potřebují symbolické odkazy můžete použít možnost "mfsymlinks" připojení. Doporučujeme "mfsymlinks", protože je také formátu, který Mac používat.

Pokud chcete použít symbolických odkazů, přidejte následující na konec příkazu CIFS připojení:

```
,mfsymlinks
```

Takže příkaz vypadá přibližně takto:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

Potom můžete vytvořit symbolických odkazů na jako navrhované [wiki](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers).

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychlé vyřešení problému.

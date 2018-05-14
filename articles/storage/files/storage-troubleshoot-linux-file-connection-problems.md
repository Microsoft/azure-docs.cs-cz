---
title: Poradce při potížích Azure soubory v systému Linux | Microsoft Docs
description: Řešení potíží s Azure soubory v systému Linux
services: storage
documentationcenter: ''
author: genlin
manager: willchen
editor: na
tags: storage
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 7b5567359e7ca87d26e05d336337b55af364031e
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>Poradce při potížích Azure soubory v systému Linux

V tomto článku jsou uvedeny běžné problémy, které se vztahují k Microsoft Azure Files při připojení z klientů Linux. Umožňuje také možné příčiny a řešení těchto problémů.

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>"[bylo odepřeno oprávnění] byla překročena disková kvóta" při pokusu o otevření souboru

V systému Linux obdržíte chybovou zprávu, která vypadá přibližně takto:

**<filename> [bylo odepřeno oprávnění] Byla překročena kvóta disku**

### <a name="cause"></a>Příčina

Bylo dosaženo horní limit počtu souběžných otevřete obslužných rutin, které jsou povoleny pro soubor.

### <a name="solution"></a>Řešení

Snižte počet souběžných otevřených obslužných rutin ukončením některé obslužné rutiny a potom operaci opakujte. Další informace najdete v tématu [kontrolní seznam výkonu a škálovatelnosti služby Microsoft Azure Storage](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Pomalé kopírování souborů do a z Azure soubory v systému Linux

-   Pokud nemáte konkrétní požadavky minimální velikost vstupně-výstupních operací, doporučujeme použít 1 MB jako velikost vstupně-výstupní operace pro zajištění optimálního výkonu.
-   Pokud znáte konečné velikosti souboru, který bude rozšiřovat s použitím zápisy a váš software nebude zaznamenat problémy s kompatibilitou, při unwritten poškozené databáze za na soubor obsahuje nuly, potom nastavte velikost souboru předem místo provedení každém zápisu rozšíření zápisu.
-   Použijte metodu pravé kopie:
    -   Použití [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) pro všechny přenosy mezi dvěma sdílenými složkami.
    -   Použití [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) mezi sdílené složky na místním počítači.

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>"Připojit error(112): hostitel je dolů" z důvodu vypršení časového limitu opětovné připojení

"112" připojení dojde k chybě na straně klienta Linux klienta bylo nečinné po dlouhou dobu. Po delší dobu nečinnosti se klient neodpojí a časový limit připojení.  

### <a name="cause"></a>Příčina

Připojení může být nečinnosti z následujících důvodů:

-   Selhání komunikace sítě, které zabraňují znovu navázání připojení TCP k serveru, pokud je použita výchozí možnost "soft" připojení
-   Nejnovější opravy opětovné připojení, které se nenacházejí v starší jádra

### <a name="solution"></a>Řešení

Tento problém opětovné připojení v systému Linux jádra vyřešen nyní jako součást následující změny:

- [Oprava připojení není odložení smb3 relace znovu připojit po obnovení připojení soketu](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
-   [Volání služby echo ihned po obnovení připojení soketu](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
-   [CIFS: Oprava poškození možné paměti při volání metody reconnect](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
-   [CIFS: Opravte možné dvojité uzamykání mutex během opětovného připojení (pro v4.9 jádra a novější)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

Ale tyto změny nemusí být přesně ještě do Linuxových distribucích. Tato oprava a ostatní opravy opětovné připojení jsou vytvářeny v jádrech následující oblíbených Linux: 4.4.40, 4.8.16 a 4.9.1. Tato oprava získáte po upgradu na jednu z těchto verzí doporučené jádra.

### <a name="workaround"></a>Alternativní řešení

Tento problém můžete vyřešit tak, že zadáte pevné připojení. Vynutí se klient čekat, dokud nebude navázáno připojení, nebo dokud ji explicitně přerušení a je možné zabránit chybám z důvodu vypršení časových limitů sítě. Toto řešení však může způsobit neomezené čeká. Připravte se na zastavení připojení v případě potřeby.

Pokud nemůžete provést upgrade na nejnovější verze jádra, můžete tento problém obejít tím, že soubor v Azure sdílené složky, zapisovat na každých 30 sekund nebo méně. Toto musí být operace zápisu, jako je například přepisování vytvořené nebo upravené datum v souboru. Jinak může získat výsledky uložené v mezipaměti a operaci nemusí spustit obnovení připojení.

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>"Připojit error(115): operace právě probíhá" při připojení Azure souborů pomocí protokolu SMB 3.0

### <a name="cause"></a>Příčina

Některé Linuxových distribucích zatím nepodporují funkcí šifrování v SMB 3.0 a uživatelé se může zobrazit zprávu "115" Chyba při pokusu o připojení Azure souborů pomocí protokolu SMB 3.0 z důvodu chybějící funkce. Protokol SMB 3.0 s úplné šifrování je podporována pouze v tuto chvíli při použití Ubuntu 16.04 nebo novější.

### <a name="solution"></a>Řešení

Šifrování funkce pro protokol SMB 3.0 pro Linux byla zavedena v 4.11 jádra. Tato funkce umožňuje připojení Azure sdílené položky z místní nebo jiné oblasti Azure. V době publikování tato funkce byla přeneseny zpět do č. 17.04 Ubuntu a Ubuntu 16.10. Pokud váš klient Linux SMB nepodporuje šifrování, připojení Azure souborů pomocí protokolu SMB 2.1 z Linux virtuálního počítače Azure, který je ve stejném datovém centru jako účet úložiště souborů.

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Nízký výkon na sdílenou složku Azure připojit na virtuální počítač s Linuxem

### <a name="cause"></a>Příčina

Možnou příčinou nízký výkon je zakázána, ukládání do mezipaměti.

### <a name="solution"></a>Řešení

Chcete-li zkontrolovat, zda je zakáže ukládání do mezipaměti, vyhledejte **mezipaměti =** položku. 

**Mezipaměť = none** označuje, že se zakáže ukládání do mezipaměti.  Pomocí příkazu výchozí připojení nebo explicitně přidáním znovu připojit sdílenou složku **mezipaměti = striktní** je povolena možnost příkazu připojit k zajištění, že ukládání do mezipaměti výchozí nebo "striktní" režim ukládání do mezipaměti.

V některých případech **serverino** možnost připojení může způsobit **ls** příkaz ke spuštění stat pro každou položku adresáře. Toto chování výsledkem snížení výkonu, když jste výpis velký adresář. Možnosti připojení můžete zkontrolovat ve vaší **/etc/fstab** položku:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Můžete také zkontrolovat, zda se správnými možnostmi jsou používány spuštěním **sudo připojení | grep cifs** příkazu a kontrola její výstup, jako je například výstupu v následujícím příkladu:

`//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)`

Pokud **mezipaměti = striktní** nebo **serverino** možnost je k dispozici, odpojte a znovu připojit soubory Azure tak, že spustíte příkaz připojení z [dokumentaci](../storage-how-to-use-files-linux.md). Potom znovu zkontrolovat, **/etc/fstab** položka má se správnými možnostmi.

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Časová razítka ztratily v kopírování souborů ze systému Windows do systému Linux

Na platformách, Linux a Unix **cp -p** příkaz se nezdaří, pokud soubor 1 a 2 jsou vlastněny různé uživatele.

### <a name="cause"></a>Příčina

Příznak force **f** v COPYFILE výsledkem provádění **cp -p -f** v systému Unix. Tento příkaz taky nedaří zachovat časové razítko souboru, kterou nevlastníte.

### <a name="workaround"></a>Alternativní řešení

Použijte uživatelský účet úložiště pro kopírování souborů:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="cannot-connect-or-mount-an-azure-file-share"></a>Nelze připojit nebo se připojte Azure sdílené složky

### <a name="cause"></a>Příčina

Obvyklé příčiny tohoto problému patří:


- Používáte nekompatibilní klient distribučního Linux. Doporučujeme že použít následující Linuxových distribucích pro připojení k Azure sdílené složky:

    - Ubuntu Server 14.04 + 
    - RHEL 7 + 
    - CentOS 7 + 
    - Debian 8 
    - openSUSE 13.2 + 
    - SUSE Linux Enterprise Server 12

- CIFS utils nejsou nainstalovány na straně klienta.
- Minimální verze protokolu SMB/CIFS 2.1 není nainstalován klient.
- Klient nepodporuje šifrování protokolu SMB 3.0. Šifrování protokolu SMB 3.0 je k dispozici v Ubuntu 16.4 a novější verze, SUSE 12.3 a novější verze. Další distribuce vyžadují jádra 4.11 a novější verze.
- Pokoušíte se připojit k účtu úložiště přes port TCP 445, která není podporována.
- Pokoušíte se pokusí připojit k Azure sdílené složky z virtuálního počítače Azure a virtuální počítač není umístěný ve stejné oblasti jako účet úložiště.

### <a name="solution"></a>Řešení

Chcete-li problém vyřešit, použijte [nástroj řešení potíží pro soubory Azure připojení chyby v systému Linux](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). Tento nástroj umožňuje ověření klienta se systémem prostředí, zjišťovat konfigurace nekompatibilní klienta, což by způsobilo selhání přístupu pro soubory Azure, poskytuje doporučený postup na samoobslužné opravit a shromažďuje diagnostické trasování.

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: nemůže získat přístup k '&lt;cesta&gt;": chyby vstupu a výstupu

Při pokusu seznam souborů v Azure file sdílet pomocí příkazu ls ls, který příkaz přestane reagovat, když seznam souborů, které jste dojít k následující chybě:

**ls: nemůže získat přístup k '&lt;cesta&gt;": chyby vstupu a výstupu**


### <a name="solution"></a>Řešení
Upgrade jádra systému Linux následující verze, které mají opravu tohoto problému:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Všechny verze, která je větší nebo rovna hodnotě 4.13

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.

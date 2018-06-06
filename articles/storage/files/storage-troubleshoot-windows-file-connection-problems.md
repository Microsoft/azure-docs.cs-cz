---
title: Řešení potíží s Azure soubory v systému Windows | Microsoft Docs
description: Řešení potíží s Azure soubory v systému Windows
services: storage
documentationcenter: ''
author: wmgries
manager: aungoo
editor: tamram
tags: storage
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: wgries
ms.openlocfilehash: 18f594586aa95afaa01bfda712dfc23c8aad3a36
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738543"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Řešení potíží s Azure soubory v systému Windows

V tomto článku jsou uvedeny běžné problémy, které se vztahují k Microsoft Azure Files při připojení klientů se systémem Windows. Umožňuje také možné příčiny a řešení těchto problémů. Kromě řešení problémů s kroky v tomto článku, můžete také použít [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) zajistit, že klienta prostředí systému Windows má správný předpoklady. AzFileDiagnostics automatizuje detekce většina příznaků uvedených v tomto článku a pomáhá nastavit svoje prostředí, abyste získali optimální výkon. Můžete také najít tyto informace [Azure sdíleným Poradce při potížích s](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) , který poskytuje postup vám pomůže s připojením, mapování/připojení Azure sdíleným problémy.


<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Chyba 53, chyba 67 nebo 87 Chyba při připojení nebo odpojení sdílenou složku Azure

Při pokusu připojit sdílenou složku z místní nebo jiném datovém centru, může se zobrazit následující chyby:

- Došlo k systémové chybě 53. Cesta v síti nebyla nalezena.
- Došlo k systémové chybě 67. Síťový název nebyl nalezen.
- Došlo k systémové chybě 87. Parametr je nesprávný.

### <a name="cause-1-unencrypted-communication-channel"></a>Příčina 1: Nezašifrované komunikační kanál

Z bezpečnostních důvodů připojení ke sdílené složky Azure jsou zablokovány, pokud není šifrován komunikační kanál, a pokud není proveden pokus o připojení ze stejného datového centra kde jsou umístěny sdílené složky Azure. Šifrování kanálu komunikace je k dispozici pouze v případě, že uživatele klientského operačního systému podporuje šifrování protokolu SMB.

Windows 8, Windows Server 2012 a novějších verzích každý systém vyjednávat požadavků, které zahrnují protokolu SMB 3.0, který podporuje šifrování.

### <a name="solution-for-cause-1"></a>Řešení pro příčina 1

Připojení z klienta, která provádí jednu z těchto možností:

- Splňuje požadavky na systém Windows 8 a Windows Server 2012 nebo novější verze
- Připojí se z virtuálního počítače ve stejném datovém centru jako účet úložiště Azure, který se používá pro sdílenou složkou Azure

### <a name="cause-2-port-445-is-blocked"></a>2 příčina: Port 445 je blokován.

Systémová chyba 53 nebo systémové chybě 67 může dojít, pokud je blokován port 445 odchozí komunikaci datacentrum Azure Files. Chcete-li zobrazit seznam poskytovatelů internetových služeb, které povolí nebo zakáže přístup z port 445, přejděte na [TechNet](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Zjistit, jestli je z důvodu za zpráva "Chyba systému 53", můžete Portqry dotazovat TCP:445 koncový bod. Pokud koncový bod TCP:445 se zobrazí jako filtrované, TCP port je blokován. Zde je příklad dotazu:

  `g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

Pokud je port TCP 445 blokovaný pravidlem na síťové cestě, uvidíte následující výsledek:

  `TCP port 445 (microsoft-ds service): FILTERED`

Další informace o použití Portqry najdete v tématu [popisujícím nástroj příkazového řádku Portqry.exe](https://support.microsoft.com/help/310099).

### <a name="solution-for-cause-2"></a>Řešení pro příčina 2

Práce s vaším IT oddělením otevřít port 445 odchozí do [rozsahy Azure IP](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="cause-3-ntlmv1-is-enabled"></a>Příčina 3: NTLMv1 je povolen.

Systémová chyba 53 nebo systémové chybě 87 může dojít, pokud je povoleno NTLMv1 komunikace na straně klienta. Soubory Azure podporuje jenom ověřování NTLMv2. S NTLMv1 povoleno vytvoří klienta méně bezpečné. Proto je blokován komunikace pro Azure Files. 

Pokud chcete zjistit, jestli se jedná o příčinu chyby, ověřte, zda následující podklíč registru je nastavena na hodnotu 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Další informace najdete v tématu [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) tématu na webu TechNet.

### <a name="solution-for-cause-3"></a>Řešení pro příčina 3

Vrátit zpět **LmCompatibilityLevel** hodnota na výchozí hodnotu 3 v následujícím podklíči registru:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Chyba 1816 "není dostatek kvóty je k dispozici pro zpracování tohoto příkazu" Pokud zkopírujete do Azure sdílené složky

### <a name="cause"></a>Příčina

Při dosažení horní limit počtu souběžných otevřete obslužných rutin, které jsou povoleny pro soubor v počítači, kde připojené sdílené složky dochází k chybě 1816.

### <a name="solution"></a>Řešení

Snižte počet souběžných otevřených obslužných rutin ukončením některé obslužné rutiny a poté opakujte. Další informace najdete v tématu [kontrolní seznam výkonu a škálovatelnosti služby Microsoft Azure Storage](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Pomalé kopírování souborů do a z Azure souborů v systému Windows

Při pokusu o přenos souborů do služby Azure File, může se zobrazit nízký výkon.

- Pokud nemáte konkrétní požadavky minimální velikost vstupně-výstupních operací, doporučujeme použít 1 MB jako velikost vstupně-výstupní operace pro zajištění optimálního výkonu.
-   Pokud znáte konečné velikosti souboru, který bude rozšiřovat s zápisy a váš software nebude mít problémy s kompatibilitou, pokud unwritten tail na soubor obsahuje nuly, potom nastavte velikost souboru předem místo provedení každém zápisu rozšíření zápisu.
-   Použijte metodu pravé kopie:
    -   Použití [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) pro všechny přenosy mezi dvěma sdílenými složkami.
    -   Použití [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) mezi sdílené složky na místním počítači.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Důležité informace pro Windows 8.1 nebo Windows Server 2012 R2

Pro klienty se systémem Windows 8.1 nebo Windows Server 2012 R2, ujistěte se, že [KB3114025](https://support.microsoft.com/help/3114025) je nainstalována oprava hotfix. Tato oprava hotfix zvyšuje výkon vytvořit a zavřete obslužné rutiny.

Můžete spustit následující skript, který chcete zkontrolovat, zda byla nainstalována oprava hotfix:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Pokud je nainstalována oprava hotfix, zobrazí se následující výstup:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Bitové kopie systému Windows Server 2012 R2 v Azure Marketplace nainstalována oprava hotfix KB3114025 nainstalované ve výchozím nastavení, od prosince 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer"></a>Žádná složka s písmenem jednotky v **tento počítač**

Pokud namapujete sdílenou složku Azure jako správce pomocí příkazu net use, sdílené složky pravděpodobně chybí.

### <a name="cause"></a>Příčina

Ve výchozím prohlížeči souborů Windows nejde spustit jako správce. Pokud spustíte příkazu net use z příkazového řádku pro správu, mapovat síťovou jednotku jako správce. Protože připojené jednotky jsou zaměřené na uživatele, nejsou zobrazeny uživatelský účet, který je přihlášen jednotky, pokud jsou připojeny v jiného uživatelského účtu.

### <a name="solution"></a>Řešení
Připojte sdílenou složku z příkazového řádku bez oprávnění správce. Alternativně můžete postupovat podle [Toto téma TechNet](https://technet.microsoft.com/library/ee844140.aspx) nakonfigurovat **EnableLinkedConnections** hodnotu registru.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Pomocí příkazu net use selže, pokud účet úložiště obsahuje lomítkem

### <a name="cause"></a>Příčina

Pomocí příkazu net use interpretuje jako možnost příkazového řádku lomítkem (/). Pokud vaše uživatelské jméno účtu začíná lomítkem, mapování jednotky se nezdaří.

### <a name="solution"></a>Řešení

Chcete-li vyřešit tento problém můžete použít některý z následujících kroků:

- Spusťte následující příkaz PowerShellu:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc" `

  Z dávkového souboru můžete spustit příkaz tímto způsobem:

  `Echo new-smbMapping ... | powershell -command –`

- Dvojité uvozovky klíč, který chcete vyřešit tento problém – uvedena, pokud je první znak dopředné lomítko. Pokud se jedná, použijte interaktivní režim a zadejte své heslo samostatně nebo znovu vygenerovat klíče získat klíč, který nezačíná lomítkem.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Aplikace nebo služba nedostupná připojenou jednotku Azure Files

### <a name="cause"></a>Příčina

Na uživatele jsou připojené jednotky. Pokud vaše aplikace nebo služba běží pod účtem uživatele jiný než ten, který připojené jednotky, aplikace se nezobrazí na jednotku.

### <a name="solution"></a>Řešení

Použijte jedno z následujících řešení:

-   Připojte jednotku ze stejného uživatelského účtu, který obsahuje aplikace. Můžete použít nástroje, jako je PsExec.
- Předejte název účtu úložiště a klíč uživatelské jméno a heslo parametry sítě, použijte příkaz.
- Pomocí příkazu cmdkey přidejte tato pověření do správce přihlašovacích údajů. Proveďte to z příkazového řádku v kontextu účtu služby, prostřednictvím interaktivní přihlášení nebo pomocí runas.
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Mapovat sdílenou složku přímo bez použití mapovaná jednotka písmeno. Některé aplikace nemusí znovu písmeno jednotky správně, takže pomocí úplné cesty UNC může být spolehlivější. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Až budete postupovat podle těchto pokynů, při spuštění příkazu net use pro účet služby systému nebo síti může zobrazit následující chybová zpráva: "došlo k systémové chybě 1312. Zadané přihlašovací relace neexistuje. Ho může již byla ukončena." Pokud k tomu dojde, ujistěte se, že zadané uživatelské jméno, který je předán příkazu net use obsahuje informace o doméně (například: "[název účtu úložiště]. file.core.windows .net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Chyba "Kopírujete soubor do cílového umístění, která nepodporuje šifrování"

Při kopírování souboru přes síť, je soubor dešifrovat na zdrojovém počítači, odesílané informace ve formátu prostého textu a znovu zašifrována v cílovém umístění. Ale když se pokoušíte zkopírovat šifrovaný soubor se může zobrazit následující chyby: "Jsou kopírování souboru do cílového umístění, které nepodporuje šifrování."

### <a name="cause"></a>Příčina
Tomuto problému může dojít, pokud používáte systému souborů EFS (ENCRYPTING File System). Šifrované nástrojem BitLocker soubory je možné zkopírovat do Azure Files. Soubory Azure nepodporuje systém souborů EFS systému souborů NTFS.

### <a name="workaround"></a>Alternativní řešení
Kopírování souboru přes síť, můžete ji nejprve dešifrovat. Použijte jednu z následujících metod:

- Použití **zkopírujte /d** příkaz. To umožňuje šifrované soubory uložit jako dešifrované soubory v cílovém umístění.
- Nastavte následující klíč registru:
  - Path = HKLM\Software\Policies\Microsoft\Windows\System
  - Typ hodnoty = DWORD
  - Název = CopyFileAllowDecryptedRemoteDestination
  - Hodnota = 1

Upozorňujeme, že nastavení klíče registru ovlivní všechny operace kopírování, které jsou vytvářeny do sdílené síťové složky.

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.
Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.

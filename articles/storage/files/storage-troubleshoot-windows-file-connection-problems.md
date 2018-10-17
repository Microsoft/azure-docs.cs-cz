---
title: Řešení potíží s Azure Files problémy ve Windows | Dokumentace Microsoftu
description: Řešení potíží se soubory Azure ve Windows
services: storage
author: jeffpatt24
tags: storage
ms.service: storage
ms.topic: article
ms.date: 05/11/2018
ms.author: jeffpatt
ms.component: files
ms.openlocfilehash: a0a330d3ea7362ffabb20a5d390cee87cbf7d8ff
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49365401"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Řešení potíží s Azure Files problémy ve Windows

Tento článek uvádí běžné problémy, které se vztahují k Microsoft Azure Files, když se připojíte z klientů Windows. Poskytuje také možné příčiny a řešení těchto problémů. Kromě použijte kroky v tomto článku, můžete také použít [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) a ujistěte se, že v prostředí klienta Windows mají správné požadavky. AzFileDiagnostics automatizuje zjišťování většiny příznaků uvedených v tomto článku a pomáhá nastavení prostředí, abyste získali optimální výkon. Můžete také najít tyto informace [Azure sdíleným složkám Poradce při potížích](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) , který vysvětluje, jak vám pomoci problémy připojení/mapování nebo připojení Azure sdíleným složkám.


<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Chyba 53, chyba 67 nebo 87 Chyba při připojování nebo odpojování sdílené složky Azure

Při pokusu o připojení sdílené složky v místním nebo z jiného datového centra, může dojít k následujícím chybám:

- Došlo k chybě systému 53. Cesta sítě nebyla nalezena.
- Došlo k chybě systému 67. Síťový název nelze nalézt.
- Došlo k chybě systému 87. Parametr je nesprávný.

### <a name="cause-1-unencrypted-communication-channel"></a>1. příčina: Nešifrované komunikační kanál

Z bezpečnostních důvodů připojení sdílených složek Azure jsou blokovány, pokud není šifrovaný komunikační kanál, a pokud se pokus o připojení není proveden ze stejné datové centrum, kde jsou umístěné sdílených složek Azure. Nezašifrované připojení ve stejném datacentru je taky možné zablokovat Pokud [vyžadovat zabezpečený přenos](https://docs.microsoft.com/en-us/azure/storage/common/storage-require-secure-transfer) pro účet úložiště je povolené nastavení. Komunikační kanál šifrování je k dispozici pouze v případě, že uživatele klientský operační systém podporuje šifrování protokolu SMB.

Windows 8, Windows Server 2012 a novějších verzích každý systém vyjednávat požadavky, které obsahují protokolu SMB 3.0, který podporuje šifrování.

### <a name="solution-for-cause-1"></a>Řešení příčiny 1

1. Ověřte, [vyžadovat zabezpečený přenos](https://docs.microsoft.com/en-us/azure/storage/common/storage-require-secure-transfer) je zakázáno v účtu úložiště.
2. Připojení z klienta, která provádí jednu z následujících akcí:

    - Splňuje požadavky na systém Windows 8 a Windows Server 2012 nebo novější verze
    - Připojení z virtuálního počítače ve stejném datacentru jako účet služby Azure storage, který se používá pro sdílené složky Azure

### <a name="cause-2-port-445-is-blocked"></a>2. příčina: Port 445 blokovaný.

Systémová chyba 53 nebo 67 systémové chybě může dojít, pokud 445 odchozí komunikace přes port na datové centrum Azure Files je blokovaná. Chcete-li zobrazit souhrn poskytovatelů internetových služeb, které povolí nebo zakáže přístup z portu 445, přejděte na [TechNet](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Informace o tom, zda se jedná o důvodem takového zpráva "Chyba 53 systému", můžete k dotazování na koncový bod TCP:445 Portqry. Pokud koncový bod TCP:445 se zobrazí, jak jsou vyfiltrovaná, je Zablokovaný TCP port. Zde je příklad dotazu:

  `g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

Pokud je port TCP 445 blokovaný pravidlem na síťové cestě, uvidíte následující výsledek:

  `TCP port 445 (microsoft-ds service): FILTERED`

Další informace o použití Portqry najdete v tématu [popisujícím nástroj příkazového řádku Portqry.exe](https://support.microsoft.com/help/310099).

### <a name="solution-for-cause-2"></a>Řešení příčiny 2

Práce s vaším IT oddělením pro otevření portu 445 odchozí do [rozsahy IP adres Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="cause-3-ntlmv1-is-enabled"></a>3. příčina: NTLMv1 je povolená.

Systémová chyba 53 nebo systémové chybě 87 může dojít, pokud je povolená komunikace NTLMv1 na straně klienta. Služba soubory Azure podporuje jenom ověřování NTLMv2. S NTLMv1 povolené vytvoří klienta méně bezpečné. Proto se zablokovat komunikaci pro soubory Azure. 

Pokud chcete zjistit, zda je příčinou chyby, ověřte, že následující podklíč registru je nastavena na hodnotu 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Další informace najdete v tématu [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) tématu na webu TechNet.

### <a name="solution-for-cause-3"></a>Řešení pro příčina 3

Vrátit zpět **LmCompatibilityLevel** hodnota, která má výchozí hodnotu 3 v následujícím podklíči registru:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Chyba 1816 "Nedostatečná kvóta je k dispozici pro zpracování tohoto příkazu" při kopírování do sdílené složky Azure

### <a name="cause"></a>Příčina

Chyba 1816 se stane při dosažení horní limit počtu souběžných otevřených popisovačů, které jsou povoleny pro soubor na počítači, kde je připojení sdílené složky.

### <a name="solution"></a>Řešení

Snižte počet souběžných otevřených popisovačů ukončením některé obslužné rutiny a pak zkuste to znovu. Další informace najdete v tématu [kontrolní seznam výkonu a škálovatelnosti Microsoft Azure Storage](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Zpomalit kopírování souborů do a z Azure souborů ve Windows

Zobrazí se pravděpodobně nízký výkon při pokusu o přenos souborů do služby Azure File.

- Pokud nemáte konkrétní požadavek minimální velikost vstupně-výstupních operací, doporučujeme použít 1 MiB jako velikost vstupně-výstupní operace pro zajištění optimálního výkonu.
-   Pokud znáte konečné velikosti souboru, který se rozšíření s zápisy a softwaru nemá problémy s kompatibilitou, když nepsaná tail na tento soubor obsahuje nuly, nastavte velikost souboru předem místo provedení při každém zápisu rozšiřování zápisu.
-   Použijte metodu pravé kopie:
    -   Použití [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) pro všechny přenosy mezi dvěma sdílenými složkami souborů.
    -   Použití [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) mezi sdílené složky na místním počítači.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Důležité informace pro Windows 8.1 nebo Windows Server 2012 R2

Pro klienty se systémem Windows 8.1 nebo Windows Server 2012 R2, ujistěte se, že [KB3114025](https://support.microsoft.com/help/3114025) je nainstalována oprava hotfix. Tato oprava hotfix zlepšuje výkon při vytvoření a zavřete obslužné rutiny.

Můžete spustit následující skript, který zkontrolujte, zda byla nainstalována oprava hotfix:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Pokud je nainstalována oprava hotfix, se zobrazí následující výstup:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Bitové kopie systému Windows Server 2012 R2 na webu Azure Marketplace nainstalována oprava hotfix nainstalovaná ve výchozím nastavení, KB3114025 od prosince 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer"></a>Žádná složka s písmenem jednotky v **tento počítač**

Při mapování sdílené složky Azure jako správce pomocí příkazu net use, sdílené složky pravděpodobně chybí.

### <a name="cause"></a>Příčina

Ve výchozím nastavení Průzkumníka souborů Windows nelze spustit jako správce. Pokud spustíte příkaz net use z příkazového řádku pro správu, mapování síťové jednotky jako správce. Protože připojené jednotky jsou zaměřené na uživatele, není zobrazena uživatelský účet, který je přihlášen jednotky, pokud jsou připojené pod účtem jiného uživatele.

### <a name="solution"></a>Řešení
Připojte sdílenou složku z příkazového řádku bez oprávnění správce. Alternativně můžete postupovat podle [v tomto tématu na webu TechNet](https://technet.microsoft.com/library/ee844140.aspx) ke konfiguraci **EnableLinkedConnections** hodnotu registru.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Příkaz net use selže, pokud účet úložiště obsahuje lomítko

### <a name="cause"></a>Příčina

Příkaz net use interpretuje jako parametr příkazového řádku lomítkem (/). Pokud vaše uživatelské jméno účtu začíná znakem lomítka, mapování jednotky se nezdaří.

### <a name="solution"></a>Řešení

Chcete-li vyřešit tento problém můžete použít některý z následujících kroků:

- Spusťte následující příkaz PowerShellu:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc" `

  Z dávkového souboru můžete spustit příkaz tímto způsobem:

  `Echo new-smbMapping ... | powershell -command –`

- Vložení uvozovek kolem klíč, který chcete vyřešit tento problém – pokud je první znak lomítka. Pokud se jedná, použijte interaktivní režim a zadejte své heslo samostatně nebo znovu vygenerovat klíče získat klíč, který nezačíná lomítkem.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Aplikace nebo služba nemá přístup k připojené jednotce soubory Azure

### <a name="cause"></a>Příčina

Disky jsou připojené na uživatele. Pokud vaše aplikace nebo služba běží pod účtem uživatele jiný než ten, který připojené jednotce, aplikace zobrazen na jednotce.

### <a name="solution"></a>Řešení

Použijte jedno z následujících řešení:

-   Připojte jednotku ze stejného uživatelského účtu, který obsahuje aplikace. Můžete použít nástroje, jako je PsExec.
- Předejte název účtu úložiště a klíč uživatelské jméno a heslo parametry sítě použijte příkaz.
- Chcete-li přidat pověření do správce přihlašovacích údajů pomocí příkazu cmdkey. Proveďte z příkazového řádku v kontextu účtu služby, prostřednictvím interaktivního přihlášení nebo pomocí runas.
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Namapujte sdílenou složku přímo bez použití písmenem mapovaná jednotka. Některé aplikace nemusí znovu připojit k písmenu jednotky správně, tak pomocí úplné cesty UNC může být spolehlivější. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Až budete postupovat podle těchto pokynů, můžete při spuštění příkazu net use pro účet služby systému/network může zobrazí následující chybová zpráva: "systém 1312 došlo k chybě. Zadané přihlašovací relace neexistuje. To může již byla ukončena." Pokud k tomu dojde, ujistěte se, že uživatelské jméno, který je předán příkazu net use obsahuje informace o doméně (například: "[název účtu úložiště]. file.core.windows .net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Chyba "Jsou kopírování souboru do cíle, které nepodporuje šifrování"

Při kopírování v síti, je soubor dešifrovat na zdrojovém počítači, přenášet ve formátu prostého textu a znovu zašifrována v cílovém umístění. Však může zobrazit následující chyba, když se snažíte zkopírujte zašifrovaného souboru: "Soubor kopírujete do cílového umístění, která nepodporuje šifrování."

### <a name="cause"></a>Příčina
Tomuto problému může dojít, pokud používáte systém souborů EFS (ENCRYPTING File System). Šifrované nástrojem BitLocker soubory je možné zkopírovat do soubory Azure. Soubory Azure nepodporuje systém souborů EFS systému souborů NTFS.

### <a name="workaround"></a>Alternativní řešení
Kopírování souboru přes síť, můžete jej nejprve dešifrovat. Použijte jednu z následujících metod:

- Použití **zkopírujte /d** příkazu. To umožňuje šifrované soubory, které chcete uložit jako dešifrované soubory v cílovém umístění.
- Nastavte následující klíč registru:
  - Path = HKLM\Software\Policies\Microsoft\Windows\System
  - Typ hodnoty = DWORD
  - Název = CopyFileAllowDecryptedRemoteDestination
  - Hodnota = 1

Mějte na paměti, že nastavení klíče registru ovlivňuje všechny operace kopírování, které jsou provedeny do sdílené síťové složky.

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.
Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychlé vyřešení problému.

---
title: Poradce při potížích se soubory Azure ve Windows | Dokumenty společnosti Microsoft
description: Řešení problémů se soubory Azure ve Windows
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 3237fe7d87ad058f255d1c77cb6d814bcd1c292e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262243"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Řešení potíží se službou Azure Files ve Windows

Tento článek uvádí běžné problémy, které souvisejí se soubory Microsoft Azure při připojení z klientů Windows. Poskytuje také možné příčiny a řešení těchto problémů. Kromě kroků řešení potíží v tomto článku můžete také použít [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) k zajištění, že klientské prostředí systému Windows má správné požadavky. AzFileDiagnostics automatizuje detekci většiny příznaků uvedených v tomto článku a pomáhá nastavit prostředí pro dosažení optimálního výkonu. Tyto informace najdete také v [Poradci při potížích se sdílenými složkami Azure Files,](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) který poskytuje kroky, které vám pomohou s problémy s připojením/mapováním/montáží sdílených složek Azure Files.

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Chyba 5 při připojení sdílené složky Azure

Při pokusu o připojení sdílené složky se může zobrazit následující chyba:

- Došlo k systémové chybě 5. Přístup byl zamítnut.

### <a name="cause-1-unencrypted-communication-channel"></a>Příčina 1: Nešifrovaný komunikační kanál

Z bezpečnostních důvodů se připojení ke sdíleným složkám Azure blokují, když komunikační kanál není šifrovaný a když k pokusu o připojení nedošlo ze stejného datacentra, ve kterém se sdílená složka Azure nachází. Nešifrovaná připojení ze stejného datacentra se můžou blokovat také v případě, že je pro účet úložiště povolené nastavení [Vyžadovat zabezpečený přenos](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer). Šifrovaný komunikační kanál je k dispozici pouze v případě, že klientský operační systém uživatele podporuje šifrování protokolu SMB.

Windows 8, Windows Server 2012 a novější verze těchto systémů vyjednávají požadavky zahrnující protokol SMB 3.0, který podporuje šifrování.

### <a name="solution-for-cause-1"></a>Řešení 1. příčiny

1. Připojte se z klienta, který podporuje šifrování SMB (Windows 8, Windows Server 2012 nebo novější) nebo se připojte z virtuálního počítače ve stejném datovém centru jako účet úložiště Azure, který se používá pro sdílenou složku Azure.
2. Ověřte, zda je v účtu úložiště zakázáno [nastavení Požadováno zabezpečeného přenosu,](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) pokud klient nepodporuje šifrování SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Příčina 2: V účtu úložiště jsou povolena pravidla virtuální sítě nebo brány firewall. 

Pokud jsou pro účet úložiště nakonfigurovaná pravidla virtuální sítě nebo brány firewall, síťovému provozu se odepře přístup, dokud se nepovolí přístup z virtuální sítě nebo IP adresy klienta.

### <a name="solution-for-cause-2"></a>Řešení 2. příčiny

Ověřte, že jsou pro účet úložiště správně nakonfigurovaná pravidla brány firewall a virtuální sítě. Pokud chcete otestovat, jestli problém způsobují pravidla brány firewall nebo virtuální sítě, dočasně změňte nastavení pro účet úložiště na **Povolit přístup ze všech sítí**. Další informace najdete v tématu [Konfigurace virtuálních sítí a bran firewall Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>Příčina 3: Oprávnění na úrovni sdílené složky jsou nesprávná při použití ověřování založeného na identitě

Pokud uživatelé přistupují ke sdílené složce Azure pomocí ověřování služby Active Directory (AD) nebo Služby Azure Active Directory Domain Services (Azure AD DS), přístup ke sdílené složce se nezdaří s chybou "Přístup byl odepřen", pokud jsou oprávnění na úrovni sdílené složky nesprávná. 

### <a name="solution-for-cause-3"></a>Řešení pro příčinu 3

Informace o aktualizaci oprávnění na úrovni sdílené složky naleznete v tématu [Přiřazení přístupových oprávnění k identitě](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-domain-service-enable#2-assign-access-permissions-to-an-identity).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Chyba 53, chyba 67 nebo chyba 87 při připojení nebo odpojit sdílenou složku Azure

Při pokusu o připojení sdílené složky z místního nebo jiného datového centra se mohou zobrazit následující chyby:

- Došlo k systémové chybě 53. Síťová cesta se nenašla.
- Došlo k systémové chybě 67. Název sítě se nenašel.
- Došlo k systémové chybě 87. Parametr je nesprávný.

### <a name="cause-1-port-445-is-blocked"></a>Příčina 1: Port 445 je blokován

Systémová chyba 53 nebo systémová chyba 67 může dojít, pokud port 445 odchozí komunikace do datového centra Azure Files je blokován. Souhrn poskytovatelů internetových služeb, kteří umožňují nebo neumožňují přístup z portu 445, najdete na webu [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Chcete-li zkontrolovat, zda brána firewall nebo isp blokuje port 445, použijte nástroj [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) nebo `Test-NetConnection` rutinu. 

Pokud chcete `Test-NetConnection` rutinu použít, musí být nainstalovaný modul Azure PowerShell, přečtěte si další informace [najdete v tématu Instalace modulu Azure PowerShell.](/powershell/azure/install-Az-ps) Nezapomeňte nahradit `<your-storage-account-name>` a `<your-resource-group-name>` odpovídajícími názvy pro váš účet úložiště.

   
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
    
Pokud připojení proběhne úspěšně, měl by se zobrazit následující výstup:
    
  
    ComputerName     : <your-storage-account-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
 

> [!Note]  
> Výše uvedený příkaz vrátí aktuální IP adresu účtu úložiště. Není zaručeno, že tato IP adresa zůstane stejná, a kdykoli se může změnit. Nekódujte pevně tuto IP adresu do skriptů ani do konfigurace brány firewall.

### <a name="solution-for-cause-1"></a>Řešení 1. příčiny

#### <a name="solution-1---use-azure-file-sync"></a>1. řešení – Použití Synchronizace souborů Azure
Azure File Sync může přeměnit váš místní Windows Server na rychlou mezipaměť sdílené složky Azure. K místnímu přístupu k datům můžete použít libovolný protokol, který je k dispozici v systému Windows Server, včetně protokolů SMB, NFS a FTPS. Azure File Sync funguje přes port 443 a lze tak použít jako řešení pro přístup k souborům Azure od klientů, kteří mají port 445 blokován. [Přečtěte si, jak nastavit Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-extend-servers).

#### <a name="solution-2---use-vpn"></a>2. řešení – Použití sítě VPN
Nastavením VPN pro váš konkrétní účet úložiště bude provoz procházet zabezpečeným tunelem na rozdíl od internetu. Podle [pokynů nastavte VPN](storage-files-configure-p2s-vpn-windows.md) pro přístup k souborům Azure z Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>3. řešení – Odblokování portu 445 s pomocí poskytovatele internetových služeb nebo správce IT
Spolupracujte se svým IT oddělením nebo ispem a otevřete port 445 odchozích [do rozsahů IP adres Azure](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>4. řešení – Použití nástrojů založených na rozhraní REST API, jako jsou Průzkumník služby Storage nebo PowerShell
Azure Files také podporuje REST kromě SMB. Rest přístup funguje přes port 443 (standardní tcp). Existují různé nástroje, které jsou napsány pomocí rozhraní REST API, které umožňují bohaté uživatelské rozhraní. [Průzkumník úložiště](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) je jedním z nich. [Stáhněte si a nainstalujte Průzkumníka úložiště](https://azure.microsoft.com/features/storage-explorer/) a připojte se ke sdílené složce podporované soubory Azure. Můžete také použít [PowerShell,](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-powershell) který také uživatel REST API.

### <a name="cause-2-ntlmv1-is-enabled"></a>Příčina 2: NTLMv1 je povolena

Pokud je v klientovi povolena komunikace NTLMv1, může dojít k systémové chybě 53 nebo systémové chybě 87. Služba Azure Files podporuje pouze ověřování pomocí protokolu NTLM v2. Klient s povoleným protokolem NTLM v1 je méně zabezpečený. Proto se blokuje komunikace služby Azure Files. 

Pokud chcete určit, jestli je toto příčinou chyby, ověřte, že je následující podklíč registru nastavený na hodnotu 3.

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Další informace najdete v tématu [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) na webu TechNet.

### <a name="solution-for-cause-2"></a>Řešení 2. příčiny

V následujícím podklíči registru vraťte hodnotu **LmCompatibilityLevel** na výchozí hodnotu 3:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Chyba 1816 "Není k dispozici dostatečná kvóta pro zpracování tohoto příkazu" při kopírování do sdílené složky Azure

### <a name="cause"></a>Příčina

Chyba 1816 se stane, když dosáhnete horní hranice souběžných otevřených popisovačů, které jsou povoleny pro soubor v počítači, kde je sdílená složka souboru připojena.

### <a name="solution"></a>Řešení

Snižte počet souběžných otevřených popisovačů zavřením některých popisovačů a opakujte akci. Další informace najdete v tématu [Microsoft Azure Storage performance and scalability checklist](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Chcete-li zobrazit otevřené popisovače pro sdílenou složku, adresář nebo soubor, použijte rutinu [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell.  

Chcete-li zavřít otevřené popisovače pro sdílenou složku, adresář nebo soubor, použijte rutinu [Prostředí PowerShell Close-AzStorageFileHandle.](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle)

> [!Note]  
> Rutiny Get-AzStorageFileHandle a Close-AzStorageFileHandle jsou součástí modulu Az PowerShell verze 2.4 nebo novější. Informace o instalaci nejnovějšího modulu Az PowerShell najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

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
Při pokusu o odstranění souboru se může zobrazit následující chyba:

Zadaný prostředek je označen k odstranění klientem SMB.

### <a name="cause"></a>Příčina
K tomuto problému obvykle dochází, pokud soubor nebo adresář má otevřený popisovač. 

### <a name="solution"></a>Řešení

Pokud klienti SMB zavřeli všechny otevřené popisovače a problém přetrvává, proveďte následující kroky:

- Pomocí rutiny [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell zobrazte otevřené popisovače.

- Pomocí rutiny [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell zavřete otevřené popisovače. 

> [!Note]  
> Rutiny Get-AzStorageFileHandle a Close-AzStorageFileHandle jsou součástí modulu Az PowerShell verze 2.4 nebo novější. Informace o instalaci nejnovějšího modulu Az PowerShell najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Pomalé kopírování souborů do služby Azure Files a z ní ve Windows

Při pokusu o přenos souborů do služby Azure File se může zobrazit pomalý výkon.

- Pokud nemáte konkrétní požadavek na minimální velikost vstupně-va, doporučujeme použít 1 MiB jako velikost vstupně-v.v.,Pro optimální výkon.
-   Pokud znáte konečnou velikost souboru, který rozšiřujete pomocí zápisů, a software nemá problémy s kompatibilitou, když nepsaný ocas v souboru obsahuje nuly, nastavte předem velikost souboru namísto toho, aby každý zápis byl rozšiřujícím zápisem.
-   Použijte správnou metodu kopírování:
    -   Pro jakýkoli přenos mezi dvěma sdílenými složkami použijte [AzCopy.](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
    -   Použijte [Robocopy](/azure/storage/files/storage-files-deployment-guide#robocopy) mezi sdílenými složkami v místním počítači.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Důležité informace o Windows 8.1 nebo Windows Server 2012 R2

U klientů se systémem Windows 8.1 nebo Windows Server 2012 R2 zkontrolujte, zda je nainstalovaná oprava hotfix [KB3114025.](https://support.microsoft.com/help/3114025) Tato oprava hotfix zlepšuje výkon vytváření a zavření úchytů.

Chcete-li zkontrolovat, zda byla oprava hotfix nainstalována, můžete spustit následující skript:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Pokud je nainstalována oprava hotfix, zobrazí se následující výstup:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Image Windows Server 2012 R2 na Webu Azure Marketplace mají opravu hotfix KB3114025 nainstalovanou ve výchozím nastavení od prosince 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Žádná složka s písmenem jednotky v "Tento počítač" nebo "Tento počítač"

Pokud mapujete sdílenou složku Azure jako správce pomocí net use, zdá se, že sdílená složka chybí.

### <a name="cause"></a>Příčina

Ve výchozím nastavení se Průzkumník souborů systému Windows nespustí jako správce. Pokud spustíte síťové použití z příkazového řádku pro správu, namapujete síťovou jednotku jako správce. Vzhledem k tomu, že mapované jednotky jsou zaměřeny na uživatele, uživatelský účet, který je přihlášen, nezobrazuje jednotky, pokud jsou připojeny pod jiným uživatelským účtem.

### <a name="solution"></a>Řešení
Připojte sdílenou složku z příkazového řádku bez oprávnění správce. Případně můžete podle [tohoto tématu technetu](https://technet.microsoft.com/library/ee844140.aspx) nakonfigurovat hodnotu registru **EnableLinkedConnections.**

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Příkaz Net use se nezdaří, pokud účet úložiště obsahuje lomítko

### <a name="cause"></a>Příčina

Příkaz net use interpretuje lomítko (/) jako možnost příkazového řádku. Pokud název uživatelského účtu začíná lomítkem, mapování jednotky se nezdaří.

### <a name="solution"></a>Řešení

K řešení problému můžete použít některý z následujících kroků:

- Spusťte následující příkaz PowerShellu:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  Z dávkového souboru můžete příkaz spustit takto:

  `Echo new-smbMapping ... | powershell -command –`

- Obejděte klíč s uvozovkami, abyste tento problém vyřešili– pokud lomítko není prvním znakem. Pokud ano, použijte interaktivní režim a zadejte heslo samostatně, nebo obnovte klíče, abyste získali klíč, který nezačíná lomítkem.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Aplikace nebo služba nemá přístup k připojené jednotce Azure Files

### <a name="cause"></a>Příčina

Jednotky jsou připojeny na uživatele. Pokud je aplikace nebo služba spuštěna pod jiným uživatelským účtem, než který byl připojen k jednotce, aplikace jednotku neuvidí.

### <a name="solution"></a>Řešení

Použijte jeden z následujících řešení:

-   Připojte jednotku ze stejného uživatelského účtu, který obsahuje aplikaci. Můžete použít nástroj, jako je PsExec.
- Předajte název a klíč účtu úložiště v parametrech uživatelského jména a hesla příkazu net use.
- Pomocí příkazu cmdkey přidejte pověření do Správce přihlašovacích údajů. Proveďte to z příkazového řádku v kontextu účtu služby, a to buď prostřednictvím interaktivního přihlášení, nebo pomocí . `runas`
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Mapuje sdílenou složku přímo bez použití namapovaného písmene jednotky. Některé aplikace se nemusí správně připojit k písmenu jednotky, takže použití úplné cesty UNC může být spolehlivější. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Po provedení těchto pokynů se může při spuštění čistého použití účtu systémových/síťových služeb zobrazit následující chybová zpráva: Došlo k systémové chybě 1312. Zadaná přihlašovací relace neexistuje. Možná už byla ukončena." Pokud k tomu dojde, ujistěte se, že uživatelské jméno, které je předáno čistému použití, obsahuje informace o doméně (například: "[název účtu úložiště].file.core.windows.net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Chyba "Kopírujete soubor do cíle, který nepodporuje šifrování"

Při kopírování souboru do sítě je soubor dešifrován ve zdrojovém počítači, přenášen ve formátu prostého textu a znovu zašifrován v cílovém umístění. Při pokusu o kopírování šifrovaného souboru se však může zobrazit následující chyba: "Kopírujete soubor do cíle, který nepodporuje šifrování."

### <a name="cause"></a>Příčina
K tomuto problému může dojít, pokud používáte systém souborů EFS (Encrypting File System). Soubory zašifrované nástrojem BitLocker lze zkopírovat do souborů Azure. Soubory Azure však nepodporuje systém souborů NTFS EFS.

### <a name="workaround"></a>Alternativní řešení
Chcete-li soubor zkopírovat přes síť, musíte jej nejprve dešifrovat. Použijte jednu z následujících metod:

- Použijte příkaz **copy /d.** To umožňuje šifrované soubory, které mají být uloženy jako dešifrované soubory v cíli.
- Nastavte následující klíč registru:
  - Cesta = HKLM\Software\Policies\Microsoft\Windows\System
  - Typ hodnoty = DWORD
  - Název = CopyFileAllowDecryptedRemoteDestination
  - Hodnota = 1

Uvědomte si, že nastavení klíče registru ovlivňuje všechny operace kopírování, které jsou provedeny pro sdílené síťové složky.

## <a name="slow-enumeration-of-files-and-folders"></a>Pomalý výčet souborů a složek

### <a name="cause"></a>Příčina

K tomuto problému může dojít, pokud není dostatek mezipaměti v klientském počítači pro velké adresáře.

### <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, úprava hodnoty registru **DirectoryCacheEntrySizeMax** tak, aby bylo možné ukládání větších výpisů adresářů do mezipaměti v klientském počítači:

- Umístění: HKLM\System\CCS\Services\Lanmanworkstation\Parametry
- Hodnota hříva: DirectoryCacheEntrySizeMax 
- Typ hodnoty:DWORD
 
 
Můžete ji například nastavit na 0x100000 a zjistit, zda se výkon zlepší.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-aad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Chyba AadDsTenantNotFound při povolení ověřování služby Azure Active Directory Domain Service (AAD DS) pro soubory Azure "Nelze najít aktivní klienty s id klienta aad-tenant-id"

### <a name="cause"></a>Příčina

Chyba AadDsTenantNotFound se stane, když se pokusíte [povolit ověřování Azure Active Directory Domain Services (Azure AD DS) na soubory Azure](storage-files-identity-auth-active-directory-domain-service-enable.md) na účet úložiště, kde [aad domain service (AAD DS)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) není vytvořen v tenantovi AAD přidruženého předplatného.  

### <a name="solution"></a>Řešení

Povolte AAD DS v tenantovi AAD předplatného, do kterého se nasadí váš účet úložiště. K vytvoření spravované domény potřebujete oprávnění správce klienta AAD. Pokud nejste správcem klienta Azure AD, obraťte se na správce a postupujte podle podrobných pokynů k [povolení služby Azure Active Directory Domain Services pomocí portálu Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="error-system-error-1359-has-occurred-an-internal-error-received-over-smb-access-to-file-shares-with-azure-active-directory-domain-service-aad-ds-authentication-enabled"></a>Došlo k chybě systémová chyba 1359. Přes přístup SMB ke sdíleným souborům pomocí ověřování Služby AAD DS (AAD DS) byla přijata interní chyba.

### <a name="cause"></a>Příčina

Došlo k chybě systémová chyba 1359. Vnitřní chyba se stane, když se pokusíte připojit ke sdílené složce s ověřováním AAD DS povoleno u AAD DS s názvem DNS domény začínající číselným znakem. Pokud je například název DNS domény Služby AAD DS "1doména", zobrazí se tato chyba při pokusu o připojení sdílené složky pomocí pověření AAD. 

### <a name="solution"></a>Řešení

V současné době můžete zvážit opětovné nasazení služby AAD DS pomocí nového názvu DNS domény, který platí s následujícími pravidly:
- Názvy nemohou začínat číselným znakem.
- Názvy musí mít 3 až 63 znaků.

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Obraťte se na podporu.
Pokud stále potřebujete pomoc, obraťte se na [podporu,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) abyste problém rychle vyřešili.

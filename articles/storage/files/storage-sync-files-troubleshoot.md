---
title: Řešení potíží s Azure File Sync | Microsoft Docs
description: Řešení běžných potíží s Azure File Sync.
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: e07d154ce5dae8a461bf9db19303db685f8a4152
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103068"
---
# <a name="troubleshoot-azure-file-sync"></a>Řešit problémy se Synchronizací souborů Azure
Pomocí Azure File Sync můžete centralizovat sdílené složky ve vaší organizaci ve službě soubory Azure a zároveň udržet flexibilitu, výkon a kompatibilitu místního souborového serveru. Synchronizace souborů Azure transformuje Windows Server na rychlou mezipaměť sdílené složky Azure. Pro místní přístup k datům můžete použít libovolný protokol, který je dostupný na Windows serveru, včetně SMB, NFS a FTPS. Můžete mít tolik mezipamětí, kolik potřebujete po celém světě.

Tento článek je navržený tak, aby pomohl řešit problémy, se kterými se můžete setkat s nasazením Azure File Sync. Popisujeme také, jak shromažďovat důležité protokoly ze systému, pokud je potřeba hlubší zkoumání problému. Pokud nevidíte odpověď na svoji otázku, můžete nás kontaktovat prostřednictvím následujících kanálů (v pořadí eskalace):

1. [Azure Storage Fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
2. [Soubory Azure ve službě UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).
3. Podpora Microsoftu. Chcete-li vytvořit novou žádost o podporu, v Azure Portal na kartě **help** klikněte na tlačítko **pomoc a podpora** a pak vyberte **Nová žádost o podporu**.

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Mám potíže s Azure File Sync na mém serveru (synchronizace, vrstvení cloudu atd.). Mám odebrat a znovu vytvořit koncový bod serveru?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Instalace agenta a registrace serveru
<a id="agent-installation-failures"></a>**Řešení chyb při instalaci agenta**  
Pokud se instalace agenta Azure File Sync nezdařila, na příkazovém řádku se zvýšenými oprávněními spusťte následující příkaz, kterým zapnete protokolování při instalaci agenta:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Zkontrolujte instalační program. log a určete příčinu selhání instalace.

<a id="agent-installation-on-DC"></a>**Instalace agenta na řadiči Doména služby Active Directory se nezdařila.**  
Pokud se pokusíte nainstalovat agenta synchronizace v řadiči domény služby Active Directory, kde je vlastník role primárního řadiče domény v systému Windows Server 2008 R2 nebo pod verzí operačního systému, můžete se setkat s problémem, kdy se agent synchronizace nedaří nainstalovat.

Pokud ho chcete vyřešit, přeneste roli primárního řadiče domény na jiný řadič domény se systémem Windows Server 2012 R2 nebo novějším a pak nainstalujte synchronizaci.

<a id="server-registration-prerequisites"></a>**Při registraci serveru se zobrazí následující zpráva: Chybí požadované součásti**

Tato zpráva se zobrazí, pokud v PowerShellu 5,1 není nainstalovaný modul AZ nebo AzureRM PowerShell. 

> [!Note]  
> ServerRegistration. exe nepodporuje PowerShell 6. x. K registraci serveru můžete použít rutinu Register-AzStorageSyncServer na PowerShellu 6. x.

Pokud chcete nainstalovat modul AZ nebo AzureRM do PowerShellu 5,1, proveďte následující kroky:

1. Do příkazového řádku se zvýšenými oprávněními zadejte **PowerShell** a stiskněte ENTER.
2. Pomocí dokumentace k instalaci nejnovějšího modulu AZ nebo AzureRM postupujte následovně:
    - [AZ Module (vyžaduje .NET 4.7.2)](https://go.microsoft.com/fwlink/?linkid=2062890)
    - [Modul AzureRM]( https://go.microsoft.com/fwlink/?linkid=856959)
3. Spusťte ServerRegistration. exe a dokončete průvodce a zaregistrujte Server se službou synchronizace úložiště.

<a id="server-already-registered"></a>**Při registraci serveru se zobrazí následující zpráva: Tento server je už zaregistrovaný** 

![Snímek obrazovky dialogu registrace serveru s chybovou zprávou "Server je již zaregistrován"](media/storage-sync-files-troubleshoot/server-registration-1.png)

Tato zpráva se zobrazí, pokud byl server dříve zaregistrován ve službě synchronizace úložiště. Pokud chcete zrušit registraci serveru od aktuální služby synchronizace úložiště a pak zaregistrovat novou službu synchronizace úložiště, proveďte kroky popsané v tématu [zrušení registrace serveru s Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Pokud server není uvedený v části **registrované servery** ve službě synchronizace úložiště, spusťte na serveru, který chcete zrušit, následující příkazy PowerShellu:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Pokud je Server součástí clusteru, můžete také odebrat registraci clusteru pomocí volitelného parametru *reset-StorageSyncServer-CleanClusterRegistration* .

<a id="web-site-not-trusted"></a>**Po registraci serveru se zobrazí mnoho odpovědí "Web není důvěryhodný". Proč?**  
K tomuto problému dochází, pokud jsou během registrace serveru povoleny **rozšířené zásady zabezpečení aplikace Internet Explorer** . Další informace o tom, jak správně zakázat **rozšířené zásady zabezpečení Internet Exploreru** , najdete v článku [Příprava Windows serveru pro použití s Azure File Sync](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) a [nasazení Azure File Sync](storage-sync-files-deployment-guide.md).

<a id="server-registration-missing"></a>**Server není uveden v části registrované servery v Azure Portal**  
Pokud server není uvedený v seznamu **registrovaných serverů** pro službu synchronizace úložiště:
1. Přihlaste se k serveru, který chcete zaregistrovat.
2. Otevřete Průzkumníka souborů a pak vyhledejte instalační adresář agenta synchronizace úložiště (výchozí umístění je C:\Program Files\Azure\StorageSyncAgent). 
3. Spusťte ServerRegistration. exe a dokončete průvodce a zaregistrujte Server se službou synchronizace úložiště.

## <a name="sync-group-management"></a>Správa skupin synchronizace
<a id="cloud-endpoint-using-share"></a>**Vytvoření koncového bodu cloudu se nezdařilo, došlo k této chybě: Zadanou sdílenou složku Azure již používá jiný koncový bod cloudu**  
K této chybě dochází, pokud je sdílená složka Azure již používána jiným koncovým bodem cloudu. 

Pokud se zobrazí tato zpráva a sdílená složka Azure aktuálně nepoužívá koncový bod cloudu, proveďte následující kroky, aby se ve sdílené složce Azure vymazala Azure File Syncová metadata:

> [!Warning]  
> Odstranění metadat ve sdílené složce Azure, kterou aktuálně používá koncový bod cloudu, způsobí selhání Azure File Sync operací. 

1. V Azure Portal přejdete do složky Azure File Share.  
2. Klikněte pravým tlačítkem na sdílenou složku Azure a pak vyberte **Upravit metadata**.
3. Klikněte pravým tlačítkem na **SyncService**a pak vyberte **Odstranit**.

<a id="cloud-endpoint-authfailed"></a>**Vytvoření koncového bodu cloudu se nezdařilo, došlo k této chybě: AuthorizationFailed**  
K této chybě dochází, pokud váš uživatelský účet nemá dostatečná oprávnění k vytvoření koncového bodu cloudu. 

Pokud chcete vytvořit koncový bod cloudu, musí mít váš uživatelský účet následující autorizační oprávnění Microsoftu:  
* Oprávnění Načíst definici role
* Psal Vytvořit nebo aktualizovat definici vlastní role
* Oprávnění Načíst přiřazení role
* Psal Vytvořit přiřazení role

Následující předdefinované role mají požadovaná autorizační oprávnění od Microsoftu:  
* Owner
* Správce uživatelských přístupů

Pokud chcete zjistit, jestli má role uživatelského účtu požadovaná oprávnění, postupujte takto:  
1. V Azure Portal vyberte **skupiny prostředků**.
2. Vyberte skupinu prostředků, ve které se nachází účet úložiště, a pak vyberte **řízení přístupu (IAM)** .
3. Vyberte kartu **přiřazení rolí** .
4. Pro svůj uživatelský účet vyberte **roli** (například vlastníka nebo přispěvatele).
5. V seznamu **poskytovatel prostředků** vyberte autorizaci **Microsoft**. 
    * **Přiřazení role** by mělo mít oprávnění **ke čtení** a **zápisu** .
    * **Definice role** musí mít oprávnění **ke čtení** a **zápisu** .

<a id="-2134375898"></a>**Vytvoření koncového bodu serveru se nezdařilo, došlo k této chybě: "MgmtServerJobFailed" (kód chyby:-2134375898 nebo 0x80c80226)**  
K této chybě dochází, pokud je cesta koncového bodu serveru na systémovém svazku a je povolená vrstva cloudu. Na systémovém svazku není podporováno vrstvení cloudu. Pokud chcete vytvořit koncový bod serveru na systémovém svazku, zakažte při vytváření koncového bodu serveru vrstvení cloudu.

<a id="-2147024894"></a>**Vytvoření koncového bodu serveru se nezdařilo, došlo k této chybě: "MgmtServerJobFailed" (kód chyby:-2147024894 nebo 0x80070002)**  
K této chybě dochází, pokud zadaná cesta koncového bodu serveru není platná. Ověřte, že zadaná cesta ke koncovému bodu serveru je místně připojený svazek NTFS. Poznámka: Azure File Sync nepodporuje namapované jednotky jako cestu koncového bodu serveru.

<a id="-2134347507"></a>**Vytvoření koncového bodu serveru se nezdařilo, došlo k této chybě: "MgmtServerJobFailed" (kód chyby:-2134347507 nebo 0x80c8710d)**  
K této chybě dochází, protože Azure File Sync nepodporuje koncové body serveru na svazcích, které mají komprimovanou složku s informacemi o systémovém svazku. Chcete-li tento problém vyřešit, dekomprimujte složku System Volume Information. Pokud je složka System Volume Information jedinou složkou komprimovaná na svazku, proveďte následující kroky:

1. Stáhněte si nástroj [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec) .
2. Spuštěním následujícího příkazu z příkazového řádku se zvýšenými oprávněními spusťte příkazový řádek spuštěný pod účtem System: **PsExec. exe – i-s-d cmd**
3. Z příkazového řádku spuštěného pod účtem System zadejte následující příkazy a stiskněte ENTER:   
    **CD/d "písmeno jednotky: \ systémový svazek informace"**  
    **Compact/u/s**

<a id="-2134376345"></a>**Vytvoření koncového bodu serveru se nezdařilo, došlo k této chybě: "MgmtServerJobFailed" (kód chyby:-2134376345 nebo 0x80C80067)**  
K této chybě dojde, pokud je dosaženo limitu počtu koncových bodů serveru na server. Azure File Sync aktuálně podporuje až 30 koncových bodů serveru na jeden server. Další informace najdete v tématu [Azure File Sync cíle škálování](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-file-sync-scale-targets).

<a id="-2134376427"></a>**Vytvoření koncového bodu serveru se nezdařilo, došlo k této chybě: "MgmtServerJobFailed" (kód chyby:-2134376427 nebo 0x80c80015)**  
K této chybě dochází, pokud již jiný koncový bod serveru synchronizuje zadanou cestu koncového bodu serveru. Azure File Sync nepodporuje více koncových bodů serveru, které synchronizují stejný adresář nebo svazek.

<a id="-2134347757"></a>**Odstranění koncového bodu serveru se nezdařilo, došlo k této chybě: "MgmtServerJobExpired" (kód chyby:-2134347757 nebo 0x80c87013)**  
K této chybě dochází, pokud je server offline nebo nemá připojení k síti. Pokud server již není dostupný, zrušte registraci serveru na portálu, který odstraní koncové body serveru. Pokud chcete odstranit koncové body serveru, postupujte podle kroků popsaných v tématu [zrušení registrace serveru pomocí Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

<a id="server-endpoint-provisioningfailed"></a>**Nepovedlo se otevřít stránku vlastností koncového bodu serveru nebo aktualizovat zásady cloudové vrstvy.**  
K tomuto problému může dojít, pokud se operace správy na koncovém bodu serveru nezdařila. Pokud se stránka Vlastnosti koncového bodu serveru v Azure Portal neotevře, může tento problém vyřešit aktualizace koncového bodu serveru pomocí příkazů prostředí PowerShell ze serveru. 

```powershell
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzStorageSyncServerEndpoint `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup | `
Tee-Object -Variable serverEndpoint

# Update the free space percent policy for the server endpoint
Set-AzStorageSyncServerEndpoint `
    -InputObject $serverEndpoint
    -CloudTiering `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**Koncový bod serveru má stav "bez aktivity" nebo "čeká na vyřízení" a stav serveru v okně registrované servery je "zobrazení offline".**  

K tomuto problému může dojít, pokud není proces monitorování synchronizace úložiště spuštěný nebo Server nemůže komunikovat se službou Azure File Sync z důvodu proxy serveru nebo brány firewall.

Při řešení tohoto problému postupujte následovně:

1. Otevřete na serveru Správce úloh a ověřte, že je spuštěný proces monitorování synchronizace úložiště (AzureStorageSyncMonitor.exe). Pokud tento proces není spuštěný, zkuste nejprve server restartovat. Pokud restartování serveru problém nevyřeší, upgradujte na nejnovější [verzi agenta](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes) Synchronizace souborů Azure.
2. Ověřte, že je správně nakonfigurovaná brána firewall a nastavení proxy serveru:
    - Pokud je server za bránou firewall, ověřte, že je povolený odchozí port 443. Pokud brána firewall omezuje provoz do konkrétních domén, zkontrolujte, že jsou dostupné domény uvedené v [dokumentaci](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#firewall) k bráně firewall.
    - Pokud je server za proxy, nakonfigurujte nastavení proxy serveru na úrovni počítače nebo aplikace podle kroků v [dokumentaci k](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#proxy)proxy serveru.

<a id="endpoint-noactivity-sync"></a>**Koncový bod serveru má stav bez aktivity a stav serveru v okně registrované servery je online.**  

Stav koncového bodu serveru bez aktivity znamená, že koncový bod serveru během posledních dvou hodin neprotokoloval aktivitu synchronizace.

Koncový bod serveru nemůže protokolovat aktivitu synchronizace z následujících důvodů:

- Je nainstalovaná verze agenta 4.3.0.0 nebo starší a server má aktivní relaci VSS Sync (SnapshotSync). Pokud je relace synchronizace VSS pro koncový bod serveru aktivní, jiné koncové body serveru na stejném svazku nemůžou spustit relaci spuštění synchronizace, dokud se nedokončí relace synchronizace VSS. Pokud chcete tento problém vyřešit, nainstalujte agenta verze 5.0.2.0 nebo novější, který podporuje více než několik koncových bodů serveru, které se synchronizují na svazku, když je aktivní relace synchronizace VSS.

    Postup kontroly aktuální aktivity synchronizace na serveru naleznete v tématu [návody monitorovat průběh aktuální relace synchronizace?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

- Server dosáhl maximálního počtu souběžných relací synchronizace. 
    - Agent verze 4. x a novější: Omezení se liší v závislosti na dostupných systémových prostředcích.
    - Agent verze 3. x: 2 aktivní relace synchronizace na jeden procesor nebo maximálně 8 aktivních relací synchronizace na jeden server.

> [!Note]  
> Pokud se stav serveru v okně registrované servery zobrazí jako offline, proveďte kroky popsané v [koncovém bodu serveru s stavem "žádná aktivita" nebo "čeká" a stav serveru v okně registrované servery je "Zobrazit offline"](#server-endpoint-noactivity) . .

## <a name="sync"></a>Synchronizovat
<a id="afs-change-detection"></a>**Pokud jsem soubor vytvořil přímo ve sdílené složce Azure přes protokol SMB nebo prostřednictvím portálu, jak dlouho trvá, než se soubor synchronizuje se servery ve skupině synchronizace?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**Stav koncového bodu serveru je ve stavu čekání na několik hodin.**  
Tento problém se očekává, když vytvoříte koncový bod cloudu a použijete sdílenou složku Azure, která obsahuje data. Aby bylo možné synchronizovat soubory mezi koncovými body cloudu a serveru, je potřeba provést úlohu výčtu změn, která kontroluje změny ve sdílené složce Azure. Čas k dokončení úlohy závisí na velikosti oboru názvů ve sdílené složce Azure. Stav koncového bodu serveru by se měl aktualizovat, jakmile se dokončí úloha změny výčtu.

### <a id="broken-sync"></a>Návody monitorovat stav synchronizace?
# <a name="portaltabportal1"></a>[Azure Portal](#tab/portal1)
V rámci každé skupiny synchronizace můžete přejít k podrobnostem svého jednotlivého koncového bodu serveru a zobrazit stav posledních dokončených relací synchronizace. Zelený sloupec stavu a nesynchronizovaná hodnota 0 značí, že synchronizace pracuje podle očekávání. Pokud se nejedná o tento případ, níže naleznete seznam běžných chyb synchronizace a postup zpracování nesynchronizovaných souborů. 

![Snímek obrazovky Azure Portal](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="servertabserver"></a>[Server](#tab/server)
Přejít na protokoly telemetrie serveru, které najdete v Prohlížeč událostí na `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`. Událost 9102 odpovídá dokončené relaci synchronizace; v případě nejnovějšího stavu synchronizace vyhledejte nejnovější událost s ID 9102. SyncDirection zobrazí informace o tom, jestli se jednalo o nahrání nebo stažení. Pokud je HResult 0, relace synchronizace byla úspěšná. Nenulová hodnota HResult znamená, že během synchronizace došlo k chybě; seznam běžných chyb najdete níže. Pokud je PerItemErrorCount větší než 0, znamená to, že některé soubory nebo složky se nesynchronizoval správně. Je možné mít hodnotu HResult 0, ale PerItemErrorCount, která je větší než 0.

Níže je uveden příklad úspěšného nahrání. V zájmu zkrácení jsou níže uvedené jenom některé hodnoty obsažené v každé události 9102. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Naopak neúspěšné nahrání může vypadat takto:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

V některých případech se relace synchronizace celkově nezdařila, nebo mají nenulové PerItemErrorCount, ale přesto se některé soubory úspěšně synchronizují. Tato funkce se dá zobrazit v polích použito * (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount a AppliedSizeBytes), která vám sdělí, jak velká část relace je úspěšná. Pokud se v neúspěšném řádku zobrazí více relací synchronizace, ale mají rostoucí počet *, pak byste měli před otevřením lístku podpory zkusit znovu provést pokus o synchronizaci.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Návody monitorovat průběh aktuální relace synchronizace?
# <a name="portaltabportal1"></a>[Azure Portal](#tab/portal1)
V rámci skupiny synchronizace přejděte na příslušný koncový bod serveru a podívejte se na část aktivita synchronizace a zobrazte počet souborů, které se nahrály nebo stáhly v aktuální relaci synchronizace. Všimněte si, že tento stav bude zpožděn přibližně o 5 minut a v případě, že je relace synchronizace dostatečně malá, aby mohla být dokončena v rámci této doby, nemusí být uvedena na portálu. 

# <a name="servertabserver"></a>[Server](#tab/server)
Podívejte se na nejnovější událost 9302 v protokolu telemetrie na serveru (v Prohlížeč událostí, přejít do části aplikace a služby Logs\Microsoft\FileSync\Agent\Telemetry). Tato událost označuje stav aktuální relace synchronizace. TotalItemCount označuje, kolik souborů se má synchronizovat, AppliedItemCount počet souborů, které byly doposud synchronizovány, a PerItemErrorCount počet souborů, které se nedaří synchronizovat (Další informace najdete v tématu jak s tím pracovat).

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>Návody zjistit, jestli jsou servery vzájemně synchronizované?
# <a name="portaltabportal1"></a>[Azure Portal](#tab/portal1)
Pro každý server v dané skupině synchronizace se ujistěte, že:
- Časová razítka posledního pokusu o synchronizaci pro nahrávání a stahování jsou poslední.
- Stav je zelený pro nahrávání i stahování.
- V poli aktivita synchronizace se zobrazuje velmi málo nebo žádné soubory, které se mají synchronizovat.
- Pole nesynchronizované soubory je 0 pro nahrávání i stahování.

# <a name="servertabserver"></a>[Server](#tab/server)
Podívejte se na dokončené relace synchronizace, které jsou označené událostmi 9102 v protokolu událostí telemetrie pro každý server (v Prohlížeč událostí, přejít na `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`). 

1. Na jakémkoli daném serveru se chcete ujistit, že se úspěšně odeslaly nejnovější relace nahrávání a stahování. Provedete to tak, že zkontrolujete, jestli je hodnota HResult a PerItemErrorCount 0 pro nahrávání i stahování (pole SyncDirection označuje, jestli je daná relace relace nahrávání nebo stahování). Všimněte si, že Pokud nevidíte nedávno dokončenou relaci synchronizace, je pravděpodobně relace synchronizace právě probíhající, což se očekává, pokud jste právě přidali nebo upravili velké množství dat.
2. Pokud je server plně aktuální s cloudem a nemá žádné změny k synchronizaci v obou směrech, zobrazí se prázdné relace synchronizace. Ty jsou označeny odesláním a stažením událostí, ve kterých jsou všechna pole Sync * (SyncFileCount, SyncDirCount, SyncTombstoneCount a SyncSizeBytes) nulová, což znamená, že nedošlo k žádné synchronizaci. Pamatujte na to, že tyto prázdné relace synchronizace se nemusí nacházet na serverech s vysokou mírou změny, protože k synchronizaci je vždycky něco nového. Pokud neexistují žádné aktivity synchronizace, měly by se vyskytnout každých 30 minut. 
3. Pokud jsou všechny servery aktuální s cloudem, což znamená, že nedávné relace nahrávání a stahování jsou prázdné relace synchronizace, můžete vyslovit s rozumnou jistotou, že systém jako celek je synchronizovaný. 
    
Všimněte si, že pokud jste provedli změny přímo ve sdílené složce Azure, Azure File Sync tuto změnu nezjistí, dokud se nezmění spuštění výčtu, k němuž dojde každých 24 hodin. Je možné, že server bude s cloudem aktuální a v případě, že ve skutečnosti chybí nedávné změny provedené přímo ve sdílené složce Azure. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Návody zjistit, jestli existují konkrétní soubory nebo složky, které se nesynchronizují?
Pokud vaše PerItemErrorCount na serveru nebo soubory, které nejsou synchronizované na portálu, jsou pro danou relaci synchronizace větší než 0, znamená to, že některé položky selžou synchronizaci. Soubory a složky mohou mít vlastnosti, které brání jejich synchronizaci. Tyto vlastnosti mohou být trvalé a vyžadují explicitní akci pro pokračování synchronizace, například odebrání nepodporovaných znaků ze souboru nebo složky. Mohou být také přechodné, což znamená, že soubor nebo složka budou automaticky pokračovat v synchronizaci. například soubory s otevřenými popisovači budou po zavření souboru automaticky pokračovat v synchronizaci. Když modul Azure File Sync zjistí takový problém, vytvoří se protokol chyb, který se dá analyzovat tak, aby se vygenerovalo seznam položek, které se v tuto chvíli nesynchronizují správně.

Pokud se chcete podívat na tyto chyby, spusťte skript prostředí PowerShell **FileSyncErrorsReport. ps1** (umístěný v instalačním adresáři agenta agenta Azure File Sync) a identifikujte soubory, které se nepodařilo synchronizovat kvůli otevřeným popisovačům, nepodporovaných znakům nebo jiným problémům. Pole položka Itempath oznamuje umístění souboru ve vztahu k adresáři kořenové synchronizace. Projděte si seznam běžných chyb synchronizace níže pro nápravné kroky.

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Řešení chyb při synchronizaci souborů nebo adresářů
**ItemResults chyby synchronizace protokolu pro jednotlivé položky**  

| HRESULT | HRESULT (desetinné číslo) | Text chyby | Problém | Náprava |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | Vrstvený soubor na serveru není dostupný. K tomuto problému dochází, pokud se vrstvený soubor před odstraněním koncového bodu serveru znovu nevolal. | Pokud chcete tento problém vyřešit, najdete informace v části [vrstvené soubory nejsou po odstranění koncového bodu serveru dostupné na serveru](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | Změnu souboru nebo adresáře nelze ještě synchronizovat, protože není ještě synchronizovaná závislá složka. Tato položka bude synchronizována po synchronizaci závislých změn. | Není vyžadována žádná akce. |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | Název souboru nebo adresáře je neplatný. | Přejmenujte daný soubor nebo adresář. Další informace najdete v tématu [zpracování nepodporovaných znaků](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) . |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | Název souboru nebo adresáře je neplatný. | Přejmenujte daný soubor nebo adresář. Další informace najdete v tématu [zpracování nepodporovaných znaků](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) . |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | Soubor nelze synchronizovat, protože se používá. Soubor se synchronizuje, až se přestane používat. | Není vyžadována žádná akce. Azure File Sync vytvoří dočasný snímek VSS jednou denně na serveru pro synchronizaci souborů s otevřenými popisovači. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | Soubor se změnil, ale tato změna se ještě nezjistila synchronizací. Po zjištění této změny se synchronizace obnoví. | Není vyžadována žádná akce. |
| chyby | -2147024894 | ERROR_FILE_NOT_FOUND | Soubor se odstranil a synchronizace o této změně nevíte. | Není vyžadována žádná akce. Synchronizace ukončí protokolování této chyby, jakmile zjišťování změn zjistí, že soubor byl odstraněn. |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | Soubor se přeskočil, ale během příští relace synchronizace se synchronizuje. | Není vyžadována žádná akce. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Soubor nejde synchronizovat, protože se dosáhlo limitu sdílené složky Azure. | Pokud chcete tento problém vyřešit, přečtěte si část s [omezením úložiště sdílené složky Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134351810) v Průvodci odstraňováním potíží. |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | Soubor je zašifrovaný pomocí nepodporovaného řešení (třeba NTFS EFS). | Dešifrujte soubor a použijte podporované řešení šifrování. Seznam řešení podpory najdete v části [řešení šifrování](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#encryption-solutions) v příručce plánování. |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | Soubor se nachází ve složce replikace jen pro čtení DFS-R. | Soubor se nachází ve složce replikace jen pro čtení DFS-R. Služba Azure File Sync nepodporuje koncové body serveru v replikačních složkách jen pro čtení v systému souborů DFS-R. Další informace najdete v [příručce pro plánování](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs) . |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Soubor je ve stavu čekání na odstranění. | Není vyžadována žádná akce. Až se všechny otevřené popisovače souborů zavřou, soubor se odstraní. |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | Soubor nejde synchronizovat, protože je povolené nastavení brány firewall a virtuální sítě v účtu úložiště a server nemá přístup k účtu úložiště. | Pomocí postupu popsaného v části [Konfigurace brány firewall a nastavení virtuální sítě](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) v Průvodci nasazením přidejte IP adresu serveru nebo virtuální síť. |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | Soubor nelze synchronizovat, protože velikost popisovače zabezpečení překračuje limit 64 KiB. | Pokud chcete tento problém vyřešit, odeberte v souboru položky řízení přístupu (ACE), abyste snížili velikost popisovače zabezpečení. |
| 0x8000ffff | -2147418113 | E_UNEXPECTED | Soubor nelze synchronizovat z důvodu neočekávané chyby. | Pokud chyba trvá několik dní, otevřete prosím případ podpory. |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | Soubor nelze synchronizovat, protože se používá. Soubor se synchronizuje, až se přestane používat. | Není vyžadována žádná akce. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Soubor se během synchronizace změnil, takže je potřeba ho synchronizovat znovu. | Není vyžadována žádná akce. |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | Soubor nelze synchronizovat, protože byl dosažen maximální počet souborů konfliktů. Azure File Sync podporuje soubory konfliktů 100 na jeden soubor. Další informace o konfliktech souborů najdete v tématu Azure File Sync [Nejčastější dotazy](https://docs.microsoft.com/azure/storage/files/storage-files-faq#afs-conflict-resolution). | Chcete-li tento problém vyřešit, snižte počet konfliktních souborů. Soubor se synchronizuje, jakmile bude počet konfliktních souborů menší než 100. |

#### <a name="handling-unsupported-characters"></a>Zpracování nepodporovaných znaků
Pokud skript prostředí PowerShell **FileSyncErrorsReport. ps1** zobrazuje chyby z důvodu nepodporovaných znaků (kód chyby 0x8007007B nebo 0x80c80255), měli byste odebrat nebo přejmenovat znaky při chybě z příslušných názvů souborů. PowerShell pravděpodobně vytiskne tyto znaky jako otazníky nebo prázdné obdélníky, protože většina těchto znaků nemá standardní vizuální kódování. [Nástroj pro vyhodnocení](storage-sync-files-planning.md#evaluation-cmdlet) lze použít k identifikaci nepodporovaných znaků.

Následující tabulka obsahuje všechny znaky Unicode, které Azure File Sync ještě nepodporují.

| Znaková sada | Počet znaků |
|---------------|-----------------|
| <ul><li>0x0000009D (příkaz pro operační systém OSC)</li><li>0x00000090 (řetězec řízení zařízení v řadičích domény)</li><li>0x0000008F (SS3 Single Shift)</li><li>0x00000081 (přednastavení s vysokým oktetem)</li><li>0x0000007F (del Delete)</li><li>0x0000008D (rezervovaný zpětný spojnicový kanál)</li></ul> | 6 |
| 0x0000FDD0-0x0000FDEF (Arabské prezentační Forms-a) | 32 |
| 0x0000FFF0-0x0000FFFF (speciální) | 16 |
| <ul><li>0x0001FFFE-0x0001FFFF = 2 (neznak)</li><li>0x0002FFFE-0x0002FFFF = 2 (neznak)</li><li>0x0003FFFE-0x0003FFFF = 2 (neznak)</li><li>0x0004FFFE-0x0004FFFF = 2 (neznak)</li><li>0x0005FFFE-0x0005FFFF = 2 (neznak)</li><li>0x0006FFFE-0x0006FFFF = 2 (neznak)</li><li>0x0007FFFE-0x0007FFFF = 2 (neznak)</li><li>0x0008FFFE-0x0008FFFF = 2 (neznak)</li><li>0x0009FFFE-0x0009FFFF = 2 (neznak)</li><li>0x000AFFFE-0x000AFFFF = 2 (neznak)</li><li>0x000BFFFE-0x000BFFFF = 2 (neznak)</li><li>0x000CFFFE-0x000CFFFF = 2 (neznak)</li><li>0x000DFFFE-0x000DFFFF = 2 (neznak)</li><li>0x000EFFFE-0x000EFFFF = 2 (Nedefinováno)</li><li>0x000FFFFE-0x000FFFFF = 2 (další privátní oblast použití)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Běžné chyby synchronizace
<a id="-2147023673"></a>**Relace synchronizace byla zrušena.**  

| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (desetinné číslo)** | -2147023673 | 
| **Řetězec chyby** | ERROR_CANCELLED |
| **Požadována náprava** | Ne |

Relace synchronizace mohou selhat z různých důvodů, včetně restartování nebo aktualizace serveru, snímků služby VSS atd. I když se tato chyba zdá, že vyžaduje následnou instalaci, je bezpečné tuto chybu ignorovat, pokud netrvá v průběhu několika hodin.

<a id="-2147012889"></a>**Nebylo možné navázat spojení se službou.**    

| | |
|-|-|
| **HRESULT** | 0x80072EE7 |
| **HRESULT (desetinné číslo)** | -2147012889 | 
| **Řetězec chyby** | WININET_E_NAME_NOT_RESOLVED |
| **Požadována náprava** | Ano |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**Služba omezila požadavek na uživatele.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (desetinné číslo)** | -2134376372 |
| **Řetězec chyby** | ECS_E_USER_REQUEST_THROTTLED |
| **Požadována náprava** | Ne |

Není vyžadována žádná akce; Server se znovu pokusí. Pokud tato chyba trvá několik hodin, vytvořte žádost o podporu.

<a id="-2134364043"></a>**Synchronizace je zablokovaná, dokud zjišťování změn nedokončí následné obnovení.**  

| | |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT (desetinné číslo)** | -2134364043 |
| **Řetězec chyby** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Požadována náprava** | Ne |

Nevyžaduje se žádná akce. Když se soubor nebo sdílená složka (koncový bod cloudu) obnoví pomocí Azure Backup, synchronizace se zablokuje, dokud se zjišťování změn nedokončí ve sdílené složce Azure. Zjišťování změn se spouští ihned po dokončení obnovení a doba trvání vychází z počtu souborů ve sdílené složce.

<a id="-2147216747"></a>**Synchronizace se nezdařila, protože synchronizovaná databáze byla uvolněna.**  

| | |
|-|-|
| **HRESULT** | 0x80041295 |
| **HRESULT (desetinné číslo)** | -2147216747 |
| **Řetězec chyby** | SYNC_E_METADATA_INVALID_OPERATION |
| **Požadována náprava** | Ne |

K této chybě obvykle dochází, když zálohovací aplikace vytvoří snímek služby VSS a synchronizovaná databáze se uvolní. Pokud tato chyba trvá několik hodin, vytvořte žádost o podporu.

<a id="-2134364065"></a>**Synchronizace nemá přístup ke sdílené složce Azure zadané v koncovém bodu cloudu.**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (desetinné číslo)** | -2134364065 |
| **Řetězec chyby** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **Požadována náprava** | Ano |

K této chybě dochází, protože agent Azure File Sync nemá přístup ke sdílené složce Azure, což může být způsobeno tím, že sdílená složka Azure nebo účet úložiště, který hostuje, už neexistuje. Tuto chybu můžete vyřešit pomocí následujících kroků:

1. [Ověřte, že účet úložiště existuje.](#troubleshoot-storage-account)
2. [Ujistěte se, že sdílená složka Azure existuje.](#troubleshoot-azure-file-share)
3. [Ujistěte se, že Azure File Sync má přístup k účtu úložiště.](#troubleshoot-rbac)
4. [Ověřte, že nastavení brány firewall a virtuální sítě v účtu úložiště jsou správně nakonfigurované (pokud jsou povolené).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**Použitý název účtu úložiště nelze přeložit.**  

| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (desetinné číslo)** | -2134364064 |
| **Řetězec chyby** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Požadována náprava** | Ano |

1. Ověřte, že se název DNS úložiště dá přeložit ze serveru.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Ověřte, že účet úložiště existuje.](#troubleshoot-storage-account)
3. [Ověřte, že nastavení brány firewall a virtuální sítě v účtu úložiště jsou správně nakonfigurované (pokud jsou povolené).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**Při přístupu k účtu úložiště došlo k neznámé chybě.**  

| | |
|-|-|
| **HRESULT** | 0x80c8308a |
| **HRESULT (desetinné číslo)** | -2134364022 |
| **Řetězec chyby** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **Požadována náprava** | Ano |

1. [Ověřte, že účet úložiště existuje.](#troubleshoot-storage-account)
2. [Ověřte, že nastavení brány firewall a virtuální sítě v účtu úložiště jsou správně nakonfigurované (pokud jsou povolené).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-1906441138"></a>**Synchronizace se nezdařila z důvodu problému s databází synchronizace.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (desetinné číslo)** | -1906441138 |
| **Řetězec chyby** | JET_errWriteConflict |
| **Požadována náprava** | Ano |

K této chybě dochází, pokud dojde k potížím s interní databází, kterou používá Azure File Sync. Když k tomuto problému dojde, vytvořte žádost o podporu a budeme vás kontaktovat, abychom vám pomohli tento problém vyřešit.

<a id="-2134364053"></a>**Verze agenta Azure File Sync nainstalovaná na serveru není podporovaná.**  

| | |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT (desetinné číslo)** | -2134364053 |
| **Řetězec chyby** | ECS_E_AGENT_VERSION_BLOCKED |
| **Požadována náprava** | Ano |

K této chybě dochází, pokud není podporována verze agenta Azure File Sync nainstalovaného na serveru. Pokud chcete tento problém vyřešit []( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) , upgradujte na [podporovanou verzi agenta]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions).

<a id="-2134351810"></a>**Dosáhli jste limitu úložiště sdílené složky Azure.**  

| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (desetinné číslo)** | -2134351810 |
| **Řetězec chyby** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Požadována náprava** | Ano |

K této chybě dochází, pokud bylo dosaženo limitu úložiště sdílené složky Azure, ke kterému může dojít v případě, že se pro sdílenou složku Azure používá kvóta nebo pokud využití překračuje limity pro sdílenou složku Azure. Další informace najdete v tématu [Aktuální limity sdílené složky Azure](storage-files-scale-targets.md).

1. Přejděte do skupiny synchronizace v rámci služby synchronizace úložiště.
2. V rámci skupiny synchronizace vyberte koncový bod cloudu.
3. Poznamenejte si název sdílené složky Azure v otevřeném podokně.
4. Vyberte propojený účet úložiště. Pokud tento odkaz neproběhne úspěšně, odkazovaný účet úložiště se odebral.

    ![Snímek obrazovky s podoknem podrobností cloudového koncového bodu s odkazem na účet úložiště](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Vyberte **soubory** a zobrazte seznam sdílených složek.
6. Klikněte na tři tečky na konci řádku pro sdílenou složku Azure, na kterou odkazuje koncový bod cloudu.
7. Ověřte, že **využití** je pod **kvótou**. Poznámka: Pokud se nezadá alternativní kvóta, kvóta bude odpovídat [maximální velikosti sdílené složky Azure](storage-files-scale-targets.md).

    ![Snímek obrazovky vlastností sdílené složky Azure](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Pokud je sdílená složka plná a kvóta není nastavená, jedním z možných způsobů, jak tento problém vyřešit, je učinit jednotlivé podsložky aktuálního koncového bodu serveru do svého vlastního koncového bodu serveru ve svých vlastních samostatných skupinách synchronizace. Tímto způsobem se budou všechny podsložky synchronizovat s jednotlivými sdílenými složkami Azure.

<a id="-2134351824"></a>**Sdílenou složku Azure se nepovedlo najít.**  

| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (desetinné číslo)** | -2134351824 |
| **Řetězec chyby** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Požadována náprava** | Ano |

K této chybě dojde, pokud není sdílená složka Azure přístupná. Řešení potíží:

1. [Ověřte, že účet úložiště existuje.](#troubleshoot-storage-account)
2. [Ujistěte se, že sdílená složka Azure existuje.](#troubleshoot-azure-file-share)

Pokud se sdílená složka Azure odstranila, musíte vytvořit novou sdílenou složku a pak znovu vytvořit skupinu synchronizace. 

<a id="-2134364042"></a>**Synchronizace je pozastavená, i když je toto předplatné Azure pozastavené.**  

| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (desetinné číslo)** | -2134364042 |
| **Řetězec chyby** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Požadována náprava** | Ano |

K této chybě dojde, když je předplatné Azure pozastavené. Po obnovení předplatného Azure se synchronizace znovu aktivuje. Podívejte [se, proč je moje předplatné Azure zakázané a jak ho znovu aktivovat?](../../billing/billing-subscription-become-disable.md) Další informace.

<a id="-2134364052"></a>**Účet úložiště má nakonfigurovanou bránu firewall nebo virtuální sítě.**  

| | |
|-|-|
| **HRESULT** | 0x80c8306c |
| **HRESULT (desetinné číslo)** | -2134364052 |
| **Řetězec chyby** | ECS_E_MGMT_STORAGEACLSNOTSUPPORTED |
| **Požadována náprava** | Ano |

K této chybě dochází, pokud je sdílená složka Azure nepřístupná z důvodu brány firewall účtu úložiště nebo protože účet úložiště patří do virtuální sítě. Ověřte, že nastavení brány firewall a virtuální sítě v účtu úložiště jsou správně nakonfigurované. Další informace najdete v tématu [Konfigurace nastavení brány firewall a virtuální sítě](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings). 

<a id="-2134375911"></a>**Synchronizace se nezdařila z důvodu problému s databází synchronizace.**  

| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (desetinné číslo)** | -2134375911 |
| **Řetězec chyby** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Požadována náprava** | Ne |

Tato chyba obvykle vyřeší sebe sama a může nastat, pokud jsou:

* Vysoký počet změn souborů mezi servery ve skupině synchronizace.
* Velký počet chyb na jednotlivých souborech a adresářích.

Pokud tato chyba trvá déle než pár hodin, vytvořte žádost o podporu a my vás budeme kontaktovat, abychom vám pomohli tento problém vyřešit.

<a id="-2146762487"></a>**Serveru se nepodařilo navázat zabezpečené připojení. Cloudová služba přijala neočekávaný certifikát.**  

| | |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (desetinné číslo)** | -2146762487 |
| **Řetězec chyby** | CERT_E_UNTRUSTEDROOT |
| **Požadována náprava** | Ano |

K této chybě může dojít, pokud vaše organizace používá ukončovací proxy server protokolu SSL nebo pokud škodlivá entita zachycuje provoz mezi serverem a službou Azure File Sync. Pokud jste si jistí, že je to očekávané (protože vaše organizace používá ukončovací proxy server protokolu SSL), přeskočíte ověření certifikátu s přepsáním registru.

1. Vytvořte hodnotu registru SkipVerifyingPinnedRootCertificate.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Restartujte službu synchronizace na zaregistrovaném serveru.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Když nastavíte tuto hodnotu registru, agent Azure File Sync při přenosu dat mezi serverem a cloudovou službou přijměte jakýkoliv místně důvěryhodný certifikát SSL.

<a id="-2147012894"></a>**Nebylo možné navázat spojení se službou.**  

| | |
|-|-|
| **HRESULT** | 0x80072EE2 |
| **HRESULT (desetinné číslo)** | -2147012894 |
| **Řetězec chyby** | WININET_E_TIMEOUT |
| **Požadována náprava** | Ano |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**Synchronizace se nezdařila z důvodu problému s ověřováním.**  

| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (desetinné číslo)** | -2134375680 |
| **Řetězec chyby** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Požadována náprava** | Ano |

K této chybě obvykle dochází, protože čas serveru není správný. Pokud server běží na virtuálním počítači, zkontrolujte, jestli je čas hostitele správný.

<a id="-2134364040"></a>**Synchronizace se nezdařila z důvodu vypršení platnosti certifikátu.**  

| | |
|-|-|
| **HRESULT** | 0x80c83078 |
| **HRESULT (desetinné číslo)** | -2134364040 |
| **Řetězec chyby** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **Požadována náprava** | Ano |

K této chybě dochází, protože vypršela platnost certifikátu použitého pro ověřování.

K potvrzení vypršení platnosti certifikátu proveďte následující kroky:  
1. Otevřete modul snap-in Certifikáty konzoly MMC, vyberte účet počítače a přejděte na certifikáty (místní počítač) \Personal\Certificates.
2. Ověřte, zda vypršela platnost certifikátu pro ověřování klientů.

Pokud platnost certifikátu ověřování klienta vyprší, proveďte následující kroky a problém vyřešte:

1. Ověřte, že je nainstalovaná verze agenta Azure File Sync 4.0.1.0 nebo novější.
2. Spusťte na serveru následující příkaz prostředí PowerShell:

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**Synchronizace se nezdařila z důvodu nenalezení ověřovacího certifikátu.**  

| | |
|-|-|
| **HRESULT** | 0x80c80228 |
| **HRESULT (desetinné číslo)** | -2134375896 |
| **Řetězec chyby** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **Požadována náprava** | Ano |

K této chybě dochází, protože nebyl nalezen certifikát používaný pro ověřování.

Při řešení tohoto problému postupujte následovně:

1. Ověřte, že je nainstalovaná verze agenta Azure File Sync 4.0.1.0 nebo novější.
2. Spusťte na serveru následující příkaz prostředí PowerShell:

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**Synchronizace se nezdařila, protože nebyla nalezena Identita ověřování.**  

| | |
|-|-|
| **HRESULT** | 0x80c83079 |
| **HRESULT (desetinné číslo)** | -2134364039 |
| **Řetězec chyby** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **Požadována náprava** | Ano |

K této chybě dochází, protože se nezdařilo odstranění koncového bodu serveru a koncový bod je nyní v částečně odstraněném stavu. Pokud chcete tento problém vyřešit, zkuste znovu odstranit koncový bod serveru.

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**Svazek, na kterém je umístěný koncový bod serveru, má nedostatek místa na disku.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (desetinné číslo)** | -1906441711 |
| **Řetězec chyby** | JET_errLogDiskFull |
| **Požadována náprava** | Ano |
| | |
| **HRESULT** | 0x80c8031a |
| **HRESULT (desetinné číslo)** | -2134375654 |
| **Řetězec chyby** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Požadována náprava** | Ano |

K této chybě dochází, protože svazek byl vyplněn. K této chybě obvykle dochází, protože soubory mimo koncový bod serveru používají místo na svazku. Uvolněte místo na svazku přidáním dalších koncových bodů serveru, přesunutím souborů na jiný svazek nebo zvětšením velikosti svazku, na kterém je koncový bod serveru zapnutý.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**Služba ještě není připravená na synchronizaci s tímto koncovým bodem serveru.**  

| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (desetinné číslo)** | -2134364145 |
| **Řetězec chyby** | ECS_E_REPLICA_NOT_READY |
| **Požadována náprava** | Ne |

K této chybě dochází, protože došlo ke změnám přímo ve sdílené složce Azure a probíhá zjišťování změn. Synchronizace se spustí, až se rozpoznávání změn dokončí.

[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**Synchronizace se nezdařila z důvodu problémů s mnoha jednotlivými soubory.**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (desetinné číslo)** | -2134375877 |
| **Řetězec chyby** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Požadována náprava** | Ano |
| | |
| **HRESULT** | 0x80c8021c |
| **HRESULT (desetinné číslo)** | -2134375908 |
| **Řetězec chyby** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Požadována náprava** | Ano |
| | |
| **HRESULT** | 0x80c80253 |
| **HRESULT (desetinné číslo)** | -2134375853 |
| **Řetězec chyby** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Požadována náprava** | Ano |

V případech, kdy dojde k velkému počtu chyb synchronizace souborů, se může stát, že relace synchronizace začnou selhat. <!-- To troubleshoot this state, see [Troubleshooting per file/directory sync errors]().-->

> [!NOTE]
> Azure File Sync vytvoří dočasný snímek VSS jednou denně na serveru pro synchronizaci souborů s otevřenými popisovači.

<a id="-2134376423"></a>**Synchronizace se nezdařila z důvodu problému s cestou koncového bodu serveru.**  

| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (desetinné číslo)** | -2134376423 |
| **Řetězec chyby** | ECS_E_SYNC_INVALID_PATH |
| **Požadována náprava** | Ano |

Ujistěte se, že cesta existuje, je na místním svazku NTFS a není bodem rozboru nebo existujícím koncovým bodem serveru.

<a id="-2134375817"></a>**Synchronizace se nezdařila, protože verze ovladače filtru není kompatibilní s verzí agenta.**  

| | |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT (desetinné číslo)** | -2134375817 |
| **Řetězec chyby** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Požadována náprava** | Ano |

K této chybě dochází, protože načtená verze ovladače filtru (StorageSync. sys) cloudové vrstvy není kompatibilní se službou agenta synchronizace úložiště (FileSyncSvc). Pokud byl agent Azure File Sync upgradován, restartujte server, aby se instalace dokončila. Pokud k chybě dochází i nadále, odinstalujte agenta, restartujte server a přeinstalujte agenta Azure File Sync.

<a id="-2134376373"></a>**Služba není momentálně k dispozici.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (desetinné číslo)** | -2134376373 |
| **Řetězec chyby** | ECS_E_SERVICE_UNAVAILABLE |
| **Požadována náprava** | Ne |

K této chybě dochází, protože služba Azure File Sync není k dispozici. Tato chyba bude automaticky vyřešena, jakmile bude služba Azure File Sync znovu dostupná.

<a id="-2146233088"></a>**Synchronizace se nezdařila z důvodu výjimky.**  

| | |
|-|-|
| **HRESULT** | 0x80131500 |
| **HRESULT (desetinné číslo)** | -2146233088 |
| **Řetězec chyby** | COR_E_EXCEPTION |
| **Požadována náprava** | Ne |

K této chybě dochází, protože synchronizace se nezdařila z důvodu výjimky. Pokud chyba trvá několik hodin, vytvořte prosím žádost o podporu.

<a id="-2134364045"></a>**Synchronizace se nezdařila, protože u účtu úložiště došlo k převzetí služeb při selhání do jiné oblasti.**  

| | |
|-|-|
| **HRESULT** | 0x80c83073 |
| **HRESULT (desetinné číslo)** | -2134364045 |
| **Řetězec chyby** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **Požadována náprava** | Ano |

K této chybě dochází, protože u účtu úložiště došlo k převzetí služeb při selhání do jiné oblasti. Azure File Sync nepodporuje funkci převzetí služeb při selhání účtu úložiště. Účty úložiště obsahující sdílené složky Azure, které se používají jako koncové body cloudu v Azure File Sync by neměly přenášet služby při selhání. Tím dojde k tomu, že synchronizace přestane fungovat a může také způsobit neočekávanou ztrátu dat v případě nově vrstvených souborů. Pokud chcete tento problém vyřešit, přesuňte účet úložiště do primární oblasti.

<a id="-2134375922"></a>**Synchronizace se nezdařila z důvodu přechodného problému s databází synchronizace.**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (desetinné číslo)** | -2134375922 |
| **Řetězec chyby** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Požadována náprava** | Ne |

K této chybě dochází z důvodu interního problému s databází synchronizace. Tato chyba bude automaticky vyřešena při opakování synchronizace. Pokud tato chyba trvá déle, vytvořte žádost o podporu a budeme vás kontaktovat, abychom vám pomohli tento problém vyřešit.

<a id="-2134364024"></a>**Synchronizace se nezdařila z důvodu změny v Azure Active Directory tenant**  

| | |
|-|-|
| **HRESULT** | 0x80c83088 |
| **HRESULT (desetinné číslo)** | -2134364024 | 
| **Řetězec chyby** | ECS_E_INVALID_AAD_TENANT |
| **Požadována náprava** | Ano |

K této chybě dochází, protože Azure File Sync v současné době nepodporuje přesun předplatného na jiného tenanta Azure Active Directory.
 
Chcete-li vyřešit tento problém, proveďte jednu z následujících možností:

- **Možnost 1 (doporučeno)** : Přesunout předplatné zpátky na původního Azure Active Directoryho tenanta
- **Možnost 2**: Odstraňte a znovu vytvořte aktuální skupinu synchronizace. Pokud byla na koncovém bodu serveru povolená vrstva cloudu, odstraňte skupinu synchronizace a pak proveďte kroky popsané v [části vrstvení cloudu]( https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) a odstraňte osamocené vrstvené soubory před opětovným vytvořením skupin synchronizace. 

<a id="-2134364010"></a>**Synchronizace se nezdařila z důvodu nekonfigurace výjimky brány firewall a virtuální sítě**  

| | |
|-|-|
| **HRESULT** | 0x80c83096 |
| **HRESULT (desetinné číslo)** | -2134364010 | 
| **Řetězec chyby** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **Požadována náprava** | Ano |

K této chybě dochází, pokud jsou v účtu úložiště povolena nastavení brány firewall a virtuální sítě a možnost "Povolit důvěryhodným službám Microsoftu přístup k tomuto účtu úložiště" není zaškrtnuta. Pokud chcete tento problém vyřešit, postupujte podle kroků popsaných v části [Konfigurace brány firewall a nastavení virtuální sítě](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) v Průvodci nasazením.

<a id="-2147024891"></a>**Synchronizace se nezdařila, protože oprávnění ve složce System Volume Information jsou nesprávná.**  

| | |
|-|-|
| **HRESULT** | 0x80070005 |
| **HRESULT (desetinné číslo)** | -2147024891 |
| **Řetězec chyby** | ERROR_ACCESS_DENIED |
| **Požadována náprava** | Ano |

K této chybě může dojít, pokud účet NT AUTHORITY\SYSTEM nemá oprávnění ke složce informací o systémovém svazku na svazku, kde je umístěný koncový bod serveru. Všimněte si, že pokud se nedaří synchronizovat jednotlivé soubory s ERROR_ACCESS_DENIED, proveďte kroky popsané v části [řešení potíží s chybami synchronizace podle souboru nebo adresáře](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#troubleshooting-per-filedirectory-sync-errors) .

Při řešení tohoto problému postupujte následovně:

1. Stáhněte si nástroj [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec) .
2. Spuštěním následujícího příkazu z příkazového řádku se zvýšenými oprávněními spusťte příkazový řádek pomocí systémového účtu: **PsExec. exe – i-s-d cmd** 
3. Z příkazového řádku spuštěného v účtu System spusťte následující příkaz, který potvrdí, že účet NT AUTHORITY\SYSTEM nemá přístup ke složce System Volume Information: **cacls "Písmeno_jednotky: \ System Volume Information"/T/c**
4. Pokud účet NT AUTHORITY\SYSTEM nemá přístup ke složce System Volume Information, spusťte následující příkaz: **cacls "písmeno jednotky: \ System Volume Information"/T/E/g "NT AUTHORITY\SYSTEM: F"**
    - Pokud se krok #4 nepovede s odepřením přístupu, spusťte následující příkaz, který převezme vlastnictví složky System Volume Information a pak opakujte krok #4: **takeown/A/R/f "písmeno jednotky: \ systémový svazek informace"**

<a id="-2134375810"></a>**Synchronizace se nezdařila, protože sdílená složka Azure byla odstraněna a znovu vytvořena.**  

| | |
|-|-|
| **HRESULT** | 0x80c8027e |
| **HRESULT (desetinné číslo)** | -2134375810 |
| **Řetězec chyby** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **Požadována náprava** | Ano |

K této chybě dochází, protože Azure File Sync nepodporuje odstranění a opětovné vytvoření sdílené složky Azure ve stejné skupině synchronizace. 

Chcete-li tento problém vyřešit, odstraňte a znovu vytvořte skupinu synchronizace provedením následujících kroků:

1. Odstraňte všechny koncové body serveru ve skupině synchronizace.
2. Odstraňte koncový bod cloudu. 
3. Odstraňte skupinu synchronizace.
4. Pokud byla na koncovém bodu serveru povolená vrstva cloudu, odstraňte osamocené vrstvené soubory na serveru, a to [po odstranění oddílu koncového bodu serveru na serveru není dostupné](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) .
5. Znovu vytvořte skupinu synchronizace.

### <a name="common-troubleshooting-steps"></a>Běžné kroky při řešení potíží
<a id="troubleshoot-storage-account"></a>**Ověřte, že účet úložiště existuje.**  
# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
1. Přejděte do skupiny synchronizace v rámci služby synchronizace úložiště.
2. V rámci skupiny synchronizace vyberte koncový bod cloudu.
3. Poznamenejte si název sdílené složky Azure v otevřeném podokně.
4. Vyberte propojený účet úložiště. Pokud tento odkaz neproběhne úspěšně, odkazovaný účet úložiště se odebral.
    ![Snímek obrazovky s podoknem podrobností cloudového koncového bodu s odkazem na účet úložiště](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
# Variables for you to populate based on your configuration
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Log into the Azure account
Connect-AzAccount

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.StorageSyncServiceName
}

if ($syncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.SyncGroupName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $syncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    throw [System.Exception]::new("The storage account referenced in the cloud endpoint does not exist.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Ujistěte se, že sdílená složka Azure existuje.**  
# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
1. Kliknutím na **Přehled** v obsahu na levé straně se vraťte na stránku hlavního účtu úložiště.
2. Vyberte **soubory** a zobrazte seznam sdílených složek.
3. Ověřte, že se sdílená složka, na kterou se odkazuje koncový bod cloudu, zobrazuje v seznamu sdílených složek (měli byste si je poznamenali v kroku 1 výše).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.AzureFileShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("The Azure file share referenced by the cloud endpoint does not exist")
}
```
---

<a id="troubleshoot-rbac"></a>**Ujistěte se, že Azure File Sync má přístup k účtu úložiště.**  
# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
1. V obsahu na levé straně klikněte na **řízení přístupu (IAM)** .
1. Klikněte na kartu **přiřazení rolí** a seznam uživatelů a aplikací (*instanční objekty*), které mají přístup k vašemu účtu úložiště.
1. Ověřte, že se **Služba hybridní synchronizace souborů** zobrazuje v seznamu s rolí **Čtenář a přístup k datům** . 

    ![Snímek obrazovky s instančním objektem služby Hybrid Synchronizace souborů na kartě řízení přístupu účtu úložiště](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Pokud se **Služba hybridní synchronizace souborů** v seznamu nezobrazí, proveďte následující kroky:

    - Klikněte na **Přidat**.
    - V poli **role** vyberte **Čtenář a přístup k datům**.
    - Do pole **Vybrat** zadejte **Služba Hybrid synchronizace souborů Service**, vyberte roli a klikněte na **Uložit**.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Hybrid File Sync Service" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>Návody zabránit uživatelům v vytváření souborů obsahujících nepodporované znaky na serveru?
K blokování souborů s nepodporovanými znaky v jejich názvech na serveru můžete použít blokování souborového [serveru správce prostředků (Správce prostředků souborového serveru)](https://docs.microsoft.com/windows-server/storage/fsrm/file-screening-management) . Je možné, že to budete muset udělat pomocí PowerShellu, protože většina nepodporovaných znaků není Tisknutelná, takže je potřeba nejprve přetypovat jejich hexadecimální reprezentace jako znaky.

Nejprve vytvořte skupinu souborů správce prostředků souborového serveru pomocí [rutiny New-FsrmFileGroup](https://docs.microsoft.com/powershell/module/fileserverresourcemanager/new-fsrmfilegroup). Tento příklad definuje skupinu tak, aby obsahovala pouze dva nepodporované znaky, ale můžete do vaší skupiny souborů zahrnout tolik znaků, kolik je potřeba.

```powershell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

Po definování skupiny souborů správce prostředků souborového serveru můžete pomocí rutiny New-FsrmFileScreen vytvořit obrazovku souboru Správce prostředků souborového serveru.

```powershell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> Všimněte si, že blokování souborů by měla být použita pouze k blokování vytváření znaků, které nejsou podporovány nástrojem Azure File Sync. Pokud se v jiných scénářích používají blokování souborů, synchronizace se průběžně pokusí stáhnout soubory ze sdílené složky Azure na server a zablokuje se kvůli blokování souborů, což má za následek velký výstup dat. 

## <a name="cloud-tiering"></a>Vrstvení cloudu 
Existují dvě cesty pro chyby ve vrstvách cloudu:

- Soubory se nemůžou podařit navrstvit, což znamená, že se Azure File Sync neúspěšně pokusy o vytvoření vrstvy souboru do služby soubory Azure.
- Nepovedlo se navrátit soubory, což znamená, že Azure File Sync filtr systému souborů (StorageSync. sys) se nepodaří stáhnout data, když se uživatel pokusí o přístup k souboru, který byl vrstven.

Existují dvě hlavní třídy selhání, ke kterým může dojít prostřednictvím cesty selhání:

- Selhání cloudového úložiště
    - *Dočasné problémy s dostupností služby úložiště* Další informace najdete v tématu [smlouva SLA (SLA) pro Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - *Nepřístupná sdílená složka Azure* K této chybě obvykle dochází, když odstraníte sdílenou složku Azure, pokud je stále koncový bod cloudu ve skupině synchronizace.
    - *Účet úložiště*, který je k dispozici K této chybě obvykle dochází, když odstraníte účet úložiště, zatímco stále obsahuje sdílenou složku Azure, která je cloudovým koncovým bodem ve skupině synchronizace. 
- Selhání serveru 
  - *Azure File Sync filtr systému souborů (StorageSync. sys) není načten*. Aby bylo možné reagovat na požadavky na vrstvení a odvolání, musí být načten filtr systému souborů Azure File Sync. Nenačtený filtr může být z několika důvodů, ale nejběžnějším důvodem je to, že ho správce nenačte ručně. Filtr systému souborů Azure File Sync musí být pro správné fungování Azure File Sync zaveden pro všechny časy.
  - *Chybí, je poškozený nebo jinak přerušený spojovací bod*. Spojovací bod je speciální datová struktura pro soubor, který se skládá ze dvou částí:
    1. Značka rozboru, která indikuje operačnímu systému, že Azure File Sync filtr systému souborů (StorageSync. sys) může vyžadovat určitou akci pro vstup z provozu do souboru. 
    2. Data znovu Analyzujte, což znamená, že systém souborů vyfiltruje identifikátor URI souboru v přidruženém koncovém bodě cloudu (sdílená složka Azure). 
        
       Nejběžnější způsob, jak by byl bod rozboru poškozený, je, že se správce pokusí změnit buď značku, nebo její data. 
  - *Problémy s připojením k síti*. Aby bylo možné vytvořit vrstvu nebo odvolat soubor, musí mít server připojení k Internetu.

Následující části označují, jak řešit problémy s vrstvami cloudu a zjistit, jestli se jedná o problém s cloudovým úložištěm nebo problém se serverem.

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>Jak monitorovat aktivitu vrstvení na serveru  
Chcete-li monitorovat aktivitu vrstvení na serveru, použijte ID události 9003, 9016 a 9029 v protokolu událostí telemetrie (nacházející se v části aplikace a Services\Microsoft\FileSync\Agent v Prohlížeč událostí).

- Událost s ID 9003 poskytuje distribuci chyb pro koncový bod serveru. Například celkový počet chyb, kód chyby atd. Poznámka: jedna událost je protokolována podle kódu chyby.
- ID události 9016 poskytuje duplikování výsledků pro svazek. Například volné místo v procentech je, počet souborů, u kterých došlo k neúspěšnému duplikování v relaci, počet souborů se nezdařil z důvodu neduplikování atd.
- Událost s ID 9029 poskytuje duplikaci informací o relaci koncového bodu serveru. Například počet souborů, které se v relaci pokusily, počet souborů vrstvených v relaci, počet souborů, které jsou již vrstveny atd.

### <a name="how-to-monitor-recall-activity-on-a-server"></a>Jak monitorovat aktivitu odvolání na serveru
Chcete-li monitorovat aktivitu odvolání na serveru, použijte ID události 9005, 9006, 9009 a 9059 v protokolu událostí telemetrie (nacházející se v části aplikace a Services\Microsoft\FileSync\Agent v Prohlížeč událostí).

- Událost s ID 9005 poskytuje spolehlivost odvolání pro koncový bod serveru. Například celkový počet přidaných jedinečných souborů, celkový počet jedinečných souborů s neúspěšným přístupem atd.
- Událost s ID 9006 poskytuje distribuci chyb odvolání pro koncový bod serveru. Například celkový počet neúspěšných žádostí, kód chyby atd. Poznámka: jedna událost je protokolována podle kódu chyby.
- Událost s ID 9009 poskytuje informace o odvolání relace pro koncový bod serveru. Například DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed atd.
- Událost s ID 9059 poskytuje distribuci odvolání aplikace pro koncový bod serveru. Například ShareId, název aplikace a TotalEgressNetworkBytes.

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>Řešení potíží se soubory, které se nepodařilo navrstvit
Pokud se soubory nedaří navrstvit na soubory Azure:

1. V Prohlížeč událostí zkontrolujte protokoly telemetrie, provozní a diagnostické události umístěné v části aplikace a Services\Microsoft\FileSync\Agent. 
   1. Ověřte, že soubory existují ve sdílené složce Azure.

      > [!NOTE]
      > Soubor se musí synchronizovat se sdílenou složkou Azure předtím, než se dá navrstvený.

   2. Ověřte, že je server připojený k Internetu. 
   3. Ověřte, že jsou spuštěné ovladače filtru Azure File Sync (StorageSync. sys a StorageSyncGuard. sys):
       - Na příkazovém řádku se zvýšenými oprávněními `fltmc`spusťte příkaz. Ověřte, že jsou uvedené ovladače filtru systému souborů StorageSync. sys a StorageSyncGuard. sys.

> [!NOTE]
> ID události 9003 se protokoluje jednou za hodinu v protokolu událostí telemetrie, pokud se soubor nepovede na vrstvu (jedna událost je protokolována podle kódu chyby). Provozní a diagnostické protokoly událostí by se měly použít, pokud potřebujete další informace k diagnostice problému.

### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>Řešení potíží se soubory, které se nepodařilo znovu zavolat  
Pokud se soubory nepodaří odvolat:
1. V Prohlížeč událostí zkontrolujte protokoly telemetrie, provozní a diagnostické události umístěné v části aplikace a Services\Microsoft\FileSync\Agent.
    1. Ověřte, že soubory existují ve sdílené složce Azure.
    2. Ověřte, že je server připojený k Internetu. 
    3. Otevřete modul snap-in služby konzoly MMC a ověřte, jestli je spuštěná služba agenta synchronizace úložiště (FileSyncSvc).
    4. Ověřte, že jsou spuštěné ovladače filtru Azure File Sync (StorageSync. sys a StorageSyncGuard. sys):
        - Na příkazovém řádku se zvýšenými oprávněními `fltmc`spusťte příkaz. Ověřte, že jsou uvedené ovladače filtru systému souborů StorageSync. sys a StorageSyncGuard. sys.

> [!NOTE]
> ID události 9006 se v protokolu událostí telemetrie zaznamená jednou za hodinu, pokud se soubor nepovede vyvolat (jedna událost je protokolována podle kódu chyby). Provozní a diagnostické protokoly událostí by se měly použít, pokud potřebujete další informace k diagnostice problému.

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>Vrstvené soubory nejsou po odstranění koncového bodu serveru dostupné na serveru.
Vrstvené soubory na serveru budou nepřístupné, pokud se soubory před odstraněním koncového bodu serveru nevrátí.

Chyby zaznamenané v případě nepřístupných souborů do vrstev
- Při synchronizaci souboru se do protokolu událostí ItemResults zaznamená chybový kód-2147942467 (0x80070043-ERROR_BAD_NET_NAME).
- Při opětovném volání souboru se do protokolu událostí RecallResults zaznamená chybový kód-2134376393 (0x80c80037-ECS_E_SYNC_SHARE_NOT_FOUND).

Obnovení přístupu k vrstveným souborům je možné, pokud jsou splněné následující podmínky:
- Koncový bod serveru byl odstraněn během posledních 30 dnů.
- Koncový bod cloudu nebyl odstraněn. 
- Sdílení souborů nebylo smazáno.
- Skupina synchronizace se neodstranila.

Pokud jsou splněny výše uvedené podmínky, můžete obnovit přístup k souborům na serveru tak, že znovu vytvoříte koncový bod serveru na stejné cestě na serveru ve stejné skupině synchronizace do 30 dnů. 

Pokud se výše uvedené podmínky nesplní, nebude možné přístup k obnovení, protože tyto vrstvené soubory na serveru jsou teď osamocené. Osamocené vrstvené soubory odeberte podle následujících pokynů.

**Poznámky**
- Pokud na serveru nejsou vrstvené soubory dostupné, měl by být úplný soubor dostupný i v případě, že přímo přistupujete ke sdílené složce Azure.
- Pokud chcete v budoucnu zabránit osamoceným vrstveným souborům, postupujte podle kroků popsaných v části [Odebrání koncového bodu serveru](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint) při odstraňování koncového bodu serveru.

<a id="get-orphaned"></a>**Jak získat seznam osamocených souborů** 

1. Ověřte, že je nainstalovaná verze agenta Azure File Sync v 5.1 nebo novější verzi.
2. Pro výpis osamocených vrstvených souborů spusťte následující příkazy PowerShellu:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Uložte výstupní soubor OrphanTieredFiles. txt v případě, že se soubory musí po odstranění obnovit ze zálohy.

<a id="remove-orphaned"></a>**Postup odebrání osamocených vrstvených souborů** 

*Možnost 1: Odstraní osamocené vrstvené soubory.*

Tato možnost odstraní osamocené vrstvené soubory na Windows serveru, ale vyžaduje odebrání koncového bodu serveru, pokud existuje v důsledku opětovného provedení po dobu 30 dnů nebo připojení k jiné skupině synchronizace. Konflikty souborů dojde, pokud jsou soubory aktualizovány na serveru Windows nebo sdílené složky Azure před tím, než se znovu vytvoří koncový bod serveru.

1. Ověřte, že je nainstalovaná verze agenta Azure File Sync v 5.1 nebo novější verzi.
2. Zálohujte umístění sdílené složky Azure a koncového bodu serveru.
3. Odeberte koncový bod serveru ve skupině synchronizace (pokud existuje) podle postupu popsaného v části [Odebrání koncového bodu serveru](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint).

> [!Warning]  
> Pokud se před použitím rutiny Remove-StorageSyncOrphanedTieredFiles neodebere koncový bod serveru, odstraní se osamocený vrstvený soubor na serveru a odstraní úplný soubor ve sdílené složce Azure. 

4. Pro výpis osamocených vrstvených souborů spusťte následující příkazy PowerShellu:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. Uložte výstupní soubor OrphanTieredFiles. txt v případě, že se soubory musí po odstranění obnovit ze zálohy.
6. Chcete-li odstranit osamocené vrstvené soubory, spusťte následující příkazy PowerShellu:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**Poznámky** 
- Vrstvené soubory upravené na serveru, které nejsou synchronizované se sdílenou složkou Azure, se odstraní.
- Vrstvené soubory, které jsou přístupné (ne osamocené), se neodstraní.
- Nevrstvený soubor zůstane na serveru.

7. Volitelné: Znovu vytvořte koncový bod serveru, pokud byl odstraněn v kroku 3.

*Možnost 2: Připojení sdílené složky Azure a zkopírování souborů místně, které jsou na serveru osamocené*

Tato možnost nevyžaduje odebrání koncového bodu serveru, ale vyžaduje dostatek místa na disku pro místní kopírování úplných souborů.

1. [Připojte](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) sdílenou složku Azure na Windows serveru, který má osamocené vrstvené soubory.
2. Pro výpis osamocených vrstvených souborů spusťte následující příkazy PowerShellu:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Pomocí výstupního souboru OrphanTieredFiles. txt Identifikujte osamocené vrstvené soubory na serveru.
4. Osamocené vrstvené soubory můžete přepsat zkopírováním úplného souboru ze sdílené složky Azure na Windows Server.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>Řešení potíží neočekávaně vrácených souborů na serveru  
Antivirová ochrana, zálohování a další aplikace, které čtou velký počet souborů, způsobují nezamýšlená volání, pokud nerespektují atribut Skip offline a přeskočí čtení obsahu těchto souborů. U produktů, které tuto možnost podporují, pomáhá přeskočení offline souborů zabránit nežádoucím odvoláním během operací, jako jsou antivirové kontroly nebo úlohy zálohování.

Informace o konfiguraci přeskakování čtení offline souborů v řešení vám sdělí dodavatel příslušného softwaru.

Nezamýšlená revolání mohou nastat také v jiných scénářích, například při procházení souborů v Průzkumníkovi souborů. Pokud v Průzkumníku souborů na serveru otevřete složku obsahující soubory vrstvené v cloudu, může to mít za následek nežádoucí odvolání. Pravděpodobnost, že k tomu dojde, ještě zvyšuje povolené antivirové řešení na serveru.

> [!NOTE]
>Pomocí ID události 9059 v protokolu událostí telemetrie určete, které aplikace způsobují volání. Tato událost poskytuje distribuci odvolání aplikace pro koncový bod serveru a zaznamená se do protokolu jednou za hodinu.

## <a name="general-troubleshooting"></a>Obecné řešení potíží
Pokud narazíte na problémy s Azure File Sync na serveru, začněte provedením následujících kroků:
1. V Prohlížeč událostí zkontrolujte protokoly telemetrie, provozní a diagnostické události.
    - Problémy se synchronizací, vrstvením a odvoláním se zaznamenávají do protokolů telemetrie, diagnostické a provozní události v části aplikace a Services\Microsoft\FileSync\Agent.
    - Problémy související se správou serveru (například nastavení konfigurace) se zaznamenávají do protokolů provozní a diagnostické události v části aplikace a Services\Microsoft\FileSync\Management.
2. Ověřte, že je na serveru spuštěná služba Azure File Sync:
    - Otevřete modul snap-in služby konzoly MMC a ověřte, jestli je spuštěná služba agenta synchronizace úložiště (FileSyncSvc).
3. Ověřte, že jsou spuštěné ovladače filtru Azure File Sync (StorageSync. sys a StorageSyncGuard. sys):
    - Na příkazovém řádku se zvýšenými oprávněními `fltmc`spusťte příkaz. Ověřte, že jsou uvedené ovladače filtru systému souborů StorageSync. sys a StorageSyncGuard. sys.

Pokud se problém nevyřeší, spusťte nástroj AFSDiag:
1. Vytvořte adresář, do kterého se uloží výstup AFSDiag (například C:\Output).
    > [!NOTE]
    >AFSDiag odstraní veškerý obsah ve výstupním adresáři před shromažďováním protokolů. Zadejte umístění výstupu, které neobsahuje data.
2. Otevřete okno PowerShellu se zvýšenými oprávněními a potom spusťte následující příkazy (po každém z nich stiskněte ENTER):

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Pro Azure File Sync úroveň trasování režimu jádra zadejte **1** (není-li uvedeno jinak), chcete-li vytvořit více podrobných trasování, a potom stiskněte klávesu ENTER.
4. Pro úroveň trasování režimu Azure File Sync uživatele zadejte **1** (Pokud není uvedeno jinak), aby se vytvořily podrobnější trasování, a pak stiskněte ENTER.
5. Reprodukování problému. Až skončíte, zadejte **D**.
6. Soubor. zip, který obsahuje protokoly a trasovací soubory, je uložen do výstupního adresáře, který jste zadali.

## <a name="see-also"></a>Viz také:
- [Monitorování Synchronizace souborů Azure](storage-sync-files-monitoring.md)
- [Nejčastější dotazy k souborům Azure](storage-files-faq.md)
- [Řešení potíží se službou Azure Files ve Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Řešení potíží se službou Azure Files v Linuxu](storage-troubleshoot-linux-file-connection-problems.md)

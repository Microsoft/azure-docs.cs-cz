---
title: Poradce při potížích se synchronizací souborů Azure | Dokumenty společnosti Microsoft
description: Řešení běžných problémů se synchronizací souborů Azure.
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 1/22/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: d46f513fccf9921d4cf47835bc9d5be4c6ffe241
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607497"
---
# <a name="troubleshoot-azure-file-sync"></a>Řešení problémů se Synchronizací souborů Azure
Azure File Sync slouží k centralizaci sdílených složek vaší organizace v souborech Azure a současně zachovávejte flexibilitu, výkon a kompatibilitu místního souborového serveru. Synchronizace souborů Azure transformuje Windows Server na rychlou mezipaměť sdílené složky Azure. K místnímu přístupu k datům můžete použít libovolný protokol, který je k dispozici v systému Windows Server, včetně protokolů SMB, NFS a FTPS. Můžete mít tolik cache, kolik potřebujete po celém světě.

Tento článek je navržen tak, aby vám pomohl vyřešit a vyřešit problémy, se kterými se můžete setkat s nasazením Azure File Sync. Popisujeme také, jak shromažďovat důležité protokoly ze systému, pokud je vyžadováno hlubší zkoumání problému. Pokud odpověď na vaši otázku nevidíte, můžete nás kontaktovat prostřednictvím následujících kanálů (v eskalujícím pořadí):

1. [Fórum úložišť Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
2. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).
3. Podpora společnosti Microsoft. Pokud chcete vytvořit novou žádost o podporu, na webu Azure portal na kartě **Nápověda** vyberte tlačítko **Nápověda + podpora** a pak vyberte **Nová žádost o podporu**.

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Mám problém se synchronizací souborů Azure na serveru (synchronizace, vrstvení v cloudu atd.). Mám odebrat a znovu vytvořit koncový bod serveru?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Instalace agenta a registrace serveru
<a id="agent-installation-failures"></a>**Poradce při potížích s instalací agenta**  
Pokud se instalace agenta synchronizace souborů Azure nezdaří, spusťte na příkazovém řádku se zvýšenými oprávněními následující příkaz, který zapne protokolování během instalace agenta:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Zkontrolujte soubor installer.log a zjistěte příčinu selhání instalace.

<a id="agent-installation-on-DC"></a>**Instalace agenta se nezdaří na řadiči domény služby Active Directory.**  
Pokud se pokusíte nainstalovat agenta synchronizace do řadiče domény služby Active Directory, kde je vlastník role Primární řadič domény v systému Windows Server 2008 R2 nebo pod verzí operačního systému, může dojít k problému, kdy se agentu synchronizace nepodaří nainstalovat.

Chcete-li tuto aktualizaci vyřešit, přeneste roli Primárního řadiče domény do jiného řadiče domény se systémem Windows Server 2012 R2 nebo novějším a nainstalujte synchronizaci.

<a id="parameter-is-incorrect"></a>**Přístup ke svazku v systému Windows Server 2012 R2 se nezdaří s chybou: Parametr je nesprávný**  
Po vytvoření koncového bodu serveru v systému Windows Server 2012 R2 dojde při přístupu ke svazku k následující chybě:

písmeno_jednotky:\ není přístupný.  
Parametr je nesprávný.

Chcete-li to vyřešit, nainstalujte nejnovější aktualizace pro systém Windows Server 2012 R2 a restartujte server.

<a id="server-registration-missing-subscriptions"></a>**Registrace serveru neuvádí všechna předplatná Azure**  
Při registraci serveru pomocí ServerRegistration.exe, odběry chybí po kliknutí na rozevírací seznam předplatného Azure.

K tomuto problému dochází, protože ServerRegistration.exe aktuálně nepodporuje víceklientská prostředí. Tento problém bude vyřešen v budoucí aktualizaci agenta Synchronizace souborů Azure.

Chcete-li tento problém vyřešit, zaregistrujte server pomocí následujících příkazů prostředí PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<guid>" -TenantID "<guid>"
Register-AzureRmStorageSyncServer -SubscriptionId "<guid>" -ResourceGroupName "<string>" -StorageSyncServiceName "<string>"
```

<a id="server-registration-prerequisites"></a>**Registrace serveru zobrazí následující zprávu: "Chybí předpoklady"**  
Tato zpráva se zobrazí, pokud modul Az nebo AzureRM PowerShell není nainstalován v prostředí PowerShell 5.1. 

> [!Note]  
> ServerRegistration.exe nepodporuje prostředí PowerShell 6.x. K registraci serveru můžete použít rutinu Register-AzStorageSyncServer v prostředí PowerShell 6.x.

Chcete-li nainstalovat modul Az nebo AzureRM v prostředí PowerShell 5.1, proveďte následující kroky:

1. Zadejte **powershell** z příkazového řádku se zvýšenými oprávněními a stiskněte klávesu ENTER.
2. Nainstalujte nejnovější modul Az nebo AzureRM podle dokumentace:
    - [Az modul (vyžaduje .NET 4.7.2)](https://go.microsoft.com/fwlink/?linkid=2062890)
    - [Modul AzureRM]( https://go.microsoft.com/fwlink/?linkid=856959)
3. Spusťte soubor ServerRegistration.exe a dokončete průvodce registrací serveru pomocí služby Synchronizace úložiště.

<a id="server-already-registered"></a>**Registrace serveru zobrazí následující zprávu: "Tento server je již zaregistrován"** 

![Snímek obrazovky s dialogem Registrace serveru s chybovou zprávou "server je již zaregistrován"](media/storage-sync-files-troubleshoot/server-registration-1.png)

Tato zpráva se zobrazí, pokud byl server dříve zaregistrován službou Synchronizace úložiště. Chcete-li zrušit registraci serveru z aktuální služby synchronizace úložiště a pak se zaregistrovat pomocí nové služby synchronizace úložiště, proveďte kroky popsané v části [Zrušení registrace serveru pomocí synchronizace souborů Azure](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Pokud server není uveden v části **Registrované servery** ve službě Synchronizace úložiště, spusťte na serveru, který chcete zrušit registraci, následující příkazy prostředí PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Pokud je server součástí clusteru, můžete použít volitelný parametr *Reset-StorageSyncServer -CleanClusterRegistration* k odebrání registrace clusteru.

<a id="web-site-not-trusted"></a>**Když jsem se zaregistrovat server, vidím četné "webové stránky nedůvěryhodný" odpovědi. Proč?**  
K tomuto problému dochází, pokud je při registraci serveru **povolena rozšířená zásada zabezpečení aplikace Internet Explorer.** Další informace o tom, jak správně zakázat **zásady rozšířeného zabezpečení aplikace Internet Explorer,** najdete [v tématu Příprava windows serveru na použití se synchronizací souborů Azure](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) a jak [nasadit Azure File Sync](storage-sync-files-deployment-guide.md).

<a id="server-registration-missing"></a>**Server není uveden pod registrovanými servery na webu Azure Portal.**  
Pokud server není uveden v části **Registrované servery** pro službu synchronizace úložiště:
1. Přihlaste se k serveru, který chcete zaregistrovat.
2. Otevřete Průzkumníka souborů a přejděte do instalačního adresáře Agenta synchronizace úložiště (výchozí umístění je C:\Program Files\Azure\StorageSyncAgent). 
3. Spusťte soubor ServerRegistration.exe a dokončete průvodce registrací serveru pomocí služby Synchronizace úložiště.

## <a name="sync-group-management"></a>Synchronizace správy skupin
<a id="cloud-endpoint-using-share"></a>**Vytvoření cloudového koncového bodu se nezdaří s touto chybou: "Zadaný Azure FileShare je již používán jiným cloudendpointem"**  
K této chybě dojde v případě, že sdílenou složku Azure již používá jiný koncový bod cloudu. 

Pokud se zobrazí tato zpráva a sdílená složka Azure se momentálně nepoužívá cloudový koncový bod, proveďte následující kroky, abyste vymažou metadata Azure File Sync ve sdílené složce Azure:

> [!Warning]  
> Odstranění metadat ve sdílené složce Azure, která se aktuálně používá cloudovým koncovým bodem, způsobí selhání operací azure file sync. 

1. Na webu Azure Portal přejděte do sdílené složky Azure.  
2. Klikněte pravým tlačítkem myši na sdílenou složku Azure a pak vyberte **Upravit metadata**.
3. Klepněte pravým tlačítkem myši na **položku SyncService**a vyberte příkaz **Odstranit**.

<a id="cloud-endpoint-authfailed"></a>**Vytvoření koncového bodu cloudu se nezdaří s touto chybou: "AuthorizationFailed"**  
K této chybě dochází, pokud váš uživatelský účet nemá dostatečná práva k vytvoření koncového bodu cloudu. 

Chcete-li vytvořit koncový bod cloudu, musí mít váš uživatelský účet následující oprávnění k autorizaci společnosti Microsoft:  
* Přečtěte si: Získat definici role
* Zápis: Vytvoření nebo aktualizace vlastní definice role
* Přečtěte si: Získat přiřazení role
* Zápis: Vytvoření přiřazení role

Následující předdefinované role mají požadovaná oprávnění autorizace společnosti Microsoft:  
* Vlastník
* Správce přístupu uživatelů

Chcete-li zjistit, zda má role uživatelského účtu požadovaná oprávnění:  
1. Na webu Azure Portal vyberte **Skupiny prostředků**.
2. Vyberte skupinu prostředků, ve které se nachází účet úložiště, a pak vyberte **řízení přístupu (IAM).**
3. Vyberte kartu **Přiřazení rolí.**
4. Vyberte **roli** (například Vlastník nebo Přispěvatel) pro váš uživatelský účet.
5. V seznamu **Zprostředkovatel prostředků** vyberte možnost **Autorizace společnosti Microsoft**. 
    * **Přiřazení role** by měla mít oprávnění **ke čtení** a **zápisu.**
    * **Definice role** by měla mít oprávnění **ke čtení** a **zápisu.**

<a id="-2134375898"></a>**Vytvoření koncového bodu serveru se nezdaří s touto chybou: "MgmtServerJobFailed" (Kód chyby: -2134375898 nebo 0x80c80226)**  
K této chybě dochází v případě, že je cesta ke koncovému bodu serveru na systémovém svazku a je povolené vrstvení cloudu. Vrstvení cloudu se na systémovém svazku nepodporuje. Pokud chcete vytvořit koncový bod serveru na systémovém svazku, při vytváření koncového bodu serveru zakažte vrstvení cloudu.

<a id="-2147024894"></a>**Vytvoření koncového bodu serveru se nezdaří s touto chybou: "MgmtServerJobFailed" (Kód chyby: -2147024894 nebo 0x80070002)**  
K této chybě dochází v případě, že zadaná cesta ke koncovému bodu serveru není platná. Ověřte, že zadaná cesta ke koncovému bodu serveru odpovídá místně připojenému svazku NTFS. Všimněte si, že Azure File Sync nepodporuje namapované jednotky jako cestu koncového bodu serveru.

<a id="-2134375640"></a>**Vytvoření koncového bodu serveru se nezdaří s touto chybou: "MgmtServerJobFailed" (Kód chyby: -2134375640 nebo 0x80c80328)**  
K této chybě dochází, pokud zadaná cesta koncového bodu serveru není svazkem NTFS. Ověřte, že zadaná cesta ke koncovému bodu serveru odpovídá místně připojenému svazku NTFS. Všimněte si, že Azure File Sync nepodporuje namapované jednotky jako cestu koncového bodu serveru.

<a id="-2134347507"></a>**Vytvoření koncového bodu serveru se nezdaří s touto chybou: "MgmtServerJobFailed" (Kód chyby: -2134347507 nebo 0x80c8710d)**  
K této chybě dochází, protože Synchronizace souborů Azure nepodporuje koncové body serveru na svazcích, které obsahují komprimovanou složku s informacemi o systémovém svazku. Pokud chcete tento problém vyřešit, dekomprimujte složku s informacemi o systémovém svazku. Pokud je složka s informacemi o systémovém svazku jedinou komprimovanou složkou na svazku, postupujte následovně:

1. Stáhněte si nástroj [PsExec.](https://docs.microsoft.com/sysinternals/downloads/psexec)
2. Spuštění následujícího příkazu z příkazového řádku se zvýšenými oprávněními pro spuštění příkazového řádku spuštěného pod systémovým účtem: **PsExec.exe -i -s -d cmd**
3. Na příkazovém řádku spuštěném pod systémovým účtem zadejte následující příkazy a stiskněte Enter:   
    **cd /d "písmeno jednotky:\Informace o systémovém svazku"**  
    **kompaktní /u /s**

<a id="-2134376345"></a>**Vytvoření koncového bodu serveru se nezdaří s touto chybou: "MgmtServerJobFailed" (Kód chyby: -2134376345 nebo 0x80C80067)**  
K této chybě dochází v případě, že dojde k dosažení limitu koncových bodů serverů na server. Synchronizace souborů Azure v současné době podporuje až 30 koncových bodů serveru na server. Další informace najdete v [tématu Cíle škálování synchronizace souborů Azure](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-file-sync-scale-targets).

<a id="-2134376427"></a>**Vytvoření koncového bodu serveru se nezdaří s touto chybou: "MgmtServerJobFailed" (Kód chyby: -2134376427 nebo 0x80c80015)**  
K této chybě dochází v případě, že se v zadané cestě ke koncovému bodu serveru již synchronizuje jiný koncový bod serveru. Synchronizace souborů Azure nepodporuje synchronizaci stejného adresáře nebo svazku několika koncovými body serveru.

<a id="-2160590967"></a>**Vytvoření koncového bodu serveru se nezdaří s touto chybou: "MgmtServerJobFailed" (Kód chyby: -2160590967 nebo 0x80c80077)**  
K této chybě dochází, pokud cesta koncového bodu serveru obsahuje osamocené vrstvené soubory. Pokud byl koncový bod serveru nedávno odebrán, počkejte, dokud nebude dokončeno vyčištění osamocených vrstvených souborů. ID události 6662 je zaznamenána do protokolu událostí telemetrie po vyčištění osamocené vrstvené soubory. ID události 6661 je zaznamenána po dokončení vyčištění osamocené vrstvené soubory a koncový bod serveru lze znovu vytvořit pomocí cesty. Pokud se vytvoření koncového bodu serveru nezdaří po zaznamenání ID události 6661, odeberte osamocené vrstvené soubory provedením kroků zdokumentovaných v [vrstvených souborech, které nejsou přístupné na serveru po odstranění oddílu koncového bodu serveru.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)

<a id="-2134347757"></a>**Odstranění koncového bodu serveru se nezdaří, s touto chybou: "MgmtServerJobExpired" (Kód chyby: -2134347757 nebo 0x80c87013)**  
K této chybě dojde v případě, že je server offline nebo nemá připojení k síti. Pokud server už není k dispozici, zrušte registraci serveru na portálu. Tím se odstraní koncové body serveru. Chcete-li odstranit koncové body serveru, postupujte podle kroků popsaných v části [Zrušení registrace serveru pomocí Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

<a id="server-endpoint-provisioningfailed"></a>**Nelze otevřít stránku vlastností koncového bodu serveru nebo aktualizovat zásady vrstvení cloudu.**  
K tomuto problému může dojít, pokud se nezdaří operace správy na koncovém bodu serveru. Pokud se stránka vlastností koncového bodu serveru neotevře na webu Azure Portal, může tento problém vyřešit aktualizace koncového bodu serveru pomocí příkazů Prostředí PowerShell ze serveru. 

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
<a id="server-endpoint-noactivity"></a>**Koncový bod serveru má stav "Žádná aktivita" nebo "Čeká na vyřízení" a stav serveru v okně registrované servery je "Zobrazí se offline"**  

K tomuto problému může dojít, pokud proces sledování synchronizace úložiště (AzureStorageSyncMonitor.exe) není spuštěn nebo server není schopen získat přístup ke službě Synchronizace souborů Azure.

Na serveru, který se na portálu zobrazuje jako "Zobrazuje se offline", se podívejte na ID události 9301 v protokolu událostí telemetrie (umístěném v části Aplikace a služby\Microsoft\FileSync\Agent v Prohlížeči událostí), abyste zjistili, proč server nemůže získat přístup ke službě Synchronizace souborů Azure. 

- Pokud **getnextjob dokončena se stavem: 0** je zaznamenána, server může komunikovat se službou Azure File Sync. 
    - Otevřete na serveru Správce úloh a ověřte, že je spuštěný proces monitorování synchronizace úložiště (AzureStorageSyncMonitor.exe). Pokud tento proces není spuštěný, zkuste nejprve server restartovat. Pokud restartování serveru problém nevyřeší, upgradujte na nejnovější [verzi agenta](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes) Synchronizace souborů Azure. 

- Pokud **getnextjob dokončena se stavem: -2134347756** je zaznamenána, server není schopen komunikovat se službou Azure File Sync z důvodu brány firewall nebo proxy serveru. 
    - Pokud je server za bránou firewall, ověřte, že je povolený odchozí port 443. Pokud brána firewall omezuje provoz na určité domény, zkontrolujte, zda jsou přístupné domény uvedené v [dokumentaci brány](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#firewall) firewall.
    - Pokud je server za proxy serverem, nakonfigurujte nastavení serveru proxy pro celý počítač nebo konkrétní aplikaci podle kroků v [dokumentaci k](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#proxy)serveru proxy .
    - Pomocí rutiny Test-StorageSyncNetworkConnectivity zkontrolujte připojení k síti ke koncovým bodům služby. Další informace najdete v [tématu Testování připojení k síti ke koncovým bodům služby](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#test-network-connectivity-to-service-endpoints).

- Pokud **getnextjob dokončena se stavem: -2134347764** je zaznamenána, server není schopen komunikovat se službou Azure File Sync z důvodu vypršela nebo odstraněný certifikát.  
    - Spusťte na serveru následující příkaz Prostředí PowerShell a obnovte certifikát použitý pro ověřování:
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**Koncový bod serveru má stav "Žádná aktivita" a stav serveru v okně registrované servery je "Online"**  

Stav koncového bodu serveru "Žádná aktivita" znamená, že koncový bod serveru nezaznamenal aktivitu synchronizace za poslední dvě hodiny.

Chcete-li zkontrolovat aktuální aktivitu synchronizace na serveru, přečtěte si téma [Jak lze sledovat průběh aktuální relace synchronizace?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

Koncový bod serveru nemusí protokolovat aktivitu synchronizace několik hodin z důvodu chyby nebo nedostatku systémových prostředků. Ověřte, zda je nainstalovaná nejnovější [verze agenta](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes) Azure File Sync. Pokud problém přetrvává, otevřete žádost o podporu.

> [!Note]  
> Pokud je stav serveru v okně registrované servery "Zobrazí se offline", proveďte kroky zdokumentované v [koncovém bodu serveru má stav "Žádná aktivita" nebo "Čeká na vyřízení" a stav serveru v okně registrované servery je "Zobrazí se offline"](#server-endpoint-noactivity) část.

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**Pokud jsem vytvořil soubor přímo ve sdílené složce Azure přes SMB nebo prostřednictvím portálu, jak dlouho trvá, než se soubor synchronizuje se servery ve skupině synchronizace?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**Stav koncového bodu serveru je ve stavu čekající na vyřízení po dobu několika hodin**  
Tento problém se očekává, pokud vytvoříte koncový bod cloudu a použít sdílenou složku Azure, která obsahuje data. Úloha výčtu změn, která prohledá změny ve sdílené složce Azure, musí být dokončena před synchronizací souborů mezi koncovými body cloudu a serveru. Čas dokončení úlohy závisí na velikosti oboru názvů ve sdílené složce Azure. Stav koncového bodu serveru by se měl aktualizovat po dokončení úlohy výčtu změn.

### <a name="how-do-i-monitor-sync-health"></a><a id="broken-sync"></a>Jak monitorovat stav synchronizace?
# <a name="portal"></a>[Portál](#tab/portal1)
V rámci každé skupiny synchronizace můžete přejít k podrobnostem jednotlivých koncových bodů serveru a zobrazit stav poslední dokončené relací synchronizace. Zelený sloupec Stav a hodnota Nesynchronizované soubory 0 označují, že synchronizace funguje podle očekávání. Pokud tomu tak není, naleznete níže seznam běžných chyb synchronizace a způsob zpracování souborů, které nejsou synchronizovány. 

![Snímek obrazovky s portálem Azure](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="server"></a>[Server](#tab/server)
Přejděte na protokoly telemetrie serveru, které lze nalézt `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`v Prohlížeči událostí na adrese . Událost 9102 odpovídá dokončené relaci synchronizace. Pokud hledáte nejnovější stav synchronizace, vyhledejte nejnovější událost s ID 9102. SyncDirection vám řekne, jestli toto zasedání bylo nahrát nebo stáhnout. Pokud HResult je 0, relace synchronizace byla úspěšná. Nenulový HResult znamená, že během synchronizace došlo k chybě; seznam běžných chyb naleznete níže. Pokud PerItemErrorCount je větší než 0, to znamená, že některé soubory nebo složky nebyly synchronizovány správně. Je možné mít HResult 0, ale PerItemErrorCount, který je větší než 0.

Níže je uveden příklad úspěšného nahrání. Z důvodu stručnosti jsou níže uvedeny pouze některé hodnoty obsažené v každé události 9102. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Naopak neúspěšné nahrávání může vypadat takto:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Někdy synchronizační relace selžou celkově nebo mají nenulový PerItemErrorCount, ale stále postupují vpřed, přičemž některé soubory se úspěšně synchronizují. To lze vidět v applied* pole (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount a AppliedSizeBytes), které vám řeknou, kolik relace je úspěšné. Pokud se zobrazí více synchronizačních relací v řadě, které selhávají, ale mají rostoucí počet Applied*, měli byste dát synchronizaci čas, abyste to zkusili znovu před otevřením lístku podpory.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Jak monitorovat průběh aktuální relace synchronizace?
# <a name="portal"></a>[Portál](#tab/portal1)
V rámci skupiny synchronizace přejděte na daný koncový bod serveru a v části Aktivita synchronizace se podívejte na počet souborů nahraných nebo stažených v aktuální relaci synchronizace. Všimněte si, že tento stav bude zpožděn o přibližně 5 minut a pokud je relace synchronizace dostatečně malá, aby mohla být dokončena během této doby, nemusí být na portálu hlášena. 

# <a name="server"></a>[Server](#tab/server)
Podívejte se na poslední událost 9302 v protokolu telemetrie na serveru (v Prohlížeči událostí přejděte na protokoly aplikací a služeb\Microsoft\FileSync\Agent\Telemetrie). Tato událost označuje stav aktuální relace synchronizace. TotalItemCount označuje, kolik souborů mají být synchronizovány, AppliedItemCount počet souborů, které byly synchronizovány tak daleko, a PerItemErrorCount počet souborů, které se nedaří synchronizovat (viz níže, jak se s tím vypořádat).

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

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>Jak zjistit, jestli jsou servery mezi sebou synchronizované?
# <a name="portal"></a>[Portál](#tab/portal1)
U každého serveru v dané skupině synchronizace se ujistěte, že:
- Časová razítka pro poslední pokus o synchronizaci pro nahrávání i stahování jsou nedávné.
- Stav je zelený pro nahrávání i stahování.
- Pole Synchronizovat aktivitu zobrazuje velmi málo nebo žádné soubory, které by zůstaly synchronizovány.
- Pole Soubory, které není synchronizováno, je 0 pro nahrávání i stahování.

# <a name="server"></a>[Server](#tab/server)
Podívejte se na dokončené relace synchronizace, které jsou označeny 9102 událostí v protokolu `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`událostí telemetrie pro každý server (v Prohlížeči událostí přejděte na ). 

1. Na libovolném serveru se chcete ujistit, že poslední relace nahrávání a stahování byly úspěšně dokončeny. Chcete-li to provést, zkontrolujte, zda HResult a PerItemErrorCount jsou 0 pro nahrávání i stahování (syncdirection pole označuje, pokud daná relace je nahrávání nebo stahování relace). Všimněte si, že pokud nevidíte nedávno dokončenou relaci synchronizace, je pravděpodobné, že právě probíhá relace synchronizace, což lze očekávat, pokud jste právě přidali nebo upravili velké množství dat.
2. Když je server plně aktuální s cloudem a nemá žádné změny synchronizovat v obou směrech, uvidíte prázdné relace synchronizace. Ty jsou označeny upload a download události, ve kterých všechna data Sync* (SyncFileCount, SyncDirCount, SyncTombstoneCount a SyncSizeBytes) jsou nulové, což znamená, že nebylo nic k synchronizaci. Všimněte si, že tyto prázdné synchronizační relace nemusí dojít na serverech s vysokou změnou, protože je vždy něco nového k synchronizaci. Pokud neexistuje žádná aktivita synchronizace, měly by se vyskytovat každých 30 minut. 
3. Pokud jsou všechny servery aktuální s cloudem, což znamená, že jejich nedávné relace nahrávání a stahování jsou prázdné synchronizační relace, můžete s přiměřenou jistotou říci, že systém jako celek je synchronizován. 
    
Všimněte si, že pokud jste provedli změny přímo ve sdílené složce Azure, Azure File Sync nezjistí tuto změnu, dokud se spustí výčet změn, což se stane jednou za 24 hodin. Je možné, že server bude říkat, že je aktuální s cloudem, když ve skutečnosti chybí nedávné změny provedené přímo ve sdílené složce Azure. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Jak zjistím, jestli se některé konkrétní soubory nebo složky nesynchronizují?
Pokud vaše PerItemErrorCount na serveru nebo soubory nesynchronizování počet na portálu jsou větší než 0 pro danou relaci synchronizace, to znamená, že některé položky se nedaří synchronizovat. Soubory a složky mohou mít vlastnosti, které jim brání v synchronizaci. Tyto vlastnosti mohou být trvalé a vyžadují explicitní akci k obnovení synchronizace, například odebrání nepodporovaných znaků z názvu souboru nebo složky. Mohou být také přechodné, což znamená, že soubor nebo složka automaticky obnoví synchronizaci; například soubory s otevřenými táhly automaticky obnoví synchronizaci při zavření souboru. Když modul Synchronizace souborů Azure zjistí takový problém, je vytvořen protokol chyb, který lze analyzovat na seznam položek, které se aktuálně nesynchronizují správně.

Chcete-li tyto chyby zobrazit, spusťte skript PowerShell **FileSyncErrorsReport.ps1** (umístěný v instalačním adresáři agenta Azure File Sync) k identifikaci souborů, které se nepodařilo synchronizovat z důvodu otevřených popisovačů, nepodporovaných znaků nebo jiných problémů. Pole ItemPath vám sděluje umístění souboru ve vztahu ke kořenovému synchronizačnímu adresáři. Postup nápravy naleznete v seznamu běžných chyb synchronizace níže.

> [!Note]  
> Pokud skript FileSyncErrorsReport.ps1 vrátí "Nebyly nalezeny žádné chyby souboru" nebo neuvádí chyby pro položku pro skupinu synchronizace, je příčinou buď:
>
>- Příčina 1: Poslední dokončená relace synchronizace neměla chyby pro položce. Portál by měl být brzy aktualizován, aby zobrazoval 0 souborů, které nejsou synchronizovány. 
>   - Zkontrolujte [ID události 9102](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) v protokolu událostí telemetrie a potvrďte, že PerItemErrorCount je 0. 
>
>- Příčina 2: Protokol událostí ItemResults na serveru zabalený z důvodu příliš mnoha chyb na položku a protokol událostí již neobsahuje chyby pro tuto skupinu synchronizace.
>   - Chcete-li tomuto problému zabránit, zvyšte velikost protokolu událostí ItemResults. Protokol událostí ItemResults naleznete v poli "Protokoly aplikací a služeb\Microsoft\FileSync\Agent" v prohlížeči událostí. 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Poradce při potížích s chybami synchronizace souborů nebo adresářů
**Protokol ItemResults - chyby synchronizace podle položek**  

| HRESULT | HRESULT (desítkové) | Text chyby | Problém | Odstranění rizika |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | Vrstvený soubor na serveru není přístupný. K tomuto problému dojde v případě, že se před odstraněním koncového bodu serveru neodvolal vrstvený soubor. | Chcete-li tento problém vyřešit, naleznete [v tématu Vrstvené soubory nejsou přístupné na serveru po odstranění koncového bodu serveru](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | Změnu souboru nebo adresáře zatím nelze synchronizovat, protože závislá složka ještě není synchronizována. Tato položka se synchronizuje po synchronizaci závislých změn. | Nevyžaduje se žádná akce. Pokud chyba přetrvává několik dní, použijte skript Prostředí PowerShell FileSyncErrorsReport.ps1 k určení, proč se závislá složka ještě nesynchronizovala. |
| 0x80c80284 | -2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | Změnu souboru nebo adresáře zatím nelze synchronizovat, protože závislá složka ještě není synchronizována a relace synchronizace se nezdařila. Tato položka se synchronizuje po synchronizaci závislých změn. | Nevyžaduje se žádná akce. Pokud chyba přetrvává, prozkoumejte selhání relace synchronizace. |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | Název souboru nebo adresáře je neplatný. | Přejmenujte daný soubor nebo adresář. Další informace naleznete [v tématu Zpracování nepodporovaných znaků.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | Název souboru nebo adresáře je neplatný. | Přejmenujte daný soubor nebo adresář. Další informace naleznete [v tématu Zpracování nepodporovaných znaků.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | Soubor nelze synchronizovat, protože je používán. Soubor se bude synchronizovat, jakmile se už nebude používat. | Nevyžaduje se žádná akce. Azure File Sync vytvoří dočasný snímek VSS jednou denně na serveru pro synchronizaci souborů, které mají otevřené popisovače. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | Soubor byl změněn, ale změna ještě nebyla synchronizována. Synchronizace se obnoví po zjištění této změny. | Nevyžaduje se žádná akce. |
| 0x80070002 | -2147024894 | ERROR_FILE_NOT_FOUND | Soubor byl odstraněn a synchronizace není vědoma změny. | Nevyžaduje se žádná akce. Synchronizace zastaví protokolování této chyby, jakmile zjišťování změn zjistí, že soubor byl odstraněn. |
| 0x80070003 | -2147942403 | ERROR_PATH_NOT_FOUND | Odstranění souboru nebo adresáře nelze synchronizovat, protože položka již byla odstraněna v cílovém umístění a synchronizace o této změně neví. | Nevyžaduje se žádná akce. Synchronizace zastaví protokolování této chyby, jakmile se spustí zjišťování změn v cílovém umístění a synchronizace zjistí, že položka byla odstraněna. |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | Soubor nebo adresář byl přeskočen, ale bude synchronizován během další relace synchronizace. Pokud je tato chyba hlášena při stahování položky, je název souboru nebo adresáře více než pravděpodobně neplatný. | Pokud je tato chyba hlášena při nahrávání souboru, není nutná žádná akce. Pokud je chyba hlášena při stahování souboru, přejmenujte daný soubor nebo adresář. Další informace naleznete [v tématu Zpracování nepodporovaných znaků.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) |
| 0x800700B7 | -2147024713 | ERROR_ALREADY_EXISTS | Vytvoření souboru nebo adresáře nelze synchronizovat, protože položka již v cílovém umístění existuje a synchronizace o této změně neví. | Nevyžaduje se žádná akce. Synchronizace zastaví protokolování této chyby, jakmile se spustí zjišťování změn v cílovém umístění a synchronizace si je vědoma této nové položky. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Soubor není možné synchronizovat, protože došlo k dosažení limitu sdílené složky Azure. | Chcete-li tento problém vyřešit, [najdete v tématu Jste dosáhli limitu úložiště sdílené složky Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134351810) v průvodci odstraňováním potíží. |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | Soubor je zašifrován nepodporovaným řešením (například systém souborů NTFS EFS). | Dešifrujte soubor a použijte podporované šifrovací řešení. Seznam podporovaných řešení najdete v průvodci plánováním v části [Šifrovací řešení](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#encryption). |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | Soubor je umístěn ve složce replikace jen pro čtení dfs-R. | Soubor je umístěn ve složce replikace jen pro čtení dfs-R. Synchronizace souborů Azure nepodporuje koncové body serveru ve složkách replikace DFS-R jen pro čtení. Další informace naleznete v příručce k [plánování.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs) |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Soubor má stav odstranění čekající na vyřízení. | Nevyžaduje se žádná akce. Soubor bude odstraněn, jakmile budou uzavřeny všechny otevřené popisovače souborů. |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | Soubor nelze synchronizovat, protože je povolena brána firewall a nastavení virtuální sítě v účtu úložiště a server nemá přístup k účtu úložiště. | Přidejte adresu IP serveru nebo virtuální síť podle kroků zdokumentovaných v části [Konfigurovat bránu firewall a nastavení virtuální sítě](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) v průvodci nasazením. |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | Soubor nelze synchronizovat, protože velikost popisovače zabezpečení překračuje limit 64 KiB. | Pokud chcete tento problém vyřešit, odeberte položky řízení přístupu (ACE) pro soubor, abyste snížili velikost popisovače zabezpečení. |
| 0x8000ffffff | -2147418113 | E_UNEXPECTED | Soubor nelze synchronizovat z důvodu neočekávané chyby. | Pokud chyba přetrvává několik dní, otevřete případ podpory. |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | Soubor nelze synchronizovat, protože je používán. Soubor se bude synchronizovat, jakmile se už nebude používat. | Nevyžaduje se žádná akce. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Soubor byl během synchronizace změněn, takže je třeba jej znovu synchronizovat. | Nevyžaduje se žádná akce. |
| 0x80070017 | -2147024873 | ERROR_CRC | Soubor nelze synchronizovat z důvodu chyby CRC. K této chybě může dojít, pokud vrstvený soubor nebyl odvolán před odstraněním koncového bodu serveru nebo pokud je soubor poškozen. | Chcete-li tento problém vyřešit, naleznete v [tématu Vrstvené soubory nejsou přístupné na serveru po odstranění koncového bodu serveru](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) odebrat vrstvené soubory, které jsou osamocené. Pokud k chybě dochází i po odebrání oprhaned vrstvené soubory, spusťte [chkdsk](https://docs.microsoft.com/windows-server/administration/windows-commands/chkdsk) na svazku. |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | Soubor nelze synchronizovat, protože bylo dosaženo maximálního počtu souborů konfliktů. Azure File Sync podporuje 100 souborů konfliktů na soubor. Další informace o konfliktech souborů najdete v [tématu Nejčastější dotazy k](https://docs.microsoft.com/azure/storage/files/storage-files-faq#afs-conflict-resolution)synchronizaci souborů v Azure . | Chcete-li tento problém vyřešit, snižte počet souborů konfliktů. Soubor se synchronizuje, jakmile je počet souborů konfliktu menší než 100. |

#### <a name="handling-unsupported-characters"></a>Zpracování nepodporovaných znaků
Pokud skript Prostředí PowerShell **FileSyncErrorsReport.ps1** zobrazuje selhání způsobená nepodporovanými znaky (kód chyby 0x8007007b nebo 0x80c80255), měli byste odebrat nebo přejmenovat znaky, které byly chybné z příslušných názvů souborů. Prostředí PowerShell pravděpodobně vytiskne tyto znaky jako otazníky nebo prázdné obdélníky, protože většina těchto znaků nemá standardní vizuální kódování. [Nástroj pro vyhodnocení](storage-sync-files-planning.md#evaluation-cmdlet) lze použít k identifikaci znaků, které nejsou podporovány.

Níže uvedená tabulka obsahuje všechny znaky unicode Azure File Sync ještě nepodporuje.

| Znaková sada | Počet znaků |
|---------------|-----------------|
| <ul><li>0x0000009D (příkaz operačního systému OSC)</li><li>0x00000090 (řídicí řetězec dcs zařízení)</li><li>0x0000008F (ss3 jednosměnný provoz 3)</li><li>0x0000081 (přednastavení vysokého oktetu)</li><li>0x0000007F (del delete)</li><li>0x0000008D (posuv zpětného směru)</li></ul> | 6 |
| 0x0000FDD0 - 0x0000FDEF (arabské prezentační formuláře-a) | 32 |
| 0x0000FFF0 - 0x0000FFFF (speciály) | 16 |
| <ul><li>0x0001FFFE - 0x0001FFFF = 2 (neznak)</li><li>0x0002FFFE - 0x0002FFFF = 2 (neznak)</li><li>0x0003FFFE - 0x0003FFFF = 2 (neznak)</li><li>0x0004FFFE - 0x0004FFFF = 2 (neznak)</li><li>0x0005FFFE - 0x0005FFFF = 2 (neznak)</li><li>0x0006FFFE - 0x0006FFFF = 2 (neznak)</li><li>0x0007FFFE - 0x0007FFFF = 2 (neznak)</li><li>0x0008FFFE - 0x0008FFFF = 2 (neznak)</li><li>0x0009FFFE - 0x0009FFFF = 2 (neznak)</li><li>0x000AFFFE - 0x000AFFFF = 2 (neznak)</li><li>0x000BFFFE - 0x000BFFFF = 2 (neznak)</li><li>0x000CFFFE - 0x000CFFFF = 2 (neznak)</li><li>0x000DFFFE - 0x000DFFFF = 2 (neznak)</li><li>0x000EFFFE - 0x000EFFFF = 2 (nedefinováno)</li><li>0x000FFFFE - 0x000FFFFF = 2 (doplňková soukromá užitná plocha)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Běžné chyby synchronizace
<a id="-2147023673"></a>**Relace synchronizace byla zrušena.**  

| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (desítkové)** | -2147023673 | 
| **Text chyby** | ERROR_CANCELLED |
| **Nutná náprava** | Ne |

Synchronizace relací může selhat z různých důvodů, včetně serveru restartování nebo aktualizace, snímků VSS atd. Přestože tato chyba vypadá, že vyžaduje zpracování, je bezpečné ignorovat tuto chybu, pokud přetrvává po dobu několika hodin.

<a id="-2147012889"></a>**Nelze navázat spojení se službou.**    

| | |
|-|-|
| **HRESULT** | 0x80072ee7 |
| **HRESULT (desítkové)** | -2147012889 | 
| **Text chyby** | WININET_E_NAME_NOT_RESOLVED |
| **Nutná náprava** | Ano |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**Požadavek uživatele byl omezen službou.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (desítkové)** | -2134376372 |
| **Text chyby** | ECS_E_USER_REQUEST_THROTTLED |
| **Nutná náprava** | Ne |

Není nutná žádná akce; server se pokusí znovu. Pokud tato chyba trvá několik hodin, vytvořte žádost o podporu.

<a id="-2134364043"></a>**Synchronizace je blokována, dokud se po obnovení nedokončí zjišťování změn.**  

| | |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT (desítkové)** | -2134364043 |
| **Text chyby** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Nutná náprava** | Ne |

Nevyžaduje se žádná akce. Když se soubor nebo sdílená složka (koncový bod cloudu) obnoví pomocí služby Azure Backup, synchronizace se bude blokovat, dokud se zjišťování změn ve sdílené složce Azure nedokončí. Detekce změn se spustí okamžitě po dokončení obnovení a délka jejího trvání závisí na počtu souborů ve sdílené složce.

<a id="-2147216747"></a>**Synchronizace se nezdařila, protože byla uvolněna databáze synchronizace.**  

| | |
|-|-|
| **HRESULT** | 0x80041295 |
| **HRESULT (desítkové)** | -2147216747 |
| **Text chyby** | SYNC_E_METADATA_INVALID_OPERATION |
| **Nutná náprava** | Ne |

K této chybě obvykle dochází v případě, že aplikace zálohování vytvoří snímek VSS a databáze synchronizace se uvolní. Pokud tato chyba trvá několik hodin, vytvořte žádost o podporu.

<a id="-2134364065"></a>**Synchronizace nemá přístup ke sdílené složce Azure zadané v cloudovém koncovém bodu.**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (desítkové)** | -2134364065 |
| **Text chyby** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **Nutná náprava** | Ano |

K této chybě dochází, protože agent Synchronizace souborů Azure nemůže získat přístup ke sdílené složce Azure. Důvodem může být to, že sdílená složka Azure nebo účet úložiště, který je jejím hostitelem, již neexistují. Při řešení této chyby můžete použít následující postup:

1. [Ověřte, zda účet úložiště existuje.](#troubleshoot-storage-account)
2. [Ujistěte se, že existuje sdílená složka Azure.](#troubleshoot-azure-file-share)
3. [Ujistěte se, že Azure File Sync má přístup k účtu úložiště.](#troubleshoot-rbac)
4. [Ověřte, že jsou v účtu úložiště správně nakonfigurovaná nastavení brány firewall a virtuální sítě (pokud jsou povolená)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings).

<a id="-2134351804"></a>**Synchronizace se nezdařila, protože požadavek není oprávněn k provedení této operace.**  

| | |
|-|-|
| **HRESULT** | 0x80c86044 |
| **HRESULT (desítkové)** | -2134351804 |
| **Text chyby** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **Nutná náprava** | Ano |

K této chybě dochází, protože agent Azure File Sync nemá oprávnění k přístupu ke sdílené složce Azure. Při řešení této chyby můžete použít následující postup:

1. [Ověřte, zda účet úložiště existuje.](#troubleshoot-storage-account)
2. [Ujistěte se, že existuje sdílená složka Azure.](#troubleshoot-azure-file-share)
3. [Ověřte, že jsou v účtu úložiště správně nakonfigurovaná nastavení brány firewall a virtuální sítě (pokud jsou povolená)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings).
4. [Ujistěte se, že Azure File Sync má přístup k účtu úložiště.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**Použitý název účtu úložiště nelze přeložit.**  

| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (desítkové)** | -2134364064 |
| **Text chyby** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Nutná náprava** | Ano |

1. Zkontrolujte, zda můžete přeložit název DNS úložiště ze serveru.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Ověřte, zda účet úložiště existuje.](#troubleshoot-storage-account)
3. [Ověřte, že jsou v účtu úložiště správně nakonfigurovaná nastavení brány firewall a virtuální sítě (pokud jsou povolená)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings).

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**Při přístupu k účtu úložiště došlo k neznámé chybě.**  

| | |
|-|-|
| **HRESULT** | 0x80c8308a |
| **HRESULT (desítkové)** | -2134364022 |
| **Text chyby** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **Nutná náprava** | Ano |

1. [Ověřte, zda účet úložiště existuje.](#troubleshoot-storage-account)
2. [Ověřte, že jsou v účtu úložiště správně nakonfigurovaná nastavení brány firewall a virtuální sítě (pokud jsou povolená)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings).

<a id="-2134364014"></a>**Synchronizace se nezdařila z důvodu uzamčení účtu úložiště.**  

| | |
|-|-|
| **HRESULT** | 0x80c83092 |
| **HRESULT (desítkové)** | -2134364014 |
| **Text chyby** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **Nutná náprava** | Ano |

K této chybě dochází, protože účet úložiště má [zámek prostředků](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)jen pro čtení . Chcete-li tento problém vyřešit, odeberte zámek prostředků jen pro čtení v účtu úložiště. 

<a id="-1906441138"></a>**Synchronizace se nezdařila z důvodu problému s databází synchronizace.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (desítkové)** | -1906441138 |
| **Text chyby** | JET_errWriteConflict |
| **Nutná náprava** | Ano |

K této chybě dochází, pokud dojde k potížím s interní databází používanou azure file sync. Když nastane tento problém, vytvořte žádost o podporu a my vás budeme kontaktovat, abychom vám pomohli tento problém vyřešit.

<a id="-2134364053"></a>**Verze agenta Azure File Sync nainstalovaná na serveru není podporovaná.**  

| | |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT (desítkové)** | -2134364053 |
| **Text chyby** | ECS_E_AGENT_VERSION_BLOCKED |
| **Nutná náprava** | Ano |

K této chybě dochází v případě, že se verze agenta Synchronizace souborů Azure nainstalovaná na serveru nepodporuje. Chcete-li tento problém vyřešit, [upgradujte]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) na [podporovanou verzi agenta]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions).

<a id="-2134351810"></a>**Dosáhli jste limitu úložiště sdílené složky Azure.**  

| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (desítkové)** | -2134351810 |
| **Text chyby** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Nutná náprava** | Ano |

K této chybě dochází při dosažení limitu úložiště sdílené složky Azure. K tomu může dojít v případě, že se pro sdílenou složku Azure použije kvóta nebo využití překročí limity pro sdílenou složku Azure. Další informace najdete v [aktuálních limitech pro sdílenou složku Azure](storage-files-scale-targets.md).

1. Přejděte do skupiny synchronizace ve službě Synchronizace úložiště.
2. Vyberte koncový bod cloudu v rámci skupiny synchronizace.
3. Poznamenejte si název sdílené složky Azure v otevřeném podokně.
4. Vyberte propojený účet úložiště. Pokud se tento odkaz nezdaří, byl odebrán odkazovaný účet úložiště.

    ![Snímek obrazovky s podoknem podrobností koncového bodu cloudu s odkazem na účet úložiště.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Vyberte **Soubory,** chcete-li zobrazit seznam sdílených složek.
6. Klikněte na tři tečky na konci řádku pro sdílenou složku Azure, na kterou odkazuje koncový bod cloudu.
7. Ověřte, že **využití** nedosahuje **kvóty**. Poznámka: Pokud nebyla zadána alternativní kvóta, kvóta bude odpovídat [maximální velikosti sdílené složky Azure](storage-files-scale-targets.md).

    ![Snímek obrazovky s vlastnostmi sdílené složky Azure.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Pokud je sdílená složka plná a kvóta není nastavená, jedním z možných způsobů, jak tento problém vyřešit, je převést každou podsložku aktuálního koncového bodu serveru na samostatný koncový bod serveru v samostatné skupině synchronizace. Tímto způsobem se jednotlivé podsložky budou synchronizovat do samostatných sdílených složek Azure.

<a id="-2134351824"></a>**Sdílená složka Azure nebyla nalezena.**  

| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (desítkové)** | -2134351824 |
| **Text chyby** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Nutná náprava** | Ano |

K této chybě dochází v případě, že je sdílená složka Azure nepřístupná. Řešení potíží:

1. [Ověřte, zda účet úložiště existuje.](#troubleshoot-storage-account)
2. [Ujistěte se, že existuje sdílená složka Azure.](#troubleshoot-azure-file-share)

Pokud byla sdílená složka Azure odstraněna, musíte vytvořit novou sdílenou složku a pak znovu vytvořit skupinu synchronizace. 

<a id="-2134364042"></a>**Synchronizace se pozastaví, když je toto předplatné Azure pozastaveno.**  

| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (desítkové)** | -2134364042 |
| **Text chyby** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Nutná náprava** | Ano |

K této chybě dochází v případě, že dojde k pozastavení předplatného Azure. Synchronizace se znovu povolí po obnovení předplatného Azure. Další informace najdete v tématu [Proč je moje předplatné Azure zakázané a jak ho znovu aktivuji?](../../cost-management-billing/manage/subscription-disabled.md)

<a id="-2134364052"></a>**Účet úložiště má nakonfigurovanou bránu firewall nebo virtuální sítě.**  

| | |
|-|-|
| **HRESULT** | 0x80c8306c |
| **HRESULT (desítkové)** | -2134364052 |
| **Text chyby** | ECS_E_MGMT_STORAGEACLSNOTSUPPORTED |
| **Nutná náprava** | Ano |

K této chybě dochází v případě, že je sdílená složka Azure nepřístupná kvůli bráně firewall účtu úložiště, nebo kvůli tomu, že účet úložiště patří do virtuální sítě. Ověřte, zda je správně nakonfigurováno nastavení brány firewall a virtuální sítě v účtu úložiště. Další informace naleznete v [tématu Konfigurace nastavení brány firewall a virtuální sítě](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings). 

<a id="-2134375911"></a>**Synchronizace se nezdařila z důvodu problému s databází synchronizace.**  

| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (desítkové)** | -2134375911 |
| **Text chyby** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Nutná náprava** | Ne |

Tato chyba se obvykle vyřeší sama a může k ní dojít v následujících případech:

* Vysoký počet změn souborů na serverech ve skupině synchronizace.
* Velké množství chyb v jednotlivých souborech a adresářích.

Pokud tato chyba přetrvává déle než několik hodin, vytvořte žádost o podporu a my vás budeme kontaktovat, abychom vám pomohli tento problém vyřešit.

<a id="-2146762487"></a>**Serveru se nepodařilo navázat zabezpečené připojení. Cloudová služba obdržela neočekávaný certifikát.**  

| | |
|-|-|
| **HRESULT** | 0x800b109 |
| **HRESULT (desítkové)** | -2146762487 |
| **Text chyby** | CERT_E_UNTRUSTEDROOT |
| **Nutná náprava** | Ano |

K této chybě může dojít, pokud vaše organizace používá ukončující proxy server TLS nebo pokud škodlivá entita zachycuje provoz mezi vaším serverem a službou Azure File Sync. Pokud jste si jisti, že se to očekává (protože vaše organizace používá ukončující proxy server TLS), přeskočíte ověření certifikátu s přepsáním registru.

1. Vytvořte hodnotu registru SkipVerifyingPinnedRootCertificate.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Restartujte službu synchronizace na registrovaném serveru.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Nastavením této hodnoty registru přijme agent Azure File Sync při přenosu dat mezi serverem a cloudovou službou jakýkoli místně důvěryhodný certifikát TLS/SSL.

<a id="-2147012894"></a>**Nelze navázat spojení se službou.**  

| | |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT (desítkové)** | -2147012894 |
| **Text chyby** | WININET_E_TIMEOUT |
| **Nutná náprava** | Ano |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**Synchronizace se nezdařila z důvodu problému s ověřováním.**  

| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (desítkové)** | -2134375680 |
| **Text chyby** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Nutná náprava** | Ano |

K této chybě obvykle dochází kvůli nesprávnému času na serveru. Pokud je server spuštěn ve virtuálním počítači, ověřte, že čas na hostiteli je správný.

<a id="-2134364040"></a>**Synchronizace se nezdařila z důvodu vypršení platnosti certifikátu.**  

| | |
|-|-|
| **HRESULT** | 0x80c83078 |
| **HRESULT (desítkové)** | -2134364040 |
| **Text chyby** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **Nutná náprava** | Ano |

K této chybě dochází, protože platnost certifikátu používaného k ověřování vypršela.

Pokud chcete ověřit, jestli platnost certifikátu vypršela, proveďte následující kroky:  
1. Otevřete modul snap-in Certifikáty MMC, vyberte účet počítače a přejděte na certifikáty (místní počítač)\Osobní\Certifikáty.
2. Zkontrolujte, zda vypršela platnost ověřovacího certifikátu klienta.

Pokud platnost certifikátu pro ověřování klientů vypršela, vyřešte problém provedením následujících kroků:

1. Ověřte, zda je nainstalován agent Azure File Sync verze 4.0.1.0 nebo novější.
2. Na serveru spusťte následující příkaz PowerShellu: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**Synchronizace se nezdařila z důvodu, že ověřovací certifikát nebyl nalezen.**  

| | |
|-|-|
| **HRESULT** | 0x80c80228 |
| **HRESULT (desítkové)** | -2134375896 |
| **Text chyby** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **Nutná náprava** | Ano |

K této chybě dochází, protože se nenašel certifikát používaný k ověřování.

Při řešení tohoto problému postupujte následovně:

1. Ověřte, zda je nainstalován agent Azure File Sync verze 4.0.1.0 nebo novější.
2. Na serveru spusťte následující příkaz PowerShellu: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**Synchronizace se nezdařila z důvodu, že nebyla nalezena ověřovací identita.**  

| | |
|-|-|
| **HRESULT** | 0x80c83079 |
| **HRESULT (desítkové)** | -2134364039 |
| **Text chyby** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **Nutná náprava** | Ano |

K této chybě dochází kvůli selhání odstranění koncového bodu serveru, který je teď ve stavu částečného odstranění. Pokud chcete tento problém vyřešit, zkuste koncový bod serveru odstranit znovu.

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**Svazek, ve kterém je umístěn koncový bod serveru, má nedostatek místa na disku.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (desítkové)** | -1906441711 |
| **Text chyby** | JET_errLogDiskFull |
| **Nutná náprava** | Ano |
| | |
| **HRESULT** | 0x80c8031a |
| **HRESULT (desítkové)** | -2134375654 |
| **Text chyby** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Nutná náprava** | Ano |

K této chybě dochází kvůli zaplnění svazku. K této chybě obvykle dochází kvůli tomu, že soubory mimo koncový bod serveru využívají místo na svazku. Uvolněte místo na svazku přidáním dalších koncových bodů serveru, přesunutím souborů na jiný svazek nebo zvětšením velikosti svazku, na který je koncový bod serveru.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**Služba ještě není připravena k synchronizaci s tímto koncovým bodem serveru.**  

| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (desítkové)** | -2134364145 |
| **Text chyby** | ECS_E_REPLICA_NOT_READY |
| **Nutná náprava** | Ne |

K této chybě dochází, protože koncový bod cloudu byl vytvořen s obsahem, který již existuje ve sdílené složce Azure. Azure File Sync musí prohledat sdílenou složku Azure pro veškerý obsah, než povolí koncovému bodu serveru pokračovat v počáteční synchronizaci.

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**Synchronizace se nezdařila kvůli problémům s mnoha jednotlivými soubory.**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (desítkové)** | -2134375877 |
| **Text chyby** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Nutná náprava** | Ano |
| | |
| **HRESULT** | 0x80c8021c |
| **HRESULT (desítkové)** | -2134375908 |
| **Text chyby** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Nutná náprava** | Ano |
| | |
| **HRESULT** | 0x80c80253 |
| **HRESULT (desítkové)** | -2134375853 |
| **Text chyby** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Nutná náprava** | Ano |

V případech, kdy existuje mnoho chyb synchronizace na soubor, může začít selhání synchronizačních relací. <!-- To troubleshoot this state, see [Troubleshooting per file/directory sync errors]().-->

> [!NOTE]
> Azure File Sync vytvoří dočasný snímek VSS jednou denně na serveru pro synchronizaci souborů, které mají otevřené popisovače.

<a id="-2134376423"></a>**Synchronizace se nezdařila z důvodu problému s cestou koncového bodu serveru.**  

| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (desítkové)** | -2134376423 |
| **Text chyby** | ECS_E_SYNC_INVALID_PATH |
| **Nutná náprava** | Ano |

Ujistěte se, že cesta existuje, je na místním svazku NTFS a není bodem pro analýzu nebo existujícím koncovým bodem serveru.

<a id="-2134375817"></a>**Synchronizace se nezdařila, protože verze ovladače filtru není kompatibilní s verzí agenta.**  

| | |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT (desítkové)** | -2134375817 |
| **Text chyby** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Nutná náprava** | Ano |

K této chybě dochází, protože načtená verze ovladače filtru vrstvení cloudu (StorageSync.sys) není kompatibilní se službou agenta synchronizace úložiště (FileSyncSvc). Pokud došlo k upgradu agenta Synchronizace souborů Azure, restartováním serveru dokončete instalaci. Pokud chyba přetrvává, odinstalujte agenta, restartujte server a znovu nainstalujte agenta Synchronizace souborů Azure.

<a id="-2134376373"></a>**Služba je momentálně nedostupná.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (desítkové)** | -2134376373 |
| **Text chyby** | ECS_E_SERVICE_UNAVAILABLE |
| **Nutná náprava** | Ne |

K této chybě dochází kvůli nedostupnosti služby Synchronizace souborů Azure. Tato chyba se automaticky vyřeší, jakmile bude služba Synchronizace souborů Azure opět dostupná.

<a id="-2146233088"></a>**Synchronizace se nezdařila z důvodu výjimky.**  

| | |
|-|-|
| **HRESULT** | 0x80131500 |
| **HRESULT (desítkové)** | -2146233088 |
| **Text chyby** | COR_E_EXCEPTION |
| **Nutná náprava** | Ne |

K této chybě dochází, protože synchronizace selhala kvůli výjimce. Pokud chyba přetrvává několik hodin, vytvořte žádost o podporu.

<a id="-2134364045"></a>**Synchronizace se nezdařila, protože účet úložiště převzal služby převzetí služby do jiné oblasti.**  

| | |
|-|-|
| **HRESULT** | 0x80c83073 |
| **HRESULT (desítkové)** | -2134364045 |
| **Text chyby** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **Nutná náprava** | Ano |

K této chybě dochází, protože došlo k převzetí služeb účtu úložiště při selhání do jiné oblasti. Synchronizace souborů funkci převzetí služeb účtu úložiště při selhání nepodporuje. U účtů úložiště obsahujících sdílené složky Azure, které se v Synchronizaci souborů Azure používají jako koncové body cloudu, by se nemělo provádět převzetí služeb při selhání. Pokud to uděláte, synchronizace přestane fungovat a v případě nově vrstvených souborů může dojít i k neočekávané ztrátě dat. Pokud chcete tento problém vyřešit, přesuňte účet úložiště do primární oblasti.

<a id="-2134375922"></a>**Synchronizace se nezdařila z důvodu přechodného problému s databází synchronizace.**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (desítkové)** | -2134375922 |
| **Text chyby** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Nutná náprava** | Ne |

K této chybě dochází kvůli internímu problému s databází synchronizace. Tato chyba se automaticky vyřeší při opakování synchronizace. Pokud tato chyba trvá delší dobu, vytvořte žádost o podporu a my vás budeme kontaktovat, abychom vám pomohli tento problém vyřešit.

<a id="-2134364024"></a>**Synchronizace se nezdařila z důvodu změny v tenantovi služby Azure Active Directory.**  

| | |
|-|-|
| **HRESULT** | 0x80c83088 |
| **HRESULT (desítkové)** | -2134364024 | 
| **Text chyby** | ECS_E_INVALID_AAD_TENANT |
| **Nutná náprava** | Ano |

K této chybě dochází, protože Synchronizace souborů Azure v současné době nepodporuje přesun předplatného do jiného tenanta Azure Active Directory.
 
Při řešení tohoto problému použijte některou z následujících možností:

- **Možnost 1 (doporučeno):** Přesunutí předplatného zpět do původního klienta služby Azure Active Directory
- **Možnost 2**: Odstraňte a znovu vytvořte aktuální skupinu synchronizace. Pokud na koncovém bodu serveru bylo povolené vrstvení cloudu, odstraňte skupinu synchronizace, provedením kroků popsaných v [části věnované vrstvení cloudu]( https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) odstraňte osamocené vrstvené soubory a teprve pak znovu vytvořte skupiny synchronizace. 

<a id="-2134364010"></a>**Synchronizace se nezdařila z důvodu, že brána firewall a výjimka virtuální sítě nebyla nakonfigurována.**  

| | |
|-|-|
| **HRESULT** | 0x80c83096 |
| **HRESULT (desítkové)** | -2134364010 | 
| **Text chyby** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **Nutná náprava** | Ano |

K této chybě dochází, pokud jsou v účtu úložiště povolena nastavení brány firewall a virtuální sítě a není zaškrtnuta výjimka "Povolit důvěryhodným službám společnosti Microsoft přístup k tomuto účtu úložiště". Pokud chcete tento problém vyřešit, postupujte podle kroků popsaných v průvodci nasazením v části [Konfigurace nastavení brány firewall a virtuální sítě](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings).

<a id="-2147024891"></a>**Synchronizace se nezdařila, protože oprávnění ve složce Informace o systémovém svazku jsou nesprávná.**  

| | |
|-|-|
| **HRESULT** | 0x80070005 |
| **HRESULT (desítkové)** | -2147024891 |
| **Text chyby** | ERROR_ACCESS_DENIED |
| **Nutná náprava** | Ano |

K této chybě může dojít v případě, že účet NT AUTHORITY\SYSTEM nemá oprávnění ke složce s informacemi o systémovém svazku na svazku, na kterém se nachází koncový bod serveru. Všimněte si, že pokud se jednotlivé soubory nedaří synchronizovat s ERROR_ACCESS_DENIED, proveďte kroky zdokumentované v části [Poradce při potížích s chybami synchronizace jednotlivých souborů nebo adresářů.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#troubleshooting-per-filedirectory-sync-errors)

Při řešení tohoto problému postupujte následovně:

1. Stáhněte si nástroj [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec).
2. Spuštění následujícího příkazu z příkazového řádku se zvýšenými oprávněními pro spuštění příkazového řádku pomocí systémového účtu: **PsExec.exe -i -s -d cmd** 
3. Spuštěním následujícího příkazu na příkazovém řádku pod systémovým účtem ověřte, jestli účet NT AUTHORITY\SYSTEM skutečně nemá přístup ke složce s informacemi o systémovém svazku: **cacls "<písmeno_jednotky>:\<složka_s_informacemi_o_systémovém_svazku>" /T /C**
4. Pokud účet NT AUTHORITY\SYSTEM nemá přístup ke složce s informacemi o systémovém svazku, spusťte následující příkaz: **cacls "<písmeno_jednotky>:\<složka_s_informacemi_o_systémovém_svazku>" /T /E /G "NT AUTHORITY\SYSTEM:F"**
    - Pokud krok 4 selže z důvodu odepření přístupu, spuštěním následujícího příkazu převezměte vlastnictví složky s informacemi o systémovém svazku a pak zopakujte krok 4: **takeown /A /R /F "<písmeno_jednotky>:\<složka_s_informacemi_o_systémovém_svazku>"**

<a id="-2134375810"></a>**Synchronizace se nezdařila, protože sdílená složka Azure byla odstraněna a znovu vytvořena.**  

| | |
|-|-|
| **HRESULT** | 0x80c8027e |
| **HRESULT (desítkové)** | -2134375810 |
| **Text chyby** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **Nutná náprava** | Ano |

K této chybě dochází, protože Synchronizace souborů Azure nepodporuje odstranění a opětovné vytvoření sdílené složky Azure ve stejné skupině synchronizace. 

Pokud chcete tento problém vyřešit, provedením následujících kroků odstraňte a znovu vytvořte skupinu synchronizace:

1. Odstraňte všechny koncové body serveru ve skupině synchronizace.
2. Odstraňte koncový bod cloudu. 
3. Odstraňte skupinu synchronizace.
4. Pokud bylo v koncovém bodě serveru povoleno vrstvení cloudu, odstraňte osamocené vrstvené soubory na serveru provedením kroků zdokumentovaných v [vrstvených souborech, které nejsou po odstranění oddílu koncového bodu serveru přístupné na serveru.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)
5. Znovu vytvořte skupinu synchronizace.

<a id="-2145844941"></a>**Synchronizace se nezdařila, protože požadavek HTTP byl přesměrován.**  

| | |
|-|-|
| **HRESULT** | 0x801901333 |
| **HRESULT (desítkové)** | -2145844941 |
| **Text chyby** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **Nutná náprava** | Ano |

K této chybě dochází, protože Azure File Sync nepodporuje přesměrování HTTP (stavový kód 3xx). Chcete-li tento problém vyřešit, zakažte přesměrování HTTP na serveru proxy nebo síťovém zařízení.

<a id="-2134364027"></a>**Během přenosu dat offline došlo k časovému odsávaní, ale stále probíhá.**  

| | |
|-|-|
| **HRESULT** | 0x80c83085 |
| **HRESULT (desítkové)** | -2134364027 |
| **Text chyby** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **Nutná náprava** | Ne |

K této chybě dochází, pokud operace požití dat překročí časový limit. Tato chyba může být ignorována, pokud synchronizace probíhá (AppliedItemCount je větší než 0). Viz [Jak lze sledovat průběh aktuální relace synchronizace?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

### <a name="common-troubleshooting-steps"></a>Běžné kroky řešení potíží
<a id="troubleshoot-storage-account"></a>**Ověřte, zda účet úložiště existuje.**  
# <a name="portal"></a>[Portál](#tab/azure-portal)
1. Přejděte do skupiny synchronizace ve službě Synchronizace úložiště.
2. Vyberte koncový bod cloudu v rámci skupiny synchronizace.
3. Poznamenejte si název sdílené složky Azure v otevřeném podokně.
4. Vyberte propojený účet úložiště. Pokud se tento odkaz nezdaří, byl odebrán odkazovaný účet úložiště.
    ![Snímek obrazovky s podoknem podrobností koncového bodu cloudu s odkazem na účet úložiště.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
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

<a id="troubleshoot-azure-file-share"></a>**Ujistěte se, že existuje sdílená složka Azure.**  
# <a name="portal"></a>[Portál](#tab/azure-portal)
1. Kliknutím na **Přehled** v levém obsahu se vrátíte na hlavní stránku účtu úložiště.
2. Vyberte **Soubory,** chcete-li zobrazit seznam sdílených složek.
3. Ověřte, zda se sdílená složka souboru, na kterou odkazuje koncový bod cloudu, zobrazí v seznamu sdílených složek (měli jste si to poznamenat v kroku 1 výše).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
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
# <a name="portal"></a>[Portál](#tab/azure-portal)
1. V levém obsahu klikněte na **ovládací prvek přístupu (IAM).**
1. Klikněte na kartu **Přiřazení rolí** do seznamu uživatelů a aplikací *(instancí),* kteří mají přístup k vašemu účtu úložiště.
1. Ověřte, zda se v seznamu s rolí **Čtečka a přístup k datům** zobrazí služba **Microsoft.StorageSync** nebo **Hybrid File Sync Service** (starý název aplikace). 

    ![Snímek obrazovky s objektem služby Hybridní synchronizace souborů na kartě řízení přístupu účtu úložiště](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Pokud se služba **Microsoft.StorageSync** nebo **Hybrid File Sync Service** v seznamu nezobrazí, proveďte následující kroky:

    - Klikněte na **Přidat**.
    - V poli **Role** vyberte **Reader a Data Access**.
    - Do pole **Vybrat** zadejte **Microsoft.StorageSync**, vyberte roli a klepněte na tlačítko **Uložit**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Microsoft.StorageSync" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>Jak zabráním uživatelům ve vytváření souborů obsahujících nepodporované znaky na serveru?
[Pomocí blokování souborů Správce prostředků souborového serveru (FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/file-screening-management) můžete blokovat vytváření souborů s nepodporovanými znaky v jejich názvech na serveru. Možná to budete muset provést pomocí prostředí PowerShell, protože většina nepodporovaných znaků není tisknutelná, takže je třeba nejprve přetypovat jejich šestnáctkové reprezentace jako znaky.

Nejprve vytvořte skupinu souborů FSRM pomocí [rutiny New-FsrmFileGroup](https://docs.microsoft.com/powershell/module/fileserverresourcemanager/new-fsrmfilegroup). Tento příklad definuje skupinu tak, aby obsahovala pouze dva nepodporované znaky, ale do skupiny souborů můžete zahrnout tolik znaků, kolik je potřeba.

```powershell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

Po definování skupiny souborů FSRM můžete vytvořit blokování souborů FSRM pomocí rutiny New-FsrmFileScreen.

```powershell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> Všimněte si, že blokování souborů by se mělo používat pouze k blokování vytváření znaků, které není podporováno azure file sync. Pokud se obrazovky souborů používají v jiných scénářích, synchronizace se bude průběžně pokoušet stáhnout soubory ze sdílené složky Azure na server a bude blokována kvůli blokování souborů, což vede k vysokému přenosu dat. 

## <a name="cloud-tiering"></a>Vrstvení cloudu 
Existují dvě cesty pro selhání v cloudvrstving:

- Soubory se může selhat na úroveň, což znamená, že Azure File Sync neúspěšně pokusí vrstvy souboru na soubory Azure.
- Soubory se může selhat na odvolání, což znamená, že filtr systému souborů Synchronizace souborů Azure (StorageSync.sys) se nezdaří stáhnout data, když se uživatel pokusí o přístup k souboru, který byl vrstvený.

Existují dvě hlavní třídy selhání, které se mohou stát buď cestou selhání:

- Selhání cloudového úložiště
    - *Problémy s přechodnou dostupností služby úložiště*. Další informace najdete v tématu [smlouva o úrovni služeb (SLA) pro Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - *Nepřístupná sdílená složka Azure*. K této chybě obvykle dochází, když odstraníte sdílenou složku Azure, když je stále koncový bod cloudu ve skupině synchronizace.
    - *Účet úložiště nepřístupné*. K této chybě obvykle dochází, když odstraníte účet úložiště, zatímco má stále sdílenou složku Azure, což je koncový bod cloudu ve skupině synchronizace. 
- Selhání serveru 
  - *Filtr souborového systému Azure File Sync (StorageSync.sys) není načten*. Aby bylo možné reagovat na požadavky na vrstvení a vyvolání, musí být načten filtr systému souborů Azure File Sync. K nenačtenému filtru může dojít z několika důvodů, ale nejčastějším důvodem je, že jej správce vyložil ručně. Aby azure file sync správce souborů správně fungoval, musí být vždy načten filtr systému souborů Azure File Sync.
  - *Chybějící, poškozený nebo jinak přerušený bod reparse*. Bod reparse je speciální datová struktura v souboru, která se skládá ze dvou částí:
    1. Značka pro analýzu, která operačnímu systému označuje, že filtr systému souborů Azure File Sync (StorageSync.sys) může vyžadovat nějakou akci v idea ve výběru videa. 
    2. Funkce můžete analyzovat data, která označují systému souborů filtrovat identifikátor URI souboru na přidruženém koncovém bodu cloudu (sdílené složce Azure). 
        
       Nejběžnější způsob, jak může dojít k poškození bodu změny zpracování, je, pokud se správce pokusí změnit značku nebo její data. 
  - *Problémy s připojením k síti*. Aby bylo možné vrstvit nebo odvolat soubor, musí mít server připojení k internetu.

Následující části označují, jak řešit problémy s vrstvením cloudu a určit, jestli je problém s cloudovým úložištěm nebo problém se serverem.

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>Jak monitorovat aktivitu vrstvení na serveru  
Chcete-li sledovat aktivitu vrstvení na serveru, použijte id události 9003, 9016 a 9029 v protokolu událostí telemetrie (umístěném v části Aplikace a služby\Microsoft\FileSync\Agent v Prohlížeči událostí).

- ID události 9003 poskytuje distribuci chyb pro koncový bod serveru. Například celkový počet chyb, ErrorCode atd. Všimněte si, že jedna událost je zaznamenána na kód chyby.
- ID události 9016 poskytuje výsledky ghostingu pro svazek. Například, Volné místo procento je, Počet souborů ghosted v relaci, Počet souborů se nezdařilo ghost, atd.
- ID události 9029 poskytuje informace o relaci ghostingu pro koncový bod serveru. Například Počet pokusů o soubory v relaci, Počet souborů vrstvených v relaci, Počet souborů, které již byly vrstveny atd.

### <a name="how-to-monitor-recall-activity-on-a-server"></a>Jak monitorovat aktivitu odvolání na serveru
Chcete-li sledovat aktivitu vyvolání na serveru, použijte id události 9005, 9006, 9009 a 9059 v protokolu událostí telemetrie (umístěném v části Aplikace a služby\Microsoft\FileSync\Agent v Prohlížeči událostí).

- ID události 9005 poskytuje spolehlivost vyvolání pro koncový bod serveru. Například celkový počet unikátních souborů, celkem jedinečných souborů s neúspěšným přístupem atd.
- ID události 9006 poskytuje distribuci chyb y vyvolání pro koncový bod serveru. Například celkový počet neúspěšných požadavků, ErrorCode atd. Všimněte si, že jedna událost je zaznamenána na kód chyby.
- ID události 9009 poskytuje informace o relaci odvolání pro koncový bod serveru. Například DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed, etc.
- ID události 9059 poskytuje distribuci odvolání aplikace pro koncový bod serveru. Například ShareId, Název aplikace a TotalEgressNetworkBytes.

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>Řešení potíží se soubory, které se nepodaří vrstvu
Pokud se souborům nepodaří vrstvení na soubory Azure:

1. V Prohlížeči událostí zkontrolujte telemetrii, protokoly provozních a diagnostických událostí umístěné v části Aplikace a služby\Microsoft\FileSync\Agent. 
   1. Ověřte, zda existují soubory ve sdílené složce Azure.

      > [!NOTE]
      > Soubor musí být synchronizován do sdílené složky Azure před tím, než ho bude možné vrstvené.

   2. Ověřte, zda je server připojen k internetu. 
   3. Ověřte, že jsou spuštěné ovladače filtrů Synchronizace souborů Azure (StorageSync.sys a StorageSyncGuard.sys):
       - Na příkazovém řádku `fltmc`se zvýšenými oprávněními spusťte . Ověřte, zda jsou uvedeny ovladače filtrů systému souborů StorageSync.sys a StorageSyncGuard.sys.

> [!NOTE]
> ID události 9003 je zaznamenána jednou za hodinu v protokolu událostí telemetrie, pokud soubor selže na úroveň (jedna událost je zaznamenána na kód chyby). Zkontrolujte [vrstvení chyby a nápravná](#tiering-errors-and-remediation) část a zjistěte, zda nápravné kroky jsou uvedeny pro kód chyby.

### <a name="tiering-errors-and-remediation"></a>Chyby vrstvení a náprava

| HRESULT | HRESULT (desítkové) | Text chyby | Problém | Odstranění rizika |
|---------|-------------------|--------------|-------|-------------|
| 0x80c86043 | -2134351805 | ECS_E_GHOSTING_FILE_IN_USE | Soubor se nepodařilo vrstvy, protože je používán. | Nevyžaduje se žádná akce. Soubor bude vrstvený, když se již nepoužívá. |
| 0x80c80241 | -2134375871 | ECS_E_GHOSTING_EXCLUDED_BY_SYNC | Soubor se nepodařilo vrstvy, protože je vyloučen a synchronizace. | Nevyžaduje se žádná akce. Soubory v seznamu vyloučení synchronizace nelze vrstvené. |
| 0x80c86042 | -2134351806 | ECS_E_GHOSTING_FILE_NOT_FOUND | Soubor se nezdařil o úroveň, protože nebyl nalezen na serveru. | Nevyžaduje se žádná akce. Pokud chyba přetrvává, zkontrolujte, zda soubor existuje na serveru. |
| 0x80c83053 | -2134364077 | ECS_E_CREATE_SV_FILE_DELETED | Soubor se nepodařilo vrstvy, protože byl odstraněn ve sdílené složce Azure. | Nevyžaduje se žádná akce. Soubor by měl být odstraněn na serveru při spuštění další relace synchronizace stahování. |
| 0x80c8600e | -2134351858 | ECS_E_AZURE_SERVER_BUSY | Soubor se nezdařil o úroveň z důvodu problému se sítí. | Nevyžaduje se žádná akce. Pokud chyba přetrvává, zkontrolujte připojení k síti ke sdílené složce Azure. |
| 0x80072ee7 | -2147012889 | WININET_E_NAME_NOT_RESOLVED | Soubor se nezdařil o úroveň z důvodu problému se sítí. | Nevyžaduje se žádná akce. Pokud chyba přetrvává, zkontrolujte připojení k síti ke sdílené složce Azure. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Souboru se nepodařilo vrstvu z důvodu odepření přístupu chyba. K této chybě může dojít, pokud je soubor umístěn ve složce replikace jen pro čtení dfs-R. | Synchronizace souborů Azure nepodporuje koncové body serveru ve složkách replikace DFS-R jen pro čtení. Další informace naleznete v příručce k [plánování.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs) |
| 0x80072efe | -2147012866 | WININET_E_CONNECTION_ABORTED | Soubor se nezdařil o úroveň z důvodu problému se sítí. | Nevyžaduje se žádná akce. Pokud chyba přetrvává, zkontrolujte připojení k síti ke sdílené složce Azure. |
| 0x80c80261 | -2134375839 | ECS_E_GHOSTING_MIN_FILE_SIZE | Soubor se nepodařilo vrstvy, protože velikost souboru je menší než podporovaná velikost. | Pokud je verze agenta menší než 9.0, minimální podporovaná velikost souboru je 64 kb. Pokud je verze agenta 9.0 a novější, je minimální podporovaná velikost souboru založena na velikosti clusteru systému souborů (dvojitá velikost clusteru systému souborů). Například pokud je velikost clusteru systému souborů 4 kb, minimální velikost souboru je 8 kb. |
| 0x80c83007 | -2134364153 | ECS_E_STORAGE_ERROR | Soubor se nezdařilo vrstvy z důvodu problému úložiště Azure. | Pokud chyba přetrvává, otevřete žádost o podporu. |
| 0x800703e3 | -2147023901 | ERROR_OPERATION_ABORTED | Soubor se nezdařil o úroveň, protože byl současně odvolán. | Nevyžaduje se žádná akce. Soubor bude vrstvený po dokončení odvolání a soubor již není používán. |
| 0x80c80264 | -2134375836 | ECS_E_GHOSTING_FILE_NOT_SYNCED | Soubor se nepodařilo vrstvy, protože není synchronizovándo sdílené složky Azure. | Nevyžaduje se žádná akce. Soubor bude vrstva po synchronizaci do sdílené složky Azure. |
| 0x80070001 | -2147942401 | ERROR_INVALID_FUNCTION | Soubor se nepodařilo vrstvy, protože ovladač filtru vrstvení cloudu (storagesync.sys) není spuštěn. | Chcete-li tento problém vyřešit, otevřete příkazový řádek se zvýšenými oprávněními a spusťte následující příkaz:`fltmc load storagesync`<br>Pokud se ovladač filtru storagesync nenačte při spuštění příkazu fltmc, odinstalujte agenta Azure File Sync, restartujte server a přeinstalujte agenta Synchronizace souborů Azure. |
| 0x8007070 | -2147024784 | ERROR_DISK_FULL | Soubor u množství se nepodařilo vrstvit z důvodu nedostatku místa na disku na svazku, kde je umístěn koncový bod serveru. | Chcete-li tento problém vyřešit, uvolněte alespoň 100 MB místa na disku na svazku, kde je umístěn koncový bod serveru. |
| 0x80070490 | -2147023728 | ERROR_NOT_FOUND | Soubor se nepodařilo vrstvy, protože není synchronizovándo sdílené složky Azure. | Nevyžaduje se žádná akce. Soubor bude vrstva po synchronizaci do sdílené složky Azure. |
| 0x80c80262 | -2134375838 | ECS_E_GHOSTING_UNSUPPORTED_RP | Soubor se nepodařilo vrstvy, protože je nepodporovaný bod reparse. | Pokud je soubor emeduplikační bod odstranění duplicit dat, povolte podporu odstranění duplicitdat podle pokynů pro [plánování.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#data-deduplication) Soubory s body závažnosti než odstranění duplicitdat nejsou podporovány a nebudou vrstvené.  |
| 0x80c83052 | -2134364078 | ECS_E_CREATE_SV_STREAM_ID_MISMATCH | Soubor se nezdařil o úroveň, protože byl změněn. | Nevyžaduje se žádná akce. Soubor bude vrstva po změněný soubor synchronizuje do sdílené složky Azure. |
| 0x80c80269 | -2134375831 | ECS_E_GHOSTING_REPLICA_NOT_FOUND | Soubor se nepodařilo vrstvy, protože není synchronizovándo sdílené složky Azure. | Nevyžaduje se žádná akce. Soubor bude vrstva po synchronizaci do sdílené složky Azure. |
| 0x80072ee2 | -2147012894 | WININET_E_TIMEOUT | Soubor se nezdařil o úroveň z důvodu problému se sítí. | Nevyžaduje se žádná akce. Pokud chyba přetrvává, zkontrolujte připojení k síti ke sdílené složce Azure. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Soubor se nezdařil o úroveň, protože byl změněn. | Nevyžaduje se žádná akce. Soubor bude vrstva po změněný soubor synchronizuje do sdílené složky Azure. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Soubor se nezdařil o úroveň z důvodu nedostatku systémových prostředků. | Pokud chyba přetrvává, zjistěte, která aplikace nebo ovladač režimu jádra odčerpává systémové prostředky. |



### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>Řešení potíží se soubory, které se nepodaří připomenout  
Pokud se soubory nepodaří připomenout:
1. V Prohlížeči událostí zkontrolujte telemetrii, protokoly provozních a diagnostických událostí umístěné v části Aplikace a služby\Microsoft\FileSync\Agent.
    1. Ověřte, zda existují soubory ve sdílené složce Azure.
    2. Ověřte, zda je server připojen k internetu. 
    3. Otevřete modul snap-in Služby MMC a ověřte, zda je spuštěna služba Agent synchronizace úložiště (FileSyncSvc).
    4. Ověřte, že jsou spuštěné ovladače filtrů Synchronizace souborů Azure (StorageSync.sys a StorageSyncGuard.sys):
        - Na příkazovém řádku `fltmc`se zvýšenými oprávněními spusťte . Ověřte, zda jsou uvedeny ovladače filtrů systému souborů StorageSync.sys a StorageSyncGuard.sys.

> [!NOTE]
> ID události 9006 je zaznamenána jednou za hodinu v protokolu událostí telemetrie, pokud soubor nedokáže vyvolat (jedna událost je zaznamenána na kód chyby). Zkontrolujte část [Chyby odvolání a náprava](#recall-errors-and-remediation) a zjistěte, zda jsou pro kód chyby uvedeny nápravné kroky.

### <a name="recall-errors-and-remediation"></a>Odvolat chyby a nápravu

| HRESULT | HRESULT (desítkové) | Text chyby | Problém | Odstranění rizika |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -2147942521 | ERROR_SEM_TIMEOUT | Soubor se nepodařilo odvolat z důvodu časového oběhu vstupně-no. K tomuto problému může dojít z několika důvodů: omezení prostředků serveru, špatné připojení k síti nebo problém úložiště Azure (například omezení). | Nevyžaduje se žádná akce. Pokud chyba trvá několik hodin, otevřete případ podpory. |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | Soubor se nepodařilo odvolat z důvodu problému se sítí.  | Pokud chyba přetrvává, zkontrolujte připojení k síti ke sdílené složce Azure. |
| 0x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | Soubor se nepodařilo odvolat, protože koncový bod serveru byl odstraněn. | Chcete-li tento problém vyřešit, naleznete [v tématu Vrstvené soubory nejsou přístupné na serveru po odstranění koncového bodu serveru](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Soubor u množství nepodařilo vyvolat z důvodu odepření přístupu chyba. K tomuto problému může dojít, pokud jsou povolena brána firewall a nastavení virtuální sítě v účtu úložiště a server nemá přístup k účtu úložiště. | Chcete-li tento problém vyřešit, přidejte adresu IP serveru nebo virtuální síť podle kroků zdokumentovaných v části [Konfigurace brány firewall a nastavení virtuální sítě](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) v průvodci nasazením. |
| 0x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | Soubor se nepodařilo odvolat, protože není přístupný ve sdílené složce Azure. | Chcete-li tento problém vyřešit, ověřte, zda soubor existuje ve sdílené složce Azure. Pokud soubor existuje ve sdílené složce Azure, upgradujte na nejnovější [verzi agenta](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions)Azure File Sync . |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | Soubor se nepodařilo odvolat z důvodu selhání autorizace účtu úložiště. | Chcete-li tento problém vyřešit, ověřte, zda [azure file sync má přístup k účtu úložiště](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#troubleshoot-rbac). |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | Soubor se nepodařilo odvolat, protože sdílená složka Azure není přístupná. | Ověřte, zda sdílená složka existuje a je přístupná. Pokud byla sdílená složka odstraněna a znovu vytvořena, proveďte kroky zdokumentované v [synchronizaci se nezdařily, protože sdílená složka Azure byla odstraněna a znovu vytvořena,](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134375810) aby se odstranila a znovu vytvořila skupina synchronizace. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Soubor se nepodařilo odvolat z důvodu nedostatku systémových prostředků. | Pokud chyba přetrvává, zjistěte, která aplikace nebo ovladač režimu jádra odčerpává systémové prostředky. |
| 0x8007000e | -2147024882 | ERROR_OUTOFMEMORY | Soubor se nepodařilo odvolat z důvodu nedostatku paměti. | Pokud chyba přetrvává, zjistěte, která aplikace nebo ovladač režimu jádra způsobuje stav nedostatku paměti. |
| 0x8007070 | -2147024784 | ERROR_DISK_FULL | Soubor se nepodařilo odvolat z důvodu nedostatku místa na disku. | Chcete-li tento problém vyřešit, uvolněte místo na svazku přesunutím souborů na jiný svazek, zvětšit velikost svazku nebo vynutit soubory na úroveň pomocí rutiny Invoke-StorageSyncCloudTiering. |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>Po odstranění koncového bodu serveru jsou vrstvené soubory na serveru nedostupné
Vrstvené soubory na serveru se stanou nepřístupnými, pokud soubory nejsou odvolány před odstraněním koncového bodu serveru.

Chyby zaznamenané v případě, že vrstvené soubory nejsou přístupné
- Při synchronizaci souboru je do protokolu událostí ItemResults zaznamenán kód chyby -2147942467 (0x80070043 - ERROR_BAD_NET_NAME)
- Při vyvolání souboru je do protokolu událostí RecallResults zaznamenán kód chyby -2134376393 (0x80c80037 - ECS_E_SYNC_SHARE_NOT_FOUND)

Obnovení přístupu k vrstveným souborům je možné při splnění následujících podmínek:
- K odstranění koncového bodu serveru došlo během posledních 30 dnů.
- Koncový bod cloudu se neodstranil. 
- Sdílená složka se neodstranila.
- Skupina synchronizace se neodstranila.

Pokud jsou splněné výše uvedené podmínky, můžete obnovit přístup k souborům na serveru tak, že během 30 dnů znovu vytvoříte koncový bod serveru ve stejné cestě na serveru ve stejné skupině synchronizace. 

Pokud výše uvedené podmínky nejsou splněné, přístup není možné obnovit, protože tyto vrstvené soubory na serveru jsou teď osamocené. Chcete-li odebrat osamocené vrstvené soubory, postupujte podle následujících pokynů.

**Poznámky**
- Když vrstvené soubory nejsou přístupné na serveru, celý soubor by měl být stále přístupné, pokud máte přístup ke sdílené složce Azure přímo.
- Chcete-li v budoucnu zabránit osamocené vrstvené soubory, postupujte podle kroků zdokumentovaných v [části Odebrat koncový bod serveru](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint) při odstraňování koncového bodu serveru.

<a id="get-orphaned"></a>**Jak získat seznam osamocených vrstvených souborů** 

1. Ověřte, zda je nainstalovaná verze agenta synchronizace souborů Azure verze verze verze 5.1 nebo novější.
2. Spusťte následující příkazy Prostředí PowerShell a seznam osamocených vrstvených souborů:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Uložte výstupní soubor OrphanTieredFiles.txt v případě, že je třeba obnovit soubory ze zálohy po jejich odstranění.

<a id="remove-orphaned"></a>**Jak odebrat osamocené vrstvené soubory** 

*Možnost 1: Odstranění osamocených vrstvených souborů*

Tato možnost odstraní osamocené vrstvené soubory na serveru Windows Server, ale vyžaduje odebrání koncového bodu serveru, pokud existuje z důvodu rekreace po 30 dnech nebo je připojen k jiné skupině synchronizace. Ke konfliktům souborů dojde, pokud jsou soubory aktualizovány ve sdílené složce Windows Server nebo Azure před opětovnou vytvořením koncového bodu serveru.

1. Ověřte, zda je nainstalovaná verze agenta synchronizace souborů Azure verze verze verze 5.1 nebo novější.
2. Zálohujte sdílenou složku Azure a umístění koncového bodu serveru.
3. Odeberte koncový bod serveru ve skupině synchronizace (pokud existuje) podle kroků zdokumentovaných v části [Odebrání koncového bodu serveru](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint).

> [!Warning]  
> Pokud koncový bod serveru není odebrán před použitím rutiny Remove-StorageSyncOrphanedTieredFiles, odstranění osamoceného vrstveného souboru na serveru odstraní celý soubor ve sdílené složce Azure. 

4. Spusťte následující příkazy Prostředí PowerShell a seznam osamocených vrstvených souborů:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. Uložte výstupní soubor OrphanTieredFiles.txt v případě, že je třeba obnovit soubory ze zálohy po jejich odstranění.
6. Spusťte následující příkazy Prostředí PowerShell a odstraňte osamocené vrstvené soubory:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**Poznámky** 
- Vrstvené soubory upravené na serveru, které nejsou synchronizovány se sdílenou složkou Azure, se odstraní.
- Vrstvené soubory, které jsou přístupné (ne osamocené), nebudou odstraněny.
- Na serveru zůstanou soubory bez vrstvených vrstev.

7. Volitelné: Znovu vytvořte koncový bod serveru, pokud je odstraněn v kroku 3.

*Možnost 2: Připojení sdílené složky Azure a zkopírování souborů místně, které jsou osamocené na serveru*

Tato možnost nevyžaduje odebrání koncového bodu serveru, ale vyžaduje dostatek místa na disku pro místní kopírování úplných souborů.

1. [Připojte](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) sdílenou složku Azure na Windows Serveru, který má osamocené vrstvené soubory.
2. Spusťte následující příkazy Prostředí PowerShell a seznam osamocených vrstvených souborů:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. K identifikaci osamocených vrstvených souborů na serveru použijte výstupní soubor OrphanTieredFiles.txt.
4. Přepište osamocené vrstvené soubory zkopírováním celého souboru ze sdílené složky Azure do Windows Serveru.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>Řešení potíží se soubory neočekávaně staženy na serveru  
Antivirový program, zálohování a další aplikace, které čtou velké množství souborů, způsobují neúmyslné odvolání, pokud nerespektují atribut přeskočit offline a přeskočit čtení obsahu těchto souborů. U produktů, které tuto možnost podporují, pomáhá přeskočení offline souborů zabránit nežádoucím odvoláním během operací, jako jsou antivirové kontroly nebo úlohy zálohování.

Informace o konfiguraci přeskakování čtení offline souborů v řešení vám sdělí dodavatel příslušného softwaru.

K nechtěnému navrácení může dojít také v jiných scénářích, například při procházení souborů v Průzkumníku souborů. Pokud v Průzkumníku souborů na serveru otevřete složku obsahující soubory vrstvené v cloudu, může to mít za následek nežádoucí odvolání. Pravděpodobnost, že k tomu dojde, ještě zvyšuje povolené antivirové řešení na serveru.

> [!NOTE]
>Pomocí ID události 9059 v protokolu událostí telemetrie určete, které aplikace způsobují odvolání. Tato událost poskytuje distribuci odvolání aplikace pro koncový bod serveru a je zaznamenána jednou za hodinu.

## <a name="general-troubleshooting"></a>Obecné řešení potíží
Pokud narazíte na problémy se synchronizací souborů Azure na serveru, začněte provedením následujících kroků:
1. V Prohlížeči událostí zkontrolujte telemetrii, protokoly provozních a diagnostických událostí.
    - Problémy se synchronizací, vrstvením a navrácením jsou zaznamenány do protokolů telemetrie, diagnostiky a provozních událostí v části Aplikace a služby\Microsoft\FileSync\Agent.
    - Problémy související se správou serveru (například nastavení konfigurace) jsou zaznamenány v protokolech provozních a diagnostických událostí v části Aplikace a služby\Microsoft\FileSync\Management.
2. Ověřte, zda je na serveru spuštěná služba Azure File Sync:
    - Otevřete modul snap-in Služby MMC a ověřte, zda je spuštěna služba Agent synchronizace úložiště (FileSyncSvc).
3. Ověřte, že jsou spuštěné ovladače filtrů Synchronizace souborů Azure (StorageSync.sys a StorageSyncGuard.sys):
    - Na příkazovém řádku `fltmc`se zvýšenými oprávněními spusťte . Ověřte, zda jsou uvedeny ovladače filtrů systému souborů StorageSync.sys a StorageSyncGuard.sys.

Pokud problém není vyřešen, spusťte nástroj AFSDiag:
1. Vytvořte adresář, do kterého bude uložen výstup AFSDiag (například C:\Output).
    > [!NOTE]
    >AFSDiag odstraní veškerý obsah ve výstupním adresáři před shromažďováním protokolů. Zadejte výstupní umístění, které neobsahuje data.
2. Otevřete okno PowerShellu se zvýšenými oprávněními a spusťte následující příkazy (po každém příkazu stiskněte Enter):

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Pro úroveň trasování režimu jádra Azure File Sync zadejte **hodnotu 1** (pokud není uvedeno jinak, chcete-li vytvořit podrobnější trasování) a stiskněte klávesu Enter.
4. Pro úroveň trasování uživatelského režimu Synchronizace souborů Azure zadejte **hodnotu 1** (pokud není uvedeno jinak, chcete-li vytvořit podrobnější trasování) a stiskněte klávesu Enter.
5. Reprodukujte problém. Po dokončení zadejte **D**.
6. Soubor ZIP obsahující protokoly a trasovací soubory je uložen do zadaného výstupního adresáře.

## <a name="see-also"></a>Viz také
- [Sledování služby Synchronizace souborů Azure](storage-sync-files-monitoring.md)
- [Nejčastější dotazy k souborům Azure](storage-files-faq.md)
- [Řešení potíží se službou Azure Files ve Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Poradce při potížích se soubory Azure v Linuxu](storage-troubleshoot-linux-file-connection-problems.md)

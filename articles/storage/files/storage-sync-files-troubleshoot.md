---
title: Řešení potíží s Synchronizace souborů Azure | Microsoft Docs
description: Řešení běžných problémů v nasazení na Synchronizace souborů Azure, které můžete použít k transformaci Windows serveru na rychlou mezipaměť sdílené složky Azure.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 4/12/2021
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: bf74b3a1659547772368c9fb394eeab8321b5f5d
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599634"
---
# <a name="troubleshoot-azure-file-sync"></a>Řešení problémů se Synchronizací souborů Azure
Pomocí Synchronizace souborů Azure můžete centralizovat sdílené složky ve vaší organizaci ve službě soubory Azure a zároveň udržet flexibilitu, výkon a kompatibilitu místního souborového serveru. Synchronizace souborů Azure transformuje Windows Server na rychlou mezipaměť sdílené složky Azure. Pro místní přístup k datům můžete použít jakýkoli protokol dostupný ve Windows Serveru, včetně SMB, NFS a FTPS. Můžete mít tolik mezipamětí, kolik potřebujete po celém světě.

Tento článek je navržený tak, aby pomohl řešit problémy, se kterými se můžete setkat s nasazením Synchronizace souborů Azure. Popisujeme také, jak shromažďovat důležité protokoly ze systému, pokud je potřeba hlubší zkoumání problému. Pokud nevidíte odpověď na svoji otázku, můžete nás kontaktovat prostřednictvím následujících kanálů (v pořadí eskalace):

1. [Microsoft Q&stránku s otázkou pro Azure Storage](/answers/products/azure?product=storage).
2. [Soubory Azure ve službě UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).
3. podpora Microsoftu. Chcete-li vytvořit novou žádost o podporu, v Azure Portal na kartě **help** klikněte na tlačítko **pomoc a podpora** a pak vyberte **Nová žádost o podporu**.

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Mám potíže s Synchronizace souborů Azure na mém serveru (synchronizace, vrstvení cloudu atd.). Mám odebrat a znovu vytvořit koncový bod serveru?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Instalace agenta a registrace serveru
<a id="agent-installation-failures"></a>**Řešení chyb při instalaci agenta**  
Pokud se instalace agenta Synchronizace souborů Azure nezdařila, na příkazovém řádku se zvýšenými oprávněními spusťte následující příkaz, kterým zapnete protokolování při instalaci agenta:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Zkontrolujte instalační program. log a určete příčinu selhání instalace.

<a id="agent-installation-gpo"></a>**Instalace agenta se nezdařila s chybou: Průvodce instalací agenta synchronizace úložiště byl předčasně ukončen z důvodu chyby.**

V protokolu instalace agenta se zaznamená následující chyba:

```
CAQuietExec64:  + CategoryInfo          : SecurityError: (:) , PSSecurityException
CAQuietExec64:  + FullyQualifiedErrorId : UnauthorizedAccess
CAQuietExec64:  Error 0x80070001: Command line returned an error.
```

K tomuto problému dochází, pokud jsou [zásady spouštění prostředí PowerShell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies#use-group-policy-to-manage-execution-policy) konfigurovány pomocí zásad skupiny a nastavení zásad je "povoluje pouze podepsané skripty". Všechny skripty, které jsou součástí agenta Synchronizace souborů Azure, jsou podepsané. Instalace agenta Synchronizace souborů Azure se nezdařila, protože instalační program provádí spuštění skriptu pomocí nastavení zásad pro spuštění bypass.

Pokud chcete tento problém vyřešit, dočasně zakažte nastavení zásad skupiny [zapnout spouštění skriptů](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies#use-group-policy-to-manage-execution-policy) na serveru. Po dokončení instalace agenta je možné nastavení zásad skupiny znovu povolit.

<a id="agent-installation-on-DC"></a>**Instalace agenta na řadiči Doména služby Active Directory se nezdařila.**  
Pokud se pokusíte nainstalovat agenta synchronizace v řadiči domény služby Active Directory, kde je vlastník role primárního řadiče domény v systému Windows Server 2008 R2 nebo pod verzí operačního systému, můžete se setkat s problémem, kdy se agent synchronizace nedaří nainstalovat.

Pokud ho chcete vyřešit, přeneste roli primárního řadiče domény na jiný řadič domény se systémem Windows Server 2012 R2 nebo novějším a pak nainstalujte synchronizaci.

<a id="parameter-is-incorrect"></a>**Přístup ke svazku na Windows Serveru 2012 R2 se nezdařil s chybou: parametr je nesprávný.**  
Po vytvoření koncového bodu serveru v systému Windows Server 2012 R2 dojde k následující chybě při přístupu ke svazku:

Písmeno_jednotky: \ není k dispozici.  
Parametr je nesprávný.

Pokud chcete tento problém vyřešit, nainstalujte [KB2919355](https://support.microsoft.com/help/2919355/windows-rt-8-1-windows-8-1-windows-server-2012-r2-update-april-2014) a restartujte server. Pokud se tato aktualizace nenainstaluje, protože už je nainstalovaná novější aktualizace, otevřete web Windows Update, nainstalujte nejnovější aktualizace pro Windows Server 2012 R2 a restartujte server.

<a id="server-registration-missing-subscriptions"></a>**Registrace serveru nezobrazuje seznam všech předplatných Azure.**  
Při registraci serveru pomocí ServerRegistration.exe po kliknutí na rozevírací seznam předplatné Azure chybí předplatná.

K tomuto problému dochází, protože ServerRegistration.exe načte jenom odběry z prvních 5 tenantů Azure AD. 

Pokud chcete zvýšit limit tenanta registrace serveru na serveru, vytvořte hodnotu DWORD s názvem ServerRegistrationTenantLimit v části HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync s hodnotou větší než 5.

Tento problém můžete také vyřešit pomocí následujících příkazů PowerShellu k registraci serveru:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<guid>" -TenantID "<guid>"
Register-AzureRmStorageSyncServer -SubscriptionId "<guid>" -ResourceGroupName "<string>" -StorageSyncServiceName "<string>"
```

<a id="server-registration-prerequisites"></a>**Při registraci serveru se zobrazí následující zpráva: chybí předpoklady.**  
Tato zpráva se zobrazí, pokud v PowerShellu 5,1 není nainstalovaný modul AZ nebo AzureRM PowerShell. 

> [!Note]  
> ServerRegistration.exe nepodporuje PowerShell 6. x. K registraci serveru můžete použít rutinu Register-AzStorageSyncServer na PowerShellu 6. x.

Pokud chcete nainstalovat modul AZ nebo AzureRM do PowerShellu 5,1, proveďte následující kroky:

1. Do příkazového řádku se zvýšenými oprávněními zadejte **PowerShell** a stiskněte ENTER.
2. Pomocí dokumentace k instalaci nejnovějšího modulu AZ nebo AzureRM postupujte následovně:
    - [AZ Module (vyžaduje .NET 4.7.2)](/powershell/azure/install-az-ps)
    - [Modul AzureRM](https://go.microsoft.com/fwlink/?linkid=856959)
3. Spusťte ServerRegistration.exe a dokončete průvodce k registraci serveru pomocí služby synchronizace úložiště.

<a id="server-already-registered"></a>**Při registraci serveru se zobrazí následující zpráva: "Tento server je již zaregistrován"** 

![Snímek obrazovky dialogu registrace serveru s chybovou zprávou "Server je již zaregistrován"](media/storage-sync-files-troubleshoot/server-registration-1.png)

Tato zpráva se zobrazí, pokud byl server dříve zaregistrován ve službě synchronizace úložiště. Pokud chcete zrušit registraci serveru od aktuální služby synchronizace úložiště a pak zaregistrovat novou službu synchronizace úložiště, proveďte kroky popsané v tématu [zrušení registrace serveru s synchronizace souborů Azure](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Pokud server není uvedený v části **registrované servery** ve službě synchronizace úložiště, spusťte na serveru, který chcete zrušit, následující příkazy PowerShellu:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Pokud je Server součástí clusteru, můžete také odebrat registraci clusteru pomocí volitelného parametru *reset-StorageSyncServer-CleanClusterRegistration* .

<a id="web-site-not-trusted"></a>**Po registraci serveru se zobrazí mnoho odpovědí "Web není důvěryhodný". Proč?**  
K tomuto problému dochází, pokud jsou během registrace serveru povoleny **rozšířené zásady zabezpečení aplikace Internet Explorer** . Další informace o tom, jak správně zakázat **rozšířené zásady zabezpečení Internet Exploreru** , najdete v článku [Příprava Windows serveru pro použití s Synchronizace souborů Azure](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) a [nasazení synchronizace souborů Azure](storage-sync-files-deployment-guide.md).

<a id="server-registration-missing"></a>**Server není uveden v části registrované servery v Azure Portal**  
Pokud server není uvedený v seznamu **registrovaných serverů** pro službu synchronizace úložiště:
1. Přihlaste se k serveru, který chcete zaregistrovat.
2. Otevřete Průzkumníka souborů a pak vyhledejte instalační adresář agenta synchronizace úložiště (výchozí umístění je C:\Program Files\Azure\StorageSyncAgent). 
3. Spusťte ServerRegistration.exe a dokončete průvodce k registraci serveru pomocí služby synchronizace úložiště.

## <a name="sync-group-management"></a>Správa skupin synchronizace

### <a name="cloud-endpoint-creation-errors"></a>Chyby při vytváření koncového bodu cloudu

<a id="cloud-endpoint-using-share"></a>**Vytvoření koncového bodu cloudu se nezdařilo, došlo k této chybě: "Zadaná sdílená složka Azure se již používá v jiném CloudEndpoint".**  
K této chybě dojde v případě, že sdílenou složku Azure již používá jiný koncový bod cloudu. 

Pokud se zobrazí tato zpráva a sdílená složka Azure aktuálně nepoužívá koncový bod cloudu, proveďte následující kroky, aby se ve sdílené složce Azure vymazala Synchronizace souborů Azureová metadata:

> [!Warning]  
> Odstranění metadat ve sdílené složce Azure, kterou aktuálně používá koncový bod cloudu, způsobí selhání Synchronizace souborů Azure operací. Pokud tuto sdílenou složku pak použijete ke synchronizaci v jiné skupině synchronizace, ztráta dat pro soubory ve staré skupině synchronizace je skoro určitá.

1. V Azure Portal přejdete do složky Azure File Share.  
2. Klikněte pravým tlačítkem na sdílenou složku Azure a pak vyberte **Upravit metadata**.
3. Klikněte pravým tlačítkem na **SyncService** a pak vyberte **Odstranit**.

<a id="cloud-endpoint-authfailed"></a>**Vytvoření koncového bodu cloudu se nezdařilo, došlo k této chybě: "AuthorizationFailed"**  
K této chybě dochází, pokud váš uživatelský účet nemá dostatečná oprávnění k vytvoření koncového bodu cloudu. 

Pokud chcete vytvořit koncový bod cloudu, musí mít váš uživatelský účet následující autorizační oprávnění Microsoftu:  
* Přečtěte si: získání definice role
* Zápis: vytvoření nebo aktualizace definice vlastní role
* Číst: získat přiřazení role
* Zápis: vytvořit přiřazení role

Následující předdefinované role mají požadovaná autorizační oprávnění od Microsoftu:  
* Vlastník
* Správce uživatelských přístupů

Pokud chcete zjistit, jestli má role uživatelského účtu požadovaná oprávnění, postupujte takto:  
1. V Azure Portal vyberte **skupiny prostředků**.
2. Vyberte skupinu prostředků, ve které se nachází účet úložiště, a pak vyberte **řízení přístupu (IAM)**.
3. Vyberte kartu **přiřazení rolí** .
4. Pro svůj uživatelský účet vyberte **roli** (například vlastníka nebo přispěvatele).
5. V seznamu **poskytovatel prostředků** vyberte **autorizaci Microsoft**. 
    * **Přiřazení role** by mělo mít oprávnění **ke čtení** a **zápisu** .
    * **Definice role** musí mít oprávnění **ke čtení** a **zápisu** .

### <a name="server-endpoint-creation-and-deletion-errors"></a>Chyby vytvoření a odstranění koncového bodu serveru

<a id="-2134375898"></a>**Vytvoření koncového bodu serveru se nezdařilo, došlo k této chybě: "MgmtServerJobFailed" (kód chyby:-2134375898 nebo 0x80c80226)**  
K této chybě dochází v případě, že je cesta ke koncovému bodu serveru na systémovém svazku a je povolené vrstvení cloudu. Vrstvení cloudu se na systémovém svazku nepodporuje. Pokud chcete vytvořit koncový bod serveru na systémovém svazku, při vytváření koncového bodu serveru zakažte vrstvení cloudu.

<a id="-2147024894"></a>**Vytvoření koncového bodu serveru se nezdařilo, došlo k této chybě: "MgmtServerJobFailed" (kód chyby:-2147024894 nebo 0x80070002)**  
K této chybě dochází v případě, že zadaná cesta ke koncovému bodu serveru není platná. Ověřte, že zadaná cesta ke koncovému bodu serveru odpovídá místně připojenému svazku NTFS. Poznámka: Synchronizace souborů Azure jako cestu ke koncovému bodu serveru nepodporuje mapované jednotky.

<a id="-2134375640"></a>**Vytvoření koncového bodu serveru se nezdařilo, došlo k této chybě: "MgmtServerJobFailed" (kód chyby:-2134375640 nebo 0x80c80328)**  
K této chybě dochází v případě, že zadaná cesta ke koncovému bodu serveru neodpovídá svazku NTFS. Ověřte, že zadaná cesta ke koncovému bodu serveru odpovídá místně připojenému svazku NTFS. Poznámka: Synchronizace souborů Azure jako cestu ke koncovému bodu serveru nepodporuje mapované jednotky.

<a id="-2134347507"></a>**Vytvoření koncového bodu serveru se nezdařilo, došlo k této chybě: "MgmtServerJobFailed" (kód chyby:-2134347507 nebo 0x80c8710d)**  
K této chybě dochází, protože Synchronizace souborů Azure nepodporuje koncové body serveru na svazcích, které obsahují komprimovanou složku s informacemi o systémovém svazku. Pokud chcete tento problém vyřešit, dekomprimujte složku s informacemi o systémovém svazku. Pokud je složka s informacemi o systémovém svazku jedinou komprimovanou složkou na svazku, postupujte následovně:

1. Stáhněte si nástroj [PsExec](/sysinternals/downloads/psexec) .
2. Spuštěním následujícího příkazu z příkazového řádku se zvýšenými oprávněními spusťte příkazový řádek spuštěný pod účtem System: **PsExec.exe-i-s-d cmd** .
3. Na příkazovém řádku spuštěném pod systémovým účtem zadejte následující příkazy a stiskněte Enter:   
    **CD/d "písmeno jednotky: \ systémový svazek informace"**  
    **Compact/u/s**

<a id="-2134376345"></a>**Vytvoření koncového bodu serveru se nezdařilo, došlo k této chybě: "MgmtServerJobFailed" (kód chyby:-2134376345 nebo 0x80C80067)**  
K této chybě dochází v případě, že dojde k dosažení limitu koncových bodů serverů na server. Synchronizace souborů Azure v současné době podporuje až 30 koncových bodů serveru na server. Další informace najdete v tématu [synchronizace souborů Azure cíle škálování](./storage-files-scale-targets.md#azure-file-sync-scale-targets).

<a id="-2134376427"></a>**Vytvoření koncového bodu serveru se nezdařilo, došlo k této chybě: "MgmtServerJobFailed" (kód chyby:-2134376427 nebo 0x80c80015)**  
K této chybě dochází v případě, že se v zadané cestě ke koncovému bodu serveru již synchronizuje jiný koncový bod serveru. Synchronizace souborů Azure nepodporuje synchronizaci stejného adresáře nebo svazku několika koncovými body serveru.

<a id="-2160590967"></a>**Vytvoření koncového bodu serveru se nezdařilo, došlo k této chybě: "MgmtServerJobFailed" (kód chyby:-2160590967 nebo 0x80c80077)**  
K této chybě dochází v případě, že cesta ke koncovému bodu serveru obsahuje osamocené vrstvené soubory. Pokud v nedávné době došlo k odebrání koncového bodu serveru, počkejte na dokončení vymazání osamocených vrstvených souborů. Jakmile se spustí čištění osamocených vrstvených souborů, do protokolu událostí telemetrie se zaprotokoluje událost s ID 6662. Po dokončení čištění osamocených vrstvených souborů se protokoluje událost s ID 6661 a koncový bod serveru se dá znovu vytvořit pomocí cesty. Pokud se vytvoření koncového bodu serveru nepovede, když se dokončí čištění vrstvených souborů, nebo pokud se v protokolu událostí telemetrie nenalezne událost s ID 6661, protože došlo k přechodu do protokolu událostí, odeberte osamocené vrstvené soubory provedením kroků popsaných v [vrstvených souborech na serveru po odstranění oddílu koncového bodu serveru](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) .

<a id="-2134347757"></a>**Odstranění koncového bodu serveru se nezdařilo, došlo k této chybě: "MgmtServerJobExpired" (kód chyby:-2134347757 nebo 0x80c87013)**  
K této chybě dojde v případě, že je server offline nebo nemá připojení k síti. Pokud server už není k dispozici, zrušte registraci serveru na portálu. Tím se odstraní koncové body serveru. Pokud chcete odstranit koncové body serveru, postupujte podle kroků popsaných v tématu [zrušení registrace serveru pomocí synchronizace souborů Azure](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

### <a name="server-endpoint-health"></a>Stav koncového bodu serveru

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

K tomuto problému může dojít, pokud proces monitorování synchronizace úložiště (AzureStorageSyncMonitor.exe) není spuštěný nebo server nemá přístup ke službě Synchronizace souborů Azure.

Na serveru, na kterém se zobrazuje stav "je v režimu offline" na portálu, se podívejte na událost s ID 9301 v protokolu událostí telemetrie (nacházející se v části aplikace a Services\Microsoft\FileSync\Agent v Prohlížeč událostí), abyste zjistili, proč Server nemůže získat přístup ke službě Synchronizace souborů Azure. 

- Pokud je **GetNextJob dokončeno se stavem "0"** , server může komunikovat se službou synchronizace souborů Azure. 
    - Otevřete na serveru Správce úloh a ověřte, že je spuštěný proces monitorování synchronizace úložiště (AzureStorageSyncMonitor.exe). Pokud tento proces není spuštěný, zkuste nejprve server restartovat. Pokud restartování serveru problém nevyřeší, upgradujte na nejnovější [verzi agenta](./storage-files-release-notes.md) Synchronizace souborů Azure. 

- Pokud byl **dokončen GetNextJob se stavem:-2134347756** , server nebude schopen komunikovat se službou synchronizace souborů Azure z důvodu konfigurace objednávky brány firewall, proxy serveru nebo TLS. 
    - Pokud je server za bránou firewall, ověřte, že je povolený odchozí port 443. Pokud brána firewall omezuje provoz do konkrétních domén, zkontrolujte, že jsou dostupné domény uvedené v [dokumentaci](./storage-sync-files-firewall-and-proxy.md#firewall) k bráně firewall.
    - Pokud je server za proxy, nakonfigurujte nastavení proxy serveru na úrovni počítače nebo aplikace podle kroků v [dokumentaci k](./storage-sync-files-firewall-and-proxy.md#proxy)proxy serveru.
    - Pomocí rutiny Test-StorageSyncNetworkConnectivity ověřte síťové připojení k koncovým bodům služby. Další informace najdete v tématu [Test připojení k síti koncových bodů služby](./storage-sync-files-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints).
    - Pokud je na serveru nakonfigurovaná objednávka šifrovací sada TLS, můžete k přidání šifrovacích sad použít zásady skupiny nebo rutiny TLS:
        - Pokud chcete použít zásady skupiny, přečtěte si téma [Konfigurace pořadí šifrovacích sad TLS pomocí Zásady skupiny](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-group-policy).
        - Pokud chcete používat rutiny protokolu TLS, přečtěte si téma [Konfigurace pořadí šifrovacích sad TLS pomocí rutin prostředí PowerShell pro TLS](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-tls-powershell-cmdlets).
    
        Synchronizace souborů Azure aktuálně podporuje následující šifrovací sady pro protokol TLS 1,2:  
        - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
        - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
        - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
        - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256  

- Pokud byl **dokončen GetNextJob se stavem:-2134347764** , server nebude schopen komunikovat se službou synchronizace souborů Azure z důvodu vypršení platnosti nebo odstranění certifikátu.  
    - Spusťte na serveru následující příkaz PowerShellu pro resetování certifikátu použitého pro ověřování:
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**Koncový bod serveru má stav bez aktivity a stav serveru v okně registrované servery je online.**  

Stav „Žádná aktivita“ u koncového bodu serveru znamená, že koncový bod serveru neprotokoloval za poslední dvě hodiny aktivitu synchronizace.

Postup kontroly aktuální aktivity synchronizace na serveru naleznete v tématu [návody monitorovat průběh aktuální relace synchronizace?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

Koncový bod serveru nemůže v důsledku chyby nebo nedostatku systémových prostředků protokolovat aktivitu synchronizace několik hodin. Ověřte, že je nainstalovaná nejnovější [verze agenta](./storage-files-release-notes.md) synchronizace souborů Azure. Pokud se problém opakuje, otevřete žádost o podporu.

> [!Note]  
> Pokud je stav serveru v okně registrované servery "se zobrazuje v režimu offline", proveďte kroky popsané v části [koncový bod serveru s stavem "bez aktivity" nebo "čeká" a stav serveru v okně registrované servery je "zobrazeno offline"](#server-endpoint-noactivity) .

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**Pokud jsem soubor vytvořil přímo ve sdílené složce Azure přes protokol SMB nebo prostřednictvím portálu, jak dlouho trvá, než se soubor synchronizuje se servery ve skupině synchronizace?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**Stav koncového bodu serveru je ve stavu čekání na několik hodin.**  
Tento problém se očekává, když vytvoříte koncový bod cloudu a použijete sdílenou složku Azure, která obsahuje data. Aby bylo možné synchronizovat soubory mezi koncovými body cloudu a serveru, je potřeba provést úlohu výčtu změn, která kontroluje změny ve sdílené složce Azure. Čas k dokončení úlohy závisí na velikosti oboru názvů ve sdílené složce Azure. Stav koncového bodu serveru by se měl aktualizovat, jakmile se dokončí úloha změny výčtu.

### <a name="how-do-i-monitor-sync-health"></a><a id="broken-sync"></a>Návody monitorovat stav synchronizace?
# <a name="portal"></a>[Azure Portal](#tab/portal1)
V rámci každé skupiny synchronizace můžete přejít k podrobnostem svého jednotlivého koncového bodu serveru a zobrazit stav posledních dokončených relací synchronizace. Zelený sloupec stavu a nesynchronizovaná hodnota 0 značí, že synchronizace pracuje podle očekávání. Pokud se nejedná o tento případ, níže naleznete seznam běžných chyb synchronizace a postup zpracování nesynchronizovaných souborů. 

![Snímek obrazovky Azure Portal](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="server"></a>[Server](#tab/server)
Přejít na protokoly telemetrie serveru, které najdete v Prohlížeč událostí na `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` . Událost 9102 odpovídá dokončené relaci synchronizace; v případě nejnovějšího stavu synchronizace vyhledejte nejnovější událost s ID 9102. SyncDirection zobrazí informace o tom, jestli se jednalo o nahrání nebo stažení. Pokud je HResult 0, relace synchronizace byla úspěšná. Nenulová hodnota HResult znamená, že během synchronizace došlo k chybě; seznam běžných chyb najdete níže. Pokud je PerItemErrorCount větší než 0, znamená to, že některé soubory nebo složky se nesynchronizoval správně. Je možné mít hodnotu HResult 0, ale PerItemErrorCount, která je větší než 0.

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

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Jak monitorovat průběh aktuální relace synchronizace?
# <a name="portal"></a>[Azure Portal](#tab/portal1)
V rámci skupiny synchronizace přejděte na příslušný koncový bod serveru a podívejte se na část aktivita synchronizace a zobrazte počet souborů, které se nahrály nebo stáhly v aktuální relaci synchronizace. Tento stav bude opožděn přibližně o 5 minut. Pokud je vaše relace synchronizace natolik krátká, že se během tohoto intervalu dokončí, nemusí být na portálu uvedena. 

# <a name="server"></a>[Server](#tab/server)
Podívejte se na nejnovější událost 9302 v protokolu telemetrie na serveru (v Prohlížeč událostí, přejít do části aplikace a služby Logs\Microsoft\FileSync\Agent\Telemetry). Tato událost udává stav aktuální relace synchronizace. TotalItemCount označuje, kolik souborů se má synchronizovat, AppliedItemCount počet souborů, které byly doposud synchronizovány, a PerItemErrorCount počet souborů, které se nedaří synchronizovat (Další informace najdete v tématu jak s tím pracovat).

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
# <a name="portal"></a>[Azure Portal](#tab/portal1)
Pro každý server v dané skupině synchronizace se ujistěte, že:
- Časová razítka posledního pokusu o synchronizaci pro nahrávání a stahování jsou poslední.
- Stav je zelený pro nahrávání i stahování.
- V poli aktivita synchronizace se zobrazuje velmi málo nebo žádné soubory, které se mají synchronizovat.
- Pole nesynchronizované soubory je 0 pro nahrávání i stahování.

# <a name="server"></a>[Server](#tab/server)
Podívejte se na dokončené relace synchronizace, které jsou označené událostmi 9102 v protokolu událostí telemetrie pro každý server (v Prohlížeč událostí, přejít na `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` ). 

1. Na jakémkoli daném serveru se chcete ujistit, že se úspěšně odeslaly nejnovější relace nahrávání a stahování. Provedete to tak, že zkontrolujete, jestli je hodnota HResult a PerItemErrorCount 0 pro nahrávání i stahování (pole SyncDirection označuje, jestli je daná relace relace nahrávání nebo stahování). Všimněte si, že Pokud nevidíte nedávno dokončenou relaci synchronizace, je pravděpodobně relace synchronizace právě probíhající, což se očekává, pokud jste právě přidali nebo upravili velké množství dat.
2. Pokud je server plně aktuální s cloudem a nemá žádné změny k synchronizaci v obou směrech, zobrazí se prázdné relace synchronizace. Ty jsou označeny odesláním a stažením událostí, ve kterých jsou všechna pole Sync * (SyncFileCount, SyncDirCount, SyncTombstoneCount a SyncSizeBytes) nulová, což znamená, že nedošlo k žádné synchronizaci. Pamatujte na to, že tyto prázdné relace synchronizace se nemusí nacházet na serverech s vysokou mírou změny, protože k synchronizaci je vždycky něco nového. Pokud neexistují žádné aktivity synchronizace, měly by se vyskytnout každých 30 minut. 
3. Pokud jsou všechny servery aktuální s cloudem, což znamená, že nedávné relace nahrávání a stahování jsou prázdné relace synchronizace, můžete vyslovit s rozumnou jistotou, že systém jako celek je synchronizovaný. 
    
Všimněte si, že pokud jste provedli změny přímo ve sdílené složce Azure, Synchronizace souborů Azure tuto změnu nezjistí, dokud se nezmění spuštění výčtu, k němuž dojde každých 24 hodin. Je možné, že server bude s cloudem aktuální a v případě, že ve skutečnosti chybí nedávné změny provedené přímo ve sdílené složce Azure. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Jak zjistím, jestli se některé konkrétní soubory nebo složky nesynchronizují?
Pokud vaše PerItemErrorCount na serveru nebo soubory, které nejsou synchronizované na portálu, jsou pro danou relaci synchronizace větší než 0, znamená to, že některé položky selžou synchronizaci. Soubory a složky mohou mít vlastnosti, které brání jejich synchronizaci. Tyto vlastnosti mohou být trvalé a vyžadují explicitní akci pro pokračování synchronizace, například odebrání nepodporovaných znaků ze souboru nebo složky. Mohou být také přechodné, což znamená, že soubor nebo složka budou automaticky pokračovat v synchronizaci. například soubory s otevřenými popisovači budou po zavření souboru automaticky pokračovat v synchronizaci. Když modul Synchronizace souborů Azure zjistí takový problém, vytvoří se protokol chyb, který se dá analyzovat tak, aby se vygenerovalo seznam položek, které se v tuto chvíli nesynchronizují správně.

Pokud se chcete podívat na tyto chyby, spusťte skript **FileSyncErrorsReport.ps1** PowerShell (umístěný v instalačním adresáři agenta agenta synchronizace souborů Azure) a identifikujte soubory, které se nepodařilo synchronizovat kvůli otevřeným popisovačům, nepodporovaným znakům nebo jiným problémům. Pole položka Itempath oznamuje umístění souboru ve vztahu k adresáři kořenové synchronizace. Projděte si seznam běžných chyb synchronizace níže pro nápravné kroky.

> [!Note]  
> Pokud skript FileSyncErrorsReport.ps1 vrátí "nenašly se žádné chyby v souboru" nebo neobsahují chyby pro skupinu synchronizace, je příčina buď:
>
>- Příčina 1: poslední dokončená relace synchronizace neobsahovala chyby jednotlivých položek. Portál se brzy aktualizuje, aby se zobrazily 0 souborů, které se nesynchronizují. 
>    - Zkontrolujte [ID události 9102](?tabs=server%252cazure-portal#broken-sync) v protokolu událostí telemetrie a potvrďte, že PerItemErrorCount je 0. 
>
>- Příčina 2: protokol událostí ItemResults serveru byl zabalen z důvodu příliš velkého počtu chyb na jednu položku a protokol událostí již neobsahuje chyby pro tuto skupinu synchronizace.
>    - Chcete-li tomuto problému zabránit, zvyšte velikost protokolu událostí ItemResults. Protokol událostí ItemResults najdete v části "Applications and Services Logs\Microsoft\FileSync\Agent" v tématu Prohlížeč událostí. 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Řešení chyb při synchronizaci souborů nebo adresářů
**ItemResults chyby synchronizace protokolu pro jednotlivé položky**  

| HRESULT | HRESULT (desetinné číslo) | Text chyby | Problém | Náprava |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | Vrstvený soubor na serveru není dostupný. K tomuto problému dojde v případě, že se před odstraněním koncového bodu serveru neodvolal vrstvený soubor. | Pokud chcete tento problém vyřešit, najdete informace v části [vrstvené soubory nejsou po odstranění koncového bodu serveru dostupné na serveru](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | Změnu souboru nebo adresáře nelze ještě synchronizovat, protože není ještě synchronizovaná závislá složka. Tato položka bude synchronizována po synchronizaci závislých změn. | Nevyžaduje se žádná akce. Pokud chyba trvá několik dní, použijte skript FileSyncErrorsReport.ps1 PowerShell k určení, proč se závislá složka ještě nesynchronizoval. |
| 0x80C8028A | -2134375798 | ECS_E_SYNC_CONSTRAINT_CONFLICT_ON_FAILED_DEPENDEE | Změnu souboru nebo adresáře nelze ještě synchronizovat, protože není ještě synchronizovaná závislá složka. Tato položka bude synchronizována po synchronizaci závislých změn. | Nevyžaduje se žádná akce. Pokud chyba trvá několik dní, použijte skript FileSyncErrorsReport.ps1 PowerShell k určení, proč se závislá složka ještě nesynchronizoval. |
| 0x80c80284 | -2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | Změnu souboru nebo adresáře nelze ještě synchronizovat, protože závislá složka ještě není synchronizovaná a relace synchronizace selhala. Tato položka bude synchronizována po synchronizaci závislých změn. | Nevyžaduje se žádná akce. Pokud chyba přetrvává, prozkoumejte selhání relace synchronizace. |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | Název souboru nebo adresáře je neplatný. | Přejmenujte daný soubor nebo adresář. Další informace najdete v tématu [zpracování nepodporovaných znaků](?tabs=portal1%252cazure-portal#handling-unsupported-characters) . |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | Název souboru nebo adresáře je neplatný. | Přejmenujte daný soubor nebo adresář. Další informace najdete v tématu [zpracování nepodporovaných znaků](?tabs=portal1%252cazure-portal#handling-unsupported-characters) . |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | Soubor nelze synchronizovat, protože se používá. Soubor se bude synchronizovat, jakmile se už nebude používat. | Nevyžaduje se žádná akce. Synchronizace souborů Azure vytvoří dočasný snímek VSS jednou denně na serveru pro synchronizaci souborů s otevřenými popisovači. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | Soubor se změnil, ale tato změna se ještě nezjistila synchronizací. Po zjištění této změny se synchronizace obnoví. | Nevyžaduje se žádná akce. |
| chyby | -2147024894 | ERROR_FILE_NOT_FOUND | Soubor se odstranil a synchronizace o této změně nevíte. | Nevyžaduje se žádná akce. Synchronizace ukončí protokolování této chyby, jakmile zjišťování změn zjistí, že soubor byl odstraněn. |
| 0x80070003 | -2147942403 | ERROR_PATH_NOT_FOUND | Odstranění souboru nebo adresáře nelze synchronizovat, protože položka již byla odstraněna v cíli a synchronizace neví o změně. | Nevyžaduje se žádná akce. Při synchronizaci se tato chyba zastaví, jakmile se v cíli spustí zjišťování změn a v synchronizaci zjistíte, že se položka odstranila. |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | Soubor nebo adresář se přeskočil, ale během příští relace synchronizace se synchronizuje. Pokud je tato chyba hlášena při stahování položky, název souboru nebo adresáře je více než nejspíš neplatných. | Pokud je při nahrávání souboru nahlášena Tato chyba, není vyžadována žádná akce. Pokud při stahování souboru dojde k chybě, přejmenujte příslušný soubor nebo adresář. Další informace najdete v tématu [zpracování nepodporovaných znaků](?tabs=portal1%252cazure-portal#handling-unsupported-characters) . |
| 0x800700B7 | -2147024713 | ERROR_ALREADY_EXISTS | Vytvoření souboru nebo adresáře nelze synchronizovat, protože položka již v cíli existuje a synchronizace tyto změny neví. | Nevyžaduje se žádná akce. Synchronizace přestane tuto chybu protokolovat, jakmile v cíli proběhne detekce změn a synchronizace získá informace o této nové položce. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Soubor není možné synchronizovat, protože došlo k dosažení limitu sdílené složky Azure. | Pokud chcete tento problém vyřešit, přečtěte si část s [omezením úložiště sdílené složky Azure](?tabs=portal1%252cazure-portal#-2134351810) v Průvodci odstraňováním potíží. |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | Soubor je zašifrovaný pomocí nepodporovaného řešení (třeba NTFS EFS). | Dešifrujte soubor a použijte podporované řešení šifrování. Seznam podporovaných řešení najdete v průvodci plánováním v části [Šifrovací řešení](./storage-sync-files-planning.md#encryption). |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | Soubor se nachází ve složce replikace jen pro čtení DFS-R. | Soubor se nachází ve složce replikace jen pro čtení DFS-R. Synchronizace souborů Azure nepodporuje koncové body serveru ve složkách replikace DFS-R jen pro čtení. Další informace najdete v [průvodci plánováním](./storage-sync-files-planning.md#distributed-file-system-dfs). |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Soubor je ve stavu čekání na odstranění. | Nevyžaduje se žádná akce. Až se všechny otevřené popisovače souborů zavřou, soubor se odstraní. |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | Soubor nejde synchronizovat, protože je povolené nastavení brány firewall a virtuální sítě v účtu úložiště a server nemá přístup k účtu úložiště. | Pomocí postupu popsaného v části [Konfigurace brány firewall a nastavení virtuální sítě](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) v Průvodci nasazením přidejte IP adresu serveru nebo virtuální síť. |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | Soubor nelze synchronizovat, protože velikost popisovače zabezpečení překračuje limit 64 KiB. | Pokud chcete tento problém vyřešit, odeberte položky řízení přístupu (ACE) pro soubor, abyste snížili velikost popisovače zabezpečení. |
| 0x8000ffff | -2147418113 | E_UNEXPECTED | Soubor nelze synchronizovat z důvodu neočekávané chyby. | Pokud chyba trvá několik dní, otevřete prosím případ podpory. |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | Soubor nelze synchronizovat, protože se používá. Soubor se bude synchronizovat, jakmile se už nebude používat. | Nevyžaduje se žádná akce. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Soubor se během synchronizace změnil, takže je potřeba ho synchronizovat znovu. | Nevyžaduje se žádná akce. |
| 0x80070017 | -2147024873 | ERROR_CRC | Soubor nelze synchronizovat z důvodu chyby CRC. K této chybě může dojít, pokud se vrstvený soubor před odstraněním koncového bodu serveru nevrátil, nebo pokud je soubor poškozený. | Pokud chcete tento problém vyřešit, přečtěte si téma [soubory vrstvených souborů po odstranění koncového bodu serveru](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) a odebrání vrstvených souborů, které jsou osamocené. Pokud k chybě dochází i po odebrání osamocených vrstvených souborů, spusťte na svazku [Nástroj Chkdsk](/windows-server/administration/windows-commands/chkdsk) . |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | Soubor nelze synchronizovat, protože byl dosažen maximální počet souborů konfliktů. Synchronizace souborů Azure podporuje soubory konfliktů 100 na jeden soubor. Další informace o konfliktech souborů najdete v tématu Synchronizace souborů Azure [Nejčastější dotazy](./storage-files-faq.md#afs-conflict-resolution). | Pokud chcete tento problém vyřešit, snižte počet konfliktních souborů. Soubor se bude synchronizovat, jakmile počet konfliktních souborů klesne pod 100. |

#### <a name="handling-unsupported-characters"></a>Zpracování nepodporovaných znaků
Pokud skript **FileSyncErrorsReport.ps1** PowerShellu v důsledku nepodporovaných znaků (kód chyby 0x8007007B nebo 0x80c80255) zobrazí chyby synchronizace jednotlivých položek, měli byste odebrat nebo přejmenovat znaky při chybě z příslušných názvů souborů. PowerShell pravděpodobně vytiskne tyto znaky jako otazníky nebo prázdné obdélníky, protože většina těchto znaků nemá standardní vizuální kódování. 
> [!Note]  
> [Nástroj pro vyhodnocení](storage-sync-files-planning.md#evaluation-cmdlet) lze použít k identifikaci nepodporovaných znaků. Pokud vaše datová sada obsahuje několik souborů s neplatnými znaky, použijte skript [ScanUnsupportedChars](https://github.com/Azure-Samples/azure-files-samples/tree/master/ScanUnsupportedChars) k přejmenování souborů obsahujících nepodporované znaky.

Následující tabulka obsahuje všechny znaky Unicode, které Synchronizace souborů Azure ještě nepodporují.

| Znaková sada | Počet znaků |
|---------------|-----------------|
| <ul><li>0x0000009D (příkaz pro operační systém OSC)</li><li>0x00000090 (řetězec řízení zařízení v řadičích domény)</li><li>0x0000008F (SS3 Single Shift)</li><li>0x00000081 (přednastavení s vysokým oktetem)</li><li>0x0000007F (del Delete)</li><li>0x0000008D (rezervovaný zpětný spojnicový kanál)</li></ul> | 6 |
| 0x0000FDD0-0x0000FDEF (Arabské prezentační Forms-a) | 32 |
| 0x0000FFF0-0x0000FFFF (speciální) | 16 |
| <ul><li>0x0001FFFE-0x0001FFFF = 2 (neznak)</li><li>0x0002FFFE-0x0002FFFF = 2 (neznak)</li><li>0x0003FFFE-0x0003FFFF = 2 (neznak)</li><li>0x0004FFFE-0x0004FFFF = 2 (neznak)</li><li>0x0005FFFE-0x0005FFFF = 2 (neznak)</li><li>0x0006FFFE-0x0006FFFF = 2 (neznak)</li><li>0x0007FFFE-0x0007FFFF = 2 (neznak)</li><li>0x0008FFFE-0x0008FFFF = 2 (neznak)</li><li>0x0009FFFE-0x0009FFFF = 2 (neznak)</li><li>0x000AFFFE-0x000AFFFF = 2 (neznak)</li><li>0x000BFFFE-0x000BFFFF = 2 (neznak)</li><li>0x000CFFFE-0x000CFFFF = 2 (neznak)</li><li>0x000DFFFE-0x000DFFFF = 2 (neznak)</li><li>0x000EFFFE-0x000EFFFF = 2 (Nedefinováno)</li><li>0x000FFFFE-0x000FFFFF = 2 (další privátní oblast použití)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Běžné chyby synchronizace
<a id="-2147023673"></a>**Relace synchronizace byla zrušena.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (desetinné číslo)** | -2147023673 | 
| **Text chyby** | ERROR_CANCELLED |
| **Požadována náprava** | No |

Relace synchronizace mohou selhat z různých důvodů, včetně restartování nebo aktualizace serveru, snímků služby VSS atd. I když se tato chyba zdá, že vyžaduje následnou instalaci, je bezpečné tuto chybu ignorovat, pokud netrvá v průběhu několika hodin.

<a id="-2147012889"></a>**Nebylo možné navázat spojení se službou.**    

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80072EE7 |
| **HRESULT (desetinné číslo)** | -2147012889 | 
| **Text chyby** | WININET_E_NAME_NOT_RESOLVED |
| **Požadována náprava** | Yes |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**Služba omezila požadavek na uživatele.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (desetinné číslo)** | -2134376372 |
| **Text chyby** | ECS_E_USER_REQUEST_THROTTLED |
| **Požadována náprava** | No |

Není vyžadována žádná akce; Server se znovu pokusí. Pokud tato chyba trvá několik hodin, vytvořte žádost o podporu.

<a id="-2134364043"></a>**Synchronizace je zablokovaná, dokud zjišťování změn nedokončí následné obnovení.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT (desetinné číslo)** | -2134364043 |
| **Text chyby** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Požadována náprava** | No |

Nevyžaduje se žádná akce. Když se soubor nebo sdílená složka (koncový bod cloudu) obnoví pomocí Azure Backup, synchronizace se zablokuje, dokud se zjišťování změn nedokončí ve sdílené složce Azure. Detekce změn se spustí okamžitě po dokončení obnovení a délka jejího trvání závisí na počtu souborů ve sdílené složce.

<a id="-2147216747"></a>**Synchronizace se nezdařila, protože synchronizovaná databáze byla uvolněna.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80041295 |
| **HRESULT (desetinné číslo)** | -2147216747 |
| **Text chyby** | SYNC_E_METADATA_INVALID_OPERATION |
| **Požadována náprava** | No |

K této chybě obvykle dochází v případě, že aplikace zálohování vytvoří snímek VSS a databáze synchronizace se uvolní. Pokud tato chyba trvá několik hodin, vytvořte žádost o podporu.

<a id="-2134364065"></a>**Synchronizace nemá přístup ke sdílené složce Azure zadané v koncovém bodu cloudu.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (desetinné číslo)** | -2134364065 |
| **Text chyby** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **Požadována náprava** | Yes |

K této chybě dochází, protože agent Synchronizace souborů Azure nemůže získat přístup ke sdílené složce Azure. Důvodem může být to, že sdílená složka Azure nebo účet úložiště, který je jejím hostitelem, již neexistují. Při řešení této chyby můžete použít následující postup:

1. [Ověřte, že účet úložiště existuje.](#troubleshoot-storage-account)
2. [Ujistěte se, že sdílená složka Azure existuje.](#troubleshoot-azure-file-share)
3. [Ujistěte se, že Synchronizace souborů Azure má přístup k účtu úložiště.](#troubleshoot-rbac)
4. [Ověřte, že jsou v účtu úložiště správně nakonfigurovaná nastavení brány firewall a virtuální sítě (pokud jsou povolená)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings).

<a id="-2134351804"></a>**Synchronizace se nezdařila, protože žádost není autorizována k provedení této operace.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c86044 |
| **HRESULT (desetinné číslo)** | -2134351804 |
| **Text chyby** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **Požadována náprava** | Yes |

K této chybě dochází, protože agent Synchronizace souborů Azure nemá oprávnění pro přístup ke sdílené složce Azure. Při řešení této chyby můžete použít následující postup:

1. [Ověřte, že účet úložiště existuje.](#troubleshoot-storage-account)
2. [Ujistěte se, že sdílená složka Azure existuje.](#troubleshoot-azure-file-share)
3. [Ověřte, že jsou v účtu úložiště správně nakonfigurovaná nastavení brány firewall a virtuální sítě (pokud jsou povolená)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings).
4. [Ujistěte se, že Synchronizace souborů Azure má přístup k účtu úložiště.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**Použitý název účtu úložiště nelze přeložit.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (desetinné číslo)** | -2134364064 |
| **Text chyby** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Požadována náprava** | Yes |

1. Ověřte, že se název DNS úložiště dá přeložit ze serveru.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Ověřte, že účet úložiště existuje.](#troubleshoot-storage-account)
3. [Ověřte, že jsou v účtu úložiště správně nakonfigurovaná nastavení brány firewall a virtuální sítě (pokud jsou povolená)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings).

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**Při přístupu k účtu úložiště došlo k neznámé chybě.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c8308a |
| **HRESULT (desetinné číslo)** | -2134364022 |
| **Text chyby** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **Požadována náprava** | Yes |

1. [Ověřte, že účet úložiště existuje.](#troubleshoot-storage-account)
2. [Ověřte, že jsou v účtu úložiště správně nakonfigurovaná nastavení brány firewall a virtuální sítě (pokud jsou povolená)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings).

<a id="-2134364014"></a>**Synchronizace se nezdařila z důvodu zamčení účtu úložiště.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c83092 |
| **HRESULT (desetinné číslo)** | -2134364014 |
| **Text chyby** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **Požadována náprava** | Yes |

K této chybě dochází, protože účet úložiště má [Zámek prostředků](../../azure-resource-manager/management/lock-resources.md)jen pro čtení. Tento problém vyřešíte odebráním zámku prostředků jen pro čtení v účtu úložiště. 

<a id="-1906441138"></a>**Synchronizace se nezdařila z důvodu problému s databází synchronizace.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (desetinné číslo)** | -1906441138 |
| **Text chyby** | JET_errWriteConflict |
| **Požadována náprava** | Yes |

K této chybě dochází, pokud dojde k potížím s interní databází, kterou používá Synchronizace souborů Azure. Když k tomuto problému dojde, vytvořte žádost o podporu a budeme vás kontaktovat, abychom vám pomohli tento problém vyřešit.

<a id="-2134364053"></a>**Verze agenta Synchronizace souborů Azure nainstalovaná na serveru není podporovaná.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT (desetinné číslo)** | -2134364053 |
| **Text chyby** | ECS_E_AGENT_VERSION_BLOCKED |
| **Požadována náprava** | Yes |

K této chybě dochází v případě, že se verze agenta Synchronizace souborů Azure nainstalovaná na serveru nepodporuje. Pokud chcete tento problém vyřešit, [upgradujte]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) na [podporovanou verzi agenta]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions).

<a id="-2134351810"></a>**Dosáhli jste limitu úložiště sdílené složky Azure.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (desetinné číslo)** | -2134351810 |
| **Text chyby** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Požadována náprava** | Yes |

K této chybě dochází při dosažení limitu úložiště sdílené složky Azure. K tomu může dojít v případě, že se pro sdílenou složku Azure použije kvóta nebo využití překročí limity pro sdílenou složku Azure. Další informace najdete v tématu [Aktuální limity sdílené složky Azure](storage-files-scale-targets.md).

1. Přejděte do skupiny synchronizace v rámci služby synchronizace úložiště.
2. V rámci skupiny synchronizace vyberte koncový bod cloudu.
3. Poznamenejte si název sdílené složky Azure v otevřeném podokně.
4. Vyberte propojený účet úložiště. Pokud tento odkaz neproběhne úspěšně, odkazovaný účet úložiště se odebral.

    ![Snímek obrazovky s podoknem podrobností cloudového koncového bodu s odkazem na účet úložiště](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Vyberte **soubory** a zobrazte seznam sdílených složek.
6. Klikněte na tři tečky na konci řádku pro sdílenou složku Azure, na kterou odkazuje koncový bod cloudu.
7. Ověřte, že **využití** nedosahuje **kvóty**. Poznámka: Pokud se nezadá alternativní kvóta, kvóta bude odpovídat [maximální velikosti sdílené složky Azure](storage-files-scale-targets.md).

    ![Snímek obrazovky vlastností sdílené složky Azure](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Pokud je sdílená složka plná a kvóta není nastavená, jedním z možných způsobů, jak tento problém vyřešit, je převést každou podsložku aktuálního koncového bodu serveru na samostatný koncový bod serveru v samostatné skupině synchronizace. Tímto způsobem se jednotlivé podsložky budou synchronizovat do samostatných sdílených složek Azure.

<a id="-2134351824"></a>**Sdílenou složku Azure se nepovedlo najít.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (desetinné číslo)** | -2134351824 |
| **Text chyby** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Požadována náprava** | Yes |

K této chybě dochází v případě, že je sdílená složka Azure nepřístupná. Řešení potíží:

1. [Ověřte, že účet úložiště existuje.](#troubleshoot-storage-account)
2. [Ujistěte se, že sdílená složka Azure existuje.](#troubleshoot-azure-file-share)

Pokud se sdílená složka Azure odstranila, musíte vytvořit novou sdílenou složku a pak znovu vytvořit skupinu synchronizace. 

<a id="-2134364042"></a>**Synchronizace je pozastavená, i když je toto předplatné Azure pozastavené.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (desetinné číslo)** | -2134364042 |
| **Text chyby** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Požadována náprava** | Yes |

K této chybě dochází v případě, že dojde k pozastavení předplatného Azure. Synchronizace se znovu povolí po obnovení předplatného Azure. Podívejte [se, proč je moje předplatné Azure zakázané a jak ho znovu aktivovat?](../../cost-management-billing/manage/subscription-disabled.md) Další informace.

<a id="-2134375618"></a>**Účet úložiště má nakonfigurovanou bránu firewall nebo virtuální sítě.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c8033e |
| **HRESULT (desetinné číslo)** | -2134375618 |
| **Text chyby** | ECS_E_SERVER_BLOCKED_BY_NETWORK_ACL |
| **Požadována náprava** | Yes |

K této chybě dochází v případě, že je sdílená složka Azure nepřístupná kvůli bráně firewall účtu úložiště, nebo kvůli tomu, že účet úložiště patří do virtuální sítě. Ověřte, že nastavení brány firewall a virtuální sítě v účtu úložiště jsou správně nakonfigurované. Další informace najdete v tématu [Konfigurace nastavení brány firewall a virtuální sítě](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings). 

<a id="-2134375911"></a>**Synchronizace se nezdařila z důvodu problému s databází synchronizace.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (desetinné číslo)** | -2134375911 |
| **Text chyby** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Požadována náprava** | No |

Tato chyba se obvykle vyřeší sama a může k ní dojít v následujících případech:

* Vysoký počet změn souborů mezi servery ve skupině synchronizace.
* Velký počet chyb na jednotlivých souborech a adresářích.

Pokud tato chyba trvá déle než pár hodin, vytvořte žádost o podporu a my vás budeme kontaktovat, abychom vám pomohli tento problém vyřešit.

<a id="-2146762487"></a>**Serveru se nepodařilo navázat zabezpečené připojení. Cloudová služba přijala neočekávaný certifikát.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (desetinné číslo)** | -2146762487 |
| **Text chyby** | CERT_E_UNTRUSTEDROOT |
| **Požadována náprava** | Yes |

K této chybě může dojít, pokud vaše organizace používá ukončovací proxy server TLS nebo pokud škodlivá entita zachycuje provoz mezi serverem a službou Synchronizace souborů Azure. Pokud jste si jistí, že je to očekávané (protože vaše organizace používá ukončovací proxy server protokolu TLS), přeskočíte ověření certifikátu s přepsáním registru.

1. Vytvořte hodnotu registru SkipVerifyingPinnedRootCertificate.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Restartujte službu synchronizace na zaregistrovaném serveru.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Když nastavíte tuto hodnotu registru, agent Synchronizace souborů Azure při přenosu dat mezi serverem a cloudovou službou přijměte jakýkoli místně důvěryhodný certifikát TLS/SSL.

<a id="-2147012894"></a>**Nebylo možné navázat spojení se službou.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80072EE2 |
| **HRESULT (desetinné číslo)** | -2147012894 |
| **Text chyby** | WININET_E_TIMEOUT |
| **Požadována náprava** | Yes |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**Synchronizace se nezdařila z důvodu problému s ověřováním.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (desetinné číslo)** | -2134375680 |
| **Text chyby** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Požadována náprava** | Yes |

K této chybě obvykle dochází kvůli nesprávnému času na serveru. Pokud server běží na virtuálním počítači, zkontrolujte, jestli je čas hostitele správný.

<a id="-2134364040"></a>**Synchronizace se nezdařila z důvodu vypršení platnosti certifikátu.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c83078 |
| **HRESULT (desetinné číslo)** | -2134364040 |
| **Text chyby** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **Požadována náprava** | Yes |

K této chybě dochází, protože platnost certifikátu používaného k ověřování vypršela.

Pokud chcete ověřit, jestli platnost certifikátu vypršela, proveďte následující kroky:  
1. Otevřete modul snap-in Certifikáty konzoly MMC, vyberte účet počítače a přejděte na certifikáty (místní počítač) \Personal\Certificates.
2. Ověřte, zda vypršela platnost certifikátu pro ověřování klientů.

Pokud platnost certifikátu pro ověřování klientů vypršela, vyřešte problém provedením následujících kroků:

1. Ověřte, že je nainstalovaná verze agenta Synchronizace souborů Azure 4.0.1.0 nebo novější.
2. Na serveru spusťte následující příkaz PowerShellu: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**Synchronizace se nezdařila z důvodu nenalezení ověřovacího certifikátu.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c80228 |
| **HRESULT (desetinné číslo)** | -2134375896 |
| **Text chyby** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **Požadována náprava** | Yes |

K této chybě dochází, protože se nenašel certifikát používaný k ověřování.

Při řešení tohoto problému postupujte následovně:

1. Ověřte, že je nainstalovaná verze agenta Synchronizace souborů Azure 4.0.1.0 nebo novější.
2. Na serveru spusťte následující příkaz PowerShellu: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**Synchronizace se nezdařila, protože nebyla nalezena Identita ověřování.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c83079 |
| **HRESULT (desetinné číslo)** | -2134364039 |
| **Text chyby** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **Požadována náprava** | Yes |

K této chybě dochází kvůli selhání odstranění koncového bodu serveru, který je teď ve stavu částečného odstranění. Pokud chcete tento problém vyřešit, zkuste koncový bod serveru odstranit znovu.

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**Svazek, na kterém je umístěný koncový bod serveru, má nedostatek místa na disku.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (desetinné číslo)** | -1906441711 |
| **Text chyby** | JET_errLogDiskFull |
| **Požadována náprava** | Yes |

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c8031a |
| **HRESULT (desetinné číslo)** | -2134375654 |
| **Text chyby** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Požadována náprava** | Yes |

K této chybě dochází kvůli zaplnění svazku. K této chybě obvykle dochází kvůli tomu, že soubory mimo koncový bod serveru využívají místo na svazku. Uvolněte místo na svazku přidáním dalších koncových bodů serveru, přesunutím souborů na jiný svazek nebo zvětšením velikosti svazku, na kterém je koncový bod serveru zapnutý.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**Služba ještě není připravená na synchronizaci s tímto koncovým bodem serveru.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (desetinné číslo)** | -2134364145 |
| **Text chyby** | ECS_E_REPLICA_NOT_READY |
| **Požadována náprava** | No |

K této chybě dochází, protože koncový bod cloudu byl vytvořen s obsahem již existujícím ve sdílené složce Azure. Než povolíte, aby koncový bod serveru mohl pokračovat v počáteční synchronizaci, Synchronizace souborů Azure musí zkontrolovat sdílenou složku Azure pro veškerý obsah.

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**Synchronizace se nezdařila z důvodu problémů s mnoha jednotlivými soubory.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (desetinné číslo)** | -2134375877 |
| **Text chyby** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Požadována náprava** | Yes |

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c8021c |
| **HRESULT (desetinné číslo)** | -2134375908 |
| **Text chyby** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Požadována náprava** | Yes |

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c80253 |
| **HRESULT (desetinné číslo)** | -2134375853 |
| **Text chyby** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Požadována náprava** | Yes |

Relace synchronizace se nezdaří s jednou z těchto chyb, pokud existuje mnoho souborů, které se nedaří synchronizovat s chybami jednotlivých položek. Provedením kroků popsaných v [návody zjistíte, jestli nejsou nějaké konkrétní soubory nebo složky, které se nesynchronizují?,](?tabs=portal1%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) aby se vyřešily chyby jednotlivých položek. V případě chyby synchronizace ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED otevřete prosím případ podpory.

> [!NOTE]
> Synchronizace souborů Azure vytvoří dočasný snímek VSS jednou denně na serveru pro synchronizaci souborů s otevřenými popisovači.

<a id="-2134376423"></a>**Synchronizace se nezdařila z důvodu problému s cestou koncového bodu serveru.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (desetinné číslo)** | -2134376423 |
| **Text chyby** | ECS_E_SYNC_INVALID_PATH |
| **Požadována náprava** | Yes |

Ujistěte se, že cesta existuje, je na místním svazku NTFS a není bodem rozboru nebo existujícím koncovým bodem serveru.

<a id="-2134375817"></a>**Synchronizace se nezdařila, protože verze ovladače filtru není kompatibilní s verzí agenta.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT (desetinné číslo)** | -2134375817 |
| **Text chyby** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Požadována náprava** | Yes |

K této chybě dochází, protože načtená verze ovladače filtru vrstvení cloudu (StorageSync.sys) není kompatibilní se službou agenta synchronizace úložiště (FileSyncSvc). Pokud došlo k upgradu agenta Synchronizace souborů Azure, restartováním serveru dokončete instalaci. Pokud chyba přetrvává, odinstalujte agenta, restartujte server a znovu nainstalujte agenta Synchronizace souborů Azure.

<a id="-2134376373"></a>**Služba není momentálně k dispozici.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (desetinné číslo)** | -2134376373 |
| **Text chyby** | ECS_E_SERVICE_UNAVAILABLE |
| **Požadována náprava** | No |

K této chybě dochází kvůli nedostupnosti služby Synchronizace souborů Azure. Tato chyba se automaticky vyřeší, jakmile bude služba Synchronizace souborů Azure opět dostupná.

<a id="-2146233088"></a>**Synchronizace se nezdařila z důvodu výjimky.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80131500 |
| **HRESULT (desetinné číslo)** | -2146233088 |
| **Text chyby** | COR_E_EXCEPTION |
| **Požadována náprava** | No |

K této chybě dochází, protože synchronizace selhala kvůli výjimce. Pokud chyba trvá několik hodin, vytvořte prosím žádost o podporu.

<a id="-2134364045"></a>**Synchronizace se nezdařila, protože u účtu úložiště došlo k převzetí služeb při selhání do jiné oblasti.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c83073 |
| **HRESULT (desetinné číslo)** | -2134364045 |
| **Text chyby** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **Požadována náprava** | Yes |

K této chybě dochází, protože došlo k převzetí služeb účtu úložiště při selhání do jiné oblasti. Synchronizace souborů funkci převzetí služeb účtu úložiště při selhání nepodporuje. U účtů úložiště obsahujících sdílené složky Azure, které se v Synchronizaci souborů Azure používají jako koncové body cloudu, by se nemělo provádět převzetí služeb při selhání. Pokud to uděláte, synchronizace přestane fungovat a v případě nově vrstvených souborů může dojít i k neočekávané ztrátě dat. Pokud chcete tento problém vyřešit, přesuňte účet úložiště do primární oblasti.

<a id="-2134375922"></a>**Synchronizace se nezdařila z důvodu přechodného problému s databází synchronizace.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (desetinné číslo)** | -2134375922 |
| **Text chyby** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Požadována náprava** | No |

K této chybě dochází kvůli internímu problému s databází synchronizace. Tato chyba se automaticky vyřeší při opakování synchronizace. Pokud tato chyba trvá déle, vytvořte žádost o podporu a budeme vás kontaktovat, abychom vám pomohli tento problém vyřešit.

<a id="-2134364024"></a>**Synchronizace se nezdařila z důvodu změny v Azure Active Directory tenant**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c83088 |
| **HRESULT (desetinné číslo)** | -2134364024 | 
| **Text chyby** | ECS_E_INVALID_AAD_TENANT |
| **Požadována náprava** | Yes |

Ujistěte se, že máte nejnovějšího agenta Synchronizace souborů Azure. Od agenta v10 za účelem Synchronizace souborů Azure podporuje přesun předplatného na jiného tenanta Azure Active Directory.
 
Až budete mít nejnovější verzi agenta, musíte aplikaci Microsoft. StorageSync udělit přístup k účtu úložiště ( [Zkontrolujte, jestli synchronizace souborů Azure má přístup k účtu úložiště](#troubleshoot-rbac)).

<a id="-2134364010"></a>**Synchronizace se nezdařila z důvodu nekonfigurace výjimky brány firewall a virtuální sítě**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c83096 |
| **HRESULT (desetinné číslo)** | -2134364010 | 
| **Text chyby** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **Požadována náprava** | Yes |

K této chybě dochází, pokud jsou v účtu úložiště povolena nastavení brány firewall a virtuální sítě a možnost "Povolit důvěryhodným službám Microsoftu přístup k tomuto účtu úložiště" není zaškrtnuta. Pokud chcete tento problém vyřešit, postupujte podle kroků popsaných v průvodci nasazením v části [Konfigurace nastavení brány firewall a virtuální sítě](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings).

<a id="-2147024891"></a>**Synchronizace se nezdařila, protože oprávnění ve složce System Volume Information jsou nesprávná.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80070005 |
| **HRESULT (desetinné číslo)** | -2147024891 |
| **Text chyby** | ERROR_ACCESS_DENIED |
| **Požadována náprava** | Yes |

K této chybě může dojít v případě, že účet NT AUTHORITY\SYSTEM nemá oprávnění ke složce s informacemi o systémovém svazku na svazku, na kterém se nachází koncový bod serveru. Všimněte si, že pokud se nedaří synchronizovat jednotlivé soubory s ERROR_ACCESS_DENIED, proveďte kroky popsané v části [řešení potíží s chybami synchronizace podle souboru nebo adresáře](?tabs=portal1%252cazure-portal#troubleshooting-per-filedirectory-sync-errors) .

Při řešení tohoto problému postupujte následovně:

1. Stáhněte si nástroj [PsExec](/sysinternals/downloads/psexec).
2. Spuštěním následujícího příkazu na příkazovém řádku se zvýšenými oprávněními spusťte příkazový řádek pod systémovým účtem: **PsExec.exe -i -s -d cmd** 
3. Spuštěním následujícího příkazu na příkazovém řádku pod systémovým účtem ověřte, jestli účet NT AUTHORITY\SYSTEM skutečně nemá přístup ke složce s informacemi o systémovém svazku: **cacls "<písmeno_jednotky>:\<složka_s_informacemi_o_systémovém_svazku>" /T /C**
4. Pokud účet NT AUTHORITY\SYSTEM nemá přístup ke složce s informacemi o systémovém svazku, spusťte následující příkaz: **cacls "<písmeno_jednotky>:\<složka_s_informacemi_o_systémovém_svazku>" /T /E /G "NT AUTHORITY\SYSTEM:F"**
    - Pokud krok 4 selže z důvodu odepření přístupu, spuštěním následujícího příkazu převezměte vlastnictví složky s informacemi o systémovém svazku a pak zopakujte krok 4: **takeown /A /R /F "<písmeno_jednotky>:\<složka_s_informacemi_o_systémovém_svazku>"**

<a id="-2134375810"></a>**Synchronizace se nezdařila, protože sdílená složka Azure byla odstraněna a znovu vytvořena.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c8027e |
| **HRESULT (desetinné číslo)** | -2134375810 |
| **Text chyby** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **Požadována náprava** | Yes |

K této chybě dochází, protože Synchronizace souborů Azure nepodporuje odstranění a opětovné vytvoření sdílené složky Azure ve stejné skupině synchronizace. 

Pokud chcete tento problém vyřešit, provedením následujících kroků odstraňte a znovu vytvořte skupinu synchronizace:

1. Odstraňte všechny koncové body serveru ve skupině synchronizace.
2. Odstraňte koncový bod cloudu. 
3. Odstraňte skupinu synchronizace.
4. Pokud byla na koncovém bodu serveru povolená vrstva cloudu, odstraňte osamocené vrstvené soubory na serveru, a to [po odstranění oddílu koncového bodu serveru na serveru není dostupné](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) .
5. Znovu vytvořte skupinu synchronizace.

<a id="-2145844941"></a>**Synchronizace se nezdařila, protože požadavek HTTP byl přesměrován**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80190133 |
| **HRESULT (desetinné číslo)** | -2145844941 |
| **Text chyby** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **Požadována náprava** | Yes |

K této chybě dochází, protože Synchronizace souborů Azure nepodporuje přesměrování protokolu HTTP (kód stavu 3xx). Pokud chcete tento problém vyřešit, zakažte na svém proxy serveru nebo síťovém zařízení přesměrování HTTP.

<a id="-2134364027"></a>**Během offline přenosu dat vypršel časový limit, ale stále probíhá.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c83085 |
| **HRESULT (desetinné číslo)** | -2134364027 |
| **Text chyby** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **Požadována náprava** | No |

K této chybě dojde, když operace příjmu dat překročí časový limit. Tuto chybu je možné ignorovat, pokud probíhá synchronizace (AppliedItemCount je větší než 0). Přečtěte si téma [návody sledování průběhu aktuální relace synchronizace?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

<a id="-2134375814"></a>**Synchronizace se nezdařila, protože na serveru nelze nalézt cestu koncového bodu serveru.**  

| Chyba | Kód |
|-|-|
| **HRESULT** | 0x80c8027a |
| **HRESULT (desetinné číslo)** | -2134375814 |
| **Text chyby** | ECS_E_SYNC_ROOT_DIRECTORY_NOT_FOUND |
| **Požadována náprava** | Yes |

K této chybě dojde, pokud byl adresář použitý jako cesta koncového bodu serveru přejmenován nebo odstraněn. Pokud byl adresář přejmenován, přejmenujte adresář zpátky na původní název a restartujte službu agenta synchronizace úložiště (FileSyncSvc).

Pokud byl adresář odstraněn, proveďte následující kroky a odeberte stávající koncový bod serveru a vytvořte nový koncový bod serveru pomocí nové cesty:

1. Odeberte koncový bod serveru ve skupině synchronizace podle postupu popsaného v části [Odebrání koncového bodu serveru](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint).
2. Pomocí postupu popsaného v části [Přidání koncového bodu serveru](./storage-sync-files-server-endpoint.md#add-a-server-endpoint)vytvořte nový koncový bod serveru ve skupině synchronizace.

### <a name="common-troubleshooting-steps"></a>Běžné kroky při řešení potíží
<a id="troubleshoot-storage-account"></a>**Ověřte, že účet úložiště existuje.**  
# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
1. Přejděte do skupiny synchronizace v rámci služby synchronizace úložiště.
2. V rámci skupiny synchronizace vyberte koncový bod cloudu.
3. Poznamenejte si název sdílené složky Azure v otevřeném podokně.
4. Vyberte propojený účet úložiště. Pokud tento odkaz neproběhne úspěšně, odkazovaný účet úložiště se odebral.
    ![Snímek obrazovky s podoknem podrobností cloudového koncového bodu s odkazem na účet úložiště](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

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

<a id="troubleshoot-azure-file-share"></a>**Ujistěte se, že sdílená složka Azure existuje.**  
# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
1. Kliknutím na **Přehled** v obsahu na levé straně se vraťte na stránku hlavního účtu úložiště.
2. Vyberte **soubory** a zobrazte seznam sdílených složek.
3. Ověřte, že se sdílená složka, na kterou se odkazuje koncový bod cloudu, zobrazuje v seznamu sdílených složek (měli byste si je poznamenali v kroku 1 výše).

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

<a id="troubleshoot-rbac"></a>**Ujistěte se, že Synchronizace souborů Azure má přístup k účtu úložiště.**  
# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
1. V obsahu na levé straně klikněte na **řízení přístupu (IAM)** .
1. Klikněte na kartu **přiřazení rolí** a seznam uživatelů a aplikací (*instanční objekty*), které mají přístup k vašemu účtu úložiště.
1. Ověřte, že se v seznamu zobrazí položka **Microsoft. StorageSync** nebo **Služba hybridní synchronizace souborů** (starý název aplikace) s rolí **Čtenář a přístup k datům** . 

    ![Snímek obrazovky s instančním objektem služby Hybrid Synchronizace souborů na kartě řízení přístupu účtu úložiště](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Pokud se v seznamu nezobrazí **Microsoft. StorageSync** nebo **Služba Hybrid synchronizace souborů** , proveďte následující kroky:

    - Klikněte na **Přidat**.
    - V poli **role** vyberte **Čtenář a přístup k datům**.
    - Do pole **Vyberte** zadejte **Microsoft. StorageSync**, vyberte roli a klikněte na **Uložit**.

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

## <a name="cloud-tiering"></a>Vrstvení cloudu 
Existují dvě cesty pro chyby ve vrstvách cloudu:

- Soubory se nemůžou podařit navrstvit, což znamená, že se Synchronizace souborů Azure neúspěšně pokusy o vytvoření vrstvy souboru do služby soubory Azure.
- Nepovedlo se navrátit soubory, což znamená, že Synchronizace souborů Azure filtr systému souborů (StorageSync.sys) se nepodaří stáhnout data, když se uživatel pokusí o přístup k souboru, který byl vrstven.

Existují dvě hlavní třídy selhání, ke kterým může dojít prostřednictvím cesty selhání:

- Selhání cloudového úložiště
    - *Dočasné problémy s dostupností služby úložiště* Další informace najdete v tématu [smlouva SLA (SLA) pro Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - *Nepřístupná sdílená složka Azure* K této chybě obvykle dochází, když odstraníte sdílenou složku Azure, pokud je stále koncový bod cloudu ve skupině synchronizace.
    - *Účet úložiště*, který je k dispozici K této chybě obvykle dochází, když odstraníte účet úložiště, když stále obsahuje sdílenou složku Azure, která je cloudovým koncovým bodem ve skupině synchronizace. 
- Selhání serveru 
  - *Synchronizace souborů Azure filtr systému souborů (StorageSync.sys) není načten*. Aby bylo možné reagovat na požadavky na vrstvení a odvolání, musí být načten filtr systému souborů Synchronizace souborů Azure. Nenačtený filtr může být z několika důvodů, ale nejběžnějším důvodem je to, že ho správce nenačte ručně. Filtr systému souborů Synchronizace souborů Azure musí být pro správné fungování Synchronizace souborů Azure zaveden pro všechny časy.
  - *Chybí, je poškozený nebo jinak přerušený spojovací bod*. Spojovací bod je speciální datová struktura pro soubor, který se skládá ze dvou částí:
    1. Značka rozboru, která indikuje operačnímu systému, že Synchronizace souborů Azure filtr systému souborů (StorageSync.sys) může vyžadovat určitou akci pro vstup do souboru v/v. 
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
   3. Ověřte, že jsou spuštěné ovladače filtru Synchronizace souborů Azure (StorageSync.sys a StorageSyncGuard.sys):
       - Na příkazovém řádku se zvýšenými oprávněními spusťte příkaz `fltmc` . Ověřte, že jsou uvedené StorageSync.sys a StorageSyncGuard.sys ovladače filtru systému souborů.

> [!NOTE]
> ID události 9003 se protokoluje jednou za hodinu v protokolu událostí telemetrie, pokud se soubor nepovede na vrstvu (jedna událost je protokolována podle kódu chyby). Pokud chcete zjistit, jestli jsou v tomto kódu chyby, podívejte se na část [chyby vrstvení a řešení potíží](#tiering-errors-and-remediation) .

### <a name="tiering-errors-and-remediation"></a>Chyby vrstvení a náprava

| HRESULT | HRESULT (desetinné číslo) | Text chyby | Problém | Náprava |
|---------|-------------------|--------------|-------|-------------|
| 0x80c86045 | -2134351803 | ECS_E_INITIAL_UPLOAD_PENDING | Nepovedlo se vytvořit vrstvu souboru, protože probíhá počáteční nahrávání. | Nevyžaduje se žádná akce. Po dokončení počátečního nahrávání bude soubor vrstvený. |
| 0x80c86043 | -2134351805 | ECS_E_GHOSTING_FILE_IN_USE | Úroveň souboru se nezdařila, protože se používá. | Nevyžaduje se žádná akce. Vrstvení souboru proběhne, jakmile se soubor přestane používat. |
| 0x80c80241 | -2134375871 | ECS_E_GHOSTING_EXCLUDED_BY_SYNC | Soubor se nepodařilo vytvořit do vrstvy, protože je vyloučený synchronizací. | Nevyžaduje se žádná akce. Soubory v seznamu pro vyloučení ze synchronizace se nedají vrstvit. |
| 0x80c86042 | -2134351806 | ECS_E_GHOSTING_FILE_NOT_FOUND | Soubor se nepovedlo vytvořit na úrovni, protože se na serveru nenašel. | Nevyžaduje se žádná akce. Pokud chyba přetrvává, zkontrolujte, jestli soubor na serveru existuje. |
| 0x80c83053 | -2134364077 | ECS_E_CREATE_SV_FILE_DELETED | Soubor se nepodařilo nastavit na úroveň, protože byl odstraněn ve sdílené složce Azure. | Nevyžaduje se žádná akce. Při příštím spuštění relace synchronizace stažených souborů by se měl soubor ze serveru odstranit. |
| 0x80c8600e | -2134351858 | ECS_E_AZURE_SERVER_BUSY | Soubor se nepovedlo navrstvit kvůli problému v síti. | Nevyžaduje se žádná akce. Pokud chyba přetrvává, zkontrolujte připojení ke sdílené složce Azure. |
| 0x80072EE7 | -2147012889 | WININET_E_NAME_NOT_RESOLVED | Soubor se nepovedlo navrstvit kvůli problému v síti. | Nevyžaduje se žádná akce. Pokud chyba přetrvává, zkontrolujte připojení ke sdílené složce Azure. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Soubor se nepovedlo vytvořit na úrovni z důvodu chyby odepření přístupu. K této chybě může dojít v případě, že se soubor nachází ve složce replikace DFS-R jen pro čtení. | Synchronizace souborů Azure nepodporuje koncové body serveru ve složkách replikace DFS-R jen pro čtení. Další informace najdete v [průvodci plánováním](./storage-sync-files-planning.md#distributed-file-system-dfs). |
| 0x80072efe | -2147012866 | WININET_E_CONNECTION_ABORTED | Soubor se nepovedlo navrstvit kvůli problému v síti. | Nevyžaduje se žádná akce. Pokud chyba přetrvává, zkontrolujte připojení ke sdílené složce Azure. |
| 0x80c80261 | -2134375839 | ECS_E_GHOSTING_MIN_FILE_SIZE | Nepodařilo se vytvořit vrstvu souboru, protože velikost souboru je menší než podporovaná velikost. | Pokud má agent verzi nižší než 9.0, je minimální podporovaná velikost souboru 64 kB. Pokud má agent verzi 9.0 nebo novější, závisí minimální podporovaná velikost souboru na velikosti clusteru systému souborů (dvojnásobek velikosti clusteru systému souborů). Pokud je například velikost clusteru systému souborů 4kb, minimální velikost souboru je 8 KB. |
| 0x80c83007 | -2134364153 | ECS_E_STORAGE_ERROR | Soubor se nepovedlo vytvořit z důvodu problému s úložištěm Azure. | Pokud chyba přetrvává, vytvořte žádost o podporu. |
| 0x800703e3 | -2147023901 | ERROR_OPERATION_ABORTED | Soubor se nepovedlo navrstvit, protože byl znovu vrácen ve stejnou dobu. | Nevyžaduje se žádná akce. Vrstvení souboru proběhne, až se dokončí odvolání a soubor se již nebude používat. |
| 0x80c80264 | -2134375836 | ECS_E_GHOSTING_FILE_NOT_SYNCED | Nepovedlo se vytvořit vrstvu souboru, protože se nesynchronizoval do sdílené složky Azure. | Nevyžaduje se žádná akce. Jakmile se soubor synchronizuje do sdílené složky Azure, proběhne jeho vrstvení. |
| 0x80070001 | -2147942401 | ERROR_INVALID_FUNCTION | Soubor se nepovedlo vytvořit, protože ovladač filtru vrstvení cloudu (storagesync.sys) není spuštěný. | Chcete-li tento problém vyřešit, otevřete příkazový řádek se zvýšenými oprávněními a spusťte následující příkaz: `fltmc load storagesync`<br>Pokud se nepovede načíst ovladač filtru storagesync při spuštění příkazu příkaz fltmc, odinstalujte agenta Synchronizace souborů Azure, restartujte server a přeinstalujte agenta Synchronizace souborů Azure. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Soubor se nepovedlo vytvořit z důvodu nedostatku místa na disku, na kterém je umístěný koncový bod serveru. | Pokud chcete tento problém vyřešit, uvolněte ve svazku, na kterém se nachází koncový bod serveru, alespoň 100 MB místa na disku. |
| 0x80070490 | -2147023728 | ERROR_NOT_FOUND | Nepovedlo se vytvořit vrstvu souboru, protože se nesynchronizoval do sdílené složky Azure. | Nevyžaduje se žádná akce. Jakmile se soubor synchronizuje do sdílené složky Azure, proběhne jeho vrstvení. |
| 0x80c80262 | -2134375838 | ECS_E_GHOSTING_UNSUPPORTED_RP | Nepodařilo se vytvořit vrstvu souboru, protože se jedná o nepodporovaný bod rozboru. | Pokud se jedná o bod opakované analýzy odstranění duplicitních dat, povolte podporu odstranění duplicitních dat pomocí postupu v [průvodci plánováním](./storage-sync-files-planning.md#data-deduplication). Soubory s jinými body opakované analýzy, než je odstranění duplicitních dat, nejsou podporované a neproběhne u nich vrstvení.  |
| 0x80c83052 | -2134364078 | ECS_E_CREATE_SV_STREAM_ID_MISMATCH | Soubor se nepovedlo navrstvit, protože byl upravený. | Nevyžaduje se žádná akce. Jakmile se upravený soubor synchronizuje do sdílené složky Azure, znovu proběhne jeho vrstvení. |
| 0x80c80269 | -2134375831 | ECS_E_GHOSTING_REPLICA_NOT_FOUND | Nepovedlo se vytvořit vrstvu souboru, protože se nesynchronizoval do sdílené složky Azure. | Nevyžaduje se žádná akce. Jakmile se soubor synchronizuje do sdílené složky Azure, proběhne jeho vrstvení. |
| 0x80072EE2 | -2147012894 | WININET_E_TIMEOUT | Soubor se nepovedlo navrstvit kvůli problému v síti. | Nevyžaduje se žádná akce. Pokud chyba přetrvává, zkontrolujte připojení ke sdílené složce Azure. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Soubor se nepovedlo navrstvit, protože byl upravený. | Nevyžaduje se žádná akce. Jakmile se upravený soubor synchronizuje do sdílené složky Azure, znovu proběhne jeho vrstvení. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Soubor se nepovedlo vytvořit kvůli nedostatečným systémovým prostředkům. | Pokud bude chyba přetrvávat, zjistěte, která aplikace nebo ovladač v režimu jádra způsobují nedostatek systémových prostředků. |
| 0x8e5e03fe | -1906441218 | JET_errDiskIO | Kvůli chybě vstupu/výstupu při zápisu do databáze vrstev cloudu se soubor nepovedlo vytvořit z vrstev. | Pokud chyba přetrvává, spusťte na svazku nástroj Chkdsk a ověřte hardware úložiště. |
| 0x8e5e0442 | -1906441150 | JET_errInstanceUnavailable | Nepovedlo se vytvořit vrstvu souboru, protože databáze vrstev cloudu není spuštěná. | Chcete-li tento problém vyřešit, restartujte službu nebo server FileSyncSvc. Pokud chyba přetrvává, spusťte na svazku nástroj Chkdsk a ověřte hardware úložiště. |



### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>Řešení potíží se soubory, které se nepodařilo znovu zavolat  
Pokud se soubory nepodaří odvolat:
1. V Prohlížeč událostí zkontrolujte protokoly telemetrie, provozní a diagnostické události umístěné v části aplikace a Services\Microsoft\FileSync\Agent.
    1. Ověřte, že soubory existují ve sdílené složce Azure.
    2. Ověřte, že je server připojený k Internetu. 
    3. Otevřete modul snap-in služby konzoly MMC a ověřte, jestli je spuštěná služba agenta synchronizace úložiště (FileSyncSvc).
    4. Ověřte, že jsou spuštěné ovladače filtru Synchronizace souborů Azure (StorageSync.sys a StorageSyncGuard.sys):
        - Na příkazovém řádku se zvýšenými oprávněními spusťte příkaz `fltmc` . Ověřte, že jsou uvedené StorageSync.sys a StorageSyncGuard.sys ovladače filtru systému souborů.

> [!NOTE]
> ID události 9006 se v protokolu událostí telemetrie zaznamená jednou za hodinu, pokud se soubor nepovede vyvolat (jedna událost je protokolována podle kódu chyby). V části [chyby odvolání a náprava problému](#recall-errors-and-remediation) zjistíte, jestli jsou pro kód chyby uvedené opravné kroky.

### <a name="recall-errors-and-remediation"></a>Odvolání chyb a nápravy

| HRESULT | HRESULT (desetinné číslo) | Text chyby | Problém | Náprava |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -2147942521 | ERROR_SEM_TIMEOUT | Soubor se nepovedlo navrátit z důvodu vypršení časového limitu vstupu a výstupu. K tomuto problému může dojít z několika důvodů: omezení prostředků serveru, špatné připojení k síti nebo problém se službou Azure Storage (například omezování). | Nevyžaduje se žádná akce. Pokud chyba trvá několik hodin, otevřete případ podpory. |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | Soubor se nepovedlo navrátit kvůli problému v síti.  | Pokud chyba přetrvává, zkontrolujte připojení ke sdílené složce Azure. |
| 0x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | Soubor se nepovedlo navrátit, protože koncový bod serveru se odstranil. | Pokud chcete tento problém vyřešit, najdete informace v části [vrstvené soubory nejsou po odstranění koncového bodu serveru dostupné na serveru](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Soubor se nepovedlo navrátit kvůli chybě odepření přístupu. K tomuto problému může dojít kvůli tomu, že je v účtu úložiště povolené nastavení brány firewall a virtuální sítě a server nemá přístup k účtu úložiště. | Chcete-li tento problém vyřešit, přidejte IP adresu serveru nebo virtuální síť podle postupu popsaného v části [Konfigurace brány firewall a nastavení virtuální sítě](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) v Průvodci nasazením. |
| 0x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | Soubor se nepovedlo navrátit, protože není dostupný ve sdílené složce Azure. | Pokud chcete tento problém vyřešit, zkontrolujte, jestli soubor existuje ve sdílené složce Azure. Pokud soubor ve sdílené složce Azure existuje, upgradujte na nejnovější [verzi agenta](./storage-files-release-notes.md#supported-versions)synchronizace souborů Azure. |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | Soubor se nepovedlo navrátit kvůli selhání autorizace účtu úložiště. | Pokud chcete tento problém vyřešit, ověřte, [synchronizace souborů Azure má přístup k účtu úložiště](?tabs=portal1%252cazure-portal#troubleshoot-rbac). |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | Soubor se nepovedlo navrátit, protože sdílená složka Azure není dostupná. | Ověřte, že sdílená složka existuje a je přístupná. Pokud se sdílená složka odstranila a znovu vytvořila, proveďte kroky popsané v tématu [synchronizace se nezdařila, protože byla odstraněna a znovu vytvořená sdílená složka Azure](?tabs=portal1%252cazure-portal#-2134375810) , která odstraní a znovu vytvoří skupinu synchronizace. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Soubor se nepovedlo navrátit kvůli nedostatečným systémovým prostředkům. | Pokud bude chyba přetrvávat, zjistěte, která aplikace nebo ovladač v režimu jádra způsobují nedostatek systémových prostředků. |
| 0x8007000e | -2147024882 | ERROR_OUTOFMEMORY | Soubor se nepovedlo navrátit z důvodu nedostatku paměti. | Pokud bude chyba přetrvávat, zjistěte, která aplikace nebo ovladač v režimu jádra způsobují nedostatek paměti. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Stažení souboru z důvodu nedostatku místa na disku se nezdařilo. | Pokud chcete tento problém vyřešit, uvolněte místo na svazku tím, že přesunete soubory na jiný svazek, navýšíte velikost svazku nebo pomocí rutiny Invoke-StorageSyncCloudTiering vynutíte vrstvení souborů. |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>Po odstranění koncového bodu serveru jsou vrstvené soubory na serveru nedostupné
Vrstvené soubory na serveru budou nepřístupné, pokud se soubory před odstraněním koncového bodu serveru nevrátí.

Chyby zaznamenané v případě nepřístupných souborů do vrstev
- Při synchronizaci souboru se do protokolu událostí ItemResults zaznamená chybový kód-2147942467 (0x80070043-ERROR_BAD_NET_NAME).
- Při opětovném volání souboru se do protokolu událostí RecallResults zaznamená chybový kód-2134376393 (0x80c80037-ECS_E_SYNC_SHARE_NOT_FOUND).

Obnovení přístupu k vrstveným souborům je možné při splnění následujících podmínek:
- K odstranění koncového bodu serveru došlo během posledních 30 dnů.
- Koncový bod cloudu se neodstranil. 
- Sdílená složka se neodstranila.
- Skupina synchronizace se neodstranila.

Pokud jsou splněné výše uvedené podmínky, můžete obnovit přístup k souborům na serveru tak, že během 30 dnů znovu vytvoříte koncový bod serveru ve stejné cestě na serveru ve stejné skupině synchronizace. 

Pokud výše uvedené podmínky nejsou splněné, přístup není možné obnovit, protože tyto vrstvené soubory na serveru jsou teď osamocené. Osamocené vrstvené soubory odeberete podle následujících pokynů.

**Poznámky**
- Pokud na serveru nejsou vrstvené soubory dostupné, měl by být úplný soubor dostupný i v případě, že přímo přistupujete ke sdílené složce Azure.
- Pokud chcete v budoucnu zabránit osamoceným vrstveným souborům, postupujte podle kroků popsaných v části [Odebrání koncového bodu serveru](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint) při odstraňování koncového bodu serveru.

<a id="get-orphaned"></a>**Jak získat seznam osamocených souborů** 

1. Ověřte, že je nainstalovaná verze agenta Synchronizace souborů Azure v 5.1 nebo novější verzi.
2. Pro výpis osamocených vrstvených souborů spusťte následující příkazy PowerShellu:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Uložte výstupní soubor OrphanTieredFiles.txt v případě, že se musí soubory po odstranění obnovit ze zálohy.

<a id="remove-orphaned"></a>**Postup odebrání osamocených vrstvených souborů** 

*Možnost 1: Odstraňte osamocené vrstvené soubory.*

Tato možnost odstraní osamocené vrstvené soubory na Windows serveru, ale vyžaduje odebrání koncového bodu serveru, pokud existuje v důsledku opětovného provedení po dobu 30 dnů nebo připojení k jiné skupině synchronizace. Konflikty souborů dojde, pokud jsou soubory aktualizovány na serveru Windows nebo sdílené složky Azure před tím, než se znovu vytvoří koncový bod serveru.

1. Ověřte, že je nainstalovaná verze agenta Synchronizace souborů Azure v 5.1 nebo novější verzi.
2. Zálohujte umístění sdílené složky Azure a koncového bodu serveru.
3. Odeberte koncový bod serveru ve skupině synchronizace (pokud existuje) podle postupu popsaného v části [Odebrání koncového bodu serveru](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint).

> [!Warning]  
> Pokud se před použitím rutiny Remove-StorageSyncOrphanedTieredFiles neodebere koncový bod serveru, odstraní se osamocený vrstvený soubor na serveru a odstraní úplný soubor ve sdílené složce Azure. 

4. Pro výpis osamocených vrstvených souborů spusťte následující příkazy PowerShellu:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. Uložte výstupní soubor OrphanTieredFiles.txt v případě, že se musí soubory po odstranění obnovit ze zálohy.
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

7. Volitelné: znovu vytvořte koncový bod serveru, pokud byl odstraněn v kroku 3.

*Možnost 2: připojení sdílené složky Azure a zkopírování souborů místně, které jsou na serveru osamocené*

Tato možnost nevyžaduje odebrání koncového bodu serveru, ale vyžaduje dostatek místa na disku pro místní kopírování úplných souborů.

1. [Připojte](./storage-how-to-use-files-windows.md) sdílenou složku Azure na Windows serveru, který má osamocené vrstvené soubory.
2. Pro výpis osamocených vrstvených souborů spusťte následující příkazy PowerShellu:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Pomocí výstupního souboru OrphanTieredFiles.txt Identifikujte osamocené vrstvené soubory na serveru.
4. Osamocené vrstvené soubory můžete přepsat zkopírováním úplného souboru ze sdílené složky Azure na Windows Server.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>Řešení potíží neočekávaně vrácených souborů na serveru  
Antivirová ochrana, zálohování a další aplikace, které čtou velký počet souborů, způsobují nezamýšlená volání, pokud nerespektují atribut Skip offline a přeskočí čtení obsahu těchto souborů. U produktů, které tuto možnost podporují, pomáhá přeskočení offline souborů zabránit nežádoucím odvoláním během operací, jako jsou antivirové kontroly nebo úlohy zálohování.

Informace o konfiguraci přeskakování čtení offline souborů v řešení vám sdělí dodavatel příslušného softwaru.

Nezamýšlená revolání mohou nastat také v jiných scénářích, například při procházení souborů v Průzkumníkovi souborů. Pokud v Průzkumníku souborů na serveru otevřete složku obsahující soubory vrstvené v cloudu, může to mít za následek nežádoucí odvolání. Pravděpodobnost, že k tomu dojde, ještě zvyšuje povolené antivirové řešení na serveru.

> [!NOTE]
>Pomocí ID události 9059 v protokolu událostí telemetrie určete, které aplikace způsobují volání. Tato událost poskytuje distribuci odvolání aplikace pro koncový bod serveru a zaznamená se do protokolu jednou za hodinu.

### <a name="tls-12-required-for-azure-file-sync"></a>Pro Synchronizace souborů Azure je vyžadován protokol TLS 1,2

Nastavení TLS můžete zobrazit na serveru tak, že prohlížíte [nastavení registru](/windows-server/security/tls/tls-registry-settings). 

Pokud používáte proxy server, Projděte si dokumentaci k proxy serveru a ujistěte se, že je nakonfigurována pro použití protokolu TLS 1.2.

## <a name="general-troubleshooting"></a>Obecné řešení potíží
Pokud narazíte na problémy s Synchronizace souborů Azure na serveru, začněte provedením následujících kroků:
1. V Prohlížeč událostí zkontrolujte protokoly telemetrie, provozní a diagnostické události.
    - Problémy se synchronizací, vrstvením a odvoláním se zaznamenávají do protokolů telemetrie, diagnostické a provozní události v části aplikace a Services\Microsoft\FileSync\Agent..
    - Problémy související se správou serveru (například nastavení konfigurace) se zaznamenávají do protokolů provozní a diagnostické události v části aplikace a Services\Microsoft\FileSync\Management.
2. Ověřte, že je na serveru spuštěná služba Synchronizace souborů Azure:
    - Otevřete modul snap-in služby konzoly MMC a ověřte, jestli je spuštěná služba agenta synchronizace úložiště (FileSyncSvc).
3. Ověřte, že jsou spuštěné ovladače filtru Synchronizace souborů Azure (StorageSync.sys a StorageSyncGuard.sys):
    - Na příkazovém řádku se zvýšenými oprávněními spusťte příkaz `fltmc` . Ověřte, že jsou uvedené StorageSync.sys a StorageSyncGuard.sys ovladače filtru systému souborů.

Pokud se problém nevyřeší, spusťte nástroj AFSDiag a odešlete jeho výstup souboru. zip do technické podpory, který je přiřazený k vašemu případu pro další diagnostiku.

Chcete-li spustit AFSDiag, proveďte následující postup.

Pro agenta verze V11 a novější:
1. Otevřete okno PowerShellu se zvýšenými oprávněními a potom spusťte následující příkazy (po každém z nich stiskněte ENTER):

    > [!NOTE]
    >AFSDiag vytvoří před shromažďováním protokolů výstupní adresář a dočasnou složku, která je v něm vytvořená, a po provedení odstraní složku Temp. Zadejte umístění výstupu, které neobsahuje data.
    
    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-AFS -OutputDirectory C:\output -KernelModeTraceLevel Verbose -UserModeTraceLevel Verbose
    ```

2. Reprodukujte problém. Až skončíte, zadejte **D**.
3. Soubor. zip, který obsahuje protokoly a trasovací soubory, je uložen do výstupního adresáře, který jste zadali. 

Pro agenta verze V10 za účelem a starší:
1. Vytvořte adresář, do kterého se uloží výstup AFSDiag (například C:\Output).
    > [!NOTE]
    >AFSDiag odstraní veškerý obsah ve výstupním adresáři před shromažďováním protokolů. Zadejte umístění výstupu, které neobsahuje data.
2. Otevřete okno PowerShellu se zvýšenými oprávněními a potom spusťte následující příkazy (po každém z nich stiskněte ENTER):

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Pro Synchronizace souborů Azure úroveň trasování režimu jádra zadejte **1** (není-li uvedeno jinak), chcete-li vytvořit více podrobných trasování, a potom stiskněte klávesu ENTER.
4. Pro úroveň trasování režimu Synchronizace souborů Azure uživatele zadejte **1** (Pokud není uvedeno jinak), aby se vytvořily podrobnější trasování, a pak stiskněte ENTER.
5. Reprodukujte problém. Až skončíte, zadejte **D**.
6. Soubor. zip, který obsahuje protokoly a trasovací soubory, je uložen do výstupního adresáře, který jste zadali.


## <a name="see-also"></a>Viz také
- [Sledování služby Synchronizace souborů Azure](storage-sync-files-monitoring.md)
- [Nejčastější dotazy k souborům Azure](storage-files-faq.md)
- [Řešení potíží se službou Azure Files ve Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Řešení potíží se službou Azure Files v Linuxu](storage-troubleshoot-linux-file-connection-problems.md)

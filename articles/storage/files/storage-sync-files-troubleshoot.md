---
title: Řešení potíží s synchronizace souboru Azure (preview) | Microsoft Docs
description: Řešení běžných problémů s Azure synchronizace souboru.
services: storage
documentationcenter: ''
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: 8526918630189824e26b95df7f0560c96392e55d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="troubleshoot-azure-file-sync-preview"></a>Řešení potíží s synchronizace souboru Azure (preview)
Pomocí synchronizace souboru Azure (preview) můžete centralizovat vaší organizace sdílené složky v souborech Azure, zatímco flexibilitu, výkonu a kompatibility pro místní souborový server. Synchronizace služby Azure souboru transformuje na rychlé mezipaměti Azure sdílené složky systému Windows Server. Můžete použít libovolný protokol, který je k dispozici v systému Windows Server pro přístup k datům místně, včetně protokolu SMB, systém souborů NFS a FTPS. Může mít libovolný počet mezipamětí, jako je třeba po celém světě.

Tento článek slouží můžete odstraňovat potíže a řešit problémy, které se můžete setkat s nasazením Azure synchronizace souboru. Můžeme také popisují, jak shromažďovat důležité protokoly ze systému, pokud je potřeba hlubší rozbor problému. Pokud nevidíte odpověď na svoji otázku, kontaktujte nás prostřednictvím následující kanály (v narůstajícím pořadí):

1. Komentáře části tohoto článku.
2. [Fórum pro Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [Soubory Azure UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. podporu Microsoftu. K vytvoření nové žádosti o podporu, na portálu Azure na **pomoci** vyberte **Nápověda a podpora** tlačítko a potom vyberte **nová žádost o podporu**.

## <a name="storage-sync-service-object-management"></a>Správa objektu synchronizační služby úložiště
Pokud uděláte přesun prostředků z jednoho předplatného do jiného předplatného, souborových prostředků synchronizace (Služba synchronizace úložiště) se bude blokovat přesouvání. 

## <a name="agent-installation-and-server-registration"></a>Instalace a server registrace agenta
### <a name="during-server-registration-get-the-error-the-term-find-azurermresource-is-not-recognized-as-the-name"></a>Během registrace serveru zobrazí chybová zpráva "termín 'najít AzureRMResource' nebyl rozpoznán jako název..."
Problém je, že rutina najít AzureRMResource byl změněn v AzureRM IPv6.  Další verzi agenta synchronizace bude opraven pro podporu AzureRM IPv6.  Do té doby můžete alternativně vyřešit tento problém podle:
1. Zastavit aktuální ServerRegistration.exe prostřednictvím volby Správce úloh
2. Otevřete příkazový řádek prostředí PowerShell jako správce
3. PS C:\> AzureRM odinstalace modulu
4. PS C:\> install-module-name AzureRM - RequiredVersion 5.7.0
5. Spusťte C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.

<a id="agent-installation-failures"></a>**Řešení chyb instalace agenta**  
V případě selhání instalace agenta Azure Sync soubor na příkazovém řádku se zvýšenými oprávněními spusťte následující příkaz, který zapnout protokolování během instalace agenta:

```
StorageSyncAgent.msi /l*v Installer.log
```

Zkontrolujte installer.log a zjistěte příčinu selhání instalace. 

> [!Note]  
> Instalace agenta se nezdaří, pokud váš počítač je nastavit tak, aby používat službu Microsoft Update a není spuštěna služba Windows Update.

<a id="agent-installation-on-DC"></a>**Instalace agenta selže na řadič domény služby Active Directory** Pokud akci a nainstalujte agenta synchronizace na řadič domény služby Active Directory, kde je vlastníkem této role primárního řadiče domény Windows Server 2008 R2 nebo nižší než verze operačního systému, můžete narazit problém kde synchronizace Instalace agenta se nezdařilo.

Chcete-li vyřešit, přeneste roli primárního řadiče domény do jiné domény Řadič spuštěný Windows serveru 2012 R2 nebo novější a pak instalaci synchronizace.

<a id="agent-installation-websitename-failure"></a>**Instalace agenta selže s touto chybou: "Úložiště synchronizace Agent byl předčasně ukončen"**  
Tento problém může dojít, s agentem 1.x verze a jestli název výchozí web služby IIS se změnil. Chcete-li tento problém obejít, použijte naše 2.0.11+ agenta.

<a id="server-registration-missing"></a>**Server není uveden v části registrované servery na portálu Azure**  
Pokud server není uveden v seznamu **registrované servery** pro synchronizační služba úložiště:
1. Přihlaste se k serveru, který chcete zaregistrovat.
2. Otevřete Průzkumníka souborů a potom přejděte do instalačního adresáře nástroje agenta synchronizace úložiště (výchozí umístění je C:\Program Files\Azure\StorageSyncAgent). 
3. Spusťte ServerRegistration.exe a dokončete průvodce registrace serveru se službou Sync úložiště.



<a id="server-already-registered"></a>**Registrace serveru zobrazí následující zprávu během instalace agenta Azure Sync souboru: "Tento server je již zaregistrována"** 

![Snímek obrazovky dialogového okna registrace serveru s chybou "serveru je již zaregistrován" zpráv](media/storage-sync-files-troubleshoot/server-registration-1.png)

Tato zpráva se zobrazí, pokud server byl dříve registrován u služby synchronizace úložiště. Pokud chcete zrušit registraci serveru z aktuální synchronizační služba úložiště a potom proveďte registraci s novou synchronizační službu úložiště, proveďte kroky, které jsou popsané v [zrušit registraci serveru s Azure souboru Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Pokud tento server není uveden v části **registrované servery** v synchronizační službu úložiště na serveru, který chcete zrušit registraci, spusťte následující příkazy prostředí PowerShell:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Pokud je server součástí clusteru, můžete použít nepovinný *resetování StorageSyncServer - CleanClusterRegistration* parametr také odebrání registrace clusteru.

<a id="web-site-not-trusted"></a>**Při I registraci serveru, zobrazuje mnoho odpovědí "webový server není důvěryhodný". Proč?**  
Tento problém nastane, když **rozšířeného zabezpečení aplikace Internet Explorer** je povolena při registraci serveru. Další informace o tom, jak zakázat správně **rozšířeného zabezpečení aplikace Internet Explorer** zásady, najdete v části [Příprava systému Windows Server pro použití s Azure souboru Sync](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) a [jak nasadit Azure File Synchronizace (preview)](storage-sync-files-deployment-guide.md).

## <a name="sync-group-management"></a>Synchronizace skupiny správy
<a id="cloud-endpoint-using-share"></a>**Vytvoření koncového bodu cloudu selže s touto chybou: "Zadaný Azure sdílení souborů je již používán jinou CloudEndpoint"**  
K tomuto problému dochází, pokud sdílenou složkou Azure je již používán jiným koncovým bodem cloudu. 

Pokud se zobrazí tato zpráva a sdílenou složkou Azure aktuálně není používán koncového bodu cloudu, proveďte následující kroky zrušte Azure souboru synchronizovat metadata Azure sdílené složky:

> [!Warning]  
> Odstranění metadat Azure sdílenou, který je aktuálně používán koncového bodu cloudu způsobí selhání operací synchronizace souboru Azure. 

1. Na portálu Azure přejděte do sdílené složky Azure file.  
2. Klikněte pravým tlačítkem myši sdílenou složku Azure a potom vyberte **upravit metadata**.
3. Klikněte pravým tlačítkem na **SyncService**a potom vyberte **odstranit**.

<a id="cloud-endpoint-authfailed"></a>**Vytvoření koncového bodu cloudu selže s touto chybou: "AuthorizationFailed"**  
K tomuto problému dochází, pokud váš uživatelský účet nemá dostatečná práva k vytvoření koncového bodu cloudu. 

Pokud chcete vytvořit koncový bod cloudu, váš uživatelský účet musí mít následující oprávnění Authorization Microsoft:  
* Přečtěte si: Získání definice role
* Zápisu: Vytvořit nebo aktualizovat vlastní definici role
* Přečtěte si: Získat přiřazení role
* Zápisu: Vytvořit přiřazení role

Následující předdefinované role nemá požadovaná oprávnění Authorization Microsoft:  
* Vlastník
* Správce přístupu uživatelů k určení, zda vaše uživatelská role účet má potřebná oprávnění:  
1. Na portálu Azure vyberte **skupiny prostředků**.
2. Vyberte skupinu prostředků, kde je umístěn účet úložiště a pak vyberte **přístup k ovládacímu prvku (IAM)**.
3. Vyberte **role** (například vlastníka nebo přispěvatele) pro váš uživatelský účet.
4. V **poskytovatele prostředků** seznamu, vyberte **Microsoft Authorization**. 
    * **Přiřazení role** by měl mít **čtení** a **zápisu** oprávnění.
    * **Definice role** by měl mít **čtení** a **zápisu** oprávnění.

<a id="server-endpoint-createjobfailed"></a>**Vytvoření koncového bodu serveru selže s touto chybou: "MgmtServerJobFailed" (kód chyby:-2134375898)**                                                                                                                    
K tomuto problému dochází, pokud cesta ke koncovému bodu serveru je na systémovém svazku a cloud vrstvení je povoleno. Cloud vrstvení není podporována na systémovém svazku. Chcete-li vytvořit koncový bod serveru na systémovém svazku, zakažte vrstvení při vytváření serveru koncového bodu cloudu.

<a id="server-endpoint-deletejobexpired"></a>**Odstranění koncového bodu serveru selže s touto chybou: "MgmtServerJobExpired"**                
K tomuto problému dochází, pokud server je offline nebo nemá připojení k síti. Pokud server již není k dispozici, zrušte registraci serveru na portálu, který bude server koncové body odstranit. Pokud chcete odstranit koncové body serveru, postupujte podle kroků popsaných v [zrušit registraci serveru s Azure souboru Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

<a id="server-endpoint-provisioningfailed"></a>**Nejde otevřít stránku vlastnosti koncový bod serveru nebo aktualizovat zásady vrstvení cloudu**

Tento problém může dojít, pokud dojde k selhání operace správy na serveru koncového bodu. Pokud stránka Vlastnosti koncový bod serveru nelze otevřít na portálu Azure, aktualizuje se koncový bod serveru pomocí příkazů prostředí PowerShell ze serveru může tento problém vyřešit. 

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzureRmStorageSyncServerEndpoint -SubscriptionId mysubguid -ResourceGroupName myrgname -StorageSyncServiceName storagesvcname -SyncGroupName mysyncgroup

# Update the free space percent policy for the server endpoint
Set-AzureRmStorageSyncServerEndpoint -Id serverendpointid -CloudTiering true -VolumeFreeSpacePercent 60
```

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**Pokud soubor vytvořili přímo v mé sdílenou složku Azure přes protokol SMB nebo prostřednictvím portálu, jak dlouho trvá souboru pro synchronizaci servery ve skupině synchronizace?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="broken-sync"></a>**Selhání synchronizace na serveru**  
Pokud se synchronizace nezdaří na serveru:
1. Ověřte, že koncový bod serveru existuje na portálu Azure pro adresář, který chcete synchronizovat do Azure sdílené složky:
    
    ![Snímek obrazovky skupiny synchronizace s koncového bodu cloudu a koncový bod serveru na portálu Azure](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

2. V prohlížeči událostí zkontrolujte provozu a diagnostické protokoly událostí, umístěná aplikace a Services\Microsoft\FileSync\Agent.
    1. Ověřte, zda má server připojení k Internetu.
    2. Ověřte, zda je na serveru spuštěna služba synchronizace souboru Azure. K tomu, otevřete modul snap-in konzoly MMC služby a ověřte, zda je spuštěna Služba agenta synchronizace úložiště (FileSyncSvc).

<a id="replica-not-ready"></a>**Synchronizace selže s touto chybou: "0x80c8300f - replika není připraven k provedení požadované operace."**  
Tento problém se očekává, pokud chcete vytvořit koncový bod cloudu a použít sdílenou složku Azure, který obsahuje data. Úlohu detekce změn, která hledá změny do sdílené složky Azure je naplánováno pro jednou za 24 hodin.  Čas dokončení je závislá na velikost oboru názvů do sdílené složky Azure.  Tato chyba by ji okamžitě přejděte po dokončení.


    > [!NOTE]
    > Azure File Sync periodically takes VSS snapshots to sync files that have open handles.

Aktuálně nepodporujeme přesunutí prostředku do jiného předplatného nebo na, Přesun do jiné služby Azure AD klienta.  Pokud předplatné přesune na jiný klienta, sdílenou složkou Azure nedostupné naše služby založené na změnu v hodnotě vlastnictví. Pokud dojde ke změně klienta, budete muset odstranit koncové body serveru a koncového bodu cloudu (najdete v části synchronizace skupiny Management část pokyny čistém znovu použít sdílenou složku Azure file) a znovu vytvořte skupiny synchronizace.

<a id="doesnt-have-enough-free-space"></a>**Tento počítač nemá dostatek volného místa chyby**  
Pokud portálu se zobrazí stav "Tento počítač nemá dostatek volného místa" může být problém, zůstane menší než 1 GB volného místa na svazku.  Například pokud je svazek 1,5 GB, synchronizaci pouze budou moct využívat .5GB, jestli jste nedosáhli tento problém, prosím zvětšení velikosti svazku používá pro koncový bod serveru.

## <a name="cloud-tiering"></a>Vrstvení cloudu 
Existují dvě cesty pro selhání v cloudu vrstvení:

- Soubory můžete nepodaří vrstvy, což znamená, že synchronizace souboru Azure neúspěšně pokusí vrstvy soubor k Azure Files.
- Soubory může selhat pro vyvolání, což znamená, že filtrem systému souborů synchronizace souboru Azure selže (StorageSync.sys) ke stahování dat při pokusy o uživatele pro přístup k souboru, které byly zřízeny vrstvené.

Existují dvě hlavní třídy chyb, který může nastat prostřednictvím buď selhání cesta:

- Chyby úložiště cloudu
    - *Problémy s dostupností služby přechodným úložištěm*. V tématu [o úrovni služeb smlouvy (SLA) pro Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/) Další informace.
    - *Nedostupné sdílenou složku Azure*. Tato chyba obvykle se stane, když odstraníte, že Azure sdílené složky, pokud je stále koncového bodu cloudu ve skupině pro synchronizaci.
    - *Účet úložiště nepřístupný*. Tato chyba obvykle dojde, když odstraníte účet úložiště při má stále Azure sdílenou, což je koncový bod cloudu ve skupině pro synchronizaci. 
- Selhání serveru 
    - *Azure synchronizace souboru filtrem systému souborů (StorageSync.sys) není načten*. Chcete-li reagovat na vrstvení nebo odvolání požadavky, musí být načteny filtrem systému souborů synchronizace souboru Azure. Filtr nebudou načteny může dojít z několika důvodů, ale nejběžnějším důvodem je, že správce uvolněna ji ručně. Filtrem systému souborů synchronizace souboru Azure musí být načteny na všechny časy pro Azure souboru synchronizaci správně fungovat.
    - *Chybí, je poškozený nebo jinak poškozený spojovací bod*. Bod rozboru je speciální datová struktura, na soubor, který se skládá ze dvou částí:
        1. Značku rozboru, což znamená operačního systému, že synchronizace souboru Azure filtrem systému souborů (StorageSync.sys) možná muset provést některé akce na vstupu nebo výstupu do souboru. 
        2. Rozboru data, která určuje filtrem systému souborů, identifikátor URI souboru na koncovém bodu přidruženého cloudu (Azure sdílené složky). 
        
        Bod rozboru mohly být poškozeny nejběžnější způsob je, pokud se správce pokusí změnit značky nebo jeho data. 
    - *Síťové problémy s připojením k*. Chcete-li úroveň, nebo odvolat soubor, server musí mít připojení k Internetu.

Následující části určují, jak vrstvení potíží cloudu a určí, zda problém problémem cloudové úložiště nebo problémem serveru.

<a id="files-fail-tiering"></a>**Řešení potíží s soubory, které se nepodařilo vrstvy**  
Pokud se soubory se nepodaří vrstvy do Azure souborů:

1. Ověřte, že soubory existují v Azure sdílené složky.

    > [!NOTE]
    > Soubor musí synchronizovat do Azure sdílené složky, než může být rozvrstvena.
2. V prohlížeči událostí zkontrolujte provozu a diagnostické protokoly událostí, umístěná aplikace a Services\Microsoft\FileSync\Agent.
    1. Ověřte, zda má server připojení k Internetu. 
    2. Ověřte, zda jsou spuštěny ovladače filtru synchronizace souboru Azure (StorageSync.sys a StorageSyncGuard.sys):
        - Na příkazovém řádku se zvýšenými oprávněními spusťte `fltmc`. Ověřte, že jsou uvedeny StorageSync.sys a StorageSyncGuard.sys filtru ovladače systému souborů.

<a id="files-fail-recall"></a>**Řešení potíží s soubory, které se nepodařilo zrušit**  
Pokud soubory se nepodařilo zrušit:
1. V prohlížeči událostí zkontrolujte provozu a diagnostické protokoly událostí, umístěná aplikace a Services\Microsoft\FileSync\Agent.
    1. Ověřte, že soubory existují v Azure sdílené složky.
    2. Ověřte, zda má server připojení k Internetu. 
    3. Ověřte, zda jsou spuštěny ovladače filtru synchronizace souboru Azure (StorageSync.sys a StorageSyncGuard.sys):
        - Na příkazovém řádku se zvýšenými oprávněními spusťte `fltmc`. Ověřte, že jsou uvedeny StorageSync.sys a StorageSyncGuard.sys filtru ovladače systému souborů.

<a id="files-unexpectedly-recalled"></a>**Řešení potíží s soubory neočekávaně vrácené na serveru**  
Antivirové programy, zálohování a jiné aplikace, které čtou velkého počtu souborů způsobit nečekané navrácení, není-li se respektují offline atribut přeskočit a přeskočit, je obsah těchto souborů. Přeskočení offline souborů pro produkty podporující, které tato možnost pomáhá vyhnout neočekávaným navrácení během operací, jako je antivirový kontroly nebo úlohy zálohování.

Požádejte dodavatele softwaru se dozvíte, jak nakonfigurovat své řešení tak, aby přeskočil čtení offline soubory.

Nezamýšleným navrácení také může dojít v dalších scénářích, jako když procházíte soubory v Průzkumníku souborů. Otevření složky, která má vrstvené cloudu soubory v Průzkumníku souborů na serveru může způsobit nečekané navrácení. Toto je i pravděpodobnější, pokud je na serveru povoleno antivirový řešení.

## <a name="general-troubleshooting"></a>Obecné řešení potíží
Pokud dojde k potížím se synchronizací souboru Azure na serveru, spusťte následující kroky:
1. V prohlížeči událostí zkontrolujte protokoly událostí provozu a diagnostiky.
    - Synchronizace, vrstvení a odvolání problémy jsou protokolovány v protokolech událostí diagnostiky a provozu v rámci aplikace a Services\Microsoft\FileSync\Agent.
    - Problémy související se správou serveru (například nastavení konfigurace) jsou protokolovány v provozu a diagnostické protokoly událostí aplikace a Services\Microsoft\FileSync\Management.
2. Ověřte, zda je na serveru spuštěna služba synchronizace souboru Azure:
    - Otevřete modul snap-in konzoly MMC služby a ověřte, zda je spuštěna Služba agenta synchronizace úložiště (FileSyncSvc).
3. Ověřte, zda jsou spuštěny ovladače filtru synchronizace souboru Azure (StorageSync.sys a StorageSyncGuard.sys):
    - Na příkazovém řádku se zvýšenými oprávněními spusťte `fltmc`. Ověřte, že jsou uvedeny StorageSync.sys a StorageSyncGuard.sys filtru ovladače systému souborů.

Pokud není problém vyřešen, spusťte nástroj AFSDiag:
1. Vytvořte adresář, kde se uloží výstupní AFSDiag (například C:\Output).
2. Otevřete okno prostředí PowerShell se zvýšenými oprávněními a spusťte následující příkazy (po každém příkazu stiskněte klávesu Enter):

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Úroveň trasování synchronizace souboru Azure jádra režimu, zadejte **1** (Pokud není uvedeno jinak, chcete-li vytvořit více podrobného trasování), a stiskněte klávesu Enter.
4. Úroveň trasování synchronizace souboru Azure uživatele režimu, zadejte **1** (Pokud není uvedeno jinak, chcete-li vytvořit více podrobného trasování), a stiskněte klávesu Enter.
5. Reprodukujte problém. Až budete hotovi, zadejte **D**.
6. Soubor .zip, který obsahuje protokolů a trasovací soubory se uloží do výstupního adresáře, který jste zadali.

## <a name="see-also"></a>Další informace najdete v tématech
- [Nejčastější dotazy k Azure soubory](storage-files-faq.md)
- [Řešení potíží s Azure soubory v systému Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Poradce při potížích Azure soubory v systému Linux](storage-troubleshoot-linux-file-connection-problems.md)

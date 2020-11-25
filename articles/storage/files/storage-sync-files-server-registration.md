---
title: Správa registrovaných serverů pomocí Azure File Sync | Microsoft Docs
description: Naučte se registrovat a odregistrovat Windows Server pomocí Azure File Sync služby synchronizace úložiště.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 194b0f2ff94197fe11c189e97dbc65c9d0367932
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013917"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Správa registrovaných serverů pomocí Azure File Sync
Synchronizace souborů Azure umožňuje centralizovat sdílené složky organizace ve službě Soubory Azure bez ztráty flexibility, výkonu a kompatibility místního souborového serveru. Dělá to tak, že transformuje servery Windows na rychlou mezipaměť sdílené složky Azure. Pro místní přístup k datům můžete použít jakýkoli protokol dostupný ve Windows Serveru (včetně SMB, NFS a FTPS) a můžete mít libovolný počet mezipamětí po celém světě.

Následující článek ukazuje, jak zaregistrovat a spravovat server pomocí služby synchronizace úložiště. Informace o tom, jak nasadit Azure File Sync kompletní, najdete v tématu [nasazení Azure File Sync](storage-sync-files-deployment-guide.md) .

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Registrace nebo zrušení registrace serveru pomocí služby synchronizace úložiště
Registrace serveru pomocí Azure File Sync vytvoří vztah důvěryhodnosti mezi Windows serverem a Azure. Tento vztah se pak dá použít k vytvoření *koncových bodů serveru* na serveru, který představuje konkrétní složky, které se mají synchronizovat se sdílenou složkou Azure (označovanou také jako *koncový bod cloudu*). 

### <a name="prerequisites"></a>Požadavky
Pokud chcete zaregistrovat server se službou synchronizace úložiště, musíte nejdřív připravit server s nezbytnými požadavky:

* Na serveru musí běžet podporovaná verze Windows serveru. Další informace najdete v tématu [Azure File Sync požadavky na systém a interoperabilita](storage-sync-files-planning.md#windows-file-server-considerations).
* Ujistěte se, že je nasazená služba synchronizace úložiště. Další informace o tom, jak nasadit službu synchronizace úložiště, najdete v tématu [nasazení Azure File Sync](storage-sync-files-deployment-guide.md).
* Ujistěte se, že je server připojený k Internetu a že je k dispozici Azure.
* Zakažte konfiguraci rozšířeného zabezpečení IE pro správce pomocí uživatelského rozhraní Správce serveru.
    
    ![Správce serveru uživatelské rozhraní s zvýrazněnou konfigurací rozšířeného zabezpečení IE](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Ujistěte se, že je na vašem serveru nainstalovaný modul Azure PowerShell. Pokud je váš server členem clusteru s podporou převzetí služeb při selhání, bude každý uzel v clusteru vyžadovat modul AZ Module. Další podrobnosti o tom, jak nainstalovat modul AZ Module, najdete v tématu [install and configure Azure PowerShell](/powershell/azure/install-Az-ps).

    > [!Note]  
    > K registraci nebo zrušení registrace serveru doporučujeme použít nejnovější verzi modulu AZ PowerShell. Pokud je na tomto serveru dřív nainstalovaná sada AZ Package (a verze PowerShellu na tomto serveru je 5. * nebo vyšší), můžete `Update-Module` k aktualizaci tohoto balíčku použít rutinu. 
* Pokud ve svém prostředí používáte proxy server sítě, nakonfigurujte nastavení proxy serveru tak, aby agent synchronizace mohl využívat.
    1. Určení IP adresy a čísla portu proxy serveru
    2. Upravte tyto dva soubory:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Přidejte řádky na obrázku 1 (pod tuto část) v části/System.ServiceModel do výše uvedených dvou souborů – Změna adresy 127.0.0.1:8888 na správnou IP adresu (nahraďte 127.0.0.1) a správné číslo portu (nahraďte 8888):
    4. Nastavení proxy serveru WinHTTP pomocí příkazového řádku:
        * Zobrazit proxy: netsh WinHTTP zobrazit proxy
        * Nastavte proxy: netsh winhttp set proxy 127.0.0.1:8888
        * Resetování proxy serveru: netsh WinHTTP Reset proxy
        * Pokud je toto nastavení po instalaci agenta, restartujte agenta synchronizace: net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Registrace serveru se službou synchronizace úložiště
Předtím, než se server dá použít jako *koncový bod serveru* ve *skupině synchronizace* Azure File Sync, musí být zaregistrovaný ve *službě synchronizace úložiště*. Server lze registrovat současně pouze s jednou službou synchronizace úložiště.

#### <a name="install-the-azure-file-sync-agent"></a>Instalace agenta Synchronizace souborů Azure
1. [Stáhněte agenta Azure File Sync](https://go.microsoft.com/fwlink/?linkid=858257).
2. Spusťte instalační program agenta Azure File Sync.
    
    ![První podokno instalačního programu agenta Azure File Sync](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Nezapomeňte povolit aktualizace agenta Azure File Sync pomocí Microsoft Update. Je důležité, protože důležité opravy zabezpečení a vylepšení funkcí balíčku serveru jsou dodávány prostřednictvím Microsoft Update.

    ![Ujistěte se, že je povolená možnost Microsoft Update v podokně Microsoft Update instalačního programu agenta Azure File Sync](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Pokud server nebyl dřív zaregistrován, uživatelské rozhraní pro registraci serveru se po dokončení instalace okamžitě zobrazí.

> [!Important]  
> Pokud je server členem clusteru s podporou převzetí služeb při selhání, musí být agent Azure File Sync nainstalovaný na všech uzlech v clusteru.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Registrace serveru pomocí uživatelského rozhraní pro registraci serveru
1. Pokud se uživatelské rozhraní pro registraci serveru nespouštělo hned po dokončení instalace agenta Azure File Sync, můžete ho spustit ručně spuštěním příkazu `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe` .
2. Pro přístup k předplatnému Azure klikněte na *Přihlásit* se. 

    ![Otevření dialogového okna registračního uživatelského rozhraní serveru](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Z dialogového okna vyberte správné předplatné, skupinu prostředků a službu synchronizace úložiště.

    ![Informace o službě synchronizace úložiště](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. Ve verzi Preview se k dokončení tohoto procesu vyžaduje jedno další přihlášení. 

    ![Přihlašovací dialogové okno](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Pokud je server členem clusteru s podporou převzetí služeb při selhání, musí každý server spustit registraci serveru. Po zobrazení registrovaných serverů na webu Azure Portal Azure File Sync automaticky rozpoznává každý uzel jako člena stejného clusteru s podporou převzetí služeb při selhání a odpovídajícím způsobem seskupuje jejich dohromady.

#### <a name="register-the-server-with-powershell"></a>Registrace serveru pomocí PowerShellu
Můžete taky provést registraci serveru přes PowerShell. 

```powershell
Register-AzStorageSyncServer -ResourceGroupName "<your-resource-group-name>" -StorageSyncServiceName "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Zrušení registrace serveru ve službě úložiště synchronizace
Zrušení registrace serveru pomocí služby synchronizace úložiště vyžaduje několik kroků. Pojďme se podívat, jak správně zrušit registraci serveru.

> [!Warning]  
> Nepokoušejte se řešit potíže se synchronizací, vrstvením cloudu nebo jakýmkoli jiným aspektem Azure File Sync zrušením registrace a registrace serveru nebo odebráním a opětovným vytvořením koncových bodů serveru, pokud je neexplicitně nevydá pokyn pro inženýra společnosti Microsoft. Zrušení registrace serveru a odebrání koncových bodů serveru je destruktivní operace a vrstvený soubor na svazcích s koncovými body serveru se po opětovném vytvoření zaregistrovaného serveru a koncových bodů serveru znovu nepřipojí ke svým umístěním ve sdílené složce Azure, což způsobí chyby synchronizace. Všimněte si také, že vrstvené soubory, které existují mimo obor názvů koncového bodu serveru, mohou být trvale ztraceny. Vrstvené soubory můžou existovat v rámci koncových bodů serveru i v případě, že se vrstva cloudu nikdy nepovolila.

#### <a name="optional-recall-all-tiered-data"></a>Volitelné Odvolat všechna vrstvená data
Pokud byste chtěli, aby byly soubory, které jsou aktuálně vrstveny po odebrání Azure File Sync (tj. Jedná se o produkční, nikoli test, prostředí), odvoláte všechny soubory na každém svazku, který obsahuje koncové body serveru. Zakažte vrstvení cloudu pro všechny koncové body serveru a pak spusťte následující rutinu PowerShellu:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Pokud místní svazek, který je hostitelem koncového bodu serveru, nemá dost volného místa pro odvolání všech vrstvených dat, `Invoke-StorageSyncFileRecall` rutina se nezdaří.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Odebrat server ze všech skupin synchronizace
Než zrušíte registraci serveru v rámci služby synchronizace úložiště, musí se odebrat všechny koncové body serveru na tomto serveru. To lze provést prostřednictvím Azure Portal:

1. Přejděte do služby synchronizace úložiště, kde je váš server zaregistrovaný.
2. Odebere všechny koncové body serveru pro tento server v každé skupině synchronizace ve službě synchronizace úložiště. To lze provést kliknutím pravým tlačítkem myši na příslušný koncový bod serveru v podokně synchronizovat skupinu.

    ![Odebrání koncového bodu serveru ze skupiny synchronizace](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Můžete to také provést pomocí jednoduchého skriptu prostředí PowerShell:

```powershell
Connect-AzAccount

$storageSyncServiceName = "<your-storage-sync-service>"
$resourceGroup = "<your-resource-group>"

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $storageSyncServiceName | ForEach-Object { 
    $syncGroup = $_; 
    Get-AzStorageSyncServerEndpoint -ParentObject $syncGroup | Where-Object { $_.ServerEndpointName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzStorageSyncServerEndpoint -InputObject $_ 
    } 
}
```

#### <a name="unregister-the-server"></a>Zrušení registrace serveru
Teď, když jsou všechna data odvolána a server byl odebrán ze všech skupin synchronizace, lze zrušit registraci serveru. 

1. V Azure Portal přejděte do části *registrované servery* služby synchronizace úložiště.
2. Klikněte pravým tlačítkem na server, jehož registraci chcete zrušit, a pak klikněte na zrušit registraci serveru.

    ![Zrušit registraci serveru](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Zajištění, že Azure File Sync je dobrým sousedem ve vašem datovém centru 
Vzhledem k tomu, že Azure File Sync bude jenom zřídka jediná služba běžící ve vašem datovém centru, možná budete chtít omezit využití sítě a úložiště Azure File Sync.

> [!Important]  
> Nastavení omezení je příliš nízké, bude mít vliv na výkon Azure File Sync synchronizace a odvolání.

### <a name="set-azure-file-sync-network-limits"></a>Nastavit Azure File Sync omezení sítě
Využití sítě Azure File Sync můžete omezit pomocí `StorageSyncNetworkLimit` rutin.

> [!Note]  
> Omezení sítě se nevztahují, když je k vrstvenému souboru přistupoval.

Můžete například vytvořit nové omezení omezení, abyste zajistili, že Azure File Sync nepoužívá více než 10 MB/s v rozmezí od 9 do 9:00 do 5 hodin (17:00h) během pracovního týdne: 

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

Omezení můžete zobrazit pomocí následující rutiny:

```powershell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Pokud chcete odebrat omezení sítě, použijte `Remove-StorageSyncNetworkLimit` . Například následující příkaz odebere všechna omezení sítě:

```powershell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Použití technologie QoS pro úložiště Windows serveru 
Pokud je Azure File Sync hostovaný na virtuálním počítači, který běží na hostiteli virtualizace Windows serveru, můžete použít QoS úložiště (kvalitu služby úložiště) a regulovat spotřebu v/v úložiště. Zásadu QoS úložiště je možné nastavit buď jako maximální (nebo jako limit, jako je StorageSyncNetwork limit), nebo jako minimum (nebo rezervace). Nastavení minimální hodnoty, než je maximum, umožňuje Azure File Sync nárůstu využití dostupné šířky pásma úložiště, pokud jiné úlohy je nepoužívají. Další informace najdete v tématu [kvalita služby úložiště](/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>Viz také
- [Plánování nasazení Synchronizace souborů Azure](storage-sync-files-planning.md)
- [Nasazení Synchronizace souborů Azure](storage-sync-files-deployment-guide.md)
- [Sledování služby Synchronizace souborů Azure](storage-sync-files-monitoring.md)
- [Řešit problémy se Synchronizací souborů Azure](storage-sync-files-troubleshoot.md)
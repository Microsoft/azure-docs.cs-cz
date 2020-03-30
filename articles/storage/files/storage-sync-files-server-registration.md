---
title: Správa registrovaných serverů pomocí Azure File Sync | Dokumenty společnosti Microsoft
description: Přečtěte si, jak zaregistrovat a zrušit registraci Windows Serveru pomocí služby Synchronizace úložiště synchronizace souborů Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2656716560b981481273c3032fc0c7b1a06be8a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255090"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Správa registrovaných serverů pomocí Azure File Sync
Synchronizace souborů Azure umožňuje centralizovat sdílené složky organizace ve službě Soubory Azure bez ztráty flexibility, výkonu a kompatibility místního souborového serveru. Je to tím, že transformuje vaše servery Windows do rychlé mezipaměti sdílené složky Azure. Pro místní přístup k datům můžete použít jakýkoli protokol dostupný ve Windows Serveru (včetně SMB, NFS a FTPS) a můžete mít libovolný počet mezipamětí po celém světě.

Následující článek ukazuje, jak zaregistrovat a spravovat server pomocí služby synchronizace úložiště. Informace [o tom,](storage-sync-files-deployment-guide.md) jak nasadit azure file sync, najdete v článku Jak nasadit azure file sync od konce do konce.

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Registrace/zrušení registrace serveru pomocí služby Synchronizace úložiště
Registrace serveru pomocí Azure File Sync vytvoří vztah důvěryhodnosti mezi Windows Server a Azure. Tento vztah pak lze použít k vytvoření *koncových bodů serveru* na serveru, které představují konkrétní složky, které by měly být synchronizovány se sdílenou složkou Azure (označované také jako *koncový bod cloudu).* 

### <a name="prerequisites"></a>Požadavky
Chcete-li zaregistrovat server se službou Synchronizace úložiště, musíte nejprve připravit server s nezbytnými předpoklady:

* Na serveru musí být spuštěna podporovaná verze systému Windows Server. Další informace najdete v [tématu požadavky na systém Azure File Sync a interoperabilitu](storage-sync-files-planning.md#windows-file-server-considerations).
* Ujistěte se, že byla nasazena služba synchronizace úložiště. Další informace o nasazení služby synchronizace úložiště najdete v tématu [Jak nasadit Azure File Sync](storage-sync-files-deployment-guide.md).
* Ujistěte se, že server je připojený k internetu a že Azure je přístupný.
* Zakažte konfiguraci rozšířeného zabezpečení aplikace IE pro správce pomocí ui Správce serveru.
    
    ![UI správce serveru se zvýrazněnou konfigurací rozšířeného zabezpečení aplikace IE](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Ujistěte se, že je na vašem serveru nainstalovaný modul Azure PowerShell. Pokud je váš server členem clusteru s podporou převzetí služeb při selhání, bude každý uzel v clusteru vyžadovat modul Az. Další podrobnosti o instalaci modulu Az najdete na [instalaci a konfiguraci Prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

    > [!Note]  
    > Doporučujeme použít nejnovější verzi modulu Az PowerShell k registraci/zrušení registrace serveru. Pokud byl balíček Az dříve nainstalován na tomto serveru (a verze prostředí PowerShell na `Update-Module` tomto serveru je 5.* nebo vyšší), můžete tento balíček aktualizovat pomocí rutiny. 
* Pokud ve svém prostředí využíváte server síťového proxy serveru, nakonfigurujte nastavení serveru proxy, aby jej mohl agent synchronizace využívat.
    1. Určení ip adresy proxy a čísla portu
    2. Upravte tyto dva soubory:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Přidejte řádky na obrázku 1 (pod touto částí) pod /System.ServiceModel ve výše uvedených dvou souborech, které mění 127.0.0.1:8888 na správnou adresu IP (nahradit 127.0.0.1) a správné číslo portu (nahradit 8888):
    4. Nastavení proxy serveru WinHTTP nastavte pomocí příkazového řádku:
        * Zobrazit proxy: netsh winhttp zobrazit proxy
        * Nastavit proxy server: netsh winhttp nastavit proxy 127.0.0.1:8888
        * Reset proxy: netsh winhttp reset proxy
        * Pokud se jedná o nastavení po instalaci agenta, restartujte našeho agenta synchronizace: net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Registrace serveru pomocí služby Synchronizace úložiště
Před tím, než bude možné server použít jako *koncový bod serveru* ve skupině *synchronizace synchronizace*souborů Azure , musí být zaregistrován u *služby synchronizace úložiště*. Server lze zaregistrovat pouze s jednou službou synchronizace úložiště najednou.

#### <a name="install-the-azure-file-sync-agent"></a>Instalace agenta Synchronizace souborů Azure
1. [Stáhněte si agenta Azure File Sync](https://go.microsoft.com/fwlink/?linkid=858257).
2. Spusťte instalační program agenta synchronizace souborů Azure.
    
    ![První podokno instalačního programu agenta synchronizace souborů Azure](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Nezapomeňte povolit aktualizace agenta Azure File Sync pomocí služby Microsoft Update. Je to důležité, protože důležité opravy zabezpečení a vylepšení funkcí balíčku serveru jsou dodávány prostřednictvím služby Microsoft Update.

    ![Ujistěte se, že je microsoft update povolen v podokně Microsoft Update instalačního programu agenta synchronizace souborů Azure](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Pokud server nebyl dříve zaregistrován, zobrazí se ihned po dokončení instalace vyskakovací okno s registračním uznatým

> [!Important]  
> Pokud je server členem clusteru s podporou převzetí služeb při selhání, musí být agent Azure File Sync nainstalován na každém uzlu v clusteru.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Registrace serveru pomocí hlavního nastavení pro registraci serveru
> [!Important]  
> Předplatná zprostředkovatele cloudových řešení (CSP) nemohou používat registraci na serveru. Místo toho použijte PowerShell (pod touto částí).

1. Pokud se nastavení registrace serveru nespustilo ihned po dokončení instalace agenta Azure File `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`Sync, lze jej spustit ručně spuštěním .
2. Kliknutím na *Přihlásit* se zobrazíte pro přístup k předplatnému Azure. 

    ![Otevření dialogového okna registračního uzla serveru](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. V dialogovém okně vyberte správné předplatné, skupinu prostředků a službu synchronizace úložiště.

    ![Informace o službě Synchronizace úložiště](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. Ve verzi Preview je k dokončení procesu potřeba další přihlášení. 

    ![Dialogové okno Přihlásit se](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Pokud je server členem clusteru s podporou převzetí služeb při selhání, musí každý server spustit registraci serveru. Když zobrazíte registrované servery na webu Azure Portal, Azure File Sync automaticky rozpozná každý uzel jako člena stejného clusteru s podporou převzetí služeb při selhání a odpovídajícím způsobem je seskupí.

#### <a name="register-the-server-with-powershell"></a>Registrace serveru pomocí Prostředí PowerShell
Registraci serveru můžete provést také prostřednictvím prostředí PowerShell. Toto je jediný podporovaný způsob registrace serveru pro odběry zprostředkovatele cloudových řešení (CSP):

```powershell
Register-AzStorageSyncServer -ResourceGroupName "<your-resource-group-name>" -StorageSyncServiceName "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Zrušení registrace serveru pomocí služby Synchronizace úložiště
Existuje několik kroků, které jsou nutné k zrušení registrace serveru pomocí služby synchronizace úložiště. Podívejme se na to, jak správně zrušit registraci serveru.

> [!Warning]  
> Nepokoušejte se řešit problémy se synchronizací, vrstvením cloudu nebo jakýmkoli jiným aspektem synchronizace souborů Azure zrušením registrace a registrace serveru nebo odebráním a opětovným vytvořením koncových bodů serveru, pokud to není výslovně nařízeno technikem společnosti Microsoft. Zrušení registrace serveru a odebrání koncových bodů serveru je destruktivní operace a vrstvené soubory na svazcích s koncovými body serveru nebudou "znovu připojeny" k jejich umístění ve sdílené složce Azure poté, co jsou registrované koncové body serveru a serveru znovu, což bude mít za následek chyby synchronizace. Všimněte si také, vrstvené soubory, které existují mimo obor názvů koncového bodu serveru může být trvale ztracena. Vrstvené soubory mohou existovat v rámci koncových bodů serveru i v případě, že vrstvení cloudu nebylo nikdy povoleno.

#### <a name="optional-recall-all-tiered-data"></a>(Nepovinné) Odvolat všechna vrstvená data
Pokud chcete, aby soubory, které jsou aktuálně vrstvené, byly dostupné po odebrání Azure File Sync (tj. toto je produkční, nikoli testovací prostředí), navolejte všechny soubory na každém svazku obsahujícím koncové body serveru. Zakažte vrstvení cloudu pro všechny koncové body serveru a spusťte následující rutinu prostředí PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Pokud místní svazek hostující koncový bod serveru nemá dostatek volného místa pro `Invoke-StorageSyncFileRecall` vyvolání všech vrstvených dat, rutina se nezdaří.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Odebrání serveru ze všech skupin synchronizace
Před zrušením registrace serveru ve službě Synchronizace úložiště je nutné odebrat všechny koncové body serveru na tomto serveru. To lze provést prostřednictvím portálu Azure:

1. Přejděte do služby Synchronizace úložiště, kde je váš server registrován.
2. Odeberte všechny koncové body serveru pro tento server v každé skupině synchronizace ve službě Synchronizace úložiště. Toho lze dosáhnout kliknutím pravým tlačítkem myši na příslušný koncový bod serveru v podokně skupiny synchronizace.

    ![Odebrání koncového bodu serveru ze skupiny synchronizace](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Toho lze provést také pomocí jednoduchého skriptu prostředí PowerShell:

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
Nyní, když byla všechna data odvolána a server byl odebrán ze všech skupin synchronizace, může být server neregistrovaný. 

1. Na webu Azure Portal přejděte do části *Registrované servery* ve službě Synchronizace úložiště.
2. Klikněte pravým tlačítkem myši na server, který chcete zrušit, a klikněte na tlačítko "Zrušit registraci serveru".

    ![Zrušení registrace serveru](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Zajištění Azure File Sync je dobrý soused ve vašem datovém centru 
Vzhledem k tomu, že Azure File Sync bude zřídka jedinou službou spuštěnou ve vašem datovém centru, můžete omezit využití sítě a úložiště Azure File Sync.

> [!Important]  
> Příliš nízké nastavení limitů ovlivní výkon synchronizace a odvolání synchronizace a odvolání synchronizace souborů Azure.

### <a name="set-azure-file-sync-network-limits"></a>Nastavení síťových limitů azure synchronizace souborů
Využití azure file sync pomocí `StorageSyncNetworkLimit` rutin y můžete omezit využití sítě.

> [!Note]  
> Omezení sítě se nevztahují na přístup k vrstvené souboru nebo invoke-StorageSyncFileRecall rutina se používá.

Můžete například vytvořit nový limit omezení, který zajistí, že Azure File Sync během pracovního týdne nepoužije více než 10 Mb/s mezi 9:00 a 17:00: 

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

Svůj limit můžete zobrazit pomocí následující rutiny:

```powershell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Chcete-li odebrat `Remove-StorageSyncNetworkLimit`omezení sítě, použijte . Například následující příkaz odebere všechna omezení sítě:

```powershell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Použití qoS úložiště systému Windows Server 
Když je Azure File Sync hostovaná ve virtuálním počítači běžícím na hostiteli virtualizace Windows Serveru, můžete k regulaci spotřeby videa úložiště použít Storage QoS (kvalita úložiště služby). Zásady QoS úložiště lze nastavit buď jako maximum (nebo limit, například jak storageSyncNetwork limit je vynuceno výše) nebo jako minimum (nebo rezervace). Nastavení minima namísto maxima umožňuje Azure File Sync shlukovat k použití dostupné šířky pásma úložiště, pokud ji jiné úlohy nepoužívají. Další informace naleznete v [tématu Storage Quality of Service](https://docs.microsoft.com/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>Viz také
- [Plánování nasazení Synchronizace souborů Azure](storage-sync-files-planning.md)
- [Nasazení Synchronizace souborů Azure](storage-sync-files-deployment-guide.md)
- [Monitorování Synchronizace souborů Azure](storage-sync-files-monitoring.md)
- [Řešení problémů se Synchronizací souborů Azure](storage-sync-files-troubleshoot.md)

---
title: Správa registrovaných serverů pomocí služby Azure File Sync | Dokumentace Microsoftu
description: Zjistěte, jak vytvářet a rušit registraci serveru systému Windows pomocí služby synchronizace úložiště Azure File Sync.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: cec6da78ae47b509e2bb5f8ba0007208545062e7
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478062"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Správa registrovaných serverů pomocí služby Azure File Sync
Synchronizace souborů Azure umožňuje centralizovat sdílené složky organizace ve službě Soubory Azure bez ztráty flexibility, výkonu a kompatibility místního souborového serveru. Dělá to pomocí transformace serverů Windows na rychlou mezipaměť sdílené složky Azure. Pro místní přístup k datům můžete použít jakýkoli protokol dostupný ve Windows Serveru (včetně SMB, NFS a FTPS) a můžete mít libovolný počet mezipamětí po celém světě.

V následujícím článku ukazuje, jak se zaregistrovat a spravovat server se služba synchronizace úložiště. Zobrazit [postup nasazení služby Azure File Sync](storage-sync-files-deployment-guide.md) informace o tom, jak nasadit Azure File Sync end-to-end.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Registrace nebo zrušení registrace serveru se služba synchronizace úložiště
Registrace serveru pomocí služby Azure File Sync vytváří vztah důvěryhodnosti mezi Windows serverem a Azure. Tento vztah, můžete pak použije k vytvoření *koncové body serveru* na serveru, které představují určitých složek, které by se měla synchronizovat s sdílené složky Azure (označované také jako *koncový bod v cloudu*). 

### <a name="prerequisites"></a>Požadavky
Registrace serveru se služba synchronizace úložiště, musíte nejprve připravit server nezbytné požadavky:

* Server musí běžet podporovaná verze systému Windows Server. Další informace najdete v tématu [požadavky na systém Azure File Sync a vzájemná funkční spolupráce](storage-sync-files-planning.md#azure-file-sync-system-requirements-and-interoperability).
* Ujistěte se, že byla nasazena služba synchronizace úložiště. Další informace o tom, jak nasadit služby synchronizace úložiště najdete v tématu [postup nasazení služby Azure File Sync](storage-sync-files-deployment-guide.md).
* Ujistěte se, že je server připojen k Internetu a že Azure je dostupný.
* Zakážete konfigurace rozšířeného zabezpečení pro správce, které díky uživatelskému rozhraní správce serveru.
    
    ![Uživatelské rozhraní správce serveru se konfigurace rozšířeného zabezpečení Internet Exploreru zvýrazněnou](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Ujistěte se, že je modul Azure PowerShell nainstalovali na server. Pokud je server členem clusteru převzetí služeb při selhání, každý uzel v clusteru, bude vyžadovat Az modulu. Další podrobnosti o tom, jak nainstalovat modul Az můžete najít na [instalace a konfigurace Azure Powershellu](https://docs.microsoft.com/powershell/azure/install-Az-ps).

    > [!Note]  
    > Doporučujeme používat nejnovější verze modulu Az PowerShell zaregistrovat nebo zrušit registraci serveru. Pokud balíček Az byly dříve nainstalovány na tomto serveru (a verze prostředí PowerShell na tomto serveru je 5.* nebo vyšší), můžete použít `Update-Module` rutina pro aktualizaci tohoto balíčku. 
* Pokud používáte síť proxy server ve vašem prostředí, konfigurace nastavení proxy serveru na serveru pro agenta synchronizace, aby se začala používat.
    1. Určit proxy IP adresu a číslo portu
    2. Upravte tyto dva soubory:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Přidáním řádků na obrázku 1 (pod v této části) v části /System.ServiceModel ve výše uvedených dvou souborech změna 127.0.0.1:8888 správnou IP adresu (nahradit 127.0.0.1) a správné číslo portu (nahradit 8888):
    4. Nastavení konfigurace proxy serveru WinHTTP pomocí příkazového řádku:
        * Zobrazit proxy serveru: netsh winhttp zobrazit proxy
        * Nastavení proxy serveru: netsh winhttp nastavit proxy 127.0.0.1:8888
        * Resetovat proxy serveru: netsh winhttp resetování proxy
        * Pokud je toto nastavení po instalaci agenta, restartujte našeho agenta synchronizace: filesyncsvc net stop
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Registrace serveru pomocí služby synchronizace úložiště
Předtím, než server může sloužit jako *koncový bod serveru* do Azure File Sync *skupinu synchronizace*, musí zaregistrovat *služba synchronizace úložiště*. Server lze registrovat pouze v jediné službě synchronizace úložiště po jednom.

#### <a name="install-the-azure-file-sync-agent"></a>Instalace agenta Synchronizace souborů Azure
1. [Stažení agenta Azure File Sync](https://go.microsoft.com/fwlink/?linkid=858257).
2. Spusťte instalační program agenta Azure File Sync.
    
    ![První podokně instalační program agenta Azure File Sync](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Ujistěte se, aby povolovala aktualizace pro agenta Azure File Sync pomocí služby Microsoft Update. Je důležité, protože kritické bezpečnostní opravy a vylepšení funkcí na server balíček se dodávají přes Microsoft Update.

    ![Ujistěte se, že služba Microsoft Update povolená na panelu Microsoft Update instalačního programu agenta Azure File Sync](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Pokud dříve nebyla zaregistrována na serveru, registrace serveru uživatelského rozhraní objeví ihned po dokončení instalace.

> [!Important]  
> Pokud je server členem clusteru převzetí služeb při selhání, agenta Azure File Sync musí být nainstalovaný na všech uzlech v clusteru.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Registrace serveru pomocí uživatelského rozhraní registrace serveru
> [!Important]  
> Registrace serveru uživatelského rozhraní nelze použít předplatná cloud Solution Provider (CSP). Místo toho použijte PowerShell (níže v této části).

1. Pokud registrace serveru uživatelského rozhraní se nepodařilo spustit ihned po dokončení instalace agenta Azure File Sync, ho můžete spustit ručně spuštěním `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`.
2. Klikněte na tlačítko *přihlášení* pro přístup k vašemu předplatnému Azure. 

    ![Otevření dialogového okna uživatelského rozhraní serveru zápisu](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Vyberte správné předplatné, skupinu prostředků a služba synchronizace úložiště z tohoto dialogového okna.

    ![Informace o službě synchronizace úložiště](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. Ve verzi preview jeden další přihlášení je předpokladem pro dokončení procesu. 

    ![Přihlaste se dialogové okno](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Pokud je server členem clusteru převzetí služeb při selhání, každý server potřebuje ke spuštění registrace serveru. Když zobrazujete registrované servery na webu Azure Portal, Azure File Sync automaticky rozpozná každý uzel jako členem stejného clusteru převzetí služeb při selhání a seskupuje je odpovídajícím způsobem.

#### <a name="register-the-server-with-powershell"></a>Registrace serveru pomocí Powershellu
Můžete také provést registraci serveru přes PowerShell. Toto je jediným podporovaným způsobem registrace serveru pro předplatná Cloud Solution Provider (CSP):

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<your-subscription-id>" -TenantID "<your-tenant-id>"
Register-AzureRmStorageSyncServer -SubscriptionId "<your-subscription-id>" - ResourceGroupName "<your-resource-group-name>" - StorageSyncService "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Zrušení registrace serveru se služba synchronizace úložiště
Existuje několik kroků, které jsou nutné pro zrušení registrace serveru se služba synchronizace úložiště. Pojďme se podívat, jak správně zrušení registrace serveru.

> [!Warning]  
> Nepokoušejte se řešení potíží s synchronizace, vrstvení cloudu nebo jiného aspektu Azure File Sync v rušení registrace a registraci serveru, nebo odebrat a znovu vytvořit koncové body serveru neurčí explicitně do Microsoftu. Zrušení registrace serveru a odebírá koncové body serveru je destruktivní operace a vrstvené soubory na svazcích s koncové body serveru nebude je "připojit" k jejich umístění na sdílené složky Azure po registrovaném serveru a koncové body serveru znovu vytvoří, se budou účtovat synchronizované chyby. Všimněte si také, vrstvené soubory, které existují mimo obor názvů koncový bod serveru může být trvale ztratí. Můžou existovat vrstvené soubory v rámci serveru koncových bodů i v případě vrstvení cloudu nikdy nepovolilo.

#### <a name="optional-recall-all-tiered-data"></a>(Volitelné) Odvolat všechny Vrstvená data
Pokud chcete soubory, které jsou aktuálně k dispozici po odebrání Azure File Sync (to znamená Toto je výrobní, ne testu, prostředí) vrstvený, svolat všechny soubory na každém svazku, který obsahuje koncové body serveru. Zakázat pro všechny koncové body serveru vrstvení cloudu a potom spusťte následující rutinu Powershellu:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Pokud místní svazek, který je hostitelem koncového bodu serveru nemá dostatek volného místa pro odvolání všechna Vrstvená data `Invoke-StorageSyncFileRecall` rutina selže.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Odebrání serveru ze všech skupin synchronizace
Před zrušením registrace serveru na službu synchronizace úložiště, musíte odebrat všechny koncové body serveru na tomto serveru. To lze provést prostřednictvím webu Azure portal:

1. Přejděte do služby synchronizace úložiště, ve kterém je váš server zaregistrovaný.
2. Odeberte všechny koncové body serveru pro tento server v každé skupině synchronizace ve službě synchronizace úložiště. To můžete provést kliknutím pravým tlačítkem myši na příslušný server koncového bodu v podokně skupiny synchronizace.

    ![Odebrání koncového bodu serveru ze skupiny synchronizace](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

To můžete provést také pomocí jednoduchý skript prostředí PowerShell:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"

$accountInfo = Connect-AzAccount
Login-AzStorageSync -SubscriptionId $accountInfo.Context.Subscription.Id -TenantId $accountInfo.Context.Tenant.Id -ResourceGroupName "<your-resource-group>"

$StorageSyncService = "<your-storage-sync-service>"

Get-AzureRmStorageSyncGroup -StorageSyncServiceName $StorageSyncService | ForEach-Object { 
    $SyncGroup = $_; 
    Get-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name | Where-Object { $_.DisplayName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name -ServerEndpointName $_.Name 
    } 
}
```

#### <a name="unregister-the-server"></a>Zrušit registraci serveru
Teď, když byla stažena všechna data a server je odebraná ze všech skupin synchronizace, může se zrušit registraci serveru. 

1. Na webu Azure Portal, přejděte *registrované servery* část služby synchronizace úložiště.
2. Klikněte pravým tlačítkem na server, ke kterému chcete zrušit registraci a klikněte na tlačítko "Zrušit registraci serveru".

    ![Zrušit registraci serveru](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Zajistit, že Azure File Sync je dobrý soused ve vašem datovém centru 
Protože Azure File Sync zřídka bude jedinou službou ve svém datovém centru, můžete omezit využití sítě a úložiště služby Azure File Sync.

> [!Important]  
> Nastavení omezení příliš nízká. bude mít vliv na výkon Azure File Sync synchronizace a odvolání.

### <a name="set-azure-file-sync-network-limits"></a>Nastavení omezení sítě Azure File Sync
Můžete omezit využití sítě služby Azure File Sync s použitím `StorageSyncNetworkLimit` rutiny. 

Například můžete vytvořit nový limit omezení k zajištění, že Azure File Sync nepoužívá více než 10 MB/s mezi 9: 00 a 17: 00 (h 17:00) během pracovního týdne: 

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

Limit můžete zobrazit pomocí následující rutiny:

```PowerShell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Chcete-li odebrat omezení sítě, použijte `Remove-StorageSyncNetworkLimit`. Například následující příkaz odebere všechny síťové omezení:

```PowerShell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Pomocí QoS úložiště Windows serveru 
Když Azure File Sync je hostovaný na virtuálním počítači spuštěná ve virtualizačním hostiteli systému Windows Server, můžete regulovat úložiště využití vstupně-výstupní operace kvality služby úložiště (kvalita služby úložiště). Zásada QoS pro úložiště můžete nastavit jako maximální počet (nebo omezení, třeba jak se bude vynucovat omezení StorageSyncNetwork výše) nebo jako minimální (nebo rezervace). Nastavení minimální místo maximální umožňuje Azure File Sync prudce zvýšit výkon rychlejší využívat šířku pásma úložiště k dispozici, pokud ho nepoužívají jiné úlohy. Další informace najdete v tématu [kvality služby úložiště](https://docs.microsoft.com/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>Další informace najdete v tématech
- [Plánování nasazení služby Azure File Sync](storage-sync-files-planning.md)
- [Nasazení služby Azure File Sync](storage-sync-files-deployment-guide.md)
- [Monitorování Azure File Sync](storage-sync-files-monitoring.md)
- [Řešení potíží s Azure File Sync](storage-sync-files-troubleshoot.md)

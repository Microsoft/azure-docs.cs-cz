---
title: Spravovat registrované servery se synchronizací souboru Azure (preview) | Microsoft Docs
description: Naučte se registrovat a zrušit registraci serveru systému Windows pomocí služby Azure Storage synchronizace souboru synchronizace.
services: storage
documentationcenter: ''
author: wmgries
manager: aungoo
editor: tamram
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: wgries
ms.openlocfilehash: 7385e8b84668facf8bf44f569a611e7dcdba9a1e
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738288"
---
# <a name="manage-registered-servers-with-azure-file-sync-preview"></a>Spravovat registrované servery se synchronizací souboru Azure (preview)
Azure File Sync (Preview) umožňuje centralizovat sdílené složky organizace ve službě Soubory Azure bez ztráty flexibility, výkonu a kompatibility místního souborového serveru. Dělá to pomocí transformace serverech s Windows do rychlé mezipaměti Azure sdílené složky. Pro místní přístup k datům můžete použít jakýkoli protokol dostupný ve Windows Serveru (včetně SMB, NFS a FTPS) a můžete mít libovolný počet mezipamětí po celém světě.

V následujícím článku ukazuje, jak se zaregistrovat a spravovat server se službou Sync úložiště. V tématu [nasazení synchronizace souboru Azure (preview)](storage-sync-files-deployment-guide.md) informace o tom, jak nasadit synchronizace souboru Azure klient server.

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Registrace a zrušení registrace serveru se službou Sync úložiště
Registrace serveru se synchronizací souboru Azure vytváří vztah důvěryhodnosti mezi Windows serverem a Azure. Tento vztah, můžete pak použije k vytvoření *koncové body serveru* na serveru, který reprezentuje určitých složek, které by měl být synchronizovány s sdílenou složku Azure (také označované jako *cloudu koncový bod*). 

### <a name="prerequisites"></a>Požadavky
K registraci serveru se službou Sync úložiště, musíte nejdřív připravit váš server nezbytné požadavky:

* Váš server musí používat podporovanou verzi systému Windows Server. Další informace najdete v tématu [podporované verze systému Windows Server](storage-sync-files-planning.md#supported-versions-of-windows-server).
* Ujistěte se, že byla nasazena synchronizační služby úložiště. Další informace o tom, jak nasadit službu synchronizace úložiště najdete v tématu [nasazení synchronizace souboru Azure (preview)](storage-sync-files-deployment-guide.md).
* Ujistěte se, že je server připojen k Internetu a zda je přístupný Azure.
* Zakažte konfigurace rozšířeného zabezpečení pro správce pomocí uživatelského rozhraní správce serveru.
    
    ![Konfigurace rozšířeného zabezpečení Internet Exploreru zvýrazněná uživatelské rozhraní správce serveru](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Zkontrolujte, zda je na serveru nainstalován modul AzureRM PowerShell. Pokud váš server je členem clusteru s podporou převzetí služeb při selhání, každý uzel v clusteru, bude vyžadovat AzureRM modulu. Další informace o tom, jak nainstalovat modul AzureRM naleznete na [instalace a konfigurace prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

    > [!Note]  
    > Doporučujeme používat nejnovější verze modulu AzureRM PowerShell registrace nebo odregistrace serveru. Pokud balíček AzureRM byl dříve nainstalován na tomto serveru (a verze prostředí PowerShell na tomto serveru je 5.* nebo vyšší), můžete použít `Update-Module` rutiny tento balíček aktualizace. 
* Pokud využíváte proxy serveru sítě ve vašem prostředí, nakonfigurujte nastavení proxy serveru na serveru pro synchronizaci agenta využívat.
    1. Určení vaší proxy IP adresu a číslo portu
    2. Upravte tyto dva soubory:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Přidejte řádky na obrázku 1 (pod této části) v části /System.ServiceModel ve výše uvedených dvou souborech změna 127.0.0.1:8888 na správnou IP adresu (nahraďte 127.0.0.1) a správné číslo portu (nahraďte 8888):
    4. Nastavte nastavení proxy serveru WinHTTP prostřednictvím příkazového řádku:
        * Zobrazit proxy server: Zobrazit netsh winhttp proxy
        * Nastavení proxy serveru: netsh winhttp nastavit proxy 127.0.0.1:8888
        * Resetování proxy server: resetování netsh winhttp proxy
        * Pokud je instalační program po dokončení instalace agenta, potom restartujte naše agenta synchronizace: filesyncsvc net stop
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Registraci serveru se službou Sync úložiště
Před použitím serveru jako *koncový bod serveru* v souboru synchronizace služby Azure *skupiny synchronizace*, musí být zaregistrovaný u *úložiště synchronizační služba*. Server se dají registrovat jenom s jeden synchronizační služby úložiště v čase.

#### <a name="install-the-azure-file-sync-agent"></a>Nainstalujte agenta synchronizace souboru Azure
1. [Stáhnout agenta synchronizace souboru Azure](https://go.microsoft.com/fwlink/?linkid=858257).
2. Spusťte instalační program agenta synchronizace souboru Azure.
    
    ![V první podokně instalační program agenta synchronizace souboru Azure](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Je nutné povolit aktualizace agenta synchronizace souboru Azure pomocí služby Microsoft Update. Je důležité, protože kritické zabezpečení opravy a nové funkce a vylepšení balíčku serveru jsou sice přes Microsoft Update.

    ![Ujistěte se, že služba Microsoft Update je povolena v podokně Microsoft Update instalační program agenta synchronizace souboru Azure](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Pokud server nebyl dříve registrován, registrace serveru uživatelského rozhraní objeví se okamžitě po dokončení instalace.

> [!Important]  
> Pokud je server členem clusteru s podporou převzetí služeb při selhání, agent synchronizace souboru Azure musí být nainstalovaný na všech uzlech v clusteru.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Registrace serveru pomocí registrace serveru uživatelského rozhraní
> [!Important]  
> Cloud Solution Provider (CSP) odběry nelze použít registrace serveru uživatelského rozhraní. Místo toho používejte PowerShell (níže v této části).

1. Pokud registrace serveru uživatelského rozhraní se nepodařilo spustit okamžitě po dokončení instalace agenta Azure souboru Sync, ho můžete spustit ručně spuštěním `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`.
2. Klikněte na tlačítko *přihlášení* pro přístup k předplatnému Azure. 

    ![Otevřete dialogové okno registrace serveru uživatelského rozhraní](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Vyberte správné předplatné, skupinu prostředků a úložiště synchronizační služby z tohoto dialogového okna.

    ![Informace o synchronizaci služby úložiště](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. Ve verzi preview jeden další přihlášení je předpokladem pro dokončení procesu. 

    ![Přihlaste se dialogové okno](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Pokud je server členem clusteru s podporou převzetí služeb při selhání, každý server je potřeba spustit registrací serveru. Při zobrazení registrované servery na portálu Azure, Azure souboru Sync automaticky rozpozná každý uzel jako členem stejného clusteru převzetí služeb při selhání a skupin je společně správně.

#### <a name="register-the-server-with-powershell"></a>Registrace serveru pomocí prostředí PowerShell
Můžete také provést registraci serveru pomocí prostředí PowerShell. Toto je jediný podporovaný způsob, jak registrace serveru pro předplatná Cloud Solution Provider (CSP):

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<your-subscription-id>" -TenantID "<your-tenant-id>"
Register-AzureRmStorageSyncServer -SubscriptionId "<your-subscription-id>" - ResourceGroupName "<your-resource-group-name>" - StorageSyncService "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Zrušte registraci serveru se službou Sync úložiště
Existuje několik kroků, potřebné ke zrušení registrace serveru se službou Sync úložiště. Podívejme se na to, jak se správně zrušit registraci serveru.

> [!Warning]  
> Nepokoušejte se vyřešit problémy s synchronizace, cloudu vrstvení nebo jiných aspektů synchronizace souboru Azure zrušení registrace a registraci serveru, nebo odebráním a znovu vytvořit koncové body serveru, pokud není výslovně pokyn k pracovníkem společnosti Microsoft. Zrušení registrace serveru a odebírání koncové body serveru je destruktivní operace a vrstvené souborů na svazcích s koncovými body serveru nebude "zopakovat" na jejich umístění na sdílenou složkou Azure po zaregistrovaný server a server koncové body znovu vytvořen, což způsobí synchronizované chyby. Všimněte si také, vrstvené souborů, které existují mimo obor názvů koncový bod serveru může být trvale ztratí. Vrstvené soubory mohou být v rámci serveru koncových bodů, i když cloudové vrstvení nikdy povoleno.

#### <a name="optional-recall-all-tiered-data"></a>(Volitelné) Odvolat vrstvené všechna data
Pokud chcete soubory, které jsou aktuálně vrstvené být k dispozici po odebrání synchronizace souboru Azure (tj. Toto je provozní, ne test, prostředí), odvolat všechny soubory na každý svazek obsahující koncové body serveru. Zakázat cloudu vrstvení pro všechny koncové body serveru a pak spusťte následující rutinu Powershellu:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Pokud místní svazek hostování koncový bod serveru nemá dostatek volného místa pro navrácení všech vrstvené souborů `Invoke-StorageSyncFileRecall` rutiny se nezdaří.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Odeberte tento server ze všech skupin synchronizace
Před zrušení registrace serveru na synchronizační služby úložiště, je třeba odebrat všechny koncové body serveru na tomto serveru. To lze provést prostřednictvím portálu Azure:

1. Přejděte do služby Sync úložiště, kde je server zaregistrován.
2. Odeberte všechny koncové body serveru pro tento server v každé skupině synchronizace v úložišti služby synchronizace. To můžete provést kliknutím pravým tlačítkem na příslušný server koncového bodu v podokně synchronizace skupiny.

    ![Odebrání koncového bodu serveru ze skupiny synchronizace](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

To můžete udělat taky pomocí jednoduchého skriptu prostředí PowerShell:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"

$accountInfo = Connect-AzureRmAccount
Login-AzureRmStorageSync -SubscriptionId $accountInfo.Context.Subscription.Id -TenantId $accountInfo.Context.Tenant.Id -ResourceGroupName "<your-resource-group>"

$StorageSyncService = "<your-storage-sync-service>"

Get-AzureRmStorageSyncGroup -StorageSyncServiceName $StorageSyncService | ForEach-Object { 
    $SyncGroup = $_; 
    Get-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name | Where-Object { $_.DisplayName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name -ServerEndpointName $_.Name 
    } 
}
```

#### <a name="unregister-the-server"></a>Zrušte registraci serveru
Teď, když byl odvolán všechna data a serveru byla odebrána ze všech skupin, synchronizace, může se zrušit registraci serveru. 

1. Na portálu Azure přejděte do *registrované servery* části synchronizační služby úložiště.
2. Klikněte pravým tlačítkem myši na serveru, kterou chcete zrušit registraci a klikněte na tlačítko "Zrušit registraci serveru".

    ![Zrušit registraci serveru](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Zajistit synchronizace souboru Azure je dobré sousedním ve vašem datovém centru 
Vzhledem k tomu, že synchronizace souboru Azure zřídka bude pouze služba spuštěna ve vašem datovém centru, můžete omezit využití sítě a úložiště synchronizace souborů Azure.

> [!Important]  
> Nastavení příliš nízké omezení bude mít dopad na výkon synchronizace synchronizace souboru Azure a odvolání.

### <a name="set-azure-file-sync-network-limits"></a>Nastavení limitů sítě Azure synchronizace souboru
Můžete omezit využití sítě synchronizace souborů Azure pomocí `StorageSyncNetworkLimit` rutiny. 

Například můžete vytvořit nový limit omezovače zajistit, že synchronizace souboru Azure nepoužívá víc než 10 MB/s mezi 9: 00 a 17: 00 (h 17:00) během pracovní dny: 

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

Limit můžete zobrazit pomocí následující rutiny:

```PowerShell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

K odebrání omezení sítě, použijte `Remove-StorageSyncNetworkLimit`. Například následující příkaz odebere všechna omezení sítě:

```PowerShell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Používání úložiště systému Windows Server technologie QoS 
Při synchronizaci souboru Azure je hostovaná ve virtuální počítač běží na hostiteli virtualizace systému Windows Server, můžete regulovat úložiště vstupně-výstupní operace spotřeba úložiště QoS (quality of service úložiště). Zásada QoS úložiště můžete nastavit jako maximální počet (nebo omezení, jako je jak StorageSyncNetwork limit vynucený výše), nebo jako minimální (nebo rezervace). Nastavení minimální místo maximální umožňuje Azure souboru Sync k rozšíření do využití šířky pásma úložiště k dispozici, pokud další pracovní úlohy nepoužívají. Další informace najdete v tématu [úložiště Quality of Service](https://docs.microsoft.com/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>Další informace najdete v tématech
- [Plánování nasazení synchronizace souboru Azure (preview)](storage-sync-files-planning.md)
- [Nasadit soubor synchronizaci Azure (preview)](storage-sync-files-deployment-guide.md) 
- [Řešení potíží s synchronizace souboru Azure (preview)](storage-sync-files-troubleshoot.md)

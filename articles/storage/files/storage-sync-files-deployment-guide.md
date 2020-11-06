---
title: Nasadit Azure File Sync | Microsoft Docs
description: Naučte se, jak nasadit Azure File Sync, od začátku do konce, pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/05/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 128a974c41b1c09196ecab2070136d9568b08f5d
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331783"
---
# <a name="deploy-azure-file-sync"></a>Nasazení Synchronizace souborů Azure
Pomocí Azure File Sync můžete centralizovat sdílené složky ve vaší organizaci ve službě soubory Azure a zároveň udržet flexibilitu, výkon a kompatibilitu místního souborového serveru. Synchronizace souborů Azure transformuje Windows Server na rychlou mezipaměť sdílené složky Azure. Pro místní přístup k datům můžete použít jakýkoli protokol dostupný ve Windows Serveru, včetně SMB, NFS a FTPS. Můžete mít tolik mezipamětí, kolik potřebujete po celém světě.

Před dokončením kroků popsaných v tomto článku důrazně doporučujeme, abyste si přečetli [Plánování nasazení souborů Azure](storage-files-planning.md) a [Plánování nasazení Azure File Sync](storage-sync-files-planning.md) .

## <a name="prerequisites"></a>Požadavky

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Sdílená složka Azure ve stejné oblasti, kterou chcete nasadit Azure File Sync. Další informace najdete v tématech:
    - [Dostupnost oblasti](storage-sync-files-planning.md#azure-file-sync-region-availability) pro Azure File Sync.
    - [Vytvořte sdílenou složku](storage-how-to-create-file-share.md) , kde najdete podrobný popis postupu vytvoření sdílené složky.
1. Minimálně jedna podporovaná instance clusteru Windows serveru nebo Windows serveru pro synchronizaci s Azure File Sync. Další informace o podporovaných verzích systému Windows Server a doporučených systémových prostředcích najdete v tématu [požadavky na souborový server systému Windows](storage-sync-files-planning.md#windows-file-server-considerations).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Sdílená složka Azure ve stejné oblasti, kterou chcete nasadit Azure File Sync. Další informace najdete v tématech:
    - [Dostupnost oblasti](storage-sync-files-planning.md#azure-file-sync-region-availability) pro Azure File Sync.
    - [Vytvořte sdílenou složku](storage-how-to-create-file-share.md) , kde najdete podrobný popis postupu vytvoření sdílené složky.
1. Minimálně jedna podporovaná instance clusteru Windows serveru nebo Windows serveru pro synchronizaci s Azure File Sync. Další informace o podporovaných verzích systému Windows Server a doporučených systémových prostředcích najdete v tématu [požadavky na souborový server systému Windows](storage-sync-files-planning.md#windows-file-server-considerations).

1. Modul AZ PowerShell lze použít buď s PowerShellem 5,1, nebo s PowerShellem 6 +. Můžete použít modul AZ PowerShell pro Azure File Sync v jakémkoli podporovaném systému, včetně systémů, které nejsou systémy Windows, ale rutina registrace serveru musí být vždycky spuštěná na instanci Windows serveru, kterou zaregistrujete (dá se udělat přímo nebo prostřednictvím vzdálené komunikace PowerShellu). V systému Windows Server 2012 R2 můžete ověřit, zda používáte alespoň PowerShell 5,1. \* Podívejte se na hodnotu vlastnosti **PSVersion** objektu **$PSVersionTable** :

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Pokud je hodnota **PSVersion** menší než 5,1. \* , stejně jako se jedná o případ s nejnovějšími instalacemi systému Windows Server 2012 R2, můžete snadno upgradovat stažením a instalací [rozhraní Windows Management Framework (WMF) 5,1](https://www.microsoft.com/download/details.aspx?id=54616). Příslušný balíček ke stažení a instalaci pro Windows Server 2012 R2 je **Win 8.1 andw2k12r2-KB \* \* \* \* \* \* \* -x64. msu**. 

    PowerShell 6 + se dá použít s libovolným podporovaným systémem a dá se stáhnout přes jeho [stránku GitHubu](https://github.com/PowerShell/PowerShell#get-powershell). 

    > [!Important]  
    > Pokud plánujete místo registrace přímo z PowerShellu použít uživatelské rozhraní pro registraci serveru, musíte použít PowerShell 5,1.

1. Pokud jste se rozhodli použít PowerShell 5,1, ujistěte se, že je nainstalovaná aspoň .NET 4.7.2. Přečtěte si další informace o [.NET Framework verzích a závislostech](https://docs.microsoft.com/dotnet/framework/migration-guide/versions-and-dependencies) na vašem systému.

    > [!Important]  
    > Pokud instalujete .NET 4.7.2 + na jádro Windows serveru, musíte nainstalovat s `quiet` příznaky a, jinak se `norestart` instalace nezdaří. Například při instalaci rozhraní .NET 4,8 by příkaz vypadal takto:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

1. Modul AZ PowerShell, který se dá nainstalovat, podle pokynů uvedených tady: [instalace a konfigurace Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > Modul AZ. StorageSync se teď nainstaluje automaticky při instalaci modulu AZ PowerShellu.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Sdílená složka Azure ve stejné oblasti, kterou chcete nasadit Azure File Sync. Další informace najdete v tématech:
    - [Dostupnost oblasti](storage-sync-files-planning.md#azure-file-sync-region-availability) pro Azure File Sync.
    - [Vytvořte sdílenou složku](storage-how-to-create-file-share.md) , kde najdete podrobný popis postupu vytvoření sdílené složky.
1. Minimálně jedna podporovaná instance clusteru Windows serveru nebo Windows serveru pro synchronizaci s Azure File Sync. Další informace o podporovaných verzích systému Windows Server a doporučených systémových prostředcích najdete v tématu [požadavky na souborový server systému Windows](storage-sync-files-planning.md#windows-file-server-considerations).

1. [Instalace Azure CLI](/cli/azure/install-azure-cli)

   Pokud budete chtít, můžete k dokončení kroků v tomto kurzu použít taky Azure Cloud Shell.  Azure Cloud Shell je interaktivní prostředí prostředí, které používáte v prohlížeči.  Spusťte Cloud Shell pomocí jedné z následujících metod:

   - Zvolte **Vyzkoušet** v pravém horním rohu bloku kódu. **Zkuste** otevřít Azure Cloud Shell, ale nezkopíruje automaticky kód do Cloud Shell.

   - Otevřete Cloud Shell tak, že na [https://shell.azure.com](https://shell.azure.com)

   - Vyberte tlačítko **Cloud Shell** na řádku nabídek v pravém horním rohu [Azure Portal](https://portal.azure.com)

1. Přihlaste se.

   Pokud používáte místní instalaci rozhraní příkazového řádku, přihlaste se pomocí příkazu [az login](/cli/azure/reference-index#az-login).

   ```azurecli
   az login
   ```

    Dokončete proces ověřování podle kroků zobrazených v terminálu.

1. Nainstalujte rozhraní [AZ. Sync](/cli/azure/ext/storagesync/storagesync) Azure CLI Extension.

   ```azurecli
   az extension add --name storagesync
   ```

   Po instalaci odkazu na rozšíření **storagesync** se zobrazí následující upozornění.

   ```output
   The installed extension 'storagesync' is experimental and not covered by customer support. Please use with discretion.
   ```

---

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Příprava Windows Serveru na použití se Synchronizací souborů Azure
Pro každý server, který máte v úmyslu používat s Azure File Sync, včetně každého uzlu serveru v clusteru s podporou převzetí služeb při selhání, zakažte **konfiguraci rozšířeného zabezpečení aplikace Internet Explorer**. To se vyžaduje jenom při prvotní registraci serveru. Po zaregistrování serveru můžete tuto možnost znovu povolit.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
> [!Note]  
> Tento krok můžete přeskočit, pokud nasazujete Azure File Sync na jádro Windows serveru.

1. Spusťte Správce serveru.
2. Klikněte na **místní server** :  
    ![Místní server na levé straně uživatelského rozhraní Správce serveru](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. V podokně **Vlastnosti** vyberte odkaz **Konfigurace rozšířeného zabezpečení aplikace Internet Explorer**.  
    ![Podokno Konfigurace rozšířeného zabezpečení aplikace Internet Explorer v uživatelském rozhraní Správce serveru](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. V dialogovém okně **Konfigurace rozšířeného zabezpečení aplikace Internet Explorer** vyberte možnost **vypnuto** pro **správce** a **uživatele** :  
    ![Automaticky otevírané okno Konfigurace rozšířeného zabezpečení aplikace Internet Explorer s vybranou možností Vypnuto](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pokud chcete vypnout konfiguraci rozšířeného zabezpečení aplikace Internet Explorer, spusťte z relace PowerShellu se zvýšenými oprávněními následující příkaz:

```powershell
$installType = (Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\").InstallationType

# This step is not required for Server Core
if ($installType -ne "Server Core") {
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Administrators
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Users
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Force Internet Explorer closed, if open. This is required to fully apply the setting.
    # Save any work you have open in the IE browser. This will not affect other browsers,
    # including Microsoft Edge.
    Stop-Process -Name iexplore -ErrorAction SilentlyContinue
}
``` 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Postupujte podle pokynů pro Azure Portal nebo PowerShell.

---

## <a name="deploy-the-storage-sync-service"></a>Nasazení služby synchronizace úložiště 
Nasazení Azure File Sync začíná umístěním prostředku **služby synchronizace úložiště** do skupiny prostředků vybraného předplatného. V případě potřeby doporučujeme zřídit tyto požadavky co nejvíce. Vytvoříte vztah důvěryhodnosti mezi servery a tímto prostředkem a Server lze registrovat pouze do jedné služby synchronizace úložiště. V důsledku toho se doporučuje nasadit tolik služeb synchronizace úložiště, kolik potřebujete k oddělení skupin serverů. Mějte na paměti, že servery z různých služeb synchronizace úložiště se vzájemně nemůžou synchronizovat.

> [!Note]
> Služba synchronizace úložiště dědí přístupová oprávnění z předplatného a skupiny prostředků, do které byla nasazena. Doporučujeme pečlivě zkontrolovat, kdo k němu má přístup. Entity s přístupem pro zápis můžou začít synchronizovat nové sady souborů ze serverů zaregistrovaných do této služby synchronizace úložiště a způsobit, že se data přenášejí do úložiště Azure, ke kterému mají přístup.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
Pokud chcete nasadit službu synchronizace úložiště, přejděte na [Azure Portal](https://portal.azure.com/), klikněte na *vytvořit prostředek* a vyhledejte Azure File Sync. Ve výsledcích hledání vyberte **Azure File Sync** a pak výběrem **vytvořit** otevřete kartu **nasadit synchronizaci úložiště** .

V podokně, které se otevře, zadejte následující informace:

- **Název** : jedinečný název (v jednotlivých oblastech) pro službu synchronizace úložiště.
- **Předplatné** : předplatné, ve kterém chcete vytvořit službu synchronizace úložiště. V závislosti na strategii konfigurace vaší organizace může mít přístup k jednomu nebo několika předplatným. Předplatné Azure je nejzákladnější kontejner pro fakturaci každé cloudové služby (například soubory Azure).
- **Skupina prostředků** : Skupina prostředků je logická skupina prostředků Azure, jako je například účet úložiště nebo služba synchronizace úložiště. Můžete vytvořit novou skupinu prostředků, nebo použít existující skupinu prostředků pro Azure File Sync. (Doporučujeme používat skupiny prostředků jako kontejnery k logické izolaci prostředků pro vaši organizaci, jako je například seskupení zdrojů lidských zdrojů nebo zdrojů pro konkrétní projekt.)
- **Umístění** : oblast, do které chcete nasadit Azure File Sync. V tomto seznamu jsou k dispozici pouze podporované oblasti.

Po dokončení vyberte **vytvořit** a nasaďte službu synchronizace úložiště.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Nahraďte `<Az_Region>` , `<RG_Name>` a `<my_storage_sync_service>` vlastními hodnotami a pak pomocí následujících příkazů vytvořte a nasaďte službu synchronizace úložiště:

```powershell
$hostType = (Get-Host).Name

if ($installType -eq "Server Core" -or $hostType -eq "ServerRemoteHost") {
    Connect-AzAccount -UseDeviceAuthentication
}
else {
    Connect-AzAccount
}

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

$storageSyncName = "<my_storage_sync_service>"
$storageSync = New-AzStorageSyncService -ResourceGroupName $resourceGroup -Name $storageSyncName -Location $region
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Postupujte podle pokynů pro Azure Portal nebo PowerShell.

---

## <a name="install-the-azure-file-sync-agent"></a>Instalace agenta Synchronizace souborů Azure
Agent Synchronizace souborů Azure je balíček ke stažení, který umožňuje synchronizaci Windows Serveru se sdílenou složkou Azure. 

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
Agenta si můžete stáhnout z webu [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Po dokončení stahování poklikejte na balíček MSI a spusťte instalaci agenta Azure File Sync.

> [!Important]  
> Pokud máte v úmyslu použít Azure File Sync s clusterem s podporou převzetí služeb při selhání, musí být agent Azure File Sync nainstalovaný na všech uzlech v clusteru. Aby bylo možné pracovat s Azure File Sync, musí být každý uzel v clusteru zaregistrován.

Doporučujeme, abyste provedli následující akce:
- Pokud chcete zjednodušit řešení potíží a údržbu serveru, ponechte výchozí instalační cestu (C:\Program Files\Azure\StorageSyncAgent).
- Povolte Microsoft Update, aby zůstaly aktuální Azure File Sync. Všechny aktualizace a Agent Azure File Sync, včetně aktualizací funkcí a oprav hotfix, se vyskytují v Microsoft Update. Doporučujeme nainstalovat nejnovější aktualizaci pro Azure File Sync. Další informace najdete v tématu [Azure File Sync Update Policy](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Po dokončení instalace agenta Azure File Sync se automaticky otevře uživatelské rozhraní pro registraci serveru. Před registrací musíte mít službu synchronizace úložiště. informace o tom, jak vytvořit službu synchronizace úložiště, najdete v další části.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Spusťte následující kód PowerShellu ke stažení příslušné verze agenta Azure File Sync pro váš operační systém a nainstalujte ho do svého systému.

> [!Important]  
> Pokud máte v úmyslu použít Azure File Sync s clusterem s podporou převzetí služeb při selhání, musí být agent Azure File Sync nainstalovaný na všech uzlech v clusteru. Aby bylo možné pracovat s Azure File Sync, musí být každý uzel v clusteru zaregistrován.

```powershell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 17763, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2019 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2016 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2012R2 `
        -OutFile "StorageSyncAgent.msi" 
} else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019")
}

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.msi" -Recurse -Force
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Postupujte podle pokynů pro Azure Portal nebo PowerShell.

---

## <a name="register-windows-server-with-storage-sync-service"></a>Registrace Windows Serveru ve službě synchronizace úložiště
Registrací vašeho Windows Serveru ve službě synchronizace úložiště se vytvoří vztah důvěryhodnosti mezi vaším serverem (nebo clusterem) a službou synchronizace úložiště. Každý server je možné zaregistrovat pouze k jedné službě synchronizace úložiště a může se synchronizovat s ostatními servery a sdílenými složkami Azure přidruženými ke stejné službě synchronizace úložiště.

> [!Note]
> Registrace serveru používá vaše přihlašovací údaje Azure k vytvoření důvěryhodného vztahu mezi službou synchronizace úložiště a vaším Windows serverem, ale server vytvoří a použije svou vlastní identitu, která je platná, dokud server zůstane zaregistrovaný a že je platný token sdíleného přístupového podpisu (SAS úložiště). Po zrušení registrace serveru nejde na server vydat nový token SAS, takže odeberete možnost serveru pro přístup ke sdíleným složkám Azure a zastavte synchronizaci.

Správce, který registruje Server, musí být členem role pro správu **vlastníka** nebo **přispěvatele** pro danou službu synchronizace úložiště. Tato možnost se dá nakonfigurovat v části **Access Control (IAM)** v Azure Portal pro službu synchronizace úložiště.

Je taky možné odlišit správce, kteří můžou registrovat servery z těch, které mají povoleno také konfigurovat synchronizaci v rámci služby synchronizace úložiště. V takovém případě byste museli vytvořit vlastní roli, kde můžete uvést seznam správců, kteří mají povoleno pouze registrovat servery, a přidělit vlastní roli následujícím oprávněním:

* "Microsoft. StorageSync/storageSyncServices/registeredServers/Write"
* "Microsoft. StorageSync/storageSyncServices/Read"
* "Microsoft. StorageSync/storageSyncServices/Workflows/Read"
* "Microsoft. StorageSync/storageSyncServices/Workflows/Operations/Read"

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
Uživatelské rozhraní pro registraci serveru by se mělo po instalaci agenta Azure File Sync otevřít automaticky. Pokud ne, můžete ho otevřít ručně z umístění jeho souboru: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Po otevření uživatelského rozhraní registrace serveru vyberte možnost **Přihlásit** se a začněte.

Po přihlášení se zobrazí výzva k zadání následujících informací:

![Snímek obrazovky uživatelského rozhraní pro registraci serveru](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Předplatné Azure** : předplatné, které obsahuje službu synchronizace úložiště (viz [nasazení služby synchronizace úložiště](#deploy-the-storage-sync-service)). 
- **Skupina prostředků** : Skupina prostředků, která obsahuje službu synchronizace úložiště.
- **Služba synchronizace úložiště** : název služby synchronizace úložiště, se kterou chcete zaregistrovat.

Po výběru příslušných informací vyberte **Registrovat** a dokončete registraci serveru. V rámci procesu registrace se zobrazí výzva k dalšímu přihlášení.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Postupujte podle pokynů pro Azure Portal nebo PowerShell.

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Vytvoření skupiny synchronizace a koncového bodu cloudu
Skupina synchronizace definuje topologii synchronizace sady souborů. Koncové body v rámci skupiny synchronizace se mezi sebou synchronizují. Skupina synchronizace musí obsahovat jeden koncový bod cloudu, který představuje sdílenou složku Azure, a jeden nebo několik koncových bodů serveru. Koncový bod serveru představuje cestu na zaregistrovaném serveru. Server může mít koncové body serveru ve více skupinách synchronizace. Můžete vytvořit tolik skupin synchronizace, kolik potřebujete, abyste vhodně popsali požadovanou topologii synchronizace.

Koncový bod cloudu je ukazatel na sdílenou složku Azure. Všechny koncové body serveru budou synchronizovány s koncovým bodem cloudu, což nastaví koncový bod cloudu jako rozbočovač. Účet úložiště pro sdílenou složku Azure se musí nacházet ve stejné oblasti jako služba synchronizace úložiště. Celá sdílená složka Azure bude synchronizována s jednou výjimkou: speciální složka srovnatelná se skrytou složkou "informace o systémovém svazku" na svazku NTFS se zřídí. Tento adresář se nazývá ". SystemShareInformation". Obsahuje důležitá synchronizovaná metadata, která se nesynchronizují s jinými koncovými body. Nepoužívejte ani neodstraňujte IT!

> [!Important]  
> Můžete provádět změny libovolného koncového bodu cloudu nebo koncového bodu serveru ve skupině synchronizace a nechat soubory synchronizované s ostatními koncovými body ve skupině synchronizace. Pokud provedete přímo změnu koncového bodu cloudu (sdílená složka Azure), je třeba nejprve zjistit změny Azure File Sync úlohy zjišťování změn. Úloha detekce změn se iniciuje pro koncový bod cloudu jenom jednou za 24 hodin. Další informace najdete v [nejčastějších dotazech k souborům Azure](storage-files-faq.md#afs-change-detection).

Správce vytvářející koncový bod cloudu musí být členem **vlastníka** role správy pro účet úložiště, který obsahuje sdílenou složku Azure, na kterou koncový bod cloudu odkazuje. To se dá nakonfigurovat v části **Access Control (IAM)** v Azure Portal pro účet úložiště.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
Skupinu synchronizace vytvoříte tak, že v [Azure Portal](https://portal.azure.com/)přejdete do své služby synchronizace úložiště a pak vyberete **+ Skupina synchronizace** :

![Vytvoření nové skupiny synchronizace na webu Azure Portal](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

V podokně, které se otevře, zadejte následující informace pro vytvoření skupiny synchronizace s koncovým bodem cloudu:

- **Název skupiny synchronizace** : název skupiny synchronizace, která se má vytvořit. Tento název musí být jedinečný v rámci služby synchronizace úložiště, ale může to být libovolný název, který vám dává smysl.
- **Předplatné** : předplatné, ve kterém jste nasadili službu synchronizace úložiště v [nasazení služby synchronizace úložiště](#deploy-the-storage-sync-service).
- **Účet úložiště** : Pokud vyberete **Vybrat účet úložiště** , zobrazí se jiné podokno, ve kterém můžete vybrat účet úložiště, který obsahuje sdílenou složku Azure, se kterou chcete synchronizovat.
- **Sdílená složka Azure** : název sdílené složky Azure, se kterou chcete synchronizovat.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Chcete-li vytvořit skupinu synchronizace, spusťte následující prostředí PowerShell. Nezapomeňte nahradit `<my-sync-group>` požadovaným názvem skupiny synchronizace.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

Po úspěšném vytvoření skupiny synchronizace můžete vytvořit koncový bod cloudu. Nezapomeňte nahradit `<my-storage-account>` a `<my-file-share>` za očekávané hodnoty.

```powershell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzStorageSyncCloudEndpoint `
    -Name $fileShare.Name `
    -ParentObject $syncGroup `
    -StorageAccountResourceId $storageAccount.Id `
    -AzureFileShareName $fileShare.Name
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pomocí příkazu [AZ storagesync Sync-Group](/cli/azure/ext/storagesync/storagesync/sync-group#ext-storagesync-az-storagesync-sync-group-create) vytvořte novou skupinu synchronizace.  Chcete-li nastavit výchozí skupinu prostředků pro všechny příkazy rozhraní příkazového řádku, použijte příkaz [AZ Configure](/cli/azure/reference-index#az-configure).

```azurecli
az storagesync sync-group create --resource-group myResourceGroupName \
                                 --name myNewSyncGroupName \
                                 --storage-sync-service myStorageSyncServiceName \
```

Pomocí příkazu [AZ storagesync Sync-Group Cloud-Endpoint](/cli/azure/ext/storagesync/storagesync/sync-group/cloud-endpoint#ext-storagesync-az-storagesync-sync-group-cloud-endpoint-create) vytvořte nový koncový bod cloudu.

```azurecli
az storagesync sync-group cloud-endpoint create --resource-group myResourceGroup \
                                                --storage-sync-service myStorageSyncServiceName \
                                                --sync-group-name mySyncGroupName \
                                                --name myNewCloudEndpointName \
                                                --storage-account mystorageaccountname \
                                                --azure-file-share-name azure-file-share-name
```

---

## <a name="create-a-server-endpoint"></a>Vytvoření koncového bodu serveru
Koncový bod serveru představuje konkrétní umístění na zaregistrovaném serveru, například složku na svazku serveru. Koncový bod serveru musí být cestou na zaregistrovaném serveru (spíše než připojená sdílená složka) a používat vrstvení cloudu, cesta musí být na nesystémovém svazku. Úložiště připojené k síti (NAS) není podporováno.

> [!NOTE]
> Změna cesty nebo písmene jednotky po vytvoření koncového bodu serveru na svazku není podporována. Ujistěte se, že používáte konečnou cestu na zaregistrovaném serveru.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
Pokud chcete přidat koncový bod serveru, přejdete do nově vytvořené skupiny synchronizace a pak vyberete **přidat koncový bod serveru**.

![Přidání nového koncového bodu serveru v podokně skupiny synchronizace](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

V podokně **Přidat koncový bod serveru** zadejte následující informace pro vytvoření koncového bodu serveru:

- **Registrovaný Server** : název serveru nebo clusteru, ve kterém chcete vytvořit koncový bod serveru.
- **Cesta** : cesta k Windows serveru, která se má synchronizovat jako součást skupiny synchronizace.
- **Vrstvení cloudu** : přepínač pro povolení nebo zakázání vrstvení cloudu. Při vrstvení cloudu se nečasto používané nebo dostupné soubory dají navrstvit na soubory Azure.
- **Volné místo na svazku** : množství volného místa, které se má rezervovat na svazku, na kterém je umístěný koncový bod serveru. Pokud je například volné místo na svazku nastavené na 50% na svazku, který má koncový bod serveru, zhruba polovinu množství dat se navrstvená na soubory Azure. Bez ohledu na to, jestli je povolená vrstva cloudu, bude mít vaše sdílená složka Azure vždycky úplnou kopii dat ve skupině synchronizace.
- **Režim počátečního stahování** : Jedná se o volitelný výběr, který začíná na agentovi verze 11, který může být užitečný, když jsou soubory ve sdílené složce Azure, ale ne na serveru. Taková situace může existovat například v případě, že vytvoříte koncový bod serveru pro přidání dalšího serveru firemní pobočky do skupiny synchronizace nebo když dojde k zotavení po havárii serveru, který selhal. Pokud je zapnutá vrstva cloudu, ve výchozím nastavení se jenom odvolá jenom obor názvů, ale ne žádný obsah souboru. To je užitečné, pokud se domníváte, že se spíše požadavky na přístup uživatelů musí rozhodnout, jaký obsah souboru je vrácen na server. Pokud je vrstva cloudu zakázaná, výchozí hodnota je, že se nejprve stáhne obor názvů a pak se soubory odvolají na základě naposledy upraveného časového razítka, dokud nedosáhnete místní kapacity. Režim počátečního stahování ale můžete změnit jenom na obor názvů. Třetí režim se dá použít jenom v případě, že je pro tento koncový bod serveru zakázané vrstvení cloudu. Tento režim zabrání v prvním volání oboru názvů. Soubory se zobrazí jenom na místním serveru, pokud mají možnost je úplně stáhnout. Tento režim je užitečný, pokud například aplikace vyžaduje, aby byly k dispozici úplné soubory a aby nemohly tolerovat vrstvené soubory v oboru názvů.

Pokud chcete přidat koncový bod serveru, vyberte **vytvořit**. Vaše soubory jsou nyní udržovány synchronizované napříč sdílenou složkou Azure a systémem Windows Server. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Spusťte následující příkazy PowerShellu pro vytvoření koncového bodu serveru a nezapomeňte nahradit `<your-server-endpoint-path>` `<your-volume-free-space>` a s požadovanými hodnotami a zkontrolovat volitelná nastavení pro volitelné zásady prvotního stažení.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>
# Optional property. Choose from: [NamespaceOnly] default when cloud tiering is enabled. [NamespaceThenModifiedFiles] default when cloud tiering is disabled. [AvoidTieredFiles] only available when cloud tiering is disabled.
$initialDownloadPolicy = NamespaceOnly

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage `
        -InitialDownloadPolicy $initialDownloadPolicy
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -InitialDownloadPolicy $initialDownloadPolicy
}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pomocí příkazu [AZ storagesync Sync-Group Server-Endpoint](/cli/azure/ext/storagesync/storagesync/sync-group/server-endpoint#ext-storagesync-az-storagesync-sync-group-server-endpoint-create) vytvořte nový koncový bod serveru.

```azurecli
# Create a new sync group server endpoint 
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --name myNewServerEndpointName
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0
                                                 --server-local-path d:\myPath
                                                 --storage-sync-service myStorageSyncServiceNAme
                                                 --sync-group-name mySyncGroupName

# Create a new sync group server endpoint with additional optional parameters
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --storage-sync-service myStorageSyncServiceName \
                                                 --sync-group-name mySyncGroupName \
                                                 --name myNewServerEndpointName \
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0 \
                                                 --server-local-path d:\myPath \
                                                 --cloud-tiering on \
                                                 --volume-free-space-percent 85 \
                                                 --tier-files-older-than-days 15 \
                                                 --initial-download-policy NamespaceOnly [OR] NamespaceThenModifiedFiles [OR] AvoidTieredFiles
                                                 --offline-data-transfer on \
                                                 --offline-data-transfer-share-name myfilesharename \

```

---

## <a name="configure-firewall-and-virtual-network-settings"></a>Konfigurace nastavení brány firewall a virtuální sítě

### <a name="portal"></a>Portál
Pokud chcete službu Azure File Sync nakonfigurovat tak, aby fungovala s nastavením brány firewall a virtuální sítě, udělejte toto:

1. Z Azure Portal přejděte na účet úložiště, který chcete zabezpečit.
1. V nabídce vlevo vyberte tlačítko **brány firewall a virtuální sítě** .
1. Vyberte **vybrané sítě** v části **Povolení přístupu z**.
1. Ujistěte se, že jsou servery IP nebo virtuální síť uvedené v příslušné části.
1. Ujistěte se, že je zaškrtnuté políčko **umožnit důvěryhodným službám Microsoftu přístup k tomuto účtu úložiště** .
1. Vyberte **Uložit** a uložte nastavení.

![Konfigurace nastavení brány firewall a virtuální sítě pro práci se službou Azure File Sync](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Připojování pomocí Azure File Sync
Doporučený postup při zapínání na Azure File Sync pro první s nulovým prostojem při zachování úplné věrnosti souborů a seznamu řízení přístupu (ACL) jsou následující:
 
1. Nasaďte službu synchronizace úložiště.
1. Vytvořte skupinu synchronizace.
1. Nainstalujte agenta Azure File Sync na server s celou sadou dat.
1. Zaregistrujte tento server a vytvořte koncový bod serveru ve sdílené složce. 
1. Umožněte synchronizaci provést úplné odeslání do sdílené složky Azure (koncový bod cloudu).  
1. Po dokončení počátečního nahrávání nainstalujte Azure File Sync agenta na všechny zbývající servery.
1. Na všech zbývajících serverech vytvořte nové sdílené složky.
1. V případě potřeby vytvořte koncové body serveru pro nové sdílené složky se zásadami vytváření cloudových vrstev. (Tento krok vyžaduje, aby bylo k dispozici další úložiště pro počáteční nastavení.)
1. Umožněte Azure File Sync agenta rychlé obnovení celého oboru názvů bez skutečného přenosu dat. Po úplné synchronizaci oboru názvů provede synchronizační modul místo na místním disku na základě zásad cloudové vrstvy pro koncový bod serveru. 
1. Zajistěte, aby se synchronizace dokončila, a podle potřeby otestujte topologii. 
1. Přesměruje uživatele a aplikace do této nové sdílené složky.
1. Volitelně můžete na serverech odstranit všechny duplicitní sdílené složky.
 
Pokud nemáte dodatečné úložiště pro počáteční registraci a chcete se připojit k existujícím sdíleným složkám, můžete předem naplnit data ve sdílených složkách služby soubory Azure. Tento přístup je navržený, pokud a jenom v případě, že můžete přijmout výpadky a naprosto zaručit žádné změny dat ve sdílených složkách na serveru během prvotního procesu připojování. 
 
1. Zajistěte, aby se data na žádném ze serverů během procesu připojování neměnila.
1. Předprodejní sdílené složky Azure s daty serveru pomocí libovolného nástroje pro přenos dat přes protokol SMB. Robocopy například. AzCopy můžete použít i přes REST. Nezapomeňte použít AzCopy s příslušnými přepínači pro zachování časových razítek a atributů seznamů ACL.
1. Vytvořte Azure File Sync topologii s požadovanými koncovými body serveru ukazující na existující sdílené složky.
1. Umožněte proces sladění dokončení synchronizace u všech koncových bodů. 
1. Po dokončení odsouhlasení můžete otevřít sdílené složky pro změny.
 
V současné době má přístup před osazením několik omezení – 
- Změny dat na serveru před tím, než je topologie synchronizace zcela v provozu, můžou způsobit konflikty na koncových bodech serveru.  
- Po vytvoření koncového bodu cloudu Azure File Sync spustí proces zjišťování souborů v cloudu před zahájením počáteční synchronizace. Doba potřebná k dokončení tohoto procesu se liší v závislosti na různých faktorech, jako je rychlost sítě, dostupná šířka pásma a počet souborů a složek. V případě hrubého odhadu ve verzi Preview se proces zjišťování spouští přibližně v 10 souborech za sekundu.  A to i v případě, že předběžné osazení běží rychle, může být celková doba pro plně běžící systém výrazně delší, než se data v cloudu předem dosadí.

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>Samoobslužné obnovení prostřednictvím předchozích verzí a služby VSS (služba Stínová kopie svazku)

> [!IMPORTANT]
> Následující informace lze použít pouze s verzí 9 (nebo vyšší) agenta synchronizace úložiště. Verze nižší než 9 nebudou mít rutiny StorageSyncSelfService.

Předchozí verze je funkce systému Windows, která umožňuje využívat snímky stínové kopie svazku (VSS) na straně serveru k prezentaci obnovitelné verzí souboru klientovi SMB.
To umožňuje účinný scénář, který se běžně označuje jako samoobslužné obnovení, přímo pro informační pracovníky, nikoli v závislosti na obnovení od správce IT.

Snímky a předchozí verze služby Stínová kopie svazku fungují nezávisle na Azure File Sync. Vrstvení cloudu ale musí být nastavené na kompatibilní režim. Na stejném svazku může existovat mnoho koncových bodů serveru Azure File Sync. Je nutné provést následující volání prostředí PowerShell pro každý svazek, který má i jeden koncový bod serveru, kde plánujete nebo používáte vrstvu cloudu.

```powershell
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

Snímky služby STÍNová kopie svazku jsou pořízeny celý svazek. Ve výchozím nastavení může pro daný svazek existovat až 64 snímků. k dispozici je dostatek místa pro uložení snímků. Služba Stínová kopie svazku tuto službu zpracuje automaticky. Výchozí plán snímku provede dva snímky za den, pondělí až pátek. Tento plán se dá nakonfigurovat pomocí naplánované úlohy Windows. Výše uvedená rutina prostředí PowerShell provede dvě věci:
1. Konfiguruje službu Azure File Syncs na zadaném svazku, aby byly kompatibilní s předchozími verzemi, a zaručuje, že soubor může být obnoven z předchozí verze, i když byl v cloudu na serveru vrstvený. 
1. Povoluje výchozí plán VSS. Později se můžete rozhodnout pro pozdější úpravu. 

> [!Note]  
> Pamatujte na to dvě důležité věci:
>- Pokud použijete parametr-Force a služba Stínová kopie svazku je aktuálně povolená, přepíše aktuální plán snímků VSS a nahradí ho výchozím plánem. Před spuštěním rutiny nezapomeňte uložit vlastní konfiguraci.
> - Pokud tuto rutinu používáte na uzlu clusteru, musíte ji taky spustit na všech ostatních uzlech v clusteru. 

Aby bylo možné zjistit, zda je povolena kompatibilita samoobslužného obnovení, můžete spustit následující rutinu.

```powershell
Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

Zobrazí seznam všech svazků na serveru a také počet dnů kompatibilních s vrstvou cloudu pro každý z nich. Toto číslo se automaticky vypočítá na základě maximálního počtu možných snímků na jeden svazek a výchozího plánu snímku. Ve výchozím nastavení se k obnovení ze služby dá použít všechny předchozí verze, které jsou prezentovány informačnímu pracovníkovi. Totéž platí, pokud změníte výchozí plán tak, aby bylo možné provést více snímků.
Pokud ale změníte plán způsobem, který bude mít za následek dostupný snímek na svazku, který je starší než hodnota kompatibilních dnů, nebudou uživatelé moct tento starší snímek (předchozí verze) použít k obnovení z.

> [!Note]
> Povolení samoobslužného obnovení může mít dopad na spotřebu úložiště Azure a vyúčtování. Tento dopad se omezuje na soubory aktuálně vrstvené na serveru. Povolení této funkce zajistí, že je v cloudu dostupná verze souboru, na kterou se dá odkazovat pomocí položky předchozí verze (snímek služby VSS).
>
> Pokud tuto funkci zakážete, spotřeba úložiště Azure se bude zpomalovat, dokud neuplyne kompatibilní dny v okně. Neexistuje žádný způsob, jak tuto funkci zrychlit. 

Výchozí maximální počet snímků služby VSS na svazek (64) a také výchozí plán, který je bude možné provést, má za následek maximálně 45 dní předchozích verzí, které může pracovník s informacemi obnovit v závislosti na tom, kolik snímků VSS můžete na svazku Uložit.

Pokud maximální počet 64 snímků VSS na svazek není správným nastavením, můžete [tuto hodnotu změnit pomocí klíče registru](https://docs.microsoft.com/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies).
Aby se nový limit mohl projevit, musíte rutinu znovu spustit, aby se zajistila kompatibilita předchozí verze na všech dříve povolených svazcích s příznakem-Force, který zabere nový maximální počet snímků VSS na svazek v rámci účtu. Výsledkem bude nově vypočtený počet kompatibilních dnů. Upozorňujeme, že tato změna se projeví jenom u nově vrstvených souborů a přepíše všechna vlastní nastavení plánu VSS, který jste mohli udělat.

<a id="proactive-recall"></a>
## <a name="proactively-recall-new-and-changed-files-from-an-azure-file-share"></a>Proaktivní odvolání nových a změněných souborů ze sdílené složky Azure

S agentem verze 11 se nový režim bude k dispozici na koncovém bodu serveru. Tento režim umožňuje globálně distribuovaným společnostem uchovávat mezipaměť serveru ve vzdálené oblasti předem vyplněnou ještě předtím, než místní uživatelé přistupují k souborům. Pokud je tato možnost povolená na koncovém bodu serveru, tento režim způsobí, že tento server bude navracet soubory, které se vytvořily nebo změnily ve sdílené složce Azure.

### <a name="scenario"></a>Scénář

Globálně distribuovaná společnost obsahuje pobočky v USA a v Indii. V dopoledne (čas USA) si pracovní procesy vytvoří novou složku a nové soubory pro úplně nový projekt a budou na něm fungovat celý den. Azure File Sync bude synchronizovat složku a soubory do sdílené složky Azure (koncový bod cloudu). Informační pracovníci v Indii budou v tomto časovém pásmu pokračovat v práci na projektu. Po doručení do ráno musí místní server Azure File Sync, v Indii, mít tyto nové soubory místně k dispozici, aby tým Indie mohl efektivně pracovat s místní mezipamětí. Povolením tohoto režimu zabráníte, aby byl počáteční přístup k souborům pomalejší z důvodu odvolání na vyžádání a aby server mohl znovu aktivovat soubory ihned po změně nebo vytvoření ve sdílené složce Azure.

> [!IMPORTANT]
> Je důležité si uvědomit, že sledování změn ve sdílené složce Azure, které úzce na serveru, můžou zvýšit váš výstupní provoz a vysílat z Azure. Pokud se soubory, které jsou na serveru znovu navolají, nejsou ve skutečnosti potřeba v místním prostředí, může mít zbytečné odvolání serveru negativní následky. Tento režim použijte, pokud víte, že před vyplněním mezipaměti na serveru s nedávnými změnami v cloudu bude mít pozitivní vliv na uživatele nebo aplikace používající soubory na tomto serveru.

### <a name="enable-a-server-endpoint-to-proactively-recall-what-changed-in-an-azure-file-share"></a>Povolení koncovému bodu serveru proaktivní odvolání toho, co se změnilo ve sdílené složce Azure

# <a name="portal"></a>[Azure Portal](#tab/proactive-portal)

1. V [Azure Portal](https://portal.azure.com/)otevřete službu synchronizace úložiště, vyberte správnou skupinu synchronizace a pak Identifikujte koncový bod serveru, pro který chcete pečlivě sledovat změny ve sdílené složce Azure (koncový bod cloudu).
1. V části vrstvení cloudu vyhledejte téma "stažení sdílené složky Azure". Zobrazí se aktuálně vybraný režim a může ho změnit, aby bylo možné sledovat změny sdílené složky Azure podrobněji a proaktivně je znovu vyvolat na server.

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="Obrázek znázorňující chování stahování sdílené složky Azure pro aktuálně platný koncový bod serveru a tlačítko pro otevření nabídky, která umožňuje ji změnit.":::

# <a name="powershell"></a>[PowerShell](#tab/proactive-powershell)

Vlastnosti koncového bodu serveru můžete upravit v PowerShellu pomocí rutiny [set-AzStorageSyncServerEndpoint](https://docs.microsoft.com/powershell/module/az.storagesync/set-azstoragesyncserverendpoint) .

```powershell
# Optional parameter. Default: "UpdateLocallyCachedFiles", alternative behavior: "DownloadNewAndModifiedFiles"
$recallBehavior = "DownloadNewAndModifiedFiles"

Set-AzStorageSyncServerEndpoint -InputObject <PSServerEndpoint> -LocalCacheMode $recallBehavior
```

---

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migrace nasazení Replikace DFS (DFS-R) do Azure File Sync
Migrace nasazení systému souborů DFS-R na Azure File Sync:

1. Vytvořte skupinu synchronizace, která bude představovat topologii DFS-R, kterou nahrazujete.
2. Na serveru, který má úplnou sadu dat v topologii DFS-R, začněte migrovat. Nainstalujte na tento server Azure File Sync.
3. Zaregistrujte tento server a vytvořte koncový bod serveru pro první server, který se má migrovat. Nepovolujte vrstvení cloudu.
4. Umožněte synchronizaci všech dat do sdílené složky Azure (koncový bod cloudu).
5. Nainstalujte a zaregistrujte agenta Azure File Sync na všech zbývajících serverech DFS-R.
6. Zakáže DFS-R. 
7. Vytvořte koncový bod serveru na každém serveru DFS-R. Nepovolujte vrstvení cloudu.
8. Zajistěte, aby se synchronizace dokončila, a podle potřeby otestujte topologii.
9. Vyřaďte DFS-R.
10. Na libovolném koncovém bodu serveru se teď dá povolit vrstvení cloudu, jak je potřeba.

Další informace najdete v tématu [Azure File Sync interoperability s systém souborů DFS (Distributed File System) (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Další kroky
- [Přidání nebo odebrání koncového bodu serveru Azure File Sync](storage-sync-files-server-endpoint.md)
- [Registrace nebo zrušení registrace serveru s Azure File Sync](storage-sync-files-server-registration.md)
- [Sledování služby Synchronizace souborů Azure](storage-sync-files-monitoring.md)
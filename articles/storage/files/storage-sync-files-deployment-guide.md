---
title: Nasazení synchronizace souborů Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak nasadit Azure File Sync od začátku do konce.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f2c4e762ebf10a5ca2120c13a52750a7781d60b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268064"
---
# <a name="deploy-azure-file-sync"></a>Nasazení Synchronizace souborů Azure
Azure File Sync slouží k centralizaci sdílených složek vaší organizace v souborech Azure a současně zachovávejte flexibilitu, výkon a kompatibilitu místního souborového serveru. Synchronizace souborů Azure transformuje Windows Server na rychlou mezipaměť sdílené složky Azure. K místnímu přístupu k datům můžete použít libovolný protokol, který je k dispozici v systému Windows Server, včetně protokolů SMB, NFS a FTPS. Můžete mít tolik cache, kolik potřebujete po celém světě.

Důrazně doporučujeme, abyste si před dokončením kroků popsaných v tomto článku [přečetli plánování nasazení](storage-files-planning.md) a plánování souborů Azure [pro nasazení Synchronizace souborů Azure.](storage-sync-files-planning.md)

## <a name="prerequisites"></a>Požadavky
* Sdílená složka Azure ve stejné oblasti, ve které chcete nasadit Azure File Sync. Další informace naleznete v tématu:
    - [Dostupnost oblasti](storage-sync-files-planning.md#azure-file-sync-region-availability) pro Azure File Sync.
    - [Vytvořte sdílenou složku](storage-how-to-create-file-share.md) pro podrobný popis, jak vytvořit sdílenou složku.
* Alespoň jedna podporovaná instance clusteru Windows Server nebo Windows Server pro synchronizaci s Azure File Sync. Další informace o podporovaných verzích systému Windows Server naleznete [v tématu Interoperability with Windows Server](storage-sync-files-planning.md#windows-file-server-considerations).
* Modul Az PowerShell lze použít s prostředím PowerShell 5.1 nebo PowerShell 6+. Modul Az PowerShell pro Azure File Sync můžete používat v libovolném podporovaném systému, včetně systémů jiných než Windows, ale rutina registrace serveru musí být vždy spuštěna v instanci Windows Server, kterou registrujete (to lze provést přímo nebo prostřednictvím prostředí PowerShell vzdálené komunikace). V systému Windows Server 2012 R2 můžete ověřit, zda používáte alespoň prostředí PowerShell 5.1. \* při pohledu na hodnotu **vlastnosti PSVersion** **$PSVersionTable** objektu:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Pokud je hodnota PSVersion menší než 5,1. \*, jak tomu bude v případě většiny čerstvých instalací systému Windows Server 2012 R2, můžete snadno upgradovat stažením a instalací [rozhraní Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). Vhodným balíčkem ke stažení a instalaci pro Systém Windows Server 2012 R2 je **\*\*\*\*\*\*\*Win8.1AndW2K12R2-KB -x64.msu**. 

    PowerShell 6+ lze použít s libovolným podporovaným systémem a lze jej stáhnout přes [stránku GitHub](https://github.com/PowerShell/PowerShell#get-powershell). 

    > [!Important]  
    > Pokud máte v úmyslu použít webové nastavení serveru, nikoli registraci přímo z prostředí PowerShell, musíte použít prostředí PowerShell 5.1.

* Pokud jste se rozhodli používat prostředí PowerShell 5.1, ujistěte se, že je nainstalovánalespoň .NET 4.7.2. Další informace o [verzích rozhraní .NET Framework a závislostech](https://docs.microsoft.com/dotnet/framework/migration-guide/versions-and-dependencies) v systému.

    > [!Important]  
    > Pokud instalujete rozhraní .NET 4.7.2+ do `quiet` systému Windows Server Core, je nutné ji nainstalovat pomocí příznaků a, `norestart` jinak se instalace nezdaří. Například při instalaci rozhraní .NET 4.8, příkaz bude vypadat takto:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

* Modul Az PowerShell, který lze nainstalovat podle pokynů zde: [Instalace a konfigurace Azure PowerShellu](https://docs.microsoft.com/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > Modul Az.StorageSync je nyní nainstalován automaticky při instalaci modulu Az PowerShell.

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Příprava Windows Serveru na použití se Synchronizací souborů Azure
U každého serveru, který chcete použít se synchronizací souborů Azure, včetně každého uzlu serveru v clusteru s podporou převzetí služeb při selhání, zakažte **konfiguraci rozšířeného zabezpečení aplikace Internet Explorer**. To je vyžadováno pouze pro počáteční registraci serveru. Po zaregistrování serveru můžete tuto možnost znovu povolit.

# <a name="portal"></a>[Portál](#tab/azure-portal)
> [!Note]  
> Tento krok můžete přeskočit, pokud nasazujete Azure File Sync na Windows Server Core.

1. Spusťte Správce serveru.
2. Klikněte na **místní server**:  
    ![Místní server na levé straně uživatelského rozhraní Správce serveru](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. V podokně **Vlastnosti** vyberte odkaz **Konfigurace rozšířeného zabezpečení aplikace Internet Explorer**.  
    ![Podokno Konfigurace rozšířeného zabezpečení aplikace Internet Explorer v uživatelském rozhraní Správce serveru](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. V dialogovém okně **Konfigurace rozšířeného zabezpečení aplikace Internet Explorer** vyberte možnost **Vypnuto** pro **správce** a **uživatele**:  
    ![Automaticky otevírané okno Konfigurace rozšířeného zabezpečení aplikace Internet Explorer s vybranou možností Vypnuto](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Chcete-li zakázat konfiguraci rozšířeného zabezpečení aplikace Internet Explorer, proveďte následující kroky z relace prostředí PowerShell se zvýšenými oprávněními:

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

---

## <a name="deploy-the-storage-sync-service"></a>Nasazení služby synchronizace úložiště 
Nasazení Azure File Sync začíná umístěním prostředku **služby synchronizace úložiště** do skupiny prostředků vybraného předplatného. Doporučujeme zřizování tak málo z nich podle potřeby. Vytvoříte vztah důvěryhodnosti mezi servery a tímto prostředkem a server lze zaregistrovat pouze na jednu službu synchronizace úložiště. V důsledku toho se doporučuje nasadit tolik synchronizačních služeb úložiště, kolik potřebujete k oddělení skupin serverů. Mějte na paměti, že servery z různých synchronizačních služeb úložiště nelze synchronizovat mezi sebou.

> [!Note]
> Služba synchronizace úložiště dědí přístupová oprávnění z předplatného a skupiny prostředků, do které byla nasazena. Doporučujeme pečlivě zkontrolovat, kdo k němu má přístup. Entity s přístupem pro zápis můžete spustit synchronizaci nových sad souborů ze serverů registrovaných do této služby synchronizace úložiště a způsobit tok dat do úložiště Azure, které je pro ně přístupné.

# <a name="portal"></a>[Portál](#tab/azure-portal)
Pokud chcete nasadit službu synchronizace úložiště, přejděte na [portál Azure](https://portal.azure.com/), klikněte na *Vytvořit prostředek* a pak vyhledejte Azure File Sync. Ve výsledcích hledání vyberte **Azure File Sync**a pak vyberte **Vytvořit,** abyste otevřeli kartu **Nasadit synchronizaci úložiště.**

V podokně, které se otevře, zadejte následující informace:

- **Název**: Jedinečný název (na jedno předplatné) pro službu synchronizace úložiště.
- **Předplatné**: Předplatné, ve kterém chcete vytvořit službu synchronizace úložiště. V závislosti na konfigurační strategii vaší organizace můžete mít přístup k jednomu nebo více předplatným. Předplatné Azure je nejzákladnější kontejner pro fakturaci pro každou cloudovou službu (například soubory Azure).
- **Skupina prostředků**: Skupina prostředků je logická skupina prostředků Azure, jako je například účet úložiště nebo služba synchronizace úložiště. Můžete vytvořit novou skupinu prostředků nebo použít existující skupinu prostředků pro Azure File Sync. (Doporučujeme použít skupiny prostředků jako kontejnery k logickému izolování prostředků pro vaši organizaci, jako je například seskupení zdrojů lidských zdrojů nebo zdrojů pro konkrétní projekt.)
- **Umístění:** Oblast, ve které chcete nasadit Azure File Sync. V tomto seznamu jsou k dispozici pouze podporované oblasti.

Po dokončení vyberte **Vytvořit** a nasaďte službu synchronizace úložiště.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Nahraďte `<Az_Region>`, `<RG_Name>`a `<my_storage_sync_service>` s vlastními hodnotami, pak použijte následující příkazy k vytvoření a nasazení služby synchronizace úložiště:

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

---

## <a name="install-the-azure-file-sync-agent"></a>Instalace agenta Synchronizace souborů Azure
Agent Synchronizace souborů Azure je balíček ke stažení, který umožňuje synchronizaci Windows Serveru se sdílenou složkou Azure. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Agenta si můžete stáhnout ze služby [Stažení softwaru](https://go.microsoft.com/fwlink/?linkid=858257). Po dokončení stahování poklikejte na balíček MSI a spusťte instalaci agenta Synchronizace souborů Azure.

> [!Important]  
> Pokud máte v úmyslu používat Azure File Sync s clusterem s podporou převzetí služeb při selhání, musí být agent Azure File Sync nainstalovaný na každém uzlu v clusteru. Každý uzel v clusteru musí být registrovaný, aby fungoval s Azure File Sync.

Doporučujeme provést následující kroky:
- Ponechte výchozí instalační cestu (C:\Program Files\Azure\StorageSyncAgent), abyste zjednodušili řešení potíží a údržbu serveru.
- Povolte Microsoft Update, aby byla synchronizace souborů Azure aktuální. Všechny aktualizace agenta Azure File Sync, včetně aktualizací funkcí a oprav hotfix, probíhají ze služby Microsoft Update. Doporučujeme nainstalovat nejnovější aktualizaci azure file sync. Další informace najdete v tématu [Zásady aktualizace synchronizace souborů Azure](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Po dokončení instalace agenta synchronizace souborů Azure se automaticky otevře inkase webového nastavení serveru. Před registrací musíte mít službu synchronizace úložiště. Podívejte se na další část o tom, jak vytvořit službu synchronizace úložiště.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Spusťte následující kód Prostředí PowerShell, abyste stáhli příslušnou verzi agenta Azure File Sync pro váš operační systém a nainstalovali ho do svého systému.

> [!Important]  
> Pokud máte v úmyslu používat Azure File Sync s clusterem s podporou převzetí služeb při selhání, musí být agent Azure File Sync nainstalovaný na každém uzlu v clusteru. Každý uzel v clusteru se musí zaregistrovat, aby fungoval s Azure File Sync.

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

---

## <a name="register-windows-server-with-storage-sync-service"></a>Registrace Windows Serveru ve službě synchronizace úložiště
Registrací vašeho Windows Serveru ve službě synchronizace úložiště se vytvoří vztah důvěryhodnosti mezi vaším serverem (nebo clusterem) a službou synchronizace úložiště. Každý server je možné zaregistrovat pouze k jedné službě synchronizace úložiště a může se synchronizovat s ostatními servery a sdílenými složkami Azure přidruženými ke stejné službě synchronizace úložiště.

> [!Note]
> Registrace serveru používá vaše pověření Azure k vytvoření vztahu důvěryhodnosti mezi službou Synchronizace úložiště a windows serverem, ale následně server vytvoří a použije vlastní identitu, která je platná, dokud server zůstane registrovaný a aktuální token sdíleného přístupového podpisu (Storage SAS) je platný. Nový token SAS nelze vydat na server, jakmile server je neregistrovaný, čímž se odstraní možnost serveru pro přístup ke sdíleným složek Azure, zastavení synchronizace.

# <a name="portal"></a>[Portál](#tab/azure-portal)
UI registrace serveru by se měl otevřít automaticky po instalaci agenta Azure File Sync. Pokud ne, můžete ho otevřít ručně z umístění jeho souboru: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Po otevření hlavního nastavení registrace serveru vyberte **přihlásit.**

Po přihlášení budete vyzváni k zadání následujících informací:

![Snímek obrazovky uživatelského rozhraní pro registraci serveru](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Předplatné Azure:** Předplatné, které obsahuje službu synchronizace úložiště (viz [nasazení služby synchronizace úložiště).](#deploy-the-storage-sync-service) 
- **Skupina prostředků**: Skupina prostředků, která obsahuje službu synchronizace úložiště.
- **Služba synchronizace úložiště**: Název služby synchronizace úložiště, pomocí které se chcete zaregistrovat.

Po výběru příslušných informací vyberte **příkaz Registrovat** a dokončete registraci serveru. V rámci procesu registrace se zobrazí výzva k dalšímu přihlášení.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Vytvoření skupiny synchronizace a koncového bodu cloudu
Skupina synchronizace definuje topologii synchronizace sady souborů. Koncové body v rámci skupiny synchronizace se mezi sebou synchronizují. Skupina synchronizace musí obsahovat jeden koncový bod cloudu, který představuje sdílenou složku Azure, a jeden nebo několik koncových bodů serveru. Koncový bod serveru představuje cestu na registrovaném serveru. Server může mít koncové body serveru ve více skupinách synchronizace. Můžete vytvořit tolik skupin synchronizace, kolik potřebujete k odpovídajícímu popisu požadované topologie synchronizace.

Koncový bod cloudu je ukazatel na sdílenou složku Azure. Všechny koncové body serveru se synchronizují s cloudovým koncovým bodem, takže koncový bod cloudu je centrem. Účet úložiště pro sdílenou složku Azure musí být umístěn ve stejné oblasti jako služba synchronizace úložiště. Bude synchronizována celá sdílená složka Azure, s jednou výjimkou: Bude zřízena speciální složka srovnatelná se skrytou složkou "Informace o systémovém svazku" na svazku NTFS. Tento adresář se nazývá ". SystemShareInformation". Obsahuje důležitá metadata synchronizace, která se nebudou synchronizovat s jinými koncovými body. Nepoužívejte ani neodstraňujte!

> [!Important]  
> Můžete provádět změny libovolného koncového bodu cloudu nebo koncového bodu serveru ve skupině synchronizace a nechat soubory synchronizovat s ostatními koncovými body ve skupině synchronizace. Pokud provedete změnu koncového bodu cloudu (sdílení souborů Azure) přímo, změny musí být nejprve zjištěny úlohou zjišťování změn synchronizace souborů Azure. Úloha zjišťování změn se inicializuje pro koncový bod cloudu pouze jednou za 24 hodin. Další informace najdete v tématu [Azure Files nejčastější dotazy](storage-files-faq.md#afs-change-detection).

# <a name="portal"></a>[Portál](#tab/azure-portal)
Pokud chcete vytvořit synchronizační skupinu, přejděte na [portál Azure](https://portal.azure.com/)na službu Synchronizace úložiště a vyberte + **Sync skupiny**:

![Vytvoření nové skupiny synchronizace na webu Azure Portal](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

V podokně, které se otevře, zadejte následující informace pro vytvoření skupiny synchronizace s koncovým bodem cloudu:

- **Název skupiny synchronizace**: Název skupiny synchronizace, která má být vytvořena. Tento název musí být jedinečný v rámci služby synchronizace úložiště, ale může to být libovolný název, který vám dává smysl.
- **Předplatné:** Předplatné, ve kterém jste nasadili službu Synchronizace úložiště v [nasazení služby Synchronizace úložiště](#deploy-the-storage-sync-service).
- **Účet úložiště**: Pokud vyberete **Vybrat účet úložiště**, zobrazí se jiné podokno, ve kterém můžete vybrat účet úložiště, který má sdílenou složku Azure, se kterou chcete synchronizovat.
- **Sdílená složka Azure**: Název sdílené složky Azure, se kterou chcete synchronizovat.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Chcete-li vytvořit skupinu synchronizace, spusťte následující Prostředí PowerShell. Nezapomeňte nahradit `<my-sync-group>` požadovaný název skupiny synchronizace.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

Po úspěšném vytvoření skupiny synchronizace můžete vytvořit koncový bod cloudu. Nezapomeňte nahradit `<my-storage-account>` a `<my-file-share>` s očekávanými hodnotami.

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

---

## <a name="create-a-server-endpoint"></a>Vytvoření koncového bodu serveru
Koncový bod serveru představuje konkrétní umístění na zaregistrovaném serveru, například složku na svazku serveru. Koncový bod serveru musí být cesta na registrovaném serveru (spíše než připojené sdílené složky) a chcete-li použít vrstvení cloudu, musí být cesta na svazku mimo systém. Síťové úložiště (NAS) není podporováno.

# <a name="portal"></a>[Portál](#tab/azure-portal)
Chcete-li přidat koncový bod serveru, přejděte do nově vytvořené skupiny synchronizace a vyberte **přidat koncový bod serveru**.

![Přidání nového koncového bodu serveru v podokně skupiny synchronizace](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

V podokně **Přidat koncový bod serveru** zadejte následující informace pro vytvoření koncového bodu serveru:

- **Registrovaný server**: Název serveru nebo clusteru, na kterém chcete vytvořit koncový bod serveru.
- **Cesta:** Cesta k systému Windows Server, která má být synchronizována jako součást skupiny synchronizace.
- **Vrstvení cloudu:** Přepínač pro povolení nebo zakázání vrstvení cloudu. Díky vrstvení v cloudu lze zřídka používané nebo přístupné soubory vrstvené na soubory Azure.
- **Volné místo na svazku**: Velikost volného místa pro rezervaci na svazku, na kterém je umístěn koncový bod serveru. Pokud je například volné místo na svazku nastaveno na 50 % na svazku, který má jeden koncový bod serveru, je zhruba polovina dat vrstvena na soubory Azure. Bez ohledu na to, jestli je povoleno vrstvení cloudu, vaše sdílená složka Azure má vždy úplnou kopii dat ve skupině synchronizace.

Chcete-li přidat koncový bod serveru, vyberte **příkaz Vytvořit**. Vaše soubory jsou teď synchronizovány ve sdílené složce Azure a ve Windows Serveru. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Spusťte následující příkazy prostředí PowerShell k vytvoření koncového bodu serveru a nezapomeňte nahradit `<your-server-endpoint-path>` a `<your-volume-free-space>` s požadovanými hodnotami.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>

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
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath 
}
```

---

## <a name="configure-firewall-and-virtual-network-settings"></a>Konfigurace nastavení brány firewall a virtuální sítě

### <a name="portal"></a>Portál
Pokud chcete nakonfigurovat synchronizaci souborů Azure tak, aby fungovala s nastavením brány firewall a virtuální sítě, postupujte takto:

1. Na webu Azure Portal přejděte na účet úložiště, který chcete zabezpečit.
1. V levé nabídce vyberte tlačítko **Brány firewall a virtuální sítě.**
1. V části **Povolit přístup z položky**vyberte **Vybrané sítě.**
1. Ujistěte se, že ip nebo virtuální síť serverů je uvedena v příslušné části.
1. Ujistěte se, že je zaškrtnuto **políčko Povolit důvěryhodným službám společnosti Microsoft přístup k tomuto účtu úložiště.**
1. Chcete-li uložit nastavení, vyberte **Uložit.**

![Konfigurace nastavení brány firewall a virtuální sítě pro synchronizaci souborů Azure](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Registrace pomocí synchronizace souborů Azure
Doporučené kroky k připojení na Azure File Sync pro první s nulovými prostoji při zachování úplné věrnosti souborů a seznamu řízení přístupu (ACL) jsou následující:
 
1. Nasaďte službu synchronizace úložiště.
2. Vytvořte skupinu synchronizace.
3. Nainstalujte agenta Azure File Sync na server s úplnou sadou dat.
4. Zaregistrujte tento server a vytvořte koncový bod serveru ve sdílené složce. 
5. Nechte synchronizaci provést úplné nahrání do sdílené složky Azure (cloudový koncový bod).  
6. Po dokončení počátečního nahrávání nainstalujte agenta Azure File Sync na každý ze zbývajících serverů.
7. Vytvořte nové sdílené složky na každém ze zbývajících serverů.
8. V případě potřeby vytvořte koncové body serveru pro nové sdílené složky pomocí zásad vrstvení cloudu. (Tento krok vyžaduje další úložiště, které mají být k dispozici pro počáteční nastavení.)
9. Nechte agenta Azure File Sync provést rychlé obnovení celého oboru názvů bez skutečného přenosu dat. Po úplné synchronizaci oboru názvů synchronizační modul vyplní místní místo na disku na základě zásad y cloudtiering pro koncový bod serveru. 
10. Zajistěte dokončení synchronizace a otestujte topologii podle potřeby. 
11. Přesměrujte uživatele a aplikace na tuto novou sdílenou složku.
12. Volitelně můžete odstranit všechny duplicitní sdílené složky na serverech.
 
Pokud nemáte další úložiště pro počáteční připojení a chcete připojit k existující sdílené složky, můžete předem osévat data ve sdílených složkách Azure. Tento přístup je navržen pouze v případě, že můžete přijmout prostoje a absolutně zaručit žádné změny dat na sdílených serverech během počátečního procesu registrace. 
 
1. Ujistěte se, že data na některém ze serverů se během procesu registrace nezmění.
2. Předem nastavený soubor Azure sdílí data serveru pomocí libovolného nástroje pro přenos dat přes SMB, například Robocopy, přímé kopie SMB. Vzhledem k tomu, AzCopy nenahrává data přes SMB, takže nemůže být použit pro pre-seeding.
3. Vytvořte topologii Azure File Sync s požadovanými koncovými body serveru, které odkazují na existující sdílené složky.
4. Umožňuje dokončit proces odsouhlasení synchronizace na všech koncových bodech. 
5. Po dokončení odsouhlasení můžete otevřít sdílené složky pro změny.
 
V současné době má přístup k přednastavení několik omezení - 
- Plná věrnost souborů není zachována. Soubory například ztratí seznamy AC a časová razítka.
- Změny dat na serveru před plně v provozu topologie synchronizace může způsobit konflikty na koncových bodech serveru.  
- Po vytvoření cloudového koncového bodu spustí Azure File Sync proces zjišťování souborů v cloudu před spuštěním počáteční synchronizace. Doba potřebná k dokončení tohoto procesu se liší v závislosti na různých faktorech, jako je rychlost sítě, dostupná šířka pásma a počet souborů a složek. Pro hrubý odhad ve verzi preview je proces zjišťování spuštěn přibližně při 10 souborech za sekundu.  Proto i v případě, že před-seeding běží rychle, celková doba získat plně spuštěný systém může být výrazně delší, když jsou data předem nasazena v cloudu.

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>Samoobslužné obnovení prostřednictvím předchozích verzí a služby VSS (služba Stínová kopie svazku)

> [!IMPORTANT]
> Následující informace lze použít pouze s verzí 9 (nebo vyšší) agenta synchronizace úložiště. Verze nižší než 9 nebude mít StorageSyncSelfService rutiny.

Předchozí verze je funkce systému Windows, která umožňuje využít snímky v systému VSS na straně serveru svazku k prezentaci obnovovatelných verzí souboru klientovi SMB.
To umožňuje výkonný scénář, běžně označované jako samoobslužné obnovení, přímo pro pracovníky s informacemi namísto v závislosti na obnovení od správce IT.

Snímky VSS a předchozí verze fungují nezávisle na Azure File Sync. Vrstvení cloudu však musí být nastavena na kompatibilní režim. Mnoho koncových bodů serveru Azure File Sync může existovat na stejném svazku. Musíte provést následující volání Prostředí PowerShell na svazek, který má i jeden koncový bod serveru, kde plánujete nebo používáte vrstvení cloudu.

```powershell
Import-Module ‘<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll’
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

Snímky VSS jsou pořízeny z celého svazku. Ve výchozím nastavení může pro daný svazek existovat až 64 snímků, protože je dostatek místa pro uložení snímků. VSS zpracovává to automaticky. Výchozí plán snímek trvá dva snímky za den, od pondělí do pátku. Tento plán lze konfigurovat prostřednictvím naplánované úlohy systému Windows. Výše uvedená rutina prostředí PowerShell provádí dvě věci:
1. Konfiguruje azure file syncs cloud tiering na určeném svazku tak, aby byl kompatibilní s předchozími verzemi a zaručuje, že soubor lze obnovit z předchozí verze, i když byl vrstvený do cloudu na serveru. 
2. Umožňuje výchozí plán VSS. Poté se můžete rozhodnout ji později upravit. 

> [!Note]  
> Existují dvě důležité věci, které je třeba poznamenat:
>- Pokud použijete parametr -Force a VSS je aktuálně povolena, pak přepíše aktuální plán snímků VSS a nahradí jej výchozím plánem. Před spuštěním rutiny se ujistěte, že jste uložili vlastní konfiguraci.
> - Pokud používáte tuto rutinu v uzlu clusteru, musíte ji také spustit na všech ostatních uzlech v clusteru! 

Chcete-li zjistit, zda je povolena samoobslužná obnova, můžete spustit následující rutinu.

```powershell
    Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

Bude seznam všech svazků na serveru, stejně jako počet cloud tiering kompatibilní dny pro každý. Toto číslo se automaticky vypočítá na základě maximálnímožné snímky na svazek a výchozí snímek plánu. Takže ve výchozím nastavení všechny předchozí verze prezentované informačnímu pracovníkovi lze použít k obnovení z. Totéž platí, pokud změníte výchozí plán, aby více snímků.
Pokud však změníte plán způsobem, který bude mít za následek dostupný snímek na svazku, který je starší než hodnota kompatibilnídny, uživatelé nebudou moci použít tento starší snímek (předchozí verze) k obnovení.

> [!Note]
> Povolení samoobslužného obnovení může mít vliv na spotřebu úložiště Azure a vyúčtování. Tento dopad je omezen na soubory aktuálně vrstvené na serveru. Povolení této funkce zajistí, že je k dispozici verze souboru v cloudu, na kterou lze odkazovat prostřednictvím položky předchozí verze (snímek VSS).
>
> Pokud tuto funkci zakážete, spotřeba úložiště Azure se pomalu sníží, dokud neuplyne okno kompatibilních dnů. Neexistuje žádný způsob, jak to urychlit. 

Výchozí maximální počet snímků VSS na svazek (64) a výchozí plán pro jejich převzetí, výsledkem je maximálně 45 dní předchozích verzí, ze kterých může informační pracovník obnovit v závislosti na tom, kolik snímků VSS můžete uložit na svazek.

Pokud max. 64 Snímků VSS na svazek není správné nastavení pro vás, můžete [změnit tuto hodnotu pomocí klíče registru](https://docs.microsoft.com/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies).
Aby se nové omezení projevilo, je třeba rutinu znovu spustit, aby byla povolena předchozí verze na každém svazku, který byl dříve povolen, s příznakem -Force, který zohledňuje nový maximální počet snímků VSS na svazek. Výsledkem bude nově vypočítaný počet kompatibilních dnů. Vezměte prosím na vědomí, že tato změna se projeví pouze u nově vrstvených souborů a přepíše veškeré vlastní nastavení v plánu VSS, které jste provedli.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migrace nasazení služby Replikace distribuovaného systému souborů (DFS-R) do synchronizace souborů Azure
Migrace nasazení DFS-R do Azure File Sync:

1. Vytvořte skupinu synchronizace představující topologii DFS-R, kterou nahrazujete.
2. Začněte na serveru, který má úplnou sadu dat v topologii DFS-R k migraci. Nainstalujte Azure File Sync na tento server.
3. Zaregistrujte tento server a vytvořte koncový bod serveru pro první server, který má být migrován. Nepovolujte vrstvení cloudu.
4. Unechvšechnu synchronizaci dat se sdílenou složkou Azure (cloudový koncový bod).
5. Nainstalujte a zaregistrujte agenta Azure File Sync na každém ze zbývajících serverů DFS-R.
6. Zakažte dfs-r. 
7. Vytvořte koncový bod serveru na každém serveru DFS-R. Nepovolujte vrstvení cloudu.
8. Zajistěte dokončení synchronizace a otestujte topologii podle potřeby.
9. Vyřadit DFS-R.
10. Vrstvení cloudu může být nyní povoleno na libovolném koncovém bodu serveru podle potřeby.

Další informace naleznete [v tématu Interop Azure File Sync s distribuovaným systémem souborů (DFS).](storage-sync-files-planning.md#distributed-file-system-dfs)

## <a name="next-steps"></a>Další kroky
- [Přidání nebo odebrání koncového bodu serveru synchronizace souborů Azure](storage-sync-files-server-endpoint.md)
- [Registrace nebo zrušení registrace serveru pomocí Azure File Sync](storage-sync-files-server-registration.md)
- [Monitorování Synchronizace souborů Azure](storage-sync-files-monitoring.md)

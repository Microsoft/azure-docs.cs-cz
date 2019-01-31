---
title: Nasazení služby Azure File Sync | Dokumentace Microsoftu
description: Zjistěte, jak nasadit Azure File Sync, a to od začátku do konce.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 23f1f4e8b6a3b71c3792a08645122779e4e629b2
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55465822"
---
# <a name="deploy-azure-file-sync"></a>Nasazení Synchronizace souborů Azure
Azure File Sync umožňuje centralizovat sdílené složky organizace ve službě soubory Azure, při zachování flexibility, výkonu a kompatibility s místními souborového serveru. Azure File Sync transformuje serveru systému Windows na rychlou mezipaměť sdílené složky Azure. Můžete použít jakýkoli protokol dostupný ve Windows serveru pro přístup k datům místně, včetně SMB, NFS a FTPS. Můžete mít libovolný počet mezipamětí po celém světě potřebujete.

Důrazně doporučujeme, abyste si přečetli [plánování nasazení služby soubory Azure](storage-files-planning.md) a [plánování nasazení služby Azure File Sync](storage-sync-files-planning.md) před provedením kroků popsaných v tomto článku.

## <a name="prerequisites"></a>Požadavky
* Účet úložiště Azure a Azure soubor sdílet ve stejné oblasti, kterou chcete nasazení služby Azure File Sync. Další informace naleznete v tématu:
    - [Dostupnost v oblastech](storage-sync-files-planning.md#region-availability) pro Azure File Sync.
    - [Vytvoření účtu úložiště](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) podrobný popis toho, jak vytvořit účet úložiště.
    - [Vytvoření sdílené složky](storage-how-to-create-file-share.md) podrobný popis toho, jak vytvořit sdílenou složku.
* Alespoň jeden podporované instance systému Windows Server nebo cluster s Windows serverem, synchronizaci se službou Azure File Sync. Další informace o podporovaných verzích systému Windows Server najdete v tématu [vzájemná funkční spolupráce s Windows serverem](storage-sync-files-planning.md#azure-file-sync-system-requirements-and-interoperability).
* Ujistěte se, že je nainstalovaný PowerShell 5.1 v systému Windows Server. Pokud používáte Windows Server 2012 R2, ujistěte se, že používáte alespoň prostředí PowerShell 5.1. \*. Tato kontrola ve Windows serveru 2016 můžete bezpečně přeskočit výchozí verze out-of-box je prostředí PowerShell 5.1. Ve Windows serveru 2012 R2 můžete ověřit, že používáte PowerShell 5.1. \* prohlédněte hodnoty **PSVersion** vlastnost **$PSVersionTable** objektu:

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    Pokud vaše PSVersion hodnotu menší než 5.1. \*, budou v případě většiny novou instalací systému Windows Server 2012 R2, budete moct snadno upgradovat stažením a instalací [Windows Management Frameworku (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). Odpovídající balíček ke stažení a instalaci pro Windows Server 2012 R2 je **Win8.1AndW2K12R2 KB\*\*\*\*\*\*\*-x64.msu**.

    > [!Note]  
    > Azure File Sync zatím nepodporuje Powershellu 6 + na Windows Server 2012 R2 nebo Windows Server 2016.
* Az a moduly AzureRM Powershellu.
    - Modul Az se dá nainstalovat podle pokynů uvedených zde: [Instalace a konfigurace Azure Powershellu](https://docs.microsoft.com/powershell/azure/install-Az-ps). 
    - Modul AzureRM Powershellu se dá nainstalovat spuštěním následující rutiny Powershellu:
    
        ```PowerShell
        Install-Module AzureRM
        ```

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Příprava Windows Serveru na použití se Synchronizací souborů Azure
Pro každý server, který chcete používat s Azure File Sync, včetně každého uzlu serveru v clusteru převzetí služeb při selhání, zakázat **konfigurace rozšířeného zabezpečení aplikace Internet Explorer**. To se vyžaduje jenom pro počáteční server registrace. Po zaregistrování serveru můžete tuto možnost znovu povolit.

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
1. Otevřete správce serveru.
2. Klikněte na tlačítko **místní Server**:  
    !["Místního serveru" v levé části uživatelského rozhraní správce serveru](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. V podokně **Vlastnosti** vyberte odkaz **Konfigurace rozšířeného zabezpečení aplikace Internet Explorer**.  
    ![V podokně "Konfigurace rozšířeného zabezpečení" v Uživatelském rozhraní správce serveru](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. V **konfigurace rozšířeného zabezpečení aplikace Internet Explorer** dialogu **vypnout** pro **správci** a **uživatelé**:  
    ![Pop – okno Konfigurace rozšířeného zabezpečení aplikace Internet Explorer s "Off" vybraná](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Pokud chcete zakázat rozšířeného zabezpečení aplikace Internet Explorer, spusťte z relace Powershellu se zvýšenými oprávněními následující:

```PowerShell
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
``` 

---

## <a name="deploy-the-storage-sync-service"></a>Nasazení služby synchronizace úložiště 
Nasazení služby Azure File Sync začíná umístěním **služba synchronizace úložiště** prostředků do skupiny prostředků vybraného předplatného. Doporučujeme, abyste podle potřeby zřizování jako málo z nich. Vytvoříte vztah důvěryhodnosti mezi servery a tento prostředek a serveru lze registrovat pouze do jedné služby synchronizace úložiště. V důsledku toho se doporučuje nasadit tolik služby synchronizace úložiště potřebujete k oddělení skupin serverů. Mějte na paměti, že servery z jiného úložiště služby synchronizace nemůže synchronizovat mezi sebou.

> [!Note]
> Služba synchronizace úložiště Zdědit z předplatného a skupiny prostředků, které byly nasazeny do přístupová oprávnění. Doporučujeme důkladně zkontrolovat kdo má přístup k němu. Entity s oprávněním k zápisu může provést synchronizaci nové sady souborů ze serverů zaregistrovaných do tohoto úložiště synchronizační služba a způsobit, že datový tok do úložiště Azure, který je přístupný na ně.

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Pokud chcete nasadit službu synchronizace úložiště, přejděte na [webu Azure portal](https://portal.azure.com/), klikněte na tlačítko *nový* a vyhledejte Azure File Sync. Ve výsledcích hledání vyberte **Azure File Sync**a pak vyberte **vytvořit** otevřít **nasadit synchronizaci úložiště** kartu.

V podokně, které se otevře, zadejte následující informace:

- **Název**: Jedinečný název (pro každé předplatné) služby synchronizace úložiště.
- **Předplatné**: Předplatné, ve kterém chcete vytvořit službu synchronizace úložiště. V závislosti na strategie konfigurace vaší organizace může mít přístup k jedné nebo více odběrů. Předplatné Azure je nejzákladnější kontejner fakturace pro jednotlivé cloudové služby (například soubory Azure).
- **Skupina prostředků**: Skupina prostředků je logická skupina prostředků Azure, jako je účet úložiště nebo služba synchronizace úložiště. Můžete vytvořit novou skupinu prostředků nebo použijte existující skupinu prostředků pro Azure File Sync. (Doporučujeme použít skupiny prostředků jako kontejnery a izolovat prostředky logicky pro vaši organizaci, jako je například seskupení HR prostředků nebo prostředky pro konkrétní projekt.)
- **Umístění**: Oblast, ve kterém chcete nasazení služby Azure File Sync. Pouze podporované oblasti jsou k dispozici v tomto seznamu.

Až budete hotovi, vyberte **vytvořit** nasadit službu synchronizace úložiště.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Před interakci s rutinami pro správu Azure File Sync, je potřeba importovat knihovny DLL a vytvoření kontextu správy Azure File Sync. Se totiž rutiny pro správu Azure File Sync ještě nejsou součástí moduly Azure Powershellu.

> [!Note]  
> StorageSync.Management.PowerShell.Cmdlets.dll balíček, který obsahuje rutiny pro správu Azure File Sync (záměrně) obsahuje rutiny s neschválených operací (`Login`). Název `Login-AzureStorageSync` byl vybrán tak, aby odpovídaly `Login-AzAccount` alias rutiny v modulu Azure PowerShell. Tato chybová zpráva (a rutiny) se odebere při agenta Azure File Sync je přidání do modulu Azure PowerShell.

```PowerShell
$acctInfo = Login-AzAccount

# The location of the Azure File Sync Agent. If you have installed the Azure File Sync 
# agent to a non-standard location, please update this path.
$agentPath = "C:\Program Files\Azure\StorageSyncAgent"

# Import the Azure File Sync management cmdlets
Import-Module "$agentPath\StorageSync.Management.PowerShell.Cmdlets.dll"

# this variable stores your subscription ID 
# get the subscription ID by logging onto the Azure portal
$subID = $acctInfo.Context.Subscription.Id

# this variable holds your Azure Active Directory tenant ID
# use Login-AzAccount to get the ID from that context
$tenantID = $acctInfo.Context.Tenant.Id

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

# the following command creates an AFS context 
# it enables subsequent AFS cmdlets to be executed with minimal 
# repetition of parameters or separate authentication 
Login-AzureRmStorageSync `
    -SubscriptionId $subID `
    -ResourceGroupName $resourceGroup `
    -TenantId $tenantID `
    -Location $region
```

Po vytvoření kontextu Azure File Sync s `Login-AzureR,StorageSync` rutiny, můžete vytvořit službu synchronizace úložiště. Nezapomeňte nahradit `<my-storage-sync-service>` s požadovaným názvem vaší služby synchronizace úložiště.

```PowerShell
$storageSyncName = "<my-storage-sync-service>"
New-AzureRmStorageSyncService -StorageSyncServiceName $storageSyncName
```

---

## <a name="install-the-azure-file-sync-agent"></a>Instalace agenta Synchronizace souborů Azure
Agent Synchronizace souborů Azure je balíček ke stažení, který umožňuje synchronizaci Windows Serveru se sdílenou složkou Azure. 

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Agenta z si můžete stáhnout [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Po dokončení stahování, klikněte dvakrát na balíček MSI, který chcete spustit instalaci agenta Azure File Sync.

> [!Important]  
> Pokud máte v úmyslu používat Azure File Sync s clusterem převzetí služeb při selhání, musíte na všech uzlech v clusteru nainstalovat agenta Azure File Sync. Každý uzel v clusteru musí být zaregistrovaný pro práci s Azure File Sync.

Doporučujeme, abyste udělali toto:
- Ponechte výchozí instalační cesta (C:\Program Files\Azure\StorageSyncAgent), zjednodušit Poradce při potížích a správu serveru.
- Aktivaci služby Microsoft Update k zajištění aktuálnosti Azure File Sync. Všechny aktualizace agenta Azure File Sync, včetně aktualizace funkcí a opravy hotfix, dojde k ze služby Microsoft Update. Doporučujeme nainstalovat nejnovější aktualizaci Azure File Sync. Další informace najdete v tématu [zásady aktualizace Azure File Sync](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Po dokončení instalace agenta Azure File Sync rozhraní registrace serveru se automaticky otevře. Služba synchronizace úložiště musí mít před registrací; viz následující část o tom, jak vytvořit službu synchronizace úložiště.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Spusťte následující kód Powershellu správnou verzi agenta Azure File Sync pro váš operační systém stáhnout a nainstalovat do vašeho systému.

> [!Important]  
> Pokud máte v úmyslu používat Azure File Sync s clusterem převzetí služeb při selhání, musíte na všech uzlech v clusteru nainstalovat agenta Azure File Sync. Každý uzel v clusteru musí být zaregistrovaný pro práci s Azure File Sync.

```PowerShell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://go.microsoft.com/fwlink/?linkid=875004 `
        -OutFile "StorageSyncAgent.exe" 
}
elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://go.microsoft.com/fwlink/?linkid=875002 `
        -OutFile "StorageSyncAgent.exe" 
}
else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2 and Windows Server 2016")
}

# Extract the MSI from the install package
$tempFolder = New-Item -Path "afstemp" -ItemType Directory
Start-Process -FilePath ".\StorageSyncAgent.exe" -ArgumentList "/C /T:$tempFolder" -Wait

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "$($tempFolder.FullName)\StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.exe", ".\afstemp" -Recurse -Force
```

---

## <a name="register-windows-server-with-storage-sync-service"></a>Registrace Windows Serveru ve službě synchronizace úložiště
Registrací vašeho Windows Serveru ve službě synchronizace úložiště se vytvoří vztah důvěryhodnosti mezi vaším serverem (nebo clusterem) a službou synchronizace úložiště. Každý server je možné zaregistrovat pouze k jedné službě synchronizace úložiště a může se synchronizovat s ostatními servery a sdílenými složkami Azure přidruženými ke stejné službě synchronizace úložiště.

> [!Note]
> Registrace serveru používá přihlašovací údaje Azure k vytvoření vztahu důvěryhodnosti mezi službou synchronizace úložiště a systému Windows Server, ale následně serveru vytváří a používá svůj vlastní identity, která je platná tak dlouho, dokud zůstává registrovaného serveru a platnost aktuálního tokenu sdíleného přístupového podpisu (SAS úložiště). Nový token SAS nelze mu vystavit na server po zrušit, proto odebrání serveru možnost přístupu k vaší sdílené složky Azure, zastavuje se všechny synchronizace serveru.

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Uživatelské rozhraní serveru registrace otvírat automaticky po instalaci agenta Azure File Sync. Pokud tomu tak není, lze jej otevřít ručně z umístění souboru: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Když se otevře uživatelské rozhraní serveru registrace, vyberte **přihlášení** začít.

Po přihlášení, zobrazí se výzva pro následující informace:

![Snímek obrazovky uživatelského rozhraní pro registraci serveru](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Předplatné Azure**: Předplatné obsahující službu synchronizace úložiště (viz [nasadit službu synchronizace úložiště](#deploy-the-storage-sync-service)). 
- **Skupina prostředků**: Skupina prostředků, která obsahuje službu synchronizace úložiště.
- **Služba synchronizace úložiště**: Název služby synchronizace úložiště, který chcete zaregistrovat.

Po výběru příslušné informace, vyberte **zaregistrovat** k dokončení registrace serveru. V rámci procesu registrace se zobrazí výzva k dalšímu přihlášení.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell
$registeredServer = Register-AzureRmStorageSyncServer -StorageSyncServiceName $storageSyncName
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Vytvoření skupiny synchronizace a koncového bodu cloudu
Skupina synchronizace definuje topologii synchronizace sady souborů. Koncové body v rámci skupiny synchronizace se mezi sebou synchronizují. Skupina synchronizace musí obsahovat jeden koncový bod cloudu, který představuje sdílenou složku Azure, a jeden nebo několik koncových bodů serveru. Koncový bod serveru představuje cestu na registrovaného serveru. Server může mít koncové body serveru v několika skupinách synchronizace. Můžete vytvořit libovolný počet skupin synchronizace podle musíte vhodně popsat topologii požadované synchronizace.

Koncový bod cloudu je ukazatel na sdílené složky Azure. Všechny koncové body serveru budou synchronizovat s koncovým bodem cloudové, vytváření koncového bodu cloudu rozbočovače. Účet úložiště pro sdílené složky Azure se musí nacházet ve stejné oblasti jako služba synchronizace úložiště. Podkladové sdílené složky Azure se synchronizují s jednou výjimkou: Speciální složky srovnatelné skryté složky "System Volume Information" na svazku NTFS, se zřídí. Tento adresář se nazývá ". SystemShareInformation". Obsahuje důležité synchronizovat metadata, která nebudou synchronizovány do dalších koncových bodů. Použití nebo ho odstranit.

> [!Important]  
> Můžete provádět změny libovolný koncový bod v cloudu nebo koncový bod serveru ve skupině synchronizace a soubory se synchronizovalo s ostatní koncové body ve skupině synchronizace. Pokud provedete změny ke koncovým bodům cloudu (Azure file share) přímo, nutné změny nejprve mají být zjišťované úlohou detekce změn v Azure File Sync. Pro koncový bod cloudu jenom jednou za 24 hodin se spustí úloha zjišťování změn. Další informace najdete v tématu [soubory Azure – nejčastější dotazy](storage-files-faq.md#afs-change-detection).

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Chcete-li vytvořit skupinu synchronizace v [webu Azure portal](https://portal.azure.com/), přejděte do služby synchronizace úložiště a pak vyberte **+ skupina synchronizace**:

![Vytvoření nové skupiny synchronizace na webu Azure Portal](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

V podokně, které se otevře, zadejte následující informace pro vytvoření skupiny synchronizace s koncovým bodem cloudu:

- **Název skupiny synchronizace**: Název skupiny synchronizace, který se má vytvořit. Tento název musí být jedinečný v rámci služby synchronizace úložiště, ale může to být libovolný název, který vám dává smysl.
- **Předplatné**: Předplatné, kam jste nasadili službu synchronizace úložiště v [nasadit službu synchronizace úložiště](#deploy-the-storage-sync-service).
- **Účet úložiště**: Pokud vyberete **vyberte účet úložiště**, zobrazí se další podokno, ve kterém můžete vybrat účet úložiště obsahující sdílenou složku Azure, který chcete synchronizovat s.
- **Sdílené složky Azure**: Název sdílené složky Azure se kterým chcete synchronizovat.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Chcete-li vytvořit skupinu synchronizace, spusťte následující příkaz Powershellu. Nezapomeňte nahradit `<my-sync-group>` s požadovaným názvem skupiny synchronizace.

```PowerShell
$syncGroupName = "<my-sync-group>"
New-AzureRmStorageSyncGroup -SyncGroupName $syncGroupName -StorageSyncService $storageSyncName
```

Jakmile se skupina synchronizace se úspěšně vytvořil, můžete vytvořit váš koncový bod cloudu. Nezapomeňte nahradit `<my-storage-account>` a `<my-file-share>` s očekávané hodnoty.

```PowerShell
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
New-AzureRmStorageSyncCloudEndpoint `
    -StorageSyncServiceName $storageSyncName `
    -SyncGroupName $syncGroupName ` 
    -StorageAccountResourceId $storageAccount.Id `
    -StorageAccountShareName $fileShare.Name
```

---

## <a name="create-a-server-endpoint"></a>Vytvoření koncového bodu serveru
Koncový bod serveru představuje konkrétní umístění na zaregistrovaném serveru, například složku na svazku serveru. Koncový bod serveru musí být cesta na registrovaném serveru (spíše než připojené sdílené složky) a pokud chcete použít vrstvení cloudu, cesta musí být na svazku bez systému. Úložiště připojené k síti (NAS) se nepodporuje.

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Chcete-li přidat koncový bod serveru, přejděte do nově vytvořeného synchronizace skupiny a pak vyberte **přidat koncový bod serveru**.

![Přidání nového koncového bodu serveru v podokně skupiny synchronizace](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

V podokně **Přidat koncový bod serveru** zadejte následující informace pro vytvoření koncového bodu serveru:

- **Registrovaný server**: Název serveru nebo clusteru, ve kterém chcete vytvořit koncový bod serveru.
- **Cesta**: Windows Server cesta k synchronizovat jako součást skupiny synchronizace.
- **Vrstvení cloudu**: Přepnout na povolit nebo zakázat cloudu ovládání datových vrstev. S cloudem ovládání datových vrstev, zřídka používají nebo používaných souborů můžete Vrstvená do soubory Azure.
- **Volné místo na svazku**: Množství volného místa vyhradit na svazku, na kterém je umístěn koncový bod serveru. Například pokud volné místo na svazku je nastavena na 50 % na svazku, který má koncový bod jeden server, přibližně polovina množství dat je Vrstvená do soubory Azure. Bez ohledu na to, jestli cloud ovládání datových vrstev je povoleno, sdílené složky Azure vždy obsahuje úplnou kopii dat ve skupině synchronizace.

Chcete-li přidat koncový bod serveru **vytvořit**. Soubory jsou teď udržovat synchronizované sdílené složky Azure a Windows Server. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Spusťte následující příkazy prostředí PowerShell k vytvoření koncového bodu serveru a nezapomeňte nahradit `<your-server-endpoint-path>` a `<your-volume-free-space>` s požadované hodnoty.

```PowerShell
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
    New-AzureRmStorageSyncServerEndpoint `
        -StorageSyncServiceName $storageSyncName `
        -SyncGroupName $syncGroupName `
        -ServerId $registeredServer.Id `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering $true `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage
}
else {
    # Create server endpoint
    New-AzureRmStorageSyncServerEndpoint `
        -StorageSyncServiceName $storageSyncName `
        -SyncGroupName $syncGroupName `
        -ServerId $registeredServer.Id `
        -ServerLocalPath $serverEndpointPath 
}
```

---

## <a name="onboarding-with-azure-file-sync"></a>Registrace pomocí služby Azure File Sync
Doporučený postup pro připojení v Azure File Sync první z nich s nula výpadek při zachování úplnou věrnost a seznam řízení přístupu (ACL) jsou následující:
 
1. Nasazení služby synchronizace úložiště.
2. Vytvořte skupinu synchronizace.
3. Instalace agenta Azure File Sync na serveru s plnou datovou sadou.
4. Zaregistrujte tento server a vytvořte koncový bod serveru ve sdílené složce. 
5. Umožní synchronizaci provést úplné nahrát do sdílené složky Azure (koncový bod v cloudu).  
6. Po dokončení počáteční nahrávání nainstalujte agenta Azure File Sync na všechny ostatní servery.
7. Vytvořte nové sdílené složky na všechny zbývající servery.
8. Vytvořte koncové body serveru v nové sdílené složky pomocí zásad vrstvení cloudu, v případě potřeby. (Tento krok vyžaduje další úložiště k dispozici pro počáteční instalaci.)
9. Nechte agenta Azure File Sync provést rychlé obnovení úplné oboru názvů bez převodu skutečná data. Po synchronizaci úplný obor názvů vyplní synchronizační modul na místním disku na základě zásad vrstvení cloudu pro koncový bod serveru. 
10. Ujistěte se synchronizace dokončí a testování vaší topologii, podle potřeby. 
11. Přesměrování uživatelů a aplikací do této nové sdílené složky.
12. Volitelně můžete odstranit všechny duplicitní sdílených složek na serverech.
 
Pokud není k dispozici dodatečné úložiště pro počáteční připojení a chcete se připojit k existující sdílené složky, můžete předvyplnění dat ve sdílených složek Azure. Tento přístup je určeno pouze v případě můžete přijmout výpadky a zaručit vůbec žádné změny dat na sdíleních serveru během počátečního připojování. 
 
1. Ujistěte se, že data na žádném serveru nelze změnit během procesu registrace.
2. Předem osivo sdílených složek Azure s daty serveru využít libovolný nástroj pro přenos dat přes SMB třeba Robocopy s přímým přístupem kopírování SMB. Protože nástroj AzCopy není načtení dat prostřednictvím SMB, nelze ji použít pro předem synchronizace replik indexů.
3. Vytvoření s koncovými body požadovanému serveru odkazující na existující sdílené složky Azure File Sync topologie.
4. Umožnit synchronizaci, dokončete proces sloučení pro všechny koncové body. 
5. Po odsouhlasení hotový, můžete otevřít sdílené složky pro změny.
 
Uvědomte si, že v současné době předem synchronizace replik indexů přístup má několik omezení- 
- Nezachová se plnou věrností na souborech. Například soubory ztratit seznamy řízení přístupu a časová razítka.
- Změny dat na serveru před topologie synchronizace je plně nahoru a je spuštěn, může způsobit konflikty na koncové body serveru.  
- Po vytvoření koncového bodu cloudu Azure File Sync spustí proces pro zjištění souborů v cloudu před zahájením počáteční synchronizace. Čas potřebný k dokončení tohoto procesu se liší v závislosti na různých faktorech, jako je rychlost sítě, dostupnou šířku pásma a počet souborů a složek. Pro hrubý odhad ve verzi preview spustí proces zjišťování přibližně za 10 soubory.  Proto i v případě, že před fast synchronizace replik indexů spuštění, celkový čas získat plně systému může být výrazně déle, pokud jsou předem dosazená data v cloudu.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migrace replikace DFS (DFS-R) nasazení do Azure File Sync
Migrace systému souborů DFS-R nasazení do Azure File Sync:

1. Vytvořte skupinu synchronizace k reprezentaci topologie systému souborů DFS-R, který chcete nahradit.
2. Spusťte na serveru, který má úplnou sadu dat ve vaší topologii systému souborů DFS-R na migraci. Azure File Sync nainstalujte na tomto serveru.
3. Zaregistrujte tento server a vytvořte koncový bod serveru pro první server chcete migrovat. Nepovolujte cloudu ovládání datových vrstev.
4. Umožnit všem synchronizaci dat do sdílené složky Azure (koncový bod v cloudu).
5. Instalace a registrace agenta Azure File Sync na všechny zbývající servery systému souborů DFS-R.
6. Zakázat systému souborů DFS-R. 
7. Vytvoření koncového bodu serveru na všech serverech systému souborů DFS-R. Nepovolujte cloudu ovládání datových vrstev.
8. Ujistěte se synchronizace dokončí a testování vaší topologii, podle potřeby.
9. Vyřazení z provozu systému souborů DFS-R.
10. Květen vrstvení cloudu teď možné povolit na libovolný koncový bod serveru podle potřeby.

Další informace najdete v tématu [spolupráce Azure File Sync s distribuovaného systému souborů (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Další postup
- [Přidání nebo odebrání koncového bodu Azure File Sync serveru](storage-sync-files-server-endpoint.md)
- [Zaregistrovat nebo zrušit registraci serveru pomocí služby Azure File Sync](storage-sync-files-server-registration.md)
- [Monitorování Azure File Sync](storage-sync-files-monitoring.md)

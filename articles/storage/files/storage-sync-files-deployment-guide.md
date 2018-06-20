---
title: Nasadit soubor synchronizaci Azure (preview) | Microsoft Docs
description: Zjistěte, jak nasadit Azure souboru Sync, od začátku do konce.
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
ms.date: 06/05/2017
ms.author: wgries
ms.openlocfilehash: f1230cbc4d654bfb59bb328ed7d75c6fa76ff10c
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268245"
---
# <a name="deploy-azure-file-sync-preview"></a>Nasadit soubor synchronizaci Azure (preview)
Pomocí synchronizace souboru Azure (preview) můžete centralizovat vaší organizace sdílené složky v souborech Azure, zatímco flexibilitu, výkonu a kompatibility pro místní souborový server. Synchronizace služby Azure souboru transformuje na rychlé mezipaměti Azure sdílené složky systému Windows Server. Můžete použít libovolný protokol, který je k dispozici v systému Windows Server pro přístup k datům místně, včetně protokolu SMB, systém souborů NFS a FTPS. Může mít libovolný počet mezipamětí, jako je třeba po celém světě.

Důrazně doporučujeme, abyste si přečetli [plánování nasazení Azure Files](storage-files-planning.md) a [plánování nasazení Azure souboru Sync](storage-sync-files-planning.md) ještě před dokončením kroků popsaných v tomto článku.

## <a name="prerequisites"></a>Požadavky
* Účet úložiště Azure a Azure soubor sdílet ve stejné oblasti, kterou chcete nasadit Azure synchronizace souboru. Další informace naleznete v tématu:
    - [Dostupnost v oblastech](storage-sync-files-planning.md#region-availability) pro synchronizaci souborů Azure.
    - [Vytvoření účtu úložiště](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) podrobný popis toho, jak vytvořit účet úložiště.
    - [Vytvoření sdílené složky](storage-how-to-create-file-share.md) podrobný popis toho, jak vytvořit sdílenou složku.
* Alespoň jeden podporované instance systému Windows Server nebo cluster Windows serveru pro synchronizaci se službou Azure synchronizace souboru. Další informace o podporovaných verzích systému Windows Server najdete v tématu [interoperabilita se systémem Windows Server](storage-sync-files-planning.md#azure-file-sync-interoperability).
* Zkontrolujte, zda že je nainstalována 5.1 prostředí PowerShell v systému Windows Server. Pokud používáte Windows Server 2012 R2, ujistěte se, že používáte alespoň 5.1 prostředí PowerShell. \*. Tato kontrola na Windows Server 2016, můžete přeskočit bezpečně jako 5.1 prostředí PowerShell je výchozí verze out-of-box. V systému Windows Server 2012 R2 můžete ověřit spuštěný 5.1 prostředí PowerShell. \* pohledem na hodnotu **PSVersion** vlastnost **$PSVersionTable** objektu:

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    Pokud vaše PSVersion hodnota je menší než 5.1. \*jako bude v případě u většiny novou instalací systému Windows Server 2012 R2, budete moci snadno upgradovat stahuje a instaluje [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). Příslušný balíček ke stažení a instalaci pro Windows Server 2012 R2 je **Win8.1AndW2K12R2 KB\*\*\*\*\*\*\*-x64.msu**.

    > [!Note]  
    > Synchronizace služby Azure souboru zatím nepodporuje PowerShell 6 + na Windows Server 2012 R2 nebo Windows Server 2016.
* Modul prostředí AzureRM PowerShell na serverech, které chcete používat s Azure synchronizace souboru. Další informace o tom, jak nainstalovat moduly Powershellu AzureRM najdete v tématu [instalace a konfigurace prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Vždy doporučujeme používat nejnovější verzi modulů prostředí Azure PowerShell. 

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Příprava systému Windows Server pro použití s Azure souboru Sync
Pro každý server, který chcete používat se synchronizací souboru Azure, včetně každý uzel serveru v clusteru s podporou převzetí služeb při selhání, zakázat **konfigurace rozšířeného zabezpečení aplikace Internet Explorer**. To je potřeba jenom pro registraci počáteční server. Můžete ji můžete znovu povolit po registraci serveru.

# <a name="portaltabportal"></a>[Azure Portal](#tab/portal)
1. Otevřete správce serveru.
2. Klikněte na tlačítko **místní Server**:  
    !["Místní Server" na levé straně uživatelského rozhraní správce serveru](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. Na **vlastnosti** subpane, vyberte odkaz **konfigurace rozšířeného zabezpečení aplikace Internet Explorer**.  
    ![V podokně "Konfigurace rozšířeného zabezpečení" v uživatelském rozhraní správce serveru](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. V **konfigurace rozšířeného zabezpečení aplikace Internet Explorer** dialogové okno, vyberte **vypnout** pro **správci** a **uživatelé**:  
    ![Vybrané pop okno Konfigurace rozšířeného zabezpečení aplikace Internet Explorer s "Vypnuto"](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Chcete-li zakázat rozšířeného zabezpečení aplikace Internet Explorer, spusťte následující z zvýšenými relace prostředí PowerShell:

```PowerShell
# Disable Internet Explorer Enhanced Security Configuration 
# for Administrators
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force

# Disable Internet Explorer Enhanced Security Configuration 
# for Users
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force

# Force Internet Explorer closed, if open. This is required to fully apply the setting.
# Save any work you have open in the IE browser. This will not affect other browsers,
# including Edge.
Stop-Process -Name iexplore -ErrorAction SilentlyContinue
``` 

---

## <a name="install-the-azure-file-sync-agent"></a>Nainstalujte agenta synchronizace souboru Azure
Agent Azure souboru Sync je ke stažení balíčku, který umožňuje systému Windows Server bude synchronizován se sdílenou složku Azure. 

# <a name="portaltabportal"></a>[Azure Portal](#tab/portal)
Si můžete stáhnout agenta z [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Po dokončení stahování dvakrát klikněte na balíček Instalační služby MSI zahájíte instalaci agenta synchronizace souboru Azure.

> [!Important]  
> Pokud máte v úmyslu používat synchronizaci souboru Azure s clusterem s podporou převzetí služeb při selhání, musí být na všech uzlech v clusteru nainstalován agent synchronizace souboru Azure.

Doporučujeme, abyste provedli následující:
- Ponechte výchozí instalační cesta (C:\Program Files\Azure\StorageSyncAgent), můžete zjednodušit správu řešení potíží a serveru.
- Povolte Microsoft Update a průběžně aktualizovat synchronizace souboru Azure. Všechny aktualizace, agent synchronizace souboru Azure, včetně funkce aktualizace a opravy hotfix, dojít z webu Microsoft Update. Doporučujeme nainstalovat nejnovější aktualizace pro synchronizaci souborů Azure. Další informace najdete v tématu [zásady synchronizace souboru Azure aktualizace](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Po dokončení instalace agenta Azure souboru Sync rozhraní registrace serveru se automaticky otevře. Musíte mít služby synchronizace úložiště před registrating; k vytvoření synchronizační služby úložiště najdete v další části.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Spusťte následující kód v prostředí PowerShell stáhněte si příslušnou verzi agenta synchronizace souboru Azure pro váš operační systém a nainstalujte ji v systému.

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

## <a name="deploy-the-storage-sync-service"></a>Nasazení služby Sync úložiště 
Nasazení synchronizace souborů Azure začíná umístění **úložiště synchronizační služba** prostředků do skupiny prostředků vybraného předplatného. Doporučujeme, abyste zřizování jako několika z nich podle potřeby. Vytvoříte vztah důvěryhodnosti mezi servery a tento prostředek a server se dají registrovat jenom pro jeden synchronizační služby úložiště. V důsledku toho doporučujeme nasadit jako v mnoha synchronizační služby úložiště, jak potřebujete oddělit skupiny serverů. Mějte na paměti, že servery z jiného úložiště synchronizační služby se nemůže synchronizovat mezi sebou.

> [!Note]
> Synchronizační služba úložiště zděděná oprávnění k přístupu z předplatného a skupiny prostředků, které byly nasazeny do. Doporučujeme pečlivě zkontrolujte kdo má přístup k němu. Entity s přístup pro zápis můžete spustit synchronizaci nové sady souborů ze zaregistrovaných pro toto úložiště synchronizační služba a způsobit dat toku do úložiště Azure, která je přístupná na ně.

# <a name="portaltabportal"></a>[Azure Portal](#tab/portal)
Chcete-li nasadit službu úložiště synchronizace, přejděte na [portál Azure](https://portal.azure.com/), klikněte na tlačítko *nový* a vyhledejte soubor synchronizaci Azure. Ve výsledcích hledání vyberte **synchronizace souboru Azure (preview)** a potom vyberte **vytvořit** otevřete **nasazení synchronizace úložiště** kartě.

V podokně, které se otevře zadejte následující informace:

- **Název**: jedinečný název (na jedno předplatné) pro službu synchronizace úložiště.
- **Předplatné**: předplatné, ve kterém chcete vytvořit synchronizační služby úložiště. V závislosti na konfiguraci strategie vaší organizace můžete mít přístup k jedné nebo více odběrů. Předplatné Azure je nejzákladnější kontejner pro fakturaci pro jednotlivých cloudových služeb (například soubory Azure).
- **Skupina prostředků**: Skupina prostředků je logické skupiny prostředků Azure, jako je například účet úložiště nebo synchronizační služby úložiště. Můžete vytvořit novou skupinu prostředků nebo použít existující skupinu prostředků pro synchronizaci souborů Azure. (Doporučujeme používat skupiny prostředků jako kontejnery izolovat prostředky logicky pro vaši organizaci, jako je například seskupení HR prostředky nebo prostředky pro konkrétní projekt.)
- **Umístění**: oblast, ve které chcete nasadit Azure synchronizace souboru. Pouze podporované oblasti jsou k dispozici v tomto seznamu.

Po dokončení vyberte **vytvořit** k nasazení služby Sync úložiště.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Před interakci s rutinami pro správu Azure souboru Sync, musíte importovat knihovny DLL a vytvoření kontextu synchronizace souboru Azure správy. To je potřeba, protože rutiny pro správu Azure souboru Sync ještě nejsou součástí tohoto modulu prostředí AzureRM PowerShell.

> [!Note]  
> StorageSync.Management.PowerShell.Cmdlets.dll balíčku, který obsahuje rutiny pro správu Azure souboru Sync, (záměrně) obsahuje rutinu s neschválených příkazem (`Login`). Název `Login-AzureRmStorageSync` jste vybrali tak, aby odpovídala `Login-AzureRmAccount` alias rutiny v modulu AzureRM prostředí PowerShell. Tuto chybovou zprávu (a rutiny) se odeberou agenta synchronizace souboru Azure se přidá do modulu AzureRM PowerShell.

```PowerShell
$acctInfo = Login-AzureRmAccount

# The location of the Azure File Sync Agent. If you have installed the Azure File Sync 
# agent to a non-standard location, please update this path.
$agentPath = "C:\Program Files\Azure\StorageSyncAgent"

# Import the Azure File Sync management cmdlets
Import-Module "$agentPath\StorageSync.Management.PowerShell.Cmdlets.dll"

# this variable stores your subscription ID 
# get the subscription ID by logging onto the Azure portal
$subID = $acctInfo.Context.Subscription.Id

# this variable holds your Azure Active Directory tenant ID
# use Login-AzureRMAccount to get the ID from that context
$tenantID = $acctInfo.Context.Tenant.Id

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzureRmLocation | ForEach-Object { 
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
Get-AzureRmResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzureRmResourceGroup -Name $resourceGroup -Location $region
}

# the following command creates an AFS context 
# it enables subsequent AFS cmdlets to be executed with minimal 
# repetition of parameters or separate authentication 
Login-AzureRmStorageSync `
    –SubscriptionId $subID `
    -ResourceGroupName $resourceGroup `
    -TenantId $tenantID `
    -Location $region
```

Po vytvoření kontext synchronizace souboru Azure s `Login-AzureRmStorageSync` rutiny, můžete vytvořit synchronizační služby úložiště. Nezapomeňte nahradit `<my-storage-sync-service>` s požadovaným názvem vaší služby synchronizace úložiště.

```PowerShell
$storageSyncName = "<my-storage-sync-service>"
New-AzureRmStorageSyncService -StorageSyncServiceName $storageSyncName
```

---

## <a name="register-windows-server-with-storage-sync-service"></a>Windows Server zaregistrovat úložiště synchronizační služby
Registrace systému Windows Server se službou Sync úložiště vytváří vztah důvěryhodnosti mezi serverem (nebo clusteru) a služby úložiště Sync. Server se dají registrovat jenom pro jeden synchronizační služba úložiště a můžou synchronizovat s jinými servery a Azure sdílené složky přidružené služby Sync stejné úložiště.

> [!Note]
> Registrace serveru používá vaše přihlašovací údaje Azure k vytvoření vztahu důvěryhodnosti mezi synchronizační služba úložiště a systému Windows Server, ale následně serveru vytvoří a použije vlastní identitu, která je platná, dokud zůstává registrovaný server a je platný token pro aktuální sdíleného přístupového podpisu (SAS úložiště). Nový token SAS nelze mu vystavit k serveru po zrušení registrace, proto odebrání schopnosti serveru pro přístup k vaší sdílené složky Azure, zastavování jakékoli synchronizace serveru.

# <a name="portaltabportal"></a>[Azure Portal](#tab/portal)
Uživatelské rozhraní serveru registrace by měla otevřít automaticky po instalaci agenta synchronizace souboru Azure. Pokud tomu tak není, můžete otevřít jej ručně z jeho umístění souboru: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Když se otevře rozhraní registraci serveru, vyberte **přihlášení** zahájíte.

Po přihlášení, budete vyzváni k zadání následujících informací:

![Snímek obrazovky uživatelského rozhraní registrace serveru](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Předplatné Azure**: odběr, který obsahuje službu synchronizace úložiště (v tématu [nasazení služby Sync úložiště](#deploy-the-storage-sync-service)). 
- **Skupina prostředků**: skupinu prostředků, která obsahuje synchronizační služby úložiště.
- **Služba synchronizace úložiště**: název služby Sync úložiště, pro který chcete zaregistrovat.

Jakmile vyberete příslušné informace, vyberte **zaregistrovat** dokončit registraci serveru. Jako součást procesu registrace budete vyzváni k další sign-in.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
```PowerShell
$registeredServer = Register-AzureRmStorageSyncServer -StorageSyncServiceName $storageSyncName
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Vytvoření skupiny synchronizace a koncového bodu cloudu
Synchronizace skupiny definuje topologie synchronizace pro určitou sadu souborů. Koncové body v rámci synchronizace skupiny jsou synchronizovány mezi sebou. Synchronizace skupiny musí obsahovat alespoň jeden cloud koncový bod, který představuje sdílenou složku Azure a jednu nebo koncové body serveru. Koncový bod serveru představuje cestu na zaregistrovaný server. Server může mít koncové body serveru v několika skupin synchronizace. Můžete vytvořit libovolný počet skupin synchronizace, kolik budete muset na odpovídajícím způsobem popisují topologii požadované synchronizace.

Koncový bod cloudu je ukazatel na sdílenou složku Azure. Všechny koncové body serveru se synchronizují s koncového bodu cloudu, provedení koncového bodu cloudu rozbočovače. Účet úložiště Azure sdílené složky musí být umístěny ve stejné oblasti jako službu úložiště synchronizace. Jako celek Azure sdílené složky budou synchronizovat s jednou výjimkou: zřídí speciální složky srovnatelná skrytá složka "System Volume Information" na svazku NTFS. Tento adresář se nazývá ". SystemShareInformation". Obsahuje důležité synchronizovat metadata, která nebude synchronizace pro ostatní koncové body. Použít nebo odstranit!

> [!Important]  
> Můžete provádět změny žádný koncový bod cloudu nebo koncový bod serveru ve skupině synchronizace a na dalších koncových bodů ve skupině synchronizace proběhla vaše soubory. Pokud provedete změnu koncového bodu cloudu (Azure sdílené složky) přímo, nutné změny nejprve mají být zjišťované úlohou detekce změn v souboru synchronizace Azure. Úloha zjištění změn je zahájena pro koncový bod cloudu pouze jednou za 24 hodin. Další informace najdete v tématu [nejčastější dotazy k Azure Files](storage-files-faq.md#afs-change-detection).

# <a name="portaltabportal"></a>[Azure Portal](#tab/portal)
Chcete-li vytvořit skupinu synchronizace v [portál Azure](https://portal.azure.com/), přejděte do vaší služby synchronizace úložiště a potom vyberte **+ skupiny synchronizace**:

![Vytvořit novou skupinu synchronizace na portálu Azure](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

V podokně, které se otevře zadejte následující informace a vytvořte skupinu synchronizace s koncového bodu cloudu:

- **Název skupiny synchronizace**: název skupiny synchronizace, který se má vytvořit. Tento název musí být jedinečný v rámci služby synchronizace úložiště, ale může být jakýkoli název, který je logické za vás.
- **Předplatné**: předplatné, které jste nasadili synchronizační služby úložiště v [nasazení služby Sync úložiště](#deploy-the-storage-sync-service).
- **Účet úložiště**: Pokud jste vybrali **vyberte účet úložiště**, zobrazí se další podokno, ve kterém můžete vybrat účet úložiště, který má sdílenou složku Azure, které chcete synchronizovat s.
- **Sdílenou složku Azure**: název sdílené složky Azure file, pro který chcete synchronizovat.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Chcete-li vytvořit skupiny synchronizace, spusťte následující prostředí PowerShell. Nezapomeňte nahradit `<my-sync-group>` s požadovaným názvem skupiny synchronizace.

```PowerShell
$syncGroupName = "<my-sync-group>"
New-AzureRmStorageSyncGroup -SyncGroupName $syncGroupName -StorageSyncService $storageSyncName
```

Po úspěšném vytvoření skupiny synchronizace můžete vytvořit koncový bod cloudu. Nezapomeňte nahradit `<my-storage-account>` a `<my-file-share>` s očekávaných hodnot.

```PowerShell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzureRmStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzureStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzureStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzureRmStorageSyncCloudEndpoint `
    -StorageSyncServiceName $storageSyncName `
    -SyncGroupName $syncGroupName ` 
    -StorageAccountResourceId $storageAccount.Id
    -StorageAccountShareName $fileShare.Name
```

---

## <a name="create-a-server-endpoint"></a>Vytvoření koncového bodu serveru
Koncový bod serveru představuje určitého umístění na serveru pro registrované, například do složky na serveru svazku. Koncový bod serveru musí být na cestu na zaregistrovaný server (nikoli připojenou složku) a pokud chcete použít cloudové vrstvení, cesta musí být na systémovém svazku. Úložiště připojené k síti (NAS) není podporována.

# <a name="portaltabportal"></a>[Azure Portal](#tab/portal)
Chcete-li přidat koncový bod serveru, přejděte do skupiny nově vytvořený synchronizace a potom vyberte **přidat koncový bod serveru**.

![Přidat nový koncový bod serveru v podokně skupiny synchronizace](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

V **přidat koncový bod serveru** podokně zadejte následující informace pro vytvoření koncového bodu serveru:

- **Zaregistrovaný server**: název serveru nebo clusteru, kde chcete vytvořit koncový bod serveru.
- **Cesta**: cestu v serveru Windows možné synchronizovat jako součást skupiny synchronizace.
- **Cloud Tiering**: přepínač k povolení nebo zakázání cloudu vrstev. S cloudem vrstvení, zřídka používají nebo získat přístup k souborů může být rozvrstvena k Azure Files.
- **Volné místo na svazku**: množství volného místa vyhradit na svazku, na kterém je umístěn serveru koncového bodu. Například pokud volné místo na svazku nastavená na 50 % ve svazku, který má koncový bod jeden server, zhruba poloviční množství dat, je vrstvené k Azure Files. Bez ohledu na tom, jestli cloud vrstvení je povoleno, Azure sdílené složky má vždy úplnou kopii dat ve skupině synchronizace.

Chcete-li přidat koncový bod serveru, vyberte **vytvořit**. Soubory jsou teď byly synchronizovány ve sdílenou složku Azure a Windows Server. 

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Spuštěním následujících příkazů prostředí PowerShell k vytvoření serveru koncového bodu a nezapomeňte nahradit `<your-server-endpoint-path>` a `<your-volume-free-space>` s požadované hodnoty.

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

## <a name="onboarding-with-azure-file-sync"></a>Registrace se synchronizací Azure File
Doporučené kroky zařadit do provozu v Azure souboru Sync pro první s nula výpadek při zachování celého souboru věrnosti a seznam řízení přístupu (ACL) jsou následující:
 
1. Nasazení služby synchronizace úložiště.
2. Vytvořte skupinu synchronizace.
3. Nainstalujte agenta synchronizace souboru Azure na serveru s úplnou datovou sadu.
4. Zaregistrujte tento server a vytvoření koncového bodu serveru ve sdílené složce. 
5. Umožní synchronizaci provést úplné nahrávání do sdílené složky Azure file (koncový bod cloudu).  
6. Po dokončení počáteční nahrávání nainstalujte agenta Azure soubor synchronizaci na všechny zbývající servery.
7. Vytvoření nové sdílené složky na všechny ostatní servery.
8. Vytvořte server koncové body pro nové sdílené složky s vrstvení zásady cloudu, v případě potřeby. (Tento krok vyžaduje další úložiště k dispozici pro počáteční instalaci.)
9. Umožní agenta Azure soubor synchronizaci provést rychlé obnovení úplné oboru názvů bez přenos skutečná data. Po synchronizaci úplné obor názvů naplní synchronizační modul místo na disku na základě zásad vrstvení cloudu pro koncový bod serveru. 
10. Ujistěte se synchronizace dokončí a testování vaší topologie podle potřeby. 
11. Přesměrování uživatelů a aplikací do této nové sdílené složky.
12. Volitelně můžete odstranit duplicitní sdílených složkách na serveru.
 
Pokud nemáte dodatečné úložiště pro počáteční registraci a chcete pro připojení k existující sdílené složky, můžete předvyplnění data v Azure sdílených složek. Tento přístup je navržen jenom v případě můžete přijmout výpadky a absolutně zaručit žádné změny dat ve sdílených složkách serveru během procesu počáteční registrace. 
 
1. Ujistěte se, že data na žádném serveru se nemůže změnit během procesu registrace.
2. Předem osivo Azure sdílené složky s daty na serveru, například pomocí libovolného nástroje pro přenos dat přes SMB Robocopy, přímé kopie SMB. Vzhledem k tomu, že AzCopy není načtení dat prostřednictvím SMB proto jej nelze použít pro předem synchronizace replik indexů.
3. Vytvoření topologie synchronizace souboru Azure s koncovými body požadovaný server, odkazující na existující sdílené složky.
4. Umožní synchronizaci dokončit odsouhlasení procesu na všechny koncové body. 
5. Po dokončení odsouhlasení můžete otevřít sdílených složek pro změny.
 
Upozorňujeme, že v současné době předem synchronizace replik indexů přístup má několik omezení- 
- Úplné věrnosti u souborů není zachován. Například soubory ztratit seznamy řízení přístupu a časová razítka.
- Změny dat na serveru před topologie synchronizace je plně nahoru a spuštění může způsobit konflikty na koncové body serveru.  
- Po vytvoření koncového bodu cloudu Azure souboru Sync spustí proces pro zjištění soubory v cloudu před zahájením počáteční synchronizace. Čas potřebný k dokončení tohoto procesu se liší v závislosti na různých faktorech, jako je rychlost sítě, dostupnou šířku pásma a počet souborů a složek. Pro hrubý odhad ve verzi preview spustí proces zjišťování přibližně na 10 soubory za sekundu.  Proto i v případě, že předem rychlé synchronizace replik indexů spustí, celkovou dobu získat plně spuštěný systém může být výrazně déle, pokud je v cloudu předem dosazená data.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migrace nasazení replikace distribuovaného systému souborů (DFS-R) do Azure souboru Sync
Migrace systému souborů DFS-R nasazení do Azure souboru Sync:

1. Vytvořte skupinu synchronizace představují topologii DFS-R, který chcete nahradit.
2. Spusťte na serveru, který má v úplné sadě dat v topologii DFS-R na migraci. Instalaci synchronizace Azure soubor na tomto serveru.
3. Zaregistrujte tento server a vytvořit koncový bod serveru pro první server chcete migrovat. Nepovolujte cloudu vrstev.
4. Umožní všechny synchronizaci dat do sdílené složky Azure file (koncový bod cloudu).
5. Instalace a registrace agenta Azure soubor synchronizaci na všechny zbývající servery systému souborů DFS-R.
6. Zakázat DFS R. 
7. Vytvoření koncového bodu serveru na všech serverech systému souborů DFS-R. Nepovolujte cloudu vrstev.
8. Ujistěte se synchronizace dokončí a testování vaší topologie podle potřeby.
9. Vyřadit z provozu systému souborů DFS R.
10. Cloud vrstvení může nyní povolit pro libovolný koncový bod serveru podle potřeby.

Další informace najdete v tématu [synchronizace souboru Azure zprostředkovatel komunikace s objekty s distribuovaného systému souborů (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Další postup
- [Přidat nebo odebrat koncový bod serveru synchronizace souboru Azure](storage-sync-files-server-endpoint.md)
- [Zaregistrovat nebo zrušit registraci serveru s Azure souboru Sync](storage-sync-files-server-registration.md)
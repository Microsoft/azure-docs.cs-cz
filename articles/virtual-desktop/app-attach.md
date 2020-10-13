---
title: Připojení aplikace MSIX Virtual Desktop v systému Windows – Azure
description: Jak nastavit připojení aplikace MSIX pro virtuální počítač s Windows
author: Heidilohr
ms.topic: how-to
ms.date: 06/16/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3b02be8f35ff33f758aebe03c89287c51c9ffef7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91816320"
---
# <a name="set-up-msix-app-attach"></a>Nastavení připojení aplikace MSIX

> [!IMPORTANT]
> Připojení aplikace MSIX je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučujeme ji používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V tomto tématu se dozvíte, jak nastavit připojení aplikace MSIX v prostředí virtuálních počítačů s Windows.

## <a name="requirements"></a>Požadavky

Než začnete, je potřeba nakonfigurovat připojení aplikace MSIX:

- Přístup k portálu Windows Insider pro získání verze Windows 10 s podporou aplikace MSIX připojit rozhraní API.
- Funkční nasazení virtuálních počítačů s Windows. Informace o tom, jak nasadit virtuální plochu Windows (Classic), najdete v tématu [Vytvoření tenanta ve virtuálním počítači s Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Informace o tom, jak nasadit virtuální plochu Windows s Azure Resource Manager integrací, najdete v tématu [Vytvoření fondu hostitelů pomocí Azure Portal](./create-host-pools-azure-marketplace.md).
- Nástroj pro vytváření balíčků MSIX
- Síťová sdílená složka v rámci nasazení virtuálního počítače se systémem Windows, kde bude uložen balíček MSIX.

## <a name="get-the-os-image"></a>Získat bitovou kopii operačního systému

Nejprve je třeba získat bitovou kopii operačního systému. Bitovou kopii operačního systému můžete získat pomocí Azure Portal. Pokud jste však členem programu Windows Insider, máte místo toho k dispozici možnost použít portál Windows Insider.

### <a name="get-the-os-image-from-the-azure-portal"></a>Získat bitovou kopii operačního systému z Azure Portal

Získání bitové kopie operačního systému z Azure Portal:

1. Otevřete [Azure Portal](https://portal.azure.com) a přihlaste se.

2. Přejít na **vytvořit virtuální počítač**.

3. Na kartě **základní** vyberte **Windows 10 Enterprise multi-session verze 2004**.

4. Dokončete vytváření virtuálního počítače podle zbývajících pokynů.

     >[!NOTE]
     >Tento virtuální počítač můžete použít k přímému testování připojení aplikace MSIX. Pokud se chcete dozvědět víc, přeskočte dopředu, jak [vygenerovat balíček VHD nebo VHDX pro MSIX](#generate-a-vhd-or-vhdx-package-for-msix). V opačném případě ponechte tento oddíl dál číst.

### <a name="get-the-os-image-from-the-windows-insider-portal"></a>Získání image operačního systému z portálu Windows Insider

Získání image operačního systému z portálu Windows Insider:

1. Otevřete [portál Windows Insider](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0) a přihlaste se.

     >[!NOTE]
     >Abyste mohli získat přístup k portálu Windows Insider, musíte být členem programu Windows Insider. Další informace o programu Windows Insider najdete v [dokumentaci k Windows Insider](/windows-insider/at-home/).

2. Přejděte dolů k části **Vybrat edici** a vyberte **Windows 10 Insider Preview Enterprise (Fast) – Build 19041** nebo novější.

3. Vyberte **Potvrdit**, pak vyberte jazyk, který chcete použít, a pak vyberte **Potvrdit** znovu.

     >[!NOTE]
     >V současné době je anglicky jediný jazyk, který byl testován pomocí funkce. Můžete vybrat jiné jazyky, ale nemusí se zobrazovat tak, jak mají.

4. Po vygenerování odkazu ke stažení vyberte **64 stáhnout** a uložte ho na místní pevný disk.

## <a name="prepare-the-vhd-image-for-azure"></a>Příprava image VHD pro Azure

V dalším kroku budete muset vytvořit hlavní bitovou kopii VHD. Pokud jste ještě nevytvořili image hlavního virtuálního pevného disku, přejděte na [Příprava a přizpůsobení hlavní bitové kopie VHD](set-up-customize-master-image.md) a postupujte podle pokynů.

Po vytvoření hlavní image VHD musíte zakázat automatické aktualizace pro aplikace MSIX připojit aplikace. Chcete-li zakázat automatické aktualizace, bude nutné spustit následující příkazy v příkazovém řádku se zvýšenými oprávněními:

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

rem Disable Windows Update:

sc config wuauserv start=disabled
```

Po zakázání automatických aktualizací je nutné povolit technologii Hyper-V, protože pro přípravu a odpojení VHD k odinstalaci použijete příkaz Mount-VHD.

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```
>[!NOTE]
>Tato změna bude vyžadovat, abyste virtuální počítač restartovali.

Dále Připravte virtuální pevný disk virtuálního počítače pro Azure a nahrajte výsledný disk VHD do Azure. Další informace najdete v tématu [Příprava a přizpůsobení hlavního image VHD](set-up-customize-master-image.md).

Po nahrání virtuálního pevného disku do Azure vytvořte fond hostitelů založený na této nové imagi podle pokynů uvedených v tématu [Vytvoření fondu hostitelů pomocí Azure Marketplaceho](create-host-pools-azure-marketplace.md) kurzu.

## <a name="prepare-the-application-for-msix-app-attach"></a>Příprava aplikace pro připojení aplikace MSIX

Pokud už máte balíček MSIX, přeskočte k [konfiguraci infrastruktury virtuálních počítačů s Windows](#configure-windows-virtual-desktop-infrastructure). Pokud chcete testovat starší verze aplikací, postupujte podle pokynů v tématu [Vytvoření balíčku MSIX z desktopového instalačního programu na virtuálním](/windows/msix/packaging-tool/create-app-package-msi-vm/) počítači, aby se starší verze aplikace převedla na balíček MSIX.

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>Generování balíčku VHD nebo VHDX pro MSIX

Balíčky jsou ve formátu VHD nebo VHDX k optimalizaci výkonu. MSIX vyžaduje správné fungování balíčků VHD nebo VHDX.

Vytvoření balíčku VHD nebo VHDX pro MSIX:

1. [Stáhněte si nástroj msixmgr](https://aka.ms/msixmgr) a uložte složku. zip do složky v rámci virtuálního počítače hostitele relace.

2. Rozbalte složku msixmgr Tool. zip.

3. Zdrojový balíček MSIX umístěte do stejné složky, ve které jste odhlásili nástroj msixmgr.

4. Spuštěním následující rutiny v PowerShellu vytvořte virtuální pevný disk:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >Ujistěte se, že velikost virtuálního pevného disku je dostatečně velká, aby bylo možné rozšířit MSIX. *

5. Spusťte následující rutinu pro připojení nově vytvořeného virtuálního pevného disku:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

6. Spusťte tuto rutinu pro inicializaci VHD:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

7. Spuštěním této rutiny vytvoříte nový oddíl:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

8. Ke zformátování oddílu spusťte tuto rutinu:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

9. Vytvořte nadřazenou složku na připojeném virtuálním pevném disku. Tento krok je povinný, protože připojení aplikace MSIX vyžaduje nadřazenou složku. Nadřazenou složku si můžete pojmenovat, ať už chcete.

### <a name="expand-msix"></a>Rozbalit MSIX

Potom budete muset "rozbalit" MSIX Image tím, že ji rozbalíte. Rozbalení image MSIX:

1. Otevřete příkazový řádek jako správce a přejděte do složky, do které jste stáhli a rozmsixmgrte nástroj.

2. Spuštěním následující rutiny rozbalte MSIX do virtuálního pevného disku, který jste vytvořili a připojili v předchozí části.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    Po dokončení dekomprimace by se měla zobrazit následující zpráva:

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > Pokud používáte balíčky z Microsoft Store pro firmy (nebo školství) v rámci vaší sítě nebo na zařízeních, která nejsou připojená k Internetu, budete muset získat licence k balíčku z úložiště a nainstalovat je, aby se aplikace úspěšně spustila. Viz [použití balíčků offline](#use-packages-offline).

3. Přejděte na připojený virtuální pevný disk a otevřete složku aplikace a ověřte, jestli je přítomen obsah balíčku.

4. Odpojte virtuální pevný disk(VHD).

## <a name="configure-windows-virtual-desktop-infrastructure"></a>Konfigurace infrastruktury virtuálních počítačů s Windows

Podle návrhu se dá sdílet jeden MSIX rozbalený balíček (VHD, který jste vytvořili v předchozí části) mezi virtuálními počítači hostitele s více relacemi, protože virtuální pevné disky jsou připojené v režimu jen pro čtení.

Než začnete, ujistěte se, že vaše sdílená síťová složka splňuje tyto požadavky:

- Sdílená složka je kompatibilní s protokolem SMB.
- Virtuální počítače, které jsou součástí fondu hostitelů relací, mají ke sdílené složce oprávnění NTFS.

### <a name="set-up-an-msix-app-attach-share"></a>Nastavení aplikace MSIX připojit ke sdílené složce

V prostředí virtuálních počítačů s Windows vytvořte sdílenou síťovou složku a přesuňte balíček.

>[!NOTE]
> Osvědčeným postupem při vytváření sdílených síťových složek MSIX je nastavení sdílené síťové složky s oprávněními NTFS jen pro čtení.

## <a name="install-certificates"></a>Instalace certifikátů

Pokud vaše aplikace používá certifikát, který není veřejný nebo podepsaný svým držitelem, tady je postup, jak ho nainstalovat:

1. Klikněte pravým tlačítkem na balíček a vyberte **vlastnosti**.
2. V zobrazeném okně vyberte kartu **digitální podpisy** . V seznamu na kartě by měla být pouze jedna položka, jak je znázorněno na následujícím obrázku. Vyberte tuto položku, chcete-li položku zvýraznit, a pak vyberte **Podrobnosti**.
3. Když se zobrazí okno Podrobnosti digitálního podpisu, vyberte kartu **Obecné** a pak vyberte **Zobrazit certifikát**a pak vyberte **nainstalovat certifikát**.
4. Jakmile se instalační program otevře, vyberte **místní počítač** jako umístění úložiště a pak vyberte **Další**.
5. Pokud se instalační program zeptá, jestli chcete aplikaci umožnit, aby na svém zařízení provedla změny, vyberte **Ano**.
6. Vyberte možnost **umístit všechny certifikáty do následujícího úložiště**a pak vyberte **Procházet**.
7. Když se zobrazí okno vybrat úložiště certifikátů, vyberte **Důvěryhodné osoby**a pak vyberte **OK**.
8. Vyberte **Další** a **Dokončit**.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>Příprava skriptů PowerShellu pro připojení aplikace MSIX

Připojení aplikace MSIX má čtyři samostatné fáze, které je potřeba provést v uvedeném pořadí:

1. Fáze
2. Registrovat
3. Zrušení registrace
4. Depříprava

Každá fáze vytvoří skript prostředí PowerShell. Ukázkové skripty pro každou fázi jsou k dispozici [zde](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach).

### <a name="stage-powershell-script"></a>PowerShellový skript pro přípravu

Než začnete aktualizovat skripty PowerShellu, ujistěte se, že máte identifikátor GUID svazku na virtuálním pevném disku. Získání identifikátoru GUID svazku:

1.  Otevřete sdílenou síťovou složku, kde se nachází virtuální pevný disk ve virtuálním počítači, na kterém spouštíte skript.

2.  Klikněte pravým tlačítkem na virtuální pevný disk a vyberte **připojit**. Tím se virtuální pevný disk připojí k písmenu jednotky.

3.  Po připojení virtuálního pevného disku se otevře okno **Průzkumník souborů** . Zachytit nadřazenou složku a aktualizovat **$parentFolder** proměnnou

    >[!NOTE]
    >Pokud se nezobrazuje nadřazená složka, znamená to, že se MSIX nerozšířila správně. Zopakuje předchozí oddíl a pak to zkuste znovu.

4.  Otevřete nadřazenou složku. Pokud je správně rozbalený, zobrazí se složka se stejným názvem jako balíček. Aktualizujte **$PackageName** proměnnou tak, aby odpovídala názvu této složky.

    Například, `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Otevřete příkazový řádek a zadejte příkaz **mountvol**. Tento příkaz zobrazí seznam svazků a jejich identifikátorů GUID. Zkopírujte identifikátor GUID svazku, ve kterém písmeno jednotky odpovídá jednotce, ke které jste připojili VHD v kroku 2.

    Například v tomto příkladu výstupu příkazu mountvol jste připojili virtuální pevný disk k jednotce C, budete chtít zkopírovat výše uvedenou hodnotu `C:\` :

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Aktualizujte **$volumeGuid** PROMĚNNOU s identifikátorem GUID svazku, který jste právě zkopírovali.

7. Otevřete příkazový řádek prostředí PowerShell pro správu a aktualizujte následující skript prostředí PowerShell s proměnnými, které se vztahují na vaše prostředí.

    ```powershell
    #MSIX app attach staging sample

    #region variables
    $vhdSrc="<path to vhd>"
    $packageName = "<package name>"
    $parentFolder = "<package parent folder>"
    $parentFolder = "\" + $parentFolder + "\"
    $volumeGuid = "<vol guid>"
    $msixJunction = "C:\temp\AppAttach\"
    #endregion

    #region mountvhd
    try
    {
          Mount-Diskimage -ImagePath $vhdSrc -NoDriveLetter -Access ReadOnly
          Write-Host ("Mounting of " + $vhdSrc + " was completed!") -BackgroundColor Green
    }
    catch
    {
          Write-Host ("Mounting of " + $vhdSrc + " has failed!") -BackgroundColor Red
    }
    #endregion

    #region makelink
    $msixDest = "\\?\Volume{" + $volumeGuid + "}\"
    if (!(Test-Path $msixJunction))
    {
         md $msixJunction
    }

    $msixJunction = $msixJunction + $packageName
    cmd.exe /c mklink /j $msixJunction $msixDest
    #endregion

    #region stage
    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime] | Out-Null
    Add-Type -AssemblyName System.Runtime.WindowsRuntime
    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where { $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult] AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]
    $asTaskAsyncOperation = $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult], [Windows.Management.Deployment.DeploymentProgress])
    $packageManager = [Windows.Management.Deployment.PackageManager]::new()
    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd
    $path = ([System.Uri]$path).AbsoluteUri
    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")
    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))
    $task
    #endregion
    ```

### <a name="register-powershell-script"></a>Registrovat PowerShellový skript

Pokud chcete spustit registrační skript, spusťte následující rutiny PowerShellu se zástupnými hodnotami nahrazené hodnotami, které se vztahují na vaše prostředí.

```powershell
#MSIX app attach registration sample

#region variables
$packageName = "<package name>"
$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"
#endregion

#region register
Add-AppxPackage -Path $path -DisableDevelopmentMode -Register
#endregion
```

### <a name="deregister-powershell-script"></a>Zrušit registraci skriptu PowerShellu

Pro tento skript nahraďte zástupný symbol pro **$PackageName** názvem testovaného balíčku.

```powershell
#MSIX app attach deregistration sample

#region variables
$packageName = "<package name>"
#endregion

#region deregister
Remove-AppxPackage -PreserveRoamableApplicationData $packageName
#endregion
```

### <a name="destage-powershell-script"></a>Depříprava skriptu PowerShellu

Pro tento skript nahraďte zástupný symbol pro **$PackageName** názvem testovaného balíčku. V produkčním nasazení by to bylo nejlepší spustit ho při vypnutí.

```powershell
#MSIX app attach de staging sample

$vhdSrc="<path to vhd>"

#region variables
$packageName = "<package name>"
$msixJunction = "C:\temp\AppAttach"
#endregion

#region deregister
Remove-AppxPackage -AllUsers -Package $packageName
Remove-Item "$msixJunction\$packageName" -Recurse -Force -Verbose
#endregion

#region Detach VHD
Dismount-DiskImage -ImagePath $vhdSrc -Confirm:$false
#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Nastavení skriptů simulace pro připojení agenta aplikace MSIX

Po vytvoření skriptů je uživatelé můžou ručně spustit nebo je nastavit tak, aby se spouštěly automaticky jako spouštěcí, přihlašovací, odhlašovací a ukončovací skripty. Další informace o těchto typech skriptů najdete [v tématu Použití spouštěcích, ukončovacích, přihlašovacích a odhlašovacích skriptů v Zásady skupiny](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/).

Každý z těchto automatických skriptů spouští jednu fázi připojovat skripty aplikace:

- Spouštěcí skript spustí skript fáze.
- Přihlašovací skript spustí registrační skript.
- Skript pro odhlášení spustí skript pro zrušení registrace.
- Skript pro vypnutí spustí skript pro depřípravu.

## <a name="use-packages-offline"></a>Používat balíčky offline

Pokud používáte balíčky z [Microsoft Store pro firmy](https://businessstore.microsoft.com/) nebo [Microsoft Store pro vzdělávání](https://educationstore.microsoft.com/) ve vaší síti nebo na zařízeních, která nejsou připojená k Internetu, musíte získat licence k balíčku z Microsoft Store a nainstalovat je do vašeho zařízení, aby bylo možné aplikaci úspěšně spustit. Pokud je zařízení online a může se připojit k Microsoft Store pro firmy, musí se požadované licence stáhnout automaticky, ale pokud jste offline, budete muset licence nastavit ručně.

Chcete-li nainstalovat licenční soubory, bude nutné použít skript prostředí PowerShell, který volá třídu MDM_EnterpriseModernAppManagement_StoreLicenses02_01 v poskytovateli mostu rozhraní WMI.

Tady je postup nastavení licencí pro použití v režimu offline:

1. Stáhněte si balíček aplikace, licence a požadovaná rozhraní z Microsoft Store pro firmy. Potřebujete jak kódované, tak nekódované licenční soubory. Podrobné pokyny ke stažení najdete [tady](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app).
2. Aktualizujte ve skriptu následující proměnné pro krok 3:
      1. `$contentID` je hodnota ID obsahu z nekódovaného souboru licence (. XML). Soubor s licencí můžete otevřít v textovém editoru podle vašeho výběru.
      2. `$licenseBlob` je celý řetězec pro objekt BLOB licence v souboru kódované licence (. bin). Kódovaný soubor s licencí můžete otevřít v textovém editoru podle vašeho výběru.
3. Spusťte následující skript z příkazového řádku PowerShellu pro správu. Vhodným místem pro instalaci licence je na konci [přípravného skriptu](#stage-powershell-script) , který se taky musí spustit z příkazového řádku správce.

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{'ContentID'_in_unencoded_license_file}"

#TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
$licenseBlob = "{Entire_String_in_encoded_license_file}"

$session = New-CimSession

#The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
$licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />'

$params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
$param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
$params.Add($param)


try
{
   $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
   $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

}
catch [Exception]
{
     write-host $_ | out-string
}
```

## <a name="next-steps"></a>Další kroky

Tato funkce se momentálně nepodporuje, ale můžete klást otázky komunitě na [virtuálním počítači s Windows TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Zpětnou vazbu k virtuálnímu počítači s Windows můžete také opustit v [centru pro zpětnou vazbu na virtuálním počítači s Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

---
title: Konfigurace aplikace MSIX pro Windows Virtual Desktop připojit skripty prostředí PowerShell – Azure
description: Postup vytvoření skriptů PowerShellu pro připojení aplikace MSIX k virtuálnímu klientovi Windows
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: d1ca4a843c6731cde7ed70d65fc230a21ef6e7c4
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389430"
---
# <a name="create-powershell-scripts-for-msix-app-attach"></a>Vytváření skriptů PowerShellu pro připojení aplikace MSIX

Toto téma vás seznámí s postupem nastavení skriptů PowerShellu pro připojení aplikace MSIX.

## <a name="install-certificates"></a>Instalace certifikátů

Je nutné nainstalovat certifikáty na všechny hostitele relací ve fondu hostitelů, kteří budou hostovat aplikace z aplikace MSIX připojit balíčky.

Pokud vaše aplikace používá certifikát, který není veřejný nebo podepsaný svým držitelem, tady je postup, jak ho nainstalovat:

1. Klikněte pravým tlačítkem na balíček a vyberte **vlastnosti**.
2. V zobrazeném okně vyberte kartu **digitální podpisy** . V seznamu na kartě by měla být pouze jedna položka, jak je znázorněno na následujícím obrázku. Vyberte tuto položku, chcete-li položku zvýraznit, a pak vyberte **Podrobnosti**.
3. Když se zobrazí okno Podrobnosti digitálního podpisu, vyberte kartu **Obecné** a pak vyberte **Zobrazit certifikát** a pak vyberte **nainstalovat certifikát**.
4. Jakmile se instalační program otevře, vyberte **místní počítač** jako umístění úložiště a pak vyberte **Další**.
5. Pokud se instalační program zeptá, jestli chcete aplikaci umožnit, aby na svém zařízení provedla změny, vyberte **Ano**.
6. Vyberte možnost **umístit všechny certifikáty do následujícího úložiště** a pak vyberte **Procházet**.
7. Když se zobrazí okno vybrat úložiště certifikátů, vyberte **Důvěryhodné osoby** a pak vyberte **OK**.
8. Vyberte **Další** a **Dokončit**.

## <a name="enable-microsoft-hyper-v"></a>Povolit Microsoft Hyper-V

Microsoft Hyper-V musí být povolený, protože `Mount-VHD` příkaz je nutný pro přípravu a `Dismount-VHD` je potřeba pro odfázi.

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

>[!NOTE]
>Tato změna bude vyžadovat, abyste virtuální počítač restartovali.

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

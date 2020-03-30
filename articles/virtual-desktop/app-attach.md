---
title: Připojení aplikace MSIX pro virtuální plochu Windows – Azure
description: Jak nastavit připojení aplikace MSIX pro virtuální plochu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 20a82cbd7de4b5678648bac19ab9b59bf557b0ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128317"
---
# <a name="set-up-msix-app-attach"></a>Nastavení připojení aplikace MSIX

> [!IMPORTANT]
> Připojení aplikace MSIX je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Toto téma vás provede nastavením připojení aplikace MSIX v prostředí Virtuální plochy Windows.

## <a name="requirements"></a>Požadavky

Než začnete, musíte nakonfigurovat připojení aplikace MSIX takto:

- Přístup k portálu Windows Insider získat verzi Windows 10 s podporou pro aplikaci MSIX připojit API.
- Funkční nasazení virtuální plochy systému Windows. Další informace naleznete [v tématu Vytvoření klienta ve windows virtuální ploše](tenant-setup-azure-active-directory.md).
- Balicí nástroj MSIX
- Sdílená síť ová v nasazení virtuální plochy systému Windows, kde bude uložen balíček MSIX

## <a name="get-the-os-image"></a>Získání obrázku operačního systému

Nejprve musíte získat obrázek operačního systému, který budete používat pro aplikaci MSIX. Chcete-li získat obrázek operačního systému:

1. Otevřete [portál Windows Insider](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0) a přihlaste se.

     >[!NOTE]
     >Abyste měli přístup k portálu Windows Insider, musíte být členem programu Windows Insider. Další informace o programu Windows Insider najdete v [dokumentaci](/windows-insider/at-home/)k programu Windows Insider .

2. Přejděte dolů do sekce **Vybrat edici** a vyberte **Windows 10 Insider Preview Enterprise (FAST) – Build 19035** nebo novější.

3. Vyberte **Potvrdit**, vyberte jazyk, který chcete použít, a pak znovu vyberte **Potvrdit.**
    
     >[!NOTE]
     >V současné době je angličtina jediným jazykem, který byl testován s funkcí. Můžete vybrat jiné jazyky, ale nemusí se zobrazit tak, jak mají.
    
4. Po vygenerování odkazu ke stažení vyberte **64bitové stažení** a uložte jej na místní pevný disk.

## <a name="prepare-the-vhd-image-for-azure"></a>Příprava image Virtuálního pevného disku pro Azure 

Než začnete, budete muset vytvořit hlavní obraz virtuálního pevného disku. Pokud jste ještě nevytvořili svůj hlavní obrázek virtuálního pevného disku, přejděte na [připravit a přizpůsobit hlavní obrázek virtuálního pevného disku](set-up-customize-master-image.md) a postupujte podle pokynů. 

Po vytvoření hlavní hospo- obraz virtuálního pevného disku je nutné zakázat automatické aktualizace aplikací msix připojit aplikace. Chcete-li zakázat automatické aktualizace, budete muset v příkazovém řádku se zvýšenými oprávněními spustit následující příkazy:

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

Dále připravte virtuální pevné disky v hd pro Azure a nahrajte výsledný disk Virtuálního pevného disku do Azure. Další informace najdete v [tématu Příprava a přizpůsobení hlavního obrazu virtuálního pevného disku](set-up-customize-master-image.md).

Po nahrání virtuálního pevného disku do Azure vytvořte fond hostitelů, který je založený na této nové image podle pokynů v [centru vytváření hostitelů pomocí výukového](create-host-pools-azure-marketplace.md) programu Azure Marketplace.

## <a name="prepare-the-application-for-msix-app-attach"></a>Příprava aplikace pro připojení aplikace MSIX 

Pokud již balíček MSIX máte, přeskočte na [konfiguraci infrastruktury virtuálních desktopů systému Windows](#configure-windows-virtual-desktop-infrastructure). Pokud chcete otestovat starší aplikace, postupujte podle pokynů v [příkazech Vytvořit balíček MSIX z instalačního programu plochy na virtuálním počítači](/windows/msix/packaging-tool/create-app-package-msi-vm/) převést starší aplikace na balíček MSIX.

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>Generovat balíček VHD nebo VHDX pro MSIX

Balíčky jsou ve formátu VHD nebo VHDX pro optimalizaci výkonu. MSIX vyžaduje, aby balíčky VHD nebo VHDX fungovaly správně.

Generování balíčku VHD nebo VHDX pro MSIX:

1. [Stáhněte si nástroj msixmgr](https://aka.ms/msixmgr) a uložte složku ZIP do složky v rámci virtuálního virtuálního soudu hostitele relace.

2. Rozbalte složku nástroje msixmgr .zip.

3. Vložte zdrojový balíček MSIX do stejné složky, kde jste rozbalili nástroj msixmgr.

4. Spusťte následující rutinu v prostředí PowerShell a vytvořte virtuální pevný disk:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >Ujistěte se, že velikost virtuálního pevného disku je dostatečně velká pro uložení rozšířeného msix.*

5. Spusťte následující rutinu pro připojení nově vytvořeného virtuálního pevného disku:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

6. Spuštěním této rutiny inicializujte virtuální pevný disk:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

7. Spusťte tuto rutinu a vytvořte nový oddíl:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

8. Spusťte tuto rutinu pro formátování oddílu:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

9. Vytvořte nadřazenou složku na připojeném virtuálním pevném disku. Tento krok je povinný, protože připojení aplikace MSIX vyžaduje nadřazenou složku. Nadřazenou složku můžete pojmenovat, jak chcete.

### <a name="expand-msix"></a>Rozbalit MSIX

Poté budete muset obraz MSIX "rozbalit" rozbalením. Rozbalení obrázku MSIX:

1. Otevřete příkazový řádek jako správce a přejděte do složky, do které jste stáhli a rozbalili nástroj msixmgr.

2. Spusťte následující rutinu a rozbalte MSIX do virtuálního pevného disku, který jste vytvořili a namontovali v předchozí části.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    Po rozbalení by se měla zobrazit následující zpráva:

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > Pokud používáte balíčky z Obchodu Microsoft Store pro firmy (nebo školství) v rámci vaší sítě nebo na zařízeních, která nejsou připojená k internetu, budete muset získat licence balíčků ze storu a nainstalovat je, aby se aplikace úspěšně spouštěla. Viz [Použití balíčků offline](#use-packages-offline).

3. Přejděte na připojený virtuální pevný disk a otevřete složku aplikace a potvrďte, že je k dispozici obsah balíčku.

4. Odpojte virtuální pevný disk(VHD).

## <a name="configure-windows-virtual-desktop-infrastructure"></a>Konfigurace infrastruktury virtuálních desktopů windows

Podle návrhu jeden msix rozbalený balíček (VHD jste vytvořili v předchozí části) lze sdílet mezi více virtuálních počítačích hostitele relace jako Virtuální pevné disky jsou připojeny v režimu jen pro čtení.

Než začnete, ujistěte se, že sdílená síťsplňuje tyto požadavky:

- Sdílená složky jsou kompatibilní s s rozhraním SMB.
- Virtuální společnosti, které jsou součástí fondu hostitelů relací, mají oprávnění ntfs ke sdílené položce.

### <a name="set-up-an-msix-app-attach-share"></a>Nastavení sdílené složky připojení aplikace MSIX 

V prostředí Virtuální plochy systému Windows vytvořte sdílenou síťovou složku a přesuňte balíček tam.

>[!NOTE]
> Osvědčeným postupem pro vytváření sdílených síťových složek MSIX je nastavení sdílené síťové složky s oprávněními ntfs jen pro čtení.

## <a name="install-certificates"></a>Instalace certifikátů

Pokud vaše aplikace používá certifikát, který není veřejně důvěryhodný nebo byl podepsaný svým držitelem, nainstalujte ho takto:

1. Klepněte pravým tlačítkem myši na balíček a vyberte **příkaz Vlastnosti**.
2. V okně, které se zobrazí, vyberte kartu **Digitální podpisy.** V seznamu na kartě by měla být pouze jedna položka, jak je znázorněno na následujícím obrázku. Vyberte tuto položku, chcete-li položku zvýraznit, a pak vyberte **Podrobnosti**.
3. Po zobrazení okna podrobností digitálního podpisu vyberte kartu **Obecné** a pak vyberte **Instalovat certifikát**.
4. Po otevření instalačního programu vyberte jako umístění úložiště **místní počítač** a pak vyberte **Další**.
5. Pokud se vás instalační program zeptá, jestli chcete aplikaci povolit změny v zařízení, vyberte **Ano**.
6. Vyberte **Umístit všechny certifikáty do následujícího obchodu**a pak vyberte **Procházet**.
7. Po zobrazíní okno úložiště vybraných certifikátů vyberte **položku Důvěryhodné osoby**a pak vyberte **ok**.
8. Vyberte **Finish** (Dokončit).

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>Příprava skriptů PowerShellu pro připojení aplikace MSIX

Připojení aplikace MSIX má čtyři odlišné fáze, které je třeba provést v následujícím pořadí:

1. Krok
2. Zaregistrovat
3. Zrušit registraci
4. Destage

Každá fáze vytvoří skript prostředí PowerShell. Ukázkové skripty pro každou fázi jsou k dispozici [zde](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach).

### <a name="stage-the-powershell-script"></a>Vymezení skriptu PowerShellu

Před aktualizací skriptů prostředí PowerShell zkontrolujte, zda máte identifikátor GUID svazku ve virtuálním pevném disku. Chcete-li získat identifikátor GUID hlasitosti:

1.  Otevřete sdílenou síťovou složku, kde se virtuální pevný disk nachází uvnitř virtuálního virtuálního zařízení, kde spustíte skript.

2.  Klepněte pravým tlačítkem myši na virtuální pevný disk a vyberte **možnost Připojit**. Tím se virtuální pevný disk připojí k písmenu jednotky.

3.  Po připojení virtuálního pevného disku se otevře okno **Průzkumník souborů.** Zachycení nadřazené složky a aktualizace proměnné **$parentFolder**

    >[!NOTE]
    >Pokud nevidíte nadřazenou složku, znamená to, že MSIX nebyl správně rozbalen. Zopakujte předchozí část a akci opakujte.

4.  Otevřete nadřazenou složku. Pokud je správně rozbaleno, zobrazí se složka se stejným názvem jako balíček. Aktualizujte **proměnnou $packageName** tak, aby odpovídala názvu této složky.

    Například, `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Otevřete příkazový řádek a zadejte **mountvol**. Tento příkaz zobrazí seznam svazků a jejich identifikátorů GUID. Zkopírujte identifikátor GUID svazku, na kterém písmeno jednotky odpovídá jednotce, na kterou jste namontovali virtuální pevný disk, v kroku 2.

    Například v tomto příkladu výstup pro příkaz mountvol, pokud jste připojeni v hd na `C:\`disk C, budete chtít zkopírovat výše uvedenou hodnotu :

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Aktualizujte **proměnnou $volumeGuid** pomocí identifikátoru GUID svazku, který jste právě zkopírovali.

7. Otevřete výzvu prostředí PowerShell pro správce a aktualizujte následující skript Prostředí PowerShell s proměnnými, které se vztahují na vaše prostředí.

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

    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime]
    | Out-Null

    Add-Type -AssemblyName System.Runtime.WindowsRuntime

    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where {
    $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult]
    AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]

    $asTaskAsyncOperation =
    $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult],
    [Windows.Management.Deployment.DeploymentProgress])

    $packageManager = [Windows.Management.Deployment.PackageManager]::new()

    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd

    $path = ([System.Uri]$path).AbsoluteUri

    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")

    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))

    $task

    #endregion
    ```

### <a name="register-powershell-script"></a>Registrace skriptu Prostředí PowerShell

Chcete-li spustit skript registru, spusťte následující rutiny prostředí PowerShell se zástupnými hodnotami nahrazenými hodnotami, které platí pro vaše prostředí.

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

### <a name="deregister-powershell-script"></a>Zrušení registrace skriptu prostředí PowerShell

Pro tento skript nahraďte zástupný symbol pro **$packageName** názvem balíčku, který testujete.

```powershell
#MSIX app attach deregistration sample

#region variables

$packageName = "<package name>"

#endregion

#region deregister

Remove-AppxPackage -PreserveRoamableApplicationData $packageName

#endregion
```

### <a name="destage-powershell-script"></a>Destage PowerShell skript

Pro tento skript nahraďte zástupný symbol pro **$packageName** názvem balíčku, který testujete.

```powershell
#MSIX app attach de staging sample

#region variables

$packageName = "<package name>"

$msixJunction = "C:\temp\AppAttach\"

#endregion

#region deregister

Remove-AppxPackage -AllUsers -Package $packageName

cd $msixJunction

rmdir $packageName -Force -Verbose

#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Nastavení simulačních skriptů pro agenta připojení aplikace MSIX

Po vytvoření skriptů je uživatelé mohou ručně spustit nebo je nastavit tak, aby se spouštěly automaticky jako skripty pro spuštění, přihlášení, odhlášení a vypnutí. Další informace o těchto typech skriptů naleznete [v tématu Použití skriptů pro spuštění, vypnutí, přihlášení a odhlášení v zásadách skupiny](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/).

Každý z těchto automatických skriptů spustí jednu fázi aplikace připojit skripty:

- Spouštěcí skript spustí stage skript.
- Přihlašovací skript spustí skript registru.
- Odhlašovací skript spustí skript zrušení registrace.
- Skript vypnutí spustí skript destage.

## <a name="use-packages-offline"></a>Použití balíčků offline

Pokud používáte balíčky z [Microsoft Storu pro firmy](https://businessstore.microsoft.com/) nebo z Microsoft [Storu pro vzdělávání](https://educationstore.microsoft.com/) v síti nebo na zařízeních, která nejsou připojená k internetu, musíte získat licence na balíčky z Microsoft Storu a nainstalovat je do zařízení, aby se aplikace úspěšně spouštěla. Pokud je vaše zařízení online a může se připojit k Microsoft Storu pro firmy, požadované licence by se měly stahovat automaticky, ale pokud jste offline, budete muset licence nastavit ručně. 

Chcete-li nainstalovat licenční soubory, budete muset použít skript prostředí PowerShell, který volá MDM_EnterpriseModernAppManagement_StoreLicenses02_01 třídy v zprostředkovateli mostu služby WMI.  

Licence pro offline použití nastavíte takto: 

1. Stáhněte si balíček aplikací, licence a požadované architektury z Microsoft Storu pro firmy. Potřebujete kódované i nekódované licenční soubory. Podrobné pokyny ke stažení naleznete [zde](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app).
2. Aktualizujte následující proměnné ve skriptu pro krok 3:
      1. `$contentID`je hodnota ContentID z nekódovaného licenčního souboru (.xml). Licenční soubor můžete otevřít v textovém editoru podle vašeho výběru.
      2. `$licenseBlob`je celý řetězec pro objekt blob licence v souboru licence (.bin). Kódovaný licenční soubor můžete otevřít v textovém editoru podle vašeho výběru. 
3. Spusťte následující skript z výzvy prostředí PowerShell pro správce. Dobrým místem pro instalaci licence je na konci [pracovního skriptu,](#stage-the-powershell-script) který je také třeba spustit z výzvy správce.

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

Tato funkce není v současné době podporována, ale můžete komunitě klást otázky v [komunitě Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Zpětnou vazbu pro Windows Virtual Desktop můžete také zanechat v [centru pro zpětnou vazbu pro Windows Virtual Desktop](https://aka.ms/MRSFeedbackHub)nebo zanechat zpětnou vazbu pro aplikaci MSIX a balicí nástroj v [aplikaci MSIX připojit centrum pro zpětnou vazbu](https://aka.ms/msixappattachfeedback) a [centrum zpětné vazby nástroje pro balení MSIX](https://aka.ms/msixtoolfeedback).

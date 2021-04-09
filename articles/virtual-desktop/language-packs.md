---
title: Instalace jazykových sad na virtuální počítače s Windows 10 na virtuálním počítači s Windows – Azure
description: Jak nainstalovat jazykové sady pro virtuální počítače s Windows 10 s více relacemi na virtuálním počítači s Windows
author: Heidilohr
ms.topic: how-to
ms.date: 12/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fc7892db2ca11ab7970835f8979360961ee01104
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463336"
---
# <a name="add-language-packs-to-a-windows-10-multi-session-image"></a>Přidání jazykových sad do bitové kopie Windows 10 s více relacemi

Virtuální plocha Windows je služba, kterou můžou uživatelé nasadit kdykoli a kdekoli. To je důvod, proč je důležité, aby si uživatelé mohli přizpůsobit, který jazyk se zobrazí v zobrazení Windows 10 Enterprise multi-session image.

Existují dva způsoby, jak můžete přizpůsobit jazykovým potřebám uživatelů:

- Sestavujte vyhrazené fondy hostitelů s přizpůsobenou imagí pro jednotlivé jazyky.
- Mít uživatele s různými požadavky na jazyk a lokalizaci ve stejném fondu hostitelů, ale Přizpůsobte si jejich image, abyste se ujistili, že mohou vybrat libovolný jazyk, který potřebují.

Druhá metoda je mnohem efektivnější a nákladově efektivní. Je ale na vás, abyste se rozhodli, kterou metodu nejlépe vyhovuje vašim potřebám. Tento článek vám ukáže, jak přizpůsobit jazyky pro image.

## <a name="prerequisites"></a>Požadavky

Abyste mohli přidat několik jazyků, budete potřebovat následující věci, abyste mohli přizpůsobit image Windows 10 Enterprise s více relacemi:

- Virtuální počítač Azure s více relacemi Windows 10 Enterprise, verze 1903 nebo novější

- Jazyková verze ISO, funkce na vyžádání (francouzské verze) disk 1 a aplikace doručené pošty ISO verze operačního systému, kterou image používá. Můžete si je stáhnout tady:
     
     - Jazyk ISO:
        - [Windows 10, verze 1903 nebo 1909 Language Pack ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)
        - [Windows 10, verze 2004 nebo 20H2 Language Pack ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)

     - Diskové zámořské departementy 1 ISO:
        - [Windows 10, verze 1903 nebo 1909 FRANCOUZSKÝch disket 1 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        - [Windows 10, verze 2004 nebo 20H2 francouzské disky 1 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        
     - Doručená pošta v aplikacích ISO:
        - [Windows 10, verze 1903 nebo 1909 doručených zpráv – aplikace ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_InboxApps.iso)
        - [Windows 10, verze 2004 Inbox Apps ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_InboxApps.iso)
        - [Windows 10, verze 20H2 doručených zpráv ISO](https://software-download.microsoft.com/download/pr/19041.508.200905-1327.vb_release_svc_prod1_amd64fre_InboxApps.iso)
     
     - Pokud k lokalizaci imagí používáte soubory ISO LXP (Local Experience Pack), budete také muset stáhnout odpovídající LXP ISO pro nejlepší jazykovou zkušenost.
        - Pokud používáte Windows 10, verze 1903 nebo 1909:
          - [Windows 10, verze 1903 nebo 1909 LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_1903_32_64_ARM64_MultiLng_LngPkAll_LXP_ONLY.iso)
        - Pokud používáte Windows 10, verze 2004 nebo 20H2, použijte informace v části [Přidání jazyků ve Windows 10: známé problémy](/windows-hardware/manufacture/desktop/language-packs-known-issue) , které vám pomůžou zjistit, které z následujících lxp soubory ISO jsou pro vás nejvhodnější:
          - [Windows 10, verze 2004 nebo 20H2 **9b** lxp ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_64_ARM64_MultiLang_LangPckAll_LIP_LXP_ONLY)
          - [Windows 10, verze 2004 nebo 20H2 **9C** lxp ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_32_64_ARM64_MultiLng_LngPkAll_LIP_9C_LXP_ONLY)
          - [Windows 10, verze 2004 nebo 20H2 **10C** lxp ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2010C.iso)
          - [Windows 10, verze 2004 nebo 20H2 **11c** lxp ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2011C.iso)
          - [Windows 10, verze 2004 nebo 20H2 **1C** lxp ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2101C.iso)
          - [Windows 10, verze 2004 nebo 20H2 **2C** lxp ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2102C.iso)

- Sdílená složka služby soubory Azure nebo sdílená složka na virtuálním počítači souborového serveru systému Windows

>[!NOTE]
>Sdílená složka (úložiště) musí být přístupná z virtuálního počítače Azure, který plánujete použít k vytvoření vlastní image.

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>Vytvoření úložiště obsahu pro jazykové balíčky a funkce na vyžádání

Vytvoření úložiště obsahu pro jazykové balíčky a FODs a úložiště pro balíčky aplikací doručené pošty:

1. Na virtuálním počítači Azure stáhněte z odkazů v [požadavcích](#prerequisites)aplikace s více jazyky ISO, FODs a Inbox pro Windows 10 pro více relací, verze 1903/1909 a 2004.

2. Otevřete a připojte soubory ISO na virtuálním počítači.

3. Přejít do jazykové sady ISO a zkopírovat obsah ze složek **LocalExperiencePacks** a **x64 \\ Langpacks** a pak vložit obsah do sdílené složky.

4. Přejít na **soubor ISO pro francouzské soubory**, zkopírujte veškerý jeho obsah a vložte ho do sdílené složky.
5. Do složky **amd64fre** ve složce Doručená pošta v aplikacích ISO a zkopírujte obsah v úložišti pro aplikace doručené pošty, které jste připravili.

     >[!NOTE]
     > Pokud pracujete s omezeným úložištěm, zkopírujte pouze soubory pro jazyky, které potřebují vaši uživatelé. Soubory můžete podělit tak, že v názvech svých souborů prohlížíte kódy jazyků. Například soubor ve francouzštině má ve svém názvu kód "fr-FR". Úplný seznam kódů jazyků pro všechny dostupné jazyky najdete v tématu [Dostupné jazykové sady pro Windows](/windows-hardware/manufacture/desktop/available-language-packs-for-windows).

     >[!IMPORTANT]
     > Některé jazyky vyžadují další písma zahrnutá v satelitních balíčcích, která následují podle různých konvencí pojmenování. Například názvy souborů japonského písma zahrnují "Jpan".
     >
     > [!div class="mx-imgBorder"]
     > ![Příklad japonské jazykové sady s označením jazyka "Jpan" v názvech souborů.](media/language-pack-example.png)

6. Nastavte oprávnění pro sdílenou složku úložiště obsahu jazyka, abyste měli přístup pro čtení z virtuálního počítače, který použijete k vytvoření vlastní image.

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-manually"></a>Ruční vytvoření vlastní image pro Windows 10 Enterprise s více relacemi

Ruční vytvoření vlastní image Windows 10 Enterprise s více relacemi:

1. Nasaďte virtuální počítač Azure a pak klikněte na galerii Azure a vyberte aktuální verzi Windows 10 Enterprise s více relacemi, kterou používáte.
2. Po nasazení virtuálního počítače se k němu připojte pomocí protokolu RDP jako místní správce.
3. Ujistěte se, že váš virtuální počítač má všechny nejnovější aktualizace Windows. Stáhněte si aktualizace a v případě potřeby restartujte virtuální počítač.
4. Připojte se k balíčku jazyka, FRANCOUZSKÉmu souboru a úložišti sdílené složky doručených zpráv a připojte ho k jednotce s písmenem (například jednotka E).

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-automatically"></a>Automatické vytvoření vlastní image Windows 10 Enterprise pro více relací

Pokud místo toho chcete jazyky instalovat pomocí automatizovaného procesu, můžete v PowerShellu nastavit skript. Pomocí následujícího ukázkového skriptu můžete nainstalovat jazykové sady španělština (Španělsko), francouzština (Francie) a čínština (ČLR) a satelitní balíčky pro Windows 10 Enterprise s více relacemi verze 2004. Skript integruje sadu Language Interface Pack a všechny potřebné satelitní balíčky do bitové kopie. Můžete ale také upravit tento skript pro instalaci dalších jazyků. Stačí, abyste spustili skript z relace PowerShellu se zvýšenými oprávněními, jinak nebude fungovat.

```powershell
########################################################
## Add Languages to running Windows Image for Capture##
########################################################

##Disable Language Pack Cleanup##
Disable-ScheduledTask -TaskPath "\Microsoft\Windows\AppxDeploymentClient\" -TaskName "Pre-staged app cleanup"

##Set Language Pack Content Stores##
[string]$LIPContent = "E:"

##Spanish##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\es-es\LanguageExperiencePack.es-es.Neutral.appx -LicensePath $LIPContent\es-es\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_es-es.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
Set-WinUserLanguageList $LanguageList -force

##French##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\fr-fr\LanguageExperiencePack.fr-fr.Neutral.appx -LicensePath $LIPContent\fr-fr\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_fr-fr.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~fr-fr~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("fr-fr")
Set-WinUserLanguageList $LanguageList -force

##Chinese(PRC)##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\zh-cn\LanguageExperiencePack.zh-cn.Neutral.appx -LicensePath $LIPContent\zh-cn\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_zh-cn.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Fonts-Hans-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

Tento skript může chvíli trvat v závislosti na počtu jazyků, které potřebujete nainstalovat.

Po spuštění skriptu se ujistěte, že se správně nainstalovaly jazykové sady, a to tak, že **začnou**  >  **Nastavení**  >  **čas & jazyk**  >  **jazyka**. Pokud jsou zde uvedené jazykové soubory, vše je nastaveno.

Po přidání dalších jazyků do image Windows se taky musí aktualizovat aplikace doručené pošty, aby podporovaly přidané jazyky. Můžete to udělat tak, že aktualizujete předem nainstalované aplikace pomocí obsahu z doručených zpráv ISO pro aplikace. Pokud chcete tuto aktualizaci provést v odpojeném prostředí (k dispozici není přístup k Internetu z virtuálního počítače), můžete tento proces automatizovat pomocí následující ukázky skriptu PowerShellu.

```powershell
#########################################
## Update Inbox Apps for Multi Language##
#########################################
##Set Inbox App Package Content Stores##
[string]$InboxApps = "F:\"
##Update Inbox Store Apps##
$AllAppx = Get-Item $inboxapps\*.appx | Select-Object name
$AllAppxBundles = Get-Item $inboxapps\*.appxbundle | Select-Object name
$allAppxXML = Get-Item $inboxapps\*.xml | Select-Object name
foreach ($Appx in $AllAppx) {
    $appname = $appx.name.substring(0,$Appx.name.length-5)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    
    Write-Host "Handeling with xml $appname"  
  
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
      
      Write-Host "Handeling without xml $appname"
      
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
foreach ($Appx in $AllAppxBundles) {
    $appname = $appx.name.substring(0,$Appx.name.length-11)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    Write-Host "Handeling with xml $appname"
    
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
       Write-Host "Handeling without xml $appname"
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
```

>[!IMPORTANT]
>Aplikace doručené pošty zahrnuté v ISO nejsou nejnovějšími verzemi předinstalovaných aplikací pro Windows. Chcete-li získat nejnovější verzi všech aplikací, je třeba aktualizovat aplikace pomocí aplikace pro Windows Store a provést ruční vyhledávání aktualizací po instalaci dalších jazyků.

Až budete hotovi, nezapomeňte odpojit sdílenou složku.

## <a name="finish-customizing-your-image"></a>Dokončení přizpůsobení image

Po instalaci jazykových sad můžete nainstalovat libovolný další software, který chcete přidat do přizpůsobené image.

Až dokončíte přizpůsobení image, budete muset spustit nástroj pro přípravu systému (Sysprep).

Spuštění nástroje Sysprep:

1. Otevřete příkazový řádek se zvýšenými oprávněními a spusťte následující příkaz pro generalizaci bitové kopie:  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. Pomocí pokynů v tématu [Vytvoření spravované image zobecněného virtuálního počítače v Azure](../virtual-machines/windows/capture-image-resource.md)zastavte virtuální počítač a zachyťte ho ve spravované imagi.

3. Nově přizpůsobenou image teď můžete použít k nasazení fondu hostitelů virtuální plochy Windows. Informace o tom, jak nasadit fond hostitelů, najdete v tématu [kurz: Vytvoření fondu hostitelů pomocí Azure Portal](create-host-pools-azure-marketplace.md).

## <a name="enable-languages-in-windows-settings-app"></a>Povolit jazyky v aplikaci nastavení Windows

Nakonec po nasazení fondu hostitelů budete muset přidat jazyk do seznamu jazyků každého uživatele, aby mohl vybrat preferovaný jazyk v nabídce nastavení.

Pokud chcete, aby uživatelé mohli vybrat jazyky, které jste nainstalovali, přihlaste se jako uživatel a pak spuštěním následující rutiny prostředí PowerShell přidejte nainstalované jazykové sady do nabídky jazyky. Tento skript můžete také nastavit jako automatizovaný úkol nebo přihlašovací skript, který se aktivuje, když se uživatel přihlásí ke své relaci.

```powershell
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
$LanguageList.Add("fr-fr")
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

Jakmile uživatel změní nastavení jazyka, bude se muset odhlásit z relace virtuální plochy Windows a znovu se přihlásit, aby se změny projevily. 

## <a name="next-steps"></a>Další kroky

Pokud jste zajímá o známých problémech pro jazykové sady, přečtěte si téma [přidání jazykových sad ve Windows 10 verze 1803 a novějších verzích: známé problémy](/windows-hardware/manufacture/desktop/language-packs-known-issue).

Pokud máte další dotazy týkající se více relací Windows 10 Enterprise, podívejte se na naše [Nejčastější dotazy](windows-10-multisession-faq.md).

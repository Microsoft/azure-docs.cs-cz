---
title: Instalace jazykových sad na virtuální počítače s Windows 10 na virtuálním počítači s Windows – Azure
description: Jak nainstalovat jazykové sady pro virtuální počítače s Windows 10 s více relacemi na virtuálním počítači s Windows
author: Heidilohr
ms.topic: how-to
ms.date: 08/21/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: de495d18220500e5aa5653e89776c2634d5b1c85
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719137"
---
# <a name="add-language-packs-to-a-windows-10-multi-session-image"></a>Přidání jazykových sad do bitové kopie Windows 10 s více relacemi

Virtuální plocha Windows je služba, kterou můžou uživatelé nasadit kdykoli a kdekoli. To je důvod, proč je důležité, aby si uživatelé mohli přizpůsobit, který jazyk se zobrazí v zobrazení Windows 10 Enterprise multi-session image.

Existují dva způsoby, jak můžete přizpůsobit jazykovým potřebám uživatelů:

- Sestavujte vyhrazené fondy hostitelů s přizpůsobenou imagí pro jednotlivé jazyky.
- Mít uživatele s různými požadavky na jazyk a lokalizaci ve stejném fondu hostitelů, ale Přizpůsobte si jejich image, abyste se ujistili, že mohou vybrat libovolný jazyk, který potřebují.

Druhá metoda je mnohem efektivnější a nákladově efektivní. Je ale na vás, abyste se rozhodli, kterou metodu nejlépe vyhovuje vašim potřebám. Tento článek vám ukáže, jak přizpůsobit jazyky pro image.

## <a name="prerequisites"></a>Předpoklady

Abyste mohli přidat několik jazyků, budete potřebovat následující věci, abyste mohli přizpůsobit image Windows 10 Enterprise s více relacemi:

- Virtuální počítač Azure s více relacemi Windows 10 Enterprise, verze 1903 nebo novější

- Jazyková verze ISO a funkce na vyžádání (francouzské) – disk 1 verze operačního systému, který image používá. Můžete si je stáhnout tady:
     
     - Jazyk ISO:
        - [Windows 10, verze 1903 nebo 1909 Language Pack ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)
        - [Windows 10, verze 2004 Language Pack ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)

     - Diskové zámořské departementy 1 ISO:
        - [Windows 10, verze 1903 nebo 1909 FRANCOUZSKÝch disket 1 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        - [Windows 10, verze 2004 – francouzský disk 1 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)

- Sdílená složka služby soubory Azure nebo sdílená složka na virtuálním počítači souborového serveru systému Windows

>[!NOTE]
>Sdílená složka (úložiště) musí být přístupná z virtuálního počítače Azure, který plánujete použít k vytvoření vlastní image.

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>Vytvoření úložiště obsahu pro jazykové balíčky a funkce na vyžádání

Vytvoření úložiště obsahu pro jazykové balíčky a FODs:

1. Na virtuálním počítači Azure Stáhněte s Windows 10 Multi-Language ISO a FODs pro Windows 10 Enterprise multi-session, verze 1903, 1909 a 2004 imagí z odkazů v části [požadavky](#prerequisites).

2. Otevřete a připojte soubory ISO na virtuálním počítači.

3. Přejít do jazykové sady ISO a zkopírovat obsah ze složek **LocalExperiencePacks** a **x64 \\ Langpacks** a pak vložit obsah do sdílené složky.

4. Přejít na **soubor ISO pro francouzské soubory**, zkopírujte veškerý jeho obsah a vložte ho do sdílené složky.

     >[!NOTE]
     > Pokud pracujete s omezeným úložištěm, zkopírujte pouze soubory pro jazyky, které potřebují vaši uživatelé. Soubory můžete podělit tak, že v názvech svých souborů prohlížíte kódy jazyků. Například soubor ve francouzštině má ve svém názvu kód "fr-FR". Úplný seznam kódů jazyků pro všechny dostupné jazyky najdete v tématu [Dostupné jazykové sady pro Windows](/windows-hardware/manufacture/desktop/available-language-packs-for-windows).

     >[!IMPORTANT]
     > Některé jazyky vyžadují další písma zahrnutá v satelitních balíčcích, která následují podle různých konvencí pojmenování. Například názvy souborů japonského písma zahrnují "Jpan".
     >
     > [!div class="mx-imgBorder"]
     > ![Příklad japonské jazykové sady s označením jazyka "Jpan" v názvech souborů.](media/language-pack-example.png)

5. Nastavte oprávnění pro sdílenou složku úložiště obsahu jazyka, abyste měli přístup pro čtení z virtuálního počítače, který použijete k vytvoření vlastní image.

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-manually"></a>Ruční vytvoření vlastní image pro Windows 10 Enterprise s více relacemi

Ruční vytvoření vlastní image Windows 10 Enterprise s více relacemi:

1. Nasaďte virtuální počítač Azure a pak klikněte na galerii Azure a vyberte aktuální verzi Windows 10 Enterprise s více relacemi, kterou používáte.
2. Po nasazení virtuálního počítače se k němu připojte pomocí protokolu RDP jako místní správce.
3. Ujistěte se, že váš virtuální počítač má všechny nejnovější aktualizace Windows. Stáhněte si aktualizace a v případě potřeby restartujte virtuální počítač.
4. Připojte se k jazykovému balíčku a úložišti sdílené složky pro francouzské soubory a připojte ho k jednotce s písmenem (například jednotka E).

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

>[!IMPORTANT]
>Windows 10 Enterprise verze 1903 a 1909 nevyžadují `Microsoft-Windows-Client-Language-Pack_x64_<language-code>.cab` soubor balíčku.

Tento skript může chvíli trvat v závislosti na počtu jazyků, které potřebujete nainstalovat.

Po spuštění skriptu se ujistěte, že se správně nainstalovaly jazykové sady, a to tak, že **začnou**  >  **Nastavení**  >  **čas & jazyk**  >  **jazyka**. Pokud jsou zde uvedené jazykové soubory, vše je nastaveno.

Až budete hotovi, nezapomeňte odpojit sdílenou složku.

## <a name="finish-customizing-your-image"></a>Dokončení přizpůsobení image

Po instalaci jazykových sad můžete nainstalovat libovolný další software, který chcete přidat do přizpůsobené image.

Až dokončíte přizpůsobení image, budete muset spustit nástroj pro přípravu systému (Sysprep).

Spuštění nástroje Sysprep:

1. Otevřete příkazový řádek se zvýšenými oprávněními a spusťte následující příkaz pro generalizaci bitové kopie:  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. Vypněte virtuální počítač a pak ho Zachyťte ve spravované imagi podle pokynů v tématu [Vytvoření spravované image zobecněného virtuálního počítače v Azure](../virtual-machines/windows/capture-image-resource.md).

3. Nově přizpůsobenou image teď můžete použít k nasazení fondu hostitelů virtuální plochy Windows. Informace o tom, jak nasadit fond hostitelů, najdete v tématu [kurz: Vytvoření fondu hostitelů pomocí Azure Portal](create-host-pools-azure-marketplace.md).

## <a name="enable-languages-in-windows-settings-app"></a>Povolit jazyky v aplikaci nastavení Windows

Nakonec budete muset přidat jazyk do seznamu jazyků každého uživatele, aby mohl vybrat preferovaný jazyk v nabídce nastavení.

Pokud chcete, aby uživatelé mohli vybrat jazyky, které jste nainstalovali, přihlaste se jako uživatel a pak spuštěním následující rutiny prostředí PowerShell přidejte nainstalované jazykové sady do nabídky jazyky. Tento skript můžete také nastavit jako automatizovaný úkol, který se aktivuje, když se uživatel přihlásí ke své relaci.

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

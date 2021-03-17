---
title: Virtuální počítač pro přípravu aplikace MSIX pro připojení k aplikaci Windows Virtual Desktop – verze Preview – Azure
description: Vytvoření bitové kopie aplikace MSIX pro fond hostitelů virtuálních ploch Windows
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 204cc9a05d62caf62179100fa3496be422a3ec0c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97425791"
---
# <a name="prepare-an-msix-image-for-windows-virtual-desktop"></a>Příprava image MSIX pro virtuální počítače s Windows

> [!IMPORTANT]
> Připojení aplikace MSIX je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

MSIX App Attach (Preview) je řešení pro vrstvení aplikací, které umožňuje dynamicky připojovat aplikace z balíčku MSIX k uživatelské relaci. Systém balíčků MSIX odděluje aplikace od operačního systému a usnadňuje tak vytváření imagí pro virtuální počítače. Balíčky MSIX vám také poskytují větší kontrolu nad tím, které aplikace můžou uživatelé přistupovat k jejich virtuálním počítačům. Můžete dokonce oddělit aplikace od hlavní image a dát je uživatelům později.

## <a name="create-a-vhd-or-vhdx-package-for-msix"></a>Vytvoření balíčku VHD nebo VHDX pro MSIX

Aby se správně fungovaly balíčky MSIX, musí být ve formátu VHD nebo VHDX. To znamená, že pokud chcete začít, budete muset vytvořit balíček VHD nebo VHDX.

>[!NOTE]
>Pokud jste to ještě neudělali, ujistěte se, že jste povolili technologii Hyper-V podle pokynů v tématu [instalace technologie Hyper-v ve Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).

Vytvoření balíčku VHD nebo VHDX pro MSIX:

1. Nejdřív otevřete PowerShell.
2. Dále spusťte následující rutinu pro vytvoření VHD:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    > Ujistěte se, že je virtuální pevný disk dostatečně velký, aby mohl obsahovat rozbalený balíček MSIX.

3. Spuštěním následující rutiny Připojte virtuální pevný disk, který jste právě vytvořili:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

4. V dalším kroku spusťte tuto rutinu pro inicializaci připojeného virtuálního pevného disku:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

5. Potom spuštěním této rutiny vytvořte nový oddíl inicializovaného virtuálního pevného disku:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

6. Ke zformátování oddílu spusťte tuto rutinu:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

7. Nakonec vytvořte nadřazenou složku na připojeném virtuálním pevném disku. Tento krok je nutný, protože balíček MSIX musí mít nadřazenou složku, aby správně fungovala. Nezáleží na tom, jak pojmenovat nadřazenou složku, pokud existuje nadřazená složka.

## <a name="expand-msix"></a>Rozbalit MSIX

Potom budete muset rozbalit image MSIX tím, že soubory rozbalíte do virtuálního pevného disku.

Rozšíření image MSIX:

1. [Stáhněte si nástroj msixmgr](https://aka.ms/msixmgr) a uložte složku. zip do složky v rámci virtuálního počítače hostitele relace.

2. Rozbalte složku msixmgr Tool. zip.

3. Zdrojový balíček MSIX umístěte do stejné složky, ve které jste odhlásili nástroj msixmgr.

4. Otevřete příkazový řádek jako správce a přejděte do složky, do které jste stáhli a rozmsixmgrte nástroj.

5. Spuštěním následující rutiny rozbalte MSIX do virtuálního pevného disku, který jste vytvořili v předchozí části.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    Po dokončení dekomprimace by se měla zobrazit následující zpráva:

    > Úspěšně rozbalené a použité seznamy ACL pro balíček: <package name> . msix

    >[!NOTE]
    > Pokud používáte balíčky z Microsoft Store pro firmy nebo vzdělávání ve vaší síti nebo na zařízeních, která nejsou připojená k Internetu, budete muset stáhnout a nainstalovat licence balíčku z Microsoft Store a spustit aplikace. Pokud chcete získat licence, přečtěte si téma [použití balíčků offline](app-attach.md#use-packages-offline).

6. Otevřete připojený virtuální pevný disk a otevřete složku aplikace, abyste se ujistili, že je zde obsah balíčku.

7. Odpojte virtuální pevný disk(VHD).

## <a name="upload-msix-image-to-share"></a>Nahrání image MSIX ke sdílení

Po vytvoření balíčku MSIX budete muset nahrát výsledný soubor VHD, VHDX nebo CIM do sdílené složky, kde k nim budou mít virtuální počítače vašich uživatelů přístup.

## <a name="next-steps"></a>Další kroky

Zeptejte se naší komunity na tuto funkci na [virtuálním počítači s Windows TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Zpětnou vazbu k virtuálnímu počítači s Windows můžete také opustit v [centru pro zpětnou vazbu na virtuálním počítači s Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Tady jsou některé další články, které můžete najít užitečné:

- [Glosář připojení aplikace MSIX](app-attach-glossary.md)
- [DOTAZY k připojení aplikace MSIX](app-attach-faq.md)
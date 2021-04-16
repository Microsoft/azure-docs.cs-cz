---
title: MSIX připojení PowerShellu k aplikaci virtuálních počítačů s Windows – Azure
description: Postup nastavení připojení aplikace MSIX pro virtuální plochu Windows pomocí PowerShellu
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: ebc403553443a9ea04525323b751fbdb51d23c6e
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500579"
---
# <a name="set-up-msix-app-attach-using-powershell"></a>Nastavení připojení aplikace MSIX pomocí PowerShellu

Kromě Azure Portal můžete také nastavit ruční připojení aplikace MSIX pomocí PowerShellu. Tento článek vás seznámí s postupem použití prostředí PowerShell k nastavení připojení aplikace v MSIX.

## <a name="requirements"></a>Požadavky

Tady je postup, co je potřeba ke konfiguraci připojení aplikace MSIX:

- Funkční nasazení virtuálních počítačů s Windows. Informace o tom, jak nasadit virtuální plochu Windows (Classic), najdete v tématu [Vytvoření tenanta ve virtuálním počítači s Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Informace o tom, jak nasadit virtuální plochu Windows s Azure Resource Manager integrací, najdete v tématu [Vytvoření fondu hostitelů pomocí Azure Portal](./create-host-pools-azure-marketplace.md).
- Fond hostitelů virtuálních počítačů s Windows s aspoň jedním aktivním hostitelem relace.
- Desktopová skupina vzdálených aplikací.
- Nástroj pro vytváření balíčků MSIX
- MSIX zabalená aplikace se rozšířila na obrázek MSIX, který se nahraje do sdílené složky.
- Sdílená složka v rámci nasazení virtuálního počítače s Windows, kde bude uložený balíček MSIX.
- Sdílená složka, do které jste nahráli image MSIX, musí být přístupná i pro všechny virtuální počítače ve fondu hostitelů. Uživatelé budou pro přístup k imagi potřebovat oprávnění jen pro čtení.
- Stáhněte a nainstalujte PowerShell Core.
- Stáhněte si modul Azure PowerShell pro veřejné verze Preview a rozbalte ho do místní složky.
- Nainstalujte modul Azure spuštěním následující rutiny:

    ```powershell
    Install-Module -Name Az -Force
    ```

## <a name="sign-in-to-azure-and-import-the-module"></a>Přihlaste se k Azure a importujte modul.

Až budete mít všechno připravené, otevřete PowerShell Core na příkazovém řádku se zvýšenými oprávněními a spusťte tuto rutinu:

```powershell
Connect-AzAccount
```

Po spuštění ověřte účet pomocí vašich přihlašovacích údajů. V takovém případě se může zobrazit výzva k zadání adresy URL zařízení nebo tokenu.

## <a name="import-the-azwindowsvirtualdesktop-module"></a>Import modulu AZ. WindowsVirtualDesktop

Budete potřebovat modul AZ. DesktopVirtualization, který bude postupovat podle pokynů v tomto článku.

>[!NOTE]
>Ve verzi Public Preview poskytneme modul jako samostatné soubory ZIP, které musíte ručně importovat.

Než začnete, můžete spuštěním následující rutiny zjistit, jestli je v relaci nebo VIRTUÁLNÍm počítači už nainstalovaný modul AZ. DesktopVirtualization:

```powershell
Get-Module | Where-Object { $_.Name -Like "desktopvirtualization" }
```

Pokud síť WAN odinstalujete existující kopii modulu a začnete znovu, spusťte tuto rutinu:

```powershell
Uninstall-Module Az.DesktopVirtualization
```

Pokud je modul na vašem VIRTUÁLNÍm počítači blokovaný, spusťte tuto rutinu a odblokujte ji:

```powershell
Unblock-File "<path>\Az.DesktopVirtualization.psm1"
```

Díky tomuto vyčištění je čas importovat modul.

1. Spusťte následující rutinu a po zobrazení výzvy k potvrzení, že chcete spustit vlastní kód, stiskněte klávesu **R** .

   ```powershell
   Import-Module -Name "<path>\Az.DesktopVirtualization.psm1" -Verbose
   ```

2. Po spuštění rutiny Import zkontrolujte, jestli má rutiny pro MSIX, a to spuštěním následující rutiny:

   ```powershell
   Get-Command -Module Az.DesktopVirtualization | Where-Object { $_.Name -match "MSIX" }
   ```

   Pokud jsou rutiny tam, výstup by měl vypadat takto:

   ```powershell
   CommandType     Name                                               Version    Source

   -----------     ----                                               -------    ------

   Function        Expand-AzWvdMsixImage                              0.0        Az.DesktopVirtualization

   Function        Get-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        New-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        Remove-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization

   Function        Update-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization
   ```

   Pokud tento výstup nevidíte, zavřete všechny relace PowerShellu a PowerShellu Core a zkuste to znovu.

## <a name="set-up-helper-variables"></a>Nastavení pomocných proměnných

Po importu modulu budete muset nastavit pomocné proměnné. Následující příklady vám ukážou, jak to udělat.

Postup získání ID předplatného:

```powershell
Get-AzContext -ListAvailable | fl
```

Výběr kontextu tenanta Azure a předplatného s názvem:

```powershell
$obj = Select-AzContext -Name "<Name>"
```

Nastavení proměnné předplatného:

```powershell
$subId = $obj.Subscription.Id
```

Nastavení názvu pracovního prostoru:

```powershell
$ws = "<WorksSpaceName>"
```

Nastavení názvu fondu hostitelů:

```powershell
$hp = "<HostPoolName>"
```

Chcete-li nastavit skupinu prostředků, ve které jsou konfigurovány virtuální počítače hostitele relace:

```powershell
$rg = "<ResourceGroupName>"
```

A nakonec potvrďte, že jste správně nastavili všechny proměnné:

```powershell
Get-AzWvdWorkspace -Name $ws -ResourceGroupName $rg -SubscriptionId $subID
```

## <a name="add-an-msix-package-to-a-host-pool"></a>Přidání balíčku MSIX do fondu hostitelů

Jakmile nastavíte vše, je čas přidat balíček MSIX do fondu hostitelů. Abyste to mohli udělat, musíte nejdřív získat cestu UNC k imagi MSIX.

Použijte cestu UNC, spusťte tuto rutinu, aby se rozšířila bitová kopie MSIX:

```powershell
$obj = Expand-AzWvdMsixImage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subID -Uri <UNCPath>
```

Spuštěním této rutiny přidejte balíček MSIX do požadovaného hostitelského fondu:

```powershell
New-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId -PackageAlias $obj.PackageAlias -DisplayName <DisplayName> -ImagePath <UNCPath> -IsActive:$true
```

Až to budete mít, potvrďte, že se balíček vytvořil pomocí této rutiny:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object {$_.PackageFamilyName -eq $obj.PackageFamilyName}
```

## <a name="remove-an-msix-package-from-a-host-pool"></a>Odebrání balíčku MSIX z fondu hostitelů

Postup odebrání balíčku z fondu hostitelů:

Pomocí této rutiny Získejte seznam všech balíčků přidružených k fondu hostitelů a pak najděte název balíčku, který chcete ve výstupu odebrat:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId 
```

Případně můžete také získat konkrétní balíček na základě jeho zobrazovaného názvu pomocí této rutiny:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object { $_.Name -like "Power" }
```

Pokud chcete balíček odebrat, spusťte tuto rutinu:

```powershell
Remove-AzWvdMsixPackage -FullName $obj.PackageFullName -HostPoolName $hp -ResourceGroupName $rg
```

## <a name="publish-msix-apps-to-an-app-group"></a>Publikování aplikací pro MSIX do skupiny aplikací

Pokud jste dokončili postup podle pokynů v předchozích částech, můžete postupovat podle pokynů v této části. Pokud máte fond hostitelů s aktivním hostitelem relace, alespoň jednu skupinu desktopových aplikací a přidáte do fondu hostitelů balíček MSIX, jste připraveni na to.

Chcete-li publikovat aplikaci z balíčku MSIX do skupiny aplikací, je nutné najít její název a potom použít tento název v rámci rutiny publikování.

Publikování aplikace:

Spuštěním této rutiny zobrazíte seznam všech dostupných skupin aplikací:

```powershell
Get-AzWvdApplicationGroup -ResourceGroupName $rg -SubscriptionId $subId
```

Po nalezení názvu skupiny aplikací, na kterou chcete publikovat aplikace, použijte její název v této rutině:

```powershell
$grName = "<AppGroupName>"
```

Nakonec budete muset aplikaci publikovat.

- Pokud chcete publikovat aplikaci MSIX do skupiny desktopové aplikace, spusťte tuto rutinu:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0
   ```

- Pokud chcete aplikaci publikovat do skupiny vzdálených aplikací, spusťte tuto rutinu:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0 -MsixPackageApplicationId $obj.PackageApplication.AppId
   ```

>[!NOTE]
>Pokud se uživatel přiřadí do skupiny vzdálených aplikací i do skupiny desktopových aplikací ve stejném fondu hostitelů a uživatel se připojí ke vzdálené ploše, uvidí aplikace MSIX z obou skupin.

## <a name="next-steps"></a>Další kroky

Zeptejte se naší komunity na tuto funkci na [virtuálním počítači s Windows TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Zpětnou vazbu k virtuálnímu počítači s Windows můžete také opustit v [centru pro zpětnou vazbu na virtuálním počítači s Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Tady jsou některé další články, které můžete najít užitečné:

- [Glosář připojení aplikace MSIX](app-attach-glossary.md)
- [DOTAZY k připojení aplikace MSIX](app-attach-faq.md)

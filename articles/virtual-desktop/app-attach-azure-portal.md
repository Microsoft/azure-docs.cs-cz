---
title: MSIX připojení portálu pro aplikace virtuálních počítačů s Windows – Preview – Azure
description: Postup nastavení připojení aplikace MSIX k virtuálnímu počítači s Windows pomocí Azure Portal.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ffae02a11f2857e72cf7c5d2bbc84d8ab6174e17
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97425794"
---
# <a name="set-up-msix-app-attach-with-the-azure-portal"></a>Nastavení aplikace MSIX připojit k Azure Portal

> [!IMPORTANT]
> Připojení aplikace MSIX je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučujeme ji používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek vás provede postupem nastavení připojení aplikace MSIX (ve verzi Preview) v prostředí virtuálních počítačů s Windows.

## <a name="requirements"></a>Požadavky

>[!IMPORTANT]
>Než začnete, nezapomeňte vyplnit a odeslat [Tento formulář](https://aka.ms/enablemsixappattach) , aby bylo možné ve svém předplatném připojit aplikaci MSIX. Pokud nemáte schválenou žádost, připojení aplikace MSIX nebude fungovat. Schvalování žádostí může během pracovních dnů trvat až 24 hodin. Po přijetí a dokončení vaší žádosti obdržíte e-mail.

Tady je postup, co je potřeba ke konfiguraci připojení aplikace MSIX:

- Funkční nasazení virtuálních počítačů s Windows. Informace o tom, jak nasadit virtuální plochu Windows (Classic), najdete v tématu [Vytvoření tenanta ve virtuálním počítači s Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Informace o tom, jak nasadit virtuální plochu Windows s Azure Resource Manager integrací, najdete v tématu [Vytvoření fondu hostitelů pomocí Azure Portal](./create-host-pools-azure-marketplace.md).
- Fond hostitelů virtuálních počítačů s Windows s aspoň jedním aktivním hostitelem relace.
- Nástroj pro vytváření balíčků MSIX
- MSIX zabalená aplikace se rozšířila na obrázek MSIX, který se nahraje do sdílené složky.
- Sdílená složka v rámci nasazení virtuálního počítače s Windows, kde bude uložený balíček MSIX.
- Sdílená složka, do které jste nahráli image MSIX, musí být přístupná i pro všechny virtuální počítače ve fondu hostitelů. Uživatelé budou pro přístup k imagi potřebovat oprávnění jen pro čtení.

## <a name="turn-off-automatic-updates-for-msix-app-attach-applications"></a>Vypnout automatické aktualizace pro aplikace MSIX připojit k aplikacím

Než začnete, musíte zakázat automatické aktualizace pro aplikace MSIX připojit aplikace. Chcete-li zakázat automatické aktualizace, bude nutné spustit následující příkazy v příkazovém řádku se zvýšenými oprávněními:

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

Po zakázání automatických aktualizací je nutné povolit technologii Hyper-V, protože použijete `Mount-VHD` příkaz pro přípravu a odpojení VHD pro odinstalaci.

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

>[!NOTE]
>Tato změna bude vyžadovat, abyste virtuální počítač restartovali.

## <a name="configure-the-msix-app-attach-management-interface"></a>Konfigurace rozhraní pro správu připojení aplikace MSIX

V dalším kroku budete muset stáhnout a nakonfigurovat rozhraní pro správu připojení aplikace MSIX pro Azure Portal.

Nastavení rozhraní pro správu:

1. [Otevřete portál Preview](https://preview.portal.azure.com/?feature.msixapplications=true#home).
2. Pokud se zobrazí výzva s dotazem, jestli považujete důvěryhodné rozšíření za důvěryhodné, vyberte možnost **udělit**.

      > [!div class="mx-imgBorder"]
      > ![Snímek obrazovky okna nedůvěryhodných rozšíření. Možnost "povoleno" je zvýrazněna červeně.](media/untrusted-extensions.png)


## <a name="add-an-msix-image-to-the-host-pool"></a>Přidání image MSIX do fondu hostitelů

Dál budete muset přidat image MSIX do fondu hostitelů.

Postup přidání image MSIX:

1. Otevřete web Azure Portal.

2. Do panelu hledání zadejte **virtuální klient Windows** a potom vyberte název služby.

      > [!div class="mx-imgBorder"]
      > ![Snímek obrazovky uživatele, který vybírá "virtuální plocha Windows" z rozevírací nabídky panelu hledání v Azure Portal. "Virtuální plocha Windows" je zvýrazněná červeně.](media/find-and-select.png)

3. Vyberte fond hostitelů, kam chcete umístit aplikace MSIX.

4. Výběrem **MSIX balíčky** otevřete datovou mřížku se všemi aktuálně přidanými **balíčky MSIX** do fondu hostitelů.

5. Výběrem **+ Přidat** otevřete kartu **Přidat balíček MSIX** .

6. Na kartě **Přidat balíček MSIX** zadejte následující hodnoty:

      - V poli **cesta k imagi MSIX** zadejte platnou cestu UNC ukazující na obrázek MSIX ve sdílené složce. (Například `\\storageaccount.file.core.windows.net\msixshare\appfolder\MSIXimage.vhd` .) Až budete hotovi, vyberte **Přidat** a dotazování kontejner MSIX a ověřte, jestli je cesta platná.

      - V části **balíček MSIX** vyberte příslušný název balíčku MSIX z rozevírací nabídky. Tato nabídka se naplní jenom v případě, že jste zadali platnou cestu k bitové kopii v **cestě k imagi MSIX**.

      - V případě **aplikací balíčku** se ujistěte, že seznam obsahuje všechny aplikace MSIX, které chcete zpřístupnit uživatelům v balíčku MSIX.

      - Případně můžete zadat **zobrazované jméno** , pokud chcete, aby byl balíček uživatelsky přívětivější v nasazeních uživatelů.

      - Ujistěte se, že má **verze** správné číslo verze.

      - Vyberte **typ registrace** , který chcete použít. Které použití závisí na vašich potřebách:

    - **Registrace na vyžádání** odloží úplnou registraci aplikace MSIX, dokud uživatel aplikaci nespustí. Toto je typ registrace, který doporučujeme použít.

    - Když se uživatel přihlašuje, **zablokuje** se jenom registrující Registry. Nedoporučujeme tento typ, protože to může vést k delšímu počtu přihlášení uživatelů.

7.  V případě **stav** vyberte preferovaný stav.
    -  **Aktivní** stav umožňuje uživatelům pracovat s balíčkem.
    -  Stav **neaktivní** má za následek, že virtuální plocha Windows ignoruje balíček a nedodá ho uživatelům.

8. Až budete hotovi, vyberte **Přidat**.

## <a name="publish-msix-apps-to-an-app-group"></a>Publikování aplikací pro MSIX do skupiny aplikací

V dalším kroku budete muset aplikace publikovat do balíčku. To musíte udělat pro skupiny aplikací Desktop a Remote App.

Pokud už máte image MSIX, přeskočte k [publikování aplikací MSIX do skupiny aplikací](#publish-msix-apps-to-an-app-group). Pokud chcete testovat starší verze aplikací, postupujte podle pokynů v tématu [Vytvoření balíčku MSIX z desktopového instalačního programu na virtuálním](/windows/msix/packaging-tool/create-app-package-msi-vm/) počítači, aby se starší verze aplikace převedla na balíček MSIX.

Publikování aplikací:

1. V poskytovateli prostředků virtuálního počítače se systémem Windows vyberte kartu **skupiny aplikací** .

2. Vyberte skupinu aplikací, do které chcete aplikace publikovat.

   >[!NOTE]
   >Aplikace MSIX se dají doručovat pomocí aplikace MSIX připojit ke vzdáleným aplikacím a skupinám desktopových aplikací.

3. Až budete ve skupině aplikací, vyberte kartu **aplikace** . V mřížce **aplikace** se zobrazí všechny existující aplikace ve skupině aplikací.

4. Výběrem **+ Přidat** otevřete kartu **Přidat aplikaci** .

      > [!div class="mx-imgBorder"]
      > ![Snímek obrazovky uživatele, který vyberete + Přidat, otevřete kartu Přidat aplikaci.](media/select-add.png)

5. V části **zdroj aplikace** vyberte zdroj vaší aplikace.
    - Pokud používáte skupinu desktopových aplikací, vyberte **balíček MSIX**.
      
      > [!div class="mx-imgBorder"]
      > ![Snímek obrazovky zákazníka, který vybírá balíček MSIX z rozevírací nabídky zdroje aplikace Balíček MSIX je zvýrazněný červeně.](media/select-source.png)
    
    - Pokud používáte skupinu vzdálených aplikací, vyberte jednu z následujících možností:
        
        - Nabídka Start
        - Cesta k aplikaci
        - Balíček MSIX

    - Do pole **název aplikace** zadejte popisný název aplikace.

    Můžete taky nakonfigurovat tyto volitelné funkce:
   
    - Do pole **Zobrazovaný název** zadejte nový název balíčku, který uvidí vaši uživatelé.

    - Jako **Popis** zadejte krátký popis balíčku aplikace.

    - Pokud používáte skupinu vzdálených aplikací, můžete nakonfigurovat také tyto možnosti:

        - **Cesta k ikoně**
        - **Index ikony**
        - **Zobrazit ve webovém kanálu**

6. Jakmile budete mít hotovo, vyberte **Uložit**.

>[!NOTE]
>Když je uživatel přiřazený ke skupině vzdálených aplikací a desktopové aplikaci ze stejného fondu hostitelů, v informačním kanálu se zobrazí skupina desktopová aplikace.

## <a name="assign-a-user-to-an-app-group"></a>Přiřazení uživatele ke skupině aplikací

Po přiřazení aplikací MSIX ke skupině aplikací budete muset uživatelům udělit přístup k nim. Přístup můžete přiřadit přidáním uživatelů nebo skupin uživatelů do skupiny aplikací s publikovanými MSIX aplikacemi. Podle pokynů v tématu [Správa skupin aplikací pomocí Azure Portal](manage-app-groups.md) přiřaďte uživatele do skupiny aplikací.

## <a name="change-msix-package-state"></a>Změnit stav balíčku MSIX

V dalším kroku budete muset změnit stav balíčku MSIX na **aktivní** nebo **neaktivní** v závislosti na tom, co chcete s balíčkem dělat. Aktivní balíčky jsou balíčky, se kterými uživatelé můžou pracovat, když je publikujete. Virtuální plocha Windows ignoruje neaktivní balíčky, takže uživatelé nemůžou pracovat s aplikacemi uvnitř.

### <a name="change-state-with-the-applications-list"></a>Změna stavu se seznamem aplikací

Postup změny stavu balíčku pomocí seznamu aplikací:

1. Přejít do fondu hostitelů a vybrat **balíčky MSIX**. Měl by se zobrazit seznam všech existujících balíčků MSIX v rámci fondu hostitelů.

2. Vyberte balíčky MSIX, jejichž stavy potřebujete změnit, a pak vyberte **změnit stav**.

### <a name="change-state-with-update-package"></a>Změnit stav pomocí balíčku aktualizace

Postup změny stavu balíčku pomocí balíčku aktualizace:

1. Přejít do fondu hostitelů a vybrat **balíčky MSIX**. Měl by se zobrazit seznam všech existujících balíčků MSIX v rámci fondu hostitelů.

2. V seznamu balíčků MSIX vyberte název balíčku, jehož stav chcete změnit. Tím se otevře karta **aktualizace balíčku** .

3. Přepínač **stavu** přepněte na **neaktivní** nebo **aktivní** a pak vyberte **Uložit.**

## <a name="change-msix-package-registration-type"></a>Změnit typ registrace balíčku MSIX

Postup změny typu registrace balíčku:

1. Vyberte **MSIX balíčky**. Měl by se zobrazit seznam všech existujících balíčků MSIX v rámci fondu hostitelů.

2. V **mřížce balíčků MSIX** vyberte **název balíčku** . tím se otevře okno pro aktualizaci balíčku.

3. V případě potřeby přepněte **typ registrace** prostřednictvím tlačítka **pro blokování na vyžádání nebo přihlášení** a vyberte **Uložit.**

## <a name="remove-an-msix-package"></a>Odebrání balíčku MSIX

Postup odebrání balíčku MSIX z fondu hostitelů:

1. Vyberte **MSIX balíčky**.  Měl by se zobrazit seznam všech existujících balíčků MSIX v rámci fondu hostitelů.

2. Vyberte tři tečky na pravé straně názvu balíčku, který chcete odstranit, a pak vyberte **Odebrat**.

## <a name="remove-msix-apps"></a>Odebrání aplikací MSIX

Odebrání jednotlivých aplikací MSIX z balíčku:

1. Přejít do fondu hostitelů a vybrat **skupiny aplikací**.

2. Vyberte skupinu aplikací, ze které chcete odebrat aplikace z MSIX.

3. Otevřete kartu **aplikace** .

4. Vyberte aplikaci, kterou chcete odebrat, a pak vyberte **Odebrat**.

## <a name="next-steps"></a>Další kroky

Zeptejte se naší komunity na tuto funkci na [virtuálním počítači s Windows TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Zpětnou vazbu k virtuálnímu počítači s Windows můžete také opustit v [centru pro zpětnou vazbu na virtuálním počítači s Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Tady jsou některé další články, které můžete najít užitečné:

- [Glosář připojení aplikace MSIX](app-attach-glossary.md)
- [DOTAZY k připojení aplikace MSIX](app-attach-faq.md)

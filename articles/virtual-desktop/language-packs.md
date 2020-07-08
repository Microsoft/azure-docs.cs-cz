---
title: Instalace jazykových sad na virtuální počítače s Windows 10 na virtuálním počítači s Windows – Azure
description: Jak nainstalovat jazykové sady pro virtuální počítače s Windows 10 s více relacemi na virtuálním počítači s Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 70592d940e3766597475f4a7b90a3902a53406d5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361298"
---
# <a name="install-language-packs"></a>Instalace jazykových sad

Když nakonfigurujete nasazení virtuálních klientů s Windows na mezinárodní úrovni, je vhodné zajistit, aby nasazení podporovalo několik jazyků. Jazykové sady můžete nainstalovat na image virtuálního počítače s Windows 10 Enterprise s více relacemi, abyste mohli podporovat tolik jazyků, kolik vaše organizace potřebuje. V tomto článku se dozvíte, jak nainstalovat jazykové sady a zachytit image, které umožní vašim uživatelům zvolit si vlastní jazyky zobrazení.

Další informace o tom, jak nasadit virtuální počítač v Azure, najdete [v tématu Vytvoření virtuálního počítače s Windows v zóně dostupnosti s Azure Portal](../virtual-machines/windows/create-portal-availability-zone.md).

>[!NOTE]
>Tento článek se týká virtuálních počítačů s Windows 10 Enterprise s více relacemi.

## <a name="install-a-language-pack"></a>Nainstalovat jazykovou sadu

Pokud chcete vytvořit image virtuálního počítače s jazykovými sadami, musíte nejdřív nainstalovat jazykové sady na počítač a zachytit jeho image.

Instalace jazykových sad:

1. Přihlaste se jako správce.
2. Ujistěte se, že máte nainstalované všechny nejnovější aktualizace pro Windows a Windows Store.
3. Přejít na **Nastavení**  >  **čas &**  >  **oblasti**jazyka.
4. V části **země nebo oblast**vyberte upřednostňovanou zemi nebo oblast z rozevírací nabídky.
    V tomto příkladu vybereme **Francii**, jak je znázorněno na následujícím snímku obrazovky:

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky stránky oblasti Aktuálně vybraná oblast je Francie.](media/region-page-france.png)

5. Potom vyberte **jazyk**a pak vyberte **Přidat jazyk**. V seznamu vyberte jazyk, který chcete nainstalovat, a pak vyberte **Další**.
6. Když se otevře okno **instalovat jazykové funkce** , zaškrtněte políčko **nainstalovat jazykovou sadu a nastavte možnost jazyk zobrazení systému Windows**.
7. Vyberte **Nainstalovat**.
8. Chcete-li přidat více jazyků najednou, vyberte možnost **Přidat jazyk**a poté opakujte postup, chcete-li přidat jazyk v krocích 5 a 6. Tento postup opakujte pro každý jazyk, který chcete nainstalovat. V jednom okamžiku však můžete nastavit pouze jeden jazyk jako jazyk zobrazení.

    Pojďme si projít rychlou vizuální ukázkou. Následující obrázky ukazují, jak nainstalovat francouzské a holandské jazykové sady a pak jako jazyk zobrazení nastavit francouzštinu.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky stránky jazyků na začátku procesu. Vybraný jazyk zobrazení systému Windows je angličtina.](media/language-page-default.png)

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky okna pro výběr jazyka Uživatel zadal do panelu hledání "francouzštinu", aby našel balíčky francouzského jazyka.](media/select-language-french.png)

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky se stránkou funkcí instalace jazyka Francouzština se vybere jako preferovaný jazyk. Vybrané možnosti jsou "nastavit jazyk zobrazení," nainstalovat jazykové sady, "rozpoznávání řeči" a "rukopis".](media/install-language-features.png)

    Po nainstalování jazykových sad se v seznamu jazyků zobrazí názvy jazykových sad.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky se stránkou jazyka s nainstalovanými novými jazykovými balíčky Jazykové sady pro francouzštinu a Nizozemsko jsou uvedené v části preferované jazyky.](media/language-page-complete.png)

9. Pokud se zobrazí okno s výzvou k odhlášení od vaší relace. Odhlaste se a znovu se přihlaste. Váš jazyk zobrazení by teď měl být vybraný jako jazyk.

10.  Přejdete na **ovládací panel**  >  **hodiny a oblast oblasti**  >  **Region**.

11.  Po otevření okna **region (oblast** ) vyberte kartu **Správa** a pak vyberte **Kopírovat nastavení**.

12.  Zaškrtněte políčka označená **Úvodní obrazovka a systémové účty** a **nové uživatelské účty**.

13.  Vyberte **OK**.

14.  Otevře se okno s oznámením o restartování vaší relace. Vyberte **restartovat nyní**.

15.  Až se znovu přihlásíte, vraťte se do **ovládacích panelů**  >  **hodiny a oblast oblasti**  >  **Region**.

16.  Vyberte kartu **Správa** .

17.  Vyberte **změnit národní prostředí systému**.

18. V rozevírací nabídce v části **aktuální národní prostředí systému**vyberte jazyk národního prostředí, který chcete použít. Pak vyberte **OK**.

19. Vyberte **restartovat nyní** , pokud chcete relaci znovu spustit znovu.

Gratulujeme, nainstalovali jste své jazykové sady.

Než budete pokračovat, ujistěte se, že má systém nainstalované nejnovější verze Windows a Windows Store.

## <a name="sysprep"></a>Sysprep

V dalším kroku se budete muset připravit na počítač, abyste ho připravili na proces zachytávání imagí.

Pro Sysprep počítač:

1. Otevřete Powershell jako správce.
2. Spuštěním následující rutiny přejdete do správného adresáře:

    ```powershell
    cd Windows\System32\Sysprep
    ```

3. Dále spusťte následující rutinu:

    ```powershell
    .\sysprep.exe
    ```

4. Po otevření okna nástroje pro přípravu systému zaškrtněte políčko s označením **generalizace**a pak přejděte na **Možnosti vypnutí** a v rozevírací nabídce vyberte možnost **vypnout** .

>[!NOTE]
>Dokončení procesu Syprep bude trvat několik minut. Když se virtuální počítač vypne, vaše Vzdálená relace se odpojí.

### <a name="resolve-sysprep-errors"></a>Vyřešit chyby nástroje Sysprep

Pokud se během procesu Sysprep zobrazí chybová zpráva, měli byste postupovat takto:

1. Otevřete **jednotku C** a klikněte na **Windows**  >  **system32 Sysprep**  >  **Panther**a pak otevřete soubor **Setuperr** .

   Text v souboru chyby vám sdělí, že musíte odinstalovat konkrétní jazykový balíček, jak je znázorněno na následujícím obrázku. Zkopírujte název jazykových balíčků pro další krok.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky souboru Setuperr. Text s názvem balíčku se zvýrazní tmavě modře.](media/setuperr-package-name.png)

2. Otevřete nové okno prostředí PowerShell a spuštěním následující rutiny s názvem balíčku, který jste zkopírovali v kroku 2, odeberte balíček jazyka:

   ```powershell
   Remove-AppxPackage <package name>
   ```

3. Ujistěte se, že jste balíček odebrali `Remove-AppxPackage` opětovným spuštěním rutiny. Pokud jste balíček úspěšně odebrali, měla by se zobrazit zpráva s informacemi o tom, že balíček, který se pokoušíte odebrat, není zde.

4. Spusťte `sysprep.exe` rutinu znovu.

## <a name="capture-the-image"></a>Zachytit bitovou kopii

Teď, když je váš systém připravený, můžete zachytit image, aby mohli ostatní uživatelé začít používat virtuální počítače založené na vašem systému, aniž by museli proces konfigurace opakovat.

Zachycení image:

1. Přejít na Azure Portal a vyberte název počítače, který jste nakonfigurovali v části [instalace jazykové sady](#install-a-language-pack) a [nástroje Sysprep](#sysprep).

2. Vyberte **zaznamenat**.

3. Do pole **název** zadejte název své image a přiřaďte ji do skupiny prostředků pomocí rozevírací nabídky **Skupina prostředků** , jak je znázorněno na následujícím obrázku.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky okna vytvořit obrázek Název, který uživatel přidělil této imagi testu, je "vmwvd-image-fr" a přiřadí se do skupiny prostředků "testwvdimagerg".](media/create-image.png)

4. Vyberte **Vytvořit**.

5. Počkejte několik minut, než se proces zachytávání dokončí. Když je bitová kopie připravená, měla by se v centru oznámení zobrazit zpráva s informacemi o tom, že se image zachytila.

Pomocí nové image teď můžete nasadit virtuální počítač. Při nasazení virtuálního počítače nezapomeňte postupovat podle pokynů v tématu [Vytvoření virtuálního počítače s Windows v zóně dostupnosti s Azure Portal](../virtual-machines/windows/create-portal-availability-zone.md).

### <a name="how-to-change-display-language-for-standard-users"></a>Změna jazyka zobrazení pro standardní uživatele

Standardní uživatelé můžou změnit jazyk zobrazení na svých virtuálních počítačích.

Změna jazyka zobrazení:

1. Přejít na **nastavení jazyka** Pokud si nejste jisti, kde to je, můžete zadat **jazyk** do panelu hledání v nabídce Start.

2. V rozevírací nabídce jazyk zobrazení systému Windows vyberte jazyk, který chcete použít jako jazyk zobrazení.

3. Odhlaste se z relace a pak se znovu přihlaste. Jazyk zobrazení by teď měl být ten, který jste vybrali v kroku 2.

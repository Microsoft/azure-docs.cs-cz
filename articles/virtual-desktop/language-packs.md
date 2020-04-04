---
title: Instalace jazykových sad na virtuální počítače s Windows 10 ve Windows Virtual Desktop – Azure
description: Jak nainstalovat jazykové sady pro vícerelační virtuální počítače windows 10 ve Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c40df9d821e069e2cd5ff0c42d5841f6b9041c96
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80634032"
---
# <a name="install-language-packs"></a>Instalace jazykových sad

Při mezinárodním nastavení nasazení windows virtuální plochy je vhodné zajistit, aby vaše nasazení podporovalo více jazyků. Jazykové sady můžete nainstalovat do image virtuálního počítače s více relacemi windows 10 Enterprise (VM) pro podporu tolika jazyků, kolik vaše organizace potřebuje. V tomto článku se můžete naučit instalovat jazykové sady a pořažit obrázky, které uživatelům umožní vybrat si vlastní jazyky zobrazení.

Další informace o tom, jak nasadit virtuální počítač v Azure na [vytvoření virtuálního počítače s Windows v zóně dostupnosti s portálem Azure](../virtual-machines/windows/create-portal-availability-zone.md).

>[!NOTE]
>Tento článek se vztahuje na vícerelační virtuální servery Windows 10 Enterprise.

## <a name="install-a-language-pack"></a>Instalace jazykové sady

Chcete-li vytvořit bitovou kopii virtuálního počítače s jazykovými sadami, musíte nejprve nainstalovat jazykové sady do počítače a zachytit jeho bitovou kopii.

Instalace jazykových sad:

1. Přihlaste se jako správce.
2. Ujistěte se, že jste nainstalovali všechny nejnovější aktualizace windows a windows store.
3. Přejděte do **oblasti** > **Nastavení času & jazyk** > **.**
4. V rozevírací nabídce vyberte v rozevírací nabídce položku **Země nebo oblast.**
    V tomto příkladu vybereme **Francii**, jak je znázorněno na následujícím snímku obrazovky:

    ![Snímek obrazovky se stránkou Oblast. Aktuálně vybraný region je Francie.](media/region-page-france.png)

5. Poté vyberte **Jazyk**a pak vyberte **Přidat jazyk**. V seznamu vyberte jazyk, který chcete nainstalovat, a pak vyberte **Další**.
6. Po otevření okna **Funkce instalace jazyka** zaškrtněte políčko Instalovat jazyk ovou sadu a nastavte jako jazyk zobrazení **systému Windows**.
7. Vyberte **Install** (Nainstalovat).
8. Chcete-li přidat více jazyků najednou, vyberte **Přidat jazyk**a opakujte proces a přidejte jazyk v krocích 5 a 6. Tento postup opakujte pro každý jazyk, který chcete nainstalovat. Jako jazyk zobrazení však můžete nastavit pouze jeden jazyk.

    Projedeme rychlou vizuální ukázku. Následující obrázky ukazují, jak nainstalovat jazykové sady francouzština a nizozemština, a poté nastavit francouzštinu jako jazyk zobrazení.

    ![Snímek obrazovky se stránkou Jazyk na začátku procesu. Vybraným jazykem zobrazení systému Windows je angličtina.](media/language-page-default.png)

    ![Snímek obrazovky okna pro výběr jazyka Uživatel zadal "francouzština" do vyhledávacího panelu najít francouzské jazykové balíčky.](media/select-language-french.png)

    ![Snímek obrazovky stránky Funkce instalace jazyka Jako upřednostňovaný jazyk je vybrána francouzština. Vybrané možnosti jsou "Nastavit jazyk zobrazení", "Nainstalovat jazykovou sadu", "Rozpoznávání řeči" a "Rukopis".](media/install-language-features.png)

    Po instalaci jazykových sad by se měly zobrazit názvy jazykových sad v seznamu jazyků.

    ![Snímek obrazovky s naváděnými jazykovými sadami s nainstalovanými novými jazykovými sadami Jazykové sady francouzština a Nizozemsko jsou uvedeny pod "preferovanými jazyky".](media/language-page-complete.png)

9. Pokud se zobrazí okno s žádostí o odhlášení z relace. Odhlaste se a znovu se přihlaste. Jazyk zobrazení by nyní měl být jazykem, který jste vybrali.

10.  Přejděte do oblasti**Hodin a oblasti ovládacího** >  **panelu** > .**Region**

11.  Po otevření okna **Oblast** vyberte kartu **Správa** a pak vyberte **Kopírovat nastavení**.

12.  Zaškrtněte políčka označená **uvítací obrazovkou a systémovými účty** a **novými uživatelskými účty**.

13.  Vyberte **OK**.

14.  Otevře se okno a řekne vám, abyste relaci restartovali. Vyberte **možnost Restartovat nyní**.

15.  Po přihlášení zpět se vraťte do**oblasti****Hodin a oblast** >  **ovládacího panelu** > .

16.  Vyberte kartu **Správa.**

17.  Vyberte **možnost Změnit národní prostředí systému**.

18. V rozevírací nabídce v části **Aktuální národní prostředí systému**vyberte jazyk národního prostředí, který chcete použít. Poté vyberte **ok**.

19. Chcete-li relaci znovu restartovat, vyberte **možnost Restartovat.**

Gratulujeme, nainstalovali jste jazykové balíčky!

Než budete pokračovat, ujistěte se, že je v systému nainstalovány nejnovější verze Windows a Windows Store.

## <a name="sysprep"></a>Sysprep

Dále je třeba sysprep počítače připravit jej pro proces zachytávání obrazu.

Chcete-li sysprep vašeho počítače:

1. Otevřete Powershell jako správce.
2. Spuštěním následující rutiny přejděte do správného adresáře:
   
    ```powershell
    cd Windows\System32\Sysprep
    ```

3. Dále spusťte následující rutinu:
    
    ```powershell
    .\sysprep.exe
    ```

4. Po otevření okna Nástroj pro přípravu systému zaškrtněte políčko **Generalize**s názvem Generalize , přejděte na **Možnosti vypnutí** a v rozevírací nabídce vyberte **Vypnout.**

>[!NOTE]
>Dokončení procesu syprepu bude trvat několik minut. Při vypnutí virtuálního počítače se vaše vzdálená relace odpojí.

### <a name="resolve-sysprep-errors"></a>Řešení chyb sysprep

Pokud se během procesu sysprepu zobrazí chybová zpráva, měli byste udělat toto:

1. Otevřete **jednotku C** a přejděte na **windows** > **system32 Sysprep** > **Panther**a potom otevřete soubor **setuperr.**

   Text v souboru chyb y vám řekne, že je třeba odinstalovat konkrétní jazykový balíček, jak je znázorněno na následujícím obrázku. Zkopírujte název jazykového balíčku pro další krok.

   ![Snímek obrazovky se souborem setuperr. Text s názvem balíčku je zvýrazněn tmavě modře.](media/setuperr-package-name.png)

2. Otevřete nové okno Prostředí PowerShell a spusťte následující rutinu s názvem balíčku, který jste zkopírovali v kroku 2, a odeberte jazykový balíček:

   ```powershell
   Remove-AppxPackage <package name>
   ```

3. Zkontrolujte, zda jste balíček odebrali, znovu spuštěním `Remove-AppxPackage` rutiny. Pokud jste balíček úspěšně odebrali, měla by se zobrazit zpráva, že balíček, který se pokoušíte odebrat, neexistuje.

4. Spusťte rutinu `sysprep.exe` znovu.

## <a name="capture-the-image"></a>Zachycení obrázku

Teď, když je váš systém připravený, můžete zachytit bitovou kopii, aby ostatní uživatelé mohli začít používat virtuální počítače založené na vašem systému, aniž by museli opakovat proces konfigurace.

Jak pořit snímek:

1. Přejděte na portál Azure a vyberte název počítače, který jste nakonfigurovali, v [části Instalace jazykové sady](#install-a-language-pack) a programu [sysprep](#sysprep).

2. Vyberte **možnost Zachytit**.

3. Do pole **Název** zadejte název obrázku a přiřaďte jej skupině prostředků pomocí rozevírací nabídky **Skupina prostředků,** jak je znázorněno na následujícím obrázku.

   ![Snímek obrazovky okna Vytvořit obrázek Jméno, které uživatel přidělil této testovací bitové kopii, je "vmwvd-image-fr" a přiřadil jej skupině prostředků "testwvdimagerg".](media/create-image.png)

4. Vyberte **Vytvořit**.

5. Počkejte několik minut na dokončení procesu sběru. Až bude obrázek připraven, měla by se v Centru oznámení zobrazit zpráva s oznámením, že byl snímek pořízen.

Teď můžete nasadit virtuální počítač pomocí nové image. Při nasazování virtuálního počítače postupujte podle pokynů v části [Vytvoření virtuálního počítače s Windows v zóně dostupnosti s portálem Azure](../virtual-machines/windows/create-portal-availability-zone.md).

### <a name="how-to-change-display-language-for-standard-users"></a>Jak změnit jazyk zobrazení pro standardní uživatele

Standardní uživatelé můžou změnit jazyk zobrazení na svých virtuálních počítačích.

Změna jazyka zobrazení:

1. Přejděte na **Nastavení jazyka**. Pokud nevíte, kde to je, můžete zadat **jazyk** do vyhledávacího řádku v nabídce Start.

2. V rozevírací nabídce Jazyk zobrazení systému Windows vyberte jazyk, který chcete použít jako jazyk zobrazení.

3. Odhlaste se ze svého sezení a pak se znovu přihlaste. Jazyk zobrazení by nyní měl být jazykem, který jste vybrali v kroku 2.

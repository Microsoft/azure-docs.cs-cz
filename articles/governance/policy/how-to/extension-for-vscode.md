---
title: Rozšíření zásad Azure pro kód Visual Studia
description: Přečtěte si, jak pomocí rozšíření zásad Azure pro kód Visual Studia vyhledat aliasy Správce prostředků.
ms.date: 03/07/2020
ms.topic: how-to
ms.openlocfilehash: 5e31af652f2746adbf0eda386bdb178c752f5f84
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641022"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>Použití rozšíření zásad Azure pro kód Visual Studia

> Platí pro rozšíření rozšíření zásad Azure **verze 0.0.21** a novější

Zjistěte, jak pomocí rozšíření zásad Azure pro Visual Studio Code vyhledat [aliasy](../concepts/definition-structure.md#aliases) a zkontrolovat prostředky a zásady. Nejprve popíšeme, jak nainstalovat rozšíření zásad Azure v kódu Visual Studia. Pak si projdeme, jak vyhledat aliasy.

Rozšíření zásad Azure pro visual studio kód lze nainstalovat na všech platformách, které jsou podporované Visual Studio Code. Tato podpora zahrnuje Windows, Linux a macOS.

> [!NOTE]
> Změny provedené místně zásad zobrazené v rozšíření zásad Azure pro visual studio kód nejsou synchronizovány s Azure.

## <a name="prerequisites"></a>Požadavky

Následující položky jsou vyžadovány pro dokončení kroků v tomto článku:

- Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.
- [Visual Studio kód](https://code.visualstudio.com).

## <a name="install-azure-policy-extension"></a>Instalace rozšíření zásad Azure

Po splnění požadavků můžete nainstalovat rozšíření zásad Azure pro Visual Studio Code takto:

1. Otevřete Visual Studio Code.

1. Na řádku nabídek přejděte na **Zobrazit** > **rozšíření**.

1. Do vyhledávacího pole zadejte **Zásady Azure**.

1. Ve výsledcích hledání vyberte **Zásady Azure** a pak vyberte **Instalovat**.

1. V případě potřeby vyberte **znovu načíst.**

## <a name="set-the-azure-environment"></a>Nastavení prostředí Azure

Pro uživatele národního cloudu nejprve nastavte prostředí Azure takto:

1. Vyberte **Soubor\Předvolby\Nastavení**.

1. Hledání podle následujícího řetězce: _Azure: Cloud_

1. Ze seznamu vyberte národní cloud:

   ![Nastavení výchozího přihlášení do cloudu Azure pro kód Visual Studia](../media/extension-for-vscode/set-default-azure-cloud-sign-in.png)

## <a name="connect-to-an-azure-account"></a>Připojení k účtu Azure

Chcete-li vyhodnotit prostředky a vyhledávací aliasy, musíte se připojit ke svému účtu Azure. Chcete-li se připojit k Azure z Visual Studia Code, postupujte takto:

1. Přihlaste se k Azure z rozšíření zásad Azure nebo palety příkazů.

   - Rozšíření zásad Azure

     Z rozšíření Zásad Azure vyberte **Přihlásit se k Azure**.

     ![Přihlášení ke cloudu Azure pro kód Visual Studia z rozšíření zásad Azure](../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png)

   - Paleta příkazů

     Na řádku nabídek přejděte na **Zobrazit** > **paletu příkazů**a zadejte **Azure: Přihlásit se**.

     ![Přihlášení k cloudu Azure pro kód Visual Studia z palety příkazů](../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png)

1. Podle přihlašovacích pokynů se přihlaste do Azure. Po připojení se název účtu Azure zobrazí na stavovém řádku v dolní části okna Kódu Visual Studia.

## <a name="select-subscriptions"></a>Vybrat předplatná

Při prvním přihlášení jsou pouze výchozí prostředky předplatného a zásady načteny rozšíření zásad Azure. Chcete-li přidat nebo odebrat předplatná ze zobrazení prostředků a zásad, postupujte takto:

1. Spusťte příkaz předplatného z palety příkazů nebo zápatí okna.

   - Paleta příkazů: 

     Na řádku nabídek přejděte na **Zobrazit** > **paletu příkazů**a zadejte **Azure: Vyberte předplatná**.

   - Zápatí okna

     V zápatí okna v dolní části obrazovky vyberte segment, který odpovídá **Azure: \<váš účet\>**.

1. Pomocí pole filtru můžete rychle najít předplatná podle názvu. Potom zkontrolujte nebo odeberte kontrolu z každého předplatného a nastavte předplatná zobrazená v rozšíření zásad Azure. Po přidání nebo odebrání odběrů, které chcete zobrazit, vyberte **ok**.

## <a name="search-for-and-view-resources"></a>Hledání a zobrazení zdrojů

Rozšíření zásad Azure uvádí prostředky ve vybraných předplatných podle zprostředkovatele prostředků a podle skupiny prostředků v podokně **prostředků.** Stromové zobrazení obsahuje následující seskupení prostředků v rámci vybraného předplatného nebo na úrovni předplatného:

- **Poskytovatelé prostředků**
  - Každý registrovaný zprostředkovatel prostředků s prostředky a souvisejícípodřízené prostředky, které mají aliasy zásad
- **Skupiny prostředků**
  - Všechny prostředky podle skupiny prostředků, ve které se najdou

Ve výchozím nastavení rozšíření filtruje část Zprostředkovatel prostředků podle existujících prostředků a prostředků, které mají aliasy zásad. Změňte toto chování v **rozšíření** > **nastavení** > **Azure Zásady** zobrazíte všechny zprostředkovatele prostředků bez filtrování.

Zákazníci se stovkami nebo tisíci prostředků v jednom předplatném mohou upřednostňovat prohledávatelný způsob, jak najít své prostředky. Rozšíření zásad Azure umožňuje vyhledat konkrétní prostředek pomocí následujících kroků:

1. Spusťte vyhledávací rozhraní z rozšíření zásad Azure nebo palety příkazů.

   - Rozšíření zásad Azure

     V rozšíření Zásad Azure najeďte na panel **Prostředky** a vyberte tři tečky a pak vyberte **Hledat prostředky**.

   - Paleta příkazů:

     Na řádku nabídek přejděte na **Zobrazit** > **paletu příkazů**a zadejte **Zdroje: Hledat zdroje**.

1. Pokud je pro zobrazení vybráno více než jedno předplatné, vyberte pomocí filtru, které předplatné chcete vyhledat.

1. Pomocí filtru vyberte skupinu prostředků, která má být prohledána a která je součástí dříve zvoleného předplatného.

1. Pomocí filtru vyberte, který zdroj se má zobrazit. Filtr funguje jak pro název prostředku, tak pro typ prostředku.

## <a name="discover-aliases-for-resource-properties"></a>Zjišťování aliasů pro vlastnosti prostředků

Když je vybrán prostředek, ať už prostřednictvím vyhledávacího rozhraní nebo jeho výběrem v zobrazení stromu, rozšíření Zásad azure otevře soubor JSON představující tento prostředek a všechny jeho hodnoty vlastností Správce prostředků.

Po otevření prostředku se na jenom nad názvem nebo hodnotou správce prostředků zobrazí alias Zásad azure, pokud existuje. V tomto příkladu je `Microsoft.Compute/virtualMachines` prostředek typ prostředku a vlastnost **properties.storageProfile.imageReference.offer** je nad. Najetím je zobrazeny odpovídající aliasy.

![Vznášející se rozšíření zásad Azure zobrazuje alias vlastnosti Správce prostředků.](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

## <a name="search-for-and-view-policies-and-assignments"></a>Hledání a zobrazení zásad a přiřazení

Rozšíření zásad Azure uvádí typy zásad a přiřazení zásad jako stromové zobrazení pro vybraná předplatná, která se mají zobrazit v podokně **Zásady.** Zákazníci se stovkami nebo tisíci zásad nebo přiřazení v jednom předplatném mohou upřednostňovat prohledávatelný způsob, jak najít své zásady nebo přiřazení. Rozšíření zásad Azure umožňuje vyhledat konkrétní zásady nebo přiřazení pomocí následujících kroků:

1. Spusťte vyhledávací rozhraní z rozšíření zásad Azure nebo palety příkazů.

   - Rozšíření zásad Azure

     V rozšíření Zásad Azure najeďte na panel **Zásady** a vyberte tři tečky a vyberte **Zásady vyhledávání**.

   - Paleta příkazů:

     Na řádku nabídek přejděte na **Zobrazit** > **paletu příkazů**a zadejte **Zásady: Zásady hledání**.

1. Pokud je pro zobrazení vybráno více než jedno předplatné, vyberte pomocí filtru, které předplatné chcete vyhledat.

1. Pomocí filtru vyberte typ zásady nebo přiřazení k vyhledávání, které je součástí dříve zvoleného předplatného.

1. Pomocí filtru vyberte, které zásady nebo které se mají zobrazit. Filtr funguje pro _displayName_ pro definici zásady nebo přiřazení zásad.

Při výběru zásady nebo přiřazení, ať už prostřednictvím vyhledávacího rozhraní nebo výběrem v zobrazení stromu, rozšíření zásad Azure otevře JSON, který představuje zásady nebo přiřazení a všechny jeho hodnoty vlastností Správce prostředků. Rozšíření můžete ověřit otevřené schéma Azure Policy JSON.

## <a name="sign-out"></a>Odhlásit se

Na řádku nabídek přejděte na **Zobrazit** > **paletu příkazů**a zadejte **Azure: Odhlásit se**.

## <a name="next-steps"></a>Další kroky

- Projděte si příklady na [ukázkách zásad Azure](../samples/index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).
- Pochopit, jak [programově vytvářet zásady](programmatically-create.md).
- Přečtěte si, jak [napravit nekompatibilní prostředky](remediate-resources.md).
- Zkontrolujte, co je skupina pro správu [pomocí organizace Uspořádat prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).
---
title: Azure Policy rozšíření pro Visual Studio Code
description: Přečtěte si, jak pomocí rozšíření Azure Policy Visual Studio Code vyhledat Správce prostředků aliasy.
ms.date: 11/04/2019
ms.topic: how-to
ms.openlocfilehash: 48ef098cf970b5128185c40c92f8bb02ad8d9698
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873110"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>Použít rozšíření Azure Policy pro Visual Studio Code

> Platí pro Azure Policy rozšíření verze **0.0.21** a novější.

Naučte se používat rozšíření Azure Policy pro Visual Studio Code k vyhledání [aliasů](../concepts/definition-structure.md#aliases) a kontrole prostředků a zásad. Nejdřív popíšeme, jak nainstalovat rozšíření Azure Policy v Visual Studio Code. Pak vás provedeme vyhledáním aliasů.

Azure Policy rozšíření pro Visual Studio Code lze nainstalovat na všechny platformy, které Visual Studio Code podporuje. Tato podpora zahrnuje Windows, Linux a macOS.

> [!NOTE]
> Změny provedené lokálně v zásadách zobrazených v rozšíření Azure Policy pro Visual Studio Code se nesynchronizují do Azure.

## <a name="prerequisites"></a>Předpoklady

K dokončení kroků v tomto článku jsou vyžadovány následující položky:

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- [Visual Studio Code](https://code.visualstudio.com).

## <a name="install-azure-policy-extension"></a>Nainstalovat rozšíření Azure Policy

Po splnění požadavků můžete nainstalovat rozšíření Azure Policy pro Visual Studio Code pomocí následujících kroků:

1. Otevřete Visual Studio Code.

1. V řádku nabídek přejděte na **zobrazit** > **rozšíření**.

1. Do vyhledávacího pole zadejte **Azure Policy**.

1. Ve výsledcích hledání vyberte **Azure Policy** a pak vyberte **nainstalovat**.

1. V případě potřeby vyberte **znovu načíst** .

## <a name="set-the-azure-environment"></a>Nastavení prostředí Azure

V případě národního cloudového uživatele použijte následující postup a nastavte prostředí Azure jako první:

1. Vyberte **File\Preferences\Settings**.

1. Hledat v následujícím řetězci: _Azure: Cloud_

1. Ze seznamu Vyberte cloudovou zemi:

   ![Nastavení výchozího cloudového přihlášení Azure pro Visual Studio Code](../media/extension-for-vscode/set-default-azure-cloud-sign-in.png)

## <a name="connect-to-an-azure-account"></a>Připojení k účtu Azure

K vyhodnocení prostředků a aliasů pro vyhledávání musíte být připojeni ke svému účtu Azure. Pomocí těchto kroků se připojte k Azure z Visual Studio Code:

1. Přihlaste se k Azure z rozšíření Azure Policy nebo z palety příkazů.

   - Rozšíření Azure Policy

     V rozšíření Azure Policy vyberte **Přihlásit se k Azure**.

     ![Přihlášení do cloudu Azure pro Visual Studio Code z rozšíření Azure Policy](../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png)

   - Paleta příkazů

     V řádku nabídek přejděte na **zobrazit** > **paleta příkazů**a zadejte **Azure: přihlásit**se.

     ![Přihlášení k Azure cloudu pro Visual Studio Code z palety příkazů](../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png)

1. Postupujte podle pokynů pro přihlášení a přihlaste se k Azure. Po připojení se váš název účtu Azure zobrazí na stavovém řádku v dolní části okna Visual Studio Code.

## <a name="select-subscriptions"></a>Vybrat předplatná

Při prvním přihlášení se rozšíření Azure Policy načte jenom výchozí prostředky a zásady předplatného. Pokud chcete přidat nebo odebrat předplatná, která se budou zobrazovat v prostředcích a zásadách, použijte následující postup:

1. Spusťte příkaz odběr z palety příkazů nebo z zápatí okna.

   - Paleta příkazů: 

     V řádku nabídek přejděte na **zobrazit** > **paleta příkazů**a zadejte **Azure Policy: Vyberte odběry**.

   - Zápatí okna

     V zápatí okna v dolní části obrazovky vyberte segment, který odpovídá **Azure: \<\>vašeho účtu** .

1. K rychlému vyhledání předplatných podle názvu použijte pole filtru. Potom zaškrtněte nebo odstraňte kontrolu z každého předplatného a nastavte odběry zobrazené rozšířením Azure Policy. Až se dokončí přidávání nebo odebírání předplatných k zobrazení, vyberte **OK**.

## <a name="search-for-and-view-resources"></a>Hledání a zobrazení prostředků

Rozšíření Azure Policy obsahuje seznam prostředků v vybraných předplatných podle poskytovatele prostředků a skupiny prostředků v podokně **prostředky** . Stromová kopie obsahuje následující seskupení prostředků v rámci vybraného předplatného nebo na úrovni předplatného:

- **Poskytovatelé prostředků**
  - Každý registrovaný poskytovatel prostředků s prostředky a souvisejícími podřízenými prostředky, které mají aliasy zásad
- **Skupiny prostředků**
  - Všechny prostředky podle skupiny prostředků, na kterých se nachází

Ve výchozím nastavení rozšíření filtruje část poskytovatele prostředků pomocí existujících prostředků a prostředků, které mají aliasy zásad. Toto chování můžete změnit v **nastavení** > **rozšíření** > **Azure Policy** , abyste viděli všechny poskytovatele prostředků bez filtrování.

Zákazníci se stovkami nebo tisíci prostředků v rámci jednoho předplatného můžou preferovat způsob, jak vyhledat své prostředky. Rozšíření Azure Policy umožňuje vyhledat konkrétní prostředek pomocí následujících kroků:

1. Spusťte vyhledávací rozhraní z rozšíření Azure Policy nebo paleta příkazů.

   - Rozšíření Azure Policy

     Z rozšíření Azure Policy najeďte myší na panel **prostředky** a vyberte tři tečky a pak vyberte **Hledat prostředky**.

   - Paleta příkazů:

     V řádku nabídek přejděte na **zobrazit** > **paleta příkazů**a zadejte **prostředky: Hledat prostředky**.

1. Pokud je vybráno více než jedno předplatné k zobrazení, použijte filtr k výběru předplatného, které chcete vyhledat.

1. Pomocí filtru vyberte skupinu prostředků, kterou chcete vyhledat, která je součástí dříve zvoleného předplatného.

1. Pomocí filtru vyberte, který prostředek se má zobrazit. Filtr funguje jak pro název prostředku, tak pro typ prostředku.

## <a name="discover-aliases-for-resource-properties"></a>Zjištění aliasů pro vlastnosti prostředku

Pokud je vybraný prostředek, ať už přes vyhledávací rozhraní, nebo ho vyberete v ovládacím prvku TreeView, Azure Policy rozšíření otevře soubor JSON, který představuje tento prostředek a všechny jeho Správce prostředků hodnoty vlastností.

Jakmile je prostředek otevřený, najeďte myší na Správce prostředků název vlastnosti nebo hodnota zobrazí alias Azure Policy, pokud jeden existuje. V tomto příkladu je prostředkem typ prostředku `Microsoft.Compute/virtualMachines` a vlastnost **. storageProfile. element imagereference. Offer** je najetí myší. Při najetí myší se zobrazí vyhovující aliasy.

![Azure Policy se při najetí myší zobrazuje alias vlastnosti Správce prostředků](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

## <a name="search-for-and-view-policies-and-assignments"></a>Hledání a zobrazení zásad a přiřazení

Rozšíření Azure Policy obsahuje seznam typů zásad a přiřazení zásad jako strom pro předplatná, která se mají zobrazit v podokně **zásady** . Zákazníci se stovkami nebo tisíci zásad nebo přiřazení v rámci jednoho předplatného můžou upřednostňovat způsob, jak vyhledat své zásady nebo přiřazení. Rozšíření Azure Policy umožňuje vyhledat konkrétní zásadu nebo přiřazení pomocí následujících kroků:

1. Spusťte vyhledávací rozhraní z rozšíření Azure Policy nebo paleta příkazů.

   - Rozšíření Azure Policy

     Z rozšíření Azure Policy najeďte myší na panel **zásady** a vyberte tři tečky a pak vyberte **zásady hledání**.

   - Paleta příkazů:

     V řádku nabídek přejděte na **zobrazit** > **paleta příkazů**a zadejte **zásady: hledání zásad**.

1. Pokud je vybráno více než jedno předplatné k zobrazení, použijte filtr k výběru předplatného, které chcete vyhledat.

1. Pomocí filtru vyberte, který typ zásad nebo přiřazení chcete vyhledat, který je součástí dříve zvoleného předplatného.

1. Pomocí filtru vyberte, které zásady nebo které chcete zobrazit. Filtr pracuje s parametrem _DisplayName_ pro definici zásady nebo přiřazení zásady.

Když vyberete zásadu nebo přiřazení, ať už přes vyhledávací rozhraní, nebo ho vyberete v ovládacím prvku TreeView, Azure Policy rozšíření otevře JSON, který představuje zásadu nebo přiřazení a všechny jeho Správce prostředků hodnoty vlastností. Rozšíření může ověřit otevřené schéma JSON Azure Policy.

## <a name="sign-out"></a>Odhlásit se

V řádku nabídek přejděte na **zobrazit** > **paleta příkazů**a pak zadejte **Azure: odhlásit**se.

## <a name="next-steps"></a>Další kroky

- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).
- Zjistěte, jak [programově vytvářet zásady](programmatically-create.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).
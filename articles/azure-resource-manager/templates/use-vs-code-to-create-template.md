---
title: Vytváření šablon pomocí Visual Studio Code
description: Naučte se instalovat a používat Visual Studio Code a rozšíření Azure Resource Manager Tools.
author: mumian
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: jgao
ms.openlocfilehash: 585e2773ea5d6af184f85e65b63d39b60d632146
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273628"
---
# <a name="use-visual-studio-code-to-create-azure-resource-manager-templates"></a>Vytvoření šablon Azure Resource Manager pomocí Visual Studio Code

Visual Studio Code je jednoduchý multiplatformní Editor open source. Rozšíření nástrojů pro šablony Azure Resource Manager je modul plug-in pro Správce prostředků vývoj šablon. Rozšíření přidává jazykovou podporu pro šablony, která poskytuje IntelliSense, zvýrazňování syntaxe, online nápovědu a mnoho dalších funkcí jazyka. Společně poskytují Doporučené vývojové prostředí pro šablony.

## <a name="install-visual-studio-code"></a>Nainstalovat editor Visual Studio Code

Visual Studio Code podporuje MacOS, Windows a Linux.  Dá se nainstalovat z [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-resource-manager-tools-extension"></a>Rozšíření pro instalaci Správce prostředků nástrojů

1. Otevřete Visual Studio Code.
1. V nabídce vlevo vyberte **rozšíření** . Nebo v nabídce **zobrazení** vyberte **rozšíření** a otevřete podokno rozšíření.

    ![Nainstalovat rozšíření Visual Studio Code Správce prostředků nástrojů](./media/use-vs-code-to-create-template/resource-manager-visual-studio-code-tools-extension.png)
1. Vyhledejte **Správce prostředků**.
1. V části **Azure Resource Manager nástroje**vyberte **instalovat** .

## <a name="the-extension-features"></a>Funkce rozšíření

### <a name="colorization-for-template-language-expressions"></a>Zabarvení pro výrazy jazyka šablon

Parametry, proměnné, funkce, názvy a výrazy jsou barevně kódované, jak je znázorněno na následujícím snímku obrazovky:

![Barevné navýšení rozšíření Visual Studio Code Správce prostředků nástrojů](./media/use-vs-code-to-create-template/resource-manager-tools-extension-colorization.png)

Zobrazení osnovy šablon usnadňuje procházení velkých šablon.

### <a name="intellisense"></a>IntelliSense

Rozšíření šablony Správce prostředků ví možné dokončování názvů funkcí, parametrů, proměnných a odkazů. Při psaní se automaticky otevře návrh IntelliSense. Pokud budete pokračovat v zadávání znaků, seznam členů (proměnné, metody atd.) je filtrován tak, aby zahrnoval pouze členy, které obsahují zadané znaky. Stisknutím klávesy **TAB** nebo **ENTER** vložíte vybraného člena.

- Názvy předdefinovaných funkcí

    ![Rozšíření Visual Studio Code Správce prostředků nástrojů funkce IntelliSense](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-functions.png)

- Odkazy na parametry

    ![Rozšíření Visual Studio Codech nástrojů Správce prostředků Tools – parametry IntelliSense](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-parameters.png)

- Odkazy na proměnné

    ![Visual Studio Code Správce prostředků nástrojů rozšíření – proměnné technologie IntelliSense](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-variables.png)

- vlastnosti Resource ()

    ![Rozšíření Visual Studio Code Správce prostředků nástrojů funkce IntelliSense](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-resourcegroup.png)

Kromě toho technologie IntelliSense funguje také s vlastnostmi předplatného () a vlastnostmi odkazů na proměnné, které jsou objekty.

### <a name="signature-help-for-function-parameters"></a>Nápovědu k podpisu pro parametry funkce

Při najetí myší na názvy funkcí rozšíření zobrazuje nápovědu k podpisu pro parametry funkce.

![Funkce signatury rozšíření Visual Studio Code Správce prostředků nástrojů](./media/use-vs-code-to-create-template/resource-manager-tools-extension-signature-function.png)

### <a name="go-to-definition-for-variable-and-parameter-references"></a>Přejít k definici pro proměnné a odkazy na parametry

Můžete přejít k definici pomocí **kombinace kláves Ctrl + kliknutí**nebo pomocí kontextové nabídky, jak je znázorněno na snímku obrazovky: ![Visual Studio Code správce prostředků rozšíření nástrojů přejděte na definice](./media/use-vs-code-to-create-template/resource-manager-tools-extension-context-menu.png)

Definici můžete otevřít na stranu **stisknutím kombinace kláves Ctrl + Alt + kliknutí**.

### <a name="peek-for-variable-and-parameter-definitions"></a>Náhled pro definice proměnných a parametrů

Chcete-li otevřít prohlížený editor, použijte kontextovou nabídku, jak je znázorněno na předchozím snímku obrazovky.

Následující snímek obrazovky ukazuje Editor náhledu:

![Editor náhledu rozšíření Visual Studio Code Správce prostředků nástrojů](./media/use-vs-code-to-create-template/resource-manager-tools-extension-peek-editor.png)

### <a name="find-all-references-for-variables-and-parameters"></a>Najít všechny odkazy na proměnné a parametry

Chcete-li najít všechny odkazy, použijte kontextovou nabídku, jak je znázorněno na předchozím snímku obrazovky.

Následující snímek obrazovky ukazuje, jak jsou zvýrazněny odkazy:

![Rozšíření Visual Studio Code Správce prostředků nástrojů najít všechny odkazy](./media/use-vs-code-to-create-template/resource-manager-tools-extension-find-all-references.png)

### <a name="rename-all-references-for-variables-and-parameters"></a>Přejmenovat všechny odkazy na proměnné a parametry

Chcete-li přejmenovat všechny odkazy na proměnné a parametry, použijte kontextovou nabídku, jak je znázorněno na předchozím snímku obrazovky.

### <a name="hover-for-parameter-description"></a>Najeďte myší na popis parametru

![Definice ponechání rozšíření Visual Studio Code Správce prostředků nástrojů](./media/use-vs-code-to-create-template/resource-manager-tools-extension-hover-parameters.png)

### <a name="brace-matching"></a>Spárování složených závorek

Párové závorky se zvýrazní, jakmile se ukazatel myši poblíž jednoho z nich. Když kliknete na složenou závorku, zvýrazní se i dvojice závorek, jak je znázorněno na následujícím snímku obrazovky:

![Visual Studio Code Správce prostředků nástrojů rozšíření – shoda závorek](./media/use-vs-code-to-create-template/resource-manager-tools-extension-brace-matching.png)

### <a name="show-errors-and-warnings"></a>Zobrazit chyby a upozornění

Mezi chyby identifikované rozšířením patří:

- Nedefinované odkazy na parametry
- Nedefinované odkazy na proměnné
- Nerozpoznané názvy funkcí
- použití funkce reference () v definici proměnné
- Nesprávný počet argumentů ve funkcích

Mezi upozornění patří:

- Nepoužité parametry
- Nepoužité proměnné

## <a name="next-steps"></a>Další kroky

- Další informace o vytváření šablon Azure Resource Manager najdete v tématu [kurz: vytvoření a nasazení první šablony Azure Resource Manager](template-tutorial-create-first-template.md).
- Postup rychlý Start pomocí Visual Studio Code najdete v tématu [rychlý Start: vytvoření Azure Resource Manager šablon pomocí Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md)

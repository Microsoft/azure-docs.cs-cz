---
title: Vytvoření šablon pomocí kódu Visual Studia
description: Přečtěte si, jak nainstalovat a používat kód Visual Studia a rozšíření Nástroje Správce prostředků Azure.
author: mumian
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: jgao
ms.openlocfilehash: 585e2773ea5d6af184f85e65b63d39b60d632146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273628"
---
# <a name="use-visual-studio-code-to-create-azure-resource-manager-templates"></a>Vytvoření šablon Azure Resource Manageru pomocí kódu Visual Studia

Visual Studio Code je lehký, multiplatformní editor s otevřeným zdrojovým kódem. Rozšíření Nástroje šablony Správce prostředků Azure je plugin pro vývoj šablon Správce prostředků. Rozšíření přidává jazykovou podporu pro šablony, které vám poskytnou intellisense, zvýraznění syntaxe, in-line nápovědu a mnoho dalších jazykových funkcí. Společně poskytují doporučené zkušenosti s vývojem šablon.

## <a name="install-visual-studio-code"></a>Instalace nástroje Visual Studio Code

Visual studio Code podporuje MacOS, Windows a Linux.  Lze jej nainstalovat z [kódu sady Visual Studio](https://code.visualstudio.com/).

## <a name="install-resource-manager-tools-extension"></a>Nainstalovat rozšíření Nástroje správce prostředků

1. Otevřete Visual Studio Code.
1. V levé nabídce vyberte **Rozšíření.** Nebo z nabídky **Zobrazení** vyberte **Rozšíření,** chcete-li otevřít podokno Rozšíření.

    ![Instalace rozšíření Nástroje správce prostředků kódu sady Visual Studio](./media/use-vs-code-to-create-template/resource-manager-visual-studio-code-tools-extension.png)
1. Vyhledejte **Správce prostředků**.
1. V části **Nástroje Správce prostředků Azure**vyberte **Instalovat** .

## <a name="the-extension-features"></a>Funkce rozšíření

### <a name="colorization-for-template-language-expressions"></a>Vybarvení výrazů jazyka šablony

Parametry, proměnné, funkce, názvy a výrazy jsou barevně odlišeny, jak je znázorněno na následujícím snímku obrazovky:

![Rozšíření obarvách nástroje Správce prostředků kódu sady Visual Studio](./media/use-vs-code-to-create-template/resource-manager-tools-extension-colorization.png)

Zobrazení osnovy šablony usnadňuje navigaci mezi velkými šablonami.

### <a name="intellisense"></a>Intellisense

Rozšíření šablony Správce prostředků zná možné dokončení názvů funkcí, parametrů, proměnných a odkazů. Návrhy technologie IntelliSense se při psaní objevují. Pokud budete pokračovat v psaní znaků, seznam členů (proměnné, metody atd.) se vyfiltruje tak, aby zahrnoval pouze členy obsahující zadané znaky. Stisknutím **klávesy Tab** nebo **Enter** vložíte vybraný člen.

- Názvy vestavěných funkcí

    ![Visual Studio Code Resource Manager nástroje rozšíření intellisense funkce](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-functions.png)

- Odkazy na parametry

    ![Nástroje nástroje Správce kódů visual studia prodlužují parametry intellisense](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-parameters.png)

- Odkazy na proměnné

    ![Visual Studio Code Resource Manager nástroje rozšíření intellisense proměnné](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-variables.png)

- vlastnosti skupiny().

    ![Visual Studio Code Resource Manager nástroje rozšíření intellisense funkce](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-resourcegroup.png)

Kromě toho intellisense také pracuje s subscription() vlastnosti a vlastnosti odkazy na proměnné, které jsou objekty.

### <a name="signature-help-for-function-parameters"></a>Nápověda k podpisu pro parametry funkce

Při najetí na názvy funkcí rozšíření zobrazí nápovědu k podpisu pro parametry funkce.

![Funkce podpisu rozšíření nástrojů správce prostředků kódu sady Visual Studio](./media/use-vs-code-to-create-template/resource-manager-tools-extension-signature-function.png)

### <a name="go-to-definition-for-variable-and-parameter-references"></a>Přejít na definici pro odkazy na proměnné a parametry

Na definici můžete přejít pomocí **kombinace kláves Ctrl+Click**nebo pomocí ![kontextové nabídky, jak je znázorněno na snímku obrazovky: Rozšíření nástrojů Nástroje správce kódů sady Visual Studio přejdou na definici](./media/use-vs-code-to-create-template/resource-manager-tools-extension-context-menu.png)

Definici můžete otevřít na stranu pomocí **kombinace kláves Ctrl+Alt+Click**.

### <a name="peek-for-variable-and-parameter-definitions"></a>Náhled pro definice proměnných a parametrů

Chcete-li otevřít editor náhledu, použijte kontextovou nabídku, jak je znázorněno na předchozím snímku obrazovky.

Následující snímek obrazovky ukazuje editor náhledu:

![Editor náhledu rozšíření nástrojů nástroje správce kódu sady Visual Studio](./media/use-vs-code-to-create-template/resource-manager-tools-extension-peek-editor.png)

### <a name="find-all-references-for-variables-and-parameters"></a>Najít všechny odkazy na proměnné a parametry

Chcete-li najít všechny odkazy, použijte kontextovou nabídku, jak je znázorněno na předchozím snímku obrazovky.

Následující snímek obrazovky ukazuje, jak jsou odkazy zvýrazněny:

![Rozšíření nástrojů správce prostředků kódu sady Visual Studio najdete všechny odkazy](./media/use-vs-code-to-create-template/resource-manager-tools-extension-find-all-references.png)

### <a name="rename-all-references-for-variables-and-parameters"></a>Přejmenovat všechny odkazy pro proměnné a parametry

Chcete-li přejmenovat všechny odkazy na proměnné a parametry, použijte kontextovou nabídku, jak je znázorněno na předchozím snímku obrazovky.

### <a name="hover-for-parameter-description"></a>Najetí na další položku pro popis parametru

![Rozšíření nástrojů správce prostředků kódu sady Visual Studio](./media/use-vs-code-to-create-template/resource-manager-tools-extension-hover-parameters.png)

### <a name="brace-matching"></a>Shoda složených závorek

Odpovídající závorky se zvýrazní, jakmile je kurzor blízko jednoho z nich. Po klepnutí na složenou závorku se zvýrazní také odpovídající složená závorka, jak je znázorněno na následujícím snímku obrazovky:

![Porovnání složených příporačních zařízení nástroje nástroje nástroje visual studio code správce](./media/use-vs-code-to-create-template/resource-manager-tools-extension-brace-matching.png)

### <a name="show-errors-and-warnings"></a>Zobrazit chyby a upozornění

Mezi chyby zjištěné rozšířením patří:

- Nedefinované odkazy na parametry
- Nedefinované odkazy na proměnné
- Nerozpoznané názvy funkcí
- reference() použití funkce v definici proměnné
- Nesprávný počet argumentů ve funkcích

Varování zahrnují:

- Nevyužité parametry
- Nevyužité proměnné

## <a name="next-steps"></a>Další kroky

- Další informace o vytváření šablon Azure Resource Manageru najdete [v tématu Kurz: Vytvoření a nasazení první šablony Azure Resource Manageru](template-tutorial-create-first-template.md).
- Pokud chcete projít rychlý start pomocí kódu Sady Visual Studio, přečtěte si [panel Rychlý start: Vytvoření šablon Azure Resource Manageru pomocí kódu Sady Visual Studio](quickstart-create-templates-use-visual-studio-code.md)

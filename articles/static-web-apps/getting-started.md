---
title: 'Rychlý Start: Vytvoření první statické webové aplikace pomocí statického Web Apps Azure'
description: Naučte se vytvořit instanci statického Web Apps Azure s preferovaným front-endové architekturou.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 72a76fb513dc6eb008fcf1d1e19ffc33e713cfdc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2020
ms.locfileid: "84259248"
---
# <a name="quickstart-building-your-first-static-web-app"></a>Rychlý Start: Vytvoření první statické webové aplikace

Služba Azure static Web Apps publikuje weby do produkčního prostředí tím, že vytváří aplikace z úložiště GitHub. V tomto rychlém startu vytvoříte webovou aplikaci s použitím preferované architektury front-endu z úložiště GitHub.

Pokud nemáte předplatné Azure, [Vytvořte si bezplatný zkušební účet](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Požadavky

- Účet [GitHub](https://github.com)
- Účet [Azure](https://portal.azure.com)

## <a name="create-a-repository"></a>Vytvoření úložiště

V tomto článku se používají úložiště šablon GitHubu, která usnadňují vytvoření nového úložiště. Šablony nastavují aplikace úvodní s různými rozhraními front-endu.

# <a name="angular"></a>[Úhlová](#tab/angular)

- Ujistěte se, že jste přihlášeni k GitHubu, a přejděte do následujícího umístění a vytvořte nové úložiště.
  - https://github.com/staticwebdev/angular-basic/generate
- Pojmenování úložiště **My-First-static-Web-App**

# <a name="react"></a>[React](#tab/react)

- Ujistěte se, že jste přihlášeni k GitHubu, a přejděte do následujícího umístění a vytvořte nové úložiště.
  - https://github.com/staticwebdev/react-basic/generate
- Pojmenování úložiště **My-First-static-Web-App**

# <a name="vue"></a>[Vue](#tab/vue)

- Ujistěte se, že jste přihlášeni k GitHubu, a přejděte do následujícího umístění a vytvořte nové úložiště.
  - https://github.com/staticwebdev/vue-basic/generate
- Pojmenování úložiště **My-First-static-Web-App**

# <a name="no-framework"></a>[Žádná architektura](#tab/vanilla-javascript)

- Ujistěte se, že jste přihlášeni k GitHubu, a přejděte do následujícího umístění a vytvořte nové úložiště.
  - https://github.com/staticwebdev/vanilla-basic/generate
- Pojmenování úložiště **My-First-static-Web-App**

> [!NOTE]
> Pro vytvoření webové aplikace se v Azure static Web Apps vyžaduje aspoň jeden soubor HTML. Úložiště, které vytvoříte v tomto kroku, obsahuje jeden soubor _index. html_ .

---

Klikněte na tlačítko **vytvořit úložiště z šablony** .

:::image type="content" source="media/getting-started/create-template.png" alt-text="Vytvořit úložiště ze šablony":::

## <a name="create-a-static-web-app"></a>Vytvoření statické webové aplikace

Teď, když je úložiště vytvořené, můžete z Azure Portal vytvořit statickou webovou aplikaci.

- Přejít na [Azure Portal](https://portal.azure.com)
- Klikněte na **vytvořit prostředek** .
- Hledání **statického Web Apps**
- Klikněte na **statické Web Apps (Preview)** .
- Klikněte na **vytvořit** .

### <a name="basics"></a>Základy

Začněte tím, že nakonfigurujete novou aplikaci a propojíte ji s úložištěm GitHub.

:::image type="content" source="media/getting-started/basics-tab.png" alt-text="Karta základy":::

- Výběr vašeho _předplatného Azure_
- Vyberte nebo vytvořte novou _skupinu prostředků_ .
- Pojmenujte aplikaci **My-First-static-Web-App**.
  - Platné znaky jsou `a-z` (bez rozlišení velkých a malých písmen), `0-9` a `-` .
- Vyberte _oblast_ , která je pro vás nejblíže
- Výběr **bezplatné** _SKU_
- Klikněte na tlačítko **Přihlásit se pomocí GitHubu** a proveďte ověření pomocí GitHubu.

Až se přihlásíte pomocí GitHubu, zadejte informace o úložišti.

:::image type="content" source="media/getting-started/repository-details.png" alt-text="Podrobnosti úložiště":::

- Vybrat upřednostňovanou _organizaci_
- Z rozevíracího seznamu _úložiště_ vyberte **My-First-web-static-App** .
- V rozevíracím seznamu _větev_ vyberte **Hlavní** .
- Kliknutím na tlačítko **Další: >sestavení** upravíte konfiguraci sestavení

:::image type="content" source="media/getting-started/next-build-button.png" alt-text="Tlačítko Další sestavení":::

### <a name="build"></a>Sestavení

Dále přidejte podrobnosti o konfiguraci, které jsou specifické pro preferovanou front-end architekturu.

# <a name="angular"></a>[Úhlová](#tab/angular)

- Zadejte **/** do pole _umístění aplikace_ .
- Vymazat výchozí hodnotu z pole _umístění rozhraní API_
- V poli _umístění artefaktu aplikace_ zadejte **DIST/úhlové – Basic** .

# <a name="react"></a>[React](#tab/react)

- Zadejte **/** do pole _umístění aplikace_ .
- Vymazat výchozí hodnotu z pole _umístění rozhraní API_
- Do pole _umístění artefaktu aplikace_ zadejte **Build** .

# <a name="vue"></a>[Vue](#tab/vue)

- Zadejte **/** do pole _umístění aplikace_ .
- Vymazat výchozí hodnotu z pole _umístění rozhraní API_
- Zadejte **DIST** do pole _umístění artefaktu aplikace_ .

# <a name="no-framework"></a>[Žádná architektura](#tab/vanilla-javascript)

- Zadejte **/** do pole _umístění aplikace_ .
- Vymazat výchozí hodnotu z pole _umístění rozhraní API_
- Zrušte zaškrtnutí políčka Výchozí hodnota z _umístění artefaktu aplikace_ .

---

Klikněte na tlačítko **Zkontrolovat a vytvořit**.

:::image type="content" source="media/getting-started/review-create.png" alt-text="Tlačítko pro vytvoření Revize":::

Chcete-li po vytvoření aplikace tyto hodnoty změnit, můžete upravit [soubor pracovního postupu](github-actions-workflow.md).

### <a name="review--create"></a>Zkontrolovat a vytvořit

Po ověření žádosti můžete pokračovat v vytváření aplikace.

Klikněte na tlačítko **vytvořit** .

:::image type="content" source="media/getting-started/create-button.png" alt-text="Tlačítko vytvořit":::

Po vytvoření prostředku klikněte na tlačítko **Přejít k prostředku** .

:::image type="content" source="media/getting-started/resource-button.png" alt-text="Tlačítko Přejít na prostředek":::

## <a name="view-the-website"></a>Zobrazit web

Existují dva aspekty nasazení statické aplikace. Nejdřív se zřídí základní prostředky Azure, které tvoří vaši aplikaci. Druhým je pracovní postup akcí GitHubu, který vytváří a publikuje vaši aplikaci.

Předtím, než budete moci přejít na novou statickou lokalitu, musí být sestavení nasazení nejprve dokončeno.

V okně Přehled statického Web Apps se zobrazuje řada odkazů, které vám pomůžou s webovou aplikací pracovat.

:::image type="content" source="media/getting-started/overview-window.png" alt-text="Okno s přehledem":::

1. Po kliknutí na banner se zobrazí zpráva "kliknutím sem zkontrolujete stav vašich akcí GitHubu" přejdete na akce GitHubu, které běží na vašem úložišti. Jakmile ověříte, že je úloha nasazení dokončená, můžete přejít na web prostřednictvím vygenerované adresy URL.

2. Po dokončení pracovního postupu akcí GitHubu můžete kliknutím na odkaz na _adresu URL_ otevřít web na nové kartě.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, můžete instanci statického Web Apps Azure odstranit pomocí následujících kroků:

1. Otevřete [Azure Portal](https://portal.azure.com)
1. Hledání **My-First-web-static-App** z horního panelu hledání
1. Klikněte na název aplikace.
1. Klikněte na tlačítko **Odstranit** .
1. Kliknutím na **Ano** potvrďte akci odstranit.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání rozhraní API](add-api.md)

---
title: 'Kurz: publikování webu Jekyll do Azure static Web Apps'
description: Naučte se, jak nasadit aplikaci Jekyll do statického Web Apps Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 06/08/2020
ms.author: cshoe
ms.openlocfilehash: 8c6764ad5b63aa2fde07326ab986404ea4312316
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585173"
---
# <a name="tutorial-publish-a-jekyll-site-to-azure-static-web-apps-preview"></a>Kurz: publikování webu Jekyll ve službě Azure static Web Apps Preview

Tento článek ukazuje, jak vytvořit a nasadit webovou aplikaci v [Jekyll](https://jekyllrb.com/) do [azure Azure static Web Apps](overview.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> - Vytvoření webu Jekyll
> - Nastavení statického Web Apps Azure
> - Nasazení aplikace Jekyll do Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- Nainstalovat [Jekyll](https://jekyllrb.com/docs/installation/)
  - V případě potřeby můžete použít podsystém Windows pro Linux a postupovat podle pokynů Ubuntu.
- Účet Azure s aktivním předplatným. Pokud ho nemáte, můžete si [účet zdarma vytvořit](https://azure.microsoft.com/free/).
- Účet GitHub. Pokud ho nemáte, můžete si [účet zdarma vytvořit](https://github.com/join).

## <a name="create-jekyll-app"></a>Vytvoření aplikace Jekyll

Vytvoření aplikace Jekyll pomocí rozhraní příkazového řádku Jekyll (CLI):

1. Z terminálu spusťte rozhraní příkazového řádku Jekyll, abyste mohli vytvořit novou aplikaci.

   ```bash
   jekyll new static-app
   ```

1. Přejděte na nově vytvořenou aplikaci.

   ```bash
   cd static-app
   ```

1. Inicializujte nové úložiště Git.

   ```bash
    git init
   ```

1. Potvrďte změny.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Vložení aplikace do GitHubu

Azure static Web Apps používá GitHub k publikování vašeho webu. Následující kroky ukazují, jak vytvořit úložiště GitHub.

1. Vytvořte prázdné úložiště GitHub (nevytvářejte soubor READme) z [https://github.com/new](https://github.com/new) pojmenovaného **Jekyll-Azure-static**.

1. Přidejte úložiště GitHub jako vzdálené úložiště do místního úložiště. Nezapomeňte přidat své uživatelské jméno GitHubu místo `<YOUR_USER_NAME>` zástupného znaku v následujícím příkazu.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/jekyll-static-app
   ```

1. Vložení místního úložiště do GitHubu

   ```bash
   git push --set-upstream origin main
   ```

   > [!NOTE]
   > Vaše větev Git může být pojmenována odlišně `main` . Nahraďte `main` tímto příkazem správnou hodnotou.

## <a name="deploy-your-web-app"></a>Nasazení webové aplikace

Následující kroky ukazují, jak vytvořit novou aplikaci statické lokality a jak ji nasadit do produkčního prostředí.

### <a name="create-the-application"></a>Vytvoření aplikace

1. Přejděte na [Azure Portal](https://portal.azure.com).

1. Klikněte na **vytvořit prostředek**.

1. Vyhledejte **statické Web Apps**.

1. Klikněte na možnost **statické Web Apps (Preview)**.

1. Klikněte na **Vytvořit**.

1. U **předplatného** přijměte uvedené předplatné nebo v rozevíracím seznamu vyberte nový.

1. V _skupiny prostředků_ vyberte **Nový**. Do _název nové skupiny prostředků_ zadejte **Jekyll-static-App** a vyberte **OK**.

1. Potom do pole _název_ zadejte název vaší aplikace. Mezi platné znaky `a-z` patří `A-Z` , `0-9` a `-` .

1. V _oblasti oblast_ vyberte dostupnou oblast, která je blízko vás.

1. V případě _SKU_ vyberte možnost **Free**.

    :::image type="content" source="./media/publish-jekyll/basic-app-details.png" alt-text="Podrobnosti vyplněné":::

1. Klikněte na tlačítko **Přihlásit se pomocí GitHubu** .

1. Vyberte **organizaci** , ve které jste úložiště vytvořili.

1. Jako _úložiště_ vyberte **Jekyll-static-App** .

1. Pro _větev_ vyberte **Main (hlavní**).

    :::image type="content" source="./media/publish-jekyll/completed-github-info.png" alt-text="Informace o dokončeném GitHubu":::

### <a name="build"></a>Sestavení

Dále přidáte nastavení konfigurace, které proces sestavení používá k sestavení vaší aplikace. Následující nastavení nakonfigurují soubor pracovního postupu akce GitHubu.

1. Kliknutím na tlačítko **Další: >sestavení** Upravte konfiguraci sestavení.

1. Nastavte _umístění aplikace_ na **/_SITE**.

1. _Umístění artefaktu aplikace_ nechte prázdné.

   Hodnota pro _umístění rozhraní API_ není nutná, protože v tuto chvíli nebudete NASAZOVAT rozhraní API.

### <a name="review-and-create"></a>Zkontrolovat a vytvořit

1. Kliknutím na tlačítko **Revize + vytvořit** ověřte správnost podrobností.

1. Kliknutím na **vytvořit** zahájíte vytváření statických Web Apps Azure a zřídíte akci GitHubu pro nasazení.

1. Nasazení bude napřed úspěšné, protože soubor pracovního postupu potřebuje některá nastavení specifická pro Jekyll. Pokud chcete tato nastavení přidat, přejděte do terminálu a odešlete potvrzení do svého počítače pomocí akce GitHub.

   ```bash
   git pull
   ```

1. Otevřete aplikaci Jekyll v textovém editoru a otevřete soubor _. GitHub/Workflows/Azure-Pages-<WORKFLOW_NAME>. yml_ .

1. Před řádek `- name: Build And Deploy` přidejte následující konfigurační blok.

    ```yml
    - name: Set up Ruby
      uses: ruby/setup-ruby@v1.59.1
      with:
        ruby-version: 2.6
    - name: Install dependencies
      run: bundle install
    - name: Jekyll build
      run: jekyll build
    ```

1. Potvrďte aktualizovaný pracovní postup a předejte ho do GitHubu.

    ```bash
    git add -A
    git commit -m "Updating GitHub Actions workflow"
    git push
    ```

1. Počkejte na dokončení akce GitHubu.

1. V okně _přehled_ Azure Portal kliknutím na odkaz _URL_ otevřete nasazenou aplikaci.

   :::image type="content" source="./media/publish-jekyll/deployed-app.png" alt-text="Nasazená aplikace":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání vlastní domény](custom-domain.md)

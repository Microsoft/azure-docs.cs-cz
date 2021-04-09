---
title: 'Kurz: publikování webu VuePress do Azure static Web Apps'
description: V tomto kurzu se dozvíte, jak nasadit aplikaci VuePress do služby Azure static Web Apps.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.custom: devx-track-js
ms.openlocfilehash: 6f0616df885a7f8fcd76337c810bc368aa02f3c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100650438"
---
# <a name="tutorial-publish-a-vuepress-site-to-azure-static-web-apps-preview"></a>Kurz: publikování webu VuePress ve službě Azure static Web Apps Preview

Tento článek ukazuje, jak vytvořit a nasadit webovou aplikaci v [VuePress](https://vuepress.vuejs.org/) do [azure Azure static Web Apps](overview.md). Konečný výsledek je nová aplikace statického Web Apps Azure s přidruženými akcemi GitHubu, která vám umožní řídit, jak je aplikace sestavená a publikovaná.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> - Vytvoření aplikace v VuePress
> - Nastavení statického Web Apps Azure
> - Nasazení aplikace VuePress do Azure

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. Pokud ho nemáte, můžete si [účet zdarma vytvořit](https://azure.microsoft.com/free/).
- Účet GitHub. Pokud ho nemáte, můžete si [účet zdarma vytvořit](https://github.com/join).
- [Node.js](https://nodejs.org) nainstalován.

## <a name="create-a-vuepress-app"></a>Vytvoření aplikace v VuePress

Vytvoření aplikace v VuePress z rozhraní příkazového řádku (CLI):

1. Vytvořte novou složku pro aplikaci VuePress.

   ```bash
   mkdir static-site
   ```

1. Přidejte soubor _Readme.MD_ do složky.

   ```bash
   echo '# Hello From VuePress' > README.md
   ```

1. Inicializujte _package.jsv_ souboru.

   ```bash
   npm init -y
   ```

1. Přidejte VuePress jako `devDependency` .

   ```bash
   npm install --save-dev vuepress
   ```

1. Otevřete _package.js_ v souboru v textovém editoru a přidejte do oddílu příkaz Build [`scripts`](https://docs.npmjs.com/cli-commands/run-script.html) .

   ```json
   ...
   "scripts": {
       "build": "vuepress build"
   }
   ...
   ```

1. Vytvořte soubor _. gitignore_ pro vyloučení složky _\_ modulů uzlů_ .

    ```bash
    echo 'node_modules' > .gitignore
    ```

1. Inicializujte úložiště Git.

   ```bash
    git init
    git add -A
    git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Vložení aplikace do GitHubu

K připojení ke statickým Web Appsm Azure budete potřebovat úložiště na GitHubu. Následující kroky ukazují, jak vytvořit úložiště pro svůj web.

1. Vytvořte prázdné úložiště GitHub (nevytvářejte soubor READme) z [https://github.com/new](https://github.com/new) pojmenované **vuepress-static-App**.

1. Přidejte úložiště GitHub jako vzdálené úložiště do místního úložiště. Nezapomeňte přidat své uživatelské jméno GitHubu místo `<YOUR_USER_NAME>` zástupného znaku v následujícím příkazu.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/vuepress-static-app
   ```

1. Vložení místního úložiště do GitHubu

   ```bash
   git push --set-upstream origin main
   ```

## <a name="deploy-your-web-app"></a>Nasazení webové aplikace

Následující kroky ukazují, jak vytvořit novou statickou Web Apps aplikaci a jak ji nasadit do produkčního prostředí.

### <a name="create-the-application"></a>Vytvoření aplikace

1. Přejít na [Azure Portal](https://portal.azure.com)
1. Klikněte na **Vytvořit prostředek**.
1. Vyhledejte **Static Web Apps**.
1. Klikněte na **Static Web Apps (Preview)**
1. Klikněte na **Vytvořit**.

   :::image type="content" source="./media/publish-vuepress/create-in-portal.png" alt-text="Vytvoření statického Web Apps (Preview) na portálu":::

1. U **předplatného** přijměte uvedené předplatné nebo v rozevíracím seznamu vyberte nový.

1. V _skupiny prostředků_ vyberte **Nový**. Do _název nové skupiny prostředků_ zadejte **vuepress-static-App** a vyberte **OK**.

1. Dále do pole **název** zadejte název vaší aplikace. Mezi platné znaky `a-z` patří `A-Z` , `0-9` a `-` .

1. V _oblasti oblast_ vyberte dostupnou oblast, která je blízko vás.

1. V případě _SKU_ vyberte možnost **Free**.

   :::image type="content" source="./media/publish-vuepress/basic-app-details.png" alt-text="Podrobnosti vyplněné":::

1. Klikněte na tlačítko **Přihlásit se pomocí GitHubu** .

1. Vyberte **organizaci** , ve které jste úložiště vytvořili.

1. Jako _úložiště_ vyberte **vuepress-static-App** .

1. Pro _větev_ vyberte **Main (hlavní**).

   :::image type="content" source="./media/publish-vuepress/completed-github-info.png" alt-text="Informace o dokončeném GitHubu":::

### <a name="build"></a>Sestavení

Dále přidáte nastavení konfigurace, které proces sestavení používá k sestavení vaší aplikace. Následující nastavení nakonfigurují soubor pracovního postupu akce GitHubu.

1. Klikněte na tlačítko **Další: Sestavení >** a upravte konfiguraci sestavení.

1. Nastavte _umístění aplikace_ na **/** .

1. Nastavte _umístění artefaktu aplikace_ na **. vuepress/DIST**.

Hodnota pro _umístění rozhraní API_ není nutná, protože v tuto chvíli nebudete NASAZOVAT rozhraní API.

   :::image type="content" source="./media/publish-vuepress/build-details.png" alt-text="Nastavení sestavení":::

### <a name="review-and-create"></a>Zkontrolovat a vytvořit

1. Kliknutím na tlačítko **Revize + vytvořit** ověřte správnost podrobností.

1. Kliknutím na **vytvořit** zahájíte vytváření statických Web Apps Azure a zřídíte akci GitHubu pro nasazení.

1. Až se nasazení dokončí, **přejděte na prostředek**.

1. Na obrazovce prostředek kliknutím na odkaz _URL_ otevřete nasazenou aplikaci. Aby se akce GitHubu mohla dokončit, možná budete muset počkat minutu nebo dvě.

   :::image type="content" source="./media/publish-vuepress/deployed-app.png" alt-text="Nasazená aplikace":::

### <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání vlastní domény](custom-domain.md)

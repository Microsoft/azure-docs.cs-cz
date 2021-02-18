---
title: 'Kurz: publikování webu Hugo do Azure static Web Apps'
description: Naučte se, jak nasadit aplikaci Hugo do statického Web Apps Azure.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: 4539c32a367bb0974212d989176a96b530da21a4
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652325"
---
# <a name="tutorial-publish-a-hugo-site-to-azure-static-web-apps-preview"></a>Kurz: publikování webu Hugo ve službě Azure static Web Apps Preview

Tento článek ukazuje, jak vytvořit a nasadit webovou aplikaci v [Hugo](https://gohugo.io/) do služby [Azure static Web Apps](overview.md). Konečný výsledek je nová statická webová aplikace Azure s přidruženými akcemi GitHubu, která vám umožní řídit, jak je aplikace sestavená a publikovaná.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> - Vytvoření aplikace v Hugo
> - Nastavení statického Web Apps Azure
> - Nasazení aplikace Hugo do Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. Pokud ho nemáte, můžete si [účet zdarma vytvořit](https://azure.microsoft.com/free/).
- Účet GitHub. Pokud ho nemáte, můžete si [účet zdarma vytvořit](https://github.com/join).

## <a name="create-a-hugo-app"></a>Vytvoření aplikace v Hugo

Vytvoření aplikace Hugo pomocí rozhraní příkazového řádku Hugo (CLI):

1. Postupujte podle pokynů [Průvodce instalací](https://gohugo.io/getting-started/installing/) pro Hugo v operačním systému.

1. Otevření terminálu

1. Pokud chcete vytvořit novou aplikaci, spusťte rozhraní příkazového řádku Hugo.

   ```bash
   hugo new site static-app
   ```

1. Přejděte na nově vytvořenou aplikaci.

   ```bash
   cd static-app
   ```

1. Inicializujte úložiště Git.

   ```bash
    git init
   ```

1. Dále přidejte motiv do webu tak, že nainstalujete motiv jako dílčí modul Git a pak ho zadáte do konfiguračního souboru Hugo.

   ```bash
   git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
   echo 'theme = "ananke"' >> config.toml
   ```

1. Potvrďte změny.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Vložení aplikace do GitHubu

K připojení ke statickým Web Appsm Azure budete potřebovat úložiště na GitHubu. Následující kroky ukazují, jak vytvořit úložiště pro svůj web.

1. Vytvořte prázdné úložiště GitHub (nevytvářejte soubor READme) z [https://github.com/new](https://github.com/new) pojmenované **Hugo-static-App**.

1. Přidejte úložiště GitHub jako vzdálené úložiště do místního úložiště. Nezapomeňte přidat své uživatelské jméno GitHubu místo `<YOUR_USER_NAME>` zástupného znaku v následujícím příkazu.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/hugo-static-app
   ```

1. Vložení místního úložiště do GitHubu

   ```bash
   git push --set-upstream origin main
   ```

## <a name="deploy-your-web-app"></a>Nasazení webové aplikace

Následující kroky ukazují, jak vytvořit novou aplikaci statické lokality a jak ji nasadit do produkčního prostředí.

### <a name="create-the-application"></a>Vytvoření aplikace

1. Přejít na [Azure Portal](https://portal.azure.com)
1. Klikněte na **Vytvořit prostředek**.
1. Vyhledejte **Static Web Apps**.
1. Klikněte na **Static Web Apps (Preview)**
1. Klikněte na **Vytvořit**.

   :::image type="content" source="./media/publish-hugo/create-in-portal.png" alt-text="Vytvoření prostředku statického Web Apps Azure na portálu":::

1. U **předplatného** přijměte uvedené předplatné nebo v rozevíracím seznamu vyberte nový.

1. V _skupiny prostředků_ vyberte **Nový**. Do _název nové skupiny prostředků_ zadejte **Hugo-static-App** a vyberte **OK**.

1. Dále do pole **název** zadejte název vaší aplikace. Mezi platné znaky `a-z` patří `A-Z` , `0-9` a `-` .

1. V _oblasti oblast_ vyberte dostupnou oblast, která je blízko vás.

1. V případě _SKU_ vyberte možnost **Free**.

   :::image type="content" source="./media/publish-hugo/basic-app-details.png" alt-text="Podrobnosti vyplněné":::

1. Klikněte na tlačítko **Přihlásit se pomocí GitHubu** .

1. Vyberte **organizaci** , ve které jste úložiště vytvořili.

1. Jako _úložiště_ vyberte **Hugo-static-App** .

1. Pro _větev_ vyberte **Main (hlavní**).

   :::image type="content" source="./media/publish-hugo/completed-github-info.png" alt-text="Informace o dokončeném GitHubu":::

### <a name="build"></a>Sestavení

Dále přidáte nastavení konfigurace, které proces sestavení používá k sestavení vaší aplikace. Následující nastavení nakonfigurují soubor pracovního postupu akce GitHubu.

1. Klikněte na tlačítko **Další: Sestavení >** a upravte konfiguraci sestavení.

1. Nastavte _umístění aplikace_ na **/** .

1. Nastavte _umístění artefaktu aplikace_ na **veřejné**.

   Hodnota pro _umístění rozhraní API_ není nutná, protože v tuto chvíli nebudete NASAZOVAT rozhraní API.

### <a name="review-and-create"></a>Zkontrolovat a vytvořit

1. Kliknutím na tlačítko **Revize + vytvořit** ověřte správnost podrobností.

1. Kliknutím na **vytvořit** zahájíte vytváření statických Web Apps Azure a zřídíte akci GitHubu pro nasazení.

1. Počkejte na dokončení akce GitHubu.

1. V okně _přehledu_ Azure Portal nově vytvořeného prostředku služby Azure static Web Apps kliknutím na odkaz _URL_ otevřete nasazenou aplikaci.

   :::image type="content" source="./media/publish-hugo/deployed-app.png" alt-text="Nasazená aplikace":::

#### <a name="custom-hugo-version"></a>Vlastní verze Hugo

Když vygenerujete statickou webovou aplikaci, vygeneruje se [soubor pracovního postupu](./github-actions-workflow.md) , který obsahuje nastavení konfigurace publikování pro aplikaci. Konkrétní verzi Hugo můžete určit v souboru pracovního postupu zadáním hodnoty pro `HUGO_VERSION` v `env` části. Následující příklad konfigurace ukazuje, jak nastavit sadu Hugo na konkrétní verzi.

```yaml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
          action: "upload"
          ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
          # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
          app_location: "/" # App source code path
          api_location: "api" # Api source code path - optional
          output_location: "public" # Built app content directory - optional
          ###### End of Repository/Build Configurations ######
        env:
          HUGO_VERSION: 0.58.0
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání vlastní domény](custom-domain.md)

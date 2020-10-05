---
title: 'Rychlý Start: Vytvoření aplikace v Ruby'
description: Začněte s Azure App Service nasazením první aplikace v Ruby do kontejneru Linux v App Service.
keywords: azure app service, linux, oss, ruby, rails
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.topic: quickstart
ms.date: 07/11/2019
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 49f2100386af21cee8f76403d7a2d2e4ac6b8f63
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88961325"
---
# <a name="create-a-ruby-on-rails-app-in-app-service"></a>Vytvoření aplikace v Ruby na železnici v App Service

[Azure App Service v systému Linux](overview.md#app-service-on-linux) poskytuje vysoce škálovatelnou službu s automatickými opravami pro hostování webů pomocí operačního systému Linux. V tomto kurzu rychlý Start se dozvíte, jak nasadit aplikaci v Ruby na železnici, která App Service na Linux pomocí [Cloud Shell](../cloud-shell/overview.md).

> [!NOTE]
> Vývojová sada Ruby podporuje v současnosti jen Ruby on Rails. Pokud chcete použít jinou platformu, například Sinatra, nebo pokud chcete použít nepodporovanou verzi Ruby, je nutné [ji spustit ve vlastním kontejneru](./quickstart-custom-container.md?pivots=platform-linux%3fpivots%3dplatform-linux).

![Hello World](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Nainstalovat Ruby 2,6 nebo vyšší</a>
* <a href="https://git-scm.com/" target="_blank">Nainstalovat Git</a>.

## <a name="download-the-sample"></a>Stažení ukázky

V okně terminálu naklonujte spuštěním následujícího příkazu úložiště ukázkové aplikace do místního počítače:

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>Aplikaci spustíte místně.

Spusťte aplikaci místně, abyste viděli, jak by měla vypadat po nasazení do Azure. Otevřete okno terminálu, přejděte do adresáře `hello-world` a pomocí příkazu `rails server` spusťte server.

Prvním krokem je instalace požadovaných souborů gem. `Gemfile`V ukázce je zahrnut, stačí spustit následující příkaz:

```bash
bundle install
```

Po instalaci souborů gem použijeme příkaz bundler ke spuštění aplikace:

```bash
bundle exec rails server
```

Ve webovém prohlížeči přejděte na adresu `http://localhost:3000` a místně otestujte aplikaci.

![Nakonfigurovaná aplikace Hello World](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [Try Cloud Shell](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

Přejděte do aplikace a podívejte se na nově vytvořenou webovou aplikaci s použitím integrované image. Nahraďte _ &lt; název aplikace>_ názvem vaší webové aplikace.

```bash
http://<app_name>.azurewebsites.net
```

Vaše nová webová aplikace by měla vypadat takto:

![Úvodní stránka](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>Nasazení aplikace

Spuštěním následujících příkazů nasaďte místní aplikaci do vaší webové aplikace Azure:

```bash
git remote add azure <Git deployment URL from above>
git push azure master
```

Zkontrolujte, že operace vzdáleného nasazení hlásí úspěch. Příkazy vygenerují výstup podobný následujícímu textu:

```bash
remote: Using turbolinks 5.2.0
remote: Using uglifier 4.1.20
remote: Using web-console 3.7.0
remote: Bundle complete! 18 Gemfile dependencies, 78 gems now installed.
remote: Bundled gems are installed into `/tmp/bundle`
remote: Zipping up bundle contents
remote: .......
remote: ~/site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git
   a6e73a2..ae34be9  master -> master
```

Až se nasazení dokončí, počkejte asi 10 sekund, než se webová aplikace restartuje, a pak přejděte do webové aplikace a ověřte výsledky.

```bash
http://<app-name>.azurewebsites.net
```

![aktualizovaná webová aplikace](./media/quickstart-ruby/hello-world-configured.png)

> [!NOTE]
> I když se aplikace restartuje, můžete sledovat stavový kód HTTP `Error 503 Server unavailable` v prohlížeči nebo `Hey, Ruby developers!` výchozí stránku. Úplné restartování aplikace může trvat několik minut.
>

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Ruby na kolejnicích s Postgres](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Konfigurace aplikace Ruby](configure-language-ruby.md)
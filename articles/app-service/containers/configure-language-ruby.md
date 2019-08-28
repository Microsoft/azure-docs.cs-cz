---
title: Konfigurace aplikací Ruby – Azure App Service
description: Tento kurz popisuje možnosti vytváření a konfigurace aplikací Ruby pro Azure App Service v systému Linux.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: cephalin
ms.reviewer: astay; kraigb
ms.custom: seodec18
ms.openlocfilehash: 71734396e90987fb1e318f3d8bb01d957fc0fda1
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071293"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Konfigurace aplikace pro Linux v Ruby pro Azure App Service

Tento článek popisuje, jak [Azure App Service](app-service-linux-intro.md) spouští aplikace Ruby a jak můžete v případě potřeby přizpůsobit chování App Service. Aplikace Ruby musí být nasazeny se všemi požadovanými moduly [PIP](https://pypi.org/project/pip/) .

V této příručce najdete klíčové koncepty a pokyny pro vývojáře Ruby, kteří používají integrovaný kontejner Linux v nástroji App Service. Pokud jste nikdy Azure App Service nepoužili, měli byste nejdřív postupovat podle kurzu [Ruby pro rychlé](quickstart-ruby.md) zprovoznění a [Ruby v PostgreSQL](tutorial-ruby-postgres-app.md) .

## <a name="show-ruby-version"></a>Zobrazit verzi Ruby

Aktuální verzi Ruby zobrazíte spuštěním následujícího příkazu v [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Pokud chcete zobrazit všechny podporované verze Ruby, spusťte v [Cloud Shell](https://shell.azure.com)následující příkaz:

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

Můžete spustit nepodporovanou verzi Ruby vytvořením vlastní image kontejneru. Další informace najdete v tématu [použití vlastní image Docker](tutorial-custom-docker-image.md).

## <a name="set-ruby-version"></a>Nastavit verzi Ruby

Spusťte následující příkaz v [Cloud Shell](https://shell.azure.com) k nastavení verze Ruby na 2,3:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Pokud se v době nasazení zobrazí chyby podobné následujícímu:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> or
> ```
> rbenv: version `2.3.1' is not installed
> ```
> To znamená, že verze Ruby nakonfigurovaná ve vašem projektu se liší od verze, která je nainstalovaná v kontejneru, který používáte`2.3.3` (v předchozím příkladu). V předchozím příkladu zkontrolujte *Gemfile* i *. Ruby-Version* a ověřte, že verze Ruby není nastavená, nebo je nastavená na verzi, která je nainstalovaná v kontejneru, který používáte (`2.3.3` v předchozím příkladu).

## <a name="access-environment-variables"></a>Přístup k proměnným prostředí

V App Service můžete [nastavit nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) mimo kód vaší aplikace. Pak k nim můžete přistupovat pomocí vzoru standard [ENV ['\<Path-Name > ']](https://ruby-doc.org/core-2.3.3/ENV.html) . Chcete-li například získat přístup k nastavení aplikace `WEBSITE_SITE_NAME`s názvem, použijte následující kód:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Přizpůsobení nasazení

Když nasadíte [úložiště Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)nebo se zapnul [balíček zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) s procesy sestavení, modul pro nasazení (Kudu) automaticky spustí následující kroky po nasazení:

1. Zkontroluje, jestli *Gemfile* existuje.
1. Spusťte `bundle clean`. 
1. Spusťte `bundle install --path "vendor/bundle"`.
1. Spusťte `bundle package` příkaz pro zabalení Gems do složky dodavatel/cache.

### <a name="use---without-flag"></a>Použít příznak--bez příznaku

Pokud chcete `bundle install` spustit s `BUNDLE_WITHOUT` příznakem [--bez](https://bundler.io/man/bundle-install.1.html) příznaku, nastavte [nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) na čárkami oddělený seznam skupin. Například následující příkaz nastaví na `development,test`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Pokud je toto nastavení definované, spustí `bundle install` se modul pro nasazení pomocí `--without $BUNDLE_WITHOUT`nástroje.

### <a name="precompile-assets"></a>Předkompilovat prostředky

Kroky po nasazení nekompiluje ve výchozím nastavení prostředky. Pokud chcete předkompilaci assetů zapnout, nastavte `ASSETS_PRECOMPILE` [nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) na `true`. Pak se příkaz `bundle exec rake --trace assets:precompile` spustí na konci postupu po nasazení. Příklad:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Další informace najdete v tématu [obsluha statických prostředků](#serve-static-assets).

## <a name="customize-start-up"></a>Přizpůsobení spuštění

Ve výchozím nastavení kontejner Ruby spouští server kolejnice v následující sekvenci (Další informace najdete v tématu [spouštěcí skript](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)):

1. Vygeneruje hodnotu [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security) , pokud ještě neexistuje. Tato hodnota se vyžaduje, aby se aplikace spouštěla v provozním režimu.
1. `production`Nastavte proměnnou `RAILS_ENV` prostředí na.
1. Odstraňte všechny soubory *. PID* v adresáři *TMP/PID* , který zbývá z dříve běžícího serveru s kolejnicemi.
1. Ověřte, zda jsou nainstalovány všechny závislosti. Pokud ne, zkuste nainstalovat Gems z místního *dodavatele nebo adresáře mezipaměti* .
1. Spusťte `rails server -e $RAILS_ENV`.

Proces spouštění můžete přizpůsobit následujícími způsoby:

- [Obsluha statických prostředků](#serve-static-assets)
- [Spustit v neprodukčním režimu](#run-in-non-production-mode)
- [Ruční nastavení secret_key_base](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Obsluha statických prostředků

Server kolejnice v kontejneru Ruby běží ve výchozím nastavení v produkčním režimu a [předpokládá, že prostředky jsou předkompilovány a obsluhovány vaším webovým serverem](https://guides.rubyonrails.org/asset_pipeline.html#in-production). K obsluze statických prostředků ze serveru kolejnice je třeba provést dvě věci:

- **Předkompilování prostředků** - předkompilování[statických prostředků místně](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) a jejich ruční nasazení. Nebo ho modul pro nasazení místo toho zpracuje (viz předkompilování [prostředků](#precompile-assets).
- **Povolit obsluze statických souborů** – pro obsluhu statických prostředků z kontejneru Ruby [ `RAILS_SERVE_STATIC_FILES` nastavte nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) na `true`. Příklad:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Spustit v neprodukčním režimu

Server kolejnice běží ve výchozím nastavení v provozním režimu. Chcete-li spustit v režimu pro vývoj, například nastavte `RAILS_ENV` [nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) na `development`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Toto nastavení ale samostatně způsobí, že se server kolejnice spustí v režimu vývoje, který přijímá pouze požadavky localhost a není přístupný mimo kontejner. Pokud chcete přijmout žádosti vzdálených klientů, nastavte `APP_COMMAND_LINE` [nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) na `rails server -b 0.0.0.0`. Toto nastavení aplikace umožňuje spustit vlastní příkaz v kontejneru Ruby. Příklad:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a>Ruční nastavení secret_key_base

Pokud chcete použít vlastní `secret_key_base` hodnotu místo toho, aby vám App Service pro vás vygenerovala `SECRET_KEY_BASE` jednu z nich, nastavte u [nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) hodnotu, kterou chcete. Příklad:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otevřít relaci SSH v prohlížeči

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: Aplikace kolejnice s PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Nejčastější dotazy k App Service Linux](app-service-linux-faq.md)

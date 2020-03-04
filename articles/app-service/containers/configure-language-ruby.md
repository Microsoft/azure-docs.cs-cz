---
title: Konfigurace aplikací Ruby – Azure App Service
description: Naučte se nakonfigurovat předem připravený kontejner Ruby pro vaši aplikaci. Tento článek ukazuje nejběžnější konfigurační úlohy.
ms.topic: quickstart
ms.date: 03/28/2019
ms.reviewer: astay; kraigb
ms.custom: seodec18
ms.openlocfilehash: 2b096725575598bd44d7da39f77f85dee5b5e40e
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255808"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Konfigurace aplikace pro Linux v Ruby pro Azure App Service

Tento článek popisuje, jak [Azure App Service](app-service-linux-intro.md) spouští aplikace Ruby a jak můžete v případě potřeby přizpůsobit chování App Service. Aplikace Ruby musí být nasazené se všemi požadovanými [Gems](https://rubygems.org/gems).

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
> nebo
> ```
> rbenv: version `2.3.1' is not installed
> ```
> To znamená, že verze Ruby nakonfigurovaná ve vašem projektu se liší od verze, která je nainstalovaná v kontejneru, který používáte (`2.3.3` v příkladu výše). V předchozím příkladu zkontrolujte *Gemfile* i *. Ruby-Version* a ověřte, že verze Ruby není nastavená, nebo je nastavená na verzi, která je nainstalovaná v kontejneru, který používáte (`2.3.3` v příkladu výše).

## <a name="access-environment-variables"></a>Přístup k proměnným prostředí

V App Service můžete [nastavit nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) mimo kód vaší aplikace. Pak k nim můžete přistupovat pomocí vzoru standard [ENV ['\<Path-name > ']](https://ruby-doc.org/core-2.3.3/ENV.html) . Chcete-li například získat přístup k nastavení aplikace s názvem `WEBSITE_SITE_NAME`, použijte následující kód:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Přizpůsobení nasazení

Když nasadíte [úložiště Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)nebo se zapnul [balíček zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) s procesy sestavení, modul pro nasazení (Kudu) automaticky spustí následující kroky po nasazení:

1. Zkontroluje, jestli *Gemfile* existuje.
1. Spusťte `bundle clean`. 
1. Spusťte `bundle install --path "vendor/bundle"`.
1. Spusťte `bundle package` pro zabalení Gems do složky dodavatel/cache.

### <a name="use---without-flag"></a>Použít příznak--bez příznaku

Chcete-li spustit `bundle install` s příznakem [--bez](https://bundler.io/man/bundle-install.1.html) příznaku, nastavte [nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) `BUNDLE_WITHOUT` na čárkami oddělený seznam skupin. Například následující příkaz nastaví `development,test`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Pokud je toto nastavení definované, modul pro nasazení se spustí `bundle install` s `--without $BUNDLE_WITHOUT`.

### <a name="precompile-assets"></a>Předkompilovat prostředky

Kroky po nasazení nekompiluje ve výchozím nastavení prostředky. Chcete-li zapnout předkompilaci assetů, nastavte [nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) `ASSETS_PRECOMPILE` na hodnotu `true`. Pak se příkaz `bundle exec rake --trace assets:precompile` spustí na konci postupu po nasazení. Příklad:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Další informace najdete v tématu [obsluha statických prostředků](#serve-static-assets).

## <a name="customize-start-up"></a>Přizpůsobení spuštění

Ve výchozím nastavení kontejner Ruby spouští server kolejnice v následující sekvenci (Další informace najdete v tématu [spouštěcí skript](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)):

1. Vygeneruje hodnotu [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security) , pokud již neexistuje. Tato hodnota se vyžaduje, aby se aplikace spouštěla v provozním režimu.
1. Nastavte proměnnou prostředí `RAILS_ENV` na `production`.
1. Odstraňte všechny soubory *. PID* v adresáři *TMP/PID* , který zbývá z dříve běžícího serveru s kolejnicemi.
1. Ověřte, zda jsou nainstalovány všechny závislosti. Pokud ne, zkuste nainstalovat Gems z místního *dodavatele nebo adresáře mezipaměti* .
1. Spusťte `rails server -e $RAILS_ENV`.

Proces spouštění můžete přizpůsobit následujícími způsoby:

- [Obsluha statických prostředků](#serve-static-assets)
- [Spustit v neprodukčním režimu](#run-in-non-production-mode)
- [Nastavit secret_key_base ručně](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Obsluha statických prostředků

Server kolejnice v kontejneru Ruby běží ve výchozím nastavení v produkčním režimu a [předpokládá, že prostředky jsou předkompilovány a obsluhovány vaším webovým serverem](https://guides.rubyonrails.org/asset_pipeline.html#in-production). K obsluze statických prostředků ze serveru kolejnice je třeba provést dvě věci:

- **Předkompilování prostředků** - [předkompilování statických prostředků místně](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) a jejich ruční nasazení. Nebo ho modul pro nasazení místo toho zpracuje (viz [předkompilování prostředků](#precompile-assets).
- **Povolit obsluze statických souborů** – pro obsluhu statických prostředků z kontejneru Ruby [nastavte nastavení aplikace `RAILS_SERVE_STATIC_FILES`](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) na `true`. Příklad:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Spustit v neprodukčním režimu

Server kolejnice běží ve výchozím nastavení v provozním režimu. Chcete-li spustit v režimu pro vývoj, například nastavení [aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) `RAILS_ENV` na `development`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Toto nastavení ale samostatně způsobí, že se server kolejnice spustí v režimu vývoje, který přijímá pouze požadavky localhost a není přístupný mimo kontejner. Pokud chcete přijmout žádosti vzdáleného klienta, nastavte [nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) `APP_COMMAND_LINE` na `rails server -b 0.0.0.0`. Toto nastavení aplikace umožňuje spustit vlastní příkaz v kontejneru Ruby. Příklad:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a>Nastavit secret_key_base ručně

Pokud chcete místo toho, aby se vám App Service vygenerovala, použít vlastní `secret_key_base` hodnotu, nastavte `SECRET_KEY_BASE` [nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) tak, aby byla požadovaná hodnota. Příklad:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otevřít relaci SSH v prohlížeči

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: aplikace s kolejnicemi pomocí PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Nejčastější dotazy k App Service Linux](app-service-linux-faq.md)

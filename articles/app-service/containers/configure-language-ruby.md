---
title: Konfigurace aplikace v Ruby - Azure App Service
description: Tento kurz popisuje možnosti pro vytváření a konfiguraci aplikace v Ruby pro službu Azure App Service v Linuxu.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: astay;cephalin;kraigb
ms.custom: seodec18
ms.openlocfilehash: 402c85e7902c8c2f612ad6c777d8f6773a4d0ca3
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549977"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Konfigurace aplikace v Ruby v Linuxu pro Azure App Service

Tento článek popisuje, jak [služby Azure App Service](app-service-linux-intro.md) spouští aplikace v Ruby a jak můžete přizpůsobit chování služby App Service v případě potřeby. Aplikace v Ruby musí být nasazeny s všechny požadované [pip](https://pypi.org/project/pip/) moduly.

Tato příručka obsahuje klíčové koncepty a pokyny pro vývojáře v Ruby, kteří používají předdefinované kontejneru Linuxu ve službě App Service. Pokud jste nikdy použili službu Azure App Service, měli byste postupovat podle [Ruby quickstart](quickstart-ruby.md) a [Ruby s PostgreSQL kurzu](tutorial-ruby-postgres-app.md) první.

## <a name="show-ruby-version"></a>Zobrazit verze Ruby

Chcete-li zobrazit aktuální verze Ruby, spusťte následující příkaz [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Chcete-li zobrazit všechny podporované verze Ruby, spusťte následující příkaz [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

Je-li spustit nepodporovanou verzi Ruby, místo vytvoření vlastní image kontejneru. Další informace najdete v tématu [použití vlastní image Dockeru](tutorial-custom-docker-image.md).

## <a name="set-ruby-version"></a>Nastavení verze Ruby

Spuštěním následujícího příkazu [Cloud Shell](https://shell.azure.com) nastavit Ruby verze 2.3:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Pokud se zobrazí chyby podobná následující při nasazování:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> nebo
> ```
> rbenv: version `2.3.1' is not installed
> ```
> To znamená, že je Ruby verze nakonfigurované ve vašem projektu jiná než verze nainstalovaná v kontejneru, který používáte (`2.3.3` v předchozím příkladu). V příkladu výše, zkontrolujte i *Gemfile* a *.ruby verze* a ověřte, že verze Ruby není nastavena nebo je nastaven na verzi, která je nainstalovaná v kontejneru, který používáte (`2.3.3` v výše uvedený příklad).

## <a name="access-environment-variables"></a>Přístup k proměnným prostředí

Ve službě App Service můžete [nastavení aplikace](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) mimo kód vaší aplikace. Pak můžete přistupovat pomocí standardu [ENV ['< cesta name >']](https://ruby-doc.org/core-2.3.3/ENV.html) vzor. Například pro přístup k nastavení aplikace volá `WEBSITE_SITE_NAME`, použijte následující kód:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Vlastní nastavení nasazení

Při nasazení [úložiště Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), nebo [zazipovaný balíček s](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) s procesy sestavení zapnuté, modul pro nasazení (Kudu) automaticky spustí tyto kroky po nasazení ve výchozím nastavení:

1. Zkontrolujte, jestli *Gemfile* existuje.
1. Spusťte `bundle clean`. 
1. Spusťte `bundle install --path "vendor/bundle"`.
1. Spustit `bundle package` k gems balíček do složky mezipaměti nebo dodavatele.

### <a name="use---without-flag"></a>Pomocí parametru--bez příznaku

Ke spuštění `bundle install` s [– bez](https://bundler.io/man/bundle-install.1.html) příznak, nastavte `BUNDLE_WITHOUT` [nastavení aplikace, které](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) na seznam skupin oddělený čárkami. Například následující příkaz nastaví ji na `development,test`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Pokud je toto nastavení definované pak modul pro nasazení spouští `bundle install` s `--without $BUNDLE_WITHOUT`.

### <a name="precompile-assets"></a>Předkompilovat prostředky

Kroky po nasazení není ve výchozím nastavení předkompilovat prostředky. Chcete-li zapnout předkompilace asset, nastavte `ASSETS_PRECOMPILE` [nastavení aplikace, které](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) k `true`. Potom příkaz `bundle exec rake --trace assets:precompile` běží na konci kroky po nasazení. Příklad:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Další informace najdete v tématu [obsluhovat statické prostředky](#serve-static-assets).

## <a name="customize-start-up"></a>Přizpůsobení spuštění

Ve výchozím kontejneru Ruby spustí Rails server v následujícím pořadí (Další informace najdete v tématu [spouštěcí skript](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)):

1. Generování [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security) hodnotu, pokud již neexistuje. Tato hodnota je vyžadována pro spuštění v provozním režimu.
1. Nastavte `RAILS_ENV` proměnnou prostředí, aby `production`.
1. Odstraní všechny *.pid* soubor *tmp/PID* adresář, který je zanechaný dříve spuštěný server Rails.
1. Zaškrtněte, pokud jsou nainstalované všechny závislosti. Pokud ne, zkuste nainstalovat gems z místního *mezipaměti nebo dodavatele* adresáře.
1. Spusťte `rails server -e $RAILS_ENV`.

Spuštění procesu můžete přizpůsobit následujícími způsoby:

- [Poskytování statických prostředků](#serve-static-assets)
- [Spustit v režimu o neprodukční](#run-in-non-production-mode)
- [Nastavit secret_key_base ručně](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Poskytování statických prostředků

Server Rails v kontejneru Ruby ve výchozím nastavení, spustí v provozním režimu a [předpokládá, že prostředky předkompilovány a webový server obsluhuje](https://guides.rubyonrails.org/asset_pipeline.html#in-production). K poskytování statických prostředků ze serveru Rails, musíte udělat dvě věci:

- **Předkompilovat prostředky** - [předkompilovat statické prostředky místně](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) a nasazovat je ručně. Nebo nechat ji zpracovat místo toho modul pro nasazení (viz [předkompilovat prostředky](#precompile-assets).
- **Povolit zpracování statických souborů** – Pokud chcete obsluhovat statické prostředky z kontejneru Ruby, nastavte `RAILS_SERVE_STATIC_FILES` [nastavit `RAILS_SERVE_STATIC_FILES` nastavení aplikace, které](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) k `true`. Příklad:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Spustit v režimu o neprodukční

Ve výchozím nastavení spustí v provozním režimu Rails server. Pokud chcete spustit v režimu pro vývoj, například nastavit `RAILS_ENV` [nastavení aplikace, které](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) k `development`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Však samotný toto nastavení způsobí, že server Rails spustit v režimu pro vývoj, který přijímá localhost pouze požadavky a není přístupná mimo svůj kontejner. Pro příjem požadavků vzdáleného klienta, nastavte `APP_COMMAND_LINE` [nastavení aplikace, které](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) k `rails server -b 0.0.0.0`. Tato nastavení aplikace, které umožňuje spuštění vlastního příkazu v kontejneru Ruby. Příklad:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secretkeybase-manually"></a>Nastavit secret_key_base ručně

Vlastní `secret_key_base` místo služby App Service vytvořit za vás, nastavte hodnotu `SECRET_KEY_BASE` [nastavení aplikace, které](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) s hodnotu, kterou chcete. Příklad:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otevřít relaci SSH v prohlížeči

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: Aplikace Rails s PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [App Service Linuxu – nejčastější dotazy](app-service-linux-faq.md)
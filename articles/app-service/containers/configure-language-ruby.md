---
title: Konfigurace aplikací Ruby – služba Azure App Service
description: Přečtěte si, jak pro vaši aplikaci nakonfigurovat předem sestavený kontejner Ruby. Tento článek ukazuje nejběžnější úlohy konfigurace.
ms.topic: quickstart
ms.date: 03/28/2019
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18
ms.openlocfilehash: 8daebba840223d050a14b4b99cb6ae15472ee4f5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80046329"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Konfigurace aplikace Linux Ruby pro službu Azure App Service

Tento článek popisuje, jak [služba Azure App Service](app-service-linux-intro.md) spouští aplikace Ruby a jak můžete přizpůsobit chování služby App Service v případě potřeby. Aplikace Ruby musí být nasazeny se všemi [požadovanými drahokamy](https://rubygems.org/gems).

Tato příručka obsahuje klíčové koncepty a pokyny pro vývojáře Ruby, kteří používají integrovaný kontejner Linuxu ve službě App Service. Pokud jste službu Azure App Service nikdy nepoužívali, měli byste nejprve postupovat podle [rychlého startu Ruby](quickstart-ruby.md) a [Ruby s postgreSQL kurzem.](tutorial-ruby-postgres-app.md)

## <a name="show-ruby-version"></a>Zobrazit verzi Ruby

Chcete-li zobrazit aktuální verzi Ruby, spusťte v [prostředí Cloud Shell](https://shell.azure.com)následující příkaz :

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Chcete-li zobrazit všechny podporované verze Ruby, spusťte v [prostředí Cloud Shell](https://shell.azure.com)následující příkaz :

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

Nepodporovanou verzi Ruby můžete spustit tak, že místo toho nastavíte vlastní image kontejneru. Další informace naleznete [v tématu použití vlastní image Dockeru](tutorial-custom-docker-image.md).

## <a name="set-ruby-version"></a>Nastavit verzi Ruby

Spusťte následující příkaz v [prostředí Cloud Shell](https://shell.azure.com) a nastavte verzi Ruby na verzi 2.3:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Pokud se během doby nasazení zobrazí podobné chybám jako následující:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> – nebo –
> ```
> rbenv: version `2.3.1' is not installed
> ```
> To znamená, že verze Ruby nakonfigurovaná ve vašem projektu se liší`2.3.3` od verze, která je nainstalovaná v kontejneru, který používáte (v příkladu výše). Ve výše uvedeném příkladu zkontrolujte *verzi Gemfile* i *.ruby* a ověřte, zda není verze Ruby nastavena nebo`2.3.3` je nastavena na verzi nainstalovanou v kontejneru, který používáte (v příkladu výše).

## <a name="access-environment-variables"></a>Přístup k proměnným prostředí

Ve službě App Service můžete [nastavit nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) mimo kód aplikace. Pak k nim můžete přistupovat pomocí standardního vzoru [ENV['\<path-name>'].](https://ruby-doc.org/core-2.3.3/ENV.html) Chcete-li například získat `WEBSITE_SITE_NAME`přístup k nastavení aplikace s názvem , použijte následující kód:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Přizpůsobení nasazení

Když nasadíte [úložiště Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)nebo [balíček Zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) se zapnutými procesy sestavení, modul nasazení (Kudu) automaticky ve výchozím nastavení spustí následující kroky po nasazení:

1. Zkontrolujte, zda *gemfile* existuje.
1. Spusťte `bundle clean`. 
1. Spusťte `bundle install --path "vendor/bundle"`.
1. Spusťte `bundle package` balíček drahokamů do složky dodavatele/mezipaměti.

### <a name="use---without-flag"></a>Použít --bez příznaku

Chcete-li spustit `bundle install` s [--without](https://bundler.io/man/bundle-install.1.html) flag, nastavte `BUNDLE_WITHOUT` nastavení [aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) na seznam skupin oddělených čárkami. Například následující příkaz jej `development,test`nastaví na .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Pokud je toto nastavení definováno, modul nasazení běží `bundle install` s `--without $BUNDLE_WITHOUT`.

### <a name="precompile-assets"></a>Předkompilovat datové zdroje

Kroky po nasazení nejsou předkompilovat prostředky ve výchozím nastavení. Chcete-li zapnout předkompilaci datových `ASSETS_PRECOMPILE` zdrojů, nastavte nastavení [aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) na . `true` Poté je `bundle exec rake --trace assets:precompile` příkaz spuštěn na konci kroků po nasazení. Například:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Další informace naleznete v tématu [Serve static assets](#serve-static-assets).

## <a name="customize-start-up"></a>Přizpůsobení spuštění

Ve výchozím nastavení spustí kontejner Ruby server Rails v následujícím pořadí (další informace naleznete ve [skriptu pro spuštění](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)):

1. Vygenerujte [hodnotu secret_key_base,](https://edgeguides.rubyonrails.org/security.html#environmental-security) pokud již neexistuje. Tato hodnota je vyžadována pro spuštění aplikace v produkčním režimu.
1. Nastavte `RAILS_ENV` proměnnou `production`prostředí na .
1. Odstraňte libovolný soubor *.pid* v adresáři *tmp/pids,* který je ponechán dříve spuštěným serverem Rails.
1. Zkontrolujte, zda jsou nainstalovány všechny závislosti. Pokud ne, zkuste nainstalovat drahokamy z adresáře místního *dodavatele/mezipaměti.*
1. Spusťte `rails server -e $RAILS_ENV`.

Proces spuštění můžete přizpůsobit následujícími způsoby:

- [Obsluha statických datových zdrojů](#serve-static-assets)
- [Spustit v neprodukčním režimu](#run-in-non-production-mode)
- [Ruční nastavení secret_key_base](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Obsluha statických datových zdrojů

Server Rails v kontejneru Ruby je ve výchozím nastavení spuštěn v produkčním režimu a [předpokládá, že datové zdroje jsou předkompilovány a jsou obsluhovány webovým serverem](https://guides.rubyonrails.org/asset_pipeline.html#in-production). Chcete-li obsluhovat statické datové zdroje ze serveru Rails, musíte udělat dvě věci:

- **Předkompilujte datové zdroje** - [Předkompilujte statické prostředky místně](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) a nasaďte je ručně. Nebo nechte modul nasazení zpracovat místo toho (viz [Předkompilace datových zdrojů](#precompile-assets).
- **Povolení zobrazování statických souborů** – Chcete-li obsluhovat statické `true`datové zdroje z kontejneru Ruby, nastavte nastavení [ `RAILS_SERVE_STATIC_FILES` aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) na . Například:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Spustit v neprodukčním režimu

Server Rails je ve výchozím nastavení spuštěn v produkčním režimu. Chcete-li spustit v režimu `RAILS_ENV` vývoje, `development`například nastavte nastavení [aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) na .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Toto nastavení však samo o sobě způsobí, že server Rails spustit v režimu vývoje, který přijímá pouze požadavky localhost a není přístupný mimo kontejner. Chcete-li přijímat požadavky vzdálených klientů, nastavte `APP_COMMAND_LINE` nastavení [aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) na . `rails server -b 0.0.0.0` Toto nastavení aplikace umožňuje spustit vlastní příkaz v kontejneru Ruby. Například:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a><a name="set-secret_key_base-manually"></a>Ruční nastavení secret_key_base

Pokud chcete `secret_key_base` použít vlastní hodnotu, místo toho, `SECRET_KEY_BASE` aby služba App Service pro vás vygenerovala jednu, nastavte [nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) podle požadované hodnoty. Například:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otevření relace SSH v prohlížeči

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Výuka: Rails aplikace s PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Nejčastější dotazy k aplikační službě Linux](app-service-linux-faq.md)

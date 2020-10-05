---
title: Konfigurace aplikací v Ruby – Azure App Service
description: Zjistěte, jak konfigurovat předem připravený kontejner Ruby pro vaši aplikaci. Tento článek ukazuje nejběžnější konfigurační úlohy.
ms.topic: quickstart
ms.date: 06/18/2020
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18
ms.openlocfilehash: c822dbdf9940db7b38d354fa32906c16977df0c0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88084948"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Konfigurace linuxové aplikace v Ruby pro Azure App Service

Tento článek popisuje, jak [Azure App Service](overview.md) spouští aplikace v Ruby v linuxovém kontejneru a jak můžete podle potřeby přizpůsobit chování služby App Service. Aplikace v Ruby musí být nasazené se všemi požadovanými [gemy](https://rubygems.org/gems).

V této příručce najdete klíčové koncepty a pokyny pro vývojáře v Ruby, kteří používají integrovaný kontejner Linux ve službě App Service. Pokud jste Azure App Service ještě nepoužívali, měli byste nejdřív postupovat podle [rychlého zprovoznění pro Ruby](quickstart-ruby.md) a kurzu [Ruby s PostgreSQL](tutorial-ruby-postgres-app.md).

## <a name="show-ruby-version"></a>Zobrazení verze Ruby

Aktuální verzi Ruby zobrazíte spuštěním následujícího příkazu v [Cloud Shellu](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

K zobrazení všech podporovaných verzí Ruby použijte následující příkaz v [Cloud Shellu](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

Pokud si sestavíte vlastní image kontejneru, můžete spustit nepodporovanou verzi Ruby. Další informace najdete v tématu věnovaném [použití vlastní image Dockeru](tutorial-custom-container.md?pivots=container-linux).

## <a name="set-ruby-version"></a>Nastavení verze Ruby

Pokud chcete verzi Ruby nastavit na 2.3, spusťte v [Cloud Shellu](https://shell.azure.com) následující příkaz:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Pokud se vám během nasazování zobrazují chyby typu
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> nebo
> ```
> rbenv: version `2.3.1' is not installed
> ```
> , znamená to, že verze Ruby nakonfigurovaná ve vašem projektu se liší od verze, která je nainstalovaná v kontejneru, který používáte (ve výše uvedeném příkladu `2.3.3`). V předchozím příkladu zkontrolujte *Gemfile* a *.ruby-version* a ověřte, že verze Ruby není nastavená, nebo ji nastavte na verzi nainstalovanou v kontejneru, který spouštíte (ve výše uvedeném příkladu `2.3.3`).

## <a name="access-environment-variables"></a>Přístup k proměnným prostředí

V App Service můžete [nastavit nastavení aplikace](configure-common.md#configure-app-settings) mimo kód vaší aplikace. Pro přístup k nim potom můžete využít standardní vzor [ENV['\<path-name>']](https://ruby-doc.org/core-2.3.3/ENV.html). Například pro přístup k aplikačnímu nastavení s názvem `WEBSITE_SITE_NAME` použijete následující kód:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Přizpůsobení nasazení

Když nasadíte [úložiště Git](deploy-local-git.md) nebo [balíček Zip](deploy-zip.md) a jsou aktivované procesy sestavení, nasazovací modul (Kudu) ve výchozím nastavení po ukončení nasazení automaticky spustí následující kroky:

1. Zkontrolujte, jestli *Gemfile* existuje.
1. Spusťte `bundle clean`. 
1. Spusťte `bundle install --path "vendor/bundle"`.
1. Spusťte `bundle package` pro zabalení gemů do složky vendor/cache.

### <a name="use---without-flag"></a>Použití příznaku --without

Pokud chcete spustit `bundle install` s příznakem [--without](https://bundler.io/man/bundle-install.1.html), jako [aplikační nastavení](configure-common.md#configure-app-settings) `BUNDLE_WITHOUT` nastavte seznam skupin oddělených čárkou. Například následující příkaz nastaví hodnotu `development,test`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Pokud je toto nastavení definované, modul nasazení spustí `bundle install` s nastavením `--without $BUNDLE_WITHOUT`.

### <a name="precompile-assets"></a>Předkompilace prostředků

Kroky po dokončení nasazení ve výchozím nastavení neprovádějí předkompilaci prostředků. Pokud chcete předkompilaci prostředků aktivovat, nastavte [aplikační nastavení](configure-common.md#configure-app-settings) `ASSETS_PRECOMPILE` na `true`. Následně se na konci kroků po nasazení spustí příkaz `bundle exec rake --trace assets:precompile`. Příklad:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Další informace najdete v tématu [Obsluha statických prostředků](#serve-static-assets).

## <a name="customize-start-up"></a>Přizpůsobení spuštění

Ve výchozím nastavení kontejner Ruby spustí server Rails v následujícím pořadí (další informace najdete v popisu [spouštěcího skriptu](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)):

1. Vygenerujte hodnotu [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security), pokud ještě neexistuje. Tato hodnota se vyžaduje pro spuštění aplikace v provozním režimu.
1. Proměnnou prostředí `RAILS_ENV` nastavte na `production`.
1. Odstraňte všechny *. PID* v adresáři *tmp/pids*, které za sebou zanechal předchozí spuštěný server Rails.
1. Ověřte, jestli jsou nainstalované všechny závislosti. Pokud ne, zkuste nainstalovat gemy z místního adresáře *vendor/cache*.
1. Spusťte `rails server -e $RAILS_ENV`.

Proces spouštění můžete přizpůsobit následujícími způsoby:

- [Obsluha statických prostředků](#serve-static-assets)
- [Spuštění v neprodukčním režimu](#run-in-non-production-mode)
- [Ruční nastavení secret_key_base](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Obsluha statických prostředků

Server Rails v kontejneru Ruby ve výchozím nastavení běží v produkčním režimu a [předpokládá, že prostředky jsou předkompilované a obsluhuje je váš webový server](https://guides.rubyonrails.org/asset_pipeline.html#in-production). Pokud chcete obsluhovat statické prostředky ze serveru Rails, musíte udělat dvě věci:

- **Předkompilace prostředků:**  - [Předkompilujte statické prostředky místně](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) a ručně je nasaďte. Tyto operace můžete také nechat na nasazovacím modulu (viz [Předkompilace prostředků](#precompile-assets)).
- **Povolení obsluhy statických souborů:** Pokud chcete statické prostředky obsluhovat z kontejneru Ruby, [nastavte aplikační nastavení](configure-common.md#configure-app-settings) `RAILS_SERVE_STATIC_FILES` na `true`. Příklad:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Spuštění v neprodukčním režimu

Server Rails ve výchozím nastavení běží v produkčním režimu. Pokud do chcete například spustit ve vývojářském režimu, nastavte [aplikační nastavení](configure-common.md#configure-app-settings) `RAILS_ENV` na `development`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Toto nastavení ale způsobí, že se server Rails spustí ve vývojářském režimu, který přijímá jenom požadavky localhost a není přístupný mimo kontejner. Pokud chcete přijímat požadavky vzdálených klientů, nastavte [aplikační nastavení](configure-common.md#configure-app-settings) `APP_COMMAND_LINE` na `rails server -b 0.0.0.0`. Toto nastavení umožňuje spustit vlastní příkaz v kontejneru Ruby. Příklad:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a><a name="set-secret_key_base-manually"></a>Ruční nastavení secret_key_base

Pokud chcete použít vlastní hodnotu `secret_key_base` (a nenechávat službu App Service, aby ji vytvořila za vás), nastavte [aplikační nastavení](configure-common.md#configure-app-settings) `SECRET_KEY_BASE` na požadovanou hodnotu. Příklad:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otevření relace SSH v prohlížeči

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Aplikace v Rails s PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Nejčastější dotazy k App Service v Linuxu](faq-app-service-linux.md)

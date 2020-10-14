---
title: 'Rychlý Start: váš první dotaz na Ruby'
description: V tomto rychlém startu budete postupovat podle pokynů pro povolení grafu prostředků Gem pro Ruby a spuštění prvního dotazu.
ms.date: 10/14/2020
ms.topic: quickstart
ms.openlocfilehash: 4ffd7f33fb83b7a1f247f687b77dd2ef9c8e5bc9
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057259"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-ruby"></a>Rychlý Start: spuštění prvního dotazu na graf prostředku pomocí Ruby

Prvním krokem k použití Azure Resource graphu je ověření, že je nainstalovaná požadovaná Gems pro Ruby. Tento rychlý Start vás provede procesem přidání Gems do instalace Ruby.

Na konci tohoto procesu přidáte Gems do instalace Ruby a spustíte váš první dotaz na graf prostředku.

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- Objekt služby Azure, včetně _ClientID_ a _clientSecret_.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-graph-project"></a>Vytvoření projektu prostředku grafu

Pokud chcete povolit dotazování na Azure Resource Graph, musí být Gem přidaný do `Gemfile` . Tato Gem funguje bez ohledu na to, kde je možné použít Ruby, včetně [Azure Cloud Shell](https://shell.azure.com), [bash ve Windows 10](/windows/wsl/install-win10)nebo lokálně nainstalovaných.

1. Ověřte, že je nainstalovaný poslední Ruby (aspoň **2.7.1**). Pokud ještě není nainstalovaný, Stáhněte si ho na adrese [Ruby-lang.org](https://www.ruby-lang.org/en/downloads/).

1. V prostředí Ruby podle vlastního výběru inicializujte sadu prostředků v nové složce projektu:

   ```bash
   # Initialize a bundle to create a new Gemfile
   bundle init
   ```

1. Aktualizujte si `Gemfile` je pomocí Gems, který je potřeba pro Azure Resource Graph. Aktualizovaný soubor by měl vypadat podobně jako v tomto příkladu:

   ```file
   # frozen_string_literal: true

   source "https://rubygems.org"

   git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

   # gem "rails"
   gem 'azure_mgmt_resourcegraph', '~> 0.17.2'
   ```

1. Ze složky projektu spusťte `bundle install` . Potvrďte, že se Gems nainstaloval `bundle list` .

1. Ve stejné složce projektu vytvořte `argQuery.rb` pomocí následujícího kódu a uložte aktualizovaný soubor:

   ```ruby
   #!/usr/bin/env ruby

   require 'azure_mgmt_resourcegraph'
   ARG = Azure::ResourceGraph::Profiles::Latest::Mgmt

   # Get arguments and set options
   options = {
       tenant_id: ARGV[0],
       client_id: ARGV[1],
       client_secret: ARGV[2],
       subscription_id: ARGV[3]
   }

   # Create Resource Graph client from options
   argClient = ARG::Client.new(options)

   # Create Resource Graph QueryRequest for subscription with query
   query_request = ARGModels::QueryRequest.new.tap do |qr|
       qr.subscriptions = [ARGV[3]]
       qr.query = ARGV[4]
   end

   # Get the resources from Resource Graph
   response = argClient.resources(query_request)

   # Convert data to JSON and output
   puts response.data.to_json
   ```

## <a name="run-your-first-resource-graph-query"></a>Spusťte nejdříve dotaz na Resource Graph použitím Azure CLI

Když je skript Ruby uložený a připravený k použití, je čas vyzkoušení jednoduchého dotazu na graf Resource. Dotaz vrátí prvních pět prostředků Azure s **názvem** a **typem prostředku** každého prostředku.

V každém volání metody `argQuery` jsou použity proměnné, které je třeba nahradit vlastními hodnotami:

- `{tenantId}` – Nahraďte ID tenanta.
- `{clientId}` – Nahraďte ID klienta objektu služby.
- `{clientSecret}` – Nahraďte tajným kódem klienta vašeho instančního objektu.
- Proměnnou `{subscriptionId}` nahraďte ID předplatného.

1. Změňte adresář na složku projektu, kde jste vytvořili `Gemfile` soubory a `argClient.rb` .

1. Spusťte svůj první dotaz na Azure Resource Graph pomocí Gems a `resources` metody:

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > Na tento dotaz příklad neposkytuje modifikátor řazení jako `order by`, opakované spouštění tohoto dotazu pravděpodobně poskytne jinou sadu zdrojů na jednu žádost.

1. Změňte konečný parametr na `argQuery.rb` a změňte dotaz na `order by` vlastnost **Name** :

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > Stejně jako u prvního dotazu opakované spouštění tohoto dotazu pravděpodobně poskytne jinou sadu zdrojů na jednu žádost. Pořadí příkazů dotazů je důležité. V tomto příkladu `order by` přichází po `limit`. Toto pořadí příkazů nejprve omezí výsledky dotazu a pak je vyřadí.

1. Změňte konečný parametr na `argQuery.rb` a změňte dotaz na `order by` **název vlastnosti název** a pak `limit` na horních pět výsledků:

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

Když se konečný dotaz několikrát spustí, předpokládá se, že se nic ve vašem prostředí nemění, vrácené výsledky jsou konzistentní a seřazené podle vlastnosti **Name** , ale pořád se omezí na pět nejlepších výsledků.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat nainstalované Gems z prostředí Ruby, můžete k tomu použít následující příkaz:

```bash
# Remove the installed gems from the Ruby environment
gem uninstall azure_mgmt_resourcegraph
```

> [!NOTE]
> Gem `azure_mgmt_resourcegraph` má závislosti `ms_rest` , například a `ms_rest_azure` , které mohly být také nainstalovány v závislosti na vašem prostředí. Tyto Gems můžete odinstalovat i v případě, že už nepotřebujete.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přidali graf prostředků Gems do prostředí Ruby a spustili svůj první dotaz. Chcete-li získat další informace o jazyku grafu prostředků, přejděte na stránku podrobností dotazovacího jazyka.

> [!div class="nextstepaction"]
> [Získat další informace o dotazovacím jazyku](./concepts/query-language.md)

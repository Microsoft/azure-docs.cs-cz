---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: 16ce537a54fc77fc0f72b859d6d193501d86c1fc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "67175240"
---
## <a name="create-a-ruby-application"></a>Vytvoření aplikace Ruby
Pokyny najdete [v tématu Vytvoření aplikace Ruby v Azure](../articles/virtual-machines/linux/classic/ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurace aplikace pro použití služby Service Bus
Chcete-li používat Service Bus, stáhněte a použijte balíček Azure Ruby, který obsahuje sadu knihoven pohodlí, které komunikují se službami REST úložiště.

### <a name="use-rubygems-to-obtain-the-package"></a>Získání balíčku pomocí RubyGems
1. Použijte rozhraní příkazového řádku, jako je **PowerShell** (Windows), **Terminál** (Mac) nebo **Bash** (Unix).
2. Zadejte "gem install azure" v okně příkazu pro instalaci drahokamu a závislostí.

### <a name="import-the-package"></a>Import balíčku
Pomocí oblíbeného textového editoru přidejte do horní části souboru Ruby, ve kterém chcete použít úložiště, následující:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Nastavení připojení služby Service Bus
Pomocí následujícího kódu nastavte hodnoty oboru názvů, název klíče, klíče, autora podpisu a hostitele:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Nastavte hodnotu oboru názvů na hodnotu, kterou jste vytvořili, nikoli na celou adresu URL. Například použijte **"yourexamplenamespace"**, ne "yourexamplenamespace.servicebus.windows.net".

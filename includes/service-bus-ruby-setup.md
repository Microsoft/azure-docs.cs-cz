---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: 16ce537a54fc77fc0f72b859d6d193501d86c1fc
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52269636"
---
## <a name="create-a-ruby-application"></a>Vytvoření aplikace v Ruby
Pokyny najdete v tématu [vytvoření aplikace Ruby v Azure](../articles/virtual-machines/linux/classic/ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurace aplikace pro použití služby Service Bus
Použití služby Service Bus, stáhnout a použít balíček Azure Ruby, který obsahuje sadu knihoven pohodlí, které komunikují s REST služby úložiště.

### <a name="use-rubygems-to-obtain-the-package"></a>Získání balíčku pomocí RubyGems
1. Použijte rozhraní příkazového řádku, jako je **PowerShell** (Windows), **Terminál** (Mac) nebo **Bash** (Unix).
2. Zadejte "azure gem instalace" v příkazovém okně instalace gem a závislostí.

### <a name="import-the-package"></a>Import balíčku
Pomocí oblíbeného textového editoru, přidejte následující text k horní části souboru Ruby, ve které máte v úmyslu používat úložiště:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Nastavit připojení služby Service Bus
K nastavení hodnoty oboru názvů, název klíče, klíče, podepisující osoba a hostitele, použijte následující kód:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Nastavte hodnotu oboru názvů na hodnotu, kterou jste vytvořili místo celou adresu URL. Například použít **"yourexamplenamespace"**, ne "yourexamplenamespace.servicebus.windows.net".

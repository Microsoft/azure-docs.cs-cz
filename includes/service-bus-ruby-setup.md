---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: 5c2959a1bf6225c164f8538c3c437e464d834b96
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96028565"
---
## <a name="create-a-ruby-application"></a>Vytvoření aplikace v Ruby
Pokyny najdete v tématu [Vytvoření aplikace v Ruby v Azure](/previous-versions/azure/virtual-machines/linux/classic/ruby-rails-web-app).

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurace aplikace pro použití Service Bus
Pokud chcete použít Service Bus, Stáhněte a použijte balíček Azure Ruby, který obsahuje sadu praktických knihoven, které komunikují se službou REST (Storage).

### <a name="use-rubygems-to-obtain-the-package"></a>Získání balíčku pomocí RubyGems
1. Použijte rozhraní příkazového řádku, jako je **PowerShell** (Windows), **Terminál** (Mac) nebo **Bash** (Unix).
2. Zadáním příkazu "gem Install Azure" v příkazovém okně nainstalujte Gem a závislosti.

### <a name="import-the-package"></a>Import balíčku
Pomocí oblíbeného textového editoru přidejte na začátek souboru Ruby, ve kterém chcete úložiště použít, následující:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Nastavení Service Busho připojení
Použijte následující kód k nastavení hodnot oboru názvů, názvu klíče, podpisu a hostitele:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Nastavte hodnotu oboru názvů na hodnotu, kterou jste vytvořili, a ne na celou adresu URL. Použijte například **"yourexamplenamespace"**, nikoli "yourexamplenamespace.ServiceBus.Windows.NET".

Při práci s více obory názvů můžete předat klíč a jeho název konstruktoru při vytváření `SharedAccessSigner` objektů.

```ruby
sb_namespace = '<your azure service bus namespace>'
sb_sas_key_name = '<your azure service bus access keyname>'
sb_sas_key = '<your azure service bus access key>'

signer = Azure::ServiceBus::Auth::SharedAccessSigner.new(sb_sas_key_name, sb_sas_key)
sb_host = "https://#{sb_namespace}.servicebus.windows.net"
```
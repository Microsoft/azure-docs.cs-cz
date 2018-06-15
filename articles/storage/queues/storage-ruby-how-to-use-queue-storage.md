---
title: Používání úložiště Queue z Ruby | Microsoft Docs
description: Naučte se používat službu front Azure k vytváření a odstraňování front a vložit, získání a odstranění zprávy. Ukázky napsané v Ruby.
services: storage
documentationcenter: ruby
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 59c2d81b-db9c-46ee-ade2-2f0caae6b1e6
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: 0d7624d47a6924a5c8dec66b47ac0887ff493879
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2018
ms.locfileid: "27993632"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Používání úložiště Queue z Ruby
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled
Tento průvodce vám ukáže, jak provádět běžné scénáře pomocí služby Microsoft Azure Queue Storage. Ukázky jsou zapsány pomocí rozhraní API služby Azure Ruby.
Pokryté scénáře zahrnují **vkládání**, **prohlížení**, **získávání**, a **odstraňování** fronty zpráv, a také **vytváření a odstraňování front**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Vytvoření Ruby aplikace
Vytvořte aplikaci pro poznámky Ruby. Pokyny najdete v tématu [vytvoření Ruby aplikace ve službě App Service v systému Linux](https://docs.microsoft.com/azure/app-service/containers/quickstart-ruby).

## <a name="configure-your-application-to-access-storage"></a>Konfigurace aplikace pro přístup k úložišti
Pokud chcete používat úložiště Azure, musíte stáhnout a použít Ruby azure balíčku, který obsahuje sadu knihoven pohodlí, které komunikují s služby REST úložiště.

### <a name="use-rubygems-to-obtain-the-package"></a>Použití RubyGems získat balíček
1. Pomocí rozhraní příkazového řádku, jako například **prostředí PowerShell** (Windows), **Terminálové** (Mac), nebo **Bash** (Unix).
2. "Gem instalace azure" zadejte v příkazovém okně instalace gem a závislostí.

### <a name="import-the-package"></a>Import balíčku
Použít svém oblíbeném textovém editoru, přidejte na začátek souboru Ruby, kde máte v úmyslu používat úložiště následující:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Nastavit připojení k úložišti Azure
Modul azure, bude číst proměnné prostředí **AZURE\_úložiště\_účet** a **AZURE\_úložiště\_ACCESS_KEY** informace požadované pro připojení k účtu úložiště Azure. Pokud nejsou nastavené těchto proměnných prostředí, je nutné zadat informace o účtu před použitím **Azure::QueueService** následujícím kódem:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

K získání těchto hodnot z klasický nebo účet správce prostředků úložiště na portálu Azure:

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Přejděte na účet úložiště, který chcete použít.
3. V okně nastavení na pravé straně klikněte na tlačítko **přístupové klíče**.
4. V okně klíče přístup, který se zobrazí uvidíte přístupový klíč 1 a 2 přístupový klíč. Můžete použít kteroukoli z nich. 
5. Kliknutím na ikonu kopírování do schránky zkopírujte klíč. 

## <a name="how-to-create-a-queue"></a>Postupy: Vytvoření fronty
Následující kód vytvoří **Azure::QueueService** objekt, který umožňuje pracovat s fronty.

```ruby
azure_queue_service = Azure::QueueService.new
```

Použití **create_queue()** metodu pro vytvoření fronty se zadaným názvem.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Postupy: Vložit zprávu do fronty
Chcete-li vložit zprávu do fronty, použijte **create_message()** metodu pro vytvoření nové zprávy a přidejte ji do fronty.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Postupy: Zobrazení náhledu další zprávy
Můžete prohlížet zprávy ve frontě bez odebere ji z fronty voláním **funkce Náhled\_messages()** metoda. Ve výchozím nastavení **funkce Náhled\_messages()** prohlédne do jedné zprávy. Můžete také zadat počet zpráv, které chcete prohlížet.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Postupy: Dequeue – další zprávy
Můžete odebrat zprávu z fronty ve dvou krocích.

1. Při volání **seznamu\_messages()**, získáte další zprávu ve frontě ve výchozím nastavení. Můžete také zadat počet zpráv, které chcete získat. Zpráv vrácených z **seznamu\_messages()** stane neviditelnou pro jakýkoli jiný kód čtení zpráv z této fronty. Můžete předat časový limit viditelnosti v sekundách jako parametr.
2. K dokončení odebrání zprávy z fronty, musíte také zavolat **delete_message()**.

Tento dvoukrokový proces odebrání zprávy zaručuje, že když kódu se nepodaří zpracovat zprávu z důvodu selhání hardwaru nebo softwaru, jiná instance vašeho kódu můžete stejnou zprávu získat a zkuste to znovu. Váš kód zavolá metodu **odstranit\_message()** pravým po zpracování zprávy.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Postupy: Změna obsahu zpráv zařazených ve frontě
Podle potřeby můžete změnit obsah zprávy přímo ve frontě. Kód pod používá **update_message()** metoda aktualizace zprávu. Metoda vrátí řazené kolekce členů, která obsahuje pop příjmu zprávy ve frontě a hodnotu Datum čas UTC, která představuje při zpráva se bude zobrazovat na fronty.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Postupy: Další možnosti pro vyřazení zprávy
Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby.

1. Můžete načíst dávku zpráv.
2. Můžete nastavit časový limit neviditelnosti delší nebo kratší, aby měl váš kód více nebo méně času na úplné zpracování jednotlivých zpráv.

Následující příklad kódu používá **seznamu\_messages()** metoda získat 15 zpráv v jednom volání. Pak se vytiskne a odstraní se každá zpráva. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Postupy: Získání délky fronty
Můžete získat odhad počet zpráv ve frontě. **Získat\_fronty\_metadata()** metoda požádá službu front vrátí počet zpráv přibližnou a metadata o frontě.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Postupy: Odstranění fronty
Chcete-li odstranit frontu se všemi zprávami, které v ní, zavolejte **odstranit\_queue()** metoda pro objekt fronty.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy používání služby queue storage, postupujte podle následujících odkazech na další informace o složitějších úlohách úložiště.

* Přejděte [Blog týmu Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/)
* Přejděte [Azure SDK pro Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) úložišti na Githubu

Porovnání mezi službou Azure fronty popsané v tomto článku a fronty služby Service Bus Azure popsané v [jak používat fronty služby Service Bus](/develop/ruby/how-to-guides/service-bus-queues/) článku najdete v tématu [fronty Azure a fronty služby Service Bus - porovnání a Rozdíl od aktualizovaného](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
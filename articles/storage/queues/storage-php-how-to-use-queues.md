---
title: Použití Queue Storage z PHP – Azure Storage
description: Naučte se používat službu Azure Queue Storage k vytváření a odstraňování front a vkládání, získávání a odstraňování zpráv. Ukázky jsou napsané v PHP.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 01/11/2018
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 69369d81892a10c390aa31a2c46f79fdfa41206d
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592020"
---
# <a name="how-to-use-queue-storage-from-php"></a>Použití Queue Storage z PHP

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

V této příručce se dozvíte, jak provádět běžné scénáře pomocí služby Azure Queue Storage. Ukázky jsou zapisovány prostřednictvím tříd z [klientské knihovny Azure Storage pro php](https://github.com/Azure/azure-storage-php). Zahrnuté scénáře zahrnují vkládání, prohlížení, získávání a odstraňování zpráv front a vytváření a odstraňování front.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Vytvoření aplikace PHP

Jediným požadavkem pro vytvoření aplikace PHP, která přistupuje k Azure Queue Storage, je odkazování tříd v [klientské knihovně Azure Storage pro php](https://github.com/Azure/azure-storage-php) z vašeho kódu. K vytvoření aplikace můžete použít jakékoli vývojové nástroje, včetně Poznámkového bloku.

V této příručce použijete funkce služby Queue Storage, které lze volat v rámci aplikace PHP místně, nebo v kódu běžícím v rámci webové aplikace v Azure.

## <a name="get-the-azure-client-libraries"></a>Získání klientských knihoven Azure

### <a name="install-via-composer"></a>Instalace prostřednictvím skladatele

1. Vytvořte soubor s názvem `composer.json` v kořenovém adresáři projektu a přidejte do něj následující kód:

    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```

2. Stáhněte si [`composer.phar`](https://getcomposer.org/composer.phar) do kořenového adresáře projektu.

3. Otevřete příkazový řádek a v kořenovém adresáři projektu spusťte následující příkaz:

    ```console
    php composer.phar install
    ```

Můžete také přejít na [knihovnu klienta PHP Azure Storage](https://github.com/Azure/azure-storage-php) na GitHubu a klonovat zdrojový kód.

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurace aplikace pro přístup k Queue Storage

Pokud chcete používat rozhraní API pro Azure Queue Storage, musíte:

1. Odkazování na soubor automatického zavaděče pomocí [`require_once`](https://www.php.net/manual/en/function.require-once.php) příkazu.
2. Odkázat na libovolné třídy, které můžete použít.

Následující příklad ukazuje, jak zahrnout soubor automatického zavaděče a odkazovat na `QueueRestProxy` třídu.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

V následujících příkladech `require_once` je příkaz zobrazen vždy, ale jsou odkazovány pouze třídy, které jsou požadovány ke spuštění příkladu.

## <a name="set-up-an-azure-storage-connection"></a>Nastavení Azure Storageho připojení

Pokud chcete vytvořit instanci klienta Azure Queue Storage, musíte nejdřív mít platný připojovací řetězec. Formát připojovacího řetězce Queue Storage je následující.

Přístup k živé službě:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Pro přístup k úložišti emulátoru:

```php
UseDevelopmentStorage=true
```

Chcete-li vytvořit klienta služby Azure Queue Storage, je nutné použít `QueueRestProxy` třídu. Můžete použít některý z následujících postupů:

- Předání připojovacího řetězce přímo do něj.
- Použijte proměnné prostředí ve vaší webové aplikaci k uložení připojovacího řetězce. Konfigurace připojovacích řetězců najdete v dokumentu [nastavení konfigurace webové aplikace Azure](../../app-service/configure-common.md) .

Ve zde uvedených příkladech se připojovací řetězec předává přímo.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Vytvoření fronty

`QueueRestProxy`Objekt umožňuje vytvořit frontu pomocí `CreateQueue` metody. Při vytváření fronty můžete nastavit možnosti fronty, ale není to nutné. Tento příklad ukazuje, jak nastavit metadata ve frontě.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateQueueOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set queue metadata.
$createQueueOptions = new CreateQueueOptions();
$createQueueOptions->addMetaData("key1", "value1");
$createQueueOptions->addMetaData("key2", "value2");

try    {
    // Create queue.
    $queueClient->createQueue("myqueue", $createQueueOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Neměli byste spoléhat na rozlišování velkých a malých písmen pro klíče metadat. Všechny klíče se z této služby čtou malými písmeny.

## <a name="add-a-message-to-a-queue"></a>Přidat zprávu do fronty

Chcete-li přidat zprávu do fronty, použijte `QueueRestProxy->createMessage` . Metoda vezme název fronty, text zprávy a možnosti zprávy (které jsou volitelné).

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateMessageOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Create message.
    $queueClient->createMessage("myqueue", "Hello, World");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="peek-at-the-next-message"></a>Zobrazení náhledu další zprávy

Můžete prohlížet jednu nebo více zpráv na přední straně fronty, aniž byste je museli odebírat z fronty voláním `QueueRestProxy->peekMessages` . Ve výchozím nastavení `peekMessage` metoda vrací jedinou zprávu, ale tuto hodnotu lze změnit pomocí `PeekMessagesOptions->setNumberOfMessages` metody.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\PeekMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set peek message options.
$message_options = new PeekMessagesOptions();
$message_options->setNumberOfMessages(1); // Default value is 1.

try    {
    $peekMessagesResult = $queueClient->peekMessages("myqueue", $message_options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$messages = $peekMessagesResult->getQueueMessages();

// View messages.
$messageCount = count($messages);
if($messageCount <= 0){
    echo "There are no messages.<br />";
}
else{
    foreach($messages as $message)    {
        echo "Peeked message:<br />";
        echo "Message Id: ".$message->getMessageId()."<br />";
        echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
        echo "Message text: ".$message->getMessageText()."<br /><br />";
    }
}
```

## <a name="de-queue-the-next-message"></a>Vyřazení další zprávy z fronty

Kód ve dvou krocích odstraní zprávu z fronty. Nejprve zavoláte `QueueRestProxy->listMessages` , což způsobí, že zpráva nebude viditelná pro jakýkoli jiný kód, který je čten z fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund. (Pokud se zpráva v tomto časovém období neodstraní, bude se znovu zobrazovat ve frontě.) Chcete-li dokončit odebrání zprávy z fronty, je třeba zavolat `QueueRestProxy->deleteMessage` . Tento dvoustupňový proces odebrání zprávy zaručuje, že pokud váš kód nedokáže zpracovat zprávu z důvodu selhání hardwaru nebo softwaru, může jiná instance kódu získat stejnou zprávu a zkusit to znovu. Váš kód volá `deleteMessage` hned po zpracování zprávy.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

/* ---------------------
    Process message.
   --------------------- */

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Delete message.
    $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="change-the-contents-of-a-queued-message"></a>Změna obsahu zpráv zařazených ve frontě

Můžete změnit obsah zprávy na místě ve frontě voláním `QueueRestProxy->updateMessage` . Pokud zpráva představuje pracovní úlohu, mohli byste tuto funkci použít k aktualizaci stavu pracovních úloh. Následující kód aktualizuje zprávu fronty novými obsahem a nastaví časový limit viditelnosti na prodloužení dalších 60 sekund. Tím se uloží stav práce, která je přidružená ke zprávě, a klientovi poskytne další minutu, aby pokračoval v práci na této zprávě. Tento postup můžete použít ke sledování pracovních postupů ve frontě, aniž byste museli začít znovu od začátku, pokud krok zpracování selže kvůli selhání hardwaru nebo softwaru. Obvykle byste udržovali také hodnotu počtu opakování, a pokud by se pokus o zpracování zprávy opakoval více než *n* krát, odstranili byste ji. Je to ochrana proti tomu, aby zpráva při každém pokusu o zpracování nevyvolala chyby aplikace.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

// Define new message properties.
$new_message_text = "New message text.";
$new_visibility_timeout = 5; // Measured in seconds.

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Update message.
    $queueClient->updateMessage("myqueue",
                                $messageId,
                                $popReceipt,
                                $new_message_text,
                                $new_visibility_timeout);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="additional-options-for-dequeuing-messages"></a>Další možnosti pro vyřazování zpráv do fronty

Existují dva způsoby, jak můžete přizpůsobit načítání zpráv z fronty. Za prvé si můžete načíst dávku zpráv (až 32). Za druhé můžete nastavit delší nebo kratší časový limit viditelnosti, což umožňuje, aby váš kód měl více nebo méně času na úplné zpracování každé zprávy. Následující příklad kódu používá `getMessages` metodu k získání 16 zpráv v jednom volání. Pak každou zprávu zpracuje pomocí `for` smyčky. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\ListMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Set list message options.
$message_options = new ListMessagesOptions();
$message_options->setVisibilityTimeoutInSeconds(300);
$message_options->setNumberOfMessages(16);

// Get messages.
try{
    $listMessagesResult = $queueClient->listMessages("myqueue",
                                                     $message_options);
    $messages = $listMessagesResult->getQueueMessages();

    foreach($messages as $message){

        /* ---------------------
            Process message.
        --------------------- */

        // Get message Id and pop receipt.
        $messageId = $message->getMessageId();
        $popReceipt = $message->getPopReceipt();

        // Delete message.
        $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
    }
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="get-queue-length"></a>Získat délku fronty

Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. `QueueRestProxy->getQueueMetadata`Metoda načte metadata o frontě. Volání `getApproximateMessageCount` metody u vráceného objektu poskytuje počet zpráv ve frontě. Počet je přibližný pouze vzhledem k tomu, že je možné přidat nebo odebrat zprávy, když Queue Storage odpoví na vaši žádost.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Get queue metadata.
    $queue_metadata = $queueClient->getQueueMetadata("myqueue");
    $approx_msg_count = $queue_metadata->getApproximateMessageCount();
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

echo $approx_msg_count;
```

## <a name="delete-a-queue"></a>Odstranění fronty

Chcete-li odstranit frontu a všechny zprávy v ní, zavolejte `QueueRestProxy->deleteQueue` metodu.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Delete queue.
    $queueClient->deleteQueue("myqueue");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili se základy Azure Queue Storage, na následujících odkazech získáte informace o složitějších úlohách úložiště:

- Navštívit [Reference k rozhraní API pro Azure Storage klientskou knihovnu php](https://azure.github.io/azure-storage-php/)
- Podívejte se na [příklad rozšířené fronty](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php).

Další informace najdete v tématu [středisko pro vývojáře PHP](https://azure.microsoft.com/develop/php/).

---
title: Postup používání úložiště Queue z PHP | Dokumentace Microsoftu
description: Zjistěte, jak pomocí služby úložiště front Azure můžete vytvářet a odstraňovat fronty a vložit, získání a odstranění zprávy. Ukázky jsou napsané v jazyce PHP.
services: storage
author: roygara
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 01/11/2018
ms.author: rogarana
ms.component: queues
ms.openlocfilehash: 7ab755972760d5f262e00ca4aaf284c8d2847460
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39528278"
---
# <a name="how-to-use-queue-storage-from-php"></a>Používání úložiště Queue z PHP
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled
Tento průvodce vám ukáže, jak provádět běžné scénáře pomocí služby Azure Queue storage. Ukázky jsou zapsány pomocí třídy z [Klientská knihovna pro úložiště Azure pro jazyk PHP][download]. Pokryté scénáře zahrnují vložení, prohlížení, získávání a odstraňování front zpráv, jakož i vytváření a odstraňování front.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Vytvoření aplikace PHP
Jediným požadavkem pro vytvoření aplikace v jazyce PHP, který přistupuje k Azure Queue storage se odkazuje na třídy v [Klientská knihovna pro úložiště Azure pro jazyk PHP] [ download] z vašeho kódu. K vytvoření aplikace můžete použít jakékoli vývojové nástroje, včetně Poznámkového bloku.

V této příručce použijte funkce služby fronty úložiště, které může být volána v rámci aplikace v jazyce PHP místně nebo v kódu v rámci webové role Azure, role pracovního procesu nebo webu.

## <a name="get-the-azure-client-libraries"></a>Získání klientské knihovny Azure
### <a name="install-via-composer"></a>Instalace přes Composer
1. Vytvořte soubor s názvem **composer.json** v kořenovém adresáři vašeho projektu a přidejte do ní následující kód:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```
2. Stáhněte si **[composer.phar] [ composer-phar]** v kořenovém adresáři projektu.
3. Otevřete příkazový řádek a spusťte následující příkaz v kořenovém adresáři projektu
   
    ```
    php composer.phar install
    ```

Případně přejděte [klientské knihovny PHP pro Azure Storage] [ download] na Githubu klonování zdrojového kódu.

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurace aplikace pro přístup k úložišti front
K použití rozhraní API pro Azure Queue storage, budete muset:

1. Pomocí odkazu na soubor automatického zavaděče [require_once] příkazu.
2. Odkazovat na všechny třídy, které můžete použít.

Následující příklad ukazuje, jak zahrnout automatického zavaděče souboru a odkaz **QueueRestProxy** třídy.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

V následujících příkladech `require_once` příkaz je vždy zobrazen, ale jsou odkazovány pouze třídy, které jsou potřeba například pro spuštění.

## <a name="set-up-an-azure-storage-connection"></a>Nastavit připojení k Azure storage
Chcete-li vytvořit instanci klienta aplikace Azure Queue storage, musíte nejprve mít platný připojovací řetězec. Formát pro frontu služby připojovací řetězec vypadá takto.

Pro přístup k službě za provozu:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Pro přístup k emulátoru úložiště:

```php
UseDevelopmentStorage=true
```

K vytvoření klienta služby Azure Queue, budete muset použít **QueueRestProxy** třídy. Můžete použít některou z následujících postupů:

* Předejte řetězec připojení přímo k němu.
* Použití proměnných prostředí ve vaší webové aplikaci pro uložení připojovacího řetězce. Zobrazit [konfigurační nastavení aplikace Azure web](../../app-service/web-sites-configure.md) dokumentu Konfigurace připojovacích řetězců.
Ve zde uvedených příkladech se připojovací řetězec předává přímo.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Vytvoření fronty
A **QueueRestProxy** objektu umožňuje vytvoření fronty pomocí **createQueue** metody. Při vytváření fronty, můžete nastavit možnosti ve frontě, ale to tak není potřeba. (Následující příklad ukazuje, jak nastavit metadata do fronty.)

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
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Neměli byste tedy spoléhat na rozlišování velikosti písmen pro klíče metadat. Všechny klíče jsou pro čtení ze služby na malá písmena.
> 
> 

## <a name="add-a-message-to-a-queue"></a>Přidání zprávy do fronty
Chcete-li přidat zprávu do fronty, použijte **QueueRestProxy -> createMessage**. Tato metoda přebírá název fronty, text zprávy a možnosti zprávy (které jsou volitelné).

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
    $queueClient->createMessage("myqueue", "Hello World!");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="peek-at-the-next-message"></a>Zobrazení náhledu další zprávy
Můžete prohlížet zprávy (nebo zprávy) na začátku fronty bez odebrání z fronty pomocí volání **QueueRestProxy -> peekMessages**. Ve výchozím nastavení **peekMessage** metoda vrátí jednu zprávu, ale tuto hodnotu můžete změnit pomocí **PeekMessagesOptions -> setNumberOfMessages** metody.

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
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
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
Váš kód odebere zprávu z fronty ve dvou krocích. Nejprve volat **QueueRestProxy -> listMessages**, který představuje zprávu neviditelnou pro jakýkoli jiný kód, který čte z fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund. (Pokud zpráva není v tomto časovém období odstraněn, bude viditelné ve frontě znovu.) K dokončení odebrání zprávy z fronty, je třeba volat **QueueRestProxy -> deleteMessage**. Tento dvoukrokový proces odebrání zprávy zaručuje, že pokud váš kód se nepodaří zpracovat zprávu z důvodu selhání hardwaru nebo softwaru, jiná instance vašeho kódu můžete získat stejná zpráva a zkuste to znovu. Kód volá **deleteMessage** hned po zpracování zprávy.

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
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="change-the-contents-of-a-queued-message"></a>Změna obsahu zpráv zařazených ve frontě
Můžete změnit obsah zprávy v místě ve frontě voláním **QueueRestProxy -> updateMessage**. Pokud zpráva představuje pracovní úlohu, mohli byste tuto funkci použít k aktualizaci stavu pracovních úloh. Následující kód aktualizuje zprávy ve frontě o nový obsah a nastaví časový limit viditelnosti na jiné 60 sekund. To umožňuje ušetřit tím stav práce, který je spojen s touto zprávou a jeho klient získá další minutu chcete pokračovat v práci na zprávu. Tímto způsobem může sledovat vícekrokového pracovní postupy pro zprávy ve frontě, aniž by bylo nutné v případě, že krok zpracování z důvodu selhání hardwaru nebo softwaru selže, začít znovu od začátku. Obvykle byste udržovali také hodnotu počtu opakování, a pokud by se pokus o zpracování zprávy opakoval více než *n*krát, odstranili byste ji. Je to ochrana proti tomu, aby zpráva při každém pokusu o zpracování nevyvolala chyby aplikace.

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
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="additional-options-for-de-queuing-messages"></a>Další možností pro vyřazování zpráv z fronty
Existují dva způsoby, které lze přizpůsobit načtení zprávy z fronty. Za prvé si můžete načíst dávku zpráv (až 32). Za druhé můžete nastavit viditelnost delší nebo kratší časový limit, umožňuje váš kód více nebo méně času na úplné zpracování jednotlivých zpráv. Následující příklad kódu používá **getMessages** metodu k získání 16 zpráv v jednom volání. Pak se každá zpráva zpracuje pomocí **pro** smyčky. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut.

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
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="get-queue-length"></a>Získání délky fronty
Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. **QueueRestProxy -> getQueueMetadata** metoda požádá službu front vrátit metadata o frontě. Volání **getApproximateMessageCount** metoda pro vrácený objekt poskytuje počet počet zpráv ve frontě. Počet totiž pouze přibližná zprávy můžete přidat nebo odebrat po služba fronty jsou reaguje na váš požadavek.

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
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

echo $approx_msg_count;
```

## <a name="delete-a-queue"></a>Odstranění fronty
Pokud chcete odstranit frontu se všemi zprávami, které jsou v ní, zavolejte **QueueRestProxy -> deleteQueue** metody.

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
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili základy používání služby Azure Queue storage, použijte tyto odkazy na další informace o složitějších úlohách úložiště:

* Přejděte [Reference k rozhraní API pro klientské knihovny PHP pro Azure Storage](http://azure.github.io/azure-storage-php/)
* Zobrazit [Advanced fronty příklad](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php).

Další informace najdete v tématu taky [středisko pro vývojáře PHP](/develop/php/).

[download]: https://github.com/Azure/azure-storage-php
[require_once]: http://www.php.net/manual/en/function.require-once.php
[Azure Portal]: https://portal.azure.com
[composer-phar]: http://getcomposer.org/composer.phar


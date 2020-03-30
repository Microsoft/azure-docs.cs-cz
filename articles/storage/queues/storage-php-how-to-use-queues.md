---
title: Jak používat úložiště front z PHP – Azure Storage
description: Zjistěte, jak pomocí služby úložiště Fronty Azure vytvářet a odstraňovat fronty a vkládat, získat a odstraňovat zprávy. Ukázky jsou napsány v PHP.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2018
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 692c943e48c08771b5f1c60b66412270081cf0e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72302972"
---
# <a name="how-to-use-queue-storage-from-php"></a>Používání úložiště Queue z PHP

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

Tato příručka ukazuje, jak provádět běžné scénáře pomocí služby úložiště Fronty Azure. Ukázky se zapisují prostřednictvím tříd z [klientské knihovny úložiště Azure pro PHP][download]. Zahrnuté scénáře zahrnují vkládání, prohlížení, získávání a odstranění zpráv fronty, stejně jako vytváření a mazání front.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Vytvoření aplikace PHP

Jediným požadavkem pro vytvoření aplikace PHP, která přistupuje k úložišti Azure Queue, je odkazování na třídy v [klientské knihovně úložiště Azure pro PHP][download] z vašeho kódu. K vytvoření aplikace můžete použít jakékoli vývojové nástroje, včetně Poznámkového bloku.

V této příručce použijete funkce služby úložiště fronty, které lze volat v rámci aplikace PHP místně nebo v kódu spuštěném ve webové aplikaci v Azure.

## <a name="get-the-azure-client-libraries"></a>Získání klientských knihoven Azure

### <a name="install-via-composer"></a>Instalace přes Composer

1. Vytvořte soubor s názvem **composer.json** v kořenovém adresáři projektu a přidejte do něj následující kód:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```
2. Stáhněte si **[composer.phar][composer-phar]** v kořenovém adresáři projektu.
3. Otevření příkazového řádku a spuštění následujícího příkazu v kořenovém adresáři projektu
   
    ```
    php composer.phar install
    ```

Případně přejděte do [klientské knihovny Azure Storage PHP][download] na GitHubu a naklonujte zdrojový kód.

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurace aplikace pro přístup k úložišti fronty

Pokud chcete použít api pro úložiště Azure Queue, musíte:

1. Odkazna soubor automatického zavaděče pomocí [příkazu require_once.]
2. Odkaz na všechny třídy, které můžete použít.

Následující příklad ukazuje, jak zahrnout soubor automatického zavaděče a odkazovat na třídu **QueueRestProxy.**

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

V následujících příkladech `require_once` je příkaz zobrazen vždy, ale jsou odkazovány pouze třídy, které jsou nezbytné pro provedení příkladu.

## <a name="set-up-an-azure-storage-connection"></a>Nastavení připojení úložiště Azure

Chcete-li vytvořit instanci klienta úložiště fronty Azure, musíte mít nejprve platný připojovací řetězec. Formát připojovacího řetězce služby fronty je následující.

Pro přístup k živé službě:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Pro přístup k úložišti emulátoru:

```php
UseDevelopmentStorage=true
```

Chcete-li vytvořit klienta služby Azure Queue, musíte použít třídu **QueueRestProxy.** Můžete použít některou z následujících technik:

* Předajte mu připojovací řetězec přímo.
* K uložení připojovacího řetězce použijte proměnné prostředí ve webovém appu. Na webu [Azure web app nastavení dokumentu nastavení](../../app-service/configure-common.md) pro konfiguraci připojovacích řetězců.
Ve zde uvedených příkladech se připojovací řetězec předává přímo.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Vytvoření fronty

Objekt **QueueRestProxy** umožňuje vytvořit frontu pomocí metody **createQueue.** Při vytváření fronty můžete nastavit možnosti ve frontě, ale to není nutné. (Následující příklad ukazuje, jak nastavit metadata ve frontě.)

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
> U klíčů metadat byste neměli spoléhat na rozlišování malých a velkých písmen. Všechny klíče jsou čteny ze služby v malé písmena.
> 
> 

## <a name="add-a-message-to-a-queue"></a>Přidání zprávy do fronty

Chcete-li přidat zprávu do fronty, použijte **QueueRestProxy->createMessage**. Metoda přebírá název fronty, text zprávy a možnosti zprávy (které jsou volitelné).

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
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="peek-at-the-next-message"></a>Zobrazení náhledu další zprávy

Můžete nahlédnout na zprávu (nebo zprávy) na přední straně fronty bez odebrání z fronty voláním **QueueRestProxy->peekMessages**. Ve výchozím nastavení **peekMessage** metoda vrátí jednu zprávu, ale můžete změnit tuto hodnotu pomocí **PeekMessagesOptions->setNumberOfMessages** metoda.

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

Kód odebere zprávu z fronty ve dvou krocích. Nejprve zavoláte **QueueRestProxy->listMessages**, což způsobí, že zpráva neviditelné pro jakýkoli jiný kód, který je čtení z fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund. (Pokud zpráva není odstraněna v tomto časovém období, bude znovu viditelná ve frontě.) Chcete-li dokončit odebrání zprávy z fronty, musíte volat **QueueRestProxy->deleteMessage**. Tento dvoustupňový proces odebrání zprávy zajišťuje, že když váš kód nezpracuje zprávu z důvodu selhání hardwaru nebo softwaru, může jiná instance kódu získat stejnou zprávu a zkusit to znovu. Váš kód volá **deleteMessage** hned po zpracování zprávy.

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

Obsah zprávy na místě ve frontě můžete změnit voláním **Služby QueueRestProxy->updateMessage**. Pokud zpráva představuje pracovní úlohu, mohli byste tuto funkci použít k aktualizaci stavu pracovních úloh. Následující kód aktualizuje zprávu fronty s novým obsahem a nastaví časový limit viditelnosti prodloužit dalších 60 sekund. Tím se uloží stav práce, která je přidružena ke zprávě a poskytuje klientovi další minutu pokračovat v práci na zprávě. Tímto způsobem může sledovat vícekrokového pracovní postupy pro zprávy ve frontě, aniž by bylo nutné v případě, že krok zpracování z důvodu selhání hardwaru nebo softwaru selže, začít znovu od začátku. Obvykle byste udržovali také hodnotu počtu opakování, a pokud by se pokus o zpracování zprávy opakoval více než *n*krát, odstranili byste ji. Je to ochrana proti tomu, aby zpráva při každém pokusu o zpracování nevyvolala chyby aplikace.

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

## <a name="additional-options-for-de-queuing-messages"></a>Další možnosti zrušení řazení zpráv do fronty

Existují dva způsoby, jak můžete přizpůsobit načítání zpráv z fronty. Za prvé si můžete načíst dávku zpráv (až 32). Za druhé můžete nastavit delší nebo kratší časový limit viditelnosti, což umožňuje kódu více či méně času na úplné zpracování každé zprávy. Následující příklad kódu používá **getMessages** metoda získat 16 zpráv v jednom volání. Pak zpracuje každou zprávu pomocí **smyčky for.** Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut.

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

Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. **Metoda QueueRestProxy->getQueueMetadata** požádá službu fronty, aby vrátila metadata o frontě. Volání **getApproximateMessageCount** metoda na vrácený objekt poskytuje počet kolik zpráv jsou ve frontě. Počet je pouze přibližný, protože zprávy mohou být přidány nebo odebrány poté, co služba fronty odpoví na váš požadavek.

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

Chcete-li odstranit frontu a všechny zprávy v ní, zavolejte **metodu QueueRestProxy->deleteQueue.**

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

Teď, když jste se naučili základy úložiště Azure Queue, na následujících odkazech se dozvíte o složitějších úlohách úložiště:

* Navštivte [referenční rozhraní API pro klientskou knihovnu Azure Storage PHP](https://azure.github.io/azure-storage-php/)
* Viz [příklad rozšířené fronty](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php).

Další informace naleznete také v [CENTRU pro vývojáře PHP](https://azure.microsoft.com/develop/php/).

[download]: https://github.com/Azure/azure-storage-php
[require_once]: https://www.php.net/manual/en/function.require-once.php
[Azure Portal]: https://portal.azure.com
[composer-phar]: https://getcomposer.org/composer.phar

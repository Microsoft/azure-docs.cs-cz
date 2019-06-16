---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: b6e0e57881154f5885e9f518363eda3c5b1169a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66171271"
---
### <a name="install-via-composer"></a>Instalace přes Composer
1. Vytvořte soubor s názvem **composer.json** v kořenovém adresáři vašeho projektu a přidejte do ní následující kód:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage": "*"
      }
    }
    ```
2. Stáhněte si **[composer.phar] [ composer-phar]** v kořenovém adresáři projektu.
3. Otevřete příkazový řádek a spusťte následující příkaz v kořenovém adresáři projektu
   
    ```
    php composer.phar install
    ```

Případně přejděte [klientské knihovny PHP pro Azure Storage] [ php-sdk-github] na Githubu klonování zdrojového kódu.

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar

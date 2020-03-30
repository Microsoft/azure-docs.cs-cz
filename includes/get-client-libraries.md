---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: 5e3c4622131528fc2c40a1510aeea3092018d182
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77474154"
---
### <a name="install-via-composer"></a>Instalace přes Composer
1. Vytvořte soubor s názvem **composer.json** v kořenovém adresáři projektu a přidejte do něj následující kód:
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. Stáhněte si **[composer.phar][composer-phar]** v kořenovém adresáři projektu.
3. Otevření příkazového řádku a spuštění následujícího příkazu v kořenovém adresáři projektu
   
    ```
    php composer.phar install
    ```

Případně přejděte do [klientské knihovny Azure Storage PHP][php-sdk-github] na GitHubu a naklonujte zdrojový kód.

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: https://github.com/Azure/azure-sdk-for-php
[composer-phar]: http://getcomposer.org/composer.phar

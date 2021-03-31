---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: 9e9057073c8a661e2f3382333abc7ac2778c4ee3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "84680295"
---
### <a name="install-via-composer"></a>Instalace prostřednictvím skladatele
1. V kořenovém adresáři projektu vytvořte soubor s názvem **composer.js** a přidejte do něj následující kód:
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. Stáhněte si **[skladatel. phar][composer-phar]** do kořenového adresáře projektu.
3. Otevřete příkazový řádek a v kořenovém adresáři projektu spusťte následující příkaz.
   
    ```
    php composer.phar install
    ```

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: https://github.com/Azure/azure-sdk-for-php
[composer-phar]: http://getcomposer.org/composer.phar

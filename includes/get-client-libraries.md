---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: 3cd5f59a3dcf3afcd26d16874e7dc77ca0a7e844
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841689"
---
### <a name="install-via-composer"></a>Instalace prostřednictvím skladatele
1. V kořenovém adresáři projektu vytvořte soubor s názvem **skladatel. JSON** a přidejte do něj následující kód:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage": "*"
      }
    }
    ```
2. Stáhněte si **[skladatel. phar][composer-phar]** do kořenového adresáře projektu.
3. Otevřete příkazový řádek a v kořenovém adresáři projektu spusťte následující příkaz.
   
    ```
    php composer.phar install
    ```

Můžete také přejít na [knihovnu klienta PHP Azure Storage][php-sdk-github] na GitHubu a klonovat zdrojový kód.

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: https://github.com/Azure/azure-sdk-for-php
[composer-phar]: http://getcomposer.org/composer.phar

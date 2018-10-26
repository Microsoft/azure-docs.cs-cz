---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 46cfb27b8bde95990d13ec4bca4e96f25cfe9dc5
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133325"
---
Z důvodu průběžného vývoje nemusí v nástroji Android Studio nainstalovanou verzi sady SDK pro Android odpovídat verzi v kódu. Sady Android SDK odkazované v tomto kurzu je verze 26, nejnovější v době zápisu. Zvýšit číslo verze a zobrazí nové verze sady SDK, doporučujeme používat nejnovější dostupná verze.

Jsou dva příznaky zjistila se Neshoda verzí:

- Při sestavení nebo znovu sestavit projekt, se může zobrazit chybové zprávy Gradle jako `Gradle sync failed: Failed to find target with hash string 'android-XX'`.
- Standardní Android objekty v kódu, který se musí se překládat na základě `import` příkazy mohou být generování chybové zprávy.

Pokud některý z těchto se zobrazí, nemusí shodovat na verzi sady SDK pro Android nainstalována v nástroji Android Studio cíle sady SDK ze staženého projektu. K ověření verze, proveďte následující změny:

1. V nástroji Android Studio, klikněte na tlačítko **nástroje** > **Android** > **správce sady SDK**. Pokud jste nenainstalovali nejnovější verzi sady SDK platformy, klepněte na jeho instalaci. Poznamenejte si číslo verze.

2. Na **Project Exploreru** ve skupině **skriptů Gradle**, otevřete soubor **build.gradle (modul: aplikace)**. Ujistěte se, **compileSdkVersion** a **targetSdkVersion** jsou nastaveny na nainstalovanou nejnovější verzi sady SDK. `build.gradle` Může vypadat třeba takto:

    ```gradle
    android {
        compileSdkVersion 26
        defaultConfig {
            targetSdkVersion 26
        }
    }
    ```

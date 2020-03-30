---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 46cfb27b8bde95990d13ec4bca4e96f25cfe9dc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175522"
---
Z důvodu průběžného vývoje nemusí verze sady Android SDK nainstalované ve studiu Android odpovídat verzi v kódu. Android SDK odkazuje v tomto kurzu je verze 26, nejpozději v době psaní. Číslo verze se může zvýšit, jak se zobrazí nové verze sady SDK, a doporučujeme použít nejnovější dostupnou verzi.

Dva příznaky neshody verzí jsou:

- Při vytváření nebo znovu sestavu projektu, může `Gradle sync failed: Failed to find target with hash string 'android-XX'`se stát, že se zobrazí chybové zprávy Gradle jako .
- Standardní android objekty v kódu, který by měl vyřešit na `import` základě příkazů může být generování chybových zpráv.

Pokud se zobrazí některá z těchto verzí, verze sady Android SDK nainstalované ve studiu Android nemusí odpovídat cíli sady SDK staženého projektu. Chcete-li verzi ověřit, proveďte následující změny:

1. Ve Studiu pro Android klikněte na **Nástroje** > **Správce** > **sady Android .** Pokud jste nenainstalovali nejnovější verzi platformy SDK, potom ji klepnutím nainstalujte. Poznamenejte si číslo verze.

2. Na kartě **Průzkumník projektu** otevřete v části **Gradle Scripts**soubor **build.gradle (Modul: aplikace).** Ujistěte se, že **compileSdkVersion** a **targetSdkVersion** jsou nastaveny na nejnovější nainstalovanou verzi sady SDK. Může `build.gradle` vypadat takto:

    ```gradle
    android {
        compileSdkVersion 26
        defaultConfig {
            targetSdkVersion 26
        }
    }
    ```

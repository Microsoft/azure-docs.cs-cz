---
author: russell-cooks
ms.service: media-services
ms.topic: include
ms.date: 10/05/2020
ms.author: russellcooks
ms.openlocfilehash: 359c5f93516ea6f0561865bd86e4f51dedb4c3a5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358234"
---
1. V Visual Studio Code přejděte na src/Edge. Zobrazí se soubor. ENV, který jste vytvořili společně s několika soubory šablon nasazení.

    Šablona nasazení odkazuje na manifest nasazení pro hraniční zařízení s některými zástupnými hodnotami. Soubor. env obsahuje hodnoty pro tyto proměnné.
1. Pak přejděte do složky src/Cloud-to-Device-Console-App Tady se zobrazí appsettings.jsv souboru, který jste vytvořili společně s několika dalšími soubory:

    * C2D-Console-App. csproj: Toto je soubor projektu pro Visual Studio Code.
    * operations.js: Tento soubor obsahuje seznam různých operací, které má program spustit.
    * Program. cs: Tento ukázkový kód programu:

        * Načte nastavení aplikace.
        * Vyvolá živou analýzu videa v přímých metodách modulu IoT Edge k vytvoření topologie, vytvoření instance grafu a aktivaci grafu.
        * Pozastaví, abyste prozkoumali výstup grafu v okně **terminálu** a události odeslané do služby IoT Hub v okně **výstup** .
        * Deaktivuje instanci grafu, odstraní instanci grafu a odstraní topologii grafu.

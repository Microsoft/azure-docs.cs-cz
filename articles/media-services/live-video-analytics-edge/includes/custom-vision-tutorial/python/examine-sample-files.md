---
author: russell-cooks
ms.service: media-services
ms.topic: include
ms.date: 10/05/2020
ms.author: russellcooks
ms.openlocfilehash: 5dd823c618a0d49bef29c473e6c293762b859149
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "94358245"
---
1. V Visual Studio Code přejděte na src/Edge. Zobrazí se soubor. ENV, který jste vytvořili společně s několika soubory šablon nasazení.

    Šablona nasazení odkazuje na manifest nasazení pro hraniční zařízení s některými zástupnými hodnotami. Soubor. env obsahuje hodnoty pro tyto proměnné.
1. Pak přejděte do složky src/Cloud-to-Device-Console-App Tady se zobrazí appsettings.jsv souboru, který jste vytvořili společně s několika dalšími soubory:

   * operations.json – tento soubor zobrazí seznam různých operací, které by měl program spustit.
   * main.py – jedná se o vzorový programový kód, který provede následující:
    
        * Načte nastavení aplikace.
        * Vyvolá živou analýzu videa v přímých metodách modulu IoT Edge k vytvoření topologie, vytvoření instance grafu a aktivaci grafu.
        * Pozastaví, abyste prozkoumali výstup grafu v okně **terminálu** a události odeslané do služby IoT Hub v okně **výstup** .
        * Deaktivuje instanci grafu, odstraní instanci grafu a odstraní topologii grafu.

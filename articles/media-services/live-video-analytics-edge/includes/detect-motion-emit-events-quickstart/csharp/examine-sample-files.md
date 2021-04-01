---
ms.openlocfilehash: 1e3ba4b39baa045f35c232fa97c14bc78d8de5ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88690986"
---
1. V Visual Studio Code, přejít na *Src/Edge*. Zobrazí se soubor *. env* a některé soubory šablon nasazení.

    Šablona nasazení odkazuje na manifest nasazení pro hraniční zařízení, kde jsou proměnné použity pro některé vlastnosti. Soubor *. env* obsahuje hodnoty pro tyto proměnné.
1. Přejít do složky *Src/Cloud-to-Device-Console-App* Tady vidíte *appsettings.jsv* souboru a několik dalších souborů:

    * ***C2D-Console-App. csproj*** – soubor projektu pro Visual Studio Code.
    * ***operations.jsna*** seznam operací, které má program spustit.
    * ***Program. cs*** – ukázkový kód programu Tento kód:
    
      * Načte nastavení aplikace.
      * Vyvolá přímé metody, které jsou zpřístupněny v rámci analýzy živých videí v modulu IoT Edge. Pomocí modulu můžete analyzovat živé datové proudy videa vyvoláním jeho [přímých metod](../../../direct-methods.md).
      * Pozastaví, aby bylo možné kontrolovat výstup programu v okně **terminálu** a prozkoumávat události vygenerované modulem v okně **výstup** .
      * Vyvolá přímé metody pro vyčištění prostředků.   

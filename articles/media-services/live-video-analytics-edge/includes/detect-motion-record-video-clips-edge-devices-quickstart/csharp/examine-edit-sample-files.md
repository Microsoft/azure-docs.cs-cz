---
ms.openlocfilehash: 426c735dfd0d015cdc1a734edde9d336fb88cfbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97486735"
---
Jako součást požadavků pro tento rychlý Start jste stáhli vzorový kód do složky. Pomocí těchto kroků prověřte a upravte vzorový kód.

1. V Visual Studio Code, přejít na *Src/Edge*. Zobrazí se soubor *. env* a některé soubory šablon nasazení.

    Šablona nasazení odkazuje na manifest nasazení pro hraniční zařízení, kde jsou proměnné použity pro některé vlastnosti. Soubor *. env* obsahuje hodnoty pro tyto proměnné.
1. Přejít do složky *Src/Cloud-to-Device-Console-App* Tady vidíte *appsettings.jsv* souboru a několik dalších souborů:
    * ***C2D-Console-App. csproj*** – soubor projektu pro Visual Studio Code.
    * ***operations.jsv*** seznamu operací, které má program spustit.
    * ***Program. cs*** – ukázkový kód programu Tento kód:

        * Načte nastavení aplikace.
        * Vyvolá přímé metody, které zveřejňuje živá analýza videa v modulu IoT Edge. Pomocí modulu můžete analyzovat živé datové proudy videa vyvoláním jeho [přímých metod](../../../direct-methods.md). 
        * Pozastaví, aby bylo možné kontrolovat výstup programu v okně **terminálu** a prozkoumávat události vygenerované modulem v okně **výstup** .
        * Vyvolá přímé metody pro vyčištění prostředků.

1. Upravit *operations.jsv* souboru:
    * Změňte odkaz na topologii grafu:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/2.0/topology.json"`
    * V části `GraphInstanceSet` upravte název topologie grafu tak, aby odpovídala hodnotě z předchozího odkazu:
    
      `"topologyName" : "EVRToFilesOnMotionDetection"`
    * Upravte adresu URL RTSP tak, aby odkazovala na videosoubor:

        `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`
    * V části `GraphTopologyDelete` upravte název:

        `"name": "EVRToFilesOnMotionDetection"`

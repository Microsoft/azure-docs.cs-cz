---
ms.openlocfilehash: 2349939d4997ddc57d0c0c56a21eeec0357bf0ec
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91829023"
---
Pomocí těchto kroků spusťte vzorový kód:

1. V Visual Studio Code otevřete kartu **rozšíření** (nebo stiskněte klávesy CTRL + SHIFT + X) a vyhledejte IoT Hub Azure.
1. Klikněte pravým tlačítkem a vyberte **nastavení rozšíření**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Nastavení rozšíření":::
1. Vyhledejte a povolte možnost zobrazit podrobnou zprávu.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Nastavení rozšíření"
          }
        ]
      }
    }
    ```
     
    * Volání `GraphInstanceActivate` , které spustí instanci grafu a tok videa.
    * Druhé volání, které `GraphInstanceList` ukazuje, že instance grafu je ve stavu spuštěno.
1. Výstup v okně **terminálu** pozastaví `Press Enter to continue` . Ještě nevybírejte ENTER. Posuňte se nahoru, abyste viděli datové části odpovědi JSON pro přímé metody, které jste vyvolali.
1. Přepněte do okna **výstup** v Visual Studio Code. Zobrazí se zprávy, které modul analýza videa v IoT Edge posílá do služby IoT Hub. Následující část tohoto rychlého startu popisuje tyto zprávy.
1. Mediální graf bude nadále spouštět a tisknout výsledky. Simulátor RTSP zachovává smyčku zdrojového videa. Chcete-li zastavit graf médií, vraťte se do okna **terminálu** a vyberte Enter. 

    Další série volání vyčistí prostředky:

    * Volání `GraphInstanceDeactivate` deaktivuje instanci grafu.
    * Volání `GraphInstanceDelete` Odstraní instanci.
    * Volání pro `GraphTopologyDelete` odstranění topologie.
    * Konečné volání k `GraphTopologyList` ukáže, že seznam je prázdný.

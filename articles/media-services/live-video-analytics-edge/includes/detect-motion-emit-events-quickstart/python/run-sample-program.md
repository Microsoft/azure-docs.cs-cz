---
ms.openlocfilehash: 766dd13f58268c044435a22fb30c1de816d4d151
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97531835"
---
Pomocí těchto kroků spusťte vzorový kód:

1. V Visual Studio Code otevřete kartu **rozšíření** (nebo stiskněte klávesy CTRL + SHIFT + X) a vyhledejte IoT Hub Azure.
1. Klikněte pravým tlačítkem a vyberte **nastavení rozšíření**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Nastavení rozšíření":::
1. Vyhledejte a povolte možnost zobrazit podrobnou zprávu.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Zobrazit podrobnou zprávu":::
1. V Visual Studio Code v nástroji použijte *Src/Cloud-to-Device-Console-App/operations.js*.
1. V uzlu **GraphTopologySet** se ujistěte, že vidíte následující hodnotu:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/2.0/topology.json"`
1. V uzlech **GraphInstanceSet** a **GraphTopologyDelete**  zajistěte, aby hodnota `topologyName` odpovídala hodnotě `name` vlastnosti v topologii grafu:

    `"topologyName" : "MotionDetection"`
    
1. Spusťte ladicí relaci, a to tak, že vyberete klávesu F5. V okně **terminálu** se zobrazí některé zprávy.
1. *operations.jsv* souboru se spouští s voláními `GraphTopologyList` a `GraphInstanceList` . Pokud jste vyčistili prostředky po dokončení předchozích rychlých startů, pak tento proces vrátí prázdné seznamy a potom se pozastaví. Chcete-li pokračovat, vyberte klávesu ENTER.

    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
        "@apiVersion": "2.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
        "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
    
    V okně **terminálu** se zobrazí další sada volání přímých metod:
     * Volání `GraphTopologySet` , které používá předchozí `topologyUrl`
     * Volání `GraphInstanceSet` , které používá následující tělo:
         
    ```
    {
      "@apiVersion": "2.0",
      "name": "Sample-Graph",
      "properties": {
        "topologyName": "MotionDetection",
        "description": "Sample graph description",
        "parameters": [
          {
            "name": "rtspUrl",
            "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
          },
          {
            "name": "rtspUserName",
            "value": "testuser"
          },
          {
            "name": "rtspPassword",
            "value": "testpassword"
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

---
ms.openlocfilehash: 93c88f34e32e2057efd3eae25b1f41f58b948575
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88682185"
---
1. Spusťte ladicí relaci, a to tak, že vyberete klávesu F5. Okno **terminálu** vytiskne některé zprávy.
1. *operations.jsv* kódu volá přímé metody `GraphTopologyList` a `GraphInstanceList` . Pokud jste vyčistili prostředky po předchozích rychlých startech, pak tento proces vrátí prázdné seznamy a potom se pozastaví. Vyberte klávesu Enter.
    
    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
      "@apiVersion": "1.0"
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
  
  * Volání `GraphTopologySet` , které používá `topologyUrl` 
  * Volání `GraphInstanceSet` , které používá následující tělo:
  
  ```
  {
    "@apiVersion": "1.0",
    "name": "Sample-Graph",
    "properties": {
      "topologyName": "EVRToFilesOnMotionDetection",
      "description": "Sample graph description",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/lots_015.mkv"
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
1. Přepněte do okna **výstup** v Visual Studio Code. Zobrazí se zprávy o tom, že modul analýzy živých videí v IoT Edge odesílá do služby IoT Hub. Následující část tohoto rychlého startu popisuje tyto zprávy.
1. Mediální graf bude nadále spouštět a tisknout výsledky. Simulátor RTSP zachovává smyčku zdrojového videa. Chcete-li zastavit graf médií, vraťte se do okna **terminálu** a vyberte Enter. 

    Další série volání čistí prostředky:

    * Volání `GraphInstanceDeactivate` deaktivuje instanci grafu.
    * Volání `GraphInstanceDelete` Odstraní instanci.
    * Volání pro `GraphTopologyDelete` odstranění topologie.
    * Konečné volání `GraphTopologyList` ukazuje, že seznam je nyní prázdný.

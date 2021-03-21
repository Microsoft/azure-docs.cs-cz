---
title: Aktualizace příkazu z klientské aplikace
titleSuffix: Azure Cognitive Services
description: Naučte se aktualizovat příkaz z klientské aplikace.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: nitinme
ms.openlocfilehash: 08c674a7a7ec060a4273836064cb1c21e979e725
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97560283"
---
# <a name="update-a-command-from-a-client-app"></a>Aktualizace příkazu z klientské aplikace

V tomto článku se dozvíte, jak aktualizovat probíhající příkaz z klientské aplikace.

## <a name="prerequisites"></a>Předpoklady
> [!div class = "checklist"]
> * Už [vytvořená aplikace Vlastní příkazy](quickstart-custom-commands-application.md)

## <a name="update-the-state-of-a-command"></a>Aktualizace stavu příkazu

Pokud vaše klientská aplikace vyžaduje, abyste aktualizovali stav průběžného příkazu bez hlasového vstupu, můžete odeslat událost pro aktualizaci příkazu.

Pro ilustraci tohoto scénáře odešlete následující aktivitu události pro aktualizaci stavu probíhajícího příkazu ( `TurnOnOff` ): 

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "updatedCommand": {
      "name": "TurnOnOff",
      "updatedParameters": {
        "OnOff": "on"
      },
      "cancel": false
    },
    "updatedGlobalParameters": {},
    "processTurn": true
  }
}
```

Pojďme si projít klíčové atributy této aktivity:

| Atribut | Vysvětlení |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **textový** | Aktivita je typu `"event"` a musí být název události `"RemoteUpdate"` . |
| **value** | Atribut `"value"` obsahuje atributy požadované k aktualizaci aktuálního příkazu. |
| **updatedCommand** | Atribut `"updatedCommand"` obsahuje název příkazu. V rámci tohoto atributu `"updatedParameters"` je mapa s názvy parametrů a jejich aktualizovanými hodnotami. |
| **operaci** | Pokud je potřeba zrušit probíhající příkaz, nastavte atribut `"cancel"` na `true` . |
| **updatedGlobalParameters** | Atribut `"updatedGlobalParameters"` je mapa stejně jako `"updatedParameters"` , ale používá se pro globální parametry. |
| **processTurn** | Pokud je potřeba po odeslání aktivity znovu zpracovat, nastavte atribut `"processTurn"` na `true` . |

Tento scénář můžete vyzkoušet na portálu Custom Commands:

1. Otevřete aplikaci Custom Commands, kterou jste vytvořili dříve. 
1. Vyberte možnost **výuka** a potom **test**.
1. Odeslat `turn` .
1. Otevřete postranní panel a vyberte **Editor aktivit**.
1. Zadejte a odešlete `RemoteCommand` událost určenou v předchozí části.
    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky zobrazující událost vzdáleného příkazu](media/custom-commands/send-remote-command-activity.png)

Všimněte si, jak se hodnota parametru `"OnOff"` nastavila `"on"` prostřednictvím aktivity z klienta namísto hlasu nebo textu.

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>Aktualizace katalogu parametru pro příkaz

Když nakonfigurujete seznam platných možností pro parametr, hodnoty pro parametr jsou globálně definovány pro aplikaci. 

V našem příkladu `SubjectDevice` bude mít parametr pevný seznam podporovaných hodnot bez ohledu na konverzaci.

Pokud chcete přidat nové položky do katalogu parametrů na každou konverzaci, můžete odeslat následující aktivitu:

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "catalogUpdate": {
      "commandParameterCatalogs": {
        "TurnOnOff": [
          {
            "name": "SubjectDevice",
            "values": {
              "stereo": [
                "cd player"
              ]
            }
          }
        ]
      }
    },
    "processTurn": false
  }
}
```
S touto aktivitou jste přidali záznam pro `"stereo"` do katalogu parametru `"SubjectDevice"` v příkazu `"TurnOnOff"` .

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="Snímek obrazovky, který ukazuje aktualizaci katalogu.":::

Poznamenejte si několik věcí:
- Tuto aktivitu musíte poslat jenom jednou (v ideálním případě hned po spuštění připojení).
- Po odeslání této aktivity byste měli počkat, až se událost `ParameterCatalogsUpdated` pošle zpátky do klienta.

## <a name="add-more-context-from-the-client-application"></a>Přidat další kontext z klientské aplikace

Můžete nastavit další kontext z klientské aplikace na každou konverzaci, kterou můžete později použít ve své aplikaci pro vlastní příkazy. 

Zamyslete se například na situaci, kdy chcete poslat ID a název zařízení připojeného k aplikaci Custom Commands.

K otestování tohoto scénáře vytvoříme v aktuální aplikaci nový příkaz:
1. Vytvořte nový příkaz s názvem `GetDeviceInfo` .
1. Přidejte ukázkovou větu `get device info` .
1. **V pravidle** dokončení přidejte akci **Odeslat hlasovou odezvu** , která obsahuje atributy `clientContext` .
   ![Snímek obrazovky, který zobrazuje odpověď pro odeslání řeči pomocí kontextu](media/custom-commands/send-speech-response-context.png)
1. Uložte, školení a otestujte svoji aplikaci.
1. V okně testování odešlete aktivitu pro aktualizaci kontextu klienta.

    ```json
    {
       "type": "event",
       "name": "RemoteUpdate",
       "value": {
         "clientContext": {
           "deviceId": "12345",
           "deviceName": "My device"
         },
         "processTurn": false
       }
    }
    ```
1. Odešlete text `get device info` .
   ![Snímek obrazovky, který zobrazuje aktivitu pro odeslání kontextu klienta.](media/custom-commands/send-client-context-activity.png)

Všimněte si pár věcí:
- Tuto aktivitu musíte poslat jenom jednou (v ideálním případě hned po spuštění připojení).
- Pro můžete použít komplexní objekty `clientContext` .
- Můžete použít `clientContext` v odpovědích na řeč pro posílání aktivit a volání webových koncových bodů.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Aktualizace příkazu z webového koncového bodu](./how-to-custom-commands-update-command-from-web-endpoint.md)

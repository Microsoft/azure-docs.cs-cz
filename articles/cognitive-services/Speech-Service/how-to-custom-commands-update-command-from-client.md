---
title: Aktualizace příkazu z klientské aplikace
titleSuffix: Azure Cognitive Services
description: aktualizace příkazu z klientské aplikace
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 290f9ee9c23071ac56b1ff0c65ddc03decbc7344
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94571183"
---
# <a name="update-a-command-from-the-client"></a>Aktualizace příkazu z klienta

V tomto článku se dozvíte, jak aktualizovat průběžný příkaz z klientské aplikace.

## <a name="prerequisites"></a>Požadavky
> [!div class = "checklist"]
> * Už [vytvořená aplikace Vlastní příkazy](quickstart-custom-commands-application.md)

## <a name="update-the-state-of-a-command"></a>Aktualizace stavu příkazu

Pokud vaše klientská aplikace vyžaduje aktualizaci stavu průběžného příkazu bez hlasového vstupu, můžete odeslat událost pro aktualizaci příkazu.

K tomu, aby se tento scénář mohl aktualizovat, aby se aktualizoval stav probíhajícího příkazu (TurnOnOff), můžeme odeslat následující aktivitu události. 

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

Umožňuje zkontrolovat klíčové atributy této aktivity.

| Atribut | Vysvětlení |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **textový** | Aktivita je typu Event a název události musí být "RemoteUpdate". |
| **value** | Atribut "value" obsahuje atributy vyžadované k aktualizaci aktuálního příkazu. |
| **updatedCommand** | Atribut "updatedCommand" obsahuje název příkazu, "updatedParameters" je mapa s názvem parametrů a jejich aktualizovanými hodnotami. |
| **operaci** | Pokud je potřeba zrušit probíhající příkaz, nastavte atribut Cancel na hodnotu true. |
| **updatedGlobalParameters** | Atribut "updatedGlobalParameters" je také mapa, podobně jako "updatedParameters", ale používá se pro globální parametry. |
| **processTurn** | Pokud je potřeba po odeslání aktivity znovu zpracovat, nastavte atribut "processTurn" na hodnotu true. |

Tento scénář můžete otestovat na portálu Custom Commands.

1. Otevřete aplikaci Vlastní příkazy, kterou jste si vytvořili dříve. 
1. Klikněte na výuka a pak na test.
1. Poslat zprávu "Zapnout".
1. Otevřete postranní panel a klikněte na Editor aktivit.
1. Zadejte a odešlete událost RemoteCommand určenou v předchozí části.
    > [!div class="mx-imgBorder"]
    > ![Odeslat vzdálený příkaz](media/custom-commands/send-remote-command-activity.png)

Všimněte si, že hodnota parametru "(OnOff)" byla nastavena na "on" pomocí aktivity z klienta namísto hlasu nebo textu.

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>Aktualizace katalogu parametru pro příkaz

Když nakonfigurujete seznam platných možností pro parametr, hodnoty pro parametr jsou globálně definovány pro aplikaci. 

V našem příkladu bude mít parametr SubjectDevice pevný seznam podporovaných hodnot bez ohledu na konverzaci.

V případě, že potřebujete přidat nové položky do katalogu parametrů na každou konverzaci, můžete odeslat následující aktivitu.

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
S touto aktivitou jsme do katalogu parametru "SubjectDevice" v příkazu "TurnOnOff" přidali položku "stereo".

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="Aktualizovat katalog":::

Poznamenejte si několik věcí.
1. Tuto aktivitu musíte poslat jenom jednou (v ideálním případě hned po spuštění připojení).
1. Po odeslání této aktivity byste měli počkat na odeslání služby Event ParameterCatalogsUpdated zpět do klienta.

## <a name="add-additional-context-from-the-client-application"></a>Přidat další kontext z klientské aplikace

Můžete nastavit další kontext z klientské aplikace na každou konverzaci, kterou můžete později použít ve své aplikaci pro vlastní příkazy. 

Zamyslete se například na situaci, kdy chcete poslat ID a název zařízení připojeného k aplikaci Custom Commands.

K otestování tohoto scénáře vytvoříme nový příkaz v naší aktuální aplikaci.
1. Vytvořte nový příkaz s názvem GetDeviceInfo.
1. Přidejte ukázkovou větu s "získat informace o zařízení".
1. V pravidle dokončení dokončeno přidejte akci odeslat odezvu na řeč.
    > ![Odeslat odezvu řeči pomocí kontextu](media/custom-commands/send-speech-response-context.png)
1. Uložte a prohlaste svoji aplikaci.
1. Otestujte svoji aplikaci.
    > ![Aktivita odeslání kontextu klienta](media/custom-commands/send-client-context-activity.png)

Všimněte si pár věcí.
1. Tuto aktivitu musíte poslat jenom jednou (v ideálním případě hned po spuštění připojení).
1. Pro instance třídy ClientContext můžete použít složité objekty.
1. Instance třídy ClientContext můžete použít v odpovědích na řeč pro posílání aktivit a při volání webových koncových bodů.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Aktualizace příkazu z webového koncového bodu](./how-to-custom-commands-update-command-from-web-endpoint.md)

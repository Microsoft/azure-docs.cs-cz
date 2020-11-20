---
title: Aktualizace příkazu z webového koncového bodu
titleSuffix: Azure Cognitive Services
description: Naučte se aktualizovat stav příkazu pomocí volání webového koncového bodu.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: a24f1337a68f38db273688e9a91c65ac2f4736b4
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963602"
---
# <a name="update-a-command-from-a-web-endpoint"></a>Aktualizace příkazu z webového koncového bodu

Pokud vaše klientská aplikace vyžaduje aktualizaci stavu průběžného příkazu bez hlasového vstupu, můžete použít volání webovému koncovému bodu pro aktualizaci příkazu.

V tomto článku se dozvíte, jak aktualizovat probíhající příkaz z webového koncového bodu.

## <a name="prerequisites"></a>Požadavky
> [!div class = "checklist"]
> * Už [vytvořená aplikace Vlastní příkazy](quickstart-custom-commands-application.md)

## <a name="create-an-azure-function"></a>Vytvořit funkci Azure 

V tomto příkladu budete potřebovat [funkci Azure](https://docs.microsoft.com/azure/azure-functions/) AKTIVOVANou protokolem HTTP, která podporuje následující vstup (nebo podmnožinu tohoto vstupu):

```JSON
{
  "conversationId": "SomeConversationId",
  "currentCommand": {
    "name": "SomeCommandName",
    "parameters": {
      "SomeParameterName": "SomeParameterValue",
      "SomeOtherParameterName": "SomeOtherParameterValue"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "SomeGlobalParameterValue",
      "SomeOtherGlobalParameterName": "SomeOtherGlobalParameterValue"
  }
}
```

Pojďme se podívat na klíčové atributy tohoto vstupu:

| Atribut | Vysvětlení |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | Jedinečný identifikátor konverzace Všimněte si, že toto ID může být vygenerováno z klientské aplikace. |
| **currentCommand** | Příkaz, který je aktuálně aktivní v konverzaci. |
| **Jméno** | Název příkazu `parameters`Atribut je mapa s aktuálními hodnotami parametrů. |
| **currentGlobalParameters** | Mapa, jako `parameters` je, která se používá pro globální parametry. |

Výstup funkce Azure musí podporovat následující formát:

```JSON
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

Tento formát můžete rozpoznat, protože je stejný jako ten, který jste použili při [aktualizaci příkazu z klienta](./how-to-custom-commands-update-command-from-client.md). 

Teď vytvořte funkci Azure na základě Node.js. Kopírovat/vložit tento kód:

```nodejs
module.exports = async function (context, req) {
    context.log(req.body);
    context.res = {
        body: {
            updatedCommand: {
                name: "IncrementCounter",
                updatedParameters: {
                    Counter: req.body.currentCommand.parameters.Counter + 1
                }
            }
        }
    };
}
```

Při volání této funkce Azure Functions z vlastních příkazů odešlete aktuální hodnoty konverzace. Vrátíte parametry, které chcete aktualizovat, nebo pokud chcete zrušit aktuální příkaz.

## <a name="update-the-existing-custom-commands-app"></a>Aktualizovat existující aplikaci Custom Commands

Pojďme službu Azure Functions připojit k existující aplikaci Custom Commands:

1. Přidejte nový příkaz s názvem `IncrementCounter` .
1. Přidejte pouze jednu ukázkovou větu s hodnotou `increment` .
1. Přidejte nový parametr s názvem `Counter` (stejný název, který je zadaný ve funkci Azure) typu `Number` s výchozí hodnotou `0` .
1. Přidejte nový webový koncový bod `IncrementEndpoint` s názvem URL funkce Azure s nastavenou funkcí **vzdálené aktualizace** na **povoleno**.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="Snímek obrazovky, který ukazuje nastavení koncového bodu webu se vzdálenými aktualizacemi.":::
1. Vytvořte nové pravidlo interakce nazvané **IncrementRule** a přidejte akci pro **webový koncový bod volání** .
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="Snímek obrazovky, který ukazuje vytvoření pravidla interakce.":::
1. V části Konfigurace akce vyberte `IncrementEndpoint` . **Po úspěšné** konfiguraci můžete **Odeslat odpověď na řeč** s hodnotou `Counter` a nakonfigurovat při **selhání** chybovou zprávu.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="Snímek obrazovky, který ukazuje nastavení čítače přírůstku pro volání webového koncového bodu.":::
1. Nastavte stav po spuštění pravidla, aby se **čekalo na vstup uživatele**.

## <a name="test-it"></a>Testování

1. Uložte a prohlaste svoji aplikaci.
1. Vyberte **Test**.
1. Pošle se `increment` několik časů (což je ukázková věta `IncrementCounter` příkazu).
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="Snímek obrazovky, který ukazuje příklad čítače přírůstku.":::

Všimněte si, jak funkce Azure zvýší hodnotu `Counter` parametru u každého tahu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Povolení procesu CI/CD pro aplikaci Vlastní příkazy](./how-to-custom-commands-deploy-cicd.md)

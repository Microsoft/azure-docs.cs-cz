---
title: Aktualizace příkazu z webového koncového bodu
titleSuffix: Azure Cognitive Services
description: aktualizace příkazu z webového koncového bodu
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 4432843ac93002bc92068db191706352234d76e6
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94571197"
---
# <a name="update-a-command-from-a-web-endpoint"></a>Aktualizace příkazu z webového koncového bodu

Pokud vaše klientská aplikace vyžaduje aktualizaci stavu průběžného příkazu bez hlasového vstupu, můžete použít volání webovému koncovému bodu pro aktualizaci příkazu.

V tomto článku se dozvíte, jak aktualizovat probíhající příkaz z webového koncového bodu.

## <a name="prerequisites"></a>Požadavky
> [!div class = "checklist"]
> * Už [vytvořená aplikace Vlastní příkazy](quickstart-custom-commands-application.md)

## <a name="create-an-azure-function"></a>Vytvoření funkce Azure Function 

V tomto příkladu budeme potřebovat HTTP-Triggered [funkci Azure](https://docs.microsoft.com/azure/azure-functions/) , která podporuje následující vstup (nebo podmnožinu tohoto vstupu).

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

Umožňuje zkontrolovat klíčové atributy tohoto vstupu.

| Atribut | Vysvětlení |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | "conversationId" je jedinečný identifikátor konverzace, Všimněte si, že toto ID může být vygenerováno z klientské aplikace. |
| **currentCommand** | "currentCommand" je příkaz aktuálně aktivní v konverzaci. |
| **Jméno** | "Name" je název příkazu a "Parameters" je mapa s aktuálními hodnotami parametrů. |
| **currentGlobalParameters** | "currentGlobalParameters" je také mapa, například "Parameters", ale používá se pro globální parametry. |

Výstup funkce Azure musí podporovat následující formát.

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

Tento formát můžete rozpoznat, protože je stejný jako ten, který se používá při [aktualizaci příkazu z klienta](./how-to-custom-commands-update-command-from-client.md). 

Teď vytvořte funkci Azure založenou na NodeJS a zkopírujte a vložte tento kód.

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

Když tuto funkci Azure zavoláme z vlastních příkazů, pošleme aktuální hodnoty konverzace a vrátíme parametry, které chceme aktualizovat, nebo pokud chceme zrušit aktuální příkaz.

## <a name="update-the-existing-custom-commands-app"></a>Aktualizovat existující aplikaci Custom Commands

Teď se podíváme na funkci Azure s existující aplikací Custom Commands.

1. Přidejte nový příkaz s názvem IncrementCounter.
1. Přidejte pouze jednu ukázkovou větu s hodnotou "přírůstek".
1. Přidejte nový parametr s názvem Counter (stejný název, který je zadaný ve funkci Azure výše) typu číslo s výchozí hodnotou 0.
1. Přidejte nový webový koncový bod s názvem IncrementEndpoint s adresou URL vaší funkce Azure a s povolenými vzdálenými aktualizacemi.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="Nastavit webový koncový bod se vzdálenými aktualizacemi":::
1. Vytvořte nové pravidlo interakce nazvané "IncrementRule" a přidejte akci pro webový koncový bod volání.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="Zvýšit pravidlo":::
1. V části Konfigurace akce vyberte IncrementEndpoint, nakonfigurovat po úspěšném odeslání odezvy řeči s hodnotou Counter a při selhání s chybovou zprávou.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="Nastavit přírůstek koncový bod volání čítače":::
1. Nastavte stav po spuštění pravidla, aby se čekalo na vstup uživatele.

## <a name="test-it"></a>Testování

1. Uložení a výuka aplikace
1. Kliknout na test
1. Poslat několikrát "přírůstek" (což je ukázková věta příkazu IncrementCounter)
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="Příklad přírůstku čítače":::

Všimněte si, jak se hodnota parametru čítače zvyšuje při každém zapnutí funkce Azure Functions.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Povolení procesu CI/CD pro aplikaci Vlastní příkazy](./how-to-custom-commands-deploy-cicd.md)
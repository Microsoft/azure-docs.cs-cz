---
title: Ověření instance kontejneru Extrakce klíčových frází
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak ověřit instanci kontejneru Extrakce klíčových frází.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 1e2001c1f4cb2da195a3dcd0ca8fe198de8dd264
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968559"
---
### <a name="verify-the-key-phrase-extraction-container-instance"></a>Ověření instance kontejneru Extrakce klíčových frází

1. Vyberte kartu **Přehled** a zkopírujte IP adresu.
1. Otevřete novou kartu prohlížeče a zadejte IP adresu. Zadejte `http://<IP-address>:5000 (http://55.55.55.55:5000`například). Zobrazí se Domovská stránka kontejneru, která vám umožní zjistit, že je kontejner spuštěný.

    ![Zobrazte domovskou stránku kontejneru a ověřte, že je spuštěný.](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Kliknutím na odkaz **Popis rozhraní API služby** přejdete na stránku Swagger kontejneru.

1. Zvolte kterékoli rozhraní API pro **post** a vyberte **vyzkoušet**. Zobrazí se parametry, které obsahují tento příklad vstupu:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello world"
        },
        {
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        },
        {
          "id": "4",
          "text": ":) :( :D"
        }
      ]
    }
    ```

1. Nahraďte vstup následujícím obsahem JSON:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. Nastavte **showStats** na `true`.

1. Výběrem příkazu **Execute** určete mínění textu.

    Model, který je zabalen v kontejneru, vygeneruje skóre, které je v rozsahu od 0 do 1, kde 0 je záporné a 1 je kladné.

    Vrácená odpověď JSON obsahuje mínění pro aktualizovaný textový vstup:

    ```json
    {
      "documents": [
        {
          "id": "7",
          "keyPhrases": [
            "Great people",
            "great sessions",
            "KubeCon Conference",
            "Barcelona",
            "best conferences"
          ],
          "statistics": {
            "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

Nyní můžeme sladit dokument `id` dat JSON datové části odpovědi s původním dokumentem `id`datové části požadavku. Výsledný dokument `keyPhrases` obsahuje pole, které obsahuje seznam klíčových frází, které byly extrahovány z odpovídajícího vstupního dokumentu. Kromě toho jsou k dispozici různé statistiky `characterCount` , `transactionCount` například a pro každý výsledný dokument.
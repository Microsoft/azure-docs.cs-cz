---
title: Přidat opravu s jedním krokem ve službě Custom Commands Preview – služba Speech
titleSuffix: Azure Cognitive Services
description: Naučte se, jak do aplikace ve verzi Preview přidat krok s jedním krokem pro příkaz.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 05f63ba4e70f649df33905f1e92fb1fab866a86c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310423"
---
# <a name="add-a-one-step-correction-to-a-custom-command-in-a-custom-commands-preview-application"></a>Přidání opravy s jedním krokem k vlastnímu příkazu v aplikaci ve verzi Preview pro vlastní příkazy

V tomto článku se dozvíte, jak do příkazu v aplikaci ve verzi Preview přidat potvrzení s jedním krokem.

K aktualizaci příkazu, který se právě dokončil, se používá krokování s jedním krokem. Když do příkazu alarm přidáte opravu s jedním krokem, můžete si změnit svůj názor a aktualizovat čas alarmu. Například:

- Vstup: nastavit alarm pro zítřejší čas v poledne
- Výstup: OK, sada alarmů pro 2020-05-02 12:00:00
- Vstup: Ne, zítra ve 13:00
- Výstup: OK

> [!NOTE]
> Ve scénáři reálného světa klient provede akci v důsledku dokončení příkazu. V tomto článku se předpokládá, že máte mechanizmus aktualizace alarmu ve vaší back-endové aplikaci.

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v následujících článcích:
> [!div class="checklist"]

> * [Rychlý Start: Vytvoření vlastní příkazy Preview aplikace](./quickstart-custom-speech-commands-create-new.md)
> * [Rychlý Start: Vytvoření vlastní příkazy aplikace ve verzi Preview s parametry](./quickstart-custom-speech-commands-create-parameters.md)
> * [Postupy: Přidání potvrzení do příkazu v aplikaci ve verzi Preview pro vlastní příkazy](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-interaction-rules-for-one-step-correction"></a>Přidat pravidla interakce pro opravy na jednom kroku

Chcete-li předvést opravu s jedním krokem, rozšíříte příkaz **SetAlarm** , který jste vytvořili v tématu [Postupy: Přidání potvrzení do příkazu ve verzi Preview vlastních příkazů](./how-to-custom-speech-commands-confirmations.md).

1. Přidejte pravidlo interakce k aktualizaci příkazu **SetAlarm** .

    Toto pravidlo vyzve uživatele k potvrzení data a času alarmu a očekává u dalšího tahu potvrzení (ano/ne).

   | Nastavení               | Navrhovaná hodnota                                                  | Popis                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | **Název pravidla**             | **Aktualizovat předchozí alarm**                                            | Název popisující účel pravidla          |
   | **Podmínky**            | **Předchozí příkaz se musí aktualizovat & požadovaný parametr-> data a času.**                | Podmínky, které určují, kdy se pravidlo dá spustit    |   
   | **Akce**               | **Odeslat odezvu na řeč – > jednoduchý editor – > aktualizace předchozího alarmu na {DateTime}**      | Akce, která se má provést, když jsou podmínky pravidla pravdivé |
   | **Stav po spuštění** | **Příkaz dokončen**        | Stav uživatele po zapnutí                   |

1. V podokně vyberte pravidlo interakce, které jste právě vytvořili. V levém horním rohu podokna vyberte tlačítko se třemi tečkami (**...**). Pak pomocí šipky **nahoru** přesunete pravidlo na začátek seznamu **pravidel interakce** .
   > [!div class="mx-imgBorder"]
   > ![Přidat ověření rozsahu](media/custom-speech-commands/one-step-correction-rules.png)

    > [!NOTE]
    > V reálné aplikaci můžete použít část **Akce** k odeslání zpětné aktivity klientovi nebo volání koncového bodu http a aktualizace alarmu v systému.

## <a name="try-it-out"></a>Vyzkoušejte si to.

1. Vyberte **vlak**.

1. Po dokončení školení vyberte **test**a potom zkuste tyto interakce:

   - Vstup: nastavit alarm pro zítřejší čas v poledne
   - Výstup: jste si jisti, že chcete nastavit alarm pro 2020-05-02 12:00:00
   - Vstup: Ano
   - Výstup: OK, sada alarmů pro 2020-05-02 12:00:00
   - Vstup: Ne, zítra v 2pm
   - Výstup: aktualizace předchozího alarmu na 2020-05-02 14:00:00

---
title: Jak se připojit dialogová okna v modelu konverzace Learner – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Zjistěte, jak protokolovat dialogová okna v modelu Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 74ee04fb3d4044573a98535a9bdc26d5c593a222
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796730"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-model"></a>Jak se připojit dialogová okna v modelu Learner konverzace

Tento kurz ukazuje, jak dialogová okna protokolu se použijí k lepšímu trénování modelů konverzace Learner z nahraných interakcí s uživateli reálného světa.

## <a name="video"></a>Video

[![Log dialogová okna kurz ve verzi Preview](https://aka.ms/cl_Tutorial_v3_LogDialogs_Preview)](https://aka.ms/cl_Tutorial_v3_LogDialogs)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna obecné kurz bot

    npm run tutorial-general

a počasí model vytvořený v předchozích kurzech.

## <a name="details"></a>Podrobnosti
Dialogová okna protokolu jsou zaznamenané protokoly svého robota interakce s koncovým uživatelům. Podle využití, tyto dialogy protokolu můžete vyřešit popisky entity a výběr akce pro zlepšení výkonu modelu a celkový výkon systému.

## <a name="steps"></a>Kroky

Ve webovém uživatelském rozhraní klikněte na "Import kurzy" a vyberte model s názvem "Kurzu-11-LogDialogs".

Tento Model obsahuje jednu entitu s názvem "city" a akce určeny pro reakce na dotazy o počasí v tomto městě. Dialogová okna dvou trénování byly použity pro trénování modelu, tak, aby byly poněkud nižší očekávaný výkon. Model by mohly zlepšit s další školicí a vystavení interakce uživatele reálného světa.

### <a name="create-a-new-conversation"></a>Vytvořit novou konverzaci

1. Na levém panelu klikněte na tlačítko "Protokolu dialogů" a potom na tlačítko "Dialogové okno Nový protokol".
2. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Předpověď počasí Austin"
3. Klikněte na tlačítko "Testování Hotovo".
4. Kliknutím na dialogové okno protokol "Předpověď počasí Austin" ze seznamu.
5. Klikněte na tlačítko "Předpověď počasí Austin" utterance panelu konverzace.
6. Klikněte na tlačítko "Austin" a potom klikněte na "city" v seznamu entit.
7. Klikněte na tlačítko "Odeslat změny".
    - Tuto změnu v hodnotě Entity způsobují podřízené změny konverzace, protože máme hodnoty nové entity v paměti. Pravděpodobně pozdějších akcích staly neplatnými zejména těch, které jsou zahrnující entity "city".
8. Klikněte "Které city"? utterance panelu konverzace.
9. Vyberte odpověď, "o počasí v Austin je pravděpodobně hezky."
10. Klikněte na tlačítko "Uložit jako trénování dialogové okno".
    - Školení je okamžitě spuštěna.

Poslední OneNote. Podle potřeb firmy konverzace funkce protokolování můžete vypnout tak, že přejdete na nastavení a zrušením zaškrtnutí políčka "protokolu konverzace."

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zpětné volání zjišťování entit](./12-entity-detection-callback.md)

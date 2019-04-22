---
title: Přidání zpoždění v logic apps | Dokumentace Microsoftu
description: Přehled zpoždění a zpoždění – až do akce a jak pomocí Azure logic app.
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: 915f48bf-3bd8-4656-be73-91a941d0afcd
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.openlocfilehash: 15e581454b60319ab734f2fa5faf0d90e0a7c8bf
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "58893720"
---
# <a name="get-started-with-the-delay-and-delay-until-actions"></a>Začínáme s zpoždění a zpoždění – dokud akce
S použitím zpoždění a "zpoždění – dokud" akce, můžete dokončovat scénáře pracovního postupu.

Můžete například provést následující věci:

* Počkejte, dokud jeden den v týdnu pro odeslání e-mailu aktualizace stavu.
* Zpoždění pracovní postup, dokud volání protokolu HTTP má čas dokončení a obnovení a načítání výsledku.

Abyste mohli začít používat zpoždění akce v aplikaci logiky, najdete v článku [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-delay-actions"></a>Použijte zpoždění akce

Akce je operace, která provádí pracovní postup, který je definován v aplikaci logiky. 
[Další informace o akcích](../connectors/apis-list.md).

Tady je příklad pořadí použití kroku zpoždění v aplikaci logiky:

1. Po přidání aktivační události, klikněte na tlačítko **nový krok** přidání akce.
2. Vyhledejte **zpoždění** zobrazíte zpoždění akce. V tomto příkladu vybereme **zpoždění**.
   
    ![Zpoždění akce](./media/connectors-native-delay/using-action-1.png)
3. Dokončete všechny akce vlastností konfigurace zpoždění.
   
    ![Zpoždění config](./media/connectors-native-delay/using-action-2.png)
4. Klikněte na tlačítko **Uložit** k publikování a aktivovat aplikaci logiky.

## <a name="action-details"></a>Detaily akce
Trigger opakování má následující vlastnosti, které lze nastavit.

### <a name="delay-action"></a>Zpoždění akce
Tato akce zpoždění spuštění pro určitý časový interval.
A * znamená, že je povinné pole.

| Zobrazované jméno | Název vlastnosti | Popis |
| --- | --- | --- |
| Počet * |count |Počet časových jednotek zpoždění |
| Jednotka * |jednotka |Jednotka času: `Second`, `Minute`, `Hour`, nebo `Day` |

<br>

### <a name="delay-until-action"></a>Delay – dokud akce
Tato akce zpoždění spuštění až do zadaného data a času.
A * znamená, že je povinné pole.

| Zobrazované jméno | Název vlastnosti | Popis |
| --- | --- | --- |
| Rok * |časové razítko |Období od začátku roku zpoždění do (GMT) |
| Měsíc * |časové razítko |Měsíc na zpoždění do (GMT) |
| Den * |časové razítko |Během dne zpoždění do (GMT) |

<br>

## <a name="next-steps"></a>Další postup
Nyní, vyzkoušejte si platformu a [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Další dostupné konektory v logic apps můžete prozkoumat podle našich [rozhraní API seznamu](apis-list.md).


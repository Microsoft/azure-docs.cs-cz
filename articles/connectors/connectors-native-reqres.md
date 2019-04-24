---
title: Použití akce odezvy a odpovědi | Dokumentace Microsoftu
description: Přehled požadavků a odpovědí aktivační události a akce v Azure logic app
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: 566924a4-0988-4d86-9ecd-ad22507858c0
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.openlocfilehash: 0f6ee8729cbed9cb8baf3668f7b1a332bc5eddc1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60538099"
---
# <a name="get-started-with-the-request-and-response-components"></a>Začínáme s komponentami žádostí a odpovědí
Součástí požadavků a odpovědí v aplikaci logiky můžete v reálném čase reagovat na události.

Můžete například provést následující věci:

* Odpověď na požadavek HTTP s daty z místní databáze pomocí aplikace logiky.
* Aktivujte aplikaci logiky z události externí webhooku.
* Volejte aplikaci logiky s akcí žádostí a odpovědí z v rámci jiné aplikace logiky.

Abyste mohli začít používat akce odezvy a odpovědi v aplikaci logiky, najdete v článku [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-http-request-trigger"></a>Pomocí aktivační událost požadavek HTTP
Trigger je událost, která umožňuje spustit pracovní postup, který je definován v aplikaci logiky. 
[Další informace o aktivačních událostech](../connectors/apis-list.md).

Tady je příklad posloupnosti o tom, jak nastavit požadavek HTTP v návrháři aplikace logiky.

1. Přidat aktivační událost **požadavek – přijetí požadavku HTTP při** ve vaší aplikaci logiky. Volitelně můžete zadat schématu JSON (pomocí některého nástroje, například [JSONSchema.net](https://jsonschema.net)) pro tělo požadavku. To umožňuje návrháře ke generování tokenů pro vlastnosti v požadavku HTTP.
2. Potom přidejte další akci, takže můžete uložit aplikaci logiky.
3. Po uložení aplikace logiky, můžete získat adresu URL požadavku HTTP z karty požadavku.
4. Metody POST protokolu HTTP (můžete použít nástroje, jako je [Postman](https://www.getpostman.com/)) na adresu URL aktivuje aplikace logiky.

> [!NOTE]
> Pokud nebudete definovat akce odpovědi, `202 ACCEPTED` okamžitě vrátí odpověď na volajícího. Akce odpovědi můžete použít k přizpůsobení odpovědí.
> 
> 

![Odpověď triggeru](./media/connectors-native-reqres/using-trigger.png)

## <a name="use-the-http-response-action"></a>Použití akce odpovědi HTTP
Akce odpovědi protokolu HTTP je platný pouze při použití v pracovním postupu, který se aktivuje požadavkem HTTP. Pokud nebudete definovat akce odpovědi, `202 ACCEPTED` okamžitě vrátí odpověď na volajícího.  Akce odpovědi můžete přidat na libovolný krok v pracovním postupu. Aplikace logiky pouze udržuje příchozího požadavku otevřít pro jednu minutu pro odpověď.  Po jedné minutě, pokud byla odeslána žádná odpověď z pracovního postupu (a existuje akce odpovědi v definici) `504 GATEWAY TIMEOUT` je vrátit zpět volajícímu.

Tady je postup pro přidání akce odpovědi HTTP:

1. Vyberte **nový krok** tlačítko.
2. Zvolte **přidat akci**.
3. Zadejte do vyhledávacího pole Akce **odpovědi** seznam akce odpovědi.
   
    ![Výběr akce odpovědi](./media/connectors-native-reqres/using-action-1.png)
4. Přidejte všechny parametry, které jsou požadovány pro zprávy s odpovědí HTTP.
   
    ![Dokončení akce odpovědi](./media/connectors-native-reqres/using-action-2.png)
5. Klikněte levém horním rohu panelu nástrojů uložte a vaše aplikace logiky se jak uložit a publikovat (aktivovat).

## <a name="request-trigger"></a>Aktivační událost požadavek
Tady jsou uvedené podrobnosti pro aktivační událost, která podporuje tento konektor. Existuje aktivační událost jedné žádosti.

| Trigger | Popis |
| --- | --- |
| Žádost |Vyvolá se při přijetí požadavku HTTP |

## <a name="response-action"></a>Akce odpovědi
Tady jsou uvedené podrobnosti pro akci, která podporuje tento konektor. Se jednu odpověď akce, který jde použít jenom při je přiložena trigger požadavku.

| Akce | Popis |
| --- | --- |
| Odpověď |Vrátí odpověď korelační požadavku HTTP |

### <a name="trigger-and-action-details"></a>Podrobnosti o aktivační události a akce
Následující tabulky popisují vstupní pole pro aktivační událost a akce a odpovídající výstupní informace.

#### <a name="request-trigger"></a>Aktivační událost požadavek
Níže je vstupní pole pro aktivační událost z příchozího požadavku HTTP.

| Zobrazované jméno | Název vlastnosti | Popis |
| --- | --- | --- |
| Schéma JSON |schema |Schéma JSON textu požadavku HTTP |

<br>

**Podrobnosti výstupu**

Toto jsou podrobnosti výstupu pro daný požadavek.

| Název vlastnosti | Typ dat | Popis |
| --- | --- | --- |
| Hlavičky |objekt |Hlavičky požadavku |
| Tělo |objekt |Objekt žádosti |

#### <a name="response-action"></a>Akce odpovědi
Níže jsou vstupní pole pro akce odpovědi HTTP. A * znamená, že je povinné pole.

| Zobrazované jméno | Název vlastnosti | Popis |
| --- | --- | --- |
| Stavový kód * |statusCode |Stavový kód HTTP |
| Hlavičky |Záhlaví |Objekt JSON záhlaví odpovědi zahrnout |
| Tělo |hlavní část |Text odpovědi |

## <a name="next-steps"></a>Další postup
Nyní, vyzkoušejte si platformu a [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Další dostupné konektory v logic apps můžete prozkoumat podle našich [rozhraní API seznamu](apis-list.md).


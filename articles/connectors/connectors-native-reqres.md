---
title: Použít požadavek a odpověď akce | Microsoft Docs
description: Přehled žádostí a odpovědí aktivační události a akce v aplikaci Azure logiky
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
ms.openlocfilehash: 58210db585befd7ce915d4579d4d0303eb15bff3
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2018
ms.locfileid: "27960557"
---
# <a name="get-started-with-the-request-and-response-components"></a>Začínáme s komponentami žádosti a odpovědi
S žádostí a odpovědí komponenty aplikace logiky můžete v reálném čase reakce na události.

Můžete například provést následující věci:

* Odpověď na požadavek HTTP s daty z místní databáze pomocí aplikace logiky.
* Aktivovat aplikaci logiky z externí webhooku události.
* Volání aplikace logiky pomocí akce žádostí a odpovědí z v rámci jiné aplikace logiky.

Chcete-li začít používat žádostí a odpovědí akce v aplikaci logiky, přečtěte si téma [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-http-request-trigger"></a>Použít aktivační událost požadavku HTTP
Aktivační událost je událost, která můžete použít ke spuštění pracovního postupu, který je definován v aplikaci logiky. [Další informace o aktivační události](connectors-overview.md).

Zde je v sekvenci příklad toho, jak nastavit požadavku HTTP v Návrháři logiku aplikace.

1. Přidat aktivační událost **požadavku - obdrží žádost HTTP při** v aplikaci logiky. Volitelně můžete zadat schématu JSON (pomocí některého nástroje, například [JSONSchema.net](http://jsonschema.net)) pro tělo žádosti. To umožňuje návrháři ke generování tokenů pro vlastnosti v požadavku HTTP.
2. Přidáte další akci, takže můžete uložit aplikaci logiky.
3. Po uložení aplikaci logiky, můžete získat adresu URL požadavku HTTP z karty požadavku.
4. HTTP POST (můžete použít nástroje, jako je [Postman](https://www.getpostman.com/)) na adresu URL aktivuje aplikaci logiky.

> [!NOTE]
> Pokud nedefinujete akce odpovědi, `202 ACCEPTED` okamžitě vrátila odpověď na volajícího. Akce odpovědi můžete použít k přizpůsobení odpověď.
> 
> 

![Aktivační událost odpovědi](./media/connectors-native-reqres/using-trigger.png)

## <a name="use-the-http-response-action"></a>Pomocí akce HTTP odpovědi
Akce odpovědi HTTP je platný pouze při použití v pracovním postupu, která se spustí při požadavku HTTP. Pokud nedefinujete akce odpovědi, `202 ACCEPTED` okamžitě vrátila odpověď na volajícího.  V kterémkoliv kroku v rámci pracovního postupu můžete přidat akce odpovědi. Aplikace logiky pouze udržuje příchozího požadavku otevřete jednu minutu pro odpověď.  Za minutu, pokud žádná odpověď byla odeslána z pracovního postupu (a akce odpovědi v definici existuje) `504 GATEWAY TIMEOUT` je vrácen volajícímu.

Zde je postup přidání akce odpovědi HTTP:

1. Vyberte **nový krok** tlačítko.
2. Zvolte **přidat akci**.
3. Zadejte do vyhledávacího pole Akce **odpovědi** seznam akce odpovědi.
   
    ![Vyberte akci, která odpovědi](./media/connectors-native-reqres/using-action-1.png)
4. Přidejte do žádné parametry, které jsou požadovány pro zprávu odpovědi HTTP.
   
    ![Dokončení akce odpovědi](./media/connectors-native-reqres/using-action-2.png)
5. Klikněte levém horním panelu nástrojů na Uložit a aplikace logiky jak uložíte a publikovat (aktivovat).

## <a name="request-trigger"></a>Žádost o aktivační události
Tady jsou uvedené podrobnosti pro aktivační událost, která tento konektor podporuje. Je aktivační událost jedné žádosti.

| Trigger | Popis |
| --- | --- |
| Žádost |Nastane, když obdrží požadavek HTTP |

## <a name="response-action"></a>Akce odpovědi
Tady jsou uvedené podrobnosti pro akci, která tento konektor podporuje. Není k dispozici odpověď jedné akci, která lze použít pouze když je přiložena aktivační událost požadavku.

| Akce | Popis |
| --- | --- |
| Odpověď |Vrátí odpověď korelační požadavku HTTP |

### <a name="trigger-and-action-details"></a>Podrobnosti o aktivační události a akce
Následující tabulky popisují vstupní pole pro aktivační události a akce, a odpovídající výstupní podrobnosti.

#### <a name="request-trigger"></a>Žádost o aktivační události
Zde je vstupní pole pro aktivační událost z příchozí žádosti protokolu HTTP.

| Zobrazované jméno | Název vlastnosti | Popis |
| --- | --- | --- |
| Schématu JSON |schema |JSON schéma požadavku HTTP |

<br>

**Podrobnosti o výstupu**

Níže jsou uvedeny podrobnosti výstup pro daný požadavek.

| Název vlastnosti | Typ dat | Popis |
| --- | --- | --- |
| Záhlaví |Objekt |Hlavičky požadavku |
| Tělo |Objekt |Objekt žádosti |

#### <a name="response-action"></a>Akce odpovědi
Níže jsou uvedeny vstupních polí pro akce HTTP odpovědi. A * znamená, že je povinné pole.

| Zobrazované jméno | Název vlastnosti | Popis |
| --- | --- | --- |
| Stavový kód * |statusCode |Stavový kód protokolu HTTP |
| Záhlaví |hlavičky |Objekt JSON všechny hlavičky odpovědi zahrnout |
| Tělo |hlavní část |Text odpovědi |

## <a name="next-steps"></a>Další postup
Teď vyzkoušet platformu a [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ostatní konektory k dispozici v aplikace logiky můžete prozkoumat pohledem na našem [rozhraní API seznamu](apis-list.md).


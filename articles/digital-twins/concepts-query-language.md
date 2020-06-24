---
title: Dotazovací jazyk
titleSuffix: Azure Digital Twins
description: Seznamte se se základy jazyka úložiště dotazů digitálních vláken Azure.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: e4952bc3df2b5b164038654f5d77f4c352827463
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2020
ms.locfileid: "84726802"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>O dotazovacím jazyku pro digitální vlákna Azure

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

Odvolat, že střed digitálních vláken Azure je [**dvojitým grafem**](concepts-twins-graph.md)vytvořeným z **digitálních vláken** a **vztahů**. Tento graf se dá dotázat, aby se získaly informace o digitálních vztazích a vztazích, které obsahuje. Tyto dotazy se napíší ve vlastním dotazovacím jazyce podobném SQL s názvem **jazyk úložiště dotazů digitálních vláken Azure**.

K odeslání dotazu do služby z klientské aplikace použijete **rozhraní API pro dotazování**digitálních vláken Azure. To umožňuje vývojářům psát dotazy a používat filtry pro hledání sad digitálních vláken ve dvojitých grafech a další informace o scénáři digitálních vláken Azure.

## <a name="query-language-features"></a>Funkce jazyka dotazů

Digitální vlákna Azure poskytuje rozsáhlé možnosti dotazování pro dvojitou graf. Dotazy jsou popsány pomocí syntaxe podobné SQL jako nadmnožina možností [dotazovacího jazyka IoT Hub](../iot-hub/iot-hub-devguide-query-language.md).

Tady jsou dostupné operace v jazyce úložiště dotazů digitálních vláken Azure:
* Získejte vlákna podle vlastností digitálních vláken.
* Získejte vlákna pomocí rozhraní digitálních vláken.
* Získejte vlákna podle vlastností vztahu.
* Získejte vlákna v několika typech vztahů ( `JOIN` dotazů). Existují omezení počtu `JOIN` povolených (jedna úroveň pro veřejnou verzi Preview).
* Použijte vlastní funkci `IS_OF_MODEL(twinCollection, twinTypeName)` , která umožňuje filtrování na základě [modelu](concepts-models.md)vlákna. Podporuje dědičnost.
* Použijte libovolnou kombinaci ( `AND` , `OR` , `NOT` operátor) výše uvedeného.
* Použijte skalární funkce: `IS_BOOL` , `IS_DEFINED` , `IS_NULL` , `IS_NUMBER` , `IS_OBJECT` , `IS_PRIMITIVE` , `IS_STRING` , `STARTS_WITH` , `ENDS_WITH` .
* Použijte operátory porovnání dotazu: `AND` / `OR` / `NOT` , `IN` / `NOT IN` , `STARTSWITH` / `ENDSWITH` , `=` , `!=` , `<` , `>` , `<=` , `>=` .
* Použít pokračování: objekt dotazu má vytvořenou velikost stránky (až 100). Digitální vlákna lze načíst vždy po jedné stránce, a to Opakovaným voláním `nextAsTwin` metody.

## <a name="next-steps"></a>Další kroky

Naučte se zapisovat dotazy a zobrazit příklady klientského kódu v tématu [Postupy: dotazování na nevlákenný graf](how-to-query-graph.md).
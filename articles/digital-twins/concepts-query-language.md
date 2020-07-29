---
title: Dotazovací jazyk
titleSuffix: Azure Digital Twins
description: Seznamte se se základy jazyka úložiště dotazů digitálních vláken Azure.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: f7e9a76309b4d9dcd010b85d1b55f340374be5c4
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337921"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>O dotazovacím jazyku pro digitální vlákna Azure

Odvolat, že střed digitálních vláken Azure je [**dvojitým grafem**](concepts-twins-graph.md)vytvořeným z **digitálních vláken** a **vztahů**. Tento graf se dá dotázat, aby se získaly informace o digitálních vztazích a vztazích, které obsahuje. Tyto dotazy se napíší ve vlastním dotazovacím jazyce podobném SQL s názvem **jazyk úložiště dotazů digitálních vláken Azure**.

K odeslání dotazu do služby z klientské aplikace použijete [**rozhraní API pro dotazování**](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview)digitálních vláken Azure. To umožňuje vývojářům psát dotazy a používat filtry pro hledání sad digitálních vláken ve dvojitých grafech a další informace o scénáři digitálních vláken Azure.

## <a name="query-language-features"></a>Funkce jazyka dotazů

Digitální vlákna Azure poskytuje rozsáhlé možnosti dotazování pro dvojitou graf. Dotazy jsou popsány pomocí syntaxe jako v jazyce SQL, podobně jako [dotazovací jazyk IoT Hub](../iot-hub/iot-hub-devguide-query-language.md) s mnoha srovnatelnými funkcemi.

> [!NOTE]
> U všech operací dotazů na digitální vlákna Azure se rozlišují velká a malá písmena.

Tady jsou dostupné operace v jazyce úložiště dotazů digitálních vláken Azure:
* Získejte vlákna podle vlastností digitálních vláken (včetně [značek](how-to-use-tags.md)).
* Získejte vlákna pomocí rozhraní digitálních vláken.
* Získejte vlákna podle vlastností vztahu.
* Získejte vlákna v několika typech vztahů ( `JOIN` dotazů). Existují omezení počtu `JOIN` povolených (jedna úroveň pro veřejnou verzi Preview).
* Použijte vlastní funkci `IS_OF_MODEL(twinCollection, twinTypeName)` , která umožňuje filtrování na základě [modelu](concepts-models.md)vlákna. Podporuje dědičnost.
* Použijte skalární funkce: `IS_BOOL` , `IS_DEFINED` , `IS_NULL` , `IS_NUMBER` , `IS_OBJECT` , `IS_PRIMITIVE` , `IS_STRING` , `STARTS_WITH` , `ENDS_WITH` .
* Použití relačních operátorů dotazu: `IN` / `NIN` , `=` , `!=` , `<` , `>` , `<=` , `>=` .
* Použijte libovolnou kombinaci ( `AND` , `OR` , `NOT` operátor) výše uvedeného.
* Použít pokračování: objekt dotazu má vytvořenou velikost stránky (až 100). Pomocí tokenu pokračování v následných voláních rozhraní API můžete načíst digitální vlákna na jednu stránku po dobu.

## <a name="next-steps"></a>Další kroky

Naučte se zapisovat dotazy a zobrazit příklady klientského kódu v tématu [*Postupy: dotazování na nevlákenný graf*](how-to-query-graph.md).

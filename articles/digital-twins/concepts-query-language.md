---
title: Dotazovací jazyk
titleSuffix: Azure Digital Twins
description: Seznamte se se základy dotazovacího jazyka Azure pro digitální vlákna.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 29e1fa603600e246031f2a86aae3b0876b4910ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87562463"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>O dotazovacím jazyku pro digitální vlákna Azure

Odvolat, že střed digitálních vláken Azure je [**dvojitým grafem**](concepts-twins-graph.md)vytvořeným z **digitálních vláken** a **vztahů**. Tento graf se dá dotázat, aby se získaly informace o digitálních vztazích a vztazích, které obsahuje. Tyto dotazy se napíší ve vlastním dotazovacím jazyce podobném SQL, který se označuje jako **dotazovací jazyk digitálních vláken Azure**.

K odeslání dotazu do služby z klientské aplikace použijete [**rozhraní API pro dotazování**](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview)digitálních vláken Azure. To umožňuje vývojářům psát dotazy a používat filtry pro hledání sad digitálních vláken ve dvojitých grafech a další informace o scénáři digitálních vláken Azure.

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

## <a name="next-steps"></a>Další kroky

Naučte se zapisovat dotazy a zobrazit příklady klientského kódu v tématu [*Postupy: dotazování na nevlákenný graf*](how-to-query-graph.md).

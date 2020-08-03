---
title: Dotazovací jazyk
titleSuffix: Azure Digital Twins
description: Seznamte se se základy jazyka úložiště dotazů digitálních vláken Azure.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 960fff073353375cd50b31bc7284134ca733f142
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2020
ms.locfileid: "87488019"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>O dotazovacím jazyku pro digitální vlákna Azure

Odvolat, že střed digitálních vláken Azure je [**dvojitým grafem**](concepts-twins-graph.md)vytvořeným z **digitálních vláken** a **vztahů**. Tento graf se dá dotázat, aby se získaly informace o digitálních vztazích a vztazích, které obsahuje. Tyto dotazy se napíší ve vlastním dotazovacím jazyce podobném SQL s názvem **jazyk úložiště dotazů digitálních vláken Azure**.

K odeslání dotazu do služby z klientské aplikace použijete [**rozhraní API pro dotazování**](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview)digitálních vláken Azure. To umožňuje vývojářům psát dotazy a používat filtry pro hledání sad digitálních vláken ve dvojitých grafech a další informace o scénáři digitálních vláken Azure.

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

## <a name="next-steps"></a>Další kroky

Naučte se zapisovat dotazy a zobrazit příklady klientského kódu v tématu [*Postupy: dotazování na nevlákenný graf*](how-to-query-graph.md).

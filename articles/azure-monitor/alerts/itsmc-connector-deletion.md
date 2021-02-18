---
title: Odstranění konektoru ITSM a akce, která je k němu přidružená
description: V tomto článku najdete vysvětlení postupu odstranění konektoru ITSM a skupin akcí, které jsou k němu přidružené.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: 1fbaa48d104642984e604bc66a3aa914fbfed44c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100611051"
---
# <a name="deletion-of-unused-itsm-connectors"></a>Odstraňování nepoužívaných konektorů ITSM

Proces odstranění nepoužívaného konektoru obsahuje 2 fáze:

1. Odstranění přidružených akcí: všechny akce, které jsou přidružené k konektoru ITSM, by měly být odstraněny. To by mělo být provedeno, aby nedošlo k chybám bez konektoru, který by mohl způsobit chyby ve vašem předplatném.

2. Odstranění nepoužívaného konektoru ITSM

## <a name="deletion-of-the-associated-actions"></a>Odstranění přidružených akcí

1. Pokud chcete najít skupinu akcí, měli byste přejít na  ![ snímek obrazovky monitorování výběru monitorování.](media/itsmc-connector-deletion/itsmc-monitor-selection.png)

2. Vyberte "výstrahy"  ![ snímek obrazovky výběru výstrah.](media/itsmc-connector-deletion/itsmc-alert-selection.png)
3. Vyberte snímek obrazovky Správa akcí  ![ pro výběr akcí správy.](media/itsmc-connector-deletion/itsmc-actions-selection.png)
4. Vyberte všechny konektory ITSM připojené k Cherwell  ![ snímku obrazovky ITSM konektorů, které jsou připojené k Cherwell.](media/itsmc-connector-deletion/itsmc-actions-screen.png)
5. Odstraňte  ![ snímek obrazovky skupiny akcí pro odstranění skupiny akcí.](media/itsmc-connector-deletion/itsmc-action-deletion.png)

## <a name="deletion-of-the-unused-itsm-connector"></a>Odstranění nepoužívaného konektoru ITSM

1. Měli byste Hledat a vybrat "partnera" LA na horním snímku vyhledávacího panelu hledání  ![ a vybrat "partnera" La.](media/itsmc-connector-deletion/itsmc-connector-selection.png)
2. Vyberte "připojení ITSM" a vyberte snímek obrazovky konektoru Cherwell konektorů  ![ CHERWELL ITSM.](media/itsmc-connector-deletion/itsmc-cherwell-connector.png)
3. Vyberte Odstranit  ![ snímek obrazovky s odstraněním konektoru ITSM.](media/itsmc-connector-deletion/itsmc-connector-deletion.png)

## <a name="next-steps"></a>Další kroky

* [Řešení potíží v ITSM konektoru](./itsmc-resync-servicenow.md)

---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 7aa3867fdc5de320c47a15737b655b8032f402a6
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075556"
---
Tento krok spojuje všechny prostředky a složky, které jste v instanci Windows serveru nastavili během předchozích kroků.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Vyhledejte prostředek služby synchronizace úložiště.
1. Vytvořte novou *skupinu synchronizace* v rámci prostředku služby synchronizace úložiště pro každou sdílenou složku Azure. V Synchronizace souborů Azure terminologii se sdílená složka Azure stane *koncovým bodem cloudu* v topologii synchronizace, který budete popisovat pomocí vytvoření skupiny synchronizace. Když vytváříte skupinu synchronizace, poskytněte jí známý název, který vám umožní rozpoznat, kterou sadu souborů se synchronizuje. Ujistěte se, že se na sdílenou složku Azure odkazuje pomocí odpovídajícího názvu.
1. Po vytvoření skupiny synchronizace se v seznamu skupin synchronizace zobrazí řádek. Vyberte název (odkaz), chcete-li zobrazit obsah skupiny synchronizace. Sdílená složka Azure se zobrazí v části **koncové body cloudu**.
1. Vyhledejte tlačítko **+ Přidat server koncový bod** . Složka na místním serveru, kterou jste zřídili, se stane cestou k tomuto *koncovému bodu serveru*.

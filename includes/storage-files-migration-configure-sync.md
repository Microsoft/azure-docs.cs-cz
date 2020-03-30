---
title: Konfigurace synchronizace souborů Azure
description: Konfigurace synchronizace souborů Azure. Běžný textový blok sdílený mezi dokumenty migrace.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 53d7af6b43ff24ab12501570385162759f4c7633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209581"
---
Tento krok spojuje všechny prostředky a složky, které jste nastavili na serveru Windows Server během předchozích kroků.

* Přihlaste se k [portálu Azure](https://portal.azure.com).
* Vyhledejte prostředek služby Synchronizace úložiště.
* Vytvořte novou *skupinu synchronizace* v rámci prostředku služby Synchronizace úložiště pro každou sdílenou složku Azure. V terminologii Azure File Sync se sdílená složka Azure stane *koncovým bodem cloudu* v topologii synchronizace, kterou popisujete při vytváření synchronizační skupiny. Při vytváření skupiny synchronizace jí dejte známý název, abyste rozpoznali, která sada souborů se zde synchronizuje. Ujistěte se, že odkazujete na sdílenou složku Azure s odpovídajícím názvem.
* Po vytvoření skupiny synchronizace se v seznamu skupin synchronizace zobrazí řádek. Kliknutím na název (odkaz) zobrazíte obsah skupiny synchronizace. V části "Cloudové koncové body" se zobrazí sdílená složka Azure.
* Vyhledejte příkazové tlačítko *+ Add Server Endpoint*. Složka na místním serveru, který jste zřídit, se stane cestou pro tento *koncový bod serveru*.

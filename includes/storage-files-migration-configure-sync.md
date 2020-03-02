---
title: Konfigurace Azure File Sync
description: Nakonfigurujte Azure File Sync. Společný textový blok, který je sdílen mezi dokumenty migrace.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 53d7af6b43ff24ab12501570385162759f4c7633
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209581"
---
Tento krok spojuje všechny prostředky a složky, které jste v systému Windows Server nastavili během předchozích kroků.

* Přihlaste se k [Azure Portal](https://portal.azure.com).
* Vyhledejte prostředek služby synchronizace úložiště.
* Vytvořte novou *skupinu synchronizace* v rámci prostředku služby synchronizace úložiště pro každou sdílenou složku Azure. V Azure File Sync terminologii se sdílená složka Azure stane *koncovým bodem cloudu* v topologii synchronizace, který popisujete vytvořením skupiny synchronizace. Když vytváříte skupinu synchronizace, dejte jí známý název, jako je třeba rozpoznání, která sada souborů se synchronizuje. Ujistěte se, že se na sdílenou složku Azure odkazuje pomocí odpovídajícího názvu.
* Jakmile je skupina synchronizace vytvořená, zobrazí se v seznamu skupin synchronizace řádek. Kliknutím na název (odkaz) zobrazíte obsah skupiny synchronizace. Sdílená složka Azure se zobrazí v části koncové body cloudu.
* Vyhledejte příkazové tlačítko a *přidejte koncový bod serveru*. Složka na místním serveru, kterou jste zřídili, se stane cestou k tomuto *koncovému bodu serveru*.

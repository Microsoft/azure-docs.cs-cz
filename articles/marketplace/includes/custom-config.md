---
title: zahrnout soubor
description: " – soubor"
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: a1bc7cf1fd339ca3660c7b39326f37d2763c74b2
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283900"
---
Pokud potřebujete další konfiguraci, použijte naplánovanou úlohu, která se spustí při spuštění, aby se konečné změny virtuálního počítače provedly po jeho nasazení. Zvažte také následující body:

- Pokud se jedná o úlohu spuštění jednou, je nutné úlohu po úspěšném dokončení odstranit.
- Konfigurace by se neměla spoléhat na jiné jednotky než C nebo D, protože existují jenom tyto dvě jednotky (jednotka C je disk s operačním systémem a jednotka D je dočasný místní disk).

Další informace o úpravách pro Linux najdete v tématu [rozšíření a funkce virtuálních počítačů pro Linux](../../virtual-machines/extensions/features-linux.md).
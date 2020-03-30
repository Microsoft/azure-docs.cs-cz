---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 2e90d218aa6dc90746ba0e928fb3393f0bdb5e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68966350"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Pokud je úložiště dat nakonfigurováno jedním z následujících způsobů, musíte nastavit prostředí Integrace s [vlastním hostitelem,](../articles/data-factory/create-self-hosted-integration-runtime.md) abyste se mohli připojit k tomuto úložišti dat:

- Úložiště dat se nachází uvnitř místní sítě, uvnitř virtuální sítě Azure nebo uvnitř amazonského virtuálního privátního cloudu.
- Úložiště dat je spravovaná cloudová datová služba, kde je přístup omezen na ip adresy na seznamu povolených v pravidlech brány firewall.

---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 2e90d218aa6dc90746ba0e928fb3393f0bdb5e5a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "68966350"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Pokud je úložiště dat nakonfigurované jedním z následujících způsobů, je nutné nastavit [Integration runtime](../articles/data-factory/create-self-hosted-integration-runtime.md) v místním prostředí, aby se mohl připojit k tomuto úložišti dat:

- Úložiště dat se nachází v místní síti, v rámci Azure Virtual Network nebo v rámci virtuálního privátního cloudu Amazon.
- Úložiště dat je spravovaná cloudová datová služba, ve které je přístup omezený na IP adresy povolené v pravidlech brány firewall.

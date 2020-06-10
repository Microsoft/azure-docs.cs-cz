---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 0b03957178af0578d2c6cd91d7377c93f413cec3
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84629485"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Pokud je úložiště dat nakonfigurované jedním z následujících způsobů, je třeba nastavit [prostředí Integration runtime](../articles/data-factory/create-self-hosted-integration-runtime.md) v místním prostředí pro připojení k úložišti dat:

- Úložiště dat se nachází v místní síti, ve službě Azure Virtual Network nebo ve virtuálním privátním cloudu Amazon.
- Úložiště dat je spravovaná cloudová datová služba, ve které je přístup omezený na IP adresy, které jsou na seznamu povolených pravidel brány firewall.

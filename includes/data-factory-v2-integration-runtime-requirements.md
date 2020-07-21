---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 994b8285999bfa52e2aea9a57ad832aefddcfb76
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544883"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Pokud se vaše úložiště dat nachází v místní síti, službě Azure Virtual Network nebo ve virtuálním privátním cloudu Amazon, musíte nastavit místní [prostředí Integration runtime](../articles/data-factory/create-self-hosted-integration-runtime.md) , které se k němu připojí.

Pokud je vaše úložiště dat spravovaná cloudová datová služba, ve které je přístup omezený na IP adresy, které jsou na seznamu povolených pravidel brány firewall, můžete použít Azure Integration runtime a přidat [jeho IP adresy](../articles/data-factory/azure-integration-runtime-ip-addresses.md) do seznamu povolených. 

Informace o mechanismech zabezpečení sítě podporovaných nástrojem Data Factory pro přístup k úložištím dat obecně najdete v tématu věnovaném [strategiím přístupu k datům](../articles/data-factory/data-access-strategies.md) .

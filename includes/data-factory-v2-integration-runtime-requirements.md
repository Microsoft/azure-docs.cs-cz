---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 6c348b3dd0005eeab154aa2d74abc617cbd1d0cb
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2020
ms.locfileid: "87529380"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Pokud se vaše úložiště dat nachází v místní síti, službě Azure Virtual Network nebo ve virtuálním privátním cloudu Amazon, musíte nastavit místní [prostředí Integration runtime](../articles/data-factory/create-self-hosted-integration-runtime.md) , které se k němu připojí.

Pokud vaše úložiště dat je spravovaná cloudová datová služba, můžete použít prostředí Azure Integration runtime. Pokud je přístup omezený na IP adresy, které jsou na seznamu povolených pravidel brány firewall, můžete přidat [Azure Integration runtime IP adresy](../articles/data-factory/azure-integration-runtime-ip-addresses.md) do seznamu povolených. 

Další informace o mechanismech zabezpečení sítě a možnostech podporovaných nástrojem Data Factory najdete v tématu věnovaném [strategiím přístupu k datům](../articles/data-factory/data-access-strategies.md).

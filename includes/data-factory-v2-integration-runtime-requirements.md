---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: fbde8bc28f8fc34b7a6a6443950b8733c6dcff45
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023166"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Pokud se vaše úložiště dat nachází v místní síti, službě Azure Virtual Network nebo ve virtuálním privátním cloudu Amazon, je nutné nakonfigurovat [prostředí Integration runtime](../articles/data-factory/create-self-hosted-integration-runtime.md) v místním prostředí, aby se k němu mohlo připojit.

Případně, pokud vaše úložiště dat je spravovaná cloudová datová služba, můžete použít prostředí Azure Integration runtime. Pokud je přístup omezený na IP adresy, které jsou v pravidlech brány firewall schválené, můžete do seznamu povolených [adres přidat Azure Integration runtime IP adresy](../articles/data-factory/azure-integration-runtime-ip-addresses.md) . 

Další informace o mechanismech zabezpečení sítě a možnostech podporovaných nástrojem Data Factory najdete v tématu věnovaném [strategiím přístupu k datům](../articles/data-factory/data-access-strategies.md).

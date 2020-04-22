---
title: Zabezpečení transportní vrstvy v Azure HDInsight
description: Zabezpečení transportní vrstvy (TLS) a vrstvy steskových soketů (SSL) jsou kryptografické protokoly, které poskytují zabezpečení komunikace v počítačové síti.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: fbe602581ebcea6385fb9cc9953d8e48272ce429
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771960"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Zabezpečení transportní vrstvy v Azure HDInsight

Připojení ke clusteru HDInsight prostřednictvím `https://CLUSTERNAME.azurehdinsight.net` koncového bodu veřejného clusteru jsou proxied prostřednictvím uzlů brány clusteru. Tato připojení jsou zabezpečena pomocí protokolu nazvaného TLS. Vynucení vyšších verzí protokolu TLS na branách zlepšuje zabezpečení těchto připojení. Další informace o tom, proč byste měli používat novější verze tls, naleznete [v tématu Řešení problému TLS 1.0](https://docs.microsoft.com/security/solving-tls1-problem).

Ve výchozím nastavení clustery Azure HDInsight přijímají připojení TLS 1.2 na veřejných koncových bodech HTTPS a starší verze pro zpětnou kompatibilitu. Minimální verzi TLS podporovanou na uzlech brány můžete řídit během vytváření clusteru pomocí portálu Azure nebo šablony Správce prostředků. Pro portál vyberte verzi TLS na kartě **Zabezpečení + sítě** během vytváření clusteru. Pro šablonu Správce prostředků v době nasazení použijte vlastnost **minSupportedTlsVersion.** Ukázkovou šablonu najdete v [tématu HDInsight minimální šablona rychlého startu TLS 1.2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls). Tato vlastnost podporuje tři hodnoty: "1.0", "1.1" a "1.2", které odpovídají TLS 1.0+, TLS 1.1+ a TLS 1.2+ v uvedeném pořadí.

> [!IMPORTANT]
> června 2020 bude Azure HDInsight vynucovat verze TLS 1.2 nebo novější pro všechna připojení HTTPS. Doporučujeme zajistit, aby všichni vaši klienti byli připraveni ke zpracování verzí TLS 1.2 nebo novějších verzí. Další informace najdete v [tématu Azure HDInsight TLS 1.2 Enforcement](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/).

## <a name="next-steps"></a>Další kroky

* [Plánování virtuální sítě pro Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md)
* [Vytvořte virtuální sítě pro clustery Azure HDInsight](hdinsight-create-virtual-network.md).
* [Skupiny zabezpečení sítě](../virtual-network/security-overview.md).

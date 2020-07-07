---
title: Zabezpečení transportní vrstvy ve službě Azure HDInsight
description: Protokol TLS (Transport Layer Security) a SSL (Secure Sockets Layer) jsou kryptografické protokoly, které poskytují zabezpečení komunikace v počítačové síti.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: b74ca75b26d4d98c79091683f428eb39e5827665
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "82183496"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Zabezpečení transportní vrstvy ve službě Azure HDInsight

Připojení ke clusteru HDInsight prostřednictvím koncového bodu veřejného clusteru `https://CLUSTERNAME.azurehdinsight.net` jsou proxy serverem prostřednictvím uzlů brány clusteru. Tato připojení jsou zabezpečená pomocí protokolu s názvem TLS. Vynucování vyšších verzí TLS u bran vylepšuje zabezpečení těchto připojení. Další informace o tom, proč byste měli používat novější verze protokolu TLS, najdete v tématu [řešení problému s protokolem tls 1,0](https://docs.microsoft.com/security/solving-tls1-problem).

Ve výchozím nastavení clustery Azure HDInsight přijímají připojení TLS 1,2 k veřejným koncovým bodům HTTPS a starší verze z důvodu zpětné kompatibility. Minimální verzi TLS podporovanou v uzlech brány můžete řídit během vytváření clusteru pomocí Azure Portal nebo šablony Správce prostředků. V případě portálu vyberte během vytváření clusteru verzi TLS z karty **zabezpečení + sítě** . Pro šablonu Správce prostředků v době nasazení použijte vlastnost **minSupportedTlsVersion** . Ukázkovou šablonu najdete v článku o [1,2 šablony pro rychlé zprovoznění HDInsight minima](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls). Tato vlastnost podporuje tři hodnoty: "1,0", "1,1" a "1,2", které odpovídají TLS 1.0 +, TLS 1.1 + a TLS 1.2 +.

> [!IMPORTANT]
> Od 30. června 2020 bude Azure HDInsight vymáhat TLS 1,2 nebo novější verzi pro všechna připojení HTTPS. Doporučujeme, abyste zajistili, že všichni klienti jsou připraveni na zpracování TLS 1,2 nebo novějších verzí. Další informace najdete v tématu [vynucení pro Azure HDInsight TLS 1,2](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/).

## <a name="next-steps"></a>Další kroky

* [Plánování virtuální sítě pro Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md)
* [Vytvořte virtuální sítě pro clustery Azure HDInsight](hdinsight-create-virtual-network.md).
* [Skupiny zabezpečení sítě](../virtual-network/security-overview.md).

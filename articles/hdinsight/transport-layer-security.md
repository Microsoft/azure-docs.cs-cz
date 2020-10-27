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
ms.openlocfilehash: 42c1efa176a6dbf5294e19afe02fd74a53150272
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538948"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Zabezpečení transportní vrstvy ve službě Azure HDInsight

Připojení ke clusteru HDInsight prostřednictvím koncového bodu veřejného clusteru `https://CLUSTERNAME.azurehdinsight.net` jsou proxy serverem prostřednictvím uzlů brány clusteru. Tato připojení jsou zabezpečená pomocí protokolu s názvem TLS. Vynucování vyšších verzí TLS u bran vylepšuje zabezpečení těchto připojení. Další informace o tom, proč byste měli používat novější verze protokolu TLS, najdete v tématu [řešení problému s protokolem tls 1,0](/security/solving-tls1-problem).

Ve výchozím nastavení clustery Azure HDInsight přijímají připojení TLS 1,2 k veřejným koncovým bodům HTTPS a starší verze z důvodu zpětné kompatibility. Minimální verzi TLS podporovanou v uzlech brány můžete řídit během vytváření clusteru pomocí Azure Portal nebo šablony Správce prostředků. V případě portálu vyberte během vytváření clusteru verzi TLS z karty **zabezpečení + sítě** . Pro šablonu Správce prostředků v době nasazení použijte vlastnost **minSupportedTlsVersion** . Ukázkovou šablonu najdete v článku o [1,2 šablony pro rychlé zprovoznění HDInsight minima](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls). Tato vlastnost podporuje tři hodnoty: "1,0", "1,1" a "1,2", které odpovídají TLS 1.0 +, TLS 1.1 + a TLS 1.2 +.


## <a name="next-steps"></a>Další kroky

* [Plánování virtuální sítě pro Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md)
* [Vytvořte virtuální sítě pro clustery Azure HDInsight](hdinsight-create-virtual-network.md).
* [Skupiny zabezpečení sítě](../virtual-network/network-security-groups-overview.md).
---
title: Kvóty a dostupnost oblastí pro Azure Container Instances
description: Výchozí kvóty a dostupnost oblastí služby Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 02/27/2018
ms.author: danlep
ms.openlocfilehash: 427dd8bd4abb72e2750752d828e189921401e9e0
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902346"
---
# <a name="quotas-and-region-availability-for-azure-container-instances"></a>Kvóty a dostupnost oblastí pro Azure Container Instances

Všechny služby Azure zahrnují určitá výchozí omezení a kvóty pro prostředky a funkce. V následujících oddílech jsou podrobně popsané výchozí limity pro několik prostředků Azure Container Instances (ACI) a také dostupnost služby ACI v oblastech Azure.

## <a name="service-quotas-and-limits"></a>Kvóty a omezení služeb

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="region-availability"></a>Dostupnost v oblastech

Služba Azure Container Instances je dostupná v následujících oblastech s uvedenými omezeními procesoru a paměti.

| Umístění | Operační systém | Procesor | Paměť (GB) |
| -------- | -- | :---: | :-----------: |
| USA – východ, Severní Evropa, Západní Evropa, USA – západ, USA – západ 2 | Linux | 4 | 14 |
| Austrálie – východ, USA – východ 2, Jihovýchodní Asie | Linux | 2 | 7 |
| Indie – střed, Střed USA – jih | Linux | 2 | 3,5 |
| USA – východ, Západní Evropa, USA – západ | Windows | 4 | 14 |
| Austrálie – východ, Indie – střed, USA – východ 2, Severní Evropa, Střed USA – jih, Jihovýchodní Asie, USA – západ 2 | Windows | 2 | 3,5 |

Pro instance kontejnerů vytvořené v mezích těchto omezení prostředků platí určitá omezení z hlediska dostupnosti v rámci oblasti nasazení. V případě velkého zatížení oblasti může při nasazování instancí docházet k selhání. Pokud chcete selhání nasazení zmírnit, zkuste nasazení instancí s nižšími nastaveními procesoru a paměti nebo zkuste nasazení později.

Sdělte týmu, ve kterých dalších oblastech je potřeba zvýšit omezení procesoru nebo paměti, na stránce [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Další informace o řešení potíží s nasazením instance kontejneru najdete v tématu [Řešení potíží s nasazením pomocí Azure Container Instances](container-instances-troubleshooting.md).

## <a name="next-steps"></a>Další kroky

Některé výchozí limity a kvóty je možné zvýšit. Pokud si chcete vyžádat zvýšení jednoho nebo několika prostředků, které takové zvýšení podporují, odešlete prosím [žádost o podporu Azure][azure-support] (jako **typ problému** vyberte kvótu).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

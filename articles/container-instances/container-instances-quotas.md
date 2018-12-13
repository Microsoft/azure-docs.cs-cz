---
title: Kvóty a dostupnost oblastí pro Azure Container Instances
description: Výchozí kvóty a dostupnost oblastí služby Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 12/07/2018
ms.author: danlep
ms.openlocfilehash: a7b61702feb062c57fdec84f335ace44a47d0283
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53249477"
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
| Japonsko – východ | Linux | 2 | 8 |
| Austrálie – východ, USA – východ 2, Jihovýchodní Asie | Linux | 2 | 7 |
| Kanada – střed, střed Indie, východní Asie, střed USA – sever, střed USA – jih | Linux | 2 | 3,5 |
| USA – východ, Západní Evropa, USA – západ | Windows | 4 | 14 |
| Austrálie – východ, Kanada – střed, střed Indie, východní Asie, východní USA 2, Japonsko – východ, severní centrální USA, Severní Evropa, střed USA – Jih, jihovýchodní Asie, USA – západ 2 | Windows | 2 | 3,5 |

Pro instance kontejnerů vytvořené v mezích těchto omezení prostředků platí určitá omezení z hlediska dostupnosti v rámci oblasti nasazení. V případě velkého zatížení oblasti může při nasazování instancí docházet k selhání. Pokud chcete selhání nasazení zmírnit, zkuste nasazení instancí s nižšími nastaveními procesoru a paměti nebo zkuste nasazení později.

Sdělte týmu, ve kterých dalších oblastech je potřeba zvýšit omezení procesoru nebo paměti, na stránce [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Další informace o řešení potíží s nasazením instance kontejneru najdete v tématu [Řešení potíží s nasazením pomocí Azure Container Instances](container-instances-troubleshooting.md).

## <a name="next-steps"></a>Další postup

Některé výchozí limity a kvóty je možné zvýšit. Pokud si chcete vyžádat zvýšení jednoho nebo několika prostředků, které takové zvýšení podporují, odešlete prosím [žádost o podporu Azure][azure-support] (jako **typ problému** vyberte kvótu).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

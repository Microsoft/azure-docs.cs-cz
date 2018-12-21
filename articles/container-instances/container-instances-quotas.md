---
title: Kvóty a dostupnost oblastí pro Azure Container Instances
description: Výchozí kvóty a dostupnost oblastí služby Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 12/17/2018
ms.author: danlep
ms.openlocfilehash: 86c169c84e905362e8fdb069e8ceadcb5e19fe67
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53602388"
---
# <a name="quotas-and-region-availability-for-azure-container-instances"></a>Kvóty a dostupnost oblastí pro Azure Container Instances

Všechny služby Azure zahrnují určitá výchozí omezení a kvóty pro prostředky a funkce. V následujících oddílech jsou podrobně popsané výchozí limity pro několik prostředků Azure Container Instances (ACI) a také dostupnost služby ACI v oblastech Azure.

## <a name="service-quotas-and-limits"></a>Kvóty a omezení služeb

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="region-availability"></a>Dostupnost v oblastech

Služba Azure Container Instances je dostupná v následujících oblastech s uvedenými omezeními procesoru a paměti. Hodnoty jsou aktuální k datu publikování. Aktuální informace, použijte [seznamu možnosti](/rest/api/container-instances/listcapabilities/listcapabilities) rozhraní API.

| Umístění | Operační systém | Procesor | Paměť (GB) |
| -------- | -- | :---: | :-----------: |
| Kanada – střed | Linux | 4 | 16 |
| USA – východ, Severní Evropa, Západní Evropa, Západní USA, Západní USA 2 | Linux | 4 | 14 |
| Japonsko – východ | Linux | 2 | 8 |
| Austrálie – východ, USA – východ 2, Jihovýchodní Asie | Linux | 2 | 7 |
| Střed Indie, východní Asie, severní centrální USA, střed USA – Jih, Indie – jih | Linux | 2 | 3,5 |
| USA – východ, Západní Evropa, Západní USA | Windows | 4 | 14 |
| Austrálie – východ, Kanada – střed, střed Indie, východní Asie, východní USA 2, Japonsko – východ, USA (střed) – sever, Severní Evropa, střed USA – Jih, Indie – Jih, jihovýchodní Asie, USA – západ 2 | Windows | 2 | 3,5 |

Pro instance kontejnerů vytvořené v mezích těchto omezení prostředků platí určitá omezení z hlediska dostupnosti v rámci oblasti nasazení. V případě velkého zatížení oblasti může při nasazování instancí docházet k selhání. Pokud chcete selhání nasazení zmírnit, zkuste nasazení instancí s nižšími nastaveními procesoru a paměti nebo zkuste nasazení později.

Sdělte týmu, ve kterých dalších oblastech je potřeba zvýšit omezení procesoru nebo paměti, na stránce [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Další informace o řešení potíží s nasazením instance kontejneru najdete v tématu [Řešení potíží s nasazením pomocí Azure Container Instances](container-instances-troubleshooting.md).

## <a name="next-steps"></a>Další postup

Některé výchozí limity a kvóty je možné zvýšit. Pokud si chcete vyžádat zvýšení jednoho nebo několika prostředků, které takové zvýšení podporují, odešlete prosím [žádost o podporu Azure][azure-support] (jako **typ problému** vyberte kvótu).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

---
title: Kvóty a dostupnost oblastí pro Azure Container Instances
description: Kvóty, limity a dostupnost oblastí služby Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 02/15/2019
ms.author: danlep
ms.openlocfilehash: c676989b4b882f2b1887a1b6a5091b60027f61d0
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328398"
---
# <a name="quotas-and-region-availability-for-azure-container-instances"></a>Kvóty a dostupnost oblastí pro Azure Container Instances

Všechny služby Azure zahrnují určitá výchozí omezení a kvóty pro prostředky a funkce. Následující části jsou podrobně popsané výchozí limity pro několik prostředků Azure Container Instances a také dostupnost služby v oblasti Azure, které prostředků.

## <a name="service-quotas-and-limits"></a>Kvóty a omezení služeb

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="feature-availability"></a>Dostupnost funkcí

Služba Azure Container Instances může plánovat kontejnery Windows i Linuxu pomocí stejného rozhraní API. Následující funkce jsou však aktuálně k dispozici pouze ve skupinách kontejnerů Linuxu. Podpora Windows je naplánovaná.

* Více kontejnerů na skupinu kontejnerů
* Připojení svazku (soubory Azure, emptyDir GitRepo, tajný klíč)
* Virtuální sítě (preview)
* Prostředky GPU (preview)

## <a name="region-availability"></a>Dostupnost v oblastech

Služba Azure Container Instances je dostupná v následujících oblastech s omezeními procesoru a paměti, zadaný pro každou skupinu kontejnerů. Hodnoty jsou aktuální k datu publikování. Aktuální informace, použijte [seznamu možnosti](/rest/api/container-instances/listcapabilities/listcapabilities) rozhraní API. 

Omezení dostupnosti a prostředků se může lišit při použití Azure Container Instances pomocí [virtuální sítě](container-instances-vnet.md) (preview) nebo s [GPU prostředky](container-instances-gpu.md) (preview).

| Umístění | Operační systém | Procesor | Paměť (GB) |
| -------- | -- | :---: | :-----------: |
| Kanada – střed, střed USA, východní USA 2, střed USA – jih | Linux | 4 | 16 |
| USA – východ, Severní Evropa, Západní Evropa, Západní USA, Západní USA 2 | Linux | 4 | 14 |
| Japonsko – východ | Linux | 2 | 8 |
| Austrálie – východ, Asie – jihovýchod | Linux | 2 | 7 |
| Střed Indie, východní Asie, severní centrální USA, Jižní Indie | Linux | 2 | 3,5 |
| USA – východ, Západní Evropa, Západní USA | Windows | 4 | 14 |
| Austrálie – východ, Kanada – střed, střed Indie, střed USA, východní Asie, východní USA 2, Japonsko – východ, USA (střed) – sever, Severní Evropa, střed USA – Jih, Indie – Jih, jihovýchodní Asie, USA – západ 2 | Windows | 2 | 3,5 |

Pro instance kontejnerů vytvořené v mezích těchto omezení prostředků platí určitá omezení z hlediska dostupnosti v rámci oblasti nasazení. V případě velkého zatížení oblasti může při nasazování instancí docházet k selhání. Pokud chcete selhání nasazení zmírnit, zkuste nasazení instancí s nižšími nastaveními procesoru a paměti nebo zkuste nasazení později.

Sdělte týmu, ve kterých dalších oblastech je potřeba zvýšit omezení procesoru nebo paměti, na stránce [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Další informace o řešení potíží s nasazením instance kontejneru najdete v tématu [Řešení potíží s nasazením pomocí Azure Container Instances](container-instances-troubleshooting.md).

## <a name="next-steps"></a>Další postup

Některé výchozí limity a kvóty je možné zvýšit. Pokud si chcete vyžádat zvýšení jednoho nebo několika prostředků, které takové zvýšení podporují, odešlete prosím [žádost o podporu Azure][azure-support] (jako **typ problému** vyberte kvótu).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

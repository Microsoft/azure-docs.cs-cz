---
title: Referenční tabulka pro všechna Azure Security Center doporučení
description: V tomto článku jsou uvedená doporučení zabezpečení Azure Security Center, která vám pomůžou posílit a chránit vaše prostředky.
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 03/14/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: b4fe575039c6e913ca20198f204b6529f6c482c7
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466190"
---
# <a name="security-recommendations---a-reference-guide"></a>Doporučení k zabezpečení – Referenční příručka

V tomto článku jsou uvedena doporučení, která se mohou zobrazit v Azure Security Center. Doporučení zobrazená ve vašem prostředí závisí na prostředcích, které chráníte, a na vašich vlastních konfiguracích.

Doporučení pro Security Center jsou založená na [testu zabezpečení Azure](../security/benchmarks/introduction.md). Srovnávací test zabezpečení Azure je Microsoftem vytvořená sada pokynů pro zabezpečení a osvědčené postupy na základě běžných architektur dodržování předpisů, které jsou specifické pro Azure. Toto široce uznávané sestavování srovnávacích testů v ovládacích prvcích z [centra pro Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) a [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) se soustředí na zabezpečení zaměřené na Cloud.

Další informace o tom, jak na tato doporučení reagovat, najdete v tématu o [nápravě doporučení v Azure Security Center](security-center-remediate-recommendations.md).

Vaše zabezpečené skóre vychází z počtu Security Center doporučení, která jste dokončili. Chcete-li se rozhodnout, která doporučení se mají nejdřív vyřešit, podívejte se na závažnost každého z nich a na jeho potenciální dopad na vaše zabezpečené skóre.

> [!TIP]
> Pokud popis doporučení znamená "žádná související zásada", většinou se jedná o to, že doporučení závisí na jiném _doporučení a zásadách_ . Například doporučení "selhání stavu ochrany koncového bodu Endpoint Protection by mělo být opraveno...", spoléhá na doporučení, které kontroluje, zda je řešení ochrany koncových bodů ještě _nainstalováno_ ("musí být nainstalováno řešení ochrany koncových bodů..."). Základní _doporučení má_ zásadu.
> Omezení zásad jenom na základní doporučení zjednodušuje správu zásad.

## <a name="appservices-recommendations"></a><a name='recs-appservices'></a>Doporučení pro aplikační služby

[!INCLUDE [asc-recs-appservices](../../includes/asc-recs-appservices.md)]

## <a name="compute-recommendations"></a><a name='recs-compute'></a>Doporučení k výpočtům

[!INCLUDE [asc-recs-compute](../../includes/asc-recs-compute.md)]

## <a name="container-recommendations"></a><a name='recs-container'></a>Doporučení pro kontejner

[!INCLUDE [asc-recs-container](../../includes/asc-recs-container.md)]

## <a name="data-recommendations"></a><a name='recs-data'></a>Doporučení pro data

[!INCLUDE [asc-recs-data](../../includes/asc-recs-data.md)]

## <a name="identityandaccess-recommendations"></a><a name='recs-identityandaccess'></a>Doporučení pro IdentityAndAccess

[!INCLUDE [asc-recs-identityandaccess](../../includes/asc-recs-identityandaccess.md)]

## <a name="networking-recommendations"></a><a name='recs-networking'></a>Doporučení pro sítě

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>Zastaralá doporučení

|Doporučení|Popis & souvisejících zásad|Závažnost|Je Rychlá oprava povolená? ([Další informace](security-center-remediate-recommendations.md#quick-fix-remediation))|Typ prostředku|
|----|----|----|----|----|
|**Přístup k App Services by měl být omezený.**|Omezte přístup k vašemu App Services změnou konfigurace sítě tak, aby odepřela příchozí provoz z rozsahů, které jsou příliš široké.<br>(Související zásady: [Preview]: přístup k App Services by měl být omezený)|Vysoká|N|App Service|
|**Pravidla pro webové aplikace v IaaS skupin zabezpečení sítě by se měla posílit.**|Posílit zabezpečení skupiny zabezpečení sítě (NSG) virtuálních počítačů, na kterých běží webové aplikace, s pravidly NSG, která jsou přesná, s ohledem na porty webových aplikací.<br>(Související zásady: pravidla skupin zabezpečení sítě pro webové aplikace v IaaS by měla být Posílená.)|Vysoká|N|Virtuální počítač|
|**Zásady zabezpečení pod by měly být definované tak, aby se snížily vektor útoku odebráním zbytečných aplikačních oprávnění (Preview).**|Definováním zásad zabezpečení pod můžete omezit vektor útoku odebráním zbytečných oprávnění aplikace. Doporučuje se nakonfigurovat zásady zabezpečení pod tím, že lusky mají přístup jenom k prostředkům, ke kterým mají přístup.<br>(Související zásady: [Preview]: v Kubernetes službách by se měly definovat zásady zabezpečení.)|Střední|N|Výpočetní prostředky (kontejnery)|
|**Nainstalovat Azure Security Center pro modul zabezpečení IoT a získat tak lepší přehled o zařízeních IoT**|Nainstalujte Azure Security Center pro modul zabezpečení IoT, abyste získali lepší přehled o zařízeních IoT.|Nízká|N|Zařízení IoT|

## <a name="next-steps"></a>Další kroky

Další informace o doporučeních najdete v následujících tématech:

- [Co jsou zásady zabezpečení, iniciativy a doporučení?](security-policy-concept.md)
- [Kontrola doporučení zabezpečení](security-center-recommendations.md)

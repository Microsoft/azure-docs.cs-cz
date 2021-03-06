---
title: Referenční tabulka pro všechna Azure Security Center doporučení
description: V tomto článku jsou uvedená doporučení zabezpečení Azure Security Center, která vám pomůžou posílit a chránit vaše prostředky.
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 04/06/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: e994aead1840fd3ef9b57e92cf95e94837608d7a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719128"
---
# <a name="security-recommendations---a-reference-guide"></a>Doporučení k zabezpečení – Referenční příručka

V tomto článku jsou uvedena doporučení, která se mohou zobrazit v Azure Security Center. Doporučení zobrazená ve vašem prostředí závisí na prostředcích, které chráníte, a na vašich vlastních konfiguracích.

Doporučení pro Security Center jsou založená na [testu zabezpečení Azure](https://docs.microsoft.com/security/benchmark/azure/introduction).
Srovnávací test zabezpečení Azure je Microsoftem vytvořená sada pokynů pro zabezpečení a osvědčené postupy na základě běžných architektur dodržování předpisů, které jsou specifické pro Azure. Toto široce uznávané sestavování srovnávacích testů v ovládacích prvcích z [centra pro Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) a [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) se soustředí na zabezpečení zaměřené na Cloud.

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

## <a name="iot-recommendations"></a><a name='recs-iot'></a>Doporučení IoT

[!INCLUDE [asc-recs-iot](../../includes/asc-recs-iot.md)]

## <a name="networking-recommendations"></a><a name='recs-networking'></a>Doporučení pro sítě

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>Zastaralá doporučení

|Doporučení|Popis & souvisejících zásad|Závažnost|
|----|----|----|
|Přístup k App Services by měl být omezený.|Omezte přístup k vašemu App Services změnou konfigurace sítě tak, aby odepřela příchozí provoz z rozsahů, které jsou příliš široké.<br>(Související zásady: [Preview]: přístup k App Services by měl být omezený)|Vysoká|
|Pravidla pro webové aplikace v IaaS skupin zabezpečení sítě by se měla posílit.|Posílit zabezpečení skupiny zabezpečení sítě (NSG) virtuálních počítačů, na kterých běží webové aplikace, s pravidly NSG, která jsou přesná, s ohledem na porty webových aplikací.<br>(Související zásady: pravidla skupin zabezpečení sítě pro webové aplikace v IaaS by měla být Posílená.)|Vysoká|
|Zásady zabezpečení pod by měly být definované tak, aby se snížily vektor útoku odebráním zbytečných aplikačních oprávnění (Preview).|Definováním zásad zabezpečení pod můžete omezit vektor útoku odebráním zbytečných oprávnění aplikace. Doporučuje se nakonfigurovat zásady zabezpečení pod tím, že lusky mají přístup jenom k prostředkům, ke kterým mají přístup.<br>(Související zásady: [Preview]: v Kubernetes službách by se měly definovat zásady zabezpečení.)|Střední|
|Nainstalovat Azure Security Center pro modul zabezpečení IoT a získat tak lepší přehled o zařízeních IoT|Nainstalujte Azure Security Center pro modul zabezpečení IoT, abyste získali lepší přehled o zařízeních IoT.|Nízká|
|Počítače by měly být restartovány, aby bylo možné použít aktualizace systému|Restartujte počítače, aby se projevily aktualizace systému, a zabezpečte počítač před ohrožením zabezpečení. (Související zásady: aktualizace systému by se měly nainstalovat na vaše počítače)|Střední|
|Agent monitorování by měl být nainstalovaný na vašich počítačích.|Tato akce nainstaluje agenta monitorování na vybrané virtuální počítače. Vyberte pracovní prostor, do kterého má agent nahlásit. (Žádné související zásady)|Vysoká|
||||

## <a name="next-steps"></a>Další kroky

Další informace o doporučeních najdete v následujících tématech:

- [Co jsou zásady zabezpečení, iniciativy a doporučení?](security-policy-concept.md)
- [Kontrola doporučení zabezpečení](security-center-recommendations.md)

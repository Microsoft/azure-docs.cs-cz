---
title: Přehled skupin zabezpečení aplikací Azure
titlesuffix: Azure Virtual Network
description: Seznamte se s používáním skupin zabezpečení aplikací.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 4d8ffe8451b2b2a08ab30761eaf3a928b5e117b3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99537624"
---
# <a name="application-security-groups"></a>Skupiny zabezpečení aplikací

Skupiny zabezpečení aplikací umožňují konfigurovat zabezpečení sítě jako přirozené rozšíření struktury aplikace. Můžete seskupovat virtuální počítače a na základě těchto skupin definovat zásady zabezpečení sítě. Zásady zabezpečení můžete opakovaně používat ve velkém měřítku bez potřeby ruční údržby explicitních IP adres. O složitost explicitních IP adres a několika skupin pravidel se stará platforma a vy se tak můžete zaměřit na obchodní logiku. Pro lepší pochopení skupin zabezpečení aplikací si představte následující příklad:

![Skupiny zabezpečení aplikací](./media/security-groups/application-security-groups.png)

Na předchozím obrázku jsou *NIC1* a *NIC2* členy skupiny zabezpečení aplikace *AsgWeb*. *NIC3* je členem skupiny zabezpečení aplikace *AsgLogic*. *NIC4* je členem skupiny zabezpečení aplikace *AsgDb*. I když je každé síťové rozhraní v tomto příkladu členem jenom jedné skupiny zabezpečení sítě, může být síťové rozhraní členem několika skupin zabezpečení aplikací, a to až do [omezení Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Žádné ze síťových rozhraní nemá přidruženou skupinu zabezpečení sítě. Skupina *NSG1* je přidružená k oběma podsítím a obsahuje následující pravidla:

## <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

Toto pravidlo je potřeba k povolení provozu směřujícího z internetu na webové servery. Vzhledem k tomu, že výchozí pravidlo zabezpečení **DenyAllInbound** odepírá příchozí provoz z internetu, není pro skupiny zabezpečení aplikací *AsgLogic* a *AsgDb* potřeba žádné další pravidlo.

|Priorita|Zdroj|Zdrojové porty| Cíl | Cílové porty | Protocol (Protokol) | Access |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | Povolit |

## <a name="deny-database-all"></a>Deny-Database-All

Vzhledem k tomu, že výchozí pravidlo zabezpečení **AllowVNetInBound** povoluje veškerou komunikaci mezi prostředky ve stejné virtuální síti, je toto pravidlo potřeba k odepření provozu ze všech prostředků.

|Priorita|Zdroj|Zdrojové porty| Cíl | Cílové porty | Protocol (Protokol) | Access |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | Všechny | Odepřít |

## <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

Toto pravidlo povoluje provoz ze skupiny zabezpečení aplikace *AsgLogic* do skupiny zabezpečení aplikace *AsgDb*. Priorita tohoto pravidla je vyšší než priorita pravidla *Deny-Database-All*. Díky tomu se toto pravidlo zpracuje před pravidlem *Deny-Database-All*, takže se povolí provoz ze skupiny zabezpečení aplikace *AsgLogic*, zatímco veškerý ostatní provoz se zablokuje.

|Priorita|Zdroj|Zdrojové porty| Cíl | Cílové porty | Protocol (Protokol) | Access |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | Povolit |

Pravidla určující skupinu zabezpečení aplikace jako zdroj nebo cíl se použijí pouze na síťová rozhraní, která jsou členy příslušné skupiny zabezpečení aplikace. Pokud síťové rozhraní není členem žádné skupiny zabezpečení aplikace, pravidlo se pro něj nepoužije, ani když je k podsíti přiřazená skupina zabezpečení sítě.

Pro skupiny zabezpečení aplikací platí následující omezení:

-    Počet skupin zabezpečení aplikací, které můžete mít v předplatném, je omezený. V souvislosti se skupinami zabezpečení aplikací platí také další omezení. Podrobnosti najdete v tématu věnovaném [omezením Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Jako zdroj a cíl můžete v pravidlu zabezpečení zadat jednu skupinu zabezpečení aplikace. Více skupin zabezpečení aplikací ve zdroji ani cíli zadat nemůžete.
- Všechna síťová rozhraní přiřazená ke skupině zabezpečení aplikace musí existovat ve stejné virtuální síti jako první síťové rozhraní přiřazené ke skupině zabezpečení aplikace. Pokud se například první síťové rozhraní přiřazené ke skupině zabezpečení aplikace *AsgWeb* nachází ve virtuální síti *VNet1*, pak všechna další síťová rozhraní přiřazená ke skupině zabezpečení aplikace *ASGWeb* musí existovat ve virtuální síti *VNet1*. Do stejné skupiny zabezpečení aplikace nemůžete přidat síťová rozhraní z různých virtuálních sítí.
- Pokud zadáte skupinu zabezpečení aplikací jako zdroj a cíl v pravidle zabezpečení, síťová rozhraní v obou skupinách zabezpečení aplikací musí existovat ve stejné virtuální síti. Kdyby například skupina *AsgLogic* obsahovala síťová rozhraní z virtuální sítě *VNet1* a skupina *AsgDb* obsahovala síťová rozhraní z virtuální sítě *VNet2*, nemohli byste v pravidle přiřadit skupinu *AsgLogic* jako zdroj a skupinu *AsgDb* jako cíl. Všechna síťová rozhraní pro zdrojové i cílové skupiny zabezpečení aplikací musí existovat ve stejné virtuální síti.

> [!TIP]
> Pokud chcete minimalizovat počet potřebných pravidel zabezpečení a nutnost jejich změn, naplánujte potřebné skupiny zabezpečení aplikací a vytvářejte pravidla s použitím značek služeb nebo skupin zabezpečení aplikací, a ne jednotlivých IP adres nebo rozsahů IP adres, pokud je to možné.

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak [vytvořit skupinu zabezpečení sítě](tutorial-filter-network-traffic.md).

---
title: Principy IDN ve službě Azure Stack | Dokumentace Microsoftu
description: Principy IDN funkce a možnosti v Azure stacku
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-09/28/2018started-article
ms.date: 09/28/2018
ms.author: mabrigg
ms.reviewer: scottnap
ms.openlocfilehash: 783262a5b55bd645ae3b85c1f00434648d7ee35f
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2018
ms.locfileid: "47584963"
---
# <a name="introducing-idns-for-azure-stack"></a>Představení iDNS pro Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

iDNS je síťové funkce služby Azure Stack, která umožňuje překládat externí názvy DNS (například http://www.bing.com.) také umožňuje registraci názvů interní virtuální síti. Díky tomu lze vyřešit virtuální počítače ve stejné virtuální síti name spíše než IP adresu. Tento přístup eliminuje nutnost poskytnout vlastní položky serveru DNS. Další informace o DNS, najdete v článku [přehled DNS Azure](https://docs.microsoft.com/azure/dns/dns-overview).

## <a name="what-does-idns-do"></a>Co dělá IDN?

S IDN ve službě Azure Stack získáte následující funkce, aniž byste museli zadat vlastní položky serveru DNS:

- Sdílené služby překladu názvů DNS pro úlohy klientů.
- Autoritativní službu DNS pro překlad názvů a registraci DNS v rámci virtuální sítě tenanta.
- Služba rekurzivní DNS pro překlad názvů Internetu z klientské virtuální počítače. Klienti už nemusíte určit vlastní záznamy DNS pro překlad názvů v Internetu (třeba www.bing.com.)

Stále můžete přenést vlastní DNS a použít vlastní servery DNS. Ale pomocí IDN lze přeložit názvy DNS pro Internet a připojte se k jiné virtuální počítače ve stejné virtuální síti, nemusíte vytvářet vlastní záznamy DNS.

## <a name="what-doesnt-idns-do"></a>Co není IDN?

Jaké IDN neumožňuje musíte udělat, je vytvořit záznam DNS pro název, který lze vyřešit z mimo virtuální síť.

V Azure máte možnost zadat popisek názvu DNS, který je přidružený k veřejné IP adresy. Můžete zvolit popisek (předpona), ale Azure vybere příponu, která je založena na oblasti, ve kterém můžete vytvořit veřejnou IP adresu.

![Příklad popisku názvu DNS](media/azure-stack-understanding-dns-in-tp2/image3.png)

Jak ukazuje předchozí obrázek, Azure vytvoří záznam "A" v DNS pro popisek názvu DNS zadaná v zóně **westus.cloudapp.azure.com**. Předpona a přípona jsou zkombinované sestavit [plně kvalifikovaný název domény](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (plně kvalifikovaný název domény), který lze vyřešit z kdekoli na veřejném Internetu.

Azure Stack IDN podporuje pouze pro interní název registrace, proto nelze provést následující:

- Vytvořit záznam DNS v rámci existující hostované zóny DNS (například local.azurestack.external.)
- Vytvoření zóny DNS (například Contoso.com).
- Vytvoření záznamu v části vlastní zónu DNS.
- Podpora nákup názvů domén.

## <a name="next-steps"></a>Další postup

[Pomocí služby DNS v Azure stacku](azure-stack-dns.md)

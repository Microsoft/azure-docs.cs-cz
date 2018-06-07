---
title: Principy IDN v zásobníku Azure | Microsoft Docs
description: Seznámení s IDN funkce a možnosti v Azure zásobníku
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: scottnap
ms.openlocfilehash: 9123160f42adea57c28dff265bd5b5dbbcbb7918
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724254"
---
# <a name="introducing-idns-for-azure-stack"></a>Představení IDN pro Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

IDN je zásobník Azure síťové funkce, která umožňuje překládat externí názvy DNS (například http://www.bing.com.) můžete taky k registraci názvů interní virtuální síť. Díky tomu můžete vyřešit virtuální počítače ve stejné virtuální síti názvu a nikoli IP adresu. Tento přístup eliminuje nutnost zajistit vlastní položky serveru DNS. Další informace o službě DNS, najdete v článku [přehled Azure DNS](https://docs.microsoft.com/en-us/azure/dns/dns-overview).

## <a name="what-does-idns-do"></a>Jakým způsobem IDN?

S IDN v zásobníku Azure získáte následující možnosti, aniž byste museli zadat vlastní položky serveru DNS:

- Sdílené služby překladu názvů DNS pro úlohy klientů.
- Autoritativní služby DNS pro rozlišení názvů a registraci DNS v rámci virtuální sítě klienta.
- Služba DNS rekurzivní pro překlad názvů v síti Internet z klientské virtuální počítače. Klienti se již muset zadat vlastní záznamy DNS pro překlad názvů v Internetu (například www.bing.com.)

Můžete stále přineste si vlastní DNS a použít vlastní servery DNS. Ale pomocí IDN může překládat názvy DNS pro Internet a připojte se k jiné virtuální počítače ve stejné virtuální síti, nemusíte vytvářet vlastní položky DNS.

## <a name="what-doesnt-idns-do"></a>Co není IDN?

Jaké IDN neumožňuje musíte udělat, je vytvořit záznam DNS pro název, který lze vyřešit z mimo virtuální síť.

V Azure máte možnost zadat popisek názvu DNS, která souvisí s veřejnou IP adresu. Můžete zvolit popisek (předpona), ale Azure vybere příponu, která je založena na oblast, ve kterém vytvoříte veřejnou IP adresu.

![Příklad Popisek názvu DNS](media/azure-stack-understanding-dns-in-tp2/image3.png)

Jak ukazuje předchozí obrázek, Azure vytvoří záznam "A" ve službě DNS pro popisek názvu DNS, který je zadán v rámci zóny **westus.cloudapp.azure.com**. Předpona a přípona se zkombinují k vytváření [plně kvalifikovaný název domény](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN), dají se vyřešit z kdekoli na veřejného Internetu.

Azure zásobníku IDN podporuje pouze pro interní název registraci, proto nelze provést následující:

- Vytvořit záznam DNS v rámci stávající hostované zónu DNS (například local.azurestack.external.)
- Vytvoření zóny DNS (například Contoso.com).
- Vytvořte záznam v části vlastní zónu DNS.
- Podpora nákupu názvů domén.

## <a name="next-steps"></a>Další postup

[Pomocí DNS v Azure zásobníku](azure-stack-dns.md)

---
title: Typy útoků Azure DDoS Protection standardní zmírňující rizika
description: Seznamte se s typy útoků, ke kterým Azure DDoS Protection standardně chrání.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 8eac365fc5146f315db8f09a67960aa892fa1c85
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106050"
---
# <a name="types-of-ddos-attacks-overview"></a>Přehled typů útoků DDoS

DDoS Protection Standard může zmírnit následující typy útoků:

- Navýšení **útoků**: Tyto útoky zaplavou síťovou vrstvu se značným počtem zdánlivě legitimních přenosů. Mezi ně patří zaplavení UDP, zahlcení zesílení a další falešná zaplavování paketů. DDoS Protection Standard snižuje riziko těchto potenciálních útoků s více gigabajty tím, že je absorbuje a je bude používat globální škálování v síti Azure, a to automaticky.
- **Útoky protokolu**: Tyto útoky generují cíl nepřístupný, protože využívají slabiny vrstvy 3 a protokolu vrstvy 4. Zahrnují útoky na zaplavení SYN, útoky na reflexi a další útoky protokolu. DDoS Protection Standard tyto útoky omezuje, rozlišuje mezi škodlivým a oprávněným provozem interakci s klientem a blokuje škodlivý provoz. 
- **Útoky na vrstvy prostředků (aplikace)**: Tyto útoky cílí na pakety webových aplikací a přerušují přenos dat mezi hostiteli. Zahrnují porušení protokolu HTTP, vkládání SQL, skriptování mezi weby a další útoky na úrovni 7. Použijte bránu firewall webových aplikací, jako je například [Brána Firewall webových aplikací Azure Application Gateway](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), a také DDoS Protection standard pro zajištění ochrany proti těmto útokům. K dispozici jsou také nabídky firewallu webových aplikací třetích stran dostupné v [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

## <a name="azure-ddos-protection-standard"></a>Azure DDoS Protection Standard

DDoS Protection Standard chrání prostředky ve virtuální síti, včetně veřejných IP adres přidružených k virtuálním počítačům, nástrojům pro vyrovnávání zatížení a aplikačním branám. V případě, že je propojena s bránou firewall webových aplikací Application Gateway nebo se službou Firewall webových aplikací třetí strany nasazená ve virtuální síti s veřejnou IP adresou, DDoS Protection Standard může poskytnout plnou vrstvu 3 schopnost omezit omezení vrstvy 7.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit plán DDoS Protection](manage-ddos-protection.md).
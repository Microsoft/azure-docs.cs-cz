---
title: Azure DDoS Protection standardní časté otázky
description: Zjistěte, jak služba Azure DDoS Protection úrovně Standard v kombinaci s osvědčenými postupy návrhu aplikací poskytuje ochranu před útoky DDoS.
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: yitoh
ms.openlocfilehash: 0873705e105710873be5d024269d40deb1c85e2b
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94505423"
---
# <a name="azure-ddos-protection-standard-frequent-asked-questions"></a>Azure DDoS Protection standardní časté otázky

Tento článek obsahuje odpovědi na běžné dotazy týkající se Azure DDoS Protection Standard. 

## <a name="what-is-a-distributed-denial-of-service-ddos-attack"></a>Co je útok distribuovaných útoků (DDoS)?
Distributed Denial of Service nebo DDoS je typ útoku, kdy útočník odesílá více požadavků na aplikaci, než je aplikace schopná zpracovat. Výsledný dopad je prostředky, které jsou vyčerpány, což ovlivňuje dostupnost aplikace a možnost provozovat své zákazníky. Během posledních několika let se v oboru ukázala prudká zvýšení útoků, ale útoky se v nich stanou sofistikovanější a větší. Útoky DDoS můžou být cílené na libovolný koncový bod, který je veřejně dosažitelný prostřednictvím Internetu.

## <a name="what-is-azure-ddos-protection-standard-service"></a>Co je služba Azure DDoS Protection Standard?
Azure DDoS Protection Standard, v kombinaci s osvědčenými postupy návrhu aplikací, poskytuje rozšířené funkce zmírnění DDoS, které chrání před útoky DDoS. Automaticky se vyladěna tak, aby chránila vaše konkrétní prostředky Azure ve virtuální síti. Ochranu je snadné povolit v jakékoli nové nebo existující virtuální síti a nevyžaduje žádné změny aplikací nebo prostředků. Má několik výhod oproti základní službě, včetně protokolování, upozorňování a telemetrie. Viz [přehled Azure DDoS Protection Standard](ddos-protection-overview.md). 

## <a name="what-about-protection-at-the-service-layer-layer-7"></a>Co je ochrana ve vrstvě služeb (vrstva 7)?
Zákazníci mohou používat službu Azure DDoS Protection v kombinaci s [Application Gateway WAF SKU](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview) k ochraně jak v síťové vrstvě (vrstva 3 a 4, kterou nabízí Azure DDoS Protection služba), tak i na vrstvě aplikace (vrstva 7, kterou nabízí Application Gateway SKU WAF).

## <a name="are-services-unsafe-in-azure-without-the-service"></a>Jsou služby v Azure nebezpečné bez služby?
Služby běžící v Azure jsou v podstatě chráněné Azure DDoS Protection Basic, která je na místě pro ochranu infrastruktury Azure. Ochrana, která chrání infrastrukturu, ale má mnohem vyšší prahovou hodnotu, než většina aplikací má kapacitu zvládnout a neposkytuje telemetrii ani výstrahy, takže když je objem provozu na platformě neškodný, může být devastating k aplikaci, která ji přijímá. 

Při připojování ke službě Azure DDoS Protection Standard získá aplikace vyhrazené monitorování, které odhalí útoky a prahové hodnoty specifické pro aplikaci. Služba bude chráněná pomocí profilu, který je vyladěný na očekávaný objem provozu a zajišťuje tak mnohem užší ochranu před DDoS útoky.

## <a name="what-are-the-supported-protected-resource-types"></a>Jaké jsou podporované typy chráněných prostředků?
Veřejné IP adresy v virtuální sítě založených na ARM jsou aktuálně jediným typem chráněného prostředku. PaaS Services (víceklientské) nejsou v předložených. Viz [referenční architektury Azure DDoS Protection Standard](ddos-protection-reference-architectures.md).

## <a name="are-classicrdfe-protected-resources-supported"></a>Jsou podporovány prostředky pro klasický/RDFE Protected?
Ve verzi Preview se podporují jenom chráněné prostředky založené na ARM. Virtuální počítače v nasazeních Classic a RDFE se nepodporují. Podpora není aktuálně plánována pro prostředky Classic a RDFE. Viz [referenční architektury Azure DDoS Protection Standard](ddos-protection-reference-architectures.md).

## <a name="can-i-protect-my-on-premise-resources-using-ddos-protection"></a>Můžu chránit místní prostředky pomocí DDoS Protection?
Aby bylo možné povolit ochranu DDoS, musíte mít veřejné koncové body vaší služby přidružené k virtuální síti v Azure. Příklady návrhů zahrnují:
- Weby (IaaS) v Azure a back-endové databáze v datovém centru v Prem. 
- Application Gateway v Azure (DDoS Protection povolena v App Gateway/WAF) a weby v datových centrech Prem.

Viz [referenční architektury Azure DDoS Protection Standard](ddos-protection-reference-architectures.md).

## <a name="can-i-register-a-domain-outside-of-azure-and-associate-that-to-a-protected-resource-like-vm-or-elb"></a>Můžu zaregistrovat doménu mimo Azure a přidružit ji k chráněnému prostředku, jako je třeba virtuální počítač nebo ELB?
Pro scénáře veřejné IP adresy bude DDoS Protection služba podporovat jakoukoli aplikaci bez ohledu na to, kde je přidružená doména registrovaná nebo hostovaná, dokud je přidružená veřejná IP adresa hostovaná v Azure. 

## <a name="can-i-manually-configure-the-ddos-policy-applied-to-the-vnetspublic-ips"></a>Můžu ručně nakonfigurovat zásady DDoS použité pro virtuální sítě/veřejné IP adresy?
Ne, přizpůsobení zásad není v tuto chvíli k dispozici.

## <a name="can-i-allowlistbloclist-specific-ip-addresses"></a>Můžu povolených/bloclist konkrétní IP adresy?
Ne, ruční konfigurace není momentálně k dispozici.

## <a name="how-can-i-test-ddos-protection"></a>Jak můžu testovat DDoS Protection?
Viz [testování prostřednictvím simulací](test-through-simulations.md).

## <a name="how-long-does-it-take-for-the-metrics-to-load-on-portal"></a>Jak dlouho trvá, než se metriky načtou na portálu?
Metriky by se měly zobrazovat na portálu během 5 minut. Pokud je váš prostředek napadený, začnou se na portálu během 5-7 minut zobrazovat další metriky. 
    




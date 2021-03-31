---
title: Azure DDoS Protection standardní časté otázky
description: Nejčastější dotazy týkající se Azure DDoS Protection Standard, která pomáhá zajistit obranu před útoky DDoS.
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
ms.openlocfilehash: 89685d1b8c3a57fa142bbbfd1114f9aa0ff0c400
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98611113"
---
# <a name="azure-ddos-protection-standard-frequent-asked-questions"></a>Azure DDoS Protection standardní časté otázky

Tento článek obsahuje odpovědi na běžné dotazy týkající se Azure DDoS Protection Standard. 

## <a name="what-is-a-distributed-denial-of-service-ddos-attack"></a>Co je útok distribuovaných útoků (DDoS)?
Distributed Denial of Service nebo DDoS je typ útoku, kdy útočník odesílá více požadavků na aplikaci, než je aplikace schopná zpracovat. Výsledný dopad je prostředky, které jsou vyčerpány, což ovlivňuje dostupnost aplikace a možnost provozovat své zákazníky. Během posledních několika let se v oboru ukázala prudká zvýšení útoků, ale útoky se v nich stanou sofistikovanější a větší. Útoky DDoS můžou být cílené na libovolný koncový bod, který je veřejně dosažitelný prostřednictvím Internetu.

## <a name="what-is-azure-ddos-protection-standard-service"></a>Co je služba Azure DDoS Protection Standard?
Azure DDoS Protection Standard, v kombinaci s osvědčenými postupy návrhu aplikací, poskytuje rozšířené funkce zmírnění DDoS, které chrání před útoky DDoS. Automaticky se vyladěna tak, aby chránila vaše konkrétní prostředky Azure ve virtuální síti. Ochranu je snadné povolit v jakékoli nové nebo existující virtuální síti a nevyžaduje žádné změny aplikací nebo prostředků. Má několik výhod oproti základní službě, včetně protokolování, upozorňování a telemetrie. Další podrobnosti najdete v tématu [Přehled služby Azure DDoS Protection Standard](ddos-protection-overview.md) . 

## <a name="how-does-pricing-work"></a>Jak je to s cenami?
Plány DDoS Protection mají pevnou měsíční poplatek $2 944 za měsíc, který pokrývá až 100 veřejných IP adres. Ochrana dalších prostředků bude za každý prostředek měsíčně vypůjčit dalších $30. 

V rámci tenanta se dá jeden plán DDoS Protection použít pro několik předplatných, takže nemusíte vytvářet více než jeden plán DDoS Protection.

Další podrobnosti najdete v článku o [cenách Azure DDoS Protection Standard](https://azure.microsoft.com/pricing/details/ddos-protection/) .

## <a name="is-the-service-zone-resilient"></a>Je zóna služeb odolná?
Ano. Ve výchozím nastavení je Azure DDoS Protection odolný proti zóně.

## <a name="how-do-i-configure-the-service-to-be-zone-resilient"></a>Návody nakonfigurovat službu tak, aby byla odolná proti zóně?
Pro povolení odolnosti zóny není nutná žádná konfigurace zákazníka. V zóně – odolnost pro Azure DDoS Protection prostředky je ve výchozím nastavení dostupná a spravovaná samotnými službami.

## <a name="what-about-protection-at-the-service-layer-layer-7"></a>Co je ochrana ve vrstvě služeb (vrstva 7)?
Zákazníci můžou používat službu Azure DDoS Protection v kombinaci s firewallem webových aplikací (WAF) k ochraně jak v síťové vrstvě (vrstva 3 a 4, nabízená Azure DDoS Protection Standard), tak i na vrstvě aplikace (vrstva 7, kterou nabízí WAF). Nabídky WAF zahrnují SKU Azure [Application Gateway WAF](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a také nabídky firewallu webových aplikací třetích stran, které jsou k dispozici v [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

## <a name="are-services-unsafe-in-azure-without-the-service"></a>Jsou služby v Azure nebezpečné bez služby?
Služby běžící v Azure jsou v podstatě chráněné Azure DDoS Protection Basic, která je na místě pro ochranu infrastruktury Azure. Ochrana, která chrání infrastrukturu, ale má mnohem vyšší prahovou hodnotu, než většina aplikací má kapacitu zvládnout a neposkytuje telemetrii ani výstrahy, takže když je objem provozu na platformě neškodný, může být devastating k aplikaci, která ji přijímá. 

Při připojování ke službě Azure DDoS Protection Standard získá aplikace vyhrazené monitorování, které odhalí útoky a prahové hodnoty specifické pro aplikaci. Služba bude chráněná pomocí profilu, který je vyladěný na očekávaný objem provozu a zajišťuje tak mnohem užší ochranu před DDoS útoky.

## <a name="what-are-the-supported-protected-resource-types"></a>Jaké jsou podporované typy chráněných prostředků?
Veřejné IP adresy v virtuální sítě založených na ARM jsou aktuálně jediným typem chráněného prostředku. PaaS Services (víceklientské) nejsou v předložených. Další podrobnosti najdete v tématu [Azure DDoS Protection standardní referenční architektury](ddos-protection-reference-architectures.md) .

## <a name="are-classicrdfe-protected-resources-supported"></a>Jsou podporovány prostředky pro klasický/RDFE Protected?
Ve verzi Preview se podporují jenom chráněné prostředky založené na ARM. Virtuální počítače v nasazeních Classic a RDFE se nepodporují. Podpora není aktuálně plánována pro prostředky Classic a RDFE. Další podrobnosti najdete v tématu [Azure DDoS Protection standardní referenční architektury](ddos-protection-reference-architectures.md) .

## <a name="can-i-protect-my-paas-resources-using-ddos-protection"></a>Můžu chránit svoje PaaS prostředky pomocí DDoS Protection?
Veřejné IP adresy připojené k víceklientské architektuře – jedna VIP PaaS Services se v současné době nepodporují. Mezi nepodporované prostředky patří virtuální IP adresy úložiště, virtuální IP adresy centra událostí a aplikace a aplikace Cloud Services. Další podrobnosti najdete v tématu [Azure DDoS Protection standardní referenční architektury](ddos-protection-reference-architectures.md) .

## <a name="can-i-protect-my-on-premise-resources-using-ddos-protection"></a>Můžu chránit místní prostředky pomocí DDoS Protection?
Aby bylo možné povolit ochranu DDoS, musíte mít veřejné koncové body vaší služby přidružené k virtuální síti v Azure. Příklady návrhů zahrnují:
- Weby (IaaS) v Azure a back-endové databáze v datovém centru v Prem. 
- Application Gateway v Azure (DDoS Protection povolena v App Gateway/WAF) a weby v datových centrech Prem.

Další podrobnosti najdete v tématu [Azure DDoS Protection standardní referenční architektury](ddos-protection-reference-architectures.md) .

## <a name="can-i-register-a-domain-outside-of-azure-and-associate-that-to-a-protected-resource-like-vm-or-elb"></a>Můžu zaregistrovat doménu mimo Azure a přidružit ji k chráněnému prostředku, jako je třeba virtuální počítač nebo ELB?
Pro scénáře veřejné IP adresy bude DDoS Protection služba podporovat jakoukoli aplikaci bez ohledu na to, kde je přidružená doména registrovaná nebo hostovaná, dokud je přidružená veřejná IP adresa hostovaná v Azure. 

## <a name="can-i-manually-configure-the-ddos-policy-applied-to-the-vnetspublic-ips"></a>Můžu ručně nakonfigurovat zásady DDoS použité pro virtuální sítě/veřejné IP adresy?
Ne, v tuto chvíli není k dispozici vlastní nastavení zásad.

## <a name="can-i-allowlistblocklist-specific-ip-addresses"></a>Můžu povolených/seznamu blokovaných konkrétní IP adresy?
Ne, bohužel není v tuto chvíli k dispozici ruční konfigurace.

## <a name="how-can-i-test-ddos-protection"></a>Jak můžu testovat DDoS Protection?
Viz [testování prostřednictvím simulací](test-through-simulations.md).

## <a name="how-long-does-it-take-for-the-metrics-to-load-on-portal"></a>Jak dlouho trvá, než se metriky načtou na portálu?
Metriky by se měly zobrazovat na portálu během 5 minut. Pokud je váš prostředek napadený, začnou se na portálu během 5-7 minut zobrazovat další metriky. 

## <a name="does-the-service-store-customer-data"></a>Ukládá služba data o zákaznících?
Ne, Azure DDoS Protection neukládá zákaznická data.
    

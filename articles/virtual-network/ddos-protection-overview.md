---
title: Přehled Azure DDoS Protection Standard
description: Přečtěte si, jak Azure DDoS Protection Standard, v kombinaci s osvědčenými postupy návrhu aplikací, zajišťuje ochranu před útoky DDoS.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: kumud
ms.openlocfilehash: 50b0324be8da8024dcbda140eff062f3ff712225
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91371180"
---
# <a name="azure-ddos-protection-standard-overview"></a>Přehled Azure DDoS Protection Standard

Distribuované útoky na dostupnost služeb (DDoS) patří k největším hrozbám pro dostupnost a zabezpečení, se kterými se musejí zákazníci přesouvající aplikace do cloudu vyrovnávat. Útok DDoS se pokusí vyčerpat prostředky aplikace, takže aplikace nebude k dispozici oprávněným uživatelům. Útoky DDoS můžou cílit na jakýkoli koncový bod, který je veřejně dostupný přes internet.

Ochrana Azure DDoS v kombinaci s osvědčenými postupy návrhu aplikací zajišťuje ochranu před útoky DDoS. Každá vlastnost v Azure je chráněná ochranou DDoS (Basic) infrastruktury Azure.Azure DDoS Protection Standard poskytuje další funkce pro zmírnění rizik oproti základní úrovni služeb, které jsou vyladěné konkrétně na prostředky Azure Virtual Network. 

DDoS Protection Standard se snadno povoluje a nevyžaduje žádné změny aplikací. K ladění zásad ochrany slouží vyhrazené monitorování provozu a algoritmy strojového učení. Zásady se použijí na veřejné IP adresy přidružené k prostředkům nasazeným ve virtuálních sítích, jako jsou Azure Load Balancer, Azure Application Gateway a instance služby Azure Service Fabric, ale tato ochrana se nevztahuje na App Service prostředí.Telemetrie v reálném čase jsou k dispozici prostřednictvím Azure Monitor zobrazení během útoku a pro historii. K dispozici je rozsáhlé analýzy zmírnění útoků prostřednictvím nastavení diagnostiky. Ochranu aplikační vrstvy lze přidat prostřednictvím [brány firewall webových aplikací Azure Application Gateway](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo instalací brány firewall od jiného výrobce z Azure Marketplace. Ochrana je k dispozici pro [veřejné IP adresy](virtual-network-public-ip-address.md)IPv4 a IPv6.

![DDoS Protection Basic vs Standard](./media/ddos-protection-overview/ddoscomparison.png)

Azure DDoS Protection neukládá zákaznická data.

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Typy útoků DDoS, které DDoS Protection standardní zmírňující rizika

DDoS Protection Standard může zmírnit následující typy útoků:

- Navýšení **útoků**: Tyto útoky zaplavou síťovou vrstvu se značným počtem zdánlivě legitimních přenosů. Mezi ně patří zaplavení UDP, zahlcení zesílení a další falešná zaplavování paketů. DDoS Protection Standard snižuje riziko těchto potenciálních útoků s více gigabajty tím, že je absorbuje a je bude používat globální škálování v síti Azure, a to automaticky.
- **Útoky protokolu**: Tyto útoky generují cíl nepřístupný, protože využívají slabiny vrstvy 3 a protokolu vrstvy 4. Zahrnují útoky na zaplavení SYN, útoky na reflexi a další útoky protokolu. DDoS Protection Standard tyto útoky omezuje, rozlišuje mezi škodlivým a oprávněným provozem interakci s klientem a blokuje škodlivý provoz. 
- **Útoky na vrstvy prostředků (aplikace)**: Tyto útoky cílí na pakety webových aplikací a přerušují přenos dat mezi hostiteli. Zahrnují porušení protokolu HTTP, vkládání SQL, skriptování mezi weby a další útoky na úrovni 7. Použijte bránu firewall webových aplikací, jako je například [Brána Firewall webových aplikací Azure Application Gateway](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), a také DDoS Protection standard pro zajištění ochrany proti těmto útokům. K dispozici jsou také nabídky firewallu webových aplikací třetích stran dostupné v [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

DDoS Protection Standard chrání prostředky ve virtuální síti, včetně veřejných IP adres přidružených k virtuálním počítačům, nástrojům pro vyrovnávání zatížení a aplikačním branám. V případě, že je propojena s bránou firewall webových aplikací Application Gateway nebo se službou Firewall webových aplikací třetí strany nasazená ve virtuální síti s veřejnou IP adresou, DDoS Protection Standard může poskytnout plnou vrstvu 3 schopnost omezit omezení vrstvy 7.

## <a name="ddos-protection-standard-features"></a>DDoS Protection standardní funkce

![Funkce DDoS](./media/ddos-protection-overview/ddosfeatures.png)

Mezi DDoS Protection standardní funkce patří:

- **Integrace nativní platformy:** Nativně integrovaná do Azure. Zahrnuje konfiguraci prostřednictvím Azure Portal. DDoS Protection Standard rozumí vašim prostředkům a konfiguraci prostředků.
- **Ochrana klíč:** Zjednodušená konfigurace okamžitě chrání všechny prostředky ve virtuální síti, jakmile je povolená DDoS Protection Standard. Není nutná žádná definice ani zásah uživatele. DDoS Protection Standard okamžitě a automaticky zmírnit útok, jakmile se zjistí.
- **Monitorování nepřetržitého provozu:** Vaše vzory přenosů aplikací jsou monitorované 24 hodin denně, 7 dní v týdnu a hledají indikátory DDoSch útoků. Při překročení zásad ochrany dochází ke zmírnění rizik.
- **Adaptivní ladění:** Inteligentní profilace provozu zjišťuje provoz vaší aplikace v průběhu času a vybere a aktualizuje profil, který je nejvhodnější pro vaši službu. Profil se v průběhu času upraví jako změny provozu.
- **Víceúrovňová ochrana:** Poskytuje úplnou ochranu zásobníku DDoS při použití s bránou firewall webových aplikací.
- **Rozsáhlá škála rizik:** U více než 60 různých typů útoků se dá zmírnit s globální kapacitou, která se bude chránit před největším známými DDoS útoky.
- **Analýza útoků:** Získejte podrobné sestavy během útoku za 5 minut a kompletní souhrn po skončení útoku. Protokoly toku pro zmírnění rizik streamování do [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-ddos-protection) nebo do režimu Siem (Information Security and Event Management) pro téměř v reálném čase při útoku.
- **Metriky útoku:** Souhrnná metrika z každého útoku je přístupná prostřednictvím Azure Monitor.
- **Výstrahy útoku:** Výstrahy se dají nakonfigurovat na začátku a na konci útoku a přes dobu trvání útoku pomocí integrované metriky útoku. Výstrahy se integrují do svého provozního softwaru, jako je Microsoft Azure monitorování protokolů, Splunk, Azure Storage, e-mailu a Azure Portal.
- **Záruka nákladů:** Kredity služeb pro přenos dat a škálování aplikací pro dokumentované DDoS útoky.

## <a name="ddos-protection-standard-mitigation"></a>DDoS Protection zmírnění úrovně Standard

DDoS Protection Standard monitoruje skutečné využití provozu a trvale ho porovnává s mezními hodnotami definovanými v zásadách DDoS. Při překročení prahové hodnoty přenosu se DDoSé riziko iniciuje automaticky. Když se provoz vrátí pod prahovou hodnotu, zmírnění se odstraní.

![Diagram znázorňuje tok od zákazníka přes Azure Portal prostřednictvím standardu DDoS Protection, přes veřejné I P adresy prostřednictvím generování zásad, končící na dvou veřejných I P adresách.](./media/ddos-protection-overview/mitigation.png)

Během zmírnění ochrany se provoz odeslaný do chráněného prostředku přesměruje pomocí služby DDoS Protection a provede se několik kontrol, například následující kontroly:

- Zajistěte, aby pakety odpovídaly specifikacím Internetu a nejsou poškozeny.
- Interakci s klientem, aby bylo možné zjistit, zda je přenos potenciálně falešným paketem (například soubor. cookie SYN nebo souborů cookie SYN nebo vyřazením paketu pro zdroj, který ho znovu odešle).
- Pakety s omezením četnosti, pokud není možné provést žádnou jinou metodu vynucení.

Ochrana před útoky DDoS blokuje provoz útoku a zbývající provoz přesměrovává do zamýšleného cíle. Během několika minut od detekce útoku budete upozorněni pomocí metrik Azure Monitoru. Konfigurací protokolování na DDoS Protection standardní telemetrie můžete protokoly zapsat na dostupné možnosti pro budoucí analýzu. Data metriky v Azure Monitor pro DDoS Protection Standard se uchovávají po dobu 30 dnů.

Microsoft spolupracuje s [cloudem BreakingPoint](https://www.ixiacom.com/products/breakingpoint-cloud) a vytváří rozhraní, ve kterém můžete vygenerovat provoz proti DDoS Protection veřejné IP adresy pro simulace. Simulace cloudu zarážky umožňuje:

- Ověření, jak Microsoft Azure DDoS Protection úrovně Standard chrání vaše prostředky Azure před útoky DDoS
- Optimalizace procesu reakce na incidenty při útoku DDoS
- DDoS dodržování předpisů v dokumentu
- Školení týmů zabezpečení sítě

## <a name="next-steps"></a>Další kroky

- [Konfigurace DDoS Protection Standard](manage-ddos-protection.md)
- [Ceny Azure DDoS Protection](https://azure.microsoft.com/pricing/details/ddos-protection/)

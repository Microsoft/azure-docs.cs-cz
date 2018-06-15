---
title: Standardní Přehled ochrany Azure DDoS | Microsoft Docs
description: Další informace o službě Azure ochrana proti útoku DDoS.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2018
ms.author: jdial
ms.openlocfilehash: 705f69f9143e3d2b27a3099f340218aaa12931f8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31601895"
---
# <a name="azure-ddos-protection-standard-overview"></a>Přehled služby Azure DDoS ochrany standardní

Distribuované útoků DoS (Denial) jsou některé největší dostupnost a zabezpečení obavy směřující zákazníky, kteří jsou přesunutí svých aplikací do cloudu. Útoku DDoS pokusí vyčerpat prostředky aplikace, provedení aplikace není k dispozici na oprávněné uživatele. Útoky DDoS můžete cílit na všechny koncový bod, který je veřejně dostupný prostřednictvím Internetu.

Azure ochrana proti útoku DDoS v kombinaci s osvědčenými postupy návrhu aplikace ubrání proti útokům DDoS. Azure ochrana proti útoku DDos poskytuje následující úrovně služeb:

- **Základní**: automaticky povolené v rámci platformy Azure bez dalších poplatků. Monitorování vždy v provozu a v reálném čase zmírnění běžné útoků na úrovni sítě, zadejte stejné obrany využívaných online službách společnosti Microsoft. Celého rozsahu globální sítě Azure slouží k distribuci a zmírnit útok provoz v oblastech. Ochrana se poskytuje pro protokol IPv4 a IPv6 Azure [veřejné IP adresy](virtual-network-public-ip-address.md).
- **Standardní**: poskytuje možnosti Další zmírnění přes vrstvu základní služby, která jsou přizpůsobená speciálně pro prostředky Azure Virtual Network. DDoS ochrany standardní je jednoduchá, aby a nevyžaduje žádné změny aplikace. Zásady ochrany jsou přizpůsobená prostřednictvím monitorování vyhrazené provozu a algoritmů strojového učení. Zásady se použijí pro veřejné IP adresy přidružené k nasazené ve virtuálních sítích, jako je například nástroj pro vyrovnávání zatížení Azure, Azure Application Gateway a Azure Service Fabric instancí prostředky. Při útoku a historie je k dispozici prostřednictvím zobrazení monitorování Azure v reálném čase telemetrie. Ochrana vrstvy aplikace mohou být přidány prostřednictvím [brány Firewall webových aplikací Azure Application Gateway](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ochrana se poskytuje pro IPv4 Azure [veřejné IP adresy](virtual-network-public-ip-address.md).

![Standardní ochrany Azure DDoS](./media/ddos-protection-overview/ddospic.png)

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Typy útoků DDoS, které DDoS ochrany standardní snižuje

DDoS ochrany standardní zmírnit tyto typy útoků:

- **Odměrné útoky**: cílem útok je k vyplnění síťovou vrstvou s vyžadovat značné množství zdánlivě legitimní provoz. Obsahuje záplavy, záplavy zesílení a dalších záplavy maskování paketu UDP. DDoS ochrany standardní snižuje tyto možných útoků, které více gigabajt přijmout a čištění, škálování globální sítě Azure, automaticky.
- **Protokol útoky**: tyto útoky vykreslení cíl nepřístupné, zneužitím slabé místo v zásobníku protokolu vrstvy 4 a vrstvy 3. Obsahuje, útokům zahlcení SYN, reflexe útokům a jiným útokům protokolu. DDoS ochrany standardní snižuje tyto útoky rozlišování škodlivý a legitimní provozu v interakci s klientem, a blokovat škodlivý přenos. 
- **Útoky na prostředku (aplikace)**: tyto útoky cílové webové aplikace paketů narušit přenosu dat mezi hostiteli. Útoků zahrnují HTTP porušení protokolu, SQL vkládání, skriptování mezi servery a jiným útokům vrstvy 7. Použití Azure [brány firewall webových aplikací Application Gateway](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), s DDoS ochrany Standard zajistit ochranu proti těmto útokům. Existují také nabídky brány firewall třetích stran webové aplikace k dispozici v [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

DDoS ochrany standardní chrání prostředky ve virtuální síti, včetně veřejné IP adresy přidružené virtuální počítače, nástroje pro vyrovnávání zatížení a aplikačních bran. Při kombinaci s brány firewall webových aplikací Application Gateway, může DDoS ochrany standardní poskytnout úplné vrstvy 3 pro vrstvy 7 zmírnění schopnosti.

## <a name="ddos-protection-standard-features"></a>DDoS ochrany standardní funkce

![Funkce DDoS](./media/ddos-protection-overview/ddosfeatures.png)

DDoS ochrany standardní funkce patří:

- **Integrace nativní platformy:** nativně integrována do Azure. Zahrnuje konfiguraci prostřednictvím portálu Azure. DDoS ochrany standardní rozumí vašich prostředků a konfigurace prostředků.
- **Klíč ochrany:** zjednodušená konfigurace okamžitě chrání všechny prostředky ve virtuální síti, jakmile je povoleno DDoS ochrany standardní. Je vyžadována definice bez zásahu nebo uživatele. DDoS ochrany standardní okamžitě a automaticky snižuje útoku, jakmile bude zjištěno.
- **Monitorování vždy v provozu:** vzory přenosů dat vaší aplikace jsou monitorovány 24 hodin denně, 7 dní v týdnu, hledá indikátory útoků DDoS. Zmírnění dopadů se provádí při překročení zásady ochrany.
- **Adaptivní ladění:** inteligentního provoz profilace zjišťuje provoz vaší aplikace v průběhu času a vybere a aktualizuje profil, který je nejvhodnější pro vaši službu. Profil upraví jako provoz v průběhu času mění.
- **Úrovně 3 na ochrana vrstvy 7:** poskytuje úplné zásobníku ochrana proti útoku DDoS, pokud se používá s brány firewall webových aplikací.
- **Zmírnění dopadů rozsáhlé škálování:** přes 60 útoku různé typy lze zmírnit, s globální kapacity pro ochranu proti největší známé útoky DDoS.
- **Útokům metriky:** Summarized metriky z každé útoku jsou přístupné prostřednictvím Azure monitorování.
- **Útok výstrahy:** výstrah lze nakonfigurovat na spuštění a ukončení útoku a na dobu trvání útok, pomocí předdefinovaných útoku metriky. Výstrahy integrovat do vaší provozní softwaru, třeba Microsoft Azure Log Analytics, Splunk, Azure Storage, e-mailu a portálu Azure.
- **Cenově záruku:** přenosu dat a aplikací Škálováním na více systémů kompenzace zdokumentovaných útoku DDoS.

## <a name="ddos-protection-standard-mitigation"></a>Zmírnění dopadů DDoS ochrany standardní

DDoS ochrany standardní monitoruje provoz skutečné využití a porovná je neustále s definované v zásadách DDoS prahové hodnoty. Když je prahová hodnota provoz, je DDoS zmírnění iniciován automaticky. Pokud provoz vrátí pod prahovou hodnotou, odebere se zmírnění dopadů.

![Omezení rizik](./media/ddos-protection-overview/mitigation.png)

Během zmírnění dopadů data odesílaná do chráněného prostředku přesměrována ochrany službou DDoS a několik ověřování, jako je například následující kontroly:

- Zkontrolujte pakety specifikacím internet a nejsou poškozené.
- Komunikovat s klientem nástroje k určení, pokud provoz je potenciálně falešný paket (např: SYN Auth nebo soubor Cookie SYN nebo vyřazení paket pro zdrojů a přenést ho znovu).
- Limit rychlosti paketů, pokud žádnou jinou metodu vynucení lze provést.

Ochrana proti útoku DDoS blokuje přenos útoku a předá zbývající provoz do zamýšlené cíle. Během několika minut detekce útoku budete upozorněni, používání Azure monitorování metriky. Konfigurace protokolování na DDoS ochrany standardní telemetrie, můžete napsat protokoly dostupné možnosti pro budoucí analýzu. Metriky dat v Azure monitorování pro DDoS ochrany Standard se uchovávají po dobu 30 dnů.

Má spolupráci se společností Microsoft [BreakingPoint cloudu](https://www.ixiacom.com/products/breakingpoint-cloud) k sestavení rozhraní, kde můžete vytvářet přenosy dat s povoleným ochrana proti útoku DDoS veřejné IP adresy pro simulace. Simulace zarážek cloudu umožňuje:

- Ověření, jak Microsoft Azure DDoS ochrany standardní chrání vaše prostředky Azure před útoky DDoS
- Optimalizace váš proces reakcí na incidenty v rámci útoku DDoS
- Dodržování předpisů DDoS dokumentu
- Cvičení týmům zabezpečení sítě

## <a name="next-steps"></a>Další postup

- [Konfigurace ochrany Standard DDoS](manage-ddos-protection.md)
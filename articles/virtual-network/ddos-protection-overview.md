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
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: bb19baae1bc03633b7646fd2255ca910532595c5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="azure-ddos-protection-standard-overview"></a>Přehled služby Azure DDoS ochrany standardní

Distribuované útoků DoS (Denial) jsou jedním z největší dostupnost a zabezpečení obavy čelí zákazníkům přesunutí svých aplikací do cloudu. Útoku DDoS pokusí vyčerpat prostředky aplikace, provedení aplikace není k dispozici na oprávněné uživatele. Útoky DDoS můžete cílit na všechny koncový bod, který je veřejně dostupný prostřednictvím Internetu.

Azure ochrana proti útoku DDoS v kombinaci s osvědčenými postupy návrhu aplikace ubrání proti útokům DDoS. Azure ochrana proti útoku DDos poskytuje následující úrovně služeb: 

- **Azure DDoS ochrany základní**: automaticky povolené v rámci platformy Azure bez dalších poplatků. Vždy na provoz monitorování a v reálném čase zmírnění běžné útoků na úrovni sítě poskytuje stejné obrany využívaných online službách společnosti Microsoft. Celého rozsahu globální sítě Azure slouží k distribuci a zmírnit útok provoz v oblastech. Ochrana se poskytuje pro protokol IPv4 a IPv6 Azure [veřejné IP adresy](virtual-network-public-ip-address.md).
- **Azure DDoS ochrany standardní** poskytuje možnosti Další zmírnění přizpůsobená speciálně pro prostředky Azure Virtual Network. Se snadno povolit a nevyžaduje žádné změny aplikace. Zásady ochrany jsou přizpůsobená prostřednictvím monitorování vyhrazené provozu a machine learning algoritmy a použity na veřejné IP adresy přidružené k nasazené ve virtuálních sítích, jako je například nástroj pro vyrovnávání zatížení Azure, Azure Application Gateway a Azure prostředky. Instance Service Fabric. Při útoku a historie je k dispozici prostřednictvím zobrazení monitorování Azure v reálném čase telemetrie. Ochrana vrstvy aplikace mohou být přidány prostřednictvím [brány Firewall webových aplikací aplikace brány](https://azure.microsoft.com/services/application-gateway). Ochrana se poskytuje pro IPv4 Azure [veřejné IP adresy](virtual-network-public-ip-address.md). 

![Standardní ochrany Azure DDoS](./media/ddos-protection-overview/ddos-protection-overview-fig2.png)

> [!IMPORTANT]
> Azure DDoS ochrany standardní je aktuálně ve verzi preview. Ochrana se poskytuje pro jakýmikoli prostředky Azure, který má Azure veřejnou IP adresu přidružené k, jako jsou virtuální počítače, nástroje pro vyrovnávání zatížení a aplikačních bran. Budete muset [zaregistrovat](http://aka.ms/ddosprotection) pro službu, než můžete povolit standardní ochrany DDoS pro vaše předplatné. Po registraci, tým Azure DDoS kontaktuje jste a provede vás provede procesem povolování. DDoS ochrany Standard je k dispozici ve východní USA, Východ USA 2, západ USA, Západ střední USA, Severní Evropa, západní Evropa, Japonsko – Západ, Japonsko – východ, Asie – východ a jihovýchodní Asie oblasti. Verzi Preview se vám neúčtují poplatky za používání služby.

Doporučujeme vám opakujte DDoS ochrany standardní vývoj, testovací nebo produkční prostředí. Použijte v následujících zdrojích informací k poskytnutí zpětné vazby na vaše prostředí:
- [Ochrana Azure proti útoku DDoS ve fóru Microsoft Azure](https://feedback.azure.com/forums/905032-azure-ddos-protection). 
- [Ochrana Azure proti útoku DDoS ve fóru MSDN](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azureddosprotection)
- [Ochrana Azure proti útoku DDos na přetečení zásobníku](https://stackoverflow.com/tags/azure-ddos/info)

Pro problémy podpory můžete [otevřete lístek podpory Azure](../azure-supportability/how-to-create-azure-support-request.md). Při DDoS ochrany standardní je ve verzi preview, podpora je k dispozici na základě typu best effort.

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Typy útoků DDoS, které DDoS ochrany standardní snižuje

DDoS ochrany standardní zmírnit tyto typy útoků:

- **Odměrné útoky**: cílem útok je k vyplnění síťovou vrstvou s vyžadovat značné množství zdánlivě legitimní provoz. Obsahuje záplavy, záplavy zesílení a dalších záplavy maskování paketu UDP. DDoS ochrany standardní snižuje tyto možných útoků, které více gigabajt přijmout a čištění, využití Škálováním globální sítě Azure, automaticky. 
- **Protokol útoky**: tyto útoky vykreslení cíl nepřístupný zneužitím slabé místo v zásobníku protokolu vrstvy 4 a vrstvy 3. Obsahuje, útokům zahlcení SYN, reflexe útokům a jiným útokům protokolu. DDoS ochrany standardní snižuje tyto útoky rozlišování škodlivý a legitimní provozu v interakci s klienta a blokovat škodlivý přenos. 
- **Útoky na aplikace**: tyto útoky cílové webové aplikace pakety narušit přenosu dat mezi hostiteli. Obsahuje HTTP protokol narušení, SQL vkládání, skriptování a jiným útokům vrstvy 7. Použití Azure [brány firewall webových aplikací Application Gateway](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), s DDoS ochrany Standard zajistit ochranu proti těmto útokům. 

DDoS ochrany standardní chrání prostředky ve virtuální síti, včetně veřejné IP adresy přidružené virtuální počítače, nástroje pro vyrovnávání zatížení a aplikačních bran. Při kombinaci s brány firewall webových aplikací Application Gateway, může DDoS ochrany standardní poskytnout úplné vrstvy 3 pro vrstvy 7 zmírnění schopnosti.

## <a name="ddos-protection-standard-features"></a>DDoS ochrany standardní funkce

![Funkce DDoS](./media/ddos-protection-overview/ddos-overview-fig1.png)

DDoS ochrany standardní funkce patří: 

- **Integrace nativní platformy:** nativně integrována do Azure a zahrnuje konfiguraci prostřednictvím portálu Azure a prostředí PowerShell. DDoS ochrany standardní rozumí vašich prostředků a konfigurace prostředků.
- **Monitorování vždy v provozu:** vzory přenosů dat vaší aplikace jsou monitorovány 24 hodin denně, 7 dní v týdnu, hledá indikátory útoků DDoS. Zmírnění dopadů se provádí při překročení zásady ochrany.
- **Klíč ochrany:** zjednodušená konfigurace okamžitě chrání všechny prostředky ve virtuální síti, jakmile je povoleno DDoS ochrany standardní. Je vyžadována definice bez zásahu nebo uživatele. DDoS ochrany standardní okamžitě a automaticky snižuje útoku, jakmile bude zjištěno.
- **Adaptivní ladění:** inteligentního provoz profilace zjišťuje provoz vaší aplikace v průběhu času a vybere a aktualizuje profil, který je nejvhodnější pro vaši službu. Profil upraví jako provoz v průběhu času mění.
- **Úrovně 3 na ochrana vrstvy 7:** poskytuje úplné zásobníku ochrana proti útoku DDoS, při použití s aplikační brány.
- **Zmírnění dopadů rozsáhlé škálování:** přes 60 útoku různé typy lze zmírnit, s globální kapacity pro ochranu proti největší známé útoky DDoS. 
- **Útokům metriky:** Summarized metriky z každé útoku jsou přístupné prostřednictvím Azure monitorování.
- **Útok výstrahy:** výstrah lze nakonfigurovat na spuštění a ukončení útoku a na dobu trvání útok, pomocí předdefinovaných útoku metriky. Výstrahy integrovat do vaší provozní softwaru, třeba Microsoft Azure Log Analytics, Splunk, Azure Storage, e-mailu a portálu Azure.
- **Cenově záruku:** přenosu dat a aplikací Škálováním na více systémů kompenzace zdokumentovaných útoku DDoS.

## <a name="ddos-protection-standard-mitigation"></a>Zmírnění dopadů DDoS ochrany standardní

Ochrana proti útoku DDoS služby společnosti Microsoft monitoruje provoz skutečné využití a porovná je neustále s definované v zásadách DDoS prahové hodnoty. Při překročení prahové hodnoty provoz DDoS zmírnění iniciován automaticky. Pokud provoz vrátí pod prahovou hodnotou, odebere se zmírnění dopadů.

Během zmírnění dopadů data odesílaná do chráněného prostředku přesměrována službou ochrana proti útoku DDoS a několik ověřování. Těmito kontrolami obecně provádět následující funkce:

- Zkontrolujte pakety specifikacím Internet a nejsou poškozené.
- Komunikovat s klientem nástroje k určení, pokud provoz je potenciálně falešný paket (např: SYN Auth nebo soubor Cookie SYN nebo vyřazení paket pro zdrojů a přenést ho znovu).
- Limit rychlosti paketů, pokud žádnou jinou metodu vynucení lze provést.

Bloky ochrany DDoS útokům provozu a předává zbývající provoz do zamýšlené cíle. Během několika minut detekce útoku budete upozorněni, používání Azure monitorování metriky. Konfigurace protokolování na DDoS ochrany standardní telemetrie, můžete napsat protokoly dostupné možnosti pro budoucí analýzu. Metriky dat v Azure monitorování pro DDoS ochrany Standard se aktuálně uchovávají po dobu 30 dnů.

Nedoporučujeme zákazníků k simulaci vlastní útoky DDoS. Místo toho zákazníci mohou používat podporu kanál k vyžádání DDoS útokům simulace provedený sítí Azure. Technika vás bude kontaktovat uspořádání podrobnosti útoku DDoS (porty, protokoly, cílové IP adresy) a uspořádat dobu a naplánovat test.

## <a name="next-steps"></a>Další postup

- Další informace o správě DDoS ochrany standardní pomocí [prostředí Azure PowerShell](ddos-protection-manage-ps.md) nebo [portál Azure](ddos-protection-manage-portal.md).

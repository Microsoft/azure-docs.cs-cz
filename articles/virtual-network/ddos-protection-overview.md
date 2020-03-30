---
title: Přehled ochrany Azure DDoS
description: Další informace o službě Azure DDoS Protection.
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
ms.openlocfilehash: f1dd33425a57689974fc98a28724adf7b130ab40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536339"
---
# <a name="azure-ddos-protection-standard-overview"></a>Azure DDoS Protection Standard – přehled

Distribuované útoky na dostupnost služeb (DDoS) patří k největším hrozbám pro dostupnost a zabezpečení, se kterými se musejí zákazníci přesouvající aplikace do cloudu vyrovnávat. Útok DDoS se pokusí vyčerpat prostředky aplikace, takže aplikace není k dispozici oprávněným uživatelům. Útoky DDoS můžou cílit na jakýkoli koncový bod, který je veřejně dostupný přes internet.

Azure DDoS ochrana, v kombinaci s osvědčenými postupy návrhu aplikace, poskytují ochranu proti útokům DDoS. Ochrana Azure DDoS poskytuje následující úrovně služeb:

- **Základní**: Automaticky povoleno jako součást platformy Azure. Nepřetržité sledování provozu a zmírňování běžných útoků na úrovni sítě v reálném čase poskytuje stejnou obranu využívanou online službami společnosti Microsoft.Celé škálování globální sítě Azure lze použít k distribuci a zmírnění provozu útoků napříč oblastmi.Ochrana je k dispozici pro veřejné [IP adresy](virtual-network-public-ip-address.md)IPv4 a IPv6 Azure .
- **Standardní**: Poskytuje další možnosti zmírnění přes úroveň základní služby, které jsou naladěny speciálně na prostředky virtuální sítě Azure. DDoS Protection Standard je jednoduché povolit a nevyžaduje žádné změny aplikace. K ladění zásad ochrany slouží vyhrazené monitorování provozu a algoritmy strojového učení. Zásady se používají pro veřejné IP adresy přidružené k prostředkům nasazeným ve virtuálních sítích, jako je Azure Load Balancer, Azure Application Gateway a Azure Service Fabric instance, ale tato ochrana se nevztahuje na prostředí služby App Service.Telemetrie v reálném čase je k dispozici prostřednictvím zobrazení Azure Monitor během útoku a pro historii. Bohaté analýzy zmírnění útoků jsou k dispozici prostřednictvím diagnostických nastavení. Ochranu aplikačnívrstvy lze přidat prostřednictvím [brány firewall webové aplikace Azure Application Gateway](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo instalací brány firewall třetí strany z Azure Marketplace. Ochrana je k dispozici pro veřejné [IP adresy](virtual-network-public-ip-address.md)IPv4 a IPv6 Azure .

|Funkce                                         |Základní ochrana DDoS                 |Standard ochrany DDoS                      |
|------------------------------------------------|--------------------------------------|----------------------------------------------|
|Aktivní monitorování provozu & vždy při detekci |Ano                                   |Ano                                           |
|Automatická zmírnění útoků                    |Ano                                   |Ano                                           |
|Záruka dostupnosti                          |Oblast Azure                          |Aplikace                                   |
|Zásady zmírňování změny klimatu                             |Vyladěný pro svazek oblasti návštěvnosti Azure |Vyladěno pro objem provozu aplikací          |
|Metriky & výstrahy                                |Ne                                    |Metriky útoků v reálném čase & diagnostické protokoly prostřednictvím azure monitoru                                 |
|Zprávy o zmírnění rizik                              |Ne                                    |Po zprávách o zmírnění útoku                |
|Protokoly toku ke zmírnění rizik                            |Ne                                    |Datový proud protokolu NRT pro integraci SIEM           |
|Přizpůsobení zásad zmírnění rizik                 |Ne                                    |Zapojte odborníky na DDoS                           |
|Podpora                                         |Nejlepší úsilí                           |Přístup k DDoS expertům během aktivního útoku|
|SLA                                             |Oblast Azure                          |Záruka aplikace & ochranu nákladů       |
|Ceny                                         |Free                                  |Měsíční & využití na základě                         |

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Typy útoků DDoS, které zmírňuje standard ochrany DDoS

DDoS Protection Standard může zmírnit následující typy útoků:

- **Objemové útoky**: Cílem útoku je zaplavit síťovou vrstvu značným množstvím zdánlivě legitimního provozu. Zahrnuje povodně UDP, zesílené povodně a další záplavy s falšovanými pakety. DDoS Protection Standard zmírňuje tyto potenciální multigigabajtové útoky tím, že je automaticky absorbuje a drhne v globálním síťovém měřítku Azure.
- **Útoky protokolu**: Tyto útoky činí cíl nepřístupným využitím slabiny v zásobníku protokolu vrstvy 3 a vrstvy 4. Zahrnuje, SYN povodňové útoky, reflexe útoky a další protokol útoky. DDoS Protection Standard zmírňuje tyto útoky, rozlišuje mezi škodlivým a legitimním provozem, komunikuje s klientem a blokuje škodlivý provoz. 
- **Útoky vrstvy prostředků (aplikace):** Tyto útoky se zaměřují na pakety webových aplikací, aby narušily přenos dat mezi hostiteli. Útoky zahrnují porušení protokolu HTTP, injektáž SQL, skriptování mezi weby a další útoky vrstvy 7. K zajištění ochrany proti těmto útokům použijte bránu firewall webové aplikace, jako je [brána firewall webové aplikace Azure Application Gateway](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), a také standard ochrany DDoS. Na [Webu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)jsou k dispozici také nabídky brány firewall pro webové aplikace jiných výrobců.

DDoS Protection Standard chrání prostředky ve virtuální síti, včetně veřejných IP adres přidružených k virtuálním počítačům, nástrojům pro vyrovnávání zatížení a aplikačním bránám. Ve spojení s bránou firewall webové aplikace Application Gateway nebo bránou firewall webové aplikace jiného výrobce nasaditou ve virtuální síti s veřejnou IP adresou může standard Ochrany DDoS poskytovat plnou úroveň 3 až úroveň 7.

## <a name="ddos-protection-standard-features"></a>Funkce DDoS Protection Standard

![Funkce DDoS](./media/ddos-protection-overview/ddosfeatures.png)

Funkce DDoS Protection Standard zahrnují:

- **Integrace nativní platformy:** Nativně integrované do Azure. Zahrnuje konfiguraci prostřednictvím portálu Azure. DDoS Protection Standard rozumí vašim prostředkům a konfiguraci prostředků.
- **Ochrana na klíč:** Zjednodušená konfigurace okamžitě chrání všechny prostředky ve virtuální síti, jakmile je povolen standard ochrany DDoS. Není vyžadován žádný zásah ani definice uživatele. DDoS Protection Standard okamžitě a automaticky zmírňuje útok, jakmile je detekován.
- **Nepřetržité sledování provozu:** Vaše aplikace provoz vzory jsou sledovány 24 hodin denně, 7 dní v týdnu, hledá ukazatele DDoS útoků. Zmírnění se provádí při překročení zásad ochrany.
- **Adaptivní ladění:** Inteligentní profilování provozu se učí provoz vaší aplikace v průběhu času a vybírá a aktualizuje profil, který je nejvhodnější pro vaši službu. Profil se upravuje podle toho, jak se v průběhu času mění provoz.
- **Vícevrstvá ochrana:** Poskytuje úplnou ochranu před skládacími kódy při použití s bránou firewall webové aplikace.
- **Rozsáhlé škálování zmírnění:** Více než 60 různých typů útoků lze zmírnit, s globální kapacitou, k ochraně proti největší známé útoky DDoS.
- **Analýza útoků:** Získejte podrobné zprávy v pětiminutových přírůstcích během útoku a kompletní shrnutí po skončení útoku. Streamovat protokoly toku zmírnění do systému offline zabezpečení informací a událostí (SIEM) pro monitorování téměř v reálném čase během útoku.
- **Metriky útoku:** Souhrnné metriky z každého útoku jsou přístupné prostřednictvím Azure Monitoru.
- **Upozornění na útok:** Výstrahy lze konfigurovat na začátku a zastavení útoku a po dobu trvání útoku pomocí integrovaných metrik útoku. Výstrahy se integrují do vašeho provozního softwaru, jako jsou protokoly Microsoft Azure Monitor, Splunk, Azure Storage, E-mail a portál Azure.
- **Záruka nákladů:** Kredity služby přenosu dat a horizontálního navýšení kapacity aplikací pro zdokumentované útoky DDoS.

## <a name="ddos-protection-standard-mitigation"></a>Zmírnění standardu DDoS Protection

DDoS Protection Standard monitoruje skutečné využití provozu a neustále jej porovnává s prahovými hodnotami definovanými v zásadách DDoS. Při překročení prahové hodnoty přenosu, DDoS zmírnění je zahájeno automaticky. Při provozu vrátí pod prahovou hodnotu, zmírnění je odebrána.

![Omezení rizik](./media/ddos-protection-overview/mitigation.png)

Během zmírnění je provoz odeslaný do chráněného prostředku přesměrován službou ochrany DDoS a provádí se několik kontrol, například následující kontroly:

- Ujistěte se, že pakety odpovídají specifikacím internetu a nejsou poškozeny.
- Komunikujte s klientem a zjistěte, zda je provoz potenciálně podpěrným paketem (např.: SYN Auth nebo SYN Cookie nebo uvolněním paketu pro zdroj, který jej může znovu odeslat).
- Limitní počet paketů s omezením rychlosti, pokud nelze provést žádnou jinou metodu vynucení.

Ochrana před útoky DDoS blokuje provoz útoku a zbývající provoz přesměrovává do zamýšleného cíle. Během několika minut od detekce útoku budete upozorněni pomocí metrik Azure Monitoru. Konfigurací protokolování na Telemetrie DDoS Protection Standard, můžete zapsat protokoly do dostupných možností pro budoucí analýzu. Metrická data ve službě Azure Monitor for DDoS Protection Standard se uchovávají po dobu 30 dnů.

Společnost Microsoft spolupracuje se společností [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) na vytvoření rozhraní, kde můžete generovat provoz proti veřejným IP adresám s podporou ochrany DDoS pro simulace. Simulace služby BreakPoint Cloud umožňuje:

- Ověření, jak Microsoft Azure DDoS Protection Standard chrání prostředky Azure před útoky DDoS
- Optimalizujte proces reakce na incidenty v rámci útoku DDoS
- Dodržování předpisů ddos dokumentu
- Školení týmů zabezpečení sítě

## <a name="next-steps"></a>Další kroky

- [Konfigurace standardu ochrany DDoS](manage-ddos-protection.md)

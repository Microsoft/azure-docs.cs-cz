---
title: Azure DDoS Protection standardní přehled | Dokumentace Microsoftu
description: Další informace o službě Azure DDoS Protection.
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
ms.openlocfilehash: 95fc257485aeea396185089b2b73a6e73ee2bfee
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2018
ms.locfileid: "42060080"
---
# <a name="azure-ddos-protection-standard-overview"></a>Přehled služby Azure DDoS Protection Standard

Distribuované útoky na dostupnost služeb (DDoS) jsou některé z největších obavy týkající se dostupnosti a zabezpečení zákazníci, které se pohybují své aplikace do cloudu. S útoky DDoS pokusí vyčerpání aplikační prostředky, čímž aplikaci není k dispozici pro oprávněné uživatele. Útoky DDoS můžete cílit na libovolný koncový bod, který je veřejně dostupný prostřednictvím Internetu.

Azure DDoS protection v kombinaci s aplikací osvědčené postupy pro navrhování, poskytují ochranu před útoky DDoS. Azure DDoS protection nabízí následující úrovně služeb:

- **Základní**: automaticky povolené v rámci platformy Azure, bez dalších poplatků. Monitorování neustále v provozu a v reálném čase ke zmírnění běžných útoků na úrovni sítě, poskytují stejné obranu využívaných online služeb Microsoftu. Celé škále globální sítě Azure slouží k distribuci a zmírnit útok provoz napříč oblastmi. Poskytuje ochranu pro protokoly IPv4 a IPv6 Azure [veřejné IP adresy](virtual-network-public-ip-address.md).
- **Standardní**: úroveň služeb Basic, který je vyladěný speciálně pro prostředky Azure Virtual Network poskytuje funkce pro další zmírnění útoků. DDoS Protection Standard je snadno zajistit a nevyžaduje žádné změny aplikace. Zásady ochrany je vyladěná prostřednictvím monitorování vyhrazené provozu a algoritmů strojového učení. Zásady se použijí k veřejné IP adresy přidružené k prostředkům, které jsou nasazené ve virtuálních sítích, jako je například instancí nástroje pro vyrovnávání zatížení Azure, Azure Application Gateway a Azure Service Fabric. Telemetrická data v reálném čase je k dispozici prostřednictvím Azure Monitor zobrazení během útoku a historie. Ochrana vrstvy aplikace je možné přidat prostřednictvím [Firewall webových aplikací služby Azure Application Gateway](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Poskytuje ochranu pro IPv4 Azure [veřejné IP adresy](virtual-network-public-ip-address.md).

![Azure DDoS Standard Protection](./media/ddos-protection-overview/ddospic.png)

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Typy útoků DDoS, které před útoky DDoS Protection Standard omezuje

DDoS Protection standardní lze zmírnit tyto typy útoků:

- **Odměrné útoky**: útok cílem je k vyplnění síťové vrstvě s vyžadovat značné množství zdánlivě legitimní provoz. Obsahuje UDP záplavy zesílení záplavy a dalších záplavy falešné paketů. DDoS Protection Standard omezuje tyto potenciální útoky více GB zajistit plynulý provoz a čištění, škálování globální sítě Azure, automaticky.
- **Protokol útoky**: tyto útoky cíl vykreslování nedostupný, zneužitím slabiny ve vrstvě 3 a zásobník protokolu vrstvy 4. Zahrnuje, SYN útokům zahlcení reflexe útoky a dalších útoků protokolu. DDoS Protection standardní zmírňuje tyto útoky rozlišování škodlivým a legitimní provoz tak, že komunikaci s klientem a blokování škodlivý provoz. 
- **Útoky na prostředku (aplikace)**: cílit na tyto útoky pakety webové aplikace, narušit přenos dat mezi hostiteli. Útoky patří HTTP narušením protokolu SQL vkládání, skriptování napříč weby a další útoky vrstvy 7. Použití Azure [Application Gateway firewall webových aplikací](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), před útoky DDoS Protection Standard, zajistit ochranu před těmito útoky. Existují také nabídky brány firewall třetích stran webových aplikací k dispozici v [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

Standardní před útoky DDoS Protection chrání prostředky ve virtuální síti, včetně veřejné IP adresy přidružené k virtual machines, nástroji pro vyrovnávání zatížení a brány application Gateway. Po s firewallem webových aplikací aplikační brány s velkou provázaností, můžete před útoky DDoS Protection standardní zadejte úplné vrstvy 3 vrstvy 7 funkci omezení rizik.

## <a name="ddos-protection-standard-features"></a>DDoS Protection standardní funkce

![Funkce před útoky DDoS](./media/ddos-protection-overview/ddosfeatures.png)

DDoS Protection standardní funkce patří:

- **Integrace nativní platformy:** nativně integrované do Azure. Zahrnuje konfiguraci prostřednictvím webu Azure portal. Standardní před útoky DDoS Protection rozumí vašim prostředkům a konfiguraci prostředků.
- **Ochrana na klíč:** zjednodušená konfigurace okamžitě chrání všechny prostředky ve virtuální síti jako standardní před útoky DDoS Protection je povolená. Vyžaduje se žádná definice zásahů nebo uživatele. Standardní před útoky DDoS Protection okamžitě a automaticky zmírní útok, jakmile se detekuje.
- **Monitorování vždy provozu:** vzory provozu vaší aplikace jsou monitorovány 24 hodin denně, 7 dní v týdnu, hledá indikátory útoky DDoS. Omezení rizik provádí při překročení zásady ochrany.
- **Adaptivní ladění:** provoz inteligentní profilace učí o provozu vaší aplikace v průběhu času a vybere a aktualizuje profil, který je nejvhodnější pro vaši službu. Profil, který upravuje provoz mění v průběhu času.
- **Vrstvy 3 ochrany vrstvy 7:** poskytuje ochranu před útoky DDoS plnohodnotných, při použití s firewallem webových aplikací.
- **Škálování rozsáhlé omezení rizik:** přes 60 útoku různé typy můžete minimalizovat, globální kapacitou pro ochranu před největší známé útoky DDoS.
- **Útokům metriky:** Summarized metriky z každého útoku jsou přístupné prostřednictvím služby Azure Monitor.
- **Upozornění útoku:** oznámení se dají konfigurovat na spouštění a zastavování útoků a na dobu trvání útok, pomocí integrované útoku metrik. Upozornění integrovat do provozní softwaru, například Microsoft Azure Log Analytics, Splunk, Azure Storage, e-mailu a na webu Azure portal.
- **Se zárukou nákladů:** přenosu dat a aplikací horizontální navýšení kapacity kredity pro dokument útoky DDoS.

## <a name="ddos-protection-standard-mitigation"></a>DDoS Protection standardní omezení rizik

DDoS Protection standardní monitoruje využití skutečný provoz a neustále porovná je s prahové hodnoty definované v zásadě před útoky DDoS. Pokud je překročena mezní hodnota provoz, omezení rizik útoků DDoS automaticky inicializován. Po návratu provoz pod prahovou hodnotou, odeberou se omezení rizik.

![Omezení rizik](./media/ddos-protection-overview/mitigation.png)

Během omezení rizik přesměruje provoz odeslaný na chráněný prostředek službou DDoS protection a jsou prováděny několik kontrol, jako je například následující kontroly:

- Ujistěte se pakety specifikacím internet a nejsou poškozené.
- Interakce s klientem nástroje k určení, zda je přenos potenciálně falešné paketů (např.: SYN Auth nebo soubor Cookie SYN nebo přetažením paketů pro zdroj k opětovnému přenosu je).
- Omezení četnosti pakety, pokud žádnou jinou metodu vynucení lze provést.

Služba DDoS protection blokuje provoz útoku a předává zbývající provoz do jeho požadovaného cíle. Během několika minut detekce útoku budete upozorněni, pomocí metrik Azure monitoru. Konfigurace protokolování na DDoS Protection standardní telemetrická data, můžete zaznamenat protokoly na k dispozici možnosti pro pozdější analýzu. Data metriky ve službě Azure Monitor pro standardní před útoky DDoS Protection se uchovávají po dobu 30 dnů.

Microsoft uzavřel partnerství s [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) k vytváření rozhraní ve kterém můžete vygenerovat provoz s podporou služba DDoS Protection veřejné IP adresy pro simulace. Simulace zarážku Cloud vám umožní:

- Ověření, jak Microsoft Azure DDoS Protection Standard chrání vaše prostředky Azure před útoky DDoS
- Optimalizace procesu reakce na incidenty v rámci útoky DDoS
- Dodržování předpisů dokument před útoky DDoS
- Trénování vaše týmy zabezpečení sítě

## <a name="next-steps"></a>Další postup

- [Konfigurace před útoky DDoS Standard Protection](manage-ddos-protection.md)

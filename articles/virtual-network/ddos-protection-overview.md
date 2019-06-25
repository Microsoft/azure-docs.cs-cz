---
title: Azure DDoS Protection standardní přehled | Dokumentace Microsoftu
description: Další informace o službě Azure DDoS Protection.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/13/2018
ms.author: kumud
ms.openlocfilehash: 41e9d88df49d153089e6dc7a12c5873ccc167279
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209456"
---
# <a name="azure-ddos-protection-standard-overview"></a>Přehled služby Azure DDoS Protection Standard

Distribuované útoky na dostupnost služeb (DDoS) patří k největším hrozbám pro dostupnost a zabezpečení, se kterými se musejí zákazníci přesouvající aplikace do cloudu vyrovnávat. Útoky DDoS se pokoušejí vyčerpat prostředky aplikace, aby byla aplikace nedostupná oprávněným uživatelům. Útoky DDoS můžou cílit na jakýkoli koncový bod, který je veřejně dostupný přes internet.

Azure DDoS protection v kombinaci s aplikací osvědčené postupy pro navrhování, poskytují ochranu před útoky DDoS. Azure DDoS protection nabízí následující úrovně služeb:

- **Základní**: Automaticky povolené v rámci platformy Azure. Monitorování neustále v provozu a v reálném čase ke zmírnění běžných útoků na úrovni sítě, poskytují stejné obranu využívaných online služeb Microsoftu. Celé škále globální sítě Azure slouží k distribuci a zmírnit útok provoz napříč oblastmi. Poskytuje ochranu pro protokoly IPv4 a IPv6 Azure [veřejné IP adresy](virtual-network-public-ip-address.md).
- **Standard**: Poskytuje funkce pro další zmírnění útoků úroveň služeb Basic, který je vyladěný speciálně pro prostředky Azure Virtual Network. DDoS Protection Standard je snadno zajistit a nevyžaduje žádné změny aplikace. Zásady ochrany je vyladěná prostřednictvím monitorování vyhrazené provozu a algoritmů strojového učení. Zásady se použijí k veřejné IP adresy přidružené k prostředkům, které jsou nasazené ve virtuálních sítích, jako je například nástroj pro vyrovnávání zatížení Azure, Azure Application Gateway a Azure Service Fabric instance, ale tato ochrana se nevztahuje na služby App Service Environment. Telemetrická data v reálném čase je k dispozici prostřednictvím Azure Monitor zobrazení během útoku a historie. Bohaté útoku zmírnění analytics jsou k dispozici přes nastavení diagnostiky. Ochrana vrstvy aplikace je možné přidat prostřednictvím [Firewall webových aplikací služby Azure Application Gateway](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo po instalací 3. stran firewall z Azure Marketplace. Poskytuje ochranu pro protokoly IPv4 a IPv6 Azure [veřejné IP adresy](virtual-network-public-ip-address.md).

![Azure DDoS Protection základní vs. Standard](./media/ddos-protection-overview/ddoscomparison.png)

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Typy útoků DDoS, které před útoky DDoS Protection Standard omezuje

DDoS Protection standardní lze zmírnit tyto typy útoků:

- **Odměrné útoky**: Útok cílem je k vyplnění síťové vrstvě s vyžadovat značné množství zdánlivě legitimní provoz. Obsahuje UDP záplavy zesílení záplavy a dalších záplavy falešné paketů. DDoS Protection Standard omezuje tyto potenciální útoky více GB zajistit plynulý provoz a čištění, škálování globální sítě Azure, automaticky.
- **Protokol útoky**: Tyto útoky cíl vykreslování nedostupný, zneužitím slabé stránky ve vrstvě 3 a 4 protokolů vrstvy. Zahrnuje, SYN útokům zahlcení reflexe útoky a dalších útoků protokolu. DDoS Protection standardní zmírňuje tyto útoky rozlišování škodlivým a legitimní provoz tak, že komunikaci s klientem a blokování škodlivý provoz. 
- **Útoky na prostředku (aplikace)** : Tyto útoky směrovat pakety webové aplikace, narušit přenos dat mezi hostiteli. Útoky patří HTTP narušením protokolu SQL vkládání, skriptování napříč weby a další útoky vrstvy 7. Použití Azure [Application Gateway firewall webových aplikací](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), před útoky DDoS Protection Standard, zajistit ochranu před těmito útoky. Existují také nabídky brány firewall třetích stran webových aplikací k dispozici v [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

Standardní před útoky DDoS Protection chrání prostředky ve virtuální síti, včetně veřejné IP adresy přidružené k virtual machines, nástroji pro vyrovnávání zatížení a brány application Gateway. Po s firewallem webových aplikací aplikační brány s velkou provázaností, můžete před útoky DDoS Protection standardní zadejte úplné vrstvy 3 vrstvy 7 funkci omezení rizik.

## <a name="ddos-protection-standard-features"></a>DDoS Protection standardní funkce

![Funkce před útoky DDoS](./media/ddos-protection-overview/ddosfeatures.png)

DDoS Protection standardní funkce patří:

- **Integrace nativní platformy:** Nativně integrováno do Azure. Zahrnuje konfiguraci prostřednictvím webu Azure portal. Standardní před útoky DDoS Protection rozumí vašim prostředkům a konfiguraci prostředků.
- **Ochrana na klíč:** Zjednodušená konfigurace okamžitě chrání všechny prostředky ve virtuální síti jako standardní před útoky DDoS Protection je povolená. Vyžaduje se žádná definice zásahů nebo uživatele. Standardní před útoky DDoS Protection okamžitě a automaticky zmírní útok, jakmile se detekuje.
- **Monitorování se vždy provozu:** Vzory provozu vaší aplikace jsou monitorovány 24 hodin denně, 7 dní v týdnu, hledá indikátory útoky DDoS. Omezení rizik provádí při překročení zásady ochrany.
- **Adaptivní ladění:** Inteligentní přenos profilace učí o provozu vaší aplikace v průběhu času a vybere a aktualizuje profil, který je nejvhodnější pro vaši službu. Profil, který upravuje provoz mění v průběhu času.
- **Víceúrovňová ochrany:** Poskytuje ochranu před útoky DDoS plnohodnotných, při použití s firewallem webových aplikací.
- **Škálování rozsáhlé omezení rizik:** Více než 60 útoku různé typy můžete řešit s využitím globální kapacitu, pro ochranu před největší známé útoky DDoS.
- **Útok analytics:** V pětiminutových intervalech můžete během útoku získávat podrobné sestavy. Po skončení útoku obdržíte úplný souhrn. Stream zmírnění protokolů toku pro offline bezpečnostní informace a události správu systému (SIEM) pro téměř v reálném čase monitorovat během útoku.
- **Útok metriky:** Souhrnná metriky z každého útoku jsou přístupné prostřednictvím služby Azure Monitor.
- **Útok výstrahy:** Oznámení se dají konfigurovat na spouštění a zastavování útoků a na dobu trvání útok, pomocí integrované útoku metrik. Upozornění integrovat do provozní softwaru, například protokoly Microsoft Azure Monitor, Splunk, Azure Storage, e-mailu a na webu Azure portal.
- **Se zárukou nákladů:** Přenos dat a aplikací horizontální navýšení kapacity kredity pro dokument útoky DDoS.

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

---
title: Azure DDoS Protection funkce
description: Informace o funkcích Azure DDoS Protection
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 602bb98f2cdc8a96874eba8dadfa33f3267d19ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97746554"
---
# <a name="azure-ddos-protection-standard-features"></a>Funkce služby Azure DDoS Protection úrovně Standard

Následující části popisují klíčové funkce služby Azure DDoS Protection Standard.

## <a name="always-on-traffic-monitoring"></a>Monitorování nepřetržitého provozu

DDoS Protection Standard monitoruje skutečné využití provozu a trvale ho porovnává s mezními hodnotami definovanými v zásadách DDoS. Při překročení prahové hodnoty přenosu se DDoSé riziko iniciuje automaticky. Když se provoz vrátí pod prahové hodnoty, je riziko zastaveno.

![Azure DDoS Protection zmírnění úrovně Standard](./media/ddos-protection-overview/mitigation.png)

Během zmírnění ochrany se provoz odeslaný do chráněného prostředku přesměruje pomocí služby DDoS Protection a provede se několik kontrol, jako například:

- Zajistěte, aby pakety odpovídaly specifikacím Internetu a nejsou poškozeny.
- Interakci s klientem, aby bylo možné zjistit, zda je přenos potenciálně falešným paketem (například soubor. cookie SYN nebo souborů cookie SYN nebo vyřazením paketu pro zdroj, který ho znovu odešle).
- Pakety s omezením četnosti, pokud není možné provést žádnou jinou metodu vynucení.

DDoS Protection vyřazuje útok a přepošle zbývající provoz do zamýšleného cíle. Během několika minut od rozpoznání útoku budete upozorněni pomocí metrik Azure Monitoru. Konfigurací protokolování na DDoS Protection standardní telemetrie můžete protokoly zapsat na dostupné možnosti pro budoucí analýzu. Data metriky v Azure Monitor pro DDoS Protection Standard se uchovávají po dobu 30 dnů.

## <a name="adaptive-real-time-tuning"></a>Adaptivní optimalizace v reálném čase

Služba Azure DDoS Protection Basic pomáhá chránit zákazníky a zabránit dopadům na jiné zákazníky. Pokud je například služba zřízena pro typický objem legitimního příchozího provozu, který je menší než *míra triggeru* zásady DDoS Protection v rámci infrastruktury, může se DDoS útok na prostředky tohoto zákazníka nevšimnout. Obecně platí, že složitost nedávných útoků (například DDoS s více vektory) a chování klientů, které jsou specifické pro jednotlivé zákazníky, se týkají zásad ochrany podle zákazníka. Služba to dosahuje pomocí dvou přehledů:

- Automatické učení vzorů přenosů pro jednotlivé zákazníky (na veřejné IP adresy) pro vrstvy 3 a 4.

- Minimalizace falešně pozitivních hodnot, vzhledem k tomu, že škálování Azure umožňuje, aby mohla absorbovat významné množství provozu.

![Diagram způsobu, jakým funguje DDoS Protection Standard, s kruhovým generováním zásad](./media/ddos-best-practices/image-5.png)

## <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>DDoS Protection telemetrie, monitorování a upozorňování

DDoS Protection Standard zpřístupňuje bohatou telemetrii prostřednictvím [Azure monitor](../azure-monitor/overview.md). Výstrahy pro libovolnou Azure Monitor metriky, které DDoS Protection používá, můžete nakonfigurovat. Protokolování můžete integrovat s Splunk (Azure Event Hubs), protokoly Azure Monitor a Azure Storage pro pokročilou analýzu prostřednictvím rozhraní diagnostiky Azure Monitor.

### <a name="ddos-mitigation-policies"></a>Zásady zmírnění DDoS

V Azure Portal vyberte **monitorovat**  >  **metriky**. V podokně **metriky** vyberte skupinu prostředků, vyberte typ prostředku **Veřejná IP adresa** a vyberte veřejnou IP adresu Azure. Metriky DDoS jsou viditelné v podokně **dostupné metriky** .

DDoS Protection Standard aplikuje tři zásady zmírnění rizik (TCP SYN, TCP a UDP) pro každou veřejnou IP adresu chráněného prostředku ve virtuální síti, ve které je povolený DDoS. Prahové hodnoty zásad můžete zobrazit tak, že vyberete **příchozí pakety metriky, které budou aktivovat zmírnění DDoS**.

![Graf dostupných metrik a metrik](./media/ddos-best-practices/image-7.png)

Prahové hodnoty zásad se konfigurují prostřednictvím profilace síťového provozu založeného na strojovém učení. K zmírňování DDoS dochází u IP adresy s útokem jenom v případě, že se překročení prahové hodnoty zásad.

### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Metrika pro IP adresu v rámci útoku DDoS

Pokud je veřejná IP adresa v rámci útoku, hodnota pro metriku **v rámci útoku DDoS nebo se** nemění na 1, protože DDoS Protection provádí zmírnění přenosů útoku.

!["V části útok DDoS nebo není" metrika a graf](./media/ddos-best-practices/image-8.png)

Pro tuto metriku doporučujeme nakonfigurovat výstrahu. Pak budete upozorněni, když dojde k aktivnímu zmírnění DDoS na vaší veřejné IP adrese.

Další informace najdete v tématu [správa Azure DDoS Protection Standard pomocí Azure Portal](manage-ddos-protection.md).

## <a name="web-application-firewall-for-resource-attacks"></a>Firewall webových aplikací pro útoky na prostředky

Pro usnadnění zabezpečení webových aplikací byste měli nakonfigurovat bránu firewall webových aplikací (WAF), která je specifická pro útoky na prostředky v aplikační vrstvě. WAF kontroluje příchozí webový provoz, aby blokoval vkládání SQL, skriptování mezi weby, DDoS a další útoky vrstvy 7. Azure poskytuje [WAF jako funkci Application Gateway](../web-application-firewall/ag/ag-overview.md) pro centralizovanou ochranu webových aplikací před běžnými zneužitími a chybami zabezpečení. Od partnerů Azure jsou k dispozici další nabídky WAF, které mohou být vhodnější pro vaše potřeby prostřednictvím [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Dokonce i brány firewall webových aplikací jsou náchylné k navýšení a útokům na vyčerpání stavu. Důrazně doporučujeme povolit DDoS Protection Standard ve virtuální síti WAF, aby bylo možné lépe chránit před útoky z objemu a protokolu. Další informace najdete v části [referenční architektury DDoS Protection](ddos-protection-reference-architectures.md) .

## <a name="protection-planning"></a>Plánování ochrany

Plánování a příprava jsou zásadní pro pochopení, jak systém provede během DDoS útoku. Návrh plánu odezvy správy incidentů je součástí tohoto úsilí.

Pokud máte DDoS Protection Standard, ujistěte se, že je povolená ve virtuální síti koncových bodů směřujících k Internetu. Konfigurace výstrah DDoS vám pomůže trvale sledovat případné útoky v infrastruktuře. 

Monitorujte své aplikace nezávisle. Pochopení normálního chování aplikace Připravte se na jednání, pokud se aplikace během útoku DDoS nechová podle očekávání. 

Naučte se, jak vaše služby reagují na útok pomocí [testování prostřednictvím simulací](test-through-simulations.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit plán DDoS Protection](manage-ddos-protection.md).

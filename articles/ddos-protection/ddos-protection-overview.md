---
title: Přehled služby Azure DDoS Protection úrovně Standard
description: Zjistěte, jak služba Azure DDoS Protection úrovně Standard v kombinaci s osvědčenými postupy návrhu aplikací poskytuje ochranu před útoky DDoS.
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/9/2020
ms.author: yitoh
ms.openlocfilehash: 2b0f8a73a6852883f87ba9fc4333cb6fa8101a39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101703112"
---
# <a name="azure-ddos-protection-standard-overview"></a>Přehled Azure DDoS Protection Standard

Distribuované útoky na dostupnost služeb (DDoS) patří k největším hrozbám pro dostupnost a zabezpečení, se kterými se musejí zákazníci přesouvající aplikace do cloudu vyrovnávat. Útok DDoS se pokusí vyčerpat prostředky aplikace, takže aplikace nebude k dispozici oprávněným uživatelům. Útoky DDoS můžou cílit na jakýkoli koncový bod, který je veřejně dostupný přes internet.

Každá vlastnost v Azure je chráněná ochranou infrastruktury Azure v DDoS (Basic) bez dalších nákladů. Škálování a kapacita globálně nasazené sítě Azure zajišťuje obranu proti běžným útokům na síťové vrstvy prostřednictvím nepřetržitého monitorování provozu a zmírnění rizik v reálném čase. DDoS Protection Basic nevyžaduje žádné změny konfigurace nebo aplikace uživatele. DDoS Protection Basic pomáhá chránit všechny služby Azure, včetně služeb PaaS, jako je Azure DNS.

Azure DDoS Protection Standard, v kombinaci s osvědčenými postupy návrhu aplikací, poskytuje rozšířené funkce zmírnění DDoS, které chrání před útoky DDoS. Automaticky se vyladěna tak, aby chránila vaše konkrétní prostředky Azure ve virtuální síti. Ochranu je snadné povolit v jakékoli nové nebo existující virtuální síti a nevyžaduje žádné změny aplikací nebo prostředků. Má několik výhod oproti základní službě, včetně protokolování, upozorňování a telemetrie. 

![Porovnání služby Azure DDoS Protection](./media/ddos-protection-overview/ddos-comparison.png)

Azure DDoS Protection neukládá zákaznická data.

## <a name="features"></a>Funkce

- **Integrace nativní platformy:** Nativně integrovaná do Azure. Zahrnuje konfiguraci prostřednictvím Azure Portal. DDoS Protection Standard rozumí vašim prostředkům a konfiguraci prostředků.
- **Ochrana klíč:** Zjednodušená konfigurace okamžitě chrání všechny prostředky ve virtuální síti, jakmile je povolená DDoS Protection Standard. Není nutná žádná definice ani zásah uživatele. 
- **Monitorování nepřetržitého provozu:** Vaše vzory přenosů aplikací jsou monitorované 24 hodin denně, 7 dní v týdnu a hledají indikátory DDoSch útoků. DDoS Protection Standard okamžitě a automaticky zmírnit útok, jakmile se zjistí.
- **Adaptivní ladění:** Inteligentní profilace provozu zjišťuje provoz vaší aplikace v průběhu času a vybere a aktualizuje profil, který je nejvhodnější pro vaši službu. Profil se v průběhu času upraví jako změny provozu.
- **Víceúrovňová ochrana:** Při nasazení pomocí brány firewall webových aplikací (WAF) DDoS Protection standardně chrání ve vrstvě sítě (vrstva 3 a 4, kterou nabízí Azure DDoS Protection Standard), a v aplikační vrstvě (vrstva 7, kterou nabízí WAF). Nabídky WAF zahrnují SKU Azure [Application Gateway WAF](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a také nabídky firewallu webových aplikací třetích stran, které jsou k dispozici v [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).
- **Rozsáhlá škála rizik:** U více než 60 různých typů útoků se dá zmírnit s globální kapacitou, která se bude chránit před největším známými DDoS útoky.
- **Analýza útoků:** Získejte podrobné sestavy během útoku za 5 minut a kompletní souhrn po skončení útoku. Protokoly toku pro zmírnění rizik streamování do [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md) nebo do režimu Siem (Information Security and Event Management) pro téměř v reálném čase při útoku.
- **Metriky útoku:** Souhrnná metrika z každého útoku je přístupná prostřednictvím Azure Monitor.
- **Výstrahy útoku:** Výstrahy se dají nakonfigurovat na začátku a na konci útoku a přes dobu trvání útoku pomocí integrované metriky útoku. Výstrahy se integrují do svého provozního softwaru, jako je Microsoft Azure monitorování protokolů, Splunk, Azure Storage, e-mailu a Azure Portal.
- **DDoS Rapid Response**: pro pomoc s vyšetřováním a analýzou útoků se věnuje tým služby DDoS Protection Rapid Response (DRR). Další informace najdete v tématu [DDoS Rapid Response](ddos-rapid-response.md).
- **Záruka nákladů:** Dodržením kreditu služeb přenosu dat a aplikací na více instancí za náklady na prostředky vzniklé v důsledku dokumentovaných útoků DDoS.

## <a name="pricing"></a>Ceny

Plány DDoS Protection mají pevnou měsíční poplatek $2 944 za měsíc, který pokrývá až 100 veřejných IP adres. Ochrana dalších prostředků bude za každý prostředek měsíčně vypůjčit dalších $30.

V rámci tenanta se dá jeden plán DDoS Protection použít pro několik předplatných, takže nemusíte vytvářet více než jeden plán DDoS Protection.

Další informace o cenách Azure DDoS Protection Standard najdete v tématu [Azure DDoS Protection Standard Price](https://azure.microsoft.com/pricing/details/ddos-protection/).

## <a name="reference-architectures"></a>Referenční architektury

DDoS Protection Standard je určený pro [služby, které jsou nasazené ve virtuální síti](../virtual-network/virtual-network-for-azure-services.md). Pro ostatní služby platí výchozí služba DDoS Protection Basic. Další informace o podporovaných architekturách najdete v tématu [DDoS Protection referenčních architektur](./ddos-protection-reference-architectures.md). 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření plánu DDoS Protection](manage-ddos-protection.md)
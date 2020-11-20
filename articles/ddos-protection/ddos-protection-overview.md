---
title: Přehled Azure DDoS Protection Standard
description: Zjistěte, jak služba Azure DDoS Protection úrovně Standard v kombinaci s osvědčenými postupy návrhu aplikací poskytuje ochranu před útoky DDoS.
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/9/2020
ms.author: yitoh
ms.openlocfilehash: e3ded2fc286117da1438b0bb28298632532c4329
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992535"
---
# <a name="azure-ddos-protection-standard-overview"></a>Přehled služby Azure DDoS Protection úrovně Standard

Distribuované útoky na dostupnost služeb (DDoS) patří k největším hrozbám pro dostupnost a zabezpečení, se kterými se musejí zákazníci přesouvající aplikace do cloudu vyrovnávat. Útok DDoS se pokusí vyčerpat prostředky aplikace, takže aplikace nebude k dispozici oprávněným uživatelům. Útoky DDoS můžou cílit na jakýkoli koncový bod, který je veřejně dostupný přes internet.

Každá vlastnost v Azure je chráněná ochranou infrastruktury Azure v DDoS (Basic) bez dalších nákladů. Škálování a kapacita globálně nasazené sítě Azure zajišťuje obranu proti běžným útokům na síťové vrstvy prostřednictvím nepřetržitého monitorování provozu a zmírnění rizik v reálném čase. DDoS Protection Basic nevyžaduje žádné změny konfigurace nebo aplikace uživatele. DDoS Protection Basic pomáhá chránit všechny služby Azure, včetně služeb PaaS, jako je Azure DNS.

Azure DDoS Protection Standard, v kombinaci s osvědčenými postupy návrhu aplikací, poskytuje rozšířené funkce zmírnění DDoS, které chrání před útoky DDoS. Automaticky se vyladěna tak, aby chránila vaše konkrétní prostředky Azure ve virtuální síti. Ochranu je snadné povolit v jakékoli nové nebo existující virtuální síti a nevyžaduje žádné změny aplikací nebo prostředků. Má několik výhod oproti základní službě, včetně protokolování, upozorňování a telemetrie. 

![Porovnání služby Azure DDoS Protection](./media/ddos-protection-overview/ddos-comparison.png)

Azure DDoS Protection neukládá zákaznická data.

## <a name="features"></a>Funkce

- **Integrace nativní platformy:** Nativně integrovaná do Azure. Zahrnuje konfiguraci prostřednictvím Azure Portal. DDoS Protection Standard rozumí vašim prostředkům a konfiguraci prostředků.
- **Ochrana klíč:** Zjednodušená konfigurace okamžitě chrání všechny prostředky ve virtuální síti, jakmile je povolená DDoS Protection Standard. Není nutná žádná definice ani zásah uživatele. DDoS Protection Standard okamžitě a automaticky zmírnit útok, jakmile se zjistí.
- **Monitorování nepřetržitého provozu:** Vaše vzory přenosů aplikací jsou monitorované 24 hodin denně, 7 dní v týdnu a hledají indikátory DDoSch útoků. Při překročení zásad ochrany dochází ke zmírnění rizik.
- **Adaptivní ladění:** Inteligentní profilace provozu zjišťuje provoz vaší aplikace v průběhu času a vybere a aktualizuje profil, který je nejvhodnější pro vaši službu. Profil se v průběhu času upraví jako změny provozu.
- **Víceúrovňová ochrana:** Poskytuje úplnou ochranu zásobníku DDoS při použití s bránou firewall webových aplikací.
- **Rozsáhlá škála rizik:** U více než 60 různých typů útoků se dá zmírnit s globální kapacitou, která se bude chránit před největším známými DDoS útoky.
- **Analýza útoků:** Získejte podrobné sestavy během útoku za 5 minut a kompletní souhrn po skončení útoku. Protokoly toku pro zmírnění rizik streamování do [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md) nebo do režimu Siem (Information Security and Event Management) pro téměř v reálném čase při útoku.
- **Metriky útoku:** Souhrnná metrika z každého útoku je přístupná prostřednictvím Azure Monitor.
- **Výstrahy útoku:** Výstrahy se dají nakonfigurovat na začátku a na konci útoku a přes dobu trvání útoku pomocí integrované metriky útoku. Výstrahy se integrují do svého provozního softwaru, jako je Microsoft Azure monitorování protokolů, Splunk, Azure Storage, e-mailu a Azure Portal.
- **DDoS Rapid Response**: pro pomoc s vyšetřováním a analýzou útoků se věnuje tým služby DDoS Protection Rapid Response (DRR). Další informace najdete v tématu [DDoS Rapid Response](ddos-rapid-response.md).
- **Záruka nákladů:** Kredity služeb pro přenos dat a škálování aplikací pro dokumentované DDoS útoky.

## <a name="pricing"></a>Ceny

Další informace o cenách Azure DDoS Protection Standard najdete v tématu [Azure DDoS Protection Standard Price](https://azure.microsoft.com/pricing/details/ddos-protection/).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření plánu DDoS Protection](manage-ddos-protection.md)
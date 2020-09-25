---
title: Azure Defender for IoT
description: Další informace o Azure Defenderu pro IoT
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 307916680d9a05a5083aea057b2ef4b855a4ff57
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301801"
---
# <a name="introduction-to-azure-defender-for-iot"></a>Seznámení s Azure Defenderem pro IoT

Sjednocení správy zabezpečení a umožnění komplexní detekce a analýzy hrozeb v rámci hybridních cloudových úloh a řešení Azure IoT.

Azure Defender pro IoT poskytuje:

- **Zjišťování prostředků a mapování sítě**, včetně podrobností, jako je výrobce zařízení, typ zařízení a způsob, jakým zařízení komunikují v síti
- **Správa ohrožení zabezpečení**, včetně informací o CVEs, otevřených portech a neautorizovaných internetových připojeních
- **Monitorování nepřetržitých hrozeb**s výstrahami v reálném čase, které označují neobvyklé nebo neoprávněné aktivity, jako jsou cílené útoky nebo malware

V [vyhrazené dokumentaci](https://docs.microsoft.com/azure/asc-for-iot/overview)jsou k dispozici úplné podrobnosti.

## <a name="availability"></a>Dostupnost
|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Všeobecně dostupná (GA)|
|Stanov|Vyžaduje [Azure Defender](security-center-pricing.md) .|
|Požadované role a oprávnění:|Oprávnění k zápisu do skupin zabezpečení sítě počítače|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![Yes](./media/icons/yes-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)|
|||

## <a name="what-devices-can-azure-defender-for-iot-secure"></a>Jaká zařízení můžou Azure Defender pro IoT zabezpečit?
Kombinací možností Security Center, Azure Defenderu a CyberX technologie bez agenta můžete zabezpečit:

- **Bezserverová zařízení** připojená prostřednictvím IoT Hub. To organizacím umožňuje zrychlit své iniciativy IoT tím, že budou moct pomocí jediného sjednoceného řešení zabezpečit jak moderní, tak i tradiční zařízení s funkcí ICS/SCADA.
    - Připojte nová zařízení a pomocí zásad zabezpečení pro vaše úlohy (listová zařízení, zařízení Microsoft Edge IoT Hub) zajistěte dodržování standardů zabezpečení a vylepšení stav zabezpečení.

- **Nespravovaná zařízení brownfield** používaná v prostředích provozní technologie (z), jako jsou výroba, vytváření systémů pro správu (BMS), věd o životní prostředí, energetické a vodní pomůcky, ropný &ý plyn a logistika. 
    - Pro ochranu nespravovaných zařízení používá Azure Defender pro IoT místní senzor pro pasivní, neinvazivní analýzu provozu sítě (NTA), která má nulový dopad na výkon v prostředích. 
    - Obsahuje taky podrobné porozumění specializovaným protokolům IoT/s, které jsou kombinované s použitím analytických funkcí s patenty, IoT/s a strojovým učením, aby se vyloučila nutnost nakonfigurovat jakákoli pravidla nebo signatury, což vede k typickým nasazením v řádu minut nebo hodin a nikoli ke dnům nebo týdnech. 


## <a name="azure-defender-for-iot-integration-with-azure-sentinel"></a>Azure Defender pro integraci IoT s Azure Sentinel
Pokud chcete povolit jednotné IT oddělení a monitorování zabezpečení a zásady správného řízení, Azure Defender pro IoT nativně se integruje s [Azure Sentinel](../sentinel/overview.md).

SecOps týmy můžou:

- Rychlé zjišťování a reakce na hrozby IoT/OT prostřednictvím společnosti playbooky, které jsou součástí systému Sentinel
- Využijte výhod průběžně aktualizovaných analýz hrozeb a specifických pro IoT, které poskytuje oddíl 52, interní tým Microsoftu pro IoT/s Threat Intelligence
- Integrujte Azure Defender pro IoT se stávajícími pracovními postupy SOC a nástroji zabezpečení třetích stran, jako je Splunk, IBM QRadar a ServiceNow.


## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o Azure Defenderu pro IoT v Azure Security Center. Další informace naleznete v tématech:

- [Představujeme Azure Security Center pro IoT](../asc-for-iot/overview.md)

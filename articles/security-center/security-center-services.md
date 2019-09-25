---
title: Podporované funkce, které jsou k dispozici v Azure Security Center | Microsoft Docs
description: Tento dokument poskytuje seznam služeb podporovaných nástrojem Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: ad662cdeb0a87e57eb9e3e7480d120be1c6c729e
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71218258"
---
# <a name="supported-features-available-in-azure-security-center"></a>Podporované funkce dostupné v Azure Security Center

> [!NOTE]
>Některé funkce jsou dostupné jenom na úrovni Standard. Pokud jste se ještě nezaregistrovali do úrovně Standard Security Center, k dispozici je bezplatné zkušební období. Další informace najdete na [stránce s cenami za Security Center](https://azure.microsoft.com/pricing/details/security-center/).

V následujících částech jsou uvedeny Security Center funkce, které jsou k dispozici pro [podporované platformy](security-center-os-coverage.md).

* [Virtuální počítače a servery](#vm-server-features)
* [Služby PaaS](#paas-services)


## Funkce podporované virtuálním počítačem nebo serverem<a name="vm-server-features"></a>

> [!div class="mx-tableFixed"]

|Server|Windows|||Linux|||Ceny|
|----|----|----|----|----|----|----|----|
|**Prostředí**|**Azure**||**Non-Azure**|**Azure**||**Non-Azure**||
||**Virtuální počítač**|**Sada škálování virtuálních počítačů**||**Virtuální počítač**|**Sada škálování virtuálních počítačů**|
|Výstrahy detekce hrozeb VMBA|✔|✔|✔|✔ (na podporovaných verzích)|✔ (na podporovaných verzích)|✔|Detekce hrozeb (zdarma) (Standard)|
|Výstrahy detekce hrozeb založené na síti|✔|✔|X|✔|✔|X|Standard|
|Integrace ATP v programu Microsoft Defender|✔ (na podporovaných verzích)|✔ (na podporovaných verzích)|✔|X|X|X|Standard|
|Chybějící opravy|✔|✔|✔|✔|✔|✔|Zdarma|
|Konfigurace zabezpečení|✔|✔|✔|✔|✔|✔|Zdarma|
|Posouzení služby Endpoint Protection|✔|✔|✔|X|X|X|Zdarma|
|Přístup k virtuálnímu počítači podle potřeby|✔|X|X|✔|X|X|Standard|
|Adaptivní řízení aplikací|✔|X|✔|✔|X|✔|Standard|
|FIM|✔|✔|✔|✔|✔|✔|Standard|
|Vyhodnocení šifrování disku|✔|✔|X|✔|✔|X|Zdarma|
|Nasazení třetí strany|✔|X|X|✔|X|X|Zdarma|
|Posouzení NSG|✔|✔|X|✔|✔|X|Zdarma|
|Detekce hrozeb v bezsouborovém formátu|✔|✔|✔|X|X|X|Standard|
|Mapa sítě|✔|✔|X|✔|✔|X|Standard|
|Adaptivní ovládací prvky sítě|✔|✔|X|✔|✔|X|Standard|
|Řídicí panel dodržování předpisů & sestavy|✔|✔|✔|✔|✔|✔|Standard|
|Doporučení a detekce hrozeb v kontejnerech IaaS hostovaných v Docker|X|X|X|✔|✔|✔|Standard|

### Podporovaná řešení ochrany koncových bodů<a name="endpoint-supported"></a>

Následující tabulka poskytuje matrici:

 - Zda můžete k instalaci jednotlivých řešení použít Azure Security Center.
 - Která řešení ochrany koncových bodů Security Center můžou zjistit. Pokud se zjistí jedno z těchto řešení ochrany koncových bodů, Security Center nedoporučuje instalaci.

Informace o tom, kdy jsou pro každou z těchto ochran vygenerována doporučení, najdete v tématu [Endpoint Protection Assessment and Recommendations](security-center-endpoint-protection.md).

| Endpoint Protection| Platformy | Instalace Security Center | Zjišťování Security Center |
|------|------|-----|-----|
| Windows Defender (antimalware Microsoftu)| Windows Server 2016| Ne, součást operačního systému| Ano |
| Ochrana koncových bodů System Center (antimalware Microsoftu) | Windows Server 2012 R2, 2012, 2008 R2 (viz poznámka níže) | Prostřednictvím rozšíření | Ano |
| Trend Micro – všechny verze * | Řada Windows Serverů  | Ne | Ano |
| Symantec v12.1.1100+| Řada Windows Serverů  | Ne | Ano |
| McAfee v10+ | Řada Windows Serverů  | Ne | Ano |
| McAfee v10+ | Řada Linux serverů  | Ne | Ano **\*** |
| Sophos v9 +| Řada Linux serverů  | Ne | Ano **\***  |

 **\*** Stav pokrytí a podpůrná data jsou aktuálně k dispozici pouze v pracovním prostoru Log Analytics přidruženém k vašim chráněným předplatným a nereflektují se na portálu Azure Security Center.

> [!NOTE]
>
> - Detekce služby System Center Endpoint Protection (SCEP) na virtuálním počítači s Windows Serverem 2008 R2 vyžaduje, aby se protokol SCEP nainstaloval po PowerShellu 3,0 (nebo v horní verzi).
> - Detekce funkce Trend Micro Protection je podporovaná pro obsáhlé agenty zabezpečení.  OfficeScan agenti nejsou podporováni.


## Funkce <a name="paas-services"></a> podporované službami PaaS Services

Azure Security Center podporuje následující prostředky PaaS:

|Služba|Doporučení (zdarma)|Detekce hrozeb (Standard)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|CosmosDB|X| ✔|
|Blob Storage|✔| ✔|
|Účet úložiště|✔| Není k dispozici|
|App Service|✔| ✔|
|Funkce|✔| X|
|Cloudová služba|✔| X|
|Virtuální síť|✔| Není k dispozici|
|Subnet|✔| Není k dispozici|
|NIC|✔| Není k dispozici|
|NSG|✔| Není k dispozici|
|Subscription|✔ **| ✔|
|Účet Batch|✔| X|
|Účet Service Fabric|✔| X|
|Účet služby Automation|✔| X|
|Nástroj pro vyrovnávání zatížení|✔| X|
|Search|✔| X|
|Názvový prostor služby Service Bus|✔| X|
|Stream Analytics|✔| X|
|Obor názvů centra událostí|✔| X|
|Aplikace logiky|✔| X|
|Redis|✔| Není k dispozici|
|Data Lake Analytics|✔| X|
|Data Lake Store|✔| X|
|Trezor klíčů|✔| X|

\*Tyto funkce jsou aktuálně podporované ve verzi Public Preview.

\*\*Doporučení pro Azure Active Directory (Azure AD) jsou dostupná jenom pro standardní odběry.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [Security Center shromažďuje data a agenta Log Analytics](security-center-enable-data-collection.md).
- Přečtěte si [, jak Security Center spravuje a chrání data](security-center-data-security.md).
- Naučte se [plánovat a porozumět hlediskům návrhu, které je potřeba přijmout Azure Security Center](security-center-planning-and-operations-guide.md).
- Přečtěte si [platformy, které podporují Security Center](security-center-os-coverage.md).
- Přečtěte si další informace o [detekci hrozeb pro virtuální počítače & serverech v Azure Security Center](security-center-alerts-iaas.md).
- Přečtěte si [Nejčastější dotazy týkající se použití Azure Security Center](security-center-faq.md).
- Přečtěte si [blogové příspěvky o zabezpečení Azure a dodržování předpisů](https://blogs.msdn.com/b/azuresecurity/).

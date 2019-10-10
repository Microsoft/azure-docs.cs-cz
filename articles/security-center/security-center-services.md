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
ms.openlocfilehash: ed231bc05d58a40c93cea74081c027e5b49f5306
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254512"
---
# <a name="supported-features-available-in-azure-security-center"></a>Podporované funkce dostupné v Azure Security Center

> [!NOTE]
>Některé funkce jsou dostupné jenom na úrovni Standard. Pokud jste se ještě nezaregistrovali do úrovně Standard Security Center, k dispozici je bezplatné zkušební období. Další informace najdete na stránce s [cenami Security Center](https://azure.microsoft.com/pricing/details/security-center/).

V následujících částech jsou uvedeny Security Center funkce, které jsou k dispozici pro [podporované platformy](security-center-os-coverage.md).

* [Virtuální počítače a servery](#vm-server-features)
* [Služby PaaS](#paas-services)


## Funkce podporované virtuálním počítačem nebo serverem<a name="vm-server-features"></a>

> [!div class="mx-tableFixed"]

|Server|Windows|||Linux|||stanov|
|----|----|----|----|----|----|----|----|
|**Hlediska**|**Azure**||**Mimo Azure**|**Azure**||**Mimo Azure**||
||**Virtuální počítač**|**Sada škálování virtuálních počítačů**||**Virtuální počítač**|**Sada škálování virtuálních počítačů**|
|[Integrace ATP v programu Microsoft Defender](https://docs.microsoft.com/azure/security-center/security-center-wdatp)|✔ (na podporovaných verzích)|✔ (na podporovaných verzích)|✔|X|X|X|Standardní|
|[Výstrahy detekce hrozeb při analýze chování virtuálních počítačů](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas)|✔|✔|✔|✔ (na podporovaných verzích)|✔ (na podporovaných verzích)|✔|Detekce hrozeb (zdarma) (Standard)|
|[Výstrahy detekce hrozeb nejenom pro soubory](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas#fileless-attack-detection-)|✔|✔|✔|X|X|X|Standardní|
|[Výstrahy detekce hrozeb založené na síti](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer#azure-network-layer)|✔|✔|X|✔|✔|X|Standardní|
|[Přístup k virtuálnímu počítači za běhu](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)|✔|X|X|✔|X|X|Standardní|
|[Monitorování integrity souborů](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)|✔|✔|✔|✔|✔|✔|Standardní|
|[Adaptivní řízení aplikací](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)|✔|X|✔|✔|X|✔|Standardní|
|[Mapa sítě](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations#network-map)|✔|✔|X|✔|✔|X|Standardní|
|[Adaptivní posílení zabezpečení sítě](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)|✔|X|X|✔|X|X|Standardní|
|Adaptivní ovládací prvky sítě|✔|✔|X|✔|✔|X|Standardní|
|[Řídicí panel dodržování předpisů & sestavy](https://docs.microsoft.com/azure/security-center/security-center-compliance-dashboard)|✔|✔|✔|✔|✔|✔|Standardní|
|Doporučení a detekce hrozeb v kontejnerech IaaS hostovaných v Docker|X|X|X|✔|✔|✔|Standardní|
|Chybí posouzení oprav operačního systému|✔|✔|✔|✔|✔|✔|Dost|
|Vyhodnocení nezabezpečených konfigurací zabezpečení|✔|✔|✔|✔|✔|✔|Dost|
|[Posouzení služby Endpoint Protection](https://docs.microsoft.com/azure/security-center/security-center-services#supported-endpoint-protection-solutions-)|✔|✔|✔|X|X|X|Dost|
|Vyhodnocení šifrování disku|✔|✔|X|✔|✔|X|Dost|
|Posouzení ohrožení zabezpečení třetí strany|✔|X|X|✔|X|X|Dost|
|[Posouzení zabezpečení sítě]()|✔|✔|X|✔|✔|X|Dost|

### Podporovaná řešení ochrany koncových bodů<a name="endpoint-supported"></a>

Následující tabulka poskytuje matrici:

 - Zda můžete k instalaci jednotlivých řešení použít Azure Security Center.
 - Která řešení ochrany koncových bodů Security Center můžou zjistit. Pokud se zjistí řešení ochrany koncového bodu z tohoto seznamu Security Center nedoporučujete ho instalovat.

Informace o tom, kdy jsou pro každou z těchto ochran vygenerována doporučení, najdete v tématu [Endpoint Protection Assessment and Recommendations](security-center-endpoint-protection.md).

| Endpoint Protection| Platformy | Instalace Security Center | Zjišťování Security Center |
|------|------|-----|-----|
| Windows Defender (antimalware Microsoftu)| Windows Server 2016| Ne, integrováno s operačním systémem| Ano |
| System Center Endpoint Protection (antimalware Microsoftu) | Windows Server 2012 R2, 2012, 2008 R2 (viz poznámka níže) | Přes rozšíření | Ano |
| Trend Micro – všechny verze * | Řada Windows serveru  | Ne | Ano |
| Symantec v 12.1.1100 +| Řada Windows serveru  | Ne | Ano |
| McAfee v10 za účelem + | Řada Windows serveru  | Ne | Ano |
| McAfee v10 za účelem + | Řada Linux serverů  | Ne | Ano **\*** |
| Sophos v9 +| Řada Linux serverů  | Ne | Ano **\***  |

 **\*** Stav pokrytí a podpůrná data jsou v tuto chvíli k dispozici pouze v pracovním prostoru Log Analytics přidruženém k vašim chráněným předplatným. Neprojeví se na portálu Azure Security Center.

> [!NOTE]
>
> - Detekce služby System Center Endpoint Protection (SCEP) na virtuálním počítači s Windows Serverem 2008 R2 vyžaduje, aby se protokol SCEP nainstaloval po PowerShellu 3,0 (nebo v horní verzi).
> - Detekce funkce Trend Micro Protection je podporovaná pro obsáhlé agenty zabezpečení.  OfficeScan agenti nejsou podporováni.


## Funkce <a name="paas-services"></a> podporované službami PaaS Services

Azure Security Center podporuje následující prostředky PaaS:

|Služba|Doporučení (zdarma)|Detekce hrozeb (Standard)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL|✔| ✔|
|MySQL|✔| ✔|
|CosmosDB|X| ✔|
|Úložiště objektů BLOB|✔| ✔|
|Účet úložiště|✔| Není k dispozici|
|App Service|✔| ✔|
|Funkce|✔| X|
|Cloudová služba|✔| X|
|Sítě|✔| Není k dispozici|
|Podsíť|✔| Není k dispozici|
|SÍŤOVÝCH|✔| Není k dispozici|
|NSG|✔| Není k dispozici|
|Předplatné|✔ * *| ✔|
|Účet Batch|✔| X|
|Účet Service Fabric|✔| X|
|Účet služby Automation|✔| X|
|Load Balancer|✔| X|
|Hledat|✔| X|
|Obor názvů služby Service Bus|✔| X|
|Stream Analytics|✔| X|
|Obor názvů centra událostí|✔| X|
|Logic Apps|✔| X|
|Redis|✔| Není k dispozici|
|Data Lake Analytics|✔| X|
|Data Lake Store|✔| X|
|Trezor klíčů|✔| X|

\* tyto funkce jsou aktuálně podporované ve verzi Public Preview.

doporučení \* @ no__t-1 Azure Active Directory (Azure AD) jsou dostupná jenom pro standardní odběry.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [Security Center shromažďuje data a agenta Log Analytics](security-center-enable-data-collection.md).
- Přečtěte si [, jak Security Center spravuje a chrání data](security-center-data-security.md).
- Naučte se [plánovat a porozumět hlediskům návrhu, které je potřeba přijmout Azure Security Center](security-center-planning-and-operations-guide.md).
- Přečtěte si [platformy, které podporují Security Center](security-center-os-coverage.md).
- Přečtěte si další informace o [detekci hrozeb pro virtuální počítače & serverech v Azure Security Center](security-center-alerts-iaas.md).
- Přečtěte si [Nejčastější dotazy týkající se použití Azure Security Center](security-center-faq.md).
- Přečtěte si [blogové příspěvky o zabezpečení Azure a dodržování předpisů](https://blogs.msdn.com/b/azuresecurity/).

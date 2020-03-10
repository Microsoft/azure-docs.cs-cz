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
ms.date: 03/01/2020
ms.author: memildin
ms.openlocfilehash: 9d3fa1e0b62ea6f4762c3df6ac7da310d5703807
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394674"
---
# <a name="feature-coverage-for-machines"></a>Pokrytí funkcí pro počítače

Následující tabulky ukazují Azure Security Center funkce, které jsou k dispozici pro virtuální počítače a servery.

## Podporované funkce pro virtuální počítače a servery<a name="vm-server-features"></a>

### <a name="windows-machines"></a>[Počítače s Windows](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Funkce**|**Azure Virtual Machines**|**Virtual Machine Scale Sets Azure**|**Počítače mimo Azure**|**Ceny**
|[Integrace ATP v programu Microsoft Defender](security-center-wdatp.md)|✔</br>(na podporovaných verzích)|✔</br>(na podporovaných verzích)|✔|Standardní|
|[Analýza chování virtuálního počítače (a výstrahy zabezpečení)](threat-protection.md)|✔|✔|✔|Doporučení (zdarma) </br></br> Výstrahy zabezpečení (Standard)|
|[Výstrahy zabezpečení nesouborového souboru](alerts-reference.md#alerts-windows)|✔|✔|✔|Standardní|
|[Výstrahy zabezpečení založené na síti](threat-protection.md#network-layer)|✔|✔|-|Standardní|
|[Přístup k virtuálnímu počítači za běhu](security-center-just-in-time.md)|✔|-|-|Standardní|
|[Posouzení nativních ohrožení zabezpečení](built-in-vulnerability-assessment.md)|✔|-|-|Standardní|
|[Monitorování integrity souborů](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standardní|
|[Adaptivní ovládací prvky aplikace](security-center-adaptive-application.md)|✔|-|✔|Standardní|
|[Mapa sítě](security-center-network-recommendations.md#network-map)|✔|✔|-|Standardní|
|[Adaptivní posílení zabezpečení sítě](security-center-adaptive-network-hardening.md)|✔|-|-|Standardní|
|Adaptivní ovládací prvky sítě|✔|✔|-|Standardní|
|[Řídicí panel dodržování předpisů & sestavy](security-center-compliance-dashboard.md)|✔|✔|✔|Standardní|
|Doporučení a ochrana před hrozbami v kontejnerech IaaS hostovaných v Docker|-|-|-|Standardní|
|Chybí posouzení oprav operačního systému|✔|✔|✔|Free|
|Vyhodnocení nezabezpečených konfigurací zabezpečení|✔|✔|✔|Free|
|[Posouzení služby Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Free|
|Vyhodnocení šifrování disku|✔|✔|-|Free|
|Posouzení ohrožení zabezpečení třetí strany|✔|-|-|Free|
|[Posouzení zabezpečení sítě](security-center-network-recommendations.md)|✔|✔|-|Free|


### <a name="linux-machines"></a>[Počítače se systémem Linux](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Funkce**|**Azure Virtual Machines**|**Virtual Machine Scale Sets Azure**|**Počítače mimo Azure**|**Ceny**
|[Integrace ATP v programu Microsoft Defender](security-center-wdatp.md)|-|-|-|Standardní|
|[Analýza chování virtuálního počítače (a výstrahy zabezpečení)](security-center-alerts-iaas.md)|✔</br>(na podporovaných verzích)|✔</br>(na podporovaných verzích)|✔|Doporučení (zdarma) </br></br> Výstrahy zabezpečení (Standard)|
|[Výstrahy zabezpečení nesouborového souboru](alerts-reference.md#alerts-windows)|-|-|-|Standardní|
|[Výstrahy zabezpečení založené na síti](threat-protection.md#network-layer)|✔|✔|-|Standardní|
|[Přístup k virtuálnímu počítači za běhu](security-center-just-in-time.md)|✔|-|-|Standardní|
|[Posouzení nativních ohrožení zabezpečení](built-in-vulnerability-assessment.md)|✔|-|-|Standardní|
|[Monitorování integrity souborů](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standardní|
|[Adaptivní ovládací prvky aplikace](security-center-adaptive-application.md)|✔|-|✔|Standardní|
|[Mapa sítě](security-center-network-recommendations.md#network-map)|✔|✔|-|Standardní|
|[Adaptivní posílení zabezpečení sítě](security-center-adaptive-network-hardening.md)|✔|-|-|Standardní|
|Adaptivní ovládací prvky sítě|✔|✔|-|Standardní|
|[Řídicí panel dodržování předpisů & sestavy](security-center-compliance-dashboard.md)|✔|✔|✔|Standardní|
|Doporučení a ochrana před hrozbami v kontejnerech IaaS hostovaných v Docker|✔|✔|✔|Standardní|
|Chybí posouzení oprav operačního systému|✔|✔|✔|Free|
|Vyhodnocení nezabezpečených konfigurací zabezpečení|✔|✔|✔|Free|
|[Posouzení služby Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Free|
|Vyhodnocení šifrování disku|✔|✔|-|Free|
|Posouzení ohrožení zabezpečení třetí strany|✔|-|-|Free|
|[Posouzení zabezpečení sítě](security-center-network-recommendations.md)|✔|✔|-|Free|

--- 


> [!TIP]
>Pokud chcete experimentovat s funkcemi, které jsou dostupné jenom na cenové úrovni Standard, můžou se uživatelé bezplatné úrovně zaregistrovat do 30denní zkušební verze. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).


## Podporovaná řešení ochrany koncových bodů<a name="endpoint-supported"></a>

Následující tabulka poskytuje matrici:

 - Zda můžete k instalaci jednotlivých řešení použít Azure Security Center.
 - Která řešení ochrany koncových bodů Security Center můžou zjistit. Pokud se zjistí řešení ochrany koncového bodu z tohoto seznamu Security Center nedoporučujete ho instalovat.

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

 **\*** Stav pokrytí a podpůrná data jsou v tuto chvíli k dispozici pouze v pracovním prostoru Log Analytics přidruženém k vašim chráněným předplatným. Neprojeví se na portálu Azure Security Center.

> [!NOTE]
> - Detekce služby System Center Endpoint Protection (SCEP) na virtuálním počítači s Windows Serverem 2008 R2 vyžaduje, aby se protokol SCEP nainstaloval po PowerShellu 3,0 (nebo v horní verzi).
> - Detekce funkce Trend Micro Protection je podporovaná pro obsáhlé agenty zabezpečení.  OfficeScan agenti nejsou podporováni.


## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [Security Center shromažďuje data a agenta Log Analytics](security-center-enable-data-collection.md).
- Přečtěte si [, jak Security Center spravuje a chrání data](security-center-data-security.md).
- Naučte se [plánovat a porozumět hlediskům návrhu, které je potřeba přijmout Azure Security Center](security-center-planning-and-operations-guide.md).
- Přečtěte si [platformy, které podporují Security Center](security-center-os-coverage.md).
- Přečtěte si další informace o [ochraně před hrozbami pro počítače se systémem Windows a Linux v Azure Security Center](threat-protection.md#windows-machines).
- Přečtěte si [Nejčastější dotazy týkající se Azure Security Center](faq-general.md).
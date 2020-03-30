---
title: Podporované funkce dostupné v Azure Security Center | Dokumenty společnosti Microsoft
description: Tento dokument obsahuje seznam služeb podporovaných Azure Security Center.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245236"
---
# <a name="feature-coverage-for-machines"></a>Pokrytí funkcí pro stroje

V následujících tabulkách jsou uvedeny funkce Azure Security Center, které jsou dostupné pro virtuální počítače a servery.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Podporované funkce pro virtuální počítače a servery<a name="vm-server-features"></a>

### <a name="windows-machines"></a>[Počítače se systémem Windows](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Funkce**|**Virtuální počítače Azure**|**Azure Virtual Machine Scale Sets**|**Počítače, které nejsou azure**|**Ceny**
|[Integrace ochrany ATP v programu Microsoft Defender](security-center-wdatp.md)|✔</br>(v podporovaných verzích)|✔</br>(v podporovaných verzích)|✔|Standard|
|[Analýza chování virtuálních strojů (a výstrahy zabezpečení)](threat-protection.md)|✔|✔|✔|Doporučení (zdarma) </br></br> Výstrahy zabezpečení (standardní)|
|[Výstrahy zabezpečení bez souborů](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[Výstrahy zabezpečení založené na síti](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Přístup k virtuálním virtuálním montovně just-in-time](security-center-just-in-time.md)|✔|-|-|Standard|
|[Nativní posouzení zranitelnosti](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Monitorování integrity souborů](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Adaptivní ovládací prvky aplikace](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Mapa sítě](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Adaptivní posílení sítě](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Adaptivní síťové ovládací prvky|✔|✔|-|Standard|
|[Řídicí panel dodržování předpisů & sestavy](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Doporučení a ochrana před hrozbami v kontejnerech IaaS hostovaných v Dockeru|-|-|-|Standard|
|Posouzení chybějících oprav operačního spoje|✔|✔|✔|Free|
|Vyhodnocení chyb zabezpečení|✔|✔|✔|Free|
|[Posouzení ochrany koncového bodu](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Free|
|Vyhodnocení šifrování disku|✔|✔|-|Free|
|Posouzení zranitelnosti třetích stran|✔|-|-|Free|
|[Vyhodnocení zabezpečení sítě](security-center-network-recommendations.md)|✔|✔|-|Free|


### <a name="linux-machines"></a>[Linuxové stroje](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Funkce**|**Virtuální počítače Azure**|**Azure Virtual Machine Scale Sets**|**Počítače, které nejsou azure**|**Ceny**
|[Integrace ochrany ATP v programu Microsoft Defender](security-center-wdatp.md)|-|-|-|Standard|
|[Analýza chování virtuálních strojů (a výstrahy zabezpečení)](security-center-alerts-iaas.md)|✔</br>(v podporovaných verzích)|✔</br>(v podporovaných verzích)|✔|Doporučení (zdarma) </br></br> Výstrahy zabezpečení (standardní)|
|[Výstrahy zabezpečení bez souborů](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[Výstrahy zabezpečení založené na síti](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Přístup k virtuálním virtuálním montovně just-in-time](security-center-just-in-time.md)|✔|-|-|Standard|
|[Nativní posouzení zranitelnosti](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Monitorování integrity souborů](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Adaptivní ovládací prvky aplikace](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Mapa sítě](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Adaptivní posílení sítě](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Adaptivní síťové ovládací prvky|✔|✔|-|Standard|
|[Řídicí panel dodržování předpisů & sestavy](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Doporučení a ochrana před hrozbami v kontejnerech IaaS hostovaných v Dockeru|✔|✔|✔|Standard|
|Posouzení chybějících oprav operačního spoje|✔|✔|✔|Free|
|Vyhodnocení chyb zabezpečení|✔|✔|✔|Free|
|[Posouzení ochrany koncového bodu](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Free|
|Vyhodnocení šifrování disku|✔|✔|-|Free|
|Posouzení zranitelnosti třetích stran|✔|-|-|Free|
|[Vyhodnocení zabezpečení sítě](security-center-network-recommendations.md)|✔|✔|-|Free|

--- 


> [!TIP]
>Chcete-li experimentovat s funkcemi, které jsou k dispozici pouze na standardní cenové úrovni, mohou se uživatelé bezplatné úrovně zaregistrovat do 30denní zkušební verze. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Podporovaná řešení ochrany koncových bodů<a name="endpoint-supported"></a>

Následující tabulka obsahuje matici:

 - Určuje, jestli můžete použít Azure Security Center k instalaci každého řešení za vás.
 - Která řešení ochrany koncových bodů, která může Security Center zjistit. Pokud je zjištěno řešení ochrany koncového bodu z tohoto seznamu, Security Center nedoporučuje instalaci jednoho.

Informace o tom, kdy jsou pro každou z těchto ochran generována doporučení, naleznete [v tématu Hodnocení ochrany koncových bodů a doporučení](security-center-endpoint-protection.md).

| Endpoint Protection (Ochrana koncových bodů)| Platformy | Instalace Security Center | Zjišťování Security Center |
|------|------|-----|-----|
| Windows Defender (antimalware Microsoftu)| Windows Server 2016| Ne, součást operačního systému| Ano |
| Ochrana koncových bodů System Center (antimalware Microsoftu) | Windows Server 2012 R2, 2012, 2008 R2 (viz poznámka níže) | Prostřednictvím rozšíření | Ano |
| Trend Micro – všechny verze* | Řada Windows Serverů  | Ne | Ano |
| Symantec v12.1.1100+| Řada Windows Serverů  | Ne | Ano |
| McAfee v10+ | Řada Windows Serverů  | Ne | Ano |
| McAfee v10+ | Linux Server Rodina  | Ne | Ano**\*** |
| Sophos V9+| Linux Server Rodina  | Ne | Ano**\***  |

 **\*** Stav pokrytí a podpůrná data jsou momentálně dostupná jenom v pracovním prostoru Log Analytics přidruženém k chráněným předplatným. Neodráží se to na portálu Azure Security Center.

> [!NOTE]
> - Detekce ochrany koncového bodu system center (SCEP) na virtuálním počítači se systémem Windows Server 2008 R2 vyžaduje, aby byl program SCEP nainstalován po prostředí PowerShell 3.0 (nebo v horní verzi).
> - Detekce ochrany Trend Micro je podporována pro agenty deep security.  Agenti sady OfficeScan nejsou podporováni.


## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [Security Center shromažďuje data a agent a analýzy protokolů](security-center-enable-data-collection.md).
- Zjistěte, jak [Security Center spravuje a zabezpečuje data](security-center-data-security.md).
- Přečtěte si, jak [naplánovat a pochopit aspekty návrhu a přijmout Azure Security Center](security-center-planning-and-operations-guide.md).
- Zkontrolujte [platformy, které podporují centrum zabezpečení](security-center-os-coverage.md).
- Další informace o [ochraně před hrozbami pro počítače s Windows a Linuxem v Azure Security Center](threat-protection.md#windows-machines).
- Najděte [nejčastější dotazy týkající se Azure Security Center](faq-general.md).
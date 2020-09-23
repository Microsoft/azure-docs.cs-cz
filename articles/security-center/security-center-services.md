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
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: db0c5890add303d85bdec76e00b8377567226a38
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904712"
---
# <a name="feature-coverage-for-machines"></a>Pokrytí funkcí pro počítače

Následující dvě karty ukazují funkce Azure Security Center, které jsou dostupné pro virtuální počítače a servery s Windows a Linux.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Podporované funkce pro virtuální počítače a servery <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Počítače s Windows**](#tab/features-windows)

|**Funkce**|**Azure Virtual Machines**|**Škálovací sady virtuálních počítačů Azure**|**Počítače mimo Azure**|**Vyžaduje se Azure Defender.**
|----|:----:|:----:|:----:|:----:|
|[Integrace ATP v programu Microsoft Defender](security-center-wdatp.md)|✔</br>(na podporovaných verzích)|✔</br>(na podporovaných verzích)|✔|Yes|
|[Analýza chování virtuálního počítače (a výstrahy zabezpečení)](alerts-reference.md)|✔|✔|✔|Yes|
|[Výstrahy zabezpečení nesouborového souboru](alerts-reference.md#alerts-windows)|✔|✔|✔|Yes|
|[Výstrahy zabezpečení založené na síti](other-threat-protections.md#network-layer)|✔|✔|-|Yes|
|[Přístup k virtuálnímu počítači za běhu](security-center-just-in-time.md)|✔|-|-|Yes|
|[Posouzení nativních ohrožení zabezpečení](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Yes|
|[Monitorování integrity souborů](security-center-file-integrity-monitoring.md)|✔|✔|✔|Yes|
|[Adaptivní řízení aplikací](security-center-adaptive-application.md)|✔|-|✔|Yes|
|[Mapa sítě](security-center-network-recommendations.md#network-map)|✔|✔|-|Yes|
|[Adaptivní posilování zabezpečení sítě](security-center-adaptive-network-hardening.md)|✔|-|-|Yes|
|[Řídicí panel dodržování předpisů & sestavy](security-center-compliance-dashboard.md)|✔|✔|✔|Yes|
|Doporučení a ochrana před hrozbami v kontejnerech IaaS hostovaných v Docker|-|-|-|Yes|
|Chybí posouzení oprav operačního systému|✔|✔|✔|Azure: zdarma<br><br>Mimo Azure: Ano|
|Vyhodnocení nezabezpečených konfigurací zabezpečení|✔|✔|✔|Azure: ne<br><br>Mimo Azure: Ano|
|[Posouzení služby Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: ne<br><br>Mimo Azure: Ano|
|Vyhodnocení šifrování disku|✔|✔|-|Free|
|Posouzení ohrožení zabezpečení třetí strany|✔|-|-|Free|
|[Posouzení zabezpečení sítě](security-center-network-recommendations.md)|✔|✔|-|Free|


### <a name="linux-machines"></a>[**Počítače se systémem Linux**](#tab/features-linux)

|**Funkce**|**Azure Virtual Machines**|**Škálovací sady virtuálních počítačů Azure**|**Počítače mimo Azure**|**Vyžaduje se Azure Defender.**
|----|:----:|:----:|:----:|:----:|
|[Integrace ATP v programu Microsoft Defender](security-center-wdatp.md)|-|-|-|Yes|
|[Analýza chování virtuálního počítače (a výstrahy zabezpečení)](security-center-alerts-iaas.md)|✔</br>(na podporovaných verzích)|✔</br>(na podporovaných verzích)|✔|Yes|
|[Výstrahy zabezpečení nesouborového souboru](alerts-reference.md#alerts-windows)|-|-|-|Yes|
|[Výstrahy zabezpečení založené na síti](other-threat-protections.md#network-layer)|✔|✔|-|Yes|
|[Přístup k virtuálnímu počítači za běhu](security-center-just-in-time.md)|✔|-|-|Yes|
|[Posouzení nativních ohrožení zabezpečení](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Yes|
|[Monitorování integrity souborů](security-center-file-integrity-monitoring.md)|✔|✔|✔|Yes|
|[Adaptivní řízení aplikací](security-center-adaptive-application.md)|✔|-|✔|Yes|
|[Mapa sítě](security-center-network-recommendations.md#network-map)|✔|✔|-|Yes|
|[Adaptivní posilování zabezpečení sítě](security-center-adaptive-network-hardening.md)|✔|-|-|Yes|
|[Řídicí panel dodržování předpisů & sestavy](security-center-compliance-dashboard.md)|✔|✔|✔|Yes|
|Doporučení a ochrana před hrozbami v kontejnerech IaaS hostovaných v Docker|✔|✔|✔|Yes|
|Chybí posouzení oprav operačního systému|✔|✔|✔|Azure: zdarma<br><br>Mimo Azure: Ano|
|Vyhodnocení nezabezpečených konfigurací zabezpečení|✔|✔|✔|Azure: zdarma<br><br>Mimo Azure: Ano|
|[Posouzení služby Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Free|
|Vyhodnocení šifrování disku|✔|✔|-|Free|
|Posouzení ohrožení zabezpečení třetí strany|✔|-|-|Free|
|[Posouzení zabezpečení sítě](security-center-network-recommendations.md)|✔|✔|-|Free|

--- 


> [!TIP]
>Pokud chcete experimentovat s funkcemi, které jsou k dispozici jenom v Azure Defenderu, můžete se zaregistrovat do 30denní zkušební verze. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Podporovaná řešení ochrany koncových bodů <a name="endpoint-supported"></a>

Následující tabulka poskytuje matrici:

 - Zda můžete k instalaci jednotlivých řešení použít Azure Security Center.
 - Která řešení ochrany koncových bodů Security Center můžou zjistit. Pokud se zjistí řešení ochrany koncového bodu z tohoto seznamu Security Center nedoporučujete ho instalovat.

Informace o tom, kdy jsou pro každou z těchto ochran vygenerována doporučení, najdete v tématu [Endpoint Protection Assessment and Recommendations](security-center-endpoint-protection.md).

| Funkce Endpoint Protection| Platformy | Instalace Security Center | Zjišťování Security Center |
|------|------|-----|-----|
| Antivirová ochrana v programu Microsoft Defender| Windows Server 2016 nebo novější| Ne, součást operačního systému| Yes |
| Ochrana koncových bodů System Center (antimalware Microsoftu) | Windows Server 2012 R2, 2012, 2008 R2 (viz poznámka níže) | Prostřednictvím rozšíření | Yes |
| Trend Micro – hluboké zabezpečení | Řada Windows Serverů  | No | Yes |
| Symantec v12.1.1100+| Řada Windows Serverů  | No | Yes |
| McAfee v10+ | Řada Windows Serverů  | No | Yes |
| McAfee v10+ | Řada Linux serverů  | No | Ano **\*** |
| Sophos v9 +| Řada Linux serverů  | No | Ano  **\***  |

 **\*** Stav pokrytí a podpůrná data jsou v tuto chvíli k dispozici pouze v pracovním prostoru Log Analytics přidruženém k vašim chráněným předplatným. Neprojeví se na portálu Azure Security Center.

> [!NOTE]
> Rozpoznání nástroje System Center Endpoint Protection (SCEP) na virtuálním počítači s Windows Serverem 2008 R2 vyžaduje, aby se protokol SCEP nainstaloval po PowerShellu (verze 3.0 nebo novější).


## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [Security Center shromažďuje data a agenta Log Analytics](security-center-enable-data-collection.md).
- Přečtěte si [, jak Security Center spravuje a chrání data](security-center-data-security.md).
- Přečtěte si [platformy, které podporují Security Center](security-center-os-coverage.md).
---
title: Funkce Azure Security Center v závislosti na operačním systému, typu počítače a cloudu
description: Přečtěte si, které Azure Security Center funkce jsou dostupné podle jejich operačního systému, typu a cloudového nasazení.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/24/2020
ms.author: memildin
ms.openlocfilehash: f5d6d6255b48192a5943159599531fac18ebeb26
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2020
ms.locfileid: "97796988"
---
# <a name="feature-coverage-for-machines"></a>Pokrytí funkcí pro počítače

Následující dvě karty ukazují funkce Azure Security Center, které jsou dostupné pro virtuální počítače a servery s Windows a Linux.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Podporované funkce pro virtuální počítače a servery <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Počítače s Windows**](#tab/features-windows)

|**Funkce**|**Azure Virtual Machines**|**Škálovací sady virtuálních počítačů Azure**|**Počítače s podporou ARC Azure**|**Vyžaduje se Azure Defender.**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender pro integraci koncových bodů](security-center-wdatp.md)|✔</br>(na podporovaných verzích)|✔</br>(na podporovaných verzích)|✔|Ano|
|[Analýza chování virtuálního počítače (a výstrahy zabezpečení)](alerts-reference.md)|✔|✔|✔|Ano|
|[Výstrahy zabezpečení nesouborového souboru](alerts-reference.md#alerts-windows)|✔|✔|✔|Ano|
|[Výstrahy zabezpečení založené na síti](other-threat-protections.md#network-layer)|✔|✔|-|Ano|
|[Přístup k virtuálnímu počítači za běhu](security-center-just-in-time.md)|✔|-|-|Ano|
|[Posouzení nativních ohrožení zabezpečení](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|Ano|
|[Monitorování integrity souborů](security-center-file-integrity-monitoring.md)|✔|✔|✔|Ano|
|[Adaptivní řízení aplikací](security-center-adaptive-application.md)|✔|-|✔|Ano|
|[Mapa sítě](security-center-network-recommendations.md#network-map)|✔|✔|-|Ano|
|[Adaptivní posilování zabezpečení sítě](security-center-adaptive-network-hardening.md)|✔|-|-|Ano|
|[Řídicí panel dodržování předpisů & sestavy](security-center-compliance-dashboard.md)|✔|✔|✔|Ano|
|Doporučení a ochrana před hrozbami v kontejnerech IaaS hostovaných v Docker|-|-|-|Ano|
|Chybí posouzení oprav operačního systému|✔|✔|✔|Azure: ne<br><br>Zapnutý oblouk: Ano|
|Vyhodnocení nezabezpečených konfigurací zabezpečení|✔|✔|✔|Azure: ne<br><br>Zapnutý oblouk: Ano|
|[Posouzení služby Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: ne<br><br>Zapnutý oblouk: Ano|
|Vyhodnocení šifrování disku|✔</br>(pro [podporované scénáře](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Ne|
|Posouzení ohrožení zabezpečení třetí strany|✔|-|✔|Ne|
|[Posouzení zabezpečení sítě](security-center-network-recommendations.md)|✔|✔|-|Ne|


### <a name="linux-machines"></a>[**Počítače se systémem Linux**](#tab/features-linux)

|**Funkce**|**Azure Virtual Machines**|**Škálovací sady virtuálních počítačů Azure**|**Počítače s podporou ARC Azure**|**Vyžaduje se Azure Defender.**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender pro integraci koncových bodů](security-center-wdatp.md)|-|-|-|Ano|
|[Analýza chování virtuálního počítače (a výstrahy zabezpečení)](./azure-defender.md)|✔</br>(na podporovaných verzích)|✔</br>(na podporovaných verzích)|✔|Ano|
|[Výstrahy zabezpečení nesouborového souboru](alerts-reference.md#alerts-windows)|-|-|-|Ano|
|[Výstrahy zabezpečení založené na síti](other-threat-protections.md#network-layer)|✔|✔|-|Ano|
|[Přístup k virtuálnímu počítači za běhu](security-center-just-in-time.md)|✔|-|-|Ano|
|[Posouzení nativních ohrožení zabezpečení](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|Ano|
|[Monitorování integrity souborů](security-center-file-integrity-monitoring.md)|✔|✔|✔|Ano|
|[Adaptivní řízení aplikací](security-center-adaptive-application.md)|✔|-|✔|Ano|
|[Mapa sítě](security-center-network-recommendations.md#network-map)|✔|✔|-|Ano|
|[Adaptivní posilování zabezpečení sítě](security-center-adaptive-network-hardening.md)|✔|-|-|Ano|
|[Řídicí panel dodržování předpisů & sestavy](security-center-compliance-dashboard.md)|✔|✔|✔|Ano|
|Doporučení a ochrana před hrozbami v kontejnerech IaaS hostovaných v Docker|✔|✔|✔|Ano|
|Chybí posouzení oprav operačního systému|✔|✔|✔|Azure: ne<br><br>Zapnutý oblouk: Ano|
|Vyhodnocení nezabezpečených konfigurací zabezpečení|✔|✔|✔|Azure: ne<br><br>Zapnutý oblouk: Ano|
|[Posouzení služby Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Ne|
|Vyhodnocení šifrování disku|✔</br>(pro [podporované scénáře](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Ne|
|Posouzení ohrožení zabezpečení třetí strany|✔|-|✔|Ne|
|[Posouzení zabezpečení sítě](security-center-network-recommendations.md)|✔|✔|-|Ne|

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
| Antivirová ochrana v programu Microsoft Defender| Windows Server 2016 nebo novější| Ne, součást operačního systému| Ano |
| Ochrana koncových bodů System Center (antimalware Microsoftu) | Windows Server 2012 R2, 2012, 2008 R2 (viz poznámka níže) | Prostřednictvím rozšíření | Ano |
| Trend Micro – hluboké zabezpečení | Řada Windows Serverů  | Ne | Ano |
| Symantec v12.1.1100+| Řada Windows Serverů  | Ne | Ano |
| McAfee v10+ | Řada Windows Serverů  | Ne | Ano |
| McAfee v10+ | Řada Linux serverů  | Ne | Ano * *\** _ |
| Sophos v9 +| Řada Linux serverů  | Ne | Ano  _*\**_  |

 _*\**_ Stav pokrytí a podpůrná data jsou v tuto chvíli k dispozici pouze v pracovním prostoru Log Analytics přidruženém k vašim chráněným předplatným. Neprojeví se na portálu Azure Security Center.

> [!NOTE]
> Rozpoznání nástroje System Center Endpoint Protection (SCEP) na virtuálním počítači s Windows Serverem 2008 R2 vyžaduje, aby se protokol SCEP nainstaloval po PowerShellu (verze 3.0 nebo novější).



## <a name="feature-support-in-government-clouds"></a>Podpora funkcí v cloudech pro státní správu

| Služba/funkce | US Gov | Čína gov |
|------|:----:|:----:|
|[Přístup k virtuálnímu počítači za běhu](security-center-just-in-time.md) (1)|✔|✔|
|[Monitorování integrity souborů](security-center-file-integrity-monitoring.md) (1)|✔|✔|
|[Adaptivní řízení aplikací](security-center-adaptive-application.md) (1)|✔|✔|
|[Adaptivní posílení sítě](security-center-adaptive-network-hardening.md) (1)|-|-|
|[Posílení zabezpečení hostitele Docker](harden-docker-hosts.md) (1)|✔|✔|
|[Integrované posouzení ohrožení zabezpečení pro počítače](deploy-vulnerability-assessment-vm.md) (1)|-|-|
|[Microsoft Defender pro koncový bod](harden-docker-hosts.md) (1)|✔|-|
|[Připojit účet AWS](quickstart-onboard-aws.md) (1)|-|-|
|[Připojit účet GCP](quickstart-onboard-gcp.md) (1)|-|-|
|[Průběžný export](continuous-export.md)|✔|✔ (2)|
|[Automatizace pracovního postupu](workflow-automation.md)|✔|✔|
|[Pravidla výjimky pro doporučení](exempt-resource.md)|-|-|
|[Pravidla potlačení výstrah](alerts-suppression-rules.md)|✔|✔|
|[E-mailová oznámení pro výstrahy zabezpečení](security-center-provide-security-contact-details.md)|✔|✔|
|[Inventář prostředků](asset-inventory.md)|✔|✔|
|[Azure Defender pro App Service](defender-for-app-service-introduction.md)|-|-|
|[Azure Defender pro úložiště](defender-for-storage-introduction.md)|✔|-|
|[Azure Defender for SQL](defender-for-sql-introduction.md)|✔|✔ (2)|
|[Azure Defender pro trezor klíčů](defender-for-key-vault-introduction.md)|-|-|
|[Azure Defender pro Resource Manager](defender-for-resource-manager-introduction.md)|-|-|
|[Azure Defender pro DNS](defender-for-dns-introduction.md)|-|-|
|[Azure Defender pro Kubernetes](defender-for-kubernetes-introduction.md)|✔|✔|
|[Azure Defender pro registry kontejnerů](defender-for-container-registries-introduction.md)|✔ (2)|-|
|||

(1) vyžaduje *pro servery _ Azure Defender* .*

(2) částečně


## <a name="next-steps"></a>Další kroky

- Přečtěte si [, jak Security Center shromažďuje data pomocí agenta Log Analytics](security-center-enable-data-collection.md).
- Přečtěte si [, jak Security Center spravuje a chrání data](security-center-data-security.md).
- Přečtěte si [platformy, které podporují Security Center](security-center-os-coverage.md).
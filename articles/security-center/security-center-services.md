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
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 7ffab200b50a7c4d1af60a230f237b18e8afc621
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754222"
---
# <a name="supported-features-available-in-azure-security-center"></a>Podporované funkce dostupné v Azure Security Center

> [!NOTE]
>Některé funkce jsou dostupné jenom na úrovni Standard. Pokud jste se ještě nezaregistrovali do úrovně Standard Security Center, k dispozici je bezplatné zkušební období. Další informace najdete na stránce s [cenami Security Center](https://azure.microsoft.com/pricing/details/security-center/).

V následujících částech jsou uvedeny Security Center funkce, které jsou k dispozici pro [podporované platformy](security-center-os-coverage.md).

* [Virtuální počítače a servery](#vm-server-features)
* [Služby PaaS](#paas-services)


## Funkce podporované virtuálním počítačem nebo serverem<a name="vm-server-features"></a>

### <a name="windowstabfeatures-windows"></a>[Windows](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
||**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Počítače mimo Azure**|**Ceny**
|[Integrace ATP v programu Microsoft Defender](security-center-wdatp.md)|✔ (na podporovaných verzích)|✔ (na podporovaných verzích)|✔|Úroveň Standard|
|[Výstrahy detekce hrozeb při analýze chování virtuálních počítačů](security-center-alerts-iaas.md)|✔|✔|✔|Detekce hrozeb (zdarma) (Standard)|
|[Výstrahy detekce hrozeb nejenom pro soubory](alerts-reference.md#alerts-windows)|✔|✔|✔|Úroveň Standard|
|[Výstrahy detekce hrozeb založené na síti](security-center-alerts-service-layer.md#azure-network-layer)|✔|✔|-|Úroveň Standard|
|[Přístup k virtuálnímu počítači za běhu](security-center-just-in-time.md)|✔|-|-|Úroveň Standard|
|[Posouzení nativních ohrožení zabezpečení](built-in-vulnerability-assessment.md)|✔|-|-|Úroveň Standard|
|[Monitorování integrity souborů](security-center-file-integrity-monitoring.md)|✔|✔|✔|Úroveň Standard|
|[Adaptivní ovládací prvky aplikace](security-center-adaptive-application.md)|✔|-|✔|Úroveň Standard|
|[Mapa sítě](security-center-network-recommendations.md#network-map)|✔|✔|-|Úroveň Standard|
|[Adaptivní posílení zabezpečení sítě](security-center-adaptive-network-hardening.md)|✔|-|-|Úroveň Standard|
|Adaptivní ovládací prvky sítě|✔|✔|-|Úroveň Standard|
|[Řídicí panel dodržování předpisů & sestavy](security-center-compliance-dashboard.md)|✔|✔|✔|Úroveň Standard|
|Doporučení a detekce hrozeb v kontejnerech IaaS hostovaných v Docker|-|-|-|Úroveň Standard|
|Chybí posouzení oprav operačního systému|✔|✔|✔|Zadarmo|
|Vyhodnocení nezabezpečených konfigurací zabezpečení|✔|✔|✔|Zadarmo|
|[Posouzení služby Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Zadarmo|
|Vyhodnocení šifrování disku|✔|✔|-|Zadarmo|
|Posouzení ohrožení zabezpečení třetí strany|✔|-|-|Zadarmo|
|[Posouzení zabezpečení sítě](security-center-network-recommendations.md)|✔|✔|-|Zadarmo|


### <a name="linuxtabfeatures-linux"></a>[Linux](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
||**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Počítače mimo Azure**|**Ceny**
|[Integrace ATP v programu Microsoft Defender](security-center-wdatp.md)|-|-|-|Úroveň Standard|
|[Výstrahy detekce hrozeb při analýze chování virtuálních počítačů](security-center-alerts-iaas.md)|✔ (na podporovaných verzích)|✔ (na podporovaných verzích)|✔|Detekce hrozeb (zdarma) (Standard)|
|[Výstrahy detekce hrozeb nejenom pro soubory](alerts-reference.md#alerts-windows)|-|-|-|Úroveň Standard|
|[Výstrahy detekce hrozeb založené na síti](security-center-alerts-service-layer.md#azure-network-layer)|✔|✔|-|Úroveň Standard|
|[Přístup k virtuálnímu počítači za běhu](security-center-just-in-time.md)|✔|-|-|Úroveň Standard|
|[Posouzení nativních ohrožení zabezpečení](built-in-vulnerability-assessment.md)|✔|-|-|Úroveň Standard|
|[Monitorování integrity souborů](security-center-file-integrity-monitoring.md)|✔|✔|✔|Úroveň Standard|
|[Adaptivní ovládací prvky aplikace](security-center-adaptive-application.md)|✔|-|✔|Úroveň Standard|
|[Mapa sítě](security-center-network-recommendations.md#network-map)|✔|✔|-|Úroveň Standard|
|[Adaptivní posílení zabezpečení sítě](security-center-adaptive-network-hardening.md)|✔|-|-|Úroveň Standard|
|Adaptivní ovládací prvky sítě|✔|✔|-|Úroveň Standard|
|[Řídicí panel dodržování předpisů & sestavy](security-center-compliance-dashboard.md)|✔|✔|✔|Úroveň Standard|
|Doporučení a detekce hrozeb v kontejnerech IaaS hostovaných v Docker|✔|✔|✔|Úroveň Standard|
|Chybí posouzení oprav operačního systému|✔|✔|✔|Zadarmo|
|Vyhodnocení nezabezpečených konfigurací zabezpečení|✔|✔|✔|Zadarmo|
|[Posouzení služby Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Zadarmo|
|Vyhodnocení šifrování disku|✔|✔|-|Zadarmo|
|Posouzení ohrožení zabezpečení třetí strany|✔|-|-|Zadarmo|
|[Posouzení zabezpečení sítě](security-center-network-recommendations.md)|✔|✔|-|Zadarmo|

--- 

## Podporovaná řešení ochrany koncových bodů<a name="endpoint-supported"></a>

Následující tabulka poskytuje matrici:

 - Zda můžete k instalaci jednotlivých řešení použít Azure Security Center.
 - Která řešení ochrany koncových bodů Security Center můžou zjistit. Pokud se zjistí řešení ochrany koncového bodu z tohoto seznamu Security Center nedoporučujete ho instalovat.

Informace o tom, kdy jsou pro každou z těchto ochran vygenerována doporučení, najdete v tématu [Endpoint Protection Assessment and Recommendations](security-center-endpoint-protection.md).

| Ochrana koncových bodů| Platformy | Instalace Security Center | Zjišťování Security Center |
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


## Funkce <a name="paas-services"></a> podporované službami PaaS Services

Azure Security Center podporuje následující prostředky PaaS:

|Služba|Doporučení (zdarma)|Výstrahy detekce hrozeb (Standard)|Posouzení ohrožení zabezpečení (standardní)|
|----|:----:|:----:|:----:|
|Databáze SQL Database|✔|✔|✔|
|Azure Container Registry|-|-|✔|
|Azure Kubernetes Service|✔|✔|-|
|Azure Database for PostgreSQL *|✔|✔|-|
|Azure Database for MySQL *|✔|✔|-|
|CosmosDB Azure *|-|✔|-|
|Účty úložiště|✔|-|-|
|Blob Storage|✔|✔|-|
|Aplikační služba|✔|✔|-|
|Function App|✔|-|-|
|Cloud Services|✔|-|-|
|Virtual Network|✔|-|-|
|Podsíť|✔|-|-|
|NIC|✔|-|-|
|Skupiny zabezpečení sítě|✔|-|-|
|Předplatné|✔ **|✔|-|
|Účet Batch|✔|-|-|
|Účet Service Fabric|✔|-|-|
|Účet Automation|✔|-|-|
|Load Balancer|✔|-|-|
|Kognitivní vyhledávání|✔|-|-|
|Obor názvů služby Service Bus|✔|-|-|
|Stream Analytics|✔|-|-|
|Obor názvů centra událostí|✔|-|-|
|Logické aplikace|✔|-|-|
|Mezipaměť pro Redis|✔|-|-|
|Analýza Data Lake|✔|-|-|
|Azure Data Lake Storage|✔|-|-|
|Key Vault|✔|✔ *|-|

\* tyto funkce jsou v tuto chvíli ve verzi Preview podporované.

doporučení pro \*\* Azure Active Directory (Azure AD) jsou dostupná jenom pro standardní odběry.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [Security Center shromažďuje data a agenta Log Analytics](security-center-enable-data-collection.md).
- Přečtěte si [, jak Security Center spravuje a chrání data](security-center-data-security.md).
- Naučte se [plánovat a porozumět hlediskům návrhu, které je potřeba přijmout Azure Security Center](security-center-planning-and-operations-guide.md).
- Přečtěte si [platformy, které podporují Security Center](security-center-os-coverage.md).
- Přečtěte si další informace o [detekci hrozeb pro virtuální počítače & serverech v Azure Security Center](security-center-alerts-iaas.md).
- Přečtěte si [Nejčastější dotazy týkající se použití Azure Security Center](security-center-faq.md).
- Přečtěte si [blogové příspěvky o zabezpečení Azure a dodržování předpisů](https://blogs.msdn.com/b/azuresecurity/).
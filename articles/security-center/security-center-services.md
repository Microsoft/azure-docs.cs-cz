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
ms.openlocfilehash: 275598aa50c252512348f4a04543e1beaf538626
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529480"
---
# <a name="supported-features-available-in-azure-security-center"></a>Podporované funkce dostupné v Azure Security Center

> [!NOTE]
>Některé funkce jsou dostupné jenom na úrovni Standard. Pokud jste se ještě nezaregistrovali do úrovně Standard Security Center, k dispozici je bezplatné zkušební období. Další informace najdete na stránce s [cenami Security Center](https://azure.microsoft.com/pricing/details/security-center/).

V následujících částech jsou uvedeny Security Center funkce, které jsou k dispozici pro [podporované platformy](security-center-os-coverage.md).

* [Virtuální počítače a servery](#vm-server-features)
* [Služby PaaS](#paas-services)


## Funkce podporované virtuálním počítačem nebo serverem<a name="vm-server-features"></a>

> [!div class="mx-tableFixed"]

|Server|Windows|||Linux|||Ceny|
|----|----|----|----|----|----|----|----|
|**Prostředí**|**Azure**||**Mimo Azure**|**Azure**||**Mimo Azure**||
||**Virtuální počítač**|**Sada škálování virtuálních počítačů**||**Virtuální počítač**|**Sada škálování virtuálních počítačů**|
|[Integrace ATP v programu Microsoft Defender](https://docs.microsoft.com/azure/security-center/security-center-wdatp)|✔ (na podporovaných verzích)|✔ (na podporovaných verzích)|✔|×|×|×|Úroveň Standard|
|[Výstrahy detekce hrozeb při analýze chování virtuálních počítačů](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas)|✔|✔|✔|✔ (na podporovaných verzích)|✔ (na podporovaných verzích)|✔|Detekce hrozeb (zdarma) (Standard)|
|[Výstrahy detekce hrozeb nejenom pro soubory](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas#fileless-attack-detection-)|✔|✔|✔|×|×|×|Úroveň Standard|
|[Výstrahy detekce hrozeb založené na síti](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer#azure-network-layer)|✔|✔|×|✔|✔|×|Úroveň Standard|
|[Přístup k virtuálnímu počítači za běhu](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)|✔|×|×|✔|×|×|Úroveň Standard|
|[Monitorování integrity souborů](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)|✔|✔|✔|✔|✔|✔|Úroveň Standard|
|[Adaptivní ovládací prvky aplikace](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)|✔|×|✔|✔|×|✔|Úroveň Standard|
|[Mapa sítě](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations#network-map)|✔|✔|×|✔|✔|×|Úroveň Standard|
|[Adaptivní posílení zabezpečení sítě](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)|✔|×|×|✔|×|×|Úroveň Standard|
|Adaptivní ovládací prvky sítě|✔|✔|×|✔|✔|×|Úroveň Standard|
|[Řídicí panel dodržování předpisů & sestavy](https://docs.microsoft.com/azure/security-center/security-center-compliance-dashboard)|✔|✔|✔|✔|✔|✔|Úroveň Standard|
|Doporučení a detekce hrozeb v kontejnerech IaaS hostovaných v Docker|×|×|×|✔|✔|✔|Úroveň Standard|
|Chybí posouzení oprav operačního systému|✔|✔|✔|✔|✔|✔|Zadarmo|
|Vyhodnocení nezabezpečených konfigurací zabezpečení|✔|✔|✔|✔|✔|✔|Zadarmo|
|[Posouzení služby Endpoint Protection](https://docs.microsoft.com/azure/security-center/security-center-services#supported-endpoint-protection-solutions-)|✔|✔|✔|×|×|×|Zadarmo|
|Vyhodnocení šifrování disku|✔|✔|×|✔|✔|×|Zadarmo|
|Posouzení ohrožení zabezpečení třetí strany|✔|×|×|✔|×|×|Zadarmo|
|[Posouzení zabezpečení sítě](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)|✔|✔|×|✔|✔|×|Zadarmo|

### Podporovaná řešení ochrany koncových bodů<a name="endpoint-supported"></a>

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
|CosmosDB|×| ✔|
|Úložiště blobů|✔| ✔|
|Účet úložiště|✔| ×|
|App Service|✔| ✔|
|Funkce|✔| ×|
|Cloudová služba|✔| ×|
|VNet|✔| ×|
|Podsíť|✔| ×|
|NIC|✔| ×|
|NSG|✔| ×|
|Předplatné|✔ * *| ✔|
|Účet Batch|✔| ×|
|Účet Service Fabric|✔| ×|
|Účet Automation|✔| ×|
|Nástroj pro vyrovnávání zatížení|✔| ×|
|Hledat|✔| ×|
|Obor názvů služby Service Bus|✔| ×|
|Stream Analytics|✔| ×|
|Obor názvů centra událostí|✔| ×|
|Logické aplikace|✔| ×|
|Redis|✔| ×|
|Analýza Data Lake|✔| ×|
|Data Lake Store|✔| ×|
|Key Vault|✔| ×|

\* tyto funkce jsou aktuálně podporované ve verzi Public Preview.

doporučení pro \* \* Azure Active Directory (Azure AD) jsou dostupná jenom pro standardní odběry.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [Security Center shromažďuje data a agenta Log Analytics](security-center-enable-data-collection.md).
- Přečtěte si [, jak Security Center spravuje a chrání data](security-center-data-security.md).
- Naučte se [plánovat a porozumět hlediskům návrhu, které je potřeba přijmout Azure Security Center](security-center-planning-and-operations-guide.md).
- Přečtěte si [platformy, které podporují Security Center](security-center-os-coverage.md).
- Přečtěte si další informace o [detekci hrozeb pro virtuální počítače & serverech v Azure Security Center](security-center-alerts-iaas.md).
- Přečtěte si [Nejčastější dotazy týkající se použití Azure Security Center](security-center-faq.md).
- Přečtěte si [blogové příspěvky o zabezpečení Azure a dodržování předpisů](https://blogs.msdn.com/b/azuresecurity/).

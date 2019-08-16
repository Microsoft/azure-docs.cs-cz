---
title: Funkce a platformy podporované aplikací Azure Security Center | Microsoft Docs
description: Tento dokument obsahuje seznam funkcí a platforem podporovaných nástrojem Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/03/2019
ms.author: v-mohabe
ms.openlocfilehash: 9a11af7e2875c9af5cf4b08d459bc67b55dbdcf3
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515547"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Platformy a funkce podporované nástrojem Azure Security Center

Monitorování stavu zabezpečení a doporučení jsou k dispozici pro virtuální počítače, vytvořené pomocí modelu nasazení Classic a Správce prostředků a počítačů.

> [!NOTE]
> Přečtěte si další informace o [modelech nasazení Classic a správce prostředků](../azure-classic-rm.md) pro prostředky Azure.
>

## <a name="platforms-that-support-the-data-collection-agent"></a>Platformy, které podporují agenta shromažďování dat 

Tato část obsahuje seznam platforem, na kterých je možné spustit agenta Azure Security Center a ze kterého může shromažďovat data.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Podporované platformy pro počítače a virtuální počítače s Windows
Podporovány jsou následující operační systémy Windows:

* Windows Server. 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows Server 2008

> [!NOTE]
> Integrace se službou Windows Defender ATP podporuje pouze systémy Windows Server 2012 R2 a Windows Server 2016.

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Podporované platformy pro počítače a virtuální počítače se systémem Linux

Podporovány jsou následující operační systémy Linux:

> [!NOTE]
> Vzhledem k tomu, že se seznam podporovaných operačních systémů Linux neustále mění, pokud chcete, kliknutím [sem](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) zobrazíte nejaktuálnější seznam podporovaných verzí, v případě, že došlo ke změnám od posledního publikování tohoto tématu.

64 bitů
* CentOS 6 a 7
* Amazon Linux 2017.09
* Oracle Linux 6 a 7
* Red Hat Enterprise Linux Server 6 a 7
* Debian GNU/Linux 8 a 9
* Ubuntu Linux 14,04 LTS, 16,04 LTS a 18,04 LTS
* SUSE Linux Enterprise Server 12

32 bitů
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 a 9
* Ubuntu Linux 14,04 LTS a 16,04 LTS

## <a name="vms-and-cloud-services"></a>Virtuální počítače a Cloud Services
Podporují se také virtuální počítače, které běží v cloudové službě. Monitorují se jenom webové role a role pracovních procesů Cloud Services, které běží v produkčních slotech. Další informace o cloudových službách najdete v tématu [Přehled Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Podporované funkce IaaS

> [!div class="mx-tableFixed"]
> 

|Server|Windows|||Linux|||Ceny|
|----|----|----|----|----|----|----|----|
|**Prostředí**|**Azure**||**Non-Azure**|**Azure**||**Non-Azure**||
||**Virtuální počítač**|**Sada škálování virtuálních počítačů**||**Virtuální počítač**|**Sada škálování virtuálních počítačů**|
|Výstrahy detekce hrozeb VMBA|✔|✔|✔|✔ (na podporovaných verzích)|✔ (na podporovaných verzích)|✔|Detekce hrozeb (zdarma) (Standard)|
|Výstrahy detekce hrozeb založené na síti|✔|✔|X|✔|✔|X|Standard|
|Integrace ochrany ATP v programu Windows Defender|✔ (na podporovaných verzích)|✔ (na podporovaných verzích)|✔|X|X|X|Standard|
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

### <a name="supported-endpoint-protection-solutions"></a>Podporovaná řešení ochrany koncových bodů

Následující tabulka poskytuje matrici:
 - Zda můžete k instalaci jednotlivých řešení použít Azure Security Center.
 - Která řešení ochrany koncových bodů Security Center můžou zjistit. Pokud se zjistí jedno z těchto řešení ochrany koncových bodů, Security Center nedoporučuje instalaci.

Informace o tom, kdy jsou pro každou z těchto ochran vygenerována doporučení, najdete v tématu [Endpoint Protection Assessment and Recommendations](security-center-endpoint-protection.md).

| Endpoint Protection| Platformy | Instalace Security Center | Zjišťování Security Center |
|------|------|-----|-----|
| Windows Defender (antimalware Microsoftu)| Windows Server 2016| Ne, součást operačního systému| Ano |
| Ochrana koncových bodů System Center (antimalware Microsoftu) | Windows Server 2012 R2, 2012, 2008 R2 (viz poznámka níže) | Prostřednictvím rozšíření | Ano |
| Trend Micro – všechny verze | Řada Windows Serverů  | Ne | Ano |
| Symantec v12.1.1100+| Řada Windows Serverů  | Ne | Ano |
| McAfee v10+ | Řada Windows Serverů  | Ne | Ano |
| McAfee v10+ | Řada Linux serverů  | Ne | Ano **\*** |
| Sophos v9 +| Řada Linux serverů  | Ne | Ano **\***  |

 **\*** Stav pokrytí a podpůrná data jsou aktuálně k dispozici pouze v pracovním prostoru Log Analytics přidruženém k vašim chráněným předplatným a nereflektují se na portálu Azure Security Center.

> [!NOTE]
> - Detekce služby System Center Endpoint Protection (SCEP) na virtuálním počítači s Windows Serverem 2008 R2 vyžaduje, aby se protokol SCEP nainstaloval po PowerShellu 3,0 (nebo v horní verzi).

## <a name="supported-paas-features"></a>Podporované funkce PaaS


|Služba|Doporučení (zdarma)|Detekce hrozeb (Standard)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Účty služby Azure Blob Storage|✔| ✔|
|Aplikační služby|✔| ✔|
|Cloudové služby|✔| X|
|Virtuální sítě|✔| Není k dispozici|
|Podsítě|✔| Není k dispozici|
|Síťové karty|✔| Není k dispozici|
|skupin NSG|✔| Není k dispozici|
|Subscription|✔ **| ✔|
|Batch|✔| Není k dispozici|
|Service Fabric|✔| Není k dispozici|
|Účet služby Automation|✔| Není k dispozici|
|Nástroj pro vyrovnávání zatížení|✔| Není k dispozici|
|Hledat|✔| Není k dispozici|
|Service Bus|✔| Není k dispozici|
|Stream Analytics|✔| Není k dispozici|
|Centrum událostí|✔| Není k dispozici|
|Aplikace logiky|✔| Není k dispozici|
|Účet úložiště|✔| Není k dispozici|
|Redis|✔| Není k dispozici|
|Data Lake Analytics|✔| Není k dispozici|
|Trezor klíčů|✔| Není k dispozici|




\*Tyto funkce jsou aktuálně podporované ve verzi Public Preview.

\*\*Doporučení AAD jsou dostupná jenom pro standardní odběry.



## <a name="next-steps"></a>Další postup

- Naučte se [plánovat a porozumět hlediskům návrhu, které je potřeba přijmout Azure Security Center](security-center-planning-and-operations-guide.md).
- Přečtěte si další informace o [detekci hrozeb pro virtuální počítače & serverech v Azure Security Center](security-center-alerts-iaas.md).
- Přečtěte si [Nejčastější dotazy týkající se použití Azure Security Center](security-center-faq.md).
- Přečtěte si [blogové příspěvky o zabezpečení Azure a dodržování předpisů](https://blogs.msdn.com/b/azuresecurity/).

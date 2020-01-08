---
title: Detekce hrozeb ve vrstvě služeb Azure – Azure Security Center
description: V tomto tématu jsou uvedeny výstrahy ve vrstvě služeb Azure, které jsou k dispozici v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: d4033989830323856ac14ed06eea7df74806f128
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665693"
---
# <a name="threat-detection-for-the-azure-service-layer-in-azure-security-center"></a>Detekce hrozeb pro vrstvu služby Azure v Azure Security Center

V tomto tématu jsou uvedeny výstrahy Azure Security Center, které jsou k dispozici při monitorování následujících vrstev služeb Azure:

* [Síťová vrstva Azure](#network-layer)
* [Vrstva správy Azure (Azure Resource Manager) (Preview)](#management-layer)
* [Azure Key Vault](#azure-keyvault)

## Síťová vrstva Azure<a name="network-layer"></a>

Analýza Security Centerch síťových vrstev vychází z ukázkových [dat IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), což jsou hlavičky paketů shromážděné směrovači Azure Core. Na základě tohoto datového kanálu používá Security Center k identifikaci a označení aktivit škodlivého provozu modely strojového učení. Security Center k rozšíření IP adres používá taky databázi Microsoft Threat Intelligence.

Některé konfigurace sítě mohou omezit Security Center generování výstrah na podezřelé síťové aktivity. Pokud Security Center chcete generovat výstrahy sítě, zajistěte, aby:

- Váš virtuální počítač má veřejnou IP adresu (nebo se nachází ve službě Vyrovnávání zatížení s veřejnou IP adresou).

- Externí řešení identifikátorů ID neblokuje provoz odchozího výstupu sítě vašeho virtuálního počítače.

- Vašemu virtuálnímu počítači se přiřadila stejná IP adresa pro celou hodinu, během které došlo k podezřelé komunikaci. To platí také pro virtuální počítače vytvořené jako součást spravované služby (např. AKS, datacihly).

Seznam výstrah síťových vrstev Azure najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-azurenetlayer).

Podrobnosti o tom, jak Security Center můžou použít signály související se sítí pro použití ochrany před hrozbami, najdete [v tématu heuristické detekce služby DNS v Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).


## Vrstva správy Azure (Azure Resource Manager) (Preview)<a name ="management-layer"></a>

Vrstva ochrany Security Center založená na Azure Resource Manager je momentálně ve verzi Preview.

Security Center nabízí další vrstvu ochrany pomocí Azure Resource Managerch událostí, které se považují za řídicí plochu pro Azure. Díky analýze záznamů Azure Resource Manager Security Center detekuje neobvyklé nebo potenciálně škodlivé operace v prostředí předplatného Azure.

Seznam výstrah Azure Resource Manager (Preview) najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-azureresourceman).



>[!NOTE]
> Některé z předchozích analýz jsou napájené z Microsoft Cloud App Security. Pokud chcete tyto analýzy využít, musíte aktivovat licenci Cloud App Security. Pokud máte licenci Cloud App Security, jsou tyto výstrahy ve výchozím nastavení povolené. Chcete-li je zakázat:
>
> 1. V okně **Security Center** vyberte **zásady zabezpečení**. U předplatného, které chcete změnit, vyberte **Upravit nastavení**.
> 2. Vyberte **detekci hrozeb**.
> 3. V části **Povolit integrace**zrušte zaškrtnutí políčka **Povolit Microsoft Cloud App Security k přístupu k datům**a vyberte **Uložit**.

>[!NOTE]
>Security Center ukládá zákaznická data týkající se zabezpečení ve stejné geografické podobě jako její prostředek. Pokud společnost Microsoft ještě nebyla nasazena Security Center v geografickém prostředku, uloží data do USA. Pokud je povolená Cloud App Security, ukládají se tyto informace v souladu s pravidly geografického umístění Cloud App Security. Další informace najdete v tématu [úložiště dat pro jiné než regionální služby](https://azuredatacentermap.azurewebsites.net/).

## Azure Key Vault (Preview)<a name="azure-keyvault"></a>

Azure Key Vault je cloudová služba, která chrání šifrovací klíče a tajné kódy, jako jsou certifikáty, připojovací řetězce a hesla. 

Azure Security Center zahrnuje rozšířenou ochranu před internetovými útoky Azure pro Azure Key Vault a poskytuje další vrstvu funkcí Security Intelligence. Security Center detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům Key Vault nebo jejich zneužití. Tato vrstva ochrany umožňuje řešit hrozby bez nutnosti provádět odborníky na zabezpečení a bez nutnosti spravovat systémy monitorování zabezpečení třetích stran.  

Když dojde k neobvyklé aktivitám, Security Center zobrazuje výstrahy a volitelně je odesílá prostřednictvím e-mailu správcům předplatného. Tyto výstrahy obsahují podrobnosti o podezřelé aktivitě a doporučení ohledně toho, jak tyto hrozby prozkoumat a opravit. 

> [!NOTE]
> Tato služba není aktuálně dostupná v oblastech cloudu Azure pro státní správu a svrchované oblasti.

Seznam výstrah Azure Key Vault najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-azurekv).

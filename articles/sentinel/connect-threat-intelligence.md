---
title: Shromažďovat data analýzy hrozeb v Azure Preview Sentinelu | Dokumentace Microsoftu
description: Další informace o tom, jak připojit k Azure ověřovací data analýzy hrozeb.
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 56412543-5664-44c1-b026-2dbaf78a9a50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 2c5a7dc08886e21ef8e287540d9139ec555b11a2
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242288"
---
# <a name="collect-data-from-threat-intelligence-providers"></a>Shromažďování dat od poskytovatelů analýzy hrozeb 

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Až budete Streamovat data do Azure Sentinelu, obohatit ho s hrozeb informačního kanálu, který používáte ve vaší organizaci. 

Umožňuje křížové obraťte se na hodnotu true hrozeb, například Pokud dostanete výstrahu od konkrétní IP adresu, výstrahy a pravidla integraci vašich threat intelligence poskytovatele úspěšnost dali vám vědět, pokud tuto IP adresu bylo nedávno zjištěno škodlivé , Umožňuje integraci s azure Sentinel [poskytovatelů analýzy hrozeb](https://aka.ms/graphsecuritytips). 

Do Azure Sentinelu jediným kliknutím můžete Streamovat protokoly z poskytovatelů analýzy hrozeb. Toto připojení umožňuje začlenit ukazatele, který obsahuje různé typy pozorovatelné objekty, jako je například IP adresa, domény, adresa URL a hodnota hash souboru, vyhledávání a vytvářet vlastní výstrahy v Azure ověřovacích pravidel.  

## <a name="prerequisites"></a>Požadavky  

- Uživatel se oprávnění správce zabezpečení nebo globální správce 

- Threat intelligence aplikace integrované s Microsoft Intelligent Security Graph 

## <a name="connect-to-threat-intelligence"></a>Připojte se k analýzy hrozeb 

1. Pokud už používáte zprostředkovatele threat intelligence, ujistěte se, přejděte do vaší aplikace TIP a udělte oprávnění indikátory poslat Microsoftu a určete služby jako Azure Sentinelu.  

2. V Azure Sentinelu, vyberte **shromažďování dat** a potom klikněte na tlačítko **hrozeb** dlaždici.

3. Klikněte na **Připojit**. 

4. Použít příslušné schéma v Log Analytics pro kanálů analýzy hrozeb, vyhledejte **ThreatIntelligenceIndicator**. 

 
## <a name="next-steps"></a>Další postup

V tomto dokumentu jste zjistili, jak připojit poskytovatele analýzy hrozeb v Azure Sentinelu. Další informace o Azure Sentinelu, naleznete v následujících článcích.

- Abyste mohli začít s Azure Sentinelu, budete potřebovat předplatné Microsoft Azure. Pokud předplatné nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
- Zjistěte, jak [připojit vaše data do Azure Sentinelu](quickstart-onboard.md), a [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).

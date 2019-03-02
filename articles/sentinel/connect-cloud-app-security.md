---
title: Shromažďování dat v Cloud App Security ve verzi Preview Sentinelu Azure | Dokumentace Microsoftu
description: Zjistěte, jak shromažďovat data o Cloud App Security v ověřovacích Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: cd9e5e27-fdd4-4717-8924-be4c1c430f23
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: b0033f5f8636053f88825541b8b2cfcbf2fc9f8b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245484"
---
# <a name="collect-data-from-microsoft-cloud-app-security"></a>Shromažďování dat z Microsoft Cloud App Security 

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Můžete Streamovat protokoly z [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) do Azure Sentinelu jediným kliknutím. Toto připojení umožňuje streamování upozornění z Cloud App Security do ověřovacích Azure. 

## <a name="prerequisites"></a>Požadavky

- Uživatel se oprávnění správce zabezpečení nebo globální správce

## <a name="connect-to-cloud-app-security"></a>Připojte se ke Cloud App Security

Pokud už máte Cloud App Security, ujistěte se, že je [povolena ve vaší síti](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Když Cloud App Security se nasazuje a ingestovat data, data upozornění můžete snadno Streamovat do ověřovacích Azure.


1. V Azure Sentinelu, vyberte **shromažďování dat** a potom klikněte na tlačítko **Cloud App Security** dlaždici.

2. Klikněte na **Připojit**.

3. Chcete-li použít příslušné schéma v Log Analytics pro upozornění Cloud App Security, vyhledejte **SecurityAlert**.


## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak se připojit k Azure Sentinelu Microsoft Cloud App Security. Další informace o Azure Sentinelu, naleznete v následujících článcích:
- Zjistěte, jak [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
- Začínáme [detekuje hrozby s využitím Azure Sentinelu](tutorial-detect-threats.md).

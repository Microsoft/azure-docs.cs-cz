---
title: Propojení dat v Cloud App Security Sentinelu ve verzi Preview Azure | Dokumentace Microsoftu
description: Informace o připojení Cloud App Security data pro ověřovací Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: cd9e5e27-fdd4-4717-8924-be4c1c430f23
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: a418bb318654752eaf48ffbdd05b80cabb487ece
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65207550"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Připojíte si data z Microsoft Cloud App Security 

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Můžete Streamovat protokoly z [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) do Azure Sentinelu jediným kliknutím. Toto připojení umožňuje streamování upozornění z Cloud App Security do ověřovacích Azure. 

## <a name="prerequisites"></a>Požadavky

- Uživatel se oprávnění správce zabezpečení nebo globální správce

## <a name="connect-to-cloud-app-security"></a>Připojte se ke Cloud App Security

Pokud už máte Cloud App Security, ujistěte se, že je [povolena ve vaší síti](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Když Cloud App Security se nasazuje a ingestovat data, data upozornění můžete snadno Streamovat do ověřovacích Azure.


1. V Azure Sentinelu, vyberte **datové konektory** a potom klikněte na tlačítko **Cloud App Security** dlaždici.

2. Klikněte na **Připojit**.

3. Chcete-li použít příslušné schéma v Log Analytics pro upozornění Cloud App Security, vyhledejte **SecurityAlert**.


## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak se připojit k Azure Sentinelu Microsoft Cloud App Security. Další informace o Azure Sentinelu, naleznete v následujících článcích:
- Zjistěte, jak [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
- Začínáme [detekuje hrozby s využitím Azure Sentinelu](tutorial-detect-threats.md).

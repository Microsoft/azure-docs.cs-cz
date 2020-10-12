---
title: Připojte Microsoft Defender pro data identity (dřív Azure ATP) do Azure Sentinel | Microsoft Docs
description: Přečtěte si, jak streamovat protokoly z Microsoft Defenderu na identitu (dříve Azure Advanced Threat Protection) do Azure Sentinel jediným kliknutím.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 64c1a7155d0cc4e80f97db138a0626d6e9fdc9e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89657819"
---
# <a name="connect-data-from-microsoft-defender-for-identity-formerly-azure-advanced-threat-protection"></a>Připojení dat z programu Microsoft Defender k identitě (dříve Rozšířená ochrana před internetovými útoky Azure)

> [!IMPORTANT]
> Microsoft Defender pro data Connector ve službě Azure Sentinel je momentálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pomocí jediného kliknutí můžete streamovat protokoly z [Microsoft Defenderu pro identitu](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) do Azure Sentinel.

## <a name="prerequisites"></a>Požadavky

- Uživatel s oprávněními globálního správce nebo správce zabezpečení
- Musíte být zákazníkem verze Preview programu Microsoft Defender pro identitu a povolit integraci mezi Microsoft Defenderem a identitou a Microsoft Cloud App Security. Další informace najdete v tématu [Microsoft Defender pro integraci identity](https://docs.microsoft.com/cloud-app-security/aatp-integration).

## <a name="connect-to-microsoft-defender-for-identity"></a>Připojení k programu Microsoft Defender pro identitu

Ujistěte se, že je [ve vaší síti povolená](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)verze Preview programu Microsoft Defender for identity.
Pokud je program Microsoft Defender pro identitu nasazený a ingestuje vaše data, můžou být podezřelé výstrahy snadno streamované do Azure Sentinel. Může trvat až 24 hodin, než se výstrahy spustí streamování do Azure Sentinel.


1. Aby bylo možné připojit Microsoft Defender k identitě do Azure Sentinel, musíte nejprve povolit integraci mezi Microsoft Defenderem a identitou a Microsoft Cloud App Security. Informace o tom, jak to provést, najdete v tématu [Microsoft Defender pro integraci identity](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. V Azure Sentinel vyberte **datové konektory** a potom klikněte na dlaždici **Microsoft Defender pro identitu (Preview)** .

1. Můžete vybrat, zda chcete, aby výstrahy z programu Microsoft Defender pro identitu automaticky generovaly incidenty v rámci služby Azure Sentinel automaticky. V části **vytvořit incidenty** vyberte **Povolit** , pokud chcete povolit výchozí analytické pravidlo, které automaticky vytvoří incidenty z výstrah vygenerovaných v připojené službě zabezpečení. Toto pravidlo pak můžete upravit v části **Analýza** a pak na **aktivní pravidla**.

1. Klikněte na **Připojit**.

1. Pokud chcete použít příslušné schéma v Log Analytics pro program Microsoft Defender pro výstrahy identity, vyhledejte **SecurityAlert**.

> [!NOTE]
> Pokud jsou výstrahy větší než 30 KB, Azure Sentinel přestane zobrazovat pole entity v upozorněních.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit Microsoft Defender k identitě do Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).


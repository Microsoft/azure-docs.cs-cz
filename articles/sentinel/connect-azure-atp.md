---
title: Připojení dat Azure ATP k Azure Sentinel | Microsoft Docs
description: Naučte se připojit data ATP Azure ke službě Azure Sentinel.
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
ms.openlocfilehash: 387d04cbbb125006efcc4efc53a02015fe3f5919
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588582"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Připojení dat z Azure Advanced Threat Protection (ATP)

> [!IMPORTANT]
> Datový konektor Azure Advanced Threat Protection ve službě Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Protokoly z [rozšířené ochrany před internetovými útoky Azure](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) můžete streamovat do Azure Sentinel jediným kliknutím.

## <a name="prerequisites"></a>Požadavky

- Uživatel s oprávněními globálního správce nebo správce zabezpečení
- Musíte být ve verzi Preview služby Azure ATP a povolit integraci mezi Azure ATP a Microsoft Cloud App Security. Další informace najdete v tématu [integrace rozšířené ochrany Azure](https://docs.microsoft.com/cloud-app-security/aatp-integration).

## <a name="connect-to-azure-atp"></a>Připojení k Azure ATP

Ujistěte se, že je [ve vaší síti povolená](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)verze Preview služby Azure atp.
Pokud je Azure ATP nasazený a ingestuje vaše data, můžou být podezřelé výstrahy snadno streamované do Azure Sentinel. Může trvat až 24 hodin, než se výstrahy spustí streamování do Azure Sentinel.


1. Pokud chcete připojit Azure ATP ke službě Azure Sentinel, musíte nejdřív povolit integraci mezi Azure ATP a Microsoft Cloud App Security. Informace o tom, jak to udělat, najdete v tématu [Integrace Azure Advanced Threat Protection](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. V Azure Sentinel vyberte **datové konektory** a pak klikněte na dlaždici **Azure Advanced Threat Protection (Preview)** .

1. Můžete vybrat, jestli chcete, aby výstrahy z Azure ATP automaticky generovaly incidenty v rámci služby Azure Sentinel automaticky. V části **vytvořit incidenty** vyberte **Povolit** , pokud chcete povolit výchozí analytické pravidlo, které automaticky vytvoří incidenty z výstrah vygenerovaných v připojené službě zabezpečení. Toto pravidlo pak můžete upravit v části **Analýza** a pak na **aktivní pravidla**.

1. Klikněte na **Připojit**.

1. Pokud chcete použít příslušné schéma v Log Analytics pro výstrahy Azure ATP, vyhledejte **SecurityAlert**.

> [!NOTE]
> Pokud jsou výstrahy větší než 30 KB, Azure Sentinel přestane zobrazovat pole entity v upozorněních.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit Azure Advanced Threat Protection ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).


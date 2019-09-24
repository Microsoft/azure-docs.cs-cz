---
title: Připojení dat Azure ATP k Azure Sentinel | Microsoft Docs
description: Naučte se připojit data ATP Azure ke službě Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: ebb727055296ba7886a9307ada113ab5a6e0c9e0
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240191"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp---preview"></a>Připojení dat z Azure Advanced Threat Protection (ATP) – Preview




Protokoly z [rozšířené ochrany před internetovými útoky Azure](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) můžete streamovat do Azure Sentinel jediným kliknutím.

## <a name="prerequisites"></a>Požadavky

- Uživatel s oprávněními globálního správce nebo správce zabezpečení
- Musíte být ve verzi Preview služby Azure ATP.

## <a name="connect-to-azure-atp"></a>Připojení k Azure ATP

Ujistěte se, že je [ve vaší síti povolená](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)verze Preview služby Azure atp.
Pokud je Azure ATP nasazený a ingestuje vaše data, můžou být podezřelé výstrahy snadno streamované do Azure Sentinel. Může trvat až 24 hodin, než se výstrahy spustí streamování do Azure Sentinel.


1. Pokud chcete připojit Azure ATP ke službě Azure Sentinel, musíte nejdřív povolit integraci mezi Azure ATP a Microsoft Cloud App Security. Informace o tom, jak to udělat, najdete v tématu [Integrace Azure Advanced Threat Protection](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. V Azure Sentinel vyberte **datové konektory** a potom klikněte na dlaždici **ATP Azure** .

1. Můžete vybrat, jestli chcete, aby výstrahy z Azure ATP automaticky generovaly incidenty v rámci služby Azure Sentinel automaticky. V části **vytvořit incidenty** vyberte **Povolit** , pokud chcete povolit výchozí analytické pravidlo, které automaticky vytvoří incidenty z výstrah vygenerovaných v připojené službě zabezpečení. Toto pravidlo pak můžete upravit v části **Analýza** a pak na **aktivní pravidla**.

1. Klikněte na **Připojit**.

1. Pokud chcete použít příslušné schéma v Log Analytics pro výstrahy Azure ATP, vyhledejte **SecurityAlert**.

> [!NOTE]
> Pokud jsou výstrahy větší než 30 KB, Azure Sentinel přestane zobrazovat pole entity v upozorněních.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit Azure Advanced Threat Protection ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).


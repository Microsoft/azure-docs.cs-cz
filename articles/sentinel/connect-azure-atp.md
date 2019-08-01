---
title: Připojení dat Azure ATP ke službě Azure Sentinel Preview | Microsoft Docs
description: Naučte se připojit data ATP Azure ke službě Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5bf3cc44-ecda-4c78-8a63-31ab42f43605
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/28/2019
ms.author: rkarlin
ms.openlocfilehash: 6f41a5704c783ba8aeab7bc2e82ef731c6a257ac
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599151"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Připojení dat z Azure Advanced Threat Protection (ATP)

> [!IMPORTANT]
> Služba Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Protokoly z [rozšířené ochrany před internetovými útoky Azure](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) můžete streamovat do Azure Sentinel jediným kliknutím.

## <a name="prerequisites"></a>Požadavky

- Uživatel s oprávněními globálního správce nebo správce zabezpečení
- Musíte být ve verzi Preview služby Azure ATP.

## <a name="connect-to-azure-atp"></a>Připojení k Azure ATP

Ujistěte se, že je [ve vaší síti povolená](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)verze Preview služby Azure atp.
Pokud je Azure ATP nasazený a ingestuje vaše data, můžou být podezřelé výstrahy snadno streamované do Azure Sentinel. Může trvat až 24 hodin, než se výstrahy spustí streamování do Azure Sentinel.


1. Pokud chcete připojit Azure ATP ke službě Azure Sentinel, musíte nejdřív povolit integraci mezi Azure ATP a Microsoft Cloud App Security. Informace o tom, jak to udělat, najdete v tématu [Integrace Azure Advanced Threat Protection](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. V Azure Sentinel vyberte **datové konektory** a potom klikněte na dlaždici **ATP Azure** .

2. Klikněte na **Připojit**.

6. Pokud chcete použít příslušné schéma v Log Analytics pro výstrahy Azure ATP, vyhledejte **SecurityAlert**.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit Azure Advanced Threat Protection ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats.md).


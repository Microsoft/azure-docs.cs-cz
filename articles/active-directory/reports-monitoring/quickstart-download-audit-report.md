---
title: Rychlý start stažení sestavy auditování na portálu Azure Portal| Microsoft Docs
description: Zjistěte, jak stáhnout sestavu auditování pomocí portálu Azure Portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4de121ea-f4aa-4c8a-aae4-700c2c5e97a2
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6cbea49fe39c92c8a2fc50e501cb4ef5cff74b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "68989682"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>Rychlý start: Stažení sestavy auditování pomocí portálu Azure Portal

V tomto rychlém startu se dozvíte, jak stáhnout soubor CSV protokolů auditu pro vašeho tenanta za posledních 24 hodin. Z Azure Portal můžete stáhnout záznamy až 250 000. Záznamy jsou seřazené podle nejnovějších, takže ve výchozím nastavení získáte nejnovější záznamy 250 000. 

## <a name="prerequisites"></a>Požadavky

Budete potřebovat:

* Tenanta Azure Active Directory. 
* Uživatel, který je v roli **Správce zabezpečení**, **Čtenář zabezpečení** nebo globální role **správce** pro tenanta. Každý uživatel v tenantovi může navíc použít vlastní protokoly auditu.

## <a name="quickstart-download-an-audit-report"></a>Rychlý start: Stažení sestavy auditování

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Z levého navigačního podokna vyberte **Azure Active Directory** a pomocí tlačítka **Přepnout adresář** vyberte aktivní adresář.
3. Z řídicího panelu vyberte **Azure Active Directory** a pak vyberte **Protokoly auditu**. 
4. Pokud chcete zobrazit protokoly auditu za posledních 24 hodin, v rozevíracím seznamu filtru **Rozsah data** zvolte **Posledních 24 hodin** a vyberte **Použít**. 
5. Vyberte tlačítko **Stáhnout** , jako formát souboru vyberte **CSV** a zadejte název souboru, ze kterého se má stáhnout soubor CSV obsahující filtrované záznamy. 

![Vytváření sestav](./media/quickstart-download-audit-report/download-audit-logs.png)

## <a name="next-steps"></a>Další kroky

* [Sestavy aktivit přihlašování na portálu Azure Active Directory](concept-sign-ins.md)
* [Uchování sestav v Azure Active Directory](reference-reports-data-retention.md)
* [Latence generování sestav v Azure Active Directory](reference-reports-latencies.md)

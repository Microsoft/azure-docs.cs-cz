---
title: Rychlý start stažení sestavy auditování na portálu Azure Portal| Microsoft Docs
description: Zjistěte, jak stáhnout sestavu auditování pomocí portálu Azure Portal
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4de121ea-f4aa-4c8a-aae4-700c2c5e97a2
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 09/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d6ecf62b46c25ec7cf4ab80219d1bc220e4eb4cc
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46367402"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>Rychlý start: Stažení sestavy auditování pomocí portálu Azure Portal

V tomto rychlém startu se dozvíte, jak stáhnout protokoly auditu pro vašeho tenanta za posledních 24 hodin.

## <a name="prerequisites"></a>Požadavky

Budete potřebovat:

* Tenanta Azure Active Directory 
* Uživatele, který je v roli správce zabezpečení, čtenáře zabezpečení nebo globálního správce pro tenanta. Každý uživatel v tenantovi může navíc použít vlastní protokoly auditu.

## <a name="quickstart-download-an-audit-report"></a>Rychlý start: Stažení sestavy auditování

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Z levého navigačního podokna vyberte **Azure Active Directory** a pomocí tlačítka **Přepnout adresář** vyberte aktivní adresář.
3. Z řídicího panelu vyberte **Azure Active Directory** a pak vyberte **Protokoly auditu**. 
4. Pokud chcete zobrazit protokoly auditu za posledních 24 hodin, v rozevíracím seznamu filtru **Rozsah data** zvolte **Posledních 24 hodin** a vyberte **Použít**. 
5. Pokud chcete stáhnout soubor CSV obsahující filtrované záznamy, klikněte na tlačítko **Stáhnout**. 

![Vytváření sestav](./media/quickstart-download-audit-report/download-audit-logs.png)

## <a name="next-steps"></a>Další kroky

* [Sestavy aktivit přihlašování na portálu Azure Active Directory](concept-sign-ins.md)
* [Uchování sestav v Azure Active Directory](reference-reports-data-retention.md)
* [Latence generování sestav v Azure Active Directory](reference-reports-latencies.md)

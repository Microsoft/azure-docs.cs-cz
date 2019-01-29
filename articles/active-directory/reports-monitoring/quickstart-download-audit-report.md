---
title: Rychlý start stažení sestavy auditování na portálu Azure Portal| Microsoft Docs
description: Zjistěte, jak stáhnout sestavu auditování pomocí portálu Azure Portal
services: active-directory
documentationcenter: ''
author: priyamohanram
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
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: e589f613eb3afc8efe409773f37a9855f8fc5432
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180327"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>Rychlý start: Stáhněte si sestavu auditování pomocí webu Azure portal

V tomto rychlém startu se dozvíte, jak stáhnout protokoly auditu pro vašeho tenanta za posledních 24 hodin. Až 5000 záznamů si můžete stáhnout z webu Azure portal. Záznamy jsou seřazeny podle většinu poslední, tak ve výchozím nastavení, můžete získat nejnovější 5000 záznamů. 

## <a name="prerequisites"></a>Požadavky

Budete potřebovat:

* Tenanta Azure Active Directory. 
* Uživatel, který je v **správce zabezpečení**, **Čtenář zabezpečení**, nebo **globálního správce** role pro příslušného tenanta. Každý uživatel v tenantovi může navíc použít vlastní protokoly auditu.

## <a name="quickstart-download-an-audit-report"></a>Rychlý start: Stažení sestavy auditu

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Z levého navigačního podokna vyberte **Azure Active Directory** a pomocí tlačítka **Přepnout adresář** vyberte aktivní adresář.
3. Z řídicího panelu vyberte **Azure Active Directory** a pak vyberte **Protokoly auditu**. 
4. Pokud chcete zobrazit protokoly auditu za posledních 24 hodin, v rozevíracím seznamu filtru **Rozsah data** zvolte **Posledních 24 hodin** a vyberte **Použít**. 
5. Pokud chcete stáhnout soubor CSV obsahující filtrované záznamy, klikněte na tlačítko **Stáhnout**. 

![Vytváření sestav](./media/quickstart-download-audit-report/download-audit-logs.png)

## <a name="next-steps"></a>Další postup

* [Sestavy aktivit přihlašování na portálu Azure Active Directory](concept-sign-ins.md)
* [Uchování sestav v Azure Active Directory](reference-reports-data-retention.md)
* [Latence generování sestav v Azure Active Directory](reference-reports-latencies.md)

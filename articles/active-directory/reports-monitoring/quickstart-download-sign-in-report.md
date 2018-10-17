---
title: 'Rychlý start: Stažení sestavy přihlašování na webu Azure Portal | Microsoft Docs'
description: Zjistěte, jak stáhnout sestavu přihlašování na webu Azure Portal
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 9131f208-1f90-4cc1-9c29-085cacd69317
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 06/21/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 334649dbe315cb54ba95a29f4c69441fcd2c3417
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46362965"
---
# <a name="quickstart-download-a-sign-in-report-using-the-azure-portal"></a>Rychlý start: Stažení sestavy přihlašování na webu Azure Portal

V tomto rychlém startu se dozvíte, jak stáhnout data přihlašování pro vašeho tenanta za posledních 24 hodin.

## <a name="prerequisites"></a>Požadavky

Budete potřebovat:

* Tenanta Azure Active Directory s licencí Premium k zobrazení sestavy aktivit přihlašování. 
* Uživatele, který je v roli Správce zabezpečení, Čtenář zabezpečení, Čtenář sestav nebo Globální správce pro tohoto tenanta. Každý uživatel v tenantovi může navíc přistupovat k vlastním přihlášením.

## <a name="quickstart-download-a-sign-in-report"></a>Rychlý start: Stažení sestavy přihlašování

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Z levého navigačního podokna vyberte **Azure Active Directory** a pomocí tlačítka **Přepnout adresář** vyberte aktivní adresář.
3. Z řídicího panelu vyberte **Azure Active Directory** a pak vyberte **Přihlášení**. 
4. Pokud chcete zobrazit přihlášení za posledních 24 hodin, ve filtru rozevíracího seznamu **Datum** zvolte **Posledních 24 hodin** a vyberte **Použít**. 
5. Pokud chcete stáhnout soubor CSV obsahující filtrované záznamy, klikněte na tlačítko **Stáhnout**. 

![Vytváření sestav](./media/quickstart-download-sign-in-report/download-sign-ins.png)

## <a name="next-steps"></a>Další kroky

* [Sestavy aktivit přihlašování na portálu Azure Active Directory](concept-sign-ins.md)
* [Uchování sestav v Azure Active Directory](reference-reports-data-retention.md)
* [Latence generování sestav v Azure Active Directory](reference-reports-latencies.md)

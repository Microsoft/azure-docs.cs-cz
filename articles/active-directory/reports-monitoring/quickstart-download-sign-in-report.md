---
title: 'Rychlý start: Stažení sestavy přihlašování na webu Azure Portal | Microsoft Docs'
description: Zjistěte, jak stáhnout sestavu přihlašování na webu Azure Portal
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 9131f208-1f90-4cc1-9c29-085cacd69317
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeb5a06aa4eb21ba374344623f9f96ce92a59ff5
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56168813"
---
# <a name="quickstart-download-a-sign-in-report-using-the-azure-portal"></a>Rychlý start: Stažení sestavy přihlášení pomocí webu Azure portal

V tomto rychlém startu se dozvíte, jak stáhnout data přihlašování pro vašeho tenanta za posledních 24 hodin. Až 5000 záznamů si můžete stáhnout z webu Azure portal. Záznamy jsou seřazeny podle většinu poslední, tak ve výchozím nastavení, můžete získat nejnovější 5000 záznamů. 

## <a name="prerequisites"></a>Požadavky

Budete potřebovat:

* Tenanta Azure Active Directory s licencí Premium k zobrazení sestavy aktivit přihlašování. Zobrazit [Začínáme se službou Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) upgradovat edici Azure Active Directory. Všimněte si, že pokud nemáte žádná data aktivity před upgradem, bude trvat několik dní daná data zobrazit v sestavách po upgradu na licenci premium.
* Uživatel, který je v **správce zabezpečení**, **Čtenář zabezpečení**, **čtenáře sestav** nebo **globálního správce** role pro příslušného tenanta. Každý uživatel v tenantovi může navíc přistupovat k vlastním přihlášením.

## <a name="quickstart-download-a-sign-in-report"></a>Rychlý start: Stažení sestavy přihlašování

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Z levého navigačního podokna vyberte **Azure Active Directory** a pomocí tlačítka **Přepnout adresář** vyberte aktivní adresář.
3. Z řídicího panelu vyberte **Azure Active Directory** a pak vyberte **Přihlášení**. 
4. Pokud chcete zobrazit přihlášení za posledních 24 hodin, ve filtru rozevíracího seznamu **Datum** zvolte **Posledních 24 hodin** a vyberte **Použít**. 
5. Pokud chcete stáhnout soubor CSV obsahující filtrované záznamy, klikněte na tlačítko **Stáhnout**. 

![Vytváření sestav](./media/quickstart-download-sign-in-report/download-sign-ins.png)

## <a name="next-steps"></a>Další postup

* [Sestavy aktivit přihlašování na portálu Azure Active Directory](concept-sign-ins.md)
* [Uchování sestav v Azure Active Directory](reference-reports-data-retention.md)
* [Latence generování sestav v Azure Active Directory](reference-reports-latencies.md)

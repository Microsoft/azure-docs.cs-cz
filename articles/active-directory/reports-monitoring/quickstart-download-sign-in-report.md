---
title: 'Rychlý start: Stažení sestavy přihlašování na webu Azure Portal | Microsoft Docs'
description: Zjistěte, jak stáhnout sestavu přihlašování na webu Azure Portal
services: active-directory
documentationcenter: ''
author: MarkusVi
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
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86bc72f69903134afa3750ad6b72486a713b6cc0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "68989662"
---
# <a name="quickstart-download-a-sign-in-report-using-the-azure-portal"></a>Rychlý start: Stažení sestavy přihlašování na webu Azure Portal

V tomto rychlém startu se dozvíte, jak stáhnout data přihlašování pro vašeho tenanta za posledních 24 hodin. Z webu Azure Portal si můžete stáhnout až 250 000 záznamů. Záznamy jsou seřazeny podle nejnovějších, takže ve výchozím nastavení získáte nejnovějších 250 000 záznamů. 

## <a name="prerequisites"></a>Požadavky

Budete potřebovat:

* Tenanta Azure Active Directory s licencí Premium k zobrazení sestavy aktivit přihlašování. Podívejte [se na téma Začínáme s Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) a upgradujte edici Azure Active Directory. Všimněte si, že pokud jste před upgradem neměli žádné údaje o aktivitách, bude trvat několik dní, než se data zobrazí v sestavách po upgradu na prémiovou licenci.
* Uživatel, který je v **roli Správce zabezpečení**, **Čtečka zabezpečení**, **Čtečka sestav** nebo **Globální správce** pro klienta. Každý uživatel v tenantovi může navíc přistupovat k vlastním přihlášením.

## <a name="quickstart-download-a-sign-in-report"></a>Rychlý start: Stažení sestavy přihlašování

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Z levého navigačního podokna vyberte **Azure Active Directory** a pomocí tlačítka **Přepnout adresář** vyberte aktivní adresář.
3. Z řídicího panelu vyberte **Azure Active Directory** a pak vyberte **Přihlášení**. 
4. Pokud chcete zobrazit přihlášení za posledních 24 hodin, ve filtru rozevíracího seznamu **Datum** zvolte **Posledních 24 hodin** a vyberte **Použít**. 
5. Vyberte tlačítko **Stáhnout,** jako formát souboru vyberte **CSV** a zadejte název souboru pro stažení souboru CSV obsahujícího filtrované záznamy. 

![Vytváření sestav](./media/quickstart-download-sign-in-report/download-sign-ins.png)

## <a name="next-steps"></a>Další kroky

* [Sestavy aktivit přihlašování na portálu Azure Active Directory](concept-sign-ins.md)
* [Uchování sestav v Azure Active Directory](reference-reports-data-retention.md)
* [Latence generování sestav v Azure Active Directory](reference-reports-latencies.md)

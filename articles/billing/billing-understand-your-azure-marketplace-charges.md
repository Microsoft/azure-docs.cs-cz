---
title: Vysvětlení Azure poplatků za externí služby | Dokumentace Microsoftu
description: Další informace o fakturaci externích služeb, dřív označované jako Marketplace, poplatky v Azure.
services: ''
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.assetid: 5e0e2a3c-d111-4054-8508-0c111c1b749b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: cwatson
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0e90c043976fc1798f675f8bf480b02e0ed93536
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52581914"
---
# <a name="understand-your-azure-billing-for-external-service-charges"></a>Porozumět fakturaci Azure pro poplatků za externí služby
Externí služby se publikují dodavateli softwaru třetích stran na Azure Marketplace. Například SendGrid je externích služeb, které si můžete koupit v Azure, ale nebyl vydán microsoftem.

Když si zřídíte nový externí službě nebo prostředku, upozornění se zobrazí:

![Marketplace zakoupit upozornění](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

> [!NOTE]
> Externí služby k publikování aplikací společnosti, které nejsou Microsoft, ale někdy produkty společnosti Microsoft jsou taky zařazený do kategorie jako externí služby.
> 
> 

## <a name="how-external-services-are-billed"></a>Jak se účtují externí služby
- Externí služby se účtují zvlášť. Jsou považovány za jednotlivé objednávky v rámci vašeho předplatného Azure. Fakturačního období pro každou službu je nastaven při nákupu této služby. Neměl by se zaměňovat s fakturační období předplatného, ve které jste zakoupili. Obdržíte také samostatné faktury a vaší platební karty se účtuje zvlášť.
- Každá externí služba má jiný model fakturace. Některé služby se účtují způsobem s průběžnými platbami, zatímco v jiných měsíční platby na základě modelu. Budete potřebovat platební kartu pro externí služby Azure, nelze nakupovat externích služeb s platbami faktury.
- Měsíční bezplatný kredit nelze použít pro externí služby. Pokud používáte předplatné Azure, které zahrnuje [bezplatné kredity](https://azure.microsoft.com/pricing/spending-limits/), nejde použít u externí služba účtuje. Použití platební karty nákupu externích služeb.

## <a name="view-external-service-spending-and-history-in-the-azure-portal"></a>Zobrazení externí služby útraty a historie na portálu Azure portal
Můžete zobrazit seznam externích služeb, které jsou v jednotlivých předplatných v rámci [webu Azure portal](https://portal.azure.com/): 

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako správce účtu.
2. V nabídce centra vyberte **předplatná**.
   
    ![V nabídce centra vyberte předplatná](./media/billing-understand-your-azure-marketplace-charges/sub-button.png) 
3. V **předplatná** okno, vyberte předplatné, které chcete zobrazit a potom vyberte **externích služeb**.
   
    ![Vyberte předplatné, v okně fakturace](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. Měli byste vidět všechny své objednávky externí služby, název vydavatele, úroveň služby, které jste koupili, název dáte prostředku a aktuální stav objednávky. Pokud chcete zobrazit předchozí faktury, vyberte externí služby.
   
    ![Vyberte externí služby](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. Tady můžete zobrazit posledních včetně daň za rozepsání částky faktury.
   
    ![Zobrazení historie fakturace externí služby](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png)

## <a name="view-external-service-spending-for-enterprise-agreement-ea-customers"></a>Zobrazení externí služby útraty pro zákazníky, kteří Enterprise Agreement (EA)
Zákazníci programu EA si můžou zobrazit útraty externí služby a stáhnout sestav na portálu EA. Zobrazit [Azure Marketplace pro zákazníky se smlouvou EA](https://ea.azure.com/helpdocs/azureMarketplace) začít.

## <a name="manage-payment-methods-for-external-service-orders"></a>Spravovat způsoby platby za objednávky externí služby
Aktualizovat způsobů platby za objednávky externí služby z [centra pro účty](https://account.windowsazure.com/).

> [!NOTE]
> Pokud jste zakoupili předplatné s pracovní nebo školní účet, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) měnit vaším způsobem platby.
> 
> 

1. Přihlaste se k [centra pro účty](https://account.windowsazure.com/) a [přejděte **marketplace** kartu](https://account.windowsazure.com/Store)
   
    ![Zvolte marketplace, v centru účtů](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Vyberte externí službu, kterou chcete spravovat
   
    ![Vyberte externí službu, kterou chcete spravovat](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Klikněte na tlačítko **změnit způsob platby** na pravé straně stránky. Tento odkaz vám přináší pro jiný portál pro správu vaším způsobem platby.
   
    ![Přehled objednávek](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Klikněte na tlačítko **upravit informace o** a postupujte podle pokynů a aktualizovat platební údaje.
   
    ![Informace o vybrání možnosti Upravit](./media/billing-understand-your-azure-marketplace-charges/edit-info.png)

## <a name="cancel-an-external-service-order"></a>Zrušit objednávku externí služby
Pokud chcete zrušit objednávku externí služby, odstranění prostředku v [webu Azure portal](https://portal.azure.com).

![Odstranit prostředek](./media/billing-understand-your-azure-marketplace-charges/deleteMarketplaceOrder.PNG)

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).


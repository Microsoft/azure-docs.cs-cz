---
title: Pochopení Azure externí poplatky za | Microsoft Docs
description: Další informace o fakturaci externích služeb, dřív označované jako Marketplace, poplatky v Azure.
services: ''
documentationcenter: ''
author: adpick
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 5e0e2a3c-d111-4054-8508-0c111c1b749b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/9/2017
ms.author: adpick
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6f7d8b89806e1f6d59e1a64e589558cd972f4fdc
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
ms.locfileid: "33203965"
---
# <a name="understand-your-azure-billing-for-external-service-charges"></a>Pochopení vašeho Azure fakturace poplatků za externí služby
Externích služeb jsou publikovány dodavatelé softwaru třetích stran v Azure marketplace. Například ClearDB a sendgrid vám umožňuje jsou externích služeb, které můžete zakoupit v Azure, ale nejsou vydané společností Microsoft.

Při zřizování nové externí služby nebo prostředku, upozornění se zobrazí:

![Marketplace zakoupit upozornění](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

> [!NOTE]
> Externích služeb jsou publikovány společností, které nejsou Microsoft, ale někdy jsou produkty společnosti Microsoft také klasifikovány jako externích služeb.
> 
> 

## <a name="how-external-services-are-billed"></a>Jak se účtují externích služeb
- Externích služeb se účtují samostatně. Jsou považovány za jednotlivé příkazy v rámci vašeho předplatného Azure. Fakturační období pro každou službu nastavena při nákupu služby. Termín nelze zaměňovat s fakturační období předplatného, pod kterým jste ho koupili. Zobrazí také samostatné faktur a platební karty je účtována samostatně.
- Každá externí služba má jiný model fakturace. Některé služby se účtují průběžnými platbami způsobem, jiné zase měsíční modelu na základě platby. Potřebujete platební karty pro externí služby Azure, nelze koupit externích služeb s platím faktury.
- Měsíční kredity zdarma nelze použít pro externích služeb. Pokud používáte předplatné Azure, která zahrnuje [volné kredity](https://azure.microsoft.com/pricing/spending-limits/), nelze použít externí služba faktur. Chcete-li zakoupit externích služeb používají platební kartu.

## <a name="view-external-service-spending-and-history-in-the-azure-portal"></a>Zobrazení externí služba výdaje a historie na portálu Azure
Můžete zobrazit seznam externích služeb, které jsou v každém předplatném v rámci [portál Azure](https://portal.azure.com/): 

1. Přihlaste se k [portál Azure](https://portal.azure.com/) jako správce účtu.
2. V nabídce centra vyberte **odběry**.
   
    ![V nabídce centra vyberte odběrů](./media/billing-understand-your-azure-marketplace-charges/sub-button.png) 
3. V **odběry** , vyberte předplatné, které chcete zobrazit a pak vyberte **externích služeb**.
   
    ![Vyberte předplatné, v okně fakturace](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. Měli byste vidět všechny vaše externí služba objednávky, název vydavatele, úroveň služby, které jste si zakoupili, název dáte prostředek a aktuální stav pořadí. V minulosti faktur, vyberte externí služby.
   
    ![Vyberte externí služby](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. Tady můžete zobrazit po faktury objemy včetně rozpis daň.
   
    ![Zobrazení historie fakturace externích služeb](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png)

## <a name="view-external-service-spending-for-enterprise-agreement-ea-customers"></a>Zobrazení externí služba výdaje pro zákazníky Enterprise Agreement (EA)
Zákazníci EA můžete zobrazit externí služba výdaje a stáhnout sestav na portálu EA. V tématu [Azure Marketplace pro zákazníky, EA](https://ea.azure.com/helpdocs/azureMarketplace) začít pracovat.

## <a name="manage-payment-methods-for-external-service-orders"></a>Spravovat způsoby platby pro objednávky externí služby
Aktualizace vašeho způsoby platby pro externí služba objednávky z [centra účtů](https://account.windowsazure.com/).

> [!NOTE]
> Pokud jste zakoupili předplatné s pracovního nebo školního účtu, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) změnit způsob platby.
> 
> 

1. Přihlaste se k [centra účtů](https://account.windowsazure.com/) a [přejděte do **marketplace** karta](https://account.windowsazure.com/Store)
   
    ![V centru účtů vyberte marketplace.](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Vyberte externí službu, kterou chcete spravovat
   
    ![Vyberte externí službu, kterou chcete spravovat](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Klikněte na tlačítko **změnit způsob platby** na pravé straně stránky. Tento odkaz vám přináší pro jiný portál pro správu váš způsob platby.
   
    ![Souhrn pořadí](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Klikněte na tlačítko **upravit informace o** a postupujte podle pokynů k aktualizaci vašich platebních informací.
   
    ![Vyberte možnost upravit informace o](./media/billing-understand-your-azure-marketplace-charges/edit-info.png)

## <a name="cancel-an-external-service-order"></a>Zrušení objednávky externí služby
Pokud chcete zrušit vaše externí služba pořadí, odstranit prostředek [portál Azure](https://portal.azure.com).

![Odstranit prostředek](./media/billing-understand-your-azure-marketplace-charges/deleteMarketplaceOrder.PNG)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.
Pokud máte otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.


---
title: Kurz – Odhad útraty pomocí Cloudyn v Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak odhadnout útratu na základě historických dat o využití a výdajích.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/18/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: ''
manager: benshy
ms.openlocfilehash: 25fa099bcd6fa3843deadb3dc39d4250624ead06
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52274581"
---
# <a name="tutorial-forecast-future-spending"></a>Kurz: Odhad budoucí útraty

Cloudyn pomáhá odhadovat budoucí útratu na základě historických údajů o využití a nákladech. Sestavy Cloudynu můžete využít k zobrazení všech dat odhadu nákladů. Příklady v tomto kurzu vás provedou kontrolou odhadu nákladů s využitím sestav. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Odhad budoucí útraty

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

- Musíte mít účet Azure.
- Musíte mít buď zaregistrovanou zkušební verzi, nebo placené předplatné Cloudyn.

## <a name="forecast-future-spending"></a>Odhad budoucí útraty

Cloudyn zahrnuje sestavy odhadu nákladů, které pomáhají odhadovat útratu na základě vašeho využití v čase. Jejich primárním účelem je zajistit, aby trendy nákladů nepřekračovaly očekávání vaší organizace. Použijete sestavu plánovaných nákladů pro aktuální měsíc a ročních plánovaných nákladů. Obě tyto sestavy ukazují plánovanou budoucí útratu, pokud vaše využití bude relativně konzistentní s využitím během posledních 30 dnů.

Sestava plánovaných nákladů pro aktuální měsíc ukazuje náklady na vaše služby. K zobrazení odhadovaných nákladů využívá náklady ze začátku měsíce a z předchozího měsíce. V nabídce sestav v horní části portálu klikněte na **Costs** > **Projection and Budget** > **Current Month Projected Cost** (Náklady > Plán a rozpočet > Plánované náklady pro aktuální měsíc). Příklad ukazuje následující obrázek.

![Plánované náklady pro aktuální měsíc](./media/tutorial-forecast-spending/project-month01.png)

V tomto příkladu vidíte, za které služby se utratilo nejvíc. Náklady na Azure byly nižší než náklady na AWS. Pokud chcete zobrazit podrobné informace o odhadu nákladů pro virtuální počítače Azure, v seznamu **Filtr** vyberte **Azure/VM**.

![Plánované náklady pro aktuální měsíc pro virtuální počítač Azure](./media/tutorial-forecast-spending/project-month02.png)

Použijte ten samý základní postup a podívejte se na odhady měsíčních nákladů pro další služby, které vás zajímají.

Sestava ročních plánovaných nákladů ukazuje extrapolované náklady na vaše služby za příštích 12 měsíců.

V nabídce sestav v horní části portálu klikněte na **Costs** > **Projection and Budget** > **Annual Projected Cost** (Náklady > Plán a rozpočet > Roční plánované náklady). Příklad ukazuje následující obrázek.

![Sestava ročních plánovaných nákladů](./media/tutorial-forecast-spending/project-annual01.png)

V tomto příkladu vidíte, za které služby se utratilo nejvíc. Stejně jako u příkladu pro poslední měsíc byly náklady na Azure nižší než náklady na AWS. Pokud chcete zobrazit podrobné informace o odhadu nákladů pro virtuální počítače Azure, v seznamu **Filtr** vyberte **Azure/VM**.

![Roční plánované náklady na virtuální počítače](./media/tutorial-forecast-spending/project-annual02.png)

Na obrázku výše roční plánované náklady na virtuální počítače Azure dosahují 28 374 USD.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Odhad budoucí útraty


Přejděte k dalšímu kurzu, ve kterém se naučíte spravovat náklady s využitím přidělování nákladů a sestav započítávání pohledávek.

> [!div class="nextstepaction"]
> [Správa nákladů s přidělováním nákladů a sestavami započítávání pohledávek](tutorial-manage-costs.md)

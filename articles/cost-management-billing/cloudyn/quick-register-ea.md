---
title: Registrace smlouvy Azure Enterprise u společnosti Cloudyn
description: Tento rychlý start podrobně popisuje proces registrace nezbytný k vytvoření zkušebního předplatného a přihlášení k portálu Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: 3c54328630d4e349af25c5f28a53a66117bf6a39
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79474607"
---
# <a name="register-an-azure-enterprise-agreement-and-view-cost-data"></a>Registrace smlouvy Azure Enterprise a zobrazení informací o nákladech

Použijte svoji smlouvu Azure Enterprise k registraci u Cloudyn. Registrací získáte přístup k portálu Cloudyn. Tento rychlý start podrobně popisuje proces registrace nezbytný k vytvoření zkušebního předplatného a přihlášení k portálu Cloudyn. Ukazuje také, jak rovnou začít zobrazovat informace o nákladech.

Azure Cost Management nabízí podobné funkce jako Cloudyn. Azure Cost Management je nativní řešení Azure pro správu nákladů. Umožní vám analyzovat náklady, vytvářet a spravovat rozpočty, exportovat data, kontrolovat optimalizační doporučení k úspoře peněz a reagovat na ně. Další informace najdete v tématu [Azure Cost Management](../cost-management-billing-overview.md).

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

- Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="register-with-cloudyn"></a>Registrace u Cloudyn

1. Na webu Azure Portal klikněte v seznamu služeb na **Cost Management a fakturace**.
2. V části **Přehled** klikněte na **Cloudyn**.  
    ![Stránka Cloudyn na webu Azure Portal](./media/quick-register-ea/cost-mgt-billing-service.png)
3. Na stránce **Cloudyn** klikněte na **Přejít na Cloudyn** a v novém okně se otevře stránka pro registraci u Cloudyn.
4. Na stránce pro registraci zkušební verze portálu Cloudyn zadejte název vaší společnosti a vyberte **Správce prováděcí smlouvy Azure Enterprise**.  
5. Zadejte klíč rozhraní API vaší prováděcí smlouvy Enterprise Portal. Pokud svůj klíč nemáte po ruce, klikněte na odkaz na web [Enterprise Portal](https://ea.azure.com) a proveďte následující kroky:  
    ![Vložte klíč rozhraní API na kartě Fakturace.](./media/quick-register-ea/trial-reg.png)
   1. Přihlaste se k webu Azure Enterprise, klikněte na **Sestavy**, pak na **Přístupový klíč rozhraní API** a zkopírujte váš primární klíč.  
    ![Příklad klíče rozhraní API EA na portálu EA](./media/quick-register-ea/ea-key.png)
   3. Vraťte se na registrační stránku a vložte váš klíč rozhraní API.
6. Vyjádřete souhlas s podmínkami použití a ověřte váš klíč. Kliknutím na **Další** udělte Cloudyn oprávnění ke shromažďování dat o prostředcích Azure. Mezi shromažďovaná data patří informace o využití, výkonu, fakturaci a značkách z vašich předplatných.  
    ![Příklad úspěšného ověření klíče rozhraní API EA](./media/quick-register-ea/ea-key-validated.png)
7. V části **Pozvat další účastníky** můžete zadáním příslušných e-mailových adres přidat další uživatele. Jakmile budete hotovi, klikněte na **Další**. V závislosti na velikosti vaší registrace v Azure může přidání všech vašich fakturačních dat do Cloudyn trvat až 24 hodin.
8. Kliknutím na **Přejít do Cloudyn** otevřete portál Cloudyn a na stránce **Správa cloudových účtů** by se měly zobrazit informace o vašem zaregistrovaném účtu EA.

Pokud chcete shlédnout videokurz k registraci smlouvy Enterprise, podívejte se na video [How to Find Your EA Enrollment ID and API Key for use in Cloudyn](https://youtu.be/u_phLs_udig) (Vyhledání ID prováděcí smlouvy EA a klíče rozhraní API pro použití v Cloudyn).

[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili informace o své smlouvě Azure Enterprise k registraci u Cloudyn. Také jste se přihlásili k portálu Cloudyn a začali jste zobrazovat informace o nákladech. Další informace o Cloudyn najdete v kurzu pro Cloudyn.

> [!div class="nextstepaction"]
> [Kontrola využití a nákladů](tutorial-review-usage.md)

---
title: Integrace s Office 365 Outlookem
description: Automatizace úloh a pracovních postupů, které spravují e-maily, kontakty a kalendáře v Office 365 Outlooku pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 11/13/2020
tags: connectors
ms.openlocfilehash: 87a9c8a38ede7008d658068329c8c72e7e3c4117
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101095459"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>Správa e-mailů, kontaktů a kalendářů v Office 365 Outlooku s využitím Azure Logic Apps

Pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [konektoru Office 365 Outlook](/connectors/office365connector/)můžete vytvářet automatizované úlohy a pracovní postupy, které spravují svůj pracovní nebo školní účet pomocí vytváření Logic Apps. Můžete automatizovat třeba tyto úlohy:

* Získání, odeslání a odpověď na e-mail.
* Naplánujte schůzky v kalendáři.
* Přidávání a úpravy kontaktů.

Libovolný Trigger můžete použít ke spuštění pracovního postupu, například při přijetí nového e-mailu, při aktualizaci položky kalendáře nebo při výskytu události v rozdílové službě, jako je například Salesforce. Můžete použít akce, které reagují na událost triggeru, například odeslat e-mail nebo vytvořit novou událost v kalendáři.

## <a name="prerequisites"></a>Požadavky

* Účet Outlooku, ke kterému se přihlašujete pomocí [pracovního nebo školního účtu](https://www.office.com/). Pokud máte @outlook.com @hotmail.com účet nebo, použijte místo toho [konektor Outlook.com](../connectors/connectors-create-api-outlook.md) . Pokud se chcete připojit k Outlooku pomocí jiného uživatelského účtu, jako je třeba účet služby, přečtěte si téma [připojení pomocí dalších účtů](#connect-using-other-accounts).

* Účet a předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Aplikace logiky, ke které chcete získat přístup k účtu Outlook. Abyste mohli pracovní postup spustit pomocí triggeru Office 365 Outlooku, musíte mít [prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Chcete-li do pracovního postupu přidat akci sady Office 365 Outlook, aplikace logiky musí mít již Trigger.

## <a name="add-a-trigger"></a>Přidání triggeru

[Trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) je událost, která spouští pracovní postup ve vaší aplikaci logiky. Tato ukázková aplikace logiky používá aktivační událost "cyklického dotazování", která v závislosti na zadaném intervalu a četnosti kontroluje všechny aktualizované události v kalendáři v e-mailovém účtu.

1. V [Azure Portal](https://portal.azure.com)otevřete prázdnou aplikaci logiky v návrháři aplikace logiky.

1. Do vyhledávacího pole zadejte `office 365 outlook` jako filtr. Tento příklad vybírá, **kdy brzy začíná nadcházející událost**.
   
   ![Výběr triggeru pro spuštění aplikace logiky](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Pokud nemáte aktivní připojení k účtu Outlooku, budete vyzváni k přihlášení a vytvoření tohoto připojení. Pokud se chcete připojit k Outlooku pomocí jiného uživatelského účtu, jako je třeba účet služby, přečtěte si téma [připojení pomocí dalších účtů](#connect-using-other-accounts). V opačném případě zadejte informace o vlastnostech triggeru.

   > [!NOTE]
   > Vaše připojení nevyprší, dokud nebude zrušeno, a to ani v případě, že změníte přihlašovací údaje pro přihlášení. Další informace najdete v tématu [konfigurovatelné životnosti tokenů v Azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md).

   V tomto příkladu se vybere kalendář, který Trigger kontroluje, například:

   ![Konfigurace vlastností triggeru](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. V aktivační události nastavte hodnoty **frekvence** a **interval** . Chcete-li přidat další dostupné vlastnosti triggeru, jako je například **časové pásmo**, vyberte tyto vlastnosti ze seznamu **Přidat nový parametr** .

   Například pokud chcete, aby Trigger kontroloval kalendář každých 15 minut, nastavte **četnost** na **minuty** a nastavte **interval** na `15` . 

   ![Nastavte četnost a interval pro aktivační událost.](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. Na panelu nástrojů návrháře vyberte **Uložit**.

Teď přidejte akci, která se spustí po aktivování triggeru. Můžete například přidat akci **Odeslat zprávu** Twilio, která pošle text, když se událost Calendar spustí za 15 minut.

## <a name="add-an-action"></a>Přidání akce

[Akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je operace, kterou spouští pracovní postup ve vaší aplikaci logiky. Tato ukázková aplikace logiky vytvoří nový kontakt v Office 365 Outlooku. K vytvoření kontaktu můžete použít výstup z jiné triggeru nebo akce. Předpokládejme například, že vaše aplikace logiky používá Trigger Dynamics 365 **při vytvoření záznamu**. Chcete-li vytvořit nový kontakt, můžete přidat akci **kontaktu vytvořit kontakt** pro Office 365 Outlook a použít výstupy z triggeru Salesforce.

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. Pokud chcete přidat akci jako poslední krok pracovního postupu, vyberte **Nový krok**. 

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi těmito kroky. Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte `office 365 outlook` jako filtr. Tento příklad vybere **vytvořit kontakt**.

   ![Vyberte akci, která se má spustit v aplikaci logiky.](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Pokud nemáte aktivní připojení k účtu Outlooku, budete vyzváni k přihlášení a vytvoření tohoto připojení. Pokud se chcete připojit k Outlooku pomocí jiného uživatelského účtu, jako je třeba účet služby, přečtěte si téma [připojení pomocí dalších účtů](#connect-using-other-accounts). V opačném případě zadejte informace o vlastnostech akce.

   > [!NOTE]
   > Vaše připojení nevyprší, dokud nebude zrušeno, a to ani v případě, že změníte přihlašovací údaje pro přihlášení. Další informace najdete v tématu [konfigurovatelné životnosti tokenů v Azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md).

   V tomto příkladu se vybere složka kontaktů, kde akce vytvoří nový kontakt, například:

   ![Konfigurace vlastností akce](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   Chcete-li přidat další dostupné vlastnosti akce, vyberte tyto vlastnosti ze seznamu **Přidat nový parametr** .

1. Na panelu nástrojů návrháře vyberte **Uložit**.

<a name="connect-using-other-accounts"></a>

## <a name="connect-using-other-accounts"></a>Připojení pomocí jiných účtů

Pokud se pokusíte připojit k Outlooku pomocí jiného účtu, než který je aktuálně přihlášený k Azure, může dojít k chybám [jednotného přihlašování (SSO)](../active-directory/manage-apps/what-is-single-sign-on.md) . K tomuto problému dochází, když se přihlásíte k Azure Portal pomocí jednoho účtu, ale k vytvoření připojení použijete jiný účet. Návrhář očekává, že použijete účet, který je přihlášený k Azure Portal. K vyřešení tohoto problému máte tyto možnosti:

* Nastavte druhý účet pomocí role **přispěvatele** ve skupině prostředků vaší aplikace logiky.

  1. V nabídce skupiny prostředků vaší aplikace logiky vyberte **řízení přístupu (IAM)**. Nastavte druhý účet pomocí role **Přispěvatel** . 
  
     Další informace najdete v tématu [přiřazení rolí Azure pomocí Azure Portal](../role-based-access-control/role-assignments-portal.md).

  1. Po nastavení této role se přihlaste k Azure Portal pomocí účtu, který má teď oprávnění přispěvatele. Nyní můžete tento účet použít k vytvoření připojení k aplikaci Outlook.

* Nastavte druhý účet tak, aby váš pracovní nebo školní účet měl oprávnění Odeslat jako.

   Pokud máte oprávnění správce, v poštovní schránce účtu služby nastavte svůj pracovní nebo školní účet tak, aby byl buď **odeslán** , nebo **odeslán jménem** oprávnění. Další informace najdete v tématu [udělení oprávnění k poštovní schránce pro jinou nápovědu pro správce uživatele](/microsoft-365/admin/add-users/give-mailbox-permissions-to-another-user). Pak můžete vytvořit připojení pomocí svého pracovního nebo školního účtu. Nyní můžete v aktivačních událostech nebo akcích, kde můžete zadat odesílatele, použít e-mailovou adresu účtu služby.

   Například akce **Odeslat e-mail** má volitelný parametr **from (Odeslat jako)**, který můžete přidat do akce a jako odesílatele použít e-mailovou adresu účtu služby. Chcete-li přidat tento parametr, postupujte podle následujících kroků:

   1. V akci **Odeslat e-mail** otevřete seznam **Přidat parametr** a vyberte parametr **od (Odeslat jako)** .

   1. Po zobrazení parametru v akci zadejte e-mailovou adresu účtu služby.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o této spojnici, jako jsou triggery, akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete na [referenční stránce konektoru](/connectors/office365/). 

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)

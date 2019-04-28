---
title: Vytvoření a Správa účtů pro integraci řešení B2B – Azure Logic Apps | Dokumentace Microsoftu
description: Vytvořit, propojit, přesunout a odstranit integračních účtů pro podnikovou integraci a řešení B2B s Azure Logic Apps
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/30/2018
ms.author: estfan
ms.openlocfilehash: 43ecdafac4f0a5cdc9e619537cdbe2a42ff7fe1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60999489"
---
# <a name="create-and-manage-integration-accounts-for-b2b-solutions-with-logic-apps"></a>Vytvoření a Správa účtů pro integraci řešení B2B logic Apps

Před sestavením [řešení B2B a enterprise integration](../logic-apps/logic-apps-enterprise-integration-overview.md) s [Azure Logic Apps](../logic-apps/logic-apps-overview.md), musíte nejprve mít účtu pro integraci, což je kde vytváření, ukládání a správě artefaktů B2B, například obchodní partneři, smlouvy, mapy, schémata, certifikáty a tak dále. Aplikace logiky můžete práce s artefakty v účtu integrace a konektory Logic Apps B2B, jako je třeba ověřování XML, je nutné nejprve [propojí váš účet integrace](#link-account) do aplikace logiky. Je propojit, musí mít obě integrační účet a logiku aplikace *stejné* Azure místa nebo oblasti.

Tento článek popisuje, jak k provádění těchto úkolů:

* Vytvoření účtu pro integraci.
* Propojí váš účet integrace aplikace logiky.
* Přesunutí účtu pro integraci do jiného Azure skupiny prostředků nebo předplatného.
* Odstranění účtu integrace.

Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu <a href="https://portal.azure.com" target="_blank">Azure Portal</a> pomocí přihlašovacích údajů svého účtu Azure.

## <a name="create-integration-account"></a>Vytvoření účtu pro integraci

1. V hlavní nabídce Azure zvolte **všechny služby**. Do vyhledávacího pole zadejte jako filtr "účtů integrace" a vyberte **účty pro integraci**.

   ![Najít účty pro integraci](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. V části **účty pro integraci**, zvolte **přidat**.

   ![Zvolte "Přidat" a vytvoření účtu pro integraci](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

3. Zadání informací o účtu pro integraci: 

   ![Zadejte podrobnosti pro váš účet integrace](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Vlastnost | Požaduje se | Příklad hodnoty | Popis | 
   |----------|----------|---------------|-------------|
   | Name | Ano | testovací účet integrace | Název účtu pro integraci. V tomto příkladu pomocí zadaného názvu. | 
   | Předplatné | Ano | <*název_předplatného_Azure*> | Název předplatného Azure k použití | 
   | Skupina prostředků | Ano | test-integration-account-rg | Název [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) k uspořádání souvisejících prostředků použít. V tomto příkladu vytvořte novou skupinu prostředků se zadaným názvem. | 
   | Cenová úroveň | Ano | Free | Cenová úroveň, kterou chcete použít. V tomto příkladu vyberte **Free**, ale další informace najdete v tématu [Logic Apps omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md) a [ceny Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/). | 
   | Location | Ano | Západní USA | Oblast kam se mají ukládat informace o vašem účtu integrace. Buď vyberte stejné umístění jako aplikace logiky, nebo vytvořte aplikaci logiky ve stejném umístění jako váš účet integrace. | 
   | Pracovní prostor Log Analytics | Ne | Vypnuto | Pokud chcete zapnout protokolování diagnostiky, ponechte nastavení **Vypnuto**. | 
   ||||| 

4. Až to budete mít, vyberte **Připnout na řídicí panel** a zvolte **Vytvořit**.

   Jakmile Azure nasadí váš účet pro integraci do vybraného umístění, které obvykle dokončí během jedné minuty, Azure otevře účtu pro integraci.

   ![Azure otevře účtu pro integraci](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

Teď než aplikace logiky můžete používat svůj účet integrace, je třeba propojit účet pro integraci do aplikace logiky.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Odkaz na aplikaci logiky

Poskytnout přístup aplikace logiky k účtu pro integraci, který obsahuje artefaktů B2B, jako je například obchodní partnery, smlouvy, mapy a schémata, třeba propojit účet integrace do aplikace logiky. 

> [!NOTE]
> Integrace účtu a logika aplikace musí existovat ve stejné oblasti.

1. Na webu Azure Portal najít a otevřít aplikaci logiky.

2. V nabídce aplikace logiky podle **nastavení**vyberte **nastavení pracovního postupu**. V **vyberte účtu pro integraci** vyberte účet pro integraci odkaz na aplikaci logiky.

   ![Vyberte svůj účet integrace](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-2.png)

3. Dokončete propojení, zvolte **Uložit**.

   ![Vyberte svůj účet integrace](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-3.png)

   Když svůj účet integrace byl úspěšně propojen, Azure, zobrazí se potvrzovací zpráva. 

   ![Azure potvrzuje úspěšné propojení](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-5.png)

Aplikace logiky teď můžete použít všechny a všechny artefakty v účtu pro integraci a konektory B2B, jako je například ověřování XML a plochého souboru kódování nebo dekódování.  

## <a name="unlink-from-logic-app"></a>Zrušit propojení aplikace logiky

Odkaz na jiný účet integrace aplikace logiky nebo již nebudete používat účtu pro integraci s vaší aplikací logiky, můžete odstranit propojení pomocí Průzkumníka prostředků Azure.

1. V prohlížeči přejděte na <a href="https://resources.azure.com" target="_blank">Azure Resource Exploreru (https://resources.azure.com)</a>. Ujistěte se, že jste přihlášení pomocí stejných přihlašovacích údajů Azure.

   ![Průzkumník prostředků Azure](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

2. Do vyhledávacího pole zadejte název vaší aplikace logiky pak vyhledejte a vyberte svou aplikaci logiky.

   ![Vyhledejte a vyberte aplikaci logiky](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

3. V záhlaví okna Průzkumníka zvolte **r/w**.

   ![Zapnout režim "Pro čtení a zápisu"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-read-write-mode.png)

4. Na **Data** kartě **upravit**.

   ![Na kartě "Data" zvolte možnost "Upravit"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-edit.png)

5. V editoru, najdete `integrationAccount` vlastnost integrace účtu a odstranit tuto vlastnost, která má tento formát:

   ```json
   "integrationAccount": {
      "name": "<integration-account-name>",
      "id": "<integration-account-resource-ID>",
      "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Příklad:

   ![Vyhledejte definici vlastnosti "integrationAccount"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

6. Na **Data** kartě **umístit** uložte provedené změny. 

   ![Zvolte možnost "Vložit" se uložit změny](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

7. Na webu Azure Portal, v rámci vaší aplikace logiky **nastavení pracovního postupu**, zkontrolujte, že **účtu pro integraci** vlastnosti nyní zobrazí jako prázdná.

   ![Zkontrolujte, že není propojený účet pro integraci](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Přesunutí účtu integrace

Váš účet pro integraci můžete přesunout do jiného Azure předplatné nebo skupinu prostředků.

1. V hlavní nabídce Azure zvolte **všechny služby**. Do vyhledávacího pole zadejte jako filtr "účtů integrace" a vyberte **účty pro integraci**.

   ![Vyhledejte svůj účet integrace](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. V části **účty pro integraci**, vyberte účet pro integraci, kterou chcete přesunout. Na vaše integrační účet nabídce v části **nastavení**, zvolte **vlastnosti**.

   ![V části "Nastavení" Vyberte "Properties"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-properties.png)

3. Změňte skupinu prostředků Azure nebo předplatné pro váš účet integrace.

   ![Vyberte "Změnit skupinu prostředků" nebo "změnit předplatné.](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

4. Až skončíte, ujistěte se, že aktualizace všechny skripty se nové ID prostředků pro artefaktů.  

## <a name="delete-integration-account"></a>Odstranit účet pro integraci

1. V hlavní nabídce Azure zvolte **všechny služby**. Do vyhledávacího pole zadejte jako filtr "účtů integrace" a vyberte **účty pro integraci**.

   ![Vyhledejte svůj účet integrace](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. V části **účty pro integraci**, vyberte účet pro integraci, kterou chcete odstranit. V nabídce účtu integrace, zvolte **přehled**, klikněte na tlačítko **odstranit**. 

   ![Vyberte účet pro integraci. Na stránce "Přehled" na možnost "Odstranit"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

3. Pokud chcete potvrdit, že chcete odstranit účet pro integraci, zvolte **Ano**.

   ![Potvrďte odstranění, zvolte Ano.](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Další postup

* [Vytvořit obchodní partneři](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Vytvoření smlouvy](../logic-apps/logic-apps-enterprise-integration-agreements.md)

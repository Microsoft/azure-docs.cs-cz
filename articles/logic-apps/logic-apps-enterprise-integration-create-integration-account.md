---
title: Vytvářet a spravovat účty pro integraci pro řešení B2B - Azure Logic Apps | Microsoft Docs
description: Vytvoření, odkaz, přesunout a odstranit účty pro integraci pro integraci podnikových a řešení B2B službou Azure Logic Apps
services: logic-apps
documentationcenter: ''
author: ecfan
manager: SyntaxC4
editor: ''
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/30/2018
ms.author: ecfan; LADocs
ms.openlocfilehash: 8e31a84d4508075dcb7a1d7ad8a64fa8e142681d
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2018
---
# <a name="create-and-manage-integration-accounts-for-b2b-solutions-with-logic-apps"></a>Vytvářet a spravovat účty pro integraci B2B řešení s logic apps

Před sestavením [integrace enterprise a řešení B2B](../logic-apps/logic-apps-enterprise-integration-overview.md) s [Azure Logic Apps](../logic-apps/logic-apps-overview.md), nejprve musí mít na účet integrace, což je kde vytvářet, ukládat a spravovat B2B artefaktů, jako například obchodní partnery, smlouvy, map, schémata, certifikáty a tak dále. Než můžete svou aplikaci logiky práce s artefakty ve vašem účtu integrace a používat konektory B2B aplikace logiky, jako je ověření XML, je potřeba [propojte si účet integrace](#link-account) do aplikace logiky. Je propojit, musí mít obě integrace účet a logiku aplikace *stejné* umístění Azure nebo oblast.

Tento článek ukazuje, jak k provedení těchto úloh:

* Vytvoření účtu integrace.
* Propojte si účet integrace se aplikace logiky.
* Přesunutí účtu integrace do jiné Azure skupiny prostředků nebo předplatného.
* Odstranění účtu integrace.

Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu <a href="https://portal.azure.com" target="_blank">Azure Portal</a> pomocí přihlašovacích údajů svého účtu Azure.

## <a name="create-integration-account"></a>Vytvoření účtu integrace

1. Z hlavní nabídky Azure, vyberte **všechny služby**. Do vyhledávacího pole zadejte "účty pro integraci" jako filtr a vyberte **účty pro integraci**.

   ![Najít účty pro integraci](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. V části **účty pro integraci**, zvolte **přidat**.

   ![Zvolte možnost "Přidat" k vytvoření účtu integrace](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

3. Zadejte informace o vašem účtu integrace: 

   ![Zadejte podrobnosti pro váš účet integrace](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Vlastnost | Požaduje se | Příklad hodnoty | Popis | 
   |----------|----------|---------------|-------------|
   | Název | Ano | Test integrace account | Název účtu integrace. V tomto příkladu pomocí zadaného názvu. | 
   | Předplatné | Ano | <*Název předplatného Azure*> | Název předplatného Azure k použití | 
   | Skupina prostředků | Ano | Test integrace účet rg | Název [skupina prostředků Azure](../azure-resource-manager/resource-group-overview.md) sloužící k organizování související prostředky. V tomto příkladu vytvořte novou skupinu prostředků se zadaným názvem. | 
   | Cenová úroveň | Ano | Free | Cenovou úroveň, kterou chcete použít. V tomto příkladu vyberte **volné**. | 
   | Umístění | Ano | Západní USA | Oblast kam se mají ukládat informace o integraci účtu. Buď vyberte stejné umístění jako svou aplikaci logiky, nebo vytvoření aplikace logiky ve stejném umístění jako účet integrace. V tomto příkladu | 
   | Log Analytics | Ne | Vypnuto | Pokud chcete zapnout protokolování diagnostiky, ponechte nastavení **Vypnuto**. | 
   ||||| 

4. Až budete připraveni, vyberte **připnout na řídicí panel**a zvolte **vytvořit**.

   Po Azure nasadí do vybraného umístění, což obvykle ukončí během jedné minuty, váš účet integrace Azure otevře účtu integrace.

   ![Otevře účtu integrace Azure](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

Teď aplikaci logiky můžete používat svůj účet integrace, musí propojit účet integraci do aplikace logiky.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Odkaz na aplikaci logiky

Umožnit přístup vašich aplikací logiky na integraci účet, který obsahuje B2B artefaktů, jako je například obchodní partnery, smlouvy, mapy a schémat, musí váš účet integrace propojit aplikaci logiky. 

> [!NOTE]
> Integrace účet a logiku aplikace, musí existovat ve stejné oblasti.

1. Na portálu Azure najít a otevřít aplikaci logiky.

2. V nabídce aplikace logiky v části **nastavení**, vyberte **nastavení pracovních postupů**. V **vyberte účet, integrace** vyberte integrace účet, který chcete propojit aplikaci logiky.

   ![Vyberte svůj účet integrace](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-2.png)

3. K dokončení propojení, zvolte **Uložit**.

   ![Vyberte svůj účet integrace](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-3.png)

   Pokud váš účet integrace byl úspěšně propojen, Azure zobrazuje potvrzovací zpráva. 

   ![Azure potvrzuje úspěšné odkaz](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-5.png)

Aplikace logiky teď můžete použít všechny a všechny artefakty v účtu integrace plus konektory B2B, jako je ověření XML a plochý soubor kódování nebo dekódování.  

## <a name="unlink-from-logic-app"></a>Zrušit propojení aplikace logiky

Propojení aplikace logiky na jiný účet integrace nebo nadále používat účet integrace s svou aplikaci logiky, můžete odstranit odkaz pomocí Průzkumníka prostředků Azure.

1. V prohlížeči přejděte na <a href="https://resources.azure.com" target="_blank">Průzkumníka prostředků Azure (https://resources.azure.com)</a>. Ujistěte se, že jste přihlášení pomocí stejných přihlašovacích údajů Azure.

   ![Průzkumník prostředků Azure](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

2. Do vyhledávacího pole zadejte název aplikace logiky, pak najít a vyberte svou aplikaci logiky.

   ![Najděte a vyberte aplikaci logiky](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

3. V záhlaví okna Průzkumníka, zvolte **pro čtení a zápis**.

   ![Zapnout režim "Pro čtení a zápis"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-read-write-mode.png)

4. Na **Data** , zvolte **upravit**.

   ![Na kartě "Data" Vyberte "Upravit"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-edit.png)

5. V editoru, najdete `integrationAccount` vlastnost pro integraci účtu a odstranit tuto vlastnost, která má tento formát:

   ```json
   "integrationAccount": {
      "name": "<integration-account-name>",
      "id": "<integration-account-resource-ID>",
      "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Příklad:

   ![Najít definici vlastnosti "integrationAccount"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

6. Na **Data** , zvolte **Put** uložte provedené změny. 

   ![Zvolte "Put" se uložit změny](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

7. Na portálu Azure v rámci aplikace logiky **nastavení pracovních postupů**, zkontrolujte, zda **integrace účet** vlastnost se teď zobrazí prázdné.

   ![Zkontrolujte, že není propojený účet integrace](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Přesunutí účtu integrace

Váš účet integrace můžete přesunout do jiné Azure předplatné nebo prostředek skupiny.

1. V hlavní nabídce Azure, vyberte **všechny služby**. Do vyhledávacího pole zadejte "účty pro integraci" jako filtr a vyberte **účty pro integraci**.

   ![Najít váš účet integrace](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. V části **účty pro integraci**, vyberte účet integrace, který chcete přesunout. Na svoji integraci účet nabídce v části **nastavení**, zvolte **vlastnosti**.

   ![V části "Nastavení" Vyberte možnost "Vlastnosti"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-properties.png)

3. Změňte skupinu prostředků Azure nebo předplatné pro váš účet integrace.

   ![Zvolte "Změnit skupinu prostředků" nebo "Změnit předplatné"](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

4. Když jste hotovi, ujistěte se, aktualizovat všechny skripty se nové ID prostředku pro artefakty.  

## <a name="delete-integration-account"></a>Odstranění účtu integrace

1. V hlavní nabídce Azure, vyberte **všechny služby**. Do vyhledávacího pole zadejte "účty pro integraci" jako filtr a vyberte **účty pro integraci**.

   ![Najít váš účet integrace](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. V části **účty pro integraci**, vyberte účet integrace, který chcete odstranit. V nabídce účtu integrace zvolte **přehled**, zvolte **odstranit**. 

   ![Vyberte účet integrace. Na stránce "Přehled" Vyberte "Odstranit"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

3. Chcete-li potvrdit, že chcete odstranit účet integrace, zvolte **Ano**.

   ![Potvrďte volbu odstranit, zvolte tlačítko Ano.](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Další postup

* [Vytvoření obchodními partnery](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Vytvoření smlouvy](../logic-apps/logic-apps-enterprise-integration-agreements.md)
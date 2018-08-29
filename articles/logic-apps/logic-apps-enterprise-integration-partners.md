---
title: Přidat obchodní partneři pro B2B integrace – Azure Logic Apps | Dokumentace Microsoftu
description: Vytvořit obchodní partneři pro váš účet pro integraci v Azure Logic Apps sadou Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.date: 07/08/2016
ms.openlocfilehash: 20ca5e06cd1cd0d0abfe6d31f622cd6b61b4178f
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125257"
---
# <a name="add-trading-partners-for-integration-accounts-in-azure-logic-apps-with-enterprise-integration-pack"></a>Přidat obchodní partneři pro účty pro integraci v Azure Logic Apps sadou Enterprise Integration Pack

Partneři jsou entity, které se účastní transakce business-to-business (B2B) a výměnu zpráv mezi sebou. Předtím, než budete moct vytvořit partnerů, které představují vy a jiné organizace v tyto transakce, musí se obě sdílet informace, které identifikuje a ověřuje zprávy odesílané mezi sebou. Poté, co popisují tyto údaje a můžete začít obchodní vztah, můžete vytvořit představují oba partnery v účtu integrace.

## <a name="what-roles-do-partners-play-in-your-integration-account"></a>Jaké role nepodporují partneři play v integračním účtu?

Chcete-li definovat podrobnosti o zprávách vyměňují mezi partnery, vytvoříte smluv mezi těmito partnery. Ale předtím, než budete moct vytvořit smlouvu, musíte jste přidali alespoň dva partneři ke svému účtu integrace. Vaše organizace musí být součástí smlouvy, jako **partner s identitou hostitele**. Jiného partnera nebo **partner s identitou hosta** představuje organizace, která vyměňuje zprávy ve vaší organizaci. Partner s identitou hosta může být jiné společnosti, nebo dokonce oddělení ve vaší vlastní organizaci.

Po přidání těchto partnerů můžete vytvořit smlouvu.

Příjem a odesílání nastavení jsou orientované z hlediska hostované partnera. Nastavení příjmu v smlouvu například určit, jak hostované partnerské přijímá zprávy odeslané partner s identitou hosta. Nastavení odesílání smlouvy, označuje, jak hostované partnerské odesílá zprávy partner s identitou hosta.

## <a name="create-partner"></a>Vytvoření partnera

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. V hlavní nabídce Azure zvolte **všechny služby**. Do vyhledávacího pole zadejte "integrace" a pak vyberte **účty pro integraci**.

   ![Najít účet integrace](./media/logic-apps-enterprise-integration-partners/account-1.png)

3. V části **účty pro integraci**, vyberte účet integrace, ve které chcete přidat vaši partneři.

   ![Vyberte účet pro integraci](./media/logic-apps-enterprise-integration-partners/account-2.png)

4. Zvolte **partneři** dlaždici.

   ![Zvolte možnost "Partnerů."](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. V části **partneři**, zvolte **přidat**.

   ![Zvolte "Přidat"](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. Zadejte název svého partnera, a pak vyberte **kvalifikátor**. Zadejte **hodnotu** identifikovat dokumenty, které přijímají své aplikace. Jakmile budete hotovi, zvolte **OK**.

   ![Přidat podrobnosti o partnerovi](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. Zvolte **partneři** dlaždici znovu.

   ![Zvolte dlaždici "Partnerů."](./media/logic-apps-enterprise-integration-partners/partner-5.png)

   Nyní se zobrazí váš nový partner ochrany. 

   ![Nový partner ochrany před zobrazení](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="edit-partner"></a>Upravit partnera

1. V [webu Azure portal](https://portal.azure.com)vyhledejte a vyberte svůj účet integrace. Zvolte **partneři** dlaždici.

   ![Zvolte dlaždici "Partnerů."](./media/logic-apps-enterprise-integration-partners/edit.png)

2. V části **partnery**, vyberte partnera, který chcete upravit.

   ![Vyberte partnera, který odstranit](./media/logic-apps-enterprise-integration-partners/edit-1.png)

3. V části **aktualizace partnera**, proveďte požadované změny.
Až budete hotovi, zvolte **Uložit**. 

   ![Ujistěte se a uložte provedené změny](./media/logic-apps-enterprise-integration-partners/edit-2.png)

   Chcete-li změny zrušit, vyberte **zahodit**.

## <a name="delete-partner"></a>Odstranit partnera

1. V [webu Azure portal](https://portal.azure.com)vyhledejte a vyberte svůj účet integrace. Zvolte **partneři** dlaždici.

   ![Zvolte dlaždici "Partnerů."](./media/logic-apps-enterprise-integration-partners/delete.png)

2. V části **partnery**, vyberte partnera, který chcete odstranit.
Zvolte **odstranit**.

   ![Odstranit partnera](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>Další postup

* [Další informace o smlouvy](../logic-apps/logic-apps-enterprise-integration-agreements.md "přečtěte si víc o smlouvách enterprise integration")  


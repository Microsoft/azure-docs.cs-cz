---
title: Vytvořte partnery pro business-to-business (B2B) zprávy – Azure Logic Apps | Microsoft Docs
description: Informace o postupu přidání partnery ke svému účtu integrace s Logic Apps a Enterprise integračního balíčku
services: logic-apps
documentationcenter: .net,nodejs,java
author: divyaswarnkar
manager: jeconnoc
editor: ''
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4abfdaa8145f14bc6496e57ebce0da10cdd2c365
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299735"
---
# <a name="add-or-update-partners-in-business-to-business-agreements-in-your-workflow"></a>Přidat nebo aktualizovat partnery ve smlouvách business-to-business v pracovním postupu

Partneři jsou entity, které se účastní transakce (B2B) business-to-business výměnu zpráv mezi sebou. Před vytvořením partnery, které představují jste a jiné organizaci v tyto transakce, musí oba sdílet informace, které identifikuje a ověří zprávy odeslané podle sebe navzájem. Po popisují tyto podrobnosti a budete připravení zahájit relaci vaší firmy, můžete vytvořit partnerům ve vašem účtu integrace představují oba.

## <a name="what-roles-do-partners-play-in-your-integration-account"></a>Jaké role nepodporují partnery play ve vašem účtu integrace?

Zadat podrobnosti o zprávy vyměňují mezi partnery, vytvořte smlouvy mezi těmito partnery. Ale předtím, než vytvoříte smlouvu, je třeba přidat alespoň dva partneři ke svému účtu integrace. Vaše organizace musí být součástí smlouvy jako **hostitele partnera**. Druhého partnera nebo **hosta partnera** představuje organizace, která výměny zpráv ve vaší organizaci. Partner hostů může být jiné společnosti nebo i oddělení ve vaší vlastní organizaci.

Po přidání těchto partnerů, můžete vytvořit smlouvu.

Příjem a odesílání nastavení jsou orientované z hlediska hostované partnera. Nastavení škálování v smlouvu například určit, jak hostované partnera obdrží zpráv odeslaných z hosta partnera. Nastavení odesílání na smlouvě, uvádí, jak hostované partnera odešle zprávy do hostovaného partnera.

## <a name="create-partner"></a>Vytvoření partnera

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. V hlavní nabídce Azure, vyberte **všechny služby**. Do vyhledávacího pole zadejte "integraci" a potom vyberte **účty pro integraci**.

   ![Najít účet integrace](./media/logic-apps-enterprise-integration-partners/account-1.png)

3. V části **účty pro integraci**, vyberte účet integrace, ve které chcete přidat vaši partneři.

   ![Vyberte účet integrace](./media/logic-apps-enterprise-integration-partners/account-2.png)

4. Vyberte **partnery** dlaždici.

   ![Zvolte "Partners"](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. V části **partnery**, zvolte **přidat**.

   ![Zvolte "Přidat"](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. Zadejte název svého partnera, a pak vyberte **kvalifikátor**. Zadejte **hodnotu** k identifikaci dokumenty, které vaše aplikace zobrazí. Až budete hotoví, zvolte **OK**.

   ![Přidáním podrobností o partnera](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. Vyberte **partnery** dlaždici znovu.

   ![Vyberte že dlaždici "Partners"](./media/logic-apps-enterprise-integration-partners/partner-5.png)

   Nový partnerský se teď zobrazí. 

   ![Nový server pro zobrazení](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="edit-partner"></a>Upravit partnera

1. V [portál Azure](https://portal.azure.com), najděte a vyberte svůj účet integrace. Vyberte **partnery** dlaždici.

   ![Vyberte že dlaždici "Partners"](./media/logic-apps-enterprise-integration-partners/edit.png)

2. V části **partnery**, vyberte partnera, kterou chcete upravit.

   ![Vyberte partnera k odstranění](./media/logic-apps-enterprise-integration-partners/edit-1.png)

3. V části **aktualizace partnera**, provedené změny.
Jakmile jste hotovi, zvolte **Uložit**. 

   ![Ujistěte se a uložte změny](./media/logic-apps-enterprise-integration-partners/edit-2.png)

   Chcete-li zrušit změny, vyberte **zahodit**.

## <a name="delete-partner"></a>Odstranit partnera

1. V [portál Azure](https://portal.azure.com), najděte a vyberte svůj účet integrace. Vyberte **partnery** dlaždici.

   ![Vyberte že dlaždici "Partners"](./media/logic-apps-enterprise-integration-partners/delete.png)

2. V části **partnery**, vyberte partnera, který chcete odstranit.
Zvolte **odstranit**.

   ![Odstranit partnera](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>Další postup

* [Další informace o smlouvy](../logic-apps/logic-apps-enterprise-integration-agreements.md "Další informace o integraci smlouvy enterprise")  


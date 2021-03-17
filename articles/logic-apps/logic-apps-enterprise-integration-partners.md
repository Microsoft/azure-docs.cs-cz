---
title: Přidání obchodních partnerů pro integraci B2B
description: Vytváření obchodních partnerů v účtu pro integraci pro použití s Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 8e3805fae5bf6cc5ad8cf759d3ba75220c6ddbd8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91565067"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Přidání obchodních partnerů do účtů integrace pro Azure Logic Apps

V [Azure Logic Apps](../logic-apps/logic-apps-overview.md)můžete vytvářet automatizované pracovní postupy integrace B2B (Business-to-Business) pomocí [účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) s vašimi Logic Apps. Pro reprezentaci vaší organizace a dalších uživatelů můžete vytvořit a přidat obchodní partnery jako artefakty do účtu pro integraci. Partneři jsou entity, které se podílejí na transakcích B2B a vyměňují si mezi sebou zprávy.

Před vytvořením těchto partnerů nezapomeňte diskutovat a sdílet informace s vašimi partnery o tom, jak identifikovat a ověřit zprávy, které druhý odesílá. Jakmile souhlasíte s těmito podrobnostmi, jste připraveni vytvořit partnery v účtu pro integraci.

## <a name="partner-roles-in-integration-accounts"></a>Role partnerů v integračních účtech

Pokud chcete definovat podrobnosti o zprávách vyměňovaných s vašimi partnery, vytvořte a přidejte [smlouvy](../logic-apps/logic-apps-enterprise-integration-agreements.md) jako artefakty do účtu pro integraci. Smlouvy vyžadují aspoň dva partnery v účtu pro integraci. Vaše organizace je vždy *hostitelským partnerem* v rámci smlouvy. Organizace, která vyměňuje zprávy ve vaší organizaci, je *partnerem hosta*. Partner hosta může být jiná společnost nebo dokonce oddělení ve vaší organizaci. Po přidání těchto partnerů můžete vytvořit smlouvu.

V rámci smlouvy zadáte podrobnosti pro zpracování příchozích a odchozích zpráv z perspektivy hostitelského partnera. U příchozích zpráv **Nastavení příjmu** určují, jak má hostitelský partner dostávat zprávy od hostovaného partnera v rámci smlouvy. U odchozích zpráv **nastavení odesílání** určuje způsob, jakým hostující partner odesílá zprávy hostovanému partnerovi.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [Zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Účet pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) pro ukládání partnerů, smluv a dalších artefaktů B2B. Tento účet integrace musí být přidružený k vašemu předplatnému Azure.

## <a name="create-partner"></a>Vytvořit partnera

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. V hlavní nabídce Azure vyberte **všechny služby**. Do vyhledávacího pole zadejte "Integration" a vyberte **účty pro integraci**.

   ![Vyberte účty pro integraci.](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. V části **účty pro integraci**vyberte účet pro integraci, do kterého chcete přidat své partnery.

   ![Vybrat účet pro integraci](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. Vyberte dlaždici **partneři** .

   ![Snímek obrazovky, který zobrazuje dlaždici partneři](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. V části **partneři**klikněte na možnost **Přidat**. V části **Přidat partnera**zadejte podrobnosti o partnerovi, jak je popsáno v následující tabulce.

   ![Klikněte na tlačítko Přidat a zadejte podrobnosti o partnerovi.](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název** | Yes | Název partnera |
   | **Kvalifikátor** | Yes | Ověřovací text, který poskytuje organizacím jedinečné obchodní identity, například **D-U-N-S (Dun & Bradstreet)**. <p>Partneři se můžou rozhodnout pro vzájemně definovanou obchodní identitu. U těchto scénářů vyberte možnost **vzájemně definované** pro EDIFACT nebo **vzájemně definované (X12)** pro X12. <p>Pro RosettaNet vyberte pouze **Duns**, což je standard. |
   | **Hodnota** | Yes | Hodnota, která identifikuje dokumenty, které vaše aplikace logiky obdrží. <p>Pro RosettaNet musí být tato hodnota devět číslic, které odpovídají číslu DUNS. |
   ||||

   > [!NOTE]
   > Pro partnery, kteří používají RosettaNet, můžete zadat další informace tak, že nejdřív vytvoříte tyto partnery a následně [je upravíte](#edit-partner).

1. Po dokončení vyberte **OK**.

   Nový partner se teď zobrazí v seznamu **partneři** . Dlaždice **partneři** také aktualizuje aktuální počet partnerů.

   ![Nový partner](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>Upravit partnera

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte účet pro integraci.
Vyberte dlaždici **partneři** .

   ![Vybrat dlaždici partneři](./media/logic-apps-enterprise-integration-partners/edit.png)

1. V části **partneři**vyberte partnera, který chcete upravit, a klikněte na tlačítko **Upravit**. V části **Upravit**proveďte změny.

   ![Udělejte změny a uložte je.](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   V případě RosettaNet můžete v části **vlastnosti partnerů RosettaNet**zadat tyto další informace:

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Klasifikace partnerů** | No | Typ organizace partnera |
   | **Kód dodavatelského řetězce** | No | Kód dodavatelského řetězce partnera, například "informační technologie" nebo "elektronické součásti" |
   | **Jméno kontaktu** | No | Jméno kontaktní osoby partnera |
   | **E-mail** | No | E-mailová adresa partnera |
   | **Fax** | No | Faxové číslo partnera |
   | **Telephone** | No | Telefonní číslo partnera |
   ||||

1. Až budete hotovi, uložte změny kliknutím na **tlačítko OK** .

## <a name="delete-partner"></a>Odstranit partnera

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte účet pro integraci. Vyberte dlaždici **partneři** .

   ![Snímek obrazovky, který zobrazuje dlaždici partneři, kterou vyberete, když chcete odstranit partnera.](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. V části **partneři**vyberte partnera, kterého chcete odstranit. Zvolte **Odstranit**.

   ![Odstranit partnera](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>Další kroky

* Další informace o [smlouvách](../logic-apps/logic-apps-enterprise-integration-agreements.md)
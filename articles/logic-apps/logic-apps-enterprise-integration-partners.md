---
title: Přidání obchodních partnerů pro integraci B2B
description: Vytvořte si obchodní partnery ve svém integračním účtu pro použití s Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: e58cbe85f30ea09adde45d55bb7b80c710c45495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792443"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Přidání obchodních partnerů do integračních účtů pro Azure Logic Apps

V [Azure Logic Apps](../logic-apps/logic-apps-overview.md)můžete vytvářet automatizované pracovní postupy integrace mezi podniky (B2B) pomocí účtu [integrace](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) s aplikacemi logiky. Chcete-li reprezentovat vaši organizaci a ostatní, vytvořte a přidáte obchodní partnery jako artefakty do svého účtu integrace. Partneři jsou entity, které se účastní transakcí B2B a vzájemně si vyměňují zprávy.

Před vytvořením těchto partnerů, ujistěte se, že diskutovat a sdílet informace se svými partnery o tom, jak identifikovat a ověřit zprávy, které ostatní odesílá. Po odsouhlasení těchto podrobností jste připraveni vytvořit partnery ve svém integračním účtu.

## <a name="partner-roles-in-integration-accounts"></a>Role partnerů v integračních účtech

Chcete-li definovat podrobnosti o zprávách vyměňovaných s partnery, vytvořte a přidejte [smlouvy](../logic-apps/logic-apps-enterprise-integration-agreements.md) jako artefakty do účtu integrace. Smlouvy vyžadují alespoň dva partnery ve vašem integračním účtu. Vaše organizace je vždy *hostitelským partnerem* ve smlouvě. Organizace, která si vyměňuje zprávy s vaší organizací, je *partnerem hosta*. Hostujícím partnerem může být jiná společnost, nebo dokonce oddělení ve vaší vlastní organizaci. Po přidání těchto partnerů můžete vytvořit smlouvu.

Ve smlouvě zadáte podrobnosti pro zpracování příchozích a odchozích zpráv z pohledu hostitelského partnera. U příchozích zpráv určují **nastavení příjmu způsob,** jakým hostitelský partner přijímá zprávy od partnera hosta ve smlouvě. U odchozích zpráv určují **nastavení odesílání způsob,** jakým hostitelský partner odesílá zprávy partnerovi hosta.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Účet integrace](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) pro ukládání partnerů, smluv a dalších artefaktů B2B. Tento účet integrace musí být přidružený k vašemu předplatnému Azure.

## <a name="create-partner"></a>Vytvořit partnera

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. V hlavní nabídce Azure vyberte **Všechny služby**. Do vyhledávacího pole zadejte "integrace" a vyberte **Integrační účty**.

   ![Vyberte možnost "Integrační účty"](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. V části **Účty integrace**vyberte účet integrace, do kterého chcete přidat partnery.

   ![Vybrat účet integrace](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. Zvolte dlaždici **Partneři.**

   ![Zvolte dlaždici Partneři](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. V části **Partneři**zvolte **Přidat**. V části **Přidat partnera**uveďte podrobnosti o partnerovi, jak je popsáno v následující tabulce.

   ![Zvolte "Přidat" a poskytněte podrobnosti o partnerovi](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název** | Ano | Jméno partnera |
   | **Kvalifikátor** | Ano | Ověřovací orgán, který poskytuje jedinečné obchodní identity organizacím, například **D-U-N-S (Dun & Bradstreet)**. <p>Partneři se mohou rozhodnout pro vzájemně definovanou obchodní identitu. Pro tyto scénáře vyberte **vzájemně definované** pro EDIFACT nebo vzájemně **definované (X12)** pro X12. <p>Pro RosettaNet vyberte pouze **DUNS**, což je standard. |
   | **Hodnotu** | Ano | Hodnota, která identifikuje dokumenty, které vaše aplikace logiky obdrží. <p>Pro RosettaNet tato hodnota musí být devítimístné číslo, které odpovídá číslo DUNS. |
   ||||

   > [!NOTE]
   > Pro partnery, kteří používají RosettaNet, můžete zadat další informace tak, že nejprve vytvoříte tyto partnery a [potom je upravíte](#edit-partner).

1. Po dokončení vyberte **OK**.

   Váš nový partner se nyní zobrazí v seznamu **Partneři.** Dlaždice **Partneři** také aktualizuje aktuální počet partnerů.

   ![Nový partner](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>Upravit partnera

1. Na [webu Azure Portal](https://portal.azure.com)najděte a vyberte svůj účet integrace.
Zvolte dlaždici **Partneři.**

   ![Zvolte dlaždici Partneři](./media/logic-apps-enterprise-integration-partners/edit.png)

1. V části **Partneři**vyberte partnera, kterého chcete upravit, a zvolte **Upravit**. V části **Úpravy**proveďte změny.

   ![Provádění a ukládání změn](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   Pro RosettaNet můžete v části **Vlastnosti partnera RosettaNet**zadat tyto další informace:

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Klasifikace partnerů** | Ne | Typ organizace partnera |
   | **Kód dodavatelského řetězce** | Ne | Kód dodavatelského řetězce partnera, například "Informační technologie" nebo "Elektronické součástky" |
   | **Kontaktní osoba** | Ne | Kontaktní jméno partnera |
   | **E-mail** | Ne | E-mailová adresa partnera |
   | **Fax** | Ne | Faxové číslo partnera |
   | **Telephone** | Ne | Telefonní číslo partnera |
   ||||

1. Až budete hotovi, zvolte **OK,** chcete-li změny uložit.

## <a name="delete-partner"></a>Odstranit partnera

1. Na [webu Azure Portal](https://portal.azure.com)najděte a vyberte svůj účet integrace. Zvolte dlaždici **Partneři.**

   ![Zvolte dlaždici Partneři](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. V **části Partneři**vyberte partnera, kterého chcete odstranit. Zvolte **Odstranit**.

   ![Odstranit partnera](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>Další kroky

* Další informace o [smlouvách](../logic-apps/logic-apps-enterprise-integration-agreements.md)
---
title: Přidat obchodní partneři pro B2B integrace – Azure Logic Apps
description: Vytvořit obchodní partneři v integračním účtu pro použití s Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 681f16132c1de2ec5f3b27f80633d32879b0746c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330136"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Přidání obchodní partneři pro účty pro integraci pro Azure Logic Apps

V [Azure Logic Apps](../logic-apps/logic-apps-overview.md), automatizovaných business-to-business (B2B) integraci pracovních postupů můžete vytvořit pomocí [účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) s logic apps. K reprezentaci vaší organizace a další, vytvořte a přidejte obchodní partneři jako artefakty ke svému účtu integrace. Partneři jsou entity, které se účastní transakce B2B a výměnu zpráv mezi sebou.

Před vytvořením tito partneři, ujistěte se, že projednat a sdílet informace s partnery o tom, jak identifikovat a ověřit, druhý odesílá zprávy. Po odsouhlasení tyto podrobnosti jste připraveni vytvořit partnery v účtu integrace.

## <a name="partner-roles-in-integration-accounts"></a>Partner role v účty pro integraci

Zadat podrobnosti o zprávách vyměňují se svými partnery s, vytvoříte a přidáte [smlouvy](../logic-apps/logic-apps-enterprise-integration-agreements.md) jako artefakty k vašemu účtu integrace. Smlouvy o vyžadují aspoň dva partneři v účtu integrace. Vaše organizace je vždy *partner s identitou hostitele* ve smlouvě. Organizace, který vyměňuje zprávy s vaší organizací *partner s identitou hosta*. Partner s identitou hosta může být jiné společnosti, nebo dokonce oddělení ve vaší vlastní organizaci. Po přidání těchto partnerů můžete vytvořit smlouvu.

Ve smlouvě zadejte podrobnosti pro zpracování příchozích a odchozích zpráv z pohledu hostitele partnera. Pro příchozí zprávy **přijímat nastavení** určit, jak partner s identitou hostitele přijímá zprávy od partnera hostovaného ve smlouvě. Pro odchozí zprávy **odeslat nastavení** určit, jak partner s identitou hostitele odesílá zprávy do partner s identitou hosta.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte ještě předplatné Azure [zaregistrovat si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) pro ukládání vašich partnerů, smluv a jiných artefaktů B2B. Tato integrační účet musí být přidružený k vašemu předplatnému Azure.

## <a name="create-partner"></a>Vytvoření partnera

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V hlavní nabídce Azure zvolte **všechny služby**. Do vyhledávacího pole zadejte "integrace" a vyberte **účty pro integraci**.

   ![Vyberte "Integračních účtů"](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. V části **účty pro integraci**, vyberte účet integrace, ve které chcete přidat vaši partneři.

   ![Vyberte účet pro integraci](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. Zvolte **partneři** dlaždici.

   ![Zvolte dlaždici "Partnerů."](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. V části **partneři**, zvolte **přidat**. V části **přidat partnera**, zadejte podrobnosti partnera, jak je popsáno v následující tabulce.

   ![Zvolte "Přidat" a zadejte podrobnosti o partnerovi](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název** | Ano | Na název partnera |
   | **Kvalifikátor** | Ano | Ověřovací subjekt, který obsahuje jedinečné obchodní identity pro organizace, například **D-U-N-S (Dun & Bradstreet)** . <p>Partneři můžou rozhodnout pro vzájemně definované obchodní identity. Pro tyto scénáře vyberte **vzájemně definované** pro EDIFACT nebo **vzájemně definované (X12)** pro X12. <p>RosettaNet, vyberte pouze **DUNS**, což je standard. |
   | **Hodnota** | Ano | Hodnota, která identifikuje dokumenty, které přijímají své aplikace logiky. <p>Pro RosettaNet tato hodnota musí být devět číslic, který odpovídá číslu DUNS. |
   ||||

   > [!NOTE]
   > Pro partnery, které používají RosettaNet, můžete zadat další informace o první vytvořením tito partneři a potom [upravovat později](#edit-partner).

1. Jakmile budete hotovi, zvolte **OK**.

   Váš nový partner ochrany se zobrazí na **partneři** seznamu. Také **partneři** dlaždice aktualizuje aktuální počet partnerů.

   ![Nový partner ochrany](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>Upravit partnera

1. V [webu Azure portal](https://portal.azure.com)vyhledejte a vyberte svůj účet integrace.
Zvolte **partneři** dlaždici.

   ![Zvolte dlaždici "Partnerů."](./media/logic-apps-enterprise-integration-partners/edit.png)

1. V části **partneři**, vyberte partnera, který chcete upravit a zvolte **upravit**. V části **upravit**, proveďte požadované změny.

   ![Ujistěte se a uložte provedené změny](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   Pro RosettaNet v části **RosettaNet partnera vlastnosti**, můžete zadat tyto další informace:

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Klasifikace partnera** | Ne | Typ organizace partnera |
   | **Kód dodavatelský řetězec** | Ne | Partnerské organizace dodavatelského řetězce kódu, například "Informačních technologií" nebo "Elektronických komponent" |
   | **Jméno kontaktu** | Ne | Jméno kontaktní osoby partnerské organizace |
   | **E-mailu** | Ne | E-mailovou adresu partnera |
   | **Fax** | Ne | Partnerské organizace faxové číslo |
   | **Telefon** | Ne | Partnerské organizace telefonní číslo |
   ||||

1. Jakmile budete hotovi, zvolte **OK** uložte provedené změny.

## <a name="delete-partner"></a>Odstranit partnera

1. V [webu Azure portal](https://portal.azure.com)vyhledejte a vyberte svůj účet integrace. Zvolte **partneři** dlaždici.

   ![Zvolte dlaždici "Partnerů."](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. V části **partnery**, vyberte partnera, který chcete odstranit. Zvolte **odstranit**.

   ![Odstranit partnera](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>Další postup

* Další informace o [smlouvy](../logic-apps/logic-apps-enterprise-integration-agreements.md)
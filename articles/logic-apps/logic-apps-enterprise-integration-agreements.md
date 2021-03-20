---
title: Smlouvy s obchodním partnerem
description: Vytváření a Správa smluv mezi obchodními partnery pomocí Azure Logic Apps a Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: c8cbfb619c9eed325161503f705bf5c4c0746265
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95992942"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>Vytvoření a správa smluv obchodních partnerů v Azure Logic Apps

[Obchodní Partnerská](../logic-apps/logic-apps-enterprise-integration-partners.md)  
 *smlouva* pomáhá organizacím a firmám vzájemně komunikovat pomocí definování specifického standardního protokolu, který se použije při výměně zpráv B2B (Business-to-Business). Smlouvy poskytují běžné výhody, například:

* Umožněte organizacím vyměňovat si informace pomocí známého formátu.
* Zvyšte efektivitu při provádění transakcí B2B.
* Je snadné vytvořit, spravovat a používat k vytváření podnikových integračních řešení.

V tomto článku se dozvíte, jak vytvořit smlouvu AS2, EDIFACT nebo X12, kterou můžete použít při vytváření podnikových integračních řešení pro scénáře B2B pomocí [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) a [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Po vytvoření smlouvy můžete pro výměnu zpráv B2B použít konektory AS2, EDIFACT nebo X12.

Pokud chcete vytvořit smlouvy pro výměnu zpráv RosettaNet, přečtěte si téma [zprávy Exchange RosettaNet](../logic-apps/logic-apps-enterprise-integration-rosettanet.md).

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [Zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Účet pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) pro ukládání vaší smlouvy a dalších artefaktů B2B. Tento účet integrace musí být přidružený k vašemu předplatnému Azure.

* Aspoň dva [obchodní partneři](../logic-apps/logic-apps-enterprise-integration-partners.md) , které jste už vytvořili v účtu pro integraci. Smlouva vyžaduje hostitele partnera i partnera typu Host. Oba partneři musí používat stejnou "obchodní identitu" jako smlouvu, kterou chcete vytvořit, jako je například AS2, X12 nebo EDIFACT.

* Volitelné: aplikace logiky, ve které chcete použít vaši smlouvu, a Trigger, který spouští pracovní postup vaší aplikace logiky. Pokud chcete jenom vytvořit účet pro integraci a artefakty B2B, nepotřebujete aplikaci logiky. Předtím, než aplikace logiky může použít artefakty B2B v účtu integrace, je třeba propojit účet pro integraci s vaší aplikací logiky. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý Start: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-agreements"></a>Vytvoření smluv

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
V hlavní nabídce Azure vyberte **všechny služby**. Do vyhledávacího pole zadejte jako filtr "integr". Z výsledků vyberte tento prostředek: účty pro **integraci** .

   ![Najít účet pro integraci](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. V části **účty pro integraci** vyberte účet pro integraci, ve kterém chcete vytvořit smlouvu.

   ![Vyberte účet pro integraci, ve kterém chcete vytvořit smlouvu.](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. V pravém podokně v části **komponenty** vyberte dlaždici **smlouvy** .

   ![Zvolit "smlouvy"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. V části **smlouvy** klikněte na možnost **Přidat**. V podokně **Přidat** zadejte informace o vaší smlouvě, například:

   ![Zvolit přidat](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Název** | Yes | <*smlouva – název*> | Název vaší smlouvy |
   | **Typ smlouvy** | Yes | **AS2**, **X12** nebo **EDIFACT** | Typ protokolu pro vaši smlouvu. Při vytváření souboru smlouvy musí obsah v tomto souboru odpovídat typu smlouvy. | |  
   | **Partner hostitele** | Yes | <*Host-partner – název*> | Partner hostitele představuje organizaci, která určuje smlouvu. |
   | **Hostitelská identita** | Yes | <*Host-partner – identifikátor*> | Identifikátor hostitelského partnera |
   | **Partner hosta** | Yes | <*Host – partner – název*> | Partner hosta představuje organizaci, která provádí podnikání s hostitelským partnerem. |
   | **Identita hosta** | Yes | <*Host – partner – identifikátor*> | Identifikátor hostovaného partnera |
   | **Nastavení příjmu** | Různé | Různé | Tyto vlastnosti určují, jak hostující partner obdrží všechny příchozí zprávy od hostovaného partnera v rámci smlouvy. Další informace najdete v příslušném typu smlouvy: <p>- [Nastavení zprávy AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Nastavení zprávy EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Nastavení zprávy X12](logic-apps-enterprise-integration-x12.md) |
   | **Nastavení odesílání** | Různé | Různé | Tyto vlastnosti určují, jak hostující partner odesílá všechny odchozí zprávy partnerovi hosta v rámci smlouvy. Další informace najdete v příslušném typu smlouvy: <p>- [Nastavení zprávy AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Nastavení zprávy EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Nastavení zprávy X12](logic-apps-enterprise-integration-x12.md) |
   |||||

   > [!IMPORTANT]
   > Řešení smlouvy závisí na shodě s těmito položkami, které jsou definovány v partnerovi a příchozí zprávě:
   >
   > * Kvalifikátor a identifikátor odesílatele
   > * Kvalifikátor a identifikátor přijímače
   >
   > Pokud se tyto hodnoty pro vašeho partnera změní, ujistěte se, že jste smlouvu aktualizovali.

1. Až budete s vytvářením smlouvy hotovi, klikněte na stránce **Přidat** na **tlačítko OK** a vraťte se k účtu pro integraci.

   V seznamu **smlouvy** se teď zobrazí vaše nová smlouva.

## <a name="edit-agreements"></a>Upravit smlouvy

1. V [Azure Portal](https://portal.azure.com)v hlavní nabídce Azure vyberte **všechny služby**.

1. Do vyhledávacího pole zadejte jako filtr "integr". Z výsledků vyberte tento prostředek: účty pro **integraci** .

1. V části **účty pro integraci** vyberte účet pro integraci s smlouvou, kterou chcete upravit.

1. V pravém podokně v části **komponenty** vyberte dlaždici **smlouvy** .

1. V části **smlouvy** vyberte smlouvu a zvolte **Upravit**.

1. Zajistěte a uložte provedené změny.

## <a name="delete-agreements"></a>Odstranit smlouvy

1. V [Azure Portal](https://portal.azure.com)v hlavní nabídce Azure vyberte **všechny služby**.

1. Do vyhledávacího pole zadejte jako filtr "integr". Z výsledků vyberte tento prostředek: účty pro **integraci** .

1. V části **účty pro integraci** vyberte účet pro integraci s smlouvou, kterou chcete odstranit.

1. V pravém podokně v části **komponenty** vyberte dlaždici **smlouvy** .

1. V části **smlouvy** vyberte smlouvu a zvolte **Odstranit**.

1. Potvrďte, že chcete odstranit vybranou smlouvu.

## <a name="next-steps"></a>Další kroky

* [Výměna zpráv AS2](logic-apps-enterprise-integration-as2.md)
* [Výměna zpráv EDIFACT](logic-apps-enterprise-integration-edifact.md)
* [Výměna zpráv X12](logic-apps-enterprise-integration-x12.md)

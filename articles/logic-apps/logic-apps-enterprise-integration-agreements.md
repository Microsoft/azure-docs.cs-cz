---
title: Dohody obchodních partnerů
description: Vytváření a správa smluv mezi obchodními partnery pomocí Azure Logic Apps a Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 521a0ef4053be55e6c7322da5af26ccfc6c844e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790740"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>Vytváření a správa smluv s obchodními partnery v Aplikacích Azure Logic Apps

*Dohoda obchodního* [partnera](../logic-apps/logic-apps-enterprise-integration-partners.md) 
pomáhá organizacím a firmám bezproblémovou vzájemnou komunikaci tím, že definuje konkrétní standardní protokol, který se má použít při výměně zpráv mezi podniky (B2B). Dohody poskytují společné výhody, například:

* Umožněte organizacím výměnu informací pomocí známého formátu.
* Zlepšete efektivitu při provádění transakcí B2B.
* Jsou snadno vytvářet, spravovat a používat pro vytváření řešení podnikové integrace.

Tento článek ukazuje, jak vytvořit dohodu AS2, EDIFACT nebo X12, kterou můžete použít při vytváření podnikových integračních řešení pro scénáře B2B pomocí [sady Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) a Azure Logic [Apps](../logic-apps/logic-apps-overview.md). Po vytvoření smlouvy pak můžete použít konektory AS2, EDIFACT nebo X12 pro výměnu zpráv B2B.

Chcete-li vytvořit smlouvy pro výměnu zpráv RosettaNet, naleznete [v tématu Exchange RosettaNet zprávy](../logic-apps/logic-apps-enterprise-integration-rosettanet.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Účet integrace](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) pro ukládání vaší smlouvy a další artefakty B2B. Tento účet integrace musí být přidružený k vašemu předplatnému Azure.

* Alespoň dva [obchodní partneři,](../logic-apps/logic-apps-enterprise-integration-partners.md) které jste již vytvořili ve svém integračním účtu. Dohoda vyžaduje hostitelského partnera i partnera hosta. Oba partneři musí používat stejný kvalifikátor "obchodní identity" jako smlouvu, kterou chcete vytvořit, například AS2, X12 nebo EDIFACT.

* Volitelné: Aplikace logiky, kde chcete použít smlouvu a aktivační událost, která spustí pracovní postup aplikace logiky. Chcete-li pouze vytvořit účet integrace a artefakty B2B, nepotřebujete aplikaci logiky. Však před aplikace logiky můžete použít artefakty B2B v účtu integrace, musíte propojit účet integrace s aplikací logiky. Pokud s aplikacemi logiky teprve začínáte, přečtěte [si, co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a úvodní [příručka: Vytvořte si první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-agreements"></a>Vytvořit smlouvy

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
V hlavní nabídce Azure vyberte **Všechny služby**. Do vyhledávacího pole zadejte jako filtr "integrace". Z výsledků vyberte tento zdroj: **Účty integrace**

   ![Najděte svůj integrační účet](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. V části **Účty integrace**vyberte účet integrace, ve kterém chcete vytvořit smlouvu.

   ![Vyberte účet integrace, kde chcete vytvořit smlouvu.](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. V pravém podokně v části **Komponenty**zvolte dlaždici **Smlouvy.**

   ![Zvolte "Dohody"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. V části **Smlouvy**zvolte **Přidat**. V podokně **Přidat** zadejte informace o smlouvě, například:

   ![Zvolte "Přidat"](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Název** | Ano | <*název smlouvy*> | Název vaší smlouvy |
   | **Typ smlouvy** | Ano | **AS2**, **X12**nebo **EDIFACT** | Typ protokolu pro vaši smlouvu. Při vytváření souboru smlouvy musí obsah v tomto souboru odpovídat typu smlouvy. | |  
   | **Hostitelský partner** | Ano | <*název hostitele-partnera*> | Hostitelský partner představuje organizaci, která určuje smlouvu |
   | **Identita hostitele** | Ano | <*identifikátor hostitele a partnera*> | Identifikátor hostitelského partnera |
   | **Partner pro hosty** | Ano | <*jméno host-partner*> | Partner hosta zastupuje organizaci, která obchoduje s hostitelským partnerem |
   | **Identita hosta** | Ano | <*identifikátor host-partner-*> | Identifikátor partnera hosta |
   | **Přijímat nastavení** | Různé | Různé | Tyto vlastnosti určují, jak hostitelský partner obdrží všechny příchozí zprávy od partnera hosta ve smlouvě. Další informace naleznete v příslušném typu smlouvy: <p>- [Nastavení zprávy AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Nastavení zpráv EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Nastavení zprávy X12](logic-apps-enterprise-integration-x12.md) |
   | **Odeslat nastavení** | Různé | Různé | Tyto vlastnosti určují, jak hostitelský partner odesílá všechny odchozí zprávy partnerovi hosta ve smlouvě. Další informace naleznete v příslušném typu smlouvy: <p>- [Nastavení zprávy AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Nastavení zpráv EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Nastavení zprávy X12](logic-apps-enterprise-integration-x12.md) |
   |||||

1. Po dokončení vytváření smlouvy zvolte na stránce **Přidat** **možnost OK**a vraťte se ke svému účtu pro integraci.

   Seznam **Smluv** nyní zobrazuje vaši novou smlouvu.

## <a name="edit-agreements"></a>Upravit smlouvy

1. Na [webu Azure Portal](https://portal.azure.com)v hlavní nabídce Azure vyberte **Všechny služby**.

1. Do vyhledávacího pole zadejte jako filtr "integrace". Z výsledků vyberte tento zdroj: **Účty integrace**

1. V části **Účty integrace**vyberte účet integrace, který má smlouvu, kterou chcete upravit.

1. V pravém podokně v části **Komponenty**zvolte dlaždici **Smlouvy.**

1. V **části Smlouvy**vyberte smlouvu a zvolte **Upravit**.

1. Proveďte a uložte změny.

## <a name="delete-agreements"></a>Odstranit smlouvy

1. Na [webu Azure Portal](https://portal.azure.com)v hlavní nabídce Azure vyberte **Všechny služby**.

1. Do vyhledávacího pole zadejte jako filtr "integrace". Z výsledků vyberte tento zdroj: **Účty integrace**

1. V části **Účty integrace**vyberte účet integrace, který má smlouvu, kterou chcete odstranit.

1. V pravém podokně v části **Komponenty**zvolte dlaždici **Smlouvy.**

1. V **části Smlouvy**vyberte smlouvu a zvolte **Odstranit**.

1. Potvrďte, že chcete vybranou smlouvu odstranit.

## <a name="next-steps"></a>Další kroky

* [Výměna zpráv AS2](logic-apps-enterprise-integration-as2.md)
* [Výměna zpráv EDIFACT](logic-apps-enterprise-integration-edifact.md)
* [Výměna zpráv X12](logic-apps-enterprise-integration-x12.md)

---
title: Vytvoření a Správa smluv s obchodními partnery – Azure Logic Apps
description: Vytvoření a Správa smluv mezi obchodními partnery s využitím Azure Logic Apps a Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 447ffb8e-3e91-4403-872b-2f496495899d
ms.date: 04/05/2019
ms.openlocfilehash: 26d653b873e959f0804e0456ed87ee68c39413e5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720671"
---
# <a name="create-and-manage-trading-partner-agreements-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>Vytvoření a Správa smluv s obchodními partnery s využitím Azure Logic Apps a Enterprise Integration Pack

A [obchodní partner](../logic-apps/logic-apps-enterprise-integration-partners.md) 
*smlouvy* pomáhá organizacím a podnikům bez problémů komunikovat mezi sebou definováním konkrétní standardní průmyslový protokol, pro použití při výměně zpráv Business-to-business (B2B). Smlouvy o poskytují běžné výhody, třeba:

* Umožňují organizacím k výměně informací s využitím dobře známý formát.
* Zlepšení efektivity při provádění B2B transakce.
* Je snadné vytvořit, spravovat a používat pro sestavování integračních řešení enterprise.

Tento článek popisuje, jak vytvořit AS2, EDIFACT nebo X12 smlouvy, který vám pomůže při vytváření integrovaných řešení pro scénáře B2B enterprise s použitím [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) a [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Když vytvoříte smlouvu, potom můžete použít AS2, EDIFACT nebo X12 konektory pro výměnu zpráv B2B.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte ještě předplatné Azure [zaregistrovat si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) k vaší smlouvě a jiných artefaktů B2B. Tato integrační účet musí být přidružený k vašemu předplatnému Azure.

* Alespoň dva [obchodními partnery](../logic-apps/logic-apps-enterprise-integration-partners.md) , který jste vytvořili v účtu integrace. Smlouva vyžaduje partnera hostitele i hosta partnera. Oba partneři musí používat stejný kvalifikátor "obchodní identity" jako smlouvou, kterou chcete vytvořit, jako je například AS2, X 12 nebo EDIFACT.

* Volitelné: Aplikace logiky, ve které chcete použít vaši smlouvu a aktivační události, která spustí pracovní postup aplikace logiky. Jenom vytvořit integrační účet a artefaktů B2B, není nutné aplikaci logiky. Ale předtím, než aplikace logiky můžete použít artefaktů B2B v účtu integrace, třeba propojit účet integrace do aplikace logiky. Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý start: Vytvořte svou první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-agreements"></a>Vytvoření smlouvy

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
V hlavní nabídce Azure zvolte **všechny služby**. Do vyhledávacího pole zadejte jako filtr "integrace". Ve výsledcích vyberte tento prostředek: **Účty pro integraci**

   ![Vyhledejte svůj účet integrace](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. V části **účty pro integraci**, vyberte účet integrace, ve kterém chcete vytvořit smlouvu.

   ![Vyberte místo pro vytvoření smlouvu účtu integrace](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. V pravém podokně v části **součásti**, zvolte **smlouvy** dlaždici.

   ![Zvolte možnost "Smlouvy"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. V části **smlouvy**, zvolte **přidat**. V **přidat** podokně zadejte informace o smlouvě, například:

   ![Zvolte "Přidat"](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | Vlastnost | Požaduje se | Value | Popis |
   |----------|----------|-------|-------------|
   | **Název** | Ano | <*agreement-name*> | Název smlouvy |
   | **Typ smlouvy** | Ano | **AS2**, **X12**, nebo **EDIFACT** | Typ protokolu pro vaši smlouvu. Při vytváření souboru smlouvy obsah v tomto souboru musí odpovídat typ smlouvy. | |  
   | **Host Partner** | Ano | <*název hostitele partnera*> | Představuje hostitele partnera organizace, která určuje smlouvu |
   | **Identita hostitele** | Ano | <*host-partner-identifier*> | Identifikátor hostitele partnera |
   | **Partner s identitou hosta** | Ano | <*jméno partnera hosta*> | Představuje organizace, která je podnikající s partnerem hostitele partner s identitou hosta |
   | **Identita hosta** | Ano | <*guest-partner-identifier*> | Identifikátor partner hosta |
   | **Zobrazit nastavení** | Různé | Různé | Tyto vlastnosti určit způsob zpracování všechny příchozí zprávy přijaté službou smlouvy. Další informace najdete v tématu Typ příslušné smlouvy: <p>- [Nastavení zpráv AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Nastavení zpráv EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [X12 zprávy nastavení](logic-apps-enterprise-integration-x12.md) |
   | **Nastavení odesílání** | Různé | Různé | Tyto vlastnosti určit způsob zpracování všech odchozí zprávy odeslané smlouvě. Další informace najdete v tématu Typ příslušné smlouvy: <p>- [Nastavení zpráv AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Nastavení zpráv EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [X12 zprávy nastavení](logic-apps-enterprise-integration-x12.md) |
   |||||

1. Po dokončení vytváření vaší smlouvě na **přidat** zvolte **OK**a vraťte se ke svému účtu integrace.

   **Smlouvy** seznam nyní zobrazuje nové smlouvy.

## <a name="edit-agreements"></a>Úprava smluv

1. V [webu Azure portal](https://portal.azure.com), v hlavní nabídce Azure zvolte **všechny služby**.

1. Do vyhledávacího pole zadejte jako filtr "integrace". Ve výsledcích vyberte tento prostředek: **Účty pro integraci**

1. V části **účty pro integraci**, vyberte účet integrace, který má smlouvu, kterou chcete upravit.

1. V pravém podokně v části **součásti**, zvolte **smlouvy** dlaždici.

1. V části **smlouvy**, vyberte smlouvy a zvolte **upravit**.

1. Ujistěte se a pak uložte provedené změny.

## <a name="delete-agreements"></a>Odstranění smluv

1. V [webu Azure portal](https://portal.azure.com), v hlavní nabídce Azure zvolte **všechny služby**.

1. Do vyhledávacího pole zadejte jako filtr "integrace". Ve výsledcích vyberte tento prostředek: **Účty pro integraci**

1. V části **účty pro integraci**, vyberte účet integrace, který má smlouvu, kterou chcete odstranit.

1. V pravém podokně v části **součásti**, zvolte **smlouvy** dlaždici.

1. V části **smlouvy**, vyberte smlouvy a zvolte **odstranit**.

1. Potvrďte, že chcete odstranit vybrané smlouvy.

## <a name="next-steps"></a>Další postup

* [Výměna zpráv AS2](logic-apps-enterprise-integration-as2.md)
* [Výměna zpráv EDIFACT](logic-apps-enterprise-integration-edifact.md)
* [Výměna X12 zpráv](logic-apps-enterprise-integration-x12.md)

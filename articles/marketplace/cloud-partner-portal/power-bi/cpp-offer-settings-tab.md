---
title: Nastavení nabídky pro aplikaci Power BI | Azure Marketplace
description: Nakonfigurujte nastavení nabídky pro Power BI nabídku aplikace pro Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: ca326b10a0707d5e4b1a5f05dccc303c9ec28269
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822666"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Karta nastavení nabídky pro Power BI aplikace

Když otevřete novou stránku **nabídky** pro aplikace služby, zobrazí se nejprve karta **nastavení nabídky** . Na této kartě můžete zadat primární identifikátory a název vaší nabídky. Hvězdička (*) označuje povinné pole.

![Karta nastavení nabídky](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Pole nastavení nabídky 

Na kartě **nastavení nabídky** musíte zadat informace do následujících povinných polí. Požadovaná pole jsou indicted hvězdičkou (*).

|  Pole        |  Popis                                                               |
|---------------|----------------------------------------------------------------------------|
| **ID nabídky\***  | Jedinečný identifikátor (v rámci profilu vydavatele) pro nabídku. Tento identifikátor bude viditelný v adresách URL produktů, Azure Resource Manager šablonách a fakturačních sestavách. Maximální délka je 50 znaků. Může obsahovat jenom malé alfanumerické znaky a spojovníky (-). Nemůže končit pomlčkou. Po živé nabídce se tento identifikátor nedá změnit. Pokud contoso zveřejňuje nabídku s ID nabídky `sample-SvcApp`, přiřadí se k této nabídce `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`adresa URL AppSource.      |
| **\* vydavatele** | Jedinečný identifikátor vaší organizace v [AppSource](https://appsource.microsoft.com). Všechny vaše nabídky by se měly přidružit k vašemu vydavateli s vaším ID. Tuto hodnotu nelze po uložení nabídky změnit.                         |
| **Název\***      | Zobrazovaný název vaší nabídky Tento název se zobrazí v AppSource a na portál partnerů cloudu. Maximální délka je 50 znaků. Použijte název značky, který se pro váš produkt rozpoznává. Sem zadejte název vaší organizace, pokud není tato aplikace na trhu s tímto názvem. Pokud poskytujete tuto nabídku na jiné weby a publikace, použijte stejný název ve všech publikacích.    <br/>Pokud nabídku vydáte během období Preview pro aplikace Power BI, přidejte `(Preview)` řetězce na konci názvu vaší aplikace, například takto: `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>Další kroky

Na další kartě zadáte [technické informace](./cpp-technical-info-tab.md) pro vaši nabídku.

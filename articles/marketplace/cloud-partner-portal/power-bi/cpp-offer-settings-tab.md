---
title: Nastavení nabídky pro aplikaci Power BI | Azure Marketplace
description: Nakonfigurujte nastavení nabídky pro Power BI nabídku aplikace pro Microsoft AppSource Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: ddedc15b41b2b163f85f5443275daf9fbb56bac8
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143034"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Karta nastavení nabídky pro Power BI aplikace

>[!Important]
>Od 13. dubna 2020 začneme přesouvat správu nabídek Power BI aplikací do partnerského centra. Po dokončení migrace vytvoříte a budete spravovat své nabídky v partnerském centru. Při správě migrovaných nabídek postupujte podle pokynů v tématu [Přehled vytvoření aplikace Power BI](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer) .

Když otevřete novou stránku **nabídky** pro aplikace služby, zobrazí se nejprve karta **nastavení nabídky** . Na této kartě můžete zadat primární identifikátory a název vaší nabídky. Hvězdička (*) označuje povinné pole.

![Karta nastavení nabídky](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Pole nastavení nabídky 

Na kartě **nastavení nabídky** musíte zadat informace do následujících povinných polí. Požadovaná pole jsou indicted hvězdičkou (*).

|  Pole        |  Popis                                                               |
|---------------|----------------------------------------------------------------------------|
| **ID nabídky\***  | Jedinečný identifikátor (v rámci profilu vydavatele) pro nabídku. Tento identifikátor bude viditelný v adresách URL produktů, Azure Resource Manager šablonách a fakturačních sestavách. Maximální délka je 50 znaků. Může obsahovat jenom malé alfanumerické znaky a spojovníky (-). Nemůže končit pomlčkou. Po živé nabídce se tento identifikátor nedá změnit. Pokud contoso zveřejňuje nabídku s ID `sample-SvcApp`nabídky, přiřadí se této nabídce adresa URL `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`AppSource.      |
| **Microsoft\*** | Jedinečný identifikátor vaší organizace v [AppSource](https://appsource.microsoft.com). Všechny vaše nabídky by se měly přidružit k vašemu vydavateli s vaším ID. Tuto hodnotu nelze po uložení nabídky změnit.                         |
| **Název\***      | Zobrazovaný název vaší nabídky Tento název se zobrazí v AppSource a na portál partnerů cloudu. Maximální délka je 50 znaků. Použijte název značky, který se pro váš produkt rozpoznává. Sem zadejte název vaší organizace, pokud není tato aplikace na trhu s tímto názvem. Pokud poskytujete tuto nabídku na jiné weby a publikace, použijte stejný název ve všech publikacích.    <br/>Pokud nabídku vydáte během období Preview pro aplikace Power BI, přidejte řetězec `(Preview)` na konec názvu vaší aplikace, například:. `Sample Scv App (Preview)` |
|     |     |


## <a name="next-steps"></a>Další kroky

Na další kartě zadáte [technické informace](./cpp-technical-info-tab.md) pro vaši nabídku.

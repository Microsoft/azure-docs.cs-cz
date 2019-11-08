---
title: Definování nastavení nabídek pro nabídku konzultační služby | Azure Marketplace
description: Definujte nastavení nabídek v nabídce služby Azure nebo Dynamics 365 konzultační služby v portál partnerů cloudu pro Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: e505f9aa1ec08708b85176d5b05fc5b40ceb295e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818131"
---
# <a name="offer-settings-tab"></a>Karta nastavení nabídky

Na obrazovce **Nová nabídka** je prvním krokem vytvoření identity nabídky. Identita nabídky se skládá ze tří částí: **ID nabídky**, **ID vydavatele**a **název**. Každá z těchto částí je zahrnuta v následujících částech.

![Vytvoření nové nabídky konzultační služby – karta nastavení nabídky](media/consultingoffer-settings-tab.png)


### <a name="offer-id"></a>ID nabídky *

Tento identifikátor je jedinečný název, který vytvoříte při prvním odeslání nabídky. Musí obsahovat jenom malé alfanumerické znaky, pomlčky nebo podtržítka. **ID nabídky** je viditelné v adrese URL a má vliv na výsledky vyhledávacích strojů. Příklad je *yourcompanyname_exampleservice*.

Jak je znázorněno v příkladu, **ID nabídky** se připojí k vašemu vydavateli s ID, aby bylo možné vytvořit jedinečný identifikátor. Tento jedinečný identifikátor je zveřejněn jako trvalý odkaz, který může být rezervován a indexován vyhledávacími moduly.

>[!Note]
>Jakmile je nabídka živá, její identifikátor nejde aktualizovat.


### <a name="publisher-id"></a>ID vydavatele *

Tento identifikátor se vztahuje k vašemu účtu. Po přihlášení pomocí účtu organizace se v rozevírací nabídce zobrazí **ID vydavatele** .


### <a name="name"></a>Název

Tento řetězec se zobrazí jako název nabídky na AppSource nebo v Azure Marketplace. Pole **název** je omezené na 50 znaků. Recenzent možná bude muset upravit svůj název a připojit k názvu nabídky dobu trvání a typ nabídky.

Následující příklad ukazuje, jak je sestaven název nabídky. 

![Vytvořit novou nabídku konzultační služby](media/cppsampleconsultingoffer.png)

Název nabídky se skládá ze čtyř částí:

-   **Doba trvání:** Definováno na kartě **prezentace Details (podrobnosti** ) editoru. Trvání lze vyjádřit v hodinách, dnech nebo týdnech.
-   **Typ služby:** Definováno na kartě **prezentace Details (podrobnosti** ) editoru. Typy služeb jsou `Assessment`, `Briefing`, `Implementation`, `Proof of concept`a `Workshop`.
-   **Předpozice:** Vloženo kontrolorem.
-   **Název:** Definováno na stránce **nastavení nabídky** .

>[!Note]
>Pole **název** je omezené na 50 znaků. Recenzent možná bude muset upravit svůj název a připojit k názvu nabídky dobu trvání a typ nabídky.

Následující seznam uvádí několik dobře pojmenovaných názvů nabídek:

-   Základy pro profesionální služby: 1 – Přehled lidských zdrojů
-   Platforma Migrace do cloudu: 1 – Přehled lidských zdrojů
-   PowerApps a Microsoft Flow: 1. den dílny
-   Azure Machine Learning: 3 – týden pro ověření koncepce
-   Cihla a kliknutí na prodejní řešení: 1 – Přehled o HR
-   Přineste si vlastní data: 1-týden Workshop
-   Cloud Analytics: 3. den dílny
-   Školení Power BI: 3. den dílny
-   Řešení pro správu prodeje: implementace v průběhu 1 týdne
-   Rychlý Start CRM: 1. den dílny
-   Dynamics 365 for Sales: posouzení dvou dnů

Po vyplnění karty **nastavení nabídky** uložte odeslání. Název nabídky se teď zobrazí nad editorem a můžete ho najít ve **všech nabídkách**.

## <a name="next-steps"></a>Další kroky

Teď můžete zadat [prezentace podrobnosti a určit, jestli se má publikovat v Azure Marketplace nebo na AppSource](./cpp-consulting-service-storefront-details.md).

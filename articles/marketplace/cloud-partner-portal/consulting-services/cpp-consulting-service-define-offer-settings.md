---
title: Azure a Dynamics 365 nabídku konzultační služby – definují nastavení nabídky | Dokumentace Microsoftu
description: Příručka pro definování nabízejí nastavení do Azure nebo Dynamics 365 konzultační služby nabídky v portál partnerů cloudu.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: pbutlerm
ms.openlocfilehash: 9cb52aea56e9c1183af9ca76201ab65c62a58f22
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53972167"
---
# <a name="offer-settings-tab"></a>Karta nastavení nabídky

Na **nová nabídka** obrazovky, prvním krokem je vytvoření identity nabídky. Nabídka identity se skládá ze tří částí: **ID nabídky**, **ID vydavatele**, a **název**. Každá z těchto částí je popsané v následujících částech.

![Vytváří se nová konzultační služby nabídka – karta nastavení nabídky](media/consultingoffer-settings-tab.png)

*ID nabídky*

Tento identifikátor je jedinečný název, který vytvoříte, když nejdřív odešlete nabídky. Musí obsahovat jenom malé alfanumerické znaky, pomlčky nebo podtržítka. **ID nabídky** se nebude zobrazovat v adrese URL a dopady modul výsledky hledání. Například *yourcompanyname_exampleservice*

Jak je znázorněno v příkladu **ID nabídky** získá připojí k vašemu ID vydavatele k vytvoření jedinečného identifikátoru. To je vystavena jako trvalý odkaz, který může být zapsán a je indexované podle vyhledávací weby.

*Po nabídky je v provozu, nejde aktualizovat její identifikátor*

*ID vydavatele*

Tento identifikátor se vztahuje k vašemu účtu. Pokud jste se přihlásili pomocí účtu organizace vaše **vydavatele** ID se zobrazí v rozevírací nabídce.

*Název*

Tento řetězec představuje, co se zobrazí jako název nabídky na AppSource nebo Azure Marketplace. *Název* pole je omezen na 50 znaků.  Kontrolor možná muset upravit svým názvem umožňující připojení název nabídky s dobou trvání a typ nabídky.

Následující příklad pomocí Edgewater Fullscope ukazuje, jak je sestavena název nabídky. Název nabídky se zobrazí jako:

![Vytváří se nová nabídka konzultační služby](media/cppsampleconsultingoffer.png)

Název nabídky se skládá ze čtyř částí:

-   **Doba trvání:** – definované v **podrobnosti z prodejních míst** karta editoru. Doba trvání může být vyjádřena v hodinách, dnech nebo týdnech.
-   **Typ služby:** – definované v **podrobnosti z prodejních míst** karta editoru. Typy služeb jsou `Assessment`, `Briefing`, `Implementation`, `Proof of concept`, a `Workshop`.
-   **Mezerami:** – recenzentem vložené:
-   **Název:** – definován **nabízejí nastavení** stránky.

>[!Note]
>Pole názvu je omezená na 50 znaků. Název, který odešlete může být potřeba upravovat kontrolorovi povolit po dobu trvání a typ připojeným k názvu nabídky.

Následující seznam obsahuje několik názvů dobře pojmenované nabídky:

-   Základy pro profesionální služby: Přehledové 1 hodina
-   Cloudová platforma migrace: Přehledové 1 hodina
-   PowerApps a Microsoft Flow: Seminář 1 den
-   Azure Machine Learning Services: PoC 3 týdny
-   Cihla a klikněte na maloobchodní řešení: Přehledové 1 hodina
-   Přineste si vlastní Data: Seminář o 1 týden
-   Cloudové analýzy: Seminář o 3 dny
-   Školení v Power BI: Seminář o 3 dny
-   Řešení pro správu prodejní: Implementace 1 týden
-   Rychlý start CRM: Seminář 1 den
-   Dynamics 365 for Sales: Posouzení 2 dny

Po dokončení **nabízejí nastavení** kartu, můžete uložit své podání. Název nabídky se teď budou zobrazovat nad editoru, a najdete ho zpátky do všech nabízí.

**Další kroky**

Nyní můžete zadat [z prodejních míst podrobností a zjistit, jestli se má publikovat na webu Azure Marketplace nebo AppSource](./cpp-consulting-service-storefront-details.md).

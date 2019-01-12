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
ms.openlocfilehash: 590aa440f35f97e854fa827b0d8db1c3f9211faf
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54231933"
---
# <a name="offer-settings-tab"></a>Karta nastavení nabídky

Na **nová nabídka** obrazovky, prvním krokem je vytvoření identity nabídky. Nabídka identity se skládá ze tří částí: **ID nabídky**, **ID vydavatele**, a **název**. Každá z těchto částí je popsané v následujících částech.

![Vytvořit novou nabídku konzultační služby – nabízí karta nastavení](media/consultingoffer-settings-tab.png)

### <a name="offer-id"></a>ID nabídky

Tento identifikátor je jedinečný název, který vytvoříte, když nejdřív odešlete nabídky. Musí obsahovat jenom malé alfanumerické znaky, pomlčky nebo podtržítka. **ID nabídky** je zobrazen v adrese URL a ovlivňuje výsledků vyhledávacích webů. Příkladem je *yourcompanyname_exampleservice*.

Jak je znázorněno v příkladu **ID nabídky** se připojí k vašemu ID vydavatele k vytvoření jedinečného identifikátoru. Tento jedinečný identifikátor vystavena jako trvalý odkaz, který může být zapsán a je indexované podle vyhledávací weby.

>[!Note]
>Po za provozu v rámci nabídky identifikátoru se nedá aktualizovat.

### <a name="publisher-id"></a>ID vydavatele

Tento identifikátor se vztahuje k vašemu účtu. Po přihlášení pomocí účtu organizace vaše **ID vydavatele** se zobrazí v rozevírací nabídce.

### <a name="name"></a>Název

Tento řetězec se zobrazí jako název nabídky na AppSource nebo na webu Azure Marketplace. **Název** pole je omezen na 50 znaků. Revidující může být nutné upravit dobu trvání připojení a nabízet typu na název nabídky svým názvem.

Následující příklad ukazuje, jak je sestavena název nabídky. 

![Vytvořit novou nabídku konzultační služby](media/cppsampleconsultingoffer.png)

Název nabídky se skládá ze čtyř částí:

-   **Doba trvání:** Založený na **podrobnosti z prodejních míst** karta editoru. Doba trvání může být vyjádřena v hodinách, dnech nebo týdnech.
-   **Typ služby:** Založený na **podrobnosti z prodejních míst** karta editoru. Typy služeb jsou `Assessment`, `Briefing`, `Implementation`, `Proof of concept`, a `Workshop`.
-   **Mezerami:** Vložit kontrolor.
-   **Jméno:** Založený na **nabízejí nastavení** stránky.

>[!Note]
>**Název** pole je omezen na 50 znaků. Revidující může být nutné upravit dobu trvání připojení a nabízet typu na název nabídky svým názvem.

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

Po vyplnění **nabízejí nastavení** kartu, uložte své podání. Název nabídky se zobrazí nad editoru, a najdete ho v **všechny nabízí**.

## <a name="next-steps"></a>Další postup

Nyní můžete zadat [z prodejních míst podrobností a zjistit, jestli se má publikovat na webu Azure Marketplace nebo AppSource](./cpp-consulting-service-storefront-details.md).

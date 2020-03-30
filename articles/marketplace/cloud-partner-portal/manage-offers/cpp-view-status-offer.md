---
title: Zobrazit stav nabídek tržiště | Azure Marketplace
description: Zobrazení stavu nabídek na tržištích Azure a AppSource marketplaces pomocí portálu partnerů cloudu
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: dsindona
ms.openlocfilehash: 0cbe6a45ba205f32a764bdadb021dc0dcf5bf0cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275964"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>Zobrazení stavu publikování nabídek Azure Marketplace a AppSource

Po vytvoření nabídky, a zejména během procesu publikování, můžete zobrazit stav nabídky v portálu partnerů cloudu.  Celkový stav publikování je k dispozici na stránkách [**Všechny nabídky**](../portal-tour/cpp-all-offers-page.md) a [**schválení**](../portal-tour/cpp-approvals-page.md) na portálu.  Pro každou nabídku by měl být zobrazen jeden z následujících indikátorů stavu.  

|            Status              |   Popis                                                           |
|            ------              |   -----------                                                           |
| **-**                          | Nabídka byla vytvořena, ale proces publikování nebyl zahájen.            |
| **Probíhá publikování**        | Nabídka se propracovává kroky procesu publikování.   |
| **Publikování se nezdařilo.**             | Kritický problém byl zjištěn během ověřování nebo revize společností Microsoft. |
| **Publikování bylo zrušeno.**           | Vydavatel zrušil proces publikování nabídky.  Tento stav nevyřadí existující nabídku na trhu. | 
| **Čekání na odhlášení vydavatele** | Nabídka byla přezkoumána společností Microsoft a nyní čeká na konečné ověření vydavatelem. |
| **Vyřazeno ze seznamu**                   | Dříve publikovaná nabídka na trhu byla odebrána.      | 
|  |  |


## <a name="publishing-status-details"></a>Podrobnosti o stavu publikování 

Další podrobnosti o stavu nabídky při průchodu procesem publikování najdete na kartě **Stav** na stránce **Nová nabídka.**  Na této stránce jsou uvedeny všechny kroky publikování pro daný typ nabídky.  *Všimněte si, že počet a konkrétní kroky se často liší mezi typy nabídek.*  Tato stránka také označuje všechny nevyřešené problémy vyvolané kroky ověření a kontroly společnosti Microsoft, které často vyžadují akci vydavatele před pokračováním procesu publikování.  Na následujícím obrázku je například karta **Stav** pro novou nabídku virtuálního počítače. 

![Karta Stav pro nabídku virtuálního mísy](./media/vm-offer-pub-steps1.png)

Další ukázková karta **Stav** pro poradenskou službu zobrazující hlášenou chybu v nastavení správy zájemců.  Vzhledem k tomu, že pro poradenské služby je vyžadována správa zájemců, musí být tato chyba opravena, aby publikování mohlo pokračovat.

![Karta Stav pro konzultační službu zobrazující chybu](./media/consulting-service-error.png)

Konečný příklad stavu aplikace Azure ukazuje kritický problém s kontrolou Microsoftu.  Obsahuje aktivní odkaz na položku Azure DevOps, která obsahuje podrobné informace o tomto problému s recenzemi.  Další informace najdete [v tématu Publikování nabídky aplikací Azure](cpp-publish-offer.md).

![Karta Stav aplikace Azure zobrazující problém s revizí](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>Další kroky

Chcete-li opravit nevyřešené problémy nebo aktualizovat nastavení nabídky, je nutné [aktualizovat nabídku](./cpp-update-offer.md). 

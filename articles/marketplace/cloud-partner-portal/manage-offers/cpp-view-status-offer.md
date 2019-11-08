---
title: Zobrazení stavu nabídek Marketplace | Azure Marketplace
description: Zobrazení stavu nabídek Azure a AppSource Marketplace pomocí portál partnerů cloudu
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pabutler
ms.openlocfilehash: e58670d37e01bb6e453b73e42a87e42e890d10d8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826708"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>Zobrazení stavu publikování Azure Marketplace a nabídek AppSource

Po vytvoření nabídky a zejména během procesu publikování můžete zobrazit stav vaší nabídky v portál partnerů cloudu.  Celkový stav publikování je k dispozici na stránkách [**všechny nabídky**](../portal-tour/cpp-all-offers-page.md) a [**schválení**](../portal-tour/cpp-approvals-page.md) na portálu.  U každé nabídky by se mělo zobrazit jeden z následujících indikátorů stavu.  

|            Status              |   Popis                                                           |
|            ------              |   -----------                                                           |
| **-**                          | Nabídka se vytvořila, ale proces publikování se nezačal.            |
| **Probíhá publikování.**        | Nabídka pracuje způsobem, který prochází postupem procesu publikování.   |
| **Publikování se nezdařilo**             | Při ověřování nebo kontrole od Microsoftu se zjistil kritický problém. |
| **Publikování zrušeno**           | Vydavatel zrušil proces publikování nabídky.  Tento stav neprovádí devýpis stávající nabídky na webu Marketplace. | 
| **Čeká se na odhlášení vydavatele** | Nabídka byla přezkoumána společností Microsoft a nyní očekává konečné ověření vydavatelem. |
| **Odpsáno**                   | Odebrali jsme dřív publikovanou nabídku na webu Marketplace.      | 
|  |  |


## <a name="publishing-status-details"></a>Podrobnosti o stavu publikování 

Další podrobnosti o stavu nabídky, jak se procházejí prostřednictvím procesu publikování, najdete na kartě **stav** na **nové stránce nabídky** .  Tato stránka obsahuje seznam všech kroků publikování pro daný typ nabídky.  *Všimněte si, že počet a konkrétní kroky se často liší mezi typy nabídky.*  Tato stránka také indikuje všechny nedokončené problémy, které jsou vyvolány při ověření a přezkoumání kroků od společnosti Microsoft, což často vyžaduje akci vydavatele, než může proces publikování pokračovat.  Například na následujícím obrázku vidíte kartu **stav** pro novou nabídku virtuálních počítačů. 

![Karta stav pro nabídku VM](./media/vm-offer-pub-steps1.png)

Další příklad karty **stav** pro konzultační službu, která zobrazuje oznámenou chybu v nastavení správy potenciálních zákazníků.  Vzhledem k tomu, že pro konzultační služby je nutná Správa zájemců, je před pokračováním v publikování nutné tuto chybu opravit.

![Karta stav pro konzultační službu ukazující chybu](./media/consulting-service-error.png)

Konečný ukázkový stav aplikace Azure zobrazuje kritický problém Microsoft recenze.  Obsahuje aktivní odkaz na položku Azure DevOps, která obsahuje podrobné informace o tomto problému s revizí.  Další informace najdete v tématu [publikování nabídky aplikací Azure](cpp-publish-offer.md).

![Karta stav pro aplikaci Azure, která zobrazuje problém s revizí](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>Další kroky

Chcete-li opravit nedokončené problémy nebo aktualizovat nastavení nabídek, je nutné [aktualizovat nabídku](./cpp-update-offer.md). 

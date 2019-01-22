---
title: Zobrazení stavu nabídky Marketplace – Azure Marketplace | Dokumentace Microsoftu
description: Zobrazení stavu nabídky na Azure a AppSource tržišť s řešeními pomocí portál Cloud Partner
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pbutlerm
ms.openlocfilehash: bdec2d699e8448c8e2303dfbabcb4d176a9ca389
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54430377"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>Zobrazit stav publikování nabídek na webu Azure Marketplace a AppSource

Po vytvoření nabídky a zejména během procesu publikování můžete zobrazit stav vaší nabídky na portál Cloud Partner.  Celkový stav publikování je k dispozici v [ **všechny nabízí** ](../portal-tour/cpp-all-offers-page.md) a [ **schválení** ](../portal-tour/cpp-approvals-page.md) stránky portálu.  Pro každou nabídku musí zobrazí jedna z následujících indikátory stavu.  

|            Status              |   Popis                                                           |
|            ------              |   -----------                                                           |
| **-**                          | Nabídka se vytvořila, ale proces publikování nebyla zahájena.            |
| **Probíhá publikování**        | Nabídka pracuje cestě provede kroky procesu publikování.   |
| **Publikování selhalo**             | Kritický problém byl zjištěn během ověřování nebo kontrola společností Microsoft. |
| **Publikování přerušeno**           | Vydavatel zrušil proces publikování nabídky.  Tento stav není výmazu zápisu stávající nabídky na webu Marketplace. | 
| **Čeká se na vydavatele Odhlásit se.** | Nabídka byl recenzován uživatelem Microsoftu a teď čeká na finální ověření vydavatel. |
| **Delisted**                   | Byl odebrán dříve publikované nabídky na webu Marketplace.      | 
|  |  |


## <a name="publishing-status-details"></a>Publikování podrobnosti o stavu 

Další podrobnosti o stavu v rámci nabídky, protože probíhá proces publikování se nachází v **stav** karty **nová nabídka** stránky.  Tato stránka obsahuje všechny kroky pro publikování pro tento typ nabídky.  *Všimněte si, že počet a konkrétní kroky často liší mezi typy nabídek.*  Tato stránka indikuje také všechny zbývající problémy vyvolané službou ověřování společnosti Microsoft a kontrola kroků, které často vyžadují nějakou akci vydavatel předtím, než se proces publikování můžete pokračovat.  Například na následujícím obrázku **stav** kartu pro nové nabídky virtuálních počítačů. 

![Stav karty pro nabídky virtuálních počítačů](./media/vm-offer-pub-steps1.png)

Následující příklad **stav** kartu konzultační služby, zobrazující oznámenou chybu v nastavení správy potenciálních zákazníků.  Protože je vyžadován pro konzultační služby Správa potenciálních zákazníků, musí tato chyba opravena, můžete pokračovat v publikování.

![Stav kartu konzultační služby ukazuje chybu](./media/consulting-service-error.png)

Zobrazí se stav poslední příklad aplikací Azure kritický problém revize Microsoft.  Obsahuje aktivní propojení s položkou VSTS, který obsahuje podrobné informace o tomto problému zkontrolujte.  Další informace najdete v tématu [nabídka aplikaci Azure publikovat](cpp-publish-offer.md).

![Kartu stav pro aplikaci Azure znázorňující kontrolu problému](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>Další postup

Chcete-li opravit nevyřízené problémy nebo aktualizovat nastavení nabídky, musíte [v rámci nabídky aktualizace](./cpp-update-offer.md). 

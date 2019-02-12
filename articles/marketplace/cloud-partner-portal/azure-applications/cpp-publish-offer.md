---
title: Publikovat nabídku aplikací Azure – Azure Marketplace | Dokumentace Microsoftu
description: Popisuje kroky pro uvedení nabídky Azure aplikace na Azure Marketplace a proces.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: pbutlerm
ms.openlocfilehash: cafda8a48f9160c80edb02c3452035f912958bc7
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56098217"
---
# <a name="publish-azure-application-offer"></a>Publikovat nabídku aplikací Azure

Po vytvoření nabídky tím, že poskytuje informace o **nová nabídka** stránky, můžete publikovat tuto nabídku. Vyberte **publikovat** zahájíte proces publikování.

Následující diagram znázorňuje hlavní kroky v procesu publikování pro nabídku "publikování".

![Postup publikování nabídky](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>Podrobný popis kroků publikování

Následující tabulka uvádí a popisuje jednotlivé kroky pro publikování a poskytuje časový odhad jednotlivých kroků.  Časy odhady v "days" jsou definované jako pracovních dní, které vylučují bez víkendů a svátků.

|  **Publikování kroku**           | **čas**    | **Popis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Ověřit požadované součásti         | < 15 min    | Poskytuje informace a nabídku, nastavení se ověří.                        |
| Ověřte nastavení vliv na výnosy | < 15 min  | Je zaškrtnuté políčko attribution využití prostředků Azure pro tuto nabídku.             |
| Certifikace                  | < 1 den     | Nabídka se analyzuje tým certifikace Azure. Nabídka je vyhledány viry, malware, dodržování bezpečnostních předpisů a problémy se zabezpečením. Nabídka je zaškrtnuté políčko, pokud chcete zobrazit, zda splňuje všechna kritéria způsobilosti. Další informace najdete v tématu [požadavky](./cpp-prerequisites.md). Pokud je nalezen problém poskytnutí zpětné vazby. |
| Ověření testů jednotky          | < 2 hodiny   | (Volitelné) Pokud Test Drive je k dispozici, Microsoft ověřuje, že je nasadit a replikovat.  |
| Balení a registrace generování potenciálních zákazníků | < 1 hodina  | Technické prostředky nabídky jsou zabaleny pro používání zákazníka a systémy potenciálních zákazníků jsou konfigurovány a nasazeny. |
|  Odhlášení vydavatele             |  Ruční    | Zkontrolujte poslední vydavatele a potvrzení před uvedete nabídky. Nabídka je teď dostupná ve verzi preview.  Ověřit, zda splňuje všechny požadavky, můžete nasadit vaši nabídku ve vybraných předplatných (v krocích informace nabídky).  Po ověření tuto nabídku, vyberte **aktivace** tak vaši nabídku můžete přesunout k dalšímu kroku. |
| Microsoft review                | 7 – 14 dní | Microsoft holistické zkontroluje vaše aplikace Azure a odešle e-mail, pokud jsou zjištěny problémy.  Tento krok závisí na složitosti aplikace, problémů zjištěných a jak rychle reagovat na ně.  |
| Živé                           | < 1 den | Nabídka je uvolněna, replikovaná do oblastí, zadaný a veřejně k dispozici. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

Můžete monitorovat v procesu publikování **stav** kartu pro vaši nabídku v portál partnerů cloudu.

![Stav karty pro nabídky aplikace Azure](./media/offer-status-tab.png)

Po dokončení procesu publikování vaší nabídky nebude uvedené [kategorie aplikace Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/).


## <a name="errors-and-review-feedback"></a>Chyby a zpětné vazby z revize

Kromě zobrazení Stav publikování vaší nabídky **stav** kartu také zobrazí chybové zprávy a zpětnou vazbu od všech publikování kroků, kdy došlo k problému.  Pokud tento problém je velmi důležité, pak tuto aplikaci publikovat se zruší.  Pak musíte opravit nahlášené problémy a opětovné publikování nabídky.  Protože **Microsoft revize** krok představuje rozsáhlý přehled vaší nabídky a její přidružené technické prostředky (hlavně šablony Azure Resource Manageru), problémy jsou obvykle uvedeny jako o přijetí změn odkazy žádostí o přijetí změn.  Vysvětlení, jak zobrazit a reagovat na tyto žádosti o přijetí změn, naleznete v tématu [zpětné vazby z revize zpracování](./cpp-handling-review-feedback.md).


## <a name="next-steps"></a>Další postup

Pokud jste narazili na chyby v jedné nebo více kroků, publikování, musíte je opravit a znovu publikovat vaši nabídku.  Pokud nedojde k kritické problémy v **Microsoft revize** kroku, je nutné [zpracování zpětné vazby z revize](./cpp-handling-review-feedback.md) díky přístupu do Microsoft zkontrolujte úložiště týmu Azure DevOps.

Po úspěšném publikování aplikaci Azure můžete [aktualizace stávající nabídky](./cpp-update-existing-offer.md) tak, aby odrážely změny obchodní nebo technické požadavky. 

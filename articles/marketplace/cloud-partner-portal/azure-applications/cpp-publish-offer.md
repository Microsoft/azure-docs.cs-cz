---
title: Publikování nabídky aplikací Azure | Azure Marketplace
description: Popisuje proces a kroky pro publikování nabídky aplikací Azure na Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: dsindona
ms.openlocfilehash: 9f89e31c2d17ef74971d2057ba58e9572e92184c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280095"
---
# <a name="publish-azure-application-offer"></a>Publikování nabídky aplikací Azure

Po vytvoření nabídky poskytnutím informací na stránce **Nová nabídka** můžete nabídku publikovat. Chcete-li zahájit proces publikování, vyberte **publikovat.**

Následující diagram znázorňuje hlavní kroky v procesu publikování pro nabídku "přejít do provozu".

![Kroky publikování nabídky](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>Podrobný popis kroků publikování

V následující tabulce je uveden a popsán každý krok publikování a poskytuje odhad času k dokončení každého kroku.  Odhady časů ve dnech jsou definovány jako pracovní dny, které vylučují víkendy a svátky.

|  **Krok publikování**           | **Čas**    | **Popis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Ověřit požadavky         | < 15 min    | Informace o nabídce a nastavení nabídky jsou ověřeny.                        |
| Ověřit ovlivněné nastavení výnosů | < 15 min  | Je kontrolováno přiřazení využití prostředků Azure pro nabídku.             |
| Certifikace                  | < 1 den     | Nabídku analyzuje certifikační tým Azure. V nabídce se zkontrolují viry, malware, dodržování bezpečnostních předpisů a problémy se zabezpečením. Nabídka se zkontroluje, aby se zjistilo, jestli splňuje všechna kritéria způsobilosti. Další informace naleznete [v tématu požadavky](./cpp-prerequisites.md). Pokud je nalezen problém, je k dispozici zpětná vazba. |
| Ověření testovací jednotky          | < 2 hodiny   | (Nepovinné) Pokud je k dispozici testovací jednotka, společnost Microsoft ověří, že ji lze nasadit a replikovat.  |
| Registrace balení a generování olova | < 1 hodina  | Technické prostředky nabídky jsou baleny pro použití zákazníkem a systémy proutí jsou konfigurovány a nasazeny. |
|  Odhlášení vydavatele             |  Ruční    | Konečná recenze a potvrzení vydavatele před tím, než nabídka začne být v provozu. Nabídka je nyní k dispozici pro náhled.  Nabídku můžete nasadit ve vybraných předplatných (v krocích informací o nabídce) a ověřit, zda splňuje všechny vaše požadavky.  Po ověření nabídky vyberte **Přejít na živo,** aby se vaše nabídka mohla přesunout k dalšímu kroku. |
| Microsoft recenze                | 7 - 14 dní | Microsoft holisticky zkontroluje vaši aplikaci Azure a e-maily, pokud se objeví problémy.  Délka tohoto kroku závisí na složitosti aplikace, odkryté problémy a jak rychle na ně reagovat.  |
| Živé                           | < 1 den | Nabídka je uvolněna, replikována do zadaných oblastí a zpřístupněna veřejnosti. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

Proces publikování můžete sledovat na kartě **Stav** pro vaši nabídku na portálu cloudových partnerů.

![Karta Stav pro nabídku aplikace Azure](./media/offer-status-tab.png)

Po dokončení procesu publikování bude vaše nabídka uvedena v [kategorii aplikací Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/).

>[!Note]
>Přihlášení partnerského kanálu zprostředkovatelů cloudových řešení (CSP) je nyní k dispozici.  Další informace o marketingu vaší nabídky prostřednictvím partnerských kanálů Microsoft CSP najdete v tématu [Poskytovatelé cloudových řešení.](../../cloud-solution-providers.md)

## <a name="errors-and-review-feedback"></a>Chyby a zpětná vazba

Kromě zobrazení stavu publikování nabídky se na kartě **Stav** zobrazují také chybové zprávy a zpětná vazba z kroků publikování, kde došlo k problému.  Pokud je problém kritický, publikování je zrušeno.  Poté je nutné opravit nahlášené problémy a nabídku znovu publikovat.  Vzhledem k tomu, že krok **kontroly Microsoft** představuje rozsáhlou kontrolu vaší nabídky a související technické prostředky (zejména šablona Azure Resource Manager), problémy jsou obvykle prezentovány jako žádosti o přijetí vyhovení (PR) odkazy.  Vysvětlení, jak zobrazit a reagovat na tyto PR, naleznete [v tématu Zpracování zpětné vazby od recenze](./cpp-handling-review-feedback.md).


## <a name="next-steps"></a>Další kroky

Pokud jste narazili na chyby v jednom nebo více krocích publikování, je nutné je opravit a znovu publikovat nabídku.  Pokud se v kroku **kontroly Microsoftu** vyskytnou kritické problémy, musíte [zpracovat zpětnou vazbu kontroly](./cpp-handling-review-feedback.md) přístupem k úložišti Azure DevOps týmu Microsoft.

Po úspěšném publikování aplikace Azure můžete [aktualizovat stávající nabídku](./cpp-update-existing-offer.md) tak, aby odrážela měnící se obchodní nebo technické požadavky. 

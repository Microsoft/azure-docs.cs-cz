---
title: Publikování nabídky aplikací Azure | Azure Marketplace
description: Popisuje postup a kroky pro publikování nabídky aplikací Azure na Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: pabutler
ms.openlocfilehash: 0005760a16f5109ca3555df5c5c5137facc84c40
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826167"
---
# <a name="publish-azure-application-offer"></a>Publikování nabídky aplikací Azure

Po vytvoření nabídky poskytnutím informací na **nové stránce nabídky** můžete tuto nabídku publikovat. Vyberte **publikovat** a spusťte proces publikování.

Následující diagram znázorňuje hlavní kroky v procesu publikování pro nabídku "jít živě".

![Nabídka kroků publikování](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>Podrobný popis kroků publikování

Následující tabulka uvádí a popisuje jednotlivé kroky publikování a poskytuje časový odhad pro dokončení každého kroku.  Časové odhady v "dnech" se definují jako pracovní dny, což vylučuje víkendy a svátky.

|  **Krok publikování**           | **Interval**    | **Popis**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Ověřit požadavky         | < 15 min.    | Budou ověřeny informace nabídky a nastavení nabídky.                        |
| Ověřit nastavení ovlivněných výnosů | < 15 min.  | Pro tuto nabídku se vyhradí přidělení využití prostředků Azure.             |
| Certifikace                  | < 1 den     | Nabídka se analyzuje prostřednictvím certifikačního týmu Azure. V nabídce se hledají viry, malware, dodržování bezpečnostních předpisů a problémy se zabezpečením. V rámci této nabídky se kontroluje, že splňuje všechna kritéria způsobilosti. Další informace najdete v tématu [předpoklady](./cpp-prerequisites.md). Pokud je nalezen problém, je poskytnuta zpětná vazba. |
| Ověření testovací jednotky          | < 2 hodiny   | Volitelné Pokud je k dispozici testovací jednotka, společnost Microsoft ověří, zda je možné ji nasadit a replikovat.  |
| Registrace pro vytváření balíčků a vedoucích | < 1 hodina  | Technické prostředky nabídky jsou zabaleny pro zákazníky a systémy, které jsou konfigurovány a nasazeny. |
|  Odhlášení vydavatele             |  Zásah    | Poslední kontrola a potvrzení vydavatele před tím, než bude nabídka živá. Nabídka je nyní k dispozici pro verzi Preview.  Nabídku můžete nasadit ve vybraných předplatných (v informacích o nabídce) a ověřit, že splňuje všechny vaše požadavky.  Po ověření nabídky vyberte možnost přejít na **aktivní** , aby vaše nabídka mohla přejít k dalšímu kroku. |
| Kontrola společnosti Microsoft                | 7-14 dní | Microsoft komplexní zkontroluje vaši aplikaci Azure a pošle e-maily, pokud jsou problémy zjištěné.  Délka tohoto kroku závisí na složitosti aplikace, zjištěných problémech a na tom, jak s nimi rychle reagovat.  |
| Živé                           | < 1 den | Nabídka je uvolněna, replikována do zadaných oblastí a zpřístupněna veřejnosti. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

Proces publikování můžete monitorovat na kartě **stav** pro vaši nabídku v portál partnerů cloudu.

![Karta stav pro nabídku aplikace Azure](./media/offer-status-tab.png)

Po dokončení procesu publikování bude nabídka uvedená v [kategorii aplikace Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/).

>[!Note]
>Výslovný souhlas kanálu pro partnery Cloud Solution Providers (CSP) je teď k dispozici.  Další informace o marketingu vaší nabídky prostřednictvím kanálů partnerů Microsoft CSP najdete v tématu [Poskytovatelé cloudových řešení](../../cloud-solution-providers.md) .

## <a name="errors-and-review-feedback"></a>Chyby a kontrola zpětné vazby

Kromě zobrazení stavu publikování vaší nabídky zobrazuje karta **stav** také chybové zprávy a zpětnou vazbu ze všech kroků publikování, u kterých došlo k problému.  Pokud je problém kritický, publikování se zruší.  Pak je nutné opravit nahlášené problémy a znovu publikovat nabídku.  Vzhledem k tomu, že krok **kontroly společnosti Microsoft** představuje rozsáhlou kontrolu vaší nabídky a přidružených technických prostředků (zejména šablony Azure Resource Manager), jsou problémy obvykle prezentovány jako odkazy žádosti o přijetí změn (PR).  Vysvětlení, jak zobrazit tyto PR a reagovat na ně, najdete v tématu [zpracování zpětné vazby na revizi](./cpp-handling-review-feedback.md).


## <a name="next-steps"></a>Další kroky

Pokud jste narazili na chyby v jednom nebo několika krocích publikování, je nutné je opravit a znovu publikovat vaši nabídku.  Pokud se v kroku **recenze Microsoftu** vyskytují kritické problémy, je nutné [zpracovat zpětnou vazbu na revizi](./cpp-handling-review-feedback.md) přístupem k úložišti Azure DevOps týmu Microsoft přezkoumání.

Po úspěšném publikování aplikace Azure můžete [existující nabídku aktualizovat](./cpp-update-existing-offer.md) tak, aby odrážela měnící se obchodní nebo technické požadavky. 

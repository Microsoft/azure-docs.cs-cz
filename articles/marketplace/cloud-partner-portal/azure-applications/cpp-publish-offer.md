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
ms.openlocfilehash: 2adf07cf2337611b9136af47ce6a35b617e2e9ff
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55177028"
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

Kromě zobrazení Stav publikování vaší nabídky **stav** kartu také zobrazí chybové zprávy a zpětnou vazbu od **Microsoft revize** kroku.  Kontrola problémů obvykle jsou odkazovány jako žádosti o přijetí změn (žádost o přijetí změn).  Každá žádost o přijetí změn je propojen online Visual Studio Team Services (VSTS, přejmenován na [Azure DevOps](https://azure.microsoft.com/services/devops/)) položky, který obsahuje podrobnosti o problému.  Následující obrázek ukazuje příklad odkazu na revizi žádosti o přijetí změn.  Pro složitější situace revize a podporu týmy mohou e-mailem. 

![Stav karty zobrazení zpětné vazby z revize](./media/status-tab-ms-review.png)

Předtím, než nabídka pokračuje přes proces publikování, je třeba vyřešit všechny oznámené problémy.  Následující diagram znázorňuje, jak tento proces zpětná vazba se vztahuje k procesu publikování.

![Publikování kroky VSTS odeslání zpětné vazby](./media/pub-flow-vsts-access.png)


### <a name="vsts-access"></a>Přístup k VSTS

Chcete-li zobrazit tyto položky VSTS v zpětné vazby z revize, musí být vydavatelé udělena příslušná oprávnění.  Jinak se zobrazí nové vydavatelé `401 - Not Authorized` stránky odpovědi.  Požádat o přístup k systému nabídka revize VSTS, proveďte následující kroky:

1. Shromážděte následující informace:
    - Název vydavatele a ID
    - Nabízí typ (aplikace Azure), název a ID skladové položky
    - Žádost o přijetí změn propojit, například: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`  Tuto adresu URL můžete získat z zprávy oznámení nebo adresu skončí odpovědí 401 stránky.
    - E-mailové adresy osob, od publikování organizace, které chcete udělit přístup.  Ty by měly zahrnovat vlastníka adresy, které jste zadali při registraci jako vydavatel na portál partnerů cloudu.
2. Vytvořte incident podpory.  V záhlaví na portál Cloud Partner, vyberte **pomáhají** tlačítko a pak zvolte **podporu** z nabídky.  Výchozí webové prohlížeče by měl spustit a přejděte na stránku Microsoft nové podpory incidentů.  (Může mít pro první přihlášení.)
3. Zadejte **typ problému** jako **marketplace připojování** a **kategorie** jako **potíže při přístupu k**a pak vyberte **Start žádost o**.

    ![Kategorie lístku podpory](./media/support-incident1.png)

4. V **krok 1 z 2** stránky, zadejte svoje kontaktní informace a vyberte **pokračovat**.
5. V **krok 2 ze 2** stránky, zadejte název incidentu (například `Request VSTS access`) a zadejte informace shromážděné v prvním kroku (viz výše).  Přečtěte si a přijměte smlouvu a pak vyberte **odeslat**.

Pokud vytvářet incidenty byl úspěšný, zobrazí se stránka s potvrzením.  Uložte informace o potvrzení pro vaši informaci.  Podpora Microsoftu by měl odpovídat vaše žádost o přístup do několika pracovních dnů.


## <a name="next-steps"></a>Další postup

Po publikování aplikaci Azure můžete [aktualizace stávající nabídky](./cpp-update-existing-offer.md) tak, aby odrážely změny obchodní nebo technické požadavky. 

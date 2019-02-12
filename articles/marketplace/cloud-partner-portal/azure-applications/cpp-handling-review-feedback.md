---
title: Zpracování zpětné vazby z revize aplikací Azure – Azure Marketplace | Dokumentace Microsoftu
description: Vysvětluje, jak používat Azure DevOps pro zpracování zpětné vazby z revize pro nabídky Azure aplikace pro Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
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
ms.date: 02/05/2019
ms.author: pbutlerm
ms.openlocfilehash: 0d3102a269d41fded112b0704c28ca2cf5df71a6
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56101116"
---
# <a name="handling-review-feedback"></a>Zpracování zpětné vazby z revize

Tento článek vysvětluje, jak získat přístup k prostředí Azure DevOps používají tým Microsoft Azure Marketplace kontroly.  Pokud se nenajdou důležité problémy ve vaší nabídce aplikace Azure během **revize Microsoft** kroku, se můžete přihlásit do systému a zobrazit podrobné informace o těchto problémech (přečtěte si zpětnou vazbu).  Po opravě těchto problémů musí znovu nabídkou, chcete-li pokračovat v publikování na webu Azure Marketplace.  Následující diagram znázorňuje, jak tento proces zpětná vazba se vztahuje k procesu publikování.

![Publikování kroky VSTS odeslání zpětné vazby](./media/pub-flow-vsts-access.png)

Kontrola problémů obvykle jsou odkazovány jako žádosti o přijetí změn (žádost o přijetí změn).  Každá žádost o přijetí změn je propojený s online [Azure DevOps](https://azure.microsoft.com/services/devops/) (dřív měla název Visual Studio Team Services (VSTS)) položky, který obsahuje podrobnosti o problému.  Následující obrázek ukazuje příklad odkazu na revizi žádosti o přijetí změn.  Pro složitých situacích revize a podporu týmy mohou také vám e-mail. 

![Stav karty zobrazení zpětné vazby z revize](./media/status-tab-ms-review.png)


## <a name="vsts-access"></a>Přístup k VSTS

Chcete-li zobrazit žádost o přijetí změn položky odkazované v zpětné vazby z revize, vydavatel musí být nejprve udělena příslušná oprávnění.  Jinak se zobrazí nové vydavatelé `401 - Not Authorized` stránky odpovědi při pokusu o zobrazení žádosti o přijetí změn.  Chcete-li požádat o přístup k tomuto úložišti Azure DevOps, postupujte následovně:

1. Shromážděte následující informace:
    - Název vydavatele a ID
    - Nabízí typ (aplikace Azure), název a ID skladové položky
    - Žádost o přijetí změn propojit, například: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`  Tuto adresu URL můžete získat z zprávy oznámení nebo adresu skončí odpovědí 401 stránky.
    - E-mailové adresy osob, od publikování organizace, které chcete udělit přístup.  Tento seznam by měl obsahovat adresy vlastníka, který jste zadali při registraci jako vydavatel na portál partnerů cloudu.
2. Vytvořte incident podpory.  V záhlaví na portál Cloud Partner, vyberte **pomáhají** tlačítko a pak zvolte **podporu** z nabídky.  Výchozí webové prohlížeče by měl spustit a přejděte na stránku Microsoft nové podpory incidentů.  (Může mít pro první přihlášení.)
3. Zadejte **typ problému** jako **marketplace připojování** a **kategorie** jako **potíže při přístupu k**a pak vyberte **Start žádost o**.

    ![Kategorie lístku podpory](./media/support-incident1.png)

4. V **krok 1 z 2** stránky, zadejte svoje kontaktní informace a vyberte **pokračovat**.
5. V **krok 2 ze 2** stránky, zadejte název incidentu (například `Request VSTS access`) a zadejte informace shromážděné v prvním kroku (viz výše).  Přečtěte si a přijměte smlouvu a pak vyberte **odeslat**.

Pokud vytvářet incidenty byl úspěšný, zobrazí se stránka s potvrzením.  Uložte informace o potvrzení na této stránce pro vaši informaci.  Tým podpory společnosti Microsoft by měl odpovídat na vaše žádost o přístup do několika pracovních dnů.


## <a name="reviewing-the-pull-request"></a>Kontrola žádosti o přijetí změn 

Pomocí následujícího postupu zkontrolovat problémy uvedené v žádosti o přijetí změn.

1. V **Microsoft revize** část **publikování kroky** formulář a potom klikněte na odkaz žádosti o přijetí změn spusťte prohlížeč a přejděte na **přehled** (domů) stránky pro tuto žádost o přijetí změn  Následující obrázek znázorňuje příklad kritický problém Domovská stránka pro nabídku ukázková aplikace Contoso.  Tato stránka obsahuje užitečné souhrnné informace o Zkontrolujte problémy zjištěné v aplikaci Azure.  

    [ ![Domovská stránka žádosti o přijetí změn](./media/pr-home-page-thumb.png) ](./media/pr-home-page.png)
    <br/> *Kliknutím na obrázek rozbalení.*
    
2. (Volitelné) Na pravé straně okna, v části **zásady**, klikněte na zprávu problém (v tomto příkladu: **Nepovedlo se ověřit zásady**) Chcete-li prozkoumat podrobnosti nízké úrovně týkající problému, a to včetně související soubory protokolu.  Chyby se obvykle zobrazují v dolní části souboru protokolu.

3. V nabídce na levé straně domovské stránky vyberte **soubory** k zobrazení seznamu souborů, které tvoří technických prostředků pro tuto nabídku.  Microsoft revidující by přidat komentář popisující zjištěných kritické problémy.  V následujícím příkladu byly zjištěny dva problémy. 

    [ ![Domovská stránka žádosti o přijetí změn](./media/pr-files-page-thumb.png) ](./media/pr-files-page.png)
    <br/> *Kliknutím na obrázek rozbalení.*

4. Klikněte na každý uzel komentáře v levém stromu přejděte na komentář v kontextu okolním kódem.  Opravte zdrojový kód v projektu váš tým a opravte problém popsaný komentáře.

> [!Note]
> Nelze upravit vaší nabídky v rámci technických prostředků prostředí Azure DevOps tým kontroly.  Pro vydavatele to je jen pro čtení prostředí pro omezením zdrojový kód.  Však můžete nechat odpovědi na komentáře prospěch tým Microsoft kontroly.

   V následujícím příkladu má vydavatele revizi, opravit a odpověděl na prvním problémem.

   ![První odpověď opravu a komentář](./media/first-comment-reply.png)


## <a name="next-steps"></a>Další postup

Po vyřešení kritické problémy popsané v revizi PR(s) musí [nové publikování vaší nabídky aplikace Azure](./cpp-publish-offer.md).

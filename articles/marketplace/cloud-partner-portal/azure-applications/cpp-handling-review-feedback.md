---
title: Zpracování zpětné vazby pro kontrolu aplikací Azure | Azure Marketplace
description: Vysvětluje, jak používat Azure DevOps ke zpracování zpětné vazby k nabídkám aplikací Azure pro Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: pabutler
ms.openlocfilehash: ef4aff57948034fb369bd74564306b7b8674b377
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827586"
---
# <a name="handling-review-feedback"></a>Zpracování zpětné vazby

Tento článek vysvětluje, jak získat přístup k prostředí Azure DevOps, které používá tým pro kontrolu Microsoft Azure Marketplace.  V případě, že se v rámci nabídky aplikace Azure v rámci **Revize Microsoftu** nacházejí kritické problémy, můžete se přihlásit k tomuto systému a zobrazit podrobné informace o těchto problémech (zpětná vazba).  Po vyřešení všech těchto problémů je nutné znovu odeslat nabídku, abyste mohli pokračovat v jejím publikování na Azure Marketplace.  Následující obrázek znázorňuje, jak se tento proces zpětné vazby týká procesu publikování.

![Publikování kroků pomocí zpětné vazby Azure DevOps](./media/pub-flow-vsts-access.png)

Obvykle se odkazy na problémy odkazují jako žádosti o přijetí změn (PR).  Každá žádost o přijetí změn je propojená s online položkou [Azure DevOps](https://azure.microsoft.com/services/devops/) (dříve pojmenovanou Visual Studio Team Services (VSTS)), která obsahuje podrobnosti o problému.  Následující obrázek ukazuje příklad odkazu na revizi žádosti o přijetí změn.  V případě složitých situací vás můžou také e-mailem kontrolovat i týmy pro kontrolu a podporu. 

![Karta stav zobrazující názor na revizi](./media/status-tab-ms-review.png)


## <a name="azure-devops-access"></a>Přístup k Azure DevOps

Chcete-li zobrazit položky žádosti o přijetí změn, na které se odkazuje při kontrole zpětné vazby, musí být vydavateli nejprve udělena správná autorizace  Jinak noví vydavatelé obdrží stránku odpovědi `401 - Not Authorized` při pokusu o zobrazení pr.  Pokud chcete požádat o přístup k tomuto úložišti Azure DevOps, proveďte následující kroky:

1. Shromážděte následující informace:
    - Název a ID vydavatele
    - Typ nabídky (aplikace Azure), název nabídky a ID SKU
    - Odkaz na žádost o přijetí změn, například: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>` tuto adresu URL lze načíst ze zprávy oznámení nebo adresy stránky odpovědi 401.
    - E-mailové adresy jednotlivců z vaší organizace publikování, pro které chcete udělit přístup.  Tento seznam by měl obsahovat adresy vlastníka, které jste zadali při registraci vydavatele na portál partnerů cloudu.
2. Vytvořte incident podpory.  V záhlaví portál partnerů cloudu vyberte tlačítko **help** a v nabídce zvolte možnost **Podpora** .  Měli byste spustit výchozí webový prohlížeč a přejít na stránku Microsoft New support incident.  (Možná se budete muset přihlásit jako první.)
3. Zadejte **typ problému** jako **registraci do Marketplace** a **kategorii** jako problém s **přístupem**a pak vyberte **Spustit požadavek**.

    ![Kategorie lístku podpory](./media/support-incident1.png)

4. Na stránce **Krok 1 z 2** zadejte kontaktní informace a vyberte **pokračovat**.
5. V **kroku 2** na stránce zadejte název incidentu (například `Request Azure DevOps access`) a zadejte informace, které jste shromáždili v prvním kroku (výše).  Přečtěte si a přijměte smlouvu a pak vyberte **Odeslat**.

Pokud bylo vytvoření incidentu úspěšné, zobrazí se stránka s potvrzením.  Uložte informace o potvrzení na této stránce pro svůj odkaz.  Tým podpora Microsoftu by měl do několika pracovních dnů odpovědět na vaši žádost o přístup.


## <a name="reviewing-the-pull-request"></a>Kontrola žádosti o získání dat 

Pomocí následujícího postupu můžete zkontrolovat problémy popsané v žádosti o přijetí změn.

1. V části **Microsoft recenze** formuláře **kroků publikování** klikněte na odkaz PR a spusťte prohlížeč a přejděte na stránku **Přehled** (domovské) pro tuto žádost o přijetí změn.  Následující obrázek znázorňuje příklad domovské stránky kritického problému pro nabídku ukázkové aplikace contoso.  Tato stránka obsahuje užitečné souhrnné informace o problémech kontroly, které najdete v aplikaci Azure.  

    
   [domovské stránky žádosti o získání dat![](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)  <br/> *Klikněte na obrázek a rozbalte ho.*
    
2. Volitelné Na pravé straně okna v části **zásady**klikněte na zprávu problému (v tomto příkladě **se nezdařilo ověření zásad**) a prozkoumejte podrobnosti o nízké úrovni problému včetně přidružených souborů protokolu.  Chyby se obvykle zobrazují v dolní části souborů protokolu.

3. V nabídce na levé straně domovské stránky vyberte **soubory** , abyste zobrazili seznam souborů, které obsahují technické prostředky pro tuto nabídku.  Kontroloři společnosti Microsoft by měli přidat komentáře popisující zjištěné kritické problémy.  V následujícím příkladu byly zjištěny dva problémy. 

    
   [domovské stránky žádosti o získání dat![](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)  <br/> *Klikněte na obrázek a rozbalte ho.*

4. Kliknutím na každý uzel komentáře v levém stromu přejděte k komentáři v kontextu okolního kódu.  Opravte zdrojový kód v projektu týmu a opravte problém popsaný v komentáři.

> [!Note]
> Technické prostředky vaší nabídky nemůžete upravit v prostředí Azure DevOps týmu revize.  Pro vydavatele je toto prostředí jen pro čtení pro obsažený zdrojový kód.  Můžete však nechat odpovědi na komentáře a využít tak výhod týmu revize společnosti Microsoft.

   V následujícím příkladu byl Vydavatel zkontrolován, opraven a zodpovězen prvního problému.

   ![První oprava a odpověď na komentář](./media/first-comment-reply.png)


## <a name="next-steps"></a>Další kroky

Po opravě důležitých problémů popsaných v revizi žádosti o přijetí změn je nutné [znovu publikovat nabídku aplikace Azure](./cpp-publish-offer.md).

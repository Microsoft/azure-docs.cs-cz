---
title: Zpracování zpětné vazby ke kontrole aplikací Azure | Azure Marketplace
description: Vysvětluje, jak používat Azure DevOps ke zpracování zpětné vazby ke kontrole nabídek aplikací Azure pro Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: dsindona
ms.openlocfilehash: b5f290bae908ac753801eef2c9b8394ca1bb7a40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285295"
---
# <a name="handling-review-feedback"></a>Zpracování zpětné vazby

Tento článek vysvětluje, jak získat přístup k prostředí Azure DevOps používanému týmem pro kontrolu webu Microsoft Azure Marketplace.  Pokud se v nabídce aplikace Azure během kroku **kontroly Microsoftu** našly kritické problémy, můžete se přihlásit do tohoto systému a zobrazit podrobné informace o těchto problémech (zpětná vazba ke kontrole).  Po opravě všech těchto problémů je nutné znovu odeslat nabídku a pokračovat v publikování na Azure Marketplace.  Následující diagram znázorňuje, jak tento proces zpětné vazby souvisí s procesem publikování.

![Kroky publikování pomocí zpětné vazby od Azure DevOps](./media/pub-flow-vsts-access.png)

Problémy s recenzemi se obvykle zvolávají jako žádost o přijetí vzato ( PR).  Každý PR je propojen s online [Azure DevOps](https://azure.microsoft.com/services/devops/) (dříve s názvem Visual Studio Team Services (VSTS)) položky, která obsahuje podrobnosti o problému.  Následující obrázek zobrazuje příklad odkazu na pr recenzování.  V případě složitých situací vám mohou týmy pro kontrolu a podporu také poslat e-mail. 

![Karta Stav zobrazující zpětnou vazbu k revizním uzmě](./media/status-tab-ms-review.png)


## <a name="azure-devops-access"></a>Přístup k Azure DevOps

Chcete-li zobrazit položky PR odkazované ve zpětné vazbě recenze, musí být vydavatelům nejprve uděleno řádné oprávnění.  V opačném případě noví `401 - Not Authorized` vydavatelé obdrží stránku odpovědí při pokusu o zobrazení pr.  Chcete-li požádat o přístup k tomuto úložišti Azure DevOps, proveďte následující kroky:

1. Shromážděte následující údaje:
    - Jméno a ID vydavatele
    - Typ nabídky (aplikace Azure), název nabídky a ID skladové položky
    - Odkaz na žádost o `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>` přijetí akce, například: Tuto adresu URL lze načíst z oznámení nebo adresy stránky odpovědi 401.
    - E-mailové adresy jednotlivců z vaší publikační organizace, které chcete přístup udělen.  Tento seznam by měl obsahovat adresy vlastníka, které jste zadali při registraci jako vydavatel na portálu partnerů cloudu.
2. Vytvořte incident podpory.  V záhlaví portálu partnerů cloudu vyberte tlačítko **Nápověda** a v nabídce zvolte **Podpora.**  Výchozí webový prohlížeč by měl spustit a přejít na stránku nové podpory společnosti Microsoft.  (Možná se budete muset nejprve přihlásit.)
3. Zadejte **typ problem** jako marketplace **onboarding** a **category** as **Access problem**, pak vyberte **Spustit požadavek**.

    ![Kategorie lístku podpory](./media/support-incident1.png)

4. V **kroku 1 ze 2** zadejte kontaktní informace a vyberte **pokračovat**.
5. V **kroku 2 ze strany 2** zadejte název incidentu (například) `Request Azure DevOps access`a zadejte informace shromážděné v prvním kroku (výše).  Přečtěte si smlouvu a přijměte ji a pak vyberte **Odeslat**.

Pokud bylo vytvoření incidentu úspěšné, zobrazí se stránka s potvrzením.  Uložte informace o potvrzení na této stránce pro referenci.  Tým podpory společnosti Microsoft by měl odpovědět na vaši žádost o přístup během několika pracovních dnů.


## <a name="reviewing-the-pull-request"></a>Kontrola žádosti o přijetí vyhovení 

Pomocí následujícího postupu zkontrolujte problémy zdokumentované v žádosti o přijetí vyžádat.

1. V části **Microsoft recenze** **publikování kroky** formuláře, klikněte na odkaz PR spusťte prohlížeč a přejděte na **přehled** (domov) stránku pro tento PR.  Následující obrázek znázorňuje příklad domovské stránky kritického problému pro nabídku ukázkové aplikace Contoso.  Tato stránka obsahuje užitečné souhrnné informace o problémech s recenzemi, které se nacházejí v aplikaci Azure.  

    [![Domovská stránka žádosti o přijetí vyžádat](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Klikněte na obrázek rozbalit.*
    
2. (Nepovinné) Na pravé straně okna v části **Zásady**klikněte na zprávu o problému (v tomto příkladu: **Ověření zásad se nezdařilo)** a prozkoumejte podrobnosti problému na nižší úrovni, včetně přidružených souborů protokolu.  Chyby jsou obvykle zobrazeny v dolní části souborů protokolu.

3. V nabídce na levé straně domovské stránky vyberte **Soubory,** chcete-li zobrazit soubory seznamu, které obsahují technické prostředky pro tuto nabídku.  Recenzenti společnosti Microsoft měli přidat komentáře popisující zjištěné kritické problémy.  V následujícím příkladu byly zjištěny dva problémy. 

    [![Domovská stránka žádosti o přijetí vyžádat](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Klikněte na obrázek rozbalit.*

4. Kliknutím na každý uzel komentáře v levém stromu přejdete na komentář v kontextu okolního kódu.  Opravte zdrojový kód v projektu týmu a opravte problém popsaný v komentáři.

> [!Note]
> Technické prostředky nabídky nelze upravovat v prostředí Azure DevOps v týmu pro kontrolu.  Pro vydavatele se jedná o prostředí jen pro čtení pro obsažený zdrojový kód.  Odpovědi na komentáře však můžete zanechat ve prospěch týmu recenzentů společnosti Microsoft.

   V následujícím příkladu vydavatel zkontroloval, opravil a odpověděl na první vydání.

   ![První oprava a komentář odpověď](./media/first-comment-reply.png)


## <a name="next-steps"></a>Další kroky

Po opravě kritických problémů zdokumentovaných v pr(s review) je nutné [znovu publikovat nabídku aplikace Azure](./cpp-publish-offer.md).

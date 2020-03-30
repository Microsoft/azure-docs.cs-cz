---
title: Zpracování zpětné vazby od recenzí pro nabídku Azure Apps na komerčním trhu
description: Jak zpracovat zpětnou vazbu ke zpětné vazbě pro nabídku Aplikací Azure pro uvedení nebo prodej na Webu Azure Marketplace, AppSource nebo prostřednictvím programu Zprostředkovatel cloudových řešení (CSP) pomocí portálu Commercial Marketplace v Centru microsoftpartnerů.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 3932f3fc71dc6427b6cdf93d3a7bc58534a9981c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279789"
---
# <a name="handling-review-feedback"></a>Zpracování zpětné vazby

Tento článek vysvětluje, jak získat přístup k prostředí Azure DevOps používanému týmem pro kontrolu webu Microsoft Azure Marketplace. Pokud se v nabídce aplikace Azure během kroku **kontroly Microsoftu** našly kritické problémy, můžete se přihlásit do tohoto systému a zobrazit podrobné informace o těchto problémech (zpětná vazba ke kontrole). Po opravě všech těchto problémů je nutné znovu odeslat nabídku a pokračovat v publikování na Azure Marketplace. Následující diagram znázorňuje, jak tento proces zpětné vazby souvisí s procesem publikování.

![Kontrola procesu zpětné vazby](./media/review-feedback-process.png)

Problémy s recenzemi se obvykle zvolávají jako žádost o přijetí vzato ( PR). Každý PR je propojen s online [Azure DevOps](https://azure.microsoft.com/services/devops/) (dříve s názvem Visual Studio Team Services (VSTS)) položky, která obsahuje podrobnosti o problému. Následující obrázek zobrazuje příklad prostředí Partnerského centra, pokud během recenzí najdou problémy. 

![Stav publikování](./media/publishing-status.png)

Pr, který obsahuje konkrétní podrobnosti o podání budou uvedeny v "Zobrazit certifikační zprávy" odkaz. V případě složitých situací vám mohou týmy pro kontrolu a podporu také poslat e-mail.

## <a name="azure-devops-access"></a>Přístup k Azure DevOps

Všichni uživatelé s přístupem k roli "vývojář" v Partnerském centru budou mít přístup k zobrazení pr položek, na které se odkazuje ve zpětné vazbě na recenze.

<!---
To view the PR items referenced in review feedback, publishers must first be granted proper authorization. Otherwise, new publishers receive a 401 - Not Authorized response page when trying to view PRs. To request access to this Azure DevOps repository, perform the following steps:

1. Collect the following information:
    * Your publisher name and ID
    * Offer type (Azure app), offer name, and SKU ID
    * The pull request link, for example: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`. This URL can be retrieved from the notification message or the address of the 401 response page.
    * The email address(es) of the individuals from your publishing organization that you want access granted to. This list should include the owner address(es) you provided when registering as a publisher on the Cloud Partner Portal (or Partner Center??).
1. Create a support incident. In the title bar of the Cloud Partner Portal (??), select the **Help** button, then choose **Support** from the menu. Your default web browser should launch and navigate to the Microsoft new support incident page. (You may have to sign in first.)
1. Specify the **Problem type** as **marketplace onboarding** and **Category** as **Access problem**, then select **Start request**.

    ![Create an incident](./media/create-an-incident.png)

1. In **Step 1 of 2** page, supply your contact information and select **Continue**.
1. In **Step 2 of 2** page, specify an incident title (for example Request Azure DevOps access) and supply the information you collected in the first step (above). Read and accept the agreement, then select **Submit**.

If the incident creation was successful, a confirmation page is displayed. Save the confirmation information on this page for your reference. The Microsoft Support Team should reply to your access request within a few business days. --->

## <a name="reviewing-the-pull-request"></a>Kontrola žádosti o přijetí vyhovení

Pomocí následujícího postupu zkontrolujte problémy zdokumentované v žádosti o přijetí vyžádat.

1. V částech **Microsoft recenze** publikování kroky formuláře, klikněte na odkaz PR spusťte prohlížeč a přejděte na **přehled** (domov) stránku pro tento PR. Následující obrázek znázorňuje příklad domovské stránky kritického problému pro nabídku ukázkové aplikace Contoso. Tato stránka obsahuje užitečné souhrnné informace o problémech s recenzemi, které se nacházejí v aplikaci Azure.

    [![Domovská stránka žádosti o přijetí vyžádat](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Klikněte na obrázek rozbalit.*

1. (Nepovinné) Na pravé straně okna v části **Zásady**klikněte na zprávu o problému (v tomto příkladu: **Ověření zásad se nezdařilo)** a prozkoumejte podrobnosti problému na nižší úrovni, včetně přidružených souborů protokolu. Chyby jsou obvykle zobrazeny v dolní části souborů protokolu.
1. V nabídce na levé straně domovské stránky vyberte **Soubory,** chcete-li zobrazit soubory seznamu, které obsahují technické prostředky pro tuto nabídku. Recenzenti společnosti Microsoft měli přidat komentáře popisující zjištěné kritické problémy. V následujícím příkladu byly zjištěny dva problémy.

    [![Domovská stránka žádosti o přijetí vyžádat](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Klikněte na obrázek rozbalit.*

1. Kliknutím na každý uzel komentáře v levém stromu přejdete na komentář v kontextu okolního kódu. Opravte zdrojový kód v projektu týmu a opravte problém popsaný v komentáři.

>[!Note]
>Technické prostředky nabídky nelze upravovat v prostředí Azure DevOps v týmu pro kontrolu. Pro vydavatele se jedná o prostředí jen pro čtení pro obsažený zdrojový kód. Odpovědi na komentáře však můžete zanechat ve prospěch týmu recenzentů společnosti Microsoft.

   V následujícím příkladu vydavatel zkontroloval, opravil a odpověděl na první vydání.

   ![První oprava a komentář odpověď](./media/first-comment-reply.png)

## <a name="next-steps"></a>Další kroky

Po opravě kritických problémů zdokumentovaných v pr(s review) je nutné [znovu publikovat nabídku aplikace Azure](./create-new-azure-apps-offer.md#publish).

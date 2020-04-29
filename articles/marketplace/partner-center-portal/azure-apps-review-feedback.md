---
title: Zpracování zpětné vazby revize pro nabídky aplikací Azure na komerčním webu Marketplace
description: Jak zpracovávat zpětnou vazbu k nabídce aplikací Azure pro výpis nebo prodej v Azure Marketplace, AppSource nebo prostřednictvím programu Cloud Solution Provider (CSP) pomocí portálu pro komerční tržiště v partnerském centru Microsoftu.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 3932f3fc71dc6427b6cdf93d3a7bc58534a9981c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80279789"
---
# <a name="handling-review-feedback"></a>Zpracování zpětné vazby

Tento článek vysvětluje, jak získat přístup k prostředí Azure DevOps, které používá tým pro kontrolu Microsoft Azure Marketplace. V případě, že se v rámci nabídky aplikace Azure v rámci **Revize Microsoftu** nacházejí kritické problémy, můžete se přihlásit k tomuto systému a zobrazit podrobné informace o těchto problémech (zpětná vazba). Po vyřešení všech těchto problémů je nutné znovu odeslat nabídku, abyste mohli pokračovat v jejím publikování na Azure Marketplace. Následující obrázek znázorňuje, jak se tento proces zpětné vazby týká procesu publikování.

![Zkontrolovat proces zpětné vazby](./media/review-feedback-process.png)

Obvykle se odkazy na problémy odkazují jako žádosti o přijetí změn (PR). Každá žádost o přijetí změn je propojená s online položkou [Azure DevOps](https://azure.microsoft.com/services/devops/) (dříve pojmenovanou Visual Studio Team Services (VSTS)), která obsahuje podrobnosti o problému. Následující obrázek ukazuje příklad prostředí partnerského centra, pokud jsou během recenze zjištěny problémy. 

![Stav publikování](./media/publishing-status.png)

Žádost o přijetí změn, která obsahuje konkrétní podrobnosti o odeslání, bude zmíněna v odkazu zobrazit zprávu o certifikaci. V případě složitých situací vás můžou také e-mailem kontrolovat i týmy pro kontrolu a podporu.

## <a name="azure-devops-access"></a>Přístup k Azure DevOps

Všichni uživatelé s přístupem k roli Developer v partnerském centru budou mít přístup k zobrazení položek žádosti o přijetí změn, na které se odkazuje při kontrole zpětné vazby.

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

## <a name="reviewing-the-pull-request"></a>Kontrola žádosti o získání dat

Pomocí následujícího postupu můžete zkontrolovat problémy popsané v žádosti o přijetí změn.

1. V části **Microsoft prostudovat** formulář kroků publikování klikněte na odkaz PR a spusťte prohlížeč a přejděte na stránku **Přehled** (domovské) pro tuto žádost o přijetí změn. Následující obrázek znázorňuje příklad domovské stránky kritického problému pro nabídku ukázkové aplikace společnosti Contoso. Tato stránka obsahuje užitečné souhrnné informace o problémech kontroly, které najdete v aplikaci Azure.

    [![Domovská stránka žádosti o získání dat](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Klikněte na obrázek a rozbalte ho.*

1. Volitelné Na pravé straně okna v části **zásady**klikněte na zprávu problému (v tomto příkladě **se nezdařilo ověření zásad**) a prozkoumejte podrobnosti o nízké úrovni problému včetně přidružených souborů protokolu. Chyby se obvykle zobrazují v dolní části souborů protokolu.
1. V nabídce na levé straně domovské stránky vyberte **soubory** , abyste zobrazili seznam souborů, které obsahují technické prostředky pro tuto nabídku. Kontroloři společnosti Microsoft by měli přidat komentáře popisující zjištěné kritické problémy. V následujícím příkladu byly zjištěny dva problémy.

    [![Domovská stránka žádosti o získání dat](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Klikněte na obrázek a rozbalte ho.*

1. Kliknutím na každý uzel komentáře v levém stromu přejděte k komentáři v kontextu okolního kódu. Opravte zdrojový kód v projektu týmu a opravte problém popsaný v komentáři.

>[!Note]
>Technické prostředky vaší nabídky nemůžete upravit v prostředí Azure DevOps týmu revize. Pro vydavatele je toto prostředí jen pro čtení pro obsažený zdrojový kód. Můžete však nechat odpovědi na komentáře a využít tak výhod týmu revize společnosti Microsoft.

   V následujícím příkladu byl Vydavatel zkontrolován, opraven a zodpovězen prvního problému.

   ![První oprava a odpověď na komentář](./media/first-comment-reply.png)

## <a name="next-steps"></a>Další kroky

Po opravě důležitých problémů popsaných v revizi žádosti o přijetí změn je nutné [znovu publikovat nabídku aplikace Azure](./create-new-azure-apps-offer.md#publish).

---
title: Kontrola zpětné vazby pro nabídky aplikací Azure – komerční web Marketplace Microsoftu
description: Jak zpracovat zpětnou vazbu k nabídce aplikace Azure z týmu Microsoft Azure Marketplaceho recenze. Ke zpětné vazbě v Azure DevOps můžete přistupovat pomocí přihlašovacích údajů partnerského centra.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: ede4e5dd781851c781407a8acea640e3e501f259
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96436381"
---
# <a name="handling-review-feedback-for-azure-application-offers"></a>Zpracování zpětné vazby revize pro nabídky aplikací Azure

Tento článek vysvětluje, jak získat zpětnou vazbu z týmu pro kontrolu Microsoft Azure Marketplace v [Azure DevOps](https://azure.microsoft.com/services/devops/). V případě, že se v rámci nabídky aplikace Azure v rámci **Revize Microsoftu** nacházejí kritické problémy, můžete se přihlásit k tomuto systému a zobrazit podrobné informace o těchto problémech (zpětná vazba). Po vyřešení všech problémů je nutné znovu odeslat nabídku, aby se mohla dál publikovat na Azure Marketplace. Následující obrázek znázorňuje, jak se tento proces zpětné vazby týká procesu publikování.

![Zkontrolovat proces zpětné vazby](./media/review-feedback-process.png)

Obvykle se odkazy na problémy odkazují jako na žádost o přijetí změn (PR). Každá žádost o přijetí změn je propojená s online položkou Azure DevOps, která obsahuje podrobnosti o problému. Následující obrázek ukazuje příklad prostředí partnerského centra, pokud jsou během recenze zjištěny problémy. 

![Stav publikování](./media/publishing-status.png)

Žádost o přijetí změn, která obsahuje konkrétní podrobnosti o odeslání, bude zmíněna v odkazu zobrazit zprávu o certifikaci. V případě složitých situací vás můžou také e-mailem kontrolovat i týmy pro kontrolu a podporu.

## <a name="azure-devops-access"></a>Přístup k Azure DevOps

Všichni uživatelé s přístupem k roli Developer v partnerském centru budou mít přístup k zobrazení položek žádosti o přijetí změn, na které se odkazuje při kontrole zpětné vazby.

## <a name="reviewing-the-pull-request"></a>Kontrola žádosti o získání dat

Pomocí následujícího postupu můžete zkontrolovat problémy popsané v žádosti o přijetí změn.

1. V části **Microsoft prostudovat** formulář kroků publikování vyberte odkaz PR a spusťte prohlížeč a přejděte na stránku **Přehled** (domovské) pro tuto žádost o přijetí změn. Následující obrázek znázorňuje příklad domovské stránky kritického problému pro nabídku ukázkové aplikace společnosti Contoso. Tato stránka obsahuje užitečné souhrnné informace o problémech kontroly, které najdete v aplikaci Azure.

    [![Domovská stránka žádosti o získání dat](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Kliknutím na tento obrázek rozbalíte.*

1. Volitelné Na pravé straně okna v části **zásady** vyberte zprávu problém (v tomto příkladě: **ověření zásad se nezdařilo**) a prozkoumejte podrobnosti o nízké úrovni problému včetně přidružených souborů protokolu. Chyby se obvykle zobrazují v dolní části souborů protokolu.

1. V nabídce na levé straně domovské stránky vyberte **soubory** , abyste zobrazili seznam souborů, které obsahují technické prostředky pro tuto nabídku. Kontroloři společnosti Microsoft by měli přidat komentáře popisující zjištěné kritické problémy. V následujícím příkladu byly zjištěny dva problémy.

    [![Snímek obrazovky, který zvýrazní soubory a dva zjištěné problémy.](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Kliknutím na tento obrázek rozbalíte.*

1. Vyberte každý uzel komentáře v levém stromu a přejděte k komentáři v kontextu okolního kódu. Opravte zdrojový kód v projektu týmu a opravte problém popsaný v komentáři.

>[!Note]
>Technické prostředky vaší nabídky nemůžete upravit v prostředí Azure DevOps týmu revize. Pro vydavatele je toto prostředí jen pro čtení pro obsažený zdrojový kód. Můžete však nechat odpovědi na komentáře a využít tak výhod týmu revize společnosti Microsoft.

   V následujícím příkladu byl Vydavatel zkontrolován, opraven a zodpovězen prvního problému.

   ![První oprava a odpověď na komentář](./media/first-comment-reply.png)

## <a name="next-steps"></a>Další kroky

Po opravě důležitých problémů popsaných v revizi žádosti o přijetí změn je nutné [znovu publikovat nabídku aplikace Azure](../create-new-azure-apps-offer.md).

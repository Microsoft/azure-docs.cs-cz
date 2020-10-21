---
title: 'Rychlý Start: vytvoření pracovního postupu CI/CD pro Node.js – DevOps Starter pro GitHub pro nasazení do Azure'
description: DevOps Starter usnadňuje začátek práce s Azure pomocí akcí GitHubu.
author: ninallam
editor: ninallam
ms.assetid: ''
ms.devlang: na
ms.service: az-devops-project
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 08/24/2020
ms.author: ninallam
ms.openlocfilehash: 92fa445cee75b2311cbadf96e24c31a1dbd579b8
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92332759"
---
# <a name="set-up-cicd-for-a-nodejs-app-with-devops-starter-using-github-actions"></a>Nastavení CI/CD pro aplikaci pro Node.js s DevOps Starter pomocí akcí GitHubu

V tomto rychlém startu použijete zjednodušené úvodní prostředí DevOps k nastavení pracovního postupu průběžné integrace (CI) a průběžného doručování (CD) pro vaši aplikaci Node.js pomocí akcí GitHubu. DevOps Starter můžete použít k nastavení všeho, co potřebujete pro vývoj, nasazování a monitorování vaší aplikace. 

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Účet [GitHub](https://github.com/)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

DevOps Starter vytvoří pracovní postup CI/CD pomocí akcí GitHubu. DevOps Starter také vytvoří prostředky Azure v předplatném Azure podle vašeho výběru.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Do vyhledávacího pole zadejte **DevOps Starter**a pak vyberte. Kliknutím na **Přidat** vytvořte nový.

    ![Řídicí panel Starter DevOps](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Výběr ukázkové aplikace a služby Azure

1. Klikněte na ikonu **nastavit DevOps Starter na GitHubu** na pravé straně.

    ![landing_page](_img/azure-devops-project-nodejs/landing-page.png)

1. Zajistěte, aby byl poskytovatel CI/CD vybraný jako **Akce GitHubu**.

    ![CICD_provider](_img/azure-devops-project-nodejs/provider-selection.png)

1. Vyberte ukázkovou aplikaci v **Node.js**. Ukázky Node.js zahrnují výběr několika architektur aplikace.

1. Výchozí architektura ukázky je **Express.js**. Nechejte výchozí nastavení a pak vyberte **Další**.   

2. Webová aplikace Windows je výchozím cílem nasazení. Aplikační rozhraní, které jste si zvolili dříve, určuje typ cíle nasazení služby Azure, který je k dispozici zde. Ponechte výchozí službu a pak vyberte **Další**.
 
## <a name="configure-github-account-and-an-azure-subscription"></a>Konfigurace účtu GitHub a předplatného Azure 

1. Ověřování pomocí GitHubu.

   1. Klikněte na tlačítko **autorizovat** . 
   
   1. Přihlaste se k GitHubu. Pokud nemáte účet GitHubu, můžete se zaregistrovat i tady.

2. Vyberte existující **organizaci GitHubu**. 
   
   1. Vyberte název úložiště GitHub. 
   
   1. Vyberte své předplatné a umístění Azure, zvolte název vaší aplikace a potom vyberte **Hotovo**.
    
       ![Enter_GH_details](_img/azure-devops-project-nodejs/gh-details.png)


    Po několika minutách se řídicí panel DevOps Starter zobrazí v Azure Portal. Ukázková aplikace je nastavená v úložišti ve vaší organizaci Azure DevOps, aktivuje se pracovní postup GitHubu a vaše aplikace se nasadí do Azure. Tento řídicí panel poskytuje přehled o vašem úložišti kódu, pracovním postupu GitHubu a vaší aplikaci v Azure.
   
3. Vyberte **Procházet** a zobrazte spuštěnou aplikaci.
    
    Řídicí panel obsahuje podrobnosti o pracovním postupu GitHubu a prostředcích Azure. Pokud chcete zobrazit podrobnosti pracovního postupu GitHubu, jako je například nejnovější spuštění, potvrzení a stav úlohy, budete muset **provést autorizaci do GitHubu**.
   
   ![Authorize_dashboard](_img/azure-devops-project-nodejs/authenticate-dashboard.png)

DevOps Starter automaticky nakonfiguroval pracovní postup GitHubu s úlohami sestavování a nasazování pomocí akcí GitHubu. Teď jste připraveni při práci na aplikaci v Node.js spolupracovat s týmem s využitím procesu CI/CD, který automaticky nasazuje nejnovější práci na web.

   ![dashooard_view](_img/azure-devops-project-nodejs/full-dashboard.png)

## <a name="commit-code-changes-and-execute-cicd"></a>Potvrzení změn kódu a spuštění CI/CD

DevOps Starter vytvoří úložiště v GitHubu. Chcete-li zobrazit úložiště a provést změny kódu v aplikaci, postupujte následovně:

1. Na levé straně řídicího panelu DevOps Starter vyberte odkaz pro svou hlavní větev. Tento odkaz otevře zobrazení nově vytvořeného úložiště GitHub.

1. Pokud chcete zobrazit adresu URL klonování úložiště, vyberte v pravém horním rohu v prohlížeči možnost **klonovat** . Úložiště Git můžete naklonovat do svého oblíbeného integrovaného vývojového prostředí (IDE). V dalších několika krocích můžete k provedení změn kódu a jejich potvrzení přímo do hlavní větve použít webový prohlížeč.

1. Na levé straně prohlížeče otevřete soubor **/Application/views/index.pug** .

1. Vyberte **Upravit**a pak proveďte změnu textu.
    Můžete například změnit některý text pro jednu ze značek.

1. Vyberte **Potvrdit**a potom změny uložte.

1. V prohlížeči přejdete na řídicí panel DevOps Starter.   
Teď byste měli vidět probíhající úlohu sestavení pracovního postupu GitHubu. Změny, které jste provedli, jsou automaticky vytvářeny a nasazeny prostřednictvím pracovního postupu GitHubu.

## <a name="view-the-github-workflow"></a>Zobrazit pracovní postup GitHubu

V předchozím kroku DevOps Starter automaticky nakonfigurovali úplný pracovní postup GitHubu. Podle potřeby Prozkoumejte a upravte pracovní postup. Při seznámení s pracovním postupem proveďte následující kroky.

1. Vlevo od řídicího panelu DevOps Starter vyberte **pracovní postup GitHubu**. Tento odkaz otevře kartu prohlížeče a pracovní postup GitHubu pro váš nový projekt.
    > [!NOTE]
    > Neměňte název souboru pracovního postupu. Název souboru pracovního postupu by měl být **DevOps-Starter-Workflow. yml** , aby řídicí panel odrážel změny.

1. Soubor YAML pracovního postupu obsahuje všechny akce GitHubu potřebné k sestavení a nasazení aplikace. Pokud chcete soubor pracovního postupu přizpůsobit, klikněte na možnost **Upravit soubor** .

1. Na kartě **Code (kód** ) úložiště klikněte na **potvrzení**. Toto zobrazení ukazuje potvrzení kódu, která jsou přidružená ke konkrétnímu nasazení.

1. Na kartě **Akce** úložiště můžete zobrazit historii všech spuštění pracovního postupu v úložišti.

1. Výběrem **posledního spuštění** zobrazíte všechny úlohy spuštěné v pracovním postupu.

1. Kliknutím na **úlohy** zobrazíte podrobné protokoly spuštění pracovního postupu. Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během nasazení i po nich.

1. Pokud si chcete zobrazit všechny žádosti o přijetí změn v úložišti, klikněte na kartu **žádost o získání dat** .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete Azure App Service a další související prostředky odstranit. Použijte funkci **Odstranit** na řídicím panelu DevOps Starter.

## <a name="next-steps"></a>Další kroky

Při konfiguraci procesu CI/CD byl automaticky vytvořen pracovní postup GitHubu. Tento pracovní postup můžete upravit tak, aby vyhovoval potřebám vašeho týmu. Další informace o akcích a pracovních postupech GitHubu najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Přizpůsobení pracovního postupu GitHubu](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-and-managing-workflow-files-and-runs)

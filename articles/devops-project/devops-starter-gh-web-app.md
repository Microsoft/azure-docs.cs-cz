---
title: 'Kurz: nasazení aplikace Node.js do webové aplikace Azure pomocí DevOps Starter pro akce GitHubu'
description: DevOps Starter usnadňuje začátek práce s Azure a nasazení aplikace Node.js do webové aplikace Azure v několika rychlých krocích.
ms.author: ninallam
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 08/25/2020
author: ninallam
ms.openlocfilehash: 4caac943df33177728997c74c9ec812105efd62c
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588773"
---
# <a name="tutorial-deploy-nodejs-app-to-azure-web-app-using-devops-starter-for-github-actions"></a>Kurz: nasazení aplikace Node.js do webové aplikace Azure pomocí DevOps Starter pro akce GitHubu

DevOps Starter pro akce GitHubu nabízí zjednodušené prostředí, ve kterém můžete zvolit ukázkovou aplikaci pro vytvoření pracovního postupu kontinuální integrace (CI) a průběžného doručování (CD) k nasazení do Azure. 

DevOps Starter také:
* Automaticky vytvoří prostředky Azure, jako je například nová webová aplikace Azure.
* Vytvoří a nakonfiguruje pracovní postup v GitHubu, který obsahuje úlohu sestavení pro CI.
* Pracovní postup obsahuje také úlohu nasazení pro CD. 
* Vytvoří prostředek Azure Application Insights pro monitorování.

V tomto kurzu:

> [!div class="checklist"]
> * Použití DevOps Starter k nasazení aplikace Node.js
> * Konfigurace GitHubu a předplatného Azure 
> * Projděte si pracovní postup GitHubu
> * Potvrďte změny v GitHubu a automaticky je nasaďte do Azure.
> * Konfigurace monitorování pomocí Azure Application Insights
> * Vyčištění prostředků

## <a name="prerequisites"></a>Předpoklady

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/).

## <a name="use-devops-starter-to-deploy-a-nodejs-app"></a>Použití DevOps Starter k nasazení aplikace Node.js

DevOps Starter vytvoří pracovní postup v GitHubu. Můžete použít stávající organizaci GitHubu. DevOps Starter taky vytvoří prostředky Azure, jako je webová aplikace v předplatném Azure podle vašeho výběru.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Do vyhledávacího pole zadejte **DevOps Starter** a pak vyberte. Kliknutím na **Přidat** vytvořte nový.

    ![Řídicí panel Starter DevOps](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Zajistěte, aby byl poskytovatel CI/CD vybraný jako **Akce GitHubu**.

    ![CICD_provider](_img/azure-devops-project-nodejs/provider-selection.png)

1. Vyberte **Node.js** a pak vyberte **Další**.

1. V části **zvolte Aplikační architekturu** vyberte **Express.js** a pak vyberte **Další**. Aplikační rozhraní, které jste zvolili v předchozím kroku, určuje typ cíle nasazení služby Azure, který je k dispozici zde. 

1. Vyberte **webovou aplikaci Windows** a potom vyberte **Další**.

## <a name="configure-github-and-an-azure-subscription"></a>Konfigurace GitHubu a předplatného Azure

1. **Autorizovat** GitHub a vyberte existující organizaci GitHubu. 

1. Zadejte název **úložiště GitHub**. 

1. Vyberte služby předplatného Azure. Volitelně můžete vybrat **změnit** a zadat další podrobnosti o konfiguraci, například umístění prostředků Azure.
 
1. Zadejte název webové aplikace a potom vyberte **Hotovo**. Po několika minutách bude webová aplikace Azure připravena. Ukázková Node.js aplikace je nastavená v úložišti ve vaší organizaci GitHubu, spustí se pracovní postup a vaše aplikace se nasadí do nově vytvořené webové aplikace Azure.

   ![Enter_GH_details](_img/azure-devops-project-nodejs/gh-details.png)    

   Po dokončení se v Azure Portal zobrazí řídicí panel Starter DevOps. Můžete také přejít na řídicí panel přímo ze **všech prostředků** v Azure Portal. 

   Řídicí panel poskytuje přehled o vašem úložišti kódu GitHubu, pracovním postupu CI/CD a běžící aplikaci v Azure.   

   ![Zobrazení řídicího panelu](_img/azure-devops-project-nodejs/full-dashboard.png)

DevOps Starter automaticky nakonfiguruje Trigger, který nasadí změny kódu do vašeho úložiště.
    
## <a name="examine-the-github-workflow"></a>Projděte si pracovní postup GitHubu

V předchozím kroku DevOps Starter automaticky nakonfigurovali úplný pracovní postup GitHubu. Podle potřeby Prozkoumejte a upravte pracovní postup. Při seznámení s pracovním postupem proveďte následující kroky.

1. Vlevo od řídicího panelu DevOps Starter vyberte **pracovní postup GitHubu**. Tento odkaz otevře kartu prohlížeče a pracovní postup GitHubu pro váš nový projekt.
    > [!NOTE]
    > Neměňte název souboru pracovního postupu. Název souboru pracovního postupu by měl být **DevOps-Starter-Workflow. yml** , aby řídicí panel odrážel změny.

1. Soubor YAML pracovního postupu obsahuje všechny akce GitHubu potřebné k sestavení a nasazení aplikace. Pokud chcete soubor pracovního postupu přizpůsobit, klikněte na možnost **Upravit soubor** .

1. Na kartě **Code (kód** ) úložiště klikněte na **potvrzení**. Toto zobrazení ukazuje potvrzení kódu, která jsou přidružená ke konkrétnímu nasazení.

1. Na kartě **Akce** v úložišti můžete zobrazit historii všech spuštění pracovního postupu v úložišti.

1. Výběrem **posledního spuštění** zobrazíte všechny úlohy spuštěné v pracovním postupu.

1. Kliknutím na **úlohy** zobrazíte podrobné protokoly spuštění pracovního postupu. Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během nasazení i po nich.

1. Pokud si chcete zobrazit všechny žádosti o přijetí změn v úložišti, klikněte na kartu **žádost o získání dat** .

## <a name="commit-code-changes-and-execute-cicd"></a>Potvrzení změn kódu a spuštění CI/CD

DevOps Starter vytvoří úložiště v GitHubu. Chcete-li zobrazit úložiště a provést změny kódu v aplikaci, postupujte následovně:

1. Na levé straně řídicího panelu DevOps Starter vyberte odkaz pro svou hlavní větev. Tento odkaz otevře zobrazení nově vytvořeného úložiště GitHub.

1. Pokud chcete zobrazit adresu URL klonování úložiště, vyberte v pravém horním rohu v prohlížeči možnost **klonovat** . Úložiště Git můžete naklonovat do svého oblíbeného integrovaného vývojového prostředí (IDE). V několika dalších krocích můžete pomocí webového prohlížeče vytvořit a potvrdit změny kódu přímo do hlavní větve.

1. Na levé straně prohlížeče otevřete soubor **/Application/views/index.pug** .

1. Vyberte **Upravit** a pak proveďte změnu textu.
    Můžete například změnit některý text pro jednu ze značek.

1. Vyberte **Potvrdit** a potom změny uložte.

1. V prohlížeči přejdete na řídicí panel DevOps Starter.   
Teď byste měli vidět probíhající úlohu sestavení pracovního postupu GitHubu. Změny, které jste právě provedli, jsou automaticky vytvořeny a nasazeny prostřednictvím pracovního postupu GitHubu.

1. Až se nasazení dokončí, aktualizujte svoji aplikaci, aby se ověřily vaše změny.

## <a name="configure-azure-application-insights-monitoring"></a>Konfigurace monitorování pomocí Azure Application Insights

Azure Application Insights umožňuje snadné monitorování výkonu a využití aplikace. DevOps Starter automaticky nakonfiguruje prostředek Application Insights pro vaši aplikaci. Podle potřeby můžete dále nakonfigurovat různá upozornění a možnosti monitorování.

1. V Azure Portal přejdete na řídicí panel DevOps Starter. 

1. V pravém dolním rohu vyberte odkaz **Application Insights** pro vaši aplikaci. Otevře se podokno **Application Insights** . Toto zobrazení obsahuje informace o monitorování využití, výkonu a dostupnosti vaší aplikace.

   ![Podokno Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Vyberte **časový rozsah** a pak vyberte **poslední hodina**. Pokud chcete filtrovat výsledky, vyberte **aktualizovat**. Nyní můžete zobrazit všechny aktivity za posledních 60 minut. 
    
1. Chcete-li ukončit časový rozsah, vyberte **x**.

1. Vyberte **výstrahy** a pak vyberte **Přidat výstrahu metriky**. 

1. Zadejte název výstrahy.

1. V rozevíracím seznamu **metrika** prověřte různé metriky výstrah. Výchozí upozornění je na **dobu odezvy serveru delší než 1 sekunda**. Snadno můžete nakonfigurovat různá upozornění a vylepšit tak možnosti monitorování vaší aplikace.

1. Zaškrtněte políčko **oznamování vlastníkům, přispěvatelům a čtenářům e-mailem** . Volitelně můžete provádět další akce, když se zobrazí výstraha spuštěním aplikace logiky Azure.

1. Vyberte **OK** a vytvořte výstrahu. Po chvíli se výstraha zobrazí jako aktivní na řídicím panelu. 

1. Ukončete oblast **výstrahy** a vraťte se do podokna **Application Insights** .

1. Vyberte možnost **dostupnost** a poté vyberte možnost **Přidat test**. 

1. Zadejte název testu a pak vyberte **vytvořit**. Vytvoří se jednoduchý test pomocí příkazu Ping, který ověří dostupnost vaší aplikace. Po několika minutách budou k dispozici výsledky testu a na řídicím panelu Application Insights se zobrazí stav dostupnosti.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud testujete, můžete se vyhnout nabíhání poplatků za účtování pomocí vyčištění prostředků. Pokud už je nepotřebujete, můžete virtuální počítač Azure odstranit a související prostředky, které jste v tomto kurzu vytvořili. K tomu použijte funkci **Odstranit** na řídicím panelu DevOps Starter. 

> [!IMPORTANT]
> Následující postup trvale odstraní prostředky. Funkce *odstranění* zničí data vytvořená projektem v DevOps Starter v Azure a nebude možné ji načíst. Tento postup použijte až po pečlivém čtení výzev.

1. V Azure Portal přejdete na řídicí panel DevOps Starter.
1. V pravém horním rohu vyberte **Odstranit**. 
1. Po zobrazení výzvy vyberte **Ano** , pokud chcete prostředky *trvale odstranit* .

Volitelně můžete upravit pracovní postup tak, aby splňoval potřeby vašeho týmu. Tento vzor CI/CD můžete použít také jako šablonu pro vaše jiná úložiště. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Použití DevOps Starter k nasazení aplikace Node.js
> * Konfigurace GitHubu a předplatného Azure 
> * Projděte si pracovní postup GitHubu
> * Potvrďte změny v GitHubu a automaticky je nasaďte do Azure.
> * Konfigurace monitorování pomocí Azure Application Insights
> * Vyčištění prostředků

Další informace o akcích a pracovních postupech GitHubu najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Přizpůsobení pracovního postupu GitHubu](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-and-managing-workflow-files-and-runs)

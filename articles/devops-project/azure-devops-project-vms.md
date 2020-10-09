---
title: 'Kurz: nasazení aplikace ASP.NET do virtuálních počítačů Azure pomocí Azure DevOps Starter'
description: DevOps Starter usnadňuje začátek práce s Azure a nasazení ASP.NET aplikace do virtuálních počítačů Azure v několika rychlých krocích.
ms.author: mlearned
manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: ce11fe5b65cd49cc880713eb4e47b081b6f3b44f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91855969"
---
# <a name="tutorial-deploy-your-aspnet-app-to-azure-virtual-machines-by-using-azure-devops-starter"></a>Kurz: nasazení aplikace ASP.NET do virtuálních počítačů Azure pomocí Azure DevOps Starter

Azure DevOps Starter prezentuje zjednodušené prostředí, ve kterém můžete přenášet existující kód a úložiště Git nebo zvolit ukázkovou aplikaci pro vytvoření kanálu průběžné integrace (CI) a průběžného doručování (CD) do Azure. 

DevOps Starter také:
* Automaticky vytvoří prostředky Azure, jako je například nový virtuální počítač Azure (VM).
* Vytvoří a nakonfiguruje kanál verze v Azure DevOps, který obsahuje kanál sestavení pro CI.
* Nastaví pro disk CD kanál pro vydání verze. 
* Vytvoří prostředek Azure Application Insights pro monitorování.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Nasazení aplikace ASP.NET pomocí DevOps Starter
> * Konfigurace Azure DevOps a předplatného Azure 
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrďte změny Azure Repos a automaticky je nasaďte do Azure.
> * Konfigurace monitorování pomocí Azure Application Insights
> * Vyčištění prostředků

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-starter-to-deploy-your-aspnet-app"></a>Nasazení aplikace ASP.NET pomocí DevOps Starter

DevOps Starter vytvoří kanál CI/CD v Azure Pipelines. Můžete vytvořit novou organizaci Azure DevOps nebo použít stávající organizaci. DevOps Projects také vytvoří prostředky Azure, jako jsou virtuální počítače v předplatném Azure podle vašeho výběru.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Do vyhledávacího pole zadejte **DevOps Starter**a pak vyberte. Kliknutím na **Přidat** vytvořte nový.

    ![Řídicí panel Starter DevOps](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Vyberte **.NET**a pak vyberte **Další**.

1. V části **zvolte Aplikační architekturu**vyberte **ASP.NET**a pak vyberte **Další**. Aplikační rozhraní, které jste zvolili v předchozím kroku, určuje typ cíle nasazení služby Azure, který je k dispozici zde. 

1. Vyberte virtuální počítač a pak vyberte **Další**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatného Azure

1. Vytvořte novou organizaci Azure DevOps nebo vyberte existující organizaci. 

1. Zadejte název projektu Azure DevOps. 

1. Vyberte služby předplatného Azure. Volitelně můžete vybrat **změnit** a zadat další podrobnosti o konfiguraci, například umístění prostředků Azure.
 
1. Zadejte název virtuálního počítače, uživatelské jméno a heslo pro nový prostředek virtuálního počítače Azure a potom vyberte **Hotovo**. Po několika minutách bude virtuální počítač Azure připravený. Ukázková aplikace ASP.NET je nastavená v úložišti ve vaší organizaci Azure DevOps, spustí se sestavení a vydání a vaše aplikace se nasadí na nově vytvořený virtuální počítač Azure. 

   Po dokončení se v Azure Portal zobrazí řídicí panel Starter DevOps. Můžete také přejít na řídicí panel přímo ze **všech prostředků** v Azure Portal. 

   Řídicí panel poskytuje přehled o vašem úložišti kódu Azure DevOps, kanálu CI/CD a běžící aplikaci v Azure.   

   ![Zobrazení řídicího panelu](_img/azure-devops-project-vms/vm-starter-dashboard.png)

DevOps Starter automaticky nakonfiguruje Trigger sestavení a vydání CI, který nasadí změny kódu do vašeho úložiště. V Azure DevOps můžete nakonfigurovat další možnosti. Chcete-li zobrazit spuštěnou aplikaci, vyberte **Procházet**.
    
## <a name="examine-the-ci-pipeline"></a>Prozkoumání kanálu CI
 
DevOps Starter automaticky nakonfigurovala kanál CI/CD v Azure Pipelines. Kanál můžete prozkoumat a upravit. Pokud se chcete seznámit s kanálem sestavení, udělejte toto:

1. V horní části řídicího panelu DevOps Starter vyberte **vytvořit kanály**. Karta prohlížeč zobrazuje kanál sestavení pro váš nový projekt.

1. Přejděte na pole **stav** a potom vyberte tři tečky (...). V nabídce se zobrazí několik možností, jako je například zařazení nového sestavení do fronty, pozastavení sestavení a úprava kanálu sestavení.

1. Vyberte **Upravit**.

1. V tomto podokně můžete prozkoumávat různé úlohy pro svůj kanál sestavení. Sestavení provádí různé úkoly, jako je například načítání zdrojů z úložiště Git, obnovování závislostí a publikování výstupů používaných pro nasazení.

1. V horní části kanálu buildu, vyberte název kanálu buildu.

1. Změňte název vašeho kanálu sestavení na výstižnější, vyberte **uložit & fronty**a pak vyberte **Uložit**.

1. Pod názvem kanálu buildu vyberte **Historie**. V tomto podokně se zobrazuje záznam pro audit vašich nedávných změn pro sestavení. Azure DevOps sleduje všechny změny provedené v kanálu sestavení a umožňuje porovnat verze.

1. Vyberte **triggery**. DevOps Starter automaticky vytvoří Trigger CI a každé potvrzení do úložiště spustí nové sestavení. Volitelně můžete zvolit zahrnutí nebo vyloučení větví z procesu CI.

1. Vyberte **Uchování**. V závislosti na vašem scénáři můžete určit zásady, které zachovají nebo odeberou určitý počet sestavení.

## <a name="examine-the-cd-pipeline"></a>Prozkoumání kanálu CD

DevOps Starter automaticky vytvoří a nakonfiguruje nezbytné kroky pro nasazení z vaší organizace Azure DevOps do svého předplatného Azure. Tyto kroky zahrnují konfiguraci připojení služby Azure pro ověřování Azure DevOps k vašemu předplatnému Azure. Automatizace také vytvoří kanál CD, který poskytuje disk CD pro virtuální počítač Azure. Pokud se chcete dozvědět víc o kanálu CD Azure DevOps, postupujte takto:

1. Vyberte **sestavení a vydání**a pak vyberte **vydané verze**.  DevOps Starter vytvoří kanál pro vydávání verzí pro správu nasazení do Azure.

1. Vyberte tři tečky (...) vedle vašeho kanálu pro vydání a pak vyberte **Upravit**. Kanál verze obsahuje *kanál*, který definuje proces vydání.

1. V části **Artefakty** vyberte **Zahodit**. Kanál sestavení, který jste prozkoumali v předchozích krocích, vytvoří výstup, který se používá pro artefakt. 

1. Vedle ikony **rozevíracího** seznamu vyberte možnost **aktivační událost průběžného nasazování**. Tento kanál verze má povolený Trigger CD, který spustí nasazení pokaždé, když je k dispozici nový artefakt sestavení. Volitelně můžete aktivační událost zakázat, aby vaše nasazení vyžadovalo ruční spuštění. 

1. Vlevo vyberte **úlohy**a pak vyberte své prostředí. Úlohy jsou aktivity, které proces nasazení provádí, a jsou seskupeny ve fázích. Tento kanál verze se odehrává ve dvou fázích:

    - První fáze obsahuje úlohu nasazení skupiny prostředků Azure, která provede dvě věci:
     
        - Nakonfiguruje virtuální počítač pro nasazení.
        -   Přidá nový virtuální počítač do skupiny nasazení Azure DevOps. Skupina nasazení virtuálních počítačů ve službě Azure DevOps spravuje logické skupiny cílových počítačů nasazení.
     
    - Ve druhé fázi vytvoří úloha Správa webové aplikace IIS na virtuálním počítači web IIS. Pro nasazení lokality se vytvoří druhý úkol nasazení webové aplikace IIS.

1. Na pravé straně vyberte **Zobrazit vydané verze** , aby se zobrazila Historie verzí.

1. Vyberte tři tečky (...) vedle vydané verze a pak vyberte **otevřít**. Můžete prozkoumat několik nabídek, například souhrn vydaných verzí, přidružené pracovní položky a testy.

1. Vyberte **Potvrzení**. Toto zobrazení ukazuje potvrzení kódu, která jsou přidružena k tomuto nasazení. Porovnejte vydané verze a zobrazte rozdíly v potvrzeních jednotlivých nasazení.

1. Vyberte **Protokoly**. Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během i po nasazení.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Potvrďte změny Azure Repos a automaticky je nasaďte do Azure. 

Nyní jste připraveni spolupracovat s týmem v aplikaci pomocí procesu CI/CD, který automaticky nasadí nejnovější práci na web. Každá změna úložiště Git spustí sestavení ve službě Azure DevOps a kanál CD provede nasazení do Azure. Postupujte podle pokynů v této části, nebo použijte jinou techniku, abyste potvrdili změny v úložišti. Změny kódu inicializují proces CI/CD a automaticky nasadí změny na web IIS na virtuálním počítači Azure.

1. V levém podokně vyberte **kód**a pak klikněte na své úložiště.

1. V adresáři *Views\Home* vyberte tři tečky (...) vedle souboru *index. cshtml* a pak vyberte **Upravit**.

1. Proveďte v souboru změny, jako je například přidání textu do jedné z značek DIV. 

1. V pravém horním rohu vyberte **Potvrdit**a pak znovu vyberte **Potvrdit** , aby se vaše změna nahrajte. Po chvíli se Build spustí v Azure DevOps a k nasazení změn se spustí vydání. Sledujte stav buildu na řídicím panelu DevOps Starter nebo v prohlížeči s vaší organizací Azure DevOps.

1. Po dokončení vydaných verzí aktualizujte svou aplikaci, aby se ověřily vaše změny.

## <a name="configure-azure-application-insights-monitoring"></a>Konfigurace monitorování pomocí Azure Application Insights

Azure Application Insights umožňuje snadné monitorování výkonu a využití aplikace. DevOps Starter automaticky nakonfiguruje prostředek Application Insights pro vaši aplikaci. Podle potřeby můžete dále nakonfigurovat různá upozornění a možnosti monitorování.

1. V Azure Portal přejdete na řídicí panel DevOps Starter. 

1. V pravém dolním rohu vyberte odkaz **Application Insights** pro vaši aplikaci. Otevře se podokno **Application Insights** . Toto zobrazení obsahuje informace o monitorování využití, výkonu a dostupnosti vaší aplikace.

   ![Podokno Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Vyberte **časový rozsah**a pak vyberte **poslední hodina**. Pokud chcete filtrovat výsledky, vyberte **aktualizovat**. Nyní můžete zobrazit všechny aktivity za posledních 60 minut. 
    
1. Chcete-li ukončit časový rozsah, vyberte **x**.

1. Vyberte **výstrahy**a pak vyberte **Přidat výstrahu metriky**. 

1. Zadejte název výstrahy.

1. V rozevíracím seznamu **metrika** prověřte různé metriky výstrah. Výchozí upozornění je na **dobu odezvy serveru delší než 1 sekunda**. Snadno můžete nakonfigurovat různá upozornění a vylepšit tak možnosti monitorování vaší aplikace.

1. Zaškrtněte políčko **oznamování vlastníkům, přispěvatelům a čtenářům e-mailem** . Volitelně můžete provádět další akce, když se zobrazí výstraha spuštěním aplikace logiky Azure.

1. Vyberte **OK** a vytvořte výstrahu. Po chvíli se výstraha zobrazí jako aktivní na řídicím panelu. 

1. Ukončete oblast **výstrahy** a vraťte se do podokna **Application Insights** .

1. Vyberte možnost **dostupnost**a poté vyberte možnost **Přidat test**. 

1. Zadejte název testu a pak vyberte **vytvořit**. Vytvoří se jednoduchý test pomocí příkazu Ping, který ověří dostupnost vaší aplikace. Po několika minutách budou k dispozici výsledky testu a na řídicím panelu Application Insights se zobrazí stav dostupnosti.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud testujete, můžete se vyhnout nabíhání poplatků za účtování pomocí vyčištění prostředků. Pokud už je nepotřebujete, můžete virtuální počítač Azure odstranit a související prostředky, které jste v tomto kurzu vytvořili. K tomu použijte funkci **Odstranit** na řídicím panelu DevOps Starter. 

> [!IMPORTANT]
> Následující postup trvale odstraní prostředky. Funkce *odstranění* zničí data vytvořená projektem v DevOps Starter v Azure i Azure DevOps a nebude možné ji načíst. Tento postup použijte až po pečlivém čtení výzev.

1. V Azure Portal přejdete na řídicí panel DevOps Starter.
1. V pravém horním rohu vyberte **Odstranit**. 
1. Po zobrazení výzvy vyberte **Ano** , pokud chcete prostředky *trvale odstranit* .

Tyto kanály buildu a verze můžete volitelně upravit tak, aby splňovaly požadavky vašeho týmu. Tento vzor CI/CD můžete také použít jako šablonu pro své další kanály. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nasazení aplikace ASP.NET pomocí DevOps Starter
> * Konfigurace Azure DevOps a předplatného Azure 
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrďte změny Azure Repos a automaticky je nasaďte do Azure.
> * Konfigurace monitorování pomocí Azure Application Insights
> * Vyčištění prostředků

Další informace o kanálu CI/CD najdete tady:

> [!div class="nextstepaction"]
> [Definice kanálu pro průběžné nasazování (CD) s více fázemi](/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
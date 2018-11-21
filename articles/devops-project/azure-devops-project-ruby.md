---
title: 'Rychlý start: Vytvoření kanálu CI/CD pomocí projektů Azure DevOps pro aplikace Ruby on Rails'
description: Azure DevOps Projects umožňuje snadno začít používat Azure. Můžete spustit webové aplikace Ruby na služby Azure v několika rychlých krocích.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 4cf3feeb92f04b4e97cbdc83c539c206790a78c8
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52264518"
---
# <a name="create-a-cicd-pipeline-for-ruby-on-rails-by-using-azure-devops-projects"></a>Vytvoření kanálu CI/CD pomocí projektů Azure DevOps pro aplikace Ruby on Rails

Konfigurace kontinuální integrace (CI) a průběžné doručování (CD) pro vaše aplikace Ruby on Rails pomocí projektů Azure DevOps. Projekty DevOps zjednodušuje počáteční konfiguraci Azure DevOps sestavení a vydaná verze kanálu.

Pokud nemáte předplatné Azure, můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Přihlásit se na Azure Portal

Projekty Azure DevOps vytvoří kanál CI/CD v úložišti Azure. Můžete vytvořit novou organizaci Azure DevOps nebo použít existující organizace. Prostředky Azure DevOps Projects také vytvoří v rámci předplatného Azure podle vašeho výběru.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **vytvořit prostředek**.

1. Do vyhledávacího pole zadejte **DevOps Projects**a pak vyberte **vytvořit**.

    ![Řídicí panel projekty DevOps](_img/azure-devops-project-github/fullbrowser.png)

## <a name="select-a-sample-app-and-azure-service"></a>Vyberte ukázkové aplikace a služby Azure

1. Vyberte **Ruby** ukázkovou aplikaci.

1. Vyberte architekturu aplikace **Ruby on Rails**. Jakmile budete hotovi, vyberte **Další**.

1. Výchozí cíl nasazení je **Web App on Linux**.  
    Volitelně můžete vybrat **Web App for Containers**. Rozhraní framework aplikace, který jste zvolili dříve, určí typ cíli pro nasazení služby Azure, který je zde k dispozici. 
    
1. Vyberte cílovou službu podle svého výběru a pak vyberte **Další**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatné Azure 

1. Vytvořte novou organizaci bezplatné Azure DevOps nebo zvolte existující organizace. 

1. Zadejte název pro váš projekt Azure DevOps. 

1. Vyberte předplatné Azure a umístění, zadejte název pro vaši aplikaci a pak vyberte **provádí**.  
    Po několika minutách se zobrazí řídicí panel projekty DevOps na webu Azure Portal. Ukázkovou aplikaci je nastavený v úložišti ve vaší organizaci Azure DevOps, sestavení je spuštěno a aplikace je nasazená do Azure. 
    
    Řídicí panel poskytuje přehled o úložišti kódu, váš kanál CI/CD a vaší aplikace v Azure. Na pravé straně vyberte **Procházet** zobrazíte běžící aplikaci.

    ![Zobrazení řídicího panelu](_img/azure-devops-project-go/dashboardnopreview.png) 

## <a name="commit-your-code-changes-and-execute-the-cicd"></a>Potvrzení změn kódu a provedení CI/CD

Projekty Azure DevOps vytvoří úložiště Git v Azure kanály nebo GitHub. K zobrazení úložiště a provádět změny kódu do vaší aplikace, postupujte takto:

1. Na řídicím panelu projekty DevOps na levé straně vyberte odkaz pro hlavní větev.  
    Odkaz otevře zobrazení do nově vytvořené úložiště Git.

1. Chcete-li zobrazit adresu URL klonování úložiště, vyberte **klonování** v pravé horní části.  
    Klonování úložiště Git v oblíbeném prostředí IDE. V dalších několika krocích můžete k provedení změn kódu a jejich potvrzení přímo do hlavní větve použít webový prohlížeč.

1. Na levé straně, přejděte *app/views/pages/home.html.erb* souboru a pak vyberte **upravit**.

1. Změňte ho. Například upravte text v rámci jedné značky div.

1. Vyberte **potvrzení**a pak uložte provedené změny.

1. V prohlížeči přejdete na řídicí panel DevOps Projects.  
    Sestavení by měl být v průběhu. Provedené změny se automaticky vytvořené a nasazené prostřednictvím kanálu CI/CD.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Prozkoumejte kanálu Azure kanály CI/CD

Projekty Azure DevOps automaticky nakonfiguruje úplný kanál CI/CD ve vaší organizaci Azure DevOps. Prozkoumejte kanál a podle potřeby ho upravte. Seznamte se s Azure DevOps sestavení a vydávání kanálů, postupujte takto:

1. Přejdete na řídicí panel DevOps Projects.

1. V horní části stránky, vyberte **vytvářet kanály**.  
    Na kartě prohlížeče zobrazí kanálu sestavení pro nový projekt.

1. Přejděte **stav** pole a pak vyberte tři tečky (...).  
    Nabídka obsahuje několik možností, jako je například přidávání nové sestavení, přerušení sestavení a úpravy kanálu sestavení do fronty.

1. Vyberte **Upravit**.

1. V tomto podokně můžete prozkoumat různé úlohy pro vašeho kanálu sestavení.  
    Sestavení provádí různé úlohy, jako je načítání zdroje z Gitu výstupy úložiště, obnovení závislostí a publikování použít pro nasazení.

1. V horní části kanálu sestavení vyberte název kanálu sestavení.

1. Změnit na něco více popisné, vyberte název vašeho kanálu sestavení **Uložit & frontu**a pak vyberte **Uložit**.

1. Pod názvem kanálu buildu vyberte **Historie**.  
    V tomto podokně se zobrazí záznam auditovaných nedávné změny pro sestavení. Azure DevOps sleduje změny provedené do kanálu sestavení, a umožňuje porovnání verzí.

1. Vyberte **Triggery**.  
    Projekty DevOps automaticky vytvoří aktivační událost CI, a každé potvrzení do úložiště spustí nové sestavení. Volitelně můžete zahrnout nebo vyloučit větve z položek konfigurace procesu.

1. Vyberte **Uchování**.  
    V závislosti na vašem scénáři můžete zadat zásady, které chcete zachovat nebo odebrat počet sestavení.

1. Vyberte **sestavení a vydání**a pak vyberte **vydání**.  
    Projekty DevOps vytvoří kanál pro vydávání verzí pro správu nasazení do Azure.

1. Vyberte tři tečky (...) vedle kanálu pro vydávání verzí a pak vyberte **upravit**.  
    Kanál verze obsahuje *kanál*, který definuje proces vydání.

1. V části **Artefakty** vyberte **Zahodit**.  
    Kanál sestavení, kterou můžete prozkoumat dříve vytvoří výstup, který se používá pro artefakt. 

1. V pravém rohu **vyřadit** ikonu, vyberte **trigger průběžného nasazování**.  
    Tento kanál pro vydávání verzí obsahuje aktivační událost CD povolené, která spustí nasazení pokaždé, když je k dispozici nové artefakt sestavení. Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžaduje ruční spuštění. 

1. Na levé straně vyberte **úlohy**.  
    Úlohy jsou aktivity, který proces nasazení provede. V tomto příkladu byl úkol vytvořen k nasazení do služby Azure App Service.

1. Na pravé straně vyberte **zobrazit verze** zobrazíte historii verzí.

1. Vyberte tři tečky (...) vedle vydané verze a pak vyberte **otevřít**.  
    Můžete si projít několik nabídek, jako je například souhrnu vydané verze, přidružené pracovní položky a testy.

1. Vyberte **Potvrzení**.  
    Toto zobrazení ukazuje potvrzení změn kódu, které jsou spojeny s tímto nasazením. 

1. Vyberte **Protokoly**.  
    Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během a po nasazení.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit instanci služby Azure App Service a související prostředky, které jste vytvořili v rámci tohoto rychlého startu. Chcete-li tak učinit, použijte **odstranit** funkce na řídicím panelu projekty DevOps.

## <a name="next-steps"></a>Další postup

Další informace o úpravách sestavení a vydávání kanálů pro potřeby vašeho týmu, naleznete v tématu:

> [!div class="nextstepaction"]
> [Definování kanálu vícefázové průběžného nasazování (CD)](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

---
title: 'Kurz: Nasazení aplikace ASP.NET a Azure SQL Database kódu pomocí projektů Azure DevOps'
description: DevOps Projects umožňuje snadno začít používat Azure. S projekty DevOps můžete nasadit aplikace v ASP.NET a Azure SQL Database kód v několika rychlých krocích.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 0d05a2f3de92791572f0a5e6313777b5388af3df
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57845218"
---
# <a name="tutorial-deploy-your-aspnet-app-and-azure-sql-database-code-by-using-azure-devops-projects"></a>Kurz: Nasazení aplikace ASP.NET a Azure SQL Database kódu pomocí projektů Azure DevOps

Projekty Azure DevOps představuje zjednodušené prostředí, ve kterém můžete přenést váš stávající kód a úložiště Git nebo jej vybrat ukázkovou aplikaci k vytvoření kanálu průběžného doručování (CD) do Azure a kontinuální integrace (CI). 

Projekty DevOps také:
* Automaticky vytváří prostředky Azure, jako je například Azure SQL database.
* Vytvoří a nakonfiguruje kanál pro vydávání verzí v kanálech Azure, obsahující sestavení kanálu CI.
* Nastaví kanál pro vydávání verzí pro CD. 
* Vytvoří prostředek služby Azure Application Insights pro monitorování.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Nasazení aplikace ASP.NET a Azure SQL Database kódu pomocí projektů Azure DevOps
> * Konfigurace Azure DevOps a předplatné Azure 
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Zapsat změny do úložiště Azure a automaticky nasadit do Azure
> * Připojení k Azure SQL database 
> * Vyčištění prostředků

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-a-project-in-devops-projects-for-an-aspnet-app-and-an-azure-sql-database"></a>Vytvoření projektu v projekty DevOps pro aplikace ASP.NET a Azure SQL database

Projekty DevOps vytvoří kanál CI/CD v kanálech Azure. Můžete vytvořit novou organizaci Azure DevOps nebo použít existující organizace. Projekty DevOps prostředků Azure, jako je Azure SQL database, také vytvoří v rámci předplatného Azure podle vašeho výběru.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **vytvořit prostředek**.

1. Do vyhledávacího pole zadejte **DevOps Projects**a pak vyberte **vytvořit**.

    ![Řídicí panel projekty DevOps](_img/azure-devops-project-github/fullbrowser.png)

1. Vyberte **.NET**a pak vyberte **Další**.

1. V části **zvolte aplikační architekturu**vyberte **ASP.NET**.

1. Vyberte **přidat databázi**a pak vyberte **Další**.  
    Aplikační platformu, kterou jste zvolili v předchozím kroku, určí typ cíli pro nasazení služby Azure, který je zde k dispozici. 
    
1. Vyberte **Další**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatné Azure

1. Vytvořte novou organizaci Azure DevOps, nebo vyberte existující organizace. 

1. Zadejte název pro váš projekt Azure DevOps. 

1. Vyberte služby vašeho předplatného Azure.  
    Volitelně Chcete-li zobrazit další Azure nastavení konfigurace a určit uživatelské jméno v **databáze serveru přihlašovací podrobnosti** oddílu, můžete vybrat **změnu**. Store uživatelského jména pro budoucí kroky v tomto kurzu. Je-li provést tento krok volitelný, ukončete oblasti konfigurace Azure dřív, než vyberete **provádí**.
 
1. Vyberte **Done** (Hotovo).  
    Po několika minutách dokončení procesu a otevře se řídicí panel projekty DevOps na webu Azure Portal. Můžete také přejít na řídicí panel z **všechny prostředky** na webu Azure Portal. Na pravé straně vyberte **Procházet** k běžící aplikaci zobrazit.
    
## <a name="examine-the-ci-pipeline"></a>Prozkoumání kanálu CI

Projekty DevOps automaticky nakonfiguruje úplný kanál CI/CD v úložišti Azure. Kanál můžete prozkoumat a upravit. Seznamte se s kanálem Azure DevOps sestavení, postupujte takto:

1. V horní části řídicího panelu DevOps Projects, vyberte **vytvářet kanály**.  
    Na kartě prohlížeče zobrazí kanálu sestavení pro nový projekt.

1. Přejděte **stav** pole a pak vyberte tři tečky (...).  
    Nabídka obsahuje několik možností, jako je například přidávání nové sestavení, přerušení sestavení a úpravy kanálu sestavení do fronty.

1. Vyberte **Upravit**.

1. V tomto podokně můžete prozkoumat různé úlohy pro vašeho kanálu sestavení.  
    Sestavení provádí různé úlohy, jako je načítání zdroje z Gitu výstupy úložiště, obnovení závislostí a publikování použít pro nasazení.

1. V horní části kanálu sestavení vyberte název kanálu sestavení.

1. Změnit na něco více popisné, vyberte název vašeho kanálu sestavení **Uložit & frontu**a pak vyberte **Uložit**.

1. Pod názvem kanálu buildu vyberte **Historie**.  
    V tomto podokně se zobrazí záznam auditovaných nedávné změny pro sestavení. Kanály Azure uchovává informace o všechny změny provedené kanálu sestavení, a umožňuje porovnání verzí.

1. Vyberte **Triggery**.  
    Projekty DevOps automaticky vytvoří aktivační událost CI, a každé potvrzení do úložiště spustí nové sestavení. Volitelně můžete zahrnout nebo vyloučit větve z položek konfigurace procesu.

1. Vyberte **Uchování**.  
    V závislosti na vašem scénáři můžete zadat zásady, které chcete zachovat nebo odebrat počet sestavení.

## <a name="examine-the-cd-pipeline"></a>Prozkoumání kanálu CD

Projekty DevOps automaticky vytvoří a nakonfiguruje nezbytné kroky k nasazení z vaší organizace Azure DevOps se svým předplatným Azure. Tyto kroky zahrnují konfigurace připojení služby Azure k ověřování Azure DevOps se svým předplatným Azure. Automatizace také vytvoří kanál CD, který poskytuje CD na virtuálním počítači Azure. Další informace o kanálu Azure DevOps CD, postupujte takto:

1. Vyberte **sestavení a vydání**a pak vyberte **vydání**.  
    Projekty DevOps vytvoří kanál pro vydávání verzí pro správu nasazení do Azure.

1. Vyberte tři tečky (...) vedle kanálu pro vydávání verzí a pak vyberte **upravit**.  
    Kanál verze obsahuje *kanál*, který definuje proces vydání.

1. V části **Artefakty** vyberte **Zahodit**.  
    Kanál sestavení, kterou můžete prozkoumat v předchozích krocích vytvoří výstup, který se používá pro artefakt. 

1. V pravém rohu **vyřadit** ikonu, vyberte **trigger průběžného nasazování**.  
    Tento kanál pro vydávání verzí obsahuje aktivační událost CD povolené, která spustí nasazení pokaždé, když je k dispozici nové artefakt sestavení. Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžaduje ruční spuštění. 

    Projekty DevOps nastaví náhodné heslo SQL a použije ho k kanál pro vydávání verzí.
    
1. Na levé straně vyberte **proměnné**. 

   > [!NOTE]
   > Proveďte následující krok jenom v případě, že jste změnili heslo SQL serveru. Je proměnná jedno heslo.
  
1. Vedle položky **hodnotu** , vyberte ikonu visacího zámku nezobrazuje, zadejte nové heslo a potom vyberte **Uložit**.

1. Na levé straně vyberte **úlohy**a potom vyberte vaše prostředí.  
    Úkoly jsou aktivity, které spustí proces nasazení, a jsou seskupené ve fázích. Tento kanál pro vydávání verzí má jedna fáze, která obsahuje *Azure App Service nasadit* a *nasazení databáze SQL Azure* úloh.

1. Vyberte *provést SQL Azure* úloh a podívejte se na různé vlastnosti, které se používají pro nasazení SQL.  
    V části **balíček pro nasazení**, použije úloha *SQL DACPAC* souboru.

1. Na pravé straně vyberte **zobrazit verze** zobrazíte historii verzí.

1. Vyberte tři tečky (...) vedle vydané verze a pak vyberte **otevřít**.  
     Můžete si projít několik nabídek, jako je například souhrnu vydané verze, přidružené pracovní položky a testy.

1. Vyberte **Potvrzení**.  
     Toto zobrazení ukazuje potvrzení změn kódu, které jsou spojeny s tímto nasazením. Porovnejte vydané verze a zobrazte rozdíly v potvrzeních jednotlivých nasazení.

1. Vyberte **Protokoly**.  
     Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během a po nasazení.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Zapsat změny do úložiště Azure a automaticky nasadit do Azure 

 > [!NOTE]
 > Následující postup testuje kanálu CI/CD a jednoduchý text se změní. Testovací proces nasazení SQL, můžete volitelně nastavit schéma systému SQL Server změňte na tabulce.

Teď jste připraveni spolupracovat s týmem ve vaší aplikaci pomocí procesu CI/CD, který automaticky nasadí nejnovější práci na váš web. Jednotlivé změny do úložiště Git v Azure DevOps spustí sestavení a kanál CD provede nasazení do Azure. Postupujte podle pokynů v této části, nebo použít jiné techniky se zapsat změny do úložiště. Změny kódu k zahájení procesu CI/CD a automaticky nasazovat vaše změny do Azure.

1. V levém podokně vyberte **kód**a potom přejděte do úložiště.

1. Přejděte na *SampleWebApplication\Views\Home* adresář, vyberte tři tečky (...) vedle položky *Index.cshtml* souboru a pak vyberte **upravit**. 

1. Proveďte změnu souboru, např. přidejte nějaký text v rámci jedné značky div. 

1. V pravém horním rohu, vyberte **potvrzení**a pak vyberte **potvrzení** znovu, aby push změny.  
    Po chvíli se sestavení začíná v Azure DevOps a provede vydání k nasazení změny. Sledování stavu sestavení v řídicím panelu projekty DevOps, nebo v prohlížeči ve vaší organizaci Azure DevOps.

1. Po vydání aktualizace aplikace, chcete-li ověřit změny.

## <a name="connect-to-the-azure-sql-database"></a>Připojení k Azure SQL database

Budete potřebovat příslušná oprávnění pro připojení k databázi Azure SQL.

1. Na řídicím panelu projekty DevOps vyberte **SQL Database** přejděte na stránku správy pro službu SQL database.
   
1. Vyberte **nastavit bránu firewall serveru**a pak vyberte **přidat IP adresu klienta**. 

1. Vyberte **Uložit**.  
    Vaše IP adresa klienta má nyní přístup k prostředku SQL Server Azure.

1. Přejděte zpět **SQL Database** podokně. 

1. Na pravé straně vyberte název serveru, přejděte na konfigurační stránku pro **systému SQL Server**.

1. Vyberte **resetovat heslo**, zadejte heslo pro přihlašovací jméno správce SQL serveru a pak vyberte **Uložit**.  
    Ujistěte se, aby toto heslo použijete později v tomto kurzu.

    Nyní může volitelně pomocí klientských nástrojů, jako je SQL Server Management Studio nebo Visual Studio pro připojení k serveru SQL Server a Azure SQL database. Pro připojení použijte vlastnost **Název serveru**.

    Pokud jste nezměnili uživatelské jméno pro databázi při počáteční konfiguraci projektu v DevOps Projects, vaše uživatelské jméno je místní části e-mailovou adresu. Například, pokud je vaše e-mailovou adresu *johndoe\@microsoft.com*, vaše uživatelské jméno je *johndoe*.

   > [!NOTE]
   > Pokud změníte heslo pro přihlašovací jméno SQL, musíte změnit heslo v kanálu proměnná vydané verze, jak je popsáno v části "Zkontrolujte kanálu CD".

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud testujete, vyhnete se účtují poplatky podle údržbě vašich prostředků. Pokud už je nepotřebujete, můžete odstranit databázi Azure SQL a související prostředky, které jste vytvořili v tomto kurzu. Chcete-li tak učinit, použijte **odstranit** funkce na řídicím panelu projekty DevOps.

> [!IMPORTANT]
> Následující postup se trvale odstraní prostředky. *Odstranit* funkcí zničí data, která se vytvoří v projektu v projektech pro DevOps v Azure i Azure DevOps a nebude možné ho načíst. Pomocí tohoto postupu, až poté, co jste pečlivě přečtěte si zobrazených výzev.

1. Na webu Azure Portal přejděte do řídicího panelu DevOps Projects.
2. V pravém horním rohu, vyberte **odstranit**. 
3. Do příkazového řádku vyberte **Ano** k *trvale odstranit* prostředky.

## <a name="next-steps"></a>Další postup

Tyto kanály buildu a verze můžete volitelně upravit tak, aby splňovaly požadavky vašeho týmu. Tento vzor CI/CD můžete také použít jako šablonu pro své další kanály. V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nasazení aplikace ASP.NET a Azure SQL Database kódu pomocí projektů Azure DevOps
> * Konfigurace Azure DevOps a předplatné Azure 
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Zapsat změny do úložiště Azure a automaticky nasadit do Azure
> * Připojení k Azure SQL database 
> * Vyčištění prostředků

Další informace o kanálu CI/CD, naleznete v tématu:

> [!div class="nextstepaction"]
> [Definování kanálu vícefázové průběžného nasazování (CD)](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Videa

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]

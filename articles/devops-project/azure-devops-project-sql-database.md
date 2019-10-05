---
title: 'Kurz: nasazení aplikace ASP.NET a kódu Azure SQL Database pomocí Azure DevOps Projects'
description: DevOps Projects usnadňuje začátek práce s Azure. Pomocí DevOps Projects můžete nasadit aplikaci ASP.NET a kód Azure SQL Database v několika rychlých krocích.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 560b16a40d73d07df6c9f446c7196cd930e2274e
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71971499"
---
# <a name="tutorial-deploy-your-aspnet-app-and-azure-sql-database-code-by-using-azure-devops-projects"></a>Kurz: nasazení aplikace ASP.NET a kódu Azure SQL Database pomocí Azure DevOps Projects

Azure DevOps Projects představuje zjednodušené prostředí, ve kterém můžete přenášet existující kód a úložiště Git nebo zvolit ukázkovou aplikaci pro vytvoření kanálu průběžné integrace (CI) a průběžného doručování (CD) do Azure. 

DevOps Projects také:
* Automaticky vytvoří prostředky Azure, jako je Azure SQL Database.
* Vytvoří a nakonfiguruje kanál vydání v Azure Pipelines, který obsahuje kanál sestavení pro CI.
* Nastaví pro disk CD kanál pro vydání verze. 
* Vytvoří prostředek Azure Application Insights pro monitorování.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Použití Azure DevOps Projects k nasazení aplikace ASP.NET a kódu Azure SQL Database
> * Konfigurace Azure DevOps a předplatného Azure 
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrďte změny Azure Repos a automaticky je nasaďte do Azure.
> * Připojení k databázi SQL Azure 
> * Vyčištění prostředků

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-a-project-in-devops-projects-for-an-aspnet-app-and-an-azure-sql-database"></a>Vytvoření projektu v DevOps Projects pro aplikaci ASP.NET a službu Azure SQL Database

DevOps Projects vytvoří v Azure Pipelines kanál CI/CD. Můžete vytvořit novou organizaci Azure DevOps nebo použít stávající organizaci. DevOps Projects taky vytvoří prostředky Azure, jako je Azure SQL Database, v předplatném Azure dle vašeho výběru.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **vytvořit prostředek**.

1. Do vyhledávacího pole zadejte **DevOps Projects**a pak vyberte **vytvořit**.

    ![Řídicí panel DevOps Projects](_img/azure-devops-project-github/fullbrowser.png)

1. Vyberte **.NET**a pak vyberte **Další**.

1. V části **zvolit rozhraní aplikace**vyberte **ASP.NET**.

1. Vyberte **Přidat databázi**a pak vyberte **Další**.  
    Aplikační rozhraní, které jste zvolili v předchozím kroku, určuje typ cíle nasazení služby Azure, který je k dispozici zde. 
    
1. Vyberte **Další**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatného Azure

1. Vytvořte novou organizaci Azure DevOps nebo vyberte existující organizaci. 

1. Zadejte název projektu Azure DevOps. 

1. Vyberte služby předplatného Azure.  
    Pokud chcete zobrazit další nastavení konfigurace Azure a identifikovat uživatelské jméno v části **Podrobnosti o přihlášení k databázovému serveru** , můžete vybrat **změnit**. Uložte si uživatelské jméno pro budoucí kroky v tomto kurzu. Pokud tento volitelný krok provedete, před výběrem **Hotovo**ukončete oblast konfigurace Azure.
 
1. Vyberte **Done** (Hotovo).  
    Po několika minutách se proces dokončí a v Azure Portal se otevře řídicí panel DevOps Projects. Můžete také přejít na řídicí panel přímo ze **všech prostředků** v Azure Portal. Na pravé straně vyberte **Procházet** a zobrazte spuštěnou aplikaci.
    
## <a name="examine-the-ci-pipeline"></a>Prozkoumání kanálu CI

DevOps Projects automaticky konfiguruje plný kanál CI/CD v Azure Repos. Kanál můžete prozkoumat a upravit. Pokud se chcete seznámit s kanálem sestavení Azure DevOps, postupujte takto:

1. V horní části řídicího panelu DevOps Projects vyberte **vytvořit kanály**.  
    Karta prohlížeč zobrazuje kanál sestavení pro váš nový projekt.

1. Přejděte na pole **stav** a potom vyberte tři tečky (...).  
    V nabídce se zobrazí několik možností, jako je například zařazení nového sestavení do fronty, pozastavení sestavení a úprava kanálu sestavení.

1. Vyberte **Upravit**.

1. V tomto podokně můžete prozkoumávat různé úlohy pro svůj kanál sestavení.  
    Sestavení provádí různé úkoly, jako je například načítání zdrojů z úložiště Git, obnovení závislostí a publikování výstupů používaných pro nasazení.

1. V horní části kanálu sestavení vyberte název kanálu sestavení.

1. Změňte název vašeho kanálu sestavení na výstižnější, vyberte **uložit & fronty**a pak vyberte **Uložit**.

1. Pod názvem vašeho kanálu buildu vyberte **Historie**.  
    V tomto podokně se zobrazuje záznam pro audit vašich nedávných změn pro sestavení. Azure Pipelines sleduje všechny změny provedené v kanálu sestavení a umožňuje porovnat verze.

1. Vyberte **Triggery**.  
    DevOps Projects automaticky vytvoří Trigger CI a každé potvrzení do úložiště spustí nové sestavení. Volitelně můžete zvolit zahrnutí nebo vyloučení větví z procesu CI.

1. Vyberte **Uchování**.  
    V závislosti na vašem scénáři můžete určit zásady, které zachovají nebo odeberou určitý počet sestavení.

## <a name="examine-the-cd-pipeline"></a>Prozkoumání kanálu CD

DevOps Projects automaticky vytvoří a nakonfiguruje nezbytné kroky pro nasazení z vaší organizace Azure DevOps do svého předplatného Azure. Tyto kroky zahrnují konfiguraci připojení služby Azure pro ověřování Azure DevOps k vašemu předplatnému Azure. Automatizace také vytvoří kanál CD, který poskytuje disk CD pro virtuální počítač Azure. Pokud se chcete dozvědět víc o kanálu CD Azure DevOps, postupujte takto:

1. Vyberte **sestavení a vydání**a pak vyberte **vydané verze**.  
    DevOps Projects vytvoří kanál pro vydávání verzí pro správu nasazení do Azure.

1. Vyberte tři tečky (...) vedle vašeho kanálu pro vydání a pak vyberte **Upravit**.  
    Kanál verze obsahuje *kanál*, který definuje proces vydání.

1. V části **Artefakty** vyberte **Zahodit**.  
    Kanál sestavení, který jste prozkoumali v předchozích krocích, vytvoří výstup, který se používá pro artefakt. 

1. Napravo od ikony **drop** vyberte **Trigger průběžného nasazování**.  
    Tento kanál verze má povolený Trigger CD, který spustí nasazení pokaždé, když je k dispozici nový artefakt sestavení. Volitelně můžete aktivační událost zakázat, aby vaše nasazení vyžadovalo ruční spuštění. 

    DevOps Projects nastaví náhodné heslo SQL a použije ho pro kanál pro vydávání verzí.
    
1. Vlevo vyberte **proměnné**. 

   > [!NOTE]
   > Následující krok proveďte pouze v případě, že jste změnili SQL Server heslo. Existuje proměnná s jedním heslem.
  
1. Vedle pole **hodnota** vyberte ikonu visacího zámku nezobrazuje, zadejte nové heslo a pak vyberte **Uložit**.

1. Vlevo vyberte **úlohy**a pak vyberte své prostředí.  
    Úlohy jsou aktivity, které proces nasazení provádí, a jsou seskupeny ve fázích. Tento kanál vydaných verzí má jednu fázi, která obsahuje úlohu nasazení *Azure App Service nasazení* a *Azure SQL Database* .

1. Vyberte úlohu *Spustit SQL Azure* a prověřte různé vlastnosti, které se používají pro nasazení SQL.  
    V části **balíček pro nasazení**používá úloha soubor *SQL DACPAC* .

1. Na pravé straně vyberte **Zobrazit vydané verze** , aby se zobrazila Historie verzí.

1. Vyberte tři tečky (...) vedle vydané verze a pak vyberte **otevřít**.  
     Můžete prozkoumat několik nabídek, například souhrn vydaných verzí, přidružené pracovní položky a testy.

1. Vyberte **Potvrzení**.  
     Toto zobrazení ukazuje potvrzení kódu, která jsou přidružena k tomuto nasazení. Porovnejte vydané verze a zobrazte rozdíly v potvrzeních jednotlivých nasazení.

1. Vyberte **Protokoly**.  
     Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během i po nasazení.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Potvrďte změny Azure Repos a automaticky je nasaďte do Azure. 

 > [!NOTE]
 > Následující postup testuje kanál CI/CD s jednoduchou změnou textu. Chcete-li otestovat proces nasazení SQL, můžete volitelně vytvořit SQL Server schématu změny v tabulce.

Nyní jste připraveni spolupracovat s týmem v aplikaci pomocí procesu CI/CD, který automaticky nasadí nejnovější práci na web. Každá změna úložiště Git spustí sestavení ve službě Azure DevOps a kanál CD provede nasazení do Azure. Postupujte podle pokynů v této části, nebo použijte jiný postup, chcete-li potvrdit změny do úložiště. Změny kódu inicializují proces CI/CD a automaticky nasadí vaše změny do Azure.

1. V levém podokně vyberte **kód**a pak klikněte na své úložiště.

1. V adresáři *SampleWebApplication\Views\Home* vyberte tři tečky (...) vedle souboru *index. cshtml* a pak vyberte **Upravit**. 

1. Proveďte v souboru změny, jako je například přidání textu do jedné z značek DIV. 

1. V pravém horním rohu vyberte **Potvrdit**a pak znovu vyberte **Potvrdit** , aby se vaše změna nahrajte.  
    Po chvíli se Build spustí v Azure DevOps a k nasazení změn se spustí vydání. Sledujte stav buildu na řídicím panelu DevOps Projects nebo v prohlížeči s vaší organizací Azure DevOps.

1. Po dokončení vydaných verzí aktualizujte svou aplikaci, aby se ověřily vaše změny.

## <a name="connect-to-the-azure-sql-database"></a>Připojení k databázi SQL Azure

Pro připojení k databázi SQL Azure potřebujete příslušná oprávnění.

1. Na řídicím panelu DevOps Projects vyberte **SQL Database** a přejdete na stránku správy pro databázi SQL.
   
1. Vyberte **nastavit bránu firewall serveru**a pak vyberte **Přidat IP adresu klienta**. 

1. Vyberte **Save** (Uložit).  
    Vaše IP adresa klienta má teď přístup k SQL Server prostředku Azure.

1. Vraťte se do podokna **SQL Database** . 

1. Na pravé straně vyberte název serveru, který chcete přejít na stránku konfigurace pro **SQL Server**.

1. Vyberte **resetovat heslo**, zadejte heslo pro přihlašovací jméno správce SQL Server a pak vyberte **Uložit**.  
    Nezapomeňte toto heslo zachovat pro pozdější použití v tomto kurzu.

    K připojení k SQL Server a databázi SQL Azure teď můžete volitelně použít klientské nástroje, jako je SQL Server Management Studio nebo Visual Studio. Pro připojení použijte vlastnost **Název serveru**.

    Pokud jste nezměnili uživatelské jméno databáze při počáteční konfiguraci projektu v DevOps Projects, je vaše uživatelské jméno místní součástí vaší e-mailové adresy. Pokud je vaše e-mailová adresa například *johndoe\@microsoft.com*, vaše uživatelské jméno je *johndoe*.

   > [!NOTE]
   > Pokud změníte heslo pro přihlášení SQL, je nutné změnit heslo v proměnné kanálu verze, jak je popsáno v části "Projděte si kanál CD".

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud testujete, můžete se vyhnout nabíhání poplatků za účtování pomocí vyčištění prostředků. Pokud už je nepotřebujete, můžete databázi SQL Azure a související prostředky, které jste vytvořili v tomto kurzu, odstranit. K tomu použijte funkci **Odstranit** na řídicím panelu DevOps Projects.

> [!IMPORTANT]
> Následující postup trvale odstraní prostředky. Funkce *odstranění* zničí data vytvořená projektem v DevOps Projects v Azure i ve službě Azure DevOps a nebude možné ji načíst. Tento postup použijte až po pečlivém čtení výzev.

1. V Azure Portal přejdete na řídicí panel DevOps Projects.
2. V pravém horním rohu vyberte **Odstranit**. 
3. Po zobrazení výzvy vyberte **Ano** , pokud chcete prostředky *trvale odstranit* .

## <a name="next-steps"></a>Další kroky

Tyto kanály buildu a verze můžete volitelně upravit tak, aby splňovaly požadavky vašeho týmu. Tento vzor CI/CD můžete také použít jako šablonu pro své další kanály. V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Použití Azure DevOps Projects k nasazení aplikace ASP.NET a kódu Azure SQL Database
> * Konfigurace Azure DevOps a předplatného Azure 
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrďte změny Azure Repos a automaticky je nasaďte do Azure.
> * Připojení k databázi SQL Azure 
> * Vyčištění prostředků

Další informace o kanálu CI/CD najdete tady:

> [!div class="nextstepaction"]
> [Definice kanálu pro průběžné nasazování (CD) s více fázemi](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Videa

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]

---
title: 'Kurz: Nasazení ASP.NET aplikace a kódu databáze Azure SQL pomocí projektů Azure DevOps'
description: DevOps Projekty usnadňuje začínáme v Azure. S DevOps Projects můžete nasadit ASP.NET aplikaci a kód Azure SQL Database v několika rychlých krocích.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 560b16a40d73d07df6c9f446c7196cd930e2274e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "71971499"
---
# <a name="tutorial-deploy-your-aspnet-app-and-azure-sql-database-code-by-using-azure-devops-projects"></a>Kurz: Nasazení ASP.NET aplikace a kódu databáze Azure SQL pomocí projektů Azure DevOps

Azure DevOps Projects představuje zjednodušené prostředí, kde můžete přenést existující kód a úložiště Git nebo zvolit ukázkovou aplikaci pro vytvoření kanálu průběžné integrace (CI) a průběžného doručování (CD) do Azure. 

DevOps Projekty také:
* Automaticky vytvoří prostředky Azure, jako je například databáze Azure SQL.
* Vytvoří a nakonfiguruje kanál vydání v Azure Pipelines, který zahrnuje kanál sestavení pro CI.
* Nastaví kanál vydání pro disk CD. 
* Vytvoří prostředek Azure Application Insights pro monitorování.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Nasazení ASP.NET aplikace a kódu databáze Azure SQL pomocí projektů Azure DevOps
> * Konfigurace Azure DevOps a předplatného Azure 
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrzení změn v Azure Repos a jejich automatické nasazení do Azure
> * Připojení k databázi Azure SQL 
> * Vyčištění prostředků

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-a-project-in-devops-projects-for-an-aspnet-app-and-an-azure-sql-database"></a>Vytvoření projektu v projektech DevOps pro ASP.NET aplikaci a databázi Azure SQL

DevOps Projekty vytvoří kanál CI/CD v Azure pipelines. Můžete vytvořit novou organizaci Azure DevOps nebo použít existující organizaci. DevOps Projects také vytvoří prostředky Azure, jako je například databáze Azure SQL, v předplatném Azure dle vašeho výběru.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. V levém podokně vyberte **Vytvořit prostředek**.

1. Do vyhledávacího pole zadejte **DevOps Projects**a pak vyberte **Vytvořit**.

    ![Řídicí panel Projekty DevOps](_img/azure-devops-project-github/fullbrowser.png)

1. Vyberte **možnost .NET**a pak vyberte **Další**.

1. V části **Zvolit architekturu aplikace**vyberte **ASP.NET**.

1. Vyberte **Přidat databázi**a pak vyberte **Další**.  
    Rozhraní aplikace, které jste zvolili v předchozím kroku, určuje typ cíle nasazení služby Azure, který je k dispozici zde. 
    
1. Vyberte **další**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatného Azure

1. Vytvořte novou organizaci Azure DevOps nebo vyberte existující organizaci. 

1. Zadejte název projektu Azure DevOps. 

1. Vyberte předplacené služby Azure.  
    Volitelně můžete zobrazit další nastavení konfigurace Azure a identifikovat uživatelské jméno v části **Přihlašovací údaje databázového serveru,** můžete vybrat **změnit**. Uložte si uživatelské jméno pro další kroky v tomto kurzu. Pokud provedete tento volitelný krok, ukončete oblast konfigurace Azure dříve, než vyberete **Hotovo**.
 
1. Vyberte **Done** (Hotovo).  
    Po několika minutách se proces dokončí a otevře se řídicí panel DevOps Projects na webu Azure Portal. Na řídicí panel můžete taky přejít přímo ze **všech prostředků** na webu Azure Portal. Vpravo vyberte **Procházet** a zobrazte spuštěnou aplikaci.
    
## <a name="examine-the-ci-pipeline"></a>Prozkoumání kanálu CI

DevOps Projects automaticky konfiguruje úplný kanál CI/CD v Azure Repos. Kanál můžete prozkoumat a upravit. Pokud se chcete seznámit s kanálem sestavení Azure DevOps, postupujte takto:

1. V horní části řídicího panelu Projekty DevOps vyberte **Sestavit kanály**.  
    Karta prohlížeče zobrazuje kanál sestavení pro váš nový projekt.

1. Přejděte na pole **Stav** a vyberte tři tečky (...).  
    Nabídka zobrazuje několik možností, jako je například zařazení nového sestavení do fronty, pozastavení sestavení a úprava kanálu sestavení.

1. Vyberte **Upravit**.

1. V tomto podokně můžete prozkoumat různé úkoly pro kanál sestavení.  
    Sestavení provádí různé úlohy, jako je například načítání zdrojů z úložiště Git, obnovení závislostí a publikování výstupů používaných pro nasazení.

1. V horní části kanálu buildu, vyberte název kanálu buildu.

1. Změňte název kanálu sestavení na něco popisnějšího, vyberte **Uložit & fronty**a pak vyberte **Uložit**.

1. Pod názvem kanálu buildu vyberte **Historie**.  
    Toto podokno zobrazuje záznam auditu posledních změn pro sestavení. Azure Pipelines sleduje všechny změny provedené v kanálu sestavení a umožňuje porovnat verze.

1. Vyberte **možnost Aktivační události**.  
    DevOps Projekty automaticky vytvoří aktivační událost CI a každé potvrzení do úložiště spustí nové sestavení. Volitelně můžete zahrnout nebo vyloučit větve z procesu CI.

1. Vyberte **Uchování**.  
    V závislosti na scénáři můžete určit zásady zachovat nebo odebrat určitý počet sestavení.

## <a name="examine-the-cd-pipeline"></a>Prozkoumání kanálu CD

DevOps Projects automaticky vytvoří a nakonfiguruje nezbytné kroky k nasazení z vaší organizace Azure DevOps do vašeho předplatného Azure. Mezi tyto kroky patří konfigurace připojení služby Azure k ověření Azure DevOps k vašemu předplatnému Azure. Automatizace také vytvoří kanál CD, který poskytuje CD do virtuálního počítače Azure. Další informace o kanálu disku CD Azure DevOps postupujte takto:

1. Vyberte **Sestavit a uvolnit**a pak vyberte **Verze**.  
    DevOps Projects vytvoří kanál vydání pro správu nasazení do Azure.

1. Vyberte tři tečky (...) vedle kanálu vydání a pak vyberte **Upravit**.  
    Kanál verze obsahuje *kanál*, který definuje proces vydání.

1. V části **Artefakty** vyberte **Zahodit**.  
    Kanál sestavení, který jste zkoumali v předchozích krocích, vytvoří výstup, který se používá pro artefakt. 

1. Na pravé straně ikony **Přetažení** vyberte **aktivační událost nepřetržitého nasazení**.  
    Tento kanál vydání má povolenou aktivační událost CD, která spustí nasazení pokaždé, když je k dispozici nový artefakt sestavení. Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžadují ruční spuštění. 

    DevOps Projects nastaví náhodné heslo SQL a použije ho pro kanál vydání.
    
1. Vlevo vyberte **Proměnné**. 

   > [!NOTE]
   > Následující krok proveďte pouze v případě, že jste změnili heslo serveru SQL Server. Existuje jedna proměnná hesla.
  
1. Vedle pole **Hodnota** vyberte ikonu visacího zámku, zadejte nové heslo a pak vyberte **Uložit**.

1. Vlevo vyberte **Úkoly**a pak vyberte prostředí.  
    Úkoly jsou aktivity, které proces nasazení provádí a jsou seskupeny ve fázích. Tento kanál vydání má jednu fázi, která obsahuje úlohu *nasazení služby Azure App Service* a nasazení databáze Azure *SQL.*

1. Vyberte *úlohu Spustit Azure SQL* a zkontrolujte různé vlastnosti, které se používají pro nasazení SQL.  
    V části **Balíček nasazení**úloha používá soubor *SQL DACPAC.*

1. Vpravo vyberte **Zobrazit vydání,** chcete-li zobrazit historii verzí.

1. Vyberte tři tečky (...) vedle verze a pak vyberte **Otevřít**.  
     Můžete prozkoumat několik nabídek, jako je například souhrn vydání, přidružené pracovní položky a testy.

1. Vyberte **Potvrzení**.  
     Toto zobrazení zobrazuje potvrzení kódu, které jsou přidruženy k tomuto nasazení. Porovnejte vydané verze a zobrazte rozdíly v potvrzeních jednotlivých nasazení.

1. Vyberte **Protokoly**.  
     Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během nasazení i po něm.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Potvrzení změn v Azure Repos a jejich automatické nasazení do Azure 

 > [!NOTE]
 > Následující postup testuje kanál CI/CD s jednoduchou změnou textu. Chcete-li otestovat proces nasazení SQL, můžete volitelně provést změnu schématu sql serveru do tabulky.

Nyní jste připraveni spolupracovat s týmem na vaší aplikaci pomocí procesu CI/CD, který automaticky nasazuje vaši nejnovější práci na váš web. Každá změna úložiště Git spustí sestavení v Azure DevOps a kanál CD spustí nasazení do Azure. Postupujte podle postupu v této části nebo použijte jinou techniku k potvrzení změn v úložišti. Změny kódu zahájí proces CI/CD a automaticky nasadí vaše změny do Azure.

1. V levém podokně vyberte **Kód**a přejděte do úložiště.

1. Přejděte do *adresáře SampleWebApplication\Views\Home,* vyberte tři tečky (...) vedle souboru *Index.cshtml* a pak vyberte **Upravit**. 

1. Proveďte změnu souboru, například přidání textu do jedné z značek div. 

1. Vpravo nahoře vyberte **Potvrdit**a pak znovu vyberte **Potvrdit,** abyste změnu posunuli.  
    Po několika okamžicích se sestavení spustí v Azure DevOps a verze se spustí k nasazení změn. Sledujte stav sestavení na řídicím panelu DevOps Projects nebo v prohlížeči pomocí organizace Azure DevOps.

1. Po dokončení vydání aktualizujte aplikaci a ověřte změny.

## <a name="connect-to-the-azure-sql-database"></a>Připojení k databázi Azure SQL

Potřebujete příslušná oprávnění pro připojení k databázi Azure SQL.

1. Na řídicím panelu DevOps Projects vyberte **SQL Database** a přejděte na stránku pro správu databáze SQL.
   
1. Vyberte **Nastavit bránu firewall serveru**a pak **vyberte Přidat IP klienta**. 

1. Vyberte **Uložit**.  
    Vaše IP adresa klienta má nyní přístup k prostředku SQL Server Azure.

1. Vraťte se do **podokna Databáze SQL.** 

1. Vpravo vyberte název serveru a přejděte na konfigurační stránku serveru **SQL Server**.

1. Vyberte **Obnovit heslo**, zadejte heslo pro přihlášení správce serveru SQL Server a pak vyberte **Uložit**.  
    Ujistěte se, že zachovat toto heslo použít později v tomto kurzu.

    Nyní můžete volitelně používat klientské nástroje, jako je SQL Server Management Studio nebo Visual Studio pro připojení k SQL Server a azure SQL databáze. Pro připojení použijte vlastnost **Název serveru**.

    Pokud jste nezměnili uživatelské jméno databáze při počáteční konfiguraci projektu v devops projekty, vaše uživatelské jméno je místní část vaší e-mailové adresy. Pokud je například vaše e-mailová adresa *johndoe\@microsoft.com*, vaše uživatelské jméno je *johndoe*.

   > [!NOTE]
   > Pokud změníte heslo pro přihlášení SQL, musíte změnit heslo v proměnné kanálu vydání, jak je popsáno v části "Zkontrolujte kanál CD".

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud testujete, můžete se vyhnout nabíhání fakturačních poplatků vyčištěním prostředků. Když už nejsou potřeba, můžete odstranit databázi Azure SQL a související prostředky, které jste vytvořili v tomto kurzu. Chcete-li tak učinit, použijte funkci **Odstranit** na řídicím panelu Projekty DevOps.

> [!IMPORTANT]
> Následující postup trvale odstraní prostředky. Delete *Delete* funkce zničí data, která je vytvořena projektu v devops projekty v Azure a Azure DevOps a nebude možné načíst. Tento postup použijte až poté, co si pečlivě přečtete výzvy.

1. Na webu Azure Portal přejděte na řídicí panel Projekty DevOps.
2. Vpravo nahoře vyberte **Odstranit**. 
3. V řádku vyberte **Ano,** *chcete-li* prostředky trvale odstranit.

## <a name="next-steps"></a>Další kroky

Tyto kanály buildu a verze můžete volitelně upravit tak, aby splňovaly požadavky vašeho týmu. Tento vzor CI/CD můžete také použít jako šablonu pro své další kanály. V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nasazení ASP.NET aplikace a kódu databáze Azure SQL pomocí projektů Azure DevOps
> * Konfigurace Azure DevOps a předplatného Azure 
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Potvrzení změn v Azure Repos a jejich automatické nasazení do Azure
> * Připojení k databázi Azure SQL 
> * Vyčištění prostředků

Další informace o kanálu CI/CD najdete v následujících tématech:

> [!div class="nextstepaction"]
> [Definování vícestupňového kanálu průběžného nasazení (CD)](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Videa

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]

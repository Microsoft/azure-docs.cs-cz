---
title: Nasazení aplikace ASP.NET a služby Azure SQL Database pomocí projektu Azure DevOps | Kurz Azure DevOps Services
description: DevOps Project usnadňuje začátek práce v Azure. Projekt Azure DevOps umožňuje snadno a v několika rychlých krocích nasadit aplikaci ASP.NET se službou Azure SQL Database.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: d9c7c94e344daee5af87ce40ddf4dcb686696ded
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297329"
---
# <a name="tutorial--deploy-your-aspnet-app-and-azure-sql-database-with-the-azure-devops-project"></a>Kurz: Nasazení aplikace ASP.NET a služby Azure SQL Database pomocí služby Azure DevOps Project

Azure DevOps Project představuje zjednodušené prostředí, kam můžete přenést váš stávající kód a úložiště Git a kde si můžete vybrat jednu z ukázkových aplikací pro vytvoření kanálu průběžné integrace (CI) a průběžného doručování (CD) do Azure.  Projekt DevOps automaticky vytvoří prostředky Azure, jako je služba Azure SQL Database, vytvoří a nakonfiguruje kanál verze v Azure DevOps zahrnující kanál buildu pro kontinuální integraci, nastaví kanál verze pro průběžné nasazování a pak vytvoří prostředek Azure Application Insights pro účely monitorování.

Vaším úkolem je:

> [!div class="checklist"]
> * Vytvoření projektu Azure DevOps pro aplikaci ASP.NET a službu Azure SQL Database
> * Konfigurace služby Azure DevOps Services a předplatného Azure 
> * Prozkoumání kanálu CI služby Azure DevOps Services
> * Prozkoumání kanálu CD služby Azure DevOps Services
> * Potvrzení změn do služby Azure DevOps Services a automatické nasazení do Azure
> * Připojení k databázi Azure SQL Serveru 
> * Vyčištění prostředků

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-an-azure-devops-project-for-an-aspnet-app-and-azure-sql-database"></a>Vytvoření projektu Azure DevOps pro aplikaci ASP.NET a službu Azure SQL Database

Projekt Azure DevOps vytvoří kanál CI/CD v Azure.  Můžete vytvořit novou organizaci služby **Azure DevOps Services** nebo použít **existující organizaci**.  Azure DevOps Project také vytvoří **prostředky Azure**, jako je služba Azure SQL Database, v **předplatném Azure** podle vašeho výběru.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V levém navigačním panelu zvolte ikonu **Vytvořit prostředek** a pak vyhledejte **Projekt DevOps**.  Zvolte **Vytvořit**.

    ![Zahájení průběžného doručování](_img/azure-devops-project-github/fullbrowser.png)

1. Vyberte **.NET** a pak zvolte **Další**.

1. V části **Zvolte architekturu aplikace** vyberte **ASP.NET**.

1. Vyberte **Přidat databázi** a zvolte **Další**.  

1. Architektura aplikace, kterou jste zvolili v předchozích krocích, určuje typ cíle nasazení služby Azure, který je zde k dispozici.  Vyberte **Další**.

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Konfigurace služby Azure DevOps Services a předplatného Azure

1. Vytvořte **novou** organizaci služby Azure DevOps Services nebo zvolte některou **existující** organizaci.  Zvolte **název** projektu Azure DevOps.  

1. Vyberte své **předplatné Azure**.

1. Volitelně výběrem odkazu **Změnit** zobrazte další nastavení konfigurace Azure a v části **Údaje pro přihlášení k databázovému serveru** identifikujte **uživatelské jméno**.  **Uložte** si **uživatelské jméno** pro další kroky v tomto kurzu.
 
1. Pokud jste provedli předchozí krok, opusťte oblast konfigurace Azure a zvolte **Hotovo**.  Jinak stačí vybrat **Hotovo**.

1. Dokončení procesu bude trvat několik minut.  Po dokončení se na webu Azure Portal načte **řídicí panel projektu** Azure DevOps.  Na **řídicí panel Azure DevOps Project** můžete přejít také přímo z části **Všechny prostředky** na webu **Azure Portal**.  Na pravé straně řídicího panelu vyberte **Procházet** a zobrazte vaši spuštěnou aplikaci.
    
## <a name="examine-the-azure-devops-services-ci-pipeline"></a>Prozkoumání kanálu CI služby Azure DevOps Services

Projekt Azure DevOps ve vaší organizaci služby Azure DevOps Services automaticky nakonfiguruje úplný kanál Azure CI/CD.  Kanál můžete prozkoumat a upravit.  Pomocí následujícího postupu se seznamte s kanálem buildu služby Azure DevOps Services.

1. Přejděte na **řídicí panel Azure DevOps Project**.

1. V **horní** části **řídicího panelu projektu Azure DevOps** vyberte **Kanály sestavení**.  Tento odkaz otevře na nové záložce prohlížeče kanál buildu Azure DevOps Services pro váš nový projekt.

1. Přesuňte kurzor myši napravo od kanálu buildu vedle pole **Stav**. Vyberte **tři tečky**, které se zobrazí.  Tato akce otevře nabídku, ve které můžete provést několik aktivit, jako je **zařazení nového buildu do fronty**, **pozastavení buildu** a **úprava kanálu buildu**.

1. Vyberte **Upravit**.

1. V tomto zobrazení můžete **prozkoumat různé úlohy** pro váš kanál buildu.  Tento build provádí různé úlohy, jako je načtení zdrojových kódů z úložiště Git Azure DevOps Services, obnovení závislostí a publikování výstupů používaných pro nasazení.

1. V horní části kanálu buildu vyberte **název kanálu buildu**.

1. Změňte **název** kanálu buildu na něco výstižnějšího.  Vyberte **Uložit a zařadit do fronty** a pak vyberte **Uložit**.

1. Pod názvem kanálu buildu vyberte **Historie**.  Zobrazí se protokol auditu nedávno provedených změn sestavení.  Azure DevOps Services uchovává informace o všech změnách provedených v kanálu buildu a umožňuje porovnávat verze.

1. Vyberte **Triggery**.  Projekt Azure DevOps automaticky vytvořil trigger CI a každé potvrzení v úložišti spustí nový build.  Volitelně můžete zvolit, které větve se do procesu CI zahrnou nebo se z něj vyloučí.

1. Vyberte **Uchování**.  V závislosti na vašem scénáři můžete určit zásady pro zachování nebo odebrání určitého počtu sestavení.

## <a name="examine-the-azure-devops-services-cd-pipeline"></a>Prozkoumání kanálu CD služby Azure DevOps Services

Projekt Azure DevOps automaticky vytvoří a nakonfiguruje potřebné kroky pro nasazení z organizace služby Azure DevOps Services do předplatného Azure.  Mezi tyto kroky patří konfigurace připojení ke službě Azure za účelem ověření služby Azure DevOps Services vůči vašemu předplatnému Azure.  Automatizace také vytvoří definici verze Azure DevOps Services, která zajišťuje průběžné nasazování do Azure.  Pomocí níže uvedeného postupu dále prozkoumejte definici verze služby Azure DevOps Services.

1. Vyberte **Sestavení a vydání** a zvolte **Verze**.  Projekt Azure DevOps vytvořil kanál verze Azure DevOps Services pro správu nasazení do Azure.

1. Na levé straně prohlížeče vyberte **tři tečky** vedle kanálu verze a pak zvolte **Upravit**.

1. Kanál verze obsahuje **kanál**, který definuje proces vydání.  V části **Artefakty** vyberte **Zahodit**.  Kanál buildu, který jste prozkoumali v předchozích krocích, vytvoří výstup pro artefakt. 

1. Napravo od ikony **Zahodit** vyberte **Trigger průběžného nasazování** (**ikona** blesku).  Tento kanál verze obsahuje povolený trigger průběžného nasazování.  Tento trigger spustí nasazení pokaždé, když bude k dispozici nový artefakt sestavení.  Volitelně můžete trigger zakázat, aby pak vaše nasazení vyžadovala ruční spuštění. 

1. Projekt Azure DevOps nastavil náhodné heslo SQL a toto heslo použil pro kanál verze.  Na levé straně prohlížeče vyberte **Proměnné**. 

1. **Tento krok proveďte pouze v případě, že jste změnili heslo pro SQL Server.**  Zobrazí se jedna proměnná **Heslo**.  Napravo od textového pole **Hodnota** vyberte ikonu **zámku**.  **Zadejte** nové heslo a pak vyberte **Uložit**.

1. Na levé straně prohlížeče vyberte **Úlohy** a pak zvolte vaše **prostředí**.  

1. Úlohy jsou aktivity, které se provádí ve vašem procesu nasazení a které se seskupují do **fází**.  Tento kanál verze obsahuje jednu fázi.  Tato fáze obsahuje úlohy **nasazení služby Azure App Service** a **nasazení služby Azure SQL Database**.

1. Vyberte úlohu **spuštění Azure SQL** a prozkoumejte různé vlastnosti použité pro nasazení SQL.  V části **Balíček pro nasazení** si všimněte, že úloha používá **soubor DACPAC SQL**.

1. Na pravé straně prohlížeče vyberte **Zobrazit verze**.  Toto zobrazení ukazuje historii vydaných verzí.

1. Vyberte **tři tečky** vedle některé z vydaných verzí a zvolte **Otevřít**.  Toto zobrazení obsahuje několik nabídek, které můžete prozkoumat, například **souhrn verze**, **související pracovní položky** a **testy**.

1. Vyberte **Potvrzení**.  Toto zobrazení ukazuje potvrzení kódu související s konkrétním nasazením. Můžete porovnat vydané verze a zobrazit rozdíly v potvrzeních jednotlivých nasazení.

1. Vyberte **Protokoly**.  Protokoly obsahují užitečné informace o procesu nasazení.  Můžete je zobrazit během nasazení i po nich.

## <a name="commit-changes-to-azure-devops-services-and-automatically-deploy-to-azure"></a>Potvrzení změn do služby Azure DevOps Services a automatické nasazení do Azure 

 > [!NOTE]
 > Následujícím postupem se provede test kanálu CI/CD prostřednictvím prosté změny textu.  Volitelně můžete u tabulky provést změnu schématu SQL Serveru a otestovat proces nasazení SQL.

Teď jste připraveni při práci na vaší aplikaci spolupracovat s týmem s využitím procesu CI/CD, který automaticky nasazuje nejnovější práci na web.  Každá změna v úložišti Git Azure DevOps Services spustí build ve službě Azure DevOps Services a kanál průběžného nasazování služby Azure DevOps Services provede nasazení do Azure.  K potvrzení změn v úložišti můžete použít následující postup nebo jiné techniky.  Změny kódu spustí proces CI/CD a vaše nové změny se automaticky nasadí do Azure.

1. V nabídce Azure DevOps Services vyberte **Kód** a přejděte do svého úložiště.

1. Přejděte do adresáře **SampleWebApplication\Views\Home**, vyberte **tři tečky** vedle souboru **Index.cshtml** a pak zvolte **Upravit**.

1. Proveďte změnu souboru například úpravou textu uvnitř některé ze **značek div**.  V pravém horním rohu vyberte **Potvrdit**.  Znovu vyberte **Potvrdit**, aby se změna nasdílela. 

1. Za chvíli se **spustí build ve službě Azure DevOps Services** a pak se provede vydání za účelem nasazení změn.  **Stav buildu** můžete monitorovat na řídicím panelu projektu DevOps nebo v prohlížeči s vaší organizací služby Azure DevOps Services.

1. Po dokončení vydání **aktualizujte aplikaci** v prohlížeči a ověřte, že se zobrazí provedené změny.

## <a name="connect-to-the-azure-sql-server-database"></a>Připojení k databázi Azure SQL Serveru

Pro připojení ke službě Azure SQL Database potřebujete odpovídající oprávnění.

1. Na řídicím panelu Azure DevOps Project vyberte **SQL Database** a přejděte na stránku pro správu služby SQL Database.
   
1. Vyberte **Nastavit bránu firewall serveru** a pak **+ Přidat IP adresu klienta**.  

1. Vyberte **Uložit**.  IP adresa vašeho klienta má teď povolený přístup k **prostředku Azure SQL Serveru**.

1. Vraťte se do okna **SQL Database**. 

1. Na pravé straně obrazovky vyberte **Název serveru** a přejděte na stránku konfigurace **SQL Serveru**.

1. Vyberte **Resetovat heslo**, zadejte heslo pro **Přihlášení správce SQL Serveru** a pak vyberte **Uložit**.  **Uložte** si toto heslo pro další kroky v tomto kurzu.

1. Teď se můžete volitelně připojit k Azure SQL Serveru a službě Azure SQL Database pomocí klientských nástrojů, jako je aplikace **SQL Server Management Studio** nebo sada **Visual Studio**.  Pro připojení použijte vlastnost **Název serveru**.

   Pokud jste při počáteční konfiguraci služby DevOps Project nezměnili uživatelské jméno databáze, vaše uživatelské jméno je místní část vaší e-mailové adresy.  Pokud je vaše e-mailová adresa například johndoe@microsoft.com, vaše uživatelské jméno je johndoe.

 > [!NOTE]
 > Pokud si změníte heslo pro přihlášení k SQL, musíte změnit heslo v proměnné kanálu verze služby Azure DevOps Services, jak je popsáno v oddílu **Prozkoumání kanálu CD služby Azure DevOps Services**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

 > [!NOTE]
 > Následujícím postupem se trvale odstraní prostředky.  Tuto funkci použijte pouze po důkladném prostudování výzev.

Pokud provádíte testování, můžete vyčistit prostředky a zamezit tak nabíhání poplatků.  Pokud už je nepotřebujete, můžete službu Azure SQL Database a související prostředky vytvořené v tomto kurzu odstranit pomocí funkce **Odstranit** na řídicím panelu Azure DevOps Project.  **Buďte opatrní**, protože funkce odstranění zničí všechna data vytvořená projektem Azure DevOps v Azure i ve službě Azure DevOps Services. Po odstranění dat je nebudete moct načíst.

1. Na webu **Azure Portal** přejděte do služby **Azure DevOps Project**.
2. Na **pravé horní** straně řídicího panelu vyberte **Odstranit**.  Po přečtení výzvy vyberte **Ano** a **trvale odstraňte** prostředky.

## <a name="next-steps"></a>Další kroky

Tyto kanály buildu a verze můžete volitelně upravit tak, aby splňovaly požadavky vašeho týmu. Tento vzor CI/CD můžete také použít jako šablonu pro své další projekty.  Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření projektu Azure DevOps pro aplikaci ASP.NET a službu Azure SQL Database
> * Konfigurace služby Azure DevOps Services a předplatného Azure 
> * Prozkoumání kanálu CI služby Azure DevOps Services
> * Prozkoumání kanálu CD služby Azure DevOps Services
> * Potvrzení změn do služby Azure DevOps Services a automatické nasazení do Azure
> * Připojení k databázi Azure SQL Serveru 
> * Vyčištění prostředků

Další informace o kanálu Azure najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Přizpůsobení procesu CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Videa

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]
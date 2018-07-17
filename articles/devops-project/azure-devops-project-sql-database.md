---
title: Nasazení aplikace ASP.NET a služby Azure SQL Database pomocí služby Azure DevOps Project | Kurz VSTS
description: DevOps Project usnadňuje začátek práce v Azure. Projekt Azure DevOps umožňuje snadno a v několika rychlých krocích nasadit aplikaci ASP.NET se službou Azure SQL Database.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: ba15a57f92ad58a46732444890d9e670fcc75cf3
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967322"
---
# <a name="tutorial--deploy-your-aspnet-app-and-azure-sql-database-with-the-azure-devops-project"></a>Kurz: Nasazení aplikace ASP.NET a služby Azure SQL Database pomocí služby Azure DevOps Project

Azure DevOps Project představuje zjednodušené prostředí, kam můžete přenést váš stávající kód a úložiště Git a kde si můžete vybrat jednu z ukázkových aplikací pro vytvoření kanálu průběžné integrace (CI) a průběžného doručování (CD) do Azure.  Projekt DevOps automaticky vytvoří prostředky Azure, jako je služba Azure SQL Database, vytvoří a nakonfiguruje kanál verze ve VSTS obsahující definici sestavení pro průběžnou integraci, nastaví definici verze pro průběžné nasazování a pak vytvoří prostředek Azure Application Insights pro účely monitorování.

Vaším úkolem je:

> [!div class="checklist"]
> * Vytvoření projektu Azure DevOps pro aplikaci ASP.NET a službu Azure SQL Database
> * Konfigurace VSTS a předplatného Azure 
> * Prozkoumání definice sestavení CI VSTS
> * Prozkoumání definice nástroje Release Management pro CD VSTS
> * Potvrzení změn ve VSTS a automatické nasazení do Azure
> * Připojení k databázi Azure SQL Serveru 
> * Vyčištění prostředků

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-an-azure-devops-project-for-an-aspnet-app-and-azure-sql-database"></a>Vytvoření projektu Azure DevOps pro aplikaci ASP.NET a službu Azure SQL Database

Azure DevOps Project vytvoří kanál CI/CD ve VSTS.  Můžete vytvořit **nový účet VSTS** nebo použít **existující účet**.  Azure DevOps Project také vytvoří **prostředky Azure**, jako je služba Azure SQL Database, v **předplatném Azure** podle vašeho výběru.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V levém navigačním panelu zvolte ikonu **Vytvořit prostředek** a pak vyhledejte **Projekt DevOps**.  Zvolte **Vytvořit**.

    ![Zahájení průběžného doručování](_img/azure-devops-project-github/fullbrowser.png)

1. Vyberte **.NET** a pak zvolte **Další**.

1. V části **Zvolte architekturu aplikace** vyberte **ASP.NET**.

1. Vyberte **Přidat databázi** a zvolte **Další**.  

1. Architektura aplikace, kterou jste zvolili v předchozích krocích, určuje typ cíle nasazení služby Azure, který je zde k dispozici.  Vyberte **Next** (Další).

## <a name="configure-vsts-and-an-azure-subscription"></a>Konfigurace VSTS a předplatného Azure

1. Vytvořte **nový** účet VSTS nebo zvolte **existující** účet.  Zvolte **název** pro váš projekt VSTS.  

1. Vyberte své **předplatné Azure**.

1. Volitelně výběrem odkazu **Změnit** zobrazte další nastavení konfigurace Azure a v části **Údaje pro přihlášení k databázovému serveru** identifikujte **uživatelské jméno**.  **Uložte** si **uživatelské jméno** pro další kroky v tomto kurzu.
 
1. Pokud jste provedli předchozí krok, opusťte oblast konfigurace Azure a zvolte **Hotovo**.  Jinak stačí vybrat **Hotovo**.

1. Dokončení procesu bude trvat několik minut.  Po dokončení se na webu Azure Portal načte **řídicí panel projektu** Azure DevOps.  Na **řídicí panel Azure DevOps Project** můžete přejít také přímo z části **Všechny prostředky** na webu **Azure Portal**.  Na pravé straně řídicího panelu vyberte **Procházet** a zobrazte vaši spuštěnou aplikaci.
    
## <a name="examine-the-vsts-ci-build-definition"></a>Prozkoumání definice sestavení CI VSTS

Azure DevOps Project ve vašem účtu VSTS automaticky nakonfiguruje úplný kanál CI/CD VSTS.  Kanál můžete prozkoumat a upravit.  Postupujte podle následujících kroků a seznamte se s definicí sestavení VSTS.

1. Přejděte na **řídicí panel Azure DevOps Project**.

1. V **horní** části **řídicího panelu projektu Azure DevOps** vyberte **Kanály sestavení**.  Tento odkaz na nové kartě prohlížeče otevře definici sestavení VSTS pro váš nový projekt.

1. Přesuňte kurzor myši napravo od definice sestavení vedle pole **Stav**. Vyberte **tři tečky**, které se zobrazí.  Tato akce otevře nabídku, ze které můžete provést několik aktivit, jako je **zařazení nového sestavení do fronty**, **pozastavení sestavení** a **úprava definice sestavení**.

1. Vyberte **Upravit**.

1. V tomto zobrazení můžete **prozkoumat různé úlohy** pro vaši definici sestavení.  Sestavení provádí různé úlohy, jako je načtení zdrojů z úložiště VSTS Git, obnovení závislostí a publikování výstupů používaných pro nasazení.

1. V horní části definice sestavení vyberte **název definice sestavení**.

1. Změňte **název** vaší definice sestavení na něco výstižnějšího.  Vyberte **Uložit a zařadit do fronty** a pak vyberte **Uložit**.

1. Pod názvem vaší definice sestavení vyberte **Historie**.  Zobrazí se protokol auditu nedávno provedených změn sestavení.  VSTS uchovává informace o všech změnách definice sestavení a umožňuje porovnání verzí.

1. Vyberte **Triggery**.  Projekt Azure DevOps automaticky vytvořil trigger CI a každé potvrzení v úložišti začíná novým sestavením.  Volitelně můžete zvolit, které větve se do procesu CI zahrnou nebo se z něj vyloučí.

1. Vyberte **Uchování**.  V závislosti na vašem scénáři můžete určit zásady pro zachování nebo odebrání určitého počtu sestavení.

## <a name="examine-the-vsts-cd-release-management-definition"></a>Prozkoumání definice nástroje Release Management pro CD VSTS

Azure DevOps Project automaticky vytvoří a nakonfiguruje potřebné kroky pro nasazení z vašeho účtu VSTS do předplatného Azure.  Mezi tyto kroky patří konfigurace připojení ke službě Azure za účelem ověření VSTS ve vašem předplatném Azure.  Automatizace také vytvoří definici verze VSTS a tato verze zajišťuje průběžné nasazování do Azure.  Postupujte podle následujících kroků a prozkoumejte blíže definici sestavení VSTS.

1. Vyberte **Sestavení a vydání** a zvolte **Verze**.  Projekt Azure DevOps vytvořil definici verze VSTS pro správu nasazení do Azure.

1. Na levé straně prohlížeče vyberte **tři tečky** vedle vaší definice verze a pak zvolte **Upravit**.

1. Definice verze obsahuje **kanál**, který definuje proces vydání.  V části **Artefakty** vyberte **Zahodit**.  Definice sestavení, kterou jste zkoumali v předchozích krocích, vygeneruje výstup, který se použije pro artefakt. 

1. Napravo od ikony **Zahodit** vyberte **Trigger průběžného nasazování** (**ikona** blesku).  Tato definice verze má povolený trigger CD.  Tento trigger spustí nasazení pokaždé, když bude k dispozici nový artefakt sestavení.  Volitelně můžete trigger zakázat, aby pak vaše nasazení vyžadovala ruční spuštění. 

1. Projekt Azure DevOps nastavil náhodné heslo SQL a toto heslo použil pro definici verze.  Na levé straně prohlížeče vyberte **Proměnné**. 

1. **Tento krok proveďte pouze v případě, že jste změnili heslo pro SQL Server.**  Zobrazí se jedna proměnná **Heslo**.  Napravo od textového pole **Hodnota** vyberte ikonu **zámku**.  **Zadejte** nové heslo a pak vyberte **Uložit**.

1. Na levé straně prohlížeče vyberte **Úlohy** a pak zvolte vaše **prostředí**.  

1. Úlohy jsou aktivity, které se provádí ve vašem procesu nasazení a které se seskupují do **fází**.  Tato definice verze zahrnuje jednu fázi.  Tato fáze obsahuje úlohy **nasazení služby Azure App Service** a **nasazení služby Azure SQL Database**.

1. Vyberte úlohu **spuštění Azure SQL** a prozkoumejte různé vlastnosti použité pro nasazení SQL.  V části **Balíček pro nasazení** si všimněte, že úloha používá **soubor DACPAC SQL**.

1. Na pravé straně prohlížeče vyberte **Zobrazit verze**.  Toto zobrazení ukazuje historii vydaných verzí.

1. Vyberte **tři tečky** vedle některé z vydaných verzí a zvolte **Otevřít**.  Toto zobrazení obsahuje několik nabídek, které můžete prozkoumat, například **souhrn verze**, **související pracovní položky** a **testy**.

1. Vyberte **Potvrzení**.  Toto zobrazení ukazuje potvrzení kódu související s konkrétním nasazením. Můžete porovnat vydané verze a zobrazit rozdíly v potvrzeních jednotlivých nasazení.

1. Vyberte **Protokoly**.  Protokoly obsahují užitečné informace o procesu nasazení.  Můžete je zobrazit během nasazení i po nich.

## <a name="commit-changes-to-vsts-and-automatically-deploy-to-azure"></a>Potvrzení změn ve VSTS a automatické nasazení do Azure 

 > [!NOTE]
 > Následujícím postupem se provede test kanálu CI/CD prostřednictvím prosté změny textu.  Volitelně můžete u tabulky provést změnu schématu SQL Serveru a otestovat proces nasazení SQL.

Teď jste připraveni při práci na vaší aplikaci spolupracovat s týmem s využitím procesu CI/CD, který automaticky nasazuje nejnovější práci na web.  Každá změna v úložišti VSTS Git spustí sestavení ve VSTS a definice nástroje Release Management pro VSTS provede nasazení do Azure.  K potvrzení změn v úložišti můžete použít následující postup nebo jiné techniky.  Změny kódu spustí proces CI/CD a vaše nové změny se automaticky nasadí do Azure.

1. V nabídce VSTS vyberte **Kód** a přejděte do svého úložiště.

1. Přejděte do adresáře **SampleWebApplication\Views\Home**, vyberte **tři tečky** vedle souboru **Index.cshtml** a pak zvolte **Upravit**.

1. Proveďte změnu souboru například úpravou textu uvnitř některé ze **značek div**.  V pravém horním rohu vyberte **Potvrdit**.  Znovu vyberte **Potvrdit**, aby se změna nasdílela. 

1. Za chvíli se **spustí sestavení ve VSTS** a pak se provede vydání za účelem nasazení změn.  **Stav sestavení** můžete monitorovat na řídicím panelu projektu DevOps nebo ve vašem účtu VSTS v prohlížeči.

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
 > Pokud změníte své heslo pro přihlášení k SQL, je potřeba změnit heslo v proměnné definice verze VSTS, jak je popsáno v části **Prozkoumání definice nástroje Release Management pro CD VSTS**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

 > [!NOTE]
 > Následujícím postupem se trvale odstraní prostředky.  Tuto funkci použijte pouze po důkladném prostudování výzev.

Pokud provádíte testování, můžete vyčistit prostředky a zamezit tak nabíhání poplatků.  Pokud už je nepotřebujete, můžete službu Azure SQL Database a související prostředky vytvořené v tomto kurzu odstranit pomocí funkce **Odstranit** na řídicím panelu Azure DevOps Project.  **Buďte opatrní**, protože funkce odstranění zničí všechna data vytvořená službou Azure DevOps Project v Azure i ve VSTS. Jakmile se data odstraní, nebudete je moct načíst.

1. Na webu **Azure Portal** přejděte do služby **Azure DevOps Project**.
2. Na **pravé horní** straně řídicího panelu vyberte **Odstranit**.  Po přečtení výzvy vyberte **Ano** a **trvale odstraňte** prostředky.

## <a name="next-steps"></a>Další kroky

Tyto definice sestavení a verze můžete volitelně upravit tak, aby splňovaly požadavky vašeho týmu. Tento vzor CI/CD můžete také použít jako šablonu pro své další projekty.  Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření projektu Azure DevOps pro aplikaci ASP.NET a službu Azure SQL Database
> * Konfigurace VSTS a předplatného Azure 
> * Prozkoumání definice sestavení CI VSTS
> * Prozkoumání definice nástroje Release Management pro CD VSTS
> * Potvrzení změn ve VSTS a automatické nasazení do Azure
> * Připojení k databázi Azure SQL Serveru 
> * Vyčištění prostředků

Další informace o kanálu VSTS najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Přizpůsobení procesu CD](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Videa

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]
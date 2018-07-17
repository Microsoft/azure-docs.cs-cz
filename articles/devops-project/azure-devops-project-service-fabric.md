---
title: Nasazení aplikace ASP.NET Core do Azure Service Fabric pomocí služby Azure DevOps Project | Kurz VSTS
description: DevOps Project usnadňuje začátek práce v Azure. Projekt Azure DevOps umožňuje snadno a v několika rychlých krocích nasadit aplikaci ASP.NET Core s využitím Azure Service Fabric.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 2fd1fc968eb6b61d7378dbc0efa48f2f7eb2aa54
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967329"
---
# <a name="tutorial--deploy-your-aspnet-core-app-to-azure-service-fabric-with-the-azure-devops-project"></a>Kurz: Nasazení aplikace ASP.NET Core do Azure Service Fabric pomocí služby Azure DevOps Project

Azure DevOps Project představuje zjednodušené prostředí, kam můžete přenést váš stávající kód a úložiště Git a kde si můžete vybrat jednu z ukázkových aplikací pro vytvoření kanálu průběžné integrace (CI) a průběžného doručování (CD) do Azure.  Projekt DevOps automaticky vytvoří prostředky Azure, jako je Azure Service Fabric, vytvoří a nakonfiguruje kanál verze ve VSTS obsahující definici sestavení pro průběžnou integraci, nastaví definici verze pro průběžné nasazování a pak vytvoří prostředek Azure Application Insights pro účely monitorování.

Vaším úkolem je:

> [!div class="checklist"]
> * Vytvoření projektu Azure DevOps pro aplikaci ASP.NET Core a Service Fabric
> * Konfigurace VSTS a předplatného Azure 
> * Prozkoumání definice sestavení CI VSTS
> * Prozkoumání definice nástroje Release Management pro CD VSTS
> * Potvrzení změn ve VSTS a automatické nasazení do Azure
> * Vyčištění prostředků

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-an-azure-devops-project-for-an-aspnet-core-app-and-service-fabric"></a>Vytvoření projektu Azure DevOps pro aplikaci ASP.NET Core a Service Fabric

Azure DevOps Project vytvoří kanál CI/CD ve VSTS.  Můžete vytvořit **nový účet VSTS** nebo použít **existující účet**.  Azure DevOps Project také vytvoří **prostředky Azure**, jako je cluster Service Fabric, v **předplatném Azure** podle vašeho výběru.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V levém navigačním panelu zvolte ikonu **Vytvořit prostředek** a pak vyhledejte **Projekt DevOps**.  Zvolte **Vytvořit**.

    ![Zahájení průběžného doručování](_img/azure-devops-project-service-fabric/fullbrowser.png)

1. Vyberte **.NET** a pak zvolte **Další**.

1. V části **Zvolte architekturu aplikace** vyberte **ASP.NET Core** a pak vyberte **Další**.

1. Vyberte **Cluster Service Fabric** a pak zvolte **Další**.  

## <a name="configure-vsts-and-an-azure-subscription"></a>Konfigurace VSTS a předplatného Azure

1. Vytvořte **nový** účet VSTS nebo zvolte **existující** účet.  Zvolte **název** pro váš projekt VSTS.  

1. Vyberte své **předplatné Azure**.

1. Výběrem odkazu **Změnit** zobrazte další nastavení konfigurace Azure a určete **velikost virtuálního počítače uzlu** a **operační systém** pro **cluster Service Fabric**.  Tady máte k dispozici různé možnost konfigurace typu a umístění služeb Azure.
 
1. Opusťte oblast konfigurace Azure a zvolte **Hotovo**.

1. Dokončení procesu bude trvat několik minut.  Ukázková aplikace ASP.NET Core se nastaví v úložišti ve vašem účtu VSTS, vytvoří se cluster Service Fabric, spustí se sestavení a vydání a vaše aplikace se nasadí do Azure.  

    Po dokončení se na webu Azure Portal načte **řídicí panel projektu** Azure DevOps.  Na **řídicí panel Azure DevOps Project** můžete přejít také přímo z části **Všechny prostředky** na webu **Azure Portal**.  

    Tento řídicí panel poskytuje přehled o vašem **úložišti kódu** VSTS, **kanálu CI/CD VSTS** a **clusteru Service Fabric**.  Ve VSTS můžete dále nakonfigurovat další možnosti.  Na pravé straně řídicího panelu vyberte **Procházet** a zobrazte vaši spuštěnou aplikaci.

## <a name="examine-the-vsts-ci-build-definition"></a>Prozkoumání definice sestavení CI VSTS

Azure DevOps Project ve vašem účtu VSTS automaticky nakonfiguruje úplný kanál CI/CD VSTS.  Kanál můžete prozkoumat a upravit.  Postupujte podle následujících kroků a seznamte se s definicí sestavení VSTS.

1. Přejděte na **řídicí panel Azure DevOps Project**.

1. V **horní** části **řídicího panelu projektu Azure DevOps** vyberte **Kanály sestavení**.  Tento odkaz na nové kartě prohlížeče otevře definici sestavení VSTS pro váš nový projekt.

1. Přesuňte kurzor myši napravo od definice sestavení vedle pole **Stav**. Vyberte **tři tečky**, které se zobrazí.  Tato akce otevře nabídku, ze které můžete spustit několik aktivit, jako je **zařazení nového sestavení do fronty**, **pozastavení sestavení** a **úprava definice sestavení**.

1. Vyberte **Upravit**.

1. V tomto zobrazení můžete **prozkoumat různé úlohy** pro vaši definici sestavení.  Sestavení provádí různé úlohy, jako je načtení zdrojů z úložiště VSTS Git, obnovení závislostí a publikování výstupů používaných pro nasazení.

1. V horní části definice sestavení vyberte **název definice sestavení**.

1. Změňte **název** vaší definice sestavení na něco výstižnějšího.  Vyberte **Uložit a zařadit do fronty** a pak vyberte **Uložit**.

1. Pod názvem vaší definice sestavení vyberte **Historie**.  Zobrazí se protokol auditu nedávno provedených změn sestavení.  VSTS uchovává informace o všech změnách definice sestavení a umožňuje porovnání verzí.

1. Vyberte **Triggery**.  Projekt Azure DevOps automaticky vytvořil trigger CI a každé potvrzení v úložišti spustí nové sestavení.  Volitelně můžete zvolit, které větve se do procesu CI zahrnou nebo se z něj vyloučí.

1. Vyberte **Uchování**.  V závislosti na vašem scénáři můžete určit zásady pro zachování nebo odebrání určitého počtu sestavení.

## <a name="examine-the-vsts-cd-release-management-definition"></a>Prozkoumání definice nástroje Release Management pro CD VSTS

Azure DevOps Project automaticky vytvoří a nakonfiguruje potřebné kroky pro nasazení z vašeho účtu VSTS do předplatného Azure.  Mezi tyto kroky patří konfigurace připojení ke službě Azure za účelem ověření VSTS ve vašem předplatném Azure.  Automatizace také vytvoří definici verze VSTS a tato verze zajišťuje průběžné nasazování do Azure.  Postupujte podle následujících kroků a prozkoumejte blíže definici sestavení VSTS.

1. Vyberte **Sestavení a vydání** a zvolte **Verze**.  Projekt Azure DevOps vytvořil definici verze VSTS pro správu nasazení do Azure.

1. Na levé straně prohlížeče vyberte **tři tečky** vedle vaší definice verze a pak zvolte **Upravit**.

1. Definice verze obsahuje **kanál**, který definuje proces vydání.  V části **Artefakty** vyberte **Zahodit**.  Definice sestavení, kterou jste zkoumali v předchozích krocích, vygeneruje výstup, který se použije pro artefakt. 

1. Napravo od ikony **Zahodit** vyberte **Trigger průběžného nasazování** (**ikona** blesku).  Tato definice verze má povolený trigger CD.  Tento trigger spustí nasazení pokaždé, když bude k dispozici nový artefakt sestavení.  Volitelně můžete trigger zakázat, aby pak vaše nasazení vyžadovala ruční spuštění. 

1. Na pravé straně prohlížeče vyberte **Zobrazit verze**.  Toto zobrazení ukazuje historii vydaných verzí.

1. Vyberte **tři tečky** vedle některé z vydaných verzí a zvolte **Otevřít**.  Toto zobrazení obsahuje několik nabídek, které můžete prozkoumat, například **souhrn verze**, **související pracovní položky** a **testy**.

1. Vyberte **Potvrzení**.  Toto zobrazení ukazuje potvrzení kódu související s konkrétním nasazením. Můžete porovnat vydané verze a zobrazit rozdíly v potvrzeních jednotlivých nasazení.

1. Vyberte **Protokoly**.  Protokoly obsahují užitečné informace o procesu nasazení.  Můžete je zobrazit během nasazení i po nich.

## <a name="commit-changes-to-vsts-and-automatically-deploy-to-azure"></a>Potvrzení změn ve VSTS a automatické nasazení do Azure 

 > [!NOTE]
 > Následujícím postupem se provede test kanálu CI/CD prostřednictvím prosté změny textu ve vaší webové aplikaci.

Teď jste připraveni při práci na vaší aplikaci spolupracovat s týmem s využitím procesu CI/CD, který automaticky nasazuje nejnovější práci na web.  Každá změna v úložišti VSTS Git spustí sestavení ve VSTS a definice nástroje Release Management pro VSTS nasadí provedené změny do Azure.  K potvrzení změn v úložišti můžete použít následující postup nebo jiné techniky.  Například můžete **naklonovat** úložiště Git do svého oblíbeného nástroje nebo integrovaného vývojového prostředí (IDE) a pak do tohoto úložiště nasdílet změny.

1. V nabídce VSTS vyberte **Kód**, pak **Soubory** a přejděte do svého úložiště.
1. Přejděte do adresáře **Views\Home**, vyberte **tři tečky** vedle souboru **Index.cshtml** a pak zvolte **Upravit**.

1. Proveďte změnu souboru například úpravou textu uvnitř některé ze **značek div**.  V pravém horním rohu vyberte **Potvrdit**.  Znovu vyberte **Potvrdit**, aby se změna nasdílela. 

1. Za chvíli se **spustí sestavení ve VSTS** a pak se provede vydání za účelem nasazení změn.  **Stav sestavení** můžete monitorovat na řídicím panelu projektu DevOps nebo ve vašem účtu VSTS v prohlížeči.

1. Po dokončení vydání **aktualizujte aplikaci** v prohlížeči a ověřte, že se zobrazí provedené změny.

## <a name="clean-up-resources"></a>Vyčištění prostředků

 > [!NOTE]
 > Následujícím postupem se trvale odstraní prostředky.  Tuto funkci použijte pouze po důkladném prostudování výzev.

Pokud provádíte testování, můžete vyčistit prostředky a zamezit tak nabíhání poplatků.  Pokud už je nepotřebujete, můžete cluster Azure Service Fabric a související prostředky vytvořené v tomto kurzu odstranit pomocí funkce **Odstranit** na řídicím panelu Azure DevOps Project.  **Buďte opatrní**, protože funkce odstranění zničí všechna data vytvořená službou Azure DevOps Project v Azure i ve VSTS. Jakmile se data odstraní, nebudete je moct načíst.

1. Na webu **Azure Portal** přejděte do služby **Azure DevOps Project**.
2. Na **pravé horní** straně řídicího panelu vyberte **Odstranit**.  Po přečtení výzvy vyberte **Ano** a **trvale odstraňte** prostředky.

## <a name="next-steps"></a>Další kroky

Tyto definice sestavení a verze můžete volitelně upravit tak, aby splňovaly požadavky vašeho týmu. Tento vzor CI/CD můžete také použít jako šablonu pro své další projekty.  Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření projektu Azure DevOps pro aplikaci ASP.NET Core a Service Fabric
> * Konfigurace VSTS a předplatného Azure 
> * Prozkoumání definice sestavení CI VSTS
> * Prozkoumání definice nástroje Release Management pro CD VSTS
> * Potvrzení změn ve VSTS a automatické nasazení do Azure
> * Vyčištění prostředků

Další informace o Service Fabric a mikroslužbách najdete v následujícím tématu:

> [!div class="nextstepaction"]
> [Použití přístupu založeného na mikroslužbách při vytváření aplikací](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)

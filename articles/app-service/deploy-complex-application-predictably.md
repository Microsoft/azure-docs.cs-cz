---
title: Prediktivní nasazení aplikací s využitím ARM
description: Naučte se, jak nasadit několik aplikací Azure App Service jako jednu jednotku a předvídatelný způsob pomocí šablon Azure Resource managementu a skriptování v prostředí PowerShell.
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 01/06/2016
ms.custom: seodec18
ms.openlocfilehash: 8742b590af89954cb8480e5282827bcd5228673b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101095842"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Prediktivní zřizování a nasazování mikroslužeb v Azure
V tomto kurzu se dozvíte, jak zřídit a nasadit aplikaci skládající se z [mikroslužeb](https://en.wikipedia.org/wiki/Microservices) v [Azure App Service](https://azure.microsoft.com/services/app-service/) jako jediná jednotka a předvídatelným způsobem pomocí šablon skupin prostředků JSON a skriptů prostředí PowerShell. 

Při zřizování a nasazování vysoce škálovatelných aplikací, které se skládají z vysoce oddělených mikroslužeb, je zásadní pro úspěch a předvídatelnost. [Azure App Service](https://azure.microsoft.com/services/app-service/) vám umožní vytvářet mikroslužby, které zahrnují Web Apps, mobilní back-end a aplikace API. [Azure Resource Manager](../azure-resource-manager/management/overview.md) vám umožní spravovat všechny mikroslužby jako jednotku spolu se závislostmi prostředků, jako je například nastavení databáze a správy zdrojů. Nyní můžete tuto aplikaci nasadit také pomocí šablon JSON a jednoduchého skriptování prostředí PowerShell. 

## <a name="what-you-will-do"></a>Co budete dělat
V tomto kurzu nasadíte aplikaci, která zahrnuje:

* Dvě aplikace App Service (tj. dvě mikroslužby)
* SQL Database back-endu
* Nastavení aplikace, připojovací řetězce a Správa zdrojového kódu
* Application Insights, výstrahy, nastavení automatického škálování

## <a name="tools-you-will-use"></a>Nástroje, které budete používat
V tomto kurzu budete používat následující nástroje. Vzhledem k tomu, že není vyčerpávající diskuze na nástrojích, mám na začátku celý scénář a stačí vám poskytnout stručný úvod do každého a kde najdete další informace. 

### <a name="azure-resource-manager-templates-json"></a>Šablony Azure Resource Manager (JSON)
Pokaždé, když vytvoříte aplikaci v Azure App Service, například Azure Resource Manager používá šablonu JSON k vytvoření celé skupiny prostředků s prostředky komponent. Složitá šablona z [Azure Marketplace](../marketplace/index.yml) může zahrnovat databázi, účty úložiště, plán App Service, samotnou aplikaci, pravidla upozornění, nastavení aplikace, nastavení automatického škálování a další, a všechny tyto šablony jsou k dispozici prostřednictvím PowerShellu. Další informace o šablonách Azure Resource Manager najdete v tématu [vytváření šablon Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) .

### <a name="azure-sdk-26-for-visual-studio"></a>Azure SDK 2,6 pro Visual Studio
Nejnovější sada SDK obsahuje vylepšení podpory šablon Správce prostředků v editoru JSON. Tuto možnost můžete použít k rychlému vytvoření šablony skupiny prostředků od začátku nebo otevření stávající šablony JSON (například stažené šablony galerie) pro úpravu, naplnění souboru parametrů a dokonce nasazení skupiny prostředků přímo z řešení skupiny prostředků Azure.

Další informace najdete v tématu [sada Azure SDK 2,6 pro sadu Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Azure PowerShell 0.8.0 nebo novější
Počínaje verzí 0.8.0 zahrnuje instalace Azure PowerShell kromě modulu Azure také modul Azure Resource Manager. Tento nový modul vám umožní vytvářet skripty pro nasazení skupin prostředků.

Další informace najdete v tématu [použití Azure PowerShell s Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md)

### <a name="azure-resource-explorer"></a>Průzkumník prostředků Azure
Tento [Nástroj pro náhled](https://resources.azure.com) umožňuje prozkoumat definice JSON všech skupin prostředků v rámci vašeho předplatného a jednotlivých prostředků. V nástroji můžete upravit definice JSON prostředku, odstranit celou hierarchii prostředků a vytvořit nové prostředky.  Informace, které jsou v tomto nástroji snadno dostupné, jsou velmi užitečné pro vytváření šablon, protože vám ukáže, jaké vlastnosti je potřeba nastavit pro konkrétní typ prostředku, správné hodnoty atd. Můžete dokonce vytvořit skupinu prostředků na webu [Azure Portal](https://portal.azure.com/)a pak zkontrolovat definice JSON v nástroji Průzkumník, které vám pomůžou templatize skupiny prostředků.

### <a name="deploy-to-azure-button"></a>Tlačítko pro nasazení do Azure
Pokud používáte GitHub pro správu zdrojového kódu, můžete do souboru READme přidat [tlačítko nasadit do Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-to-azure-button) . MD, který umožňuje uživatelské rozhraní nasazení na klíč do Azure. I když to můžete udělat u jakékoli jednoduché aplikace, můžete to roztáhnout tak, aby bylo možné nasadit celou skupinu prostředků vložením azuredeploy.jsdo souboru v kořenovém adresáři úložiště. Tento soubor JSON, který obsahuje šablonu skupiny prostředků, se použije k vytvoření skupiny prostředků pomocí tlačítka nasadit do Azure. Příklad najdete v ukázce [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) , který budete používat v tomto kurzu.

## <a name="get-the-sample-resource-group-template"></a>Získat ukázkovou šablonu skupiny prostředků
Teď to máme k tomu přímo.

1. Přejděte k ukázce [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) App Service.
2. V readme.md klikněte na **nasadit do Azure**.
3. Přejdete na web [nasazení do Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure-appservice-samples%2FToDoApp%2Fmaster%2Fazuredeploy.json) a zobrazí se výzva k zadání parametrů nasazení. Všimněte si, že většina polí se naplní názvem úložiště a dalšími náhodnými řetězci. Všechna pole můžete změnit, pokud chcete, ale stačí, když zadáte jenom ty, které musíte zadat, SQL Server přihlašovací jméno správce a heslo a pak klikněte na **Další**.
   
   ![Zobrazuje vstupní parametry nasazení na webu nasazení na platformě Azure.](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Potom kliknutím na **nasadit** spusťte proces nasazení. Po dokončení procesu klikněte na http://todoapp odkaz *XXXX*. azurewebsites.NET a procházejte nasazenou aplikaci. 
   
   ![Zobrazuje proces nasazení vaší aplikace.](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   Uživatelské rozhraní by bylo trochu pomalé při prvním procházení, protože se aplikace právě spouštějí, ale přesvědčit se, že se jedná o plně funkční aplikaci.
5. Zpátky na stránce nasazení kliknutím na odkaz **Správa** Zobrazte novou aplikaci na webu Azure Portal.
6. V rozevíracím seznamu **Essentials** klikněte na odkaz skupina prostředků. Všimněte si také, že aplikace je už v **externím projektu** připojená k úložišti GitHubu. 
   
   ![Zobrazuje odkaz skupina prostředků v rozevíracím seznamu Essentials.](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. V okně Skupina prostředků si všimněte, že ve skupině prostředků už existují dvě aplikace a jeden SQL Database.
   
   ![Zobrazuje prostředky, které jsou k dispozici ve vaší skupině prostředků.](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Všechno, co jste právě viděli během několika krátkých minut, je plně nasazená aplikace se dvěma mikroslužbami, které mají všechny komponenty, závislosti, nastavení, databáze a průběžné publikování, nastavené pomocí automatizované orchestrace v Azure Resource Manager. To všechno bylo provedeno dvěma způsoby:

* Tlačítko nasadit do Azure
* azuredeploy.jsv kořenu úložiště

Můžete nasazovat stejnou aplikaci desítky, stovky nebo tisíce a mít vždycky stejnou konfiguraci. Opakovatelnost a předvídatelnost tohoto přístupu vám umožní snadno a spolehlivě nasazovat vysoce škálovatelné aplikace.

## <a name="examine-or-edit-azuredeployjson"></a>Prověřte (nebo upravte) AZUREDEPLOY.JSna
Teď se podíváme na to, jak se nastavilo úložiště GitHub. V sadě Azure .NET SDK budete používat editor JSON, takže pokud jste ještě nenainstalovali [sadu Azure .NET SDK 2,6](https://azure.microsoft.com/downloads/), udělejte to hned teď.

1. Naklonujte úložiště [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) pomocí oblíbeného nástroje Git. Na snímku obrazovky níže jsem to dělá v Team Explorer v Visual Studio 2013.
   
   ![Ukazuje, jak pomocí nástroje Git klonovat úložiště ToDoApp.](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. V kořenovém adresáři úložiště otevřete azuredeploy.jsv aplikaci Visual Studio. Pokud nevidíte podokno osnova JSON, musíte nainstalovat sadu Azure .NET SDK.
   
   ![Zobrazuje podokno osnova JSON v aplikaci Visual Studio.](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Nepopisujeme všechny podrobnosti formátu JSON, ale část [Další zdroje](#resources) obsahuje odkazy na výuku jazyka šablony skupiny prostředků. Tady teď ukážeme jenom zajímavé funkce, které vám pomůžou začít vytvářet vlastní šablonu pro nasazení aplikací.

### <a name="parameters"></a>Parametry
Podívejte se na část parametry a podívejte se, že většina těchto parametrů se zobrazí na tlačítku **nasadit do Azure** , které vás vyzve k zadání. Lokalita za tlačítkem **nasadit do Azure** naplní vstupní uživatelské rozhraní pomocí parametrů definovaných v azuredeploy.js. Tyto parametry se používají v rámci definic prostředků, jako jsou názvy prostředků, hodnoty vlastností atd.

### <a name="resources"></a>Zdroje informací
V uzlu Resources (prostředky) uvidíte, že jsou definované 4 prostředky nejvyšší úrovně, včetně SQL Server instance, App Serviceho plánu a dvou aplikací. 

#### <a name="app-service-plan"></a>Plán služby App Service
Pojďme začít s jednoduchým prostředkem na kořenové úrovni ve formátu JSON. V osnově JSON kliknutím na plán App Service s názvem **[hostingPlanName]** zvýrazněte odpovídající kód JSON. 

![Zobrazuje oddíl [hostingPlanName] kódu JSON.](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Všimněte si, že `type` element určuje řetězec pro plán App Service (byl označován jako serverová farma delší a dlouhou dobou) a další prvky a vlastnosti jsou vyplněny pomocí parametrů definovaných v souboru JSON a tento prostředek nemá žádné vnořené prostředky.

> [!NOTE]
> Všimněte si také, že hodnota `apiVersion` oznamuje Azure, na kterou verzi REST API použít definici prostředků JSON, a může ovlivnit způsob formátování prostředku uvnitř `{}` . 
> 
> 

#### <a name="sql-server"></a>SQL Server
Potom v osnově JSON klikněte na prostředek SQL Server s názvem **SQLServer** .

![Zobrazuje SQL Server prostředek s názvem SQLServer v osnově JSON.](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Všimněte si následujícího o zvýrazněném kódu JSON:

* Použití parametrů zajišťuje, aby byly vytvořené prostředky pojmenovány a nakonfigurovány způsobem, který je bude mezi sebou konzistentní.
* Prostředek SQLServer má dva vnořené prostředky, z nichž každá má jinou hodnotu pro `type` .
* Vnořené prostředky uvnitř `“resources”: […]` , kde jsou definovány databáze a pravidla brány firewall, mají `dependsOn` element, který určuje ID prostředku prostředku SQLServer na úrovni kořenového adresáře. Před vytvořením tohoto prostředku se zobrazí zpráva, že tento prostředek už musí existovat, Azure Resource Manager. a pokud je tento jiný prostředek definovaný v šabloně, pak ho vytvořte jako první.
  
  > [!NOTE]
  > Podrobné informace o použití `resourceId()` funkce naleznete v tématu [Azure Resource Manager Functions Template](../azure-resource-manager/templates/template-functions-resource.md#resourceid).
  > 
  > 
* Výsledkem `dependsOn` elementu je, že Azure Resource Manager může zjistit, které prostředky lze vytvořit paralelně a které prostředky je nutné vytvořit sekvenčně. 

#### <a name="app-service-app"></a>Aplikace služby App Service
Nyní se teď podíváme na samotné vlastní aplikace, které jsou složitější. Kliknutím na aplikaci [Variables (' apiSiteName ')] v osnově JSON zvýrazněte svůj kód JSON. Všimnete si, že věci jsou mnohem zajímavější. V tomto smyslu se o funkcích podíváme o jednu po jedné:

##### <a name="root-resource"></a>Kořenový prostředek
Aplikace závisí na dvou různých prostředcích. To znamená, že Azure Resource Manager vytvoří aplikaci pouze po vytvoření plánu App Service a instanci SQL Server.

![Zobrazuje závislosti aplikace v plánu App Service a instanci SQL Server.](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Nastavení aplikace
Nastavení aplikace jsou také definována jako vnořený prostředek.

![Zobrazuje nastavení aplikace definované jako vnořený prostředek v kódu JSON.](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

V `properties` elementu pro `config/appsettings` má dvě nastavení aplikace ve formátu `"<name>" : "<value>"` .

* `PROJECT` je [Nastavení KUDU](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) , které oznamuje nasazení v Azure, které projekty se mají použít v řešení sady Visual Studio s více projekty. Později ukážeme, jak je nakonfigurováno řízení zdrojů, ale vzhledem k tomu, že kód ToDoApp je v řešení sady Multi-Project sady Visual Studio, potřebujeme toto nastavení.
* `clientUrl` je jednoduše nastavení aplikace, které používá kód aplikace.

##### <a name="connection-strings"></a>Připojovací řetězce
Připojovací řetězce jsou také definovány jako vnořený prostředek.

![Ukazuje, jak jsou připojovací řetězce definovány jako vnořené prostředky v kódu JSON.](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

V `properties` elementu pro je `config/connectionstrings` každý připojovací řetězec definován také jako název: dvojice hodnot s konkrétním formátem `"<name>" : {"value": "…", "type": "…"}` . Pro `type` element jsou možné hodnoty `MySql` ,, a `SQLServer` `SQLAzure` `Custom` .

> [!TIP]
> Chcete-li zobrazit konečný seznam typů připojovacích řetězců, spusťte následující příkaz v Azure PowerShell: \[ Enum]:: GetNames ("Microsoft. windowsazure. Commands. Utilities. Web. Services. webentities. DatabaseType")
> 
> 

##### <a name="source-control"></a>Správa zdrojového kódu
Nastavení správy zdrojového kódu jsou také definována jako vnořený prostředek. Azure Resource Manager používá tento prostředek ke konfiguraci průběžného publikování (viz upozornění `IsManualIntegration` později) a také k automatickému nasazení kódu aplikace během zpracování souboru JSON.

![Ukazuje, jak jsou nastavení správy zdrojů definována jako vnořený prostředek v kódu JSON.](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl` a `branch` měly by být poměrně intuitivní a měly by odkazovat na úložiště Git a název větve, ze které se mají publikovat. Tyto hodnoty jsou znovu definované vstupními parametry. 

Všimněte si, `dependsOn` že v elementu, který kromě samotného prostředku aplikace `sourcecontrols/web` závisí také na `config/appsettings` a `config/connectionstrings` . Důvodem je to `sourcecontrols/web` , že když se nakonfiguruje, proces nasazení Azure se automaticky pokusí nasadit, sestavit a spustit kód aplikace. Proto vložení této závislosti vám pomůže zajistit, že aplikace má přístup k požadovaným nastavením aplikace a připojovacím řetězcům před spuštěním kódu aplikace. 

> [!NOTE]
> Všimněte si také, že `IsManualIntegration` je nastavena na `true` . Tato vlastnost je v tomto kurzu nezbytná, protože nevlastníte úložiště GitHubu, takže nemůžete ve skutečnosti udělit oprávnění k Azure ke konfiguraci průběžného publikování z [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (tj. Automatické aktualizace úložiště nabízených oznámení do Azure). Výchozí hodnotu `false` pro zadané úložiště můžete použít jenom v případě, že jste nakonfigurovali přihlašovací údaje vlastníka GitHubu v [Azure Portal](https://portal.azure.com/) před. Jinými slovy, pokud jste dříve nastavili správu zdrojového kódu na GitHub nebo BitBucket pro libovolnou aplikaci na webu [Azure Portal](https://portal.azure.com/) pomocí vašich uživatelských přihlašovacích údajů, pak Azure si přihlašovací údaje zapamatuje a použije je při každém nasazení jakékoli aplikace z GitHubu nebo Bitbucket v budoucnu. Pokud jste to ale ještě neudělali, nasazení šablony JSON selže, když se Azure Resource Manager pokusí nakonfigurovat nastavení správy zdrojů aplikace, protože se nemůže přihlásit do GitHubu nebo BitBucket s přihlašovacími údaji vlastníka úložiště.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>Porovnání šablony JSON s nasazenou skupinou prostředků
Tady můžete projít všechna okna aplikace na [portálu Azure Portal](https://portal.azure.com/), ale pokud ne, existuje další nástroj, který je stejně užitečný. Přečtěte si nástroj [Azure Resource Explorer](https://resources.azure.com) Preview, který poskytuje reprezentaci ve formátu JSON všech skupin prostředků v předplatných, protože ve skutečnosti existují v back-endu Azure. Můžete si také prohlédnout, jak hierarchie JSON skupiny prostředků v Azure odpovídá hierarchii v souboru šablony, která se používá k jejímu vytvoření.

Když například přejdete na nástroj [Azure Resource Explorer](https://resources.azure.com) a rozbalíte uzly v Průzkumníkovi, můžu zobrazit skupinu prostředků a prostředky kořenové úrovně, které se shromažďují v příslušných typech prostředků.

![Prohlédněte si skupinu prostředků a prostředky na kořenové úrovni v rozšířeném nástroji Průzkumník prostředků Azure.](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Pokud přejdete k podrobnostem aplikace, měli byste vidět podrobnosti konfigurace aplikace podobné tomuto snímku obrazovky:

![Přejděte k podrobnostem a zobrazte podrobnosti o konfiguraci v aplikaci.](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Vnořené prostředky by měly mít stejnou hierarchii velmi podobné těm v souboru šablony JSON a v podokně JSON by se měla zobrazit nastavení aplikace, připojovací řetězce atd. Zde uvedená neexistence nastavení může znamenat problém s vaším souborem JSON a může pomoct při odstraňování potíží se souborem šablony JSON.

## <a name="deploy-the-resource-group-template-yourself"></a>Nasaďte šablonu skupiny prostředků sami.
Tlačítko **nasadit do Azure** je skvělé, ale umožňuje nasadit šablonu skupiny prostředků v azuredeploy.jsjenom v případě, že jste už azuredeploy.jsna GitHubu. Sada Azure .NET SDK také poskytuje nástroje, pomocí kterých můžete nasadit libovolný soubor šablony JSON přímo z místního počítače. Chcete-li to provést, postupujte podle následujících kroků:

1. V aplikaci Visual Studio klikněte na **soubor**  >  **Nový**  >  **projekt**.
2. Klikněte na  >  **cloudová**  >  **Skupina prostředků Azure** Visual C# a pak klikněte na **OK**.
   
   ![Vytvořte nový projekt jako skupinu prostředků Azure v sadě Azure .NET SDK.](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. V nabídce **Vybrat šablonu Azure** vyberte **prázdná šablona** a klikněte na **OK**.
4. Přetáhněte azuredeploy.jsdo složky **template** v novém projektu.
   
   ![Zobrazuje výsledek přetažení azuredeploy.jsdo souboru do složky šablony projektu.](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. Z Průzkumník řešení otevřete zkopírovaný azuredeploy.jsna.
6. Do našeho souboru JSON můžeme přidat několik standardních prostředků Application Insights, a to tak, že kliknete na **Přidat prostředek**. Pokud vás zajímá jenom nasazení souboru JSON, přejděte k postupu nasazení.
   
   ![Zobrazuje tlačítko Přidat prostředek, které můžete použít k přidání standardních prostředků Application Insights do souboru JSON.](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. **Pro Web Apps vyberte Application Insights**, pak ověřte, že je vybraná možnost plán a aplikace App Service, a pak klikněte na **Přidat**.
   
   ![Zobrazuje výběr Application Insights pro Web Apps, název App Service plán a webovou aplikaci.](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Teď budete moct zobrazit několik nových prostředků, které v závislosti na prostředku a jeho činnosti mají závislosti v plánu App Service nebo aplikaci. Tyto prostředky nejsou povolené stávající definicí a vy budete tuto změnu měnit.
   
   ![Prohlédněte si nové prostředky, které mají závislosti na plánu App Service nebo aplikaci.](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. V osnově JSON kliknutím na **appInsights AutoScale** zvýrazněte svůj kód JSON. Toto je nastavení škálování pro váš App Service plán.
9. Ve zvýrazněném kódu JSON Najděte `location` `enabled` vlastnosti a a nastavte je tak, jak vidíte níže.
   
   ![Zobrazuje umístění a povolené vlastnosti v kódu JSON automatického škálování appInsights a hodnoty, na které byste je měli nastavit.](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. V osnově JSON klikněte na **CPUHigh appInsights** a zvýrazněte jeho kód JSON. Toto je výstraha.
11. Vyhledejte `location` vlastnosti a `isEnabled` a nastavte je tak, jak vidíte níže. Totéž udělejte pro ostatní tři výstrahy (fialové cibule).
    
    ![Zobrazuje umístění a deaktivované vlastnosti v kódu JSON CPUHigh appInsights a hodnoty, na které byste je měli nastavit.](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Teď jste připraveni nasadit. Klikněte pravým tlačítkem na projekt a vyberte **nasadit**  >  **nové nasazení**.
    
    ![Ukazuje, jak nasadit nový projekt.](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Pokud jste to ještě neudělali, přihlaste se k účtu Azure.
14. V předplatném vyberte existující skupinu prostředků nebo vytvořte novou, vyberte **azuredeploy.jszapnuto** a pak klikněte na **Upravit parametry**.
    
    ![Ukazuje, jak upravit parametry v azuredeploy.jsv souboru.](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Teď budete moct upravit všechny parametry definované v souboru šablony v tabulce s dobrým oprávněním. Parametry, které definují výchozí hodnoty, již mají své výchozí hodnoty a parametry definující seznam povolených hodnot budou zobrazeny jako rozevírací seznamy.
    
    ![Zobrazuje parametry, které definují seznam povolených hodnot jako rozevírací seznamy.](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Vyplňte všechny prázdné parametry a použijte [adresu úložiště GitHub pro ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) v části **relévání**. Pak klikněte na **Uložit**.
    
    ![Zobrazuje nově vyplněné parametry azuredeploy.jsv souboru.](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > Automatické škálování je funkce nabízená na úrovni **Standard** nebo vyšší a výstrahy na úrovni plánu jsou funkce nabízené na úrovni **Basic** nebo vyšší, takže budete muset nastavit parametr **SKU** na úroveň **Standard** nebo **Premium** , abyste viděli všechny nové prostředky služby App Insights.
    > 
    > 
16. Klikněte na **Deploy** (Nasadit). Pokud jste vybrali možnost **Uložit hesla**, heslo bude uloženo v souboru parametrů **v prostém textu**. V opačném případě budete požádáni o zadání hesla databáze během procesu nasazení.

A to je vše! Teď stačí přejít na [portál Azure Portal](https://portal.azure.com/) a nástroj [Azure Resource Explorer](https://resources.azure.com) , abyste viděli nové výstrahy a nastavení automatického škálování přidané do nasazené aplikace JSON.

Kroky v této části doplňují zejména následující:

1. Příprava souboru šablony
2. Vytvořili jste soubor parametrů pro přechod k souboru šablony.
3. Nasazený soubor šablony se souborem parametrů

Posledním krokem je snadné provedení rutiny prostředí PowerShell. Chcete-li zjistit, co aplikace Visual Studio provedla při nasazení aplikace, otevřete Scripts\Deploy-AzureResourceGroup.ps1. Existuje velké množství kódu, ale stačí pouze zdůraznit veškerý související kód, který potřebujete k nasazení souboru šablony se souborem parametrů.

![Zobrazuje relevantní kód ve skriptu, který potřebujete použít k nasazení souboru šablony se souborem parametrů.](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

Poslední rutinou `New-AzureResourceGroup` je ta, která tuto akci skutečně provádí. To by vám mělo Ukázat na to, že při použití nástrojů je poměrně snadné nasadit cloudovou aplikaci. Pokaždé, když spustíte rutinu na stejné šabloně se stejným souborem parametrů, vrátíte stejný výsledek.

## <a name="summary"></a>Souhrn
V DevOps je opakovatelnost a předvídatelnost klíčem k jakémukoli úspěšnému nasazení vysoce škálovatelné aplikace složené z mikroslužeb. V tomto kurzu jste do Azure nasadili aplikaci se dvěma mikroslužbami jako s jednou skupinou prostředků pomocí šablony Azure Resource Manager. Snad vám vaše znalosti, které potřebujete k zahájení konverze vaší aplikace v Azure do šablony, a jejich předpovídat a jejich nasazení. 

<a name="resources"></a>

## <a name="more-resources"></a>Další zdroje informací
* [Jazyk šablony Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Vytváření šablon Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Azure Resource Manager funkce šablon](../azure-resource-manager/templates/template-functions.md)
* [Nasazení aplikace pomocí šablony Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)
* [Použití Azure PowerShellu s Azure Resource Managerem](../azure-resource-manager/management/manage-resources-powershell.md)
* [Řešení potíží s nasazeními skupin prostředků v Azure](../azure-resource-manager/templates/common-deployment-errors.md)

## <a name="next-steps"></a>Další kroky

Další informace o syntaxi a vlastnostech JSON pro typy prostředků nasazené v tomto článku najdete v těchto tématech:

* [Microsoft. SQL/servery](/azure/templates/microsoft.sql/servers)
* [Microsoft. SQL/servery/databáze](/azure/templates/microsoft.sql/servers/databases)
* [Microsoft. SQL/servery/firewallRules](/azure/templates/microsoft.sql/servers/firewallrules)
* [Microsoft. Web/serverových farem](/azure/templates/microsoft.web/serverfarms)
* [Microsoft. Web/weby](/azure/templates/microsoft.web/sites)
* [Microsoft. Web/weby/sloty](/azure/templates/microsoft.web/sites/slots)
* [Microsoft. Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)

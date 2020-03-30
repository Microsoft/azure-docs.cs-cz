---
title: Předvídatelné nasazování aplikací pomocí ARM
description: Zjistěte, jak nasadit více aplikací Azure App Service jako jednu jednotku a předvídatelným způsobem pomocí šablon Azure Resource Management a skriptování powershellu.
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 01/06/2016
ms.custom: seodec18
ms.openlocfilehash: 62d0bf776b2d0c97d95b992ed6a1fd2a356e467a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75967381"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Zřizování a nasazování mikroslužeb předvídatelně v Azure
Tento kurz ukazuje, jak zřídit a nasadit aplikaci složenou z [mikroslužeb](https://en.wikipedia.org/wiki/Microservices) ve [službě Azure App Service](https://azure.microsoft.com/services/app-service/) jako jednu jednotku a předvídatelným způsobem pomocí šablon skupiny prostředků JSON a skriptování prostředí PowerShell. 

Při zřizování a nasazování aplikací ve vysokém měřítku, které se skládají z vysoce oddělených mikroslužeb, opakovatelnost a předvídatelnost jsou klíčové pro úspěch. [Azure App Service](https://azure.microsoft.com/services/app-service/) umožňuje vytvářet mikroslužby, které zahrnují webové aplikace, mobilní back-endy a aplikace rozhraní API. [Azure Resource Manager](../azure-resource-manager/management/overview.md) umožňuje spravovat všechny mikroslužeb jako celek, spolu s závislostmi prostředků, jako je například nastavení databáze a správy zdrojového kódu. Nyní můžete také nasadit takovou aplikaci pomocí šablon JSON a jednoduchého skriptování prostředí PowerShell. 

## <a name="what-you-will-do"></a>Co budete dělat
V kurzu nasadíte aplikaci, která obsahuje:

* Dvě aplikace služby App Service (tj. dvě mikroslužby)
* Back-endová databáze SQL
* Nastavení aplikací, připojovací řetězce a ovládací prvek zdrojového kódu
* Přehledy aplikací, výstrahy, nastavení automatického škálování

## <a name="tools-you-will-use"></a>Nástroje, které budete používat
V tomto kurzu budete používat následující nástroje. Vzhledem k tomu, že to není komplexní diskuse o nástrojích, budu se držet end-to-end scénář a jen vám stručný úvod do každého, a kde najdete více informací o tom. 

### <a name="azure-resource-manager-templates-json"></a>Šablony Azure Resource Manageru (JSON)
Pokaždé, když vytvoříte aplikaci ve službě Azure App Service, například Azure Resource Manager používá šablonu JSON k vytvoření celé skupiny prostředků s prostředky komponenty. Složitá šablona z [Azure Marketplace](/azure/marketplace) může zahrnovat databázi, účty úložiště, plán služby App Service, samotnou aplikaci, pravidla výstrah, nastavení aplikací, nastavení automatického škálování a další a všechny tyto šablony jsou vám k dispozici prostřednictvím Prostředí PowerShell. Další informace o šablonách Azure Resource Manageru najdete v [tématu Vytváření šablon Azure Resource Manageru.](../azure-resource-manager/templates/template-syntax.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Sada Azure SDK 2.6 pro Visual Studio
Nejnovější sada SDK obsahuje vylepšení podpory šablony Správce prostředků v editoru JSON. Pomocí této možnosti můžete rychle vytvořit šablonu skupiny prostředků od začátku nebo otevřít existující šablonu JSON (například staženou šablonu galerie) pro úpravy, naplnění souboru parametrů a dokonce i nasazení skupiny prostředků přímo z prostředku Azure. Skupinové řešení.

Další informace naleznete [v tématu Azure SDK 2.6 pro Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Azure PowerShell 0.8.0 nebo novější
Počínaje verzí 0.8.0 zahrnuje instalace Prostředí Azure PowerShell kromě modulu Azure navíc modul Azure. Tento nový modul umožňuje skriptovat nasazení skupin prostředků.

Další informace najdete [v tématu Používání Azure PowerShellu ve Správci prostředků Azure.](../powershell-azure-resource-manager.md)

### <a name="azure-resource-explorer"></a>Průzkumník prostředků Azure
Tento [nástroj preview](https://resources.azure.com) umožňuje prozkoumat definice JSON všech skupin prostředků v předplatném a jednotlivých prostředků. V nástroji můžete upravit definice JSON prostředku, odstranit celou hierarchii prostředků a vytvořit nové prostředky.  Informace, které jsou v tomto nástroji snadno dostupné, jsou velmi užitečné pro vytváření šablon, protože ukazují, jaké vlastnosti je třeba nastavit pro určitý typ prostředku, správné hodnoty atd. Můžete dokonce vytvořit skupinu prostředků na [webu Azure Portal](https://portal.azure.com/)a pak zkontrolovat jeho definice JSON v nástroji průzkumníka, abyste pomohli templatize skupiny prostředků.

### <a name="deploy-to-azure-button"></a>Tlačítko Nasazení do Azure
Pokud používáte GitHub pro správou zdrojového kódu, můžete do svého README vložit [tlačítko Deploy to Azure.](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) MD, který umožňuje na klíč nasazení ui do Azure. I když to můžete udělat pro libovolnou jednoduchou aplikaci, můžete rozšířit to povolit nasazení celé skupiny prostředků tím, že soubor azuredeploy.json v kořenovém adresáři úložiště. Tento soubor JSON, který obsahuje šablonu skupiny prostředků, bude použit tlačítkem Nasazení do Azure k vytvoření skupiny prostředků. Příklad naleznete v ukázku [ToDoApp,](https://github.com/azure-appservice-samples/ToDoApp) kterou použijete v tomto kurzu.

## <a name="get-the-sample-resource-group-template"></a>Získání ukázkové šablony skupiny prostředků
Takže teď pojďme rovnou k tomu.

1. Přejděte na ukázku služby [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) App Service.
2. V readme.md klikněte na **Deploy to Azure**.
3. Přejdete na web [nasazení do azure](https://deploy.azure.com) a budete požádáni o zadání parametrů nasazení. Všimněte si, že většina polí jsou naplněny název úložiště a některé náhodné řetězce pro vás. Pokud chcete, můžete změnit všechna pole, ale jediné, co musíte zadat, je přihlášení správce serveru SQL Server a heslo a potom klepněte na tlačítko **Další**.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Potom klikněte na **Nasadit** a spusťte proces nasazení. Po dokončení procesu klikněte http://todoappna odkaz *XXXX*.azurewebsites.net a procházejte nasazenou aplikaci. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   UI by bylo trochu pomalé, když jste poprvé procházet, protože aplikace jsou právě na startu, ale přesvědčit sami sebe, že je to plně funkční aplikace.
5. Na stránce Nasazení klikněte na odkaz **Spravovat** a podívejte se na novou aplikaci na webu Azure Portal.
6. V rozevíracím seznamu **Essentials** klikněte na odkaz Skupina prostředků. Všimněte si také, že aplikace je již připojena k úložišti GitHub v části **Externí projekt**. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. V okně skupiny prostředků si všimněte, že ve skupině prostředků již existují dvě aplikace a jedna databáze SQL.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Vše, co jste právě viděli během několika krátkých minut, je plně nasazená aplikace dvou mikroslužeb se všemi součástmi, závislostmi, nastaveními, databází a nepřetržitým publikováním nastaveným pomocí automatizované orchestrace ve Správci prostředků Azure. To vše bylo provedeno dvěma věcmi:

* Tlačítko Nasadit do Azure
* azuredeploy.json v kořenovém adresáři repo

Můžete nasadit stejnou aplikaci desítky, stovky nebo tisíce krát a mají přesně stejnou konfiguraci pokaždé. Opakovatelnost a předvídatelnost tohoto přístupu umožňuje snadno a s jistotou nasazovat aplikace ve velkém měřítku.

## <a name="examine-or-edit-azuredeployjson"></a>Zkontrolujte (nebo upravte) AZUREDEPLOY. Json
Teď se podívejme na to, jak bylo nastaveno úložiště GitHubu. Budete používat editor JSON v Azure .NET SDK, takže pokud jste ještě nenainstalovali [Azure .NET SDK 2.6](https://azure.microsoft.com/downloads/), udělejte to teď.

1. Klonujte úložiště [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) pomocí svého oblíbeného git nástroje. Na následujícím snímku obrazovky to dělám v Průzkumníkovi týmu v Sadě Visual Studio 2013.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. Z kořenového úložiště otevřete azuredeploy.json v sadě Visual Studio. Pokud podokno Osnova JSON nevidíte, je potřeba nainstalovat azure .NET SDK.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Nebudu popisovat každý detail formátu JSON, ale sekce [Další zdroje](#resources) obsahuje odkazy pro výuku jazyka šablony skupiny zdrojů. Tady vám ukážu zajímavé funkce, které vám pomohou začít vytvářet vlastní šablonu pro nasazení aplikací.

### <a name="parameters"></a>Parametry
Podívejte se na část parametrů a zjistěte, že většina těchto parametrů jsou to, co tlačítko **Nasadit do Azure** vyzve k zadání. Lokalita za tlačítkem **Nasadit do Azure** naplní vstupní uI pomocí parametrů definovaných v azuredeploy.json. Tyto parametry se používají v rámci definice prostředků, jako jsou názvy prostředků, hodnoty vlastností atd.

### <a name="resources"></a>Prostředky
V uzlu prostředků uvidíte, že jsou definovány 4 prostředky nejvyšší úrovně, včetně instance serveru SQL Server, plánu služby App Service a dvou aplikací. 

#### <a name="app-service-plan"></a>Plán služby App Service
Začněme s jednoduchým prostředek kořenové úrovně v JSON. V osnově JSON klikněte na plán služby App Service s názvem **[hostingPlanName]** a zvýrazněte odpovídající kód JSON. 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Všimněte `type` si, že prvek určuje řetězec pro plán služby App Service (byl nazýván serverovou farmu před dlouhou, dávno) a další prvky a vlastnosti jsou vyplněny pomocí parametrů definovaných v souboru JSON a tento prostředek nemá žádné vnořené prostředky.

> [!NOTE]
> Všimněte si také, že hodnota `apiVersion` říká Azure, která verze rozhraní REST API použít definici prostředku JSON `{}`s a může ovlivnit, jak by měl být prostředek formátován uvnitř . 
> 
> 

#### <a name="sql-server"></a>SQL Server
Dále klikněte na prostředek serveru SQL Server s názvem **SQLServer** v osnově JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Všimněte si následujícího kódu JSON:

* Použití parametrů zajišťuje, že vytvořené prostředky jsou pojmenovány a konfigurovány tak, aby byly vzájemně konzistentní.
* Prostředek SQLServer má dva vnořené prostředky, `type`z nichž každý má jinou hodnotu pro .
* Vnořené `“resources”: […]`prostředky uvnitř , kde jsou definovány `dependsOn` databáze a pravidla brány firewall, mají prvek, který určuje ID prostředku SQLServer kořenové úrovně. To říká Azure Resource Manager, "před vytvořením tohoto prostředku, že jiný prostředek již musí existovat; a pokud je tento jiný zdroj definován v šabloně, vytvořte jej nejprve".
  
  > [!NOTE]
  > Podrobné informace o tom, `resourceId()` jak používat tuto funkci, najdete v [tématu Funkce šablony Správce prostředků Azure](../azure-resource-manager/templates/template-functions-resource.md#resourceid).
  > 
  > 
* Efekt `dependsOn` prvku je, že Azure Resource Manager můžete vědět, které prostředky lze vytvořit paralelně a které prostředky musí být vytvořeny postupně. 

#### <a name="app-service-app"></a>Aplikace služby App Service
Nyní se přesuneme k samotným samotným aplikacím, které jsou složitější. Kliknutím na aplikaci [variables('apiSiteName')] v osnově JSON zvýrazněte její kód JSON. Všimněte si, že věci jsou stále mnohem zajímavější. Za tímto účelem budu mluvit o funkcích jeden po druhém:

##### <a name="root-resource"></a>Kořenový prostředek
Aplikace závisí na dvou různých prostředcích. To znamená, že Azure Resource Manager vytvoří aplikaci pouze po vytvoření plánu služby App Service a instance SQL Server.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Nastavení aplikace
Nastavení aplikace jsou také definovány jako vnořený prostředek.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

V `properties` prvku `config/appsettings`pro , máte dvě nastavení `"<name>" : "<value>"`aplikace ve formátu .

* `PROJECT`je [nastavení KUDU,](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) které říká nasazení Azure, který projekt použít v řešení Visual Studio s více projekty. Ukážu vám později, jak je nakonfigurována kontrola zdrojového kódu, ale protože kód ToDoApp je v řešení visual studia s více projekty, potřebujeme toto nastavení.
* `clientUrl`je jednoduše nastavení aplikace, které používá kód aplikace.

##### <a name="connection-strings"></a>Připojovací řetězce
Připojovací řetězce jsou také definovány jako vnořený prostředek.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

V `properties` elementu `config/connectionstrings`pro je každý připojovací řetězec také definován jako `"<name>" : {"value": "…", "type": "…"}`dvojice name:value s určitým formátem . Pro `type` prvek `MySql`jsou možné `SQLServer`hodnoty `SQLAzure`, `Custom`, a .

> [!TIP]
> Konečný seznam typů připojovacích řetězců spusťte v \[prostředí Azure PowerShell: Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
> 
> 

##### <a name="source-control"></a>Správa zdrojového kódu
Nastavení správy zdrojového kódu jsou také definovány jako vnořený prostředek. Azure Resource Manager používá tento prostředek ke konfiguraci průběžné publikování (viz upozornění na `IsManualIntegration` později) a také zahájit nasazení kódu aplikace automaticky během zpracování souboru JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl`a `branch` měla by být docela intuitivní a měla by ukazovat na úložiště Git a název větve, ze které chcete publikovat. Opět jsou definovány vstupními parametry. 

Všimněte `dependsOn` si v elementu, který kromě `sourcecontrols/web` samotného `config/appsettings` `config/connectionstrings`prostředku aplikace závisí také na a . Důvodem je, že jakmile `sourcecontrols/web` je nakonfigurován, proces nasazení Azure se automaticky pokusí nasadit, sestavit a spustit kód aplikace. Proto vložení této závislosti vám pomůže zajistit, že aplikace má přístup k požadované nastavení aplikace a připojovací řetězce před spuštěním kódu aplikace. 

> [!NOTE]
> Všimněte `IsManualIntegration` si také, že je nastavena na `true`. Tato vlastnost je nezbytná v tomto kurzu, protože ve skutečnosti nevlastníte úložiště GitHub, a proto ve skutečnosti nemůžete udělit oprávnění Azure ke konfiguraci průběžného publikování z [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (tj. push automatické aktualizace úložiště do Azure). Výchozí hodnotu `false` pro zadané úložiště můžete použít jenom v případě, že jste dříve na [webu Azure nawebu nawebu nawebu nawebu nakonfigurovali](https://portal.azure.com/) přihlašovací údaje vlastníka GitHubu. Jinými slovy, pokud jste nastavili správu zdrojového kódu na GitHub nebo BitBucket pro libovolnou aplikaci na [webu Azure Portal](https://portal.azure.com/) dříve pomocí přihlašovacích údajů uživatele, pak Azure bude pamatovat přihlašovací údaje a používat je vždy, když nasadíte libovolnou aplikaci z GitHub nebo BitBucket v budoucnu. Pokud jste to však ještě neudělali, nasazení šablony JSON se nezdaří, když se Správce prostředků Azure pokusí nakonfigurovat nastavení správy zdrojového kódu aplikace, protože se nemůže přihlásit k GitHubu nebo BitBucketu s přihlašovacími údaji vlastníka úložiště.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>Porovnání šablony JSON s nasazenou skupinou prostředků
Tady můžete projít všechny listy aplikace na [Webu Azure Portal](https://portal.azure.com/), ale je tu další nástroj, který je stejně užitečný, ne-li víc. Přejděte na nástroj preview [Průzkumníka prostředků Azure,](https://resources.azure.com) který vám poskytuje json reprezentaci všech skupin prostředků ve vašich předplatných, protože ve skutečnosti existují v back-endu Azure. Můžete také zobrazit, jak hierarchie JSON skupiny prostředků v Azure odpovídá hierarchii v souboru šablony, který se používá k jejímu vytvoření.

Například když přejdu na nástroj [Průzkumník prostředků Azure](https://resources.azure.com) a rozbalit uzly v průzkumníku, vidím skupinu prostředků a prostředky kořenové úrovně, které jsou shromažďovány v rámci jejich příslušné typy prostředků.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Pokud přejdete k aplikaci, měli byste vidět podrobnosti konfigurace aplikace podobné níže uvedenému snímku obrazovky:

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Opět by vnořené prostředky měly mít hierarchii velmi podobnou hierarchii v souboru šablony JSON a měli byste vidět nastavení aplikace, připojovací řetězce atd., které se správně projeví v podokně JSON. Absence nastavení zde může znamenat problém se souborem JSON a může vám pomoci při řešení potíží se souborem šablony JSON.

## <a name="deploy-the-resource-group-template-yourself"></a>Nasazení šablony skupiny prostředků sami
Tlačítko **Nasadit do Azure** je skvělé, ale umožňuje nasadit šablonu skupiny prostředků v azuredeploy.json pouze v případě, že jste už na GitHubu zatlačili azuredeploy.json. Sada Azure .NET SDK také poskytuje nástroje pro nasazení libovolného souboru šablony JSON přímo z místního počítače. Chcete-li to provést, postupujte podle následujících kroků:

1. V sadě Visual Studio klepněte na **položku Soubor** > **nového** > **projektu**.
2. Klikněte na **Visual C#** > **Cloud** > **Azure Resource Group**a potom klikněte na **OK**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. V **části Vyberte šablonu Azure**vyberte **Prázdná šablona** a klepněte na **OK**.
4. Přetáhněte azuredeploy.json do složky **Šablona** nového projektu.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. Z Průzkumníka řešení otevřete zkopírovaný azuredeploy.json.
6. Jen kvůli demonstraci, pojďme přidat některé standardní application insight prostředky do našeho souboru JSON, kliknutím **na přidat prostředek**. Pokud máte zájem o nasazení souboru JSON, přejděte na kroky nasazení.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Vyberte **Přehledy aplikací pro webové aplikace**, zkontrolujte, jestli je vybrán existující plán služby App Service a aplikace, a klikněte na **Přidat**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Teď budete moct zobrazit několik nových prostředků, které v závislosti na prostředku a co dělá, mají závislosti na plánu služby App Service nebo na aplikaci. Tyto prostředky nejsou povoleny jejich stávající definici a budete to změnit.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. V osnově JSON klikněte na **appInsights AutoScale,** abyste zvýraznili jeho kód JSON. Toto je nastavení škálování pro váš plán služby App Service.
9. Ve zvýrazněném kódu JSON `location` `enabled` vyhledejte vlastnosti a nastavte je, jak je znázorněno níže.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. V osnově JSON klikněte na **CPUHigh appInsights,** abyste zvýraznili jeho kód JSON. Tohle je poplach.
11. Vyhledejte `location` `isEnabled` vlastnosti a nastavte je tak, jak je znázorněno níže. Proveďte totéž pro další tři výstrahy (fialové žárovky).
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Nyní jste připraveni k nasazení. Klepněte pravým tlačítkem myši na projekt a vyberte **možnost Nasadit** > **nové nasazení**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Pokud jste tak ještě neučinili, přihlaste se ke svému účtu Azure.
14. Vyberte existující skupinu prostředků ve vašem předplatném nebo vytvořte novou, vyberte **azuredeploy.json**a klikněte na **Upravit parametry**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Nyní budete moci upravit všechny parametry definované v souboru šablony v pěkné tabulce. Parametry, které definují výchozí hodnoty, již budou mít své výchozí hodnoty a parametry, které definují seznam povolených hodnot, budou zobrazeny jako rozevírací seznamy.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Vyplňte všechny prázdné parametry a použijte [iphub ovou adresu pro ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) v **repoUrl**. Potom klepněte na tlačítko **Uložit**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > Automatické škálování je funkce nabízená **ve** standardní úrovni nebo vyšší a výstrahy na úrovni plánu jsou funkce nabízené ve vrstvě **Basic** nebo vyšší, budete muset nastavit parametr **sku** na **standardní** nebo **premium,** abyste viděli, jak se rozsvítí všechny vaše nové prostředky App Insights.
    > 
    > 
16. Klepněte na tlačítko **Nasadit**. Pokud jste vybrali **uložit hesla**, heslo bude uloženo v souboru parametrů ve **formátu prostého textu**. V opačném případě budete vyzváni k zadání hesla databáze během procesu nasazení.

A to je vše! Teď stačí přejít na [Portál Azure](https://portal.azure.com/) a nástroj Azure Resource [Explorer](https://resources.azure.com) a zobrazit nové výstrahy a nastavení automatického škálování přidané do nasazené aplikace JSON.

Vaše kroky v této části byly provedeny především následujícími kroky:

1. Připravený soubor šablony
2. Vytvoření souboru parametrů pro použití souboru šablony
3. Nasazení souboru šablony se souborem parametrů

Poslední krok lze snadno provést rutinou prostředí PowerShell. Chcete-li zjistit, co Visual Studio udělalo při nasazení vaší aplikace, otevřete skripty\Deploy-AzureResourceGroup.ps1. Je tam spousta kódu, ale já jsem jen chtěl upozornit na všechny relevantní kód, který potřebujete k nasazení souboru šablony s parametrem souboru.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

Poslední rutina , `New-AzureResourceGroup`, je ta, která akci skutečně provádí. To vše by vám mělo ukázat, že s pomocí nástrojů je relativně jednoduché nasadit cloudovou aplikaci předvídatelně. Pokaždé, když spustíte rutinu na stejné šabloně se stejným souborem parametrů, získáte stejný výsledek.

## <a name="summary"></a>Souhrn
V DevOps opakovatelnost a předvídatelnost jsou klíčem k jakékoli úspěšné nasazení aplikace ve velkém měřítku složené z mikroslužeb. V tomto kurzu jste nasadili aplikaci dvou mikroslužeb do Azure jako jednu skupinu prostředků pomocí šablony Azure Resource Manager. Doufejme, že vám poskytl znalosti, které potřebujete k zahájení převodu aplikace v Azure do šablony a může ji zřídit a nasadit předvídatelně. 

<a name="resources"></a>

## <a name="more-resources"></a>Další zdroje informací
* [Jazyk šablony Správce prostředků Azure](../azure-resource-manager/templates/template-syntax.md)
* [Vytváření šablon Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Funkce šablony Správce prostředků Azure](../azure-resource-manager/templates/template-functions.md)
* [Nasazení aplikace pomocí šablony Azure Resource Manageru](../azure-resource-manager/templates/deploy-powershell.md)
* [Použití Azure PowerShellu s Azure Resource Managerem](../azure-resource-manager/powershell-azure-resource-manager.md)
* [Poradce při potížích s nasazením skupiny prostředků v Azure](../azure-resource-manager/templates/common-deployment-errors.md)

## <a name="next-steps"></a>Další kroky

Informace o syntaxi JSON a vlastnostech typů prostředků nasazených v tomto článku najdete v těchto tématech:

* [Microsoft.SQL/servery](/azure/templates/microsoft.sql/servers)
* [Microsoft.Sql/servery/databáze](/azure/templates/microsoft.sql/servers/databases)
* [Microsoft.Sql/servery/firewallRules](/azure/templates/microsoft.sql/servers/firewallrules)
* [Farmy Microsoft.Web/server](/azure/templates/microsoft.web/serverfarms)
* [Web/weby společnosti Microsoft](/azure/templates/microsoft.web/sites)
* [Microsoft.Web/weby/sloty](/azure/templates/microsoft.web/sites/slots)
* [Microsoft.Insights/nastavení automatického škálování](/azure/templates/microsoft.insights/autoscalesettings)
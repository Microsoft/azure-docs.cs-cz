---
title: Zřídit a nasadit mikroslužeb předvídatelné v Azure
description: Informace o nasazení aplikace skládá z mikroslužeb v Azure App Service jako na jednu jednotku a předvídatelný způsobem pomocí šablony pro skupiny prostředků JSON a skriptů prostředí PowerShell.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2016
ms.author: cephalin
ms.openlocfilehash: 3719e037f1564411a8f94d1ca962ba1ef6b5d435
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23837096"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Zřídit a nasadit mikroslužeb předvídatelné v Azure
Tento kurz ukazuje, jak zřídit a nasadit aplikace skládá z [mikroslužeb](https://en.wikipedia.org/wiki/Microservices) v [Azure App Service](/services/app-service/) jako na jednu jednotku a předvídatelný způsobem pomocí šablony pro skupiny prostředků JSON a skriptů prostředí PowerShell. 

Při zřizování a nasazování aplikací špičkové, které se skládají z vysoce odpojené mikroslužeb, opakovatelnost a předvídatelnost jsou klíčové pro úspěch. [Aplikační služba Azure](/services/app-service/) umožňuje vytvářet mikroslužeb, které zahrnují webové aplikace, mobilní aplikace, aplikace API a logic apps. [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) vám umožňuje spravovat všechny mikroslužeb jako celek, společně s závislosti prostředků, jako jsou databáze a nastavení řízení zdroje. Teď můžete nasadit taky takové aplikace pomocí šablony JSON a jednoduchý skriptů prostředí PowerShell. 

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="what-you-will-do"></a>Co provedete
V tomto kurzu nasazujete aplikaci, která zahrnuje:

* Dva webové aplikace (tj. dva mikroslužeb)
* Back-end databáze SQL
* Nastavení aplikace, připojovací řetězce a Správa zdrojového kódu
* Application insights, výstrahy, nastavení automatického škálování

## <a name="tools-you-will-use"></a>Nástroje, které budete používat
V tomto kurzu budete používat následující nástroje. Vzhledem k tomu, že není komplexní zabývat nástroje, kliknete na přilepit do začátku do konce scénáře a právě získáte stručný úvod ke každému, a kde můžete najít další informace o jeho. 

### <a name="azure-resource-manager-templates-json"></a>Šablony Azure Resource Manageru (JSON)
Pokaždé, když vytvoříte webovou aplikaci v Azure App Service, například Azure Resource Manager používá k vytvoření skupiny celý prostředků s prostředky součást šablonu JSON. Komplexní šablony z [Azure Marketplace](/marketplace) jako [škálovatelné WordPress](/marketplace/partners/wordpress/scalablewordpress/) aplikace může obsahovat databáze MySQL, účty úložiště, plán služby App Service, webové aplikace, pravidla výstrah, nastavení aplikace, nastavení automatického škálování a další, a všechny tyto šablony jsou k dispozici prostřednictvím prostředí PowerShell. Informace o tom, jak stáhnout a použít tyto šablony najdete v tématu [použití Azure Powershellu s Azure Resource Manager](../powershell-azure-resource-manager.md).

Další informace o šablonách Azure Resource Manager, najdete v části [vytváření šablon Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)

### <a name="azure-sdk-26-for-visual-studio"></a>2.6 Azure SDK pro Visual Studio
Nejnovější SDK obsahuje vylepšení podpory šablony správce prostředků v editoru JSON. Používejte rychle vytvořit šablonu skupiny prostředků od nuly nebo otevřete stávající šablonu JSON (například šablonu stažené galerie) pro úpravy, naplnění souboru parametrů a i nasazení skupiny prostředků přímo z řešení skupiny prostředků Azure.

Další informace najdete v tématu [2.6 Azure SDK pro Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Prostředí Azure PowerShell 0.8.0 nebo novější
Počínaje verzí 0.8.0 je součástí instalace prostředí Azure PowerShell modulu Azure Resource Manager kromě modulu Azure. Tento nový modul umožňuje skript nasazení skupiny prostředků.

Další informace najdete v tématu [použití Azure Powershellu s Azure Resource Manager](../powershell-azure-resource-manager.md)

### <a name="azure-resource-explorer"></a>Průzkumník prostředků Azure
To [nástroje preview](https://resources.azure.com) umožňuje prozkoumat JSON definice všech skupin prostředků v předplatného a jednotlivých zdrojů. V nástroji můžete upravovat JSON definice prostředku, odstranit celou hierarchii prostředků a vytvořit nové prostředky.  Snadno dostupné v tomto nástroji informace jsou velmi užitečné při vytváření šablony, protože zobrazuje jaké vlastnosti je třeba nastavit pro konkrétní typ prostředku, správné hodnoty, atd. Můžete například vytvořit vaší skupiny prostředků v [portálu Azure](https://portal.azure.com/), zkontrolujte jeho definice JSON v nástroj Průzkumník můžete templatize skupině prostředků.

### <a name="deploy-to-azure-button"></a>Nasazení do Azure tlačítko
Pokud používáte GitHub pro řízení zdrojů, které můžete vložit [nasadit do Azure tlačítko](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) do vašeho souboru README. MD, což umožňuje nasazení klíč uživatelského rozhraní do Azure. Zatímco můžete provést pro všechny jednoduché webové aplikace, můžete rozšířit na povolit nasazení celé skupiny prostředků umístěním souboru azuredeploy.json v kořenovém adresáři úložiště. Tento soubor JSON, který obsahuje šablony skupiny prostředků, použije nasadit do Azure tlačítko pro vytvoření skupiny prostředků. Příklad, naleznete v části [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) vzorku, který budete používat v tomto kurzu.

## <a name="get-the-sample-resource-group-template"></a>Získat šablonu ukázka skupiny prostředků
Proto nyní Pojďme vpravo na ni.

1. Přejděte na [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) Ukázka aplikace služby.
2. V readme.md, klikněte na **nasadit do Azure**.
3. Se [nasadit do azure](https://deploy.azure.com) lokality a výzva k vstupní parametry nasazení. Všimněte si, že většina polí se naplní název úložiště a některé náhodného řetězce pro vás. Všechna pole můžete změnit, pokud chcete, ale pouze věcí, je nutné zadat jsou přihlášení správce systému SQL Server a heslo a pak klikněte na **Další**.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Klikněte na tlačítko **nasadit** k zahájení procesu nasazení. Jakmile se spustí proces dokončen, klikněte na tlačítko http://todoapp*XXXX*. azurewebsites.net odkaz na Procházet nasazené aplikace. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   Uživatelské rozhraní by být trochu pomalé, pokud nejprve procházení vzhledem k tomu, že se aplikace právě spuštění, ale přimět sami, že je plně funkční aplikaci.
5. Zpět na stránce nasadit, klikněte **spravovat** odkaz zobrazíte novou aplikaci na portálu Azure.
6. V **Essentials** rozevíracího seznamu, klikněte na odkaz skupiny prostředků. Všimněte si také, že webová aplikace je již připojen k úložišti GitHub pod **externí projektu**. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. V okně skupiny prostředků Všimněte si, že již existují dva webové aplikace a jedna databáze SQL ve skupině prostředků.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Vše, co jste viděli pouze za pár minut krátké plně nasazené dva mikroslužbu aplikace, se všechny součásti, závislosti, nastavení, databáze a průběžné publikování nastavil automatizované orchestration ve službě Správce prostředků Azure. Všechny tomu bylo potřeba dvě věci:

* Nasadit do Azure tlačítko
* azuredeploy.JSON v kořenovém úložišti

Můžete nasadit stejná aplikace desítek, stovek nebo tisíců časy a přesně stejnou konfiguraci pokaždé, když máte. Opakovatelnost a předvídatelnost tento přístup umožňuje nasadit špičkové aplikace s jednoduchosti a spolehlivosti.

## <a name="examine-or-edit-azuredeployjson"></a>Zkontrolujte (nebo upravit) AZUREDEPLOY. JSON
Nyní podíváme, jak bylo nastaveno úložiště GitHub. Budete používat JSON editor v .NET SDK služby Azure, takže pokud jste ještě nenainstalovali [Azure .NET SDK 2.6](https://azure.microsoft.com/downloads/), udělejte teď.

1. Klon [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) úložiště pomocí vaše oblíbené git nástroje. Na tomto snímku obrazovky to bude to v Průzkumník týmových projektů v sadě Visual Studio 2013.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. Na kořenovém adresáři úložiště spusťte azuredeploy.json v sadě Visual Studio. Pokud nevidíte v podokně osnovou JSON, budete muset nainstalovat sadu Azure .NET SDK.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Není, který bude popisují každou podrobností formátu JSON, ale [více prostředků](#resources) část obsahuje odkazy pro učení jazyka šablony skupiny prostředků. Právě zde bude ukázat vám, že zajímavé funkce, které vám pomůžou začít při vytvoření vlastní šablony pro nasazení aplikace.

### <a name="parameters"></a>Parametry
Podívejte se na části Parametry uvidíte, že většina tyto parametry jsou co **nasadit do Azure** tlačítko vás vyzve, abyste vstup. Lokality za **nasadit do Azure** tlačítko naplní vstupní parametry definované v azuredeploy.json pomocí uživatelského rozhraní. Tyto parametry se používají v definicích prostředků, jako je například názvy prostředků, hodnoty vlastností, atd.

### <a name="resources"></a>Zdroje
V uzlu prostředků uvidíte, že jsou definovány 4 nejvyšší úrovně prostředky, včetně instance systému SQL Server, plán služby App Service a dva webové aplikace. 

#### <a name="app-service-plan"></a>Plán služby App Service
Začněme jednoduché prostředků úrovni kořenového adresáře v kódu JSON. V osnovou JSON, klikněte na plán služby App Service s názvem **[hostingPlanName]** zvýrazněte odpovídající kód JSON. 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Všimněte si, že `type` element určuje řetězec pro plán služby App Service (byla volána v serverové farmě dlouhý, dlouhou dobou) a další prvky a vlastnosti jsou vyplněny pomocí parametry definované v souboru JSON a tento prostředek neobsahuje žádné vnořené prostředky.

> [!NOTE]
> Poznámka: hodnota `apiVersion` informuje Azure, která verze rozhraní REST API použijte JSON definice prostředků s ale může mít vliv na způsob formátování prostředek uvnitř `{}`. 
> 
> 

#### <a name="sql-server"></a>SQL Server
Potom klikněte na prostředek systému SQL Server s názvem **SQLServer** osnovy JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Vezměte na vědomí následující skutečnosti související zvýrazněný kód JSON:

* Použití parametrů zajišťuje, že jsou vytvořené prostředky s názvem a nakonfigurovat tak, že jsou konzistentní s jednu na druhou.
* Prostředek SQLServer má dva vnořených prostředků, každá z nich má jinou hodnotu pro `type`.
* Vnořených prostředků uvnitř `“resources”: […]`, kde jsou definovány databáze a pravidla brány firewall, mají `dependsOn` element, který určuje ID prostředku prostředku SQLServer kořenové úrovni. Tato hodnota informuje Azure Resource Manager, "před vytvořením tohoto prostředku, který už musí existovat jiný prostředek; a pokud tento jiný prostředek je definován v šabloně, vytvořte než nejprve".
  
  > [!NOTE]
  > Podrobné informace o tom, jak používat `resourceId()` funkce najdete v tématu [funkce šablon Azure Resource Manager](../azure-resource-manager/resource-group-template-functions-resource.md#resourceid).
  > 
  > 
* Účinek `dependsOn` element je, že Azure Resource Manager mohli zjistit prostředky, ke kterým může být vytvořen paralelně a prostředky, ke kterým musí být vytvořený postupně. 

#### <a name="web-app"></a>Webová aplikace
Nyní můžeme přesunout k skutečné webové aplikace, sami, které jsou složitější. Klikněte na tlačítko webové aplikace [variables('apiSiteName')] do osnovou JSON, abyste měli na očích jeho kód JSON. Můžete si všimnout, že jsou získávání věcí mnohem víc zajímavé. Pro tento účel I mluvit o funkcích jeden po druhém:

##### <a name="root-resource"></a>Kořenové prostředků
Webové aplikace, závisí na dvou různých prostředků. To znamená, že bude Azure Resource Manager až po, vytvoří se plán služby App Service a instance systému SQL Server vytvořit webové aplikace.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Nastavení aplikace
Nastavení aplikace jsou také definovat jako vnořeného prostředku.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

V `properties` element pro `config/appsettings`, máte dvě nastavení aplikace ve formátu `“<name>” : “<value>”`.

* `PROJECT`je [KUDU nastavení](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) které sděluje nasazení Azure, které projektu pro použití v sadě Visual Studio řešení vícenásobného projektu. I vám ukáže, později zdrojového kódu konfiguraci, ale vzhledem k tomu, že kód ToDoApp je v sadě Visual Studio řešení vícenásobného projektu, je třeba, aby toto nastavení.
* `clientUrl`je jednoduše aplikace nastavení, která kódu aplikace používá.

##### <a name="connection-strings"></a>Připojovací řetězce
Připojovací řetězce, je definována jako vnořeného prostředku.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

V `properties` element pro `config/connectionstrings`, každý připojovací řetězec je také definován jako dvojice názvu a hodnoty, s konkrétní formát `“<name>” : {“value”: “…”, “type”: “…”}`. Pro `type` elementu možné hodnoty jsou `MySql`, `SQLServer`, `SQLAzure`, a `Custom`.

> [!TIP]
> Pro konečné seznam typů řetězec připojení, spusťte následující příkaz v prostředí Azure PowerShell: \[Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
> 
> 

##### <a name="source-control"></a>Správa zdrojového kódu
Nastavení správy zdrojů, je definována jako vnořeného prostředku. Azure Resource Manager používá tento prostředek konfigurace průběžné publikování (viz přímý přístup paměti na `IsManualIntegration` později) a taky ji nasazování kódu aplikace automaticky během zpracování souboru JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl`a `branch` by měl být poměrně intuitivní a by měla odkazovat na název větve pro publikování z a úložiště Git. Opakujte tyto jsou definované vstupní parametry. 

Poznámka: v `dependsOn` element, který kromě webovým prostředkům aplikace samostatně, `sourcecontrols/web` závisí také na `config/appsettings` a `config/connectionstrings`. Důvodem je, že po `sourcecontrols/web` je nakonfigurován, proces nasazení Azure budou snažit o automatické nasazení, sestavit a spustit kódu aplikace. Proto vkládání tuto závislost vám pomůže Ujistěte se, že aplikace má přístup k nastavení požadovaná aplikace a připojovací řetězce, před spuštěním kódu aplikace. 

> [!NOTE]
> Všimněte si také, že `IsManualIntegration` je nastaven na `true`. Tato vlastnost je nutné v tomto kurzu, protože ve skutečnosti nevlastníte úložiště GitHub a proto nelze udělit ve skutečnosti oprávnění k Azure a nakonfigurovat nepřetržité publikování z [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (tj. nabízené automatické úložiště aktualizace do Azure). Můžete použít výchozí hodnotu `false` pro zadané úložiště jenom v případě, že jste nakonfigurovali přihlašovací údaje Githubu vlastníka v [portál Azure](https://portal.azure.com/) před. Jinými slovy Pokud jste nastavili zdrojového kódu pro libovolnou aplikaci v GitHub nebo BitBucket [portálu Azure](https://portal.azure.com/) dříve, pomocí vaší uživatelské přihlašovací údaje, pak Azure bude pamatovat přihlašovací údaje a použít je vždy, když nasazujete žádné aplikace z webu GitHub nebo BitBucket v budoucnu. Ale pokud jste to ještě neudělali, nasazení šablony JSON se nezdaří když Azure Resource Manager pokusí konfigurovat nastavení správy zdrojů webové aplikace, protože se nemůžete přihlásit do GitHub nebo BitBucket s přihlašovacími údaji úložiště vlastníka.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>Porovnání šablona JSON se skupinou nasazené prostředků
Zde můžete přejít pomocí okna všechny webové aplikace v [portálu Azure](https://portal.azure.com/), ale existuje jiný nástroj, který není stejně jako užitečné, pokud informace. Přejděte na [Průzkumníka prostředků Azure](https://resources.azure.com) preview nástroj, který poskytuje reprezentaci JSON všech skupin prostředků v rámci vašich předplatných, které jsou ve skutečnosti v Azure back-end. Můžete také zjistit, jak odpovídá hierarchii JSON skupině prostředků v Azure s hierarchií v souboru šablony, který se používá k jeho vytvoření.

Například když přejít k [Průzkumníka prostředků Azure](https://resources.azure.com) nástroje a rozbalte uzly v Průzkumníku, zobrazují se skupina prostředků a prostředky úrovni kořenového adresáře, které se shromažďují v jejich typy příslušných prostředků.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Pokud můžete přejít k podrobnostem a webovou aplikaci, byste měli vidět podrobnosti konfigurace webové aplikace podobně jako následující snímek obrazovky:

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Znovu vnořených prostředků by měl mít velmi podobná těm v souboru šablony JSON hierarchie a měli byste vidět nastavení aplikace, připojovací řetězce, atd., správně odrážela v podokně JSON. Nastavení v tomto poli chybí může značit problém s souboru JSON a může pomoci při odstraňování souboru šablony JSON.

## <a name="deploy-the-resource-group-template-yourself"></a>Nasazení šablony skupiny prostředků sami
**Nasadit do Azure** tlačítko je skvělé, ale umožňuje nasadit šablony skupiny prostředků v azuredeploy.json pouze v případě, že jste již nabídnutých azuredeploy.json do Githubu. .NET SDK služby Azure také poskytuje nástroje, které můžete nasadit všechny soubor šablony JSON přímo z vašeho místního počítače. Chcete-li to provést, postupujte následujícím způsobem:

1. V sadě Visual Studio, klikněte na tlačítko **soubor** > **nový** > **projektu**.
2. Klikněte na tlačítko **Visual C#** > **cloudu** > **skupiny prostředků Azure**, pak klikněte na tlačítko **OK**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. V **vybrat šablonu Azure**, vyberte **prázdné šablonu** a klikněte na tlačítko **OK**.
4. Přetáhněte azuredeploy.json do **šablony** složky nový projekt.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. V Průzkumníku řešení otevřete zkopírovaný azuredeploy.json.
6. Pouze z důvodu ukázky, přidejme některé standardní prostředky aplikace vhled do našich souboru JSON, kliknutím na **přidat prostředek**. Pokud vás zajímá jenom nasazení souboru JSON, pokračujte kroky nasazení.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Vyberte **Application Insights pro webové aplikace**, pak se ujistěte, že je vybrána existující aplikaci plán a webové služby App Service a pak klikněte na tlačítko **přidat**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Nyní budete moct najdete několik nových prostředků, který v závislosti na prostředek a jakým způsobem, jsou závislé na plán služby App Service nebo webovou aplikaci. Tyto prostředky nejsou povolené podle jejich stávající definice a změnit, který se chystáte.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. V osnovou JSON, klikněte na **appInsights škálování** zvýrazněte jeho kód JSON. Toto je nastavení škálování pro plán služby App Service.
9. Vyhledejte ve zvýrazněný kód JSON `location` a `enabled` vlastnosti a nastavte je, jak je uvedeno níže.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. V osnovou JSON, klikněte na **CPUHigh appInsights** zvýrazněte jeho kód JSON. Toto je upozornění.
11. Vyhledejte `location` a `isEnabled` vlastnosti a nastavte je, jak je uvedeno níže. Totéž pro další tři výstrahy (fialové žárovky).
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Teď jste připravení nasadit. Klikněte pravým tlačítkem na projekt a vyberte **nasadit** > **nové nasazení**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Přihlaste se k účtu Azure, pokud jste tak již neučinili.
14. Vyberte existující skupinu prostředků ve vašem předplatném nebo vytvořte novou jeden, vyberte **azuredeploy.json**a potom klikněte na **upravit parametry**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Nyní budete moci upravit všechny parametry definované v souboru šablony dobrý tabulky. Parametry, které definují výchozí hodnoty budou již máte výchozí hodnoty a parametry, které definují seznam povolených hodnot se zobrazí jako rozevírací seznamy.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Zadejte všechny prázdné parametry a použít [adresu úložišti GitHub pro ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) v **repoUrl**. Potom klikněte na **Uložit**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > Automatické škálování je funkce, které nabízí v **standardní** vrstvě nebo vyšší a plán úroveň výstrahy jsou funkce nenabízí **základní** úroveň, nebo vyšší, budete muset nastavit **sku** parametru **standardní** nebo **Premium** Chcete-li zobrazit všechny nové aplikace Insights prostředky světla nahoru.
    > 
    > 
16. Klikněte na tlačítko **nasazení**. Pokud jste vybrali **ukládat hesla**, heslo bude uloženo v souboru parametrů **ve formátu prostého textu**. Jinak budete požádáni o zadání hesla databáze během procesu nasazení.

A to je vše! Teď stačí přejít na [portálu Azure](https://portal.azure.com/) a [Průzkumníka prostředků Azure](https://resources.azure.com) nástroj zobrazíte nové výstrahy a nastavení automatického škálování přidán do vaší JSON nasazené aplikace.

Vaše kroky v této části především udělat následující:

1. Připravený soubor šablony
2. Vytvořit soubor parametr pomocí souboru šablony
3. Nasadit soubor šablony s soubor parametrů

Posledním krokem je snadno provádí rutiny prostředí PowerShell. Chcete-li zjistit, co Visual Studio se po jeho nasazení vaší aplikace, otevřete Scripts\Deploy AzureResourceGroup.ps1. Existuje mnoho kódu existuje, ale právě přechod do zvýrazněte všechny příslušné kód, který potřebujete k nasazení šablony souborů a parametr.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

Rutinu poslední `New-AzureResourceGroup`, je ten, který ve skutečnosti provádí akce. To vše prokázat vám, že s pomocí nástrojů, je relativně jednoduché nasazení cloudových aplikací předvídatelné. Pokaždé, když spustíte rutinu na stejné šablony s stejný soubor parametrů, budete získáte stejný výsledek.

## <a name="summary"></a>Souhrn
V DevOps opakovatelnost a předvídatelnost jsou klíče pro všechny úspěšné nasazení velkého rozsahu aplikace skládá z mikroslužeb. V tomto kurzu jste nasadili dva mikroslužbu aplikaci do Azure jako jedna skupina prostředků pomocí šablony Azure Resource Manager. Doufáme ho udělil znalosti, které potřebujete, aby bylo možné spustit převodu šablonu aplikace v Azure a můžete zřídit a nasadit ji předvídatelné. 

<a name="resources"></a>

## <a name="more-resources"></a>Další zdroje informací
* [Jazyk šablony Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* [Vytváření šablon Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* [Funkce šablony Azure Resource Manager](../azure-resource-manager/resource-group-template-functions.md)
* [Nasazení aplikace pomocí šablony Azure Resource Manageru](../azure-resource-manager/resource-group-template-deploy.md)
* [Použití Azure PowerShellu s Azure Resource Managerem](../azure-resource-manager/powershell-azure-resource-manager.md)
* [Řešení potíží s nasazením skupin prostředků v Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md)


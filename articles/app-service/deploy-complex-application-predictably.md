---
title: Zřizování a nasazování mikroslužeb předvídatelně - službě Azure App Service
description: Zjistěte, jak nasadit aplikaci skládající se z mikroslužeb v Azure App Service jako jednu jednotku a předvídatelným způsobem použití šablony skupin prostředků JSON a skriptování Powershellu.
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
ms.custom: seodec18
ms.openlocfilehash: f9b2d1a227a210b09e64db8a61d99b216b9b362a
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2018
ms.locfileid: "53653148"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Zřizování a nasazování mikroslužeb předvídatelně v Azure
Tento kurz ukazuje, jak zřídit a nasadit aplikaci skládající se z [mikroslužeb](https://en.wikipedia.org/wiki/Microservices) v [služby Azure App Service](https://azure.microsoft.com/services/app-service/) jako jednu jednotku a předvídatelným způsobem použití šablony skupin prostředků JSON a Skriptování PowerShell. 

Při zřizování a nasazování vysoce škálovatelné aplikace, které se skládají z vysoce oddělení mikroslužeb, opakovatelnost a předvídatelnost jsou klíčové pro úspěch. [Azure App Service](https://azure.microsoft.com/services/app-service/) vám umožní vytvářet mikroslužby, které obsahují webové aplikace, back-endů mobilních a API apps. [Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md) vám umožní spravovat všechny mikroslužby jako celek, společně se závislosti prostředků, jako jsou databáze a nastavení ovládacího prvku zdroje. Teď můžete také nasadit takovou aplikaci pomocí šablon JSON a jednoduché skripty prostředí PowerShell. 

## <a name="what-you-will-do"></a>Co budete dělat
V tomto kurzu nasazujete aplikaci, která zahrnuje:

* Dvě aplikace služby App Service (tj. dva mikroslužeb)
* Back-end SQL Database
* Nastavení aplikace, připojovacích řetězců a Správa zdrojového kódu
* Application insights, oznámení, nastavení automatického škálování

## <a name="tools-you-will-use"></a>Nástroje, které budete používat
V tomto kurzu použijete následující nástroje. Protože není komplexní informace o nástroji, teď předvedu zůstat do scénáře začátku do konce a právě získáte stručný úvod ke každému, a místo, kde najdete další informace v něm. 

### <a name="azure-resource-manager-templates-json"></a>Šablony Azure Resource Manageru (JSON)
Pokaždé, když vytvoříte aplikaci v Azure App Service, například Azure Resource Manageru používá k vytvoření celou skupinu prostředků s prostředky součásti šablony JSON. Komplexní šablony z [Azure Marketplace](/azure/marketplace) může obsahovat databáze, účty úložiště, plán služby App Service, aplikace, pravidel upozornění, nastavení aplikace, nastavení automatického škálování a další, a tyto šablony jsou k dispozici pomocí prostředí PowerShell. Informace o tom, jak stáhnout a použít tyto šablony najdete v tématu [pomocí Azure Powershellu s Azure Resource Managerem](../powershell-azure-resource-manager.md).

Další informace o šablonách Azure Resource Manageru najdete v tématu [vytváření šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Azure SDK 2.6 pro Visual Studio
Nejnovější sada SDK obsahuje vylepšení podpory šablony Resource Manageru v editoru JSON. Používá se pro rychlé vytvoření zcela nové šablony skupiny prostředků nebo otevřete existující šablonu JSON (například šablonu stažené galerie) pro úpravy, naplnění souboru parametrů a i nasazení skupiny prostředků přímo z Azure Resource Skupina řešení.

Další informace najdete v tématu [Azure SDK 2.6 pro sadu Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Prostředí Azure PowerShell 0.8.0 nebo novější
Počínaje verzí 0.8.0, součástí prostředí Azure PowerShell instalace modulu Azure Resource Manageru kromě modulu Azure. Tento nový modul umožňuje skript nasazení skupiny prostředků.

Další informace najdete v tématu [pomocí Azure Powershellu s Azure Resource Manageru](../powershell-azure-resource-manager.md)

### <a name="azure-resource-explorer"></a>Průzkumník prostředků Azure
To [nástroje ve verzi preview](https://resources.azure.com) umožňuje prozkoumat definice JSON všech skupin prostředků v předplatného i jednotlivé prostředky. V nástroji můžete upravit definici JSON prostředku, odstranit celou hierarchii prostředků a vytvářet nové prostředky.  Informace v tomto nástroji snadno k dispozici je velmi užitečné pro šablony pro vytváření, protože ho se dozvíte, jaké vlastnosti, je potřeba nastavit pro konkrétní typ prostředku, správné hodnoty, atd. Můžete dokonce vytvořit skupinu prostředků v [webu Azure Portal](https://portal.azure.com/), potom zkontrolovat jeho definice JSON v nástroji Průzkumník při vytvoření šablon z skupinu prostředků.

### <a name="deploy-to-azure-button"></a>Tlačítko nasazení do Azure
Pokud používáte pro správu zdrojového kódu GitHub, můžete vložit [tlačítko nasazení do Azure](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) do vašeho souboru README. MD, což umožňuje nasazení klíč uživatelského rozhraní do Azure. Přestože můžete provést pro všechny jednoduchou aplikaci, můžete rozšířit to umožňuje nasazení celou skupinu prostředků vložením souboru azuredeploy.json v kořenovém adresáři úložiště. Tento soubor JSON, který obsahuje šablonu skupiny prostředků, použije tlačítko nasazení do Azure a vytvořte skupinu prostředků. Příklad najdete v tématu [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) vzorku, který budete používat v tomto kurzu.

## <a name="get-the-sample-resource-group-template"></a>Získat ukázkovou šablonu skupiny prostředků
Takže teď pojďme si právo na to.

1. Přejděte [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) Ukázka služby App Service.
2. V readme.md, klikněte na tlačítko **nasadit do Azure**.
3. Přejdete tak [nasadit do azure](https://deploy.azure.com) lokality a zobrazí se výzva k zadání parametrů nasazení. Všimněte si, že většina polí se vyplní názvem úložiště a některé náhodného řetězce za vás. Všechna pole můžete změnit, pokud chcete, ale pouze věcí, je nutné zadat jsou přihlášení správce SQL serveru a heslo a potom klikněte na **Další**.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Klepnutím na tlačítko **nasadit** k zahájení procesu nasazení. Jakmile proces úloha poběží do konce, klikněte na tlačítko http://todoapp *XXXX*. azurewebsites.net odkaz procházením nasazené aplikace. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   Uživatelské rozhraní by být trochu pomalé, když jste nejdřív na ni přejít pomocí vzhledem k tomu, že aplikace zrovna začínáte, ale sami přesvědčit, že je plně funkční aplikace.
5. Zpátky na stránce nasazení klikněte na tlačítko **spravovat** odkaz a prohlédněte si novou aplikaci na webu Azure Portal.
6. V **Essentials** rozevírací seznam, klikněte na odkaz skupiny prostředků. Všimněte si také, že aplikace je již připojen k úložišti Githubu v rámci **externí projekt**. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. V okně skupiny prostředků mějte na paměti, že již existují dvě aplikace a jednu databázi SQL ve skupině prostředků.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Vše, co před chvílí jste za pár minut krátký aplikace plně nasazené dvě mikroslužeb s všechny komponenty, závislosti, nastavení, databáze a nepřetržité publikování nastavil automatizované Orchestrace v Azure Resource Manageru. To vše se provádí dvě věci:

* Tlačítko nasazení do Azure
* azuredeploy.JSON v kořenovém adresáři úložiště

Stejná aplikace můžete nasadit desítky, stovky nebo tisíce časy a mají přesně stejnou konfiguraci pokaždé, když. Opakovatelnost a předvídatelnost tento přístup umožňuje snadno a s jistotou nasazovat vysoce škálovatelné aplikace.

## <a name="examine-or-edit-azuredeployjson"></a>Zkontrolujte (nebo upravit) AZUREDEPLOY. JSON
Nyní Pojďme se podívat na nastavení se úložiště GitHub. Budete používat JSON editor v sadě Azure .NET SDK, takže pokud jste ještě nenainstalovali [Azure .NET SDK 2.6](https://azure.microsoft.com/downloads/), proveďte to nyní.

1. Klonování [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) úložiště pomocí nástrojů Oblíbené git. Na snímku obrazovky níže uděláme to v Průzkumníku týmových projektů v sadě Visual Studio 2013.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. V kořenovém adresáři úložiště otevřete v sadě Visual Studio azuredeploy.json. Pokud se nezobrazí podokno pro osnovou JSON, musíte nainstalovat sadu Azure .NET SDK.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Nechystám se popisují každou podrobné informace o formátu JSON, ale [více prostředků](#resources) část obsahuje odkazy pro výuku jazyka šablony skupiny prostředků. Tady mám vytvořím ukazují, zajímavé funkce, které vám pomůžou začít s při vytvoření vlastní šablony pro nasazení aplikace.

### <a name="parameters"></a>Parametry
Podívejte se na sekci parametrů zobrazíte, že většina z těchto parametrů je co **nasadit do Azure** tlačítko vás vyzve k zadání. Lokality za **nasadit do Azure** tlačítko naplní vstupní parametry definované v azuredeploy.json pomocí uživatelského rozhraní. Tyto parametry se používají v definicích prostředků, jako jsou názvy prostředků, hodnoty vlastností, atd.

### <a name="resources"></a>Zdroje a prostředky
V uzlu prostředků uvidíte, že jsou definovány prostředky na 4 nejvyšší úrovně, včetně instanci systému SQL Server, plán služby App Service a dvě aplikace. 

#### <a name="app-service-plan"></a>Plán služby App Service
Začněme jednoduchou úrovni kořenového adresáře prostředků v kódu JSON. Osnovy JSON, klikněte na plán služby App Service s názvem **[hostingPlanName]** zvýrazněte odpovídající kód JSON. 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Všimněte si, `type` prvek určuje řetězec pro plán služby App Service (byla volána v serverové farmě dlouhý, dlouhou dobou) a další prvky a vlastnosti jsou vyplněna pomocí parametry definované v souboru JSON a tento prostředek nemá žádné vnořené prostředky.

> [!NOTE]
> Všimněte si také, že hodnota `apiVersion` Azure, kterou verzi rozhraní API REST pro použití JSON definice prostředků s a může ovlivnit, jak by měly být formátovány prostředek uvnitř zjistí, `{}`. 
> 
> 

#### <a name="sql-server"></a>SQL Server
Pak klikněte na prostředek systému SQL Server s názvem **SQLServer** osnovy JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Mějte na paměti následující skutečnosti související zvýrazněný kód JSON:

* Použití parametrů zajistí, že jsou s názvem a nakonfigurovaná tak, aby je mezi nimi vlastně konzistentní mezi sebou vytvořené prostředky.
* Prostředek systému SQL Server má dvě vnořených prostředků, každá má jinou hodnotu pro `type`.
* Vnořených prostředků uvnitř `“resources”: […]`, kde jsou definovány databáze a pravidla brány firewall, jste `dependsOn` prvek, který určuje ID prostředku prostředku systému SQL Server kořenové úrovně. Azure Resource Managerem se říká "před vytvořením tento prostředek, který už musí existovat jinému prostředku; a pokud se tento prostředek je definován v šabloně, vytvořte, že jedna nejdřív".
  
  > [!NOTE]
  > Podrobné informace o tom, jak používat `resourceId()` funkce naleznete v tématu [funkce šablon Azure Resource Manageru](../azure-resource-manager/resource-group-template-functions-resource.md#resourceid).
  > 
  > 
* Vliv `dependsOn` element je Azure Resource Manageru můžete zjistit, prostředků, které je možné vytvořit paralelně a které prostředky musí být vytvořeny postupně. 

#### <a name="app-service-app"></a>Aplikace služby App Service
Nyní pokročíme ke skutečné aplikací, které jsou složitější. Kliknutím na aplikaci [variables('apiSiteName')] v osnovou JSON, abyste měli na očích jeho kód JSON. Můžete si všimnout, že se zobrazuje mnohem zajímavější věci. V tomto případě jsem budeme mluvit o funkce jeden po druhém:

##### <a name="root-resource"></a>Kořenové prostředků
Aplikace závisí na dvou různých zdrojů. To znamená, že Azure Resource Manageru vytvoří aplikaci, až se vytvoří plán služby App Service a instanci systému SQL Server.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Nastavení aplikací
Nastavení aplikace jsou také definovány jako vnořených prostředků.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

V `properties` – element pro `config/appsettings`, máte dvě nastavení aplikace ve formátu `"<name>" : "<value>"`.

* `PROJECT` je [KUDU nastavení](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) , které sděluje nasazení v Azure, které projekt pro použití v řešení vícenásobného projektu sady Visual Studio. Vám ukážu později způsob konfigurace správy zdrojového kódu, ale protože ToDoApp kódu v řešení vícenásobného projektu sady Visual Studio, potřebujeme, aby toto nastavení.
* `clientUrl` je jednoduše aplikace nastavení, která kód aplikace používá.

##### <a name="connection-strings"></a>Připojovací řetězce
Připojovací řetězce jsou také definovány jako vnořených prostředků.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

V `properties` – element pro `config/connectionstrings`, každý připojovací řetězec je také definováno jako dvojice názvu a hodnoty ve formátu konkrétní `"<name>" : {"value": "…", "type": "…"}`. Pro `type` elementu, možné hodnoty jsou `MySql`, `SQLServer`, `SQLAzure`, a `Custom`.

> [!TIP]
> Konečné seznam typů řetězec připojení spusťte následující příkaz v prostředí Azure PowerShell: \[Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.websites.Services.WebEntities.DatabaseType")
> 
> 

##### <a name="source-control"></a>Správy zdrojového kódu
Nastavení správy zdrojových kódů jsou také definovány jako vnořených prostředků. Azure Resource Manageru používá tento prostředek konfigurace průběžné publikování (viz výstrahou na `IsManualIntegration` později) a také a podívejte se na nasazování kódu aplikace automaticky během zpracování souboru JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl` a `branch` by měl být poměrně intuitivní a by měl odkazují na úložiště Git a název větve, které chcete publikovat ze. Znovu jsou definovány ve vstupní parametry. 

Poznámka: v `dependsOn` element, který, kromě samotného, prostředku aplikace `sourcecontrols/web` závisí také na `config/appsettings` a `config/connectionstrings`. Důvodem je, že jakmile `sourcecontrols/web` je nakonfigurován, procesu nasazení v Azure se automaticky pokusí o nasazení, sestavit a spustit kód aplikace. Proto vložení této závislosti vám pomůže zajistit, že aplikace má přístup k nastavení požadovaná aplikace a připojovacích řetězců, před spuštěním kódu aplikace. 

> [!NOTE]
> Všimněte si také, že `IsManualIntegration` je nastavena na `true`. Tato vlastnost je nezbytné v tomto kurzu, protože není vlastníkem skutečně úložiště GitHub a proto nelze udělit ve skutečnosti oprávnění Azure ke konfiguraci průběžné publikování z [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (to znamená nabízených oznámení automatického úložiště aktualizace Azure). Můžete použít výchozí hodnotu `false` k zadanému úložišti pouze v případě, že jste nakonfigurovali přihlašovací údaje pro GitHub vlastníka v [webu Azure portal](https://portal.azure.com/) před. Jinými slovy Pokud nastavíte správu zdrojového kódu na Githubu nebo Bitbucketu pro libovolnou aplikaci v [webu Azure Portal](https://portal.azure.com/) dříve, pomocí vaší uživatelské přihlašovací údaje, budou pamatovat přihlašovací údaje a jejich použití pokaždé, když nasazujete libovolnou aplikaci z Azure GitHub nebo BitBucket v budoucnu. Nicméně pokud jste to ještě neudělali, nasazení šablony JSON se nezdaří Azure Resource Manageru se pokusí o konfiguraci nastavení správy zdrojového kódu aplikace, protože nemůže přihlásit do Githubu nebo Bitbucketu se přihlašovací údaje vlastníka úložiště.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>Porovnání šablony JSON se skupinou nasazených prostředků
Tady můžete projít všechny aplikace oken v [webu Azure Portal](https://portal.azure.com/), ale existuje jiný nástroj, který není stejně jako užitečné, pokud informace. Přejděte [Azure Resource Exploreru](https://resources.azure.com) nástroje ve verzi preview, který poskytuje reprezentaci JSON všech skupin prostředků ve vašem předplatném podle skutečně existuje v back-endu Azure. Uvidíte také, jak odpovídá hierarchii JSON skupinu prostředků v Azure s hierarchií v souboru šablony, který slouží k jeho vytvoření.

Například když přejdu [Azure Resource Exploreru](https://resources.azure.com) nástroj a rozbalte uzly v Průzkumníku, můžu vidět skupinu prostředků a prostředky úrovni kořenového adresáře, které jsou shromážděny v rámci jejich typy příslušných prostředků.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Pokud přejdete aplikaci, byste měli vidět podrobnosti konfigurace aplikace podobně jako následující snímek obrazovky:

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Opět vnořených prostředků by měl mít hierarchii velmi podobné těm v souboru šablony JSON a měli byste vidět nastavení aplikace, připojovacích řetězců, atd., správně projeví v podokně JSON. Nastavení tady chybí může značit problém se souborem JSON a vám můžou pomoct vyřešit váš soubor šablony JSON.

## <a name="deploy-the-resource-group-template-yourself"></a>Nasazení šablony skupiny prostředků, sami
**Nasadit do Azure** tlačítko je skvělé, ale umožňuje nasadit šablonu skupiny prostředků v azuredeploy.json pouze v případě, že azuredeploy.json již byly přesunuty do Githubu. .NET SDK služby Azure také poskytuje nástroje, které můžete nasadit libovolný soubor šablony JSON přímo z místního počítače. Chcete-li to provést, postupujte podle kroků níže:

1. V sadě Visual Studio klikněte na **Soubor** > **Nový** > **Projekt**.
2. Klikněte na tlačítko **Visual C#** > **cloudu** > **skupiny prostředků Azure**, pak klikněte na tlačítko **OK**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. V **vybrat šablonu Azure**vyberte **prázdnou šablonu** a klikněte na tlačítko **OK**.
4. Přetáhněte azuredeploy.json do **šablony** složky nového projektu.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. V Průzkumníku řešení otevřete zkopírovaný azuredeploy.json.
6. Právě představu, přidáme některé standardní prostředky Application Insights do našich souboru JSON kliknutím **přidat prostředek**. Pokud vás zajímá jenom nasazení souboru JSON, přeskočte kroky nasazení.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Vyberte **Application Insights pro webové aplikace**, zkontrolujte, zda je vybrána existující plán služby App Service a aplikaci a pak klikněte na tlačítko **přidat**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Nyní budete moci zobrazit několik nových prostředků, v závislosti na zdroji a co to dělá, mají závislosti na plánu služby App Service nebo z aplikace. Tyto prostředky nejsou povoleny podle jejich stávající definice a se chystáte změnit.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. Osnovy JSON, klikněte na tlačítko **appInsights automatického škálování** zvýrazněte jeho kód JSON. Toto je nastavení škálování pro plán služby App Service.
9. Zvýrazněný kód JSON, vyhledejte `location` a `enabled` vlastnosti a jejich nastavení, jak je znázorněno níže.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. Osnovy JSON, klikněte na tlačítko **CPUHigh appInsights** zvýrazněte jeho kód JSON. Toto je upozornění.
11. Vyhledejte `location` a `isEnabled` vlastnosti a jejich nastavení, jak je znázorněno níže. Totéž pro další tři výstrahy (fialová žárovky).
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Teď jste připravení nasadit. Klikněte pravým tlačítkem na projekt a vyberte **nasadit** > **nové nasazení**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Přihlaste se ke svému účtu Azure, pokud jste tak již neučinili.
14. Vyberte existující skupinu prostředků ve vašem předplatném nebo vytvořit nové z nich, vyberte **azuredeploy.json**a potom klikněte na tlačítko **upravit parametry**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Nyní budete moci upravovat všechny parametry definované v souboru šablony v dobré tabulce. Parametry, které definují výchozí hodnoty již mají výchozí hodnoty a parametry, které definují seznam povolených hodnot se zobrazí jako rozevíracích seznamech.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Zadejte všechny prázdné parametry a použít [adresu úložiště GitHub pro ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) v **repoUrl**. Potom klikněte na **Uložit**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > Automatické škálování je funkce nabízené v **standardní** vrstvy nebo vyšší a úrovně plánu upozornění jsou funkcí, které nabízí v **základní** vrstvy nebo vyšší, budete muset nastavit **sku** parametr k **standardní** nebo **Premium** Chcete-li zobrazit všechny nové App Insights prostředky osvětlí.
    > 
    > 
16. Klikněte na tlačítko **nasazení**. Pokud jste vybrali **ukládat hesla**, heslo bude uloženo v souboru parametrů **ve formátu prostého textu**. V opačném případě budete vyzváni k zadání hesla databáze během procesu nasazení.

A to je vše! Teď stačí přejít na [webu Azure Portal](https://portal.azure.com/) a [Azure Resource Exploreru](https://resources.azure.com) nástroj zobrazíte nové výstrahy a nastavení automatického škálování, které jsou přidány do vaše struktury JSON nasazené aplikace.

Vaše kroky v této části hlavně provedli následující:

1. Připravený soubor šablony
2. Vytvoří soubor s parametry pro soubor šablony
3. Nasadit soubor šablony pomocí souboru parametrů

Posledním krokem se snadno provádí pomocí rutiny Powershellu. Chcete-li zjistit, co Visual Studio nebyla při nasazení vaší aplikace, otevřete Scripts\Deploy AzureResourceGroup.ps1. Dochází k mnoha kódu existuje, ale já zvolím právě zvýrazněte všechny relevantní kód, musíte nasadit soubor šablony s soubor parametrů.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

Poslední rutinu `New-AzureResourceGroup`, je ten, který ve skutečnosti provádí akci. To vše prokázat vám, že pomocí nástrojů, je poměrně přímočarý předvídatelně nasadit cloudové aplikace. Pokaždé, když spustíte rutinu na stejnou šablonu se stejným souborem parametr, budete získáte stejný výsledek.

## <a name="summary"></a>Souhrn
V DevOps opakovatelnost a předvídatelnost jsou klíče pro všechny úspěšné nasazení vysoce škálovatelné aplikace tvořené mikroslužbami. V tomto kurzu jste nasadili dvě mikroslužeb aplikace do Azure jako jedinou skupinu prostředků pomocí šablony Azure Resource Manageru. Snad to udělil znalostní báze, které potřebujete, aby bylo možné spustit vaši aplikaci v Azure převodu do šablony a můžete zřídit a nasadit ho předvídatelným způsobem. 

<a name="resources"></a>

## <a name="more-resources"></a>Další zdroje informací
* [Jazyk šablony Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md)
* [Tvorba šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md)
* [Funkce šablon Azure Resource Manageru](../azure-resource-manager/resource-group-template-functions.md)
* [Nasazení aplikace pomocí šablony Azure Resource Manageru](../azure-resource-manager/resource-group-template-deploy.md)
* [Použití Azure PowerShellu s Azure Resource Managerem](../azure-resource-manager/powershell-azure-resource-manager.md)
* [Řešení potíží s nasazeními skupin prostředků v Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md)


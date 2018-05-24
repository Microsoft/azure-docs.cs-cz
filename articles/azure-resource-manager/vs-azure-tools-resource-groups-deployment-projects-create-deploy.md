---
title: Projekty skupiny prostředků Azure v sadě Visual Studio | Dokumentace Microsoftu
description: Pomocí sady Visual Studio můžete vytvořit projekt skupiny prostředků Azure a nasadit tyto prostředky do Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 4bd084c8-0842-4a10-8460-080c6a085bec
ms.service: azure-resource-manager
ms.devlang: multiple
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/09/2018
ms.author: tomfitz
ms.openlocfilehash: bd2869b35d92ea92261223131476d7cc8eb854eb
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360100"
---
# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Vytvoření a nasazení skupiny prostředků Azure pomocí sady Visual Studio
Pomocí sady Visual Studio a [Azure SDK](https://azure.microsoft.com/downloads/) můžete vytvořit projekt, který nasadí vaši infrastrukturu a kód do Azure. Můžete například definovat webového hostitele, webový server a databázi pro vaši aplikaci a nasadit tuto infrastrukturu spolu s kódem. Nebo můžete definovat virtuální počítač, virtuální síť a účet úložiště a nasadit tuto infrastrukturu spolu se skriptem, který se spouští na virtuálním počítači. Projekt nasazení **skupiny prostředků Azure** umožňuje nasadit všechny potřebné prostředky v rámci jedné opakovatelné operace. Další informace o nasazení a správě prostředků najdete v tématu [Přehled Azure Resource Manageru](resource-group-overview.md).

Projekty skupiny prostředků Azure obsahují šablony JSON pro Azure Resource Manager, které definují prostředky, nasazované do Azure. Další informace o jednotlivých prvcích šablony Resource Manageru najdete v tématu o [vytváření šablon Azure Resource Manageru](resource-group-authoring-templates.md). Visual Studio umožňuje upravit tyto šablony a poskytuje nástroje, které práci s nimi usnadňují.

V tomto článku nasadíte webovou aplikaci a SQL Database. Postup je však téměř stejný pro libovolný typ prostředku. Stejně snadno můžete nasadit virtuální počítač a prostředky, které s ním souvisejí. Visual Studio poskytuje řadu různých předem připravených šablon pro běžné scénáře nasazení.

Tento článek ukazuje Visual Studio 2017. Pokud používáte Visual Studio 2015 Update 2 a Microsoft Azure SDK pro .NET 2.9, nebo Visual Studio 2013 a Azure SDK 2.9, prostředí je z velké části stejné. Sadu Azure SDK můžete využít ve verzi 2.6 nebo novější. Podoba uživatelského rozhraní se ale může lišit od příkladů uvedených v tomto článku. Důrazně doporučujeme, abyste si před zahájením těchto kroků nainstalovali nejnovější verzi sady [Azure SDK](https://azure.microsoft.com/downloads/). 

## <a name="create-azure-resource-group-project"></a>Vytvoření projektu skupiny prostředků Azure
V tomto postupu vytvoříte projekt skupiny prostředků Azure pomocí šablony **Web app + SQL** (Webová aplikace a SQL).

1. V sadě Visual Studio zvolte **Soubor**, **Nový projekt** a potom vyberte **C#** nebo **Visual Basic** (To, který jazyk zvolíte, nemá žádný vliv na pozdější fáze, protože tyto projekty obsahují jenom obsah JSON a PowerShell). Potom vyberte **Cloud** a projekt **Azure Resource Group** (Skupina prostředků Azure).
   
    ![Projekt nasazení v cloudu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-project.png)
2. Zvolte šablonu, kterou chcete nasadit do Azure Resource Manageru. Všimněte si, že máte spoustu různých možností v závislosti na typu projektu, který chcete nasadit. Pro tento článek si vyberte šablonu **Web app + SQL** (Webová aplikace a SQL).
   
    ![Volba šablony](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)
   
    Šablona, kterou vyberete, je jenom výchozí bod, podle potřeb vašeho scénáře můžete prostředky přidat nebo odebrat.
   
   > [!NOTE]
   > Visual Studio načte seznam dostupných šablon online. Seznam se může změnit.
   > 
   > 
   
    Visual Studio vytvoří projekt nasazení skupiny prostředků pro webovou aplikaci a SQL Database.
3. Pokud si chcete vytvořený projekt prohlédnout, projděte si uzly v projektu nasazení.
   
    ![zobrazení uzlů](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)
   
    Vzhledem k tomu, že jsme pro tento příklad vybrali šablonu Web app + SQL, zobrazí se následující soubory: 
   
   | Název souboru | Popis |
   | --- | --- |
   | Deploy-AzureResourceGroup.ps1 |Skript PowerShellu, který vyvolá příkazy PowerShellu pro nasazení Azure Resource Manageru.<br />**Poznámka** Sada Visual Studio tento skript PowerShellu využívá k nasazení vaší šablony. Libovolné změny tohoto skriptu ovlivní také nasazení v sadě Visual Studio, proto buďte opatrní. |
   | WebSiteSQLDatabase.json |Šablona Resource Manageru, která definuje infrastrukturu, kterou chcete nasadit do Azure, a parametry, které můžete během nasazení zadat. Definuje také závislosti mezi prostředky, takže je Resource Manager nasadí ve správném pořadí. |
   | WebSiteSQLDatabase.parameters.json |Soubor parametrů, který obsahuje hodnoty požadované šablonou. Předáním hodnot těchto parametrů přizpůsobujete jednotlivá nasazení. |
   
    Tyto základní soubory obsahují všechny projekty nasazení skupiny prostředků. Ostatní projekty mohou obsahovat i další soubory, které podporují jiné funkce.

## <a name="customize-the-resource-manager-template"></a>Přizpůsobení šablony Resource Manageru
Projekt nasazení lze přizpůsobit úpravou šablony JSON s popisem prostředků, které chcete nasadit. JSON je zkratka pro JavaScript Object Notation. Jedná se o serializovaný datový formát, se kterým se snadno pracuje. Soubory JSON využívají schéma, na které se odkazujete na začátku každého souboru. Pokud chcete schématu lépe porozumět, můžete si ho stáhnout a prohlédnout. Schéma definuje platné prvky, typy a formáty polí, přípustné výčtové hodnoty a tak dále. Další informace o jednotlivých prvcích šablony Resource Manageru najdete v tématu o [vytváření šablon Azure Resource Manageru](resource-group-authoring-templates.md).

Pokud chcete se svou šablonou začít pracovat, otevřete **WebSiteSQLDatabase.json**.

Editor sady Visual Studio poskytuje nástroje, které vám s úpravami šablony Resource Manageru pomohou. Okno **JSON Outline** (Osnova JSON) usnadňuje zobrazení prvků, které jsou v šabloně definované.

![zobrazení osnovy JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

Výběrem libovolné prvku v osnově přejdete k příslušné části šablony a zvýrazní se odpovídající JSON.

![navigace JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

Pokud chcete přidat prostředek, můžete buď použít tlačítko **Přidat prostředek** v horní části okna s osnovou JSON, nebo kliknout pravým tlačítkem na **resources** a vybrat **Přidat nový prostředek**.

![přidání prostředku](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

V tomto kurzu vyberte **účet úložiště** a pojmenujte ho. Zadejte název, který nebude delší než 11 znaků. Obsahovat smí jenom čísla a malá písmena.

![přidání úložiště](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

Všimněte si, že se přidal nejenom tento prostředek, ale také parametr pro typ účtu úložiště a proměnná pro název účtu úložiště.

![zobrazení osnovy](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

Parametr **storageType** je předdefinovaný a obsahuje povolené typy a také výchozí typ. Tyto hodnoty můžete ponechat beze změny nebo je můžete podle potřeby upravit. Pokud nechcete, aby pomocí této šablony nasazoval účet úložiště **Premium_LRS**, stačí ho odebrat z povolených typů. 

```json
"storageType": {
  "type": "string",
  "defaultValue": "Standard_LRS",
  "allowedValues": [
    "Standard_LRS",
    "Standard_ZRS",
    "Standard_GRS",
    "Standard_RAGRS"
  ]
}
```

Visual Studio také poskytuje technologii IntelliSense, která vám pomůže pochopit, které vlastnosti jsou při úpravě šablony dostupné. Pokud chcete třeba upravit vlastnosti pro plán služby App Service, přejděte k prostředku **HostingPlan** a přidejte hodnotu **properties**. Všimněte si, že IntelliSense zobrazuj dostupné hodnoty a poskytuje k nim také popis.

![zobrazení technologie IntelliSense](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

Hodnotu **numberOfWorkers** můžete nastavit na 1.

```json
"properties": {
  "name": "[parameters('hostingPlanName')]",
  "numberOfWorkers": 1
}
```

## <a name="deploy-the-resource-group-project-to-azure"></a>Nasazení projektu skupiny prostředků do Azure
Nyní jste připraveni svůj projekt nasadit. Když nasadíte projekt skupiny prostředků Azure, nasadíte ho do skupiny prostředků Azure. Skupina prostředků je logické seskupení prostředků, které sdílejí společný životní cyklus.

1. V místní nabídce uzlu projektu nasazení zvolte **Nasadit** > **Nový**.
   
    ![Položka nabídky Nasadit, Nové nasazení](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/deploy.png)
   
    Zobrazí se dialogové okno **Deploy to Resource Group** (Nasadit do skupiny prostředků).
   
    ![Dialogové okno nasazení do skupiny prostředků](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)
2. V rozevíracím seznamu **Skupina prostředků** vyberte existující skupinu prostředků nebo vytvořte novou. Pokud chcete vytvořit novou skupinu prostředků, otevřete rozevírací seznam **Skupina prostředků** a vyberte **Vytvořit novou**.
   
    ![Dialogové okno nasazení do skupiny prostředků](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-new-group.png)
   
    Zobrazí se dialogové okno **Vytvořit skupinu prostředků**. Přidělte skupině název a umístění a stiskněte **Vytvořit**.
   
    ![Dialogové okno vytvoření skupiny prostředků](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-resource-group.png)
3. Výběrem tlačítka **Upravit parametry** můžete upravit parametry pro nasazení.
   
    ![Tlačítko Upravit parametry](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/edit-parameters.png)
4. Zadejte hodnoty prázdných parametrů a stiskněte tlačítko **Uložit**. Prázdnými parametry jsou **hostingPlanName**, **administratorLogin**, **administratorLoginPassword** a **databaseName**.
   
    **hostingPlanName** určuje název [plánu služby App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), který chcete vytvořit. 
   
    **administratorLogin** určuje uživatelské jméno správce SQL Serveru. Nepoužívejte běžné názvy správců, například **sa** nebo **admin**. 
   
    **administratorLoginPassword** určuje heslo správce SQL Serveru. Možnost **Ukládat hesla do souboru parametrů jako prostý text** není bezpečná. Proto ji nepoužívejte. Vzhledem k tomu, že heslo není uložené jako prostý text, budete ho během nasazení muset zadat znovu. 
   
    **databaseName** určuje název databáze, kterou chcete vytvořit. 
   
    ![Dialogové okno Upravit parametry](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/provide-parameters.png)
5. Stisknutím tlačítka **Nasadit** nasadíte projekt do Azure. Otevře se powershellová konzola mimo instanci sady Visual Studio. Po zobrazení výzvy zadejte v powershellové konzole heslo správce SQL Serveru. **Konzola PowerShellu může být skrytá za jinými položkami nebo minimalizovaná na hlavním panelu.** Najděte ji, vyberte ji a zadejte heslo.
   
   > [!NOTE]
   > Sada Visual Studio vás může požádat o instalaci rutin Azure PowerShell. Rutiny Azure PowerShell jsou zapotřebí k úspěšnému nasazení skupin prostředků. Pokud k tomu budete vyzváni, nainstalujte je. Další informace najdete v článku [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-azurerm-ps).
   > 
   > 
6. Nasazení může zabrat několik minut. V oknech **Výstup** se zobrazí stav nasazení. Po dokončení nasazení vám poslední zpráva oznámí úspěšné nasazení. Bude vypadat zhruba následovně:
   
        ... 
        18:00:58 - Successfully deployed template 'websitesqldatabase.json' to resource group 'DemoSiteGroup'.
7. V prohlížeči otevřete web [Azure portal](https://portal.azure.com/) a přihlaste se ke svému účtu. Chcete-li skupinu prostředků zobrazit, klikněte na odkaz **Skupiny prostředků** a pak na skupinu, do které jste provedli nasazení.
   
    ![výběr skupiny](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-group.png)
8. Zobrazí se všechny nasazené prostředky. Všimněte si, že název účtu úložiště není přesně stejný jako název, který jste zadali během přidávání tohoto prostředku. Účet úložiště musí být jedinečný. Šablona k názvu automaticky přidá řetězec znaků, které zajistí jedinečnost názvu. 
   
    ![zobrazení prostředků](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)
9. Pokud provedete změny a chcete projekt znovu nasadit, vyberte si stávající skupinu prostředků z místní nabídky projektu skupiny prostředků Azure. V místní nabídce zvolte **Nasadit** a pak vyberte skupinu prostředků, kterou jste nasadili.
   
    ![Nasazená skupina prostředků Azure](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

## <a name="deploy-code-with-your-infrastructure"></a>Nasazení kódu do vaší infrastruktury
V tomto okamžiku jste nasadili infrastrukturu pro vaši aplikaci, ale zatím není v projektu nasazený žádný kód. Tento článek ukazuje, jak nasadit webovou aplikaci a tabulky SQL Database. Pokud nasazujete namísto webové aplikace virtuální počítač, budete v něm asi chtít spouštět nějaký kód. Proces nasazení kódu pro webovou aplikaci nebo pro virtuální počítač je téměř stejný.

1. Přidejte projekt do řešení sady Visual Studio. Klikněte pravým tlačítkem na řešení a vyberte **Přidat** > **Nový projekt**.
   
    ![přidání projektu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-project.png)
2. Přidejte **webovou aplikaci ASP.NET**. 
   
    ![přidání webové aplikace](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)
3. Vyberte **MVC**.
   
    ![výběr MVC](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-mvc.png)
4. Když sada Visual Studio vytvoří webovou aplikaci, uvidíte oba projekty v řešení.
   
    ![zobrazení projektů](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-projects.png)
5. Teď potřebujete zajistit, aby projekt skupiny prostředků věděl o novém projektu. Vraťte se do projektu skupiny prostředků (AzureResourceGroup1). Klikněte pravým tlačítkem na **Odkazy** a vyberte **Přidat odkaz**.
   
    ![přidání odkazu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-new-reference.png)
6. Vyberte vámi vytvořený projekt webové aplikace.
   
    ![přidání odkazu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)
   
    Přidáním odkazu propojíte projekt webové aplikace s projektem skupiny prostředků a automaticky nastavíte tři klíčové vlastnosti. Tyto vlastnosti se zobrazují v okně **Vlastnosti**.
   
      ![zobrazení odkazu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)
   
    Mezi vlastnosti patří:
   
   * Pole **Additional Properties** (Další vlastnosti) obsahuje pracovní umístění balíčku pro nasazení webu, který se vloží do úložiště Azure. Poznamenejte si složku (ExampleApp) a soubor (package.zip). Tyto hodnoty musíte znát, protože je uvádíte jako parametry při nasazování aplikace. 
   * Pole **Include File Path** (Cesta k souboru pro zahrnutí) obsahuje cestu, kde je balíček vytvořen. Pole **Include Targets** (Zahrnout cíle) obsahuje příkaz, který proces nasazení provede. 
   * Výchozí hodnota **Build;Package** umožňuje sestavení a vytvoření balíčku pro nasazení webu (package.zip).  
     
     Profil publikování není potřeba, protože nasazení získá informace potřebné k vytvoření balíčku z nastavených vlastností.
7. Vraťte se k souboru WebSiteSQLDatabase.json a přidejte prostředek do šablony.
   
    ![přidání prostředku](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource-2.png)
8. Tentokrát vyberte **Nasazení webu pro webové aplikace**. 
   
    ![přidání nasazení webu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)
9. Znovu nasaďte projekt skupiny prostředků do skupiny prostředků. Tentokrát použijeme několik nových parametrů. Není třeba zadávat hodnoty pro **_artifactsLocation** nebo **_artifactsLocationSasToken**, ty sada Visual Studio vygeneruje automaticky. Název složky a souboru jste však nastavili do cesty, která obsahuje balíček pro nasazení (zobrazené na následujícím obrázku jako **ExampleAppPackageFolder** a **ExampleAppPackageFileName**). Zadejte hodnoty, které jste viděli v referenčních vlastnostech (**ExampleApp** a **package.zip**).
   
    ![přidání nasazení webu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/set-new-parameters.png)
   
    V případě možnosti **Účet úložiště artefaktů** vyberte účet nasazený s touto skupinou prostředků.
10. Po dokončení nasazení vyberte na portálu svojí webovou aplikaci. Vyberte adresu URL a přejděte na web.
    
     ![procházení webu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/browse-site.png)
11. Všimněte si, že jste úspěšně nasadili výchozí aplikaci ASP.NET.
    
     ![zobrazení nasazené aplikace](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## <a name="add-an-operations-dashboard-to-your-deployment"></a>Přidání řídicího panelu operací do nasazení
Teď, když jsme vytvořili řešení, je čas na poslední krok, kterým je jeho zprovoznění. Nejste omezeni pouze na prostředky dostupné prostřednictvím rozhraní sady Visual Studio. Můžeme využít sdílené řídicí panely, které se definují jako prostředky v JSON. Provedeme to tak, že upravíme šablonu a přidáme vlastní prostředek. 

1. Otevřete soubor WebsiteSqlDeploy.json a přidejte následující bok kódu za prostředek účtu úložiště, ale před uzavírající závorku ] části resources (prostředky).

```json
    ,{
      "properties": {
        "lenses": {
          "0": {
            "order": 0,
            "parts": {
              "0": {
                "position": {
                  "x": 0,
                  "y": 0,
                  "colSpan": 4,
                  "rowSpan": 6
                },
                "metadata": {
                  "inputs": [
                    {
                      "name": "resourceGroup",
                      "isOptional": true
                    },
                    {
                      "name": "id",
                      "value": "[resourceGroup().id]",
                      "isOptional": true
                    }
                  ],
                  "type": "Extension/HubsExtension/PartType/ResourceGroupMapPinnedPart"
                }
              },
              "1": {
                "position": {
                  "x": 4,
                  "y": 0,
                  "rowSpan": 3,
                  "colSpan": 4
                },
                "metadata": {
                  "inputs": [],
                  "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                  "settings": {
                    "content": {
                      "settings": {
                        "content": "__Customizations__\n\nUse this dashboard to create and share the operational views of services critical to the application performing. To customize simply pin components to the dashboard and then publish when you're done. Others will see your changes when you publish and share the dashboard.\n\nYou can customize this text too. It supports plain text, __Markdown__, and even limited HTML like images <img width='10' src='https://portal.azure.com/favicon.ico'/> and <a href='https://azure.microsoft.com' target='_blank'>links</a> that open in a new tab.\n",
                        "title": "Operations",
                        "subtitle": "[resourceGroup().name]"
                      }
                    }
                  }
                }
              }
            }
          }
        },
        "metadata": {
          "model": {
            "timeRange": {
              "value": {
                "relative": {
                  "duration": 24,
                  "timeUnit": 1
                }
              },
              "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
            }
          }
        }
      },
      "apiVersion": "2015-08-01-preview",
      "name": "[concat('ARM-',resourceGroup().name)]",
      "type": "Microsoft.Portal/dashboards",
      "location": "[resourceGroup().location]",
      "tags": {
        "hidden-title": "[concat('OPS-',resourceGroup().name)]"
      }
    }
}
```

2. Znovu nasaďte skupinu prostředků. Když se podíváte na řídicí panel na webu Azure Portal, uvidíte, že se sdílený řídicí panel přidal do seznamu nabízených možností. 

    ![Vlastní řídicí panel](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/view-custom-dashboards.png)



   > [!NOTE] 
   > Přístup k řídicímu panelu je možné spravovat pomocí skupin řízení přístupu na základě role. Přizpůsobení je možné do prostředku publikovat i po jeho nasazení. Mějte na paměti, že po opětovném nasazení se skupina prostředků v šabloně resetuje na výchozí hodnotu. Měli byste zvážit aktualizaci šablony s použitím přizpůsobení. Nápovědu k tomu, jak to provést, najdete v tématu [Vytváření řídicích panelů Azure prostřednictvím kódu programu](../azure-portal/azure-portal-dashboards-create-programmatically.md).


    ![Vlastní řídicí panel](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/Ops-DemoSiteGroup-dashboard.png)
    
    
## <a name="next-steps"></a>Další kroky
* Další informace o správě prostředků prostřednictvím tohoto portálu najdete v článku [Použití webu Azure Portal ke správě prostředků Azure](resource-group-portal.md).
* Další informace o šablonách najdete v tématu o [vytváření šablon Azure Resource Manageru](resource-group-authoring-templates.md).


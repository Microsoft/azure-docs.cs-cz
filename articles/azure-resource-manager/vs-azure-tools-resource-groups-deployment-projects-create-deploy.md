---
title: Vytvoření a nasazení projekty skupiny prostředků Visual Studio v Azure
description: Vytvoření projektu skupiny prostředků Azure a nasadit prostředky do Azure pomocí sady Visual Studio.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: tomfitz
ms.openlocfilehash: 8677d906375853bdde5c192c86dacc7479f2e31e
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2019
ms.locfileid: "67311327"
---
# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Vytvoření a nasazení skupiny prostředků Azure pomocí sady Visual Studio

Pomocí sady Visual Studio můžete vytvořit projekt, který nasadí vaši infrastrukturu a kód do Azure. Například můžete nasadit webový hostitel, webu a kód pro webový server. Visual Studio poskytuje řadu různých předem připravených šablon pro běžné scénáře nasazení. V tomto článku nasadíte webovou aplikaci.  

Tento článek popisuje, jak používat [sady Visual Studio 2019 nebo novější s Azure – vývoj a nainstalovaných úlohách ASP.NET](/visualstudio/install/install-visual-studio?view=vs-2019). Pokud používáte Visual Studio 2017, prostředí je z velké části stejný.

## <a name="create-azure-resource-group-project"></a>Vytvoření projektu skupiny prostředků Azure

V této části vytvoříte projekt skupiny prostředků Azure s **webovou aplikaci** šablony.

1. V sadě Visual Studio, zvolte **souboru**, **nový**, a **projektu**. Vyberte **skupiny prostředků Azure** šablony projektu a **Další**.

    ![Vytvoření projektu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-project.png)

1. Zadejte název vašeho projektu. Ostatní výchozí nastavení jsou pravděpodobně v pořádku, ale zkontrolujte je, aby fungují pro vaše prostředí. Až budete hotovi, vyberte **Vytvořit**.

    ![Vytvoření projektu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/name-project.png)

1. Zvolte šablonu, kterou chcete nasadit do Azure Resource Manageru. Všimněte si, že máte spoustu různých možností v závislosti na typu projektu, který chcete nasadit. Pro účely tohoto článku, zvolte **webovou aplikaci** šablony a **OK**.

    ![Volba šablony](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)

    Šablona, kterou vyberete, je jenom výchozí bod, podle potřeb vašeho scénáře můžete prostředky přidat nebo odebrat.

1. Visual Studio vytvoří projekt nasazení skupiny prostředků pro webovou aplikaci. Pokud chcete zobrazit soubory pro váš projekt, projděte si uzly v projektu nasazení.

    ![zobrazení uzlů](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)

    Vzhledem k tomu, že jste zvolili šablony webové aplikace, zobrazí se následující soubory:

   | Název souboru | Popis |
   | --- | --- |
   | Deploy-AzureResourceGroup.ps1 |Skript PowerShellu, který spustí příkazy PowerShellu pro nasazení Azure Resource Manageru. Visual Studio tento skript Powershellu využívá k nasazení vaší šablony. |
   | WebSite.json |Šablona Resource Manageru, která definuje infrastrukturu, kterou chcete nasadit do Azure, a parametry, které můžete během nasazení zadat. Definuje také závislosti mezi prostředky, takže je Resource Manager nasadí ve správném pořadí. |
   | WebSite.parameters.json |Soubor parametrů, který má hodnoty požadované šablonou. Předáním hodnot těchto parametrů přizpůsobujete jednotlivá nasazení. |

    Tyto základní soubory mají všechny projekty nasazení skupiny prostředků. Ostatní projekty můžou mít i další soubory, které podporují jiné funkce.

## <a name="customize-resource-manager-template"></a>Přizpůsobení šablony Resource Manageru

Projekt nasazení lze přizpůsobit úpravou šablony Resource Manageru, který popisuje prostředky, které chcete nasadit. Další informace o jednotlivých prvcích šablony Resource Manageru najdete v tématu o [vytváření šablon Azure Resource Manageru](resource-group-authoring-templates.md).

1. Chcete-li pracovat v šabloně, otevřete **WebSite.json**.

1. Editor sady Visual Studio poskytuje nástroje, které vám s úpravami šablony Resource Manageru pomohou. Okno **JSON Outline** (Osnova JSON) usnadňuje zobrazení prvků, které jsou v šabloně definované.

   ![zobrazení osnovy JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

1. Výběr prvku v osnově přejdete k příslušné části šablony.

   ![navigace JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

1. Pokud chcete přidat prostředek, můžete buď použít tlačítko **Přidat prostředek** v horní části okna s osnovou JSON, nebo kliknout pravým tlačítkem na **resources** a vybrat **Přidat nový prostředek**.

   ![Přidat prostředek](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

1. Vyberte **účtu úložiště** a pojmenujte ho. Zadejte název, který nebude delší než 11 znaků. Obsahovat smí jenom čísla a malá písmena.

   ![Přidání úložiště](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

1. Všimněte si, že se přidal nejenom tento prostředek, ale také parametr pro typ účtu úložiště a proměnná pro název účtu úložiště.

   ![Zobrazit osnovu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

1. Parametr pro typ účtu úložiště je předem definované povolené typy a výchozího typu. Tyto hodnoty můžete ponechat beze změny nebo je můžete podle potřeby upravit. Pokud nechcete, aby pomocí této šablony někdo nasadil účet úložiště **Premium_LRS**, stačí ho odebrat z povolených typů.

   ```json
   "demoaccountType": {
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

1. Visual Studio také poskytuje technologii intellisense, abychom vám pomohou pochopit, vlastnosti, které jsou k dispozici při úpravách v šabloně. Pokud chcete třeba upravit vlastnosti pro plán služby App Service, přejděte k prostředku **HostingPlan** a přidejte hodnotu **properties**. Všimněte si, že IntelliSense zobrazuj dostupné hodnoty a poskytuje k nim také popis.

   ![zobrazení technologie intellisense](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

   Můžete nastavit **numberOfWorkers** na hodnotu 1 a uložte soubor.

   ```json
   "properties": {
     "name": "[parameters('hostingPlanName')]",
     "numberOfWorkers": 1
   }
   ```

1. Otevřít **WebSite.parameters.json** souboru. Použít soubor parametrů k předávání hodnot během nasazení, které upravit prostředek nasazuje. Zadejte název plánu hostování a soubor uložte.

   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "hostingPlanName": {
         "value": "demoHostPlan"
       }
     }
   }
   ```

## <a name="deploy-project-to-azure"></a>Nasazení projektu do Azure

Nyní jste připraveni nasadit svůj projekt do skupiny prostředků.

Ve výchozím nastavení používá modul AzureRM skript prostředí PowerShell (Deploy-AzureResourceGroup.ps1) v projektu. Pokud stále máte modul AzureRM nainstalovaný a chcete ho chcete dál používat, můžete použít tento skript výchozí. Pomocí tohoto skriptu můžete pomocí rozhraní sady Visual Studio k nasazení svého řešení.

Ale pokud jste migrovali na nové [Az modulu](/powershell/azure/new-azureps-module-az), budete muset přidat nový skript do svého projektu. Chcete-li přidat skript, který používá modul Az, zkopírujte [nasadit AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) skriptu a přidejte ho do projektu. Pokud chcete použít tento skript pro nasazení, musíte ho spustit z konzole Powershellu, nikoli pomocí rozhraní sady Visual Studio nasazení.

V tomto článku jsou uvedeny oba přístupy. Tento článek odkazuje na výchozí skript jako skript modulu AzureRM a nový skript jako skript Az modulu.

### <a name="az-module-script"></a>AZ modulu skriptu

Pro modul skriptu Az otevřete konzolu Powershellu a spusťte:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory . -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json
```

### <a name="azurerm-module-script"></a>Skript modulu AzureRM

Pro skript modulu AzureRM pomocí sady Visual Studio:

1. V místní nabídce uzlu projektu nasazení zvolte **Nasadit** > **Nový**.

    ![Nové nasazení položky nabídky](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/deploy.png)

1. Zobrazí se dialogové okno **Deploy to Resource Group** (Nasadit do skupiny prostředků). V rozevíracím seznamu **Skupina prostředků** vyberte existující skupinu prostředků nebo vytvořte novou. Vyberte **Nasadit**.

    ![Nasazení do skupiny prostředků – dialogové okno](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)

1. V oknech **Výstup** se zobrazí stav nasazení. Po dokončení nasazení vám poslední zpráva oznámí úspěšné nasazení. Bude vypadat zhruba následovně:

   ```output
   18:00:58 - Successfully deployed template 'website.json' to resource group 'ExampleAppDeploy'.
   ```

## <a name="view-deployed-resources"></a>Zobrazení nasazené prostředky

Podíváme se na výsledky.

1. V prohlížeči otevřete web [Azure portal](https://portal.azure.com/) a přihlaste se ke svému účtu. Chcete-li skupinu prostředků zobrazit, klikněte na odkaz **Skupiny prostředků** a pak na skupinu, do které jste provedli nasazení.

1. Zobrazí se všechny nasazené prostředky. Všimněte si, že název účtu úložiště není přesně stejný jako název, který jste zadali během přidávání tohoto prostředku. Účet úložiště musí být jedinečný. Šablona automaticky přidá řetězec znaků pro název, který jste zadali do vytvořit jedinečný název.

    ![zobrazení prostředků](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)

## <a name="add-code-to-project"></a>Přidání kódu do projektu

V tomto okamžiku jste nasadili infrastrukturu pro vaši aplikaci, ale zatím není v projektu nasazený žádný kód.

1. Přidejte projekt do řešení sady Visual Studio. Klikněte pravým tlačítkem na řešení a vyberte **Přidat** > **Nový projekt**.

    ![Přidání projektu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-project.png)

1. Přidat **webová aplikace ASP.NET Core**.

    ![Přidání webové aplikace](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)

1. Zadejte název vaší webové aplikace a vyberte **vytvořit**.

    ![Název webové aplikace](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/name-web-app.png)

1. Vyberte **webovou aplikaci** a **vytvořit**.

    ![Výběr webové aplikace](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project-type.png)

1. Když sada Visual Studio vytvoří webovou aplikaci, uvidíte oba projekty v řešení.

    ![Zobrazit projekty](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-projects.png)

1. Teď potřebujete zajistit, aby projekt skupiny prostředků věděl o novém projektu. Přejděte zpět do projektu skupiny prostředků (ExampleAppDeploy). Klikněte pravým tlačítkem na **Odkazy** a vyberte **Přidat odkaz**.

    ![Přidat odkaz](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-new-reference.png)

1. Vyberte vámi vytvořený projekt webové aplikace.

   ![Přidat odkaz](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)

   Přidáním odkazu propojíte projekt webové aplikace projektu skupiny prostředků a některé vlastnosti nastaví automaticky. Tyto vlastnosti se zobrazují v okně **Vlastnosti**. Pole **Include File Path** (Cesta k souboru pro zahrnutí) obsahuje cestu, kde je balíček vytvořený. Poznamenejte si složku (ExampleApp) a soubor (package.zip). Tyto hodnoty musíte znát, protože je uvádíte jako parametry při nasazování aplikace.

   ![Přečtěte si referenční informace](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)

1. Přejděte zpět do šablony (WebSite.json) a přidejte prostředek do šablony.

    ![Přidat prostředek](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource-2.png)

1. Tentokrát vyberte **Nasazení webu pro webové aplikace**. 

    ![Přidání webu nasadit](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)

   Uložte šablonu.

1. Existuje několik nových parametrů v šabloně. Byly přidány v předchozím kroku. Není nutné zadávat hodnoty pro **_artifactsLocation** nebo **_artifactsLocationSasToken** vzhledem k tomu, že tyto hodnoty jsou automaticky generovány. Ale budete muset nastavit složku a název souboru do cesty, který obsahuje balíček pro nasazení. Názvy těchto parametrů končit **PackageFolder** a **PackageFileName**. První část názvu je název prostředku nasazení webu, který jste přidali. V tomto článku jste název **ExampleAppPackageFolder** a **ExampleAppPackageFileName**. 

   Otevřít **Website.parameters.json** a nastavte tyto parametry na hodnoty, které jste viděli v referenčních vlastnostech. Nastavte **ExampleAppPackageFolder** k názvu složky. Nastavte **ExampleAppPackageFileName** k názvu souboru zip.

   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "hostingPlanName": {
         "value": "demoHostPlan"
       },
       "ExampleAppPackageFolder": {
         "value": "ExampleApp"
       },
       "ExampleAppPackageFileName": {
         "value": "package.zip"
       }
     }
   }
   ```

## <a name="deploy-code-with-infrastructure"></a>Nasazení kódu pomocí infrastruktury

Protože kód se přidá do projektu, vaše nasazení je trochu jiné tento čas. Během nasazení připravit artefakty pro váš projekt na místo, s přístupem k Resource Manageru. Artefakty, které jsou připraveny k účtu úložiště.

### <a name="az-module-script"></a>AZ modulu skriptu

Existuje jeden menší změnu, kterou je třeba provést do šablony, pokud používáte modul skriptu Az. Tento skript přidá lomítko umístění artefakty, ale šablony nepředpokládá, že tento lomítko. Otevřete WebSite.json a najít vlastnosti pro rozšíření MSDeploy. Má vlastnost s názvem **packageUri**. Odeberte lomítko mezi artefakty umístění a složky balíčku.

By měl vypadat:

```json
"packageUri": "[concat(parameters('_artifactsLocation'), parameters('ExampleAppPackageFolder'), '/', parameters('ExampleAppPackageFileName'), parameters('_artifactsLocationSasToken'))]",
```

Všimněte si, že v předchozím příkladu se žádné `'/',` mezi **parameters('_artifactsLocation')** a **parameters('ExampleAppPackageFolder')** .

Sestavte projekt znovu. Sestavení projektu je zajištěno, že se do pracovní složky přidají soubory, které potřebujete k nasazení.

Nyní otevřete konzolu Powershellu a spusťte:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory .\bin\Debug\staging\ExampleAppDeploy -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json -UploadArtifacts -StorageAccountName <storage-account-name>
```

### <a name="azurerm-module-script"></a>Skript modulu AzureRM

Pro skript modulu AzureRM pomocí sady Visual Studio:

1. Chcete-li znovu nasadit, zvolte **nasadit**a skupinu prostředků, které jste předtím nasadili.

    ![Znovu nasaďte projekt](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

1. Vyberte účet úložiště, který je nasazený s touto skupinou prostředků pro **účet úložiště artefaktu**.

   ![Opětovné nasazení webové nasazení](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy-web-app.png)

## <a name="view-web-app"></a>Zobrazení webové aplikace

1. Po dokončení nasazení vyberte na portálu svojí webovou aplikaci. Vyberte adresu URL a přejděte na web.

   ![procházení webu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/browse-site.png)

1. Všimněte si, že jste úspěšně nasadili výchozí aplikaci ASP.NET.

   ![zobrazení nasazené aplikace](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## <a name="add-operations-dashboard"></a>Přidání řídicího panelu operací

Nejste omezení jenom na prostředky dostupné prostřednictvím rozhraní sady Visual Studio. Nasazení můžete přizpůsobit tak, že přidáte do šablony vlastní prostředek. Pokud chcete zobrazit přidávání prostředku, přidáte provozní řídicí panel pro správu prostředku, který jste nasadili.

1. Otevřete soubor WebSite.json a přidejte následující kód JSON za prostředek účtu úložiště, ale před uzavírající `]` oddílu prostředků.

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
   ```

1. Projekt znovu nasadíte.

1. Po dokončení nasazení se zobrazení řídicího panelu na portálu. Vyberte **řídicí panel** a vyberte si ten, který jste nasadili.

   ![Vlastní řídicí panel](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/view-custom-dashboards.png)

1. Zobrazí přizpůsobeného řídicího panelu.

   ![Vlastní řídicí panel](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/Ops-DemoSiteGroup-dashboard.png)

Přístup k řídicímu panelu můžete spravovat pomocí skupin RBAC. Vzhled řídicího panelu můžete také upravit po nasazení. Pokud ovšem provádíte opakované nasazení skupiny prostředků, uvede se řídicí panel do výchozího stavu ve vaší šabloně. Další informace o vytváření řídicích panelů najdete v tématu [Vytváření řídicích panelů Azure prostřednictvím kódu programu](../azure-portal/azure-portal-dashboards-create-programmatically.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na webu Azure Portal, vyberte **skupiny prostředků** v levé nabídce.

1. Vyberte název skupiny prostředků.

1. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste se naučili, jak vytvořit a nasadit šablony pomocí sady Visual Studio. V dalším kurzu se dozvíte, jak v referenčních informacích k šablonám vyhledat potřebné informace, abyste mohli vytvořit šifrovaný účet služby Azure Storage.

> [!div class="nextstepaction"]
> [Vytvoření šifrovaného účtu úložiště](./resource-manager-tutorial-create-encrypted-storage-accounts.md)

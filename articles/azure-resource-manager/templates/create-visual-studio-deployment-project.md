---
title: Vytvoření projektů skupiny prostředků sady Visual Studio pro & nasazení
description: Pomocí sady Visual Studio vytvořte projekt skupiny prostředků Azure a nasaďte prostředky do Azure.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 479dbed1f288148c24fc8464f7895cd3e2b182f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91372642"
---
# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Vytvoření a nasazení skupiny prostředků Azure pomocí sady Visual Studio

Pomocí sady Visual Studio můžete vytvořit projekt, který nasadí vaši infrastrukturu a kód do Azure. Můžete například nasadit webového hostitele, web a kód webu. Visual Studio poskytuje řadu různých předem připravených šablon pro běžné scénáře nasazení. V tomto článku nasadíte webovou aplikaci.

Tento článek ukazuje, jak používat [Visual Studio 2019 nebo novější s nainstalovanými úlohami vývoje a ASP.NET pro Azure](/visualstudio/install/install-visual-studio?view=vs-2019). Pokud používáte Visual Studio 2017, vaše prostředí je převážně stejné.

## <a name="create-azure-resource-group-project"></a>Vytvoření projektu skupiny prostředků Azure

V této části vytvoříte projekt skupiny prostředků Azure pomocí šablony **webové aplikace** .

1. V aplikaci Visual Studio vyberte **soubor** > **Nový** > **projekt**.
1. Vyberte šablonu projektu **skupiny prostředků Azure** a **Další**.

    ![Snímek obrazovky se zobrazí okno vytvořit nové projektu se skupinou prostředků Azure a zvýrazněným tlačítkem Další.](./media/create-visual-studio-deployment-project/create-project.png)

1. Zadejte název svého projektu. Ostatní výchozí nastavení jsou pravděpodobně jemné, ale je třeba je zkontrolovat, aby fungovaly pro vaše prostředí. Až budete hotovi, vyberte **Vytvořit**.

    ![Vytvoření projektu](./media/create-visual-studio-deployment-project/name-project.png)

1. Zvolte šablonu, kterou chcete nasadit do Azure Resource Manageru. Všimněte si, že máte spoustu různých možností v závislosti na typu projektu, který chcete nasadit. V tomto článku vyberte šablonu **Webová aplikace** a klikněte na **OK**.

    ![Volba šablony](./media/create-visual-studio-deployment-project/select-project.png)

    Šablona, kterou vyberete, je jenom výchozí bod, podle potřeb vašeho scénáře můžete prostředky přidat nebo odebrat.

1. Visual Studio vytvoří projekt nasazení skupiny prostředků pro webovou aplikaci. Chcete-li zobrazit soubory projektu, podívejte se na uzel v projektu nasazení.

    ![Zobrazit uzly](./media/create-visual-studio-deployment-project/show-items.png)

    Vzhledem k tomu, že jste vybrali šablonu webové aplikace, zobrazí se následující soubory:

   | Název souboru | Description |
   | --- | --- |
   | Deploy-AzureResourceGroup.ps1 |Skript PowerShellu, který spustí příkazy PowerShellu pro nasazení Azure Resource Manageru. Visual Studio pomocí tohoto skriptu PowerShellu nasadí vaši šablonu. |
   | WebSite.jsna |Šablona Resource Manageru, která definuje infrastrukturu, kterou chcete nasadit do Azure, a parametry, které můžete během nasazení zadat. Definuje také závislosti mezi prostředky, takže je Resource Manager nasadí ve správném pořadí. |
   | WebSite.parameters.jsna |Soubor parametrů, který má hodnoty požadované šablonou. Předáním hodnot těchto parametrů přizpůsobujete jednotlivá nasazení. |

    Tyto základní soubory mají všechny projekty nasazení skupiny prostředků. Ostatní projekty můžou mít i další soubory, které podporují jiné funkce.

## <a name="customize-resource-manager-template"></a>Přizpůsobení šablony Správce prostředků

Projekt nasazení můžete přizpůsobit úpravou šablony Správce prostředků, která popisuje prostředky, které chcete nasadit. Další informace o jednotlivých prvcích šablony Resource Manageru najdete v tématu o [vytváření šablon Azure Resource Manageru](template-syntax.md).

1. Chcete-li pracovat se šablonou, otevřete **WebSite.jsna**.

1. Editor sady Visual Studio poskytuje nástroje, které vám s úpravami šablony Resource Manageru pomohou. Okno **JSON Outline** (Osnova JSON) usnadňuje zobrazení prvků, které jsou v šabloně definované.

   ![Zobrazit osnovu JSON](./media/create-visual-studio-deployment-project/show-json-outline.png)

1. Vyberte prvek v obrysu, který chcete přejít k této části šablony.

   ![Přejít na JSON](./media/create-visual-studio-deployment-project/navigate-json.png)

1. Pokud chcete přidat prostředek, můžete buď použít tlačítko **Přidat prostředek** v horní části okna s osnovou JSON, nebo kliknout pravým tlačítkem na **resources** a vybrat **Přidat nový prostředek**.

   ![Snímek obrazovky znázorňující okno osnovy JSON s zvýrazněnou možností přidat nový prostředek.](./media/create-visual-studio-deployment-project/add-resource.png)

1. Vyberte **účet úložiště** a pojmenujte ho. Zadejte název, který nebude delší než 11 znaků. Obsahovat smí jenom čísla a malá písmena.

   ![Přidání úložiště](./media/create-visual-studio-deployment-project/add-storage.png)

1. Všimněte si, že se přidal nejenom tento prostředek, ale také parametr pro typ účtu úložiště a proměnná pro název účtu úložiště.

   ![Zobrazit osnovu](./media/create-visual-studio-deployment-project/show-new-items.png)

1. Parametr pro typ účtu úložiště je předem definovaný s povolenými typy a výchozím typem. Tyto hodnoty můžete ponechat beze změny nebo je můžete podle potřeby upravit. Pokud nechcete, aby pomocí této šablony někdo nasadil účet úložiště **Premium_LRS**, stačí ho odebrat z povolených typů.

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

1. Visual Studio také poskytuje IntelliSense, který vám pomůže pochopit vlastnosti, které jsou k dispozici při úpravách šablony. Pokud chcete třeba upravit vlastnosti pro plán služby App Service, přejděte k prostředku **HostingPlan** a přidejte hodnotu **properties**. Všimněte si, že IntelliSense zobrazuj dostupné hodnoty a poskytuje k nim také popis.

   ![Zobrazit IntelliSense](./media/create-visual-studio-deployment-project/show-intellisense.png)

   Můžete nastavit **numberOfWorkers** na 1 a soubor uložit.

   ```json
   "properties": {
     "name": "[parameters('hostingPlanName')]",
     "numberOfWorkers": 1
   }
   ```

1. Otevřete **WebSite.parameters.jsv** souboru. Pomocí souboru Parameters (parametry) předáte hodnoty během nasazení, které přizpůsobují nasazený prostředek. Poskytněte plánu hostování název a uložte soubor.

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

Nyní jste připraveni nasadit projekt do skupiny prostředků.

Ve výchozím nastavení používá skript prostředí PowerShell (Deploy-AzureResourceGroup.ps1) v projektu modul AzureRM. Pokud stále máte nainstalovaný modul AzureRM a chcete ho dál používat, můžete použít tento výchozí skript. Pomocí tohoto skriptu můžete nasadit své řešení pomocí rozhraní sady Visual Studio.

Pokud jste však migrovali do nového [modulu AZ Module](/powershell/azure/new-azureps-module-az), je nutné přidat nový skript do projektu. Chcete-li přidat skript, který používá modul AZ, zkopírujte skript [Deploy-AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) a přidejte jej do projektu. Pokud chcete tento skript použít pro nasazení, musíte ho spustit z konzoly PowerShellu namísto použití rozhraní nasazení sady Visual Studio.

Oba přístupy jsou uvedené v tomto článku. Tento článek odkazuje na výchozí skript jako skript modulu AzureRM a nový skript jako skript AZ Module.

### <a name="az-module-script"></a>AZ Module Script

V případě skriptu AZ Module Script otevřete konzolu PowerShellu a spusťte:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory . -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json
```

### <a name="azurerm-module-script"></a>Skript modulu AzureRM

Pro skript modulu AzureRM použijte Visual Studio:

1. V místní nabídce uzlu projektu nasazení klikněte na možnost **nasadit**  >  **Nový**.

    ![Nová položka nabídky nasazení](./media/create-visual-studio-deployment-project/deploy.png)

1. Zobrazí se dialogové okno **Deploy to Resource Group** (Nasadit do skupiny prostředků). V rozevíracím seznamu **Skupina prostředků** vyberte existující skupinu prostředků nebo vytvořte novou. Vyberte **Nasadit**.

    ![Dialogové okno nasadit do skupiny prostředků](./media/create-visual-studio-deployment-project/show-deployment.png)

1. V oknech **Výstup** se zobrazí stav nasazení. Po dokončení nasazení vám poslední zpráva oznámí úspěšné nasazení. Bude vypadat zhruba následovně:

   ```output
   18:00:58 - Successfully deployed template 'website.json' to resource group 'ExampleAppDeploy'.
   ```

## <a name="view-deployed-resources"></a>Zobrazit nasazené prostředky

Pojďme se podívat na výsledky.

1. V prohlížeči otevřete [Azure Portal](https://portal.azure.com/) a přihlaste se ke svému účtu. Chcete-li skupinu prostředků zobrazit, klikněte na odkaz **Skupiny prostředků** a pak na skupinu, do které jste provedli nasazení.

1. Zobrazí se všechny nasazené prostředky. Všimněte si, že název účtu úložiště není přesně stejný jako název, který jste zadali během přidávání tohoto prostředku. Účet úložiště musí být jedinečný. Šablona automaticky přidá řetězec znaků k názvu, který jste zadali, chcete-li vytvořit jedinečný název.

    ![Zobrazit prostředky](./media/create-visual-studio-deployment-project/show-deployed-resources.png)

## <a name="add-code-to-project"></a>Přidat kód do projektu

V tomto okamžiku jste nasadili infrastrukturu pro vaši aplikaci, ale zatím není v projektu nasazený žádný kód.

1. Přidejte projekt do řešení sady Visual Studio. Klikněte pravým tlačítkem na řešení a vyberte **Přidat**  >  **Nový projekt**.

    ![Přidat projekt](./media/create-visual-studio-deployment-project/add-project.png)

1. Přidejte **ASP.NET Core webovou aplikaci**.

    ![Přidat webovou aplikaci](./media/create-visual-studio-deployment-project/add-app.png)

1. Zadejte název vaší webové aplikace a vyberte **vytvořit**.

    ![Pojmenování webové aplikace](./media/create-visual-studio-deployment-project/name-web-app.png)

1. Vyberte **Webová aplikace** a **vytvořte**.

    ![Výběr webové aplikace](./media/create-visual-studio-deployment-project/select-project-type.png)

1. Když sada Visual Studio vytvoří webovou aplikaci, uvidíte oba projekty v řešení.

    ![Zobrazit projekty](./media/create-visual-studio-deployment-project/show-projects.png)

1. Teď potřebujete zajistit, aby projekt skupiny prostředků věděl o novém projektu. Vraťte se do projektu skupiny prostředků (ExampleAppDeploy). Klikněte pravým tlačítkem na **Odkazy** a vyberte **Přidat odkaz**.

    ![Snímek obrazovky s zvýrazněnou možností přidat odkaz se zobrazí nabídka ExampleAppDeploy.](./media/create-visual-studio-deployment-project/add-new-reference.png)

1. Vyberte vámi vytvořený projekt webové aplikace.

   ![Přidání odkazu](./media/create-visual-studio-deployment-project/add-reference.png)

   Přidáním odkazu propojíte projekt webové aplikace s projektem skupiny prostředků a automaticky nastavíte některé vlastnosti. Tyto vlastnosti se zobrazují v okně **Vlastnosti**. Pole **Include File Path** (Cesta k souboru pro zahrnutí) obsahuje cestu, kde je balíček vytvořený. Poznamenejte si složku (ExampleApp) a soubor (package.zip). Tyto hodnoty musíte znát, protože je uvádíte jako parametry při nasazování aplikace.

   ![Viz Referenční informace](./media/create-visual-studio-deployment-project/see-reference.png)

1. Vraťte se do šablony (WebSite.jsna) a přidejte prostředek do šablony.

    ![Přidat prostředek](./media/create-visual-studio-deployment-project/add-resource-2.png)

1. Tentokrát vyberte **Nasazení webu pro webové aplikace**.

    ![Přidat nasazení webu](./media/create-visual-studio-deployment-project/add-web-deploy.png)

   Uložte šablonu.

1. V šabloně jsou nějaké nové parametry. Přidaly se v předchozím kroku. Nemusíte zadávat hodnoty pro **_artifactsLocation** nebo **_artifactsLocationSasToken** , protože tyto hodnoty se generují automaticky. Je však třeba nastavit složku a název souboru na cestu, která obsahuje balíček pro nasazení. Názvy těchto parametrů končí na **PackageFolder** a **PackageFileName**. První část názvu je název prostředku Nasazení webu, který jste přidali. V tomto článku se nazývají **ExampleAppPackageFolder** a **ExampleAppPackageFileName**.

   Otevřete **Website.parameters.js** a nastavte tyto parametry na hodnoty, které jste viděli v referenčních vlastnostech. Nastavte **ExampleAppPackageFolder** na název složky. Nastavte **ExampleAppPackageFileName** na název souboru ZIP.

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

## <a name="deploy-code-with-infrastructure"></a>Nasazení kódu s infrastrukturou

Vzhledem k tomu, že jste do projektu přidali kód, vaše nasazení je trochu jiné. Během nasazování budete mít artefakty pro váš projekt na místo, ke kterému Správce prostředků přistupovat. Artefakty jsou připravené na účet úložiště.

### <a name="az-module-script"></a>AZ Module Script

Pokud používáte skript AZ Module, je třeba provést v šabloně jednu malou změnu. Tento skript přidá lomítko do umístění artefaktů, ale šablona neočekává lomítko. Otevřete WebSite.jsa vyhledejte vlastnosti pro rozšíření MSDeploy. Má vlastnost s názvem **packageUri**. Odeberte lomítko mezi umístěním artefaktů a složkou balíčku.

Měl by vypadat takto:

```json
"packageUri": "[concat(parameters('_artifactsLocation'), parameters('ExampleAppPackageFolder'), '/', parameters('ExampleAppPackageFileName'), parameters('_artifactsLocationSasToken'))]",
```

Všimněte si, že v předchozím příkladu nejsou `'/',` žádné **parametry (' _artifactsLocation ')** a **Parameters (' ExampleAppPackageFolder ')**.

Znovu sestavte projekt. Při sestavování projektu se zajišťují, že soubory, které potřebujete nasadit, se přidají do pracovní složky.

Nyní otevřete konzolu prostředí PowerShell a spusťte příkaz:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory .\bin\Debug\staging\ExampleAppDeploy -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json -UploadArtifacts -StorageAccountName <storage-account-name>
```

### <a name="azurerm-module-script"></a>Skript modulu AzureRM

Pro skript modulu AzureRM použijte Visual Studio:

1. Pokud ho chcete znovu nasadit, vyberte **nasadit**a skupinu prostředků, kterou jste předtím nasadili.

    ![Znovu nasadit projekt](./media/create-visual-studio-deployment-project/redeploy.png)

1. Vyberte účet úložiště, který jste nasadili s touto skupinou prostředků pro **účet úložiště artefaktů**.

   ![Znovu nasadit nasazení webu](./media/create-visual-studio-deployment-project/redeploy-web-app.png)

## <a name="view-web-app"></a>Zobrazit webovou aplikaci

1. Po dokončení nasazení vyberte na portálu svojí webovou aplikaci. Vyberte adresu URL a přejděte na web.

   ![Procházet web](./media/create-visual-studio-deployment-project/browse-site.png)

1. Všimněte si, že jste úspěšně nasadili výchozí aplikaci ASP.NET.

   ![Zobrazit nasazenou aplikaci](./media/create-visual-studio-deployment-project/show-deployed-app.png)

## <a name="add-operations-dashboard"></a>Přidat řídicí panel operací

Nejste omezení jenom na prostředky dostupné prostřednictvím rozhraní sady Visual Studio. Nasazení můžete přizpůsobit tak, že přidáte do šablony vlastní prostředek. Pokud chcete zobrazit přidávání prostředku, přidáte provozní řídicí panel pro správu prostředku, který jste nasadili.

1. Otevřete WebSite.jsv souboru a přidejte následující JSON za prostředek účtu úložiště, ale před zavřením `]` oddílu prostředků.

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
      "type": "Microsoft.Portal/dashboards",
      "apiVersion": "2015-08-01-preview",
      "name": "[concat('ARM-',resourceGroup().name)]",
      "location": "[resourceGroup().location]",
      "tags": {
        "hidden-title": "[concat('OPS-',resourceGroup().name)]"
      }
    }
   ```

1. Znovu nasaďte projekt.

1. Po dokončení nasazení si zobrazte řídicí panel na portálu. Vyberte **řídicí panel** a vyberte ten, který jste nasadili.

   ![Snímek obrazovky zobrazující stránku řídicího panelu se zvýrazněným ukázkovým vlastním řídicím panelem](./media/create-visual-studio-deployment-project/view-custom-dashboards.png)

1. Zobrazí se přizpůsobený řídicí panel.

   ![Vlastní řídicí panel](./media/create-visual-studio-deployment-project/Ops-DemoSiteGroup-dashboard.png)

Přístup k řídicímu panelu můžete spravovat pomocí řízení přístupu na základě role Azure (RBAC). Vzhled řídicího panelu můžete také upravit po nasazení. Pokud ovšem provádíte opakované nasazení skupiny prostředků, uvede se řídicí panel do výchozího stavu ve vaší šabloně. Další informace o vytváření řídicích panelů najdete v tématu [Vytváření řídicích panelů Azure prostřednictvím kódu programu](../../azure-portal/azure-portal-dashboards-create-programmatically.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **skupiny prostředků** .

1. Vyberte název skupiny prostředků.

1. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak vytvořit a nasadit šablony pomocí sady Visual Studio. Další informace o vývoji šablon najdete v naší nové řadě kurzů pro začátečníky:

> [!div class="nextstepaction"]
> [Kurzy pro začátečníky](./template-tutorial-create-first-template.md)

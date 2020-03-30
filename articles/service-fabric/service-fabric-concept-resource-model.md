---
title: Model prostředků aplikace Azure Service Fabric
description: Tento článek obsahuje přehled správy aplikace Azure Service Fabric pomocí Azure Resource Manager.
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: sfrev
ms.openlocfilehash: 69c10b0e9d3b7c29122c8432ab1e4bc06d3a3120
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481112"
---
# <a name="service-fabric-application-resource-model"></a>Model prostředků aplikace Service Fabric

Máte několik možností pro nasazení aplikací Azure Service Fabric v clusteru Service Fabric. Doporučujeme používat Azure Resource Manager. Pokud používáte Resource Manager, můžete popsat aplikace a služby v JSON a pak je nasadit ve stejné šabloně Správce prostředků jako váš cluster. Na rozdíl od použití PowerShellu nebo Azure CLI k nasazení a správě aplikací, pokud používáte Správce prostředků, nemusíte čekat na připravenost clusteru; registrace, zřizování a nasazování aplikací může proběhnout v jednom kroku. Pomocí Správce prostředků je nejlepší způsob, jak spravovat životní cyklus aplikace v clusteru. Další informace naleznete v [tématu Doporučené postupy: Infrastruktura jako kód](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources).

Správa aplikací jako prostředků ve Správci prostředků vám může pomoci získat zlepšení v těchto oblastech:

* Záznam auditu: Správce prostředků audituje každou operaci a vede podrobný protokol aktivit. Protokol aktivit vám může pomoci sledovat všechny změny provedené v aplikacích a v clusteru.
* Řízení přístupu založené na rolích: Přístup ke clusterům a aplikacím nasazeným v clusteru můžete spravovat pomocí stejné šablony Správce prostředků.
* Efektivita správy: Pomocí Správce prostředků poskytuje jedno umístění (portál Azure) pro správu clusteru a nasazení kritických aplikací.

V tomto dokumentu se dozvíte, jak:

> [!div class="checklist"]
>
> * Nasaďte prostředky aplikací pomocí Správce prostředků.
> * Upgradujte prostředky aplikací pomocí Správce prostředků.
> * Odstraňte prostředky aplikace.

## <a name="deploy-application-resources"></a>Nasazení prostředků aplikací

Kroky vysoké úrovně, které provedete nasazení aplikace a jejích služeb pomocí modelu prostředků aplikace Resource Manager, jsou:
1. Zabalte kód aplikace.
1. Nahrajte balíček.
1. Odkazna umístění balíčku v šabloně Správce prostředků jako prostředek aplikace. 

Další informace naleznete [v aplikaci Package a Application](service-fabric-package-apps.md#create-an-sfpkg).

Potom vytvoříte šablonu Správce prostředků, aktualizujete soubor parametrů s podrobnostmi o aplikaci a nasadíte šablonu do clusteru Service Fabric. [Prozkoumejte ukázky](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM).

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Chcete-li nasadit aplikaci ze šablony Správce prostředků, musíte mít účet úložiště. Účet úložiště se používá k fázi image aplikace. 

Můžete znovu použít existující účet úložiště nebo můžete vytvořit nový účet úložiště pro přípravu aplikací. Pokud používáte existující účet úložiště, můžete tento krok přeskočit. 

![vytvořit účet úložiště][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>Konfigurace účtu úložiště

Po vytvoření účtu úložiště vytvoříte kontejner objektů blob, kde lze aplikace připravené. Na webu Azure Portal přejděte na účet Azure Storage, kde chcete ukládat aplikace. Vyberte **objekty** > BLOB**přidat kontejner**. 

Prostředky v clusteru lze zabezpečit nastavením úrovně veřejného přístupu na **soukromé**. Přístup můžete udělit několika způsoby:

* Autorizujte přístup k objektům BLOB a frontám pomocí [služby Azure Active Directory](../storage/common/storage-auth-aad-app.md).
* Udělte přístup k datům objektů blob Azure a fronty pomocí [RBAC na webu Azure Portal](../storage/common/storage-auth-aad-rbac-portal.md).
* Delegujte přístup pomocí [sdíleného přístupového podpisu](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature).

Příklad na následujícím snímku obrazovky používá anonymní přístup pro čtení pro objekty BLOB.

![Vytvoření objektu blob][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>Inscenujte aplikaci ve vašem účtu úložiště

Před nasazením aplikace je nutné nastínit aplikaci v úložišti objektů blob. V tomto kurzu vytvoříme balíček aplikace ručně. Mějte na paměti, že tento krok lze automatizovat. Další informace naleznete v [tématu Balíček aplikace](service-fabric-package-apps.md#create-an-sfpkg). 

V tomto kurzu používáme [ukázkovou aplikaci Hlasování](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart).

1. V sadě Visual Studio klikněte pravým **tlačítkem** myši na hlasovací projekt a potom vyberte **balíček**.

   ![Aplikace balíčku][PackageApplication]  
1. Přejděte do adresáře *.\service-fabric-dotnet-quickstart\Voting\pkg\Debug.* Zapněte obsah do souboru s názvem *Voting.zip*. Soubor *ApplicationManifest.xml* by měl být v kořenovém adresáři souboru ZIP.

   ![Aplikace Zip][ZipApplication]  
1. Přejmenujte soubor a změňte příponu z .zip na *.sfpkg*.

1. Na webu Azure Portal vyberte v kontejneru **aplikací** pro váš účet úložiště možnost **Nahrát**a pak nahrajte **Hlasovací.sfpkg**. 

   ![Nahrát balíček aplikace][UploadAppPkg]

Nyní je aplikace připravená a můžete vytvořit šablonu Správce prostředků pro nasazení aplikace.

### <a name="create-the-resource-manager-template"></a>Vytvoření šablony Resource Manageru

Ukázková aplikace obsahuje [šablony Azure Resource Manageru, které](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) můžete použít k nasazení aplikace. Názvy souborů šablony jsou *UserApp.json* a *UserApp.Parameters.json*.

> [!NOTE]
> Soubor *UserApp.Parameters.json* musí být aktualizován názvem clusteru.
>
>

| Parametr              | Popis                                 | Příklad                                                      | Komentáře                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | Název clusteru, do který ho nasazujete | sf-cluster123                                                |                                                              |
| aplikace            | Název aplikace                 | Hlasování                                                       |
| applicationTypeName    | Název typu aplikace           | Typ hlasování                                                   | Musí odpovídat applicationManifest.xml                 |
| applicationTypeVersion | Verze typu aplikace         | 1.0.0                                                        | Musí odpovídat applicationManifest.xml                 |
| Název_služby            | Název služby         | Hlasování ~ Hlasovací Web                                             | Musí být ve formátu ApplicationName~ServiceType            |
| název_služby serviceTypeName        | Název typu služby                | VotingWeb                                                    | Musí odpovídat souboru ServiceManifest.xml.                 |
| appPackageUrl          | Adresa URL úložiště objektů blob aplikace     | https://servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | Adresa URL balíčku aplikace v úložišti objektů blob (postup nastavení adresy URL je popsán dále v článku) |

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-application"></a>Nasazení aplikace

Spusťte rutinu **New-AzResourceGroupDeployment** a nasaďte aplikaci do skupiny prostředků, která obsahuje váš cluster:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>Upgrade aplikace Service Fabric pomocí Správce prostředků

Můžete upgradovat aplikaci, která je již nasazena do clusteru Service Fabric z jednoho z těchto důvodů:

* Do aplikace je přidána nová služba. Definice služby musí být přidána do souborů *service-manifest.xml* a *application-manifest.xml* při přidání služby do aplikace. Chcete-li zohlednit novou verzi aplikace, musíte také změnit verzi typu aplikace z 1.0.0 na 1.0.1 v [UserApp.Parameters.json](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json):

    ```json
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "Voting~VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```

* Do aplikace je přidána nová verze existující služby. Mezi příklady patří změny kódu aplikace a aktualizace verze a názvu typu aplikace. Pro tento upgrade aktualizujte soubor UserApp.Parameters.json takto:

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>Odstranění prostředků aplikace

Odstranění aplikace, která byla nasazena pomocí modelu prostředků aplikace ve Správci prostředků:

1. Pomocí rutiny [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0) získáte ID prostředku pro aplikaci:

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. K odstranění prostředků aplikace použijte rutinu [Odebrat azResource:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0)

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Další kroky

Získejte informace o modelu prostředků aplikace:

* [Modelování aplikace v service fabric](service-fabric-application-model.md)
* [Manifesty aplikací a služeb Service Fabric](service-fabric-application-and-service-manifests.md)
* [Osvědčené postupy: Infrastruktura jako kód](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [Správa aplikací a služeb jako prostředků Azure](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png

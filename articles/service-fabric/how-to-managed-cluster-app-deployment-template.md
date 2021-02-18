---
title: Nasazení aplikace spravovaného clusteru Service Fabric (Preview) pomocí šablony ARM
description: Nasaďte aplikaci do spravovaného clusteru Azure Service Fabric (Preview) pomocí šablony Azure Resource Manager.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: b2d73180de61f44850b9c52a7ac24b0c23c36db2
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642394"
---
# <a name="deploy-a-service-fabric-managed-cluster-preview-application-using-arm-template"></a>Nasazení aplikace spravovaného clusteru Service Fabric (Preview) pomocí šablony ARM

Máte několik možností, jak nasadit aplikace Service Fabric Azure do spravovaného clusteru Service Fabric. Doporučujeme použít Azure Resource Manager. Pokud používáte Správce prostředků, můžete popsat aplikace a služby ve formátu JSON a pak je nasadit do stejné Správce prostředků šablony jako svůj cluster. Na rozdíl od použití PowerShellu nebo rozhraní příkazového řádku Azure k nasazení a správě aplikací platí, že pokud používáte Správce prostředků, nemusíte čekat, až bude cluster připravený. registrace, zřizování a nasazení aplikace může probíhat v jednom kroku. Použití Správce prostředků je nejlepším způsobem, jak spravovat životní cyklus aplikací v clusteru. Další informace najdete v tématu [osvědčené postupy: infrastruktura jako kód](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources).

Správa aplikací jako prostředků v Správce prostředků vám může pomáhat získat vylepšení těchto oblastí:

* Záznam pro audit: Správce prostředků Audituje každou operaci a udržuje podrobný protokol aktivit. Protokol aktivit vám může pomáhat sledovat všechny změny provedené v aplikacích a ve vašem clusteru.
* Řízení přístupu na základě role: přístup ke clusterům a aplikacím nasazeným v clusteru můžete spravovat pomocí stejné šablony Správce prostředků.
* Efektivita správy: použití Správce prostředků poskytuje jedno umístění (Azure Portal) pro správu clusteru a důležitých nasazení aplikací.

V tomto dokumentu se dozvíte, jak:

> [!div class="checklist"]
>
> * Nasaďte prostředky aplikace pomocí Správce prostředků.
> * Upgradujte prostředky aplikace pomocí Správce prostředků.
> * Odstraňte prostředky aplikace.

## <a name="deploy-application-resources"></a>Nasazení prostředků aplikace

Kroky vysoké úrovně, které provádíte při nasazení aplikace a jejích služeb pomocí modelu Správce prostředkůch prostředků aplikace:
1. Zabalit kód aplikace
1. Nahrajte balíček.
1. Odkaz na umístění balíčku v šabloně Správce prostředků jako prostředek aplikace 

Další informace najdete v [balíčku aplikace](service-fabric-package-apps.md#create-an-sfpkg).

Pak vytvoříte šablonu Správce prostředků, aktualizujte soubor parametrů s podrobnostmi o aplikaci a nasaďte šablonu do clusteru Service Fabric. [Prozkoumejte ukázky](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/voting-sample-no-reverse-proxy/ARM-Managed-Cluster).

### <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Pokud chcete nasadit aplikaci ze šablony Správce prostředků, musíte mít účet úložiště. Účet úložiště se používá k přípravě image aplikace. 

Můžete znovu použít existující účet úložiště, nebo můžete vytvořit nový účet úložiště pro přípravu svých aplikací. Pokud použijete existující účet úložiště, můžete tento krok přeskočit. 

![Vytvoření účtu úložiště][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>Konfigurace účtu úložiště

Po vytvoření účtu úložiště vytvoříte kontejner objektů blob, kde můžete aplikace připravit. V Azure Portal otevřete Azure Storage účet, do kterého chcete ukládat své aplikace. Vyberte **objekty blob**  >  **Přidat kontejner**. 

Prostředky v clusteru je možné zabezpečit nastavením úrovně veřejného přístupu na **Private**. Přístup můžete udělit několika způsoby:

* Pomocí [Azure Active Directory](../storage/common/storage-auth-aad-app.md)autorizujte přístup k objektům blob a frontám.
* Pomocí [Azure RBAC v Azure Portal](../storage/common/storage-auth-aad-rbac-portal.md)udělte přístup k datům objektů blob Azure a frontám.
* Delegovat přístup pomocí [sdíleného přístupového podpisu](/rest/api/storageservices/delegate-access-with-shared-access-signature).

Příklad na následujícím snímku obrazovky používá anonymní přístup pro čtení pro objekty blob.

![Vytvořit objekt BLOB][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>Příprava aplikace ve vašem účtu úložiště

Než budete moct nasadit aplikaci, musíte aplikaci připravit v úložišti objektů BLOB. V tomto kurzu vytvoříme balíček aplikace ručně. Mějte na paměti, že tento krok může být automatizovaný. Další informace najdete v tématu [Package a Application](service-fabric-package-apps.md#create-an-sfpkg). 

V tomto kurzu používáme [hlasovací ukázkovou aplikaci](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart).

1. V aplikaci Visual Studio klikněte pravým tlačítkem myši na **hlasovací** projekt a vyberte možnost **balíček**.

   ![Aplikace balíčku][PackageApplication]  
1. Přejít do adresáře *.\Service-Fabric-dotnet-quickstart\Voting\pkg\Debug* Zip obsah do souboru s názvem *Voting.zip*. Soubor *ApplicationManifest.xml* by měl být v kořenovém adresáři v souboru ZIP.

   ![Aplikace zip][ZipApplication]  
1. Přejmenujte soubor pro změnu rozšíření z. zip na *. sfpkg*.

1. V Azure Portal v kontejneru **aplikace** pro váš účet úložiště vyberte **Odeslat** a pak nahrajte **hlasovací. sfpkg**. 

   ![Nahrát balíček aplikace][UploadAppPkg]

Nyní je aplikace připravená a můžete vytvořit šablonu Správce prostředků k nasazení aplikace.

### <a name="create-the-resource-manager-template"></a>Vytvoření šablony Resource Manageru

Ukázková aplikace obsahuje [šablony Azure Resource Manager](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) , které můžete použít k nasazení aplikace. Názvy souborů šablon jsou *UserApp.jszapnuté* a *UserApp.Parameters.jsna*.

> [!NOTE]
> *UserApp.Parameters.js* souboru musí být aktualizovaný s názvem vašeho clusteru.
>
>

| Parametr              | Popis                                 | Příklad                                                      | Komentáře                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | Název clusteru, do kterého nasazujete | SF – cluster123                                                |                                                              |
| aplikace            | Název aplikace                 | Hlasování                                                       |
| verze    | ID prostředku, typ aplikace a verze aplikace       | /providers/Microsoft.ServiceFabric/managedClusters/sf-cluster-123/applicationTypes/VotingType/versions/1.0.0                                               | Musí odpovídat ApplicationManifest.xml                 |              |
| serviceName            | Název služby         | VotingWeb                                             | Musí být ve formátu ServiceType            |
| serviceTypeName        | Název typu služby                | VotingWebType                                                    | Musí odpovídat ServiceManifest.xml                 |
| appPackageUrl          | Adresa URL pro úložiště objektů BLOB aplikace     | https: \/ /servicefabricapps.blob.Core.Windows.NET/Apps/voting.sfpkg | Adresa URL balíčku aplikace v úložišti objektů BLOB (postup nastavení adresy URL je popsán dále v článku) |

```json
{
    "apiVersion": "2021-01-01-preview",
    "type": "Microsoft.ServiceFabric/managedclusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-01-01-preview",
    "type": "Microsoft.ServiceFabric/managedclusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-01-01-preview",
    "type": "Microsoft.ServiceFabric/managedclusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-01-01-preview",
    "type": "Microsoft.ServiceFabric/managedclusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-application"></a>Nasazení aplikace

Spusťte rutinu **New-AzResourceGroupDeployment** k nasazení aplikace do skupiny prostředků, která obsahuje váš cluster:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>Upgrade aplikace Service Fabric pomocí Správce prostředků

> [!IMPORTANT]
> Všechny služby nasazené přes definici JSON pro ARM se musí odebrat z oddílu DefaultServices odpovídajícího souboru ApplicationManifest.xml.


Můžete upgradovat aplikaci, která je už nasazená do clusteru Service Fabric, z některého z těchto důvodů:

* Do aplikace se přidá nová služba. Při přidání služby do aplikace musí být do *service-manifest.xml* a *application-manifest.xml* souborů přidána definice služby. Aby odrážela novou verzi aplikace, musíte také změnit verzi typu aplikace z 1.0.0 na 1.0.1 v [UserApp.Parameters.js](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json):

    ```json
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```

* Do aplikace se přidá nová verze existující služby. Mezi příklady patří změny kódu aplikace a aktualizace verze a názvu typu aplikace. Pro tento upgrade UserApp.Parameters.jsaktualizujte takto:

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```
## <a name="delete-application-resources"></a>Odstranit prostředky aplikace

Postup odstranění aplikace nasazené pomocí modelu prostředku aplikace v Správce prostředků:

1. K získání ID prostředku pro aplikaci použijte rutinu [Get-AzResource](/powershell/module/az.resources/get-azresource) :

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. K odstranění prostředků aplikace použijte rutinu [Remove-AzResource](/powershell/module/az.resources/remove-azresource) :

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Další kroky

Získat informace o modelu prostředků aplikace:

* [Modelování aplikace v Service Fabric](service-fabric-application-model.md)
* [Service Fabric manifestů aplikací a služeb](service-fabric-application-and-service-manifests.md)
* [Osvědčené postupy: infrastruktura jako kód](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [Správa aplikací a služeb jako prostředků Azure](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png
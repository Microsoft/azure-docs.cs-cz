---
title: Model prostředků aplikace Azure Service Fabric
description: Tento článek poskytuje přehled správy aplikace Service Fabric Azure pomocí Azure Resource Manager
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: sfrev
ms.openlocfilehash: 44073967730d95e803f57d504aa9d8c529250a8d
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751204"
---
# <a name="service-fabric-application-resource-model"></a>Service Fabric model prostředku aplikace

Doporučuje se Service Fabric aplikace nasadit do Service Fabric clusteru prostřednictvím Azure Resource Manager. Tato metoda umožňuje popsat aplikace a služby ve formátu JSON a nasazovat je do stejné šablony Správce prostředků jako váš cluster. Na rozdíl od nasazení a správy aplikací přes PowerShell nebo Azure CLI není nutné čekat na to, aby cluster byl připravený. Proces registrace, zřizování a nasazování aplikací potom může probíhat v jediném kroku. Je to osvědčený postup pro správu životního cyklu aplikací ve vašem clusteru. Další informace najdete v tématu [osvědčené postupy: infrastruktura jako kód](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-service-fabric-resources).

V případě potřeby můžete své aplikace spravovat jako Správce prostředků prostředky pro zlepšení:

* Záznam pro audit: Správce prostředků Audituje každou operaci a udržuje podrobný *Protokol aktivit* , který vám může pomáhat sledovat změny provedené v těchto aplikacích a clusteru.
* Řízení přístupu na základě role: Správa přístupu ke clusterům i aplikacím nasazeným v clusteru je možné provádět prostřednictvím stejné Správce prostředků šablony.
* Azure Resource Manager (přes Azure Portal) se stane jedním zastavením pro správu clusteru a důležitých nasazení aplikací.

## <a name="service-fabric-application-life-cycle-with-azure-resource-manager"></a>Service Fabric životní cyklus aplikace s Azure Resource Manager

V tomto dokumentu se dozvíte, jak:

> [!div class="checklist"]
>
> * Nasazení prostředků aplikace pomocí Azure Resource Manager
> * Upgrade prostředků aplikace pomocí Azure Resource Manager
> * Odstranit prostředky aplikace

## <a name="deploy-application-resources-using-azure-resource-manager"></a>Nasazení prostředků aplikace pomocí Azure Resource Manager

Chcete-li nasadit aplikaci a její služby pomocí modelu Azure Resource Managerch prostředků aplikace, je nutné zabalit kód aplikace, nahrát balíček a pak odkazovat na umístění balíčku v šabloně Azure Resource Manager jako aplikaci. partner. Další informace najdete v [balíčku aplikace](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg).

Pak vytvořte šablonu Azure Resource Manager, aktualizujte soubor parametrů s podrobnostmi o aplikaci a nasaďte ji do clusteru Service Fabric. [Tady](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)najdete ukázky.

### <a name="create-a-storage-account"></a>Vytvoření účtu Storage

Nasazení aplikace ze šablony Správce prostředků vyžaduje účet úložiště pro přípravu image aplikace. Můžete znovu použít existující účet úložiště nebo vytvořit nový účet úložiště pro přípravu svých aplikací. Pokud chcete použít existující účet úložiště, můžete tento krok přeskočit. 

![Vytvoření účtu úložiště][CreateStorageAccount]

### <a name="configure-storage-account"></a>Konfigurace účtu úložiště

Po vytvoření účtu úložiště je potřeba vytvořit kontejner objektů blob, kde můžete aplikace připravit. V Azure Portal přejděte do účtu úložiště, do kterého chcete aplikace uložit. Vyberte okno **objekty blob** a klikněte na tlačítko **Přidat kontejner** . Prostředky v clusteru je možné zabezpečit nastavením úrovně veřejného přístupu na Private. Přístup lze udělit několika způsoby:

* [Autorizace přístupu k objektům blob a frontám pomocí Azure Active Directory](../storage/common/storage-auth-aad-app.md)
* [Udělení přístupu k datům front a objektů blob Azure s využitím RBAC na webu Azure Portal](../storage/common/storage-auth-aad-rbac-portal.md)
* [Delegovat přístup pomocí sdíleného přístupového podpisu (SAS)](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature
)

 V tomto příkladu použijeme anonymní přístup pro čtení pro objekty blob.

![Vytvořit objekt BLOB][CreateBlob]

### <a name="stage-application-in-a-storage-account"></a>Fáze aplikace v účtu úložiště

Předtím, než bude možné aplikaci nasadit, je nutné ji připravit v úložišti objektů BLOB. V tomto kurzu vytvoříme balíček aplikace ručně, ale tento krok se dá automatizovat.  Další informace najdete v [balíčku aplikace](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg). V následujících krocích se použije [hlasovací ukázková aplikace](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) .

1. V aplikaci Visual Studio klikněte pravým tlačítkem myši na hlasovací projekt a vyberte možnost balíček.
][PackageApplication] aplikace ![ového balíčku  
2. Otevřete adresář **.\Service-Fabric-dotnet-quickstart\Voting\pkg\Debug** , který jste právě vytvořili, a nahlaste obsah do souboru s názvem **hlasovat. zip** , aby soubor souboru ApplicationManifest. XML byl v kořenu souboru ZIP.  
Aplikace ![zip][ZipApplication]  
3. Přejmenujte příponu souboru z. zip na **. sfpkg**.
4. V Azure Portal v kontejneru **aplikace** svého účtu úložiště klikněte na **nahrát** a nahrajte **hlasování. sfpkg**.  
![odeslat balíček aplikace][UploadAppPkg]

Aplikace je teď připravená. Nyní jsme připraveni vytvořit šablonu Azure Resource Manager pro nasazení aplikace.

### <a name="create-the-azure-resource-manager-template"></a>Vytvoření šablony Azure Resource Manager

Ukázková aplikace obsahuje [Azure Resource Manager šablony](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) , které lze použít k nasazení aplikace. Soubory šablon mají název **UserApp. JSON** a **UserApp. Parameters. JSON**.

> [!NOTE]
> Soubor **UserApp. Parameters. JSON** se musí aktualizovat názvem vašeho clusteru.
>
>

| Parametr              | Popis                                 | Příklad:                                                      | Komentáře                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | Název clusteru, do kterého nasazujete | SF – cluster123                                                |                                                              |
| aplikace            | Název aplikace                 | Právem                                                       |
| applicationTypeName    | Název typu aplikace           | VotingType                                                   | Musí odpovídat, co je v souboru ApplicationManifest. XML.                 |
| applicationTypeVersion | Verze typu aplikace         | 1.0.0                                                        | Musí odpovídat, co je v souboru ApplicationManifest. XML.                 |
| serviceName            | Název služby, kterou služba         | Hlasovacího tlačítka ~ VotingWeb                                             | Musí být ve formátu ApplicationName ~ ServiceType            |
| serviceTypeName        | Název typu služby                | VotingWeb                                                    | Musí odpovídat, co je v souboru ServiceManifest. XML.                 |
| appPackageUrl          | Adresa URL pro úložiště objektů BLOB aplikace     | https://servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | Adresa URL balíčku aplikace v úložišti objektů BLOB (postup nastavení je popsaný níže) |

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

Pokud chcete nasadit aplikaci, spusťte rutinu New-AzResourceGroupDeployment a nasaďte ji do skupiny prostředků, která obsahuje váš cluster.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-service-fabric-application-using-azure-resource-manager"></a>Upgrade aplikace Service Fabric pomocí Azure Resource Manager

Aplikace, které jsou už nasazené do clusteru Service Fabric, se upgradují z následujících důvodů:

1. Do aplikace se přidá nová služba. Do souboru Service-manifest. XML a Application-manifest. XML musí být přidána definice služby. V případě, že se má projevit nová verze aplikace, je třeba aktualizovat verzi typu aplikace z 1.0.0 na 1.0.1 [UserApp. Parameters. JSON](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json).

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

2. Do aplikace se přidá nová verze existující služby. To zahrnuje změny kódu aplikace a aktualizace verze a názvu typu aplikace.

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>Odstranit prostředky aplikace

Aplikace nasazené pomocí modelu prostředků aplikace v Azure Resource Manager se dají z clusteru odstranit pomocí následujících kroků.

1) Získat ID prostředku pro aplikaci pomocí [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0):

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

2) Odstraňte prostředky aplikace pomocí [Remove-AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0):

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Další kroky

Získat informace o modelu prostředků aplikace:

* [Modelování aplikace v Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model)
* [Service Fabric manifestů aplikací a služeb](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-and-service-manifests)

## <a name="see-also"></a>Viz také

* [Osvědčené postupy](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)
* [Správa aplikací a služeb jako prostředků Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)

<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png

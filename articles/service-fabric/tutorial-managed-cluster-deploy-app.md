---
title: Nasazení aplikace do spravovaného clusteru s Service Fabric prostřednictvím PowerShellu (Preview)
description: V tomto kurzu se připojíte ke spravovanému clusteru Service Fabric a nasadíte aplikaci přes PowerShell.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 36a91d2852bcda5f958441b48ef4721d6ccc83c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91410439"
---
# <a name="tutorial-deploy-an-app-to-a-service-fabric-managed-cluster-preview"></a>Kurz: nasazení aplikace do spravovaného clusteru Service Fabric (Preview)

V této sérii kurzů si probereme následující:

> [!div class="checklist"]
> * [Postup nasazení spravovaného clusteru Service Fabric](tutorial-managed-cluster-deploy.md)
> * [Postup horizontálního navýšení kapacity Service Fabric spravovaného clusteru](tutorial-managed-cluster-scale.md)
> * [Postup přidání a odebrání uzlů ve spravovaném clusteru s Service Fabric](tutorial-managed-cluster-add-remove-node-type.md)
> * Postup nasazení aplikace na spravovaný Cluster Service Fabric

Tato část řady se zabývá těmito postupy:

> [!div class="checklist"]
> * Připojení k vašemu Service Fabric spravovanému clusteru
> * Nahrání aplikace do clusteru
> * Vytvoření instance aplikace v clusteru
> * Odebrání aplikace z clusteru

## <a name="prerequisites"></a>Požadavky

* Service Fabric spravovaný cluster (viz [*nasazení spravovaného clusteru*](tutorial-managed-cluster-deploy.md)).

## <a name="connect-to-your-cluster"></a>Připojení ke clusteru

Pokud se chcete připojit ke clusteru, budete potřebovat kryptografický otisk certifikátu clusteru. Tuto hodnotu můžete najít ve výstupu vlastností clusteru v nasazení prostředků nebo dotazem na vlastnosti clusteru v existujícím prostředku.

Následující příkaz se dá použít k dotazování prostředku clusteru na kryptografický otisk certifikátu clusteru.

```powershell
$serverThumbprint = (Get-AzResource -ResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ServiceFabric/managedclusters/mysfcluster).Properties.clusterCertificateThumbprint
```

Pomocí kryptografického otisku certifikátu clusteru jste připraveni připojit se ke clusteru.

```powershell
$connectionEndpoint = "mysfcluster.eastus2.cloudapp.azure.com:19000"
Connect-ServiceFabricCluster -ConnectionEndpoint $connectionEndpoint -KeepAliveIntervalInSec 10 `
      -X509Credential `
      -ServerCertThumbprint $serverThumbprint  `
      -FindType FindByThumbprint `
      -FindValue $clientThumbprint `
      -StoreLocation CurrentUser `
      -StoreName My

```

### <a name="upload-an-application-package"></a>Nahrání balíčku aplikace

V tomto kurzu použijeme ukázku [Service Fabric hlasovací aplikace](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/voting-sample-no-reverse-proxy) . Další podrobnosti o Service Fabric nasazení aplikací prostřednictvím PowerShellu najdete v tématu [Service Fabric nasazení a odebrání aplikací](service-fabric-deploy-remove-applications.md).

> [!NOTE]
> Ve verzi Preview spravovaného clusteru Service Fabric nebudete moct publikovat aplikace přímo ze sady Visual Studio.

Nejdřív budete muset [zabalit aplikaci pro nasazení](service-fabric-package-apps.md). Pro tento kurz použijte postup pro sbalení aplikace v sadě Visual Studio. Je důležité poznamenat cestu, kde byla aplikace zabalena, jak bude použita pro cestu níže.

Po vytvoření balíčku aplikace můžete nahrát balíček aplikace do clusteru. Aktualizujte `$path` hodnotu tak, aby reprezentovala cestu, kde existuje balíček aplikace, a spusťte následující příkaz:

```powershell
$path = "C:\Users\<user>\Documents\service-fabric-dotnet-quickstart\Voting\pkg\Debug"
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage
Register-ServiceFabricApplicationType -ApplicationPathInImageStore Debug
```

### <a name="create-an-application"></a>Vytvoření aplikace

Můžete vytvořit instanci aplikace z jakékoli verze typu aplikace, která byla úspěšně zaregistrována pomocí rutiny New-ServiceFabricApplication. Název každé aplikace musí začínat schématem "Fabric:" a musí být jedinečný pro každou instanci aplikace. Vytvoří se také všechny výchozí služby definované v manifestu aplikace typu cílové aplikace.

```powershell
New-ServiceFabricApplication fabric:/Voting VotingType 1.0.0
```

Po dokončení této operace byste měli vidět spuštěné instance aplikace v Service Fabric Explorer.

### <a name="remove-an-application"></a>Odebrání aplikace

Pokud už instanci aplikace nepotřebujete, můžete ji trvale odebrat pomocí `Remove-ServiceFabricApplication` rutiny, která také automaticky odebere všechny služby patřící do aplikace a trvale odebere celý stav služby.

```powershell
Remove-ServiceFabricApplication fabric:/Voting
```

## <a name="next-steps"></a>Další kroky

V tomto kroku jsme nasadili aplikaci na spravovaný Cluster Service Fabric. Další informace o Service Fabric spravovaných clusterech najdete v tématech:

> [!div class="nextstepaction"]
> [Nejčastější dotazy k Service Fabric spravovaných clusterů](faq-managed-cluster.md)

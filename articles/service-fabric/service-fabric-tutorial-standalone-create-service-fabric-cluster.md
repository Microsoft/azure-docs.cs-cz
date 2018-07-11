---
title: Kurz instalace samostatného klienta Service Fabric – Azure Service Fabric | Microsoft Docs
description: V tomto kurzu se dozvíte, jak nainstalovat samostatného klienta Service Fabric v clusteru, který jste vytvořili v předchozím článku kurzu.
services: service-fabric
documentationcenter: .net
author: david-stanford
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dastanfo
ms.custom: mvc
ms.openlocfilehash: 7ba5fa1d4c5b87d1c4828ee98dae36f415d37c20
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344151"
---
# <a name="tutorial-install-and-create-service-fabric-cluster"></a>Kurz: Instalace a vytvoření clusteru Service Fabric

Samostatné clustery Service Fabric nabízejí možnost volby vlastního prostředí a vytvoření clusteru v rámci přístupu Service Fabric „libovolný OS a libovolný cloud“. V této sérii kurzů vytvoříte samostatný cluster hostovaný na AWS a nainstalujete do něj aplikaci.

Tento kurz je druhá část série. Tento kurz vás provede kroky vytvoření samostatného clusteru Service Fabric.

Ve druhé části této série se naučíte:

> [!div class="checklist"]
> * Stáhnout a nainstalovat samostatný balíček Service Fabric
> * Vytvořit cluster Service Fabric
> * Připojit se ke clusteru Service Fabric

## <a name="download-the-service-fabric-for-windows-server-package"></a>Stažení balíčku Service Fabric pro Windows Server

Service Fabric nabízí instalační balíček pro vytváření samostatných clusterů Service Fabric.  [Stáhněte instalační balíček](http://go.microsoft.com/fwlink/?LinkId=730690) do místního počítače.  Po úspěšném stažení ho prostřednictvím připojení RDP zkopírujte do své instance EC2 a vložte ho na plochu.

Vyberte soubor ZIP, otevřete místní nabídku a vyberte **Extrahovat vše** > **Extrahovat**.  Při extrahování souborů se na ploše vygeneruje složka se stejným názvem jako soubor ZIP.

Můžete si přečíst podrobnější informace o [obsahu instalačního balíčku](service-fabric-cluster-standalone-package-contents.md).

## <a name="set-up-your-configuration-file"></a>Nastavení konfiguračního souboru

Sestavujete cluster Windows se třemi uzly, takže potřebujete upravit soubor `ClusterConfig.Unsecure.MultiMachine.json`.

Potom je třeba aktualizovat tři řádky ipAddress, které jsou v souboru jako řádky 8, 15 a 22, na IP adresy pro každou z instancí.

Po aktualizaci uzlů vypadají hodnoty takto:

```json
        {
            "nodeName": "vm0",
            "ipAddress": "172.31.27.1",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        }
```

Pak je třeba aktualizovat několik vlastností.  Na řádku 34 budete muset upravit připojovací řetězec pro diagnostické úložiště, který by měl vypadat přibližně takto: `"connectionstring": "C:\\ProgramData\\SF\\DiagnosticsStore"`

Nakonec v části `nodeTypes` konfigurace přidejte novou sekci pro namapování dočasných portů, které bude používat systém Windows.  Konfigurační soubor by měl vypadat přibližně takto:

```json
"applicationPorts": {
    "startPort": "20001",
    "endPort": "20031"
},
"ephemeralPorts": {
    "startPort": "20606",
    "endPort": "20861"
},
"isPrimary": true
```

## <a name="validate-the-environment"></a>Ověření prostředí

Skript *TestConfiguration.ps1* v samostatném balíčku se používá jako analyzátor s osvědčenými postupy pro ověření, jestli je možné cluster nasadit do daného prostředí. V článku [Příprava nasazení](service-fabric-cluster-standalone-deployment-preparation.md) jsou vypsány předpoklady a požadavky prostředí. Spusťte skript a ověřte, jestli můžete vývojový cluster vytvořit:

```powershell
cd .\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
```

Měl by se zobrazit výstup podobný následujícímu. Pokud je pro spodní pole „Passed“ (Úspěch) vrácena hodnota `True`, znamená to, že kontroly správnosti úspěšně proběhly a na základě vstupní konfigurace zřejmě bude možné cluster nasadit.

```powershell
Trace folder already exists. Traces will be written to existing trace folder: C:\Users\Administrator\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 :
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
DataDrivesAvailable        : True
NoDomainController         : True
Passed                     : True
```

## <a name="create-the-cluster"></a>Vytvoření clusteru

Po úspěšném ověření konfigurace clusteru spusťte skript *CreateServiceFabricCluster.ps1* a nasaďte cluster Service Fabric do virtuálních počítačů v konfiguračním souboru.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA
```

Pokud všechno funguje správně, měl by výstup vypadat přibližně takto:

```powershell
Your cluster is successfully created! You can connect and manage your cluster using Microsoft Azure Service Fabric Explorer or PowerShell. To connect through PowerShell, run 'Connect-ServiceFabricCluster [ClusterConnectionEndpoint]'.
```

> [!NOTE]
> Trasování nasazení se zapisují do virtuálního počítače nebo počítače, ve kterém jste spustili skript prostředí PowerShell CreateServiceFabricCluster.ps1. Najít je můžete v podsložce DeploymentTraces v rámci adresáře, ze kterého se skript spustil. Pokud chcete zjistit, jestli nasazení Service Fabric do počítače proběhlo úspěšně, vyhledejte nainstalované soubory v adresáři FabricDataRoot podle popisu v části FabricSettings konfiguračního souboru clusteru (ve výchozím nastavení c:\ProgramData\SF). Kromě toho jsou ve Správci úloh vidět spuštěné procesy FabricHost.exe a Fabric.exe.
>
>

### <a name="bring-up-service-fabric-explorer"></a>Vyvolání Service Fabric Exploreru

Teď se můžete připojit ke clusteru pomocí Service Fabric Exploreru buď přímo z jednoho z počítačů prostřednictvím adresy http://localhost:19080/Explorer/index.html, nebo vzdáleně prostřednictvím adresy http://<*IP_adresa_počítače*>:19080/Explorer/index.html.

## <a name="add-and-remove-nodes"></a>Přidávání a odebírání uzlů

Podle toho, jak se vaše obchodní potřeby mění, můžete uzly do samostatného clusteru Service Fabric přidávat nebo je z něj odebírat. Podrobné kroky najdete v tématu [Přidávání uzlů do samostatného clusteru Service Fabric nebo jejich odebírání](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="next-steps"></a>Další kroky

V druhé části série jste se seznámili s paralelním nahráváním velkých objemů náhodných dat do účtu úložiště a naučili jste se například:

> [!div class="checklist"]
> * Konfigurace připojovacího řetězce
> * Sestavení aplikace
> * Spuštění aplikace
> * Ověření počtu připojení

Přejděte ke třetí části série, ve které do vytvořeného clusteru nainstalujete aplikaci.

> [!div class="nextstepaction"]
> [Nainstalovat aplikaci do clusteru Service Fabric](service-fabric-tutorial-standalone-install-an-application.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png

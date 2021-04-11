---
title: Nasadit řadič dat ARC Azure | Režim přímého připojení
description: Vysvětluje, jak nasadit řadič dat v režimu přímého připojení.
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 04/06/2021
ms.topic: overview
ms.openlocfilehash: 220618f167237d5937766eb5e28b9b6569cef76a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031480"
---
#  <a name="deploy-azure-arc-data-controller--direct-connect-mode"></a>Nasadit řadič dat ARC Azure | Režim přímého připojení

Tento článek popisuje, jak nasadit řadič dat ARC Azure v režimu přímého připojení během aktuální verze Preview této funkce. 

V současné době můžete vytvořit řadič dat ARC Azure z Azure Portal. Jiné nástroje pro datové služby s podporou ARC Azure nepodporují vytváření řadičů dat v režimu přímého připojení. Podrobnosti najdete v tématu [známé problémy – datové služby s podporou ARC Azure (Preview)](known-issues.md).

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="complete-prerequisites"></a>Splnění požadavků

Než začnete, ověřte, že jste dokončili požadavky v části [nasazení řadiče dat – přímý režim připojení – požadavky](deploy-data-controller-direct-mode-prerequisites.md).

Z vysoké úrovně Tento článek vysvětluje, jak tyto úlohy provést:

1. Vytvořte rozšíření datových služeb s povoleným obloukem Azure. 
1. Vytvořte vlastní umístění.
1. Nasaďte řadič dat z portálu.

## <a name="create-an-azure-arc-enabled-data-services-extension"></a>Vytvoření rozšíření datových služeb s povoleným obloukem Azure

K vytvoření rozšíření datových služeb použijte rozhraní příkazového řádku k8s.

### <a name="set-environment-variables"></a>Nastavení proměnných prostředí

Nastavte následující proměnné prostředí, které se pak použijí v dalším kroku.

#### <a name="linux"></a>Linux

``` terminal
# where you want the connected cluster resource to be created in Azure 
export subscription=<Your subscription ID>
export resourceGroup=<Your resource group>
export resourceName=<name of your connected kubernetes cluster>
export location=<Azure location>
```

#### <a name="windows-powershell"></a>Windows PowerShell
``` PowerShell
# where you want the connected cluster resource to be created in Azure 
$ENV:subscription="<Your subscription ID>"
$ENV:resourceGroup="<Your resource group>"
$ENV:resourceName="<name of your connected kubernetes cluster>"
$ENV:location="<Azure location>"
```

### <a name="create-the-arc-data-services-extension"></a>Vytvoření rozšíření ARC Data Services

#### <a name="linux"></a>Linux

```bash
az k8s-extension create -c ${resourceName} -g ${resourceGroup} --name ${ADSExtensionName} --cluster-type connectedClusters --extension-type microsoft.arcdataservices --version "1.0.015564" --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g ${resourceGroup} -c ${resourceName} --name ${ADSExtensionName} --cluster-type connectedclusters
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:ADSExtensionName="ads-extension"

az k8s-extension create -c "$ENV:resourceName" -g "$ENV:resourceGroup" --name "$ENV:ADSExtensionName" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --version "1.0.015564" --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --name "$ENV:ADSExtensionName" --cluster-type connectedclusters
```

> [!NOTE]
> Instalace rozšíření ARC (Data Services Extension) může trvat několik minut, než se dokončí.

### <a name="verify-the-arc-data-services-extension-is-created"></a>Ověření, že je vytvořeno rozšíření ARC Data Services

Můžete ověřit, jestli se rozšíření datových služeb s podporou ARC vytvoří buď z portálu, nebo připojením přímo k Kubernetes clusteru s podporou ARC. 

#### <a name="azure-portal"></a>portál Azure
1. Přihlaste se k Azure Portal a přejděte do skupiny prostředků, kde se nachází prostředek připojeného clusteru Kubernetes.
1. Vyberte cluster Kubernetes s povoleným obloukem (Type = "Kubernetes-Azure ARC"), kde bylo rozšíření nasazeno.
1. V oblasti navigace na levé straně v části **Nastavení** vyberte rozšíření (Preview).
1. Mělo by se zobrazit rozšíření, které bylo dříve vytvořeno ve stavu installed (nainstalováno).

:::image type="content" source="media/deploy-data-controller-direct-mode-prerequisites/dc-extensions-dashboard.png" alt-text="Řídicí panel rozšíření":::

#### <a name="kubectl-cli"></a>kubectl CLI

1. Připojte se ke clusteru Kubernetes prostřednictvím okna terminálu.
1. Spusťte následující příkaz a ujistěte se, že je vytvořen obor názvů (1), který je uveden výše, a `bootstrapper` před pokračováním na další krok se nachází ve stavu spuštěno.

``` console
kubectl get pods -n <name of namespace used in the json template file above>
```

Například následující příkaz získá lusky z `arc` oboru názvů.

```console
#Example:
kubectl get pods -n arc
```

## <a name="create-a-custom-location-using-custom-location-cli-extension"></a>Vytvoření vlastního umístění pomocí rozšíření CLI pro vlastní umístění

Vlastní umístění je prostředek Azure, který je ekvivalentní oboru názvů v clusteru Kubernetes.  Vlastní umístění slouží jako cíl k nasazení prostředků do nebo z Azure. Další informace o vlastních umístěních najdete v [dokumentaci k Kubernetes s podporou ARC Azure v části vlastní umístění](../kubernetes/conceptual-custom-locations.md).

### <a name="set-environment-variables"></a>Nastavení proměnných prostředí

#### <a name="linux"></a>Linux

```bash
export clName=mycustomlocation
export clNamespace=arc
export hostClusterId=$(az connectedk8s show -g ${resourceGroup} -n ${resourceName} --query id -o tsv)
export extensionId=$(az k8s-extension show -g ${resourceGroup} -c ${resourceName} --cluster-type connectedClusters --name ${ADSExtensionName} --query id -o tsv)

az customlocation create -g ${resourceGroup} -n ${clName} --namespace ${clNamespace} \
  --host-resource-id ${hostClusterId} \
  --cluster-extension-ids ${extensionId} --location eastus2euap
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:clName="mycustomlocation"
$ENV:clNamespace="arc"
$ENV:hostClusterId = az connectedk8s show -g "$ENV:resourceGroup" -n "$ENV:resourceName" --query id -o tsv
$ENV:extensionId = az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --cluster-type connectedClusters --name "$ENV:ADSExtensionName" --query id -o tsv

az customlocation create -g "$ENV:resourceGroup" -n "$ENV:clName" --namespace "$ENV:clNamespace" --host-resource-id "$ENV:hostClusterId" --cluster-extension-ids "$ENV:extensionId"
```

## <a name="validate--the-custom-location-is-created"></a>Ověří, jestli je vlastní umístění vytvořené.

Z terminálu spusťte následující příkaz, který zobrazí seznam vlastních umístění a ověří, zda se **stav zřizování** podařilo:

```azurecli
az customlocation list -o table
```

## <a name="create-the-azure-arc-data-controller"></a>Vytvoření kontroleru dat ARC Azure

Po vytvoření rozšíření a vlastního umístění pokračujte Azure Portal k nasazení řadiče dat ARC Azure.

1. Přihlaste se k webu Azure Portal.
1. V Azure Marketplace vyhledejte "řadič dat ARC Azure" a inicializujte tok pro vytváření.
1. V části **požadavky** zkontrolujte, jestli je vybraný cluster Kubernetes s podporou Azure ARC (přímý režim), a přejděte k dalšímu kroku.
1. V části **Podrobnosti řadiče dat** vyberte předplatné a skupinu prostředků.
1. Zadejte název řadiče dat.
1. Vyberte konfigurační profil založený na poskytovateli distribuce Kubernetes, do kterého nasazujete.
1. Vyberte vlastní umístění, které jste vytvořili v předchozím kroku.
1. Zadejte podrobnosti pro přihlašovací jméno a heslo správce kontroleru dat.
1. Zadejte podrobnosti pro ClientId, TenantId a tajný klíč klienta pro instanční objekt, který se použije k vytvoření objektů Azure. Podrobné pokyny k vytvoření hlavního účtu služby a rolí, které se pro tento účet musí udělit, najdete v tématu [nahrávání metrik](upload-metrics-and-logs-to-azure-monitor.md) .
1. Klikněte na **Další**, na stránce Souhrn zkontrolujte všechny podrobnosti a klikněte na **vytvořit**.

## <a name="monitor-the-creation"></a>Monitorování vytváření

Pokud stav nasazení Azure Portal ukazuje, že nasazení bylo úspěšné, můžete zjistit stav nasazení řadiče dat ARC v clusteru následujícím způsobem:

```console
kubectl get datacontrollers -n arc
```

## <a name="next-steps"></a>Další kroky

[Vytvoření skupiny serverů PostgreSQL Hyperscale s podporou služby Azure Arc](create-postgresql-hyperscale-server-group.md)

[Vytvoření spravované instance Azure SQL v Arc Azure](create-sql-managed-instance.md)

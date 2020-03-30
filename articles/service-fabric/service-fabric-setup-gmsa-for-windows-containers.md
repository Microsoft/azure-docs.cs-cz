---
title: Nastavení gMSA pro služby kontejneru Azure Service Fabric
description: Naučte se teď nastavit skupinu účtů spravovaných služeb (gMSA) pro kontejner spuštěný ve službě Azure Fabric.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9873e2d7672412b0e1e22c6c2a774cf629fd728a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639203"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Nastavení gMSA pro kontejnery windows spuštěné na Service Fabric

Chcete-li nastavit gMSA (skupinová správa účtů`credspec`služeb), je na všech uzlech v clusteru umístěn soubor specifikace pověření ( ). Soubor lze zkopírovat na všechny uzly pomocí rozšíření virtuálního počítače.  Soubor `credspec` musí obsahovat informace o účtu gMSA. Další informace o `credspec` souboru naleznete v [tématu Vytvoření specifikace pověření](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec). Specifikace pověření a `Hostname` značka jsou určeny v manifestu aplikace. Značka `Hostname` musí odpovídat názvu účtu gMSA, pod kterým kontejner běží.  Značka `Hostname` umožňuje kontejneru ověřit sám na jiné služby v doméně pomocí ověřování protokolem Kerberos.  Ukázka pro určení `Hostname` manifestu `credspec` a v aplikaci je uvedena v následujícím fragmentu:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
Jako další krok si přečtěte následující články:

* [Nasazení kontejneru Windows do service fabricu na Windows Serveru 2016](service-fabric-get-started-containers.md)
* [Nasazení kontejneru Dockeru do Service Fabric na Linuxu](service-fabric-get-started-containers-linux.md)

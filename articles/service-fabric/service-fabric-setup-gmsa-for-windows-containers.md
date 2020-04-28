---
title: Nastavení gMSA pro službu Azure Service Fabric Container Services
description: Naučte se nastavit skupinový účet spravované služby (gMSA) pro kontejner běžící v Azure Service Fabric.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9873e2d7672412b0e1e22c6c2a774cf629fd728a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75639203"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Nastavení gMSA pro kontejnery Windows běžící na Service Fabric

Pokud chcete nastavit gMSA (skupinový účet spravované služby), uloží se soubor specifikace přihlašovacích údajů (`credspec`) do všech uzlů v clusteru. Soubor se dá zkopírovat na všech uzlech pomocí rozšíření virtuálního počítače.  `credspec` Soubor musí obsahovat informace o účtu gMSA. Další informace o `credspec` souboru najdete v tématu [Vytvoření specifikace přihlašovacích údajů](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec). Specifikace přihlašovacích údajů a `Hostname` značka jsou uvedeny v manifestu aplikace. `Hostname` Značka musí odpovídat názvu účtu gMSA, pod kterým je kontejner spuštěný.  `Hostname` Značka umožňuje kontejneru sám se ověřit s ostatními službami v doméně pomocí ověřování pomocí protokolu Kerberos.  Ukázka pro určení `Hostname` a `credspec` v manifestu aplikace je uvedena v následujícím fragmentu kódu:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
V dalším kroku si přečtěte následující články:

* [Nasazení kontejneru Windows pro Service Fabric v systému Windows Server 2016](service-fabric-get-started-containers.md)
* [Nasazení kontejneru Docker pro Service Fabric v systému Linux](service-fabric-get-started-containers-linux.md)

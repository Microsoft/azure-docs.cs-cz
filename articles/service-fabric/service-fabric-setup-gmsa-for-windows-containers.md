---
title: Nastavení gMSA pro službu Azure Service Fabric Container Services
description: Naučte se nastavit skupinový účet spravované služby (gMSA) pro kontejner běžící v Azure Service Fabric.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9873e2d7672412b0e1e22c6c2a774cf629fd728a
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639203"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Nastavení gMSA pro kontejnery Windows běžící na Service Fabric

Pokud chcete nastavit gMSA (skupinový účet spravované služby), uloží se soubor specifikace přihlašovacích údajů (`credspec`) do všech uzlů v clusteru. Soubor se dá zkopírovat na všech uzlech pomocí rozšíření virtuálního počítače.  `credspec` soubor musí obsahovat informace o účtu gMSA. Další informace o souboru `credspec` najdete v tématu [Vytvoření specifikace přihlašovacích údajů](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec). Specifikace pověření a značka `Hostname` jsou uvedeny v manifestu aplikace. Značka `Hostname` musí odpovídat názvu účtu gMSA, pod kterým je kontejner spuštěný.  Značka `Hostname` umožňuje kontejneru sám se ověřit s ostatními službami v doméně pomocí ověřování pomocí protokolu Kerberos.  Ukázka pro určení `Hostname` a `credspec` v manifestu aplikace je uvedena v následujícím fragmentu kódu:

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

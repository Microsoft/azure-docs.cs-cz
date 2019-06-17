---
title: Nastavení gMSA pro služby Azure Service Fabric container Service | Dokumentace Microsoftu
description: Naučte se nastavení gMSA pro kontejneru spuštěného v Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: aljo, subramar
ms.openlocfilehash: fc4edf4cb411ea2872437f4909f06e5ac2b9f622
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64926375"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Nastavení gMSA pro kontejnery Windows se spuštěnou v Service Fabric

Nastavení gMSA (skupiny účtů spravované služby), soubor specifikace přihlašovacích údajů (`credspec`) je umístěn na všech uzlech v clusteru. Soubor je zkopírovat na všech uzlech, pomocí rozšíření virtuálního počítače.  `credspec` Soubor musí obsahovat informace o účtu gMSA. Další informace o `credspec` souborů naleznete v tématu [vytvoření přihlašovacích údajů specifikace](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec). Specifikace přihlašovacích údajů a `Hostname` značky jsou určené v manifestu aplikace. `Hostname` Značka musí odpovídat názvu účtu gMSA spuštěného v kontejneru.  `Hostname` Značky umožňuje kontejneru své autentizaci k dalším službám v doméně pomocí ověřování protokolem Kerberos.  Ukázky pro zadání `Hostname` a `credspec` v aplikaci je manifest ukazuje následující fragment kódu:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
V dalším kroku přečtěte si následující články:

* [Nasazení kontejneru Windows do Service Fabric na Windows serveru 2016](service-fabric-get-started-containers.md)
* [Nasazení kontejneru Dockeru pro Service Fabric v Linuxu](service-fabric-get-started-containers-linux.md)

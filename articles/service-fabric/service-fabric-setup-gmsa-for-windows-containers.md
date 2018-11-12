---
title: Nastavení gMSA pro služby Azure Service Fabric container Service | Dokumentace Microsoftu
description: Naučte se nastavení gMSA pro kontejneru spuštěného v Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: TylerMSFT
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: twhitney, subramar
ms.openlocfilehash: 1adb2e7fcf5542c3f422bf073e5085717c5b82e4
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51299737"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Nastavení gMSA pro kontejnery Windows se spuštěnou v Service Fabric

Nastavení gMSA (skupiny účtů spravované služby), soubor specifikace přihlašovacích údajů (`credspec`) je umístěn na všech uzlech v clusteru. Soubor je zkopírovat na všech uzlech, pomocí rozšíření virtuálního počítače.  `credspec` Soubor musí obsahovat informace o účtu gMSA. Další informace o `credspec` souborů naleznete v tématu [účty služeb](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/live/windows-server-container-tools/ServiceAccounts). Specifikace přihlašovacích údajů a `Hostname` značky jsou určené v manifestu aplikace. `Hostname` Značka musí odpovídat názvu účtu gMSA spuštěného v kontejneru.  `Hostname` Značky umožňuje kontejneru své autentizaci k dalším službám v doméně pomocí ověřování protokolem Kerberos.  Ukázky pro zadání `Hostname` a `credspec` v aplikaci je manifest ukazuje následující fragment kódu:

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

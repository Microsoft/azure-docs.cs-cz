---
title: Instalační program gMSA pro služby Azure Service Fabric kontejneru | Microsoft Docs
description: Naučte se nastavit gMSA pro kontejner spuštěné v Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: subramar
ms.openlocfilehash: e4cd0b42e21609f88edc28c8fd7b5c433d56b3c1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Nastavit gMSA pro kontejnery Windows spuštěné v Service Fabric

Nastavit gMSA (skupina účty spravované služby), specifikace soubor přihlašovacích údajů (`credspec`) je umístěn na všech uzlech v clusteru. Soubor můžete zkopírovat na všech uzlech pomocí rozšíření virtuálního počítače.  `credspec` Soubor musí obsahovat informace o účtu gMSA. Další informace o `credspec` souborů najdete v tématu [účty služby](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/live/windows-server-container-tools/ServiceAccounts). Specifikace přihlašovacích údajů a `Hostname` značky jsou určené v manifestu aplikace. `Hostname` Značky musí odpovídat názvu účtu gMSA kompatibilní se kontejneru.  `Hostname` Značka umožňuje kontejneru ke svému ověření do dalších služeb v doméně pomocí ověřování protokolem Kerberos.  Ukázka pro zadání `Hostname` a `credspec` v aplikaci se zobrazí manifestu v následující fragment kódu:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
Jako další krok přečtěte si následující články:

* [Nasazení kontejneru systému Windows pro Service Fabric na Windows Server 2016](service-fabric-get-started-containers.md)
* [Nasadit kontejner Docker do Service Fabric v systému Linux](service-fabric-get-started-containers-linux.md)

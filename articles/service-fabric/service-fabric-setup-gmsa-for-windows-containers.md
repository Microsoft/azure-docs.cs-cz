---
title: Nastavení gMSA pro službu Azure Service Fabric Container Services | Microsoft Docs
description: Naučte se, jak nastavit gMSA pro kontejner běžící v Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: subramar
ms.openlocfilehash: 09994c7676de8470efff1707598ddf32a48e41a0
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599178"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Nastavení gMSA pro kontejnery Windows běžící na Service Fabric

Pokud chcete nastavit gMSA (skupinový účet spravované služby), uloží se soubor specifikace přihlašovacích údajů (`credspec`) do všech uzlů v clusteru. Soubor se dá zkopírovat na všech uzlech pomocí rozšíření virtuálního počítače.  `credspec` Soubor musí obsahovat informace o účtu gMSA. Další informace o `credspec` souboru najdete v tématu [Vytvoření specifikace přihlašovacích údajů](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec). Specifikace přihlašovacích údajů a `Hostname` značka jsou uvedeny v manifestu aplikace. `Hostname` Značka musí odpovídat názvu účtu gMSA, pod kterým je kontejner spuštěný.  `Hostname` Značka umožňuje kontejneru sám se ověřit s ostatními službami v doméně pomocí ověřování pomocí protokolu Kerberos.  Ukázka pro určení `Hostname` `credspec` a v manifestu aplikace je uvedena v následujícím fragmentu kódu:

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

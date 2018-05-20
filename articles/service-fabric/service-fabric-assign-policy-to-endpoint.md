---
title: Přiřaďte zásady přístupu ke koncovým bodům služby Azure Service Fabric | Microsoft Docs
description: Naučte se přiřazovat zabezpečení zásady přístupu k protokolu HTTP nebo HTTPS koncových bodů ve službě Service Fabric.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: f9de8d213d11a8ccb3ffff484a67560d9e2abe77
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Přiřadit zásady zabezpečení přístupu pro koncové body HTTP a HTTPS
Pokud použijete zásadu spustit jako a service manifest deklaruje prostředky koncový bod protokolu HTTP, je nutné zadat **SecurityAccessPolicy**.  **SecurityAccessPolicy** zajistí, že porty přidělené s těmito koncovými body jsou správně omezen na uživatelský účet, který je služba spuštěna jako. V opačném **http.sys** nemá přístup ke službě, a získat selhání pomocí volání z klienta. Následující příklad se týká účet Customer1 koncový bod názvem **EndpointName**, což dává ho úplná přístupová práva.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

Pro koncový bod HTTPS také označení názvu certifikátu se vraťte do klienta. Odkazujete certifikát pomocí **EndpointBindingPolicy**.  Certifikát je definována v **certifikáty** oddílu manifest aplikace.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Další kroky přečtěte si následující články:
* [Pochopení aplikačního modelu](service-fabric-application-model.md)
* [Zadejte prostředky v service manifest](service-fabric-service-manifest-resources.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png

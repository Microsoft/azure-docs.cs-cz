---
title: Přiřadit zásady přístupu ke koncovým bodům služby Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak přiřadit zabezpečení zásady přístupu ke koncovým bodům protokol HTTP nebo HTTPS ve službě Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: 3e892e443f5e3309add48f939f26ba14eaf5a51b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614193"
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Přiřazení zásad zabezpečení přístupu pro koncové body HTTP a HTTPS
Pokud použijete zásady Spustit jako a manifest služby deklaruje prostředky koncový bod HTTP, je nutné zadat **SecurityAccessPolicy**.  **SecurityAccessPolicy** zajistí, že porty, které jsou přiděleny s těmito koncovými body jsou správně omezeny na uživatelský účet, který je služba spuštěna jako. V opačném případě **http.sys** nebudou mít přístup ke službě, a získáte selhání při voláních z klienta. Následující příklad používá účet Customer1 do koncového bodu volá **Název_koncového_bodu**, které jí úplná přístupová práva.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

Koncový bod HTTPS také určit název certifikátu se vraťte do klienta. Odkazovat pomocí certifikátu **EndpointBindingPolicy**.  Certifikát je definována v **certifikáty** manifestu aplikace.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies>
```

> [!WARNING] 
> Při použití protokolu HTTPS, nepoužívejte stejný port a certifikát pro instance různé služby (nezávisle na aplikaci) nasadí do stejného uzlu. Upgrade dvou různých služeb pomocí stejný port v různé instance aplikace povede k selhání upgradu. Další informace najdete v tématu [upgrade více aplikací pomocí koncových bodů HTTPS ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Další kroky v následujících článcích:
* [Pochopení aplikačního modelu](service-fabric-application-model.md)
* [Zadání prostředků v manifestu služby](service-fabric-service-manifest-resources.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png

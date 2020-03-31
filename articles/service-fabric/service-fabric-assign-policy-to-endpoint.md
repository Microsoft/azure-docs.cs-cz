---
title: Přiřazení zásad přístupu koncovým bodům služby
description: Zjistěte, jak přiřadit zásady přístupu zabezpečení ke koncovým bodům HTTP nebo HTTPS ve službě Service Fabric.
ms.topic: conceptual
ms.date: 03/21/2018
ms.openlocfilehash: c7d30e85848f045b5724bb8bdc6e5c810102c044
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614651"
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Přiřazení zásad přístupu k zabezpečení pro koncové body PROTOKOLU HTTP a HTTPS
Pokud použijete zásadu run-as a manifest služby deklaruje prostředky koncového bodu HTTP, je nutné zadat **zásady SecurityAccessPolicy**.  **SecurityAccessPolicy** zajišťuje, že porty přidělené těmto koncovým bodům jsou správně omezeny na uživatelský účet, který služba běží jako. V opačném případě **http.sys** nemá přístup ke službě a dostanete selhání s voláním z klienta. Následující příklad použije účet Customer1 na koncový bod s názvem **EndpointName**, který mu poskytuje úplná přístupová práva.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

Pro koncový bod HTTPS také označte název certifikátu, který se má vrátit klientovi. Odkazujete na certifikát pomocí **endpointbindingpolicy**.  Certifikát je definován v části **Certifikáty** manifestu aplikace.

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
> Při použití protokolu HTTPS nepoužívejte stejný port a certifikát pro různé instance služby (nezávisle na aplikaci) nasazené do stejného uzlu. Inovace dvou různých služeb pomocí stejného portu v různých instancích aplikace bude mít za následek selhání upgradu. Další informace naleznete v [tématu Inovace více aplikací pomocí koncových bodů HTTPS ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Další kroky naleznete v následujících článcích:
* [Principy aplikačního modelu](service-fabric-application-model.md)
* [Určení prostředků v manifestu služby](service-fabric-service-manifest-resources.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png

---
title: Přiřazení zásad přístupu k koncovým bodům služby
description: Naučte se přiřazovat zásady zabezpečení přístupu k koncovým bodům HTTP nebo HTTPS ve službě Service Fabric.
ms.topic: conceptual
ms.date: 03/21/2018
ms.openlocfilehash: c7d30e85848f045b5724bb8bdc6e5c810102c044
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "75614651"
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Přiřazení zásad zabezpečení přístupu pro koncové body HTTP a HTTPS
Použijete-li zásadu Run-as a manifest služby deklaruje prostředky koncového bodu HTTP, je nutné zadat **SecurityAccessPolicy**.  **SecurityAccessPolicy** zajišťuje, aby porty přidělené těmto koncovým bodům byly správně omezeny na uživatelský účet, ve kterém je služba spuštěna. V opačném případě **http.sys** nemá přístup ke službě a získáte chyby s voláními z klienta. Následující příklad aplikuje účet Customer1 na koncový bod s názvem **koncového bodu**, který poskytuje úplná přístupová práva.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

U koncového bodu HTTPS uveďte taky název certifikátu, který se má vrátit klientovi. Na certifikát odkazujete pomocí **EndpointBindingPolicy**.  Certifikát je definován v části **certifikáty** manifestu aplikace.

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
> Při použití protokolu HTTPS nepoužívejte stejný port a certifikát pro různé instance služby (nezávisle na aplikaci) nasazené do stejného uzlu. Upgrade dvou různých služeb pomocí stejného portu v různých instancích aplikace způsobí selhání při upgradu. Další informace najdete v tématu [upgrade více aplikací s koncovými body https ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Další postup najdete v následujících článcích:
* [Pochopení aplikačního modelu](service-fabric-application-model.md)
* [Určení prostředků v manifestu služby](service-fabric-service-manifest-resources.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png

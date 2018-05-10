---
title: Kontrolní seznam zabezpečení Azure service fabric | Microsoft Docs
description: Tento článek obsahuje sadu kontrolní seznam pro zabezpečení zabezpečení prostředků infrastruktury Azure.
services: security
documentationcenter: na
author: unifycloud
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: ef404b106d600f5cb25a46319d75c8978148b466
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="azure-service-fabric-security-checklist"></a>Kontrolní seznam zabezpečení Azure Service Fabric
Tento článek poskytuje snadno použitelnou kontrolní seznam, který vám pomůže zabezpečit vaše prostředí Azure Service Fabric.

## <a name="introduction"></a>Úvod
Azure Service Fabric je platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb. Service Fabric se taky zaměřuje na problematiku vývoje a správy cloudových aplikací. Vývojáři a správci se můžou vyhnout problémům se složitou infrastrukturou a místo toho se soustředit na implementaci zásadních a náročných úloh, které jsou škálovatelné, spolehlivé a spravovatelné.

## <a name="checklist"></a>Kontrolní seznam
Následující kontrolní seznam použijte při Ujistěte se, že nebyly přehlížen jakékoli závažné potíže v Správa a konfigurace zabezpečeného řešení Azure Service Fabric.


|Kontrolní seznam kategorie| Popis |
| ------------ | -------- |
|[Řízení přístupu na základě role (RBAC)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles) | <ul><li>Řízení přístupu umožňuje omezit přístup k určité operace clusteru pro různé skupiny uživatelů, lepší zabezpečení clusteru pomocí Správce clusteru.</li><li>Správci mají plný přístup k funkcím správy (včetně možnosti pro čtení i zápis). </li><li> Uživatelé, ve výchozím nastavení, mají pouze pro čtení přístup k možnosti správy (například možnosti dotazu) a možnost řešení aplikace a služby.</li></ul>|
|[X.509 – certifikáty a Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>[Certifikáty](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates) použít v clusterech spuštěné úlohy v produkčním prostředí by měla být vytvořen pomocí správně nakonfigurovaných certifikát služby Windows Server nebo získané z schválené [certifikační autoritou (CA)](https://en.wikipedia.org/wiki/Certificate_authority).</li><li>Nikdy používat [dočasné nebo testovací certifikáty](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-create-temporary-certificates-for-use-during-development) v produkčním prostředí, které jsou vytvořené s nástroji, jako [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968.aspx). </li><li>Můžete použít [certifikát podepsaný svým držitelem](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) ale by tak učinit pouze pro testovací clustery a ne v produkčním prostředí.</li></ul>|
|[Zabezpečení clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>Scénáře zabezpečení clusteru zahrnují – uzly zabezpečení, zabezpečení klienta k uzlu, [řízení přístupu na základě Role (RBAC)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).</li></ul>|
|[Ověření clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Ověřuje [komunikaci mezi uzly](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-security.md) pro federaci clusteru. </li></ul>|
|[Ověření serveru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Ověřuje [koncových bodů pro správu clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal) klient pro správu.</li></ul>|
|[Zabezpečení aplikací](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)| <ul><li>Šifrování a dešifrování hodnot konfigurace aplikace.</li><li>   Šifrování dat mezi uzly během replikace.</li></ul>|
|[Certifikát clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) | <ul><li>Tento certifikát je vyžadován k zabezpečení komunikace mezi uzly v clusteru.</li><li>    V části kryptografický otisk a u sekundární v proměnné ThumbprintSecondary nastavte kryptografický otisk certifikátu primární.</li></ul>|
|[ServerCertificate](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)| <ul><li>Tento certifikát je pro klienta zobrazí při pokusu o připojení k tomuto clusteru. Dva certifikáty jiný server, primární a sekundární můžete použít pro upgrade.</li></ul>|
|ClientCertificateThumbprints| <ul><li>To je sada certifikáty, které chcete instalovat na ověřené klienty. </li></ul>|
|ClientCertificateCommonNames| <ul><li>Nastavte běžný název první klientský certifikát pro CertificateCommonName. CertificateIssuerThumbprint je kryptografický otisk tohoto certifikátu vystavitele. </li></ul>|
|ReverseProxyCertificate| <ul><li>Toto je volitelné certifikát, který může být zadán, pokud chcete zabezpečit vaše [Reverse Proxy](https://docs.microsoft.com/en-in/azure/service-fabric/service-fabric-reverseproxy). </li></ul>|
|Key Vault| <ul><li>Používá ke správě certifikátů pro clusterů Service Fabric v Azure.  </li></ul>|


## <a name="next-steps"></a>Další postup
- [Proces upgradu Service Fabric Cluster a očekávání od vás](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade)
- [Správu aplikací Service Fabric v sadě Visual Studio](https://docs.microsoft.com/azure/service-fabric/service-fabric-manage-application-in-visual-studio).
- [Stav služby Fabric modelu ÚVOD](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction).

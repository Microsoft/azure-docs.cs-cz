---
title: Kontrolní seznam zabezpečení Azure service fabric | Dokumentace Microsoftu
description: Tento článek obsahuje sadu kontrolní seznam zabezpečení zabezpečení prostředků infrastruktury Azure.
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
ms.openlocfilehash: d0826d60d766874ddd186bcf406ea987134dab16
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49385851"
---
# <a name="azure-service-fabric-security-checklist"></a>Kontrolní seznam zabezpečení Azure Service Fabric
Tento článek obsahuje snadným ovládáním kontrolní seznam, který vám pomůže zabezpečit vaše prostředí Azure Service Fabric.

## <a name="introduction"></a>Úvod
Azure Service Fabric je platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb. Service Fabric se taky zaměřuje na problematiku vývoje a správy cloudových aplikací. Vývojáři a správci se můžou vyhnout problémům se složitou infrastrukturou a místo toho se soustředit na implementaci zásadních a náročných úloh, které jsou škálovatelné, spolehlivé a spravovatelné.

## <a name="checklist"></a>Kontrolní seznam
Pomocí následujícího kontrolního seznamu vám pomohou zajistit, že nebyly přehlédnuta jakékoli závažné potíže v Správa a konfigurace zabezpečeného řešení Azure Service Fabric.


|Kontrolní seznam kategorií| Popis |
| ------------ | -------- |
|[Řízení přístupu na základě role (RBAC)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles) | <ul><li>Řízení přístupu umožňuje omezit přístup k určité operace clusteru pro různé skupiny uživatelů, lepší zabezpečení clusteru pomocí Správce clusteru.</li><li>Správci mají plný přístup k funkcím správy (včetně možností pro čtení a zápis). </li><li> Uživatelé, ve výchozím nastavení, mají pouze přístup pro čtení k funkce pro správu (například schopnosti příkazů jazyka) a schopnost řešení aplikací a služeb.</li></ul>|
|[Certifikáty X.509 a Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>[Certifikáty](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates) používané clustery spuštěné úlohy v produkčním prostředí by měl vytvořit pomocí správně nakonfigurovaných certifikátů služby Windows Server nebo získané z schválené [certifikační autoritu (CA)](https://en.wikipedia.org/wiki/Certificate_authority).</li><li>Nikdy použít libovolnou [dočasné nebo testovací certifikáty](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-create-temporary-certificates-for-use-during-development) v produkčním prostředí, které jsou vytvořené pomocí nástrojů, jako [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968.aspx). </li><li>Můžete použít [certifikát podepsaný svým držitelem](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) ale musí tak učinit pouze pro testovací clustery a ne v produkčním prostředí.</li></ul>|
|[Zabezpečení clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>Scénáře zabezpečení clusteru zahrnout zabezpečení mezi uzly, uzel klienta zabezpečení [řízení přístupu na základě Role (RBAC)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).</li></ul>|
|[Ověření clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Ověří [komunikace mezi uzly](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-security.md) pro federaci clusteru. </li></ul>|
|[Ověřování serveru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Ověřuje [koncových bodů správy clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal) klientovi pro správu.</li></ul>|
|[Zabezpečení aplikací](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)| <ul><li>Šifrování a dešifrování hodnoty konfigurace aplikace.</li><li>   Šifrování dat napříč uzly během replikace.</li></ul>|
|[Certifikát clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) | <ul><li>Tento certifikát je vyžadován pro zabezpečení komunikace mezi uzly v clusteru.</li><li>    V části kryptografický otisk a že sekundární databáze v proměnné ThumbprintSecondary nastavte kryptografický otisk certifikátu primární.</li></ul>|
|[ServerCertificate](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)| <ul><li>Tento certifikát se zobrazí na klienta při pokusu o připojení k tomuto clusteru. K upgradu můžete použít dva certifikáty jiný server, primární a sekundární lokalitou.</li></ul>|
|ClientCertificateThumbprints| <ul><li>To je sada certifikáty, které chcete nainstalovat na ověření klienti. </li></ul>|
|ClientCertificateCommonNames| <ul><li>Nastavte běžný název první klientský certifikát pro CertificateCommonName. CertificateIssuerThumbprint je kryptografický otisk vystavitele certifikátu. </li></ul>|
|ReverseProxyCertificate| <ul><li>Toto je volitelné certifikát, který může být zadán, pokud chcete zabezpečit vaše [reverzního proxy serveru](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). </li></ul>|
|Key Vault| <ul><li>Použít ke správě certifikátů pro clustery Service Fabric v Azure.  </li></ul>|


## <a name="next-steps"></a>Další postup
- [Proces upgradu clusteru Service Fabric a očekáváních od vás](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade)
- [Správa aplikací Service Fabric v sadě Visual Studio](https://docs.microsoft.com/azure/service-fabric/service-fabric-manage-application-in-visual-studio).
- [Zavedení modelu Service Fabric Health](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction).

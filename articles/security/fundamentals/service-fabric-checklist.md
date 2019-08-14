---
title: Kontrolní seznam zabezpečení Azure Service Fabric | Microsoft Docs
description: Tento článek poskytuje sadu kontrolního seznamu pro zabezpečení prostředků infrastruktury Azure.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2019
ms.author: tomsh
ms.openlocfilehash: c30b70d2fccb7580dcb94c2322c0ad3a52461f34
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927730"
---
# <a name="azure-service-fabric-security-checklist"></a>Kontrolní seznam zabezpečení pro Azure Service Fabric
Tento článek poskytuje snadno použitelný kontrolní seznam, který vám pomůže zabezpečit prostředí Azure Service Fabric.

## <a name="introduction"></a>Úvod
Azure Service Fabric je platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb. Service Fabric se taky zaměřuje na problematiku vývoje a správy cloudových aplikací. Vývojáři a správci se můžou vyhnout problémům se složitou infrastrukturou a místo toho se soustředit na implementaci zásadních a náročných úloh, které jsou škálovatelné, spolehlivé a spravovatelné.

## <a name="checklist"></a>Kontrolní seznam
Pomocí následujícího kontrolního seznamu vám pomůžete zajistit, že jste nezkontrolovali důležité problémy při správě a konfiguraci zabezpečeného řešení Azure Service Fabric.


|Kategorie kontrolního seznamu| Popis |
| ------------ | -------- |
|[Řízení přístupu na základě role (RBAC)](../../service-fabric/service-fabric-cluster-security-roles.md) | <ul><li>Řízení přístupu umožňuje správci clusteru omezit přístup k určitým clusterovým operacím pro různé skupiny uživatelů a tím zvýšit zabezpečení clusteru.</li><li>Správci mají plný přístup k funkcím správy (včetně funkcí pro čtení a zápis). </li><li> Uživatelé mají ve výchozím nastavení přístup jen pro čtení k funkcím pro správu (například možnosti dotazů) a možnost přeložit aplikace a služby.</li></ul>|
|[Certifikáty X. 509 a Service Fabric](../../service-fabric/service-fabric-cluster-security.md) | <ul><li>[Certifikáty](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates) používané v clusterech s provozními úlohami by se měly vytvořit pomocí správně nakonfigurované služby Certificate Service systému Windows Server nebo získat od schválené [certifikační autority (CA)](https://en.wikipedia.org/wiki/Certificate_authority).</li><li>Nikdy nepoužívejte žádné [dočasné ani testovací certifikáty](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-create-temporary-certificates-for-use-during-development) v produkčním prostředí, které se vytvářejí pomocí nástrojů jako [Makecert. exe](https://msdn.microsoft.com/library/windows/desktop/aa386968.aspx). </li><li>Můžete použít [certifikát podepsaný svým držitelem](../../service-fabric/service-fabric-windows-cluster-x509-security.md) , ale měli byste to udělat jenom pro testovací clustery, ale ne v produkčním prostředí.</li></ul>|
|[Zabezpečení clusteru](../../service-fabric/service-fabric-cluster-security.md) | <ul><li>Mezi scénáře zabezpečení clusteru patří zabezpečení mezi uzly, zabezpečení mezi klienty a uzly, [řízení přístupu na základě role (RBAC)](../../service-fabric/service-fabric-cluster-security-roles.md).</li></ul>|
|[Ověřování clusteru](../../service-fabric/service-fabric-cluster-creation-via-arm.md) | <ul><li>Ověřuje komunikaci mezi uzly [a](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-security.md) federačním clusterem. </li></ul>|
|[Ověřování serveru](../../service-fabric/service-fabric-cluster-creation-via-arm.md) | <ul><li>Ověřuje [koncové body správy clusteru](../../service-fabric/service-fabric-cluster-creation-via-portal.md) pro klienta pro správu.</li></ul>|
|[Zabezpečení aplikací](../../service-fabric/service-fabric-cluster-creation-via-arm.md)| <ul><li>Šifrování a dešifrování hodnot konfigurace aplikace.</li><li>   Šifrování dat napříč uzly během replikace.</li></ul>|
|[Certifikát clusteru](../../service-fabric/service-fabric-windows-cluster-x509-security.md) | <ul><li>Tento certifikát je nutný k zabezpečení komunikace mezi uzly v clusteru.</li><li>    Nastavte kryptografický otisk primárního certifikátu v části kryptografický otisk a na sekundárním v proměnné ThumbprintSecondary.</li></ul>|
|[ServerCertificate](../../service-fabric/service-fabric-windows-cluster-x509-security.md)| <ul><li>Tento certifikát se zobrazí klientovi, když se pokusí připojit k tomuto clusteru. Pro upgrade můžete použít dva různé certifikáty serveru, primární a sekundární.</li></ul>|
|ClientCertificateThumbprints| <ul><li>Jedná se o sadu certifikátů, které chcete nainstalovat na ověřených klientech. </li></ul>|
|ClientCertificateCommonNames| <ul><li>Nastavte běžný název prvního klientského certifikátu pro CertificateCommonName. CertificateIssuerThumbprint je kryptografický otisk pro vystavitele tohoto certifikátu. </li></ul>|
|ReverseProxyCertificate| <ul><li>Toto je volitelný certifikát, který je možné zadat, pokud chcete zabezpečení reverzního [proxy serveru](../../service-fabric/service-fabric-reverseproxy.md). </li></ul>|
|Key Vault| <ul><li>Používá se ke správě certifikátů pro Service Fabric clustery v Azure.  </li></ul>|


## <a name="next-steps"></a>Další postup

- [Osvědčené postupy zabezpečení Service Fabric](service-fabric-best-practices.md)
- [Service Fabric proces upgradu clusteru a očekávání od vás](../../service-fabric/service-fabric-cluster-upgrade.md)
- [Správa aplikací Service Fabric v aplikaci Visual Studio](../../service-fabric/service-fabric-manage-application-in-visual-studio.md).
- [Service Fabric Úvod do modelu stavu](../../service-fabric/service-fabric-health-introduction.md).

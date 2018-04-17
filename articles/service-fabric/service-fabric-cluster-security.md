---
title: Zabezpečení clusteru služby Azure Service Fabric | Microsoft Docs
description: Další informace o zabezpečení scénáře pro cluster služby Azure Service Fabric a různých technologií, které můžete použít pro jejich implementaci.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 26b58724-6a43-4f20-b965-2da3f086cf8a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/07/2017
ms.author: aljo
ms.openlocfilehash: f60b428ba7fe93713af68851a3e9d246a3b1641b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="service-fabric-cluster-security-scenarios"></a>Scénáře zabezpečení clusteru Service Fabric
Cluster služby Azure Service Fabric je prostředek, který vlastníte. Je vaší povinností zabezpečit clusterů pomáhá zabránit neoprávněným uživatelům v připojení k nim. Zabezpečení clusteru je obzvláště důležité, pokud používáte produkční zatížení v clusteru. Přestože je možné vytvořit zabezpečená cluster, pokud clusteru zpřístupní koncové body správy do veřejného Internetu, anonymní uživatelé k nim mohla připojit. Zabezpečená clustery nejsou podporovány pro úlohy v produkčním prostředí. 

Tento článek je přehled scénáře zabezpečení pro Azure clusterů a clusterů samostatné a různých technologií, které můžete použít pro jejich implementaci:

* Zabezpečení – uzly
* Uzel Klient zabezpečení
* Řízení přístupu na základě role (RBAC)

## <a name="node-to-node-security"></a>Zabezpečení – uzly
Zabezpečení – uzly pomáhá zabezpečenou komunikaci mezi virtuální počítače nebo počítačů v clusteru. Tento scénář zabezpečení zajistí, že pouze počítače, které jsou autorizované pro připojení ke clusteru mohou být součástí hostování aplikací a služeb v clusteru.

![Diagram komunikaci mezi uzly][Node-to-Node]

Clustery se systémem na Azure a samostatné clustery se systémem Windows oba můžete použít buď [certifikátu zabezpečení](https://msdn.microsoft.com/library/ff649801.aspx) nebo [zabezpečení systému Windows](https://msdn.microsoft.com/library/ff649396.aspx) pro počítačů Windows serveru.

### <a name="node-to-node-certificate-security"></a>Zabezpečení certificate – uzly
Service Fabric používá certifikáty X.509 serveru, které zadáte jako součást konfigurace typ uzlu při vytváření clusteru. Na konci tohoto článku najdete stručný přehled tyto certifikáty jsou a jak můžete získat nebo jejich vytvoření.

Když vytvoříte cluster, buď v portálu Azure pomocí šablony Azure Resource Manager, nebo pomocí šablony JSON samostatné nastavte certifikát zabezpečení. Můžete nastavit primární certifikát a volitelné sekundární certifikát, který se používá pro certifikát efekty přechodu. Primární a sekundární certifikáty nastavíte musí být odlišná od správce klienta a jen pro čtení klientských certifikátů, která jste nastavili pro [klientský uzel zabezpečení](#client-to-node-security).

Další postupy pro nastavení zabezpečení certificate v clusteru s podporou pro Azure najdete v tématu [nastavení clusteru pomocí šablony Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Zjistěte, jak nastavit certifikát zabezpečení v clusteru pro cluster s podporou samostatné systému Windows Server, najdete v tématu [zabezpečení samostatné clusteru v systému Windows pomocí certifikátů X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Zabezpečení systému Windows pro uzel
Další informace o nastavení zabezpečení systému Windows pro samostatné clusteru Windows Server naleznete v tématu [zabezpečení samostatné clusteru v systému Windows pomocí zabezpečení systému Windows](service-fabric-windows-cluster-windows-security.md).

## <a name="client-to-node-security"></a>Uzel Klient zabezpečení
Uzel Klient zabezpečení ověřuje klienty, kteří a pomáhá zajistit komunikaci mezi klientem a jednotlivé uzly v clusteru. Tento typ zabezpečení pomáhá zajistit, že jenom autorizovaným uživatelům přístup k cluster a aplikace, které jsou nasazené na clusteru. Klienti jsou jednoznačně identifikuje prostřednictvím svých pověření zabezpečení systému Windows nebo přihlašovacích certifikátů zabezpečení.

![Diagram komunikace klienta uzlu][Client-to-Node]

Clustery se systémem na Azure a samostatné clustery se systémem Windows oba můžete použít buď [certifikátu zabezpečení](https://msdn.microsoft.com/library/ff649801.aspx) nebo [zabezpečení systému Windows](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Certifikát klienta uzel zabezpečení
Při vytváření clusteru, buď v portálu Azure pomocí šablony Resource Manageru nebo pomocí šablony JSON samostatné nastavení klienta uzlu certifikát zabezpečení. K vytvoření certifikátu, zadejte certifikát klienta správce nebo klientský certifikát uživatele. Jako osvědčený postup, musí být správce klienta a uživatelské certifikáty klienta zadáte odlišná od primární a sekundární certifikáty pro zadáte [-uzly zabezpečení](#node-to-node-security). Ve výchozím nastavení certifikáty clusteru mezi uzly zabezpečení se přidají do seznamu povolených klienta správce certifikátů.

Klienti, kteří se připojte ke clusteru pomocí certifikátu, správce mají plný přístup k možnosti správy. Klienti, kteří připojte se ke clusteru pomocí certifikátu klienta jen pro čtení uživatele obsahovat pouze pro čtení přístup k možnosti správy. Tyto certifikáty se používají pro RBAC, který je popsán dále v tomto článku.

Další postupy pro nastavení zabezpečení certificate v clusteru s podporou pro Azure najdete v tématu [nastavení clusteru pomocí šablony Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Zjistěte, jak nastavit certifikát zabezpečení v clusteru pro cluster s podporou samostatné systému Windows Server, najdete v tématu [zabezpečení samostatné clusteru v systému Windows pomocí certifikátů X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Uzel Klient služby Azure Active Directory zabezpečení v Azure
Pro clustery spuštěná v Azure také můžete zabezpečit přístup do koncových bodů správy pomocí služby Azure Active Directory (Azure AD). Další informace o vytvoření požadovaných artefaktů Azure AD, jak k naplnění, je při vytváření clusteru a jak se připojit k clustery i později, najdete v části [nastavení clusteru pomocí šablony Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="security-recommendations"></a>Doporučení zabezpečení
Pro Azure clusterů, mezi uzly zabezpečení doporučujeme použít k ověřování klientů a certifikáty zabezpečení Azure AD.

Pro samostatné clustery systému Windows Server Pokud máte systém Windows Server 2012 R2 a Windows Active Directory, doporučujeme použít zabezpečení systému Windows s skupinové účty spravované služby. Jinak použijte zabezpečení systému Windows s účty systému Windows.

## <a name="role-based-access-control-rbac"></a>Řízení přístupu na základě role (RBAC)
Řízení přístupu můžete použít k omezení přístupu k určité operace clusteru pro různé skupiny uživatelů. To pomáhá zabezpečit clusteru. Podporuje dva typy řízení přístupu pro klienty, kteří připojení ke clusteru: role správce a role uživatele.

Uživatelé, kteří mají přiřazenou roli správce mají plný přístup k možnosti správy, včetně čtení a zápis funkce. Uživatelé, kteří mají přiřazenou roli uživatele ve výchozím nastavení, mít přístup jenom pro čtení na možnosti správy (například možnosti dotazu). Také řešení aplikace a služby.

Nastavení klienta uživatelů a správců role při vytváření clusteru. Přiřazení rolí tím, že poskytuje samostatné identity (například pomocí certifikátů nebo Azure AD) pro každý typ role. Další informace o výchozí nastavení řízení přístupu a jak změnit výchozí nastavení najdete v tématu [řízení přístupu na základě rolí pro Service Fabric klienty](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>X.509 – certifikáty a Service Fabric
Digitální certifikáty X.509 se běžně používají k ověřování klientů a serverů. Také se používá k šifrování a digitálnímu podepisování zpráv. Další informace o digitální certifikáty X.509 najdete v tématu [práce s certifikáty](http://msdn.microsoft.com/library/ms731899.aspx).

Vzít v úvahu některé důležité věci:

* K vytvoření certifikátů, které pro clustery, které jsou spuštěné úlohy v produkčním prostředí, použijte správně nakonfigurována služba certifikátů systému Windows Server, nebo jedno z schválené [certifikátu certifikační autority](https://en.wikipedia.org/wiki/Certificate_authority).
* Nikdy nepoužívejte všechny dočasné nebo testovací certifikáty, které vytvoříte pomocí nástroje, například MakeCert.exe v produkčním prostředí.
* Můžete použít certifikát podepsaný svým držitelem, ale jenom v clusteru s podporou test. Nepoužívejte certifikát podepsaný svým držitelem v produkčním prostředí.

### <a name="server-x509-certificates"></a>Certifikáty X.509 serveru
Certifikáty serveru mít úlohu primární ověřování serveru (uzel) pro klienty nebo ověřování serveru (uzel) na server (uzel). Když se klient nebo uzel ověřuje uzlu, jeden počáteční kontroly je hodnota běžný název v **subjektu** pole. Tento běžný název nebo jeden z alternativní názvy subjektu certifikáty (SAN) musí být uvedena v seznamu povolených běžných názvů.

Informace o tom, čímž vygenerujete certifikáty, které mají sítě SAN, najdete v části [postup přidání alternativní název subjektu certifikát zabezpečený LDAP](http://support.microsoft.com/kb/931351).

**Subjektu** pole může obsahovat více hodnot. Každá hodnota je s předponou inicializaci označující typ hodnoty. Obvykle je inicializace **CN** (pro *běžný název*), například **CN = www.contoso.com**. **Subjektu** pole může být prázdné. Pokud volitelné **alternativní název subjektu** se vyplní pole, musí mít běžný název certifikátu a jeden záznam za sítě SAN. Tyto jsou vloženy jako **název DNS** hodnoty.

Hodnota **zamýšlené účely** pole certifikátu by měla obsahovat odpovídající hodnotu, jako například **ověřování serveru** nebo **ověření klienta**.

### <a name="client-x509-certificates"></a>Klientské certifikáty X.509
Klientské certifikáty obvykle nejsou vydány certifikační Autority třetích stran. Místo toho osobním úložišti aktuální umístění uživatele obvykle obsahuje umísťují kořenovou autoritou, s klientské certifikáty **zamýšlené účely** hodnotu **ověření klienta**. Klienta můžete použít tento certifikát, pokud se vyžaduje vzájemné ověření.

> [!NOTE]
> Všechny operace správy na cluster Service Fabric vyžadují certifikáty serveru. Klientské certifikáty nelze použít pro správu.

## <a name="next-steps"></a>Další postup
* [Vytvoření clusteru v Azure pomocí šablony Resource Manageru](service-fabric-cluster-creation-via-arm.md) 
* [Vytvoření clusteru pomocí portálu Azure](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

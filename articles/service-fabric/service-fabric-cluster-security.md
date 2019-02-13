---
title: Zabezpečení clusteru služby Azure Service Fabric | Dokumentace Microsoftu
description: Další informace o scénářích zabezpečení pro cluster Azure Service Fabric a různých technologií, které lze použít pro jejich implementaci.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 26b58724-6a43-4f20-b965-2da3f086cf8a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/14/2018
ms.author: aljo
ms.openlocfilehash: 92914b26497634de1a0c61738c6aba37acb37c17
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56109313"
---
# <a name="service-fabric-cluster-security-scenarios"></a>Scénáře zabezpečení clusteru Service Fabric
Cluster Azure Service Fabric je prostředek, který vlastníte. Je vaší odpovědností zajistit clusterům pomáhá zabránit neoprávněným uživatelům v připojení k nim. Zabezpečení clusteru je obzvláště důležité při spouštění úloh v produkčním prostředí v clusteru. I když je možné vytvořit nezabezpečenému clusteru, pokud clusteru zpřístupní koncových bodů správy do veřejného Internetu, můžete k němu připojit anonymním uživatelům. Nezabezpečené clustery nejsou podporovány pro produkční úlohy. 

Tento článek představuje přehled scénářů zabezpečení pro Azure clusterů a samostatné clustery a různých technologií, které lze použít pro jejich implementaci:

* Zabezpečení mezi uzly
* Uzel klienta zabezpečení
* Řízení přístupu na základě role (RBAC)

## <a name="node-to-node-security"></a>Zabezpečení mezi uzly
Zabezpečení mezi uzly pomáhá zajistit komunikaci mezi virtuálními počítači nebo počítačích v clusteru. Tento scénář zabezpečení zajistíte, že jenom počítače, které mají oprávnění k připojení clusteru mohl podílet na hostování aplikací a služeb v clusteru.

![Diagram komunikace mezi uzly][Node-to-Node]

Clustery se systémem na Azure a samostatné clustery se systémem na Windows i můžete použít buď [certifikátu zabezpečení](https://msdn.microsoft.com/library/ff649801.aspx) nebo [zabezpečení Windows](https://msdn.microsoft.com/library/ff649396.aspx) pro počítače se systémy Windows Server.

### <a name="node-to-node-certificate-security"></a>Zabezpečení mezi uzly certifikátu
Service Fabric používá server certifikáty X.509, které zadáte jako součást konfigurace typu uzlu při vytváření clusteru. Na konci tohoto článku uvidíte stručný přehled o co jsou tyto certifikáty a jak můžete získat nebo vytvořit.

Nastavte certifikát zabezpečení při vytváření clusteru, buď na portálu Azure pomocí šablony Azure Resource Manageru nebo pomocí samostatné šablony JSON. Sada Service Fabric SDK výchozího chování je k nasazení a nainstalovat certifikát s nejvzdálenější do budoucí u nichž vyprší platnost certifikátu; klasické chování povolené definování primárního a sekundárního certifikátu, aby ručně spuštěný efekt přechodu a se nedoporučuje používat přes nové funkce. Primární certifikáty, které budou pro použití žádné nejvzdálenější do budoucí datum konce platnosti, by se měla lišit od správce klienta a jen pro čtení klientské certifikáty, které jste nastavili pro [uzel klienta zabezpečení](#client-to-node-security).

Zjistěte, jak nastavit zabezpečení certifikátu v clusteru pro Azure, najdete v článku [nastavení clusteru s použitím šablony Azure Resource Manageru](service-fabric-cluster-creation-via-arm.md).

Zjistěte, jak nastavit zabezpečení certifikátu v clusteru pro samostatný cluster Windows serveru, najdete v článku [zabezpečení samostatného clusteru ve Windows pomocí certifikátů X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Zabezpečení mezi uzly Windows
Zjistěte, jak nastavit Windows zabezpečení samostatného clusteru Windows serveru, najdete v článku [zabezpečení samostatného clusteru ve Windows pomocí Microsoft Windows security](service-fabric-windows-cluster-windows-security.md).

## <a name="client-to-node-security"></a>Uzel klienta zabezpečení
Uzel klienta zabezpečení ověřuje klienty a pomáhá zajistit komunikaci mezi klientem a jednotlivým uzlům v clusteru. Tento typ zabezpečení pomáhá zajistit, že jenom Autorizovaní uživatelé přístup ke clusteru a aplikace, které jsou nasazené na clusteru. Klienti se jednoznačně identifikují pomocí jejich zabezpečených přihlašovacích údajů Windows nebo jejich zabezpečených přihlašovacích údajů certifikátu.

![Diagram komunikace klienta uzlu][Client-to-Node]

Clustery se systémem na Azure a samostatné clustery se systémem na Windows i můžete použít buď [certifikátu zabezpečení](https://msdn.microsoft.com/library/ff649801.aspx) nebo [zabezpečení Windows](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Certifikát klienta mezi uzly zabezpečení
Nastavte certifikát klienta mezi uzly zabezpečení při vytváření clusteru, buď na portálu Azure pomocí šablony Resource Manageru nebo pomocí samostatné šablony JSON. K vytvoření certifikátu zadejte certifikát klienta pro správu nebo klientský certifikát uživatele. Jako osvědčený postup, musí být odlišný od primárního a sekundárního certifikátu zadáte pro správce klienta a uživatelské certifikáty klienta zadáte [zabezpečení mezi uzly](#node-to-node-security). Ve výchozím nastavení certifikáty clusteru pro zabezpečení mezi uzly se přidají do seznamu povolených klienta správce certifikátů.

Klienti, kteří se připojte ke clusteru pomocí certifikátu pro správu mají plný přístup k možnosti správy. Klienti, kteří se připojte ke clusteru pomocí certifikátu klienta uživatele jen pro čtení mají pouze pro čtení k možnosti správy. Tyto certifikáty se používají pro RBAC, která je popsána dále v tomto článku.

Zjistěte, jak nastavit zabezpečení certifikátu v clusteru pro Azure, najdete v článku [nastavení clusteru s použitím šablony Azure Resource Manageru](service-fabric-cluster-creation-via-arm.md).

Zjistěte, jak nastavit zabezpečení certifikátu v clusteru pro samostatný cluster Windows serveru, najdete v článku [zabezpečení samostatného clusteru ve Windows pomocí certifikátů X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Uzel klienta Azure Active Directory zabezpečení v Azure
Azure AD umožňuje organizacím (označuje se jako tenantů) ke správě přístupu uživatelů k aplikacím. Aplikace se dělí na ty, které mají webové přihlašovacího uživatelského rozhraní a ty, které mají nativní klientské prostředí. Pokud jste ještě nevytvořili tenanta, začněte tím, že čtení [získání tenanta služby Azure Active Directory][active-directory-howto-tenant].

Cluster Service Fabric nabízí několik vstupních bodů do jeho funkce správy, včetně webová [Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] a [sady Visual Studio] [ service-fabric-manage-application-in-visual-studio]. V důsledku toho můžete vytvořit dvě aplikace Azure AD pro řízení přístupu ke clusteru, jednu webovou aplikaci a jeden nativní aplikace.

U clusterů běžící v Azure můžete také svázat přístupu ke koncovým bodům správy pomocí služby Azure Active Directory (Azure AD). Další informace o vytvoření požadovaných artefaktů Azure AD a jak do nich přidat při vytváření clusteru, najdete v části [nastavení Azure AD pro ověřování klientů](service-fabric-cluster-creation-setup-aad.md).

## <a name="security-recommendations"></a>Doporučení zabezpečení
Azure v rámci clusterů pro zabezpečení mezi uzly doporučujeme použít zabezpečení Azure AD k ověřování klientů a certifikáty.

Samostatné clustery Windows serveru Pokud máte systém Windows Server 2012 R2 a Windows Active Directory, doporučujeme použít zabezpečení Windows s skupinových účtů spravované služby. S účty Windows, použijte zabezpečení Windows.

## <a name="role-based-access-control-rbac"></a>Řízení přístupu na základě role (RBAC)
Řízení přístupu můžete použít k omezení přístupu k určité operace clusteru pro různé skupiny uživatelů. To pomáhá lépe zabezpečit clusteru. Klienti připojující se ke clusteru podporují dva typy ovládacích prvků přístupu: Role správce a role uživatele.

Uživatelé, kteří mají přiřazenou roli správce mít úplný přístup k funkcím správy, včetně číst a zapisovat možnosti. Uživatelé, kteří mají přiřazenou roli uživatele, ve výchozím nastavení, mít pouze oprávnění ke čtení funkce pro správu (například schopnosti příkazů jazyka). Také řešení aplikací a služeb.

Nastavte správce a uživatele klienta role při vytváření clusteru. Přiřazení rolí tím, že poskytuje samostatné identity (třeba pomocí certifikátů nebo Azure AD) pro každý typ role. Další informace o výchozí nastavení řízení přístupu a jak změnit výchozí nastavení, najdete v části [řízení přístupu na základě rolí pro Service Fabric klienty](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>Certifikáty X.509 a Service Fabric
Digitální certifikáty X.509 se běžně používají k ověřování klientů a serverů. Také se používá k šifrování a digitálnímu podepisování zpráv. Service Fabric pomocí certifikátů X.509 zabezpečení clusteru a poskytuje funkce pro zabezpečení aplikací. Další informace o digitálních certifikátů X.509 naleznete v tématu [práce s certifikáty](https://msdn.microsoft.com/library/ms731899.aspx). Použijete [služby Key Vault](../key-vault/key-vault-overview.md) ke správě certifikátů pro clustery Service Fabric v Azure.

Některé důležité věci k uvážení:

* K vytvoření certifikátů pro clustery, na kterých běží úlohy v produkčním prostředí, použijte správně nakonfigurovaná služba certifikátů systému Windows Server, nebo jedno z schváleném [certifikační autority (CA)](https://en.wikipedia.org/wiki/Certificate_authority).
* Nikdy nepoužívejte všechny dočasné nebo testovací certifikáty, které vytvoříte pomocí nástrojů, jako je MakeCert.exe v produkčním prostředí.
* Můžete použít certifikát podepsaný svým držitelem, ale pouze v clusteru testu. Nepoužívejte certifikát podepsaný svým držitelem v produkčním prostředí.
* Při generování kryptografický otisk certifikátu, je potřeba vygenerovat kryptografický otisk SHA1. SHA1 se používá při konfiguraci klienta a Cluster kryptografické otisky certifikátu.

### <a name="cluster-and-server-certificate-required"></a>Certifikát clusteru a serverem (povinné)
Tyto certifikáty (jeden primární a volitelně sekundární) je potřeba zabezpečit cluster a zabránit neoprávněnému přístupu k němu. Tyto certifikáty poskytovat clusteru a serverem ověřování.

Ověření clusteru se ověřuje komunikace mezi uzly clusteru federace. Pouze uzly, které se mohou prokázat svoji identitu pomocí tohoto certifikátu, můžete připojit ke clusteru. Ověřování serveru ověřování koncových bodů správy clusteru pro správu klienta, tak, aby klient správy ví, že se mluví skutečným clusterem a nikoli "muže in the middle". Tento certifikát také poskytuje zabezpečení SSL pro rozhraní API pro správu protokolu HTTPS a pro Service Fabric Explorer přes protokol HTTPS. Při ověření klient nebo uzel uzlu jeden počáteční kontroly je hodnota běžný název **subjektu** pole. Tento běžný název nebo jeden z certifikátů alternativní názvy subjektů (SAN) se musí nacházet v seznamu povolených běžných názvů.

Certifikát musí splňovat následující požadavky:

* Certifikát musí obsahovat privátní klíč. Obvykle mají tyto certifikáty PFX rozšíření nebo .pem  
* Certifikát musí být vytvořen pro výměnu klíčů, které je možné exportovat do souboru Personal Information Exchange (.pfx).
* **Název subjektu certifikátu musí odpovídat domény, který používáte pro přístup ke clusteru Service Fabric**. Tato shoda se vyžaduje kvůli zajištění SSL pro koncový bod HTTPS management a Service Fabric Explorer clusteru. Nelze získat certifikát SSL od certifikační autority (CA) pro *. cloudapp.azure.com domény. Pro svůj cluster musíte získat název vlastní domény. Pokud požádáte o certifikát od certifikační autority, musí název subjektu certifikátu odpovídat názvu vlastní domény, který používáte pro svůj cluster.

Pár dalších věcí k uvážení:

* **Subjektu** pole může mít víc hodnot. Každá hodnota je předponu inicializace označující typ hodnoty. Inicializace je obvykle **CN** (pro *běžný název*); například **CN = www.contoso.com**. 
* **Subjektu** pole může být prázdné. 
* Pokud volitelný **alternativní název předmětu** pole vyplněné, musí mít běžný název certifikátu a jeden záznam za po síti SAN. Ty se zadávají jako **název DNS** hodnoty. Zjistěte, jak vygenerovat certifikáty, které mají alternativními názvy subjektu, najdete v článku [přidání alternativní název předmětu certifikátem protokolu secure LDAP](https://support.microsoft.com/kb/931351).
* Hodnota **zamýšlené účely** pole certifikátu by měl obsahovat správnou hodnotu, jako například **ověřování serveru** nebo **ověření klienta**.

### <a name="application-certificates-optional"></a>Certifikáty aplikace (volitelné)
Libovolný počet dalších certifikátů můžete nainstalovat na clusteru pro účely zabezpečení aplikace. Před vytvořením clusteru, vezměte v úvahu scénáře zabezpečení aplikací, které vyžadují certifikát nainstalovaný na uzlech, například:

* Šifrování a dešifrování hodnoty konfigurace aplikace.
* Šifrování dat napříč uzly během replikace.

Konceptu vytváření zabezpečených clusterů je stejný, ať už jsou Linux nebo Windows clusterů.

### <a name="client-authentication-certificates-optional"></a>Certifikáty pro ověřování klientů (volitelné)
U správce nebo uživatel operací klienta můžete zadat libovolný počet dalších certifikátů. Když se vyžaduje vzájemné ověřování, může klient použít tento certifikát. Klientské certifikáty obvykle nejsou vystavené certifikační Autority třetích stran. Místo toho osobním úložišti aktuální polohu uživatele obvykle obsahuje klientské certifikáty umísťují kořenovou autoritou. Certifikát by měl mít **zamýšlené účely** hodnotu **ověření klienta**.  

Certifikát clusteru ve výchozím nastavení má oprávnění správce klienta. Tyto další klientské certifikáty by se neměly instalovat do clusteru, ale nejsou zadány jako mohou v konfiguraci clusteru.  Klientské certifikáty je však potřeba nainstalovat na klientských počítačích k připojení ke clusteru a provádět jakékoli operace.

> [!NOTE]
> Všechny operace správy v clusteru Service Fabric vyžadují certifikáty serveru. Klientské certifikáty nelze použít pro správu.

## <a name="next-steps"></a>Další postup
* [Vytvoření clusteru v Azure pomocí šablony Resource Manageru](service-fabric-cluster-creation-via-arm.md) 
* [Vytvoření clusteru pomocí webu Azure portal](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md

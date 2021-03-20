---
title: Zabezpečení clusteru Azure Service Fabric
description: Přečtěte si o scénářích zabezpečení pro cluster Azure Service Fabric a o různých technologiích, které můžete použít k jejich implementaci.
ms.topic: conceptual
ms.date: 08/14/2018
ms.openlocfilehash: 6f7bb785184938fe5c1e20e3c915b0112c7723ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96573064"
---
# <a name="service-fabric-cluster-security-scenarios"></a>Service Fabric scénáře zabezpečení clusteru

Cluster Azure Service Fabric je prostředek, který vlastníte. Je vaše zodpovědnost za zabezpečení clusterů, aby se zabránilo neoprávněným uživatelům v jejich připojení. Zabezpečený cluster je obzvláště důležitý při spuštění produkčních úloh v clusteru. Je možné vytvořit nezabezpečený cluster, ale Pokud cluster zveřejňuje koncové body správy na veřejný Internet, můžou se k němu připojit anonymní uživatelé. Nezabezpečené clustery se pro produkční úlohy nepodporují. 

Tento článek představuje přehled scénářů zabezpečení pro clustery Azure a samostatné clustery a různé technologie, které můžete použít k jejich implementaci:

* Zabezpečení mezi uzly
* Zabezpečení klient-uzel
* Service Fabric řízení přístupu na základě role

## <a name="node-to-node-security"></a>Zabezpečení mezi uzly

Zabezpečení mezi uzly pomáhá zabezpečit komunikaci mezi virtuálními počítači nebo počítači v clusteru. Tento scénář zabezpečení zajišťuje, že se můžou účastnit hostování aplikací a služeb v clusteru jenom počítače, které jsou autorizované pro připojení ke clusteru.

![Diagram komunikace mezi uzly][Node-to-Node]

Clustery běžící v Azure a samostatné clustery, které běží v systému Windows, můžou pro počítače se systémem Windows Server použít [zabezpečení certifikátů](/previous-versions/msp-n-p/ff649801(v=pandp.10)) nebo [zabezpečení systému Windows](/previous-versions/msp-n-p/ff649396(v=pandp.10)) .

### <a name="node-to-node-certificate-security"></a>Zabezpečení certifikátů mezi uzly

Service Fabric používá certifikáty serveru X. 509, které zadáte jako součást konfigurace typu uzlu při vytváření clusteru. Na konci tohoto článku vidíte stručný přehled toho, co tyto certifikáty jsou a jak je můžete získat nebo vytvořit.

Zabezpečení certifikátů nastavte při vytváření clusteru, a to buď v Azure Portal, pomocí Azure Resource Manager šablony, nebo pomocí samostatné šablony JSON. Výchozím chováním sady Service Fabric SDK je nasadit a nainstalovat certifikát od nejdále do data vypršení platnosti. klasické chování povoluje definování primárních a sekundárních certifikátů, aby bylo možné ručně iniciovat přecházení, a nedoporučuje se používat pro nové funkce. Primární certifikáty, které budou použity, budou nejdálené jako budoucí datum vypršení platnosti, měly by se lišit od klienta pro správu a klientských certifikátů jen pro čtení, které jste nastavili pro [zabezpečení klient-uzel](#client-to-node-security).

Informace o tom, jak nastavit zabezpečení certifikátů v clusteru pro Azure, najdete v tématu [Nastavení clusteru pomocí šablony Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Informace o tom, jak nastavit zabezpečení certifikátů v clusteru pro samostatný cluster Windows serveru, najdete v tématu [Zabezpečení samostatného clusteru ve Windows pomocí certifikátů X. 509](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Zabezpečení mezi uzly a systémy Windows

> [!NOTE]
> Ověřování systému Windows je založené na protokolu Kerberos. Protokol NTLM není podporován jako typ ověřování.
>
> Kdykoli je to možné, použijte pro Service Fabric clusterů ověřování pomocí certifikátu X. 509.

Informace o nastavení zabezpečení Windows pro samostatný cluster Windows serveru najdete v tématu zabezpečení [samostatného clusteru ve Windows pomocí zabezpečení systému Windows](service-fabric-windows-cluster-windows-security.md).

## <a name="client-to-node-security"></a>Zabezpečení klient-uzel

Zabezpečení typu klient-uzel ověřuje klienty a pomáhá zabezpečit komunikaci mezi klientem a jednotlivými uzly v clusteru. Tento typ zabezpečení pomáhá zajistit, že ke clusteru a aplikacím nasazeným v clusteru mají přístup jenom autorizovaní uživatelé. Klienti se jednoznačně identifikují prostřednictvím svých přihlašovacích údajů zabezpečení systému Windows nebo jejich přihlašovacích údajů pro zabezpečení certifikátů.

![Diagram komunikace mezi klientem a uzlem][Client-to-Node]

Clustery běžící v Azure a samostatné clustery, které běží v systému Windows, můžou používat [zabezpečení certifikátů](/previous-versions/msp-n-p/ff649801(v=pandp.10)) nebo [zabezpečení systému Windows](/previous-versions/msp-n-p/ff649396(v=pandp.10)), i když je to možné, pokud je to možné, doporučuje se používat ověřování certifikátů X. 509.

### <a name="client-to-node-certificate-security"></a>Zabezpečení certifikátu klienta k uzlu

Nastavte zabezpečení certifikátu klienta na uzel při vytváření clusteru, a to buď v Azure Portal, pomocí Správce prostředků šablony, nebo pomocí samostatné šablony JSON. Certifikát vytvoříte tak, že zadáte certifikát klienta správce nebo klientský certifikát uživatele. Osvědčeným postupem je, že je třeba zadat klientské certifikáty pro správce a uživatele, které zadáte, se liší od primárních a sekundárních certifikátů, které zadáte pro [zabezpečení](#node-to-node-security)mezi uzly. Certifikáty clusteru mají stejná práva jako certifikáty správce klienta. Měli byste je ale používat jenom v clusterech a ne prostřednictvím administrativních uživatelů jako osvědčený postup zabezpečení.

Klienti, kteří se připojují ke clusteru pomocí certifikátu správce, mají úplný přístup k možnostem správy. Klienti, kteří se připojují ke clusteru pomocí klientského certifikátu jen pro čtení, mají přístup jen pro čtení k funkcím pro správu. Tyto certifikáty se používají pro Service Fabric RBAC, která je popsána dále v tomto článku.

Informace o tom, jak nastavit zabezpečení certifikátů v clusteru pro Azure, najdete v tématu [Nastavení clusteru pomocí šablony Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Informace o tom, jak nastavit zabezpečení certifikátů v clusteru pro samostatný cluster Windows serveru, najdete v tématu [Zabezpečení samostatného clusteru ve Windows pomocí certifikátů X. 509](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Zabezpečení Azure Active Directory klienta na uzel v Azure

Azure AD umožňuje organizacím (označovaným jako klienti) spravovat přístup uživatelů k aplikacím. Aplikace jsou rozděleny na ty s webovým přihlašovacím uživatelským rozhraním a s nativním klientským prostředím. Pokud jste ještě nevytvořili tenanta, začněte tím, že si přečtete, [Jak získat klienta Azure Active Directory][active-directory-howto-tenant].

Cluster Service Fabric nabízí několik vstupních bodů ke svým funkcím správy, včetně webových [Service Fabric Explorer][service-fabric-visualizing-your-cluster] a sady [Visual Studio][service-fabric-manage-application-in-visual-studio]. V důsledku toho vytvoříte dvě aplikace Azure AD pro řízení přístupu ke clusteru, jedné webové aplikace a jedné nativní aplikaci.

Pro clustery běžící v Azure můžete také zabezpečit přístup k koncovým bodům správy pomocí Azure Active Directory (Azure AD). Informace o tom, jak vytvořit požadované artefakty Azure AD a jak je naplnit při vytváření clusteru, najdete v tématu [Nastavení Azure AD pro ověřování klientů](service-fabric-cluster-creation-setup-aad.md).

## <a name="security-recommendations"></a>Doporučení zabezpečení

U clusterů Service Fabric nasazených ve veřejné síti hostované v Azure je doporučení pro vzájemné ověřování mezi klientem a uzlem:

* Použití Azure Active Directory pro identitu klienta
* Certifikát pro identitu serveru a šifrování TLS pro komunikaci http

U clusterů Service Fabric nasazených ve veřejné síti hostované v Azure doporučujeme pro ověřování uzlů použít certifikát clusteru.

V případě samostatných clusterů Windows serveru, pokud máte Windows Server 2012 R2 a Windows Active Directory, doporučujeme, abyste používali zabezpečení systému Windows se skupinovými účty spravované služby. V opačném případě použijte zabezpečení systému Windows s účty systému Windows.

## <a name="service-fabric-role-based-access-control"></a>Service Fabric řízení přístupu na základě role

Řízení přístupu můžete použít k omezení přístupu k určitým operacím clusteru pro různé skupiny uživatelů. To pomáhá zvýšit zabezpečení clusteru. Pro klienty, kteří se připojují ke clusteru, jsou podporovány dva typy řízení přístupu: role správce a role uživatele.

Uživatelé, kteří mají přiřazenou roli správce, mají plný přístup k funkcím správy, včetně funkcí pro čtení a zápis. Uživatelům, kteří mají přiřazenou roli uživatele, mají ve výchozím nastavení přístup jen pro čtení k funkcím pro správu (například možnosti dotazů). Můžou také řešit aplikace a služby.

Nastavte role správce a uživatele klienta při vytváření clusteru. Přiřaďte role poskytnutím samostatných identit (například pomocí certifikátů nebo Azure AD) pro každý typ role. Další informace o výchozím nastavení řízení přístupu a o tom, jak změnit výchozí nastavení, najdete v tématu [Service Fabric řízení přístupu na základě role pro klienty Service Fabric](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>Certifikáty X. 509 a Service Fabric

Digitální certifikáty X. 509 se běžně používají k ověřování klientů a serverů. Používají se také k šifrování a digitálnímu podepisování zpráv. Service Fabric k zabezpečení clusteru a poskytování funkcí zabezpečení aplikací používá certifikáty X. 509. Další informace o digitálních certifikátech X. 509 najdete v tématu [práce s certifikáty](/dotnet/framework/wcf/feature-details/working-with-certificates). Pomocí [Key Vault](../key-vault/general/overview.md) můžete spravovat certifikáty pro Service Fabric clustery v Azure.

Mezi důležité věci, které je potřeba vzít v úvahu:

* Pokud chcete vytvořit certifikáty pro clustery, na kterých běží produkční úlohy, použijte správnou nakonfigurovanou službu Certificate Service systému Windows Server nebo jednu z schválených certifikační [autority (CA)](https://en.wikipedia.org/wiki/Certificate_authority).
* Nikdy nepoužívejte žádné dočasné ani testovací certifikáty, které vytvoříte pomocí nástrojů, jako je MakeCert.exe v produkčním prostředí.
* Můžete použít certifikát podepsaný svým držitelem, ale pouze v testovacím clusteru. Nepoužívejte certifikát podepsaný svým držitelem v produkčním prostředí.
* Při generování kryptografického otisku certifikátu nezapomeňte vygenerovat kryptografický otisk SHA1. SHA1 je to, co se používá při konfiguraci kryptografických otisků certifikátu klienta a clusteru.

### <a name="cluster-and-server-certificate-required"></a>Certifikát clusteru a serveru (povinné)

K zabezpečení clusteru a zabránění neoprávněnému přístupu k němu se vyžadují tyto certifikáty (jedna primární a volitelně sekundární). Tyto certifikáty poskytují ověřování clusteru a serverů.

Ověřování clusteru ověřuje komunikaci mezi uzly a federačním clusterem. Do clusteru se mohou připojit pouze uzly, které mohou prokázat svoji identitu pomocí tohoto certifikátu. Ověřování serveru ověřuje koncové body správy clusteru klientovi pro správu, takže klient pro správu ví, že mluví se skutečným clusterem a ne "muž uprostřed". Tento certifikát taky poskytuje TLS pro rozhraní API pro správu HTTPS a pro Service Fabric Explorer přes HTTPS. Když klient nebo uzel ověří uzel, jednou z počátečních kontrol je hodnota společného názvu v poli **subjekt** . V seznamu povolených běžných názvů musí být buď tento běžný název, nebo jeden z alternativních názvů předmětu (San) s certifikáty.

Certifikát musí splňovat následující požadavky:

* Certifikát musí obsahovat privátní klíč. Tyto certifikáty mají obvykle příponu. pfx nebo. pem.  
* Certifikát musí být vytvořen pro výměnu klíčů, který lze exportovat do souboru. pfx (Personal Information Exchange).
* **Název subjektu certifikátu se musí shodovat s doménou, kterou používáte pro přístup ke clusteru Service Fabric**. Tato shoda se vyžaduje k poskytnutí TLS pro koncový bod správy HTTPS clusteru a Service Fabric Explorer. Certifikát TLS/SSL nemůžete od certifikační autority (CA) pro doménu *. cloudapp.azure.com získat. Pro svůj cluster musíte získat název vlastní domény. Pokud požádáte o certifikát od certifikační autority, musí název subjektu certifikátu odpovídat názvu vlastní domény, který používáte pro svůj cluster.

Mezi další věci, které je potřeba vzít v úvahu:

* Pole **předmětu** může mít více hodnot. Každá hodnota je předpona s inicializací k označení typu hodnoty. Obvykle se jedná o inicializaci **CN** (pro *běžný název*); například **cn = www \. contoso.com**.
* Pole **předmětu** může být prázdné.
* Pokud se v poli **alternativní název subjektu** naplní nepovinné pole, musí mít běžný název certifikátu i jednu položku na síť SAN. Ty se zadávají jako hodnoty **názvu DNS** . Informace o tom, jak vygenerovat certifikáty, které mají sítě SAN, najdete v tématu [Postup přidání alternativního názvu subjektu do certifikátu zabezpečeného protokolu LDAP](https://support.microsoft.com/kb/931351).
* Hodnota pole **zamýšleného účelu** certifikátu by měla obsahovat odpovídající hodnotu, jako je **ověřování serveru** nebo **ověřování klientů**.

### <a name="application-certificates-optional"></a>Certifikáty aplikací (volitelné)

V clusteru je možné nainstalovat libovolný počet dalších certifikátů pro účely zabezpečení aplikací. Před vytvořením clusteru Vezměte v úvahu scénáře zabezpečení aplikací, které vyžadují, aby byl v uzlech nainstalován certifikát, například:

* Šifrování a dešifrování hodnot konfigurace aplikace.
* Šifrování dat napříč uzly během replikace.

Koncept vytváření zabezpečených clusterů je stejný, bez ohledu na to, jestli jsou clustery Linux nebo Windows.

### <a name="client-authentication-certificates-optional"></a>Certifikáty pro ověřování klientů (volitelné)

Pro klientské operace správce nebo uživatele lze zadat libovolný počet dalších certifikátů. Klient může tyto certifikáty použít, když je potřeba vzájemné ověřování. Klientské certifikáty obvykle nejsou vydávány certifikační autoritou třetí strany. Místo toho osobní úložiště aktuálního umístění uživatele obvykle obsahuje klientské certifikáty, které jsou umístěny v kořenové autoritě. Certifikát by měl mít **zamýšlenou** hodnotu pro **ověření klienta**.  

Ve výchozím nastavení má certifikát clusteru oprávnění klienta správce. Tyto další klientské certifikáty by neměly být nainstalovány do clusteru, ale jsou zadány jako povolené v konfiguraci clusteru.  Klientské certifikáty ale musí být nainstalované na klientských počítačích pro připojení ke clusteru a provádění operací.

> [!NOTE]
> Všechny operace správy na clusteru Service Fabric vyžadují certifikáty serveru. Klientské certifikáty nelze použít ke správě.

## <a name="next-steps"></a>Další kroky

* [Vytvoření clusteru v Azure pomocí šablony Správce prostředků](service-fabric-cluster-creation-via-arm.md)
* [Vytvoření clusteru pomocí webu Azure Portal](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md

---
title: Zabezpečení clusteru Azure Service Fabric
description: Přečtěte si o scénářích zabezpečení pro cluster Azure Service Fabric a o různých technologiích, které můžete použít k jejich implementaci.
ms.topic: conceptual
ms.date: 08/14/2018
ms.custom: sfrev
ms.openlocfilehash: 5912f98f6a1c82250a66ec4d9fe39f2f69b1cc8f
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80753797"
---
# <a name="service-fabric-cluster-security-scenarios"></a>Scénáře zabezpečení clusteru Service Fabric

Cluster Azure Service Fabric je prostředek, který vlastníte. Je vaší odpovědností zabezpečit clustery, abyste zabránili neoprávněným uživatelům v připojení k nim. Zabezpečený cluster je obzvláště důležitý, pokud v clusteru spouštěte produkční úlohy. Je možné vytvořit nezabezpečený cluster, ale pokud cluster zveřejňuje koncové body správy veřejnému internetu, anonymní uživatelé se k němu mohou připojit. Nezabezpečené clustery nejsou podporovány pro produkční úlohy. 

Tento článek je přehled scénářů zabezpečení pro clustery Azure a samostatné clustery a různé technologie, které můžete použít k jejich implementaci:

* Zabezpečení mezi uzlužinami
* Zabezpečení klient-uzel
* Řízení přístupu na základě role (RBAC)

## <a name="node-to-node-security"></a>Zabezpečení mezi uzlužinami

Zabezpečení mezi uzny pomáhá zabezpečit komunikaci mezi virtuálními počítači nebo počítači v clusteru. Tento scénář zabezpečení zajišťuje, že pouze počítače, které jsou oprávněny připojit se ke clusteru, se mohou účastnit hostování aplikací a služeb v clusteru.

![Diagram komunikace mezi uzlinami][Node-to-Node]

Clustery spuštěné v Azure i v samostatných clusterech spuštěných v systému Windows mohou používat [zabezpečení certifikátů](https://msdn.microsoft.com/library/ff649801.aspx) nebo [zabezpečení systému Windows](https://msdn.microsoft.com/library/ff649396.aspx) pro počítače se systémem Windows Server.

### <a name="node-to-node-certificate-security"></a>Zabezpečení certifikátu mezi uzlinami

Service Fabric používá certifikáty serveru X.509, které zadáte jako součást konfigurace typu uzlu při vytváření clusteru. Na konci tohoto článku můžete zobrazit stručný přehled o tom, co jsou tyto certifikáty a jak je můžete získat nebo vytvořit.

Při vytváření clusteru nastavte zabezpečení certifikátu, buď na webu Azure Portal, pomocí šablony Azure Resource Manageru nebo pomocí samostatné šablony JSON. Výchozí chování sady Service Fabric SDK je nasazení a instalace certifikátu s nejdále do budoucího certifikátu s končící platností. klasické chování povoleno definování primární a sekundární certifikáty, aby ručně iniciované rollovers a není doporučeno pro použití přes nové funkce. Primární certifikáty, které budou používat, mají nejdále do budoucího data vypršení platnosti, by se měly lišit od klienta správce a klientských certifikátů jen pro čtení, které jste nastavili pro [zabezpečení klient-uzel](#client-to-node-security).

Informace o tom, jak nastavit zabezpečení certifikátů v clusteru pro Azure, najdete v [tématu Nastavení clusteru pomocí šablony Správce prostředků Azure](service-fabric-cluster-creation-via-arm.md).

Informace o nastavení zabezpečení certifikátů v clusteru pro samostatný cluster Windows Serveru naleznete v [tématu Zabezpečení samostatného clusteru v systému Windows pomocí certifikátů X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Zabezpečení systému Windows mezi uzlinami

Informace o nastavení zabezpečení systému Windows pro samostatný cluster windows serveru naleznete [v tématu Zabezpečení samostatného clusteru v systému Windows pomocí zabezpečení systému Windows](service-fabric-windows-cluster-windows-security.md).

## <a name="client-to-node-security"></a>Zabezpečení klient-uzel

Zabezpečení klient-uzel ověřuje klienty a pomáhá zabezpečit komunikaci mezi klientem a jednotlivými uzly v clusteru. Tento typ zabezpečení pomáhá zajistit, aby ke clusteru a aplikacím nasazeným v clusteru měli přístup pouze oprávnění uživatelé. Klienti jsou jednoznačně identifikováni pomocí svých pověření zabezpečení systému Windows nebo pověření zabezpečení certifikátu.

![Diagram komunikace mezi klientem a uzly][Client-to-Node]

Clustery spuštěné v Azure a samostatné clustery spuštěné v systému Windows mohou používat [zabezpečení certifikátů](https://msdn.microsoft.com/library/ff649801.aspx) nebo [zabezpečení systému Windows](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Zabezpečení certifikátu klient-uzel

Při vytváření clusteru nastavte zabezpečení certifikátu mezi klientem a uzlem, a to buď na portálu Azure, pomocí šablony Správce prostředků nebo pomocí samostatné šablony JSON. Chcete-li certifikát vytvořit, zadejte klientský certifikát správce nebo klientský certifikát uživatele. Jako osvědčený postup by se zadaný klient správce a uživatelské certifikáty měly lišit od primárních a sekundárních certifikátů, které zadáte pro [zabezpečení mezi uzly](#node-to-node-security). Clusterové certifikáty mají stejná práva jako certifikáty správce klienta. Měly by však být používány pouze clusterem a nikoli administrativními uživateli jako osvědčený postup zabezpečení.

Klienti, kteří se připojují ke clusteru pomocí certifikátu správce, mají úplný přístup k možnostem správy. Klienti, kteří se připojují ke clusteru pomocí klientského certifikátu uživatele jen pro čtení, mají pouze přístup ke správě pro čtení. Tyto certifikáty se používají pro RBAC, který je popsán dále v tomto článku.

Informace o tom, jak nastavit zabezpečení certifikátů v clusteru pro Azure, najdete v [tématu Nastavení clusteru pomocí šablony Správce prostředků Azure](service-fabric-cluster-creation-via-arm.md).

Informace o nastavení zabezpečení certifikátů v clusteru pro samostatný cluster Windows Serveru naleznete v [tématu Zabezpečení samostatného clusteru v systému Windows pomocí certifikátů X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Zabezpečení Azure Active Directory mezi klientem a uzly v Azure

Azure AD umožňuje organizacím (označované jako tenanty) spravovat přístup uživatelů k aplikacím. Aplikace jsou rozděleny do aplikací s webovým přihlašovacím uživatelským uživatelským uživatelským prostředím a nativní klientské prostředí. Pokud jste ještě nevytvořili klienta, začněte tak, že si přečtete [jak získat klienta Azure Active Directory][active-directory-howto-tenant].

Cluster Service Fabric nabízí několik vstupních bodů pro své funkce správy, včetně webové aplikace [Service Fabric Explorer][service-fabric-visualizing-your-cluster] a sady Visual [Studio][service-fabric-manage-application-in-visual-studio]. V důsledku toho vytvoříte dvě aplikace Azure AD pro řízení přístupu ke clusteru, jedné webové aplikace a jedné nativní aplikace.

Pro clustery spuštěné v Azure můžete také zabezpečit přístup ke koncovým bodům správy pomocí Služby Azure Active Directory (Azure AD). Informace o tom, jak vytvořit požadované artefakty Azure AD a jak je naplnit při vytváření clusteru, najdete v [tématu Nastavení Azure AD k ověření klientů](service-fabric-cluster-creation-setup-aad.md).

## <a name="security-recommendations"></a>Doporučení zabezpečení

Pro clustery Service Fabric nasazené ve veřejné síti hostované v Azure je doporučení pro vzájemné ověřování mezi klientem a uzlem:

* Použití služby Azure Active Directory pro identitu klienta
* Certifikát pro identitu serveru a šifrování TLS http komunikace

Pro clustery Service Fabric nasazené ve veřejné síti hostované v Azure je doporučenípro zabezpečení mezi uzly použít certifikát clusteru k ověření uzlů.

Pokud máte systémy Windows Server 2012 R2 a Windows Active Directory, doporučujeme použít zabezpečení systému Windows se skupinovými účty spravovaných služeb v samostatných clusterech Windows Server. V opačném případě použijte zabezpečení systému Windows s účty systému Windows.

## <a name="role-based-access-control-rbac"></a>Řízení přístupu na základě role (RBAC)

Pomocí řízení přístupu můžete omezit přístup k určitým operacím clusteru pro různé skupiny uživatelů. To pomáhá zvýšit zabezpečení clusteru. Pro klienty, kteří se připojují ke clusteru, jsou podporovány dva typy řízení přístupu: role správce a role uživatele.

Uživatelé, kterým je přiřazena role Správce, mají plný přístup k možnostem správy, včetně možností čtení a zápisu. Uživatelé, kterým je přiřazena role Uživatel, mají ve výchozím nastavení pouze přístup pro čtení k možnostem správy (například možnosti dotazů). Mohou také vyřešit aplikace a služby.

Při vytváření clusteru nastavte role správce a klienta uživatele. Přiřaďte role tím, že poskytne samostatné identity (například pomocí certifikátů nebo Azure AD) pro každý typ role. Další informace o výchozím nastavení řízení přístupu a o změně výchozího nastavení naleznete [v tématu Řízení přístupu na základě rolí pro klienty Service Fabric](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>Certifikáty X.509 a servisní tkanina

Digitální certifikáty X.509 se běžně používají k ověřování klientů a serverů. Používají se také k šifrování a digitálnímu podepisování zpráv. Service Fabric používá certifikáty X.509 k zabezpečení clusteru a poskytování funkcí zabezpečení aplikací. Další informace o digitálních certifikátech X.509 naleznete v [tématu Práce s certifikáty](https://msdn.microsoft.com/library/ms731899.aspx). Trezor [klíčů](../key-vault/key-vault-overview.md) slouží ke správě certifikátů pro clustery Service Fabric v Azure.

Některé důležité věci, aby zvážila:

* Chcete-li vytvořit certifikáty pro clustery se spuštěnými produkčními úlohami, použijte správně nakonfigurovanou certifikační službu systému Windows Server nebo službu od schválené [certifikační autority](https://en.wikipedia.org/wiki/Certificate_authority).
* Nikdy nepoužívejte žádné dočasné nebo testovací certifikáty, které vytvoříte pomocí nástrojů, jako je MakeCert.exe v produkčním prostředí.
* Certifikát podepsaný svým držitelem můžete použít, ale pouze v testovacím clusteru. V produkčním prostředí nepoužívejte certifikát podepsaný svým držitelem.
* Při generování kryptografického otisku certifikátu nezapomeňte vygenerovat kryptografický otisk SHA1. SHA1 je co se používá při konfiguraci kryptografických otisků klienta a clusteru certifikátu.

### <a name="cluster-and-server-certificate-required"></a>Certifikát clusteru a serveru (povinné)

Tyto certifikáty (jeden primární a volitelně sekundární) jsou nutné k zabezpečení clusteru a zabránění neoprávněnému přístupu k němu. Tyto certifikáty poskytují ověřování clusteru a serveru.

Ověřování clusteru ověřuje komunikaci mezi mezi mezi mezidoménami clusteru pro federaci clusteru. Ke clusteru se mohou připojit pouze uzly, které mohou prokázat svou identitu pomocí tohoto certifikátu. Ověřování serveru ověřuje koncové body správy clusteru klientovi pro správu, takže klient pro správu ví, že mluví se skutečným clusterem a není "man in the middle". Tento certifikát také poskytuje TLS pro rozhraní API pro správu PROTOKOLU HTTPS a pro průzkumníka prostředků infrastruktury služeb přes protokol HTTPS. Pokud klient nebo uzel ověří uzel, jedním z počátečních kontrol je hodnota běžného názvu v poli **Předmět.** Tento běžný název nebo jeden z alternativních názvů certifikátů (SAN) musí být uveden v seznamu povolených běžných názvů.

Osvědčení musí splňovat tyto požadavky:

* Certifikát musí obsahovat soukromý klíč. Tyto certifikáty mají obvykle rozšíření .pfx nebo .pem  
* Certifikát musí být vytvořen pro výměnu klíčů, který lze exportovat do souboru Výměny osobních informací (.pfx).
* **Název předmětu certifikátu se musí shodovat s doménou, kterou používáte pro přístup k clusteru Service Fabric**. Tato shoda je vyžadována k poskytnutí protokolu TLS pro koncový bod správy HTTPS clusteru a průzkumník prostředků service fabric. Certifikát TLS/SSL nelze získat od certifikační autority pro doménu *.cloudapp.azure.com. Pro svůj cluster musíte získat název vlastní domény. Pokud požádáte o certifikát od certifikační autority, musí název subjektu certifikátu odpovídat názvu vlastní domény, který používáte pro svůj cluster.

Některé další věci, aby zvážila:

* Pole **Předmět** může mít více hodnot. Každá hodnota je předponou s inicializací k označení typu hodnoty. Obvykle je inicializace **CN** (pro *běžný název*); například **CN =\.www contoso.com**.
* Pole **Předmět** může být prázdné.
* Pokud je vyplněno volitelné pole **Alternativní název subjektu,** musí mít běžný název certifikátu i jednu položku pro sítě SAN. Ty jsou zadány jako hodnoty **DNS Name.** Informace o generování certifikátů, které mají názvy SALE, naleznete [v tématu Přidání alternativního názvu subjektu k zabezpečenému certifikátu LDAP](https://support.microsoft.com/kb/931351).
* Hodnota pole **Určené účely** certifikátu by měla obsahovat odpovídající hodnotu, například **ověřování serveru** nebo **ověřování klienta**.

### <a name="application-certificates-optional"></a>Certifikáty žádostí (nepovinné)

V clusteru lze z bezpečnostních důvodů nainstalovat libovolný počet dalších certifikátů. Před vytvořením clusteru zvažte scénáře zabezpečení aplikace, které vyžadují instalaci certifikátu na uzly, například:

* Šifrování a dešifrování hodnot konfigurace aplikace.
* Šifrování dat mezi uzly během replikace.

Koncept vytváření zabezpečených clusterů je stejný, ať už se jedná o clustery Linux nebo Windows.

### <a name="client-authentication-certificates-optional"></a>Ověřovací certifikáty klienta (volitelné)

Pro operace správce nebo klienta uživatele lze zadat libovolný počet dalších certifikátů. Klient může tento certifikát použít, pokud je vyžadováno vzájemné ověřování. Klientské certifikáty obvykle nejsou vydávány certifikační autoritou jiného výrobce. Místo toho osobní úložiště aktuálního umístění uživatele obvykle obsahuje klientské certifikáty umístěné tam kořenovou autoritou. Certifikát by měl mít hodnotu **Určené účely** **ověřování klienta**.  

Ve výchozím nastavení má certifikát clusteru oprávnění klienta správce. Tyto další klientské certifikáty by neměly být nainstalovány do clusteru, ale jsou určeny jako povolené v konfiguraci clusteru.  Klientské certifikáty je však nutné nainstalovat do klientských počítačů pro připojení ke clusteru a provádět všechny operace.

> [!NOTE]
> Všechny operace správy v clusteru Service Fabric vyžadují certifikáty serveru. Klientské certifikáty nelze použít pro správu.

## <a name="next-steps"></a>Další kroky

* [Vytvoření clusteru v Azure pomocí šablony Správce prostředků](service-fabric-cluster-creation-via-arm.md)
* [Vytvoření clusteru pomocí webu Azure Portal](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md

---
title: Osvědčené postupy pro zabezpečení Azure Service Fabric
description: Tento článek poskytuje sadu osvědčených postupů pro Azure Service Fabric Security.
author: unifycloud
ms.author: tomsh
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: 91d7e0777cbdad459e4514a0216146e0d5739f6d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750945"
---
# <a name="azure-service-fabric-security-best-practices"></a>Osvědčené postupy zabezpečení Azure Service Fabric
Nasazení aplikace v Azure je rychlé, jednoduché a nákladově efektivní. Před nasazením cloudové aplikace do produkčního prostředí si Projděte náš seznam základních a doporučených osvědčených postupů pro implementaci zabezpečených clusterů ve vaší aplikaci.

Azure Service Fabric je platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb. Service Fabric se taky zaměřuje na problematiku vývoje a správy cloudových aplikací. Vývojáři a správci se můžou vyhnout problémům se složitou infrastrukturou a místo toho se soustředit na implementaci zásadních a náročných úloh, které jsou škálovatelné, spolehlivé a spravovatelné.

Pro každý osvědčený postup je vysvětleno:

-   Co je nejlepší postup.
-   Proč byste měli implementovat osvědčené postupy.
-   Co se může stát, když neimplementujete osvědčený postup.
-   Způsob, jakým se naučíte implementovat osvědčené postupy.

Doporučujeme následující osvědčené postupy zabezpečení pro Azure Service Fabric:

-   K vytváření zabezpečených clusterů použijte Azure Resource Manager šablony a modul Service Fabric PowerShell.
-   Použijte certifikáty X. 509.
-   Nakonfigurujte zásady zabezpečení.
-   Implementujte konfiguraci zabezpečení Reliable Actors.
-   Nakonfigurujte TLS pro Azure Service Fabric.
-   Použijte izolaci sítě a zabezpečení pomocí Service Fabric Azure.
-   Nakonfigurujte Azure Key Vault pro zabezpečení.
-   Přiřaďte uživatele k rolím.


## <a name="best-practices-for-securing-your-clusters"></a>Osvědčené postupy pro zabezpečení clusterů

Vždy používat zabezpečený cluster:
-   Implementujte zabezpečení clusteru pomocí certifikátů.
-   Poskytněte klientský přístup (správce a jen pro čtení) pomocí Azure Active Directory (Azure AD).

Používat automatizovaná nasazení:
-   Pomocí skriptů vygenerujte, nasaďte a převeďte tajné klíče.
-   Uložte tajné kódy do Azure Key Vault a použijte Azure AD pro všechny ostatní klientské přístupy.
-   Vyžadovat ověřování pro lidské přístup k tajným klíčům.

Kromě toho vezměte v úvahu následující možnosti konfigurace:
-   Pomocí skupin zabezpečení sítě Azure (skupin zabezpečení sítě) vytvořte hraniční sítě (označované také jako zóny demilitarizovaná, zóny DMZ a monitorované podsítě).
-   Přístup k virtuálním počítačům clusteru nebo Správa clusteru pomocí serverů pro odkazy s Připojení ke vzdálené ploše.

Vaše clustery musí být zabezpečené, aby se zabránilo neautorizovaným uživatelům v připojení, zejména když cluster běží v produkčním prostředí. I když je možné vytvořit nezabezpečený cluster, můžou se anonymní uživatelé připojit ke clusteru, Pokud cluster zpřístupňuje koncové body správy k veřejnému Internetu.

Existují tři [scénáře](../../service-fabric/service-fabric-cluster-security.md) implementace zabezpečení clusteru pomocí různých technologií:

-   Zabezpečení mezi uzly: Tento scénář zabezpečuje komunikaci mezi virtuálními počítači a počítači v clusteru. Tato forma zabezpečení zajišťuje, že aplikace a služby v clusteru můžou hostovat jenom ty počítače, které jsou autorizované pro připojení ke clusteru.
V tomto scénáři můžou clustery spuštěné v Azure nebo samostatné clustery, které běží v systému Windows, používat [zabezpečení certifikátů](../../service-fabric/service-fabric-windows-cluster-x509-security.md) nebo [zabezpečení systému Windows](../../service-fabric/service-fabric-windows-cluster-windows-security.md) pro počítače se systémem Windows Server.
-   Zabezpečení typu klient-uzel: Tento scénář zabezpečuje komunikaci mezi klientem Service Fabric a jednotlivými uzly v clusteru.
-   Service Fabric řízení přístupu na základě role (Service Fabric RBAC): Tento scénář používá samostatné identity (certifikáty, Azure AD atd.) pro každou roli správce a klienta, která přistupuje ke clusteru. Identity role se určují při vytváření clusteru.

>[!NOTE]
>**Doporučení zabezpečení pro clustery Azure:** Použijte zabezpečení Azure AD k ověřování klientů a certifikátů pro zabezpečení mezi uzly.

Postup konfigurace samostatného clusteru Windows najdete v tématu [Konfigurace nastavení samostatného clusteru Windows](../../service-fabric/service-fabric-cluster-manifest.md).

Pomocí šablon Azure Resource Manager a modulu Service Fabric PowerShellu vytvořte zabezpečený cluster.
Podrobné pokyny k vytvoření zabezpečeného Service Fabric clusteru pomocí šablon Azure Resource Manager najdete v tématu [Vytvoření clusteru Service Fabric](../../service-fabric/service-fabric-cluster-creation-via-arm.md).

Použijte šablonu Azure Resource Manager:
-   Přizpůsobte si cluster pomocí šablony a nakonfigurujte spravované úložiště pro virtuální pevné disky virtuálních počítačů (VHD).
-   Proveďte změny ve své skupině prostředků pomocí šablony pro jednoduchou správu konfigurace a auditování.

Zacházet s konfigurací clusteru jako s kódem:
-   Při kontrole konfigurací nasazení důkladně poznáte.
-   Nepoužívejte implicitní příkazy pro přímou úpravu prostředků.

Mnoho aspektů [životního cyklu aplikace Service Fabric](../../service-fabric/service-fabric-application-lifecycle.md) lze automatizovat. [Modul Service Fabric PowerShell](../../service-fabric/service-fabric-deploy-remove-applications.md#upload-the-application-package) automatizuje běžné úlohy pro nasazení, upgrade, odebrání a testování aplikací Azure Service Fabric. K dispozici jsou také spravovaná rozhraní API a rozhraní API protokolu HTTP pro správu aplikací.

## <a name="use-x509-certificates"></a>Použití certifikátů X. 509
Vždy Zabezpečte své clustery pomocí certifikátů X. 509 nebo zabezpečení systému Windows. Zabezpečení je konfigurováno pouze při vytváření clusteru. Po vytvoření clusteru není možné zapnout zabezpečení.

Chcete-li zadat [certifikát clusteru](../../service-fabric/service-fabric-windows-cluster-x509-security.md), nastavte hodnotu vlastnosti **ClusterCredentialType** na hodnotu x509. Chcete-li zadat certifikát serveru pro vnější připojení, nastavte vlastnost **ServerCredentialType** na hodnotu x509.

Kromě toho postupujte podle těchto postupů:
-   Vytvořte certifikáty pro produkční clustery pomocí správně nakonfigurované služby Certificate Service systému Windows Server. Můžete také získat certifikáty od schválené certifikační autority (CA).
-   Pro produkční clustery nikdy nepoužívejte dočasný nebo testovací certifikát, pokud byl certifikát vytvořen pomocí MakeCert.exe nebo podobného nástroje.
-   Použijte certifikát podepsaný svým držitelem pro testovací clustery, ale ne pro produkční clustery.

Pokud je cluster nezabezpečený, může se kdokoli připojit ke clusteru anonymně a provádět operace správy. Z tohoto důvodu vždy Zabezpečte provozní clustery pomocí certifikátů X. 509 nebo zabezpečení systému Windows.

Další informace o používání certifikátů X. 509 najdete v tématu [Přidání nebo odebrání certifikátů pro cluster Service Fabric](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md).

## <a name="configure-security-policies"></a>Konfigurace zásad zabezpečení
Service Fabric také zabezpečují prostředky používané aplikacemi. Prostředky, jako jsou soubory, adresáře a certifikáty, se při nasazení aplikace ukládají v rámci uživatelských účtů. Tato funkce usnadňuje spouštění aplikací mezi sebou, dokonce i ve sdíleném hostovaném prostředí.

-   Použít skupinu domény nebo uživatele služby Active Directory: Spusťte službu pod pověřeními pro účet uživatele nebo skupiny služby Active Directory. Nezapomeňte použít místní službu Active Directory v rámci vaší domény a neAzure Active Directory. Přístup k dalším prostředkům v doméně, kterým bylo uděleno oprávnění, pomocí uživatele domény nebo skupiny. Například prostředky, jako jsou sdílené soubory.

-   Přiřaďte zásadu zabezpečení přístupu pro koncové body HTTP a HTTPS: Určete vlastnost **SecurityAccessPolicy** , která použije zásadu **runas** na službu, když manifest služby deklaruje prostředky koncového bodu pomocí protokolu HTTP. Porty přidělené koncovým bodům HTTP jsou správně řízené seznamy přístupu pro uživatelský účet RunAs, pod kterým služba běží. Když zásada není nastavená, http.sys nemá přístup ke službě, a můžete získat chyby s voláními z klienta.

Informace o tom, jak používat zásady zabezpečení v Service Fabricm clusteru, najdete v tématu [Konfigurace zásad zabezpečení pro vaši aplikaci](../../service-fabric/service-fabric-application-runas-security.md).

## <a name="implement-the-reliable-actors-security-configuration"></a>Implementace konfigurace zabezpečení Reliable Actors
Service Fabric Reliable Actors je implementace vzoru návrhu objektu actor. Stejně jako u jakéhokoli vzoru návrhu softwaru se rozhodnutí o použití konkrétního vzoru vychází z toho, jestli problém se softwarem vyhovuje.

Obecně platí, že pomocí vzoru návrhu objektu actor můžete namodelovat řešení pro následující problémy se softwarem nebo scénáře zabezpečení:
-   Vaše místo na problému zahrnuje velký počet (tisíců nebo více) malých, nezávislých a izolovaných jednotek stavu a logiky.
-   Pracujete s objekty s jedním vláknem, které nevyžadují významnou interakci z externích komponent, včetně stavu dotazování v rámci sady aktérů.
-   Instance objektu actor neblokují volající s nepředvídatelnými zpožděními vydávajících vstupně-výstupních operací.

V Service Fabric jsou objekty actor implementovány v Reliable Actors aplikačním rozhraní. Tato architektura je založená na vzoru objektu actor a postavená nad [Service Fabric Reliable Services](../../service-fabric/service-fabric-reliable-services-introduction.md). Každá služba Reliable Actors, kterou píšete, je spolehlivý stavová služba typu dělená.

Každý objekt actor je definován jako instance typu objektu actor, která je shodná se způsobem, jakým objekt .NET je instancí typu .NET. Například **typ objektu actor** , který implementuje funkce kalkulačky, může mít mnoho aktérů tohoto typu, které jsou distribuovány na různých uzlech v rámci clusteru. Každý z distribuovaných objektů actor je jedinečně charakterizován identifikátorem objektu actor.

[Konfigurace zabezpečení replikátoru](../../service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration.md) slouží k zabezpečení komunikačního kanálu, který se používá při replikaci. Tato konfigurace brání službám zobrazovat provoz replikace mezi sebou a zajišťuje zabezpečení vysoce dostupných dat. Ve výchozím nastavení se v prázdném oddílu konfigurace zabezpečení zabrání zabezpečení replikace.
Konfigurace replikátoru konfigurují Replikátor, který zodpovídá za vysoce spolehlivý stav poskytovatele stavu objektu actor.

## <a name="configure-tls-for-azure-service-fabric"></a>Konfigurace TLS pro Azure Service Fabric
Proces ověřování serveru [ověřuje](../../service-fabric/service-fabric-cluster-creation-via-arm.md) koncové body správy clusteru pro klienta pro správu. Klient pro správu pak rozpozná, že se jedná o skutečný cluster. Tento certifikát taky poskytuje [TLS](../../service-fabric/service-fabric-cluster-creation-via-arm.md) pro rozhraní API pro správu https a pro Service Fabric Explorer přes HTTPS.
Pro svůj cluster musíte získat název vlastní domény. Když vyžádáte certifikát od certifikační autority, název subjektu certifikátu se musí shodovat s názvem vlastní domény, který používáte pro svůj cluster.

Pokud chcete nakonfigurovat TLS pro aplikaci, musíte nejdřív získat certifikát SSL/TLS, který je podepsaný certifikační autoritou. CA je důvěryhodná třetí strana, která vydává certifikáty pro účely zabezpečení TLS. Pokud ještě nemáte certifikát SSL/TLS, musíte ho získat od společnosti, která prodává certifikáty SSL/TLS.

Certifikát musí splňovat následující požadavky pro certifikáty SSL/TLS v Azure:
-   Certifikát musí obsahovat privátní klíč.

-   Certifikát se musí vytvořit pro výměnu klíčů a exportovatelné do souboru Personal Information Exchange (. pfx).

-   Název subjektu certifikátu se musí shodovat s názvem domény, který se používá pro přístup ke cloudové službě.

    - Získejte vlastní název domény, který se použije pro přístup ke cloudové službě.
    - Vyžádejte si certifikát od certifikační autority s názvem subjektu, který se shoduje s názvem vlastní domény vaší služby. Pokud například vlastní název domény je __Contoso__**. com**, měl by certifikát z vaší certifikační autority mít název subjektu **. contoso.com** nebo __www__**. contoso.com**.

    >[!NOTE]
    >Nemůžete získat certifikát SSL/TLS z certifikační autority pro doménu __cloudapp__**.NET** .

-   Certifikát musí používat minimálně 2 048 bitů šifrování.

Protokol HTTP není zabezpečený a podléhá odposlouchávání útoků. Data přenášená přes protokol HTTP se odesílají jako prostý text z webového prohlížeče na webový server nebo do jiných koncových bodů. Útočníci mohou zachytit a zobrazit citlivá data, která jsou odesílána prostřednictvím protokolu HTTP, například Podrobnosti o kreditních kartách a přihlášení účtu. Pokud jsou data odesílána nebo odeslána prostřednictvím prohlížeče prostřednictvím protokolu HTTPS, protokol SSL zajišťuje šifrování citlivých informací a jejich zabezpečení před zachytáváním.

Další informace o používání certifikátů SSL/TLS najdete v tématu [Konfigurace TLS pro aplikaci v Azure](../../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Použití izolace a zabezpečení sítě s využitím Azure Service Fabric
Pomocí [šablony Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) jako ukázku nastavte clusterový zabezpečený cluster s protokolem NodeType. Řízení příchozího a odchozího síťového provozu pomocí šablony a skupin zabezpečení sítě.

Šablona má NSG pro každou sadu škálování virtuálních počítačů a slouží k řízení provozu v a ze sady. Pravidla jsou ve výchozím nastavení nakonfigurována tak, aby umožňovala veškerý provoz potřebný pro systémové služby a porty aplikací zadané v šabloně. Zkontrolujte tato pravidla a proveďte jakékoli změny, které odpovídají vašim potřebám, včetně přidání nových pravidel pro aplikace.

Další informace najdete v tématu [běžné síťové scénáře pro Azure Service Fabric](../../service-fabric/service-fabric-patterns-networking.md).

## <a name="set-up-azure-key-vault-for-security"></a>Nastavit Azure Key Vault pro zabezpečení
Service Fabric používá certifikáty k poskytování ověřování a šifrování pro zabezpečení clusteru a jeho aplikací.

Service Fabric pomocí certifikátů X. 509 zabezpečuje cluster a poskytuje funkce zabezpečení aplikací. Pomocí Azure Key Vault můžete [spravovat certifikáty](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md) pro Service Fabric clustery v Azure. Poskytovatel prostředků Azure, který vytvoří clustery, vyžádá certifikáty z trezoru klíčů. Poskytovatel pak nainstaluje certifikáty na virtuální počítače, když je cluster nasazený v Azure.

Mezi [Azure Key Vault](../../key-vault/general/security-overview.md), clusterem Service Fabric a poskytovatelem prostředků, který používá certifikáty, existuje vztah certifikátu. Po vytvoření clusteru se informace o vztahu certifikátu ukládají do trezoru klíčů.

Existují dva základní kroky nastavení trezoru klíčů:
1. Vytvořte skupinu prostředků specificky pro Trezor klíčů.

    Doporučujeme, abyste do vlastní skupiny prostředků umístili Trezor klíčů. Tato akce pomáhá zabránit ztrátě klíčů a tajných kódů v případě, že se odeberou jiné skupiny prostředků, například úložiště, výpočetní prostředky nebo skupina obsahující váš cluster. Skupina prostředků, která obsahuje váš Trezor klíčů, musí být ve stejné oblasti jako cluster, který ho používá.

2. Vytvořte Trezor klíčů v nové skupině prostředků.

    Pro nasazení musí být povolený Trezor klíčů. Poskytovatel výpočetních prostředků pak může získat certifikáty z trezoru a nainstalovat je na instance virtuálních počítačů.

Další informace o tom, jak nastavit Trezor klíčů, najdete v tématu [co je Azure Key Vault?](../../key-vault/general/overview.md).

## <a name="assign-users-to-roles"></a>Přiřazení uživatelů k rolím
Po vytvoření aplikací, které reprezentují váš cluster, přiřaďte uživatele k rolím, které jsou podporovány nástrojem Service Fabric: jen pro čtení a správce. Tyto role můžete přiřadit pomocí Azure Portal.

>[!NOTE]
> Další informace o použití rolí v Service Fabric najdete v tématu [Service Fabric řízení přístupu na základě role pro Service Fabric klienty](../../service-fabric/service-fabric-cluster-security-roles.md).

Azure Service Fabric podporuje dva typy řízení přístupu pro klienty, kteří jsou připojení ke [clusteru Service Fabric](../../service-fabric/service-fabric-cluster-creation-via-arm.md): správce a uživatel. Správce clusteru může pomocí řízení přístupu omezit přístup k určitým operacím clusteru pro různé skupiny uživatelů. Řízení přístupu zajišťuje bezpečnější cluster.

## <a name="next-steps"></a>Další kroky

- [Kontrolní seznam zabezpečení Service Fabric](../../service-fabric/service-fabric-best-practices-security.md)
- Nastavte [vývojové prostředí](../../service-fabric/service-fabric-get-started.md)Service Fabric.
- Přečtěte si o [možnostech podpory Service Fabric](../../service-fabric/service-fabric-support.md).
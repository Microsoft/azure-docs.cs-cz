---
title: Doporučené postupy pro zabezpečení Azure Service Fabric
description: Tento článek obsahuje sadu osvědčených postupů pro zabezpečení Azure Service Fabric.
author: unifycloud
ms.author: tomsh
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: 4548bf77c01194802c2e6203bcbf9fbd240370a2
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461646"
---
# <a name="azure-service-fabric-security-best-practices"></a>Osvědčené postupy zabezpečení Azure Service Fabric
Nasazení aplikace v Azure je rychlé, snadné a nákladově efektivní. Než nasadíte cloudovou aplikaci do produkčního prostředí, projděte si náš seznam základních a doporučených doporučených postupů pro implementaci zabezpečených clusterů ve vaší aplikaci.

Azure Service Fabric je platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb. Service Fabric se taky zaměřuje na problematiku vývoje a správy cloudových aplikací. Vývojáři a správci se můžou vyhnout problémům se složitou infrastrukturou a místo toho se soustředit na implementaci zásadních a náročných úloh, které jsou škálovatelné, spolehlivé a spravovatelné.

Pro každý osvědčený postup vysvětlujeme:

-   Jaký je nejlepší postup.
-   Proč byste měli implementovat osvědčený postup.
-   Co se může stát, pokud neimplementujete osvědčené postupy.
-   Jak se můžete naučit implementovat osvědčené postupy.

Doporučujeme následující doporučené postupy zabezpečení Azure Service Fabric:

-   K vytvoření zabezpečených clusterů použijte šablony Azure Resource Manager a modul PowerShell service fabric.
-   Používejte certifikáty X.509.
-   Nakonfigurujte zásady zabezpečení.
-   Implementujte konfiguraci zabezpečení Reliable Actors.
-   Konfigurace TLS pro Azure Service Fabric.
-   Pomocí azure service fabric utíhej izolaci a zabezpečení sítě.
-   Konfigurace trezoru klíčů Azure pro zabezpečení.
-   Přiřaďte uživatele k rolím.


## <a name="best-practices-for-securing-your-clusters"></a>Doporučené postupy pro zabezpečení clusterů

Vždy používejte zabezpečený cluster:
-   Implementujte zabezpečení clusteru pomocí certifikátů.
-   Poskytněte přístup klientům (jen pro správce a jen pro čtení) pomocí Služby Azure Active Directory (Azure AD).

Použití automatizovaných nasazení:
-   Pomocí skriptů můžete generovat, nasazovat a přepínat tajné kódy.
-   Uložte tajné klíče v azure key vault a používat Azure AD pro všechny ostatní přístup klienta.
-   Vyžadovat ověření pro lidský přístup k tajným kódům.

Dále zvažte následující možnosti konfigurace:
-   Pomocí skupin zabezpečení sítě Azure (NsGs) můžete vytvářet hraniční sítě (označované také jako demilitarizované zóny, zóny DMZ a promítané podsítě).
-   Získejte přístup k virtuálním počítačům clusteru nebo spravujte cluster pomocí serverů pro přeskoče s připojením ke vzdálené ploše.

Clustery musí být zabezpečeny, aby se zabránilo připojení neoprávněných uživatelů, zejména v případě, že je cluster spuštěn v produkčním prostředí. I když je možné vytvořit nezabezpečený cluster, anonymní uživatelé se mohou připojit k vašemu clusteru, pokud cluster zveřejňuje koncové body správy veřejnému Internetu.

Existují tři [scénáře](../../service-fabric/service-fabric-cluster-security.md) pro implementaci zabezpečení clusteru pomocí různých technologií:

-   Zabezpečení mezi uzlem: Tento scénář zabezpečuje komunikaci mezi virtuálními počítači a počítači v clusteru. Tato forma zabezpečení zajišťuje, že pouze počítače, které jsou oprávněny připojit se ke clusteru, mohou hostovat aplikace a služby v clusteru.
V tomto scénáři clustery, které běží v Azure nebo samostatné clustery, které běží v systému Windows, můžete použít zabezpečení [certifikátu](../../service-fabric/service-fabric-windows-cluster-x509-security.md) nebo [zabezpečení systému Windows](../../service-fabric/service-fabric-windows-cluster-windows-security.md) pro počítače se systémem Windows Server.
-   Zabezpečení klient-uzel: Tento scénář zabezpečuje komunikaci mezi klientem Service Fabric a jednotlivými uzly v clusteru.
-   Řízení přístupu na základě rolí (RBAC): Tento scénář používá samostatné identity (certifikáty, Azure AD a tak dále) pro každou roli správce a klienta uživatele, který přistupuje ke clusteru. Při vytváření clusteru zadáte identity rolí.

>[!NOTE]
>**Doporučení zabezpečení pro clustery Azure:** Zabezpečení Azure AD slouží k ověřování klientů a certifikátů pro zabezpečení mezi uzny.

Informace o konfiguraci samostatného clusteru Windows naleznete v [tématu Konfigurace nastavení samostatného clusteru Windows](../../service-fabric/service-fabric-cluster-manifest.md).

K vytvoření zabezpečeného clusteru použijte šablony Azure Resource Manager a modul PowerShell service fabric.
Podrobné pokyny k vytvoření zabezpečeného clusteru Service Fabric pomocí šablon Azure Resource Manager najdete v [tématu Vytvoření clusteru Service Fabric](../../service-fabric/service-fabric-cluster-creation-via-arm.md).

Použijte šablonu Správce prostředků Azure:
-   Přizpůsobte si cluster pomocí šablony ke konfiguraci spravovaného úložiště pro virtuální pevné disky virtuálních počítačů(VD).
-   Podpořte změny ve skupině prostředků pomocí šablony pro snadnou správu konfigurace a auditování.

Považovat konfiguraci clusteru za kód:
-   Buďte důkladní při kontrole konfigurace nasazení.
-   Nepoužívejte implicitní příkazy k přímé úpravě prostředků.

Mnoho aspektů [životního cyklu aplikace Service Fabric](../../service-fabric/service-fabric-application-lifecycle.md) lze automatizovat. Modul [Service Fabric PowerShell](../../service-fabric/service-fabric-deploy-remove-applications.md#upload-the-application-package) automatizuje běžné úlohy pro nasazení, upgrade, odebrání a testování aplikací Azure Service Fabric. K dispozici jsou také spravovaná rozhraní API a http rozhraní API pro správu aplikací.

## <a name="use-x509-certificates"></a>Použití certifikátů X.509
Clustery vždy zabezpečte pomocí certifikátů X.509 nebo zabezpečení systému Windows. Zabezpečení je konfigurováno pouze v době vytvoření clusteru. Po vytvoření clusteru není možné zapnout zabezpečení.

Chcete-li zadat [certifikát clusteru](../../service-fabric/service-fabric-windows-cluster-x509-security.md), nastavte hodnotu vlastnosti **ClusterCredentialType** na Hodnotu X509. Chcete-li zadat certifikát serveru pro vnější připojení, nastavte vlastnost **ServerCredentialType** na X509.

Kromě toho postupujte podle těchto postupů:
-   Vytvořte certifikáty pro produkční clustery pomocí správně nakonfigurované certifikační služby systému Windows Server. Certifikáty můžete také získat od schválené certifikační autority.
-   Nikdy nepoužívejte dočasný nebo testovací certifikát pro produkční clustery, pokud byl certifikát vytvořen pomocí nástroje MakeCert.exe nebo podobného nástroje.
-   Certifikát podepsaný svým držitelem použijte pro testovací clustery, ale ne pro produkční clustery.

Pokud cluster není zabezpečený, může se ke clusteru připojit kdokoli anonymně a provádět operace správy. Z tohoto důvodu vždy zabezpečte produkční clustery pomocí certifikátů X.509 nebo zabezpečení systému Windows.

Další informace o používání certifikátů X.509 naleznete v tématu [Přidání nebo odebrání certifikátů pro cluster Service Fabric](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md).

## <a name="configure-security-policies"></a>Konfigurace zásad zabezpečení
Service Fabric také zabezpečuje prostředky, které jsou používány aplikacemi. Prostředky, jako jsou soubory, adresáře a certifikáty, jsou při nasazení aplikace uloženy pod uživatelskými účty. Tato funkce umožňuje spouštění aplikací bezpečnější od sebe navzájem, a to i ve sdíleném hostovaném prostředí.

-   Použití skupiny nebo uživatele domény služby Active Directory: Spusťte službu pod pověřeními pro uživatelský nebo skupinový účet služby Active Directory. Nezapomeňte používat službu Active Directory místně v rámci vaší domény a ne službu Azure Active Directory. Získejte přístup k dalším prostředkům v doméně, kterým byla udělena oprávnění pomocí uživatele nebo skupiny domény. Například prostředky, jako jsou sdílené složky.

-   Přiřazení zásad přístupu k zabezpečení pro koncové body PROTOKOLU HTTP a HTTPS: Zadejte vlastnost **SecurityAccessPolicy,** která použije zásadu **RunAs** na službu, když manifest služby deklaruje prostředky koncového bodu pomocí protokolu HTTP. Porty přidělené koncovým bodům HTTP jsou správně řízené seznamy pro uživatelský účet RunAs, pod kterým služba běží. Pokud zásada není nastavena, http.sys nemá přístup ke službě a můžete získat selhání s voláním od klienta.

Informace o použití zásad zabezpečení v clusteru Service Fabric naleznete v [tématu Konfigurace zásad zabezpečení pro vaši aplikaci](../../service-fabric/service-fabric-application-runas-security.md).

## <a name="implement-the-reliable-actors-security-configuration"></a>Implementace konfigurace zabezpečení Reliable Actors
Service Fabric Reliable Actors je implementace vzoru návrhu objektu actor. Stejně jako u každého vzoru návrhu softwaru je rozhodnutí použít určitý vzor založeno na tom, zda softwarový problém odpovídá vzoru.

Obecně použijte návrhový vzor objektu actor k tomu, abyste pomohli modelovat řešení následujících softwarových problémů nebo scénářů zabezpečení:
-   Problémový prostor zahrnuje velké množství (tisíce nebo více) malých, nezávislých a izolovaných jednotek stavu a logiky.
-   Pracujete s objekty s jedním podprocesem, které nevyžadují významnou interakci z externích součástí, včetně dotazování stavu napříč sadou objektů actor.
-   Instance objektu actor neblokují volající s nepředvídatelnými zpožděními vydáním vstupně-va operací.

V Service Fabric, objekty actor jsou implementovány v rámci aplikace spolehlivé actors. Tento rámec je založen na objektu actor vzor a je postaven na vrcholu [service fabric spolehlivé služby](../../service-fabric/service-fabric-reliable-services-introduction.md). Každá spolehlivá služba actor, kterou napíšete, je rozdělená stavová spolehlivá služba.

Každý objekt actor je definován jako instance typu objektu actor, shodný se způsobem, jakým je objekt .NET instancí typu .NET. Například **typ objektu actor,** který implementuje funkce kalkulačky může mít mnoho aktérů tohoto typu, které jsou distribuovány na různých uzlech v clusteru. Každý z distribuovaných objektů actor je jedinečně charakterizován identifikátorem objektu actor.

[Konfigurace zabezpečení replikátoru](../../service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration.md) se používají k zabezpečení komunikačního kanálu, který se používá během replikace. Tato konfigurace zabraňuje službám v tom, aby se vzájemně zobcházely přenosy replikace, a zajišťuje, že jsou zabezpečena vysoce dostupná data. Ve výchozím nastavení brání prázdný oddíl konfigurace zabezpečení zabezpečení zabezpečení zabezpečení.
Konfigurace replikátoru nakonfigurují replikátor, který je zodpovědný za to, že stav zprostředkovatele stavu actor je vysoce spolehlivý.

## <a name="configure-tls-for-azure-service-fabric"></a>Konfigurace protokolu TLS pro azure service fabric
Proces ověřování serveru [ověřuje](../../service-fabric/service-fabric-cluster-creation-via-arm.md) koncové body správy clusteru klientovi pro správu. Klient pro správu pak rozpozná, že mluví se skutečným clusterem. Tento certifikát také poskytuje [TLS](../../service-fabric/service-fabric-cluster-creation-via-arm.md) pro rozhraní API pro správu PROTOKOLU HTTPS a pro průzkumníka prostředků infrastruktury služeb přes protokol HTTPS.
Pro svůj cluster musíte získat název vlastní domény. Pokud požadujete certifikát od certifikační autority, musí se název subjektu certifikátu shodovat s vlastním názvem domény, který používáte pro cluster.

Chcete-li nakonfigurovat protokol TLS pro aplikaci, musíte nejprve získat certifikát SSL/TLS podepsaný certifikační autoritou. Certifikační autorita je důvěryhodná třetí strana, která vydává certifikáty pro účely zabezpečení TLS. Pokud ještě nemáte certifikát SSL/TLS, musíte ho získat od společnosti, která certifikáty SSL/TLS prodává.

Certifikát musí splňovat následující požadavky na certifikáty SSL/TLS v Azure:
-   Certifikát musí obsahovat soukromý klíč.

-   Certifikát musí být vytvořen pro výměnu klíčů a musí být exportovatelný do souboru výměny osobních informací (.pfx).

-   Název předmětu certifikátu se musí shodovat s názvem domény, který se používá pro přístup ke cloudové službě.

    - Získejte vlastní název domény, který chcete použít pro přístup ke své cloudové službě.
    - Požádejte certifikační autoritu o certifikát s názvem subjektu, který odpovídá vlastnímu názvu domény vaší služby. Pokud je například název vaší vlastní domény __contoso__**.com**, měl by mít certifikát certifikační autority název subjektu **.contoso.com** nebo __www__**.contoso.com**.

    >[!NOTE]
    >Certifikát SSL/TLS nelze získat od certifikační autority pro doménu __cloudapp__**.net.**

-   Certifikát musí používat minimálně 2 048bitové šifrování.

Protokol HTTP je nezabezpečený a podléhá odposlouchávání útoků. Data přenášená přes protokol HTTP jsou odesílána jako prostý text z webového prohlížeče na webový server nebo mezi jinými koncovými body. Útočníci mohou zachytit a zobrazit citlivá data odeslaná prostřednictvím protokolu HTTP, například údaje o platební kartě a přihlášení k účtu. Když jsou data odesílána nebo odesílána prostřednictvím prohlížeče prostřednictvím protokolu HTTPS, protokol SSL zajišťuje, že citlivé informace jsou šifrovány a zabezpečeny před zachycením.

Další informace o používání certifikátů SSL/TLS najdete [v tématu Konfigurace TLS pro aplikaci v Azure](../../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Použití izolace a zabezpečení sítě pomocí Azure Service Fabric
Nastavte cluster zabezpečeného typu 3 uzlu pomocí [šablony Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) jako ukázky. Řízení příchozího a odchozího síťového provozu pomocí šablony a skupin zabezpečení sítě.

Šablona má skupinu nsg pro každou škálovací sady virtuálních strojů a slouží k řízení provozu do a ze sady. Pravidla jsou ve výchozím nastavení nakonfigurována tak, aby umožňovala veškerý provoz potřebný pro systémové služby a porty aplikací zadané v šabloně. Zkontrolujte tato pravidla a proveďte všechny změny, které budou vyhovovat vašim potřebám, včetně přidání nových pravidel pro vaše aplikace.

Další informace naleznete [v tématu Běžné scénáře sítě pro Azure Service Fabric](../../service-fabric/service-fabric-patterns-networking.md).

## <a name="set-up-azure-key-vault-for-security"></a>Nastavení azure key vaultu pro zabezpečení
Service Fabric používá certifikáty k zajištění ověřování a šifrování pro zabezpečení clusteru a jeho aplikací.

Service Fabric používá certifikáty X.509 k zabezpečení clusteru a k poskytování funkcí zabezpečení aplikací. Azure Key Vault se používá ke [správě certifikátů](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md) pro clustery Service Fabric v Azure. Poskytovatel prostředků Azure, který vytvoří clustery, vytáhne certifikáty z trezoru klíčů. Zprostředkovatel pak nainstaluje certifikáty na virtuálnípočítače při nasazení clusteru v Azure.

Existuje vztah certifikátu mezi [trezorem klíčů Azure](../../key-vault/general/secure-your-key-vault.md), clusterem Service Fabric a poskytovatelem prostředků, který používá certifikáty. Po vytvoření clusteru jsou informace o vztahu certifikátu uloženy v trezoru klíčů.

K nastavení trezoru klíčů jsou dva základní kroky:
1. Vytvořte skupinu prostředků speciálně pro trezor klíčů.

    Doporučujeme umístit trezor klíčů do vlastní skupiny prostředků. Tato akce pomáhá zabránit ztrátě klíčů a tajných klíčů, pokud jsou odebrány jiné skupiny prostředků, jako je například úložiště, výpočetní prostředky nebo skupina, která obsahuje cluster. Skupina prostředků, která obsahuje trezor klíčů, musí být ve stejné oblasti jako cluster, který jej používá.

2. Vytvořte trezor klíčů v nové skupině prostředků.

    Trezor klíčů musí být povolen pro nasazení. Poskytovatel výpočetních prostředků pak může získat certifikáty z trezoru a nainstalovat je na instance virtuálních aplikací.

Další informace o nastavení trezoru klíčů najdete v tématu [Co je Azure Key Vault?](../../key-vault/general/overview.md).

## <a name="assign-users-to-roles"></a>Přiřazení uživatelů k rolím
Po vytvoření aplikací představujících váš cluster přiřaďte uživatele k rolím, které service fabric podporuje: jen pro čtení a správce. Tyto role můžete přiřadit pomocí portálu Azure.

>[!NOTE]
> Další informace o použití rolí v aplikaci Service Fabric naleznete v [tématu Řízení přístupu na základě rolí pro klienty Service Fabric](../../service-fabric/service-fabric-cluster-security-roles.md).

Azure Service Fabric podporuje dva typy řízení přístupu pro klienty, kteří jsou připojeni k [clusteru Service Fabric:](../../service-fabric/service-fabric-cluster-creation-via-arm.md)správce a uživatel. Správce clusteru může pomocí řízení přístupu omezit přístup k určitým operacím clusteru pro různé skupiny uživatelů. Díky řízení přístupu je cluster bezpečnější.

## <a name="next-steps"></a>Další kroky

- [Kontrolní seznam zabezpečení service fabric](service-fabric-checklist.md)
- Nastavte [vývojové prostředí](../../service-fabric/service-fabric-get-started.md)Service Fabric .
- Další informace o [možnostech podpory service fabric](../../service-fabric/service-fabric-support.md).

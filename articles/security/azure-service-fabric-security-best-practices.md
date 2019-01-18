---
title: Azure Service Fabric osvědčené postupy zabezpečení | Dokumentace Microsoftu
description: Tento článek poskytuje sadu osvědčených postupů pro zabezpečení Azure Service Fabric.
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
ms.date: 01/16/2019
ms.author: tomsh
ms.openlocfilehash: 718b6b38121981bdec1f677537f9cd1180dfdb08
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54391231"
---
# <a name="azure-service-fabric-security-best-practices"></a>Azure Service Fabric osvědčené postupy zabezpečení
Nasazení aplikace v Azure je rychlá, snadná a nákladově efektivní. Před nasazením do produkčního prostředí cloudové aplikace, projděte si náš seznam důležité a doporučené osvědčené postupy pro implementaci zabezpečené clustery ve vaší aplikaci.

Azure Service Fabric je platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb. Service Fabric se taky zaměřuje na problematiku vývoje a správy cloudových aplikací. Vývojáři a správci se můžou vyhnout problémům se složitou infrastrukturou a místo toho se soustředit na implementaci zásadních a náročných úloh, které jsou škálovatelné, spolehlivé a spravovatelné.

Pro každý osvědčeným postupem je vám vysvětlíme:

-   Jaký je doporučený postup.
-   Proč byste měli implementovat osvědčený postup.
-   Co může nastat, Pokud implementujete není nejlepším postupem.
-   Jak se můžete naučit provádět osvědčený postup.

Doporučujeme osvědčené postupy zabezpečení Azure Service Fabric:

-   Vytvářet zabezpečené clustery pomocí šablon Azure Resource Manageru a modulu Powershellu pro Service Fabric.
-   Použití certifikátů X.509.
-   Konfigurovat zásady zabezpečení.
-   Implementace zabezpečení konfigurace Reliable Actors.
-   Konfigurace SSL pro Azure Service Fabric.
-   Pomocí izolace sítě a zabezpečení pomocí Azure Service Fabric.
-   Konfigurace služby Azure Key Vault pro zabezpečení.
-   Přiřazení uživatelů k rolím.


## <a name="best-practices-for-securing-your-clusters"></a>Osvědčené postupy pro zabezpečení vašich clusterů

Vždy použijte zabezpečený cluster:
-   Implementace zabezpečení clusteru pomocí certifikátů.
-   Zadejte klientský přístup (admin a jen pro čtení) pomocí služby Azure Active Directory (Azure AD).

Pomocí automatizovaných nasazení:
-   Vytvořit, nasadit a nespotřebujete tajné klíče pomocí skriptů.
-   Store tajné klíče ve službě Azure Key Vault a pomocí služby Azure AD pro všechny ostatní klientský přístup.
-   Vyžadovat ověření pro lidské přístup k tajné klíče.

Kromě toho zvažte následující možnosti konfigurace:
-   Vytvoříte pomocí skupiny zabezpečení sítě Azure (Nsg) hraniční sítě (označované také jako demilitarizovaná zón, zóny DMZ a monitorovaná podsítě).
-   Přístup ke clusteru virtuálních počítačů (VM) nebo pomocí připojení ke vzdálené ploše jump servery pro správu clusteru.

Své clustery musí být zabezpečená umožňují zabránit neoprávněným uživatelům v připojení, zejména když cluster běží v produkčním prostředí. I když je možné vytvořit nezabezpečený cluster, anonymní uživatelé se mohou připojit ke svému clusteru Pokud clusteru zpřístupní koncových bodů správy do veřejného Internetu.

Existují tři [scénáře](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) pro implementaci zabezpečení clusteru pomocí různých technologií:

-   Zabezpečení mezi uzly: Tento scénář zabezpečuje komunikaci mezi virtuálními počítači a počítačů v clusteru. Tato forma zabezpečení zajišťuje, že může hostovat pouze do počítačů, které mají oprávnění k připojení ke clusteru, aplikací a služeb v clusteru.
V tomto scénáři, clustery, které běží na Azure nebo samostatné clustery se systémem Windows, můžete použít buď [certifikátu zabezpečení](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) nebo [zabezpečení Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security) pro počítače s Windows serverem.
-   Uzel klienta zabezpečení: Tento scénář zabezpečuje komunikaci mezi klientem Service Fabric a jednotlivým uzlům v clusteru.
-   Řízení přístupu na základě role (RBAC): Tento scénář používá samostatné identity (certifikáty, Azure AD, a tak dále) pro každou roli klienta správce a uživatele, který přistupuje ke clusteru. Identity rolí se zadávají při vytváření clusteru.

>[!NOTE]
>**Doporučení zabezpečení pro Azure clustery:** Zabezpečení Azure AD použijte k ověřování klientů a certifikáty pro zabezpečení mezi uzly.

Nastavení samostatného clusteru Windows viz [nakonfigurovat nastavení pro samostatný cluster Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest).

Vytvoření zabezpečeného clusteru pomocí šablony Azure Resource Manageru a modulu Powershellu pro Service Fabric.
Podrobné pokyny pro vytvoření zabezpečeného clusteru Service Fabric pomocí šablony Azure Resource Manageru, najdete v části [vytváření clusteru Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Pomocí šablony Azure Resource Manageru:
-   Přizpůsobení clusteru pomocí šablony konfiguraci spravované úložiště pro virtuální počítač virtuální pevné disky (VHD).
-   Jednotka změny do vaší skupiny prostředků pomocí šablony pro správu jednoduché konfiguraci a auditování.

Konfigurace clusteru považovat za kód:
-   Být důkladné při kontrole konfigurace vašeho nasazení.
-   Vyhněte se použití implicitní příkazy přímo upravit vaše prostředky.

Mnoho aspektů [životního cyklu aplikací Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle) lze automatizovat. [Modul Powershellu pro Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications#upload-the-application-package) automatizuje běžné úlohy pro nasazení, upgrade, odebrání a testování aplikací pro Azure Service Fabric. Spravovaná rozhraní API a rozhraní API HTTP pro správu aplikací jsou také k dispozici.

## <a name="use-x509-certificates"></a>Použití certifikátů X.509
Své clustery vždy zabezpečení pomocí certifikátů X.509 nebo zabezpečení Windows. Zabezpečení se konfiguruje jenom při vytváření clusteru. Není možné zapnout na zabezpečení po vytvoření clusteru.

Chcete-li určit [certifikát clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security), nastavte hodnotu **ClusterCredentialType** vlastnost X509. Chcete-li zadat certifikát serveru pro externí připojení, nastavte **ServerCredentialType** vlastnost X509.

Kromě toho postupujte podle těchto postupů:
-   Vytvoření certifikátů pro produkční clustery pomocí správně nakonfigurovaných certifikát služby Windows Server. Certifikáty můžete získat také z schválené certifikační autority (CA).
-   Nikdy nepoužívejte dočasný nebo testování certifikátu pro produkční clustery, pokud byl certifikát vytvořen pomocí MakeCert.exe nebo něco podobného.
-   Použijte certifikát podepsaný svým držitelem pro testovací clustery, ale nikoli pro produkční clustery.

Pokud cluster není zabezpečený, všem uživatelům připojit se anonymně do clusteru a provádět operace správy. Z tohoto důvodu se vždy produkční clustery zabezpečené pomocí certifikátů X.509 nebo zabezpečení Windows.

Další informace o používání certifikátů X.509 naleznete v tématu [přidat nebo odebrat certifikáty pro cluster Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure).

## <a name="configure-security-policies"></a>Konfigurace zásad zabezpečení
Service Fabric také zabezpečuje prostředky, které jsou používané aplikacemi. Zdroje, jako jsou soubory, adresáře a certifikáty jsou uloženy v části uživatelské účty, když je aplikace nasazená. Tato funkce umožňuje spouštění aplikací bezpečnější jeden z druhého, i do sdíleného hostovaného prostředí.

-   Použijte skupiny domény služby Active Directory nebo uživatele: Spuštění služby pod přihlašovacími údaji pro uživatele služby Active Directory nebo účet skupiny. Nezapomeňte použít služby Active Directory místně v rámci vaší domény a ne Azure Active Directory. Přístup k jiným prostředkům v doméně, která byla udělena oprávnění s použitím účtem uživatele domény nebo skupiny. Například prostředky, jako jsou sdílené složky.

-   Přiřazení zásad zabezpečení přístupu pro koncové body HTTP a HTTPS: Zadejte **SecurityAccessPolicy** vlastnost použít **RunAs** zásady ke službě při manifestu služby deklaruje koncový bod prostředků pomocí protokolu HTTP. Porty, které jsou přiděleny do koncových bodů HTTP jsou správně přístup řízen seznamy pro uživatelský účet Spustit jako, který je služba spuštěna pod. Pokud zásady není nastavena, ovladač http.sys nemá přístup ke službě a získáte selhání při voláních z klienta.

Další informace o použití zásad zabezpečení v clusteru Service Fabric, najdete v článku [konfigurovat zásady zabezpečení pro vaši aplikaci](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-runas-security).

## <a name="implement-the-reliable-actors-security-configuration"></a>Implementace zabezpečení konfigurace Reliable Actors
Service Fabric Reliable Actors je implementace návrhový vzor objektu actor. Stejně jako u jakékoli vzoru návrhu softwaru rozhodnout a použít konkrétní vzor vychází Určuje, zda problém softwaru vyhovuje vzoru.

Obecně platí pomáhá řešením model pro následující problémy se softwarem nebo scénáře zabezpečení pomocí návrhový vzor objektu actor:
-   Zahrnuje velké množství prostoru problém (tisíce nebo více) malé, nezávislou a izolovanou jednotek ve stavu a logiku.
-   Pracujete s objekty s jedním vláknem, které nevyžadují významné interakce z externí komponenty, včetně dotazování na stav mezi sadu objektů actor.
-   Vaše instance objektu actor nedošlo k blokování volajících s neočekávaným zpožděním vydáním vstupně-výstupních operací.

V Service Fabric actors jsou implementovány v rámci aplikace Reliable Actors. Toto rozhraní je založené na vzor objektu actor a postavený na [Service Fabric Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction). Každá služba reliable actors, který píšete je dělené spolehlivé stavové služby.

Každý objekt actor se definuje jako instance objektu actor typu identické s stejným způsobem jako objekt .NET je instance typu .NET. Například **typ actor** , že implementuje funkce kalkulačku může mít mnoho actors tohoto typu, které jsou distribuovány na různých uzlech v clusteru. Každá distribuovaná actors jednoznačně charakterizován identifikátor objektu actor.

[Konfigurace zabezpečení replikátoru](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration) slouží k zabezpečení komunikačního kanálu, který se používá během replikace. Tato konfigurace zabraňuje zobrazuje provoz replikace druhé strany služby a zajišťuje, že je zabezpečení dat s vysokou dostupností. Ve výchozím nastavení zabraňuje oddíl konfigurace prázdný zabezpečení zabezpečení replikace.
Konfigurace replikace nakonfigurovat replikátoru je zodpovědný za vytváření vysoce spolehlivých stavu zprostředkovatele stavu objektu Actor.

## <a name="configure-ssl-for-azure-service-fabric"></a>Konfigurace SSL pro Azure Service Fabric
Proces ověřování serveru [ověřuje](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) koncových bodů správy clusteru klientovi pro správu. Klient správy poté rozpozná, že je komunikaci se skutečným clusterem. Tento certifikát také poskytuje [SSL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) pro rozhraní API pro správu protokolu HTTPS a pro Service Fabric Explorer přes protokol HTTPS.
Pro svůj cluster musíte získat název vlastní domény. Pokud budete požadovat certifikát od certifikační autority, název subjektu certifikátu musí odpovídat názvu vlastní domény, který používáte pro váš cluster.

Konfigurace SSL pro aplikaci, musíte nejprve získat certifikát SSL podepsaný Certifikační autoritou. Certifikační Autorita je důvěryhodná třetí strana, která vydává certifikáty SSL z bezpečnostních důvodů. Pokud ještě nemáte certifikát SSL, budete muset získat jeden ze společnosti, která se prodává certifikáty SSL.

Certifikát musí splňovat následující požadavky na certifikáty protokolu SSL v Azure:
-   Certifikát musí obsahovat privátní klíč.

-   Certifikát musí být vytvořen pro výměnu klíčů a nelze exportovat do souboru personal information exchange (.pfx).

-   Název subjektu certifikátu musí odpovídat názvu domény, který se používá pro přístup k vaší cloudové služby.

    - Získejte název vlastní domény pro použití pro přístup k vaší cloudové služby.
    - Vyžádejte certifikát od certifikační Autority s názvem subjektu, který odpovídá názvu vlastní domény vaší služby. Například, pokud je název vaší vlastní domény __contoso__**.com**, certifikát z certifikační Autority by měl mít název subjektu **. contoso.com** nebo __www__ **. contoso.com**.

    >[!NOTE]
    >Nelze získat od certifikační Autority pro certifikát SSL __cloudapp__**.net** domény.

-   Certifikát musí používat minimálně 2 048 bitů šifrování.

Protokol HTTP je nezabezpečené a můžou se před útoky odposloucháváním na. Data přenášená přes protokol HTTP se odešle jako prostý text z webového prohlížeče na webový server nebo mezi ostatní koncové body. Útočník může zachytit a zobrazit citlivá data, která je odeslána přes protokol HTTP, jako je například údajů z platební karty a přihlašovacích údajů účtu. Když nebo odeslání dat odesílají prostřednictvím prohlížeče prostřednictvím protokolu HTTPS, SSL zajišťuje, že citlivých informací zašifrována takže před narušením zabezpečení.

Další informace o používání certifikátů SSL najdete v tématu [konfigurace SSL pro aplikace Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-configure-ssl-certificate).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Izolace sítě a zabezpečení pomocí Azure Service Fabric
Nastavení zabezpečeného clusteru s 3 nodetype pomocí [šablony Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) jako ukázku. Řízení příchozího a odchozího provozu sítě pomocí šablony a skupiny zabezpečení sítě.

Šablona má skupinu NSG pro každou škálovací sady virtuálních počítačů a slouží k řízení provozu do sady. Pravidla jsou nakonfigurována ve výchozím nastavení k povolení veškerého provozu nezbytné pro systémové služby a porty aplikace zadané v šabloně. Tato pravidla zkontrolovat a proveďte požadované změny podle vašich potřeb, včetně přidání nových pravidel pro vaše aplikace.

Další informace najdete v tématu [běžné síťové scénáře pro Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

## <a name="set-up-azure-key-vault-for-security"></a>Nastavení služby Azure Key Vault pro zabezpečení
Service Fabric používá certifikáty k ověřování a šifrování pro zabezpečení clusteru a jeho aplikací.

Service Fabric pomocí certifikátů X.509 zabezpečení clusteru a poskytuje funkce pro zabezpečení aplikací. Použití Azure Key Vault a [Správa certifikátů](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure) pro clustery Service Fabric v Azure. Poskytovatel prostředků Azure, která vytváří clustery si vyžádá certifikáty ze služby key vault. Zprostředkovatel pak nainstaluje certifikáty na virtuálních počítačích, při nasazení clusteru v Azure.

Certifikát relace existuje mezi [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), cluster Service Fabric a poskytovatele prostředků, která používá certifikáty. Při vytvoření clusteru, informace o vztahu certifikát uložený ve službě key vault.

Existují dva základní kroky k nastavení služby key vault:
1. Vytvořte skupinu prostředků pro váš trezor klíčů.

    Doporučujeme umístit služby key vault do své vlastní skupině prostředků. Tato akce pomáhá zabránit ztrátě klíče a tajné klíče, pokud jsou odebrány jiných skupinách prostředků, jako je například úložiště, výpočetní prostředky nebo skupiny, která obsahuje váš cluster. Skupina prostředků obsahující trezor klíčů musí být ve stejné oblasti jako cluster, který je používán.

2. Vytvoření služby key vault do nové skupiny prostředků.

    Trezor klíčů musí být povolen pro nasazení. Poskytovateli prostředků compute můžete získat certifikáty z trezoru a nainstalujte si ji na instance virtuálních počítačů.

Další informace o tom, jak nastavit trezor klíčů najdete v tématu [Začínáme s Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).

## <a name="assign-users-to-roles"></a>Přiřazení uživatelů k rolím
Po vytvoření aplikace, které chcete cluster představují, přiřadit uživatele k rolím, které jsou podporovány pomocí Service Fabric: jen pro čtení a správce. Tyto role můžete přiřadit pomocí webu Azure portal.

>[!NOTE]
> Další informace o používání rolí ve službě Service Fabric najdete v tématu [řízení přístupu na základě rolí pro Service Fabric klienty](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

Azure Service Fabric podporuje dva typy ovládacích prvků přístupu pro klienty, kteří jsou připojení k [clusteru Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm): správce a uživatele. Správce clusteru můžete použít řízení přístupu k omezení přístupu k určité operace clusteru pro různé skupiny uživatelů. Řízení přístupu umožňuje clusteru bezpečnější.

## <a name="next-steps"></a>Další postup

- [Kontrolní seznam zabezpečení Service Fabric](azure-service-fabric-security-checklist.md)
- Nastavení Service Fabric [vývojové prostředí](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started).
- Další informace o [možnosti podpory pro Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).

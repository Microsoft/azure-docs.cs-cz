---
title: Azure Service Fabric osvědčené postupy zabezpečení | Microsoft Docs
description: Tento článek obsahuje sadu osvědčené postupy pro zabezpečení Azure Service Fabric.
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
ms.date: 11/01/2017
ms.author: tomsh
ms.openlocfilehash: b908589903d243b2d284e2a23b6111785229c16f
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894125"
---
# <a name="azure-service-fabric-security-best-practices"></a>Azure Service Fabric osvědčené postupy zabezpečení
Nasazení aplikace v Azure je rychlý, snadný a nákladově efektivní. Před nasazením cloudové aplikace do produkčního prostředí, projděte si seznam důležité a doporučené osvědčené postupy pro implementaci zabezpečeného clustery ve vaší aplikaci.

Azure Service Fabric je platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb. Service Fabric se taky zaměřuje na problematiku vývoje a správy cloudových aplikací. Vývojáři a správci se můžou vyhnout problémům se složitou infrastrukturou a místo toho se soustředit na implementaci zásadních a náročných úloh, které jsou škálovatelné, spolehlivé a spravovatelné. 

Pro každý osvědčený postup objasníme:

-   Co je doporučený postup.
-   Proč byste měli implementovat osvědčený postup.
-   Co může dojít, pokud neimplementují osvědčený postup.
-   Jak získat informace můžete implementovat osvědčený postup.

Doporučujeme následující zabezpečení Azure Service Fabric osvědčené postupy:

-   Pomocí šablony Azure Resource Manager a modul Service Fabric prostředí PowerShell k vytvoření zabezpečeného clusterů.
-   Pomocí certifikátů X.509.
-   Konfigurovat zásady zabezpečení.
-   Implementujte konfiguraci zabezpečení Reliable Actors.
-   Konfigurace protokolu SSL pro Azure Service Fabric.
-   Izolace sítě a zabezpečení pomocí Azure Service Fabric.
-   Azure Key Vault konfigurujte pro zabezpečení.
-   Přiřadíte uživatele k rolím.


## <a name="best-practices-for-securing-your-clusters"></a>Osvědčené postupy pro zabezpečení vašeho clusterů

Vždy použijte cluster s podporou zabezpečení:
-   Zabezpečení clusteru implementovat pomocí certifikátů.
-   Zadejte klientský přístup (správce a jen pro čtení) pomocí služby Azure Active Directory (Azure AD).

Použití automatického nasazení:
-   Používejte skripty k vytvoření, nasazení a mění těchto tajných klíčů.
-   Ukládání těchto tajných klíčů v Azure Key Vault a používat Azure AD pro všechny ostatní klientský přístup.
-   Vyžadovat ověřování pro lidské přístup do těchto tajných klíčů.

Kromě toho zvažte následující možnosti konfigurace:
-   Pomocí Azure skupin zabezpečení sítě (Nsg) vytvořte hraniční sítě (známé také jako demilitarizovaná zón, zóny DMZ a monitorovaná podsítě).
-   Přístup k clusteru virtuálních počítačů (VM) nebo správě tohoto clusteru pomocí jump servery s připojení ke vzdálené ploše.

Chcete-li zabránit neoprávněným uživatelům v připojení, zejména pokud cluster běží v produkčním prostředí musí být zabezpečená clusterů. Přestože je možné vytvořit zabezpečená clusteru, anonymní uživatelé se mohou připojit ke svému clusteru Pokud clusteru zpřístupní koncové body správy do veřejného Internetu.

Existují tři [scénáře](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) pro implementaci zabezpečení clusteru pomocí různých technologií:

-   Zabezpečení – uzly: Tento scénář zabezpečuje komunikaci mezi počítači v clusteru a virtuální počítače. Tato forma zabezpečení zajišťuje pouze počítače, které jsou autorizované pro připojení ke clusteru může být hostitelem aplikace a služby v clusteru.
V tomto scénáři, clustery, které běží na Azure nebo samostatné clustery, které běží v systému Windows, můžete použít buď [certifikátu zabezpečení](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) nebo [zabezpečení systému Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security) pro počítače, Windows Server.
-   Uzel Klient zabezpečení: Tento scénář zabezpečuje komunikaci mezi klientem Service Fabric a jednotlivé uzly v clusteru.
-   Na základě rolí řízení přístupu (RBAC): Tento scénář používá samostatné identity (certifikáty, Azure AD, a tak dále) pro každou roli klienta správce a uživatele, který přistupuje k clusteru. Při vytváření clusteru zadáte identity role.

>[!NOTE]
>**Doporučení zabezpečení pro Azure clustery:** zabezpečení pomocí služby Azure AD k ověřování klientů a certifikáty pro zabezpečení mezi uzly.

Konfigurace clusteru se systémem Windows samostatné najdete v tématu [nakonfigurovat nastavení pro cluster s podporou Windows samostatné](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest).

Pomocí šablony Azure Resource Manager a modul Service Fabric prostředí PowerShell k vytvoření clusteru s podporou zabezpečení.
Podrobné pokyny k vytvoření zabezpečení clusteru Service Fabric pomocí šablon Azure Resource Manageru najdete v tématu [vytvořte cluster Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Pomocí šablony Azure Resource Manageru:
-   Přizpůsobte clusteru pomocí šablony pro konfiguraci spravovaného úložiště pro virtuální počítač virtuální pevné disky (VHD).
-   Jednotka změny do skupiny prostředků pomocí šablony pro správu snadno konfigurace a auditování.

Konfiguraci clusteru považovat za kódu:
-   Být důkladné při kontrole konfigurace vašeho nasazení.
-   Nepoužívejte implicitní příkazy přímo upravit vaše prostředky.

Mnoho aspektů [životního cyklu aplikace Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle) lze automatizovat. [Modul Service Fabric prostředí PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications#upload-the-application-package) automatizuje běžné úlohy pro nasazení, upgrade, odebrání a testování aplikací pro Azure Service Fabric. Spravovaná rozhraní API a rozhraní API HTTP pro správu aplikací jsou také k dispozici.

## <a name="use-x509-certificates"></a>Použijte certifikáty X.509
Vždy Zabezpečte vaše clustery pomocí certifikátů X.509 nebo zabezpečení systému Windows. Zabezpečení je nakonfigurovaná pouze při vytváření clusteru. Není možné po vytvoření clusteru zapnout zabezpečení.

K určení [clusteru certifikát](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security), nastavte hodnotu **ClusterCredentialType** vlastnost X509. Chcete-li zadat certifikát serveru pro připojení mimo, nastavte **ServerCredentialType** vlastnost X509.

Kromě toho postupujte podle těchto postupů:
-   Vytvořte certifikáty pro produkční clustery pomocí správně nakonfigurovaných certifikát služby Windows Server. Certifikáty můžete získat i z schválené certifikační autoritu (CA).
-   Nikdy nepoužívejte dočasného nebo pokud certifikát byl vytvořen pomocí nástroje MakeCert.exe nebo podobné nástroj testovací certifikát pro produkčních clusterů.
-   Použijte certifikát podepsaný svým držitelem pro testovací clustery, ale ne pro produkčních clusterů.

Pokud cluster nezabezpečená, každý, kdo anonymního připojení ke clusteru a provádět operace správy. Z tohoto důvodu vždy produkčních clusterů Zabezpečte pomocí certifikátů X.509 nebo zabezpečení systému Windows.

Další informace o používání certifikátů X.509 najdete v tématu [přidat nebo odebrat certifikáty pro cluster Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure).

## <a name="configure-security-policies"></a>Konfigurovat zásady zabezpečení
Service Fabric také zabezpečuje prostředky, které jsou používány aplikací. Prostředky, například soubory, adresářů, a certifikáty jsou uložené v části uživatelské účty, když je aplikace nasazená. Díky této funkci spuštěné aplikace bezpečnější od sebe navzájem i ve sdíleném hostované prostředí.

-   Použít skupiny domény služby Active Directory nebo uživatele: spouštění služby v části přihlašovací údaje pro účet uživatele nebo skupiny služby Active Directory. Nezapomeňte použít služby Active Directory místně v rámci domény a není Azure Active Directory. Přístup k jiným prostředkům v doméně, která byla udělena oprávnění pomocí účtem uživatele domény nebo skupiny. Například prostředky, jako jsou sdílené složky.

-   Přiřadit zásady zabezpečení přístupu pro koncové body HTTP a HTTPS: Zadejte **SecurityAccessPolicy** vlastnost pro použití **RunAs** zásady služby při service manifest deklaruje koncový bod prostředků s protokolem HTTP. Porty přidělené do koncových bodů protokolu HTTP jsou správně přístup řídí seznamy pro uživatelský účet Spustit jako, který se spouští služba. Pokud zásady není nastavena, ovladač http.sys nemá přístup ke službě a selhání pomocí volání můžete získat z klienta.

Další informace o použití zásad zabezpečení v clusteru Service Fabric, najdete v tématu [konfigurovat zásady zabezpečení pro vaši aplikaci](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-runas-security).

## <a name="implement-the-reliable-actors-security-configuration"></a>Implementovat konfiguraci zabezpečení Reliable Actors
Služba Fabric Reliable Actors je implementace tohoto vzoru návrhu objektu actor. Stejně jako u jakékoli vzoru návrhu softwaru rozhodnutí ohledně pomocí specifického vzoru vychází jestli potíže se softwarem vyhovuje vzoru.

Vzor návrhu objektu actor se obvykle používá ke modelu řešení pro následující problémy se softwarem nebo scénáře zabezpečení:
-   Váš prostor problém zahrnuje velký počet (tisíc nebo více) malé, nezávislé a izolované jednotek stavu a logiku.
-   Pracujete s jedním podprocesem objekty, které nevyžadují významné interakce z externí součásti, včetně dotaz na stav mezi sadu aktéři.
-   Vaše instance objektu actor neblokovat volající s nepředvídatelným zpoždění vydáním vstupně-výstupních operací.

V Service Fabric kteří se implementují ve rozhraní Reliable Actors. Toto rozhraní je na základě vzoru objektu actor a postavený na [spolehlivé služby Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction). Každá služba spolehlivé objektu actor, která můžete zapsat je oddílů stavová služba spolehlivé.

Každý objekt actor je definován jako instanci objektu actor typu identické způsobem, jakým objekt .NET je instance typu .NET. Například **typ objektu actor** , implementuje funkce kalkulačky můžou mít mnoho aktéři daného typu, které jsou rozmístěny v různých uzlech v clusteru. Všechny distribuované aktéři je jednoznačně charakterizovaná identifikátor objektu actor.

[Konfigurace zabezpečení Replikátor](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration) se používají k zabezpečení komunikačního kanálu, který se používá během replikace. Tato konfigurace zabraňuje zobrazuje provoz replikace druhé strany služby a zajišťuje, že vysoce dostupných dat zabezpečené. Ve výchozím nastavení zabraňuje konfigurační oddíl prázdný zabezpečení zabezpečení replikace.
Konfigurace Replikátor nakonfigurovat Replikátor, která zodpovídá za vytvoření zprostředkovatele stavu objektu Actor stavu vysoce spolehlivé.

## <a name="configure-ssl-for-azure-service-fabric"></a>Konfigurace protokolu SSL pro Azure Service Fabric
Proces ověřování serveru [ověřuje](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) clusteru koncových bodů správy klient pro správu. Klient pro správu poté rozpozná, že je rozhovoru s skutečné clusteru. Tento certifikát také poskytuje [SSL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) pro rozhraní API pro správu protokolu HTTPS a pro Service Fabric Explorer přes protokol HTTPS.
Pro svůj cluster musíte získat název vlastní domény. Pokud budete požadovat certifikát od certifikační autority, název subjektu certifikátu musí odpovídat názvu vlastní domény, který používáte pro váš cluster.

Konfigurace protokolu SSL pro aplikaci, musíte nejdřív získat certifikát SSL, který podepsala certifikační Autority. Tato certifikační Autorita je důvěryhodná třetí strany, která vydává certifikáty SSL z bezpečnostních důvodů. Pokud ještě nemáte certifikát SSL, budete muset získat jeden ze společnosti, která prodává certifikáty SSL.

Certifikát musí splňovat následující požadavky na certifikáty SSL v Azure:
-   Certifikát musí obsahovat privátní klíč.

-   Certifikát musí být vytvořený pro výměnu klíčů a nelze exportovat do souboru osobní údaje exchange (.pfx).

-   Název subjektu certifikátu musí odpovídat názvu domény, který se používá k přístupu ke službě cloud.

    - Získejte název vlastní domény pro použití pro přístup k cloudové služby.
    - Žádost o certifikát od certifikační Autority s názvem subjektu, který odpovídá názvu vlastní domény vaší služby. Například, pokud je váš vlastní název domény __contoso__**.com**, certifikát z certifikační Autority by měl mít název subjektu **. contoso.com** nebo __www__ **. contoso.com**.

    >[!NOTE]
    >Nelze získat od certifikační Autority pro certifikát SSL __cloudapp__**.net** domény.
    
-   Certifikát musí používat minimálně 2 048 bitů šifrování.

Protokol HTTP je nezabezpečená a vystavené útokům odposlechu. Data přenášená přes protokol HTTP je odeslán jako prostý text z webového prohlížeče na webový server nebo mezi ostatní koncové body. Útočník může zachytávat a zobrazit citlivá data, která je odeslána prostřednictvím protokolu HTTP, jako je například údajů z platební karty a účet přihlášení. Pokud data jsou odeslána nebo odeslány prostřednictvím prohlížeče přes HTTPS, SSL zajišťuje, že citlivé informace šifrované a před narušením zabezpečení.

Další informace o používání certifikátů SSL najdete v tématu [konfigurace protokolu SSL pro Azure aplikace](https://docs.microsoft.com/azure/cloud-services/cloud-services-configure-ssl-certificate).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Izolace sítě a zabezpečení pomocí Azure Service Fabric
Nastavení zabezpečení clusteru 3 nodetype pomocí [šablony Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) jako ukázka. Řídit příchozí a odchozí síťový provoz pomocí šablony a skupiny zabezpečení sítě.

Šablona má skupina NSG pro všechny škálovací sady virtuálních počítačů a slouží k řízení provozu do a z sadu. Pravidla jsou nakonfigurována ve výchozím nastavení povolují veškerý provoz potřebné pro systémové služby a porty aplikace zadané v šabloně. Tato pravidla zkontrolovat a proveďte požadované změny podle vašich potřeb, včetně přidávání nové pravidel pro vaše aplikace.

Další informace najdete v tématu [běžné síťové scénáře pro Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

## <a name="set-up-azure-key-vault-for-security"></a>Nastavení Azure Key Vault pro zabezpečení
Service Fabric používá certifikáty k ověřování a šifrování pro zabezpečení cluster a jeho aplikace.

Service Fabric používá certifikáty X.509 pro zabezpečené cluster a poskytují funkce zabezpečení aplikací. Použití Azure Key Vault [spravovat certifikáty](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure) pro clusterů Service Fabric v Azure. Poskytovatel prostředků Azure, která vytvoří clustery vrátí certifikáty z trezoru klíčů. Zprostředkovatel pak nainstaluje certifikátů na virtuálních počítačích, při nasazování clusteru v Azure.

Certifikát vztah mezi [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), cluster Service Fabric a poskytovatele prostředků, která používá certifikáty. Když je vytvořen cluster, informace o certifikátu relace jsou uloženy v trezoru klíčů.

Existují dva základní kroky při nastavení trezoru klíčů:
1. Vytvořte skupinu prostředků speciálně pro váš trezor klíčů.

    Doporučujeme umístit trezoru klíčů do vlastní skupiny prostředků. Tato akce pomáhá zabránit ztrátě klíče a tajné klíče, pokud jsou odebrány další skupiny zdrojů, jako je například úložiště, výpočetního nebo skupině, která obsahuje daný cluster. Skupinu prostředků, která obsahuje váš trezor klíčů musí být ve stejné oblasti jako cluster, který se používá.

2. Vytvoření trezoru klíčů do nové skupiny prostředků.

    Trezor klíčů musí být povolen pro nasazení. Zprostředkovatel výpočetních prostředků můžete získat certifikáty z trezoru a instalovat je instance virtuálních počítačů.

Další informace o tom, jak nastavit trezor klíčů najdete v tématu [Začínáme s Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).

## <a name="assign-users-to-roles"></a>Přiřadit uživatele k rolím
Po vytvoření aplikace, které chcete cluster představují, přiřadit uživatele k rolím, které jsou podporovány pomocí Service Fabric: jen pro čtení a správce. Tyto role můžete přiřadit pomocí portálu Azure.

>[!NOTE]
> Další informace o používání rolí v Service Fabric najdete v tématu [řízení přístupu na základě rolí pro Service Fabric klienty](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

Azure Service Fabric podporuje dva typy řízení přístupu pro klienty, které jsou připojené k [cluster Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm): správce a uživatele. Správce clusteru můžete použít řízení přístupu omezit přístup k určité operace clusteru pro různé skupiny uživatelů. Řízení přístupu umožňuje clusteru bezpečnější.

## <a name="next-steps"></a>Další postup
- Nastavit Service Fabric [vývojového prostředí](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started).
- Další informace o [možnosti podpory Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).

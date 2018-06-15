---
title: Přehled zabezpečení služby Azure service fabric | Microsoft Docs
description: Tento článek obsahuje přehled o zabezpečení Azure Service Fabric.
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
ms.openlocfilehash: 19e4e55c4bd63e5d7a9ef6ea3b0bf6ae63f929d5
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895659"
---
# <a name="azure-service-fabric-security-overview"></a>Přehled zabezpečení služby Azure Service Fabric
[Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) je platforma distribuovaných systémů, která usnadňuje balíčku, nasazovat a spravovat škálovatelného a spolehlivého mikroslužeb. Service Fabric řeší významné problémy vývoj a správu aplikací v cloudu. Vývojáři a správci se můžou vyhnout problémům se složitou infrastrukturou a místo toho se soustředit na implementaci zásadních a náročných úloh, které jsou škálovatelné, spolehlivé a spravovatelné.

V tomto článku Přehled zabezpečení služby Azure Service Fabric se zaměřuje na tyto oblasti:

-   Zabezpečení clusteru
-   Principy sledování a Diagnostika
-   Vytváření bezpečnější prostředí pomocí certifikátů
-   Pomocí řízení přístupu na základě Role (RBAC)
-   Zabezpečení clusterů pomocí zabezpečení systému Windows
-   Konfigurace zabezpečení aplikace v Service Fabric
-   Zabezpečení komunikace pro služby v Azure Service Fabric 

## <a name="secure-your-cluster"></a>Zabezpečení clusteru
Azure Service Fabric služeb orchestruje mezi clustery počítačů. Clustery musí být zabezpečená neoprávněným uživatelům zabránit v připojení, a to zejména v případě, že běží úlohy v produkčním prostředí. Přestože je možné vytvořit cluster zabezpečená, může to umožní anonymní uživatelé připojení k tomuto (Pokud vystavuje koncové body správy do veřejného Internetu).

Tato část obsahuje přehled scénáře zabezpečení pro clustery, které běží buď samostatně nebo v Azure. Také popisuje různé technologie, které se používají k implementaci těchto scénářů. Scénáře zabezpečení clusteru jsou:

-   Zabezpečení – uzly
-   Uzel Klient zabezpečení

### <a name="node-to-node-security"></a>Zabezpečení – uzly
Zabezpečení – uzly zabezpečuje komunikaci mezi virtuální počítače nebo počítače v clusteru. Zabezpečení – uzly můžete pouze počítače, které jsou autorizované pro připojení ke clusteru účastnit hostování aplikací a služeb v clusteru.

Clustery, které jsou spuštěny v Azure nebo samostatné clustery, které běží na systému Windows můžete použít buď [certifikátu zabezpečení](https://msdn.microsoft.com/library/ff649801.aspx) nebo [zabezpečení systému Windows](https://msdn.microsoft.com/library/ff649396.aspx) pro počítače, Windows Server.

**Porozumět zabezpečení certifikátu – uzly**

Service Fabric používá certifikáty X.509 serveru, zda jste zadali při vytváření clusteru s podporou. Rychlý přehled toho, co jsou tyto certifikáty a jak můžete získat nebo je vytvořit, naleznete v části [práce s certifikáty](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates).

Certifikát zabezpečení se konfigurují při vytváření clusteru, a to buď prostřednictvím portálu Azure, šablon Azure Resource Manageru nebo samostatné JSON šablony. Můžete určit primární certifikát a volitelné sekundární certifikát, který se používá pro certifikát efekty přechodu. Primární a sekundární certifikáty zadáte by měla být jiná než správce klienta a jen pro čtení klientské certifikáty, které zadáte pro [klientský uzel zabezpečení](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).

### <a name="client-to-node-security"></a>Uzel Klient zabezpečení
Nakonfigurujete klientský uzel zabezpečení pomocí identity klienta. K vybudování důvěry mezi klientem a cluster, je nutné nakonfigurovat clusteru potřebujete vědět, který klient identit, které můžete důvěřovat. Tento krok můžete provést dvěma způsoby:

-   Zadejte skupinu uživatele domény, které se můžou připojit. 
-   Zadejte uživatele uzlu domény, které se můžou připojit.

Service Fabric podporuje dva typy ovládacích prvků různý přístup pro klienty, kteří jsou připojené ke clusteru Service Fabric:

-   Správce
-   Uživatel

Pomocí řízení přístupu clusteru správci můžou omezit přístup k určitým typům operace clusteru. Díky tomu clusteru bezpečnější.

 Správci mají plný přístup k funkcím správy (včetně možnosti pro čtení i zápis). Uživatelé, ve výchozím nastavení, mají pouze pro čtení přístup k možnosti správy (například možnosti dotazu) a možnost řešení aplikace a služby.

**Porozumět zabezpečení certifikát klienta uzlu**

Při vytváření clusteru s podporou buď prostřednictvím portálu Azure, šablony Resource Manageru nebo šablonu JSON samostatné nakonfigurujete klientský uzel certifikát zabezpečení. Budete muset zadat certifikát klienta správce nebo klientský certifikát uživatele. 

Správce klientů a uživatelů klientských certifikátů, které zadáte, musí být jiné než primární a sekundární certifikáty, které zadáte pro zabezpečení mezi uzly.

Klienti, kteří se připojte ke clusteru pomocí certifikátu, správce mají plný přístup k možnosti správy. Klienti, kteří připojte se ke clusteru pomocí certifikátu klienta jen pro čtení uživatele obsahovat pouze pro čtení přístup k možnosti správy. Jinými slovy tyto certifikáty se používají pro RBAC.

Zjistěte, jak nakonfigurovat certifikát zabezpečení v clusteru, najdete v tématu [nastavení clusteru pomocí šablony Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

**Porozumět zabezpečení služby Azure Active Directory uzlu klientů v Azure**

Clustery, které jsou spuštěné v Azure můžete také zabezpečený přístup k koncové body správy pomocí služby Azure Active Directory (Azure AD). Informace o vytvoří artefakty potřebné Azure Active Directory, naplnit při vytváření clusteru a jak se připojit k tyto clustery najdete v tématu [nastavení clusteru pomocí šablony Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Azure AD umožňuje spravovat přístup uživatelů k aplikacím organizace (označované jako klienty). Existují aplikace s webové přihlášení uživatelského rozhraní a aplikací prostředí nativního klienta.

Cluster Service Fabric nabízí několik vstupní body k jeho funkce správy, včetně webových Service Fabric Explorer a Visual Studio. V důsledku toho můžete vytvořit dvě aplikace Azure AD k řízení přístupu ke clusteru: jednu webovou aplikaci a jeden nativní aplikaci.

Pro Azure clusterů doporučujeme použít k ověřování klientů a certifikáty pro zabezpečení – uzly zabezpečení Azure AD.

Pro samostatné clustery systému Windows Server s Windows Server 2012 R2 a služby Active Directory doporučujeme použít s účty spravované skupiny zabezpečení systému Windows.  Jinak použijte zabezpečení systému Windows s účty systému Windows.

## <a name="understand-monitoring-and-diagnostics-in-azure-service-fabric"></a>Porozumění sledování a Diagnostika v Azure Service Fabric
[Monitorovací a diagnostické](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) jsou důležité pro vývoj, testování a nasazení aplikace a služby v jakémkoli prostředí. Service Fabric řešení je nejvhodnější při implementaci monitorování a Diagnostika zajistit, že aplikace a služby fungují podle očekávání v místním vývojovém prostředí, nebo v produkčním prostředí.

Z hlediska zabezpečení hlavní cíle monitorování a Diagnostika jsou:

-   Najít a diagnostikovat problémy s hardwarem a infrastruktury, které může být způsobeno událostí zabezpečení.
-   Rozpoznat, softwaru a aplikace problémy, které by mohly být slouží jako ukazatel ohrožení (IoC).
-   Pochopení spotřeby prostředků, aby se zabránilo nechtěnému odepření služby.

Celkové pracovní postup monitorování a Diagnostika zahrnuje tři kroky:

-   **Generování událostí:** generování událostí zahrnuje událostí (protokoly, trasování, vlastních událostí) v infrastruktuře (cluster) a úroveň aplikace nebo služby. Další informace o [událostí na úrovni infrastruktury](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra) a [událostí na úrovni aplikace](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) pochopit, co je k dispozici a jak přidat další instrumentace.

-   **Agregace událostí:** události generované třeba shromažďovat a agregovat předtím, než je možné zobrazit. Obvykle doporučujeme používat [Azure Diagnostics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) (podobně jako kolekce založené na agentovi protokolu) nebo [EventFlow](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow) (v procesu protokolu kolekce).

-   **Analýza:** události musí být vizualizovaných a v některých formátu, aby za účelem analýzy a zobrazení. Existuje několik platforem pro analýzu a vizualizace dat monitorování a diagnostiky. Jsou dva, které doporučujeme [analýzy protokolů](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms) a [Azure Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights) z důvodu jejich dobrý integrace s Service Fabric.

Můžete také použít [Azure monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) monitorovat mnoho prostředků Azure, na kterých je vytvořen cluster Service Fabric.

Sledovací zařízení je samostatný služba, která může sledovat stav, zatížení v rámci služby a zprávy o stavu pro všechno, co je v hierarchii stavu modelu. Použití sledovací zařízení může pomoci zabránit chybám, které by se na základě zobrazení jedné služby. 

Watchdogs jsou také vhodné místo pro hostitele kód, který provede nápravné akce bez zásahu uživatele (například čištění souborů protokolů v úložiště v určitých časových intervalech). Můžete najít ukázkové implementaci služby sledovací zařízení na [Azure Service Fabric sledovací zařízení ukázka](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/).

## <a name="understand-how-to-secure-communication-by-using-certificates"></a>Pochopit, jak zabezpečit komunikaci pomocí certifikátů
Certifikáty pomáhá vám zabezpečit komunikaci mezi různými uzly clusteru Windows samostatné. Pomocí certifikátů X.509 lze také ověřovat klienti, kteří se připojují k tomuto clusteru. Zajistíte tak, že mají jenom autorizovaným uživatelům přístup ke clusteru. Doporučujeme, abyste povolili certifikát v clusteru při jeho vytvoření.

### <a name="x509-certificates-and-service-fabric"></a>X.509 – certifikáty a Service Fabric
Digitální certifikáty X.509 běžně se používají k ověřování klientů a serverů. Používají se také k šifrování a digitálnímu podepisování zpráv.

Následující tabulka uvádí certifikáty, které musíte na vaše nastavení clusteru:

|Informace o nastavení certifikátů |Popis|
|-------------------------------|-----------|
|ClusterCertificate|    Tento certifikát je vyžadován k zabezpečení komunikace mezi uzly v clusteru. Můžete použít dvě různé certifikáty: certifikát primární a sekundární pro upgrade.|
|ServerCertificate| Tento certifikát je pro klienta zobrazí při pokusu o připojení k tomuto clusteru. Můžete použít dva certifikáty jiný server: certifikát primární a sekundární pro upgrade.|
|ClientCertificateThumbprints|  To je sada certifikáty k instalaci na ověření klientů.|
|ClientCertificateCommonNames|  Toto je běžný název první klientský certifikát pro CertificateCommonName. CertificateIssuerThumbprint je kryptografický otisk tohoto certifikátu vystavitele.|
|ReverseProxyCertificate|   Toto je volitelné certifikát, který lze zabezpečit vaše [reverse proxy](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy).|

Další informace o zabezpečení certifikátů najdete v tématu [zabezpečení samostatné clusteru v systému Windows pomocí certifikátů X.509](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security).

## <a name="understand-role-based-access-control"></a>Pochopení řízení přístupu na základě rolí
Řízení přístupu umožňuje omezit přístup k určité operace clusteru pro různé skupiny uživatelů, díky čemuž bezpečnější clusteru pomocí Správce clusteru. Podporuje dva typy různý přístup řízení pro klienty, kteří se připojují ke clusteru: 

- Role správce
- role uživatele

Správci mají plný přístup k funkcím správy (včetně možnosti pro čtení i zápis). Uživatelé, ve výchozím nastavení, mají pouze pro čtení přístup k možnosti správy (například možnosti dotazu) a možnost řešení aplikace a služby.

Zadáte správce a uživatelských rolí klienta v době vytváření clusteru poskytnutím oddělené identity (včetně certifikátů) pro každý. Další informace o výchozí nastavení řízení přístupu a jak změnit výchozí nastavení najdete v tématu [řízení přístupu na základě rolí pro Service Fabric klienty](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

## <a name="secure-standalone-cluster-by-using-windows-security"></a>Zabezpečení clusteru samostatné pomocí zabezpečení systému Windows
K zabránění neoprávněného přístupu k cluster Service Fabric, je nutné zabezpečit clusteru. Zabezpečení je zvlášť důležité při spuštění úlohy v produkčním prostředí clusteru. Popisuje konfiguraci zabezpečení – uzly a uzlu klientů pomocí zabezpečení systému Windows v souboru ClusterConfig.JSON souboru.

**Konfigurace zabezpečení systému Windows pomocí gMSA**

Pokud Service Fabric musí běžet pod gMSA, můžete nakonfigurovat zabezpečení – uzly nastavením [ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security). Pokud chcete vytvořit vztahy důvěryhodnosti mezi uzly, se musí být upozorněni vzájemně.

Pomocí ClientIdentities nakonfigurujete klientský uzel zabezpečení. K vybudování důvěry mezi klientem a cluster, je nutné nakonfigurovat cluster rozpoznat které identity klienta, můžete vztah důvěryhodnosti.

**Konfigurace zabezpečení systému Windows pomocí skupinu počítačů**

Pokud chcete použít skupinu počítače v doméně služby Active Directory, nakonfigurujete nastavením ClusterIdentity zabezpečení mezi uzly. Další informace najdete v tématu [vytvořit skupinu počítače ve službě Active Directory](https://msdn.microsoft.com/library/aa545347).

Pomocí ClientIdentities nakonfigurujete klientský uzel zabezpečení. K vybudování důvěry mezi klientem a cluster, je nutné nakonfigurovat cluster rozpoznat identity klienta, které můžete důvěřovat clusteru. Můžete vytvořit vztah důvěryhodnosti v dvěma různými způsoby:

-   Zadejte skupinu uživatele domény, které se můžou připojit.
-   Zadejte uživatele uzlu domény, které se můžou připojit.

## <a name="configure-application-security-in-service-fabric"></a>Konfigurace zabezpečení aplikace v Service Fabric
### <a name="manage-secrets-in-service-fabric-applications"></a>Spravovat tajných klíčů v aplikace Service Fabric
Tato metoda pomáhá spravovat tajných klíčů v aplikace Service Fabric. Tajné klíče může být žádné citlivé informace, jako je například úložiště připojovací řetězce, hesla nebo jiné hodnoty, které by neměly být zpracovány v prostém textu.

Tento postup používá [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) ke správě klíčů a tajných klíčů. Použití tajných klíčů v aplikaci je vázané na cloudové platformy. To znamená, že může být aplikace nasazeny na clusteru, který je hostovat kdekoli. V tomto toku existují čtyři hlavní kroky:

-   Získejte certifikát dat šifrování.
-   Nainstalujte certifikát v clusteru.
-   Šifrování tajný hodnoty při nasazení aplikace pomocí certifikátu a vložit je do služby souborech Settings.xml konfigurační soubor.
-   Číst šifrované hodnoty mimo souborech Settings.xml dešifrováním je stejný certifikát šifrování.

>[!NOTE]
>Další informace o [Správa tajných klíčů v Service Fabric aplikace](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="configure-security-policies-for-your-application"></a>Konfigurace zásad zabezpečení pro aplikaci
Pomocí Azure Service Fabric zabezpečení, může pomoct zabezpečených aplikací, které jsou spuštěny v clusteru v rámci jiné uživatelské účty. Zabezpečení infrastruktury služby také pomáhá zabezpečit prostředky, které jsou používány aplikací v době nasazení podle uživatelských účtů – například soubory, adresářů a certifikáty. Díky spuštěné aplikace, i v prostředí sdílené hostované bezpečnější.

Zahrnuje následující kroky:

-   Konfigurace zásad pro vstupní bod služby Instalační program.
-   Spouštění příkazů prostředí PowerShell ze vstupního bodu instalační program.
-   Pomocí konzoly přesměrování pro místní ladění.
-   Konfigurace zásad pro balíčky služeb kódu.
-   Přiřazení zásadu zabezpečení přístupu pro koncové body HTTP a HTTPS.

## <a name="secure-communication-for-services-in-azure-service-fabric-security"></a>Zabezpečené komunikace pro služby v Azure Service Fabric zabezpečení
Zabezpečení je jedním z nejdůležitějších aspektů komunikace. Rozhraní spolehlivé služby poskytuje několik předem komunikace zásobníky a nástroje, které slouží k vylepšení zabezpečení.

-   [Pomoc se zabezpečením služby, pokud používáte vzdálenou komunikaci služby](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication)
-   [Pomoc se zabezpečením služby při použití zásobníku komunikace na základě WCF](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication#help-secure-a-service-when-youre-using-a-wcf-based-communication-stack)

## <a name="next-steps"></a>Další postup
- Koncepční informace o zabezpečení clusteru najdete v tématu [vytvořit cluster Service Fabric pomocí Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) a [portál Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal).
- Další informace o zabezpečení clusteru Service Fabric najdete v tématu [zabezpečení clusteru Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).

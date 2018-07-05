---
title: Přehled zabezpečení služby Azure Service Fabric | Dokumentace Microsoftu
description: Tento článek poskytuje přehled o zabezpečení Azure Service Fabric.
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
ms.openlocfilehash: 629b6fba9ced5fa2ccf22f473fe25c87d1cc4818
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37436806"
---
# <a name="azure-service-fabric-security-overview"></a>Přehled zabezpečení služby Azure Service Fabric
[Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) je platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb. Service Fabric řeší problematiku vývoje a správy cloudových aplikací. Vývojáři a správci můžou vyhnout komplexním problémům s infrastrukturou a soustředit na implementaci důležitějších a náročných úloh, které jsou škálovatelné a spolehlivé.

Tento článek představuje přehled aspektů zabezpečení pro nasazení aplikace Service Fabric.

## <a name="secure-your-cluster"></a>Zabezpečení clusteru
Azure Service Fabric orchestruje služeb napříč clusterem počítačů. Clustery musí být zabezpečená umožňují zabránit neoprávněným uživatelům v připojení k nim, zejména v případě, že spouštíte produkční úlohy. I když je možné vytvořit nezabezpečený cluster, může to umožnit anonymní uživatelé pro připojení ke clusteru (pokud ho zpřístupní koncových bodů správy do veřejného Internetu).

Pro clustery, které jsou spuštěny buď samostatně nebo v Azure jsou dva scénáře, které byste měli zvážit zabezpečení mezi uzly a uzel klienta zabezpečení. Různé technologie můžete použít k implementaci těchto scénářů.

### <a name="node-to-node-security"></a>Zabezpečení mezi uzly
Zabezpečení mezi uzly se vztahuje na komunikaci mezi virtuální počítače nebo počítače v clusteru. Zabezpečení mezi uzly můžou účastnit jenom počítače, které mají oprávnění k připojení ke clusteru hostování aplikací a služeb v clusteru.

Clustery, které běží na Azure nebo samostatné clustery, které běží na Windows můžete použít buď [certifikátu zabezpečení](https://msdn.microsoft.com/library/ff649801.aspx) nebo [zabezpečení Windows](https://msdn.microsoft.com/library/ff649396.aspx) pro počítače s Windows serverem.

#### <a name="node-to-node-certificate-security"></a>Zabezpečení mezi uzly certifikátu

Service Fabric používá certifikáty X.509 server zadat při vytváření clusteru. Rychlý přehled toho, co jsou tyto certifikáty a jak můžete získat nebo vytvořit, naleznete v tématu [práce s certifikáty](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates).

Můžete nakonfigurovat certifikát zabezpečení při vytváření clusteru pomocí webu Azure portal, šablon Azure Resource Manageru nebo samostatné šablony JSON. Můžete určit primární certifikát a volitelné sekundární certifikát, který se používá pro efekty certifikát přechodu. Primární a sekundární certifikát, které zadáte, musí být odlišný od správce klienta a jen pro čtení klientské certifikáty, které zadáte pro [uzel klienta zabezpečení](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).

### <a name="client-to-node-security"></a>Uzel klienta zabezpečení
Konfigurace zabezpečení uzel klienta s použitím identity klienta. K navázání vztahu důvěryhodnosti mezi klientem a clusterem, musí konfigurace clusteru vědět, který klient identity, kterému můžete důvěřovat.

Service Fabric podporuje dva typy ovládacích prvků přístupu pro klienty, kteří jsou připojené ke clusteru Service Fabric:

-   **Správce**: úplný přístup k funkcím správy, včetně možnosti pro čtení a zápisu.
-   **Uživatel**: pouze přístup pro čtení k funkce pro správu (například schopnosti příkazů jazyka) a schopnost řešení aplikací a služeb.

Pomocí řízení přístupu správce clusterů můžete omezit přístup k určitým typům operace clusteru. Díky tomu clusteru bezpečnější.

#### <a name="client-to-node-certificate-security"></a>Certifikát klienta mezi uzly zabezpečení

Uzel klienta certifikát zabezpečení konfigurujete při vytvoření clusteru pomocí webu Azure portal, šablon Resource Manageru nebo samostatné šablony JSON. Musíte zadat certifikát klienta pro správu a/nebo klientský certifikát uživatele. Ujistěte se, že tyto certifikáty se liší od primárního a sekundárního certifikátu, které zadáte pro zabezpečení mezi uzly.

Klienti, kteří se připojte ke clusteru pomocí certifikátu pro správu mají plný přístup k možnosti správy. Klienti, kteří se připojte ke clusteru pomocí certifikátu klienta uživatele jen pro čtení mají pouze pro čtení k možnosti správy. Jinými slovy tyto certifikáty slouží k řízení přístupu na základě rolí (RBAC).

Další informace o konfiguraci certifikátů zabezpečení v clusteru, najdete v článku [nastavení clusteru s použitím šablony Azure Resource Manageru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

#### <a name="client-to-node-azure-active-directory-security"></a>Uzel klienta zabezpečení Azure Active Directory

Clustery, které běží v Azure můžete také zabezpečený přístup ke koncovým bodům správy pomocí služby Azure Active Directory (Azure AD). Informace o tom, jak vytvořit artefakty potřebné služby Azure Active Directory, jak do nich přidat při vytváření clusteru a jak se připojit k tyto clustery najdete v tématu [nastavení clusteru s použitím šablony Azure Resource Manageru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Azure AD umožňuje organizacím (označuje se jako tenantů) ke správě přístupu uživatelů k aplikacím. Existují aplikace s webové přihlášení uživatelským rozhraním a aplikace s nativní klientské prostředí.

Cluster Service Fabric nabízí několik vstupních bodů do jeho funkce správy, včetně webové rozhraní Service Fabric Explorer a Visual Studio. Proto vytvoříte dvě aplikace Azure AD pro řízení přístupu ke clusteru: jeden webové aplikace a jedné nativní aplikace.

Pro clustery Azure doporučujeme použít zabezpečení Azure AD k ověřování klientů a certifikáty pro zabezpečení mezi uzly.

Samostatné clustery Windows serveru s Windows serverem 2012 R2 a služby Active Directory doporučujeme použít zabezpečení Windows s skupinových účtů spravované služby (Gmsa). S účty Windows, použijte zabezpečení Windows.

## <a name="understand-monitoring-and-diagnostics-in-service-fabric"></a>Porozumění sledování a diagnostiky v Service Fabric
[Monitorování a Diagnostika](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) jsou důležité pro vývoj, testování a nasazení aplikací a služeb v jakémkoli prostředí. Service Fabric řešení fungují lépe, když implementujete monitorování a diagnostiky k zajištění, že aplikace a služby fungují podle očekávání v místním vývojovém prostředí nebo v produkčním prostředí.

Z hlediska zabezpečení je hlavních cílů monitorování a diagnostiku:

-   Detekovat a diagnostikovat problémy hardwaru a infrastruktury, které mohou být způsobeny událostí zabezpečení.
-   Zjištění problémů s softwaru a aplikace, které může být indikátorem ohrožení zabezpečení.
-   Vysvětlení využití prostředků, aby se zabránilo nechtěnému útok DoS.

Pracovní postup monitorování a diagnostiky se skládá ze tří kroků:

1.  **Generování události**: generování událostí zahrnuje událostí (protokoly, trasování, vlastní události) na úrovni infrastruktury) (cluster) i na úrovni aplikace/služby. Další informace o [událostí na úrovni infrastruktury](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra) a [událostí na úrovni aplikace](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) pochopit, co je k dispozici a jak přidat další instrumentaci.

2.  **Agregace událostí**: vygenerovaných událostí potřeba shromažďovat a agregovat předtím, než je možné zobrazit. Většinou doporučujeme používat [Azure Diagnostics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) (podobně jako kolekce založené na agentovi protokolů) nebo [využitím EventFlow](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow) (v procesu shromažďování protokolů).

3.  **Analýza**: události musí být vizualizovaný a v některých formátu umožňující analýzy a zobrazení k dispozici. Existuje několik platforem pro analýzy a vizualizace dat monitorování a diagnostiku. Doporučujeme [Azure Log Analytics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms) a [Azure Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights) vzhledem k tomu, že se integrují s Service Fabric.

Můžete také použít [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) monitorování řadu prostředků Azure, na kterých je postavená clusteru Service Fabric.

Sledovacího zařízení je samostatná služba, která může sledovat stav a zatížení napříč službami a stavu sestavy pro všechno, co v hierarchii stavu modelu. Použití sledovacího zařízení může pomoci zabránit chybám, které nebudou zjištěny založené na zobrazení jedné služby. 

Watchdogs je také vhodné místo pro hostování kódu, který provede nápravné akce bez zásahu uživatele. Příkladem je čištění souborů protokolu v úložišti v určitých časových intervalech. Můžete najít ukázková implementace služby sledovacího zařízení na [ukázka Azure Service Fabric sledovacích](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/).

## <a name="secure-communication-by-using-certificates"></a>Zabezpečená komunikace pomocí certifikátů
Certifikáty umožňují zabezpečenou komunikaci mezi různými uzly samostatného clusteru Windows. Pomocí certifikátů X.509, můžete také ověřit klienty, kteří se připojují k tomuto clusteru. Tím se zajistí, že mají jenom oprávnění uživatelé přístup ke clusteru. Doporučujeme, abyste povolili certifikát clusteru při jeho vytvoření.

Digitální certifikáty X.509 se běžně používají k ověřování klientů a serverů. Také slouží k šifrování a digitálnímu podepisování zpráv.

V následující tabulce jsou uvedeny certifikáty, které je třeba na vašem nastavení clusteru:

|Informace o nastavení certifikátu. |Popis|
|-------------------------------|-----------|
|ClusterCertificate|    Tento certifikát je vyžadován pro zabezpečení komunikace mezi uzly v clusteru. Můžete použít dva certifikáty clusteru: certifikát primární a sekundární pro upgrade.|
|ServerCertificate| Tento certifikát se zobrazí na klienta při pokusu o připojení k tomuto clusteru. Můžete použít dva certifikáty serveru: certifikát primární a sekundární pro upgrade.|
|ClientCertificateThumbprints|  To je sada certifikáty k instalaci na ověření klienti.|
|ClientCertificateCommonNames|  Toto je běžný název první klientský certifikát pro CertificateCommonName. CertificateIssuerThumbprint je kryptografický otisk vystavitele certifikátu.|
|ReverseProxyCertificate|   Toto je volitelné certifikát, který můžete použít k zabezpečení vašich [reverzní proxy server](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy).|

Další informace o zabezpečení certifikáty najdete v tématu [zabezpečení samostatného clusteru ve Windows pomocí certifikátů X.509](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security).

## <a name="understand-role-based-access-control"></a>Principy řízení přístupu na základě rolí
Zadáte správce a uživatelských rolí klienta v době vytváření clusteru poskytnutím samostatné identity (včetně certifikátů) pro každý. Další informace o výchozí nastavení řízení přístupu a jak změnit výchozí nastavení, najdete v části [řízení přístupu na základě rolí pro Service Fabric klienty](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

## <a name="secure-standalone-clusters-by-using-windows-security"></a>Samostatné clustery zabezpečení pomocí zabezpečení Windows
Chcete-li zabránit neoprávněnému přístupu ke clusteru Service Fabric, třeba zabezpečení clusteru. Zabezpečení je obzvláště důležité, když v clusteru běží úlohy v produkčním prostředí. Konfigurace uzlu k uzlu a uzel klienta zabezpečení pomocí zabezpečení Windows v souboru ClusterConfig.JSON.

Když Service Fabric potřebuje ke spuštění v rámci gMSA, nastavením konfigurace zabezpečení mezi uzly [ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security). K vytváření vztahů důvěryhodnosti mezi uzly, musíte je seznámen mezi sebou.

Pokud chcete použít skupinu počítačů v doméně služby Active Directory, je nakonfigurovat tak, že nastavíte ClusterIdentity zabezpečení mezi uzly. Další informace najdete v tématu [vytvořit skupinu počítačů ve službě Active Directory](https://msdn.microsoft.com/library/aa545347).

Uzel klienta zabezpečení můžete nakonfigurovat pomocí ClientIdentities. Je nutné nakonfigurovat cluster tak, aby rozpoznal které identity klienta můžete důvěřovat. Můžete vytvořit vztah důvěryhodnosti dvěma způsoby:

-   Zadejte skupinu uživatele domény, se můžou připojit.
-   Určení uživatelů domény uzlu, se můžou připojit.

## <a name="configure-application-security-in-service-fabric"></a>Konfigurace zabezpečení aplikace ve službě Service Fabric
### <a name="manage-secrets-in-service-fabric-applications"></a>Správa tajných kódů aplikace Service Fabric
Tajné klíče může být žádné citlivé údaje, jako je například úložiště připojovací řetězce, hesla nebo jiné hodnoty, které by neměly být zpracovat ve formátu prostého textu.

Můžete použít [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) spravovat klíče a tajné kódy. Použití tajných kódů v aplikaci však nemusí spoléhat na konkrétní cloudové platformě. Můžete nasazovat aplikace do clusteru, který je už jsou hostované kdekoli. Existují čtyři hlavní kroky v tomto toku:

1.  Získání certifikátu šifrování data.
2.  Instalace certifikátu ve vašem clusteru.
3.  Šifrovat hodnoty tajných kódů při nasazení aplikace pomocí certifikátu a vložit do konfiguračního souboru Settings.xml služby.
4.  Číst šifrované hodnoty mimo Settings.xml dešifrování pomocí stejného certifikátu šifrování.

Další informace najdete v tématu [správu tajných kódů aplikace Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="configure-security-policies-for-an-application"></a>Konfigurovat zásady zabezpečení pro aplikaci
Pomocí Azure Service Fabric zabezpečení může pomoct zabezpečené aplikace, které jsou spuštěny v clusteru pod různými uživatelskými účty. Zabezpečení Service Fabric také pomáhá je zabezpečit prostředky, které používají aplikace v době nasazení podle uživatelských účtů – například soubory, adresáře a certifikáty. Díky spouštění aplikací, i do sdíleného hostovaného prostředí, bezpečnější.

Úkoly ke konfiguraci zásad zabezpečení patří:

-   Konfigurace zásad pro vstupního bodu nastavení služby
-   Spouštění příkazů prostředí PowerShell ze vstupního bodu nastavení
-   Používání konzoly přesměrování pro místní ladění
-   Konfigurace zásad pro balíčky kódu služby
-   Přiřazení zásady zabezpečení přístupu pro koncové body HTTP a HTTPS

## <a name="secure-communication-for-services"></a>Zabezpečená komunikace služeb
Zabezpečení je jedním z nejdůležitějších aspektů komunikace. Rozhraní framework aplikace Reliable Services poskytuje několik nástrojů, které můžete použít ke zlepšení zabezpečení a předem připravených komunikační balíky. Další informace najdete v tématu [zabezpečená komunikace služeb vzdálené komunikace služby](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication).

## <a name="next-steps"></a>Další postup
- Koncepční informace o zabezpečení clusteru najdete v tématu [vytvořit cluster Service Fabric pomocí Azure Resource Manageru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) a [vytvořit cluster Service Fabric s využitím webu Azure portal](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal).
- Další informace o zabezpečení clusteru Service Fabric najdete v tématu [scénáře zabezpečení clusteru Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).

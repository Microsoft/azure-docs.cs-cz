---
title: Přehled zabezpečení služby Azure Service Fabric | Microsoft Docs
description: Tento článek poskytuje přehled služby Azure Service Fabric Security.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: ca2f346950d84fda736437f439efc5d35e342799
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934573"
---
# <a name="azure-service-fabric-security-overview"></a>Přehled zabezpečení služby Azure Service Fabric
[Azure Service Fabric](../../service-fabric/service-fabric-overview.md) je platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb. Service Fabric řeší problémy při vývoji a správě cloudových aplikací. Vývojáři a správci se můžou vyhnout složitým problémům s infrastrukturou a soustředit se na implementaci klíčových a náročných úloh, které jsou škálovatelné a spolehlivé.

Tento článek představuje přehled důležitých informací o zabezpečení pro nasazení Service Fabric.

## <a name="secure-your-cluster"></a>Zabezpečení clusteru
Služba Azure Service Fabric orchestruje služby napříč clusterem počítačů. Clustery musí být zabezpečené, aby se zabránilo neautorizovaným uživatelům v připojení k nim, zejména když používají produkční úlohy. I když je možné vytvořit nezabezpečený cluster, může to umožnit anonymním uživatelům připojit se ke clusteru (Pokud zveřejňuje koncové body správy pro veřejný Internet).

Pro clustery, které používají samostatné nebo v Azure, jsou dva scénáře, které je třeba zvážit, jsou zabezpečení mezi uzly a mezi klientem a uzlem. K implementaci těchto scénářů můžete použít různé technologie.

### <a name="node-to-node-security"></a>Zabezpečení mezi uzly
Zabezpečení mezi uzly se vztahuje na komunikaci mezi virtuálními počítači nebo počítači v clusteru. V případě zabezpečení mezi uzly se můžou v clusteru hostovat aplikace a služby, jenom počítače, které jsou autorizované pro připojení ke clusteru.

Clustery spuštěné v Azure nebo samostatné clustery, které běží ve Windows, můžou používat [zabezpečení certifikátů](https://msdn.microsoft.com/library/ff649801.aspx) nebo [zabezpečení systému Windows](https://msdn.microsoft.com/library/ff649396.aspx) pro počítače se systémem Windows Server.

#### <a name="node-to-node-certificate-security"></a>Zabezpečení certifikátů mezi uzly

Service Fabric používá certifikáty serveru X. 509, které zadáte při vytváření clusteru. Rychlý přehled toho, co tyto certifikáty jsou a jak je můžete získat nebo vytvořit, najdete v tématu [práce s certifikáty](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates).

Zabezpečení certifikátu konfigurujete při vytváření clusteru prostřednictvím Azure Portal, Azure Resource Manager šablon nebo samostatné šablony JSON. Můžete zadat primární certifikát a volitelný sekundární certifikát, který se používá pro přecházení mezi certifikáty. Primární a sekundární certifikáty, které zadáte, by se měly lišit od klienta pro správu a certifikátů klienta jen pro čtení, které zadáte pro [zabezpečení klient-uzel](../../service-fabric/service-fabric-cluster-security.md).

### <a name="client-to-node-security"></a>Zabezpečení klient-uzel
Zabezpečení klienta na Node nakonfigurujete pomocí identit klientů. Aby bylo možné navázat vztah důvěryhodnosti mezi klientem a clusterem, je nutné nakonfigurovat cluster tak, aby věděli, které identity klientů může důvěřovat.

Service Fabric podporuje dva typy řízení přístupu pro klienty, kteří jsou připojení ke clusteru Service Fabric:

-   **Správce**: Úplný přístup k možnostem správy, včetně možností pro čtení a zápis.
-   **Uživatel**: Jenom přístup pro čtení k funkcím správy (například možnosti dotazů) a možnost přeložit aplikace a služby.

Správci clusteru můžou pomocí řízení přístupu omezit přístup k určitým typům operací clusteru. Tím je cluster bezpečnější.

#### <a name="client-to-node-certificate-security"></a>Zabezpečení certifikátu klienta k uzlu

Zabezpečení certifikátů mezi uzly konfigurujete při vytváření clusteru prostřednictvím Azure Portal, Správce prostředků šablon nebo samostatné šablony JSON. Je nutné zadat certifikát klienta správce nebo klientský certifikát uživatele. Ujistěte se, že se tyto certifikáty liší od primárních a sekundárních certifikátů, které zadáte pro zabezpečení mezi uzly.

Klienti, kteří se připojují ke clusteru pomocí certifikátu správce, mají úplný přístup k možnostem správy. Klienti, kteří se připojují ke clusteru pomocí klientského certifikátu jen pro čtení, mají přístup jen pro čtení k funkcím pro správu. Jinými slovy, tyto certifikáty se používají pro řízení přístupu na základě role (RBAC).

Informace o tom, jak nakonfigurovat zabezpečení certifikátů v clusteru, najdete v tématu [Nastavení clusteru pomocí šablony Azure Resource Manager](../../service-fabric/service-fabric-cluster-creation-via-arm.md).

#### <a name="client-to-node-azure-active-directory-security"></a>Zabezpečení Azure Active Directory klienta na uzel

Clustery, které běží na Azure, můžou také zabezpečit přístup k koncovým bodům správy pomocí Azure Active Directory (Azure AD). Informace o tom, jak vytvořit potřebné artefakty Azure Active Directory, jak je naplnit během vytváření clusteru a jak se připojit k těmto clusterům, najdete v tématu [Nastavení clusteru pomocí Azure Resource Manager šablony](../../service-fabric/service-fabric-cluster-creation-via-arm.md).

Azure AD umožňuje organizacím (označovaným jako klienti) spravovat přístup uživatelů k aplikacím. Existují aplikace s webovým přihlašovacím uživatelským rozhraním a aplikacemi s nativním prostředím klienta.

Cluster Service Fabric nabízí několik vstupních bodů ke svým funkcím správy, včetně webových Service Fabric Explorer a sady Visual Studio. V důsledku toho vytvoříte dvě aplikace Azure AD pro řízení přístupu ke clusteru: jednu webovou aplikaci a jednu nativní aplikaci.

U clusterů Azure doporučujeme použít zabezpečení Azure AD k ověřování klientů a certifikátů pro zabezpečení mezi uzly.

Pro samostatné clustery Windows serveru se systémem Windows Server 2012 R2 a Active Directory doporučujeme používat zabezpečení systému Windows se skupinovými účty spravované služby (účty gMSA). V opačném případě použijte zabezpečení systému Windows s účty systému Windows.

## <a name="understand-monitoring-and-diagnostics-in-service-fabric"></a>Pochopení monitorování a diagnostiky v Service Fabric
[Monitorování a diagnostika](../../service-fabric/service-fabric-diagnostics-overview.md) jsou zásadní pro vývoj, testování a nasazení aplikací a služeb v jakémkoli prostředí. Řešení Service Fabric fungují nejlépe při implementaci monitorování a diagnostiky, aby bylo zajištěno, že aplikace a služby fungují podle očekávání v místním vývojovém prostředí nebo v produkčním prostředí.

Z hlediska zabezpečení jsou hlavní cíle monitorování a diagnostiky:

-   Detekovat a diagnostikovat problémy s hardwarem a infrastrukturou, které mohou být způsobeny událostí zabezpečení.
-   Odhalte problémy softwaru a aplikací, které by mohly být indikátorem ohrožení (IoC).
-   Pochopte spotřebu prostředků, abyste zabránili nechtěnému odepření služby.

Pracovní postup monitorování a diagnostiky se skládá ze tří kroků:

1.  **Generování události**: Generování událostí zahrnuje události (protokoly, trasování, vlastní události) na úrovni infrastruktury (clusteru) i na úrovni aplikace nebo služby. Přečtěte si další informace o událostech na úrovni [infrastruktury](../../service-fabric/service-fabric-diagnostics-event-generation-infra.md) a událostech na [úrovni aplikace](../../service-fabric/service-fabric-diagnostics-event-generation-app.md) a zjistěte, co je k dispozici a jak přidat další instrumentaci.

2.  **Agregace událostí**: Vygenerované události musí být shromažďovány a agregovány předtím, než mohou být zobrazeny. Typicky doporučujeme použít [Azure Diagnostics](../../service-fabric/service-fabric-diagnostics-event-aggregation-wad.md) (podobně jako shromáždění protokolů založeného na agentech) nebo [využitím eventflow](../../service-fabric/service-fabric-diagnostics-event-aggregation-eventflow.md) (vnitroprocesové shromažďování protokolů).

3.  **Analýza**: Aby bylo možné analyzovat a zobrazovat, je nutné události v některém formátu zpřístupnit a zobrazit. K dispozici je několik platforem pro analýzu a vizualizaci dat monitorování a diagnostiky. Doporučujeme [protokoly Azure monitor](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) a [Azure Application Insights](../../service-fabric/service-fabric-diagnostics-event-analysis-appinsights.md) , protože jsou správně integrovány s Service Fabric.

[Azure monitor](../../azure-monitor/overview.md) můžete použít také k monitorování mnoha prostředků Azure, na kterých je sestaven Service Fabric cluster.

Sledovací zařízení je samostatná služba, která může sledovat stav a zatížení napříč službami a nahlásit stav pro cokoli v hierarchii modelu stavu. Použití sledovacího zařízení vám může pomoci zabránit chybám, které se nezjistily na základě zobrazení jedné služby. 

Sledovací zařízení jsou také dobrým místem pro hostování kódu, který provádí nápravné akce bez zásahu uživatele. Příkladem je čištění souborů protokolu v úložišti v určitých časových intervalech. Ukázkovou implementaci sledovací služby najdete v [ukázce sledovacího zařízení Azure Service Fabric](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/).

## <a name="secure-communication-by-using-certificates"></a>Zabezpečená komunikace pomocí certifikátů
Certifikáty vám pomůžou zabezpečit komunikaci mezi různými uzly samostatného clusteru Windows. Pomocí certifikátů X. 509 můžete také ověřit klienty, kteří se připojují k tomuto clusteru. Tím se zajistí, že ke clusteru budou mít přístup jenom autorizovaní uživatelé. Při vytváření certifikátu doporučujeme pro cluster povolit certifikát.

Digitální certifikáty X. 509 se běžně používají k ověřování klientů a serverů. Používají se také k šifrování a digitálnímu podepisování zpráv.

V následující tabulce jsou uvedené certifikáty, které v instalaci clusteru potřebujete:

|Nastavení informací o certifikátu |Popis|
|-------------------------------|-----------|
|ClusterCertificate|    Tento certifikát je nutný k zabezpečení komunikace mezi uzly v clusteru. Můžete použít dva certifikáty clusteru: primární certifikát a sekundární pro upgrade.|
|ServerCertificate| Tento certifikát se zobrazí klientovi, když se pokusí připojit k tomuto clusteru. Můžete použít dva certifikáty serveru: primární certifikát a sekundární pro upgrade.|
|ClientCertificateThumbprints|  Toto je sada certifikátů, které se mají nainstalovat na ověřených klientech.|
|ClientCertificateCommonNames|  Toto je běžný název prvního klientského certifikátu pro CertificateCommonName. CertificateIssuerThumbprint je kryptografický otisk vystavitele tohoto certifikátu.|
|ReverseProxyCertificate|   Toto je volitelný certifikát, který můžete použít k zabezpečení reverzního [proxy serveru](../../service-fabric/service-fabric-reverseproxy.md).|

Další informace o zabezpečení certifikátů najdete v tématu [Zabezpečení samostatného clusteru ve Windows pomocí certifikátů X. 509](../../service-fabric/service-fabric-windows-cluster-x509-security.md).

## <a name="understand-role-based-access-control"></a>Principy řízení přístupu na základě rolí
Role správce a uživatele klienta v době vytváření clusteru určíte tak, že poskytnete samostatné identity (včetně certifikátů) pro každý z nich. Další informace o výchozím nastavení řízení přístupu a o tom, jak změnit výchozí nastavení, najdete v tématu [řízení přístupu na základě rolí pro klienty Service Fabric](../../service-fabric/service-fabric-cluster-security-roles.md).

## <a name="secure-standalone-clusters-by-using-windows-security"></a>Zabezpečení samostatných clusterů pomocí zabezpečení systému Windows
Aby se zabránilo neoprávněnému přístupu ke clusteru Service Fabric, musíte zabezpečit cluster. Zabezpečení je obzvláště důležité, když cluster spouští produkční úlohy. Zabezpečení mezi uzly a klient-uzel nakonfigurujete pomocí zabezpečení systému Windows v souboru ClusterConfig. JSON.

Když Service Fabric potřebuje běžet pod gMSA, nakonfigurujete zabezpečení mezi uzly nastavením [ClustergMSAIdentity](../../service-fabric/service-fabric-windows-cluster-windows-security.md). Chcete-li vytvořit vztahy důvěryhodnosti mezi uzly, je nutné, abyste si je navzájem dozvěděli.

Pokud chcete použít skupinu počítačů v doméně služby Active Directory, nakonfigurujte zabezpečení mezi uzly nastavením ClusterIdentity. Další informace najdete v tématu [Vytvoření skupiny počítačů ve službě Active Directory](https://msdn.microsoft.com/library/aa545347).

Zabezpečení klienta na Node můžete nakonfigurovat pomocí ClientIdentities. Cluster musíte nakonfigurovat tak, aby rozpoznal, které identity klientů může důvěřovat. Vztah důvěryhodnosti můžete vytvořit dvěma způsoby:

-   Určete uživatele skupiny domény, které se mohou připojit.
-   Zadejte uživatele uzlu domény, které se mohou připojit.

## <a name="configure-application-security-in-service-fabric"></a>Konfigurace zabezpečení aplikace v Service Fabric
### <a name="manage-secrets-in-service-fabric-applications"></a>Správa tajných kódů v aplikacích Service Fabric
Tajné kódy můžou obsahovat citlivé informace, jako jsou například připojovací řetězce úložiště, hesla nebo jiné hodnoty, které by neměly být zpracovány v prostém textu.

Ke správě klíčů a tajných kódů můžete použít [Azure Key Vault](../../key-vault/key-vault-whatis.md) . Použití tajných klíčů v aplikaci se ale nespoléhá na konkrétní cloudovou platformu. Aplikace můžete nasadit do clusteru, který je hostovaný kdekoli. Tento tok obsahuje čtyři hlavní kroky:

1.  Získejte certifikát pro zakódování dat.
2.  Nainstalujte certifikát do clusteru.
3.  Šifrování tajných hodnot při nasazování aplikace s certifikátem a jejich vložení do konfiguračního souboru. XML služby.
4.  V souboru Settings. XML se šifrují šifrované hodnoty, které je dešifrují se stejným certifikátem pro zakódování.

Další informace najdete v tématu [Správa tajných kódů v aplikacích Service Fabric](../../service-fabric/service-fabric-application-secret-management.md).

### <a name="configure-security-policies-for-an-application"></a>Konfigurace zásad zabezpečení pro aplikaci
Pomocí služby Azure Service Fabric Security můžete pomoci zabezpečit aplikace spuštěné v clusteru v rámci různých uživatelských účtů. Service Fabric Security také pomáhá zabezpečit prostředky používané aplikacemi v době nasazování v rámci uživatelských účtů, například soubory, adresáře a certifikáty. Díky tomu budou aplikace spuštěny i ve sdíleném hostovaném prostředí, což je bezpečnější.

Mezi úlohy týkající se konfigurace zásad zabezpečení patří:

-   Konfigurace zásad pro vstupní bod nastavení služby
-   Spuštění příkazů PowerShellu z vstupního bodu instalace
-   Použití přesměrování konzoly pro místní ladění
-   Konfigurace zásad pro balíčky kódu služby
-   Přiřazení zásad zabezpečení přístupu pro koncové body HTTP a HTTPS

## <a name="secure-communication-for-services"></a>Zabezpečená komunikace pro služby
Zabezpečení je jedním z nejdůležitějších aspektů komunikace. Rozhraní Reliable Services Application Framework poskytuje několik předem připravených komunikačních zásobníků a nástrojů, které můžete použít ke zvýšení zabezpečení. Další informace najdete v tématu [Zabezpečená komunikace vzdálené služby pro službu](../../service-fabric/service-fabric-reliable-services-secure-communication.md).

## <a name="next-steps"></a>Další kroky
- Koncepční informace o zabezpečení clusteru najdete v tématu [Vytvoření clusteru Service Fabric pomocí Azure Resource Manager](../../service-fabric/service-fabric-cluster-creation-via-arm.md) a [Vytvoření clusteru Service Fabric pomocí Azure Portal](../../service-fabric/service-fabric-cluster-creation-via-portal.md).
- Další informace o zabezpečení clusteru v Service Fabric najdete v tématu [Service Fabric scénáře zabezpečení clusteru](../../service-fabric/service-fabric-cluster-security.md).

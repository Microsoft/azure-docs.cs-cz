---
title: Osvědčené postupy provozní zabezpečení Azure | Dokumentace Microsoftu
description: Tento článek poskytuje sadu osvědčených postupů pro provozní zabezpečení Azure.
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
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: d8f6ad48c62ff2021c91e593cee44dd6f5551247
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297904"
---
# <a name="azure-operational-security-best-practices"></a>Osvědčené postupy Azure provozní zabezpečení
Azure Operational Security odkazuje na služby, ovládací prvky a funkce, které jsou dostupné pro uživatele k ochraně svých dat, aplikací a dalších prostředků ve službě Microsoft Azure. Provozní zabezpečení Azure je založen na rozhraní, která zahrnuje poznatky získané při různé schopnosti, které jsou jedinečné pro Microsoftu, mezi které patří Microsoft Security Development Lifecycle (SDL), programu Microsoft Security Response Center a deep povědomí o zabezpečení.

V tomto článku se podíváme na kolekci osvědčené postupy zabezpečení Azure database. Tyto osvědčené postupy jsou odvozeny z našich zkušeností s zabezpečení služby Azure database a prostředí zákazníků, jako je sami.

Pro každý osvědčeným postupem je vám vysvětlíme:
-   Jaký je doporučený postup
-   Proč chcete povolit tento osvědčený postup
-   Pokud chcete povolit osvědčený postup, co mohou být způsobeny
- Jak lze zjistíte, jak povolit osvědčený postup

Tento článek osvědčené postupy provozní zabezpečení Azure je založen na stanovisko shody a možnostech platformy Azure a sady funkcí, protože existují v okamžiku, kdy byla zapsána v tomto článku. Názory a technologie v průběhu času měnit a tento článek bude aktualizován v pravidelných intervalech, aby tyto změny projevily.

Provozní zabezpečení Azure, které zahrnují osvědčené postupy popsané v tomto článku:

-   Monitorování, Správa a ochrana cloudové infrastruktury
-   Správa identit a provádět jednotné přihlašování (SSO)
-   Trasování požadavků, analýze trendů využití a Diagnostika problémů
-   Monitorování služeb s řešením centralizované monitorování
-   Hrozbami, detekci a reakce na hrozby
-   Monitorování sítě založené na scénářích začátku do konce
-   Zabezpečení nasazení pomocí prověřené nástroje DevOps

## <a name="monitor-manage-and-protect-cloud-infrastructure"></a>Monitorování, Správa a ochrana cloudové infrastruktury
Provoz IT zodpovídá za správu infrastruktury datového centra, aplikace a data, včetně stabilitu a zabezpečení těchto systémů. Získávání informací o zabezpečení napříč často zvýšení komplexní IT prostředí však vyžaduje organizacím cobble dohromady data z několika systémů zabezpečení a správu.

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) je společnosti Microsoft cloudové řešení pro správu IT, která pomáhá spravovat a chránit místní a cloudovou infrastrukturu.

[Řešení OMS Security and Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) umožňují IT aktivně monitorovat všechny prostředky, které může pomoci minimalizovat dopad incidentů zabezpečení. OMS Security and Audit mít domény zabezpečení, které lze použít pro monitorování prostředků.

Další informace o OMS najdete v článku [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

Abychom vám hrozbami, detekci a reakce na hrozby, [řešení Operations Management Suite (OMS) zabezpečení a Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) shromažďuje a zpracovává data o vašich prostředků, která zahrnuje:

-   Protokol událostí zabezpečení
-   Události Trasování událostí pro Windows
-   Události auditování AppLocker
-   Protokol brány Windows Firewall
-   Události Rozšířené analýzy hrozeb
-   Výsledky základního vyhodnocování
-   Výsledky antimalwarového vyhodnocování
-   Výsledky vyhodnocování aktualizací a oprav
-   Datové proudy Syslog explicitně povolené v agentovi


## <a name="manage-identity-and-implement-single-sign-on"></a>Správa identit a implementaci jednotného přihlašování
[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) Microsoftu pro více tenantů cloudový adresář a služba pro správu identit.

[Azure AD](https://azure.microsoft.com/services/active-directory/) zahrnuje také plnohodnotné sadě [správy identit](https://docs.microsoft.com/azure/security/security-identity-management-overview) možnosti, jako je [ověřování službou Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication), Správa hesla pomocí samoobslužné služby, registrace zařízení Samoobslužná správa skupin, správu privilegovaných účtů, řízení přístupu na základě rolí, využití aplikací, sledování, formátováním auditování a monitorování a výstrah zabezpečení.

Tyto možnosti můžete pomůže zabezpečených cloudových aplikací, zjednodušit IT procesy, snížit náklady a ujistěte se, že dodržování podnikových předpisů cíle jsou splněny:

-   Správa identit a přístupu do cloudu
-   Zjednodušení přístupu uživatelů do cloudových aplikací
-   Ochrana citlivých dat a aplikací
-   Zajistěte pro svoje zaměstnance samoobslužné možnosti.
-   Integrace se službou Azure Active Directory

### <a name="identity-and-access-management-for-the-cloud"></a>Správa identit a přístupu do cloudu
Azure Active Directory (Azure AD) je komplexní [cloudové řešení správy identit a přístupu](https://www.microsoft.com/cloud-platform/identity-management), které poskytuje robustní sadu funkcí pro správu uživatelů a skupin. Pomáhá zabezpečit přístup k místním i cloudovým aplikacím, včetně webových služeb Microsoftu jako Office 365 a mnoho jiných společností než Microsoft software jako služba (SaaS) aplikací.
Zjistíte, jak povolit identity protection ve službě Azure AD, najdete v článku [povolení Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable).

### <a name="simplify-user-access-to-any-cloud-app"></a>Zjednodušení přístupu uživatelů do cloudových aplikací
[Povolit jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) pro zjednodušení přístupu uživatelů k tisícům cloudových aplikací ze zařízení s Windows, Mac OS, Android a iOS. Uživatelé můžou spouštět aplikace z panelu individuální přístupu na základě webové nebo mobilní aplikace pomocí svých přihlašovacích údajů společnosti. Můžete jít za hranice aplikací SaaS a publikovat místní webové aplikace a zajistit vysoce zabezpečený vzdálený přístup a jednotné přihlašování pomocí modulu Azure AD Application Proxy.

### <a name="protect-sensitive-data-and-applications"></a>Ochrana citlivých dat a aplikací
Povolit [ověřování Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) zabránit neoprávněnému přístupu k místním a cloudovým aplikacím pomocí víceúrovňového ověřování. Chraňte svoji firmu a reagujte na potenciální hrozby sledováním výstrah zabezpečení a sestav založených na strojovém učení, které rozpoznávají nekonzistentní vzorce přístupu.

### <a name="enable-self-service-for-your-employees"></a>Zajistěte pro svoje zaměstnance samoobslužné možnosti.
Delegujte důležité úkoly, jako je resetování hesel nebo vytváření a správa skupin, na svoje zaměstnance. [Povolit změnu hesla pomocí samoobslužné služby](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password), resetování nebo samoobslužné skupiny správy s využitím Azure AD.

### <a name="integrate-with-azure-active-directory"></a>Integrace se službou Azure Active Directory
Rozšíření [služby Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate) a další místní adresáře do služby Azure AD umožňuje jednotné přihlašování pro všechny cloudové aplikace. Atributy uživatelů se budou automaticky synchronizovat s vaší cloudovou adresářovou službou. 

Další informace o integraci služby Azure Active Directory a jak se dá povolit, najdete v článku [integrace místních adresářů se službou Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="trace-requests-analyze-usage-trends-and-diagnose-issues"></a>Trasování požadavků, analýze trendů využití a Diagnostika problémů
[Azure Storage Analytics](https://docs.microsoft.com/azure/storage/storage-analytics) provádí protokolování a poskytuje data metriky pro účet úložiště. Tato data můžete použít k trasování požadavků, analýze trendů použití a diagnostice problémů s účtem úložiště.

Ve výchozím nastavení pro nové účty úložiště jsou povolené metrikách Storage Analytics. Můžete povolit protokolování a nakonfigurovat metrik a protokolování na portálu Azure Portal; Podrobnosti najdete v tématu [monitorování účtu úložiště na webu Azure Portal](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Můžete také povolit Storage Analytics prostřednictvím kódu programu přes rozhraní REST API nebo knihovny klienta. Pomocí operace nastavit vlastnosti služby můžete povolit Storage Analytics jednotlivě pro každou službu.

Podrobný průvodce k identifikaci, diagnostice a řešit problémy související s Azure Storage pomocí Storage Analytics a další nástroje, najdete v části [monitorování, Diagnostika a řešení problémů s Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Další informace o integraci služby Azure Active Directory a jak se dá povolit, najdete v článku [povolení a konfigurace úložiště analýzy](https://docs.microsoft.com/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics?redirectedfrom=MSDN).

## <a name="monitoring-services"></a>Monitorování služby
Cloudové aplikace jsou složité s řadu pohyblivých částí. Monitorování poskytuje data k zajištění, že systém zůstane vaší aplikace v provozu a spuštěná v dobrém stavu. Také pomáhá stave vypnout potenciální problémy a řešení potíží s poslední těch, které jsou. Kromě toho můžete data monitorování získat podrobný přehled o vaší aplikaci. Dané znalosti můžete dozvíte, jak zlepšit výkon aplikace nebo udržovatelnosti nebo automatizaci akcí, které by jinak vyžadují ruční zásah.

### <a name="monitor-azure-resources"></a>Monitorování prostředků Azure
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) je služba platformy, která poskytuje jeden zdroj pro monitorování prostředků Azure. Prostřednictvím služby Azure Monitor můžete vizualizovat metriky a protokoly pocházející z prostředků v Azure, zadávat na ně dotazy, směrovat je, archivovat je a provádět s nimi příslušné akce. S těmito daty můžete pracovat prostřednictvím okna portálu Monitor, [rutin prostředí PowerShell pro monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples), [rozhraní příkazového řádku pro různé platformy](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples) nebo [rozhraní API REST služby Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx).

### <a name="enable-autoscale-with-azure-monitor"></a>Povolit automatické škálování pomocí Azure monitoru
Povolit [automatické škálování služby Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started) platí pouze pro škálovací sady virtuálních počítačů (VMSS), cloud services, plánů služby app service a app service Environment.

### <a name="manage-roles-permissions-and-security"></a>Spravovat role oprávnění a zabezpečení
Mnoho týmů potřebuje pro výhradně [regulovat přístup k monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security) data a nastavení. Například pokud jste členy týmu, kteří pracují výhradně na monitorování (techniky podpory, technikům devops) nebo pokud používáte poskytovatel spravované služby, můžete jim udělit přístup k datům monitorování pouze při omezení jejich schopnost vytvářet, upravovat, nebo Odstraňte prostředky.

To ukazuje, jak rychle použít předdefinované role RBAC monitorování na uživatele v Azure nebo vytvářet vlastní vlastní role pro uživatele, který potřebuje monitorování omezená oprávnění. Pak popisuje aspekty zabezpečení pro vaše prostředky související s Azure Monitor a jak můžete omezit přístup k datům, které obsahují.

## <a name="prevent-detect-and-respond-to-threats"></a>Hrozbami, detekci a reakce na hrozby
Detekce hrozeb služby Security Center funguje tak, že se automaticky shromažďují informace o zabezpečení z prostředků Azure, sítě a připojených partnerských řešení. Analyzuje se tyto informace, často přitom koreluje data z více zdrojů, a identifikovat hrozby. U výstrah zabezpečení se ve službě Security Center stanoví priority spolu s doporučením, jak danou hrozbu vyřešit.

-   [Konfigurace zásad zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-policies) pro vaše předplatné Azure.
-   Použití [doporučení ve službě Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations) k ochraně vašich prostředků Azure.
-   Prohlížet a spravovat vaše aktuální [výstrahy zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts).

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) pomáhá předcházet hrozbám, rozpoznávat je a reagovat na ně a nabízí lepší přehled o zabezpečení prostředků Azure a kontrolu nad nimi. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

Security Center nabízí snadno použitelné a efektivní funkce prevence, zjišťování a reakce na ohrožení, které jsou integrované v Azure. Klíčové funkce:

-   Vysvětlení stavu cloudového zabezpečení
-   Převzetí kontroly nad zabezpečením cloudu
-   Snadné nasazení integrovaných řešení cloudového zabezpečení
-   Rozpoznání hrozeb a rychlá reakce

### <a name="understand-cloud-security-state"></a>Vysvětlení stavu cloudového zabezpečení
Díky službě Azure Security Center získáte přehled o stavu zabezpečení všech vašich prostředků Azure. Na první pohled ověřte, že jsou splněné kontrolních mechanismů zabezpečení a správně nakonfigurovaný a rychle identifikovat všechny prostředky, které vyžadují pozornost.

### <a name="take-control-of-cloud-security"></a>Převzetí kontroly nad zabezpečením cloudu
Definování [zásady zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-policies) pro předplatná Azure podle potřeb zabezpečení vaší společnosti cloudu, přizpůsobené podle typu aplikací nebo citlivosti dat v každém předplatném. K tomu, abyste vlastníky prostředků provedli procesem implementace požadovaných kontrolních mechanismů, použijte doporučení na základě zásad – zabezpečení cloudu se tak provede najisto.

### <a name="easily-deploy-integrated-cloud-security-solutions"></a>Snadné nasazení integrovaných řešení cloudového zabezpečení
[Povolit řešení zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) od Microsoftu a jeho partnerů, včetně špičkové firewallů a antimalwaru. Používá se zjednodušené zřizování, které umožňuje nasazovat řešení zabezpečení – dokonce i změny v nastavení sítě se nakonfigurují automaticky. Události zabezpečení z partnerských řešení se automaticky shromažďují, aby je bylo možné analyzovat a generovat z nich upozornění.

### <a name="detect-threats-and-respond-fast"></a>Rozpoznání hrozeb a rychlá reakce
Udržování náskoku před aktuálními a nově vznikajícími cloudovými hrozbami vyžaduje integrovaný analytický přístup. Díky spojení globálních Microsoft [hrozeb](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities) a zkušenosti, díky přehledu o cloudových událostech týkajících se zabezpečení celého nasazení Azure, Security Center pomáhá včas zjišťovat skutečné hrozby a snížil počet falešných poplachů. Výstrahy cloudového zabezpečení poskytují podrobné informace o kampaních obsahujících útoky, včetně souvisejících událostí a ovlivněných prostředků a navrhují možnosti, jak tyto problémy vyřešit a rychle obnovit provoz.

## <a name="end-to-end-scenario-based-network-monitoring"></a>Monitorování sítě založené na scénářích začátku do konce
Zákazníků, kteří vytvářejí síť začátku do konce v Azure pomocí Orchestrace a sestavování různých jednotlivým síťovým prostředkům, například virtuální síť, ExpressRoute, služba Application Gateway, nástroje pro vyrovnávání zatížení a další. Monitorování je k dispozici na všech síťových prostředků.

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) je místní služba, která umožňuje monitorovat a diagnostikovat podmínky na scénář sítě úrovně, do a z Azure. Pro diagnostiku sítě a vizualizačních nástrojů, které jsou k dispozici pomocí služby Network Watcher pomáhají porozumět, diagnostice a získání přehledu o vaší síti v Azure.

### <a name="automate-remote-network-monitoring-with-packet-capture"></a>Automatizace monitorování vzdálené sítě pomocí zachytávání paketů
Při monitorování a diagnostice síťových potíží není nutné se připojit k virtuálním počítačům pomocí Network Watcheru. Aktivační událost [zachytávání paketů](https://docs.microsoft.com/azure/network-watcher/network-watcher-alert-triggered-packet-capture) tím, že nastavení výstrah a získat přístup k informacím o výkonu v reálném čase na úrovni paketů. Když narazíte na problém, můžete ho prozkoumat podrobněji a lépe diagnostikovat.

### <a name="gain-insight-into-your-network-traffic-using-flow-logs"></a>Získejte přehled o provozu sítě pomocí protokolů toků
Získáte lepší přehled pomocí vzoru provoz sítě [protokoly toků skupin zabezpečení sítě](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview). Informace, které protokoly toků poskytují umožňuje shromažďovat data pro dodržování předpisů, auditování a monitorování profilu síťového zabezpečení.

### <a name="diagnose-vpn-connectivity-issues"></a>Diagnostika potíží s připojením VPN
Network Watcher dává možnost [diagnostikovat nejběžnější potíže připojeními a branami VPN](https://docs.microsoft.com/azure/network-watcher/network-watcher-diagnose-on-premises-connectivity). Umožňují nejenom identifikovat problém, ale také na základě podrobných protokolů vytvořili za účelem dále zkoumat.

Další informace o tom, jak konfigurace Network watcheru a jak se dá povolit, najdete v článku [konfigurace Network watcheru](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="secure-deployment-using-proven-devops-tools"></a>Zabezpečení nasazení pomocí prověřené nástroje DevOps
Toto jsou některé seznamu o Azure DevOps postupy v tomto prostoru cloudu společnosti Microsoft, který umožňuje podnikům a týmům produktivnějších a efektivnějších.

-   **Infrastruktura jako kód (IaC):** infrastruktura jako kód je sada technik a postupů, které pomáhají odborníkům v odebrání režie související se správou infrastruktury modulární a každodenní sestavení. To umožňuje odborníkům na IT vytvářet a spravovat jejich prostředí moderních serverových způsobem, který je třeba jak vývojářům softwaru vytvářet a spravovat kód aplikace. Pro Azure, máme [Azure Resource Manageru]( https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) umožňuje zřizovat aplikace pomocí deklarativní šablony. S jednou šablonou můžete nasadit několik služeb společně s jejich závislostmi. Stejnou šablonu můžete použít k opakovanému nasazení aplikace během každé fáze životního cyklu této aplikace.
-   **Průběžná integrace a nasazování:** projektů Azure DevOps můžete nakonfigurovat [automaticky vytvářet a nasazovat](https://www.visualstudio.com/docs/build/overview) do Azure web apps nebo cloud services. Azure DevOps automaticky nasadí binární soubory po provedení po každé kód vrácení se změnami sestavení do Azure. Proces sestavení balíčku je zde popsáno, je ekvivalentní příkazu balíčku v sadě Visual Studio a postup publikování je ekvivalentní příkazu Publikovat v sadě Visual Studio.
-   **Release Management:** sady Visual Studio [Release Management](https://msdn.microsoft.com/library/vs/alm/release/overview) je skvělým řešením pro automatizaci nasazení více fázi procesu vydávání verzí. Vytvoření spravovaných postupů plynulého nasazování kanálů k uvolnění rychlé, snadné a časté. Pomocí řešení Release Management mnohem dokážeme automatizovat náš proces vydávání verzí a budeme mít v předdefinované schvalovacích pracovních postupů. Nasazení v místním prostředí a do cloudu, rozšíření a přizpůsobení podle potřeby.
-   **Monitorování výkonu aplikace:** zjistit potíže, vyřešit problémy a neustále vylepšovat vaše aplikace. Rychle diagnostikuje problémy uvedené ve vaší živé aplikaci. Pochopte, co s ní vaši uživatelé dělají. Konfigurace se snadno přidáte kód JS a webconfig záznam a zobrazit výsledky v rámci minut na portálu najdete všechny podrobnosti. [App insights](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/) pomáhá podnikům pro rychlejší zjišťování problémů s & nápravy problému.
-   **Načíst testování a automatické škálování:** vyskytly problémy s výkonem v aplikaci a zlepšovat kvalitu nasazení a a ujistěte se, že naše aplikace je vždy nahoru nebo budou k dispozici pro obchodní potřeby. Ujistěte se, že vaše aplikace dokáže zpracovat provoz pro další spuštění nebo marketingové kampani. Začněte spouštět cloudové [zátěžové testy](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) raz téměř s Azure DevOps.

## <a name="next-steps"></a>Další postup
- Další informace o [provozní zabezpečení Azure](https://docs.microsoft.com/azure/security/azure-operational-security).
- Další informace [Operations Management Suite | Security & Compliance](https://www.microsoft.com/cloud-platform/security-and-compliance).
- [Začínáme se službou Operations Management Suite Security a řešení auditu](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started).

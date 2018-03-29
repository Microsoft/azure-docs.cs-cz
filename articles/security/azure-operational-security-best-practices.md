---
title: Azure osvědčené postupy zabezpečení provozní | Microsoft Docs
description: Tento článek obsahuje sadu osvědčené postupy pro zabezpečení provozu Azure.
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: bb7e0df046ecc2ffcd3fa59ac53edf36095933c7
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="azure-operational-security-best-practices"></a>Osvědčené postupy provozní zabezpečení Azure
Zabezpečení provozu Azure se odkazuje na služby, ovládací prvky a funkce, které jsou k dispozici uživatelům pro ochranu svá data, aplikace a dalších prostředků ve službě Microsoft Azure. Zabezpečení provozu Azure je založený na rozhraní, které zahrnuje poznatky získané při různých možnostech, které jsou jedinečné pro společnosti Microsoft, včetně Microsoft SDL Security Development Lifecycle (), programu Microsoft Security Response Center a hloubkové povědomí o povahu hrozeb počítačové bezpečnosti.

V tomto článku probereme kolekce osvědčené postupy zabezpečení databáze Azure. Tyto doporučené postupy jsou odvozeny od našich zkušeností s zabezpečení databáze Azure a prostředí zákazníků, jako sami.

Pro každý osvědčený postup objasníme:
-   Co je osvědčeným postupem
-   Proč chcete povolit tento osvědčený postup
-   Pokud se nepodaří povolit osvědčený postup, co může být výsledkem
- Jak můžete informace o povolení osvědčený postup

Tento článek osvědčené postupy provozní zabezpečení Azure je založen na konsenzu stanovisko a možnostech platformy Azure a sady funkcí, které jsou v době, kdy byla zapsána v tomto článku. Názory a technologie v průběhu času mění a v tomto článku budeme je aktualizovat v pravidelných intervalech, aby odrážela tyto změny.

Azure provozního zabezpečení osvědčené postupy popsané v tomto článku patří:

-   Monitorovat, spravovat a chránit infrastruktury cloudu
-   Správa identity a implementovat jednotné přihlašování (SSO)
-   Trasování požadavků, analyzovat trendy využití a diagnostikovat problémy
-   Monitorování služeb s řešením centralizované monitorování
-   Zabránit, zjistit a reagovat na hrozby
-   Monitorování sítě na základě scénáře začátku do konce
-   Zabezpečit pomocí Principy DevOps nástrojů nasazení

## <a name="monitor-manage-and-protect-cloud-infrastructure"></a>Monitorovat, spravovat a chránit infrastruktury cloudu
IT oddělení je zodpovědný za správu infrastruktuře datacentra, aplikace a data, včetně stability a zabezpečení těchto systémech. Získat přehled o zabezpečení napříč zvýšení komplexní prostředí IT často však vyžaduje organizacím cobble společně data z několika systémů, zabezpečení a správu.

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) je společnosti Microsoft založená na cloudu IT řešení správy, které pomáhá spravovat a chránit místní a cloudové infrastruktury.

[Řešení OMS zabezpečení a Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) umožňuje IT aktivně sledovat všechny prostředky, které může pomoci minimalizovat dopad incidentů, zabezpečení. OMS zabezpečení a Audit mít zabezpečení domény, které lze použít ke sledování prostředků.

Další informace o OMS najdete v článku [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

Můžete zabránit, zjistit a reagovat na hrozby, [Operations Management Suite (OMS) zabezpečení a Audit řešení](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) shromažďuje a zpracovává data o vašich prostředků, která zahrnuje:

-   Protokol událostí zabezpečení
-   Události Trasování událostí pro Windows
-   Události auditování AppLocker
-   Protokol brány Windows Firewall
-   Události Rozšířené analýzy hrozeb
-   Výsledky základního vyhodnocování
-   Výsledky antimalwarového vyhodnocování
-   Výsledky vyhodnocování aktualizací a oprav
-   Datové proudy Syslog, které jsou výslovně povoleny u agenta


## <a name="manage-identity-and-implement-single-sign-on"></a>Správa identity a implementovat jednotné přihlašování
[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) víceklientské cloudový adresář společnosti Microsoft a služba identity management.

[Azure AD](https://azure.microsoft.com/services/active-directory/) také zahrnuje úplná sada [správy identit](https://docs.microsoft.com/azure/security/security-identity-management-overview) funkcí, včetně [služby Multi-Factor authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication), registrace zařízení, správu hesla pomocí samoobslužné služby, Samoobslužná správa skupin, správy privilegovaného účtu, řízení přístupu na základě rolí, využití aplikací, sledování, bohaté auditování a sledování a výstrah zabezpečení.

Tyto možnosti můžete pomůže zabezpečené cloudové aplikace, zjednodušit procesy IT, snížit náklady a ujistěte se, zda jsou splněny dodržování podnikových předpisů cíle:

-   Správa identit a přístupu do cloudu
-   Zjednodušení přístupu uživatelů do cloudových aplikací
-   Ochrana citlivých dat a aplikací
-   Zajistěte pro svoje zaměstnance samoobslužné možnosti.
-   Integrace se službou Azure Active Directory

### <a name="identity-and-access-management-for-the-cloud"></a>Správa identit a přístupu do cloudu
Azure Active Directory (Azure AD) představuje komplexní [cloudové řešení správy identit a přístupu](https://www.microsoft.com/cloud-platform/identity-management), který poskytuje výkonnou sadu funkcí pro správu uživatelů a skupin. Pomáhá zabezpečit přístup k místní i cloudové aplikace, včetně webových služeb společnosti Microsoft jako je Office 365 a množství softwaru jiných společností než Microsoft jako služba (SaaS) aplikace.
Další informace jak povolit ochranu identity ve službě Azure AD, najdete v části [povolení Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable).

### <a name="simplify-user-access-to-any-cloud-app"></a>Zjednodušení přístupu uživatelů do cloudových aplikací
[Povolit jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) zjednodušit přístup uživatelů k tisícům cloudových aplikací ze zařízení s Windows, Mac, Android a iOS. Uživatele můžete spustit panel přizpůsobený webový přístup nebo mobilní aplikace pomocí firemních přihlašovacích aplikací. Pomocí modulu Azure AD Application Proxy nad rámec SaaS aplikace a publikovat místní webové aplikace k poskytování vysoce zabezpečený vzdálený přístup a jednotné přihlašování.

### <a name="protect-sensitive-data-and-applications"></a>Ochrana citlivých dat a aplikací
Povolit [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) k zabránění neoprávněného přístupu k místním a cloudovým aplikacím tím, že poskytuje další úroveň ověřování. Chraňte svoji firmu a reagujte na potenciální hrozby sledováním výstrah zabezpečení a sestav založených na strojovém učení, které rozpoznávají nekonzistentní vzorce přístupu.

### <a name="enable-self-service-for-your-employees"></a>Zajistěte pro svoje zaměstnance samoobslužné možnosti.
Delegujte důležité úkoly, jako je resetování hesel nebo vytváření a správa skupin, na svoje zaměstnance. [Povolit změnu hesla pomocí samoobslužné služby](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password), resetování a samoobslužné služby skupiny správy s Azure AD.

### <a name="integrate-with-azure-active-directory"></a>Integrace se službou Azure Active Directory
Rozšíření [služby Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate) a všemi ostatními místní adresáře do Azure AD umožňující jednotného přihlašování pro všechny cloudové aplikace. Atributy uživatelů se budou automaticky synchronizovat s vaší cloudovou adresářovou službou. 

Další informace o integraci služby Azure Active Directory a jak jej povolit, najdete v článku [integraci místních adresářů se službou Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="trace-requests-analyze-usage-trends-and-diagnose-issues"></a>Trasování požadavků, analyzovat trendy využití a diagnostikovat problémy
[Azure Storage Analytics](https://docs.microsoft.com/azure/storage/storage-analytics) provádí protokolování a poskytuje data metriky pro účet úložiště. Tato data můžete použít k trasování požadavků, analýze trendů použití a diagnostice problémů s účtem úložiště.

Metriky Analytics úložiště jsou povolené ve výchozím nastavení pro nové účty úložiště. Můžete povolit protokolování a nakonfigurovat metrik a protokolování na portálu Azure; Podrobnosti najdete v tématu [monitorování účtu úložiště na portálu Azure](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Můžete také povolit Storage Analytics programově pomocí rozhraní REST API nebo knihovny klienta. Pomocí operace nastavit vlastnosti služby tak, aby Storage Analytics samostatně pro každou službu.

Podrobné informace týkající se použití analytika úložiště a dalších nástrojů pro identifikovat, diagnostikovat a řešit problémy související s Azure Storage najdete v tématu [monitorování, Diagnostika a řešení Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Další informace o integraci služby Azure Active Directory a jak jej povolit, najdete v článku [povolení a konfigurace úložiště analýzy](https://docs.microsoft.com/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics?redirectedfrom=MSDN).

## <a name="monitoring-services"></a>Monitorování služeb
Cloudové aplikace jsou komplexní s mnoha přesunutí částmi. Monitorování poskytuje data a ujistěte se, že vaše aplikace zůstává nahoru a spuštěna v dobrém stavu. Také pomáhá stave vypnout potenciální problémy nebo vyřešit potíže s uplynulou těch, které jsou. Kromě toho můžete data monitorování a získáte přehled o hloubkové o vaší aplikaci. Dané znalosti můžete dozvíte, jak zlepšit výkon aplikace nebo udržovatelnosti nebo automatizaci akcí, které by jinak vyžadují ruční zásah.

### <a name="monitor-azure-resources"></a>Sledování prostředků Azure
[Azure monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) je služba platformy, která poskytuje jednoho zdroje pro monitorování prostředků Azure. Prostřednictvím služby Azure Monitor můžete vizualizovat metriky a protokoly pocházející z prostředků v Azure, zadávat na ně dotazy, směrovat je, archivovat je a provádět s nimi příslušné akce. S těmito daty můžete pracovat prostřednictvím okna portálu Monitor, [rutin prostředí PowerShell pro monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples), [rozhraní příkazového řádku pro různé platformy](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples) nebo [rozhraní API REST služby Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx).

### <a name="enable-autoscale-with-azure-monitor"></a>Povolit automatické škálování s Azure monitorování
Povolit [Azure monitorování škálování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started) se vztahuje pouze na sady škálování virtuálního počítače (VMSS), cloudové služby, aplikace služby plány a prostředí app service.

### <a name="manage-roles-permissions-and-security"></a>Správa rolí zabezpečení a oprávnění
Výhradně muset mnoha týmy [regulovat přístup k monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security) data a nastavení. Například pokud máte členové týmu, kteří pracují výhradně na monitorování (pracovníci technické podpory, technici devops) nebo pokud používáte poskytovatel spravované služby, můžete jim udělit přístup k datům monitorování pouze při omezení jejich schopnost vytvářet, upravovat, nebo Odstraňte prostředky.

To ukazuje, jak rychle použít předdefinovaná role RBAC monitorování na uživatele v Azure nebo vytvořit vlastní vlastní role pro uživatele, který potřebuje monitorování omezenými oprávněními. Potom popisuje aspekty zabezpečení vašich prostředků související s monitorování Azure a jak můžete omezit přístup k datům, která obsahují.

## <a name="prevent-detect-and-respond-to-threats"></a>Zabránit, zjistit a reagovat na hrozby
Detekce hrozeb Security Center funguje tak, že automaticky shromažďování informací o zabezpečení prostředků Azure, sítě a připojených partnerských řešení. Analyzuje se tyto informace, často korelace informace z více zdrojů, k identifikaci hrozeb. U výstrah zabezpečení se ve službě Security Center stanoví priority spolu s doporučením, jak danou hrozbu vyřešit.

-   [Konfigurace zásad zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-policies) pro vaše předplatné Azure.
-   Použití [doporučení v Centru zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-recommendations) k ochraně vašich prostředků Azure.
-   Zkontrolujte a spravovat vaše aktuální [výstrahy zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts).

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) pomáhá předcházet hrozbám, rozpoznávat je a reagovat na ně a nabízí lepší přehled o zabezpečení prostředků Azure a kontrolu nad nimi. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

Security Center nabízí snadno použitelné a efektivní funkce prevence, zjišťování a reakce na ohrožení, které jsou integrované v Azure. Klíčové funkce:

-   Pochopení stav zabezpečení cloudu
-   Převzetí kontroly nad zabezpečením cloudu
-   Snadné nasazení integrovaných řešení cloudového zabezpečení
-   Rozpoznání hrozeb a rychlá reakce

### <a name="understand-cloud-security-state"></a>Pochopení stav zabezpečení cloudu
Díky službě Azure Security Center získáte přehled o stavu zabezpečení všech vašich prostředků Azure. Na první pohled ověřte, že příslušná bezpečnostní prvky jsou na místě a správně nakonfigurován a rychle identifikovat všechny prostředky, které vyžadují pozornost.

### <a name="take-control-of-cloud-security"></a>Převzetí kontroly nad zabezpečením cloudu
Definování [zásady zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-policies) pro předplatné Azure podle potřeb zabezpečení vaší společnosti cloudu podle typu aplikací nebo citlivosti dat v každém předplatném. K tomu, abyste vlastníky prostředků provedli procesem implementace požadovaných kontrolních mechanismů, použijte doporučení na základě zásad – zabezpečení cloudu se tak provede najisto.

### <a name="easily-deploy-integrated-cloud-security-solutions"></a>Snadné nasazení integrovaných řešení cloudového zabezpečení
[Povolit řešení zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) od společnosti Microsoft a jeho partnery, včetně špičkový brány firewall a antimalwaru. Používá se zjednodušené zřizování, které umožňuje nasazovat řešení zabezpečení – dokonce i změny v nastavení sítě se nakonfigurují automaticky. Události zabezpečení z partnerských řešení se automaticky shromažďují, aby je bylo možné analyzovat a generovat z nich upozornění.

### <a name="detect-threats-and-respond-fast"></a>Rozpoznání hrozeb a rychlá reakce
Udržování náskoku před aktuálními a nově vznikajícími cloudovými hrozbami vyžaduje integrovaný analytický přístup. Kombinací Microsoft globální [hrozby intelligence](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities) a znalosti, s přehledy cloudu události související se zabezpečením v nasazeních Azure, Security Center pomáhá již v rané fázi detekovat skutečné hrozby a snížil počet falešných poplachů. Výstrahy zabezpečení cloudu získáte přehled o kampaň útoku, včetně související události a zasažené prostředky a zjistíte, jak opravit problémy a rychle obnovit.

## <a name="end-to-end-scenario-based-network-monitoring"></a>Monitorování sítě na základě scénáře začátku do konce
Zákazníci vytvářet síť začátku do konce v Azure tak, že orchestruje a skládání různé jednotlivých síťovým prostředkům, například virtuální síť, ExpressRoute, aplikační bránu, nástroje pro vyrovnávání zatížení a další. Monitorování je k dispozici na všech síťových prostředků.

[Sledovací proces sítě](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) je místní služba, která umožňuje sledovat a diagnostikovat podmínky v případě sítě úrovni, do a z Azure. Diagnostika sítě a k dispozici sledovací proces sítě vizualizace nástroje vám pomůžou pochopit, diagnostikovat a získáte přehled o k síti v Azure.

### <a name="automate-remote-network-monitoring-with-packet-capture"></a>Automatizace monitorování vzdálené sítě pomocí zachytávání paketů
Při monitorování a diagnostice síťových potíží není nutné se připojit k virtuálním počítačům pomocí Network Watcheru. Aktivační událost [zachytáváním paketů](https://docs.microsoft.com/azure/network-watcher/network-watcher-alert-triggered-packet-capture) pomocí nastavení výstrah a získat přístup k informacím o výkonu v reálném čase na úrovni paketů. Když narazíte na problém, můžete ho prozkoumat podrobněji a lépe diagnostikovat.

### <a name="gain-insight-into-your-network-traffic-using-flow-logs"></a>Získejte přehled o provozu sítě pomocí protokolů toků
Sestavení lépe pochopili, váš síťový provoz vzor pomocí [skupinu zabezpečení sítě toku protokoly](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview). Informace poskytované toku protokoly umožňuje shromažďování dat pro dodržování předpisů, auditování a sledování profil zabezpečení sítě.

### <a name="diagnose-vpn-connectivity-issues"></a>Diagnostika potíží s připojením VPN
Sledovací proces sítě poskytuje schopnost [diagnostikovat nejběžnějších problémů brány sítě VPN a připojení](https://docs.microsoft.com/azure/network-watcher/network-watcher-diagnose-on-premises-connectivity). Umožní vám nejenom identifikovat problém, ale také použít podrobné protokoly, které pomáhá analyzovat podrobněji.

Další informace o tom, jak nakonfigurovat sledovací proces sítě a jak ji povolit, najdete v článku [konfigurace sledovací proces sítě](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="secure-deployment-using-proven-devops-tools"></a>Zabezpečit pomocí Principy DevOps nástrojů nasazení
Toto jsou některé seznam z Azure DevOps postupy v tomto cloudu Microsoftu prostor, který umožňuje podnikům a týmům produktivní a efektivní.

-   **Infrastruktura jako kód (IaC):** infrastruktury jako kód je sada technik a postupů, které odborníkům v oblasti IT pomohou odeberte zatížení související s den sestavení a správu modulární infrastruktury. To umožňuje IT specialisté tvorbu a udržování jejich prostředí moderní server tak, že je jako jak vývojářům tvorbu a udržování kódu aplikace. Pro Azure, máme [Azure Resource Manager]( https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) umožňuje zřídit aplikace pomocí deklarativní šablony. S jednou šablonou můžete nasadit několik služeb společně s jejich závislostmi. Stejnou šablonu můžete použít k opakovanému nasazení aplikace během každé fáze životního cyklu této aplikace.
-   **Průběžnou integraci a nasazení:** můžete nakonfigurovat na týmových projektech Visual Studio Team Services (VSTS) [automaticky sestavení a nasazení](https://www.visualstudio.com/docs/build/overview) k službě Azure web apps nebo cloudové služby. Služby VSTS automaticky nasadí binární soubory po provedení sestavení do Azure po každé změnami kódu. V rámci popsaného procesu sestavení balíčku je ekvivalentní příkaz balíčku v sadě Visual Studio a publikování kroky jsou ekvivalentní příkaz Publikovat v sadě Visual Studio.
-   **Správa verzí:** Visual Studio [řízení vydání](https://msdn.microsoft.com/library/vs/alm/release/overview) je vynikající řešení pro automatizaci více fáze nasazení a správa verzí procesu. Vytvoření spravované průběžné nasazování kanály k uvolnění rychle, snadno a často. Pomocí řízení vydání jsme mnohem můžete automatizovat proces naše verze a jsme může mít předdefinované schválení pracovních postupů. Nasadit místně a v cloudu, rozšířit a přizpůsobit podle potřeby.
-   **Monitorování výkonu aplikace:** rozpoznat problémy, řešení problémů a neustálé zlepšování aplikací. Rychle diagnostikuje problémy uvedené ve vaší živé aplikaci. Pochopte, co s ní vaši uživatelé dělají. Konfigurace je snadno řádu přidání kódu JS a položku webconfig a zobrazte výsledky v rámci minut na portálu se všechny podrobnosti. [App insights](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/) pomáhá podnikům pro rychlejší detekce problémy & nápravy problému.
-   **Spouštění testování & škálování:** problémy s výkonem jsme můžete najít v naší aplikaci ke zlepšení kvality nasazení a je třeba zajistit vaší aplikace je vždy nahoru a k dispozici pro firmy. Ujistěte se, že vaše aplikace dokáže zpracovat provoz další spuštění nebo marketingové kampaně. Spustit cloudové [spouštění testů](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) v současně s služby VSTS.

## <a name="next-steps"></a>Další postup
- Další informace o [provozního zabezpečení Azure](https://docs.microsoft.com/azure/security/azure-operational-security).
- Další informace [Operations Management Suite | Zabezpečení a dodržování předpisů](https://www.microsoft.com/cloud-platform/security-and-compliance).
- [Začínáme s Operations Management Suite zabezpečení a Audit řešení](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started).

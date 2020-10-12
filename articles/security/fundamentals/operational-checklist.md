---
title: Kontrolní seznam pro operační zabezpečení Azure | Microsoft Docs
description: Projděte si tento kontrolní seznam, abyste vašemu podniku pomohli zvážit provozní zabezpečení Azure.
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
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: bb987bb2a028ae17bb0dfcdd2f75b8fee5a6c288
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87926463"
---
# <a name="azure-operational-security-checklist"></a>Kontrolní seznam pro operační zabezpečení Azure
Nasazení aplikace v Azure je rychlé, jednoduché a nákladově efektivní. Před nasazením cloudové aplikace v produkčním prostředí je vhodné mít kontrolní seznam, který vám pomůže při vyhodnocování vaší aplikace před seznamem základních a doporučených provozních akcí zabezpečení, které je potřeba vzít v úvahu.

## <a name="introduction"></a>Úvod

Azure poskytuje sadu služeb infrastruktury, které můžete použít k nasazení svých aplikací. Provozní zabezpečení Azure odkazuje na služby, ovládací prvky a funkce, které jsou k dispozici uživatelům pro ochranu svých dat, aplikací a dalších prostředků v Microsoft Azure.

-   K získání maximálního přínosu pro cloudovou platformu doporučujeme využívat služby Azure a postupovat podle kontrolního seznamu.
-   Organizace, které investují čas a prostředky, které vyhodnotí Provozní připravenost svých aplikací, ještě před spuštěním mají mnohem větší míru spokojenosti než ty, kteří ne. Při provádění této práce může být kontrolní seznam nehodnotným mechanismem, aby bylo zajištěno, že aplikace budou vyhodnocovány konzistentně a komplexní.
-   Úroveň provozní posouzení se liší v závislosti na úrovni splatnosti cloudu organizace a vývojové fázi aplikace, potřebě dostupnosti a požadavcích na citlivost dat.

## <a name="checklist"></a>Kontrolní seznam

Tento kontrolní seznam je určený k tomu, aby podniky při nasazování sofistikovaných podnikových aplikací v Azure pokrývaly různé požadavky na provozní zabezpečení. Dá se taky použít k vytvoření zabezpečené cloudové migrace a provozní strategie pro vaši organizaci.

|Kategorie kontrolního seznamu| Description|
| ------------ | -------- |
| [<br>Role zabezpečení & řízení přístupu](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Pomocí [řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) můžete poskytnout konkrétního uživatele, který se používá k přiřazení oprávnění uživatelům, skupinám a aplikacím v určitém oboru.</li></ul> |
| [<br>Úložiště & shromažďování dat](../../storage/blobs/security-recommendations.md)|<ul><li>Pomocí zabezpečení roviny správy Zabezpečte svůj účet úložiště pomocí [řízení přístupu na základě role Azure (RBAC)](../../role-based-access-control/role-assignments-portal.md).</li><li>Zabezpečení roviny dat pro zabezpečení přístupu k datům pomocí [sdílených přístupových podpisů (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) a uložených zásad přístupu.</li><li>Použijte Transport-Level šifrování – pomocí protokolu HTTPS a šifrování, které používá protokol [SMB (Server Message Block Protocol) 3,0](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) pro [sdílené složky Azure](../../storage/files/storage-dotnet-how-to-use-files.md).</li><li>Pomocí [šifrování na straně klienta](../../storage/common/storage-client-side-encryption.md) Zabezpečte data, která odesíláte do účtů úložiště, pokud požadujete výhradní kontrolu šifrovacích klíčů. </li><li>Pomocí [šifrování služby Storage (SSE)](../../storage/common/storage-service-encryption.md)  automaticky Zašifrujte data v Azure Storage a [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) Zašifrujte soubory disků virtuálního počítače pro operační systém a datové disky.</li><li>K monitorování typu autorizace použijte Azure [Analýza úložiště](https://docs.microsoft.com/rest/api/storageservices/storage-analytics) . Podobně jako u Blob Storage můžete zjistit, jestli uživatelé použili sdílený přístupový podpis nebo klíče účtu úložiště.</li><li>Pro přístup k prostředkům úložiště z různých domén použijte [sdílení prostředků mezi zdroji (CORS)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) .</li></ul> |
|[<br>Zásady zabezpečení & doporučení](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Pomocí [Azure Security Center](../../security-center/security-center-install-endpoint-protection.md) nasaďte řešení koncových bodů.</li><li>Přidejte [Firewall webových aplikací (WAF)](../../application-gateway/waf-overview.md) , chcete-li zabezpečit webové aplikace.</li><li>   K zvýšení ochrany zabezpečení použijte [bránu firewall](../../sentinel/connect-data-sources.md) od partnera Microsoftu. </li><li>Použijte podrobnosti o kontaktu zabezpečení pro vaše předplatné Azure; Toto [Centrum Microsoft Security Response Center (MSRC)](https://technet.microsoft.com/security/dn528958.aspx) vás v případě zjištění, že vaše zákaznická data získala protiprávní nebo neoprávněná strana.</li></ul> |
| [<br>Správa identit & přístupu](identity-management-best-practices.md)|<ul><li>[Synchronizujte svůj místní adresář s vaším cloudovým adresářem pomocí Azure AD](../../active-directory/hybrid/whatis-hybrid-identity.md).</li><li>Pomocí [jednotného přihlašování](https://azure.microsoft.com/resources/videos/overview-of-single-sign-on/) můžete uživatelům povolit přístup k jejich SaaS aplikacím na základě účtu organizace v Azure AD.</li><li>Pomocí sestavy [aktivita registrace pro resetování hesla](../../active-directory/active-directory-passwords-reporting.md) můžete monitorovat uživatele, kteří se registrují.</li><li>Povolte službu [Multi-Factor Authentication (MFA)](../../active-directory/authentication/multi-factor-authentication.md) pro uživatele.</li><li>Vývojáři pro použití zabezpečených funkcí identity pro aplikace, jako je [Microsoft Security Development Lifecycle (SDL)](https://www.microsoft.com/download/details.aspx?id=12379).</li><li>Aktivně monitorujte podezřelé aktivity pomocí Azure AD Premiumch sestav anomálií a [funkce Azure AD Identity Protection](../../active-directory/identity-protection/overview.md).</li></ul> |
|[<br>Průběžné sledování zabezpečení](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Pomocí Malware Assessmentch [protokolů Azure monitor](../../log-analytics/log-analytics-queries.md) řešení můžete nahlásit stav antimalwarové ochrany ve vaší infrastruktuře.</li><li>Pomocí [posouzení aktualizací](../../automation/update-management/update-mgmt-overview.md) můžete určit celkovou expozici potenciálním problémům se zabezpečením a zjistit, jestli jsou tyto aktualizace pro vaše prostředí důležité.</li><li>[Identita a přístup](../../security-center/security-center-monitoring.md) poskytují přehled o uživateli. </li><ul><li>stav identity uživatele,</li><li>počet neúspěšných pokusů o přihlášení,</li><li>   účet uživatele, který byl použit během těchto pokusů, účtů, které byly uzamčeny</li> <li>účty se změněným nebo resetováním hesla </li><li>Aktuálně počet účtů, které jsou přihlášené.</li></ul></ul> |
| [<br>Možnosti detekce v Azure Security Center](../../security-center/security-center-alerts-overview.md#detect-threats)|<ul><li>Pomocí [možností detekce](../../security-center/security-center-alerts-overview.md#detect-threats)Identifikujte aktivní hrozby, které cílí na vaše Microsoft Azure prostředky.</li><li>Využijte [integrovanou analýzu hrozeb](https://blogs.msdn.microsoft.com/azuresecurity/2016/12/19/get-threat-intelligence-reports-with-azure-security-center/) , která hledá známé chybné objekty actor pomocí globální analýzy hrozeb z produktů a služeb společnosti Microsoft, [jednotky digitálních zločinů společnosti Microsoft (DCU)](https://www.microsoft.com/trustcenter/security/cybercrime), [služby Microsoft Security Response Center (MSRC)](response-center.md)a externích informačních kanálů.</li><li>Pomocí [analýzy chování](https://blogs.technet.microsoft.com/enterprisemobility/2016/06/30/ata-behavior-analysis-monitoring/) , která se používá pro známé vzorce, zjistí škodlivé chování. </li><li>Použijte [detekci anomálií](https://msdn.microsoft.com/library/azure/dn913096.aspx) , která používá statistickou profilaci k sestavení historických standardních hodnot.</li></ul> |
| [<br>Vývojářské operace (DevOps)](https://docs.microsoft.com/azure/architecture/checklist/dev-ops)|<ul><li>[Infrastruktura jako kód (IAC)](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) je postup, který umožňuje automatizaci a ověřování vytváření a rozboru sítí a virtuálních počítačů při poskytování zabezpečených a stabilních hostujících platforem aplikací.</li><li>[Průběžná integrace a nasazování](/visualstudio/containers/overview#continuous-delivery-and-continuous-integration-cicd) jednotky, průběžné slučování a testování kódu, který vede k brzkému nalezení vad. </li><li>[Release Management](https://msdn.microsoft.com/library/vs/alm/release/overview) Spravujte automatizovaná nasazení prostřednictvím každé fáze vašeho kanálu.</li><li>[Sledování výkonu](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/) aplikací, včetně produkčních prostředí pro poskytování aplikací pro stav aplikací a zákaznická využívání, tvoří hypotézy a rychle ověřují nebo kontrolují strategie.</li><li>Používání [zátěžového testování & automatické škálování](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) . v naší aplikaci můžeme najít problémy s výkonem, abychom vylepšili kvalitu nasazení a zajistili, že je naše aplikace vždycky v provozu a bude dostupná pro obchodní potřeby.</li></ul> |


## <a name="conclusion"></a>Závěr
Mnoho organizací úspěšně nasadilo a provozoval své cloudové aplikace v Azure. Uvedené kontrolní seznamy zvýrazňují několik kontrolních seznamů, které jsou nezbytné, a umožňují zvýšit pravděpodobnost úspěchu nasazení a frustrace operací bez provozu. Důrazně doporučujeme tyto provozní a strategické požadavky na vaše stávající a nové nasazení aplikací v Azure.

## <a name="next-steps"></a>Další kroky
Další informace o zabezpečení najdete v následujících článcích:

- [Návrh a provozní zabezpečení](https://www.microsoft.com/trustcenter/security/designopsecurity).
- [Azure Security Center plánování a provozu](../../security-center/security-center-planning-and-operations-guide.md).

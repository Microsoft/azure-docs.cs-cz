---
title: Kontrolní seznam provozního zabezpečení Azure| Dokumenty společnosti Microsoft
description: Tento článek obsahuje sadu kontrolního seznamu pro provozní zabezpečení Azure.
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
ms.openlocfilehash: fb940857a0f88590cb9bbbf56b9e6a791299309f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980949"
---
# <a name="azure-operational-security-checklist"></a>Kontrolní seznam provozního zabezpečení Azure
Nasazení aplikace v Azure je rychlé, snadné a nákladově efektivní. Před nasazením cloudové aplikace v produkčním prostředí užitečné mít kontrolní seznam, který vám pomůže při hodnocení vaší aplikace proti seznamu základních a doporučených akcí provozního zabezpečení, které byste měli zvážit.

## <a name="introduction"></a>Úvod

Azure poskytuje sadu služeb infrastruktury, které můžete použít k nasazení aplikací. Provozní zabezpečení Azure označuje služby, ovládací prvky a funkce, které jsou uživatelům k dispozici pro ochranu jejich dat, aplikací a dalších prostředků v Microsoft Azure.

-   Chcete-li získat maximální užitek z cloudové platformy, doporučujeme využít služby Azure a postupujte podle kontrolního seznamu.
-   Organizace, které investují čas a zdroje, které posuzují provozní připravenost svých aplikací před uvedením na trh, mají mnohem vyšší míru spokojenosti než ti, kteří je neinvestují. Při provádění této práce mohou být kontrolní seznamy neocenitelným mechanismem, který zajistí, že aplikace budou vyhodnocovány konzistentně a holisticky.
-   Úroveň provozního hodnocení se liší v závislosti na úrovni vyspělosti cloudu organizace a vývojové fázi aplikace, potřebách dostupnosti a požadavcích na citlivost dat.

## <a name="checklist"></a>Kontrolní seznam

Tento kontrolní seznam je určen k tomu, aby pomohl podnikům promyslet si různé aspekty provozního zabezpečení při nasazování sofistikovaných podnikových aplikací v Azure. Lze jej také použít k vytvoření zabezpečené migrace a strategie provozu cloudu pro vaši organizaci.

|Kategorie kontrolního seznamu| Popis|
| ------------ | -------- |
| [<br>Role zabezpečení & ovládací prvky přístupu](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Pomocí [řízení přístupu na základě rolí (RBAC)](../../role-based-access-control/role-assignments-portal.md) poskytnout specifické pro uživatele, který slouží k přiřazení oprávnění uživatelům, skupin a aplikací v určitém oboru.</li></ul> |
| [<br>Shromažďování dat & úložiště](../../storage/blobs/security-recommendations.md)|<ul><li>Pomocí zabezpečení roviny správy můžete zabezpečit účet úložiště pomocí [řízení přístupu na základě rolí (RBAC).](../../role-based-access-control/role-assignments-portal.md)</li><li>Zabezpečení datové roviny zabezpečení zabezpečení zabezpečení přístupu k datům pomocí [sdílených přístupových podpisů (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) a uložených přístupových zásad.</li><li>Použití šifrování na úrovni přenosu – použití protokolu HTTPS a šifrování používané [protokoly SMB (serverových protokolů blokování zpráv) 3.0](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) pro [sdílené složky Azure](../../storage/files/storage-dotnet-how-to-use-files.md).</li><li>Šifrování [na straně klienta](../../storage/common/storage-client-side-encryption.md) slouží k zabezpečení dat, která odesíláte na účty úložiště, když potřebujete výhradní kontrolu šifrovacích klíčů. </li><li>Pomocí [šifrování služby Storage Service (SSE)](../../storage/common/storage-service-encryption.md) můžete automaticky šifrovat data v Úložišti Azure a [Šifrování disku Azure](../azure-security-disk-encryption-overview.md) k šifrování souborů virtuálních počítačů pro operační systém a datové disky.</li><li>Ke sledování typu autorizace použijte Azure [Storage Analytics.](https://docs.microsoft.com/rest/api/storageservices/storage-analytics) Třeba u úložiště objektů Blob Storage můžete zjistit, jestli uživatelé použili sdílený přístupový podpis nebo klíče účtu úložiště.</li><li>Použití [sdílení prostředků mezi zdroji (CORS)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) pro přístup k prostředkům úložiště z různých domén.</li></ul> |
|[<br>Zásady zabezpečení & doporučení](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>K nasazení koncových center použijte [Azure Security Center.](../../security-center/security-center-install-endpoint-protection.md)</li><li>Přidejte [bránu firewall webové aplikace (WAF)](../../application-gateway/waf-overview.md) pro zabezpečení webových aplikací.</li><li>   Pomocí [brány firewall](../../sentinel/connect-data-sources.md) od partnera společnosti Microsoft zvyšte ochranu zabezpečení. </li><li>Použijte bezpečnostní kontaktní údaje pro vaše předplatné Azure; Toto je [Služba Microsoft Security Response Center (MSRC),](https://technet.microsoft.com/security/dn528958.aspx) která vás kontaktuje, pokud zjistí, že k vašim zákaznickým datům byla přístupná nezákonná nebo neoprávněná strana.</li></ul> |
| [<br>Správa přístupu & identity](identity-management-best-practices.md)|<ul><li>[Synchronizujte místní adresář s cloudovým adresářem pomocí Služby Azure AD](../../active-directory/hybrid/whatis-hybrid-identity.md).</li><li>Pomocí [jednotného přihlašování](https://azure.microsoft.com/resources/videos/overview-of-single-sign-on/) umožníte uživatelům přístup k jejich aplikacím SaaS na základě jejich účtu organizace ve službě Azure AD.</li><li>Pomocí sestavy [Aktivita registrace obnovení hesla](../../active-directory/active-directory-passwords-reporting.md) můžete sledovat uživatele, kteří se registrují.</li><li>Povolte [vícefaktorové ověřování (MFA)](../../active-directory/authentication/multi-factor-authentication.md) pro uživatele.</li><li>Vývojáři používají funkce zabezpečené identity pro aplikace, jako [je Microsoft Security Development Lifecycle (SDL)](https://www.microsoft.com/download/details.aspx?id=12379).</li><li>Aktivně sledovat podezřelé aktivity pomocí Azure AD Premium sestavy anomálií a [Azure AD možnost ochrany identity](../../active-directory/identity-protection/overview.md).</li></ul> |
|[<br>Průběžné sledování zabezpečení](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Pomocí [protokolů](../../log-analytics/log-analytics-queries.md) Řešení pro vyhodnocení malwaru Azure Monitor můžete podávat zprávy o stavu antimalwarové ochrany ve vaší infrastruktuře.</li><li>Pomocí [vyhodnocení aktualizace](../../automation/automation-update-management.md) určete celkovou expozici potenciálním problémům se zabezpečením a zda nebo jak důležité jsou tyto aktualizace pro vaše prostředí.</li><li>[Identita a přístup](../../security-center/security-center-monitoring.md) vám poskytují přehled o uživatelích </li><ul><li>stav identity uživatele,</li><li>počet neúspěšných pokusů o přihlášení,</li><li> uživatelský účet, který byl použit během těchto pokusů, účty, které byly uzamčeny</li> <li>účty se změněným nebo resetovacím heslem </li><li>Aktuálně počet přihlášených účtů.</li></ul></ul> |
| [<br>Funkce detekce Azure Security Center](../../security-center/security-center-alerts-overview.md#detect-threats)|<ul><li>Pomocí [možností detekce](../../security-center/security-center-alerts-overview.md#detect-threats)můžete identifikovat aktivní hrozby zaměřené na vaše prostředky Microsoft Azure.</li><li>Pomocí [integrované analýzy hrozeb,](https://blogs.msdn.microsoft.com/azuresecurity/2016/12/19/get-threat-intelligence-reports-with-azure-security-center/) která hledá známé špatné aktéry, můžete využít globální analýzu hrozeb z produktů a služeb společnosti Microsoft, [jednotky Microsoft Digital Crimes Unit (DCU),](https://www.microsoft.com/trustcenter/security/cybercrime) [Centra microsoft security response center (MSRC)](response-center.md)a externích informačních kanálů.</li><li>Pomocí [behaviorální analýzy,](https://blogs.technet.microsoft.com/enterprisemobility/2016/06/30/ata-behavior-analysis-monitoring/) která používá známé vzory ke zjištění škodlivého chování. </li><li>Pomocí [detekce anomálií,](https://msdn.microsoft.com/library/azure/dn913096.aspx) která používá statistické profilování k vytvoření historického směrného plánu.</li></ul> |
| [<br>Vývojářské operace (DevOps)](https://docs.microsoft.com/azure/architecture/checklist/dev-ops)|<ul><li>[Infrastruktura jako kód (IaC)](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) je praxe, která umožňuje automatizaci a validaci vytváření a stržení sítí a virtuálních počítačů, které pomáhají s poskytováním bezpečných a stabilních platforem pro hostování aplikací.</li><li>[Průběžná integrace a nasazení](/visualstudio/containers/overview#continuous-delivery-and-continuous-integration-cicd) řídit probíhající slučování a testování kódu, což vede k včasnému zjištění vad. </li><li>[Správa verzí](https://msdn.microsoft.com/library/vs/alm/release/overview) Spravujte automatizovaná nasazení v každé fázi kanálu.</li><li>[Monitorování výkonu aplikací](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/) spuštěných aplikací včetně produkčních prostředí pro stav aplikací a využití zákazníků pomáhá organizacím vytvořit hypotézu a rychle ověřit nebo vyvrátit strategie.</li><li>Pomocí [zátěžového testování & automatickéškálování](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) můžeme v naší aplikaci najít problémy s výkonem, abychom zlepšili kvalitu nasazení a ujistili se, že naše aplikace je vždy nahoru nebo dostupná, aby vyhovovala obchodním potřebám.</li></ul> |


## <a name="conclusion"></a>Závěr
Mnoho organizací úspěšně nasadilo a provozovalo své cloudové aplikace v Azure. Kontrolní seznamy, které jsou k dispozici, zvýrazní několik kontrolních seznamů, které jsou nezbytné a pomáhají vám zvýšit pravděpodobnost úspěšného nasazení a operací bez frustrace. Důrazně doporučujeme tyto provozní a strategické aspekty pro vaše stávající a nové aplikace nasazení v Azure.

## <a name="next-steps"></a>Další kroky
Další informace o zabezpečení najdete v následujících článcích:

- [Návrh a provozní bezpečnost](https://www.microsoft.com/trustcenter/security/designopsecurity).
- [Plánování a provoz Centra zabezpečení Azure](../../security-center/security-center-planning-and-operations-guide.md).

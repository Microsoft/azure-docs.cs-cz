---
title: Azure identit a přístupu osvědčené postupy zabezpečení | Microsoft Docs
description: Tento článek obsahuje sadu osvědčené postupy pro správu identit a přístupu k řízení pomocí součástí možnosti Azure.
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2017
ms.author: yurid
ms.openlocfilehash: 2b77167992704bd342d67dc03b66af1e55955532
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Osvědčené postupy zabezpečení řízení Azure správu identit a přístupu
Mnoho zvažte identity jako novou vrstvu hranice zabezpečení, převzetí této role z perspektivy tradiční zaměřené na síti. Tento vývoj primární pivot pro zabezpečení pozornost a investice do pocházet z skutečnost, že se staly stále porézní hranice sítě a že hraniční obrana nemůže být co nejúčinnější se jednou byly před rozbalení [BYOD](http://aka.ms/byodcg) zařízení a cloudových aplikací.

V tomto článku probereme kolekce správy identit Azure a osvědčené postupy pro zabezpečení řízení přístupu. Tyto doporučené postupy jsou odvozeny od našich zkušeností s [Azure AD](../active-directory/active-directory-whatis.md) a prostředí zákazníků, jako sami.

Pro každý osvědčený postup objasníme:

* Co je osvědčeným postupem
* Proč chcete povolit tento osvědčený postup
* Pokud se nepodaří povolit osvědčený postup, co může být výsledkem
* Možné alternativy doporučené postupy
* Jak můžete informace o povolení osvědčený postup

Tento správy identit Azure a zabezpečení řízení přístupu, článek o osvědčených postupech je založen na konsenzu stanovisko a možnostech platformy Azure a sady funkcí, které jsou v době byla zapsána v tomto článku. Názory a technologie v průběhu času mění a v tomto článku budeme je aktualizovat v pravidelných intervalech, aby odrážela tyto změny.

Identit Azure správy a přístupu k řízení osvědčené postupy zabezpečení popsané v tomto článku patří:

* Centralizovat správu vaší identity
* Povolit jednotné přihlašování (SSO)
* Nasazení správy hesel
* Vynutit ověřování Multi-Factor authentication (MFA) pro uživatele
* Pomocí řízení přístupu na základě role (RBAC)
* Řízení umístění, kde jsou prostředky vytvořené pomocí Resource Manager
* Příručka vývojáře využívat funkce identit pro aplikace SaaS
* Aktivně sledovat pro podezřelé aktivity

## <a name="centralize-your-identity-management"></a>Centralizovat správu vaší identity
Jeden důležitý krok k zabezpečení vaší identity je zajistit, že může oddělení IT spravovat účty z jednoho umístění jednoho týkající se kde tento účet vytvořil. I když většina podniků IT organizace má primární účet adresář místní, hybridního cloudu nebo nasazení jsou na vzestupu a je důležité, aby vám pochopit, jak integrovat místní a cloudové adresáře a poskytují plynulého prostředí koncového uživatele.

K tomu [hybridní identita](../active-directory/active-directory-hybrid-identity-design-considerations-overview.md) scénáři doporučujeme dvě možnosti:

* Synchronizace místního adresáře s adresářem cloudu pomocí služby Azure AD Connect
* Povolit jednotné přihlašování s [synchronizaci hodnoty hash hesla](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization), [předávací ověřování](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq) nebo vytvořit federaci vaší identity na místě s directory cloudu s použitím [služby Active Directory Federační služby](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/deployment/deploying-federation-servers) (AD FS)

Organizace, které se nepodaří jejich Cloudová identita integrovat svou identitu místní prostředí vyšší režijní náklady na správu při správě účtů, což zvyšuje pravděpodobnost chyb a porušení zabezpečení.

Další informace o synchronizace Azure AD, najdete v článku [integrace místních identit s Azure Active Directory](../active-directory/active-directory-aadconnect.md).

## <a name="enable-single-sign-on-sso"></a>Povolit jednotné přihlašování (SSO)
Pokud máte několik adresářů ke správě, to se stane problémem správy nejen pro oddělení IT, ale i pro koncové uživatele, které je nutné pamatovat více hesel. Pomocí [jednotného přihlašování k](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) poskytovat uživatelům možnost používat stejnou sadu přihlašovacích údajů přihlásit a přístup k prostředkům, které potřebují, bez ohledu na to tam, kde tento prostředek je nacházejí na místních nebo v cloudu.

Povolit uživatelům přístup k pomocí jednotného přihlašování k jejich [aplikace SaaS](../active-directory/active-directory-appssoaccess-whatis.md) podle jejich organizace účtu ve službě Azure AD. Tento krok platí nejen pro aplikace Microsoft SaaS, ale také další aplikace, jako například [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) a [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md). Aplikace může být nakonfigurován k používání Azure AD jako [identity na základě SAML](../active-directory/fundamentals-identity.md) zprostředkovatele. Jako ovládací prvek zabezpečení Azure AD nevydá token, což jim umožní přihlášení do aplikace, pokud mají udělené přístup pomocí služby Azure AD. Můžete udělit přístup přímo nebo prostřednictvím skupiny, že jsou členem.

> [!NOTE]
> rozhodnutí ohledně přes jednotné přihlašování, bude mít vliv jak integraci místního adresáře s adresářem v cloudu. Pokud chcete jednotné přihlašování, budete muset použít federace, protože synchronizace adresářů se poskytují jenom [stejné prostředí pro přihlašování](../active-directory/active-directory-aadconnect.md).
>
>

Organizace, které nebudou vynucovat jednotného přihlašování pro svoje uživatele a aplikace se více zveřejňují pro scénáře, které uživatelé mají více hesel, která přímo zvyšuje pravděpodobnost uživatelé opakovaného použití hesla nebo pomocí vytváření silných hesel.

Další informace o Azure AD SSO přečíst v článku [správu služby AD FS a vlastního nastavení službou Azure AD Connect](../active-directory/active-directory-aadconnect-federation-management.md).

## <a name="deploy-password-management"></a>Nasazení správy hesel
Ve scénářích, kdy máte víc klientů nebo chcete povolit uživatelům [resetovat vlastní heslo](../active-directory/active-directory-passwords-update-your-own-password.md), je důležité, aby se zabránilo zneužití použít vhodná bezpečnostní zásady. V Azure můžete využít funkce resetování hesla pomocí samoobslužné služby a přizpůsobit možnosti zabezpečení, aby splňovaly vaše podnikové požadavky.

Je důležité získat zpětnou vazbu od těchto uživatelů a dozvědět se od jejich prostředí při pokusu o provedení těchto kroků. Podle toho, tyto činnosti, propracovanější plán pro zmírnění potenciální problémy, které mohou nastat při nasazení pro skupinu větší. Dále je doporučeno používat [sestava aktivit registrace resetování hesla](../active-directory/active-directory-passwords-get-insights.md) ke sledování uživatelů, kteří jsou registrace.

Organizace, které chcete vyhnout volání podpory změnit heslo, ale povolit uživatelům resetovat vlastní hesla budou náchylnější k vyšší svazku volání na technickou podporu kvůli problémům s heslem. V organizacích, které mají víc klientů je nutné implementovat tuto možnost a povolte uživatelům provádět v rámci hranice zabezpečení, které byly vytvořeny v zásadě zabezpečení resetování hesla.

Další informace o resetování přečíst článek hesla [nasazení správy hesel a vyškolení uživatelů, můžete použít](../active-directory/authentication/howto-sspr-deployment.md).

## <a name="enforce-multi-factor-authentication-mfa-for-users"></a>Vynutit ověřování Multi-Factor authentication (MFA) pro uživatele
Pro organizace, které musí být kompatibilní s oborové standardy, jako například [PCI DSS verze 3.2](http://blog.pcisecuritystandards.org/preparing-for-pci-dss-32), službu Multi-Factor authentication je musí mít možnost pro ověřování uživatelů. Kromě dodržení předpisů oborovým standardům, vynucování vícefaktorového ověřování k ověření uživatelů také pomáhá organizacím zmírnit typ krádeže přihlašovacích údajů útoku, jako například [Pass-the-Hash (PtH)](http://aka.ms/PtHPaper).

Povolením Azure MFA pro uživatele přidáte druhou vrstvu zabezpečení uživatelská přihlášení a transakce. V takovém případě transakci může získávat přístup k dokumentu umístěná na souborovém serveru nebo ve vaší službě SharePoint Online. Azure MFA také pomáhá IT, které sníží pravděpodobnost, že ohrožené přihlašovacích údajů má přístup k datům společnosti.

Příklad: můžete vynutit vícefaktorové ověřování Azure pro vaše uživatele a bude sloužit jako ověření telefonního hovoru nebo textové zprávy. Pokud dojde k ohrožení přihlašovacích údajů uživatele, není útočník přístup k jakémukoli prostředku, protože nemají přístup k telefonu uživatele. Organizace, které Nepřidávejte další vrstvy ochrany identit budou náchylnější pro útoku krádeží přihlašovacích údajů, což může vést k ohrožení zabezpečení dat.

Jeden alternativou pro organizace, které chcete zachovat celý proces ověřování řízení místní je použít [Azure Multi-Factor Authentication Server](../multi-factor-authentication/multi-factor-authentication-get-started-server.md), označované taky jako MFA na místě. Pomocí této metody přesto bude možné vynutit ověřování Multi-Factor authentication, a zajistit přitom ochranu MFA server místní.

Další informace o Azure MFA, najdete v článku [Začínáme s Azure Multi-Factor Authentication v cloudu](../active-directory/authentication/howto-mfa-getstarted.md).

## <a name="use-role-based-access-control-rbac"></a>Pomocí řízení přístupu na základě role (RBAC)
Omezení přístupu na základě [potřebovat znát](https://en.wikipedia.org/wiki/Need_to_know) a [nejnižší oprávnění](https://en.wikipedia.org/wiki/Principle_of_least_privilege) Principy zabezpečení je nutné pro organizace, které chcete vynutit zásady zabezpečení pro přístup k datům. Azure na základě rolí řízení přístupu (RBAC) slouží k přiřazení oprávnění pro uživatele, skupiny a aplikace v určité oboru. Předplatné, skupinu prostředků nebo jediný zdroj, může být oboru přiřazení role.

Můžete využít [součástí RBAC](../role-based-access-control/built-in-roles.md) role v Azure přiřadit oprávnění pro uživatele. Zvažte použití *Přispěvatel účet úložiště* pro operátorům cloudu, kteří potřebují spravovat účty úložiště a *Classic Přispěvatel účet úložiště* rolí ke správě klasické účty úložiště. Operátoři cloudu, které potřebuje ke správě virtuálních počítačů a účet úložiště, zvažte přidá do *Přispěvatel virtuálních počítačů* role.

Organizace, které nebudou vynucovat řízení přístupu dat s využitím funkcí, jako je RBAC může poskytnutí další oprávnění, než je nutné uživatelům. To může vést k datům ohrožení zabezpečení podle povolit uživatelům přístup k určité typy dat (například vysoký obchodní dopad), který by neměl mít na prvním místě.

Další informace o Azure RBAC přečíst v článku [řízení přístupu](../role-based-access-control/role-assignments-portal.md).

## <a name="control-locations-where-resources-are-created-using-resource-manager"></a>Řízení umístění, kde jsou prostředky vytvořené pomocí Resource Manager
Povolení operátorům cloudu úkoly při brání nejnovější konvence, které jsou potřebné ke správě prostředků vaší organizace je důležité. Organizace, které chcete mít pod kontrolou umístění, kde jsou vytvářeny prostředky měli pevného kódu těchto umístění.

Organizace mohou dosáhnout, vytvářet zásady zabezpečení, které mají definice, které popisují akce nebo prostředky, které jsou zamítnuty. Definice těchto zásad na požadovaný rozsah, jako je například předplatné, skupinu prostředků nebo prostředek jednotlivých přiřazujete.

> [!NOTE]
> Toto není stejný jako RBAC, ve skutečnosti využívá RBAC k ověřování uživatelů, kteří mají oprávnění k vytváření těchto prostředků.
>
>

Využívání [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) vytvořit vlastní zásady také pro scénáře, kde organizace chce umožnit operace jenom v případě, že odpovídající nákladové středisko je přidružené; v opačném případě, že daný požadavek je odepřen.

Organizace, které nejsou řízení vytváření prostředky budou náchylnější k uživatele, kteří mohou zneužití službu tak, že vytvoříte více prostředků, než potřebují. Posílení zabezpečení procesu vytváření prostředků je důležitým krokem při zabezpečené víceklientské scénář.

Další informace o vytváření zásad s Azure Resource Manager přečíst v článku [co je Azure zásad?](../azure-policy/azure-policy-introduction.md)

## <a name="guide-developers-to-leverage-identity-capabilities-for-saas-apps"></a>Příručka vývojáře využívat funkce identit pro aplikace SaaS
Identita uživatele se využívají záznamy v řadě scénářů, když uživatelé přistupují k [aplikace SaaS](https://azure.microsoft.com/marketplace/active-directory/all/) , lze integrovat s místní nebo cloudové adresáře. Především, doporučujeme, aby vývojáři použít k vývoji těchto aplikací, jako například zabezpečené metodika [Microsoft životního cyklu SDL (Security Development)](https://www.microsoft.com/sdl/default.aspx). Azure AD zjednodušuje ověřování pro vývojáře a poskytovat identity jako služby, podpora pro standardní protokoly, jako [OAuth 2.0](http://oauth.net/2/) a [OpenID Connect](http://openid.net/connect/)také jako open source knihovny pro různé platformy.

Zajistěte, aby k registraci jakékoli aplikace, která outsources ověřování do služby Azure AD, toto je povinné procedury. Z důvodu za to je proto Azure AD potřebuje ke koordinaci komunikaci s aplikací při zpracování přihlašování (SSO) nebo výměna tokeny. Relace uživatele vyprší po vypršení platnosti tokenem vydaným službou Azure AD. Vždy vyhodnoceny, pokud vaše aplikace by měla používat této doby nebo jestli můžete snížit této doby. Snižuje dobu životnosti může fungovat jako bezpečnostní opatření, která vynutí uživatelům přihlásit na základě na období nečinnosti.

Organizace, které nebudou vynucovat řízení identitu pro přístup k aplikacím a není průvodce svého vývojáře o tom, jak bezpečně integraci aplikace s jejich systém správy identit může být náchylnější k krádež typ útoku, přihlašovacích údajů, jako [slabé ověřování a relace správy popsané v aplikaci otevřít webový projekt zabezpečení (OWASP) Top 10](https://www.owasp.org/index.php/OWASP_Top_Ten_Cheat_Sheet).

Další informace o ověřování scénáře pro aplikace SaaS načtením [scénáře ověřování pro Azure AD](../active-directory/active-directory-authentication-scenarios.md).

## <a name="actively-monitor-for-suspicious-activities"></a>Aktivně sledovat pro podezřelé aktivity
Podle [Verizon 2016 Data porušení sestavy](http://www.verizonenterprise.com/verizon-insights-lab/dbir/2016/), zneužití přihlašovacích údajů je stále v rostoucím a jeden z nejvíce ziskové podnikům stane pro zločinci. Z tohoto důvodu je důležité, aby byly aktivní identitu sledování systému v místě, ke kterému můžete rychle zjistit aktivity podezřelého chování a aktivaci výstrahy pro další šetření. Azure AD má dvě hlavní funkce, které pomůžou organizacím monitorovat jejich identit: Azure AD Premium [anomálií sestavy](../active-directory/active-directory-view-access-usage-reports.md) a Azure AD [ochrany identit](../active-directory/active-directory-identityprotection.md) schopností.

Ujistěte se, pokud chcete používat sestavy anomálií k identifikaci pokusy o přihlášení [bez trasovány](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md), [hrubou silou](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md) proti určitého účtu před útoky, pokusí se přihlásit z více míst, přihlášení z [ nakažených zařízení a podezřelé IP adresy. Uvědomte si, že jsou sestavy. Jinými slovy musí mít procesy a postupy zavedené pro správce IT spustit tyto sestavy každý den nebo na vyžádání (obvykle ve scénáři s reakcí na incidenty).

Na rozdíl od ochrany identit Azure AD je aktivní sledování systém a označuje aktuální rizika na svůj vlastní řídicí panel. Kromě toho, že se také zobrazí denní souhrn oznámení e-mailem. Doporučujeme vám, že můžete upravit úroveň rizika podle vašich obchodních požadavků. Úroveň rizika pro událost riziko je označením závažnost události riziko (vysoká, střední nebo nízká). Úroveň rizika pomáhá uživatelům Identity Protection prioritu akce, které musíte provést pro snížení rizika pro jejich organizaci.

Organizace, které aktivně nemonitorujte jejich systémy identity je riziko toho, že dojde k ohrožení přihlašovací údaje uživatele. Bez znalostní báze, který trvá podezřelé aktivity umístit pomocí těchto přihlašovacích údajů, organizace nebude možné zmírnit tohoto typu ohrožení.
Další informace o ochrany identit Azure načtením [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md).

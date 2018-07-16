---
title: Identita a přístup osvědčené postupy zabezpečení Azure | Dokumentace Microsoftu
description: Tento článek poskytuje sadu osvědčených postupů pro správu identit a přístupu k řízení pomocí integrované možnosti Azure.
services: security
documentationcenter: na
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 210781b36f6215afc925266e597031d772a94002
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39060555"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Azure správu identit a řízení přístupu osvědčené postupy zabezpečení

Mnoho vezměte v úvahu identity jako novou vrstvu hranici pro zabezpečení, trvá déle než tuto roli z hlediska tradiční zaměřené na síti. Tomuto vývoji primární pivot pro zabezpečení pozornost a investic pocházejí z skutečnost, že se stává stále porézní perimetry sítě a této hraniční obrana nemůže být co nejúčinnější, jednou byly před obrovské množství nových [modelu BYOD ](http://aka.ms/byodcg) zařízení a cloudové aplikace.

V tomto článku se podíváme na kolekci správy identit Azure a osvědčené postupy pro zabezpečení řízení přístupu. Tyto osvědčené postupy, které jsou odvozeny z našich zkušenostech s [Azure AD](../active-directory/fundamentals/active-directory-whatis.md) a prostředí zákazníků, jako je sami.

Pro každý osvědčeným postupem je vám vysvětlíme:

* Jaký je doporučený postup
* Proč chcete povolit tento osvědčený postup
* Pokud chcete povolit osvědčený postup, co mohou být způsobeny
* Je to možné alternativy doporučené postupy
* Jak lze zjistíte, jak povolit osvědčený postup

Tento správy identit Azure a zabezpečení řízení přístupu, článek o osvědčených postupech je založena na názor shody a možnostech platformy Azure a sady funkcí, protože existují v době byl napsán v tomto článku. Názory a technologie v průběhu času měnit a tento článek bude aktualizován v pravidelných intervalech, aby tyto změny projevily.

Identita Azure správu nebo přístupová ovládací prvek osvědčené postupy zabezpečení popsané v tomto článku zahrnují:

* Centralizujte si správu vaší identity
* Povolit jednotné přihlašování (SSO)
* Správa nasazení hesel
* Vynutit ověřování Multi-Factor Authentication (MFA) pro uživatele
* Použití řízení přístupu na základě rolí (RBAC)
* Řízení umístění, kde se prostředky vytvoří pomocí Resource Manageru
* Příručka pro vývojáře využívat možnosti identit pro SaaS aplikace
* Aktivně monitorujte pro podezřelé aktivity

## <a name="centralize-your-identity-management"></a>Centralizujte si správu vaší identity

Jeden důležitý krok k zabezpečení vaší identity je zajistit, že může oddělení IT spravovat účty z jednoho místa týkající se kde se tento účet vytvořil. I když většina podniků IT organizace má svého primárního účtu místní adresáře, hybridní cloudové nasazení jsou na vzestupu a je důležité pochopit, jak integrace místních a cloudových adresářích a poskytují zajistí bezproblémové prostředí pro koncového uživatele.

K tomu [hybridní identita](../active-directory/active-directory-hybrid-identity-design-considerations-overview.md) scénáři doporučujeme dvě možnosti:

* Synchronizaci vašeho místního adresáře s adresářem cloudu pomocí služby Azure AD Connect
* Povolit jednotné přihlašování s [synchronizaci hodnot hash hesel](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization), [předávací ověřování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq) nebo Federovat vaší místní identity s pomocí cloudu adresář [služby Active Directory Federation Services](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers) (AD FS)

Organizace, které se nepodařilo integrovat svoje místní identity s jejich Cloudová identita prostředí zvýšenou administrativní režii při správě účtů, které zvyšují pravděpodobnost chyb a porušení zabezpečení.

Další informace o synchronizaci Azure AD, najdete v článku [integrace místních identit s Azure Active Directory](../active-directory/active-directory-aadconnect.md).

## <a name="enable-single-sign-on-sso"></a>Povolit jednotné přihlašování (SSO)

Až budete mít ke správě více adresářů, toto řešení pro správu problém není pouze pro oddělení IT, ale také pro koncové uživatele, které je nutné pamatovat více hesel. S použitím [jednotného přihlašování](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) poskytovat uživatelům možnost používat stejnou sadu přihlašovacích údajů přihlásit a získat přístup k prostředkům, které potřebují, bez ohledu na to tam, kde tento prostředek je umístěný v místním nebo v cloudu.

Používat jednotné přihlašování umožňuje uživatelům přístup k jejich [aplikací SaaS](../active-directory/manage-apps/what-is-single-sign-on.md) podle jejich účet organizace ve službě Azure AD. To platí nejen pro aplikace SaaS společnosti Microsoft, ale také další aplikace, jako například [Google Apps](../active-directory/saas-apps/google-apps-tutorial.md) a [Salesforce](../active-directory/saas-apps/salesforce-tutorial.md). Aplikace lze nastavit používání Azure AD jako [identity založené na SAML](../active-directory/fundamentals-identity.md) zprostředkovatele. Jako ovládací prvek zabezpečení Azure AD nevydá token, že jim umožníte přihlásit do aplikace, není-li, kterým byl udělen přístup k používání služby Azure AD. Můžete udělit přístup přímo nebo prostřednictvím skupiny, že jsou členem.

> [!NOTE]
> rozhodnutí o použití jednotného přihlašování ovlivní, jak integrace v místním adresáři cloudového adresáře. Pokud chcete jednotného přihlašování, budete muset použít federace, protože synchronizace adresářů pouze poskytne [stejné přihlašování](../active-directory/active-directory-aadconnect.md).
>
>

Organizace, které Nevynucovat jednotného přihlašování pro svoje uživatele a aplikace jsou více přístupné pro scénáře, kdy uživatelé mají více hesel, které přímo zvyšuje pravděpodobnost, že uživatelé opětovné použití hesla nebo používání Slabá hesla.

Další informace o jednotné přihlašování služby Azure AD najdete v článku [Správa služby AD FS a vlastního nastavení službou Azure AD Connect](../active-directory/active-directory-aadconnect-federation-management.md).

## <a name="deploy-password-management"></a>Správa nasazení hesel

Ve scénářích, kde máte více tenantů nebo chcete uživatelům umožnit [resetovat vlastní heslo](../active-directory/user-help/active-directory-passwords-update-your-own-password.md), je důležité použít vhodná bezpečnostní zásady zamezilo. V Azure můžete využít funkci resetování hesla pomocí samoobslužné služby a přizpůsobit možnosti zabezpečení podle požadavků vaší firmy.

Je důležité získat zpětnou vazbu od těchto uživatelů a Učte se od svých zkušenostech se pokusí o provedení těchto kroků. Podle těchto možností, vypracovat plán pro zmírnění potenciální problémy, které mohou nastat při nasazení pro skupinu větší. Doporučuje se také, že používáte [sestava aktivit registrace resetování hesla](../active-directory/active-directory-passwords-get-insights.md) sledovat uživatele, kteří se registrace.

Organizace, které chcete vyhnout volání podpory změnit heslo, ale umožnit uživatelům resetování vlastních hesel, budou náchylnější k vyšší objemy volání do oddělení služeb kvůli problémům s heslem. V organizacích, které máte více tenantů je nutné, aby implementaci tohoto typu funkce a umožňují uživatelům provádět v rámci hranice zabezpečení, které byly vytvořeny v zásadách zabezpečení pro vytvoření nového hesla.

Další informace o resetování hesla prostřednictvím přečtení tohoto článku [nasazení správy hesel a vyškolení uživatelů, jeho použití](../active-directory/authentication/howto-sspr-deployment.md).

## <a name="enforce-multi-factor-authentication-mfa-for-users"></a>Vynutit ověřování Multi-Factor Authentication (MFA) pro uživatele

Pro organizace, které musí být v souladu se standardy, jako například [PCI DSS verze 3.2](http://blog.pcisecuritystandards.org/preparing-for-pci-dss-32), je ověřování službou Multi-Factor Authentication musí mít možnost pro ověřování uživatelů. Kromě toho splňující oborové standardy, vynucování vícefaktorového ověřování k ověření uživatelů může také pomoct organizacím zmírnit typ krádeže přihlašovacích údajů útoku, třeba [Pass-the-Hash (PtH)](http://aka.ms/PtHPaper).

Tím, že Azure MFA pro uživatele, který přidáváte druhou vrstvu zabezpečení do uživatelská přihlášení a transakce. V takovém případě transakce může být přístup k dokumentu umístěné na souborovém serveru nebo v Sharepointu Online. Azure MFA také pomáhá snížit pravděpodobnost, že ohrožení zabezpečení přihlašovacích údajů má přístup k datům organizace IT oddělení.

Příklad: Vynutit Azure MFA pro uživatele a nakonfigurujte ho na použití jako ověřovací prostřednictvím telefonního hovoru nebo textové zprávy. Pokud dojde k ohrožení přihlašovacích údajů uživatele, není útočník přístup k jakémukoli prostředku, protože nemají přístup k telefonu uživatele. Organizace, které nepřidáte další vrstvy ochrany identit budou náchylnější k útoku krádeží přihlašovacích údajů, což může vést k ohrožení dat.

Jeden možností pro organizace, které chcete zachovat ověřování celý ovládací prvek místní [Azure Multi-Factor Authentication Server](../active-directory/authentication/howto-mfaserver-deploy.md), také nazývané MFA v místním prostředí. Pomocí této metody bude stále budete moci vynutit ověřování Multi-Factor Authentication, a zajistit přitom ochranu MFA serveru v místním prostředí.

Další informace o Azure MFA, najdete v článku [Začínáme se službou Azure Multi-Factor Authentication v cloudu](../active-directory/authentication/howto-mfa-getstarted.md).

## <a name="use-role-based-access-control-rbac"></a>Použití řízení přístupu na základě rolí (RBAC)

Omezení přístupu na základě [znát](https://en.wikipedia.org/wiki/Need_to_know) a [nejnižší možná oprávnění](https://en.wikipedia.org/wiki/Principle_of_least_privilege) principů zabezpečení je nutné pro organizace, které chcete vynutit zásady zabezpečení pro přístup k datům. Azure na základě rolí řízení přístupu (RBAC) slouží k přiřazení oprávnění pro uživatele, skupiny nebo aplikace v určitém rozsahu. Obor přiřazení role může být předplatné, skupinu prostředků nebo jediný prostředek.

Můžete využít [integrovaným RBAC](../role-based-access-control/built-in-roles.md) role v Azure a přiřadit oprávnění pro uživatele. Zvažte použití *Přispěvatel účtů úložiště* pro operátorům cloudu, které je potřeba spravovat účty úložiště a *Classic Přispěvatel účtů úložiště* rolí ke správě klasických účtů úložiště. U operátorům cloudu, které je potřeba spravovat virtuální počítače a účtu úložiště, zvažte jejich přidání na *Přispěvatel virtuálních počítačů* role.

Organizace, které se nebudou vynucovat řízení přístupu dat s využitím funkcí, jako je RBAC může poskytuje více oprávnění než je nezbytné na svoje uživatele. To může vést k datům ohrožení zabezpečení podle povolit uživatelům přístup k určité typy dat (například vysoký dopad na chod firmy), který by neměl mít na prvním místě.

Další informace o Azure RBAC najdete v článku [řízení přístupu](../role-based-access-control/role-assignments-portal.md).

## <a name="control-locations-where-resources-are-created-using-resource-manager"></a>Řízení umístění, kde se prostředky vytvoří pomocí Resource Manageru

Umožňuje operátorům cloudu k provádění úloh, a brání narušení smluv, které jsou potřeba ke správě prostředků vaší organizace je důležité. Organizace, které chcete mít pod kontrolou umístění, kde se prostředky vytvoří by měl kód intenzivně tato místa.

Za tím účelem organizace mohou vytvářet zásady zabezpečení, které mají definice, které popisují akce nebo prostředky, které jsou zamítnuty. Můžete přiřadit tyto definice zásad v požadovaném oboru, jako je například předplatné, skupinu prostředků nebo samostatný prostředek.

> [!NOTE]
> Toto není stejný jako RBAC, ve skutečnosti využívá RBAC k ověřování uživatelů, kteří mají oprávnění k vytvoření těchto prostředků.
>
>

Využijte [Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md) k vytvoření vlastních zásad pro scénáře, kdy organizace chce umožnit operace jenom v případě odpovídající nákladové středisko je přidružené; v opačném případě se zamítnout žádost o.

Organizace, které nejsou řízení, jak vytvořit prostředky budou náchylnější pro uživatele, kteří mohou zneužívání službu tak, že vytvoříte víc prostředků, než budou potřebovat. Posílení zabezpečení v procesu vytváření prostředků je důležitý krok k zabezpečení scénářích s více tenanty.

Další informace o vytváření zásad pomocí Azure Resource Manageru najdete v článku [co je Azure Policy?](../azure-policy/azure-policy-introduction.md)

## <a name="guide-developers-to-leverage-identity-capabilities-for-saas-apps"></a>Příručka pro vývojáře využívat možnosti identit pro SaaS aplikace

Identita uživatele se využívá v mnoha scénářích, když uživatelé přistupují k [aplikace SaaS](https://azure.microsoft.com/marketplace/active-directory/all/) , která lze integrovat s místní nebo cloudový adresář. Především doporučujeme vývojáři použít zabezpečené metodologie pro vývoj těchto aplikací, jako například [Microsoft Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx). Azure AD usnadňuje ověřování pro vývojáře tím, že poskytuje identitu jako služba, se podporují pro standardní protokoly jako je například [OAuth 2.0](http://oauth.net/2/) a [OpenID Connect](http://openid.net/connect/), stejně jako open source knihovny pro různé platformy.

Ujistěte se, že k registraci všech aplikací, outsources ověřování do služby Azure AD, toto je povinné procedury. Důvodem takového to je proto, že Azure AD potřebuje ke koordinaci komunikaci s aplikací při zpracování přihlašování (SSO) nebo výměna tokenů. Relace uživatele vyprší po vypršení platnosti tokenu vydaného službou Azure AD. Vždy vyhodnoceny, pokud vaše aplikace by měla používat tentokrát nebo jestli můžete snížit této doby. Snižuje dobu života může fungovat jako bezpečnostní opatření, která vynutí se odhlásit uživatele na základě v určité době nečinnosti.

Organizace, které nedokáže vynutit řízení identit pro přístup k aplikacím a ne Průvodce svého vývojáře o tom, jak bezpečně integrovat aplikace s jejich systémem správy identit může být náchylnější k odcizení typ útoku, přihlašovacích údajů, jako [slabé ověřování a relace správy popsaných v Open Web Application zabezpečení projektu (OWASP) prvních 10](https://www.owasp.org/index.php/OWASP_Top_Ten_Cheat_Sheet).

Další informace o scénářích ověřování pro aplikace SaaS načtením [scénáře ověřování pro službu Azure AD](../active-directory/active-directory-authentication-scenarios.md).

## <a name="actively-monitor-for-suspicious-activities"></a>Aktivně monitorujte pro podezřelé aktivity

Podle [sestavu porušení zabezpečení dat společnosti Verizon 2016](http://www.verizonenterprise.com/verizon-insights-lab/dbir/2016/), zneužití přihlašovacích údajů je stále v rostoucím a jeden z přinášejí největší zisk firmám stane pro zločinci. Z tohoto důvodu je důležité mít systém sledování aktivní identitu v místě, ke kterému můžete rychle zjišťovat podezřelé chování aktivity a aktivaci oznámení o pomoc. Azure AD má dvě hlavní možnosti, které pomáhá organizacím monitorovat jejich identit: Azure AD Premium [anomálií sestavy](../active-directory/active-directory-view-access-usage-reports.md) a Azure AD [identity protection](../active-directory/active-directory-identityprotection.md) funkce.

Ujistěte se, pokud chcete používat sestavy anomálií k identifikaci pokusy o přihlášení [bez trasovány](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md), [hrubou silou](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md) proti určitého účtu před útoky, pokusí se přihlásit z více míst, přihlášení z [ nakažených zařízení a podezřelé IP adresy. Uvědomte si, že jsou sestavy. Jinými slovy musí mít procesy a postupy v místě pro správce IT spustit tyto sestavy, každý den nebo na vyžádání (obvykle ve scénáři reakce na incidenty).

Naproti tomu Azure AD identity protection je aktivní sledování systému a označí aktuální rizika na svůj vlastní řídicí panel. Kromě toho obdržíte také denní souhrn oznámení e-mailem. Doporučujeme nastavit úroveň rizika podle požadavků vašeho podniku. Úroveň rizika pro rizikové události je indikací (vysoká, střední nebo nízká) závažnost rizikové události. Úroveň rizika pomáhá uživatelům Identity Protection stanovit priority akce, které jsou nezbytné k omezení rizika pro organizaci.

Organizace, které aktivně nemonitorujte svých systémů identit hrozí správné přihlašovací údaje uživatele, dojde k ohrožení bezpečnosti. Bez znalosti, které je možné podezřelé aktivity umístit pomocí těchto přihlašovacích údajů, organizace nebudete moct tento typ hrozbu zmírnit.
Další informace o Azure Identity protection načtením [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md).

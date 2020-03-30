---
title: Referenční příručka pro operace zásad správného řízení služby Azure Active Directory
description: Tato referenční příručka k operacím popisuje kontroly a akce, které byste měli provést k zabezpečení správy zásad správného řízení.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 4826bcdc85e0c6189c51aa262014fe154bb479b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74535454"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Referenční příručka pro operace zásad správného řízení služby Azure Active Directory

Tato část [referenční příručky pro operace Azure AD](active-directory-ops-guide-intro.md) popisuje kontroly a akce, které byste měli provést k posouzení a ověření přístupu uděleného neprivilegovaným a privilegovaným identitám, auditu a řízení změn v prostředí.

> [!NOTE]
> Tato doporučení jsou aktuální k datu publikování, ale mohou se v průběhu času měnit. Organizace by měly průběžně vyhodnocovat své postupy zásad správného řízení, jak se produkty a služby společnosti Microsoft v průběhu času vyvíjejí.

## <a name="key-operational-processes"></a>Klíčové provozní procesy

### <a name="assign-owners-to-key-tasks"></a>Přiřazení vlastníků ke klíčovým úkolům

Správa služby Azure Active Directory vyžaduje průběžné provádění klíčových provozních úkolů a procesů, které nemusí být součástí projektu zavádění. Je stále důležité nastavit tyto úkoly pro optimalizaci prostředí. Mezi klíčové úkoly a jejich doporučené vlastníky patří:

| Úkol | Vlastník |
| :- | :- |
| Archivovat protokoly auditu Azure AD v systému SIEM | Operační tým InfoSec |
| Zjišťujte aplikace, které jsou spravovány mimo dodržování předpisů | Operační tým IAM |
| Pravidelně kontrolujte přístup k žádostem | Tým architektury Infosec |
| Pravidelná kontrola přístupu k externím identitám | Tým architektury Infosec |
| Pravidelně kontrolujte, kdo má privilegované role | Tým architektury Infosec |
| Definování bran zabezpečení pro aktivaci privilegovaných rolí | Tým architektury Infosec |
| Pravidelné přezkoumávání udělení souhlasu | Tým architektury Infosec |
| Návrh katalogů a přístupových balíčků pro aplikace a prostředky založené na zaměstnancích v organizaci | Vlastníci aplikací |
| Definování zásad zabezpečení pro přiřazení uživatelů k balíčkům | Tým InfoSec + Vlastníci aplikací |
| Pokud zásady obsahují pracovní postupy schvalování, pravidelně kontrolujte schválení pracovního postupu | Vlastníci aplikací |
| Zkontrolujte výjimky v zásadách zabezpečení, jako jsou zásady podmíněného přístupu, pomocí kontrol přístupu | Operační tým InfoSec |

Při kontrole seznamu můžete zjistit, že budete muset buď přiřadit vlastníka pro úkoly, které chybí vlastník, nebo upravit vlastnictví úkolů s vlastníky, kteří nejsou v souladu s výše uvedenými doporučeními.

#### <a name="owner-recommended-reading"></a>Majitel doporučil čtení

- [Přiřazení rolí správce v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Zásady správného řízení v Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

### <a name="configuration-changes-testing"></a>Testování změn konfigurace

Existují změny, které vyžadují zvláštní a spekty při testování, od jednoduchých technik, jako je například zavedení cílové podmnožiny uživatelů na nasazení změny v paralelní matné služby test. Pokud jste neimplementovali strategii testování, měli byste definovat testovací přístup založený na pokynech v následující tabulce:

| Scénář| Doporučení |
|-|-|
|Změna typu ověřování z federovaného na PHS/PTA nebo naopak| Použití [fázované zavedení](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout) otestovat dopad změny typu ověřování.|
|Zavedení nové zásady podmíněného přístupu (CA) nebo zásady ochrany identity|Vytvořte novou zásadu certifikační autority a přiřaďte je testovacím uživatelům.|
|Registrace testovacího prostředí aplikace|Přidejte aplikaci do produkčního prostředí, skryjte ji z panelu MyApps a přiřaďte ji testovacím uživatelům během fáze zajištění kvality (QA).|
|Změna pravidel synchronizace|Proveďte změny v testovacím Azure AD Connect se stejnou konfigurací, která je aktuálně v produkčním prostředí, označovaná také jako pracovní režim, a analyzujte výsledky CSExport. Pokud je splněna, swap do výroby, když je připraven.|
|Změna značky|Testování v samostatném testovacím tenantovi.|
|Zavedení nové funkce|Pokud funkce podporuje zavedení do cílové sady uživatelů, identifikujte pilotní uživatele a vytvořte. Samoobslužné resetování hesla a vícefaktorové ověřování může například cílit na konkrétní uživatele nebo skupiny.|
|Přeplňovaný soubor aplikace od místního zprostředkovatele identity (IdP), například služby Active Directory, do služby Azure AD|Pokud aplikace podporuje více konfigurací Protokolu IdP, například Salesforce, nakonfigurujte oba a otestujte Azure AD během okna změny (v případě, že aplikace zavádí stránku HRD). Pokud aplikace nepodporuje více ips, naplánujte testování během okna řízení změn a prostojů programu.|
|Aktualizace pravidel dynamické skupiny|Vytvořte paralelní dynamickou skupinu s novým pravidlem. Porovnejte s vypočtený výsledek, například spustit prostředí PowerShell se stejnou podmínkou.<br>Pokud test projít, swap místa, kde byla použita stará skupina (pokud je to možné).|
|Migrace licencí produktů|Odkaz na [odkaz Změna licence pro jednoho uživatele v licencované skupině ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-change-licenses).|
|Změna pravidel ad FS, jako je autorizace, vystavování, vícefaktorové povolení|Pomocí deklarace skupiny zacilte na podmnožinu uživatelů.|
|Změna prostředí ověřování ve službě AD FS nebo podobných změn v rámci celé farmy|Vytvořte paralelní farmu se stejným názvem hostitele, implementujte změny konfigurace, testujte od klientů pomocí souboru HOSTS, směrovacích pravidel služby Vyrovnávání zatížení sítě nebo podobného směrování.<br>Pokud cílová platforma nepodporuje soubory HOSTS (například mobilní zařízení), změňte ovládání.|

## <a name="access-reviews"></a>Kontroly přístupu

### <a name="access-reviews-to-applications"></a>Přístup k recenzím aplikací

V průběhu času mohou uživatelé při pohybu v různých týmech a pozicích shromažďovat přístup k prostředkům. Je důležité, aby vlastníci prostředků pravidelně kontrolovali přístup k aplikacím a odeberte oprávnění, která již nejsou potřebná v průběhu celého životního cyklu uživatelů. [Kontroly přístupu azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) ad umožňují organizacím efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazení rolí. Vlastníci prostředků by měli pravidelně kontrolovat přístup uživatelů, aby se ujistili, že přístup mají pouze ti praví lidé. V ideálním případě byste měli zvážit použití kontrolpřístupu Azure AD pro tento úkol.

![Úvodní stránka access recenze](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> Každý uživatel, který pracuje s recenzemi přístupu, musí mít placenou licenci Azure AD Premium P2.

### <a name="access-reviews-to-external-identities"></a>Přístup k recenzím k externím identitám

Je velmi důležité zachovat přístup k externím identitám omezený pouze na prostředky, které jsou potřebné, a to během doby, která je potřebná. Vytvořte pravidelný proces automatické kontroly přístupu pro všechny externí identity a přístup k aplikacím pomocí [kontrol přístupu Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)AD . Pokud proces již existuje místně, zvažte použití kontrol přístupu Azure AD. Jakmile je aplikace vyřazena nebo již není používána, odeberte všechny externí identity, které měly přístup k aplikaci.

> [!NOTE]
> Každý uživatel, který pracuje s recenzemi přístupu, musí mít placenou licenci Azure AD Premium P2.

## <a name="privileged-account-management"></a>Správa privilegovaného účtu

### <a name="privileged-account-usage"></a>Používání privilegovaného účtu

Hackeři se často zaměřují na účty správců a další prvky privilegovaného přístupu k rychlému získání přístupu k citlivým datům a systémům.Vzhledem k tomu, že uživatelé s privilegovanými rolemi mají tendenci se časem hromadit, je důležité pravidelně kontrolovat a spravovat přístup správce a poskytovat privilegovaný přístup k prostředkům Azure AD a Azure za čase.

Pokud ve vaší organizaci neexistuje žádný proces správy privilegovaných účtů nebo máte v současné době správce, kteří používají své běžné uživatelské účty ke správě služeb a prostředků, měli byste okamžitě začít používat samostatné účty, například jeden pro běžné každodenní účty. činnosti; druhý pro privilegovaný přístup a nakonfigurován s vícefaktorové finanční pomocí. Ještě lepší je, pokud vaše organizace má předplatné Azure AD Premium P2, pak byste měli okamžitě nasadit [Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure#license-requirements) (PIM). Ve stejném tokenu byste měli také zkontrolovat tyto privilegované účty a [přiřadit méně privilegované role,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure) pokud je to možné.

Dalším aspektem správy privilegovaných účtů, které by měly být implementovány, je definování [kontrol přístupu](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) pro tyto účty, a to buď ručně, nebo [automatizovaně prostřednictvím PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-perform-security-review).

#### <a name="privileged-account-management-recommended-reading"></a>Správa privilegovaných účtů doporučená čtení

- [Role ve správě privilegovaných identit Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-roles)

### <a name="emergency-access-accounts"></a>Účty nouzového přístupu

Organizace musí vytvořit [nouzové účty,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access) aby byly připraveny ke správě Azure AD pro případy, jako jsou výpadky ověřování, jako jsou:

- Výpadky součástí infrastruktur ověřování (Služba AD FS, místní služba AD, služba MFA)
- Fluktuace administrativních pracovníků

Chcete-li zabránit nechtěnému uzamčení klienta, protože se nemůžete přihlásit nebo aktivovat účet existujícího jednotlivého uživatele jako správce, měli byste vytvořit dva nebo více nouzových účtů a zajistit jejich implementaci a sladění s [osvědčenými postupy společnosti Microsoft](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure) a [postupy pro přerušení skla](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency).

### <a name="privileged-access-to-azure-ea-portal"></a>Privilegovaný přístup k portálu Azure EA

[Portál Azure Enterprise Agreement (Azure EA)](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/) umožňuje vytvářet předplatná Azure proti hlavní podnikové smlouvě, což je výkonná role v rámci podniku. Je běžné zavést vytvoření tohoto portálu ještě před zavedením Azure AD, takže je nutné použít identity Azure AD k jeho uzamčení, odebrání osobních účtů z portálu, zajištění správného delegování a zmírnění rizika uzamčení .

Aby bylo jasno, pokud je úroveň autorizace portálu EA aktuálně nastavená na "smíšený režim", musíte odebrat všechny [účty Microsoft](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account) z veškerého privilegovaného přístupu na portálu EA a nakonfigurovat portál EA tak, aby používal jenom účty Azure AD. Pokud nejsou nakonfigurované delegované role portálu EA, měli byste také najít a implementovat delegované role pro oddělení a účty.

#### <a name="privileged-access-recommended-reading"></a>Doporučený přístup k privilegovaným možnostem čtení

- [Oprávnění role správce v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

## <a name="entitlement-management"></a>Správa nároků

[Správa nároků (EM)](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) umožňuje vlastníkům aplikací sdružovat prostředky a přiřazovat je konkrétním osobám v organizaci (interníi i externí). EM umožňuje samoobslužné registrace a delegování na vlastníky firem při zachování zásad zásad správného řízení udělit přístup, nastavit dobu trvání přístupu a povolit schválení pracovních postupů. 

> [!NOTE]
> Správa nároků Azure AD vyžaduje licence Azure AD Premium P2.

## <a name="summary"></a>Souhrn

Existuje osm aspektů zabezpečení identity. Tento seznam vám pomůže určit akce, které byste měli provést k posouzení a ověření přístupu uděleného neprivilegovaným a privilegovaným identitám, auditu a řízení změn prostředí.

- Přiřaďte vlastníky ke klíčovým úkolům.
- Implementujte strategii testování.
- Pomocí kontrol y Přístupu k Azure AD můžete efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazení rolí.
- Vytvořte pravidelný automatizovaný proces kontroly přístupu pro všechny typy externích identit a přístupu k aplikacím.
- Vytvořte proces kontroly přístupu, který bude pravidelně kontrolovat a spravovat přístup správce a poskytovat privilegovaný přístup k prostředkům Azure AD a Azure.
- Zřízení nouzových účtů, které mají být připraveny ke správě Azure AD pro neočekávané výpadky.
- Uzamkněte přístup k portálu Azure EA.
- Implementujte správu nároků a poskytněte řízený přístup ke kolekci prostředků.

## <a name="next-steps"></a>Další kroky

Začínáme s [provozními kontrolami a akcemi Azure AD](active-directory-ops-guide-ops.md).

---
title: Referenční příručka pro operace identity služby Azure active directory a správy přístupu
description: Tato referenční příručka pro operace popisuje kontroly a akce, které byste měli provést k zabezpečení operací správy identity a přístupu.
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
ms.openlocfilehash: 5653fa7c67d36dbf2ee71f51f182168bccb69105
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298610"
---
# <a name="azure-active-directory-identity-and-access-management-operations-reference-guide"></a>Referenční příručka pro operace identity služby Azure active directory a správy přístupu

Tato část [referenční příručky pro operace Azure AD](active-directory-ops-guide-intro.md) popisuje kontroly a akce, které byste měli zvážit k zabezpečení a správě životního cyklu identit a jejich přiřazení.

> [!NOTE]
> Tato doporučení jsou aktuální k datu publikování, ale mohou se v průběhu času měnit. Organizace by měly průběžně vyhodnocovat své postupy identity, jak se produkty a služby společnosti Microsoft v průběhu času vyvíjejí.

## <a name="key-operational-processes"></a>Klíčové provozní procesy

### <a name="assign-owners-to-key-tasks"></a>Přiřazení vlastníků ke klíčovým úkolům

Správa služby Azure Active Directory vyžaduje průběžné provádění klíčových provozních úkolů a procesů, které nemusí být součástí projektu zavádění. Je stále důležité nastavit tyto úkoly pro udržení prostředí. Mezi klíčové úkoly a jejich doporučené vlastníky patří:

| Úkol | Vlastník |
| :- | :- |
| Definování procesu vytváření předplatných Azure | Liší se podle organizace |
| Rozhodněte, kdo získá licence Enterprise Mobility + Security | Operační tým IAM |
| Rozhodnutí, kdo získá licence Office 365 | Tým produktivity |
| Rozhodněte, kdo získá další licence, například Dynamics, VSO | Vlastník aplikace |
| Přiřazení licencí | Operační tým IAM |
| Poradce při potížích s chybami přiřazení licencí a jejich náprava | Operační tým IAM |
| Zřizování identit aplikacím ve službě Azure AD | Operační tým IAM |

Při kontrole seznamu můžete zjistit, že budete muset buď přiřadit vlastníka pro úkoly, které chybí vlastník, nebo upravit vlastnictví úkolů s vlastníky, kteří nejsou v souladu s výše uvedenými doporučeními.

#### <a name="assigning-owners-recommended-reading"></a>Přiřazení vlastníků doporučené čtení

- [Přiřazení rolí správce v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Zásady správného řízení v Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="on-premises-identity-synchronization"></a>Místní synchronizace identit

### <a name="identify-and-resolve-synchronization-issues"></a>Identifikace a řešení problémů se synchronizací

Společnost Microsoft doporučuje mít dobrý směrný plán a pochopení problémů v místním prostředí, které mohou mít za následek problémy se synchronizací do cloudu. Vzhledem k tomu, že automatizované nástroje, jako je [IdFix](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) a [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#why-use-azure-ad-connect-health) můžete generovat velký objem falešných poplachů, doporučujeme určit chyby synchronizace, které byly ponechány bez adresované více než 100 dní vyčištěním těchto objektů omylem. Dlouhodobé nevyřešené chyby synchronizace mohou generovat incidenty podpory. [Řešení chyb během synchronizace](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors) poskytuje přehled různých typů chyb synchronizace, některé z možných scénářů, které způsobují tyto chyby a potenciální způsoby, jak opravit chyby.

### <a name="azure-ad-connect-sync-configuration"></a>Konfigurace synchronizace azure a připojení

Chcete-li povolit všechna hybridní prostředí, stav zabezpečení na základě zařízení a integraci s Azure AD, je nutné synchronizovat uživatelské účty, které vaši zaměstnanci používají k přihlášení ke svým počítačům.

Pokud nesynchronizujete přihlášení uživatelů doménové struktury, měli byste změnit synchronizaci tak, aby pocházela ze správné doménové struktury.

#### <a name="synchronization-scope-and-object-filtering"></a>Obor synchronizace a filtrování objektů

Odebrání známých bloků objektů, které nemusí být synchronizovány, má následující provozní výhody:

- Méně zdrojů chyb synchronizace
- Rychlejší synchronizační cykly
- Méně "odpadků" pro přenos z místních prostor, například znečištění globálního seznamu adres pro místní účty služeb, které nejsou relevantní v cloudu

> [!NOTE]
> Pokud zjistíte, že importujete mnoho objektů, které se neexportují do cloudu, měli byste filtrovat podle ou jednotky nebo podle konkrétních atributů.

Příklady objektů, které mají být vyloučeny, jsou:

- Účty služeb, které se nepoužívají pro cloudové aplikace
- Skupiny, které nejsou určeny k použití v cloudových scénářích, jako jsou ty, které se používají k udělení přístupu k prostředkům
- Uživatelé nebo kontakty, které jsou externí identity, které jsou určeny k reprezentováns Azure AD B2B spolupráce
- Účty počítačů, u kterých zaměstnanci nemají přístup ke cloudovým aplikacím například ze serverů

> [!NOTE]
> Pokud má jedna lidská identita zřízené více účtů z něčeho, jako je například migrace, fúze nebo akvizice starší domény, měli byste synchronizovat pouze účet používaný uživatelem na základě každodenního, například to, co používá k přihlášení k počítači .

V ideálním případě budete chtít dosáhnout rovnováhy mezi snížením počtu objektů k synchronizaci a složitostí pravidel. Obecně platí, že kombinace mezi [filtrováním](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering) ou/kontejnerů a jednoduchým mapováním atributů na atribut cloudFiltered je efektivní kombinace filtrování.

> [!IMPORTANT]
> Pokud používáte filtrování skupin v produkčním prostředí, měli byste přejít na jiný přístup filtrování.

#### <a name="sync-failover-or-disaster-recovery"></a>Synchronizace převzetí služeb při selhání nebo zotavení po havárii

Azure AD Connect hraje klíčovou roli v procesu zřizování. Pokud synchronizační server přejde z nějakého důvodu do režimu offline, změny v místním prostředí nelze aktualizovat v cloudu a může mít za následek problémy s přístupem pro uživatele. Proto je důležité definovat strategii převzetí služeb při selhání, která správcům umožňuje rychle obnovit synchronizaci po přepnesynchronizační server do režimu offline. Tyto strategie mohou spadat do těchto kategorií:

- **Nasazení serveru Azure AD Connect Server(y) v pracovním režimu** – umožňuje správci "povýšit" pracovní server do produkčního prostředí pomocí jednoduchého konfiguračního přepínače.
- **Použití virtualizace** – pokud se připojení Azure AD nasadí ve virtuálním počítači (VM), správci můžou využít svůj zásobník virtualizace k živé migraci nebo rychlému opětovnému nasazení virtuálního počítače a proto obnovení synchronizace.

Pokud vaší organizaci chybí strategie zotavení po havárii a převzetí služeb při selhání pro synchronizaci, neměli byste váhat nasadit Azure AD Connect v pracovním režimu. Podobně pokud existuje neshoda mezi produkční a pracovní konfigurace, měli byste re-baseline Azure AD Connect pracovní režim tak, aby odpovídaly konfigurace výroby, včetně verzí softwaru a konfigurace.

![Snímek obrazovky konfigurace pracovního režimu Azure AD Connect](./media/active-directory-ops-guide/active-directory-ops-img1.png)

#### <a name="stay-current"></a>Zajištění aktuálnosti

Microsoft pravidelně aktualizuje Azure AD Connect. Zůstaňte aktuální a využijte výhod vylepšení výkonu, oprav chyb a nových funkcí, které každá nová verze poskytuje.

Pokud je vaše verze Azure AD Connect více než šest měsíců pozadu, měli byste upgradovat na nejnovější verzi.

#### <a name="source-anchor"></a>Zdrojová kotva

Použití **ms-DS-consistencyguid** jako [zdrojové kotvy](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts) umožňuje snadnější migraci objektů mezi doménovými strukturami a doménami, což je běžné v konsolidaci a vyčištění domény služby AD, fúzích, akvizicích a prodejích.

Pokud aktuálně používáte **ObjektGuid** jako zdrojovou kotvu, doporučujeme přepnout na using **ms-DS-ConsistencyGuid**.

#### <a name="custom-rules"></a>Vlastní pravidla

Vlastní pravidla Služby Azure AD Connect poskytují možnost řídit tok atributů mezi místními objekty a cloudovými objekty. Nadměrné používání nebo zneužití vlastních pravidel však může představovat následující rizika:

- Řešení složitosti řešení potíží
- Snížení výkonu při provádění složitých operací napříč objekty
- Vyšší pravděpodobnost rozdílnosti konfigurace mezi produkčním a pracovním serverem
- Další režie při upgradu Azure AD Connect, pokud jsou vytvořena vlastní pravidla v rámci priority větší než 100 (používá předdefinovaná pravidla)

Pokud používáte příliš složitá pravidla, měli byste prozkoumat důvody složitosti a najít příležitosti pro zjednodušení. Podobně pokud jste vytvořili vlastní pravidla s hodnotou priority nad 100, měli byste je opravit tak, aby nebyla ohrožena nebo byla v konfliktu s výchozí sadou.

Příklady zneužití vlastních pravidel:

- **Kompenzovat nečistá data v adresáři** - V tomto případě se doporučuje pracovat s vlastníky týmu AD a vyčistit data v adresáři jako úkol nápravy a upravit procesy, aby se zabránilo opětovnému zavedení chybných dat.
- **Jednorázová náprava jednotlivých uživatelů** - Je běžné najít pravidla, která se odkreslují zvláštní případ, obvykle z důvodu problému s konkrétním uživatelem.
- **Overcomplicated "CloudFiltering"** – Při snížení počtu objektů je osvědčeným postupem, existuje riziko vytvoření a příliš složitý rozsah synchronizace pomocí mnoha pravidel synchronizace. Pokud existuje složitá logika zahrnout nebo vyloučit objekty mimo filtrování ou, je doporučeno řešit tuto logiku mimo synchronizaci a popisek objekty s jednoduchým "cloudFiltered" atribut, který může toku s jednoduchým pravidlem synchronizace.

#### <a name="azure-ad-connect-configuration-documenter"></a>Dokumentátor konfigurace Azure AD Connect

[Azure AD Connect Configuration Documenter](https://github.com/Microsoft/AADConnectConfigDocumenter) je nástroj, který můžete použít ke generování dokumentace instalace Azure AD Connect, abyste umožnili lepší pochopení konfigurace synchronizace, získali důvěru v správné nastavení věcí a věděli, co se změnilo, když jste použili nové sestavení nebo konfiguraci Azure AD Connect nebo přidali nebo aktualizovali vlastní pravidla synchronizace. Mezi současné možnosti nástroje patří:

- Dokumentace o kompletní konfiguraci synchronizace Azure AD Connect.
- Dokumentace o všech změnách v konfiguraci dvou synchronizačních serverů Azure AD Connect nebo změny z daného směrného plánu konfigurace.
- Generování skriptu nasazení prostředí PowerShell pro migraci rozdílů pravidel synchronizace nebo přizpůsobení z jednoho serveru na jiný.

## <a name="assignment-to-apps-and-resources"></a>Přiřazení k aplikacím a prostředkům

### <a name="group-based-licensing-for-microsoft-cloud-services"></a>Skupinové licencování cloudových služeb Microsoftu

Azure Active Directory zjednodušuje správu licencí prostřednictvím [skupinových licencí](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal) pro cloudové služby Microsoftu. Tímto způsobem IAM poskytuje infrastrukturu skupiny a delegované správu těchto skupin na správné týmy v organizacích. Existuje několik způsobů, jak nastavit členství ve skupinách ve službě Azure AD, včetně:

- **Synchronizované z místních** – skupiny mohou pocházet z místních adresářů, což může být vhodné pro organizace, které zavedly procesy správy skupiny, které lze rozšířit o přiřazení licencí v office 365.

- **Založené na atributech / dynamické** - Skupiny mohou být vytvořeny v cloudu na základě výrazu založeného na atributech uživatele, například Oddělení se rovná "prodeji". Azure AD udržuje členy skupiny, udržovat konzistentní s definovaným výrazem. Použití tohoto druhu skupiny pro přiřazení licencí umožňuje přiřazení licencí na základě atributů, což je vhodné pro organizace, které mají vysokou kvalitu dat ve svém adresáři.

- **Delegované vlastnictví** – skupiny lze vytvořit v cloudu a mohou být určeny vlastníky. Tímto způsobem můžete vlastníkům firem, například týmu spolupráce nebo týmu BI, poukázat k definování toho, kdo by měl mít přístup.

Pokud aktuálně používáte ruční proces k přiřazení licencí a součástí uživatelům, doporučujeme implementovat skupinové licencování. Pokud váš aktuální proces nesleduje chyby licencí nebo to, co je přiřazeno versus k dispozici, měli byste definovat vylepšení procesu, který by řešil chyby licencí a monitoroval přiřazení licencí.

Dalším aspektem správy licencí je definice plánů služeb (součástí licence), které by měly být povoleny na základě pracovních funkcí v organizaci. Udělení přístupu k plánům služeb, které nejsou nutné, může vést k tomu, že se uživatelům na portálu Office zotlouže, pro které nebyli vyškoleni nebo by je neměli používat. Může zvýšit objem další chod technické podpory, zbytečné zřizování a ohrozit váš dodržování předpisů a zásady správného řízení, například při zřizování OneDrivu pro firmy jednotlivcům, kterým nemusí být povoleno sdílet obsah.

K definování plánů služeb uživatelům použijte následující pokyny:

- Správci by měli definovat "balíčky" plánů služeb, které mají být nabízeny uživatelům na základě jejich role, například pracovníka s bílým límečkem versus pracovníka podlaží.
- Vytvořte skupiny podle clusteru a přiřaďte licenci s plánem služeb.
- Volitelně lze definovat atribut, který pojme balíčky pro uživatele.

> [!IMPORTANT]
> Licencování na základě skupiny ve službě Azure AD zavádí koncept uživatelů ve stavu chyby licencování. Pokud zaznamenáte případné chyby v licencování, měli byste okamžitě [identifikovat a vyřešit](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-resolve-problems) případné problémy s přiřazením licencí.

![Automaticky generovaný snímek obrazovky počítače](./media/active-directory-ops-guide/active-directory-ops-img2.png)

#### <a name="lifecycle-management"></a>Správa životního cyklu

Pokud aktuálně používáte nástroj, například [Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/) nebo systém jiného výrobce, který se spoléhá na místní infrastrukturu, doporučujeme překládat přiřazení z existujícího nástroje, implementovat skupinové licencování a definovat správu životního cyklu skupiny na základě [skupin](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-group-advanced#use-group-based-licensing-with-dynamic-groups). Podobně pokud váš stávající proces nezohledňuje nové zaměstnance nebo zaměstnance, kteří opouštějí organizaci, měli byste nasadit skupinové licencování založené na dynamických skupinách a definovat životní cyklus členství ve skupině. Pokud se licencování na základě skupin nasazuje proti místním skupinám, které nemají správu životního cyklu, zvažte použití cloudových skupin k povolení funkcí, jako je delegované vlastnictví nebo dynamické členství založené na atributech.

### <a name="assignment-of-apps-with-all-users-group"></a>Přiřazení aplikací se skupinou Všichni uživatelé

Vlastníci zdrojů se mohou domnívat, že skupina **Všichni uživatelé** obsahuje pouze **zaměstnance organizace,** pokud mohou ve skutečnosti obsahovat **zaměstnance organizace** i **hosty**. V důsledku toho byste měli věnovat zvláštní pozornost použití **skupiny Všichni uživatelé** pro přiřazení aplikací a udělení přístupu k prostředkům, jako je obsah služby SharePoint nebo aplikace.

> [!IMPORTANT]
> Pokud je skupina **Všichni uživatelé** povolená a používá se pro zásady podmíněného přístupu, přiřazení aplikace nebo prostředků, nezapomeňte [ji zabezpečit,](https://docs.microsoft.com/azure/active-directory/b2b/use-dynamic-groups) pokud nechcete, aby zahrnovala uživatele typu Host. Kromě toho byste měli opravit přiřazení licencí vytvořením a přiřazením skupinám, které obsahují pouze **zaměstnance rozlehlé sítě.** Na druhou stranu, pokud zjistíte, že skupina **Všichni uživatelé** je povolena, ale není použita k udělení přístupu k prostředkům, ujistěte se, že provozní pokyny vaší organizace je záměrně používat tuto skupinu (která zahrnuje **zaměstnance rozlehlé sítě** a **hosty**).

### <a name="automated-user-provisioning-to-apps"></a>Automatické zřizování uživatelů do aplikací

[Automatizované zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) pro aplikace je nejlepší způsob, jak vytvořit konzistentní zřizování, zrušení zřizování a životního cyklu identit ve více systémech.

Pokud aktuálně zřizujete aplikace ad-hoc způsobem nebo používáte věci, jako jsou soubory CSV, JIT nebo místní řešení, které neřeší správu životního cyklu, doporučujeme [implementovat zřizování aplikací](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#how-do-i-set-up-automatic-provisioning-to-an-application) s Azure AD pro podporované aplikace a definovat konzistentní vzor pro aplikace, které ještě nejsou podporované službou Azure AD.

![Zřizovací služba Azure AD](./media/active-directory-ops-guide/active-directory-ops-img3.png)

### <a name="azure-ad-connect-delta-sync-cycle-baseline"></a>Základový plán cyklu rozdílové synchronizace připojení Azure AD

Je důležité porozumět objemu změn ve vaší organizaci a ujistit se, že netrvá příliš dlouho, než bude čas předvídatelné synchronizace.

Výchozí frekvence [synchronizace delta](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler) je 30 minut. Pokud synchronizace delta trvá déle než 30 minut konzistentně nebo existují významné rozdíly mezi výkonem synchronizace delta pracovní a produkční, měli byste prozkoumat a zkontrolovat [faktory ovlivňující výkon Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors).

#### <a name="azure-ad-connect-troubleshooting-recommended-reading"></a>Azure AD Connect poradce při potížích doporučená čtení

- [Příprava atributů adresáře pro synchronizaci s Office 365 pomocí nástroje IdFix – Office 365](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)
- [Azure AD Connect: Řešení potíží s chybami během synchronizace](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors)

## <a name="summary"></a>Souhrn

Existuje pět aspektů zabezpečené identity infrastruktury. Tento seznam vám pomůže rychle najít a přijmout nezbytná opatření k zabezpečení a správě životního cyklu identit a jejich nároků ve vaší organizaci.

- Přiřaďte vlastníky ke klíčovým úkolům.
- Vyhledejte a vyřešte problémy se synchronizací.
- Definujte strategii převzetí služeb při selhání pro zotavení po havárii.
- Zjednodušte správu licencí a přiřazení aplikací.
- Automatizujte zřizování uživatelů do aplikací.

## <a name="next-steps"></a>Další kroky

Začínáme s [kontrolami a akcemi správy ověřování](active-directory-ops-guide-auth.md).

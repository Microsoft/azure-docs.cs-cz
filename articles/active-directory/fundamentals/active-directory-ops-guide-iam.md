---
title: Referenční příručka operací správy identit a přístupu Azure Active Directory
description: Referenční příručka operací popisuje kontroly a akce, které byste měli provést při zabezpečení operací správy identit a přístupu.
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
ms.openlocfilehash: 2312befa5fe534cc2042b7586755ac5322d036db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90601298"
---
# <a name="azure-active-directory-identity-and-access-management-operations-reference-guide"></a>Referenční příručka operací správy identit a přístupu Azure Active Directory

Tato část [Referenční příručka operací Azure AD](active-directory-ops-guide-intro.md) popisuje kontroly a akce, které byste měli zvážit při zabezpečení a správě životního cyklu identit a jejich přiřazení.

> [!NOTE]
> Tato doporučení jsou aktuální k datu publikování, ale mohou se v průběhu času měnit. Organizace by měly průběžně vyhodnocovat své postupy své identity, protože produkty a služby Microsoftu se v průběhu času vyvíjí.

## <a name="key-operational-processes"></a>Klíčové provozní procesy

### <a name="assign-owners-to-key-tasks"></a>Přiřazení vlastníků ke klíčovým úlohám

Správa Azure Active Directory vyžaduje nepřetržité provádění klíčových provozních úloh a procesů, které nemusí být součástí projektu zavedení. Stále je důležité, abyste nastavili tyto úlohy pro zachování vašeho prostředí. Mezi klíčové úlohy a jejich Doporučené vlastníky patří:

| Úloha | Vlastník |
| :- | :- |
| Definování procesu vytváření předplatných Azure | Liší se podle organizace |
| Rozhodnutí o tom, kdo obdrží licence Enterprise Mobility + Security | Provozní tým IAM |
| Rozhodnutí o tom, kdo obdrží licence Microsoft 365 | Tým pro produktivitu |
| Rozhodnutí o tom, kdo získá další licence, například Dynamics, Visual Studio Codespaces | Vlastník aplikace |
| Přiřazení licencí | Provozní tým IAM |
| Řešení potíží a oprava chyb přiřazení licencí | Provozní tým IAM |
| Zřizování identit pro aplikace v Azure AD | Provozní tým IAM |

Při revizi seznamu se můžete setkat s tím, že budete muset buď přiřadit vlastníka pro úlohy, u kterých chybí vlastník, nebo upravit vlastnictví pro úlohy s vlastníky, které nejsou zarovnané na výše uvedená doporučení.

#### <a name="assigning-owners-recommended-reading"></a>Při přiřazování vlastníků se doporučuje číst.

- [Přiřazení rolí správce v Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)
- [Zásady správného řízení v Azure](../../governance/index.yml)

## <a name="on-premises-identity-synchronization"></a>Synchronizace místní identity

### <a name="identify-and-resolve-synchronization-issues"></a>Identifikace a řešení potíží s synchronizací

Microsoft doporučuje, abyste měli dobrý základ a pochopili problémy v místním prostředí, které můžou vést k problémům se synchronizací do cloudu. Vzhledem k tomu, že automatizované nástroje, jako je [IdFix](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) a [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md#why-use-azure-ad-connect-health) , mohou generovat velký objem falešně pozitivních hodnot, doporučujeme, abyste identifikovali chyby synchronizace, které byly ponechány po více než 100 dnech tím, že se tyto objekty při chybě čistí. Dlouhodobé nevyřešené chyby synchronizace můžou generovat incidenty podpory. [Řešení chyb během synchronizace](../hybrid/tshoot-connect-sync-errors.md) nabízí přehled různých typů chyb synchronizace. některé z možných scénářů způsobujících tyto chyby a potenciální způsoby, jak chyby opravit.

### <a name="azure-ad-connect-sync-configuration"></a>Konfigurace synchronizace Azure AD Connect

Aby bylo možné povolit veškeré hybridní prostředí, zabezpečení na základě zařízení a integraci s Azure AD, je nutné synchronizovat uživatelské účty, které vaši zaměstnanci používají pro přihlášení ke svým plochám.

Pokud nesynchronizujete uživatele doménové struktury do nástroje, měli byste změnit synchronizaci tak, aby pocházela ze správné doménové struktury.

#### <a name="synchronization-scope-and-object-filtering"></a>Obor synchronizace a filtrování objektů

Odebrání známých kontejnerů objektů, které nejsou nutné k synchronizaci, má následující provozní výhody:

- Méně zdrojů chyb synchronizace
- Rychlejší synchronizační cykly
- Méně "uvolňování" pro přenos z místního prostředí, například znečištění globálního seznamu adres pro místní účty služeb, které nejsou relevantní v cloudu

> [!NOTE]
> Pokud hledáte import mnoha objektů, které nejsou exportovány do cloudu, měli byste filtrovat podle organizační jednotky nebo konkrétních atributů.

Příklady objektů, které se mají vyloučit:

- Účty služeb, které se nepoužívají pro cloudové aplikace
- Skupiny, které nemají být používány v cloudových scénářích, jako jsou ty, které se používají pro udělení přístupu k prostředkům
- Uživatelé nebo kontakty s externími identitami, které mají být zastoupeny spoluprací Azure AD B2B
- Účty počítačů, ve kterých zaměstnanci nejsou určeni pro přístup ke cloudovým aplikacím, například servery

> [!NOTE]
> Pokud má jedna lidská identita zřízenou víc účtů, jako je třeba migrace starší domény, fúze nebo akvizice, měli byste jenom synchronizovat účet používaný uživatelem v každodenním provozu, například to, co používají pro přihlášení ke svému počítači.

V ideálním případě budete chtít dosáhnout rovnováhy mezi snížením počtu objektů, které se mají synchronizovat, a složitou složitostí pravidel. Obecně platí, že kombinace mezi [filtrováním](../hybrid/how-to-connect-sync-configure-filtering.md) organizační jednotky/kontejneru a jednoduchým mapováním atributů na atribut cloudFiltered je efektivní kombinací filtrování.

> [!IMPORTANT]
> Pokud používáte filtrování skupin v produkčním prostředí, měli byste přejít na jiný přístup pro filtrování.

#### <a name="sync-failover-or-disaster-recovery"></a>Synchronizace převzetí služeb při selhání nebo zotavení po havárii

Azure AD Connect hraje klíčovou roli v procesu zřizování. Pokud synchronizační Server z jakéhokoli důvodu přejde do offline režimu, změny v místním prostředí nejde v cloudu aktualizovat a můžou mít za následek problémy s přístupem pro uživatele. Proto je důležité definovat strategii převzetí služeb při selhání, která umožňuje správcům rychle obnovit synchronizaci po přechodu synchronizačního serveru do režimu offline. Tyto strategie můžou patřit do následujících kategorií:

- **Nasazení Azure AD Connectch serverů v pracovním režimu** – umožní správci "propagovat" pracovní server do produkčního prostředí jednoduchým přepínačem konfigurace.
- **Použít virtualizaci** – Pokud je služba Azure AD Connect nasazená na virtuálním počítači, můžou správci využít svůj zásobník virtualizace k migraci za provozu nebo rychle znovu nasadit virtuální počítač, a proto obnovit synchronizaci.

Pokud vaše organizace nemá strategii zotavení po havárii a převzetí služeb při selhání pro synchronizaci, neměli byste váhají nasazení Azure AD Connect v pracovním režimu. Podobně platí, že pokud dojde k neshodě mezi vaší produkční a pracovní konfigurací, měli byste znovu nasměrovat Azure AD Connect pracovní režim tak, aby odpovídal konfiguraci výroby, včetně verzí softwaru a konfigurací.

![Snímek obrazovky s konfigurací pracovního režimu Azure AD Connect](./media/active-directory-ops-guide/active-directory-ops-img1.png)

#### <a name="stay-current"></a>Zajištění aktuálnosti

Aktualizace společnosti Microsoft Azure AD Connect pravidelně. Současným využitím vylepšení výkonu, oprav chyb a nových funkcí, které poskytuje každá nová verze.

Pokud je vaše verze Azure AD Connect víc než šest měsíců, měli byste upgradovat na nejnovější verzi.

#### <a name="source-anchor"></a>Zdrojové ukotvení

Použití služby **MS-DS-consistencyguid** jako [zdrojového ukotvení](../hybrid/plan-connect-design-concepts.md) umožňuje snazší migraci objektů napříč doménovými strukturami a doménami, což je běžné při konsolidaci/vyčištění domény AD, fúzí, akvizicích a divestitures.

Pokud v tuto chvíli jako zdrojové kotvy používáte **objectGUID** , doporučujeme přepnout na použití **MS-DS-ConsistencyGuid**.

#### <a name="custom-rules"></a>Vlastní pravidla

Azure AD Connect vlastní pravidla poskytují možnost řídit tok atributů mezi místními objekty a objekty cloudu. Přestavování nebo nepoužívání vlastních pravidel ale může způsobit následující rizika:

- Řešení potíží s složitostí
- Snížení výkonu při provádění složitých operací napříč objekty
- Vyšší pravděpodobnost rozdílů mezi konfigurací mezi provozním serverem a přípravným serverem
- Další režie při upgradu Azure AD Connect, pokud jsou vlastní pravidla vytvořená v rámci priority větší než 100 (používá se v předdefinovaných pravidlech)

Pokud používáte příliš složitá pravidla, měli byste prozkoumat důvody složitosti a najít příležitosti pro zjednodušení. Podobně platí, že pokud jste vytvořili vlastní pravidla s hodnotou priority vyšší než 100, měli byste pravidla opravit, aby nedocházelo k rizikům nebo byly v konfliktu s výchozí sadou.

Mezi příklady nepoužití vlastních pravidel patří:

- Kompenzovat nezměněná **data v adresáři** – v takovém případě se doporučuje pracovat s vlastníky týmu AD a vyčistit data v adresáři jako úlohu nápravy a upravit procesy tak, aby nedocházelo k opětovnému nasazování chybných dat.
- **Jednorázová náprava individuálních uživatelů** – je běžné najít pravidla, která mají zvláštní velká a malá písmena, většinou kvůli problému s konkrétním uživatelem.
- Velmi **komplikované "CloudFiltering"** – při snižování počtu objektů je dobrým zvykem vytvoření a velmi komplikovaný rozsah synchronizace s použitím řady pravidel synchronizace. Pokud existuje složitá logika pro zahrnutí/vyloučení objektů mimo filtrování organizačních jednotek, doporučuje se řešit tuto logiku mimo synchronizaci a označit objekty jednoduchým atributem "cloudFiltered", který může být v rámci jednoduchého pravidla synchronizace.

#### <a name="azure-ad-connect-configuration-documenter"></a>Dokumentace ke konfiguraci Azure AD Connect

[Dokument konfigurace Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter) je nástroj, pomocí kterého můžete vygenerovat dokumentaci k instalaci Azure AD Connect a umožnit tak lepší porozumění konfiguraci synchronizace, zajistit spolehlivější sestavování a vědět, co se změnilo při použití nového buildu nebo konfigurace Azure AD Connect nebo přidání nebo aktualizace vlastních pravidel synchronizace. Mezi aktuální funkce nástroje patří:

- Dokumentace k kompletní konfiguraci Azure AD Connect synchronizace.
- Dokumentace o všech změnách v konfiguraci dvou Azure AD Connectch synchronizačních serverů nebo změnách z daného směrného plánu konfigurace.
- Generování skriptu nasazení prostředí PowerShell pro migraci rozdílů v pravidlech synchronizace nebo přizpůsobení z jednoho serveru na jiný.

## <a name="assignment-to-apps-and-resources"></a>Přiřazení k aplikacím a prostředkům

### <a name="group-based-licensing-for-microsoft-cloud-services"></a>Licencování na základě skupin pro cloudové služby Microsoftu

Azure Active Directory zjednodušuje správu licencí prostřednictvím [licencování na základě skupin](./active-directory-licensing-whatis-azure-portal.md) pro cloudové služby Microsoftu. Díky tomu IAM zajišťuje infrastrukturu skupin a delegovanou správu těchto skupin do řádných týmů v organizacích. Existuje několik způsobů, jak nastavit členství ve skupinách v Azure AD, včetně těchto:

- **Synchronizace z místních** skupin může pocházet z místních adresářů, což může být vhodné pro organizace, které mají zavedené procesy správy skupin, které se dají rozšířit tak, aby se přidělily licence v Microsoft 365.

- **Atributy založené na atributech a dynamické** skupiny se dají vytvořit v cloudu na základě výrazu založeného na uživatelských atributech, například oddělení se rovná "Sales". Azure AD udržuje členy skupiny a udržuje je konzistentní s definovaným výrazem. Použití tohoto druhu skupiny pro přiřazení licence umožňuje přiřazení licence na základě atributů, které je vhodné pro organizace, které mají ve svém adresáři vysokou kvalitu dat.

- **Delegované vlastnictví** – skupiny se dají vytvářet v cloudu a můžou se jmenovat vlastníci. Tímto způsobem můžete svým vlastníkům, například týmu pro spolupráci nebo týmu BI, určit, kdo má mít přístup.

Pokud aktuálně používáte ruční proces k přiřazení licencí a součástí uživatelům, doporučujeme implementovat licencování na základě skupin. Pokud váš aktuální proces nemonitoruje chyby licencování nebo to, co je k dispozici, měli byste definovat vylepšení procesu pro řešení chyb při licencování a monitorování přiřazení licencí.

Dalším aspektem správy licencí je definice plánů služeb (součásti licence), které by měly být povolené na základě pracovních funkcí v organizaci. Udělení přístupu k plánům služeb, které nejsou nezbytné, může mít za následek, že uživatelé uvidí nástroje na portálu Office, na které nebyli vyškolení nebo by se neměly používat. Může dodávat další objemy helpdesku, zbytečné zřizování a nastavovat dodržování předpisů a zásady správného řízení, například při zřizování OneDrivu pro firmy jednotlivcům, kteří nemusí mít povolený sdílení obsahu.

Pro definování plánů služeb pro uživatele použijte následující pokyny:

- Správci by měli definovat "sady" plánů služeb, které mají být nabídnuty uživatelům na základě jejich role, například pracovní proces bílého kroužku a pracovní proces.
- Vytvořte skupiny podle clusteru a přiřaďte licenci k plánu služby.
- Volitelně lze atribut definovat tak, aby obsahoval balíčky pro uživatele.

> [!IMPORTANT]
> Licencování na základě skupin ve službě Azure AD zavádí koncept uživatelů v chybovém stavu licencování. Pokud si všimnete jakýchkoli chyb licencování, měli byste okamžitě [identifikovat a vyřešit](../users-groups-roles/licensing-groups-resolve-problems.md) případné problémy s přiřazením licencí.

![Snímek obrazovky s popisem obrazovky počítače se automaticky vygeneroval.](./media/active-directory-ops-guide/active-directory-ops-img2.png)

#### <a name="lifecycle-management"></a>Správa životního cyklu

Pokud aktuálně používáte nástroj, například [Microsoft Identity Manager](/microsoft-identity-manager/) nebo systém třetí strany, který spoléhá na místní infrastrukturu, doporučujeme, abyste přesměrovali přiřazení z existujícího nástroje, implementovali licencování na základě skupin a definovali správu životního cyklu skupin na základě [skupin](../users-groups-roles/licensing-group-advanced.md#use-group-based-licensing-with-dynamic-groups). Podobně platí, že pokud se váš stávající proces nechystá pro nové zaměstnance nebo zaměstnance, kteří odejdou z organizace, měli byste nasadit licencování na základě skupin na základě dynamických skupin a definovat životní cyklus členství ve skupině. Nakonec, pokud se licencování na základě skupin nasadí do místních skupin, které nemají správu životního cyklu, zvažte použití skupin cloudových funkcí, které umožňují využívat možnosti, jako je delegované vlastnictví nebo dynamické členství založené na atributech.

### <a name="assignment-of-apps-with-all-users-group"></a>Přiřazení aplikací ke skupině Všichni uživatelé

Vlastníci prostředků se můžou domnívat, že skupina **Všichni uživatelé** obsahuje jenom **zaměstnance v podniku** , když můžou ve skutečnosti obsahovat **podnikové zaměstnance** i **hosty**. V důsledku toho byste měli věnovat zvláštní pozornost při použití skupiny **Všichni uživatelé** pro přiřazení aplikace a udělení přístupu k prostředkům, jako je například sharepointový obsah nebo aplikace.

> [!IMPORTANT]
> Pokud je skupina **Všichni uživatelé** povolená a použitá pro zásady podmíněného přístupu, aplikaci nebo přiřazení prostředků, ujistěte se, že [skupinu zabezpečíte](../external-identities/use-dynamic-groups.md) , pokud nechcete, aby zahrnovala uživatele typu Host. Kromě toho byste měli opravit přiřazení licencí tím, že vytvoříte a přiřadíte skupiny, které obsahují pouze **zaměstnance v podniku** . Pokud zjistíte, že je povolená skupina **Všichni uživatelé** , ale nepoužívá se k udělení přístupu k prostředkům, ujistěte se, že provozní pokyny vaší organizace tuto skupinu záměrně používají (což zahrnuje i **zaměstnance v podniku** i **hosty**).

### <a name="automated-user-provisioning-to-apps"></a>Automatizované zřizování uživatelů pro aplikace

[Automatizované zřizování uživatelů](../app-provisioning/user-provisioning.md) pro aplikace je nejlepší způsob, jak vytvořit konzistentní zřizování, zrušení zřízení a životní cyklus identit napříč různými systémy.

Pokud aktuálně vytváříte aplikace v rámci ad hoc nebo pomocí takových akcí jako soubory CSV, JIT nebo místní řešení, které neřeší správu životního cyklu, doporučujeme, abyste [implementovali zřizování aplikací](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) s Azure AD pro podporované aplikace a definovali konzistentní vzor pro aplikace, které zatím Azure AD nepodporují.

![Služba zřizování Azure AD](./media/active-directory-ops-guide/active-directory-ops-img3.png)

### <a name="azure-ad-connect-delta-sync-cycle-baseline"></a>Standardní hodnota Azure AD Connect cyklus synchronizace Delta

Je důležité porozumět objemu změn ve vaší organizaci a ujistit se, že netrvá příliš dlouho k předvídatelnému času synchronizace.

[Výchozí četnost rozdílové synchronizace](../hybrid/how-to-connect-sync-feature-scheduler.md) je 30 minut. Pokud rozdílová synchronizace trvá déle než 30 minut, nebo dochází ke značným rozdílům mezi výkonem rozdílové synchronizace fázování a výroby, měli byste prozkoumat a zkontrolovat [faktory ovlivňující výkon Azure AD Connect](../hybrid/plan-connect-performance-factors.md).

#### <a name="azure-ad-connect-troubleshooting-recommended-reading"></a>Poradce při potížích s doporučeným čtením Azure AD Connect

- [Příprava atributů adresáře pro synchronizaci s Microsoft 365 pomocí nástroje IdFix](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)
- [Azure AD Connect: řešení chyb při synchronizaci](../hybrid/tshoot-connect-sync-errors.md)

## <a name="summary"></a>Shrnutí

Zabezpečená infrastruktura identity má pět aspektů. Tento seznam vám pomůže rychle vyhledat a provést nezbytné akce pro zabezpečení a správu životního cyklu identit a jejich nároků ve vaší organizaci.

- Přiřaďte vlastníky ke klíčovým úlohám.
- Vyhledejte a vyřešte problémy s synchronizací.
- Definujte strategii převzetí služeb při selhání pro zotavení po havárii.
- Zjednodušte správu licencí a přiřazování aplikací.
- Automatizujte zřizování uživatelů s aplikacemi.

## <a name="next-steps"></a>Další kroky

Začněte s [kontrolami a akcemi správy ověřování](active-directory-ops-guide-auth.md).
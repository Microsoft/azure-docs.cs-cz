---
title: Jak fungují vztahy důvěryhodnosti pro služby Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Další informace o tom, jak funguje důvěryhodnost doménové struktury se službou Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 903881a1d15c1f043e381f50e5b69d661cd08192
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476441"
---
# <a name="how-trust-relationships-work-for-resource-forests-in-azure-active-directory-domain-services"></a>Jak fungují vztahy důvěryhodnosti pro doménové struktury prostředků ve službě Azure Active Directory Domain Services

Služba AD DS (Active Directory Domain Services) poskytuje zabezpečení napříč více doménami nebo doménovými strukturami prostřednictvím vztahů důvěryhodnosti domény a doménové struktury. Před ověřením mezi vztahy důvěryhodnosti musí systém Windows nejprve zkontrolovat, zda doména požadovaná uživatelem, počítačem nebo službou má vztah důvěryhodnosti s doménou žádajícího účtu.

Chcete-li zkontrolovat tento vztah důvěryhodnosti, systém zabezpečení systému Windows vypočítá cestu důvěryhodnosti mezi řadičem domény (DC) pro server, který obdrží požadavek, a řadičem domény v doméně žádajícího účtu.

Mechanismy řízení přístupu poskytované službou AD DS a distribuovaným modelem zabezpečení systému Windows poskytují prostředí pro provoz vztahů důvěryhodnosti domény a doménové struktury. Aby tyto vztahy důvěryhodnosti fungovaly správně, musí mít každý prostředek nebo počítač přímou cestu důvěryhodnosti k řadiči domény v doméně, ve které je umístěn.

Cesta důvěryhodnosti je implementována službou Přihlašování k síti pomocí ověřeného připojení vzdáleného volání procedur (RPC) k důvěryhodné autoritě domény. Zabezpečený kanál se také rozšiřuje na další domény služby AD DS prostřednictvím vztahů důvěryhodnosti mezi doménami. Tento zabezpečený kanál slouží k získání a ověření informací o zabezpečení, včetně identifikátorů zabezpečení (SID) pro uživatele a skupiny.

## <a name="trust-relationship-flows"></a>Toky vztahů důvěryhodnosti

Tok zabezpečené komunikace přes svěřenské fondy určuje pružnost vztahu důvěryhodnosti. Způsob vytvoření nebo konfigurace vztahu důvěryhodnosti určuje, do jaké míry se komunikace rozprostírá v doménových strukturách nebo mezi ně.

Tok komunikace přes vztahy důvěryhodnosti je určen směrem vztahu důvěryhodnosti. Vztahy důvěryhodnosti mohou být jednosměrné nebo obousměrné a mohou být přenosné nebo nepřenosné.

Následující diagram ukazuje, že všechny domény ve *stromech 1* a *stromu 2* mají ve výchozím nastavení přenosité vztahy důvěryhodnosti. V důsledku toho mohou uživatelé ve *stromu 1* přistupovat k prostředkům v doménách ve *stromu 2* a uživatelé ve *stromu 1* mají přístup k prostředkům ve *stromu 2*, pokud jsou u prostředku přiřazena správná oprávnění.

![Diagram vztahů důvěryhodnosti mezi dvěma doménovými strukturami](./media/concepts-forest-trust/trust-relationships.png)

### <a name="one-way-and-two-way-trusts"></a>Jednosměrné a obousměrné vztahy důvěryhodnosti

Vztahy důvěryhodnosti umožňují přístup k prostředkům může být jednosměrný nebo obousměrný.

Jednosměrný vztah důvěryhodnosti je jednosměrná cesta ověřování vytvořená mezi dvěma doménami. V jednosměrném vztahu důvěryhodnosti mezi *doménou A* a *doménou B*mají uživatelé v *doméně A* přístup k prostředkům v *doméně B*. Uživatelé v *doméně B* však nemají přístup k prostředkům v *doméně A*.

Některé jednosměrné vztahy důvěryhodnosti mohou být nepřenosné nebo přenosné v závislosti na typu vytvářeného vztahu důvěryhodnosti.

Ve obousměrném vztahu *důvěryhodnosti důvěřuje doména A* *doméně B* a domény *B* *důvěřuje doméně A*. Tato konfigurace znamená, že požadavky na ověření mohou být předány mezi dvěma doménami v obou směrech. Některé obousměrné vztahy mohou být nepřenosné nebo přenosné v závislosti na typu vytvářeného vztahu důvěryhodnosti.

Všechny vztahy důvěryhodnosti domény v doménové struktuře služby AD DS jsou obousměrné přenosné vztahy důvěryhodnosti. Při vytvoření nové podřízené domény se automaticky vytvoří obousměrný přenositý vztah důvěryhodnosti mezi novou podřízenou doménou a nadřazenou doménou.

### <a name="transitive-and-non-transitive-trusts"></a>Přenosité a nepřenosné vztahy důvěryhodnosti

Přenositelnost určuje, zda lze vztah důvěryhodnosti rozšířit mimo dvě domény, se kterými byl vytvořen.

* Přenositý vztah důvěryhodnosti lze použít k rozšíření vztahů důvěryhodnosti s jinými doménami.
* Nepřenositezvolený vztah důvěryhodnosti lze použít k odepření vztahů důvěryhodnosti s jinými doménami.

Při každém vytvoření nové domény v doménové struktuře se automaticky vytvoří obousměrný přenositý vztah důvěryhodnosti mezi novou doménou a její nadřazenou doménou. Pokud jsou do nové domény přidány podřízené domény, cesta důvěryhodnosti natéká nahoru hierarchií domény a rozšiřuje počáteční cestu důvěryhodnosti vytvořenou mezi novou doménou a její nadřazenou doménou. Přenosité vztahy důvěryhodnosti protékají nahoru stromem domény při vytváření přechodných vztahů důvěryhodnosti mezi všemi doménami ve stromu domény.

Požadavky na ověření následují tyto cesty důvěryhodnosti, takže účty z libovolné domény v doménové struktuře mohou být ověřeny jinou doménou v doménové struktuře. Pomocí jednoho procesu přihlášení mohou účty se správnými oprávněními přistupovat k prostředkům v libovolné doméně v doménové struktuře.

## <a name="forest-trusts"></a>Vztahy důvěryhodnosti doménové struktury

Vztahy důvěryhodnosti doménové struktury pomáhají spravovat segmentované infrastruktury služby AD DS a podporují přístup k prostředkům a dalším objektům ve více doménových strukturách. Svěřenské fondy v oblasti domén ové struktury jsou užitečné pro poskytovatele služeb, společnosti, které procházejí fúzemi nebo akvizicemi, extranety pro spolupráci a společnosti, které hledají řešení administrativní autonomie.

Pomocí vztahů důvěryhodnosti doménové struktury můžete propojit dvě různé doménové struktury a vytvořit tak jednosměrný nebo obousměrný přenositý vztah důvěryhodnosti. Vztah důvěryhodnosti doménové struktury umožňuje správcům propojit dvě doménové struktury služby AD DS s jedním vztahem důvěryhodnosti a zajistit tak bezproblémové ověřování a autorizaci v doménových strukturách.

Vztah důvěryhodnosti doménové struktury lze vytvořit pouze mezi kořenovou doménou doménové struktury v jedné doménové struktuře a kořenovou doménou doménové struktury v jiné doménové struktuře. Vztahy důvěryhodnosti doménové struktury lze vytvořit pouze mezi dvěma doménovými strukturami a nelze je implicitně rozšířit na třetí doménovou strukturu. Toto chování znamená, že pokud je vytvořen vztah důvěryhodnosti doménové struktury mezi *doménovou strukturu 1* a *doménovou struktuře 2*a vytvoří se jiný vztah důvěryhodnosti doménové struktury mezi *doménovou strukturu 2* a *doménovou struktuře 3*, *doménová struktura 1* nemá implicitní vztah důvěryhodnosti s *doménovou strukturu 3*.

Následující diagram znázorňuje dva samostatné vztahy důvěryhodnosti doménové struktury mezi třemi doménovými strukturami služby AD DS v jedné organizaci.

![Diagram vztahů důvěryhodnosti doménové struktury v rámci jedné organizace](./media/concepts-forest-trust/forest-trusts.png)

Konfigurace tohoto příkladu poskytuje následující přístup:

* Uživatelé v *doménové struktuře 2* mají přístup k prostředkům v libovolné doméně v *doménové struktuře 1* nebo *doménové struktuře 3.*
* Uživatelé v *doménové struktuře 3* mají přístup k prostředkům v libovolné doméně v *doménové struktuře 2.*
* Uživatelé v *doménové struktuře 1* mají přístup k prostředkům v libovolné doméně v *doménové struktuře 2.*

Tato konfigurace neumožňuje uživatelům v *doménové struktuře 1* přístup k prostředkům v *doménové struktuře 3* nebo naopak. Chcete-li uživatelům v *doménové struktuře 1* i *doménové struktuře 3* umožnit sdílení prostředků, musí být mezi dvěma doménovými strukturami vytvořen obousměrný přenositý vztah důvěryhodnosti.

Pokud je mezi dvěma doménovými strukturami vytvořen jednosměrný vztah důvěryhodnosti doménové struktury, mohou členové důvěryhodné doménové struktury využívat zdroje umístěné v důvěřující doménové struktuře. Vztah důvěryhodnosti však funguje pouze v jednom směru.

Například při jednosměrné, vztahy důvěryhodnosti doménové struktury mezi *doménovou strukturu 1* (důvěryhodná doménová struktura) a *doménové struktury 2* (důvěřující doménová struktura):

* Členové *doménové struktury 1* mají přístup ke zdrojům umístěným v *doménové struktuře 2*.
* Členové *doménové struktury 2* nemají přístup k prostředkům umístěným v *doménové struktuře 1* pomocí stejného vztahu důvěryhodnosti.

> [!IMPORTANT]
> Doménová struktura prostředků služby Azure AD Domain Services podporuje jenom jednosměrný vztah důvěryhodnosti doménové struktury pro místní službu Active Directory.

### <a name="forest-trust-requirements"></a>Požadavky na důvěryhodnost doménové struktury

Před vytvořením vztahu důvěryhodnosti doménové struktury je třeba ověřit, zda máte k ono správnou infrastrukturu DNS (Domain Name System). Vztahy důvěryhodnosti doménové struktury lze vytvořit pouze v případě, že je k dispozici jedna z následujících konfigurací DNS:

* Jeden kořenový server DNS je kořenový server DNS pro oba obory názvů DNS doménové struktury - kořenová zóna obsahuje delegování pro každý obor názvů DNS a kořenové rady všech serverů DNS zahrnují kořenový server DNS.
* Tam, kde neexistuje žádný sdílený kořenový server DNS, a kořenové servery DNS pro každý obor názvů DNS v doménové struktuře DNS používají pro každý obor názvů DNS podmíněné servery DNS ke směrování dotazů na názvy v jiném oboru názvů.

    > [!IMPORTANT]
    > Doménová struktura prostředků služby Azure AD Domain Services musí používat tuto konfiguraci DNS. Hostování jiného oboru názvů DNS než oboru názvů DNS doménové struktury není funkcí služby Azure AD Domain Services. Podmíněné předávání je správná konfigurace.

* Tam, kde neexistuje žádný sdílený kořenový server DNS, a kořenové servery DNS pro každý obor názvů DNS doménové struktury používají sekundární zóny DNS v každém oboru názvů DNS ke směrování dotazů na názvy v jiném oboru názvů.

Chcete-li vytvořit vztah důvěryhodnosti doménové struktury, musíte být členem skupiny Domain Admins (v kořenové doméně doménové struktury) nebo skupiny Enterprise Admins ve službě Active Directory. Každému vztahu důvěryhodnosti je přiřazeno heslo, které musí znát správci v obou doménových strukturách. Členové enterprise admins v obou doménových strukturách můžete vytvořit vztahy důvěryhodnosti v obou doménových strukturách najednou a v tomto scénáři je automaticky generováno a zapsáno heslo, které je kryptograficky náhodné je automaticky generováno a zapsáno pro obě doménové struktury.

Odchozí doménové struktury pro služby Azure AD Domain Services se vytvoří na webu Azure Portal. Nevytváříte vztah důvěryhodnosti ručně se samotnou spravovanou doménou. Příchozí vztah důvěryhodnosti doménové struktury musí být nakonfigurován uživatelem s oprávněními, která byla dříve zaznamenána v místní službě Active Directory.

## <a name="trust-processes-and-interactions"></a>Důvěryhodné procesy a interakce

Mnoho transakcí mezi doménami a mezi doménovými strukturami závisí na vztahu důvěryhodnosti domény nebo doménové struktury za účelem dokončení různých úkolů. Tato část popisuje procesy a interakce, ke kterým dochází při přístupu k prostředkům napříč vztahy důvěryhodnosti a vyhodnocují se odkazy na ověřování.

### <a name="overview-of-authentication-referral-processing"></a>Přehled zpracování doporučení pro ověřování

Pokud je žádost o ověření odkazována na doménu, musí řadič domény v této doméně určit, zda existuje vztah důvěryhodnosti s doménou, ze které požadavek pochází. Směr vztahu důvěryhodnosti a to, zda je vztah důvěryhodnosti přenositý nebo nepřenositý, musí být také určen před ověřením uživatele pro přístup k prostředkům v doméně. Proces ověřování mezi důvěryhodnými doménami se liší v závislosti na ověřovacím protokolu, který se používá. Protokoly Kerberos V5 a NTLM zpracovávají odkazy na ověřování do domény odlišně

### <a name="kerberos-v5-referral-processing"></a>Zpracování odkazů protokolu Kerberos V5

Ověřovací protokol Kerberos V5 je závislý na službě Přihlašování k síti v řadičích domény pro informace o ověřování a autorizaci klientů. Protokol Kerberos se připojuje k online centru distribuce klíčů (KDC) a k úložišti účtů služby Active Directory pro lístky na relace.

Protokol Kerberos také používá vztahy důvěryhodnosti pro služby udělování lístků mezi sférami (TGS) a k ověření certifikátů atributů oprávnění (PAC) v zabezpečeném kanálu. Protokol Kerberos provádí ověřování mezi sférami pouze s doménovými oblastmi protokolu Kerberos jiné ho schodišti, jako je například sféra Protokolu Kerberos mit, a nemusí pracovat se službou Přihlášení k síti.

Pokud klient používá protokol Kerberos V5 pro ověřování, požaduje lístek na server v cílové doméně od řadiče domény v doméně účtu. Protokol Kerberos KDC funguje jako důvěryhodný prostředník mezi klientem a serverem a poskytuje klíč relace, který umožňuje oběma stranám vzájemně se ověřovat. Pokud se cílová doména liší od aktuální domény, KDC následuje logický proces k určení, zda lze odkazovat na požadavek na ověření:

1. Je aktuální doména důvěryhodná přímo doménou serveru, který je požadován?
    * Pokud ano, odešlete klientovi odkaz na požadovanou doménu.
    * Pokud ne, přejděte k dalšímu kroku.

2. Existuje přenositý vztah důvěryhodnosti mezi aktuální doménou a další doménou na cestě důvěryhodnosti?
    * Pokud ano, odešlete klientovi odkaz na další doménu na cestě důvěryhodnosti.
    * Pokud ne, odešlete klientovi zprávu o odepření přihlášení.

### <a name="ntlm-referral-processing"></a>Zpracování odkazů NTLM

Ověřovací protokol NTLM je závislý na službě Přihlašování k síti v řadičích domény pro informace o ověřování a autorizaci klientů. Tento protokol ověřuje klienty, kteří nepoužívají ověřování protokolem Kerberos. NTLM používá vztahy důvěryhodnosti k předání požadavků na ověření mezi doménami.

Pokud klient používá ntlm pro ověřování, počáteční požadavek na ověření jde přímo z klienta na server prostředků v cílové doméně. Tento server vytvoří výzvu, na kterou klient reaguje. Server pak odešle odpověď uživatele do řadiče domény v doméně účtu počítače. Tento řadič domény zkontroluje uživatelský účet v databázi svých účtů zabezpečení.

Pokud účet v databázi neexistuje, řadič domény určí, zda má provést předávací ověřování, předat požadavek nebo požadavek odepřít pomocí následující logiky:

1. Má aktuální doména vztah přímédůvěryhodnosti s doménou uživatele?
    * Pokud ano, řadič domény odešle pověření klienta do řadiče domény v doméně uživatele pro předávací ověřování.
    * Pokud ne, přejděte k dalšímu kroku.

2. Má aktuální doména přenositý vztah důvěryhodnosti s doménou uživatele?
    * Pokud ano, předajte požadavek na ověření další doméně v cestě důvěryhodnosti. Tento řadič domény tento proces zopakuje kontrolou pověření uživatele podle vlastní databáze účtů zabezpečení.
    * Pokud ne, odešlete klientovi zprávu o odepření přihlášení.

### <a name="kerberos-based-processing-of-authentication-requests-over-forest-trusts"></a>Zpracování požadavků na ověření na základě protokolu Kerberos přes vztahy důvěryhodnosti doménové struktury

Pokud jsou dvě doménové struktury propojeny vztahem důvěryhodnosti doménové struktury, mohou být požadavky na ověření provedené pomocí protokolů Kerberos V5 nebo NTLM směrovány mezi doménovými strukturami a poskytovat tak přístup k prostředkům v obou doménových strukturách.

Při prvním vytvoření vztahu důvěryhodnosti doménové struktury shromažďuje každá doménová struktura všechny důvěryhodné obory názvů ve své partnerské doménové struktuře a ukládá informace do [důvěryhodného objektu domény](#trusted-domain-object). Důvěryhodné obory názvů zahrnují názvy doménových stromů, přípony hlavního názvu uživatele (UPN), přípony hlavního názvu služby (SPN) a obory názvů ID zabezpečení používané v jiné doménové struktuře. Objekty TDO jsou replikovány do globálního katalogu.

Před tím, než ověřovací protokoly budou moci sledovat cestu důvěryhodnosti doménové struktury, musí být hlavní název služby (SPN) počítače prostředků přeložen do umístění v jiné doménové struktuře. Hlavní aktualizace služeb spn může být jedna z následujících:

* Název DNS hostitele.
* Název DNS domény.
* Rozlišující název objektu bodu připojení služby.

Pokud se pracovní stanice v jedné doménové struktuře pokusí o přístup k datům v počítači prostředků v jiné doménové struktuře, proces ověřování protokolu Kerberos kontaktuje řadič domény pro lístek služby do hlavního názvu služby počítače poskytujícího prostředky. Jakmile řadič domény zažádá o dotaz globálního katalogu a zjistí, že název SPN není ve stejné doménové struktuře jako řadič domény, odešle řadič domény odkaz pro svou nadřazenou doménu zpět do pracovní stanice. V tomto okamžiku pracovní stanice dotazuje nadřazené domény pro lístek služby a nadále sledovat postoupení řetězce, dokud se nedostane do domény, kde je umístěn prostředek.

Následující diagram a kroky poskytují podrobný popis procesu ověřování protokolem Kerberos, který se používá, když se počítače se systémem Windows pokusí o přístup k prostředkům z počítače umístěného v jiné doménové struktuře.

![Diagram procesu protokolu Kerberos přes vztah důvěryhodnosti doménové struktury](media/concepts-forest-trust/kerberos-over-forest-trust-process.png)

1. *Uživatel1* se přihlásí k *Workstation1* pomocí pověření z *domény europe.tailspintoys.com.* Uživatel se pak pokusí o přístup ke sdílenému prostředku na *serveru FileServer1* umístěnému v doménové struktuře *usa.wingtiptoys.com.*

2. *Workstation1* kontaktuje protokol Kerberos KDC na řadiči domény ve své doméně *ChildDC1*a požádá o lístek služby pro hlavní název služby *FileServer1.*

3. *ChildDC1* nenajde hlavní název služby ve své databázi domény a dotazuje se globálního katalogu, zda některé domény v *tailspintoys.com* doménové struktuře tento název SPN obsahují. Vzhledem k tomu, že globální katalog je omezen na vlastní doménovou strukturu, není název SPN nalezen.

    Globální katalog poté zkontroluje v databázi informace o všech doménových fondech vytvořených s doménovou strukturu. Pokud je nalezen, porovná přípony názvů uvedené v důvěryhodném objektu domény důvěryhodnosti doménové struktury (TDO) s příponou cílového názvu SPN a vyhledá shodu. Jakmile je nalezena shoda, globální katalog poskytuje nápovědu směrování zpět na *ChildDC1*.

    Tipy při směrování pomáhají směrovat požadavky na ověření směrem k cílové doménové struktuře. Rady při psaní mohou být použity pouze v případě, že všechny tradiční ověřovací kanály, například místní řadič domény a potom globální katalog, nevyhledávají název SPN.

4. *ChildDC1* odešle odkaz pro svou nadřazenou doménu zpět do *Workstation1*.

5. *Workstation1* kontaktuje řadič domény v *doménové struktuře ForestRootDC1* (jeho nadřazená doména) a může být odkazna řadič domény *(ForestRootDC2)* v kořenové doménové struktuře *doménové* struktury wingtiptoys.com.

6. *Pracovní stanice1* kontaktuje *ForestRootDC2* v *wingtiptoys.com* doménové struktuře pro lístek služby k požadované službě.

7. *ForestRootDC2* kontaktuje svůj globální katalog, aby našel hlavní název služby, a globální katalog najde shodu pro hlavní název služby a odešle jej zpět do *ForestRootDC2*.

8. *ForestRootDC2* pak odešle odkaz na *usa.wingtiptoys.com* zpět do *Workstation1*.

9. *Workstation1* kontaktuje KDC na *ChildDC2* a vyjedná lístek pro *User1* získat přístup k *FileServer1*.

10. Jakmile *workstation1* má lístek služby, odešle lístek služby *FileServer1*, který čte *User1*'s bezpečnostní pověření a vytvoří přístupový token odpovídajícím způsobem.

## <a name="trusted-domain-object"></a>Důvěryhodný objekt domény

Každá doménová struktura nebo vztah důvěryhodnosti doménové struktury v rámci organizace je reprezentována důvěryhodným objektem domény (TDO) *uloženým* v systémovém kontejneru v rámci své domény.

### <a name="tdo-contents"></a>Obsah TDO

Informace obsažené v tdo se liší v závislosti na tom, zda tdo byl vytvořen vztah důvěryhodnosti domény nebo důvěryhodnosti doménové struktury.

Při vytvoření vztahu důvěryhodnosti domény jsou v tdo reprezentovány atributy, jako je název domény DNS, SID domény, typ důvěryhodnosti, přenositelnost důvěryhodnosti a reciproční název domény. Úložiště důvěryhodnosti tdos uložit další atributy k identifikaci všech důvěryhodných oborů názvů z partnerské doménové struktury. Mezi tyto atributy patří názvy doménových stromů, přípony hlavního názvu uživatele (UPN), přípony hlavního názvu služby (SPN) a obory názvů ID zabezpečení (SID).

Vzhledem k tomu, že vztahy důvěryhodnosti jsou uloženy ve službě Active Directory jako prodejné vztahy důvěryhodnosti, všechny domény v doménové struktuře mají znalosti vztahů důvěryhodnosti, které jsou v celé doménové struktuře zavedeny. Podobně když jsou dvě nebo více doménových struktur spojeny prostřednictvím vztahů důvěryhodnosti doménové struktury, kořenové domény doménové struktury v každé doménové struktuře znají vztahy důvěryhodnosti, které jsou zavedeny ve všech doménách v důvěryhodných doménových strukturách.

### <a name="tdo-password-changes"></a>Změny hesla TDO

Obě domény ve vztahu důvěryhodnosti sdílejí heslo, které je uloženo v objektu TDO ve službě Active Directory. V rámci procesu údržby účtu mění důvěřující řadič domény každých 30 dní heslo uložené v tdo. Vzhledem k tomu, že všechny obousměrné vztahy důvěryhodnosti jsou ve skutečnosti dva jednosměrné vztahy důvěryhodnosti, které jdou opačným směrem, proces probíhá dvakrát pro obousměrné vztahy důvěryhodnosti.

Svěřenský fond má důvěřivou a důvěryhodnou stránku. Na důvěryhodné straně lze pro tento proces použít libovolný řadič domény s zapisovatelný. Na důvěřující straně emulátor Primárního řadiče domény provede změnu hesla.

Chcete-li změnit heslo, dokončí řadiče domény následující proces:

1. Emulátor primárního řadiče domény (PDC) v důvěřující doméně vytvoří nové heslo. Řadič domény v důvěryhodné doméně nikdy neiniciuje změnu hesla. Vždy je iniciován důvěřujícím emulátorem PDC domény.

2. Emulátor Primárního řadiče domény v důvěřující doméně nastaví pole *OldPassword* objektu TDO na aktuální pole *NewPassword.*

3. Emulátor Primárního řadiče domény v důvěřující doméně nastaví pole *NewPassword* objektu TDO na nové heslo. Uchovávání kopie předchozího hesla umožňuje vrátit se ke starému heslu, pokud řadič domény v důvěryhodné doméně změnu neobdrží nebo pokud změna není replikována před vytvořením požadavku, který používá nové heslo důvěryhodnosti.

4. Emulátor Primárního řadiče domény v důvěřující doméně provede vzdálené volání řadiče domény v důvěryhodné doméně a požádá jej o nastavení hesla v účtu důvěryhodnosti na nové heslo.

5. Řadič domény v důvěryhodné doméně změní důvěryhodné heslo na nové heslo.

6. Na každé straně vztahu důvěryhodnosti jsou aktualizace replikovány do ostatních řadičů domény v doméně. V důvěřující doméně změna spustí naléhavou replikaci důvěryhodného objektu domény.

Heslo je nyní změněno v obou řadičích domény. Normální replikace distribuuje objekty TDO do jiných řadičů domény v doméně. Je však možné, aby řadič domény v důvěřující doméně změnil heslo, aniž by úspěšně aktualizoval řadič domény v důvěryhodné doméně. K tomuto scénáři může dojít, protože nelze vytvořit zabezpečený kanál, který je nutný ke zpracování změny hesla. Je také možné, že řadič domény v důvěryhodné doméně nemusí být v určitém okamžiku procesu k dispozici a nemusí obdržet aktualizované heslo.

Chcete-li řešit situace, ve kterých změna hesla není úspěšně sdělena, řadič domény v důvěřující doméně nikdy nezmění nové heslo, pokud úspěšně ověřen (nastavit zabezpečený kanál) pomocí nového hesla. Toto chování je důvod, proč jsou stará i nová hesla uložena v objektu TDO důvěřující domény.

Změna hesla není dokončena, dokud ověření pomocí hesla proběhne úspěšně. Staré uložené heslo lze použít přes zabezpečený kanál, dokud řadič domény v důvěryhodné doméně neobdrží nové heslo, což umožňuje nepřetržitou službu.

Pokud se ověření pomocí nového hesla nezdaří, protože heslo je neplatné, důvěryhodný řadič domény se pokusí ověřit pomocí starého hesla. Pokud se úspěšně ověří pomocí starého hesla, obnoví proces změny hesla do 15 minut.

Aktualizace důvěryhodných hesel je třeba replikovat do řadičů domény na obou stranách vztahu důvěryhodnosti do 30 dnů. Pokud se heslo důvěryhodnosti změní po 30 dnech a řadič domény má pouze heslo N-2, nemůže použít vztah důvěryhodnosti z důvěryhodné strany a nemůže vytvořit zabezpečený kanál na důvěryhodné straně.

## <a name="network-ports-used-by-trusts"></a>Síťové porty používané vztahy důvěryhodnosti

Vzhledem k tomu, že vztahy důvěryhodnosti musí být nasazeny přes různé hranice sítě, může být nutné span jeden nebo více bran firewall. V takovém případě můžete buď tunelovat důvěryhodný provoz přes bránu firewall, nebo otevřít určité porty v bráně firewall, aby přenos mohl projít.

> [!IMPORTANT]
> Služba Active Directory Domain Services nepodporuje omezení přenosů vzdáleného volání procedur služby Active Directory na určité porty.

Přečtěte si část Windows **Server 2008 a novější verze** článku o podpoře společnosti Microsoft Jak [nakonfigurovat bránu firewall pro domény a vztahy důvěryhodnosti služby Active Directory,](https://support.microsoft.com/help/179442/how-to-configure-a-firewall-for-domains-and-trusts) abyste se dozvěděli o portech potřebných pro vztah důvěryhodnosti doménové struktury.

## <a name="supporting-services-and-tools"></a>Podpůrné služby a nástroje

Pro podporu vztahů důvěryhodnosti a ověřování se používají některé další funkce a nástroje pro správu.

### <a name="net-logon"></a>Přihlašování

Služba Přihlašování k síti udržuje zabezpečený kanál z počítače se systémem Windows do řadiče domény. Používá se také v následujících procesech souvisejících s důvěryhodností:

* Nastavení a správa důvěryhodnosti – Přihlašování k síti pomáhá udržovat hesla důvěryhodnosti, shromažďuje informace o důvěryhodnosti a ověřuje vztahy důvěryhodnosti interakcí s procesem LSA a tdo.

    U vztahů důvěryhodnosti doménové struktury zahrnují informace o důvěryhodnosti doménové struktury *(FTInfo),* který zahrnuje sadu oborů názvů, které důvěryhodná doménová struktura označuje ke správě, s anotovaným polem označujícím, zda je každá deklarace důvěryhodná důvěřující doménovou struktury důvěryhodná.

* Ověřování – Poskytuje pověření uživatele prostřednictvím zabezpečeného kanálu řadiči domény a vrátí uživateli sidy domény a uživatelská práva.

* Umístění řadiče domény – pomáhá při hledání nebo vyhledání řadičů domény v doméně nebo napříč doménami.

* Předávací ověření – Pověření uživatelů v jiných doménách jsou zpracována službou Net Logon. Pokud důvěřující doména potřebuje ověřit identitu uživatele, předá pověření uživatele prostřednictvím aplikace Net Logon důvěryhodné doméně k ověření.

* Ověření certifikátu atributu oprávnění (PAC) – Pokud server používající protokol Kerberos pro ověřování potřebuje ověřit certifikát PAC v lístku služby, odešle certifikát PAC přes zabezpečený kanál k ověření svému řadiči domény.

### <a name="local-security-authority"></a>Místní úřad zabezpečení

Místní úřad zabezpečení (LSA) je chráněný subsystém, který uchovává informace o všech aspektech místního zabezpečení v systému. LSA, souhrnně známá jako místní zásady zabezpečení, poskytuje různé služby pro překlad mezi názvy a identifikátory.

Podsystém zabezpečení LSA poskytuje služby v režimu jádra i v uživatelském režimu pro ověřování přístupu k objektům, kontrolu uživatelských oprávnění a generování zpráv auditu. LSA je zodpovědná za kontrolu platnosti všech lístků relace prezentovaných službami v důvěryhodných nebo nedůvěryhodných doménách.

### <a name="management-tools"></a>Nástroje pro správu

Správci mohou pomocí *domén a vztahů důvěryhodnosti služby Active Directory*, *Netdom* a *Nltest* zpřístupnit, vytvořit, odebrat nebo upravit vztahy důvěryhodnosti.

* *Domény a vztahy důvěryhodnosti služby Active Directory* je konzola MMC (MMC), která slouží ke správě vztahů důvěryhodnosti domén, úrovní funkčnosti domény a doménové struktury a přípon hlavních názvů uživatelů.
* Nástroje příkazového řádku *Netdom* a *Nltest* lze použít k vyhledání, zobrazení, vytvoření a správě vztahů důvěryhodnosti. Tyto nástroje komunikují přímo s autoritou LSA na řadiči domény.

## <a name="next-steps"></a>Další kroky

Další informace o doménových strukturách prostředků najdete v tématu [Jak fungují vztahy důvěryhodnosti doménové struktury ve službě Azure AD DS?][concepts-trust]

Pokud chcete začít s vytvářením spravované domény Azure AD DS s doménovou doménovou strukturu prostředků, přečtěte si informace [o vytvoření a konfiguraci spravované domény Služby Azure AD DS][tutorial-create-advanced]. Potom můžete [vytvořit odchozí vztah důvěryhodnosti doménové struktury pro místní doménu (náhled).][create-forest-trust]

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md

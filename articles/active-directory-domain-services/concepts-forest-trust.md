---
title: Jak vztahy důvěryhodnosti fungují pro Azure AD Domain Services | Microsoft Docs
description: Další informace o tom, jak vztah důvěryhodnosti doménové struktury funguje s Azure AD Domain Services
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: 50b400ffa047d3865a9df77912da187de1ce9cc9
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962611"
---
# <a name="how-trust-relationships-work-for-resource-forests-in-azure-active-directory-domain-services"></a>Jak vztahy důvěryhodnosti fungují pro doménové struktury prostředků v Azure Active Directory Domain Services

Active Directory Domain Services (služba AD DS) poskytuje zabezpečení napříč více doménami nebo doménovými strukturami prostřednictvím vztahů důvěryhodnosti domén a doménových struktur. Předtím, než může ověřování probíhat napříč vztahy důvěryhodnosti, musí systém Windows nejdřív ověřit, jestli má doména, kterou uživatel, počítač nebo služba požaduje vztah důvěryhodnosti s doménou žádajícího účtu.

Pro kontrolu tohoto vztahu důvěryhodnosti systém Windows Security vypočítá cestu vztahu důvěryhodnosti mezi řadičem domény (DC) pro server, který obdrží požadavek a řadič domény v doméně žádajícího účtu.

Mechanismy řízení přístupu poskytované služba AD DS a modelem distribuovaného zabezpečení systému Windows poskytují prostředí pro fungování vztahů důvěryhodnosti domény a doménové struktury. Aby tyto vztahy důvěryhodnosti fungovaly správně, musí mít každý prostředek nebo počítač přímo důvěryhodnou cestu k řadiči domény v doméně, ve které se nachází.

Cesta vztahu důvěryhodnosti je implementovaná službou přihlašování k síti pomocí ověřeného připojení RPC (Remote Procedure Call) k důvěryhodné autoritě domény. Zabezpečený kanál se také rozšiřuje na jiné služba AD DS domény prostřednictvím vztahů mezi vztahy důvěryhodnosti mezi doménami. Tento zabezpečený kanál slouží k získání a ověření informací o zabezpečení, včetně identifikátorů zabezpečení (SID) pro uživatele a skupiny.

Přehled o tom, jak se vztahy důvěryhodnosti vztahují na Azure služba AD DS, najdete v tématu [Koncepty a funkce doménové struktury prostředků][create-forest-trust].

Pokud chcete začít používat vztahy důvěryhodnosti v Azure služba AD DS, [vytvořte spravovanou doménu, která používá vztahy důvěryhodnosti doménové struktury][tutorial-create-advanced].

## <a name="trust-relationship-flows"></a>Toky vztahů důvěryhodnosti

Tok zabezpečené komunikace přes vztahy důvěryhodnosti určuje pružnost vztahu důvěryhodnosti. Způsob vytvoření nebo konfigurace vztahu důvěryhodnosti určuje, jak daleko se komunikace rozšiřuje v rámci doménové struktury nebo napříč doménami.

Tok komunikace v rámci vztahů důvěryhodnosti určuje směr vztahu důvěryhodnosti. Vztahy důvěryhodnosti můžou být jednosměrné nebo obousměrné a můžou být přenosné nebo netranzitivní.

Následující diagram znázorňuje, že všechny domény ve *stromové struktuře 1* a *stromu 2* mají ve výchozím nastavení přenositelné vztahy důvěryhodnosti. Výsledkem je, že uživatelé ve *stromové struktuře 1* mají přístup k prostředkům v doménách ve *stromové struktuře 2* a uživatelé ve *stromové struktuře 1* mají přístup k prostředkům ve *stromu 2*, pokud jsou přiřazena správná oprávnění k prostředku.

![Diagram vztahů důvěryhodnosti mezi dvěma doménovými strukturami](./media/concepts-forest-trust/trust-relationships.png)

### <a name="one-way-and-two-way-trusts"></a>Jednosměrné a obousměrné vztahy důvěryhodnosti

Vztahy důvěryhodnosti povolují přístup k prostředkům, které můžou být jednosměrné nebo obousměrné.

Jednosměrný vztah důvěryhodnosti je jednosměrná ověřovací cesta vytvořená mezi dvěma doménami. V jednosměrovém vztahu důvěryhodnosti mezi *doménou* a a *doménou b*můžou uživatelé v *doméně a* přistupovat k prostředkům v *doméně b*. Uživatelé v *doméně B* ale nemůžou získat přístup k prostředkům v *doméně A*.

Některé jednosměrné vztahy důvěryhodnosti mohou být buď netranzitivní, nebo přechodné v závislosti na typu vytvářeného vztahu důvěryhodnosti.

V obousměrném *vztahu důvěryhodnosti doména důvěřuje* *doméně B* a *doména b* důvěřuje *doméně a*. Tato konfigurace znamená, že požadavky na ověřování se dají mezi oběma doménami předávat v obou směrech. Některé obousměrné relace mohou být v závislosti na typu vytvářeného vztahu důvěryhodnosti netranzitivní nebo přechodné.

Všechny vztahy důvěryhodnosti domén v doménové struktuře služba AD DS jsou obousměrné a přenositelné vztahy důvěryhodnosti. Když se vytvoří nová podřízená doména, mezi novou podřízenou doménou a nadřazenou doménou se automaticky vytvoří obousměrný přenositelný vztah důvěryhodnosti.

### <a name="transitive-and-non-transitive-trusts"></a>Přenosné a netranzitivní vztahy důvěryhodnosti

Přenositelnost určuje, zda je možné vztah důvěryhodnosti rozšířit mimo dvě domény, se kterými byl vytvořen.

* Přenosná důvěra se dá použít k rozšiřování vztahů důvěryhodnosti s ostatními doménami.
* Netranzitivní vztah důvěryhodnosti lze použít k odepření vztahů důvěryhodnosti s jinými doménami.

Pokaždé, když vytvoříte novou doménu v doménové struktuře, automaticky se vytvoří obousměrný přenositelný vztah důvěryhodnosti mezi novou doménou a nadřazenou doménou. Pokud se do nové domény přidají podřízené domény, prochází cesta vztahu důvěryhodnosti směrem nahoru v doménové hierarchii, která rozšiřuje počáteční cestu důvěryhodnosti vytvořenou mezi novou doménou a nadřazenou doménou. Vztahy s přenositelnými vztahy důvěryhodnosti procházejí při vytváření doménového stromu směrem nahoru a vytvářejí přenositelné vztahy důvěryhodnosti mezi všemi doménami ve stromu domén.

Žádosti o ověření následují tyto cesty důvěryhodnosti, takže účty z libovolné domény v doménové struktuře se dají ověřit v jakékoli jiné doméně v doménové struktuře. Při jednotném procesu přihlašování mají účty s řádnými oprávněními přístup k prostředkům v libovolné doméně v doménové struktuře.

## <a name="forest-trusts"></a>Vztahy důvěryhodnosti doménové struktury

Vztahy důvěryhodnosti doménové struktury vám pomůžou spravovat segmentované služba AD DS infrastruktury a podporovat přístup k prostředkům a dalším objektům v několika doménových strukturách. Vztahy důvěryhodnosti doménové struktury jsou užitečné pro poskytovatele služeb, společnosti, které procházejí fúzí nebo akvizicou, obchodní extranety pro spolupráci a společnosti, které hledají řešení pro administrativní autonomii.

Pomocí vztahů důvěryhodnosti doménové struktury můžete propojit dvě různé doménové struktury a vytvořit tak jednosměrný nebo obousměrný vztah s obousměrným vztahem důvěryhodnosti. Vztah důvěryhodnosti doménové struktury umožňuje správcům propojit dvě služba AD DS doménových struktur s jedním vztahem důvěryhodnosti a zajistit tak bezproblémové ověřování a možnosti autorizace napříč doménovými strukturami.

Vztah důvěryhodnosti doménové struktury je možné vytvořit jenom mezi kořenovou doménou struktury v jedné doménové struktuře a kořenovou doménou struktury v jiné doménové struktuře. Vztahy důvěryhodnosti doménové struktury je možné vytvořit jenom mezi dvěma doménovými strukturami a nedá se implicitně rozšířit na třetí doménovou strukturu. To znamená, že pokud se vytvoří vztah důvěryhodnosti doménové struktury mezi doménovou strukturou *1* a doménovou strukturou *2*a vytvoří se další vztah důvěryhodnosti doménové struktury mezi *doménovou strukturou 2* a *doménovou* *strukturou*3, *doménová struktura 1* nemá implicitní vztah důvěryhodnosti

Následující diagram znázorňuje dva samostatné vztahy důvěryhodnosti doménové struktury mezi třemi služba AD DS doménovou strukturou v jedné organizaci.

![Diagram vztahů důvěryhodnosti doménové struktury v rámci jedné organizace](./media/concepts-forest-trust/forest-trusts-diagram.png)

Tato příklad konfigurace poskytuje následující přístup:

* Uživatelé v *doménové struktuře 2* mají přístup k prostředkům v libovolné doméně v *doménové struktuře 1* nebo *doménové struktuře 3* .
* Uživatelé v *doménové struktuře 3* mají přístup k prostředkům v libovolné doméně v *doménové struktuře 2* .
* Uživatelé v *doménové struktuře 1* mají přístup k prostředkům v libovolné doméně v *doménové struktuře 2* .

Tato konfigurace neumožňuje uživatelům v *doménové struktuře 1* přistupovat k prostředkům v *doménové struktuře 3* nebo naopak. Chcete-li uživatelům v *doménové struktuře 1* a *doménové struktuře 3* dovolit sdílení prostředků, je třeba vytvořit obousměrný tranzitivní vztah důvěryhodnosti mezi dvěma doménovými strukturami.

Pokud je mezi dvěma doménovými strukturami vytvořen jednosměrný vztah důvěryhodnosti doménové struktury, můžou členové důvěryhodné doménové struktury využívat prostředky umístěné v důvěřující doménové struktuře. Vztah důvěryhodnosti však pracuje pouze v jednom směru.

Například když se vztah důvěryhodnosti doménové struktury vytvoří mezi *doménovou* strukturou 1 (důvěryhodná doménová struktura) a *doménovou* strukturou 2 (důvěřující doménová struktura):

* Členové *doménové struktury 1* mají přístup k prostředkům umístěným v *doménové struktuře 2*.
* Členové *doménové struktury 2* nemůžou získat přístup k prostředkům umístěným v *doménové struktuře 1* pomocí stejného vztahu důvěryhodnosti.

> [!IMPORTANT]
> Azure AD Domain Services doménová struktura prostředků podporuje pouze jednosměrnou důvěryhodnost doménové struktury k místní službě Active Directory.

### <a name="forest-trust-requirements"></a>Požadavky vztahu důvěryhodnosti doménové struktury

Než budete moct vytvořit vztah důvěryhodnosti doménové struktury, musíte ověřit, jestli máte zavedenou správnou infrastrukturu DNS (Domain Name System). Vztahy důvěryhodnosti doménové struktury lze vytvořit pouze v případě, že je k dispozici jedna z následujících konfigurací služby DNS:

* Jeden kořenový server DNS je kořenový server DNS pro oba obory názvů DNS doménové struktury – kořenová zóna obsahuje delegování pro každý obor názvů DNS a kořenové odkazy všech serverů DNS zahrnuje kořenový server DNS.
* Pokud není žádný sdílený kořenový server DNS a kořenové servery DNS v každém oboru názvů DNS doménové struktury, použijte k Směrování dotazů na názvy v jiném oboru názvů DNS podmíněné předávací služby DNS.

    > [!IMPORTANT]
    > Tato konfigurace DNS musí používat Azure AD Domain Services doménová struktura prostředků. Hostování jiného oboru názvů DNS, než je obor názvů DNS doménové struktury prostředků, není funkce Azure AD Domain Services. Je vhodná konfigurace pro podmíněné dopředné.

* Pokud není žádný sdílený kořenový server DNS a kořenové servery DNS v každém oboru názvů DNS doménové struktury, v každém oboru názvů DNS se nakonfigurují sekundární zóny DNS na směrování dotazů na názvy v jiném oboru názvů.

Chcete-li vytvořit vztah důvěryhodnosti doménové struktury, musíte být členem skupiny Domain Admins (v kořenové doméně doménové struktury) nebo skupiny Enterprise Admins ve službě Active Directory. Každému vztahu důvěryhodnosti je přiřazeno heslo, které musí znát správci v obou doménových strukturách. Členové skupiny Enterprise Admins v obou doménových strukturách můžou vytvářet vztahy důvěryhodnosti v obou doménových strukturách najednou a v tomto případě je heslo, které je kryptograficky náhodné, automaticky vygenerováno a napsáno pro obě doménové struktury.

V Azure Portal se vytvoří odchozí vztah důvěryhodnosti doménové struktury pro Azure AD Domain Services. Nemusíte ručně vytvořit vztah důvěryhodnosti se samotnou spravovanou doménou. Příchozí vztah důvěryhodnosti doménové struktury musí být nakonfigurovaný uživatelem s oprávněními, která se dřív poznamenala v místní službě Active Directory.

## <a name="trust-processes-and-interactions"></a>Vztahy důvěryhodnosti procesů a interakcí

Mnoho transakcí mezi doménami a mezi doménovými strukturami závisí na vztahu důvěryhodnosti domény nebo doménové struktury, aby bylo možné dokončit různé úlohy. V této části jsou popsány procesy a interakce, ke kterým dochází v případě, že se k prostředkům přistupovaly v rámci vztahů důvěryhodnosti.

### <a name="overview-of-authentication-referral-processing"></a>Přehled zpracování odkazů ověřování

Pokud je žádost o ověření odkazována na doménu, musí řadič domény v této doméně určit, zda existuje vztah důvěryhodnosti s doménou, ze které pochází požadavek. Aby bylo možné získat přístup k prostředkům v doméně, je třeba určit směr vztahu důvěryhodnosti a informace o tom, zda je vztah důvěryhodnosti nebo nepřenosný. Proces ověřování, který probíhá mezi důvěryhodnými doménami, se liší podle používaného ověřovacího protokolu. Protokoly Kerberos V5 a NTLM zpracovávají odkazy pro ověřování do domény odlišně.

### <a name="kerberos-v5-referral-processing"></a>Zpracování odkazů protokolu Kerberos V5

Ověřovací protokol Kerberos V5 závisí na službě přihlášení k síti na řadičích domény pro informace o ověřování a autorizaci klientů. Protokol Kerberos se připojuje ke službě KDC (online služba KDC (Key Distribution Center)) a úložišti účtů služby Active Directory pro lístky relací.

Protokol Kerberos používá také vztahy důvěryhodnosti pro služby udělování lístků (TGS) mezi sférami a k ověřování certifikátů atributů oprávnění (PACs) napříč zabezpečeným kanálem. Protokol Kerberos provádí ověřování mezi sférami jenom s sférami protokolu Kerberos v operačním systému, které nepoužívají Windows, jako je například sféra protokolu MIT Kerberos, a nemusí komunikovat se službou přihlašování k síti.

Pokud klient používá k ověřování protokol Kerberos V5, požádá o lístek na server v cílové doméně z řadiče domény ve své doméně účtu. Služba KDC protokolu Kerberos funguje jako důvěryhodný prostředník mezi klientem a serverem a poskytuje klíč relace, který oběma stranám umožňuje vzájemné ověřování. Pokud je cílová doména odlišná od aktuální domény, služba KDC sleduje logický proces a určí, jestli se dá odkazovat na žádost o ověření:

1. Je aktuální doména důvěryhodná přímo doménou serveru, který je požadován?
    * Pokud ano, pošlete klientovi odkaz na požadovanou doménu.
    * Pokud ne, pokračujte na další krok.

2. Existuje mezi aktuální doménou a další doménou v cestě vztahu důvěryhodnosti vztah přenosné důvěryhodnosti?
    * Pokud ano, pošlete klientovi odkaz na další doménu v cestě důvěryhodnosti.
    * Pokud ne, pošlete klientovi zprávu o odepřeném přihlášení.

### <a name="ntlm-referral-processing"></a>Zpracování odkazů protokolu NTLM

Ověřovací protokol NTLM závisí na službě přihlášení k síti na řadičích domény pro informace o ověřování a autorizaci klientů. Tento protokol ověřuje klienty, kteří nepoužívají ověřování pomocí protokolu Kerberos. Protokol NTLM používá vztahy důvěryhodnosti k předávání žádostí o ověření mezi doménami.

Pokud klient používá pro ověřování protokol NTLM, počáteční požadavek na ověření směřuje přímo z klienta na server prostředků v cílové doméně. Tento server vytvoří výzvu, na kterou klient odpoví. Server pak pošle reakci uživatele na řadič domény v doméně svého účtu počítače. Tento řadič domény zkontroluje uživatelský účet s databází zabezpečení účtů.

Pokud účet v databázi neexistuje, řadič domény určí, jestli se má provést předávací ověřování, předání žádosti nebo zamítnutí žádosti pomocí následující logiky:

1. Má aktuální doména vztah s přímým vztahem důvěryhodnosti s doménou uživatele?
    * Pokud ano, řadič domény pošle přihlašovací údaje klienta k řadiči domény v doméně uživatele pro předávací ověřování.
    * Pokud ne, pokračujte na další krok.

2. Má aktuální doména vztah přenosného vztahu důvěryhodnosti s doménou uživatele?
    * Pokud ano, předejte žádost o ověření k další doméně v cestě vztahu důvěryhodnosti. Tento řadič domény tento proces zopakuje kontrolou přihlašovacích údajů uživatele proti vlastní databázi účtů zabezpečení.
    * Pokud ne, pošlete klientovi zprávu o odepřeném přihlášení.

### <a name="kerberos-based-processing-of-authentication-requests-over-forest-trusts"></a>Zpracování žádostí o ověření pomocí protokolu Kerberos na základě vztahů důvěryhodnosti doménové struktury

Pokud je mezi doménovými strukturami propojeny dvě doménové struktury, požadavky na ověřování vytvořené pomocí protokolů Kerberos V5 nebo NTLM je možné směrovat mezi doménovou strukturou, aby poskytovaly přístup k prostředkům v obou doménových strukturách.

Při prvním navázání vztahu důvěryhodnosti doménové struktury shromáždí každá doménová struktura všechny důvěryhodné obory názvů ve své partnerské struktuře partnerů a uloží je do [objektu důvěryhodné domény](#trusted-domain-object). Mezi důvěryhodné obory názvů patří názvy doménových struktur, přípony hlavního názvu uživatele (UPN), přípony hlavního názvu služby (SPN) a obory názvů identifikátoru zabezpečení (SID) používané v jiné doménové struktuře. Objekty objektu pro replikaci se replikují do globálního katalogu.

Než mohou ověřovací protokoly sledovat cestu vztahu důvěryhodnosti doménové struktury, musí být hlavní název služby (SPN) počítače prostředku přeložen do umístění v jiné doménové struktuře. Hlavní název služby (SPN) může být jeden z následujících názvů:

* Název DNS hostitele.
* Název DNS domény.
* Rozlišující název objektu spojovacího bodu služby.

Když se pracovní stanice v jedné doménové struktuře pokusí získat přístup k datům v počítači prostředků v jiné doménové struktuře, proces ověřování protokolu Kerberos kontaktuje řadič domény pro lístek služby na hlavní název služby (SPN) počítače prostředku. Jakmile řadič domény zadá dotaz na globální katalog a zjistí, že hlavní název služby není ve stejné doménové struktuře jako řadič domény, odešle řadič domény odkaz na svou nadřazenou doménu zpátky na pracovní stanici. V tomto okamžiku pracovní stanice zadá dotaz na nadřazenou doménu pro lístek služby a pokračuje podle řetězce odkazů, dokud nedosáhne domény, ve které je prostředek umístěný.

Následující diagram a kroky poskytují podrobný popis procesu ověřování protokolu Kerberos, který se používá, když se počítače se systémem Windows pokusí získat přístup k prostředkům z počítače umístěného v jiné doménové struktuře.

![Diagram procesu Kerberos přes vztah důvěryhodnosti doménové struktury](media/concepts-forest-trust/kerberos-over-forest-trust-process-diagram.png)

1. *Uživatel1* se přihlásí k *Workstation1* pomocí přihlašovacích údajů z domény *Europe.tailspintoys.com* . Uživatel se pak pokusí získat přístup ke sdílenému prostředku ve složce *Server1* umístěné v doménové struktuře *USA.wingtiptoys.com* .

2. *Workstation1* kontaktuje službu KDC protokolu Kerberos v řadiči domény ve své doméně, *ChildDC1*a požádá o lístek služby pro *hlavní název* služby (SPN).

3. *ChildDC1* nenajde hlavní název služby (SPN) v doméně databáze a dotazuje se na globální katalog, aby bylo možné zjistit, zda některé domény v doménové struktuře *tailspintoys.com* obsahují tento název SPN. Vzhledem k tomu, že globální katalog je omezený na vlastní doménovou strukturu, hlavní název služby se nenalezne.

    Globální katalog potom zkontroluje svou databázi, kde zjistí informace o všech vztazích důvěryhodnosti doménové struktury, které jsou vytvořeny s doménovou strukturou. Pokud se najde, porovná přípony názvů uvedené v doménové struktuře Trust Object (důvěryhodné domény) k příponě cílového hlavního názvu služby (SPN), aby nalezla shodu. Po nalezení shody globální katalog poskytne doporučení směrování zpátky na *ChildDC1*.

    Pomocné parametry směrování vám pomůžou s přímými požadavky na ověřování směrem k cílové doménové struktuře. Pomocné parametry se používají jenom v případě, že se hlavní název služby (SPN) nepodaří najít u všech tradičních ověřovacích kanálů, jako je například místní řadič domény a pak globální katalog.

4. *ChildDC1* odešle odkaz na svou nadřazenou doménu zpět na *Workstation1*.

5. *Workstation1* kontaktuje řadič domény v *ForestRootDC1* (jeho nadřazená doména) pro odkaz na řadič domény (*ForestRootDC2*) v kořenové doméně doménové struktury *wingtiptoys.com* .

6. *Workstation1* kontakty *ForestRootDC2* v doménové struktuře *wingtiptoys.com* pro lístek služby na požadovanou službu.

7. *ForestRootDC2* kontaktuje svůj globální katalog, aby našel hlavní název služby (SPN), a globální katalog najde shodu pro hlavní název služby (SPN) a odešle ho zpátky do *ForestRootDC2*.

8. *ForestRootDC2* pak odešle odkaz na *USA.wingtiptoys.com* zpět na *Workstation1*.

9. *Workstation1* kontaktuje službu KDC na *ChildDC2* a vyjednává lístek pro *Uživatel1* , aby získal přístup k souborům *Server1*.

10. Jakmile *Workstation1* má lístek služby, pošle lístek služby do příkazu *Server1*, který přečte přihlašovací údaje zabezpečení *user1*a podle toho vytvoří token pro přístup.

## <a name="trusted-domain-object"></a>Objekt důvěryhodné domény

Každý vztah důvěryhodnosti domény nebo doménové struktury v rámci organizace je reprezentován objektem důvěryhodné domény, který je uložený v kontejneru *System* v rámci příslušné domény.

### <a name="tdo-contents"></a>Obsah pro

Informace obsažené v poli se liší v závislosti na tom, jestli byl objekt pro vytváření domén vytvořený pomocí vztahu důvěryhodnosti domény nebo vztahu důvěryhodnosti doménové struktury.

Když se vytvoří vztah důvěryhodnosti domény, ve kterém se nachází atributy, jako je název domény DNS, identifikátor SID domény, typ důvěryhodnosti, Přenositelnost vztahu důvěryhodnosti a název protější domény. Vztah důvěryhodnosti doménové struktury TDOs ukládá další atributy pro identifikaci všech důvěryhodných oborů názvů z partnerské struktury partnera. Mezi tyto atributy patří názvy doménových struktur, přípony hlavního názvu uživatele (UPN), přípony hlavního názvu služby (SPN) a obory názvů IDENTIFIKÁTORu zabezpečení (SID).

Vzhledem k tomu, že vztahy důvěryhodnosti jsou uloženy ve službě Active Directory jako TDOs, všechny domény v doménové struktuře mají znalosti o vztazích vztahů důvěryhodnosti, které jsou umístěny v celé doménové struktuře. Podobně platí, že pokud jsou dvě nebo více doménových struktur propojených prostřednictvím vztahů důvěryhodnosti doménové struktury, kořenové domény doménové struktury v každé doménové struktuře mají znalosti o vztazích vztahů důvěryhodnosti, které jsou umístěny v rámci všech domén v důvěryhodných doménových strukturách.

### <a name="tdo-password-changes"></a>Změny hesla pro heslo

Obě domény ve vztahu důvěryhodnosti sdílejí heslo, které je uloženo v objektu objektů úložiště ve službě Active Directory. V rámci procesu údržby účtu každých 30 dnů změní důvěřující řadič domény heslo uložené v řadiči domény. Vzhledem k tomu, že všechny obousměrné vztahy důvěryhodnosti jsou ve skutečnosti 2 1 vztahy důvěryhodnosti v opačném směru, proces se pro oboustranné vztahy důvěryhodnosti vyskytuje dvakrát.

Vztah důvěryhodnosti má důvěřující a důvěryhodnou stranu. Na straně důvěryhodné se dá k procesu použít libovolný zapisovatelný řadič domény. Na straně vztahu důvěryhodnosti provede emulátor primárního řadiče domény změnu hesla.

K provedení změny hesla řadiče domény dokončí následující postup:

1. Emulátor primárního řadiče domény v důvěřující doméně vytvoří nové heslo. Řadič domény v důvěryhodné doméně nikdy neinicializuje změnu hesla. Je vždy iniciována důvěryhodným emulátorem primárního řadiče domény.

2. Emulátor primárního řadiče domény v důvěřující doméně nastaví pole *oldPassword* objektu na aktuální pole *nové_heslo* .

3. Emulátor primárního řadiče domény v důvěřující doméně nastaví pole *nové_heslo* objektu na nové heslo. Když si kopii předchozího hesla zachováte, můžete se vrátit k původnímu heslu v případě, že se řadič domény v důvěryhodné doméně nepovede změnit, nebo pokud se změna nereplikuje předtím, než se vytvoří žádost, která používá nové heslo vztahu důvěryhodnosti.

4. Emulátor primárního řadiče domény v důvěřující doméně vytvoří vzdálené volání řadiče domény v důvěryhodné doméně, aby na něj nastavila heslo pro účet důvěry na nové heslo.

5. Řadič domény v důvěryhodné doméně změní heslo vztahu důvěryhodnosti na nové heslo.

6. Na každé straně vztahu důvěryhodnosti se aktualizace replikují do ostatních řadičů domény v doméně. V důvěřující doméně vyvolá změna naléhavou replikaci objektu důvěryhodné domény.

Heslo je nyní změněno na obou řadičích domény. Normální replikace distribuuje objekty objektu datacontroller na ostatní řadiče domény v doméně. Je ale možné, že řadič domény v důvěřující doméně změní heslo, aniž by se úspěšně aktualizoval řadič domény v důvěryhodné doméně. K tomuto scénáři může dojít, protože zabezpečený kanál, který je nutný ke zpracování změny hesla, se nedal zřídit. Je také možné, že řadič domény v důvěryhodné doméně nemusí být v určitém okamžiku během procesu k dispozici a nemusí získat aktualizované heslo.

Pokud se chcete zabývat situací, kdy se změna hesla úspěšně nekomunikuje, řadič domény v důvěřující doméně nikdy nemění nové heslo, pokud se úspěšně neověřil (nastavení zabezpečeného kanálu) pomocí nového hesla. To je důvod, proč se stará i nová hesla uchovávají v objektu důvěryhodné domény.

Změna hesla není dokončena, dokud nebude ověřování pomocí hesla úspěšné. Staré uložené heslo lze použít přes zabezpečený kanál, dokud řadič domény v důvěryhodné doméně neobdrží nové heslo, čímž povolí nepřerušenou službu.

Pokud ověřování pomocí nového hesla neproběhne úspěšně, protože heslo je neplatné, pokusí se důvěřující řadič domény ověřit pomocí starého hesla. Pokud se úspěšně ověřuje pomocí starého hesla, pokračuje proces změny hesla během 15 minut.

Aktualizace hesel trustu se musí replikovat na řadiče domény obou stran důvěry do 30 dnů. Pokud je heslo vztahu důvěryhodnosti změněno po 30 dnech a řadič domény má pouze heslo N-2, nemůže použít vztah důvěryhodnosti ze strany vztahu důvěryhodnosti a nemůže vytvořit zabezpečený kanál na důvěryhodné straně.

## <a name="network-ports-used-by-trusts"></a>Síťové porty používané vztahy důvěryhodnosti

Vzhledem k tomu, že vztahy důvěryhodnosti musí být nasazeny napříč různými hranicemi sítě, může být nutné rozložit jednu nebo více bran firewall. Pokud se jedná o tento případ, můžete buď tunelovat vztah důvěryhodnosti přes bránu firewall nebo otevřít konkrétní porty v bráně firewall, aby bylo možné předávání provozu.

> [!IMPORTANT]
> Active Directory Domain Services nepodporuje omezení provozu služby Active Directory RPC na konkrétní porty.

Přečtěte si část podpora Microsoftu článku o **Windows serveru 2008 a novějších verzích** , [jak nakonfigurovat bránu firewall pro domény služby Active Directory a vztahy důvěryhodnosti](https://support.microsoft.com/help/179442/how-to-configure-a-firewall-for-domains-and-trusts) , abyste se dozvěděli o portech potřebných pro vztah důvěryhodnosti doménové struktury.

## <a name="supporting-services-and-tools"></a>Podpůrné služby a nástroje

Pro podporu vztahů důvěryhodnosti a ověřování se používají některé další funkce a nástroje pro správu.

### <a name="net-logon"></a>Přihlášení k síti

Služba přihlášení k síti udržuje zabezpečený kanál z počítače se systémem Windows do řadiče domény. Používá se také v následujících procesech souvisejících s důvěryhodností:

* Nastavení a Správa důvěryhodnosti – přihlášení k síti pomáhá udržovat hesla důvěry, shromažďuje informace o důvěryhodnosti a ověřuje vztahy důvěryhodnosti pomocí interakce s procesem LSA a s DOMÉNou.

    U vztahů důvěryhodnosti doménové struktury obsahují informace o vztahu důvěryhodnosti záznam o vztahu důvěryhodnosti doménové struktury (*FTInfo*), který zahrnuje sadu oborů názvů, které důvěryhodná doménová struktura spravuje, s použitím pole, které indikuje, jestli je každá deklarace identity důvěřující doménové struktuře.

* Ověřování – poskytuje přihlašovací údaje uživatele přes zabezpečený kanál k řadiči domény a vrací identifikátory SID domény a uživatelská práva pro uživatele.

* Umístění řadiče domény – pomáhá najít nebo vyhledat řadiče domény v doméně nebo napříč doménami.

* Předávací ověřování – přihlašovací údaje uživatelů v jiných doménách jsou zpracovávány pomocí příkazu Net Logon. Když důvěřující doména potřebuje ověřit identitu uživatele, předá přihlašovací údaje uživatele pomocí příkazu Net Logon k důvěryhodné doméně za účelem ověření.

* Ověření certifikátu PAC (Privileged Certificate) – Pokud server, který používá protokol Kerberos pro ověřování, musí ověřit PAC v lístku služby, pošle PAC přes zabezpečený kanál na jeho řadič domény a ověří tak ověření.

### <a name="local-security-authority"></a>Místní úřad zabezpečení

Místní úřad zabezpečení (LSA) je chráněný podsystém, který uchovává informace o všech aspektech místního zabezpečení v systému. V zásadě označované jako místní zásady zabezpečení poskytuje LSA různé služby pro překlad mezi názvy a identifikátory.

Subsystém zabezpečení LSA poskytuje služby v režimu jádra i v uživatelském režimu pro ověřování přístupu k objektům, kontrolu uživatelských oprávnění a generování zpráv auditu. LSA zodpovídá za kontrolu platnosti všech lístků relací prezentovaných službami v důvěryhodných nebo nedůvěryhodných doménách.

### <a name="management-tools"></a>Nástroje pro správu

Správci můžou *domény a vztahy důvěryhodnosti služby Active Directory*používat *Netdom* *k* vystavení, vytváření, odebírání a úpravám vztahů důvěryhodnosti.

* *Domény a vztahy důvěryhodnosti služby Active Directory* je konzola MMC (Microsoft Management Console), která slouží ke správě vztahů důvěryhodnosti domén, úrovní funkčnosti domény a doménové struktury a přípon hlavního názvu uživatele.
* Nástroje příkazového řádku *netdom* a *Nltest* lze použít k vyhledání, zobrazení, vytvoření a správě vztahů důvěryhodnosti. Tyto nástroje komunikují přímo s autoritou LSA na řadiči domény.

## <a name="next-steps"></a>Další kroky

Další informace o doménových strukturách prostředků najdete v tématu [jak vztahy důvěryhodnosti doménové struktury fungují v Azure služba AD DS?][concepts-trust]

Informace o vytváření spravované domény pomocí doménové struktury prostředků najdete v tématu [Vytvoření a konfigurace spravované domény Azure služba AD DS][tutorial-create-advanced]. Pak můžete [vytvořit vztah důvěryhodnosti odchozí doménové struktury k místní doméně][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md

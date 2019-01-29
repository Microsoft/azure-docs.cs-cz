---
title: Hybridní identita Návrh – strategie přijetí Azure | Dokumentace Microsoftu
description: Pomocí řízení podmíněného přístupu Azure Active Directory ověří zvláštní podmínky, kterou vyberete při ověřování uživatele a před povolením přístupu k aplikaci. Po splnění těchto podmínek, uživatel ověří a povolí se přístup k aplikaci.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: b92fa5a9-c04c-4692-b495-ff64d023792c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: d58f789461b761431bd86dce60b2cd059ca8df53
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55164328"
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>Definování strategie přijetí hybridní identity
V této úloze definujete strategie přijetí hybridních identit pro hybridní řešení identit plnění obchodních požadavků, které byly zmíněny v:

* [Určit, potřeb firmy](plan-hybrid-identity-design-considerations-business-needs.md)
* [Určete požadavky na synchronizaci adresáře](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [Určení požadavků na ověření službou Multi-Factor Authentication](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Definování strategie pro obchodní potřeby
První úloha adresy určení obchodní organizace potřebuje.  To může být velmi široké a nepřesný může dojít, pokud si nejste opatrní.  Na začátku je všechno jednoduché, ale nezapomeňte naplánovat pro návrh, který se podle a usnadněte změnu v budoucnu.  Bez ohledu na to, zda je jednoduchý návrhu nebo velmi komplexní, Azure Active Directory je platforma Microsoft Identity, která podporuje Office 365, Microsoft Online Services a cloudové aplikace.

## <a name="define-an-integration-strategy"></a>Definování strategie pro integraci
Microsoft má tři hlavní integrační scénáře, které jsou federované identity, cloudové identity a synchronizované identity.  Měli byste počítat s přijetím jedné z těchto strategií integrace.  Strategie, kterou zvolíte se může lišit a může zahrnovat rozhodnutí, která při výběru jednoho, jaký typ činnost koncového uživatele, které byste chtěli poskytnout, máte stávající infrastrukturu, a co je nejúčinnějším.  

![](./media/plan-hybrid-identity-design-considerations/integration-scenarios.png)

Scénářích definovaných na obrázku výše jsou:

* **Cloudové identity**: Jedná se o identitách, které existují pouze v cloudu.  V případě Azure AD kde by se nacházejí, konkrétně v adresáři služby Azure AD.
* **Synchronizovat**: Jedná se o identitách, které existují v místním prostředí i v cloudu.  Pomocí služby Azure AD Connect, tito uživatelé jsou buď vytvořit nebo připojit k existující účty Azure AD.  Hodnota hash hesla uživatele je synchronizované z místního prostředí do cloudu, v jaké se nazývá hodnoty hash hesla.  Při použití synchronizaci jeden výstrahou je, že pokud je uživatel zakázán v místním prostředí, může trvat až tři hodiny stav tohoto účtu se zobrazí ve službě Azure AD.  Příčinou je časový interval synchronizace.
* **Federované**: tyto identity neexistuje v místním prostředí i v cloudu.  Pomocí služby Azure AD Connect, tito uživatelé jsou buď vytvořit nebo připojit k existující účty Azure AD.  

> [!NOTE]
> Další informace o možnosti synchronizace, najdete v článku [integrace místních identit s Azure Active Directory](whatis-hybrid-identity.md).
> 
> 

V následující tabulce pomáhá při určování, výhody a nevýhody každé z následujících strategií:

| Strategie | Výhody | Nevýhody |
| --- | --- | --- |
| **Cloudové identity** |Jednodušší spravovat pro menší organizace. <br> Nic k místní instalaci. Žádný další hardware potřeby<br>Snadno k dispozici, pokud uživatel odejde ze společnosti |Uživatelé budou muset přihlásit, když přistupoval k úlohy v cloudu <br> Hesla mohou nebo nemusí být stejné pro cloudové a místní identity |
| **Synchronizovat** |Místní heslo ověří místních i cloudových adresářích <br>Jednodušší spravovat pro malé, střední nebo velké firmy <br>Uživatelé mají jednotné přihlašování (SSO) pro některé zdroje <br> Microsoft preferované metody pro synchronizaci <br> Snadnější správa |Někteří zákazníci mohou mít zdráhají synchronizace jejich adresářů ke cloudu kvůli policejní konkrétní společnosti |
| **Federované** |Uživatelé mohou mít jednotné přihlašování (SSO) <br>Pokud uživatel je ukončen nebo opustí, můžete okamžitě zakázat účet a odvolat přístup<br> Podporuje rozšířené scénáře, které nelze provést pomocí synchronizovat |Další kroky k nastavení a konfigurace <br> Vyšší údržby <br> Může vyžadovat další hardware pro infrastrukturu služby tokenů zabezpečení <br> Může vyžadovat další hardware pro instalaci federační server. Další software je vyžadována, pokud se používá služba AD FS <br> Vyžadovat rozsáhlé instalační program pro jednotné přihlašování <br> Kritický bod selhání, pokud je federační server je vypnutý, uživatelé nebudou moci ověřit |

### <a name="client-experience"></a>Možnosti klienta
Strategie, kterou použijete, bude určovat přihlašování uživatelů.  Následující tabulky poskytují informace o jaké uživatelů byste očekávat, že jejich přihlašovací prostředí bude.  Ne všichni poskytovatelé federované identity podporují jednotné přihlašování ve všech scénářích.

**Doména připojený a privátní síťové aplikace**:

|  | Synchronizované Identity | Federated Identity |
| --- | --- | --- |
| Webové prohlížeče |Ověřování pomocí formulářů |jednotné přihlašování, někdy požádáni o zadání ID organizace |
| Outlook |Vyzvat k zadání pověření |Vyzvat k zadání pověření |
| Skype pro firmy (Lync) |Vyzvat k zadání pověření |jednotné přihlašování služby Lync, zobrazí výzva přihlašovací údaje pro server Exchange |
| OneDrive pro firmy |Vyzvat k zadání pověření |Jednotné přihlašování |
| Office Pro Plus předplatné |Vyzvat k zadání pověření |Jednotné přihlašování |

**Externí nebo nedůvěryhodných zdrojů**:

|  | Synchronizované Identity | Federated Identity |
| --- | --- | --- |
| Webové prohlížeče |Ověřování pomocí formulářů |Ověřování pomocí formulářů |
| Aplikace Outlook, Skype pro firmy (Lync), OneDrive pro firmy, předplatné Office |Vyzvat k zadání pověření |Vyzvat k zadání pověření |
| Exchange ActiveSync |Vyzvat k zadání pověření |jednotné přihlašování služby Lync, zobrazí výzva přihlašovací údaje pro server Exchange |
| Mobilní aplikace |Vyzvat k zadání pověření |Vyzvat k zadání pověření |

Pokud zjistíte z úlohy 1, abyste měli třetí strany zprostředkovatele identity nebo jsou budete používat jeden k poskytování federace se službou Azure AD, musíte mít na paměti následující podporované funkce:

* Všechny poskytovatele SAML 2.0, který je kompatibilní pro profil SP Lite podporuje ověřování Azure AD a přidružené aplikace
* Podporuje pasivní ověřování, které usnadňuje ověřování aplikace OWA, SPO atd.
* Exchange Online klientů mohou být podporovány prostřednictvím SAML 2.0 rozšířeného klienta profilu (ECP)

Musíte taky vědět, jaké funkce nebudou k dispozici:

* Bez podpory důvěryhodnosti/WS-Federation přerušení všech aktivních klientů
  * To znamená, že žádné klienta Lyncu, klient Onedrivu, předplatné Office, Office Mobile před Office 2016
* Přechod z Office pasivní režim ověřování umožňuje jejich podporují čistě zprostředkovatelů identity SAML 2.0, ale každý klient klient stále bude podpora

> [!NOTE]
> Nejaktuálnější seznam najdete v článku [seznam kompatibilit pro federaci Azure AD](how-to-connect-fed-compatibility.md).
> 
> 

## <a name="define-synchronization-strategy"></a>Definování strategie pro synchronizaci
V této úloze budete definovat nástroje, které budou použity k synchronizaci organizace místních dat do cloudu a co byste měli používat topologii.  Protože většina organizací používá služby Active Directory, informace o používání služby Azure AD Connect na výše uvedené otázky najdete v některé podrobnosti.  Pro prostředí, které nemají služby Active Directory je informace o použití FIM 2010 R2 nebo MIM 2016, abyste mohli naplánovat tuto strategii.  Ale budoucích verzí služby Azure AD Connect bude podporovat adresáře LDAP, takže v závislosti na vaší časové ose, tyto informace mohou být schopni pomoct.

### <a name="synchronization-tools"></a>Nástroje pro synchronizaci
V průběhu let máte několik nástrojů pro synchronizaci existoval a použít pro různé scénáře.  Azure AD Connect je aktuálně přejít na nástroj pro všechny podporované scénáře.  AAD Sync a DirSync se stále také kolem a dokonce může být k dispozici ve vašem prostředí teď. 

> [!NOTE]
> Nejnovější informace týkající se podporovaných schopností jednotlivých nástrojích najdete v článku [porovnání nástrojů pro integraci adresáře](plan-hybrid-identity-design-considerations-tools-comparison.md) článku.  
> 
> 

### <a name="supported-topologies"></a>Podporované topologie
Při definování strategie synchronizace, musíte určit topologie, který se používá. V závislosti na informace, které bylo zjištěno v kroku 2 můžete určit, kterou topologii je ta správná pro použití. Jedné doménové struktury, jediné služby Azure AD topologie je nejčastěji používaných a se skládá z jedné doménové struktury služby Active Directory a jednu instanci služby Azure AD.  To se bude používat ve většině scénářů a je očekávané topologie, při použití Azure AD Connect Expresní instalace, jak je znázorněno na následujícím obrázku.

![](./media/plan-hybrid-identity-design-considerations/single-forest.png) Scénář jedné doménové struktury je běžné, že i malé i velké organizace s více doménovými strukturami, jak je znázorněno na obrázku 5.

> [!NOTE]
> Další informace o různých místních a topologie služby Azure AD službou Azure AD Connect sync najdete v článku [topologie pro Azure AD Connect](plan-connect-topologies.md).
> 
> 

![](./media/plan-hybrid-identity-design-considerations/multi-forest.png) 

Scénář s více doménovými strukturami

Pokud se jedná o tento případ a pak jednou více doménovými strukturami topologie služby Azure AD má brát, pokud jsou splněny následující položky:

* Uživatelé mají pouze 1 identity mezi doménovými strukturami všem – jednoznačně identifikující uživatele níže uvedené části to popisuje podrobněji.
* Uživatel se ověřuje doménové struktury, ve kterém se nachází svoji identitu
* Hlavní název uživatele a zdrojové ukotvení (neměnné id) budou přicházet z této doménové struktury
* Všechny doménové struktury jsou přístupné pomocí služby Azure AD Connect – to znamená, že nemusí být připojené k doméně a můžete umístit v hraniční síti, pokud to usnadňuje to.
* Uživatelé mají pouze jedna poštovní schránka
* Nejlepší kvalita dat pro atributy, které jsou viditelné v globální adresy seznamu (GAL) systému Exchange má doménové struktury, který je hostitelem poštovních schránek uživatele
* Pokud na uživatele není žádná poštovní schránky, pak všech doménových strukturách lze přispívat tyto hodnoty
* Pokud máte propojená poštovní schránky, pak je zde i jiný účet použitý k podpisu jiné doménové struktuře.

> [!NOTE]
> Objekty, které existují v místním prostředí i v cloudu jsou "propojené" prostřednictvím jedinečného identifikátoru. V rámci synchronizace adresářů tento jedinečný identifikátor se nazývá SourceAnchor. V rámci sady Single Sign-On označuje se jako ImmutableId. [Koncepty návrhu Azure AD Connect](plan-connect-design-concepts.md#sourceanchor) další důležité informace týkající se použití SourceAnchor.
> 
> 

Pokud máte více než jeden aktivní účet nebo poštovní schránky více než jeden výše uvedené neplatí, Azure AD Connect bude vyberte jeden a ignorovat druhou.  Pokud propojení poštovních schránek, ale žádný účet, tyto účty se nebudou exportovat do služby Azure AD a tomuto uživateli nebudou členem žádné skupiny.  Tím se liší od jak v minulosti nástrojem DirSync a je úmyslné pro lepší podpory těchto scénářů s více doménovými strukturami. Na následujícím obrázku je zobrazený scénář více doménovými strukturami.

![](./media/plan-hybrid-identity-design-considerations/multiforest-multipleAzureAD.png) 

**Více doménových struktur více scénáře služby Azure AD**

Doporučuje se mít jenom jeden adresář ve službě Azure AD pro organizaci, ale podporují ji vztah 1:1 se ukázal jako nedocenitelný mezi server synchronizace služby Azure AD Connect a adresář Azure AD.  Pro každou instanci služby Azure AD musíte instalaci služby Azure AD Connect.  Navíc Azure AD, podle návrhu je izolované a uživatelé v jedné instanci služby Azure AD nebudou moci zobrazit uživatelům v jiné instanci.

Je možné a podporované pro jednu instanci v místním Active Directory připojit k více adresářů Azure AD, jak je znázorněno na následujícím obrázku:

![](./media/plan-hybrid-identity-design-considerations/single-forest-flitering.png) 

**Scénáře filtrování jednou doménovou strukturou**

Pokud chcete to provést, musí být splněné následující podmínky:

* Servery synchronizace služby Azure AD Connect musí být nakonfigurovaný pro filtrování tak, že každý z nich vzájemně se vylučující sadu objektů.  To provádí, například rozsahu každého serveru pro určitou doménu nebo organizační jednotku.
* Doména DNS lze registrovat pouze v jednom adresáři Azure AD proto UPN uživatelů v místní AD musíte použít samostatné obory názvů
* Uživatelé v jedné instanci služby Azure AD pouze budou moci zobrazit uživatelé z jejich instance.  Nebudou moci zobrazit uživatelům v jiných případech
* Pouze jeden z adresáře Azure AD povolit hybridní řešení Exchange se místní AD
* Vzájemné exkluzivitu, kterou platí také pro zpětný zápis.  Díky tomu některé zpětný zápis funkce nejsou podporovány s touto topologií, protože tyto předpokládají jednomu místnímu konfigurace.  To zahrnuje:
  * Skupina se zpětným zápisem s výchozí konfigurace
  * Zpětný zápis zařízení

Následující se nepodporuje a by neměl být vybrána jako implementace:

* Není možné mít víc synchronizačních serverů služby Azure AD Connect připojení do stejného adresáře Azure AD i v případě, že jsou nakonfigurované k synchronizaci vzájemně se vylučující sadu objektů
* Není podporováno synchronizace stejného uživatele do více adresářů Azure AD. 
* Je také podporováno konfiguraci změnit, aby uživatele v Azure AD se zobrazí jako kontakty z jiného adresáře služby Azure AD. 
* Také se nepodporuje upravit synchronizace Azure AD Connect pro připojení k více adresářů Azure AD.
* Adresáře Azure AD jsou standardně izolované. To se nepodporuje a změňte konfiguraci synchronizace Azure AD Connect na čtení dat z jiného adresáře služby Azure AD za účelem vytvoření běžné a jednotné GAL mezi adresáře. Je také podporováno exportovat uživatele jako kontakty do jiné místní AD pomocí synchronizace Azure AD Connect.

> [!NOTE]
> Pokud vaše organizace počítače ve vaší síti brání připojení k Internetu, v tomto článku jsou uvedené koncové body (plně kvalifikované názvy domén, IPv4 a IPv6 rozsahy adres), měli byste zahrnout do vaší odchozí povolit seznamy a zóně důvěryhodných lokalit aplikace Internet Explorer klienta počítačích a ověřte vaše počítače můžou úspěšně používat Office 365. Další informace najdete [Office 365 – adresy URL a rozsahy IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>Definování strategie pro ověřování službou Multi-Factor Authentication
Při plnění tohoto úkolu určíte strategii ověřování službou Multi-Factor Authentication použít.  Azure Multi-Factor Authentication je k dispozici ve dvou verzích.  Jedna je Cloudová a druhá místní pomocí Azure MFA serveru.  Na základě vyhodnocení výše si můžete můžete určit, které řešení je varianty pro vaše strategie.  Následující tabulku použijte k určení, která možnost návrhu nejlépe splňuje požadavek na zabezpečení vaší společnosti:

Možnosti návrhu služby Multi-Factor Authentication:

| Prostředek k zabezpečení | MFA v cloudu | Místní MFA |
| --- | --- | --- |
| Microsoft apps |ano |ano |
| Aplikace Saas v galerii aplikací |ano |ano |
| Aplikace služby IIS publikované prostřednictvím proxy aplikace Azure AD |ano |ano |
| Aplikace služby IIS nepublikované prostřednictvím Proxy aplikace Azure AD |ne |ano |
| Vzdálený přístup jako síť VPN, RDG |ne |ano |

I v případě, že pro strategii, může mít vyrovnané na řešení, budete stále muset použít z výše uvedených vyhodnocení toho, kde se nachází vaši uživatelé.  To může způsobit řešení Chcete-li změnit.  Následující tabulku můžete použijte při určování toto:

| Umístění uživatele | Upřednostňovanou možnost |
| --- | --- |
| Azure Active Directory |Více FactorAuthentication v cloudu |
| Azure AD a místní AD využívající federaci se službou AD FS |Obojí |
| Azure AD a místní AD pomocí služby Azure AD Connect bez synchronizace hesla |Obojí |
| Azure AD a na místní klasifikaci pomocí služby Azure AD Connect se synchronizací hesla |Obojí |
| Místní AD |Server Multi-Factor Authentication |

> [!NOTE]
> Také se ujistěte, že vybrané možnosti návrhu služby Multi-Factor authentication podporuje funkce, které jsou požadovány pro návrh.  Další informace najdete [vyberte řešení zabezpečení Multi-Factor Authentication vám](../authentication/concept-mfa-whichversion.md#what-am-i-trying-to-secure).
> 
> 

## <a name="multi-factor-auth-provider"></a>Poskytovatel Multi-Factor Auth
Ověřování službou Multi-Factor Authentication je k dispozici ve výchozím nastavení pro globální správce, kteří mají tenanta služby Azure Active Directory. Ale pokud budete chtít rozšířit ověřování službou Multi-Factor Authentication pro všechny uživatele a/nebo chcete globální správci mohli využít výhod funkce, jako je portál pro správu, vlastní přivítání a sestavy, pak musíte zakoupit a nakonfigurovat Poskytovatele služby Multi-Factor Authentication.

> [!NOTE]
> Také se ujistěte, že vybrané možnosti návrhu služby Multi-Factor authentication podporuje funkce, které jsou požadovány pro návrh. 
> 
> 

## <a name="next-steps"></a>Další postup
[Určení požadavků na ochranu dat](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Další informace najdete v tématech
[Přehled aspektů návrhu](plan-hybrid-identity-design-considerations-overview.md)


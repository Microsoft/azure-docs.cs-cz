---
title: Návrh hybridní identity – strategie přijetí Azure | Dokumenty společnosti Microsoft
description: Pomocí řízení podmíněného přístupu služba Azure Active Directory zkontroluje konkrétní podmínky, které vyberete při ověřování uživatele a před povolením přístupu k aplikaci. Jakmile jsou tyto podmínky splněny, uživatel je ověřen a povolen přístup k aplikaci.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: b92fa5a9-c04c-4692-b495-ff64d023792c
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: e662d2c6d7939756dee6eb25ca62fef171b7d6d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109337"
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>Definování strategie přechodu hybridní identity
V tomto úkolu definujete strategii přechodu hybridní identity pro řešení hybridní identity tak, aby splňovaly obchodní požadavky, které byly popsány v:

* [Určení obchodních potřeb](plan-hybrid-identity-design-considerations-business-needs.md)
* [Určení požadavků na synchronizaci adresářů](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [Určení vícefaktorových požadavků na ověřování](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Definování strategie podniku potřeb
První úkol řeší určení obchodních potřeb organizací.  To může být velmi široké a rozsah tečení může dojít, pokud si nejste opatrní.  Na začátku, aby to jednoduché, ale vždy nezapomeňte naplánovat design, který bude vyhovovat a usnadnit změny v budoucnu.  Bez ohledu na to, zda se jedná o jednoduchý návrh nebo o extrémně složitý návrh, je Služba Azure Active Directory platformou Microsoft Identity, která podporuje služby Office 365, služby Microsoft Online Services a aplikace podporující cloud.

## <a name="define-an-integration-strategy"></a>Definování integrační strategie
Microsoft má tři hlavní scénáře integrace, které jsou cloudové identity, synchronizované identity a federované identity.  Měli byste naplánovat přijetí jedné z těchto integračních strategií.  Strategie, kterou zvolíte, se může lišit a rozhodnutí při výběru může zahrnovat, jaký typ uživatelského prostředí chcete poskytnout, máte existující infrastrukturu a co je nákladově nejefektivnější.  

![scénáře integrace](./media/plan-hybrid-identity-design-considerations/integration-scenarios.png)

Scénáře definované na výše uvedeném obrázku jsou:

* **Cloudové identity**: jedná se o identity, které existují výhradně v cloudu.  V případě Azure AD by umístěny konkrétně ve vašem adresáři Azure AD.
* **Synchronizované**: jedná se o identity, které existují místně a v cloudu.  Pomocí Azure AD Connect se tito uživatelé buď vytvoří, nebo se spojí s existujícími účty Azure AD.  Hash hesla uživatele je synchronizován z místního prostředí do cloudu v takzvané matné hash hesla.  Při použití synchronizované jedné upozornění je, že pokud je uživatel zakázán v místním prostředí, může trvat až tři hodiny pro tento stav účtu se zobrazí ve službě Azure AD.  To je způsobeno časovým intervalem synchronizace.
* **Federované**: tyto identity existují jak místně, tak v cloudu.  Pomocí Azure AD Connect se tito uživatelé buď vytvoří, nebo se spojí s existujícími účty Azure AD.  

> [!NOTE]
> Další informace o možnostech synchronizace načtete [v části Integrace místních identit pomocí služby Azure Active Directory](whatis-hybrid-identity.md).
> 
> 

Následující tabulka pomáhá při určování výhod a nevýhod každé z následujících strategií:

| Strategie | Výhody | Nevýhody |
| --- | --- | --- |
| **Cloudové identity** |Snadnější správa pro malé organizace. <br> Nic k instalaci v místním prostředí. Není potřeba žádný další hardware<br>Snadno se deaktivuje, pokud uživatel opustí společnost |Uživatelé se budou muset přihlásit při přístupu k úlohám v cloudu <br> Hesla mohou nebo nemusí být stejná pro cloudové a místní identity |
| **Synchronizovány** |Místní heslo ověřuje místní i cloudové adresáře. <br>Snadnější správa pro malé, střední nebo velké organizace <br>Uživatelé mohou mít jednotné přihlašování (SSO) pro některé prostředky <br> Upřednostňovaná metoda synchronizace společnosti Microsoft <br> Snadnější správa |Někteří zákazníci se mohou zdráhat synchronizovat své adresáře s cloudem kvůli policii konkrétní společnosti |
| **Federovaní** |Uživatelé mohou mít jednotné přihlašování (SSO) <br>Pokud je uživatel ukončen nebo odejde, může být účet okamžitě deaktivován a přístup odvolán,<br> Podporuje pokročilé scénáře, které nelze provést pomocí synchronizovaných |Další kroky k nastavení a konfiguraci <br> Vyšší údržba <br> Může vyžadovat další hardware pro infrastrukturu STS <br> Může vyžadovat další hardware pro instalaci federačního serveru. Další software je vyžadován, pokud je použit ad FS <br> Vyžadovat rozsáhlé nastavení pro sso <br> Kritický bod selhání, pokud federační server nese, uživatelé nebudou moci ověřit |

### <a name="client-experience"></a>Možnosti klienta
Strategie, kterou používáte bude diktovat uživatelské přihlašovací prostředí.  Následující tabulky poskytují informace o tom, co by měli uživatelé očekávat, že jejich přihlašovací prostředí bude.  Ne všichni poskytovatelé federovaných identit podporují zabezpečení ve všech scénářích.

**Doman-připojil a soukromé síťové aplikace**:

|  | Synchronizovaná identita | Federated Identity |
| --- | --- | --- |
| Webové prohlížeče |Ověřování založené na formulářích |jednotné přihlašování, někdy nutné k dodání ID organizace |
| Outlook |Vyzvat k zadání pověření |Vyzvat k zadání pověření |
| Skype pro firmy (Lync) |Vyzvat k zadání pověření |jednotné přihlašování k Lyncu, výzva k zadání přihlašovacích údajů pro Exchange |
| OneDrive pro firmy |Vyzvat k zadání pověření |jednotné přihlašování |
| Předplatné Office Pro Plus |Vyzvat k zadání pověření |jednotné přihlašování |

**Externí nebo nedůvěryhodné zdroje**:

|  | Synchronizovaná identita | Federated Identity |
| --- | --- | --- |
| Webové prohlížeče |Ověřování založené na formulářích |Ověřování založené na formulářích |
| Outlook, Skype pro firmy (Lync), OneDrive pro firmy, předplatné Office |Vyzvat k zadání pověření |Vyzvat k zadání pověření |
| Exchange ActiveSync |Vyzvat k zadání pověření |jednotné přihlašování k Lyncu, výzva k zadání přihlašovacích údajů pro Exchange |
| Mobilní aplikace |Vyzvat k zadání pověření |Vyzvat k zadání pověření |

Pokud jste z úkolu 1 zjistili, že máte idp třetí strany nebo budete ho používat k poskytování federace s Azure AD, musíte si být vědomi následujících podporovaných funkcí:

* Každý poskytovatel SAML 2.0, který je kompatibilní s profilem SP-Lite, může podporovat ověřování ve službě Azure AD a přidružených aplikacích.
* Podporuje pasivní ověřování, které usnadňuje ověřování pro OWA, SPO atd.
* Klienti Exchange Online mohou být podporováni prostřednictvím rozšířeného profilu klienta SAML 2.0 (ECP)

Musíte si také být vědomi toho, jaké možnosti nebudou k dispozici:

* Bez podpory WS-Trust/Federation se přeruší všechny ostatní aktivní klienty.
  * To znamená, že před Office 2016 není žádný klient Lyncu, klient OneDrivu, Předplatné Office, Office Mobile.
* Přechod office na pasivní ověřování jim umožňuje podporovat čisté IP adresy SAML 2.0, ale podpora bude stále na základě klienta pro klienta

> [!NOTE]
> Nejaktuálnější seznam napíše článek [Seznam kompatibility federací Azure AD](how-to-connect-fed-compatibility.md).
> 
> 

## <a name="define-synchronization-strategy"></a>Definovat strategii synchronizace
V této úloze definujete nástroje, které budou použity k synchronizaci místních dat organizace do cloudu a jakou topologii byste měli použít.  Vzhledem k tomu, že většina organizací používá službu Active Directory, informace o použití služby Azure AD Connect k řešení výše uvedených otázek jsou k dispozici v některých podrobnostech.  V prostředích, která nemají službu Active Directory, jsou k dispozici informace o použití fim 2010 R2 nebo MIM 2016 k plánování této strategie.  Budoucí verze Azure AD Connect však bude podporovat adresáře LDAP, takže v závislosti na časové ose, tyto informace může být schopen pomoci.

### <a name="synchronization-tools"></a>Nástroje pro synchronizaci
V průběhu let existovalo a používalo se několik synchronizačních nástrojů pro různé scénáře.  V současné době Azure AD Connect je přejít na nástroj volby pro všechny podporované scénáře.  AAD Sync a DirSync jsou také stále kolem a mohou být dokonce přítomny ve vašem prostředí nyní. 

> [!NOTE]
> Nejnovější informace týkající se podporovaných funkcí jednotlivých nástrojů naleznete v článku pro [porovnání nástrojů pro integraci adresářů.](plan-hybrid-identity-design-considerations-tools-comparison.md)  
> 
> 

### <a name="supported-topologies"></a>Podporované topologie
Při definování strategie synchronizace musí být určena topologie, která se používá. V závislosti na informacích, které byly určeny v kroku 2, můžete určit, která topologie je správná. Jedna doménová struktura, jedna topologie Azure AD je nejběžnější a skládá se z jedné doménové struktury služby Active Directory a jedné instance Azure AD.  To se bude používat ve většině scénářů a je očekávané topologie při použití instalace Azure AD Connect Express, jak je znázorněno na obrázku níže.

![Podporované topologie](./media/plan-hybrid-identity-design-considerations/single-forest.png) Single Forest Scénář Je běžné, že velké a dokonce i malé organizace mají více doménových struktur, jak je znázorněno na obrázku 5.

> [!NOTE]
> Další informace o různých místních topologiích a topologiích Azure AD pomocí synchronizace Azure AD Connect narážka na článek [Topologie pro Azure AD Connect](plan-connect-topologies.md).
> 
> 

![topologie s více doménovými strukturami](./media/plan-hybrid-identity-design-considerations/multi-forest.png) 

Scénář více doménových vrstev

Pokud se jedná o tento případ, pak více doménové struktury jeden Topologie Azure AD by měla být považována za následující položky:

* Uživatelé mají pouze 1 identitu ve všech doménových strukturách – níže uvedená část s jedinečným identifikací uživatelů to popisuje podrobněji.
* Uživatel se ověří do doménové struktury, ve které je umístěna jeho identita.
* Hlavní název domény a zdrojová kotva (neměnné id) budou pocházet z této doménové struktury.
* Všechny doménové struktury jsou přístupné pomocí Azure AD Connect – to znamená, že nemusí být připojen k doméně a může být umístěn v DMZ, pokud to usnadňuje.
* Uživatelé mají pouze jednu poštovní schránku
* Doménová struktura, která hostuje poštovní schránku uživatele, má nejlepší kvalitu dat pro atributy viditelné v globálním seznamu adres serveru Exchange .)
* Pokud uživatel nemá žádnou poštovní schránku, může být k přisuzování těchto hodnot použita jakákoli doménová struktura.
* Pokud máte propojenou poštovní schránku, pak je také jiný účet v jiné doménové struktuře používá k přihlášení.

> [!NOTE]
> Objekty, které existují v místním i cloudu, jsou "propojeny" pomocí jedinečného identifikátoru. V kontextu synchronizace adresáře se tento jedinečný identifikátor označuje jako SourceAnchor. V kontextu jednotného přihlašování se označuje jako ImmutableId. [Návrh koncepty pro Azure AD Connect](plan-connect-design-concepts.md#sourceanchor) pro další aspekty týkající se použití SourceAnchor.
> 
> 

Pokud výše uvedené nejsou pravdivé a máte více než jeden aktivní účet nebo více než jednu poštovní schránku, Azure AD Connect vybere jeden a ignorovat ostatní.  Pokud jste propojili poštovní schránky, ale žádný jiný účet, tyto účty se nebudou exportovat do Azure AD a tento uživatel nebude členem žádné skupiny.  To se liší od toho, jak to bylo v minulosti s DirSync a je záměrné lépe podporovat tyto scénáře více doménové struktury. Scénář více doménové struktury je znázorněn na obrázku níže.

![více klientů Azure AD](./media/plan-hybrid-identity-design-considerations/multiforest-multipleAzureAD.png) 

**Vícedoménových doménových scénářů Azure AD**

Doporučuje se mít pouze jeden adresář ve službě Azure AD pro organizaci, ale je podporován a vztah 1:1 je zachována mezi synchronizačníserver Azure AD Connect a adresář Azure AD.  Pro každou instanci Azure AD potřebujete instalaci Azure AD Connect.  Azure AD, podle návrhu je izolované a uživatelé v jedné instanci Azure AD nebude moct zobrazit uživatele v jiné instanci.

Je možné a podporované připojit jednu místní instanci služby Active Directory k více adresářům Azure AD, jak je znázorněno na obrázku níže:

![filtrování jedné doménové struktury](./media/plan-hybrid-identity-design-considerations/single-forest-flitering.png) 

**Scénář filtrování jedné doménové struktury**

Chcete-li to provést, musí být splněna následující hodnota:

* Synchronizační servery Azure AD Connect musí být nakonfigurované pro filtrování, aby každý z nich měl vzájemně se vylučující sadu objektů.  To se provádí například vymezením jednotlivých serverů na určitou doménu nebo ou.
* Doménu DNS lze zaregistrovat jenom v jednom adresáři Azure AD, takže hlavní názvy názvů uživatelů v místním službě AD musí používat samostatné obory názvů.
* Uživatelé v jedné instanci Azure AD budou moci zobrazit jenom uživatele z jejich instance.  Nebudou moci zobrazit uživatele v ostatních případech
* Pouze jeden z adresářů Azure AD můžete povolit Exchange hybrid s místní službou AD
* Vzájemná exkluzivita platí i pro zpětný zápis.  To umožňuje některé funkce zpětného zápisu není podporována s touto topologii, protože tyto předpokládat jednu místní konfiguraci.  To zahrnuje:
  * Skupinový zpětný zápis s výchozí konfigurací
  * Zpětný zápis zařízení

Následující není podporována a neměla by být vybrána jako implementace:

* Není podporováno, aby se k stejnému adresáři Azure AD Connect připojovalo více synchronizačních serverů Azure AD Connect, i když jsou nakonfigurované k synchronizaci vzájemně se vylučující sady objektů.
* Není podporováno synchronizovat stejného uživatele s více adresáři Azure AD. 
* Je také nepodporované provést změnu konfigurace, aby se uživatelé v jednom Azure AD zobrazili jako kontakty v jiném adresáři Azure AD. 
* Je také nepodporované upravit synchronizaci Azure AD Connect pro připojení k více adresářům Azure AD.
* Adresáře Azure AD jsou podle návrhu izolované. Není podporováno změnit konfiguraci synchronizace Azure AD Connect na čtení dat z jiného adresáře Azure AD ve snaze vytvořit společný a jednotný globálním seznamu adres mezi adresáři. Je také nepodporovaný pro export uživatelů jako kontaktů do jiného místního služby AD pomocí synchronizace Azure AD Connect.

> [!NOTE]
> Pokud vaše organizace omezuje připojení počítačů v síti k Internetu, uvádí tento článek koncové body (oblasti adres VF, IPv4 a IPv6), které byste měli zahrnout do odchozích seznamů povolených serverů a zóny důvěryhodných serverů aplikace Internet Explorer. počítače, aby vaše počítače mohly úspěšně používat Office 365. Další informace načtou [adresy URL office 365 a rozsahy IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>Definování strategie vícefaktorového ověřování
V tomto úkolu definujete vícefaktorovou strategii ověřování, která se má použít.  Azure Multi-Factor Authentication se dodává ve dvou různých verzích.  Jeden je na základě cloudu a druhý je místní na základě azure mfa server.  Na základě výše uvedeného hodnocení můžete určit, které řešení je správné pro vaši strategii.  V následující tabulce můžete určit, která možnost návrhu nejlépe splňuje požadavky vaší společnosti na zabezpečení:

Vícefaktorové možnosti návrhu:

| Majetek pro zabezpečení | MFA v cloudu | Místní MFA |
| --- | --- | --- |
| Aplikace Microsoft |ano |ano |
| Aplikace Saas v galerii aplikací |ano |ano |
| Aplikace služby IIS publikované prostřednictvím proxy aplikace Azure AD |ano |ano |
| Aplikace služby IIS, které nejsou publikovány prostřednictvím proxy aplikace Azure AD |ne |ano |
| Vzdálený přístup jako VPN, RDG |ne |ano |

I když jste se možná dohodli na řešení pro vaši strategii, stále musíte použít hodnocení shora o tom, kde se nacházejí vaši uživatelé.  To může způsobit změnu řešení.  Pomocí následující tabulky můžete určit toto:

| Umístění uživatele | Upřednostňovaná možnost návrhu |
| --- | --- |
| Azure Active Directory |Vícefaktorové ověřování v cloudu |
| Azure AD a místní AD využívající federaci se službou AD FS |Obojí |
| Azure AD a místní ad pomocí Azure AD Connect bez synchronizace hesel |Obojí |
| Azure AD a místní pomocí Azure AD Connect se synchronizací hesel |Obojí |
| Místní ad |Server Multi-Factor Authentication |

> [!NOTE]
> Měli byste také zajistit, aby vybraná možnost vícefaktorového ověřování podporovala funkce, které jsou pro návrh vyžadovány.  Další informace naleznete [v části Vyberte vícefaktorové řešení zabezpečení pro vás](../authentication/concept-mfa-howitworks.md).
> 

## <a name="multi-factor-auth-provider"></a>Poskytovatel vícefaktorového authu
Vícefaktorové ověřování je ve výchozím nastavení k dispozici pro globální správce, kteří mají klienta Služby Azure Active Directory. Pokud však chcete rozšířit vícefaktorové ověřování na všechny uživatele nebo chcete globálním správcům využívat funkce, jako je portál pro správu, vlastní pozdravy a sestavy, je nutné zakoupit a nakonfigurovat zprostředkovatele vícefaktorového ověřování.

> [!NOTE]
> Měli byste také zajistit, aby vybraná možnost vícefaktorového ověřování podporovala funkce, které jsou pro návrh vyžadovány. 
> 
> 

## <a name="next-steps"></a>Další kroky
[Určení požadavků na ochranu údajů](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Viz také
[Přehled aspekty návrhu](plan-hybrid-identity-design-considerations-overview.md)


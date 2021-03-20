---
title: Návrh hybridní identity – strategie přijetí Azure | Microsoft Docs
description: Díky řízení podmíněného přístupu Azure AD kontroluje konkrétní podmínky, které vyberete při ověřování uživatele, a před povolením přístupu k aplikaci.
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
ms.openlocfilehash: 7f52e46ff9cab7d3d150af9fd7b4f1c432bec74b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94836185"
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>Definice strategie přijetí hybridní identity
V této úloze definujete strategii přijetí hybridní identity pro vaše řešení hybridní identity, která bude vyhovovat obchodním požadavkům, které byly popsané v tématu:

* [Určení obchodních potřeb](plan-hybrid-identity-design-considerations-business-needs.md)
* [Určení požadavků na synchronizaci adresářů](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [Určení požadavků na službu Multi-Factor Authentication](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Definování strategie obchodních potřeb
První úkol řeší obchodní potřeby organizace.  To může být velmi široké a v případě, že nebudete opatrní, může dojít k nárůstu rozsahu.  Na začátku mějte jednoduché, ale vždycky nezapomeňte naplánovat návrh, který bude přizpůsoben a v budoucnu usnadňuje změnu.  Bez ohledu na to, jestli se jedná o jednoduchý návrh nebo extrémně složitou, Azure Active Directory je platforma pro identity Microsoftu, která podporuje Microsoft 365, online služby Microsoftu a aplikace pracující s cloudem.

## <a name="define-an-integration-strategy"></a>Definování strategie integrace
Společnost Microsoft má tři hlavní scénáře integrace, které jsou cloudové identity, synchronizované identity a federované identity.  Měli byste naplánovat přijetí jedné z těchto strategií integrace.  Strategie, kterou si zvolíte, se může lišit a rozhodnutí o jejich výběru může zahrnovat, jaký typ uživatelského prostředí chcete poskytnout, jak máte stávající infrastrukturu a jaké jsou náklady na maximum.  

![scénáře integrace](./media/plan-hybrid-identity-design-considerations/integration-scenarios.png)

Scénáře definované výše na obrázku jsou následující:

* **Cloudové identity**: Jedná se o identity, které existují výhradně v cloudu.  V případě služby Azure AD by se měly nacházet konkrétně v adresáři Azure AD.
* **Synchronizované**: Jedná se o identity, které existují místně a v cloudu.  Pomocí Azure AD Connect jsou tito uživatelé buď vytvořeni, nebo se připojili k existujícím účtům služby Azure AD.  Hodnota hash hesla uživatele je synchronizovaná z místního prostředí do cloudu v tom, co se nazývá hodnota hash hesla.  Pokud používáte synchronizaci s jednou ochranou, znamená to, že pokud je uživatel v místním prostředí zakázaný, může trvat až tři hodiny, než se stav tohoto účtu zobrazí ve službě Azure AD.  Důvodem je časový interval synchronizace.
* **Federované**: tyto identity existují místně i v cloudu.  Pomocí Azure AD Connect jsou tito uživatelé buď vytvořeni, nebo se připojili k existujícím účtům služby Azure AD.  

> [!NOTE]
> Další informace o možnostech synchronizace najdete [v tématu Integrace místních identit s Azure Active Directory](whatis-hybrid-identity.md).
> 
> 

Následující tabulka vám pomůže určit výhody a nevýhody každé z následujících strategií:

| Strategie | Výhody | Nevýhody |
| --- | --- | --- |
| **Cloudové identity** |Jednodušší Správa v malých organizacích. <br> Není nic k místní instalaci. Není potřeba žádný další hardware.<br>Snadno zakázané, pokud uživatel odejde ze společnosti |Uživatelé se budou muset přihlásit při přístupu k úlohám v cloudu. <br> Hesla můžou nebo nemusí být stejná pro cloudové a místní identity. |
| **Shoda** |Místní heslo ověřuje místní i cloudové adresáře. <br>Jednodušší Správa pro malé, střední nebo velké organizace <br>Uživatelé můžou mít pro některé prostředky jednotné přihlašování (SSO). <br> Upřednostňovaná metoda pro synchronizaci Microsoftu <br> Jednodušší Správa |Někteří zákazníci se můžou zdráhají synchronizovat své adresáře s konkrétními podnikovými zásadami, které jsou v cloudu splatné. |
| **Federovaní** |Uživatelé můžou mít jednotné přihlašování (SSO). <br>Pokud je uživatel ukončený nebo opustí, může být účet okamžitě zakázán a přístup odvolaný,<br> Podporuje pokročilé scénáře, které se nedají provést synchronizovaným |Další kroky k nastavení a konfiguraci <br> Vyšší údržba <br> Může vyžadovat další hardware pro infrastrukturu STS. <br> Může vyžadovat další hardware pro instalaci federačního serveru. Pokud se používá AD FS, vyžaduje se další software. <br> Vyžadovat rozsáhlou instalaci jednotného přihlašování <br> Kritický bod selhání v případě výpadku federačního serveru, uživatelé se nebudou moci ověřit |

### <a name="client-experience"></a>Možnosti klienta
Strategii, kterou použijete, se projeví při přihlašování uživatelů.  Následující tabulky obsahují informace o tom, co by uživatelé měli očekávat, že se jim přihlašuje.  Ne všichni zprostředkovatelé federovaných identit podporují jednotné přihlašování ve všech scénářích.

**Aplikace připojené k doména a privátní sítě**:

| Aplikace | Synchronizovaná identita | Federated Identity |
| --- | --- | --- |
| Webové prohlížeče |Ověřování založené na formulářích |jednotné přihlašování se někdy vyžaduje k poskytnutí ID organizace. |
| Outlook |Vyzvat k zadání pověření |Vyzvat k zadání pověření |
| Skype pro firmy (Lync) |Vyzvat k zadání pověření |jednotné přihlašování pro Lync – výzva k zadání přihlašovacích údajů pro Exchange |
| OneDrive pro firmy |Vyzvat k zadání pověření |jednotné přihlašování |
| Předplatné Office pro plus |Vyzvat k zadání pověření |jednotné přihlašování |

**Externí nebo nedůvěryhodné zdroje**:

| Aplikace | Synchronizovaná identita | Federated Identity |
| --- | --- | --- |
| Webové prohlížeče |Ověřování založené na formulářích |Ověřování založené na formulářích |
| Outlook, Skype pro firmy (Lync), OneDrive pro firmy, předplatné Office |Vyzvat k zadání pověření |Vyzvat k zadání pověření |
| Exchange ActiveSync |Vyzvat k zadání pověření |jednotné přihlašování pro Lync – výzva k zadání přihlašovacích údajů pro Exchange |
| Mobilní aplikace |Vyzvat k zadání pověření |Vyzvat k zadání pověření |

Pokud jste zjistili z úlohy 1, kterou máte IdP třetí strany, nebo ji použijete k zajištění federace se službou Azure AD, musíte mít na paměti následující podporované možnosti:

* Jakékoli poskytovatele SAML 2,0, který je kompatibilní s profilem SP-Lite, může podporovat ověřování u služby Azure AD a přidružených aplikací.
* Podporuje pasivní ověřování, které usnadňuje ověřování pro aplikaci OWA, SPO atd.
* Klienti Exchange Online se můžou podporovat přes Rozšířený profil klienta SAML 2,0 (ECP).

Musíte si také uvědomit, jaké možnosti nebudou k dispozici:

* Bez podpory WS-Trust/federace jsou všechny ostatní aktivní klienti přerušení.
  * To znamená, že se nejedná o klienta Lyncu, klienta OneDrive, předplatné Office, Office Mobile před Office 2016.
* Přechod mezi Office a pasivním ověřováním umožňuje, aby podporovaly čistě SAML 2,0 zprostředkovatelů identity, ale podpora bude pořád v klientském počítači.

> [!NOTE]
> Seznam nejvíce aktualizovaných najdete v článku [seznam kompatibility federace služby Azure AD](how-to-connect-fed-compatibility.md).
> 
> 

## <a name="define-synchronization-strategy"></a>Definovat strategii synchronizace
V této úloze definujete nástroje, které se použijí k synchronizaci místních dat organizace s cloudem a jakou topologii, kterou byste měli použít.  Vzhledem k tomu, že většina organizací používá službu Active Directory, informace o použití Azure AD Connect k zodpovězení výše uvedených otázek jsou k dispozici podrobněji.  Pro prostředí, která nemají službu Active Directory, jsou k dispozici informace o použití FIM 2010 R2 nebo MIM 2016 k naplánování této strategie.  Budoucí verze Azure AD Connect ale budou podporovat adresáře LDAP, takže v závislosti na časové ose vám tyto informace můžou pomoct.

### <a name="synchronization-tools"></a>Nástroje pro synchronizaci
V průběhu let existovalo několik synchronizačních nástrojů, které se používají pro různé scénáře.  V současné době je Azure AD Connect Nástroj pro výběr nástroje pro všechny podporované scénáře.  AAD Sync a DirSync se taky pořád vyskytují a můžou být ve vašem prostředí dokonce i ve vašem prostředí. 

> [!NOTE]
> Nejnovější informace o podporovaných funkcích jednotlivých nástrojů najdete v článku [porovnání nástrojů pro integraci adresářů](plan-hybrid-identity-design-considerations-tools-comparison.md) .  
> 
> 

### <a name="supported-topologies"></a>Podporované topologie
Při definování strategie synchronizace musí být určena topologie, která se používá. V závislosti na informacích, které byly určeny v kroku 2, můžete určit, která topologie je vhodná pro použití. Jediná doménová struktura je jediná topologie služby Azure AD, která se skládá z jedné doménové struktury Active Directory a jedné instance Azure AD.  Tato hodnota se bude používat ve většině scénářů a při použití Azure AD Connect Expresní instalace je očekávaná topologie, jak je znázorněno na následujícím obrázku.

![](./media/plan-hybrid-identity-design-considerations/single-forest.png)Scénář s jednou doménovou strukturou topologie s jednou doménou je běžné, že velké a i malé organizace mají více doménových struktur, jak je znázorněno na obrázku 5.

> [!NOTE]
> Další informace o různých místních topologiích a topologiích Azure AD s Azure AD Connect synchronizace najdete v článku [topologie pro Azure AD Connect](plan-connect-topologies.md).
> 
> 

![topologie s více doménovými strukturami](./media/plan-hybrid-identity-design-considerations/multi-forest.png) 

Scénář s více doménovými strukturami

Pokud se jedná o tento případ, měla by se zvážit jedna topologie Azure AD s více doménovými strukturami, pokud jsou splněné následující položky:

* Uživatelé mají v rámci všech doménových struktur jenom 1 identitu – oddíl jedinečně identifikující uživatele níže tento popis popisuje podrobněji.
* Uživatel se ověří v doménové struktuře, ve které se nachází jejich identita.
* Z této doménové struktury bude pocházet hlavní název uživatele (UPN) a zdrojové kotvy (neměnné ID)
* Všechny doménové struktury jsou dostupné pomocí Azure AD Connect – to znamená, že nemusí být připojené k doméně a můžou být umístěné do DMZ, pokud to Toto usnadňuje.
* Uživatelé mají jenom jednu poštovní schránku.
* Doménová struktura, která hostuje poštovní schránku uživatele, má nejlepší kvalitu dat pro atributy viditelné v seznamu globálních adres Exchange (globálního adresáře).
* Pokud uživatel nemá poštovní schránku, je možné použít jakoukoli doménovou strukturu k přispívání těchto hodnot.
* Pokud máte propojenou poštovní schránku, pak je v jiné doménové struktuře, která se používá k přihlášení, k dispozici i jiný účet.

> [!NOTE]
> Objekty, které existují v místním prostředí i v cloudu, jsou "připojené" prostřednictvím jedinečného identifikátoru. V kontextu synchronizace adresářů se tento jedinečný identifikátor označuje jako SourceAnchor. V kontextu jednotného přihlašování se označuje jako ImmutableId. [Navrhněte koncepty pro Azure AD Connect](plan-connect-design-concepts.md#sourceanchor) , kde najdete další informace týkající se používání SourceAnchor.
> 
> 

Pokud výše uvedené nejsou pravdivé a máte více než jeden aktivní účet nebo více než jednu poštovní schránku, Azure AD Connect vybere jednu a ignoruje druhou.  Pokud máte propojené poštovní schránky, ale ne jiný účet, tyto účty se nebudou do Azure AD exportovat a tento uživatel nebude členem žádné skupiny.  To se liší od toho, jak byla v minulosti s DirSync a je úmyslné pro lepší podporu těchto scénářů s více doménovými strukturami. Scénář s více doménovými strukturami je znázorněn na následujícím obrázku.

![několik tenantů Azure AD](./media/plan-hybrid-identity-design-considerations/multiforest-multipleAzureAD.png) 

**Scénář více doménových struktur s více doménovými strukturami Azure AD**

Doporučuje se mít v Azure AD jenom jeden adresář pro organizaci, ale podporuje se tím, že vztah 1:1 se udržuje mezi Azure AD Connect synchronizačním serverem a adresářem služby Azure AD.  Pro každou instanci Azure AD potřebujete instalaci Azure AD Connect.  Služba Azure AD je také izolovaně navržená a uživatelé v jedné instanci služby Azure AD nebudou moci zobrazit uživatele v jiné instanci.

Je možné, že se má připojit jedna místní instance služby Active Directory k více adresářům Azure AD, jak je znázorněno na následujícím obrázku:

![filtrování jedné doménové struktury](./media/plan-hybrid-identity-design-considerations/single-forest-flitering.png) 

**Scénář filtrování s jednou doménovou strukturou**

Chcete-li to provést, musí být splněny následující podmínky:

* Azure AD Connect synchronizace serverů musí být nakonfigurovány pro filtrování, aby každý z nich měl vzájemně exkluzivní sadu objektů.  To se provádí například vytvořením oboru každého serveru pro určitou doménu nebo organizační jednotku.
* Doménu DNS je možné zaregistrovat jenom v jednom adresáři služby Azure AD, aby hlavní názvy uživatelů v místní službě AD museli používat samostatné obory názvů.
* Uživatelé v jedné instanci Azure AD budou moct jenom zobrazit uživatele ze své instance.  Nebudou moct zobrazit uživatele v ostatních instancích.
* Jenom jeden adresář služby Azure AD může povolit službu Exchange hybrid s místní službou AD.
* Vzájemná výlučná práva platí také pro zpětný zápis.  Díky tomu některé funkce zpětného zápisu nejsou v této topologii podporované, protože se předpokládá jedna místní konfigurace.  Sem patří:
  * Zpětný zápis skupiny s výchozí konfigurací
  * Zpětný zápis zařízení

Následující možnost není podporována a neměla by být vybrána jako implementace:

* Není podporováno více Azure AD Connect synchronizačních serverů, které se připojují ke stejnému adresáři služby Azure AD, i když jsou nakonfigurovány pro synchronizaci vzájemně se exkluzivní sady objektů.
* Synchronizace stejného uživatele s více adresáři služby Azure AD není podporována. 
* Také se nepodporuje, aby se změnila konfigurace, aby se uživatelé v jedné službě Azure AD zobrazovali jako kontakty v jiném adresáři služby Azure AD. 
* Pro připojení k více adresářům Azure AD se taky nedoporučuje změnit Azure AD Connect synchronizaci.
* Adresáře Azure AD jsou v izolovaném designu. Změna konfigurace Azure AD Connect synchronizace pro čtení dat z jiného adresáře služby Azure AD se nezdařila při pokusu o vytvoření společného a sjednoceného globálního globálního adresáře mezi adresáři. Také se nedoporučuje exportovat uživatele jako kontakty do jiné místní služby AD pomocí Azure AD Connect synchronizace.

> [!NOTE]
> Pokud vaše organizace omezuje počítače v síti tak, aby se připojovaly k Internetu, v tomto článku jsou uvedeny koncové body (plně kvalifikované názvy domén, IPv4 a rozsahy adres IPv6), které byste měli zahrnout do seznamů odchozích povolení a důvěryhodných lokalit v Internet Exploreru klientských počítačů, aby bylo zajištěno, že vaše počítače budou moci úspěšně používat Microsoft 365. Další informace najdete v tématu [adresy URL a rozsahy IP adres pro Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>Definování strategie Multi-Factor Authentication
V tomto úkolu definujete strategii Multi-Factor Authentication, která se bude používat.  Azure AD Multi-Factor Authentication se dodává ve dvou různých verzích.  Jedna je cloudová a druhá v místním prostředí založená na Azure MFA serveru.  Na základě hodnocení, které jste použili výše, můžete určit, které řešení je pro vaši strategii správné.  Pomocí následující tabulky určete, která možnost návrhu nejlépe splňuje požadavky zabezpečení vaší společnosti:

Možnosti pro Multi-Factor design:

| Prostředek, který se má zabezpečit | MFA v cloudu | Místní MFA |
| --- | --- | --- |
| Aplikace Microsoftu |ano |ano |
| Aplikace Saas v galerii aplikací |ano |ano |
| Aplikace služby IIS publikované prostřednictvím proxy aplikace Azure AD |ano |ano |
| Aplikace služby IIS nepublikované prostřednictvím Aplikace Azure AD proxy |ne |ano |
| Vzdálený přístup jako VPN, RDG |ne |ano |

I když jste se mohli vyhodnotit na řešení pro vaši strategii, stále musíte použít hodnocení výše na místě, kde se uživatelé nacházejí.  To může způsobit změnu řešení.  Následující tabulka vám pomůže určit tyto funkce:

| Umístění uživatele | Preferovaný způsob návrhu |
| --- | --- |
| Azure Active Directory |Multi-FactorAuthentication v cloudu |
| Azure AD a místní AD využívající federaci se službou AD FS |Obojí |
| Azure AD a místní služba AD s využitím Azure AD Connect bez synchronizace hesel |Obojí |
| Azure AD a místní použití Azure AD Connect se synchronizací hesel |Obojí |
| Místní služba AD |Server Multi-Factor Authentication |

> [!NOTE]
> Měli byste taky zajistit, aby vybraná možnost návrhu služby Multi-Factor Authentication podporovala funkce, které jsou pro váš návrh potřeba.  Další informace najdete [v tématu Výběr řešení Multi-Factor Security pro vás](../authentication/concept-mfa-howitworks.md).
> 

## <a name="multi-factor-auth-provider"></a>Poskytovatel Multi-Factor auth
Služba Multi-Factor Authentication je k dispozici ve výchozím nastavení pro globální správce, kteří mají klienta Azure Active Directory. Pokud ale chcete službu Multi-Factor Authentication zvětšit na všechny uživatele a/nebo chcete, aby mohli využívat výhody funkcí, jako je portál pro správu, vlastní pozdravy a sestavy, musíte zakoupit a nakonfigurovat poskytovatele Multi-Factor Authentication.

> [!NOTE]
> Měli byste taky zajistit, aby vybraná možnost návrhu služby Multi-Factor Authentication podporovala funkce, které jsou pro váš návrh potřeba. 
> 
> 

## <a name="next-steps"></a>Další kroky
[Určení požadavků na ochranu dat](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Viz také
[Přehled otázek návrhu](plan-hybrid-identity-design-considerations-overview.md)


---
title: Návrhu hybridní identity - strategii přijetí Azure | Microsoft Docs
description: Azure Active Directory pomocí podmíněného řízení přístupu, zkontroluje konkrétní podmínky, kterou vyberete při ověřování uživatele a před povolením přístupu k aplikaci. Po splnění těchto podmínek je uživatel ověřený a přistupovat k aplikaci.
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: b92fa5a9-c04c-4692-b495-ff64d023792c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 290c41e62080edcd9a2fad1b5045bac4328cc4cd
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>Definování strategie přijetí hybridní identity
V této úloze definujete strategií hybridní identity přijetí řešení hybridní identity splňovat obchodní požadavky, které se zabývá:

* [Určení obchodních potřeb](active-directory-hybrid-identity-design-considerations-business-needs.md)
* [Určení požadavků na synchronizaci adresáře](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [Stanovení požadavků na službu Multi-Factor authentication](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Definování strategie obchodních potřeb
První úloha adresy určení obchodní organizace potřebuje.  To může být velmi široký a oboru nárůstu může dojít, pokud si nejste opatrní.  Na začátek, jednoduchost ale nezapomeňte vždy plánování návrh, který bude přizpůsobení a usnadnění změn v budoucnu.  Bez ohledu na to, zda je jednoduchý návrhu nebo některého velmi složité, Azure Active Directory je platforma Microsoft Identity, která podporuje Office 365, Microsoft Online Services a cloudových aplikací.

## <a name="define-an-integration-strategy"></a>Definování strategie integraci
Microsoft má tři scénáře hlavní integrace, které jsou cloudové identity, synchronizované identity a federované identity.  Měli byste naplánovat na jednu z těchto integračních strategie přijetí.  Strategie, kterou zvolíte, se může lišit a může zahrnovat rozhodnutí, která v zvolili jeden, jaký typ byste chtěli poskytnout, máte k dispozici některé stávající infrastrukturu již místní a co je nákladově nejefektivnější činnost koncového uživatele.  

![](./media/hybrid-id-design-considerations/integration-scenarios.png)

Scénáře definované v předchozí obrázek jsou:

* **Cloudové identity**: Jedná se o identity, které existují výhradně v cloudu.  V případě Azure AD by nacházet konkrétně v adresáři služby Azure AD.
* **Synchronizovat**: Jedná se o identity, které existují místně a v cloudu.  Pomocí služby Azure AD Connect, tito uživatelé vytvoření nebo propojit s existující účty Azure AD.  Hodnota hash hesla uživatele synchronizovaných z místní prostředí do cloudu v co se nazývá hodnoty hash hesla.  Při použití synchronizaci přímý přístup do jedné paměti je, pokud je uživatel zakázán v místním prostředí, může trvat až tři hodiny pro tento stav účtu objeví ve službě Azure AD.  Příčinou je časový interval synchronizace.
* **Federované**: tyto identity existují i místně a v cloudu.  Pomocí služby Azure AD Connect, tito uživatelé vytvoření nebo propojit s existující účty Azure AD.  

> [!NOTE]
> Další informace o možnosti synchronizace, najdete v tématu [integrace místních identit s Azure Active Directory](connect/active-directory-aadconnect.md).
> 
> 

V následující tabulce pomáhá při určování výhody a nevýhody každé z následujících strategií:

| Strategie | Výhody | Nevýhody |
| --- | --- | --- |
| **Cloudové identity** |Snazší správa pro menší organizace. <br> Nic k instalaci potřeba další hardware ne na místní<br>Snadno zakázat, pokud uživatel odejde ze společnosti |Uživatelé budou muset přihlásit při přístupu k úlohy v cloudu <br> Hesla mohou nebo nemusí být stejné pro cloudové a místní identity |
| **Synchronizovat** |Místní heslo ověřuje místní i cloudové adresáře <br>Snazší správa pro malé, střední nebo velké organizace <br>Uživatelé mohou mít jednotné přihlašování (SSO) pro některé zdroje <br> Metoda Microsoft upřednostňovaný pro synchronizaci <br> Snadnější správa |Někteří zákazníci možná nebudete k synchronizaci s cloudem z důvodu konkrétní společnosti dá jejich adresáře |
| **Federované** |Uživatelé mohou mít jednotné přihlašování (SSO) <br>Pokud uživatel je ukončen nebo opustí, účet můžete okamžitě zakázáno a odvolat přístup,<br> Podporuje rozšířené scénáře, které nelze provést pomocí synchronizovat |Další kroky pro nastavení a konfigurace <br> Vyšší údržby <br> Může vyžadovat další hardware pro infrastrukturu služby tokenů zabezpečení <br> Může vyžadovat další hardware k instalaci federační server. Další software je vyžadován, pokud se používá služba AD FS <br> Vyžadovat rozsáhlé instalační program pro jednotné přihlašování <br> Kritický bod selhání, pokud je federační server je vypnutý, uživatelé nebudou moci ověřit |

### <a name="client-experience"></a>Možnosti klienta
Strategie, kterou použijete, bude určovat přihlášení uživatele.  Následující tabulky poskytují informace o jaké uživatele by měl očekávat jejich přihlašovat jako.  Ne všichni zprostředkovatelé federovaných identit podporují jednotné přihlašování ve všech scénářích.

**Domain připojený a privátní síťových aplikací**:

|  | Synchronizované Identity | Federated Identity |
| --- | --- | --- |
| Webové prohlížeče |Ověřování pomocí formulářů |jednotné přihlašování, někdy požádáni o zadání ID organizace |
| Outlook |Výzva k zadání přihlašovacích údajů |Výzva k zadání přihlašovacích údajů |
| Skype pro firmy (Lync) |Výzva k zadání přihlašovacích údajů |jednotné přihlašování pro aplikace Lync, výzva přihlašovací údaje pro Exchange |
| SkyDrive Pro |Výzva k zadání přihlašovacích údajů |Jednotné přihlašování |
| Office Pro Plus předplatného |Výzva k zadání přihlašovacích údajů |Jednotné přihlašování |

**Externí nebo nedůvěryhodných zdrojů**:

|  | Synchronizované Identity | Federated Identity |
| --- | --- | --- |
| Webové prohlížeče |Ověřování pomocí formulářů |Ověřování pomocí formulářů |
| Outlook, Skype pro firmy (Lync) Skydrive Pro předplatného systému Office |Výzva k zadání přihlašovacích údajů |Výzva k zadání přihlašovacích údajů |
| Exchange ActiveSync |Výzva k zadání přihlašovacích údajů |jednotné přihlašování pro aplikace Lync, výzva přihlašovací údaje pro Exchange |
| Mobilní aplikace |Výzva k zadání přihlašovacích údajů |Výzva k zadání přihlašovacích údajů |

Pokud zjistíte z úlohy 1, abyste měli třetích stran IdP nebo se má použít k poskytování federace se službou Azure AD, budete muset mít na paměti následující podporované funkce:

* Všechny zprostředkovatele SAML 2.0, která je kompatibilní s profilem SP Lite může podporovat ověřování do služby Azure AD a přidružené aplikace
* Podporuje pasivní ověřování, což usnadňuje ověřování aplikace OWA, SPO, atd.
* Exchange Online klientů může být podporován pomocí SAML 2.0 rozšířeného klienta profilu (ECP)

Musí být také vědět, jaké funkce nebudete mít k dispozici:

* Bez podpory důvěryhodnosti/WS-Federation přerušení všech aktivních klientů
  * To znamená ne klienta, klient OneDrive, předplatného systému Office, Office Mobile před Office 2016
* Přechod Office pasivní ověřování umožňuje, aby byla možná čistý IdPs SAML 2.0, ale podpora bude na základě klienta klienta

> [!NOTE]
> Pro nejaktuálnější seznamu najdete v článku https://aka.ms/ssoproviders.
> 
> 

## <a name="define-synchronization-strategy"></a>Definování strategie pro synchronizaci
V této úloze určíte nástroje, které budou použity k synchronizaci organizace místní data do cloudu a co byste měli používat topologie.  Protože většina organizací použít služby Active Directory, informace o používání Azure AD Connect na výše uvedené otázky je součástí některých podrobností.  Pro prostředí, které nemají služby Active Directory je informace o používání FIM 2010 R2 nebo MIM 2016 k usnadnění plánování této strategie.  Ale budoucích verzích služby Azure AD Connect bude podporovat adresáře LDAP, tak v závislosti na časové ose, tyto informace můžou pomoct.

### <a name="synchronization-tools"></a>Nástroje pro synchronizaci
V průběhu let máte několik nástrojů pro synchronizaci existoval a použít pro různé scénáře.  Azure AD Connect je aktuálně přejít k nástroji pro všechny podporované scénáře.  AAD Sync a DirSync jsou pořád kolem a může i nacházet ve vašem prostředí teď. 

> [!NOTE]
> Nejnovější informace o podporovaných schopností jednotlivé nástroje najdete v tématu [porovnání nástrojů integrace adresáře](active-directory-hybrid-identity-design-considerations-tools-comparison.md) článku.  
> 
> 

### <a name="supported-topologies"></a>Podporované topologie
Při definování strategie synchronizace, musíte určit topologie, která se používá. V závislosti na informace, které bylo zjištěno v kroku 2 můžete určit, které topologie je ta správná pro použít. Jedné doménové struktury, jeden topologie Azure AD je nejčastější a skládá se z jedné doménové struktury služby Active Directory jednu instanci Azure AD.  To se bude používat ve většině scénářů a je očekávané topologie při použití instalace Azure AD Connect Express, jak je znázorněno na obrázku níže.

![](./media/hybrid-id-design-considerations/single-forest.png) Scénář jedné doménové struktury je běžné i malé i velké organizace mají několik doménových struktur, jak je znázorněno na obrázku 5.

> [!NOTE]
> Další informace o různých místních a topologie služby Azure AD s Azure AD Connect sync najdete v článku [topologie pro Azure AD Connect](connect/active-directory-aadconnect-topologies.md).
> 
> 

![](./media/hybrid-id-design-considerations/multi-forest.png) 

Scénář s více doménovými strukturami

Pokud to tento případ pak topologie více-forest-jedním Azure AD považovat za pokud platí následující položky:

* Uživatelé mají pouze 1 identity v rámci všech doménových struktur – to jednoznačně identifikující uživatelé části popisuje podrobněji.
* Uživatel se ověřuje na doménové struktury, ve kterém se nachází svou identitu
* Hlavní název uživatele a zdrojové ukotvení (neměnné id) bude pocházet z této doménové struktury
* Všech doménových strukturách jsou přístupné přes Azure AD Connect – to znamená, že nemusí být domény připojený a mohou být umístěny v hraniční síti, pokud to usnadňuje to.
* Uživatelé mají pouze jedna poštovní schránka
* Doménová struktura, která hostuje poštovní schránky uživatele má nejlepší kvalitu dat pro atributy, které jsou viditelné v systému Exchange globální seznam adres
* Pokud neexistuje žádné poštovní schránky na uživatele, pak všechny doménové struktury může být použit přispívání tyto hodnoty
* Pokud máte propojená poštovní schránka, pak je také jiný účet v jiné doménové struktuře použít k přihlášení.

> [!NOTE]
> Objekty, které existují v jak místně a v cloudu "připojení" prostřednictvím jedinečný identifikátor. V rámci synchronizace adresářů tento jedinečný identifikátor se označuje jako SourceAnchor. V kontextu systému jednotné přihlašování to se označuje jako ImmutableId. [Koncepty návrhu Azure AD Connect](connect/active-directory-aadconnect-design-concepts.md#sourceanchor) další důležité informace týkající se použití SourceAnchor.
> 
> 

Pokud máte více než jeden aktivní účet nebo více než jedna poštovní schránka výše, která nejsou splněné, Azure AD Connect bude vyberte jeden a dalších ignorovat.  Pokud jste propojili poštovních schránek, ale žádný jiný účet, tyto účty nebudou exportovány do služby Azure AD a tento uživatel nebude členem žádné skupiny.  To se liší od byla v minulosti s nástrojem DirSync a je záměrné pro lepší podporu těchto scénářů s více doménovými strukturami. V níže uvedeném obrázku je zobrazený scénář více doménovými strukturami.

![](./media/hybrid-id-design-considerations/multiforest-multipleAzureAD.png) 

**Více doménovými strukturami více scénáři se službou Azure AD**

Doporučuje se mít jenom jeden adresář ve službě Azure AD pro organizaci, ale je podporovaná je relace 1:1 se ukládají mezi server synchronizace služby Azure AD Connect a adresář Azure AD.  Pro každou instanci Azure AD budete potřebovat instalace služby Azure AD Connect.  Navíc Azure AD, návrh je izolované a uživatelé jednu instanci Azure AD, nebudou moci zobrazit uživatelům v jiné instanci.

Je možné a podporované se připojit jednu místní instanci služby Active Directory k více adresářů Azure AD, jak je znázorněno na obrázku níže:

![](./media/hybrid-id-design-considerations/single-forest-flitering.png) 

**Filtrování scénář jednou doménovou strukturou**

Pokud to chcete provést následující musí být splněné:

* Azure AD Connect synchronizačních serverů musí nakonfigurovat pro filtrování, každá má vzájemně se vylučuje sadu objektů.  To provést, například rozsahu každý server pro konkrétní doménu nebo organizační jednotku.
* Doménu DNS se dají registrovat jenom v jedné adresář Azure AD proto UPN uživatelů v místní AD musíte použít samostatné obory názvů
* Zobrazit uživatele z jejich instance budou pouze uživatelé v jednu instanci Azure AD.  Nebudou moci zobrazit uživatelům v jiné instance
* Pouze jeden z adresáře služby Azure AD můžete povolit hybridní Exchange s místní AD
* Vzájemné výhradní právo platí také pro zpětný zápis.  Díky tomu některé funkce zpětný zápis, není podporován s touto topologií, protože se tyto předpokládá jednomu místnímu konfigurace.  To zahrnuje následující:
  * Skupiny s výchozí konfigurací zpětný zápis
  * Zpětný zápis zařízení

Následující se nepodporuje a nesmí být vybrána jako implementace:

* Není možné mít víc synchronizačních serverů Azure AD Connect připojení do stejného adresáře Azure AD i v případě, že je nakonfigurovaný k synchronizaci vzájemně se vylučuje sadu objektů
* Ji není podporována pro synchronizaci stejného uživatele do více adresářů Azure AD. 
* Toto pravidlo je nepodporovaná také k provedení změn, aby uživatele ve službě Azure AD, se zobrazí jako kontakty do jiného adresáře služby Azure AD v konfiguraci. 
* Je také nepodporované upravit synchronizace Azure AD Connect se připojit k více adresářů Azure AD.
* Adresáře Azure AD jsou záměrné izolované. Nepodporovaná měnit konfiguraci synchronizace služby Azure AD Connect číst data z jiného adresáře služby Azure AD při pokusu o sestavení běžných a jednotná GAL mezi adresáři. Je také nepodporovaný exportovat uživatele jako kontakty do jiné místní AD pomocí synchronizace Azure AD Connect.

> [!NOTE]
> Pokud vaše organizace brání počítači v síti připojení k Internetu, v tomto článku jsou uvedené koncové body (plně kvalifikovaný název domény, IPv4 a IPv6 rozsahy adres), by měla obsahovat vaše odchozí povolit seznamy a zóně důvěryhodných lokalit aplikace Internet Explorer klienta počítače zajistit počítače můžou úspěšně pomocí Office 365. Další informace najdete v tématu [Office 365 adresy URL a rozsahy IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>Definování strategie pro službu Multi-Factor authentication
V této úloze určíte strategii služby Multi-Factor authentication používat.  Azure Multi-Factor Authentication se dodává v dvě různé verze.  Jeden je cloudu a druhá je místní pomocí Azure MFA serveru.  Na základě vyhodnocení výše můžete můžete určit, které řešení je tu správnou pro strategie.  Následující tabulku použijte k určení, který způsob návrhu nejlépe splňuje požadavek na zabezpečení vaší společnosti:

Možnosti služby Multi-Factor návrhu:

| Prostředek pro zabezpečení | MFA v cloudu | Místní MFA |
| --- | --- | --- |
| Aplikace Microsoft |ano |ano |
| Aplikace Saas v galerii aplikací |ano |ano |
| Aplikace služby IIS publikované prostřednictvím proxy aplikace Azure AD |ano |ano |
| Aplikace služby IIS nepublikované prostřednictvím Proxy aplikace Azure AD |ne |ano |
| Vzdálený přístup jako síť VPN, RDG |ne |ano |

I když vám může mít vyrovnané na řešení pro strategie, přesto potřebujete použít vyhodnocení z výše toho, kde se nachází vaši uživatelé.  To může způsobit řešení Chcete-li změnit.  Následující tabulku použijte při určování tohoto:

| Umístění uživatele | Upřednostňovanou možnost |
| --- | --- |
| Azure Active Directory |Více-FactorAuthentication v cloudu |
| Azure AD a místní AD využívající federaci se službou AD FS |Obojí |
| Azure AD a místní AD pomocí služby Azure AD Connect bez synchronizace hesla |Obojí |
| Azure AD a místní pomocí služby Azure AD Connect se synchronizací hesla |Obojí |
| Místní AD |Server Multi-Factor Authentication |

> [!NOTE]
> Také se ujistěte, že vybrané možnosti návrhu služby Multi-Factor authentication podporuje funkce, které jsou požadovány pro návrhu.  Další informace najdete v tématu [zvolte Multi-Factor Authentication řešení pro vás](authentication/concept-mfa-whichversion.md#what-am-i-trying-to-secure).
> 
> 

## <a name="multi-factor-auth-provider"></a>Zprostředkovatel vícefaktorového ověřování
Služba Multi-Factor authentication je k dispozici ve výchozím nastavení pro globální správce, kteří mají klienta služby Azure Active Directory. Ale pokud chcete rozšířit služby Multi-Factor authentication všichni uživatelé nebo chcete globální správci mohli využít výhod funkce jako je například portál pro správu, vlastní přivítání a sestavy, pak je nutné zakoupit a nakonfigurovat Poskytovatele služby Multi-Factor Authentication.

> [!NOTE]
> Také se ujistěte, že vybrané možnosti návrhu služby Multi-Factor authentication podporuje funkce, které jsou požadovány pro návrhu. 
> 
> 

## <a name="next-steps"></a>Další postup
[Určení požadavků na ochranu dat](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Další informace najdete v tématech
[Přehled aspektů návrhu](active-directory-hybrid-identity-design-considerations-overview.md)


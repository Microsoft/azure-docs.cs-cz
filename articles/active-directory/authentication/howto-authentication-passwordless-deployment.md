---
title: Plánování nasazení ověřování bez hesla pomocí Azure AD
description: Přečtěte si, jak naplánovat a nasadit implementaci ověřování bez hesla azure služby Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: baselden
author: iainfoulds
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3ed549e51b911452bca7d4d4a16c7ef45594a8f
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451427"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>Plánování nasazení ověřování bez hesla ve službě Azure Active Directory

> [!NOTE]
> Chcete-li vytvořit offline verzi tohoto plánu nasazení, použijte funkci Tisk do PDF v prohlížeči.

Většina kybernetických útoků začíná kompromitovaným uživatelským jménem a heslem. Organizace se snaží čelit hrozbě tím, že vyžadují, aby uživatelé používali jeden z následujících přístupů:

- Dlouhá hesla
- Složitá hesla
- Časté změny hesla
- Multi-Factor Authentication (MFA)

Výzkum společnosti Microsoft [ukazuje,](https://aka.ms/passwordguidance) že tyto snahy obtěžují uživatele a zvýšit náklady na podporu. Další informace naleznete v [tématu Your Pa$$word nezáleží](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

### <a name="benefits-of-passwordless-authentication"></a>Výhody ověřování bez hesla

- **Zvýšená bezpečnost**. Snižte riziko phishingových útoků a hesel odstraněním hesel jako útočné plochy.
-  **Lepší uživatelské prostředí**. Poumožňuje uživatelům pohodlný způsob přístupu k datům odkudkoli. Poskytněte snadný přístup k aplikacím a službám, jako je Outlook, OneDrive nebo Office, zatímco jsou mobilní.
-  **Robustní přehledy**. Získejte přehled o aktivitách bez hesla uživatelů díky robustnímu protokolování a auditování.

S heslem, heslo je nahrazeno něčím, co máte plus něco, co jste, nebo něco, co víte. Windows Hello pro firmy může například používat biometrické gesto, jako je obličej nebo otisk prstu, nebo pin specifický pro zařízení, který se nepřenáší v síti.

## <a name="passwordless-authentication-methods"></a>Metody ověřování bez hesla
Společnost Microsoft nabízí tři možnosti ověřování bez hesla, které pokrývají mnoho scénářů. Tyto metody lze použít v tandemu:

- [Windows Hello pro firmy](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) je nejlepší pro uživatele na jejich vyhrazených počítačích se systémem Windows.
- Přihlašování pomocí [bezpečnostních klíčů FIDO2](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) je užitečné zejména pro uživatele, kteří se přihlašují ke sdíleným počítačům, jako jsou kiosky, v situacích, kdy je používání telefonů omezeno, a pro vysoce privilegované identity.
- Přihlášení telefonem pomocí [aplikace Microsoft Authenticator](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) je užitečné pro poskytování možnosti bez hesla uživatelům s mobilními zařízeními. Aplikace Authenticator změní jakýkoli telefon iOS nebo Android na silné přihlašovací údaje bez hesla tím, že uživatelům umožní přihlásit se do libovolné platformy nebo prohlížeče. Uživatelé se přihlašují tak, že dostanou oznámení do svého telefonu, což odpovídá číslu zobrazenému na obrazovce číslu v telefonu a následnému potvrzení pomocí biometrických údajů nebo kódu PIN.

### <a name="passwordless-authentication-scenarios"></a>Scénáře ověřování bez hesla

Metody ověřování bez hesla společnosti Microsoft umožňují různé scénáře. Zvažte potřeby organizace, požadavky a možnosti každé metody ověřování pro výběr strategie ověřování bez hesla. Doporučujeme, aby každá organizace, která používá zařízení s Windows 10, používala Windows Hello pro firmy. Potom přidejte buď telefonické přihlášení (s aplikací Microsoft Authenticator) nebo bezpečnostní klíče pro další scénáře.

| Scénář | Ověření telefonu | Bezpečnostní klíče | Windows Hello pro firmy |
| --- | --- | --- | --- |
| **Přihlášení počítače**: <br> Z přiřazeného zařízení s Windows 10 | **Ne** | **Ano** <br> S biometrickým kódem PIN | **Ano**<br>s biometrickým rozpoznáváním a nebo PIN |
| **Přihlášení počítače**: <br> Ze sdíleného zařízení s Windows 10 | **Ne** | **Ano** <br> S biometrickým kódem PIN  | **Ne** |
| **Přihlášení webové aplikace**: <br>z počítače vyhrazeného pro uživatele | **Ano** | **Ano** <br> Za předpokladu, že jednotné přihlašování k aplikacím je povoleno přihlašování k počítači | **Ano**<br> Za předpokladu, že jednotné přihlašování k aplikacím je povoleno přihlašování k počítači |
| **Přihlášení webové aplikace**: <br> z mobilního zařízení nebo zařízení, které není v systému Windows | **Ano** | **Ne** | **Ne** |
| **Přihlášení počítače**: <br> Počítač, který není se systémem Windows | **Ne** | **Ne** | **Ne** |

Informace o výběru nejlepší metody pro vaši organizaci naleznete v [tématu Rozhodnutí o metodě bez hesla](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless#choose-a-passwordless-method).

## <a name="prerequisites"></a>Požadavky

Organizace musí před zahájením nasazení bez hesla splňovat následující požadavky:

| Požadavek | Aplikace Authenticator | Bezpečnostní klíče FIDO2 |
| --- | --- | --- |
| [Kombinovaná registrace pro azure vícefaktorové ověřování a samoobslužné resetování hesla (SSPR)](howto-registration-mfa-sspr-combined.md) je povolená | √ | √ |
| [Uživatelé můžou provádět vícefaktorové ověřování Azure](howto-mfa-getstarted.md) | √ | √ |
| [Uživatelé se zaregistrovali pro azure vícefaktorové ověřování a samodotykové ověřování](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [Uživatelé zaregistrovali svá mobilní zařízení do služby Azure Active Directory](../devices/overview.md) | √ |   |
| Windows 10 verze 1809 nebo vyšší pomocí podporovaného prohlížeče, jako je Microsoft Edge nebo Mozilla Firefox <br> (verze 67 nebo vyšší). <br> *Společnost Microsoft doporučuje verzi 1903 nebo vyšší pro nativní podporu*. |   | √ |
| Kompatibilní bezpečnostní klíče FIDO2. Ujistěte se, že používáte bezpečnostní zařízení FIDO2 [testované společností Microsoft](howto-authentication-passwordless-enable.md) nebo jiné kompatibilní bezpečnostní zařízení FIDO2. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Požadavky pro Windows Hello pro firmy

Předpoklady pro Windows Hello jsou vysoce závislé na tom, zda nasazujete v místní, hybridní nebo cloudové konfiguraci. Další informace naleznete v [úplném seznamu předpokladů pro Windows Hello pro firmy](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification).

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Uživatelé zaregistrovat svou metodu bez hesla jako součást toku registrace azure vícefaktorového ověřování. Vícefaktorové ověřování s uživatelským jménem a heslem spolu s jinou registrovanou metodou lze použít jako záložní v případě, že v některých scénářích nemohou používat svůj telefon nebo bezpečnostní klíč.

### <a name="licensing"></a>Licencování 
Neexistuje žádné další náklady na ověřování bez hesla, i když některé požadavky mohou vyžadovat předplatné premium. Podrobné informace o funkcích a licencích na [stránce licencování služby Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). 

## <a name="develop-a-plan"></a>Vypracovat plán

Zvažte vaše obchodní potřeby a případy použití pro každou metodu ověřování. Pak vyberte metodu, která nejlépe vyhovuje vašim potřebám.

### <a name="use-cases"></a>Případy použití

Následující tabulka popisuje případy použití, které mají být implementovány během tohoto projektu.

| Oblast | Popis |
| --- | --- |
| **Přístup** | Přihlášení bez hesla je k dispozici na podnikovém nebo osobním zařízení v rámci podnikové sítě nebo mimo ni. |
| **Auditování** | Údaje o využití jsou správcům k dispozici k auditu téměř v reálném čase. <br> Data o využití se stahují do podnikových systémů nejméně každých 29 dní, nebo se používá nástroj SIEM. |
| **Zásady správného řízení** | Životní cyklus přiřazení uživatelů k příslušné metodě ověřování a přidruženým skupinám je definován a sledován. |
| **Zabezpečení** | Přístup k příslušné metodě ověřování je řízen pomocí přiřazení uživatelů a skupin. <br> Přihlášení bez hesla mohou používat pouze oprávnění uživatelé. |
| **Výkon** | Časové osy šíření přiřazení přístupu jsou dokumentovány a monitorovány. <br> Časy přihlášení se měří pro snadné použití. |
| **Zkušenosti uživatele** | Uživatelé jsou si vědomi mobilní kompatibility. <br> Uživatelé mohou nakonfigurovat aplikaci Authenticator bez hesla přihlášení. |
| **Podpora** | Uživatelé jsou si vědomi, jak najít podporu pro problémy s přihlášením bez hesla. |

### <a name="engage-the-right-stakeholders"></a>Zapojte správné zúčastněné strany

Když technologické projekty selžou, je to obvykle z důvodu neshody očekávání na dopad, výsledky a odpovědnosti. Abyste se těmto nástrahám vyhnuli, [ujistěte se, že zapojujete správné zúčastněné strany](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) a že role zúčastněných stran v projektu jsou dobře pochopeny.

### <a name="plan-communications"></a>Plán komunikace

Komunikace je rozhodující pro úspěch každé nové služby. Proaktivně sdělujte, jak se změní prostředí uživatelů, kdy se změní a jak získat podporu, pokud se u něj vyskytnou problémy.

Vaše komunikace s koncovými uživateli by měla obsahovat následující informace:

- [Povolení kombinovaného prostředí pro registraci zabezpečení](howto-authentication-passwordless-phone.md)
- [Stažení aplikace Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md)
- [Registrace v aplikaci Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Přihlášení pomocí telefonu](../user-help/user-help-auth-app-sign-in.md)

Společnost Microsoft poskytuje šablony komunikace s [vícefaktorovým ověřováním](https://aka.ms/mfatemplates), šablony samoobslužného resetování hesla (SSPR) a [dokumentaci pro koncového uživatele,](../user-help/security-info-setup-signin.md) které pomáhají sestavovat komunikaci. [communication templates](https://www.microsoft.com/download/details.aspx?id=56768) Uživatelé se [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) mohou zaregistrovat přímo výběrem odkazů **Bezpečnostní údaje** na této stránce.

### <a name="plan-to-pilot"></a>Plán na pilotní projekt

Při nasazení ověřování bez hesla byste měli nejprve povolit jednu nebo více pilotních skupin. Skupiny můžete [vytvořit](../fundamentals/active-directory-groups-create-azure-portal.md) speciálně pro tento účel. Přidejte uživatele, kteří se zúčastní pilotního projektu, do skupin. Potom povolte nové metody ověřování bez hesla pro vybrané skupiny.

Skupiny lze synchronizovat z místního adresáře nebo z Azure AD. Jakmile budete spokojeni s výsledky pilotního projektu, můžete zapnout ověřování bez hesla pro všechny uživatele.

Podívejte se na [doporučené postupy pro pilota](https://aka.ms/deploymentplans) na stránce plány nasazení.

## <a name="plan-passwordless-authentication-with-the-microsoft-authenticator-app"></a>Plánování ověřování bez hesla pomocí aplikace Microsoft Authenticator

Aplikace Microsoft Authenticator je bezplatná možnost ke stažení z Google Play nebo Apple App Store. [Přečtěte si další informace o stahování aplikace Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6). Vyzvěte uživatele, aby si stáhli aplikaci Microsoft Authenticator. a postupujte podle pokynů, abyste povolili přihlášení telefonu. 

Změní jakýkoli telefon iOS nebo Android na silné pověření bez hesla. Uživatelé se přihlašují k libovolné platformě nebo prohlížeči tak, že dostanou oznámení do svého telefonu, přiřazují číslo zobrazené na obrazovce číslu v telefonu a poté k potvrzení pomocí biometrických údajů nebo KÓDU PIN. [Podívejte se na podrobnosti o tom, jak aplikace Microsoft Authenticator funguje](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless#microsoft-authenticator-app).

![přihlášení pomocí aplikace Authenticator](./media/howto-authentication-passwordless-deployment/passwordless-dp-sign-in.png)

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Technické aspekty aplikace Microsoft Authenticator

**Integrace služby AD FS** – Pokud uživatel povolí pověření microsoft authenticator bez hesla, ověřování pro tohoto uživatele ve výchozím nastavení odešle oznámení ke schválení. Uživatelům v hybridním tenantovi není bráněno v přemísťování služby ADFS pro přihlášení, pokud nezvolí možnost Použít heslo. Tento proces také obchází všechny místní zásady podmíněného přístupu a předávací toky ověřování. Pokud je však zadán *a login_hint,* je uživatel předán službě ADFS a obejde možnost použít pověření bez hesla.

**Server azure vícefaktorového ověřování** – koncoví uživatelé, kteří jsou povoleni pro vícefaktorové ověřování prostřednictvím místního serveru Azure MFA organizace, můžou vytvářet a používat přihlašovací přihlašovací údaje pro telefon bez hesla. Pokud se uživatel pokusí upgradovat více instalací (5 nebo více) microsoft authenticator s pověřením, tato změna může mít za následek chybu.

**Registrace zařízení** – chcete-li použít aplikaci Authenticator pro ověřování bez hesla, musí být zařízení registrované v tenantovi Azure AD a nemůže být sdílené zařízení. Zařízení lze zaregistrovat pouze v jednom klientovi. Toto omezení znamená, že pro přihlašování po telefonu pomocí aplikace Authenticator je podporován pouze jeden pracovní nebo školní účet.

## <a name="plan-passwordless-authentication-with-fido2-security-keys"></a>Plánování ověřování bez hesla pomocí bezpečnostních klíčů FIDO2
S klíči zabezpečení jsou k dispozici tři typy přihlašovacích nasazení bez hesla:

-    Webové aplikace Azure Active Directory v podporovaném prohlížeči
-    Zařízení Azure Active Directory připojená k Windows 10
-    Hybridní zařízení Azure Active Directory připojená k Windows 10 (preview)
     -    Poskytuje přístup k cloudovým i místním prostředkům. Další informace o přístupu k místním prostředkům najdete v [tématu přihlašující služby SSO k místním prostředkům pomocí klíčů FIDOP2.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises)

Je nutné povolit **kompatibilní klíče zabezpečení FIDO2**. Společnost Microsoft oznámila [klíčová partnerství s klíčovými dodavateli FIDO2](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Microsoft-passwordless-partnership-leads-to-innovation-and-great/ba-p/566493).

**Pro webové aplikace Azure AD a zařízení Azure AD windows připojená**:

-    Windows 10 verze 1809 nebo vyšší pomocí podporovaného prohlížeče, jako je Microsoft Edge nebo Mozilla Firefox (verze 67 nebo vyšší). 
-    Windows 10 verze 1809 podporuje přihlášení FIDO2 a může vyžadovat nasazení softwaru od výrobce klíčů FIDO2. Doporučujeme použít verzi 1903 nebo novější. 

**Pro hybridní zařízení připojená k doméně služby Azure Active Directory**: 
-    Windows 10 Insider sestavení 18945 nebo novější
-    Plně opravené domény se systémem Windows Server 2016 nebo 2019.
-    Nejnovější verze Azure AD Connect

Úplný seznam požadavků najdete v [tématu Povolení přihlašování pomocí azure active directory pomocí klíče zabezpečení bez hesla k zařízením s Windows 10.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-windows#requirements)


### <a name="security-key-life-cycle"></a>Životní cyklus bezpečnostního klíče

Bezpečnostní klíče umožňují přístup k prostředkům a měli byste plánovat správu těchto fyzických zařízení.

1. **Distribuce klíčů**: Naplánujte, jak zřídit klíče pro vaši organizaci. Můžete mít centralizovaný proces zřizování nebo povolit koncovým uživatelům zakoupit klíče kompatibilní s FIDO 2.0.
1. **Aktivace klíče**: Koncoví uživatelé musí bezpečnostní klíč aktivovat samostatně. Koncoví uživatelé zaregistrují své bezpečnostní klíče a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) povolí druhý faktor (PIN nebo biometrický) při prvním použití.
1. **Zakázání klíče**: Zatímco funkce bezpečnostního klíče je ve fázi náhledu, neexistuje žádný způsob, jak správce odebrat klíč z uživatelského účtu. Uživatel jej musí odebrat. Pokud dojde ke ztrátě nebo odcizení klíče:
   1. Odeberte uživatele z libovolné skupiny povolené pro ověřování bez hesla.
   1. Ověřte, zda klíč odebrali jako metodu ověřování.
   1. Vystaví nový klíč. **Výměna klíče**: Uživatelé mohou povolit dva klíče zabezpečení současně. Při výměně bezpečnostního klíče se ujistěte, že uživatel odstranil také klíč, který byl nahrazen.

### <a name="enable-windows-10-support"></a>Povolení podpory Windows 10

Povolení přihlášení k Windows 10 pomocí klíčů zabezpečení FIDO2 vyžaduje povolení funkce poskytovatele pověření ve Windows 10. Vyberte jednu z následujících možností:

- [Povolení zprostředkovatele pověření pomocí Intune](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - Doporučené možností je nasazení Intune.
- [Povolení zprostředkovatele pověření pomocí zřizovacího balíčku](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Pokud nasazení Intune není možné, správci musí nasadit balíček na každém počítači, aby povolili funkce zprostředkovatele pověření. Instalaci balíčku lze provést jednou z následujících možností:
      - Zásady skupiny nebo Správce konfigurace
      - Místní instalace na počítači s Windows 10
- [Povolení zprostředkovatele pověření pomocí zásad skupiny](howto-authentication-passwordless-security-key-windows.md#enable-with-group-policy)
   - Podporované jenom pro hybridní zařízení Azure AD připojená.

#### <a name="enable-on-premises-integration"></a>Povolení místní integrace

Chcete-li povolit přístup k místním prostředkům, postupujte podle pokynů [k povolení přístupu klíče zabezpečení bez hesla k místním prostředkům (náhled).](howto-authentication-passwordless-security-key-on-premises.md)

> [!IMPORTANT]
> Tyto kroky musí být také dokončeny pro všechny hybridní zařízení připojená k Azure AD využívat klíče zabezpečení FIDO2 pro Windows 10 přihlášení.

### <a name="register-security-keys"></a>Registrace klíčů zabezpečení

Uživatelé musí zaregistrovat svůj klíč zabezpečení na každém ze svých počítačů Azure Active Directory spojených s Windows 10.

Další informace naleznete [v tématu Registrace a správa klíčů zabezpečení FIDO2 uživatelem](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys).


## <a name="plan-auditing-security-and-testing"></a>Plánování auditů, zabezpečení a testování
Plánování auditování, které splňuje vaše organizační a compliance frameworky, je nezbytnou součástí vašeho nasazení.

### <a name="auditing-passwordless"></a>Auditování bez hesla

Azure AD má sestavy, které poskytují technické a obchodní přehledy. Nech vlastníky vašich obchodních a technických aplikací převzít vlastnictví těchto sestav a využívat je na základě požadavků vaší organizace.

Oddíl **Metody ověřování** na portálu Azure Active Directory je místo, kde správci můžou povolit a spravovat nastavení přihlašovacích údajů bez hesla.

Azure AD přidá položky do protokolů auditu, když:

- Správce provede změny v části Metody ověřování.
- Uživatel provede jakoukoli změnu svých přihlašovacích údajů v rámci služby Azure Active Directory.

Následující tabulka obsahuje některé příklady typických scénářů vykazování:

|   | Řízení rizik | Zvýšení produktivity | Zásady správného řízení a dodržování předpisů |
| --- | --- | --- | --- |
| **Typy sestav** | Metody ověřování - uživatelé registrovaní pro kombinovanou registraci zabezpečení | Metody ověřování – uživatelé registrovaní pro oznámení aplikace | Přihlášení: zkontrolujte, kdo přistupuje k tenantovi a jak |
| **Potenciální akce** | Cíloví uživatelé, kteří ještě nejsou zaregistrováni | Podpojit přijetí aplikace Microsoft Authenticator nebo bezpečnostních klíčů | Odvolání přístupu nebo vynucení dalších zásad zabezpečení pro správce |

**Azure AD uchovává většinu dat auditování po dobu 30 dnů** a zpřístupňuje data prostřednictvím portálu Azure Admin nebo rozhraní API, které si můžete stáhnout do svých analytických systémů. Pokud požadujete delší uchovávání, exportujte a spotřebováváte protokoly v nástroji SIEM, jako je [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk nebo Sumo Logic. [Přečtěte si další informace o zobrazení zpráv o přístupu a používání](../reports-monitoring/overview-reports.md).

Uživatelé se mohou zaregistrovat a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)spravovat svá pověření přejdete na . Tento odkaz směruje uživatele na prostředí správy pověření koncového uživatele, které bylo povoleno prostřednictvím kombinovaného prostředí pro registraci samoobslužné správě a vícefaktorového ověřování. Azure AD protokoly registraci zařízení zabezpečení FIDO2 a změny metod ověřování uživateli.

### <a name="plan-security"></a>Plánování zabezpečení
V rámci tohoto plánu zavádění společnost Microsoft doporučuje, aby bylo povoleno ověřování bez hesla pro všechny účty privilegovaných správců.

Pokud uživatelé povolí nebo zakážou účet na klíč zabezpečení nebo resetují druhý faktor pro klíč zabezpečení na svých počítačích se systémem Windows 10, je do protokolu zabezpečení přidána položka pod následujícími ID událostí: *4670* a *5382*.

### <a name="plan-testing"></a>Plánování testování

V každé fázi nasazení při testování scénářů a přijetí, ujistěte se, že výsledky jsou podle očekávání.

#### <a name="testing-the-microsoft-authenticator-app"></a>Testování aplikace Microsoft Authenticator

Následují ukázkové testovací případy pro ověřování bez hesla pomocí aplikace Microsoft Authenticator:

| Scénář | Očekávané výsledky |
| --- | --- |
| Uživatel může zaregistrovat aplikaci Microsoft Authenticator | Uživatel může zaregistrovat aplikaci z aka.ms/mysecurityinfo |
| Uživatel může povolit přihlášení k telefonu | Přihlášení po telefonu nakonfigurované pro pracovní účet |
| Uživatel má přístup k aplikaci s přihlášením k telefonu | Uživatel prochází tok emitora přihlášení a dosáhne aplikace. |
| Otestujte registraci přihlašování k opětovnému zápisu zpět vypnutím přihlášení bez hesla microsoft authenticator u obrazovky Metody ověřování na portálu Azure Active Directory | Dříve povolené uživatelé nemohli používat přihlášení bez hesla z Microsoft Authenticator. |
| Odebrání přihlášení k telefonu z aplikace Microsoft Authenticator | Pracovní účet již není k dispozici v aplikaci Microsoft Authenticator |

#### <a name="testing-security-keys"></a>Testování klíčů zabezpečení

Následují ukázkové testovací případy pro ověřování bez hesla pomocí klíčů zabezpečení.

**Přihlášení fido bez hesla k zařízením Azure Active Directory se připojilo k Windows 10**

| Scénář | Očekávané výsledky |
| --- | --- |
| Uživatel může zaregistrovat zařízení FIDO2 (1809) | Uživatel může zaregistrovat zařízení FIDO2 pomocí nastavení > účtů > možnosti přihlášení > bezpečnostní klíč |
| Uživatel může resetovat zařízení FIDO2 (1809) | Uživatel může resetovat zařízení FIDO2 pomocí softwaru výrobce |
| Uživatel se může přihlásit pomocí zařízení FIDO2 (1809) | Uživatel může vybrat bezpečnostní klíč z přihlašovacího okna a úspěšně se přihlásit. |
| Uživatel může zaregistrovat zařízení FIDO2 (1903) | Uživatel může zaregistrovat zařízení FIDO2 na adrese Nastavení > účtů > možnosti přihlášení > bezpečnostní klíč |
| Uživatel může resetovat zařízení FIDO2 (1903) | Uživatel může obnovit zařízení FIDO2 v nastavení > účty > možnosti přihlášení > bezpečnostní klíč |
| Uživatel se může přihlásit pomocí zařízení FIDO2 (1903) | Uživatel může vybrat bezpečnostní klíč z přihlašovacího okna a úspěšně se přihlásit. |

**Přihlášení fido bez hesla k webovým aplikacím Azure AD**

| Scénář | Očekávané výsledky |
| --- | --- |
| Uživatel může zaregistrovat zařízení FIDO2 na aka.ms/mysecurityinfo pomocí aplikace Microsoft Edge | Registrace by měla být úspěšná |
| Uživatel může zaregistrovat zařízení FIDO2 na aka.ms/mysecurityinfo pomocí Firefoxu | Registrace by měla být úspěšná |
| Uživatel se může přihlásit k OneDrivu online pomocí zařízení FIDO2 pomocí Microsoft Edge | Přihlášení by mělo být úspěšné. |
| Uživatel se může přihlásit k OneDrivu online pomocí zařízení FIDO2 pomocí Firefoxu | Přihlášení by mělo být úspěšné. |
| Otestujte vrácení registrace zařízení FIDO2 vypnutím klíčů zabezpečení FIDO2 v okně metody ověřování na portálu Azure Active Directory | Uživatelé budou vyzváni k přihlášení pomocí svého bezpečnostního klíče. Uživatelé se úspěšně přihlásí a zobrazí se chyba: "Zásady vaší společnosti vyžadují, abyste k přihlášení použili jinou metodu." Uživatelé by pak měli mít možnost vybrat jinou metodu a úspěšně se přihlásit. Zavřete okno a znovu se přihlaste, abyste ověřili, že se nezobrazuje stejná chybová zpráva. |

### <a name="plan-for-rollback"></a>Plán vrácení zpět

Ačkoli ověřování bez hesla je odlehčená funkce s minimálním dopadem na koncové uživatele, může být nutné vrátit zpět.

Vrácení zpět vyžaduje, aby se správce přihlásil k portálu Azure Active Directory, vyberte požadované silné metody ověřování a změňte možnost povolit na **Ne**. Tento proces vypne funkci bez hesla pro všechny uživatele.

Uživatelé, kteří již zaregistrovali zabezpečovací zařízení FIDO2, budou při dalším přihlášení vyzváni k použití zabezpečovacího zařízení a zobrazí se následující chyba:

![zvolit jiný způsob přihlášení](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

## <a name="deploy-and-troubleshoot-passwordless-authentication"></a>Nasazení a řešení potíží s ověřováním bez hesla

Postupujte podle kroků zarovnaných s vámi zvolenou metodou níže.

### <a name="required-administrative-roles"></a>Požadované role pro správu

| Azure AD Role | Popis |
| --- | --- |
| Globální správce|Nejméně privilegovaná role schopná implementovat kombinované registrační prostředí. |
| Správce ověřování | Nejméně privilegovaná role schopná implementovat a spravovat metody ověřování. |
| Uživatel | Nejméně privilegovaná role pro konfiguraci aplikace Authenticator na zařízení nebo pro registraci zařízení bezpečnostního klíče pro webové prostředí nebo přihlášení k Windows 10. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Nasazení přihlášení k telefonu pomocí aplikace Microsoft Authenticator

Postupujte podle kroků v článku [Povolit přihlášení bez hesla pomocí aplikace Microsoft Authenticator](howto-authentication-passwordless-phone.md) a povolit aplikaci Microsoft Authenticator jako metodu ověřování bez hesla ve vaší organizaci.

### <a name="deploy-fido2-security-key-sign-in"></a>Nasazení přihlašovacího klíče zabezpečení FIDO2

Postupujte podle kroků v článku [Povolit přístup ový klíč zabezpečení bez hesla pro Azure AD,](howto-authentication-passwordless-security-key.md) abyste povolili klíče zabezpečení FIDO2 jako metody ověřování bez hesla.

### <a name="troubleshoot-phone-sign-in"></a>Poradce při potížích s přihlášením k telefonu

| Scénář | Řešení |
| --- | --- |
| Uživatel nemůže provést kombinovanou registraci. | Ujistěte se, že je [povolena kombinovaná registrace.](concept-registration-mfa-sspr-combined.md) |
| Uživatel nemůže povolit aplikaci pro ověřování přihlášení k telefonu. | Ujistěte se, že uživatel je v oboru pro nasazení. |
| Uživatel není v oboru pro ověřování bez hesla, ale je prezentována s možností přihlášení bez hesla, které nemohou dokončit. | K tomuto scénáři dochází, když uživatel povolil přihlášení k telefonu v aplikaci před vytvořením zásady. <br> *Povolení přihlášení*: Přidejte uživatele do oboru uživatelů, kteří mají povolené přihlášení bez hesla. <br> *Chcete-li blokovat přihlášení:* chcete- li, aby uživatel odebral jejich přihlašovací údaje z této aplikace. |

### <a name="troubleshoot-security-key-sign-in"></a>Poradce při potížích s přihlášením klíče zabezpečení

| Scénář | Řešení |
| --- | --- |
| Uživatel nemůže provádět kombinovanou registraci. | Ujistěte se, že je [povolena kombinovaná registrace.](concept-registration-mfa-sspr-combined.md) |
| Uživatel nemůže přidat klíč zabezpečení v nastavení [zabezpečení](https://aka.ms/mysecurityinfo). | Ujistěte se, že jsou [povoleny bezpečnostní klíče.](howto-authentication-passwordless-security-key.md) |
| Uživatel nemůže přidat klíč zabezpečení v možnostech přihlášení k Windows 10. | [Ujistěte se, že se přihlašují bezpečnostní klíče pro Windows](howto-authentication-passwordless-enable.md) |
| **Chybová zpráva**: Zjistili jsme, že tento prohlížeč nebo operační systém nepodporuje bezpečnostní klíče FIDO2. | Zabezpečovací zařízení FIDO2 bez hesla lze zaregistrovat pouze v podporovaných prohlížečích (Microsoft Edge, Firefox verze 67) v systému Windows 10 verze 1809 nebo vyšší. |
| **Chybová zpráva**: Zásady společnosti vyžadují, abyste k přihlášení použili jinou metodu. | Nejste si jisti, že klíče zabezpečení jsou v tenantovi povolené. |
| Uživatel nemůže spravovat klíč zabezpečení v systému Windows 10 verze 1809 | Verze 1809 vyžaduje použití softwaru pro správu bezpečnostních klíčů poskytovaného dodavatelem klíče FIDO2. Obraťte se na dodavatele pro podporu. |
| Myslím, že můj bezpečnostní klíč FIDO2 může být vadný – jak jej mohu otestovat. | Přejděte [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/)na , zadejte přihlašovací údaje pro testovací účet, **+** připojte podezřelý klíč zabezpečení, vyberte tlačítko v pravém horním rohu obrazovky, klepněte na tlačítko vytvořit a projít procesem vytváření. Pokud se tento scénář nezdaří, může být vadné zařízení. |

## <a name="next-steps"></a>Další kroky

- [Povolení klíčů zabezpečení bez hesla pro přihlášení pro Azure AD](howto-authentication-passwordless-security-key.md)
- [Povolení přihlášení bez hesla pomocí aplikace Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Další informace o využití metod ověřování & přehledech](howto-authentication-methods-usage-insights.md)


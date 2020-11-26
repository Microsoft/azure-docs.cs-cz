---
title: Plánování nasazení ověřování bez hesla pomocí Azure AD
description: Naučte se plánovat a nasazovat Azure Active Directory implementaci ověřování nejenom s heslem.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: baselden
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4c06e53d3ddfb5f344a759e31d83cb22ce6d4d3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96174132"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>Plánování nasazení ověřování s neplatnými hesly v Azure Active Directory

> [!NOTE]
> Pokud chcete vytvořit offline verzi tohoto plánu nasazení, použijte funkci Tisk v prohlížeči do formátu PDF.

Většina internetoví útoků začíná napadeným uživatelským jménem a heslem. Organizace se pokusí tuto hrozbu zmírnit tím, že požadují, aby uživatelé používali některý z následujících přístupů:

- Dlouhá hesla
- Složitá hesla
- Časté změny hesla
- Multi-Factor Authentication (MFA)

Výzkum Microsoftu [ukazuje](https://aka.ms/passwordguidance) , že tato snaha annoy uživatele a provedou náklady na podporu. Další informace najdete v tématu o [nezáleží na vašem PA $ $Word](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

### <a name="benefits-of-passwordless-authentication"></a>Výhody ověřování s nehesly

- **Zvýšené zabezpečení**. Odebráním hesel jako prostoru pro útoky snižte riziko útoků phishing a postřik hesla.
-  **Lepší uživatelské prostředí**. Poskytněte uživatelům pohodlný způsob, jak přistupovat k datům odkudkoli. Poskytněte snadný přístup k aplikacím a službám, jako je Outlook, OneDrive nebo Office, a to i v mobilní aplikaci.
-  **Robustní přehledy**. Robustní protokolování a auditování vám umožní získat přehled o aktivitě bez hesla uživatelů.

Bez hesla je heslo nahrazeno něčím, co máte vy nebo něco, co znáte. Například Windows Hello pro firmy může používat biometrická gesta jako obličej nebo otisk prstu nebo PIN kód specifický pro zařízení, které se nepřenáší přes síť.

## <a name="passwordless-authentication-methods"></a>Metody ověřování s nehesly
Microsoft nabízí tři možnosti ověřování s nehesly, které pokrývají mnoho scénářů. Tyto metody lze použít společně:

- [Windows Hello pro firmy](./concept-authentication-passwordless.md) je nejvhodnější pro uživatele na svých vyhrazených počítačích s Windows.
- Přihlášení k bezpečnostnímu klíči pomocí [klíčů zabezpečení FIDO2](./concept-authentication-passwordless.md) je zvlášť užitečné pro uživatele, kteří se přihlásí ke sdíleným počítačům, jako jsou veřejné terminály, v situacích, kdy je používání telefonů omezené a pro vysoce privilegované identity.
- Telefonický zápis pomocí [Microsoft Authenticator aplikace](./concept-authentication-passwordless.md) je užitečný pro poskytování možnosti bez hesla uživatelům s mobilními zařízeními. Aplikace ověřovatele přepíná telefon s iOS nebo Androidem do silných přihlašovacích údajů bez hesla tím, že uživatelům umožní přihlašovat se k libovolné platformě nebo prohlížeči. Uživatelé se přihlásí pomocí oznámení na telefon, odpovídajícím číslem zobrazeným na obrazovce na telefonu a následným použitím jejich biometrických dat nebo PIN kódu k potvrzení.

### <a name="passwordless-authentication-scenarios"></a>Scénáře ověřování s nehesly

Metody ověřování s neheslem od Microsoftu umožňují různé scénáře. Pokud chcete vybrat strategii ověřování bez hesla, vezměte v úvahu potřeby vaší organizace, požadavky a možnosti jednotlivých metod ověřování. Doporučujeme, aby všechny organizace, které používají zařízení s Windows 10, používaly Windows Hello pro firmy. Pak přidejte buď telefonické přihlášení (s aplikací Microsoft Authenticator), nebo bezpečnostní klíče pro další scénáře.

| Scénář | Ověřování pro telefon | Klíče zabezpečení | Windows Hello pro firmy |
| --- | --- | --- | --- |
| **Přihlášení počítače**: <br> Z přiřazeného zařízení s Windows 10 | **Ne** | **Ano** <br> S biometrikou, připnout | **Ano**<br>pomocí biometrického rozpoznávání a kódu PIN |
| **Přihlášení počítače**: <br> Ze sdíleného zařízení s Windows 10 | **Ne** | **Ano** <br> S biometrikou, připnout  | **Ne** |
| **Přihlášení k webové aplikaci**: <br>z počítače vyhrazeného uživatelem | **Ano** | **Ano** <br> Zadané jednotné přihlašování k aplikacím je povolené při přihlášení k počítači. | **Ano**<br> Zadané jednotné přihlašování k aplikacím je povolené při přihlášení k počítači. |
| **Přihlášení k webové aplikaci**: <br> z mobilního zařízení nebo z jiného zařízení než Windows | **Ano** | **Ne** | **Ne** |
| **Přihlášení počítače**: <br> Počítač s jiným systémem než Windows | **Ne** | **Ne** | **Ne** |

Informace o tom, jak vybrat nejlepší metodu pro vaši organizaci, najdete v tématu věnovaném [postupu bez hesla](./concept-authentication-passwordless.md#choose-a-passwordless-method).

## <a name="prerequisites"></a>Předpoklady

Organizace musí před zahájením nasazení s neplatným heslem splňovat následující požadavky:

| Požadavek | Ověřovací aplikace | FIDO2 klíče zabezpečení |
| --- | --- | --- |
| Je povolená [Kombinovaná registrace pro Azure AD Multi-Factor Authentication a Samoobslužné resetování hesla (SSPR)](howto-registration-mfa-sspr-combined.md) . | √ | √ |
| [Uživatelé můžou provádět Multi-Factor Authentication služby Azure AD.](howto-mfa-getstarted.md) | √ | √ |
| [Uživatelé zaregistrovali pro Azure AD Multi-Factor Authentication a SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [Uživatelé zaregistrovali svoje mobilní zařízení Azure Active Directory](../devices/overview.md) | √ |   |
| Windows 10 verze 1809 nebo vyšší s použitím podporovaného prohlížeče, jako je Microsoft Edge nebo Mozilla Firefox <br> (verze 67 nebo vyšší). <br> *Společnost Microsoft doporučuje pro nativní podporu verzi 1903 nebo vyšší*. |   | √ |
| Kompatibilní klíče zabezpečení FIDO2 Ujistěte se, že používáte zařízení se zabezpečením [Microsoft testováno a ověřené](./concept-authentication-passwordless.md) FIDO2 nebo jiné kompatibilní zařízení zabezpečení FIDO2. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Předpoklady pro Windows Hello pro firmy

Požadavky na Windows Hello jsou vysoce závislé na tom, jestli nasazujete v konfiguraci místních, hybridních nebo cloudových konfigurací. Další informace najdete v [úplném seznamu požadavků pro Windows Hello pro firmy](/windows/security/identity-protection/hello-for-business/hello-identity-verification).

### <a name="azure-ad-multi-factor-authentication"></a>Vícefaktorové ověřování Azure AD

Uživatelé registrují svou metodu nejenom hesla jako součást toku registrace Multi-Factor Authentication služby Azure AD. Vícefaktorové ověřování s uživatelským jménem a heslem společně s jinou registrovanou metodou se dá použít jako záložní pro případ, že v některých scénářích nemůžou použít svůj telefon nebo bezpečnostní klíč.

### <a name="licensing"></a>Licencování 
Pro ověřování bez hesla se neúčtují žádné další poplatky, i když některé předpoklady můžou vyžadovat předplatné Premium. Podrobné informace o funkcích a licencování najdete na [stránce Azure Active Directory licencování](https://azure.microsoft.com/pricing/details/active-directory/). 

## <a name="develop-a-plan"></a>Vývoj plánu

Vezměte v úvahu své obchodní potřeby a případy použití pro jednotlivé metody ověřování. Pak vyberte metodu, která nejlépe vyhovuje vašim potřebám.

### <a name="use-cases"></a>Případy použití

Následující tabulka popisuje případy použití, které mají být implementovány během tohoto projektu.

| Oblast | Popis |
| --- | --- |
| **Přístup** | Přihlášení bez hesla je k dispozici v podnikovém nebo osobním zařízení v rámci podnikové sítě nebo mimo ni. |
| **Auditování** | Data o využití jsou k dispozici správcům k zaznamenávání téměř v reálném čase. <br> Data o využití se stáhnou do podnikových systémů aspoň každých 29 dní nebo se použije nástroj SIEM Tool. |
| **Zásady správného řízení** | Je definováno a sledováno životní cyklus přiřazení uživatelů k příslušné metodě ověřování a přidruženým skupinám. |
| **Zabezpečení** | Přístup k příslušné metodě ověřování je řízen pomocí přiřazení uživatelů a skupin. <br> Jenom autorizovaní uživatelé můžou používat přihlášení nejenom heslem. |
| **Výkon** | Časové osy šíření přístupu k přiřazení jsou zdokumentovány a monitorovány. <br> Časy přihlášení se měří pro snadné použití. |
| **Činnost koncového uživatele** | Uživatelé mají informace o kompatibilitě mobilních zařízení. <br> Uživatelé můžou nakonfigurovat přihlášení k neheslům aplikace ověřovatele. |
| **Podpora** | Uživatelé si vědomi, jak najít podporu pro problémy s přihlašováním bez hesla. |

### <a name="engage-the-right-stakeholders"></a>Zapojení správných zúčastněných stran

Když projekty technologie selžou, je obvykle z důvodu neshodných očekávání na dopad, výsledky a odpovědnosti. Chcete-li se těmto nástrah vyhnout, [Ujistěte se, že jste připravují správné zúčastněné strany](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) a že role účastníků v projektu jsou dobře pochopitelné.

### <a name="plan-communications"></a>Plán komunikace

Komunikace je zásadní pro úspěch jakékoli nové služby. Proaktivně komunikuje s tím, jak se budou uživatelé měnit, když se změní, a jak získat podporu, pokud budou mít problémy.

Vaše komunikace s koncovými uživateli by měla obsahovat tyto informace:

- [Povolení kombinovaného prostředí pro registraci zabezpečení](howto-authentication-passwordless-phone.md)
- [Stahuje se aplikace Microsoft Authenticator.](../user-help/user-help-auth-app-download-install.md)
- [Registrace v aplikaci Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Přihlášení pomocí telefonu](../user-help/user-help-auth-app-sign-in.md)

Microsoft poskytuje [komunikační](https://aka.ms/mfatemplates)šablony služby Multi-Factor Authentication, Self-Service [šablony komunikace](https://www.microsoft.com/download/details.aspx?id=56768)pro resetování hesla (SSPR) a [dokumentaci pro koncové uživatele](../user-help/security-info-setup-signin.md) , které vám pomůžou s konceptem vaší komunikace. Můžete odeslat uživatele do, [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) aby se zaregistrovali přímo, a to tak, že na této stránce vyberete odkazy na **informace o zabezpečení** .

### <a name="plan-to-pilot"></a>Plánování pilotního nasazení

Při nasazení ověřování bez hesla byste měli nejdřív povolit jednu nebo více pilotních skupin. Skupiny můžete [vytvořit](../fundamentals/active-directory-groups-create-azure-portal.md) speciálně pro tento účel. Přidejte uživatele, kteří se budou součástí pilotního projektu, do skupin. Potom povolte nové metody ověřování bez hesla pro vybrané skupiny.

Skupiny je možné synchronizovat z místního adresáře nebo z Azure AD. Jakmile budete spokojeni s výsledky svého pilotního projektu, můžete pro všechny uživatele přepnout na ověřování bez hesla.

[Doporučené postupy pro pilotní](../fundamentals/active-directory-deployment-plans.md) nasazení najdete na stránce plány nasazení.

## <a name="plan-passwordless-authentication-with-the-microsoft-authenticator-app"></a>Plánování ověřování bez hesla pomocí aplikace Microsoft Authenticator

Aplikace Microsoft Authenticator je zdarma ke stažení z Google Play nebo z Apple App Storu. [Přečtěte si další informace o stahování aplikace Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6). Uživatelé si stáhnou aplikaci Microsoft Authenticator. a postupujte podle pokynů pro povolení přihlášení telefonem. 

Přepíná telefon s iOS nebo Androidem do silných přihlašovacích údajů bez hesla. Uživatelé se přihlásí k libovolné platformě nebo prohlížeči tím, že získají oznámení na telefonu, odpovídají číslu zobrazenému na obrazovce na telefonu a pak se k potvrzení použije biometrika nebo PIN. [Podívejte se na podrobnosti o tom, jak aplikace Microsoft Authenticator funguje](./concept-authentication-passwordless.md#microsoft-authenticator-app).

![přihlášení pomocí ověřovací aplikace](./media/howto-authentication-passwordless-deployment/passwordless-dp-sign-in.png)

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Technické požadavky pro aplikaci Microsoft Authenticator

**AD FS Integration** – Pokud uživatel povolí Microsoft Authenticator přihlašovací údaje bez hesla, ověřování pro tohoto uživatele standardně posílá oznámení ke schválení. Uživatelům v hybridním tenantovi se zabrání v přesměrování na službu AD FS, aby se přihlásili, pokud nevyberou místo toho použít heslo. Tento proces také obchází všechny místní zásady podmíněného přístupu a předávací ověřovací toky. Pokud je však zadán *login_hint* , uživatel je předaný do služby ADFS a obejít možnost použití přihlašovacích údajů bez hesla.

**Služba Azure AD Multi-Factor Authentication serveru** – koncoví uživatelé s povoleným ověřováním službou Multi-Factor Authentication prostřednictvím místního Azure MFA serveru organizace může vytvořit a použít jenom jedno přihlašovací údaje pro přihlášení k telefonu bez hesla. Pokud se uživatel pokusí upgradovat více instalací (5 nebo více) Microsoft Authenticator s přihlašovacími údaji, může tato změna způsobit chybu.

**Registrace zařízení** : Pokud chcete používat ověřovací aplikaci pro ověřování bez hesla, musí být zařízení zaregistrované v TENANTOVI Azure AD a nemůže být sdíleným zařízením. Zařízení se dá zaregistrovat jenom v jednom tenantovi. Tento limit znamená, že pro přihlášení telefonem pomocí ověřovací aplikace se podporuje jenom jeden pracovní nebo školní účet.

## <a name="plan-passwordless-authentication-with-fido2-security-keys"></a>Plánování ověřování bez hesla pomocí klíčů zabezpečení FIDO2
K dispozici jsou tři typy nasazení přihlášení bez hesla s bezpečnostními klíči:

-    Azure Active Directory webové aplikace v podporovaném prohlížeči
-    Azure Active Directory připojená zařízení s Windows 10
-    Zařízení s Windows 10 připojená k hybridnímu Azure Active Directory (Preview)
     -    Poskytuje přístup ke cloudovým i místním prostředkům. Další informace o přístupu k místním prostředkům najdete v tématu [jednotné přihlašování k místním prostředkům pomocí FIDOP2 klíčů](./howto-authentication-passwordless-security-key-on-premises.md) .

Musíte povolit **kompatibilní bezpečnostní klíče FIDO2**. Společnost Microsoft oznámila [klíčová partnerství s dodavateli FIDO2 klíčů](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Microsoft-passwordless-partnership-leads-to-innovation-and-great/ba-p/566493).

**Pro webové aplikace Azure AD a zařízení s Windows připojená ke službě Azure AD**:

-    Windows 10 verze 1809 nebo vyšší s použitím podporovaného prohlížeče, jako je Microsoft Edge nebo Mozilla Firefox (verze 67 nebo novější). 
-    Windows 10 verze 1809 podporuje přihlašování FIDO2 a může vyžadovat software od výrobce FIDO2 Key, který se má nasadit. Doporučujeme použít verzi 1903 nebo novější. 

**Pro hybridní Azure Active Directory zařízení připojených k doméně**: 
-    Windows 10 verze 2004 nebo novější
-    Plně opravené doménové servery se systémem Windows Server 2016 nebo 2019.
-    Nejnovější verze Azure AD Connect

Úplný seznam požadavků najdete v tématu [Povolení přihlášení k bezpečnostnímu klíči bez hesla pro zařízení s Windows 10 pomocí Azure Active Directory](./howto-authentication-passwordless-security-key-windows.md#requirements).


### <a name="security-key-life-cycle"></a>Životní cyklus bezpečnostního klíče

Klíče zabezpečení umožňují přístup k vašim prostředkům a měli byste naplánovat správu těchto fyzických zařízení.

1. **Distribuce klíčů**: Naplánujte způsob zřízení klíčů vaší organizaci. Je možné, že máte centralizovaný proces zřizování nebo pokud chcete, aby koncoví uživatelé koupili klíče kompatibilní s FIDO 2,0.
1. **Aktivace klíče**: koncoví uživatelé musí klíč zabezpečení sami aktivovat. Koncoví uživatelé zaregistrují své bezpečnostní klíče na [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) a povolí druhý faktor (PIN nebo biometrika) při prvním použití.
1. **Zakázání klíče**: i když je funkce klíče zabezpečení ve fázi Preview, neexistuje žádný způsob, jak správce odebrat klíč z uživatelského účtu. Uživatel ho musí odebrat. Pokud dojde ke ztrátě nebo odcizení klíče:
   1. Odebere uživatele ze všech skupin povolených pro ověřování bez hesla.
   1. Ověřte, že jste klíč odebrali jako metodu ověřování.
   1. Vydejte nový klíč. **Nahrazení klíče**: uživatelé můžou současně povolit dva bezpečnostní klíče. Při nahrazování bezpečnostního klíče zajistěte, aby uživatel odebral i nahrazovaný klíč.

### <a name="enable-windows-10-support"></a>Povolit podporu Windows 10

Povolení přihlášení Windows 10 pomocí klíčů zabezpečení FIDO2 vyžaduje povolení funkcí poskytovatele přihlašovacích údajů ve Windows 10. Vyberte jednu z následujících možností:

- [Povolení poskytovatele přihlašovacích údajů v Intune](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - Doporučovaná možnost nasazení Intune.
- [Povolení poskytovatele přihlašovacích údajů pomocí zřizovacího balíčku](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Pokud není nasazení Intune možné, musí správci nasadit balíček do každého počítače, aby se povolily funkce poskytovatele přihlašovacích údajů. Instalaci balíčku lze provést pomocí jedné z následujících možností:
      - Zásady skupiny nebo Configuration Manager
      - Místní instalace na počítači s Windows 10
- [Povolení poskytovatele pověření pomocí Zásady skupiny](howto-authentication-passwordless-security-key-windows.md#enable-with-group-policy)
   - Podporuje se jenom pro zařízení připojená k hybridní službě Azure AD.

#### <a name="enable-on-premises-integration"></a>Povolit místní integraci

Pokud chcete povolit přístup k místním prostředkům, postupujte podle pokynů pro [Povolení nezabezpečeného přihlášení k místním prostředkům (Preview) pomocí klíče zabezpečení bez hesla](howto-authentication-passwordless-security-key-on-premises.md).

> [!IMPORTANT]
> Tyto kroky je potřeba provést taky pro všechna zařízení připojená k hybridní službě Azure AD, která využívají klíče zabezpečení FIDO2 pro přihlášení Windows 10.

### <a name="register-security-keys"></a>Registrovat klíče zabezpečení

Uživatelé musí zaregistrovat svůj bezpečnostní klíč na každé Azure Active Directory připojené počítače s Windows 10.

Další informace najdete v tématu [registrace uživatelů a Správa klíčů zabezpečení FIDO2](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys).


## <a name="plan-auditing-security-and-testing"></a>Plánování auditování, zabezpečení a testování
Základní součástí nasazení je plánování auditování, které splňuje vaše organizace a architektury dodržování předpisů.

### <a name="auditing-passwordless"></a>Auditování v neheslech

Azure AD obsahuje sestavy, které poskytují technickou a obchodní přehledy. Vlastníci vaší firmy a technické aplikace předpokládají vlastnictví a využívání těchto sestav na základě požadavků vaší organizace.

V části metody **ověřování** na portálu Azure Active Directory se můžou správci povolit a spravovat nastavení pro přihlašovací údaje nepřipojeného hesla.

Azure AD přidává záznamy do protokolů auditu v těchto případech:

- Správce provede změny v části metody ověřování.
- Uživatel provede libovolný typ změny přihlašovacích údajů v rámci Azure Active Directory.

Následující tabulka uvádí některé příklady typických scénářů vytváření sestav:

|   | Řízení rizik | Zvýšení produktivity | Zásady správného řízení a dodržování předpisů |
| --- | --- | --- | --- |
| **Typy sestav** | Metody ověřování – uživatelé zaregistrovaní pro kombinovanou registraci zabezpečení | Metody ověřování – uživatelé zaregistrovaní pro oznámení aplikace | Přihlášení: kontrola, kdo přistupuje k tenantovi a jak |
| **Potenciální akce** | Cíloví uživatelé ještě nejsou zaregistrovaní. | Mechanika při přijetí Microsoft Authenticator aplikace nebo klíčů zabezpečení | Odvolání přístupu nebo uplatnění dalších zásad zabezpečení pro správce |

**Azure AD uchovává většinu dat auditování po dobu 30 dnů** a zpřístupňuje data prostřednictvím portálu pro správu Azure nebo rozhraní API, abyste je mohli stáhnout do svých analytických systémů. Pokud budete potřebovat delší dobu uchovávání, exportujte a využívejte protokoly v nástroji SIEM, jako je například [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk nebo sumo Logic. [Přečtěte si další informace o zobrazení sestav přístupu a využití](../reports-monitoring/overview-reports.md).

Uživatelé můžou zaregistrovat a spravovat svoje přihlašovací údaje tak, že přejde na [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . Tento odkaz uživatele přesměruje na prostředí pro správu přihlašovacích údajů koncového uživatele, které bylo povoleno prostřednictvím kombinovaného prostředí pro registraci SSPR/Multi-Factor Authentication. Služba Azure AD zaznamená registraci zařízení zabezpečení FIDO2 a změny metod ověřování pro uživatele.

### <a name="plan-security"></a>Plánování zabezpečení
V rámci tohoto plánu zavedení společnost Microsoft doporučuje, aby u všech privilegovaných účtů správce bylo povoleno ověřování bez hesla.

Když uživatel povolí nebo zakáže účet v bezpečnostním klíči nebo obnoví druhý faktor bezpečnostního klíče na počítačích s Windows 10, přidá se do protokolu zabezpečení položka s následujícími identifikátory událostí: *4670* a *5382*.

### <a name="plan-testing"></a>Plánování testování

V každé fázi nasazení při testování scénářů a přijetí se ujistěte, že výsledky jsou očekávané.

#### <a name="testing-the-microsoft-authenticator-app"></a>Testování aplikace Microsoft Authenticator

Zde jsou ukázkové testovací případy pro ověřování bez hesla pomocí Microsoft Authenticator aplikace:

| Scénář | Očekávané výsledky |
| --- | --- |
| Uživatel může zaregistrovat Microsoft Authenticator aplikaci. | Uživatel může zaregistrovat aplikaci z aka.ms/mysecurityinfo. |
| Uživatel může povolit přihlášení telefonem. | Telefonické přihlášení nakonfigurované pro pracovní účet |
| Uživatel má přístup k aplikaci s přihlášením telefonem. | Uživatel přejde přes tok přihlášení k telefonu a dosáhne aplikace. |
| Test registrace zpětného přihlášení k telefonickému telefonu vypnutím Microsoft Authenticator přihlašování nepodepsaných heslem na obrazovce metody ověřování na portálu Azure Active Directory | Dříve povolení uživatelé nemůžou v Microsoft Authenticator používat přihlášení pomocí hesla. |
| Odebírá se přihlášení telefonem z aplikace Microsoft Authenticator. | Pracovní účet už není na Microsoft Authenticator k dispozici. |

#### <a name="testing-security-keys"></a>Testování klíčů zabezpečení

Zde jsou ukázkové testovací případy pro ověřování bez hesla s bezpečnostními klíči.

**NeFIDOelné přihlášení k Azure Active Directory připojená zařízení s Windows 10**

| Scénář | Očekávané výsledky |
| --- | --- |
| Uživatel může zaregistrovat zařízení FIDO2 (1809). | Uživatel může zaregistrovat zařízení FIDO2 pomocí nastavení > účty > možnosti přihlášení > klíč zabezpečení. |
| Uživatel může resetovat zařízení FIDO2 (1809). | Uživatel může resetovat zařízení FIDO2 pomocí softwaru výrobce. |
| Uživatel se může přihlásit pomocí zařízení FIDO2 (1809). | Uživatel může v přihlašovacím okně Vybrat klíč zabezpečení a úspěšně se přihlásit. |
| Uživatel může zaregistrovat zařízení FIDO2 (1903). | Uživatel může zaregistrovat zařízení FIDO2 v nastavení > účty > možnosti přihlášení > klíč zabezpečení. |
| Uživatel může resetovat zařízení FIDO2 (1903). | Uživatel může resetovat zařízení FIDO2 v nastavení > účty > možnosti přihlášení > klíč zabezpečení. |
| Uživatel se může přihlásit pomocí zařízení FIDO2 (1903). | Uživatel může v přihlašovacím okně Vybrat klíč zabezpečení a úspěšně se přihlásit. |

**Přihlášení FIDO k webovým aplikacím Azure AD s heslem**

| Scénář | Očekávané výsledky |
| --- | --- |
| Uživatel může zaregistrovat zařízení FIDO2 v aka.ms/mysecurityinfo pomocí Microsoft Edge. | Registrace by měla být úspěšná. |
| Uživatel může zaregistrovat zařízení FIDO2 v aka.ms/mysecurityinfo pomocí prohlížeče Firefox. | Registrace by měla být úspěšná. |
| Uživatel se může přihlásit k OneDrivu online pomocí zařízení FIDO2 pomocí Microsoft Edge. | Přihlášení by mělo být úspěšné |
| Uživatel se může přihlásit k OneDrivu online pomocí zařízení FIDO2 pomocí prohlížeče Firefox. | Přihlášení by mělo být úspěšné |
| Test vracení zařízení zpět FIDO2 vypnutím klíčů zabezpečení FIDO2 v okně metoda ověřování na portálu Azure Active Directory | Uživatelům se zobrazí výzva, abyste se přihlásili pomocí svého bezpečnostního klíče. Uživatelé se úspěšně přihlásí a zobrazí se chyba: "vaše firemní zásady vyžadují, abyste se přihlásili pomocí jiné metody." Uživatelé by pak měli být schopni vybrat jinou metodu a úspěšně se přihlásit. Zavřete okno a znovu se přihlaste, abyste ověřili, že se stejná chybová zpráva nezobrazí. |

### <a name="plan-for-rollback"></a>Plánování vrácení zpět

Přestože ověřování bez hesla je zjednodušenou funkcí s minimálním dopadem na koncové uživatele, může být nutné vrátit se zpět.

Vracení zpět vyžaduje, aby se správce přihlásil k portálu Azure Active Directory, vybral požadované metody silného ověřování a změnil možnost povolit na **ne**. Tento proces vypne funkce pro všechny uživatele, které nejsou v heslech.

Uživatelům, kteří už zaregistrovali zařízení zabezpečení FIDO2, se zobrazí výzva k použití zabezpečovacího zařízení při příštím přihlášení a pak se zobrazí následující chyba:

![zvolit jiný způsob, jak se přihlásit](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

## <a name="deploy-and-troubleshoot-passwordless-authentication"></a>Nasazení a řešení potíží s ověřováním heslem

Postupujte podle kroků zarovnaných k vybrané metodě níže.

### <a name="required-administrative-roles"></a>Požadované role pro správu

| Role Azure AD | Popis |
| --- | --- |
| Globální správce|Nejnižší privilegovaná role schopna implementovat kombinované prostředí registrace. |
| Správce ověřování | Nejnižší privilegovaná role schopná implementovat a spravovat metody ověřování. |
| Uživatel | Nejnižší privilegovaná role pro konfiguraci ověřovací aplikace na zařízení nebo registrace zařízení zabezpečení klíče pro web nebo přihlášení Windows 10. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Nasazení telefonického přihlášení pomocí aplikace Microsoft Authenticator

Postupujte podle kroků v článku a [Povolte přihlášení bez hesla pomocí aplikace Microsoft Authenticator](howto-authentication-passwordless-phone.md) , abyste aplikaci Microsoft Authenticator ve vaší organizaci povolili jako metodu ověřování bez hesla.

### <a name="deploy-fido2-security-key-sign-in"></a>Nasadit přihlášení k bezpečnostnímu klíči FIDO2

Postupujte podle kroků v článku a [Povolte přihlášení klíče zabezpečení bez hesla pro službu Azure AD](howto-authentication-passwordless-security-key.md) a povolte klíče zabezpečení FIDO2 jako metody ověřování bez hesla.

### <a name="troubleshoot-phone-sign-in"></a>Řešení potíží s přihlášením telefonem

| Scénář | Řešení |
| --- | --- |
| Uživatel nemůže provést kombinovanou registraci. | Zajistěte, aby byla povolená [Kombinovaná registrace](concept-registration-mfa-sspr-combined.md) . |
| Uživatel nemůže povolit aplikaci ověřovatele přihlášení k telefonu. | Zajistěte, aby byl uživatel v oboru pro nasazení. |
| Uživatel není v oboru pro ověřování bez hesla, ale je dodáván s možností přihlášení bez hesla, které nelze dokončit. | K tomuto scénáři dojde, když uživatel povolil přihlášení do aplikace před vytvořením zásady. <br> *Povolení přihlášení*: přidejte uživatele do oboru uživatelů s povoleným přihlášením bez hesla. <br> *Chcete-li zablokovat přihlášení*: uživatel si z této aplikace odeberou své přihlašovací údaje. |

### <a name="troubleshoot-security-key-sign-in"></a>Řešení potíží s přihlášením ke klíčům zabezpečení

| Scénář | Řešení |
| --- | --- |
| Uživatel nemůže provést kombinovanou registraci. | Zajistěte, aby byla povolená [Kombinovaná registrace](concept-registration-mfa-sspr-combined.md) . |
| Uživatel nemůže do svého [nastavení zabezpečení](https://aka.ms/mysecurityinfo)přidat bezpečnostní klíč. | Zajistěte, aby byly povolené [zabezpečovací klíče](howto-authentication-passwordless-security-key.md) . |
| Uživatel nemůže přidat bezpečnostní klíč v možnostech přihlášení ve Windows 10. | [Ujistěte se, že klíče zabezpečení pro přihlášení Windows](./concept-authentication-passwordless.md) |
| **Chybová zpráva**: zjistili jsme, že tento prohlížeč nebo operační systém nepodporuje bezpečnostní klíče FIDO2. | FIDO2á zařízení zabezpečení bez hesla se dají registrovat jenom v podporovaných prohlížečích (Microsoft Edge, Firefox verze 67) ve Windows 10 verze 1809 nebo novější. |
| **Chybová zpráva**: vaše firemní zásady vyžadují, abyste se přihlásili pomocí jiné metody. | V tenantovi nemusíte mít povolené klíče zabezpečení. |
| Uživatel nemůže spravovat můj klíč zabezpečení ve Windows 10 verze 1809. | Verze 1809 vyžaduje, abyste používali software pro správu klíčů zabezpečení, který poskytuje dodavatel FIDO2 Key. Pro podporu se obraťte na dodavatele. |
| Myslím, že můj klíč zabezpečení FIDO2 může být vadný – jak ho můžu otestovat. | Přejděte na [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/) , zadejte přihlašovací údaje k testovacímu účtu, zapojte podezřelý bezpečnostní klíč, vyberte **+** tlačítko v pravé horní části obrazovky, klikněte na vytvořit a Projděte si proces vytváření. Pokud se tento scénář nepovede, může být zařízení vadné. |

## <a name="next-steps"></a>Další kroky

- [Povolit klíče zabezpečení s nezabezpečenými hesly pro přihlášení pro Azure AD](howto-authentication-passwordless-security-key.md)
- [Povolení přihlášení bez hesla pomocí aplikace Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Další informace o použití metod ověřování & Insights](howto-authentication-methods-usage-insights.md)
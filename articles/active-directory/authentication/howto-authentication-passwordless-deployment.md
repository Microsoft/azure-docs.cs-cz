---
title: Dokončení nasazení ověřování bez hesla pomocí Azure AD
description: Dokončení nasazení ověřování neAzure Active Directoryho hesla
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0eb8398decd1a447d0676195d6369cdc7e791e40
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848489"
---
# <a name="complete-a-passwordless-authentication-deployment"></a>Dokončení nasazení ověřování pomocí hesla

Většina kyberútokům začíná s uživatelským jménem a heslem odcizeným od někoho v organizaci. 

Organizace se pokusí tuto hrozbu zmírnit tím, že si vyžádá, aby uživatelé používali:

- Dlouhá hesla
- Složitá hesla
- Časté změny hesla
- Multi-Factor Authentication (MFA)

Výzkum Microsoftu [ukazuje](https://aka.ms/passwordguidance) , že tato snaha annoy uživatele a provedou náklady na podporu. Další informace najdete v tématu o [nezáleží na vašem PA $ $Word](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

Nasazení ověřování neheslem přináší následující výhody:

- Zvýšené zabezpečení. Odebráním hesel jako prostoru pro útoky snižte riziko útoků phishing a postřik hesla.
- Lepší uživatelské prostředí. Poskytněte uživatelům pohodlný způsob, jak získat přístup k datům odkudkoli a zajistit vám i mobilní aplikace pro Outlook, OneDrive, Office a další.
- Robustní přehledy. Robustní protokolování a auditování vám umožní získat přehled o aktivitě bez hesla uživatelů.

Heslo je nahrazeno něčím, co jste vy a něco něco znáte. Například ve Windows Hello pro firmy je biometrická gesta, jako je například obličej nebo otisk prstu nebo PIN kód pro konkrétní zařízení, který není přenášen přes síť.

Microsoft nabízí tři možnosti ověřování s nehesly, které pokrývají mnoho scénářů. Tyto metody lze použít společně. 

- [Windows Hello pro firmy](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) je nejvhodnější pro uživatele na svých vyhrazených počítačích s Windows.
- Klíč zabezpečení přihlašování pomocí [klíčů zabezpečení FIDO2](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) je zvláště užitečný pro uživatele, kteří se přihlásí ke sdíleným počítačům, jako jsou veřejné terminály, situace, kdy je používání telefonů omezené a pro vysoce privilegované identity.
- Telefonický zápis pomocí [Microsoft Authenticator aplikace](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) je užitečný pro poskytování možnosti bez hesla uživatelům s mobilními zařízeními. Přepíná telefon s iOS nebo Androidem do silných přihlašovacích údajů bez hesla tím, že se uživatelům umožní přihlásit se k libovolné platformě nebo prohlížeči. Uživatelé se přihlásí pomocí oznámení na telefon, odpovídající číslo zobrazené na obrazovce na telefonu a následným použitím jejich biometrických dat nebo PIN kódu k potvrzení.

## <a name="compare-passwordless-authentication-methods"></a>Porovnání metod ověřování s nehesly

Metody ověřování s neheslem od Microsoftu umožňují různé scénáře. Pokud chcete vybrat strategii ověřování bez hesla, vezměte v úvahu potřeby vaší organizace, požadavky a možnosti jednotlivých metod ověřování. Doporučujeme, aby všechny organizace, které používají zařízení s Windows 10, používaly Windows Hello pro firmy. Pak přidejte buď telefonické přihlášení (s aplikací Microsoft Authenticator), nebo bezpečnostní klíče pro další scénáře.

### <a name="passwordless-authentication-scenarios"></a>Scénáře ověřování s nehesly

| Scénář | Ověřování pro telefon | Klíče zabezpečení | Windows Hello pro firmy |
| --- | --- | --- | --- |
| **Přihlášení počítače**: <br> Z přiřazeného zařízení s Windows 10 | **Ne** | **Ano** <br> S biometrikou, připnout | **Ano**<br>pomocí biometrického rozpoznávání a kódu PIN |
| **Přihlášení počítače**: <br> Ze sdíleného zařízení s Windows 10 | **Ne** | **Ano** <br> S biometrikou, připnout  | **Ne** |
| **Přihlášení k webové aplikaci**: <br>z počítače vyhrazeného uživatelem | **Ano** | **Ano** <br> Zadané jednotné přihlašování k aplikacím je povolené při přihlášení počítače. | **Ano**<br> Zadané jednotné přihlašování k aplikacím je povolené při přihlášení počítače. |
| **Přihlášení k webové aplikaci**: <br> z mobilního zařízení nebo z jiného zařízení než Windows | **Ano** | **Ne** | **Ne** |
| **Přihlášení počítače**: <br> Počítač s jiným systémem než Windows | **Ne** | **Ne** | **Ne** |

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Technické požadavky pro aplikaci Microsoft Authenticator

**AD FS Integration** – Pokud uživatel povolí Microsoft Authenticator přihlašovací údaje bez hesla, ověřování pro tohoto uživatele standardně posílá oznámení ke schválení. Uživatelům v hybridním tenantovi se zabrání v přesměrování na službu AD FS, aby se přihlásili, pokud nevyberou místo toho použít heslo. Tento proces také obchází všechny místní zásady podmíněného přístupu a předávací ověřovací toky. Pokud je ale zadaný login_hint, uživatel se přepošle na ADFS a obejít možnost používat přihlašovací údaje bez hesla.

**Azure MFA Server** – koncoví uživatelé, kteří mají povolený vícefaktorové ověřování prostřednictvím místního Azure MFA serveru organizace, můžou pořád vytvářet a používat přihlašovací údaje pro přihlášení bez hesla. Pokud se uživatel pokusí upgradovat více instalací (5 +) Microsoft Authenticator s přihlašovacími údaji, může tato změna způsobit chybu.

**Registrace zařízení** : Pokud chcete používat ověřovací aplikaci pro ověřování bez hesla, musí být zařízení zaregistrované v TENANTOVI Azure AD a nemůže být sdíleným zařízením. Zařízení se dá zaregistrovat jenom v jednom tenantovi. Tento limit znamená, že pro přihlášení telefonem pomocí ověřovací aplikace se podporuje jenom jeden pracovní nebo školní účet.

## <a name="prerequisites"></a>Předpoklady

Organizace musí před zahájením nasazení s neplatným heslem splňovat následující požadavky.

| Požadavek | Ověřovací aplikace | FIDO2 klíče zabezpečení |
| --- | --- | --- |
| Je povolená [Kombinovaná registrace pro Azure MFA a Samoobslužné resetování hesla (SSPR)](howto-registration-mfa-sspr-combined.md) . (funkce Preview) | √ | √ |
| [Uživatelé můžou provádět Azure MFA](howto-mfa-getstarted.md) | √ | √ |
| [Uživatelé zaregistrovali pro Azure MFA a SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [Uživatelé zaregistrovali svoje mobilní zařízení Azure Active Directory](../devices/overview.md) | √ |   |
| Windows 10 verze 1809 nebo vyšší s použitím podporovaného prohlížeče, jako je Microsoft Edge nebo Mozilla Firefox <br> (verze 67 nebo vyšší). <br> *Společnost Microsoft doporučuje pro nativní podporu verzi 1903 nebo vyšší*. |   | √ |
| Kompatibilní klíče zabezpečení FIDO2 Ujistěte se, že používáte zařízení se zabezpečením [Microsoft testováno a ověřené](howto-authentication-passwordless-enable.md) FIDO2 nebo jiné kompatibilní zařízení zabezpečení FIDO2. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Předpoklady pro Windows Hello pro firmy

Požadavky na Windows Hello jsou vysoce závislé na tom, jestli nasazujete v konfiguraci místních, hybridních nebo cloudových konfigurací. Další informace najdete v [úplném seznamu požadavků pro Windows Hello pro firmy](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification).

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Uživatelé registrují svou metodu nejenom hesla jako součást toku registrace Azure MFA. Vícefaktorové ověřování s uživatelským jménem a heslem společně s jinou registrovanou metodou se dá použít jako záložní pro případ, že v některých scénářích nemůžou použít svůj telefon nebo bezpečnostní klíč.

### <a name="security-key-lifecycle"></a>Životní cyklus bezpečnostního klíče

Klíče zabezpečení umožňují přístup k vašim prostředkům a měli byste naplánovat správu těchto fyzických zařízení.

1. Distribuce klíčů: Naplánujte způsob zřízení klíčů vaší organizaci. Je možné, že máte centralizovaný proces zřizování nebo pokud chcete, aby koncoví uživatelé koupili klíče kompatibilní s FIDO 2,0.
1. Aktivace klíče: koncoví uživatelé musí klíč zabezpečení sami aktivovat. Koncoví uživatelé zaregistrují své bezpečnostní klíče na [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) a při prvním použití povolí druhý faktor (PIN nebo biometrické).
1. Zakázání klíče: i když je funkce klíče zabezpečení ve fázi Preview, neexistuje žádný způsob, jak správce odebrat klíč z uživatelského účtu. Uživatel ho musí odebrat. Pokud dojde ke ztrátě nebo odcizení klíče:
   1. Odebere uživatele ze všech skupin povolených pro ověřování bez hesla.
   1. Ověřte, že jste klíč odebrali jako metodu ověřování.
   1. Vydejte nový klíč.
1. Nahrazení klíče: uživatelé můžou současně povolit dva bezpečnostní klíče. Při nahrazování bezpečnostního klíče zajistěte, aby uživatel odebral i nahrazovaný klíč.

### <a name="enable-windows-10-support"></a>Povolit podporu Windows 10

Povolení přihlášení Windows 10 pomocí klíčů zabezpečení FIDO2 vyžaduje povolení funkcí poskytovatele přihlašovacích údajů ve Windows 10. Povolte ji jedním ze dvou způsobů:

- [Povolení poskytovatele přihlašovacích údajů v Intune](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - Doporučovaná možnost nasazení Intune.
- [Povolení poskytovatele přihlašovacích údajů pomocí zřizovacího balíčku](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Pokud není nasazení Intune možné, musí správci nasadit balíček do každého počítače, aby se povolily funkce poskytovatele přihlašovacích údajů. Instalaci balíčku lze provést pomocí jedné z následujících možností:
      - Zásady skupiny nebo System Center Configuration Manager (SCCM)
      - Místní instalace na počítači s Windows 10

### <a name="register-security-keys"></a>Registrovat klíče zabezpečení

Uživatelé musí zaregistrovat svůj bezpečnostní klíč na každé Azure Active Directory připojené počítače s Windows 10.

Další informace najdete v tématu [registrace uživatelů a Správa klíčů zabezpečení FIDO2](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys).

### <a name="licensing-for-passwordless-authentication"></a>Licencování pro ověřování neheslem

Pro ověřování bez hesla se neúčtují žádné další poplatky, i když některé předpoklady můžou vyžadovat předplatné Premium. Podrobné informace o funkcích a licencích najdete na [stránce Azure Active Directory licencování](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="develop-a-plan"></a>Vývoj plánu

Vezměte v úvahu své obchodní potřeby a případy použití pro jednotlivé metody ověřování. Pak vyberte metodu, která nejlépe vyhovuje vašim potřebám.

### <a name="use-cases"></a>Případy použití

Následující tabulka popisuje případy použití, které mají být implementovány během tohoto projektu.

| Oblast | Popis |
| --- | --- |
| **Přístup** | Přihlášení bez hesla je k dispozici v podnikovém nebo osobním zařízení v rámci podnikové sítě nebo mimo ni. |
| **Auditování** | Data o využití jsou k dispozici správcům k zaznamenávání téměř v reálném čase. <br> Data o využití se stáhnou do podnikových systémů aspoň každých 29 dní nebo se použije nástroj SIEM Tool. |
| **Zásady správného řízení** | Je definováno a sledováno životní cyklus přiřazení uživatelů k příslušné metodě ověřování a přidruženým skupinám. |
| **Zabezpečení** | Přístup k příslušné metodě ověřování je řízen pomocí přiřazení uživatelů a skupin. <br> Jenom autorizovaní uživatelé můžou používat přihlašování nejenom heslem. |
| **Výkon** | Časové osy šíření přístupu k přiřazení jsou zdokumentovány a monitorovány. <br> Časy přihlášení se měří pro snadné použití. |
| **Činnost koncového uživatele** | Uživatelé mají informace o kompatibilitě mobilních zařízení. <br> Uživatelé můžou nakonfigurovat přihlášení k neheslům aplikace ověřovatel. |
| **Podpora** | Uživatelé si vědomi, jak najít podporu pro problémy s přihlašováním bez hesla. |

### <a name="engage-the-right-stakeholders"></a>Zapojení správných zúčastněných stran

Když projekty technologie selžou, je obvykle z důvodu neshodných očekávání na dopad, výsledky a odpovědnosti. Chcete-li se těmto nástrah vyhnout, [Ujistěte se, že jste připravují správné zúčastněné strany](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) a že role účastníků v projektu jsou dobře pochopitelné.

### <a name="organization-communications"></a>Komunikace organizace

Komunikace je zásadní pro úspěch jakékoli nové služby. Proaktivně komunikuje s tím, jak se budou uživatelé měnit, když se změní, a jak získat podporu, pokud budou mít problémy.

Vaše sdělení koncovým uživatelům budou muset zahrnovat:

- [Povolení kombinovaného prostředí pro registraci zabezpečení](howto-authentication-passwordless-phone.md)
- [Stahuje se aplikace Microsoft Authenticator.](../user-help/user-help-auth-app-download-install.md)
- [Registrace v aplikaci Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Přihlášení pomocí telefonu](../user-help/user-help-auth-app-sign-in.md)

Společnost Microsoft poskytuje [šablony](https://aka.ms/mfatemplates)komunikace MFA, [šablony komunikace](https://www.microsoft.com/download/details.aspx?id=56768)samoobslužného resetování hesla (SSPR) a [dokumentaci pro koncové uživatele](../user-help/security-info-setup-signin.md) , které vám pomůžou s konceptem komunikace. Uživatelům můžete odeslat [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) k registraci přímo tak, že vyberete odkazy na informace o zabezpečení na této stránce.

### <a name="testing-passwordless"></a>Testování bezheslem

V každé fázi nasazení se ujistěte, že testujete, že výsledky jsou očekávané.

#### <a name="testing-the-microsoft-authenticator-app"></a>Testování aplikace Microsoft Authenticator

Zde jsou ukázkové testovací případy pro ověřování bez hesla pomocí aplikace Microsoft Authenticator.

| Scénář | Očekávané výsledky |
| --- | --- |
| Uživatel může zaregistrovat Microsoft Authenticator aplikaci. | Uživatel může zaregistrovat aplikaci z aka.ms/mysecurityinfo. |
| Uživatel může povolit přihlášení telefonem. | Telefonické přihlášení nakonfigurované pro pracovní účet |
| Uživatel má přístup k aplikaci s přihlašováním telefonem. | Uživatel přejde do toku telefonického přihlášení a dosáhne určené aplikace. |
| Test registrace zpětného volání při registraci vypnutím Microsoft Authenticator přihlašování se neheslem na obrazovce metody ověřování na portálu Azure Active Directory | Dříve povolení uživatelé nemůžou v Microsoft Authenticator používat přihlašování pomocí hesla. |
| Odebírání telefonického přihlášení z aplikace Microsoft Authenticator | Pracovní účet už není na Microsoft Authenticator k dispozici. |

#### <a name="testing-security-keys"></a>Testování klíčů zabezpečení

Zde jsou ukázkové testovací případy pro ověřování bez hesla s bezpečnostními klíči.

**FIDO přihlášení k zařízení s Windows 10 Azure Active Directory s přihlašováním k heslům**

| Scénář | Očekávané výsledky |
| --- | --- |
| Uživatel může zaregistrovat zařízení FIDO2 (1809). | Uživatel může zaregistrovat zařízení FIDO2 pomocí nastavení > účty > Možnosti přihlášení > klíč zabezpečení. |
| Uživatel může resetovat zařízení FIDO2 (1809). | Uživatel může resetovat zařízení FIDO2 pomocí softwaru výrobce. |
| Uživatel se může přihlásit pomocí zařízení FIDO2 (1809). | Uživatel může vybrat klíč zabezpečení z okna přihlášení a úspěšně se přihlásit. |
| Uživatel může zaregistrovat zařízení FIDO2 (1903). | Uživatel může zaregistrovat zařízení FIDO2 v nastavení > účty > Možnosti přihlášení > klíč zabezpečení. |
| Uživatel může resetovat zařízení FIDO2 (1903). | Uživatel může resetovat zařízení FIDO2 v nastavení > účty > Možnosti přihlášení > klíč zabezpečení. |
| Uživatel se může přihlásit pomocí zařízení FIDO2 (1903). | Uživatel může vybrat klíč zabezpečení z okna přihlášení a úspěšně se přihlásit. |

**Přihlášení FIDO k webovým aplikacím Azure AD s heslem**

| Scénář | Očekávané výsledky |
| --- | --- |
| Uživatel může zaregistrovat zařízení FIDO2 v aka.ms/mysecurityinfo pomocí Microsoft Edge. | Registrace by měla být úspěšná. |
| Uživatel může zaregistrovat zařízení FIDO2 v aka.ms/mysecurityinfo pomocí prohlížeče Firefox. | Registrace by měla být úspěšná. |
| Uživatel se může přihlásit k OneDrivu online pomocí zařízení FIDO2 pomocí Microsoft Edge. | Přihlášení by mělo být úspěšné |
| Uživatel se může přihlásit k OneDrivu online pomocí zařízení FIDO2 pomocí prohlížeče Firefox. | Přihlášení by mělo být úspěšné |
| Vyzkoušejte FIDO2 registraci zařízení vypnutím klíčů zabezpečení FIDO2 v okně metody ověřování na portálu Azure Active Directory. | Uživatelé budou vyzváni, abyste se přihlásili pomocí svého bezpečnostního klíče, úspěšně se k němu přihlásí a zobrazí se chyba: "vaše firemní zásady vyžadují, abyste se přihlásili pomocí jiné metody." Uživatelé by pak měli být schopni vybrat jinou metodu a úspěšně se přihlásit. Zavřete okno a znovu se přihlaste, abyste ověřili, že se stejná chybová zpráva nezobrazí. |

### <a name="auditing-passwordless"></a>Auditování v neheslech

Azure AD obsahuje sestavy, které poskytují technickou a obchodní přehledy. Vlastníci vaší firmy a technické aplikace předpokládají vlastnictví a využívání těchto sestav na základě požadavků vaší organizace.

V části metody ověřování na portálu Azure Active Directory se můžou správci povolit a spravovat nastavení pro přihlašovací údaje nepřipojeného hesla.

Azure AD přidává záznamy do protokolů auditu v těchto případech:

- Správce provede změny v části metody ověřování.
- Uživatel provede libovolný typ změny přihlašovacích údajů v rámci Azure Active Directory.

Následující tabulka uvádí některé příklady typických scénářů generování sestav.

|   | Řízení rizik | Zvýšení produktivity | Zásady správného řízení a dodržování předpisů |
| --- | --- | --- | --- |
| **Typy sestav** | Metody ověřování – uživatelé zaregistrovaní pro kombinovanou registraci zabezpečení | Metody ověřování – uživatelé zaregistrovaní pro oznámení aplikace | Přihlášení: kontrola, kdo přistupuje k tenantovi a jak |
| **Potenciální akce** | Cíloví uživatelé ještě nejsou zaregistrovaní. | Mechanika při přijetí Microsoft Authenticator aplikace nebo klíčů zabezpečení | Odvolání přístupu nebo uplatnění dalších zásad zabezpečení pro správce |

**Azure AD uchovává většinu dat auditování po dobu 30 dnů** a zpřístupňuje data prostřednictvím portálu pro správu Azure nebo rozhraní API, abyste je mohli stáhnout do svých analytických systémů. Pokud vaše organizace potřebuje delší dobu uchovávání, protokoly je potřeba exportovat a spotřebovat do nástroje SIEM, jako je [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk nebo sumo Logic. [Přečtěte si další informace o zobrazení sestav přístupu a využití](../reports-monitoring/overview-reports.md).

Uživatelé můžou zaregistrovat a spravovat svoje přihlašovací údaje tak, že přejde na [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). Tento odkaz uživatele přesměruje na prostředí pro správu přihlašovacích údajů koncového uživatele, které bylo povoleno prostřednictvím kombinovaného prostředí pro registraci SSPR/MFA. Všechna registrace zařízení zabezpečení FIDO2 nebo změny metod ověřování uživatelem jsou protokolovány v protokolech auditu Azure Active Directory.

Když uživatel povolí nebo zakáže účet v bezpečnostním klíči nebo obnoví druhý faktor bezpečnostního klíče na počítačích s Windows 10, přidá se do protokolu zabezpečení položka s následujícími ID událostí: 4670, 5382.

### <a name="plan-for-rollback"></a>Plánování vrácení zpět

Přestože ověřování bez hesla je zjednodušenou funkcí s minimálním dopadem na koncové uživatele, může být nutné vrátit se zpět.

Vracení zpět vyžaduje, aby se správce přihlásil k portálu Azure Active Directory, vybral požadované metody silného ověřování a změnil možnost povolit na ne. Tento proces vypne funkce bezheslem pro všechny uživatele.

Uživatelé, kteří už zaregistrovali zařízení zabezpečení FIDO2, budou při příštím přihlášení vyzváni k použití zabezpečovacího zařízení a zobrazí se tato chyba:

![zvolit jiný způsob, jak se přihlásit](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

### <a name="plan-to-pilot"></a>Plánování pilotního nasazení

Při nasazení ověřování bez hesla byste měli nejdřív povolit jednu nebo více pilotních skupin. Skupiny můžete [vytvořit](../fundamentals/active-directory-groups-create-azure-portal.md) speciálně pro tento účel. Přidejte uživatele, kteří se budou součástí pilotního projektu, do skupin. Potom povolte nové metody ověřování bez hesla pro vybrané skupiny.

Skupiny je možné synchronizovat z místního adresáře nebo z Azure AD. Jakmile budete spokojeni s výsledky svého pilotního projektu, můžete pro všechny uživatele přepnout na ověřování bez hesla.

[Doporučené postupy pro pilotní](https://aka.ms/deploymentplans) nasazení najdete na stránce plány nasazení.

## <a name="deploy-passwordless-authentication"></a>Nasazení ověřování neheslem

Postupujte podle kroků zarovnaných k vybrané metodě níže.

### <a name="required-administrative-roles"></a>Požadované role pro správu

| Role Azure AD | Popis |
| --- | --- |
| Správce ověřování | Nejnižší privilegovaná role schopná implementovat a spravovat metody ověřování |
| Uživatel | Nejnižší privilegovaná role pro konfiguraci ověřovací aplikace na zařízení nebo registrace zařízení zabezpečení klíčů pro web nebo Windows 10. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Nasazení telefonu přihlášení pomocí aplikace Microsoft Authenticator

Postupujte podle kroků v článku a [Povolte přihlášení bez hesla pomocí aplikace Microsoft Authenticator](howto-authentication-passwordless-phone.md) , abyste aplikaci Microsoft Authenticator ve vaší organizaci povolili jako metodu ověřování bez hesla.

### <a name="deploy-fido2-security-key-sign-in"></a>Nasadit FIDO2 bezpečnostní klíč – přihlášení

Postupujte podle kroků v článku a [Povolte přihlášení klíče zabezpečení bez hesla pro službu Azure AD](howto-authentication-passwordless-security-key.md) a povolte klíče zabezpečení FIDO2 jako metody ověřování bez hesla ve vaší organizaci.

### <a name="troubleshoot-phone-sign-in"></a>Řešení potíží s přihlášením telefonem

| Scénář | Řešení |
| --- | --- |
| Uživatel nemůže provést kombinovanou registraci. | Zajistěte, aby byla povolená [Kombinovaná registrace](concept-registration-mfa-sspr-combined.md) . |
| Uživatel nemůže povolit aplikaci ověřovatele telefonického přihlášení. | Zajistěte, aby byl uživatel v oboru pro nasazení. |
| Uživatel není v oboru pro ověřování bez hesla, ale je nabízený s možností přihlášení bez hesla, kterou nelze dokončit. | K tomuto scénáři dochází, když uživatel povolil přihlášení uživatele v n aplikaci před vytvořením zásady. <br> Povolení přihlášení: přidejte uživatele do oboru uživatelů s povoleným přihlášením bez hesla. <br> Chcete-li zablokovat přihlášení: uživatel musí z aplikace odebrat své přihlašovací údaje. |

### <a name="troubleshoot-security-key-sign-in"></a>Řešení potíží s přihlášením ke klíčům zabezpečení

| Scénář | Řešení |
| --- | --- |
| Uživatel nemůže provést kombinovanou registraci. | Zajistěte, aby byla povolená [Kombinovaná registrace](concept-registration-mfa-sspr-combined.md) . |
| Uživatel nemůže do svého [nastavení zabezpečení](https://aka.ms/mysecurityinfo) přidat bezpečnostní klíč. | Zajistěte, aby byly povolené [zabezpečovací klíče](howto-authentication-passwordless-security-key.md) . |
| Uživatel nemůže přidat bezpečnostní klíč v možnostech přihlášení ve Windows 10. | [Ujistěte se, že klíče zabezpečení pro přihlášení Windows](howto-authentication-passwordless-enable.md) |
| **Chybová zpráva**: zjistili jsme, že tento prohlížeč nebo operační systém nepodporuje klíče zabezpečení FIDO2. | FIDO2á zařízení zabezpečení bez hesla se dají registrovat jenom v podporovaných prohlížečích (Microsoft Edge, Firefox verze 67) ve Windows 10 verze 1809 nebo novější. |
| **Chybová zpráva**: vaše firemní zásady vyžadují, abyste se přihlásili pomocí jiné metody. | V tenantovi nemusíte mít povolené klíče zabezpečení. |
| Uživatel nemůže spravovat můj klíč zabezpečení ve Windows 10 verze 1809. | Verze 1809 vyžaduje, abyste používali software pro správu klíčů zabezpečení, který poskytuje dodavatel FIDO2 Key. Pro podporu se obraťte na dodavatele. |
| Myslím, že můj klíč zabezpečení FIDO2 může být vadný – jak ho můžu otestovat | Přejděte na [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/), zadejte přihlašovací údaje k testovacímu účtu, zapojte podezřelý bezpečnostní klíč, klikněte na tlačítko "+" v pravém horním rohu obrazovky, klikněte na tlačítko vytvořit a Projděte si proces vytváření. Pokud se tento scénář nepovede, může být zařízení vadné. |

## <a name="next-steps"></a>Další kroky

- [Povolit klíče zabezpečení s nezabezpečenými hesly pro přihlášení pro Azure AD](howto-authentication-passwordless-security-key.md)
- [Povolení přihlášení bez hesla pomocí aplikace Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Další informace o použití metod ověřování & Insights](howto-authentication-methods-usage-insights.md)

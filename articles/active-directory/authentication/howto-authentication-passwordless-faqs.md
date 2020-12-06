---
title: Nejčastější dotazy k nasazení FIDO2 zabezpečení klíčů pro hybridní nasazení – Azure Active Directory
description: Přečtěte si o některých nejčastějších dotazech pro přihlášení k FIDO2 bezpečnostnímu klíči s heslem a pomocí Azure Active Directory (Preview).
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/19/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98cb990ede7c4d6e261bba05b0b8c97d758e6c32
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743526"
---
# <a name="deployment-frequently-asked-questions-faqs-for-hybrid-fido2-security-keys-in-azure-ad-preview"></a>Nejčastější dotazy k nasazení (FAQ) pro klíče zabezpečení Hybrid FIDO2 ve službě Azure AD (Preview)

Tento článek obsahuje informace o nejčastějších dotazech k nasazení pro zařízení připojená k hybridní službě Azure AD a k tomu, aby se přihlásily k prostředkům Prem. Pomocí této funkce bez hesla můžete povolit ověřování Azure AD na zařízeních s Windows 10 pro zařízení připojená k hybridní službě Azure AD pomocí klíčů zabezpečení FIDO2. Uživatelé se můžou na svých zařízeních přihlašovat pomocí moderních přihlašovacích údajů, jako jsou FIDO2 klíče, a přistupovat k tradičním prostředkům založeném na Active Directory Domain Services (služba AD DS) s bezproblémové jednotné přihlašování (SSO) k Prem prostředkům.

Podporovány jsou následující scénáře pro uživatele v hybridním prostředí:

* Přihlaste se k hybridnímu zařízení připojenému k Azure AD pomocí klíčů zabezpečení FIDO2 a získejte přístup k přihlašování k prostředkům na Prem.
* Přihlaste se k zařízením připojeným k Azure AD pomocí klíčů zabezpečení FIDO2 a získejte přístup k přihlašování k prostředkům na Prem.

Pokud chcete začít s klíči zabezpečení FIDO2 a s hybridním přístupem k místním prostředkům, přečtěte si následující články:

* [Klíče zabezpečení FIDO2 bez hesla](howto-authentication-passwordless-security-key.md)
* [Windows 10 bez hesla](howto-authentication-passwordless-security-key-windows.md)
* [Místní prostředí bez hesla](howto-authentication-passwordless-security-key-on-premises.md)

> [!NOTE]
> Bezpečnostní klíče FIDO2 jsou funkcí veřejné verze Preview Azure Active Directory. Další informace o verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

## <a name="security-keys"></a>Klíče zabezpečení

* [Moje organizace vyžaduje pro přístup k prostředkům dva faktory ověřování. Co můžu na podporu tohoto požadavku udělat?](#my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement)
* [Kde najdu FIDO2 klíče zabezpečení vyhovující předpisům?](#where-can-i-find-compliant-fido2-security-keys)
* [Jak mám postupovat, když ztratím svůj bezpečnostní klíč?](#what-if-i-lose-my-security-key)
* [Jak jsou data chráněná na FIDO2 bezpečnostní klíč?](#how-is-the-data-protected-on-the-fido2-security-key)
* [Jak funguje registrace klíčů zabezpečení FIDO2?](#how-does-the-registering-of-fido2-security-keys-work)
* [Existuje způsob, jak můžou správci zřídit klíče pro uživatele přímo?](#is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly)

### <a name="my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Moje organizace vyžaduje k přístupu k prostředkům službu Multi-Factor Authentication. Co můžu na podporu tohoto požadavku udělat?

Bezpečnostní klíče FIDO2 se přidávají v nejrůznějších faktorech formy. Obraťte se na výrobce zařízení, aby pomohly diskutovat, jak je možné jejich zařízení povolit pomocí PIN kódu nebo biometriky jako druhý faktor. Seznam podporovaných zprostředkovatelů naleznete v tématu [FIDO2 Security Keys Providers](concept-authentication-passwordless.md#fido2-security-key-providers).

### <a name="where-can-i-find-compliant-fido2-security-keys"></a>Kde najdu FIDO2 klíče zabezpečení vyhovující předpisům?

Seznam podporovaných zprostředkovatelů naleznete v tématu [FIDO2 Security Keys Providers](concept-authentication-passwordless.md#fido2-security-key-providers).

### <a name="what-if-i-lose-my-security-key"></a>Co když ztratím svůj bezpečnostní klíč?

Klíče v Azure Portal můžete odebrat tak, že přejdete na stránku **informace o zabezpečení** a odeberete klíč zabezpečení FIDO2.

### <a name="how-is-the-data-protected-on-the-fido2-security-key"></a>Jak jsou data chráněná na FIDO2 bezpečnostní klíč?

Klíče zabezpečení FIDO2 mají zabezpečený enclaves, které chrání privátní klíče uložené v nich. Bezpečnostní klíč FIDO2 obsahuje také vlastnosti boje proti kladivům, například ve Windows Hello, kde nelze extrahovat privátní klíč.

### <a name="how-does-the-registering-of-fido2-security-keys-work"></a>Jak funguje registrace klíčů zabezpečení FIDO2?

Další informace o registraci a používání klíčů zabezpečení FIDO2 najdete v tématu [Povolení přihlášení k bezpečnostnímu klíči bez hesla](howto-authentication-passwordless-security-key.md).

### <a name="is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly"></a>Existuje způsob, jak můžou správci zřídit klíče pro uživatele přímo?

Ne, v tuto chvíli ne.

## <a name="prerequisites"></a>Předpoklady

* [Funguje tato funkce, pokud není k dispozici připojení k Internetu?](#does-this-feature-work-if-theres-no-internet-connectivity)
* [Jaké jsou konkrétní koncové body, které je potřeba otevřít ve službě Azure AD?](#what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad)
* [Návody identifikovat typ připojení k doméně (připojené ke službě Azure AD nebo připojení k hybridní službě Azure AD) pro zařízení s Windows 10?](#how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device)
* [Jaké je doporučení k počtu řadičů domény, které se mají opravit?](#whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched)
* [Můžu poskytovatele přihlašovacích údajů FIDO2 nasadit na místní zařízení?](#can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device)
* [Přihlášení k FIDO2 bezpečnostnímu klíči nefunguje pro správce domény nebo jiné účty s vysokou úrovní oprávnění. Proč?](#fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why)

### <a name="does-this-feature-work-if-theres-no-internet-connectivity"></a>Funguje tato funkce, pokud není k dispozici připojení k Internetu?

K povolení této funkce je nutné, aby připojení k Internetu bylo nezbytné. Když se uživatel poprvé přihlásí pomocí klíčů zabezpečení FIDO2, musí mít připojení k Internetu. Pro následné přihlašovací události by přihlášení do mezipaměti mělo fungovat a umožnit uživateli ověřování bez připojení k Internetu.

Aby se zajistilo konzistentní prostředí, ujistěte se, že zařízení mají přístup k Internetu a řadu pohledů na řadiče domény.

### <a name="what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad"></a>Jaké jsou konkrétní koncové body, které je potřeba otevřít ve službě Azure AD?

Pro registraci a ověřování jsou potřeba tyto koncové body:

* **. microsoftonline.com*
* **. microsoftonline-p.com*
* **. msauth.net*
* **. msauthimages.net*
* **. msecnd.net*
* **. msftauth.net*
* **. msftauthimages.net*
* **. phonefactor.net*
* *enterpriseregistration.windows.net*
* *management.azure.com*
* *policykeyservice.dc.ad.msft.net*
* *secure.aadcdn.microsoftonline-p.com*

Úplný seznam koncových bodů potřebných k používání produktů Microsoft Online Products najdete v tématu [adresy URL a rozsahy IP adres pro Office 365](/microsoft-365/enterprise/urls-and-ip-address-ranges).

### <a name="how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device"></a>Návody identifikovat typ připojení k doméně (připojené ke službě Azure AD nebo připojení k hybridní službě Azure AD) pro zařízení s Windows 10?

Pokud chcete zjistit, jestli má klientské zařízení s Windows 10 správný typ připojení k doméně, použijte následující příkaz:

```console
Dsregcmd/status
```

Následující ukázkový výstup ukazuje, že je zařízení připojené k Azure AD, protože *AzureADJoined* je nastavené na *Ano*:

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: NO
```

Následující ukázkový výstup ukazuje, že zařízení je připojené k hybridní službě Azure AD, protože *DomainedJoined* je také nastaveno na *Ano*. Zobrazuje se také *doména domainname* :

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: YES
DomainName: CONTOSO
```

Na řadiči domény se systémem Windows Server 2016 nebo 2019 ověřte, zda jsou aplikovány následující opravy. V případě potřeby je nainstalujte spuštěním web Windows Update:

* Windows Server 2016 – [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019 – [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

V klientském zařízení spusťte následující příkaz, který ověří připojení k příslušnému řadiči domény s nainstalovanými opravami:

```console
nltest /dsgetdc:<domain> /keylist /kdc
```

### <a name="whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched"></a>Jaké je doporučení k počtu řadičů domény, které se mají opravit?

Doporučujeme, abyste pomocí opravy použili většinu řadičů domény s Windows serverem 2016 nebo 2019 a zajistili tak, že budou moct zpracovat zátěž žádosti o ověření vaší organizace.

Na řadiči domény se systémem Windows Server 2016 nebo 2019 ověřte, zda jsou aplikovány následující opravy. V případě potřeby je nainstalujte spuštěním web Windows Update:

* Windows Server 2016 – [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019 – [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

### <a name="can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device"></a>Můžu poskytovatele přihlašovacích údajů FIDO2 nasadit na místní zařízení?

Ne, tato funkce se nepodporuje jenom pro místní zařízení. Poskytovatel pověření FIDO2 se nezobrazuje.

### <a name="fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why"></a>Přihlášení k FIDO2 bezpečnostnímu klíči nefunguje pro správce domény nebo jiné účty s vysokou úrovní oprávnění. Proč?

Výchozí zásady zabezpečení neudělují oprávnění Azure AD k podepisování účtů s vysokou úrovní oprávnění k místním prostředkům.

Pokud chcete odblokovat účty, pomocí modulu **Uživatelé a počítače služby Active Directory** upravte vlastnost *msDS-NeverRevealGroup* *objektu počítače Azure AD Kerberos (CN = AzureADKerberos, OU = řadiče domény, \<domain-DN> )*.

## <a name="under-the-hood"></a>Pod pokličkou

* [Jak se Azure AD Kerberos odkazuje na místní Active Directory Domain Services prostředí?](#how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment)
* [Kde můžu zobrazit tyto objekty serveru Kerberos vytvořené ve službě AD a publikovat ve službě Azure AD?](#where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad)
* [Proč není možné veřejný klíč zaregistrovat k místnímu služba AD DS, takže na internetu není žádná závislost?](#why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet)
* [Jak se klíče otáčí v objektu serveru Kerberos?](#how-are-the-keys-rotated-on-the-kerberos-server-object)
* [Proč potřebuji Azure AD Connect? Zapisuje se do služba AD DS z Azure AD nějaké informace zpátky?](#why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad)
* [Jak požadavek a odpověď protokolu HTTP vypadají jako při žádosti o PRT + částečný lístek TGT?](#what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt)

### <a name="how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment"></a>Jak se Azure AD Kerberos odkazuje na místní Active Directory Domain Services prostředí?

K dispozici jsou dvě části – místní služba AD DS prostředí a tenant služby Azure AD.

**Služba Active Directory Domain Services (AD DS)**

Server Kerberos služby Azure AD je reprezentován v místním prostředí služba AD DS jako objekt řadiče domény (DC). Tento objekt DC se skládá z více objektů:

* *CN = AzureADKerberos, OU = řadiče domény,\<domain-DN>*
    
    Objekt *počítače* , který představuje řadič domény Read-Only (RODC) v služba AD DS. K tomuto objektu není přidružen žádný počítač. Místo toho se jedná o logickou reprezentaci řadiče domény.

* *CN = krbtgt_AzureAD, CN = uživatelé,\<domain-DN>*

    Objekt *uživatele* , který reprezentuje šifrovací klíč lístku TGT protokolu Kerberos pro řadič domény.

* *CN = 900274c4-b7d2-43c8-90EE-00a9f650e335, CN = AzureAD, CN = System,\<domain-DN>*

    Objekt *ServiceConnectionPoint* , který ukládá metadata o objektech serveru Azure AD Kerberos. Nástroje pro správu používají tento objekt k identifikaci a vyhledání objektů serveru Kerberos služby Azure AD.

**Azure Active Directory**

Server Kerberos služby Azure AD je reprezentován ve službě Azure AD jako objekt *KerberosDomain* . Každé místní služba AD DS prostředí se v tenantovi Azure AD reprezentuje jako jeden objekt *KerberosDomain* .

Můžete mít například doménovou strukturu služba AD DS se dvěma doménami, například *contoso.com* a *fabrikam.com*. Pokud povolíte, aby služba Azure AD vydávala lístky pro udělování lístků protokolu Kerberos (TGT) pro celou doménovou strukturu, existují dva objekty *KerberosDomain* ve službě Azure AD – jeden objekt pro *contoso.com* a druhý pro *fabrikam.com*.

Pokud máte více služba AD DS doménových struktur, máte jeden objekt *KerberosDomain* pro každou doménu v každé doménové struktuře.

### <a name="where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad"></a>Kde můžu zobrazit tyto objekty serveru Kerberos, které jsou vytvořené v služba AD DS a publikované ve službě Azure AD?

Pokud chcete zobrazit všechny objekty, použijte rutiny prostředí PowerShell serveru Azure AD, které jsou součástí nejnovější verze Azure AD Connect.

Další informace, včetně pokynů k zobrazení objektů, najdete v tématu [vytvoření objektů serveru Kerberos](howto-authentication-passwordless-security-key-on-premises.md#create-kerberos-server-object).

### <a name="why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet"></a>Proč není možné veřejný klíč zaregistrovat k místnímu služba AD DS, takže na internetu není žádná závislost?

Dostali jsme zpětnou vazbu o složitosti modelu nasazení pro Windows Hello pro firmy, takže jsme chtěli zjednodušit model nasazení bez použití certifikátů a infrastruktury veřejných klíčů (FIDO2 nepoužívá certifikáty).

### <a name="how-are-the-keys-rotated-on-the-kerberos-server-object"></a>Jak se klíče otáčí v objektu serveru Kerberos?

Stejně jako u jakýchkoli jiných řadičů domény by se měly pravidelně střídat šifrovací klíče serveru Azure AD Kerberos *KRBTGT* . Doporučuje se postupovat podle stejného plánu, jaký používáte k otočení všech ostatních služba AD DS *KRBTGT* klíče.

> [!NOTE]
> I když jsou k dispozici další nástroje pro otočení klíčů *KRBTGT* , je nutné [použít rutiny prostředí PowerShell k otočení klíčů *KRBTGT*](howto-authentication-passwordless-security-key-on-premises.md#rotating-the-azure-ad-kerberos-server-key) serveru Kerberos služby Azure AD. Tato metoda zajistí, že se klíče aktualizují v místním prostředí služba AD DS i ve službě Azure AD.

### <a name="why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad"></a>Proč potřebuji Azure AD Connect? Zapisuje se do služba AD DS z Azure AD nějaké informace zpátky?

Azure AD Connect nepíše informace zpět z Azure AD do služba AD DS. Nástroj zahrnuje modul PowerShell k vytvoření objektu serveru Kerberos v služba AD DS a jeho publikování v Azure AD.

### <a name="what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt"></a>Jak požadavek a odpověď protokolu HTTP vypadají jako při žádosti o PRT + částečný lístek TGT?

Požadavek HTTP je standardní žádost o primární obnovovací token (PRT). Tato žádost PRT obsahuje deklaraci identity, která značí, že je potřeba lístek TGT protokolu Kerberos.

| Deklarovat | Hodnota | Popis                             |
|-------|-------|-----------------------------------------|
| TGT   | true  | Deklarace identity indikuje, že klient potřebuje lístek TGT. |

Azure AD kombinuje zašifrovaný klíč klienta a vyrovnávací paměť zpráv do odpovědi PRT jako další vlastnosti. Datová část je šifrovaná pomocí klíče relace zařízení Azure AD.

| Pole              | Typ   | Popis  |
|--------------------|--------|--------------|
| tgt_client_key     | řetězec | Klíč klienta kódovaný v kódování Base64 (tajný kód). Tento klíč je tajný kód klienta, který se používá k ochraně lístku TGT. V tomto scénáři bez hesla je tajný klíč klienta vygenerovaný serverem v rámci každé žádosti TGT a pak se vrátí klientovi v odpovědi. |
| tgt_key_type       | int    | Typ místního služba AD DS klíče použitý pro klíč klienta i klíč relace protokolu Kerberos obsažený v KERB_MESSAGE_BUFFER. |
| tgt_message_buffer | řetězec | KERB_MESSAGE_BUFFER kódovaný v kódování Base64. |

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s klíči zabezpečení FIDO2 a s hybridním přístupem k místním prostředkům, přečtěte si následující články:

* [Klíče zabezpečení FIDO2 bez hesla](howto-authentication-passwordless-security-key.md)
* [Windows 10 bez hesla](howto-authentication-passwordless-security-key-windows.md)
* [Místní prostředí bez hesla](howto-authentication-passwordless-security-key-on-premises.md)

---
title: Atributy synchronizované službou Azure AD Connect | Dokumenty společnosti Microsoft
description: Zobrazí seznam atributů, které jsou synchronizovány se službou Azure Active Directory.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c2bb36e0-5205-454c-b9b6-f4990bcedf51
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a82766be01476890bbf18b518ce21febe0d07f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253608"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Synchronizace služby Azure AD Connect: Atributy synchronizované se službou Azure Active Directory
Toto téma uvádí atributy, které jsou synchronizovány pomocí synchronizace Azure AD Connect.  
Atributy jsou seskupeny podle související aplikace Azure AD.

## <a name="attributes-to-synchronize"></a>Atributy k synchronizaci
Obvyklou otázkou *je, co je seznam minimálních atributů pro synchronizaci*. Výchozí a doporučený přístup je zachovat výchozí atributy, takže úplný seznam adres (globální seznam adres) lze sestavit v cloudu a získat všechny funkce v úlohách Office 365. V některých případech existují některé atributy, které vaše organizace nechce synchronizovat do cloudu, protože tyto atributy obsahují citlivá data nebo osobní údaje (osobně identifikovatelné informace), jako v tomto příkladu:  
![chybné atributy](./media/reference-connect-sync-attributes-synchronized/badextensionattribute.png)

V takovém případě začněte se seznamem atributů v tomto tématu a identifikujte atributy, které by obsahovaly citlivá data nebo data pii a nelze je synchronizovat. Pak zrušit výběr těchto atributů během instalace pomocí [aplikace Azure AD a filtrování atributů](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering).

> [!WARNING]
> Při odznačení atributů byste měli být opatrní a pouze odznačte tyto atributy, které není absolutně možné synchronizovat. Zrušení výběru jiných atributů může mít negativní dopad na funkce.
>
>

## <a name="office-365-proplus"></a>Office 365 ProPlus
| Název atributu | Uživatel | Poznámka |
| --- |:---:| --- |
| účetPovolený |× |Definuje, zda je účet povolen. |
| Kn |× | |
| displayName |× | |
| objectSID |× |mechanické vlastnosti. Identifikátor uživatele služby AD, který se používá k udržování synchronizace mezi službou Azure AD a službou AD. |
| pwdLastSet |× |mechanické vlastnosti. Používá se vědět, kdy zrušit platnost již vydané tokeny. Používá se synchronizace hash hesla, předávací ověřování i federace. |
|samAccountName|×| |
| sourceAnchor |× |mechanické vlastnosti. Neměnný identifikátor pro zachování vztahu mezi přidá a Azure AD. |
| usageUmístění |× |mechanické vlastnosti. Země nebo oblast uživatele. Používá se pro přiřazení licence. |
| userPrincipalName (Hlavní název uživatele) |× |HLAVNÍ uživatel je přihlašovací ID uživatele. Nejčastěji stejná jako hodnota [mail]. |

## <a name="exchange-online"></a>Exchange Online
| Název atributu | Uživatel | Kontakt | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| účetPovolený |× | | |Definuje, zda je účet povolen. |
| Asistent |× |× | | |
| altPříjemce |× | | |Vyžaduje Azure AD Connect sestavení 1.1.552.0 nebo po. |
| authOrig |× |× |× | |
| c |× |× | | |
| Kn |× | |× | |
| Co |× |× | | |
| company |× |× | | |
| kód země |× |× | | |
| Oddělení |× |× | | |
| description | | |× | |
| displayName |× |× |× | |
| dLMemRejectPerms |× |× |× | |
| dLMemSubmitPerms |× |× |× | |
| extensionAttribute1 |× |× |× | |
| extensionAttribute10 |× |× |× | |
| extensionAttribute11 |× |× |× | |
| extensionAttribute12 |× |× |× | |
| extensionAttribute13 |× |× |× | |
| extensionAttribute14 |× |× |× | |
| extensionAttribute15 |× |× |× | |
| extensionAttribute2 |× |× |× | |
| extensionAttribute3 |× |× |× | |
| extensionAttribute4 |× |× |× | |
| extensionAttribute5 |× |× |× | |
| extensionAttribute6 |× |× |× | |
| extensionAttribute7 |× |× |× | |
| extensionAttribute8 |× |× |× | |
| extensionAttribute9 |× |× |× | |
| faxčíslo |× |× | | |
| givenName |× |× | | |
| domácí telefon |× |× | | |
| Info |× |× |× |Tento atribut není aktuálně spotřebován pro skupiny. |
| Iniciály |× |× | | |
| l |× |× | | |
| dědictvíExchangeDN |× |× |× | |
| přezdívka |× |× |× | |
| managedBy | | |× | |
| manager |× |× | | |
| člen | | |× | |
| mobil |× |× | | |
| msDS-HABSeniorityIndex |× |× |× | |
| msDS-PhoneticDisplayName |× |× |× | |
| msExchArchiveGUID |× | | | |
| msExchNázev archivu |× | | | |
| msExchAssistantName |× |× | | |
| msExchAuditAdmin |× | | | |
| msExchAuditDelegate |× | | | |
| msExchAuditDelegateAdmin |× | | | |
| msExchAuditOwner |× | | | |
| msExchBlockedSendersHash |× |× | | |
| msExchBypassAudit |× | | | |
| msExchBypassModeonLink | | |× |K dispozici ve verzi Azure AD Connect 1.1.524.0 |
| msExchCoManagedByLink | | |× | |
| msExchDelegateListLink |× | | | |
| msExchELCExpirySuspensionEnd |× | | | |
| msExchELCExpirySuspensionStart |× | | | |
| msExchELCMailboxFlags |× | | | |
| msExchEnableModeon |× | |× | |
| msExchExtensionCustomAttribute1 |× |× |× |Tento atribut není aktuálně spotřebována Exchange Online. |
| msExchExtensionCustomAttribute2 |× |× |× |Tento atribut není aktuálně spotřebována Exchange Online. |
| msExchExtensionCustomAttribute3 |× |× |× |Tento atribut není aktuálně spotřebována Exchange Online. |
| msExchExtensionCustomAttribute4 |× |× |× |Tento atribut není aktuálně spotřebována Exchange Online. |
| msExchExtensionCustomAttribute5 |× |× |× |Tento atribut není aktuálně spotřebována Exchange Online. |
| msExchHideFromAddressLists |× |× |× | |
| msExchImmutableID |× | | | |
| msExchLitigationHoldDate |× |× |× | |
| msExchLitigationHoldOwner |× |× |× | |
| msExchMailboxAuditEnable |× | | | |
| msExchMailboxAuditLogAgeLimit |× | | | |
| msExchMailboxGuid |× | | | |
| msExchModeratedByLink |× |× |× | |
| msExchModeonFlags |× |× |× | |
| msExchRecipientDisplayType |× |× |× | |
| msExchRecipientTypeDetails |× |× |× | |
| msExchRemoteRecipientType |× | | | |
| msExchRequireAuthToSendTo |× |× |× | |
| msExchResourceCapacity |× | | | |
| msExchResourceDisplay |× | | | |
| msExchResourceMetaData |× | | | |
| msExchResourceSearchProperties |× | | | |
| msExchretentionKomentář |× |× |× | |
| msExchRetentionURL |× |× |× | |
| msExchSafePříjemciHash |× |× | | |
| msExchSafeSendersHash |× |× | | |
| msExchSenderHintPřeklady |× |× |× | |
| msExchTeamMailboxExpiration |× | | | |
| msExchTeamMailboxOwners |× | | | |
| msExchTeamMailboxSharePointUrl |× | | | |
| msExchUserHoldPolicies |× | | | |
| msOrg-IsOrganizační | | |× | |
| objectSID |× | |× |mechanické vlastnosti. Identifikátor uživatele služby AD, který se používá k udržování synchronizace mezi službou Azure AD a službou AD. |
| oOFReplyToOriginator | | |× | |
| otherFacsimileTelefon |× |× | | |
| ostatníHomePhone |× |× | | |
| otherTelefon |× |× | | |
| Pager |× |× | | |
| physicalDeliveryOfficeName |× |× | | |
| Postalcode |× |× | | |
| proxyAddresses |× |× |× | |
| publicDelegates |× |× |× | |
| pwdLastSet |× | | |mechanické vlastnosti. Používá se vědět, kdy zrušit platnost již vydané tokeny. Používá se synchronizací hesel i federací. |
| reportToOriginator | | |× | |
| reportToOwner | | |× | |
| sn |× |× | | |
| sourceAnchor |× |× |× |mechanické vlastnosti. Neměnný identifikátor pro zachování vztahu mezi přidá a Azure AD. |
| st |× |× | | |
| Streetaddress |× |× | | |
| Targetaddress |× |× | | |
| phoneAssistant |× |× | | |
| phoneČíslo |× |× | | |
| miniatury fotky |× |× | | |
| title |× |× | | |
| unauthOrig |× |× |× | |
| usageUmístění |× | | |mechanické vlastnosti. Země nebo oblast uživatele. Používá se pro přiřazení licence. |
| userCertificate |× |× | | |
| userPrincipalName (Hlavní název uživatele) |× | | |HLAVNÍ uživatel je přihlašovací ID uživatele. Nejčastěji stejná jako hodnota [mail]. |
| userSMIMECertifikáty |× |× | | |
| wWWHomePage |× |× | | |

## <a name="sharepoint-online"></a>SharePoint Online
| Název atributu | Uživatel | Kontakt | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| účetPovolený |× | | |Definuje, zda je účet povolen. |
| authOrig |× |× |× | |
| c |× |× | | |
| Kn |× | |× | |
| Co |× |× | | |
| company |× |× | | |
| kód země |× |× | | |
| Oddělení |× |× | | |
| description |× |× |× | |
| displayName |× |× |× | |
| dLMemRejectPerms |× |× |× | |
| dLMemSubmitPerms |× |× |× | |
| extensionAttribute1 |× |× |× | |
| extensionAttribute10 |× |× |× | |
| extensionAttribute11 |× |× |× | |
| extensionAttribute12 |× |× |× | |
| extensionAttribute13 |× |× |× | |
| extensionAttribute14 |× |× |× | |
| extensionAttribute15 |× |× |× | |
| extensionAttribute2 |× |× |× | |
| extensionAttribute3 |× |× |× | |
| extensionAttribute4 |× |× |× | |
| extensionAttribute5 |× |× |× | |
| extensionAttribute6 |× |× |× | |
| extensionAttribute7 |× |× |× | |
| extensionAttribute8 |× |× |× | |
| extensionAttribute9 |× |× |× | |
| faxčíslo |× |× | | |
| givenName |× |× | | |
| hideDLČlenství | | |× | |
| domácí telefon |× |× | | |
| Info |× |× |× | |
| Iniciály |× |× | | |
| ipPhone |× |× | | |
| l |× |× | | |
| pošta |× |× |× | |
| přezdívka na poště |× |× |× | |
| managedBy | | |× | |
| manager |× |× | | |
| člen | | |× | |
| Middlename |× |× | | |
| mobil |× |× | | |
| msExchTeamMailboxExpiration |× | | | |
| msExchTeamMailboxOwners |× | | | |
| msExchTeamMailboxSharePointLinkedBy |× | | | |
| msExchTeamMailboxSharePointUrl |× | | | |
| objectSID |× | |× |mechanické vlastnosti. Identifikátor uživatele služby AD, který se používá k udržování synchronizace mezi službou Azure AD a službou AD. |
| oOFReplyToOriginator | | |× | |
| otherFacsimileTelefon |× |× | | |
| ostatníHomePhone |× |× | | |
| otherIpPhone |× |× | | |
| otherMobile |× |× | | |
| otherPager |× |× | | |
| otherTelefon |× |× | | |
| Pager |× |× | | |
| physicalDeliveryOfficeName |× |× | | |
| Postalcode |× |× | | |
| postOfficeBox |× |× | |Tento atribut momentálně SharePoint Online nespotřebovává. |
| preferredLanguage |× | | | |
| proxyAddresses |× |× |× | |
| pwdLastSet |× | | |mechanické vlastnosti. Používá se vědět, kdy zrušit platnost již vydané tokeny. Používá se synchronizace hash hesla, předávací ověřování i federace. |
| reportToOriginator | | |× | |
| reportToOwner | | |× | |
| sn |× |× | | |
| sourceAnchor |× |× |× |mechanické vlastnosti. Neměnný identifikátor pro zachování vztahu mezi přidá a Azure AD. |
| st |× |× | | |
| Streetaddress |× |× | | |
| Targetaddress |× |× | | |
| phoneAssistant |× |× | | |
| phoneČíslo |× |× | | |
| miniatury fotky |× |× | | |
| title |× |× | | |
| unauthOrig |× |× |× | |
| url |× |× | | |
| usageUmístění |× | | |mechanické vlastnosti. Země nebo oblast uživatele
. Používá se pro přiřazení licence. |
| userPrincipalName (Hlavní název uživatele) |× | | |HLAVNÍ uživatel je přihlašovací ID uživatele. Nejčastěji stejná jako hodnota [mail]. |
| wWWHomePage |× |× | | |

## <a name="teams-and-skype-for-business-online"></a>Týmy a Online Skype pro firmy
| Název atributu | Uživatel | Kontakt | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| účetPovolený |× | | |Definuje, zda je účet povolen. |
| c |× |× | | |
| Kn |× | |× | |
| Co |× |× | | |
| company |× |× | | |
| Oddělení |× |× | | |
| description |× |× |× | |
| displayName |× |× |× | |
| faxčíslo |× |× |× | |
| givenName |× |× | | |
| domácí telefon |× |× | | |
| ipPhone |× |× | | |
| l |× |× | | |
| pošta |× |× |× | |
| přezdívka |× |× |× | |
| managedBy | | |× | |
| manager |× |× | | |
| člen | | |× | |
| mobil |× |× | | |
| msExchHideFromAddressLists |× |× |× | |
| msRTCSIP-ApplicationOptions |× | | | |
| msRTCSIP-DeploymentLocator |× |× | | |
| řada msRTCSIP |× |× | | |
| msRTCSIP-OptionFlags |× |× | | |
| msRTCSIP-OwnerUrn |× | | | |
| adresa msRTCSIP-PrimaryUserAddress |× |× | | |
| msRTCSIP-UserEnabled |× |× | | |
| objectSID |× | |× |mechanické vlastnosti. Identifikátor uživatele služby AD, který se používá k udržování synchronizace mezi službou Azure AD a službou AD. |
| otherTelefon |× |× | | |
| physicalDeliveryOfficeName |× |× | | |
| Postalcode |× |× | | |
| preferredLanguage |× | | | |
| proxyAddresses |× |× |× | |
| pwdLastSet |× | | |mechanické vlastnosti. Používá se vědět, kdy zrušit platnost již vydané tokeny. Používá se synchronizace hash hesla, předávací ověřování i federace. |
| sn |× |× | | |
| sourceAnchor |× |× |× |mechanické vlastnosti. Neměnný identifikátor pro zachování vztahu mezi přidá a Azure AD. |
| st |× |× | | |
| Streetaddress |× |× | | |
| phoneČíslo |× |× | | |
| miniatury fotky |× |× | | |
| title |× |× | | |
| usageUmístění |× | | |mechanické vlastnosti. Země nebo oblast uživatele. Používá se pro přiřazení licence. |
| userPrincipalName (Hlavní název uživatele) |× | | |HLAVNÍ uživatel je přihlašovací ID uživatele. Nejčastěji stejná jako hodnota [mail]. |
| wWWHomePage |× |× | | |

## <a name="azure-rms"></a>Azure RMS
| Název atributu | Uživatel | Kontakt | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| účetPovolený |× | | |Definuje, zda je účet povolen. |
| Kn |× | |× |Běžný název nebo alias. Nejčastěji předpona [mail] value. |
| displayName |× |× |× |Řetězec, který představuje název často zobrazený jako popisný název (křestní jméno příjmení). |
| pošta |× |× |× |e-mailovou adresu. |
| člen | | |× | |
| objectSID |× | |× |mechanické vlastnosti. Identifikátor uživatele služby AD, který se používá k udržování synchronizace mezi službou Azure AD a službou AD. |
| proxyAddresses |× |× |× |mechanické vlastnosti. Používá Azure AD. Obsahuje všechny sekundární e-mailové adresy pro uživatele. |
| pwdLastSet |× | | |mechanické vlastnosti. Používá se vědět, kdy zrušit platnost již vydané tokeny. |
| sourceAnchor |× |× |× |mechanické vlastnosti. Neměnný identifikátor pro zachování vztahu mezi přidá a Azure AD. |
| usageUmístění |× | | |mechanické vlastnosti. Země nebo oblast uživatele. Používá se pro přiřazení licence. |
| userPrincipalName (Hlavní název uživatele) |× | | |Tento hlavní uživatel je přihlašovací ID uživatele. Nejčastěji stejná jako hodnota [mail]. |

## <a name="intune"></a>Intune
| Název atributu | Uživatel | Kontakt | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| účetPovolený |× | | |Definuje, zda je účet povolen. |
| c |× |× | | |
| Kn |× | |× | |
| description |× |× |× | |
| displayName |× |× |× | |
| pošta |× |× |× | |
| přezdívka na poště |× |× |× | |
| člen | | |× | |
| objectSID |× | |× |mechanické vlastnosti. Identifikátor uživatele služby AD, který se používá k udržování synchronizace mezi službou Azure AD a službou AD. |
| proxyAddresses |× |× |× | |
| pwdLastSet |× | | |mechanické vlastnosti. Používá se vědět, kdy zrušit platnost již vydané tokeny. Používá se synchronizace hash hesla, předávací ověřování i federace. |
| sourceAnchor |× |× |× |mechanické vlastnosti. Neměnný identifikátor pro zachování vztahu mezi přidá a Azure AD. |
| usageUmístění |× | | |mechanické vlastnosti. Země nebo oblast uživatele. Používá se pro přiřazení licence. |
| userPrincipalName (Hlavní název uživatele) |× | | |HLAVNÍ uživatel je přihlašovací ID uživatele. Nejčastěji stejná jako hodnota [mail]. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Název atributu | Uživatel | Kontakt | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| účetPovolený |× | | |Definuje, zda je účet povolen. |
| c |× |× | | |
| Kn |× | |× | |
| Co |× |× | | |
| company |× |× | | |
| kód země |× |× | | |
| description |× |× |× | |
| displayName |× |× |× | |
| faxčíslo |× |× | | |
| givenName |× |× | | |
| l |× |× | | |
| managedBy | | |× | |
| manager |× |× | | |
| člen | | |× | |
| mobil |× |× | | |
| objectSID |× | |× |mechanické vlastnosti. Identifikátor uživatele služby AD, který se používá k udržování synchronizace mezi službou Azure AD a službou AD. |
| physicalDeliveryOfficeName |× |× | | |
| Postalcode |× |× | | |
| preferredLanguage |× | | | |
| pwdLastSet |× | | |mechanické vlastnosti. Používá se vědět, kdy zrušit platnost již vydané tokeny. Používá se synchronizace hash hesla, předávací ověřování i federace. |
| sn |× |× | | |
| sourceAnchor |× |× |× |mechanické vlastnosti. Neměnný identifikátor pro zachování vztahu mezi přidá a Azure AD. |
| st |× |× | | |
| Streetaddress |× |× | | |
| phoneČíslo |× |× | | |
| title |× |× | | |
| usageUmístění |× | | |mechanické vlastnosti. Země nebo oblast uživatele. Používá se pro přiřazení licence. |
| userPrincipalName (Hlavní název uživatele) |× | | |HLAVNÍ uživatel je přihlašovací ID uživatele. Nejčastěji stejná jako hodnota [mail]. |

## <a name="3rd-party-applications"></a>Aplikace třetích stran
Tato skupina je sada atributů používaných jako minimální atributy potřebné pro obecné úlohy nebo aplikace. Lze jej použít pro úlohy, které nejsou uvedeny v jiné části nebo pro aplikaci jiného uživatele než Microsoft. Výslovně se používá pro následující:

* Yammer (spotřebovává se jenom uživatel)
* [Hybridní scénáře spolupráce mezi podniky (B2B) nabízené prostředky, jako je SharePoint](https://go.microsoft.com/fwlink/?LinkId=747036)

Tato skupina je sada atributů, které lze použít, pokud adresář Azure AD není použit pro podporu Office 365, Dynamics nebo Intune. Má malou sadu základních atributů.

| Název atributu | Uživatel | Kontakt | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| účetPovolený |× | | |Definuje, zda je účet povolen. |
| Kn |× | |× | |
| displayName |× |× |× | |
| employeeID |× |  |  | |
| givenName |× |× | | |
| pošta |× | |× | |
| managedBy | | |× | |
| mailNickName |× |× |× | |
| člen | | |× | |
| objectSID |× | | |mechanické vlastnosti. Identifikátor uživatele služby AD, který se používá k udržování synchronizace mezi službou Azure AD a službou AD. |
| proxyAddresses |× |× |× | |
| pwdLastSet |× | | |mechanické vlastnosti. Používá se vědět, kdy zrušit platnost již vydané tokeny. Používá se synchronizace hash hesla, předávací ověřování i federace. |
| sn |× |× | | |
| sourceAnchor |× |× |× |mechanické vlastnosti. Neměnný identifikátor pro zachování vztahu mezi přidá a Azure AD. |
| usageUmístění |× | | |mechanické vlastnosti. Země nebo oblast uživatele. Používá se pro přiřazení licence. |
| userPrincipalName (Hlavní název uživatele) |× | | |HLAVNÍ uživatel je přihlašovací ID uživatele. Nejčastěji stejná jako hodnota [mail]. |

## <a name="windows-10"></a>Windows 10
Počítač (zařízení) s doménou Windows 10 synchronizuje některé atributy s Azure AD. Další informace o scénářích najdete v [tématu Připojení zařízení spojených s doménou k prostředí Azure AD pro Windows 10](../active-directory-azureadjoin-devices-group-policy.md). Tyto atributy se vždy synchronizují a Systém Windows 10 se nezobrazí jako aplikace, kterou můžete zrušit. Počítač se systémem Windows 10 je identifikován tak, že je vyplněn atribut userCertificate.

| Název atributu | Zařízení | Poznámka |
| --- |:---:| --- |
| účetPovolený |× | |
| deviceTrustType |× |Pevně zakódovaná hodnota pro počítače spojené s doménou. |
| displayName |× | |
| ms-DS-CreatorSID |× |Také se nazývá registeredOwnerReference. |
| objectGUID |× |Nazývá se také deviceID. |
| objectSID |× |Také se nazývá onPremisesSecurityIdentifier. |
| Operatingsystem |× |Nazývá se také deviceOSType. |
| operatingSystemVersion |× |Nazývá se také deviceOSVersion. |
| userCertificate |× | |

Tyto atributy pro **uživatele** jsou navíc k ostatním aplikacím, které jste vybrali.  

| Název atributu | Uživatel | Poznámka |
| --- |:---:| --- |
| doménaFQDN |× |Nazývá se také dnsDomainName. Například contoso.com. |
| doménaNetBios |× |Také se nazývá netBiosName. Například CONTOSO. |
| msDS-KeyCredentialLink |× |Jakmile je uživatel zaregistrovaný do Windows Hello pro firmy. | 

## <a name="exchange-hybrid-writeback"></a>Exchange hybridní zpětný zápis
Tyto atributy se odepíšou zpět z Azure AD do místní služby Active Directory, když vyberete povolení **hybridního serveru Exchange**. V závislosti na verzi serveru Exchange může být synchronizováno méně atributů.

| Název atributu (místní ad) | Název atributu (uživatelské uuživatelské uživatelské) | Uživatel | Kontakt | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |---|
| id objektu msDS-ExternalDirectory| ms-DS-External-Directory-Id |× | | |Odvozeno z cloudAnchor ve službě Azure AD. Tento atribut je nový ve Exchange 2016 a Windows Server 2016 AD. |
| msExchArchiveStatus| ms-Exch-ArchiveStatus |× | | |Online archiv: Umožňuje zákazníkům archivovat poštu. |
| msExchBlockedSendersHash| ms-Exch-BlockedOdesílateléHash |× | | |Filtrování: Zapisuje zpět místní filtrování a online bezpečné a blokované data odesílatele z klientů. |
| msExchSafePříjemciHash| ms-Exch-SafePříjemciHash  |× | | |Filtrování: Zapisuje zpět místní filtrování a online bezpečné a blokované data odesílatele z klientů. |
| msExchSafeSendersHash| ms-Exch-SafeOdesílatel  |× | | |Filtrování: Zapisuje zpět místní filtrování a online bezpečné a blokované data odesílatele z klientů. |
| msExchUCVoiceMailSettings| ms-Exch-UCVoiceMailSettings |× | | |Povolení jednotného zasílání zpráv ( UM) – Online hlasová pošta: Používá se integrace microsoft lyncserveru k označení místního lyncserveru, že uživatel má hlasovou poštu v online službách. |
| msExchUserHoldPolicies| ms-Exch-UserHoldPolicies |× | | |Blokování z důvodu soudních sporů: Umožňuje cloudovým službám určit, kteří uživatelé jsou v rámci blokování z důvodu soudních sporů. |
| proxyAddresses| proxyAddresses |× |× |× |Je vložena pouze adresa x500 z Exchange Online. |
| publicDelegates| ms-Exch-Veřejné-Delegáti  |× | | |Umožňuje poštovní schránce Exchange Online udělit oprávnění SendOnBehalfTo uživatelům s místní poštovní schránkou Exchange. Vyžaduje Azure AD Connect sestavení 1.1.552.0 nebo po. |

## <a name="exchange-mail-public-folder"></a>Veřejná složka exchange mailu
Tyto atributy jsou synchronizovány z místní služby Active Directory do služby Azure AD, když vyberete povolení **veřejné složky Pošty exchange**.

| Název atributu | Veřejná složka | Poznámka |
| --- | :---:| --- |
| displayName | × |  |
| pošta | × |  |
| msExchRecipientTypeDetails | × |  |
| objectGUID | × |  |
| proxyAddresses | × |  |
| Targetaddress | × |  |

## <a name="device-writeback"></a>Zpětný zápis zařízení
Objekty zařízení jsou vytvářeny ve službě Active Directory. Tyto objekty můžou být zařízení připojená k Azure AD nebo počítačům s Windows 10, ke které se připojila k doméně.

| Název atributu | Zařízení | Poznámka |
| --- |:---:| --- |
| altSecurityIdentities |× | |
| displayName |× | |
| rozlišující název |× | |
| msDS-CloudAnchor |× | |
| id zařízení msDS |× | |
| msDS-DeviceObjectVersion |× | |
| msDS-DeviceOSType |× | |
| msDS-DeviceOSVersion |× | |
| msDS-DevicePhysicalIDs |× | |
| msDS-KeyCredentialLink |× |Pouze se schématem ad systému Windows Server 2016 |
| msDS-IsCompliant |× | |
| msDS-IsEnabled |× | |
| msDS-IsManaged |× | |
| msDS-RegisteredOwner |× | |

## <a name="notes"></a>Poznámky
* Při použití alternativní ID, místní atribut userPrincipalName je synchronizován s atributem Azure AD onPremisesUserPrincipalName. Alternativní ID atribut, například pošta, je synchronizován s atributem Azure AD userPrincipalName.
* Ve výše uvedených seznamech se typ objektu **Uživatel** vztahuje také na typ objektu **iNetOrgPerson**.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o konfiguraci [synchronizace Azure AD Connect.](how-to-connect-sync-whatis.md)

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).

---
title: Atributy synchronizované pomocí Azure AD Connect | Microsoft Docs
description: Zobrazuje seznam atributů, které jsou synchronizované s Azure Active Directory.
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
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766085"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Azure AD Connect synchronizace: atributy synchronizované s Azure Active Directory
Toto téma obsahuje seznam atributů synchronizovaných pomocí Azure AD Connect synchronizace.  
Atributy jsou seskupené podle související aplikace Azure AD.

## <a name="attributes-to-synchronize"></a>Atributy, které se mají synchronizovat
Běžným dotazem je *seznam minimálních atributů, které se mají synchronizovat*. Výchozím a doporučeným přístupem je zachování výchozích atributů, aby bylo možné v cloudu vytvořit celý globální adresář (globální seznam adres) a získat všechny funkce pro úlohy Office 365. V některých případech existují některé atributy, které vaše organizace nechce synchronizovat do cloudu, protože tyto atributy obsahují data citlivých nebo PII (osobní údaje), jako v tomto příkladu:  
![chybné atributy](./media/reference-connect-sync-attributes-synchronized/badextensionattribute.png)

V takovém případě začněte seznamem atributů v tomto tématu a Identifikujte tyto atributy, které by mohly obsahovat citlivé nebo PII data a nelze je synchronizovat. Pak tyto atributy během instalace odznačte pomocí [filtrování aplikací a atributů Azure AD](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering).

> [!WARNING]
> Při debírání atributů byste měli být opatrní a jenom zrušit výběr těchto atributů, které nemůžete synchronizovat. Zrušit výběr jiných atributů může mít negativní dopad na funkce.
>
>

## <a name="office-365-proplus"></a>Office 365 ProPlus
| Název atributu | Uživatel | Poznámka |
| --- |:---:| --- |
| accountEnabled |× |Definuje, jestli je účet povolený. |
| CN |× | |
| displayName |× | |
| objectSID |× |Mechanická vlastnost. Identifikátor uživatele služby AD používaný k údržbě synchronizace mezi službami Azure AD a AD. |
| pwdLastSet |× |Mechanická vlastnost. Používá se k zjištění, kdy už vydané tokeny jsou neověřené. Používá se pro synchronizaci hodnot hash hesel, předávací ověřování a federaci. |
|samAccountName|×| |
| sourceAnchor |× |Mechanická vlastnost. Neproměnlivý identifikátor, který udržuje vztah mezi PŘIDÁVÁNÍm a službou Azure AD. |
| usageLocation |× |Mechanická vlastnost. Země nebo oblast uživatele. Používá se pro přiřazení licencí. |
| userPrincipalName (Hlavní název uživatele) |× |Hlavní název uživatele (UPN) je přihlašovací ID pro uživatele. Nejčastěji se shoduje s hodnotou [mail]. |

## <a name="exchange-online"></a>Exchange Online
| Název atributu | Uživatel | Kontaktovat | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |× | | |Definuje, jestli je účet povolený. |
| pomocníka |× |× | | |
| altRecipient |× | | |Vyžaduje Azure AD Connect Build 1.1.552.0 nebo After. |
| authOrig |× |× |× | |
| r |× |× | | |
| CN |× | |× | |
| společném |× |× | | |
| Podnikový |× |× | | |
| countryCode |× |× | | |
| Ministerstvo |× |× | | |
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
| facsimiletelephonenumber |× |× | | |
| givenName |× |× | | |
| homePhone |× |× | | |
| příjemce |× |× |× |Tento atribut se v tuto chvíli nespotřebovává pro skupiny. |
| Iniciály |× |× | | |
| l |× |× | | |
| legacyExchangeDN |× |× |× | |
| mailNickname |× |× |× | |
| managedBy | | |× | |
| programu |× |× | | |
| člen | | |× | |
| telefon |× |× | | |
| msDS-HABSeniorityIndex |× |× |× | |
| msDS-PhoneticDisplayName |× |× |× | |
| msExchArchiveGUID |× | | | |
| msExchArchiveName |× | | | |
| msExchAssistantName |× |× | | |
| msExchAuditAdmin |× | | | |
| msExchAuditDelegate |× | | | |
| msExchAuditDelegateAdmin |× | | | |
| msExchAuditOwner |× | | | |
| msExchBlockedSendersHash |× |× | | |
| msExchBypassAudit |× | | | |
| msExchBypassModerationLink | | |× |K dispozici ve verzi Azure AD Connect 1.1.524.0 |
| msExchCoManagedByLink | | |× | |
| msExchDelegateListLink |× | | | |
| msExchELCExpirySuspensionEnd |× | | | |
| msExchELCExpirySuspensionStart |× | | | |
| msExchELCMailboxFlags |× | | | |
| msExchEnableModeration |× | |× | |
| msExchExtensionCustomAttribute1 |× |× |× |Tento atribut aktuálně nevyužívá Exchange Online. |
| msExchExtensionCustomAttribute2 |× |× |× |Tento atribut aktuálně nevyužívá Exchange Online. |
| msExchExtensionCustomAttribute3 |× |× |× |Tento atribut aktuálně nevyužívá Exchange Online. |
| msExchExtensionCustomAttribute4 |× |× |× |Tento atribut aktuálně nevyužívá Exchange Online. |
| msExchExtensionCustomAttribute5 |× |× |× |Tento atribut aktuálně nevyužívá Exchange Online. |
| msExchHideFromAddressLists |× |× |× | |
| msExchImmutableID |× | | | |
| msExchLitigationHoldDate |× |× |× | |
| msExchLitigationHoldOwner |× |× |× | |
| msExchMailboxAuditEnable |× | | | |
| msExchMailboxAuditLogAgeLimit |× | | | |
| msExchMailboxGuid |× | | | |
| msExchModeratedByLink |× |× |× | |
| msExchModerationFlags |× |× |× | |
| msExchRecipientDisplayType |× |× |× | |
| msExchRecipientTypeDetails |× |× |× | |
| msExchRemoteRecipientType |× | | | |
| msExchRequireAuthToSendTo |× |× |× | |
| msExchResourceCapacity |× | | | |
| msExchResourceDisplay |× | | | |
| msExchResourceMetaData |× | | | |
| msExchResourceSearchProperties |× | | | |
| msExchRetentionComment |× |× |× | |
| msExchRetentionURL |× |× |× | |
| msExchSafeRecipientsHash |× |× | | |
| msExchSafeSendersHash |× |× | | |
| msExchSenderHintTranslations |× |× |× | |
| msExchTeamMailboxExpiration |× | | | |
| msExchTeamMailboxOwners |× | | | |
| msExchTeamMailboxSharePointUrl |× | | | |
| msExchUserHoldPolicies |× | | | |
| msOrg-organizační | | |× | |
| objectSID |× | |× |Mechanická vlastnost. Identifikátor uživatele služby AD používaný k údržbě synchronizace mezi službami Azure AD a AD. |
| oOFReplyToOriginator | | |× | |
| otherFacsimileTelephone |× |× | | |
| otherHomePhone |× |× | | |
| otherTelephone |× |× | | |
| stránek |× |× | | |
| physicalDeliveryOfficeName |× |× | | |
| Ovládacím |× |× | | |
| proxyAddresses |× |× |× | |
| publicDelegates |× |× |× | |
| pwdLastSet |× | | |Mechanická vlastnost. Používá se k zjištění, kdy už vydané tokeny jsou neověřené. Používá se pro synchronizaci hesla i federaci. |
| reportToOriginator | | |× | |
| reportToOwner | | |× | |
| SN |× |× | | |
| sourceAnchor |× |× |× |Mechanická vlastnost. Neproměnlivý identifikátor, který udržuje vztah mezi PŘIDÁVÁNÍm a službou Azure AD. |
| Sv |× |× | | |
| streetAddress |× |× | | |
| targetAddress |× |× | | |
| telephoneAssistant |× |× | | |
| telephoneNumber |× |× | | |
| thumbnailphoto |× |× | | |
| hlava |× |× | | |
| unauthOrig |× |× |× | |
| usageLocation |× | | |Mechanická vlastnost. Země nebo oblast uživatele. Používá se pro přiřazení licencí. |
| userCertificate |× |× | | |
| userPrincipalName (Hlavní název uživatele) |× | | |Hlavní název uživatele (UPN) je přihlašovací ID pro uživatele. Nejčastěji se shoduje s hodnotou [mail]. |
| userSMIMECertificates |× |× | | |
| wWWHomePage |× |× | | |

## <a name="sharepoint-online"></a>SharePoint Online
| Název atributu | Uživatel | Kontaktovat | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |× | | |Definuje, jestli je účet povolený. |
| authOrig |× |× |× | |
| r |× |× | | |
| CN |× | |× | |
| společném |× |× | | |
| Podnikový |× |× | | |
| countryCode |× |× | | |
| Ministerstvo |× |× | | |
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
| facsimiletelephonenumber |× |× | | |
| givenName |× |× | | |
| hideDLMembership | | |× | |
| homePhone |× |× | | |
| příjemce |× |× |× | |
| iniciály |× |× | | |
| ipPhone |× |× | | |
| l |× |× | | |
| modul |× |× |× | |
| mailNickname |× |× |× | |
| managedBy | | |× | |
| programu |× |× | | |
| člen | | |× | |
| middleName |× |× | | |
| telefon |× |× | | |
| msExchTeamMailboxExpiration |× | | | |
| msExchTeamMailboxOwners |× | | | |
| msExchTeamMailboxSharePointLinkedBy |× | | | |
| msExchTeamMailboxSharePointUrl |× | | | |
| objectSID |× | |× |Mechanická vlastnost. Identifikátor uživatele služby AD používaný k údržbě synchronizace mezi službami Azure AD a AD. |
| oOFReplyToOriginator | | |× | |
| otherFacsimileTelephone |× |× | | |
| otherHomePhone |× |× | | |
| otherIpPhone |× |× | | |
| otherMobile |× |× | | |
| otherPager |× |× | | |
| otherTelephone |× |× | | |
| stránek |× |× | | |
| physicalDeliveryOfficeName |× |× | | |
| Ovládacím |× |× | | |
| postOfficeBox |× |× | |SharePoint Online tuto vlastnost aktuálně nevyužívá. |
| preferredLanguage |× | | | |
| proxyAddresses |× |× |× | |
| pwdLastSet |× | | |Mechanická vlastnost. Používá se k zjištění, kdy už vydané tokeny jsou neověřené. Používá se pro synchronizaci hodnot hash hesel, předávací ověřování a federaci. |
| reportToOriginator | | |× | |
| reportToOwner | | |× | |
| SN |× |× | | |
| sourceAnchor |× |× |× |Mechanická vlastnost. Neproměnlivý identifikátor, který udržuje vztah mezi PŘIDÁVÁNÍm a službou Azure AD. |
| Sv |× |× | | |
| streetAddress |× |× | | |
| targetAddress |× |× | | |
| telephoneAssistant |× |× | | |
| telephoneNumber |× |× | | |
| thumbnailphoto |× |× | | |
| hlava |× |× | | |
| unauthOrig |× |× |× | |
| url |× |× | | |
| usageLocation |× | | |Mechanická vlastnost. Země nebo oblast uživatele
. Používá se pro přiřazení licencí. |
| userPrincipalName (Hlavní název uživatele) |× | | |Hlavní název uživatele (UPN) je přihlašovací ID pro uživatele. Nejčastěji se shoduje s hodnotou [mail]. |
| wWWHomePage |× |× | | |

## <a name="teams-and-skype-for-business-online"></a>Týmy a Skype pro firmy online
| Název atributu | Uživatel | Kontaktovat | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |× | | |Definuje, jestli je účet povolený. |
| r |× |× | | |
| CN |× | |× | |
| společném |× |× | | |
| Podnikový |× |× | | |
| Ministerstvo |× |× | | |
| description |× |× |× | |
| displayName |× |× |× | |
| facsimiletelephonenumber |× |× |× | |
| givenName |× |× | | |
| homePhone |× |× | | |
| ipPhone |× |× | | |
| l |× |× | | |
| modul |× |× |× | |
| mailNickname |× |× |× | |
| managedBy | | |× | |
| programu |× |× | | |
| člen | | |× | |
| telefon |× |× | | |
| msExchHideFromAddressLists |× |× |× | |
| msRTCSIP – ApplicationOptions |× | | | |
| msRTCSIP – DeploymentLocator |× |× | | |
| msRTCSIP-line |× |× | | |
| msRTCSIP – OptionFlags |× |× | | |
| msRTCSIP – OwnerUrn |× | | | |
| msRTCSIP – PrimaryUserAddress |× |× | | |
| msRTCSIP – UserEnabled |× |× | | |
| objectSID |× | |× |Mechanická vlastnost. Identifikátor uživatele služby AD používaný k údržbě synchronizace mezi službami Azure AD a AD. |
| otherTelephone |× |× | | |
| physicalDeliveryOfficeName |× |× | | |
| Ovládacím |× |× | | |
| preferredLanguage |× | | | |
| proxyAddresses |× |× |× | |
| pwdLastSet |× | | |Mechanická vlastnost. Používá se k zjištění, kdy už vydané tokeny jsou neověřené. Používá se pro synchronizaci hodnot hash hesel, předávací ověřování a federaci. |
| SN |× |× | | |
| sourceAnchor |× |× |× |Mechanická vlastnost. Neproměnlivý identifikátor, který udržuje vztah mezi PŘIDÁVÁNÍm a službou Azure AD. |
| Sv |× |× | | |
| streetAddress |× |× | | |
| telephoneNumber |× |× | | |
| thumbnailphoto |× |× | | |
| hlava |× |× | | |
| usageLocation |× | | |Mechanická vlastnost. Země nebo oblast uživatele. Používá se pro přiřazení licencí. |
| userPrincipalName (Hlavní název uživatele) |× | | |Hlavní název uživatele (UPN) je přihlašovací ID pro uživatele. Nejčastěji se shoduje s hodnotou [mail]. |
| wWWHomePage |× |× | | |

## <a name="azure-rms"></a>Azure RMS
| Název atributu | Uživatel | Kontaktovat | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |× | | |Definuje, jestli je účet povolený. |
| CN |× | |× |Běžný název nebo alias. Nejčastěji se jedná o předponu hodnoty [mail]. |
| displayName |× |× |× |Řetězec, který představuje název často zobrazený jako popisný název (křestní jméno příjmení). |
| modul |× |× |× |úplná e-mailová adresa |
| člen | | |× | |
| objectSID |× | |× |Mechanická vlastnost. Identifikátor uživatele služby AD používaný k údržbě synchronizace mezi službami Azure AD a AD. |
| proxyAddresses |× |× |× |Mechanická vlastnost. Používá se v Azure AD. Obsahuje všechny sekundární e-mailové adresy uživatele. |
| pwdLastSet |× | | |Mechanická vlastnost. Používá se k zjištění, kdy už vydané tokeny jsou neověřené. |
| sourceAnchor |× |× |× |Mechanická vlastnost. Neproměnlivý identifikátor, který udržuje vztah mezi PŘIDÁVÁNÍm a službou Azure AD. |
| usageLocation |× | | |Mechanická vlastnost. Země nebo oblast uživatele. Používá se pro přiřazení licencí. |
| userPrincipalName (Hlavní název uživatele) |× | | |Tento hlavní název uživatele (UPN) je přihlašovací ID pro uživatele. Nejčastěji se shoduje s hodnotou [mail]. |

## <a name="intune"></a>Intune
| Název atributu | Uživatel | Kontaktovat | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |× | | |Definuje, jestli je účet povolený. |
| r |× |× | | |
| CN |× | |× | |
| description |× |× |× | |
| displayName |× |× |× | |
| modul |× |× |× | |
| mailNickname |× |× |× | |
| člen | | |× | |
| objectSID |× | |× |Mechanická vlastnost. Identifikátor uživatele služby AD používaný k údržbě synchronizace mezi službami Azure AD a AD. |
| proxyAddresses |× |× |× | |
| pwdLastSet |× | | |Mechanická vlastnost. Používá se k zjištění, kdy už vydané tokeny jsou neověřené. Používá se pro synchronizaci hodnot hash hesel, předávací ověřování a federaci. |
| sourceAnchor |× |× |× |Mechanická vlastnost. Neproměnlivý identifikátor, který udržuje vztah mezi PŘIDÁVÁNÍm a službou Azure AD. |
| usageLocation |× | | |Mechanická vlastnost. Země nebo oblast uživatele. Používá se pro přiřazení licencí. |
| userPrincipalName (Hlavní název uživatele) |× | | |Hlavní název uživatele (UPN) je přihlašovací ID pro uživatele. Nejčastěji se shoduje s hodnotou [mail]. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Název atributu | Uživatel | Kontaktovat | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |× | | |Definuje, jestli je účet povolený. |
| r |× |× | | |
| CN |× | |× | |
| společném |× |× | | |
| Podnikový |× |× | | |
| countryCode |× |× | | |
| description |× |× |× | |
| displayName |× |× |× | |
| facsimiletelephonenumber |× |× | | |
| givenName |× |× | | |
| l |× |× | | |
| managedBy | | |× | |
| programu |× |× | | |
| člen | | |× | |
| telefon |× |× | | |
| objectSID |× | |× |Mechanická vlastnost. Identifikátor uživatele služby AD používaný k údržbě synchronizace mezi službami Azure AD a AD. |
| physicalDeliveryOfficeName |× |× | | |
| Ovládacím |× |× | | |
| preferredLanguage |× | | | |
| pwdLastSet |× | | |Mechanická vlastnost. Používá se k zjištění, kdy už vydané tokeny jsou neověřené. Používá se pro synchronizaci hodnot hash hesel, předávací ověřování a federaci. |
| SN |× |× | | |
| sourceAnchor |× |× |× |Mechanická vlastnost. Neproměnlivý identifikátor, který udržuje vztah mezi PŘIDÁVÁNÍm a službou Azure AD. |
| Sv |× |× | | |
| streetAddress |× |× | | |
| telephoneNumber |× |× | | |
| hlava |× |× | | |
| usageLocation |× | | |Mechanická vlastnost. Země nebo oblast uživatele. Používá se pro přiřazení licencí. |
| userPrincipalName (Hlavní název uživatele) |× | | |Hlavní název uživatele (UPN) je přihlašovací ID pro uživatele. Nejčastěji se shoduje s hodnotou [mail]. |

## <a name="3rd-party-applications"></a>aplikace třetích stran
Tato skupina je sadou atributů používaných jako minimální atributy, které jsou potřeba pro obecné úlohy nebo aplikace. Dá se použít pro úlohy, které nejsou uvedené v jiné části nebo pro aplikaci od jiného výrobce než Microsoftu. Používá se explicitně pro následující:

* Yammer (jenom uživatel se spotřebovává)
* [Hybridní scénáře spolupráce mezi organizacemi B2B (Business-to-Business) nabízené prostředky, jako je SharePoint](https://go.microsoft.com/fwlink/?LinkId=747036)

Tato skupina je sadou atributů, které se dají použít, pokud se adresář služby Azure AD nepoužívá k podpoře Office 365, Dynamics nebo Intune. Má malou sadu základních atributů.

| Název atributu | Uživatel | Kontaktovat | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |× | | |Definuje, jestli je účet povolený. |
| CN |× | |× | |
| displayName |× |× |× | |
| Zaměstnance |× |  |  | |
| givenName |× |× | | |
| modul |× | |× | |
| managedBy | | |× | |
| mailNickName |× |× |× | |
| člen | | |× | |
| objectSID |× | | |Mechanická vlastnost. Identifikátor uživatele služby AD používaný k údržbě synchronizace mezi službami Azure AD a AD. |
| proxyAddresses |× |× |× | |
| pwdLastSet |× | | |Mechanická vlastnost. Používá se k zjištění, kdy už vydané tokeny jsou neověřené. Používá se pro synchronizaci hodnot hash hesel, předávací ověřování a federaci. |
| SN |× |× | | |
| sourceAnchor |× |× |× |Mechanická vlastnost. Neproměnlivý identifikátor, který udržuje vztah mezi PŘIDÁVÁNÍm a službou Azure AD. |
| usageLocation |× | | |Mechanická vlastnost. Země nebo oblast uživatele. Používá se pro přiřazení licencí. |
| userPrincipalName (Hlavní název uživatele) |× | | |Hlavní název uživatele (UPN) je přihlašovací ID pro uživatele. Nejčastěji se shoduje s hodnotou [mail]. |

## <a name="windows-10"></a>Windows 10
Počítač připojený k doméně Windows 10 (zařízení) synchronizuje některé atributy do Azure AD. Další informace o těchto scénářích najdete v tématu [připojení zařízení připojených k doméně ke službě Azure AD pro prostředí Windows 10](../active-directory-azureadjoin-devices-group-policy.md). Tyto atributy se vždycky synchronizují a Windows 10 se nezobrazí jako aplikace, kterou můžete zrušit. Počítač připojený k doméně Windows 10 je identifikovaný, protože má atribut userCertificate vyplněný.

| Název atributu | Zařízení | Poznámka |
| --- |:---:| --- |
| accountEnabled |× | |
| deviceTrustType |× |Hodnota pevně zakódované pro počítače připojené k doméně |
| displayName |× | |
| MS-DS-CreatorSID |× |Také se označuje jako registeredOwnerReference. |
| objectGUID |× |Také se nazývá deviceID. |
| objectSID |× |Také se označuje jako onPremisesSecurityIdentifier. |
| operatingSystem |× |Také se označuje jako deviceOSType. |
| operatingSystemVersion |× |Také se označuje jako deviceOSVersion. |
| userCertificate |× | |

Tyto atributy pro **uživatele** jsou kromě dalších vybraných aplikací.  

| Název atributu | Uživatel | Poznámka |
| --- |:---:| --- |
| domainFQDN |× |Také se označuje jako dnsDomainName. Například contoso.com. |
| domainNetBios |× |Označuje se také jako netBios. Například CONTOSO. |
| msDS-KeyCredentialLink |× |Jakmile se uživatel zaregistruje ve Windows Hello pro firmy. | 

## <a name="exchange-hybrid-writeback"></a>Hybridní zpětný zápis systému Exchange
Tyto atributy se napíší zpátky z Azure AD do místní služby Active Directory, když vyberete možnost povolit službu **Exchange Hybrid**. V závislosti na verzi systému Exchange může být synchronizovaný méně atributů.

| Název atributu (místní služba AD) | Název atributu (připojit uživatelské rozhraní) | Uživatel | Kontaktovat | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |---|
| msDS-ExternalDirectoryObjectID| MS-DS-External-Directory-Object-ID |× | | |Je odvozeno od cloudAnchor ve službě Azure AD. Tento atribut je v systémech Exchange 2016 a Windows Server 2016 AD nový. |
| msExchArchiveStatus| MS-Exch-ArchiveStatus |× | | |Online archiv: umožňuje zákazníkům archivovat poštu. |
| msExchBlockedSendersHash| MS-Exch-BlockedSendersHash |× | | |Filtrování: zapisuje zpět místní filtrování a online bezpečná a blokovaná data odesílatele od klientů. |
| msExchSafeRecipientsHash| MS-Exch-SafeRecipientsHash  |× | | |Filtrování: zapisuje zpět místní filtrování a online bezpečná a blokovaná data odesílatele od klientů. |
| msExchSafeSendersHash| MS-Exch-SafeSendersHash  |× | | |Filtrování: zapisuje zpět místní filtrování a online bezpečná a blokovaná data odesílatele od klientů. |
| msExchUCVoiceMailSettings| MS-Exch-UCVoiceMailSettings |× | | |Povolit Unified Messaging (UM) – online hlasová pošta: používá se integrací Microsoft Lync serveru k tomu, aby se na serveru Lync používala hlasová pošta v online služby. |
| msExchUserHoldPolicies| MS-Exch-UserHoldPolicies |× | | |Blokování sporů: umožňuje cloudovým službám určit, kteří uživatelé jsou v rámci sporů. |
| proxyAddresses| proxyAddresses |× |× |× |Je vložena pouze adresa x500 z Exchange Online. |
| publicDelegates| MS-Exch – veřejné – Delegáti  |× | | |Umožňuje poštovní schránce Exchange Online udělit uživatelům s místní poštovní schránkou Exchange SendOnBehalfTo práva. Vyžaduje Azure AD Connect Build 1.1.552.0 nebo After. |

## <a name="exchange-mail-public-folder"></a>Veřejná složka pošty Exchange
Tyto atributy se synchronizují z místní služby Active Directory do Azure AD, když vyberete povolení **veřejné složky pošty Exchange**.

| Název atributu | PublicFolder | Poznámka |
| --- | :---:| --- |
| displayName | × |  |
| modul | × |  |
| msExchRecipientTypeDetails | × |  |
| objectGUID | × |  |
| proxyAddresses | × |  |
| targetAddress | × |  |

## <a name="device-writeback"></a>Zpětný zápis zařízení
Objekty zařízení se vytvářejí ve službě Active Directory. Tyto objekty můžou být zařízení připojená ke službě Azure AD nebo počítačům s Windows 10 připojenými k doméně.

| Název atributu | Zařízení | Poznámka |
| --- |:---:| --- |
| altSecurityIdentities |× | |
| displayName |× | |
| jména |× | |
| msDS-CloudAnchor |× | |
| msDS-DeviceID |× | |
| msDS-DeviceObjectVersion |× | |
| msDS-DeviceOSType |× | |
| msDS-DeviceOSVersion |× | |
| msDS-DevicePhysicalIDs |× | |
| msDS-KeyCredentialLink |× |Jenom se schématem AD ve Windows serveru 2016 |
| msDS-nedodržuje předpisy |× | |
| msDS-povoleno |× | |
| msDS-spravovatelný |× | |
| msDS-RegisteredOwner |× | |

## <a name="notes"></a>Poznámky
* Při použití alternativního ID se místní atribut userPrincipalName synchronizuje s atributem Azure AD onPremisesUserPrincipalName. Identifikátor alternativní ID, například pošta, je synchronizovaný s atributem userPrincipalName služby Azure AD.
* Ve výše uvedených seznamech se typ objektu **uživatel** vztahuje také na typ objektu **InetOrgPerson**.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o konfiguraci [Azure AD Connect synchronizace](how-to-connect-sync-whatis.md) .

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).

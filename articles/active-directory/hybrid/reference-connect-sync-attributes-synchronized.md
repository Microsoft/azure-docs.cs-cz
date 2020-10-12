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
ms.date: 04/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9411c22183620f883b4d2819eb3078e49837e578
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90016074"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Synchronizace služby Azure AD Connect: Atributy synchronizované se službou Azure Active Directory
Toto téma obsahuje seznam atributů synchronizovaných pomocí Azure AD Connect synchronizace.  
Atributy jsou seskupené podle související aplikace Azure AD.

## <a name="attributes-to-synchronize"></a>Atributy, které se mají synchronizovat
Běžným dotazem je *seznam minimálních atributů, které se mají synchronizovat*. Výchozím a doporučeným přístupem je zachování výchozích atributů, aby bylo možné v cloudu vytvořit úplný seznam celých adres (globální seznam adres) a získat všechny funkce Microsoft 365 úloh. V některých případech existují některé atributy, které vaše organizace nechce synchronizovat s cloudem, protože tyto atributy obsahují citlivé osobní údaje, jako v tomto příkladu:  
![Chybné atributy](./media/reference-connect-sync-attributes-synchronized/badextensionattribute.png)

V takovém případě začněte seznamem atributů v tomto tématu a Identifikujte atributy, které by obsahovaly osobní údaje a nelze je synchronizovat. Pak tyto atributy během instalace odznačte pomocí [filtrování aplikací a atributů Azure AD](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering).

> [!WARNING]
> Při debírání atributů byste měli být opatrní a jenom zrušit výběr těchto atributů, které nemůžete synchronizovat. Zrušit výběr jiných atributů může mít negativní dopad na funkce.
>
>

## <a name="microsoft-365-apps-for-enterprise"></a>Aplikace Microsoft 365 pro podniky
| Název atributu | Uživatel | Komentář |
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
| Název atributu | Uživatel | Kontakt | Group (Skupina) | Komentář |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |× | | |Definuje, jestli je účet povolený. |
| pomocníka |X |X | | |
| altRecipient |× | | |Vyžaduje Azure AD Connect Build 1.1.552.0 nebo After. |
| authOrig |X |X |X | |
| c |X |X | | |
| CN |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| Oddělení |X |X | | |
| Popis | | |× | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| homePhone |X |X | | |
| příjemce |X |X |X |Tento atribut se v tuto chvíli nespotřebovává pro skupiny. |
| Iniciály |X |X | | |
| l |X |X | | |
| legacyExchangeDN |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |× | |
| manager |X |X | | |
| člen | | |× | |
| mobil |X |X | | |
| msDS-HABSeniorityIndex |X |X |X | |
| msDS-PhoneticDisplayName |X |X |X | |
| msExchArchiveGUID |× | | | |
| msExchArchiveName |× | | | |
| msExchAssistantName |X |X | | |
| msExchAuditAdmin |× | | | |
| msExchAuditDelegate |× | | | |
| msExchAuditDelegateAdmin |× | | | |
| msExchAuditOwner |× | | | |
| msExchBlockedSendersHash |X |X | | |
| msExchBypassAudit |× | | | |
| msExchBypassModerationLink | | |× |K dispozici ve verzi Azure AD Connect 1.1.524.0 |
| msExchCoManagedByLink | | |× | |
| msExchDelegateListLink |× | | | |
| msExchELCExpirySuspensionEnd |× | | | |
| msExchELCExpirySuspensionStart |× | | | |
| msExchELCMailboxFlags |× | | | |
| msExchEnableModeration |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |Tento atribut aktuálně nevyužívá Exchange Online. |
| msExchExtensionCustomAttribute2 |X |X |X |Tento atribut aktuálně nevyužívá Exchange Online. |
| msExchExtensionCustomAttribute3 |X |X |X |Tento atribut aktuálně nevyužívá Exchange Online. |
| msExchExtensionCustomAttribute4 |X |X |X |Tento atribut aktuálně nevyužívá Exchange Online. |
| msExchExtensionCustomAttribute5 |X |X |X |Tento atribut aktuálně nevyužívá Exchange Online. |
| msExchHideFromAddressLists |X |X |X | |
| msExchImmutableID |× | | | |
| msExchLitigationHoldDate |X |X |X | |
| msExchLitigationHoldOwner |X |X |X | |
| msExchMailboxAuditEnable |× | | | |
| msExchMailboxAuditLogAgeLimit |× | | | |
| msExchMailboxGuid |× | | | |
| msExchModeratedByLink |X |X |X | |
| msExchModerationFlags |X |X |X | |
| msExchRecipientDisplayType |X |X |X | |
| msExchRecipientTypeDetails |X |X |X | |
| msExchRemoteRecipientType |× | | | |
| msExchRequireAuthToSendTo |X |X |X | |
| msExchResourceCapacity |× | | | |
| msExchResourceDisplay |× | | | |
| msExchResourceMetaData |× | | | |
| msExchResourceSearchProperties |× | | | |
| msExchRetentionComment |X |X |X | |
| msExchRetentionURL |X |X |X | |
| msExchSafeRecipientsHash |X |X | | |
| msExchSafeSendersHash |X |X | | |
| msExchSenderHintTranslations |X |X |X | |
| msExchTeamMailboxExpiration |× | | | |
| msExchTeamMailboxOwners |× | | | |
| msExchTeamMailboxSharePointUrl |× | | | |
| msExchUserHoldPolicies |× | | | |
| msOrg-organizační | | |× | |
| objectSID |X | |X |Mechanická vlastnost. Identifikátor uživatele služby AD používaný k údržbě synchronizace mezi službami Azure AD a AD. |
| oOFReplyToOriginator | | |× | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherTelephone |X |X | | |
| stránek |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| Ovládacím |X |X | | |
| proxyAddresses |X |X |X | |
| publicDelegates |X |X |X | |
| pwdLastSet |× | | |Mechanická vlastnost. Používá se k zjištění, kdy už vydané tokeny jsou neověřené. Používá se pro synchronizaci hesla i federaci. |
| reportToOriginator | | |× | |
| reportToOwner | | |× | |
| sn |X |X | | |
| sourceAnchor |X |X |X |Mechanická vlastnost. Neproměnlivý identifikátor, který udržuje vztah mezi PŘIDÁVÁNÍm a službou Azure AD. |
| st |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |synchronizuje se jenom jednou z Azure AD po Exchange Online, po které se Exchange Online stal zdrojem oprávnění pro tento atribut a jakékoli pozdější změny se nedají synchronizovat z místního. Další informace najdete v tématu ([KB](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange)).|
| title |X |X | | |
| unauthOrig |X |X |X | |
| usageLocation |× | | |Mechanická vlastnost. Země nebo oblast uživatele. Používá se pro přiřazení licencí. |
| userCertificate |X |X | | |
| userPrincipalName (Hlavní název uživatele) |× | | |Hlavní název uživatele (UPN) je přihlašovací ID pro uživatele. Nejčastěji se shoduje s hodnotou [mail]. |
| userSMIMECertificates |X |X | | |
| wWWHomePage |X |X | | |

## <a name="sharepoint-online"></a>SharePoint Online
| Název atributu | Uživatel | Kontakt | Group (Skupina) | Komentář |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |× | | |Definuje, jestli je účet povolený. |
| authOrig |X |X |X | |
| c |X |X | | |
| CN |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| Oddělení |X |X | | |
| Popis |X |X |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| hideDLMembership | | |× | |
| homePhone |X |X | | |
| příjemce |X |X |X | |
| iniciály |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| pošta |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |× | |
| manager |X |X | | |
| člen | | |× | |
| middleName |X |X | | |
| mobil |X |X | | |
| msExchTeamMailboxExpiration |× | | | |
| msExchTeamMailboxOwners |× | | | |
| msExchTeamMailboxSharePointLinkedBy |× | | | |
| msExchTeamMailboxSharePointUrl |× | | | |
| objectSID |X | |X |Mechanická vlastnost. Identifikátor uživatele služby AD používaný k údržbě synchronizace mezi službami Azure AD a AD. |
| oOFReplyToOriginator | | |× | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherIpPhone |X |X | | |
| otherMobile |X |X | | |
| otherPager |X |X | | |
| otherTelephone |X |X | | |
| stránek |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| Ovládacím |X |X | | |
| postOfficeBox |X |X | |SharePoint Online tuto vlastnost aktuálně nevyužívá. |
| preferredLanguage |× | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |× | | |Mechanická vlastnost. Používá se k zjištění, kdy už vydané tokeny jsou neověřené. Používá se pro synchronizaci hodnot hash hesel, předávací ověřování a federaci. |
| reportToOriginator | | |× | |
| reportToOwner | | |× | |
| sn |X |X | | |
| sourceAnchor |X |X |X |Mechanická vlastnost. Neproměnlivý identifikátor, který udržuje vztah mezi PŘIDÁVÁNÍm a službou Azure AD. |
| st |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |synchronizuje se jenom jednou z Azure AD po Exchange Online, po které se Exchange Online stal zdrojem oprávnění pro tento atribut a jakékoli pozdější změny se nedají synchronizovat z místního. Další informace najdete v tématu ([KB](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange)).|
| title |X |X | | |
| unauthOrig |X |X |X | |
| url |X |X | | |
| usageLocation |× | | |Mechanická vlastnost. Země nebo oblast uživatele
. Používá se pro přiřazení licencí. |
| userPrincipalName (Hlavní název uživatele) |× | | |Hlavní název uživatele (UPN) je přihlašovací ID pro uživatele. Nejčastěji se shoduje s hodnotou [mail]. |
| wWWHomePage |X |X | | |

## <a name="teams-and-skype-for-business-online"></a>Týmy a Skype pro firmy online
| Název atributu | Uživatel | Kontakt | Group (Skupina) | Komentář |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |× | | |Definuje, jestli je účet povolený. |
| c |X |X | | |
| CN |X | |X | |
| co |X |X | | |
| company |X |X | | |
| Oddělení |X |X | | |
| Popis |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X |X | |
| givenName |X |X | | |
| homePhone |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| pošta |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |× | |
| manager |X |X | | |
| člen | | |× | |
| mobil |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP – ApplicationOptions |× | | | |
| msRTCSIP – DeploymentLocator |X |X | | |
| msRTCSIP-line |X |X | | |
| msRTCSIP – OptionFlags |X |X | | |
| msRTCSIP – OwnerUrn |× | | | |
| msRTCSIP – PrimaryUserAddress |X |X | | |
| msRTCSIP – UserEnabled |X |X | | |
| objectSID |X | |X |Mechanická vlastnost. Identifikátor uživatele služby AD používaný k údržbě synchronizace mezi službami Azure AD a AD. |
| otherTelephone |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| Ovládacím |X |X | | |
| preferredLanguage |× | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |× | | |Mechanická vlastnost. Používá se k zjištění, kdy už vydané tokeny jsou neověřené. Používá se pro synchronizaci hodnot hash hesel, předávací ověřování a federaci. |
| sn |X |X | | |
| sourceAnchor |X |X |X |Mechanická vlastnost. Neproměnlivý identifikátor, který udržuje vztah mezi PŘIDÁVÁNÍm a službou Azure AD. |
| st |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |synchronizuje se jenom jednou z Azure AD po Exchange Online, po které se Exchange Online stal zdrojem oprávnění pro tento atribut a jakékoli pozdější změny se nedají synchronizovat z místního. Další informace najdete v tématu ([KB](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange)).|
| title |X |X | | |
| usageLocation |× | | |Mechanická vlastnost. Země nebo oblast uživatele. Používá se pro přiřazení licencí. |
| userPrincipalName (Hlavní název uživatele) |× | | |Hlavní název uživatele (UPN) je přihlašovací ID pro uživatele. Nejčastěji se shoduje s hodnotou [mail]. |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| Název atributu | Uživatel | Kontakt | Group (Skupina) | Komentář |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |× | | |Definuje, jestli je účet povolený. |
| CN |X | |X |Běžný název nebo alias. Nejčastěji se jedná o předponu hodnoty [mail]. |
| displayName |X |X |X |Řetězec, který představuje název často zobrazený jako popisný název (křestní jméno příjmení). |
| pošta |X |X |X |úplná e-mailová adresa |
| člen | | |× | |
| objectSID |X | |X |Mechanická vlastnost. Identifikátor uživatele služby AD používaný k údržbě synchronizace mezi službami Azure AD a AD. |
| proxyAddresses |X |X |X |Mechanická vlastnost. Používá se v Azure AD. Obsahuje všechny sekundární e-mailové adresy uživatele. |
| pwdLastSet |× | | |Mechanická vlastnost. Používá se k zjištění, kdy už vydané tokeny jsou neověřené. |
| sourceAnchor |X |X |X |Mechanická vlastnost. Neproměnlivý identifikátor, který udržuje vztah mezi PŘIDÁVÁNÍm a službou Azure AD. |
| usageLocation |× | | |Mechanická vlastnost. Země nebo oblast uživatele. Používá se pro přiřazení licencí. |
| userPrincipalName (Hlavní název uživatele) |× | | |Tento hlavní název uživatele (UPN) je přihlašovací ID pro uživatele. Nejčastěji se shoduje s hodnotou [mail]. |

## <a name="intune"></a>Intune
| Název atributu | Uživatel | Kontakt | Group (Skupina) | Komentář |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |× | | |Definuje, jestli je účet povolený. |
| c |X |X | | |
| CN |X | |X | |
| Popis |X |X |X | |
| displayName |X |X |X | |
| pošta |X |X |X | |
| mailNickname |X |X |X | |
| člen | | |× | |
| objectSID |X | |X |Mechanická vlastnost. Identifikátor uživatele služby AD používaný k údržbě synchronizace mezi službami Azure AD a AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |× | | |Mechanická vlastnost. Používá se k zjištění, kdy už vydané tokeny jsou neověřené. Používá se pro synchronizaci hodnot hash hesel, předávací ověřování a federaci. |
| sourceAnchor |X |X |X |Mechanická vlastnost. Neproměnlivý identifikátor, který udržuje vztah mezi PŘIDÁVÁNÍm a službou Azure AD. |
| usageLocation |× | | |Mechanická vlastnost. Země nebo oblast uživatele. Používá se pro přiřazení licencí. |
| userPrincipalName (Hlavní název uživatele) |× | | |Hlavní název uživatele (UPN) je přihlašovací ID pro uživatele. Nejčastěji se shoduje s hodnotou [mail]. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Název atributu | Uživatel | Kontakt | Group (Skupina) | Komentář |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |× | | |Definuje, jestli je účet povolený. |
| c |X |X | | |
| CN |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| Popis |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| l |X |X | | |
| managedBy | | |× | |
| manager |X |X | | |
| člen | | |× | |
| mobil |X |X | | |
| objectSID |X | |X |Mechanická vlastnost. Identifikátor uživatele služby AD používaný k údržbě synchronizace mezi službami Azure AD a AD. |
| physicalDeliveryOfficeName |X |X | | |
| Ovládacím |X |X | | |
| preferredLanguage |× | | | |
| pwdLastSet |× | | |Mechanická vlastnost. Používá se k zjištění, kdy už vydané tokeny jsou neověřené. Používá se pro synchronizaci hodnot hash hesel, předávací ověřování a federaci. |
| sn |X |X | | |
| sourceAnchor |X |X |X |Mechanická vlastnost. Neproměnlivý identifikátor, který udržuje vztah mezi PŘIDÁVÁNÍm a službou Azure AD. |
| st |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| title |X |X | | |
| usageLocation |× | | |Mechanická vlastnost. Země nebo oblast uživatele. Používá se pro přiřazení licencí. |
| userPrincipalName (Hlavní název uživatele) |× | | |Hlavní název uživatele (UPN) je přihlašovací ID pro uživatele. Nejčastěji se shoduje s hodnotou [mail]. |

## <a name="3rd-party-applications"></a>aplikace třetích stran
Tato skupina je sadou atributů používaných jako minimální atributy, které jsou potřeba pro obecné úlohy nebo aplikace. Dá se použít pro úlohy, které nejsou uvedené v jiné části nebo pro aplikaci od jiného výrobce než Microsoftu. Používá se explicitně pro následující:

* Yammer (jenom uživatel se spotřebovává)
* [Hybridní scénáře spolupráce mezi organizacemi B2B (Business-to-Business) nabízené prostředky, jako je SharePoint](https://go.microsoft.com/fwlink/?LinkId=747036)

Tato skupina je sadou atributů, které se dají použít, když se adresář služby Azure AD nepoužívá k podpoře Microsoft 365, Dynamics nebo Intune. Má malou sadu základních atributů. Mějte na paměti, že jednotné přihlašování nebo zřizování pro některé aplikace třetích stran vyžaduje konfiguraci synchronizace atributů kromě atributů popsaných tady. Požadavky na aplikace jsou popsány v [kurzu aplikace SaaS](../saas-apps/tutorial-list.md) pro jednotlivé aplikace.

| Název atributu | Uživatel | Kontakt | Group (Skupina) | Komentář |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |× | | |Definuje, jestli je účet povolený. |
| CN |X | |X | |
| displayName |X |X |X | |
| employeeID |× |  |  | |
| givenName |X |X | | |
| pošta |X | |X | |
| managedBy | | |× | |
| mailNickName |X |X |X | |
| člen | | |× | |
| objectSID |× | | |Mechanická vlastnost. Identifikátor uživatele služby AD používaný k údržbě synchronizace mezi službami Azure AD a AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |× | | |Mechanická vlastnost. Používá se k zjištění, kdy už vydané tokeny jsou neověřené. Používá se pro synchronizaci hodnot hash hesel, předávací ověřování a federaci. |
| sn |X |X | | |
| sourceAnchor |X |X |X |Mechanická vlastnost. Neproměnlivý identifikátor, který udržuje vztah mezi PŘIDÁVÁNÍm a službou Azure AD. |
| usageLocation |× | | |Mechanická vlastnost. Země nebo oblast uživatele. Používá se pro přiřazení licencí. |
| userPrincipalName (Hlavní název uživatele) |× | | |Hlavní název uživatele (UPN) je přihlašovací ID pro uživatele. Nejčastěji se shoduje s hodnotou [mail]. |

## <a name="windows-10"></a>Windows 10
Počítač připojený k doméně Windows 10 (zařízení) synchronizuje některé atributy do Azure AD. Další informace o těchto scénářích najdete v tématu [připojení zařízení připojených k doméně ke službě Azure AD pro prostředí Windows 10](../devices/hybrid-azuread-join-plan.md). Tyto atributy se vždycky synchronizují a Windows 10 se nezobrazí jako aplikace, kterou můžete zrušit. Počítač připojený k doméně Windows 10 je identifikovaný, protože má atribut userCertificate vyplněný.

| Název atributu | Zařízení | Komentář |
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

| Název atributu | Uživatel | Komentář |
| --- |:---:| --- |
| domainFQDN |× |Také se označuje jako dnsDomainName. Například contoso.com. |
| domainNetBios |× |Označuje se také jako netBios. Například CONTOSO. |
| msDS-KeyCredentialLink |× |Jakmile se uživatel zaregistruje ve Windows Hello pro firmy. | 

## <a name="exchange-hybrid-writeback"></a>Hybridní zpětný zápis systému Exchange
Tyto atributy se napíší zpátky z Azure AD do místní služby Active Directory, když vyberete možnost povolit službu **Exchange Hybrid**. V závislosti na verzi systému Exchange může být synchronizovaný méně atributů.

| Název atributu (místní služba AD) | Název atributu (připojit uživatelské rozhraní) | Uživatel | Kontakt | Group (Skupina) | Komentář |
| --- |:---:|:---:|:---:| --- |---|
| msDS-ExternalDirectoryObjectID| MS-DS-External-Directory-Object-ID |× | | |Je odvozeno od cloudAnchor ve službě Azure AD. Tento atribut je v systémech Exchange 2016 a Windows Server 2016 AD nový. |
| msExchArchiveStatus| MS-Exch-ArchiveStatus |× | | |Online archiv: umožňuje zákazníkům archivovat poštu. |
| msExchBlockedSendersHash| MS-Exch-BlockedSendersHash |× | | |Filtrování: zapisuje zpět místní filtrování a online bezpečná a blokovaná data odesílatele od klientů. |
| msExchSafeRecipientsHash| MS-Exch-SafeRecipientsHash  |× | | |Filtrování: zapisuje zpět místní filtrování a online bezpečná a blokovaná data odesílatele od klientů. |
| msExchSafeSendersHash| MS-Exch-SafeSendersHash  |× | | |Filtrování: zapisuje zpět místní filtrování a online bezpečná a blokovaná data odesílatele od klientů. |
| msExchUCVoiceMailSettings| MS-Exch-UCVoiceMailSettings |× | | |Povolit Unified Messaging (UM) – online hlasová pošta: používá se integrací Microsoft Lync serveru k tomu, aby se na serveru Lync používala hlasová pošta v online služby. |
| msExchUserHoldPolicies| MS-Exch-UserHoldPolicies |× | | |Blokování sporů: umožňuje cloudovým službám určit, kteří uživatelé jsou v rámci sporů. |
| proxyAddresses| proxyAddresses |X |X |X |Je vložena pouze adresa x500 z Exchange Online. |
| publicDelegates| MS-Exch – veřejné – Delegáti  |× | | |Umožňuje poštovní schránce Exchange Online udělit uživatelům s místní poštovní schránkou Exchange SendOnBehalfTo práva. Vyžaduje Azure AD Connect Build 1.1.552.0 nebo After. |

## <a name="exchange-mail-public-folder"></a>Veřejná složka pošty Exchange
Tyto atributy se synchronizují z místní služby Active Directory do Azure AD, když vyberete povolení **veřejné složky pošty Exchange**.

| Název atributu | PublicFolder | Komentář |
| --- | :---:| --- |
| displayName | × |  |
| pošta | × |  |
| msExchRecipientTypeDetails | × |  |
| objectGUID | × |  |
| proxyAddresses | × |  |
| targetAddress | × |  |

## <a name="device-writeback"></a>Zpětný zápis zařízení
Objekty zařízení se vytvářejí ve službě Active Directory. Tyto objekty můžou být zařízení připojená ke službě Azure AD nebo počítačům s Windows 10 připojenými k doméně.

| Název atributu | Zařízení | Komentář |
| --- |:---:| --- |
| altSecurityIdentities |× | |
| displayName |× | |
| rozlišující název |× | |
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
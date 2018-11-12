---
title: Atributy synchronizované službou Azure AD Connect | Dokumentace Microsoftu
description: Seznam atributů, které jsou synchronizovány do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: c2bb36e0-5205-454c-b9b6-f4990bcedf51
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 08633b52e6b7cfef28635925ad9fcf34e065e9bf
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51251794"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Synchronizace Azure AD Connect: atributy synchronizované do Azure Active Directory
Toto téma obsahuje seznam atributy, které jsou synchronizovány pomocí synchronizace Azure AD Connect.  
Atributy jsou seskupené podle související Azure AD aplikace.

## <a name="attributes-to-synchronize"></a>Atributy pro synchronizaci
Obvyklá otázka je *k čemu slouží seznamy minimální atributy pro synchronizaci*. Výchozí a doporučený postup je zajistit výchozí atributy tak úplné GAL (globální seznam adres) lze sestavit v cloudu a získáte všechny funkce v Office 365 úlohy. V některých případech existují některé atributy, které vaší organizaci, aby synchronizovaná do cloudu vzhledem k tomu, že tyto atributy obsahují citlivé nebo identifikovatelné osobní údaje (identifikovatelné osobní údaje) data, jako jsou v tomto příkladu:  
![Chybné atributy](./media/reference-connect-sync-attributes-synchronized/badextensionattribute.png)

V tomto případě začněte seznamem atributů v tomto tématu a identifikujte ty atributy, které by obsahovala citlivé nebo identifikovatelné osobní údaje dat a není možné synchronizovat. Následně zrušte výběr těchto atributů během instalace pomocí [aplikace Azure AD a filtrování atributů](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering).

> [!WARNING]
> Při zrušení atributy, by měl být opatrní a pouze zrušte výběr těchto atributů naprosto nebylo možné synchronizovat. Unselecting dalších atributů může mít negativní dopad na funkce.
>
>

## <a name="office-365-proplus"></a>Office 365 ProPlus
| Název atributu | Uživatel | Poznámka |
| --- |:---:| --- |
| accountEnabled |X |Definuje, jestli je účet povolený. |
| CN |X | |
| displayName |X | |
| atributy objectSID |X |mechanickým vlastnost. Identifikátor uživatele AD používá k udržení synchronizace mezi Azure AD a AD. |
| pwdLastSet |X |mechanickým vlastnost. Umožňuje vědět, kdy platnost již vydané tokeny. Používá se synchronizace hodnot hash hesel, předávacího ověřování a federace. |
| sourceAnchor |X |mechanickým vlastnost. Neměnné identifikátor Udržovat vztah mezi službami AD DS a Azure AD. |
| Místo využívání |X |mechanickým vlastnost. Země daného uživatele. Používá se pro přiřazení licence. |
| userPrincipalName (Hlavní název uživatele) |X |UPN je přihlašovací ID uživatele. Nejčastěji používané jako [e-mailu] stejnou hodnotu. |

## <a name="exchange-online"></a>Exchange Online
| Název atributu | Uživatel | Kontakt | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definuje, jestli je účet povolený. |
| Pomocníka s nastavením |X |X | | |
| altRecipient |X | | |Vyžaduje Azure AD Connect 1.1.552.0 sestavení nebo po. |
| authOrig |X |X |X | |
| c |X |X | | |
| CN |X | |X | |
| co |X |X | | |
| Společnosti |X |X | | |
| CountryCode |X |X | | |
| Oddělení |X |X | | |
| description |X |X |X | |
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
| Telefon domů |X |X | | |
| informace |X |X |X |Tento atribut není v současné době spotřebovávat pro skupiny. |
| Iniciály |X |X | | |
| l |X |X | | |
| legacyExchangeDN |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manažer |X |X | | |
| člen | | |X | |
| Mobilní zařízení |X |X | | |
| msDS-HABSeniorityIndex |X |X |X | |
| msDS-PhoneticDisplayName |X |X |X | |
| msExchArchiveGUID |X | | | |
| msExchArchiveName |X | | | |
| msExchAssistantName |X |X | | |
| msExchAuditAdmin |X | | | |
| msExchAuditDelegate |X | | | |
| msExchAuditDelegateAdmin |X | | | |
| msExchAuditOwner |X | | | |
| msExchBlockedSendersHash |X |X | | |
| msExchBypassAudit |X | | | |
| msExchBypassModerationLink | | |X |K dispozici ve službě Azure AD Connect verze 1.1.524.0 |
| msExchCoManagedByLink | | |X | |
| msExchDelegateListLink |X | | | |
| msExchELCExpirySuspensionEnd |X | | | |
| msExchELCExpirySuspensionStart |X | | | |
| msExchELCMailboxFlags |X | | | |
| msExchEnableModeration |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |Tento atribut není aktuálně používané Exchange Online. |
| msExchExtensionCustomAttribute2 |X |X |X |Tento atribut není aktuálně používané Exchange Online. |
| msExchExtensionCustomAttribute3 |X |X |X |Tento atribut není aktuálně používané Exchange Online. |
| msExchExtensionCustomAttribute4 |X |X |X |Tento atribut není aktuálně používané Exchange Online. |
| msExchExtensionCustomAttribute5 |X |X |X |Tento atribut není aktuálně používané Exchange Online. |
| msExchHideFromAddressLists |X |X |X | |
| msExchImmutableID |X | | | |
| msExchLitigationHoldDate |X |X |X | |
| msExchLitigationHoldOwner |X |X |X | |
| msExchMailboxAuditEnable |X | | | |
| msExchMailboxAuditLogAgeLimit |X | | | |
| msExchMailboxGuid |X | | | |
| msExchModeratedByLink |X |X |X | |
| msExchModerationFlags |X |X |X | |
| msExchRecipientDisplayType |X |X |X | |
| msExchRecipientTypeDetails |X |X |X | |
| msExchRemoteRecipientType |X | | | |
| msExchRequireAuthToSendTo |X |X |X | |
| msExchResourceCapacity |X | | | |
| msExchResourceDisplay |X | | | |
| msExchResourceMetaData |X | | | |
| msExchResourceSearchProperties |X | | | |
| msExchRetentionComment |X |X |X | |
| msExchRetentionURL |X |X |X | |
| msExchSafeRecipientsHash |X |X | | |
| msExchSafeSendersHash |X |X | | |
| msExchSenderHintTranslations |X |X |X | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| msExchUserHoldPolicies |X | | | |
| msOrg IsOrganizational | | |X | |
| atributy objectSID |X | |X |mechanickým vlastnost. Identifikátor uživatele AD používá k udržení synchronizace mezi Azure AD a AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherTelephone |X |X | | |
| Stránkování |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| PSČ |X |X | | |
| proxyAddresses |X |X |X | |
| publicDelegates |X |X |X | |
| pwdLastSet |X | | |mechanickým vlastnost. Umožňuje vědět, kdy platnost již vydané tokeny. Používat synchronizaci hesla i federací. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| securityEnabled | | |X |Odvozený od groupType |
| sériové číslo |X |X | | |
| sourceAnchor |X |X |X |mechanickým vlastnost. Neměnné identifikátor Udržovat vztah mezi službami AD DS a Azure AD. |
| St |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto nastavuje |X |X | | |
| název |X |X | | |
| unauthOrig |X |X |X | |
| Místo využívání |X | | |mechanickým vlastnost. Země daného uživatele. Používá se pro přiřazení licence. |
| userCertificate |X |X | | |
| userPrincipalName (Hlavní název uživatele) |X | | |UPN je přihlašovací ID uživatele. Nejčastěji používané jako [e-mailu] stejnou hodnotu. |
| userSMIMECertificates |X |X | | |
| wWWHomePage |X |X | | |

## <a name="sharepoint-online"></a>SharePoint Online
| Název atributu | Uživatel | Kontakt | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definuje, jestli je účet povolený. |
| authOrig |X |X |X | |
| c |X |X | | |
| CN |X | |X | |
| co |X |X | | |
| Společnosti |X |X | | |
| CountryCode |X |X | | |
| Oddělení |X |X | | |
| description |X |X |X | |
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
| hideDLMembership | | |X | |
| Telefon domů |X |X | | |
| informace |X |X |X | |
| Iniciály |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| mail |X |X |X | |
| mailnickname |X |X |X | |
| managedBy | | |X | |
| manažer |X |X | | |
| člen | | |X | |
| middleName |X |X | | |
| Mobilní zařízení |X |X | | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| atributy objectSID |X | |X |mechanickým vlastnost. Identifikátor uživatele AD používá k udržení synchronizace mezi Azure AD a AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherIpPhone |X |X | | |
| otherMobile |X |X | | |
| otherPager |X |X | | |
| otherTelephone |X |X | | |
| Stránkování |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| PSČ |X |X | | |
| postOfficeBox |X |X | |Tento atribut není aktuálně používané Sharepointu Online. |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mechanickým vlastnost. Umožňuje vědět, kdy platnost již vydané tokeny. Používá se synchronizace hodnot hash hesel, předávacího ověřování a federace. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| securityEnabled | | |X |Odvozený od groupType |
| sériové číslo |X |X | | |
| sourceAnchor |X |X |X |mechanickým vlastnost. Neměnné identifikátor Udržovat vztah mezi službami AD DS a Azure AD. |
| St |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto nastavuje |X |X | | |
| název |X |X | | |
| unauthOrig |X |X |X | |
| url |X |X | | |
| Místo využívání |X | | |mechanickým vlastnost. Země daného uživatele. Používá se pro přiřazení licence. |
| userPrincipalName (Hlavní název uživatele) |X | | |UPN je přihlašovací ID uživatele. Nejčastěji používané jako [e-mailu] stejnou hodnotu. |
| wWWHomePage |X |X | | |

## <a name="lync-online-subsequently-known-as-skype-for-business"></a>Lync Online (následně označované jako Skype pro firmy)
| Název atributu | Uživatel | Kontakt | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definuje, jestli je účet povolený. |
| c |X |X | | |
| CN |X | |X | |
| co |X |X | | |
| Společnosti |X |X | | |
| Oddělení |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X |X | |
| givenName |X |X | | |
| Telefon domů |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| mail |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manažer |X |X | | |
| člen | | |X | |
| Mobilní zařízení |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP-ApplicationOptions |X | | | |
| msRTCSIP-DeploymentLocator |X |X | | |
| msRTCSIP-Line |X |X | | |
| msRTCSIP-OptionFlags |X |X | | |
| msRTCSIP-OwnerUrn |X | | | |
| msRTCSIP-PrimaryUserAddress |X |X | | |
| msRTCSIP-UserEnabled |X |X | | |
| atributy objectSID |X | |X |mechanickým vlastnost. Identifikátor uživatele AD používá k udržení synchronizace mezi Azure AD a AD. |
| otherTelephone |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| PSČ |X |X | | |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mechanickým vlastnost. Umožňuje vědět, kdy platnost již vydané tokeny. Používá se synchronizace hodnot hash hesel, předávacího ověřování a federace. |
| securityEnabled | | |X |Odvozený od groupType |
| sériové číslo |X |X | | |
| sourceAnchor |X |X |X |mechanickým vlastnost. Neměnné identifikátor Udržovat vztah mezi službami AD DS a Azure AD. |
| St |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto nastavuje |X |X | | |
| název |X |X | | |
| Místo využívání |X | | |mechanickým vlastnost. Země daného uživatele. Používá se pro přiřazení licence. |
| userPrincipalName (Hlavní název uživatele) |X | | |UPN je přihlašovací ID uživatele. Nejčastěji používané jako [e-mailu] stejnou hodnotu. |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| Název atributu | Uživatel | Kontakt | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definuje, jestli je účet povolený. |
| CN |X | |X |Běžný název nebo alias. Nejčastěji předponu hodnotu [e-mailu]. |
| displayName |X |X |X |Řetězec, který představuje název často zobrazuje jako popisný název (křestní jméno příjmení). |
| mail |X |X |X |úplné e-mailovou adresu. |
| člen | | |X | |
| atributy objectSID |X | |X |mechanickým vlastnost. Identifikátor uživatele AD používá k udržení synchronizace mezi Azure AD a AD. |
| proxyAddresses |X |X |X |mechanickým vlastnost. Azure AD používá. Obsahuje všechny sekundární e-mailové adresy uživatele. |
| pwdLastSet |X | | |mechanickým vlastnost. Umožňuje vědět, kdy platnost již vydané tokeny. |
| securityEnabled | | |X |Odvozený od groupType. |
| sourceAnchor |X |X |X |mechanickým vlastnost. Neměnné identifikátor Udržovat vztah mezi službami AD DS a Azure AD. |
| Místo využívání |X | | |mechanickým vlastnost. Země daného uživatele. Používá se pro přiřazení licence. |
| userPrincipalName (Hlavní název uživatele) |X | | |Tento název UPN je přihlašovací ID uživatele. Nejčastěji používané jako [e-mailu] stejnou hodnotu. |

## <a name="intune"></a>Intune
| Název atributu | Uživatel | Kontakt | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definuje, jestli je účet povolený. |
| c |X |X | | |
| CN |X | |X | |
| description |X |X |X | |
| displayName |X |X |X | |
| mail |X |X |X | |
| mailnickname |X |X |X | |
| člen | | |X | |
| atributy objectSID |X | |X |mechanickým vlastnost. Identifikátor uživatele AD používá k udržení synchronizace mezi Azure AD a AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mechanickým vlastnost. Umožňuje vědět, kdy platnost již vydané tokeny. Používá se synchronizace hodnot hash hesel, předávacího ověřování a federace. |
| securityEnabled | | |X |Odvozený od groupType |
| sourceAnchor |X |X |X |mechanickým vlastnost. Neměnné identifikátor Udržovat vztah mezi službami AD DS a Azure AD. |
| Místo využívání |X | | |mechanickým vlastnost. Země daného uživatele. Používá se pro přiřazení licence. |
| userPrincipalName (Hlavní název uživatele) |X | | |UPN je přihlašovací ID uživatele. Nejčastěji používané jako [e-mailu] stejnou hodnotu. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Název atributu | Uživatel | Kontakt | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definuje, jestli je účet povolený. |
| c |X |X | | |
| CN |X | |X | |
| co |X |X | | |
| Společnosti |X |X | | |
| CountryCode |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| l |X |X | | |
| managedBy | | |X | |
| manažer |X |X | | |
| člen | | |X | |
| Mobilní zařízení |X |X | | |
| atributy objectSID |X | |X |mechanickým vlastnost. Identifikátor uživatele AD používá k udržení synchronizace mezi Azure AD a AD. |
| physicalDeliveryOfficeName |X |X | | |
| PSČ |X |X | | |
| preferredLanguage |X | | | |
| pwdLastSet |X | | |mechanickým vlastnost. Umožňuje vědět, kdy platnost již vydané tokeny. Používá se synchronizace hodnot hash hesel, předávacího ověřování a federace. |
| securityEnabled | | |X |Odvozený od groupType |
| sériové číslo |X |X | | |
| sourceAnchor |X |X |X |mechanickým vlastnost. Neměnné identifikátor Udržovat vztah mezi službami AD DS a Azure AD. |
| St |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| název |X |X | | |
| Místo využívání |X | | |mechanickým vlastnost. Země daného uživatele. Používá se pro přiřazení licence. |
| userPrincipalName (Hlavní název uživatele) |X | | |UPN je přihlašovací ID uživatele. Nejčastěji používané jako [e-mailu] stejnou hodnotu. |

## <a name="3rd-party-applications"></a>3. stran aplikace
Tato skupina je sadu atributů používat jako minimální atributy, které jsou potřebné pro obecné úlohy nebo aplikace. Můžete použít pro pracovní postup není uvedený v jiné části nebo aplikace jiných výrobců. Používá se explicitně pro následující:

* Yammer (využívá jenom uživatele)
* [Hybridní Business-to-Business (B2B) napříč organizací scénáře využívající spolupráci nabízí prostředky, jako je SharePoint](https://go.microsoft.com/fwlink/?LinkId=747036)

Tato skupina je sada atributů, které lze použít, pokud se nepoužívá v adresáři Azure AD pro podporu Office 365, Dynamics nebo Intune. Má malou sadu základních atributů.

| Název atributu | Uživatel | Kontakt | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definuje, jestli je účet povolený. |
| CN |X | |X | |
| displayName |X |X |X | |
| givenName |X |X | | |
| mail |X | |X | |
| managedBy | | |X | |
| mailNickName |X |X |X | |
| člen | | |X | |
| atributy objectSID |X | | |mechanickým vlastnost. Identifikátor uživatele AD používá k udržení synchronizace mezi Azure AD a AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mechanickým vlastnost. Umožňuje vědět, kdy platnost již vydané tokeny. Používá se synchronizace hodnot hash hesel, předávacího ověřování a federace. |
| sériové číslo |X |X | | |
| sourceAnchor |X |X |X |mechanickým vlastnost. Neměnné identifikátor Udržovat vztah mezi službami AD DS a Azure AD. |
| Místo využívání |X | | |mechanickým vlastnost. Země daného uživatele. Používá se pro přiřazení licence. |
| userPrincipalName (Hlavní název uživatele) |X | | |UPN je přihlašovací ID uživatele. Nejčastěji používané jako [e-mailu] stejnou hodnotu. |

## <a name="windows-10"></a>Windows 10
Windows 10 připojených k doméně computer(device) synchronizuje některé atributy do služby Azure AD. Další informace o scénářích najdete v tématu [připojení zařízení připojených k doméně do Azure AD pro Windows 10 dojde](../active-directory-azureadjoin-devices-group-policy.md). Tyto atributy synchronizovat vždy a Windows 10 se nezobrazí jako aplikace, kterou můžete zrušit výběr. Počítače s Windows 10 připojených k doméně je identifikován s atributem userCertificate naplněna.

| Název atributu | Zařízení | Poznámka |
| --- |:---:| --- |
| accountEnabled |X | |
| deviceTrustType |X |Hodnota pevně zakódované pro počítače připojené k doméně. |
| displayName |X | |
| ms-DS-CreatorSID |X |Zkratka registeredOwnerReference. |
| objectGUID |X |Také se označují jako ID zařízení. |
| atributy objectSID |X |Zkratka onPremisesSecurityIdentifier. |
| operačního systému |X |Zkratka deviceOSType. |
| operatingSystemVersion |X |Zkratka deviceOSVersion. |
| userCertificate |X | |

Tyto atributy pro **uživatele** jsou kromě jiných aplikací, které jste vybrali.  

| Název atributu | Uživatel | Poznámka |
| --- |:---:| --- |
| domainFQDN |X |Zkratka Název_domény_dns. Např. contoso.com. |
| domainNetBios |X |Zkratka netBiosName. Například CONTOSO. |
| msDS-KeyCredentialLink |X |Jakmile se uživatel zaregistruje do Windows Hello pro firmy. | 

## <a name="exchange-hybrid-writeback"></a>Zpětný zápis hybridní Exchange
Tyto atributy jsou zapsány zpět z Azure AD do místní služby Active Directory vyberete povolit **hybridní Exchange**. V závislosti na vaší verzi systému Exchange může být méně atributy synchronizovat.

| Atribut názvu (připojit uživatelského rozhraní) |Atribut názvu (v místním AD) | Uživatel | Kontakt | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |---|
| msDS-ExternalDirectoryObjectID| ms-DS-External-Directory-Object-Id |X | | |Odvozený od cloudAnchor ve službě Azure AD. Tento atribut je novinkou systému Exchange 2016 a Windows Server 2016 AD. |
| msExchArchiveStatus| ms-Exch-ArchiveStatus |X | | |Online archivu: Umožňuje zákazníkům archivovat e-mailu. |
| msExchBlockedSendersHash| ms-Exch-BlockedSendersHash |X | | |Filtrování: Zapíše místní filtrování a online bezpečné a zablokování odesílatele data od klientů. |
| msExchSafeRecipientsHash| ms-Exch-SafeRecipientsHash  |X | | |Filtrování: Zapíše místní filtrování a online bezpečné a zablokování odesílatele data od klientů. |
| msExchSafeSendersHash| ms-Exch-SafeSendersHash  |X | | |Filtrování: Zapíše místní filtrování a online bezpečné a zablokování odesílatele data od klientů. |
| msExchUCVoiceMailSettings| ms-Exch-UCVoiceMailSettings |X | | |Povolit Unified Messaging (UM) - Online hlasová pošta: používá Microsoft Lync Server integrace, mají-li aplikace Lync serveru místní, že uživatel má hlasová pošta v online službách. |
| msExchUserHoldPolicies| ms-Exc-hUserHoldPolicies |X | | |Blokování sporů: Cloudové služby umožňuje určit, kteří uživatelé jsou v rámci soudního sporu uchování. |
| proxyAddresses| proxyAddresses |X |X |X |Pouze x500 vložili adresu z Exchange Online. |
| publicDelegates| ms-Exch-Public-Delegates  |X | | |Umožňuje poštovní schránku Exchange Online, chcete-li být udělena práva SendOnBehalfTo uživatelům s poštovní schránku v místním systému Exchange. Vyžaduje Azure AD Connect 1.1.552.0 sestavení nebo po. |

## <a name="exchange-mail-public-folder"></a>Veřejné složky pošty Exchange
Tyto atributy jsou synchronizované z místní služby Active Directory do služby Azure AD, když vyberete povolit **veřejné složky pošty Exchange**.

| Název atributu | PublicFolder | Poznámka |
| --- | :---:| --- |
| displayName | X |  |
| mail | X |  |
| msExchRecipientTypeDetails | X |  |
| objectGUID | X |  |
| proxyAddresses | X |  |
| targetAddress | X |  |

## <a name="device-writeback"></a>Zpětný zápis zařízení
Objekty zařízení se vytvoří ve službě Active Directory. Tyto objekty mohou být zařízení připojená k Azure AD nebo počítače s Windows 10 připojených k doméně.

| Název atributu | Zařízení | Poznámka |
| --- |:---:| --- |
| altSecurityIdentities |X | |
| displayName |X | |
| rozlišující název |X | |
| msDS-CloudAnchor |X | |
| msDS-DeviceID |X | |
| msDS-DeviceObjectVersion |X | |
| msDS-DeviceOSType |X | |
| msDS-DeviceOSVersion |X | |
| msDS-DevicePhysicalIDs |X | |
| msDS-KeyCredentialLink |X |Jenom se schématem systému Windows Server 2016 AD |
| msDS-IsCompliant |X | |
| msDS-IsEnabled |X | |
| ismanaged msDS – |X | |
| msDS-RegisteredOwner |X | |

## <a name="notes"></a>Poznámky
* Při použití atributu alternativní ID, místní atribut userPrincipalName je synchronizován s onPremisesUserPrincipalName atribut Azure AD. Atributu alternativní ID, například e-mailu, se synchronizuje s Azure AD atribut userPrincipalName.
* V seznamu výše, typ objektu **uživatele** platí také pro typ objektu **iNetOrgPerson**.

## <a name="next-steps"></a>Další postup
Další informace o [synchronizace Azure AD Connect](how-to-connect-sync-whatis.md) konfigurace.

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).

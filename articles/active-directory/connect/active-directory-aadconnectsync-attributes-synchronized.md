---
title: Atributy se synchronizují přes Azure AD Connect | Microsoft Docs
description: Obsahuje seznam atributy, které jsou synchronizovány do Azure Active Directory.
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
ms.openlocfilehash: 4009a35226473cd3ecc68929e53d70f7fb749156
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593431"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Synchronizace Azure AD Connect: atributy se synchronizují do Azure Active Directory
Toto téma obsahuje seznam atributy, které jsou synchronizovány pomocí synchronizace Azure AD Connect.  
Atributy jsou seskupené podle Azure související aplikace AD.

## <a name="attributes-to-synchronize"></a>Atributy pro synchronizaci
Je běžné otázky *co je v seznamu minimální atributů k synchronizaci*. Výchozí a doporučený postup je potřeba zachovat výchozí atributy, takže konstruovat úplné GAL (seznamu pro globální adresa) v cloudu a získat všechny funkce v úlohami Office 365. V některých případech jsou některé atributy, které vaše organizace nechce synchronizované do cloudu vzhledem k tomu, že tyto atributy obsahují citlivé nebo PII data (identifikovatelné osobní údaje), jako jsou v tomto příkladu:  
![Chybný atributy](./media/active-directory-aadconnectsync-attributes-synchronized/badextensionattribute.png)

V takovém případě začínat v seznamu atributů v tomto tématu a identifikujte ty atributy, které by obsahovat velká a malá písmena nebo PII data a nelze synchronizovat. Poté zrušte výběr těchto atributů během instalace pomocí [aplikaci Azure AD a filtrování atributů](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering).

> [!WARNING]
> Při výběru atributy, by měl buďte opatrní a pouze zrušte výběr těchto atributů absolutně není možné synchronizovat. Ostatní atributy unselecting může mít negativní dopad na funkce.
>
>

## <a name="office-365-proplus"></a>Office 365 ProPlus
| Název atributu | Uživatel | Poznámka |
| --- |:---:| --- |
| accountEnabled |X |Určuje, zda je povoleno účet. |
| CN |X | |
| displayName |X | |
| objectSID |X |mechanických vlastnost. Identifikátor uživatele AD používá k udržení synchronizaci mezi Azure AD a AD. |
| pwdLastSet |X |mechanických vlastnost. Používá se ke zjištění, kdy zneplatní již vystavené tokeny. Používá synchronizace hodnot hash hesel, předávací ověřování a federace. |
| sourceAnchor |X |mechanických vlastnost. Neměnné identifikátor Udržovat vztah mezi přidá a Azure AD. |
| usageLocation |X |mechanických vlastnost. Země daného uživatele. Použít pro přiřazení licence. |
| userPrincipalName |X |UPN je přihlašovací Identifikátor uživatele. Nejčastěji jako [e-mailu] stejnou hodnotu. |

## <a name="exchange-online"></a>Exchange Online
| Název atributu | Uživatel | Kontakt | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Určuje, zda je povoleno účet. |
| Pomocník pro |X |X | | |
| altRecipient |X | | |Vyžaduje Azure AD Connect sestavení 1.1.552.0 nebo po. |
| authOrig |X |X |X | |
| c |X |X | | |
| CN |X | |X | |
| co |X |X | | |
| Společnosti |X |X | | |
| CountryCode |X |X | | |
| oddělení |X |X | | |
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
| informace |X |X |X |Tento atribut není aktuálně využité pro skupiny. |
| Iniciály |X |X | | |
| l |X |X | | |
| legacyExchangeDN |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manažer |X |X | | |
| člen | | |X | |
| mobilní |X |X | | |
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
| msExchExtensionCustomAttribute1 |X |X |X |Tento atribut není aktuálně spotřebovávána Exchange Online. |
| msExchExtensionCustomAttribute2 |X |X |X |Tento atribut není aktuálně spotřebovávána Exchange Online. |
| msExchExtensionCustomAttribute3 |X |X |X |Tento atribut není aktuálně spotřebovávána Exchange Online. |
| msExchExtensionCustomAttribute4 |X |X |X |Tento atribut není aktuálně spotřebovávána Exchange Online. |
| msExchExtensionCustomAttribute5 |X |X |X |Tento atribut není aktuálně spotřebovávána Exchange Online. |
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
| objectSID |X | |X |mechanických vlastnost. Identifikátor uživatele AD používá k udržení synchronizaci mezi Azure AD a AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherTelephone |X |X | | |
| Pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| PSČ |X |X | | |
| proxyAddresses |X |X |X | |
| publicDelegates |X |X |X | |
| pwdLastSet |X | | |mechanických vlastnost. Používá se ke zjištění, kdy zneplatní již vystavené tokeny. Používat synchronizaci hesla i federací. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| securityEnabled | | |X |Odvozené z groupType |
| sn |X |X | | |
| sourceAnchor |X |X |X |mechanických vlastnost. Neměnné identifikátor Udržovat vztah mezi přidá a Azure AD. |
| St |X |X | | |
| StreetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| název |X |X | | |
| unauthOrig |X |X |X | |
| usageLocation |X | | |mechanických vlastnost. Země daného uživatele. Použít pro přiřazení licence. |
| userCertificate |X |X | | |
| userPrincipalName |X | | |UPN je přihlašovací Identifikátor uživatele. Nejčastěji jako [e-mailu] stejnou hodnotu. |
| userSMIMECertificates |X |X | | |
| wWWHomePage |X |X | | |

## <a name="sharepoint-online"></a>SharePoint Online
| Název atributu | Uživatel | Kontakt | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Určuje, zda je povoleno účet. |
| authOrig |X |X |X | |
| c |X |X | | |
| CN |X | |X | |
| co |X |X | | |
| Společnosti |X |X | | |
| CountryCode |X |X | | |
| oddělení |X |X | | |
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
| iniciály |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| E-mailu |X |X |X | |
| mailnickname |X |X |X | |
| managedBy | | |X | |
| manažer |X |X | | |
| člen | | |X | |
| middleName |X |X | | |
| mobilní |X |X | | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| objectSID |X | |X |mechanických vlastnost. Identifikátor uživatele AD používá k udržení synchronizaci mezi Azure AD a AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherIpPhone |X |X | | |
| otherMobile |X |X | | |
| otherPager |X |X | | |
| otherTelephone |X |X | | |
| Pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| PSČ |X |X | | |
| postOfficeBox |X |X | |Tento atribut není aktuálně spotřebovávána SharePoint Online. |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mechanických vlastnost. Používá se ke zjištění, kdy zneplatní již vystavené tokeny. Používá synchronizace hodnot hash hesel, předávací ověřování a federace. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| securityEnabled | | |X |Odvozené z groupType |
| sn |X |X | | |
| sourceAnchor |X |X |X |mechanických vlastnost. Neměnné identifikátor Udržovat vztah mezi přidá a Azure AD. |
| St |X |X | | |
| StreetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| název |X |X | | |
| unauthOrig |X |X |X | |
| url |X |X | | |
| usageLocation |X | | |mechanických vlastnost. Země daného uživatele. Použít pro přiřazení licence. |
| userPrincipalName |X | | |UPN je přihlašovací Identifikátor uživatele. Nejčastěji jako [e-mailu] stejnou hodnotu. |
| wWWHomePage |X |X | | |

## <a name="lync-online-subsequently-known-as-skype-for-business"></a>Aplikace Lync Online (následně označované jako Skype pro firmy)
| Název atributu | Uživatel | Kontakt | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Určuje, zda je povoleno účet. |
| c |X |X | | |
| CN |X | |X | |
| co |X |X | | |
| Společnosti |X |X | | |
| oddělení |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X |X | |
| givenName |X |X | | |
| Telefon domů |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| E-mailu |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manažer |X |X | | |
| člen | | |X | |
| mobilní |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP-ApplicationOptions |X | | | |
| msRTCSIP-DeploymentLocator |X |X | | |
| msRTCSIP-Line |X |X | | |
| msRTCSIP-OptionFlags |X |X | | |
| msRTCSIP-OwnerUrn |X | | | |
| msRTCSIP-PrimaryUserAddress |X |X | | |
| msRTCSIP-UserEnabled |X |X | | |
| objectSID |X | |X |mechanických vlastnost. Identifikátor uživatele AD používá k udržení synchronizaci mezi Azure AD a AD. |
| otherTelephone |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| PSČ |X |X | | |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mechanických vlastnost. Používá se ke zjištění, kdy zneplatní již vystavené tokeny. Používá synchronizace hodnot hash hesel, předávací ověřování a federace. |
| securityEnabled | | |X |Odvozené z groupType |
| sn |X |X | | |
| sourceAnchor |X |X |X |mechanických vlastnost. Neměnné identifikátor Udržovat vztah mezi přidá a Azure AD. |
| St |X |X | | |
| StreetAddress |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| název |X |X | | |
| usageLocation |X | | |mechanických vlastnost. Země daného uživatele. Použít pro přiřazení licence. |
| userPrincipalName |X | | |UPN je přihlašovací Identifikátor uživatele. Nejčastěji jako [e-mailu] stejnou hodnotu. |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| Název atributu | Uživatel | Kontakt | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Určuje, zda je povoleno účet. |
| CN |X | |X |Běžné jméno nebo alias. Předpona, nejčastěji [e-mailu] hodnoty. |
| displayName |X |X |X |Řetězec, který představuje název často zobrazené jako popisný název (křestní jméno příjmení). |
| E-mailu |X |X |X |úplné e-mailovou adresu. |
| člen | | |X | |
| objectSID |X | |X |mechanických vlastnost. Identifikátor uživatele AD používá k udržení synchronizaci mezi Azure AD a AD. |
| proxyAddresses |X |X |X |mechanických vlastnost. Používá Azure AD. Obsahuje všechny sekundární e-mailové adresy pro uživatele. |
| pwdLastSet |X | | |mechanických vlastnost. Používá se ke zjištění, kdy zneplatní již vystavené tokeny. |
| securityEnabled | | |X |Odvozená od groupType. |
| sourceAnchor |X |X |X |mechanických vlastnost. Neměnné identifikátor Udržovat vztah mezi přidá a Azure AD. |
| usageLocation |X | | |mechanických vlastnost. Země daného uživatele. Použít pro přiřazení licence. |
| userPrincipalName |X | | |Tento název UPN je přihlašovací Identifikátor uživatele. Nejčastěji jako [e-mailu] stejnou hodnotu. |

## <a name="intune"></a>Intune
| Název atributu | Uživatel | Kontakt | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Určuje, zda je povoleno účet. |
| c |X |X | | |
| CN |X | |X | |
| description |X |X |X | |
| displayName |X |X |X | |
| E-mailu |X |X |X | |
| mailnickname |X |X |X | |
| člen | | |X | |
| objectSID |X | |X |mechanických vlastnost. Identifikátor uživatele AD používá k udržení synchronizaci mezi Azure AD a AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mechanických vlastnost. Používá se ke zjištění, kdy zneplatní již vystavené tokeny. Používá synchronizace hodnot hash hesel, předávací ověřování a federace. |
| securityEnabled | | |X |Odvozené z groupType |
| sourceAnchor |X |X |X |mechanických vlastnost. Neměnné identifikátor Udržovat vztah mezi přidá a Azure AD. |
| usageLocation |X | | |mechanických vlastnost. Země daného uživatele. Použít pro přiřazení licence. |
| userPrincipalName |X | | |UPN je přihlašovací Identifikátor uživatele. Nejčastěji jako [e-mailu] stejnou hodnotu. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Název atributu | Uživatel | Kontakt | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Určuje, zda je povoleno účet. |
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
| mobilní |X |X | | |
| objectSID |X | |X |mechanických vlastnost. Identifikátor uživatele AD používá k udržení synchronizaci mezi Azure AD a AD. |
| physicalDeliveryOfficeName |X |X | | |
| PSČ |X |X | | |
| preferredLanguage |X | | | |
| pwdLastSet |X | | |mechanických vlastnost. Používá se ke zjištění, kdy zneplatní již vystavené tokeny. Používá synchronizace hodnot hash hesel, předávací ověřování a federace. |
| securityEnabled | | |X |Odvozené z groupType |
| sn |X |X | | |
| sourceAnchor |X |X |X |mechanických vlastnost. Neměnné identifikátor Udržovat vztah mezi přidá a Azure AD. |
| St |X |X | | |
| StreetAddress |X |X | | |
| telephoneNumber |X |X | | |
| název |X |X | | |
| usageLocation |X | | |mechanických vlastnost. Země daného uživatele. Použít pro přiřazení licence. |
| userPrincipalName |X | | |UPN je přihlašovací Identifikátor uživatele. Nejčastěji jako [e-mailu] stejnou hodnotu. |

## <a name="3rd-party-applications"></a>3. stran aplikace
Tato skupina je sada atributy používané jako minimální atributy, které jsou potřebné pro obecné zatížení nebo aplikace. Můžete použít pro úlohu není uvedený v jinou část nebo pro aplikaci jiného výrobce než Microsoftu. Použije se explicitně pro následující:

* Yammer (pouze uživatele se využívá)
* [Hybridní Business-to-Business (B2B) mezi org spolupráce scénáře nabízené prostředkům, jako jsou služby SharePoint](http://go.microsoft.com/fwlink/?LinkId=747036)

Tato skupina je sada atributů, které lze použít, pokud adresář Azure AD se nepoužije pro podporu Office 365, Dynamics nebo Intune. Má malou sadu atributů jádra.

| Název atributu | Uživatel | Kontakt | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Určuje, zda je povoleno účet. |
| CN |X | |X | |
| displayName |X |X |X | |
| givenName |X |X | | |
| E-mailu |X | |X | |
| managedBy | | |X | |
| mailNickName |X |X |X | |
| člen | | |X | |
| objectSID |X | | |mechanických vlastnost. Identifikátor uživatele AD používá k udržení synchronizaci mezi Azure AD a AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mechanických vlastnost. Používá se ke zjištění, kdy zneplatní již vystavené tokeny. Používá synchronizace hodnot hash hesel, předávací ověřování a federace. |
| sn |X |X | | |
| sourceAnchor |X |X |X |mechanických vlastnost. Neměnné identifikátor Udržovat vztah mezi přidá a Azure AD. |
| usageLocation |X | | |mechanických vlastnost. Země daného uživatele. Použít pro přiřazení licence. |
| userPrincipalName |X | | |UPN je přihlašovací Identifikátor uživatele. Nejčastěji jako [e-mailu] stejnou hodnotu. |

## <a name="windows-10"></a>Windows 10
Windows 10 připojených k doméně computer(device) synchronizuje některých atributů do služby Azure AD. Další informace o scénářích najdete v tématu [připojení zařízení připojených k doméně ke službě Azure AD pro Windows 10 vyskytne](../active-directory-azureadjoin-devices-group-policy.md). Tyto atributy provádět synchronizaci a Windows 10 se nezobrazí jako aplikace, které můžete zrušit zaškrtnutí. Počítač připojený k doméně Windows 10 bude označen tak, že userCertificate atribut naplněno.

| Název atributu | Zařízení | Poznámka |
| --- |:---:| --- |
| accountEnabled |X | |
| deviceTrustType |X |Hodnota pevně zakódované pro počítače připojené k doméně. |
| displayName |X | |
| ms-DS-CreatorSID |X |Také se nazývá registeredOwnerReference. |
| objectGUID |X |Zkratka deviceID. |
| objectSID |X |Také se nazývá onPremisesSecurityIdentifier. |
| operačního systému |X |Také se nazývá deviceOSType. |
| operatingSystemVersion |X |Také se nazývá deviceOSVersion. |
| userCertificate |X | |

Tyto atributy pro **uživatele** jsou kromě dalších aplikací, které jste vybrali.  

| Název atributu | Uživatel | Poznámka |
| --- |:---:| --- |
| domainFQDN |X |Zkratka Název_domény_dns. Například contoso.com. |
| domainNetBios |X |Volá se také název pro rozhraní NetBIOS. Například CONTOSO. |

## <a name="exchange-hybrid-writeback"></a>Zpětný zápis hybridní Exchange
Tyto atributy se zapisují zpět z Azure AD do místní služby Active Directory při výběru povolit **Exchange hybridní**. V závislosti na vaší verzi Exchange méně atributy synchronizováni.

| Atribut Name (připojit uživatelského rozhraní) |Atribut Name (místní AD) | Uživatel | Kontakt | Skupina | Poznámka |
| --- |:---:|:---:|:---:| --- |---|
| msDS-ExternalDirectoryObjectID| ms-DS-External-Directory-Object-Id |X | | |Odvozená od cloudAnchor ve službě Azure AD. Tento atribut je nového v Exchange 2016 a Windows Server 2016 AD. |
| msExchArchiveStatus| ms-Exch-ArchiveStatus |X | | |Online archivu: Umožňuje zákazníkům k archivaci e-mailu. |
| msExchBlockedSendersHash| ms-Exch-BlockedSendersHash |X | | |Filtrování: Zapíše místní filtrování a online bezpečné a blokovaných odesílatele data od klientů. |
| msExchSafeRecipientsHash| ms-Exch-SafeRecipientsHash  |X | | |Filtrování: Zapíše místní filtrování a online bezpečné a blokovaných odesílatele data od klientů. |
| msExchSafeSendersHash| ms-Exch-SafeSendersHash  |X | | |Filtrování: Zapíše místní filtrování a online bezpečné a blokovaných odesílatele data od klientů. |
| msExchUCVoiceMailSettings| ms-Exch-UCVoiceMailSettings |X | | |Povolit Unified Messaging (UM) – Online hlasová pošta: Microsoft Lync Server používá integrace a informuje Lync serveru místní, že má uživatel hlasová pošta v online službách. |
| msExchUserHoldPolicies| ms-Exc-hUserHoldPolicies |X | | |Uchování soudního sporu: Umožňuje cloudové služby k určení uživatelů, kteří jsou pod uložení soudního sporu. |
| proxyAddresses| proxyAddresses |X |X |X |Pouze x500 vloží adresu z Exchange Online. |
| publicDelegates| ms-Exch-Public-Delegates  |X | | |Umožňuje poštovní schránku systému Exchange Online k udělení oprávnění SendOnBehalfTo uživatelům s místní poštovní schránka systému Exchange. Vyžaduje Azure AD Connect sestavení 1.1.552.0 nebo po. |

## <a name="exchange-mail-public-folder"></a>Veřejné složky e-mailu Exchange
Tyto atributy se synchronizují z místní služby Active Directory do Azure AD, když vyberete volbu Povolit **veřejné složky e-mailu Exchange**.

| Název atributu | PublicFolder | Poznámka |
| --- | :---:| --- |
| displayName | X |  |
| E-mailu | X |  |
| msExchRecipientTypeDetails | X |  |
| objectGUID | X |  |
| proxyAddresses | X |  |
| targetAddress | X |  |

## <a name="device-writeback"></a>Zpětný zápis zařízení
Objekty zařízení jsou vytvořeny ve službě Active Directory. Tyto objekty lze zařízení připojených ke službě Azure AD nebo připojený k doméně počítače s Windows 10.

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
| msDS-ismanaged – |X | |
| msDS-RegisteredOwner |X | |

## <a name="notes"></a>Poznámky
* Při použití atributu alternativní ID, místní atribut userPrincipalName je synchronizován s onPremisesUserPrincipalName atributů Azure AD. Atributu alternativní ID, například e-mailu, je synchronizován s Azure AD atribut userPrincipalName.
* V seznamu výš, typ objektu **uživatele** platí také pro typ objektu **iNetOrgPerson**.

## <a name="next-steps"></a>Další postup
Další informace o [synchronizace Azure AD Connect](active-directory-aadconnectsync-whatis.md) konfigurace.

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md).

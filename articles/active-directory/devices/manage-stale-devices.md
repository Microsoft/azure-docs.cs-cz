---
title: How to manage stale devices in Azure AD | Microsoft Docs
description: Learn how to remove stale devices from your database of registered devices in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1829c56f9804c5aa808461db98a5048d63f55446
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207283"
---
# <a name="how-to-manage-stale-devices-in-azure-ad"></a>How To: Manage stale devices in Azure AD

Registrace zařízení, která už nejsou zapotřebí, měla se kvůli dokončení jejich životního cyklu měla v ideálním případě zrušit. Avšak například kvůli ztraceným, odcizeným a poškozeným zařízením nebo přeinstalaci operačního systému zpravidla ve svém prostředí máte zastaralá zařízení. Jako správce IT budete zřejmě potřebovat nějakou metodu pro odebrání zastaralých zařízení, abyste se mohli soustředit na správu zařízení, která to opravdu potřebují.

V tomto článku se dozvíte, jak efektivně spravovat zastaralá zařízení ve vašem prostředí.
  

## <a name="what-is-a-stale-device"></a>Co je zastaralé zařízení?

Zastaralé zařízení je zařízení, které bylo zaregistrováno ve službě Azure AD, ale po určitou dobu se nepoužívá pro přístup ke cloudovým aplikacím. Zastaralá zařízení ovlivňují vaši schopnost spravovat a podporovat zařízení a uživatele v tenantovi, protože: 

- Duplicitní zařízení mohou pracovníkům helpdesku znesnadnit identifikaci, které zařízení je aktuálně aktivní.
- An increased number of devices creates unnecessary device writebacks increasing the time for Azure AD connect syncs.
- Kvůli obecnému udržení pořádku a dodržování předpisů potřebujete znát přesný stav zařízení. 

Zastaralá zařízení ve službě Azure AD mohou kolidovat s obecnými zásadami životního cyklu zařízení ve vaší organizaci.

## <a name="detect-stale-devices"></a>Detekce zastaralých zařízení

Protože se zastaralé zařízení definuje jako registrované zařízení, které se po určitou dobu nepoužívá pro přístup ke cloudovým aplikacím, vyžaduje detekce zastaralých zařízení nějakou vlastnost související s časovým razítkem. Ve službě Azure AD má tato vlastnost název **ApproximateLastLogonTimestamp**, neboli **časové razítko aktivity**. Pokud rozdíl mezi nynějškem a hodnotou **časového razítka aktivity** překročí dobu, kterou jste definovali pro aktivní zařízení, považuje se zařízení za zastaralé. Toto **časové razítko aktivity** je teď ve veřejné verzi Preview.

## <a name="how-is-the-value-of-the-activity-timestamp-managed"></a>Jak se hodnota časového razítka aktivity spravuje?  

Vyhodnocení časového razítka aktivity se aktivuje při pokusu o ověření zařízení. Služba Azure AD časové razítko aktivity vyhodnocuje, když:

- A Conditional Access policies requiring [managed devices](../conditional-access/require-managed-devices.md) or [approved client apps](../conditional-access/app-based-conditional-access.md) has been triggered.
- Jsou v síti aktivní zařízení s Windows 10, která jsou připojená k Azure AD nebo hybridně připojená k Azure AD. 
- Zařízení spravovaná přes Intune se přihlásí k této službě.

If the delta between the existing value of the activity timestamp and the current value is more than 14 days (+/-5 day variance), the existing value is replaced with the new value.

## <a name="how-do-i-get-the-activity-timestamp"></a>Jak získám časové razítko aktivity?

Ke získání hodnoty časového razítka aktivity máte dvě možnosti:

- Sloupec **Aktivita** na [stránce se zařízeními](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) na webu Azure Portal

    ![Časové razítko aktivity](./media/manage-stale-devices/01.png)

- Rutina [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0)

    ![Časové razítko aktivity](./media/manage-stale-devices/02.png)

## <a name="plan-the-cleanup-of-your-stale-devices"></a>Plánování úklidu zastaralých zařízení

Pokud chcete ve svém prostředí efektivně uklidit zastaralá zařízení, měli byste definovat související zásady. Tyto zásady vám pomohou zohlednit všechny aspekty, které souvisejí se zastaralými zařízeními. V následujících oddílech najdete příklady častých aspektů při vytváření těchto zásad. 

### <a name="cleanup-account"></a>Úklidový účet

K aktualizaci zařízení ve službě Azure AD potřebujete účet, který má přiřazenu jednu z následujících rolí:

- Globální správce
- Cloud Device Administrator
- Správce služby Intune

V zásadách úklidu vyberte účty, které mají přiřazené požadované role. 

### <a name="timeframe"></a>Časový rámec

Definujte časové období, které je ukazatelem zastaralého zařízení. When defining your timeframe, factor the window noted for updating the activity timestamp into your value. For example, you shouldn't consider a timestamp that is younger than 21 days (includes variance) as an indicator for a stale device. V určitých situacích se zařízení může jevit jako zastaralé, přestože není. Vlastník takového zařízení může být například na dovolené nebo na nemocenské,  která přesahuje časové období pro zastaralá zařízení.

### <a name="disable-devices"></a>Zakázání zařízení

Zařízení, které se jeví jako zastaralé, není vhodné okamžitě odstranit, protože v případě falešně pozitivního výsledku nemůžete odstranění vrátit zpět. Osvědčeným postupem je zakázat zařízení po určité období odkladu předtím, než ho odstraníte. V zásadách definujte časové období, po které bude zařízení před odstraněním zakázané.

### <a name="mdm-controlled-devices"></a>Zařízení pod kontrolou správy mobilních zařízení (MDM)

Pokud je zařízení pod kontrolou Intune nebo jiných řešení MDM, před zakázáním nebo odstraněním vyřaďte zařízení z tohoto systému správy.

### <a name="system-managed-devices"></a>Zařízení spravovaná systémem

Neodstraňujte zařízení spravovaná systémem. Jedná se obecně o zařízení, jako je například autopilot. Once deleted, these devices can't be reprovisioned. Nová rutina `get-msoldevice` standardně vylučuje zařízení spravovaná systémem. 

### <a name="hybrid-azure-ad-joined-devices"></a>Hybridní zařízení připojená k Azure AD

Zařízení hybridně připojená k Azure AD by měla dodržovat zásady pro správu místních zastaralých zařízení. 

Službu Azure AD uklidíte takto:

- **Zařízení s Windows 10** – zakažte nebo odstraňte zařízení s Windows 10 v místní službě AD a nechejte nástroj Azure AD Connect synchronizovat stav změněných zařízení se službou Azure AD.
- **Windows 7/8** - Disable or delete Windows 7/8 devices in your on-premises AD first. K zakázání nebo odstranění zařízení s Windows 7/8 ve službě Azure AD nemůžete použít Azure AD Connect. Instead, when you make the change in your on-premises, you must disable/delete in Azure AD.

> [!NOTE]
>* Deleting devices in your on-premises AD or Azure AD does not remove registration on the client. It will only prevent access to resources using device as an identity (e.g. Conditional Access). Read additional information on how to [remove registration on the client](faq.md#hybrid-azure-ad-join-faq).
>* Deleting a Windows 10 device only in Azure AD will re-synchronize the device from your on-premises using Azure AD connect but as a new object in "Pending" state. A re-registration is required on the device.
>* Removing the device from sync scope for Windows 10/Server 2016 devices will delete the Azure AD device. Adding it back to sync scope will place a new object in "Pending" state. A re-registration of the device is required.
>* If you not using Azure AD Connect for Windows 10 devices to synchronize (e.g. ONLY using AD FS for registration), you must manage lifecycle similar to Windows 7/8 devices.


### <a name="azure-ad-joined-devices"></a>Zařízení připojená k Azure AD

Zakažte nebo odstraňte zařízení připojená k Azure AD ve službě Azure AD.

> [!NOTE]
>* Deleting an Azure AD device does not remove registration on the client. It will only prevent access to resources using device as an identity (e.g Conditional Access). 
>* Read more on [how to unjoin on Azure AD](faq.md#azure-ad-join-faq) 

### <a name="azure-ad-registered-devices"></a>Zařízení zaregistrovaná v Azure AD

Zakažte nebo odstraňte zařízení zaregistrovaná v Azure AD ve službě Azure AD.

> [!NOTE]
>* Deleting an Azure AD registered device in Azure AD does not remove registration on the client. It will only prevent access to resources using device as an identity (e.g. Conditional Access).
>* Read more on [how to remove a registration on the client](faq.md#azure-ad-register-faq)

## <a name="clean-up-stale-devices-in-the-azure-portal"></a>Vymazání zastaralých zařízení na webu Azure Portal  

I když zastaralá zařízení můžete uklidit na webu Azure Portal, je efektivnější použít k tomuto účelu powershellový skript. Pomocí nejnovějšího modulu PowerShell V1 můžete použít filtr časového razítka a odfiltrovat zařízení spravovaná systémem, například autopilot. V tento okamžik není doporučeno používat PowerShell V2.

Typická rutina se skládá z následujících kroků:

1. Připojení ke službě Azure Active Directory pomocí rutiny [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0)
1. Získání seznamu zařízení
1. Zakázání zařízení pomocí rutiny [Disable-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/disable-msoldevice?view=azureadps-1.0) 
1. Před odstraněním zařízení vyčkejte po období odkladu, jehož délku ve dnech si zvolíte.
1. Odebrání zařízení pomocí rutiny [Remove-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/remove-msoldevice?view=azureadps-1.0)

### <a name="get-the-list-of-devices"></a>Získání seznamu zařízení

Všechna zařízení získáte a vrácená data uložíte do souboru CSV takto:

```PowerShell
Get-MsolDevice -all | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, Approxi
mateLastLogonTimestamp | export-csv devicelist-summary.csv
```

If you have a large number of devices in your directory, use the timestamp filter to narrow down the number of returned devices. Všechna zařízení s časovým razítkem starším než určité datum a vrácená data uložíte do souboru CSV takto: 

```PowerShell
$dt = [datetime]’2017/01/01’
Get-MsolDevice -all -LogonTimeBefore $dt | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, ApproximateLastLogonTimestamp | export-csv devicelist-olderthan-Jan-1-2017-summary.csv
```

## <a name="what-you-should-know"></a>Co byste měli vědět

### <a name="why-is-the-timestamp-not-updated-more-frequently"></a>Proč se časové razítko neaktualizuje častěji?

Časové razítko se aktualizuje kvůli podpoře životního cyklu zařízení. Nejedná se o audit. Pokud o zařízení potřebujete častější aktualizace, použijte protokoly auditu přihlašování.

### <a name="why-should-i-worry-about-my-bitlocker-keys"></a>Proč si mám dělat starosti o klíče nástroje BitLocker?

Pokud je to nakonfigurováno, jsou klíče nástroje BitLocker pro zařízení s Windows 10 uložené v objektu zařízení služby Azure AD. Když odstraníte zastaralé zařízení, odstraníte také klíče nástroje Bitlocker, které jsou uložené v tomto zařízení. Před odstraněním zastaralého zařízení byste měli určit, jestli jsou zásady úklidu v souladu s životním cyklem zařízení. 

### <a name="why-should-i-worry-about-windows-autopilot-devices"></a>Why should I worry about Windows Autopilot devices?

When a Azure AD device was associated with a Windows Autopilot object the following three scenarios can occur if the device will be repurposed in future:
- With Windows Autopilot user-driven deployments without using white glove, a new Azure AD device will be created, but it won’t be tagged with the ZTDID.
- With Windows Autopilot self-deploying mode deployments, they will fail because an associate Azure AD device cannot be found.  (This is a security mechanism to make sure that no “imposter” devices try to join Azure AD with no credentials.) The failure will indicate a ZTDID mismatch.
- With Windows Autopilot white glove deployments, they will fail because an associated Azure AD device cannot be found. (Behind the scenes, white glove deployments use the same self-deploying mode process, so they enforce the same security mechanisms.)

### <a name="how-do-i-know-all-the-type-of-devices-joined"></a>Jak poznám všechny typy připojených zařízení?

Další informace o různých typech najdete v [přehledu správy zařízení](overview.md).

### <a name="what-happens-when-i-disable-a-device"></a>Co se stane, když zařízení zakážu?

Všechna ověřování, kdy se zařízení používá k ověřování vůči službě Azure AD, budou odepřena. Obvyklými příklady jsou:

- **Zařízení hybridně připojená k Azure AD** – uživatelé mohou zařízení používat pro přihlášení k místní doméně. Nemají ale přístup k prostředkům Azure AD, jako je Office 365.
- **Zařízení připojená k Azure AD** – uživatelé nemůžou používat zařízení k přihlášení. 
- **Mobilní zařízení** – uživatel nemá přístup k prostředkům Azure AD, jako je Office 365. 

## <a name="next-steps"></a>Další kroky

Přehled správy zařízení na webu Azure Portal najdete v tématu věnovaném [správě zařízení pomocí webu Azure Portal](device-management-azure-portal.md).

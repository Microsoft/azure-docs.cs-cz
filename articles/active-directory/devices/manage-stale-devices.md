---
title: Jak spravovat zastaralá zařízení ve službě Azure AD | Dokumenty společnosti Microsoft
description: Zjistěte, jak odebrat zastaralá zařízení z databáze registrovaných zařízení ve službě Azure Active Directory.
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
ms.openlocfilehash: 46be728216ed4b9c9e84c1c7f68c5ddf2051f42b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672305"
---
# <a name="how-to-manage-stale-devices-in-azure-ad"></a>Postup: Správa zastaralých zařízení ve službě Azure AD

Registrace zařízení, která už nejsou zapotřebí, měla se kvůli dokončení jejich životního cyklu měla v ideálním případě zrušit. Avšak například kvůli ztraceným, odcizeným a poškozeným zařízením nebo přeinstalaci operačního systému zpravidla ve svém prostředí máte zastaralá zařízení. Jako správce IT budete zřejmě potřebovat nějakou metodu pro odebrání zastaralých zařízení, abyste se mohli soustředit na správu zařízení, která to opravdu potřebují.

V tomto článku se dozvíte, jak efektivně spravovat zastaralá zařízení ve vašem prostředí.
  

## <a name="what-is-a-stale-device"></a>Co je zastaralé zařízení?

Zastaralé zařízení je zařízení, které bylo zaregistrováno ve službě Azure AD, ale po určitou dobu se nepoužívá pro přístup ke cloudovým aplikacím. Zastaralá zařízení ovlivňují vaši schopnost spravovat a podporovat zařízení a uživatele v tenantovi, protože: 

- Duplicitní zařízení mohou pracovníkům helpdesku znesnadnit identifikaci, které zařízení je aktuálně aktivní.
- Zvýšený počet zařízení vytváří zbytečné zpětné zápisy zařízení, které zvyšují dobu synchronizace připojení Azure AD.
- Kvůli obecnému udržení pořádku a dodržování předpisů potřebujete znát přesný stav zařízení. 

Zastaralá zařízení ve službě Azure AD mohou kolidovat s obecnými zásadami životního cyklu zařízení ve vaší organizaci.

## <a name="detect-stale-devices"></a>Detekce zastaralých zařízení

Protože se zastaralé zařízení definuje jako registrované zařízení, které se po určitou dobu nepoužívá pro přístup ke cloudovým aplikacím, vyžaduje detekce zastaralých zařízení nějakou vlastnost související s časovým razítkem. Ve službě Azure AD má tato vlastnost název **ApproximateLastLogonTimestamp**, neboli **časové razítko aktivity**. Pokud rozdíl mezi nynějškem a hodnotou **časového razítka aktivity** překročí dobu, kterou jste definovali pro aktivní zařízení, považuje se zařízení za zastaralé. Toto **časové razítko aktivity** je teď ve veřejné verzi Preview.

## <a name="how-is-the-value-of-the-activity-timestamp-managed"></a>Jak se hodnota časového razítka aktivity spravuje?  

Vyhodnocení časového razítka aktivity se aktivuje při pokusu o ověření zařízení. Služba Azure AD časové razítko aktivity vyhodnocuje, když:

- Byly aktivovány zásady podmíněného přístupu, které vyžadují [spravovaná zařízení](../conditional-access/require-managed-devices.md) nebo [schválené klientské aplikace.](../conditional-access/app-based-conditional-access.md)
- Jsou v síti aktivní zařízení s Windows 10, která jsou připojená k Azure AD nebo hybridně připojená k Azure AD. 
- Zařízení spravovaná přes Intune se přihlásí k této službě.

Pokud je rozdíl mezi existující hodnotou časového razítka aktivity a aktuální hodnotou větší než 14 dní (odchylka +/-5 dnů), bude stávající hodnota nahrazena novou hodnotou.

## <a name="how-do-i-get-the-activity-timestamp"></a>Jak získám časové razítko aktivity?

Ke získání hodnoty časového razítka aktivity máte dvě možnosti:

- Sloupec **Aktivita** na [stránce se zařízeními](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) na webu Azure Portal

    ![Časové razítko aktivity](./media/manage-stale-devices/01.png)

- Rutina [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0)

    ![Časové razítko aktivity](./media/manage-stale-devices/02.png)

## <a name="plan-the-cleanup-of-your-stale-devices"></a>Plánování úklidu zastaralých zařízení

Pokud chcete ve svém prostředí efektivně uklidit zastaralá zařízení, měli byste definovat související zásady. Tyto zásady vám pomohou zohlednit všechny aspekty, které souvisejí se zastaralými zařízeními. V následujících oddílech najdete příklady častých aspektů při vytváření těchto zásad. 

### <a name="cleanup-account"></a>Úklidový účet

K aktualizaci zařízení ve službě Azure AD potřebujete účet, který má přiřazenu jednu z následujících rolí:

- Globální správce
- Správce cloudových zařízení
- Správce služby Intune

V zásadách úklidu vyberte účty, které mají přiřazené požadované role. 

### <a name="timeframe"></a>Časové období

Definujte časové období, které je ukazatelem zastaralého zařízení. Při definování časového rámce počítejte s oknem, které bylo zaznamenáno pro aktualizaci časového razítka aktivity na hodnotu. Například byste neměli považovat časové razítko, které je mladší než 21 dní (včetně odchylky) jako indikátor pro zastaralé zařízení. V určitých situacích se zařízení může jevit jako zastaralé, přestože není. Vlastník takového zařízení může být například na dovolené nebo na nemocenské,  která přesahuje časové období pro zastaralá zařízení.

### <a name="disable-devices"></a>Zakázání zařízení

Zařízení, které se jeví jako zastaralé, není vhodné okamžitě odstranit, protože v případě falešně pozitivního výsledku nemůžete odstranění vrátit zpět. Osvědčeným postupem je zakázat zařízení po určité období odkladu předtím, než ho odstraníte. V zásadách definujte časové období, po které bude zařízení před odstraněním zakázané.

### <a name="mdm-controlled-devices"></a>Zařízení pod kontrolou správy mobilních zařízení (MDM)

Pokud je zařízení pod kontrolou Intune nebo jiných řešení MDM, před zakázáním nebo odstraněním vyřaďte zařízení z tohoto systému správy.

### <a name="system-managed-devices"></a>Zařízení spravovaná systémem

Neodstraňujte zařízení spravovaná systémem. Obecně se jedná o zařízení, jako je autopilot. Po odstranění nelze tato zařízení znovu zřídit. Nová rutina `get-msoldevice` standardně vylučuje zařízení spravovaná systémem. 

### <a name="hybrid-azure-ad-joined-devices"></a>Hybridní zařízení připojená k Azure AD

Zařízení hybridně připojená k Azure AD by měla dodržovat zásady pro správu místních zastaralých zařízení. 

Službu Azure AD uklidíte takto:

- **Zařízení s Windows 10** – zakažte nebo odstraňte zařízení s Windows 10 v místní službě AD a nechejte nástroj Azure AD Connect synchronizovat stav změněných zařízení se službou Azure AD.
- **Windows 7/8** – Nejprve zakažte nebo odstraňte zařízení se systémem Windows 7/8 v místním prostředí služby AD. K zakázání nebo odstranění zařízení s Windows 7/8 ve službě Azure AD nemůžete použít Azure AD Connect. Místo toho při změně v místním prostředí, musíte zakázat nebo odstranit ve službě Azure AD.

> [!NOTE]
>* Odstranění zařízení ve vašem místním ad nebo Azure AD neodebere registraci na straně klienta. Zabrání pouze přístupk prostředkům pomocí zařízení jako identity (např. podmíněný přístup). Přečtěte si další informace o tom, jak [odebrat registraci na straně klienta](faq.md#hybrid-azure-ad-join-faq).
>* Odstranění zařízení s Windows 10 jenom ve službě Azure AD bude znovu synchronizovat zařízení z vašeho místního prostředí pomocí připojení Azure AD, ale jako nový objekt ve stavu Čekající na vyřízení. Na zařízení je nutná opětovná registrace.
>* Odebráním zařízení z oboru synchronizace pro zařízení s Windows 10/Server 2016 odstraníte zařízení Azure AD. Přidání zpět do oboru synchronizace umístí nový objekt ve stavu Čeká na vyřízení. Je nutná opětovná registrace zařízení.
>* Pokud k synchronizaci zařízení Azure AD Connect pro Windows 10 nepoužíváte (například jenom pomocí služby AD FS pro registraci), musíte spravovat životní cyklus podobný zařízením s Windows 7/8.


### <a name="azure-ad-joined-devices"></a>Zařízení připojená k Azure AD

Zakažte nebo odstraňte zařízení připojená k Azure AD ve službě Azure AD.

> [!NOTE]
>* Odstranění zařízení Azure AD neodebere registraci na straně klienta. Zabrání pouze přístupu k prostředkům, které používají zařízení jako identitu (např. podmíněný přístup). 
>* Další informace o [tom, jak se ve službě Azure AD odpojit](faq.md#azure-ad-join-faq) 

### <a name="azure-ad-registered-devices"></a>Zařízení zaregistrovaná v Azure AD

Zakažte nebo odstraňte zařízení zaregistrovaná v Azure AD ve službě Azure AD.

> [!NOTE]
>* Odstranění zařízení registrovaného na Azure AD ve službě Azure AD neodebere registraci na straně klienta. Zabrání pouze přístupk prostředkům pomocí zařízení jako identity (např. podmíněný přístup).
>* Přečtěte si více [o tom, jak odstranit registraci na straně klienta](faq.md#azure-ad-register-faq)

## <a name="clean-up-stale-devices-in-the-azure-portal"></a>Vymazání zastaralých zařízení na webu Azure Portal  

I když zastaralá zařízení můžete uklidit na webu Azure Portal, je efektivnější použít k tomuto účelu powershellový skript. Pomocí nejnovějšího modulu PowerShell V1 můžete použít filtr časového razítka a odfiltrovat zařízení spravovaná systémem, jako je autopilot. V tento okamžik není doporučeno používat PowerShell V2.

Typická rutina se skládá z následujících kroků:

1. Připojení ke službě Azure Active Directory pomocí rutiny [Connect-MsolService](/powershell/module/msonline/connect-msolservice?view=azureadps-1.0)
1. Získání seznamu zařízení
1. Zakázání zařízení pomocí rutiny [Disable-MsolDevice](/powershell/module/msonline/disable-msoldevice?view=azureadps-1.0) 
1. Před odstraněním zařízení vyčkejte po období odkladu, jehož délku ve dnech si zvolíte.
1. Odebrání zařízení pomocí rutiny [Remove-MsolDevice](/powershell/module/msonline/remove-msoldevice?view=azureadps-1.0)

### <a name="get-the-list-of-devices"></a>Získání seznamu zařízení

Všechna zařízení získáte a vrácená data uložíte do souboru CSV takto:

```PowerShell
Get-MsolDevice -all | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, Approxi
mateLastLogonTimestamp | export-csv devicelist-summary.csv
```

Pokud máte v adresáři velký počet zařízení, použijte filtr časového razítka k zúžení počtu vrácených zařízení. Všechna zařízení s časovým razítkem starším než určité datum a vrácená data uložíte do souboru CSV takto: 

```PowerShell
$dt = [datetime]’2017/01/01’
Get-MsolDevice -all -LogonTimeBefore $dt | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, ApproximateLastLogonTimestamp | export-csv devicelist-olderthan-Jan-1-2017-summary.csv
```

## <a name="what-you-should-know"></a>Co byste měli vědět

### <a name="why-is-the-timestamp-not-updated-more-frequently"></a>Proč se časové razítko neaktualizuje častěji?

Časové razítko se aktualizuje kvůli podpoře životního cyklu zařízení. Nejedná se o audit. Pokud o zařízení potřebujete častější aktualizace, použijte protokoly auditu přihlašování.

### <a name="why-should-i-worry-about-my-bitlocker-keys"></a>Proč si mám dělat starosti o klíče nástroje BitLocker?

Pokud je to nakonfigurováno, jsou klíče nástroje BitLocker pro zařízení s Windows 10 uložené v objektu zařízení služby Azure AD. Když odstraníte zastaralé zařízení, odstraníte také klíče nástroje Bitlocker, které jsou uložené v tomto zařízení. Před odstraněním zastaralého zařízení byste měli určit, jestli jsou zásady úklidu v souladu s životním cyklem zařízení. 

### <a name="why-should-i-worry-about-windows-autopilot-devices"></a>Proč bych se měl starat o zařízení Windows Autopilot?

Když zařízení Azure AD byla přidružena k objektu Autopilot systému Windows následující tři scénáře může dojít, pokud zařízení bude repurposed v budoucnu:
- S nasazeními Windows Autopilot řízenými uživateli bez použití bílých rukavic se vytvoří nové zařízení Azure AD, ale nebude označeno ztdid.
- S Windows Autopilot vlastní nasazení režimu nasazení, se nezdaří, protože přidružené zařízení Azure AD nelze najít.  (Toto je mechanismus zabezpečení, který zajistí, že žádná "podvodná" zařízení se pokusí připojit azure ad bez přihlašovacích údajů.) Selhání bude znamenat neshodu ZTDID.
- S windows autopilot bílé rukavice nasazení, se nezdaří, protože přidružené zařízení Azure AD nelze najít. (V zákulisí používají nasazení bílých rukavic stejný proces vlastního nasazování, takže vynucují stejné mechanismy zabezpečení.)

### <a name="how-do-i-know-all-the-type-of-devices-joined"></a>Jak poznám všechny typy připojených zařízení?

Další informace o různých typech najdete v [přehledu správy zařízení](overview.md).

### <a name="what-happens-when-i-disable-a-device"></a>Co se stane, když zařízení zakážu?

Všechna ověřování, kdy se zařízení používá k ověřování vůči službě Azure AD, budou odepřena. Obvyklými příklady jsou:

- **Zařízení hybridně připojená k Azure AD** – uživatelé mohou zařízení používat pro přihlášení k místní doméně. Nemají ale přístup k prostředkům Azure AD, jako je Office 365.
- **Zařízení připojená k Azure AD** – uživatelé nemůžou používat zařízení k přihlášení. 
- **Mobilní zařízení** – uživatel nemá přístup k prostředkům Azure AD, jako je Office 365. 

## <a name="next-steps"></a>Další kroky

Přehled správy zařízení na webu Azure Portal najdete v tématu věnovaném [správě zařízení pomocí webu Azure Portal](device-management-azure-portal.md).

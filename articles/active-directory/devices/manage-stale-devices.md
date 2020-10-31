---
title: Jak spravovat zastaralá zařízení v Azure AD | Microsoft Docs
description: Přečtěte si, jak z databáze registrovaných zařízení z Azure Active Directory odebrat zastaralá zařízení.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7651f84e14d6ea7dcb4e12d57e2bf494d5aeff1e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93083180"
---
# <a name="how-to-manage-stale-devices-in-azure-ad"></a>Postupy: Správa zastaralých zařízení v Azure AD

Registrace zařízení, která už nejsou zapotřebí, měla se kvůli dokončení jejich životního cyklu měla v ideálním případě zrušit. Avšak například kvůli ztraceným, odcizeným a poškozeným zařízením nebo přeinstalaci operačního systému zpravidla ve svém prostředí máte zastaralá zařízení. Jako správce IT budete zřejmě potřebovat nějakou metodu pro odebrání zastaralých zařízení, abyste se mohli soustředit na správu zařízení, která to opravdu potřebují.

V tomto článku se dozvíte, jak efektivně spravovat zastaralá zařízení ve vašem prostředí.
  

## <a name="what-is-a-stale-device"></a>Co je zastaralé zařízení?

Zastaralé zařízení je zařízení, které bylo zaregistrováno ve službě Azure AD, ale po určitou dobu se nepoužívá pro přístup ke cloudovým aplikacím. Zastaralá zařízení ovlivňují vaši schopnost spravovat a podporovat zařízení a uživatele v tenantovi, protože: 

- Duplicitní zařízení mohou pracovníkům helpdesku znesnadnit identifikaci, které zařízení je aktuálně aktivní.
- Větší počet zařízení vytváří zbytečné buněky zařízení, které zvyšují čas pro synchronizaci služby Azure AD Connect.
- Kvůli obecnému udržení pořádku a dodržování předpisů potřebujete znát přesný stav zařízení. 

Zastaralá zařízení ve službě Azure AD mohou kolidovat s obecnými zásadami životního cyklu zařízení ve vaší organizaci.

## <a name="detect-stale-devices"></a>Detekce zastaralých zařízení

Protože se zastaralé zařízení definuje jako registrované zařízení, které se po určitou dobu nepoužívá pro přístup ke cloudovým aplikacím, vyžaduje detekce zastaralých zařízení nějakou vlastnost související s časovým razítkem. Ve službě Azure AD má tato vlastnost název **ApproximateLastLogonTimestamp** , neboli **časové razítko aktivity** . Pokud rozdíl mezi nynějškem a hodnotou **časového razítka aktivity** překročí dobu, kterou jste definovali pro aktivní zařízení, považuje se zařízení za zastaralé. Toto **časové razítko aktivity** je teď ve veřejné verzi Preview.

## <a name="how-is-the-value-of-the-activity-timestamp-managed"></a>Jak se hodnota časového razítka aktivity spravuje?  

Vyhodnocení časového razítka aktivity se aktivuje při pokusu o ověření zařízení. Služba Azure AD časové razítko aktivity vyhodnocuje, když:

- Aktivovaly se zásady podmíněného přístupu vyžadující [spravovaná zařízení](../conditional-access/require-managed-devices.md) nebo [schválené klientské aplikace](../conditional-access/app-based-conditional-access.md) .
- Jsou v síti aktivní zařízení s Windows 10, která jsou připojená k Azure AD nebo hybridně připojená k Azure AD. 
- Zařízení spravovaná přes Intune se přihlásí k této službě.

Pokud rozdíl mezi existující hodnotou časového razítka aktivity a aktuální hodnotou je více než 14 dní (odchylka od 5 dnů), stávající hodnota se nahradí novou hodnotou.

## <a name="how-do-i-get-the-activity-timestamp"></a>Jak získám časové razítko aktivity?

Ke získání hodnoty časového razítka aktivity máte dvě možnosti:

- Sloupec **Aktivita** na [stránce se zařízeními](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) na webu Azure Portal

    :::image type="content" source="./media/manage-stale-devices/01.png" alt-text="Snímek obrazovky stránky v Azure Portal výpis názvu, vlastníka a dalších informací o zařízení. V jednom sloupci se zobrazuje časové razítko aktivity." border="false":::

- Rutina [Get-AzureADDevice](/powershell/module/azuread/Get-AzureADDevice)

    :::image type="content" source="./media/manage-stale-devices/02.png" alt-text="Snímek obrazovky stránky v Azure Portal výpis názvu, vlastníka a dalších informací o zařízení. V jednom sloupci se zobrazuje časové razítko aktivity." border="false":::

## <a name="plan-the-cleanup-of-your-stale-devices"></a>Plánování úklidu zastaralých zařízení

Pokud chcete ve svém prostředí efektivně uklidit zastaralá zařízení, měli byste definovat související zásady. Tyto zásady vám pomohou zohlednit všechny aspekty, které souvisejí se zastaralými zařízeními. V následujících oddílech najdete příklady častých aspektů při vytváření těchto zásad. 

### <a name="cleanup-account"></a>Úklidový účet

K aktualizaci zařízení ve službě Azure AD potřebujete účet, který má přiřazenu jednu z následujících rolí:

- Globální správce
- Správce cloudového zařízení
- Správce služby Intune

V zásadách úklidu vyberte účty, které mají přiřazené požadované role. 

### <a name="timeframe"></a>Časové období

Definujte časové období, které je ukazatelem zastaralého zařízení. Při definování časového rámce přihlaste okno, ve kterém se aktualizuje časové razítko aktivity, na svou hodnotu. Například byste neměli považovat časové razítko mladší než 21 dní (zahrnuje odchylku) jako indikátor pro zastaralé zařízení. V určitých situacích se zařízení může jevit jako zastaralé, přestože není. Vlastník takového zařízení může být například na dovolené nebo na nemocenské,  která přesahuje časové období pro zastaralá zařízení.

### <a name="disable-devices"></a>Zakázání zařízení

Zařízení, které se jeví jako zastaralé, není vhodné okamžitě odstranit, protože v případě falešně pozitivního výsledku nemůžete odstranění vrátit zpět. Osvědčeným postupem je zakázat zařízení po určité období odkladu předtím, než ho odstraníte. V zásadách definujte časové období, po které bude zařízení před odstraněním zakázané.

### <a name="mdm-controlled-devices"></a>Zařízení pod kontrolou správy mobilních zařízení (MDM)

Pokud je zařízení pod kontrolou Intune nebo jiných řešení MDM, před zakázáním nebo odstraněním vyřaďte zařízení z tohoto systému správy.

### <a name="system-managed-devices"></a>Zařízení spravovaná systémem

Neodstraňujte zařízení spravovaná systémem. Jedná se o obecně zařízení, jako je například autopilot. Po odstranění nebude možné tato zařízení znovu zřídit. Nová rutina `Get-AzureADDevice` standardně vylučuje zařízení spravovaná systémem. 

### <a name="hybrid-azure-ad-joined-devices"></a>Hybridní zařízení připojená k Azure AD

Zařízení hybridně připojená k Azure AD by měla dodržovat zásady pro správu místních zastaralých zařízení. 

Službu Azure AD uklidíte takto:

- **Zařízení s Windows 10** – zakažte nebo odstraňte zařízení s Windows 10 v místní službě AD a nechejte nástroj Azure AD Connect synchronizovat stav změněných zařízení se službou Azure AD.
- **Windows 7/8** – nejdřív zakažte nebo odstraňte zařízení s Windows 7/8 v místní službě AD. K zakázání nebo odstranění zařízení s Windows 7/8 ve službě Azure AD nemůžete použít Azure AD Connect. Pokud provedete změnu v místním prostředí, musíte v Azure AD zakázat nebo odstranit.

> [!NOTE]
>* Při odstraňování zařízení v místní službě AD nebo Azure AD nedojde k odebrání registrace na klientovi. Znemožní přístup k prostředkům pomocí zařízení jako identity (např. podmíněný přístup). Přečtěte si další informace o tom, jak [Odebrat registraci na klientovi](faq.md#hybrid-azure-ad-join-faq).
>* Odstranění zařízení s Windows 10 v Azure AD znovu synchronizuje zařízení z místního prostředí pomocí služby Azure AD Connect, ale jako nový objekt ve stavu čeká na vyřízení. V zařízení se vyžaduje nová registrace.
>* Odebrání zařízení z oboru synchronizace pro zařízení s Windows 10/serverem 2016 odstraní zařízení Azure AD. Přidáním zpátky do oboru synchronizace dojde k umístění nového objektu do stavu čeká na vyřízení. Vyžaduje se opakovaná registrace zařízení.
>* Pokud nepoužíváte Azure AD Connect pro synchronizaci zařízení s Windows 10 (například jenom pomocí AD FS k registraci), musíte pro správu životního cyklu podobně jako u zařízení s Windows 7/8.


### <a name="azure-ad-joined-devices"></a>Zařízení připojená k Azure AD

Zakažte nebo odstraňte zařízení připojená k Azure AD ve službě Azure AD.

> [!NOTE]
>* Odstraněním zařízení Azure AD se nepovedlo odebrat registraci na klientovi. Znemožní přístup k prostředkům pomocí zařízení jako identity (např. podmíněný přístup). 
>* Další informace o [odpojování v Azure AD](faq.md#azure-ad-join-faq) 

### <a name="azure-ad-registered-devices"></a>Zařízení zaregistrovaná v Azure AD

Zakažte nebo odstraňte zařízení zaregistrovaná v Azure AD ve službě Azure AD.

> [!NOTE]
>* Odstraněním zařízení registrovaného službou Azure AD ve službě Azure AD nedojde k odebrání registrace na klientovi. Znemožní přístup k prostředkům pomocí zařízení jako identity (např. podmíněný přístup).
>* Další informace o [tom, jak odebrat registraci na klientovi](faq.md#azure-ad-register-faq)

## <a name="clean-up-stale-devices-in-the-azure-portal"></a>Vymazání zastaralých zařízení na webu Azure Portal  

I když zastaralá zařízení můžete uklidit na webu Azure Portal, je efektivnější použít k tomuto účelu powershellový skript. Použijte nejnovější modul PowerShell V1 k použití filtru časových razítek a k odfiltrování zařízení spravovaných systémem, jako je třeba Automatický pilot. V tento okamžik není doporučeno používat PowerShell V2.

Typická rutina se skládá z následujících kroků:

1. Připojení k Azure Active Directory pomocí rutiny [Connect-AzureAD](/powershell/module/azuread/connect-azuread)
1. Získání seznamu zařízení
1. Zakažte zařízení pomocí rutiny [set-AzureADDevice](/powershell/module/azuread/Set-AzureADDevice) (zakázat pomocí možnosti-AccountEnabled). 
1. Před odstraněním zařízení vyčkejte po období odkladu, jehož délku ve dnech si zvolíte.
1. Odeberte zařízení pomocí rutiny [Remove-AzureADDevice](/powershell/module/azuread/Remove-AzureADDevice) .

### <a name="get-the-list-of-devices"></a>Získání seznamu zařízení

Všechna zařízení získáte a vrácená data uložíte do souboru CSV takto:

```PowerShell
Get-AzureADDevice -All:$true | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, ApproximateLastLogonTimestamp | export-csv devicelist-summary.csv
```

Pokud máte ve svém adresáři velký počet zařízení, použijte filtr časových razítek k zúžení počtu vrácených zařízení. Všechna zařízení s časovým razítkem starším než určité datum a vrácená data uložíte do souboru CSV takto: 

```PowerShell
$dt = [datetime]’2017/01/01’
Get-AzureADDevice | Where {$_.ApproximateLastLogonTimeStamp -le $dt} | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, ApproximateLastLogonTimestamp | export-csv devicelist-olderthan-Jan-1-2017-summary.csv
```

## <a name="what-you-should-know"></a>Co byste měli vědět

### <a name="why-is-the-timestamp-not-updated-more-frequently"></a>Proč se časové razítko neaktualizuje častěji?

Časové razítko se aktualizuje kvůli podpoře životního cyklu zařízení. Nejedná se o audit. Pokud o zařízení potřebujete častější aktualizace, použijte protokoly auditu přihlašování.

### <a name="why-should-i-worry-about-my-bitlocker-keys"></a>Proč si mám dělat starosti o klíče nástroje BitLocker?

Pokud je to nakonfigurováno, jsou klíče nástroje BitLocker pro zařízení s Windows 10 uložené v objektu zařízení služby Azure AD. Když odstraníte zastaralé zařízení, odstraníte také klíče nástroje Bitlocker, které jsou uložené v tomto zařízení. Před odstraněním zastaralého zařízení byste měli určit, jestli jsou zásady úklidu v souladu s životním cyklem zařízení. 

### <a name="why-should-i-worry-about-windows-autopilot-devices"></a>Proč se mám starat o zařízení s Windows autopilotem?

Když odstraníte zařízení Azure AD, které bylo přidruženo k objektu Windows autopilotu, může dojít k následujícím třem scénářům, pokud bude zařízení v budoucnu účelné:
- U uživatelských nasazení řízených pomocí Windows autopilotu bez použití předběžného zřizování se vytvoří nové zařízení Azure AD, které ale nebude označené ZTDID.
- S nasazením autopilotního režimu automatického nasazení Windows se nezdaří, protože nejde najít přidružení zařízení Azure AD.  (Toto je bezpečnostní mechanismus, který zajistí, že se zařízení bez jakýchkoli přihlašovacích údajů pokusí připojit k Azure AD.) Selhání bude označovat neshodu ZTDID.
- S předběžnými nasazeními Windows autopilotu se nezdaří, protože se nepovedlo najít přidružené zařízení Azure AD. (Na pozadí předběžných nasazení používá stejný proces režimu samoobslužného nasazení, aby vynutila stejné mechanismy zabezpečení.)

### <a name="how-do-i-know-all-the-type-of-devices-joined"></a>Jak poznám všechny typy připojených zařízení?

Další informace o různých typech najdete v [přehledu správy zařízení](overview.md).

### <a name="what-happens-when-i-disable-a-device"></a>Co se stane, když zařízení zakážu?

Všechna ověřování, kdy se zařízení používá k ověřování vůči službě Azure AD, budou odepřena. Obvyklými příklady jsou:

- **Hybridní zařízení připojené k Azure AD** – uživatelé můžou být schopni použít zařízení k přihlášení do své místní domény. Nemůžou ale získat přístup k prostředkům Azure AD, jako je Microsoft 365.
- **Zařízení připojená k Azure AD** – uživatelé nemůžou používat zařízení k přihlášení. 
- **Mobilní zařízení** – uživatel nemá přístup k prostředkům služby Azure AD, jako je Microsoft 365. 

## <a name="next-steps"></a>Další kroky

Přehled správy zařízení na webu Azure Portal najdete v tématu věnovaném [správě zařízení pomocí webu Azure Portal](device-management-azure-portal.md).

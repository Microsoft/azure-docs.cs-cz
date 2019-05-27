---
title: Konfigurace ověřování relace správy s podmíněným přístupem Azure Active Directory
description: Vlastní konfigurace relací ověřování Azure AD, včetně frekvence a prohlížeč trvalost relace přihlášení uživatele.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8897de5ee86d20e52b948f21afaef4acf196539
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988583"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Konfigurace ověřování relace správy s podmíněným přístupem

V komplexních nasazení organizace pravděpodobně potřeba omezovat relace ověřování. Některé scénáře patří:

* Přístup k prostředkům z nespravovaný nebo sdíleného zařízení
* Přístup k citlivým informacím z externí síť
* Vysoký dopad na uživatele
* Důležitých podnikových aplikací

Řízení podmíněného přístupu umožňují vytvářet zásady, které se zaměřují konkrétní případy použití v rámci vaší organizace bez ovlivnění všech uživatelů.

Před všemi zúčastněnými stranami podrobnosti o tom, jak nakonfigurovat zásady, Podívejme se na výchozí konfiguraci.

## <a name="user-sign-in-frequency"></a>Frekvence přihlášení uživatele

Frekvence přihlášení definuje časové období před opakovaným uživateli se zobrazí výzva k přihlášení znovu pokusí o přístup k prostředku.

Výchozí konfigurace služby Azure Active Directory (Azure AD) pro přihlášení uživatele v možnosti četnost je kumulativní okno 90 dnů. S žádostí uživatele o přihlašovací údaje často vypadá jako rozumné věc, kterou provedete, ale můžete zdařit: uživatelé, kteří jsou trénované k zadání přihlašovacích údajů bez přemýšlení můžou neúmyslně vydávat výzvu k zadání pověření škodlivé.

Může být zvuk, odpuzujícím žádat není pro uživatele se znovu přihlásit po dobu 90 dnů, ve skutečnosti jakékoliv porušení zásady IT odvolají relace. Příklady zahrnují (ale nejsou omezeny) změnu hesla, nekompatibilního zařízení nebo účet zakázat. Můžete také explicitně [odvolat uživatelské relace powershellu](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Výchozí konfigurace služby Azure AD se vrátí k "neptat uživatelům zadávat přihlašovací údaje, pokud nedošlo ke změně stavu zabezpečení jejich relací".

Nastavení četnosti přihlašování funguje s aplikacemi, které jste implementovali OATH2 nebo OIDC protokoly podle normy. Většina Microsoft nativní aplikace pro Windows, Mac a mobilní zařízení v souladu s nastavením.

## <a name="persistence-of-browsing-sessions"></a>Trvalost relace procházení

Relace prohlížeče trvalé umožňuje uživatelům zůstal přihlášen i po zavření a znovuotevření jejich okno prohlížeče.

Výchozí nastavení Azure AD pro trvalost relace prohlížeče umožňuje uživatelům na osobních zařízeních zvolit, jestli se má zachovat relace zobrazením "Pobytu přihlášení?" výzvu se po úspěšném ověření. Pokud trvalost prohlížeč konfigurován ve službě AD FS pomocí pokynů v článku [jednotné přihlašování – nastavení služby AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
), budeme v souladu s touto zásadou a zachovat Azure AD přednášce. Můžete také nakonfigurovat, jestli uživatelé ve vašem tenantovi, naleznete v tématu "Pobytu přihlášení?" příkazový řádek tak, že změníte příslušné nastavení v podokně webu Azure Portal pomocí pokynů v článku brandingu [přizpůsobit přihlašovací stránku Azure AD](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Konfigurace řízení relace ověřování

Podmíněný přístup je schopnost Azure AD Premium a vyžaduje licenci premium. Pokud chcete další informace o podmíněném přístupu, přečtěte si téma [co je podmíněný přístup v Azure Active Directory?](overview.md#license-requirements)

> [!WARNING]
> Pokud používáte [konfigurovatelné životnost tokenu](../develop/active-directory-configurable-token-lifetimes.md) funkce aktuálně ve verzi public preview, mějte prosím na paměti, že nepodporujeme vytvoříte dva různé zásady pro stejného uživatele nebo aplikaci kombinaci: jednu s tuto funkci a jinou s Funkce konfigurovat doba platnosti tokenu. Společnost Microsoft plánuje vyřadit z provozu funkci konfigurovatelné doby života tokenů na 15. října a nahraďte ji metodou funkce podmíněného přístupu ověřování relace správy.  

### <a name="policy-1-sign-in-frequency-control"></a>Zásady 1: Řízení přihlašování frekvence

1. Vytvořit novou zásadu
1. Vyberte všechny požadované podmínky pro prostředí zákazníků, včetně cílové cloudové aplikace.

   > [!NOTE]
   > Doporučuje se nastavení četnosti výzvy stejné ověřování pro klíčové aplikace Microsoft Office Exchange Online a SharePoint Online pro nejlepší uživatelské prostředí.

1. Přejděte na **ovládacích prvků přístupu** > **relace** a klikněte na tlačítko **frekvence přihlášení**
1. Do prvního textového pole zadejte požadovaná hodnota dnů a hodin
1. Vyberte hodnotu **hodin** nebo **dnů** z rozevíracího seznamu
1. Uložení zásady

![Zásady podmíněného přístupu, které jsou nakonfigurované pro přihlášení v možnosti četnost](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

V Azure AD registrované Windows zařízení přihlašovat do zařízení se považuje za výzva. Například pokud jste nakonfigurovali přihlašování v možnosti četnost až 24 hodin pro aplikace Office, uživatele v Azure AD registrované Windows zařízení budou splňovat přihlášení frekvence zásad po přihlášení k zařízení a nebudete vyzváni znovu při otevírání aplikace Office.

Pokud jste nakonfigurovali jinou frekvencí přihlášení pro jiné webové aplikace, které jsou spuštěny ve stejné relaci prohlížeče, nejpřísnější zásady použijí pro obě aplikace sdílejí všechny aplikace spuštěné ve stejné relaci prohlížeče token jedné relace.

### <a name="policy-2-persistent-browser-session"></a>Zásady 2: Trvalá relace prohlížeče

1. Vytvořit novou zásadu
1. Vyberte všechny požadované podmínky.

   > [!NOTE]
   > Mějte prosím na paměti, že tento ovládací prvek požaduje zvolit "Všechny cloudové aplikace" jako podmínku. Trvalost relace prohlížeče se řídí ověřování tokenu relace. Všechny karty v relaci prohlížeče sdílet jednu relaci token a proto všechny musí sdílet trvalého stavu.

1. Přejděte na **ovládacích prvků přístupu** > **relace** a klikněte na tlačítko **trvalé prohlížeče relace**
1. Vyberte hodnotu z rozevíracího seznamu
1. Uložit zásadu

![Zásady podmíněného přístupu, které jsou nakonfigurované pro trvalé prohlížeče](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Trvalé konfigurace relace prohlížeče v podmíněného přístupu Azure AD se přepíšou "zůstat přihlášení?" nastavení firemního brandingu podokně webu Azure Portal pro stejného uživatele, pokud jste nakonfigurovali obě zásady.

## <a name="validation"></a>Ověřování

Použijte nástroj What If pro simulaci přihlášení od uživatele pro cílovou aplikaci a jiných podmínek založených na tom, jak jste nakonfigurovali zásady. Ovládací prvky ověřování relací správu se zobrazí ve výsledku tohoto nástroje.

![Podmíněný přístup, jak postupovat, pokud nástroj pro výsledky](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Nasazení zásad

Pokud chcete mít jistotu, že vaše zásady funguje podle očekávání, doporučené osvědčeným postupem je otestovat před zavedením do produkčního prostředí. V ideálním případě ověřte, zda vaše nová zásada funguje očekávaným způsobem pomocí testovacího tenanta. Další informace najdete v článku [osvědčené postupy pro podmíněný přístup ve službě Azure Active Directory](best-practices.md).

## <a name="next-steps"></a>Další postup

* Pokud chcete vědět, jak nakonfigurovat zásady podmíněného přístupu, najdete v článku [vyžadovat vícefaktorové ověřování pro konkrétní aplikace s podmíněným přístupem Azure Active Directory](app-based-mfa.md).
* Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro vaše prostředí, najdete v článku [osvědčené postupy pro podmíněný přístup ve službě Azure Active Directory](best-practices.md).

---
title: Podmínka umístění v Azure Active Directory podmíněný přístup
description: Naučte se používat podmínku umístění k řízení přístupu ke cloudovým aplikacím na základě síťového umístění uživatele.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 915675af1e646f2cb77e36c0018ed372ff9496fc
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380301"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Jaká je podmínka umístění v Azure Active Directory podmíněný přístup? 

Pomocí [podmíněného přístupu Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md)můžete řídit, jak můžou autorizovaní uživatelé přistupovat k vašim cloudovým aplikacím. Podmínka umístění zásad podmíněného přístupu umožňuje propojení nastavení řízení přístupu k síťovým umístěním vašich uživatelů.

Tento článek poskytuje informace, které potřebujete ke konfiguraci podmínky umístění.

## <a name="locations"></a>Umístění

Azure AD umožňuje jednotné přihlašování k zařízením, aplikacím a službám odkudkoli na veřejném Internetu. V případě podmínky umístění můžete řídit přístup k vašim cloudovým aplikacím na základě síťového umístění uživatele. Běžné případy použití pro podmínku umístění:

- Vyžadování služby Multi-Factor Authentication pro uživatele, kteří přistupují ke službě, když jsou mimo podnikovou síť.
- Blokování přístupu pro uživatele, kteří přistupují ke službě z určitých zemí nebo oblastí.

Umístění je popisek pro síťové umístění, které buď představuje pojmenované umístění nebo důvěryhodné IP adresy služby Multi-Factor Authentication.

## <a name="named-locations"></a>Pojmenovaná umístění

Pomocí pojmenovaných umístění můžete vytvořit logická seskupení rozsahů IP adres nebo zemí a oblastí.

K pojmenovaným umístěním se dostanete v části **Správa** na stránce podmíněný přístup.

![Pojmenovaná umístění v podmíněném přístupu](./media/location-condition/02.png)

Pojmenované umístění má následující komponenty:

![Vytvořit nové pojmenované umístění](./media/location-condition/42.png)

- **Name** – zobrazovaný název pojmenovaného umístění.
- **Rozsahy IP** adres – jeden nebo víc rozsahů IPv4 adres ve formátu CIDR. Zadání rozsahu IPv6 adres se nepodporuje.

   > [!NOTE]
   > Rozsahy IPv6 adres se aktuálně nedají zahrnout do pojmenovaného umístění. To znamená, že rozsahy IPv6 nelze vyloučit ze zásady podmíněného přístupu.

- **Označit jako důvěryhodné umístění** – příznak, který můžete nastavit pro pojmenované umístění k označení důvěryhodného umístění. Obvykle jsou důvěryhodná umístění síťová oblast, která je řízena vaším IT oddělením. Kromě podmíněného přístupu jsou důvěryhodná pojmenovaná umístění používána také v sestavách Azure Identity Protection a Azure AD k omezení [falešně pozitivních](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1)událostí.
- **Země nebo oblasti** – Tato možnost umožňuje vybrat jednu nebo více zemí nebo oblastí k definování pojmenovaného umístění.
- **Zahrnout neznámé oblasti** – některé IP adresy nejsou namapované na konkrétní zemi nebo oblast. Tato možnost umožňuje zvolit, jestli se mají tyto IP adresy zahrnout do pojmenovaného umístění. Toto nastavení použijte, pokud se zásada používající pojmenované umístění má vztahovat na neznámá umístění.

Počet pojmenovaných umístění, která můžete konfigurovat, se omezuje na velikost souvisejícího objektu ve službě Azure AD. Umístění můžete nakonfigurovat na základě následujících omezení:

- Jedno pojmenované umístění s rozsahem IP adres až 1200.
- Do každé z nich se přiřadí maximálně 90 pojmenovaných umístění s jedním rozsahem IP adres.

Zásady podmíněného přístupu se vztahují na přenosy IPv4 a IPv6. Aktuálně pojmenovaná umístění neumožňují konfigurovat rozsahy IPv6. Toto omezení způsobuje následující situace:

- Zásady podmíněného přístupu se nedají cílit na konkrétní rozsahy IPv6.
- Zásady podmíněného přístupu nemůžou vyloučit konkrétní rozsahy IPV6.

Pokud je zásada nakonfigurovaná tak, aby se nastavila na jakékoli místo, bude se vztahovat na přenosy IPv4 a IPv6. Pojmenovaná umístění konfigurovaná pro zadané země a oblasti podporují pouze adresy IPv4. Přenos IPv6 je zahrnutý jenom v případě, že je vybraná možnost zahrnout neznámé oblasti.

## <a name="trusted-ips"></a>Důvěryhodné IP adresy

V [nastavení služby Multi-Factor Authentication Service](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx)můžete také nakonfigurovat rozsahy IP adres reprezentující místní intranet vaší organizace. Tato funkce vám umožní nakonfigurovat až 50 rozsahů IP adres. Rozsahy IP adres jsou ve formátu CIDR. Další informace najdete v tématu [důvěryhodné IP adresy](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Pokud máte nakonfigurované důvěryhodné IP adresy, zobrazí se v seznamu umístění pro podmínku umístění jako **důvěryhodné IP adresy MFA** .

### <a name="skipping-multi-factor-authentication"></a>Přeskakuje se Multi-Factor Authentication

Na stránce nastavení služby Multi-Factor Authentication Service můžete identifikovat uživatele firemní sítě intranet tak, že **pro žádosti od federovaných uživatelů v mém intranetu vyberete přeskočit vícefaktorové ověřování**. Toto nastavení indikuje, že deklarace identity uvnitř podnikové sítě, která je vydaná AD FS, by měla být důvěryhodná a slouží k identifikaci uživatele jako v podnikové síti. Další informace najdete v tématu [Povolení funkce důvěryhodných IP adres pomocí podmíněného přístupu](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Po zaškrtnutí této možnosti, včetně pojmenovaného umístění, **důvěryhodných IP adres MFA** budou platit pro všechny zásady s vybranou možností.

U mobilních a desktopových aplikací, které mají dlouhodobé životnosti relací, se podmíněný přístup pravidelně znovu vyhodnocuje. Výchozí hodnota je jednou hodinu. Pokud je deklarace identity uvnitř podnikové sítě vydaná jenom v době počátečního ověřování, služba Azure AD nemusí mít seznam důvěryhodných IP adres. V takovém případě je obtížné zjistit, jestli je uživatel stále v podnikové síti:

1. Ověřte, zda je IP adresa uživatele v jednom z rozsahů důvěryhodných IP adres.
2. Ověřte, zda první tři oktety IP adresy uživatele odpovídají prvním třem oktetům IP adresy počátečního ověřování. IP adresa je porovnávána s počátečním ověřováním, když byla původně vydaná deklarace identity uvnitř podnikové sítě a bylo ověřené umístění uživatele.

Pokud dojde k selhání obou kroků, bude uživatel považován za již nepřipojený k důvěryhodné IP adrese.

## <a name="location-condition-configuration"></a>Konfigurace podmínky umístění

Když konfigurujete podmínku umístění, máte možnost rozlišovat mezi:

- Jakékoli umístění
- Všechna důvěryhodná umístění
- Vybraná umístění

![Konfigurace podmínky umístění](./media/location-condition/01.png)

### <a name="any-location"></a>Jakékoli umístění

Ve výchozím nastavení při výběru **kteréhokoli umístění** dojde k použití zásad pro všechny IP adresy, což znamená libovolnou adresu na internetu. Toto nastavení není omezeno na IP adresy, které jste nakonfigurovali jako pojmenované umístění. Když vyberete **libovolné umístění**, můžete i nadále vyloučit konkrétní umístění ze zásad. Můžete například použít zásadu na všechna umístění kromě důvěryhodných umístění a nastavit obor na všechna umístění kromě podnikové sítě.

### <a name="all-trusted-locations"></a>Všechna důvěryhodná umístění

Tato možnost se týká:

- Všechna umístění, která byla označena jako důvěryhodná umístění
- **Důvěryhodné IP adresy MFA** (pokud jsou nakonfigurované)

### <a name="selected-locations"></a>Vybraná umístění

Pomocí této možnosti můžete vybrat jedno nebo více pojmenovaných umístění. Chcete-li použít zásadu s tímto nastavením, musí se uživatel připojit z libovolného umístění. Když kliknete na **Vybrat** ovládací prvek pro výběr sítě, který zobrazuje seznam pojmenovaných sítí, otevře se. V seznamu se zobrazí také informace o tom, zda bylo síťové umístění označeno jako důvěryhodné. Pojmenované umístění s názvem **MFA Trusted IP** adresa slouží k zahrnutí nastavení IP adresy, která se dají konfigurovat na stránce nastavení služby Multi-Factor Authentication Service.

## <a name="what-you-should-know"></a>Co byste měli vědět

### <a name="when-is-a-location-evaluated"></a>Kdy je vyhodnoceno umístění?

Zásady podmíněného přístupu jsou vyhodnocovány v těchto případech:

- Uživatel se zpočátku přihlašuje k webové aplikaci, mobilní aplikaci nebo aplikaci klasické pracovní plochy.
- Mobilní aplikace nebo aplikace klasické pracovní plochy, která používá moderní ověřování, používá k získání nového přístupového tokenu obnovovací token. Ve výchozím nastavení je tato kontrolu jednou za hodinu.

Tato kontrolu znamená, že mobilní a desktopové aplikace využívají moderní ověřování, v průběhu změny síťového umístění se zjistí změna v umístění. Pro mobilní a desktopové aplikace, které nepoužívají moderní ověřování, se zásady použijí u každé žádosti o token. Frekvence požadavku se může lišit v závislosti na aplikaci. Podobně platí, že u webových aplikací se zásady aplikují při počátečním přihlašování a jsou vhodné po dobu života relace ve webové aplikaci. V důsledku rozdílů v životnosti relací napříč aplikacemi se čas mezi vyhodnocením zásad liší také. Pokaždé, když aplikace požádá o nový token přihlášení, zásada se použije.

Ve výchozím nastavení služba Azure AD vydá token po hodinách. Po přesunu podnikové sítě do celé hodiny se zásady vynutily pro aplikace používající moderní ověřování.

### <a name="user-ip-address"></a>IP adresa uživatele

IP adresa, která se používá při vyhodnocování zásad, je veřejná IP adresa uživatele. V případě zařízení v privátní síti tato IP adresa nepředstavuje IP adresu klienta zařízení uživatele v intranetu, jedná se o adresu, kterou síť používá pro připojení k veřejnému Internetu.

> [!WARNING]
> Pokud má vaše zařízení jenom adresu IPv6, konfigurace podmínky umístění není podporovaná.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Hromadné nahrávání a stahování pojmenovaných umístění

Při vytváření nebo aktualizaci pojmenovaných umístění můžete pro hromadné aktualizace nahrát nebo stáhnout soubor CSV s rozsahy IP adres. Nahrávání nahradí rozsahy IP adres v seznamu hodnotami ze souboru. Každý řádek souboru obsahuje jeden rozsah IP adres ve formátu CIDR.

### <a name="cloud-proxies-and-vpns"></a>Cloudové proxy servery a sítě VPN

Pokud používáte cloudové proxy hostované nebo řešení sítě VPN, IP adresa Azure AD používá při vyhodnocování zásad je IP adresa proxy serveru. Záhlaví X-předané (XFF), které obsahuje veřejnou IP adresu uživatele, se nepoužívá, protože neexistuje žádné ověření, které pochází z důvěryhodného zdroje, takže by mohla představovat metodu pro Faking IP adresu.

Pokud je cloudový proxy server, je možné použít zásadu, která vyžaduje zařízení připojené k doméně, nebo Corpnet deklaraci identity v rámci AD FS.

### <a name="api-support-and-powershell"></a>Podpora rozhraní API a prostředí PowerShell

Rozhraní API a PowerShell se zatím nepodporují pro pojmenovaná umístění nebo pro zásady podmíněného přístupu.

## <a name="next-steps"></a>Další kroky

- Pokud chcete zjistit, jak nakonfigurovat zásady podmíněného přístupu, přečtěte si téma [vyžádání MFA pro konkrétní aplikace s Azure Active Directory podmíněný přístup](app-based-mfa.md).
- Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro vaše prostředí, přečtěte si [osvědčené postupy pro podmíněný přístup v Azure Active Directory](best-practices.md).

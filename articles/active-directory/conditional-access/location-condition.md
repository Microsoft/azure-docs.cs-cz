---
title: Podmínka umístění v Azure Active Directory podmíněný přístup
description: Naučte se používat podmínku umístění k řízení přístupu ke cloudovým aplikacím na základě síťového umístění uživatele.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 07af586bac71ee9b33ef314756454cb3c52ec912
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305918"
---
# <a name="using-the-location-condition-in-a-conditional-access-policy"></a>Použití podmínky umístění v zásadách podmíněného přístupu 

Jak je vysvětleno v [článku](overview.md) zásady podmíněného přístupu, jsou na nejvyšší úrovni základní příkazy if-then kombinující signály, jejich rozhodování a prosazování zásad organizace. Jedním z těchto signálů, které je možné začlenit do procesu rozhodování, je umístění v síti.

![Koncepční podmíněný signál plus rozhodnutí pro získání vynucování](./media/location-condition/conditional-access-signal-decision-enforcement.png)

Organizace můžou použít toto síťové umístění pro běžné úkoly, jako je: 

- Vyžadování služby Multi-Factor Authentication pro uživatele, kteří přistupují ke službě, když jsou mimo podnikovou síť.
- Blokování přístupu pro uživatele, kteří přistupují ke službě z určitých zemí nebo oblastí.

Umístění v síti je určené veřejnou IP adresou, kterou klient poskytuje Azure Active Directory. Zásady podmíněného přístupu se ve výchozím nastavení vztahují na všechny adresy IPv4 a IPv6. 

## <a name="named-locations"></a>Pojmenovaná umístění

Umístění jsou určena v Azure Portal v části **Azure Active Directory**  >  **zabezpečení**  >  **podmíněného přístupu**  >  **s názvem umístění**. Tato umístění v síti můžou zahrnovat umístění, jako jsou například rozsahy sítě ústředí organizace, rozsahy sítě VPN nebo rozsahy, které chcete blokovat. Pojmenovaná umístění můžete definovat rozsahy adres IPv4/IPv6 nebo země nebo oblasti. 

![Pojmenovaná umístění v Azure Portal](./media/location-condition/new-named-location.png)

### <a name="ip-address-ranges"></a>Rozsahy IP adres

Pro definování pojmenovaného umístění rozsahů adres IPv4/IPv6 budete muset zadat **název** a rozsah IP adres. 

Pojmenovaná umístění definovaná rozsahy adres IPv4/IPv6 podléhají následujícím omezením: 
- Nakonfigurovat až 195 pojmenovaných umístění
- Nakonfigurovat až 2000 rozsahů IP adres na pojmenované umístění
- Podporují se i rozsahy IPv4 i IPv6.
- Connot se nakonfigurují rozsahy privátních IP adres.
- Počet IP adres obsažených v rozsahu je omezený. Při definování rozsahu IP adres jsou povoleny pouze masky CIDR větší než/8. 

### <a name="trusted-locations"></a>Důvěryhodná umístění

Správci mohou určit pojmenovaná umístění definovaná rozsahy IP adres, která budou důvěryhodná pojmenovaná umístění. 

![Důvěryhodná umístění v Azure Portal](./media/location-condition/new-trusted-location.png)

Přihlášení z důvěryhodných pojmenovaných umístění zlepšují přesnost výpočtu rizik Azure AD Identity Protection, snižuje riziko přihlašování uživatelů při ověřování z umístění označeného jako důvěryhodné. V případě zásad podmíněného přístupu můžou být taky cílem důvěryhodných pojmenovaných umístění. Můžete například vyžadovat omezení registrace Multi-Factor Authentication pouze do důvěryhodných pojmenovaných umístění. 

### <a name="countries-and-regions"></a>Země a oblasti

Některé organizace se můžou rozhodnout omezit přístup k určitým zemím nebo oblastem pomocí podmíněného přístupu. Kromě definování pojmenovaných umístění podle rozsahů IP adres můžou správci definovat pojmenovaná umístění podle zemí nebo oblastí. Když se uživatel přihlásí, Azure AD přeloží IPv4 adresu uživatele na zemi nebo oblast a mapování se pravidelně aktualizuje. Organizace můžou pomocí pojmenovaných míst definovaných v zemích blokovat provoz ze zemí, kde nedělají firmy, jako je například Severní Korea. 

> [!NOTE]
> Přihlášení z IPv6 adres nejde namapovat na země nebo oblasti a považují se za neznámé oblasti. Pouze adresy IPv4 lze namapovat na země nebo oblasti.

![Vytvořit nové umístění na základě země nebo oblasti v Azure Portal](./media/location-condition/new-named-location-country-region.png)

#### <a name="include-unknown-areas"></a>Zahrnout neznámé oblasti

Některé IP adresy nejsou namapované na konkrétní zemi nebo oblast, včetně všech adres IPv6. Pokud chcete zachytit tato umístění IP adres, zaškrtněte políčko při definování umístění **Zahrnout neznámé oblasti** . Tato možnost umožňuje zvolit, jestli se mají tyto IP adresy zahrnout do pojmenovaného umístění. Toto nastavení použijte, pokud se zásada používající pojmenované umístění má vztahovat na neznámá umístění.

### <a name="configure-mfa-trusted-ips"></a>Konfigurace důvěryhodných IP adres MFA

V [nastavení služby Multi-Factor Authentication Service](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx)můžete také nakonfigurovat rozsahy IP adres reprezentující místní intranet vaší organizace. Tato funkce vám umožní nakonfigurovat až 50 rozsahů IP adres. Rozsahy IP adres jsou ve formátu CIDR. Další informace najdete v tématu [důvěryhodné IP adresy](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Pokud máte nakonfigurované důvěryhodné IP adresy, zobrazí se v seznamu umístění pro podmínku umístění jako **důvěryhodné IP adresy MFA** .

### <a name="skipping-multi-factor-authentication"></a>Přeskakuje se Multi-Factor Authentication

Na stránce nastavení služby Multi-Factor Authentication Service můžete identifikovat uživatele firemní sítě intranet tak, že  **pro žádosti od federovaných uživatelů v mém intranetu vyberete přeskočit vícefaktorové ověřování**. Toto nastavení indikuje, že deklarace identity uvnitř podnikové sítě, která je vydaná AD FS, by měla být důvěryhodná a slouží k identifikaci uživatele jako v podnikové síti. Další informace najdete v tématu [Povolení funkce důvěryhodných IP adres pomocí podmíněného přístupu](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Po zaškrtnutí této možnosti, včetně pojmenovaného umístění, **důvěryhodných IP adres MFA** budou platit pro všechny zásady s vybranou možností.

U mobilních a desktopových aplikací, které mají dlouhodobé životnosti relací, se podmíněný přístup pravidelně znovu vyhodnocuje. Výchozí hodnota je jednou hodinu. Pokud je deklarace identity uvnitř podnikové sítě vydaná jenom v době počátečního ověřování, služba Azure AD nemusí mít seznam důvěryhodných IP adres. V takovém případě je obtížné zjistit, jestli je uživatel stále v podnikové síti:

1. Ověřte, zda je IP adresa uživatele v jednom z rozsahů důvěryhodných IP adres.
1. Ověřte, zda první tři oktety IP adresy uživatele odpovídají prvním třem oktetům IP adresy počátečního ověřování. IP adresa je porovnávána s počátečním ověřováním, když byla původně vydaná deklarace identity uvnitř podnikové sítě a bylo ověřené umístění uživatele.

Pokud dojde k selhání obou kroků, bude uživatel považován za již nepřipojený k důvěryhodné IP adrese.

## <a name="location-condition-in-policy"></a>Podmínka umístění v zásadách

Když konfigurujete podmínku umístění, máte možnost rozlišovat mezi:

- Jakékoli umístění
- Všechna důvěryhodná umístění
- Vybraná umístění

### <a name="any-location"></a>Jakékoli umístění

Ve výchozím nastavení při výběru **kteréhokoli umístění** dojde k použití zásad pro všechny IP adresy, což znamená libovolnou adresu na internetu. Toto nastavení není omezeno na IP adresy, které jste nakonfigurovali jako pojmenované umístění. Když vyberete **libovolné umístění**, můžete i nadále vyloučit konkrétní umístění ze zásad. Můžete například použít zásadu na všechna umístění kromě důvěryhodných umístění a nastavit obor na všechna umístění kromě podnikové sítě.

### <a name="all-trusted-locations"></a>Všechna důvěryhodná umístění

Tato možnost se týká:

- Všechna umístění, která byla označena jako důvěryhodná umístění
- **Důvěryhodné IP adresy MFA** (pokud jsou nakonfigurované)

### <a name="selected-locations"></a>Vybraná umístění

Pomocí této možnosti můžete vybrat jedno nebo více pojmenovaných umístění. Chcete-li použít zásadu s tímto nastavením, musí se uživatel připojit z libovolného umístění. Když kliknete na **Vybrat** ovládací prvek pro výběr sítě, který zobrazuje seznam pojmenovaných sítí, otevře se. V seznamu se zobrazí také informace o tom, zda bylo síťové umístění označeno jako důvěryhodné. Pojmenované umístění s názvem **MFA Trusted IP** adresa slouží k zahrnutí nastavení IP adresy, která se dají konfigurovat na stránce nastavení služby Multi-Factor Authentication Service.

## <a name="ipv6-traffic"></a>Přenos IPv6

Ve výchozím nastavení se zásady podmíněného přístupu použijí na všechny přenosy protokolu IPv6. Pokud nechcete, aby se zásady vynutily pro konkrétní rozsahy IPv6, můžete vyloučit konkrétní rozsahy adres IPv6 ze zásad podmíněného přístupu. Například pokud chcete vymáhat zásadu pro použití ve vaší podnikové síti a vaše podniková síť je hostována ve veřejných rozsahech IPv6.  

### <a name="when-will-my-tenant-have-ipv6-traffic"></a>Kdy bude můj tenant provozovat protokolem IPv6?

Azure Active Directory (Azure AD) v současné době nepodporuje přímá síťová připojení, která používají protokol IPv6. Existují však případy, kdy je provoz ověřování proxy serverem prostřednictvím jiné služby. V těchto případech se adresa IPv6 použije při vyhodnocování zásad.

Většina provozu protokolu IPv6, která se proxy serverem do služby Azure AD dostane, přichází z Microsoft Exchange Online. Pokud je k dispozici, Exchange bude preferovat připojení IPv6. **Takže pokud máte nějaké zásady podmíněného přístupu pro Exchange, které jsou nakonfigurované pro konkrétní rozsahy IPv4, budete chtít, abyste měli jistotu, že jste taky přidali rozsahy IPv6 organizací.** Nezahrnuje rozsahy IPv6 způsobí neočekávané chování v následujících dvou případech:

- Pokud se e-mailový klient připojuje k Exchangi Online se starším ověřováním, může Azure AD získat adresu IPv6. Počáteční požadavek na ověření přejde na Exchange a pak se proxy server zaznamená do Azure AD.
- Pokud se v prohlížeči používá aplikace Outlook Web Access (OWA), bude pravidelně ověřovat všechny zásady podmíněného přístupu. Tato kontrolu se používá k zachytávání případů, kdy se uživatel mohl přesunout z povolené IP adresy na nové místo, jako je třeba káva na ulici. V takovém případě, pokud se použije adresa IPv6, a pokud adresa IPv6 není v nakonfigurovaném rozsahu, uživatel může svou relaci přerušit a vrátit se zpět do Azure AD a znovu ověřit. 

Toto jsou nejběžnější důvody, které možná budete potřebovat ke konfiguraci rozsahů IPv6 ve vašich pojmenovaných umístěních. Pokud navíc používáte Azure virtuální sítě, budete mít provoz z IPv6 adresy. Pokud máte provoz virtuální sítě blokovaný zásadou podmíněného přístupu, podívejte se do přihlašovacího protokolu Azure AD. Jakmile identifikujete provoz, můžete získat využívanou adresu IPv6 a vyloučit ji ze zásad. 

> [!NOTE]
> Pokud chcete zadat rozsah IP CIDR pro jednu adresu, použijte bitovou masku/128. Pokud říkáte IPv6 adresu 2607: fb90: b27a: 6f69: f8d5: dea0: fb39:74A a chtěli byste vyloučit tuto jednotlivou adresu jako rozsah, použijte 2607: fb90: b27a: 6f69: f8d5: dea0: fb39:74A/128.

### <a name="identifying-ipv6-traffic-in-the-azure-ad-sign-in-activity-reports"></a>Určení provozu protokolu IPv6 v sestavách aktivit přihlášení k Azure AD

Pomocí [sestav aktivit přihlašování služby Azure AD](../reports-monitoring/concept-sign-ins.md)můžete zjistit provoz protokolu IPv6 ve vašem tenantovi. Po otevření sestavy aktivity přidejte sloupec IP adresa. Tento sloupec vám umožní identifikovat přenos protokolu IPv6.

IP adresu klienta můžete najít také tak, že kliknete na řádek v sestavě a pak na kartu umístění v podrobnostech o přihlašovací aktivitě. 

## <a name="what-you-should-know"></a>Co byste měli vědět

### <a name="when-is-a-location-evaluated"></a>Kdy je vyhodnoceno umístění?

Zásady podmíněného přístupu jsou vyhodnocovány v těchto případech:

- Uživatel se zpočátku přihlašuje k webové aplikaci, mobilní aplikaci nebo aplikaci klasické pracovní plochy.
- Mobilní aplikace nebo aplikace klasické pracovní plochy, která používá moderní ověřování, používá k získání nového přístupového tokenu obnovovací token. Ve výchozím nastavení je tato kontrolu jednou za hodinu.

Tato kontrolu znamená, že mobilní a desktopové aplikace využívají moderní ověřování, v průběhu změny síťového umístění se zjistí změna v umístění. Pro mobilní a desktopové aplikace, které nepoužívají moderní ověřování, se zásady použijí u každé žádosti o token. Frekvence požadavku se může lišit v závislosti na aplikaci. Podobně platí, že u webových aplikací se zásady aplikují při počátečním přihlašování a jsou vhodné po dobu života relace ve webové aplikaci. V důsledku rozdílů v životnosti relací napříč aplikacemi se čas mezi vyhodnocením zásad liší také. Pokaždé, když aplikace požádá o nový token přihlášení, zásada se použije.

Ve výchozím nastavení služba Azure AD vydá token po hodinách. Po přesunu podnikové sítě do celé hodiny se zásady vynutily pro aplikace používající moderní ověřování.

### <a name="user-ip-address"></a>IP adresa uživatele

IP adresa, která se používá při vyhodnocování zásad, je veřejná IP adresa uživatele. V případě zařízení v privátní síti tato IP adresa nepředstavuje IP adresu klienta zařízení uživatele v intranetu, jedná se o adresu, kterou síť používá pro připojení k veřejnému Internetu.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Hromadné nahrávání a stahování pojmenovaných umístění

Při vytváření nebo aktualizaci pojmenovaných umístění můžete pro hromadné aktualizace nahrát nebo stáhnout soubor CSV s rozsahy IP adres. Nahrávání nahradí rozsahy IP adres v seznamu těmito rozsahy ze souboru. Každý řádek souboru obsahuje jeden rozsah IP adres ve formátu CIDR.

### <a name="cloud-proxies-and-vpns"></a>Cloudové proxy servery a sítě VPN

Pokud používáte cloudové proxy hostované nebo řešení sítě VPN, IP adresa Azure AD používá při vyhodnocování zásad je IP adresa proxy serveru. Záhlaví X-předané (XFF), které obsahuje veřejnou IP adresu uživatele, se nepoužívá, protože neexistuje žádné ověření, které pochází z důvěryhodného zdroje, takže by mohla představovat metodu pro Faking IP adresu.

Pokud je cloudový proxy server, můžete použít zásadu, která vyžaduje, aby bylo možné použít hybridní zařízení připojené k Azure AD, nebo Corpnet deklaraci identity v rámci služby AD FS.

### <a name="api-support-and-powershell"></a>Podpora rozhraní API a prostředí PowerShell

K dispozici je verze Preview Graph API pro pojmenovaná umístění, další informace najdete v tématu [rozhraní API pro namedLocation](/graph/api/resources/namedlocation?view=graph-rest-beta).

> [!NOTE]
> Pojmenovaná umístění, která vytvoříte pomocí PowerShellu, se zobrazují jenom v pojmenovaných umístěních (Preview). Ve starém zobrazení se nedají zobrazit pojmenovaná umístění.  

## <a name="next-steps"></a>Další kroky

- Pokud chcete zjistit, jak nakonfigurovat zásadu podmíněného přístupu, přečtěte si článek [Vytvoření zásady podmíněného přístupu](concept-conditional-access-policies.md).
- Hledáte ukázkovou zásadu pomocí podmínky umístění? Projděte si článek [podmíněný přístup: blokování přístupu podle umístění](howto-conditional-access-policy-location.md)

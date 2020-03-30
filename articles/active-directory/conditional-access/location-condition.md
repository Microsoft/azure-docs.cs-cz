---
title: Podmínka umístění v podmíněném přístupu služby Azure Active Directory
description: Přečtěte si, jak pomocí podmínky určování polohy řídit přístup ke cloudovým aplikacím na základě umístění uživatele v síti.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263228"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Jaká je podmínka umístění v podmíněném přístupu služby Azure Active Directory? 

S [podmíněným přístupem Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md)můžete řídit, jak budou mít oprávnění uživatelé přístup k vašim cloudovým aplikacím. Podmínka umístění zásad podmíněného přístupu umožňuje svázat nastavení ovládacích prvků přístupu se síťovými umístěními uživatelů.

Tento článek obsahuje informace, které potřebujete ke konfiguraci podmínky umístění.

## <a name="locations"></a>Umístění

Azure AD umožňuje jednotné přihlašování k zařízením, aplikacím a službám z libovolného místa na veřejném internetu. S podmínkou umístění můžete řídit přístup ke cloudovým aplikacím na základě umístění v síti uživatele. Běžné případy použití podmínky umístění jsou:

- Vyžadování vícefaktorového ověřování pro uživatele, kteří přistupují ke službě, když jsou mimo podnikovou síť.
- Blokování přístupu uživatelů, kteří přistupují ke službě z konkrétních zemí nebo oblastí.

Umístění je popisek pro síťové umístění, který představuje pojmenované umístění nebo vícefaktorové ověřování důvěryhodné IP adresy.

## <a name="named-locations"></a>Pojmenovaná umístění

S pojmenovanými umístěními můžete vytvořit logická seskupení rozsahů IP adres nebo zemí a oblastí.

K pojmenovaným umístěním můžete přistupovat v části **Správa** na stránce Podmíněný přístup.

![Pojmenovaná umístění v podmíněném přístupu](./media/location-condition/02.png)

Pojmenované umístění má následující součásti:

![Vytvoření nového pojmenovaného umístění](./media/location-condition/42.png)

- **Název** - Zobrazovaný název pojmenovaného umístění.
- **Rozsahy IP adres** - Jeden nebo více rozsahů adres IPv4 ve formátu CIDR. Určení rozsahu adres IPv6 není podporováno.

   > [!NOTE]
   > Rozsahy adres IPv6 nelze aktuálně zahrnout do pojmenovaného umístění. To znamená, že rozsahy IPv6 nelze vyloučit ze zásad podmíněného přístupu.

- **Označit jako důvěryhodné umístění** – Příznak, který můžete nastavit pro pojmenované umístění a označit tak, aby označovalo důvěryhodné umístění. Důvěryhodná umístění jsou obvykle síťové oblasti, které jsou řízeny vaším oddělením IT. Kromě podmíněného přístupu důvěryhodná pojmenovaná umístění používají také Azure Identity Protection a Azure AD sestavy zabezpečení ke snížení [falešně pozitivních hodnot](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1).
- **Země nebo oblasti** – tato možnost umožňuje vybrat jednu nebo více zemí nebo oblastí a definovat pojmenované umístění.
- **Zahrnout neznámé oblasti** – některé IP adresy nejsou mapovány na konkrétní zemi nebo oblast. Tato možnost umožňuje zvolit, zda mají být tyto adresy IP zahrnuty do pojmenovaného umístění. Toto nastavení použijte v případě, že by se zásady používající pojmenované umístění měly vztahovat na neznámá umístění.

Počet pojmenovaných umístění, které můžete nakonfigurovat, je omezen velikostí souvisejícího objektu ve službě Azure AD. Umístění můžete konfigurovat na základě následujících omezení:

- Jedno pojmenované umístění s rozsahy IP adres až 1200.
- Maximálně 90 pojmenovaných umístění s jedním rozsahem IP adres přiřazeným každému z nich.

Zásady podmíněného přístupu platí pro přenosy IPv4 a IPv6. Aktuálně pojmenované umístění neumožňují konfiguraci rozsahů IPv6. Toto omezení způsobuje následující situace:

- Zásady podmíněného přístupu nelze cílit na konkrétní rozsahy IPv6.
- Zásady podmíněného přístupu nemohou vyloučit určité rozsahy IPV6.

Pokud je zásada nakonfigurována tak, aby se vztahovala na "Libovolné umístění", bude se vztahovat na přenosy IPv4 a IPv6. Pojmenovaná umístění nakonfigurovaná pro zadané země a oblasti podporují pouze adresy IPv4. Přenos protokolu IPv6 je zahrnut pouze v případě, že je vybrána možnost "zahrnout neznámé oblasti".

## <a name="trusted-ips"></a>Důvěryhodné IP adresy

Rozsahy adres IP představující místní intranet vaší organizace můžete také nakonfigurovat v [nastavení služby vícefaktorového ověřování](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Tato funkce umožňuje konfigurovat až 50 rozsahů IP adres. Rozsahy IP adres jsou ve formátu CIDR. Další informace naleznete [v tématu Důvěryhodné adresy IP](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Pokud máte nakonfigurované důvěryhodné IP adresy, zobrazí se v seznamu umístění pro podmínku umístění jako **důvěryhodné služby MFA.**

### <a name="skipping-multi-factor-authentication"></a>Přeskočení vícefaktorového ověřování

Na stránce nastavení služby vícefaktorového ověřování můžete identifikovat podnikové uživatele intranetu výběrem **možnosti Přeskočit vícefaktorové ověřování pro požadavky federovaných uživatelů v síti intranet**. Toto nastavení označuje, že deklarace uvnitř podnikové sítě, která je vydána službaí AD FS, by měla být důvěryhodná a použita k identifikaci uživatele jako uživatele v podnikové síti. Další informace naleznete [v tématu Enable the Trusted IP Ps feature using Conditional Access](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Po zaškrtnutí této možnosti, včetně pojmenované umístění **MFA Důvěryhodné IPS** se bude vztahovat na všechny zásady s touto vybranou možností.

U mobilních a desktopových aplikací, které mají dlouhou životnost relace, je podmíněný přístup pravidelně přehodnocován. Výchozí hodnota je jednou za hodinu. Pokud je deklarace uvnitř podnikové sítě vydána jenom v době počátečního ověřování, služba Azure AD nemusí mít seznam oblastí důvěryhodných IP adres. V tomto případě je obtížnější určit, zda je uživatel stále v podnikové síti:

1. Zkontrolujte, zda se adresa IP uživatele nachází v jednom z důvěryhodných rozsahů IP adres.
2. Zkontrolujte, zda první tři oktety IP adresy uživatele odpovídají prvním třem oktetům adresy IP počátečního ověřování. IP adresa je porovnána s počátečním ověřováním, když byla původně vydána deklarace uvnitř podnikové sítě a umístění uživatele bylo ověřeno.

Pokud se oba kroky nezdaří, uživatel se považuje za již na důvěryhodné IP.

## <a name="location-condition-configuration"></a>Konfigurace podmínky umístění

Při konfiguraci podmínky umístění máte možnost rozlišovat mezi:

- Jakékoli místo
- Všechna důvěryhodná umístění
- Vybraná místa

![Konfigurace podmínky umístění](./media/location-condition/01.png)

### <a name="any-location"></a>Jakékoli místo

Ve výchozím nastavení výběr libovolného **umístění** způsobí, že se zásada použije na všechny adresy IP, což znamená libovolnou adresu v Internetu. Toto nastavení není omezeno na adresy IP, které jste nakonfigurovali jako pojmenované umístění. Když vyberete **Libovolné umístění**, můžete ze zásady vyloučit určitá místa. Můžete například použít zásadu pro všechna umístění s výjimkou důvěryhodných umístění a nastavit obor pro všechna umístění s výjimkou podnikové sítě.

### <a name="all-trusted-locations"></a>Všechna důvěryhodná umístění

Tato možnost se vztahuje na:

- Všechna umístění, která byla označena jako důvěryhodné umístění
- **Důvěryhodné ips mfa** (pokud jsou nakonfigurovány)

### <a name="selected-locations"></a>Vybraná místa

Pomocí této možnosti můžete vybrat jedno nebo více pojmenovaných umístění. Chcete-li použít zásadu s tímto nastavením, musí se uživatel připojit z libovolného vybraného umístění. Po klepnutí na tlačítko **Vybrat** pojmenovaný ovládací prvek výběru sítě, který zobrazuje seznam pojmenovaných sítí, se otevře. V seznamu je také uvedeno, zda bylo síťové umístění označeno jako důvěryhodné. Pojmenované umístění nazývané **důvěryhodné IP adresy MFA** zahrnuje nastavení IP adresy, které lze konfigurovat na stránce nastavení služby vícefaktorového ověřování.

## <a name="what-you-should-know"></a>Co byste měli vědět

### <a name="when-is-a-location-evaluated"></a>Kdy je umístění vyhodnoceno?

Zásady podmíněného přístupu jsou vyhodnocovány v:Conditional Access Policies are evaluated when:

- Uživatel se zpočátku přihlásí k webové aplikaci, mobilní nebo desktopové aplikaci.
- Mobilní nebo desktopová aplikace, která používá moderní ověřování, používá obnovovací token k získání nového přístupového tokenu. Ve výchozím nastavení je tato kontrola jednou za hodinu.

Tato kontrola znamená, že pro mobilní a desktopové aplikace pomocí moderního ověřování by změna umístění byla zjištěna do jedné hodiny od změny umístění v síti. Pro mobilní a desktopové aplikace, které nepoužívají moderní ověřování, zásady se použijí na každém požadavku tokenu. Četnost požadavku se může lišit v závislosti na aplikaci. Podobně pro webové aplikace zásady se použije při počátečním přihlášení a je dobré pro životnost relace ve webové aplikaci. Vzhledem k rozdílům v životnosti relace mezi aplikacemi se bude lišit také doba mezi vyhodnocením zásad. Pokaždé, když aplikace požaduje nový přihlašovací token, zásady se použije.

Ve výchozím nastavení Azure AD vydává token na hodinovém základě. Po přesunutí z podnikové sítě je během jedné hodiny zásada vynucena pro aplikace používající moderní ověřování.

### <a name="user-ip-address"></a>IP adresa uživatele

IP adresa, která se používá při hodnocení zásad, je veřejná IP adresa uživatele. U zařízení v privátní síti není tato IP adresa klientskou IP adresou zařízení uživatele v síti intranet, ale adresou používanou sítí pro připojení k veřejnému internetu.

> [!WARNING]
> Pokud má vaše zařízení pouze adresu IPv6, není konfigurace podmínky umístění podporována.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Hromadné nahrávání a stahování pojmenovaných míst

Při vytváření nebo aktualizaci pojmenovaných umístění můžete pro hromadné aktualizace nahrát nebo stáhnout soubor CSV s rozsahy IP adres. Nahrávání nahradí rozsahy IP adres v seznamu rozsahy IP adres ze souboru. Každý řádek souboru obsahuje jeden rozsah IP adres ve formátu CIDR.

### <a name="cloud-proxies-and-vpns"></a>Cloudové proxy servery a virtuální ny

Při použití cloudu hostované proxy nebo VPN řešení, IP adresa Azure AD používá při vyhodnocování zásad je IP adresa proxy serveru. Hlavička X-Forwarded-For (XFF), která obsahuje veřejnou IP adresu uživatele, se nepoužívá, protože neexistuje žádné ověření, že pochází z důvěryhodného zdroje, takže by představovala metodu pro falšování IP adresy.

Pokud je na místě proxy cloud, lze použít zásadu, která se používá k vyžadování zařízení přidruženého k doméně, nebo deklarace uvnitř korpusu ze služby AD FS.

### <a name="api-support-and-powershell"></a>Podpora rozhraní API a PowerShell

Rozhraní API a Prostředí PowerShell ještě není podporováno pro pojmenované umístění nebo zásady podmíněného přístupu.

## <a name="next-steps"></a>Další kroky

- Pokud chcete vědět, jak nakonfigurovat zásady podmíněného přístupu, přečtěte si informace [o vyžadování vícefaktorové žádosti pro konkrétní aplikace s podmíněným přístupem služby Azure Active Directory](app-based-mfa.md).
- Pokud jste připraveni nakonfigurovat zásady podmíněného přístupu pro vaše prostředí, podívejte se na [osvědčené postupy pro podmíněný přístup ve službě Azure Active Directory](best-practices.md).

---
title: Co je podmínky umístění v Azure Active Directory podmíněného přístupu? | Dokumenty Microsoft
description: Další informace o použití podmínku umístění k řízení přístupu k vaší cloudové aplikace založené na síťovém umístění uživatele.
services: active-directory
keywords: podmíněný přístup k aplikacím, podmíněného přístupu s Azure AD, zabezpečený přístup k prostředkům společnosti, zásady podmíněného přístupu
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: e5924262d9965c4e6732f8c23031cd575dc7958d
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232308"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Co je podmínka umístění v Azure Active Directory podmíněného přístupu? 

S [podmíněného přístupu Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), můžete řídit způsob Autorizovaní uživatelé můžou používat vaše cloudové aplikace. Umístění podmínku zásady podmíněného přístupu umožňuje svázání ovládacích prvků nastavení přístupu k umístění v síti uživatelů.

Tento článek vám poskytne informace, které budete muset nakonfigurovat podmínky umístění. 

## <a name="locations"></a>Umístění

Azure AD umožňuje jednotné přihlašování na zařízení, aplikace a službám odkudkoli na veřejného Internetu. S podmínkou umístění můžete řídit přístup k vaší cloudové aplikace založené na síťovém umístění uživatele. Běžné případy použití pro podmínku umístění jsou:

- Vyžadování vícefaktorového ověřování pro uživatele, kteří používají službu, když jsou mimo firemní síti  

- Blokování přístupu pro uživatele, kteří používají službu z konkrétní zemích nebo oblastech. 

Umístění je štítek pro umístění v síti, buď představuje umístění s názvem nebo služby Multi-Factor authentication důvěryhodné IP adresy.


## <a name="named-locations"></a>Pojmenovaná umístění 

Pomocí pojmenovaného umístění můžete vytvořit logické skupiny rozsahů adres IP, jiných zemí a oblastí. 

Můžete získat přístup k vaší pojmenované umístění v **spravovat** části stránky podmíněného přístupu.

![Umístění](./media/active-directory-conditional-access-locations/02.png)

 


Pojmenované umístění má následující komponenty:

![Umístění](./media/active-directory-conditional-access-locations/42.png)

- **Název** -zobrazovaný název s názvem umístění.

- **Rozsahy IP adres** -rozsahy jeden nebo více IP adres ve formátu CIDR.

- **Označit jako důvěryhodné umístění** -příznak můžete nastavit pro pojmenované umístění k označení důvěryhodném umístění. Důvěryhodná umístění jsou obvykle oblastem sítě, které jsou řízené vaše IT oddělení. Kromě podmíněný přístup, pojmenovaná umístění důvěryhodných jsou také používány ochrany identit Azure a Azure AD sestavy zabezpečení ke snížení [falešně pozitivních](active-directory-reporting-risk-events.md#impossible-travel-to-atypical-locations-1).

- **Země nebo oblasti** – tato možnost umožňuje vybrat jednu nebo více zemi nebo oblast definovat pojmenované umístění. 

- **Zahrnout neznámé oblasti** – některé IP adresy, které nejsou namapované na konkrétní země. Tato možnost umožňuje zvolit, pokud tyto IP adresy by měl být součástí s názvem umístění. Pokud zásady pomocí pojmenovaného umístění by se měly používat k neznámým umístěním, může být kontrola.

Počet pojmenované umístění, které můžete konfigurovat, je omezené velikost je související objekt ve službě Azure AD. Můžete nakonfigurovat:

- Jednu s názvem umístění s až 1200 rozsahy IP adres.

- Maximálně 90 s názvem umístění s jeden rozsah IP adres přiřazené ke každému z nich.




## <a name="trusted-ips"></a>Důvěryhodné IP adresy

Můžete také nakonfigurovat reprezentující místní intranet vaší organizace v rozsahy IP adres [nastavení služby Multi-Factor authentication](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Tato funkce umožňuje nakonfigurovat až 50 rozsahy IP adres. Rozsahy IP adres jsou ve formátu CIDR. Další informace najdete v tématu [důvěryhodné IP adresy](authentication/howto-mfa-mfasettings.md#trusted-ips).  

Pokud máte důvěryhodné IP adresy nakonfigurované, se zobrazují jako **důvěryhodné IP adresy MFA** v seznamu místa pro podmínku umístění.   

### <a name="skipping-multi-factor-authentication"></a>Přeskočení vícefaktorového ověřování

Na stránce nastavení služby Multi-Factor authentication service můžete identifikovat uživatele podnikové sítě intranet výběrem **vynechat službu Multi-Factor authentication pro žádosti od federovaných uživatelů v mém intranetu**. Toto nastavení určuje, že uvnitř podnikové sítě deklarace identity, které se objeví službou AD FS, musí důvěryhodnou a používá k identifikaci uživatele, že je v podnikové síti. Další informace najdete v tématu [povolit funkci důvěryhodné IP adresy pomocí podmíněného přístupu](authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Po zkontrolování tuto možnost, včetně umístění s názvem **MFA důvěryhodné IP adresy** budou platit pro všechny zásady s tímto vybrané.

Pro mobilních a desktopových aplikací, které mají dlouhodobě trvání relace, se pravidelně znovu zhodnotí podmíněného přístupu. Výchozí hodnota je jednou za hodinu. Když uvnitř podnikové sítě deklarace identity je a jen vydané v době počáteční ověřování, Azure AD nemusí mít seznam důvěryhodných rozsahů IP adres. V takovém případě je obtížné určit, zda uživatel je stále v podnikové síti:

1. Zkontrolujte, jestli se IP adresa uživatele je v jednom důvěryhodné rozsahů IP adres.

2. Zkontrolujte, zda první tři oktety uživatele IP adresy odpovídají nejprve 3 oktety počáteční ověřování na IP adresu. IP adresa se porovná se počáteční ověřování, protože se jedná, kdy uvnitř podnikové sítě vydána deklarace identity, byl původně a umístění uživatele potvrzená.

Pokud oba kroky nezdaří, uživatel se považuje za na důvěryhodných adres IP.



## <a name="location-condition-configuration"></a>Konfigurace stavu umístění

Pokud nakonfigurujete podmínku umístění, máte možnost k rozlišení mezi:

- Libovolné umístění 
- Všechna důvěryhodná umístění
- Vybraná umístění

![Umístění](./media/active-directory-conditional-access-locations/01.png)

### <a name="any-location"></a>Libovolné umístění

Ve výchozím nastavení výběr **libovolného umístění** způsobí, že zásady má být použita pro všechny IP adresy, což znamená všechny adresy v síti Internet. Toto nastavení není omezen na IP adresy, které jste nakonfigurovali jako s názvem umístění. Když vyberete **libovolného umístění**, stále můžete vyloučit konkrétní umístění ze zásady. Můžete například použít zásady do všech umístění s výjimkou důvěryhodného umístění nastavit obor na všech umístěních, s výjimkou podnikové síti.

### <a name="all-trusted-locations"></a>Všechna důvěryhodná umístění

Tato možnost platí pro:

- Všechny umístění, které byly označeny jako důvěryhodné umístění
- **Důvěryhodné IP adresy MFA** (Pokud je nakonfigurováno)


### <a name="selected-locations"></a>Vybraná umístění

Pomocí této možnosti můžete vybrat jeden nebo více umístění s názvem. Pro zásady se toto nastavení použít uživatel potřebuje k připojení z vybraného umístění. Když kliknete na tlačítko **vyberte** otevře se ovládací prvek výběru pojmenované sítě, který zobrazí seznam pojmenované sítě. V seznamu také ukazuje, zda umístění v síti byl označen jako důvěryhodné. Pojmenované umístění nazývá **MFA důvěryhodné IP adresy** se používá k zahrnují nastavení IP adresy, které lze nakonfigurovat na stránce nastavení služby Multi-Factor authentication service.

## <a name="what-you-should-know"></a>Důležité informace

### <a name="when-is-a-location-evaluated"></a>Když je vyhodnocován umístění?

Zásady podmíněného přístupu se vyhodnocují při: 

- Uživatel se původně přihlásí do aplikace webové aplikace, mobilní nebo plochy. 

- Mobilní nebo desktopové aplikace, která používá moderní ověřování, použije token obnovení pro získání nového tokenu přístupu. Ve výchozím nastavení je to jednou za hodinu. 

To znamená pro mobilní a desktopové aplikace používající moderní ověřování, by být zjištěna změna v umístění v rámci hodinu změny umístění v síti. Pro mobilních a desktopových aplikací, které nepoužívají moderní ověřování se zásady použijí na každé žádosti o token. Frekvence žádosti může lišit v závislosti na aplikaci. Pro webové aplikace, podobně zásady platí při počáteční přihlášení a je vhodný pro doba platnosti relace ve webové aplikaci. Vzhledem k rozdílům v trvání relace napříč aplikacemi budou lišit čas mezi vyhodnocení zásad. Pokaždé, když aplikace požádá o nový token přihlašování, zásady se použijí.


Ve výchozím nastavení Azure AD vydá token hodinu. Po přesunutí vypnout podnikovou síť, v rámci hodiny zásady se vynucují pro aplikace používající moderní ověřování.


### <a name="user-ip-address"></a>IP adresa uživatele

IP adresu, která se používá v hodnocení zásad je veřejnou IP adresu uživatele. Pro zařízení v privátní síti to není IP adresa klienta uživatele zařízení v síti intranet, je adresa použitá sítě pro připojení do veřejného Internetu. 

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Hromadné odesílání a stahování z pojmenované umístění

Při vytváření nebo aktualizaci s názvem umístění pro hromadné aktualizace, je odeslání nebo stažení souboru CSV s rozsahy IP. Nahrávaný nahradí rozsahy IP v seznamu těch, které ze souboru. Každý řádek souboru obsahuje jeden rozsah IP adres ve formátu CIDR. 


### <a name="cloud-proxies-and-vpns"></a>Proxy cloudu a sítě VPN 

Pokud používáte server proxy hostovaný cloud nebo řešení sítě VPN, používá IP adresu služby Azure AD při vyhodnocování zásad je IP adresa proxy serveru. Záhlaví X-Forwarded-For (XFF), který obsahuje uživatele, které se veřejná IP adresa se nepoužívá, protože není k dispozici žádné ověření, které pocházejí z důvěryhodného zdroje, takže by prezentovat metodu pro faking IP adresu. 

Pokud je cloudový proxy v místě, zásadu, která je použita pro požadavek, je možné zařízení připojeného k doméně nebo uvnitř corpnet deklarace identity ze služby AD FS.



### <a name="api-support-and-powershell"></a>Podpora rozhraní API a prostředí PowerShell 

Rozhraní API a prostředí PowerShell ještě není podporována pro umístění s názvem, nebo pro zásady podmíněného přístupu.





## <a name="next-steps"></a>Další postup

- Pokud chcete vědět, jak konfigurovat zásadu podmíněného přístupu, najdete v článku [vyžadovat vícefaktorové ověřování pro konkrétní aplikace s podmíněným přístupem Azure Active Directory](active-directory-conditional-access-app-based-mfa.md).

- Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro prostředí, najdete v článku [osvědčené postupy pro podmíněný přístup v Azure Active Directory](active-directory-conditional-access-best-practices.md). 

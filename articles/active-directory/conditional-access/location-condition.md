---
title: Co je podmíněný přístup Azure Active Directory podmínky umístění? | Dokumenty Microsoft
description: Další informace o použití podmínka umístění pro řízení přístupu k vašim cloudovým aplikacím založené na síťovém umístění uživatele.
services: active-directory
keywords: podmíněný přístup pro aplikace, podmíněný přístup s Azure AD, zabezpečený přístup k prostředkům společnosti, zásady podmíněného přístupu
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: eeb12500a5ddfb95317b3d20b41acf12e3978bad
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39626518"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Co je podmínka umístění podmíněného přístupu Azure Active Directory? 

S [podmíněného přístupu Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md), můžete určit, jak Autorizovaní uživatelé můžou přistupovat k vašim cloudovým aplikacím. Podmínka umístění zásad podmíněného přístupu můžete spojit nastavení ovládacích prvků přístupu do síťových umístění vašich uživatelů.

Tento článek obsahuje informace, na kterých je nutné nakonfigurovat podmínka umístění. 

## <a name="locations"></a>Umístění

Azure AD umožňuje jednotné přihlašování na zařízení, aplikací a službám odkudkoli na veřejném Internetu. Podmínka umístění můžete řídit přístup k vašim cloudovým aplikacím založené na síťovém umístění uživatele. Běžné případy použití pro podmínku umístění jsou:

- Vyžadování vícefaktorového ověřování pro uživatele, kteří používají službu, když jsou mimo firemní síť  

- Blokuje přístup pro uživatele, kteří používají službu z určitých zemích nebo oblastech. 

Umístění je popisek pro umístění v síti, že buď představuje umístění s názvem nebo ověřování službou Multi-Factor Authentication důvěryhodné IP adresy.


## <a name="named-locations"></a>Pojmenovaná umístění 

Pojmenovaná umístění vytvoříte logické skupiny rozsahů adres IP, zemích a oblastech. 

Můžete přistupovat k vaší pojmenovaná umístění v **spravovat** část stránky podmíněného přístupu.

![Umístění](./media/location-condition/02.png)

 


Pojmenované umístění má následující komponenty:

![Umístění](./media/location-condition/42.png)

- **Název** – zobrazovaný název pojmenované umístění.

- **Rozsahy IP adres** – rozsahy jeden nebo více IP adres ve formátu CIDR.

- **Označit jako důvěryhodné umístění** -příznak, který můžete nastavit pro pojmenované umístění pro důvěryhodného umístění. Důvěryhodná umístění jsou obvykle oblastem sítě, které jsou řízené vaším IT oddělením. Kromě podmíněného přístupu, důvěryhodné pojmenovaná umístění jsou také používány zprávy o zabezpečení Azure Identity Protection a Azure AD ke snížení [počet falešně pozitivních výsledků](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1).

- **Země / oblasti** – tato možnost umožňuje vybrat jeden nebo více zemi nebo oblast pro definování pojmenovaných umístění. 

- **Včetně neznámých oblastí** – některé IP adresy, které nejsou namapované na konkrétní zemi. Tato možnost umožňuje zvolit, pokud se tyto IP adresy, měly by být součástí pojmenované umístění. Uplatnění zásad, pomocí pojmenovaných umístění na neznámé umístění, může být kontrola.

Počet pojmenovaná umístění, které můžete nakonfigurovat je omezen velikostí související objekt ve službě Azure AD. Můžete nakonfigurovat:

- Jeden s názvem umístění s až 1200 rozsahy IP adres.

- Maximálně 90 pojmenovaná umístění s jeden rozsah IP adres přiřazené ke každému z nich.




## <a name="trusted-ips"></a>Důvěryhodné IP adresy

Můžete taky nakonfigurovat představující vaše organizace místní intranet v rozsahy IP adres [nastavení služby Multi-Factor authentication](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Tato funkce umožňuje konfigurovat až 50 rozsahy IP adres. Rozsahy IP adres jsou ve formátu CIDR. Další informace najdete v tématu [důvěryhodné IP adresy](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Pokud mají důvěryhodné IP adresy nakonfigurované, zobrazí se jako **důvěryhodné IP adresy MFA** v seznamu umístění pro umístění podmínku.   

### <a name="skipping-multi-factor-authentication"></a>Přeskočení ověřování služby Multi-Factor Authentication

Na stránce nastavení služby Multi-Factor authentication service můžete identifikovat podnikové uživatele tak, že vyberete **přeskočit ověřování službou Multi-Factor Authentication pro žádosti od federovaných uživatelů v mém intranetu**. Toto nastavení určuje, že uvnitř podnikové sítě deklarace identity, které vystaví služba AD FS, musí důvěryhodnou a používá se k identifikaci uživatelů jako v podnikové síti. Další informace najdete v tématu [povolit funkci důvěryhodné IP adresy pomocí podmíněného přístupu](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Po kontrole tuto možnost, včetně pojmenovaných umístění **důvěryhodné IP adresy MFA** budou platit pro všechny zásady s tímto vybrali.

Pro mobilní i desktopové aplikace, které mají dlouhodobě doby trvání relace, je pravidelně znovu zhodnotí podmíněný přístup. Výchozí hodnota je jednou za hodinu. Když uvnitř je deklarace identity podnikové sítě a jen pro vydání v době počáteční ověřování Azure AD nemůže mít seznam Důvěryhodné rozsahy IP adres. V takovém případě je obtížnější k určení, zda uživatel je stále v podnikové síti:

1. Zkontrolujte, jestli je IP adresa uživatele v jednom z důvěryhodné rozsahy IP adres.

2. Zkontrolujte, zda první tři oktety IP adresa uživatele odpovídá první 3 oktety IP adresu počáteční ověřování. IP adresa je ve srovnání s počáteční ověřování, protože se jedná, kdy uvnitř deklarace identity podnikové sítě byl původně vydaný a umístění uživatele byl ověřen.

Pokud se oba kroky nezdaří, uživatel se považuje za už za důvěryhodné IP adresy.



## <a name="location-condition-configuration"></a>Konfigurace podmínky umístění

Při konfiguraci umístění podmínky, máte možnost k rozlišení mezi:

- Libovolné umístění 
- Všechna důvěryhodná umístění
- Vybraná umístění

![Umístění](./media/location-condition/01.png)

### <a name="any-location"></a>Libovolné umístění

Ve výchozím nastavení že vyberete **libovolného umístění** způsobí, že se zásady použijí pro všechny IP adresy, což znamená, že jakákoli adresa v síti Internet. Toto nastavení není omezený na IP adresy, které jste nakonfigurovali jako pojmenované umístění. Když vyberete **libovolného umístění**, stále můžete vyloučit konkrétní umístění na základě zásady. Můžete například použít zásady ke všem místům s výjimkou důvěryhodných umístění k nastavení oboru ke všem místům, s výjimkou v podnikové síti.

### <a name="all-trusted-locations"></a>Všechna důvěryhodná umístění

Tato možnost platí pro:

- Všechna místa, které jsou označené jako důvěryhodné umístění
- **Důvěryhodné IP adresy MFA** (je-li konfigurováno)


### <a name="selected-locations"></a>Vybraná umístění

Pomocí této možnosti můžete vybrat jeden nebo více pojmenovaná umístění. Pro zásadu pomocí tohoto nastavení můžete použít uživatel potřebuje pro připojení z kterékoli z vybraného umístění. Po kliknutí na **vyberte** otevře pojmenované sítě ovládacího prvku pro výběr, který zobrazuje seznam pojmenované sítě. Také v seznamu uvedena, pokud umístění v síti byl označen jako důvěryhodné. Volá se umístění s názvem **důvěryhodné IP adresy MFA** je použít k zahrnutí nastavení IP adresy, které lze nastavit na stránce nastavení služby Multi-Factor authentication.

## <a name="what-you-should-know"></a>Co byste měli vědět

### <a name="when-is-a-location-evaluated"></a>Když je vyhodnocen na místě?

Vyhodnotí se zásady podmíněného přístupu při: 

- Zpočátku přihlášení uživatele k webové aplikaci app, mobily nebo stolní počítače. 

- Desktopové nebo mobilní aplikaci, která používá moderní ověřování, obnovovací token používá k získání nového tokenu přístupu. Ve výchozím nastavení je to jednou za hodinu. 

To znamená, že pro mobilní a desktopové aplikace používající moderní ověřování, změna umístění by se zjistilo během hodiny po změně umístění v síti. Pro mobilní i desktopové aplikace, které nepoužívají moderní ověřování které zásady platí v každé žádosti o token. Frekvence žádosti může lišit v závislosti na aplikaci. Pro webové aplikace podobně zásady platí při počáteční přihlášení a je vhodný pro po dobu platnosti relace webové aplikace. Z důvodu rozdílů v doby trvání relace mezi aplikacemi se také liší čas mezi vyhodnocení zásad. Pokaždé, když aplikace požádá o nový token přihlášení, které zásady platí.


Ve výchozím nastavení Azure AD vydá token po hodinách. Po přesunutí mimo podnikovou síť, do jedné hodiny zásady se vynucují pro aplikace používající moderní ověřování.


### <a name="user-ip-address"></a>IP adresy uživatele

IP adresa, která se používá v hodnocení zásad je veřejnou IP adresu uživatele. Pro zařízení v privátní síti to není IP adresa klienta zařízení uživatele v síti intranet, je adresa použitá v síti se připojit k veřejnému Internetu. 

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Hromadné nahrávání a stahování pojmenovaná umístění

Při vytvoření nebo aktualizaci pojmenovaná umístění pro hromadné aktualizace je odeslání nebo stažení souboru CSV s rozsahy IP adres. Nahrání nahradí rozsahy IP adres v seznamu těch ze souboru. Každý řádek souboru obsahuje jeden rozsah IP adres ve formátu CIDR. 


### <a name="cloud-proxies-and-vpns"></a>Cloudová proxy servery a sítěmi VPN 

Pokud používáte proxy server hostované v cloudu nebo řešení sítě VPN, IP adresu služby Azure AD používá při vyhodnocování zásad je IP adresa proxy serveru. Záhlaví X-Forwarded-For (XFF), která obsahuje uživatele, veřejné IP adresy se nepoužívá, protože není k dispozici žádné ověření, který pochází z důvěryhodného zdroje, takže by prezentovat metodu faking IP adresu. 

Když cloudový proxy server je v místě, zásadu, která se používá k vyžadovat zařízení připojené k doméně je možné nebo uvnitř podnikové sítě deklarací ze služby AD FS.



### <a name="api-support-and-powershell"></a>Podpora rozhraní API a Powershellu 

Rozhraní API a PowerShell není dosud podporována pro pojmenovaná umístění, nebo pro zásady podmíněného přístupu.





## <a name="next-steps"></a>Další postup

- Pokud chcete vědět, jak nakonfigurovat zásady podmíněného přístupu najdete v tématu [vyžadovat vícefaktorové ověřování pro konkrétní aplikace s podmíněným přístupem Azure Active Directory](app-based-mfa.md).

- Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro vaše prostředí, najdete v článku [osvědčené postupy pro podmíněný přístup ve službě Azure Active Directory](best-practices.md). 

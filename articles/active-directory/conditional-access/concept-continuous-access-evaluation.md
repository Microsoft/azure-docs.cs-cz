---
title: Vyhodnocování průběžného přístupu v Azure AD
description: Rychlejší reakce na změny v stavech uživatelů pomocí vyhodnocení průběžného přístupu v Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74009759bb9ca2a0516148fc1387b150b67452ab
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387900"
---
# <a name="continuous-access-evaluation"></a>Nepřetržité vyhodnocování přístupu

Vypršení platnosti tokenu a aktualizace je standardní mechanismus v oboru. Když se klientská aplikace, jako je Outlook, připojí ke službě, jako je Exchange Online, požadavky rozhraní API se autorizují pomocí přístupových tokenů OAuth 2,0. Ve výchozím nastavení jsou tyto přístupové tokeny platné po dobu jedné hodiny, kdy jejich platnost vyprší, klient se přesměruje zpátky do služby Azure AD, aby je aktualizoval. Tato doba aktualizace nabízí možnost znovu vyhodnotit zásady pro přístup uživatelů. Příklad: můžeme se rozhodnout neaktualizovat token z důvodu zásady podmíněného přístupu nebo proto, že uživatel byl v adresáři zakázán. 

Zákazníci vyjádřili obavy ohledně prodlevy mezi tím, kdy se u uživatele mění podmínky, jako je síťové umístění nebo odcizení přihlašovacích údajů, a když můžete zásady uplatnit v souvislosti s touto změnou. Povedli jsme experimenty s přístupem "tupý objekt" s omezenou životností tokenu, ale zjistili jsme, že můžou snížit činnost uživatelů a spolehlivost bez eliminace rizik.

Včasná reakce na porušení zásad nebo problémy se zabezpečením vyžadují "konverzaci" mezi vystavitelem tokenu, jako je Azure AD, a předávající stranou, jako je Exchange Online. Tato obousměrná konverzace nabízí dvě důležité možnosti. Předávající strana si může všimnout, že se změnily nějaké věci, třeba když klient přichází z nového umístění, a sdělí vystaviteli tokenů. Také dává vydavateli tokenů způsob, jak říct předávající straně, aby zastavila dodržování tokenů pro daného uživatele z důvodu ohrožení bezpečnosti, zákazu nebo jiných obav. Mechanismus pro tuto konverzaci je vyhodnocování nepřetržitého přístupu (CAE). Cílem je, aby odpověď byla prakticky v reálném čase, ale v některých případech může být v důsledku doby šíření událostí zjištěna latence až 15 minut.

Počáteční implementace vyhodnocení průběžného přístupu se zaměřuje na Exchange, týmy a SharePoint Online.

Pokud chcete připravit své aplikace na používání CAE, přečtěte si téma [Jak používat rozhraní API s povoleným vyhodnocováním průběžného přístupu ve vašich aplikacích](../develop/app-resilience-continuous-access-evaluation.md).

### <a name="key-benefits"></a>Klíčové výhody

- Ukončení uživatele nebo změna nebo resetování hesla: odvolání uživatelské relace bude vynucovat téměř v reálném čase.
- Změna umístění v síti: zásady umístění podmíněného přístupu se vynutily téměř v reálném čase.
- Export tokenu do počítače mimo důvěryhodnou síť je možné zabránit pomocí zásad umístění podmíněného přístupu.

## <a name="scenarios"></a>Scénáře 

Existují dva scénáře, které vytvářejí vyhodnocování průběžného přístupu, kritické vyhodnocení událostí a vyhodnocení zásad podmíněného přístupu.

### <a name="critical-event-evaluation"></a>Kritické vyhodnocení událostí

Vyhodnocování průběžného přístupu se implementuje povolením služeb, jako je Exchange Online, SharePoint Online a týmy, pro přihlášení k odběru kritických událostí ve službě Azure AD, aby se tyto události vyhodnotily a vynutily téměř v reálném čase. Kritické vyhodnocení událostí nespoléhá na zásady podmíněného přístupu, takže jsou k dispozici v jakémkoli tenantovi. V tuto chvíli jsou vyhodnoceny následující události:

- Uživatelský účet je odstraněný nebo zakázaný.
- Heslo pro uživatele se změnilo nebo resetuje.
- Pro uživatele je povoleno Multi-Factor Authentication.
- Správce explicitně odvolá všechny aktualizační tokeny pro uživatele.
- Bylo zjištěno vysoké riziko uživatele Azure AD Identity Protection

Tento proces umožňuje situaci, kdy uživatelé ztratí přístup k souborům SharePointu Online, e-mailu, kalendáři nebo úlohám a týmům z Microsoft 365 klientských aplikací v minutách po jedné z těchto kritických událostí. 

> [!NOTE] 
> Týmy a SharePoint Online ještě nepodporují rizikové události uživatele.

### <a name="conditional-access-policy-evaluation-preview"></a>Vyhodnocení zásad podmíněného přístupu (Preview)

Exchange a SharePoint jsou schopné synchronizovat klíčová pravidla podmíněného přístupu, aby je bylo možné vyhodnotit v rámci samotné služby.

Tento proces umožňuje situaci, kdy uživatelé ztratí přístup k firemním souborům, e-mailu, kalendáři nebo úlohám z Microsoft 365 klientských aplikací nebo SharePointu Online hned po změně umístění v síti.

> [!NOTE]
> Není podporovaná kombinace všech aplikací a poskytovatelů prostředků. Viz tabulka níže. Office odkazuje na Word, Excel a PowerPoint.

| | Outlook Web | Outlook Win32 | Outlook iOS | Outlook Android | Outlook Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Podporováno | Podporováno | Podporováno | Podporováno | Podporováno |
| **Exchange Online** | Podporováno | Podporováno | Podporováno | Podporováno | Podporováno |

| | Webové aplikace Office | Aplikace Office Win32 | Office pro iOS | Office pro Android | Office pro Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Nepodporuje se | Podporováno | Podporováno | Podporováno | Podporováno |
| **Exchange Online** | Nepodporuje se | Podporováno | Podporováno | Podporováno | Podporováno |

| | Web OneDrive | OneDrive Win32 | OneDrive iOS | OneDrive – Android | OneDrive – Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Podporováno | Podporováno | Podporováno | Podporováno | Podporováno |

### <a name="client-side-claim-challenge"></a>Výzva k deklaracím na straně klienta

Před vyhodnocením průběžného přístupu by se klienti vždy pokusili přehrát přístupový token z mezipaměti, dokud nevypršela jeho platnost. V CAE zavádíme nový případ, který poskytovatel prostředků může odmítnout token i v případě, že nevypršela jeho platnost. Abychom informovali, že klienti mají obejít mezipaměť i v případě, že nevypršela platnost tokenů uložených v mezipaměti, zavádíme mechanismus s názvem **Challenge Challenge** , který indikuje, že token byl zamítnutý a že Azure AD musí vystavit nový přístupový token. CAE vyžaduje aktualizaci klienta pro pochopení výzvy deklarací identity. Výzva pro deklaraci identity podporuje nejnovější verzi následujících aplikací:

| | Web | Win32 | iOS | Android | Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Outlook** | Podporováno | Podporováno | Podporováno | Podporováno | Podporováno |
| **Teams** | Podporováno | Podporováno | Podporováno | Podporováno | Podporováno |
| **Office** | Nepodporuje se | Podporováno | Podporováno | Podporováno | Podporováno |
| **OneDrive** | Podporováno | Podporováno | Podporováno | Podporováno | Podporováno |

### <a name="token-lifetime"></a>Životnost tokenu

Vzhledem k tomu, že rizika a zásady jsou vyhodnocovány v reálném čase, klienti, kteří vyjednají relace s průběžným vyhodnocováním přístupu, se budou spoléhat na CAE namísto existujících zásad životnosti statického přístupového tokenu, což znamená, že konfigurovatelné zásady životního cyklu tokenu již nebudou přijaty pro klienty podporující CAE, kteří vyjednávají

Životnost tokenu se během relací CAE zvýšila na dlouhou dobu a až 28 hodin. Odvolání je založené na kritických událostech a vyhodnocování zásad, nikoli jenom v libovolném časovém období. Tato změna zvyšuje stabilitu aplikací, aniž by to ovlivnilo stav zabezpečení. 

Pokud nepoužíváte klienty podporující CAE, vaše výchozí doba platnosti přístupového tokenu zůstane 1 hodina, pokud jste nenakonfigurovali dobu života přístupového tokenu pomocí funkce [(CTL)](../develop/active-directory-configurable-token-lifetimes.md) ve verzi Preview.

## <a name="example-flows"></a>Příklady toků

### <a name="user-revocation-event-flow"></a>Tok událostí odvolání uživatele:

![Tok událostí odvolaných uživatelem](./media/concept-continuous-access-evaluation/user-revocation-event-flow.png)

1. Klient podporující CAE prezentuje přihlašovací údaje nebo obnovovací token pro Azure AD, který žádá o přístupový token pro určitý prostředek.
1. Přístupový token je vrácen spolu s jinými artefakty klientovi.
1. Správce explicitně [odvolá všechny aktualizační tokeny pro uživatele](/powershell/module/azuread/revoke-azureaduserallrefreshtoken). Do poskytovatele prostředků z Azure AD se pošle událost odvolání.
1. Poskytovateli prostředků se zobrazí přístupový token. Poskytovatel prostředků vyhodnocuje platnost tokenu a zkontroluje, jestli pro uživatele existuje nějaká událost odvolání. Poskytovatel prostředků používá tyto informace k rozhodnutí o udělení přístupu k prostředku nebo ne.
1. V takovém případě poskytovatel prostředků odepře přístup a pošle klientovi výzvu s deklarací 401 +.
1. Klient podporující CAE rozumí výzvě 401 + Claim. Obchází mezipaměti a vrací se ke kroku 1, odesílá obnovovací token spolu s výzvou k deklaraci identity zpátky do služby Azure AD. Služba Azure AD potom znovu vyhodnotí všechny podmínky a vyzve uživatele k opětovnému ověření v tomto případě.

### <a name="user-condition-change-flow-preview"></a>Tok změn stavu uživatele (Preview):

V následujícím příkladu má správce podmíněného přístupu nakonfigurovanou zásadu podmíněného přístupu na základě umístění, aby povolovala přístup jenom z konkrétních rozsahů IP adres:

![Tok událostí uživatelské podmínky](./media/concept-continuous-access-evaluation/user-condition-change-flow.png)

1. Klient podporující CAE prezentuje přihlašovací údaje nebo obnovovací token pro Azure AD, který žádá o přístupový token pro určitý prostředek.
1. Azure AD vyhodnocuje všechny zásady podmíněného přístupu a zjistí, jestli uživatel a klient splňují podmínky.
1. Přístupový token je vrácen spolu s jinými artefakty klientovi.
1. Uživatel se přesune mimo povolený rozsah IP adres.
1. Klient prezentuje přístupový token poskytovateli prostředků mimo povolený rozsah IP adres.
1. Poskytovatel prostředků vyhodnocuje platnost tokenu a zkontroluje zásady umístění synchronizované z Azure AD.
1. V takovém případě poskytovatel prostředků odmítne přístup a pošle výzvu 401 + nárok zpátky klientovi, protože nepřichází z povoleného rozsahu IP adres.
1. Klient podporující CAE rozumí výzvě 401 + Claim. Obchází mezipaměti a vrací se ke kroku 1, odesílá obnovovací token spolu s výzvou k deklaraci identity zpátky do služby Azure AD. Azure AD znovu vyhodnotí všechny podmínky a v tomto případě bude odepřen přístup.

## <a name="enable-or-disable-cae-preview"></a>Povolit nebo zakázat CAE (Preview)

1. Přihlaste se k **Azure Portal** jako správce podmíněného přístupu, správce zabezpečení nebo globální správce.
1. Přejděte na **Azure Active Directory**  >    >  **vyhodnocování průběžného přístupu** zabezpečení.
1. Vyberte možnost **Povolit náhled**.

Na této stránce můžete volitelně omezit uživatele a skupiny, které budou předmětem verze Preview.

![Povolení CAE ve verzi Preview v Azure Portal](./media/concept-continuous-access-evaluation/enable-cae-preview.png)

## <a name="troubleshooting"></a>Řešení potíží

### <a name="supported-location-policies"></a>Podporované zásady umístění

V případě CAE máme přehledy o pojmenovaných umístěních založených na protokolu IP. Neposkytujeme žádné informace o dalších nastaveních umístění, jako jsou [důvěryhodné IP adresy MFA](../authentication/howto-mfa-mfasettings.md#trusted-ips) nebo umístění na bázi země. Když uživatel pochází z důvěryhodné IP adresy MFA nebo z důvěryhodných umístění, která zahrnují důvěryhodné IP adresy nebo umístění země, CAE se po přesunu uživatele do jiného umístění neuplatní. V těchto případech budeme vydávat 1 hodinový token CAE bez kontroly vynucení okamžité IP adresy.

> [!IMPORTANT]
> Při konfiguraci umístění pro vyhodnocení průběžného přístupu použijte jenom [podmínku umístění podmíněného přístupu na základě IP adresy](../conditional-access/location-condition.md) a NAKONFIGURUJTE všechny IP adresy, **včetně IPv4 i IPv6**, které může vidět poskytovatel identity a poskytovatel prostředků. Nepoužívejte podmínky umístění země ani funkci důvěryhodných IP adres, která je k dispozici na stránce nastavení služby Azure AD Multi-Factor Authentication.

### <a name="ip-address-configuration"></a>Konfiguraci IP adresy

Poskytovatelé identity a poskytovatelé prostředků můžou zobrazovat různé IP adresy. Tato neshoda může nastat kvůli implementaci síťových proxy serverů ve vaší organizaci nebo nesprávných konfiguracích protokolu IPv4/IPv6 mezi poskytovatelem identity a poskytovatelem prostředků. Například:

- Poskytovatel identity uvidí jednu IP adresu z klienta.
- Poskytovatel prostředků uvidí od klienta jinou IP adresu po předání prostřednictvím proxy serveru.
- IP adresa, kterou poskytovatel identity uvidí, je součástí povoleného rozsahu IP adres v zásadě, ale IP adresa od poskytovatele prostředků není.

Pokud tento scénář existuje ve vašem prostředí, aby se předešlo nekonečným smyčkám, Azure AD vydá jeden CAE token a nevyhodnotí změnu umístění klienta. I v tomto případě se zvyšuje zabezpečení v porovnání s tradičními tokeny hodin, protože stále ještě vyhodnocujeme [Další události](#critical-event-evaluation) kromě událostí změny umístění klienta.

### <a name="office-and-web-account-manager-settings"></a>Nastavení správce účtů pro Office a Web

| Kanál aktualizace Office | DisableADALatopWAMOverride | DisableAADWAM |
| --- | --- | --- |
| Semi-Annual podnikového kanálu | Pokud je nastavené na povoleno nebo 1, CAE se nepodporuje. | Pokud je nastavené na povoleno nebo 1, CAE se nepodporuje. |
| Aktuální kanál <br> nebo <br> Měsíční podnikový kanál | CAE se podporuje bez ohledu na nastavení. | CAE se podporuje bez ohledu na nastavení. |

Vysvětlení kanálů aktualizace Office najdete v tématu [Přehled kanálů aktualizací pro aplikace Microsoft 365](/deployoffice/overview-update-channels). Doporučuje se, aby organizace nezakázal správce webových účtů (WAM).

### <a name="group-membership-and-policy-update-effective-time"></a>Efektivní čas aktualizace členství ve skupinách a zásad

Členství ve skupinách a aktualizace zásad provedené správci můžou trvat až jeden den. Některá optimalizace byla provedena pro aktualizace zásad, které omezují zpoždění na dvě hodiny. Ale nepokrývá všechny scénáře ještě předtím. 

Pokud dojde k naléhavosti a potřebujete aktualizovat zásady nebo změnit členství ve skupině na konkrétní uživatele okamžitě, měli byste použít tento [příkaz PowerShellu](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) nebo "odvolat relaci" na stránce profilu uživatele k odvolání relace uživatelů. tím se zajistí, že se aktualizované zásady použijí okamžitě.

### <a name="coauthoring-in-office-apps"></a>Spoluvytváření v aplikacích Office

Když na stejném dokumentu současně spolupracuje více uživatelů, přístup uživatele k dokumentu nemusí okamžitě odvolat CAE na základě odvolání nebo událostí změny zásad uživatele. V takovém případě uživatel ztratí přístup kompletně po uzavření dokumentu, zavření aplikace Word, Excelu nebo PowerPointu nebo po uplynutí 10 hodin.

Pro snížení této doby může správce SharePointu volitelně zkrátit maximální dobu života spoluvytváření relací pro dokumenty uložené na SharePointu Online a OneDrivu pro firmy [konfigurací zásady síťového umístění v SharePointu Online](/sharepoint/control-access-based-on-network-location). Po změně této konfigurace se maximální doba života relací spoluvytváření sníží na 15 minut a dá se dál upravit pomocí příkazu PowerShellu pro SharePoint Online set-SPOTenant – IPAddressWACTokenLifetime.

### <a name="enable-after-a-user-is-disabled"></a>Povolit po zakázání uživatele

Pokud uživatele povolíte ihned po jeho zakázání. Než bude možné účet povolit, dojde k určité latenci. SPO a týmy budou mít zpoždění 15 minut. Zpoždění je 35-40 minut pro EXO.

## <a name="faqs"></a>Nejčastější dotazy

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Jak bude CAE pracovat s frekvencí přihlášení?

Frekvence přihlašování se bude respektovat s CAE nebo bez ní.

## <a name="next-steps"></a>Další kroky

[Oznamujeme Průběžné vyhodnocování přístupu.](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)

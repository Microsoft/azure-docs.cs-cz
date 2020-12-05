---
title: Řešení potíží s jednotným přihlašováním založeném na heslech v Azure Active Directory
description: Řešení potíží s aplikací Azure AD, která je nakonfigurovaná pro jednotné přihlašování založené na heslech
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: d122cedbad41d6984614a0edccb2fd98269710f2
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618072"
---
# <a name="troubleshoot-password-based-single-sign-on-in-azure-ad"></a>Řešení potíží s jednotným přihlašováním založeným na heslech v Azure AD

Pokud chcete v aplikacích používat jednotné přihlašování (SSO) založené na heslech, musí být nainstalované rozšíření prohlížeče. Rozšíření se automaticky stáhne při výběru aplikace, která je nakonfigurovaná pro jednotné přihlašování založené na heslech. Další informace o používání mých aplikací z perspektivy koncových uživatelů najdete v [nápovědě k portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="my-apps-browser-extension-not-installed"></a>Rozšíření prohlížeče moje aplikace není nainstalované.
Ujistěte se, že je nainstalované rozšíření prohlížeče. Další informace najdete v tématu [Plánování nasazení Azure Active Directory moje aplikace](access-panel-deployment-plan.md). 

## <a name="single-sign-on-not-configured"></a>Jednotné přihlašování není nakonfigurované.
Ujistěte se, že je nakonfigurováno jednotné přihlašování založené na heslech. Další informace najdete v tématu [Konfigurace jednotného přihlašování založeného na heslech](configure-password-single-sign-on-non-gallery-applications.md).

## <a name="users-not-assigned"></a>Uživatelé nejsou přiřazeni.
Ujistěte se, že je uživatel přiřazený k aplikaci. Další informace najdete v tématu [přiřazení uživatele nebo skupiny k aplikaci](assign-user-or-group-access-portal.md).

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Přihlašovací údaje jsou vyplněné, ale rozšíření je neodešle

K tomuto problému obvykle dochází v případě, že dodavatel aplikace změnil svou přihlašovací stránku nedávno, aby přidal pole, změnil identifikátor používaný k detekci polí uživatelského jména a hesla nebo změnil způsob, jakým funguje přihlašování pro aplikaci. Naštěstí může společnost Microsoft v mnoha instancích spolupracovat s dodavateli aplikací a rychle tyto problémy vyřešit.

I když má společnost Microsoft technologie, aby automaticky zjistily, kdy integrace přeruší, nemusí být možné tyto problémy najít hned, jinak problémy vyřeší. V případě, že některá z těchto integrací nefunguje správně, otevřete případ podpory, aby bylo možné je opravit co nejrychleji.

**Pokud se obrátíte na dodavatele této aplikace,** pošlete jim náš způsob, aby Microsoft mohl s nimi pracovat, aby nativně integroval svou aplikaci s Azure Active Directory. Dodavatele můžete odeslat do [výpisu vaší aplikace v Azure Active Directory Galerie aplikací](../develop/v2-howto-app-gallery-listing.md) , abyste je mohli začít.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Přihlašovací údaje jsou vyplněné a odeslané, ale na stránce se zobrazuje, že jsou přihlašovací údaje nesprávné

Chcete-li tento problém vyřešit, vyzkoušejte nejprve tyto věci:

- Nejdřív se uživatel pokusí **přihlásit k webu aplikace přímo** s přihlašovacími údaji, které jsou pro ně uložené.

  * Pokud se přihlásíte, pak uživatel musí kliknout na tlačítko **Aktualizovat přihlašovací údaje** na **dlaždici aplikace** **v části aplikace v** části [Moje aplikace](https://myapps.microsoft.com/) , aby je aktualizovali na nejnovější známé funkční uživatelské jméno a heslo.

  * Pokud jste vy nebo jiný správce přiřadili přihlašovací údaje pro tohoto uživatele, vyhledejte přiřazení aplikace uživateli nebo skupině, a to tak, že přejdete na kartu **uživatelé & skupiny** aplikace, vyberete přiřazení a kliknete na tlačítko **Aktualizovat přihlašovací údaje** .

- Pokud uživatel přiřadil svoje vlastní přihlašovací údaje, musí **zkontrolovat, jestli v aplikaci nevypršela platnost hesla** , a pokud ano, **aktualizovat heslo s vypršenou platností** tak, že se přihlásí přímo k aplikaci.

  * Až se v aplikaci aktualizuje heslo, požádejte uživatele, aby po kliknutí na tlačítko **Aktualizovat přihlašovací údaje** na **dlaždici aplikace** **v části aplikace v** okně [Moje aplikace](https://myapps.microsoft.com/) aktualizovali na nejnovější známé funkční uživatelské jméno a heslo.

  * Pokud jste vy nebo jiný správce přiřadili přihlašovací údaje pro tohoto uživatele, vyhledejte přiřazení aplikace uživateli nebo skupině, a to tak, že přejdete na kartu **uživatelé & skupiny** aplikace, vyberete přiřazení a kliknete na tlačítko **Aktualizovat přihlašovací údaje** .

- Zajistěte, aby v prohlížeči uživatele běželo rozšíření prohlížeče moje aplikace a bylo povolené.

- Ujistěte se, že se uživatelé nesnaží přihlásit k aplikaci z mých aplikací v **anonymním, InPrivate nebo privátním režimu**. Rozšíření moje aplikace se v těchto režimech nepodporuje.

V případě, že předchozí návrhy nefungují, může to být případ, kdy došlo ke změně na straně aplikace, která dočasně přerušila integraci aplikace s Azure AD. K tomu může dojít například v případě, že dodavatel aplikace zavádí skript na své stránce, který se chová jinak jako ruční vs automatizované zadání, což způsobí, že automatizovaná integrace, jako je naše vlastní, přeruší. Naštěstí může společnost Microsoft v mnoha instancích spolupracovat s dodavateli aplikací a rychle tyto problémy vyřešit.

I když má společnost Microsoft technologie, aby automaticky zjistily, kdy integrace aplikace přeruší, nemusí být možné tyto problémy najít hned, jinak může opravit problémy. Pokud integrace nefunguje správně, můžete otevřít případ podpory, abyste ho mohli co nejrychleji opravit. 

V případě, že se **s dodavatelem této aplikace obrátíte,** **pošlete je našemu způsobu** , abychom s nimi mohli s nimi pracovat nativně integrací aplikace s Azure Active Directory. Dodavatele můžete odeslat do [výpisu vaší aplikace v Azure Active Directory Galerie aplikací](../develop/v2-howto-app-gallery-listing.md) , abyste je mohli začít.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Zkontroluje, jestli se přihlašovací stránka aplikace v poslední době změnila, nebo jestli vyžaduje další pole.

Pokud se přihlašovací stránka aplikace významně změnila, někdy to způsobí, že naše integrace přeruší. Příkladem je, že dodavatel aplikace přidá do svých prostředí přihlašovací pole, CAPTCHA nebo Multi-Factor Authentication. Naštěstí může společnost Microsoft v mnoha instancích spolupracovat s dodavateli aplikací a rychle tyto problémy vyřešit.

I když má společnost Microsoft technologie, aby automaticky zjistily, kdy integrace aplikace přeruší, nemusí být možné tyto problémy najít hned, jinak může opravit problémy. Pokud integrace nefunguje správně, můžete otevřít případ podpory, abyste ho mohli co nejrychleji opravit. 

V případě, že se **s dodavatelem této aplikace obrátíte,** **pošlete je našemu způsobu** , abychom s nimi mohli s nimi pracovat nativně integrací aplikace s Azure Active Directory. Dodavatele můžete odeslat do [výpisu vaší aplikace v Azure Active Directory Galerie aplikací](../develop/v2-howto-app-gallery-listing.md) , abyste je mohli začít.

## <a name="capture-sign-in-fields-for-an-app"></a>Zaznamenání přihlašovacích polí pro aplikaci

Zachytávání přihlašovacích polí se podporuje jenom pro přihlašovací stránky s povoleným HTML. Není podporováno pro nestandardní přihlašovací stránky, jako jsou ty, které používají Adobe Flash nebo jiné technologie, které nepodporují formát HTML.

Existují dva způsoby, jak zaznamenat přihlašovací pole pro vlastní aplikace:

- **Automatické zachycení přihlašovacích polí** funguje dobře s největší přihlašovací stránkou s povoleným HTML, pokud používají pro pole uživatelského jména a hesla *dobře známá ID div* . KÓD HTML na stránce je vyřazen, aby bylo možné najít ID DIV, která odpovídají určitým kritériím. Tato metadata se uloží, aby je bylo možné později znovu přehrát do aplikace.

- Pokud dodavatel aplikace *neoznačí vstupní pole přihlašování*, použije se **Ruční zachycení pole** . Ruční zachytávání se používá také v případě *, že dodavatel vykresluje více polí, která nelze automaticky zjistit*. Azure Active Directory (Azure AD) může ukládat data pro libovolný počet polí, která jsou na přihlašovací stránce, pokud se jim poříkáte, kde jsou tato pole na stránce.

Obecně platí, že pokud automatické zachycení přihlašovacích polí nefunguje, zkuste ruční možnost.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Automatické zachycení přihlašovacích polí pro aplikaci

Pokud chcete nakonfigurovat jednotné přihlašování založené na heslech pomocí automatického zachycení pole pro přihlášení, postupujte takto:
1. Otevřete web [Azure Portal](https://portal.azure.com/). Přihlaste se jako globální správce nebo spolusprávce.
2. V navigačním podokně na levé straně vyberte **všechny služby** a otevřete rozšíření Azure AD.
3. Do vyhledávacího pole filtru zadejte **Azure Active Directory** a pak vyberte **Azure Active Directory**.
4. V navigačním podokně Azure AD vyberte **podnikové aplikace** .
5. Výběrem **všech aplikací** zobrazíte seznam aplikací.
   > [!NOTE]
   > Pokud nevidíte aplikaci, kterou chcete, použijte ovládací prvek **filtru** v horní části seznamu **všechny aplikace** . Nastavte možnost **Zobrazit** na všechny aplikace.
6. Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.
7. Po načtení aplikace vyberte v navigačním podokně na levé straně **jednotné přihlašování** .
8. Vyberte režim **přihlašování založený na hesle** .
9. Zadejte **přihlašovací adresu URL**, což je adresa URL stránky, kde uživatelé zadají své uživatelské jméno a heslo, aby se mohli přihlásit. Ujistěte se *, že jsou na stránce zobrazena přihlašovací pole pro adresu URL, kterou zadáte*.
10. Vyberte **Uložit**.
    Tato stránka se automaticky vyřadí do vstupních polí uživatelské jméno a heslo. Teď můžete pomocí Azure AD bezpečně přenášet hesla do této aplikace pomocí rozšíření prohlížeče moje aplikace.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Ruční zachytávání přihlašovacích polí pro aplikaci

Pokud chcete ručně zachytit pole přihlašování, musíte mít nainstalované rozšíření prohlížeče moje aplikace. Prohlížeč také nemůže běžet ve službě *InPrivate*, *anonymním* nebo *privátním* režimu.

Pokud chcete pro aplikaci nakonfigurovat jednotné přihlašování na základě hesla pomocí ručního zachycení pole pro přihlášení, postupujte podle těchto kroků:
1. Otevřete web [Azure Portal](https://portal.azure.com/). Přihlaste se jako globální správce nebo spolusprávce.
2. V navigačním podokně na levé straně vyberte **všechny služby** a otevřete rozšíření Azure AD.
3. Do vyhledávacího pole filtru zadejte **Azure Active Directory** a pak vyberte **Azure Active Directory**.
4. V navigačním podokně Azure AD vyberte **podnikové aplikace** .
5. Výběrem **všech aplikací** zobrazíte seznam aplikací.
   > [!NOTE] 
   > Pokud nevidíte aplikaci, kterou chcete, použijte ovládací prvek **filtru** v horní části seznamu **všechny aplikace** . Nastavte možnost **Zobrazit** na všechny aplikace.
6. Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.
7. Po načtení aplikace vyberte v navigačním podokně na levé straně **jednotné přihlašování** .
8. Vyberte režim **přihlašování založený na hesle** .
9. Zadejte **přihlašovací adresu URL**, což je stránka, kde uživatelé zadají své uživatelské jméno a heslo, aby se mohli přihlásit. Ujistěte se *, že jsou na stránce zobrazena přihlašovací pole pro adresu URL, kterou zadáte*.
10. Vyberte **Konfigurovat nastavení jednotného přihlašování pro heslo *&lt; AppName &gt;***.
11. Vyberte možnost **ručně zjišťovat přihlašovací pole**.
14. Vyberte **OK**.
15. Vyberte **Uložit**.
16. Podle pokynů použijte moje aplikace.


## <a name="troubleshoot-problems"></a>Poradce při potížích

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Zobrazuje se chybová zpráva: nenašly se žádné přihlašovací pole na adrese URL.

Tato chybová zpráva se zobrazí, když se automatické zjišťování přihlašovacích polí nezdařilo. Chcete-li tento problém vyřešit, zkuste Ruční rozpoznávání polí pro přihlašování. Další informace najdete v části [Ruční zachytávání přihlašovacích polí pro aplikaci](#manually-capture-sign-in-fields-for-an-app) v tomto článku.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Zobrazí se chyba "nepovedlo se uložit konfiguraci jednotného přihlašování"

Zřídka se aktualizace konfigurace jednotného přihlašování nezdařila. Chcete-li tento problém vyřešit, zkuste konfiguraci uložit znovu.

Pokud se vám tato chyba bude zobrazovat, otevřete případ podpory. Přidejte informace, které jsou popsány v [podrobnostech oznámení o zobrazení portálu](#view-portal-notification-details) a [odeslání podrobností oznámení pracovníkovi podpory, aby získali nápovědu k](#send-notification-details-to-a-support-engineer-to-get-help) části tohoto článku.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Nejde ručně detekovat přihlašovací pole pro moji aplikaci

V případě, že ruční zjišťování nefunguje, můžete sledovat následující chování:
- Proces ručního zachytávání se zdá fungovat, ale zachycená pole nejsou správná.
- Při spuštění procesu zachycení nejsou správná pole zvýrazněna.
- Proces zachytávání vás přesměruje na přihlašovací stránku aplikace podle očekávání, ale nic se nestane.
- Zdá se, že ruční zachycení funguje, ale SSO se nestane, když uživatelé přejdou do aplikace z mých aplikací.

Pokud se setkáte s některým z těchto problémů, proveďte následující akce:
- Ujistěte se, že máte *nainstalovanou a povolenou* nejnovější verzi rozšíření prohlížeče moje aplikace.
- Ujistěte se, že během procesu zachycení není váš prohlížeč v *anonymním*, *InPrivate* nebo *privátním* režimu. Rozšíření moje aplikace se v těchto režimech nepodporuje.
- Ujistěte se, že se uživatelé nesnaží přihlašovat k aplikaci z mých aplikací v *anonymním*, *InPrivate* nebo v *privátním režimu*.
- Zkuste znovu zpracovat proces ručního zachycení. Ujistěte se, že červené značky jsou nad správnými poli.
- Pokud se zdá, že proces ručního zachytávání přestane reagovat nebo stránka pro přihlášení nereaguje, zkuste proces ručního zachytávání zopakovat. Tentokrát ale po dokončení procesu stiskněte klávesu F12 a otevřete konzolu pro vývojáře v prohlížeči. Vyberte kartu **Konzola** . zadejte **window. Location = "*&lt; přihlašovací adresa URL, kterou jste zadali při konfiguraci aplikace &gt;***, a potom stiskněte klávesu ENTER. Tím se vynutí přesměrování stránky, které ukončí proces zachytávání a uloží pole, která byla zachycena.

### <a name="i-cant-add-another-user-to-my-password-based-sso-app"></a>Nejde přidat dalšího uživatele do aplikace jednotného přihlašování založené na heslech

Aplikace jednotného přihlašování založená na heslech má omezení 48 uživatelů. Proto má omezení 48 klíčů pro dvojice uživatelského jména a hesla na aplikaci.
Pokud chcete přidat další uživatele, můžete:
-   Přidat další instanci aplikace
-   Odebrat uživatele, kteří už aplikaci nepoužívají

## <a name="request-support"></a>Požádat o podporu 
Pokud se zobrazí chybová zpráva, když nastavíte jednotné přihlašování a přiřadíte uživatele, otevřete lístek podpory. Co je možné zahrnout co nejvíc následujících informací:

-   ID chyby korelace
-   UPN (uživatelská e-mailová adresa)
-   TenantID
-   Typ prohlížeče
-   Časové pásmo a časový/časový rámec, kdy došlo k chybě
-   Fiddler trasování

### <a name="view-portal-notification-details"></a>Zobrazit podrobnosti oznámení na portálu

Chcete-li zobrazit podrobnosti o jakémkoli oznámení portálu, postupujte takto:
1. Vyberte ikonu **oznámení** (zvonek) v pravém horním rohu Azure Portal.
2. Vyberte jakékoli oznámení, které zobrazuje *chybový* stav. (Mají červenou "!".)
   > [!NOTE]
   > Nemůžete vybrat oznámení, která jsou v *úspěšném* nebo *probíhajícím* stavu.
3. Otevře se podokno **Podrobnosti oznámení** . Přečtěte si informace o tomto problému.
5. Pokud stále potřebujete pomoc, sdílejte tyto informace s pracovníkem podpory nebo produktovou skupinou. Kliknutím na ikonu **Kopírovat** napravo od pole **Chyba kopírování** zkopírujte podrobnosti oznámení ke sdílení.

### <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Odeslat podrobnosti oznámení pracovníkovi podpory a získat pomoc

Je důležité, abyste nasdíleli *všechny* podrobnosti uvedené v této části s podporou, aby vám mohli rychle pomoci. Pokud ho chcete zaznamenat, můžete si udělat snímek obrazovky nebo vybrat **chybu kopírování**.

Následující informace popisují, co jednotlivé položky oznámení znamenají a obsahují příklady.

#### <a name="essential-notification-items"></a>Základní položky oznámení

- **Název**: popisný název oznámení.

   Příklad: *nastavení proxy aplikace*

- **Popis**: co se stalo v důsledku operace.

   Příklad: *zadaná interní adresa URL je již používána jinou aplikací.*

- **ID oznámení**: jedinečné ID oznámení.

    Příklad: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **ID žádosti klienta**: konkrétní ID žádosti, kterou vytvořil váš prohlížeč.

    Příklad: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Časové razítko UTC**: časové razítko v okamžiku, kdy k oznámení došlo, ve formátu UTC.

    Příklad: *2017-03-23T19:50:43.7583681 z*

- **ID interní transakce**: interní ID, které se používá k vyhledání chyby v našich systémech.

    Příklad: **71a2f329-ca29-402F-aa72-bc00a7aca603**

- **Hlavní název uživatele (UPN)**: uživatel, který operaci spustil.

    Příklad: *tperkins \@ f128.info*

- **ID tenanta**: jedinečné ID tenanta, kterého je uživatel, který spustil operaci, členem.

    Příklad: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **ID objektu uživatele**: jedinečné ID uživatele, který operaci spustil.

    Příklad: *17f84be4-51f8-483a-b533-383791227a99*

#### <a name="detailed-notification-items"></a>Podrobné položky oznámení

- **Zobrazovaný název**: (může být prázdný) podrobnější zobrazovaný název chyby.

    Příklad: *nastavení proxy aplikace*

- **Stav**: konkrétní stav oznámení.

    Příklad: *selhání*

- **ID objektu**: (může být prázdné) ID objektu, proti kterému byla operace spuštěna.

   Příklad: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Podrobnosti**: podrobný popis toho, co se stalo v důsledku operace.

    Příklad: *interní adresa URL ' <https://bing.com/> ' je neplatná, protože je již používána.*

- **Chyba kopírování**: umožňuje vybrat **ikonu kopírování** napravo od textového pole **Kopírovat chybu** a Kopírovat podrobnosti oznámení, které vám pomůžou s podporou.

    Případě   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```


## <a name="next-steps"></a>Další kroky
* [Série rychlý Start při správě aplikací](view-applications-portal.md)
* [Plánování nasazení pro Moje aplikace](access-panel-deployment-plan.md)

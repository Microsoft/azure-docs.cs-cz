---
title: Problémy s konfigurací jednotného přihlašování k heslům pro aplikace mimo galerii
description: Běžné problémy, ke kterým dochází při konfiguraci jednotného přihlašování k heslům (SSO) pro vlastní aplikace, které nejsou v galerii aplikací Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed8bafe7f5bc28cf37205107f8ab6dd5cdb4907c
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "74274137"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problémy s konfigurací jednotného přihlašování k heslům pro aplikaci mimo galerii

Tento článek popisuje běžné problémy, ke kterým může dojít při konfiguraci *jednotného přihlašování* (SSO) hesel pro aplikaci mimo galerii.

## <a name="capture-sign-in-fields-for-an-app"></a>Zaznamenání přihlašovacích polí pro aplikaci

Zachytávání přihlašovacích polí se podporuje jenom pro přihlašovací stránky s povoleným HTML. Není podporováno pro nestandardní přihlašovací stránky, jako jsou ty, které používají Adobe Flash nebo jiné technologie, které nepodporují formát HTML.

Existují dva způsoby, jak zaznamenat přihlašovací pole pro vlastní aplikace:

- **Automatické zachycení přihlašovacích polí** funguje dobře s největší přihlašovací stránkou s povoleným HTML, pokud používají pro pole uživatelského jména a hesla *dobře známá ID div* . KÓD HTML na stránce je vyřazen, aby bylo možné najít ID DIV, která odpovídají určitým kritériím. Tato metadata se uloží, aby je bylo možné později znovu přehrát do aplikace.

- Pokud dodavatel aplikace *neoznačí vstupní pole přihlašování*, použije se **Ruční zachycení pole** . Ruční zachytávání se používá také v případě *, že dodavatel vykresluje více polí, která nelze automaticky zjistit*. Azure Active Directory (Azure AD) může ukládat data pro libovolný počet polí, která jsou na přihlašovací stránce, pokud se jim poříkáte, kde jsou tato pole na stránce.

Obecně platí, že pokud automatické zachycení přihlašovacích polí nefunguje, zkuste ruční možnost.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Automatické zachycení přihlašovacích polí pro aplikaci

Pokud chcete nakonfigurovat jednotné přihlašování založené na heslech pomocí automatického zachycení pole pro přihlášení, postupujte takto:

1. Otevřete [Azure Portal](https://portal.azure.com/). Přihlaste se jako globální správce nebo spolusprávce.

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

    Tato stránka se automaticky vyřadí do vstupních polí uživatelské jméno a heslo. Teď můžete pomocí Azure AD bezpečně přenášet hesla do této aplikace pomocí rozšíření prohlížeče přístupového panelu.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Ruční zachytávání přihlašovacích polí pro aplikaci

Chcete-li ručně zachytit pole přihlášení, je nutné mít nainstalované rozšíření prohlížeče přístupového panelu. Prohlížeč také nemůže běžet ve službě *InPrivate*, *anonymním*nebo *privátním* režimu.

Informace o instalaci rozšíření najdete v části [Instalace rozšíření prohlížeče přístupového panelu](#install-the-access-panel-browser-extension) v tomto článku.

Pokud chcete pro aplikaci nakonfigurovat jednotné přihlašování na základě hesla pomocí ručního zachycení pole pro přihlášení, postupujte podle těchto kroků:

1. Otevřete [Azure Portal](https://portal.azure.com/). Přihlaste se jako globální správce nebo spolusprávce.

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

10. Vyberte **Konfigurovat * &lt;nastavení&gt; * jednotného přihlašování pro heslo AppName**.

11. Vyberte možnost **ručně zjišťovat přihlašovací pole**.

14. Vyberte **OK**.

15. Vyberte **Uložit**.

16. Postupujte podle pokynů k použití přístupového panelu.

## <a name="troubleshoot-problems"></a>Odstraňování potíží

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

- Je možné, že ruční zachycení funguje, ale SSO se nestane, když uživatelé přejdou na aplikaci z přístupového panelu.

Pokud se setkáte s některým z těchto problémů, proveďte následující akce:

- Ujistěte se, že máte *nainstalovanou a povolenou*nejnovější verzi rozšíření prohlížeče přístupového panelu. Viz část [Instalace rozšíření prohlížeče přístupového panelu](#install-the-access-panel-browser-extension) v tomto článku.

- Ujistěte se, že během procesu zachycení není váš prohlížeč v *anonymním*, *InPrivate*nebo *privátním* režimu. V těchto režimech není rozšíření přístupového panelu podporováno.

- Ujistěte se, že se uživatelé nesnaží přihlašovat k aplikaci z přístupového panelu v *anonymním*, *InPrivate*nebo *privátním režimu*.

- Zkuste znovu zpracovat proces ručního zachycení. Ujistěte se, že červené značky jsou nad správnými poli.

- Pokud se zdá, že proces ručního zachytávání přestane reagovat nebo stránka pro přihlášení nereaguje, zkuste proces ručního zachytávání zopakovat. Tentokrát ale po dokončení procesu stiskněte klávesu F12 a otevřete konzolu pro vývojáře v prohlížeči. Vyberte kartu **Konzola** . zadejte **window. Location = "*&lt;přihlašovací adresa URL, kterou jste zadali při konfiguraci aplikace&gt;***, a potom stiskněte klávesu ENTER. Tím se vynutí přesměrování stránky, které ukončí proces zachytávání a uloží pole, která byla zachycena.

### <a name="contact-support"></a>Kontaktování podpory

Pokud stále máte problémy, otevřete případ s podpora Microsoftu. Popište, co jste zkusili. Uveďte podrobnosti, které jsou popsány v [podrobnostech oznámení o zobrazení portálu](#view-portal-notification-details) a [odeslání podrobností oznámení pracovníkovi podpory, aby získali nápovědu k](#send-notification-details-to-a-support-engineer-to-get-help) oddílům tohoto článku (pokud jsou k dispozici).

## <a name="install-the-access-panel-browser-extension"></a>Nainstalovat rozšíření prohlížeče přístupového panelu

Postupujte následovně:

1. Otevřete [panel přístupu](https://myapps.microsoft.com) v podporovaném prohlížeči. Přihlaste se k Azure AD jako *uživatel*.

2. V přístupovém panelu vyberte **aplikace heslem pro jednotné přihlašování** .

3. Až se zobrazí výzva k instalaci softwaru, vyberte **nainstalovat hned**.

4. Budete přesměrováni na stránku ke stažení pro váš prohlížeč. Vyberte možnost **Přidat** rozšíření.

5. Pokud budete vyzváni, vyberte **Povolit** nebo **Povolit**.

6. Po instalaci restartujte prohlížeč.

7. Přihlaste se k přístupovému panelu. Podívejte se, jestli můžete otevřít aplikace s podporou jednotného přihlašování k heslům.

Rozšíření prohlížeče pro Chrome a Firefox můžete také přímo stáhnout pomocí těchto odkazů:

-   [Rozšíření přístupového panelu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozšíření přístupového panelu Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>Zobrazit podrobnosti oznámení na portálu

Chcete-li zobrazit podrobnosti o jakémkoli oznámení portálu, postupujte takto:

1. Vyberte ikonu **oznámení** (zvonek) v pravém horním rohu Azure Portal.

2. Vyberte jakékoli oznámení, které zobrazuje *chybový* stav. (Mají červenou "!".)

   > [!NOTE]
   > Nemůžete vybrat oznámení, která jsou v *úspěšném* nebo *probíhajícím* stavu.

3. Otevře se podokno **Podrobnosti oznámení** . Přečtěte si informace o tomto problému.

5. Pokud stále potřebujete pomoc, sdílejte tyto informace s pracovníkem podpory nebo produktovou skupinou. Kliknutím na ikonu **Kopírovat** napravo od pole **Chyba kopírování** zkopírujte podrobnosti oznámení ke sdílení.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Odeslat podrobnosti oznámení pracovníkovi podpory a získat pomoc

Je důležité, abyste nasdíleli *všechny* podrobnosti uvedené v této části s podporou, aby vám mohli rychle pomoci. Pokud ho chcete zaznamenat, můžete si udělat snímek obrazovky nebo vybrat **chybu kopírování**.

Následující informace popisují, co jednotlivé položky oznámení znamenají a obsahují příklady.

### <a name="essential-notification-items"></a>Základní položky oznámení

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

    Příklad: *tperkins\@f128.info*

- **ID tenanta**: jedinečné ID tenanta, kterého je uživatel, který spustil operaci, členem.

    Příklad: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **ID objektu uživatele**: jedinečné ID uživatele, který operaci spustil.

    Příklad: *17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>Podrobné položky oznámení

- **Zobrazovaný název**: (může být prázdný) podrobnější zobrazovaný název chyby.

    Příklad: *nastavení proxy aplikace*

- **Stav**: konkrétní stav oznámení.

    Příklad: *selhání*

- **ID objektu**: (může být prázdné) ID objektu, proti kterému byla operace spuštěna.

   Příklad: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Podrobnosti**: podrobný popis toho, co se stalo v důsledku operace.

    Příklad: *interní adresa URL<https://bing.com/>' ' je neplatná, protože je již používána.*

- **Chyba kopírování**: umožňuje vybrat **ikonu kopírování** napravo od textového pole **Kopírovat chybu** a Kopírovat podrobnosti oznámení, které vám pomůžou s podporou.

    Případě```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Další kroky
[Zajištění jednotného přihlašování k aplikacím pomocí proxy aplikací](application-proxy-configure-single-sign-on-with-kcd.md)

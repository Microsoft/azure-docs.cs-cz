---
title: Problémy s konfigurací hesla SSO pro aplikace bez galerie
description: Běžné problémy, ke kterým dochází při konfiguraci jednotného přihlašování hesla (SSO) pro vlastní aplikace, které nejsou v galerii aplikací Azure AD.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274137"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problémy s konfigurací jednotného přihlašování hesla pro aplikaci mimo galerii

Tento článek popisuje běžné problémy, které mohou nastat při konfiguraci *hesla jednotného přihlašování* (SSO) pro aplikaci bez galerie.

## <a name="capture-sign-in-fields-for-an-app"></a>Zachycení přihlašovacích polí pro aplikaci

Zachycení přihlašovacího pole je podporováno pouze pro přihlašovací stránky s podporou HTML. Není podporována pro nestandardní přihlašovací stránky, jako jsou stránky, které používají Adobe Flash nebo jiné technologie, které nejsou povoleny html.

Přihlašovací pole pro vlastní aplikace lze zachytit dvěma způsoby:

- **Automatické zachycení přihlašovacích polí** funguje dobře s většinou přihlašovacích stránek s podporou HTML, pokud pro pole uživatelského jména a hesla *používají známá id DIV.* Kód HTML na stránce je seškrábnut, aby našel ID DIV, která odpovídají určitým kritériím. Tato metadata se uloží, aby je bylo možné později přehrát do aplikace.

- **Ruční zachycení přihlašovacího pole** se používá, pokud dodavatel aplikace *neoznačuje přihlašovací vstupní pole*. Ruční sběr se používá také v případě, že dodavatel *vykreslí více polí, která nelze automaticky zjistit*. Azure Active Directory (Azure AD) můžete ukládat data pro tolik polí, jako jsou na přihlašovací stránce, pokud zjistíte, kde jsou tato pole na stránce.

Obecně platí, že pokud automatické zachycení přihlašovacího pole nefunguje, vyzkoušejte ruční možnost.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Automatické zachycení přihlašovacích polí pro aplikaci

Chcete-li nakonfigurovat přihlašování založené na heslech pomocí automatického zachycení přihlašovacích polí, postupujte takto:

1. Otevřete [portál Azure](https://portal.azure.com/). Přihlaste se jako globální správce nebo spolusprávce.

2. V navigačním podokně na levé straně vyberte **Všechny služby,** chcete-li otevřít rozšíření Azure AD.

3. Do vyhledávacího pole filtru zadejte **službu Azure Active Directory** a pak vyberte **Službu Azure Active Directory**.

4. V navigačním podokně Azure AD vyberte **Podnikové aplikace.**

5. Výběrem **možnosti Všechny aplikace** zobrazíte seznam svých aplikací.

   > [!NOTE]
   > Pokud požadovanou aplikaci nevidíte, použijte ovládací prvek **Filtr** v horní části seznamu **Všechny aplikace.** Nastavte možnost **Zobrazit** na "Všechny aplikace".

6. Vyberte aplikaci, kterou chcete nakonfigurovat pro službu SSO.

7. Po načtení aplikace vyberte **jednotné přihlašování** v navigačním podokně na levé straně.

8. Vyberte režim **přihlašování na základě hesla.**

9. Zadejte **přihlašovací adresu URL**, což je adresa URL stránky, na které uživatelé zadají své uživatelské jméno a heslo pro přihlášení. *Ujistěte se, že přihlašovací pole jsou zobrazeny na stránce pro adresu URL, kterou zadáte*.

10. Vyberte **Uložit**.

    Stránka je automaticky vyškrábána pro vstupní pole uživatelského jména a hesla. Azure AD teď můžete použít k bezpečnému přenosu hesel do této aplikace pomocí rozšíření prohlížeče přístupového panelu.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Ruční zachycení přihlašovacích polí pro aplikaci

Chcete-li ručně zachytit přihlašovací pole, musíte mít nainstalované rozšíření prohlížeče přístupového panelu. Prohlížeč také nemůže být spuštěn *v inPrivate*, *inkognito*nebo *v soukromém* režimu.

Pokud chcete rozšíření nainstalovat, přečtěte si část [Instalace rozšíření prohlížeče přístupového panelu](#install-the-access-panel-browser-extension) v tomto článku.

Chcete-li nakonfigurovat přihlašování založené na heslech pro aplikaci pomocí ručního zachycení přihlašovacího pole, postupujte takto:

1. Otevřete [portál Azure](https://portal.azure.com/). Přihlaste se jako globální správce nebo spolusprávce.

2. V navigačním podokně na levé straně vyberte **Všechny služby,** chcete-li otevřít rozšíření Azure AD.

3. Do vyhledávacího pole filtru zadejte **službu Azure Active Directory** a pak vyberte **Službu Azure Active Directory**.

4. V navigačním podokně Azure AD vyberte **Podnikové aplikace.**

5. Výběrem **možnosti Všechny aplikace** zobrazíte seznam svých aplikací.

   > [!NOTE] 
   > Pokud požadovanou aplikaci nevidíte, použijte ovládací prvek **Filtr** v horní části seznamu **Všechny aplikace.** Nastavte možnost **Zobrazit** na "Všechny aplikace".

6. Vyberte aplikaci, kterou chcete nakonfigurovat pro službu SSO.

7. Po načtení aplikace vyberte **jednotné přihlašování** v navigačním podokně na levé straně.

8. Vyberte režim **přihlašování na základě hesla.**

9. Zadejte **přihlašovací adresu URL**, což je stránka, na které uživatelé zadají své uživatelské jméno a heslo pro přihlášení. *Ujistěte se, že přihlašovací pole jsou zobrazeny na stránce pro adresu URL, kterou zadáte*.

10. Vyberte **Konfigurovat * &lt;&gt; * nastavení jednotného přihlášení s názvem appname Password**.

11. Vyberte **Ručně rozpoznat přihlašovací pole**.

14. Vyberte **OK**.

15. Vyberte **Uložit**.

16. Podle pokynů použijte přístupový panel.

## <a name="troubleshoot-problems"></a>Odstraňování potíží

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Na této adrese URL se zobrazuje chyba "Na této adrese URL jsme nenašli žádná pole pro přihlášení"

Tato chybová zpráva se zobrazí při selhání automatického zjišťování polí přihlášení. Chcete-li problém vyřešit, zkuste ruční detekci přihlašovacích polí. V tomto článku najdete v [části ručně zachycování přihlašovacích polí.](#manually-capture-sign-in-fields-for-an-app)

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Zobrazuje se chyba "Nelze uložit konfiguraci jednotného přihlašování"

Zřídka aktualizace konfigurace s připojatá zařízení nezdaří. Chcete-li tento problém vyřešit, zkuste konfiguraci uložit znovu.

Pokud se chyba zobrazuje stále, otevřete případ podpory. Zahrňte informace popsané v [podrobnostech oznámení zobrazit portál](#view-portal-notification-details) a [odeslat podrobnosti oznámení pracovníkovi podpory, abyste získali](#send-notification-details-to-a-support-engineer-to-get-help) části nápovědy tohoto článku.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Nelze ručně rozpoznat přihlašovací pole pro svou aplikaci

Pokud ruční zjišťování nefunguje, můžete sledovat následující chování:

- Zdálo se, že proces ručního sběru funguje, ale zachycená pole nejsou správná.

- Při spuštění procesu sběru se nezvýrazní správná pole.

- Proces sběru vás přenese na přihlašovací stránku aplikace podle očekávání, ale nic se nestane.

- Ruční zachycení se zdá fungovat, ale při chycení do pojetí uživatelského přihlašování se nestane, když uživatelé přejít na aplikaci z přístupového panelu.

Pokud narazíte na některý z těchto problémů, proveďte následující kroky:

- Zkontrolujte, zda je *nainstalována a povolena*nejnovější verze rozšíření prohlížeče přístupového panelu . V tomto článku najdete v části [Instalace rozšíření prohlížeče přístupového panelu.](#install-the-access-panel-browser-extension)

- Ujistěte se, že váš prohlížeč není *v anonymním*, *inPrivate*nebo *Soukromém* režimu během procesu sběru. Rozšíření přístupového panelu není v těchto režimech podporováno.

- Ujistěte se, že se uživatelé nepokouší přihlásit k aplikaci z přístupového panelu *v anonymním*, *inPrivate*nebo *soukromém režimu*.

- Opakujte proces ručního sběru. Ujistěte se, že červené značky jsou nad správnými poli.

- Pokud se zdá, že proces ručního sběru přestane reagovat nebo přihlašovací stránka nereaguje, zkuste proces ručního sběru znovu. Ale tentokrát, po dokončení procesu, stiskněte klávesu F12 otevřít vývojářskou konzoli prohlížeče. Vyberte kartu **konzoly.** Zadejte **window.location="*&lt;přihlašovací adresu URL,&gt;kterou jste zadali při konfiguraci aplikace*,** a stiskněte Enter. To vynutí přesměrování stránky, které ukončí proces sběru a uloží pole, která byla zachycena.

### <a name="contact-support"></a>Kontaktování podpory

Pokud potíže přetrvávají, otevřete případ s podporou společnosti Microsoft. Popište, co jste zkoušeli. Uveďte podrobnosti, které jsou popsány v [podrobnostech oznámení zobrazit portál a](#view-portal-notification-details) [odeslat podrobnosti oznámení pracovníka podpory získat nápovědu](#send-notification-details-to-a-support-engineer-to-get-help) části tohoto článku (pokud je k dispozici).

## <a name="install-the-access-panel-browser-extension"></a>Instalace rozšíření prohlížeče Přístupového panelu

Postupujte následovně:

1. Otevřete [přístupový panel](https://myapps.microsoft.com) v podporovaném prohlížeči. Přihlaste se k Azure AD jako *uživatel*.

2. Vyberte **aplikaci heslo-SSO** na přístupovém panelu.

3. Až se zobrazí výzva k instalaci softwaru, vyberte **Instalovat .**

4. Budete přesměrováni na stránku pro stažení prohlížeče. Zvolte **přidání** rozšíření.

5. Pokud se zobrazí výzva, vyberte **Povolit** nebo **Povolit**.

6. Po instalaci restartujte prohlížeč.

7. Přihlaste se k přístupovému panelu. Podívejte se, jestli můžete otevřít aplikace s podporou hesla SSO.

Můžete také přímo stáhnout rozšíření prohlížeče pro Chrome a Firefox prostřednictvím těchto odkazů:

-   [Rozšíření přístupového panelu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozšíření přístupového panelu Firefoxu](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>Zobrazit podrobnosti oznámení portálu

Chcete-li zobrazit podrobnosti o každém oznámení portálu, postupujte takto:

1. Vpravém horním rohu portálu Azure vyberte ikonu **Oznámení** (zvonek).

2. Vyberte všechna oznámení, která zobrazuje *chybový* stav. (Mají červené "!".)

   > [!NOTE]
   > Nelze vybrat oznámení, která jsou ve stavu *Úspěšné* nebo *Probíhá.*

3. Otevře se podokno **Podrobnosti oznámení.** Přečtěte si informace, které se o problému dozvíte.

5. Pokud stále potřebujete pomoc, sdílejte informace s pracovníkem podpory nebo skupinou produktů. Vyberte ikonu **kopírování** napravo od chybového pole **Kopírovat,** chcete-li zkopírovat podrobnosti oznámení, které chcete sdílet.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Odeslání podrobností oznámení pracovníkovi podpory za účelem získání nápovědy

Je důležité, abyste sdíleli *všechny* podrobnosti, které jsou uvedeny v této části, s podporou, aby vám mohly rychle pomoci. Chcete-li jej zaznamenat, můžete pořídit snímek obrazovky nebo vybrat **chybu Kopírovat**.

Následující informace vysvětlují, co každá položka oznámení znamená, a poskytují příklady.

### <a name="essential-notification-items"></a>Základní položky oznámení

- **Název**: popisný název oznámení.

   Příklad: *Nastavení proxy serveru aplikace*

- **Popis**: co se stalo v důsledku operace.

   Příklad: *Zadaná interní adresa URL je již používána jinou aplikací.*

- **ID oznámení**: jedinečné ID oznámení.

    Příklad: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **ID žádosti o klienta**: konkrétní ID žádosti, které váš prohlížeč provedl.

    Příklad: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Časové razítko UTC**: časové razítko, kdy došlo k oznámení, v UTC.

    Příklad: *2017-03-23T19:50:43.7583681Z*

- **Interní ID transakce**: interní ID, které se používá k vyhledat chybu v našich systémech.

    Příklad: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **HLAVNÍ UŽIVATEL,** který operaci spustil.

    Příklad: *tperkins\@f128.info*

- **ID klienta**: jedinečné ID klienta, jehož členem je uživatel, který operaci spustil.

    Příklad: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **ID objektu uživatele**: Jedinečné ID uživatele, který operaci spustil.

    Příklad: *17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>Podrobné položky oznámení

- **Zobrazovaný název**: (může být prázdný) podrobnější zobrazovaný název chyby.

    Příklad: *Nastavení proxy serveru aplikace*

- **Stav**: specifický stav oznámení.

    Příklad: *Nezdařilo se*

- **ID objektu**: (může být prázdný) ID objektu, proti kterému byla operace spuštěna.

   Příklad: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Podrobnosti**: podrobný popis toho, co se stalo v důsledku operace.

    Příklad: *Interní<https://bing.com/>adresa URL ' je neplatná, protože je již používána.*

- **Chyba kopírování**: Umožňuje vybrat **ikonu kopírování** vpravo od textového pole **chyby kopírovat** a zkopírovat podrobnosti oznámení, které vám pomůže s podporou.

    Příklad:```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Další kroky
[Poskytněte svým aplikacím jednotné přihlašování pomocí proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)

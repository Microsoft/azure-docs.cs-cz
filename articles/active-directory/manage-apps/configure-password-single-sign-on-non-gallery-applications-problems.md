---
title: Potíže s konfigurací heslem jednotného přihlašování pro aplikaci mimo Galerii | Dokumentace Microsoftu
description: Vysvětlení běžných tváří lidí problémy při konfiguraci hesla Single Sign-on pro vlastní aplikace mimo galerii, které nejsou uvedené v galerii aplikací Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 223532a169dc1655d8c20a7bd60ecb68d9b5052b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834294"
---
# <a name="problem-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Potíže s konfigurací heslem jednotného přihlašování pro aplikaci mimo Galerii

Tento článek vám pomůže lépe porozumět běžné tváří lidí problémy při konfigurování **heslem jednotného přihlašování** aplikaci mimo galerii.

## <a name="how-to-capture-sign-in-fields-for-an-application"></a>Jak zachytit pole pro aplikaci

Zachycení pole je podporován pouze pro povolené HTML přihlašovací stránky a je **není podporována pro nestandardní přihlašovací stránky**, jako jsou ty, které používají Flash, nebo jiné technologie HTML nepodporujícím.

Existují dva způsoby, jak lze zachytit pole pro přihlášení pro vaše vlastní aplikace:

-   Zachycení pole Automatické přihlášení

-   Zachycení ruční pole

**Automatické přihlášení pole zachycení** dobře funguje pro většinu podporujících formát HTML přihlašovací stránky, pokud používají **dobře známé identifikátory DIV uživatelské jméno a heslo zadejte** pole. Způsob, jakým tento postup funguje, je tak, že automatizované získávání dat HTML na stránce Najít DIV ID, které splňují určitá kritéria a potom uložení těchto metadat pro tuto aplikaci přehrát hesla k němu později.

**Ruční pole zachycení** je možné v případě, která aplikace **neoznačuje popiskem dodavatele** vstupních polí použitých pro přihlášení. Zachycení ruční pole lze také v případě, když **dodavatele vykreslí více polí** , který nemůže být automaticky zjištěno. Azure AD můžete ukládat data pro libovolný počet polí tak, že jsou na stránce přihlášení, tak dlouho, dokud Řekněte nám kde jsou tato pole na stránce.

Obecně platí **Pokud zachycení pole automatické přihlašování nefunguje, vyzkoušet ruční variantu.**

### <a name="how-to-automatically-capture-sign-in-fields-for-an-application"></a>Jak automaticky zachytit pole pro aplikaci

Ke konfiguraci **založené na heslech Single Sign-on** pro aplikace s využitím **automatické přihlášení pole zachycení**, postupujte podle následujících kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

  * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8.  Vyberte režim **přihlašování na základě heslo.**

9.  Zadejte **přihlašovací adresa URL**, adresu URL, kde uživatelé zadat svoje uživatelské jméno a heslo pro přihlášení. **Zkontrolujte pole přihlášení zobrazují na adrese URL můžete poskytnout**.

10. Klikněte na tlačítko **Uložit**.

11. Jakmile provedete, že adresa URL je automaticky získaný k zadání uživatelského jména a hesla vstupní pole a umožní vám umožňuje bezpečně přenášet hesla k dané aplikaci pomocí rozšíření prohlížeče přístupového panelu Azure AD.

## <a name="how-to-manually-capture-sign-in-fields-for-an-application"></a>Jak ručně zachytit pole pro aplikaci

Chcete-li ručně zachytit pole pro přihlášení, musíte nejprve mít nainstalované rozšíření prohlížeče Panel přístupu a **spuštěn v režimu inPrivate, anonymní nebo privátní.** K instalaci rozšíření prohlížeče, postupujte podle kroků v [instalace rozšíření prohlížeče Panel přístupu](#i-cannot-manually-detect-sign-in-fields-for-my-application) oddílu.

Ke konfiguraci **založené na heslech Single Sign-on** pro aplikace s využitím **ruční pole zachycení**, postupujte podle následujících kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8.  Vyberte režim **přihlašování na základě heslo.**

9.  Zadejte **přihlašovací adresa URL**, adresu URL, kde uživatelé zadat svoje uživatelské jméno a heslo pro přihlášení. **Zkontrolujte pole přihlášení zobrazují na adrese URL můžete poskytnout**.

10. Klikněte na tlačítko **Uložit**.

11. Jakmile provedete, že adresa URL je automaticky získaný k zadání uživatelského jména a hesla vstupní pole a umožní vám umožňuje bezpečně přenášet hesla k dané aplikaci pomocí rozšíření prohlížeče přístupového panelu Azure AD. V případě selhání, můžete **změnit režim přihlašování použít ruční pole zachycení** budete pokračovat v kroku 12.

12. Klikněte na tlačítko **konfigurovat &lt;appname&gt; nastavení hesla jednotného přihlašování**.

13. Vyberte **ručně zjišťovat pole pro přihlášení** možnosti konfigurace.

14. Klikněte na tlačítko **OK**.

15. Klikněte na **Uložit**.

16. Postupujte podle pokynů na obrazovce a použít na přístupovém panelu.

## <a name="i-see-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Zobrazí chybu "Jsme nenašli žádné pole na této adrese URL"

Pokud se nezdaří automatické zjišťování pole se zobrazí tato chyba. K vyřešení problému, zkuste zjišťování ruční pole přihlášení podle kroků v [jak ručně zachytit pole pro aplikaci](#how-to-manually-capture-sign-in-fields-for-an-application) oddílu.

## <a name="i-see-an-unable-to-save-single-sign-on-configuration-error"></a>"Nepovedlo se uložit konfiguraci jednotného přihlašování" se zobrazuje chyba

V některých výjimečných případech aktualizuje se konfigurace přihlášení může selhat. Pokud chcete vyřešit, uložte jednotné přihlašování konfiguraci znovu.

Pokud bude nadále selhávat konzistentně, otevřete případ podpory a poskytnout informace shromážděné ve [jak zobrazit podrobnosti o oznámení na portálu](#i-cannot-manually-detect-sign-in-fields-for-my-application) a [jak získat nápovědu odesláním podrobnosti oznámení podpoře inženýr](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) oddíly.

## <a name="i-cannot-manually-detect-sign-in-fields-for-my-application"></a>Můžu nerozpozná ručně pole přihlášení pro aplikaci

Chování, které se můžete setkat při ruční detekce nefunguje patří:

-   Proces ručního zachycení pravděpodobně fungovat, ale pole zachycené nejsou správné

-   Pravé pole není získat zvýrazněnou při provádění procesu zachycení

-   Proces sběru budu přesměrován na přihlašovací stránku vaší aplikace podle očekávání, ale nic se nestane

-   Ruční zachycení zdánlivě fungovat, ale jednotného přihlašování nemá dojít v případě, že moje uživatelé přejdou na aplikace na přístupovém panelu.

Pokud narazíte na některý z těchto problémů zkontrolujte následující:

-   Ujistěte se, že máte nejnovější verzi rozšíření prohlížeče přístupového panelu **nainstalované** a **povolené** podle postupu v [instalace rozšíření prohlížeče Panel přístupu](#how-to-install-the-access-panel-browser-extension) oddílu.

-   Ujistěte se, že se o proces zachycení při prohlížeči **privátní, se službou inPrivate nebo anonymní režim**. V těchto režimech nepodporuje rozšíření přístupového panelu.

-   Ujistěte se, že vaši uživatelé nejsou pokoušel se přihlásit do aplikace na přístupovém panelu při v **privátní, se službou inPrivate nebo anonymní režim**. V těchto režimech nepodporuje rozšíření přístupového panelu.

-   Zkuste ruční proces sběru znovu zajistit, že červené značky jsou přes pole správný.

-   Pokud proces ručního zachycení zdá se, že přestane reagovat, nebo přihlašovací stránku neumí proces ručního zachycení nic (případ 3 výše), zkuste znovu. Ale tentokrát po dokončení procesu, stiskněte **F12** tlačítko otevřete konzolu pro vývojáře v prohlížeči. Jednou, otevřete **konzoly** a typ **window.location= "&lt;zadejte přihlašovací adresu url jste zadali při konfiguraci aplikace&gt;"** a potom stiskněte klávesu **Enter** . To vynutí přesměrování stránky, která ukončí proces zachycení a uloží pole, která byla zachycena.

Pokud žádná z těchto přístupů pro vás nejvhodnější, může přispět podpora. Otevření případu podpory s podrobnostmi o co jste se pokusili, a také informace shromážděné ve [jak zobrazit podrobnosti o oznámení na portálu](#i-cannot-manually-detect-sign-in-fields-for-my-application) a [jak získat nápovědu odesláním oznámení podrobnosti pro pracovníka podpory ](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) oddíly (pokud existuje).

## <a name="how-to-install-the-access-panel-browser-extension"></a>Postup instalace rozšíření prohlížeče Panel přístupu

K instalaci rozšíření prohlížeče panelu přístup, postupujte podle následujících kroků:

1.  Otevřít [přístupového panelu](https://myapps.microsoft.com) v jednom z podporovaných prohlížečů a přihlaste se jako **uživatele** ve službě Azure AD.

2.  Klikněte na tlačítko **heslem jednotného přihlašování aplikace** na přístupovém panelu.

3.  V příkazovém řádku s výzvou k instalaci softwaru vyberte **nainstalovat**.

4.  Závislosti na vašem prohlížeči budete přesměrováni na odkaz ke stažení. **Přidat** rozšíření do prohlížeče.

5.  Pokud vás prohlížeč vyzve, vyberte buď **povolit** nebo **povolit** rozšíření.

6.  Po instalaci **restartovat** relace prohlížeče.

7.  Přihlaste se na přístupovém panelu a zobrazit, pokud můžete **spuštění** vaše heslo jednotného přihlašování aplikace.

Rozšíření pro Chrome a Firefox lze také stáhnout z přímé odkazy níže:

-   [Rozšíření Chrome přístupového panelu](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozšíření Firefox přístupového panelu](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Jak zobrazit podrobnosti o oznámení na portálu

Můžete zobrazit podrobnosti o oznámení portálu podle následujících kroků:

1.  Klikněte na tlačítko **oznámení** ikonu (zvonek) v pravém horním rohu webu Azure portal

2.  Vyberte všechna oznámení v **chyba** stavu (ty s červenou (!) vedle sebe).

  >! Poznámka:] nelze klikněte na oznámení **úspěšné** nebo **v průběhu** stavu.
  >
  >

3.  **Podrobnosti oznámení** se otevře podokno.

4.  Pomocí informací sami, abyste porozuměli další podrobnosti o problému.

5.  Pokud stále potřebujete pomoc, můžete také sdílet informace s pracovníkem technické podpory nebo produktovou skupinou účelem vyřešení vašeho problému.

6.  Klikněte na tlačítko **kopírování** **ikonu** napravo od **Kopírovat chybu** textového pole zkopírujte všechny podrobnosti oznámení sdílet s pracovníkem skupiny podpory nebo produktu.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Jak získat nápovědu odesláním oznámení podrobnosti pro pracovníka podpory

Je velmi důležité, jakým sdílíte **níže uvedených údajů** s pracovníkem technické podpory Pokud potřebujete pomoc, aby se vám může pomoct rychle. Můžete **pořídit snímek obrazovky,** nebo klikněte na tlačítko **ikona chyby kopírování**napravo od byl nalezen **Kopírovat chybu** textového pole.

## <a name="notification-details-explained"></a>Vysvětlení podrobnosti o oznámení

Níže popisuje více co jednotlivé oznámení položky znamená, že a příkladů, každý z nich.

### <a name="essential-notification-items"></a>Základní oznámení položky

-   **Název** – popisný název oznámení

    -   Příklad – **nastavení proxy aplikace.**

-   **Popis** – popis, k čemu došlo v důsledku operace

    -   Příklad – **zadanou vnitřní adresu url se už používá jiná aplikace**

-   **ID oznámení** – jedinečné id oznámení

    -   Příklad – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **ID žádosti klienta** – id konkrétní žádosti od prohlížeče

    -   Příklad – **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **Časové razítko UTC** – časové razítko, během které oznámení došlo k chybě, ve standardu UTC

    -   Příklad – **2017-03-23T19:50:43.7583681Z**

-   **Interní ID transakce** – interní ID použít k vyhledání Chyba v našem systému

    -   Příklad – **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **Hlavní název uživatele** – uživatel, který provedl operaci

    -   Příklad: **tperkins@f128.info**

-   **ID tenanta** – jedinečné ID tenanta, který byl členem skupiny uživatele, který provedl operaci

    -   Příklad – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **ID objektu uživatele** – jedinečné ID uživatele, který provedl operaci

    -   Příklad – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Podrobný oznamovací položky

-   **Zobrazovaný název** – **(může být prázdné)** podrobnější zobrazovaný název pro chybu

    -   Příklad * – **nastavení proxy aplikace.**

-   **Stav** – konkrétní stavové oznámení

    -   Příklad * – **se nezdařilo**

-   **ID objektu** – **(může být prázdné)** ID objektu, proti kterému byla provedena operace

    -   Příklad – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Podrobnosti o** – podrobný popis, k čemu došlo v důsledku operace

    -   Příklad – **interní adresa url 'https://bing.com/"je neplatná, protože se už používá**

-   **Chyba při kopírování** – klikněte na tlačítko **ikonu kopírování** napravo od **Kopírovat chybu** textového pole zkopírujte všechny podrobnosti oznámení sdílet s pracovníkem skupiny podpory nebo produktu

    -   Příklad: ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Další postup
[Poskytovat jednotné přihlašování do aplikací pomocí Proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)


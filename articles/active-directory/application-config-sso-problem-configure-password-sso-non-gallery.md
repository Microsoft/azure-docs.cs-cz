---
title: Problém konfigurace hesla jednotné přihlašování pro aplikace bez Galerie | Microsoft Docs
description: Pochopení tučné osoby běžné problémy při konfiguraci hesla jednotné přihlašování pro vlastní aplikace bez galerie, které nejsou uvedené v galerii aplikací Azure AD
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 7b671089bfac04b359717874928d6a342c44b3d4
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="problem-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problém konfigurace hesla jednotné přihlašování pro aplikace bez Galerie

Tento článek vám pomůže lépe porozumět tučné osoby běžné problémy při konfiguraci **heslo jednotné přihlašování** s aplikací bez galerie.

## <a name="how-to-capture-sign-in-fields-for-an-application"></a>Jak zachytit pole přihlášení pro aplikaci

Zachycení pole přihlášení je podporována pouze pro podporujících formát HTML přihlašovací stránky a je **není podporováno pro nestandardní přihlašovací stránky**, jako jsou ty, které používáte Flash nebo jiné technologie nepodporujícím HTML.

Existují dva způsoby, můžete zaznamenávat pole přihlašování pro vaše vlastní aplikace:

-   Zachycení pole Automatické přihlášení

-   Zachycení pole Ruční přihlášení

**Automatické přihlášení pole zachycení** funguje dobře u většiny podporujících formát HTML přihlašovací stránky, pokud používají **dobře známé DIV ID pro uživatelské jméno a heslo zadejte** pole. Tento postup funguje způsob je oškrabávání HTML na stránce Najít DIV ID, které splňují určitá kritéria a potom uložení aby metadata pro tuto aplikaci opakování hesla k němu později.

**Ruční pole přihlášení zachycení** lze použít v případě, který aplikace **dodavatele neoznačí** vstupních polí použitých pro přihlášení. Zachycení ruční přihlášení pole lze také v případě, že při **dodavatele vykreslí více polí** , nemůže být automaticky nalezeny. Azure AD můžete ukládat data pro libovolný počet polí jsou na stránce přihlášení, tak dlouho, dokud Řekněte nám kde tato pole jsou na stránce.

Obecně platí **Pokud pole Automatické přihlášení zachycení nefunguje, zkuste možnost ručně.**

### <a name="how-to-automatically-capture-sign-in-fields-for-an-application"></a>Jak automaticky zachytit pole přihlášení pro aplikaci

Ke konfiguraci **založené na heslech jednotné přihlašování** pro aplikace pomocí **pole Automatické přihlášení zachycení**, postupujte podle následujících kroků:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **ko-správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

  * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete konfigurovat jednotné přihlašování.

7.  Po načtení aplikace, klikněte na **jednotného přihlašování** z levé navigační nabídky.

8.  Vyberte režim **založené na heslech přihlášení.**

9.  Zadejte **přihlašovací adresa URL**, adresu URL, kde uživatelé zadat svoje uživatelské jméno a heslo pro přihlášení. **Zkontrolujte pole přihlášení jsou viditelné na adrese URL je zadat**.

10. Klikněte na tlačítko **Uložit**.

11. Jednou, můžete udělat, který adresa URL je automaticky oškrábána pro uživatelské jméno a heslo vstupní pole a vám umožní používat Azure AD bezpečně přenést hesla k dané aplikaci pomocí rozšíření přístup k panelu prohlížeče.

## <a name="how-to-manually-capture-sign-in-fields-for-an-application"></a>Jak ručně zachytit pole přihlášení pro aplikaci

Pokud chcete zaznamenat ručně pole přihlášení, nejprve musí mít rozšíření přístup k panelu prohlížeče, nainstalovat a **nebyla spuštěna v režimu se službou inPrivate, incognito nebo soukromé.** K instalaci rozšíření prohlížeče, postupujte podle kroků v [postup instalace rozšíření přístup k panelu prohlížeče](#i-cannot-manually-detect-sign-in-fields-for-my-application) části.

Ke konfiguraci **založené na heslech jednotné přihlašování** pro aplikace pomocí **ruční pole přihlášení zachycení**, postupujte podle následujících kroků:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **ko-správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

   * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete konfigurovat jednotné přihlašování.

7.  Po načtení aplikace, klikněte na **jednotného přihlašování** z levé navigační nabídky.

8.  Vyberte režim **založené na heslech přihlášení.**

9.  Zadejte **přihlašovací adresa URL**, adresu URL, kde uživatelé zadat svoje uživatelské jméno a heslo pro přihlášení. **Zkontrolujte pole přihlášení jsou viditelné na adrese URL je zadat**.

10. Klikněte na tlačítko **Uložit**.

11. Jednou, můžete udělat, který adresa URL je automaticky oškrábána pro uživatelské jméno a heslo vstupní pole a vám umožní používat Azure AD bezpečně přenést hesla k dané aplikaci pomocí rozšíření přístup k panelu prohlížeče. V případě selhání, můžete **změnit režim přihlášení k ruční pole přihlášení zaznamenáte** podle pokračovat v kroku 12.

12. Klikněte na tlačítko **konfigurace &lt;appname&gt; nastavení hesla jednotného přihlašování**.

13. Vyberte **ručně zjistit přihlášení pole** možnosti konfigurace.

14. Klikněte na tlačítko **OK**.

15. Klikněte na **Uložit**.

16. Postupujte podle pokynů na obrazovce a použít na přístupovém panelu.

## <a name="i-see-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Zobrazuje chybu "Jsme nenašli žádná pole přihlášení na této adrese URL."

Tato chyba zobrazí při automatické zjišťování pole přihlášení selže. Chcete-li vyřešit tento problém, zkuste detekce ruční pole přihlášení podle kroků v [ručně zaznamenání pole přihlášení pro aplikaci](#how-to-manually-capture-sign-in-fields-for-an-application) části.

## <a name="i-see-an-unable-to-save-single-sign-on-configuration-error"></a>Najdete v části "Nelze pro uložení konfigurace jednotného přihlašování" Chyba

V některých výjimečných případech aktualizuje se konfigurace přihlášení může selhat. K vyřešení, vyzkoušejte ukládání jedné přihlašování konfiguraci znovu.

Pokud nadále selhání opakovaná, otevřete případu podpory a poskytnout informace shromážděné ve [postup najdete v části Podrobnosti o portálu oznámení](#i-cannot-manually-detect-sign-in-fields-for-my-application) a [jak získat nápovědu zasláním oznámení podrobnosti o podporu pracovník](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) oddíly.

## <a name="i-cannot-manually-detect-sign-in-fields-for-my-application"></a>I nedokáže detekovat ručně pole přihlášení pro Moje aplikace

Některá chování, které se můžete setkat při nepracuje ruční detekce patří:

-   Proces ručního zachycení zobrazovaly pracovat, ale pole zachytit nebyla správná

-   Pole pravé nemáte získat zvýrazněná při provádění proces zachycení

-   Proces zachycení trvá mi na přihlašovací stránku aplikace podle očekávání, ale nic nestane

-   Ruční zachycení pravděpodobně fungovat, ale jednotného přihlašování nemá dojít v případě, že moje uživatelé přejdou do aplikace na přístupovém panelu.

Pokud narazíte na některý z těchto problémů zkontrolujte následující:

-   Zajistěte, abyste měli nejnovější verzi rozšíření přístup k panelu prohlížeče **nainstalován** a **povoleno** podle kroků v [postup instalace rozšíření přístup k panelu prohlížeče](#how-to-install-the-access-panel-browser-extension) části.

-   Ujistěte se, že proces zachycení neprovádíte při prohlížeč v **privátní, se službou inPrivate nebo incognito režimu**. Rozšíření přístup k panelu v těchto režimech nepodporuje.

-   Ujistěte se, že uživatelé nejsou pokouší přihlásit k aplikaci na přístupovém panelu při v **privátní, se službou inPrivate nebo incognito režimu**. Rozšíření přístup k panelu v těchto režimech nepodporuje.

-   Zkuste ruční proces zachycení znovu, zajištění, že red značky jsou přes správná pole.

-   Pokud proces ruční zachycení zdá se, že zablokuje nebo na přihlašovací stránce neprovádí proces ruční zachycení nic (případě 3 výše), opakujte akci. Ale tentokrát po dokončení procesu, stiskněte **F12** tlačítko otevřete konzolu pro vývojáře v prohlížeči. Jednou existuje, otevřete **konzoly** a typ **window.location= "&lt;zadejte přihlašovací adresu url jste zadali při konfiguraci aplikace&gt;"** a potom stiskněte klávesu **Enter** . Vynutí přesměrování stránky, která ukončí proces zachycení a uloží pole, která byla zachycena.

Pokud žádný z těchto postupů fungovat pro vás, může přispět podpora. Otevření případu podpory podrobnosti co jste se pokusili, a také informace shromážděné ve [postup najdete v části Podrobnosti o portálu oznámení](#i-cannot-manually-detect-sign-in-fields-for-my-application) a [jak získat nápovědu zasláním oznámení podrobnosti pracovníkovi podpory](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) částech (pokud existuje).

## <a name="how-to-install-the-access-panel-browser-extension"></a>Postup instalace rozšíření přístup k panelu prohlížeče

Chcete-li nainstalovat rozšíření přístup k panelu prohlížeče, postupujte následujícím způsobem:

1.  Otevřete [přístupový Panel](https://myapps.microsoft.com) v jednom z podporovaných prohlížečích a přihlaste se jako **uživatele** ve službě Azure AD.

2.  Klikněte na tlačítko **SSO heslo aplikace** na přístupovém panelu.

3.  V řádku, požádá o instalaci softwaru, vyberte **instalovat nyní**.

4.  Založené na prohlížeči budete přesměrováni na odkaz ke stažení. **Přidat** rozšíření do prohlížeče.

5.  Pokud prohlížeč zobrazí dotaz, vyberte buď **povolit** nebo **povolit** rozšíření.

6.  Po instalaci **restartujte** relaci prohlížeče.

7.  Přihlaste se do přístupového panelu a zobrazit, pokud můžete **spusťte** SSO hesla aplikací.

Rozšíření pro Chrome a Firefox může také stáhnout z přímé odkazy níže:

-   [Rozšíření pro Chrome přístup panely](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozšíření panelu Firefox přístup](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Postup najdete v části Podrobnosti o portálu oznámení

Pomocí následujícího postupu můžete zobrazit podrobnosti o portálu oznámení:

1.  klikněte **oznámení** ikonu (zvonku) v pravém horním rohu stránky na portálu Azure

2.  Vyberte všechna oznámení v **chyba** stavu (ty se zobrazí červený (!) vedle je).

  >! Všimněte si] je nelze kliknout oznámení v **úspěšné** nebo **probíhá** stavu.
  >
  >

3.  **Podrobnosti oznámení** otevře se podokno.

4.  Pomocí informací sami, abyste porozuměli další podrobnosti o problému.

5.  Pokud stále potřebujete pomoc, můžete také sdílet informace s pracovníka podpory nebo product group, které chcete získat pomoc s váš problém.

6.  Klikněte na tlačítko **kopie** **ikonu** napravo od **Chyba kopírování** textbox zkopírovat všechny podrobnosti oznámení sdílet s skupiny pracovník podpory nebo produktu.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Získání nápovědy poslat podrobnosti oznámení na pracovníka podpory

To je velmi důležité, že můžete sdílet **všechny níže uvedené podrobnosti** s pracovníka podpory, pokud potřebujete pomoc, tak, aby se vám může pomoct rychle. Můžete **pořídit snímek,** nebo klikněte na tlačítko **ikona chyby kopie**, který se nachází vpravo od **Chyba kopírování** textové pole.

## <a name="notification-details-explained"></a>Vysvětlení podrobnosti oznámení

Níže se dozvíte více co každý oznámení položky znamená a jsou uvedeny příklady každý z nich.

### <a name="essential-notification-items"></a>Základní oznámení položky

-   **Název** – popisný název oznámení.

    -   Příklad – **nastavení proxy aplikace**

-   **Popis** – popis co došlo k chybě v důsledku operaci

    -   Příklad – **zadaná interní adresa url je již používán jinou aplikací**

-   **ID oznámení** – jedinečné id oznámení.

    -   Příklad – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **ID žádosti klienta** – id konkrétní žádosti provedené prohlížeč

    -   Příklad – **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **Čas UTC razítko** – časové razítko, během které oznámení došlo k chybě, v UTC

    -   Příklad – **2017-03-23T19:50:43.7583681Z**

-   **Interní ID transakce** – interní ID použita k vyhledání Chyba v našem systému

    -   Příklad – **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **Hlavní název uživatele** – uživatel, který provedl operaci

    -   Příklad – **tperkins@f128.info**

-   **ID klienta** – jedinečné ID klienta, který uživatel, který provedl operaci byl uživatel členem

    -   Příklad – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **ID objektu uživatelské** – jedinečné ID uživatele, který provádí operaci

    -   Příklad – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Podrobné oznámení položky

-   **Zobrazovaný název** – **(nesmí být prázdné)** podrobnější zobrazovaný název chyby

    -   Příklad * – **nastavení proxy aplikace**

-   **Stav** – konkrétní stav oznámení.

    -   Příklad * – **se nezdařilo**

-   **ID objektu** – **(nesmí být prázdné)** ID objektu, pro kterou byla provedena operace

    -   Příklad – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Podrobnosti o** – podrobný popis co došlo k chybě v důsledku operaci

    -   Příklad – **interní adresa url sehttp://bing.com/' je neplatný, protože je již používán**

-   **Chyba při kopírování** – klikněte na tlačítko **ikona kopírování** napravo od **Chyba kopírování** textbox zkopírovat všechny podrobnosti oznámení sdílet s skupiny pracovník podpory nebo produkt

    -   Příklad – ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'http://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'http://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Další postup
[Zadejte jednotné přihlašování pro vaše aplikace s Proxy aplikace](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)


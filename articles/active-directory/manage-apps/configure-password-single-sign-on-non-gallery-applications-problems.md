---
title: Problémy s konfigurací heslem jednotného přihlašování pro aplikaci mimo Galerii | Dokumentace Microsoftu
description: Běžné problémy, ke kterým dochází při konfiguraci hesla jednotné přihlašování (SSO) pro vlastní aplikace, které nejsou v galerii aplikací Azure AD.
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
ms.openlocfilehash: 24330dc874173ba1c6f15abb7b4caf9f23e2e00c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440355"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problémy s konfigurací heslem jednotného přihlašování pro aplikaci mimo Galerii

Tento článek popisuje běžné problémy, které může dojít, když nakonfigurujete *heslem jednotného přihlašování* (SSO) pro aplikaci mimo galerii.

## <a name="capture-sign-in-fields-for-an-app"></a>Zachytit pole přihlášení pro aplikaci

Pole zachycování je podporováno pouze pro povolené HTML přihlašovací stránky. To má není podporována pro nestandardní přihlašovacích stránek služby, jako jsou ty, které používají Adobe Flash nebo jiné technologie HTML nepodporujícím.

Existují dva způsoby, jak zachytit pole pro vaše vlastní aplikace:

- **Automatické přihlášení pole zachycení** dobře funguje pro většinu podporujících formát HTML přihlašovací stránky, *pokud používají známé identifikátory DIV* pro pole uživatelské jméno a heslo. K vyhledání ID DIV, které splňují určitá kritéria, je získaný kód HTML na stránce. Tato metadata se uloží, takže ho můžete znovu přehrát aplikaci později.

- **Ruční pole zachycení** se používá v případě jejím dodavatelem *nemá popisek přihlašovací vstupní pole*. Ruční zachytávání se také používá, pokud dodavatel *vykreslí několik polí, která nelze automaticky rozpoznané*. Azure Active Directory (Azure AD) můžete ukládat data pro libovolný počet polí tak, že existují na přihlašovací stránku, pokud můžete určit, kde jsou tato pole na stránce.

Obecně platí Pokud zachytit pole automatické přihlašování nefunguje, vyzkoušet ruční variantu.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Automaticky zachytit pole přihlášení pro aplikaci

Konfigurace jednotného přihlašování pomocí hesla pomocí zachycení pole automatické přihlašování, postupujte podle těchto kroků:

1. Otevřete web [Azure Portal](https://portal.azure.com/). Přihlaste se jako globální správce nebo spolusprávce.

2. V navigačním podokně na levé straně vyberte **všechny služby** otevřít rozšíření Azure AD.

3. Typ **Azure Active Directory** do vyhledávacího pole filtrovat a pak vyberte **Azure Active Directory**.

4. Vyberte **podnikové aplikace** v navigačním podokně Azure AD.

5. Vyberte **všechny aplikace** zobrazení seznamu aplikací.

   > [!NOTE]
   > Pokud nevidíte aplikaci, kterou chcete, použijte **filtr** ovládacího prvku v horní části **všechny aplikace** seznamu. Nastavte **zobrazit** možnost "Všech aplikací."

6. Vyberte aplikaci, kterou chcete konfigurovat pro jednotné přihlašování.

7. Po dokončení načítání aplikace, vyberte **jednotného přihlašování** v navigačním podokně na levé straně.

8. Vyberte **přihlašování na základě heslo** režimu.

9. Zadejte **přihlašovací adresa URL**, což je adresa URL stránky, kde uživatelé zadat svoje uživatelské jméno a heslo pro přihlášení. *Ujistěte se, že pole přihlášení se zobrazují na stránce pro adresu URL, kterou zadáte*.

10. Vyberte **Uložit**.

    Na stránce je automaticky získaný pro uživatelské jméno a heslo vstupních polí. Nyní můžete v Azure AD bezpečně přenášet hesla do této aplikace s použitím rozšíření prohlížeče přístupového panelu.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Ručně zachytit pole přihlášení pro aplikaci

Chcete-li ručně zachytit pole pro přihlášení, musíte mít nainstalované rozšíření prohlížeče přístupového panelu. Navíc prohlížeči nemůže běžet ve *se službou inPrivate*, *incognito*, nebo *privátní* režimu.

K instalaci rozšíření, najdete v článku [nainstalovat rozšíření prohlížeče Panel přístupu](#install-the-access-panel-browser-extension) části tohoto článku.

Konfigurace založené na heslech jednotné přihlašování pro aplikace pomocí ruční pole zachycení, postupujte podle těchto kroků:

1. Otevřete web [Azure Portal](https://portal.azure.com/). Přihlaste se jako globální správce nebo spolusprávce.

2. V navigačním podokně na levé straně vyberte **všechny služby** otevřít rozšíření Azure AD.

3. Typ **Azure Active Directory** do vyhledávacího pole filtrovat a pak vyberte **Azure Active Directory**.

4. Vyberte **podnikové aplikace** v navigačním podokně Azure AD.

5. Vyberte **všechny aplikace** zobrazení seznamu aplikací.

   > [!NOTE] 
   > Pokud nevidíte aplikaci, kterou chcete, použijte **filtr** ovládacího prvku v horní části **všechny aplikace** seznamu. Nastavte **zobrazit** možnost "Všech aplikací."

6. Vyberte aplikaci, kterou chcete konfigurovat pro jednotné přihlašování.

7. Po dokončení načítání aplikace, vyberte **jednotného přihlašování** v navigačním podokně na levé straně.

8. Vyberte **přihlašování na základě heslo** režimu.

9. Zadejte **přihlašovací adresa URL**, což je stránka, kde uživatelé zadat svoje uživatelské jméno a heslo pro přihlášení. *Ujistěte se, že pole přihlášení se zobrazují na stránce pro adresu URL, kterou zadáte*.

10. Vyberte **konfigurovat *&lt;appname&gt;* nastavení hesla jednotného přihlašování**.

11. Vyberte **ručně zjišťovat pole pro přihlášení**.

14. Vyberte **OK**.

15. Vyberte **Uložit**.

16. Postupujte podle pokynů a použít přístupový Panel.

## <a name="troubleshoot-problems"></a>Poradce při potížích

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Získat chybu "Jsme nenašli žádné pole na této adrese URL"

Pokud se nezdaří automatické zjišťování pole se zobrazit tato chybová zpráva. Chcete-li vyřešit tento problém, zkuste detekce ruční pole. Zobrazit [ručně zachytit pole přihlášení pro aplikaci](#manually-capture-sign-in-fields-for-an-app) části tohoto článku.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Můžu získat "Nelze pro uložení konfigurace jednotného přihlašování" Chyba

Jen zřídka aktualizuje se konfigurace jednotného přihlašování se nezdaří. Chcete-li vyřešit tento problém, uložte konfiguraci znovu.

Pokud bude dále zobrazovat chybu, otevřete případ podpory. Zahrnují informace, podle popisu v [zobrazit podrobnosti o portálu oznámení](#view-portal-notification-details) a [Odeslat podrobnosti o oznámení pro pracovníka podpory pomoc](#send-notification-details-to-a-support-engineer-to-get-help) částech tohoto článku.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Můžu nerozpozná ručně pole pro moji aplikaci

Můžete všimnout následujícího chování při ruční detekce nefunguje:

- Proces ručního zachycení pravděpodobně fungovat, ale zachycené pole nejsou správné.

- Správná pole není získat zvýrazněnou při spuštění procesu zachycení.

- Proces sběru vás přesměruje na přihlašovací stránce aplikace podle očekávání, ale nic se nestane.

- Ruční zachycení zdánlivě fungovat, ale jednotného přihlašování nemá dojít, když uživatelé přejdou na aplikace z přístupového panelu.

Pokud dochází k některým z těchto problémů, proveďte následující akce:

- Ujistěte se, že máte nejnovější verzi rozšíření prohlížeče přístupového panelu *nainstalovaný a povolený*. Zobrazit [nainstalovat rozšíření prohlížeče přístupového panelu](#install-the-access-panel-browser-extension) části tohoto článku.

- Ujistěte se, že váš prohlížeč není v *incognito*, *se službou inPrivate*, nebo *privátní* režimu během procesu zachycení. Přístupový Panel rozšíření není podporováno v těchto režimech.

- Ujistěte se, že vaši uživatelé nejsou pokoušel se přihlásit k aplikaci z přístupového panelu při v *incognito*, *se službou inPrivate*, nebo *privátním režimu*.

- Opakujte proces ručního zachycení. Ujistěte se, že červené značky jsou přes správné pole.

- Pokud proces ručního zachycení zdá se, že přestane reagovat nebo nereaguje na přihlašovací stránku, opakujte proces ručního zachycení. Ale tentokrát po dokončení procesu, stisknutím klávesy F12 otevřete konzolu pro vývojáře v prohlížeči. Vyberte **konzoly** kartu. Typ **window.location= " *&lt;přihlašovací adresa URL, který jste zadali při konfiguraci aplikace&gt;* "** , a potom stiskněte klávesu Enter. To vynutí přesměrování stránky, která ukončí proces zachycení a uloží pole, které byly zachyceny.

### <a name="contact-support"></a>Kontaktujte podporu

Pokud stále dochází k potížím, otevřete případ s Microsoft Support. Popište, co jste se pokusili. Zahrnout podrobnosti, které jsou popsány v [zobrazit podrobnosti o portálu oznámení](#view-portal-notification-details) a [Odeslat podrobnosti o oznámení pro pracovníka podpory pomoc](#send-notification-details-to-a-support-engineer-to-get-help) částech tohoto článku (pokud existuje).

## <a name="install-the-access-panel-browser-extension"></a>Instalace rozšíření prohlížeče přístupového panelu

Postupujte následovně:

1. Otevřít [přístupového panelu](https://myapps.microsoft.com) v podporovaném prohlížeči. Přihlaste se k Azure AD jako *uživatele*.

2. Vyberte **heslem jednotného přihlašování aplikace** přístupovém panelu.

3. Po zobrazení výzvy k instalaci softwaru, vyberte **nainstalovat**.

4. Budete přesměrováni na stránku stahování pro váš prohlížeč. Zvolit **přidat** rozšíření.

5. Pokud budete vyzváni, vyberte **povolit** nebo **povolit**.

6. Po instalaci restartujte prohlížeč.

7. Přihlaste se na přístupovém panelu. Podívejte se, pokud lze otevřít vaše heslo podporou jednotného přihlašování aplikace.

Můžete také přímo stáhnout rozšíření prohlížeče Chrome a Firefox prostřednictvím těchto odkazů:

-   [Rozšíření Chrome přístupového panelu](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozšíření Firefox přístupového panelu](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>Zobrazit podrobnosti o portálu oznámení

Pokud chcete zobrazit podrobnosti o každém portálu oznámení, postupujte podle těchto kroků:

1. Vyberte **oznámení** ikonu (zvonek) v pravém horním rohu webu Azure portal.

2. Vyberte všechna oznámení, který ukazuje *chyba* stavu. (Mají červený "!".)

   > [!NOTE]
   > Nelze vybrat oznámení, které jsou v *úspěšné* nebo *probíhá* stavu.

3. **Podrobnosti oznámení** se otevře podokno. Přečtěte si informace o další informace o problému.

5. Pokud stále potřebujete pomoc, sdílejte informace s pracovníkem technické podpory nebo produktové skupiny. Vyberte **kopírování** ikony napravo **Kopírovat chybu** pole zkopírovat podrobnosti oznámení, které chcete sdílet.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Odeslat podrobnosti o oznámení pro pracovníka podpory k získání nápovědy

Je důležité, jakým sdílíte *všechny* podrobnosti, které jsou uvedené v této části s podporou tak, aby se vám může pomoct rychle. Poznamenejte si ho, můžete pořídit snímek obrazovky nebo vyberte **Kopírovat chybu**.

Následující informace vysvětluje, co jednotlivé položky oznámení znamená, že a poskytuje příklady.

### <a name="essential-notification-items"></a>Základní oznámení položky

- **Název**: popisný název oznámení.

   Příklad: *Nastavení proxy aplikace.*

- **Popis**: k čemu došlo v důsledku operace.

   Příklad: *Zadanou vnitřní adresu URL už používá jiná aplikace.*

- **ID oznámení**: jedinečné ID oznámení.

    Příklad: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **ID žádosti klienta**: ID konkrétní požadavek, který vytvořil váš prohlížeč.

    Příklad: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Časové razítko UTC**: časové razítko při oznámení došlo k chybě, ve standardu UTC.

    Příklad: *2017-03-23T19:50:43.7583681Z*

- **Interní ID transakce**: interní ID, který se používá k vyhledání Chyba v našem systému.

    Příklad: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **HLAVNÍ NÁZEV UŽIVATELE**: Uživatel, který operaci spustil.

    Příklad: *tperkins\@f128.info*

- **ID tenanta**: jedinečné ID tenanta, který je členem uživatel, který operaci spustil.

    Příklad: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **ID objektu uživatele**: Jedinečné ID uživatele, který operaci spustil.

    Příklad: *17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>Podrobný oznamovací položky

- **Zobrazovaný název**: (může být prázdné) podrobnější zobrazovaný název pro chybu.

    Příklad: *Nastavení proxy aplikace.*

- **Stav**: konkrétní stavové oznámení.

    Příklad: *Se nezdařilo*

- **ID objektu**: (může být prázdné) ID objektu, pro který byla spustit operaci.

   Příklad: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Podrobnosti o**: podrobný popis, k čemu došlo v důsledku operace.

    Příklad: *Interní adresa url '<https://bing.com/>"je neplatná, protože je již používán.*

- **Chyba při kopírování**: Umožňuje vybrat **ikonu kopírování** napravo od **Kopírovat chybu** textového pole zkopírujte podrobnosti oznámení, které pomůžou s podporou.

    Příklad:   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Další postup
[Poskytovat jednotné přihlašování do aplikací pomocí Proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)

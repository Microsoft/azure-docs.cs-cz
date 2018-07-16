---
title: Nápověda k aplikaci Microsoft Authenticator – Azure AD | Dokumentace Microsoftu
description: Obsahuje seznam nejčastějších dotazů a odpovědi související s aplikací Microsoft Authentication a ověřování Azure Multi-Factor Authentication.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/08/2018
ms.author: lizross
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: d86bc84653e38a9b64a336b8ce9ed7e657129e8c
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059827"
---
# <a name="microsoft-authenticator-app-faq"></a>Aplikace Microsoft Authenticator – nejčastější dotazy

Tento článek obsahuje odpovědi na běžné dotazy týkající se aplikace Microsoft Authenticator. Pokud nevidíte odpověď na svoji otázku, přejděte [fórum aplikace Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp). Kromě toho můžete zkontrolovat další nejčastější dotazy týkající se konkrétní funkce v aplikaci, [přihlášení pomocí telefonu nejčastější dotazy k](microsoft-authenticator-app-phone-signin-faq.md).

Aplikace Microsoft Authenticator nahradit aplikaci Azure Authenticator a je doporučenou aplikaci, pokud používáte ověřování Azure Multi-Factor Authentication. Aplikace Microsoft Authenticator je k dispozici pro [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) a [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="what-data-does-the-authenticator-store-on-my-behalf-and-how-can-i-delete-it"></a>Jaká data ověřovacích ukládá na můj účet a jak ho můžete odstranit?

Microsoft Authenticator ukládá informace o účtu, který vytvoříte, když přidáte nějaký účet. Při použití Authenticator diagnostický protokol je vytvořili pro účely ladění a uloží užitečná data v pomáhá společnosti Microsoft diagnostikovat problémy s nepředvídatelnými. Data protokolu se zpřístupní po otevření **pomáhají** > **odeslat protokoly** > **zobrazit protokoly**.

Data můžete odstranit tak, že odstraníte dlaždici účtu. Odstraňuje se účet dlaždice se odstraní také všechny informace o účtech používá aplikace, včetně protokolů. 

Další informace o tom, jak Microsoft používá data naleznete zde: https://servicetrust.microsoft.com/ViewPage/PrivacyGettingStarted

### <a name="what-are-the-codes-in-the-app-for-why-does-the-number-keep-counting-down"></a>Co jsou kódy v aplikaci pro? Proč čísla zachovat odpočítávání?

Když otevřete aplikaci Microsoft Authenticator, uvidíte účty, které jste přidali a současně všech šest nebo osm číslic čísla každou z nich. Může se zobrazit 30sekundovém časovač odpočítávání.

Tyto kódy se používají při přihlášení ke svému účtu. Po zadání uživatelského jména a hesla, může se zobrazit výzva k zadejte ověřovací kód. Otevřete aplikaci Microsoft Authenticator a zkopírujte kód, který se aktuálně zobrazují. Zadejte přihlašovací stránku a dokončete tento kód.

Proč se kód změnit každých 30 sekund je tak, aby nikdy používat stejný kód dvakrát. Není třeba heslo, které se má mějte na paměti. Cílem je, že pouze uživatelé s přístupem na váš telefon ví svůj ověřovací kód.

Kódy nevyžadují internet nebo data, takže nemusíte obávat o telefonní služby k přihlášení. Jakmile ukončíte aplikaci, nebude dál běžet v pozadí a nespotřebovává baterie. Můžete zavřít aplikaci a ignorovat až do příštího přihlášení.  

### <a name="i-only-get-notifications-when-i-have-the-app-open-if-the-app-isnt-open-i-dont-get-any-notifications"></a>Pouze oznámení při zobrazí mám aplikaci otevřené. Pokud aplikace není otevřen, nezobrazí žádná upozornění.

Pokud se zobrazí oznámení, ale nechcete provést šumu nebo uvede do vibrace bez ohledu na vaši vyzvánění, že, nejprve zkontrolujte nastavení aplikace. Povolte aplikaci ke zvukovému sdělení, uvede do vibrace s jeho oznámení.

Pokud neobdržíte oznámení vůbec, najdete následujících případech:

- Je váš telefon v Nerušit nebo tichý režim? Tento režim zachovat odesílání oznámení z aplikace.
- Budete dostávat oznámení z jiných aplikací? V opačném případě může být problém se síťovými připojeními na váš telefon nebo kanál oznámení z Androidu nebo Apple. První možnost se vyřešit v nastavení telefonu, ale možná budete muset obraťte se na váš poskytovatel služeb pro pomoc s druhou možnost.
- Budete dostávat oznámení pro některé účty v aplikaci, ale ne pro jiné? Pokud ano, problematické účet odebrat z vaší aplikace a přidejte ji znovu povolte nabízená oznámení.

Pokud tyto tipy pro řešení potíží se pokusili, ale pořád máte problémy, můžete odeslat protokoly pro diagnostiku. Přejděte do nastavení aplikace a pak vyberte **Nápověda a zpětná vazba** a **odeslat protokoly**. Potom pokračujte [fórum aplikace Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) a dejte nám vědět, co problém, jak vidíte a jaké kroky jste se pokusili zatím.

### <a name="im-already-using-the-microsoft-authenticator-application-for-verification-codes-how-do-i-switch-to-one-click-push-notifications"></a>Používám už aplikace Microsoft Authenticator pro ověřovací kódy. Jak přepnout na jedním kliknutím nabízených oznámení?
Schvalování u přihlášení prostřednictvím nabízených oznámení je dostupná jenom pro osobní účty Microsoft nebo pracovních a školních účtů Microsoft, nikoli pro účty třetích stran, jako je Google nebo Facebook. Pokud máte pracovní nebo školní účet Microsoft, vaše organizace můžete zakázat tuto možnost.

Pokud používáte účet Microsoft pro váš osobní účet a chcete přejít k odesílání nabízených oznámení, musíte přidat svůj účet znovu. Znovu zaregistrovat zařízení s vaším účtem a nastavení nabízených oznámení.  

Pokud používáte Microsoft Authenticator pro váš pracovní nebo školní účet, pak se vaše organizace rozhodne, zda se má povolit oznámení jedním kliknutím.

### <a name="do-one-click-push-notifications-work-for-non-microsoft-accounts"></a>Jedním kliknutím nabízená oznámení fungují pro účty jiných společností než Microsoft
Ne, nabízená oznámení fungují pouze u účtů Microsoft a účtů služby Azure Active Directory. Pokud vaše firma nebo škola používá účty Azure AD, mohou tuto funkci zakázat.  

### <a name="i-got-a-new-device-or-restored-my-device-from-a-backup-how-do-i-set-up-my-accounts-in-the-microsoft-authenticator-app-again"></a>Můžu je teď nové zařízení nebo obnovení zařízení ze zálohy. Jak nastavím my účtů v aplikaci Microsoft Authenticator znovu?
Pokud používáte zařízení s Iosem, jste zapnuli **automatické zálohování**a vytvořili zálohu vašich účtů v zařízení stará; můžete použít tuto zálohu k obnovení přihlašovacích údajů k účtu svoje nové zařízení. Další informace najdete v tématu [zálohování a obnovení přihlašovacích údajů účtu v aplikaci Microsoft Authenticator](microsoft-authenticator-app-backup-and-recovery.md) článku. 

### <a name="i-lost-my-device-or-moved-on-to-a-new-device-how-do-i-make-sure-notifications-dont-continue-to-go-to-my-old-device"></a>Můžu dojde ke ztrátě zařízení nebo přesunul se na nové zařízení. Jak se ujistěte se, že oznámení není nadále přejít na staré zařízení?  
Přidání aplikace Microsoft Authenticator na nová zařízení s Iosem neodebere automaticky aplikace ze zařízení s původní. Ještě se aplikace odstranila ze staré zařízení není k dispozici dostatek. Musíte jak odstranit aplikaci ze staré zařízení a informace společnosti Microsoft nebo organizaci zapomenout se starým zařízením a zrušit registraci účtu.
- **Chcete tuto aplikaci odinstalovat ze zařízení pomocí osobního účtu Microsoft.** Přejděte do oblasti dvoustupňové ověření vašeho [zabezpečení účtu](https://account.microsoft.com/security) stránce a vypnout ověřování pro vaše staré zařízení.  
- **Odebere aplikaci ze zařízení pomocí svého pracovního nebo školního účtu Microsoft.** Přejděte do oblasti dvoustupňové ověření vašeho [MyApps](https://myapps.microsoft.com/) stránku nebo na vlastním portálu vaší organizace a vypnout ověřování pro vaše staré zařízení. 



### <a name="how-do-i-remove-an-account-from-the-app"></a>Jak odebrat účet z aplikace
* iOS: Z hlavní obrazovky, potažením doleva na dlaždici účtu. Vyberte **Odstranit**.
* Windows Phone: Z hlavní obrazovky, vyberte tlačítko nabídky, poté **úpravy účtů**. Klepněte **X** vedle názvu účtu.
* Android: Z hlavní obrazovky, vyberte tlačítko nabídky, poté **úpravy účtů**. Klepněte **X** vedle názvu účtu.

Pokud máte zařízení, které je registrované ve vaší organizaci, budete muset provést další krok pro svůj účet odeberte. V těchto zařízeních se aplikace Microsoft Authenticator automaticky zaregistruje jako správce zařízení. Pokud chcete úplně odinstalovat aplikaci, musíte nejprve zrušit registraci aplikace v aplikaci nastavení.

### <a name="why-does-the-app-request-so-many-permissions"></a>Proč žádostí aplikace o tolik oprávnění?
Tady je úplný seznam oprávnění, která můžete požádat a jak se používají v aplikaci. Konkrétní oprávnění, která se zobrazí závisí na typu telefon, který máte.

* **Fotoaparát**: umožňuje skenovat kódy QR, když přidáte pracovní, školní nebo jiného subjektu než Microsoft účtu.
* **Kontakty a phone**: využívají ke zjednodušení procesu tím, že hledá existujících účtů v telefonu při přihlašování pomocí osobního účtu Microsoft.
* **SMS**: používá k zajištění toho, telefonní číslo odpovídá číslu na záznam. Po přihlášení pomocí osobního účtu Microsoft poprvé.  Zašleme textovou zprávu do telefonu kterého jste stáhli aplikaci, která zahrnuje 6 – 8 místný číselný ověřovací kód. Místo s výzvou k vyhledání tento kód a zadejte ho v aplikaci, je nalezen v textovou zprávu za vás.
* **Kreslení v jiných aplikacích**: dostanete oznámení, který ověří vaši identitu se zobrazuje taky na jakoukoli jinou aplikaci, která může být spuštěn.
* **Příjem dat z Internetu**: Tato oprávnění jsou nutná pro odesílání oznámení.
* **Zabránit telefon v režimu spánku**: Pokud registraci zařízení ve vaší organizaci vaší organizaci můžete změnit tyto zásady na váš telefon.
* **Ovládací prvek pronikavost**: můžete zvolit, jestli byste chtěli vibrace, pokaždé, když obdržíte oznámení ověřit vaši identitu.
* **Pomocí otisku prstu hardware**: některé pracovním a školním účtům vyžadovat další kód PIN, pokaždé, když se ověřit vaši identitu. PRO usnadnění procesu jsme umožňují používat svůj otisk prstu místo zadáním kódu PIN.
* **Zobrazení síťových připojení**: Když přidáte účet Microsoft, aplikace vyžaduje připojení k síti a Internetu.
* **Číst obsah úložiště**: Toto oprávnění se používá pouze při nahlásit technický problém pomocí nastavení aplikace. Některé informace z vašeho úložiště jsou shromažďovány k diagnostice problému.
* **Úplný přístup k síti**: Tato oprávnění jsou nutná pro odesílání oznámení ověřit vaši identitu.
* **Spustit při spuštění**: Pokud je restartovat svůj telefon, toto oprávnění zajistí, že budete pokračovat, budete dostávat oznámení ověřit vaši identitu.

### <a name="why-does-the-microsoft-authenticator-app-allow-you-to-approve-a-request-without-unlocking-the-device"></a>Proč si aplikaci Microsoft Authenticator Umožňuje schválit žádost o bez odemknutí zařízení?

Není nutné k odemykání zařízení ke schválení žádosti o ověření, protože budete potřebovat prokázat, že je, že máte telefon s vámi. Dvoustupňové ověřování vyžaduje prokázání dvě věci – si to, které už znáte a co musíte. Víte, co je vaše heslo. Co budete mít, je váš telefon (nastavení pomocí aplikace Microsoft Authenticator a zaregistrovat jako MFA důkazu.) Proto s telefonu a schválit požadavek splňuje kritéria pro druhý faktor ověřování.

### <a name="what-does-the-lock-icon-in-the-account-list-mean"></a>Co znamená ikona zámku v seznamu účtů?

Ikona visací zámek označuje, že je zařízení zaregistrované ve službě Azure AD a zaregistrované k účtu. Registrace zařízení s Iosem probíhá během registrace v Microsoft Intune.

## <a name="next-steps"></a>Další postup

### <a name="contact-us"></a>Kontaktujte nás
Pokud jste tady, chceme znát váš názor. Přejděte [fórum aplikace Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) zveřejněte svůj dotaz a získat pomoc od komunity nebo komentář na této stránce.


### <a name="related-topics"></a>Související témata
* [O dvoustupňovém ověřování](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) u účtů Microsoft
* [Potíže s dvoustupňovým ověřováním](multi-factor-authentication-end-user-troubleshoot.md) pro váš pracovní nebo školní účet?
* [Využijte Microsoft Authenticator pro přihlášení z telefonu](microsoft-authenticator-app-phone-signin-faq.md)

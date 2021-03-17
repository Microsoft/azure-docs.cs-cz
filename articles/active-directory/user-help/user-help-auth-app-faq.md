---
title: Otázky & odpovědi týkající se Microsoft Authenticator aplikace – Azure AD
description: Nejčastější dotazy a odpovědi (FAQ) o aplikaci ověřování Microsoft a dvojúrovňové ověřování.
services: active-directory
author: curtand
manager: daveba
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/28/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 8ddc02075d2cdeba4a4fa83a2475ec20c3f18d6e
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418075"
---
# <a name="frequently-asked-questions-faq-about-the-microsoft-authenticator-app"></a>Nejčastější dotazy k aplikaci Microsoft Authenticator

Tento článek obsahuje odpovědi na běžné dotazy týkající se aplikace Microsoft Authenticator. Pokud nevidíte odpověď na svoji otázku, přejděte na [Fórum aplikace Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp).

Aplikace Microsoft Authenticator nahradila Azure Authenticator aplikaci a při použití Multi-Factor Authentication Azure AD se jedná o doporučenou aplikaci. Aplikace Microsoft Authenticator je k dispozici pro [Android](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator) a [iOS](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="registering-a-device"></a>Registrace zařízení

**Otázka**: zaregistruji zařízení, které přijímá přístup společnosti nebo služby k zařízení?

Odpověď **: registrace** zařízení dává vašemu zařízení přístup ke službám vaší organizace a neumožňuje vaší organizaci přístup k vašemu zařízení.

### <a name="error-adding-account"></a>Chyba při přidávání účtu

**Otázka**: při pokusu o přidání účtu se zobrazí chybová zpráva oznamující, že účet, který se pokoušíte přidat, není v tuto chvíli platný. Požádejte správce, aby tento problém vyřešil (ověření jedinečnosti). " Co bych měl/a dělat?

Odpověď **: obraťte** se na správce a sdělte mu, že se vám nedaří přidat účet do ověřovatele kvůli problému s ověřením jedinečnosti. Budete muset zadat uživatelské jméno pro přihlášení, aby vám správce mohl vyhledat ve vaší organizaci.

### <a name="legacy-apns-support-deprecated"></a>Starší verze podpory služby APNs je zastaralá.

**Otázka**: protože starší verze binárního rozhraní pro službu Apple Push Notification Service je v listopadu 2020 zastaralá, jak můžu i nadále používat Microsoft Authenticator/Phone faktor pro přihlášení?

Odpověď: [Apple oznámil](https://developer.apple.com/news/?id=11042019a) vyřazení nabízených oznámení, která používají své binární rozhraní pro zařízení s iOS, jako jsou například ty, které používá telefonní faktor. Aby bylo možné nadále přijímat nabízená oznámení, doporučujeme, aby uživatelé aktualizovali svou ověřovací aplikaci na nejnovější verzi aplikace. Mezitím je můžete obejít ruční kontrolou oznámení v ověřovací aplikaci.

### <a name="app-lock-feature"></a>Funkce zámku aplikace

**Otázka**: co je zámek aplikace a jak ho můžu použít k lepšímu zabezpečení?

Odpověď **: zámek** aplikace pomáhá lépe zabezpečit vaše jednorázové ověřovací kódy, informace o aplikaci a nastavení aplikace. Když je povolen zámek aplikace, budete požádáni o ověření pomocí PIN kódu zařízení nebo biometriky pokaždé, když otevřete ověřovací data. Zámek aplikace také pomáhá zajistit, že jste jediným z nich, kdo může schvalovat oznámení pomocí výzvy k zadání kódu PIN nebo biometriky, kdykoli schválíte oznámení o přihlášení. Můžete zapnout nebo vypnout zámek aplikace na stránce nastavení ověřovatele. Ve výchozím nastavení je zámek aplikace zapnutý při nastavení PIN nebo biometriky na vašem zařízení.<br><br>Bohužel není nijak zaručeno, že zámek aplikace zabrání někomu v přístupu k ověřovacím údajům. Důvodem je, že k registraci zařízení může docházet v jiných umístěních mimo ověřovatel, například v nastavení účtu Android nebo v aplikaci Portál společnosti.

### <a name="windows-mobile-retired"></a>Systém Windows Mobile vyřazen

**Otázka**: Mám mobilní zařízení se systémem Windows a Microsoft Authenticator v systému Windows Mobile již byla zastaralá. Můžu pokračovat v ověřování pomocí aplikace?

**O**: všechna ověřování pomocí Microsoft Authenticator ve Windows Mobile budou vycházet po 15. červenci 2020. Důrazně doporučujeme použít alternativní metodu ověřování, abyste se vyhnuli uzamčení účtů.<br>Mezi alternativní možnosti pro podnikové uživatele patří:<br><ul><li>Nastavuje se Microsoft Authenticator pro [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) nebo [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).</li><li>[Nastavení serveru SMS](multi-factor-authentication-setup-phone-number.md) pro příjem ověřovacích kódů.</li><li>Nastavení telefonního čísla pro příjem [telefonních hovorů pro ověření jejich identity](multi-factor-authentication-setup-office-phone.md).</li></ul><br>Mezi alternativní možnosti pro uživatele osobních účet Microsoft patří:<br><ul><li>Nastavuje se Microsoft Authenticator pro [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) nebo [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).</li><li>Nastavení alternativní metody přihlašování (SMS nebo e-mailu) prostřednictvím aktualizace bezpečnostních údajů ze [stránky zabezpečení účtu Microsoft](https://account.microsoft.com/security/).</li></ul>

### <a name="android-screenshots"></a>Snímky obrazovky Androidu

**Otázka**: mohu pořizovat snímky obrazovky pro kód jednorázového hesla (jednorázového hesla) v ověřovacích zařízeních s Androidem?

Odpověď **: od** vydání verze 6.2003.1704 ověřování pro Android se ve výchozím nastavení všechny kódy jednorázového hesla skryjí, kdykoli se povede snímek obrazovky s ověřovacími daty. Pokud si chcete zobrazit kódy jednorázového hesla na snímcích obrazovky nebo pokud chcete, aby ostatní aplikace zachytí obrazovku ověřovatele, můžete. Stačí zapnout nastavení **zachycení obrazovky** v ověřovateli a restartovat aplikaci.

### <a name="delete-stored-data"></a>Odstranit uložená data

**Otázka**: jaká data uchovává úložiště ověřovatele a jak ho můžu odstranit?

Odpověď: aplikace ověřovatele shromažďuje tři typy **informací:**

- Informace o účtu, které poskytnete při přidávání účtu. Tato data můžete odebrat odebráním účtu.
- Data diagnostického protokolu, která zůstávají pouze v aplikaci, dokud **neodešlete zpětnou vazbu** do hlavní nabídky aplikace k odeslání protokolů společnosti Microsoft. Tyto protokoly můžou obsahovat osobní údaje, jako jsou e-mailové adresy, adresy serveru nebo IP adresy. Můžou taky obsahovat data zařízení, jako je název zařízení a verze operačního systému. Všechny Shromažďované osobní údaje jsou omezené na informace potřebné k řešení problémů s aplikacemi. V aplikaci můžete kdykoli procházet tyto soubory protokolů, aby se zobrazily shromažďované informace. Pokud odesíláte soubory protokolů, budou je technici aplikace ověřování používat jenom k řešení problémů hlášených zákazníky.
- Data o využití, která nejsou osobně identifikovatelná, například Začínáme přidat účet nebo úspěšně přidaný účet, nebo schválená oznámení Tato data jsou nedílnou součástí našich rozhodnutí o technickém rozhodování. Vaše využití nám pomáhá určit, kde můžeme aplikace vylepšit tak, jak jsou pro vás důležité. Při prvním použití aplikace se zobrazí oznámení o této kolekci dat. Informuje vás, že je možné ho vypnout na stránce **Nastavení** aplikace   . Toto nastavení můžete kdykoli zapnout nebo vypnout.

### <a name="codes-in-the-app"></a>Kódy v aplikaci

**Otázka**: Jaké jsou kódy v aplikaci pro?

Odpověď **: když** otevřete ověřovací data, zobrazí se vaše přidané účty jako dlaždice. Vaše pracovní nebo školní účty a vaše osobní účty Microsoft budou mít šest nebo osm číslic, které se zobrazí v zobrazení celé obrazovky účtu (k němuž se dostanete klepnutím na dlaždici účet). U ostatních účtů se na stránce **účty** aplikace zobrazí šest nebo osm číslic.<br>Tyto kódy použijete jako heslo s jedním použitím, abyste ověřili, že jste se sami řekli. Po přihlášení pomocí uživatelského jména a hesla zadáte ověřovací kód, který je přidružený k tomuto účtu. Pokud se například Katy přihlašujete k účtu contoso, klepněte na dlaždici účtu a pak použijte ověřovací kód 895823. Pro účet Outlooku byste měli postupovat podle stejných kroků.<br>Klepněte na dlaždici účtu contoso.<br>![Dlaždice účtu v ověřovací aplikaci](media/user-help-auth-app-faq/katy-signin.png)<br>Po klepnutí na dlaždici účtu společnosti Contoso je ověřovací kód viditelný na celé obrazovce.<br>![Ověřovací kód na dlaždici účtu v Authenticator](media/user-help-auth-app-faq/verification-code.png)

### <a name="countdown-timer"></a>Časovač odpočítávání

**Otázka**: Proč je číslo vedle kódu průběžně rozpočítáno?

Odpověď: u aktivního ověřovacího **kódu se může** zobrazit výpočet časovače za 30 sekund. Tento časovač je, takže se nikdy přihlašujete pomocí stejného kódu dvakrát. Na rozdíl od hesla si nepřejete pamatovat si toto číslo. Nápad je, že váš kód zná jenom někdo s přístupem k vašemu telefonu.

### <a name="grayed-account-tile"></a>Dlaždice šedý účet

**Otázka**: Proč je dlaždice můj účet šedá?

Odpověď **: některé** organizace vyžadují, aby ověřovací data pracovala s jednotným přihlašováním a chránila prostředky organizace. V takové situaci se účet nepoužívá pro dvoustupňové ověřování a zobrazuje se jako šedý nebo neaktivní. Tento typ účtu se často označuje jako "Broker" účet.

### <a name="device-registration"></a>Registrace zařízení

**Otázka**: co je registrace zařízení?

Odpověď **: vaše** organizace může vyžadovat, abyste si zaregistrovali zařízení, abyste mohli sledovat přístup k zabezpečeným prostředkům, například k souborům a aplikacím. Můžou taky zapnout podmíněný přístup, aby se snížilo riziko nechtěného přístupu k těmto prostředkům. V **Nastavení** můžete zrušit registraci zařízení, ale možná ztratíte přístup k e-mailům v Outlooku, souborům na OneDrivu a ztratíte možnost používat přihlášení telefonem.

### <a name="verification-codes-when-connected"></a>Ověřovací kódy při připojení

**Otázka**: musím být připojeni k Internetu nebo má síť pro získání a používání ověřovacích kódů?

Odpověď **: kódy** nevyžadují, abyste byli na internetu nebo jste připojeni k datům, takže nepotřebujete telefonickou službu pro přihlášení. Navíc vzhledem k tomu, že se aplikace přestane spouštět hned po jejím zavření, nevyprázdní baterii.

### <a name="no-notifications-when-app-is-closed"></a>Žádná oznámení, když je aplikace zavřená

**Otázka**: Proč mohu dostávat oznámení jenom při otevření aplikace? Když je aplikace zavřená, neobdržím oznámení.

Odpověď **: Pokud** obdržíte oznámení, ale ne upozornění, a to ani u vašeho vyzvánění na, měli byste si ověřit nastavení aplikace. Ujistěte se, že je aplikace zapnutá, aby se mohla používat zvuk nebo vibrace pro oznámení. Pokud se vám nezobrazují žádná oznámení, měli byste si ověřit následující podmínky:<ul><li>Je váš telefon v režimu Nerušit nebo tichý? Tyto režimy můžou zabránit aplikacím v posílání oznámení.</li><li>Můžete dostávat oznámení z jiných aplikací? Pokud ne, může se jednat o problém se síťovými připojeními na telefonu nebo s kanálem oznámení z Androidu nebo Apple. Můžete zkusit vyřešit síťová připojení prostřednictvím nastavení telefonu. Možná budete muset kontaktovat svého poskytovatele služeb a pomáhat s kanálem oznámení pro Android nebo Apple.</li><li>Můžete dostávat oznámení pro některé účty v aplikaci, ale ne jiné? Pokud ano, odeberte problematický účet z aplikace, znovu ho přidejte a umožněte oznámení a zjistěte, jestli tento problém vyřeší.</li></ul>Pokud jste si vyzkoušeli všechny tyto kroky a stále dochází k problémům, doporučujeme vám odeslat soubory protokolu pro diagnostiku. Otevřete aplikaci, přejděte do nabídky nejvyšší úrovně aplikace a pak vyberte **Odeslat názor**. Pak přejdete na [Fórum aplikace Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) a sdělte Microsoftu problém, který jste viděli, a kroky, které jste si vyzkoušeli.

### <a name="switch-to-push-notifications"></a>Přepnout na nabízená oznámení

**Otázka**: Používám ověřovacích kódů v aplikaci, ale jak můžu přepnout na nabízená oznámení?

Odpověď **: můžete** nastavit oznámení pro svůj pracovní nebo školní účet (pokud to povoluje správce) nebo pro osobní účet Microsoft. Oznámení nebudou fungovat pro účty třetích stran, jako je Google nebo Facebook.<br>Pokud chcete přepnout svůj osobní účet na oznámení, budete muset zařízení znovu zaregistrovat k účtu. Klikněte na **Přidat účet**, vyberte **osobní účet Microsoft** a potom se přihlaste pomocí svého uživatelského jména a hesla.<br>U svého pracovního nebo školního účtu rozhodne vaše organizace, jestli povolíte oznámení jedním kliknutím.

### <a name="notifications-for-other-accounts"></a>Oznámení pro jiné účty

**Otázka**: Pracujte s oznámeními pro účty jiných společností než Microsoft?

Odpověď **: ne**, oznámení fungují pouze s účty Microsoft a účty Azure Active Directory. Pokud vaše práce nebo škola používá účty Azure AD, může tuto funkci vypnout.

### <a name="backup-and-recovery"></a>Backup a obnovení

**Otázka**: Mám nové zařízení nebo obnovilo zařízení ze zálohy. Návody nastavit moje účty znovu v ověřovateli?

**O**: Pokud jste zapnuli **cloudové zálohování** na starém zařízení, můžete použít starou zálohu k obnovení přihlašovacích údajů svého účtu na novém zařízení s iOS nebo Androidem. Další informace najdete v článku [zálohování a obnovení pověření účtu pomocí ověřovatele](user-help-auth-app-backup-recovery.md) .

### <a name="lost-device"></a>Ztracené zařízení

**Otázka**: ztratil jsem zařízení nebo bylo přesunuto do nového zařízení. Návody zajistěte, aby oznámení nepokračovala v přechodu na staré zařízení?

Odpověď **: Přidání** ověřovatele do nového zařízení automaticky neodebere aplikaci ze starého zařízení. Ani odstranění aplikace ze starého zařízení není dostatečné. Musíte aplikaci odstranit ze starého zařízení a sdělit Microsoftu nebo vaší organizaci, aby zazapomněli a zrušili registraci starého zařízení.<ul><li>**Odebrání aplikace ze zařízení pomocí osobního účet Microsoft.** V rámci stránky [zabezpečení účtu](https://account.microsoft.com/security)v oblasti ověřování dvakrát na krok   proveďte možnost vypnutí ověřování starého zařízení.</li><li>**Pokud chcete aplikaci odebrat ze zařízení pomocí pracovní nebo školní účet Microsoft.** Chcete-li vypnout ověřování starého zařízení, v rámci své [stránky MyApp](https://myapps.microsoft.com/) nebo vlastního portálu ve vaší organizaci přepněte do oblasti ověřování dvěma kroky.</li></ul>

### <a name="remove-account-from-app"></a>Odebrat účet z aplikace

**Otázka**: návody odebrat účet z aplikace?

Odpověď **: klepněte** na dlaždici účtu pro účet, který chcete odebrat z aplikace, abyste zobrazili účet na celé obrazovce. Pokud chcete účet z aplikace odebrat, klepněte na **Odebrat účet** .<br>Pokud máte zařízení, které je zaregistrované ve vaší organizaci, možná budete potřebovat dodatečný krok k odebrání svého účtu. V těchto zařízeních se ověřovatel automaticky zaregistruje jako správce zařízení. Pokud chcete aplikaci úplně odinstalovat, musíte nejdřív zrušit registraci aplikace v nastavení aplikace.

### <a name="too-many-permissions"></a>Příliš mnoho oprávnění

**Otázka**: Proč aplikace požaduje mnoho oprávnění?

Odpověď **: zde** je úplný seznam oprávnění, která mohou být požádána o jejich použití v aplikaci. Konkrétní oprávnění, která vidíte, budou záviset na typu telefonu, který máte.<ul><li>**Umístění:** Někdy vaše organizace chce znát vaše umístění, než vám umožní přístup k určitým prostředkům. Aplikace bude požadovat toto oprávnění pouze v případě, že vaše organizace má zásady vyžadující umístění.</li><li>**Použijte biometrického hardwaru.** Některé pracovní a školní účty vyžadují další kód PIN, kdykoli ověříte svoji identitu. Aplikace vyžaduje váš souhlas s použitím biometriky nebo rozpoznávání obličeje místo zadání kódu PIN.</li><li>**Kamery.** Používá se ke skenování kódů QR při přidávání pracovní, školní nebo neúčet Microsoft.</li><li>**Kontakty a telefon.** Aplikace vyžaduje toto oprávnění k hledání pracovních nebo školních účtů Microsoft na telefonu a jejich přidání do aplikace za vás.</li><li>**SMS.** Slouží k zajištění, že telefonní číslo odpovídá číslu záznamu při prvním přihlášení pomocí osobního účet Microsoft. Pošleme textovou zprávu na telefon, na který jste nainstalovali aplikaci, která obsahuje ověřovací kód 6-8 číslic. Nemusíte tento kód najít a zadat, protože ověřovatel ho v textové zprávě automaticky najde.</li><li>**Nakreslete přes jiné aplikace.** Oznámení, které ověřuje vaši identitu, se zobrazí také v jakékoli jiné spuštěné aplikaci.</li><li>**Příjem dat z Internetu.** Toto oprávnění je vyžadováno pro odesílání oznámení.</li><li>**Zabránit telefonu v režimu spánku.** Pokud zaregistrujete zařízení ve vaší organizaci, může vaše organizace tuto zásadu na telefonu změnit.</li><li>**Řízení vibrací.** Můžete zvolit, jestli chcete, aby se při každém přijetí oznámení k ověření vaší identity zobrazily vibrace.</li><li>**Použijte hardware otisku prstu.** Některé pracovní a školní účty vyžadují další kód PIN, kdykoli ověříte svoji identitu. Abychom to usnadnili, umožní vám místo zadávání kódu PIN používat otisk prstu.</li><li> **Zobrazit síťová připojení.** Když přidáte účet Microsoft, vyžaduje aplikace připojení k síti nebo Internetu.</li><li>**Přečtěte si obsah svého úložiště**. Toto oprávnění se používá jenom v případě, že budete Nahlásit technický problém prostřednictvím nastavení aplikace. K diagnostice tohoto problému se shromažďují nějaké informace z vašeho úložiště.</li><li>**Úplný přístup k síti** Toto oprávnění je vyžadováno k odesílání oznámení k ověření vaší identity.</li><li>**Spustit při spuštění.** Když restartujete telefon, toto oprávnění zajistí, že budete pokračovat v doručování oznámení, abyste ověřili svoji identitu.</li></ul>

### <a name="approve-requests-without-unlocking"></a>Schvalovat žádosti bez odemknutí

**Otázka**: Proč ověřovatel umožňuje schválit požadavek bez odemknutí zařízení?

Odpověď: nemusíte odemykat zařízení, abyste mohli schvalovat žádosti o ověření, protože stačí, abyste se rozhodli, že máte telefon s vámi. Dvoustupňové ověřování vyžaduje, abyste provedli prokázání dvou věcí, a to věcí, kterou znáte, a věcí, kterou máte. To, co znáte, je vaše heslo. Věc, kterou máte, je váš telefon (nastavte s ověřovatelem a zaregistrujte se jako ověření služby Multi-Factor Authentication). Proto má telefon a schválení žádosti splňovat kritéria pro druhý faktor ověřování.

### <a name="activity-notifications"></a>Oznámení o aktivitách

**Otázka**: Proč mi Získávám oznámení o aktivitě svého účtu?

Odpověď **: oznámení o** aktivitách se odesílají do ověřovatele okamžitě, kdykoli dojde ke změně vašich osobních účtů Microsoft, což vám pomůže zajistit vyšší zabezpečení. Tato oznámení jsme poslali dřív prostřednictvím e-mailu a SMS. Další informace o těchto oznámeních o aktivitách najdete v tématu [co se stane, když máte k vašemu účtu neobvyklé přihlášení](https://support.microsoft.com/help/13967/microsoft-account-unusual-sign-in). Pokud chcete změnit, kam dostávat oznámení, přihlaste se k webu, [kde vám můžeme kontaktovat stránku výstrahy](https://account.live.com/SecurityNotifications/Update) účtu, který není kritický.

### <a name="one-time-passcodes"></a>Jednorázová hesla

**Otázka**: moje jednorázová hesla nefungují. Co bych měl/a dělat?

Odpověď **: Ujistěte** se, že datum a čas v zařízení jsou správné a že jsou automaticky synchronizovány. Pokud je datum a čas chybné nebo není synchronizován, kód nebude fungovat.

### <a name="windows-10-mobile"></a>Windows 10 Mobile

**Otázka**: operační systém Windows 10 Mobile byl zastaralý v prosinci 2019. Budou Microsoft Authenticator v operačních systémech Windows Mobile i nadále zastaralé?

Odpověď **: ověřovací** data ve všech operačních systémech Windows Mobile nebudou podporována po 28. únoru 2020. Uživatelé nebudou mít nárok na příjem jakýchkoli nových aktualizací do aplikace, aby vyúčtovali uvedené datum. Až 28. února 2020 služby společnosti Microsoft, které aktuálně podporují ověřování pomocí Microsoft Authenticator ve všech operačních systémech Windows Mobile, začnou svoji podporu vyřadit. Aby bylo možné ověřovat služby společnosti Microsoft, důrazně doporučujeme všem našim uživatelům, aby před tímto datem přešli na alternativní mechanismus ověřování.

### <a name="default-mail-app"></a>Výchozí poštovní aplikace

**Otázka**: když se přihlašujete k pracovnímu nebo školnímu účtu pomocí výchozí poštovní aplikace, která je součástí iOS, zobrazí se výzva ověřovatel pro informace o ověření zabezpečení. Po zadání těchto informací a návratu do e-mailové aplikace se zobrazí chyba. Co mám udělat?

Odpověď: k tomuto problému **dochází, protože** vaše přihlašovací a poštovní aplikace se vyskytují ve dvou různých aplikacích, což způsobuje, že počáteční proces přihlašování na pozadí přestane fungovat a selže. Pokud se chcete pokusit tuto chybu opravit, doporučujeme při přihlášení k aplikaci pošty vybrat ikonu **Safari** na pravé dolní straně obrazovky. Přesunutím na Safari dojde k tomu, že se celý proces přihlašování stane v jedné aplikaci, takže se můžete úspěšně přihlásit k aplikaci.

### <a name="apple-watch-watchos-7"></a>Apple Watch watchOS 7

**Otázka**: Proč mám problémy s Apple Watch na watchOS 7?

Odpověď **: došlo** k potížím s schvalováním oznámení na watchOS 7 a pracujeme na tom, abychom to vyřešili. Mezitím by měla být všechna oznámení, která vyžadují aplikaci Microsoft Authenticator watchOS, schválená v telefonu.

### <a name="apple-watch-doesnt-show-accounts"></a>Apple Watch nezobrazuje účty

**Otázka**: Proč se při otevření ověřovacích dat na mém Apple Watch nezobrazí všechny moje účty?

Odpověď **: ověřovatel** podporuje jenom osobní nebo školní účty Microsoftu s nabízenými oznámeními v doprovodné aplikaci Apple Watch. U ostatních účtů, jako je Google nebo Facebook, musíte otevřít ověřovací aplikaci na telefonu, abyste viděli ověřovací kódy.

### <a name="apple-watch-notifications"></a>Oznámení Apple Watch

**Otázka**: Proč nelze schválit nebo zamítnout oznámení na mém Apple Watch?

Odpověď **: Nejdřív se ujistěte**, že jste upgradovali na 6.0.0 nebo novější verzi ověřovacího data na iPhonu. Potom otevřete Microsoft Authenticator doprovodné aplikace v Apple Watch a vyhledejte všechny účty s tlačítkem **nastavit** pod nimi. Dokončením procesu nastavení schválíte oznámení pro tyto účty.

### <a name="apple-watch-communication-error"></a>Apple Watch Chyba komunikace

**Otázka**: zobrazuje se chyba komunikace mezi Apple Watch a telefonem. Co můžu vyřešit?

Odpověď: k této chybě **dochází, když** se obrazovka kukátka přepne do režimu spánku, než dokončí komunikaci s vaším telefonem.<br><b>Pokud k chybě dojde při instalaci:</b><br>Zkuste znovu spustit instalační program a ujistěte se, že vaše kukátko zůstane v provozu, dokud se proces nedokončí. Ve stejnou chvíli otevřete aplikaci na telefonu a odpovíte na všechny zobrazené výzvy.<br>Pokud váš telefon a sledování stále nekomunikuje, můžete vyzkoušet následující akce:<ol><li>Vynutit ukončení aplikace Microsoft Authenticator Phone a znovu ji otevřete na iPhonu.</li><li>Vynutit ukončení doprovodné aplikace v Apple Watch.<ol><li> Otevřete v kukátku aplikaci Microsoft Authenticator doprovodné aplikace</li><li>Držte tlačítko na pravé straně, dokud se nezobrazí obrazovka pro **vypnutí** .</li><li>Uvolněte postranní tlačítko a podržením Digital Crown vynutíte ukončení aktivní aplikace.</li></ol></li><li>Vypněte Bluetooth i Wi-Fi pro váš telefon i vaše kukátko a pak je znovu zapněte.</li><li>Restartujte iPhone a vaše kukátko.</li></ol><b>Pokud k chybě dojde při pokusu o schválení oznámení:</b><br>Při dalším pokusu o schválení oznámení na vašem Apple Watch ponechte obrazovku spuštěnou, dokud není žádost dokončena a uslyšíte zvuk, který indikuje, že byl úspěšný.

### <a name="apple-watch-companion-app-not-syncing"></a>Apple Watch doprovodné aplikace se nesynchronizují.

**Otázka**: Proč se nezobrazují aplikace Microsoft Authenticator companion pro Apple Watch synchronizaci nebo zobrazení na mém kukátku?

Odpověď **: Pokud** se aplikace v kukátku nezobrazuje, zkuste provést následující akce: <ol><li>Ujistěte se, že vaše kukátko běží watchOS 4,0 nebo vyšší.</li><li>Znovu synchronizujte kukátko.</li></ol>

### <a name="apple-watch-companion-app-crashed"></a>Došlo k chybě doprovodné aplikace Apple Watch.

**Otázka**: došlo k chybě doprovodné aplikace My Apple Watch. Můžu vám poslat svoje protokoly o chybách, abyste je mohli prozkoumat?

Odpověď **: nejprve** se musíte ujistit, že jste zvolili sdílení vašich analýz s námi. Pokud jste uživatelem testovacího prostředí, už jste se zaregistrovali. V opačném případě můžete přejít na **nastavení > ochrany osobních údajů > Analytics** a vybrat možnost **sdílet iPhone & sledovat analýzy** a **sdílet s možnostmi vývojářů aplikací** .<br>Po registraci se můžete pokusit reprodukování chyby, aby byly protokoly chyb automaticky odesílány do nás pro účely šetření. Pokud ale nemůžete reprodukování svého selhání, můžete soubory protokolu ručně zkopírovat a odeslat je do nás.<ol><li>Otevřete aplikaci Watch na telefonu, přejděte na **nastavení > obecné** a potom klikněte na **Kopírovat sledovací analýzu**.</li><li>Najděte odpovídající chybu v části **nastavení > ochrany osobních údajů > analytics > analytické údaje** a pak ručně zkopírujte celý text.</li><li>Otevřete ověřovací data na telefonu a vložte zkopírovaný text do pole  **Popis problému, který máte** v souladu s tím, jak **máte potíže?** na stránce pro  **odeslání zpětné vazby** . </li></ol>

## <a name="autofill-with-authenticator"></a>Automatické vyplňování pomocí ověřovatele

**Otázka**: co je automatické vyplňování pomocí ověřovatele?

Odpověď **: aplikace** ověřovatele teď bezpečně ukládá a vyplní hesla pro aplikace a weby, které navštívíte na telefonu. Automatické vyplňování můžete použít k synchronizaci a automatickému vyplňování hesel na zařízeních s iOS a Androidem. Po nastavení aplikace ověřovatele jako poskytovatele automatického vyplňování na telefonu nabízí možnost uložit hesla, když je zadáte na webu nebo na přihlašovací stránce aplikace. Hesla se ukládají jako součást [vašich osobních účet Microsoft](https://account.microsoft.com/account) a jsou k dispozici i při přihlášení k Microsoft Edge pomocí vašich osobních účet Microsoft.

**Otázka**: Jaké informace může ověřovatel automaticky vyplnit?

Odpověď **: ověřovací** data umožňují automatické vyplňování uživatelských jmen a hesel na webech a v aplikacích, které navštívíte na telefonu.

**Otázka**: návody zapnout automatické vyplňování hesla v telefonu?

Odpověď **: proveďte následující kroky:**

1. Otevřete ověřovací aplikaci.
1. Na kartě **hesla** v okně ověřovatel vyberte **Přihlásit se účtem Microsoft** a přihlaste se pomocí [účet Microsoft](https://account.microsoft.com/account). Tato funkce aktuálně podporuje jenom účty Microsoft, a zatím nepodporuje pracovní nebo školní účty.

**Otázka**: návody nastavit ověřovatele jako výchozího zprostředkovatele automatického vyplňování na telefonu?

Odpověď **: proveďte následující kroky:**

1. Otevřete ověřovací aplikaci.
1. Na kartě **hesla** v aplikaci vyberte **Přihlásit se účtem Microsoft** a přihlaste se pomocí [účet Microsoft](https://account.microsoft.com/account).
1. Proveďte některou z následujících akcí:

   - V systému iOS v části **Nastavení** vyberte **zapnout automatické vyplňování** v části nastavení automatického vyplňování, kde se dozvíte, jak nastavit ověřovací data jako výchozího poskytovatele automatického vyplňování.
   - V Androidu v části **Nastavení** vyberte v části nastavení automatického vyplňování možnost **nastavit jako zprostředkovatele automatického vyplňování** .

**Otázka**: Co když **Automatické vyplňování** není pro mě v nastavení k dispozici?

Odpověď **: Pokud** automatické vyplňování není pro vás v ověřovateli k dispozici, může to být způsobeno tím, že pro vaši organizaci nebo typ účtu ještě není povolené automatické vyplňování. Tuto funkci můžete použít na zařízení, kam nepřidáte pracovní nebo školní účet. Další informace o tom, jak pro vaši organizaci zapnout automatické vyplňování, najdete v tématu [Automatické vyplňování pro správce IT](#autofill-for-it-admins).

**Otázka**: návody zastavit synchronizaci hesel?

Odpověď **: Chcete-li** zastavit synchronizaci hesel v ověřovací aplikaci, otevřete **Nastavení**  >  **automatického vyplňování** nastavení  >  **účet synchronizace**. Na další obrazovce můžete vybrat možnost **zastavit synchronizaci a odebrat všechna data automatického vyplňování**. Tím se ze zařízení odeberou hesla a další data automatického vyplňování. Odebrání automatického vyplňování dat nemá vliv na službu Multi-Factor Authentication.

**Otázka**: jak jsou hesla chráněná ověřovací aplikací?

Odpověď **: aplikace** ověřovatele již poskytuje vysokou úroveň zabezpečení pro službu Multi-Factor Authentication a správu účtů a stejný vysoký pruh zabezpečení je také rozšířen o správu hesel.

- **Ověřovací aplikace vyžaduje silné ověřování**: přihlášení k ověřovateli vyžaduje druhý faktor. To znamená, že hesla uvnitř ověřovací aplikace jsou chráněna i v případě, že někdo má vaše účet Microsoft heslo.
- **Data automatického vyplnění jsou chráněná pomocí biometrika a** hesla: než budete moct zadat automatické vyplňování hesla pro aplikaci nebo web, vyžaduje ověřovatel biometriku nebo heslo zařízení. To vám pomůže zvýšit zabezpečení, takže i když někdo jiný má přístup k vašemu zařízení, nemůže vyplnit ani zobrazit vaše heslo, protože nedokáže zadat kód PIN biometriky nebo zařízení. Uživatel také nemůže otevřít stránku hesla, pokud nezadává biometrické nebo PIN, a to ani v případě, že v nastavení aplikace vypne zámek aplikace.
- **Šifrovaná hesla na zařízení**: hesla na zařízení jsou šifrovaná a šifrovací a dešifrovací klíče se nikdy neukládají a v případě potřeby se vždy generují. Hesla se dešifrují jenom v případě, že uživatel chce, to znamená při automatickém vyplňování nebo když chce, aby si uživatel heslo zobrazil, a to jak pro to je potřeba biometriku nebo PIN.
- **Zabezpečení cloudu a sítě**: vaše hesla na cloudu se šifrují a dešifrují jenom v případě, že se k vašemu zařízení dostanou. Hesla se synchronizují přes připojení HTTPS s chráněným protokolem SSL, které pomáhá zabránit útočníkovi v odposlouchávání citlivých dat při synchronizaci. Také zajišťujeme, abychom správnosti data synchronizovaná přes síť s použitím kryptografických funkcí hash (konkrétně kód pro ověřování zpráv založených na algoritmu hash).

## <a name="autofill-for-it-admins"></a>Automatické vyplňování pro správce IT

**Otázka**: budou se zaměstnanci nebo studenti při použití automatické vyplňování hesel v ověřovací aplikaci používat?

Odpověď **: Ano**, automatické vyplňování [osobních účtů Microsoft](https://go.microsoft.com/fwlink/?linkid=2144423) teď funguje pro většinu uživatelů v podniku i v případě, že se do ověřovací aplikace přidá pracovní nebo školní účet. Můžete vyplnit formulář a povolit nebo odepřít automatické vyplňování vaší organizace a [Odeslat ho ověřovacímu týmu](https://aka.ms/ConfigureAutofillInAuthenticator). Automatické vyplňování není aktuálně k dispozici pro pracovní nebo školní účty.

**Otázka**: budou automaticky synchronizována hesla "pracovní nebo školní účet uživatele"?

Odpověď **: ne**. Automatické vyplňování hesla nebude synchronizovat heslo pracovního nebo školního účtu pro vaše uživatele. Když uživatel navštíví web nebo aplikaci, bude přihlašovací data nabízet možnost Uložit heslo pro daný web nebo aplikaci a heslo bude uloženo pouze v případě, že uživatel zvolí možnost.
  
**Otázka**: Můžu pro automatické vyplňování povolených jenom někteří uživatelé mojí organizace?

Odpověď **: ne**. Podniky můžou v tuto chvíli povolit automatické vyplňování hesel jenom pro všechny zaměstnance nebo žádné z nich.

**Otázka**: co dělat v případě, že má můj zaměstnanec nebo student více pracovních nebo školních účtů? Například můj zaměstnanec má v Microsoft Authenticator účty z více podniků nebo škol.

**O**: všechny podniky nebo školy přidané v ověřovací aplikaci je třeba umožnit, aby se v ověřovacím poli pro vlastníka aplikace, který má být možné použít, vyplnili. Jedinou výjimkou z tohoto omezení je, že zaměstnanec nebo student přidá svůj pracovní nebo školní účet do služby Multi-Factor Authentication založeného na cloudu Microsoftu jako [externí účet nebo účet třetí strany](user-help-auth-app-add-non-ms-account.md).

## <a name="next-steps"></a>Další kroky

- Pokud máte potíže s získáním ověřovacího kódu pro osobní účet Microsoft, přečtěte si část **problémy s ověřovacím kódem pro řešení potíží** v článku [účet Microsoft informace o zabezpečení & ověřovací kódy](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) .

- Pokud chcete získat další informace o dvoustupňovém ověřování, přečtěte si téma [Nastavení mého účtu pro dvoustupňové ověřování](multi-factor-authentication-end-user-first-time.md) .

- Pokud chcete získat další informace o zabezpečení, přečtěte si téma [Přehled informací o zabezpečení (Preview)](./security-info-setup-signin.md) .

- Pokud vám vaše otázka neodpověděla, chceme vás od vás. Pokud chcete odeslat svůj dotaz a získat pomoc od komunity nebo si na této stránce ponechte komentář, navštivte [fórum Microsoft Authenticator aplikace](https://social.technet.microsoft.com/Forums/en-us/home?forum=MicrosoftAuthenticatorApp) .

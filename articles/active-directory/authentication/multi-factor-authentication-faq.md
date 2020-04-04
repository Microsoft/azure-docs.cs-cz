---
title: Nejčastější dotazy k vícefaktorovému ověřování Azure – Azure Active Directory
description: Nejčastější dotazy a odpovědi související s azure multifaktorovým ověřováním.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/18/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 186e5a533bac5d7adac8b0423eff6c05f797c56f
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652128"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Nejčastější dotazy ohledně služby Azure Multi-Factor Authentication

Tyto nejčastější dotazy odpovídají na běžné otázky týkající se vícefaktorového ověřování Azure a používání služby vícefaktorového ověřování. Je rozdělena do otázek týkajících se služby obecně, fakturačních modelů, uživatelských prostředí a řešení potíží.

## <a name="general"></a>Obecné

> [!IMPORTANT]
> července 2019 již společnost Microsoft nebude nabízet server MFA pro nová nasazení. Noví zákazníci, kteří by chtěli od svých uživatelů vyžadovat vícefaktorové ověřování, by měli používat vícefaktorové ověřování Azure na základě cloudu. Stávající zákazníci, kteří aktivovali server MFA před 1.
> 
> Září 2018 již nejsou pro nové zákazníky k dispozici licencování na základě spotřeby.
> září 2018 již nemusí být vytvářeny nové poskytovatele auth. Stávající zprostředkovatelé auth mohou být nadále používány a aktualizovány. Vícefaktorové ověřování bude i nadále dostupnou funkcí v licencích Azure AD Premium.

> [!NOTE]
> Informace sdílené níže týkající se serveru Azure Multi-Factor Authentication Server se vztahují pouze na uživatele, kteří už mají spuštěný server MFA.

**Otázka: Jak server Azure pro vícefaktorové ověřování zpracovává uživatelská data?**

U serveru s vícefaktorovým ověřováním jsou uživatelská data uložena pouze na místních serverech. V cloudu se neukládají žádná trvalá data. Když uživatel provede dvoustupňové ověření, vícefaktorový ověřovací server odešle data do cloudové služby Azure Multi-Factor Authentication pro ověřování. Komunikace mezi vícefaktorovým ověřovacím serverem a cloudovou službou vícefaktorového ověřování používá zabezpečenou vrstvu soketů (SSL) nebo zabezpečení transportní vrstvy (TLS) přes odchozí port 443.

Při odeslání požadavků na ověření do cloudové služby jsou shromažďována data pro zprávy o ověřování a využití. Datová pole zahrnutá do dvoustupňových ověřovacích protokolů jsou následující:

* **Jedinečné ID** (uživatelské jméno nebo místní ID vícefaktorového ověřovacího serveru)
* **Jméno a příjmení** (volitelné)
* **E-mailová adresa** (nepovinné)
* **Telefonní číslo** (při použití hlasového hovoru nebo ověřování POMOCÍ SMS)
* **Token zařízení** (při použití ověřování v mobilní aplikaci)
* **Režim ověřování**
* **Výsledek ověřování**
* **Název vícefaktorového ověřovacího serveru**
* **Vícefaktorový ověřovací server IP**
* **IP klienta** (je-li k dispozici)

Volitelná pole lze konfigurovat na vícefaktorovém ověřovacím serveru.

Výsledek ověření (úspěch nebo odmítnutí) a důvod, pokud byl odepřen, je uložen spolu s ověřovacími daty. Tato data jsou k dispozici v sestavách ověřování a používání.

**Otázka: Jaké SMS krátké kódy se používají pro odesílání SMS zpráv mým uživatelům?**

Ve Spojených státech používá společnost Microsoft následující krátké kódy SMS:

   * 97671
   * 69829
   * 51789
   * 99399

V Kanadě společnost Microsoft používá následující sms krátké kódy:

   * 759731 
   * 673801

Společnost Microsoft nezaručuje konzistentní sms nebo hlasové vícefaktorové ověřování rychlé doručení stejným číslem. V zájmu našich uživatelů může společnost Microsoft kdykoli přidat nebo odebrat krátké kódy, protože provedeme úpravy trasy za účelem zlepšení doručování SMS. Společnost Microsoft nepodporuje krátké kódy pro země nebo oblasti kromě Spojených států a Kanady.

## <a name="billing"></a>Fakturace

Na většinu otázek s fakturací lze odpovědět odkazem na [stránku S vícefaktorovým ověřováním](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) nebo dokumentaci o [tom, jak získat azure multifaktorové ověřování](concept-mfa-licensing.md).

**Otázka: Účtuje se mé organizaci za odesílání telefonních hovorů a textových zpráv, které se používají k ověřování?**

Ne, neúčtují se vám poplatky za jednotlivé telefonní hovory nebo textové zprávy odeslané uživatelům prostřednictvím azure multifaktorového ověřování. Pokud používáte zprostředkovatele mfa ověřování pro ověřování, bude se vám účtovat za každé ověřování, ale ne za použitou metodu.

Vašim uživatelům mohou být účtovány telefonní hovory nebo textové zprávy, které obdrží, podle jejich osobní telefonní služby.

**Otázka: Účtuje mi model fakturace pro jednotlivé uživatele za všechny povolené uživatele nebo jen za ty, kteří provedli dvoustupňové ověření?**

Fakturace je založena na počtu uživatelů nakonfigurovaných pro použití vícefaktorového ověřování bez ohledu na to, zda provedli dvoustupňové ověření v tomto měsíci.

**Otázka: Jak funguje fakturace s vícefaktorovým ověřováním?**

Když vytvoříte poskytovatele vícefaktorové mfa pro jednotlivé uživatele nebo ověřování, bude se předplatné Azure vaší organizace účtovat měsíčně na základě využití. Tento model fakturace je podobný tomu, jak Azure účtuje za využití virtuálních počítačů a webů.

Když si zakoupíte předplatné azure multi-factor authentication, vaše organizace platí pouze roční licenční poplatek pro každého uživatele. Tímto způsobem se účtují licence MFA a balíčky Office 365, Azure AD Premium nebo Enterprise Mobility + Security. 

Další informace o možnostech najdete v [části Jak získat Azure Multi-Factor Authentication](concept-mfa-licensing.md).

**Otázka: Existuje bezplatná verze Azure Multi-Factor Authentication?**

V některých případech ano.

Vícefaktorové ověřování pro správce Azure nabízí podmnožinu funkcí Azure MFA zdarma pro přístup k online službám Microsoftu, včetně [portálu Azure Portal](https://portal.azure.com) a [Centra pro správu Microsoftu 365](https://admin.microsoft.com). Tato nabídka platí jenom pro globální správce v instancích Služby Azure Active Directory, kteří nemají plnou verzi Azure MFA prostřednictvím licence MFA, balíčku nebo samostatného poskytovatele založeného na spotřebě. Pokud vaši správci používají bezplatnou verzi a pak si koupíte plnou verzi Azure MFA, pak všichni globální správci jsou povýšeni na placenou verzi automaticky.

Vícefaktorové ověřování pro uživatele Office 365 nabízí podmnožinu funkcí Azure MFA zdarma pro přístup ke službám Office 365, včetně Exchange Online a SharePointu Online. Tato nabídka platí pro uživatele, kteří mají přiřazenou licenci Office 365, když odpovídající instance služby Azure Active Directory nemá plnou verzi Azure MFA prostřednictvím licence MFA, balíčku nebo samostatného poskytovatele založeného na spotřebě.

**Otázka: Může moje organizace kdykoli přepínat mezi modely fakturace spotřeby na uživatele a ověřování?**

Pokud vaše organizace nakupuje vícefaktorové informace jako samostatnou službu s fakturací založenou na spotřebě, zvolíte při vytváření zprostředkovatele mfa model fakturace. Po vytvoření zprostředkovatele mfa nelze změnit fakturační model. 

Pokud váš poskytovatel MFA *není* propojen s tenantem Azure AD nebo propojíte nového zprostředkovatele MFA s jiným klientem Azure AD, nepřenesou se uživatelská nastavení a možnosti konfigurace. Je také potřeba znovu aktivovat stávající Azure MFA Servery pomocí přihlašovacích údajů pro aktivaci vygenerovaných prostřednictvím nového poskytovatele MFA. Opětovná aktivace MFA Serverů za účelem jejich propojení s novým poskytovatelem MFA nemá vliv na ověřování pomocí telefonního hovoru a textové zprávy, ale do opětovné aktivace mobilní aplikace přestanou pro všechny uživatele fungovat oznámení mobilní aplikace.

Další informace o zprostředkovatelích MFA najdete v [aplikaci Začínáme s poskytovatelem Azure Multi-Factor Auth .](concept-mfa-authprovider.md)

**Otázka: Může moje organizace kdykoli přepínat mezi fakturací založenou na spotřebě a předplatnými (model založený na licenci)?**

V některých případech ano.

Pokud má váš adresář poskytovatele azure multi-factor authentication *pro jednotlivé uživatele,* můžete přidat licence MFA. Uživatelé s licencemi se do fakturace založené na spotřebě na jednotlivé uživatele nezapočítávají. Uživatelé bez licencí lze stále povolit pro vícefaktorové povolení prostřednictvím zprostředkovatele mfa. Pokud zakoupíte a přiřadíte licence všem uživatelům nakonfigurovaným pro použití vícefaktorového ověřování, můžete odstranit zprostředkovatele azure multi-factor authentication. Pokud máte v budoucnu více uživatelů než licencí, můžete vždy vytvořit jiného poskytovatele vícefaktorové ověřování pro jednotlivé uživatele.

Pokud váš adresář má zprostředkovatele azure multi-factor authentication pro ověřování podle *ověřování,* bude se vám vždy účtovat za každé ověřování, pokud je poskytovatel MFA propojen s vaším předplatným. Licence MFA můžete přiřadit uživatelům, ale bude se vám stále účtovat každý dvoufázový požadavek na ověření, ať už pochází od někoho, kdo má přiřazenou licenci MFA.

**Otázka: Musí moje organizace používat a synchronizovat identity, aby mohla používat azure multifaktorové ověřování?**

Pokud vaše organizace používá fakturační model založený na spotřebě, služba Azure Active Directory je volitelná, ale není povinná. Pokud váš poskytovatel MFA není propojený s klientem Azure AD, můžete nasadit pouze server Azure s vícefaktorovým ověřováním místně.

Azure Active Directory je vyžadována pro licenční model, protože licence se přidávají do klienta Azure AD při nákupu a přiřadíje je uživatelům v adresáři.

## <a name="manage-and-support-user-accounts"></a>Správa a podpora uživatelských účtů

**Otázka: Co mám uživatelům říct, aby udělali, když v telefonu neobdrží odpověď?**

Nechte své uživatele, aby se pokusili až 5krát za 5 minut získat telefonní hovor nebo SMS pro ověření. Společnost Microsoft používá pro doručování volání a ZPRÁV SMS více poskytovatelů. Pokud to nepomůže, otevřete případ podpory u společnosti Microsoft k dalšímu řešení potíží.

Pokud výše uvedené kroky nefungují, doufejme, že všichni vaši uživatelé nakonfigurovali více než jednu metodu ověření. Řekněte jim, aby se zkusili znovu přihlásit, ale na přihlašovací stránce vybrali jinou metodu ověření.

Uživatele můžete nasměrovat na [příručku pro řešení potíží s koncovým uživatelem](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

**Otázka: Co mám dělat, když se jeden z mých uživatelů nemůže dostat ke svému účtu?**

Účet uživatele můžete obnovit tak, že jej znovu provedete procesem registrace. Přečtěte si další informace o [správě nastavení uživatelů a zařízení pomocí Azure Multi-Factor Authentication v cloudu](howto-mfa-userdevicesettings.md).

**Otázka: Co mám dělat, když jeden z mých uživatelů ztratí telefon, který používá hesla aplikací?**

Chcete-li zabránit neoprávněnému přístupu, odstraňte všechna hesla aplikací uživatele. Poté, co má uživatel náhradní zařízení, může znovu vytvořit hesla. Přečtěte si další informace o [správě nastavení uživatelů a zařízení pomocí Azure Multi-Factor Authentication v cloudu](howto-mfa-userdevicesettings.md).

**Otázka: Co když se uživatel nemůže přihlásit k aplikacím, které nejsou v prohlížeči?**

Pokud vaše organizace stále používá starší klienty a povolili jste [používání hesel aplikací](howto-mfa-mfasettings.md#app-passwords), uživatelé se k těmto starším klientům nemohou přihlásit pomocí jejich uživatelského jména a hesla. Místo toho je třeba [nastavit hesla aplikací](../user-help/multi-factor-authentication-end-user-app-passwords.md). Uživatelé musí vymazat (odstranit) přihlašovací údaje, restartovat aplikaci a pak se přihlásit pomocí svého uživatelského jména a *hesla aplikace* namísto svého běžného hesla.

Pokud vaše organizace nemá starší klienty, neměli byste uživatelům povolit vytváření hesel aplikací.

> [!NOTE]
> Moderní ověřování pro klienty Office 2013
>
> Hesla aplikací jsou nezbytná jenom pro aplikace, které nepodporují moderní ověřování. Klienti Office 2013 podporují moderní ověřovací protokoly, ale je potřeba je nakonfigurovat. Nyní je moderní ověřování dostupné všem zákazníkům, kteří scházejí aktualizaci pro Office 2013 z března 2015 nebo novější. Další informace najdete v příspěvku blogu [Aktualizované moderní ověřování Office 365](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/).

**Otázka: Moji uživatelé říkají, že někdy neobdrží textovou zprávu nebo časový čas ověření.**

Doručení SMS zpráv není zaručeno, protože existují nekontrolovatelné faktory, které mohou ovlivnit spolehlivost služby. Mezi tyto faktory patří cílová země nebo oblast, operátor mobilního telefonu a síla signálu.

Pokud mají uživatelé často problémy se spolehlivým přijímáním textových zpráv, řekněte jim, aby místo toho používali mobilní aplikaci nebo metodu telefonního hovoru. Mobilní aplikace může přijímat oznámení jak přes mobilní připojení, tak wi-fi připojení. Kromě toho může mobilní aplikace generovat ověřovací kódy, i když zařízení nemá vůbec žádný signál. Aplikace Microsoft Authenticator je k dispozici pro [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [IOS](https://go.microsoft.com/fwlink/?Linkid=825073)a [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).

**Otázka: Mohu změnit dobu, po kterou mají uživatelé zadat ověřovací kód z textové zprávy před časovým odesízněním systému?**

V některých případech ano. 

Pro jednosměrnou SMS s Azure MFA Server v7.0 nebo vyšší, můžete nakonfigurovat nastavení časového limitu nastavením klíče registru. Po mrakové službě MFA odešle textovou zprávu, ověřovací kód (nebo jednorázový přístupový kód) je vrácena na server MFA. Server MFA ukládá kód do paměti po dobu 300 sekund ve výchozím nastavení. Pokud uživatel nezadá kód před uplynutím 300 sekund, je jeho ověřování odmítnuto. Pomocí těchto kroků můžete změnit výchozí nastavení časového limitu:

1. Přejděte na HKLM\Software\Wow6432Node\Pozitivní sítě\PhoneFactor.
2. Vytvořte klíč registru DWORD s názvem **pfsvc_pendingSmsTimeoutSeconds** a nastavte čas v sekundách, který chcete, aby server Azure MFA ukládal jednorázové přístupové kódy.

>[!TIP] 
>Pokud máte více serverů MFA, pouze ten, který zpracoval původní požadavek na ověření, zná ověřovací kód, který byl odeslán uživateli. Když uživatel zadá kód, požadavek na ověření ověření musí být odeslán na stejný server. Pokud je ověření kódu odesláno na jiný server, je ověření odmítnuto. 

Pokud uživatelé nereagují na SMS v rámci definovaného časového času, jejich ověřování je odepřeno. 

Pro jednosměrnou SMS s Azure MFA v cloudu (včetně adaptéru služby AD FS nebo rozšíření serveru síťových zásad) nelze nakonfigurovat nastavení časového času. Azure AD ukládá ověřovací kód po dobu 180 sekund. 

**Otázka: Můžu používat hardwarové tokeny s Vícefaktorovým ověřovacím serverem Azure?**

Pokud používáte Azure Multi-Factor Authentication Server, můžete importovat tokeny založené na čase a jednorázovém heslu (TOTP) od jiných výrobců a pak je použít pro dvoustupňové ověření.

Tokeny ActiveIdentity, které jsou tokeny OATH TOTP, můžete použít, pokud vložíte tajný klíč do souboru CSV a importujete na server Azure Multi-Factor Authentication Server. Tokeny OATH můžete používat s ověřováním pomocí formulářů ADFS (ADFS) služby ADFS, internetového informačního serveru (IIS) a služby RADIUS (Remote Authentication Dial-In User Service), pokud klientský systém může přijmout vstup uživatele.

Tokeny OATH TOTP třetích stran můžete importovat v následujících formátech:  

- Přenosný kontejner symetrického klíče (PSKC)  
- CSV, pokud soubor obsahuje sériové číslo, tajný klíč ve formátu Base 32 a časový interval  

**Otázka: Můžu k zabezpečení Terminálové služby použít server Azure S vícefaktorovým ověřováním?**

Ano, ale pokud používáte Windows Server 2012 R2 nebo novější, můžete terminálovou službu zabezpečit jenom pomocí služby Brána vzdálené plochy (Brána VP).

Změny zabezpečení v systému Windows Server 2012 R2 změnily způsob připojení serveru Azure Multi-Factor Authentication Server k balíčku zabezpečení místního úřadu zabezpečení (LSA) v systému Windows Server 2012 a starších verzích. U verzí Terminálové služby v systému Windows Server 2012 nebo starších můžete [zabezpečit aplikaci pomocí ověřování systému Windows](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Pokud používáte Windows Server 2012 R2, potřebujete bránu VP.

**Otázka: Na serveru MFA byla nakonfigurována ID volajícího, ale uživatelé stále přijímají vícefaktorová volání ověřování od anonymního volajícího.**

Když jsou vícefaktorová volání ověřování umístěna prostřednictvím veřejné telefonní sítě, někdy jsou směrována prostřednictvím operátora, který nepodporuje ID volajícího. Z tohoto důvodu není zaručeno ID volajícího, i když systém vícefaktorového ověřování vždy odešle.

**Otázka: Proč jsou moji uživatelé vyzváni k registraci svých bezpečnostních údajů?**
Existuje několik důvodů, proč by uživatelé mohli být vyzváni k registraci svých bezpečnostních údajů:

- Uživatel byl povolen pro Vícefaktorové ověřování jejich správce ve službě Azure AD, ale ještě nemá informace o zabezpečení registrované pro svůj účet.
- Uživatel byl povolen pro samoobslužné resetování hesla ve službě Azure AD. Bezpečnostní informace jim pomohou obnovit heslo v budoucnu, pokud na něj někdy zapomenou.
- Uživatel přistupoval k aplikaci, která má zásady podmíněného přístupu vyžadovat vícefaktorové ověřování a nebyl a dosud registrovánpro vícefaktorové ověřování.
- Uživatel registruje zařízení pomocí Azure AD (včetně připojení Azure AD) a vaše organizace vyžaduje vícefaktorové ověřování pro registraci zařízení, ale uživatel se dříve nezaregistroval pro vícefaktorové ověřování.
- Uživatel generuje Windows Hello pro firmy v systému Windows 10 (který vyžaduje vícefaktorové ověřování) a ještě nebyl registrován pro vícefaktorové ověřování.
- Organizace vytvořila a povolila zásadu registrace vícefaktorové ověřování, která byla použita pro uživatele.
- Uživatel dříve zaregistroval vícefaktorové ověřování, ale zvolil metodu ověření, kterou od té doby zakázal správce. Uživatel proto musí znovu projít registraci vícefaktorové ověřování a vybrat novou výchozí metodu ověření.

## <a name="errors"></a>chyby

**Otázka: Co by měli uživatelé dělat, když se při používání oznámení mobilní aplikace zobrazí chybová zpráva "Žádost o ověření není pro aktivovaný účet"?**

Řekněte jim, aby podle tohoto postupu odebrali svůj účet z mobilní aplikace a znovu ho přidali:

1. Přejděte na [profil na webu Azure a](https://account.activedirectory.windowsazure.com/profile/) přihlaste se pomocí svého účtu organizace.
2. Vyberte **další ověření zabezpečení**.
3. Odeberte existující účet z mobilní aplikace.
4. Klikněte na **Konfigurovat**a podle pokynů překonfigurujte mobilní aplikaci.

**Otázka: Co by měli uživatelé dělat, když se jim při přihlašování k aplikaci, která není v prohlížeči, zobrazí chybová zpráva 0x800434D4L?**

Chyba 0x800434D4L nastane při pokusu o přihlášení k aplikaci, která není v prohlížeči, nainstalované v místním počítači, která nefunguje s účty, které vyžadují dvoustupňové ověření.

Řešení této chyby je mít samostatné uživatelské účty pro operace související s správcem a bez správce. Později můžete propojit poštovní schránky mezi účtem správce a účtem, který není správcem, abyste se mohli přihlásit k Outlooku pomocí účtu, který není správcem. Další podrobnosti o tomto řešení naleznete v příručce správce [k otevření a zobrazení obsahu poštovní schránky uživatele](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Další kroky

Pokud vaše otázka není zodpovězena zde, nechte ji v komentářích v dolní části stránky. Nebo zde jsou některé další možnosti pro získání nápovědy:

* Vyhledejte řešení běžných technických problémů ve [znalostní bázi Microsoft Support Knowledge Base.](https://support.microsoft.com)
* Vyhledávejte a procházejte technické otázky a odpovědi z komunity nebo si položte vlastní otázku ve [fórech Služby Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* Pokud jste starší zákazník PhoneFactor a máte dotazy nebo potřebujete pomoc resetovat heslo, otevřete případ podpory pomocí odkazu [pro resetování hesla.](mailto:phonefactorsupport@microsoft.com)
* Obraťte se na pracovníka podpory prostřednictvím [podpory serveru Azure Multi-Factor Authentication Server (PhoneFactor).](https://support.microsoft.com/oas/default.aspx?prid=14947) Když nás kontaktujete, je užitečné, pokud můžete uvést co nejvíce informací o vašem problému. Informace, které můžete zadat, zahrnují stránku, na které jste viděli chybu, konkrétní kód chyby, konkrétní ID relace a ID uživatele, který chybu viděl.

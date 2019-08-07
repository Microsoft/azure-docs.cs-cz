---
title: Nejčastější dotazy k Azure Multi-Factor Authentication – Azure Active Directory
description: Nejčastější dotazy a odpovědi související s Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46abe367c9047616174a1e43dffd57861e6278e8
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68811828"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Nejčastější dotazy k Azure Multi-Factor Authentication

Tato Nejčastější dotazy vám kladou odpovědi na nejčastější dotazy k Azure Multi-Factor Authentication a používání služby Multi-Factor Authentication. Je rozdělené na dotazy týkající se služby obecně, fakturačních modelů, uživatelských zkušeností a řešení potíží.

## <a name="general"></a>Obecné

**Otázka: Jak Azure Multi-Factor Authentication Server zpracovává data uživatelů?**

Pomocí aplikace Multi-Factor Authentication Server se uživatelská data ukládají pouze na místní servery. V cloudu se neukládají žádná trvalá data. Když uživatel provede dvoustupňové ověřování, aplikace Multi-Factor Authentication Server pošle data do cloudové služby Azure Multi-Factor Authentication pro ověřování. Komunikace mezi serverem Multi-Factor Authentication Server a cloudovou službou Multi-Factor Authentication používá SSL (Secure Sockets Layer) (SSL) nebo TLS (Transport Layer Security) prostřednictvím odchozího portu 443.

Když se do cloudové služby odesílají žádosti o ověření, shromažďují se data pro ověřování a sestavy o využití. Datová pole zahrnutá ve dvou krocích ověřování protokoly jsou následující:

* **Jedinečné ID** (uživatelské jméno nebo místní ID serveru Multi-Factor Authentication Server)
* **Křestní jméno a příjmení** volitelné
* **E-mailová adresa** volitelné
* **Telefonní číslo** (při použití hlasového hovoru nebo ověřování SMS)
* **Token zařízení** (při použití ověřování mobilní aplikace)
* **Režim ověřování**
* **Výsledek ověřování**
* **Název Multi-Factor Authentication serveru**
* **IP adresa Multi-Factor Authentication serveru**
* **IP adresa klienta** (Pokud je k dispozici)

Volitelná pole lze nakonfigurovat na serveru Multi-Factor Authentication Server.

Výsledek ověření (úspěch nebo odmítnutí) a důvod zamítnutí je uložen s ověřovacími daty. Tato data jsou k dispozici v sestavách ověřování a využití.

**Otázka: Které krátké kódy SMS slouží k posílání zpráv SMS uživatelům?**

V USA Microsoft používá následující krátké kódy SMS:

   * 97671
   * 69829
   * 51789
   * 99399

V Kanadě Microsoft používá následující krátké kódy SMS:

   * 759731 
   * 673801

Společnost Microsoft nezaručuje konzistentní doručování výzev služby Multi-Factor Authentication pro službu SMS nebo hlasové ověřování stejným číslem. V zájmu našich uživatelů může společnost Microsoft kdykoli přidat nebo odebrat krátké kódy, protože provádíme úpravy postupů pro zlepšení poskytování služby SMS. Společnost Microsoft nepodporuje krátké kódy pro země a oblasti kromě USA a Kanady.

## <a name="billing"></a>Fakturace

Na většinu otázek fakturace se dá odpovědět tak, že odkazujete na [stránku s cenami Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) nebo v dokumentaci, [Jak získat Azure Multi-Factor Authentication](concept-mfa-licensing.md).

**Otázka: Účtuje se mi moje organizace za odesílání telefonních hovorů a textových zpráv používaných k ověřování?**

Ne, neúčtují se poplatky za jednotlivé telefonní hovory nebo textové zprávy odesílané uživatelům prostřednictvím Azure Multi-Factor Authentication. Pokud použijete poskytovatele MFA pro ověřování, bude se vám účtovat každé ověřování, ale ne pro použitou metodu.

Vašim uživatelům se můžou účtovat telefonní hovory nebo textové zprávy, které obdrží, podle svých osobních telefonních služeb.

**Otázka: Účtuje se model fakturace podle uživatele pro všechny povolené uživatele nebo jenom ty, které provedly dvoustupňové ověřování?**

Fakturace vychází z počtu uživatelů, kteří mají nakonfigurovanou službu Multi-Factor Authentication, bez ohledu na to, jestli provedli dvoustupňové ověřování za měsíc.

**Otázka: Jak funguje fakturace Multi-Factor Authentication?**

Když vytvoříte poskytovatele MFA podle uživatele nebo ověřování, předplatné Azure vaší organizace se fakturuje měsíčně na základě využití. Tento model fakturace se podobá tomu, jak Azure účtuje využití virtuálních počítačů a webů.

Když si koupíte předplatné pro Azure Multi-Factor Authentication, vaše organizace platí jenom pro každého uživatele roční licenční poplatek. Licence MFA a sady Office 365, Azure AD Premium nebo Enterprise Mobility + Security sady se účtují tímto způsobem. 

Přečtěte si další informace o možnostech [získání služby Azure Multi-Factor Authentication](concept-mfa-licensing.md).

**Otázka: Je k dispozici bezplatná verze služby Azure Multi-Factor Authentication?**

V některých případech Ano.

Multi-Factor Authentication pro správce Azure nabízí podmnožinu funkcí Azure MFA zdarma pro přístup k online služby Microsoftu, včetně [centra pro správu](https://admin.microsoft.com) [Azure Portal](https://portal.azure.com) a Microsoft 365. Tato nabídka platí jenom pro globální správce v instancích Azure Active Directory, které nemají plnou verzi Azure MFA, prostřednictvím licence MFA, sady nebo samostatného poskytovatele založeného na spotřebě. Pokud vaši Správci používají bezplatnou verzi a pak koupíte plnou verzi Azure MFA, pak se všichni globální správci budou automaticky navýšit na placená verzi.

Multi-Factor Authentication pro uživatele Office 365 nabízí podmnožinu funkcí Azure MFA zdarma pro přístup ke službám Office 365, včetně Exchange Online a SharePointu Online. Tato nabídka platí pro uživatele, kteří mají přiřazenou licenci Office 365, pokud odpovídající instance Azure Active Directory nemá plnou verzi Azure MFA prostřednictvím licence MFA, sady nebo samostatného poskytovatele založeného na spotřebě.

**Otázka: Může moje organizace přepínat mezi jednotlivými modely fakturace podle počtu uživatelů a ověřování?**

Pokud vaše organizace koupí MFA jako samostatnou službu s fakturací na základě spotřeby, zvolíte model fakturace při vytváření poskytovatele MFA. Po vytvoření poskytovatele MFA nelze model fakturace změnit. 

Pokud váš poskytovatel MFA není propojený s klientem služby Azure AD nebo Pokud propojíte nového poskytovatele MFA s jiným klientem Azure AD, nastavení uživatele a možnosti konfigurace se nepřenesou. Je také potřeba znovu aktivovat stávající Azure MFA Servery pomocí přihlašovacích údajů pro aktivaci vygenerovaných prostřednictvím nového poskytovatele MFA. Opětovná aktivace MFA Serverů za účelem jejich propojení s novým poskytovatelem MFA nemá vliv na ověřování pomocí telefonního hovoru a textové zprávy, ale do opětovné aktivace mobilní aplikace přestanou pro všechny uživatele fungovat oznámení mobilní aplikace.

Další informace o poskytovatelích VÍCEFAKTOROVÉHO ověřování najdete v článku [Začínáme s poskytovatelem ověřování Azure Multi-Factor auth](concept-mfa-authprovider.md).

**Otázka: Může mi kdykoli přepínat mezi fakturací a předplatnými na základě spotřeby (modelem založeným na licencích)?**

V některých případech Ano.

Pokud má váš adresář poskytovatele Azure Multi-Factor Authentication *pro jednotlivé uživatele* , můžete přidat licence MFA. Uživatelům s licencemi se nepočítají při fakturaci na základě spotřeby podle uživatele. Uživatelům bez licencí je stále možné povolit vícefaktorové ověřování prostřednictvím poskytovatele MFA. Pokud si koupíte a přiřadíte licence všem uživatelům nakonfigurovaným pro použití služby Multi-Factor Authentication, můžete poskytovatele služby Azure Multi-Factor Authentication odstranit. Pokud v budoucnu máte více uživatelů, než kolik jich máte, můžete vždy vytvořit dalšího zprostředkovatele vícefaktorového ověřování pro každého uživatele.

Pokud má váš adresář poskytovatele *ověřování* Azure Multi-Factor Authentication, budou se vám vždycky účtovat všechna ověřování, pokud je poskytovatel MFA propojený s vaším předplatným. Uživatelům můžete přiřadit licence MFA, ale pořád se vám bude účtovat každá žádost o dvoustupňové ověření, ať už pochází od někoho, kdo má přiřazenou licenci MFA.

**Otázka: Musí moje organizace používat a synchronizovat identity pro použití Azure Multi-Factor Authentication?**

Pokud vaše organizace používá model fakturace založený na spotřebě, Azure Active Directory je volitelné, ale nevyžaduje se. Pokud váš poskytovatel MFA není propojený s klientem služby Azure AD, můžete nasadit jenom Azure Multi-Factor Authentication Server v místním prostředí.

Pro licenční model se vyžaduje Azure Active Directory, protože licence se přidávají do tenanta služby Azure AD, když je koupíte a přiřadíte uživatelům v adresáři.

## <a name="manage-and-support-user-accounts"></a>Správa a podpora uživatelských účtů

**Otázka: Co mám sdělit svým uživatelům, pokud neobdrží na svém telefonu odpověď?**

Požádejte uživatele, aby během 5 minut pomohly získat telefonní hovor nebo SMS k ověřování. Microsoft používá více poskytovatelů pro doručování hovorů a SMS zpráv. Pokud to nepomůže, otevřete prosím případ podpory u Microsoftu pro další řešení potíží.

Pokud výše uvedené kroky nefungují snad, všichni uživatelé nakonfigurovali více než jednu metodu ověřování. Řekněte jim, aby se zkusili znovu přihlásit, ale na přihlašovací stránce vybrali jinou metodu ověření.

Uživatele můžete nasměrovat na [Průvodce odstraňováním potíží koncových uživatelů](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

**Otázka: Co mám dělat, když některý z mých uživatelů nemůže získat přístup ke svému účtu?**

Můžete resetovat uživatelský účet tím, že ho provedete znovu pomocí procesu registrace. Přečtěte si další informace o [správě nastavení uživatelů a zařízení pomocí služby Azure Multi-Factor Authentication v cloudu](howto-mfa-userdevicesettings.md).

**Otázka: Co mám dělat, když některý z mých uživatelů ztratí telefon, který používá hesla aplikací?**

Pokud chcete zabránit neoprávněnému přístupu, odstraňte všechna hesla aplikací uživatele. Jakmile má uživatel náhradní zařízení, může hesla znovu vytvořit. Přečtěte si další informace o [správě nastavení uživatelů a zařízení pomocí služby Azure Multi-Factor Authentication v cloudu](howto-mfa-userdevicesettings.md).

**Otázka: Co když se uživatel nemůže přihlásit k neprohlížečovým aplikacím?**

Pokud vaše organizace stále používá starší klienty a Vy jste [povolili používání hesel aplikací](howto-mfa-mfasettings.md#app-passwords), nebudou se uživatelé moct k těmto starším klientům přihlašovat pomocí svého uživatelského jména a hesla. Místo toho musí nastavovat [hesla aplikací](../user-help/multi-factor-authentication-end-user-app-passwords.md). Uživatelé musí zrušit (odstranit) jejich přihlašovací údaje, restartovat aplikaci a pak se přihlašovat pomocí uživatelského jména a *hesla aplikace* místo jejich běžného hesla.

Pokud vaše organizace nemá starší verze klientů, neměli byste uživatelům vytvářet hesla aplikací.

> [!NOTE]
> Moderní ověřování pro klienty Office 2013
>
> Hesla aplikací jsou nutná jenom pro aplikace, které nepodporují moderní ověřování. Klienti Office 2013 podporují moderní ověřovací protokoly, ale je potřeba je nakonfigurovat. Teď je k dispozici moderní ověřování pro všechny zákazníky s aktualizací 2015 nebo novější pro Office 2013. Další informace najdete v blogovém příspěvku [aktualizovaného moderního ověřování Office 365](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/).

**Otázka: Moji uživatelé říkají, že někdy neobdrží textovou zprávu, nebo odpoví na obousměrné textové zprávy, ale vyprší časový limit ověřování.**

Doručování textových zpráv a příjem odpovědí v obousměrném SMS nejsou zaručeny, protože existují neovladatelné faktory, které by mohly ovlivnit spolehlivost služby. Mezi tyto faktory patří cílová země/oblast, mobilní telefonní hovor a síla signálu.

Pokud mají vaši uživatelé často problémy s spolehlivým příjmem textových zpráv, řekněte jim, aby místo toho používali metodu mobilní aplikace nebo telefonní hovor. Mobilní aplikace může dostávat oznámení přes mobilní připojení i připojení Wi-Fi. Kromě toho může mobilní aplikace generovat ověřovací kódy i v případě, že zařízení nemá žádný signál. Aplikace Microsoft Authenticator je dostupná pro [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)a [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071).

Pokud je nutné použít textové zprávy, doporučujeme, abyste pomocí jednosměrového SMS nepoužívali službu SMS, pokud je to možné. Jednosměrná služba SMS je spolehlivější a zabraňuje uživatelům v navracení globálních poplatků za SMS z odpovědi na textovou zprávu, která byla odeslána z jiné země nebo oblasti.

**Otázka: Můžu změnit dobu, po kterou musí uživatelé zadat ověřovací kód z textové zprávy, než vyprší časový limit pro systém?**

V některých případech Ano. 

Pro jednosměrné služby SMS s Azure MFA serverem v 7.0 nebo vyšší můžete nakonfigurovat nastavení časového limitu nastavením klíče registru. Jakmile cloudová služba MFA pošle textovou zprávu, ověřovací kód (nebo jednorázové heslo) se vrátí na server MFA. Server MFA ukládá kód do paměti po dobu 300 sekund ve výchozím nastavení. Pokud uživatel nezadá kód před uplynutím 300 sekund, je jeho ověřování zamítnuto. Pomocí těchto kroků můžete změnit výchozí nastavení časového limitu:

1. Přejít na HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor.
2. Vytvořte klíč registru typu DWORD s názvem **pfsvc_pendingSmsTimeoutSeconds** a nastavte dobu v sekundách, po kterou má Azure MFA Server ukládat jednorázová hesla.

>[!TIP] 
>Pokud máte více serverů MFA, pouze ta, která zpracovala původní požadavek na ověření, zná ověřovací kód, který byl odeslán uživateli. Když uživatel zadá kód, žádost o ověření, která se má ověřit, musí být odeslána na stejný server. Pokud je ověřování kódu odesláno na jiný server, ověřování je odepřeno. 

Pro oboustranný SMS s Azure MFA serverem můžete nakonfigurovat nastavení časový limit v Portál pro správu MFA. Pokud uživatelé nereagují na zprávu SMS v rámci definovaného časového limitu, je jejich ověření zamítnuto. 

V případě jednosměrného SMS s Azure MFA v cloudu (včetně adaptéru AD FS nebo rozšíření serveru síťových zásad) nemůžete konfigurovat nastavení časového limitu. Azure AD ukládá ověřovací kód po dobu 180 sekund. 

**Otázka: Můžu používat hardwarové tokeny u serveru Azure Multi-Factor Authentication?**

Pokud používáte Azure Multi-Factor Authentication Server, můžete importovat tokeny založené na čase a TOTP (jednorázové heslo) třetích stran a pak je použít pro dvoustupňové ověřování.

Tokeny ActiveIdentity, které jsou tokeny OATH TOTP, můžete použít, pokud vložíte tajný klíč do souboru CSV a naimportujete na server Azure Multi-Factor Authentication. Můžete použít tokeny OATH s ověřováním pomocí služby Active Directory Federation Services (AD FS) (ADFS), ověřování založené na formulářích internetové informační služby (IIS) a vzdáleného ověřování pro uživatele (RADIUS), pokud klientský systém může přijmout uživatelský vstup.

Tokeny OATH TOTP třetích stran můžete importovat v následujících formátech:  

- Přenosný kontejner symetrického klíče (PSKC)  
- Sdílený svazek clusteru (CSV), pokud soubor obsahuje sériové číslo, tajný klíč ve formátu Base 32 a časový interval  

**Otázka: Můžu použít Azure Multi-Factor Authentication Server k zabezpečení Terminálové služby?**

Ano, ale pokud používáte Windows Server 2012 R2 nebo novější, můžete zabezpečit Terminálové služby jenom pomocí Brána vzdálené plochy (Brána VP).

Změny zabezpečení ve Windows Serveru 2012 R2 mění způsob, jakým se Azure Multi-Factor Authentication Server připojuje k balíčku zabezpečení místního úřadu zabezpečení (LSA) v systému Windows Server 2012 a starších verzích. Pro verze Terminálové služby ve Windows Serveru 2012 nebo starší můžete [zabezpečit aplikaci pomocí ověřování systému Windows](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Pokud používáte Windows Server 2012 R2, potřebujete Brána VP.

**Otázka: Nakonfiguroval (a) jsem ID volajícího na serveru MFA, ale všichni uživatelé pořád obdrží volání služby Multi-Factor Authentication od anonymního volajícího.**

Pokud jsou volání Multi-Factor Authentication umístěna prostřednictvím veřejné telefonní sítě, někdy jsou směrována prostřednictvím dopravce, který nepodporuje ID volajícího. Z tohoto důvodu není ID volajícího zaručeno, a to i v případě, že je systém Multi-Factor Authentication vždy odesílá.

**Otázka: Proč jsou moji uživatelé vyzváni k registraci svých informací o zabezpečení?**
Existuje několik důvodů, proč se uživatelům zobrazí výzva k registraci informací o zabezpečení:

- Uživatel povolil ověřování MFA správcem ve službě Azure AD, ale ještě nemá pro svůj účet zaregistrované žádné informace o zabezpečení.
- Uživatel povolil samoobslužné resetování hesla ve službě Azure AD. Informace o zabezpečení jim pomůžou resetovat heslo v budoucnu, pokud ho někdy zapomenou.
- Uživatel, který získal přístup k aplikaci, která má zásady podmíněného přístupu, aby vyžadoval MFA a nebyl dřív zaregistrován pro MFA.
- Uživatel registruje zařízení ve službě Azure AD (včetně připojení k Azure AD) a vaše organizace vyžaduje MFA pro registraci zařízení, ale uživatel se dřív nezaregistroval pro MFA.
- Uživatel generuje Windows Hello pro firmy ve Windows 10 (který vyžaduje MFA) a ještě není zaregistrovaný pro MFA.
- Organizace vytvořila a povolila zásady registrace MFA, které se pro uživatele nastavily.
- Uživatel, který byl dříve zaregistrován pro MFA, ale zvolil metodu ověřování, kterou správce od zakázání zakázal. Uživatel musí proto znovu projít registrací MFA a vybrat novou výchozí metodu ověřování.

## <a name="errors"></a>Chyby

**Otázka: Co by uživatelé měli dělat, pokud se při použití oznámení mobilní aplikace zobrazí chybová zpráva "žádost o ověření není pro aktivovaný účet"?**

Řekněte jim, abyste mohli pomocí tohoto postupu odebrat účet z mobilní aplikace a pak ho znovu přidat:

1. Přejít na [profil Azure Portal](https://account.activedirectory.windowsazure.com/profile/) a přihlaste se pomocí účtu organizace.
2. Vyberte **Další ověření zabezpečení**.
3. Odeberte existující účet z mobilní aplikace.
4. Klikněte na **Konfigurovat**a potom postupujte podle pokynů pro překonfigurování mobilní aplikace.

**Otázka: Co by uživatelé měli dělat, pokud se při přihlašování k aplikaci bez prohlížeče uvidí chybová zpráva 0x800434D4L?**

K chybě 0x800434D4L dojde při pokusu o přihlášení do neprohlížečové aplikace nainstalované v místním počítači, která nefunguje s účty, které vyžadují dvoustupňové ověřování.

Alternativním řešením této chyby je, aby byly samostatné uživatelské účty pro operace týkající se správců a jiných správců. Později můžete propojit poštovní schránky mezi účtem správce a účtem bez správce, abyste se mohli přihlásit k Outlooku pomocí účtu bez správce. Další informace o tomto řešení najdete v tématu Jak [dát správci možnost otevřít a zobrazit obsah poštovní schránky uživatele](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Další postup

Pokud tady na váš dotaz neodpovíte, ponechte ho prosím v komentářích v dolní části stránky. Nebo tady jsou některé další možnosti, jak získat nápovědu:

* Vyhledejte v [Podpora Microsoftu znalostní bázi](https://www.microsoft.com/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport) řešení běžných technických problémů.
* Vyhledejte a procházejte technické dotazy a odpovědi z komunity nebo položte svoji vlastní otázku ve [fórech Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* Pokud jste PhoneFactor starší verze zákazníka a máte nějaké dotazy nebo potřebujete pomoc s obnovením hesla, použijte odkaz pro [resetování hesla](mailto:phonefactorsupport@microsoft.com) a otevřete případ podpory.
* Kontaktujte odborníka na podporu prostřednictvím [podpory Azure Multi-Factor Authentication serveru (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947). Při kontaktování nás je užitečné, pokud můžete zahrnout co nejvíce informací o vašem problému. Informace, které můžete dodat, zahrnují stránku, kde jste viděli chybu, konkrétní kód chyby, konkrétní ID relace a ID uživatele, který chybu viděl.

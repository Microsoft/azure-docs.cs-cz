---
title: Azure Multi-Factor Authentication – nejčastější dotazy | Dokumentace Microsoftu
description: Nejčastější dotazy a odpovědi související s ověřováním Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 46d8a26398ed63bef4fa101fbaf0be9087880109
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214143"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Nejčastější dotazy k Azure Multi-Factor Authentication

Tyto nejčastější dotazy odpovědi na běžné dotazy týkající se Azure Multi-Factor Authentication a pomocí služby Multi-Factor Authentication. To je rozdělena do otázky týkající se služby obecně modelem fakturace vázaným uživatelského prostředí a řešení potíží.

## <a name="general"></a>Obecné

**Otázka: Jak Azure Multi-Factor Authentication Server zpracovává data uživatelů?**

Multi-Factor Authentication Server uživatelská data uloží jenom na místních serverech. V cloudu se neukládají žádná trvalá data. Když uživatel provádí dvoustupňové ověřování, ověřování Multi-Factor Authentication Server odešle data do cloudové služby Azure Multi-Factor Authentication pro ověřování. Komunikace mezi Multi-Factor Authentication Server a cloudovou službou Multi-Factor Authentication používá vrstvy SSL (Secure Sockets) nebo zabezpečení TLS (Transport Layer) přes port 443 odchozí.

Při žádosti o ověření odesílají do cloudové služby pro ověřování a využití se shromažďují data sestavy. Datová pole, které jsou součástí dvoustupňové ověření protokoly jsou následující:

* **Jedinečné ID** (buď uživatelské jméno nebo místní Multi-Factor Authentication Server ID)
* **První a poslední název** (volitelné)
* **E-mailová adresa** (volitelné)
* **Telefonní číslo** (při použití hlasový hovor nebo SMS ověřování)
* **Token zařízení** (při použití ověřování mobilní aplikací)
* **Režim ověřování**
* **Výsledek ověřování**
* **Název serveru služby Multi-Factor Authentication**
* **Server Multi-Factor Authentication IP**
* **Klient IP** (Pokud je k dispozici)

Volitelná pole je možné nakonfigurovat na serveru Multi-Factor Authentication Server.

Výsledek ověření (úspěch nebo odmítnutí) a důvod, pokud byl odepřen, je uložen s ověřovacími údaji. Tato data jsou k dispozici k ověřování a v sestavách využití.

**Otázka: Jaké krátké kódy SMS se používají pro zasílání zpráv SMS pro moje uživatele?**

V USA Microsoft používá následující kódy krátký SMS:

   * 97671
   * 69829
   * 51789
   * 99399

V Kanadě Microsoft používá následující kódy krátký SMS:

   * 759731 
   * 673801

Microsoft nezaručuje konzistentní SMS nebo ověřování službou Multi-Factor Authentication pomocí hlasu výzvy doručení stejné číslo. Větší naši uživatelé mohou Microsoft přidávat nebo odebírat krátké kódy kdykoli během postupu úpravy ke zlepšení doručitelnosti SMS. Microsoft nepodporuje krátké kódy zemí kromě USA a Kanada

## <a name="billing"></a>Fakturace

Většina fakturaci může zodpoví odkazující na buď [stránce s cenami ověřování službou Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) nebo v dokumentaci o [jak získat Azure Multi-Factor Authentication](concept-mfa-licensing.md).

**Otázka: Účtuje se Moje organizace pro odesílání telefonní hovory a textové zprávy, které se používají k ověřování?**

Ne, se neúčtují jednotlivé telefonní hovory nebo textové zprávy odeslané uživatelům prostřednictvím ověřování Azure Multi-Factor Authentication. Pokud používáte poskytovatele MFA podle ověřování, se účtují pro každé ověření, ale ne pro metodu použít.

Uživatelé můžou účtovat telefonní hovory nebo textové zprávy, kterou obdrží, podle jejich osobní telefonní služby.

**Otázka: Model fakturace za uživatele účtuje se mi všechny povolené uživatele nebo pouze ty, které provádí dvoustupňové ověřování?**

Účtování vychází z počtu uživatelů nakonfigurována pro použití služby Multi-Factor Authentication, bez ohledu na to, jestli se provádí dvoustupňové ověřování tento měsíc.

**Otázka: Jak funguje fakturace služby Multi-Factor Authentication?**

Při vytváření poskytovatele vícefaktorového ověřování na uživatele nebo na ověřování se vaše organizace předplatné Azure účtuje každý měsíc na základě využití. Tento model fakturace se podobá Azure účtuje za využití virtual machines a websites.

Když si koupíte předplatné pro Azure Multi-Factor Authentication, vaše organizace jenom platí roční poplatek za licenci pro každého uživatele. Tímto způsobem se účtují licence MFA a Office 365, Azure AD Premium nebo Enterprise Mobility + Security sady. 

Další informace o možnostech [jak získat Azure Multi-Factor Authentication](concept-mfa-licensing.md).

**Otázka: Existuje bezplatná verze Azure Multi-Factor Authentication?**

V některých případech Ano.

Ověřování službou Multi-Factor Authentication pro správce Azure nabízí podmnožinu funkcí Azure MFA bez poplatků pro přístup k online službám Microsoftu, včetně portály správce Azure a Office 365. Tato nabídka platí jenom pro globální správci v instancích Azure Active Directory, které nemají plnou verzi Azure MFA prostřednictvím licenci MFA, sadu nebo samostatný založenou na skutečné spotřebě zprostředkovatele. Pokud vaši správci použít bezplatnou verzi, a poté zakoupit plnou verzi Azure MFA, pak všichni globální správci se převedou na placenou verzi automaticky.

Multi-Factor Authentication pro uživatele Office 365 nabízí podmnožinu funkcí Azure MFA bez poplatků pro přístup ke službám Office 365, jako jsou Exchange Online a SharePoint Online. Tato nabídka platí pro uživatele, kteří mají přiřazen, když odpovídající instance služby Azure Active Directory nemá plnou verzi Azure MFA prostřednictvím licenci MFA, sadu nebo samostatný založenou na skutečné spotřebě poskytovatelem licence k Office 365.

**Otázka: Přepínat mezi jednotlivých uživatelů nebo ověření spotřeby modely fakturace kdykoli mé organizaci?**

Pokud vaše organizace zakoupí MFA jako samostatná služba s využitím fakturace založený na spotřebě, zvolte model fakturace při vytváření poskytovatele MFA. Model fakturace nelze změnit po vytvoření poskytovatele MFA. Můžete však poskytovatele MFA odstranit a pak vytvořte jiný model fakturace.

Po vytvoření poskytovatele MFA nesmí být propojení služby Azure Active Directory (označuje se také jako "Azure AD tenant"). Pokud aktuální poskytovatele MFA je propojený s tenantem Azure AD, můžete poskytovatele MFA odstranit a vytvořit nového, který je spojen do stejného tenanta služby Azure AD. Alternativně, pokud jste zakoupili dostatek licencí MFA, Azure AD Premium nebo Enterprise Mobility + Security (EMS) k pokrytí všech uživatelů s povoleným vícefaktorovým ověřováním, můžete poskytovatele MFA odstranit úplně.

Pokud je poskytovatel MFA *není* propojený s tenantem Azure AD nebo propojení nového poskytovatele MFA do jiné služby Azure AD tenanta, uživatelská nastavení a možnosti konfigurace se nepřenesou. Je také potřeba znovu aktivovat stávající Azure MFA Servery pomocí přihlašovacích údajů pro aktivaci vygenerovaných prostřednictvím nového poskytovatele MFA. Opětovná aktivace MFA Serverů za účelem jejich propojení s novým poskytovatelem MFA nemá vliv na ověřování pomocí telefonního hovoru a textové zprávy, ale do opětovné aktivace mobilní aplikace přestanou pro všechny uživatele fungovat oznámení mobilní aplikace.

Další informace o poskytovatelích MFA v [Začínáme s poskytovatelem služby Azure Multi-Factor Auth](concept-mfa-authprovider.md).

**Otázka: Moje organizace přepínat mezi založenou na skutečné spotřebě fakturace a předplatných (na základě licenční model) v čase?**

V některých případech Ano.

Pokud má váš adresář *uživatelská* poskytovatele ověřování Azure Multi-Factor Authentication, můžete přidat licence MFA. Uživatelé s licencí se nepočítají ve fakturaci za uživatele založenou na skutečné spotřebě. Uživatelé bez licence je možné povolit stále pro vícefaktorové ověřování prostřednictvím poskytovatele MFA. Pokud zakoupíte a přiřazení licencí pro vaše uživatele nakonfigurována pro použití služby Multi-Factor Authentication, můžete odstranit poskytovatele ověřování Azure Multi-Factor Authentication. Pokud máte více uživatelů než licence v budoucnu můžete kdykoli vytvořit jiného poskytovatele MFA podle uživatele.

Pokud má váš adresář *podle ověřování* poskytovatele ověřování Azure Multi-Factor Authentication, je vždy se účtují pro každé ověření tak dlouho, dokud se k předplatnému připojený poskytovatele MFA. Můžete přiřadit licence MFA pro uživatele, ale budete pořád se vám účtovat každou žádost o dvoustupňové ověření, zda pochází od uživatele s licencí MFA, nebo nemáte přiřazené.

**Otázka: Má Moje organizace používat a synchronizaci identit pomocí Azure Multi-Factor Authentication?**

Pokud vaše organizace používá model fakturace založený na spotřebě, Azure Active Directory je volitelné, ale nejsou vyžadována. Pokud poskytovatel MFA není propojený s tenantem Azure AD, můžete nasadit jenom ověřování Azure Multi-Factor Authentication Server lokálně.

Azure Active Directory je požadovaná pro licenční model, protože licence se přidají do tenanta Azure AD při nákupu a přiřadit je uživatelům v adresáři.

## <a name="manage-and-support-user-accounts"></a>Správa a podpora uživatelských účtů

**Otázka: Co říct Moji uživatelé dělat, když nedostávají odpovědi na svůj telefon?**

Požádejte uživatele, pokus až 5krát během 5 minut. Chcete-li získat telefonního hovoru nebo SMS pro ověření. Společnost Microsoft používá několik poskytovatelů pro doručování hovory a SMS zpráv. Pokud to nebude fungovat otevřete případ podpory s Microsoftem-li dále řešit.

Pokud výše uvedené kroky nefungují snad všichni uživatelé nakonfigurovaní více než jednu metodu ověřování. Řekněte jim, aby se zkusili znovu přihlásit, ale na přihlašovací stránce vybrali jinou metodu ověření.

Pak můžete uživatelům [Průvodce odstraňováním potíží s koncovým uživatelem](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

**Otázka: Co mám dělat, když jeden z uživatelů nemůže dostat se do svého účtu?**

Uživatelský účet můžete obnovit tak, že je znovu projít procesem registrace. Další informace o [Správa nastavení uživatelů a zařízení s Azure Multi-Factor Authentication v cloudu](howto-mfa-userdevicesettings.md).

**Otázka: Co mám dělat, pokud jeden z uživatelů ztratí telefon, který používá hesla aplikací?**

Abyste zabránili neoprávněnému přístupu, odstraňte všechna uživatelská hesla aplikace. Poté, co uživatel má náhradní zařízení, můžete znovu vytvořit hesla. Další informace o [Správa nastavení uživatelů a zařízení s Azure Multi-Factor Authentication v cloudu](howto-mfa-userdevicesettings.md).

**Otázka: Co když uživatel přihlásit k neprohlížečovým aplikacím?**

Pokud vaše organizace stále používá starší verze klientů a [povolené použití hesla aplikací](howto-mfa-mfasettings.md#app-passwords), pak uživatelé přihlásit do těchto starších verzí klientů pomocí uživatelského jména a hesla. Místo toho je nutné [nastavit hesla aplikací](../user-help/multi-factor-authentication-end-user-app-passwords.md). Uživatelé musí vymazat (delete) své přihlašovací údaje, restartujte ji a pak se přihlaste pomocí svého uživatelského jména a *heslo aplikace* místo hesla regulárních.

Pokud vaše organizace nemá starší verze klientů, by neměla uživatelům povolíte vytváření hesel aplikací.

> [!NOTE]
> Moderní ověřování pro klienty Office 2013
>
> Hesla aplikací jsou jenom pro aplikace, které nepodporují moderní ověřování. Podpora moderních ověřovacích protokolů klientech Office 2013, ale je potřeba nakonfigurovat. Novější klienti Office automaticky podporují moderních ověřovacích protokolů. Další informace najdete v tématu [oznámení veřejné preview moderní ověřování Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

**Otázka: Moji uživatelé prohlašují, že někdy nedostávají textovou zprávu nebo se odpovědět na obousměrné textové zprávy, ale ověření vyprší časový limit.**

Doručování textových zpráv a příjem odpovědí v obousměrné služby SMS není zaručena. protože neovladatelném faktorů, které by mohly ovlivnit spolehlivost služby. Tyto faktory zahrnují určení země, poskytovatelé mobilních služeb a sílu signálu.

Pokud mají vaši uživatelé často potíže se spolehlivým příjmem textových zpráv, řekněte jim, aby místo toho použijte metodu mobilní aplikace nebo telefonní hovor. Mobilní aplikace může přijímat oznámení i přes mobilní síť a připojení Wi-Fi. Mobilní aplikace navíc můžete generovat ověřovací kódy, i v případě, že zařízení nemá žádné signál vůbec. Aplikace Microsoft Authenticator je dostupná pro [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [IOS](https://go.microsoft.com/fwlink/?Linkid=825073), a [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071).

Pokud je nutné použít textové zprávy, doporučujeme používat jednosměrná zpráva SMS, spíše než obousměrné služby SMS, pokud je to možné. Jednosměrné služby SMS je spolehlivější a zabraňuje uživatelům v poplatků globální služby SMS v odpovědi na textovou zprávu, která byla odeslána z jiné země.

**Otázka: Můžete změnit dobu Moji uživatelé muset před systému vypršením časového limitu zadejte ověřovací kód z textové zprávy?**

V některých případech Ano. 

Pro jednosměrná zpráva SMS s Azure MFA Server 7.0 nebo vyšší můžete nakonfigurovat časový limit nastavte klíč registru. Po cloudové služby MFA odešle textovou zprávu, ověřovací kód (nebo jednorázové heslo) se vrátí do MFA serveru. MFA Server ukládá kód v paměti pro 300 sekund ve výchozím nastavení. Pokud uživatel nemá kód, předtím, než jste předali do 300 sekund, je jejich ověřování odepřeno. Chcete-li změnit výchozí nastavení časového limitu, použijte tyto kroky:

1. Přejdete na HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor.
2. Vytvořte klíč registru DWORD s názvem **pfsvc_pendingSmsTimeoutSeconds** a nastavit čas v sekundách, že chcete Azure MFA Server k ukládání jednorázového hesla.

>[!TIP] 
>Pokud máte víc serverů MFA, zná pouze jeden, který zpracovává požadavek na původní ověření ověřovací kód, který byl odeslán uživateli. Když uživatel zadá kódu, žádost o ověření, abyste ověřili, že musí poslat na stejný server. Pokud se ověření kódu se pošle na jiný server a ověřování byl odepřen. 

Pro obousměrné služby SMS s Azure MFA serverem můžete nakonfigurovat nastavení časového limitu v portálu pro správu MFA. Jestliže uživatelé není na serveru SMS v rámci definovaný časový limit, je jejich ověřování odepřeno. 

Pro jednosměrná zpráva SMS s Azure MFA v cloudu (včetně adaptéru služby AD FS nebo rozšíření serveru Network Policy Server) nemůže nakonfigurovat nastavení časového limitu. Ověřovací kód, který ukládá služba Azure AD pro 180 sekund. 

**Otázka: Můžete používat tokeny hardwaru serverem Azure Multi-Factor Authentication?**

Pokud používáte ověřování Azure Multi-Factor Authentication Server, můžete importovat tokeny na základě času, jednorázové heslo (TOTP) otevřete ověřování (OATH) třetích stran a budete je moct používat k dvoustupňovému ověřování.

Můžete použít ActiveIdentity tokeny, které jsou tokeny OATH TOTP, je-li uložit tajný klíč v souboru CSV a importovat do Azure Multi-Factor Authentication Server. Můžete použít tokeny OATH s Active Directory Federation Services (ADFS), ověřování pomocí formulářů Internet Information Server (IIS) a RADIUS Remote Authentication telefonického uživatele Service (), tak dlouho, dokud klientský systém může přijmout vstup uživatele.

Můžete importovat tokeny OATH TOTP třetích stran pomocí následujících formátů:  

- Kontejner přenosný symetrického klíče (PSKC)  
- Sdílený svazek clusteru, pokud soubor obsahuje sériové číslo, tajný klíč ve formátu Base 32 a časový interval  

**Otázka: Můžete použít Azure Multi-Factor Authentication Server pro zabezpečené Terminálové služby**

Ano, ale pokud používáte Windows Server 2012 R2 nebo novější můžete pouze zabezpečené Terminálové služby s použitím služby Brána vzdálené plochy (Brána VP).

Změny zabezpečení ve Windows serveru 2012 R2 změnit, jak se Azure Multi-Factor Authentication Server připojí k balíčku zabezpečení místního úřadu zabezpečení (LSA) v systému Windows Server 2012 a dřívějších verzích. Pro Terminálové služby v systému Windows Server 2012 nebo starší verze, můžete [zabezpečení aplikace s ověřováním Windows](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Pokud používáte Windows Server 2012 R2, je třeba Brána VP.

**Otázka: Jsem nakonfiguroval ID volajícího MFA serveru, ale uživatelé stále přijímat hovor služby Multi-Factor Authentication z anonymní volajícího.**

Při volání služby Multi-Factor Authentication jsou umístěny přes veřejnou telefonní síť, někdy se směrují prostřednictvím operátora, který nepodporuje ID volajícího. Z tohoto důvodu není zaručeno ID volajícího, i když systém Multi-Factor Authentication je vždy posílá.

**Otázka: Proč svým uživatelům se výzva k registraci informací o jejich zabezpečení?**
Tady je několik důvodů, že by mohla uživatelé vyzváni k registraci jejich zabezpečení informací:

- Uživatel byl povolen pro MFA podle jejich správce ve službě Azure AD, ale neobsahuje informace o zabezpečení, které jste se nezaregistrovali pro svůj účet.
- Uživatel je povolen pro samoobslužné resetování hesla ve službě Azure AD. Informace o zabezpečení je v budoucnu obnovit své heslo, pokud se někdy zapomněli pomůže.
- Uživatel získat přístup k aplikaci, která má zásady podmíněného přístupu k vynucení vícefaktorového ověřování a nebyl dříve registrován pro vícefaktorové ověřování.
- Uživatel registruje zařízení s Azure AD (včetně připojení ke službě Azure AD) a vaše organizace vyžaduje vícefaktorové ověřování pro registraci zařízení, ale uživatel nebyl dříve registrován pro vícefaktorové ověřování.
- Uživatel je generování Windows Hello pro firmy ve Windows 10 (které vyžadují vícefaktorové ověřování) a nebyl dříve registrován pro vícefaktorové ověřování.
- Organizace vytvořila a povolené zásady registrace MFA, který byl použit pro uživatele.
- Uživatel dříve zaregistrovaný pro MFA, ale vybrali metodu ověřování, které od té doby správce zakázal. Uživatel musí projít proto registrace MFA znovu a vyberte nový výchozí metodu ověřování.

## <a name="errors"></a>Chyby

**Otázka: Co uživatelé dělat, když se zobrazí chybová zpráva "žádost o ověření není pro aktivovaný účet", při použití oznámení mobilní aplikace?**

Řekněte jim, aby pomocí následujícího postupu odebrat svůj účet z mobilní aplikace a pak ji znovu přidat:

1. Přejděte na [profilu Azure portal](https://account.activedirectory.windowsazure.com/profile/) a přihlaste se pomocí účtu organizace.
2. Vyberte **dalšího ověření zabezpečení**.
3. Odeberte existující účet z mobilní aplikace.
4. Klikněte na tlačítko **konfigurovat**a pak postupujte podle pokynů k překonfigurování mobilní aplikace.

**Otázka: Co uživatelé dělat, když se zobrazí chybová zpráva 0x800434D4L při přihlášení k aplikaci nezaložené na prohlížeči?**

Při pokusu o přihlášení k aplikaci nezaložené na prohlížeči, nainstalované na místním počítači, který nefunguje s účty, které vyžadují dvoustupňové ověření, dojde k chybě 0x800434D4L.

Alternativní řešení pro tuto chybu je mít samostatné uživatelské účty pro správu související a operace bez oprávnění správce. Později můžete propojit poštovní schránky mezi účtem správce a účet bez oprávnění správce tak, aby můžete se přihlásit k aplikaci Outlook pomocí účtu bez oprávnění správce. Další podrobnosti o tomto řešení, zjistěte, jak [správce umožnit otevřít a zobrazit obsah z poštovní schránky uživatele](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Další postup

Pokud váš dotaz zde nenalezl, nechejte ji prosím do komentářů v dolní části stránky. Nebo, tady jsou některé další možnosti pro získání nápovědy:

* Hledání [znalostní báze podpory Microsoftu](https://www.microsoft.com/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport) pro řešení běžných potíží technické.
* Vyhledat a procházet technické dotazy a odpovědi z komunity nebo vlastní dotaz v [fóra služby Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* Pokud jste zákazník starší verze PhoneFactor a máte otázky nebo potřebujete pomoc, resetuje se heslo, použijte [resetování hesla](mailto:phonefactorsupport@microsoft.com) odkaz k otevření případu podpory.
* Obraťte se na pracovníka technické podpory prostřednictvím [podporu ověřování Azure Multi-Factor Authentication Server (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947). Pokud nás kontaktujete, je užitečné, pokud zahrnete co nejvíce informací o svém problému nejvíce. Obsahuje informace, které můžete zadat stránky, kde jste viděli chyby, kód chyby, ID konkrétní relace a ID uživatele, který zobrazila chyba.

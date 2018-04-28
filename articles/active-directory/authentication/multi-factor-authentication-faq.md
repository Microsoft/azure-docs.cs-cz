---
title: Azure Multi-Factor Authentication – nejčastější dotazy | Microsoft Docs
description: Časté otázky a odpovědi související s Azure Multi-Factor Authentication. Služba Multi-Factor Authentication je metoda ověření identity uživatele, který vyžaduje více než uživatelské jméno a heslo. Poskytuje další úroveň zabezpečení pro přihlášení uživatele a transakce.
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.assetid: 50bb8ac3-5559-4d8b-a96a-799a74978b14
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: joflore
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7c6941f381c6c469001bab7a4ff9e742daa2d804
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Časté otázky k Azure Multi-Factor Authentication
Tyto nejčastější dotazy odpovídá na běžné otázky o Azure Multi-Factor Authentication a pomocí služby Multi-Factor Authentication. Ho je rozdělena do otázky týkající se služby obecně fakturace modely, koncových uživatelů a řešení potíží.

## <a name="general"></a>Obecné
**Otázka: jak Azure Multi-Factor Authentication Server zpracovávat uživatelská data?**

Pomocí aplikace Multi-Factor Authentication Server je uložená data uživatele pouze na místní servery. V cloudu se neukládají žádná trvalá data. Když uživatel provede dvoustupňové ověřování, služba Multi-Factor Authentication Server odešle data ke cloudové službě Azure Multi-Factor Authentication pro ověřování. Komunikace mezi serverem služby Multi-Factor Authentication a cloudové služby Multi-Factor Authentication využívá Secure Sockets Layer (SSL) nebo zabezpečení TLS (Transport Layer) přes port 443 odchozí.

Pokud požadavky na ověření pošlou do cloudové služby, data se shromažďují pro ověřování a použití sestav. Datová pole, které jsou součástí dvoustupňové ověření protokoly jsou následující:

* **Jedinečné ID** (buď název nebo místní služby Multi-Factor Authentication Server ID uživatele)
* **První a poslední název** (volitelné)
* **E-mailová adresa** (volitelné)
* **Telefonní číslo** (při použití telefonní hovor nebo SMS ověřování)
* **Token zařízení** (při použití ověřování mobilní aplikací)
* **Režim ověřování**
* **Výsledek ověřování**
* **Název serveru služby Multi-Factor Authentication**
* **Server Multi-Factor Authentication IP**
* **Klient IP** (Pokud je k dispozici)

Volitelná pole lze konfigurovat v aplikaci Multi-Factor Authentication Server.

Výsledek ověření (úspěch nebo odmítnutí) a z důvodu, pokud mu byl odepřen, se uloží spolu data ověřování. Tato data jsou k dispozici na ověřování a v sestavách využití.

**Otázka: jaký SMS krátké kódy se používají pro odesílání zpráv serveru SMS na můj uživatele?**

Ve Spojených státech amerických Microsoft používá následující kódy krátké SMS:

   * 97671
   * 69829
   * 51789
   * 99399

V Kanadě na společnost Microsoft používá následující kódy krátké SMS:

   * 759731 
   * 673801

Společnost Microsoft nezaručuje konzistentní SMS nebo na základě hlasové služby Multi-Factor Authentication výzva doručení o stejné číslo. V zájmu naši uživatelé Microsoft může přidat nebo odebrat krátké kódy kdykoli, jak můžeme provádět úpravy trasy ke zlepšení SMS deliverability. Microsoft nepodporuje krátké kódy zemí kromě USA a Kanada

## <a name="billing"></a>Fakturace
Většina máte dotazy k fakturaci může odpovědět odkazující na buď [stránce s cenami vícefaktorového ověřování](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) nebo v dokumentaci o [jak získat Azure Multi-Factor Authentication](concept-mfa-licensing.md).

**Otázka: je Moje organizace účtovat pro posílání telefonních hovorů a textové zprávy, které se používají pro ověřování?**

Ne, vám není účtován u jednotlivých telefonní hovory umístit nebo textu zprávy odeslané do uživatele pomocí ověřování Azure Multi-Factor Authentication. Pokud používáte poskytovatele MFA podle ověření, se účtují pro každé ověření, ale ne pro metodu používanou.

Vaši uživatelé možná budou účtovat pro telefonní hovory nebo textové zprávy, které obdrží, podle jejich osobní telefonní služby.

**Otázka: model fakturace na uživatele účtují mi pro všechny povolené uživatele nebo pouze ty, které provádí dvoustupňové ověřování?**

Počet uživatelů, které jsou nakonfigurované na používání ověřování Multi-Factor Authentication, bez ohledu na to, jestli se provedlo dvoustupňové ověření daném měsíci vychází fakturace.

**Otázka: jak fakturace služby Multi-Factor Authentication funguje?**

Při vytváření poskytovatele MFA podle uživatele nebo podle ověření předplatného Azure vaší organizace se fakturuje měsíčně na základě využití. Tento model fakturace je podobná jak Azure účtuje poplatky za využití virtuálních počítačů a weby.

Při zakoupení předplatného pro Azure Multi-Factor Authentication, vaše organizace jenom platí roční poplatky licence pro každého uživatele. Tímto způsobem se účtují licence MFA a Office 365, Azure AD Premium nebo Enterprise Mobility + Security sady. 

Další informace o dostupných možnostech najdete v [jak získat Azure Multi-Factor Authentication](concept-mfa-licensing.md).

**Otázka: je bezplatnou verzi Azure Multi-Factor Authentication?**

V některých případech Ano.

Služba Multi-Factor Authentication pro správce Azure nabízí podmnožinu funkcí Azure MFA bez nákladů pro přístup k online službám společnosti Microsoft, včetně portálů správce Azure a Office 365. Tato nabídka platí jenom pro globální správci instancí Azure Active Directory, které nemají plnou verzi Azure MFA prostřednictvím licenci MFA, sady nebo samostatné zprostředkovatele na základě spotřeby. Pokud správci použít bezplatnou verzi, a poté zakoupit plnou verzi Azure MFA, pak všichni globální správci jsou se zvýšenými oprávněními na placené verzi automaticky.

Víceúrovňové ověřování pro uživatele služeb Office 365 nabízí podmnožinu funkcí Azure MFA bez nákladů pro přístup ke službám Office 365, včetně Exchange Online a SharePoint Online. Tato nabídka platí pro uživatele, kteří mají licenci Office 365, který je přiřazen, když odpovídající instance služby Azure Active Directory nemá plnou verzi Azure MFA prostřednictvím licenci MFA, sady nebo samostatné zprostředkovatele na základě spotřeby.

**Otázka: je možné mezi jednotlivé uživatele nebo podle ověření spotřeba modely fakturace kdykoli přepnout Moje organizace?**

Pokud vaše organizace zakoupí MFA jako samostatné služby s fakturace na základě spotřeby, zvolíte Fakturační model, při vytváření poskytovatele MFA. Fakturační model nelze změnit po vytvoření poskytovatele MFA. Můžete však odstranit zprostředkovatele vícefaktorového ověřování a pak vytvořte jiný model fakturace.

Při vytvoření poskytovatele MFA, můžete propojit s Azure Active Directory (neboli "klienta Azure AD"). Pokud aktuální zprostředkovatel vícefaktorového ověřování je propojený klient služby Azure AD, můžete bezpečně odstranit zprostředkovatele vícefaktorového ověřování a vytvořit propojenou stejné klienta Azure AD. Alternativně, pokud jste zakoupili dostatek licencí MFA, Azure AD Premium nebo Enterprise Mobility + Security (EMS) k pokrytí všech uživatelů s povoleným vícefaktorovým ověřováním, můžete poskytovatele MFA odstranit úplně.

Pokud je zprostředkovatel vícefaktorového ověřování *není* propojené s klient služby Azure AD, nebo odkaz nového poskytovatele MFA do jiné služby Azure AD nejsou přenesená klienty, uživatelská nastavení a možnosti konfigurace. Je také potřeba znovu aktivovat stávající Azure MFA Servery pomocí přihlašovacích údajů pro aktivaci vygenerovaných prostřednictvím nového poskytovatele MFA. Opětovná aktivace MFA Serverů za účelem jejich propojení s novým poskytovatelem MFA nemá vliv na ověřování pomocí telefonního hovoru a textové zprávy, ale do opětovné aktivace mobilní aplikace přestanou pro všechny uživatele fungovat oznámení mobilní aplikace.

Další informace o poskytovatelích MFA v [Začínáme s poskytovatele Azure Multi-Factor Auth](concept-mfa-authprovider.md).

**Otázka: je možné Moje organizace přepínat mezi fakturace a předplatných (na základě licencí modelu) na základě spotřeby kdykoli?**

V některých případech Ano.

Pokud má adresáře *uživatelská* poskytovatele ověřování Azure Multi-Factor Authentication, můžete přidat licence MFA. Uživatelé s licencí nejsou v fakturace na základě spotřeby uživatelská zahrnuty. Uživatelé bez licence můžete stále povolené pro MFA prostřednictvím poskytovatele MFA. Pokud si zakoupíte a přiřazení licencí pro vaše uživatele nakonfigurována pro použití služby Multi-Factor Authentication, můžete odstranit poskytovatele ověřování Azure Multi-Factor Authentication. Pokud máte více uživatelů než licencí v budoucnu můžete kdykoli vytvořit jiného poskytovatele MFA podle uživatele.

Pokud má adresáře *za ověřování* poskytovatele ověřování Azure Multi-Factor Authentication, vždy fakturuje se pro každé ověření tak dlouho, dokud je zprostředkovatel vícefaktorového ověřování přidružený k vašemu předplatnému. Můžete přiřadit uživatelům licence MFA, ale stále platit budete pro každý požadavek dvoustupňové ověření, zda pochází od uživatele licence MFA přiřazené nebo ne.

**Otázka: Moje organizace má používat a synchronizace identit, které pomocí ověřování Azure Multi-Factor Authentication?**

Pokud vaše organizace používá model fakturace na základě spotřeby, Azure Active Directory je volitelné, ale nejsou vyžadována. Pokud váš poskytovatel MFA není propojený s klient služby Azure AD, lze nasadit pouze Server Azure Multi-Factor Authentication na místě.

Azure Active Directory není nutná pro model licence licence jsou přidány do klienta Azure AD při nákupu a přiřaďte je pro uživatele v adresáři.

## <a name="manage-and-support-user-accounts"></a>Spravovat a podporovat uživatelské účty

**Otázka: co říct Moji uživatelé dělat, když nemáte, obdrží odpověď na svůj telefon, nebo nemáte svůj telefon s nimi?**

Všichni uživatelé zpravidla nakonfigurovat více než jednu metodu ověřování. Řekněte jim, aby se zkusili znovu přihlásit, ale na přihlašovací stránce vybrali jinou metodu ověření.

Uživatelům může ukazovat [Průvodci odstraňováním potíží koncového uživatele](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-troubleshoot.md).


**Otázka: co je třeba udělat, pokud jeden z mých uživatelů nemůže získat v jejich účtu?**

Můžete resetovat účet uživatele tím, že je znovu projít proces registrace. Další informace o [Správa nastavení uživatelů a zařízení s Azure Multi-Factor Authentication v cloudu](howto-mfa-userdevicesettings.md).

**Otázka: co je třeba udělat, pokud jeden z mých uživatelů ztratí telefonního čísla, která používá hesla aplikací?**

Pokud chcete zabránit neoprávněným přístupem, odstraníte všechny uživatele hesla aplikací. Poté, co uživatel má náhradní zařízení, můžete znovu vytvořit hesla. Další informace o [Správa nastavení uživatelů a zařízení s Azure Multi-Factor Authentication v cloudu](howto-mfa-userdevicesettings.md).

**Otázka: Co když uživatel nemůžete se přihlásit k neprohlížečovým aplikacím?**

Pokud vaše organizace stále používá starší verze klientů a [povoleno použití hesel aplikací](howto-mfa-mfasettings.md#app-passwords), pak uživatelům nemůžete se přihlásit do těchto starších verzí klientů s uživatelského jména a hesla. Místo toho potřebují k [nastavit hesla aplikací](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-app-passwords.md). Uživatelé musí vymazat (odstranit) jejich přihlašovací údaje, restartujte aplikaci a pak se přihlaste pomocí svého uživatelského jména a *heslo aplikace* místo hesla pro regulární.

Pokud vaše organizace nemá starší verze klientů, by neměl umožní uživatelům vytvářet hesla aplikací.

> [!NOTE]
> Moderní ověřování pro klienty Office 2013
>
> Hesla aplikací jsou pouze nezbytné pro aplikace, které nepodporují moderní ověřování. Klienti Office 2013 podporovat protokoly moderní ověřování, ale nutné nakonfigurovat. Novějších klientů Office automaticky podporují protokoly moderní ověřování. Další informace najdete v tématu [oznámení veřejné preview moderní ověřování Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

**Otázka: Moji uživatelé prohlašují, že v některých případech nebudete přijímat textové zprávy, nebo jejich odpovědět na obousměrné textové zprávy, ale ověření vyprší časový limit.**

Doručování textové zprávy a obdržení odpovědi v obousměrné služby SMS se nezaručuje, protože nejsou k dispozici neovladatelném faktory, které mohou ovlivnit spolehlivost služby. Tyto faktory zahrnují země určení, operátora mobilní telefon a síla signálu.

Pokud se uživatelé často dochází k problémům s spolehlivě přijímat textové zprávy, sdělte jim, že místo toho použít metodu mobilní aplikace nebo telefonní hovor. Mobilní aplikace může přijímat oznámení jak přes mobilní služby a připojení Wi-Fi. Kromě toho mobilní aplikace může generovat ověřovací kódy, i v případě, že zařízení nemá žádné signál vůbec. Je k dispozici pro aplikaci Microsoft Authenticator [Android](http://go.microsoft.com/fwlink/?Linkid=825072), [IOS](http://go.microsoft.com/fwlink/?Linkid=825073), a [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

Pokud musíte použít textové zprávy, doporučujeme pomocí jednosměrné služby SMS, nikoli obousměrné služby SMS, pokud je to možné. Jednosměrné služby SMS je spolehlivější a uživatelům zabrání by docházelo k globální poplatky za služby SMS v odpovědi na textovou zprávu, která byla odeslána z jiné země.

**Otázka: je možné změnit dobu mé uživatelé budou muset před vypršením systému zadejte ověřovací kód z textové zprávy?**

V některých případech Ano. 

Pro jednosměrné služby SMS s Azure MFA serveru verze 7.0 nebo vyšší můžete nakonfigurovat časový limit nastavení podle nastavení klíče registru. Poté, co cloudové služby MFA odešle textovou zprávu, ověřovací kód (nebo jednorázové heslo) je vrácen do serveru MFA. MFA Server ukládá kód v paměti na 300 sekund ve výchozím nastavení. Pokud uživatel není zadat kód předané do 300 sekund, jejich ověřování byl odepřen. Tyto kroky použijte, chcete-li změnit výchozí nastavení časového limitu:

1. Přejděte na HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor.
2. Vytvořte klíč registru typu DWORD s názvem **pfsvc_pendingSmsTimeoutSeconds** a nastavte čas v sekundách, který má Azure MFA serveru k ukládání jednorázového hesla.

>[!TIP] 
>Pokud máte více serverů MFA, zná pouze jeden, který zpracovává požadavek na původní ověření ověřovací kód, který vám byl zaslán uživatele. Když uživatel zadá kód, musí se poslat požadavek na ověření ověřte ji na stejný server. Pokud se ověření kódu je odeslat na jiný server, je ověřování odepřeno. 

Pro obousměrné služby SMS s Azure MFA serveru můžete nakonfigurovat nastavení časového limitu v portálu pro správu MFA. Pokud uživatelé nejsou reagovat na serveru SMS během definovaného časového limitu, jejich ověřování byl odepřen. 

Pro jednosměrné služby SMS s Azure MFA v cloudu (včetně adaptér AD FS nebo rozšíření Network Policy Server) nelze nakonfigurovat nastavení časového limitu. Azure AD ukládá ověřovací kód po dobu 180 sekund. 

**Otázka: je možné použít hardwarové tokeny s Azure Multi-Factor Authentication Server?**

Pokud používáte Azure Multi-Factor Authentication Server, můžete import tokenů založené na čase, jednorázové heslo (TOTP) otevřete ověřování (OATH) třetí strany a potom je použít pro dvoustupňové ověření.

Můžete použít ActiveIdentity tokeny, které jsou tokeny OATH TOTP Pokud uložit tajný klíč v souboru CSV a importovat do Azure Multi-Factor Authentication Server. Můžete tokeny OATH s Active Directory Federation Services (ADFS), ověřování pomocí formulářů Internet Information Server (IIS) a RADIUS Remote Authentication Dial-In User Service (), tak dlouho, dokud klientský systém může přijmout vstup uživatele.

Můžete importovat tokeny OATH TOTP třetích stran s následujících formátů:  

- Kontejner přenosné symetrického klíče (PSKC)  
- Sdílený svazek clusteru, pokud soubor obsahuje sériové číslo, tajný klíč ve formátu Base 32 a časovém intervalu  

**Otázka: je možné použít Server Azure Multi-Factor Authentication pro zabezpečené Terminálové služby?**

Ano, ale pokud používáte Windows Server 2012 R2 nebo novější můžete pouze zabezpečené Terminálové služby pomocí Brána vzdálené plochy (Brána VP).

Změny zabezpečení ve Windows serveru 2012 R2 změnit způsob Azure Multi-Factor Authentication Server připojení k místní úřad zabezpečení (LSA) balíček zabezpečení v systému Windows Server 2012 a starší verze. Pro Terminálové služby v systému Windows Server 2012 nebo starší verze, můžete [zabezpečit aplikaci pomocí ověřování systému Windows](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Pokud používáte Windows Server 2012 R2, musíte služby Brána VP.

**Otázka: I MFA serveru nakonfigurovaná ID volajícího, ale Moji uživatelé stále přijímat volání služby Multi-Factor Authentication z anonymní volajícího.**

Při volání služby Multi-Factor Authentication jsou umístěny přes veřejnou telefonní síť, někdy směrování v poskytovatel, který nepodporuje ID volajícího. Z toho důvodu není zaručena ID volajícího, i když systém Multi-Factor Authentication je vždy odešle.

**Otázka: Proč Moji uživatelé se výzva k registraci jejich informace o zabezpečení?**
Tady je několik důvodů, může uživatelé vyzváni k registraci jejich informace o zabezpečení:

- Uživatel povolen pro MFA jim správce ve službě Azure AD, ale nemá informace o zabezpečení pro svůj účet ještě zaregistrována.
- Uživatele je povoleno pro samoobslužné služby ve službě Azure AD pro vytvoření nového hesla. Informace o zabezpečení je v budoucnu obnovit své heslo, pokud se někdy v budoucnu zapomněli pomůže.
- Uživatel získat přístup k aplikaci, která má zásady podmíněného přístupu vyžadovat vícefaktorové ověřování a nebyl dříve registrován pro MFA.
- Uživatel je registrace zařízení s Azure AD (včetně Azure AD Join) a vaše organizace vyžaduje vícefaktorové ověřování pro registraci zařízení, ale uživatel nebyl dříve registrován pro MFA.
- Uživatel je generování Windows Hello pro firmy v systému Windows 10 (který vyžaduje vícefaktorové ověřování) a nebyl dříve registrován pro MFA.
- Organizace má vytvořit a povolit zásady registrace MFA, která byla použita pro uživatele.
- Uživatel dříve zaregistrovaný pro MFA, ale vybrali metodu ověření, která od správce zakázal. Uživatel musí projít proto registrace MFA znovu a vyberte nový výchozí metoda ověření.


## <a name="errors"></a>Chyby
**Otázka: co uživatelé dělat, když se zobrazí chybová zpráva "žádosti o ověření není pro aktivovaný účet", při použití oznámení mobilní aplikace?**

Sdělte jim pomocí tohoto postupu k odebrání účtu z mobilní aplikace, a poté ji znovu přidejte:

1. Přejděte na [profilu Azure portálu](https://account.activedirectory.windowsazure.com/profile/) a přihlaste se pomocí účtu organizace.
2. Vyberte **dalšího ověření zabezpečení**.
3. Odeberte existující účet z mobilní aplikace.
4. Klikněte na tlačítko **konfigurace**a pak postupujte podle pokynů a změňte konfiguraci mobilní aplikace.

**Otázka: co by uživatelé dělat, pokud se zobrazí chybová zpráva 0x800434D4L při přihlášení do neprohlížečových aplikací?**

Při pokusu o přihlášení k neprohlížečové aplikace, nainstalována na místním počítači, který nelze použít s účty, které vyžadují dvoustupňové ověření, dojde k chybě 0x800434D4L.

Alternativní řešení pro tato chyba je mít samostatné uživatelské účty pro správce související a operace bez oprávnění správce. Později můžete se propojit poštovních schránek mezi účtu správce a účet bez oprávnění správce, aby mohli podepsat Outlooku pomocí účtu bez oprávnění správce. Další podrobnosti o tomto řešení, zjistěte, jak [poskytují možnost otevírat a zobrazovat obsah poštovní schránky uživatele správce](http://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Další postup
Pokud váš dotaz není zodpovězené, nechejte ji prosím v komentářích v dolní části stránky. Nebo, tady jsou některé další možnosti nápovědy:

* Hledání [znalostní báze podpory společnosti Microsoft](https://www.microsoft.com/en-us/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport) pro řešení pro běžné technické problémy.
* Vyhledejte a procházet technické dotazy a odpovědi od komunity nebo požádat vlastní dotaz [fóra Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* Pokud jste zákazník starší verze PhoneFactor a máte nějaké otázky nebo potřebujete pomoc, resetování hesla, použijte [resetování hesla](mailto:phonefactorsupport@microsoft.com) odkazu k otevření případu podpory.
* Obraťte se na pracovníky technické podpory prostřednictvím [podpory Azure Multi-Factor Authentication Server (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947). Při kontaktování nám, je užitečné, pokud zahrnete tolik informací o problému nejdříve. Obsahuje informace, které můžete zadat stránky, kde jste viděli chyba, kód chyby, ID konkrétní relace a ID uživatele, který viděli chyba.

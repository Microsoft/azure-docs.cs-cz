---
title: 'Azure AD Connect: archiv Historie vydání verze | Microsoft Docs'
description: V tomto článku jsou uvedené všechny archivované verze Azure AD Connect a Azure AD Sync
services: active-directory
author: billmath
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 07/23/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 859a4f199e65dd0c3aee9424029f6060683d5fbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836083"
---
# <a name="azure-ad-connect-version-release-history-archive"></a>Azure AD Connect: archiv Historie vydání verze

Tým Azure Active Directory (Azure AD) pravidelně aktualizuje Azure AD Connect s novými funkcemi a funkcemi. Ne všechny dodatky platí pro všechny cílové skupiny.

>[!NOTE] 
> Tento článek obsahuje referenční informace o verzi všech archivovaných verzí Azure AD – 1.3.20.0 a starší.  Aktuální verze najdete v části [Azure AD Connect Historie vydání verzí](reference-connect-version-history.md) .

## <a name="13200"></a>1.3.20.0 

### <a name="release-status"></a>Stav verze 

04/24/2019: vydáno ke stažení

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení 

- Přidání podpory pro aktualizaci domény 
- Funkce veřejné složky pošty Exchange bude GA 
- Zlepšení zpracování chyb v průvodci při selhání služby 
- Přidal se odkaz na upozornění na uživatelském rozhraní Synchronization Service Manager na stránce vlastností konektoru. 
- Funkce zpětného zápisu sjednocené skupiny je teď GA. 
- Vylepšená chybová zpráva SSPR v případě, že řadič domény nemá ovládací prvek LDAP 
- Přidání diagnostiky chyb registru DCOM během instalace  
- Vylepšené trasování chyb KOSMETICE RPC 
- Povolení přihlašovacích údajů EA z podřízené domény 
- Povoluje zadání názvu databáze při instalaci (výchozí název ADSync).
- Upgrade na ADAL 3.19.8 pro vyzvednutí WS-Trust opravy pro příkazy pro odeslání a přidání podpory pro nové instance Azure 
- Úprava pravidel synchronizace skupin pro tok samAccountName, DomainNetbios a DomainFQDN do cloudu pro deklarace identity 
- Upravení výchozího zpracování pravidla synchronizace – Další informace najdete [tady](how-to-connect-fix-default-rules.md).
- Byl přidán nový agent spuštěný jako služba systému Windows. Tento agent s názvem "Agent pro správu" umožňuje hlubší vzdálenou diagnostiku serveru Azure AD Connect, aby pomohl technikům Microsoftu řešit problémy při otevření případu podpory. Tento agent není nainstalován a povolen ve výchozím nastavení.  Další informace o tom, jak nainstalovat a povolit agenta, najdete v tématu [co je agent pro správu Azure AD Connect?](whatis-aadc-admin-agent.md). 
- Aktualizace licenční smlouvy s koncovým uživatelem (EULA) 
- Byla přidána podpora automatického upgradu pro nasazení, která jako typ přihlášení používá AD FS.  Tato operace také odebrala požadavek aktualizace AD FS vztahu důvěryhodnosti předávající strany Azure AD v rámci procesu upgradu. 
- Přidali jsme úkol správy důvěryhodnosti služby Azure AD, který poskytuje dvě možnosti: analyzovat/aktualizovat důvěryhodnost a resetovat vztah důvěryhodnosti. 
- Změnila se AD FS chování vztahu důvěryhodnosti předávající strany Azure AD tak, aby vždycky používala přepínač-SupportMultipleDomain (zahrnuje důvěryhodnost a aktualizace domény služby Azure AD). 
- Změna chování farmy instalace nové AD FS tak, aby vyžadovala certifikát. pfx odebráním možnosti použití předem nainstalovaného certifikátu.
- Pracovní postup instalace nové AD FS farmy byl aktualizován tak, aby umožňoval nasazení 1 AD FS a 1 serveru WAP.  Všechny další servery budou provedeny po počáteční instalaci. 

### <a name="fixed-issues"></a>Opravené problémy 

- Oprava logiky opětovného připojení SQL pro službu ADSync 
- Oprava pro povolení čisté instalace pomocí prázdné databáze SQL AOA 
- Opravte skript oprávnění PS, abyste mohli Upřesnit oprávnění GWB. 
- Oprava chyb VSS pomocí LocalDB  
- Oprava zavádějící chybové zprávy, když typ objektu není v oboru 
- Opravili jsme problém, kdy instalace Azure AD PowerShellu na server potenciálně způsobila konflikt sestavení s Azure AD Connect. 
- Opravená chyba KOSMETICE na přípravném serveru, když se aktualizují přihlašovací údaje konektoru v uživatelském rozhraní Synchronization Service Manager. 
- Opravili jsme nevracení paměti. 
- Různé opravy pro autoupgrade 
- Různé opravy pro export a nepotvrzené zpracování importu 
- Opravili jsme chybu při zpracování zpětného lomítka při filtrování domén a organizačních jednotek. 
- Opravili jsme problém, kdy se zastaví služba ADSync déle než 2 minuty, a způsobí problém v době upgradu. 




## <a name="12700"></a>1.2.70.0

### <a name="release-status"></a>Stav verze

12/18/2018: vydáno ke stažení

### <a name="fixed-issues"></a>Opravené problémy

Toto sestavení aktualizuje nestandardní konektory (například obecný konektor LDAP a obecný konektor SQL) dodávané s Azure AD Connect. Další informace o použitelných konektorech najdete v tématu verze 1.1.911.0 v [historii verzí konektoru](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history).


## <a name="12690"></a>1.2.69.0

### <a name="release-status"></a>Stav verze
12/11/2018: vydáno ke stažení

### <a name="fixed-issues"></a>Opravené problémy
Toto sestavení opravy hotfix umožňuje uživateli vybrat cílovou doménu v rámci zadané doménové struktury pro kontejner RegisteredDevices při povolování zpětného zápisu zařízení.  V předchozích verzích, které obsahují nové funkce možností zařízení (1.1.819.0 – 1.2.68.0), bylo umístění kontejneru RegisteredDevices omezené na kořen doménové struktury a nepovolilo podřízené domény.  Toto omezení se projevuje jenom při nových nasazeních – místní upgrady neovlivnily.  

Pokud se nějaké sestavení obsahující aktualizované funkce možností zařízení nasadilo na nový server a zpětný zápis zařízení byl povolený, budete muset ručně zadat umístění kontejneru, pokud ho nechcete v kořenovém adresáři doménové struktury.  K tomu je potřeba zakázat zpětný zápis zařízení a znovu ho povolit, což vám umožní určit umístění kontejneru na stránce "doménová struktura zpětného zápisu".



## <a name="12680"></a>1.2.68.0

### <a name="release-status"></a>Stav verze 

11/30/2018: vydáno ke stažení

### <a name="fixed-issues"></a>Opravené problémy

Toto sestavení opravy hotfix opravuje konflikt, ve kterém může dojít k chybě ověřování z důvodu nezávislé přítomnosti modulu MSOnline Galerie prostředí PowerShell na synchronizačním serveru.



## <a name="12670"></a>1.2.67.0

### <a name="release-status"></a>Stav verze 

11/19/2018: vydáno ke stažení

### <a name="fixed-issues"></a>Opravené problémy

Toto sestavení opravy hotfix opravuje regresi v předchozím buildu, kde při použití řadiče domény v systému Windows Server 2008/R2 dojde k chybě zpětného zápisu hesla.

## <a name="12650"></a>1.2.65.0 

### <a name="release-status"></a>Stav verze 

10/25/2018: vydáno ke stažení


### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení 


- Změnily se funkce zpětného zápisu atributu, aby bylo zajištěno, že hostovaný hlasová pošta funguje podle očekávání.  V některých případech služba Azure AD Přepisuje atribut msExchUcVoicemailSettings během zpětného zápisu s hodnotou null.  Pokud není nastavená hodnota cloudu, Azure AD už nebude mít k dispozici místní hodnotu tohoto atributu.
- Přidali jsme diagnostiku v průvodci Azure AD Connect k prozkoumání a identifikaci problémů s připojením ke službě Azure AD. Tyto stejné diagnostiky je možné spustit také přímo prostřednictvím PowerShellu pomocí rutiny Test-AdSyncAzureServiceConnectivity. 
- Přidali jsme diagnostiku v průvodci Azure AD Connect k prozkoumání a identifikaci problémů s připojením ke službě AD. Tato stejná diagnostika se dá spustit taky přímo prostřednictvím PowerShellu pomocí funkce Start-ConnectivityValidation v modulu PowerShellu pro ADConnectivityTools.  Další informace najdete v tématu [co je modul ADConnectivityTool PowerShellu?](how-to-connect-adconnectivitytools.md)
- Přidala se předběžná kontrolu verze schématu AD pro hybridní Azure Active Directory JOIN a zpětný zápis zařízení. 
- Bylo změněno hledání atributu stránky rozšíření adresáře, aby nedošlo k nerozlišování velkých a malých písmen.
-   Přidání plné podpory pro TLS 1,2. Tato verze podporuje všechny ostatní protokoly, které jsou zakázané, a na počítači, na kterém je nainstalovaná Azure AD Connect, se povolí jenom TLS 1,2.  Další informace najdete v tématu [vynucení TLS 1,2 pro Azure AD Connect](reference-connect-tls-enforcement.md)



### <a name="fixed-issues"></a>Opravené problémy   

- Opravili jsme chybu, kdy Azure AD Connect upgrade selže při použití SQL Always On. 
- Opravili jsme chybu pro správné analyzování názvů organizačních jednotek, které obsahují lomítko. 
- Opravili jsme problém, kdy je Pass-Through ověřování pro čistou instalaci v pracovním režimu zakázané. 
- Opravili jsme chybu, která zabránila načtení modulu PowerShellu při spuštění nástrojů pro řešení potíží. 
- Opravili jsme chybu, která by zablokovala zákazníkům použití číselných hodnot v prvním znaku názvu hostitele. 
- Opravila se chyba, kde Azure AD Connect by povolovala neplatné oddíly a výběr kontejneru. 
- Když je povolené jednotné přihlašování k ploše, opravili jsme chybovou zprávu "neplatné heslo". 
- Různé opravy chyb pro správu důvěryhodnosti AD FS  
- Při konfiguraci zpětného zápisu zařízení – opravili kontrolu schématu, aby vyhledala třídu objektu msDs-DeviceContainer (představená v WS2012 R2)


## <a name="118820"></a>1.1.882.0  

9/7/2018: vydané ke stažení, nebude vydávat verze pro automatický upgrade. 

### <a name="fixed-issues"></a>Opravené problémy  

Azure AD Connect upgrade se nepovede, pokud je pro ADSync DB nakonfigurovaná dostupnost SQL Always On. Tato oprava hotfix tento problém řeší a umožňuje úspěšné provedení upgradu. 

## <a name="118800"></a>1.1.880.0

### <a name="release-status"></a>Stav verze

8/21/2018: vydáno pro stažení a automatický upgrade. 

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

- Azure AD Connect federovat je teď dostupná pro obecnou dostupnost. [Přečtěte si další informace o federovaném Azure AD pomocí testu federovat](./plan-connect-user-signin.md#federation-with-pingfederate)
- Azure AD Connect teď vytvoří zálohu vztahu důvěryhodnosti služby Azure AD v AD FS pokaždé, když se provede aktualizace, a v případě potřeby ji uloží do samostatného souboru, aby se snadno obnovila. [Přečtěte si další informace o nových funkcích a správě důvěryhodnosti Azure AD v Azure AD Connect](./how-to-connect-azure-ad-trust.md).
- Nové nástroje pro řešení potíží pomáhají při odstraňování potíží se změnou primární e-mailové adresy a skrytím účtu z globálního seznamu adres.
- Azure AD Connect se aktualizovala tak, aby obsahovala nejnovější verzi SQL Server 2012 Native Client.
- Když přepnete přihlášení uživatele na synchronizaci hodnoty hash hesla nebo předávací ověřování v úloze Změna přihlašování uživatelů, je políčko bezproblémové jediné Sign-On ve výchozím nastavení povolené.
- Přidání podpory pro Windows Server Essentials 2019
- Agent Azure AD Connect Health se aktualizoval na nejnovější verzi 3.1.7.0.
- Pokud instalační program během upgradu detekuje změny výchozích pravidel synchronizace, před přepsáním upravených pravidel se zobrazí výzva s upozorněním. Uživatel tak bude moci provést opravné akce a později pokračovat v práci. Staré chování: Pokud existovalo nějaké upravené pravidlo, bude ruční upgrade přepsat tato pravidla, aniž by bylo nutné uživateli a plánovači synchronizace zakázali bez informování uživatele. Nové chování: uživateli se zobrazí výzva s upozorněním, než se upraví upravená pravidla synchronizace. Uživatel bude mít možnost zastavovat proces upgradu a pokračovat později po provedení nápravných akcí.
- Poskytněte lepší zpracování problému s dodržováním standardu FIPS a zajistěte chybovou zprávu pro generování hodnot hash MD5 v prostředí vyhovujícím standardu FIPS a odkaz na dokumentaci, která poskytuje pro tento problém alternativní řešení.
- Aktualizace uživatelského rozhraní pro zlepšení úloh federace v průvodci, které jsou nyní v rámci samostatné podskupiny pro federaci. 
- Všechny další úlohy federace se teď seskupují do jedné podnabídky pro snadné použití.
- Nový přepracované ADSyncConfig Posh modul (AdSyncConfig. psm1) s novými funkcemi oprávnění služby Active Directory přesunutými ze starého ADSyncPrep. psm1 (které se můžou v krátké době přestat používat)

### <a name="fixed-issues"></a>Opravené problémy 

- Opravili jsme chybu, kdy Azure AD Connect Server po upgradu na .NET 4.7.2 zobrazilo vysoké využití procesoru.
- Opravili jsme chybu, která by občas vytvořila chybovou zprávu pro automatický vyřešený problém se zablokování SQL.
- Opravili jsme několik problémů s přístupností pro Editor pravidel synchronizace a Service Manager synchronizace.  
- Opravena chyba, kdy Azure AD Connect nemůže získat informace o nastavení registru
- Opravili jsme chybu, která vytvořila problémy, když uživatel přejde do průvodce.
- Opravili jsme chybu, aby se zabránilo chybě v důsledku nesprávného zpracování více vláken v průvodci.
- Když na stránce filtrování synchronizace skupiny dojde k chybě LDAP při řešení skupin zabezpečení, Azure AD Connect nyní vrací výjimku s plnou věrností.  Hlavní příčina výjimky reference je stále neznámá a bude řešena jinou chybou.
-  Opravili jsme chybu, kdy nebyla správně nastavena oprávnění pro klíče STK a NGC (atribut ms-DS-KeyCredentialLink u objektů uživatele nebo zařízení pro WHfB).     
- Opravila se chyba, kde ' set-ADSyncRestrictedPermissions ' nebyl správně volán.
-  Přidání podpory pro udělení oprávnění pro zpětný zápis skupin v Průvodci instalací služby Azure ADConnect
- Při změně metody přihlašování z synchronizace hodnot hash hesla na AD FS se synchronizace hodnot hash hesel neaktivovala.
- Přidání ověření pro IPv6 adresy v konfiguraci AD FS
- Aktualizace zprávy oznámení, aby se informovalo, že existuje existující konfigurace.
- Zpětný zápis zařízení nemůže detekovat kontejner v nedůvěryhodné doménové struktuře. Aktualizovali jsme to tak, aby poskytovalo lepší chybovou zprávu a odkaz na příslušnou dokumentaci.
- Když oddělíte organizační jednotku a pak na synchronizaci/zpětný zápis odpovídající této organizační jednotce dojde k obecné chybě synchronizace. Tato změna se změnila tak, aby vytvořila podrobnější chybovou zprávu.

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>Stav verze

5/14/2018: vydaná pro automatický upgrade a stažení.

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

Nové funkce a vylepšení

- Tato verze zahrnuje veřejnou verzi Preview integrace PingFederate ve službě Azure AD Connect. V této verzi můžou zákazníci snadno a spolehlivě konfigurovat své Azure Active Directory prostředí a využívat PingFederate jako poskytovatele federace. Další informace o tom, jak používat tuto novou funkci, najdete v naší [online dokumentaci](plan-connect-user-signin.md#federation-with-pingfederate). 
- Aktualizovali jsme nástroj Azure AD Connect Průvodce odstraňováním potíží, kde teď analyzuje více chybových scénářů, jako jsou propojené poštovní schránky a dynamické skupiny AD. Další informace o nástroji pro řešení potíží [najdete tady](tshoot-connect-objectsync.md).
- Konfigurace zpětného zápisu zařízení je teď spravovaná výhradně v rámci průvodce Azure AD Connect.
- Přidá se nový modul PowerShellu s názvem ADSyncTools. psm1, který se dá použít k řešení problémů s připojením SQL a k různým dalším nástrojům pro řešení potíží. Přečtěte si další informace o [modulu ADSyncTools](tshoot-connect-tshoot-sql-connectivity.md). 
- Přidá se nový další úkol "Konfigurace možností zařízení". Tuto úlohu můžete použít ke konfiguraci následujících dvou operací: 
  - **Připojení k hybridní službě Azure AD**: v případě, že vaše prostředí obsahuje místní služby AD a máte výhod výhod funkcí poskytovaných Azure Active Directory, můžete implementovat zařízení připojená k hybridní službě Azure AD. Jedná se o zařízení připojená k místní službě Active Directory i k Azure Active Directory.
  - **Zpětný zápis zařízení**: zpětný zápis zařízení umožňuje povolit podmíněný přístup na základě zařízení, aby se AD FS (2012 R2 nebo novější) chráněná zařízení.

    >[!NOTE] 
    > - Možnost Povolit zpětný zápis zařízení z přizpůsobení možností synchronizace bude šedá. 
    > -  V této verzi je modul PowerShell pro nástroj ADPrep zastaralý.


### <a name="fixed-issues"></a>Opravené problémy 

- Tato verze aktualizuje SQL Server Express instalaci na SQL Server 2012 SP4, což mimo jiné poskytuje opravy pro několik chyb zabezpečení.  Další informace o SQL Server 2012 SP4 najdete [tady](https://support.microsoft.com/help/4018073/sql-server-2012-service-pack-4-release-information) .
- Zpracování pravidla synchronizace: pravidla synchronizace odchozího připojení bez podmínky spojení by se neměla použít, pokud pravidlo nadřazené synchronizace už neplatí.
- V uživatelském rozhraní Synchronization Service Manager a editoru pravidel synchronizace bylo použito několik oprav usnadnění.
- Azure AD Connect Průvodce: Chyba při vytváření účtu konektoru služby AD, když je Azure AD Connect v pracovní skupině
- Průvodce Azure AD Connect: na přihlašovací stránce služby Azure AD se zobrazí zaškrtávací políčko ověřování vždy, když dojde k neshodě domén AD a ověřených domén Azure AD.
- Automatické upgrade opravy PowerShellu pro správné nastavení stavu automatického upgradu v některých případech po pokusu o automatický upgrade
- Průvodce Azure AD Connect: aktualizovaná telemetrie pro zachycení dříve chybějících informací
- Průvodce Azure AD Connect: při použití úlohy **změnit přihlašování uživatele** k přepnutí z AD FS na předávací ověřování došlo k následujícím změnám:
    - Agent předávacího ověřování je nainstalovaný na serveru Azure AD Connect a funkce předávacího ověřování je povolená, než převedeme domény z federované na spravovanou.
    - Uživatelé už nejsou převedení z federovaných na spravované. Jsou převedeny pouze domény.
- Průvodce Azure AD Connect: AD FS více doménového regulárního výrazu není správné, pokud má hlavní název uživatele (UPN) možnost aktualizace speciálního regulárního znaku pro podporu speciálních znaků.
- Průvodce Azure AD Connect: odebrat zprávu spurious "konfigurovat atribut zdrojového ukotvení", pokud se žádná změna nemění 
- Průvodce Azure AD Connect: AD FS podporu pro scénář dvojí federace
- Průvodce Azure AD Connect: deklarace AD FS se při převodu spravované domény na federované neaktualizují pro přidanou doménu.
- Průvodce Azure AD Connect: během zjišťování nainstalovaných balíčků jsme našli zastaralé produkty v DirSync/Azure AD Sync/Azure AD Connect. Nyní se pokusíme odinstalovat zastaralé produkty.
- Průvodce Azure AD Connect: správné mapování chybové zprávy, když se nepodaří nainstalovat ověřovacího agenta pro předávání
- Průvodce Azure AD Connect: byl odebrán kontejner konfigurace ze stránky filtrování OJ domény.
- Instalace synchronizačního modulu: Odeberte nepotřebnou starší verzi logiky, která se občas nezdařila z synchronizačního modulu
- Průvodce Azure AD Connect: Opravte text místní nabídky na stránce volitelné funkce pro synchronizaci hodnot hash hesel.
- Modul runtime synchronizace: Opravte scénář, ve kterém má objekt CS importovaná pravidla odstranění a synchronizace, která se pokusí o opětovné zřízení objektu.
- Modul runtime synchronizace: Přidání odkazu nápovědy pro Průvodce odstraňováním potíží s online připojením do protokolu událostí pro chybu importu
- Modul runtime synchronizace: snížení využití paměti plánovačem synchronizace při vytváření výčtu konektorů
- Průvodce Azure AD Connect: Opravte problém vyřešením vlastního účtu synchronizační služby, který nemá žádná oprávnění ke čtení AD.
- Průvodce Azure AD Connect: vylepšení protokolování možností filtrování domén a organizačních jednotek
- Průvodce Azure AD Connect: AD FS Přidání výchozích deklarací identity ke federačnímu vztahu důvěryhodnosti vytvořenému pro scénář MFA
- Průvodce Azure AD Connect: AD FS nasazení WAP: Přidání serveru se nepodařilo použít nový certifikát.
- Průvodce Azure AD Connect: výjimka DSSO, pokud onPremCredentials není inicializován pro doménu 
- Preferenční tok atributu rozlišujícího služby AD z aktivního objektu uživatele.
- Opravili jsme chybu typu kosmetickí, ale priorita prvního pravidla synchronizace OOB byla nastavená na 99 namísto 100.



## <a name="117510"></a>1.1.751.0
Stav 4/12/2018: vydáno pouze ke stažení

>[!NOTE]
>Tato verze je opravou hotfix pro Azure AD Connect
### <a name="azure-ad-connect-sync"></a>Synchronizace služby Azure AD Connect
#### <a name="fixed-issues"></a>Opravené problémy
Opravili jsme problém, ale automatické zjišťování instancí Azure pro klienty v Číně občas selhává.  

### <a name="ad-fs-management"></a>Správa AD FS
#### <a name="fixed-issues"></a>Opravené problémy

Došlo k problému s logikou opakování konfigurace, která by způsobila, že došlo k chybě ArgumentException informující o tom, že položka se stejným klíčem již byla přidána. "  To by způsobilo selhání všech operací opakování.

## <a name="117500"></a>1.1.750.0
Stav 3/22/2018: vydaná pro automatický upgrade a stažení.
>[!NOTE]
>Až se upgrade na tuto novou verzi dokončí, automaticky aktivuje úplnou synchronizaci a úplný Import pro konektor Azure AD a úplnou synchronizaci pro konektor služby Active Directory. Vzhledem k tomu, že to může nějakou dobu trvat, v závislosti na velikosti Azure AD Connectho prostředí se ujistěte, že jste provedli potřebné kroky pro podporu tohoto nebo na upgradování, dokud nezjistíte vhodný okamžik.
>[!NOTE]
>Funkce autoupgrade byla pro některé klienty, kteří nasadila buildy později než 1.1.524.0, nesprávně zakázaná. Abyste měli jistotu, že je vaše instance Azure AD Connect i nadále oprávněná k automatického upgradu, spusťte následující rutinu PowerShellu: set-ADSyncAutoUpgrade-AutoupGradeState Enabled.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Opravené problémy

* Rutina Set-ADSyncAutoUpgrade by dřív blokovala automatický upgrade, pokud je stav automatického upgradu nastavený na pozastaveno. Tato funkce se teď změnila tak, aby neblokovala autoinovaci budoucích buildů.
* Byla změněna možnost **přihlašovací stránky uživatele** "synchronizace hesel" na "synchronizace hodnot hash hesel".  Azure AD Connect synchronizuje hodnoty hash hesel, nikoli hesla, takže se zarovnává s tím, co se skutečně objevuje.  Další informace najdete v tématu [implementace synchronizace hodnot hash hesel pomocí Azure AD Connect synchronizace](how-to-connect-password-hash-synchronization.md) .

## <a name="117490"></a>1.1.749.0
Stav: vydáno pro výběr zákazníků

>[!NOTE]
>Až se upgrade na tuto novou verzi dokončí, automaticky aktivuje úplnou synchronizaci a úplný Import pro konektor Azure AD a úplnou synchronizaci pro konektor služby Active Directory. Vzhledem k tomu, že to může nějakou dobu trvat, v závislosti na velikosti Azure AD Connectho prostředí se ujistěte, že jste provedli potřebné kroky pro podporu tohoto nebo na upgradování, dokud nezjistíte vhodný okamžik.
### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Opravené problémy
* Oprava okna časování na úkolech na pozadí pro stránku filtrování oddílů při přechodu na další stránku

* Opravili jsme chybu, která způsobila porušení přístupu během vlastní akce ConfigDB.

* Opravili jsme chybu pro obnovení z časového limitu připojení SQL.

* Opravili jsme chybu, kdy se v certifikátech se zástupnými znaky sítě SAN nezdařila kontrola požadavků.

* Opravili jsme chybu, která způsobí, že miiserver.exe při exportu konektoru Azure AD dojde k chybě.

* Opravili jsme chybu, která při spuštění Průvodce Azure AD Connect pro změnu konfigurace pokusy o chybné heslo přihlásily k řadiči domény.


#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* Přidání nastavení ochrany osobních údajů pro Obecné nařízení o ochraně osobních údajů (GDPR).  Další informace najdete [v článku.](reference-connect-user-privacy.md)

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* Telemetrie aplikací – Správce může zapnout nebo vypnout tuto třídu dat na adrese.

* Data služby Azure AD Health – správce musí navštívit portál stavu, aby mohl řídit jeho nastavení stavu.
   Po změně zásady služby ji budou agenti číst a vymáhat.

* Pro inicializaci stránky byly přidány akce konfigurace se zpětným zápisem zařízení a indikátor průběhu.

* Vylepšená Obecná diagnostika se sestavou HTML a úplným shromažďováním dat v ZIP-Text/HTML

* Zvýšila se spolehlivost automatického upgradu a přidala se další telemetrie, která zajistí, že se dá určit stav serveru.

* Omezení oprávnění dostupných pro privilegované účty na účtu konektoru služby AD

  * Pro nové instalace Průvodce omezí oprávnění, která privilegované účty mají na účtu MSOL po vytvoření účtu MSOL.

Změny se postará o tyto změny:
1. Expresní instalace
2. Vlastní instalace s automatickým vytvořením účtu
3. Změnil instalační program, aby při čisté instalaci Azure AD Connect nevyžadoval oprávnění správce.

* Přidání nového nástroje pro řešení potíží se synchronizací pro určitý objekt. Je k dispozici v části řešení potíží s synchronizací objektů Azure AD Connect Průvodce při řešení potíží s další úlohou. V současné době nástroj kontroluje následující:

  * Hodnota UserPrincipalName nesouhlasí mezi synchronizovaným objektem uživatele a uživatelským účtem v Tenantovi Azure AD.
  * Pokud je objekt filtrován z synchronizace z důvodu filtrování domény
  * Pokud je objekt filtrován z synchronizace kvůli filtrování organizační jednotky (OU)

* Byl přidán nový nástroj, který synchronizuje aktuální hodnotu hash hesla uloženou v místní službě Active Directory pro konkrétní uživatelský účet.

Nástroj nevyžaduje změnu hesla. Je k dispozici v části řešení potíží se synchronizací hodnot hash hesel Azure AD Connect Průvodce při řešení potíží s další úlohou.






## <a name="116540"></a>1.1.654.0
Stav: 12. prosince 2017

>[!NOTE]
>Tato verze je opravou hotfix související se zabezpečením pro Azure AD Connect
### <a name="azure-ad-connect"></a>Azure AD Connect
Do Azure AD Connect verze 1.1.654.0 (a After) bylo přidáno vylepšení, aby se zajistilo, že se doporučené změny oprávnění popsané v části [uzamčení přístupu k účtu služba AD DS](#lock) automaticky použije, když Azure AD Connect vytvoří účet služba AD DS. 

- Při nastavování Azure AD Connect může správce instalace poskytnout buď existující účet služba AD DS, nebo nechat Azure AD Connect automaticky vytvořit účet. Změny oprávnění se automaticky aplikují na účet služba AD DS, který se vytvoří pomocí Azure AD Connect během instalace. Nejsou aplikovány na existující účet služba AD DS poskytovaný správcem instalace.
- Pro zákazníky, kteří provedli upgrade ze starší verze Azure AD Connect na 1.1.654.0 (nebo po ní), se změny oprávnění nepoužijí na stávající účty služba AD DS vytvořené před upgradem. Použijí se jenom pro nové účty služba AD DS vytvořené po upgradu. K tomu dochází, když přidáváte nové doménové struktury služby Active Directory, které se budou synchronizovat do Azure AD.

>[!NOTE]
>Tato verze odstraní pouze ohrožení zabezpečení pro nové instalace Azure AD Connect, kde je účet služby vytvořen procesem instalace. V případě existujících instalací nebo v případech, kdy účet poskytnete sami, je nutné zajistit, že tato chyba zabezpečení neexistuje.
#### <a name="lock-down-access-to-the-ad-ds-account"></a><a name="lock"></a> Uzamknout přístup k účtu služba AD DS
Pomocí následujících změn oprávnění v místní službě AD Zablokujte přístup k účtu služba AD DS.  

*   Zakázat dědění pro zadaný objekt
*   Odebere všechny položky ACE u konkrétního objektu s výjimkou položek ACE specifických pro sebe. Chceme, aby výchozí oprávnění zůstala beze změny, když se dostane do sebe.
*   Přiřaďte tato konkrétní oprávnění:

Typ     | Name                          | Access               | Platí pro
---------|-------------------------------|----------------------|--------------|
Povolit    | SYSTEM                        | Úplné řízení         | Tento objekt  |
Povolit    | Enterprise Admins             | Úplné řízení         | Tento objekt  |
Povolit    | Domain Admins                 | Úplné řízení         | Tento objekt  |
Povolit    | Administrators                | Úplné řízení         | Tento objekt  |
Povolit    | Podnikové řadiče domény | Vypsat obsah        | Tento objekt  |
Povolit    | Podnikové řadiče domény | Číst všechny vlastnosti  | Tento objekt  |
Povolit    | Podnikové řadiče domény | Oprávnění ke čtení     | Tento objekt  |
Povolit    | Authenticated Users           | Vypsat obsah        | Tento objekt  |
Povolit    | Authenticated Users           | Číst všechny vlastnosti  | Tento objekt  |
Povolit    | Authenticated Users           | Oprávnění ke čtení     | Tento objekt  |

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>Skript PowerShellu pro zpřísnění existujícího účtu služby

Pokud chcete použít skript PowerShellu, použijte toto nastavení na stávající účet služba AD DS (Ether poskytovaný vaší organizací nebo vytvořenou předchozí instalací Azure AD Connect, Stáhněte si prosím skript z výše uvedeného odkazu.

##### <a name="usage"></a>Použití:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Kde 

**$ObjectDN** = účet služby Active Directory, jehož oprávnění je nutné zvýšit.

**$Credential** = přihlašovací údaje správce, které mají nezbytná oprávnění k omezení oprávnění pro účet $ObjectDN. Tato oprávnění obvykle uchovává správce domény nebo organizace. Použijte plně kvalifikovaný název domény účtu správce, aby nedocházelo k chybám při vyhledávání účtů. Příklad: contoso. com\admin.

>[!NOTE] 
>$credential. Uživatelské jméno by mělo být ve formátu FQDN\username. Příklad: contoso. com\admin 
##### <a name="example"></a>Příklad:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>Byla tato chyba zabezpečení použita k získání neoprávněného přístupu?

Pokud chcete zjistit, jestli se tato chyba zabezpečení použila k ohrožení konfigurace Azure AD Connect, měli byste ověřit poslední datum resetování hesla účtu služby.  Pokud má být pro tuto událost resetování hesla provedena časová razítka v neočekávaném a dalším zkoumání, je třeba provést další šetření prostřednictvím protokolu událostí.

Další informace najdete v článku [informační zpravodaj zabezpečení společnosti Microsoft 4056318](/security-updates/securityadvisories/2017/4056318)

## <a name="116490"></a>1.1.649.0
Stav: Říjen 27 2017

>[!NOTE]
>Toto sestavení není pro zákazníky k dispozici prostřednictvím funkce Azure AD Connect automatické aktualizace.
### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Oprava potíží 
* Opravili jsme problém s kompatibilitou verzí mezi Azure AD Connect a agentem Azure AD Connect Health (pro synchronizaci). Tento problém má vliv na zákazníky, kteří provádějí Azure AD Connect místního upgradu na verzi 1.1.647.0, ale v současné době má agenta Health verze 3.0.127.0. Po upgradu již Agent stavu nebude moci odesílat data o stavu o službě Azure AD Connect Synchronization Service do služby Azure AD Health Service. S touto opravou se Agent stavu 3.0.129.0 nainstaluje během Azure AD Connect místního upgradu. Agent stavu verze 3.0.129.0 nemá problémy s kompatibilitou s Azure AD Connect verzí 1.1.649.0.


## <a name="116470"></a>1.1.647.0
Stav: říjen 19 2017

> [!IMPORTANT]
> Došlo k známému problému s kompatibilitou mezi Azure AD Connect verzí 1.1.647.0 a agentem Azure AD Connect Health (pro synchronizaci) verze 3.0.127.0. Tento problém zabraňuje agentovi stavu v posílání dat o stavu služby Azure AD Connect Synchronization Service (včetně chyb synchronizace objektů a dat historie spouštění) do služby Azure AD Health Service. Před ručním upgradem Azure AD Connect nasazení na verzi 1.1.647.0 Ověřte prosím aktuální verzi agenta Azure AD Connect Health nainstalovaného na serveru Azure AD Connect. Můžete to udělat tak, že v *Ovládacích panelech spustíte ovládací panely → Přidat odebrat programy* a vyhledáte aplikace *Microsoft Azure AD připojit agenta stavu pro synchronizaci*. Pokud je jeho verze 3.0.127.0, doporučujeme počkat, až bude k dispozici další verze Azure AD Connect před upgradem. Pokud není verze agenta stavu 3.0.127.0, je dobré pokračovat v ručním upgradu. Upozorňujeme, že tento problém nemá vliv na upgrade nebo zákazníky, kteří provádějí novou instalaci Azure AD Connect.
>
>
### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Opravené problémy
* Opravili jsme problém s *přihlašovacím úkolem změny uživatele* v Azure AD Connect Průvodce:

  * K tomuto problému dochází, když máte existující nasazení Azure AD Connect se **zapnutou** synchronizací hesel a pokoušíte se nastavit metodu přihlašování uživatelů jako *předávací ověřování*. Před použitím změny se v průvodci nesprávně zobrazí výzva "*zakázat synchronizaci hesla*". Synchronizace hesel ale zůstane zapnutá i po použití změny. V této opravě už průvodce nezobrazuje výzvu.

  * V rámci návrhu průvodce nevypne synchronizaci hesla, když aktualizujete metodu přihlašování uživatelů pomocí úlohy změny přihlašování *uživatele* . Je to zabránit tomu, aby zákazníci, kteří chtějí uchovat synchronizaci hesel, nenarušili, i když povolují předávací ověřování nebo federaci jako svoji primární metodu přihlašování uživatele.

  * Pokud chcete po aktualizaci metody přihlašování uživatele zakázat synchronizaci hesel, je nutné spustit úlohu *přizpůsobení konfigurace synchronizace* v průvodci. Když přejdete na stránku *volitelné funkce* , zrušte zaškrtnuté políčko *Synchronizace hesel* .

  * Pamatujte na to, že pokud se pokusíte povolit nebo zakázat bezproblémové jednotné přihlašování, nastane stejný problém taky. Konkrétně máte k dispozici existující nasazení Azure AD Connect se zapnutou synchronizací hesel a metoda přihlašování uživatele je už nakonfigurovaná jako *předávací ověřování*. Pomocí úlohy *změnit přihlašování uživatele* se pokusíte zaškrtnout nebo zrušit kontrolu možnosti *Povolit bezproblémové používání jednotného přihlašování* , zatímco metoda přihlašování uživatele zůstává nakonfigurovaná jako předávací ověřování. Před použitím změny se v průvodci nesprávně zobrazí výzva "*zakázat synchronizaci hesla*". Synchronizace hesel ale zůstane zapnutá i po použití změny. V této opravě už průvodce nezobrazuje výzvu.

* Opravili jsme problém s *přihlašovacím úkolem změny uživatele* v Azure AD Connect Průvodce:

  * K tomuto problému dochází, když máte existující nasazení Azure AD Connect se **zakázanou** synchronizací hesel a pokoušíte se nastavit metodu přihlašování uživatelů jako *předávací ověřování*. Při použití změny Průvodce povolí předávací ověřování i synchronizaci hesel. V této opravě už průvodce neumožňuje synchronizaci hesel.

  * Dřív byla synchronizace hesel předpokladem pro povolení předávacího ověřování. Když nastavíte metodu přihlašování uživatele jako *předávací ověřování*, průvodce povolí jak předávací ověřování, tak i synchronizaci hesel. Nedávno se synchronizace hesel odstranila jako požadavek. V rámci Azure AD Connect verze 1.1.557.0 byla provedena změna Azure AD Connect, aby nepovolovala synchronizaci hesel při nastavení metody přihlašování uživatelů jako *předávacího ověřování*. Tato změna se ale použila jenom pro Azure AD Connect instalaci. V této opravě se u *přihlašovací úlohy změny uživatele* použije taky stejná změna.

  * Pamatujte na to, že pokud se pokusíte povolit nebo zakázat bezproblémové jednotné přihlašování, nastane stejný problém taky. Konkrétně jste měli stávající nasazení Azure AD Connect se zakázanou synchronizací hesel a metoda přihlašování uživatelů je už nakonfigurovaná jako *předávací ověřování*. Pomocí úlohy *změnit přihlašování uživatele* se pokusíte zaškrtnout nebo zrušit kontrolu možnosti *Povolit bezproblémové používání jednotného přihlašování* , zatímco metoda přihlašování uživatele zůstává nakonfigurovaná jako předávací ověřování. Při použití změny Průvodce povolí synchronizaci hesel. V této opravě už průvodce neumožňuje synchronizaci hesel. 

* Opravili jsme problém, který způsobil, že Azure AD Connect upgrade selže, a dojde k chybě "*nepovedlo se upgradovat synchronizační službu*". Synchronizační služba již nadále nemůže začínat chybou události "*službu nebylo možné spustit, protože verze databáze je novější než verze nainstalovaná v binárních souborech*". K tomuto problému dochází, pokud správce, který provádí upgrade, nemá oprávnění správce systému pro SQL Server, který používá Azure AD Connect. Tato oprava Azure AD Connect vyžaduje, aby správce měl během upgradu db_owner oprávnění k databázi ADSync.

* Opravili jsme problém s upgradem Azure AD Connect, který ovlivnil zákazníky, kteří mají povolené [bezproblémové jednotné přihlašování](./how-to-connect-sso.md). Po upgradu Azure AD Connect se bezproblémové jednoduché Sign-On v průvodci Azure AD Connect jeví jako zakázané, a to i v případě, že funkce zůstane zapnutá a plně funkční. V této opravě se tato funkce teď v průvodci zobrazí správně, jak je zapnutá.

* Opravili jsme problém, který způsobil, že Průvodce Azure AD Connect, aby na stránce *připraveno ke konfiguraci* vždycky zobrazoval výzvu "*konfigurace zdrojového ukotvení*", i když se neudělaly žádné změny související se zdrojovým ukotvením.

* Při ručním upgradu Azure AD Connect se zákazník musí zadat přihlašovací údaje globálního správce odpovídajícího tenanta Azure AD. Předchozí upgrade může pokračovat i v případě, že přihlašovací údaje globálního správce patří do jiného tenanta Azure AD. I když se upgrade jeví k úspěšnému dokončení, některé konfigurace se při upgradu správně neuloží. V této změně Průvodce brání v pokračování upgradu, pokud zadané přihlašovací údaje neodpovídají tenantovi Azure AD.

* Byla odebrána redundantní logika, která zbytečně restartovala Azure AD Connect Health službu na začátku ručního upgradu.


#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Přidání logiky pro zjednodušení kroků požadovaných k nastavení Azure AD Connect s Microsoft německým cloudem Dříve se vyžaduje, abyste na Azure AD Connect serveru aktualizovali určité klíče registru, aby správně fungovaly s Microsoft Německo cloudem, jak je popsáno v tomto článku. Nyní Azure AD Connect může automaticky zjistit, jestli je tenant v cloudu Microsoft Německo, na základě přihlašovacích údajů globálního správce, které jste zadali během instalace.

### <a name="azure-ad-connect-sync"></a>Synchronizace služby Azure AD Connect
> [!NOTE]
> Poznámka: synchronizační služba má rozhraní WMI, které umožňuje vyvíjet vlastní Plánovač. Toto rozhraní je nyní zastaralé a bude odebráno z budoucích verzí Azure AD Connect dodaných po 30. června 2018. Zákazníci, kteří chtějí přizpůsobit plán synchronizace, by měli používat [integrovaný Plánovač](./how-to-connect-sync-feature-scheduler.md).
#### <a name="fixed-issues"></a>Opravené problémy
* Když průvodce Azure AD Connect vytvoří účet konektoru služby AD potřebný k synchronizaci změn z místní služby Active Directory, účet k tomu nesprávně přiřadí oprávnění potřebné ke čtení objektů PublicFolder. Tento problém má vliv na expresní instalaci i na vlastní instalaci. Tato změna opravuje problém.

* Opravili jsme problém, který způsobil, že se stránka Poradce při potížích s průvodcem Azure AD Connect nesprávně vygenerovala pro správce se systémem Windows Server 2016.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Při odstraňování potíží se synchronizací hesel pomocí Průvodce Azure AD Connect stránka Poradce při potížích teď vrací stav specifický pro doménu.

* Pokud jste dřív zkusili povolit synchronizaci hodnot hash hesel, Azure AD Connect neověřuje, jestli má účet služby AD Connector potřebná oprávnění k synchronizaci hodnot hash hesel z místní služby AD. Nyní Průvodce Azure AD Connect ověří a upozorní vás, pokud účet konektoru služby AD nemá dostatečná oprávnění.

### <a name="ad-fs-management"></a>Správa AD FS
#### <a name="fixed-issue"></a>Oprava potíží 
* Opravili jsme problém týkající se použití funkce [MS-DS-ConsistencyGuid jako zdrojového ukotvení](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) . Tento problém má vliv na zákazníky, kteří mají nakonfigurovanou *federaci s AD FS* jako způsob přihlašování uživatele. Když v průvodci vykonáte úlohu *konfigurace zdrojového ukotvení* , Azure AD Connect se pro immutableId přepnou pomocí atributu source * ms-DS-ConsistencyGuid as source. V rámci této změny se Azure AD Connect pokusí aktualizovat pravidla deklarace identity pro ImmutableId v AD FS. Tento krok se ale nezdařil, protože Azure AD Connect neměl oprávnění správce, která jsou nutná ke konfiguraci AD FS. Tato oprava Azure AD Connect nyní vyzve k zadání přihlašovacích údajů správce pro AD FS při spuštění úlohy *konfigurace zdrojového ukotvení* .



## <a name="116140"></a>1.1.614.0
Stav: září 05 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Známé problémy
* Došlo k známému problému, který způsobuje, že Azure AD Connect upgrade selže a dojde k chybě "*nepovedlo se upgradovat synchronizační službu*". Synchronizační služba již nadále nemůže začínat chybou události "*službu nebylo možné spustit, protože verze databáze je novější než verze nainstalovaná v binárních souborech*". K tomuto problému dochází, pokud správce, který provádí upgrade, nemá oprávnění správce systému pro SQL Server, který používá Azure AD Connect. Oprávnění dbo nejsou dostatečná.

* Došlo k známému problému s Azure AD Connect upgradem, který má vliv na zákazníky, kteří mají povolené [bezproblémové jednotné přihlašování](how-to-connect-sso.md). Po upgradu Azure AD Connect se tato funkce v průvodci zobrazí jako zakázaná, i když tato funkce zůstane zapnutá. Oprava tohoto problému bude k dispozici v budoucí verzi. Zákazníci, kteří mají obavy z tohoto problému, ho můžou ručně opravit tak, že v průvodci povolí bezproblémové jednoduché Sign-On.

#### <a name="fixed-issues"></a>Opravené problémy
* Opravili jsme problém, který zabránil Azure AD Connect aktualizaci pravidel deklarací v místních AD FS při povolování funkce [MS-DS-ConsistencyGuid jako zdrojového ukotvení](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) . K tomuto problému dochází, pokud se pokusíte povolit funkci pro existující nasazení Azure AD Connect, které má AD FS nakonfigurován jako metoda přihlašování. K tomuto problému dochází, protože průvodce nezobrazuje výzvu k zadání přihlašovacích údajů služby ADFS předtím, než se pokusí aktualizovat pravidla deklarace identity v AD FS.
* Opravili jsme problém, který způsobil, že Azure AD Connect neúspěšná instalace, pokud má místní doménová struktura AD zakázaný protokol NTLM. Příčinou tohoto problému je Azure AD Connect, že Průvodce při vytváření kontextů zabezpečení vyžadovaných pro ověřování pomocí protokolu Kerberos neposkytuje plně kvalifikované přihlašovací údaje. Tím dojde k selhání ověřování protokolem Kerberos a Azure AD Connect průvodce, aby se vrátil k použití protokolu NTLM.

### <a name="azure-ad-connect-sync"></a>Synchronizace služby Azure AD Connect
#### <a name="fixed-issues"></a>Opravené problémy
* Opravili jsme problém, kdy se nedá vytvořit nové synchronizační pravidlo, pokud se nezadá atribut značky.
* Opravili jsme problém, který způsobil Azure AD Connect připojení k místní službě AD za účelem synchronizace hesel pomocí protokolu NTLM, a to i v případě, že je protokol Kerberos k dispozici. K tomuto problému dochází, pokud místní topologie služby AD má jeden nebo více řadičů domény, které byly obnoveny ze zálohy.
* Opravili jsme problém, který způsobil úplnou synchronizaci kroků po upgradu. Obecně platí, že po upgradu se po upgradu vyžadují spuštěné úplné synchronizace, pokud dojde ke změnám v dodaných pravidlech synchronizace. K tomuto problému došlo z důvodu chyby v logice detekce změn, která nesprávně zjistila změnu při výskytu výrazu pravidla synchronizace se znaky nového řádku. Znaky nového řádku jsou vloženy do výrazu pravidla synchronizace pro zlepšení čitelnosti.
* Opravili jsme problém, který může po automatickém upgradu způsobit, že Azure AD Connect server nebude správně fungovat. Tento problém se týká Azure AD Connectch serverů s verzí verze 1.1.443.0 (nebo starší). Podrobnosti o problému najdete v článku [Azure AD Connect nefunguje správně po automatickém upgradu](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade).
* Opravili jsme problém, který může při výskytu chyb způsobit opakování automatického upgradu každých 5 minut. V případě, že se vyskytnou chyby, je funkce Automatické aktualizace při výskytu chyb pokusy s exponenciálním přechodem.
* Opravili jsme problém, kdy se událost synchronizace hesla 611 nesprávně zobrazuje v protokolech událostí aplikace Windows jako **informativní** místo **chyby**. Událost 611 se vygeneruje, kdykoli při synchronizaci hesel dojde k problému. 
* Opravili jsme problém v průvodci Azure AD Connect, který umožňuje povolit funkci zpětného zápisu skupin bez výběru organizační jednotky požadované pro zpětný zápis skupin.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Přidání úlohy odstraňování potíží do Průvodce Azure AD Connect v části Další úkoly. Zákazníci mohou tuto úlohu využít k řešení potíží souvisejících se synchronizací hesel a shromažďováním obecné diagnostiky. V budoucnu bude úloha odstraňování potíží rozšířena tak, aby zahrnovala i další problémy související se synchronizací adresáře.
* Azure AD Connect teď podporuje nový režim instalace s názvem **použít existující databázi**. Tento režim instalace umožňuje zákazníkům nainstalovat Azure AD Connect, které určují existující databázi ADSync. Další informace o této funkci najdete v článku [použití existující databáze](how-to-connect-install-existing-database.md).
* Pro lepší zabezpečení Azure AD Connect teď standardně používá protokol TLS 1.2 pro připojení ke službě Azure AD pro synchronizaci adresářů. Dříve byla ve výchozím nastavení protokol TLS 1.0.
* Když se spustí Azure AD Connect agenta synchronizace hesel, pokusí se připojit k dobře známému koncovému bodu Azure AD pro synchronizaci hesel. Po úspěšném připojení se přesměruje na koncový bod specifický pro danou oblast. Dřív agent synchronizace hesel uloží koncový bod specifický pro oblast do mezipaměti, dokud se nerestartuje. Agent nyní vymaže mezipaměť a opakuje se s dobře známým koncovým bodem, pokud dojde k potížím s připojením pomocí koncového bodu pro konkrétní oblast. Tato změna zajišťuje, aby synchronizace hesel mohla převzetí služeb při selhání na jiný koncový bod specifický pro oblast, když koncový bod v mezipaměti, který je specifický pro oblast, již není dostupný.
* K synchronizaci změn z místní doménové struktury AD se vyžaduje účet služby AD DS. Můžete buď vytvořit účet služba AD DS sami sebe a zadat jeho přihlašovací údaje pro Azure AD Connect, nebo (II) poskytnout přihlašovací údaje správce podniku a nechat Azure AD Connect vytvořit účet služba AD DS za vás. Dřív je výchozí možnost v průvodci Azure AD Connect. Nyní je výchozí volbou (II).

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Přidala se podpora pro Microsoft Azure Government Cloud a Microsoft Cloud Německo.

### <a name="ad-fs-management"></a>Správa AD FS
#### <a name="fixed-issues"></a>Opravené problémy
* Rutina Initialize-ADSyncNGCKeysWriteBack v modulu PowerShellu pro přípravu služby Active Directory nepoužívala v kontejneru registrací zařízení seznamy ACL, takže by proto zdědili jenom existující oprávnění.  Tato aktualizace byla aktualizována, aby měl účet synchronizační služby správná oprávnění.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Azure AD Connect ověření přihlašovací úlohy služby ADFS se aktualizovala tak, aby ověřovala přihlášení přes Microsoft Online, a ne jenom načtení tokenů ze služby AD FS.
* Při nastavování nové farmy AD FS pomocí Azure AD Connect se stránka žádající o přihlašovací údaje služby AD FS přesunula tak, aby se stala ještě předtím, než se uživateli zobrazí výzva k zadání serverů ADFS a WAP.  To umožňuje Azure AD Connect kontrolovat, zda má zadaný účet správná oprávnění.
* Během Azure AD Connect upgradu už neprovedeme upgrade, pokud se nepodaří aktualizovat vztah důvěryhodnosti služby Azure AD FS.  Pokud k tomu dojde, zobrazí se uživateli příslušná varovná zpráva a měla by pokračovat v resetování vztahu důvěryhodnosti prostřednictvím Azure AD Connect další úlohy.

### <a name="seamless-single-sign-on"></a>Bezproblémové jednoduché Sign-On
#### <a name="fixed-issues"></a>Opravené problémy
* Opravili jsme problém, který způsobil, že Průvodce Azure AD Connect vrátit chybu, pokud se pokusíte povolit [bezproblémové jednotné přihlašování](how-to-connect-sso.md). Chybová zpráva je *"konfigurace ověřovacího agenta Microsoft Azure AD Connect se nezdařila."* Tento problém má vliv na stávající zákazníky, kteří ručně upgradovali verzi Preview ověřovacích agentů pro [předávací ověřování](how-to-connect-sso.md) na základě kroků popsaných v tomto [článku](how-to-connect-pta-upgrade-preview-authentication-agents.md).


## <a name="115610"></a>1.1.561.0
Stav: Červenec 23 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Oprava potíží 

* Opravili jsme problém, který způsobil pravidlo pro okamžité synchronizace, které se vyvolalo do AD-User ImmutableId, aby se odebralo:

  * K tomuto problému dochází, když je upgrade Azure AD Connect, nebo když se v průvodci Azure AD Connect používá možnost úlohy *aktualizovat konfiguraci synchronizace* Azure AD Connect.

  * Toto synchronizační pravidlo platí pro zákazníky, kteří povolili [funkci MS-DS-ConsistencyGuid jako zdrojového ukotvení](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). Tato funkce byla představena ve verzi 1.1.524.0 a After. Po odebrání synchronizačního pravidla už Azure AD Connect nemůže naplnit místní atribut AD ms-DS-ConsistencyGuid s hodnotou atributu ObjectGuid. Nebrání tomu, aby se noví uživatelé zřídili do služby Azure AD.

  * Oprava zajistí, že synchronizační pravidlo již nebude během upgradu odebráno nebo během změny konfigurace, pokud je tato funkce povolena. Pro existující zákazníky, kterým byl tento problém ovlivněn, oprava také zajistí, že je pravidlo synchronizace po upgradu na tuto verzi Azure AD Connect přidáno zpátky.

* Opravili jsme problém, který způsobí, že dodaná pravidla synchronizace mají hodnotu priority menší než 100:

  * Obecně platí, že hodnoty priority 0-99 jsou vyhrazené pro vlastní pravidla synchronizace. Během upgradu se hodnoty priorit pro dopředná pravidla synchronizace aktualizují tak, aby odpovídaly změnám pravidel synchronizace. Kvůli tomuto problému může být k dodanému pravidla synchronizace přiřazena přednost hodnota, která je menší než 100.

  * Oprava zabrání problému, ke kterému došlo během upgradu. Neobnoví ale hodnoty priority pro stávající zákazníky, kterým problém ovlivnil. V budoucnu bude k dispozici samostatná oprava pro pomoc s obnovením.

* Opravili jsme problém, kdy se na [obrazovce pro filtrování domén a organizačních jednotek](how-to-connect-install-custom.md#domain-and-ou-filtering) v průvodci Azure AD Connect zobrazuje možnost *synchronizovat všechny domény a organizační jednotky* , a to i v případě, že je povolené filtrování na základě organizační jednotky.

*   Opravili jsme problém, který způsobil, že [obrazovka konfigurovat oddíly adresáře](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) v Synchronization Service Manager vrátila chybu, když se klikne na tlačítko *aktualizovat* . Chybová zpráva je *"při obnovování domén došlo k chybě: nelze přetypovat objekt typu ' System. Collections. ArrayList ' na typ ' Microsoft. DirectoryServices. MetadirectoryServices. UI. PropertySheetBase. MaPropertyPages. PartitionObject."* K této chybě dojde, pokud byla do existující doménové struktury služby AD přidána nová doména služby Active Directory a pokoušíte se aktualizovat Azure AD Connect pomocí tlačítka aktualizovat.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* [Funkce automatického upgradu](how-to-connect-install-automatic-upgrade.md) se rozšířila tak, aby podporovala zákazníky v následujících konfiguracích:
  * Povolili jste funkci zpětného zápisu zařízení.
  * Povolili jste funkci zpětného zápisu skupiny.
  * Instalace není expresním nastavením nebo DirSyncm upgradem.
  * V úložišti Metaverse máte více než 100 000 objektů.
  * Připojujete se k více než jedné doménové struktuře. Expresní instalace se připojuje jenom k jedné doménové struktuře.
  * Účet konektoru služby AD již není výchozím účtem MSOL_.
  * Server je nastavený jako pracovní režim.
  * Povolili jste funkci zpětného zápisu uživatelů.

  >[!NOTE]
  >Rozšiřování oboru funkce automatického upgradu má vliv na zákazníky s Azure AD Connect Build 1.1.105.0 a After. Pokud nechcete, aby se server Azure AD Connect automaticky Upgradoval, musíte na serveru Azure AD Connect spustit následující rutinu: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled` . Další informace o povolení nebo zakázání automatického upgradu najdete v článku [Azure AD Connect: automatický upgrade](how-to-connect-install-automatic-upgrade.md).
## <a name="115580"></a>1.1.558.0
Stav: nebude uvolněn. Změny v tomto sestavení jsou součástí verze 1.1.561.0.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Oprava potíží 

* Opravili jsme problém, který způsobil, že při aktualizaci konfigurace filtrování na základě organizační jednotky dojde k odebrání synchronizačního pravidla, které se vyvolalo, do AD-User ImmutableId. Toto synchronizační pravidlo se vyžaduje pro [funkci MS-DS-ConsistencyGuid jako zdrojové kotvy](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).

* Opravili jsme problém, kdy se na [obrazovce pro filtrování domén a organizačních jednotek](how-to-connect-install-custom.md#domain-and-ou-filtering) v průvodci Azure AD Connect zobrazuje možnost *synchronizovat všechny domény a organizační jednotky* , a to i v případě, že je povolené filtrování na základě organizační jednotky.

*   Opravili jsme problém, který způsobil, že [obrazovka konfigurovat oddíly adresáře](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) v Synchronization Service Manager vrátila chybu, když se klikne na tlačítko *aktualizovat* . Chybová zpráva je *"při obnovování domén došlo k chybě: nelze přetypovat objekt typu ' System. Collections. ArrayList ' na typ ' Microsoft. DirectoryServices. MetadirectoryServices. UI. PropertySheetBase. MaPropertyPages. PartitionObject."* K této chybě dojde, pokud byla do existující doménové struktury služby AD přidána nová doména služby Active Directory a pokoušíte se aktualizovat Azure AD Connect pomocí tlačítka aktualizovat.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* [Funkce automatického upgradu](how-to-connect-install-automatic-upgrade.md) se rozšířila tak, aby podporovala zákazníky v následujících konfiguracích:
  * Povolili jste funkci zpětného zápisu zařízení.
  * Povolili jste funkci zpětného zápisu skupiny.
  * Instalace není expresním nastavením nebo DirSyncm upgradem.
  * V úložišti Metaverse máte více než 100 000 objektů.
  * Připojujete se k více než jedné doménové struktuře. Expresní instalace se připojuje jenom k jedné doménové struktuře.
  * Účet konektoru služby AD již není výchozím účtem MSOL_.
  * Server je nastavený jako pracovní režim.
  * Povolili jste funkci zpětného zápisu uživatelů.

  >[!NOTE]
  >Rozšiřování oboru funkce automatického upgradu má vliv na zákazníky s Azure AD Connect Build 1.1.105.0 a After. Pokud nechcete, aby se server Azure AD Connect automaticky Upgradoval, musíte na serveru Azure AD Connect spustit následující rutinu: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled` . Další informace o povolení nebo zakázání automatického upgradu najdete v článku [Azure AD Connect: automatický upgrade](how-to-connect-install-automatic-upgrade.md).
## <a name="115570"></a>1.1.557.0
Stav: červenec 2017

>[!NOTE]
>Toto sestavení není pro zákazníky k dispozici prostřednictvím funkce Azure AD Connect automatické aktualizace.
### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Oprava potíží 
* Opravili jsme problém s rutinou Initialize-ADSyncDomainJoinedComputerSync, která způsobila, že ověřená doména nakonfigurovaná u stávajícího objektu spojovacího bodu služby se změní i v případě, že je stále platnou doménou. K tomuto problému dochází, pokud má tenant služby Azure AD více než jednu ověřenou doménu, kterou lze použít ke konfiguraci spojovacího bodu služby.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Zpětný zápis hesla je teď k dispozici pro náhled s Microsoft Azure Government cloudem a Microsoft Cloud Německu. Další informace o podpoře Azure AD Connect pro různé instance služby najdete v článku [Azure AD Connect: zvláštní důležité informace týkající se instancí](reference-connect-instances.md).

* Rutina Initialize-ADSyncDomainJoinedComputerSync nyní obsahuje nový volitelný parametr s názvem AzureADDomain. Tento parametr umožňuje určit, která ověřená doména se má použít ke konfiguraci spojovacího bodu služby.

### <a name="pass-through-authentication"></a>Předávací ověřování

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Název agenta požadovaného pro předávací ověřování byl změněn z *Microsoft Azure AD konektor proxy aplikací* na *Microsoft Azure AD připojit ověřovacího agenta*.

* Povolení předávacího ověřování už ve výchozím nastavení nepovoluje synchronizaci hodnot hash hesel.


## <a name="115530"></a>1.1.553.0
Stav: červen 2017

> [!IMPORTANT]
> V tomto sestavení jsou představeny změny schématu a synchronizace. Služba Azure AD Connect Synchronization spustí po upgradu úplný postup importu a úplné synchronizace. Podrobnosti o změnách jsou popsány níže. Chcete-li po upgradu dočasně odložit úplné kroky importu a úplné synchronizace, přečtěte si článek [jak po upgradu odložit úplnou synchronizaci](how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).
>
>
### <a name="azure-ad-connect-sync"></a>Synchronizace služby Azure AD Connect

#### <a name="known-issue"></a>Známý problém
* Existuje problém, který má vliv na zákazníky, kteří používají [filtrování podle organizačních jednotek](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) s Azure AD Connect synchronizace. Když přejdete na [stránku filtrování domén a organizačních jednotek](how-to-connect-install-custom.md#domain-and-ou-filtering) v průvodci Azure AD Connect, je očekáváno následující chování:
  * Pokud je povolené filtrování na základě organizační jednotky, vybere se možnost **synchronizovat vybrané domény a organizační jednotky** .
  * V opačném případě je vybrána možnost **synchronizovat všechny domény a organizační jednotky** .

K tomuto problému dochází, když při spuštění Průvodce vždycky vyberete **možnost Synchronizovat všechny domény a organizační jednotky** .  K tomu dojde i v případě, že bylo filtrování na základě organizační jednotky dříve nakonfigurováno. Před uložením změn konfigurace Azure AD Connect se ujistěte, že **je vybraná možnost synchronizovat vybrané domény a organizační jednotky** , a potvrďte, že jsou znovu povoleny všechny organizační jednotky, které je třeba synchronizovat. V opačném případě bude filtrování na základě organizační jednotky zakázáno.

#### <a name="fixed-issues"></a>Opravené problémy

* Opravili jsme problém se zpětným zápisem hesla, který správci Azure AD umožňuje resetovat heslo k místnímu uživatelskému účtu s oprávněním AD. K tomuto problému dochází, když Azure AD Connect k privilegovanému účtu udělili oprávnění k resetování hesla. Tento problém je řešený v této verzi Azure AD Connect tím, že neumožní správci Azure AD resetovat heslo libovolného místního privilegovaného uživatelského účtu, pokud správce není vlastníkem tohoto účtu. Další informace najdete v tématu [Security advisor 4033453](/security-updates/SecurityAdvisories/2017/4033453).

* Opravili jsme problém týkající se funkce [MS-DS-ConsistencyGuid jako zdrojového ukotvení](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) , kde se Azure AD Connect nezpětný zápis do místního atributu AD ms-DS-ConsistencyGuid. K tomuto problému dochází, když je na Azure AD Connect přidáno několik místních doménových struktur AD a je vybrána *možnost identity uživatelů v rámci více adresářů* . Při použití této konfigurace výsledná pravidla synchronizace neplní atribut sourceAnchorBinary v úložišti Metaverse. Atribut sourceAnchorBinary se používá jako zdrojový atribut pro atribut ms-DS-ConsistencyGuid. V důsledku toho neproběhne zpětný zápis do atributu MS-DSConsistencyGuid. Pokud chcete problém vyřešit, aktualizují se následující pravidla synchronizace, aby se zajistilo, že atribut sourceAnchorBinary v úložišti metaverse je vždycky vyplněný:
  * V nástroji ze služby AD-InetOrgPerson AccountEnabled.xml
  * V nástroji ze služby AD-InetOrgPerson Common.xml
  * V nástroji ze služby AD-User AccountEnabled.xml
  * V nástroji ze služby AD-User Common.xml
  * V nástroji ze služby AD – připojení uživatele SOAInAAD.xml

* Dříve i v případě, že funkce [MS-DS-ConsistencyGuid as source kotvy](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) není povolená, je synchronizační pravidlo "odchozí na uživatele ImmutableId" stále přidáno do Azure AD Connect. Účinek je neškodný a nezpůsobí, že by došlo ke zpětnému zápisu atributu ms-DS-ConsistencyGuid. Aby nedocházelo k omylům, přidala se logika, která zajistí, že se pravidlo synchronizace přidá jenom v případě, že je funkce povolená.

* Opravili jsme problém, který způsobil, že se synchronizace hodnot hash hesel nezdařila s chybovou událostí 611. K tomuto problému dochází po odebrání jednoho nebo více řadičů domény z místní služby AD. Na konci každého cyklu synchronizace hesel obsahuje synchronizační soubor cookie vydaný místní službou AD ID vyvolání odebraných řadičů domény s hodnotou USN (číslo sekvence aktualizace) 0. Správce synchronizace hesel nemůže uchovat synchronizující soubor cookie s hodnotou USN 0 a v případě chyby dojde k chybě 611. Během dalšího cyklu synchronizace Správce synchronizace hesel znovu použije poslední trvale uložený soubor cookie synchronizace, který neobsahuje hodnotu USN 0. To způsobí, že se stejné změny hesla budou znovu synchronizovat. V této opravě ukládá Správce synchronizace hesel správně soubor cookie synchronizace.

* Dříve i v případě, že byl při použití rutiny Set-ADSyncAutoUpgrade zakázán automatický upgrade, proces automatického upgradu nadále pravidelně kontroluje upgrade a spoléhá na to, že stažený instalační program bude akceptovat. V rámci této opravy již proces automatického upgradu nekontroluje aktualizace pravidelně. Oprava se automaticky použije, když se instalační program upgradu pro tuto Azure AD Connect verzi spustí jednou.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* Dříve byla funkce [MS-DS-ConsistencyGuid as source Anchor](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) dostupná jenom pro nová nasazení. Teď je dostupná pro existující nasazení. A konkrétně:
  * Chcete-li získat přístup k této funkci, spusťte Průvodce Azure AD Connect a vyberte možnost *aktualizace zdrojového ukotvení* .
  * Tato možnost je viditelná jenom pro existující nasazení, která používají objectGuid jako atribut sourceAnchor.
  * Při konfiguraci možnosti ověří Průvodce stav atributu ms-DS-ConsistencyGuid ve vaší místní službě Active Directory. Pokud atribut není nakonfigurován na žádném objektu uživatele v adresáři, průvodce použije jako atribut sourceAnchor ms-DS-ConsistencyGuid. Pokud je atribut nakonfigurován na jednom nebo více objektech uživatelů v adresáři, průvodce uzavře atribut je používán jinými aplikacemi a není vhodný jako atribut sourceAnchor a nepovoluje pokračování změny zdrojového kotvy. Pokud jste si jisti, že atribut není používán existujícími aplikacemi, je třeba kontaktovat podporu a získat informace o tom, jak chybu potlačit.

* V případě **userCertificate** atributu v objektech zařízení teď Azure AD Connect vyhledá hodnoty certifikátů potřebné pro [připojení zařízení připojených k doméně k Azure AD pro prostředí Windows 10](../devices/hybrid-azuread-join-plan.md) a odfiltruje zbytek před synchronizací do Azure AD. Chcete-li toto chování povolit, bylo aktualizováno pravidlo synchronizace, které se dokončí s názvem "připojení k AAD – zařízení SOAInAD".

* Azure AD Connect teď podporuje zpětný zápis atributu Exchange online **cloudPublicDelegates** do místního atributu AD **publicDelegates** . To umožňuje situaci, kdy je možné poštovní schránce Exchange Online udělit oprávnění SendOnBehalfTo uživatelům s místní poštovní schránkou Exchange. Aby se tato funkce podporovala, přidalo se nové předem připravené pravidlo synchronizace "odchozí na službu AD – User Exchange Hybrid PublicDelegates zpětného zápisu". Toto pravidlo synchronizace se přidá jenom do Azure AD Connect, když je povolená funkce hybridního serveru Exchange.

* Azure AD Connect teď podporuje synchronizaci atributu **altRecipient** z Azure AD. Aby se tato změna podporovala, Aktualizovali jsme dodaná pravidla synchronizace, aby obsahovala požadovaný tok atributů:
  * V nástroji ze služby AD – uživatel Exchange
  * Na AAD – uživatel ExchangeOnline

* Atribut **cloudSOAExchMailbox** v úložišti Metaverse udává, jestli má daný uživatel poštovní schránku Exchange Online, nebo ne. Jeho definice se aktualizovala tak, aby zahrnovala další Exchange Online RecipientDisplayTypes jako taková zařízení a poštovní schránky konferenční místnosti. Pokud chcete tuto změnu povolit, definice atributu cloudSOAExchMailbox, která se nachází pod přednastaveným pravidlem synchronizace "v z AAD – User Exchange hybrid", se aktualizovala z:

    ```
    CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
    ```

    ... na následující:

    ```
    CBool(
      IIF(IsPresent([cloudMSExchRecipientDisplayType]),(
        IIF([cloudMSExchRecipientDisplayType]=0,True,(
          IIF([cloudMSExchRecipientDisplayType]=2,True,(
            IIF([cloudMSExchRecipientDisplayType]=7,True,(
              IIF([cloudMSExchRecipientDisplayType]=8,True,(
                IIF([cloudMSExchRecipientDisplayType]=10,True,(
                  IIF([cloudMSExchRecipientDisplayType]=16,True,(
                    IIF([cloudMSExchRecipientDisplayType]=17,True,(
                      IIF([cloudMSExchRecipientDisplayType]=18,True,(
                        IIF([cloudMSExchRecipientDisplayType]=1073741824,True,(
                          IF([cloudMSExchRecipientDisplayType]=1073741840,True,False)))))))))))))))))))),False))
    ```

* Přidali jsme následující sadu funkcí kompatibilních s X509Certificate2 pro vytváření výrazů synchronizačního pravidla pro zpracování hodnot certifikátů v atributu userCertificate:
  * CertSubject 
  * CertIssuer
  * CertKeyAlgorithm
  * CertSubjectNameDN
  * CertIssuerOid
  * CertNameInfo
  * CertSubjectNameOid
  * CertIssuerDN
  * Certifikát
  * CertFriendlyName
  * CertThumbprint
  * CertExtensionOids
  * CertFormat
  * CertNotAfter
  * CertPublicKeyOid 
  * CertSerialNumber
  * CertNotBefore
  * CertPublicKeyParametersOid
  * CertVersion
  * CertSignatureAlgorithmOid
  * Vyberte
  * CertKeyAlgorithmParams
  * CertHashString
  * Kde
  * With

* Byly zavedeny změny schématu, které zákazníkům umožňují vytvářet vlastní pravidla synchronizace pro tok objektů sAMAccountName, domainNetBios a domainFQDN pro objekty skupiny a také rozlišující označení pro objekty uživatele:

  * Do schématu MV byly přidány následující atributy:
    * Skupina: účet Account
    * Skupina: domainNetBios
    * Skupina: domainFQDN
    * Person: rozlišující

  * Do schématu konektoru služby Azure AD se přidaly tyto atributy:
    * Skupina: OnPremisesSamAccountName
    * Skupina: NetBios
    * Skupina: DnsDomainName
    * Uživatel: OnPremisesDistinguishedName

* Skript rutiny ADSyncDomainJoinedComputerSync nyní obsahuje nový volitelný parametr s názvem AzureEnvironment. Parametr slouží k určení oblasti, ve které je hostovaný příslušný tenant Azure Active Directory. Platné hodnoty zahrnují:
  * AzureCloud (výchozí)
  * AzureChinaCloud
  * AzureGermanyCloud
  * USGovernment

* Aktualizovaný Editor pravidel synchronizace pro použití Join (místo zřízení) jako výchozí hodnota typu odkazu během vytváření pravidla synchronizace.

### <a name="ad-fs-management"></a>Správa AD FS

#### <a name="issues-fixed"></a>Opravené problémy

* Následující adresy URL jsou novými koncovými body WS-Federation zavedenými službou Azure AD za účelem zlepšení odolnosti proti výpadku ověřování a přidají se do místní AD FS konfigurace vztahu důvěryhodnosti předávající strany:
  * https: \/ /ESTS.Login.microsoftonline.com/login.SRF
  * https: \/ /stamp2.Login.microsoftonline.com/login.SRF
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf

* Opravili jsme problém, který způsobil, že AD FS generovala nesprávnou hodnotu deklarace pro IssuerID. K tomuto problému dochází, pokud je v tenantovi Azure AD více ověřených domén a přípona domény atributu userPrincipalName používaného k vygenerování deklarace IssuerID je alespoň 3 úrovně (například johndoe@us.contoso.com ). Problém je vyřešen aktualizací regulárního výrazu používaného pravidly deklarace identity.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Dříve se funkce správy certifikátů ADFS, kterou poskytuje Azure AD Connect, dá použít jenom s farmami ADFS spravovanými přes Azure AD Connect. Teď můžete tuto funkci použít se farmami ADFS, které nejsou spravované pomocí Azure AD Connect.

## <a name="115240"></a>1.1.524.0
Vydáno: květen 2017

> [!IMPORTANT]
> V tomto sestavení jsou představeny změny schématu a synchronizace. Služba Azure AD Connect Sync spustí po upgradu úplný postup importu a úplné synchronizace. Podrobnosti o změnách jsou popsány níže.
>
>
**Opravené problémy:**

Synchronizace služby Azure AD Connect

* Opravili jsme problém, který způsobí, že na Azure AD Connect serveru dojde k automatickému upgradu i v případě, že zákazník funkci zakázal pomocí rutiny Set-ADSyncAutoUpgrade. V případě této opravy stále proces automatického upgradu na serveru nadále pravidelně kontroluje upgrade, ale stažený instalační program se dodrží konfigurace automatického upgradu.
* Během DirSync místního upgradu vytvoří Azure AD Connect účet služby Azure AD, který bude používat konektor Azure AD pro synchronizaci se službou Azure AD. Po vytvoření účtu se Azure AD Connect ověří pomocí Azure AD pomocí účtu. V některých případech se ověřování nezdaří kvůli přechodným problémům, které zase způsobí, že DirSync místní upgrade na selhání s chybou *"při provádění konfigurace AAD Sync úlohy došlo k chybě: AADSTS50034: Chcete-li se přihlásit k této aplikaci, je nutné účet přidat do adresáře xxx.onmicrosoft.com."* Pro zvýšení odolnosti upgradu DirSync se teď Azure AD Connect opakuje krok ověření.
* Došlo k potížím s Build 443, který způsobí, že se DirSync místní upgrade na úspěšný, ale nevytvoří se profily vyžadované pro synchronizaci adresáře. V tomto sestavení Azure AD Connect je obsažena retušovací logika. Když zákazník upgraduje na toto sestavení, Azure AD Connect zjistí chybějící profily spuštění a vytvoří je.
* Opravili jsme problém, který způsobí, že se proces synchronizace hesel nespustí s ID události 6900 a chybou *"položka se stejným klíčem již byla přidána"*. K tomuto problému dochází, pokud aktualizujete konfiguraci filtrování organizačních jednotek tak, aby zahrnovala oddíl konfigurace služby AD. Tento problém můžete vyřešit tak, že proces synchronizace hesel teď synchronizuje změny hesel jenom z oddílů domény služby AD. Nedoménové oddíly, jako je konfigurační oddíl, se přeskočí.
* Během Expresní instalace Azure AD Connect vytvoří místní účet služba AD DS, který konektor služby AD použije ke komunikaci s místní službou AD. Dřív se účet vytvoří s příznakem PASSWD_NOTREQD nastaveným na atributu User-Account-Control a na účtu se nastaví náhodné heslo. Nyní Azure AD Connect explicitně odstraní příznak PASSWD_NOTREQD poté, co je na účtu nastaveno heslo.
* Opravili jsme problém, který způsobí selhání upgradu DirSync s chybou *"došlo k zablokování v SQL serveru, který se pokouší získat zámek aplikace"* , když se v místním schématu AD najde atribut mailNickname, ale není vázaný na třídu uživatelského objektu AD.
* Opravili jsme problém, který způsobí automatické zakázání funkce zpětného zápisu zařízení, když správce aktualizuje konfiguraci Azure AD Connect synchronizace pomocí Průvodce Azure AD Connect. Tato chyba je způsobená průvodcem, který provádí kontrolu požadavků pro stávající konfiguraci zpětného zápisu zařízení v místní službě AD, a kontrola se nezdařila. Touto opravou tuto kontrolu přeskočíte, pokud už je zpětný zápis zařízení povolený dříve.
* Chcete-li konfigurovat filtrování organizační jednotky, můžete použít Průvodce Azure AD Connect nebo Synchronization Service Manager. Pokud jste dřív používali Průvodce Azure AD Connect ke konfiguraci filtrování organizačních jednotek, budou nové organizační jednotky vytvořené později zahrnuty do synchronizace adresářů. Pokud nechcete, aby byly zahrnuty nové organizační jednotky, je nutné nakonfigurovat filtrování organizační jednotky pomocí Synchronization Service Manager. Nyní můžete stejné chování dosáhnout pomocí Průvodce Azure AD Connect.
* Opravili jsme problém, který způsobí, že uložené procedury vyžadované Azure AD Connect se vytvoří ve schématu instalace správce místo ve schématu dbo.
* Opravili jsme problém, který způsobí, že se atribut TrackingId vrácený službou Azure AD vynechá v protokolech událostí serveru Azure AD Connect. K tomuto problému dochází, pokud Azure AD Connect obdrží zprávu o přesměrování ze služby Azure AD a Azure AD Connect se nemůže připojit k zadanému koncovému bodu. TrackingId je používají technici podpory ke korelaci s protokoly na straně služby během řešení potíží.
* Když Azure AD Connect přijme LargeObject chybu z Azure AD, Azure AD Connect vygeneruje událost s ID události 6941 a zprávou *"zřízený objekt je moc velký. Ořízne počet hodnot atributů tohoto objektu.* Ve stejnou dobu Azure AD Connect také vygeneruje zavádějící událost s ID události 6900 a zprávou *"Microsoft. online. koexistence. ProvisionRetryException: nelze komunikovat se službou Windows Azure Active Directory Service."* Aby se minimalizovala jasnost, Azure AD Connect už při přijetí chyby LargeObject negeneruje druhou událost.
* Opravili jsme problém, který způsobí, že Synchronization Service Manager přestane reagovat při pokusu o aktualizaci konfigurace pro obecný konektor LDAP.

**Nové funkce a vylepšení:**

Synchronizace služby Azure AD Connect
* Změny pravidel synchronizace – implementovaly se následující změny pravidla synchronizace:
  * Sada pravidel výchozí synchronizace se aktualizovala tak, aby neexportoval atributy **userCertificate** a **userSMIMECertificate** , pokud atributy mají více než 15 hodnot.
  * **Atributy AD** a **msExchBypassModerationLink** jsou teď zahrnuté do výchozí sady pravidel synchronizace.
  * **Fotka** atributu AD se odebrala z výchozí sady pravidel synchronizace.
  * Přidání **preferredDataLocation** do schématu Metaverse schématu a konektoru služby Azure AD. Zákazníci, kteří chtějí aktualizovat buď atributy v Azure AD, můžou implementovat vlastní pravidla synchronizace. 
  * Bylo přidáno **userType** do schématu Metaverse schématu a konektoru služby Azure AD. Zákazníci, kteří chtějí aktualizovat buď atributy v Azure AD, můžou implementovat vlastní pravidla synchronizace.

* Azure AD Connect nyní automaticky povoluje použití atributu ConsistencyGuid jako atributu zdrojového ukotvení místních objektů služby AD. Dále Azure AD Connect naplní atribut ConsistencyGuid hodnotou atributu objectGuid, pokud je prázdný. Tato funkce se vztahuje pouze na nové nasazení. Další informace o této funkci najdete v části článek [Azure AD Connect: koncepty návrhu – použití MS-DS-ConsistencyGuid jako sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).
* Přidala se nová rutina odstraňování potíží Invoke-ADSyncDiagnostics, která vám umožní diagnostikovat problémy související se synchronizací hodnot hash hesel. Informace o použití rutiny najdete v článku [řešení potíží se synchronizací hodnot hash hesel pomocí Azure AD Connect synchronizace](tshoot-connect-password-hash-synchronization.md).
* Azure AD Connect teď podporuje synchronizaci Mail-Enabled objektů veřejné složky z místní služby AD do Azure AD. Tuto funkci můžete povolit pomocí Průvodce Azure AD Connect v části volitelné funkce. Pokud chcete získat další informace o této funkci, přečtěte si článek na [webu Office 365 na základě hraničního blokování podpory pro veřejné složky s povolenými místními e-maily](https://techcommunity.microsoft.com/t5/exchange/office-365-directory-based-edge-blocking-support-for-on-premises/m-p/74218).
* Azure AD Connect vyžaduje, aby byl účet služba AD DS synchronizovaný z místní služby AD. Pokud jste dřív nainstalovali Azure AD Connect pomocí expresního režimu, mohli byste zadat přihlašovací údaje účtu správce podniku a Azure AD Connect by se vytvořil účet služba AD DS, který potřebujete. Pro vlastní instalaci a Přidání doménových struktur do existujícího nasazení jste ale museli místo toho zadat služba AD DS účet. Nyní máte také možnost při vlastní instalaci zadat přihlašovací údaje účtu správce podniku a nechat Azure AD Connect vytvořit služba AD DS účet povinný.
* Azure AD Connect teď podporuje SQL AOA. Před instalací Azure AD Connect musíte povolit SQL AOA. Během instalace Azure AD Connect zjistí, jestli je zadaná instance SQL povolená pro SQL AOA nebo ne. Pokud je povolený SQL AOA, Azure AD Connect dál vyhodnotí, pokud je SQL AOA nakonfigurované na používání synchronní replikace nebo asynchronní replikace. Při nastavování naslouchacího procesu skupiny dostupnosti doporučujeme nastavit vlastnost RegisterAllProvidersIP na hodnotu 0. Toto doporučení je kvůli tomu, že Azure AD Connect aktuálně používá SQL Native Client k připojení k SQL a SQL Native Client nepodporuje použití vlastnosti MultiSubNetFailover.
* Pokud jako databázi pro Azure AD Connect Server používáte LocalDB a dosáhli jste limitu velikosti 10 GB, synchronizační služba se už nespustí. Dříve musíte v LocalDB provést operaci ShrinkDatabase a uvolnit tak dostatek místa, aby se synchronizační služba spustila. Potom můžete pomocí Synchronization Service Manager odstranit historii spuštění a získat tak další místo v databázi. Nyní můžete pomocí rutiny Start-ADSyncPurgeRunHistory vyprázdnit data historie spuštění z LocalDB a uvolnit tak místo v databázi. Tato rutina dále podporuje offline režim (zadáním parametru-offline), který se dá použít, když synchronizační služba není spuštěná. Poznámka: režim offline lze použít pouze v případě, že synchronizační služba není spuštěna a použitá databáze je LocalDB.
* Aby se snížila velikost požadovaného prostoru úložiště, Azure AD Connect nyní komprimuje podrobnosti o chybách synchronizace před jejich uložením v databázích LocalDB/SQL. Při upgradu ze starší verze Azure AD Connect na tuto verzi Azure AD Connect provede jednorázovou kompresi pro existující podrobnosti chyby synchronizace.
* Když jste dřív aktualizovali konfiguraci filtrování organizačních jednotek, musíte ručně spustit úplný import, abyste zajistili, že existující objekty budou správně zahrnuté nebo vyloučené z synchronizace adresářů. Nyní Azure AD Connect automaticky spustí úplný import během příštího synchronizačního cyklu. Kromě toho je možné úplný import použít pouze na konektory služby AD ovlivněné aktualizací. Poznámka: Toto vylepšení se vztahuje pouze na aktualizace filtrování organizačních jednotek provedené pomocí Průvodce Azure AD Connect. Neplatí pro aktualizace filtrování organizačních jednotek provedené pomocí Synchronization Service Manager.
* V předchozích případech podporuje filtrování na základě skupin pouze uživatele, skupiny a objekty kontaktů. V současné době filtrování na základě skupin podporuje také objekty počítačů.
* Dříve můžete odstranit data prostoru konektoru bez zakázání plánovače Azure AD Connect synchronizace. Nyní Synchronization Service Manager zablokuje odstranění dat prostoru konektoru, pokud zjistí, že je Plánovač povolený. Kromě toho se vrátí upozornění, které zákazníkům informuje o potenciální ztrátě dat, pokud se odstraní data prostoru konektoru.
* Předtím, než bude průvodce Azure AD Connect správně fungovat, je nutné zakázat příkaz prostředí PowerShell přepis. Tento problém je částečně vyřešený. Pokud ke správě konfigurace synchronizace používáte Azure AD Connect průvodce, můžete povolit PowerShellový přepis. Pokud ke správě konfigurace služby ADFS používáte Azure AD Connect průvodce, musíte zakázat přepis prostředí PowerShell.



## <a name="114860"></a>1.1.486.0
Vydáno: duben 2017

**Opravené problémy:**
* Opravili jsme problém, kdy se Azure AD Connect do lokalizované verze Windows serveru nenainstaloval úspěšně.

## <a name="114840"></a>1.1.484.0
Vydáno: duben 2017

**Známé problémy:**

* Tato verze Azure AD Connect nebude úspěšně nainstalována, pokud jsou splněny následující podmínky:
   1. Provádíte buď DirSync místní upgrade, nebo novou instalaci Azure AD Connect.
   2. Používáte lokalizovanou verzi systému Windows Server, kde název předdefinované skupiny správců na serveru není "Administrators".
   3. Používáte výchozí SQL Server 2012 Express LocalDB nainstalované s Azure AD Connect místo poskytování vlastního úplného SQL.

**Opravené problémy:**

Synchronizace služby Azure AD Connect
* Opravili jsme problém, kdy Plánovač synchronizace přeskočí celý krok synchronizace, pokud u jednoho nebo více konektorů chybí pro tento krok synchronizace profil spuštění. Například ručně jste přidali konektor pomocí Synchronization Service Manager, aniž byste pro něj vytvořili rozdílový profil spuštění importu. Tato oprava zajistí, že Plánovač synchronizace bude pokračovat v provádění rozdílového importu pro jiné konektory.
* Opravili jsme problém, kdy synchronizační služba okamžitě zastaví zpracování profilu spuštění, když dojde k potížím s některým z kroků spuštění. Tato oprava zajišťuje, že synchronizační služba přeskočí tento krok spuštění a pokračuje ve zpracování REST. Máte třeba rozdílový profil spuštění importu pro konektor služby AD s několika kroky spuštění (jeden pro každou místní doménu AD). Synchronizační služba spustí rozdílový import s ostatními doménami služby AD, a to i v případě, že jedna z nich má problémy s připojením k síti.
* Opravili jsme problém, který způsobí, že se aktualizace konektoru služby Azure AD při automatickém upgradu přeskočí.
* Opravili jsme problém, který způsobí, že Azure AD Connect nesprávně určit, jestli je server řadičem domény během instalace, což zase způsobí selhání upgradu DirSync.
* Opravili jsme problém, který způsobí, že DirSync místní upgrade nevytvoří žádný profil spuštění pro konektor Azure AD.
* Opravili jsme problém, kdy Synchronization Service Manager uživatelské rozhraní při pokusu o konfiguraci obecného konektoru LDAP přestane reagovat.

Správa AD FS
* Opravili jsme problém, kdy se Průvodce Azure AD Connect nepovede, pokud byl primární uzel AD FS přesunutý na jiný server.

Jednotné přihlašování k ploše
* Opravili jsme problém v průvodci Azure AD Connect, kde obrazovka Sign-In neumožňuje povolit funkci jednotného přihlašování k ploše, pokud jste při nové instalaci zvolili možnost synchronizace hesel jako Sign-In.

**Nové funkce a vylepšení:**

Synchronizace služby Azure AD Connect
* Azure AD Connect Sync teď podporuje použití účtu virtuální služby, účtu spravované služby a skupinového účtu spravované služby jako svého účtu služby. To platí jenom pro novou instalaci Azure AD Connect. Při instalaci Azure AD Connect:
    * Ve výchozím nastavení vytvoří průvodce Azure AD Connect účet virtuální služby a použije ho jako svůj účet služby.
    * Pokud instalujete na řadič domény, Azure AD Connect se vrátí k předchozímu chování, kde vytvoří účet uživatele domény a použije ho jako svůj účet služby.
    * Výchozí chování můžete přepsat zadáním jedné z následujících možností:
      * Skupinový účet spravované služby
      * Účet spravované služby
      * Účet uživatele domény
      * Místní uživatelský účet
* Pokud jste dříve upgradovali na nové sestavení Azure AD Connect obsahující změny pravidel aktualizace nebo synchronizace konektorů, Azure AD Connect spustí úplný cyklus synchronizace. Nyní Azure AD Connect možnost selektivně spouští úplný krok importu pouze pro konektory s aktualizací a krok úplné synchronizace pouze pro konektory se změnami pravidla synchronizace.
* Dříve se prahová hodnota pro odstranění exportu vztahuje pouze na exporty, které jsou aktivovány pomocí plánovače synchronizace. Teď je tato funkce Rozšířená tak, aby zahrnovala exporty ručně aktivované zákazníkem pomocí Synchronization Service Manager.
* V tenantovi Azure AD existuje konfigurace služby, která indikuje, jestli je funkce synchronizace hesel pro vašeho tenanta povolená, nebo ne. V předchozích krocích je konfigurace služby snadno nakonfigurovaná Azure AD Connect, když máte aktivní a pracovní server. Nyní se Azure AD Connect pokusí zachovat konfiguraci služby konzistentní s aktivním Azure AD Connectm serverem.
* Průvodce Azure AD Connect nyní detekuje a vrátí upozornění, pokud místní služba AD nemá povolenu složku Koš služby AD.
* Dřív se export do Azure AD vyprší a dojde k chybě, pokud celková velikost objektů v dávce překročí určitou prahovou hodnotu. Nyní se synchronizační služba pokusí znovu odeslat objekty v samostatných, menších dávkách, pokud dojde k problému.
* Aplikace správy klíčů synchronizační služby byla odebrána z nabídky Start systému Windows. Správa šifrovacího klíče bude nadále podporována prostřednictvím rozhraní příkazového řádku pomocí miiskmu.exe. Informace o správě šifrovacího klíče najdete [v článku opuštění Azure AD Connect synchronizaci šifrovacího klíče](./how-to-connect-sync-change-serviceacct-pass.md#abandoning-the-adsync-service-account-encryption-key).
* Pokud jste dřív změnili heslo účtu služby Azure AD Connect Sync, synchronizační služba nebude moct správně spustit, dokud jste nezrušili šifrovací klíč a znovu nevytvořili heslo účtu služby Azure AD Connect synchronizace. Nyní se tento proces už nepožaduje.

Jednotné přihlašování k ploše

* Průvodce Azure AD Connect už nepotřebuje, aby se v síti otevřel port 9090 při konfiguraci předávacího ověřování a jednotného přihlašování k ploše. Je vyžadován pouze port 443.

## <a name="114430"></a>verze 1.1.443.0
Vydáno: březen 2017

**Opravené problémy:**

Synchronizace služby Azure AD Connect
* Opravili jsme problém, který způsobí, že Průvodce Azure AD Connect selže, pokud zobrazovaný název konektoru Azure AD neobsahuje počáteční doménu onmicrosoft.com přiřazenou klientovi Azure AD.
* Opravili jsme problém, který způsobí selhání Průvodce Azure AD Connect při navazování připojení ke službě SQL Database, když heslo účtu služby synchronizace obsahuje speciální znaky, jako je apostrof, dvojtečka a mezera.
* Opravili jsme problém, který způsobí, že chyba "obrázek obsahuje kotvu, která se liší od obrázku", který se má zobrazit na Azure AD Connect serveru v pracovním režimu, po dočasném vyloučení místního objektu AD z synchronizace a jeho opětovného zahrnutí pro synchronizaci.
* Opravili jsme problém, který způsobí, že chyba "objekt umístěný v rámci DN je fiktivní", ke kterému dojde na Azure AD Connect serveru v pracovním režimu, po dočasném vyloučení místního objektu AD z synchronizace a jeho opětovného zahrnutí pro synchronizaci.

Správa AD FS
* Opravili jsme problém, kdy Průvodce Azure AD Connect neaktualizuje konfiguraci AD FS a nastavil správné deklarace identity pro vztah důvěryhodnosti předávající strany po nakonfigurování alternativního přihlašovacího ID.
* Opravili jsme problém, kdy Průvodce Azure AD Connect nedokáže správně zpracovat AD FS servery, jejichž účty služeb jsou nakonfigurované pomocí formátu userPrincipalName namísto formátu sAMAccountName.

Předávací ověřování
* Opravili jsme problém, který způsobí, že Azure AD Connect Průvodce selže v případě, že je vybraná možnost předávací ověřování, ale registrace jeho konektoru selže.
* Opravili jsme problém, který způsobí, že Průvodce Azure AD Connect obejít kontrolu ověřování u metody přihlašování vybrané, když je povolená funkce jednotného přihlašování k ploše.

Resetování hesla
* Opravili jsme problém, který může způsobit, že se server Azure Azure AD Connect nepokouší znovu připojit, pokud bylo připojení ukončené bránou firewall nebo proxy serverem.

**Nové funkce a vylepšení:**

Synchronizace služby Azure AD Connect
* Rutina Get-ADSyncScheduler nyní vrací novou logickou vlastnost s názvem SyncCycleInProgress. Pokud je vrácená hodnota true, znamená to, že probíhá naplánovaná synchronizační cyklus.
* Cílová složka pro ukládání Azure AD Connect instalačních a instalačních protokolů se přesunula z%localappdata%\AADConnect na%programdata%\AADConnect, aby se zlepšila dostupnost souborů protokolu.

Správa AD FS
* Přidala se podpora pro aktualizaci certifikátu TLS/SSL AD FS farmy.
* Přidání podpory pro správu AD FS 2016.
* Během AD FS instalace teď můžete zadat existující gMSA (skupinový účet spravované služby).
* Nyní můžete nakonfigurovat SHA-256 jako algoritmus hash podpisu pro vztah důvěryhodnosti předávající strany Azure AD.

Resetování hesla
* Představena vylepšení, která umožňují, aby produkt fungoval v prostředích s přísnějšími pravidly brány firewall.
* Lepší spolehlivost připojení k Azure Service Bus.

## <a name="113800"></a>1.1.380.0
Vydáno: prosinec 2016

**Opravený problém:**

* Opravili jsme problém, kdy v tomto sestavení chybí pravidlo deklarace identity issuerid pro Active Directory Federation Services (AD FS) (AD FS).

>[!NOTE]
>Toto sestavení není pro zákazníky k dispozici prostřednictvím funkce Azure AD Connect automatické aktualizace.
## <a name="113710"></a>1.1.371.0
Vydáno: prosinec 2016

**Známý problém:**

* V tomto sestavení chybí pravidlo deklarace identity issuerid pro AD FS. Pokud jste federování více domén Azure Active Directory (Azure AD), je nutné pravidlo deklarace identity issuerid. Pokud používáte Azure AD Connect ke správě místního nasazení AD FS, upgrade na toto sestavení odebere stávající pravidlo deklarace identity issuerid z konfigurace AD FS. Problém můžete obejít tak, že přidáte pravidlo deklarace issuerid po instalaci nebo upgradu. Podrobnosti o přidání pravidla deklarace identity issuerid najdete v tomto článku o [podpoře více domén pro federování se službou Azure AD](how-to-connect-install-multiple-domains.md).

**Opravený problém:**

* Pokud pro odchozí připojení není otevřený port 9090, Azure AD Connect instalace nebo upgrade dojde k chybě.

>[!NOTE]
>Toto sestavení není pro zákazníky k dispozici prostřednictvím funkce Azure AD Connect automatické aktualizace.
## <a name="113700"></a>1.1.370.0
Vydáno: prosinec 2016

**Známé problémy:**

* V tomto sestavení chybí pravidlo deklarace identity issuerid pro AD FS. Pokud federování více domén s Azure AD, je nutné pravidlo deklarace identity issuerid. Pokud používáte Azure AD Connect ke správě místního nasazení AD FS, upgrade na toto sestavení odebere stávající pravidlo deklarace identity issuerid z konfigurace AD FS. Problém můžete obejít tak, že přidáte pravidlo deklarace issuerid po instalaci nebo upgradu. Podrobnosti o přidání pravidla deklarace identity issuerid najdete v tomto článku o [podpoře více domén pro federování se službou Azure AD](how-to-connect-install-multiple-domains.md).
* Aby bylo možné dokončit instalaci, musí být port 9090 otevřený odchozí.

**Nové funkce:**

* Předávací ověřování (Preview).

>[!NOTE]
>Toto sestavení není pro zákazníky k dispozici prostřednictvím funkce Azure AD Connect automatické aktualizace.
## <a name="113430"></a>1.1.343.0
Vydáno: listopadu 2016

**Známý problém:**

* V tomto sestavení chybí pravidlo deklarace identity issuerid pro AD FS. Pokud federování více domén s Azure AD, je nutné pravidlo deklarace identity issuerid. Pokud používáte Azure AD Connect ke správě místního nasazení AD FS, upgrade na toto sestavení odebere stávající pravidlo deklarace identity issuerid z konfigurace AD FS. Problém můžete obejít tak, že přidáte pravidlo deklarace issuerid po instalaci nebo upgradu. Podrobnosti o přidání pravidla deklarace identity issuerid najdete v tomto článku o [podpoře více domén pro federování se službou Azure AD](how-to-connect-install-multiple-domains.md).

**Opravené problémy:**

* Instalace Azure AD Connect se někdy nepovede, protože nemůže vytvořit účet místní služby, jehož heslo splňuje úroveň složitosti určenou zásadami hesel organizace.
* Opravili jsme problém, kdy pravidla spojování nejsou znovu vyhodnocena, když se objekt v prostoru konektoru současně stane mimo rozsah pro jedno pravidlo spojení a stane se v oboru pro jiný obor. K tomu může dojít, pokud máte dvě nebo více pravidel spojení, jejichž podmínky spojení se vzájemně vylučují.
* Opravili jsme problém, kdy pravidla příchozí synchronizace (z Azure AD), která neobsahují pravidla pro připojení, se nezpracují, pokud mají hodnoty s nižší prioritou, než obsahují pravidla připojení.

**Vylepšení:**

* Přidání podpory pro instalaci Azure AD Connect v systému Windows Server 2016 Standard nebo vyšším.
* Přidání podpory pro použití SQL Server 2016 jako vzdálené databáze pro Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Vydáno: srpen 2016

**Opravené problémy:**

* Změny intervalu synchronizace se neprojeví, dokud se nedokončí další cyklus synchronizace.
* Průvodce Azure AD Connect nepřijímá účet služby Azure AD, jehož uživatelské jméno začíná podtržítkem ( \_ ).
* V průvodci Azure AD Connect se nepovedlo ověřit účet Azure AD, pokud heslo účtu obsahuje příliš mnoho speciálních znaků. Chybová zpráva: Nepodařilo se ověřit přihlašovací údaje. Došlo k neočekávané chybě. " .
* Odinstalace přípravného serveru zakáže synchronizaci hesel v tenantovi Azure AD a způsobí selhání synchronizace hesel s aktivním serverem.
* Synchronizace hesla se v neobvyklých případech nezdařila, pokud se na uživatele neuloží hodnota hash hesla.
* Když je Azure AD Connect Server povolený pro pracovní režim, zpětný zápis hesla není dočasně zakázaný.
* Průvodce Azure AD Connect nezobrazuje skutečnou synchronizaci hesel a konfiguraci zpětného zápisu hesla, když je server v pracovním režimu. Vždycky se zobrazí jako zakázané.
* Změny konfigurace synchronizace hesel a zpětného zápisu hesla nejsou trvale uložené pomocí Průvodce Azure AD Connect, když je server v pracovním režimu.

**Vylepšení:**

* Rutina Start-ADSyncSyncCycle se aktualizovala tak, aby označovala, jestli je možné úspěšně spustit nový cyklus synchronizace, nebo ne.
* Přidání rutiny Stop-ADSyncSyncCycle pro ukončení procesu synchronizace a operace, které aktuálně probíhá.
* Rutina Stop-ADSyncScheduler se aktualizovala tak, aby ukončila synchronizační cyklus a operaci, které aktuálně probíhá.
* Při konfiguraci [rozšíření adresáře](how-to-connect-sync-feature-directory-extensions.md) v průvodci Azure AD Connect se teď dá vybrat atribut Azure AD typu "Teletex řetězec".

## <a name="111890"></a>1.1.189.0
Vydáno: červen 2016

**Opravené problémy a vylepšení:**

* Azure AD Connect se teď dají nainstalovat na server kompatibilní se standardem FIPS.
  * Informace o synchronizaci hesel najdete v tématu [synchronizace hodnot hash hesel a FIPS](how-to-connect-password-hash-synchronization.md#password-hash-synchronization-and-fips).
* Opravili jsme problém, kdy se název NetBIOS nepovedlo přeložit na plně kvalifikovaný název domény v konektoru služby Active Directory.

## <a name="111800"></a>1.1.180.0
Vydáno: květen 2016

**Nové funkce:**

* Upozorňuje a pomáhá ověřit domény, pokud jste to neudělali předtím, než spustíte Azure AD Connect.
* Přidání podpory pro [Microsoft Cloud Německo](reference-connect-instances.md#microsoft-cloud-germany)
* Přidali jsme podporu pro nejnovější [Microsoft Azure Government cloudovou](reference-connect-instances.md#microsoft-azure-government) infrastrukturu s novými požadavky na adresu URL.

**Opravené problémy a vylepšení:**

* Do editoru pravidel synchronizace se přidalo filtrování, které usnadňuje hledání pravidel synchronizace.
* Vylepšený výkon při odstraňování prostoru konektoru
* Opravili jsme problém, když se stejný objekt odstranil a přidal do stejného běhu (s názvem odstranit/přidat).
* Pravidlo zakázané synchronizace již znovu nepovoluje zahrnutí objektů a atributů při upgradu nebo aktualizaci schématu adresáře.

## <a name="111300"></a>1.1.130.0
Vydáno: duben 2016

**Nové funkce:**

* Přidání podpory pro vícehodnotové atributy do [rozšíření adresáře](how-to-connect-sync-feature-directory-extensions.md).
* Přidání podpory pro další varianty konfigurace pro [Automatický upgrade](how-to-connect-install-automatic-upgrade.md) , které se považují za opravňující pro upgrade.
* Přidali jsme některé rutiny pro [vlastní Plánovač](how-to-connect-sync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Vydáno: březen 2016

**Opravené problémy:**

* Ujistěte se, že expresní instalaci nelze použít v systému Windows Server 2008 (pre-R2), protože synchronizace hesla není v tomto operačním systému podporována.
* Upgrade z DirSync s konfigurací vlastního filtru nefungoval podle očekávání.
* Při upgradu na novější verzi a neexistují žádné změny v konfiguraci, nelze naplánovat úplný Import a synchronizaci.

## <a name="111100"></a>1.1.110.0
Vydáno: únor 2016

**Opravené problémy:**

* Upgrade z dřívějších verzí nefunguje, pokud instalace není ve výchozí složce C:\Program Files.
* Pokud nainstalujete a zaškrtnete políčko **spustit proces synchronizace** na konci Průvodce instalací, spuštění Průvodce instalací podruhé neumožní plánovači.
* Plánovač nefunguje podle očekávání na serverech, na kterých se nepoužívá formát data a času US-EN. Také se zablokuje, `Get-ADSyncScheduler` aby vracely správné časy.
* Pokud jste nainstalovali dřívější verzi Azure AD Connect s AD FS jako možnost přihlášení a upgrade, nemůžete znovu spustit Průvodce instalací nástroje.

## <a name="111050"></a>1.1.105.0
Vydáno: únor 2016

**Nové funkce:**

* Funkce [automatického upgradu](how-to-connect-install-automatic-upgrade.md) pro zákazníky expresního nastavení
* Podpora globálního správce pomocí Multi-Factor Authentication Azure AD a Privileged Identity Management v Průvodci instalací.
  * Pokud používáte Multi-Factor Authentication, musíte proxy serveru dovolit, aby taky povolili provoz https://secure.aadcdn.microsoftonline-p.com .
  * Pro https://secure.aadcdn.microsoftonline-p.com správné fungování Multi-Factor Authentication musíte přidat do seznamu důvěryhodných webů.
* Povolí změnu způsobu přihlášení uživatele po počáteční instalaci.
* Povolí [filtrování domén a organizačních jednotek](how-to-connect-install-custom.md#domain-and-ou-filtering) v Průvodci instalací nástroje. To také umožňuje připojení k doménovým strukturám, kde nejsou k dispozici všechny domény.
* [Plánovač](how-to-connect-sync-feature-scheduler.md) je integrovaný do synchronizačního modulu.

**Funkce, které jsou povýšené z verze Preview na GA:**

* [Zpětný zápis zařízení](how-to-connect-device-writeback.md)
* [Rozšíření adresáře](how-to-connect-sync-feature-directory-extensions.md).

**Nové funkce ve verzi Preview:**

* Nový výchozí interval synchronizačního cyklu je 30 minut. Používá se pro všechny předchozí verze tři hodiny. Přidá podporu pro změnu chování [plánovače](how-to-connect-sync-feature-scheduler.md) .

**Opravené problémy:**

* Stránka ověřit domény DNS nikdy nerozpoznala domény.
* Při konfiguraci AD FS vyzve k zadání přihlašovacích údajů správce domény.
* V případě, že se nachází v doméně s jiným stromem DNS než s kořenovou doménou, Průvodce instalací nerozpozná místní účty služby AD.

## <a name="1091310"></a>1.0.9131.0
Vydáno: prosinec 2015

**Opravené problémy:**

* Synchronizace hesla nemusí fungovat, když změníte hesla v Active Directory Domain Services (služba AD DS), ale funguje při nastavení hesla.
* Když máte proxy server, ověřování do služby Azure AD může během instalace selhat nebo pokud se upgrade na stránce konfigurace zrušil.
* Aktualizace z předchozí verze Azure AD Connect s úplnou SQL Server instancí se nezdařila, pokud nejste správcem systému SQL Server (SA).
* Aktualizace z předchozí verze Azure AD Connect se vzdáleným SQL Server zobrazuje chybu "nelze získat přístup k ADSync databázi SQL".

## <a name="1091250"></a>1.0.9125.0
Vydáno: listopadu 2015

**Nové funkce:**

* Může překonfigurovat AD FS na vztah důvěryhodnosti služby Azure AD.
* Může aktualizovat schéma služby Active Directory a znovu vygenerovat pravidla synchronizace.
* Může zakázat pravidlo synchronizace.
* Může definovat "AuthoritativeNull" jako nový literál v pravidle synchronizace.

**Nové funkce ve verzi Preview:**

* [Azure AD Connect Health pro synchronizaci](how-to-connect-health-sync.md).
* Podpora [Azure AD Domain Services](../user-help/active-directory-passwords-update-your-own-password.md) synchronizace hesel.

**Nový podporovaný scénář:**

* Podporuje několik místních organizací Exchange. Další informace najdete v tématu [hybridní nasazení s několika doménovými strukturami služby Active Directory](/previous-versions/exchange-server/exchange-150/jj873754(v=exchg.150)).

**Opravené problémy:**

* Potíže se synchronizací hesel:
  * Objekt přesunutý z oboru mimo rozsah do oboru nebude mít synchronizované heslo. To zahrnuje i filtrování organizačních jednotek i atributů.
  * Výběr nové organizační jednotky, která se má zahrnout do synchronizace, nevyžaduje úplnou synchronizaci hesel.
  * Když je povolen zakázaný uživatel, heslo se nesynchronizuje.
  * Fronta opakování hesla je nekonečná a předchozí limit 5 000 objektů, které mají být vyřazeny, byl odebrán.
* Nelze se připojit ke službě Active Directory s úrovní funkčnosti doménové struktury Windows Server 2016.
* Po počáteční instalaci není možné změnit skupinu, která se používá pro filtrování skupin.
* Již nevytváří nový profil uživatele na serveru Azure AD Connect pro každého uživatele, který provádí změnu hesla se zapnutým zpětným zápisem hesla.
* V oborech pravidel synchronizace není možné používat dlouhé celočíselné hodnoty.
* Zaškrtávací políčko "zpětný zápis zařízení" zůstává zakázané, pokud jsou k dispozici nedosažitelné řadiče domény.

## <a name="1086670"></a>1.0.8667.0
Vydáno: srpen 2015

**Nové funkce:**

* Průvodce instalací Azure AD Connect je teď lokalizovaný na všechny jazyky Windows serveru.
* Přidání podpory pro odemčení účtu při použití správy hesel služby Azure AD.

**Opravené problémy:**

* Průvodce instalací Azure AD Connect se zhroutí, pokud jiný uživatel pokračuje v instalaci, a ne osoba, která nejdřív spustila instalaci.
* Pokud se předchozí odinstalace Azure AD Connect nepovede k odinstalaci Azure AD Connect synchronizace, není možné ji znovu nainstalovat.
* Pokud uživatel není v kořenové doméně doménové struktury nebo pokud se používá jiná než anglická verze služby Active Directory, nejde Azure AD Connect nainstalovat pomocí Expresní instalace.
* Pokud nelze přeložit plně kvalifikovaný název domény uživatelského účtu služby Active Directory, zobrazí se zpráva zavádějící chybová zpráva s potvrzením schématu se nezdařilo.
* Pokud se účet použitý v konektoru služby Active Directory změní mimo průvodce, Průvodce při dalších spuštěních přestane.
* Azure AD Connect instalace na řadič domény se někdy nezdařila.
* Pokud byly přidány atributy rozšíření, nelze povolit a zakázat "pracovní režim".
* Zpětný zápis hesla v některých konfiguracích selhává kvůli chybnému heslu v konektoru služby Active Directory.
* DirSync nelze upgradovat, pokud je v filtrování atributů použit rozlišující název (DN).
* Nadměrné využití procesoru při použití resetování hesla.

**Odebrané funkce verze Preview:**

* [Zpětný zápis uživatele](how-to-connect-preview.md#user-writeback) do funkce Preview byl dočasně odebrán na základě názoru našich zákazníků ve verzi Preview. Po vyřešení poskytnuté zpětné vazby se později přidá.

## <a name="1086410"></a>1.0.8641.0
Vydáno: červen 2015

**Počáteční verze Azure AD Connect.**

Změnil se název z Azure AD Sync na Azure AD Connect.

**Nové funkce:**

* Instalace [expresního nastavení](how-to-connect-install-express.md)
* Může [nakonfigurovat AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs)
* Může [upgradovat z DirSync](how-to-dirsync-upgrade-get-started.md)
* [Prevence náhodného odstranění](how-to-connect-sync-feature-prevent-accidental-deletes.md)
* Představený [pracovní režim](how-to-connect-sync-staging-server.md)

**Nové funkce ve verzi Preview:**

* [Zpětný zápis uživatele](how-to-connect-preview.md#user-writeback)
* [Zpětný zápis zařízení](how-to-connect-device-writeback.md)
* [Rozšíření adresáře](how-to-connect-preview.md)

## <a name="104940501"></a>1.0.494.0501
Vydáno: květen 2015

**Nový požadavek:**

* Azure AD Sync teď vyžaduje, aby se nainstalovala .NET Framework verze 4.5.1.

**Opravené problémy:**

* Zpětný zápis hesla z Azure AD selhává kvůli chybě připojení Azure Service Bus.

## <a name="104910413"></a>1.0.491.0413
Vydáno: duben 2015

**Opravené problémy a vylepšení:**

* Pokud je koš povolený a v doménové struktuře je víc domén, nezpracovává se konektor služby Active Directory správně.
* Byl vylepšen výkon operací importu pro konektor Azure Active Directory.
* Pokud skupina překročila limit členství (ve výchozím nastavení je limit nastavený na 50 000 objektů), skupina se odstraní v Azure Active Directory. Při novém chování není skupina odstraněna, je vyvolána chyba a nové změny členství nebudou exportovány.
* Nový objekt nelze zřídit, pokud v prostoru konektoru již existuje dvoufázové odstranění se stejným rozlišujícím názvem.
* Některé objekty jsou označeny pro synchronizaci během rozdílové synchronizace, i když se u tohoto objektu nemění fáze.
* Vynucení synchronizace hesla také odstraní seznam upřednostňovaných řadičů domény.
* CSExportAnalyzer má problémy s některými stavy objektů.

**Nové funkce:**

* Připojení se teď může připojit k LIBOVOLNÉmu typu objektu v MV.

## <a name="104850222"></a>1.0.485.0222
Vydáno: únor 2015

**Vylepšení:**

* Vylepšený výkon při importu.

**Opravené problémy:**

* Synchronizace hesla respektuje atribut cloudFiltered, který používá filtrování atributů. Filtrované objekty již nejsou v oboru pro synchronizaci hesel.
* Ve výjimečných situacích, kdy topologie měla mnoho řadičů domény, synchronizace hesla nefunguje.
* "Zastaveno-Server" při importu z konektoru služby Azure AD po povolení správy zařízení ve službě Azure AD/Intune.
* Spojení cizích objektů zabezpečení (FSPs) z více domén ve stejné doménové struktuře způsobuje nejednoznačnou chybu připojení.

## <a name="104751202"></a>1.0.475.1202
Vydáno: prosinec 2014

**Nové funkce:**

* Synchronizace hesel pomocí filtrování založeného na atributu je teď podporovaná. Další informace najdete v tématu [Synchronizace hesel s filtrováním](how-to-connect-sync-configure-filtering.md).
* Atribut ms-DS-ExternalDirectoryObjectID se zapisuje zpátky do služby Active Directory. Tato funkce přidá podporu pro aplikace Microsoft 365. Používá OAuth2 k přístupu k online a místním poštovním schránkám v hybridním nasazení Exchange.

**Vyřešené problémy s upgradem:**

* Na serveru je k dispozici novější verze pomocníka pro přihlášení.
* K instalaci Azure AD Sync se použila vlastní cesta instalace.
* Upgrade má neplatné kritérium vlastního spojení.

**Další opravy:**

* Opravili jsme šablony pro Office pro plus.
* Opravené problémy při instalaci způsobené uživatelskými jmény, které začínají pomlčkou
* Opraveno ztratí nastavení sourceAnchor při dalším spuštění Průvodce instalací.
* Opravili trasování ETW pro synchronizaci hesel.

## <a name="104701023"></a>1.0.470.1023
Vydáno: říjen 2014

**Nové funkce:**

* Synchronizace hesel z několika místních Active Directory do Azure AD.
* Lokalizované uživatelské rozhraní instalace pro všechny jazyky Windows serveru.

**Upgrade z AADSync 1,0 GA**

Pokud jste už Azure AD Sync nainstalovanou, je potřeba provést ještě jeden krok, pokud jste změnili některá z nepřipravených synchronizačních pravidel. Po upgradu na verzi 1.0.470.1023 se synchronizační pravidla, která jste změnili, duplikují. U každého upraveného pravidla synchronizace proveďte tyto kroky:

1. Vyhledejte synchronizační pravidlo, které jste upravili, a poznamenejte si změny.
1. Odstraňte pravidlo synchronizace.
1. Vyhledejte nové pravidlo synchronizace vytvořené nástrojem Azure AD Sync a potom změny znovu použijte.

**Oprávnění pro účet služby Active Directory**

Aby bylo možné číst hodnoty hash hesel ze služby Active Directory, musí být účtu služby Active Directory udělena další oprávnění. Oprávnění pro udělení jsou pojmenována "replikace změn adresáře" a "replikace změn adresáře" vše. Aby bylo možné číst hodnoty hash hesel, musí být obě oprávnění.

## <a name="104190911"></a>1.0.419.0911
Vydáno: září 2014

**Počáteční verze Azure AD Sync.**

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
---
title: Ověřování – nástroj Microsoft Threat modelování – Azure | Microsoft Docs
description: způsoby zmírnění hrozeb, které jsou zveřejněné v nástroji pro modelování hrozeb
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: d53ade1e5c31ca25636b95d4f8b9e0fe29f9d081
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37031103"
---
# <a name="security-frame-authentication--mitigations"></a>Zabezpečení rámce: Ověřování | Způsoby zmírnění rizik 
| Produktům a službám | Článek |
| --------------- | ------- |
| **Webové aplikace**    | <ul><li>[Zvažte použití standardní ověřovací mechanismus ke svému ověření u webové aplikace](#standard-authn-web-app)</li><li>[Aplikace musí bezpečně zpracovávají scénáře selhání ověřování](#handle-failed-authn)</li><li>[Povolit krok nahoru nebo Adaptivní ověřování](#step-up-adaptive-authn)</li><li>[Ujistěte se, že jsou správně uzamčené rozhraní pro správu](#admin-interface-lockdown)</li><li>[Implementace zapomněli jste heslo funkce bezpečně](#forgot-pword-fxn)</li><li>[Uplatňování zásady hesla a účtu](#pword-account-policy)</li><li>[Implementovat kontroly, aby se zabránilo username – výčet](#controls-username-enum)</li></ul> |
| **Database** | <ul><li>[Pokud je to možné, použijte ověřování systému Windows pro připojení k systému SQL Server](#win-authn-sql)</li><li>[Pokud je to možné používejte ověřování Azure Active Directory pro připojení k databázi SQL](#aad-authn-sql)</li><li>[Když se používá režim ověřování systému SQL, ujistěte se, že účet a heslo, zásady se vynucují v systému SQL server](#authn-account-pword)</li><li>[Nepoužívejte v databázích s omezením ověřování SQL.](#autn-contained-db)</li></ul> |
| **Centra událostí Azure** | <ul><li>[Podle zařízení ověřování přihlašovacích údajů pomocí tokeny SaS](#authn-sas-tokens)</li></ul> |
| **Hranice vztahů důvěryhodnosti Azure** | <ul><li>[Povolení Azure Multi-Factor Authentication pro Azure správce](#multi-factor-azure-admin)</li></ul> |
| **Hranice vztahů důvěryhodnosti Service Fabric** | <ul><li>[Omezení anonymní přístup k Service Fabric Cluster](#anon-access-cluster)</li><li>[Zajistěte, aby byl certifikát klienta uzlu Service Fabric liší od certifikátu – uzly](#fabric-cn-nn)</li><li>[Použít k ověřování klientů pro clustery infrastruktury služby AAD](#aad-client-fabric)</li><li>[Ujistěte se, že certifikáty infrastruktury služby jsou získány z schválené certifikační autoritou (CA)](#fabric-cert-ca)</li></ul> |
| **Serveru identit** | <ul><li>[Použijte standardní ověřování scénáře nepodporuje serveru identit](#standard-authn-id)</li><li>[Přepsat výchozí Identity mezipaměti na serveru tokenu škálovatelné alternativou](#override-token)</li></ul> |
| **Počítač hranice vztahů důvěryhodnosti** | <ul><li>[Ujistěte se, že binární soubory nasazené aplikace jsou digitálně podepsané.](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Povolit ověřování při připojování k MSMQ fronty ve WCF](#msmq-queues)</li><li>[Proveďte WCF clientCredentialType zpráva není nastavena na hodnotu none](#message-none)</li><li>[Přenos clientCredentialType WCF proveďte není nastavena na hodnotu none](#transport-none)</li></ul> |
| **Webové rozhraní API** | <ul><li>[Ujistěte se, že standardní ověřování, postupy se používají k zabezpečení rozhraní Web API](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[Použijte standardní ověřování scénáře podporované službou Azure Active Directory](#authn-aad)</li><li>[Přepsat výchozí ADAL mezipamětí tokenů škálovatelné alternativou](#adal-scalable)</li><li>[Ujistěte se, že TokenReplayCache se používá při prevenci opětovného přehrání tokenů ověřování ADAL](#tokenreplaycache-adal)</li><li>[Správa žádosti o tokeny od klientů OAuth2 aad pomocí knihovny ADAL (nebo místní AD)](#adal-oauth2)</li></ul> |
| **Brána pole IoT** | <ul><li>[Ověření zařízení připojující se k bráně pole](#authn-devices-field)</li></ul> |
| **Brána IoT cloudu** | <ul><li>[Ujistěte se, že se zařízení připojující se ke cloudové brány ověří](#authn-devices-cloud)</li><li>[Použít pověření ověřování podle zařízení](#authn-cred)</li></ul> |
| **Azure Storage** | <ul><li>[Zajištění, aby pouze požadované kontejnery a objekty BLOB anonymní přístup pro čtení](#req-containers-anon)</li><li>[Udělit omezený přístup k objektům v úložišti Azure pomocí SAS nebo SAP](#limited-access-sas)</li></ul> |

## <a id="standard-authn-web-app"></a>Zvažte použití standardní ověřovací mechanismus ke svému ověření u webové aplikace

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| Podrobnosti | <p>Ověřování je proces, kde entity prokáže svou identitu, obvykle prostřednictvím přihlašovacích údajů, jako je například uživatelské jméno a heslo. Existuje více ověřovací protokoly dostupné kterých lze považovat za. Některé z nich, jsou uvedeny níže:</p><ul><li>Klientské certifikáty</li><li>Založené na Windows</li><li>Na základě formulářů</li><li>Federační - služby AD FS</li><li>Federační – Azure AD</li><li>Federační - serveru identit</li></ul><p>Zvažte použití standardní ověřovací mechanismus pro identifikaci zdroje procesu</p>|

## <a id="handle-failed-authn"></a>Aplikace musí bezpečně zpracovávají scénáře selhání ověřování

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| Podrobnosti | <p>Aplikace, které explicitně ověřování uživatelů musí bezpečně zpracovávat scénáře neúspěšné ověřování. Musí být tento mechanismus ověřování:</p><ul><li>Odepřít přístup k privilegované prostředky, pokud se ověření nezdaří</li><li>Zobrazit obecnou chybovou zprávu po selhání ověření a dojde k odepření přístupu</li></ul><p>Test pro:</p><ul><li>Ochranu privilegovaných prostředků po neúspěšných přihlášení</li><li>Zobrazí se obecnou chybovou zprávu o selhání ověřování a přístup odepřen událostí</li><li>Účty jsou zakázány po nadměrný počet neúspěšných pokusů o přihlášení</li><ul>|

## <a id="step-up-adaptive-authn"></a>Povolit krok nahoru nebo Adaptivní ověřování

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| Podrobnosti | <p>Ověřte aplikace má další autorizace (například krok nebo Adaptivní ověřování pomocí služby Multi-Factor authentication, například odeslání jednorázového HESLA v serveru SMS, e-mailu atd. nebo dotaz na opětovné ověření), je před udělením přístupu k postiženy uživatele citlivé informace. Toto pravidlo platí také pro provádění změn důležité účtu nebo akce</p><p>Zároveň to znamená, že přizpůsobení ověřování musí implementovat způsobem, aplikace správně vynucuje kontextová autorizace tak, aby se v příkladu nepovolíte neoprávněné manipulaci prostřednictvím parametru manipulaci</p>|

## <a id="admin-interface-lockdown"></a>Ujistěte se, že jsou správně uzamčené rozhraní pro správu

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| Podrobnosti | První řešením je poskytnout přístup pouze z určitý rozsah zdrojové IP rozhraní pro správu. Pokud toto řešení nebude možné, než je vždy nedoporučuje k vynucení přechody nebo Adaptivní ověřování pro přihlášení do rozhraní pro správu |

## <a id="forgot-pword-fxn"></a>Implementace zapomněli jste heslo funkce bezpečně

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| Podrobnosti | <p>První věc, kterou je ověřit, jestli zapomněli jste heslo a jiných cest obnovení odeslat odkaz včetně token časově omezené aktivace, nikoli heslo sám sebe. Další ověřování podle konfigurace soft tokeny (například SMS tokenu, nativní mobilní aplikace atd.) může být nutná také před odesláním přes odkaz. Druhý jste neměli zablokovat účet uživatele a zároveň probíhá proces získávání nové heslo.</p><p>To může vést k útoku DoS vždy, když útočník rozhodne na záměrně uzamčení uživatele s automatizované útoku. Třetí vždy, když požadavek na novou heslo bylo nastaveno v průběhu, zprávu, kterou můžete zobrazit by měl využívajících prevence výčtu uživatelské jméno. Čtvrté vždy zakáže použití původního hesla a implementovat zásady silné heslo.</p> |

## <a id="pword-account-policy"></a>Uplatňování zásady hesla a účtu

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| Podrobnosti | <p>Zásady účet a heslo v souladu s zásad organizace a osvědčené postupy by měla být implementována.</p><p>Bránit proti hrubou silou a slovník na základě uhodnutí: zásady silné heslo, musí být implementována zajistit, že uživatelé vytvářet složité heslo (např. minimální délka 12 znaků, alfanumerické a speciální znaky).</p><p>Zásady uzamčení účtu může být implementována následujícím způsobem:</p><ul><li>**Logicky uzamčení:** to může být vhodný pro ochranu před útoky hrubou silou vaši uživatelé. Například pokaždé, když uživatel zadá chybné heslo třikrát aplikace může zamknout účet pro minutu aby zpomalí hrubou vynucení jeho hesla. Díky tomu méně ziskové útočník by mohl pokračovat. Pokud byste chtěli implementovat pevný opatření na více systémů zámek pro tento příklad by dosáhnout a "Dos" podle trvale uzamykání účtů. Alternativně může aplikace generovat Jednorázovým (jedno heslo času) a odešle out-of-band (prostřednictvím e-mailu, sms atd.) pro uživatele. K implementaci CAPTCHA po dosažení prahová hodnota počtu neúspěšných pokusů o přihlášení může být jiná možnost.</li><li>**Pevné uzamčení:** tento typ uzamčení bude použito, vždy, když zjistit uživatele napadení vaší aplikace a čítač mu prostřednictvím trvale uzamčení účtu, dokud tým odpovědi měl čas jejich forenzních. Po tento proces, můžete se rozhodnout uživateli přidělit zpět svůj účet nebo provádět další právní akce u mu. Tento typ přístupu útočník zabrání další narušující vaše aplikace a infrastrukturu.</li></ul><p>Bránit proti útokům na výchozí a předvídatelný účtů, ověřte, zda všechny klíče a hesla jsou nahraditelné a jsou generována nebo změní za čas instalace.</p><p>Pokud aplikace má pro automatické generování hesel, zajistěte, aby vygenerované hesla jsou náhodných a mají vysokou šifrování.</p>|

## <a id="controls-username-enum"></a>Implementovat kontroly, aby se zabránilo username – výčet

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Všechny chybové zprávy by měl být zobecněn prevence výčtu uživatelské jméno. Také v některých případech se nelze vyhnout informace vracena v odkudkoli třeba na stránce registrace. Tady budete muset použít omezení rychlosti metody, třeba CAPTCHA, abyste zabránili automatizované útok uživatelem se zlými úmysly. |

## <a id="win-authn-sql"></a>Pokud je to možné, použijte ověřování systému Windows pro připojení k systému SQL Server

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | OnPrem |
| **Atributy**              | Verze SQL – všechny |
| **Odkazy**              | [SQL Server – volba režimu ověřování](https://msdn.microsoft.com/library/ms144284.aspx) |
| **Kroky** | Ověřování systému Windows používá bezpečnostní protokol Kerberos, umožňuje vynucování zásad hesel s ohledem na složitost ověření pro silná hesla, poskytuje podporu pro uzamčení účtu a vypršení platnosti hesla.|

## <a id="aad-authn-sql"></a>Pokud je to možné používejte ověřování Azure Active Directory pro připojení k databázi SQL

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | SQL Azure |
| **Atributy**              | Verze SQL - V12 |
| **Odkazy**              | [Připojení k databázi SQL pomocí ověřování Azure Active Directory](https://azure.microsoft.com/documentation/articles/sql-database-aad-authentication/) |
| **Kroky** | **Minimální verze:** Azure SQL Database V12 nutný pro povolení Azure SQL Database používat ověřování AAD proti Microsoft Directory |

## <a id="authn-account-pword"></a>Když se používá režim ověřování systému SQL, ujistěte se, že účet a heslo, zásady se vynucují v systému SQL server

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Zásady hesel systému SQL Server](https://technet.microsoft.com/library/ms161959(v=sql.110).aspx) |
| **Kroky** | Pokud používáte ověřování systému SQL Server, přihlášení se vytvoří v systému SQL Server, které nejsou založené na uživatelské účty systému Windows. Uživatelské jméno a heslo jsou vytvořené pomocí serveru SQL Server a uložené v systému SQL Server. SQL Server můžete použít mechanismy zásady hesel systému Windows. Můžete ji použít stejné složitost a zásadami pro konec platnosti hesla použit v rámci systému SQL Server používá v systému Windows. |

## <a id="autn-contained-db"></a>Nepoužívejte v databázích s omezením ověřování SQL.

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | OnPrem, SQL Azure |
| **Atributy**              | Verze - MSSQL2012, verze SQL - SQL verze 12 |
| **Odkazy**              | [Osvědčené postupy zabezpečení pomocí databáze s omezením](http://msdn.microsoft.com/library/ff929055.aspx) |
| **Kroky** | Neexistence zásadu vynucené heslo může zvýšit pravděpodobnost slabé pověření zavedeno v databázi s omezením. Ověřování systému Windows využívají. |

## <a id="authn-sas-tokens"></a>Podle zařízení ověřování přihlašovacích údajů pomocí tokeny SaS

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Centrum událostí Azure | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Ověřování a zabezpečení modelu přehled služby Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroky** | <p>Model zabezpečení služby Event Hubs je založena na kombinaci tokenů sdíleného přístupového podpisu (SAS) a zdroje událostí. Název vydavatele představuje ID zařízení, která přijímá token. To by pomoci přidružit tokeny vygenerovat pomocí příslušné zařízení.</p><p>Všechny zprávy jsou označené původce na straně služby povolení detekce falšování pokusy o původu ve datové části. Při ověřování zařízení, generovat za token SaS zařízení obor na jedinečný vydavatele.</p>|

## <a id="multi-factor-azure-admin"></a>Povolení Azure Multi-Factor Authentication pro Azure správce

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti Azure | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Co je Azure Multi-Factor Authentication?](https://azure.microsoft.com/documentation/articles/multi-factor-authentication/) |
| **Kroky** | <p>Vícefaktorové ověřování (MFA) je metoda ověřování, který vyžaduje více než jednu metodu ověřování a přidá velmi důležitou druhou vrstvu zabezpečení uživatelská přihlášení a transakce. Funguje tím, že jakékoliv dva nebo více z následujících metod ověřování:</p><ul><li>Něco znáte (obvykle heslo)</li><li>Něco co uživatel má (důvěryhodné zařízení, která není duplikovaná snadno, například telefon)</li><li>Něco že se (biometrika)</li><ul>|

## <a id="anon-access-cluster"></a>Omezení anonymní přístup k Service Fabric Cluster

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti Service Fabric | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | Prostředí – Azure  |
| **Odkazy**              | [Scénáře zabezpečení clusteru Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security) |
| **Kroky** | <p>Clustery musí být vždy zabezpečený neoprávněným uživatelům zabránit v připojení ke clusteru, zejména v případě, že je v něm spuštěny úlohy v produkčním prostředí.</p><p>Při vytváření service fabric cluster, ujistěte se, zda režim zabezpečení je nastavena na "secure (zabezpečeno) a nakonfigurovat požadovaný certifikát X.509 serveru. Vytváření clusteru služby "nezabezpečené" vám umožní všem anonymní uživatelům k nim připojit, pokud vystavuje koncové body správy do veřejného Internetu.</p>|

## <a id="fabric-cn-nn"></a>Zajistěte, aby byl certifikát klienta uzlu Service Fabric liší od certifikátu – uzly

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti Service Fabric | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | Prostředí – Azure, prostředí – samostatný |
| **Odkazy**              | [Zabezpečení certificate Service Fabric klientský uzel](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#_client-to-node-certificate-security), [připojení ke clusteru zabezpečené pomocí klientského certifikátu](https://azure.microsoft.com/documentation/articles/service-fabric-connect-to-secure-cluster/) |
| **Kroky** | <p>Při vytváření clusteru, buď prostřednictvím portálu Azure Resource Manager šablony nebo šablony JSON samostatné zadáním certifikát klienta správce nebo klientský certifikát uživatele konfigurace uzlu klientů certifikát zabezpečení.</p><p>Správce klienta a uživatelské certifikáty klienta, který zadáte, musí být jiné než primární a sekundární certifikáty, které zadáte pro zabezpečení mezi uzly.</p>|

## <a id="aad-client-fabric"></a>Použít k ověřování klientů pro clustery infrastruktury služby AAD

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti Service Fabric | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | Prostředí – Azure |
| **Odkazy**              | [Cluster scénáře zabezpečení - doporučení zabezpečení](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#security-recommendations) |
| **Kroky** | Clustery se systémem na platformě Azure můžete také zabezpečený přístup ke koncovým bodům správy pomocí Azure Active Directory (AAD), kromě klientských certifikátů. Pro Azure clusterů se doporučuje použít k ověřování klientů a certifikáty pro zabezpečení – uzly zabezpečení AAD.|

## <a id="fabric-cert-ca"></a>Ujistěte se, že certifikáty infrastruktury služby jsou získány z schválené certifikační autoritou (CA)

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti Service Fabric | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | Prostředí – Azure |
| **Odkazy**              | [X.509 – certifikáty a Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#x509-certificates-and-service-fabric) |
| **Kroky** | <p>Service Fabric používá certifikáty X.509 serveru za účelem ověřování totožnosti uzly a klienty.</p><p>Některé důležité co je třeba zvážit při používání certifikátů v service Fabric:</p><ul><li>Certifikáty používané v clusterech spuštění úlohy v produkčním prostředí by měla být vytvořen pomocí správně nakonfigurovaných certifikát služby Windows Server nebo získat ze schválené certifikační autoritou (CA). Certifikační Autorita může být schválené externí certifikační Autority nebo správně spravované interní infrastruktury veřejných klíčů (PKI)</li><li>Nikdy nepoužívejte žádné dočasných nebo testovací certifikáty v produkčním prostředí, které jsou vytvořeny pomocí nástrojů, jako je MakeCert.exe</li><li>Můžete použít certifikát podepsaný svým držitelem, ale měli tak učinit pouze pro testovací clustery a ne v produkčním prostředí</li></ul>|

## <a id="standard-authn-id"></a>Použijte standardní ověřování scénáře nepodporuje serveru identit

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Serveru identit | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [IdentityServer3 - velký obrázek](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Kroky** | <p>Tady jsou obvyklá interakce nepodporuje serveru identit:</p><ul><li>Prohlížeče komunikovat s webovými aplikacemi</li><li>Webové aplikace komunikovat s webových rozhraní API (někdy na své vlastní, někdy jménem uživatele)</li><li>Aplikace založené na prohlížeči komunikovat s webových rozhraní API</li><li>Nativní aplikace komunikovat s webových rozhraní API</li><li>Serverové aplikace komunikovat s webových rozhraní API</li><li>Webová rozhraní API komunikovat s webových rozhraní API (někdy na své vlastní, někdy jménem uživatele)</li></ul>|

## <a id="override-token"></a>Přepsat výchozí Identity mezipaměti na serveru tokenu škálovatelné alternativou

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Serveru identit | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Nasazení serveru identity - ukládání do mezipaměti](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Kroky** | <p>IdentityServer má jednoduché předdefinované mezipaměti v paměti. To je vhodné pro nativní aplikace v menším měřítku, není vhodné pro střední vrstvy a back-end aplikace z následujících důvodů:</p><ul><li>Tyto aplikace jsou dostupné přes mnoho uživatelů najednou. Ukládání všechny tokeny přístupu v úložišti stejné vytvoří izolace problémů a představuje výzvy při fungování ve velkém měřítku: mnoho uživatelů, každý s tolik tokeny jako prostředky aplikace přístup k jejich jménem, může zahrnovat vyhledávání velmi náročná operace a velký čísla</li><li>Tyto aplikace se obvykle nasazují na Distribuovaná topologie, kde více uzlů musí mít přístup ke stejným mezipaměti</li><li>V mezipaměti tokeny musí překonat proces recykluje a deaktivací</li><li>Pro všechny výše uvedené důvody při implementaci webové aplikace, se doporučuje přepsat výchozí Server Identity tokenu mezipaměti škálovatelné alternativou například Azure Redis cache</li></ul>|

## <a id="binaries-signed"></a>Ujistěte se, že binární soubory nasazené aplikace jsou digitálně podepsané.

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Počítač hranice vztahů důvěryhodnosti | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Ujistěte se, že binární soubory nasazené aplikace jsou digitálně podepsané, aby se dá ověřit integritu binárních souborů|

## <a id="msmq-queues"></a>Povolit ověřování při připojování k MSMQ fronty ve WCF

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné, NET Framework 3 |
| **Atributy**              | neuvedeno |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx) |
| **Kroky** | Program selže k povolení ověřování při připojení k frontám služby MSMQ, útočník může anonymně odeslání zprávy do fronty pro zpracování. Pokud ověření není používá pro připojení pro frontu MSMQ používá pro doručení zprávy do jiného programu, útočník může odeslat zprávu anonymní, který je škodlivý.|

### <a name="example"></a>Příklad:
`<netMsmqBinding/>` Prvek konfiguračního souboru WCF, které jsou níže dá pokyn WCF zakázat ověřování při připojování k frontu služby MSMQ pro doručování zpráv.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""None"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```
Konfigurace služby MSMQ tak, aby vyžadovala domény systému Windows nebo ověření certifikátu po celou dobu pro všechny příchozí nebo odchozí zprávy.

### <a name="example"></a>Příklad:
`<netMsmqBinding/>` Prvek konfiguračního souboru WCF, které jsou níže dá pokyn k povolení ověřování pomocí certifikátů, pokud se připojujete pro frontu MSMQ WCF. Klient je ověřen pomocí certifikátů X.509. Klientský certifikát musí být v úložišti certifikátů serveru.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""Certificate"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```

## <a id="message-none"></a>Proveďte WCF clientCredentialType zpráva není nastavena na hodnotu none

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Rozhraní .NET framework 3 |
| **Atributy**              | Typ pověření klienta - None |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení](https://vulncat.fortify.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_anonymous_message_client) |
| **Kroky** | Neexistence ověřování znamená, že všichni je mít přístup k této službě. Služba, která neověřuje svým klientům umožňuje přístup na všechny uživatele. Konfigurace aplikace pro ověřování na základě pověření klienta. Tento krok můžete provést nastavením clientCredentialType zpráv systému Windows nebo certifikátu. |

### <a name="example"></a>Příklad:
```
<message clientCredentialType=""Certificate""/>
```

## <a id="transport-none"></a>Přenos clientCredentialType WCF proveďte není nastavena na hodnotu none

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecná rozhraní .NET Framework 3 |
| **Atributy**              | Typ pověření klienta - None |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení](https://vulncat.hpefod.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_anonymous_transport_client) |
| **Kroky** | Neexistence ověřování znamená, že všichni je mít přístup k této službě. Služba, která neověřuje svým klientům umožňuje všem uživatelům přístup k jeho funkci. Konfigurace aplikace pro ověřování na základě pověření klienta. Tento krok můžete provést nastavením clientCredentialType přenosu Windows nebo certifikátu. |

### <a name="example"></a>Příklad:
```
<transport clientCredentialType=""Certificate""/>
```

## <a id="authn-secure-api"></a>Ujistěte se, že standardní ověřování, postupy se používají k zabezpečení rozhraní Web API

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Ověřování a autorizace v rozhraní ASP.NET Web API](http://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api), [externí ověřovací služby s rozhraním ASP.NET Web API (C#)](http://www.asp.net/web-api/overview/security/external-authentication-services) |
| **Kroky** | <p>Ověřování je proces, kde entity prokáže svou identitu, obvykle prostřednictvím přihlašovacích údajů, jako je například uživatelské jméno a heslo. Existuje více ověřovací protokoly dostupné kterých lze považovat za. Některé z nich, jsou uvedeny níže:</p><ul><li>Klientské certifikáty</li><li>Založené na Windows</li><li>Na základě formulářů</li><li>Federační - služby AD FS</li><li>Federační – Azure AD</li><li>Federační - serveru identit</li></ul><p>Odkazy v části odkazy obsahují nízké úrovně podrobnosti o tom, jak každý z schémat ověřování můžete implementují pro zabezpečení webového rozhraní API.</p>|

## <a id="authn-aad"></a>Použijte standardní ověřování scénáře podporované službou Azure Active Directory

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure AD | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Scénáře ověřování pro Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/), [Azure Active Directory ukázky kódu](https://azure.microsoft.com/documentation/articles/active-directory-code-samples/), [Příručka pro vývojáře Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-developers-guide/) |
| **Kroky** | <p>Azure Active Directory (Azure AD) zjednodušuje ověřování pro vývojáře a poskytovat identity jako služby, podpora pro standardní protokoly, jako je například OAuth 2.0 a OpenID Connect. V následující tabulce jsou pět scénáře primární aplikace podporované službou Azure AD:</p><ul><li>Webový prohlížeč k webové aplikaci: uživatel potřebuje k přihlášení k webové aplikaci, která je zabezpečená službou Azure AD</li><li>Jediné stránce aplikace (SPA): Uživatel musí pro přihlášení k jednostránkové aplikace, která je zabezpečená službou Azure AD</li><li>Nativní aplikace za účelem webového rozhraní API: nativní aplikaci, která běží na telefon, tablet nebo počítač potřebuje k ověření uživatele k získání prostředky z webového rozhraní API, která je zabezpečená službou Azure AD</li><li>Webové aplikace do webového rozhraní API: webová aplikace musí získat prostředky z webového rozhraní API, které jsou zabezpečené službou Azure AD</li><li>Démon procesu nebo serverové aplikace webového rozhraní API: démon aplikaci nebo serveru bez webového uživatelského rozhraní musí získat prostředky z webového rozhraní API, které jsou zabezpečené službou Azure AD</li></ul><p>Podrobnosti najdete na odkazy v části odkazy podrobnosti implementace nízké úrovně</p>|

## <a id="adal-scalable"></a>Přepsat výchozí ADAL mezipamětí tokenů škálovatelné alternativou

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure AD | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Moderní ověřování s Azure Active Directory pro webové aplikace](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/), [pomocí Redis jako ADAL mezipamětí tokenů](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **Kroky** | <p>Výchozí mezipaměti, která používá ADAL (Active Directory Authentication Library), je mezipaměti v paměti, které jsou závislé na statické úložiště, k dispozici úrovni procesu. Když tato metoda funguje u nativních aplikací, není vhodné pro střední vrstvy a back-end aplikace z následujících důvodů:</p><ul><li>Tyto aplikace jsou dostupné přes mnoho uživatelů najednou. Ukládání všechny tokeny přístupu v úložišti stejné vytvoří izolace problémů a představuje výzvy při fungování ve velkém měřítku: mnoho uživatelů, každý s tolik tokeny jako prostředky aplikace přístup k jejich jménem, může zahrnovat vyhledávání velmi náročná operace a velký čísla</li><li>Tyto aplikace se obvykle nasazují na Distribuovaná topologie, kde více uzlů musí mít přístup ke stejným mezipaměti</li><li>V mezipaměti tokeny musí překonat proces recykluje a deaktivací</li></ul><p>Pro všechny výše uvedené důvody při implementaci webové aplikace, se doporučuje přepsat výchozí ADAL mezipamětí tokenů škálovatelné alternativou například Azure Redis cache.</p>|

## <a id="tokenreplaycache-adal"></a>Ujistěte se, že TokenReplayCache se používá při prevenci opětovného přehrání tokenů ověřování ADAL

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure AD | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Moderní ověřování s Azure Active Directory pro webové aplikace](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) |
| **Kroky** | <p>Vlastnost TokenReplayCache umožňuje vývojářům vyhradit mezipaměť opětovného přehrání tokenu, úložiště, které lze použít pro ukládání tokeny pro účely ověření, že žádné token lze použít více než jednou.</p><p>Toto je míra proti útoku běžné útoku vhodně volané opětovného přehrání tokenu: útočník brání token odeslaný při přihlášení se může pokusit znovu odeslat do aplikace ("" Přehrajte si jej znovu) pro vytvoření nové relace. Například v OIDC kódu grant tok, po úspěšné ověření uživatele, požadavek na "/ signin-oidc" koncový bod předávající strany se provádí s "požadavku id_token", "kódu" a "stavu" parametry.</p><p>Předávající strana ověří tento požadavek a vytvoří novou relaci. Pokud nežádoucí osoba zaznamená tuto žádost a replays ho, si můžete určit úspěšné relace a zfalšovat uživatele. Přítomnost hodnotu nonce v OpenID Connect můžete omezit, ale není plně eliminovat v případech, ve kterých útok úspěšně použity. Vývojáři můžou chránit své aplikace, zadejte implementaci ITokenReplayCache a přiřadit TokenReplayCache instance.</p>|

### <a name="example"></a>Příklad:
```csharp
// ITokenReplayCache defined in ADAL
public interface ITokenReplayCache
{
bool TryAdd(string securityToken, DateTime expiresOn);
bool TryFind(string securityToken);
}
```

### <a name="example"></a>Příklad:
Zde je ukázka implementace rozhraní ITokenReplayCache. (Prosím přizpůsobit a implementovat vaše specifické pro projekt ukládání do mezipaměti framework)
```csharp
public class TokenReplayCache : ITokenReplayCache
{
    private readonly ICacheProvider cache; // Your project-specific cache provider
    public TokenReplayCache(ICacheProvider cache)
    {
        this.cache = cache;
    }
    public bool TryAdd(string securityToken, DateTime expiresOn)
    {
        if (this.cache.Get<string>(securityToken) == null)
        {
            this.cache.Set(securityToken, securityToken);
            return true;
        }
        return false;
    }
    public bool TryFind(string securityToken)
    {
        return this.cache.Get<string>(securityToken) != null;
    }
}
```
Implementovaná mezipaměť obsahuje bude odkazovat na možnosti OIDC přes vlastnost "parametry tokenvalidationparameters." následujícím způsobem.
```csharp
OpenIdConnectOptions openIdConnectOptions = new OpenIdConnectOptions
{
    AutomaticAuthenticate = true,
    ... // other configuration properties follow..
    TokenValidationParameters = new TokenValidationParameters
    {
        TokenReplayCache = new TokenReplayCache(/*Inject your cache provider*/);
    }
}
```

Pamatujte, že pro testování efektivitu této konfigurace přihlášení do místní OIDC chráněné aplikace a zaznamenat požadavek na `"/signin-oidc"` koncového bodu v aplikaci fiddler. Pokud ochranu není na místě, přehrání této žádosti v aplikaci fiddler nastaví nového souboru cookie relace. Při požadavku je přehrány po přidání ochrany TokenReplayCache, aplikace bude vyvolána výjimka následujícím způsobem: `SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a id="adal-oauth2"></a>Správa žádosti o tokeny od klientů OAuth2 aad pomocí knihovny ADAL (nebo místní AD)

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure AD | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [ADAL](https://azure.microsoft.com/documentation/articles/active-directory-authentication-libraries/) |
| **Kroky** | <p>Azure AD authentication Library (ADAL) umožňuje vývojářům aplikací klienta snadno ověřování uživatelů do cloudu nebo místní služby Active Directory (AD) a získat přístupové tokeny zabezpečení volání rozhraní API.</p><p>ADAL obsahuje mnoho funkcí, zkontrolujte ověření snazší pro vývojáře, jako je třeba asynchronní podpora, konfigurovat tokenu mezipaměti, která ukládá přístupové tokeny a obnovovacích tokenů, automatické aktualizace tokenu, když vyprší platnost přístupového tokenu a obnovovací token je k dispozici, a Další.</p><p>Pomocí zpracování většinu složitost, můžete ADAL pomáhají vývojáře zaměřená na obchodní logiku ve svých aplikacích a snadno zabezpečení prostředků, aniž by musel být odborník na zabezpečení. Samostatné knihovny jsou k dispozici pro rozhraní .NET, JavaScript (klient a Node.js), iOS, Android a Java.</p>|

## <a id="authn-devices-field"></a>Ověření zařízení připojující se k bráně pole

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána pole IoT | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Ujistěte se, že každé zařízení je ověřována brána pole před přijetím data z nich a před usnadnění nadřazeného komunikace s Cloudová brána. Také se ujistěte, že zařízení připojit s na každé zařízení přihlašovacích údajů, aby jednotlivých zařízení je možné jednoznačně identifikovat.|

## <a id="authn-devices-cloud"></a>Ujistěte se, že se zařízení připojující se ke cloudové brány ověří

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána IoT cloudu | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné, C#, Node.JS,  |
| **Atributy**              | Není k dispozici, brány volba - Azure IoT Hub |
| **Odkazy**              | Není k dispozici, [Azure IoT hub pro rozhraní .NET](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/), [Začínáme se službou IoT hub wih a uzel JS](https://azure.microsoft.com/documentation/articles/iot-hub-node-node-getstarted), [zabezpečení IoT SAS a certifikáty](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/), [úložiště Git](https://github.com/Azure/azure-iot-sdks/tree/master/node) |
| **Kroky** | <ul><li>**Obecné:** ověření zařízení pomocí zabezpečení TLS (Transport Layer) nebo protokol IPSec. Infrastruktura by měla podporovat použití předsdílený klíč (PSK) na těchto zařízeních, které nelze zpracovat úplné asymetrické šifrování. Využijte Azure AD, Oauth.</li><li>**C#:** při vytváření DeviceClient instance, ve výchozím nastavení, metodu Create vytvoří instanci DeviceClient, který používá protokol AMQP komunikovat s centrem IoT. Pokud chcete používat protokol HTTPS, použijte přepis metody Create, která umožňuje určit protokol. Pokud používáte protokol HTTPS, měli byste také přidat `Microsoft.AspNet.WebApi.Client` balíček NuGet do projektu zahrnout `System.Net.Http.Formatting` oboru názvů.</li></ul>|

### <a name="example"></a>Příklad:
```csharp
static DeviceClient deviceClient;

static string deviceKey = "{device key}";
static string iotHubUri = "{iot hub hostname}";

var messageString = "{message in string format}";
var message = new Message(Encoding.ASCII.GetBytes(messageString));

deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

await deviceClient.SendEventAsync(message);
```

### <a name="example"></a>Příklad:
**Node.JS: ověřování**
#### <a name="symmetric-key"></a>Symetrický klíč
* Vytvoření centra IoT v azure
* Vytvořit položku v registru identit zařízení
    ```javascript
    var device = new iothub.Device(null);
    device.deviceId = <DeviceId >
    registry.create(device, function(err, deviceInfo, res) {})
    ```
* Vytvoření simulovaného zařízení
    ```javascript
    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>SharedAccessKey=<SharedAccessKey>';
    var client = clientFromConnectionString(connectionString);
    ```
#### <a name="sas-token"></a>Token SAS
* Získá generované interně při použití symetrický klíč, ale nemůžeme můžete vygenerovat a použít jej explicitně také
* Definujte protokol: `var Http = require('azure-iot-device-http').Http;`
* Vytvořte sas token:
    ```javascript
    resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();
    var deviceName = "<deviceName >";
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    var toSign = resourceUri + '\n' + expires;
    // using crypto
    var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
    const hmac = crypto.createHmac('sha256', decodedPassword);
    hmac.update(toSign);
    var base64signature = hmac.digest('base64');
    var base64UriEncoded = encodeURIComponent(base64signature);
    // construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "%2fdevices%2f"+deviceName+"&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
    ```
* Připojte pomocí tokenu sas: 
    ```javascript
    Client.fromSharedAccessSignature(sas, Http); 
    ```
#### <a name="certificates"></a>Certifikáty
* Generovat vlastní podepsané X509 certifikátů pomocí libovolného nástroje, jako je například OpenSSL pro vygenerování .cert a s příponou Key souborů k ukládání certifikát a klíč v uvedeném pořadí
* Zřídit zařízení, které přijímá zabezpečené připojení pomocí certifikátů.
    ```javascript
    var connectionString = '<connectionString>';
    var registry = iothub.Registry.fromConnectionString(connectionString);
    var deviceJSON = {deviceId:"<deviceId>",
    authentication: {
        x509Thumbprint: {
        primaryThumbprint: "<PrimaryThumbprint>",
        secondaryThumbprint: "<SecondaryThumbprint>"
        }
    }}
    var device = deviceJSON;
    registry.create(device, function (err) {});
    ```
* Připojení zařízení pomocí certifikátu
    ```javascript
    var Protocol = require('azure-iot-device-http').Http;
    var Client = require('azure-iot-device').Client;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>x509=true';
    var client = Client.fromConnectionString(connectionString, Protocol);
    var options = {
        key: fs.readFileSync('./key.pem', 'utf8'),
        cert: fs.readFileSync('./server.crt', 'utf8')
    }; 
    // Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client //transport to use x509 when connecting to IoT Hub
    client.setOptions(options);
    //call fn to execute after the connection is set up
    client.open(fn);
    ```

## <a id="authn-cred"></a>Použít pověření ověřování podle zařízení

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána IoT cloudu  | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | Volba brány - Azure IoT Hub |
| **Odkazy**              | [Tokeny zabezpečení Azure IoT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) |
| **Kroky** | Použití za použití tokeny SaS pověření ověřování zařízení na základě klíč zařízení nebo klientský certifikát, místo IoT Hub úrovni sdílených zásad přístupu. Zabrání se tak opakované použití ověřování tokenů jednoho zařízení nebo pole brány jiným |

## <a id="req-containers-anon"></a>Zajištění, aby pouze požadované kontejnery a objekty BLOB anonymní přístup pro čtení

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | StorageType – objekt Blob |
| **Odkazy**              | [Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/), [sdílené přístupové podpisy, část 1: vysvětlení modelu SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) |
| **Kroky** | <p>Ve výchozím kontejneru a všechny objekty BLOB v něm může být přístupné jenom vlastník účtu úložiště. Umožnit anonymní uživatelé oprávnění ke čtení pro kontejner a jeho objekty BLOB jeden můžete nastavit kontejner oprávnění, která umožní veřejný přístup. Anonymní uživatelé mohou číst objekty BLOB do kontejneru, veřejně přístupný bez ověřování žádosti.</p><p>Kontejnery poskytují následující možnosti pro správu přístupu kontejneru:</p><ul><li>Úplný veřejný přístup pro čtení: přes anonymní dotazy můžete číst data kontejnerů a objektů blob. Klienti, můžete vytvořit výčet objektů BLOB v kontejneru přes anonymní žádost, ale nemůže vytvořit výčet kontejnery v rámci účtu úložiště.</li><li>Veřejný přístup pro objekty BLOB pouze pro čtení: data objektu Blob v tomto kontejneru mohou číst přes anonymní žádost, ale kontejneru data nejsou k dispozici. Klienty nelze vytvořit výčet objektů BLOB v kontejneru přes anonymní dotazy</li><li>Žádný veřejný přístup pro čtení: kontejnerů a objektů blob dat může číst pouze majiteli účtu</li></ul><p>Anonymní přístup je nejvhodnější pro scénáře, kde některé objekty BLOB musí být vždy k dispozici pro anonymní přístup pro čtení. Pro řízení citlivější jeden můžete vytvořit sdílený přístupový podpis, který umožňuje delegáta omezený přístup pomocí různých oprávnění a v zadaném časovém intervalu. Zajištění, aby kontejnery a objekty BLOB, které mohou obsahovat potenciálně citlivá data, nejsou anonymní přístup omylem</p>|

## <a id="limited-access-sas"></a>Udělit omezený přístup k objektům v úložišti Azure pomocí SAS nebo SAP

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno |
| **Odkazy**              | [Sdílené přístupové podpisy, část 1: Vysvětlení modelu SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/), [sdílené přístupové podpisy, část 2: vytvoření a použití SAS s úložištěm Blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/), [postup delegovat přístup k objektům v účtu pomocí sdíleného Podpisy přístupu a zásady uložené přístupu](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies) |
| **Kroky** | <p>Použití sdíleného přístupového podpisu (SAS) je efektivní způsob, jak udělit omezený přístup k objektům v účtu úložiště pro ostatní klienty, aniž by bylo nutné vystavit přístupový klíč účtu. SAS je identifikátor URI, který zahrnuje v jeho parametry dotazu všechny informace potřebné pro ověřený přístup k prostředku úložiště. Pro přístup k prostředkům úložiště s SAS, klient pouze musí předat SAS metodu, nebo odpovídající konstruktor.</p><p>SAS můžete použít, pokud chcete poskytnout přístup k prostředkům ve vašem účtu úložiště do klienta, který nemůže být považován za důvěryhodný klíč účtu. Klíče účtu úložiště zahrnují jak primární a sekundární klíč, které obě udělit přístup pro správu pro váš účet a všechny prostředky v ní. Vystavení buď klíče účtu otevře účet tak, aby možnost škodlivý nebo nedbalosti použití. Sdílené přístupové podpisy zadejte bezpečné alternativu, která umožňuje dalším klientům čtení, zápisu a odstranění dat ve vašem účtu úložiště podle oprávnění, která jste udělena a bez nutnosti klíč účtu.</p><p>Pokud máte logickou sadu parametrů, které jsou podobné pokaždé, když, použití uložené přístup zásad (SAP) je lepší představu. Protože pomocí SAS odvozené od zásadu uložené přístupu vám dává možnost odvolat tuto SAS okamžitě, je vždy nutné použít uložené Pokud je to možné zásady přístupu doporučený osvědčený postup.</p>|
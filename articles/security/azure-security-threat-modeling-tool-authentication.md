---
title: Ověřování – nástroj pro modelování hrozeb Microsoftu – Azure | Dokumentace Microsoftu
description: zmírnění rizik pro hrozby v nástroj pro modelování hrozeb
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 23e219fa49146158c97f392427eee7c42c347a7a
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307416"
---
# <a name="security-frame-authentication--mitigations"></a>Zabezpečení Frame: Ověřování | Zmírnění rizik 
| Produkt nebo službu | Článek |
| --------------- | ------- |
| **Webové aplikace**    | <ul><li>[Zvažte možnost použít standardní ověřovací mechanismus ověřování do webové aplikace](#standard-authn-web-app)</li><li>[Aplikace musí zvládnout bezpečně scénáře selhání ověření](#handle-failed-authn)</li><li>[Povolit krok nahoru nebo Adaptivní ověřování](#step-up-adaptive-authn)</li><li>[Ujistěte se, že jsou správně uzamčena rozhraní pro správu](#admin-interface-lockdown)</li><li>[Implementace zapomněli jste heslo funkce bezpečně](#forgot-pword-fxn)</li><li>[Ujistěte se, že jsou implementovány zásady hesla a účtu](#pword-account-policy)</li><li>[Implementace ovládacích prvků, aby se zabránilo výčet uživatelské jméno](#controls-username-enum)</li></ul> |
| **Database** | <ul><li>[Pokud je to možné, používejte ověřování Windows pro připojení k serveru SQL Server](#win-authn-sql)</li><li>[Pokud je to možné, použijte ověřování Azure Active Directory pro připojení k SQL Database](#aad-authn-sql)</li><li>[Při použití režimu ověřování SQL, ujistěte se, že účet a heslo, zásady se vynucují na SQL serveru](#authn-account-pword)</li><li>[Nepoužívejte ověřování serveru SQL v databázích s omezením](#autn-contained-db)</li></ul> |
| **Centrum událostí Azure** | <ul><li>[Podle zařízení ověřování přihlašovacích údajů a tokeny SaS](#authn-sas-tokens)</li></ul> |
| **Hranice vztahů důvěryhodnosti Azure** | <ul><li>[Povolit ověřování Azure Multi-Factor Authentication pro správce Azure](#multi-factor-azure-admin)</li></ul> |
| **Hranice vztahů důvěryhodnosti Service Fabric** | <ul><li>[Omezit anonymní přístup ke clusteru Service Fabric](#anon-access-cluster)</li><li>[Ujistěte se, že certifikát klienta uzlu Service Fabric se liší od certifikátu mezi uzly](#fabric-cn-nn)</li><li>[Použít AAD k ověřování klientů pro clustery service fabric](#aad-client-fabric)</li><li>[Ujistěte se, že service fabric certifikáty jsou získány z schválené certifikační autority (CA)](#fabric-cert-ca)</li></ul> |
| **Serveru identit** | <ul><li>[Použít standardní ověřovací scénáře podporované serverem Identity](#standard-authn-id)</li><li>[Přepsat výchozí token mezipaměti serveru identit se škálovatelnou alternativu](#override-token)</li></ul> |
| **Hranice vztahů důvěryhodnosti počítače** | <ul><li>[Ujistěte se, že binární soubory nasazené aplikace jsou digitálně podepsané](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Povolení ověřování při připojování ke službě MSMQ fronty ve WCF](#msmq-queues)</li><li>[Nebyl typ clientCredentialType zpráv WCF není nastavený na hodnotu none](#message-none)</li><li>[Nebyl typ clientCredentialType přenosu WCF není nastavený na hodnotu none](#transport-none)</li></ul> |
| **Webové rozhraní API** | <ul><li>[Ujistěte se, že standardní ověřování techniky se používají k zabezpečení webových rozhraní API](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[Použít standardní ověřovací scénáře podporované službou Azure Active Directory](#authn-aad)</li><li>[Přepsat výchozí mezipaměť tokenů ADAL s škálovatelnou alternativu](#adal-scalable)</li><li>[Ujistěte se, že TokenReplayCache se používá při prevenci opakování tokenů ověřování ADAL](#tokenreplaycache-adal)</li><li>[Spravovat žádosti o tokeny od klientů OAuth2 k AAD pomocí knihovny ADAL (nebo místní AD)](#adal-oauth2)</li></ul> |
| **Brána IoT pole** | <ul><li>[Ověřování zařízení připojování k bráně pole](#authn-devices-field)</li></ul> |
| **Brána IoT cloudu** | <ul><li>[Ujistěte se, že se ověření zařízení připojující se k Cloudová brána](#authn-devices-cloud)</li><li>[Použít pověření ověřování podle zařízení](#authn-cred)</li></ul> |
| **Azure Storage** | <ul><li>[Ujistěte se, že jsou uvedeny pouze požadované kontejnerům a objektům BLOB anonymní přístup pro čtení](#req-containers-anon)</li><li>[Udělit omezený přístup k objektům ve službě Azure storage pomocí SAS nebo SAP](#limited-access-sas)</li></ul> |

## <a id="standard-authn-web-app"></a>Zvažte možnost použít standardní ověřovací mechanismus ověřování do webové aplikace

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| Podrobnosti | <p>Ověřování je proces, ve kterém entita prokáže svoji identitu, obvykle pomocí přihlašovacích údajů, jako je například uživatelské jméno a heslo. Existuje více ověřovací protokoly dostupné kterých lze považovat za. Některé z nich jsou uvedeny níže:</p><ul><li>Klientské certifikáty</li><li>Založené na Windows</li><li>Na základě formulářů</li><li>Federace – služby AD FS</li><li>Federace – Azure AD</li><li>Federace - serveru identit</li></ul><p>Zvažte možnost použít standardní ověřovací mechanismus pro identifikaci zdroje procesu</p>|

## <a id="handle-failed-authn"></a>Aplikace musí zvládnout bezpečně scénáře selhání ověření

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| Podrobnosti | <p>Aplikace, které explicitně ověřování uživatelů musí umět zpracovat selhání ověření scénáře bezpečně. Musí se mechanismus ověřování:</p><ul><li>Odepřít přístup k privilegovaným prostředky při ověřování se nezdaří</li><li>Po neúspěšném ověření zobrazí obecné chybové zprávy a dojde k odepření přístupu</li></ul><p>Test pro:</p><ul><li>Ochranu privilegovaných prostředků po neúspěšných přihlášení</li><li>Zobrazí se obecné chybové zprávy o selhání ověřování a přístup odepřen události</li><li>Účty jsou zakázány po nadměrný počet neúspěšných pokusů o přihlášení</li><ul>|

## <a id="step-up-adaptive-authn"></a>Povolit krok nahoru nebo Adaptivní ověřování

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| Podrobnosti | <p>Ověřte aplikace má další ověření (třeba na vyšší edici ještě nebo Adaptivní ověřování pomocí služby Multi-Factor authentication, jako je odeslání jednorázového HESLA v zprávu SMS, e-mail atd. nebo vás vyzve k zadání opakované ověření) tak, že uživatel vyzván před udělením přístupu k citlivé informace. Toto pravidlo platí také pro kritické změn k účtu nebo akce</p><p>Zároveň to znamená, že přizpůsobení ověřování musí být implementovány způsobem, že aplikace správně vynucuje kontextové autorizace tak, aby se v příkladu, nepovoluje neoprávněné manipulace prostřednictvím parametru manipulaci</p>|

## <a id="admin-interface-lockdown"></a>Ujistěte se, že jsou správně uzamčena rozhraní pro správu

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| Podrobnosti | První řešení je udělit přístup pouze z určitého rozsahu zdrojové IP rozhraní pro správu. Pokud toto řešení nebude možné, než je vždy nedoporučuje k vynucení přechody nebo Adaptivní ověřování pro přihlášení do rozhraní pro správu |

## <a id="forgot-pword-fxn"></a>Implementace zapomněli jste heslo funkce bezpečně

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| Podrobnosti | <p>První věc, kterou je pro ověření, že si vzpomenout na heslo a jiné cesty obnovení odešlete odkaz včetně časově omezené aktivační token, nikoli samotného heslo. Další ověření podle konfigurace soft tokeny (například SMS token, nativní mobilní aplikace atd.) může být vyžaduje také před odesláním přes odkaz. Za druhé můžete by neměl uzamknete účet uživatele zatímco probíhá proces načítání nové heslo.</p><p>To může vést k útoku DoS pokaždé, když se útočník rozhodne záměrně uzamčení uživatele pomocí automatizovaných útoku. Třetí pokaždé, když se nová žádost o hesla bylo nastaveno v průběhu, by mělo být zpráva, kterou můžete zobrazit obecné prevence výčet uživatelské jméno. Čtvrtý vždy zakáže použití starého hesla a implementovat zásady silné heslo.</p> |

## <a id="pword-account-policy"></a>Ujistěte se, že jsou implementovány zásady hesla a účtu

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| Podrobnosti | <p>Zásady účet a heslo v souladu s organizační zásady a osvědčené postupy by měla být implementována.</p><p>Pro ochranu před útoky hrubou silou a opakovaně uhodnout slovníku na základě: Chcete-li zajistit, aby uživatelé vytvářet složité heslo (například minimální délku 12 znaky, alfanumerické a speciální znaky) musí být implementované zásady silné heslo.</p><p>Zásady uzamčení účtu může implementovat následujícím způsobem:</p><ul><li>**Obnovitelně uzamčení:** to může být dobrou volbou pro ochranu před útoky hrubou silou vaši uživatelé. Například pokaždé, když uživatel zadá chybné heslo třikrát aplikace může zamknout účet minutu Pokud chcete zpomalit proces hrubou vynucení jeho hesla. Díky tomu je méně ziskové útočníkovi, aby bylo možné pokračovat. Pokud byste chtěli implementace pevné protiopatření uzamčení v tomto příkladu by dosáhnout "DOS" podle trvale uzamčení účtů. Alternativně může aplikace generovat ověřování jednorázovým HESLEM (jeden čas heslo) a odesílají je out-of-band (prostřednictvím e-mailu, sms atd.) pro uživatele. K implementaci test CAPTCHA. po dosažení prahové hodnoty počtu neúspěšných pokusů o přihlášení může být jiný přístup.</li><li>**Pevné uzamčení:** tohoto typu uzamčení bude použito pokaždé, když se rozpoznat uživatele na vaše aplikace a čítač mu prostřednictvím trvale uzamčení jeho účet, dokud odpovědi tým měl čas jejich forenzní účely. Po dokončení tohoto procesu můžete dát uživateli zpět jeho účet nebo provést další právní akce proti mu. Tento typ přístupu přináší útočník zabrání další pronikající svoji aplikaci a infrastruktuře.</li></ul><p>Chránit před útoky zaměřenými na výchozí a předvídatelné účty, ověřte, že všechny klíče a hesla replaceable a jsou vygenerována nebo nahradit po instalaci.</p><p>Pokud má aplikace pro automatické generování hesel, zajistěte, aby vygenerované hesla se náhodný a mají vysokou entropie.</p>|

## <a id="controls-username-enum"></a>Implementace ovládacích prvků, aby se zabránilo výčet uživatelské jméno

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Aby se zabránilo výčet uživatelské jméno by mělo být obecné všech chybových zpráv. Také v některých případech nelze vyhnout nevrácení v funkce, jako jsou stránky registrační informace. Tady budete muset použít omezení rychlosti metody, jako je test CAPTCHA. aby se zabránilo automatické útoku útočník. |

## <a id="win-authn-sql"></a>Pokud je to možné, používejte ověřování Windows pro připojení k serveru SQL Server

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | OnPrem |
| **Atributy**              | Verze SQL - všechny |
| **Odkazy**              | [SQL Server – volba režimu ověřování](https://msdn.microsoft.com/library/ms144284.aspx) |
| **Kroky** | Ověřování Windows používá bezpečnostní protokol Kerberos, umožňuje vynucování zásad pro hesla s ohledem na složitost ověření pro silná hesla, poskytuje podporu pro uzamčení účtu a vypršení platnosti hesla.|

## <a id="aad-authn-sql"></a>Pokud je to možné, použijte ověřování Azure Active Directory pro připojení k SQL Database

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | SQL Azure |
| **Atributy**              | Verze SQL - V12 |
| **Odkazy**              | [Připojení k SQL Database pomocí ověřování Azure Active Directory](https://azure.microsoft.com/documentation/articles/sql-database-aad-authentication/) |
| **Kroky** | **Minimální verze:** potřeba povolit Azure SQL Database a ověřování AAD Directory Microsoft Azure SQL Database V12 |

## <a id="authn-account-pword"></a>Při použití režimu ověřování SQL, ujistěte se, že účet a heslo, zásady se vynucují na SQL serveru

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Zásad hesel SQL serveru](https://technet.microsoft.com/library/ms161959(v=sql.110).aspx) |
| **Kroky** | Při použití ověřování SQL serveru, přihlašovací údaje jsou vytvořeny v systému SQL Server, které nejsou založené na Windows uživatelské účty. Uživatelské jméno a heslo jsou vytvořeny pomocí systému SQL Server a uložená v SQL serveru. Systém SQL Server můžete použít Windows heslo zásad mechanismech. Může používat stejný složitost a zásadami pro konec platnosti používané Windows to hesel použitých v SQL serveru. |

## <a id="autn-contained-db"></a>Nepoužívejte ověřování serveru SQL v databázích s omezením

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Místního prostředí, SQL Azure |
| **Atributy**              | Verze SQL verze - MSSQL2012, verze SQL - 12 |
| **Odkazy**              | [Osvědčené postupy zabezpečení s databáze s omezením](http://msdn.microsoft.com/library/ff929055.aspx) |
| **Kroky** | Neexistence zásadu vynucené heslo může zvýšit pravděpodobnost, že slabé přihlašovacích údajů zavedeno v databázi s omezením. Využijte ověřování Windows. |

## <a id="authn-sas-tokens"></a>Podle zařízení ověřování přihlašovacích údajů a tokeny SaS

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Centrum událostí Azure | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Ověřování a zabezpečení modelu přehled služby Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroky** | <p>Model zabezpečení služby Event Hubs je založen na kombinaci tokeny sdíleného přístupového podpisu (SAS) a zdroje událostí. Název vydavatele představuje ID zařízení, která přijímá token. To by pomohl přiřadit tokenů generuje s použitím příslušných zařízení.</p><p>Všechny zprávy jsou označené původce na straně služby povolení detekce původu ve datové části falšování pokusy. Při ověřování zařízení, generovat za token SaS zařízení omezená na jedinečné vydavatele.</p>|

## <a id="multi-factor-azure-admin"></a>Povolit ověřování Azure Multi-Factor Authentication pro správce Azure

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti Azure | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Co je Azure Multi-Factor Authentication?](https://azure.microsoft.com/documentation/articles/multi-factor-authentication/) |
| **Kroky** | <p>Vícefaktorové ověřování (MFA) je metoda ověřování, který vyžaduje více než jednu metodu ověřování a přidá do uživatelská přihlášení a transakce velmi důležitou druhou vrstvu zabezpečení. Funguje tak, že vyžaduje jakékoliv dvě nebo více z následujících metod ověřování:</p><ul><li>Něco víte (obvykle heslo)</li><li>Něco, co máte (důvěryhodné zařízení, které není lehké duplikovat, jako je telefon)</li><li>Něco, co že je (vaše biometrika)</li><ul>|

## <a id="anon-access-cluster"></a>Omezit anonymní přístup ke clusteru Service Fabric

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti Service Fabric | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | Prostředí – Azure  |
| **Odkazy**              | [Scénáře zabezpečení clusteru Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security) |
| **Kroky** | <p>Clustery musí být vždy svázán s zabránit neoprávněným uživatelům v připojení ke clusteru, zejména v případě, že se v něm spuštěné úlohy v produkčním prostředí.</p><p>Při vytváření clusteru service fabric, zkontrolujte, že režim zabezpečení je nastavená na "Secure (zabezpečeno) a nakonfigurovat požadovaný server certifikát X.509. Vytvoření clusteru služby "nezabezpečené" vám umožní jakékoli anonymního uživatele se k němu připojit, pokud ho zpřístupní koncových bodů správy do veřejného Internetu.</p>|

## <a id="fabric-cn-nn"></a>Ujistěte se, že certifikát klienta uzlu Service Fabric se liší od certifikátu mezi uzly

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti Service Fabric | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | Prostředí – Azure, prostředí – samostatné |
| **Odkazy**              | [Service Fabric uzel klienta certifikát zabezpečení](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#_client-to-node-certificate-security), [připojit k zabezpečenému clusteru pomocí certifikátu klienta](https://azure.microsoft.com/documentation/articles/service-fabric-connect-to-secure-cluster/) |
| **Kroky** | <p>Konfigurace zabezpečení certifikátu klienta uzlu při vytváření clusteru, buď prostřednictvím webu Azure portal, šablon Resource Manageru nebo samostatné šablony JSON tak, že zadáte certifikát klienta pro správu a/nebo klientský certifikát uživatele.</p><p>Správce klientů a uživatelů klientské certifikáty, které zadáte, by měl být jiné než primární a sekundární certifikát, který zadáte pro zabezpečení mezi uzly.</p>|

## <a id="aad-client-fabric"></a>Použít AAD k ověřování klientů pro clustery service fabric

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti Service Fabric | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | Prostředí – Azure |
| **Odkazy**              | [Scénáře zabezpečení - doporučení zabezpečení clusteru](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#security-recommendations) |
| **Kroky** | Clustery spuštěné v Azure můžete také zabezpečený přístup ke koncovým bodům správy pomocí Azure Active Directory (AAD), kromě klientských certifikátů. Pro clustery Azure se doporučuje použít zabezpečení AAD k ověřování klientů a certifikáty pro zabezpečení mezi uzly.|

## <a id="fabric-cert-ca"></a>Ujistěte se, že service fabric certifikáty jsou získány z schválené certifikační autority (CA)

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti Service Fabric | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | Prostředí – Azure |
| **Odkazy**              | [Certifikáty X.509 a Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#x509-certificates-and-service-fabric) |
| **Kroky** | <p>Service Fabric používá certifikáty X.509 serveru za účelem ověřování totožnosti uzly a klienty.</p><p>Některé důležité věci k uvážení při používání certifikátů v service Fabric:</p><ul><li>Certifikáty používané v clusterech spouštění úloh v produkčním prostředí by měl vytvořené pomocí správně nakonfigurovaných certifikát služby Windows Server nebo získané z schválené certifikační autority (CA). Certifikační Autorita může být schválené externí certifikační Autority nebo správně spravované interní infrastruktury veřejných klíčů (PKI)</li><li>Nikdy neměli používat žádné dočasné nebo testovací certifikáty v produkčním prostředí, které jsou vytvořené pomocí nástrojů, jako je MakeCert.exe</li><li>Můžete použít certifikát podepsaný svým držitelem, ale musí tak učinit pouze pro testovací clustery a ne v produkčním prostředí</li></ul>|

## <a id="standard-authn-id"></a>Použít standardní ověřovací scénáře podporované serverem Identity

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Serveru identit | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [IdentityServer3 - celkový přehled](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Kroky** | <p>V následující tabulce jsou typické interakce podporované serverem Identity:</p><ul><li>Prohlížeče komunikovat s webovými aplikacemi</li><li>Webové aplikace komunikovat s webovými rozhraními API (někdy nedělají, někdy jménem uživatele)</li><li>Aplikace využívající prohlížeč komunikaci s webovým rozhraním API</li><li>Nativní aplikace komunikovat s webovými rozhraními API</li><li>Serverové aplikace komunikovat s webovými rozhraními API</li><li>Webová rozhraní API komunikaci s webovým rozhraním API (někdy nedělají, někdy jménem uživatele)</li></ul>|

## <a id="override-token"></a>Přepsat výchozí token mezipaměti serveru identit se škálovatelnou alternativu

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Serveru identit | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Identita nasazení serveru – ukládání do mezipaměti](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Kroky** | <p>IdentityServer má jednoduché integrovanou mezipaměť v paměti. Když je vhodná pro malé škálování nativní aplikace, není vhodné pro aplikace střední vrstvy a back-end z následujících důvodů:</p><ul><li>Tyto aplikace přistupuje mnoho uživatelů současně. Ukládají se všechny přístupové tokeny ve stejném úložišti vytvoří izolace problémů a představuje výzev při fungování ve velkém měřítku: mnoho uživatelů, každý s tolika tokeny jako prostředky má aplikace přístup jménem, může znamenat velký a operace velmi nákladné vyhledávání</li><li>Tyto aplikace jsou obvykle implementovány na distribuované topologie, ve kterém musí mít přístup ke stejné mezipaměti více uzlů</li><li>Tokeny v mezipaměti musí překonat proces recykluje a deaktivací</li><li>Pro všechny výše uvedené důvody při implementaci webových aplikací, se doporučuje přepsat výchozí Server Identity tokenu mezipaměti se škálovatelnou alternativu, jako je Azure Redis cache</li></ul>|

## <a id="binaries-signed"></a>Ujistěte se, že binární soubory nasazené aplikace jsou digitálně podepsané

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti počítače | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Ujistěte se, že binární soubory nasazené aplikace jsou digitálně podepsané, tak, aby se dá ověřit integritu binárních souborů|

## <a id="msmq-queues"></a>Povolení ověřování při připojování ke službě MSMQ fronty ve WCF

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecná rozhraní NET Framework 3 |
| **Atributy**              | neuvedeno |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx) |
| **Kroky** | Program se nepodařilo povolit ověřování při připojení k frontám služby MSMQ, útočník může anonymně odeslat zprávy do fronty ke zpracování. Pokud se ověřování nepoužívá k připojení do fronty služby MSMQ pro doručení zprávy do jiné aplikace, útočník může odeslat anonymní zprávu, která se zlými úmysly.|

### <a name="example"></a>Příklad:
`<netMsmqBinding/>` Element konfigurační soubor WCF níže dá pokyn WCF zakázat ověřování při připojování do fronty pro doručování zpráv služby MSMQ.
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
Konfigurace služby MSMQ tak, aby vyžadovala domény Windows nebo ověřování certifikátu po celou dobu pro všechny příchozí nebo odchozí zprávy.

### <a name="example"></a>Příklad:
`<netMsmqBinding/>` Element konfigurační soubor WCF níže dá pokyn WCF povolit ověřování pomocí certifikátu, při připojení k frontě MSMQ. Klient se ověřují pomocí certifikátů X.509. Klientský certifikát se musí nacházet v úložišti certifikátů serveru.
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

## <a id="message-none"></a>Nebyl typ clientCredentialType zpráv WCF není nastavený na hodnotu none

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Rozhraní .NET framework 3 |
| **Atributy**              | Typ přihlašovacích údajů klienta - žádný |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení](https://vulncat.fortify.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_anonymous_message_client) |
| **Kroky** | Neexistence ověřování znamená, že všem uživatelům přístup k této službě. Služba, která neověřuje svým klientům umožňuje přístup ke všem uživatelům. Konfigurace aplikace k ověřování na základě přihlašovacích údajů klienta. To můžete udělat tak, že nastavíte nebyl typ clientCredentialType zpráv Windows nebo certifikát. |

### <a name="example"></a>Příklad:
```
<message clientCredentialType=""Certificate""/>
```

## <a id="transport-none"></a>Nebyl typ clientCredentialType přenosu WCF není nastavený na hodnotu none

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecná rozhraní .NET Framework 3 |
| **Atributy**              | Typ přihlašovacích údajů klienta - žádný |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení](https://vulncat.hpefod.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_anonymous_transport_client) |
| **Kroky** | Neexistence ověřování znamená, že všem uživatelům přístup k této službě. Služba, která neověřuje svým klientům umožňuje všem uživatelům přístup k jeho funkce. Konfigurace aplikace k ověřování na základě přihlašovacích údajů klienta. To můžete udělat nastavením přenosu nebyl typ clientCredentialType na Windows nebo certifikát. |

### <a name="example"></a>Příklad:
```
<transport clientCredentialType=""Certificate""/>
```

## <a id="authn-secure-api"></a>Ujistěte se, že standardní ověřování techniky se používají k zabezpečení webových rozhraní API

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Ověřování a autorizace v rozhraní ASP.NET Web API](http://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api), [externí ověřovací služby pomocí rozhraní ASP.NET Web API (C#)](http://www.asp.net/web-api/overview/security/external-authentication-services) |
| **Kroky** | <p>Ověřování je proces, ve kterém entita prokáže svoji identitu, obvykle pomocí přihlašovacích údajů, jako je například uživatelské jméno a heslo. Existuje více ověřovací protokoly dostupné kterých lze považovat za. Některé z nich jsou uvedeny níže:</p><ul><li>Klientské certifikáty</li><li>Založené na Windows</li><li>Na základě formulářů</li><li>Federace – služby AD FS</li><li>Federace – Azure AD</li><li>Federace - serveru identit</li></ul><p>Odkazy v části odkazy poskytují podrobnosti nižší úrovně o jak každý z ověřovací schémata je možné implementovat pro zabezpečení webového rozhraní API.</p>|

## <a id="authn-aad"></a>Použít standardní ověřovací scénáře podporované službou Azure Active Directory

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure AD | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Scénáře ověřování pro službu Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/), [Azure Active Directory ukázky kódu](https://azure.microsoft.com/documentation/articles/active-directory-code-samples/), [Příručka pro vývojáře Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-developers-guide/) |
| **Kroky** | <p>Azure Active Directory (Azure AD) zjednodušuje ověřování pro vývojáře tím, že poskytuje identitu jako služba, díky podpoře pro standardní protokoly jako OAuth 2.0 a OpenID Connect. V následující tabulce jsou pěti primární aplikace scénáře podporované službou Azure AD:</p><ul><li>Webový prohlížeč na webovou aplikaci: uživatel potřebuje pro přihlášení k webové aplikaci, která je zabezpečena pomocí služby Azure AD</li><li>Jednostránkové aplikace (SPA): Uživatel potřebuje přihlásit jednostránkové aplikaci, která je zabezpečena pomocí služby Azure AD</li><li>Nativní aplikace pro webové rozhraní API: nativní aplikaci, která běží na telefonu, tabletu nebo počítači potřebuje ověřit uživatele k získání zdroje z webového rozhraní API, která je zabezpečena pomocí služby Azure AD</li><li>Webové aplikace k webovému rozhraní API: webová aplikace je potřeba získat prostředky z webového rozhraní API zabezpečené pomocí Azure AD</li><li>Proces démon nebo serverová aplikace webové rozhraní API: aplikace proces démon nebo serverovou aplikaci s žádné webové uživatelské rozhraní je potřeba získat prostředky z webového rozhraní API zabezpečené pomocí Azure AD</li></ul><p>Najdete na odkazech v oddílu odkazy pro podrobnosti nízké úrovně implementace</p>|

## <a id="adal-scalable"></a>Přepsat výchozí mezipaměť tokenů ADAL s škálovatelnou alternativu

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure AD | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Moderní ověřování pomocí Azure Active Directory pro webové aplikace](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/), [použití Redisu jako mezipaměti tokenů ADAL](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **Kroky** | <p>Výchozí mezipaměti, která používá knihovnu ADAL (Active Directory Authentication Library) je mezipaměti v paměti, která závisí na statické úložiště k dispozici celého procesu. Přestože tento postup funguje pro nativní aplikace, není vhodné pro aplikace střední vrstvy a back-end z následujících důvodů:</p><ul><li>Tyto aplikace přistupuje mnoho uživatelů současně. Ukládají se všechny přístupové tokeny ve stejném úložišti vytvoří izolace problémů a představuje výzev při fungování ve velkém měřítku: mnoho uživatelů, každý s tolika tokeny jako prostředky má aplikace přístup jménem, může znamenat velký a operace velmi nákladné vyhledávání</li><li>Tyto aplikace jsou obvykle implementovány na distribuované topologie, ve kterém musí mít přístup ke stejné mezipaměti více uzlů</li><li>Tokeny v mezipaměti musí překonat proces recykluje a deaktivací</li></ul><p>Pro všechny výše uvedené důvody při implementaci webových aplikací, se doporučuje přepsat výchozí mezipaměť tokenů ADAL s škálovatelnou alternativu, jako je Azure Redis cache.</p>|

## <a id="tokenreplaycache-adal"></a>Ujistěte se, že TokenReplayCache se používá při prevenci opakování tokenů ověřování ADAL

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure AD | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Moderní ověřování pomocí Azure Active Directory pro webové aplikace](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) |
| **Kroky** | <p>Vlastnost TokenReplayCache umožňuje vývojářům definovat mezipaměti opětovného přehrání tokenu, úložiště, které lze použít pro ukládání tokenů pro účely ověření, že žádný token lze použít více než jednou.</p><p>Jedná se o opatření proti běžných způsobů útoku útoku vhodně volané opětovného přehrání tokenu: útočník zachycování token odeslaný při přihlášení se může pokusit znovu odeslat do aplikace ("opakování" ji) pro vytvoření nové relace. Například OIDC v toku udělení kódu, po ověření uživatele úspěšné, požadavek na "/ signin-oidc" koncový bod předávající strany, která se provádí pomocí "id_token", "code" a "stavu" parametry.</p><p>Předávající strana tento požadavek ověří a vytvoří novou relaci. Pokud útočníkem zaznamenává tuto žádost a přehrává ho, učitelského navázat relaci úspěšné a zfalšovat uživatele. Přítomnost nonce v OpenID Connect můžete omezit, ale není zcela vyloučit okolnosti, ve kterých útok úspěšně plnění. K ochraně svých aplikací, můžete vývojáři poskytnout implementaci ITokenReplayCache a přiřadit TokenReplayCache instance.</p>|

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
Tady je ukázková implementace rozhraní ITokenReplayCache. (Zkontrolujte přizpůsobit a implementovat vaše specifické pro projekt Architektura ukládání do mezipaměti)
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
Implementováno mezipaměti musí odkazovat v možnostech OIDC prostřednictvím vlastnosti "Parametry tokenvalidationparameters." následujícím způsobem.
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

Pamatujte, že otestovat efektivitu této konfigurace, přihlaste se k místní aplikaci OIDC chráněné a žádost o zachycení `"/signin-oidc"` koncového bodu ve fiddleru. Pokud ochranu není na místě, přehráním tuto žádost ve fiddleru nastaví nového souboru cookie relace. Když žádost je znovu přehrát po přidání TokenReplayCache ochrany, aplikace vyvolá výjimku následujícím způsobem: `SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a id="adal-oauth2"></a>Spravovat žádosti o tokeny od klientů OAuth2 k AAD pomocí knihovny ADAL (nebo místní AD)

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure AD | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [ADAL](https://azure.microsoft.com/documentation/articles/active-directory-authentication-libraries/) |
| **Kroky** | <p>Azure AD authentication Library (ADAL) umožňuje vývojáři klientských aplikací můžou snadno ověřovat uživatele do cloudu nebo místní služby Active Directory (AD) a získat přístupové tokeny pro volání rozhraní API zabezpečení.</p><p>ADAL obsahuje mnoho funkcí, že ověřování zkontrolujte jednodušší pro vývojáře, jako jsou asynchronní podporu, konfigurovatelné mezipaměť tokenu, který ukládá, tokeny přístupu a obnovovací tokeny, automatické obnovení tokenu, když vyprší platnost přístupového tokenu a tokenu obnovení je k dispozici, a Další.</p><p>Díky zpracování většinu složitost, můžete ADAL pro vývojáře zaměřit na obchodní logiku ve svých aplikacích a snadno svázat prostředky bez experti na zabezpečení. Samostatné knihovny jsou dostupné pro .NET, JavaScript (klient a Node.js), iOS, Android a Java.</p>|

## <a id="authn-devices-field"></a>Ověřování zařízení připojování k bráně pole

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána IoT pole | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Ujistěte se, že každé zařízení proběhne ověření hraniční brána před přijetím jejich dat a před usnadnění nadřazeného komunikace s využitím cloudové bráně. Také se ujistěte, že zařízení připojit se každém zařízení přihlašovacích údajů, takže můžete jednoznačně identifikují jednotlivá zařízení.|

## <a id="authn-devices-cloud"></a>Ujistěte se, že se ověření zařízení připojující se k Cloudová brána

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána IoT cloudu | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné, C#, Node.JS,  |
| **Atributy**              | Není k dispozici, brána výběru – Azure IoT Hub |
| **Odkazy**              | Není k dispozici, [Azure IoT hub s využitím .NET](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/), [Začínáme se službou IoT hub wih a Node JS](https://azure.microsoft.com/documentation/articles/iot-hub-node-node-getstarted), [zabezpečení IoT SAS a certifikáty](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/), [úložiště Git](https://github.com/Azure/azure-iot-sdks/tree/master/node) |
| **Kroky** | <ul><li>**Obecné:** ověřovat zařízení pomocí zabezpečení TLS (Transport Layer) nebo protokol IPSec. Infrastruktura by měla podporovat použití předsdílený klíč (PSK) na těchto zařízeních, které nelze zpracovat úplnou asymetrické šifrování. Využijte služby Azure AD Oauth.</li><li>**C#:** při vytváření DeviceClient instance, ve výchozím nastavení, vytvoření metoda vytvoří instanci DeviceClient službou IoT Hub pro komunikaci pomocí protokolu AMQP. Pokud chcete použít protokol HTTPS, použijte přepis metody metody Create, která umožňuje určit protokol. Pokud používáte protokol HTTPS, měli byste také přidat `Microsoft.AspNet.WebApi.Client` balíček NuGet do projektu mají zahrnout `System.Net.Http.Formatting` oboru názvů.</li></ul>|

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
**Node.JS: ověření**
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
* Získá generováno interně při použití symetrický klíč, ale můžeme můžete vygenerovat a použít ho explicitně i
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
* Připojte se pomocí tokenu sas: 
    ```javascript
    Client.fromSharedAccessSignature(sas, Http); 
    ```
#### <a name="certificates"></a>Certifikáty
* Generovat vlastní X509 podepsaný certifikát, využít libovolný nástroj, jako je například OpenSSL ke generování .cert a .key souborů k ukládání certifikát a klíč v uvedeném pořadí
* Zřízení zařízení, která přijímá zabezpečené připojení pomocí certifikátů.
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
* Připojte zařízení za použití certifikátu
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
| **Použitelných technologiích** | Obecné |
| **Atributy**              | Výběr brány - Azure IoT Hub |
| **Odkazy**              | [Tokeny zabezpečení Azure IoT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) |
| **Kroky** | Použití za použití tokenů SaS přihlašovací údaje ověřování zařízení na základě klíče zařízení nebo klientský certifikát, namísto IoT Hub úrovně sdílené zásady přístupu. To zabraňuje opětovnému použití ověřovacích tokenů jedna brána v zařízení nebo pole jiným |

## <a id="req-containers-anon"></a>Ujistěte se, že jsou uvedeny pouze požadované kontejnerům a objektům BLOB anonymní přístup pro čtení

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | StorageType – objekt Blob |
| **Odkazy**              | [Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/), [sdílené přístupové podpisy, část 1: vysvětlení modelu SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) |
| **Kroky** | <p>Ve výchozím kontejneru a všech objektů BLOB v něm přístupná jenom vlastník účtu úložiště. Pokud chcete dát anonymním uživatelům oprávnění ke čtení pro kontejner a jeho objekty BLOB, jeden můžete nastavit oprávnění kontejner umožní veřejný přístup. Anonymní uživatelé mohou číst objektů BLOB v kontejneru veřejně přístupné bez ověření požadavku.</p><p>Kontejnery nabízejí následující možnosti pro správu přístupu ke kontejneru:</p><ul><li>Úplné veřejné oprávnění ke čtení: kontejneru a objektu blob data lze číst prostřednictvím anonymní žádosti. Klienty můžete zobrazit výčet objektů BLOB v kontejneru prostřednictvím anonymní žádosti, ale nelze vytvořit výčet kontejnery v rámci účtu úložiště.</li><li>Veřejný přístup pro objekty BLOB pouze pro čtení: data objektů Blob v tomto kontejneru lze číst prostřednictvím anonymní žádosti, ale kontejnerů dat není k dispozici. Klienty nelze vytvořit výčet objektů BLOB v kontejneru prostřednictvím anonymní žádosti</li><li>Žádné veřejné přístup pro čtení: data kontejneru a objektu blob je možné načíst pouze vlastník účtu</li></ul><p>Anonymní přístup je nejvhodnější pro scénáře, kde některé objekty BLOB by měl mít vždycky k dispozici pro anonymní přístup pro čtení. Pro citlivější ovládací prvek lze vytvořit sdílený přístupový podpis, který umožňuje delegáta s omezením pomocí specifikátoru přístupu pomocí různých oprávnění a v zadaném časovém intervalu. Zajištění, aby kontejnerům a objektům BLOB, které mohou obsahovat citlivá data, nejsou anonymního přístupu omylem</p>|

## <a id="limited-access-sas"></a>Udělit omezený přístup k objektům ve službě Azure storage pomocí SAS nebo SAP

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno |
| **Odkazy**              | [Sdílené přístupové podpisy, část 1: Vysvětlení modelu SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/), [sdílené přístupové podpisy, část 2: vytvoření a použití SAS s úložištěm objektů Blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/), [jak delegovat přístup k objektům ve vašem účtu pomocí sdíleného Přístupové podpisy a uložené zásady přístupu](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies) |
| **Kroky** | <p>Pomocí sdíleného přístupového podpisu (SAS) je efektivní způsob, jak udělit omezený přístup k objektům v účtu úložiště pro ostatní klienty, aniž byste museli vystavit přístupový klíč účtu. Je identifikátor URI, který zahrnuje jeho parametry dotazu SAS všechny informace potřebné pro ověřený přístup k prostředku úložiště. Pro přístup k prostředkům úložiště pomocí sdíleného přístupového podpisu, musí klient pouze a zajistěte tak předání SAS odpovídajícího konstruktoru nebo metody.</p><p>SAS můžete použít, pokud chcete poskytnout přístup k prostředkům ve vašem účtu úložiště do klienta, který nemůže být důvěryhodný klíčem účtu. Klíče účtu úložiště zahrnují jak primární a sekundární klíč, které udělit přístup pro správu k vašemu účtu a všechny prostředky v ní. Vystavení buď klíče ke svému účtu otevře svůj účet a možnost použití škodlivých aktivit nebo nedbalosti. Sdílené přístupové podpisy poskytnout bezpečné alternativu, která umožňuje dalším klientům čtení, zápisu a odstraňování dat v účtu úložiště podle oprávnění, které jste poskytli a bez nutnosti klíč účtu.</p><p>Pokud už máte logickou sadu parametrů, které jsou podobné pokaždé, když, použití uložené přístup zásad (SAP) je lepší představu. Protože pomocí SAS odvozené ze zásad přístupu uložené poskytuje možnost neprodleně odvolat tento SAS, je doporučených osvědčených postupů a vždy uložené zásady přístupu použít pokud je to možné.</p>|
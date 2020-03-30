---
title: Ověřování – Nástroj pro modelování hrozeb společnosti Microsoft – Azure | Dokumenty společnosti Microsoft
description: zmírnění hrozeb vystavených v nástroji pro modelování hrozeb
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 1bef73e6be4bdbe8828e1d20ea6e684759984627
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72244635"
---
# <a name="security-frame-authentication--mitigations"></a>Rámec zabezpečení: Ověřování | Skutečnosti snižující závažnost rizika 

| Produkt/služba | Článek |
| --------------- | ------- |
| **Webová aplikace**    | <ul><li>[Zvažte použití standardního ověřovacího mechanismu k ověření webové aplikace.](#standard-authn-web-app)</li><li>[Aplikace musí bezpečně zpracovávat neúspěšné scénáře ověřování.](#handle-failed-authn)</li><li>[Povolení stupňovacího nebo adaptivního ověřování](#step-up-adaptive-authn)</li><li>[Ujistěte se, že jsou administrativní rozhraní vhodně uzamčena](#admin-interface-lockdown)</li><li>[Bezpečně implementovat funkce zapomenutých hesel](#forgot-pword-fxn)</li><li>[Zajištění implementace zásad hesel a účtů](#pword-account-policy)</li><li>[Implementovat ovládací prvky, které zabrání výčtu uživatelského jména](#controls-username-enum)</li></ul> |
| **Databáze** | <ul><li>[Pokud je to možné, použijte pro připojení k serveru SQL Server ověřování systému Windows.](#win-authn-sql)</li><li>[Pokud je to možné, použijte azure advitace pro připojení k databázi SQL](#aad-authn-sql)</li><li>[Při použití režimu ověřování SQL se ujistěte, že jsou na serveru SQL vynuceny zásady účtu a hesla.](#authn-account-pword)</li><li>[Nepoužívejte ověřování SQL v obsažených databázích](#autn-contained-db)</li></ul> |
| **Azure Event Hub** | <ul><li>[Použití ověřovacích pověření pro zařízení pomocí tokenů SaS](#authn-sas-tokens)</li></ul> |
| **Hranice důvěryhodnosti Azure** | <ul><li>[Povolení vícefaktorového ověřování Azure pro správce Azure](#multi-factor-azure-admin)</li></ul> |
| **Hranice vztahu důvěryhodnosti služby Fabric** | <ul><li>[Omezení anonymního přístupu ke clusteru Service Fabric](#anon-access-cluster)</li><li>[Ujistěte se, že certifikát Service Fabric mezi klientem a uzly se liší od certifikátu mezi mezi mezi zem.](#fabric-cn-nn)</li><li>[Použití aad k ověření klientů pro služby clusterů prostředků infrastruktury](#aad-client-fabric)</li><li>[Ujistěte se, že certifikáty service fabric jsou získávány od schválené certifikační autority (CA)](#fabric-cert-ca)</li></ul> |
| **Server identit** | <ul><li>[Použití standardních scénářů ověřování podporovaných serverem identity](#standard-authn-id)</li><li>[Přepsat výchozí mezipaměť tokenů serveru identity pomocí škálovatelné alternativy](#override-token)</li></ul> |
| **Hranice důvěryhodnosti počítače** | <ul><li>[Ujistěte se, že jsou binární soubory nasazených aplikací digitálně podepsané](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Povolení ověřování při připojování k frontám služby MSMQ v wcf](#msmq-queues)</li><li>[WCF-Nenastavovat typ klienta messageCredentialType na žádný](#message-none)</li><li>[WCF-Nenastavovat typ klienta přenosu typu credentialtype na žádný](#transport-none)</li></ul> |
| **Web API** | <ul><li>[Ujistěte se, že se k zabezpečení webových api používají standardní techniky ověřování.](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[Použití standardních scénářů ověřování podporovaných službou Azure Active Directory](#authn-aad)</li><li>[Přepsat výchozí mezipaměť tokenů ADAL škálovatelnou alternativou](#adal-scalable)</li><li>[Ujistěte se, že tokenReplayCache se používá k zabránění přehrání ověřovacích tokenů ADAL](#tokenreplaycache-adal)</li><li>[Použití knihoven ADAL ke správě požadavků na tokeny od klientů OAuth2 do služby AAD (nebo místní služby AD)](#adal-oauth2)</li></ul> |
| **Brána ioT pole** | <ul><li>[Ověření zařízení připojujících se k bráně pole](#authn-devices-field)</li></ul> |
| **Cloudová brána IoT** | <ul><li>[Zajištění ověření zařízení připojujících se ke cloudové bráně](#authn-devices-cloud)</li><li>[Použití přihlašovacích údajů pro jedno zařízení](#authn-cred)</li></ul> |
| **Azure Storage** | <ul><li>[Ujistěte se, že anonymní přístup pro čtení mají pouze požadované kontejnery a objekty BLOB.](#req-containers-anon)</li><li>[Udělit omezený přístup k objektům v úložišti Azure pomocí SAS nebo SAP](#limited-access-sas)</li></ul> |

## <a name="consider-using-a-standard-authentication-mechanism-to-authenticate-to-web-application"></a><a id="standard-authn-web-app"></a>Zvažte použití standardního ověřovacího mechanismu k ověření webové aplikace.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| Podrobnosti | <p>Ověřování je proces, při kterém entita prokáže svou identitu, obvykle prostřednictvím pověření, jako je například uživatelské jméno a heslo. K dispozici je více ověřovacích protokolů, které mohou být považovány za. Některé z nich jsou uvedeny níže:</p><ul><li>Klientské certifikáty</li><li>Na základě systému Windows</li><li>Na základě formulářů</li><li>Federace - ADFS</li><li>Federace – azure ad</li><li>Federace – server identit</li></ul><p>Zvažte použití standardního ověřovacího mechanismu k identifikaci procesu zdroje</p>|

## <a name="applications-must-handle-failed-authentication-scenarios-securely"></a><a id="handle-failed-authn"></a>Aplikace musí bezpečně zpracovávat neúspěšné scénáře ověřování.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| Podrobnosti | <p>Aplikace, které explicitně ověřují uživatele, musí bezpečně zpracovávat neúspěšné scénáře ověřování. Mechanismus ověřování musí:</p><ul><li>Odepřít přístup privilegovaným prostředkům při selhání ověřování</li><li>Zobrazení obecné chybové zprávy po neúspěšném ověření a odepření přístupu</li></ul><p>Zkouška pro:</p><ul><li>Ochrana privilegovaných prostředků po neúspěšných přihlášeních</li><li>Při neúspěšných událostech ověřování a odepření přístupu se zobrazí obecná chybová zpráva.</li><li>Účty jsou zakázány po nadměrném počtu neúspěšných pokusů</li><ul>|

## <a name="enable-step-up-or-adaptive-authentication"></a><a id="step-up-adaptive-authn"></a>Povolení stupňovacího nebo adaptivního ověřování

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| Podrobnosti | <p>Ověřte, zda má aplikace další autorizaci (například zvýšení nebo adaptivní ověřování, prostřednictvím vícefaktorového ověřování, jako je odesílání OTP v SMS, e-mailu atd. nebo výzva k opětovnému ověření), aby byl uživatel před udělením přístupu k aplikaci citlivých informací. Toto pravidlo platí také pro provádění kritických změn účtu nebo akce</p><p>To také znamená, že úprava autentizace musí být provedena takovým způsobem, aby aplikace správně vynucovala kontextově citlivé oprávnění tak, aby neumožňovala neautorizovanou manipulaci například pomocí manipulace s parametry</p>|

## <a name="ensure-that-administrative-interfaces-are-appropriately-locked-down"></a><a id="admin-interface-lockdown"></a>Ujistěte se, že jsou administrativní rozhraní vhodně uzamčena

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| Podrobnosti | Prvním řešením je udělit přístup pouze z určitého rozsahu ip zdrojové adresy do rozhraní pro správu. Pokud by toto řešení nebylo možné, než se vždy doporučuje vynutit postupné nebo adaptivní ověřování pro přihlášení do rozhraní pro správu |

## <a name="implement-forgot-password-functionalities-securely"></a><a id="forgot-pword-fxn"></a>Bezpečně implementovat funkce zapomenutých hesel

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| Podrobnosti | <p>První věc je ověřit, že zapomenuté heslo a další cesty pro obnovení odeslat odkaz včetně časově omezené aktivační token spíše než samotné heslo. Před odesláním odkazu lze také vyžadovat další autentizaci založenou na softwarových tokenech (např. SMS token, nativní mobilní aplikace atd.). Za druhé, neměli byste zamknout uživatelský účet, zatímco proces získávání nového hesla probíhá.</p><p>To by mohlo vést k útoku odmítnutí služby vždy, když se útočník rozhodne úmyslně uzamknout uživatele pomocí automatického útoku. Za třetí, vždy, když byl nastaven nový požadavek na heslo v průběhu, zpráva, kterou zobrazíte by měla být zobecněna, aby se zabránilo výčtu uživatelského jména. Za čtvrté, vždy zakázat používání starých hesel a implementovat silné zásady hesel.</p> |

## <a name="ensure-that-password-and-account-policy-are-implemented"></a><a id="pword-account-policy"></a>Zajištění implementace zásad hesel a účtů

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| Podrobnosti | <p>Zásady hesel a účtů v souladu s organizačními zásadami a osvědčenými postupy by měly být implementovány.</p><p>Chcete-li se bránit proti hádání hrubou silou a slovníkem: Musí být implementovány zásady silného hesla, aby bylo zajištěno, že uživatelé vytvoří složité heslo (např. minimální délka 12 znaků, alfanumerické a speciální znaky).</p><p>Zásady uzamčení účtu mohou být implementovány následujícím způsobem:</p><ul><li>**Měkké uzamčení:** To může být dobrou volbou pro ochranu uživatelů před útoky hrubou silou. Například vždy, když uživatel zadá nesprávné heslo třikrát aplikace může uzamknout účet na minutu, aby se zpomalil proces hrubosti vynucení jejich heslo dělat to méně výhodné pro útočníka pokračovat. Pokud byste měli implementovat tvrdé uzamčení protiopatření pro tento příklad byste dosáhnout "DoS" trvale uzamčení účtů. Alternativně může aplikace vygenerovat OTP (One Time Password) a odeslat jej mimo pásmo (prostřednictvím e-mailu, SMS atd.) uživateli. Dalším přístupem může být implementace CAPTCHA po dosažení prahového počtu neúspěšných pokusů.</li><li>**Tvrdé uzamčení:** Tento typ uzamčení by měla být použita vždy, když zjistíte, že uživatel útočí na vaši aplikaci a čelit jim pomocí trvale uzamčení jejich účet, dokud tým odpovědi měl čas udělat jejich forenzní. Po tomto procesu se můžete rozhodnout vrátit uživateli jeho účet nebo podniknout další právní kroky proti němu. Tento typ přístupu zabraňuje útočníkovi v dalším proniknutí do aplikace a infrastruktury.</li></ul><p>Chcete-li se bránit proti útokům na výchozí a předvídatelné účty, ověřte, zda jsou všechny klíče a hesla nahraditelné a zda jsou generovány nebo nahrazeny po instalaci.</p><p>Pokud aplikace má automaticky generovat hesla, ujistěte se, že vygenerovaná hesla jsou náhodné a mají vysokou entropie.</p>|

## <a name="implement-controls-to-prevent-username-enumeration"></a><a id="controls-username-enum"></a>Implementovat ovládací prvky, které zabrání výčtu uživatelského jména

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Všechny chybové zprávy by měly být zobecněny, aby se zabránilo výčtu uživatelského jména. Někdy se také nemůžete vyhnout úniku informací ve funkcích, jako je registrační stránka. Zde musíte použít metody omezení rychlosti, jako je CAPTCHA, abyste zabránili automatizovanému útoku útočníka. |

## <a name="when-possible-use-windows-authentication-for-connecting-to-sql-server"></a><a id="win-authn-sql"></a>Pokud je to možné, použijte pro připojení k serveru SQL Server ověřování systému Windows.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | OnPrem |
| **Atributy**              | VERZE SQL - vše |
| **Odkazy**              | [SQL Server – volba režimu ověřování](https://msdn.microsoft.com/library/ms144284.aspx) |
| **Kroky** | Ověřování systému Windows používá protokol zabezpečení Kerberos, poskytuje vynucení zásad hesel s ohledem na ověření složitosti silných hesel, poskytuje podporu pro uzamčení účtu a podporuje vypršení platnosti hesla.|

## <a name="when-possible-use-azure-active-directory-authentication-for-connecting-to-sql-database"></a><a id="aad-authn-sql"></a>Pokud je to možné, použijte azure advitace pro připojení k databázi SQL

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | SQL Azure |
| **Atributy**              | Verze SQL - V12 |
| **Odkazy**              | [Připojení k databázi SQL pomocí ověřování služby Azure Active Directory](https://azure.microsoft.com/documentation/articles/sql-database-aad-authentication/) |
| **Kroky** | **Minimální verze:** Azure SQL Database V12 vyžaduje povolit Azure SQL Database používat ověřování AAD proti Adresáři Microsoft |

## <a name="when-sql-authentication-mode-is-used-ensure-that-account-and-password-policy-are-enforced-on-sql-server"></a><a id="authn-account-pword"></a>Při použití režimu ověřování SQL se ujistěte, že jsou na serveru SQL vynuceny zásady účtu a hesla.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Zásady hesel serveru SQL Server](https://technet.microsoft.com/library/ms161959(v=sql.110).aspx) |
| **Kroky** | Při použití ověřování serveru SQL Server jsou přihlášení vytvořena na serveru SQL Server, která nejsou založena na uživatelských účtech systému Windows. Uživatelské jméno i heslo jsou vytvořeny pomocí serveru SQL Server a uloženy v serveru SQL Server. SQL Server může používat mechanismy zásad hesel systému Windows. Může použít stejné zásady složitosti a vypršení platnosti používané v systému Windows pro hesla používaná uvnitř serveru SQL Server. |

## <a name="do-not-use-sql-authentication-in-contained-databases"></a><a id="autn-contained-db"></a>Nepoužívejte ověřování SQL v obsažených databázích

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | OnPrem, SQL Azure |
| **Atributy**              | VERZE SQL - MSSQL2012, VERZE SQL - V12 |
| **Odkazy**              | [Doporučené postupy zabezpečení s obsaženými databázemi](https://msdn.microsoft.com/library/ff929055.aspx) |
| **Kroky** | Absence vynucených zásad hesel může zvýšit pravděpodobnost vytvoření slabého pověření v uzavřené databázi. Využijte ověřování systému Windows. |

## <a name="use-per-device-authentication-credentials-using-sas-tokens"></a><a id="authn-sas-tokens"></a>Použití ověřovacích pověření pro zařízení pomocí tokenů SaS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Event Hub | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Přehled modelu ověřování a zabezpečení centra událostí](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroky** | <p>Model zabezpečení Centra událostí je založen na kombinaci tokenů sdílených přístupových podpisů (SAS) a vydavatelů událostí. Název vydavatele představuje DeviceID, který přijímá token. To by pomohlo přidružit tokeny generované s příslušnými zařízeními.</p><p>Všechny zprávy jsou označeny původcem na straně služby, což umožňuje detekci pokusů o falšování falšování stavu v datové části. Při ověřování zařízení vygenerujte token SaS pro zařízení s rozsahem pro jedinečného vydavatele.</p>|

## <a name="enable-azure-multi-factor-authentication-for-azure-administrators"></a><a id="multi-factor-azure-admin"></a>Povolení vícefaktorového ověřování Azure pro správce Azure

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice důvěryhodnosti Azure | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Co je Azure Multi-Factor Authentication?](https://azure.microsoft.com/documentation/articles/multi-factor-authentication/) |
| **Kroky** | <p>Vícefaktorové ověřování (MFA) je metoda ověřování, která vyžaduje více než jednu metodu ověření a přidává kritickou druhou vrstvu zabezpečení pro přihlášení uživatelů a transakce. Funguje tak, že vyžaduje jakékoli dvě nebo více z následujících metod ověřování:</p><ul><li>Něco, co znáte (obvykle heslo)</li><li>Něco, co máte (důvěryhodné zařízení, které není snadno duplikovat, jako je telefon)</li><li>Něco, co jste (biometrie)</li><ul>|

## <a name="restrict-anonymous-access-to-service-fabric-cluster"></a><a id="anon-access-cluster"></a>Omezení anonymního přístupu ke clusteru Service Fabric

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahu důvěryhodnosti služby Fabric | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Prostředí – Azure  |
| **Odkazy**              | [Scénáře zabezpečení clusteru Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security) |
| **Kroky** | <p>Clustery by měly být vždy zabezpečené, aby se zabránilo neoprávněným uživatelům v připojení ke clusteru, zejména v případě, že má produkční úlohy spuštěné na něm.</p><p>Při vytváření clusteru prostředků infrastruktury služeb se ujistěte, že je režim zabezpečení nastaven na "zabezpečený", a nakonfigurujte požadovaný certifikát serveru X.509. Vytvoření "nezabezpečeného" clusteru umožní anonymnímu uživateli připojit se k němu, pokud zpřístupní koncové body správy veřejnému Internetu.</p>|

## <a name="ensure-that-service-fabric-client-to-node-certificate-is-different-from-node-to-node-certificate"></a><a id="fabric-cn-nn"></a>Ujistěte se, že certifikát Service Fabric mezi klientem a uzly se liší od certifikátu mezi mezi mezi zem.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahu důvěryhodnosti služby Fabric | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Prostředí – Azure, prostředí – samostatné |
| **Odkazy**              | [Service Fabric Zabezpečení certifikátu klient-uzel](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#_client-to-node-certificate-security), [Připojení k zabezpečenému clusteru pomocí klientského certifikátu](https://azure.microsoft.com/documentation/articles/service-fabric-connect-to-secure-cluster/) |
| **Kroky** | <p>Zabezpečení certifikátu klient-node je nakonfigurované při vytváření clusteru buď prostřednictvím portálu Azure, šablon Správce prostředků nebo samostatné šablony JSON zadáním klientského certifikátu správce nebo klientského certifikátu uživatele.</p><p>Zadané certifikáty klienta správce a uživatele, které zadáte, by se měly lišit od primárních a sekundárních certifikátů, které zadáte pro zabezpečení uzlu k uzlu.</p>|

## <a name="use-aad-to-authenticate-clients-to-service-fabric-clusters"></a><a id="aad-client-fabric"></a>Použití aad k ověření klientů pro služby clusterů prostředků infrastruktury

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahu důvěryhodnosti služby Fabric | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Prostředí – Azure |
| **Odkazy**              | [Scénáře zabezpečení clusteru – doporučení zabezpečení](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#security-recommendations) |
| **Kroky** | Clustery spuštěné v Azure můžou taky zabezpečit přístup ke koncovým bodům správy pomocí Azure Active Directory (AAD), kromě klientských certifikátů. Pro clustery Azure se doporučuje použít zabezpečení AAD k ověřování klientů a certifikátů pro zabezpečení mezi uzlinami.|

## <a name="ensure-that-service-fabric-certificates-are-obtained-from-an-approved-certificate-authority-ca"></a><a id="fabric-cert-ca"></a>Ujistěte se, že certifikáty service fabric jsou získávány od schválené certifikační autority (CA)

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahu důvěryhodnosti služby Fabric | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Prostředí – Azure |
| **Odkazy**              | [Certifikáty X.509 a servisní tkanina](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#x509-certificates-and-service-fabric) |
| **Kroky** | <p>Service Fabric používá serverové certifikáty X.509 pro ověřování uzlů a klientů.</p><p>Některé důležité věci, které je třeba zvážit při používání certifikátů v servisních tkaninách:</p><ul><li>Certifikáty používané v clusterech s produkčními úlohami by měly být vytvořeny pomocí správně nakonfigurované certifikační služby systému Windows Server nebo získané od schválené certifikační autority (CA). Certifikační autorita může být schválená externí certifikační autorita nebo správně spravovaná interní infrastruktura veřejných klíčů (PKI)</li><li>Nikdy nepoužívejte žádné dočasné nebo zkušební certifikáty v produkčním prostředí, které jsou vytvořeny pomocí nástrojů, jako je MakeCert.exe</li><li>Můžete použít certifikát podepsaný svým držitelem, ale měli byste tak učinit pouze pro testovací clustery a nikoli v produkčním prostředí.</li></ul>|

## <a name="use-standard-authentication-scenarios-supported-by-identity-server"></a><a id="standard-authn-id"></a>Použití standardních scénářů ověřování podporovaných serverem identity

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Server identit | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [IdentityServer3 - Velký obraz](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Kroky** | <p>Níže jsou uvedeny typické interakce podporované identitním serverem:</p><ul><li>Prohlížeče komunikují s webovými aplikacemi</li><li>Webové aplikace komunikují s webovými rozhraními API (někdy samostatně, někdy jménem uživatele)</li><li>Aplikace založené na prohlížeči komunikují s webovými api</li><li>Nativní aplikace komunikují s webovými api</li><li>Serverové aplikace komunikují s webovými api</li><li>Webová rozhraní API komunikují s webovými rozhraními API (někdy samostatně, někdy jménem uživatele)</li></ul>|

## <a name="override-the-default-identity-server-token-cache-with-a-scalable-alternative"></a><a id="override-token"></a>Přepsat výchozí mezipaměť tokenů serveru identity pomocí škálovatelné alternativy

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Server identit | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Nasazení serveru identity – ukládání do mezipaměti](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Kroky** | <p>IdentityServer má jednoduchou vestavěnou mezipaměť v paměti. I když je to dobré pro malé nativní aplikace, neškáluje pro aplikace střední vrstvy a back-endz následujících důvodů:</p><ul><li>K těmto aplikacím přistupuje mnoho uživatelů najednou. Uložení všech přístupových tokenů ve stejném úložišti vytváří problémy s izolací a představuje problémy při provozu ve velkém měřítku: mnoho uživatelů, z nichž každý má tolik tokenů jako prostředky, které aplikace přistupuje jejich jménem, může znamenat obrovské množství a velmi nákladné operace vyhledávání</li><li>Tyto aplikace se obvykle nasazují na distribuované topologie, kde více uzlů musí mít přístup ke stejné mezipaměti</li><li>Tokeny uložené v mezipaměti musí přežít recyklace a deaktivace procesu.</li><li>Ze všech výše uvedených důvodů se při implementaci webových aplikací doporučuje přepsat výchozí mezipaměť tokenů serveru Identity Server škálovatelnou alternativou, jako je například Azure Cache for Redis.</li></ul>|

## <a name="ensure-that-deployed-applications-binaries-are-digitally-signed"></a><a id="binaries-signed"></a>Ujistěte se, že jsou binární soubory nasazených aplikací digitálně podepsané

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice důvěryhodnosti počítače | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Ujistěte se, že binární soubory nasazených aplikací jsou digitálně podepsány, aby bylo možné ověřit integritu binárních souborů.|

## <a name="enable-authentication-when-connecting-to-msmq-queues-in-wcf"></a><a id="msmq-queues"></a>Povolení ověřování při připojování k frontám služby MSMQ v wcf

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecný, NET Framework 3 |
| **Atributy**              | Není dostupné. |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx) |
| **Kroky** | Program nepovolí ověřování při připojování k frontám služby MSMQ, útočník může anonymně odesílat zprávy do fronty ke zpracování. Pokud se ověřování nepoužívá k připojení k frontě služby MSMQ používané k doručení zprávy jinému programu, mohl by útočník odeslat anonymní zprávu, která je škodlivá.|

### <a name="example"></a>Příklad
Prvek `<netMsmqBinding/>` konfiguračního souboru WCF níže pokyn WCF zakázat ověřování při připojování k frontě služby MSMQ pro doručování zpráv.
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
Nakonfigurujte službu MSMQ tak, aby pro příchozí nebo odchozí zprávy vždy vyžadovala ověření domény nebo certifikátu systému Windows.

### <a name="example"></a>Příklad
Prvek `<netMsmqBinding/>` konfiguračního souboru WCF níže dává wcf pokyn povolit ověřování certifikátů při připojování k frontě služby MSMQ. Klient je ověřen pomocí certifikátů X.509. Klientský certifikát musí být k dispozici v úložišti certifikátů serveru.
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

## <a name="wcf-do-not-set-message-clientcredentialtype-to-none"></a><a id="message-none"></a>WCF-Nenastavovat typ klienta messageCredentialType na žádný

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Rozhraní .NET Framework 3 |
| **Atributy**              | Typ pověření klienta – žádný |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Opevnit](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Kroky** | Absence ověřování znamená, že k této službě má přístup každý. Služba, která neověřuje své klienty, umožňuje přístup všem uživatelům. Nakonfigurujte aplikaci tak, aby se ověřovala podle pověření klienta. To lze provést nastavením clientCredentialType zprávy na systém Windows nebo certifikát. |

### <a name="example"></a>Příklad
```
<message clientCredentialType=""Certificate""/>
```

## <a name="wcf-do-not-set-transport-clientcredentialtype-to-none"></a><a id="transport-none"></a>WCF-Nenastavovat typ klienta přenosu typu credentialtype na žádný

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecný, rozhraní .NET Framework 3 |
| **Atributy**              | Typ pověření klienta – žádný |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Opevnit](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Kroky** | Absence ověřování znamená, že k této službě má přístup každý. Služba, která neověřuje své klienty, umožňuje všem uživatelům přístup k jejím funkcím. Nakonfigurujte aplikaci tak, aby se ověřovala podle pověření klienta. To lze provést nastavením přenosu clientCredentialType na systém Windows nebo certifikát. |

### <a name="example"></a>Příklad
```
<transport clientCredentialType=""Certificate""/>
```

## <a name="ensure-that-standard-authentication-techniques-are-used-to-secure-web-apis"></a><a id="authn-secure-api"></a>Ujistěte se, že se k zabezpečení webových api používají standardní techniky ověřování.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Ověřování a autorizace v rozhraní ASP.NET web API](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api), externí ověřovací [služby s ASP.NET webovým rozhraním API (C#)](https://www.asp.net/web-api/overview/security/external-authentication-services) |
| **Kroky** | <p>Ověřování je proces, při kterém entita prokáže svou identitu, obvykle prostřednictvím pověření, jako je například uživatelské jméno a heslo. K dispozici je více ověřovacích protokolů, které mohou být považovány za. Některé z nich jsou uvedeny níže:</p><ul><li>Klientské certifikáty</li><li>Na základě systému Windows</li><li>Na základě formulářů</li><li>Federace - ADFS</li><li>Federace – azure ad</li><li>Federace – server identit</li></ul><p>Odkazy v části odkazy poskytují podrobnosti nižší úrovně o tom, jak lze implementovat každé schéma ověřování k zabezpečení webového rozhraní API.</p>|

## <a name="use-standard-authentication-scenarios-supported-by-azure-active-directory"></a><a id="authn-aad"></a>Použití standardních scénářů ověřování podporovaných službou Azure Active Directory

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure AD | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Scénáře ověřování pro Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/), [ukázky kódu Služby Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-code-samples/), [Průvodce vývojářem služby Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-developers-guide/) |
| **Kroky** | <p>Azure Active Directory (Azure AD) zjednodušuje ověřování pro vývojáře tím, že poskytuje identitu jako službu, s podporou standardních protokolů, jako je OAuth 2.0 a OpenID Connect. Níže je pět scénářů primární aplikace podporovaných službou Azure AD:</p><ul><li>Webový prohlížeč na webovou aplikaci: Uživatel se musí přihlásit k webové aplikaci, která je zabezpečená službou Azure AD</li><li>Jednostránková aplikace (SPA): Uživatel se musí přihlásit k jednostránkové aplikaci, která je zabezpečená službou Azure AD</li><li>Nativní aplikace pro webové rozhraní API: Nativní aplikace, která běží na telefonu, tabletu nebo počítači, potřebuje ověřit uživatele, aby získala prostředky z webového rozhraní API zabezpečeného službou Azure AD</li><li>Webová aplikace do webového rozhraní API: Webová aplikace potřebuje získat prostředky z webového rozhraní API zabezpečeného službou Azure AD</li><li>Daemon nebo Server Aplikace na web API: Daemon aplikace nebo serverové aplikace bez webového uživatelského rozhraní potřebuje získat prostředky z webového rozhraní API zabezpečené službou Azure AD</li></ul><p>Podrobnosti o provádění na nízké úrovni naleznete v odkazech v části reference.</p>|

## <a name="override-the-default-adal-token-cache-with-a-scalable-alternative"></a><a id="adal-scalable"></a>Přepsat výchozí mezipaměť tokenů ADAL škálovatelnou alternativou

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure AD | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Moderní ověřování pomocí služby Azure Active Directory pro webové aplikace](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/), použití [redisu jako mezipaměti tokenů ADAL](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **Kroky** | <p>Výchozí mezipaměť, kterou adal (Knihovna ověřování služby Active Directory) používá, je mezipaměť v paměti, která závisí na statickém úložišti, které je k dispozici pro celý proces. I když to funguje pro nativní aplikace, neškáluje pro střední vrstvy a back-endové aplikace z následujících důvodů:</p><ul><li>K těmto aplikacím přistupuje mnoho uživatelů najednou. Uložení všech přístupových tokenů ve stejném úložišti vytváří problémy s izolací a představuje problémy při provozu ve velkém měřítku: mnoho uživatelů, z nichž každý má tolik tokenů jako prostředky, které aplikace přistupuje jejich jménem, může znamenat obrovské množství a velmi nákladné operace vyhledávání</li><li>Tyto aplikace se obvykle nasazují na distribuované topologie, kde více uzlů musí mít přístup ke stejné mezipaměti</li><li>Tokeny uložené v mezipaměti musí přežít recyklace a deaktivace procesu.</li></ul><p>Ze všech výše uvedených důvodů se při implementaci webových aplikací doporučuje přepsat výchozí mezipaměť tokenů ADAL škálovatelnou alternativou, jako je například Azure Cache for Redis.</p>|

## <a name="ensure-that-tokenreplaycache-is-used-to-prevent-the-replay-of-adal-authentication-tokens"></a><a id="tokenreplaycache-adal"></a>Ujistěte se, že tokenReplayCache se používá k zabránění přehrání ověřovacích tokenů ADAL

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure AD | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Moderní ověřování pomocí služby Azure Active Directory pro webové aplikace](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) |
| **Kroky** | <p>Vlastnost TokenReplayCache umožňuje vývojářům definovat mezipaměť pro opětovné přehrání tokenu, úložiště, které lze použít pro ukládání tokenů za účelem ověření, že žádný token nelze použít více než jednou.</p><p>Jedná se o opatření proti běžnému útoku, vhodně nazývanému útoku na přehrání tokenu: útočník, který zachycuje token odeslaný při přihlášení, se může pokusit odeslat jej do aplikace znovu ("přehrát") pro vytvoření nové relace. Například v OIDC kód-grant toku, po úspěšném ověření uživatele, požadavek na "/signin-oidc" koncový bod předávající strany se provádí s parametry "id_token", "kód" a "stav".</p><p>Předávající strana tento požadavek ověří a vytvoří novou relaci. Pokud protivník zachytí tento požadavek a přehraje jej, může vytvořit úspěšnou relaci a zfalšovat uživatele. Přítomnost nonce v OpenID Connect může omezit, ale ne zcela eliminovat okolnosti, za kterých může být útok úspěšně přijat. Chcete-li chránit své aplikace, vývojáři mohou poskytnout implementaci ITokenReplayCache a přiřadit instanci TokenReplayCache.</p>|

### <a name="example"></a>Příklad
```csharp
// ITokenReplayCache defined in ADAL
public interface ITokenReplayCache
{
bool TryAdd(string securityToken, DateTime expiresOn);
bool TryFind(string securityToken);
}
```

### <a name="example"></a>Příklad
Zde je ukázková implementace rozhraní ITokenReplayCache. (Přizpůsobte a implementujte rámec ukládání do mezipaměti pro konkrétní projekt)
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
Implementovaná mezipaměť musí být odkazována v možnostech OIDC prostřednictvím vlastnosti "TokenValidationParameters" následujícím způsobem.
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

Vezměte prosím na vědomí, že chcete-li otestovat účinnost této konfigurace, přihlaste se do místní aplikace chráněné OIDC a zachytit požadavek na `"/signin-oidc"` koncový bod v houslista. Pokud ochrana není na místě, přehrání tohoto požadavku v houslista nastaví novou relaci cookie. Při přehrání požadavku po přidání ochrany TokenReplayCache aplikace vyvolá výjimku následujícím způsobem:`SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a name="use-adal-libraries-to-manage-token-requests-from-oauth2-clients-to-aad-or-on-premises-ad"></a><a id="adal-oauth2"></a>Použití knihoven ADAL ke správě požadavků na tokeny od klientů OAuth2 do služby AAD (nebo místní služby AD)

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure AD | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [ADAL](https://azure.microsoft.com/documentation/articles/active-directory-authentication-libraries/) |
| **Kroky** | <p>Knihovna ověřování Azure AD (ADAL) umožňuje vývojářům klientských aplikací snadno ověřovat uživatele do cloudu nebo místní služby Active Directory (AD) a pak získat přístupové tokeny pro zabezpečení volání rozhraní API.</p><p>ADAL má mnoho funkcí, které usnadňují ověřování pro vývojáře, jako je například asynchronní podpora, konfigurovatelná mezipaměť tokenů, která ukládá přístupové tokeny a obnovovací tokeny, automatická aktualizace tokenů při vypršení platnosti přístupového tokenu a je k dispozici obnovovací token a Více.</p><p>Zpracováním většiny složitosti může ADAL pomoci vývojáři zaměřit se na obchodní logiku v jejich aplikaci a snadno zabezpečit prostředky, aniž by byl odborníkem na zabezpečení. Samostatné knihovny jsou k dispozici pro .NET, JavaScript (klient a Node.js), Python, iOS, Android a Java.</p>|

## <a name="authenticate-devices-connecting-to-the-field-gateway"></a><a id="authn-devices-field"></a>Ověření zařízení připojujících se k bráně pole

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána ioT pole | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Ujistěte se, že každé zařízení je ověřeno bránou pole před přijetím dat z nich a před usnadněním upstream komunikace s cloudovou bránou. Také se ujistěte, že zařízení připojit s pověření pro jednotlivá zařízení tak, aby jednotlivá zařízení lze jednoznačně identifikovat.|

## <a name="ensure-that-devices-connecting-to-cloud-gateway-are-authenticated"></a><a id="authn-devices-cloud"></a>Zajištění ověření zařízení připojujících se ke cloudové bráně

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Cloudová brána IoT | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecný, C#, Node.JS,  |
| **Atributy**              | Není k tomu a d, brána – Azure IoT Hub |
| **Odkazy**              | Není k mna, [Azure IoT hub s rozhraním .NET](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/), [Začínáme s centrem IoT a JS uzlu](https://azure.microsoft.com/documentation/articles/iot-hub-node-node-getstarted), [Zabezpečení IoT s SAS a certifikáty](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/), [úložiště Git](https://github.com/Azure/azure-iot-sdks/tree/master/node) |
| **Kroky** | <ul><li>**Obecné:** Ověřte zařízení pomocí zabezpečení transportní vrstvy (TLS) nebo protokolu IPSec. Infrastruktura by měla podporovat použití předsdíleného klíče (PSK) na zařízeních, která nezvládnou úplnou asymetrickou kryptografii. Využijte Azure AD, Oauth.</li><li>**C#:** Při vytváření instance DeviceClient ve výchozím nastavení vytvoří metoda Create instanci DeviceClient, která ke komunikaci s službou IoT Hub používá protokol AMQP. Pokud chcete používat protokol HTTPS, použijte přepis metody Create, který umožňuje určit protokol. Pokud používáte protokol HTTPS, měli `Microsoft.AspNet.WebApi.Client` byste také přidat balíček `System.Net.Http.Formatting` NuGet do projektu zahrnout obor názvů.</li></ul>|

### <a name="example"></a>Příklad
```csharp
static DeviceClient deviceClient;

static string deviceKey = "{device key}";
static string iotHubUri = "{iot hub hostname}";

var messageString = "{message in string format}";
var message = new Message(Encoding.ASCII.GetBytes(messageString));

deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

await deviceClient.SendEventAsync(message);
```

### <a name="example"></a>Příklad
**Node.JS: Ověřování**
#### <a name="symmetric-key"></a>Symetrický klíč
* Vytvoření centra IoT hub v azure
* Vytvoření položky v registru identit zařízení
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
  #### <a name="sas-token"></a>SAS Token
* Získá interně generované při použití symetrického klíče, ale můžeme generovat a používat explicitně stejně
* Definovat protokol :`var Http = require('azure-iot-device-http').Http;`
* Vytvořit token sas :
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
* Generovat vlastní podepsaný certifikát X509 pomocí libovolného nástroje, jako je OpenSSL, pro generování souborů CERT a .key pro uložení certifikátu a klíče
* Zřízení zařízení, které přijímá zabezpečené připojení pomocí certifikátů.
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

## <a name="use-per-device-authentication-credentials"></a><a id="authn-cred"></a>Použití přihlašovacích údajů pro jedno zařízení

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Cloudová brána IoT  | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Volba brány – Azure IoT Hub |
| **Odkazy**              | [Tokeny zabezpečení centra Azure IoT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) |
| **Kroky** | Místo zásad sdíleného přístupu na úrovni služby IoT Hub používejte ověřovací pověření pro zařízení pomocí tokenů SaS založených na klíči zařízení nebo klientském certifikátu. Tím se zabrání opakovanému použití ověřovacích tokenů jednoho zařízení nebo brány pole jiným |

## <a name="ensure-that-only-the-required-containers-and-blobs-are-given-anonymous-read-access"></a><a id="req-containers-anon"></a>Ujistěte se, že anonymní přístup pro čtení mají pouze požadované kontejnery a objekty BLOB.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | StorageType – objekt blob |
| **Odkazy**              | [Správa anonymního přístupu pro čtení ke kontejnerům a objektům BLOB](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/), [sdíleným přístupovým podpisům, část 1: Principy modelu SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) |
| **Kroky** | <p>Ve výchozím nastavení kontejner a všechny objekty BLOB v něm může přistupovat pouze vlastník účtu úložiště. Chcete-li anonymním uživatelům udělit oprávnění ke čtení kontejneru a jeho objektů BLOB, můžete nastavit oprávnění kontejneru tak, aby umožňovala veřejný přístup. Anonymní uživatelé mohou číst objekty BLOB v rámci veřejně přístupného kontejneru bez ověření požadavku.</p><p>Kontejnery poskytují následující možnosti pro správu přístupu kontejneru:</p><ul><li>Úplný veřejný přístup pro čtení: Data kontejneru a objektu blob lze číst prostřednictvím anonymního požadavku. Klienti můžete vytvořit výčet objektů BLOB v rámci kontejneru prostřednictvím anonymní požadavek, ale nelze vytvořit výčet kontejnerů v rámci účtu úložiště.</li><li>Veřejný přístup pro čtení pouze pro objekty BLOB: Data objektů blob v rámci tohoto kontejneru lze číst prostřednictvím anonymní ho požadavku, ale data kontejneru není k dispozici. Klienti nemohou vytvořit výčet objektů BLOB v kontejneru prostřednictvím anonymního požadavku.</li><li>Žádný veřejný přístup pro čtení: Data kontejneru a objektu blob může číst jenom vlastník účtu.</li></ul><p>Anonymní přístup je nejvhodnější pro scénáře, kde by určité objekty BLOB měly být vždy k dispozici pro anonymní přístup pro čtení. Pro podrobnější odstupňovanou kontrolu lze vytvořit sdílený přístupový podpis, který umožňuje delegovat omezený přístup pomocí různých oprávnění a v zadaném časovém intervalu. Ujistěte se, že kontejnery a objekty BLOB, které mohou potenciálně obsahovat citlivá data, nejsou náhodně anonymní přístup</p>|

## <a name="grant-limited-access-to-objects-in-azure-storage-using-sas-or-sap"></a><a id="limited-access-sas"></a>Udělit omezený přístup k objektům v úložišti Azure pomocí SAS nebo SAP

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné. |
| **Odkazy**              | [Sdílené přístupové podpisy, část 1: Principy modelu SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/), [Sdílené přístupové podpisy, část 2: Vytvoření a použití SAS s úložištěm objektů Blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/), [Jak delegovat přístup k objektům ve vašem účtu pomocí sdílených přístupových podpisů a uložených přístupových zásad](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies) |
| **Kroky** | <p>Použití sdíleného přístupového podpisu (SAS) je účinný způsob, jak udělit omezený přístup k objektům v účtu úložiště ostatním klientům, aniž byste museli vystavit přístupový klíč účtu. SAS je identifikátor URI, který ve svých parametrech dotazu zahrnuje všechny informace potřebné pro ověřený přístup k prostředku úložiště. Pro přístup k prostředkům úložiště s SAS, klient potřebuje pouze předat v SAS příslušného konstruktoru nebo metody.</p><p>SAS můžete použít, pokud chcete poskytnout přístup k prostředkům v účtu úložiště klientovi, kterému nelze důvěřovat s klíčem účtu. Klíče účtu úložiště obsahují primární i sekundární klíč, který udělí přístup správce k vašemu účtu a všem prostředkům v něm. Vystavením některého z klíčů vašeho účtu se váš účet otevře možnosti škodlivého nebo nedbalého použití. Sdílené přístupové podpisy poskytují bezpečnou alternativu, která umožňuje ostatním klientům číst, zapisovat a odstraňovat data v účtu úložiště podle oprávnění, která jste udělili, a bez nutnosti klíče účtu.</p><p>Pokud máte logickou sadu parametrů, které jsou podobné pokaždé, pomocí zásady uloženého přístupu (SAP) je lepší nápad. Vzhledem k tomu, že použití SAS odvozené z zásad y uloženého přístupu umožňuje odvolat, že SAS okamžitě, je doporučeným osvědčeným postupem vždy používat uložené zásady přístupu, pokud je to možné.</p>|

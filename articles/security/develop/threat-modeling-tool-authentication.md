---
title: Ověřování-Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Přečtěte si o zmírnění ověřování v Threat Modeling Tool. Podívejte se na informace o zmírnění rizik a zobrazte příklady kódu.
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
ms.custom: has-adal-ref, devx-track-js, devx-track-csharp
ms.openlocfilehash: eade2d24dccb9fc8033a903be5e48b07b44e17ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317986"
---
# <a name="security-frame-authentication--mitigations"></a>Rámec zabezpečení: ověřování | Hrozeb

| Produkt/služba | Článek |
| --------------- | ------- |
| **Webová aplikace**    | <ul><li>[Zvažte použití mechanismu standardního ověřování pro ověření u webové aplikace](#standard-authn-web-app)</li><li>[Aplikace musí bezpečně zpracovávat scénáře neúspěšného ověření.](#handle-failed-authn)</li><li>[Povolit krokování nebo adaptivní ověřování](#step-up-adaptive-authn)</li><li>[Zajistěte, aby byla rozhraní pro správu náležitě uzamčena.](#admin-interface-lockdown)</li><li>[Zabezpečená implementace funkcí zapomenutého hesla](#forgot-pword-fxn)</li><li>[Ujistěte se, že jsou implementované zásady hesel a účtů.](#pword-account-policy)</li><li>[Implementace ovládacích prvků, aby se zabránilo výčtu uživatelského jména](#controls-username-enum)</li></ul> |
| **Databáze** | <ul><li>[Pokud je to možné, použijte ověřování systému Windows pro připojení k SQL Server](#win-authn-sql)</li><li>[Pokud je to možné, použijte Azure Active Directory ověřování pro připojení k SQL Database](#aad-authn-sql)</li><li>[Při použití režimu ověřování SQL se ujistěte, že je na SQL serveru vynutila zásada účtu a hesla.](#authn-account-pword)</li><li>[Nepoužívat ověřování SQL v obsažených databázích](#autn-contained-db)</li></ul> |
| **Azure Event Hub** | <ul><li>[Použití přihlašovacích údajů pro ověřování zařízení pomocí tokenů SaS](#authn-sas-tokens)</li></ul> |
| **Hranice důvěry Azure** | <ul><li>[Povolení služby Azure Multi-Factor Authentication pro správce Azure](#multi-factor-azure-admin)</li></ul> |
| **Service Fabric hranice důvěryhodnosti** | <ul><li>[Omezení anonymního přístupu na cluster Service Fabric](#anon-access-cluster)</li><li>[Ujistěte se, že Service Fabric certifikát klienta na uzel se liší od certifikátu Node-to-Node.](#fabric-cn-nn)</li><li>[Použití AAD k ověřování klientů v clusterech Service Fabric](#aad-client-fabric)</li><li>[Zajistěte, aby se certifikáty Service Fabric získaly od schválené certifikační autority (CA).](#fabric-cert-ca)</li></ul> |
| **Server identit** | <ul><li>[Použití standardních scénářů ověřování podporovaných serverem identity](#standard-authn-id)</li><li>[Přepsat výchozí mezipaměť tokenů serveru identity pomocí škálovatelné alternativy](#override-token)</li></ul> |
| **Hranice důvěryhodnosti počítače** | <ul><li>[Zajistěte, aby byly binární soubory nasazené aplikace digitálně podepsané.](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Povolit ověřování při připojování k frontám MSMQ ve službě WCF](#msmq-queues)</li><li>[WCF – nenastavit zprávu clientCredentialType na None](#message-none)</li><li>[WCF – nenastavit Transport clientCredentialType na None](#transport-none)</li></ul> |
| **Web API** | <ul><li>[Zajistěte, aby se pro zabezpečení webových rozhraní API používaly standardní techniky ověřování.](#authn-secure-api)</li></ul> |
| **Služba Azure AD** | <ul><li>[Použití standardních scénářů ověřování podporovaných nástrojem Azure Active Directory](#authn-aad)</li><li>[Přepsat výchozí mezipaměť tokenů ADAL s škálovatelnou alternativou](#adal-scalable)</li><li>[Ujistěte se, že se používá TokenReplayCache k zabránění opakovanému přehrání ověřovacích tokenů ADAL.](#tokenreplaycache-adal)</li><li>[Použití knihoven ADAL ke správě žádostí o tokeny od klientů OAuth2 do AAD (nebo místního AD)](#adal-oauth2)</li></ul> |
| **Brána pole IoT** | <ul><li>[Ověřování zařízení připojujících se k bráně pole](#authn-devices-field)</li></ul> |
| **Cloudová brána IoT** | <ul><li>[Ujistěte se, že jsou ověřená zařízení připojující se ke cloudové bráně.](#authn-devices-cloud)</li><li>[Použití přihlašovacích údajů pro ověřování podle zařízení](#authn-cred)</li></ul> |
| **Azure Storage** | <ul><li>[Zajistěte, aby byl anonymní přístup pro čtení zadán pouze požadovaným kontejnerům a objektům blob.](#req-containers-anon)</li><li>[Udělení omezeného přístupu k objektům ve službě Azure Storage pomocí SAS nebo SAP](#limited-access-sas)</li></ul> |

## <a name="consider-using-a-standard-authentication-mechanism-to-authenticate-to-web-application"></a><a id="standard-authn-web-app"></a>Zvažte použití mechanismu standardního ověřování pro ověření u webové aplikace

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | Není k dispozici  |
| Podrobnosti | <p>Ověřování je proces, při kterém entita prokáže svou identitu, obvykle prostřednictvím přihlašovacích údajů, jako je uživatelské jméno a heslo. K dispozici je několik ověřovacích protokolů, které je možné zvážit. Některé z nich jsou uvedeny níže:</p><ul><li>Klientské certifikáty</li><li>Založené na systému Windows</li><li>Založené na formulářích</li><li>Federace – ADFS</li><li>Federace – Azure AD</li><li>Federační server identity</li></ul><p>Zvažte použití mechanismu standardního ověřování k identifikaci zdrojového procesu.</p>|

## <a name="applications-must-handle-failed-authentication-scenarios-securely"></a><a id="handle-failed-authn"></a>Aplikace musí bezpečně zpracovávat scénáře neúspěšného ověření.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | Není k dispozici  |
| Podrobnosti | <p>Aplikace, které explicitně ověřují uživatele, musí správně zpracovat scénáře neúspěšného ověření. Mechanismus ověřování musí:</p><ul><li>Odepřít přístup k privilegovaným prostředkům v případě neúspěšného ověření</li><li>Po neúspěšném ověření zobrazit obecnou chybovou zprávu a dojde k odepření přístupu</li></ul><p>Test pro:</p><ul><li>Ochrana privilegovaných prostředků po neúspěšných přihlášeních</li><li>V případě neúspěšného ověření se zobrazí obecná chybová zpráva a přístup k odepřeným událostem:</li><li>Účty jsou po nadměrném počtu neúspěšných pokusů zakázané.</li><ul>|

## <a name="enable-step-up-or-adaptive-authentication"></a><a id="step-up-adaptive-authn"></a>Povolit krokování nebo adaptivní ověřování

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | Není k dispozici  |
| Podrobnosti | <p>Ověřte, že aplikace má další autorizaci (například krok nahoru nebo adaptivní ověřování, prostřednictvím služby Multi-Factor Authentication, jako je odeslání jednorázového hesla, e-mailu atd. nebo dotazování na opětovné ověření), aby byl uživatel před udělením přístupu k citlivým informacím vyzván. Toto pravidlo platí také pro provádění kritických změn účtu nebo akce.</p><p>To také znamená, že přizpůsobení ověřování musí být implementováno takovým způsobem, že aplikace správně vynutila ověřování s kontextem, aby nepovolovala neoprávněné manipulace prostřednictvím, například manipulace s parametry.</p>|

## <a name="ensure-that-administrative-interfaces-are-appropriately-locked-down"></a><a id="admin-interface-lockdown"></a>Zajistěte, aby byla rozhraní pro správu náležitě uzamčena.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | Není k dispozici  |
| Podrobnosti | Prvním řešením je udělit přístup jenom z určitého zdrojového rozsahu IP adres k rozhraní pro správu. Pokud by toto řešení nebylo možné, doporučujeme, abyste pro přihlášení k rozhraní pro správu vždy vynutili krokování nebo adaptivní ověřování. |

## <a name="implement-forgot-password-functionalities-securely"></a><a id="forgot-pword-fxn"></a>Zabezpečená implementace funkcí zapomenutého hesla

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | Není k dispozici  |
| Podrobnosti | <p>První věc je ověřit, že zapomenuté heslo a další cesty pro obnovení odesílají odkaz, včetně aktivačního tokenu s časovým limitem, a ne samotného hesla. Další ověřování na základě provizorních tokenů (např. token SMS, nativní mobilní aplikace atd.) se může vyžadovat i před odesláním odkazu. Za druhé, neměli byste zamknout účet uživatele, zatímco probíhá proces získávání nového hesla.</p><p>To může vést k útokům na útok DOS pokaždé, když se útočník rozhodne úmyslně uzamknout uživatele pomocí automatizovaného útoku. Třetí, pokaždé, když byla nastavena nová žádost o heslo, by se měla zobecnit zpráva, kterou zobrazíte, aby se zabránilo vyčíslení uživatelského jména. Čtvrtý, vždycky zakažte použití starých hesel a Implementujte zásady silného hesla.</p> |

## <a name="ensure-that-password-and-account-policy-are-implemented"></a><a id="pword-account-policy"></a>Ujistěte se, že jsou implementované zásady hesel a účtů.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | Není k dispozici  |
| Podrobnosti | <p>Měli byste implementovat zásady hesel a účtů v souladu se zásadami organizace a osvědčenými postupy.</p><p>Pro obranu před odhadem na základě slovníku nenáročného na slovník: je nutné implementovat zásady silného hesla, aby uživatelé mohli vytvářet složitá hesla (například 12 znaků minimální délka, alfanumerické a speciální znaky).</p><p>Zásady uzamčení účtů mohou být implementovány následujícím způsobem:</p><ul><li>**Částečný zámek:** Tato možnost může být vhodná pro ochranu uživatelů před útoky hrubou silou. Například pokaždé, když uživatel zadá špatné heslo třikrát, může aplikace Uzamknout účet za minutu, aby bylo možné zpomalit proces hrubosti vynuceného používání hesla, což by mohlo útočník snížit, aby bylo méně ziskové. Pokud jste v tomto příkladu implementovali protiopatření pevně na zamykací úrovni, měli byste pomocí možnosti "DoS" trvale zamykat účty. Aplikace může také vygenerovat jednorázové heslo (jednorázové heslo) a odeslat ho mimo IP síť (prostřednictvím e-mailu, SMS atd.) uživateli. Dalším přístupem může být implementace CAPTCHA po dosažení prahového počtu neúspěšných pokusů.</li><li>**Pevné uzamčení:** Tento typ uzamčení by měl být použit vždy, když zjistíte, že uživatel nastavil útok na svou aplikaci, a vyhodnotit je pomocí trvalého uzamknutí účtu, dokud by tým odpovědí neměl čas na jejich forenzní. Po tomto procesu se můžete rozhodnout dát uživateli zpátky svůj účet nebo provést další zákonné akce proti nim. Tento typ přístupu brání útočníkovi v dalším průniku vaší aplikace a infrastruktury.</li></ul><p>Aby bylo možné chránit před útoky ve výchozích a předvídatelných účtech, ověřte, zda jsou všechny klíče a hesla nahraditelný a zda jsou vygenerovány nebo nahrazeny po čase instalace.</p><p>Pokud aplikace musí automaticky generovat hesla, ujistěte se, že vygenerovaná hesla jsou náhodná a mají vysokou entropii.</p>|

## <a name="implement-controls-to-prevent-username-enumeration"></a><a id="controls-username-enum"></a>Implementace ovládacích prvků, aby se zabránilo výčtu uživatelského jména

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | Není k dispozici  |
| **Kroky** | Všechny chybové zprávy by se měly zobecnit, aby se zabránilo vyčíslení uživatelského jména. V některých případech se někdy nemůžete vyhnout úniku informací, jako je třeba registrační stránka. Tady je potřeba použít metody omezování četnosti, jako je CAPTCHA, aby se zabránilo automatickému útoku útočníka. |

## <a name="when-possible-use-windows-authentication-for-connecting-to-sql-server"></a><a id="win-authn-sql"></a>Pokud je to možné, použijte ověřování systému Windows pro připojení k SQL Server

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | databáze |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | Místní |
| **Atributy**              | SQL verze – vše |
| **Reference**              | [SQL Server – výběr režimu ověřování](https://msdn.microsoft.com/library/ms144284.aspx) |
| **Kroky** | Ověřování systému Windows používá protokol zabezpečení Kerberos, zajišťuje vynucování zásad hesel s ohledem na ověřování složitosti pro silná hesla, poskytuje podporu pro uzamknutí účtu a podporuje vypršení platnosti hesla.|

## <a name="when-possible-use-azure-active-directory-authentication-for-connecting-to-sql-database"></a><a id="aad-authn-sql"></a>Pokud je to možné, použijte Azure Active Directory ověřování pro připojení k SQL Database

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | databáze |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | SQL Azure |
| **Atributy**              | SQL verze – V12 |
| **Reference**              | [Připojení k SQL Database pomocí ověřování Azure Active Directory](https://azure.microsoft.com/documentation/articles/sql-database-aad-authentication/) |
| **Kroky** | **Minimální verze:** Azure SQL Database V12 je nutné, aby Azure SQL Database mohl používat ověřování AAD proti adresáři Microsoftu. |

## <a name="when-sql-authentication-mode-is-used-ensure-that-account-and-password-policy-are-enforced-on-sql-server"></a><a id="authn-account-pword"></a>Při použití režimu ověřování SQL se ujistěte, že je na SQL serveru vynutila zásada účtu a hesla.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | databáze |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [Zásady hesla SQL Server](https://technet.microsoft.com/library/ms161959(v=sql.110).aspx) |
| **Kroky** | Při použití ověřování SQL Server se přihlašovací jména vytvářejí v SQL Server, která nejsou založená na uživatelských účtech Windows. Uživatelské jméno i heslo se vytvoří pomocí SQL Server a ukládají se v SQL Server. SQL Server můžou používat mechanismy zásad hesel Windows. Může použít stejné zásady složitosti a vypršení platnosti používané v systému Windows k heslům použitým v SQL Server. |

## <a name="do-not-use-sql-authentication-in-contained-databases"></a><a id="autn-contained-db"></a>Nepoužívat ověřování SQL v obsažených databázích

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | databáze |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | OnPrem, SQL Azure |
| **Atributy**              | SQL verze – MSSQL2012, verze SQL – V12 |
| **Reference**              | [Osvědčené postupy zabezpečení s databázemi s omezením](https://msdn.microsoft.com/library/ff929055.aspx) |
| **Kroky** | Nepřítomnost zásad vynutilosti hesla může zvýšit pravděpodobnost, že se v databázi s omezením naváže slabé přihlašovací údaje. Využijte ověřování systému Windows. |

## <a name="use-per-device-authentication-credentials-using-sas-tokens"></a><a id="authn-sas-tokens"></a>Použití přihlašovacích údajů pro ověřování zařízení pomocí tokenů SaS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Event Hub |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [Přehled ověřování a modelu zabezpečení Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroky** | <p>Model zabezpečení Event Hubs je založen na kombinaci tokenů sdíleného přístupového podpisu (SAS) a vydavatelů událostí. Název vydavatele představuje DeviceID, který obdrží token. To by pomohlo přidružit tokeny vygenerované příslušnému zařízení.</p><p>Všechny zprávy jsou označeny pomocí původce na straně služby, což umožňuje detekci pokusů o falšování zdroje v datové části. Při ověřování zařízení vygenerujte token SaS vázaný na zařízení, které je v oboru jedinečného vydavatele.</p>|

## <a name="enable-azure-multi-factor-authentication-for-azure-administrators"></a><a id="multi-factor-azure-admin"></a>Povolení služby Azure Multi-Factor Authentication pro správce Azure

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice důvěry Azure |
| **Fáze SDL**               | Nasazení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [Co je Azure Multi-Factor Authentication?](https://azure.microsoft.com/documentation/articles/multi-factor-authentication/) |
| **Kroky** | <p>Multi-Factor Authentication (MFA) je metoda ověřování, která vyžaduje více než jednu metodu ověřování a přidává kritickou druhou vrstvu zabezpečení pro přihlášení a transakce uživatele. Funguje tak, že vyžaduje některou ze dvou nebo více následujících metod ověřování:</p><ul><li>Něco, co znáte (obvykle heslo)</li><li>Něco, co máte (důvěryhodné zařízení, které není jednoduše duplikováno, jako je telefon)</li><li>Něco, co používáte (biometrika)</li><ul>|

## <a name="restrict-anonymous-access-to-service-fabric-cluster"></a><a id="anon-access-cluster"></a>Omezení anonymního přístupu na cluster Service Fabric

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Service Fabric hranice důvěryhodnosti |
| **Fáze SDL**               | Nasazení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | Prostředí – Azure  |
| **Reference**              | [Service Fabric scénáře zabezpečení clusteru](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security) |
| **Kroky** | <p>Clustery by měly být vždy zabezpečené, aby se zabránilo neautorizovaným uživatelům v připojení ke clusteru, zejména pokud má spuštěné provozní úlohy.</p><p>Při vytváření clusteru Service Fabric se ujistěte, že je režim zabezpečení nastavený na zabezpečení a že je nakonfigurovaný požadovaný certifikát serveru X. 509. Když se vytvoří "nezabezpečený" cluster, umožní se k němu připojit anonymní uživatel, pokud zveřejňuje koncové body správy pro veřejný Internet.</p>|

## <a name="ensure-that-service-fabric-client-to-node-certificate-is-different-from-node-to-node-certificate"></a><a id="fabric-cn-nn"></a>Ujistěte se, že Service Fabric certifikát klienta na uzel se liší od certifikátu Node-to-Node.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Service Fabric hranice důvěryhodnosti |
| **Fáze SDL**               | Nasazení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | Prostředí – Azure, prostředí – samostatné |
| **Reference**              | [Service Fabric zabezpečení certifikátu klienta na uzel](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#_client-to-node-certificate-security), [Připojte se k zabezpečenému clusteru pomocí klientského certifikátu](https://azure.microsoft.com/documentation/articles/service-fabric-connect-to-secure-cluster/) . |
| **Kroky** | <p>Zabezpečení certifikátů klienta na uzlu se konfiguruje při vytváření clusteru buď prostřednictvím Azure Portal, Správce prostředků šablon nebo samostatné šablony JSON zadáním certifikátu klienta správce nebo klientského certifikátu uživatele.</p><p>Zadané certifikáty klienta a klienta pro správu by se měly lišit od primárních a sekundárních certifikátů, které zadáte pro zabezpečení mezi uzly.</p>|

## <a name="use-aad-to-authenticate-clients-to-service-fabric-clusters"></a><a id="aad-client-fabric"></a>Použití AAD k ověřování klientů v clusterech Service Fabric

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Service Fabric hranice důvěryhodnosti |
| **Fáze SDL**               | Nasazení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | Prostředí – Azure |
| **Reference**              | [Scénáře zabezpečení clusteru – doporučení pro zabezpečení](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#security-recommendations) |
| **Kroky** | Clustery běžící v Azure můžou také zabezpečit přístup k koncovým bodům správy pomocí Azure Active Directory (AAD) Kromě klientských certifikátů. U clusterů Azure se doporučuje použít zabezpečení AAD k ověřování klientů a certifikátů pro zabezpečení mezi uzly.|

## <a name="ensure-that-service-fabric-certificates-are-obtained-from-an-approved-certificate-authority-ca"></a><a id="fabric-cert-ca"></a>Zajistěte, aby se certifikáty Service Fabric získaly od schválené certifikační autority (CA).

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Service Fabric hranice důvěryhodnosti |
| **Fáze SDL**               | Nasazení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | Prostředí – Azure |
| **Reference**              | [Certifikáty X. 509 a Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#x509-certificates-and-service-fabric) |
| **Kroky** | <p>Service Fabric k ověřování uzlů a klientů používá certifikáty serveru X. 509.</p><p>Mezi důležité věci, které je potřeba vzít v úvahu při používání certifikátů v Service fabricu:</p><ul><li>Certifikáty používané v clusterech s provozními úlohami by se měly vytvořit pomocí správně nakonfigurované služby Certificate Service systému Windows Server nebo získat od schválené certifikační autority (CA). Certifikační autorita může být schválená externí certifikační autoritou nebo správně spravovaná interní infrastruktura veřejných klíčů (PKI).</li><li>Nikdy nepoužívejte v produkčním prostředí žádné dočasné ani testovací certifikáty, které se vytvářejí pomocí nástrojů, jako je MakeCert.exe</li><li>Můžete použít certifikát podepsaný svým držitelem, ale proveďte ho pouze pro testovací clustery, nikoli pro produkční prostředí.</li></ul>|

## <a name="use-standard-authentication-scenarios-supported-by-identity-server"></a><a id="standard-authn-id"></a>Použití standardních scénářů ověřování podporovaných serverem identity

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Server identit |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [IdentityServer3 – velký obrázek](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Kroky** | <p>Níže jsou uvedené typické interakce podporované serverem identity:</p><ul><li>Prohlížeče komunikují s webovými aplikacemi</li><li>Webové aplikace komunikují s webovými rozhraními API (někdy v zastoupení uživatele).</li><li>Aplikace založené na prohlížeči, které komunikují s webovými rozhraními API</li><li>Nativní aplikace komunikující s webovými rozhraními API</li><li>Serverové aplikace komunikující s webovými rozhraními API</li><li>Webová rozhraní API komunikují s webovými rozhraními API (někdy v zastoupení uživatele).</li></ul>|

## <a name="override-the-default-identity-server-token-cache-with-a-scalable-alternative"></a><a id="override-token"></a>Přepsat výchozí mezipaměť tokenů serveru identity pomocí škálovatelné alternativy

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Server identit |
| **Fáze SDL**               | Nasazení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [Nasazení serveru identity – ukládání do mezipaměti](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Kroky** | <p>IdentityServer má jednoduchou vestavěnou mezipaměť v paměti. I když je to pro nativní aplikace v malém měřítku dobré, není škálované pro aplikace střední vrstvy a back-endu z následujících důvodů:</p><ul><li>K těmto aplikacím má přistup mnoho uživatelů najednou. Uložení všech přístupových tokenů ve stejném úložišti vytvoří problémy s izolací a prezentuje problémy při současném škálování: mnoho uživatelů, každý s tolika tokeny jako prostředky, ke kterým má aplikace přístup, může znamenat obrovský počet a velmi nákladné operace vyhledávání.</li><li>Tyto aplikace jsou obvykle nasazeny v distribuovaných topologiích, kde více uzlů musí mít přístup ke stejné mezipaměti.</li><li>Tokeny uložené v mezipaměti musí zacházet s recyklací procesu a deaktivace.</li><li>U všech výše uvedených důvodů doporučujeme při implementaci webových aplikací přepsat výchozí mezipaměť tokenů serveru identit pomocí škálovatelné alternativy, jako je například Azure cache pro Redis.</li></ul>|

## <a name="ensure-that-deployed-applications-binaries-are-digitally-signed"></a><a id="binaries-signed"></a>Zajistěte, aby byly binární soubory nasazené aplikace digitálně podepsané.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice důvěryhodnosti počítače |
| **Fáze SDL**               | Nasazení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | Není k dispozici  |
| **Kroky** | Zajistěte, aby byly binární soubory nasazené aplikace digitálně podepsané, aby bylo možné ověřit integritu binárních souborů.|

## <a name="enable-authentication-when-connecting-to-msmq-queues-in-wcf"></a><a id="msmq-queues"></a>Povolit ověřování při připojování k frontám MSMQ ve službě WCF

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | Obecné, NET Framework 3 |
| **Atributy**              | Není k dispozici |
| **Reference**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx) |
| **Kroky** | Programu se nepodařilo povolit ověřování při připojování k frontám MSMQ, útočník může anonymně odeslat zprávy do fronty ke zpracování. Pokud se ověřování nepoužívá pro připojení k frontě MSMQ použité k doručení zprávy jinému programu, útočník by mohl odeslat anonymní zprávu, která je škodlivá.|

### <a name="example"></a>Příklad
`<netMsmqBinding/>`Element konfiguračního souboru služby WCF vydá pokyn pro WCF, aby při připojení k frontě MSMQ pro doručování zpráv nezakázal ověřování.
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
Nakonfigurujte službu MSMQ tak, aby pro každou příchozí nebo odchozí zprávu vyžadovala všechny ověřování domény a certifikátů systému Windows.

### <a name="example"></a>Příklad
`<netMsmqBinding/>`Element konfiguračního souboru služby WCF vydá pokyn pro WCF k povolení ověřování certifikátů při připojování ke frontě MSMQ. Klient se ověřuje pomocí certifikátů X. 509. Certifikát klienta se musí nacházet v úložišti certifikátů serveru.
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

## <a name="wcf-do-not-set-message-clientcredentialtype-to-none"></a><a id="message-none"></a>WCF – nenastavit zprávu clientCredentialType na None

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | .NET Framework 3 |
| **Atributy**              | Typ přihlašovacích údajů klienta – žádné |
| **Reference**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Kroky** | Absence ověřování znamená, že všichni uživatelé budou mít přístup k této službě. Služba, která neověřuje své klienty, umožňuje přístup všem uživatelům. Nakonfigurujte aplikaci pro ověřování pomocí přihlašovacích údajů klienta. Můžete to udělat nastavením clientCredentialType zprávy na Windows nebo certifikát. |

### <a name="example"></a>Příklad
```
<message clientCredentialType=""Certificate""/>
```

## <a name="wcf-do-not-set-transport-clientcredentialtype-to-none"></a><a id="transport-none"></a>WCF – nenastavit Transport clientCredentialType na None

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | Obecné, .NET Framework 3 |
| **Atributy**              | Typ přihlašovacích údajů klienta – žádné |
| **Reference**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Kroky** | Absence ověřování znamená, že všichni uživatelé budou mít přístup k této službě. Služba, která neověřuje klienty, umožňuje všem uživatelům přístup k jejím funkcím. Nakonfigurujte aplikaci pro ověřování pomocí přihlašovacích údajů klienta. To se dá udělat nastavením přenosového clientCredentialTypeu na Windows nebo certifikát. |

### <a name="example"></a>Příklad
```
<transport clientCredentialType=""Certificate""/>
```

## <a name="ensure-that-standard-authentication-techniques-are-used-to-secure-web-apis"></a><a id="authn-secure-api"></a>Zajistěte, aby se pro zabezpečení webových rozhraní API používaly standardní techniky ověřování.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [Ověřování a autorizace ve webovém rozhraní api ASP.NET](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api), [služby externích ověřování s webovým rozhraním API ASP.NET (C#)](https://www.asp.net/web-api/overview/security/external-authentication-services) |
| **Kroky** | <p>Ověřování je proces, při kterém entita prokáže svou identitu, obvykle prostřednictvím přihlašovacích údajů, jako je uživatelské jméno a heslo. K dispozici je několik ověřovacích protokolů, které je možné zvážit. Některé z nich jsou uvedeny níže:</p><ul><li>Klientské certifikáty</li><li>Založené na systému Windows</li><li>Založené na formulářích</li><li>Federace – ADFS</li><li>Federace – Azure AD</li><li>Federační server identity</li></ul><p>Odkazy v části odkazy poskytují podrobné informace o tom, jak je možné implementovat každé z schémat ověřování k zabezpečení webového rozhraní API.</p>|

## <a name="use-standard-authentication-scenarios-supported-by-azure-active-directory"></a><a id="authn-aad"></a>Použití standardních scénářů ověřování podporovaných nástrojem Azure Active Directory

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure AD |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [Scénáře ověřování pro Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/), [Azure Active Directory ukázky kódu](https://azure.microsoft.com/documentation/articles/active-directory-code-samples/) [Azure Active Directory příručka pro vývojáře](https://azure.microsoft.com/documentation/articles/active-directory-developers-guide/) |
| **Kroky** | <p>Azure Active Directory (Azure AD) zjednodušuje ověřování pro vývojáře tím, že poskytuje identitu jako službu a podporuje standardní protokoly, jako je OAuth 2,0 a OpenID Connect. Níže jsou uvedené pět scénářů primárních aplikací podporovaných službou Azure AD:</p><ul><li>Webový prohlížeč do webové aplikace: uživatel se musí přihlásit k webové aplikaci, která je zabezpečená službou Azure AD.</li><li>Jednostránkové aplikace (SPA): uživatel se musí přihlásit k jednostránkové aplikaci, která je zabezpečená službou Azure AD.</li><li>Nativní aplikace do webového rozhraní API: nativní aplikace, která běží na telefonu, tabletu nebo počítači, musí ověřit uživatele a získat prostředky z webového rozhraní API zabezpečeného službou Azure AD.</li><li>Webová aplikace do webového rozhraní API: webová aplikace potřebuje získat prostředky z webového rozhraní API zabezpečeného službou Azure AD.</li><li>Démon nebo serverová aplikace do webového rozhraní API: aplikace démona nebo serverová aplikace bez webového uživatelského rozhraní musí získat prostředky z webového rozhraní API zabezpečeného službou Azure AD.</li></ul><p>Podrobnosti o implementaci nižší úrovně najdete v odkazech v části odkazy.</p>|

## <a name="override-the-default-adal-token-cache-with-a-scalable-alternative"></a><a id="adal-scalable"></a>Přepsat výchozí mezipaměť tokenů ADAL s škálovatelnou alternativou

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure AD |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [Moderní ověřování pomocí Azure Active Directory pro webové aplikace s](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) [využitím Redis jako mezipaměti tokenů ADAL](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **Kroky** | <p>Výchozí mezipaměť, kterou používá ADAL (Active Directory Authentication Library), je mezipaměť uložená v paměti, která spoléhá na statický obchod, který je dostupný v rámci procesu. I když to funguje u nativních aplikací, neškáluje se pro aplikace na úrovni střední úrovně a back-end z následujících důvodů:</p><ul><li>K těmto aplikacím má přistup mnoho uživatelů najednou. Uložení všech přístupových tokenů ve stejném úložišti vytvoří problémy s izolací a prezentuje problémy při současném škálování: mnoho uživatelů, každý s tolika tokeny jako prostředky, ke kterým má aplikace přístup, může znamenat obrovský počet a velmi nákladné operace vyhledávání.</li><li>Tyto aplikace jsou obvykle nasazeny v distribuovaných topologiích, kde více uzlů musí mít přístup ke stejné mezipaměti.</li><li>Tokeny uložené v mezipaměti musí zacházet s recyklací procesu a deaktivace.</li></ul><p>U všech výše uvedených důvodů doporučujeme při implementaci webových aplikací přepsat výchozí mezipaměť tokenů ADAL s škálovatelnou alternativou, jako je například Azure cache pro Redis.</p>|

## <a name="ensure-that-tokenreplaycache-is-used-to-prevent-the-replay-of-adal-authentication-tokens"></a><a id="tokenreplaycache-adal"></a>Ujistěte se, že se používá TokenReplayCache k zabránění opakovanému přehrání ověřovacích tokenů ADAL.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure AD |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [Moderní ověřování pomocí Azure Active Directory pro webové aplikace](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) |
| **Kroky** | <p>Vlastnost TokenReplayCache umožňuje vývojářům definovat mezipaměť pro opětovné přehrání tokenu, úložiště, které lze použít k ukládání tokenů za účelem ověření, že žádný token nelze použít více než jednou.</p><p>Jedná se o míru proti běžnému útoku, aptly se nazývá útok na opětovné přehrání tokenu: útočník, který zachytává token, který se poslal při přihlášení, se může pokusit znovu odeslat aplikaci ("přehrát") a vytvořit novou relaci. Například v toku pro udělení kódu OIDC po úspěšném ověření uživatele se vytvoří požadavek na koncový bod "/SignIn-oidc" předávající strany s parametry "id_token", "Code" a "State".</p><p>Předávající strana ověří tento požadavek a vytvoří novou relaci. Pokud nežádoucí osoba tento požadavek zachytí a znovu ho přehraje, může vytvořit úspěšnou relaci a uživatele s falešnou identitou. Přítomnost hodnoty nonce v OpenID Connect se může omezit, ale ne zcela eliminovat okolnosti, za kterých se dá útok úspěšně přijmout. Pro ochranu svých aplikací můžou vývojáři poskytnout implementaci ITokenReplayCache a přiřadit instanci k TokenReplayCache.</p>|

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
Zde je ukázková implementace rozhraní ITokenReplayCache. (Přizpůsobení a implementace rozhraní pro ukládání do mezipaměti specifického pro projekt)
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
Implementovaná mezipaměť musí být odkazována v možnostech OIDC prostřednictvím vlastnosti TokenValidationParameters, jak je znázorněno níže.
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

Počítejte s tím, že pokud chcete otestovat efektivitu této konfigurace, přihlaste se do místní aplikace chráněné v OIDC a zachyťte požadavek do `"/signin-oidc"` koncového bodu v Fiddler. Pokud ochrana není na svém místě, bude přehrání této žádosti v Fiddler nastavit nový soubor cookie relace. Po přidání žádosti po přidání TokenReplayCache ochrany vyvolá aplikace výjimku následujícím způsobem: `SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a name="use-adal-libraries-to-manage-token-requests-from-oauth2-clients-to-aad-or-on-premises-ad"></a><a id="adal-oauth2"></a>Použití knihoven ADAL ke správě žádostí o tokeny od klientů OAuth2 do AAD (nebo místního AD)

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure AD |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [ADAL](https://azure.microsoft.com/documentation/articles/active-directory-authentication-libraries/) |
| **Kroky** | <p>Knihovna Azure AD Authentication Library (ADAL) umožňuje vývojářům klientských aplikací snadno ověřovat uživatele v cloudu nebo místní službě Active Directory (AD) a pak získat přístupové tokeny pro zabezpečení volání rozhraní API.</p><p>ADAL má mnoho funkcí, které usnadňují ověřování pro vývojáře, jako je například asynchronní podpora, konfigurovatelný mezipaměť tokenu, která ukládá přístupové tokeny a aktualizuje tokeny, automatickou aktualizaci tokenu, když vyprší platnost přístupového tokenu a je k dispozici aktualizační token a další.</p><p>S využitím většiny složitosti může ADAL pomáhat vývojářům ve své aplikaci soustředit se na obchodní logiku a snadno zabezpečit prostředky, aniž by byl odborník na zabezpečení. K dispozici jsou samostatné knihovny pro .NET, JavaScript (klient a Node.js), Python, iOS, Android a Java.</p>|

## <a name="authenticate-devices-connecting-to-the-field-gateway"></a><a id="authn-devices-field"></a>Ověřování zařízení připojujících se k bráně pole

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána pole IoT |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | Není k dispozici  |
| **Kroky** | Ujistěte se, že je každé zařízení ověřené bránou pole před přijetím dat z nich a před tím, než usnadníte komunikaci s cloudovou bránou. Ujistěte se také, že se zařízení připojují k přihlašovacím údajům na zařízení, aby bylo možné jednotlivá zařízení jednoznačně identifikovat.|

## <a name="ensure-that-devices-connecting-to-cloud-gateway-are-authenticated"></a><a id="authn-devices-cloud"></a>Ujistěte se, že jsou ověřená zařízení připojující se ke cloudové bráně.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Cloudová brána IoT |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | Generic, C#, Node.JS,  |
| **Atributy**              | Není k dispozici, volba brány – Azure IoT Hub |
| **Reference**              | Není k dispozici, [Azure IoT Hub s .NET](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/), [Začínáme se službou IoT Hub a Node js](https://azure.microsoft.com/documentation/articles/iot-hub-node-node-getstarted), [zabezpečení IoT pomocí SAS a certifikátů](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/), [úložiště Git](https://github.com/Azure/azure-iot-sdks/) |
| **Kroky** | <ul><li>**Obecné:** Ověřte zařízení pomocí protokolu TLS (Transport Layer Security) nebo IPSec. Infrastruktura by měla podporovat použití předsdíleného klíče (PSK) na zařízeních, která nemůžou zpracovávat úplný asymetrický kryptografii. Využijte Azure AD, OAuth.</li><li>**Jazyk C#:** Při vytváření instance DeviceClient ve výchozím nastavení vytvoří metoda Create instanci DeviceClient, která k komunikaci s IoT Hub používá protokol AMQP. Pokud chcete používat protokol HTTPS, použijte přepis metody Create, který umožňuje určit protokol. Pokud používáte protokol HTTPS, měli byste `Microsoft.AspNet.WebApi.Client` do svého projektu přidat také balíček NuGet, aby zahrnoval `System.Net.Http.Formatting` obor názvů.</li></ul>|

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
**Node.JS: ověřování**
#### <a name="symmetric-key"></a>Symetrický klíč
* Vytvoření služby IoT Hub v Azure
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
  #### <a name="sas-token"></a>Token SAS
* Se interně generuje při použití symetrického klíče, ale můžeme ho vygenerovat a použít explicitně.
* Definovat protokol: `var Http = require('azure-iot-device-http').Http;`
* Vytvořit token SAS:
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
* Připojit pomocí tokenu SAS:
    ```javascript
    Client.fromSharedAccessSignature(sas, Http);
    ```
  #### <a name="certificates"></a>Certifikáty
* Vygenerujte certifikát podepsaný svým vlastníkem pomocí libovolného nástroje, jako je třeba OpenSSL, a vygenerujte soubory. CERT a. Key pro uložení certifikátu a klíč v uvedeném pořadí.
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

## <a name="use-per-device-authentication-credentials"></a><a id="authn-cred"></a>Použití přihlašovacích údajů pro ověřování podle zařízení

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Cloudová brána IoT  |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | Volba brány – Azure IoT Hub |
| **Reference**              | [Tokeny zabezpečení Azure IoT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) |
| **Kroky** | Použijte přihlašovací údaje pro ověřování zařízení pomocí tokenů SaS na základě klíče zařízení nebo klientského certifikátu místo zásad sdíleného přístupu na úrovni IoT Hub. Tím zabráníte opakovanému použití ověřovacích tokenů pro jednu zařízení nebo bránu pole jiným. |

## <a name="ensure-that-only-the-required-containers-and-blobs-are-given-anonymous-read-access"></a><a id="req-containers-anon"></a>Zajistěte, aby byl anonymní přístup pro čtení zadán pouze požadovaným kontejnerům a objektům blob.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | StorageType – objekt BLOB |
| **Reference**              | [Správa anonymního přístupu pro čtení kontejnerů a objektů BLOB](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/), [podpisů sdíleného přístupu, část 1: Principy modelu SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) |
| **Kroky** | <p>Ve výchozím nastavení může být kontejner a všechny objekty BLOB v něm k dispozici pouze vlastníkem účtu úložiště. Pokud chcete anonymním uživatelům udělit oprávnění ke čtení kontejneru a jeho objektů blob, může jeden nastavit oprávnění kontejneru a povolit tak veřejný přístup. Anonymní uživatelé mohou číst objekty BLOB v rámci veřejně přístupného kontejneru bez ověření žádosti.</p><p>Kontejnery poskytují následující možnosti pro správu přístupu k kontejneru:</p><ul><li>Úplný veřejný přístup pro čtení: data kontejneru a objektu BLOB je možné číst prostřednictvím anonymního požadavku. Klienti mohou vytvořit výčet objektů BLOB v kontejneru prostřednictvím anonymního požadavku, ale nemohou vytvořit výčet kontejnerů v rámci účtu úložiště.</li><li>Veřejný přístup pro čtení jenom pro objekty BLOB: data objektu BLOB v tomto kontejneru je možné číst prostřednictvím anonymního požadavku, ale data kontejneru nejsou k dispozici. Klienti nemohou pomocí anonymní žádosti vytvořit výčet objektů BLOB v kontejneru.</li><li>Bez veřejného přístupu pro čtení: data kontejneru a objektu BLOB můžou číst jenom vlastník účtu.</li></ul><p>Anonymní přístup je nejvhodnější pro scénáře, kdy by měly být pro anonymní přístup pro čtení vždy k dispozici určité objekty blob. V případě jemnějšího řízení může jeden vytvořit sdílený přístupový podpis, který umožňuje delegovat omezený přístup pomocí různých oprávnění a v zadaném časovém intervalu. Zajistěte, aby kontejnery a objekty blob, které by mohly obsahovat citlivá data, nechtěně udělily anonymní přístup.</p>|

## <a name="grant-limited-access-to-objects-in-azure-storage-using-sas-or-sap"></a><a id="limited-access-sas"></a>Udělení omezeného přístupu k objektům ve službě Azure Storage pomocí SAS nebo SAP

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage |
| **Fáze SDL**               | Sestavení |
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici |
| **Reference**              | [Signatury sdíleného přístupu, část 1: Princip modelu SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/), [podpisů sdíleného přístupu, část 2: vytvoření a použití SAS s úložištěm BLOB](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/), [Postup delegování přístupu k objektům ve vašem účtu pomocí sdílených přístupových podpisů a zásad uložených přístupu](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies) |
| **Kroky** | <p>Použití sdíleného přístupového podpisu (SAS) představuje účinný způsob, jak udělit omezený přístup k objektům v účtu úložiště jiným klientům, a to bez nutnosti vystavit přístupový klíč účtu. SAS je identifikátor URI, který v parametrech dotazu zahrnuje všechny informace potřebné pro ověřený přístup k prostředku úložiště. Chcete-li získat přístup k prostředkům úložiště pomocí SAS, klient musí předat SAS pouze příslušnému konstruktoru nebo metodě.</p><p>Můžete použít SAS, pokud chcete poskytnout přístup k prostředkům v účtu úložiště klientovi, který nemůže být důvěryhodný pomocí klíče účtu. Klíče účtu úložiště zahrnují primární i sekundární klíč, z nichž oba udělují přístup pro správu k vašemu účtu a všem prostředkům v něm. Vystavení některého z vašich klíčů účtu otevře váš účet s možností škodlivého nebo nedbalostho použití. Sdílené přístupové podpisy poskytují bezpečnou alternativu, která umožňuje ostatním klientům číst, zapisovat a odstraňovat data v účtu úložiště na základě oprávnění, která jste udělili, a nemusíte přitom klíč účtu.</p><p>Pokud máte logickou sadu parametrů, které jsou pokaždé podobné, je lepší nápad pomocí zásad uložených v přístupu (SAP). Vzhledem k tomu, že použití SAS odvozeného z uložených zásad přístupu umožňuje okamžitě odvolat toto SAS, je doporučený osvědčený postup, pokud je to možné, vždy používejte uložené zásady přístupu.</p>|

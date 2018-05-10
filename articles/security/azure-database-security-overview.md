---
title: Přehled zabezpečení databáze Azure | Microsoft Docs
description: Tento článek obsahuje přehled Azure databáze funkce zabezpečení.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: b5e9e412fddf727237b008e37238fa2c547694de
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="azure-database-security-overview"></a>Přehled zabezpečení databáze Azure

Zabezpečení je velmi důležité při správě databáze a byla vždy prioritu pro Azure SQL Database. Azure SQL Database podporuje připojení zabezpečení se pravidla brány firewall a šifrování připojení. Podporuje ověřování pomocí uživatelského jména a hesla a Azure Active Directory, ověřování, který používá identity spravované službou Azure Active Directory. Autorizace používá řízení přístupu na základě rolí.

Azure SQL Database podporuje šifrování provedením v reálném čase šifrování a dešifrování databáze, přidružených záloh a souborů protokolů transakci bez nutnosti změny aplikace.

Společnost Microsoft poskytuje další způsoby, jak šifrování podnikových dat:

-   Šifrování na úrovni buněk k šifrování i buněk dat nebo konkrétní sloupce s různými šifrovacími klíči.
-   Pokud potřebujete modul hardwarového zabezpečení nebo Centrální správa ve vaší hierarchii šifrovací klíče, zvažte použití Azure Key Vault se systémem SQL Server ve virtuálním počítači Azure.
-   Vždy šifrovaný (momentálně ve verzi preview) umožňuje transparentní šifrování pro aplikace a umožňuje klientům šifrovat citlivá data uvnitř klientské aplikace bez sdílení šifrovací klíče s databází SQL.

Auditování databáze SQL Azure umožňuje podnikům záznamů událostí přihlášení auditu Azure Storage. Auditování služby SQL Database je také integrované do služby Microsoft Power BI kvůli snadnějšímu procházení sestav a analýz.

 Databáze SQL Azure mohly být zabezpečeny úzce vyhovět nejvíce zákonných nebo požadavky na zabezpečení, mimo jiné včetně HIPAA, ISO 27001/27002 a PCI DSS úrovně 1. Aktuální seznam certifikace dodržování předpisů zabezpečení je k dispozici na [webu Microsoft Azure Trust Center](http://azure.microsoft.com/support/trust-center/services/).

Tento článek vás provede základy zabezpečení Microsoft databází SQL Azure pro Structured, tabulková a relační Data. Tento článek vám především pomůže začít s prostředky pro ochranu dat, řízením přístupu a proaktivním monitorováním.

V tomto článku Přehled zabezpečení databáze Azure se zaměřuje na tyto oblasti:

-   Ochrana dat
-   Řízení přístupu
-   Proaktivní monitorování
-   Centralizované zabezpečení správy
-   Azure Marketplace

## <a name="protect-data"></a>Ochrana dat

Databáze SQL zabezpečuje vašich dat tím, že poskytuje šifrování dat pomocí pohybu [Transport Layer Security](https://support.microsoft.com/kb/3135244)pro data na rest pomocí [transparentní šifrování dat](http://go.microsoft.com/fwlink/?LinkId=526242)a pro data v použití pomocí [ Funkce Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx).

V této části se věnuje:

-   Šifrování v provozu
-   Šifrování v klidovém stavu
-   Šifrování používá (klient)

Existují i jiné možnosti šifrování dat:

-   [Šifrování na úrovni buňky](https://msdn.microsoft.com/library/ms179331.aspx) umožňuje šifrovat určité sloupce nebo buňky dat pomocí různých šifrovacích klíčů.
-   Pokud potřebujete modul hardwarového zabezpečení nebo chcete centrálně spravovat hierarchii šifrovacích klíčů, měli byste uvažovat o [řešení Azure Key Vault s SQL Serverem na virtuálním počítači v Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).

### <a name="encryption-in-motion"></a>Šifrování v provozu

Častých problémů pro všechny aplikace, klient server je potřeba ochrany osobních údajů, protože data se přesouvají přes veřejný a privátní sítě. Pokud není šifrovat data přesouvání přes síť, je pravděpodobné, můžete zachytit a odcizení neoprávnění uživatelé. Při plánování práce s databázové služby, musíte zajistit, že data se šifrují mezi databáze klient a server i mezi databázové servery, které komunikují mezi sebou a se aplikace střední vrstvy.

Jedním z problémů při správě sítě je zabezpečení dat posílaných mezi aplikacemi přes nedůvěryhodné sítě. Můžete použít [TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) k ověření serverů a klientů a pak jeho pomocí šifrovat zprávy mezi ověřenými stranami.

V procesu ověřování TLS/SSL klient odešle zprávu serveru TLS/SSL a server odpoví informace, které potřebuje ke svému ověření. Klient a server provedou další výměnu klíčů relací a ověřovací dialog se ukončí. Po dokončení ověření může mezi serverem a klientem využitím klíčů symetrického šifrování, které se vytvoří během procesu ověřování začít komunikace zabezpečená protokolem SSL.

Všechna připojení ke službě Azure SQL Database vyžadují nepřetržité šifrování (SSL/TLS) příchozích a odchozích databázových dat. SQL Azure používá protokol TLS/SSL k ověření serverů a klientů a pak jeho pomocí šifrovat zprávy mezi ověřenými stranami. V připojovacím řetězci vaší aplikace, je nutné zadat parametry k šifrování připojení a nechcete důvěřovat certifikátu serveru (Pokud udělá se to pro vás zkopírujte připojovací řetězec mimo portál Azure), jinak hodnota připojení nebude ověřte Identita serveru a bude náchylné k útokům "man-in-the-middle". Pro ovladač technologie ADO.NET pro instanci tyto řetězce jsou parametry připojení šifrovat = True a TrustServerCertificate = False.

### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu
Může trvat několik opatření pro zvýšení zabezpečení databáze například návrhu zabezpečení systému, šifrování důvěrné prostředky a vytváření brány firewall kolem databázové servery. Ale ve scénáři, kde jsou odcizení fyzických médií (například jednotky nebo záložní pásky), škodlivý strany můžete jenom obnovit nebo připojit databázi a procházet data.

Jedno řešení je k šifrování citlivých dat v databázi a chrání klíčů, které se používají k šifrování dat pomocí certifikátu. To zabrání ostatním uživatelům bez klíče používat data, ale musí být plánované takovou ochranu.

Chcete-li tento problém vyřešit, SQL Server a Azure SQL podporovat [transparentní šifrování šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/securityrecryption/transparent-data-encryption-tde). Šifrování TDE šifruje SQL Server a databáze SQL Azure datových souborů, označuje jako šifrování dat v klidovém stavu.

Azure SQL Database transparentní šifrování dat pomáhá chránit před ohrožením škodlivých aktivit provedením v reálném čase šifrování a dešifrování databáze, přidružených záloh a soubory protokolu transakcí v klidovém stavu bez nutnosti změny aplikace.  

Šifrování TDE zašifruje úložiště celé databáze pomocí symetrický klíč s názvem šifrovací klíč databáze. V databázi SQL šifrovací klíč databáze je chráněn certifikát integrovaného serveru. Certifikát integrovaného serveru je jedinečný pro každý server databáze SQL.

Pokud je databáze v relace GeoDR, je chráněn jiný klíč na každém serveru. Pokud dvě databáze jsou připojeny ke stejnému serveru, budou sdílet stejný certifikát předdefinované. Microsoft automaticky otočí tyto certifikáty alespoň jednou za 90 dní. Obecný popis TDE, najdete v části [transparentní šifrování šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Šifrování používá (klient)

Většina úniky dat zahrnuje krádež důležitých dat, třeba čísla platebních karet nebo identifikovatelné osobní údaje. Databáze může být poklad troves citlivých informací. Mohou obsahovat osobní data zákazníků, důvěrné informace produktivní a duševní vlastnictví. Ztracené nebo odcizené data, zejména zákaznická data, může způsobit poškození značky, konkurenční nevýhodou a závažné pokutami – dokonce i probíhajících řízení.

![Funkce Always Encrypted](./media/azure-databse-security-overview/azure-database-fig1.png)

[Funkce Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) je funkce určená k ochraně citlivých dat, třeba čísla platebních karet nebo national identifikačními čísly (například USA čísel sociálního pojištění), uloženy v databázi Azure SQL Database nebo SQL Server. Vždy šifrovaný umožňuje klientům šifrovat citlivá data uvnitř klientské aplikace a nikdy odhalit šifrovací klíče pro databázový stroj (SQL Database nebo SQL Server).

Vždy šifrovaný zajišťuje oddělení mezi uživatelů, kteří vlastní data (a můžete ji zobrazit) a ty, kteří spravovat data (ale musí mít žádný přístup). Zajištěním místní správci databází cloudu operátory databáze nebo jiných vysokou úrovní oprávnění, ale neoprávnění uživatelé získat přístup šifrovaná data.

Kromě toho funkce Always Encrypted vytváří šifrování pro otevřenou aplikace. Podporou funkce Always Encrypted nainstalovaný ovladač v klientském počítači, aby mohli automaticky šifrování a dešifrování citlivých dat v aplikaci klienta. Ovladač šifruje data v citlivých sloupce před jejím odesláním k databázovému stroji a automaticky přepíše dotazy tak, aby se zachovají sémantiku k aplikaci. Podobně ovladač transparentně dešifruje data, uložená v sloupců šifrované databáze, obsažené ve výsledcích dotazů.

## <a name="access-control"></a>Řízení přístupu
Služba SQL Database zajišťuje zabezpečení řízením přístupu pomocí pravidel brány firewall omezujících připojení podle IP adresy, ověřovacích mechanismů vyžadujících po uživatelích prokázání identity a autorizačních mechanismů omezujících uživatelům přístup ke konkrétním akcím a datům.

### <a name="database-access"></a>Přístup k databázi

Ochrana dat začíná řízení přístupu k datům. Datového centra hostujícího vaše data spravuje fyzický přístup, když můžete nakonfigurovat bránu firewall pro správu zabezpečení síťové vrstvy. Také můžete řídit přístup nakonfigurováním přihlášení pro ověřování a definování oprávnění pro role serveru a databáze.

V této části se věnuje:

-   Brána firewall a pravidla brány firewall
-   Authentication
-   Autorizace

#### <a name="firewall-and-firewall-rules"></a>Brána firewall a pravidla brány firewall

Microsoft Azure SQL Database poskytuje relační databázovou službu pro aplikace Azure a další internetové aplikace. Brána firewall pomáhá chránit vaše data tím, že brání veškerému přístupu k vašemu databázovému serveru, dokud neurčíte, které počítače k tomu mají oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku. Další informace najdete v tématu [Přehled pravidel brány firewall služby Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) služba je k dispozici prostřednictvím portu TCP 1433. Pokud chcete z počítače získat přístup ke službě SQL Database, ověřte, že brána firewall vašeho klientského počítače umožňuje odchozí komunikaci TCP přes port TCP 1433. Pokud ho jiné aplikace nepotřebují, zablokujte na portu TCP 1433 příchozí připojení.

#### <a name="authentication"></a>Authentication

Ověřování ve službě SQL Database určuje, jakým způsobem prokážete svou identitu při připojování k databázi. SQL Database podporuje dva typy ověřování:

-   **Ověřování SQL:** účet jednotné přihlášení se vytvoří při vytvoření logické instance SQL s názvem účet odběratele databáze SQL. Tento účet se připojí pomocí [ověřování serveru SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview) (uživatelské jméno a heslo). Tento účet je správcem v logické instanci na serveru a ve všech uživatelských databázích připojených k této instanci. Oprávnění účtu předplatitele není možné omezit. Existovat může jenom jeden z těchto účtů.
-   **Azure Active Directory Authentication:** [ověřování Azure Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) mechanismus připojit k Microsoft Azure SQL Database a SQL Data Warehouse pomocí identit v Azure Active Directory ( Azure AD). To umožňuje centrálně spravovat identity uživatelů databáze.

![Authentication](./media/azure-databse-security-overview/azure-database-fig2.png)

 Výhody ověřování Azure Active Directory:
  - Nabízí alternativu k ověřování serveru SQL Server.
  - Také pomáhá zastavit šíření identit uživatelů mezi servery databáze & umožňuje otočení heslo na jednom místě.
  - Můžete spravovat oprávnění databáze pomocí externí skupiny (Azure Active Directory).
  - Ukládání hesel se může eliminovat tím, že umožňuje integrované ověřování systému Windows a jiných forem ověřování podporovaných službou Azure Active Directory.

#### <a name="authorization"></a>Autorizace
[Autorizace](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins) odkazuje na co může uživatel provádět v rámci Azure SQL Database, a tím je řízeno váš uživatelský účet databáze [členství v rolích](https://msdn.microsoft.com/library/ms189121) a [oprávnění na úrovni objektů](https://msdn.microsoft.com/library/ms191291.aspx). Autorizace je proces pro určení toho, že zabezpečitelné prostředky, ke kterým přístup objekt zabezpečení a operací, které jsou povoleny pro tyto prostředky.

### <a name="application-access"></a>Přístup k aplikaci

V této části se věnuje:

-   Dynamické maskování dat
-   Zabezpečení na úrovni řádku

#### <a name="dynamic-data-masking"></a>Dynamické maskování dat
Zástupce služby v telefonickém centru může identifikovat volající podle několik číslic jejich číslo sociálního pojištění nebo číslo platební karty, ale tyto datové položky by neměly být vystaveny plně služeb zákazníkům.

Všechny masky ale poslední čtyři číslice čísla sociálního zabezpečení nebo číslo platební karty ve výsledku sadu jakýkoli dotaz, může být definováno pravidlo maskování.

![Dynamické maskování dat](./media/azure-databse-security-overview/azure-database-fig3.png)

Například může být definováno masku příslušná data k ochraně dat identifikovatelné osobní údaje (PII), tak, aby vývojář může dotazovat provozní prostředí pro účely odstraňování potíží, aniž bychom při tom porušili předpisy.

[SQL databáze dynamického maskování dat](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) omezuje zranitelnost citlivá data pomocí maskování uživatelům bez oprávnění. Dynamická data maskování je podporována pro Azure SQL Database verze 12.

[Dynamická data maskování](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) pomáhá zabránit neoprávněnému přístupu k citlivým datům tím, že vám určit, kolik citlivých dat na nich s minimálním dopadem na aplikační vrstvu. Je to funkce zabezpečení založená na zásadách, která skrývá citlivá data v sadě výsledků dotazu nad určenými poli databáze, zatímco data v databázi se nemění.


> [!Note]
> Dynamická data maskování dá nakonfigurovat pomocí Správce Azure databáze, správce serveru nebo úředník role zabezpečení.

#### <a name="row-level-security"></a>Zabezpečení na úrovni řádků
Další běžné požadavky zabezpečení pro víceklientské databáze je [zabezpečení na úrovni řádků](https://msdn.microsoft.com/library/dn765131.aspx). Tato funkce umožňuje řídit přístup na řádky v tabulce databáze na základě charakteristik uživatele provádění dotazu (například skupinu členství nebo provádění kontextu).

![-Zabezpečení na úrovni řádků](./media/azure-databse-security-overview/azure-database-fig4.png)

Logika omezení přístupu je umístěn v databázové vrstvy spíše než tokeny z dat v jiném aplikační vrstvě. Databázový systém platí omezení přístupu pokaždé, když dojde k pokusu o tento přístup k datům z libovolné úrovně. Díky systému zabezpečení spolehlivější a robustní snížením oblasti plochy v systému zabezpečení.

Zabezpečení na úrovni řádků zavádí řízení přístupu predikátem. Nabízí flexibilní, centralizované, na základě predikát hodnocení, které můžete vzít v úvahu metadata nebo jiných kritérií, které správce určí podle potřeby. Predikát se používá jako kritérium k určení, zda uživatel má odpovídající přístup k datům založit na atributech uživatelů. Řízení přístupu na základě popisku můžete implementovat pomocí řízení přístupu na základě predikátu.

## <a name="proactive-monitoring"></a>Proaktivní monitorování
Databáze SQL zabezpečuje vašich dat tím, že poskytuje **auditování** a **detekce hrozby** možnosti.

### <a name="auditing"></a>Auditování
Auditování databáze SQL se zvyšuje možnost proniknout do události a změny, které se vyskytují v databázi, včetně aktualizací a dotazy na data.

[Auditování databáze SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) sleduje události databáze a zapisuje je do auditu protokolu ve vašem účtu úložiště Azure. Auditování pomáhá zajistit dodržování předpisů, porozumět databázové aktivitě a získat přehled o nesrovnalostech a anomáliích, které můžou značit problémy obchodního charakteru nebo vzbuzovat podezření na narušení zabezpečení. Auditování umožňuje a zjednodušuje dodržování standardů dodržování předpisů, ale není zajistit dodržování předpisů.

Auditování databáze SQL umožňuje:

-   **Zachovat** monitorovat vybrané události. Můžete definovat kategorie akce databáze k auditování.
-   **Sestava** v databázové aktivitě. Abyste mohli rychle začít s aktivity a události vytváření sestav můžete předem nakonfigurované sestavy a řídicí panel.
-   **Analýza** sestavy. Můžete najít podezřelé události, neobvyklé aktivity a trendy.

Existují dvě metody auditování:

-   **Auditování objektů blob** -protokoly zapisují do úložiště objektů Blob Azure. Toto je novější auditování metoda, která poskytuje vyšší výkon a podporuje auditování vyšší na úrovni objektů členitosti, větší finanční efektivita.
-   **Auditování tabulka** -protokoly zapisují na Azure Table Storage.

### <a name="threat-detection"></a>Detekce hrozeb
[Detekce hrozeb Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) detekuje podezřelé aktivity, které indikují potenciální ohrožení. Detekce hrozeb umožňuje reakce na podezřelé události v databázi, jako je například vložení SQL, kdy k nim dojde. Poskytuje výstrahy a umožňuje použití audity Azure SQL Database a prozkoumejte podezřelé události.

![Detekce hrozeb](./media/azure-databse-security-overview/azure-database-fig5.jpg)

Například Injektáž SQL je jedním z běžné problémy zabezpečení webových aplikací na Internetu, slouží k útokům na základě dat aplikace. Útočníci využít výhod ohrožení zabezpečení aplikace se zlými úmysly příkazy SQL, do pole pro zadání aplikací, vložit před nedodržením nebo upravovat data v databázi.

Osoby zabezpečení nebo jiné určený správce můžete získat okamžité odeslání oznámení o aktivitách podezřelé databáze, kdy k nim dojde. Jednotlivá oznámení najdete podrobnosti podezřelé aktivity a doporučuje postup dál prozkoumat a zmírnit riziko.        

## <a name="centralized-security-management"></a>Centralizované zabezpečení správy

[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

[Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-database) pomáhá zabezpečit data v databázi SQL tím, že poskytuje přehled o zabezpečení serverů a databází. Security Center můžete:

-   Definujte zásady pro šifrování SQL Database a auditování.
-   Sledování zabezpečení prostředků databáze SQL ve vašich předplatných.
-   Rychle identifikovat a opravit problémy se zabezpečením.
-   Integrovat výstrahy z [detekce hrozeb Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).
-   Security Center podporuje přístup na základě rolí.

## <a name="azure-marketplace"></a>Azure Marketplace

Azure Marketplace je internetové tržiště aplikací a služeb, kde start-upy a nezávislí dodavatelé softwaru můžou nabízet svoje řešením zákazníkům Azure z celého světa.
Azure Marketplace spojuje ekosystémy partnerů Microsoft Azure do jediné unifikované platformy, abychom svým zákazníkům a partnerům mohli poskytovat lepší služby. Klikněte na tlačítko [sem](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) na první pohled databáze zabezpečovací produkty k dispozici v Azure Marketplace.

## <a name="next-steps"></a>Další postup

- Další informace o [zabezpečení vaší databázi SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial).
- Další informace o [služby Azure Security Center a Azure SQL Database](https://docs.microsoft.com/azure/security-center/security-center-sql-database).
- Další informace o detekce hrozeb, najdete v části [detekce hrozeb databáze SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).
- Další informace najdete v tématu [SQL zlepšit výkon databáze](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial). 

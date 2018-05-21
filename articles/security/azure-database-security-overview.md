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
ms.openlocfilehash: 44abf7a4fc24893146179b34d3357f54450decab
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="azure-database-security-overview"></a>Přehled zabezpečení databáze Azure

Zabezpečení je velmi důležité pro správu databáze a byla vždy prioritu pro Azure SQL Database. Azure SQL Database podporuje připojení zabezpečení se pravidla brány firewall a šifrování připojení. Podporuje ověřování pomocí uživatelského jména a hesla a ověřování služby Azure Active Directory (Azure AD), které používá identity spravované službou Azure Active Directory. Autorizace používá řízení přístupu na základě rolí.

Azure SQL Database podporuje šifrování provedením v reálném čase šifrování a dešifrování databáze, přidružených záloh a souborů protokolů transakci bez nutnosti změny aplikace.

Společnost Microsoft poskytuje další způsoby, jak šifrování podnikových dat:

-   Šifrování na úrovni buněk je k dispozici k šifrování i buněk dat nebo konkrétní sloupce s různými šifrovacími klíči.
-   Pokud potřebujete modul hardwarového zabezpečení nebo Centrální správa ve vaší hierarchii šifrovací klíče, zvažte použití Azure Key Vault s SQL serverem v Azure virtuální počítač (VM).
-   Vždy šifrovaný (momentálně ve verzi preview) umožňuje šifrování transparentní pro aplikace. Také to umožňuje klientům šifrovat citlivá data uvnitř klientské aplikace bez sdílení šifrovací klíče s databází SQL.

Auditování databáze SQL Azure umožňuje podnikům záznam, který události auditu protokolu ve službě Azure Storage. Auditování služby SQL Database je také integrované do služby Microsoft Power BI kvůli snadnějšímu procházení sestav a analýz.

Databáze Azure SQL mohly být zabezpečeny úzce vyhovět nejvíce zákonných nebo požadavky na zabezpečení, včetně HIPAA, ISO 27001/27002 a PCI DSS úrovně 1. Aktuální seznam certifikace dodržování předpisů zabezpečení je k dispozici na [webu Microsoft Azure Trust Center](http://azure.microsoft.com/support/trust-center/services/).

Tento článek vás provede základy zabezpečení Microsoft Azure SQL databáze pro strukturovaných, tabulkový a relační data. Tento článek vám především pomůže začít s prostředky pro ochranu dat, řízením přístupu a proaktivním monitorováním.

## <a name="protection-of-data"></a>Ochrana dat

Databáze SQL pomáhá zabezpečení vašich dat tím, že poskytuje šifrování:

- Pro data v pohybu prostřednictvím [zabezpečení TLS (Transport Layer)](https://support.microsoft.com/kb/3135244).
- Pro data v klidovém stavu prostřednictvím [transparentní šifrování dat](http://go.microsoft.com/fwlink/?LinkId=526242).
- Pro data používá prostřednictvím [vždy šifrována](https://msdn.microsoft.com/library/mt163865.aspx).

Existují i jiné možnosti šifrování dat:

-   [Šifrování na úrovni buňky](https://msdn.microsoft.com/library/ms179331.aspx) umožňuje šifrovat určité sloupce nebo buňky dat pomocí různých šifrovacích klíčů.
-   [Azure Key Vault se systémem SQL Server ve virtuálním počítači Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx), pokud potřebujete modul hardwarového zabezpečení nebo Centrální správa ve vaší hierarchii šifrovací klíče.

### <a name="encryption-in-motion"></a>Šifrování v provozu

Častých problémů pro všechny aplikace, klient server je potřeba ochrany osobních údajů, protože data se přesouvají přes veřejný a privátní sítě. Pokud není šifrovat data přesouvání přes síť, existuje pravděpodobnost, že můžete zachytit a odcizení neoprávnění uživatelé. V případě, že pracujete s databázové služby, ujistěte se, že data se šifrují mezi databáze klientem a serverem. Také se ujistěte, že data se šifrují mezi databázové servery, které komunikují mezi sebou a se aplikace střední vrstvy.

Jedním z problémů při správě sítě je zabezpečení dat posílaných mezi aplikacemi přes nedůvěryhodné sítě. Můžete použít [TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) k ověření serverů a klientů a pak jeho pomocí šifrovat zprávy mezi ověřenými stranami.

V procesu ověřování TLS/SSL klient odešle zprávu do serveru TLS/SSL. Server odpoví informace, které server potřebuje ke svému ověření. Klient a server provedou další výměnu klíčů relací a ověřovací dialog se ukončí. Po dokončení ověření může začít komunikace zabezpečená protokolem SSL mezi serverem a klientem prostřednictvím klíčů symetrického šifrování, které se vytvoří během procesu ověřování.

Všechna připojení k databázi SQL Azure vyžadovat šifrování (TLS/SSL) na všechny časy při dat je "při přenosu" a z databáze. Databáze SQL používá protokol TLS/SSL k ověření serverů a klientů a pak jeho pomocí šifrovat zprávy mezi ověřenými stranami. 

V připojovacím řetězci vaší aplikace je nutné zadat parametry k šifrování připojení a nechcete důvěřovat certifikátu serveru. (Pokud udělá se to pro vás zkopírujte připojovací řetězec z portálu Azure.) Připojení, jinak nebude ověřit identitu serveru a bude náchylné k útokům "man-in-the-middle". Pro technologii ADO.NET ovladač, například tyto řetězce jsou parametry připojení `Encrypt=True` a `TrustServerCertificate=False`.

### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu
Může trvat několik opatření pro zvýšení zabezpečení databáze. Například návrhu zabezpečení systému, šifrovat důvěrné prostředky a vytvoření brány firewall kolem databázové servery. Ale ve scénáři, kde jsou odcizení fyzických médií (například jednotky nebo záložní pásky), škodlivý strany můžete jenom obnovit nebo připojit databázi a procházet data.

Jedno řešení je pro šifrování citlivých dat v databázi a ochranu klíčů, které se používají k šifrování dat pomocí certifikátu. Toto řešení osobě bez klíče bránit v použití data, ale musí být plánované takovou ochranu.

Chcete-li vyřešit tento problém, SQL Server a SQL Database podporu [transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/securityrecryption/transparent-data-encryption-tde). Transparentní šifrování dat šifruje systému SQL Server a SQL Database datových souborů, označuje jako šifrování dat v klidovém stavu.

Transparentní šifrování dat pomáhá chránit před ohrožením škodlivých aktivit. Provede v reálném čase šifrování a dešifrování databáze, přidružených záloh a souborů protokolů transakci bez nutnosti změny aplikace.  

Transparentní šifrování dat pomocí symetrický klíč s názvem šifrovací klíč databáze zašifruje úložiště celé databáze. V databázi SQL šifrovací klíč databáze je chráněn certifikát integrovaného serveru. Certifikát integrovaného serveru je jedinečný pro každý server databáze SQL.

Pokud je databáze ve vztahu Geo-zotavení po Havárii, je chráněn jiný klíč na každém serveru. Pokud dvě databáze jsou připojeny ke stejnému serveru, budou sdílet stejný certifikát předdefinované. Microsoft automaticky otočí tyto certifikáty alespoň jednou za 90 dní. 

Další informace najdete v tématu [transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Šifrování používá (klient)

Většina úniky dat zahrnuje krádež důležitých dat, třeba čísla platebních karet nebo identifikovatelné osobní údaje. Databáze může být poklad troves citlivých informací. Mohou obsahovat osobní údaje (například national identifikační čísla), důvěrné informace produktivní a duševní vlastnictví zákazníků. Ztracené nebo odcizené data, zejména zákaznická data, může způsobit poškození značky, konkurenční nevýhodou a závažné pokutami – i probíhajících řízení.

![Funkce Always Encrypted zobrazené jako zámek a klíč](./media/azure-databse-security-overview/azure-database-fig1.png)

[Funkce Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) je funkce určená k ochraně citlivých dat uložených v databáze Azure SQL Database nebo SQL Server. Vždy šifrovaný umožňuje klientům šifrovat citlivá data uvnitř klientské aplikace a nikdy odhalit šifrovací klíče pro databázový stroj (SQL Database nebo SQL Server).

Vždy šifrovaný zajišťuje oddělení mezi uživateli, kteří vlastní data (a můžete ji zobrazit) a uživatelům, kteří spravovat data (by měl mít žádný přístup). Pomáhá zajistit, aby místní databáze správci, operátoři cloudu databáze nebo jiných vysokým oprávněním, ale neoprávnění uživatelé nelze přístupu k zašifrovaným datům.

Kromě toho funkce Always Encrypted vytváří šifrování pro otevřenou aplikace. Podporou funkce Always Encrypted ovladače je nainstalována na klientském počítači, takže můžete automaticky šifrování a dešifrování citlivých dat v aplikaci klienta. Ovladač šifruje data v citlivých sloupce před jejím odesláním k databázovému stroji. Ovladač automaticky přepíše dotazy tak, aby se zachovají sémantiku k aplikaci. Podobně ovladač transparentně dešifruje data, uložená v sloupců šifrované databáze, obsažené ve výsledcích dotazů.

## <a name="access-control"></a>Řízení přístupu
Databáze SQL pro zabezpečení, řídí přístup pomocí:

- Pravidla brány firewall, které omezují připojení pomocí IP adresy.
- Mechanismy ověřování, které vyžaduje, aby uživatel k prokázání své identity.
- Mechanismy ověřování, které omezení uživatelů na konkrétní akce a data.

### <a name="database-access"></a>Přístup k databázi

Ochrana dat začíná řízení přístupu k datům. Datacenter, hostující vaše data spravuje fyzický přístup. Můžete nakonfigurovat bránu firewall pro správu zabezpečení síťové vrstvy. Také můžete řídit přístup nakonfigurováním přihlášení pro ověřování a definování oprávnění pro role serveru a databáze.

#### <a name="firewall-and-firewall-rules"></a>Brána firewall a pravidla brány firewall

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) poskytuje služba relační databáze pro Azure a jiné internetové aplikace. Brána firewall pomáhá chránit vaše data tím, že brání veškerému přístupu k vašemu databázovému serveru, dokud neurčíte, které počítače k tomu mají oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku. Další informace najdete v tématu [Přehled pravidel brány firewall služby Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

Služba Azure SQL Database je k dispozici pouze prostřednictvím portu TCP 1433. Pro přístup k databázi SQL z vašeho počítače, ujistěte se, že klientský počítač brány firewall umožňuje odchozí komunikaci TCP na portu TCP 1433. Pokud příchozí připojení nejsou potřebné pro jiné aplikace, můžete je zablokujte na portu TCP 1433.

#### <a name="authentication"></a>Authentication

Ověřování se týká způsobu, jakým prokážete svou identitu při připojování k databázi. SQL Database podporuje dva typy ověřování:

-   **Ověřování systému SQL Server**: účet jednotné přihlášení se vytvoří při vytvoření logické instance SQL s názvem účet odběratele databáze SQL. Tento účet se připojí pomocí [ověřování serveru SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview) (uživatelské jméno a heslo). Tento účet je správcem v logické instanci na serveru a ve všech uživatelských databázích připojených k této instanci. Nedá se omezit oprávnění účtu odběratele. Existovat může jenom jeden z těchto účtů.
-   **Ověřování Azure Active Directory**: [ověřování Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) mechanismus, připojení k databázi SQL Azure a Azure SQL Data Warehouse pomocí identit ve službě Azure AD. Můžete ho centrálně spravovat identity uživatelů databáze.

![Ověřování Azure AD s databází SQL](./media/azure-databse-security-overview/azure-database-fig2.png)

 Výhody ověřování Azure AD:
  - Nabízí alternativu k ověřování serveru SQL Server.
  - Ho pomáhá zastavit šíření identit uživatelů mezi servery databáze a umožňuje otočení heslo na jednom místě.
  - Pomocí skupin externí (Azure AD) můžete spravovat oprávnění pro databázi.
  - Ukládání hesel se může eliminovat tím, že umožňuje integrované ověřování systému Windows a jiných forem ověřování, které podporuje Azure AD.

#### <a name="authorization"></a>Autorizace
[Autorizace](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins) odkazuje na co může uživatel provádět v rámci Azure SQL database. Je ovládáno pomocí uživatelského účtu databáze [členství v rolích](https://msdn.microsoft.com/library/ms189121) a [oprávnění na úrovni objektů](https://msdn.microsoft.com/library/ms191291.aspx). Autorizace je proces pro určení toho, že zabezpečitelné prostředky, ke kterým přístup objekt zabezpečení a operací, které jsou povoleny pro tyto prostředky.

### <a name="application-access"></a>Přístup k aplikaci

#### <a name="dynamic-data-masking"></a>Dynamické maskování dat
Zástupce služby v telefonickém centru může identifikovat volající několik číslic jejich číslo sociálního pojištění nebo číslo platební karty. Ale tyto datové položky by neměly být vystaveny plně služeb zákazníkům.

Můžete definovat zakrývá všechny kromě poslední čtyři číslice čísla sociálního zabezpečení nebo číslo platební karty v sadě výsledků dotazu žádné pravidlo maskování.

![Maskování na řadě posílají mezi SQL databáze a obchodní aplikace](./media/azure-databse-security-overview/azure-database-fig3.png)

Například může být definováno masku příslušná data k ochraně identifikovatelné osobní údaje. Vývojář může potom budete dotazovat provozní prostředí pro účely odstraňování potíží, aniž bychom při tom porušili předpisy.

[Dynamické maskování dat služby SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) omezuje riziko ohrožení citlivých dat pomocí jejich maskování pro neoprávněné uživatele. Dynamická data maskování je podporována pro Azure SQL Database verze 12.

[Dynamická data maskování](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) pomáhá zabránit neoprávněnému přístupu k citlivým datům tím, že vám určit, kolik citlivých dat na nich s minimálním dopadem na aplikační vrstvu. Je to funkce zabezpečení založená na zásadách, která skrývá citlivá data v sadě výsledků dotazu nad určenými poli databáze, zatímco data v databázi se nemění.


> [!Note]
> Dynamická data maskování dá nakonfigurovat pomocí Správce Azure databáze, správce serveru nebo úředník role zabezpečení.

#### <a name="row-level-security"></a>Zabezpečení na úrovni řádku
Další běžné požadavky zabezpečení pro víceklientské databáze je [zabezpečení na úrovni řádků](https://msdn.microsoft.com/library/dn765131.aspx). Tato funkce slouží k řízení přístupu na řádky v tabulce databáze na základě charakteristik uživatele, který spouští dotaz. (Příklad vlastnosti jsou skupiny členství a provádění kontextu).

![Umožňuje uživatelům přístup řádky v tabulce prostřednictvím klienta aplikace zabezpečení na úrovni řádků](./media/azure-databse-security-overview/azure-database-fig4.png)

Logika omezení přístupu je umístěn v databázové vrstvy spíše než tokeny z dat v jiném aplikační vrstvě. Databázový systém platí omezení přístupu pokaždé, když dojde k pokusu o tento přístup k datům z libovolné úrovně. Díky systému zabezpečení spolehlivější a robustní snížením oblasti plochy v systému zabezpečení.

Zabezpečení na úrovni řádků zavádí řízení přístupu na základě predikátu. Nabízí flexibilní, centralizované hodnocení, které můžete vzít v úvahu metadata nebo jiných kritérií, které správce určí podle potřeby. Predikát se používá jako kritérium k určení, zda uživatel má odpovídající přístup k datům založit na atributech uživatelů. Řízení přístupu na základě popisku můžete implementovat pomocí řízení přístupu na základě predikátu.

## <a name="proactive-monitoring"></a>Proaktivní monitorování
Databáze SQL pomáhá zabezpečení vašich dat tím, že poskytuje *auditování* a *detekce hrozby* možnosti.

### <a name="auditing"></a>Auditování
[Auditování databáze SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) zvyšuje možnost proniknout do události a změny, které se vyskytují v databázi. Příklady jsou aktualizace a dotazy na data.

Auditování databáze SQL sleduje události databáze a zápisu, které mají auditu přihlášení účtu úložiště Azure. Auditování vám může pomoct zajistit dodržování předpisů, porozumět databázové aktivitě a proniknout do nesrovnalostí a anomálií, které může být známkou problémů obchodního charakteru nebo by mohly vzbuzovat podezření narušení zabezpečení. Auditování umožňuje a zjednodušuje dodržování standardů dodržování předpisů, ale není zajistit dodržování předpisů.

Můžete použít SQL Database, aby bylo auditování:

-   **Zachovat** monitorovat vybrané události. Můžete definovat kategorie akce databáze k auditování.
-   **Sestava** v databázové aktivitě. Abyste mohli rychle začít s aktivity a události vytváření sestav můžete předem nakonfigurované sestavy a řídicí panel.
-   **Analýza** sestavy. Můžete najít podezřelé události, neobvyklé aktivity a trendy.

Existují dvě metody auditování:

-   **Auditování objektů blob**: protokoly se zapisují do úložiště objektů Blob v Azure. Toto je novější auditování metoda. Poskytuje vyšší výkon, podporuje auditování vyšší na úrovni objektů členitosti a větší finanční efektivita.
-   **Auditování tabulka**: protokoly zapisují na Azure Table storage.

### <a name="threat-detection"></a>Detekce hrozeb
[Detekce hrozeb Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) detekuje podezřelé aktivity, které indikují potenciální ohrožení. Detekce hrozeb reagovat na podezřelé události v databázi, jako je například vložení SQL, můžete použít, když k nim dojde. Poskytuje výstrahy a umožňuje použití auditování databáze SQL Azure a prozkoumejte podezřelé události.

![Detekce hrozeb pro SQL Database a webové aplikace se útočník a zevnitř](./media/azure-databse-security-overview/azure-database-fig5.jpg)

Například Injektáž SQL je jedním z běžné problémy se zabezpečením pro webové aplikace. Použije se k útoku na základě dat aplikace. Útočníci využít výhod ohrožení zabezpečení aplikace se zlými úmysly příkazy SQL, do pole pro zadání aplikací, vložit před nedodržením nebo upravovat data v databázi.

Osoby zabezpečení nebo jiné určený správce můžete získat okamžité odeslání oznámení o aktivitách podezřelé databáze, kdy k nim dojde. Jednotlivá oznámení najdete podrobnosti podezřelé aktivity a doporučuje postup dál prozkoumat a zmírnit riziko.        

## <a name="centralized-security-management"></a>Centralizované zabezpečení správy

[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure. Pomáhá zjišťovat hrozby, kterých byste jinak nevšimli, a funguje s řadou řešení zabezpečení.

[Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-database) pomáhá zabezpečit data v databázi SQL tím, že poskytuje přehled o zabezpečení serverů a databází. Security Center můžete:

-   Definujte zásady pro šifrování SQL Database a auditování.
-   Sledování zabezpečení prostředků databáze SQL ve vašich předplatných.
-   Rychle identifikovat a opravit problémy se zabezpečením.
-   Integrovat výstrahy z [detekce hrozeb Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).

Security Center podporuje přístup na základě rolí.

## <a name="azure-marketplace"></a>Azure Marketplace

Azure Marketplace je internetové tržiště aplikací a služeb, kde start-upy a nezávislí dodavatelé softwaru můžou nabízet svoje řešením zákazníkům Azure z celého světa.
Azure Marketplace spojuje do jednotnou platformu pro lepší používat zákazníci a partneři ekosystémů partnera Microsoft Azure. Můžete [spustit hledání](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) Chcete-li zobrazit dostupné databáze zabezpečovací produkty v Azure Marketplace.

## <a name="next-steps"></a>Další postup

- [Zabezpečení databáze Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial)
- [Služba Azure Security Center a Azure SQL Database](https://docs.microsoft.com/azure/security-center/security-center-sql-database)
- [Detekce hrozeb databáze SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Zlepšení výkonu databáze SQL](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial)

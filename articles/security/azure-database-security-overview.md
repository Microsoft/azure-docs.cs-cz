---
title: Přehled zabezpečení služby Azure database | Dokumentace Microsoftu
description: Tento článek obsahuje přehled služby Azure database funkce zabezpečení.
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
ms.date: 09/20/2018
ms.author: TomSh
ms.openlocfilehash: 460ef8a3d4436f240793025cbec874c624a2a6f4
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47039016"
---
# <a name="azure-database-security-overview"></a>Přehled zabezpečení služby Azure database

Zabezpečení je velmi důležité pro správu databází a byl vždy prioritu pro službu Azure SQL Database. Azure SQL Database podporuje zabezpečení připojení pomocí pravidel brány firewall a šifrování připojení. Podporuje ověřování pomocí uživatelského jména a hesla a ověřování Azure Active Directory (Azure AD), které používá identity spravované v Azure Active Directory. Autorizace používá řízení přístupu na základě rolí.

Azure SQL Database podporuje šifrování pomocí provádí v reálném čase šifrování a dešifrování databáze, přidružené zálohy a soubory protokolů transakcí v klidovém stavu bez nutnosti změny aplikace.

Společnost Microsoft poskytuje další způsoby, jak šifrování podnikových dat:

-   Šifrování na úrovni buňky je možné šifrovat určité sloupce nebo buňky dat pomocí různých šifrovacích klíčů.
-   Pokud potřebujete modulu hardwarového zabezpečení nebo chcete centrálně spravovat hierarchii šifrovacích klíčů, zvažte použití služby Azure Key Vault s SQL serverem v Azure virtuální počítač (VM).
-   Always Encrypted (aktuálně ve verzi preview) činí šifrování přehlednějším pro aplikace. Také umožňuje klientům šifrovat citlivá data v klientských aplikacích bez sdílení šifrovacích klíčů se službou SQL Database.

Auditování Azure SQL Database umožňuje podnikům záznam událostí auditování protokolu ve službě Azure Storage. Auditování služby SQL Database je také integrované do služby Microsoft Power BI kvůli snadnějšímu procházení sestav a analýz.

Azure SQL Database je možné svázat úzce splňovat nejvíce zákonné nebo požadavky na zabezpečení, včetně HIPAA, ISO 27001/27002 a PCI DSS úrovně 1. Aktuální seznam certifikací dodržování předpisů zabezpečení je k dispozici na [webu Microsoft Azure Trust Center](http://azure.microsoft.com/support/trust-center/services/).

Tento článek vás provede základy zabezpečení databáze Microsoft Azure SQL pro strukturovaná, tabulková a relační data. Tento článek vám především pomůže začít s prostředky pro ochranu dat, řízením přístupu a proaktivním monitorováním.

## <a name="protection-of-data"></a>Ochrana dat

SQL Database pomáhá je zabezpečit vaše data zajištěním šifrování:

- Pro data přenášená prostřednictvím [zabezpečení TLS (Transport Layer)](https://support.microsoft.com/kb/3135244).
- Pro data v klidovém stavu pomocí [transparentní šifrování dat](http://go.microsoft.com/fwlink/?LinkId=526242).
- Pro data použitá prostřednictvím [s funkcí Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx).

Existují i jiné možnosti šifrování dat:

-   [Šifrování na úrovni buňky](https://msdn.microsoft.com/library/ms179331.aspx) umožňuje šifrovat určité sloupce nebo buňky dat pomocí různých šifrovacích klíčů.
-   [Služba Azure Key Vault s SQL serverem na Virtuálním počítači Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx), pokud potřebujete modulu hardwarového zabezpečení nebo chcete centrálně spravovat hierarchii šifrovacích klíčů.

### <a name="encryption-in-motion"></a>Šifrování přenášených

Běžný problém pro všechny aplikace klient/server je nutné pro ochranu osobních údajů, protože data se přesouvají přes veřejný a privátní sítě. Pokud není zašifrovaná data přesouvaná přes síť, může se stát, že můžete zachytit a vám ho někdo ukradne neoprávnění uživatelé. Pokud pracujete s databázové služby, ujistěte se, že se šifrují data mezi databáze klientem a serverem. Také se ujistěte, že se šifrují data mezi databázové servery, které komunikovat mezi sebou i s aplikacemi střední vrstvy.

Jeden problém při správě sítě je zabezpečení dat posílaných mezi aplikacemi přes nedůvěryhodné sítě. Můžete použít [TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) k ověření serverů a klientů a pak jeho pomocí šifrovat zprávy mezi ověřenými stranami.

V procesu ověřování odešle klient TLS/SSL zprávu serveru TLS/SSL. Server odpoví informacemi, server potřebuje ke svému ověření. Klient a server provedou další výměnu klíčů relací a ověřovací dialog se ukončí. Po dokončení ověření může začít zabezpečené protokolem SSL komunikace mezi serverem a klientem pomocí klíčů symetrického šifrování, které jsou vytvořeny během procesu ověřování.

Všechna připojení ke službě Azure SQL Database vyžadují šifrování (TLS/SSL) na celou dobu dat je "při přenosu" do a z databáze. SQL Database používá protokol TLS/SSL k ověření serverů a klientů a pak jeho pomocí šifrovat zprávy mezi ověřenými stranami. 

V připojovacím řetězci aplikace musíte zadat parametry, které šifrují připojení a ne do důvěřovat certifikátu serveru. (Pokud se to pro vás zkopírujte připojovací řetězec z portálu Azure portal.) Jinak připojení neověří identitu serveru a může být napadeno útočníky "man-in-the-middle" útoky. Pro ovladač ADO.NET pro instanci parametry připojovacího řetězce jsou `Encrypt=True` a `TrustServerCertificate=False`.

### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu
Může trvat několik opatření pro zvýšení zabezpečení databáze. Například návrh zabezpečení systému, šifrovat důvěrné prostředky a vytvářet brány firewall kolem databázové servery. Ale ve scénáři, kde jsou odcizení fyzická média (například jednotky nebo záložní pásky), škodlivý stran stačí obnovit nebo připojit databázi a procházet data.

Jedním řešením je šifrovat citlivá data v databázi a ochranu klíčů, které slouží k šifrování dat pomocí certifikátu. Toto řešení zabrání ostatním uživatelům bez klíčů používat data, ale tento typ ochrany je třeba se připravit.

Chcete-li vyřešit tento problém, SQL Server a SQL Database podporu [transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current&viewFallbackFrom=sql-server-2017). Transparentní šifrování dat šifruje systému SQL Server a SQL Database datových souborů, označované jako neaktivní uložená data šifrování.

Transparentní šifrování dat pomáhá chránit před hrozbou škodlivých činností. Provede v reálném čase šifrování a dešifrování databáze, přidružené zálohy a soubory protokolů transakcí v klidovém stavu bez nutnosti změny aplikace.  

Transparentní šifrování dat šifruje úložiště celou databázi pomocí symetrický klíč s názvem šifrovací klíč databáze. Ve službě SQL Database šifrovací klíč databáze je chráněno certifikátem integrovaného serveru. Certifikát integrovaného serveru je jedinečný pro každý server SQL Database.

Pokud je databáze v relaci Geo-DR, je chráněn jiným klíčem na každém serveru. Pokud dvě databáze jsou připojené ke stejnému serveru, sdílejí stejný certifikát integrované. Microsoft automaticky otočí tyto certifikáty nejméně každých 90 dní. 

Další informace najdete v tématu [transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Šifrování používá (klient)

Většina úniky dat zahrnují krádeže důležitých dat, třeba čísla platebních karet nebo identifikovatelné osobní údaje. Databáze může být poklad troves citlivé informace. Mohou obsahovat osobní údaje (například národního identifikačního čísla), důvěrné informace konkurenceschopnost a duševního vlastnictví zákazníků. Ztracené nebo odcizené data, zejména zákaznická data, může způsobit poškození značky, konkurenčním boji a vážné pokuty – dokonce i spory.

![Funkci Always Encrypted znázorněný jako zámek a klíč](./media/azure-databse-security-overview/azure-database-fig1.png)

[Funkce Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) je funkce, navržená k ochraně citlivá data uložená v databázích Azure SQL Database nebo SQL Server. Funkce Always Encrypted umožňuje klientům šifrovat citlivá data v klientských aplikacích a nikdy odhalit šifrovací klíče pro databázový stroj (SQL Database nebo SQL Server).

Vždy šifrovaný zajišťuje oddělení mezi lidmi, kdo data vlastní (a mohou je zobrazovat) a uživatelů, kteří spravovat data (ale nemají žádný přístup). Pomáhá zajistit, aby místní databáze správci, cloudové databáze operátory nebo jiné vysokými ale neoprávněnými uživateli nelze přístupu k zašifrovaným datům.

Kromě toho funkce Always Encrypted činí šifrování přehlednějším pro aplikace. Ovladač podporou funkce Always Encrypted je nainstalována na klientském počítači, takže můžete automaticky šifrovat a dešifrovat důvěrné osobní údaje v klientské aplikaci. Ovladač šifruje data v citlivých sloupce před jejím odesláním k databázovému stroji. Ovladač automaticky přepíše dotazy tak, aby se zachovají sémantiku pro aplikace. Podobně ovladač transparentně dešifruje data uložená v šifrovaného databázového sloupce, obsažené ve výsledcích dotazu.

## <a name="access-control"></a>Řízení přístupu
SQL Database zajišťuje zabezpečení řídí přístup s použitím:

- Pravidla brány firewall, které omezují připojení podle IP adresy.
- Ověřovací mechanismy, které vyžadují, aby uživatelé k prokázání své identity.
- Autorizačních mechanismů, které omezují uživatelů ke konkrétním akcím a data.

### <a name="database-access"></a>Přístup k databázi

Ochrana dat začíná řízení přístupu k vašim datům. Datové centrum, který je hostitelem vašich dat spravuje fyzický přístup. Můžete nakonfigurovat bránu firewall pro správu zabezpečení na síťové vrstvě. Můžete také řídit přístup tak, že konfigurace přihlášení k ověřování a definování oprávnění pro role serveru a databáze.

#### <a name="firewall-and-firewall-rules"></a>Brána firewall a pravidla brány firewall

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) poskytuje relační databázovou službu pro Azure a další internetové aplikace. Brána firewall pomáhá chránit vaše data tím, že brání veškerému přístupu k vašemu databázovému serveru, dokud neurčíte, které počítače k tomu mají oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku. Další informace najdete v tématu [Přehled pravidel brány firewall služby Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

Služba Azure SQL Database je dostupná jenom prostřednictvím portu TCP 1433. Chcete-li z počítače získat přístup k databázi SQL, ujistěte se, že brána firewall klientského počítače umožňuje odchozí komunikaci TCP na portu TCP 1433. Pokud příchozí připojení nejsou potřeba u ostatních aplikací, zablokujte na portu TCP 1433.

#### <a name="authentication"></a>Authentication

Ověřování se týká způsobu, jakým prokážete svou identitu při připojování k databázi. SQL Database podporuje dva typy ověřování:

-   **Ověřování serveru SQL Server**: účet podporou jediného přihlášení je vytvořen při vytvoření logické instance SQL, nazývá účet předplatitele služby SQL Database. Tento účet se připojí pomocí [ověřování serveru SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview) (uživatelské jméno a heslo). Tento účet je správcem v logické instanci na serveru a ve všech uživatelských databázích připojených k této instanci. Oprávnění účtu předplatitele není možné omezit. Existovat může jenom jeden z těchto účtů.
-   **Ověřování pomocí Azure Active Directory**: [ověřování Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) virtuálních sítí je mechanismus připojení k Azure SQL Database a Azure SQL Data Warehouse s využitím identit ve službě Azure AD. Můžete to centrálně spravovat identity uživatelů databáze.

![Ověřování Azure AD s využitím SQL Database](./media/azure-databse-security-overview/azure-database-fig2.png)

 Mezi výhody ověřování Azure AD patří:
  - To poskytuje alternativu k ověřování serveru SQL Server.
  - Pomáhá zastavit šíření identit uživatelů více databázových serverů a umožňuje rotace hesla na jednom místě.
  - Oprávnění k databázi můžete spravovat pomocí skupin externí (Azure AD).
  - Ukládání hesel se může eliminovat tím, že integrované ověřování Windows a další formy ověřování, které podporuje Azure AD.

#### <a name="authorization"></a>Autorizace
[Autorizace](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins) odkazuje na co může uživatel provádět ve službě Azure SQL database. Je ovládáno pomocí uživatelského účtu databáze [členství v rolích](https://msdn.microsoft.com/library/ms189121) a [oprávnění na úrovni objektu](https://msdn.microsoft.com/library/ms191291.aspx). Autorizace je procesu, který určuje přístup k objektu zabezpečení zabezpečitelné prostředky a operace, které jsou povoleny pro tyto prostředky.

### <a name="application-access"></a>Přístup k aplikaci

#### <a name="dynamic-data-masking"></a>Dynamické maskování dat
Zástupce služby v centru volání může identifikovat volající několik číslic jejich číslo sociálního pojištění nebo číslo platební karty. Ale tyto datové položky by neměly být vystaveny plně služeb zákazníkům.

Můžete definovat pravidla maskování, které zakrývá všechny kromě poslední čtyři číslice čísla sociálního zabezpečení nebo číslo platební karty v sadě výsledků libovolného dotazu.

![Maskování na několika posílaná mezi SQL database a obchodní aplikace](./media/azure-databse-security-overview/azure-database-fig3.png)

Další příklad lze definovat masku příslušná data k ochraně osobních identifikovatelných informací. Vývojář potom může dotazovat produkčních prostředí pro účely odstraňování potíží, aniž by byla porušena dodržování předpisů.

[Dynamické maskování dat služby SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) omezuje riziko ohrožení citlivých dat pomocí jejich maskování pro neoprávněné uživatele. Dynamické maskování dat se podporuje pro Azure SQL Database verze V12.

[Dynamické maskování dat](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) pomáhá zabránit neoprávněnému přístupu k citlivým datům tím, že vám určit, kolik citlivých dat zobrazíte s minimálním dopadem na aplikační vrstvu. Je to funkce zabezpečení založená na zásadách, která skrývá citlivá data v sadě výsledků dotazu nad určenými poli databáze, zatímco data v databázi se nemění.


> [!Note]
> Dynamické maskování dat je možné nakonfigurovat správce databáze Azure, správce serveru nebo oddělení rolí zabezpečení.

#### <a name="row-level-security"></a>Zabezpečení na úrovni řádku
Mezi další běžné požadavky zabezpečení pro víceklientské databáze je [zabezpečení na úrovní řádků](https://msdn.microsoft.com/library/dn765131.aspx). Tato funkce slouží k řízení přístupu k řádkům v databázové tabulce na základě charakteristiky uživatele, který spouští dotaz. (Příklad vlastnosti jsou skupiny členství a spuštění kontextu.)

![Zabezpečení na úrovní řádků umožní uživateli přístup řádky v tabulce prostřednictvím klientskou aplikaci](./media/azure-databse-security-overview/azure-database-fig4.png)

Logiky přístupu k omezení je umístěn v databázové vrstvě spíše než pryč z dat v jiné aplikační vrstvy. Databázový systém platí omezení přístupu pokaždé, když dojde k pokusu o tento přístup k datům z libovolné úrovně. Díky tomu systém zabezpečení spolehlivější a robustní snížením útoku na systém zabezpečení.

Zabezpečení na úrovní řádků zavádí řízení přístupu na základě predikátu. Nabízí flexibilní, centralizované hodnocení, které můžete vzít v úvahu metadata nebo jiných kritérií, které správce určí podle potřeby. Predikát je použít jako kritérium slouží k určení, zda uživatel má odpovídající přístup k datům na základě atributů uživatelů. Řízení přístupu podle popisku můžete implementovat pomocí řízení přístupu na základě predikátu.

## <a name="proactive-monitoring"></a>Proaktivní monitorování
SQL Database pomáhá je zabezpečit vaše data tím, že poskytuje *auditování* a *detekce hrozeb* možnosti.

### <a name="auditing"></a>Auditování
[Auditování služby Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) zvýší vaši schopnost získat přehled o události a změny, ke kterým dochází v rámci databáze. Příklady jsou aktualizace a dotazy na data.

Auditování služby SQL Database sleduje události databáze a zapisuje je do auditu protokolu ve vašem účtu úložiště Azure. Auditování pomáhá zajistit dodržování předpisů, porozumět databázové aktivitě a proniknout do nesrovnalostí a anomálií, které můžou značit problémy obchodního charakteru nebo vzbuzovat podezření na narušení zabezpečení. Auditování umožňuje a usnadňuje dodržování standardů dodržování předpisů, ale nezaručuje dodržování předpisů.

Auditování SQL Database můžete použít:

-   **Zachovat** záznam auditovaných vybrané události. Můžete definovat kategorie akce databáze, které se budou auditovat.
-   **Sestava** na databázové aktivity. Abyste mohli rychle začít s aktivitou a generování sestav událostí můžete použít předem nakonfigurované sestavy a řídicí panel.
-   **Analýza** sestavy. Můžete najít podezřelých událostí, neobvyklé aktivity a trendů.

Existují dvě metody auditování:

-   **Auditování objektů blob**: protokoly se zapisují do úložiště objektů Blob v Azure. Toto je novější auditování metody. Poskytuje vyšší výkon, podporuje auditování na úrovni objektů v vyšší členitost a cenově výhodnější.
-   **Auditování tabulek**: protokoly se zapisují do služby Azure Table storage.

### <a name="threat-detection"></a>Detekce hrozeb
[Detekce hrozeb služby Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) detekuje podezřelé aktivity, které značí potenciální bezpečnostní hrozby. Detekce hrozeb můžete použít pro reakci na podezřelé události v databázi, jako jsou injektáže SQL, když k nim dojde. Poskytuje výstrahy a umožňuje použití auditování služby Azure SQL Database prozkoumat podezřelé události.

![Detekce hrozeb SQL Database a webové aplikace, externí útočník a zlými úmysly](./media/azure-databse-security-overview/azure-database-fig5.jpg)

Například útok prostřednictvím injektáže SQL je jedním z běžných problémů se zabezpečením pro webové aplikace. Používá se k útoku na aplikace řízené daty. Útočníci využívají ohrožení zabezpečení aplikací k vložit škodlivé příkazy SQL do vstupních polí aplikace, porušení nebo úpravy dat v databázi.

Vedoucí pracovníci pověření ochranou zabezpečení nebo jiné určené správce můžete získat okamžité odeslání oznámení o podezřelých databázových aktivitách, když k nim dojde. Jednotlivým oznámením poskytuje podrobnosti o podezřelé aktivitě a doporučuje jak dále zkoumat a zmírnit hrozby.        

## <a name="centralized-security-management"></a>Centralizované zabezpečení správy

[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure. To pomáhá detekovat hrozby, které jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

[Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-database) pomáhá chránit data ve službě SQL Database tím, že poskytuje vhled do zabezpečení všech vašich serverů a databází. Se službou Security Center můžete:

-   Můžete Definujte zásady pro šifrování SQL Database a auditování.
-   Monitorování zabezpečení SQL Database prostředků napříč všemi předplatnými.
-   Rychle identifikovat a opravit problémy se zabezpečením.
-   Integrujte upozornění z [detekce hrozeb Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).

Security Center podporuje přístup na základě rolí.

## <a name="azure-marketplace"></a>Azure Marketplace

Azure Marketplace je internetové tržiště aplikací a služeb, kde start-upy a nezávislí dodavatelé softwaru můžou nabízet svoje řešením zákazníkům Azure z celého světa.
Azure Marketplace spojuje ekosystémy partnerů Microsoft Azure do jednotné platformy pro lepší slouží zákazníky a partnery. Je možné [spustíte hledání v](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) Chcete-li zobrazit dostupné produkty v zabezpečení databáze na webu Azure Marketplace.

## <a name="next-steps"></a>Další postup

- [Zabezpečení služby Azure SQL database](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial)
- [Služba Azure Security Center a Azure SQL Database](https://docs.microsoft.com/azure/security-center/security-center-sql-database)
- [Detekce hrozeb služby SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Zvyšte výkon databáze SQL](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial)

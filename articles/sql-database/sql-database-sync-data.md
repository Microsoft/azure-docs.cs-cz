---
title: Synchronizaci dat Azure SQL (Preview) | Microsoft Docs
description: Tento přehled zavádí synchronizaci dat SQL Azure (Preview)
services: sql-database
author: douglaslms
manager: craigg
ms.service: sql-database
ms.custom: data-sync
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: aa8f5e3b78a65c42840bbe831f5a4f2984a4a357
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650396"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync-preview"></a>Synchronizaci dat mezi několika databází cloudu a místně s synchronizaci dat SQL (Preview)

Synchronizaci dat SQL je služba založená na Azure SQL Database, která umožňuje synchronizaci dat, kterou vyberete obousměrně napříč více databází SQL a instance systému SQL Server.

Synchronizace dat je založena kolem koncept synchronizace skupiny. Synchronizace skupiny je skupina databází, které chcete synchronizovat.

Synchronizace skupiny má následující vlastnosti:

-   **Schématu synchronizace** popisuje data, která se synchronizuje.

-   **Směr synchronizace** může být obousměrně nebo můžete pouze v jednom směru toku. To znamená, může být směr synchronizace *rozbočovače na člen* nebo *člena do centra*, nebo obojí.

-   **Interval synchronizace** jak často dochází k synchronizaci.

-   **Zásady překladu IP adres konflikt** je úrovni zásady skupiny, který může být *rozbočovače wins* nebo *člen wins*.

Synchronizaci dat používá k synchronizaci dat hvězdicové topologii. Definujte jedna z databází ve skupině jako databázi rozbočovače. Zbytek databáze jsou databází člena. Synchronizace nastávají jenom mezi rozbočovače a jednotlivé členy.
-   **Rozbočovače databáze** musí být databáze SQL Azure.
-   **Databází člena** může být databáze SQL, databáze systému SQL Server pro místní nebo instance systému SQL Server na virtuálních počítačích Azure.
-   **Databáze Sync** obsahuje metadata a protokolu pro synchronizaci dat. Synchronizace databáze musí být, že Azure SQL Database umístěný ve stejné oblasti jako databázi rozbočovače. Databáze Sync je zákazník vytvořit a zákazníka, které jsou ve vlastnictví.

> [!NOTE]
> Pokud používáte databázi na místní, budete muset [konfigurace agenta pro místní](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Synchronizaci dat mezi databázemi](media/sql-database-sync-data/sync-data-overview.png)

## <a name="when-to-use-data-sync"></a>Kdy používat synchronizaci dat

Synchronizace dat je užitečné v případech, kdy data musí být pořád aktuální napříč několika databází SQL Azure nebo databáze systému SQL Server. Zde jsou nejčastěji se využívá případů pro synchronizaci dat:

-   **Synchronizace dat hybridní:** se synchronizací dat, abyste zajistili, že data synchronizovat mezi vaší místní databáze a databáze SQL Azure povolit hybridní aplikace. Tato funkce může odvolat pro zákazníky, kteří jsou s přechodu do cloudu a chcete uvést některé své aplikace do Azure.

-   **Distribuované aplikace:** v mnoha případech je vhodné k rozdělení různé úlohy do různých databází. Například pokud máte velké provozní databáze, ale také muset spustit úlohy vytváření sestav nebo analýza tato data, je vhodné mít druhý databázi pro tento další úlohy. Tento postup minimalizuje dopad na výkon na vaše produkční úlohy. Můžete používat synchronizaci dat aby tyto dva databáze synchronizovány.

-   **Globálně distribuované aplikace:** mnoho firem rozmístěny v několika oblastech a i několika zemích. Chcete-li minimalizovat latence sítě, je vhodné vaše data v oblasti blízko vás. S synchronizaci dat se snadnou vejdou databází v oblastech po celém světě synchronizovány.

Synchronizaci dat není vhodná pro následující scénáře:

-   Zotavení po havárii

-   Škálování pro čtení

-   ETL (OLTP na OLAP)

-   Migrace ze systému SQL Server pro místní databáze Azure SQL

## <a name="how-does-data-sync-work"></a>Jak funguje synchronizaci dat? 

-   **Sledování změn dat:** synchronizaci dat sleduje změny pomocí vložit, aktualizovat a odstranit aktivační události. Změny se zaznamenávají v tabulce straně v uživatelské databázi.

-   **Synchronizace dat:** synchronizaci dat slouží v hvězdicové modelu. Rozbočovače synchronizuje s každý člen jednotlivě. Změny z centra, se stáhnou do člen a pak změny z člen odeslány do rozbočovače.

-   **Řešení konfliktů:** synchronizaci dat nabízí dvě možnosti pro řešení konfliktů *rozbočovače wins* nebo *člen wins*.
    -   Pokud vyberete *rozbočovače wins*, změny v centru vždy přepsat změny v člena.
    -   Pokud vyberete *člen wins*, změny v změn přepsání člena v centru. Pokud existuje více než jednoho člena, konečná hodnota závisí na člena, který synchronizuje nejdřív.

## <a name="sync-req-lim"></a> Požadavky a omezení

### <a name="general-considerations"></a>Obecné aspekty

#### <a name="eventual-consistency"></a>Konečná konzistence
Vzhledem k tomu, že synchronizace dat je na základě aktivační události, není zaručena konzistence transakcí. Microsoft zaručuje, že jsou všechny změny provedené nakonec a synchronizaci dat není způsobit ztrátu dat.

#### <a name="performance-impact"></a>Vliv na výkon
Synchronizace dat se používají vložit, aktualizovat a odstranit aktivačních událostí ke sledování změn. Vytvoří straně tabulky v databázi uživatelů pro sledování změn. Tyto aktivity sledování změn mají vliv na vaše zatížení databáze. Vyhodnocení vašeho vrstvy služeb a upgradujte v případě potřeby.

### <a name="general-requirements"></a>Obecné požadavky

-   Každá tabulka musí mít primární klíč. Neměnit hodnotu primární klíč v některém z řádků. Pokud budete muset změnit hodnotu primárního klíče, odstranit řádek a znovu ji vytvořte s novou hodnotu primárního klíče. 

-   Musí být povolena izolace snímku. Další informace najdete v tématu [izolaci snímku v systému SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Obecná omezení

-   Tabulka nemůže obsahovat sloupec identity, který není primární klíč.

-   Data a času datový typ nemůže mít primární klíč.

-   Názvy objektů (databáze, tabulek a sloupců) nesmí obsahovat tisknutelná znaků tečkou (.), zbývající hranaté závorky ([), nebo právo hranatá závorka (]).

-   Ověřování Azure Active Directory není podporována.

#### <a name="unsupported-data-types"></a>Nepodporované datové typy

-   FileStream

-   SQL/CLR UDT

-   Kolekci XMLSchemaCollection (XML podporována)

-   Kurzor, časové razítko, Hierarchyid

#### <a name="limitations-on-service-and-database-dimensions"></a>Omezení dimenzí služby a databáze

| **Dimenze**                                                      | **Limit**              | **Alternativní řešení**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maximální počet skupin synchronizace všechny databáze může patřit do.       | 5                      |                             |
| Maximální počet koncových bodů ve skupině jedním synchronizačním              | 30                     | Vytvoření více skupin synchronizace |
| Maximální počet koncových bodů místně v jednom synchronizace skupiny. | 5                      | Vytvoření více skupin synchronizace |
| Databáze, tabulky, schémat a sloupce                       | 50 znaků na název |                             |
| Tabulky ve skupině pro synchronizaci                                          | 500                    | Vytvoření více skupin synchronizace |
| Sloupce v tabulce v skupiny synchronizace                              | 1000                   |                             |
| Velikost řádek dat v tabulce                                        | 24 Mb                  |                             |
| Interval minimální synchronizace                                           | 5 minut              |                             |
|||

## <a name="faq-about-sql-data-sync"></a>Nejčastější dotazy o synchronizaci dat SQL

### <a name="how-much-does-the-sql-data-sync-preview-service-cost"></a>Kolik službu synchronizaci dat SQL (Preview) stojí?

Ve verzi Preview není nijak zpoplatněn pro samotnou službu synchronizaci dat SQL (Preview).  Však stále naběhnou první poplatky za přenos dat pro přesun dat do aplikace a z vaší instance databáze SQL. Další informace najdete v tématu [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Které oblasti podporují synchronizaci dat?

Synchronizaci dat SQL (Preview) je k dispozici ve všech oblastech veřejného cloudu.

### <a name="is-a-sql-database-account-required"></a>Je potřeba účet SQL Database? 

Ano. Musíte mít účet databáze SQL pro hostování databáze rozbočovače.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Můžete používat synchronizaci dat k synchronizaci mezi místní databáze systému SQL Server pouze? 
Ne přímo. Pro synchronizaci mezi místní databáze systému SQL Server nepřímo, ale vytvoření centra databáze v Azure a následným přidáním do skupiny synchronizace místní databáze.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Můžete používat synchronizaci dat k synchronizaci mezi databázemi SQL, které patří do různých předplatných?
Ano. Můžete synchronizovat mezi databází SQL, které patří do různých předplatných vlastníkem skupiny prostředků.
-   Pokud předplatná patří do stejné klienta, a zda máte oprávnění pro všechny odběry, můžete nakonfigurovat skupiny synchronizace na portálu Azure.
-   Jinak budete muset použít PowerShell k přidání synchronizace členů, které patří do různých předplatných.
   
### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-keep-them-synchronized"></a>Lze používat synchronizaci dat na počáteční hodnoty data z provozní databáze pro prázdnou databázi a pak je synchronizovat zachovat? 
Ano. Vytvoření schématu ručně v nové databáze pomocí skriptování z původní. Jakmile vytvoříte schéma, přidáte do skupiny synchronizace zkopírovat data a udržovat synchronizované tabulky.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Musí používat synchronizaci dat SQL k zálohování a obnovení databází.?

Není doporučeno používat synchronizaci dat SQL (Preview) k vytvoření zálohy, vaše data. Nelze zálohování a obnovení k určitému bodu v čase, protože synchronizace synchronizaci dat SQL (Preview) nejsou verzí. Kromě toho synchronizaci dat SQL (Preview) nezálohuje další SQL objekty, například uložené procedury a rychle neprovádí ekvivalent operaci obnovení.

Pro jeden doporučuje zálohování techniku, najdete v části [zkopírujte Azure SQL database](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Můžete synchronizovat synchronizaci dat šifrovaných tabulky a sloupce?

-   Pokud databáze používá funkce Always Encrypted, budete moct synchronizovat pouze ty tabulky a sloupce, které jsou *není* šifrované. Šifrované sloupce nelze synchronizovat, protože synchronizaci dat nelze dešifrovat data.

-   Pokud sloupec používá šifrování na úrovni sloupce (Vymazat), budete moct synchronizovat sloupci, tak dlouho, dokud velikost řádku je menší než maximální velikost 24 Mb. Synchronizaci dat zpracovává sloupci šifrované pomocí klíče (Vymazat) jako normální binární data. K dešifrování dat na ostatní členové synchronizace, musíte mít stejný certifikát.

### <a name="is-collation-supported-in-sql-data-sync"></a>Jsou podporované kolace v synchronizaci dat SQL?

Ano. Synchronizaci dat SQL podporuje kolaci v následujících scénářích:

-   Pokud schéma tabulky vybrané synchronizace ještě nejsou v databázích máte rozbočovače nebo člen a pak při nasazení skupiny synchronizace, služba automaticky vytvoří odpovídající tabulky a sloupce s nastavení kolace vybraný v prázdné cílové databáze.

-   Pokud tabulky, které chcete synchronizovat, již existuje v databázích rozbočovače a člen, synchronizaci dat SQL vyžaduje, aby sloupců primárního klíče měly stejnou kolaci mezi databázemi rozbočovače a člen pro úspěšné nasazení skupiny synchronizace. Neexistují žádná omezení kolace na sloupce mimo sloupců primárních klíčů.

### <a name="is-federation-supported-in-sql-data-sync"></a>Je federation podporovaný v synchronizaci dat SQL?

Kořenová databáze federace lze ve službě synchronizaci dat SQL (Preview) bez omezení. Koncový bod federované databázi nelze přidat do aktuální verze synchronizaci dat SQL (Preview).

## <a name="next-steps"></a>Další postup

Další informace o Synchronizaci dat SQL:

-   [Nastavení Synchronizace dat SQL Azure](sql-database-get-started-sql-data-sync.md)
-   [Osvědčené postupy pro Synchronizaci dat SQL Azure](sql-database-best-practices-data-sync.md)
-   [Monitorování Synchronizace dat SQL Azure s využitím Log Analytics](sql-database-sync-monitor-oms.md)
-   [Řešení potíží se Synchronizací dat SQL Azure](sql-database-troubleshoot-data-sync.md)

-   Úplné příklady PowerShellu ukazující konfiguraci Synchronizace dat SQL:
    -   [Synchronizace mezi několika databázemi SQL Azure pomocí PowerShellu](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Použití PowerShellu k synchronizaci mezi službou Azure SQL Database a místní databází SQL Serveru](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Stažení dokumentace k rozhraní REST API Synchronizace dat SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Další informace o službě SQL Database:

-   [Přehled služby SQL Database](sql-database-technical-overview.md)
-   [Správa životního cyklu databáze](https://msdn.microsoft.com/library/jj907294.aspx)

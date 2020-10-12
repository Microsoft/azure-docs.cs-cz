---
title: Přehled redundantní vysoké dostupnosti zóny pomocí Azure Database for PostgreSQL-flexibilního serveru (Preview)
description: Přečtěte si o konceptech redundantní vysoké dostupnosti zóny s Azure Database for PostgreSQLm flexibilním serverem.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 7db9ac0eb624c2732295639d65e0311fcf459f71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934930"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql---flexible-server"></a>Koncepty vysoké dostupnosti v Azure Database for PostgreSQL – flexibilní Server

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

Azure Database for PostgreSQL – flexibilní Server nabízí konfiguraci vysoké dostupnosti s funkcí automatického převzetí služeb při selhání s využitím nasazení **zóny redundantního** serveru. Flexibilní server nasazený v zónově redundantní konfiguraci automaticky zřídí a spravuje pohotovostní repliku v jiné zóně dostupnosti. Pomocí replikace PostgreSQL streaming se data replikují do záložního serveru repliky v **synchronním** režimu. 

Redundantní konfigurace zóny umožňuje automatické možnosti převzetí služeb při selhání s nulovou ztrátou dat během plánovaných událostí, jako je například operace výpočetního rozsahu iniciované uživatelem, a také během neplánovaných událostí, jako jsou základní hardwarové a softwarové chyby, selhání sítě a selhání zóny dostupnosti. 

:::image type="content" source="./media/business-continuity/concepts-zone-redundant-high-availability-architecture.png" alt-text="redundantní vysoká dostupnost zóny"::: 

## <a name="zone-redundant-high-availability-architecture"></a>Architektura redundantního vysoké dostupnosti zóny

Můžete si zvolit oblast a zónu dostupnosti pro nasazení primárního databázového serveru. Pohotovostní server repliky se zřídí v jiné zóně dostupnosti se stejnou konfigurací jako primární server, včetně úrovně a velikosti výpočetních prostředků, velikosti úložiště a konfigurace sítě. Protokoly transakcí jsou replikovány do pohotovostní repliky pomocí replikace PostgreSQL streaming v synchronním režimu. Automatické zálohování probíhá pravidelně z primárního databázového serveru, zatímco protokoly transakcí jsou nepřetržitě archivovány do úložiště záloh v pohotovostní replice. 

Stav konfigurace vysoké dostupnosti se nepřetržitě monitoruje a oznamuje na portálu. Níže jsou uvedeny stavy redundantního vysoké dostupnosti zóny:

| **Stav** | **Popis** |
| ------- | ------ |
| <b> Inicializace | Postup vytvoření nového pohotovostního serveru |
| <b> Replikace dat | Po vytvoření pohotovostního režimu se zachytí s primárním. |
| <b> V pořádku | Replikace je v stabilním stavu a v pořádku. |
| <b> Převzetí služeb při selhání | U databázového serveru probíhá převzetí služeb při selhání do úsporného režimu. |
| <b> Odebírá se pohotovostní režim. | V procesu odstraňování pohotovostního serveru. | 
| <b> Nepovoleno | Redundantní vysoká dostupnost zóny není povolená.  |

## <a name="steady-state-operations"></a>Operace s ustáleným stavem

Klientské aplikace PostgreSQL jsou připojené k primárnímu serveru pomocí názvu DATABÁZOVÉho serveru. Čtení z aplikace se obsluhují přímo z primárního serveru, zatímco potvrzení a zápis se potvrdí do aplikace až po zachování dat na primárním serveru i v pohotovostní replice. Vzhledem k tomuto dodatečnému požadavku na zpáteční cestách můžou aplikace očekávat zvýšenou latenci pro zápisy a potvrzení. Stav vysoké dostupnosti můžete monitorovat na portálu.

:::image type="content" source="./media/business-continuity/concepts-high-availability-steady-state.png" alt-text="redundantní vysoká dostupnost zóny"::: 

1. Klienti se připojují k flexibilnímu serveru a provádějí operace zápisu.
2. Změny se replikují do pohotovostní lokality.
3. Primární příjem potvrzení
4. Zápisy/potvrzení jsou potvrzeny.

## <a name="failover-process---planned-downtimes"></a>Proces převzetí služeb při selhání – plánované výpadky

Plánované výpadky událostí zahrnují plánované pravidelné aktualizace softwaru a upgrady dílčí verze. Při konfiguraci vysoké dostupnosti se tyto operace nejprve aplikují na pohotovostní repliku, zatímco aplikace i nadále přistupují k primárnímu serveru. Po aktualizaci repliky v pohotovostním režimu dojde k vyprázdnění primárních připojení k serveru a aktivuje se převzetí služeb při selhání, které aktivuje pohotovostní repliku jako primární stejný název databázového serveru. Klientské aplikace se budou muset znovu připojit ke stejnému názvu databázového serveru na nový primární server a můžou obnovit jeho operace. Nový pohotovostní server bude vytvořen ve stejné zóně jako stará primární. 

Pro jiné operace iniciované uživatelem, jako je například škálování na více míst nebo škálování úložiště, se změny aplikují v pohotovostním režimu jako první a pak na primární. V současné době se u připojení neprovádí převzetí služeb při selhání v pohotovostním režimu, takže při provádění operace na primárním serveru dojde k výpadku.

### <a name="reducing-planned-downtime-with-managed-maintenance-window"></a>Snížení plánovaného výpadku se spravovaným časovým obdobím údržby

 Můžete naplánovat aktivity údržby iniciované v Azure tím, že vyberete 30 minutové okno za den, ve kterém se očekává, že aktivity v databázích mají být nízké. Úlohy údržby Azure, jako jsou třeba opravy nebo upgrade podverze, by se v průběhu tohoto okna probíhat.  U flexibilních serverů konfigurovaných s vysokou dostupností se tyto aktivity údržby provádějí nejprve v pohotovostní replice a pak se aktivuje. Aplikace se pak znovu připojí k novému primárnímu serveru a obnoví své operace, i když se zřídí nový pohotovostní režim.

## <a name="failover-process---unplanned-downtimes"></a>Proces převzetí služeb při selhání – neplánované výpadky

Neplánované výpadky zahrnují chyby softwaru nebo selhání součástí infrastruktury, které mají vliv na dostupnost databáze. V případě, že systém monitorování detekuje nedostupnost serveru, je replikace do pohotovostní repliky závažná a aktivuje se pohotovostní replika jako primární databázový server. Klienti se mohou znovu připojit k databázovému serveru pomocí stejného připojovacího řetězce a obnovit jejich operace. Očekává se, že celková doba převzetí služeb při selhání bude trvat 60 – 120s. V závislosti na aktivitě v primárním databázovém serveru v době převzetí služeb při selhání, jako jsou například velké transakce a doba obnovení, může převzetí služeb při selhání trvat déle.

:::image type="content" source="./media/business-continuity/concepts-high-availability-failover-state.png" alt-text="redundantní vysoká dostupnost zóny"::: 

1. Primární databázový server je mimo provoz a klienti ztratí připojení k databázi. 
2. Pohotovostní server je aktivovaný tak, aby se stal novým primárním serverem. Klient se připojí k novému primárnímu serveru pomocí stejného připojovacího řetězce. Klientská aplikace ve stejné zóně jako primární databázový server snižuje latenci a zvyšuje výkon.
3. Pohotovostní server je vytvořený ve stejné zóně jako starý primární server a replikace streamování se iniciuje. 
4. Jakmile je replikace ustálených stavů navázána, potvrzení a zápisy klientské aplikace budou potvrzeny po uložení dat v obou lokalitách.

## <a name="point-in-time-restore"></a>Obnovení k určitému bodu v čase 

Flexibilní servery, které mají nakonfigurovanou vysokou dostupnost, replikují data v reálném čase na pohotovostní server a udržují aktuální data. Všechny chyby uživatelů na primárním serveru – například nechtěné zrušení tabulky nebo nesprávné aktualizace dat, jsou v pohotovostní replice replikovány. Proto nemůžete použít pohotovostní režim k obnovení z takových logických chyb. K zotavení z takových chyb je nutné provést obnovení k bodu v čase ze záloh.  Pomocí možnosti obnovení k časovému okamžiku flexibilního serveru můžete obnovit čas před tím, než k chybě došlo. Pro databáze nakonfigurované s vysokou dostupností se nový databázový server obnoví jako flexibilní Server s jednou zónou a uživatelem zadaný název. Pak můžete objekt exportovat z databázového serveru a naimportovat ho do provozního databázového serveru. Podobně platí, že pokud chcete klonovat databázový server pro účely testování a vývoje nebo chcete provést obnovení pro jakékoli jiné účely, můžete provést obnovení k časovému okamžiku.

## <a name="zone-redundant-high-availability---features"></a>Redundantní vysoká dostupnost zóny – funkce

-   Pohotovostní replika bude nasazená v přesně stejné konfiguraci virtuálního počítače, jako je primární server, včetně virtuální jádra, úložiště, nastavení sítě (virtuální síť, brána firewall) atd.

-   Možnost Přidat vysokou dostupnost pro existující databázový server.

-   Možnost odebrání záložní repliky tím, že zakážete vysokou dostupnost.

-   Možnost výběru zóny dostupnosti pro primární databázový server.

-   Možnost zastavit, spustit a restartovat primární i pohotovostní databázové servery.

-   Automatické zálohování se provádí z primárního databázového serveru a ukládá se v zóně redundantního úložiště.

-   Klienti se vždy připojují k primárnímu databázovému serveru.

-   Možnost restartovat server a vybrat všechny změny parametrů statického serveru.
  
-   Pravidelné aktivity údržby, jako jsou například upgrady podverze, se v pohotovostním režimu stanou a služba převezme služby při selhání za účelem snížení prostojů.  

## <a name="zone-redundant-high-availability---limitations"></a>Redundantní vysoká dostupnost zóny – omezení

-   Vysoká dostupnost se u výpočetní vrstvy s vysokou dostupností nepodporuje.
-   Vysoká dostupnost je podporována pouze v oblastech, kde je k dispozici více zón.
-   V důsledku synchronní replikace do jiné zóny dostupnosti můžou aplikace nacházet zvýšené latence zápisu a potvrzení.

-   Záložní repliku nelze použít pro dotazy jen pro čtení.

-   V závislosti na aktivitě na primárním serveru v době převzetí služeb při selhání může trvat až dvě minuty nebo déle, než se převzetí služeb při selhání dokončí.

-   Restartování primárního databázového serveru pro výběr změn statických parametrů také restartuje pohotovostní repliku.

-   Konfigurace dalších replik pro čtení není podporována.

-   Konfigurace úkolů správy iniciované zákazníky nemůže být naplánována během spravovaného časového období údržby.

-   K plánovaným událostem, jako jsou škálování výpočetních prostředků nebo škálování úložiště, dochází nejprve na pohotovostním serveru a pak na primárním serveru. Nedojde k převzetí služeb při selhání služby. 

## <a name="next-steps"></a>Další kroky

-   Informace o [kontinuitě podnikových aplikací](./concepts-business-continuity.md)
-   Informace o tom, jak [Spravovat vysokou dostupnost](./how-to-manage-high-availability-portal.md)
-   Další informace o [zálohování a obnovení](./concepts-backup-restore.md)
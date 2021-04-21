---
title: Přehled redundantní vysoké dostupnosti zóny s Azure Database for MySQLm flexibilním serverem
description: Přečtěte si o konceptech redundantní vysoké dostupnosti zóny s Azure Database for MySQL flexibilním serverem.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 5b5e1491d7f76cd4cff76d0c9a1af4daa49fa483
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812997"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Koncepty vysoké dostupnosti v Azure Database for MySQL flexibilním serveru (Preview)

> [!IMPORTANT] 
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

Azure Database for MySQL flexibilní Server (Preview) umožňuje konfigurovat vysokou dostupnost s automatickým převzetím služeb při selhání pomocí možnosti vysoké dostupnosti **zóny** . Flexibilní server nasazený v zónově redundantní konfiguraci automaticky zřídí a spravuje pohotovostní repliku v jiné zóně dostupnosti. Při použití replikace na úrovni úložiště se data **synchronně replikují** na pohotovostní server v sekundární zóně, aby se po převzetí služeb při selhání neztratila žádná data. Převzetí služeb při selhání je plně transparentní z klientské aplikace a nevyžaduje žádné akce uživatele. Pohotovostní server není k dispozici pro žádné operace čtení nebo zápisu, ale jedná se o pasivní pohotovostní režim, který umožňuje rychlé převzetí služeb při selhání. Časy převzetí služeb při selhání obvykle rozsahují od 60-120 sekund.

Konfigurace redundantního vysoké dostupnosti zóny umožňuje automatické převzetí služeb při selhání při plánovaných událostech, jako jsou například výpočetní operace na úrovni uživatele iniciované uživatelem, a neplánované události, jako jsou základní hardwarové a softwarové chyby, selhání sítě a dokonce selhání zóny dostupnosti.

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="zobrazení redundantní vysoké dostupnosti zóny":::

## <a name="zone-redundancy-architecture"></a>Architektura redundance zóny

Primární server je nasazený v oblasti a v konkrétní zóně dostupnosti. Pokud zvolíte vysokou dostupnost, automaticky se nasadí pohotovostní server repliky se stejnou konfigurací, jakou má primární server, včetně výpočetní úrovně, výpočetní velikosti, velikosti úložiště a konfigurace sítě. Data protokolu jsou synchronně replikována do pohotovostní repliky, aby se zajistila nulová ztráta dat v jakékoli situaci. Automatické zálohování, snímky i zálohy protokolů se provádějí z primárního databázového serveru. 

Stav HA se nepřetržitě monitoruje a oznamuje na stránce Přehled.

Níže jsou uvedeny různé stavy replikace:

| **Stav** | **Popis** |
| :----- | :------ |
| <b>NotEnabled | Redundantní HA zóny není povolená. |
| <b>CreatingStandby | V procesu vytváření nového pohotovostního režimu |
| <b>ReplicatingData | Po vytvoření se pohotovostním režimem zachytí s primárním serverem. |
| <b>FailingOver | Databázový server převezme služby při selhání z primárního serveru do úsporného režimu. |
| <b>V pořádku | Redundantní HA zóny je v stabilním stavu a v pořádku. |
| <b>RemovingStandby | Na základě akce uživatele je pohotovostní replika v procesu odstranění.| 

## <a name="advantages"></a>Výhody

Tady jsou některé výhody použití funkce redundance v zóně s vysokou dostupností: 

- Pohotovostní replika se nasadí přes přesnou konfiguraci virtuálních počítačů jako primární, jako je virtuální jádra, Storage, nastavení sítě (virtuální síť, brána firewall) atd.
- Možnost odebrání záložní repliky tím, že zakážete vysokou dostupnost.
- Automatické zálohování jsou založené na snímcích, provádí se z primárního databázového serveru a ukládají se do zóny redundantního úložiště.
- V případě převzetí služeb při selhání se Azure Database for MySQL flexibilní server automaticky převezme do pohotovostní repliky, pokud je povolená vysoká dostupnost. Nastavení vysoké dostupnosti monitoruje primární server a vrátí ho zpátky do režimu online.
- Klienti se vždy připojují k primárnímu databázovému serveru.
- Pokud dojde k selhání databáze nebo selhání uzlu, probíhá pokus o restartování na stejném uzlu. V případě selhání se aktivuje automatické převzetí služeb při selhání.
- Možnost restartovat server a vybrat všechny změny parametrů statického serveru.

## <a name="steady-state-operations"></a>Operace s ustáleným stavem

Aplikace jsou připojené k primárnímu serveru pomocí názvu databázového serveru. Záložní informace o replice nejsou k dispozici pro přímý přístup. Potvrzení a zápisy se aplikacím potvrdí až po trvalém ukládání souborů protokolu na disku primárního serveru i v případě synchronní repliky. Vzhledem k tomuto dodatečnému požadavku na zpáteční cestách můžou aplikace očekávat zvýšenou latenci pro zápisy a potvrzení. Stav vysoké dostupnosti můžete monitorovat na portálu.

## <a name="failover-process"></a>Proces převzetí služeb při selhání 
Pro zajištění kontinuity podnikových procesů potřebujete mít proces převzetí služeb při selhání pro plánované a neplánované události. 

### <a name="planned-events"></a>Plánované události

Plánované výpadky událostí zahrnují aktivity naplánované v Azure, jako jsou pravidelné aktualizace softwaru, upgrady podverze nebo iniciované zákazníky, jako je například škálování výpočetních a škálování operací úložiště. Všechny tyto změny jsou nejprve aplikovány na pohotovostní repliku. Během této doby budou aplikace nadále přistupovat k primárnímu serveru. Po aktualizaci repliky v pohotovostním režimu dojde k vyprázdnění primárních připojení k serveru a aktivuje se převzetí služeb při selhání, které aktivuje pohotovostní repliku tak, aby byla primárním názvem databázového serveru pomocí aktualizace záznamu DNS. Připojení klienta jsou odpojena a musí se znovu připojit a může pokračovat v práci. Nový pohotovostní server se vytvoří ve stejné zóně jako stará primární. Celkový čas převzetí služeb při selhání by měl být 60-120 s. 

>[!NOTE]
> V případě operace škálování výpočetních prostředků je server sekundární repliky škálovat za primárním serverem. Nedošlo k žádnému převzetí služeb při selhání.

### <a name="failover-process---unplanned-events"></a>Proces převzetí služeb při selhání – neplánované události
Neplánované výpadky služeb zahrnují chyby softwaru nebo chyby infrastruktury, jako jsou výpočetní prostředky, sítě, selhání úložiště nebo výpadky napájení, které mají vliv na dostupnost databáze. V případě nedostupnosti databáze je replikace do záložní repliky v pohotovostním stavu a je aktivovaná pohotovostní replika jako primární databáze. Služba DNS je aktualizována a klienti se pak znovu připojí k databázovému serveru a obnoví jejich provoz. Pro celkový čas převzetí služeb při selhání se očekává, že bude trvat 60-120 s. V závislosti na aktivitě v primárním databázovém serveru v době převzetí služeb při selhání, jako jsou například velké transakce a doba obnovení, může převzetí služeb při selhání trvat déle.

### <a name="forced-failover"></a>Vynucené převzetí služeb při selhání
Azure Database for MySQL vynucené převzetí služeb při selhání umožňuje ručně vynutit převzetí služeb při selhání, což vám umožní testovat funkce ve scénářích vaší aplikace a pomůže vám, abyste byli připraveni v případě výpadků. Vynucené převzetí služeb při selhání přepne pohotovostní server, který se stane primárním serverem, aktivací převzetí služeb při selhání, které aktivuje pohotovostní repliku, aby se stal primárním serverem se stejným názvem databázového serveru pomocí aktualizace záznamu DNS. Původní primární server se restartuje a přepne na pohotovostní repliku. Připojení klienta jsou odpojená a musí se znovu připojit, aby se operace obnovila. V závislosti na aktuální úloze a posledním kontrolním bodu se změří celkový čas převzetí služeb při selhání. Obecně se očekává, že bude mezi 60 – 120s.

## <a name="schedule-maintenance-window"></a>Naplánovat časový interval pro správu a údržbu 

Flexibilní servery nabízejí možnosti plánování údržby, kterými si můžete vybrat 30 minutové okno během dne, během kterého bude údržba Azure fungovat, jako je třeba oprava nebo inovace dílčí verze. Pro vaše flexibilní servery nakonfigurované s vysokou dostupností se tyto aktivity údržby provádějí nejdřív v pohotovostní replice. 

## <a name="point-in-time-restore"></a>Obnovení k určitému bodu v čase 
Protože je flexibilní server nakonfigurovaný s vysokou dostupností synchronně replikuje data, je pohotovostní server aktuální s primárním serverem. Všechny chyby uživatelů na primárním počítači, jako je například náhodné zrušení tabulky nebo nesprávné aktualizace, jsou v pohotovostním stavu replikovány do úsporného režimu. Proto nemůžete použít pohotovostní režim pro obnovení takových logických chyb. Chcete-li provést obnovení z těchto logických chyb, je nutné provést obnovení k časovému okamžiku přímo před tím, než došlo k chybě. Při obnovení databáze s vysokou dostupností pomocí flexibilní možnosti obnovení k určitému bodu v čase můžete nový databázový server obnovit jako nový flexibilní Server s uživatelem zadaným názvem. Pak můžete objekt exportovat z databázového serveru a naimportovat ho do provozního databázového serveru. Podobně pokud chcete vytvořit klonování databázového serveru pro účely testování a vývoje nebo chcete obnovit pro jakékoli jiné účely, můžete zvolit buď poslední bod obnovení, nebo vlastní bod obnovení. Operace obnovení vytvoří jeden server flexibilní zóny.

## <a name="mitigate-downtime"></a>Zmírnění výpadků 
Pokud nepoužíváte funkci redundance zóny HA, budete muset mít pořád možnost zmírnit výpadky vaší aplikace. Plánování výpadků, jako jsou plánované opravy, upgrady podverze nebo operace iniciované uživatelem, se dají provádět v rámci plánovaných časových období údržby. Plánovaná výpadky služeb, jako například plánované opravy, upgrady podverze nebo uživatelem iniciované operace, jako je například škálování výpočetních operací, se výpadky nemění. Chcete-li zmírnit dopad aplikace na úlohy údržby iniciované v Azure, můžete zvolit jejich naplánování během dne v týdnu a dobu, kterou aplikace nejméně ovlivňuje. 

Během neplánovaných výpadků, jako je například selhání databáze nebo selhání serveru, bude ovlivněný server restartován ve stejné zóně. I když je na celou zónu ovlivněná celá zóna, můžete databázi obnovit v jiné zóně v rámci dané oblasti. 

## <a name="things-to-know-with-zone-redundancy"></a>Věci, které byste měli znát s redundancí zóny 

Tady je několik důležitých informací, které byste měli mít na paměti při použití vysoké dostupnosti zóny pro zajištění redundance: 

- Vysokou dostupnost lze nastavit **pouze** během flexibilního vytvoření serveru.
- Vysoká dostupnost není podporovaná na výpočetní úrovni s vysokou dostupností.
- V důsledku synchronní replikace do jiné zóny dostupnosti může primární databázový server mít zvýšenou latenci zápisu a potvrzení.
- Záložní repliku nelze použít pro dotazy jen pro čtení.
- V závislosti na aktivitě na primárním serveru v době převzetí služeb při selhání může trvat až 60-120 sekund nebo déle, než se převzetí služeb při selhání dokončí.
- Restartování primárního databázového serveru pro výběr změn statických parametrů také restartuje pohotovostní repliku.
- Konfigurace replik pro čtení pro servery s vysokou dostupností zóny se nepodporuje.
- Konfigurace úkolů správy iniciované zákazníky nemůže být během spravovaných časových období údržby automatizovaná.
- Plánované události, jako je například škálování na výpočetní výkon a dílčí verze, se nastavují v primárním i pohotovostním stavu současně. 


## <a name="next-steps"></a>Další kroky

- Informace o [kontinuitě podnikových aplikací](./concepts-business-continuity.md)
- Přečtěte si o [vysoké dostupnosti zóny jako redundantní](./concepts-high-availability.md) .
- Další informace o [zálohování a obnovení](./concepts-backup-restore.md)

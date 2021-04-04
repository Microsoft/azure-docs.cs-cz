---
title: Vysoká dostupnost – Azure Database for MariaDB
description: Tento článek poskytuje informace o vysoké dostupnosti v Azure Database for MariaDB
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: 4dcb1ac7ce4b468374993c11578bce553f766a42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664329"
---
# <a name="high-availability-in-azure-database-for-mariadb"></a>Vysoká dostupnost v Azure Database for MariaDB
Služba Azure Database for MariaDB poskytuje zaručenou vysokou úroveň dostupnosti s finančně zálohovanou smlouvou o úrovni služeb (SLA) [99,99%](https://azure.microsoft.com/support/legal/sla/MariaDB) doba provozu. Azure Database for MariaDB poskytuje vysokou dostupnost během plánovaných událostí, jako je například operace COMPUTE initated (User-Scale), a také když dojde k neplánovaným událostem, jako je například základní hardware, software nebo selhání sítě. Azure Database for MariaDB se můžou rychle zotavit z nejdůležitějších okolností, takže při použití této služby prakticky neexistují žádné aplikace.

Azure Database for MariaDB je vhodný pro provoz důležitých databází, které vyžadují vysokou dobu provozu. Služba má postavená na architektuře Azure a nabízí vysokou dostupnost, redundanci a odolnost proti chybám při zmírňování výpadků databáze z plánovaných i neplánovaných výpadků, aniž by bylo nutné konfigurovat další součásti. 

## <a name="components-in-azure-database-for-mariadb"></a>Součásti v Azure Database for MariaDB

| **Komponenta** | **Popis**|
| ------------ | ----------- |
| <b>Server databáze MariaDB | Azure Database for MariaDB poskytuje zabezpečení, izolaci, zabezpečování prostředků a možnost rychlého restartování pro databázové servery. Tyto možnosti usnadňují operace, jako je škálování a operace obnovení databázového serveru, po výpadku, který se má stát v sekundách. <br/> Změny dat na databázovém serveru se většinou vyskytují v kontextu transakce databáze. Všechny změny v databázi se zaznamenávají synchronně ve formě protokolů zápisu předem (ib_log) v Azure Storage, který je připojený k databázovému serveru. Během procesu [kontrolního bodu](https://mariadb.com/kb/innodb-redo-log/#checkpoints) databáze jsou datové stránky z paměti databázového serveru také vyprázdněny do úložiště. |
| <b>Vzdálené úložiště | Všechny MariaDB fyzické datové soubory a soubory protokolu jsou uloženy na Azure Storage, který je navržen tak, aby ukládal tři kopie dat v rámci oblasti, aby se zajistila redundance dat, dostupnost a spolehlivost. Vrstva úložiště je také nezávislá na databázovém serveru. Dá se odpojit od serveru databáze, který selhal, a během několika sekund se znovu připojí k novému databázovému serveru. Také Azure Storage nepřetržitě monitorovat všechny chyby úložiště. Pokud je zjištěno poškození bloku, je automaticky vyřešen vytvořením instance nové kopie úložiště. |
| <b>brána | Brána funguje jako proxy databáze a směruje všechna připojení klientů k databázovému serveru. |

## <a name="planned-downtime-mitigation"></a>Omezení zmírňování plánovaných výpadků
Azure Database for MariaDB je navržena tak, aby poskytovala vysokou dostupnost během plánovaných výpadků. 

![zobrazení elastického škálování v Azure MariaDB](./media/concepts-high-availability/elastic-scaling-mariadb-server.png)

Tady je několik plánovaných scénářů údržby:

| **Scénář** | **Popis**|
| ------------ | ----------- |
| <b>Horizontální navýšení nebo snížení kapacity | Když uživatel provede operaci horizontálního navýšení nebo snížení kapacity, zřídí se nový databázový server s využitím konfigurace COMPUTE s možností škálování. Na starém databázovém serveru se můžou dokončit aktivní kontrolní body, připojení klientů se vyprazdňuje, všechny nepotvrzené transakce se zruší a pak se vypnou. Úložiště se pak odpojí od původního databázového serveru a připojí se k novému databázovému serveru. Když klientská aplikace znovu pokusí připojení, nebo se pokusí vytvořit nové připojení, brána přesměruje požadavek na připojení k novému databázovému serveru.|
| <b>Škálování úložiště | Vertikální navýšení kapacity úložiště je online operace a neprovádí přerušování databázového serveru.|
| <b>Nové nasazení softwaru (Azure) | Nové opravy funkcí nebo opravy chyb se automaticky nastávají v rámci plánované údržby služby. Další informace najdete v [dokumentaci](concepts-monitoring.md#planned-maintenance-notification)a také na [portálu](https://aka.ms/servicehealthpm).|
| <b>Upgrady dílčí verze | Azure Database for MariaDB automaticky opraví databázové servery na podverzi určenou v Azure. K tomu dochází jako součást plánované údržby služby. To by znamenalo krátké výpadky v sekundách a databázový server se automaticky restartuje s novou podverzí. Další informace najdete v [dokumentaci](concepts-monitoring.md#planned-maintenance-notification)a také na [portálu](https://aka.ms/servicehealthpm).|


##  <a name="unplanned-downtime-mitigation"></a>Zmírnění neplánovaných výpadků

K neplánovanému výpadku může dojít v důsledku neočekávaných selhání, včetně základní hardwarové chyby, potíží se sítí a softwarových chyb. Pokud dojde k neočekávanému výpadku databázového serveru, nový databázový server se automaticky zřídí během několika sekund. Vzdálené úložiště se automaticky připojí k novému databázovému serveru. MariaDB Engine provádí operaci obnovení pomocí WAL a souborů databáze a otevírá databázový server, aby se klienti mohli připojit. Nepotvrzené transakce jsou ztraceny a musí je aplikace opakovat. I když nemůžete zabránit neplánovaným výpadkům, Azure Database for MariaDB zmírnit výpadky automatickým prováděním operací obnovení na databázovém serveru a vrstvách úložiště bez nutnosti zásahu člověka. 


![zobrazení vysoké dostupnosti v Azure MariaDB](./media/concepts-high-availability/availability-mariadb-server.png)

### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Neplánované výpadky: scénáře selhání a obnovení služby
Tady je několik scénářů selhání a Azure Database for MariaDB automatické obnovení:

| **Scénář** | **Automatické obnovení** |
| ---------- | ---------- |
| <B>Selhání databázového serveru | Pokud je databázový server mimo provoz z důvodu určité základní hardwarové chyby, jsou aktivní připojení vynechána a všechny transakce v transakci jsou přerušeny. Automaticky se nasadí nový databázový server a vzdálené úložiště dat je připojené k novému databázovému serveru. Po dokončení obnovení databáze se klienti mohou připojit k novému databázovému serveru prostřednictvím brány. <br /> <br /> Aplikace, které používají databáze MariaDB, musí být sestaveny způsobem, který detekuje a znovu Zahozená připojení a neúspěšné transakce.  Po opakování aplikace brána transparentně přesměruje připojení k nově vytvořenému databázovému serveru. |
| <B>Selhání úložiště | Aplikace nevidí žádný dopad na jakékoli problémy související s úložištěm, jako je selhání disku nebo poškození fyzického bloku. Jelikož jsou data uložená ve třech kopiích, kopie dat se obsluhuje zbývajícím úložištěm. Blokování poškození se automaticky opraví. Pokud dojde ke ztrátě kopie dat, automaticky se vytvoří nová kopie dat. |

Tady je několik scénářů selhání, které vyžadují akci uživatele při obnovení:

| **Scénář** | **Plán obnovení** |
| ---------- | ---------- |
| <b> Selhání oblasti | Selhání oblasti je vzácná událost. Pokud však potřebujete ochranu při selhání oblasti, můžete nakonfigurovat jednu nebo více replik pro čtení v jiných oblastech pro zotavení po havárii (DR). (Podrobnosti najdete v [tomto článku](howto-read-replicas-portal.md) o vytváření a správě replik pro čtení). V případě selhání na úrovni oblasti můžete ručně povýšit repliku pro čtení nakonfigurovanou v jiné oblasti na provozní server databáze. |
| <b> Chyby logických/uživatelských uživatelů | Obnovení z uživatelských chyb, například omylem vyřazených tabulek nebo nesprávně aktualizovaných dat, zahrnuje provádění obnovení k určitému [bodu v čase](concepts-backup.md) (PITR) tím, že se obnoví a obnoví data, až do doby, kdy došlo k chybě.<br> <br>  Chcete-li obnovit pouze podmnožinu databází nebo konkrétních tabulek a nikoli všechny databáze na databázovém serveru, můžete obnovit databázový server v nové instanci, exportovat tabulky prostřednictvím [mysqldump](howto-migrate-dump-restore.md)a pak pomocí [obnovení](howto-migrate-dump-restore.md#restore-your-mariadb-database) obnovit tyto tabulky do vaší databáze. |



## <a name="summary"></a>Souhrn

Azure Database for MariaDB poskytuje možnost rychlého restartování databázových serverů, redundantního úložiště a efektivního směrování z brány. Pro dodatečnou ochranu dat můžete nakonfigurovat zálohování na geograficky replikované a také nasadit jednu nebo více replik pro čtení v jiných oblastech. Díky funkcím vysoké dostupnosti Azure Database for MariaDB chrání vaše databáze před Nejčastějšími výpadky a nabízí špičkovou smlouvu SLA, která se poskytuje s financemi [99,99% z provozu](https://azure.microsoft.com/support/legal/sla/MariaDB). Všechny tyto možnosti dostupnosti a spolehlivosti umožňují, aby Azure byl ideální platformou pro spouštění důležitých podnikových aplikací.

## <a name="next-steps"></a>Další kroky
- Přečtěte si o [oblastech Azure](../availability-zones/az-overview.md)
- Další informace o [zpracování chyb s přechodným připojením](concepts-connectivity.md)
- Naučte [se replikovat data pomocí replik pro čtení](howto-read-replicas-portal.md) .
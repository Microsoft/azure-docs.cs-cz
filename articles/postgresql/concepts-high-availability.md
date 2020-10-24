---
title: Vysoká dostupnost – Azure Database for PostgreSQL – jeden server
description: Tento článek poskytuje informace o vysoké dostupnosti v Azure Database for PostgreSQL jednom serveru.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 6/15/2020
ms.openlocfilehash: aa9f38b2cefa60a0c3341c1317cf45fbcb735301
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92485439"
---
# <a name="high-availability-in-azure-database-for-postgresql--single-server"></a>Vysoká dostupnost v Azure Database for PostgreSQL – jeden server
Azure Database for PostgreSQL – jedna serverová služba poskytuje zaručenou vysokou úroveň dostupnosti s finančně zajištěnou smlouvou SLA [99,99%](https://azure.microsoft.com/support/legal/sla/postgresql) provozní dobou. Azure Database for PostgreSQL poskytuje vysokou dostupnost během plánovaných událostí, jako je například operace COMPUTE initated (User-Scale), a také když dojde k neplánovaným událostem, jako je například základní hardware, software nebo selhání sítě. Azure Database for PostgreSQL se můžou rychle zotavit z nejdůležitějších okolností, takže při použití této služby prakticky neexistují žádné aplikace.

Azure Database for PostgreSQL je vhodný pro provoz důležitých databází, které vyžadují vysokou dobu provozu. Služba má postavená na architektuře Azure a nabízí vysokou dostupnost, redundanci a odolnost proti chybám při zmírňování výpadků databáze z plánovaných i neplánovaných výpadků, aniž by bylo nutné konfigurovat další součásti. 

## <a name="components-in-azure-database-for-postgresql--single-server"></a>Komponenty v Azure Database for PostgreSQL – jeden server

| **Komponenta** | **Popis**|
| ------------ | ----------- |
| <b>Server databáze PostgreSQL | Azure Database for PostgreSQL poskytuje zabezpečení, izolaci, zabezpečování prostředků a možnost rychlého restartování pro databázové servery. Tyto možnosti usnadňují operace, jako je škálování a operace obnovení databázového serveru, po výpadku, který se má stát v sekundách. <br/> Změny dat na databázovém serveru se většinou vyskytují v kontextu transakce databáze. Všechny změny v databázi se zaznamenávají synchronně ve formě protokolů zápisu předem (WAL) na Azure Storage –, který je připojený k databázovému serveru. Během procesu [kontrolního bodu](https://www.postgresql.org/docs/11/sql-checkpoint.html) databáze jsou datové stránky z paměti databázového serveru také vyprázdněny do úložiště. |
| <b>Vzdálené úložiště | Všechny fyzické datové soubory PostgreSQL a soubory WAL jsou uloženy na Azure Storage, který je navržen tak, aby ukládal tři kopie dat v rámci oblasti, aby se zajistila redundance dat, dostupnost a spolehlivost. Vrstva úložiště je také nezávislá na databázovém serveru. Dá se odpojit od serveru databáze, který selhal, a během několika sekund se znovu připojí k novému databázovému serveru. Také Azure Storage nepřetržitě monitorovat všechny chyby úložiště. Pokud je zjištěno poškození bloku, je automaticky vyřešen vytvořením instance nové kopie úložiště. |
| <b>Brány | Brána funguje jako proxy databáze a směruje všechna připojení klientů k databázovému serveru. |

## <a name="planned-downtime-mitigation"></a>Omezení zmírňování plánovaných výpadků
Azure Database for PostgreSQL je navržena tak, aby poskytovala vysokou dostupnost během plánovaných výpadků. 

:::image type="content" source="./media/concepts-high-availability/azure-postgresql-elastic-scaling.png" alt-text="zobrazení elastického škálování v Azure PostgreSQL":::

1. Horizontální navýšení a snížení kapacity PostgreSQL databázových serverů v řádu sekund
2. Brána, která funguje jako proxy ke směrování klienta, se připojuje ke správnému databázovému serveru.
3. Škálování úložiště se dá provádět bez výpadků. Po převzetí služeb při selhání umožňuje vzdálené úložiště rychlé odpojení nebo opětovné připojení.
Tady je několik plánovaných scénářů údržby:

| **Scénář** | **Popis**|
| ------------ | ----------- |
| <b>Horizontální navýšení nebo snížení kapacity | Když uživatel provede operaci horizontálního navýšení nebo snížení kapacity, zřídí se nový databázový server s využitím konfigurace COMPUTE s možností škálování. Na starém databázovém serveru se můžou dokončit aktivní kontrolní body, připojení klientů se vyprazdňuje, všechny nepotvrzené transakce se zruší a pak se vypnou. Úložiště se pak odpojí od původního databázového serveru a připojí se k novému databázovému serveru. Když klientská aplikace znovu pokusí připojení, nebo se pokusí vytvořit nové připojení, brána přesměruje požadavek na připojení k novému databázovému serveru.|
| <b>Škálování úložiště | Vertikální navýšení kapacity úložiště je online operace a neprovádí přerušování databázového serveru.|
| <b>Nové nasazení softwaru (Azure) | Nové opravy funkcí nebo opravy chyb se automaticky nastávají v rámci plánované údržby služby. Další informace najdete v [dokumentaci](./concepts-monitoring.md#planned-maintenance-notification)a také na [portálu](https://aka.ms/servicehealthpm).|
| <b>Upgrady dílčí verze | Azure Database for PostgreSQL automaticky opraví databázové servery na podverzi určenou v Azure. K tomu dochází jako součást plánované údržby služby. To by znamenalo krátké výpadky v sekundách a databázový server se automaticky restartuje s novou podverzí. Další informace najdete v [dokumentaci](./concepts-monitoring.md#planned-maintenance-notification)a také na [portálu](https://aka.ms/servicehealthpm).|


##  <a name="unplanned-downtime-mitigation"></a>Zmírnění neplánovaných výpadků

K neplánovanému výpadku může dojít v důsledku neočekávaných selhání, včetně základní hardwarové chyby, potíží se sítí a softwarových chyb. Pokud dojde k neočekávanému výpadku databázového serveru, nový databázový server se automaticky zřídí během několika sekund. Vzdálené úložiště se automaticky připojí k novému databázovému serveru. PostgreSQL Engine provádí operaci obnovení pomocí WAL a souborů databáze a otevírá databázový server, aby se klienti mohli připojit. Nepotvrzené transakce jsou ztraceny a musí je aplikace opakovat. I když nemůžete zabránit neplánovaným výpadkům, Azure Database for PostgreSQL zmírnit výpadky automatickým prováděním operací obnovení na databázovém serveru a vrstvách úložiště bez nutnosti zásahu člověka. 


:::image type="content" source="./media/concepts-high-availability/azure-postgresql-built-in-high-availability.png" alt-text="zobrazení elastického škálování v Azure PostgreSQL":::

1. Servery Azure PostgreSQL s možnostmi rychlého škálování.
2. Brána, která slouží jako proxy ke směrování připojení klienta ke správnému databázovému serveru
3. Azure Storage se třemi kopiemi pro spolehlivost, dostupnost a redundanci.
4. Po převzetí služeb při selhání serveru umožňuje vzdálené úložiště také rychlé odpojení nebo opětovné připojení.
   
### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Neplánované výpadky: scénáře selhání a obnovení služby
Tady je několik scénářů selhání a Azure Database for PostgreSQL automatické obnovení:

| **Scénář** | **Automatické obnovení** |
| ---------- | ---------- |
| <B>Selhání databázového serveru | Pokud je databázový server mimo provoz z důvodu určité základní hardwarové chyby, jsou aktivní připojení vynechána a všechny transakce v transakci jsou přerušeny. Automaticky se nasadí nový databázový server a vzdálené úložiště dat je připojené k novému databázovému serveru. Po dokončení obnovení databáze se klienti mohou připojit k novému databázovému serveru prostřednictvím brány. <br /> <br /> Doba obnovení (RTO) závisí na různých faktorech, včetně aktivity v době selhání, jako je například Velká transakce, a množství obnovení, které se má provést během procesu spuštění databázového serveru. <br /> <br /> Aplikace, které používají databáze PostgreSQL, musí být sestaveny způsobem, který detekuje a znovu Zahozená připojení a neúspěšné transakce.  Po opakování aplikace brána transparentně přesměruje připojení k nově vytvořenému databázovému serveru. |
| <B>Selhání úložiště | Aplikace nevidí žádný dopad na jakékoli problémy související s úložištěm, jako je selhání disku nebo poškození fyzického bloku. Jelikož jsou data uložená ve třech kopiích, kopie dat se obsluhuje zbývajícím úložištěm. Blokování poškození se automaticky opraví. Pokud dojde ke ztrátě kopie dat, automaticky se vytvoří nová kopie dat. |

Tady je několik scénářů selhání, které vyžadují akci uživatele při obnovení:

| **Scénář** | **Plán obnovení** |
| ---------- | ---------- |
| <b> Selhání oblasti | Selhání oblasti je vzácná událost. Pokud však potřebujete ochranu při selhání oblasti, můžete nakonfigurovat jednu nebo více replik pro čtení v jiných oblastech pro zotavení po havárii (DR). (Podrobnosti najdete v [tomto článku](./howto-read-replicas-portal.md) o vytváření a správě replik pro čtení). V případě selhání na úrovni oblasti můžete ručně povýšit repliku pro čtení nakonfigurovanou v jiné oblasti na provozní server databáze. |
| <b> Chyby logických/uživatelských uživatelů | Obnovení z uživatelských chyb, například omylem vyřazených tabulek nebo nesprávně aktualizovaných dat, zahrnuje provádění obnovení k určitému [bodu v čase](./concepts-backup.md) (PITR) tím, že se obnoví a obnoví data, až do doby, kdy došlo k chybě.<br> <br>  Chcete-li obnovit pouze podmnožinu databází nebo konkrétních tabulek a nikoli všechny databáze na databázovém serveru, můžete obnovit databázový server v nové instanci, exportovat tyto tabulky prostřednictvím [pg_dump](https://www.postgresql.org/docs/11/app-pgdump.html)a potom pomocí [pg_restore](https://www.postgresql.org/docs/11/app-pgrestore.html) obnovit tyto tabulky do databáze. |



## <a name="summary"></a>Shrnutí

Azure Database for PostgreSQL poskytuje možnost rychlého restartování databázových serverů, redundantního úložiště a efektivního směrování z brány. Pro dodatečnou ochranu dat můžete nakonfigurovat zálohování na geograficky replikované a také nasadit jednu nebo více replik pro čtení v jiných oblastech. Díky funkcím vysoké dostupnosti Azure Database for PostgreSQL chrání vaše databáze před Nejčastějšími výpadky a nabízí špičkovou smlouvu SLA, která se poskytuje s financemi [99,99% z provozu](https://azure.microsoft.com/support/legal/sla/postgresql). Všechny tyto možnosti dostupnosti a spolehlivosti umožňují, aby Azure byl ideální platformou pro spouštění důležitých podnikových aplikací.

## <a name="next-steps"></a>Další kroky
- Přečtěte si o [oblastech Azure](../availability-zones/az-overview.md)
- Další informace o [zpracování chyb s přechodným připojením](concepts-connectivity.md)
- Naučte [se replikovat data pomocí replik pro čtení](howto-read-replicas-portal.md) .
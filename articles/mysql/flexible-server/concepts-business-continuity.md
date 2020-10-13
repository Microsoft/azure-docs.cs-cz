---
title: Přehled provozní kontinuity pomocí Azure Database for MySQL flexibilního serveru
description: Přečtěte si o konceptech kontinuity podnikových aplikací pomocí Azure Database for MySQL flexibilního serveru.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 6beab6f470a39c281020bfdfb7d43c4b6c5e3b70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91756496"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mysql---flexible-server-preview"></a>Přehled provozní kontinuity pomocí Azure Database for MySQL-flexibilního serveru (Preview)

> [!IMPORTANT]
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

Azure Database for MySQL flexibilní Server umožňuje využívat možnosti kontinuity podnikových aplikací, které chrání vaše databáze v případě plánovaného a neplánovaného výpadku. Funkce, jako například automatizované zálohování a vysoká dostupnost, řeší různé úrovně ochrany proti chybám s různou dobou obnovení a únikem informací. Při sestavování aplikace pro ochranu před chybami byste měli zvážit cíl doby obnovení (RTO) a cíl bodu obnovení (RPO) pro každou aplikaci. RTO je odolnost proti výpadkům a RPO je odolnost proti ztrátám dat po přerušení databázové služby.

Následující tabulka znázorňuje funkce flexibilní nabídky serveru.


| **Funkce** | **Popis** | **Omezení** |
| ---------- | ----------- | ------------ |
| **Zálohování & obnovení** | Flexibilní server automaticky provádí každodenní zálohování souborů databáze a průběžně zálohuje transakční protokoly. Zálohy je možné uchovávat po dobu od 1 do 35 dnů. Databázový server budete moct obnovit do libovolného bodu v čase v rámci doby uchovávání záloh. Čas obnovení bude závislý na velikosti dat, která se mají obnovit, a času k provedení obnovení protokolu. Další podrobnosti najdete v tématu [Koncepty – zálohování a obnovení](./concepts-backup-restore.md) . |Zálohovaná data zůstávají v rámci oblasti. |
| **Místní redundantní zálohování** | Flexibilní zálohování serveru se automaticky a bezpečně ukládají v rámci oblasti a ve stejné zóně dostupnosti v místním redundantním úložišti. Místně redundantní zálohy replikují soubory zálohovacích dat serveru třikrát v jednom fyzickém umístění v primární oblasti. Místně redundantní úložiště záloh poskytuje v průběhu daného roku alespoň 99,999999999% (11 devíti) odolnosti objektů. Další podrobnosti najdete v tématu [Koncepty – zálohování a obnovení](./concepts-backup-restore.md) .| Platí pro všechny oblasti |
| **Redundantní vysoká dostupnost zóny** | Flexibilní Server se dá nasadit v režimu vysoké dostupnosti, který nasadí primární a pohotovostní servery ve dvou různých zónách dostupnosti v rámci jedné oblasti. Tato akce se chrání před selháním na úrovni zóny a také pomáhá snižovat prostoje aplikací během plánovaných a neplánovaných výpadků. Data z primárního serveru se synchronně replikují do záložní repliky. Během jakékoliv výpadky se databázový server automaticky převezme z pohotovostní repliky. Další podrobnosti najdete v tématu [Koncepty – vysoká dostupnost](./concepts-high-availability.md) . | Podporováno pro výpočetní úrovně pro obecné účely a paměťově optimalizované. K dispozici pouze v oblastech, kde je k dispozici více zón.|
| **Soubory ke sdílení souborů úrovně Premium** | Soubory databáze jsou uložené v vysoce trvalých a spolehlivých sdílených složkách Azure Premium, které poskytují redundanci dat se třemi kopiemi repliky uloženými v zóně dostupnosti s využitím automatických možností obnovení dat. Další podrobnosti najdete v tématu [sdílené složky úrovně Premium](../../storage/files/storage-how-to-create-premium-fileshare.md) . | Data uložená v rámci zóny dostupnosti |

> [!IMPORTANT]
> Během období Preview se nenabízí smlouva SLA pro dobu provozu, RTO a RPO. Podrobnosti uvedené na této stránce pro vaše informace a účely plánování.

## <a name="planned-downtime-mitigation"></a>Omezení zmírňování plánovaných výpadků
Tady je několik plánovaných scénářů údržby, které účtují výpadky:

| **Scénář** | **Proces**|
| :------------ | :----------- |
| **Škálování na výpočetní výkon (uživatel)**| Když provádíte operaci výpočetního škálování, nový flexibilní Server se zřídí pomocí výpočetní konfigurace s měřítkem. V existujícím databázovém serveru se můžou dokončit aktivní kontrolní body, připojení klientů se vyprazdňuje, všechny nepotvrzené transakce se zruší a pak se vypnou. Úložiště se pak připojí k novému serveru a spustí se databáze, která v případě potřeby provede obnovení do přijetí připojení klienta. |
| **Nové nasazení softwaru (Azure)** | Nové opravy funkcí nebo opravy chyb se automaticky nastávají v rámci plánované údržby služby a můžete naplánovat, kdy se tyto aktivity budou provádět. Další informace najdete v [dokumentaci](https://aka.ms/servicehealthpm)a také na [portálu](https://aka.ms/servicehealthpm) . |
| **Upgrady dílčí verze (Azure)** | Azure Database for MySQL automaticky opraví databázové servery na podverzi určenou v Azure. K tomu dochází jako součást plánované údržby služby. To by znamenalo krátké výpadky v sekundách a databázový server se automaticky restartuje s novou podverzí. Další informace najdete v [dokumentaci](https://docs.microsoft.com/azure/mysql/concepts-monitoring#planned-maintenance-notification)a také na [portálu](https://aka.ms/servicehealthpm).|

Pokud je flexibilní server nakonfigurovaný s **vysokou dostupností zóny**, provede flexibilní Server nejdřív operace na pohotovostním serveru a pak na primárním serveru bez převzetí služeb při selhání. Další podrobnosti najdete v tématu [Koncepty – vysoká dostupnost](./concepts-high-availability.md) .

##  <a name="unplanned-downtime-mitigation"></a>Zmírnění neplánovaných výpadků

Neplánované výpadky se můžou vyskytnout v důsledku neočekávaných selhání, včetně základní hardwarové chyby, potíží se sítí a softwarových chyb. Pokud dojde k neočekávanému výpadku databázového serveru, pokud je nakonfigurovaný s vysokou dostupností [HA], bude se aktivovat pohotovostní replika. V takovém případě se automaticky zřídí nový databázový server. I když nemůžete se vyhnout neplánovanému výpadku, flexibilní Server snižuje výpadek tím, že automaticky provádí operace obnovení na databázovém serveru a vrstvách úložiště, aniž by to vyžadovalo zásah člověka.

### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Neplánované výpadky: scénáře selhání a obnovení služby

Tady je několik neplánovaných scénářů selhání a proces obnovení:

| **Scénář** | **Proces obnovení [ne HA]** | **Proces obnovení [HA]** |
| :---------- | ---------- | ------- |
| **Selhání databázového serveru** | Pokud je databázový server mimo provoz z důvodu určité základní hardwarové chyby, jsou aktivní připojení vynechána a všechny transakce v transakci jsou přerušeny. Azure se pokusí restartovat databázový server. V případě úspěchu se provede obnovení databáze. Pokud se restartování nezdaří, databázový server se pokusí o restartování na jiném fyzickém uzlu.  <br /> <br /> Doba obnovení (RTO) závisí na různých faktorech, včetně aktivity v době selhání, jako je například Velká transakce, a množství obnovení, které se má provést během procesu spuštění databázového serveru. <br /> <br /> Aplikace, které používají databáze MySQL, musí být sestaveny způsobem, který detekuje a znovu Zahozená připojení a neúspěšné transakce.  Když se aplikace opakuje, připojení se přesměrují na nově vytvořený databázový server. | Pokud se zjistí, že se zjistilo selhání databázového serveru, dojde k aktivaci pohotovostního databázového serveru, čímž se sníží prostoje. Další podrobnosti najdete na [stránce s koncepty ha](concepts-high-availability.md) . RTO se očekává jako 60-120 s a RPO = 0. |
| **Selhání úložiště** | Aplikace nevidí žádný dopad na jakékoli problémy související s úložištěm, jako je selhání disku nebo poškození fyzického bloku. Jelikož jsou data uložená ve třech kopiích, kopie dat se obsluhuje zbývajícím úložištěm. Blokování poškození se automaticky opraví. Pokud dojde ke ztrátě kopie dat, automaticky se vytvoří nová kopie dat. | U neobnovitelných chyb dojde při převzetí služeb při selhání do záložní repliky na flexibilní Server, aby se snížil výpadek. Další podrobnosti najdete na [stránce s koncepty ha](../concepts-high-availability.md) . |
| **Chyby logických/uživatelských uživatelů** | Obnovení z uživatelských chyb, například omylem vyřazených tabulek nebo nesprávně aktualizovaných dat, zahrnuje provádění obnovení k určitému [bodu v čase](https://docs.microsoft.com/azure/MySQL/concepts-backup) (PITR) tím, že se obnoví a obnoví data, až do doby, kdy došlo k chybě.<br> <br>  Chcete-li obnovit pouze podmnožinu databází nebo konkrétních tabulek a nikoli všechny databáze na databázovém serveru, můžete obnovit databázový server v nové instanci, exportovat tyto tabulky prostřednictvím [pg_dump](https://www.postgresql.org/docs/current/app-pgdump.html)a potom pomocí [pg_restore](https://www.postgresql.org/docs/current/app-pgrestore.html) obnovit tyto tabulky do databáze. | Tyto chyby uživatelů nejsou chráněny s vysokou dostupností, protože všechny operace uživatele jsou replikovány do úsporného režimu. |
| **Selhání zóny dostupnosti** | I když se jedná o vzácnou událost, pokud chcete provést obnovení při selhání na úrovni zóny, můžete provést obnovení k určitému bodu v čase pomocí zálohování a volbou možnosti vlastní bod obnovení, abyste získali nejnovější data. Nový flexibilní Server bude nasazený v jiné zóně. Doba potřebná k obnovení závisí na předchozím zálohování a na počtu transakčních protokolů, které se mají obnovit. | Flexibilní server provádí automatické převzetí služeb při selhání do pohotovostní lokality. Další podrobnosti najdete na [stránce s koncepty ha](../concepts-high-availability.md) . |
| **Selhání oblasti** | Funkce repliky mezi oblastmi a geografické obnovení se ještě ve verzi Preview nepodporují. | |


> [!IMPORTANT]
> Odstraněné servery **nelze**   obnovit. Pokud server odstraníte, odstraní se i všechny databáze patřící do serveru a nebude možné je obnovit. Pomocí [zámku prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)můžete   zabránit nechtěnému odstranění serveru.


## <a name="next-steps"></a>Další kroky

-   Přečtěte si o [vysoké dostupnosti zóny jako redundantní](./concepts-high-availability.md) .
-   Další informace o [zálohování a obnovení](./concepts-backup-restore.md)

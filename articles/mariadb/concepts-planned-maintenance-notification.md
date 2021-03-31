---
title: Oznámení o plánované údržbě – Azure Database for MariaDB
description: Tento článek popisuje funkci plánovaného oznámení o údržbě v Azure Database for MariaDB
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: c290236dfe7e88999847f8cb0d66b2d3c868c1ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664261"
---
# <a name="planned-maintenance-notification-in-azure-database-for-mariadb"></a>Oznámení o plánované údržbě v Azure Database for MariaDB

Přečtěte si, jak připravit na plánované události údržby Azure Database for MariaDB.

## <a name="what-is-a-planned-maintenance"></a>Co je plánovaná údržba?

Služba Azure Database for MariaDB provádí automatizované opravy základního hardwaru, operačního systému a databázového stroje. Tato oprava zahrnuje nové funkce služby, zabezpečení a aktualizace softwaru. V případě stroje MariaDB jsou upgrady podverze automaticky a zahrnuty v rámci cyklu oprav. Pro opravy není nutná žádná akce uživatele nebo nastavení konfigurace. Oprava se důkladně testuje a zavádí se pomocí postupů bezpečného nasazení.

Plánovaná údržba je časový interval pro správu a údržbu, když se tyto aktualizace služby nasazují na servery v dané oblasti Azure. Během plánované údržby se vytvoří událost oznámení, která informuje zákazníky o nasazení aktualizace služeb v oblasti Azure, která je hostitelem jejich serverů. Minimální doba mezi dvěma plánovanými údržbou je 30 dní. Oznámení o dalším časovém období údržby obdržíte 72 hodin předem.

## <a name="planned-maintenance---duration-and-customer-impact"></a>Plánovaná údržba – Doba trvání a dopad na zákazníky

Očekávaná doba trvání plánované údržby v oblasti Azure je obvykle 15 hodin. Okno také obsahuje dobu ukládání do vyrovnávací paměti pro provedení plánu vrácení zpět, pokud je to nutné. Během plánované údržby může dojít k restartování databázového serveru nebo převzetí služeb při selhání, což může vést k krátké nedostupnosti databázových serverů pro koncové uživatele. Azure Database for MariaDB servery běží v kontejnerech, takže restartování databázového serveru je obvykle rychlé, očekává se dokončení obvykle během 60-120 sekund. Veškerá plánovaná událost údržby včetně každého restartování serveru je pečlivě sledována technickým týmem. Doba převzetí služeb serveru při selhání je závislá na čase obnovení databáze, což může způsobit, že databáze bude v době převzetí služeb při selhání fungovat v online režimu, pokud máte velmi transakční aktivitu. Chcete-li se vyhnout delší době restartování, doporučujeme vyhnout se jakýmkoli dlouho běžícím transakcím (hromadnému zatížení) během plánovaných událostí údržby.

Ve shrnutí, zatímco plánovaná událost údržby běží 15 hodin, ovlivňuje jednotlivý Server většinou 60 sekund v závislosti na transakční aktivitě na serveru. Oznámení se pošle 72 kalendářních hodin před zahájením plánované údržby a další, zatímco probíhá údržba dané oblasti.

## <a name="how-can-i-get-notified-of-planned-maintenance"></a>Jak můžu dostávat oznámení o plánované údržbě?

K přijímání oznámení o nadcházející plánované události údržby můžete využít funkci Plánovaná oznámení údržby. Obdržíte oznámení o nadcházející údržbě 72 kalendářních hodin před událostí a jinou v době, kdy probíhá údržba dané oblasti.

### <a name="planned-maintenance-notification"></a>Oznámení o plánované údržbě

> [!IMPORTANT]
> Plánovaná oznámení o údržbě jsou v tuto chvíli k dispozici ve verzi Preview ve všech oblastech **kromě** středozápadní USA

**Plánovaná oznámení údržby** vám umožní přijímat výstrahy na nadcházející plánovanou událost údržby pro váš Azure Database for MariaDB. Tato oznámení jsou integrovaná s plánovanou údržbou [Service Health](../service-health/overview.md) a umožňují zobrazit veškerou plánovanou údržbu vašich předplatných na jednom místě. Pomáhá také škálovat oznámení do správných cílových skupin pro různé skupiny prostředků, protože u různých zdrojů můžete mít zodpovědné různé kontakty. Oznámení o nadcházející údržbě 72 kalendářní hodiny před událostí obdržíte.

Povedeme všechny pokusy o poskytnutí **oznámení o plánované údržbě** 72 hodin pro všechny události. V případech kritických nebo bezpečnostních oprav ale můžou být oznámení odeslána blíže k události nebo by se měla vynechat.

Můžete buď zaškrtnout oznámení o plánované údržbě Azure Portal nebo nakonfigurovat výstrahy pro příjem oznámení. 

### <a name="check-planned-maintenance-notification-from-azure-portal"></a>Kontrolovat oznámení o plánované údržbě z Azure Portal

1. V [Azure Portal](https://portal.azure.com)vyberte **Service Health**.
2. Vybrat kartu **plánované údržby**
3. Vyberte **předplatné**, * * oblast a **službu** , pro kterou chcete kontrolovat oznámení o plánované údržbě. 
   
### <a name="to-receive-planned-maintenance-notification"></a>Příjem plánovaného oznámení o údržbě

1. Na [portálu](https://portal.azure.com)vyberte **Service Health**.
2. V části **výstrahy** vyberte výstrahy týkající se **stavu**.
3. Vyberte **+ Přidat upozornění na stav služby** a vyplňte pole.
4. Vyplňte požadovaná pole. 
5. Zvolte **Typ události**, vyberte **plánovaná údržba** nebo **Vybrat vše** .
6. V části **skupiny akcí** definujte způsob, jakým chcete výstrahu přijmout (získat e-mail, aktivovat aplikaci logiky atd.).  
7. Zajistěte, aby pravidlo Povolit při vytváření bylo nastaveno na Ano.
8. Vyberte **vytvořit pravidlo upozornění** pro dokončení upozornění.

Podrobné informace o tom, jak vytvořit **výstrahy týkající se stavu služby**, najdete [v tématu vytváření výstrah protokolu aktivit u oznámení služby](../service-health/alerts-activity-log-service-notifications-portal.md).

## <a name="can-i-cancel-or-postpone-planned-maintenance"></a>Můžu plánovanou údržbu zrušit nebo odložit?

Údržba je nutná k zajištění zabezpečeného, stabilního a aktuálnosti serveru. Událost plánované údržby nelze zrušit nebo odložit. Po odeslání oznámení do určité oblasti Azure nelze provést změny plánu oprav pro žádný jednotlivý Server v této oblasti. Oprava je zahrnuta pro celou oblast najednou. Služba Azure Database for MariaDB je navržená pro cloudovou nativní aplikaci, která nevyžaduje podrobné řízení nebo přizpůsobení služby.

## <a name="are-all-the-azure-regions-patched-at-the-same-time"></a>Dochází k opravám ve všech oblastech Azure ve stejnou dobu?

Ne, všechny oblasti Azure jsou opraveny během časování okna nasazení. Toto okno nasazení se obvykle roztáhne z 5 odp. na místní čas příštího dne v dané oblasti Azure. Geograficky spárované oblasti Azure jsou opraveny v různých dnech. Pro zajištění vysoké dostupnosti a kontinuity podnikových databázových serverů se doporučuje používat [repliky čtení v různých oblastech](./concepts-read-replicas.md#cross-region-replication) .

## <a name="retry-logic"></a>Logika opakování

Přechodná chyba, označovaná také jako přechodná chyba, je chyba, která se vyřeší sám. Při údržbě může dojít k [přechodným chybám](./concepts-connectivity.md#transient-errors) . Většina těchto událostí je systémem automaticky snížena za méně než 60 sekund. Přechodné chyby by se měly zpracovat pomocí [logiky opakování](./concepts-connectivity.md#handling-transient-errors).


## <a name="next-steps"></a>Další kroky

- V případě jakýchkoli otázek nebo návrhů, které byste mohli potřebovat při práci se Azure Database for MariaDB, pošlete e-mail týmu Azure Database for MariaDB na adrese AskAzureDBforMariaDB@service.microsoft.com
- Pokyny k vytvoření výstrahy na metrikě najdete v tématu [Nastavení výstrah](howto-alert-metric.md) .
- [Řešení potíží s připojením ke službě Azure Database for MariaDB](howto-troubleshoot-common-connection-issues.md)
- [Zpracování přechodných chyb a efektivní připojení k Azure Database for MariaDB](concepts-connectivity.md)
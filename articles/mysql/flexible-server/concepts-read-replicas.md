---
title: Čtení replik – Azure Database for MySQL-flexibilní Server
description: 'Přečtěte si o replikách pro čtení v Azure Database for MySQL flexibilním serveru: vytváření replik, připojování k replikám, monitorování replikace a zastavení replikace.'
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: 6f3482bdc608d97e4adba5f99393e74f2e6c7cde
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92795151"
---
# <a name="read-replicas-in-azure-database-for-mysql---flexible-server"></a>Čtení replik v Azure Database for MySQL-flexibilním serveru

> [!IMPORTANT]
> Čtení replik v Azure Database for MySQL-flexibilní Server je ve verzi Preview.

MySQL je jedním z oblíbených databázových modulů pro provozování webových a mobilních aplikací v internetovém měřítku. Spousta našich zákazníků ji používá pro svoje online vzdělávací služby, služby streamování videí, digitální platební řešení, platformy elektronického obchodování, herní služby, diskusní portály, státní správu a weby zdravotnictví. Tyto služby jsou nutné k obsluze a škálování při zvyšování provozu webové nebo mobilní aplikace.

Na straně aplikace je aplikace obvykle vyvinutá v jazyce Java nebo php a migrována do provozu na Azure Virtual Machine Scale Sets nebo v Azure App Services nebo je kontejnerem pro spuštění ve službě Azure Kubernetes Service (AKS). Pomocí sady škálování virtuálních počítačů, App Service nebo AKS jako základní infrastruktury, se škálování aplikace zjednodušuje okamžitým zřizováním nových virtuálních počítačů a replikací bezstavových komponent aplikací do zařízení do systému stravování až po požadavky, ale databáze ukončí kritické body jako centralizované stavové součásti.

Funkce replika čtení umožňuje replikovat data z Azure Database for MySQL flexibilního serveru do serveru jen pro čtení. Můžete replikovat ze zdrojového serveru do až **10** replik. Repliky se aktualizují asynchronně s využitím technologie replikace na základě pozice v souboru binárního protokolu (binlog) nativní pro stroj MySQL. Další informace o replikaci binlog najdete v tématu [Přehled replikace MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Repliky jsou nové servery, které spravujete podobně jako vaše zdrojové Azure Database for MySQL flexibilní servery. Účtují se vám poplatky za každou repliku čtení na základě zřízené výpočetní služby v virtuální jádra a úložišti v GB/měsíc. Další informace najdete v tématu [ceny](./concepts-compute-storage.md#pricing).

Další informace o funkcích a problémech replikace MySQL najdete v [dokumentaci k replikaci MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

> [!NOTE]
> Komunikace bez posunu
>
> Microsoft podporuje různé a zahrnuté prostředí. Tento článek obsahuje odkazy na _podřízený_ text. [Průvodce stylem Microsoft pro komunikaci bez předplatných](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) se tímto způsobem rozpoznává jako vyloučené slovo. Toto slovo se v tomto článku používá kvůli konzistenci, protože je aktuálně slovo, které se zobrazuje v softwaru. Když se software aktualizuje, aby se odebralo slovo, aktualizuje se tento článek na zarovnání.
>

## <a name="common-use-cases-for-read-replica"></a>Běžné případy použití pro čtení repliky

Funkce replika čtení pomáhá zlepšit výkon a škálu úloh náročných na čtení. Úlohy čtení se dají pro repliky izolovat, zatímco úlohy zápisu můžou být směrované na zdroj.

Mezi běžné scénáře patří:

* Škálování čtení-úloh přicházejících z aplikace pomocí proxy jednoduchého připojení jako [ProxySQL](https://aka.ms/ProxySQLLoadBalanceReplica) nebo pomocí vzoru založeného na mikroslužbách pro horizontální navýšení kapacity čtení dotazů přicházejících z aplikace na čtení replik
* Úlohy sestav BI nebo analytická můžou používat repliky pro čtení jako zdroj dat pro vytváření sestav.
* V případě IoT nebo výrobního scénáře, kdy se informace o telemetrii ingestují do databázového stroje MySQL, zatímco pro vytváření sestav dat se používá víc replik pro čtení

Vzhledem k tomu, že repliky jsou jen pro čtení, nesnižují na zdroj přímo zátěž kapacity pro zápis. Tato funkce není určená pro úlohy, které jsou náročné na zápis.

Funkce replika čtení používá asynchronní replikaci MySQL. Tato funkce není určena pro scénáře synchronní replikace. Mezi zdrojem a replikou bude měřitelná prodleva. Data v replice nakonec budou konzistentní s daty ve zdroji. Tato funkce se používá pro úlohy, které můžou toto zpoždění obsloužit.

## <a name="create-a-replica"></a>Vytvoření repliky

Pokud na zdrojovém serveru nejsou žádné existující servery repliky, zdroj se nejdřív restartuje a připraví se pro replikaci.

Když spustíte pracovní postup vytvoření repliky, vytvoří se prázdný Azure Database for MySQL server. Nový server je vyplněn daty, která byla na zdrojovém serveru. Čas vytvoření závisí na množství dat ve zdroji a času od posledního týdenního úplného zálohování. Čas může být v rozsahu od několika minut až po několik hodin.

> [!NOTE]
> Repliky čtení se vytvářejí se stejnou konfigurací serveru jako zdroj. Konfiguraci serveru repliky je možné po vytvoření změnit. Server repliky se vždycky vytvoří ve stejné skupině prostředků, stejné lokalitě a stejném předplatném jako zdrojový server. Pokud chcete vytvořit server repliky pro jinou skupinu prostředků nebo jiné předplatné, můžete [server repliky](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) po vytvoření přesunout. Doporučuje se udržovat konfiguraci serveru repliky ve stejné nebo větší hodnotě než zdroj, aby bylo zajištěno, že je replika schopná udržet se zdrojem.

Naučte se [vytvořit repliku pro čtení v Azure Portal](how-to-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Připojení k replice

Při vytváření repliky zdědí metodu připojení zdrojového serveru. Nelze změnit metodu připojení repliky. Například pokud má zdrojový server **privátní přístup (Integration VNET)** , replika nemůže být ve **veřejném přístupu (povolených IP adres)** .

Replika dědí účet správce ze zdrojového serveru. Všechny uživatelské účty na zdrojovém serveru se replikují do replik pro čtení. K replice pro čtení se můžete připojit pouze pomocí uživatelských účtů, které jsou k dispozici na zdrojovém serveru.

K replice se můžete připojit pomocí jejího názvu hostitele a platného uživatelského účtu, stejně jako při běžném Azure Database for MySQL flexibilním serveru. Pro server s názvem **myreplica** s uživatelským jménem správce **myadmin** se můžete připojit k replice pomocí rozhraní příkazového řádku MySQL:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin -p
```

Na příkazovém řádku zadejte heslo pro uživatelský účet.

## <a name="monitor-replication"></a>Monitorování replikace

Azure Database for MySQL flexibilní Server poskytuje metriku **replikace v řádu sekund** v Azure monitor. Tato metrika je k dispozici pouze pro repliky. Tato metrika se počítá pomocí `seconds_behind_master` metriky dostupné v `SHOW SLAVE STATUS` příkazu MySQL. Nastavte výstrahu, která vás informuje, když prodleva replikace dosáhne hodnoty, která není pro vaše zatížení přijatelná.

Pokud se zobrazí zvýšené zpoždění replikace, přečtěte si téma [řešení potíží s latencí replikace](./../howto-troubleshoot-replication-latency.md) k řešení potíží a pochopení možných příčin.

## <a name="stop-replication"></a>Zastavení replikace

Replikaci mezi zdrojem a replikou můžete zastavit. Po zastavení replikace mezi zdrojovým serverem a replikou pro čtení se replika na samostatný server. Data na samostatném serveru jsou data, která byla v replice k dispozici v době spuštění příkazu pro zastavení replikace. Samostatný server není zachytávání na zdrojovém serveru.

Pokud se rozhodnete zastavit replikaci do repliky, ztratíte všechny odkazy na předchozí zdrojové a další repliky. Mezi zdrojem a jeho replikou nedochází k automatizovanému převzetí služeb při selhání.

> [!IMPORTANT]
> Samostatný server se nedá znovu vytvořit do repliky.
> Před zastavením replikace v replice pro čtení zajistěte, aby měla replika všechna data, která požadujete.

Přečtěte si, jak [zastavit replikaci do repliky](how-to-read-replicas-portal.md).

## <a name="failover"></a>Převzetí služeb při selhání

Mezi zdrojovým serverem a serverem repliky neexistuje automatizované převzetí služeb při selhání. 

Repliky čtení jsou určené pro škálování úloh náročných na čtení a nejsou navržené tak, aby splňovaly požadavky serveru na vysokou dostupnost. Mezi zdrojovým serverem a serverem repliky neexistuje automatizované převzetí služeb při selhání. Když se replikace v replice pro čtení zastavuje online v režimu čtení, je to způsob, jakým se provádí ruční převzetí služeb při selhání.

Vzhledem k tomu, že replikace je asynchronní, existuje prodleva mezi zdrojem a replikou. Velikost prodlevy může mít vliv na několik faktorů, jako je to, jak velké zatížení na zdrojovém serveru běží a latence mezi datovými centry. Ve většině případů je prodleva repliky v rozsahu od několika sekund do několika minut. Vlastní prodlevu replikace můžete sledovat pomocí *prodlevy repliky* metriky, která je k dispozici pro každou repliku. Tato metrika ukazuje čas od poslední opakované transakce. Doporučujeme, abyste zjistili, jaký je průměrný prodleva tím, že v časovém intervalu pozoruje prodlevu repliky. Můžete nastavit upozornění na prodlevu repliky, takže pokud bude mimo očekávaný rozsah, můžete provést akci.

> [!Tip]
> Pokud převzetí služeb při selhání repliky přestanou, prodleva v době odpojování repliky ze zdroje bude označovat, kolik dat se ztratilo.

Jakmile se rozhodnete, že chcete převzít služeb při selhání do repliky, 

1. Zastavení replikace do repliky<br/>
   Tento krok je nezbytný k tomu, aby server repliky mohl přijímat zápisy. V rámci tohoto procesu se server repliky odpojí ze zdroje. Jakmile zahájíte zastavení replikace, proces back-endu obvykle trvá přibližně 2 minuty, než se dokončí. V části [zastavení replikace](#stop-replication) v tomto článku se seznámíte s důsledky této akce.
    
2. Nasměrujte aplikaci na (bývalé) repliku.<br/>
   Každý server má jedinečný připojovací řetězec. Aktualizujte svou aplikaci tak, aby odkazovala na (bývalé) repliku místo zdroje.
    
Po úspěšném zpracování čtení a zápisu vaší aplikace jste dokončili převzetí služeb při selhání. Množství prostojů, na kterých bude prostředí aplikace záviset při zjištění problému a dokončení kroků 1 a 2 výše.

## <a name="considerations-and-limitations"></a>Důležité informace a omezení

| Scénář | Omezení/zvážení |
|:-|:-|
| Replika na serveru s povolenou úrovní dostupnosti v zóně – redundantní | Nepodporováno |
| Ceny | Náklady na spuštění serveru repliky jsou založené na oblasti, ve které je spuštěný server repliky. |
| Restart zdrojového serveru | Když vytvoříte repliku pro zdroj, který nemá žádné existující repliky, zdroj se nejdřív restartuje, aby se připravil pro replikaci. Vezměte v úvahu a udělejte tyto operace v době mimo špičku. |
| Nové repliky | Replika pro čtení je vytvořená jako nový Azure Database for MySQL flexibilní Server. Existující server nelze vytvořit do repliky. Nemůžete vytvořit repliku jiné repliky pro čtení. |
| Konfigurace repliky | Replika je vytvořena pomocí stejné konfigurace serveru jako zdroj. Po vytvoření repliky se dá změnit několik nastavení nezávisle na zdrojovém serveru: generování výpočetních prostředků, virtuální jádra, úložiště a doba uchovávání záloh. Výpočetní vrstvu lze také změnit nezávisle.<br> <br> **DŮLEŽITÉ**  <br> – Před aktualizací konfigurace zdrojového serveru na nové hodnoty aktualizujte konfiguraci repliky na hodnotu stejné nebo větší. Tato akce zajistí, že replika bude moct udržovat krok se všemi změnami na zdrojovém serveru. <br/> Metoda připojení a nastavení parametrů jsou při vytvoření repliky zděděné ze zdrojového serveru do repliky. Pak jsou pravidla repliky nezávislá. |
| Zastavené repliky | Pokud zastavíte replikaci mezi zdrojovým serverem a replikou pro čtení, zastavená replika se stane samostatným serverem, který přijímá čtení i zápis. Samostatný server se nedá znovu vytvořit do repliky. |
| Odstraněné zdrojové a samostatné servery | Po odstranění zdrojového serveru se replikace zastaví na všechny repliky čtení. Tyto repliky se automaticky změní na samostatné servery a můžou přijímat operace čtení i zápisu. Samotný zdrojový server se odstraní. |
| Uživatelské účty | Uživatelé na zdrojovém serveru se replikují do replik pro čtení. K replice pro čtení se můžete připojit pouze pomocí uživatelských účtů, které jsou k dispozici na zdrojovém serveru. |
| Parametry serveru | Aby se při použití replik pro čtení zabránilo přerušení synchronizace dat a možné ztrátě nebo poškození dat, některé parametry serveru neumožňují aktualizaci. <br> Následující parametry serveru jsou uzamčené na zdrojovém serveru i na serverech repliky:<br> - [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) <br> - [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) <br> [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler)Parametr je uzamčen na serverech repliky. <br> Pokud chcete na zdrojovém serveru aktualizovat jeden z výše uvedených parametrů, odstraňte prosím servery repliky, aktualizujte hodnotu parametru ve zdroji a znovu vytvořte repliky. |
| Ostatní | -Vytvoření repliky repliky není podporováno. <br> – Tabulky v paměti můžou způsobit, že se repliky nesynchronizují. Toto je omezení technologie replikace MySQL. Další informace najdete v [referenční dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) . <br>– Zajistěte, aby tabulky zdrojového serveru měly primární klíče. Nedostatek primárních klíčů může způsobit latenci replikace mezi zdrojem a replikami.<br>– Projděte si úplný seznam omezení replikace MySQL v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) . |

## <a name="next-steps"></a>Další kroky

- Naučte se [vytvářet a spravovat repliky pro čtení pomocí Azure Portal](how-to-read-replicas-portal.md)
- Naučte se [vytvářet a spravovat repliky pro čtení pomocí Azure CLI](how-to-read-replicas-cli.md) .

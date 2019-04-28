---
title: Konfigurace geografické replikace pro Azure Cache pro Redis | Dokumentace Microsoftu
description: Zjistěte, jak replikovat napříč zeměpisnými oblastmi Azure Cache pro instance Redis.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 375643dc-dbac-4bab-8004-d9ae9570440d
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: 4254175955c3560c7bd0fdd08c6b60c318238b76
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60552287"
---
# <a name="how-to-configure-geo-replication-for-azure-cache-for-redis"></a>Konfigurace geografické replikace pro Azure Cache pro Redis

Geografická replikace poskytuje mechanismus pro propojení dvou úroveň Premium mezipaměti Azure pro instance Redis. Jeden mezipaměti je vybrán jako primární propojené mezipaměti a druhý jako sekundární propojené mezipaměti. Propojené sekundární mezipaměť bude jen pro čtení a data zapsaná do primární mezipaměti se replikuje do sekundární propojené mezipaměti. Tuto funkci je možné replikovat do mezipaměti v různých oblastech Azure. Tento článek obsahuje pokyny pro konfigurace geografické replikace pro Premium úroveň mezipaměti Azure pro instance Redis.

## <a name="geo-replication-prerequisites"></a>Požadavky na geografickou replikaci

Konfigurace geografické replikace mezi dvěma mezipaměti, musí být splněny následující požadavky:

- Obě mezipaměti jsou [úroveň Premium](cache-premium-tier-intro.md) ukládá do mezipaměti.
- Obě mezipaměti jsou ve stejném předplatném Azure.
- Sekundární propojené mezipaměti se stejnou velikost mezipaměti nebo větší velikost mezipaměti, než primární propojené mezipaměti.
- Obě mezipaměti jsou vytvořeny a v běžícím stavu.

Některé funkce nejsou podporovány s geografickou replikaci:

- Trvalost není podporován s geografickou replikací.
- Clustering se podporuje, pokud obě mezipamětí aktivovaným clusteringem a mít stejný počet horizontálních oddílů.
- Jsou podporovány mezipaměti ve stejné virtuální síti.
- Podporují se mezipaměti v různých virtuálních sítích s omezením. Zobrazit [můžete použít geografickou replikaci s Moje mezipamětí ve virtuální síti?](#can-i-use-geo-replication-with-my-caches-in-a-vnet) Další informace.

Po dokončení konfigurace geografické replikace vaše propojené mezipaměti pár platí následující omezení:

- Sekundární propojené mezipaměti je jen pro čtení; může číst z něj, ale nemohou do ní zapisovat žádná data. 
- Je odebraná všechna data, která byla v sekundární propojené mezipaměti před přidáním odkazu. Replikovaná data zůstanou v sekundární propojené mezipaměti odebrat ale pokud geografická replikace je novější.
- Není možné [škálování](cache-how-to-scale.md) buď mezipaměti, když jsou propojení mezipamětí.
- Není možné [změnit počet horizontálních dělení](cache-how-to-premium-clustering.md) Pokud mezipaměť obsahuje povoleným clusteringem.
- Nelze zapnout stálost buď mezipaměť App Fabric.
- Je možné [exportovat](cache-how-to-import-export-data.md#export) z obou mezipaměti.
- Není možné [Import](cache-how-to-import-export-data.md#import) do sekundární propojené mezipaměti.
- Nelze odstranit propojené mezipaměti nebo skupinu prostředků, které obsahuje, dokud se zrušit propojení mezipamětí. Další informace najdete v tématu [proč operace nezdaří při pokusu odstranit mé propojené mezipaměti?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Mezipaměti jsou v různých oblastech, platit náklady na celkový výstup sítě pro data přesouvat mezi oblastmi. Další informace najdete v tématu [kolik to stojí Moje data replikovat napříč několika oblastmi Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Automatické převzetí služeb při selhání nedojde mezi primárním a sekundárním propojené mezipaměti. Další informace a informace o tom, jak převzetí služeb při selhání klienta aplikace najdete v tématu [jak funguje přebírání služeb při selhání sekundární propojené mezipaměti?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Přidat odkaz geografické replikace

1. K propojení dvou mezipaměti pro geografickou replikaci, prvním kliknutí **geografickou replikaci** z nabídky prostředků v mezipaměti, kterou chcete být primární propojené mezipaměti. Klepnutím na tlačítko **přidat propojení replikace mezipaměti** z **geografickou replikaci** okno.

    ![Přidat odkaz](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Klikněte na název určený sekundární mezipaměť z **kompatibilní mezipaměti** seznamu. Pokud vaše sekundární mezipaměť se nezobrazí v seznamu, zkontrolujte, zda [požadavky na geografickou replikaci](#geo-replication-prerequisites) pro sekundární mezipaměť jsou splněny. Chcete-li filtrovat mezipaměti podle oblasti, klikněte na oblasti na mapě zobrazíte pouze mezipaměti v **kompatibilní mezipaměti** seznamu.

    ![Kompatibilní mezipaměti geografické replikace](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Můžete také spustit proces propojení nebo zobrazení podrobností o sekundární mezipaměť pomocí místní nabídky.

    ![Místní nabídka geografické replikace](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Klikněte na tlačítko **odkaz** propojit dvě mezipaměti a zahájí proces replikace.

    ![Propojení mezipamětí](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Můžete zobrazit průběh procesu replikace na **geografickou replikaci** okno.

    ![Stav propojení](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Můžete také zobrazit stav propojení na **přehled** okno pro primární a sekundární mezipaměti.

    ![Stav mezipaměti](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Po dokončení procesu replikace **propojit stav** změny **Succeeded**.

    ![Stav mezipaměti](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Primární propojené mezipaměti zůstává k dispozici pro použití během procesu vytváření odkazů. Sekundární propojené mezipaměti není k dispozici, dokud se nedokončí proces propojení.

## <a name="remove-a-geo-replication-link"></a>Odebrat odkaz geografické replikace

1. Odebere propojení mezi dvěma mezipaměti a zastavení geografickou replikaci, klikněte na tlačítko **zrušit propojení mezipamětí** z **geografickou replikaci** okno.
    
    ![Zrušit propojení mezipamětí](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Po dokončení procesu zrušení propojení sekundární mezipaměť je k dispozici pro operace čtení i zápisu.

>[!NOTE]
>Pokud je odebrán odkaz geografické replikace, replikovaných dat z primární propojené mezipaměti zůstává v sekundární mezipaměti.
>
>

## <a name="geo-replication-faq"></a>Nejčastější dotazy týkající se geografická replikace

- [Můžete použít geografickou replikaci s mezipamětí úroveň Standard nebo Basic?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [Je k dispozici pro použití mezipaměť během procesu propojení nebo odpojení?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Můžete propojit více než dva mezipaměti najednou?](#can-i-link-more-than-two-caches-together)
- [Můžete propojit dvě mezipaměti z různých předplatných Azure?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Můžete propojit dvě mezipaměti s jinou velikostí?](#can-i-link-two-caches-with-different-sizes)
- [Můžete použít geografickou replikaci s aktivovaným clusteringem?](#can-i-use-geo-replication-with-clustering-enabled)
- [Můžete použít geografickou replikaci s Moje mezipamětí ve virtuální síti?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Co je plán replikace pro Redis geografické replikace?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Jak dlouho replikace geografické replikace trvá?](#how-long-does-geo-replication-replication-take)
- [Je zaručeno bod obnovení replikace](#is-the-replication-recovery-point-guaranteed)
- [Můžete použít PowerShell nebo rozhraní příkazového řádku Azure ke správě geografické replikace?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Kolik stojí Moje data replikovat napříč několika oblastmi Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Proč operace selhání při pokusu odstranit mé propojené mezipaměti?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Jaké oblasti použít pro sekundární mezipaměť propojené?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Jak funguje přebírání služeb při selhání sekundární propojené mezipaměti](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Můžete použít geografickou replikaci s mezipamětí úroveň Standard nebo Basic?

Ne, geografická replikace je k dispozici pouze pro mezipaměť na úrovni Premium.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>Je k dispozici pro použití mezipaměť během procesu propojení nebo odpojení?

- Při propojování, primární propojené mezipaměti zůstává k dispozici, zatímco se dokončují proces propojení.
- Při propojování, sekundární propojené mezipaměti není k dispozici, dokud se nedokončí proces propojení.
- Při rušení propojení, obě mezipamětí zůstanou k dispozici při dokončení procesu zrušení propojení.

### <a name="can-i-link-more-than-two-caches-together"></a>Můžete propojit více než dva mezipaměti najednou?

Ne, lze propojit pouze dva mezipamětí společně.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Můžete propojit dvě mezipaměti z různých předplatných Azure?

Ne, obě mezipaměti musí být ve stejném předplatném Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Můžete propojit dvě mezipaměti s jinou velikostí?

Ano, dokud sekundární propojené mezipaměti je větší než primární propojené mezipaměti.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Můžete použít geografickou replikaci s aktivovaným clusteringem?

Ano, jakož i mezipaměti má stejný počet horizontálních oddílů.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Můžete použít geografickou replikaci s Moje mezipamětí ve virtuální síti?

Ano, je podporováno georeplikace mezipamětí ve virtuálních sítích s omezením:

- Geografická replikace mezi mezipamětí ve stejné virtuální síti se nepodporuje.
- Geografická replikace mezi mezipamětí v různých virtuálních sítích je také podporována.
  - Pokud virtuální sítě nacházejí ve stejné oblasti, můžete je propojit pomocí [VNET peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) nebo [připojení brány VPN typu VNET-to-VNET](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V).
  - Pokud se virtuálních sítí v různých oblastech, geografickou replikaci pomocí virtuální sítě partnerských vztahů není podporována z důvodu omezení s základní interní služby load balancer. Další informace o omezení partnerských vztahů virtuálních sítí najdete v tématu [- partnerský vztah – požadavky a omezení](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints). Použití připojení brány VPN typu VNET-to-VNET je doporučené řešení.

Pomocí [šabloně Azure](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/), můžete rychle nasadit dvěma mezipamětí geograficky replikované do virtuální sítě připojené k připojení brány VPN typu VNET-to-VNET.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Co je plán replikace pro Redis geografické replikace?

Replikace je nepřetržitý a asynchronní a nebude probíhat na konkrétní plán. Všechny operace zápisu na primární Hotovo okamžitě a asynchronně replikují na sekundární.

### <a name="how-long-does-geo-replication-replication-take"></a>Jak dlouho replikace geografické replikace trvá?

Replikace je přírůstkový, asynchronní a průběžné a doba trvání se výrazně liší od latence napříč oblastmi. Za určitých okolností sekundární mezipaměť může být nutné provést úplnou synchronizaci dat z primárního serveru. Doba replikace v tomto případě je závislá na řadě faktorů, jako jsou: zatížení primární mezipaměti, dostupnou šířku pásma sítě a latencí mezi oblastmi. Zjistili jsme, že čas replikace pro pár úplné 53GB geograficky replikovaného může být kdekoli mezi 5 až 10 minut.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Je zaručeno bod obnovení replikace

Pro mezipaměti v režimu geograficky replikovaného trvalost zakázána. Při nepropojené, jako je například selhání iniciovaných zákazníkem pár geograficky replikované sekundární propojené mezipaměti zajišťuje jeho synchronizace dat až do té doby. Žádný bod obnovení je zaručeno, že v takových situacích.

Získat bod obnovení [exportovat](cache-how-to-import-export-data.md#export) z obou mezipaměti. Později můžete [Import](cache-how-to-import-export-data.md#import) do primární propojené mezipaměti.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Můžete použít PowerShell nebo rozhraní příkazového řádku Azure ke správě geografické replikace?

Ano, geografická replikace je možné spravovat pomocí webu Azure portal, Powershellu nebo rozhraní příkazového řádku Azure. Další informace najdete v tématu [PowerShell docs](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) nebo [dokumentace Azure CLI](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest).

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Kolik stojí Moje data replikovat napříč několika oblastmi Azure?

Pokud používáte geografickou replikaci, data z primární propojené mezipaměti replikují do sekundární propojené mezipaměti. Neplatí žádné poplatky za přenos dat, pokud jsou dvě propojené mezipaměti ve stejné oblasti. Pokud dvě propojené mezipaměti jsou v různých oblastech, poplatky za přenos dat se náklady na výchozí přenos dat sítě data přesouvaná v jedné oblasti. Další informace najdete v tématu [podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Proč operace selhání při pokusu odstranit mé propojené mezipaměti?

Geograficky replikované mezipaměti a jejich skupiny prostředků nelze odstranit, pokud propojené, dokud neodeberete odkaz geografické replikace. Pokud se pokusíte odstranit skupinu prostředků, která obsahuje jednu nebo obě z propojené mezipaměti, dalších prostředků ve skupině prostředků se odstraní, ale zůstává skupinu prostředků v `deleting` stavu a všechny propojené mezipaměti ve skupině prostředků zůstanou v `running`stavu. Zcela odstranit skupinu prostředků a propojené mezipaměti v ní, zrušit propojení mezipamětí, jak je popsáno v [odebrat odkaz geografické replikace](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Jaké oblasti použít pro sekundární mezipaměť propojené?

Obecně se doporučuje pro mezipaměť existovat ve stejné oblasti Azure jako aplikace, která přistupuje. Pro aplikace s primární a záložní oblastech se doporučuje, že primární a sekundární mezipaměti existuje v těchto stejných oblastech. Další informace o spárovaných oblastí najdete v tématu [osvědčené postupy – Azure spárované oblasti](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Jak funguje přebírání služeb při selhání sekundární propojené mezipaměti

Automatické převzetí služeb při selhání mezi oblastmi Azure se nepodporuje pro geograficky replikovaného mezipaměti. Ve scénáři zotavení po havárii zákazníkům by měl vyvolat celý zásobník aplikací koordinovaným způsobem v jejich oblasti zálohování. Takže jednotlivé aplikace, které součásti rozhodnout při přepnutí na jejich zálohování v jejich vlastní může negativně ovlivnit výkon. Jednou z klíčových výhod Redis je, že je velmi nízkou latencí úložiště. Pokud zákazník hlavní aplikace v jiné oblasti než uloženou v mezipaměti, přidání dobu odezvy by měla znatelnému dopadu na výkon. Z tohoto důvodu jsme Vyhněte se převzetí služeb při selhání automaticky z důvodu problémů s dostupností přechodné.

Spuštění převzetí služeb při iniciovaných zákazníkem, nejprve zrušit propojení mezipamětí. Změňte vašeho klienta Redis k použití koncový bod připojení sekundární mezipaměti (dříve propojené). Když dvě mezipaměti se odpojila, sekundární mezipaměť opět regulární mezipaměti pro čtení i zápis a přijímá požadavky od klientů Redis přímo.

## <a name="next-steps"></a>Další postup

Další informace o [mezipaměti Azure Redis na úrovni Premium](cache-premium-tier-intro.md).

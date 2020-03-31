---
title: Jak nastavit geografickou replikaci pro Azure Cache for Redis | Dokumenty společnosti Microsoft
description: Zjistěte, jak replikovat instance Azure Cache for Redis napříč geografickými oblastmi.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: ce50c665fa79c361f638fda4ec373d5215c407f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129430"
---
# <a name="how-to-set-up-geo-replication-for-azure-cache-for-redis"></a>Jak nastavit geografickou replikaci pro Azure Cache pro Redis

Geografická replikace poskytuje mechanismus pro propojení dvou premium tier Azure Cache pro instance Redis. Jedna mezipaměť je vybrána jako primární propojená mezipaměť a druhá jako sekundární propojená mezipaměť. Sekundární propojená mezipaměť se stane jen pro čtení a data zapsaná do primární mezipaměti jsou replikována do sekundární propojené mezipaměti. Tuto funkci lze použít k replikaci mezipaměti napříč oblastmi Azure. Tento článek obsahuje návod na konfiguraci geografické replikace pro vaše premium vrstvy Azure Cache pro instance Redis.

## <a name="geo-replication-prerequisites"></a>Požadavky geografické replikace

Chcete-li nakonfigurovat geografickou replikaci mezi dvěma mezipamětmi, musí být splněny následující požadavky:

- Obě mezipaměti jsou mezipaměti [úrovně Premium.](cache-premium-tier-intro.md)
- Obě mezipaměti jsou ve stejném předplatném Azure.
- Sekundární propojená mezipaměť má stejnou velikost mezipaměti nebo větší velikost mezipaměti než primární propojená mezipaměť.
- Obě mezipaměti jsou vytvořeny a ve spuštěném stavu.

Některé funkce nejsou podporovány pomocí geografické replikace:

- Trvalost není podporována geografickou replikací.
- Clustering je podporován, pokud mají obě mezipaměti povoleno clustering a mají stejný počet šmejdů.
- Mezipaměti ve stejné virtuální síti jsou podporovány.
- Mezipaměti v různých virtuálních doutcích jsou podporovány s upozorněními. Další informace najdete [v tématu Můžu použít geografickou replikaci s mezipamětí ve virtuální síti?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)

Po konfiguraci geografické replikace platí pro dvojici propojených mezipamětí následující omezení:

- Sekundární propojená mezipaměť je jen pro čtení. můžete z něj číst, ale nemůžete do něj zapisovat žádná data. 
- Všechna data, která byla v sekundární propojené mezipaměti před přidáním propojení, budou odebrána. Pokud je však geografická replikace později odebrána, replikovaná data zůstanou v sekundární propojené mezipaměti.
- Nelze [škálovat](cache-how-to-scale.md) buď mezipaměti, zatímco mezipaměti jsou propojeny.
- [Počet úlomků](cache-how-to-premium-clustering.md) nelze změnit, pokud je povoleno clustering v mezipaměti.
- Nelze povolit trvalost na obou mezipaměti.
- [Exportovat](cache-how-to-import-export-data.md#export) můžete z obou mezipamětí.
- Do sekundární propojené mezipaměti nelze [importovat.](cache-how-to-import-export-data.md#import)
- Nelze odstranit propojenou mezipaměť ani skupinu prostředků, která je obsahuje, dokud neodpojíte mezipaměti. Další informace naleznete [v tématu Proč se operace při pokusu o odstranění propojené mezipaměti nepodařilo odstranit?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Pokud jsou mezipaměti v různých oblastech, náklady na odchozí síťový přenos se vztahují na data přesunutá napříč oblastmi. Další informace najdete [v tématu Kolik stojí replikace dat v rámci oblastí Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Automatické převzetí služeb při selhání nedochází mezi primární a sekundární propojené mezipaměti. Další informace a informace o tom, jak převzetí služeb při selhání klientské aplikace, naleznete v tématu [Jak funguje převzetí služeb při selhání do sekundární propojené mezipaměti?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Přidání geografického replikačního propojení

1. Chcete-li propojit dvě mezipaměti pro geografickou replikaci, klepněte z nabídky Prostředky mezipaměti, kterou chcete zacházet jako primární propojená mezipaměť, klepněte na položku **Geografická replikace.** Dále klikněte na **přidat odkaz replikace mezipaměti** z okna **geografické replikace.**

    ![Přidat odkaz](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. V seznamu Kompatibilní mezipaměti klepněte na název zamýšlené sekundární **mezipaměti.** Pokud sekundární mezipaměť není zobrazena v seznamu, ověřte, zda jsou [splněny požadavky geografické replikace](#geo-replication-prerequisites) pro sekundární mezipaměť. Chcete-li filtrovat mezipaměti podle oblasti, klepněte na oblast v mapě a zobrazte pouze ty mezipaměti v seznamu **Kompatibilní mezipaměti.**

    ![Mezipaměti kompatibilní se georeplikací](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Můžete také spustit proces propojení nebo zobrazit podrobnosti o sekundární mezipaměti pomocí kontextové nabídky.

    ![Kontextová nabídka geografické replikace](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Kliknutím na **Propojit** propojte obě mezipaměti a začněte proces replikace.

    ![Propojení mezipamětí](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Průběh procesu replikace můžete zobrazit v okně **geografické replikace.**

    ![Stav propojení](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Můžete také zobrazit stav propojení v okně **Přehled** pro primární i sekundární mezipaměti.

    ![Stav mezipaměti](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Po dokončení procesu replikace se **stav propojení** změní na **Succeeded**.

    ![Stav mezipaměti](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Primární propojená mezipaměť zůstane k dispozici pro použití během procesu propojení. Sekundární propojená mezipaměť není k dispozici, dokud není proces propojení dokončen.

## <a name="remove-a-geo-replication-link"></a>Odebrání propojení geografické replikace

1. Chcete-li odebrat propojení mezi dvěma mezipamětmi a zastavit geografickou replikaci, klepněte na tlačítko **Zrušit propojení mezipamětí** z okna **geografické replikace.**
    
    ![Zrušení propojení mezipamětí](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Po dokončení procesu odpojení je k dispozici sekundární mezipaměť pro čtení i zápisy.

>[!NOTE]
>Po odebrání propojení geografické replikace zůstanou replikovaná data z primární propojené mezipaměti v sekundární mezipaměti.
>
>

## <a name="geo-replication-faq"></a>Nejčastější dotazy ke geografické replikaci

- [Můžu použít geografickou replikaci s mezipaměť úrovně Standard nebo Basic?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [Je moje mezipaměť dostupná pro použití během procesu propojování nebo odpojování?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Mohu propojit více než dvě mezipaměti dohromady?](#can-i-link-more-than-two-caches-together)
- [Můžu propojit dvě mezipaměti z různých předplatných Azure?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Mohu propojit dvě mezipaměti s různými velikostmi?](#can-i-link-two-caches-with-different-sizes)
- [Je možné použít geografickou replikaci s povoleným clusterováním?](#can-i-use-geo-replication-with-clustering-enabled)
- [Můžu použít geografickou replikaci s mezipamětí ve Virtuální síti?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Jaký je plán replikace pro geografickou replikaci Redisu?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Jak dlouho trvá geografická replikace replikace?](#how-long-does-geo-replication-replication-take)
- [Je bod obnovení replikace zaručen?](#is-the-replication-recovery-point-guaranteed)
- [Můžu ke správě geografické replikace použít PowerShell nebo Azure CLI?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Kolik stojí replikace mých dat v rámci oblastí Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Proč se operace nepodařilo, když jsem se pokusil odstranit propojenou mezipaměť?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Jakou oblast mám použít pro sekundární propojenou mezipaměť?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Jak funguje převzetí selhání do sekundární propojené mezipaměti?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Můžu použít geografickou replikaci s mezipaměť úrovně Standard nebo Basic?

Ne, geografická replikace je k dispozici pouze pro mezipaměti úrovně Premium.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>Je moje mezipaměť dostupná pro použití během procesu propojování nebo odpojování?

- Při propojení zůstane primární propojená mezipaměť k dispozici i po dokončení procesu propojení.
- Při propojení není sekundární propojená mezipaměť k dispozici, dokud není proces propojení dokončen.
- Při odpojení zůstávají obě mezipaměti k dispozici i po dokončení procesu odpojení.

### <a name="can-i-link-more-than-two-caches-together"></a>Mohu propojit více než dvě mezipaměti dohromady?

Ne, můžete propojit pouze dvě mezipaměti dohromady.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Můžu propojit dvě mezipaměti z různých předplatných Azure?

Ne, obě mezipaměti musí být ve stejném předplatném Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Mohu propojit dvě mezipaměti s různými velikostmi?

Ano, pokud je sekundární propojená mezipaměť větší než primární propojená mezipaměť.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Je možné použít geografickou replikaci s povoleným clusterováním?

Ano, pokud obě mezipaměti mají stejný počet štřepů.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Můžu použít geografickou replikaci s mezipamětí ve Virtuální síti?

Ano, geografická replikace mezipamětí v virtuálních sítích je podporována upozorněními:

- Geografická replikace mezi mezipaměti ve stejné virtuální síti je podporována.
- Geo-replikace mezi mezipaměti v různých virtuálních sítích je také podporována.
  - Pokud virtuální sítě jsou ve stejné oblasti, můžete je připojit pomocí [partnerského vztahu virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) nebo [připojení virtuální sítě vpn brány v síti](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V).
  - Pokud virtuální sítě jsou v různých oblastech, geografická replikace pomocí partnerského vztahu virtuální sítě není podporována z důvodu omezení se základními interními vynakladači zatížení. Další informace o omezenípartnerského vztahu virtuální sítě naleznete [v tématu Virtuální síť – partnerský vztah – požadavky a omezení](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints). Doporučeným řešením je použití připojení virtuální sítě VPN Gateway k virtuální síti.

Pomocí [této šablony Azure](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)můžete rychle nasadit dvě geograficky replikované mezipaměti do virtuální sítě připojené k připojení virtuální sítě vpn gateway.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Jaký je plán replikace pro geografickou replikaci Redisu?

Replikace je nepřetržitá a asynchronní a nedochází k ní podle určitého plánu. Všechny zápisy provedené na primární jsou okamžitě a asynchronně replikovány na sekundární.

### <a name="how-long-does-geo-replication-replication-take"></a>Jak dlouho trvá geografická replikace replikace?

Replikace je přírůstková, asynchronní a souvislá a doba, která se neliší od latence napříč oblastmi. Za určitých okolností může být požadováno, aby sekundární mezipaměť provedla úplnou synchronizaci dat z primární. Doba replikace v tomto případě závisí na počtu faktorů, jako je: zatížení primární mezipaměti, dostupná šířka pásma sítě a latence mezi oblastmi. Zjistili jsme, že doba replikace pro celý 53GB geo-replikovaný pár může být kdekoli mezi 5 až 10 minut.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Je bod obnovení replikace zaručen?

U mezipamětí v geograficky replikovaném režimu je trvalosti zakázána. Pokud je geograficky replikovaná dvojice odpojena, například převzetí služeb při selhání iniciované zákazníkem, sekundární propojená mezipaměť uchovává synchronizovaná data až do tohoto okamžiku. V takových situacích není zaručen žádný bod obnovení.

Chcete-li získat bod obnovení, [Export](cache-how-to-import-export-data.md#export) z obou mezipamětí. Později můžete [importovat](cache-how-to-import-export-data.md#import) do primární propojené mezipaměti.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Můžu ke správě geografické replikace použít PowerShell nebo Azure CLI?

Ano, geografickou replikaci se dá spravovat pomocí portálu Azure, PowerShellu nebo Azure CLI. Další informace najdete v [tématu Dokumenty PowerShellu](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) nebo [Dokumenty příkazového příkazu k onomu Azure](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest).

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Kolik stojí replikace mých dat v rámci oblastí Azure?

Při použití geografické replikace jsou data z primární propojené mezipaměti replikována do sekundární propojené mezipaměti. Pokud jsou dvě propojené mezipaměti ve stejné oblasti, neplatí se za přenos dat žádný poplatek. Pokud jsou dvě propojené mezipaměti v různých oblastech, poplatek za přenos dat je náklady na odchozí přenos dat v síti, které se přesouvají v obou oblastech. Další informace naleznete v [tématu Podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Proč se operace nepodařilo, když jsem se pokusil odstranit propojenou mezipaměť?

Geograficky replikované mezipaměti a jejich skupiny prostředků nelze odstranit během propojení, dokud neodeberete propojení geografické replikace. Pokud se pokusíte odstranit skupinu prostředků, která obsahuje jednu nebo obě propojené mezipaměti, ostatní prostředky `deleting` ve skupině prostředků budou odstraněny, ale `running` skupina prostředků zůstane ve stavu a všechny propojené mezipaměti ve skupině prostředků zůstanou ve stavu. Chcete-li skupinu prostředků a propojené mezipaměti v ní zcela odstranit, zrušte propojení mezipamětí, jak je popsáno v části [Odebrat propojení geografické replikace](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Jakou oblast mám použít pro sekundární propojenou mezipaměť?

Obecně se doporučuje, aby vaše mezipaměť existovala ve stejné oblasti Azure jako aplikace, která k ní přistupuje. Pro aplikace se samostatnými primárními a záložními oblastmi se doporučuje, aby primární a sekundární mezipaměti existovaly ve stejných oblastech. Další informace o spárovaných oblastech najdete [v tématu Doporučené postupy – Spárované oblasti Azure](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Jak funguje převzetí selhání do sekundární propojené mezipaměti?

Automatické převzetí služeb při selhání v rámci oblastí Azure není podporováno pro geograficky replikované mezipaměti. Ve scénáři zotavení po havárii by zákazníci měli koordinovaně vyvolat celý zásobník aplikací v oblasti zálohování. Nechat jednotlivé součásti aplikace rozhodnout, kdy přepnout na jejich zálohy na vlastní pěst může negativně ovlivnit výkon. Jednou z klíčových výhod Redis je, že je to úložiště s velmi nízkou latencí. Pokud se hlavní aplikace zákazníka nachází v jiné oblasti než jeho mezipaměti, by přidaná doba odezvy měla znatelný dopad na výkon. Z tohoto důvodu se vyhneme převzetí automaticky převzetím služby při selhání z důvodu přechodných problémů s dostupností.

Chcete-li spustit převzetí služeb při selhání iniciované zákazníkem, nejprve odpojte mezipaměti. Potom změňte klienta Redis tak, aby používal koncový bod připojení (dříve propojené) sekundární mezipaměti. Když jsou dvě mezipaměti odpojeny, sekundární mezipaměť se znovu stane běžnou mezipamětí pro čtení a zápis a přijímá požadavky přímo od klientů Redis.

## <a name="next-steps"></a>Další kroky

Další informace o [úrovni Azure Cache for Redis Premium](cache-premium-tier-intro.md).

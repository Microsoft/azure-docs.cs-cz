---
title: Konfigurace geografické replikace pro Redis instance Premium Azure cache
description: Naučte se replikovat mezipaměť Azure pro instance Redis Premium napříč oblastmi Azure.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 8701f7bcb2e7ff705e4f1d1b401f4eb3e680f28b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501035"
---
# <a name="configure-geo-replication-for-premium-azure-cache-for-redis-instances"></a>Konfigurace geografické replikace pro Redis instance Premium Azure cache

V tomto článku se dozvíte, jak nakonfigurovat geograficky replikovanou mezipaměť Azure pomocí Azure Portal.

Odkazy na geografickou replikaci tvoří dvě mezipaměti Azure Premium pro instance Redis a vytváří vztah replikace dat. Tyto instance mezipaměti se většinou nacházejí v různých oblastech Azure, i když nejsou nutné. Jedna instance funguje jako primární a druhá jako sekundární. Primární zpracovává požadavky na čtení a zápis a šíří změny do sekundárního. Tento proces pokračuje, dokud nedojde k odebrání propojení mezi těmito dvěma instancemi.

> [!NOTE]
> Geografická replikace je navržená jako řešení zotavení po havárii.
>
>

## <a name="geo-replication-prerequisites"></a>Předpoklady geografické replikace

Chcete-li konfigurovat geografickou replikaci mezi dvěma mezipamětmi, musí být splněny následující předpoklady:

- Obě mezipaměti jsou mezipaměti [úrovně Premium](cache-overview.md#service-tiers) .
- Obě mezipaměti jsou ve stejném předplatném Azure.
- Sekundární propojená mezipaměť má buď stejnou velikost mezipaměti, nebo větší velikost mezipaměti než primární propojená mezipaměť.
- Obě mezipaměti jsou vytvořeny a ve stavu spuštěno.

U geografické replikace nejsou podporované některé funkce:

- Trvalost není u geografické replikace podporovaná.
- Clustering se podporuje, pokud jsou povolené clustery v obou mezipamětech a mají stejný počet horizontálních oddílů.
- Jsou podporovány mezipamětí ve stejné virtuální síti.
- Mezipaměti v různých virtuální sítě jsou podporovány s upozorněními. Další informace najdete v tématu [použití geografické replikace s mezipamětí ve virtuální síti?](#can-i-use-geo-replication-with-my-caches-in-a-vnet) .

Po nakonfigurování geografické replikace platí následující omezení pro váš pár propojených mezipamětí:

- Sekundární propojená mezipaměť je jen pro čtení; můžete z něj číst, ale do něj nemůžete zapisovat žádná data. 
- Všechna data, která byla v sekundární odkazované mezipaměti před přidáním odkazu, budou odebrána. Pokud je geografická replikace později odebrána, replikovaná data zůstanou v sekundární odkazované mezipaměti.
- Při propojení mezipamětí nelze [škálovat](cache-how-to-scale.md) buď mezipaměť.
- Pokud je povolená podpora clusteringu, nemůžete [změnit počet horizontálních oddílů](cache-how-to-premium-clustering.md) .
- U obou mezipamětí nemůžete povolit trvalost.
- [Exportovat](cache-how-to-import-export-data.md#export) můžete z obou mezipamětí.
- Nemůžete [importovat](cache-how-to-import-export-data.md#import) do sekundární propojené mezipaměti.
- Nemůžete odstranit buď propojenou mezipaměť, nebo skupinu prostředků, která je obsahuje, dokud zrušíte propojení mezipamětí. Další informace najdete v tématu [proč došlo k chybě operace při pokusu odstranit propojenou mezipaměť?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Pokud jsou mezipaměti v různých oblastech, účtují se náklady na výstup ze sítě na data přesunutá mezi oblasti. Další informace najdete v tématu [Kolik stojí za replikaci dat napříč oblastmi Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- K automatickému převzetí služeb při selhání nedochází mezi primární a sekundární propojenou mezipamětí. Další informace a informace o tom, jak převzít služby při selhání u klientské aplikace, najdete v tématu Jak funguje převzetí služeb při selhání [sekundární propojenou mezipamětí?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Přidání odkazu na geografickou replikaci

1. Pokud chcete propojit dvě mezipamětí pro geografickou replikaci, Fist klikněte na **geografickou replikaci** z nabídky prostředků v mezipaměti, kterou chcete použít jako primární propojenou mezipaměť. Potom v okně **geografická replikace** klikněte na **Přidat odkaz replikace mezipaměti** .

    ![Přidat odkaz](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Klikněte na název zamýšlené sekundární mezipaměti ze seznamu **kompatibilních mezipamětí** . Pokud se v seznamu nezobrazí vaše sekundární mezipaměť, ověřte, že jsou splněné [předpoklady geografické replikace](#geo-replication-prerequisites) pro sekundární mezipaměť. Pokud chcete filtrovat mezipaměti podle oblasti, klikněte na oblast v mapě, aby se zobrazily jenom mezipaměti v seznamu **kompatibilních mezipaměti** .

    ![Mezipaměti kompatibilní geografické replikace](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Můžete také spustit proces propojení nebo zobrazit podrobnosti o sekundární mezipaměti pomocí místní nabídky.

    ![Místní nabídka geografické replikace](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Kliknutím na **odkaz** propojíte dvě mezipaměti společně a zahajte proces replikace.

    ![Propojení mezipamětí](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Průběh procesu replikace můžete zobrazit v okně **geografické replikace** .

    ![Stav propojení](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Můžete také zobrazit stav propojení v okně **Přehled** pro primární i sekundární mezipaměť.

    ![Snímek obrazovky, který ukazuje, jak zobrazit stav propojení primárních a sekundárních mezipamětí.](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Po dokončení procesu replikace se **stav odkazu** změní na **úspěch**.

    ![Stav mezipaměti](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Primární propojená mezipaměť zůstává k dispozici pro použití během procesu propojování. Sekundární propojená mezipaměť není k dispozici, dokud proces propojení nedokončí.

## <a name="remove-a-geo-replication-link"></a>Odebrat odkaz na geografickou replikaci

1. Pokud chcete odebrat propojení mezi dvěma mezipamětmi a zastavit geografickou replikaci, klikněte na **Zrušit propojení mezipamětí** z okna **geografické replikace** .
    
    ![Zrušit propojení mezipamětí](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Po dokončení procesu odpojování je sekundární mezipaměť k dispozici pro čtení i zápis.

>[!NOTE]
>Po odebrání odkazu geografické replikace zůstanou replikovaná data z primární propojené mezipaměti v sekundární mezipaměti.
>
>

## <a name="geo-replication-faq"></a>Nejčastější dotazy k geografické replikaci

- [Můžu geografickou replikaci použít s mezipamětí úrovně Standard nebo Basic?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [Je moje mezipaměť k dispozici pro použití během připojování nebo odpojování procesu?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Můžu propojit více než dvě mezipaměti společně?](#can-i-link-more-than-two-caches-together)
- [Můžu propojit dvě mezipaměti z různých předplatných Azure?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Můžu propojit dvě mezipaměti s různými velikostmi?](#can-i-link-two-caches-with-different-sizes)
- [Můžu použít geografickou replikaci s povoleným clusteringem?](#can-i-use-geo-replication-with-clustering-enabled)
- [Můžu v síti VNET použít geografickou replikaci s mezipamětí?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Jaký je plán replikace pro geografickou replikaci Redis?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Jak dlouho trvá replikace geografické replikace?](#how-long-does-geo-replication-replication-take)
- [Garantuje se bod obnovení replikace?](#is-the-replication-recovery-point-guaranteed)
- [Můžu pomocí PowerShellu nebo Azure CLI spravovat geografickou replikaci?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Kolik stojí za replikaci dat napříč oblastmi Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Proč při pokusu o odstranění propojené mezipaměti došlo k chybě operace?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Jakou oblast mám použít pro moji sekundární propojenou mezipaměť?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Jak převzetí služeb při selhání sekundární propojenou mezipamětí funguje?](#how-does-failing-over-to-the-secondary-linked-cache-work)
- [Můžu nakonfigurovat bránu firewall pro geografickou replikaci?](#can-i-configure-a-firewall-with-geo-replication)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Můžu geografickou replikaci použít s mezipamětí úrovně Standard nebo Basic?

Ne, geografická replikace je dostupná jenom pro mezipaměti úrovně Premium.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>Je moje mezipaměť k dispozici pro použití během připojování nebo odpojování procesu?

- Při propojování zůstane primární propojená mezipaměť k dispozici, zatímco se proces propojení dokončí.
- Při propojování není sekundární propojená mezipaměť k dispozici, dokud proces propojení nedokončí.
- Při odpojování zůstanou v době, kdy se proces odpojování dokončí, pořád dostupná mezipaměť.

### <a name="can-i-link-more-than-two-caches-together"></a>Můžu propojit více než dvě mezipaměti společně?

Ne, propojení dvou mezipamětí lze propojit pouze dohromady.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Můžu propojit dvě mezipaměti z různých předplatných Azure?

Ne, mezipamětí musí být ve stejném předplatném Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Můžu propojit dvě mezipaměti s různými velikostmi?

Ano, pokud je sekundární propojená mezipaměť větší než primární propojená mezipaměť.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Můžu použít geografickou replikaci s povoleným clusteringem?

Ano, pokud obě mezipaměti mají stejný počet horizontálních oddílů.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Můžu v síti VNET použít geografickou replikaci s mezipamětí?

Ano, geografická replikace mezipamětí v virtuální sítě se podporuje s upozorněními:

- Geografická replikace mezi mezipamětí ve stejné virtuální síti je podporovaná.
- Je také podporována geografická replikace mezi mezipamětí v různých virtuální sítě.
  - Pokud se virtuální sítě nacházejí ve stejné oblasti, můžete je propojit pomocí [partnerského vztahu](../virtual-network/virtual-network-peering-overview.md) virtuálních sítí nebo [VPN Gateway připojení typu VNet-to-VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
  - Pokud se virtuální sítě nacházejí v různých oblastech, je geografická replikace pomocí partnerského vztahu virtuálních sítí podporovaná, ale klientský virtuální počítač ve virtuální síti 1 (region 1) nebude mít přístup k mezipaměti ve virtuální síti 2 (region 2) prostřednictvím názvu DNS z důvodu omezení se základními interními nástroji pro vyrovnávání zatížení. Další informace o omezeních partnerských vztahů virtuálních sítí najdete v tématu [Virtual Network-peer-to-požadavky a omezení](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Doporučené řešení je použití VPN Gateway připojení typu VNET-to-VNET.
  
Pomocí [této šablony Azure](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)můžete rychle nasadit dvě geograficky replikované mezipaměti do virtuální sítě připojené s VPN Gateway připojení typu VNet-to-VNet.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Jaký je plán replikace pro geografickou replikaci Redis?

Replikace je nepřetržitá a asynchronní a neprovádí se podle konkrétního plánu. Všechny zápisy provedené na primárním počítači jsou okamžitě a asynchronně replikovány na sekundární.

### <a name="how-long-does-geo-replication-replication-take"></a>Jak dlouho trvá replikace geografické replikace?

Replikace je přírůstková, asynchronní a nepřetržitá a doba trvání se neliší od latence napříč oblastmi. Za určitých okolností může být k provedení úplné synchronizace dat z primární mezipaměti nutná sekundární mezipaměť. Doba replikace v tomto případě závisí na počtu faktorů, jako je například zatížení primární mezipaměti, dostupná šířka pásma sítě a latence mezi oblastmi. Našli jsme čas replikace pro úplný pár geograficky replikovaný na 53 GB, který může být v rozmezí 5 až 10 minut.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Garantuje se bod obnovení replikace?

V případě mezipamětí v režimu geograficky replikovaných je trvalost zakázaná. Pokud je v geograficky replikovaném páru odpojování, jako je třeba převzetí služeb při selhání iniciované zákazníkem, sekundární propojená mezipaměť uchovává data synchronizovaná v daném časovém okamžiku. V takových situacích není zaručen žádný bod obnovení.

Chcete-li získat bod obnovení, [exportujte](cache-how-to-import-export-data.md#export) z obou mezipamětí. Později můžete [importovat](cache-how-to-import-export-data.md#import) do primární propojené mezipaměti.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Můžu pomocí PowerShellu nebo Azure CLI spravovat geografickou replikaci?

Ano, geografickou replikaci je možné spravovat pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure. Další informace najdete v dokumentaci k [prostředí PowerShell](/powershell/module/az.rediscache/#redis_cache) nebo v dokumentaci k rozhraní příkazového [řádku Azure CLI](/cli/azure/redis/server-link).

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Kolik stojí za replikaci dat napříč oblastmi Azure?

Při použití geografické replikace se data z primární propojené mezipaměti replikují do sekundární propojené mezipaměti. Přenosy dat se neúčtují, pokud jsou dvě propojené mezipaměti ve stejné oblasti. Pokud jsou dvě propojené mezipaměti v různých oblastech, poplatky za přenos dat jsou náklady na výstup v síti, které se pohybují v obou oblastech. Další informace najdete v tématu [Podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Proč při pokusu o odstranění propojené mezipaměti došlo k chybě operace?

Geograficky replikované mezipaměti a jejich skupiny prostředků nejde odstranit, dokud neodeberete odkaz geografické replikace. Pokud se pokusíte odstranit skupinu prostředků, která obsahuje jednu nebo obě propojené mezipaměti, odstraní se ostatní prostředky ve skupině prostředků, ale skupina prostředků zůstane ve `deleting` stavu a všechny propojené mezipaměti ve skupině prostředků zůstanou ve `running` stavu. Pokud chcete úplně odstranit skupinu prostředků a propojenou mezipaměť, zrušte propojení mezipamětí, jak je popsáno v tématu [Odebrání odkazu na geografickou replikaci](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Jakou oblast mám použít pro moji sekundární propojenou mezipaměť?

Obecně se doporučuje, aby vaše mezipaměť existovala ve stejné oblasti Azure jako aplikace, která k ní přistupuje. Pro aplikace s oddělenými primárními a záložními oblastmi doporučujeme, aby vaše primární a sekundární mezipaměť existovaly v těchto oblastech. Další informace o spárovaných oblastech najdete v tématu [osvědčené postupy – spárované oblasti Azure](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Jak převzetí služeb při selhání sekundární propojenou mezipamětí funguje?

Automatické převzetí služeb při selhání napříč oblastmi Azure se u geograficky replikovaných mezipamětí nepodporuje. Ve scénáři zotavení po havárii by zákazníci měli v oblasti zálohování zařadit celý zásobník aplikace koordinovaným způsobem. Pokud chcete, aby se jednotlivé součásti aplikace rozhodly, kdy přepnout na vlastní zálohy, můžou mít negativní vliv na výkon. Jednou z klíčových výhod Redis je, že se jedná o velmi nízkou latenci. Pokud je hlavní aplikace zákazníka v jiné oblasti než její mezipaměť, přidaná doba odezvy bude mít znatelný dopad na výkon. Z tohoto důvodu se vyhnete převzetí služeb při selhání automaticky kvůli problémům s přechodnou dostupností.

Pokud chcete zahájit převzetí služeb při selhání iniciované zákazníkem, nejdřív odpojte mezipaměti. Pak změňte svého klienta Redis na použití koncového bodu připojení (dříve propojené) sekundární mezipaměti. Pokud jsou dvě mezipaměti odpojování, sekundární mezipaměť se znovu vytvoří do běžné mezipaměti pro čtení a zápis a přijímá požadavky přímo od klientů Redis.

### <a name="can-i-configure-a-firewall-with-geo-replication"></a>Můžu nakonfigurovat bránu firewall pro geografickou replikaci?

Ano, můžete nakonfigurovat [bránu firewall](./cache-configure.md#firewall) pomocí geografické replikace. Aby geografická replikace fungovala společně s bránou firewall, zajistěte, aby se IP adresa sekundární mezipaměti přidala do pravidel brány firewall primární mezipaměti.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o funkcích Azure cache pro Redis.

* [Mezipaměť Azure pro úrovně služeb Redis](cache-overview.md#service-tiers)
* [Vysoká dostupnost pro Azure cache pro Redis](cache-high-availability.md)

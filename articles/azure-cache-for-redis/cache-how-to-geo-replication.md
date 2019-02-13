---
title: Konfigurace geografické replikace pro Azure Cache pro Redis | Dokumentace Microsoftu
description: Zjistěte, jak replikovat napříč zeměpisnými oblastmi Azure Cache pro instance Redis.
services: azure-cache-for-redis
documentationcenter: ''
author: yegu-ms
manager: cfowler
editor: ''
ms.assetid: 375643dc-dbac-4bab-8004-d9ae9570440d
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: yegu
ms.openlocfilehash: d2f01c700875a33ca63a1c30d5aca4934d412d18
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56113664"
---
# <a name="how-to-configure-geo-replication-for-azure-cache-for-redis"></a>Konfigurace geografické replikace pro Azure Cache pro Redis

Geografická replikace poskytuje mechanismus pro propojení dvou úroveň Premium mezipaměti Azure pro instance Redis. Jeden mezipaměti je určený jako primární propojené mezipaměti a druhý jako sekundární propojené mezipaměti. Propojené sekundární mezipaměť bude jen pro čtení a data zapsaná do primární mezipaměti se replikuje do sekundární propojené mezipaměti. Tuto funkci je možné replikovat do mezipaměti v různých oblastech Azure. Tento článek obsahuje pokyny pro konfigurace geografické replikace pro Premium úroveň mezipaměti Azure pro instance Redis.

## <a name="geo-replication-prerequisites"></a>Požadavky na geografickou replikaci

Konfigurace geografické replikace mezi dvěma mezipaměti, musí být splněny následující požadavky:

- Musí být obě mezipamětí [úroveň Premium](cache-premium-tier-intro.md) ukládá do mezipaměti.
- Obě mezipaměti musí být ve stejném předplatném Azure.
- Sekundární propojené mezipaměti musí být na stejné cenové úrovni nebo na vyšší cenovou úroveň než primární propojené mezipaměti.
- Pokud primární propojené mezipaměti aktivovaným clusteringem, sekundární propojené mezipaměti musí mít stejný počet horizontálních oddílů jako primární propojené mezipaměti povoleným clusteringem.
- Obě mezipaměti musí být vytvořená a v běžícím stavu.
- Trvalost nesmí být povoleno buď mezipaměť App Fabric.
- Geografická replikace mezi mezipamětí ve stejné virtuální síti se nepodporuje. 
- Geografická replikace mezi mezipamětí v partnerských virtuálních sítích ve stejné oblasti je aktuálně ve verzi preview funkce. Dané dvě virtuální sítě je potřeba nakonfigurovat tak, že jsou navzájem spojit prostřednictvím připojení TCP se prostředky v dané virtuální sítě.
- Geografická replikace mezi mezipamětí v partnerských virtuálních sítích v různých oblastech se zatím nepodporuje, ale budou brzy ve verzi preview.

Po dokončení konfigurace geografické replikace vaše propojené mezipaměti pár platí následující omezení:

- Sekundární propojené mezipaměti je jen pro čtení; může číst z něj, ale nemohou do ní zapisovat žádná data. 
- Je odebraná všechna data, která byla v sekundární propojené mezipaměti před přidáním odkazu. Pokud však je následně odstraněny geografickou replikaci, replikovaná data zůstanou v sekundární propojené mezipaměti.
- Nemůžete zahájit [operace škálování](cache-how-to-scale.md) buď je mezipaměť nebo [změnit počet horizontálních dělení](cache-how-to-premium-clustering.md) Pokud mezipaměť obsahuje povoleným clusteringem.
- Nelze zapnout stálost buď mezipaměť App Fabric.
- Můžete použít [exportovat](cache-how-to-import-export-data.md#export) s buď mezipaměti, ale můžete jenom [Import](cache-how-to-import-export-data.md#import) do primární propojené mezipaměti.
- Nelze odstranit propojené mezipaměti nebo skupinu prostředků, které obsahuje, dokud neodeberete odkaz geografické replikace. Další informace najdete v tématu [proč operace nezdaří při pokusu odstranit mé propojené mezipaměti?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Pokud dvě mezipaměti jsou v různých oblastech, náklady na celkový výstup sítě platit pro data replikovat napříč oblastmi na sekundární propojené mezipaměti. Další informace najdete v tématu [kolik to stojí Moje data replikovat napříč několika oblastmi Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Neexistuje žádné automatické převzetí služeb při selhání do sekundární propojené mezipaměti, pokud primární mezipaměti (a její repliky) přejděte. V pořadí převzetí služeb při selhání klientským aplikacím musíte ručně odebrat odkaz geografické replikace a bodu klientské aplikace do mezipaměti, který byl dříve sekundární propojené mezipaměti. Další informace najdete v tématu [jak funguje přebírání služeb při selhání sekundární propojené mezipaměti?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Přidat odkaz geografické replikace

1. Chcete-li propojit dvě úrovně premium mezipaměti pro geografickou replikaci, klikněte na tlačítko **geografické replikace** z nabídky prostředků v mezipaměti určený jako primární propojené mezipaměti a potom klikněte na **přidat propojení replikace mezipaměti** z **geografickou replikaci** okno.

    ![Přidat odkaz](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Klikněte na název požadovaného sekundární mezipaměť z **kompatibilní mezipaměti** seznamu. Pokud požadované mezipaměti se v seznamu nezobrazí, zkontrolujte, zda [požadavky na geografickou replikaci](#geo-replication-prerequisites) pro určité sekundární mezipaměti jsou splněny. Chcete-li filtrovat mezipaměti podle oblasti, klikněte na požadované oblasti na mapě zobrazíte pouze mezipaměti v **kompatibilní mezipaměti** seznamu.

    ![Kompatibilní mezipaměti geografické replikace](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Můžete také zahájit proces propojení nebo zobrazení podrobností o sekundární mezipaměť pomocí místní nabídky.

    ![Místní nabídka geografické replikace](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Klikněte na tlačítko **odkaz** propojit dvě mezipaměti a zahájí proces replikace.

    ![Propojení mezipamětí](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Můžete zobrazit průběh procesu replikace na **geografickou replikaci** okno.

    ![Stav propojení](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Můžete také zobrazit stav propojení na **přehled** okno pro primární a sekundární mezipaměti.

    ![Stav mezipaměti](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Po dokončení procesu replikace **propojit stav** změny **Succeeded**.

    ![Stav mezipaměti](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Během procesu vytváření odkazů primární propojené mezipaměti zůstává k dispozici pro použití, ale sekundární propojená mezipaměť není k dispozici, dokud se nedokončí proces propojení.

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

- Při propojení dvou mezipaměti pro geografickou replikaci, zůstávají dostupné k použití primární propojené mezipaměti, ale sekundární propojená mezipaměť není k dispozici, dokud se nedokončí proces propojení.
- Při odebírání odkaz geografické replikace mezi dvěma mezipamětí, zůstávají dostupné k použití obou mezipamětí.

### <a name="can-i-link-more-than-two-caches-together"></a>Můžete propojit více než dva mezipaměti najednou?

Ne, při použití geografické replikace lze propojit pouze dva mezipamětí společně.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Můžete propojit dvě mezipaměti z různých předplatných Azure?

Ne, obě mezipaměti musí být ve stejném předplatném Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Můžete propojit dvě mezipaměti s jinou velikostí?

Ano, dokud sekundární propojené mezipaměti je větší než primární propojené mezipaměti.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Můžete použít geografickou replikaci s aktivovaným clusteringem?

Ano, jakož i mezipaměti má stejný počet horizontálních oddílů.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Můžete použít geografickou replikaci s Moje mezipamětí ve virtuální síti?

Ano, jsou podporovány georeplikace mezipamětí ve virtuálních sítích. 

- Geografická replikace mezi mezipamětí ve stejné virtuální síti se nepodporuje.
- Geografická replikace mezi mezipamětí v různých virtuálních sítích je také podporována, tak dlouho, dokud dvě virtuální sítě jsou nakonfigurovány tak, že jsou navzájem spojit prostřednictvím připojení TCP se prostředky v dané virtuální sítě.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Co je plán replikace pro Redis geografické replikace?

Replikace neproběhne ani u určitého plánu, je nepřetržitý a asynchronní tj všechny operace zápisu na primární Hotovo hned. už nechtějí asynchronně replikují na sekundární.

### <a name="how-long-does-geo-replication-replication-take"></a>Jak dlouho replikace geografické replikace trvá?

Replikace je asynchronní a průběžné přírůstkové a doba trvání není obvykle mnohem liší od latence napříč oblastmi. Za určitých okolností může v určitých časech sekundární může být nutné provést úplnou synchronizaci dat z primárního serveru. Doba replikace v tomto případě je závislá na řadě faktorů, jako jsou: zátěž primární mezipaměti šířky pásma v počítači mezipaměti k dispozici mimo oblast latence atd. Jako příklad, na základě některých testování, které jsme našli tento čas replikace pro úplné 53 GB geograficky replikovaného spárujte východě USA a oblasti západní USA mohou být mezi 5 až 10 minut.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Je zaručeno bod obnovení replikace

V současné době pro mezipaměti v režimu geograficky replikovaného trvalosti a import/export zakázána. Takže v případě, že zákazníka iniciované převzetí služeb při selhání nebo v případech, kdy replikační připojení bylo přerušeno dvojici geograficky replikované sekundární zachová v paměti data, která je synchronizovaná z primárního serveru až do té doby. Neexistuje žádná záruka bodu obnovení k dispozici v těchto situacích.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Můžete použít PowerShell nebo rozhraní příkazového řádku Azure ke správě geografické replikace?

V tuto chvíli můžete spravovat pouze geografickou replikaci s použitím webu Azure portal.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Kolik stojí Moje data replikovat napříč několika oblastmi Azure?

Pokud používáte geografickou replikaci, data z primární propojené mezipaměti replikují do sekundární propojené mezipaměti. Pokud dvě propojené mezipaměti jsou ve stejné oblasti Azure, neplatí žádné poplatky za přenos dat. Pokud dvě propojené mezipaměti jsou v různých oblastech Azure, se poplatky za přenos dat geografické replikace náklady na šířku pásma replikace těchto dat do jiné oblasti Azure. Další informace najdete v tématu [podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Proč operace selhání při pokusu odstranit mé propojené mezipaměti?

Když dva mezipaměti jsou spojeny dohromady, nelze odstranit mezipaměti nebo skupinu prostředků, který je obsahuje, dokud neodeberete odkaz geografické replikace. Pokud se pokusíte odstranit skupinu prostředků, která obsahuje jednu nebo obě z propojené mezipaměti, dalších prostředků ve skupině prostředků se odstraní, ale zůstává skupinu prostředků v `deleting` stavu a všechny propojené mezipaměti ve skupině prostředků zůstanou v `running`stavu. A dokončete odstranění skupiny prostředků a propojené mezipaměti v ní, zrušit propojení geografickou replikaci, jak je popsáno v [odebrat odkaz geografické replikace](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Jaké oblasti použít pro sekundární mezipaměť propojené?

Obecně se doporučuje pro mezipaměť existovat ve stejné oblasti Azure jako aplikace, která přistupuje. Pokud má vaše aplikace primární a záložní oblast, primární a sekundární mezipaměti by měla existovat v těchto stejných oblastech. Další informace o spárovaných oblastí najdete v tématu [osvědčené postupy – Azure spárované oblasti](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Jak funguje přebírání služeb při selhání sekundární propojené mezipaměti

V počáteční verzi geografickou replikaci mezipaměti Azure Redis nepodporuje automatické převzetí služeb při selhání v různých oblastech Azure. Geografická replikace se používá především na scénář zotavení po havárii. Ve scénáři zotavení distater by měl zákazníkům otevřete celé aplikace zásobníku v zálohování oblasti koordinované, místo aby jednotlivé součásti aplikace při rozhodování, kdy chcete přepnout na jejich zálohy na své vlastní. To je obzvláště důležité pro Redis. Jednou z klíčových výhod Redis je, že je velmi nízkou latencí úložiště. Pokud Redis používaný aplikací k převezme služby při selhání do jiné oblasti Azure, ale nikoli výpočetní vrstvě, doby odezvy přidání by měla znatelnému dopadu na výkon. Z tohoto důvodu rádi bychom, aby selhání Redis přes automaticky z důvodu problémů s dostupností přechodné.

V současné době k zahájení převzetí služeb při selhání, musíte odebrat odkaz geografické replikace na portálu Azure portal a potom změňte koncový bod připojení klienta Redis z primární propojené mezipaměti na sekundární mezipaměti (dříve propojené). Pokud nejsou přidruženy k dvě mezipamětí, repliky opět regulární mezipaměti pro čtení i zápis a přijímá požadavky od klientů Redis přímo.


## <a name="next-steps"></a>Další postup

Další informace o [mezipaměti Azure Redis na úrovni Premium](cache-premium-tier-intro.md).


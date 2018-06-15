---
title: Postup konfigurace geografická replikace pro Azure Redis Cache | Microsoft Docs
description: Zjistěte, jak replikovat vaše instance služby Azure Redis Cache pomocí zeměpisné oblasti.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 375643dc-dbac-4bab-8004-d9ae9570440d
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: wesmc
ms.openlocfilehash: 883683f6af7943fa4da49095c9a15aefd5cfa719
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2018
ms.locfileid: "27911366"
---
# <a name="how-to-configure-geo-replication-for-azure-redis-cache"></a>Postup konfigurace geografická replikace pro Azure Redis Cache

Geografická replikace poskytuje mechanismus pro propojení dvě instance vrstvy Azure Redis Cache Premium. Jeden mezipaměti je určený jako primární propojené mezipaměti a druhý jako sekundární propojené mezipaměti. Sekundární propojené mezipaměti se stane, jen pro čtení a data zapsat do mezipaměti. primární se replikují do sekundární propojené mezipaměti. Tato funkce slouží k replikaci mezipaměti mezi oblastmi Azure. Tento článek poskytuje návod ke konfiguraci geografická replikace pro vaše instance služby Azure Redis Cache Premium vrstvy.

## <a name="geo-replication-prerequisites"></a>Geografická replikace požadavky

Ke konfiguraci geografická replikace mezi dvěma mezipaměti, musí být splněny následující požadavky:

- Musí být obě mezipamětí [úroveň Premium](cache-premium-tier-intro.md) ukládá do mezipaměti.
- Obě mezipaměti musí být ve stejném předplatném Azure.
- Sekundární propojené mezipaměti musí být stejné cenová úroveň nebo větší cenovou úroveň než primární propojené mezipaměti.
- Pokud primární propojené mezipaměti povoleným clusteringem, musí mít sekundární mezipaměti propojené s stejný počet horizontálních oddílů jako primární propojené mezipaměti povoleným clusteringem.
- Obě mezipaměti musí být vytvořený a ve spuštěném stavu.
- U buď mezipaměti nesmí být povolena trvalost.
- Geografická replikace mezi mezipamětí ve stejné virtuální síti je podporována. Geografická replikace mezi mezipaměti v různých virtuálních sítí je také podporována, dvě virtuální sítě jsou nakonfigurovány tak, že prostředky ve virtuální sítě jsou vzájemně dosažitelné prostřednictvím připojení TCP.

Po dokončení konfigurace geografická replikace vaší propojené mezipaměti pár platí následující omezení:

- Sekundární propojené mezipaměť je jen pro čtení; si můžete přečíst z něj, ale nemohou do ní zapisovat žádná data. 
- Odeberou se všechna data, která byla v sekundární propojené mezipaměti před přidáním odkazu. Pokud ale je následně odstraněny geografickou replikaci, zůstává replikovaná data v sekundární propojené mezipaměti.
- Nelze inicializovat, [škálování operaci](cache-how-to-scale.md) na buď mezipaměti nebo [změnit počet horizontálních oddílů](cache-how-to-premium-clustering.md) když mezipaměť obsahuje povoleným clusteringem.
- Nelze zapnout stálost na buď mezipaměti.
- Můžete použít [exportovat](cache-how-to-import-export-data.md#export) s buď mezipaměti, ale můžete pouze [Import](cache-how-to-import-export-data.md#import) do primární propojené mezipaměti.
- Nelze odstranit propojené mezipaměti nebo skupinu prostředků, která obsahuje, dokud neodeberete odkaz geografická replikace. Další informace najdete v tématu [proč operace nezdaří při pokusu odstranit mé propojené mezipaměti?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Pokud dva mezipamětí jsou v různých oblastech, náklady na celkový výstup sítě bude použita pro data replikovat přes oblasti sekundární propojené mezipaměti. Další informace najdete v tématu [kolik stojí k replikaci dat mezi oblastech Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Neexistuje žádné automatické převzetí služeb při selhání do sekundárního propojené mezipaměti, pokud primární mezipaměti (a její replika) přejděte. V pořadí pro převzetí služeb při selhání klientské aplikace musíte ručně odebrat odkaz geografická replikace a přejděte do mezipaměti, který byl dříve sekundární propojené mezipaměti klientské aplikace. Další informace najdete v tématu [jak funguje převzetí služeb při selhání do sekundárního propojené mezipaměti?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Přidat odkaz geografická replikace

1. Pokud chcete propojit dvě prémiových mezipamětí společně za geografickou replikaci, klikněte na tlačítko **geografická replikace** z nabídky prostředků mezipaměti určený jako primární propojené mezipaměti a pak klikněte na tlačítko **odkaz replikace mezipaměti přidat** z **geografická replikace** okno.

    ![Přidání odkazu](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Klikněte na název požadované sekundární mezipaměti z **kompatibilní mezipamětí** seznamu. Pokud se v seznamu nezobrazí, požadované mezipaměti, ověřte, zda [požadavky geografická replikace](#geo-replication-prerequisites) pro požadované mezipaměti sekundární jsou splněny. Chcete-li filtrovat mezipamětí podle oblasti, klikněte na požadovanou oblast v mapě zobrazíte pouze mezipaměti v **kompatibilní mezipamětí** seznamu.

    ![Geografická replikace kompatibilní mezipaměti](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Můžete také zahájit proces propojení nebo zobrazit podrobnosti o sekundární mezipaměti pomocí místní nabídky.

    ![Geografická replikace kontextové nabídky](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Klikněte na tlačítko **odkaz** k propojení dvou mezipamětí společně a zahájit proces replikace.

    ![Ukládá do mezipaměti odkaz](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Průběh procesu replikace můžete zobrazit na **geografická replikace** okno.

    ![Stav propojení](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Můžete také zobrazit spojující stavu na **přehled** okno pro primární a sekundární mezipamětí.

    ![Stav mezipaměti](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Po dokončení procesu replikace **propojit stav** změny **úspěšné**.

    ![Stav mezipaměti](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Během procesu vytváření primární propojené mezipaměti zůstává k dispozici pro použití ale sekundární propojené mezipaměti není k dispozici, dokud se nedokončí proces propojení.

## <a name="remove-a-geo-replication-link"></a>Odebrat odkaz geografická replikace

1. Odebere propojení mezi dvěma mezipaměti nebo zastavit geografická replikace, klikněte na tlačítko **zrušit propojení mezipamětí** z **geografická replikace** okno.
    
    ![Zrušit propojení mezipamětí](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Po dokončení procesu zrušení propojení sekundární mezipaměť je k dispozici pro čtení i zápisy.

>[!NOTE]
>Pokud je odebrán odkaz geografická replikace, replikovaných dat z primární propojené mezipaměti zůstává v sekundární mezipaměti.
>
>

## <a name="geo-replication-faq"></a>Geografická replikace – nejčastější dotazy

- [Můžete použít geografická replikace s mezipamětí úroveň Standard nebo Basic?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [Je k dispozici pro použití Moje mezipaměti během procesu vytváření nebo zrušení propojení?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Můžete propojit více než dva mezipamětí společně?](#can-i-link-more-than-two-caches-together)
- [Můžete propojit dvě mezipaměti z různých předplatných Azure?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Můžete propojit dvě mezipaměti s jinou velikostí?](#can-i-link-two-caches-with-different-sizes)
- [Můžete použít geografická replikace s povoleným clusteringem?](#can-i-use-geo-replication-with-clustering-enabled)
- [Můžete použít geografická replikace s Moje mezipamětí ve virtuální síti?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Co je plán replikace pro Redis geografická replikace?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Jak dlouho geografická replikace replikace trvá?](#how-long-does-geo-replication-replication-take)
- [Vytvoření bodu obnovení replikace záruku, že se?](#is-the-replication-recovery-point-guaranteed)
- [Můžete použít PowerShell nebo rozhraní příkazového řádku Azure ke správě geografická replikace?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Kolik stojí replikovat data mezi oblastech Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Proč operace nezdaří při pokusu odstranit mé propojené mezipaměti?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Jaké oblasti je vhodné použít pro moje sekundární propojené mezipaměti?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Jak funguje převzetí služeb při selhání do sekundárního propojené mezipaměti?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Můžete použít geografická replikace s mezipamětí úroveň Standard nebo Basic?

Ne, geografická replikace je k dispozici pouze pro prémiových mezipamětí vrstvy.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>Je k dispozici pro použití Moje mezipaměti během procesu vytváření nebo zrušení propojení?

- Při propojení dvou mezipamětí společně za geografickou replikaci, zůstane primární propojené mezipaměti k dispozici pro použití ale sekundární propojené mezipaměti není k dispozici, dokud se nedokončí proces propojení.
- Při odebírání geografická replikace propojení mezi dvěma mezipamětí, zůstávají dostupné k použití obou mezipamětí.

### <a name="can-i-link-more-than-two-caches-together"></a>Můžete propojit více než dva mezipamětí společně?

Ne, při použití geografická replikace můžete propojit pouze dvě mezipamětí společně.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Můžete propojit dvě mezipaměti z různých předplatných Azure?

Ne, obě mezipaměti musí být ve stejném předplatném Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Můžete propojit dvě mezipaměti s jinou velikostí?

Ano, tak dlouho, dokud sekundární propojené mezipaměti je větší než primární propojené mezipaměti.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Můžete použít geografická replikace s povoleným clusteringem?

Ano, tak dlouho, dokud obě mezipamětí mít stejný počet horizontálních oddílů.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Můžete použít geografická replikace s Moje mezipamětí ve virtuální síti?

Ano, jsou podporovány geografická replikace mezipamětí ve virtuálních sítí. 

- Geografická replikace mezi mezipamětí ve stejné virtuální síti je podporována.
- Geografická replikace mezi mezipaměti v různých virtuálních sítí je také podporována, dvě virtuální sítě jsou nakonfigurovány tak, že prostředky ve virtuální sítě jsou vzájemně dosažitelné prostřednictvím připojení TCP.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Co je plán replikace pro Redis geografická replikace?

Replikace se neodehrává na konkrétním plánem, je nepřetržitý a asynchronní jednofaktorovému replikují všech zápisů provádí na primární na sekundární se okamžitě asynchronně.

### <a name="how-long-does-geo-replication-replication-take"></a>Jak dlouho geografická replikace replikace trvá?

Replikace je přírůstkové, asynchronní a souvislých a doba se obvykle mnohem neliší od latence v oblastech. Za určitých okolností v určitých časech sekundární může být nutné provést úplnou synchronizaci dat z primární. Čas replikace v tomto případě je závislá na mnoha faktorech, jako je: zatížení primární mezipaměti šířky pásma, které jsou k dispozici na počítači mezipaměti mimo oblast latence atd. Jako příklad podle některé testování, které se této doby replikace pro úplnou našli jsme 53 GB geograficky replikované spárujte východě nám a oblasti západní USA mohou být mezi 5 až 10 minut.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Vytvoření bodu obnovení replikace záruku, že se?

V současné době pro mezipaměti v režimu geograficky replikované vypnutá funkce trvalosti a importu a exportu. Proto v případě, že zákazníka inicializovat převzetí služeb při selhání nebo v případech, kde bylo přerušeno mezi geograficky replikované pár odkaz replikace, sekundární zachovají paměti data, která má synchronizované z primární až do té doby. Není zaručeno bodu obnovení, zadaný v takových situacích.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Můžete použít PowerShell nebo rozhraní příkazového řádku Azure ke správě geografická replikace?

V tuto chvíli můžete spravovat pouze geografická replikace pomocí portálu Azure.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Kolik stojí replikovat data mezi oblastech Azure?

Pokud používáte geografická replikace, data z primární propojené mezipaměti se replikují do sekundární propojené mezipaměti. Pokud jsou dvě propojené mezipamětí ve stejné oblasti Azure, je bezplatná pro přenos dat. Pokud dvě propojené mezipaměti v různých oblastech Azure, je zdarma přenos dat geografické replikace náklady na šířku pásma replikace dat oblasti Azure. Další informace najdete v tématu [podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Proč operace nezdaří při pokusu odstranit mé propojené mezipaměti?

Při dvou mezipamětí jsou spojeny dohromady, nelze odstranit mezipaměti nebo skupinu prostředků, která je obsahuje, dokud neodeberete odkaz geografická replikace. Pokud se pokusíte odstranit skupinu prostředků, který obsahuje jedno nebo obě propojené mezipamětí, další prostředky ve skupině prostředků se odstraní, ale zůstává skupině prostředků v `deleting` stavu a všechny propojené mezipamětí ve skupině prostředků zůstat v `running` stavu. Pokud chcete provést odstranění skupiny prostředků a propojené mezipaměti v něm, zrušit propojení geografická replikace, jak je popsáno v [odebrat odkaz geografická replikace](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Jaké oblasti je vhodné použít pro moje sekundární propojené mezipaměti?

Obecně se doporučuje pro mezipaměť existovat ve stejné oblasti Azure jako aplikace, která se přistupuje. Pokud vaše aplikace má primární a záložní oblast, musí existovat své mezipaměti primární a sekundární v těchto oblastech stejné. Další informace o spárované oblastech najdete v tématu [osvědčené postupy – Azure spárované oblasti](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Jak funguje převzetí služeb při selhání do sekundárního propojené mezipaměti?

V první verzi systému geografická replikace Azure Redis Cache automatické převzetí služeb při selhání v oblastech Azure nepodporuje. Geografická replikace se používá hlavně ve scénáři zotavení po havárii. Ve scénáři zotavení distater by měl v koordinované spíše než umožníte jednotlivé součásti aplikace při rozhodování, kdy přepnout do své zálohy na své vlastní zákazníkům zprovoznit celý zásobník aplikací v oblasti zálohování. To je obzvláště důležité pro Redis. Jedním z klíčových výhod Redis je, že se jedná o velmi nízkou latencí úložiště. Pokud Redis používaný aplikací k převezme v jiné oblasti Azure, ale nemá výpočetní vrstvě, doba odezvy přidané by mít znatelný dopad na výkon. Z tohoto důvodu jsme přeje, aby se zabránilo selhání Redis přes automaticky z důvodu problémů s dostupností přechodný.

V současné době zahájíte převzetí služeb při selhání, musíte odebrat odkaz geografická replikace na portálu Azure a poté změňte koncový bod připojení v klientovi Redis z primární propojené mezipaměti (dříve propojené) sekundární mezipaměti. Pokud nejsou přidruženy k dvě mezipamětí, replika se stane regulární mezipaměti pro čtení a zápis znovu a přijímá požadavky přímo z klientů Redis.


## <a name="next-steps"></a>Další postup

Další informace o [Azure Redis Cache na úrovni Premium](cache-premium-tier-intro.md).


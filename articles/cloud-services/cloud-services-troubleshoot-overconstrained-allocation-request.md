---
title: Řešení potíží s OverconstrainedAllocationRequest při nasazení cloudové služby (Classic) do Azure | Microsoft Docs
description: Tento článek popisuje, jak vyřešit výjimku OverconstrainedAllocationRequest při nasazení cloudové služby (Classic) do Azure.
services: cloud-services
documentationcenter: ''
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 1b50ded166b3f62b38830b4c2d18da7c4c4f0d35
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744852"
---
# <a name="troubleshoot-overconstrainedallocationrequest-when-deploying-cloud-services-classic-to-azure"></a>Řešení potíží s OverconstrainedAllocationRequest při nasazení Cloud Services (Classic) do Azure

V tomto článku budete řešit problémy s omezenými alokacemi, které zabraňují nasazení Cloud Services Azure (Classic).

Když nasadíte instance do cloudové služby nebo přidáte nové instance webových nebo pracovních rolí, Microsoft Azure přiděluje výpočetní prostředky.

Během těchto operací může občas docházet k chybám, i když jste dosáhli limitu předplatného Azure.

> [!TIP]
> Tyto informace mohou být užitečné také při plánování nasazení služeb.

## <a name="symptom"></a>Příznak

![Image zobrazuje okno protokol operací (Classic).](./media/cloud-services-troubleshoot-overconstrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

|Typ výjimky  |Chybová zpráva  |
|---------|---------|
|OverconstrainedAllocationRequest |Velikost virtuálního počítače (nebo kombinace velikostí virtuálních počítačů) vyžadovaných tímto nasazením nejde zřídit kvůli omezením požadavků na nasazení. Pokud je to možné, vyzkoušejte požadavků omezení, jako jsou vazby virtuální sítě, nasazení na hostovanou službu bez dalšího nasazení v ní a jiné skupiny vztahů nebo bez skupiny vztahů, nebo zkuste nasazení nasadit do jiné oblasti.|

## <a name="cause"></a>Příčina

Hlavní příčina se liší, pokud je cloudová služba **připnutá** nebo **není připnutá**.

- [**Nepřipojeno:** Chyby při prvním nasazení nové cloudové služby (Classic)](#not-pinned-to-a-cluster)
- [**Připnuté:** Chyby z existující cloudové služby (klasické)](#pinned-to-a-cluster)

> [!NOTE]
> Při nasazení první instance do cloudové služby (v přípravné nebo produkční) se tato cloudová služba připnula ke clusteru.
>
> V průběhu času se prostředky v clusteru můžou plně využívat. Pokud cloudová služba (Classic) vytvoří požadavek na přidělení dalších prostředků v případě, že v připnutých clusterech není k dispozici dostatek prostředků, bude mít tato žádost [selhání přidělení](cloud-services-allocation-failures.md).

## <a name="solution"></a>Řešení

Postupujte podle pokynů pro chyby přidělení v následujících scénářích.

### <a name="not-pinned-to-a-cluster"></a>Nepřipojeno ke clusteru

Když nasadíte cloudovou službu (Classic) poprvé, cluster ještě není vybraný, takže cloudová služba není *připnutá*. Azure může mít selhání nasazení z těchto důvodů:

- Vybrali jste konkrétní velikost, která není v dané oblasti k dispozici.
- V oblasti není k dispozici kombinace velikostí, které jsou potřeba v různých rolích.

Když v tomto scénáři dojde k chybě přidělení, doporučuje se, abyste zkontrolovali dostupné velikosti v oblasti a změnili jste dříve zadanou velikost.

1. Můžete kontrolovat velikosti dostupné v oblasti na stránce [cloudové služby (klasické) produkty](https://azure.microsoft.com/global-infrastructure/services/?products=cloud-services) .

    > [!NOTE]
    > Na stránce *produkty* se nezobrazí dostupná kapacita. V případě jakékoli nové alokace by měl být Azure schopný vybrat v daném časovém okamžiku optimální cluster ve vaší oblasti.

1. Aktualizujte definiční soubor služby pro vaši cloudovou službu (Classic), abyste určili jinou [Velikost produktu](cloud-services-sizes-specs.md#configure-sizes-for-cloud-services) z vaší oblasti.

### <a name="pinned-to-a-cluster"></a>Připnuté ke clusteru

Existující cloudové služby jsou *připnuté* na cluster. Všechna další nasazení cloudové služby (Classic) se spustí ve stejném clusteru.

Pokud v tomto scénáři dojde k chybě přidělení, doporučuje se provést akci znovu nasadit do nové cloudové služby (Classic) (a aktualizovat *záznam CNAME*).

> [!TIP]
> Toto řešení je pravděpodobně nejúspěšnější, protože poskytuje platformě možnost výběru ze všech clusterů v dané oblasti.

> [!NOTE]
> Toto řešení by mělo mít žádný výpadek.

1. Nasaďte zatížení do nové cloudové služby (Classic).
    - Další pokyny najdete v tématu [Vytvoření a nasazení průvodce cloudovou službou (Classic)](cloud-services-how-to-create-deploy-portal.md) .

    > [!WARNING]
    > Pokud nechcete přijít o IP adresu přidruženou k tomuto slotu nasazení, můžete použít [Řešení 3 – zachovat IP adresu](cloud-services-allocation-failures.md#solutions).

1. Aktualizujte záznam *CNAME* nebo *a* , aby odkazoval na provoz do nové cloudové služby (Classic).
    - Další pokyny najdete v průvodci [konfigurací vlastního názvu domény pro cloudovou službu Azure (Classic)](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records) .

1. Po skončení provozu na starý Web můžete odstranit starou cloudovou službu (Classic).
    - Další pokyny najdete v průvodci [odstraňováním nasazení a cloudové služby (Classic)](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service) .
    - Pokud chcete zobrazit síťový provoz ve vaší cloudové službě (Classic), přečtěte si téma [Úvod do monitorování cloudové služby (Classic)](cloud-services-how-to-monitor.md).

Viz [řešení potíží se selháním přidělení cloudové služby (Classic) | Microsoft Docs](cloud-services-allocation-failures.md#common-issues) pro další nápravný postup.

## <a name="next-steps"></a>Další kroky

Pro další řešení selhání přidělení a informace na pozadí:

> [!div class="nextstepaction"]
> [Selhání přidělení – cloudová služba (klasická)](cloud-services-allocation-failures.md)

Pokud váš problém s Azure není v tomto článku vyřešen, navštivte fóra Azure na [webu MSDN a Stack Overflow](https://azure.microsoft.com/support/forums/). Svůj problém můžete vystavit na těchto fórech nebo odeslat na [ @AzureSupport Twitter](https://twitter.com/AzureSupport). Můžete také odeslat žádost o podporu Azure. Pokud chcete odeslat žádost o podporu, vyberte na stránce [podpory Azure](https://azure.microsoft.com/support/options/) možnost *získat podporu*.

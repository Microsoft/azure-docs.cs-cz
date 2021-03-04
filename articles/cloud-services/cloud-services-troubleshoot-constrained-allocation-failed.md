---
title: Řešení potíží s ConstrainedAllocationFailed při nasazení cloudové služby (Classic) do Azure | Microsoft Docs
description: Tento článek popisuje, jak vyřešit výjimku ConstrainedAllocationFailed při nasazení cloudové služby (Classic) do Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 346e7eb77039ab80e6f9dffb8ea8360198040504
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738275"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-classic-to-azure"></a>Řešení potíží s ConstrainedAllocationFailed při nasazení cloudové služby (Classic) do Azure

V tomto článku vyřešíte selhání přidělení, kde nejde nasadit Azure Cloud Services (Classic) kvůli omezením přidělení.

Když nasadíte instance do cloudové služby (Classic) nebo přidáte nové instance webových nebo pracovních rolí, Microsoft Azure přidělí výpočetní prostředky.

Během těchto operací může občas docházet k chybám, i když jste dosáhli limitu předplatného Azure.

> [!TIP]
> Tyto informace mohou být užitečné také při plánování nasazení služeb.

## <a name="symptom"></a>Příznak

V Azure Portal přejděte do vaší cloudové služby (Classic) a na bočním panelu vyberte *protokol operací (Classic)* a zobrazte si protokoly.

![Image zobrazuje okno protokol operací (Classic).](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

Při kontrole protokolů vaší cloudové služby (Classic) se zobrazí následující výjimka:

|Typ výjimky  |Chybová zpráva  |
|---------|---------|
|ConstrainedAllocationFailed     |Operace Azure se `{Operation ID}` nezdařila s kódem Compute. ConstrainedAllocationFailed. Podrobnosti: přidělení nebylo úspěšné; v požadavku nelze naplnit omezení. Požadované nasazení nové služby je spojené se skupinou vztahů nebo je jeho cílem virtuální síť, anebo je v této hostované službě nějaké nasazení. Kterákoli z těchto podmínek omezuje nové nasazení na konkrétní prostředky Azure. Zkuste to znovu později nebo zkuste zmenšit velikost virtuálního počítače nebo počet instancí rolí. Pokud je to možné, můžete případně odebrat výše uvedená omezení nebo vyzkoušet nasazení v jiné oblasti.|

## <a name="cause"></a>Příčina

Při nasazení první instance do cloudové služby (v přípravné nebo produkční) se tato cloudová služba připnula ke clusteru.

V průběhu času se prostředky v tomto clusteru můžou plně využívat. Pokud cloudová služba (Classic) vytvoří požadavek na přidělení pro další prostředky, pokud jsou v připojeném clusteru k dispozici nedostatečné prostředky, výsledkem bude selhání přidělení. Další informace najdete v tématu [běžné problémy při přidělování](cloud-services-allocation-failures.md#common-issues).

## <a name="solution"></a>Řešení

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
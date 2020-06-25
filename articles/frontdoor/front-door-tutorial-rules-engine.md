---
title: Azure Front Door
description: Tento článek popisuje, jak nakonfigurovat modul pravidel v Azure Portal i CLI.
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/30/2020
ms.author: mebeatty
ms.openlocfilehash: a931a12889cec67baf6ef2db09091c8ec581ef08
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2020
ms.locfileid: "85321558"
---
# <a name="configure-your-rules-engine"></a>Konfigurace modulu pravidel

Tento článek popisuje kroky pro vytvoření konfigurace modulu pravidel a prvního pravidla v Azure Portal i CLI. 

## <a name="configure-rules-engine-in-azure-portal"></a>Konfigurace modulu pravidel v Azure Portal
1. Před vytvořením konfigurace modulu pravidel [vytvořte přední dveře](quickstart-create-front-door.md).

2. V rámci prostředku front-dveří klikněte na **Nastavení** a vyberte **Konfigurace modulu pravidel**. Klikněte na **Přidat**, zadejte název konfigurace a začněte vytvářet konfiguraci modulu prvních pravidel.

    ![vyhledat modul pravidel](./media/front-door-rules-engine/rules-engine-tutorial-1.png)

3. Klikněte na **Přidat pravidlo** a vytvořte své první pravidlo. Potom kliknutím na **Přidat podmínku** nebo **přidat akci** můžete pravidlo definovat.
    
    > [!NOTE]
    >- Pokud chcete odstranit podmínku nebo akci z pravidla, můžete použít odpadkový koš na pravé straně konkrétní podmínky nebo akce.
    > - Pokud chcete vytvořit pravidlo, které platí pro veškerý příchozí provoz, nezadávejte žádné podmínky.
    > - Pokud chcete zastavit vyhodnocení pravidel po splnění první podmínky shody, klikněte na **zastavit vyhodnocení zbývajícího pravidla**. Pokud je toto políčko zaškrtnuté a splní se všechny podmínky shody určitého pravidla, zbývající pravidla v konfiguraci se nespustí.  

    ![vyhledat modul pravidel](./media/front-door-rules-engine/rules-engine-tutorial-4.png) 

4. Určete prioritu pravidel v rámci vaší konfigurace pomocí tlačítek nahoru, dolů a přesunout na horní. Priorita má vzestupné pořadí, což znamená, že první pravidlo je nejdůležitější.

5. Po vytvoření jednoho nebo více pravidel stiskněte **Uložit**. Tato akce vytvoří konfiguraci modulu pravidel.

6. Po vytvoření jedné nebo více konfigurací přidružte konfiguraci modulu pravidel k pravidlu směrování. V případě, že je možné použít jednu konfiguraci pro mnoho pravidel směrování, pravidlo směrování může obsahovat pouze jednu konfiguraci modulu pravidel. Přidružení provedete tak, že přejdete do **okna**  >  **pravidla směrování**na přední dveře. Vyberte pravidlo směrování, do kterého chcete přidat konfiguraci modulu pravidel, přejít do konfigurace modulu pravidla **podrobností o směrování**  >  **Rules engine configuration**a vyberte konfiguraci, kterou chcete přidružit.

    ![vyhledat modul pravidel](./media/front-door-rules-engine/rules-engine-tutorial-5.png)


## <a name="configure-rules-engine-in-azure-cli"></a>Konfigurace modulu pravidel v Azure CLI

1. Pokud jste to ještě neudělali, nainstalujte rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Přidat příponu "front-dveří":-AZ Extension add--name front-dveří. Pak Přihlaste se a přepněte do svého předplatného AZ Account set--Subscription <name_or_Id>.

2. Začněte vytvořením modulu pravidel – tento příklad ukazuje jedno pravidlo s jednou akcí založenou na hlavičkách a jednu podmínkou shody. 

```azurecli-interactive
az network front-door rules-engine rule create -f {front_door} -g {resource_group} --rules-engine-name {rules_engine} --name {rule1} --priority 1 --action-type RequestHeader --header-action Overwrite --header-name Rewrite --header-value True --match-variable RequestFilenameExtension --operator Contains --match-values jpg png --transforms Lowercase
```

3. Vypíše všechna pravidla. 

```azurecli-interactive
az network front-door rules-engine rule list -f {front_door} -g {rg} --name {rules_engine}
```

4. Přidejte akci přepsání trasy přesměrování. 

```azurecli-interactive
az network front-door rules-engine rule action add -f {front_door} -g {rg} --rules-engine-name {rules_engine} --name {rule1} --action-type ForwardRouteOverride --backend-pool {backend_pool_name} --caching Disabled
```

5. Vypíše všechny akce v pravidle. 

```azurecli-interactive
az network front-door rules-engine rule action list -f {front_door} -g {rg} -r {rules_engine} --name {rule1}
```

6. Propojte konfiguraci modulu pravidel s pravidlem směrování.  

```azurecli-interactive
az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --rules-engine {rules_engine}
```

7. Odpojte modul pravidel. 

```azurecli-interactive
az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --remove rulesEngine # case sensitive word ‘rulesEngine’
```

Další informace najdete tady: úplný seznam příkazů modulu pravidel AFD najdete [tady](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest).   

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [modulu pravidel AFD](front-door-rules-engine.md). 
- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
- Další informace najdete v [referenčních informacích](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest)k rozhraní CLI pro modul AFD Rules. 
- Další informace najdete v [referenčních informacích k PowerShellu](https://docs.microsoft.com/powershell/module/az.frontdoor/?view=azps-3.8.0)pro modul AFD Rules. 

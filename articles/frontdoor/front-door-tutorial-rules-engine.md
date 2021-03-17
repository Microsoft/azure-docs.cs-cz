---
title: 'Kurz: Konfigurace modulu pravidel – přední dvířka Azure'
description: Tento článek popisuje, jak nakonfigurovat modul pravidel v Azure Portal i CLI.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/09/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5d26d36c9f4ecb4aa0c7114b1ebef066e104f175
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198859"
---
# <a name="tutorial-configure-your-rules-engine"></a>Kurz: Konfigurace modulu pravidel

V tomto kurzu se dozvíte, jak vytvořit konfiguraci modulu pravidel a vaše první pravidlo v Azure Portal i CLI. 

V tomto kurzu se naučíte:
> [!div class="checklist"]
> - Nakonfigurujte modul pravidel pomocí portálu.
> - Konfigurace modulu pravidel pomocí Azure CLI

## <a name="prerequisites"></a>Požadavky

* Před dokončením kroků v tomto kurzu musíte nejprve vytvořit službu Front Door. Další informace najdete v tématu [Rychlý start: Vytvoření služby Front Door](quickstart-create-front-door.md).

## <a name="configure-rules-engine-in-azure-portal"></a>Konfigurace modulu pravidel v Azure Portal
1. V rámci prostředku front-dveří klikněte na **Nastavení** a vyberte **Konfigurace modulu pravidel**. Klikněte na **Přidat**, zadejte název konfigurace a začněte vytvářet konfiguraci modulu prvních pravidel.

    ![Nabídka nastavení front-dveří](./media/front-door-rules-engine/rules-engine-tutorial-1.png)

1. Klikněte na **Přidat pravidlo** a vytvořte své první pravidlo. Potom kliknutím na **Přidat podmínku** nebo **přidat akci** můžete pravidlo definovat.
    
    > [!NOTE]
    >- Pokud chcete odstranit podmínku nebo akci z pravidla, můžete použít odpadkový koš na pravé straně konkrétní podmínky nebo akce.
    > - Pokud chcete vytvořit pravidlo, které platí pro veškerý příchozí provoz, nezadávejte žádné podmínky.
    > - Pokud chcete zastavit vyhodnocení pravidel po splnění první podmínky shody, klikněte na **zastavit vyhodnocení zbývajícího pravidla**. Pokud je toto políčko zaškrtnuté a splní se všechny podmínky shody určitého pravidla, zbývající pravidla v konfiguraci se nespustí.  

    ![Konfigurace modulu pravidel](./media/front-door-rules-engine/rules-engine-tutorial-4.png) 

1. Určete prioritu pravidel v rámci vaší konfigurace pomocí tlačítek nahoru, dolů a přesunout na horní. Priorita má vzestupné pořadí, což znamená, že první pravidlo je nejdůležitější.

1. Po vytvoření jednoho nebo více pravidel stiskněte **Uložit**. Tato akce vytvoří konfiguraci modulu pravidel.

1. Po vytvoření jedné nebo více konfigurací přidružte konfiguraci modulu pravidel k pravidlu směrování. V případě, že je možné použít jednu konfiguraci pro mnoho pravidel směrování, pravidlo směrování může obsahovat pouze jednu konfiguraci modulu pravidel. Přidružení provedete tak, že přejdete do **okna**  >  **pravidla směrování** na přední dveře. Vyberte pravidlo směrování, do kterého chcete přidat konfiguraci modulu pravidel, přejít do konfigurace modulu pravidla **podrobností o směrování**  >  a vyberte konfiguraci, kterou chcete přidružit.

    ![Konfigurace pravidla směrování](./media/front-door-rules-engine/rules-engine-tutorial-5.png)


## <a name="configure-rules-engine-in-azure-cli"></a>Konfigurace modulu pravidel v Azure CLI

1. Pokud jste to ještě neudělali, nainstalujte rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli). Přidat příponu "front-dveří":-AZ Extension add--name front-dveří. Pak Přihlaste se a přepněte do svého předplatného AZ Account set--Subscription <name_or_Id>.

1. Začněte vytvořením modulu pravidel – tento příklad ukazuje jedno pravidlo s jednou akcí založenou na hlavičkách a jednu podmínkou shody. 

    ```azurecli-interactive
    az network front-door rules-engine rule create -f {front_door} -g {resource_group} --rules-engine-name {rules_engine} --name {rule1} --priority 1 --action-type RequestHeader --header-action Overwrite --header-name Rewrite --header-value True --match-variable RequestFilenameExtension --operator Contains --match-values jpg png --transforms Lowercase
    ```

1. Vypíše všechna pravidla. 

    ```azurecli-interactive
    az network front-door rules-engine rule list -f {front_door} -g {rg} --name {rules_engine}
    ```

1. Přidejte akci přepsání trasy přesměrování. 

    ```azurecli-interactive
    az network front-door rules-engine rule action add -f {front_door} -g {rg} --rules-engine-name {rules_engine} --name {rule1} --action-type ForwardRouteOverride --backend-pool {backend_pool_name} --caching Disabled
    ```

1. Vypíše všechny akce v pravidle. 

    ```azurecli-interactive
    az network front-door rules-engine rule action list -f {front_door} -g {rg} -r {rules_engine} --name {rule1}
    ```

1. Propojte konfiguraci modulu pravidel s pravidlem směrování.  

    ```azurecli-interactive
    az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --rules-engine {rules_engine}
    ```

1. Odpojte modul pravidel. 

    ```azurecli-interactive
    az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --remove rulesEngine # case sensitive word ‘rulesEngine’
    ```

Další informace najdete tady: úplný seznam příkazů modulu pravidel AFD najdete [tady](/cli/azure/ext/front-door/network/front-door/rules-engine).   

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste pro pravidla směrování nakonfigurovali a přidružíte konfiguraci modulu pravidel. Pokud už nechcete, aby byla konfigurace modulu pravidel přidružená k vašim předním dveřím, můžete konfiguraci odebrat provedením následujících kroků:

1. Zrušte přidružení pravidel směrování od konfigurace modulu pravidel kliknutím na tři tečky vedle pole název modulu pravidel.

    :::image type="content" source="./media/front-door-rules-engine/front-door-rule-engine-routing-association.png" alt-text="Přidružit pravidla směrování":::

1. Zrušte kontrolu všech pravidel směrování, ke kterým je tato konfigurace modulu pravidel přidružená, a klikněte na Uložit.

    :::image type="content" source="./media/front-door-rules-engine/front-door-routing-rule-association.png" alt-text="Přidružení pravidla směrování":::

1. Nyní můžete z předních dveří odstranit konfiguraci modulu pravidel.

    :::image type="content" source="./media/front-door-rules-engine/front-door-delete-rule-engine-configuration.png" alt-text="Odstranit konfiguraci modulu pravidel":::

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

* Vytvoření konfigurace modulu pravidel
* Přidružte konfiguraci k pravidlům směrování na přední dveře.

Pokud chcete zjistit, jak přidat záhlaví zabezpečení pomocí modulu pravidel, přejděte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Záhlaví zabezpečení s modulem pravidel](front-door-security-headers.md)
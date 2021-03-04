---
title: Konfigurace škálování pro Azure Cloud Services (Rozšířená podpora)
description: Jak povolit možnosti škálování pro Azure Cloud Services (Rozšířená podpora)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: cfa5be01a0d36764086c6c9adf97e6cb166d2bb6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101728157"
---
# <a name="configure-scaling-options-with-azure-cloud-services-extended-support"></a>Konfigurace možností škálování pomocí služby Azure Cloud Services (Rozšířená podpora) 

Podmínky je možné nakonfigurovat tak, aby umožňovaly horizontální navýšení nebo navýšení kapacity nasazení Cloud Services (rozšířené podpory). Tyto podmínky můžou být založené na využití procesoru, zatížení disku a zatížení sítě. 

Při konfiguraci škálování nasazení cloudových služeb Vezměte v úvahu následující informace:
- Škálování má vliv na základní využití. Větší instance rolí využívají více jader a můžete škálovat jenom v rámci limitu základní hodnoty vašeho předplatného. Další informace najdete v tématu [Limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Je podporováno škálování na základě prahové hodnoty pro zasílání zpráv ve frontě. Další informace najdete v tématu [Začínáme s úložištěm Azure Queue](../storage/queues/storage-dotnet-how-to-use-queues.md).
- Abyste zajistili vysokou dostupnost aplikací cloudové služby (rozšířené podpory), zajistěte nasazení se dvěma nebo více instancemi role.
- Vlastní automatické škálování může nastat pouze v případě, že jsou všechny role v **připraveném** stavu.

## <a name="configure-and-manage-scaling"></a>Konfigurace a Správa škálování

1. Přihlaste se na [Azure Portal](https://portal.azure.com). 
2. Vyberte nasazení cloudové služby (rozšířené podpory), u kterého chcete nakonfigurovat škálování. 
3. Vyberte okno **měřítko** . 

    :::image type="content" source="media/enable-scaling-1.png" alt-text="Image zobrazuje výběr možnosti Vzdálená plocha v Azure Portal":::

4. Na stránce se zobrazí seznam všech rolí, ve kterých lze nakonfigurovat škálování. Vyberte roli, kterou chcete nakonfigurovat. 
5. Vyberte typ škálování, který chcete konfigurovat.
    - **Ruční škálování** nastaví absolutní počet instancí.
        1. Vyberte **Ruční škálování**.
        2. Zadejte počet instancí, které chcete škálovat nahoru nebo dolů.
        3. Vyberte **Uložit**.

        :::image type="content" source="media/enable-scaling-2.png" alt-text="Obrázek ukazuje nastavení ručního škálování v Azure Portal":::

        4. Operace škálování se spustí okamžitě. 
        
    - **Vlastní automatické škálování** vám umožní nastavit pravidla, která určují, jak velký nebo jak malý rozsah. 
        1. Vybrat **vlastní automatické škálování**
        2. Vyberte škálování podle metriky nebo počtu instancí.

        :::image type="content" source="media/enable-scaling-3.png" alt-text="Obrázek ukazuje nastavení vlastního automatického škálování v Azure Portal":::

        3. Pokud se škálování na základě metriky přidají, podle potřeby přidejte pravidla, aby bylo možné dosáhnout požadovaných výsledků škálování.

        :::image type="content" source="media/enable-scaling-4.png" alt-text="Obrázek ukazuje nastavení vlastních pravidel automatického škálování v Azure Portal":::

        4. Vyberte **Uložit**.
        5. Operace škálování začnou ihned po aktivaci pravidla.
        
6. Existující pravidla škálování použitá pro vaše nasazení můžete zobrazit nebo upravit tak, že vyberete kartu **škálování** .

    :::image type="content" source="media/enable-scaling-5.png" alt-text="Obrázek ukazuje úpravu stávajícího pravidla škálování v Azure Portal":::

## <a name="next-steps"></a>Další kroky 
- Přečtěte si [požadavky na nasazení](deploy-prerequisite.md) pro Cloud Services (Rozšířená podpora).
- Přečtěte si [Nejčastější dotazy](faq.md) k Cloud Services (Rozšířená podpora).
- Nasaďte cloudovou službu (rozšířenou podporu) pomocí [Azure Portal](deploy-portal.md), [PowerShellu](deploy-powershell.md), [šablony](deploy-template.md) nebo sady [Visual Studio](deploy-visual-studio.md).
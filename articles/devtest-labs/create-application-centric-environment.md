---
title: Vytvoření prostředí zaměřeného na aplikace – Azure
description: Tento článek ukazuje, jak vytvořit prostředí orientované na aplikace s Cloud Shellmi středníky a Microsoft Azure.
ms.topic: how-to
ms.date: 11/26/2020
ms.openlocfilehash: 88244f268d5ed038e9bb7082d3d5cc1179e5ec4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94918154"
---
# <a name="create-an-application-centric-environment"></a>Vytvoření prostředí zaměřeného na aplikace

[Cloudshellu dvojtečka Quali](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview) je platforma SaaS pro poskytování automatizace infrastruktury pro komplexní prostředí zaměřená na aplikace v cloudových technologiích, včetně Azure a Kubernetes. Cloudshellu dvojtečky doplňují Azure DevTest Labs, které vývojářům pomůžou nasazovat komplexní aplikace v celém datovém proudu, a to všechno jako v produkčním prostředí.

Tento článek ukazuje, jak vytvořit prostředí zaměřené na aplikace s Cloudshellu dvojtečkou a Microsoft Azure.

## <a name="set-up-the-environment-with-cloudshell-colony-and-microsoft-azure"></a>Nastavení prostředí s Cloudshellu dvojtečkou a Microsoft Azure

1. Zaregistrujte se do bezplatné zkušební verze [dvojtečky](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview).

    :::image type="content" source="./media/create-application-centric-environment/free-trial.png" alt-text="Registrace bezplatné zkušební verze":::    
1. Propojte svůj účet Azure ([tady si můžete prohlédnout kroky](https://colonysupport.quali.com/hc/articles/360008222234)).

    :::image type="content" source="./media/create-application-centric-environment/welcome.png" alt-text="Vítá vás dvojtečka":::     
1. Pozvěte uživatele do svého prostoru.
1. Vytvořte svůj první podrobný plán pomocí souboru YAML ([tady si můžete prohlédnout kroky](https://colonysupport.quali.com/hc/articles/360001680807-Steps-to-Developing-a-Blueprint)).
    1. Propojte své úložiště podrobného plánu v GitHubu nebo BitBucket s dvojtečkou.
    1. Jako základ použijte vzorek podrobných středníků a upravte podle potřeby.

        :::image type="content" source="./media/create-application-centric-environment/performance-stress-tests.png" alt-text="Zátěžové testy":::    
    1. Publikujte svůj podrobný plán, který budou používat ostatní.
1. Spusťte prostředí aplikace do izolovaného prostoru pomocí dvojtečky.

    :::image type="content" source="./media/create-application-centric-environment/blueprints.png" alt-text="Spuštění aplikačního prostředí do izolovaného prostoru (sandbox) pomocí dvojtečky":::    

> [!NOTE]
> Svůj plán můžete integrovat také jako součást pracovního postupu CI/CD v Azure DevOps (tady si můžete[prohlédnout kroky](https://colonysupport.quali.com/hc/articles/360008464234)).

:::image type="content" source="./media/create-application-centric-environment/devops-pipeline.png" alt-text="Připojení k vašemu kanálu Azure DevOps":::    

## <a name="next-steps"></a>Další kroky

[Požádat o ukázku dvojtečky](https://info.quali.com/cloudshell-colony-demo-request)

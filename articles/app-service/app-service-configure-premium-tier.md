---
title: Konfigurace úrovně PremiumV2 pro službu Azure App Service | Dokumentace Microsoftu
description: Zjistěte, jak díky škálování na cenovou úroveň PremiumV2 lepší výkon pro webové, mobilní a API aplikace ve službě Azure App Service.
keywords: app service, azure app service, škálování, škálovatelné, plán služby App Service, náklady služby App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: cephalin
ms.openlocfilehash: 59e5825554843ef7265cbccdd9a63db0426891fa
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47226305"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Konfigurace úrovně PremiumV2 pro službu Azure App Service

Nové **PremiumV2** cenová úroveň poskytuje rychlejší procesory, SSD úložištěm a čísly typu Double paměti k jádrům poměr stávající cenové úrovně. S výhodou, výkon můžete ušetřit peníze při spuštění aplikace na méně instancí. V tomto článku se dozvíte, jak vytvořit aplikaci v **PremiumV2** vrstvy nebo vertikálně navýšit kapacitu aplikace, která **PremiumV2** vrstvy.

## <a name="prerequisites"></a>Požadavky

Vertikálně navýšit kapacitu, webové aplikace do **PremiumV2**, budete muset mít třeba webovou aplikaci ve službě Azure App Service, na kterém běží v cenové úrovni, která je nižší než **PremiumV2**, a webové aplikace musí být spuštěna v, který podporuje nasazení služby App Service PremiumV2.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>Dostupnost PremiumV2

**PremiumV2** úroveň je dostupná pro službu App Service na obou _Windows_ stejně jako _Linux_.

**PremiumV2** je k dispozici ve většině oblastí Azure. Pokud chcete zobrazit, pokud je k dispozici ve vaší oblasti, spusťte následující příkaz rozhraní příkazového řádku Azure [Azure Cloud Shell](../cloud-shell/overview.md):

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Vytvoření aplikace ve úrovně PremiumV2

Cenová úroveň aplikace služby App Service je definována v [plán služby App Service](azure-web-sites-web-hosting-plans-in-depth-overview.md) , která běží na. Můžete vytvořit plán služby App Service, samostatně nebo jako součást vytváření webové aplikace.

Při konfiguraci plánu služby App Service v <a href="https://portal.azure.com" target="_blank">webu Azure portal</a>vyberte **cenová úroveň**. 

Vyberte **produkční**a pak vyberte **P1V2**, **P2V2**, nebo **P3V2**, pak klikněte na tlačítko **použít**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Pokud nevidíte **P1V2**, **P2V2**, a **P3V2** jako možnosti, nebo pokud jsou zobrazena šedě možnosti out pak **PremiumV2** pravděpodobně není k dispozici v Základní nasazení služby App Service, která obsahuje plán služby App Service. Zobrazit [vertikálně navýšit kapacitu z nepodporované prostředek skupiny a oblasti kombinaci](#unsupported) další podrobnosti.

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Vertikálně navýšit kapacitu stávající aplikace pro úrovně PremiumV2

Před Škálováním stávající aplikace pro **PremiumV2** vrstvy, ujistěte se, že **PremiumV2** je k dispozici. Informace najdete v tématu [PremiumV2 dostupnosti](#availability). Pokud není k dispozici, najdete v článku [vertikálně navýšit kapacitu z nepodporované prostředek skupiny a oblasti kombinaci](#unsupported).

V závislosti na prostředí hostingu vertikální navýšení kapacity může vyžadovat dodatečné kroky. 

V <a href="https://portal.azure.com" target="_blank">webu Azure portal</a>, otevřete stránku aplikace služby App Service.

V levém navigačním panelu na stránce aplikace služby App Service, vyberte **vertikálně navýšit kapacitu (plán služby App Service)**.

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Vyberte **produkční**a pak vyberte **P1V2**, **P2V2**, nebo **P3V2**, pak klikněte na tlačítko **použít**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Pokud vaše operace skončí úspěšně, stránka s přehledem vaší aplikace ukazuje, že teď je **PremiumV2** vrstvy.

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Pokud dojde k chybě

Některé nabízené plány služby App Service nejde vertikálně navýšit kapacitu na úrovně PremiumV2 Pokud základní nasazení služby App Service nepodporuje PremiumV2.  Zobrazit [vertikálně navýšit kapacitu z nepodporované prostředek skupiny a oblasti kombinaci](#unsupported) další podrobnosti.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Vertikálně navýšit kapacitu z kombinace Nepodporovaný zdroj skupiny a oblasti

Pokud vaše aplikace běží v nasazení služby App Service kde **PremiumV2** není k dispozici, nebo pokud vaše aplikace běží v oblasti, které aktuálně nepodporuje **PremiumV2**, budete muset znovu nasadit vaši aplikaci Výhodou **PremiumV2**.  Máte dvě možnosti:

- Vytvoření **nové** prostředku skupiny a pak vytvořte **nové** plán webové aplikace a služby app service v **nové** skupinu prostředků, výběr požadovanou oblast Azure při vytváření proces.  Můžete **musí** vyberte **PremiumV2** plán v době vytvoření nového plánu služby app service.  Tím se zajistí kombinací skupinu prostředků, plán služby App Service, a oblasti Azure bude mít za následek plán služby App Service, která se vytvářejí v nasazení služby App Service, která podporuje **PremiumV2**.  Znovu nasaďte kód vaší aplikace do nově vytvořené aplikace a plán služby app service. V případě potřeby plán služby App Service můžete následně vertikálně snížit kapacitu z **PremiumV2** uložit náklady a bude ji moct úspěšně zpět vertikální navýšení kapacity v budoucnu znovu pomocí **PremiumV2**.
- Pokud vaše aplikace už běží v existujícím **Premium** vrstvy, pak můžete naklonovat vaší aplikace pomocí nastavení aplikace, připojovacích řetězců a konfigurace nasazení nový plán služby app service, která používá **PremiumV2**.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    V **klonování aplikací** stránky, můžete vytvořit plán služby App Service pomocí **PremiumV2** v oblasti mají a zadejte nastavení aplikace a konfigurace, které chcete naklonovat.

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

Můžete automatizovat vytváření aplikací v **PremiumV2** úroveň pomocí skriptů pomocí [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) nebo [prostředí Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>Azure CLI

Následující příkaz vytvoří plán služby App Service v _P1V2_. Můžete ji spustit ve službě Cloud Shell. Možnosti pro `--sku` jsou P1V2, _P2V2_, a _P3V2_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

Následující příkaz vytvoří plán služby App Service v _P1V2_. Možnosti pro `-WorkerSize` jsou _malé_, _střední_, a _velké_.

```PowerShell
New-AzureRmAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>Další zdroje informací

[Škálování aplikace v Azure](web-sites-scale.md)  
[Ruční nebo automatické škálování počtu instancí](../monitoring-and-diagnostics/insights-how-to-scale.md)
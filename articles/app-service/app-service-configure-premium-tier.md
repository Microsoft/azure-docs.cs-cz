---
title: Konfigurace úrovně PremiumV2
description: Zjistěte, jak zlepšit výkon pro vaši webovou, mobilní a api aplikaci ve službě Azure App Service škálováním na novou cenovou úroveň PremiumV2.
keywords: app service, azure app service, škálování, škálovatelné, plán služby App Service, náklady služby App Service
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 07/25/2018
ms.custom: seodec18
ms.openlocfilehash: f7f9f6d5617ad0f9be69c47ce514d395534fd892
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672216"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Konfigurace úrovně PremiumV2 pro službu Azure App Service

Nová cenová úroveň **PremiumV2** poskytuje rychlejší procesory, úložiště SSD a zdvojnásobuje poměr paměti k jádru stávajících cenových úrovní. Díky výhodě výkonu můžete ušetřit peníze spuštěním aplikací v menším počtu instancí. V tomto článku se dozvíte, jak vytvořit aplikaci na úrovni **PremiumV2** nebo vertikálně navýšit kapacitu aplikace na úroveň **PremiumV2.**

## <a name="prerequisites"></a>Požadavky

Chcete-li rozšířit kapacitu aplikace na **PremiumV2**, musíte mít aplikaci Azure App Service, která běží v cenové úrovni nižší než **PremiumV2**a aplikace musí běžet v nasazení služby App Service, která podporuje PremiumV2.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>Dostupnost PremiumV2

Úroveň **PremiumV2** je k dispozici pro službu App Service na _Windows_ i _Linuxu_.

**PremiumV2** je k dispozici ve většině oblastí Azure. Pokud chcete zjistit, jestli je dostupná ve vaší oblasti, spusťte v [prostředí Azure Cloud Shell](../cloud-shell/overview.md)následující příkaz y Azure CLI :

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Vytvoření aplikace na úrovni PremiumV2

Cenová úroveň aplikace App Service je definována v [plánu služby App Service,](overview-hosting-plans.md) na které běží. Plán služby App Service můžete vytvořit samostatně nebo jako součást vytváření aplikací.

Při konfiguraci plánu služby App Service na <a href="https://portal.azure.com" target="_blank">webu Azure Portal</a>vyberte **Cenová úroveň**. 

Vyberte **Výroba**, pak vyberte **P1V2**, **P2V2**nebo **P3V2**a klepněte na **tlačítko Použít**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Pokud nevidíte **P1V2**, **P2V2**a **P3V2** jako možnosti, nebo pokud jsou možnosti šedě, pak **PremiumV2** pravděpodobně není k dispozici v základní nasazení služby App Service, která obsahuje plán služby App Service. Další podrobnosti najdete [v tématu Vertika navýšit kapacitu z kombinace nepodporované skupiny prostředků a oblastí,](#unsupported) kde najdete další podrobnosti.

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Škálování na úroveň existující aplikace na úroveň PremiumV2

Před škálováním existující aplikace na úroveň **PremiumV2** se ujistěte, že je **PremiumV2** k dispozici. Další informace naleznete v [tématu PremiumV2 availability](#availability). Pokud není k dispozici, [přečtěte si tématu Vertika navýšit kapacitu z nepodporované kombinace skupiny prostředků a oblastí](#unsupported).

V závislosti na hostitelském prostředí může škálování vyžadovat další kroky. 

Na <a href="https://portal.azure.com" target="_blank">webu Azure Portal</a>otevřete stránku aplikace App Service.

V levém navigačním panelu na stránce aplikace App Service vyberte **Vertikálně navýšit kapacitu (plán služby App Service).**

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Vyberte **Výroba**, pak vyberte **P1V2**, **P2V2**nebo **P3V2**a klepněte na **tlačítko Použít**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Pokud vaše operace úspěšně skončí, stránka s přehledem aplikace ukazuje, že je teď na úrovni **PremiumV2.**

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Pokud se zobrazí chyba

Některé plány služby App Service nelze vertikálně škálovat na úroveň PremiumV2, pokud základní nasazení služby App Service nepodporuje PremiumV2.  Další podrobnosti najdete [v tématu Vertika navýšit kapacitu z kombinace nepodporované skupiny prostředků a oblastí,](#unsupported) kde najdete další podrobnosti.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Škálování z nepodporované kombinace skupiny prostředků a oblastí

Pokud vaše aplikace běží v nasazení služby App Service, kde **PremiumV2** není k dispozici, nebo pokud vaše aplikace běží v oblasti, která v současné době nepodporuje **PremiumV2**, budete muset znovu nasadit aplikaci využít **PremiumV2**.  Máte dvě možnosti:

- Vytvořte **novou** skupinu prostředků a pak vytvořte **nový** plán aplikace a služby App Service v **nové** skupině prostředků a během procesu vytváření vyberte požadovanou oblast Azure.  Plán **PremiumV2** **musíte** vybrat v okamžiku vytvoření nového plánu služby App Service.  Tím zajistíte, že kombinace skupiny prostředků, plánu služby App Service a oblasti Azure bude mít za následek vytvoření plánu služby App Service v nasazení služby App Service, které podporuje **PremiumV2**.  Pak znovu nasadit kód aplikace do nově vytvořené aplikace a plánu služby aplikace. V případě potřeby můžete následně škálovat plán služby App Service z **PremiumV2,** abyste ušetřili náklady, a v budoucnu budete moci úspěšně znovu škálovat pomocí **PremiumV2**.
- Pokud vaše aplikace už běží na existující úrovni **Premium,** můžete ji naklonovat se všemi nastaveními aplikace, připojovacími řetězci a konfigurací nasazení do nového plánu služby App Service, který používá **PremiumV2**.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    Na stránce **klonování aplikace** můžete vytvořit plán služby App Service pomocí **PremiumV2** v požadované oblasti a určit nastavení aplikace a konfiguraci, kterou chcete klonovat.

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

Vytváření aplikací na úrovni **PremiumV2** můžete automatizovat pomocí skriptů pomocí [azure cli](/cli/azure/install-azure-cli) nebo [Azure PowerShellu](/powershell/azure/overview).

### <a name="azure-cli"></a>Azure CLI

Následující příkaz vytvoří plán služby App Service v _P1V2_. Můžete spustit v prostředí Cloud Shell. Možnosti `--sku` jsou P1V2, _P2V2_a _P3V2_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Následující příkaz vytvoří plán služby App Service v _P1V2_. Možnosti `-WorkerSize` pro jsou _malé_, _střední_a _velké_.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>Další zdroje informací

[Vertikální navýšení kapacity aplikace v Azure](manage-scale-up.md)  
[Ruční nebo automatické škálování počtu instancí](../monitoring-and-diagnostics/insights-how-to-scale.md)

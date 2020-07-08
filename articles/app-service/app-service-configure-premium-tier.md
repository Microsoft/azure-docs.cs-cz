---
title: Konfigurace úrovně PremiumV2
description: Přečtěte si, jak lépe zvýšit výkon webové, mobilní a API aplikace v Azure App Service škálováním na novou cenovou úroveň PremiumV2.
keywords: app service, azure app service, škálování, škálovatelné, plán služby App Service, náklady služby App Service
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 07/25/2018
ms.custom: seodec18
ms.openlocfilehash: f7f9f6d5617ad0f9be69c47ce514d395534fd892
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "74672216"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Konfigurace PremiumV2 úrovně pro Azure App Service

Nová cenová úroveň **PremiumV2** poskytuje rychlejší procesory, SSD úložiště a zdvojnásobí poměr paměti k jádrům pro stávající cenové úrovně. Díky výhodám výkonu můžete ušetřit peníze tím, že své aplikace spustíte na méně instancí. V tomto článku se dozvíte, jak vytvořit aplikaci ve vrstvě **PremiumV2** nebo jak škálovat aplikaci na **PremiumV2** vrstvu.

## <a name="prerequisites"></a>Požadavky

Abyste mohli škálovat aplikaci na **PremiumV2**, musíte mít Azure App Service aplikaci, která běží v cenové úrovni nižší než **PremiumV2**, a aplikace musí běžet v nasazení App Service, které podporuje PremiumV2.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>Dostupnost PremiumV2

Úroveň **PremiumV2** je k dispozici pro App Service v obou oknech i v _systému_ _Linux_.

**PremiumV2** je k dispozici ve většině oblastí Azure. Pokud chcete zjistit, jestli je ve vaší oblasti k dispozici, spusťte následující příkaz rozhraní příkazového řádku Azure v [Azure Cloud Shell](../cloud-shell/overview.md):

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Vytvoření aplikace v PremiumV2 vrstvě

Cenová úroveň aplikace App Service je definována v [plánu App Service](overview-hosting-plans.md) , na kterém běží. Plán App Service můžete vytvořit samostatně nebo jako součást vytváření aplikací.

Při konfiguraci plánu App Service v <a href="https://portal.azure.com" target="_blank">Azure Portal</a>vyberte **cenová úroveň**. 

Vyberte možnost **Výroba**, pak **Vyberte P1V2**, **P2V2**nebo **P3V2**a pak klikněte na **použít**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Pokud nevidíte **P1V2**, **P2V2**a **P3V2** jako možnosti, nebo pokud jsou možnosti šedé, pak **PremiumV2** pravděpodobně není k dispozici v podkladovém nasazení App Service, které obsahuje plán App Service. Další podrobnosti najdete v tématu [horizontální navýšení kapacity z nepodporované skupiny prostředků a kombinace oblastí](#unsupported) .

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Horizontální navýšení kapacity existující aplikace na úroveň PremiumV2

Před změnou měřítka existující aplikace na **PremiumV2** úroveň se ujistěte, že je k dispozici **PremiumV2** . Informace najdete v tématu [dostupnost PremiumV2](#availability). Pokud není k dispozici, přečtěte si téma [horizontální navýšení kapacity z nepodporované skupiny prostředků a oblasti](#unsupported).

V závislosti na vašem hostitelském prostředí může škálování vyžadovat další kroky. 

V <a href="https://portal.azure.com" target="_blank">Azure Portal</a>otevřete stránku aplikace App Service.

V levém navigačním panelu aplikace App Service vyberte **škálovat nahoru (App Service plán)**.

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Vyberte možnost **Výroba**, pak **Vyberte P1V2**, **P2V2**nebo **P3V2**a pak klikněte na **použít**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Pokud se vaše operace úspěšně dokončí, zobrazí se na stránce Přehled vaší aplikace, že je teď ve vrstvě **PremiumV2** .

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Pokud se zobrazí chyba

Některé plány App Service nemůžou škálovat až na úroveň PremiumV2, pokud základní nasazení App Service nepodporuje PremiumV2.  Další podrobnosti najdete v tématu [horizontální navýšení kapacity z nepodporované skupiny prostředků a kombinace oblastí](#unsupported) .

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Navýšení kapacity z nepodporované kombinace skupiny prostředků a oblastí

Pokud je vaše aplikace spuštěná v nasazení App Service, kde **PremiumV2** není k dispozici, nebo pokud je vaše aplikace spuštěná v oblasti, která v současné době nepodporuje **PremiumV2**, budete muset aplikaci znovu nasadit, abyste mohli využít výhod **PremiumV2**.  Máte dvě možnosti:

- Vytvořte **novou** skupinu prostředků a potom v **nové** skupině prostředků vytvořte **Nový** plán aplikace a App Service a v rámci procesu vytváření vyberte požadovanou oblast Azure.  V době, kdy je vytvořen nový plán služby App Service, **musíte** vybrat plán **PremiumV2** .  Tím se zajistí, že kombinace skupiny prostředků, App Service plánu a oblasti Azure bude v nasazení App Service, která podporuje **PremiumV2**, vytvořena App Service plán.  Pak znovu nasaďte kód aplikace do nově vytvořené aplikace a plánu služby App Service. Pokud budete chtít, můžete následně škálovat App Service plán z **PremiumV2** a ušetřit tak náklady a stále budete moct znovu škálovat zálohování znovu v budoucnu pomocí **PremiumV2**.
- Pokud už vaše aplikace běží na stávající úrovni **Premium** , můžete svoji aplikaci klonovat pomocí nastavení aplikace, připojovacích řetězců a konfigurace nasazení do nového plánu služby App Service, který používá **PremiumV2**.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    Na stránce **klonovat aplikaci** můžete vytvořit plán App Service pomocí **PremiumV2** v požadované oblasti a zadat nastavení a konfiguraci aplikace, které chcete klonovat.

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

Vytváření aplikací můžete automatizovat v **PremiumV2** vrstvě pomocí skriptů pomocí [Azure CLI](/cli/azure/install-azure-cli) nebo [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>Azure CLI

Následující příkaz vytvoří plán App Service v _P1V2_. Můžete ji spustit v Cloud Shell. Možnosti pro `--sku` jsou P1V2, _P2V2_a _P3V2_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Následující příkaz vytvoří plán App Service v _P1V2_. Možnosti pro `-WorkerSize` jsou _malá_, _střední_a _Velká_.

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

---
title: Konfigurace úrovně PremiumV3
description: Přečtěte si, jak lépe zvýšit výkon webové, mobilní a API aplikace v Azure App Service škálováním na novou cenovou úroveň PremiumV3.
keywords: app service, azure app service, škálování, škálovatelné, plán služby App Service, náklady služby App Service
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: e6b8c7d54cf24d810a1f32082d816c908966f63c
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92739682"
---
# <a name="configure-premiumv3-tier-for-azure-app-service"></a>Konfigurace PremiumV3 úrovně pro Azure App Service

Nová cenová úroveň **PremiumV3** poskytuje rychlejší procesory, SSD úložiště a čtyřnásobné poměry paměti k jádrům pro stávající cenové úrovně (dvě úrovně **PremiumV2** ). Díky výhodám výkonu můžete ušetřit peníze tím, že své aplikace spustíte na méně instancí. V tomto článku se dozvíte, jak vytvořit aplikaci ve vrstvě **PremiumV3** nebo jak škálovat aplikaci na **PremiumV3** vrstvu.

## <a name="prerequisites"></a>Předpoklady

Abyste mohli škálovat aplikaci na **PremiumV3** , musíte mít Azure App Service aplikaci, která běží v cenové úrovni nižší než **PremiumV3** , a aplikace musí běžet v nasazení App Service, které podporuje PremiumV3.

<a name="availability"></a>

## <a name="premiumv3-availability"></a>Dostupnost PremiumV3

Úroveň **PremiumV3** je k dispozici pro nativní i kontejnerové aplikace, včetně kontejnerů Windows a kontejnerů Linux.

> [!NOTE]
> Všechny kontejnery Windows běžící na úrovni **kontejneru Premium** během období Preview budou i nadále fungovat tak, jak jsou, ale úroveň **kontejneru Premium** zůstane ve verzi Preview. Úroveň **PremiumV3** je oficiální náhradou úrovně **kontejneru Premium** . 

**PremiumV3** je k dispozici v některých oblastech Azure a dostupnost v dalších oblastech se průběžně přidávají. Pokud chcete zjistit, jestli je ve vaší oblasti k dispozici, spusťte následující příkaz rozhraní příkazového řádku Azure v [Azure Cloud Shell](../cloud-shell/overview.md):

```azurecli-interactive
az appservice list-locations --sku P1V3
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv3-tier"></a>Vytvoření aplikace v PremiumV3 vrstvě

Cenová úroveň aplikace App Service je definována v [plánu App Service](overview-hosting-plans.md) , na kterém běží. Plán App Service můžete vytvořit samostatně nebo jako součást vytváření aplikací.

Při konfiguraci plánu App Service v <a href="https://portal.azure.com" target="_blank">Azure Portal</a>vyberte **cenová úroveň** . 

Vyberte možnost **Výroba** , pak **Vyberte P1V3** , **P2V3** nebo **P3V3** a pak klikněte na **použít** .

![Snímek obrazovky znázorňující Doporučené cenové úrovně pro vaši aplikaci](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Pokud nevidíte **P1V3** , **P2V3** a **P3V3** jako možnosti, nebo pokud jsou možnosti šedé, pak **PremiumV3** pravděpodobně není k dispozici v podkladovém nasazení App Service, které obsahuje plán App Service. Další podrobnosti najdete v tématu [horizontální navýšení kapacity z nepodporované skupiny prostředků a kombinace oblastí](#unsupported) .

## <a name="scale-up-an-existing-app-to-premiumv3-tier"></a>Horizontální navýšení kapacity existující aplikace na úroveň PremiumV3

Před změnou měřítka existující aplikace na **PremiumV3** úroveň se ujistěte, že je k dispozici **PremiumV3** . Informace najdete v tématu [dostupnost PremiumV3](#availability). Pokud není k dispozici, přečtěte si téma [horizontální navýšení kapacity z nepodporované skupiny prostředků a oblasti](#unsupported).

V závislosti na vašem hostitelském prostředí může škálování vyžadovat další kroky. 

V <a href="https://portal.azure.com" target="_blank">Azure Portal</a>otevřete stránku aplikace App Service.

V levém navigačním panelu aplikace App Service vyberte **škálovat nahoru (App Service plán)** .

![Snímek obrazovky znázorňující postup horizontálního navýšení kapacity plánu služby App Service.](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Vyberte možnost **Výroba** , pak **Vyberte P1V3** , **P2V3** nebo **P3V3** a pak klikněte na **použít** .

![Snímek obrazovky znázorňující Doporučené cenové úrovně pro vaši aplikaci](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Pokud se vaše operace úspěšně dokončí, zobrazí se na stránce Přehled vaší aplikace, že je teď ve vrstvě **PremiumV3** .

![Snímek obrazovky zobrazující cenovou úroveň PremiumV3 na stránce přehledu vaší aplikace](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Pokud se zobrazí chyba

Některé plány App Service nemůžou škálovat až na úroveň PremiumV3, pokud základní nasazení App Service nepodporuje PremiumV3. Další podrobnosti najdete v tématu [horizontální navýšení kapacity z nepodporované skupiny prostředků a kombinace oblastí](#unsupported) .

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Navýšení kapacity z nepodporované kombinace skupiny prostředků a oblastí

Pokud je vaše aplikace spuštěná v nasazení App Service, kde **PremiumV3** není k dispozici, nebo pokud je vaše aplikace spuštěná v oblasti, která v současné době nepodporuje **PremiumV3** , musíte aplikaci znovu nasadit, abyste mohli využít výhod **PremiumV3** .  Máte dvě možnosti:

- Vytvořte aplikaci v nové skupině prostředků a pomocí nového plánu App Service. Při vytváření plánu App Service vyberte úroveň **PremiumV3** . Tento krok zajistí, že je plán App Service nasazený do jednotky nasazení, která podporuje **PremiumV3** . Pak znovu nasaďte kód aplikace do nově vytvořené aplikace. I když App Service plán škálovat na nižší úroveň a ušetřit tak náklady, můžete vždy škálovat na **PremiumV3** , protože jednotka nasazení ho podporuje.
- Pokud už vaše aplikace běží na stávající úrovni **Premium** , můžete svoji aplikaci klonovat pomocí nastavení aplikace, připojovacích řetězců a konfigurace nasazení do nového plánu služby App Service, který používá **PremiumV3** .

    ![Snímek obrazovky znázorňující způsob klonování aplikace](media/app-service-configure-premium-tier/clone-app.png)

    Na stránce **klonovat aplikaci** můžete vytvořit plán App Service pomocí **PremiumV3** v požadované oblasti a zadat nastavení a konfiguraci aplikace, které chcete klonovat.

## <a name="moving-from-premium-container-to-premium-v3-sku"></a>Přesun z kontejneru Premium na SKU Premium V3

Pokud máte aplikaci, která využívá SKU kontejneru verze Preview Premium a chcete přejít na novou SKLADOVOU položku Premium v3, budete muset aplikaci znovu nasadit, abyste mohli využívat **PremiumV3** . Provedete to tak, že si přečtěte první možnost ve [škále po nepodporované kombinaci skupiny prostředků a oblasti](#scale-up-from-an-unsupported-resource-group-and-region-combination) .

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

Vytváření aplikací můžete automatizovat v **PremiumV3** vrstvě pomocí skriptů pomocí [Azure CLI](/cli/azure/install-azure-cli) nebo [Azure PowerShell](/powershell/azure/).

### <a name="azure-cli"></a>Azure CLI

Následující příkaz vytvoří plán App Service v _P1V2_ . Můžete ji spustit v Cloud Shell. Možnosti pro `--sku` jsou P1V3, _P2V3_ a _P3V3_ .

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V3
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Následující příkaz vytvoří plán App Service v _P1V3_ . Možnosti pro `-WorkerSize` jsou _malá_ , _střední_ a _Velká_ .

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV3" `
    -WorkerSize "Small"
```

## <a name="more-resources"></a>Další zdroje informací

[Horizontální navýšení kapacity aplikace v Azure](manage-scale-up.md) 
 [Ruční nebo automatické škálování počtu instancí](../azure-monitor/platform/autoscale-get-started.md)

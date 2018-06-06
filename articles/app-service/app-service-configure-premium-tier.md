---
title: Konfigurace PremiumV2 vrstvy pro Azure App Service | Microsoft Docs
description: Naučte se lepší výkon pro webové, mobilní a aplikace API v Azure App Service pomocí škálování pro nové PremiumV2 cenovou úroveň.
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
ms.date: 09/19/2017
ms.author: cephalin
ms.openlocfilehash: 4c157ed905b7dc48c886b26987c164ef9a47f3c3
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714557"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Konfigurace PremiumV2 vrstvy pro Azure App Service

Nové **PremiumV2** cenová úroveň nabízí rychlejších procesorů, úložiště SSD a hodnoty Double paměti jádra poměr existující cenové úrovně. S výhodou, výkon může ušetřit peníze při spuštění aplikace na méně instancí. V tomto článku se naučíte vytvořit aplikaci v **PremiumV2** úroveň, nebo škálování aplikace na **PremiumV2** vrstvy.

## <a name="prerequisites"></a>Požadavky

Pro škálování webové aplikace do **PremiumV2**, musíte mít webovou aplikaci ve službě Azure App Service, který je spuštěn v cenovou úroveň, která je nižší než **PremiumV2**.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>PremiumV2 dostupnosti

Úroveň PremiumV2 je aktuálně dostupné pro aplikační služby na _Windows_ pouze. Kontejnery Linux se ještě nepodporují.

PremiumV2 je již k dispozici v oblastech nejvíce Azure a rostoucí. Pokud chcete zobrazit, pokud je k dispozici ve vašem regionu, spusťte následující příkaz rozhraní příkazového řádku Azure [prostředí cloudu Azure](../cloud-shell/overview.md):

```azurecli-interactive
az appservice list-locations --sku P1V2
```

Pokud se zobrazí chyba při vytváření aplikace nebo služby App Service vytvoření plánu, pak **PremiumV2** pravděpodobně není k dispozici pro vaši oblast výběru.

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Vytvoření aplikace v PremiumV2 vrstvy

Cenová úroveň aplikace služby App Service je definována v [plán služby App Service](azure-web-sites-web-hosting-plans-in-depth-overview.md) spuštěnou v. Plán služby App Service můžete vytvořit samostatně nebo jako součást vytváření webové aplikace.

Při konfiguraci plánu služby App Service v <a href="https://portal.azure.com" target="_blank">portál Azure</a>, vyberte **cenová úroveň**. 

Vyberte **produkční**, pak vyberte **P1V2**, **P2V2**, nebo **P3V2**, pak klikněte na tlačítko **použít**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Pokud nevidíte **P1V2**, **P2V2**, a **P3V2** jako možnosti, buď **PremiumV2** není k dispozici ve vašem regionu podle vlastní volby, nebo jste Konfigurace plánu služby App Service pro Linux, který nepodporuje **PremiumV2**.

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Škálování stávající aplikace pro PremiumV2 vrstvy

Před škálování stávající aplikace pro **PremiumV2** vrstvy, ujistěte se, že **PremiumV2** je k dispozici ve vašem regionu. Informace najdete v tématu [PremiumV2 dostupnosti](#availability). Pokud není k dispozici ve vašem regionu, přečtěte si téma [škálování z nepodporované oblasti](#unsupported).

V závislosti na prostředí hostování vertikálním navýšení kapacity vyžadují zvláštní postup. 

V <a href="https://portal.azure.com" target="_blank">portál Azure</a>, otevřete stránku aplikace služby App Service.

V levém navigačním panelu stránky aplikace služby App Service, vyberte **škálování (plán služby App Service)**.

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Vyberte **produkční**, pak vyberte **P1V2**, **P2V2**, nebo **P3V2**, pak klikněte na tlačítko **použít**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Pokud vaše operace skončí úspěšně, vaší aplikace přehled stránka zobrazuje, že je nyní **PremiumV2** vrstvy.

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Pokud dojde k chybě

K vrstvě PremiumV2 nelze škálovat některé plány služby App Service. Pokud vaše operace škálování vrátí chybu, je třeba nový plán aplikační služby pro vaši aplikaci.

Vytvoření _Windows_ plán služby App Service ve stejné oblasti a prostředků skupině jako existující aplikace služby App Service. Postupujte podle kroků v [vytvořit aplikaci ve vrstvě PremiumV2](#create) nastavit na **PremiumV2** vrstvy. Pokud chcete, použijte stejnou konfiguraci škálovaného na více systémů jako existující plán služby App Service (počet instancí, škálování a tak dále).

Znovu otevřete stránku aplikace služby App Service. V levém navigačním panelu služby App Service, vyberte **plán služby App Service změnu**.

![](media/app-service-configure-premium-tier/change-plan.png)

Vyberte plán aplikační služby, kterou jste vytvořili.

![](media/app-service-configure-premium-tier/select-plan.png)

Po dokončení operace změny, vaše aplikace běží v **PremiumV2** vrstvy.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-region"></a>Vertikální navýšení kapacity z nepodporované oblasti

Pokud vaše aplikace běží v oblasti, kde **PremiumV2** není k dispozici ještě aplikace můžete přesunout do jiné oblasti využívat výhod **PremiumV2**. Máte dvě možnosti:

- Vytvoření nové aplikace v **PremiumV2** plánování a pak znovu nasaďte kódu aplikace. Postupujte podle kroků v [vytvořit aplikaci ve vrstvě PremiumV2](#create) nastavit na **PremiumV2** vrstvy. V případě potřeby použijte stejnou konfiguraci škálovaného na více systémů jako existující plán služby App Service (počet instancí, škálování a tak dále).
- Pokud vaše aplikace, které už běží v existující **Premium** vrstvy, potom můžete klonování aplikaci se všemi nastavení aplikace, připojovací řetězce a konfigurace nasazení.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    V **klonování aplikace** stránky, můžete vytvořit plán služby App Service v oblasti a zadejte nastavení, které chcete klonovat.

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

Můžete automatizovat vytváření aplikace v **PremiumV2** vrstvy pomocí skriptů, pomocí [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) nebo [prostředí Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>Azure CLI

Následující příkaz vytvoří plán služby App Service v _P1V2_. Můžete ho spustit v prostředí cloudu. Možnosti pro `--sku` jsou P1V2, _P2V2_, a _P3V2_.

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
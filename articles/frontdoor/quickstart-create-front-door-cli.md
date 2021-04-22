---
title: 'Rychlý Start: nastavení vysoké dostupnosti s využitím služby Azure – přední vrátka – Azure CLI'
description: V tomto rychlém startu se dozvíte, jak pomocí Azure CLI vytvořit vysoce dostupnou globální webovou aplikaci s vysokou dostupností a vysoce výkonným globálním webovým serverem.
services: front-door
author: duongau
manager: KumudD
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/19/2021
ms.author: duau
ms.openlocfilehash: 3567d5af31b0c7bc2443e3d02426a5bb7aba06f7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861998"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-cli"></a>Rychlý Start: vytvoření přední dveře pro globální webovou aplikaci s vysokou dostupností pomocí Azure CLI

Začínáme s předními dveřmi Azure pomocí rozhraní příkazového řádku Azure k vytvoření vysoce dostupné a vysoce výkonné globální webové aplikace.

Přední dveře přesměrují webový provoz na konkrétní prostředky v back-end fondu. Definovali jste doménu front-end, přidáte prostředky do back-endu fondu a vytvoříte pravidlo směrování. Tento článek používá jednoduchou konfiguraci jednoho back-end fondu se dvěma prostředky webové aplikace a jedno pravidlo směrování s použitím výchozí cesty, která odpovídá "/*".

:::image type="content" source="media/quickstart-create-front-door/environment-diagram.png" alt-text="Diagram prostředí nasazení front-dveří pomocí Azure CLI." border="false":::

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Rozhraní příkazového řádku Azure je nainstalované místně nebo Azure Cloud Shell
- Zajistěte, aby do Azure CLI bylo přidané rozšíření front-dveří.

```azurecli-interactive 
az extension add --name front-door
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte použít Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

V Azure přidělíte související prostředky skupině prostředků. Můžete použít buď existující skupinu prostředků, nebo vytvořit novou.

Pro tento rychlý Start potřebujete dvě skupiny prostředků. Jednu v *střed USA* a druhý v *střed USA – jih*.

Vytvořte skupinu prostředků pomocí [AZ Group Create](/cli/azure/group#az_group_create):

```azurecli-interactive
az group create \
    --name myRGFDCentral \
    --location centralus

az group create \
    --name myRGFDEast \
    --location eastus
```

## <a name="create-two-instances-of-a-web-app"></a>Vytvoření dvou instancí webové aplikace

Pro tento rychlý Start se vyžadují dvě instance webové aplikace, které běží v různých oblastech Azure. Instance webové aplikace běží v režimu aktivní/aktivní, takže jeden z nich může obsluhovat provoz.

Pokud ještě nemáte webovou aplikaci, použijte následující skript k nastavení dvou ukázkových webových aplikací.

### <a name="create-app-service-plans"></a>Vytvoření plánů služby App Service

Než budete moct vytvořit webové aplikace, budete potřebovat dva plány služby App Service, jednu v *střed USA* a druhý v *východní USA*.

Vytvoření plánů služby App Service pomocí [AZ AppService Plan Create](/cli/azure/appservice/plan#az_appservice_plan_create&preserve-view=true):

```azurecli-interactive
az appservice plan create \
--name myAppServicePlanCentralUS \
--resource-group myRGFDCentral

az appservice plan create \
--name myAppServicePlanEastUS \
--resource-group myRGFDEast
```

### <a name="create-web-apps"></a>Vytváření webových aplikací

Spuštěním následujících příkazů se v předchozím kroku vytvoří webová aplikace v každém z plánů služby App Service. Názvy webových aplikací musí být globálně jedinečné.

Vytvoření webové aplikace pomocí [AZ WebApp Create](/cli/azure/webapp#az_webapp_create&preserve-view=true):

```azurecli-interactive
az webapp create \
--name WebAppContoso-1 \
--resource-group myRGFDCentral \
--plan myAppServicePlanCentralUS 

az webapp create \
--name WebAppContoso-2 \
--resource-group myRGFDEast \
--plan myAppServicePlanEastUS
```

Poznamenejte si výchozí název hostitele každé webové aplikace, abyste mohli definovat back-endové adresy při nasazení předních dveří v dalším kroku.

## <a name="create-the-front-door"></a>Vytvoření front-dveří

Vytvořte základní front-dvířka s výchozím nastavením vyrovnávání zatížení, sondou stavu a pravidly směrování, a to spuštěním následujícího postupu:

Vytvoření front-dveří pomocí [AZ Network front-dvířk Create](/cli/azure/network/front-door#az_network_front_door_create&preserve-view=true):

```azurecli-interactive
az network front-door create \
--resource-group myRGFDCentral \
--name contoso-frontend \
--accepted-protocols http https \
--backend-address webappcontoso-1.azurewebsites.net webappcontoso-2.azurewebsites.net 
```

**--Resource-Group:** Zadejte skupinu prostředků, do které chcete nasadit přední dveře.

**--Název:** Zadejte globálně jedinečný název pro vaše přední dveře Azure. 

**--přijato – protokoly:** Přijaté hodnoty jsou **http** a **https**. Pokud chcete použít obojí, specifické pro oddělené mezerou.

**--back-endové adresy:** Zadejte název hostitele Web Apps, který je oddělen mezerou.

Po úspěšném dokončení nasazení si poznamenejte název hostitele v oddílu *frontEndpoints* .

## <a name="test-the-front-door"></a>Test předních dveří

Otevřete webový prohlížeč a zadejte název hostitele, který se získá z příkazů. Přední dveře budou směrovat váš požadavek na jeden ze zdrojů back-endu.

:::image type="content" source="./media/quickstart-create-front-door-cli/front-door-testing-page.png" alt-text="Stránka testování front-dveří":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete prostředky, které jste vytvořili v rámci front, odstraňte obě skupiny prostředků. Když odstraníte skupinu prostředků, odstraníte také přední dveře a všechny související prostředky. 

Pokud chcete odstranit skupinu prostředků, použijte [AZ Group Delete](/cli/azure/group#az_group_delete&preserve-view=true):

```azurecli-interactive
az group delete \
--name myRGFDCentral 

az group delete \
--name myRGFDEast
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili:
* Front Door
* Dvě webové aplikace

Další informace o tom, jak přidat vlastní doménu do předních dveří, najdete v výukových kurzech pro přední dveře.

> [!div class="nextstepaction"]
> [Přidání vlastní domény](front-door-custom-domain.md)

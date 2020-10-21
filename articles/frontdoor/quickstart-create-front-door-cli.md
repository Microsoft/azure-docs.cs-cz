---
title: 'Rychlý Start: nastavení vysoké dostupnosti s využitím služby Azure – přední vrátka – Azure CLI'
description: V tomto rychlém startu se dozvíte, jak pomocí Azure CLI vytvořit vysoce dostupnou globální webovou aplikaci s vysokou dostupností a vysoce výkonným globálním webovým serverem.
services: front-door
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/21/2020
ms.author: duau
ms.openlocfilehash: a24c0fdb244f87dbf281bcf59b5e1986a215142a
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92275622"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-cli"></a>Rychlý Start: vytvoření přední dveře pro globální webovou aplikaci s vysokou dostupností pomocí Azure CLI

Začínáme s předními dveřmi Azure pomocí rozhraní příkazového řádku Azure k vytvoření vysoce dostupné a vysoce výkonné globální webové aplikace.

Přední dveře přesměrují webový provoz na konkrétní prostředky v back-end fondu. Definovali jste doménu front-end, přidáte prostředky do back-endu fondu a vytvoříte pravidlo směrování. Tento článek používá jednoduchou konfiguraci jednoho back-end fondu se dvěma prostředky webové aplikace a jedno pravidlo směrování s použitím výchozí cesty, která odpovídá "/*".

## <a name="prerequisites"></a>Předpoklady

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

Vytvořte skupinu prostředků pomocí [AZ Group Create](/cli/azure/group?view=azure-cli-latest#az-group-create&preserve-view=true):

```azurecli-interactive
az group create \
    --name myRGFDCentral \
    --location centralus

az group create \
    --name myRGFDSouthCentral \
    --location southcentralus
```

## <a name="create-two-instances-of-a-web-app"></a>Vytvoření dvou instancí webové aplikace

Pro tento rychlý Start se vyžadují dvě instance webové aplikace, které běží v různých oblastech Azure. Instance webové aplikace běží v režimu aktivní/aktivní, takže jeden z nich může obsluhovat provoz.

Pokud ještě nemáte webovou aplikaci, použijte následující skript k nastavení dvou ukázkových webových aplikací.

### <a name="create-app-service-plans"></a>Vytvoření plánů služby App Service

Než budete moct vytvořit webové aplikace, budete potřebovat dva plány služby App Service, jednu v *střed USA* a druhý v *střed USA – jih*.

Vytvoření plánů služby App Service pomocí [AZ AppService Plan Create](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create&preserve-view=true):

```azurecli-interactive
az appservice plan create \
--name myAppServicePlanCentralUS \
--resource-group myRGFDCentral

az appservice plan create \
--name myAppServicePlanSouthCentralUS \
-resource-groupg myRGFDSouthCentral
```

### <a name="create-web-apps"></a>Vytváření webových aplikací

Spuštěním následujících příkazů se v předchozím kroku vytvoří webová aplikace v každém z plánů služby App Service. Názvy webových aplikací musí být globálně jedinečné.

Vytvoření webové aplikace pomocí [AZ WebApp Create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create&preserve-view=true):

```azurecli-interactive
az webapp create \
--name WebAppContoso1 \
--resource-group myRGFDCentral \
--plan myAppServicePlanCentralUS 

az webapp create \
--name WebAppContoso2 \
--resource-group myRGFDSouthCentral \
--plan myAppServicePlanSouthCentralUS
```

Poznamenejte si výchozí název hostitele každé webové aplikace, abyste mohli definovat back-endové adresy při nasazení předních dveří v dalším kroku.

## <a name="create-the-front-door"></a>Vytvoření front-dveří

Vytvořte základní front-dvířka s výchozím nastavením vyrovnávání zatížení, sondou stavu a pravidly směrování, a to spuštěním následujícího postupu:

Vytvoření front-dveří pomocí [AZ Network front-dvířk Create](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext_front_door_az_network_front_door_create&preserve-view=true):

```azurecli-interactive
az network front-door create \
--resource-group myRGFDCentral \
--name contoso-frontend \
--accepted-protocols http https \
--backend-address webappcontoso1.azurewebsites.net webappcontoso2.azurewebsites.net 
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

Pokud chcete odstranit skupinu prostředků, použijte [AZ Group Delete](/cli/azure/group?view=azure-cli-latest#az_group_delete&preserve-view=true):

```azurecli-interactive
az group delete \
--name myRGFDCentral 

az group delete \
--name myRGFDSouthCentral
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili:
* Front Door
* Dvě webové aplikace

Další informace o tom, jak přidat vlastní doménu do předních dveří, najdete v výukových kurzech pro přední dveře.

> [!div class="nextstepaction"]
> [Přidání vlastní domény](front-door-custom-domain.md)
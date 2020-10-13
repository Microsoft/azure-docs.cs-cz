---
title: Kurz – nasazení z geograficky replikovaného registru
description: Nasazení webové aplikace se systémem Linux do dvou různých oblastí Azure pomocí Image kontejneru z geograficky replikovaného registru kontejnerů Azure. Druhá část třídílné série.
ms.topic: tutorial
ms.date: 08/20/2018
ms.custom: seodec18, mvc
ms.openlocfilehash: bcbce3c60aa0b4613b1dd1840c335c800193e549
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328684"
---
# <a name="tutorial-deploy-a-web-app-from-a-geo-replicated-azure-container-registry"></a>Kurz: nasazení webové aplikace z geograficky replikovaného registru kontejnerů Azure

Toto je druhá část z třídílné série kurzů. V [první části](container-registry-tutorial-prepare-registry.md) jste vytvořili privátní, geograficky replikovaný registr kontejneru a ze zdroje jste sestavili image kontejneru, kterou jste vložili do registru. V tomto článku nasadíte kontejner do instancí webových aplikací ve dvou různých oblastech Azure a využijete tak aspekt blízkosti sítě u geograficky replikovaného registru. Každá instance pak načte image kontejneru z nejbližšího registru.

V této druhé části série kurzů se naučíte:

> [!div class="checklist"]
> * Nasadit image kontejneru do dvou instancí služby *Web Apps for Containers*.
> * Ověřit nasazenou aplikaci.

Pokud jste ještě nevytvořili geograficky replikovaný registr a nevložili jste image kontejnerizované ukázkové aplikace do registru, vraťte se do předchozího kurzu v této sérii: [Příprava geograficky replikovaného registru kontejnerů Azure](container-registry-tutorial-prepare-registry.md).

V dalším článku této série aplikaci aktualizujete a pak vložíte aktualizovanou image kontejneru do registru. Nakonec přejdete na každou ze spuštěných instancí webové aplikace a přesvědčíte se, že se změny automaticky projevily v obou instancích. Uvidíte tak, jak v praxi funguje geografická replikace registru kontejneru Azure a webhooky.

## <a name="automatic-deployment-to-web-apps-for-containers"></a>Automatické nasazení do služby Web Apps for Containers

Služba Azure Container Registry poskytuje podporu pro nasazování kontejnerizovaných aplikací do služby [Web Apps for Containers](../app-service/containers/index.yml). V tomto kurzu nasadíte pomocí webu Azure Portal image kontejneru, kterou jste vytvořili v předchozím kurzu, do dvou plánů webových aplikací umístěných v různých oblastech Azure.

Když nasadíte webovou aplikaci z image kontejneru v registru a máte geograficky replikovaný registr ve stejné oblasti, vytvoří vám služba Azure Container Registry [webhook](container-registry-webhook.md) pro nasazení image. Když do úložiště kontejnerů vložíte novou image, převezme webhook tuto změnu a automaticky nasadí novou image kontejneru do vaší webové aplikace.

## <a name="deploy-a-web-app-for-containers-instance"></a>Nasazení instance služby Web App for Containers

V tomto kroku vytvoříte instanci služby Web App for Containers v oblasti *USA – západ*.

Přihlaste se na portál [Azure Portal](https://portal.azure.com) a přejděte do registru, který jste vytvořili v předchozím kurzu.

Vyberte **úložiště**  >  **ACR-HelloWorld**, potom klikněte pravým tlačítkem na značku **v1** v části **značky** a vyberte **nasadit do webové aplikace**:

![Nasazení do služby App Service na portálu Azure Portal][deploy-app-portal-01]

Pokud je možnost Nasadit do webové aplikace zakázaná, pravděpodobně jste nepovolili uživatele s rolí správce registru podle pokynů v části [Vytvoření registru kontejneru](container-registry-tutorial-prepare-registry.md#create-a-container-registry) v prvním kurzu. Můžete povolit uživatele správce v **Nastavení**  >  **přístupové klíče** v Azure Portal.

V části **Web App for Containers**, která se zobrazí po výběru možnosti Nasadit do webové aplikace, zadejte u jednotlivých nastavení následující hodnoty:

| Nastavení | Hodnota |
|---|---|
| **Název lokality** | Globálně jedinečný název webové aplikace. V tomto příkladu používáme formát `<acrName>-westus`, abychom mohli snadno identifikovat, ze kterého registru a oblasti je webová aplikace nasazená. |
| **Skupina prostředků** | **Použít existující** > `myResourceGroup` |
| **Plán/umístění služby App Service** | Vytvořte nový plán s názvem `plan-westus` v oblasti **USA – západ**. |
| **Image** | `acr-helloworld:v1` |
| **Operační systém** | Linux |

> [!NOTE]
> Při vytváření nového plánu služby App Service pro nasazení kontejnerové aplikace je automaticky vybrán výchozí plán pro hostování aplikace. Výchozí plán závisí na nastavení operačního systému.

Vyberte **Vytvořit** a webovou aplikaci zřiďte v oblasti *USA – západ*.

![Snímek obrazovky ukazuje Web App for Containers s zvýrazněným tlačítkem pro vytvoření.][deploy-app-portal-02]

## <a name="view-the-deployed-web-app"></a>Zobrazení nasazené webové aplikace

Po dokončení nasazování můžete běžící aplikaci zobrazit tak, že přejdete v prohlížeči na její adresu URL.

Na portálu vyberte **App Services** a pak vyberte webovou aplikaci, kterou jste zřídili v předchozím kroku. V tomto příkladu je název webové aplikace *uniqueregistryname-westus*.

Výběrem hypertextového odkazu na adresu URL webové aplikace v pravé horní části přehledu **App Service** zobrazíte běžící aplikaci v prohlížeči.

![Snímek obrazovky s zvýrazněnou adresou URL webové aplikace zobrazuje přehled App Service.][deploy-app-portal-04]

Po nasazení image Dockeru z geograficky replikovaného registru kontejneru se v lokalitě zobrazí obrázek představující oblast Azure, která je hostitelem registru kontejneru.

![Snímek obrazovky se zobrazí nasazená webová aplikace zobrazená v prohlížeči.][deployed-app-westus]

## <a name="deploy-second-web-app-for-containers-instance"></a>Nasazení druhé instance služby Web App for Containers

Podle postupu uvedeného v předchozí části nasaďte druhou webovou aplikaci do oblasti *USA – východ*. V části **Web App for Containers** zadejte následující hodnoty:

| Nastavení | Hodnota |
|---|---|
| **Název lokality** | Globálně jedinečný název webové aplikace. V tomto příkladu používáme formát `<acrName>-eastus`, abychom mohli snadno identifikovat, ze kterého registru a oblasti je webová aplikace nasazená. |
| **Skupina prostředků** | **Použít existující** > `myResourceGroup` |
| **Plán/umístění služby App Service** | Vytvořte nový plán s názvem `plan-eastus` v oblasti **USA – východ**. |
| **Image** | `acr-helloworld:v1` |
| **Operační systém** | Linux |

Vyberte **Vytvořit** a webovou aplikaci zřiďte v oblasti *USA – východ*.

![Snímek obrazovky ukazuje Web App for Containers okno vytvořit s zvýrazněným tlačítkem pro vytvoření.][deploy-app-portal-06]

## <a name="view-the-second-deployed-web-app"></a>Zobrazení druhé nasazené webové aplikace

Stejně jako v předchozí části můžete běžící aplikaci zobrazit tak, že přejdete v prohlížeči na její adresu URL.

Na portálu vyberte **App Services** a pak vyberte webovou aplikaci, kterou jste zřídili v předchozím kroku. V tomto příkladu je název webové aplikace *uniqueregistryname-eastus*.

Vyberte adresu URL webové aplikace v pravém horním rohu **přehledu App Service** a zobrazte běžící aplikaci v prohlížeči.

![Webová aplikace s linuxovou konfigurací na portálu Azure Portal][deploy-app-portal-07]

Po nasazení image Dockeru z geograficky replikovaného registru kontejneru se v lokalitě zobrazí obrázek představující oblast Azure, která je hostitelem registru kontejneru.

![Nasazená webová aplikace zobrazená v prohlížeči][deployed-app-eastus]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili dvě instance služby Web App for Containers z geograficky replikovaného registru kontejneru Azure.

Přejděte na další kurz, ve kterém provedete aktualizaci a pak nasadíte novou image kontejneru do registru kontejneru. Pak ověříte, že webové aplikace běžící v obou oblastech se automaticky aktualizovaly.

> [!div class="nextstepaction"]
> [Nasazení aktualizace image v geograficky replikovaném kontejneru](./container-registry-tutorial-deploy-update.md)

<!-- IMAGES -->
[deploy-app-portal-01]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-01.png
[deploy-app-portal-02]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-02.png
[deploy-app-portal-03]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-03.png
[deploy-app-portal-04]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-04.png
[deploy-app-portal-05]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-05.png
[deploy-app-portal-06]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-06.png
[deploy-app-portal-07]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-07.png
[deployed-app-westus]: ./media/container-registry-tutorial-deploy-app/deployed-app-westus.png
[deployed-app-eastus]: ./media/container-registry-tutorial-deploy-app/deployed-app-eastus.png
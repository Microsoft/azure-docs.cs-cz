---
title: Rychlý start – spuštění aplikace ve službě Azure Container Instances – portál
description: V tomto rychlém startu použijete Azure portal k nasazení aplikace typu kontejner Dockeru pro spuštění v izolovaného kontejneru ve službě Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 8f547977e544854e281e1c6be442607d55149e5e
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53190284"
---
# <a name="quickstart-run-a-container-application-in-azure-container-instances-in-the-azure-portal"></a>Rychlý start: Spuštění aplikace typu kontejner ve službě Azure Container Instances na webu Azure Portal

Spouštějte kontejnery Dockeru v Azure rychle a snadno pomocí Azure Container Instances. Nemusíte nasazovat virtuální počítače ani používat úplnou platformu orchestrace kontejnerů jako Kubernetes. V tomto rychlém startu vytvoříte pomocí webu Azure Portal kontejner v Azure a zpřístupníte jeho aplikaci s použitím plně kvalifikovaného názvu domény. Po konfiguraci několika nastavení a nasazení kontejneru můžete přejít na běžící aplikaci:

![Aplikace nasazená do služby Azure Container Instances zobrazená v prohlížeči][aci-portal-07]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet][azure-free-account] před tím, než začnete.

## <a name="create-a-container-instance"></a>Vytvoření instance kontejneru

Vyberte **Vytvořit prostředek** > **Kontejnery** > **Container Instances**.

![Zahájení vytváření nové instance kontejneru na webu Azure Portal][aci-portal-01]

Do textových polí **Název kontejneru**, **Image kontejneru** a **Skupina prostředků** zadejte následující hodnoty. U ostatních hodnot ponechte výchozí nastavení a vyberte **OK**.

* Název kontejneru: `mycontainer`
* Image kontejneru: `microsoft/aci-helloworld`
* Skupina zdrojů: **Vytvořit nový** > `myResourceGroup`

![Konfigurace základního nastavení pro novou instanci kontejneru na webu Azure Portal][aci-portal-03]

Ve službě Azure Container Instances můžete vytvářet kontejnery Windows i Linuxu. Pro účely tohoto rychlého startu ponechte výchozí nastavení **Linux**, aby se nasadila image `microsoft/aci-helloworld` založená na Linuxu.

V části **Konfigurace** zadejte **Popisek názvu DNS** pro váš kontejner. Název musí být jedinečný v rámci oblasti Azure, ve které instanci kontejneru vytváříte. Váš kontejner bude veřejně dostupný na adrese `<dns-name-label>.<region>.azurecontainer.io`.

U ostatních nastavení v části **Konfigurace** ponechte jejich výchozí hodnoty a výběrem **OK** ověřte konfiguraci.

![Konfigurace nové instance kontejneru na webu Azure Portal][aci-portal-04]

Po dokončení ověření se zobrazí souhrn nastavení kontejneru. Vyberte **OK** a odešlete žádost o nasazení kontejneru.

![Souhrn nastavení pro novou instanci kontejneru na webu Azure Portal][aci-portal-05]

Po zahájení nasazení se zobrazí oznámení, které informuje o průběhu nasazení. Po nasazení skupiny kontejnerů se zobrazí další oznámení.

![Průběh vytváření nové instance kontejneru na webu Azure Portal][aci-portal-08]

Přechodem na položky **Skupiny prostředků** > **myResourceGroup** > **mycontainer** otevřete přehled skupiny kontejnerů. Poznamenejte si **Plně kvalifikovaný název domény** instance kontejneru a také její **Stav**.

![Přehled skupiny kontejnerů na webu Azure Portal][aci-portal-06]

Jakmile se její **Stav** změní na *Spuštěno*, přejděte v prohlížeči na plně kvalifikovaný název domény kontejneru.

![Aplikace nasazená pomocí služby Azure Container Instances zobrazená v prohlížeči][aci-portal-07]

Blahopřejeme! Prostým nakonfigurováním několika nastavení jste nasadili veřejně přístupnou aplikaci ve službě Azure Container Instances.

## <a name="view-container-logs"></a>Zobrazení protokolů kontejneru

Prohlížení protokolů pro instanci kontejneru je užitečné při řešení problémů s kontejnerem nebo aplikací, která se v něm spouští.

Pokud chcete zobrazit protokoly kontejneru, v části **Nastavení** vyberte **Kontejnery** a potom **Protokoly**. Měl by se zobrazit požadavek HTTP GET, který se vygeneroval, když jste aplikaci zobrazili v prohlížeči.

![Protokoly kontejneru na webu Azure Portal][aci-portal-11]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete s kontejnerem hotovi, vyberte **Přehled** pro instanci kontejneru *mycontainer* a pak vyberte **Odstranit**.

![Odstranění instance kontejneru na webu Azure Portal][aci-portal-09]

Jakmile se zobrazí potvrzovací dialogové okno, vyberte **Ano**.

![Potvrzení odstranění instance kontejneru na webu Azure Portal][aci-portal-10]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili instanci kontejneru Azure z image ve veřejném registru Docker Hub. Pokud chcete sestavit image kontejneru a nasadit ji z privátního registru kontejnerů Azure, pokračujte ke kurzu služby Azure Container Instances.

> [!div class="nextstepaction"]
> [Kurz služby Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-portal-07]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png
[aci-portal-09]: ./media/container-instances-quickstart-portal/qs-portal-09.png
[aci-portal-10]: ./media/container-instances-quickstart-portal/qs-portal-10.png
[aci-portal-11]: ./media/container-instances-quickstart-portal/qs-portal-11.png

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/
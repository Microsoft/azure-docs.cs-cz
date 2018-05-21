---
title: Rychlý start – Vytvoření prvního kontejneru služby Azure Container Instances pomocí webu Azure Portal
description: V tomto rychlém startu pomocí webu Azure Portal nasadíte kontejner ve službě Azure Container Instances.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 6aa6fb27b2aa7c8b9614e5812fadc629b1e185f8
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>Rychlý start: Vytvoření prvního kontejneru ve službě Azure Container Instances

Služba Azure Container Instances usnadňuje vytváření a správu kontejnerů Dockeru v Azure, aniž byste museli zřizovat virtuální počítače nebo používat službu vyšší úrovně. V tomto rychlém startu pomocí webu Azure Portal vytvoříte kontejner v Azure a zveřejníte ho na internetu s použitím plně kvalifikovaného názvu domény. Po nakonfigurování několika nastavení se ve vašem prohlížeči zobrazí následující:

![Aplikace nasazená pomocí služby Azure Container Instances zobrazená v prohlížeči][aci-portal-07]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet][azure-free-account] před tím, než začnete.

## <a name="create-a-container-instance"></a>Vytvoření instance kontejneru

Vyberte **Vytvořit prostředek** > **Kontejnery** > **Container Instances**.

![Zahájení vytváření nové instance kontejneru na webu Azure Portal][aci-portal-01]

Do textových polí **Název kontejneru**, **Image kontejneru** a **Skupina prostředků** zadejte následující hodnoty. U ostatních hodnot ponechte výchozí nastavení a vyberte **OK**.

* Název kontejneru: `mycontainer`
* Image kontejneru: `microsoft/aci-helloworld`
* Skupina prostředků: `myResourceGroup`

![Konfigurace základního nastavení pro novou instanci kontejneru na webu Azure Portal][aci-portal-03]

Ve službě Azure Container Instances můžete vytvářet kontejnery Windows i Linuxu. Pro účely tohoto rychlého startu ponechte výchozí nastavení **Linux**, aby se nasadila image `microsoft/aci-helloworld` založená na Linuxu.

V části **Konfigurace** zadejte **Popisek názvu DNS** pro váš kontejner. Název musí být jedinečný v rámci oblasti Azure, ve které instanci kontejneru vytváříte. Váš kontejner bude veřejně dostupný na adrese `<dns-name-label>.<region>.azurecontainer.io`.

U ostatních nastavení v části **Konfigurace** ponechte jejich výchozí hodnoty a výběrem **OK** ověřte konfiguraci.

![Konfigurace nové instance kontejneru na webu Azure Portal][aci-portal-04]

Po dokončení ověření se zobrazí souhrn nastavení kontejneru. Vyberte **OK** a odešlete žádost o nasazení kontejneru.

![Souhrn nastavení pro novou instanci kontejneru na webu Azure Portal][aci-portal-05]

Po zahájení nasazení se na vašem řídicím panelu portálu zobrazí dlaždice oznamující průběh nasazení. Po nasazení se na dlaždici zobrazí nová instance kontejneru.

![Průběh vytváření nové instance kontejneru na webu Azure Portal][aci-portal-08]

Výběrem instance kontejneru **mycontainer** zobrazte její vlastnosti. Poznamenejte si **Plně kvalifikovaný název domény** instance kontejneru a také její **Stav**.

![Přehled skupiny kontejnerů na webu Azure Portal][aci-portal-06]

Jakmile se její **Stav** změní na *Spuštěno*, přejděte v prohlížeči na plně kvalifikovaný název domény kontejneru.

![Aplikace nasazená pomocí služby Azure Container Instances zobrazená v prohlížeči][aci-portal-07]

Blahopřejeme! Prostým nakonfigurováním několika nastavení jste nasadili veřejně přístupnou aplikaci ve službě Azure Container Instances.

## <a name="view-container-logs"></a>Zobrazení protokolů kontejneru

Prohlížení protokolů pro instanci kontejneru je užitečné při řešení problémů s kontejnerem nebo aplikací, která se v něm spouští.

Pokud chcete zobrazit protokoly kontejneru, v části **NASTAVENÍ** vyberte **Kontejnery** a pak **Protokoly**. Měl by se zobrazit požadavek HTTP GET, který se vygeneroval, když jste aplikaci zobrazili v prohlížeči.

![Protokoly kontejneru na webu Azure Portal][aci-portal-11]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete s kontejnerem hotovi, vyberte **Přehled** pro instanci kontejneru *mycontainer* a pak vyberte **Odstranit**.

![Odstranění instance kontejneru na webu Azure Portal][aci-portal-09]

Jakmile se zobrazí potvrzovací dialogové okno, vyberte **Ano**.

![Potvrzení odstranění instance kontejneru na webu Azure Portal][aci-portal-10]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili instanci kontejneru Azure z image ve veřejném registru Docker Hub. Pokud si chcete sami sestavit image kontejneru a nasadit ji do služby Azure Container Instances z privátního registru kontejnerů Azure, pokračujte ke kurzu služby Azure Container Instances.

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
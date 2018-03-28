---
title: Rychlý start – Vytvoření prvního kontejneru služby Azure Container Instances pomocí webu Azure Portal
description: V tomto rychlém startu pomocí webu Azure Portal nasadíte kontejner ve službě Azure Container Instances.
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/19/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 18238c94ba9acff15497a666a63b65b95779d4ac
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>Rychlý start: Vytvoření prvního kontejneru ve službě Azure Container Instances

Azure Container Instances zjednodušuje vytváření a správu kontejnerů v Azure. V tomto rychlém startu vytvoříte kontejner v Azure a zveřejníte ho na internetu s použitím veřejné IP adresy. Tuto operaci provedete pomocí webu Azure Portal. Po několika kliknutích uvidíte ve svém prohlížeči toto:

![Aplikace nasazená pomocí služby Azure Container Instances zobrazená v prohlížeči][aci-portal-07]

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.

## <a name="create-a-container-instance"></a>Vytvoření instance kontejneru

Vyberte **Vytvořit prostředek** > **Kontejnery** > **Azure Container Instances (Preview)**.

![Zahájení vytváření nové instance kontejneru na webu Azure Portal][aci-portal-01]

Do textových polí **Název kontejneru**, **Image kontejneru** a **Skupina prostředků** zadejte následující hodnoty. U ostatních hodnot ponechte výchozí nastavení a klikněte na **OK**.

* Název kontejneru: `mycontainer`
* Image kontejneru: `microsoft/aci-helloworld`
* Skupina prostředků: `myResourceGroup`

![Konfigurace základního nastavení pro novou instanci kontejneru na webu Azure Portal][aci-portal-03]

Ve službě Azure Container Instances můžete vytvářet kontejnery Windows i Linuxu. V tomto rychlém startu ponecháme výchozí nastavení pro **Linux**, protože jsme v předchozím kroku zadali kontejner založený na Linuxu (`microsoft/aci-helloworld`).

U ostatních nastavení v části **Konfigurace** ponechte jejich výchozí hodnoty a kliknutím na **OK** ověřte konfiguraci.

![Konfigurace nové instance kontejneru na webu Azure Portal][aci-portal-04]

Po dokončení ověření se zobrazí souhrn nastavení kontejneru. Vyberte **OK** a odešlete žádost o nasazení kontejneru.

![Souhrn nastavení pro novou instanci kontejneru na webu Azure Portal][aci-portal-05]

Po zahájení nasazení se na váš řídicí panel portálu umístí dlaždice oznamující průběh nasazování. Po dokončení nasazení se dlaždice aktualizuje a zobrazí novou skupinu kontejnerů **mycontainer-myc1**.

![Průběh vytváření nové instance kontejneru na webu Azure Portal][aci-portal-08]

Vyberte skupinu kontejnerů **mycontainer-myc1** a zobrazte její vlastnosti. Poznamenejte si **IP adresu** skupiny kontejnerů a také **STAV** vašeho kontejneru.

![Přehled skupiny kontejnerů na webu Azure Portal][aci-portal-06]

Jakmile se stav kontejner změní na **Spuštěno**, přejděte na IP adresu, kterou jste si poznamenali v předchozím kroku, a zobrazte aplikaci hostovanou v novém kontejneru.

![Aplikace nasazená pomocí služby Azure Container Instances zobrazená v prohlížeči][aci-portal-07]

## <a name="delete-the-container"></a>Odstranění kontejneru
Po dokončení práce s kontejnerem vyberte skupinu kontejnerů **mycontainer-myc1** a poté klikněte na **Odstranit**.

![Odstranění instance kontejneru na webu Azure Portal][aci-portal-09]

To spustí dialogové okno pro potvrzení, po zobrazení výzvy vyberte volbu **Ano**.

![Potvrzení odstranění instance kontejneru na webu Azure Portal][aci-portal-10]

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-02]: ./media/container-instances-quickstart-portal/qs-portal-02.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-portal-07]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png
[aci-portal-09]: ./media/container-instances-quickstart-portal/qs-portal-09.png
[aci-portal-10]: ./media/container-instances-quickstart-portal/qs-portal-10.png

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili instanci kontejneru Azure z image ve veřejném úložišti Docker Hub. Pokud byste si chtěli sami vyzkoušet sestavení kontejneru a jeho nasazení do služby Azure Container Instances pomocí služby Azure Container Registry, pokračujte na kurz služby Azure Container Instances.

> [!div class="nextstepaction"]
> [Kurzy služby Azure Container Instances](./container-instances-tutorial-prepare-app.md)
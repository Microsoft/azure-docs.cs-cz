---
title: Rychlý Start – nasazení kontejneru Docker do instance kontejneru – portál
description: V tomto rychlém startu použijete Azure Portal k rychlému nasazení kontejnerové webové aplikace, která běží v izolované instanci kontejneru Azure.
ms.topic: quickstart
ms.date: 08/24/2020
ms.custom: seodec18, mvc, devx-track-js
ms.openlocfilehash: c8477bd91c3a02a2cd02d341c38c16da251902ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004802"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Rychlý Start: nasazení instance kontejneru v Azure pomocí Azure Portal

Použijte Azure Container Instances ke spouštění kontejnerů Docker bez serveru v Azure s využitím jednoduchosti a rychlosti. Pokud nepotřebujete úplnou platformu orchestrace kontejnerů, jako je třeba služba Azure Kubernetes, nasaďte aplikaci na vyžádání do instance kontejneru na vyžádání.

V tomto rychlém startu použijete Azure Portal k nasazení izolovaného kontejneru Docker a zpřístupníte jeho aplikaci s plně kvalifikovaným názvem domény (FQDN). Po konfiguraci několika nastavení a nasazení kontejneru můžete přejít na běžící aplikaci:

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="Aplikace nasazená pomocí služby Azure Container Instances zobrazená v prohlížeči":::

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet][azure-free-account].

## <a name="create-a-container-instance"></a>Vytvoření instance kontejneru

Vyberte Container Instances **vytvořit**  >  **kontejnery** prostředků  >  .

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-01.png" alt-text="Zahájení vytváření nové instance kontejneru na webu Azure Portal":::

Na stránce **základy** zadejte do textových polí **Skupina prostředků**, **název kontejneru** a **Image kontejneru** následující hodnoty. U ostatních hodnot ponechte výchozí nastavení a vyberte **OK**.

* Skupina prostředků: **vytvořit novou** > `myresourcegroup`
* Název kontejneru: `mycontainer`
* Zdroj obrázku: **obrázky rychlého** startu
* Image kontejneru: `mcr.microsoft.com/azuredocs/aci-helloworld` (Linux)

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-03.png" alt-text="Konfigurace základního nastavení pro novou instanci kontejneru na webu Azure Portal":::

V tomto rychlém startu použijete výchozí nastavení k nasazení veřejné `aci-helloworld` Image Microsoft. Tato ukázková bitová kopie systému Linux zapisuje malou webovou aplikaci napsanou v Node.js, která slouží jako statická stránka HTML. Můžete také přenést vlastní image kontejnerů uložené v Azure Container Registry, Docker Hub nebo v jiných registrech.

Na stránce **sítě** zadejte **popisek názvu DNS** pro váš kontejner. Název musí být jedinečný v rámci oblasti Azure, ve které vytvoříte instanci kontejneru. Váš kontejner bude veřejně dostupný na adrese `<dns-name-label>.<region>.azurecontainer.io`. Pokud se zobrazí chybová zpráva „Popisek názvu DNS není dostupný“, zkuste jiný popisek názvu DNS.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-04.png" alt-text="Konfigurace nastavení sítě pro novou instanci kontejneru v Azure Portal":::

U ostatních nastavení ponechte výchozí nastavení a pak vyberte **zkontrolovat + vytvořit**.

Po dokončení ověření se zobrazí souhrn nastavení kontejneru. Vyberte **vytvořit** a odešlete žádost o nasazení kontejneru.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-05.png" alt-text="Souhrn nastavení pro novou instanci kontejneru na webu Azure Portal":::

Po zahájení nasazení se zobrazí oznámení, které indikuje, že nasazení probíhá. Po nasazení skupiny kontejnerů se zobrazí další oznámení.

Otevřete přehled skupiny kontejnerů tak, že přejdete na **skupiny prostředků**  >  **myresourcegroup**  >  **myContainer**. Poznamenejte si **Plně kvalifikovaný název domény** instance kontejneru a také její **Stav**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-06.png" alt-text="Přehled skupiny kontejnerů na webu Azure Portal":::

Jakmile se její **Stav** změní na *Spuštěno*, přejděte v prohlížeči na plně kvalifikovaný název domény kontejneru.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="Aplikace nasazená pomocí služby Azure Container Instances zobrazená v prohlížeči":::

Gratulujeme! Prostým nakonfigurováním několika nastavení jste nasadili veřejně přístupnou aplikaci ve službě Azure Container Instances.

## <a name="view-container-logs"></a>Zobrazení protokolů kontejneru

Prohlížení protokolů pro instanci kontejneru je užitečné při řešení problémů s kontejnerem nebo aplikací, která se v něm spouští.

Pokud chcete zobrazit protokoly kontejneru, v části **Nastavení** vyberte **Kontejnery** a potom **Protokoly**. Měl by se zobrazit požadavek HTTP GET, který se vygeneroval, když jste aplikaci zobrazili v prohlížeči.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-11.png" alt-text="Protokoly kontejneru na webu Azure Portal":::


## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete s kontejnerem hotovi, vyberte **Přehled** pro instanci kontejneru *mycontainer* a pak vyberte **Odstranit**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-09.png" alt-text="Odstranění instance kontejneru v Azure Portal]":::

Jakmile se zobrazí potvrzovací dialogové okno, vyberte **Ano**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-10.png" alt-text="Odstranění potvrzení instance kontejneru v Azure Portal]":::

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili službu Azure Container instance z veřejné image Microsoft. Pokud si chcete sestavit image kontejneru a nasadit ji z privátního registru kontejnerů Azure, pokračujte ke kurzu služby Azure Container Instances.

> [!div class="nextstepaction"]
> [Kurz služby Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/
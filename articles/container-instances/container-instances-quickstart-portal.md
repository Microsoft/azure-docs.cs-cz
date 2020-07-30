---
title: Rychlý Start – nasazení kontejneru Docker do instance kontejneru – portál
description: V tomto rychlém startu použijete Azure Portal k rychlému nasazení kontejnerové webové aplikace, která běží v izolované instanci kontejneru Azure.
ms.topic: quickstart
ms.date: 03/09/2020
ms.custom: seodec18, mvc, devx-track-javascript
ms.openlocfilehash: 5f36c81abd462f6dfd59c42da71e096e07206cae
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408063"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Rychlý Start: nasazení instance kontejneru v Azure pomocí Azure Portal

Použijte Azure Container Instances ke spouštění kontejnerů Docker bez serveru v Azure s využitím jednoduchosti a rychlosti. Pokud nepotřebujete úplnou platformu orchestrace kontejnerů, jako je třeba služba Azure Kubernetes, nasaďte aplikaci na vyžádání do instance kontejneru na vyžádání.

V tomto rychlém startu použijete Azure Portal k nasazení izolovaného kontejneru Docker a zpřístupníte jeho aplikaci s plně kvalifikovaným názvem domény (FQDN). Po konfiguraci několika nastavení a nasazení kontejneru můžete přejít na běžící aplikaci:

![Aplikace nasazená do služby Azure Container Instances zobrazená v prohlížeči][aci-portal-07]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet][azure-free-account].

## <a name="create-a-container-instance"></a>Vytvoření instance kontejneru

Vyberte Container Instances **vytvořit**  >  **kontejnery**prostředků  >  **Container Instances**.

![Zahájení vytváření nové instance kontejneru na webu Azure Portal][aci-portal-01]

Na stránce **základy** zadejte do textových polí **Skupina prostředků**, **název kontejneru**a **Image kontejneru** následující hodnoty. U ostatních hodnot ponechte výchozí nastavení a vyberte **OK**.

* Skupina prostředků: **vytvořit novou** > `myresourcegroup`
* Název kontejneru: `mycontainer`
* Zdroj obrázku: **obrázky rychlého** startu
* Image kontejneru: `mcr.microsoft.com/azuredocs/aci-helloworld` (Linux)

![Konfigurace základního nastavení pro novou instanci kontejneru na webu Azure Portal][aci-portal-03]

V tomto rychlém startu použijete výchozí nastavení k nasazení veřejné `aci-helloworld` Image Microsoft. Tato ukázková bitová kopie systému Linux zapisuje malou webovou aplikaci napsanou v Node.js, která slouží jako statická stránka HTML. Můžete také přenést vlastní image kontejnerů uložené v Azure Container Registry, Docker Hub nebo v jiných registrech.

Na stránce **sítě** zadejte **popisek názvu DNS** pro váš kontejner. Název musí být jedinečný v rámci oblasti Azure, ve které vytvoříte instanci kontejneru. Váš kontejner bude veřejně dostupný na adrese `<dns-name-label>.<region>.azurecontainer.io`. Pokud se zobrazí chybová zpráva „Popisek názvu DNS není dostupný“, zkuste jiný popisek názvu DNS.

![Konfigurace nastavení sítě pro novou instanci kontejneru v Azure Portal][aci-portal-04]

U ostatních nastavení ponechte výchozí nastavení a pak vyberte **zkontrolovat + vytvořit**.

Po dokončení ověření se zobrazí souhrn nastavení kontejneru. Vyberte **vytvořit** a odešlete žádost o nasazení kontejneru.

![Souhrn nastavení pro novou instanci kontejneru na webu Azure Portal][aci-portal-05]

Po zahájení nasazení se zobrazí oznámení, které indikuje, že nasazení probíhá. Po nasazení skupiny kontejnerů se zobrazí další oznámení.

Otevřete přehled skupiny kontejnerů tak, že přejdete na **skupiny prostředků**  >  **myresourcegroup**  >  **myContainer**. Poznamenejte si **Plně kvalifikovaný název domény** instance kontejneru a také její **Stav**.

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

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili službu Azure Container instance z veřejné image Microsoft. Pokud si chcete sestavit image kontejneru a nasadit ji z privátního registru kontejnerů Azure, pokračujte ke kurzu služby Azure Container Instances.

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
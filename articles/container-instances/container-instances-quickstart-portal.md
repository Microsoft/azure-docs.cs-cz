---
title: Úvodní příručka – nasazení kontejneru Dockeru do instance kontejneru – portál
description: V tomto rychlém startu použijete portál Azure k rychlému nasazení kontejnerizované webové aplikace, která běží v izolované instanci kontejneru Azure.
ms.topic: quickstart
ms.date: 03/09/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: 7a872e955db46b76d3b12f8ffc38d4a8e497ea63
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79087997"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Úvodní příručka: Nasazení instance kontejneru v Azure pomocí portálu Azure

Instance kontejnerů Azure slouží ke spouštění kontejnerů Dockeru bez serveru v Azure s jednoduchostí a rychlostí. Nasaďte aplikaci na instanci kontejneru na vyžádání, když nepotřebujete úplnou platformu orchestrace kontejneru, jako je služba Azure Kubernetes Service.

V tomto rychlém startu použijete portál Azure k nasazení izolovaného kontejneru Dockeru a zpřístupníte jeho aplikaci s plně kvalifikovaným názvem domény (FQDN). Po konfiguraci několika nastavení a nasazení kontejneru můžete přejít na běžící aplikaci:

![Aplikace nasazená do služby Azure Container Instances zobrazená v prohlížeči][aci-portal-07]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,][azure-free-account] než začnete.

## <a name="create-a-container-instance"></a>Vytvoření instance kontejneru

Vyberte **možnost Vytvořit** > **instance kontejnerů****prostředků** > .

![Zahájení vytváření nové instance kontejneru na webu Azure Portal][aci-portal-01]

Na stránce **Základy** zadejte následující hodnoty do textových polí **Skupiny prostředků**, **Název kontejneru**a **Obrázek kontejneru.** U ostatních hodnot ponechte výchozí nastavení a vyberte **OK**.

* Skupina prostředků: **Vytvoření nového** > `myresourcegroup`
* Název kontejneru: `mycontainer`
* Zdroj obrázku: **Obrázky rychlého startu**
* Obrázek `mcr.microsoft.com/azuredocs/aci-helloworld` kontejneru: (Linux)

![Konfigurace základního nastavení pro novou instanci kontejneru na webu Azure Portal][aci-portal-03]

Pro tento rychlý start použijete výchozí nastavení `aci-helloworld` k nasazení veřejné image Microsoft. Tato ukázková bitová kopie Linuxu sbalí malou webovou aplikaci napsanou v souboru Node.js, která slouží statické stránce HTML. Můžete také přenést vlastní image kontejneru uložené v Azure Container Registry, Docker Hub nebo jiných registrech.

Na stránce **Síť** zadejte **popisek názvu DNS** pro kontejner. Název musí být jedinečný v oblasti Azure, kde vytvoříte instanci kontejneru. Váš kontejner bude veřejně dostupný na adrese `<dns-name-label>.<region>.azurecontainer.io`. Pokud se zobrazí chybová zpráva „Popisek názvu DNS není dostupný“, zkuste jiný popisek názvu DNS.

![Konfigurace nastavení sítě pro novou instanci kontejneru na webu Azure Portal][aci-portal-04]

Ostatní nastavení ponechejte ve výchozím nastavení a pak vyberte **Zkontrolovat + vytvořit**.

Po dokončení ověření se zobrazí souhrn nastavení kontejneru. Výběrem **možnosti Vytvořit** odešlete požadavek na nasazení kontejneru.

![Souhrn nastavení pro novou instanci kontejneru na webu Azure Portal][aci-portal-05]

Při spuštění nasazení se zobrazí oznámení oznamovat, že probíhá nasazení. Po nasazení skupiny kontejnerů se zobrazí další oznámení.

Otevřete přehled pro skupinu kontejnerů přechodem na **skupiny** > prostředků**myresourcegroup** > **mycontainer**. Poznamenejte si **Plně kvalifikovaný název domény** instance kontejneru a také její **Stav**.

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

V tomto rychlém startu jste vytvořili instanci kontejneru Azure z veřejné image Microsoftu. Pokud si chcete sestavit image kontejneru a nasadit ji z privátního registru kontejnerů Azure, pokračujte ke kurzu služby Azure Container Instances.

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
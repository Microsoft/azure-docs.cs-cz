---
title: Rychlý start – Vytvoření privátního registru Dockeru v Azure pomocí webu Azure Portal
description: Rychle se naučíte, jak vytvořit privátní registr Dockeru pomocí webu Azure Portal.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 1e039c465bf37e0ee5ca1db5837798680e27463d
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51278663"
---
# <a name="quickstart-create-a-container-registry-using-the-azure-portal"></a>Rychlý start: Vytvoření registru kontejnerů pomocí webu Azure Portal

Registr kontejnerů Azure je privátním registrem Dockeru v Azure, kde můžete ukládat a spravovat privátní image kontejnerů Dockeru. V tomto rychlém startu pomocí webu Azure Portal vytvoříte registr kontejneru, nasdílíte do registru image kontejneru a nakonec nasadíte kontejner z registru do služby Azure Container Instances (ACI).

K dokončení tohoto rychlého startu je potřeba místně nainstalovaný Docker. Docker nabízí balíčky pro snadnou konfiguraci Dockeru na jakémkoli [Macu][docker-mac] nebo systému [Windows][docker-windows] či [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

Vyberte **Vytvořit prostředek** > **Kontejnery** > **Container Registry**.

![Vytvoření registru kontejnerů na webu Azure Portal][qs-portal-01]

Zadejte odpovídající hodnoty pro **Název registru** a **Skupina prostředků**. Název registru musí být jedinečný v rámci Azure a musí obsahovat 5 až 50 alfanumerických znaků. Pro účely tohoto rychlého startu vytvořte novou skupinu prostředků `myResourceGroup` v umístění `West US` a jako **skladovou položku** vyberte Basic. Vyberte **Vytvořit** a nasaďte instanci služby ACR.

![Vytvoření registru kontejnerů na webu Azure Portal][qs-portal-03]

V tomto rychlém startu vytvoříme registr úrovně *Basic*. Služba Azure Container Registry je dostupná v několika různých skladových položkách, které stručně popisuje následující tabulka. Další podrobnosti o každé z nich najdete v tématu [Skladové položky služby Container Registry][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Po zobrazení zprávy **Nasazení bylo úspěšné** vyberte na portálu registr kontejnerů a pak vyberte **Přístupové klíče**.

![Vytvoření registru kontejnerů na webu Azure Portal][qs-portal-05]

V části **Uživatel s rolí správce** vyberte **Povolit**. Poznamenejte si následující hodnoty:

* Přihlašovací server
* Uživatelské jméno
* heslo

Tyto hodnoty použijete v následujících krocích při práci s registrem pomocí rozhraní příkazového řádku Dockeru.

![Vytvoření registru kontejnerů na webu Azure Portal][qs-portal-06]

## <a name="log-in-to-acr"></a>Přihlášení ke službě ACR

Před odesíláním a vyžadováním imagí kontejnerů se musíte přihlásit k instanci služby ACR. K tomu použijte příkaz [docker login][docker-login]. Nahraďte hodnoty *username* (uživatelské jméno), *password* (heslo) a *login server* (přihlašovací server) hodnotami, které jste si poznamenali v předchozím kroku.

```bash
docker login --username <username> --password <password> <login server>
```

Příkaz po dokončení vrátí zprávu `Login Succeeded` (Přihlášení bylo úspěšné). Může se zobrazit také upozornění zabezpečení doporučující použití parametru `--password-stdin`. I když je jeho použití nad rámec tohoto článku, doporučujeme řídit se osvědčeným postupem. Další informace najdete v referenčních informacích k příkazu [docker login][docker-login].

## <a name="push-image-to-acr"></a>Nasdílení image do služby ACR

Pokud chcete nasdílet image do služby Azure Container Registry, musíte nejprve mít nějakou image. V případě potřeby si přetáhněte existující image z Docker Hubu spuštěním následujícího příkazu.

```bash
docker pull microsoft/aci-helloworld
```

Před nasdílením image do registru k ní musíte přidat značku s názvem přihlašovacího serveru služby ACR. Označte image pomocí příkazu [docker tag][docker-tag]. Nahraďte *login server* názvem přihlašovacího serveru, který jste si poznamenali dříve. Přidejte *název úložiště*, například **`myrepo`**, aby se vaše image umístila do úložiště.

```bash
docker tag microsoft/aci-helloworld <login server>/<repository name>/aci-helloworld:v1
```

Nakonec pomocí příkazu [docker push][docker-push] nasdílejte image do instance služby ACR. Nahraďte *login server* názvem přihlašovacího serveru vaší instance ACR a *repository name* nahraďte názvem úložiště, který jste použili v předchozím příkazu.

```bash
docker push <login server>/<repository name>/aci-helloworld:v1
```

Výstup z úspěšného příkazu `docker push` vypadá nějak takto:

```
The push refers to repository [specificregistryname.azurecr.io/myrepo/aci-helloworld]
31ba1ebd9cf5: Pushed
cd07853fe8be: Pushed
73f25249687f: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:565dba8ce20ca1a311c2d9485089d7ddc935dd50140510050345a1b0ea4ffa6e size: 1576
```

## <a name="list-container-images"></a>Výpis imagí kontejnerů

Pokud chcete zobrazit výpis všech imagí ve vaší instanci služby ACR, přejděte na portálu do vašeho registru, vyberte **Úložiště** a pak vyberte úložiště, které jste vytvořili pomocí příkazu `docker push`.

V tomto příkladu vybereme úložiště **aci-helloworld** a v části **ZNAČKY** se zobrazí image se značkou `v1`.

![Vytvoření registru kontejnerů na webu Azure Portal][qs-portal-09]

## <a name="deploy-image-to-aci"></a>Nasazení image do služby ACI

Abychom mohli do instance nasadit image z registru, musíme přejít do úložiště (aci-helloworld) a pak kliknout na tři tečky vedle v1.

![Spuštění instance kontejneru Azure z portálu][qs-portal-10]

Zobrazí se místní nabídka, ve které vyberte **Spustit instanci**:

![Spuštění ACI – Místní nabídka][qs-portal-11]

Vyplňte **Název kontejneru**, ujistěte se, že je vybrané správné předplatné, a vyberte existující **skupinu prostředků** myResourceGroup. Ujistěte se, že je povolená možnost Veřejná IP adresa (je nastavená na **Ano**), a pak kliknutím na **OK** spusťte instanci kontejneru Azure.

![Spuštění ACI – Možnosti nasazení][qs-portal-12]

Po zahájení nasazení se na váš řídicí panel portálu umístí dlaždice oznamující průběh nasazování. Po dokončení nasazení se dlaždice aktualizuje a zobrazí novou skupinu kontejnerů **mycontainer**.

![Stav nasazení ACI][qs-portal-13]

Vyberte skupinu kontejnerů mycontainer a zobrazte její vlastnosti. Poznamenejte si **IP adresu** skupiny kontejnerů a také **STAV** kontejneru.

![Podrobnosti o kontejneru ACI][qs-portal-14]

## <a name="view-the-application"></a>Zobrazení aplikace

Jakmile bude kontejner ve stavu **Spuštěno**, v oblíbeném prohlížeči přejděte na IP adresu, kterou jste si poznamenali v předchozím kroku, a zobrazte aplikaci.

![Aplikace Hello World v prohlížeči][qs-portal-15]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky, přejděte na portálu do skupiny prostředků **myResourceGroup**. Po načtení skupiny prostředků kliknutím na **Odstranit skupinu prostředků** odeberte skupinu prostředků, službu Azure Container Registry a všechny instance kontejnerů Azure.

![Odstranění skupiny prostředků na webu Azure Portal][qs-portal-08]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste pomocí Azure CLI vytvořili službu Azure Container Registry a prostřednictvím služby Azure Container Instances jste spustili její instanci. Pokračujte ke kurzu služby Azure Container Instances, kde najdete podrobnější přehled ACI.

> [!div class="nextstepaction"]
> [Kurzy služby Azure Container Instances][container-instances-tutorial-prepare-app]

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-04]: ./media/container-registry-get-started-portal/qs-portal-04.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-06]: ./media/container-registry-get-started-portal/qs-portal-06.png
[qs-portal-07]: ./media/container-registry-get-started-portal/qs-portal-07.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png
[qs-portal-10]: ./media/container-registry-get-started-portal/qs-portal-10.png
[qs-portal-11]: ./media/container-registry-get-started-portal/qs-portal-11.png
[qs-portal-12]: ./media/container-registry-get-started-portal/qs-portal-12.png
[qs-portal-13]: ./media/container-registry-get-started-portal/qs-portal-13.png
[qs-portal-14]: ./media/container-registry-get-started-portal/qs-portal-14.png
[qs-portal-15]: ./media/container-registry-get-started-portal/qs-portal-15.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md

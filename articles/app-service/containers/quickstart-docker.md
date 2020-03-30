---
title: 'Úvodní příručka: Spuštění vlastního kontejneru Linuxu'
description: S linuxovými kontejnery ve službě Azure App Service můžete začít nasazením prvního vlastního kontejneru pomocí registrů kontejnerů Azure.
author: msangapu-msft
ms.author: msangapu
ms.date: 08/28/2019
ms.topic: quickstart
ms.openlocfilehash: 62e6b007e89fc6be726d3d971ca838770db9cb6e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75422154"
---
# <a name="deploy-a-custom-linux-container-to-azure-app-service"></a>Nasazení vlastního kontejneru Linuxu do služby Azure App Service

App Service na Linuxu poskytuje předdefinované aplikační zásobníky na Linuxu s podporou jazyků, jako je .NET, PHP, Node.js a další. Můžete také použít vlastní image Dockeru a spouštět webovou aplikaci v zásobníku aplikací, který ještě není v Azure definovaný. Tento rychlý start ukazuje, jak nasadit image z [registru kontejnerů Azure](/azure/container-registry) (ACR) do služby App Service.

## <a name="prerequisites"></a>Požadavky

* [Účet Azure](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
* [Docker](https://www.docker.com/community-edition)
* [Kód visual studia](https://code.visualstudio.com/)
* Rozšíření [služby Azure App Service pro kód VS](vscode:extension/ms-azuretools.vscode-azureappservice). Toto rozšíření můžete použít k vytváření, správě a nasazení Linux Web Apps na platformě Azure jako služba (PaaS).
* [Rozšíření Dockeru pro kód VS](vscode:extension/ms-azuretools.vscode-docker). Toto rozšíření můžete použít ke zjednodušení správy místních ibi a příkazů Dockeru a k nasazení vytvořených ibi aplikací do Azure.

## <a name="create-an-image"></a>Vytvoření obrázku

K dokončení tohoto rychlého startu budete potřebovat vhodnou bitovou kopii webové aplikace uloženou v [registru kontejnerů Azure](/azure/container-registry). Postupujte podle pokynů v [úvodním panelu: Vytvořte registr soukromého kontejneru pomocí portálu Azure](/azure/container-registry/container-registry-get-started-portal), ale místo bitové `mcr.microsoft.com/azuredocs/go` `hello-world` kopie použijte bitovou kopii. Pro referenci [ukázkový soubor Dockerfile se nachází v repo ukázky Azure](https://github.com/Azure-Samples/go-docs-hello-world).

> [!IMPORTANT]
> Nezapomeňte nastavit možnost **Správce uživatele** **povolit** při vytváření registru kontejneru. Můžete ji také nastavit z části **Přístupové klíče** na stránce registru na webu Azure Portal. Toto nastavení je vyžadováno pro přístup služby App Service.

## <a name="sign-in"></a>Přihlášení

Dále spusťte VS Code a přihlaste se ke svému účtu Azure pomocí rozšíření App Service. Chcete-li to provést, vyberte logo Azure na panelu aktivit, přejděte na průzkumník **APP SERVICE,** pak **vyberte Přihlásit se do Azure** a postupujte podle pokynů.

![přihlášení do Azure](./media/quickstart-docker/sign-in.png)

## <a name="check-prerequisites"></a>Kontrola požadavků

Nyní můžete zkontrolovat, zda máte všechny požadavky správně nainstalovány a nakonfigurovány.

V Kódu VS byste měli vidět svou e-mailovou adresu Azure na stavovém řádku a předplatné v průzkumníku **APP SERVICE.**

Dále ověřte, zda máte docker nainstalovaný a spuštěný. Následující příkaz zobrazí verzi Dockeru, pokud je spuštěna.

```bash
docker --version
```

Nakonec se ujistěte, že je váš registr kontejnerů Azure připojený. Chcete-li to provést, vyberte logo Dockeru na panelu aktivit a přejděte do **části REGISTRY .**

![Registry](./media/quickstart-docker/registries.png)

## <a name="deploy-the-image-to-azure-app-service"></a>Nasazení image do služby Azure App Service

Teď, když je všechno nakonfigurované, můžete nasadit svou image do [služby Azure App Service](https://azure.microsoft.com/services/app-service/) přímo z průzkumníka rozšíření Dockeru.

Najděte obrázek pod uzlu **Registrů** v průzkumníku **DOCKERu** a rozbalte ho tak, aby zobrazoval jeho značky. Klikněte pravým tlačítkem myši na značku a pak vyberte **Deploy Image to Azure App Service**.

Zde postupujte podle pokynů k výběru předplatného, globálně jedinečného názvu aplikace, skupiny prostředků a plánu služby App Service. Zvolte **B1 Basic** pro cenovou úroveň a oblast.

Po nasazení je vaše `http://<app name>.azurewebsites.net`aplikace dostupná na adrese .

**Skupina prostředků** je pojmenovaná kolekce všech prostředků vaší aplikace v Azure. Skupina prostředků může například obsahovat odkaz na web, databázi a funkci Azure.

**Plán služby App Service** definuje fyzické prostředky, které budou použity k hostování vašeho webu. Tento rychlý start používá **základní** hostingový plán na infrastruktuře **Linuxu,** což znamená, že web bude hostován na počítači s Linuxem vedle jiných webových stránek. Pokud začnete s **plánem Basic,** můžete pomocí portálu Azure vertikálně navýšit kapacitu tak, aby vaše je jediná lokalita spuštěná na počítači.

## <a name="browse-the-website"></a>Procházení webu

Výstupní **Output** panel se otevře během nasazení k označení stavu operace. Po dokončení operace najděte aplikaci, kterou jste vytvořili v **průzkumníku služby APP SERVICE,** klikněte na ni pravým tlačítkem myši a pak vyberte **Procházet web** a otevřete web v prohlížeči.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/PWZWZ52?tutorial=quickstart-docker&step=deploy-app)

## <a name="next-steps"></a>Další kroky

Gratulujeme, úspěšně jste dokončili tento rychlý start!

Dále se podívejte na další rozšíření Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Nástroje azure cli](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Nástroje Správce prostředků Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Nebo je všechny získejte instalací rozšíření [Nástroje Azure.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)

---
title: 'Rychlý Start: spuštění vlastního kontejneru Linux'
description: Začněte s kontejnery Linux na Azure App Service nasazením prvního vlastního kontejneru pomocí služby Azure Container Registry.
author: msangapu-msft
ms.author: msangapu
ms.date: 08/28/2019
ms.topic: quickstart
ms.openlocfilehash: 14ea5444d3d8827919405a4d5aa8a176746c2e72
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688950"
---
# <a name="deploy-a-custom-linux-container-to-azure-app-service"></a>Nasazení vlastního kontejneru Linux na Azure App Service

App Service v systému Linux poskytuje předdefinované zásobníky aplikací v systému Linux s podporou pro jazyky, jako je například .NET, PHP, Node. js a další. Můžete také použít vlastní image Dockeru a spouštět webovou aplikaci v zásobníku aplikací, který ještě není v Azure definovaný. V tomto rychlém startu se dozvíte, jak nasadit image z [Azure Container Registry](/azure/container-registry) (ACR) do App Service.

## <a name="prerequisites"></a>Předpoklady

* [Účet Azure](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
* [Docker](https://www.docker.com/community-edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure App Service rozšíření pro vs Code](vscode:extension/ms-azuretools.vscode-azureappservice). Toto rozšíření můžete použít k vytváření, správě a nasazování Web Apps pro Linux na platformě Azure jako službu (PaaS).
* [Rozšíření Docker pro vs Code](vscode:extension/ms-azuretools.vscode-docker). Pomocí tohoto rozšíření můžete zjednodušit správu místních imagí a příkazů Docker a nasazovat do Azure sestavené image aplikací.

## <a name="create-an-image"></a>Vytvoření obrázku

K dokončení tohoto rychlého startu budete potřebovat vhodnou image webové aplikace uloženou v [Azure Container Registry](/azure/container-registry). Postupujte podle pokynů v tématu [rychlý Start: Vytvoření privátního registru kontejnerů pomocí Azure Portal](/azure/container-registry/container-registry-get-started-portal), ale místo obrázku `hello-world` použijte obrázek `mcr.microsoft.com/azuredocs/go`.

> [!IMPORTANT]
> Nezapomeňte nastavit možnost uživatel s **oprávněními správce** , aby se **povolil** při vytváření registru kontejneru. Můžete ji také nastavit v části **přístupové klíče** stránky registru v Azure Portal. Toto nastavení se vyžaduje pro App Service přístup.

## <a name="sign-in"></a>Přihlásit se

Dále spusťte VS Code a přihlaste se k účtu Azure pomocí rozšíření App Service. Pokud to chcete provést, vyberte na řádku aktivity logo Azure, přejděte do Průzkumníka **služby App Service** , vyberte možnost **Přihlásit se k Azure** a postupujte podle pokynů.

![Přihlaste se k Azure](./media/quickstart-docker/sign-in.png)

## <a name="check-prerequisites"></a>Ověřit požadavky

Nyní můžete zkontrolovat, zda máte správně nainstalované a nakonfigurované požadavky.

V VS Code byste měli na stavovém řádku a v rámci vašeho předplatného v Průzkumníkovi **služby App Service** vidět vaši e-mailovou adresu Azure.

Pak ověřte, že máte nainstalovaný a spuštěný Docker. V následujícím příkazu se zobrazí verze Docker, pokud je spuštěná.

```bash
docker --version
```

Nakonec zajistěte, aby byl váš Azure Container Registry připojený. Pokud to chcete provést, vyberte na panelu aktivity logo Docker a pak přejděte do části **Registry**.

![Registry](./media/quickstart-docker/registries.png)

## <a name="deploy-the-image-to-azure-app-service"></a>Nasaďte bitovou kopii na Azure App Service

Teď, když je všechno nakonfigurované, můžete image nasadit do [Azure App Service](https://azure.microsoft.com/services/app-service/) přímo z Průzkumníka rozšíření Docker.

Najděte obrázek pod uzlem **Registry** v Průzkumníkovi **Docker** a rozbalte ho, aby se zobrazily jeho značky. Klikněte pravým tlačítkem na značku a pak vyberte **nasadit obrázek do Azure App Service**.

Tady můžete podle pokynů zvolit předplatné, globálně jedinečný název aplikace, skupinu prostředků a plán App Service. Pro cenovou úroveň vyberte **B1 Basic** a oblast.

Po nasazení je vaše aplikace k dispozici na adrese `http://<app name>.azurewebsites.net`.

**Skupina prostředků** je pojmenovaná kolekce všech prostředků vaší aplikace v Azure. Například skupina prostředků může obsahovat odkaz na web, databázi a funkci Azure Functions.

**Plán App Service** definuje fyzické prostředky, které se budou používat k hostování vašeho webu. V tomto rychlém startu se používá **základní** plán hostování v infrastruktuře **Linux** , což znamená, že lokalita bude hostována v počítači se systémem Linux společně s ostatními weby. Pokud začnete s plánem **Basic** , můžete k horizontálnímu navýšení kapacity použít Azure Portal, aby se na počítači používala jediná lokalita.

## <a name="browse-the-website"></a>Procházet web

Panel **výstup** se otevře během nasazování, aby označoval stav operace. Až se operace dokončí, Najděte aplikaci, kterou jste vytvořili v Průzkumníkovi **služby App Service** , klikněte na ni pravým tlačítkem myši a vyberte **Procházet web** a otevřete web v prohlížeči.

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/PWZWZ52?tutorial=quickstart-docker&step=deploy-app)

## <a name="next-steps"></a>Další kroky

Blahopřejeme, úspěšně jste dokončili tento rychlý Start.

Pak se podívejte na další rozšíření Azure.

* [Databáze Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Nástroje rozhraní příkazového řádku Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Nástroje Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Nebo si je všechny nainstalujte pomocí balíčku rozšíření [nástroje Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) .

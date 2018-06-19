---
title: Vytvoření vývojového prostoru Kubernetes v cloudu | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: ghogen
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: ghogen
ms.date: 06/06/2018
ms.topic: quickstart
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
manager: douge
ms.openlocfilehash: 16ec493708f85e9b3819943e131b9f9c3649f27e
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824634"
---
# <a name="quickstart-create-a-kubernetes-dev-space-with-azure-dev-spaces-net-core-and-visual-studio"></a>Rychlý start: Vytvoření vývojového prostoru Kubernetes pomocí služby Azure Dev Spaces (.NET Core a Visual Studio)

V tomto průvodci se naučíte:

- Nastavit Azure Dev Spaces se spravovaným clusterem Kubernetes v Azure
- Iterativně vyvíjet kód v kontejnerech pomocí sady Visual Studio
- Ladit kód běžící v clusteru

> [!Note]
> **Pokud se někde zaseknete**, podívejte se na článek o [odstraňování potíží](troubleshooting.md) nebo na tuto stránku přidejte komentář. Můžete také zkusit postupovat podle podrobnějšího [kurzu](get-started-netcore-visualstudio.md).

## <a name="prerequisites"></a>Požadavky

- Cluster Kubernetes na platformě Kubernetes 1.9.6 v oblasti Východní USA, Západní Evropa nebo Východní Kanada s povolenou možností Směrování aplikace HTTP.

  ![Nezapomeňte povolit možnost Směrování aplikace HTTP.](media/common/Kubernetes-Create-Cluster-3.PNG)

- Sada Visual Studio 2017 se sadou funkcí Výboj pro web. Pokud ji nemáte nainstalovanou, stáhněte si ji [odtud](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

## <a name="set-up-azure-dev-spaces"></a>Nastavení služby Azure Dev Spaces

Nainstalujte [rozšíření sady Visual Studio pro Azure Dev Spaces](https://aka.ms/get-azds-visualstudio).

## <a name="connect-to-a-cluster"></a>Připojení ke clusteru

V dalším kroku vytvoříte a nakonfigurujete projekt pro Azure Dev Spaces.

### <a name="create-an-aspnet-web-app"></a>Vytvoření webové aplikace ASP.NET

V sadě Visual Studio 2017 vytvořte nový projekt. V současné době se musí jednat o projekt **Webová aplikace ASP.NET Core**. Dejte projektu název **webfrontend**.

Vyberte šablonu **Webová aplikace (model-zobrazení-kontroler)** a nastavte cílení na **.NET Core** a **ASP.NET Core 2.0**.

### <a name="create-a-dev-space-in-azure"></a>Vytvoření vývojového prostoru v Azure

V projektu, který jste právě otevřeli, vyberte v rozevíracím okně nastavení spouštění **Azure Dev Spaces**, jak vidíte na následujícím obrázku.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

V zobrazeném dialogovém okně zkontrolujte, jestli jste přihlášení pomocí správného účtu, a potom vyberte existující cluster.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

V rozevíracím seznamu **Space** (Prostor) zatím nechte nastavenou položku `default`. Zaškrtněte políčko **Publicly Accessible** (Veřejně přístupný), aby byla webová aplikace přístupná prostřednictvím veřejného koncového bodu.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

Klikněte na **OK** a vyberte nebo vytvořte cluster.

Pokud zvolíte cluster, který není nakonfigurovaný pro práci s Azure Dev Spaces, zobrazí se zpráva s dotazem, jestli ho chcete nakonfigurovat.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Vyberte **OK**. 

### <a name="look-at-the-files-added-to-project"></a>Prohlédnutí souborů přidaných do projektu
Během čekání na vytvoření vývojového prostoru se podívejte na soubory, které se přidaly do vašeho projektu, když jste si zvolili použití služby Azure Dev Spaces.

- Přidala se složka s názvem `charts` a v ní se vygeneroval [Helm chart](https://docs.helm.sh) pro vaši aplikaci. Tyto soubory slouží k nasazení vaší aplikace do vývojového prostoru.
- Soubor `Dockerfile` obsahuje informace potřebné k zabalení vaší aplikace do standardního formátu Dockeru.
- Soubor `azds.yaml` obsahuje informace o konfiguraci potřebné pro vývojový prostor, třeba to, jestli má být aplikace přístupná prostřednictvím veřejného koncového bodu.

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Ladění kontejneru v Kubernetes
Po úspěšném vytvoření vývojového prostoru můžete začít ladit aplikaci. Nastavte v kódu zarážku, třeba na řádku 20 v souboru `HomeController.cs`, kde je nastavená proměnná `Message`. Ladění spustíte kliknutím na **F5**. 

Visual Studio bude komunikovat s vývojovým prostorem, aby se mohla aplikace sestavit a nasadit, a potom otevře prohlížeč se spuštěnou webovou aplikací. I když to může vypadat, že kontejner je spuštěný lokálně, ve skutečnosti je spuštěný ve vývojovém prostoru v Azure. Adresa místního hostitele se zobrazuje proto, že služba Azure Dev Spaces vytvoří dočasný tunel SSH do kontejneru běžícího v Azure.

Zarážku aktivujete kliknutím na odkaz **O aplikaci** v horní části stránky. Máte plný přístup k informacím o ladění, jako jsou zásobník volání, místní proměnné, informace o výjimkách apod., úplně stejně jako při místním spuštění kódu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Práce s více kontejnery a týmový vývoj](get-started-netcore-visualstudio.md#call-another-container)
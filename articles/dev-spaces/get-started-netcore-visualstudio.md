---
title: 'Create a Kubernetes dev space: Visual Studio & .NET Core'
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 07/09/2018
ms.topic: tutorial
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
ms.openlocfilehash: bd59046af58f63c974d5c8d6a97a372f63b848ae
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327324"
---
# <a name="create-a-kubernetes-dev-space-visual-studio-and-net-core-with-azure-dev-spaces"></a>Create a Kubernetes dev space: Visual Studio and .NET Core with Azure Dev Spaces

V tomto průvodci se naučíte:

- Nastavit Azure Dev Spaces se spravovaným clusterem Kubernetes v Azure
- Iterativně vyvíjet kód v kontejnerech pomocí sady Visual Studio
- Vyvíjet nezávisle dvě samostatné služby a pomocí zjišťování služby DNS v Kubernetes volat jinou službu
- Produktivně vyvíjet a testovat kód v týmovém prostředí

> [!Note]
> **If you get stuck** at any time, see the [Troubleshooting](troubleshooting.md) section.


## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Vytvoření clusteru Kubernetes s podporou Azure Dev Spaces

1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
1. Zvolte **Vytvořit prostředek**, vyhledejte **Kubernetes** a vyberte **Služba Kubernetes** > **Vytvořit**.

   Complete the following steps under each heading of the *Create Kubernetes cluster* form and verify your selected [region supports Azure Dev Spaces][supported-regions].

   - **PROJECT DETAILS**: select an Azure subscription and a new or existing Azure resource group.
   - **PODROBNOSTI O CLUSTERU:** Zadejte název, oblast, verzi a předponu názvu DNS pro cluster AKS.
   - **ŠKÁLOVÁNÍ:** Vyberte velikost virtuálního počítače pro uzly agentů AKS a počet uzlů. Pokud s Azure Dev Spaces začínáte, na prozkoumání všech funkcí vám stačí jeden uzel. Počet uzlů můžete po nasazení clusteru kdykoli snadno upravit. Mějte na paměti, že velikost virtuálního počítače není možné po vytvoření clusteru AKS změnit. Pokud však potřebujete vertikálně navýšit kapacitu, po nasazení clusteru AKS můžete snadno vytvořit nový cluster AKS s většími virtuálními počítači a pomocí Dev Spaces provést opětovné nasazení do tohoto většího clusteru.

   ![Nastavení konfigurace Kubernetes](media/common/Kubernetes-Create-Cluster-2.PNG)


   Po dokončení vyberte **Další: Ověřování**.

1. Vyberte požadované nastavení pro řízení přístupu na základě role (RBAC). Služba Azure Dev Spaces podporuje clustery s vypnutým i zapnutým řízením přístupu na základě role.

    ![Nastavení řízení přístupu na základě role](media/common/k8s-RBAC.PNG)

1. Vyberte **Zkontrolovat a vytvořit** a po dokončení vyberte **Vytvořit**.

## <a name="get-the-visual-studio-tools"></a>Získání nástrojů sady Visual Studio
Install the latest version of [Visual Studio](https://www.visualstudio.com/vs/). For Visual Studio 2019 on Windows you need to install the Azure Development workload. For Visual Studio 2017 on Windows you need to install the ASP.NET and web development workload as well as [Visual Studio Tools for Kubernetes](https://aka.ms/get-azds-visualstudio).

## <a name="create-a-web-app-running-in-a-container"></a>Vytvoření webové aplikace spuštěné v kontejneru

In this section, you'll create an ASP.NET Core web app and get it running in a container in Kubernetes.

### <a name="create-an-aspnet-web-app"></a>Vytvoření webové aplikace ASP.NET

From within Visual Studio, create a new project. V současné době se musí jednat o projekt **Webová aplikace ASP.NET Core**. Dejte projektu název **webfrontend**.

![](media/get-started-netcore-visualstudio/NewProjectDialog1.png)

Vyberte šablonu **Webová aplikace (model-zobrazení-kontroler)** a ujistěte se, že jsou ve dvou rozevíracích seznamech v horní části dialogového okna vybrané položky **.NET Core** a **ASP.NET Core 2.0**. Klikněte na **OK**, tím vytvoříte projekt.

![](media/get-started-netcore-visualstudio/NewProjectDialog2.png)

### <a name="enable-dev-spaces-for-an-aks-cluster"></a>Povolení služby Dev Spaces pro cluster AKS

V projektu, který jste právě vytvořili, vyberte v rozevíracím okně nastavení spouštění **Azure Dev Spaces**, jak je znázorněno na následujícím obrázku.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

V dalším zobrazeném dialogovém okně zkontrolujte, jestli jste přihlášení pomocí správného účtu, a potom vyberte existující cluster Kubernetes.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.PNG)

V rozevíracím seznamu **Space** (Prostor) zatím nechte vybranou položku `default`. O této možnosti se později dozvíte víc. Zaškrtněte políčko **Publicly Accessible** (Veřejně přístupný), aby byla webová aplikace přístupná prostřednictvím veřejného koncového bodu. Toto nastavení není povinné, ale v rámci tohoto názorného postupu nám pomůže předvést některé koncepty. Nemusíte si ale dělat starosti, v každém případě totiž budete moct ladit svůj web pomocí sady Visual Studio.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

Klikněte na **OK** a vyberte nebo vytvořte cluster.

Pokud zvolíte cluster, který není povolený pro práci s Azure Dev Spaces, zobrazí se zpráva s dotazem, jestli ho chcete nakonfigurovat.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Zvolte **OK**.

> [!IMPORTANT]
> The Azure Dev Spaces configuration process will remove the `azds` namespace in the cluster, if it exists.

 K provedení této akce se spustí úloha na pozadí. Dokončí se za několik minut. Pokud chcete zjistit, jestli se úloha pořád vytváří, podržte ukazatel myši nad ikonou **úloh na pozadí** v levém dolním rohu stavového řádku, jak znázorňuje následující obrázek.

![](media/get-started-netcore-visualstudio/BackgroundTasks.PNG)

> [!Note]
> Dokud se vývojový prostor úspěšně nevytvoří, nemůžete ladit aplikaci.

### <a name="look-at-the-files-added-to-project"></a>Prohlédnutí souborů přidaných do projektu
Během čekání na vytvoření vývojového prostoru se podívejte na soubory, které se přidaly do vašeho projektu, když jste zvolili použití vývojového prostoru.

Nejdřív uvidíte, že se přidala složka s názvem `charts` a v ní se vygeneroval [graf Helm](https://docs.helm.sh) pro vaši aplikaci. Tyto soubory slouží k nasazení vaší aplikace do vývojového prostoru.

Uvidíte, že se přidal soubor s názvem `Dockerfile`. Tento soubor obsahuje informace potřebné k zabalení vaší aplikace ve standardním formátu Dockeru.

Nakonec uvidíte soubor `azds.yaml`, který obsahuje konfiguraci v době vývoje, kterou vývojový prostor potřebuje.

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Ladění kontejneru v Kubernetes
Po úspěšném vytvoření vývojového prostoru můžete začít ladit aplikaci. Nastavte v kódu zarážku, třeba na řádku 20 v souboru `HomeController.cs`, kde je nastavená proměnná `Message`. Ladění spustíte kliknutím na **F5**. 

Visual Studio bude komunikovat s vývojovým prostorem, aby se mohla aplikace sestavit a nasadit, a potom otevře prohlížeč se spuštěnou webovou aplikací. I když to může vypadat, že kontejner je spuštěný lokálně, ve skutečnosti je spuštěný ve vývojovém prostoru v Azure. Adresa místního hostitele se zobrazuje proto, že služba Azure Dev Spaces vytvoří dočasný tunel SSH do kontejneru spuštěného v AKS.

Zarážku aktivujete kliknutím na odkaz **O aplikaci** v horní části stránky. Máte plný přístup k informacím o ladění, jako jsou zásobník volání, místní proměnné, informace o výjimkách apod., úplně stejně jako při místním spuštění kódu.

## <a name="iteratively-develop-code"></a>Iterativní vývoj kódu

Azure Dev Spaces neslouží jenom ke spuštění kódu v prostředí Kubernetes. Umožňuje také rychle opakovaně prohlížet změny kódu, ke kterým dochází v prostředí Kubernetes v cloudu.

### <a name="update-a-content-file"></a>Aktualizace souboru obsahu


1. Najděte soubor `./Views/Home/Index.cshtml` a upravte kód HTML. For example, change [line 73 that reads `<h2>Application uses</h2>`](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Views/Home/Index.cshtml#L73) to something like: 
  
    ```html
    <h2>Hello k8s in Azure!</h2>`
    ```

2. Uložte soubor.
3. Přejděte do prohlížeče a aktualizujte stránku. Na webové stránce by se měl zobrazit aktualizovaný kód HTML.

Co se stalo? Úpravy souborů obsahu jako HTML a CSS nevyžadují rekompilaci ve webové aplikaci .NET Core. Aktivní relace F5 totiž automaticky synchronizuje všechny upravené soubory obsahu do kontejneru spuštěného v AKS, abyste změny obsahu viděli okamžitě.

### <a name="update-a-code-file"></a>Aktualizace souboru s kódem
Aktualizace souborů s kódem je o něco pracnější, protože aplikace .NET Core musí znovu sestavit a vytvořit aktualizované binární soubory aplikace.

1. Zastavte ladicí program v sadě Visual Studio.
1. Otevřete soubor s kódem, který se jmenuje `Controllers/HomeController.cs`, a upravte zprávu, která se zobrazí na stránce O aplikaci: `ViewData["Message"] = "Your application description page.";`
1. Uložte soubor.
1. Stisknutím klávesy **F5** znovu spusťte ladění. 

Místo opětovného sestavení a nasazení nové image kontejneru po každé provedené změně, což často dlouho trvá, rekompiluje služba Azure Dev Spaces kód po přírůstcích ve stávajícím kontejneru, aby se zrychlil cyklus úprav/ladění.

Aktualizujte webovou aplikaci v prohlížeči a přejděte na stránku O aplikaci. V uživatelském rozhraní by se měla zobrazit vaše upravená zpráva.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Learn about multi-service development](multi-service-netcore-visualstudio.md)


[supported-regions]: about.md#supported-regions-and-configurations
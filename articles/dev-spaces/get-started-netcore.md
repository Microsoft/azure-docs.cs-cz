---
title: 'Vytvoření Kubernetesho vývojového prostoru: Visual Studio Code & .NET Core'
services: azure-dev-spaces
ms.date: 09/26/2018
ms.topic: tutorial
ms.custom: devx-track-azurecli
description: V tomto kurzu se dozvíte, jak používat Azure Dev Spaces a Visual Studio Code k ladění a rychlé iteraci aplikace .NET Core ve službě Azure Kubernetes.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s
ms.openlocfilehash: f4202ef63f77571e279114525fa6f14178821e9c
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2020
ms.locfileid: "87499479"
---
# <a name="create-a-kubernetes-dev-space-visual-studio-code-and-net-core-with-azure-dev-spaces"></a>Vytvoření Kubernetesho vývojového prostoru: Visual Studio Code a .NET Core s Azure Dev Spaces

V tomto průvodci se naučíte:

- Vytvořit v Azure prostředí založené na Kubernetes, které je optimalizované pro vývoj – _vývojářský prostor_
- Iterativně vyvíjet kód v kontejnerech pomocí editoru VS Code a příkazového řádku
- Produktivně vyvíjet a testovat kód v týmovém prostředí

> [!NOTE]
> **Pokud se vám pozastavila** kdykoli, přečtěte si část [Poradce při potížích](troubleshooting.md) .

## <a name="install-the-azure-cli"></a>Instalace Azure CLI
Azure Dev Spaces vyžaduje minimální nastavení místního počítače. Většina konfigurace vývojového prostoru se ukládá do cloudu, aby ji šlo sdílet s ostatními uživateli. Nejdřív si stáhněte a spusťte [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="sign-in-to-azure-cli"></a>Přihlášení k Azure CLI
Přihlaste se k Azure. V okně terminálu zadejte následující příkaz:

```azurecli
az login
```

> [!NOTE]
> Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).

#### <a name="if-you-have-multiple-azure-subscriptions"></a>Pokud máte více předplatných Azure...
Svoje předplatná můžete zobrazit spuštěním tohoto příkazu: 

```azurecli
az account list --output table
```

Vyhledejte předplatné, které má *hodnotu true* pro *výchozí nastavení*.
Pokud se nejedná o předplatné, které chcete použít, můžete výchozí předplatné změnit:

```azurecli
az account set --subscription <subscription ID>
```

## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Vytvoření clusteru Kubernetes s podporou Azure Dev Spaces

V příkazovém řádku vytvořte skupinu prostředků v [oblasti, která podporuje Azure dev Spaces][supported-regions].

```azurecli
az group create --name MyResourceGroup --location <region>
```

Pomocí následujícího příkazu vytvořte cluster Kubernetes:

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

Vytvoření clusteru bude trvat několik minut.

### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Konfigurace clusteru AKS, aby používal Azure Dev Spaces

Zadejte následující příkaz Azure CLI se skupinou prostředků, která obsahuje váš cluster AKS, a názvem clusteru AKS. Příkaz nakonfiguruje cluster, aby podporoval Azure Dev Spaces.

   ```azurecli
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```
   
> [!IMPORTANT]
> Proces konfigurace Azure Dev Spaces odstraní `azds` obor názvů v clusteru, pokud existuje.

## <a name="get-kubernetes-debugging-for-vs-code"></a>Získání ladění Kubernetes pro VS Code
Výkonné funkce, jako je ladění Kubernetes, jsou prostřednictvím editoru VS Code k dispozici vývojářům .NET Core a Node.js.

1. Pokud [VS Code](https://code.visualstudio.com/Download) nemáte, nainstalujte si ho.
1. Stáhněte a nainstalujte rozšíření [VS Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) a [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) . Pro každé rozšíření klikněte na stránce Tržiště rozšíření na instalovat a znovu v VS Code.

## <a name="create-a-web-app-running-in-a-container"></a>Vytvoření webové aplikace spuštěné v kontejneru

V této části vytvoříte webovou aplikaci ASP.NET Core a získáte její spuštění v kontejneru v Kubernetes.

### <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core
Naklonujte nebo stáhněte [ukázkovou aplikaci Azure dev Spaces](https://github.com/Azure/dev-spaces). Tento článek používá kód v adresáři *Samples/dotnetcore/Začínáme/webfront-Endu* .

## <a name="preparing-code-for-docker-and-kubernetes-development"></a>Příprava kódu pro vývoj v Dockeru a Kubernetes
Zatím máte základní webovou aplikaci, kterou můžete spustit místně. Teď ji vytvořením prostředků, které definují kontejner aplikace a způsob nasazení do Kubernetes, kontejnerizujete. Tento úkol snadno provedete v Azure Dev Spaces: 

1. Spusťte VS Code a otevřete složku `webfrontend`. (Případné výchozí výzvy ohledně přidání prostředků ladění nebo obnovení projektu můžete ignorovat.)
1. Otevřete ve VS Code integrovaný terminál (v nabídce **Zobrazení > Integrovaný terminál**).
1. Spusťte následující příkaz (přesvědčte se, že je aktuálním adresářem **webfrontend**):

    ```cmd
    azds prep --enable-ingress
    ```

Příkaz Azure CLI `azds prep` vygeneruje prostředky Dockeru a Kubernetes s výchozím nastavením:
* `./Dockerfile` popisuje image kontejneru aplikace a způsob vytvoření a běhu zdrojového kódu v rámci kontejneru.
* [Helm chart](https://docs.helm.sh) v `./charts/webfrontend` popisuje, jak do Kubernetes nasadit kontejner.

> [!TIP]
> [Graf souboru Dockerfile a Helm](how-dev-spaces-works-prep.md#prepare-your-code) pro váš projekt je používán Azure dev Spaces k sestavení a spuštění kódu, ale tyto soubory lze změnit, pokud chcete změnit způsob sestavení a spuštění projektu.

Celému obsahu těchto souborů prozatím rozumět nemusíte. Stojí však za zmínku, že **stejné prostředky konfigurace jako kódu pro Kubernetes a Docker můžete používat v různých fázích od vývoje až po produkci, takže si napříč různými prostředími zajistíte lepší konzistentnost**.
 
Příkaz `prep` také vygeneruje soubor s názvem `./azds.yaml`, což je konfigurační soubor pro Azure Dev Spaces. Doplňuje artefakty Dockeru a Kubernetes další konfigurací, která v Azure zapne iterativní vývojové prostředí.

## <a name="build-and-run-code-in-kubernetes"></a>Sestavení a spuštění kódu v Kubernetes
Teď spustíme náš kód! V okně terminálu spusťte tento příkaz z **kořenové složky kódu** webfrontend:

```cmd
azds up
```

Sledujte výstup příkazu. Během jeho zpracování si můžete všimnout několika věcí:
- Zdrojový kód je synchronizovaný s vývojářským prostorem v Azure.
- V Azure se vytvoří image kontejneru, jak určuje prostředek Docker ve složce kódu.
- Vytvoří se objekty Kubernetes, které využívají image kontejneru podle specifikace Helm Chart ve složce kódu.
- Zobrazí se informace o koncových bodech kontejneru. V našem případě očekáváme veřejnou adresu URL protokolu HTTP.
- Za předpokladu, že se výše uvedené fáze úspěšně dokončily, měl by se začít zobrazovat výstup `stdout` (a `stderr`), jak se kontejner spouští.

> [!NOTE]
> Při prvním spuštění příkazu `up` budou tyto kroky trvat déle, ale následná spuštění by měla být rychlejší.

### <a name="test-the-web-app"></a>Test webové aplikace
Zkontrolujte výstup konzoly pro zprávu *spuštění aplikace* a potvrďte, že `up` byl příkaz dokončen:

```
Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Microsoft (R) Build Engine version 15.9.20+g88f5fadfbe for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  webfrontend -> /src/bin/Debug/netcoreapp2.2/webfrontend.dll
  webfrontend -> /src/bin/Debug/netcoreapp2.2/webfrontend.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:00.94
[...]
webfrontend-5798f9dc44-99fsd: Now listening on: http://[::]:80
webfrontend-5798f9dc44-99fsd: Application started. Press Ctrl+C to shut down.
```

Identifikujte veřejnou adresu URL pro službu ve výstupu `up` příkazu. Končí na `.azds.io` . Ve výše uvedeném příkladu je veřejná adresa URL `http://webfrontend.1234567890abcdef1234.eus.azds.io/` .

Webovou aplikaci zobrazíte otevřením veřejné adresy URL v prohlížeči. Všimněte si také, že když pracujete s webovou aplikací, Všimněte si také, `stdout` že oznámení a `stderr` výstup jsou streamované do okna *azds Trace* Terminal. V případě, že procházejí systémem, uvidíte také informace o sledování požadavků HTTP. Díky tomu je snazší sledovat komplexní volání více služeb během vývoje. Toto sledování požadavků poskytuje instrumentace, kterou přidávají vývojové prostory.

![okno terminálu trasování z d s](media/get-started-netcore/azds-trace.png)

> [!NOTE]
> Kromě veřejné adresy URL můžete použít alternativní `http://localhost:<portnumber>` adresu URL, která se zobrazí ve výstupu konzoly. Pokud použijete adresu URL místního hostitele, může se zdát, že je kontejner spuštěný v místním prostředí, ale ve skutečnosti je spuštěný v AKS. Azure Dev Spaces používá k namapování portu localhost na kontejner spuštěný v AKS funkci Kubernetes pro *přeposílání portů* . To usnadňuje interakci se službou z místního počítače.

### <a name="update-a-content-file"></a>Aktualizace souboru obsahu
Azure Dev Spaces neslouží jenom ke spuštění kódu v prostředí Kubernetes. Umožňuje také rychle opakovaně prohlížet změny kódu, ke kterým dochází v prostředí Kubernetes v cloudu.

1. Najděte soubor `./Views/Home/Index.cshtml` a upravte kód HTML. Například změňte [řádek 73, který `<h2>Application uses</h2>` ](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Views/Home/Index.cshtml#L73) bude vypadat přibližně takto: 

    ```html
    <h2>Hello k8s in Azure!</h2>
    ```

1. Soubor uložte. Za chvilku se v okně terminálu zobrazí zpráva o aktualizaci souboru ve spuštěném kontejneru.
1. Přejděte do prohlížeče a aktualizujte stránku. Na webové stránce by se měl zobrazit aktualizovaný kód HTML.

Co se stalo? Úpravy obsahových souborů jako HTML a CSS nevyžadují rekompilaci ve webové aplikaci .NET Core. Aktivní příkaz `azds up` totiž automaticky synchronizuje všechny upravené soubory obsahu do kontejneru spuštěného v Azure, abyste změny obsahu viděli okamžitě.

### <a name="update-a-code-file"></a>Aktualizace souboru s kódem
Aktualizace souborů s kódem je o něco pracnější, protože aplikace .NET Core musí znovu sestavit a vytvořit aktualizované binární soubory aplikace.

1. V okně terminálu stiskněte `Ctrl+C`, abyste zastavili `azds up`.
1. Otevřete soubor s kódem, který se jmenuje `Controllers/HomeController.cs`, a upravte zprávu, která se zobrazí na stránce O aplikaci: `ViewData["Message"] = "Your application description page.";`
1. Soubor uložte.
1. V okně terminálu spusťte `azds up`. 

Tento příkaz znovu sestaví image kontejneru a znovu nasadí Helm chart. Pokud chcete vidět, jak se změny kódu projevily v běžící aplikaci, přejděte do nabídky O aplikaci webové aplikace.

Existuje ještě *rychlejší způsob* vývoje kódu, který si ukážeme v další části. 

## <a name="debug-a-container-in-kubernetes"></a>Ladění kontejneru v Kubernetes

V této části použijete editor VS Code k přímému ladění kontejneru spuštěného v Azure. Naučíte se také, jak zrychlit cyklus úpravy-spuštění-testování.

![Diagram znázorňuje vývojovou smyčku se třemi fázemi: upravit kód, aktualizovat kontejner a zobrazit aktualizaci.](media/common/edit-refresh-see.png)

> [!NOTE]
> **Pokud se někde zaseknete**, podívejte se na článek o [odstraňování potíží](troubleshooting.md) nebo na tuto stránku přidejte komentář.

### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Inicializace prostředků ladění s využitím rozšíření VS Code
Nejdřív musíte nakonfigurovat projekt kódu tak, aby editor VS Code komunikoval s naším vývojářským prostorem v Azure. Rozšíření VS Code pro Azure Dev Spaces poskytuje pomocný příkaz pro nastavení konfigurace ladění. 

Otevřete **paletu příkazů** (pomocí nabídky **Zobrazit | Paleta příkazů**) a pomocí automatického dokončování zadejte a vyberte tento příkaz: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. 

Tím přidáte konfiguraci ladění pro Azure Dev Spaces do složky `.vscode`. Nezaměňujte tento příkaz s příkazem `azds prep`, který projekt nakonfiguruje pro nasazení.

![Tento snímek obrazovky ukazuje výběr příkazu "Azure Dev Spaces: Příprava konfiguračních souborů pro Azure Dev Spaces" z okna palety příkazů.](media/common/command-palette.png)


### <a name="select-the-azds-debug-configuration"></a>Výběr konfiguraci AZDS pro ladění
1. Pokud chcete zobrazit ladění, klikněte na boku editoru VS Code na **panelu aktivit** na ikonu Ladění.
1. Jako aktivní konfiguraci ladění vyberte **.NET Core Launch (AZDS)** (Spustit .NET Core (AZDS)).

![Snímek obrazovky je v levém horním rohu okna Visual Studio Code. Ikona ladění je zvýrazněna, levý panel má název "ladění" a rozevírací seznam napravo od názvu zobrazuje "tečka .NET Core Launch (A Z D S).](media/get-started-netcore/debug-configuration.png)

> [!NOTE]
> Pokud na paletě příkazů nevidíte příkazy Azure Dev Spaces, ověřte, že máte nainstalované rozšíření VS Code pro Azure Dev Spaces. Ujistěte se, že pracovní prostor, který jste otevřeli ve VS Code, je složka obsahující soubor azds.yaml.


### <a name="debug-the-container-in-kubernetes"></a>Ladění kontejneru v Kubernetes
Když chcete v Kubernetes ladit kód, stiskněte **F5**.

Stejně jako u příkazu `up` se kód synchronizuje s vývojovým prostorem a sestaví se kontejner, který se nasadí v Kubernetes. Ladicí program se tentokrát samozřejmě připojí ke vzdálenému kontejneru.

> [!TIP]
> Stavový řádek VS Code změní oranžová, což znamená, že ladicí program je připojen. Zobrazí se také adresa URL pro kliknutí, kterou můžete použít k otevření webu.

![Snímek obrazovky ukazuje dolní část okna Visual Studio Code. Oranžový stavový řádek je poslední řádek. Obsahuje a U R L pro otevření webu.](media/common/vscode-status-bar-url.png)

V serverovém souboru s kódem nastavte zarážku, třeba ve funkci `About()` ve zdrojovém souboru `Controllers/HomeController.cs`. Aktualizace stránky prohlížeče způsobí aktivaci zarážky.

Máte plný přístup k informacím o ladění, jako je zásobník volání, místní proměnné, informace o výjimkách apod., stejně jako při lokálním spuštění kódu.

### <a name="edit-code-and-refresh"></a>Úprava a aktualizace kódu
V aktivním ladicím programu upravte kód. Můžete třeba změnit zprávu na stránce O aplikaci v `Controllers/HomeController.cs`. 

```csharp
public IActionResult About()
{
    ViewData["Message"] = "My custom message in the About page.";
    return View();
}
```

Uložte soubor a v **podokně akce ladění**klikněte na tlačítko **restartovat** . 

![Podokno akce ladění je malé podokno v horním středu stránky (hned pod nadpisem stránky). Tlačítko restartovat, KRUHOVÁ ŠIPKA, je zvýrazněna. Obrázek tlačítka myši pro tlačítko je "restartovat (CTRL + SHIFT + F 5)".](media/common/debug-action-refresh.png)

Místo opětovného sestavení a nasazení nové image kontejneru po každé provedené změně, což často dlouho trvá, rekompiluje služba Azure Dev Spaces kód po přírůstcích ve stávajícím kontejneru, aby se zrychlil cyklus úprav/ladění.

Aktualizujte webovou aplikaci v prohlížeči a přejděte na stránku O aplikaci. V uživatelském rozhraní by se měla zobrazit vaše upravená zpráva.

**Teď máte metodu, jak rychle provádět iteraci kódu a jeho ladění v Kubernetes.** Příště si ukážeme, jak vytvořit a volat druhý kontejner.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o vývoji více služeb](multi-service-netcore.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

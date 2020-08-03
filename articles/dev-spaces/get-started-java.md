---
title: 'Vytvoření Kubernetes vývojového prostoru: Visual Studio Code & Java'
services: azure-dev-spaces
author: stepro
ms.custom: devx-track-java, devx-track-azurecli
ms.author: stephpr
ms.date: 09/26/2018
ms.topic: tutorial
description: V tomto kurzu se dozvíte, jak pomocí Azure Dev Spaces a Visual Studio Code ladit a rychle iterovat aplikaci Java ve službě Azure Kubernetes.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 927ad3b8f3535dbd905f06949b34e87d15394533
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496810"
---
# <a name="create-a-kubernetes-dev-space-visual-studio-code-and-java-with-azure-dev-spaces"></a>Vytvoření Kubernetes vývojového prostoru: Visual Studio Code a Java s Azure Dev Spaces

V tomto průvodci se naučíte:

- Vytvořit v Azure prostředí založené na Kubernetes, které je optimalizované pro vývoj – _vývojářský prostor_
- Iterativně vyvíjet kód v kontejnerech pomocí editoru VS Code a příkazového řádku
- Produktivně vyvíjet a testovat kód v týmovém prostředí

> [!Note]
> **Pokud se vám pozastavila** kdykoli, přečtěte si část [Poradce při potížích](troubleshooting.md) .

## <a name="install-the-azure-cli"></a>Instalace Azure CLI
Azure Dev Spaces vyžaduje minimální nastavení místního počítače. Většina konfigurace vývojového prostoru se ukládá do cloudu, aby ji šlo sdílet s ostatními uživateli. Nejdřív si stáhněte a spusťte [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="sign-in-to-azure-cli"></a>Přihlášení k Azure CLI
Přihlaste se k Azure. V okně terminálu zadejte následující příkaz:

```azurecli
az login
```

> [!Note]
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
1. Stáhněte a nainstalujte [rozšíření VS Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Klikněte na Nainstalovat jednou na stránce rozšíření na webu Marketplace a pak znovu ve VS Code. 

Pokud chcete v Azure Dev Spaces ladit aplikace v Javě, stáhněte si a nainstalujte rozšíření [Java Debugger pro Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) pro VS Code. Klikněte na Nainstalovat jednou na stránce rozšíření na webu Marketplace a pak znovu ve VS Code.

## <a name="create-a-web-app-running-in-a-container"></a>Vytvoření webové aplikace spuštěné v kontejneru

V této části vytvoříte webovou aplikaci v Javě a spustíte ji v kontejneru v prostředí Kubernetes.

### <a name="create-a-java-web-app"></a>Vytvoření webové aplikace Java
Stáhněte si kód z GitHubu tak, že přejdete na adresu [https://github.com/Azure/dev-spaces](https://github.com/Azure/dev-spaces) a vyberete **klonovat nebo stáhnout** a stáhnete úložiště GitHub do svého místního prostředí. Kód tohoto průvodce je tady: `samples/java/getting-started/webfrontend`.

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

> [!Note]
> Při prvním spuštění příkazu `up` budou tyto kroky trvat déle, ale následná spuštění by měla být rychlejší.

### <a name="test-the-web-app"></a>Test webové aplikace
Ve výstupu konzoly vyhledejte informace o veřejné adrese URL, kterou vytvořil příkaz `up`. Bude v tomto tvaru: 

```
(pending registration) Service 'webfrontend' port 'http' will be available at <url>
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
```

Identifikujte veřejnou adresu URL pro službu ve výstupu `up` příkazu. Končí na `.azds.io` . Ve výše uvedeném příkladu je veřejná adresa URL `http://webfrontend.1234567890abcdef1234.eus.azds.io/` .

Webovou aplikaci zobrazíte otevřením veřejné adresy URL v prohlížeči. Všimněte si také, že když pracujete s webovou aplikací, Všimněte si také, `stdout` že oznámení a `stderr` výstup jsou streamované do okna *azds Trace* Terminal. V případě, že procházejí systémem, uvidíte také informace o sledování požadavků HTTP. Díky tomu je snazší sledovat komplexní volání více služeb během vývoje. Toto sledování požadavků poskytuje instrumentace, kterou přidávají vývojové prostory.

> [!Note]
> Kromě veřejné adresy URL můžete použít alternativní `http://localhost:<portnumber>` adresu URL, která se zobrazí ve výstupu konzoly. Pokud použijete adresu URL místního hostitele, může se zdát, že je kontejner spuštěný v místním prostředí, ale ve skutečnosti je spuštěný v AKS. Azure Dev Spaces používá k namapování portu localhost na kontejner spuštěný v AKS funkci Kubernetes pro *přeposílání portů* . To usnadňuje interakci se službou z místního počítače.

### <a name="update-a-content-file"></a>Aktualizace souboru obsahu
Azure Dev Spaces neslouží jenom ke spuštění kódu v prostředí Kubernetes. Umožňuje také rychle opakovaně prohlížet změny kódu, ke kterým dochází v prostředí Kubernetes v cloudu.

1. V okně terminálu stiskněte `Ctrl+C`, abyste zastavili `azds up`.
1. Otevřete `src/main/java/com/ms/sample/webfrontend/Application.java` a upravte zprávu pozdravu na [řádku 19](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L19):

    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. Soubor uložte.
1. V okně terminálu spusťte `azds up`.

Tento příkaz znovu sestaví image kontejneru a znovu nasadí Helm chart. Pokud chcete vidět, jak se změny kódu projevily v běžící aplikaci, jednoduše aktualizujte prohlížeč.

Existuje ještě *rychlejší způsob* vývoje kódu, který si ukážeme v další části. 

## <a name="debug-a-container-in-kubernetes"></a>Ladění kontejneru v Kubernetes

V této části použijete editor VS Code k přímému ladění kontejneru spuštěného v Azure. Naučíte se také, jak zrychlit cyklus úpravy-spuštění-testování.

![Diagram znázorňující úpravu kódu, obnovení kontejneru, viz cyklus aktualizace.](media/common/edit-refresh-see.png)

> [!Note]
> **Pokud se někde zaseknete**, podívejte se na článek o [odstraňování potíží](troubleshooting.md) nebo na tuto stránku přidejte komentář.

### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Inicializace prostředků ladění s využitím rozšíření VS Code
Nejdřív musíte nakonfigurovat projekt kódu tak, aby editor VS Code komunikoval s naším vývojářským prostorem v Azure. Rozšíření VS Code pro Azure Dev Spaces poskytuje pomocný příkaz pro nastavení konfigurace ladění. 

Otevřete **paletu příkazů** (pomocí nabídky **Zobrazit | Paleta příkazů**) a pomocí automatického dokončování zadejte a vyberte tento příkaz: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. 

Tím přidáte konfiguraci ladění pro Azure Dev Spaces do složky `.vscode`. Nezaměňujte tento příkaz s příkazem `azds prep`, který projekt nakonfiguruje pro nasazení.

![Snímek obrazovky znázorňující možnost Azure Dev Spaces na paletě příkazů VS Code](media/common/command-palette.png)

### <a name="select-the-azds-debug-configuration"></a>Výběr konfiguraci AZDS pro ladění
1. Pokud chcete zobrazit ladění, klikněte na boku editoru VS Code na **panelu aktivit** na ikonu Ladění.
1. Jako aktivní konfiguraci ladění vyberte **Launch Java Program (AZDS)**.

![Snímek obrazovky funkce VS Code ladění pomocí možnosti spustit program Java, která je popsaný červeně](media/get-started-java/debug-configuration.png)

> [!Note]
> Pokud na paletě příkazů nevidíte příkazy Azure Dev Spaces, ověřte, že máte nainstalované rozšíření VS Code pro Azure Dev Spaces. Ujistěte se, že se pracovní prostor, který jste otevřeli v VS Code, nachází ve složce, která obsahuje `azds.yaml` .

### <a name="debug-the-container-in-kubernetes"></a>Ladění kontejneru v Kubernetes
Když chcete v Kubernetes ladit kód, stiskněte **F5**.

Stejně jako u příkazu `up` se kód synchronizuje s vývojovým prostorem a sestaví se kontejner, který se nasadí v Kubernetes. Ladicí program se tentokrát samozřejmě připojí ke vzdálenému kontejneru.

> [!Tip]
> Stavový řádek VS Code změní oranžová, což znamená, že ladicí program je připojen. Zobrazí se také adresa URL pro kliknutí, kterou můžete použít k otevření aplikace.

![Snímek obrazovky se stavovým řádkem VS Code poté, co byla deaktivována oranžová.](media/common/vscode-status-bar-url.png)

V serverovém souboru s kódem nastavte zarážku, třeba ve funkci `greeting()` ve zdrojovém souboru `src/main/java/com/ms/sample/webfrontend/Application.java`. Aktualizace stránky prohlížeče způsobí aktivaci zarážky.

Máte plný přístup k informacím o ladění, jako je zásobník volání, místní proměnné, informace o výjimkách apod., stejně jako při lokálním spuštění kódu.

### <a name="edit-code-and-refresh"></a>Úprava a aktualizace kódu
V aktivním ladicím programu upravte kód. Například změňte úvodní zprávu v kódu `src/main/java/com/ms/sample/webfrontend/Application.java`. 

```java
public String greeting()
{
    return "I'm debugging Java code in Azure!";
}
```

Uložte soubor a v **podokně akce ladění**klikněte na tlačítko **restartovat** .

![Snímek obrazovky s podoknem akcí VS Code ladění s možností restartování popsaný červeně](media/common/debug-action-refresh.png)

Místo opětovného sestavení a nasazení nové image kontejneru po každé provedené změně, což často dlouho trvá, rekompiluje služba Azure Dev Spaces kód po přírůstcích ve stávajícím kontejneru, aby se zrychlil cyklus úprav/ladění.

Aktualizujte webovou aplikaci v prohlížeči. V uživatelském rozhraní by se měla zobrazit vaše upravená zpráva.

**Teď máte metodu, jak rychle provádět iteraci kódu a jeho ladění v Kubernetes.** Příště si ukážeme, jak vytvořit a volat druhý kontejner.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o vývoji více služeb](multi-service-java.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

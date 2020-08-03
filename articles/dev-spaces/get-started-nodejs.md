---
title: 'Vytvoření Kubernetesho vývojového prostoru: Visual Studio Code & Node.js'
services: azure-dev-spaces
ms.date: 09/26/2018
ms.topic: tutorial
description: V tomto kurzu se dozvíte, jak pomocí Azure Dev Spaces a Visual Studio Code ladit a rychle iterovat Node.js aplikace ve službě Azure Kubernetes.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s
ms.custom: devx-track-javascript, devx-track-azurecli
ms.openlocfilehash: a351ab92b01ac1a9cfe2834f6019bbb947751d35
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2020
ms.locfileid: "87504343"
---
# <a name="create-a-kubernetes-dev-space-visual-studio-code-and-nodejs-with-azure-dev-spaces"></a>Vytvoření Kubernetesho vývojového prostoru: Visual Studio Code a Node.js pomocí Azure Dev Spaces

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
1. Stáhněte a nainstalujte [rozšíření VS Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Klikněte na Nainstalovat jednou na stránce rozšíření na webu Marketplace a pak znovu ve VS Code. 

## <a name="create-a-nodejs-container-in-kubernetes"></a>Vytvoření kontejneru Node.js v Kubernetes

V této části vytvoříte webovou aplikaci Node.js a spustíte ji v kontejneru v prostředí Kubernetes.

### <a name="create-a-nodejs-web-app"></a>Vytvoření webové aplikace Node.js
Stáhněte si kód z GitHubu tak, že přejdete na adresu [https://github.com/Azure/dev-spaces](https://github.com/Azure/dev-spaces) a vyberete **klonovat nebo stáhnout** a stáhnete úložiště GitHub do svého místního prostředí. Kód tohoto průvodce je tady: `samples/nodejs/getting-started/webfrontend`.

## <a name="prepare-code-for-docker-and-kubernetes-development"></a>Příprava kódu pro vývoj Docker a Kubernetes
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
Ve výstupu konzoly vyhledejte informace o veřejné adrese URL, kterou vytvořil příkaz `up`. Bude v tomto tvaru: 

```
(pending registration) Service 'webfrontend' port 'http' will be available at <url>
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
```

Identifikujte veřejnou adresu URL pro službu ve výstupu `up` příkazu. Končí na `.azds.io` . Ve výše uvedeném příkladu je veřejná adresa URL `http://webfrontend.1234567890abcdef1234.eus.azds.io/` .

Webovou aplikaci zobrazíte otevřením veřejné adresy URL v prohlížeči. Všimněte si také, že když pracujete s webovou aplikací, Všimněte si také, `stdout` že oznámení a `stderr` výstup jsou streamované do okna *azds Trace* Terminal. V případě, že procházejí systémem, uvidíte také informace o sledování požadavků HTTP. Díky tomu je snazší sledovat komplexní volání více služeb během vývoje. Toto sledování požadavků poskytuje instrumentace, kterou přidávají vývojové prostory.

> [!NOTE]
> Kromě veřejné adresy URL můžete použít alternativní `http://localhost:<portnumber>` adresu URL, která se zobrazí ve výstupu konzoly. Pokud použijete adresu URL místního hostitele, může se zdát, že je kontejner spuštěný místně, ale ve skutečnosti běží v Azure. Azure Dev Spaces používá k namapování portu localhost na kontejner spuštěný v AKS funkci Kubernetes pro *přeposílání portů* . To usnadňuje interakci se službou z místního počítače.

### <a name="update-a-content-file"></a>Aktualizace souboru obsahu
Azure Dev Spaces neslouží jenom ke spuštění kódu v prostředí Kubernetes. Umožňuje také rychle opakovaně prohlížet změny kódu, ke kterým dochází v prostředí Kubernetes v cloudu.

1. Najděte soubor `./public/index.html` a upravte kód HTML. Například změňte barvu pozadí stránky na odstín modré [na řádku 15](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/public/index.html#L15):

    ```html
    <body style="background-color: #95B9C7; margin-left:10px; margin-right:10px;">
    ```

1. Soubor uložte. Za chvilku se v okně terminálu zobrazí zpráva o aktualizaci souboru ve spuštěném kontejneru.
1. Přejděte do prohlížeče a aktualizujte stránku. Měli byste vidět novou barvu.

Co se stalo? Úpravy souborů obsahu, jako je HTML a CSS, nevyžadují restartování procesu Node.js. Aktivní příkaz `azds up` automaticky synchronizuje změněné soubory obsahu přímo se spuštěným kontejnerem v Azure, abyste si mohli rychle prohlédnout upravený obsah.

### <a name="test-from-a-mobile-device"></a>Testování na mobilním zařízení
Otevřete webovou aplikaci na mobilním zařízení s použitím veřejné adresy URL webového front-endu. Adresu URL můžete zkopírovat na svém stolním počítači a odeslat ji do zařízení, abyste ji nemuseli celou zadávat. Po načtení webové aplikace na mobilním zařízení si můžete všimnout, že na malém zařízení se uživatelské rozhraní nezobrazuje správně.

Pokud chcete problém opravit, přidejte metaznačku `viewport`:
1. Otevřete soubor `./public/index.html`.
1. `viewport`Do existujícího `head` elementu, který začíná [na řádku 6](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/public/index.html#L6), přidejte metaznačku:

    ```html
    <head>
        <!-- Add this line -->
        <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    ```

1. Soubor uložte.
1. Aktualizujte prohlížeč zařízení. Webová aplikace by se měla vykreslit správně. 

Z tohoto příkladu jasně vyplývá, že některé potíže nezjistíte, dokud aplikaci neotestujete na zařízení, pro které je určená. V Azure Dev Spaces můžete rychle iterovat kód a ověřovat změny na cílových zařízeních.

### <a name="update-a-code-file"></a>Aktualizace souboru s kódem
Aktualizace souborů s kódem na straně serveru je pracnější, protože aplikaci Node.js je potřeba restartovat.

1. V okně terminálu stiskněte `Ctrl+C`, abyste zastavili `azds up`.
1. Otevřete soubor s kódem nazvaný `server.js` a změňte úvodní zprávu služby: 

    ```javascript
    res.send('Hello from webfrontend running in Azure!');
    ```

3. Soubor uložte.
1. V okně terminálu spusťte `azds up`. 

Tento příkaz znovu sestaví image kontejneru a znovu nasadí Helm chart. Načtěte znovu stránku prohlížeče a podívejte se, jestli se promítly změny kódu.

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

![Snímek obrazovky ukazuje výběr příkazu "Azure Dev Spaces: Příprava konfiguračních souborů pro Azure Dev Spaces" z okna paleta příkazů.](media/common/command-palette.png)

### <a name="select-the-azds-debug-configuration"></a>Výběr konfiguraci AZDS pro ladění
1. Pokud chcete zobrazit ladění, klikněte na boku editoru VS Code na **panelu aktivit** na ikonu Ladění.
1. Jako aktivní ladicí konfiguraci vyberte **Launch Program (AZDS)**.

![Snímek obrazovky je v levém horním rohu okna Visual Studio Code. Ikona ladění je zvýrazněna, levý panel má název "ladění" a rozevírací seznam napravo od názvu zobrazuje "spuštění programu (A Z D S).](media/get-started-node/debug-configuration-nodejs2.png)

> [!NOTE]
> Pokud nevidíte žádné příkazy Azure Dev Spaces v paletě příkazů, ujistěte se, že máte [nainstalovanou příponu vs Code pro Azure dev Spaces](get-started-nodejs.md#get-kubernetes-debugging-for-vs-code).

### <a name="debug-the-container-in-kubernetes"></a>Ladění kontejneru v Kubernetes
Stiskněte **F5** pro ladění kódu v Kubernetes!

Podobně jako při příkazu `up` se na začátku ladění synchronizuje kód s vývojovým prostředím a sestaví se kontejner, který se nasadí v Kubernetes. Tentokrát se ale ladicí program připojí ke vzdálenému kontejneru.

> [!TIP]
> Stavový řádek VS Code změní oranžová, což znamená, že ladicí program je připojen. Zobrazí se také adresa URL pro kliknutí, kterou můžete použít k rychlému otevření webu.

![Snímek obrazovky ukazuje dolní část okna Visual Studio Code. Oranžový stavový řádek je poslední řádek. Obsahuje a U R L pro otevření webu.](media/common/vscode-status-bar-url.png)

Nastavte zarážku v souboru kódu na straně serveru, například v `app.get('/api'...` hlavičce na [řádku 13 `server.js` ](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13). 

```javascript
app.get('/api', function (req, res) {
    res.send('Hello from webfrontend');
});
```

Aktualizujte stránku prohlížeče, nebo stiskněte tlačítko *řekněte ho znovu* a měli byste se dostat ke zarážce a být schopni krokovat kód.

Máte plný přístup k informacím o ladění, jako je zásobník volání, místní proměnné, informace o výjimkách apod., stejně jako při lokálním spuštění kódu.

### <a name="edit-code-and-refresh-the-debug-session"></a>Úprava kódu a aktualizace ladicí relace
S aktivním ladicím programem udělejte úpravu kódu; například upravte zprávu Hello na [řádku 13 `server.js` ](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) znovu:

```javascript
app.get('/api', function (req, res) {
    res.send('**** Hello from webfrontend running in Azure! ****');
});
```

Uložte soubor a v **podokně akce ladění**klikněte na tlačítko **restartovat** . 

![Podokno akce ladění je malé podokno v horním středu stránky (hned pod nadpisem stránky). Na tlačítku restartovat se zobrazí KRUHOVÁ ŠIPKA a zvýrazní se. Obrázek tlačítka myši pro tlačítko je "restartovat (CTRL + SHIFT + F 5)".](media/common/debug-action-refresh.png)

Místo opětovného sestavení a nasazení nové image kontejneru po každé provedené změně, což často dlouho trvá, restartuje služba Azure Dev Spaces mezi ladicími relacemi proces Node.js, aby se zrychlil cyklus úprav a ladění.

Aktualizujte webovou aplikaci v prohlížeči nebo stiskněte tlačítko *Aktualizovat*. V uživatelském rozhraní by se měla zobrazit vaše upravená zpráva.

### <a name="use-nodemon-to-develop-even-faster"></a>Zrychlení vývoje nástrojem NodeMon
*Nodemon* je oblíbený nástroj, který používají vývojáři aplikací Node.js k rychlejšímu vývoji. Místo ručního restartování procesu Node po každé úpravě kódu na straně serveru vývojáři často nakonfigurují projekt Node tak, aby změny souboru monitoroval nástroj *nodemon*, který automaticky restartuje procesy na serveru. Při tomto stylu práce vývojář po úpravě kódu jenom aktualizuje prohlížeč.

V Azure Dev Spaces můžete používat řadu stejných vývojových pracovních postupů, jaké používáte při místním vývoji. Pro názornost jsme ukázkový projekt `webfrontend` nakonfigurovali tak, aby používal nástroj *nodemon* (je nakonfigurovaný jako vývojová závislost v souboru `package.json`).

Vyzkoušejte následující kroky:
1. Zastavte ladicí program VS Code.
1. Na boku editoru VS Code na **panelu aktivit** klikněte na ikonu Ladění. 
1. Jako aktivní konfiguraci ladění vyberte **Attach (AZDS)** (Připojit (AZDS)).
1. Stiskněte F5.

Při této konfiguraci kontejner spustí *nodemon*. Po úpravě kódu na straně serveru nástroj *nodemon* automaticky restartuje proces Node úplně stejně, jako při místním vývoji. 
1. Znovu upravte úvodní zprávu v souboru `server.js` a pak soubor uložte.
1. Obnovte prohlížeč nebo klikněte na tlačítko *Aktualizovat*, aby se změny projevily.

**Teď máte metodu, jak rychle provádět iteraci kódu a jeho ladění v Kubernetes.** Příště si ukážeme, jak vytvořit a volat druhý kontejner.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o vývoji více služeb](multi-service-nodejs.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

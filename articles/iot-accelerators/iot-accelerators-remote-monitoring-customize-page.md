---
title: Přidání stránky do uživatelského uživatelského uživatelského zařízení řešení vzdáleného monitorování – Azure | Dokumenty společnosti Microsoft
description: Tento článek ukazuje, jak přidat novou stránku do webového uživatelského uživatelského uživatelského uživatelského uživatelského zařízení akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: 0228f317e2d3380f2387dd557a27203eb3abc4ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240262"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Přidání vlastní stránky do webového uživatelského uživatelského uživatelského nastavení akcelerátoru řešení vzdáleného monitorování

Tento článek ukazuje, jak přidat novou stránku do webového uživatelského uživatelského uživatelského uživatelského uživatelského zařízení akcelerátoru řešení vzdáleného monitorování. Článek popisuje:

- Jak připravit prostředí místního rozvoje.
- Jak přidat novou stránku do webového uživatelského uživatelského uživatelského mise.

Další návody rozšiřují tento scénář a přidávají na stránku, kterou přidáte, další funkce.

## <a name="prerequisites"></a>Požadavky

Chcete-li provést kroky v tomto návodu, potřebujete v místním vývojovém počítači nainstalovaný následující software:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Příprava prostředí místního rozvoje pro ui

Kód uživatelského rozhraní urychlovače řešení vzdáleného monitorování je implementován pomocí architektury [React](https://reactjs.org/) JavaScript. Zdrojový kód najdete v úložišti [GitHub vzdáleného monitorování.](https://github.com/Azure/pcs-remote-monitoring-webui)

Chcete-li provést a otestovat změny v ui, můžete jej spustit na místním vývojovém počítači. Volitelně se místní kopie může připojit k nasazené instanci akcelerátoru řešení a umožnit mu interakci s vašimi skutečnými nebo simulovanými zařízeními.

Chcete-li připravit místní vývojové prostředí, použijte Git ke klonování úložiště [WebUI vzdáleného monitorování](https://github.com/Azure/pcs-remote-monitoring-webui) do místního počítače:

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>Přidat stránku

Chcete-li přidat stránku do webového uživatelského uživatelského nastavení, je třeba přidat zdrojové soubory, které definují stránku, a upravit některé existující soubory tak, aby webové uživatelské uživatelské uživatelské nastavení bylo informováno o nové stránce.

### <a name="add-the-new-files-that-define-the-page"></a>Přidání nových souborů, které definují stránku

Chcete-li začít, **složka src/walkthrough/components/pages/basicPage** obsahuje čtyři soubory, které definují jednoduchou stránku:

**basicPage.container.js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage.js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**basicPage.scss**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage.test.js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

Vytvořte novou složku **src/components/pages/example** a zkopírujte do ní tyto čtyři soubory.

### <a name="add-the-new-page-to-the-web-ui"></a>Přidání nové stránky do webového uživatelského uživatelského uživatelského

Chcete-li přidat novou stránku do webového uživatelského uživatelského uživatelského, proveďte následující změny existujících souborů:

1. Přidejte nový kontejner stránky do souboru **src/components/pages/index.js:**

    ```js
    export * from './example/basicPage.container';
    ```

1. (Nepovinné)  Přidejte ikonu SVG pro novou stránku. Další informace naleznete [v tématu webui/src/utilities/README.md](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md). Můžete použít existující soubor SVG.

1. Přidejte název stránky do souboru překladů, **public/locales/en/translations.json**. Webové uživatelské uživatelské nastavení používá [i18next](https://www.i18next.com/) pro internacionalizaci.

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. Otevřete soubor **src/components/app.js,** který definuje stránku aplikace nejvyšší úrovně. Přidejte novou stránku do seznamu importů:

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. Ve stejném souboru přidejte novou `pagesConfig` stránku do pole. Nastavte `to` adresu trasy, nazvěte ikonu SVG a překlady `component` přidané dříve a nastavte na kontejner stránky:

    ```js
    const pagesConfig = [
      //...
      {
        to: '/basicpage',
        exact: true,
        svg: svgs.tabs.example,
        labelId: 'tabs.basicPage',
        component: BasicPageContainer
      },
      //...
    ];
    ```

1. Přidejte do `crumbsConfig` pole nové popisy cesty:

    ```js
    const crumbsConfig = [
      //...
      {
        path: '/basicpage', crumbs: [
          { to: '/basicpage', labelId: 'tabs.basicPage' }
        ]
      },
      //...
    ];
    ```

    Tato ukázková stránka má pouze jednu popis cesty, ale některé stránky mohou mít více.

Uložte všechny provedené změny. S přidanou novou stránkou můžete spustit webové uživatelské uživatelské nastavení.

### <a name="test-the-new-page"></a>Testování nové stránky

Na příkazovém řádku přejděte do kořenového adresáře místní kopie úložiště a spusťte následující příkazy pro instalaci požadovaných knihoven a místní spuštění webového uživatelského prostředí:

```cmd/sh
npm install
npm start
```

Předchozí příkaz spustí ui místně [http://localhost:3000/dashboard](http://localhost:3000/dashboard)na .

Bez připojení místní instance webového uživatelského prostředí k nasazené instanci akcelerátoru řešení se na řídicím panelu zobrazí chyby. Tyto chyby nemají vliv na možnost testování nové stránky.

Nyní můžete upravit kód, když je web spuštěn místně a dynamicky zobrazit aktualizaci webového uživatelského prostředí.

## <a name="optional-connect-to-deployed-instance"></a>[Nepovinné] Připojení k nasazené instanci

Volitelně můžete připojit místní spuštěnou kopii webového uživatelského prostředí k akcelerátoru řešení vzdáleného monitorování v cloudu:

1. Nasazení **základní** instance akcelerátoru řešení pomocí **příkazového příkazového příkazu pcs.** Poznamenejte si název nasazení a přihlašovací údaje, které jste zadali pro virtuální počítač. Další informace naleznete v [tématu Deploy using the CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Pomocí portálu Azure nebo [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) povolte přístup SSH k virtuálnímu počítači, který hostuje mikroslužeb ve vašem řešení. Například:

    ```azurecli
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Přístup SSH byste měli povolit pouze během testování a vývoje. Pokud povolíte SSH, [měli byste jej co nejdříve znovu zakázat](../security/fundamentals/network-best-practices.md).

1. Pomocí portálu Azure nebo [příkazového řádku az](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) najdete název a veřejnou IP adresu vašeho virtuálního počítače. Například:

    ```azurecli
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Pomocí SSH se můžete připojit k virtuálnímu počítači pomocí IP adresy z předchozího kroku a přihlašovacích údajů, které jste zadali při spuštění **počítačů** k nasazení řešení.

1. Chcete-li povolit připojení místního uživatelského prostředí, spusťte následující příkazy v prostředí bash ve virtuálním počítači:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Po dokončení příkazu a spuštění webu se můžete odpojit od virtuálního počítače.

1. V místní kopii úložiště [WebUI vzdáleného monitorování](https://github.com/Azure/pcs-remote-monitoring-webui) upravte soubor **ENV** a přidejte adresu URL nasazeného řešení:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o dostupných prostředcích, které vám pomohou přizpůsobit webové uživatelské rozhraní v akcelerátoru řešení vzdáleného monitorování.

Nyní jste definovali stránku, dalším krokem je [přidání vlastní služby do webového uživatelského uživatelského uživatelského nastavení akcelerátoru řešení vzdáleného monitorování,](iot-accelerators-remote-monitoring-customize-service.md) které načítá data, která se mají zobrazit v uživatelském ukazateli.

Další rámcové informace o akcelerátoru řešení vzdáleného monitorování naleznete v [tématu Architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md).

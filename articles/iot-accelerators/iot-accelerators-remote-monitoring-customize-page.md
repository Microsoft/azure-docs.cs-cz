---
title: Přidání stránky do uživatelského rozhraní řešení vzdáleného monitorování – Azure | Microsoft Docs
description: V tomto článku se dozvíte, jak přidat novou stránku do webového uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: e585bc52121943102522f8c44291a4f9453d6214
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318394"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Přidat vlastní stránku do webového uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování

V tomto článku se dozvíte, jak přidat novou stránku do webového uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování. Článek popisuje:

- Jak připravit místní vývojové prostředí.
- Postup přidání nové stránky do webového uživatelského rozhraní

Další průvodce návody rozšiřuje tento scénář, aby bylo možné na stránku, kterou přidáte, přidat další funkce.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto průvodci, potřebujete na svém místním vývojovém počítači nainstalovaný následující software:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Příprava místního vývojového prostředí pro uživatelské rozhraní

Kód uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování je implementován pomocí rozhraní JavaScript s [reakci](https://reactjs.org/) . Zdrojový kód najdete v úložišti GitHub [WebUI vzdáleného monitorování](https://github.com/Azure/pcs-remote-monitoring-webui) .

Chcete-li provést a otestovat změny v uživatelském rozhraní, můžete je spustit na místním vývojovém počítači. Volitelně se místní kopie může připojit k nasazené instanci akcelerátoru řešení, aby mohla spolupracovat se skutečnými nebo simulovanými zařízeními.

Pokud chcete připravit místní vývojové prostředí, pomocí Gitu naklonujte úložiště [vzdáleného monitorování WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) do místního počítače:

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>Přidat stránku

Chcete-li přidat stránku do webového uživatelského rozhraní, je nutné přidat zdrojové soubory, které definují stránku, a změnit některé existující soubory tak, aby webové uživatelské rozhraní bylo na nové stránce vědomo.

### <a name="add-the-new-files-that-define-the-page"></a>Přidat nové soubory, které definují stránku

Chcete-li začít, složka **Src/názor/komponenty/Pages/šablonou základní stránky** obsahuje čtyři soubory definující jednoduchou stránku:

**basicPage.container.js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage.js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**Šablonou základní stránky. scss**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage.test.js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

Vytvoří novou složku **Src/Components/Pages/example** a zkopíruje do ní tyto čtyři soubory.

### <a name="add-the-new-page-to-the-web-ui"></a>Přidání nové stránky do webového uživatelského rozhraní

Chcete-li přidat novou stránku do webového uživatelského rozhraní, proveďte následující změny existujících souborů:

1. Přidejte nový kontejner stránky do souboru **Src/Components/Pages/index.js** :

    ```js
    export * from './example/basicPage.container';
    ```

1. Volitelné  Přidejte ikonu SVG pro novou stránku. Další informace najdete v tématu [WebUI/src/Utilities/Readme. MD](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md). Můžete použít existující soubor SVG.

1. Přidejte název stránky do souboru s překlady, **Public/Locals/EN/translations.json**. Webové uživatelské rozhraní používá [i18next](https://www.i18next.com/) pro mezinárodní účely.

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. Otevřete soubor **Src/Components/app.js** , který definuje stránku aplikace nejvyšší úrovně. Přidejte novou stránku do seznamu importů:

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. Ve stejném souboru přidejte novou stránku do `pagesConfig` pole. Nastavte `to` adresu pro trasu, odkazujte na dříve přidané ikony SVG a překlady a nastavte na `component` kontejner stránky:

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

1. Přidejte jakékoli nové popisy cesty k `crumbsConfig` poli:

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

    Tato Ukázková stránka obsahuje jenom jednu popis cesty, ale některé stránky můžou mít víc.

Uložte všechny provedené změny. Jste připraveni spustit webové uživatelské rozhraní s přidanou novou stránkou.

### <a name="test-the-new-page"></a>Otestování nové stránky

Na příkazovém řádku přejděte do kořenového adresáře místní kopie úložiště a spuštěním následujících příkazů nainstalujte požadované knihovny a spusťte webové uživatelské rozhraní lokálně:

```cmd/sh
npm install
npm start
```

Předchozí příkaz spustí uživatelské rozhraní lokálně na `http://localhost:3000/dashboard` .

Bez připojení místní instance webového uživatelského rozhraní k nasazené instanci akcelerátoru řešení vidíte chyby na řídicím panelu. Tyto chyby neovlivňují vaši možnost testování vaší nové stránky.

Kód teď můžete upravovat, když je web spuštěný místně a dynamicky se zobrazuje aktualizace webového uživatelského rozhraní.

## <a name="optional-connect-to-deployed-instance"></a>Volitelné Připojit k nasazené instanci

Volitelně můžete připojit místní běžící kopii webového uživatelského rozhraní k akcelerátoru řešení vzdáleného monitorování v cloudu:

1. Nasaďte **základní** instanci akcelerátoru řešení pomocí rozhraní příkazového řádku pro **počítače** . Poznamenejte si název vašeho nasazení a přihlašovací údaje, které jste zadali pro virtuální počítač. Další informace najdete v tématu [nasazení pomocí rozhraní](iot-accelerators-remote-monitoring-deploy-cli.md)příkazového řádku.

1. Pomocí Azure Portal nebo [AZ CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) povolte přístup SSH k virtuálnímu počítači, který je hostitelem mikroslužeb ve vašem řešení. Například:

    ```azurecli
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Během testování a vývoje byste měli povolit jenom přístup přes SSH. Pokud povolíte SSH, [měli byste ho znovu deaktivovat, jakmile to bude možné](../security/fundamentals/network-best-practices.md).

1. Pomocí Azure Portal nebo [AZ CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) najděte název a veřejnou IP adresu vašeho virtuálního počítače. Například:

    ```azurecli
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Pomocí SSH se připojte k virtuálnímu počítači pomocí IP adresy z předchozího kroku a přihlašovací údaje, které jste zadali při spuštění **počítače** k nasazení řešení.

1. Pokud chcete místnímu UŽIVATELSKÉmu prostředí připojit, spusťte v prostředí bash ve virtuálním počítači následující příkazy:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Až se zobrazí příkaz dokončí se a web se spustí, můžete se z virtuálního počítače odpojit.

1. V místní kopii úložiště [WebUI vzdáleného monitorování](https://github.com/Azure/pcs-remote-monitoring-webui) upravte soubor **. env** a přidejte tak adresu URL vašeho nasazeného řešení:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o dostupných zdrojích, které vám pomůžou přizpůsobit webové uživatelské rozhraní v akcelerátoru řešení vzdáleného monitorování.

Teď jste definovali stránku. dalším krokem je [Přidání vlastní služby do webového uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-service.md) , které načte data, která se mají zobrazit v uživatelském rozhraní.

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování najdete v tématu [architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md).

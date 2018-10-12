---
title: Přidání stránky do řešení vzdálené monitorování uživatelské rozhraní – Azure | Dokumentace Microsoftu
description: Tento článek ukazuje, jak přidat nové stránky do vzdáleného monitorování řešení akcelerátoru webového uživatelského rozhraní.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: 95830cdffb232e16f9fbae51cfa11fbd18172c3c
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49094556"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Přidat vlastní stránky pro vzdálené monitorování řešení akcelerátoru webového uživatelského rozhraní

Tento článek ukazuje, jak přidat nové stránky do vzdáleného monitorování řešení akcelerátoru webového uživatelského rozhraní. Tento článek popisuje:

- Jak připravit místní vývojové prostředí.
- Postup přidání nové stránky do webového uživatelského rozhraní.

Další návody rozšiřte tento scénář přidat na stránku, kterou přidáte další funkce.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v této příručce s postupy, musíte na svém místním vývojovém počítači nainstalovaný následující software:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Příprava prostředí pro místní vývoj uživatelského rozhraní

Akcelerátor řešení vzdálené monitorování kód uživatelského rozhraní je implementováno pomocí [React](https://reactjs.org/) Javascriptové architektury. Můžete najít zdrojový kód v [vzdálené monitorování WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) úložiště GitHub.

Chcete-li vytvořit a testovat změny v uživatelském rozhraní, můžete ji spustit na svém místním vývojovém počítači. Místní kopii lze volitelně připojit k nasazené instance akcelerátor řešení, který umožňuje pracovat s reálných nebo simulovaných zařízení.

Příprava místního vývojového prostředí, pomocí Gitu naklonujte [vzdálené monitorování WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) úložiště do svého místního počítače:

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>Přidat stránku

Přidání stránky do webového uživatelského rozhraní, budete muset přidat zdrojové soubory, které definují stránky a změnit některé existující soubory ve webovém uživatelském rozhraní vědět nová stránka.

### <a name="add-the-new-files-that-define-the-page"></a>Přidat nové soubory, které definují stránky

Abyste mohli začít, **src/návod/součásti/stránek/šablonou základní** složka obsahuje čtyři soubory, které definují jednoduché stránky:

**basicPage.container.js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage.js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**basicPage.scss**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage.test.js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

Vytvořte novou složku **src/součásti/stránek/příklad** a zkopírujte do něj tyto čtyři soubory.

### <a name="add-the-new-page-to-the-web-ui"></a>Přidejte novou stránku do webového uživatelského rozhraní

Chcete-li přidat novou stránku do webového uživatelského rozhraní, proveďte následující změny k existující soubory:

1. Přidat nové stránky kontejneru **src/components/pages/index.js** souboru:

    ```js
    export * from './example/basicPage.container';
    ```

1. (Volitelné)  Přidáte ikonu SVG nové stránky. Další informace najdete v tématu [webui/src/utilities/README.md](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md). Můžete použít existující soubor SVG.

1. Přidejte název stránky do souboru překlady **public/locales/en/translations.json**. Webové uživatelské rozhraní používá [i18next](https://www.i18next.com/) pro internacionalizaci.

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. Otevřít **src/components/app.js** soubor, který definuje stránce aplikace nejvyšší úrovně. Přidejte novou stránku do seznamu importů:

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. Ve stejném souboru přidejte novou stránku `pagesConfig` pole. Nastavte `to` adres pro trasu, odkaz SVG ikonu a překlady přidali dříve, nastavte `component` na stránce kontejneru:

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

1. Přidat všechny nové popisu `crumbsConfig` pole:

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

    Tento příklad stránky má jenom jednu s popisem cesty, ale některé stránky mohou mít více.

Uložte všechny provedené změny. Jste připraveni spustit ve webovém uživatelském rozhraní se přidat nové stránky.

### <a name="test-the-new-page"></a>Otestovat novou stránku

Na příkazovém řádku přejděte do kořenové složce místní kopie úložiště a spusťte následující příkazy instalace potřebných knihoven a spustit místně ve webovém uživatelském rozhraní:

```cmd/sh
npm install
npm start
```

Předchozí příkaz se spustí místně na uživatelské rozhraní [ http://localhost:3000/dashboard ](http://localhost:3000/dashboard).

Bez připojení vaší místní instance webové uživatelské rozhraní k nasazené instance akcelerátor řešení, uvidíte na řídicím panelu chyby. Tyto chyby nemají vliv na schopnost testování nové stránky.

Teď můžete upravit jeho kód, zatímco je web spuštěný místně a najdete v článku webu uživatelského rozhraní dynamicky aktualizovat.

## <a name="optional-connect-to-deployed-instance"></a>[Volitelné] Připojte se k nasazené instance

Volitelně můžete připojit místní kopie spuštěné ve webovém uživatelském rozhraní pro akcelerátor řešení vzdálené monitorování v cloudu:

1. Nasazení **základní** instanci pomocí akcelerátor řešení **počítače** rozhraní příkazového řádku. Poznamenejte si název vašeho nasazení a přihlašovací údaje, které jste zadali pro virtuální počítač. Další informace najdete v tématu [nasazení pomocí rozhraní příkazového řádku](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Pomocí webu Azure portal nebo [az rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) povolit přístup přes SSH k virtuálnímu počítači, který je hostitelem mikroslužeb ve vašem řešení. Příklad:

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Přístup přes SSH byste měli povolit jenom během vývoj a testování. Pokud povolíte SSH, [byste měli znovu co nejdříve zakázat](../security/azure-security-network-security-best-practices.md).

1. Pomocí webu Azure portal nebo [az rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) najít název a veřejnou IP adresu vašeho virtuálního počítače. Příklad:

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Pomocí SSH se připojte k virtuálnímu počítači pomocí IP adresy z předchozího kroku a přihlašovací údaje, které jste zadali při spuštění **počítače** k nasazení řešení.

1. Pokud chcete povolit místní uživatelského rozhraní pro připojení, spusťte následující příkazy v prostředí bash ve virtuálním počítači:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Jakmile ověříte, že se příkaz dokončí a spustí webovou stránku, můžete odpojit od virtuálního počítače.

1. V místní kopii [vzdálené monitorování WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) úložiště, upravit **.env** soubor a přidejte adresu URL vašeho nasazeného řešení:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli o prostředky dostupné pro vám umožní přizpůsobit uživatelské rozhraní v akcelerátoru řešení vzdáleného monitorování.

Nyní jste definovali stránku, dalším krokem je [přidat vlastní službu vzdáleného monitorování řešení akcelerátoru uživatelské rozhraní](iot-accelerators-remote-monitoring-customize-service.md) , která načte data se mají zobrazit v uživatelském rozhraní.

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování najdete v tématu [architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md).

---
title: 'Rychlý Start: váš první dotaz v jazyce JavaScript'
description: V tomto rychlém startu budete postupovat podle pokynů pro povolení knihovny grafu prostředků pro JavaScript a spuštění prvního dotazu.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 97cbd9ddf65a4135f55304f6dd67c704c6fcac5d
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917517"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-javascript"></a>Rychlý Start: spuštění prvního dotazu na graf prostředku pomocí JavaScriptu

Tento rychlý Start vás provede procesem přidávání knihoven do instalace JavaScriptu. Prvním krokem při použití Azure Resource graphu je inicializace aplikace JavaScriptu s požadovanými knihovnami.

Na konci tohoto procesu jste přidali knihovny do instalace JavaScriptu a spustíte svůj první dotaz na graf prostředku.

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure**: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

- **Node.js**: je vyžadována [Node.js](https://nodejs.org/) verze 12 nebo vyšší.

## <a name="application-initialization"></a>Inicializace aplikací

Pokud chcete povolit JavaScriptu dotazování na Azure Resource Graph, musí být prostředí nakonfigurované. Tato instalace funguje bez ohledu na to, kde je možné použít JavaScript, včetně [bash ve Windows 10](/windows/wsl/install-win10).

1. Spusťte následující příkaz pro inicializaci nového projektu Node.js.

   ```bash
   npm init -y
   ```

1. Přidejte odkaz na modul yargs.

   ```bash
   npm install yargs
   ```

1. Přidejte odkaz na modul Azure Resource Graph.

   ```bash
   npm install @azure/arm-resourcegraph
   ```

1. Přidejte odkaz na knihovnu ověřování Azure.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Ověřte v _package.js_ `@azure/arm-resourcegraph` verze **2.0.0** nebo novější a `@azure/ms-rest-nodeauth` verze **3.0.3** nebo vyšší.

## <a name="query-the-resource-graph"></a>Dotazování grafu prostředků

1. Vytvořte nový soubor s názvem _index.js_ a zadejte následující kód.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const resourceGraph = require("@azure/arm-resourcegraph");

   if (argv.query && argv.subs) {
       const subscriptionList = argv.subs.split(",");

       const query = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new resourceGraph.ResourceGraphClient(credentials);
          const result = await client.resources(
             {
                 query: argv.query,
                 subscriptions: subscriptionList,
             },
             { resultFormat: "table" }
          );
          console.log("Records: " + result.totalRecords);
          console.log(result.data);
       };

       query();
   }
   ```

1. Do terminálu zadejte následující příkaz:

   ```bash
   node index.js --query "Resources | project name, type | limit 5" --subs <YOUR_SUBSCRIPTION_ID_LIST>
   ```

   `<YOUR_SUBSCRIPTION_ID_LIST>`Zástupný text nahraďte čárkami odděleným seznamem ID předplatných Azure.

   > [!NOTE]
   > Jelikož tento příklad dotazu neposkytuje modifikátor řazení, jako je `order by` spuštění tohoto dotazu vícekrát, je pravděpodobně výsledkem jiné sady prostředků na žádost.

1. Změňte první parametr na `index.js` a změňte dotaz na `order by` vlastnost **Name** . Nahraďte `<YOUR_SUBSCRIPTION_ID_LIST>` ID předplatného:

   ```bash
   node index.js --query "Resources | project name, type | limit 5 | order by name asc" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

   Při pokusu o ověření skriptu se v terminálu zobrazí zpráva podobná následující zprávě:

   > Pokud se chcete přihlásit, otevřete stránku pomocí webového prohlížeče https://microsoft.com/devicelogin a zadejte kód FGB56WJUGK, který se má ověřit.

   Po ověření v prohlížeči bude skript dál běžet.

   > [!NOTE]
   > Stejně jako u prvního dotazu opakované spouštění tohoto dotazu pravděpodobně poskytne jinou sadu zdrojů na jednu žádost. Pořadí příkazů dotazů je důležité. V tomto příkladu `order by` přichází po `limit`. Toto pořadí příkazů nejprve omezí výsledky dotazu a pak je vyřadí.

1. Změňte první parametr na `index.js` a změňte dotaz na první `order by` vlastnost **název** a potom `limit` na pět nejlepších výsledků. Nahraďte `<YOUR_SUBSCRIPTION_ID_LIST>` ID předplatného:

   ```bash
   node index.js --query "Resources | project name, type | order by name asc | limit 5" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

Když se konečný dotaz několikrát spustí, předpokládá se, že se nic ve vašem prostředí nemění, vrácené výsledky jsou konzistentní a seřazené podle vlastnosti **Name** , ale pořád se omezí na pět nejlepších výsledků.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat nainstalované knihovny z aplikace, spusťte následující příkaz.

```bash
npm uninstall @azure/arm-resourcegraph @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přidali knihovny grafu prostředků do prostředí JavaScriptu a spustili svůj první dotaz. Chcete-li získat další informace o jazyku grafu prostředků, přejděte na stránku podrobností dotazovacího jazyka.

> [!div class="nextstepaction"]
> [Získat další informace o dotazovacím jazyku](./concepts/query-language.md)
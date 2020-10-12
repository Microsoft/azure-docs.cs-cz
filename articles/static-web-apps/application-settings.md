---
title: Konfigurace nastavení aplikace pro statickou Web Apps Azure
description: Naučte se konfigurovat nastavení aplikace pro statickou Web Apps Azure
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: c89ccee430d374d9aee58326627ff800f737324b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91250024"
---
# <a name="configure-application-settings-for-azure-static-web-apps-preview"></a>Konfigurace nastavení aplikace pro Azure static Web Apps Preview

Nastavení aplikace uchovávají nastavení konfigurace pro hodnoty, které se mohou změnit, například připojovací řetězce databáze. Přidání nastavení aplikace umožňuje změnit vstup konfigurace do vaší aplikace, aniž byste museli měnit kód aplikace.

Nastavení aplikace jsou:

- Šifrované v klidovém stavu
- Zkopírováno do [přípravného](review-publish-pull-requests.md) a produkčního prostředí

Nastavení aplikace se někdy označují také jako proměnné prostředí.

> [!IMPORTANT]
> Nastavení aplikace popsaná v tomto článku se vztahují jenom na back-endové rozhraní API statické webové aplikace Azure.
>
> Informace o použití proměnných prostředí u vaší webové aplikace front-end najdete v dokumentaci k vašemu [rozhraní JavaScript](#javascript-frameworks-and-libraries) nebo ke [generátoru statických webů](#static-site-generators).

## <a name="prerequisites"></a>Požadavky

- Aplikace statického Web Apps Azure
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="types-of-application-settings"></a>Typy nastavení aplikace

Existují obvykle dvě aspekty aplikace statické Web Apps Azure. První je webová aplikace nebo statický obsah, který je reprezentován HTML, CSS, JavaScriptem a obrázky. Druhým je back-endové rozhraní API, které používá aplikace Azure Functions.

Tento článek ukazuje, jak spravovat nastavení aplikace pro back-endové rozhraní API v Azure Functions.

Nastavení aplikace popsané v tomto článku nelze použít ani na něj nelze odkazovat ve statických webových aplikacích. Mnoho rozhraní front-end a generátory statických webů však umožňuje použití proměnných prostředí během vývoje. V okamžiku sestavení jsou tyto proměnné nahrazeny jejich hodnotami ve vygenerovaném kódu HTML nebo JavaScriptu. Vzhledem k tomu, že data v HTML a JavaScriptu je snadno zjistitelná návštěvníkem lokality, chcete se vyhnout vkládání citlivých informací do front-endové aplikace. Nastavení, která uchovávají citlivá data, jsou nejlépe umístěná v části rozhraní API vaší aplikace.

Informace o použití proměnných prostředí s vaším JavaScriptovým rozhraním nebo knihovnou najdete v následujících článcích, kde najdete další podrobnosti.

### <a name="javascript-frameworks-and-libraries"></a>JavaScriptové architektury a knihovny

- [Angular](https://angular.io/guide/build#configuring-application-environments)
- [React](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Svelte](https://linguinecode.com/post/how-to-add-environment-variables-to-your-svelte-js-app)
- [Vue](https://cli.vuejs.org/guide/mode-and-env.html)

### <a name="static-site-generators"></a>Generátory statických webů

- [Gatsby](https://www.gatsbyjs.org/docs/environment-variables/)
- [Hugo](https://gohugo.io/getting-started/configuration/)
- [Jekyll](https://jekyllrb.com/docs/configuration/environments/)

## <a name="about-api-app-settings"></a>O nastavení aplikace API

Rozhraní API ve statických Web Appsch Azure využívají technologii Azure Functions, která umožňuje definovat nastavení aplikace v _local.settings.js_ souboru. Tento soubor definuje nastavení aplikace v `Values` Vlastnosti konfigurace.

Následující vzorový _local.settings.js_ ukazuje, jak přidat hodnotu pro `DATABASE_CONNECTION_STRING` .

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
  }
}
```

Na nastavení definované ve `Values` vlastnosti může být odkazováno z kódu jako proměnné prostředí, které jsou k dispozici z `process.env` objektu.

```js
const connectionString = process.env.DATABASE_CONNECTION_STRING;
```

`local.settings.json`Soubor není sledován úložištěm GitHub, protože v souboru jsou často zahrnuté citlivé informace, jako jsou databázové připojovací řetězce. Vzhledem k tomu, že místní nastavení zůstávají na vašem počítači, budete muset ručně odeslat nastavení do Azure.

Obecně platí, že nahrávání nastavení se provádí zřídka a nevyžaduje se u každého buildu.

## <a name="uploading-application-settings"></a>Nahrávání nastavení aplikace

Nastavení aplikace můžete nakonfigurovat prostřednictvím Azure Portal nebo pomocí Azure CLI.

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Azure Portal poskytuje rozhraní pro vytváření, aktualizaci a odstraňování nastavení aplikace.

1. Přejděte na [Azure Portal](https://portal.azure.com).

1. Na panelu hledání vyhledejte a vyberte **statické Web Apps**.

1. Na bočním panelu klikněte na možnost **Konfigurace** .

1. Vyberte prostředí, pro které chcete použít nastavení aplikace. Přípravná prostředí se automaticky vytvoří při vygenerování žádosti o přijetí změn a po sloučení žádosti o přijetí změn se zvýší na produkční prostředí. Můžete nastavit nastavení aplikace na jedno prostředí.

1. Kliknutím na **tlačítko Přidat** přidejte nové nastavení aplikace.

    :::image type="content" source="media/application-settings/configuration.png" alt-text="Zobrazení konfigurace statického Web Apps Azure":::

1. Zadejte **název** a **hodnotu**.

1. Klikněte na **OK**.

1. Klikněte na **Uložit**.

### <a name="using-the-azure-cli"></a>Použití Azure CLI

Pomocí `az rest` příkazu můžete hromadně načítat nastavení do Azure. Příkaz přijme nastavení aplikace jako objekty JSON v nadřazené vlastnosti s názvem `properties` .

Nejjednodušší způsob, jak vytvořit soubor JSON s příslušnými hodnotami, je vytvořit upravenou verzi _local.settings.jsv_ souboru.

1. Aby se zajistilo, že váš nový soubor s citlivými daty nebude veřejně vystavený, přidejte do souboru _. gitignore_ následující položku.

   ```bash
   local.settings*.json
   ```

2. Dále vytvořte kopii _local.settings.jsna_ soubor a pojmenujte ji _local.settings.properties.jsna_.

3. V novém souboru odeberte všechna ostatní data ze souboru s výjimkou nastavení aplikace a přejmenujte `Values` na `properties` .

   Váš soubor by teď měl vypadat podobně jako v následujícím příkladu:

   ```json
   {
     "properties": {
       "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
     }
   }
   ```

Příkaz Azure CLI vyžaduje pro váš účet několik hodnot, které jsou specifické pro spuštění nahrávání. V okně _Přehled_ prostředku statického Web Apps máte přístup k následujícím informacím:

1. Název statického webu
2. Název skupiny prostředků
3. ID předplatného

:::image type="content" source="media/application-settings/overview.png" alt-text="Zobrazení konfigurace statického Web Apps Azure":::

4. Z terminálu nebo příkazového řádku spusťte následující příkaz. Nezapomeňte nahradit zástupné symboly `<YOUR_STATIC_SITE_NAME>` , `<YOUR_RESOURCE_GROUP_NAME>` a `<YOUR_SUBSCRIPTION_ID>` pomocí hodnot z okna _Přehled_ .

   ```bash
   az rest --method put --headers "Content-Type=application/json" --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/config/functionappsettings?api-version=2019-12-01-preview" --body @local.settings.properties.json
   ```

> [!IMPORTANT]
> Soubor local.settings.properties.json musí být ve stejném adresáři, ve kterém je tento příkaz spuštěn. Tento soubor může být pojmenován cokoli, co chcete. Název není významný.

### <a name="view-application-settings-with-the-azure-cli"></a>Zobrazení nastavení aplikace pomocí Azure CLI

Nastavení aplikace jsou k dispozici pro zobrazení prostřednictvím rozhraní příkazového řádku Azure CLI.

- Z terminálu nebo příkazového řádku spusťte následující příkaz. Zástupné symboly Nezapomeňte nahradit `<YOUR_SUBSCRIPTION_ID>` `<YOUR_RESOURCE_GROUP_NAME>` `<YOUR_STATIC_SITE_NAME>` hodnotami.

   ```bash
   az rest --method post --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/listFunctionAppSettings?api-version=2019-12-01-preview"
   ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nastavení místního vývoje](local-development.md)

---
title: 'Kurz: nasazení serverových Nuxt.jsových webů ve službě Azure static Web Apps'
description: Vygenerujte a nasaďte Nuxt.js dynamické lokality se statickou Web Apps Azure.
services: static-web-apps
author: christiannwamba
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: chnwamba
ms.custom: devx-track-js
ms.openlocfilehash: 8f21f5fa8ee9035fe594cecff37a63b1ef2115cc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97563462"
---
# <a name="deploy-server-rendered-nuxtjs-websites-on-azure-static-web-apps-preview"></a>Nasazení Nuxt.js webů vygenerovaných serverem ve službě Azure static Web Apps Preview

V tomto kurzu se naučíte nasadit [Nuxt.js](https://nuxtjs.org) generovaný statický web do služby [Azure static Web Apps](overview.md). Pokud chcete začít, naučíte se, jak nastavit, nakonfigurovat a nasadit aplikaci Nuxt.js. Během tohoto procesu se naučíte také řešit běžné výzvy, které se často vyskytují při generování statických stránek pomocí Nuxt.js

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/).
- Účet GitHub. [Vytvořte si účet zdarma](https://github.com/join).
- [Node.js](https://nodejs.org) nainstalován.

## <a name="set-up-a-nuxtjs-app"></a>Nastavení aplikace Nuxt.js

Můžete nastavit nový Nuxt.js projekt pomocí `create-nuxt-app` . Místo nového projektu můžete v tomto kurzu začít klonovat stávající úložiště. Toto úložiště je nastavené tak, aby předvedlo, jak nasadit dynamickou aplikaci Nuxt.js jako statickou lokalitu.

1. Vytvořte nové úložiště pod účtem GitHub z úložiště šablon.
1. Přejít na [http://github.com/staticwebdev/nuxtjs-starter/generate](https://github.com/login?return_to=/staticwebdev/nuxtjs-starter/generate)
1. Pojmenování úložiště **nuxtjs-Starter**
1. Pak na svém počítači naklonujte nové úložiště. Nezapomeňte nahradit <YOUR_GITHUB_ACCOUNT_NAME> název svého účtu.

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nuxtjs-starter
    ```

1. Přejděte do nově naklonované Nuxt.js aplikace:

   ```bash
   cd nuxtjs-starter
   ```

1. Závislosti instalace:

    ```bash
    npm install
    ```

1. Spustit aplikaci Nuxt.js ve vývoji:

    ```bash
    npm run dev
    ```

Přejděte na adresu `http://localhost:3000` a otevřete aplikaci, kde by se měl zobrazit následující web v upřednostňovaném prohlížeči:

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-app.png" alt-text="Spustit aplikaci Nuxt.js":::

Když kliknete na architekturu nebo knihovnu, měla by se zobrazit Stránka s podrobnostmi o vybrané položce:

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-details.png" alt-text="Stránka podrobností":::

## <a name="generate-a-static-website-from-nuxtjs-build"></a>Vygenerování statického webu z Nuxt.js sestavení

Když vytváříte lokalitu Nuxt.js pomocí nástroje `npm run build` , je aplikace sestavena jako tradiční webová aplikace, nikoli jako statická lokalita. Chcete-li vygenerovat statickou lokalitu, použijte následující konfiguraci aplikace.

1. Aktualizujte _package.js_ skript sestavení tak, aby vygeneroval pouze statickou lokalitu pomocí `nuxt generate` příkazu:

    ```json
    "scripts": {
      "dev": "nuxt dev",
      "build": "nuxt generate",
    },
    ```

    Když je tento příkaz na místě, statický Web Apps spustí `build` skript při každém vložení potvrzení změn.

1. Generovat statický web:

    ```bash
    npm run build
    ```

    Nuxt.js vygeneruje statický web a zkopíruje ho do složky _DIST_ v kořenovém adresáři vašeho pracovního adresáře.

    > [!NOTE]
    > Tato složka je uvedena v souboru _. gitignore_ , protože při nasazení by měla být generována pomocí CI/CD.

## <a name="push-your-static-website-to-github"></a>Vložení statického webu do GitHubu

Azure static Web Apps nasadí vaši aplikaci z úložiště GitHubu a zachová se tak pro všechna vložená potvrzení do určené větve. Pomocí následujících příkazů Synchronizujte změny na GitHubu.

1. Připravit všechny změněné soubory:

    ```bash
    git add .
    ```

1. Potvrdit všechny změny

    ```bash
    git commit -m "Update build config"
    ```

1. Doručovat změny do GitHubu.

    ```bash
    git push origin main
    ```

## <a name="deploy-your-static-website"></a>Nasazení statického webu

Následující kroky ukazují, jak propojit aplikaci, kterou jste právě odeslali do GitHubu, do služby Azure static Web Apps. V Azure můžete aplikaci nasadit do produkčního prostředí.

### <a name="create-an-azure-static-web-apps-preview-resource"></a>Vytvoření prostředku Azure static Web Apps Preview

1. Přejít na [Azure Portal](https://portal.azure.com)
1. Klikněte na **Vytvořit prostředek**.
1. Vyhledejte **Static Web Apps**.
1. Klikněte na **Static Web Apps (Preview)**
1. Klikněte na **Vytvořit**.

1. V rozevíracím seznamu *předplatné* vyberte předplatné nebo použijte výchozí hodnotu.
1. V rozevíracím seznamu *Skupina prostředků* klikněte na **Nový** odkaz. Do *nového názvu skupiny prostředků* zadejte **mystaticsite** a klikněte na **OK** .
1. Do textového pole **název** zadejte globálně jedinečný název vaší aplikace. Mezi platné znaky patří `a-z` , `A-Z` , `0-9` a `-` . Tato hodnota se používá jako předpona adresy URL vaší statické aplikace ve formátu `https://<APP_NAME>.azurestaticapps.net` .
1. V rozevíracím seznamu *oblast* vyberte oblast, která je pro vás nejblíže.
1. V rozevíracím seznamu SKU vyberte **volné** .

   :::image type="content" source="media/deploy-nuxtjs/create-static-web-app.png" alt-text="Vytvoření statické webové aplikace":::

### <a name="add-a-github-repository"></a>Přidat úložiště GitHub

Nový účet statického Web Apps potřebuje přístup k úložišti pomocí aplikace Nuxt.js, aby mohl automaticky nasadit potvrzení změn.

1. Klikněte na **tlačítko Přihlásit se pomocí GitHubu** .
1. Vyberte **organizaci** , ve které jste úložiště vytvořili pro svůj Nuxt.js projekt, což může být vaše uživatelské jméno GitHubu.
1. Vyhledejte a vyberte název úložiště, které jste vytvořili dříve.
1. Z rozevíracího seznamu *větev* vyberte **Main (Hlavní** ) jako větev.

   :::image type="content" source="media/deploy-nuxtjs/connect-github.png" alt-text="Připojení ke GitHubu":::

### <a name="configure-the-build-process"></a>Konfigurace procesu sestavení

Statická Web Apps Azure je sestavená tak, aby automaticky provedla běžné úkoly, jako je instalace modulů npm a spuštění `npm run build` během každého nasazení. Existuje však několik nastavení, jako je zdrojová složka aplikace a cílová složka pro sestavení, které je třeba nakonfigurovat ručně.

1. Chcete-li nakonfigurovat statickou výstupní složku, klikněte na kartu **sestavení** .

      :::image type="content" source="media/deploy-nuxtjs/build-tab.png" alt-text="Karta sestavení":::

1. Do textového pole *umístění artefaktu aplikace* zadejte text **DIST** .

### <a name="review-and-create"></a>Zkontrolovat a vytvořit

1. Kliknutím na tlačítko **Revize + vytvořit** ověřte správnost podrobností.
1. Kliknutím na **vytvořit** zahájíte vytváření prostředku a zároveň zajistěte akci GitHubu pro nasazení.
1. Po dokončení nasazení klikněte na **Přejít k prostředku** .
1. V okně _Přehled_ klikněte na odkaz *URL* a otevřete nasazenou aplikaci. 

Pokud si web nahraje hned hned, je pracovní postup akcí GitHubu na pozadí stále spuštěný. Po dokončení pracovního postupu můžete kliknutím na aktualizovat prohlížeč zobrazit webovou aplikaci.

Stav pracovních postupů akcí můžete zjistit tak, že přejdete k akcím pro vaše úložiště:

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nuxtjs-starter/actions
```

### <a name="sync-changes"></a>Synchronizovat změny

Při vytváření aplikace se v úložišti Azure static Web Apps vytvořil soubor pracovního postupu akcí GitHubu. Tento soubor budete muset přenést do místního úložiště, aby se synchronizoval historie Gitu.

Vraťte se do terminálu a spusťte následující příkaz `git pull origin main` .

## <a name="configure-dynamic-routes"></a>Konfigurace dynamických tras

Přejděte k nově nasazenému webu a klikněte na jedno z log rozhraní nebo loga knihovny. Místo toho, abyste získali stránku s podrobnostmi, dostanete chybovou stránku 404.

:::image type="content" source="media/deploy-nuxtjs/404-in-production.png" alt-text="404 na dynamických trasách":::

Důvodem je, Nuxt.js vygenerovali statickou lokalitu, pouze pro domovskou stránku. Nuxt.js může generovat ekvivalentní statické `.html` soubory pro každý `.vue` soubor stránky, ale dojde k výjimce. 

Pokud se jedná o dynamickou stránku, například `_id.vue` nebude mít dostatek informací, aby vygenerovala statický kód HTML z takové dynamické stránky. Budete muset explicitně zadat možné cesty pro dynamické trasy.

## <a name="generate-static-pages-from-dynamic-routes"></a>Generování statických stránek z dynamických tras

1. Aktualizujte soubor _nuxt.config.js_ tak, aby Nuxt.js používal seznam všech dostupných dat pro generování statických stránek pro každé rozhraní nebo knihovnu:

   ```javascript
     import { projects } from "./utils/projectsData";

     export default {
       mode: "universal",

       //...truncated

       generate: {
         async routes() {
           const paths = [];

           projects.forEach(project => {
             paths.push(`/project/${project.slug}`);
           });

           return paths;
         }
       }
     };
   ```

   > [!NOTE]
   > `routes` je asynchronní funkce, takže můžete vytvořit požadavek na rozhraní API v této funkci a použít vrácený seznam k vygenerování cest.

2. Nahrajte nové změny do úložiště GitHubu a počkejte pár minut, než akce GitHubu znovu vytvoří váš web. Po dokončení sestavení se zobrazí chyba 404.

   :::image type="content" source="media/deploy-nuxtjs/404-in-production-fixed.png" alt-text="404 na pevných dynamických trasách":::

> [!div class="nextstepaction"]
> [Nastavení vlastní domény](custom-domain.md)

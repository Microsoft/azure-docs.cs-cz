---
title: 'Kurz: publikování webu Gatsby do Azure static Web Apps'
description: V tomto kurzu se dozvíte, jak nasadit aplikaci Gatsby do služby Azure static Web Apps.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.custom: devx-track-js
ms.openlocfilehash: 4430ed34858077b13b4fec69756c1c7e9f3ef7ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652349"
---
# <a name="tutorial-publish-a-gatsby-site-to-azure-static-web-apps-preview"></a>Kurz: publikování webu Gatsby ve službě Azure static Web Apps Preview

Tento článek ukazuje, jak vytvořit a nasadit webovou aplikaci v [Gatsby](https://gatsbyjs.org) do služby [Azure static Web Apps](overview.md). Konečný výsledek je nový statický Web Apps Web (s přidruženými akcemi GitHubu), který vám umožní řídit, jak je aplikace sestavená a publikovaná.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> - Vytvořte aplikaci Gatsby
> - Nastavení webu Azure static Web Apps
> - Nasazení aplikace Gatsby do Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. Pokud ho nemáte, můžete si [účet zdarma vytvořit](https://azure.microsoft.com/free/).
- Účet GitHub. Pokud ho nemáte, můžete si [účet zdarma vytvořit](https://github.com/join).
- [Node.js](https://nodejs.org) nainstalován.

## <a name="create-a-gatsby-app"></a>Vytvoření aplikace v Gatsby

Vytvoření aplikace Gatsby pomocí rozhraní příkazového řádku Gatsby (CLI):

1. Otevření terminálu
1. Pomocí nástroje [npx](https://www.npmjs.com/package/npx) vytvořte novou aplikaci s rozhraním příkazového řádku Gatsby. Může to trvat několik minut.

   ```bash
   npx gatsby new static-web-app
   ```

1. Přejít na nově vytvořenou aplikaci

   ```bash
   cd static-web-app
   ```

1. Inicializovat úložiště Git

   ```bash
   git init
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Vložení aplikace do GitHubu

Abyste mohli vytvořit nový prostředek statického Web Apps Azure, musíte mít úložiště na GitHubu.

1. Vytvořte prázdné úložiště GitHub (nevytvářejte soubor READme) z [https://github.com/new](https://github.com/new) pojmenované **Gatsby-static-Web-App**.

1. Potom do svého místního úložiště přidejte úložiště GitHub, které jste právě vytvořili, jako vzdálené. Nezapomeňte přidat své uživatelské jméno GitHubu místo `<YOUR_USER_NAME>` zástupného znaku v následujícím příkazu.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/gatsby-static-web-app
   ```

1. Vložte své místní úložiště do GitHubu.

   ```bash
   git push --set-upstream origin main
   ```

## <a name="deploy-your-web-app"></a>Nasazení webové aplikace

Následující kroky ukazují, jak vytvořit novou aplikaci statické lokality a jak ji nasadit do produkčního prostředí.

### <a name="create-the-application"></a>Vytvoření aplikace

1. Přejít na [Azure Portal](https://portal.azure.com)
1. Klikněte na **Vytvořit prostředek**.
1. Vyhledejte **Static Web Apps**.
1. Klikněte na **Static Web Apps (Preview)**
1. Klikněte na **Vytvořit**.

   :::image type="content" source="./media/publish-gatsby/create-in-portal.png" alt-text="Vytvoření statického Web Apps (Preview) na portálu":::

1. U _předplatného_ přijměte uvedené předplatné nebo v rozevíracím seznamu vyberte nový.

1. V _skupiny prostředků_ vyberte **Nový**. Do _název nové skupiny prostředků_ zadejte **Gatsby-static-Web-App** a vyberte **OK**.

1. Dále do pole **název** zadejte název vaší aplikace. Mezi platné znaky `a-z` patří `A-Z` , `0-9` a `-` .

1. V _oblasti oblast_ vyberte dostupnou oblast, která je blízko vás.

1. V případě _SKU_ vyberte možnost **Free**.

   :::image type="content" source="./media/publish-gatsby/basic-app-details.png" alt-text="Podrobnosti vyplněné":::

1. Klikněte na tlačítko **Přihlásit se pomocí GitHubu** .

1. Vyberte **organizaci** , ve které jste úložiště vytvořili.

1. Jako _úložiště_ vyberte **Gatsby-static-Web-App** .

1. Pro _větev_ vyberte **Main (hlavní**).

   :::image type="content" source="./media/publish-gatsby/completed-github-info.png" alt-text="Informace o dokončeném GitHubu":::

### <a name="build"></a>Sestavení

Dále přidejte nastavení konfigurace, které proces sestavení používá k sestavení vaší aplikace.

1. Klikněte na tlačítko **Další: Sestavení >** a upravte konfiguraci sestavení.

1. Pokud chcete nakonfigurovat nastavení kroku v akcích GitHubu, nastavte _umístění aplikace_ na **/** .

1. Nastavte _umístění artefaktu aplikace_ na **veřejné**.

   Hodnota pro _umístění rozhraní API_ není nutná, protože v tuto chvíli nebudete NASAZOVAT rozhraní API.

   :::image type="content" source="./media/publish-gatsby/build-details.png" alt-text="Nastavení sestavení":::

### <a name="review-and-create"></a>Zkontrolovat a vytvořit

1. Kliknutím na tlačítko **Revize + vytvořit** ověřte správnost podrobností.

1. Kliknutím na **vytvořit** zahájíte vytváření App Servicech statických webových aplikací a zřídíte akci GitHubu pro nasazení.

1. Až se nasazení dokončí, **přejděte na prostředek**.

1. Na obrazovce prostředek kliknutím na odkaz _URL_ otevřete nasazenou aplikaci. Aby se akce GitHubu mohla dokončit, možná budete muset počkat minutu nebo dvě.

   :::image type="content" source="./media/publish-gatsby/deployed-app.png" alt-text="Nasazená aplikace":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání vlastní domény](custom-domain.md)

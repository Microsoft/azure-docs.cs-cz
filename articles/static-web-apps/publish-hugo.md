---
title: 'Kurz: publikování webu Hugo do Azure static Web Apps'
description: Naučte se, jak nasadit aplikaci Hugo do statického Web Apps Azure.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: ff408f114784fa3f0b8fab49521b5ec7ec2be102
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88797713"
---
# <a name="tutorial-publish-a-hugo-site-to-azure-static-web-apps-preview"></a>Kurz: publikování webu Hugo ve službě Azure static Web Apps Preview

Tento článek ukazuje, jak vytvořit a nasadit webovou aplikaci v [Hugo](https://gohugo.io/) do služby [Azure static Web Apps](overview.md). Konečný výsledek je nová statická webová aplikace Azure s přidruženými akcemi GitHubu, která vám umožní řídit, jak je aplikace sestavená a publikovaná.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> - Vytvoření aplikace v Hugo
> - Nastavení statického Web Apps Azure
> - Nasazení aplikace Hugo do Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. Pokud ho nemáte, můžete si [účet zdarma vytvořit](https://azure.microsoft.com/free/).
- Účet GitHub. Pokud ho nemáte, můžete si [účet zdarma vytvořit](https://github.com/join).

## <a name="create-a-hugo-app"></a>Vytvoření aplikace v Hugo

Vytvoření aplikace Hugo pomocí rozhraní příkazového řádku Hugo (CLI):

1. Postupujte podle pokynů [Průvodce instalací](https://gohugo.io/getting-started/installing/) pro Hugo v operačním systému.

1. Otevření terminálu

1. Pokud chcete vytvořit novou aplikaci, spusťte rozhraní příkazového řádku Hugo.

   ```bash
   hugo new site static-app
   ```

1. Přejděte na nově vytvořenou aplikaci.

   ```bash
   cd static-app
   ```

1. Inicializujte úložiště Git.

   ```bash
    git init
   ```

1. Dále přidejte motiv do webu tak, že nainstalujete motiv jako dílčí modul Git a pak ho zadáte do konfiguračního souboru Hugo.

   ```bash
   git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
   echo 'theme = "ananke"' >> config.toml
   ```

1. Potvrďte změny.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Vložení aplikace do GitHubu

K připojení ke statickým Web Appsm Azure budete potřebovat úložiště na GitHubu. Následující kroky ukazují, jak vytvořit úložiště pro svůj web.

1. Vytvořte prázdné úložiště GitHub (nevytvářejte soubor READme) z [https://github.com/new](https://github.com/new) pojmenované **Hugo-static-App**.

1. Přidejte úložiště GitHub jako vzdálené úložiště do místního úložiště. Nezapomeňte přidat své uživatelské jméno GitHubu místo `<YOUR_USER_NAME>` zástupného znaku v následujícím příkazu.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/hugo-static-app
   ```

1. Vložení místního úložiště do GitHubu

   ```bash
   git push --set-upstream origin master
   ```

## <a name="deploy-your-web-app"></a>Nasazení webové aplikace

Následující kroky ukazují, jak vytvořit novou aplikaci statické lokality a jak ji nasadit do produkčního prostředí.

### <a name="create-the-application"></a>Vytvoření aplikace

1. Přejít na [Azure Portal](https://portal.azure.com)
1. Klikněte na **Vytvořit prostředek**.
1. Vyhledejte **Static Web Apps**.
1. Klikněte na **Static Web Apps (Preview)**
1. Klikněte na **Vytvořit**.

   :::image type="content" source="./media/publish-hugo/create-in-portal.png" alt-text="Vytvoření prostředku statického Web Apps Azure na portálu":::

1. U **předplatného**přijměte uvedené předplatné nebo v rozevíracím seznamu vyberte nový.

1. V _skupiny prostředků_vyberte **Nový**. Do _název nové skupiny prostředků_zadejte **Hugo-static-App** a vyberte **OK**.

1. Dále do pole **název** zadejte název vaší aplikace. Mezi platné znaky `a-z` patří `A-Z` , `0-9` a `-` .

1. V _oblasti oblast_vyberte dostupnou oblast, která je blízko vás.

1. V případě _SKU_vyberte možnost **Free**.

   :::image type="content" source="./media/publish-hugo/basic-app-details.png" alt-text="Vytvoření prostředku statického Web Apps Azure na portálu":::

1. Klikněte na tlačítko **Přihlásit se pomocí GitHubu** .

1. Vyberte **organizaci** , ve které jste úložiště vytvořili.

1. Jako _úložiště_ vyberte **Hugo-static-App** .

1. Pro _větev_ vyberte **hlavní**uzel.

   :::image type="content" source="./media/publish-hugo/completed-github-info.png" alt-text="Vytvoření prostředku statického Web Apps Azure na portálu":::

### <a name="build"></a>Sestavení

Dále přidáte nastavení konfigurace, které proces sestavení používá k sestavení vaší aplikace. Následující nastavení nakonfigurují soubor pracovního postupu akce GitHubu.

1. Klikněte na tlačítko **Další: Build >** (Další: Sestavení) a upravte konfiguraci sestavení.

1. Nastavte _umístění aplikace_ na **/** .

1. Nastavte _umístění artefaktu aplikace_ na **veřejné**.

   Hodnota pro _umístění rozhraní API_ není nutná, protože v tuto chvíli nebudete NASAZOVAT rozhraní API.

### <a name="review-and-create"></a>Podokno Zkontrolovat a vytvořit

1. Kliknutím na tlačítko **Revize + vytvořit** ověřte správnost podrobností.

1. Kliknutím na **vytvořit** zahájíte vytváření statických Web Apps Azure a zřídíte akci GitHubu pro nasazení.

1. Počkejte na dokončení akce GitHubu.

1. V okně _přehledu_ Azure Portal nově vytvořeného prostředku služby Azure static Web Apps kliknutím na odkaz _URL_ otevřete nasazenou aplikaci.

   :::image type="content" source="./media/publish-hugo/deployed-app.png" alt-text="Vytvoření prostředku statického Web Apps Azure na portálu":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání vlastní domény](custom-domain.md)

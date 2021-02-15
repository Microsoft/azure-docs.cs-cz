---
title: Jak nasadit nástroj pro označování ukázek pro rozpoznávání formulářů
titleSuffix: Azure Cognitive Services
description: Seznamte se s různými způsoby, jak můžete nasadit nástroj pro označování ukázek pro rozpoznávání formulářů, který vám umožní pomáhat s dohledem.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 02/11/2021
ms.author: lajanuar
ms.openlocfilehash: 9535c1aa044fdce529d83c2e46a1b585e8e5f056
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370010"
---
# <a name="deploy-the-sample-labeling-tool"></a>Nasazení ukázkového nástroje pro popisování

Nástroj pro označování ukázkových popisků ve formulářích je aplikace, která poskytuje jednoduché uživatelské rozhraní (UI), které můžete použít k ručnímu označení formulářů (dokumentů) pro účely dohledu pod dohledem. V tomto článku poskytneme odkazy a pokyny, které vám pomůžou:

* [Spustit nástroj pro označování ukázek místně](#run-the-sample-labeling-tool-locally)
* [Nasazení ukázkového nástroje pro označování popisků do instance kontejneru Azure (ACI)](#deploy-with-azure-container-instances-aci)
* [Použití a přispívání na open source nástroj pro označování formátu OCR](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>Spustit nástroj pro označování ukázek místně

Nejrychlejší způsob, jak začít s označováním dat, je spustit nástroj pro označování ukázek v místním prostředí. V následujícím rychlém startu se používá REST API pro rozpoznávání formulářů a vzorový Nástroj pro popisování vlastního modelu s ručně označenými daty. 

* [Rychlý Start: značení formulářů, výuka modelu a analýza formuláře s použitím ukázkového nástroje pro označování](./quickstarts/label-tool.md).

## <a name="deploy-with-azure-container-instances-aci"></a>Nasazení pomocí Azure Container Instances (ACI)

Než začneme, je důležité si uvědomit, že existují dva způsoby, jak nasadit vzorový Nástroj pro označování do instance kontejneru Azure (ACI). Obě možnosti slouží ke spuštění ukázkového nástroje pro označování pomocí ACI:

* [Použití webu Azure Portal](#azure-portal)
* [Použití Azure CLI](#azure-cli)

### <a name="azure-portal"></a>portál Azure

Pomocí následujících kroků vytvořte nový prostředek pomocí Azure Portal: 

1. Přihlaste se na [Azure Portal](https://portal.azure.com/signin/index/).
2. Vyberte **Vytvořit prostředek**.
3. Pak vyberte **Webová aplikace**.

   > [!div class="mx-imgBorder"]
   > ![Výběr webové aplikace](./media/quickstarts/create-web-app.png)

4. Nejdřív se ujistěte, že je vybraná karta **základy** . Teď budete muset zadat nějaké informace:

   > [!div class="mx-imgBorder"]
   > ![Výběr základních](./media/quickstarts/select-basics.png)
   * Předplatné – výběr existujícího předplatného Azure
   * Skupina prostředků – můžete znovu použít existující skupinu prostředků nebo vytvořit novou pro tento projekt. Doporučuje se vytvořit novou skupinu prostředků.
   * Název – zadejte název vaší webové aplikace. 
   * Publikování – výběr **kontejneru Docker**
   * Operační systém – výběr systému **Linux**
   * Oblast – vyberte oblast, která vám dává smysl.
   * Plán pro Linux – vyberte cenovou úroveň/plán pro službu App Service. 

   > [!div class="mx-imgBorder"]
   > ![Konfigurace webové aplikace](./media/quickstarts/select-docker.png)

5. V dalším kroku vyberte kartu **Docker** .

   > [!div class="mx-imgBorder"]
   > ![Vybrat Docker](./media/quickstarts/select-docker.png)

6. Teď nakonfigurujeme kontejner Docker. Všechna pole jsou povinná, pokud není uvedeno jinak:

    # <a name="v20"></a>[v2.0](#tab/v2-0)

* Možnosti – výběr **jednoho kontejneru**
* Zdroj image – výběr **privátního registru** 
* Adresa URL serveru – nastavte tuto hodnotu na `https://mcr.microsoft.com`
* Username (volitelné) – vytvořte uživatelské jméno. 
* Heslo (volitelné) – vytvořte zabezpečené heslo, které si zapamatujete.
* Image a tag – nastavte tuto hodnotu na `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest`
* Průběžné nasazování – toto nastavte na **zapnuto** , pokud chcete dostávat automatické aktualizace, když vývojový tým provede změny v nástroji Sample labeling.
* Spouštěcí příkaz – nastavte tuto hodnotu na `./run.sh eula=accept`

    # <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1) 

* Možnosti – výběr **jednoho kontejneru**
* Zdroj image – výběr **privátního registru** 
* Adresa URL serveru – nastavte tuto hodnotu na `https://mcr.microsoft.com`
* Username (volitelné) – vytvořte uživatelské jméno. 
* Heslo (volitelné) – vytvořte zabezpečené heslo, které si zapamatujete.
* Image a tag – nastavte tuto hodnotu na `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview`
* Průběžné nasazování – toto nastavte na **zapnuto** , pokud chcete dostávat automatické aktualizace, když vývojový tým provede změny v nástroji Sample labeling.
* Spouštěcí příkaz – nastavte tuto hodnotu na `./run.sh eula=accept`

    ---

   > [!div class="mx-imgBorder"]
   > ![Konfigurovat Docker](./media/quickstarts/configure-docker.png)

7. Hotovo. V dalším kroku vyberte **zkontrolovat + vytvořit** a pak **vytvořit** a nasaďte webovou aplikaci. Po dokončení budete mít přístup k webové aplikaci na adrese URL uvedené v **přehledu** prostředku.

> [!NOTE]
> Při vytváření webové aplikace můžete taky nakonfigurovat autorizaci a ověřování. Není to nutné pro začátek.

> [!IMPORTANT]
> Pro vaši webovou aplikaci možná budete muset povolit TLS, aby se mohla zobrazit na `https` adrese. Postupujte podle pokynů v části [Povolení koncového bodu TLS](../../container-instances/container-instances-container-group-ssl.md) pro nastavení kontejneru Webhooku, než pro vaši webovou aplikaci povolíte protokol TLS/SSL.

### <a name="azure-cli"></a>Azure CLI

Jako alternativu k používání Azure Portal můžete vytvořit prostředek pomocí Azure CLI. Než budete pokračovat, bude nutné nainstalovat rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli). Pokud už s Azure CLI pracujete, můžete tento krok přeskočit. 

K tomuto příkazu potřebujete znát několik věcí:

* `DNS_NAME_LABEL=aci-demo-$RANDOM` vygeneruje náhodný název DNS. 
* V této ukázce se předpokládá, že máte skupinu prostředků, kterou můžete použít k vytvoření prostředku. Nahraďte `<resource_group_name>` platnou skupinou prostředků, která je přidružená k vašemu předplatnému. 
* Budete muset určit, kde chcete prostředek vytvořit. Nahraďte `<region name>` požadovanou oblastí pro webovou aplikaci. 
* Tento příkaz automaticky přijme smlouvu EULA.

V Azure CLI spuštěním tohoto příkazu vytvořte prostředek webové aplikace pro nástroj pro označování ukázek:

# <a name="v20"></a>[v2.0](#tab/v2-0)

```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"
`

# [v2.1 preview](#tab/v2-1) 
   
```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"
```

---

### <a name="connect-to-azure-ad-for-authorization"></a>Připojení k Azure AD pro autorizaci

Doporučuje se připojit svou webovou aplikaci k Azure Active Directory. Tím se zajistí, že se můžou přihlásit a používat vaše webová aplikace jenom uživatelé s platnými přihlašovacími údaji. Postupujte podle pokynů v tématu [Konfigurace aplikace App Service](../../app-service/configure-authentication-provider-aad.md) pro připojení k Azure Active Directory.

## <a name="open-source-on-github"></a>Open source na GitHubu

Nástroj pro označování ve formě rozpoznávání znaků je také k dispozici jako open source projekt na GitHubu. Nástroj je webová aplikace sestavená pomocí reakce + Redux a je napsaná v TypeScript. Další informace nebo přispívání naleznete v tématu [Nástroj pro označování ve formě rozpoznávání OCR](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

## <a name="next-steps"></a>Další kroky

Pomocí průvodce [výukou s popisky se](./quickstarts/label-tool.md) naučíte používat nástroj k ručnímu označení školicích dat a provádění vzdělávání pod dohledem.
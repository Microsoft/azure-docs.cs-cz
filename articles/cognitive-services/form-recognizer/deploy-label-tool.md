---
title: Jak nasadit nástroj pro rozpoznávání vzorků formuláře
titleSuffix: Azure Cognitive Services
description: Seznamte se s různými způsoby nasazení nástroje pro rozpoznávání vzorků formuláře, který vám pomůže s učením pod dohledem.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 7ddb4b2cd465b5e9542d777d33b9bd8cb952becd
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531333"
---
# <a name="deploy-the-sample-labeling-tool"></a>Nasazení ukázkového nástroje pro popisování

Nástroj pro rozpoznávání vzorků formuláře je aplikace, která poskytuje jednoduché uživatelské rozhraní (UI), které můžete použít k ručnímu označení formulářů (dokumentů) pro účely učení pod dohledem. V tomto článku vám poskytneme odkazy a pokyny, které vás naučí:

* [Spuštění ukázkového štítkovacího nástroje místně](#run-the-sample-labeling-tool-locally)
* [Nasazení ukázkového štítku do instance kontejneru Azure (ACI)](#deploy-with-azure-container-instances-aci)
* [Použití a přispívání k nástroji pro označování formulářů OCR s otevřeným zdrojovým kódem](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>Spuštění ukázkového štítkovacího nástroje místně

Nejrychlejší způsob, jak začít popisovat data, je spustit ukázkový nástroj pro označování místně. Následující rychlý start používá rozhraní REST API nástroje pro rozpoznávání formulářů a ukázkový nástroj pro modelování popisů k trénování vlastního modelu s ručně označenými daty. 

* [Úvodní příručka: Popisky formulářů, trénování modelu a analýza formuláře pomocí nástroje pro označování vzorků](./quickstarts/label-tool.md).

## <a name="deploy-with-azure-container-instances-aci"></a>Nasazení s instancemi kontejnerů Azure (ACI)

Než začneme, je důležité si uvědomit, že existují dva způsoby, jak nasadit nástroj pro označování ukázkových vzorků do instance kontejneru Azure (ACI). Obě možnosti se používají ke spuštění ukázkového štítkovacího nástroje s ACI: 

* [Použití webu Azure Portal](#azure-portal)
* [Pomocí Azure CLI](#azure-cli)

### <a name="azure-portal"></a>portál Azure

Podle těchto kroků vytvořte nový prostředek pomocí webu Azure Portal: 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/signin/index/).
2. Vyberte **Vytvořit prostředek**. 
3. Dále vyberte **Web App**. 

   > [!div class="mx-imgBorder"]
   > ![Výběr webové aplikace](./media/quickstarts/formre-create-web-app.png)
   
4. Nejprve zkontrolujte, zda je vybrána karta **Základy.** Nyní budete muset poskytnout nějaké informace: 

   > [!div class="mx-imgBorder"]
   > ![Vybrat základy](./media/quickstarts/formre-select-basics.png)
   * Předplatné – výběr existujícího předplatného Azure
   * Skupina zdrojů – můžete znovu použít existující skupinu zdrojů nebo vytvořit novou skupinu pro tento projekt. Doporučujeme vytvořit novou skupinu prostředků.
   * Jméno – Pojmenujte webovou aplikaci. 
   * Publikovat – vybrat **kontejner Dockeru**
   * Operační systém - Vyberte **Linux**
   * Region – Vyberte oblast, která vám dává smysl.
   * Plán Linuxu – vyberte cenovou úroveň/plán pro vaši aplikační službu. 

   > [!div class="mx-imgBorder"]
   > ![Konfigurace webové aplikace](./media/quickstarts/formre-select-docker-linux.png)

5. Dále vyberte kartu **Docker.** 

   > [!div class="mx-imgBorder"]
   > ![Vybrat Docker](./media/quickstarts/formre-select-docker.png)

6. Nyní nakonfigurujeme váš kontejner Dockeru. Není-li uvedeno jinak, jsou vyžadována všechna pole:

   * Možnosti – výběr **jednoho kontejneru**
   * Zdroj obrázku – výběr **soukromého registru** 
   * Adresa URL serveru – nastavte tuto`https://mcr.microsoft.com`
   * Uživatelské jméno (Volitelné) - Vytvořte uživatelské jméno. 
   * Heslo (volitelné) – Vytvořte si bezpečné heslo, které si zapamatujete.
   * Obrázek a značka - Nastavte tuto`mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest`
   * Průběžné nasazení – nastavte tuto možnost **na zapnuto,** pokud chcete dostávat automatické aktualizace, když vývojový tým provede změny v nástroji pro popisování vzorků.
   * Příkaz Po spuštění – nastavte tuto položku na`./run.sh eula=accept`

   > [!div class="mx-imgBorder"]
   > ![Konfigurace Dockeru](./media/quickstarts/formre-configure-docker.png)

7. A to je vše. Dále vyberte **Zkontrolovat + Vytvořit**a pak **Vytvořit** a nasadit webovou aplikaci. Po dokončení můžete přistupovat k webové aplikaci na adrese URL uvedené v **přehledu** pro váš prostředek.

> [!NOTE]
> Při vytváření webové aplikace můžete také nakonfigurovat autorizaci/ověřování. To není nutné, abyste mohli začít. 

### <a name="azure-cli"></a>Azure CLI

Jako alternativu k používání portálu Azure můžete vytvořit prostředek pomocí azure cli. Než budete pokračovat, budete muset nainstalovat [azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Tento krok můžete přeskočit, pokud už pracujete s azure CLI. 

O tomto příkazu je třeba vědět několik věcí:

* `DNS_NAME_LABEL=aci-demo-$RANDOM`vygeneruje náhodný název DNS. 
* Tato ukázka předpokládá, že máte skupinu prostředků, kterou můžete použít k vytvoření prostředku. Nahraďte `<resource_group_name>` platnou skupinou prostředků přidruženou k vašemu předplatnému. 
* Budete muset určit, kde chcete zdroj vytvořit. Nahraďte `<region name>` požadovanou oblast pro webovou aplikaci. 
* Tento příkaz automaticky přijme eula.

Z příkazového příkazu Konto Azure spusťte tento příkaz a vytvořte prostředek webové aplikace pro ukázkový nástroj pro popisování: 

```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resorunce_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8
  --command-line "./run.sh eula=accept"
```

### <a name="connect-to-azure-ad-for-authorization"></a>Připojení k Azure AD pro autorizaci

Doporučujeme připojit webovou aplikaci k Azure Active Directory. Tím zajistíte, že se k vaší webové aplikaci budou moci přihlašovat a používat pouze uživatelé s platnými přihlašovacími údaji. Podle pokynů [postupujte](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) podle pokynů v části Konfigurace aplikace App Service pro připojení k Azure Active Directory.

## <a name="open-source-on-github"></a>Open source na GitHubu

Nástroj pro popisování formulářů OCR je také k dispozici jako open source projekt na GitHubu. Nástroj je webová aplikace postavena pomocí React + Redux, a je napsán v TypeScriptu. Další informace nebo příspěvky naleznete [v tématu Nástroj pro označování formulářů OCR](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

## <a name="next-steps"></a>Další kroky

Pomocí [rychlého](./quickstarts/label-tool.md) startu Train s popisky se dozvíte, jak pomocí nástroje ručně označovat trénovací data a provádět učení pod dohledem.

---
title: Kurz – import a správa rozhraní API – Azure API Management a Visual Studio Code | Microsoft Docs
description: V tomto kurzu se naučíte používat rozšíření Azure API Management pro Visual Studio Code k importu, testování a správě rozhraní API.
ms.service: api-management
author: dlepow
ms.author: apimpm
ms.topic: tutorial
ms.date: 12/10/2020
ms.openlocfilehash: 6cf5c6f716912689b39264ed71f6a7c55f944ad2
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97439491"
---
# <a name="tutorial-use-the-api-management-extension-for-visual-studio-code-to-import-and-manage-apis"></a>Kurz: použití rozšíření API Management pro Visual Studio Code k importu a správě rozhraní API

V tomto kurzu se naučíte používat rozšíření API Management Extension Preview pro Visual Studio Code pro běžné operace v API Management. Pomocí známého Visual Studio Code prostředí můžete importovat, aktualizovat, testovat a spravovat rozhraní API.

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Import rozhraní API do API Management
> * Úprava rozhraní API
> * Použití zásad API Management
> * Testování rozhraní API


:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-result.png" alt-text="Rozhraní API v rozšíření API Management":::

Úvod k dalším funkcím API Management najdete v kurzech API Management pomocí [Azure Portal](import-and-publish.md).

## <a name="prerequisites"></a>Předpoklady
- Vysvětlení [terminologie služby Azure API Management](api-management-terminology.md)
- Ujistěte se, že máte nainstalované [Visual Studio Code](https://code.visualstudio.com/) a nejnovější [rozšíření Azure API Management pro Visual Studio Code (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview) .
- [Vytvoření instance API Management](vscode-create-service-instance.md)

## <a name="import-an-api"></a>Import rozhraní API

Následující příklad importuje specifikaci OpenAPI ve formátu JSON do API Management. Microsoft poskytuje rozhraní back-end používané v tomto příkladu a hostuje ho v Azure na adrese `https://conferenceapi.azurewebsites.net?format=json` .

1. V Visual Studio Code na řádku aktivity vyberte ikonu Azure.
1. V podokně Průzkumník rozbalte instanci API Management, kterou jste vytvořili.
1. Klikněte pravým tlačítkem na **rozhraní API** a vyberte **odkaz importovat z openapi**. 
1. Po zobrazení výzvy zadejte následující hodnoty:
    1. **Odkaz openapi** pro obsah ve formátu JSON. V tomto příkladu: *https://conferenceapi.azurewebsites.net?format=json* .
    Tato adresa URL je služba, která implementuje ukázkové rozhraní API. API Management přepošle požadavky na tuto adresu.
    1. **Název rozhraní API**, jako je například *demo-konferenční rozhraní API*, které je v instanci API Management jedinečné. Tento název může obsahovat jenom písmena, číslice a spojovníky. První a poslední znak musí být alfanumerický. Tento název se používá v cestě pro volání rozhraní API.

Po úspěšném importu rozhraní API se zobrazí v podokně Průzkumník a v uzlu **operace** se zobrazí dostupné operace rozhraní API.

:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-operations.png" alt-text="Importované rozhraní API v podokně Průzkumníka":::

## <a name="edit-the-api"></a>Úprava rozhraní API

Rozhraní API můžete upravit v Visual Studio Code. Pokud například chcete odebrat protokol **http** , který se používá pro přístup k rozhraní API, upravte správce prostředků popisu JSON rozhraní API v okně editoru. Pak vyberte **soubor**  >  **Uložit**.

:::image type="content" source="media/visual-studio-code-tutorial/import-demo-api.png" alt-text="Upravit popis JSON":::

Pokud chcete upravit formát OpenAPI, klikněte pravým tlačítkem myši na název rozhraní API v podokně Průzkumník a vyberte **Upravit openapi**. Proveďte požadované změny a pak vyberte **soubor**  >  **Uložit**.

## <a name="apply-policies-to-the-api"></a>Použití zásad pro rozhraní API 

API Management poskytuje [zásady](api-management-policies.md) , které můžete nakonfigurovat pro vaše rozhraní API. Zásady představují kolekci příkazů, které se postupně provádí na základě požadavku nebo odezvy z rozhraní API. Zásady můžou být globální, které se vztahují na všechna rozhraní API ve vaší instanci API Management, nebo můžou být vymezené na konkrétní rozhraní API nebo operaci rozhraní API.

V této části se dozvíte, jak na rozhraní API použít některé běžné odchozí zásady, které transformují odpověď rozhraní API. Zásady v tomto příkladu mění hlavičky odpovědí a skryjí původní adresy URL back-end, které se zobrazí v těle odpovědi.

1. V podokně Průzkumník vyberte v části *ukázková konference – rozhraní API* , které jste naimportovali, **zásadu** . V okně editoru se otevře soubor zásad. Tento soubor nakonfiguruje zásady pro všechny operace v rozhraní API. 

1. Aktualizujte soubor s následujícím obsahem v `<outbound>` elementu:
    ```html
    [...]
    <outbound>
        <set-header name="Custom" exists-action="override">
            <value>"My custom value"</value>
        </set-header>
        <set-header name="X-Powered-By" exists-action="delete" />
        <redirect-content-urls />
        <base />
    </outbound>
    [...]
    ```

    * První `set-header` zásada přidá vlastní hlavičku odpovědi pro demonstrační účely.
    * Druhá `set-header` zásada odstraní hlavičku **X-The-by** , pokud existuje. Tato hlavička může odhalit aplikační rozhraní používané v back-endu rozhraní API a vydavatelé ji často odstraní.
    * `redirect-content-urls`Zásady přepisují odkazy (masky) v těle odpovědi tak, aby odkazovaly na ekvivalentní odkazy prostřednictvím API Management brány.
    
1. Uložte soubor. Pokud se zobrazí výzva, vyberte **nahrát** a nahrajte soubor do cloudu.

## <a name="test-the-api"></a>Testování rozhraní API

### <a name="get-the-subscription-key"></a>Získat klíč předplatného

K otestování importovaného rozhraní API, které jste naimportovali, a zásad, které jsou aplikovány, potřebujete klíč předplatného pro vaši instanci API Management.

1. V podokně Průzkumník klikněte pravým tlačítkem myši na název vaší instance API Management.
1. Vyberte **Kopírovat klíč předplatného**.

    :::image type="content" source="media/visual-studio-code-tutorial/copy-subscription-key.png" alt-text="Kopírovat klíč předplatného":::

### <a name="test-an-api-operation"></a>Testování operace rozhraní API

1. V podokně Průzkumník rozbalte uzel **Operations** v části *demo-konferenční rozhraní API* , které jste naimportovali.
1. Vyberte operaci, například *Getspeakers*.
1. V okně editoru vedle klíče **OCP-APIM-Subscription-Key** nahraďte `{{SubscriptionKey}}` klíč předplatného, který jste zkopírovali.
1. Vyberte **Odeslat žádost**. 

:::image type="content" source="media/visual-studio-code-tutorial/test-api.png" alt-text="Odeslat požadavek rozhraní API z Visual Studio Code":::

Po úspěšném dokončení požadavku back-end odpoví s **200 OK** a dalšími daty.

:::image type="content" source="media/visual-studio-code-tutorial/test-api-policies.png" alt-text="Operace testu rozhraní API":::

V odpovědi si všimněte následujících podrobností:
* **Vlastní** hlavička je přidána do odpovědi.
* Hlavička **X-The-by** se v odpovědi nezobrazuje.
* Adresy URL back-endu rozhraní API jsou v tomto případě přesměrovány na bránu API Management `https://apim-hello-world.azure-api.net/demo-conference-api` .

### <a name="trace-the-api-operation"></a>Trasování operace rozhraní API

Podrobné informace o trasování, které vám pomůžou ladit operaci rozhraní API, získáte výběrem odkazu, který se zobrazí vedle **OCP-APIM-Trace-Location**. 

Soubor JSON v tomto umístění obsahuje informace o příchozím, back-endu a odchozím trasování, abyste mohli určit, kde k problémům dochází po provedení žádosti.

> [!TIP]
> Když testujete operace rozhraní API, rozšíření API Management umožňuje volitelné [ladění zásad](api-management-debug-policies.md) (k dispozici v úrovni služby pro vývojáře).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odeberte instanci API Management tak, že kliknete pravým tlačítkem a vyberete **otevřít na portálu** a [odstraníte API Management službu](get-started-create-service-instance.md#clean-up-resources) a její skupinu prostředků.

Alternativně můžete vybrat možnost **odstranit API Management** a odstranit pouze instanci API Management (Tato operace neodstraní skupinu prostředků).

:::image type="content" source="media/visual-studio-code-tutorial/vscode-apim-delete.png" alt-text="Odstranit instanci API Management z VS Code":::

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zavedli několik funkcí rozšíření API Management pro Visual Studio Code, které můžete použít k importu a správě rozhraní API. Naučili jste se:

> [!div class="checklist"]
> * Import rozhraní API do API Management
> * Úprava rozhraní API
> * Použití zásad API Management
> * Testování rozhraní API

Rozšíření API Management poskytuje další funkce pro práci s rozhraními API. Například [zásady ladění](api-management-debug-policies.md) (k dispozici v úrovni Vývojářské služby) nebo vytváření a Správa [pojmenovaných hodnot](api-management-howto-properties.md).
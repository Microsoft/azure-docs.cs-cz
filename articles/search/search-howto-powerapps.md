---
title: 'Kurz: dotazování z Power Apps'
titleSuffix: Azure Cognitive Search
description: Podrobné pokyny k vytvoření Power aplikace, která se připojuje k indexu služby Azure Kognitivní hledání, odesílá dotazy a vykresluje výsledky.
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: tutorial
ms.date: 11/17/2020
ms.openlocfilehash: e8c16f02cf6b77fa54d2a19abac48e9914aa99bd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008561"
---
# <a name="tutorial-query-a-cognitive-search-index-from-power-apps"></a>Kurz: dotazování indexu Kognitivní hledání z Power Apps

Využijte rychlé vývojové prostředí aplikací Power Apps k vytvoření vlastní aplikace pro prohledávatelný obsah v Azure Kognitivní hledání.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připojení k Azure Kognitivní hledání
> * Nastavení žádosti o dotaz
> * Vizualizace výsledků v aplikaci plátna

Pokud ještě nemáte předplatné Azure, otevřete si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* [Účet Power Apps](https://make.powerapps.com)

* [Hotely – ukázkový index](search-get-started-portal.md) hostovaný na vaší vyhledávací službě

* [Klíč rozhraní API pro dotazování](search-security-api-keys.md#find-existing-keys)

## <a name="1---create-a-custom-connector"></a>1. vytvoření vlastního konektoru

Konektor v Power Apps je připojení ke zdroji dat. V tomto kroku vytvoříte vlastní konektor pro připojení k indexu vyhledávání v cloudu.

1. [Přihlaste](https://make.powerapps.com) se k Power Apps.

1. Na levé straně rozbalte položku **Data**  >  **vlastní konektory** dat.
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="Nabídka vlastních konektorů" border="true":::

1. Vyberte  **+ Nový vlastní konektor** a pak vyberte **vytvořit z prázdné**.

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="Vytvořit z prázdné nabídky" border="true":::

1. Zadejte název vlastního konektoru (například *AzureSearchQuery*) a potom klikněte na **pokračovat**.

1. Zadejte informace na stránce Obecné:

   * Barva pozadí ikony (např. #007ee5)
   * Popis (např. "konektor do Azure Kognitivní hledání")
   * V hostiteli budete muset zadat adresu URL vyhledávací služby (například `<yourservicename>.search.windows.net` ).
   * Pro základní adresu URL stačí zadat "/".

    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="Obecný dialog informací" border="true":::

1. Na stránce zabezpečení nastavte *klíč rozhraní API* jako **typ ověřování**, pro *klíč rozhraní API* nastavte jak popisek parametru, tak název parametru. V části **umístění parametru** vyberte *záhlaví* , jak je znázorněno níže.

    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="Možnost typu ověřování" border="true":::

1. Na stránce definice vyberte **+ Nová akce** a vytvořte akci, která bude dotazovat index. Zadejte hodnotu "dotaz" pro souhrn a název ID operace. Zadejte popis jako *"dotazování indexu hledání"*.

    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="Možnosti nové akce" border="true":::

1. Posuňte se dolů. V části požadavky kliknutím na **+ importovat z ukázkového** tlačítka nakonfigurujte požadavek na dotaz na vyhledávací službu:

   * Vyberte operaci. `GET`

   * Pro adresu URL zadejte ukázkový dotaz pro index vyhledávání ( `search=*` vrátí všechny dokumenty, `$select=` umožňuje zvolit pole). Verze rozhraní API je povinná. V plném rozsahu adresa URL může vypadat takto: `https://mydemo.search.windows.net/indexes/hotels-sample-index/docs?search=*&$select=HotelName,Description,Address/City&api-version=2020-06-30`

   * Pro záhlaví zadejte `Content-Type` . Hodnotu nastavíte `application/json` v pozdějším kroku.

     **Power Apps** používá syntaxi v adrese URL k extrakci parametrů z dotazu: parametry hledání, výběr a rozhraní API-Version se v průběhu průvodce stanou konfigurovatelnými.

       :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="Importování z ukázky" border="true":::

1. Kliknutím na **importovat** můžete žádost automaticky vyplnit. Nastavení metadat parametru dokončíte kliknutím na symbol **...** vedle každého parametru. Po aktualizaci každého parametru se kliknutím na tlačítko **zpět** vraťte na stránku žádosti.

   :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="Import z ukázkového dialogu" border="true":::

1. For *Search*: nastavte `*` jako **výchozí hodnotu**, nastavte **Required** jako *false* a nastavte **viditelnost** na *žádná*. 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="Vyhledat metadata parametrů" border="true":::

1. U pole *Vybrat*: nastavit `HotelName,Description,Address/City` jako **výchozí hodnotu** nastavte hodnotu **požadováno** na *false* a nastavte vlastnost **visibility** na *none*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-4-parameter-metadata-select.png" alt-text="Vybrat metadata parametrů" border="true":::

1. Pro *rozhraní API-Version*: `2020-06-30` nastavte jako **výchozí hodnotu**, nastavte **required** na *true* a nastavte **visibility** jako *interní*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="Metadata parametrů verze" border="true":::

1. Pro *typ obsahu*: nastavte na `application/json` .

1. Po provedení těchto změn přepněte do zobrazení **Editor Swagger** . V části Parameters by se měla zobrazit následující konfigurace:

    ```JSON
    parameters:
      - {name: search, in: query, required: false, type: string, default: '*'}
      - {name: $select, in: query, required: false, type: string, default: 'HotelName,Description,Address/City'}
      - {name: api-version, in: query, required: true, type: string, default: '2020-06-30',
        x-ms-visibility: internal}
      - {name: Content-Type, in: header, required: false, type: string}
    ```

1. Přepněte zpět do průvodce a vraťte se na **3. Požadavek** na krok Přejděte dolů do části Response (odpověď). Klikněte na **Přidat výchozí odpověď**. To je důležité, protože pomůže Power Apps pochopit schéma odpovědi. 

1. Vložte ukázkovou odpověď. Snadný způsob, jak zachytit ukázkovou odpověď, je prostřednictvím Průzkumníka služby Search v Azure Portal. V Průzkumníku služby Search byste měli zadat stejný dotaz jako u žádosti, ale přidáním **$Top = 2** omezit výsledky pouze na dva dokumenty:: `search=*&$select=HotelName,Description,Address/City&$top=2` . 

   Power Apps potřebuje k detekci schématu jenom pár výsledků. Do Průvodce teď můžete zkopírovat následující odpověď za předpokladu, že používáte hotely-Sample-index.

    ```JSON
    {
        "@odata.context": "https://mydemo.search.windows.net/indexes('hotels-sample-index')/$metadata#docs(*)",
        "value": [
            {
                "@search.score": 1,
                "HotelName": "Arcadia Resort & Restaurant",
                "Description": "The largest year-round resort in the area offering more of everything for your vacation – at the best value!  What can you enjoy while at the resort, aside from the mile-long sandy beaches of the lake? Check out our activities sure to excite both young and young-at-heart guests. We have it all, including being named “Property of the Year” and a “Top Ten Resort” by top publications.",
                "Address": {
                    "City": "Seattle"
                }
            },
            {
                "@search.score": 1,
                "HotelName": "Travel Resort",
                "Description": "The Best Gaming Resort in the area.  With elegant rooms & suites, pool, cabanas, spa, brewery & world-class gaming.  This is the best place to play, stay & dine.",
                "Address": {
                    "City": "Albuquerque"
                }
            }
        ]
    }
    ```

    > [!TIP] 
    > Pro odpověď JSON je omezení znaků, které můžete zadat, takže před vložením je vhodné tento formát JSON zjednodušit. Schéma a formát odpovědi jsou důležitější než hodnoty samotné. Například pole Popis může být zjednodušené jenom na první větu.

1. Kliknutím na **importovat** přidejte výchozí odpověď.

1. Kliknutím na **vytvořit spojnici** v pravém horním rohu uložte definici.

1. Kliknutím na **Zavřít** konektor zavřete.

## <a name="2---test-the-connection"></a>2. testování připojení

Při prvním vytvoření konektoru ho budete muset znovu otevřít ze seznamu vlastních konektorů, aby ho bylo možné otestovat. Později, pokud provedete další aktualizace, můžete otestovat v rámci průvodce.

Pro tento úkol budete potřebovat [klíč rozhraní API pro dotazy](search-security-api-keys.md#find-existing-keys) . Při každém vytvoření připojení, ať už se jedná o testovací běh nebo zahrnutí do aplikace, konektor potřebuje klíč rozhraní API pro dotazování, který se používá pro připojení k Azure Kognitivní hledání.

1. Úplně vlevo klikněte na **vlastní konektory**.

1. Najděte svůj konektor v seznamu (v tomto kurzu je to "AzureSearchQuery").

1. Vyberte konektor, rozbalte seznam akce a vyberte **Zobrazit vlastnosti**.

    :::image type="content" source="./media/search-howto-powerapps/1-11-1-test-connector.png" alt-text="Zobrazit vlastnosti" border="true":::

1. V pravém horním rohu vyberte **Upravit** .

1. Vyberte **4. Test** pro otevření zkušební stránky

1. V testovací operaci klikněte na **+ nové připojení**.

1. Zadejte klíč rozhraní API pro dotazování. Toto je dotaz Azure Kognitivní hledání pro přístup k indexu jen pro čtení. Klíč můžete [Najít](search-security-api-keys.md#find-existing-keys) v Azure Portal. 

1. V části operace klikněte na tlačítko **test operace** . Pokud jste úspěšní, měli byste vidět stav 200 a v těle odpovědi byste měli vidět JSON, který popisuje výsledky hledání.

    :::image type="content" source="./media/search-howto-powerapps/1-11-2-test-connector.png" alt-text="Odpověď JSON" border="true":::

## <a name="3---visualize-results"></a>3 – vizualizace výsledků

V tomto kroku vytvoříte aplikaci Power, která má vyhledávací pole, tlačítko vyhledávání a oblast zobrazení pro výsledky. Power App se připojí k nedávno vytvořenému vlastnímu konektoru a získá data z Azure Search.

1. Na levé straně rozbalte **aplikace**  >  **a nové**  >  **plátno** aplikace.

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="Vytvoření aplikace plátna" border="true":::

1. Vyberte typ aplikace. V tomto kurzu vytvoříte **prázdnou aplikaci** s **rozložením pro telefon**. Zobrazí se **aplikace Power App Studio** .

1. V nástroji Studio vyberte kartu **zdroje dat** a klikněte na nový konektor, který jste právě vytvořili. V našem případě se nazývá *AzureSearchQuery*. Klikněte na **Přidat připojení**.

   Zadejte klíč rozhraní API pro dotazování.

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="konektor připojení" border="true":::

    Nyní *AzureSearchQuery* je zdroj dat, který je k dispozici pro použití z vaší aplikace.

1. Na **kartě Vložení** přidejte do plátna několik ovládacích prvků.

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="Vložit ovládací prvky" border="true":::

1. Vložte následující prvky:

   * Textový popisek s hodnotou dotaz:
   * Textový vstup elementu (volání IT *txtQuery*, výchozí hodnota: "*")
   * Tlačítko s textem hledání 
   * Svislá galerie s názvem (volání IT *galleryResults*)

    Plátno by mělo vypadat přibližně takto:

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="Rozložení ovládacích prvků" border="true":::

1. Chcete-li, aby **tlačítko Hledat** vydávalo dotaz, vložte následující akci do **příkazového výběru**:

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Query({search: txtQuery.Text}).value))
    ```

   Na následujícím snímku obrazovky vidíte řádek vzorců pro akci při **výběru** .

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="Tlačítko při výběru" border="true":::

   Tato akce způsobí, že tlačítko aktualizuje novou kolekci s názvem *azResult* s výsledkem vyhledávacího dotazu, a to pomocí textu v textovém poli *txtQuery* jako termín dotazu.

   > [!NOTE]
   > Zkuste to znovu, pokud se zobrazí chyba syntaxe vzorce "funkce ClearCollect" obsahuje některé neplatné funkce ":
   > 
   > * Nejdřív se ujistěte, že je odkaz na konektor správný. Vymažte název konektoru a začněte psát název vašeho konektoru. Technologie IntelliSense by měla navrhovat pravou spojnici a operaci.
   > 
   > * Pokud chyba přetrvává, odstraňte a znovu vytvořte konektor. Pokud je k dispozici více instancí konektoru, aplikace pravděpodobně používá nesprávný jeden z nich.
   > 

1. Propojte svislý ovládací prvek galerie s kolekcí *azResult* , která byla vytvořena při dokončení předchozího kroku. 

   Vyberte ovládací prvek galerie a v podokně Vlastnosti proveďte následující akce.

   * Nastavte **zdroj dat** na *azResult*.
   * Vyberte **rozložení** , které pro vás bude fungovat na základě typu dat v indexu. V tomto případě jsme použili rozložení *název, podnadpis a text* .
   * **Upravte pole** a vyberte pole, která chcete vizualizovat.

    Vzhledem k tomu, že jsme tento konektor definovali jako výsledek ukázky, aplikace si uvědomuje pole, která jsou k dispozici ve vašem indexu.
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="Pole galerie" border="true":::   
 
1. Stisknutím klávesy **F5** zobrazte náhled aplikace.  

    :::image type="content" source="./media/search-howto-powerapps/2-8-3-final.png" alt-text="Konečná aplikace" border="true":::    

<!--     Remember that the fields can be set to calculated values.

    For the example, setting using the *"Image, Title and Subtitle"* layout and specifying the *Image* function as the concatenation of the root path for the data and the file name (for instance, `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`) will produce the result below.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="Final app" border="true":::         -->

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás stojí peníze. Můžete odstraňovat prostředky jednotlivě nebo odstraněním skupiny prostředků odstranit celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem.

## <a name="next-steps"></a>Další kroky

Power Apps umožňuje rychlý vývoj aplikací pro vlastní aplikace. Teď, když víte, jak se připojit k indexu hledání, se dozvíte víc o vytváření bohatých vizualizací v rámci vlastní aplikace Power.

> [!div class="nextstepaction"]
> [Katalog kurzů pro Power Apps](/powerapps/learning-catalog/get-started)
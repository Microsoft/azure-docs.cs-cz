---
title: Jak se dotazovat azure kognitivní vyhledávání z Power Apps
titleSuffix: Azure Cognitive Search
description: Podrobné pokyny, jak vytvořit vlastní konektor pro kognitivní vyhledávání a jak ho vizualizovat z Power Appu
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: c246f8652227a5ad2c0798880e530d6039cdeea8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385108"
---
# <a name="how-to-query-a-cognitive-search-index-from-power-apps"></a>Jak zadat dotaz na index kognitivního vyhledávání z Power Apps

Tento dokument ukazuje, jak vytvořit vlastní konektor Power Apps, abyste mohli načíst výsledky hledání z indexu vyhledávání. Ukazuje také, jak vydat vyhledávací dotaz a vizualizovat výsledky z Power App. 

## <a name="prerequisites"></a>Požadavky
*    Přístup k účtu Power Apps s možností vytvářet vlastní konektory.
*    Předpokládáme, že jste už vytvořili index azure vyhledávání.

## <a name="create-a-custom-connector-to-query-azure-search"></a>Vytvoření vlastního konektoru pro dotazování azure search

Existují dva hlavní kroky k mít PowerApp, který zobrazuje výsledky Azure Cognitive Search. Nejprve vytvoříme konektor, který může dotazovat index vyhledávání. V [další části](#visualize-results-from-the-custom-connector) aktualizujeme vaši aplikaci Power Apps, abychom vizualizovali výsledky vrácené konektorem.

1. Přejděte na [make.powerapps.com](http://make.powerapps.com) a **přihlaste se**.

1. Hledání **vlastních datových** > **konektorů**
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="Vlastní nabídka konektoru" border="true":::

1. Klikněte na **+ Nový vlastní konektor** a pak vyberte Vytvořit z **prázdné**.

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="Vytvořit z prázdné nabídky" border="true":::

1. Pojmenujte vlastní konektor. (to znamená *AzureSearchQuery*) a potom klepněte na tlačítko **Pokračovat**. Tím vyvoláte průvodce pro vytvoření nového konektoru.

1. Zadejte informace na obecnou stránku.

    - Barva pozadí ikony (například #007ee5)
    - Popis (například "Konektor pro Azure Cognitive Search")
    - V hostiteli budete muset zadat adresu URL vyhledávací `<yourservicename>.search.windows.net`služby (například )
    - Pro základní adresu URL jednoduše zadejte "/"
    
    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="Obecný informační dialog" border="true":::

1. Na stránce zabezpečení nastavte *klíč rozhraní API* jako typ **ověřování**, nastavte popisek parametru a pole názvu parametru jako klíč *rozhraní API*. V **části Umístění parametru**vyberte *Záhlaví,* jak je znázorněno níže.
 
    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="Možnost typu ověřování" border="true":::

1. Na stránce Definice vyberte **+ Nová akce** a vytvořte akci, která bude dotazovat index. Zadejte hodnotu "Dotaz" pro souhrn a název ID operace. Zadejte popis jako *"Dotazy na index vyhledávání"*.
 
    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="Nové možnosti akce" border="true":::


1. Stisknutím tlačítka **+ Import z ukázky** definujte parametry a záhlaví. Dále definujete požadavek na dotaz.  

    * Výběr slovesa`GET`
    * Pro adresu URL zadejte ukázkový dotaz pro index vyhledávání, například:
       
    >https://yoursearchservicename.search.windows.net/indexes/yourindexname/docs?search=*&api-version=2019-05-06-Preview
    

    **Power Apps** použije syntaxi k extrahování parametrů z dotazu. Všimněte si, že jsme explicitně definovali vyhledávací pole. 

    :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="Importování z ukázky" border="false":::

1.  Kliknutím na **Importovat** automaticky předvyplnění dialogového okna Požadavek.

    :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="Import ze vzorového dialogu" border="false":::


1. Dokončete nastavení metadat parametrů kliknutím na **...** vedle každého z parametrů.

    - Pro *hledání* `*` : Nastavit jako **výchozí hodnotu**, nastavit **jako** *false* a nastavit **viditelnost** na *žádnou*. 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="Metadata vyhledávacího parametru" border="true":::

    - Pro *verzi rozhraní* `2019-05-06-Preview` api : Nastavit jako **výchozí hodnotu**nastavte **viditelnost** jako interní a **nastavenou** na *hodnotu True*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="Metadata parametrů verze" border="true":::

    - Podobně pro *api-key*nastavte podle **potřeby**s *vnitřní* **viditelností**. Jako **výchozí hodnotu**zadejte klíč rozhraní API vyhledávací služby .
    
    Po provedeném provádění těchto změn přepněte do zobrazení **Editor swagger.** V části parametry byste měli vidět následující konfiguraci:    

    ```
          parameters:
          - {name: search, in: query, required: false, type: string, default: '*'}
          - {name: api-version, in: query, required: true, type: string, default: 2019-05-06-Preview,
            x-ms-visibility: internal}
          - {name: api-key, in: header, required: true, type: string, default: YOURKEYGOESHERE,
            x-ms-visibility: internal}
    ```

1. V části Odpověď klepněte na **tlačítko "Přidat výchozí odpověď"**. To je důležité, protože to pomůže **Power Apps** pochopit schéma odpovědi. Vložte vzorovou odpověď.

    > [!TIP] 
    > Existuje omezení znaků pro odpověď JSON, kterou můžete zadat, takže můžete chtít zjednodušit JSON tak, aby před vložením. Důležité aspekty schéma/formát odpovědi. Skutečné hodnoty v odpovědi vzorku jsou méně důležité a lze je zjednodušit, aby se snížil počet znaků.
    

1.    Než ho budete moci otestovat, klepněte na tlačítko **Vytvořit konektor** v pravém horním rohu obrazovky.

1.  Na testovací stránce klikněte na tlačítko **+ Nové připojení**a zadejte klíč dotazu vyhledávací služby jako hodnotu pro klíč rozhraní *API*.

    Tento krok může převést ven průvodce a do připojení stránky. Můžete chtít vrátit do editoru vlastní připojení skutečně otestovat připojení. Přejít na **vlastní konektor** > Vyberte nově vytvořený konektor > *...* > **Zobrazit vlastnosti** > **Upravit** > **4. Test** se dostat zpět na zkušební stránku.

1.    Nyní klikněte na **možnost Testovat operaci** a ujistěte se, že získáváte výsledky z indexu. Pokud jste byli úspěšní, měli byste vidět stav 200 a v textu odpovědi byste měli vidět JSON, který popisuje výsledky hledání.




## <a name="visualize-results-from-the-custom-connector"></a>Vizualizujte výsledky z vlastního konektoru
Cílem tohoto kurzu není ukázat vám, jak vytvořit efektní uživatelské prostředí s aplikacemi napájení, takže rozložení uživatelského rozhraní bude minimalistické. Pojďme vytvořit PowerApp s vyhledávacím polem, vyhledávacím tlačítkem a zobrazit výsledky v ovládacím prvku galerie.  PowerApp se připojí k našemu nedávno vytvořenému vlastnímu konektoru, aby získal data z Azure Search.

1. Vytvořte novou power aplikaci. Přejděte do části **Aplikace,** klikněte na **+ Nová aplikace**a vyberte Základní **stránka**.

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="Vytvoření aplikace plátna" border="true":::

1. Vyberte typ aplikace, který chcete. Pro tento kurz vytvořte **prázdnou aplikaci** s **rozložením telefonu**. Zobrazí se **Power Apps Studio.**

1. Jakmile jste ve studiu, vyberte kartu **Zdroje dat** a klikněte na nový konektor, který jste právě vytvořili. V našem případě se nazývá *AzureSearchQuery*. Klepněte **na tlačítko Přidat připojení**.

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="připojit konektor" border="true":::

    *AzureSearchQuery* je nyní zdroj dat, který je k dispozici pro použití z vaší aplikace.
    
1. Přejděte na **kartu Vložení**, abychom do formuláře mohli přidat několik ovládacích prvků.

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="Vložit ovládací prvky" border="true":::

1.  Vložte tyto prvky:
    -   Textový popisek s hodnotou Dotaz:"
    -   Element pro zadávání textu (volat *txtQuery*, výchozí hodnota: "*")
    -   Tlačítko s textem "Hledat" 
    -   S názvem Vertikální galerie (zavolejte *galeriiResults*)
    
    Formulář by měl vypadat nějak takto:

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="Rozložení Ovládací prvky" border="true":::

1. Chcete-li, aby **tlačítko Hledat** vystavilo dotaz, vyberte tlačítko a vložte následující akci, kterou chcete provést s **onSelect**:

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Get({search: txtQuery.Text}).value))
    ```

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="Tlačítko OnSelect" border="true":::
 
    Tato akce způsobí, že tlačítko aktualizovat novou kolekci s názvem *azResult* s výsledkem vyhledávacího dotazu, pomocí textu v textovém poli *txtQuery* jako termín dotazu.
    
1.  Jako další krok propojíme vertikální galerii, kterou jsme vytvořili, s kolekcí *azResult.* Vyberte ovládací prvek galerie a proveďte následující akce v podokně vlastností.

    -  Nastavte **Zdroj dat** na *azResult*.
    
    -  Vyberte **rozložení,** které vám vyhovuje na základě typu dat v indexu. V tomto případě jsme použili *název, titulky a rozložení těla.*
    
    -  **Upravte pole**a vyberte pole, která chcete vizualizovat.

    Vzhledem k tomu, že jsme poskytli ukázkový výsledek, když jsme definovali konektor, aplikace si je vědoma polí dostupných ve vašem indexu.
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="Pole galerie" border="true":::   
 
1.  Stisknutím **klávesy F5** zobrazíte náhled aplikace.  

    Nezapomeňte, že pole lze nastavit na vypočtené hodnoty.      
    V tomto příkladu nastavení pomocí rozložení *"Obrázek, Název a Titulky"* a určení funkce *Obrázek* jako zřetězení `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`kořenové cesty pro data a název souboru (například ) vytvoří níže uvedený výsledek.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="Konečná aplikace" border="true":::        

## <a name="next-steps"></a>Další kroky

Další informace a online školení najdete v [tématu Power Apps Learning Catalog](https://docs.microsoft.com/powerapps/learning-catalog/get-started).


---
title: Volání funkce z PowerApps | Dokumentace Microsoftu
description: Vytvoření vlastního konektoru, pak zavolat funkci pomocí tohoto konektoru.
services: functions
keywords: cloudové aplikace, cloud services, PowerApps, obchodní procesy, obchodní aplikace
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: glenga
ms.reviewer: sunayv
ms.custom: ''
ms.openlocfilehash: 55de3cd8830834a2af512661d5389952d927ef9f
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094311"
---
# <a name="call-a-function-from-powerapps"></a>Volání funkce z PowerApps
[PowerApps](https://powerapps.microsoft.com) platformy je určená pro obchodní specialisty k vytváření aplikací bez kódu pro tradiční aplikace. Profesionální vývojáři mohou pomocí Azure Functions rozšířit možnosti PowerApps, při stínění tvůrci aplikace PowerApps z technické podrobnosti.

Vytvoříte aplikaci v tomto tématu na základě scénáře údržby pro větrné turbíny. V tomto tématu se dozvíte, jak volat funkci, která jste definovali v [vytvoření definice OpenAPI pro funkci](functions-openapi-definition.md). Funkce určuje, zda je nouzová oprava větrné turbíny nákladově efektivní.

![Dokončená aplikace v PowerApps](media/functions-powerapps-scenario/finished-app.png)

Informace o volání samé funkce z Microsoft Flow, najdete v části [volání funkce z Microsoft Flow](functions-flow-scenario.md).

V tomto tématu se dozvíte, jak:

> [!div class="checklist"]
> * Příprava ukázkových dat v aplikaci Excel.
> * Exportujte definici rozhraní API.
> * Přidáte připojení k rozhraní API.
> * Vytvoření nové aplikace a přidat zdroje dat.
> * Přidání ovládacích prvků pro zobrazení dat v aplikaci.
> * Přidání ovládacích prvků pro volání funkce a zobrazovat data.
> * Spuštění aplikace k určení, zda na opravy je nákladově efektivní.

## <a name="prerequisites"></a>Požadavky

+ Aktivní [obchodního vztahu v PowerApps](https://docs.microsoft.com/en-us/powerapps/maker/signup-for-powerapps) pomocí stejné přihlašovací údaje jako váš účet Azure. 
+ Aplikace Excel a [Excelový ukázkový soubor](https://procsi.blob.core.windows.net/docs/turbine-data.xlsx) , kterou použijete jako zdroj dat pro vaši aplikaci.
+ Dokončení tohoto kurzu [vytvoření definice OpenAPI pro funkci](functions-openapi-definition.md).

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>Přidat připojení k rozhraní API
Vlastní rozhraní API (označované také jako vlastní konektor) je k dispozici v PowerApps, ale je třeba připojení k rozhraní API před použitím v aplikaci.

1. V [web.powerapps.com](https://web.powerapps.com), klikněte na tlačítko **připojení**.

    ![Připojení PowerApps](media/functions-powerapps-scenario/powerapps-connections.png)

1. Klikněte na tlačítko **nové připojení**, přejděte dolů k položce **opravy turbíny** konektor a klikněte na něj.

    ![Nové připojení](media/functions-powerapps-scenario/new-connection.png)

1. Zadejte klíč rozhraní API a klikněte na tlačítko **vytvořit**.

    ![Vytvoření připojení](media/functions-powerapps-scenario/create-connection.png)

> [!NOTE]
> Pokud aplikaci sdílíte s jinými uživateli, každý uživatel, který funguje na nebo použije aplikaci musíte také zadat klíč rozhraní API pro připojení k rozhraní API. Toto chování může v budoucnu změnit a aktualizujeme tak, aby odrážely, které toto téma.

## <a name="create-an-app-and-add-data-sources"></a>Vytvoření nové aplikace a přidat zdroje dat
Nyní jste připraveni vytvořit aplikaci v PowerApps a přidat vlastní rozhraní API a Excelových dat jako zdroj dat pro aplikaci.

1. Na webu [web.powerapps.com](https://web.powerapps.com) zvolte **Začít od začátku** > ![ikona mobilní aplikace](media/functions-powerapps-scenario/icon-phone-app.png) (telefon) > **Vytvořit tuto aplikaci**.

    ![Začít od začátku - telefonní aplikace](media/functions-powerapps-scenario/create-phone-app.png)

    Aplikace se otevře v PowerApps Studio pro web. Následující obrázek ukazuje různé části PowerApps Studio.

    ![PowerApps Studio](media/functions-powerapps-scenario/powerapps-studio.png)

    **(A) levého navigačního panelu**, ve kterém se zobrazí hierarchické zobrazení všech ovládacích prvků na jednotlivých obrazovkách

    **(B) prostředním podokně**, který se zobrazuje obrazovka, že pracujete na

    **(C) v pravém podokně**, kde nastavujete možnosti, jako je rozložení a zdroje dat

    **(D) vlastnost** rozevíracího seznamu, kde můžete vybrat vlastnosti, které se vztahují vzorce

    **(E) vzorců**, kde můžete přidávat vzorce (jako v Excelu), které definují chování aplikace
    
    **(F) pás karet**, ve kterém můžete přidat ovládací prvky a přizpůsobit prvky návrhu

1. Jako zdroj dat, přidejte Excelový soubor.

    Data, která budete importovat vypadá takto:

    ![Import Excelových dat](media/functions-powerapps-scenario/excel-table.png)

    1. Na plátně aplikace zvolte **připojit k datům**.

    1. Na **Data** panelu, klikněte na tlačítko **přidat do aplikace statická data**.

        ![Přidání zdroje dat](media/functions-powerapps-scenario/add-static-data.png)

        Obvykle by číst a zapisovat data z externího zdroje, ale přidáte data aplikace Excel jako statická data vzhledem k tomu, že toto je vzorový.

    1. Přejděte k souboru aplikace Excel můžete uložit, vyberte **turbíny** tabulky a klikněte na tlačítko **připojit**.

        ![Přidání zdroje dat](media/functions-powerapps-scenario/choose-table.png)


1. Jako zdroj dat přidáte vlastní rozhraní API.

    1. Na **Data** panelu, klikněte na tlačítko **přidat zdroj dat**.

    1. Klikněte na tlačítko **opravy turbíny**.

        ![Konektor opravy turbíny](media/functions-powerapps-scenario/turbine-connector.png)

## <a name="add-controls-to-view-data-in-the-app"></a>Přidání ovládacích prvků pro zobrazení dat v aplikaci
Teď, když zdroje dat jsou k dispozici v aplikaci, přidáte obrazovku do vaší aplikace, můžete zobrazit data turbíny.

1. Na **Domů** klikněte na tlačítko **novou obrazovku** > **obrazovka se seznamem**.

    ![Obrazovka se seznamem](media/functions-powerapps-scenario/list-screen.png)

    PowerApps se přidá obrazovku, která obsahuje *Galerie* zobrazíte položky a další ovládací prvky, které umožňují vyhledávání, řazení a filtrování.

1. Změnit záhlaví `Turbine Repair`a změňte velikost Galerie tak, že existuje místo pro další ovládací prvky v ní.

    ![Změnit název a změňte velikost Galerie](media/functions-powerapps-scenario/gallery-title.png)

1. Je Galerie vybraná, v pravém podokně v části **vlastnosti**, klikněte na tlačítko **CustomGallerySample**.

    ![Změnit zdroj dat](media/functions-powerapps-scenario/change-data-source.png)

1. V **Data** panelu, vyberte **turbíny** ze seznamu.

    ![Výběr zdroje dat](media/functions-powerapps-scenario/select-data-source.png)

    Datová sada neobsahuje bitové kopie, takže teď že můžete změnit rozložení, aby lépe vyhovovaly data. 

1. Pořád ještě v **Data** panelu, změňte **rozložení** k **název, titulek a text**.

    ![Změna rozložení galerie](media/functions-powerapps-scenario/change-layout.png)

1. V posledním kroku v **Data** panelu, změnit pole, která se zobrazí v galerii.

    ![Změna polí Galerie](media/functions-powerapps-scenario/change-fields.png)
    
    + **Body1** = LastServiceDate
    + **Subtitle2** = ServiceRequired
    + **Název2** = název 

1. Je Galerie vybraná, nastavte **TemplateFill** nastavte na následující vzorec: `If(ThisItem.IsSelected, Orange, White)`.

    ![Vzorec výplň šablony](media/functions-powerapps-scenario/formula-fill.png)

    Nyní je snazší zjistit, která položka Galerie je vybrána.

    ![Vybrané položky](media/functions-powerapps-scenario/selected-item.png)

1. Není nutné na původní obrazovku v aplikaci. V levém podokně, najeďte myší na **Screen1**, klikněte na tlačítko **...** , a **odstranit**.

    ![Odstranění obrazovky](media/functions-powerapps-scenario/delete-screen.png)

1. Klikněte na tlačítko **souboru**a název aplikace. Klikněte na tlačítko **Uložit** v nabídce vlevo klikněte **Uložit** v pravém dolním rohu.

Existuje mnoho dalších formátování, které byste obvykle udělali v produkční aplikace, ale přejdeme k důležité části v tomto scénáři – volání funkce.

## <a name="add-controls-to-call-the-function-and-display-data"></a>Přidání ovládacích prvků pro volání funkce a zobrazení dat
Budete mít aplikaci, která zobrazuje souhrnná data pro každou turbíny, takže teď že je čas přidat ovládací prvky, které volají funkce, do které jste vytvořili a zobrazit data, která je vrácena. Přístup k funkci na základě způsobu pojmenování v definici OpenAPI; v tomto případě má `TurbineRepair.CalculateCosts()`.

1. Na pásu karet na **vložit** klikněte na tlačítko **tlačítko**. Klepnutím na stejné kartě **popisek**

    ![Vložit tlačítko a popisek](media/functions-powerapps-scenario/insert-controls.png)

1. Přetáhněte tlačítko a popisek pod galerii a změňte velikost popisku. 

1. Vyberte text tlačítka a změňte ji na `Calculate costs`. Aplikace by měl vypadat jako na následujícím obrázku.

    ![Aplikace s tlačítkem](media/functions-powerapps-scenario/move-button-label.png)

1. Klikněte na tlačítko a zadejte následující vzorec na tlačítku pro **OnSelect** vlastnost.

    ```
    If (BrowseGallery1.Selected.ServiceRequired="Yes", ClearCollect(DetermineRepair, TurbineRepair.CalculateCosts({hours: BrowseGallery1.Selected.EstimatedEffort, capacity: BrowseGallery1.Selected.MaxOutput})))
    ```
    Tento vzorec provede, když dojde ke kliknutí na tlačítko a provede následující akce, pokud má vybrané položky galerie **ServiceRequired** hodnotu `Yes`:

    + Vymaže *kolekce* `DetermineRepair` k odebrání dat z předchozích volání. Kolekce je tabulková proměnná.

    + Přiřadí do kolekce data vrácená voláním funkce `TurbineRepair.CalculateCosts()`. 
    
        Obsahuje hodnotu předanou do funkce pocházejí z **EstimatedEffort** a **MaxOutput** pole pro vybranou položku v galerii. Tato pole nejsou zobrazeny v galerii, ale jsou stále k dispozici pro použití ve vzorcích.

1. Vyberte popisek a zadejte následující vzorec v popisku **Text** vlastnost.

    ```
    "Repair decision: " & First(DetermineRepair).message & " | Cost: " & First(DetermineRepair).costToFix & " | Revenue: " & First(DetermineRepair).revenueOpportunity
    ```
    Tento vzorec používá `First()` funkce pro přístup k prvnímu (zároveň jedinému) řádek `DetermineRepair` kolekce. Potom zobrazí tři hodnoty, které vrátí funkce hodnotu: `message`, `costToFix`, a `revenueOpportunity`. Tyto hodnoty jsou prázdné před prvním spuštění aplikace.

    Dokončené aplikace by měl vypadat jako na následujícím obrázku.

    ![Dokončená aplikace před spuštěním](media/functions-powerapps-scenario/finished-app-before-run.png)


## <a name="run-the-app"></a>Spuštění aplikace
Budete mít kompletní aplikaci! Nyní je čas ho spustit, vidíme, že volá funkci v akci.

1. V pravém horním rohu PowerApps Studio klikněte na tlačítko pro spuštění: ![Tlačítko spustit aplikaci](media/functions-powerapps-scenario/f5-arrow-sm.png).

1. Vyberte turbíny s hodnotou `Yes` pro **ServiceRequired**, klikněte **vypočítat náklady** tlačítko. Měli byste vidět výsledek jako na následujícím obrázku.

    ![Dokončená aplikace v PowerApps](media/functions-powerapps-scenario/finished-app.png)

1. Zkuste jiné turbíny chcete zobrazit, co bude vráceno z funkce pokaždé, když.

## <a name="next-steps"></a>Další postup
V tomto tématu jste zjistili, jak:

> [!div class="checklist"]
> * Příprava ukázkových dat v aplikaci Excel.
> * Exportujte definici rozhraní API.
> * Přidáte připojení k rozhraní API.
> * Vytvoření nové aplikace a přidat zdroje dat.
> * Přidání ovládacích prvků pro zobrazení dat v aplikaci.
> * Přidání ovládacích prvků pro volání funkce a zobrazení dat
> * Spuštění aplikace k určení, zda na opravy je nákladově efektivní.

Další informace týkající se PowerApps, najdete v článku [seznámení s PowerApps](https://powerapps.microsoft.com/tutorials/getting-started/).

Další informace o dalších zajímavých scénářů, které pomocí služby Azure Functions najdete v tématu [volání funkce z Microsoft Flow](functions-flow-scenario.md) a [vytvořit funkci, která se integruje s Azure Logic Apps](functions-twitter-email.md).

---
title: Voláním funkce Azure z Microsoft Flow | Dokumentace Microsoftu
description: Vytvoření vlastního konektoru, pak zavolat funkci pomocí tohoto konektoru.
services: functions
keywords: cloudové aplikace, cloud services, Microsoft Flow, obchodní procesy, obchodní aplikace
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: glenga
ms.reviewer: sunayv
ms.custom: ''
ms.openlocfilehash: 662c78fc7074b0dafc53c393962aa4b578779095
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092254"
---
# <a name="call-a-function-from-microsoft-flow"></a>Volání funkce z Microsoft Flow

[Microsoft Flow](https://flow.microsoft.com/) usnadňuje automatizaci pracovních postupů a obchodních procesů mezi vašimi oblíbenými aplikacemi a službami. Profesionální vývojáři mohou pomocí Azure Functions rozšířit možnosti Microsoft Flow, při stínění tvůrci tok z technické podrobnosti.

Vytvoříte tok v tomto tématu na základě scénáře údržby pro větrné turbíny. V tomto tématu se dozvíte, jak volat funkci, která jste definovali v [vytvoření definice OpenAPI pro funkci](functions-openapi-definition.md). Funkce určuje, zda je nouzová oprava větrné turbíny nákladově efektivní. Pokud je nákladově efektivní, tok pošle e-mailu pro doporučování opravy.

Informace o volání samé funkce z PowerApps, najdete v části [volání funkce z PowerApps](functions-powerapps-scenario.md).

V tomto tématu se dozvíte, jak:

> [!div class="checklist"]
> * Vytvoření seznamu v Sharepointu.
> * Exportujte definici rozhraní API.
> * Přidáte připojení k rozhraní API.
> * Vytvoření toku pro odeslání e-mailu, pokud je nákladově efektivní opravu.
> * Spuštění toku.

## <a name="prerequisites"></a>Požadavky

+ Aktivní [účet Microsoft Flow](https://flow.microsoft.com/documentation/sign-up-sign-in/) pomocí stejné přihlašovací údaje jako váš účet Azure. 
+ SharePoint, který použijete jako zdroj dat pro tento tok. Zaregistrujte si [zkušební verzi Office 365](https://signup.microsoft.com/Signup?OfferId=467eab54-127b-42d3-b046-3844b860bebf&dl=O365_BUSINESS_PREMIUM&ali=1) Pokud ještě nemáte služby SharePoint.
+ Dokončení tohoto kurzu [vytvoření definice OpenAPI pro funkci](functions-openapi-definition.md).

## <a name="create-a-sharepoint-list"></a>Vytvoření Sharepointového seznamu
Začněte tím, že vytvoření seznamu, který použijete jako zdroj dat pro příslušný tok. Seznam obsahuje následující sloupce.

| Sloupec seznamu     | Typ dat           | Poznámky                                    |
|-----------------|---------------------|------------------------------------------|
| **Název**           | Jeden řádek textu | Název turbíny                      |
| **LastServiceDate** | Datum                |                                          |
| **MaxOutput**       | Číslo              | Výstup turbíny v KwH            |
| **ServiceRequired** | Ano/Ne              |                                          |
| **EstimatedEffort** | Číslo              | Odhadovaný čas na opravě v hodinách |

1. Na Sharepointovém webu klikněte nebo klepněte na **nový**, pak **seznamu**.

    ![Vytvoření nového seznamu SharePointu](./media/functions-flow-scenario/new-list.png)

2. Zadejte název `Turbines`a pak klikněte nebo klepněte na **vytvořit**.

    ![Zadání názvu nového seznamu](./media/functions-flow-scenario/create-list.png)

    **Turbíny** seznamu se vytvoří s výchozí **název** pole.

    ![Seznam turbíny](./media/functions-flow-scenario/initial-list.png)

3. Klikněte nebo klepněte na ![ikona nové položky](./media/functions-flow-scenario/icon-new.png) pak **datum**.

    ![Přidání pole s jedním řádkem textu](./media/functions-flow-scenario/add-column.png)

4. Zadejte název `LastServiceDate`a pak klikněte nebo klepněte na **vytvořit**.

    ![Vytvoření sloupce LastServiceDate](./media/functions-flow-scenario/date-column.png)

5. Poslední dva kroky zopakujte pro další tři sloupce v seznamu:

    1. **Číslo** > "MaxOutput"

    2. **Ano/Ne** > "ServiceRequired"

    3. **Číslo** > "EstimatedEffort"

Je to teď – byste měli mít prázdný seznam, který vypadá jako na následujícím obrázku. Přidání dat do seznamu, když vytvoříte tok.

![Prázdný seznam](media/functions-flow-scenario/empty-list.png)

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>Přidat připojení k rozhraní API
Vlastní rozhraní API (označované také jako vlastní konektor) je k dispozici v Microsoft Flow, ale je třeba připojení k rozhraní API před použitím v toku.

1. V [flow.microsoft.com](https://flow.microsoft.com), klikněte na ikonu ozubeného kolečka (v pravém horním rohu) a pak klikněte na tlačítko **připojení**.

    ![Připojení toků.](media/functions-flow-scenario/flow-connections.png)

1. Klikněte na tlačítko **vytvořit připojení**, přejděte dolů k položce **opravy turbíny** konektor a klikněte na něj.

    ![Vytvoření připojení](media/functions-flow-scenario/create-connection.png)

1. Zadejte klíč rozhraní API a klikněte na tlačítko **vytvořit připojení**.

    ![Zadejte klíč rozhraní API a vytvořit](media/functions-flow-scenario/api-key.png)

> [!NOTE]
> Pokud sdílíte tok s jinými uživateli, každý uživatel, který funguje na nebo používá tok musíte také zadat klíč rozhraní API pro připojení k rozhraní API. Toto chování může v budoucnu změnit a aktualizujeme tak, aby odrážely, které toto téma.


## <a name="create-a-flow"></a>Vytvořit tok

Nyní jste připraveni vytvořit tok, který používá vlastní konektor a seznamu služby SharePoint, kterou jste vytvořili.

### <a name="add-a-trigger-and-specify-a-condition"></a>Přidat aktivační události a určit podmínku

Vytvoření toku od začátku (bez šablony) a přidejte *aktivační událost* , který je vyvoláno, když je vytvořena položka v Sharepointovém seznamu. Potom přidáte *podmínku* k určení, co bude dál.

1. V [flow.microsoft.com](https://flow.microsoft.com), klikněte na tlačítko **Moje toky**, pak **vytvořit z prázdné**.

    ![Vytvořit z prázdné](media/functions-flow-scenario/create-from-blank.png)

2. Klikněte na aktivační události Sharepointu **když je vytvořena položka**.

    ![Zvolte aktivační událost.](media/functions-flow-scenario/choose-trigger.png)

    Pokud jste to ještě neudělali do služby SharePoint, budete vyzváni k tomu.

3. Pro **adresa webu**, zadejte název webu služby SharePoint a pro **název seznamu**, zadejte seznam, který obsahuje data turbíny.

    ![Zvolte aktivační událost.](media/functions-flow-scenario/site-list.png)

4. Klikněte na tlačítko **nový krok**, pak **přidat podmínku**.

    ![Přidat podmínku](media/functions-flow-scenario/add-condition.png)

    Microsoft Flow přidá dvě větve do toku: **Pokud Ano** a **Pokud žádná**. Přidat kroky do jednoho nebo obou větví, potom co definujete podmínku, kterou chcete porovnat.

    ![Podmínka větve](media/functions-flow-scenario/condition-branches.png)

5. Na **podmínku** karet, klepněte na první pole a pak vyberte **ServiceRequired** z **dynamický obsah** dialogové okno.

    ![Vyberte pole pro podmínku](media/functions-flow-scenario/condition1-field.png)

6. Zadejte hodnotu `True` podmínky.

    ![Zadejte hodnotu true pro podmínku](media/functions-flow-scenario/condition1-yes.png)

    Hodnota se zobrazí jako `Yes` nebo `No` v Sharepointový seznam, ale se ukládá jako *logická*– buď `True` nebo `False`. 

7. Klikněte na tlačítko **vytvořit tok** v horní části stránky. Nezapomeňte kliknout na **aktualizace toku** pravidelně.

Pro všechny položky v seznamu, tento tok zkontroluje, zda **ServiceRequired** je nastaveno na `Yes`, potom přejde na **Pokud Ano** větev nebo **Pokud ne** větve je to vhodné. Chcete-li ušetřit čas, v tomto tématu můžete určit pouze akce pro **Pokud Ano** větve.

### <a name="add-the-custom-connector"></a>Přidání vlastního konektoru

Nyní přidejte vlastní konektor, který volá funkci v Azure. Přidání vlastního konektoru do služby flow, stejně jako standardní konektor. 

1. V **Pokud Ano** větev, klikněte na tlačítko **přidat akci**.

    ![Přidání akce](media/functions-flow-scenario/condition1-yes-add-action.png)

2. V **zvolte akci** dialogovém okně vyhledejte `Turbine Repair`, pak vyberte akci **opravy turbíny - vypočítá náklady**.

    ![Zvolte akci.](media/functions-flow-scenario/choose-turbine-repair.png)

    Následující obrázek ukazuje kartu, která se přidá k toku. Pole a popisy pocházet z definice OpenAPI pro konektor.

    ![Vypočítá náklady na výchozí hodnoty](media/functions-flow-scenario/calculates-costs-default.png)

3. Na **vypočítá náklady** karet, použijte **dynamický obsah** dialogové okno Vybrat vstupy pro funkci. Microsoft Flow zobrazí číselná pole, ale ne pole data, protože definice OpenAPI, který určuje **hodin** a **kapacity** jsou číselná.

    Pro **hodin**vyberte **EstimatedEffort**a pro **kapacity**vyberte **MaxOutput**.

    ![Zvolte akci.](media/functions-flow-scenario/calculates-costs-fields.png)

     Teď můžete přidat další podmínku, na základě výstupu funkce.

4. V dolní části **Pokud Ano** větev, klikněte na tlačítko **Další**, pak **přidat podmínku**.

    ![Přidat podmínku](media/functions-flow-scenario/condition2-add.png)

5. Na **podmínka 2** karet, klepněte na první pole a pak vyberte **zpráva** z **dynamický obsah** dialogové okno.

    ![Vyberte pole pro podmínku](media/functions-flow-scenario/condition2-field.png)

6. Zadejte hodnotu `Yes`. Tok přejde na další **Pokud Ano** větev nebo **Pokud žádná** větev podle toho, jestli zpráva vrácená funkcí (ujistěte se, oprava) Ano nebo ne (nenastavujte oprava). 

    ![Zadejte yes pro podmínku](media/functions-flow-scenario/condition2-yes.png)

Tok by teď měl vypadat jako na následujícím obrázku.

![Zadejte yes pro podmínku](media/functions-flow-scenario/flow-checkpoint1.png)

### <a name="send-email-based-on-function-results"></a>Odeslání e-mailu na základě výsledků – funkce

V tomto okamžiku v toku, funkce vrátila **zpráva** hodnotu `Yes` nebo `No` z funkce, jakož i další informace o nákladech a potenciál výnosů. V **Pokud Ano** větev druhou podmínku, kterou odešlete e-mailu, ale může dělají nekonečně věcí, jako je zpětnému zápisu do Sharepointového seznamu nebo spuštění [proces schvalování](https://flow.microsoft.com/documentation/modern-approvals/).

1. V **Pokud Ano** větev druhou podmínku, můžete kliknout na tlačítko **přidat akci**.

    ![Přidání akce](media/functions-flow-scenario/condition2-yes-add-action.png)

2. V **zvolte akci** dialogovém okně vyhledejte `email`, pak vyberte akci odeslání e-mailu podle e-mailovému systému, použít (v tomto případu Outlook).

    ![Odeslání e-mailu aplikace Outlook](media/functions-flow-scenario/outlook-send-email.png)

3. Na **odeslat e-mailu** karty, vytvořte e-mailu. Zadejte platný název ve vaší organizaci pro **k** pole. Na obrázku níže vidíte i ostatní pole jsou kombinaci textu a tokenů z **dynamický obsah** dialogové okno. 

    ![Pole e-mailu](media/functions-flow-scenario/email-fields.png)

    **Title** token pochází ze Sharepointového seznamu a **CostToFix** a **RevenueOpportunity** jsou vrácené funkcí.

    Dokončený tok by měl vypadat jako na následujícím obrázku (jsme vynechali první **Pokud žádná** větev pro úsporu místa).

    ![Dokončení toku](media/functions-flow-scenario/complete-flow.png)

4. Klikněte na tlačítko **aktualizace toku** v horní části stránky klikněte **provádí**.

## <a name="run-the-flow"></a>Spuštění toku

Teď hotový, můžete přidat řádek do Sharepointového seznamu a zobrazit jak tok reaguje.

1. Vraťte se do Sharepointového seznamu a klikněte na tlačítko **rychlý upravte**.

    ![Rychlé úpravy](media/functions-flow-scenario/quick-edit.png)

2. Zadejte následující hodnoty v mřížce úpravy.

    | Sloupec seznamu     | Hodnota           |
    |-----------------|---------------------|
    | **Název**           | Turbíny 60 |
    | **LastServiceDate** | 08/04/2017 |
    | **MaxOutput**       | 2500 |
    | **ServiceRequired** | Ano |
    | **EstimatedEffort** | 10 |

3. Klikněte na **Done** (Hotovo).

    ![Rychlé úpravy – Hotovo](media/functions-flow-scenario/quick-edit-done.png)

    Když přidáte položku, aktivuje tok, který se podívejte na další.

4. V [flow.microsoft.com](https://flow.microsoft.com), klikněte na tlačítko **Moje toky**, klepněte na tok, který jste vytvořili.

    ![Moje toky](media/functions-flow-scenario/my-flows.png)

5. V části **HISTORIE BĚHŮ**, klikněte na tlačítko spuštění toku.

    ![Historie spuštění](media/functions-flow-scenario/run-history.png)

    Pokud spuštění úspěšné, můžete zkontrolovat tok operací na další stránku. Pokud z nějakého důvodu se nepodařilo spustit, další stránka obsahuje informace o odstraňování potíží.

6. Rozbalte karet zobrazíte, k čemu došlo během toku. Rozbalte například položku **vypočítá náklady** karty zobrazíte vstupy a výstupy z funkce. 

    ![Vypočítá náklady na vstupy a výstupy](media/functions-flow-scenario/calculates-costs-outputs.png)

7. Zkontrolujte e-mailový účet pro osobu, která jste zadali v **k** pole **odeslat e-mailu** karty. E-mailu z toku by měl vypadat jako na následujícím obrázku.

    ![Tok e-mailu](media/functions-flow-scenario/flow-email.png)

    Uvidíte jak tokeny se nahradily správné hodnoty ze seznamu služby SharePoint a funkce.

## <a name="next-steps"></a>Další postup
V tomto tématu jste zjistili, jak:

> [!div class="checklist"]
> * Vytvoření seznamu v Sharepointu.
> * Exportujte definici rozhraní API.
> * Přidáte připojení k rozhraní API.
> * Vytvoření toku pro odeslání e-mailu, pokud je nákladově efektivní opravu.
> * Spuštění toku.

Další informace o Microsoft Flow, najdete v článku [Začínáme s Microsoft Flow](https://flow.microsoft.com/documentation/getting-started/).

Další informace o dalších zajímavých scénářů, které pomocí služby Azure Functions najdete v tématu [volání funkce z PowerApps](functions-powerapps-scenario.md) a [vytvořit funkci, která se integruje s Azure Logic Apps](functions-twitter-email.md).

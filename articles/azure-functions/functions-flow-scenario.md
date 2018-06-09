---
title: Volání funkce jazyka Azure z Microsoft Flow | Microsoft Docs
description: Vytvořte vlastní konektor pak volání funkce pomocí tohoto konektoru.
services: functions
keywords: cloudových aplikací, cloud services, Microsoft Flow obchodních procesů, obchodní aplikace
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: glenga
ms.reviewer: sunayv
ms.custom: ''
ms.openlocfilehash: 57d80ad836a16b8821ba0cce42c822728c654dfd
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234797"
---
# <a name="call-a-function-from-microsoft-flow"></a>Volání funkce z Microsoft Flow

[Microsoft Flow](https://flow.microsoft.com/) usnadňuje automatizovat pracovní postupy a obchodní procesy mezi vaše oblíbené aplikace a služby. Profesionální vývojáře můžete použít Azure Functions k rozšíření možností Microsoft Flow při stínění toku počítačů z technické podrobnosti.

Sestavování toku v tomto tématu podle scénáři údržby pro turbín větru. Toto téma ukazuje, jak zavolat funkci, která je definována v [vytvořit definici OpenAPI pro funkci](functions-openapi-definition.md). Funkce určuje, zda nouzové opravy na turbínu větru nákladově efektivní. Pokud je nákladově efektivní, toku odešle e-mail na doporučujeme opravu.

Informace o volání stejnou funkci z PowerApps najdete v tématu [volat funkci z PowerApps](functions-powerapps-scenario.md).

V tomto tématu se dozvíte, jak:

> [!div class="checklist"]
> * Vytvoří seznam ve službě SharePoint.
> * Exportujte definici rozhraní API.
> * Přidáte připojení k rozhraní API.
> * Vytvořte toku k odesílání e-mailu, pokud je nákladově efektivní a opravit.
> * Spusťte toku.

## <a name="prerequisites"></a>Požadavky

+ Aktivní [účet Microsoft Flow](https://flow.microsoft.com/documentation/sign-up-sign-in/) s stejné přihlašovací údaje jako účet Azure. 
+ SharePoint, které můžete použít jako zdroj dat pro tento tok. Zaregistrujte si [zkušební verzi Office 365](https://signup.microsoft.com/Signup?OfferId=467eab54-127b-42d3-b046-3844b860bebf&dl=O365_BUSINESS_PREMIUM&ali=1) Pokud ještě nemáte služby SharePoint.
+ Dokončení tohoto kurzu [vytvořit definici OpenAPI pro funkci](functions-openapi-definition.md).

## <a name="create-a-sharepoint-list"></a>Vytvoření seznamu služby SharePoint
Můžete začít tak, že vytváření seznamu, který používáte jako zdroj dat pro tok. Seznam obsahuje následující sloupce.

| Sloupec seznamu     | Typ dat           | Poznámky                                    |
|-----------------|---------------------|------------------------------------------|
| **Název**           | Jeden řádek textu | Název turbíně                      |
| **LastServiceDate** | Datum                |                                          |
| **MaxOutput**       | Číslo              | Výstup turbíně v KwH            |
| **ServiceRequired** | Ano/Ne              |                                          |
| **EstimatedEffort** | Číslo              | Odhadovaná doba opravu, v hodinách |

1. Na webu služby SharePoint klikněte nebo klepněte na **nový**, pak **seznamu**.

    ![Vytvoření nového seznamu SharePointu](./media/functions-flow-scenario/new-list.png)

2. Zadejte název `Turbines`, klikněte nebo klepněte na **vytvořit**.

    ![Zadání názvu nového seznamu](./media/functions-flow-scenario/create-list.png)

    **Turbín** vytvoření seznamu s výchozím **název** pole.

    ![Seznam turbín](./media/functions-flow-scenario/initial-list.png)

3. Klikněte nebo klepněte na ![nové ikony položky](./media/functions-flow-scenario/icon-new.png) pak **datum**.

    ![Přidání pole s jedním řádkem textu](./media/functions-flow-scenario/add-column.png)

4. Zadejte název `LastServiceDate`, klikněte nebo klepněte na **vytvořit**.

    ![Vytvoření LastServiceDate sloupce](./media/functions-flow-scenario/date-column.png)

5. Zopakujte poslední dva kroky pro ostatní tři sloupce v seznamu:

    1. **Číslo** > "MaxOutput"

    2. **Ano/Ne** > "ServiceRequired"

    3. **Číslo** > "EstimatedEffort"

Je to teď – byste měli mít prázdný seznam, který vypadá jako na následujícím obrázku. Po vytvoření toku přidáte do seznamu data.

![Prázdný seznam](media/functions-flow-scenario/empty-list.png)

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>Přidat připojení k rozhraní API
Vlastní rozhraní API (také označované jako vlastní konektor) je k dispozici v Flow Microsoft, ale je třeba připojení k rozhraní API před použitím v toku.

1. V [flow.microsoft.com](https://flow.microsoft.com), klikněte na ikonu zařízení (v pravém horním rohu) a pak klikněte na tlačítko **připojení**.

    ![Postup připojení](media/functions-flow-scenario/flow-connections.png)

1. Klikněte na tlačítko **vytvořit připojení**, přejděte dolů k položce **turbína Repair** konektor a klikněte na něj.

    ![Vytvoření připojení](media/functions-flow-scenario/create-connection.png)

1. Zadejte klíč rozhraní API a klikněte na tlačítko **vytvořit připojení**.

    ![Zadejte klíč rozhraní API a vytvořit](media/functions-flow-scenario/api-key.png)

> [!NOTE]
> Pokud vaše toku sdílíte s ostatními, musí každý uživatel, který funguje na nebo použije tok také zadejte klíč rozhraní API pro připojení k rozhraní API. Toto chování může v budoucnu změnit, a toto téma tak, aby odrážela, který bude aktualizován.


## <a name="create-a-flow"></a>Vytvoření toku

Nyní jste připraveni vytvořit toku, který používá vlastní konektor a seznamu služby SharePoint, kterou jste vytvořili.

### <a name="add-a-trigger-and-specify-a-condition"></a>Přidat aktivační událost a zadat podmínku

Vytvoření tokem z prázdné (bez šablony) a přidejte *aktivační událost* , aktivuje se při vytvoření nové položky v seznamu serveru SharePoint. Pak přidáte *podmínku* určit, co se stane dále.

1. V [flow.microsoft.com](https://flow.microsoft.com), klikněte na tlačítko **Moje toků**, pak **vytvořit z prázdné**.

    ![vytvoření z prázdný](media/functions-flow-scenario/create-from-blank.png)

2. Klikněte na aktivační událost SharePoint **vytvoření položky**.

    ![Zvolte aktivační událost.](media/functions-flow-scenario/choose-trigger.png)

    Pokud jste již přihlášení do služby SharePoint, budete vyzváni k tomu.

3. Pro **adresu webu**, zadejte název lokality služby SharePoint a pro **název seznamu**, zadejte seznam, který obsahuje data turbína.

    ![Zvolte aktivační událost.](media/functions-flow-scenario/site-list.png)

4. Klikněte na tlačítko **nový krok**, pak **přidat podmínku**.

    ![Přidat podmínku](media/functions-flow-scenario/add-condition.png)

    Microsoft Flow přidá dvě větve toku: **Pokud Ano** a **Pokud žádné**. Přidat kroky do jednoho nebo obou poboček po definování podmínku, která chcete porovnat.

    ![Podmínka větví](media/functions-flow-scenario/condition-branches.png)

5. Na **podmínku** karty, klikněte na první pole a pak vyberte **ServiceRequired** z **dynamický obsah** dialogové okno.

    ![Vyberte pole pro podmínku](media/functions-flow-scenario/condition1-field.png)

6. Zadejte hodnotu `True` pro podmínku.

    ![Zadejte hodnotu true pro podmínku](media/functions-flow-scenario/condition1-yes.png)

    Hodnota je zobrazena jako `Yes` nebo `No` v SharePoint seznamu, ale je uloženo jako *boolean*, buď `True` nebo `False`. 

7. Klikněte na tlačítko **vytvořit toku** v horní části stránky. Ujistěte se, klikněte na **aktualizace toku** pravidelně.

Pro všechny položky vytvořené v seznamu, tok ověří, zda **ServiceRequired** je nastaveno na `Yes`, potom přejde na **Pokud Ano** firemní pobočky nebo **Pokud žádné** větev jako vhodné. Chcete-li ušetřit čas, v tomto tématu můžete určit pouze akce pro **Pokud Ano** firemní pobočky.

### <a name="add-the-custom-connector"></a>Přidání vlastního konektoru

Teď můžete přidat vlastní konektor, který volá funkci v Azure. Přidejte vlastní konektor toku stejně jako standardní konektor. 

1. V **Pokud Ano** větev, klikněte na tlačítko **přidat akci**.

    ![Přidání akce](media/functions-flow-scenario/condition1-yes-add-action.png)

2. V **vybrat akci** dialogové okno, vyhledejte `Turbine Repair`, pak vyberte akci, která **turbína Repair - vypočítá náklady**.

    ![Zvolte akci.](media/functions-flow-scenario/choose-turbine-repair.png)

    Následující obrázek znázorňuje karty, které se přidají do toku. Pole a popisy pocházet z definice OpenAPI pro konektor.

    ![Vypočítá náklady na výchozí hodnoty](media/functions-flow-scenario/calculates-costs-default.png)

3. Na **vypočítá náklady** karty, použijte **dynamický obsah** dialogové okno Vybrat vstupy pro funkci. Microsoft Flow ukazuje číselná pole, ale není pole datum, protože definice OpenAPI Určuje, že **hodin** a **kapacity** jsou číselná.

    Pro **hodin**, vyberte **EstimatedEffort**a pro **kapacity**, vyberte **MaxOutput**.

    ![Zvolte akci.](media/functions-flow-scenario/calculates-costs-fields.png)

     Teď můžete přidat další podmínku podle výstup funkce.

4. V dolní části **Pokud Ano** větev, klikněte na tlačítko **Další**, pak **přidat podmínku**.

    ![Přidat podmínku](media/functions-flow-scenario/condition2-add.png)

5. Na **podmínku 2** karty, klikněte na první pole a pak vyberte **zpráva** z **dynamický obsah** dialogové okno.

    ![Vyberte pole pro podmínku](media/functions-flow-scenario/condition2-field.png)

6. Zadejte hodnotu `Yes`. Tok přejde na další **Pokud Ano** firemní pobočky nebo **Pokud žádné** větve podle toho, zda zpráva vrácená funkcí yes (ujistěte se, oprava) nebo ne (Nevytvářejte oprava). 

    ![Zadejte yes pro podmínku](media/functions-flow-scenario/condition2-yes.png)

Tok by teď měl vypadat jako na následujícím obrázku.

![Zadejte yes pro podmínku](media/functions-flow-scenario/flow-checkpoint1.png)

### <a name="send-email-based-on-function-results"></a>Odeslání e-mailu na základě výsledků – funkce

V tomto okamžiku v toku, funkce vrátila **zpráva** hodnotu `Yes` nebo `No` z funkce, jakož i jiné informace o náklady a potenciální výnosy. V **Pokud Ano** větve druhou podmínku, kterou odešlete e-mailu, ale můžete udělat libovolný počet věci, jako je zápis zpátky do seznamu serveru SharePoint nebo spouštění [procesu schvalování](https://flow.microsoft.com/documentation/modern-approvals/).

1. V **Pokud Ano** větve druhou podmínku, klikněte na tlačítko **přidat akci**.

    ![Přidání akce](media/functions-flow-scenario/condition2-yes-add-action.png)

2. V **vybrat akci** dialogové okno, vyhledejte `email`, pak vyberte akce odesílání e-mailu v závislosti na e-mailovému systému použití (v této případu Outlook).

    ![Outlook odesílání e-mailu](media/functions-flow-scenario/outlook-send-email.png)

3. Na **e-mailovou zprávu** karty, napište e-mailu. Zadejte platný název ve vaší organizaci pro **k** pole. Na následujícím obrázku se zobrazí v ostatních polích představují kombinaci textu a tokeny od **dynamický obsah** dialogové okno. 

    ![Pole e-mailu](media/functions-flow-scenario/email-fields.png)

    **Název** tokenu pochází ze seznamu služby SharePoint a **CostToFix** a **RevenueOpportunity** jsou vráceným funkcí.

    Dokončené toku by měl vypadat jako na následujícím obrázku (jsme vynecháno první **Pokud žádné** větve ušetřit místo).

    ![Celého toku](media/functions-flow-scenario/complete-flow.png)

4. Klikněte na tlačítko **aktualizace toku** v horní části stránky klikněte **provádí**.

## <a name="run-the-flow"></a>Spustit toku

Teď, tok je dokončili, řádek přidáte do seznamu služby SharePoint a najdete v části Jak toku reagovat.

1. Přejděte zpět do seznamu služby SharePoint a klikněte na tlačítko **rychlé upravit**.

    ![Rychlé úpravy](media/functions-flow-scenario/quick-edit.png)

2. Zadejte následující hodnoty v mřížce upravit.

    | Sloupec seznamu     | Hodnota           |
    |-----------------|---------------------|
    | **Název**           | Turbína 60 |
    | **LastServiceDate** | 08/04/2017 |
    | **MaxOutput**       | 2500 |
    | **ServiceRequired** | Ano |
    | **EstimatedEffort** | 10 |

3. Klikněte na **Done** (Hotovo).

    ![Rychlé úpravy – Hotovo](media/functions-flow-scenario/quick-edit-done.png)

    Když přidáte položku, aktivuje tok, který jste podívejte se na další.

4. V [flow.microsoft.com](https://flow.microsoft.com), klikněte na tlačítko **Moje toků**, pak klikněte na tlačítko toku, který jste vytvořili.

    ![Moje toky](media/functions-flow-scenario/my-flows.png)

5. V části **HISTORII BĚHŮ**, klikněte na tlačítko Spustit toku.

    ![Historie spuštění](media/functions-flow-scenario/run-history.png)

    Pokud spustit byl úspěšný, můžete zkontrolovat průběh operace na další stránce. Pokud z nějakého důvodu se nezdařilo spustit, na další stránku poskytuje informace o odstraňování potíží.

6. Rozbalte karty, které chcete zobrazit, co došlo k chybě během toku. Rozbalte například položku **vypočítá náklady** karty zobrazíte vstupy a výstupy z funkce. 

    ![Vypočítá náklady na vstupy a výstupy](media/functions-flow-scenario/calculates-costs-outputs.png)

7. Vyhledávat osoby, které jste zadali v e-mailový účet **k** pole z **e-mailovou zprávu** karty. E-mailu z toku by měl vypadat jako na následujícím obrázku.

    ![Tok e-mailu](media/functions-flow-scenario/flow-email.png)

    Uvidíte, jak tokeny nahradil správné hodnoty z seznamu služby SharePoint a funkce.

## <a name="next-steps"></a>Další postup
V tomto tématu, jste zjistili, jak:

> [!div class="checklist"]
> * Vytvoří seznam ve službě SharePoint.
> * Exportujte definici rozhraní API.
> * Přidáte připojení k rozhraní API.
> * Vytvořte toku k odesílání e-mailu, pokud je nákladově efektivní a opravit.
> * Spusťte toku.

Další informace o Flow Microsoft najdete v tématu [Začínáme s Microsoft Flow](https://flow.microsoft.com/documentation/getting-started/).

Další informace o dalších zajímavých scénáře, které používají Azure Functions najdete v tématu [volat funkci z PowerApps](functions-powerapps-scenario.md) a [vytvoří funkci, která se integruje se službou Azure Logic Apps](functions-twitter-email.md).
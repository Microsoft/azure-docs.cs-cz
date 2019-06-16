---
title: Tabulek v Azure | Azure Marketplace
description: Konfigurace správy potenciálních zákazníků pro Azure Table.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: pabutler
ms.openlocfilehash: a1bcab9816627b453ba8b20b7bcd9402c2dfd151
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240561"
---
# <a name="lead-management-instructions-for-azure-table"></a>Průběžná správa pokyny, jak Azure Table

Tento článek popisuje, jak nakonfigurovat tabulku Azure pro ukládání potenciálních zákazníků. Tabulka Azure umožňuje ukládat a upravit informace o zákaznících.


## <a name="how-to-configure-azure-table"></a>Postup konfigurace tabulek Azure

1. Pokud nemáte účet Azure, můžete si [vytvořit Bezplatný zkušební účet](https://azure.microsoft.com/pricing/free-trial/).
2. Poté, co je aktivní účet Azure, přihlaste se k [webu Azure portal](https://portal.azure.com).
3. Na webu Azure Portal vytvořte účet úložiště pomocí následujícího postupu.  
    1. Vyberte **+ vytvořit prostředek** v levém řádku nabídek.  **Nový** vpravo se zobrazí podokno (okno).
    2. Vyberte **úložiště** v **nový** podokně.  A **doporučené** seznamu se zobrazí na pravé straně.
    3. Vyberte **účtu úložiště** zahájíte vytváření účtů.  Postupujte podle pokynů v článku [vytvořit účet úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Postup vytvoření účtu služby Azure storage](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    Další informace o účtech úložiště, vyberte [rychlý úvodní kurz](https://docs.microsoft.com/azure/storage/).  Další informace o cenách služby storage, najdete v části [ceny za službu storage](https://azure.microsoft.com/pricing/details/storage/).

4. Počkejte, až se vašeho účtu úložiště nepřidělí, proces, který obvykle trvá několik minut.  Pak přístup k účtu úložiště z **Domů** stránky na webu Azure Portal tak, že vyberete **zobrazte všechny prostředky** nebo tak, že vyberete **všechny prostředky** z levé navigace řádek nabídek na webu Azure portal.

    ![Přístup k vašemu účtu Azure storage](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

5. Z podokna účet úložiště, zkopírujte připojovací řetězec účtu úložiště pro klíč a vložte ho do **připojovací řetězec účtu úložiště** na portál partnerů cloudu. Je například připojení stingu:

```sql
DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
```

  ![Klíč úložiště Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Můžete použít [Průzkumníka služby Azure storage](https://azurestorageexplorer.codeplex.com/) nebo jiný podobný nástroj k zobrazení dat v tabulce úložiště. Můžete také exportovat data z tabulky Azure.


## <a name="use-microsoft-flow-with-an-azure-table-optional"></a>Azure Table pomocí Microsoft Flow (*volitelné*) 

Můžete použít [Microsoft Flow](https://docs.microsoft.com/flow/) k automatizaci oznámení pokaždé, když se přidá nový zájemce do tabulek v Azure. Pokud účet nemáte, můžete si [zaregistrujte si bezplatný účet](https://flow.microsoft.com/).


### <a name="lead-notification-example"></a>Příklad průběžná oznámení

V tomto příkladu použijte jako vodítko k vytvoření základní tok, který automaticky odesílá e-mailové oznámení, když se přidá nový zájemce do tabulky Azure. Tento příklad nastaví počet opakování zasílání informací o zájemcích každou hodinu, pokud tabulka úložiště se aktualizuje.

1. Přihlaste se ke svému účtu Microsoft Flow.
2. V levém navigačním panelu vyberte **Moje toky**.
3. Na horním navigačním panelu vyberte **+ nová**.  
4. V rozevíracím seznamu vyberte **+ vytvořit z prázdné**
5. V části Vytvoření toku od začátku, vyberte **vytvořit z prázdné**.

   ![Vytvoření nového toku od začátku](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

6. Na stránce konektory a triggery hledání, vyberte **triggery**.
7. V části **triggery**vyberte **opakování**.
8. V **opakování** okně ponechte výchozí nastavení 1 pro **Interval**. Z **frekvence** rozevíracího seznamu vyberte **hodinu**.

   >[!NOTE] 
   >I když v tomto příkladu je interval 1 hodina, můžete vybrat interval a frekvenci, kterou je nejvhodnější pro vaše obchodní potřeby.

   ![Nastavení četnosti 1 hodiny pro opakování](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

9. Vyberte **+ nový krok**.
10. Vyhledejte "Get poslední čas" a pak vyberte **získat minulý čas** v části akce. 

    ![Vyhledejte a vyberte získat minulý čas akce](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

11. V **získat minulý čas** okno, nastaveno **Interval** na hodnotu 1.  Z **časovou jednotku** rozevíracího seznamu vyberte **hodinu**.
    >[!IMPORTANT] 
    >Ujistěte se, že tato jednotka intervalu a času odpovídá Interval a frekvenci jste nakonfigurovali pro opakování.

    ![Získat sadu poslední časový interval](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >Váš tok můžete zkontrolovat v každém okamžiku k ověření, že každý krok je správně nakonfigurovaný. Pokud chcete zkontrolovat svůj tok, vyberte **kontrola toku** z řádku nabídek Flow.

V následující sadu kroků se připojíte k Azure table a nastavte logika zpracování pro zpracování nových potenciálních zákazníků.

1. Po Get za časový krok, vyberte **+ nový krok**a poté vyhledejte "Get"subjekty.
2. V části **akce**vyberte **získat entity**a pak vyberte **zobrazit pokročilé možnosti**.
3. V **získat entity** okno, zadejte informace pro následující pole:

   - **Tabulka** – zadejte název vašeho Azure Table Storage. Další snímek obrazovky ukazuje výzvu, při zadání "MarketPlaceLeads" v tomto příkladu se. 

     ![Vyberte vlastní hodnotu pro název tabulky Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **Dotaz filtru** – klikněte na toto pole a **získat minulý čas** ikona se zobrazí v automaticky otevíraném okně. Vyberte **minulý čas** využít jako časové razítko pro filtrování dotazu. Alternativně můžete vložit následující funkci do pole: čas vytvoření `gt datetime'@{body('Get_past_time')}'` 

     ![Nastavit filtr dotazu – funkce](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

4. Vyberte **nový krok** a přidáním podmínky, kontrola tabulku Azure pro nové zájemce.

   ![Přidejte podmínku, která skenování tabulek v Azure pomocí nového kroku](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

5. V **zvolte akci** okně **akce**a pak vyberte **podmínku** ovládacího prvku.

     ![Přidat podmínku ovládací prvek](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

6. V **podmínku** okna, vyberte **zvolit hodnotu** pole a pak vyberte **výraz** v automaticky otevíraném okně.
7. Vložit `length(body('Get_entities')?['value'])` do ***fx*** pole. Vyberte **OK** přidání této funkce. Abychom mohli dokončit nastavování podmínky:

   - Vyberte možnost "je větší než" z rozevíracího seznamu.
   - Zadejte jako hodnotu 0 

     ![Přidání funkce do podmínky](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

8. Nastavení akce provedou v závislosti na výsledek podmínky.

     ![Nastavení akce na základě výsledků podmínku](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

9. Pokud se podmínka přeloží na **Pokud žádná**, nic nedělají. 
10. Pokud se podmínka přeloží na **Pokud Ano**, aktivují akci, která se připojuje účtu Office 365 k odesílání e-mailu. Vyberte **přidat akci**.
11. Vyberte **odeslat e-mailu**. 
12. V **odeslat e-mailu** okno, zadejte informace pro následující pole:

    - **K** – zadejte e-mailovou adresu pro všechny uživatele, který se zobrazí toto oznámení.
    - **Předmět** – zadejte předmět e-mailu. Příklad: Nové zájemce!
    - **Tělo**:   Text, který chcete zahrnout do každého e-mailu (volitelné) a potom vložit do těla `body('Get_entities')?['value']` jako funkci pro vložení informací o potenciálních zákazníků.

      >[!NOTE] 
      >Můžete vložit další statických nebo dynamických datových bodů do těla tohoto e-mailu.

      ![Nastavení e-mailu pro oznámení o potenciálních zákazníků](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

13. Vyberte **Uložit** uložte tok. Microsoft Flow bude automaticky testování tohoto toku pro chyby. Pokud nejsou k dispozici nějaké chyby, váš tok se spustí po uložení.

Následující snímek obrazovky ukazuje příklad vzhled konečné toku.

[![Sekvence konečné toku](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

(*Klepněte na ikonu zvětšení.* )


### <a name="manage-your-flow"></a>Správa toku

Správa toku po jeho spuštění je snadné.  Máte plnou kontrolu nad váš tok. Můžete ji zastavit, upravovat, najdete v historii spuštění a analýz. Další snímek obrazovky znázorňuje možnosti, které jsou dostupné pro správu toku. 

 ![Správa toku](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

Tok běžel, dokud jej nezastavíte pomocí **vypnout tok** možnost.

Pokud se nezobrazují žádné zájemce e-mailová oznámení, znamená to, že nebyly přidány nové zájemce do tabulky Azure. Pokud neexistují žádné selhání toků, získáte podobný e-mail jako v příkladu v následující snímek obrazovky.

 ![Tok selhání e-mailové oznámení](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)


## <a name="next-steps"></a>Další postup

[Konfigurace potenciálních zákazníků](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)

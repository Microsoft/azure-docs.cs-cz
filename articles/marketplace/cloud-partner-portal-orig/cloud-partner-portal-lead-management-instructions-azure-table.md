---
title: Úložiště tabulek v Azure | Azure Marketplace
description: Konfigurace správy zájemců ve službě Azure Table Storage.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: dsindona
ms.openlocfilehash: e65f2041cdb2e25341bfd63783c70ec09c1216b5
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124699"
---
# <a name="lead-management-instructions-for-table-storage"></a>Pokyny pro správu úložiště tabulek pro vedoucí služby

Tento článek popisuje, jak nakonfigurovat službu Azure Table Storage pro správu zájemců prodeje. Table Storage pomáhá ukládat a upravovat informace o zákaznících.

## <a name="configure-table-storage"></a>Konfigurace úložiště tabulek

1. Pokud nemáte účet Azure, [Vytvořte si bezplatný zkušební účet](https://azure.microsoft.com/pricing/free-trial/).
1. Až bude váš účet aktivní, přihlaste se k [Azure Portal](https://portal.azure.com).
1. V Azure Portal postupujte podle následujících kroků:  
    1. V podokně na levé straně vyberte **+ vytvořit prostředek** . Otevře se **nové** podokno.
    1. V podokně **Nový** vyberte **úložiště**. **Vybraný** seznam se otevře na pravé straně.
    1. Vyberte **účet úložiště**. Pak postupujte podle pokynů v tématu [Vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Vytvoření účtu úložiště Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    Další informace o účtech úložiště najdete v [kurzech rychlý Start](https://docs.microsoft.com/azure/storage/). Informace o cenách najdete v tématu ceny za službu [Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

1. Počkejte, než se zřídí účet úložiště, což obvykle trvá několik minut. Pak přejděte k účtu z domovské stránky Azure Portal: vyberte **Zobrazit všechny prostředky** nebo **všechny prostředky** v navigačním podokně.

    ![Přístup k účtu služby Azure Storage](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

1. V podokně účtu úložiště zkopírujte připojovací řetězec účtu úložiště pro tento klíč. Vložte ho do pole **připojovací řetězec** pro účet úložiště v portál partnerů cloudu.

    Příklad připojovacího řetězce:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

      ![Klíč úložiště Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Data v úložišti tabulek můžete zobrazit pomocí [Průzkumník služby Azure Storage](https://azurestorageexplorer.codeplex.com/) nebo podobného nástroje. Můžete také exportovat data z ní.

## <a name="use-microsoft-flow-with-table-storage-optional"></a>Použití Microsoft Flow s tabulkovým úložištěm (*volitelné*)

[Microsoft Flow](https://docs.microsoft.com/flow/) můžete použít k automatickému odesílání oznámení při přidání zájemce do úložiště tabulky. Pokud nemáte účet Microsoft Flow, [Zaregistrujte si bezplatný účet](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Příklad oznámení zájemce

Tento příklad ukazuje, jak vytvořit základní tok. Tok automaticky pošle e-mailové oznámení, když se do úložiště tabulek přidají Noví zájemci.

1. Přihlaste se ke svému účtu Microsoft Flow.
1. V navigačním podokně na levé straně vyberte **Moje toky**.
1. V horním navigačním panelu vyberte **+ Nový**.  
1. Z rozevíracího seznamu vyberte **+ vytvořit z prázdné**.
1. V části **vytvořit tok z prázdné**vyberte **vytvořit z prázdné**.

   ![Vytvořit nový tok z prázdného](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

1. Na stránce pro vyhledávání konektorů a triggerů vyberte **triggery**.
1. V části **triggery**vyberte **opakování**.
1. V okně **opakování** ponechte výchozí nastavení **1** pro **interval**. V rozevíracím seznamu **frekvence** vyberte **hodina**.

   >[!NOTE] 
   >V tomto příkladu je použit interval s jednou hodinou. Můžete ale vybrat interval a frekvenci, které nejlépe vyhovuje vašim obchodním potřebám.

   ![Nastavení četnosti 1 hodiny pro opakování](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

1. Vyberte **+ Nový krok**.
1. Vyhledejte možnost **získat čas**a v části **Zvolte akci**vyberte **získat uplynulý čas** .

    ![Vyhledejte a vyberte akci "získat čas v minulosti".](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

1. V okně **načíst minulý čas** nastavte **interval** na **1**.  V rozevíracím seznamu **Časová jednotka** vyberte **hodina**.
    >[!IMPORTANT] 
    >Ujistěte se, že **interval** a **Časová jednotka** odpovídají intervalu a frekvenci, kterou jste nakonfigurovali pro opakování (krok 8).

    ![Nastavení intervalu Get uplynulého času](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >Tok můžete kdykoli zkontrolovat, abyste ověřili, že je správně nakonfigurován každý krok: z řádku nabídek Flow vyberte možnost **Kontrola toku** .

V další sadě kroků se připojíte k tabulce úložiště a nastavíte logiku zpracování pro zpracování nových potenciálních zákazníků.

1. Po kroku **získat čas** vyberte **+ Nový krok**a pak vyhledejte **získat entity**.
1. V části **Akce**vyberte **získat entity**a potom vyberte **Zobrazit upřesňující možnosti**.
1. V okně **získat entity** vyplňte následující pole:

   - **Tabulka**: název úložiště tabulky. Následující obrázek ukazuje zadání "MarketPlaceLeads":

     ![Vyberte vlastní hodnotu pro název tabulky Azure.](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **Dotaz filtru**: Když vyberete toto pole, zobrazí se v automaticky otevíraném okně ikona **získat čas v minulosti** . Vyberte **čas v čase** , kdy se má tato hodnota použít jako časové razítko pro filtrování dotazu. Nebo můžete do pole vložit následující funkci:
   
      `CreatedTime Timestamp gt '@{body('Get_past_time')}'` 

     ![Nastavení funkce dotazu filtru](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

1. Vyberte **Nový krok** a přidejte podmínku pro kontrolu úložiště tabulek pro nové zájemce.

   ![Pomocí příkazu "nový krok" přidáte podmínku pro skenování úložiště tabulek](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

1. V okně **Zvolte akci** vyberte **Akce**a pak vyberte **řízení podmínek**.

     ![Přidat ovládací prvek podmínky](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

1. V okně **Podmínka** vyberte možnost **zvolit hodnotu**a v automaticky otevíraném okně vyberte **výraz** .
1. Vložte `length(body('Get_entities')?['value'])` do pole ***FX*** . Pro přidání této funkce vyberte **OK** . 



     ![Přidání funkce do podmínky](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

1. Nastavte akci, která se má provést na základě výsledku podmínky.

    1. Příkaz Select **je větší než** z rozevíracího seznamu.
   1. Jako hodnotu zadejte **0** .

     ![Nastavení akce na základě výsledků podmínky](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

1. Pokud se podmínka přeloží na "Pokud ne", neprovádějte nic.

    Pokud se podmínka přeloží na "Pokud ano", aktivuje se akce, která připojí váš účet Office 365 k odeslání e-mailu:
   1. Vyberte **Přidat akci**.
   1. Vyberte **Odeslat e-mail**.
   1. V okně **Odeslat e-mail** zadejte informace do následujících polí:

      - **Komu**: e-mailová adresa pro všechny uživatele, kteří obdrží oznámení.
      - **Předmět**: předmět e-mailu. Například: *Noví zájemci!*
      - **Tělo**: text, který chcete zahrnout do každého e-mailu (volitelné). Vložte také `body('Get_entities')?['value']` jako funkci pro vložení informací o zájemci.

        >[!NOTE] 
        >Do těla e-mailu můžete vložit další statické nebo dynamické datové body.

      ![Nastavení e-mailu pro oznámení zájemce](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

1. Uložte tok výběrem možnosti **Uložit**. Microsoft Flow automaticky otestuje chyby. Pokud nedojde k žádným chybám, tok začne běžet po jeho uložení.

    Následující obrázek ukazuje příklad toho, jak by měl konečný tok vypadat.

    [![Finální sekvence toků](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

    (*Vyberte obrázek, který chcete zvětšit.*)

### <a name="manage-your-flow"></a>Správa toku

Po spuštění můžete tok snadno spravovat. Máte plnou kontrolu nad vaším tokem. Můžete ho například zastavit, upravit, zobrazit historii spuštění a získat analýzy. Následující obrázek znázorňuje možnosti správy toku.

 ![Možnosti správy toku](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

Tok zůstane spuštěný, dokud nevyberete možnost **vypnout tok**.

Pokud nebudete dostávat žádná e-mailová oznámení o potenciálních zákaznících, do úložiště tabulek se nepřidaly žádní noví zájemci.
Pokud dojde k selhání toku, dostanete e-mail jako v následujícím příkladu:

 ![E-mailové oznámení o selhání toku](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>Další kroky

[Konfigurace potenciálních zákazníků](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)

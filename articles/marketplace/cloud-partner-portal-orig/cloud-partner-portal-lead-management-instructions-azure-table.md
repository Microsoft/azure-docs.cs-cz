---
title: Úložiště azure table | Azure Marketplace
description: Konfigurace správy potenciálních zákazníků v úložišti Azure Table.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: dsindona
ms.openlocfilehash: cee28c65b97d4ac163f78b746dc7be9ee210843c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280350"
---
# <a name="lead-management-instructions-for-table-storage"></a>Pokyny pro správu zájemců pro úložiště tabulek

Tento článek popisuje, jak nakonfigurovat úložiště azure table pro správu prodejních zájemců. Úložiště tabulek pomáhá ukládat a upravovat informace o zákaznících.

## <a name="configure-table-storage"></a>Konfigurace úložiště tabulky

1. Pokud nemáte účet Azure, [vytvořte si bezplatný zkušební účet](https://azure.microsoft.com/pricing/free-trial/).
1. Po aktivním účtu se přihlaste na [portál Azure](https://portal.azure.com).
1. Na webu Azure Portal postupujte takto:  
    1. Vyberte **+Vytvořit prostředek** v podokně na levé straně. Otevře se podokno **Nové.**
    1. V podokně **Nový** vyberte **Možnost Úložiště**. Na pravé straně se otevře seznam **Doporučené.**
    1. Vyberte **účet úložiště**. Potom postupujte podle pokynů na [vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Vytvoření účtu úložiště Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    Další informace o účtech úložiště najdete [v tématu Úvodní kurzy .](https://docs.microsoft.com/azure/storage/) Informace o cenách najdete v [tématu Ceny za úložiště Azure](https://azure.microsoft.com/pricing/details/storage/).

1. Počkejte, až se zřídí váš účet úložiště, což obvykle trvá několik minut. Potom přístup k účtu z domovské stránky portálu Azure: Vyberte **Zobrazit všechny prostředky** nebo všechny **prostředky** v navigačním podokně.

    ![Přístup k účtu úložiště Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

1. Z podokna účtu úložiště zkopírujte připojovací řetězec účtu úložiště pro klíč. Vložte ji do pole **Připojovací řetězec** pro účet úložiště na portálu partnerů cloudu.

    Příklad připojovacího řetězce:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

      ![Klíč úložiště Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Data v úložišti tabulky můžete zobrazit pomocí [Průzkumníka úložiště Azure](https://azurestorageexplorer.codeplex.com/) nebo podobného nástroje. Můžete také exportovat data z něj.

## <a name="use-microsoft-flow-with-table-storage-optional"></a>Použití Microsoft Flow s úložištěm tabulek *(volitelné)*

[Microsoft Flow](https://docs.microsoft.com/flow/) můžete použít k automatickému odesílání oznámení při přidání zájemce do úložiště tabulky. Pokud nemáte účet Microsoft Flow, [zaregistrujte si bezplatný účet](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Příklad oznámení o potenciálních zákazníků

Tento příklad ukazuje, jak vytvořit základní tok. Tok automaticky odešle e-mailové oznámení každou hodinu, když jsou do úložiště tabulky přidáni noví zájemci.

1. Přihlaste se ke svému účtu Microsoft Flow.
1. V navigačním podokně na levé straně vyberte **možnost Moje toky**.
1. Na horním navigačním panelu vyberte **+Nový**.  
1. V rozevíracím seznamu vyberte **možnost +Vytvořit z prázdného souboru**.
1. V části **Vytvořit tok z prázdného**vyberte **Vytvořit z prázdného**.

   ![Vytvoření nového toku z prázdného](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

1. Na vyhledávací stránce konektorů a aktivačních událostí vyberte **možnost Aktivační události**.
1. V části **Triggers**vyberte **Opakování**.
1. V okně **Opakování** zachovat výchozí nastavení **1** pro **interval**. V rozevíracím seznamu **Frekvence** vyberte **hodina**.

   >[!NOTE] 
   >Tento příklad používá hodinový interval. Můžete si však vybrat interval a frekvenci, která nejlépe vyhovuje vašim obchodním potřebám.

   ![Nastavení 1hodinové frekvence pro opakování](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

1. Vyberte **+Nový krok**.
1. Vyhledejte **možnost Získat minulý čas**a v části Zvolit akci vyberte **Možnost** **Získat minulý čas** .

    ![Vyhledání a výběr akce "získat čas"](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

1. V okně **Získat za čas** nastavte **interval** na **1**.  V rozevíracím seznamu **Jednotka času** vyberte **Hodina**.
    >[!IMPORTANT] 
    >Ujistěte se, že interval **a** **čas jednotka** odpovídá intervalu a frekvence, které jste nakonfigurovali pro opakování (krok 8).

    ![Nastavení časového intervalu získání přes](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >Tok můžete kdykoli zkontrolovat a ověřit, zda je každý krok správně nakonfigurován: Vyberte **kontrolu toku** z panelu nabídek Tok.

V další sadě kroků se připojíte k tabulce úložiště a nastavíte logiku zpracování pro zpracování nových zájemců.

1. Po kroku **Získat čas** vyberte **+Nový krok**a **vyhledejte entity Get**.
1. V části **Akce**vyberte **Získat entity**a pak vyberte **Zobrazit upřesňující možnosti**.
1. V okně **Získat entity** vyplňte následující pole:

   - **Tabulka**: název úložiště tabulky. Následující obrázek znázorňuje "MarketPlaceLeads" zadán:

     ![Výběr vlastní hodnoty pro název tabulky Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **Filtrační dotaz**: Když vyberete toto pole, zobrazí se v rozbalovacím okně ikona **Získat minulý čas.** Chcete-li tuto hodnotu použít jako časové razítko k filtrování dotazu, vyberte **možnost Minulý čas.** Nebo můžete do pole vložit následující funkci:
   
      `CreatedTime Timestamp gt datetime'@{body('Get_past_time')}'` 

     ![Nastavení funkce dotazu filtru](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

1. Výběrem **možnosti Nový krok** přidáte podmínku pro prohledávací úložiště tabulky pro nové zájemce.

   ![Přidání podmínky pro prohledávací úložiště tabulky pomocí "Nový krok"](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

1. V okně **Zvolit akci** vyberte **Akce**a pak vyberte Ovládací **prvek podmínky**.

     ![Přidání ovládacího prvku podmínky](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

1. V okně **Podmínka** vyberte **Vybrat hodnotu**a pak v rozbalovacím okně vyberte **Výraz.**
1. Vložit `length(body('Get_entities')?['value'])` do ***fx*** pole. Chcete-li přidat tuto funkci, vyberte **ok.** 



     ![Přidání funkce do podmínky](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

1. Nastavte akci, která má být vykonat, na základě výsledku podmínky.

    1. Výběr **je větší než** v rozevíracím seznamu.
   1. Jako hodnotu zadejte **hodnotu 0.**

     ![Nastavení akce na základě výsledků podmínky](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

1. Pokud se podmínka vyřeší na "Pokud ne", neprovázte nic.

    Pokud se podmínka vyřeší na "Pokud ano", aktivujte akci, která propojuje váš účet Office 365 a pošle e-mail:
   1. Vyberte **Přidat akci**.
   1. Vyberte **Odeslat e-mail**.
   1. V okně **Odeslat e-mail** zadejte informace do následujících polí:

      - **Komu**: e-mailová adresa pro každého, kdo dostane oznámení.
      - **Předmět**: předmět pro e-mail. Například: *Noví zájemci!*
      - **Text**: text, který chcete zahrnout do každého e-mailu (nepovinné). Také vložit `body('Get_entities')?['value']` jako funkci pro vložení informací o zájemce.

        >[!NOTE] 
        >Do těla e-mailu můžete vložit další statické nebo dynamické datové body.

      ![Nastavení e-mailu pro oznámení o potenciálních zákazníků](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

1. Uložte tok výběrem možnosti **Uložit**. Microsoft Flow bude automaticky testovat chyby. Pokud nejsou žádné chyby, tok se spustí po uložení.

    Následující obrázek ukazuje příklad, jak by měl vypadat konečný tok.

    [![Konečná sekvence průtoku](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

    (*Vyberte obrázek, který chcete zvětšit.)*

### <a name="manage-your-flow"></a>Správa toku

Po spuštění je snadné řídit tok. Máte úplnou kontrolu nad svým tokem. Můžete ji například zastavit, upravit, zobrazit historii běhu a získat analýzy. Následující obrázek znázorňuje možnosti správy toku.

 ![Možnosti řízení toku](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

Tok běží, dokud nevyberete **možnost Vypnout tok**.

Pokud nedostáváte žádná e-mailová oznámení pro potenciální zákazníky, nebyli do úložiště tabulky přidáni žádní noví zájemci.
Pokud dojde k selhání toku, dostanete e-mail jako v následujícím příkladu:

 ![E-mailové oznámení o selhání toku](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>Další kroky

[Konfigurace potenciálních zákazníků](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)

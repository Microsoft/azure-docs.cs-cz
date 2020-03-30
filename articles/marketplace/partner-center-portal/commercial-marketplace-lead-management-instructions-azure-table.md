---
title: Tabulka Azure | Azure Marketplace
description: Konfigurace správy potenciálních zákazníků pro Azure Table.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dsindona
ms.openlocfilehash: f511a60b533d6d1e0b1ae8847d0ee0fb6be3500c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288831"
---
# <a name="configure-lead-management-using-an-azure-table"></a>Konfigurace správy potenciálních zákazníků pomocí tabulky Azure

Pokud váš systém správy vztahů se zákazníky (CRM) není explicitně podporován v Centru partnerů pro příjem zájemců Azure Marketplace a AppSource, můžete k zpracování těchto zájemců použít tabulku Azure. Potom můžete data exportovat a importovat je do systému CRM. Pokyny v tomto článku vás provede procesem vytvoření účtu Azure Storage a tabulky Azure pod tímto účtem. Kromě toho můžete pomocí Microsoft Flow vytvořit nový tok a odeslat e-mailové oznámení, když vaše nabídka obdrží zájemce.

## <a name="configure-azure-table"></a>Konfigurace tabulky Azure

1. Pokud nemáte účet Azure, můžete [si vytvořit bezplatný zkušební účet](https://azure.microsoft.com/pricing/free-trial/).
2. Až bude váš účet Azure aktivní, přihlaste se na [portál Azure](https://portal.azure.com).
3. Na webu Azure Portal vytvořte účet úložiště pomocí následujícího postupu.  
    1. V levém řádku nabídek vyberte **+Vytvořit zdroj.**  Vpravo se zobrazí podokno **Nový** (okno).
    2. V **novém** podokně vyberte **Úložiště.**  Vpravo se zobrazí seznam **Doporučené.**
    3. Chcete-li zahájit vytváření účtu, vyberte **účet úložiště.**  Postupujte podle pokynů v článku [Vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

        ![Postup vytvoření účtu úložiště Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        Další informace o účtech úložiště získáte výběrem [možnosti Úvodní kurz .](https://docs.microsoft.com/azure/storage/)  Další informace o cenách úložiště najdete v [tématu ceny za úložiště](https://azure.microsoft.com/pricing/details/storage/).

4. Počkejte, až se zřídí váš účet úložiště, proces, který obvykle trvá několik minut.  Pak přistupte ke svému účtu úložiště z **domovské** stránky portálu Azure výběrem **Zobrazit všechny vaše prostředky** nebo výběrem všechny **prostředky** z levého navigačního panelu nabídky na portálu Azure.

    ![Přístup k účtu úložiště Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

5. V podokně účtu úložiště vyberte **přístupové klíče** a zkopírujte hodnotu *připojovacího řetězce* pro klíč. Uložte tuto hodnotu, protože se jedná o hodnotu *připojovacího řetězce účtu úložiště,* kterou budete muset zadat na portálu pro publikování, abyste získali zájemce pro vaši nabídku marketplace. 

    Příkladem připojení sting je:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

    ![Klíč úložiště Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

6. V podokně účtu úložiště vyberte **Tabulky** a vyberte **+Tabulka** pro vytvoření tabulky. Zadejte název tabulky a vyberte **OK**. Tuto hodnotu uložte tak, jak ji budete potřebovat, pokud chcete nakonfigurovat Tok MS pro příjem e-mailových oznámení při přijetí zájemců.

    ![Tabulky Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    Data v tabulce úložiště můžete zobrazit pomocí [Průzkumníka úložiště Azure](https://archive.codeplex.com/?p=azurestorageexplorer) nebo jiného nástroje. Data můžete také exportovat v tabulce Azure. 

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>(Nepovinné) Použití Microsoft Flow s tabulkou Azure  

[Microsoft Flow](https://docs.microsoft.com/flow/) můžete použít k automatizaci oznámení při každém přidání zájemce do tabulky Azure. Pokud účet nemáte, můžete si [zaregistrovat bezplatný účet](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Příklad oznámení o potenciálních zákazníků

Tento příklad použijte jako vodítko k vytvoření jednoduchého toku, který automaticky odešle e-mailové oznámení při přidání nového zájemce do tabulky Azure. Tento příklad nastaví opakování pro odesílání informací o zájemcích každou hodinu, pokud je aktualizováno úložiště tabulky.

1. Přihlaste se ke svému účtu Microsoft Flow.
2. Na levém navigačním panelu vyberte **Moje toky**.
3. Na horním navigačním panelu vyberte **+ Nový**.  
4. V rozevíracím seznamu vyberte **+ Naplánované - z prázdné**

   ![Moje toky **+ Naplánováno - z prázdné**](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

5.  V *sestavení naplánovaného toku* okna v části *Opakovat každý* výběr "1" pro interval a "hodina" pro frekvenci. Také uveďte název toku, pokud se vám líbí. Vyberte **Vytvořit**.

    >[!Note]
    >I když tento příklad používá interval 1 hodin, můžete vybrat interval a frekvenci, která je nejlepší pro vaše obchodní potřeby.

    ![Vytvořte naplánovaný tok.](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

6. Vyberte **+ Nový krok**.
7. V části Zvolit okno *akce* vyhledejte "získat čas" a pak v části Akce vyberte **Získat čas.**

   ![Zvolte akci.](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

8. V okně **Získat za čas** nastavte **interval** na 1. V rozevíracím seznamu **Jednotky času** vyberte **Hodina**.

    >[!Important]
    >Ujistěte se, že tato jednotka intervalu a času odpovídá intervalu a frekvenci, které jste nakonfigurovali pro opakování v kroku 5.

    ![Nastavit časový interval získání](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

>[!TIP] 
>Tok můžete kdykoli zkontrolovat a ověřit, zda je každý krok správně nakonfigurován. Chcete-li zkontrolovat tok, vyberte **kontrolu toku** na řádku nabídek Tok.

V další sadě kroků se připojíte k tabulce Azure a nastavíte logiku zpracování pro zpracování nových zájemců.

9. Po kroku Získat čas vyberte **+ Nový krok**a v okně *Zvolte akci* vyhledejte výraz "Získat entity".
10. V části **Akce**vyberte **Získat entity (Azure Table Storage)**.
11. V okně **Azure Table Storage** zadejte informace pro následující pole a vyberte **Vytvořit**:

    * *Název připojení* – zadejte smysluplný název pro připojení, které vytváříte mezi tímto tokem a azure table.
    * *Název účtu úložiště* – zadejte název účtu úložiště pro tabulku Azure. Najdete ji na stránce **přístupových klíčů** účtu úložiště.
    * *Sdílený klíč úložiště* – zadejte hodnotu klíče pro váš účet úložiště pro vaši tabulku Azure. Najdete ji na stránce **přístupových klíčů** účtu úložiště.

        ![Azure Table storage.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

    Po kliknutí na tlačítko Vytvořit se zobrazí okno *Získat entity.* Zde vyberte **Zobrazit upřesňující možnosti** a poskytněte informace pro následující pole:

       * *Tabulka* – vyberte název úložiště tabulek Azure (od kroku 6 pokynů ke konfiguraci tabulky Azure). Další snímek obrazovky zobrazí výzvu, když je vybrána tabulka "marketplaceleads" v tomto příkladu.

            ![Azure Table získat entity.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

        * *Filtrovat dotaz* - Vyberte toto pole a vložte tuto funkci do pole:`Timestamp gt datetime'@{body('Get_past_time')}'`

            ![Azure Table získat entity – filtr Querry.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

12. Teď, když jste dokončili nastavení připojení k tabulce Azure, vyberte **Nový krok** a přidejte podmínku pro skenování tabulky Azure pro nové zájemce. 

13. V okně **Zvolit akci** vyberte **Akce**a pak vyberte ovládací **prvek Podmínka**.

    ![Tabulka Azure – zvolte akci.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

14. V okně **Podmínka** vyberte pole **Zvolte hodnotu** a pak vyberte **Výraz** v vyskakovacím okně.

15. Vložit `length(body('Get_entities')?['value'])` do ***fx*** pole. Chcete-li přidat tuto funkci, vyberte **ok.** 

16. Dokončení nastavení podmínky:
    1. V rozevíracím seznamu vyberte možnost "je větší než".
    2. Jako hodnotu zadejte hodnotu 0.

        ![Azure Tabulka – podmínka.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

V několika dalších krocích nastavíte akci, která má být přijata na základě výsledku podmínky.

* Pokud se podmínka vyřeší na **Pokud ne**, neprovázte nic.
* Pokud se podmínka vyřeší na **Pokud ano**, aktivujte akci, která propojuje váš účet Office 365 a pošle e-mail. 

17. V části **Pokud ano**, **vyberte Přidat akci.**

    ![Azure Table – podmínka, **Pokud ano**.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

18. Vyberte **Odeslat e-mail (Office 365 Outlook)**.

    ![Tabulka Azure – podmínka, **Pokud ano**, odešlete e-mail.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!Note]
    >Pokud chcete použít jiného poskytovatele e-mailu, vyhledejte a místo toho jako akci vyberte Odeslat e-mailové oznámení (E-mail). Pokyny vám ukážou, jak nastavit pomocí Office 365 Outlook, ale pokyny jsou podobné pro jiného poskytovatele e-mailu.

19. V okně **Office 365 Outlook** zadejte informace o následujících polích:

    1. **Chcete-li** - Zadejte e-mailovou adresu pro každého, kdo dostane toto oznámení.
    1. **Předmět** - Zadejte předmět pro e-mail. Například: Noví zájemci!
    1. **Text** – Přidejte text, který chcete zahrnout do každého e-mailu (volitelné), a potom vložte do těla `body('Get_entities')?['value']`.

    >[!Note]
    >Do těla tohoto e-mailu můžete vložit další statické nebo dynamické datové body.

    ![Tabulka Azure – podmínka, **Pokud ano**, okno Office 365 Outlook.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

20. Uložte tok výběrem možnosti **Uložit**. Microsoft Flow automaticky otestuje tok chyb. Pokud nejsou žádné chyby, tok se spustí po uložení.

Další snímek obrazovky ukazuje příklad, jak by měl vypadat konečný tok.

![Příklad konečného toku.](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Správa toku

Správa toku po jeho spuštění je snadná. Máte úplnou kontrolu nad svým tokem. Můžete ji například zastavit, upravit, zobrazit historii běhu a získat analýzy. Další snímek obrazovky zobrazuje možnosti, které jsou k dispozici pro správu toku. 

 ![Správa toku](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

Tok běží, dokud jej nezastavíte pomocí možnosti **Vypnout tok.**

Pokud nedostáváte žádná e-mailová oznámení pro potenciální zákazníky, znamená to, že noví zájemci nebyli přidáni do tabulky Azure. Pokud dojde k selhání toku, dostanete e-mail jako v příkladu v dalším zachycení obrazovky.

 ![E-mailové oznámení o selhání toku](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Konfigurace nabídky pro odesílání zájemců do tabulky Azure

Až budete připraveni nakonfigurovat informace o správě zájemců pro vaši nabídku na portálu pro publikování, postupujte podle následujících kroků:

1. Přejděte na stránku **Nastavení nabídky** pro vaši nabídku.
2. V části Správa zájemců vyberte **Připojit.**
3. V rozbalovacím okně Podrobnosti o připojení vyberte **Azure Table** pro **cíl zájemce**a vložte do připojovacího řetězce z účtu úložiště Azure, který jste vytvořili podle předchozích kroků do pole **připojovacího řetězce účtu úložiště.**
4. Vyberte **Uložit**. 

>[!Note]
>Před přijetím zájemců o nabídku je nutné dokončit konfiguraci zbývající nabídky a publikovat ji.

Když jsou vygenerováni zájemci, Microsoft odesílá zájemce do tabulky Azure. Pokud jste nakonfigurovali tok, bude na e-mailovou adresu, kterou jste nakonfigurovali, odeslán také e-mail.

![Správa potenciálních zákazníků](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![Správa zájemců – podrobnosti o připojení](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![Správa zájemců – účet úložiště podrobností o připojení](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)


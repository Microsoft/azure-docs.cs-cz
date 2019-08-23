---
title: Tabulka Azure | Azure Marketplace
description: Konfigurace správy zájemců pro Azure Table
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: evansma
ms.openlocfilehash: 7151be3ac9f55825fd2e9dde35c9afda6a30726a
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902634"
---
# <a name="configure-lead-management-using-an-azure-table"></a>Konfigurace správy potenciálních zákazníků pomocí tabulky Azure

Pokud se v partnerském centru pro příjem Azure Marketplacech a AppSourcech potenciálních zákazníků nepodporovaný systém pro správu vztahů se zákazníky (CRM), můžete k obsluze těchto zájemců použít tabulku Azure. Pak můžete exportovat data a importovat je do systému CRM. Pokyny v tomto článku vás provedou procesem vytvoření účtu Azure Storage a tabulky Azure pod tímto účtem. Kromě toho můžete vytvořit nový tok pomocí Microsoft Flow k odeslání e-mailového oznámení, když vaše nabídka dostane zájemce.

## <a name="configure-azure-table"></a>Konfigurace tabulky Azure

1. Pokud nemáte účet Azure, můžete si [vytvořit bezplatný zkušební účet](https://azure.microsoft.com/pricing/free-trial/).
2. Po aktivním účtu Azure se přihlaste k [Azure Portal](https://portal.azure.com).
3. V Azure Portal vytvořte pomocí následujícího postupu účet úložiště.  
    1. V levém panelu nabídek vyberte **+ vytvořit prostředek** .  **Nové** podokno (okno) se zobrazí vpravo.
    2. V podokně **Nový** vyberte **úložiště** .  Na pravé straně se zobrazí **vybraný** seznam.
    3. Vyberte **účet úložiště** a začněte vytvářet účet.  Postupujte podle pokynů v článku [Vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

        ![Postup vytvoření účtu úložiště Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        Další informace o účtech úložiště získáte po výběru [kurzu rychlý Start](https://docs.microsoft.com/azure/storage/).  Další informace o cenách služby Storage najdete v tématu [ceny služby Storage](https://azure.microsoft.com/pricing/details/storage/).

4. Počkejte, než se zřídí účet úložiště, což je proces, který obvykle trvá několik minut.  Pak k účtu úložiště přistupujete z **domovské** stránky Azure Portal výběrem možnosti **Zobrazit všechny prostředky** nebo výběrem možnosti **všechny prostředky** v levém navigačním panelu Azure Portal.

    ![Přístup k účtu služby Azure Storage](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

5. V podokně účtu úložiště vyberte **přístupové klíče** a zkopírujte hodnotu připojovacího *řetězce* pro tento klíč. Tuto hodnotu uložte, protože se jedná o hodnotu *připojovacího řetězce účtu úložiště* , kterou budete muset zadat na portálu pro publikování a získat zájemce pro vaši nabídku na webu Marketplace. 

    Příkladem připojení Sting je:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

    ![Klíč úložiště Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

6. V podokně účtu úložiště vyberte **tabulky** a vyberte **+ tabulka** pro vytvoření tabulky. Zadejte název tabulky a vyberte **OK**. Tuto hodnotu uložte, protože ji budete potřebovat, pokud chcete nakonfigurovat MS flow, který bude dostávat e-mailová oznámení při přijetí zájemců.

    ![Tabulky Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    K zobrazení dat v tabulce úložiště můžete použít [Průzkumníka služby Azure Storage](https://archive.codeplex.com/?p=azurestorageexplorer) nebo jakýkoli jiný nástroj. Data můžete také exportovat v tabulce Azure. 

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>Volitelné Použití Microsoft Flow s tabulkou Azure  

[Microsoft Flow](https://docs.microsoft.com/flow/) můžete použít k automatizaci oznámení pokaždé, když se do tabulky Azure přidá zájemce. Pokud účet nemáte, můžete si [zaregistrovat bezplatný účet](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Příklad oznámení zájemce

Tento příklad slouží jako Průvodce vytvořením jednoduchého toku, který automaticky pošle e-mailové oznámení při přidání nového zájemce do tabulky Azure. V tomto příkladu se nastaví opakování pro odeslání informací o vedoucích každé hodiny, pokud se aktualizuje tabulka úložiště.

1. Přihlaste se k účtu Microsoft Flow.
2. V levém navigačním panelu vyberte **Moje toky**.
3. V horním navigačním panelu vyberte **+ Nový**.  
4. V rozevíracím seznamu vyberte **+ naplánované – od prázdné** .

   ![Moje toky * * + naplánované – od prázdné * *](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

5.  V okně *sestavit naplánovaný tok* v části *opakovat každé* vyberte 1 pro interval a hodiny pro četnost. Pokud chcete, pojmenujte tok také. Vyberte **Vytvořit**.

    >[!Note]
    >I když tento příklad používá interval 1 hodiny, můžete vybrat interval a frekvenci, které nejlépe vyhovuje vašim obchodním potřebám.

    ![Sestavte naplánovaný tok.](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

6. Vyberte **+ Nový krok**.
7. V okně *Zvolte akci* vyhledejte "získat čas a" a potom v části Akce vyberte **získat uplynulý čas** .

   ![Vyberte akci.](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

8. V okně **načíst minulý čas** nastavte **interval** na 1. V rozevíracím seznamu **Časová jednotka** vyberte **hodina**.

    >[!Important]
    >Ujistěte se, že tento interval a Časová jednotka odpovídají intervalu a četnosti, kterou jste nakonfigurovali pro opakování v kroku 5.

    ![Nastavit interval získání minulého časového intervalu](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

>[!TIP] 
>Tok můžete kdykoli zkontrolovat, abyste ověřili, jestli je každý krok správně nakonfigurovaný. Pokud chcete tok kontrolovat, vyberte v řádku nabídek Flow možnost **Kontrola toku** .

V další sadě kroků se připojíte ke své tabulce Azure a nastavíte logiku zpracování pro zpracování nových potenciálních zákazníků.

9. Po kroku získat čas vyberte **+ Nový krok**a potom v okně *Zvolte akci* vyhledejte "získat entity".
10. V části **Akce**vyberte **získat entity (Azure Table Storage)** .
11. V okně **Azure Table Storage** zadejte informace o následujících polích a vyberte **vytvořit**:

    * *Název připojení* – zadejte smysluplný název pro připojení, které nakonfigurujete mezi tímto tokem a tabulkou Azure.
    * *Název účtu úložiště* – zadejte název účtu úložiště pro tabulku Azure. Najdete ho na stránce **přístupové klíče** účtu úložiště.
    * *Klíč sdíleného úložiště* – zadejte hodnotu klíče pro váš účet úložiště pro tabulku Azure. Najdete ho na stránce **přístupové klíče** účtu úložiště.

        ![Úložiště tabulek Azure.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

    Po kliknutí na vytvořit se zobrazí okno *získat entity* . Tady vyberte **Zobrazit pokročilé možnosti** a zadejte informace pro následující pole:

       * *Tabulka* – vyberte název Azure Table Storage (z kroku 6 pokynů ke konfiguraci tabulky Azure). V dalším snímku obrazovky se zobrazí výzva, když je pro tento příklad Vybraná tabulka "marketplaceleads".

            ![Entity Azure Table Get](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

        * *Filtrovat dotaz* – toto pole vyberte a vložte tuto funkci do pole:`Timestamp gt datetime'@{body('Get_past_time')}'`

            ![Entity Azure Table Get – filtr Querry.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

12. Když jste už nastavili připojení k tabulce Azure, vyberte **Nový krok** a přidejte podmínku pro kontrolu nových potenciálních zákazníků v tabulce Azure. 

13. V okně **Zvolte akci** vyberte **Akce**a potom vyberte **ovládací prvek podmínka**.

    ![Tabulka Azure – vyberte akci.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

14. V okně **Podmínka** vyberte pole **zvolit hodnotu** a potom v místním okně vyberte **výraz** .

15. Vložte `length(body('Get_entities')?['value'])` do pole ***FX*** . Pro přidání této funkce vyberte **OK** . 

16. Dokončení nastavení podmínky:
    1. V rozevíracím seznamu vyberte "je větší než".
    2. Jako hodnotu zadejte 0.

        ![Tabulka Azure – podmínka](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

V několika dalších krocích nastavíte akci, která se má provést, na základě výsledku podmínky.

* Pokud se podmínka vyřeší na **ne**, neprovádějte nic.
* Pokud se podmínka přeloží na **hodnotu Ano**, spustí se akce, která připojí váš účet Office 365 k odeslání e-mailu. 

17. **Pokud ano**, vyberte **přidat akci** .

    ![Azure Table-Condition * * if Yes * *.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

18. Vyberte **Odeslat e-mail (Office 365 Outlook)** .

    ![Azure Table-Condition * * Pokud ano * *, poslat e-mail.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!Note]
    >Pokud chcete použít jiné hledání poskytovatele e-mailu a místo toho vybrat odeslat e-mailové oznámení (E-mail). V pokynech se dozvíte, jak nakonfigurovat pomocí Office 365 Outlooku, ale pokyny jsou podobné pro jiného poskytovatele e-mailu.

19. V okně **Office 365 Outlook** zadejte informace pro následující pole:

    1. Zadejte e-mailovou adresu pro každého, kdo získá toto oznámení.
    1. **Předmět** – zadejte předmět e-mailu. Příklad: Noví zájemci!
    1. **Tělo** – přidejte text, který chcete zahrnout do každého e-mailu (volitelné) a vložte ho do textu `body('Get_entities')?['value']`.

    >[!Note]
    >Do těla tohoto e-mailu můžete vložit další statické nebo dynamické datové body.

    ![Tabulka Azure – podmínka * * if Ano * *, Office 365 Outlook Window.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

20. Vyberte **Uložit** a tok se uloží. Microsoft Flow automaticky otestuje tok a zobrazí chyby. Pokud nedojde k žádným chybám, tok začne běžet po jeho uložení.

Následující snímek obrazovky ukazuje příklad toho, jak by měl konečný tok vypadat.

![Příklad finálního toku.](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Správa toku

Správa toku po jeho spuštění je snadná. Máte plnou kontrolu nad vaším tokem. Můžete ho například zastavit, upravit, zobrazit historii spuštění a získat analýzy. Následující snímek obrazovky zobrazuje možnosti, které jsou k dispozici pro správu toku. 

 ![Správa toku](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

Tok zůstane spuštěný, dokud ho nezastavíte pomocí možnosti **zapnout tok** .

Pokud neobdržíte žádná e-mailová oznámení o potenciálních zákaznících, znamená to, že noví zájemci nebyli přidáni do tabulky Azure. Pokud dojde k výpadkům toků, dostanete e-mail podobný příkladu v následujícím snímku obrazovky.

 ![E-mailové oznámení o selhání toku](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Konfigurace nabídky pro odesílání zájemců do tabulky Azure

Až budete připraveni ke konfiguraci informací o správě zájemce pro vaši nabídku na portálu pro publikování, postupujte podle následujících kroků:

1. Přejděte na stránku **nastavení nabídky** pro vaši nabídku.
2. V části Správa zájemců vyberte **připojit** .
3. V automaticky otevíraném okně Podrobnosti připojení vyberte pro **cíl zájemce** **tabulku Azure** a vložte do připojovacího řetězce z účtu služby Azure Storage, který jste vytvořili pomocí předchozích kroků, do **připojovacího řetězce účtu úložiště** . dílčí.
4. Vyberte **Uložit**. 

>[!Note]
>Musíte dokončit konfiguraci zbytku nabídky a publikovat ji předtím, než budete moct získat zájemce pro tuto nabídku.

Po vygenerování zájemců pošle společnost Microsoft zájemce do tabulky Azure. Pokud jste nakonfigurovali tok, pošle se e-mail na e-mailovou adresu, kterou jste nakonfigurovali.

![Správa potenciálních zákazníků](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![Správa vedoucích – podrobnosti připojení](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![Správa vedoucích – účet úložiště podrobnosti o připojení](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)


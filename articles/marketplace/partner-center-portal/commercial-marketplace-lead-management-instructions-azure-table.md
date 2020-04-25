---
title: Konfigurace vedoucích Microsoftu pro komerční tržiště pomocí tabulky Azure
description: Naučte se používat tabulku Azure ke správě potenciálních zákazníků z Microsoft AppSource a Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 9814b03e348fc807c04364afbf027369f917670a
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82131131"
---
# <a name="configure-lead-management-by-using-an-azure-table"></a>Konfigurace správy potenciálních zákazníků pomocí tabulky Azure

Pokud se v partnerském centru nepodporuje Podniková správa vztahů se zákazníky (CRM), aby přijímala Microsoft AppSource a Azure Marketplace potenciální zákazníky, můžete k těmto zájemcům využít tabulku Azure. Pak můžete exportovat data a importovat je do systému CRM. Pokyny v tomto článku vás provedou procesem vytvoření účtu Azure Storage a tabulky Azure v rámci tohoto účtu. Kromě toho můžete vytvořit nový tok pomocí Power Automata k odeslání e-mailového oznámení, když vaše nabídka dostane zájemce.

## <a name="configure-an-azure-table"></a>Konfigurace tabulky Azure

1. Pokud nemáte účet Azure, můžete si [vytvořit bezplatný zkušební účet](https://azure.microsoft.com/pricing/free-trial/).
1. Po aktivním účtu Azure se přihlaste k [Azure Portal](https://portal.azure.com).
1. V Azure Portal vytvořte účet úložiště pomocí následujícího postupu:

    1. V levém panelu nabídek vyberte **+ vytvořit prostředek** . **Nové** podokno se zobrazí vpravo.
    1. V podokně **Nový** vyberte **úložiště** . Na pravé straně se zobrazí **vybraný** seznam.
    1. Vyberte **účet úložiště** a začněte vytvářet účet. Postupujte podle pokynů v části [Vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

        ![Postup vytvoření účtu úložiště Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        Další informace o účtech úložiště najdete v [kurzu rychlý Start](https://docs.microsoft.com/azure/storage/). Další informace o cenách služby Storage najdete v tématu [ceny služby Storage](https://azure.microsoft.com/pricing/details/storage/).

1. Počkejte, než se zřídí účet úložiště. Tento proces obvykle trvá několik minut. Pak na **domovské** stránce Azure Portal přístup k účtu úložiště výběrem možnosti **Zobrazit všechny vaše prostředky**. Můžete také vybrat **všechny prostředky** z levého řádku nabídky Azure Portal.

    ![Přístup k účtu služby Azure Storage](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

1. V podokně účtu úložiště vyberte **přístupové klíče** a zkopírujte hodnotu **připojovacího řetězce** pro tento klíč. Tuto hodnotu uložte, protože se jedná o hodnotu **připojovacího řetězce účtu úložiště** , kterou budete muset zadat na portálu pro publikování a získat tak zájemce pro vaši nabídku Azure Marketplace. 

    Tady je příklad připojovacího řetězce.

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.screens.net
    ```

    ![Klíč úložiště Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

1. V podokně účtu úložiště vyberte **tabulky**a vyberte **+ tabulka** pro vytvoření tabulky. Zadejte název tabulky a vyberte **OK**. Tuto hodnotu uložte, protože ji budete potřebovat, pokud chcete nakonfigurovat tok, který bude dostávat e-mailová oznámení při přijetí zájemců.

    ![Tabulky Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    Data v tabulce úložiště můžete zobrazit pomocí [Průzkumník služby Azure Storage](https://archive.codeplex.com/?p=azurestorageexplorer) nebo jakéhokoli jiného nástroje. Data můžete také exportovat v tabulce Azure. 

## <a name="optional-use-power-automate-with-an-azure-table"></a>Volitelné Použití Power Automate s tabulkou Azure 

K automatizaci oznámení pokaždé, když se do tabulky Azure přidá zájemce, můžete použít [Power](https://docs.microsoft.com/flow/) automat. Pokud účet nemáte, můžete si [zaregistrovat bezplatný účet](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Příklad oznámení zájemce

Tento příklad slouží jako Průvodce vytvořením jednoduchého toku, který automaticky pošle e-mailové oznámení při přidání nového zájemce do tabulky Azure. V tomto příkladu se nastaví opakování pro odeslání informací o vedoucích každé hodiny, pokud se aktualizuje tabulka úložiště.

1. Přihlaste se ke svému účtu Power automat.
1. Na levém panelu vyberte **Moje toky**.
1. V horním panelu vyberte **+ Nový**. 
1. V rozevíracím seznamu vyberte **+ naplánováno--od prázdné**.

   ![Moje toky + naplánované – od prázdného](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

1. V okně **sestavit naplánovaný tok** pro opakování klikněte u **každého**intervalu a **hodiny** na hodnotu **1** . Pokud chcete, pojmenujte tok také. Vyberte **Vytvořit**.

   >[!NOTE]
   >I když tento příklad používá jednorázové intervaly, můžete vybrat interval a frekvenci, které nejlépe vyhovuje vašim obchodním potřebám.

   ![Vytvoření naplánovaného toku](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

1. Vyberte **+ Nový krok**.
1. V okně **zvolit akci** vyhledejte **získat včas**. Potom v části **Akce**vyberte **získat uplynulý čas**.

   ![Zvolte akci.](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

1. V okně **načíst minulý čas** nastavte **interval** na **1**. V rozevíracím seznamu **Časová jednotka** vyberte **hodina**.

    >[!IMPORTANT]
    >Ujistěte se, že interval a Časová jednotka, kterou jste odeslali v kroku 8, odpovídá intervalu a frekvenci, kterou jste nakonfigurovali pro opakování v kroku 5.

    ![Nastavit interval získání minulého časového intervalu](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

   >[!TIP] 
   >Tok můžete kdykoli zkontrolovat, abyste ověřili, jestli je každý krok správně nakonfigurovaný. Pokud chcete tok kontrolovat, vyberte v řádku nabídek **Flow** možnost **Kontrola toku** .

   V další sadě kroků se připojíte ke své tabulce Azure a nastavíte logiku zpracování pro zpracování nových potenciálních zákazníků.

1. Po kroku 8 vyberte **+ Nový krok**. Pak v okně **Zvolte akci** vyhledejte **entity Get** .
1. V části **Akce**vyberte **získat entity (Azure Table Storage)**.
1. V okně **Azure Table Storage** zadejte informace do následujících polí a vyberte **vytvořit**:

    * **Název připojení**: zadejte smysluplný název pro připojení, které jste navázáni mezi tímto tokem a tabulkou Azure.
    * **Název účtu úložiště**: zadejte název účtu úložiště pro tabulku Azure. Tento název najdete na stránce **přístupové klíče** účtu úložiště.
    * **Klíč sdíleného úložiště**: zadejte hodnotu klíče pro váš účet úložiště pro tabulku Azure. Tuto hodnotu najdete na stránce **přístupové klíče** účtu úložiště.

      ![Okno Azure Table Storage](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

   Po výběru položky **vytvořit**se zobrazí okno **získat entity** . Tady vyberte **Zobrazit upřesňující možnosti**a zadejte informace pro následující pole:

   * **Tabulka**: vyberte název úložiště Azure Table (z kroku 6 pokynů v části Konfigurace tabulky Azure). Následující obrázek ukazuje výzvu, když je pro tento příklad vybrána tabulka "marketplaceleads".

     ![Získat okno s entitami](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

   * **Dotaz filtru**: zaškrtněte toto políčko a vložte tuto funkci do pole:`Timestamp gt datetime'@{body('Get_past_time')}'`

     ![Získat entity, filtrovat pole dotazu](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

1. Když jste už nastavili připojení k tabulce Azure, vyberte **Nový krok** a přidejte podmínku pro kontrolu nových potenciálních zákazníků v tabulce Azure.

1. V okně **Zvolte akci** vyberte **Akce**. Pak vyberte **řízení podmínek**.

    ![Zvolit okno akce](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

1. V okně **Podmínka** vyberte **možnost zvolit hodnotu**. Pak v automaticky otevíraném okně vyberte **výraz** .

1. Vložte `length(body('Get_entities')?['value'])` do pole **FX** . Pro přidání této funkce vyberte **OK** . 

1. Dokončení nastavení podmínky:
    1. Příkaz Select **je větší než** z rozevíracího seznamu.
    2. Jako hodnotu zadejte **0** .

        ![Okno podmínky](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

   V několika dalších krocích jste nastavili akci, která se má provést na základě výsledku podmínky:

   * Pokud se podmínka vyřeší na **ne**, neprovádějte nic.
   * Pokud se podmínka přeloží na **hodnotu Ano**, spustí se akce, která připojí váš účet Office 365 k odeslání e-mailu. 

1. **Pokud ano**, vyberte **přidat akci** .

    ![Okno podmínky, pokud ano, přidat akci](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

1. Vyberte **Odeslat e-mail (Office 365 Outlook)**.

    ![Okno podmínky, pokud ano, odeslat e-mail](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!NOTE]
    >Pokud chcete použít jiného poskytovatele e-mailu, vyhledejte ho a jako akci vyberte **Odeslat e-mailové oznámení (e-mail)** . V pokynech se dozvíte, jak nakonfigurovat pomocí Office 365 Outlooku, ale pokyny jsou podobné pro jiného poskytovatele e-mailu.

1. V okně Office 365 Outlook zadejte informace do následujících polí:

    1. **Komu**: Zadejte e-mailovou adresu pro všechny uživatele, kteří obdrží toto oznámení.
    1. **Předmět**: Zadejte předmět e-mailu. Jedná se například o **nové zájemce.**
    1. **Tělo**: přidejte text, který chcete zahrnout do každého e-mailu (volitelné) a vložte ho do `body('Get_entities')?['value']`.

    >[!NOTE]
    >Do těla tohoto e-mailu můžete vložit další statické nebo dynamické datové body.

    ![Okno podmínky, pokud ano, okno Office 365 Outlook](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

1. Uložte tok výběrem možnosti **Uložit**. Power Automate automaticky testuje tok pro chyby. Pokud nedojde k žádným chybám, tok začne běžet po jeho uložení.

Následující obrázek ukazuje příklad toho, jak by měl konečný tok vypadat.

![Příklad finálního toku](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Správa toku

Správa toku po jeho spuštění je snadná. Máte plnou kontrolu nad vaším tokem. Můžete ho například zastavit, upravit, zobrazit historii spuštění a získat analýzy. Následující obrázek ukazuje možnosti, které jsou k dispozici pro správu toku.

 ![Správa toku](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

Tok zůstane spuštěný, dokud ho nezastavíte pomocí možnosti **zapnout tok** .

Pokud neobdržíte žádná e-mailová oznámení o potenciálních zákaznících, znamená to, že noví zájemci nebyli přidáni do tabulky Azure. Pokud dojde k nějakým chybám toků, dostanete e-mail podobný tomuto příkladu.

 ![E-mailové oznámení o selhání toku](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Konfigurace nabídky pro odesílání zájemců do tabulky Azure

Až budete připraveni ke konfiguraci informací o správě zájemců pro vaši nabídku na portálu pro publikování, postupujte podle těchto kroků.

1. Pro vaši nabídku přejdete na stránku **nastavení nabídek** .
1. V části **Správa zájemců** vyberte **připojit** .
1. V místním okně **Podrobnosti připojení** vyberte pro **cíl zájemce**možnost **tabulka Azure** . Vložte do připojovacího řetězce z účtu služby Azure Storage, který jste vytvořili pomocí předchozích kroků v poli **připojovací řetězec účtu úložiště** .
1. **Kontaktní e-mail**: Zadejte e-maily pro lidi ve vaší společnosti, kteří by měli dostávat e-mailová oznámení při přijetí nového zájemce. Více e-mailů můžete zadat tak, že je oddělíte středníkem.
1. Vyberte **OK**.

Chcete-li se ujistit, že jste úspěšně připojeni k cíli zájemce, klikněte na tlačítko **ověřit** . V případě úspěchu budete mít vedoucího testu v cíli realizace.

>[!NOTE]
>Musíte dokončit konfiguraci zbytku nabídky a publikovat ji předtím, než budete moct získat zájemce pro tuto nabídku.

Po vygenerování zájemců pošle společnost Microsoft zájemce do tabulky Azure. Pokud jste nakonfigurovali tok, pošle se e-mail na e-mailovou adresu, kterou jste nakonfigurovali.

![Správa potenciálních zákazníků](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![Řízení vedoucích, podrobnosti o připojení](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![Správa zájemců, účet úložiště podrobnosti o připojení](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)


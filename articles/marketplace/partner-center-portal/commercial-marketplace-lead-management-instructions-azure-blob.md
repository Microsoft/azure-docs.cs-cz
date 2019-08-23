---
title: Tabulka Azure v programu pro komerční tržiště | Azure Marketplace
description: Konfigurace správy zájemců pro Azure Blob
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: evansma
ms.openlocfilehash: c67855422808f5ec4c81242edcece4e447f2f44f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902361"
---
# <a name="lead-management-instructions-for-azure-blob"></a>Pokyny pro správu informací o službě Azure Blob

>[!Caution]
>Možnost Azure Blob pro zpracování zájemců z nabídky na webu Marketplace je zastaralá. Pokud teď máte nabídku publikovanou s konfigurací správy zájemců pro Azure Blob, nebudete už dostávat zákazníky od zákazníků. Aktualizujte prosím konfiguraci správy zájemců na kteroukoli z dalších možností správy vedoucích zákazníků. Seznamte se s dalšími možnostmi na [úvodní stránce vedoucího řízení](./commercial-marketplace-get-customer-leads.md).

Pokud se v partnerském centru pro příjem Azure Marketplacech a AppSourcech potenciálních zákazníků nepodporovaný systém pro správu vztahů se zákazníky (CRM), můžete tyto zájemce zpracovat pomocí objektu blob Azure. Pak můžete exportovat data a importovat je do systému CRM. Pokyny v tomto článku vám poskytnou postup vytvoření účtu Azure Storage a Azure Blob pod tímto účtem. Kromě toho můžete vytvořit nový tok pomocí Microsoft Flow k odeslání e-mailového oznámení, když vaše nabídka dostane zájemce.


## <a name="how-to-configure-azure-blob"></a>Jak nakonfigurovat Azure Blob

1. Pokud nemáte účet Azure, můžete si [vytvořit bezplatný zkušební účet](https://azure.microsoft.com/pricing/free-trial/).
1. Po aktivním účtu Azure se přihlaste k [Azure Portal](https://portal.azure.com).
1. V Azure Portal vytvořte pomocí následujícího postupu účet úložiště.  
    1. V levém panelu nabídek vyberte **+ vytvořit prostředek** .  **Nové** podokno (okno) se zobrazí vpravo.
    2. V podokně **Nový** vyberte **úložiště** .  Na pravé straně se zobrazí **vybraný** seznam.
    3. Vyberte **účet úložiště** pro zahájení vytváření účtu.  Postupujte podle pokynů v článku [Vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Postup vytvoření účtu úložiště Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Další informace o účtech úložiště získáte po výběru [kurzu rychlý Start](https://docs.microsoft.com/azure/storage/).  Další informace o cenách služby Storage najdete v tématu [ceny služby Storage](https://azure.microsoft.com/pricing/details/storage/).

4. Počkejte, než se zřídí účet úložiště, což je proces, který obvykle trvá několik minut.  Pak k účtu úložiště přistupujete z **domovské** stránky Azure Portal výběrem možnosti **Zobrazit všechny vaše prostředky** nebo výběrem možnosti **všechny prostředky** z levé navigační panelu nabídek Azure Portal.

    ![Přístup k účtu služby Azure Storage](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. V podokně účtu úložiště vyberte **přístupové klíče** a zkopírujte hodnotu připojovacího *řetězce* pro tento klíč. Tuto hodnotu uložte, protože se jedná o hodnotu *připojovacího řetězce účtu úložiště* , kterou budete muset zadat na portálu pro publikování a získat zájemce pro vaši nabídku na webu Marketplace.

     Příkladem připojení Sting je:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Klíč úložiště Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. Na stránce účet úložiště vyberte **objekty blob**.

   ![Klíč úložiště Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. Po výběru na stránce objektů BLOB vyberte tlačítko **+ kontejner** .

8. Zadejte **název** nového kontejneru. Název kontejneru musí obsahovat malá písmena, musí začínat písmenem nebo číslicí a smí obsahovat jenom písmena, číslice a spojovníky (-). Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

    Tuto hodnotu uložte, protože je to hodnota *názvu kontejneru* , kterou musíte poskytnout na portálu pro publikování a získat tak zájemce pro vaši nabídku na webu Marketplace.

9. Nastavte úroveň veřejného přístupu ke kontejneru jako **privátní (bez anonymního přístupu)** .

10. Kliknutím na **OK** kontejner vytvoříte.

    ![Nový kontejner](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-blob"></a>Konfigurace nabídky pro odeslání zájemců do objektu blob Azure

Až budete připraveni ke konfiguraci informací o správě zájemce pro vaši nabídku na portálu pro publikování, postupujte podle následujících kroků:

1. Přejděte na stránku **nastavení nabídky** pro vaši nabídku.
2. V části Správa zájemců vyberte **připojit** .

    ![Připojit nabídku](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. V místním okně Podrobnosti připojení vyberte **Azure Blob** pro cíl zájemce.

    ![Podrobnosti připojení](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Zadejte **název kontejneru** a **připojovací řetězec účtu úložiště** , který jste získali z následujících pokynů.

    * Příklad názvu kontejneru:`marketplaceleadcontainer`
    * Příklad připojovacího řetězce účtu úložiště: `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net`![Podrobnosti o připojení](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Vyberte **Uložit**.

    > [!NOTE]
    > Musíte dokončit konfiguraci zbytku nabídky a publikovat ji předtím, než budete moct získat zájemce pro tuto nabídku.



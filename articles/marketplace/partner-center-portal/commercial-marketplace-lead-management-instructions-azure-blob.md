---
title: Správa zájemců pomocí Azure Blob Storage – Microsoft Commercial Marketplace
description: Naučte se používat Azure Blob ke konfiguraci zájemců pro Microsoft AppSource a Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 05/01/2020
ms.openlocfilehash: bd2f3d40b1aea1c0133f95e069ebfd527d30bd59
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94491099"
---
# <a name="use-azure-blob-storage-to-manage-commercial-marketplace-leads"></a>Použití úložiště objektů BLOB v Azure ke správě zájemců z komerčního tržiště

>[!Caution]
>Podpora komerčního tržiště pro úložiště objektů BLOB v Azure je zastaralá a už není možnost zpracovávat zájemce z vaší nabídky. Pokud teď máte nabídku komerčního tržiště se správou potenciálních zákazníků nakonfigurovanou pro Azure Blob, nebudete už dostávat potenciální zákazníky. Aktualizujte prosím konfiguraci správy zájemců na kteroukoli z dalších možností správy vedoucích zákazníků. Seznamte se s dalšími možnostmi na [úvodní stránce vedoucího řízení](./commercial-marketplace-get-customer-leads.md).

 Pokud se v partnerském centru pro příjem Microsoft AppSource a Azure Marketplace potenciálních zákazníků nepodporuje váš systém správy vztahů se zákazníky (CRM), můžete použít službu Azure Blob Storage. Pak můžete exportovat data a importovat je do systému CRM. Pokyny v tomto článku vás provedou procesem vytvoření účtu Azure Storage a objektu BLOB pod tímto účtem. Navíc můžete vytvořit nový tok pomocí Power Automata k odeslání e-mailového oznámení, když vaše nabídka dostane zájemce.

>[!NOTE]
>Konektor Power automat používaný v těchto pokynech vyžaduje placené předplatné k automatizaci. Než budete postupovat podle pokynů v tomto článku, ujistěte se, že tento účet máte.

## <a name="configure-azure-blob-storage"></a>Konfigurace služby Azure Blob Storage

1. Pokud nemáte účet Azure, můžete si [vytvořit bezplatný zkušební účet](https://azure.microsoft.com/pricing/free-trial/).

2. Po aktivním účtu Azure se přihlaste k [Azure Portal](https://portal.azure.com).

3. V Azure Portal vytvořte pomocí následujícího postupu účet úložiště.  
    1. V levém panelu nabídek vyberte **+ vytvořit prostředek** .  **Nové** podokno (okno) se zobrazí vpravo.
    2. V podokně **Nový** vyberte **úložiště** .  Na pravé straně se zobrazí **vybraný** seznam.
    3. Vyberte **účet úložiště** pro zahájení vytváření účtu.  Postupujte podle pokynů v článku [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md?tabs=azure-portal).

    ![Postup vytvoření účtu úložiště Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Další informace o účtech úložiště najdete v tomto [rychlém](../../storage/blobs/storage-quickstart-blobs-portal.md)startu.  Další informace o cenách služby Storage najdete v tématu [ceny služby Storage](https://azure.microsoft.com/pricing/details/storage/).

4. Počkejte, než se zřídí účet úložiště, což je proces, který obvykle trvá několik minut.  Pak k účtu úložiště přistupujete z **domovské** stránky Azure Portal výběrem možnosti **Zobrazit všechny vaše prostředky** nebo výběrem možnosti **všechny prostředky** z levé navigační panelu nabídek Azure Portal.

    ![Přístup k účtu služby Azure Storage](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. V podokně účtu úložiště vyberte **přístupové klíče** a zkopírujte hodnotu *připojovacího řetězce* pro tento klíč. Tuto hodnotu uložte, protože se jedná o hodnotu *připojovacího řetězce účtu úložiště* , kterou budete muset zadat na portálu pro publikování a získat zájemce pro vaši nabídku na webu Marketplace.

     Příkladem připojovacího řetězce je:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Snímek obrazovky zobrazující stránku přístupové klíče se zvýrazněným textovým polem "připojovací řetězec".](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. Na stránce účet úložiště vyberte **objekty blob**.

   ![Snímek obrazovky se stránkou účtu úložiště Azure s vybranými objekty blob](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. Po výběru na stránce objektů BLOB vyberte tlačítko **+ kontejner** .

8. Zadejte **název** nového kontejneru. Název kontejneru musí obsahovat malá písmena, musí začínat písmenem nebo číslicí a smí obsahovat jenom písmena, číslice a spojovníky (-). Další informace o názvech kontejnerů a objektů BLOB najdete v tématu [pojmenování a odkazování kontejnerů, objektů BLOB a metadat](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

    Tuto hodnotu uložte, protože je to hodnota *názvu kontejneru* , kterou musíte poskytnout na portálu pro publikování a získat tak zájemce pro vaši nabídku na webu Marketplace.

9. Nastavte úroveň veřejného přístupu ke kontejneru jako **privátní (bez anonymního přístupu)**.

10. Kliknutím na **OK** kontejner vytvoříte.

    ![Nový kontejner](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-azure-blob-storage"></a>Konfigurace nabídky pro odesílání zájemců do úložiště objektů BLOB v Azure

Až budete připraveni ke konfiguraci informací o správě zájemce pro vaši nabídku na portálu pro publikování, postupujte podle následujících kroků:

1. Přejděte na stránku **nastavení nabídky** pro vaši nabídku.
2. V části **zájemci pro zákazníky** vyberte **připojit**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-blob/customer-leads.png" alt-text="Potenciální zákazníci":::

3. V místním okně Podrobnosti připojení vyberte **Azure Blob** pro cíl zájemce.

    ![Podrobnosti připojení](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Zadejte **název kontejneru** a **připojovací řetězec účtu úložiště** , který jste získali z následujících pokynů.

    * Příklad názvu kontejneru: `marketplaceleadcontainer`
    * Příklad připojovacího řetězce účtu úložiště: `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![ Podrobnosti o připojení](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Vyberte **Uložit**.

    > [!NOTE]
    > Musíte dokončit konfiguraci zbytku nabídky a publikovat ji předtím, než budete moct získat zájemce pro tuto nabídku.
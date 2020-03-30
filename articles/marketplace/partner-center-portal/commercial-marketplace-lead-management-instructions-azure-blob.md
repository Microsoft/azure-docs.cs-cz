---
title: Azure Table v programu komerčního tržiště| Azure Marketplace
description: Konfigurace správy potenciálních zákazníků pro objekt blob Azure
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: dsindona
ms.openlocfilehash: 062252b007e22fcd2644c8b647fc0ecc2f5938cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285244"
---
# <a name="lead-management-instructions-for-azure-blob"></a>Pokyny pro správu potenciálních zákazníků pro objekt blob Azure

>[!Caution]
>Možnost Azure Blob pro zpracování zájemců z nabídky marketplace byla zastaralá. Pokud máte aktuálně zveřejněnou nabídku s konfigurací správy potenciálních zákazníků pro objekt Blob Azure, už nepřijímáte zákazníky. Aktualizujte konfiguraci správy zájemců na některou z dalších možností správy zájemců. Další informace o dalších možnostech na [vstupní stránce správy zájemců](./commercial-marketplace-get-customer-leads.md).

Pokud váš systém správy vztahů se zákazníky (CRM) není explicitně podporován v Centru partnerů pro příjem zájemců Azure Marketplace a AppSource, můžete k zpracování těchto zájemců použít objekt blob Azure. Potom můžete data exportovat a importovat je do systému CRM. Pokyny v tomto článku vám umožní prostřednictvím procesu vytváření účtu Azure Storage a objektblob Azure pod tímto účtem. Kromě toho můžete pomocí Microsoft Flow vytvořit nový tok a odeslat e-mailové oznámení, když vaše nabídka obdrží zájemce.


## <a name="how-to-configure-azure-blob"></a>Konfigurace objektu blob Azure

1. Pokud nemáte účet Azure, můžete [si vytvořit bezplatný zkušební účet](https://azure.microsoft.com/pricing/free-trial/).
1. Až bude váš účet Azure aktivní, přihlaste se na [portál Azure](https://portal.azure.com).
1. Na webu Azure Portal vytvořte účet úložiště pomocí následujícího postupu.  
    1. V levém řádku nabídek vyberte **+Vytvořit zdroj.**  Vpravo se zobrazí podokno **Nový** (okno).
    2. V **novém** podokně vyberte **Úložiště.**  Vpravo se zobrazí seznam **Doporučené.**
    3. Vyberte **účet úložiště,** který zahájí vytváření účtu.  Postupujte podle pokynů v článku [Vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Postup vytvoření účtu úložiště Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Další informace o účtech úložiště získáte výběrem [možnosti Úvodní kurz .](https://docs.microsoft.com/azure/storage/)  Další informace o cenách úložiště najdete v [tématu ceny za úložiště](https://azure.microsoft.com/pricing/details/storage/).

4. Počkejte, až se zřídí váš účet úložiště, proces, který obvykle trvá několik minut.  Pak přistupte ke svému účtu úložiště z **domovské** stránky portálu Azure výběrem **Zobrazit všechny vaše prostředky** nebo výběrem všechny **prostředky** z levého navigačního panelu webu Portálu Azure.

    ![Přístup k účtu úložiště Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. V podokně účtu úložiště vyberte **přístupové klíče** a zkopírujte hodnotu *připojovacího řetězce* pro klíč. Uložte tuto hodnotu, protože se jedná o hodnotu *připojovacího řetězce účtu úložiště,* kterou budete muset zadat na portálu pro publikování, abyste získali zájemce pro vaši nabídku marketplace.

     Příkladem připojení sting je:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Klíč úložiště Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. Na stránce účtu úložiště vyberte **Objekty blob .**

   ![Klíč úložiště Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. Jakmile se na stránce objektů BLOB zobrazí, vyberte tlačítko **+ Kontejner.**

8. Zadejte **název** nového kontejneru. Název kontejneru musí obsahovat malá písmena, musí začínat písmenem nebo číslicí a smí obsahovat jenom písmena, číslice a spojovníky (-). Další informace o názvech kontejnerů a objektů blob najdete [v tématu Pojmenování a odkazování na kontejnery, objekty BLOB a metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

    Uložte tuto hodnotu, protože se jedná o hodnotu *Název kontejneru,* kterou je třeba zadat na portálu pro publikování, abyste získali zájemce pro vaši nabídku marketplace.

9. Nastavte úroveň veřejného přístupu ke kontejneru jako **private (žádný anonymní přístup).**

10. Kliknutím na **OK** kontejner vytvoříte.

    ![Nový kontejner](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-blob"></a>Konfigurace nabídky pro odesílání potenciálních zákazníků do objektu Blob Azure

Až budete připraveni nakonfigurovat informace o správě zájemců pro vaši nabídku na portálu pro publikování, postupujte podle následujících kroků:

1. Přejděte na stránku **Nastavení nabídky** pro vaši nabídku.
2. V části Správa zájemců vyberte **Připojit.**

    ![Nabídka připojení](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. V rozbalovacím okně Podrobnosti o připojení vyberte **objekt blob Azure** pro cíl zájemce.

    ![Podrobnosti o připojení](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Zadejte **název kontejneru** a **řetězec připojení účtu úložiště,** který jste získali z následujících pokynů.

    * Příklad názvu kontejneru:`marketplaceleadcontainer`
    * Příklad řetězce připojení `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![účtu úložiště: Podrobnosti připojení](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Vyberte **Uložit**.

    > [!NOTE]
    > Před přijetím zájemců o nabídku je nutné dokončit konfiguraci zbývající nabídky a publikovat ji.



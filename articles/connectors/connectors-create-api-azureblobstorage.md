---
title: Připojení k Azure Blob Storage
description: Vytvářejte a spravujte objekty BLOB v účtech úložiště Azure pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: 34608a085c0d60e0ce07e5d198622f80a43f8b38
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87284077"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Vytváření a správa objektů BLOB v Azure Blob Storage pomocí Azure Logic Apps

V tomto článku se dozvíte, jak můžete v rámci aplikace logiky pomocí konektoru služby Azure Blob Storage získat přístup k souborům uloženým jako objekty BLOB v účtu služby Azure Storage a spravovat je. Tímto způsobem můžete vytvářet aplikace logiky, které automatizují úlohy a pracovní postupy pro správu souborů. Můžete například vytvářet aplikace logiky, které vytvářejí, získávají, aktualizují a odstraňují soubory v účtu úložiště.

Předpokládejme, že máte nástroj, který se aktualizuje na webu Azure. který funguje jako Trigger vaší aplikace logiky. Když k této události dojde, můžete aplikaci logiky aktualizovat nějaký soubor v kontejneru úložiště objektů blob, což je akce v aplikaci logiky.

Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý Start: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Technické informace specifické pro konektor najdete v referenčních informacích k [Azure Blob Storage Connectoru](/connectors/azureblobconnector/).

> [!IMPORTANT]
> Aplikace logiky nemají přímý přístup k účtům úložiště, které jsou za bránami firewall, pokud jsou ve stejné oblasti. Jako alternativní řešení můžete mít aplikace logiky a účet úložiště v různých oblastech. Další informace o povolení přístupu z Azure Logic Apps k účtům úložiště za branami firewall najdete v části [přístup k účtům úložiště za brány firewall](#storage-firewalls) dále v tomto tématu.

<a name="blob-storage-limits"></a>

## <a name="limits"></a>Omezení

* Ve výchozím nastavení můžou akce Azure Blob Storage číst nebo zapisovat soubory o *velikosti 50 MB nebo menší*. Aby bylo možné zpracovat soubory větší než 50 MB, ale až 1024 MB, akce Azure Blob Storage podporují vytváření [bloků zpráv](../logic-apps/logic-apps-handle-large-messages.md). Akce **získat obsah objektu BLOB** implicitně používá bloky dat.

* Aktivační události Azure Blob Storage nepodporují vytváření bloků dat. Při vyžádání obsahu souboru triggery vyberou pouze soubory, které jsou 50 MB nebo menší. Pokud chcete získat soubory větší než 50 MB, postupujte podle tohoto vzoru:

  * Použijte Trigger Blob Storage služby Azure, který vrátí vlastnosti souboru, například **při přidání nebo úpravě objektu BLOB (pouze vlastnosti)**.

  * Postupujte podle triggeru s akcí Azure Blob Storage **získat obsah objektu BLOB** , který načte kompletní soubor a implicitně použije bloky dat.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Účet úložiště Azure a kontejner úložiště](../storage/blobs/storage-quickstart-blobs-portal.md)

* Aplikace logiky, kde potřebujete přístup k vašemu účtu služby Azure Blob Storage. Chcete-li spustit aplikaci logiky pomocí triggeru Blob Storage služby Azure, budete potřebovat [prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Přidat Trigger úložiště objektů BLOB

V Azure Logic Apps musí každá aplikace logiky začínat [triggerem](../logic-apps/logic-apps-overview.md#logic-app-concepts), který se aktivuje, když dojde ke konkrétní události nebo když dojde ke splnění určité podmínky. Pokaždé, když se Trigger aktivuje, modul Logic Apps vytvoří instanci aplikace logiky a začne spouštět pracovní postup vaší aplikace.

Tento příklad ukazuje, jak můžete spustit pracovní postup aplikace logiky s **při přidání nebo úpravě objektu BLOB (jen vlastnosti)** triggeru, když se do kontejneru úložiště přidají nebo aktualizují vlastnosti objektu BLOB.

1. V [Azure Portal](https://portal.azure.com) nebo Visual Studiu vytvořte prázdnou aplikaci logiky, která otevře návrháře aplikace logiky. V tomto příkladu se používá Azure Portal.

2. Do vyhledávacího pole zadejte jako filtr "Azure Blob". V seznamu triggery vyberte aktivační událost, kterou chcete.

   Tento příklad používá tuto aktivační událost: **při přidání nebo úpravě objektu BLOB (pouze vlastnosti)**

   ![Vybrat aktivační událost služby Azure Blob Storage](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-trigger.png)

3. Pokud se zobrazí výzva k zadání podrobností o připojení, [vytvořte připojení k úložišti objektů BLOB hned teď](#create-connection). Nebo, pokud připojení již existuje, zadejte potřebné informace pro aktivační událost.

   V tomto příkladu vyberte kontejner a složku, které chcete monitorovat.

   1. V poli **kontejner** vyberte ikonu složky.

   2. V seznamu složka zvolte pravou ostrou závorku ( **>** ) a potom přejděte do složky, kterou chcete najít, a vyberte ji.

      ![Výběr složky úložiště pro použití s triggerem](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Vyberte interval a frekvenci, jak často chcete, aby Trigger kontroloval změny ve složce.

4. Až budete hotovi, na panelu nástrojů návrháře klikněte na **Uložit**.

5. Nyní pokračujte v přidávání jedné nebo více akcí do aplikace logiky pro úlohy, které chcete provést s výsledky triggeru.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Přidat akci úložiště objektů BLOB

V Azure Logic Apps [Akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je krok v pracovním postupu, který následuje po triggeru nebo jiné akci. V tomto příkladu se aplikace logiky spustí s [triggerem opakování](../connectors/connectors-native-recurrence.md).

1. V [Azure Portal](https://portal.azure.com) nebo Visual Studiu otevřete aplikaci logiky v návrháři aplikace logiky. V tomto příkladu se používá Azure Portal.

2. V návrháři aplikace logiky pod triggerem nebo akcí vyberte **Nový krok**.

   ![Přidat nový krok do pracovního postupu aplikace logiky](./media/connectors-create-api-azureblobstorage/add-new-step-logic-app-workflow.png) 

   Pokud chcete přidat akci mezi stávajícími kroky, přesuňte ukazatel myši na šipku připojení. Zvolte symbol plus ( **+** ), který se zobrazí, a vyberte **přidat akci**.

3. Do vyhledávacího pole zadejte jako filtr "Azure Blob". V seznamu akce vyberte akci, kterou chcete.

   Tento příklad používá tuto akci: **získat obsah objektu BLOB**

   ![Výběr akce Blob Storage v Azure](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-action.png)

4. Pokud se zobrazí výzva k zadání podrobností o připojení, [vytvořte připojení k Azure Blob Storage hned teď](#create-connection).
Nebo, pokud připojení již existuje, zadejte potřebné informace pro akci.

   V tomto příkladu vyberte soubor, který chcete.

   1. V poli **objekt BLOB** vyberte ikonu složky.
  
      ![Výběr složky úložiště pro použití s akcí](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Vyhledejte a vyberte požadovaný soubor na základě čísla **ID** objektu BLOB. Toto **identifikační** číslo můžete najít v metadatech objektu blob, která jsou vrácená dříve popsanou triggerem služby Blob Storage.

5. Až budete hotovi, na panelu nástrojů návrháře klikněte na **Uložit**.
Pokud chcete otestovat aplikaci logiky, ujistěte se, že vybraná složka obsahuje objekt BLOB.

V tomto příkladu se načte jenom obsah objektu BLOB. Chcete-li zobrazit obsah, přidejte další akci, která vytvoří soubor s objektem BLOB pomocí jiného konektoru. Přidejte například akci OneDrivu, která vytvoří soubor na základě obsahu objektu BLOB.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Připojit k účtu úložiště

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Po zobrazení výzvy k vytvoření připojení zadejte tyto informace:

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Název připojení** | Yes | <*název připojení*> | Název, který se má pro připojení vytvořit |
   | **Účet úložiště** | Yes | <*účet úložiště*> | V seznamu vyberte svůj účet úložiště. |
   ||||

   Například:

   ![Vytvoření připojení účtu služby Azure Blob Storage](./media/connectors-create-api-azureblobstorage/create-storage-account-connection.png) 

1. Až budete připraveni, vyberte **vytvořit** .

1. Po vytvoření připojení pokračujte [přidáním triggeru úložiště objektů BLOB](#add-trigger) nebo [Akce Přidat úložiště objektů BLOB](#add-action).

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o této spojnici, jako jsou triggery, akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete na [referenční stránce konektoru](/connectors/azureblobconnector/).

> [!NOTE]
> V případě Logic Apps v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Tato verze konektoru ISE-Label používá místo toho [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

<a name="storage-firewalls"></a>

## <a name="access-storage-accounts-behind-firewalls"></a>Přístup k účtům úložiště za branami firewall

Zabezpečení sítě můžete přidat do účtu služby Azure Storage tím, že omezíte přístup pomocí [brány firewall a pravidel brány firewall](../storage/common/storage-network-security.md). Tato Instalační služba ale vytvoří výzvu pro Azure a další služby Microsoftu, které potřebují přístup k účtu úložiště. Místní komunikace v datacentru obsahuje interní IP adresy, takže nemůžete nastavit pravidla firewallu s omezeními IP adres. Další informace najdete v tématu [Konfigurace virtuálních sítí a bran firewall Azure Storage](../storage/common/storage-network-security.md).

Tady jsou různé možnosti pro přístup k účtům úložiště za branami firewall z Azure Logic Apps pomocí konektoru Azure Blob Storage nebo jiných řešení:

* Konektor Azure Storage Blob

  * [Přístup k účtům úložiště v jiných oblastech](#access-other-regions)
  * [Přístup k účtům úložiště přes důvěryhodnou virtuální síť](#access-trusted-virtual-network)

* Další řešení

  * [Přístup k účtům úložiště jako k důvěryhodné službě se spravovanými identitami](#access-trusted-service)
  * [Přístup k účtům úložiště prostřednictvím Azure API Management](#access-api-management)

<a name="access-other-regions"></a>

### <a name="problems-accessing-storage-accounts-in-the-same-region"></a>Problémy při přístupu k účtům úložiště ve stejné oblasti

Aplikace logiky nemají přímý přístup k účtům úložiště za branami firewall, pokud jsou ve stejné oblasti. Jako alternativní řešení umístěte aplikace logiky do oblasti, která se liší od vašeho účtu úložiště, a udělte přístup k [odchozím IP adresám spravovaných konektorů ve vaší oblasti](../logic-apps/logic-apps-limits-and-config.md#outbound).

> [!NOTE]
> Toto řešení se nevztahuje na konektor Azure Table Storage a konektor Azure Queue Storage. Místo toho můžete pro přístup k Table Storage nebo Queue Storage použít integrovaný Trigger HTTP a akce.

<a name="access-trusted-virtual-network"></a>

### <a name="access-storage-accounts-through-a-trusted-virtual-network"></a>Přístup k účtům úložiště přes důvěryhodnou virtuální síť

Účet úložiště můžete umístit do virtuální sítě Azure, kterou spravujete, a pak tuto virtuální síť přidat do seznamu důvěryhodných virtuálních sítí. Aby aplikace logiky měla přístup k účtu úložiště přes [důvěryhodnou virtuální síť](../virtual-network/virtual-networks-overview.md), musíte tuto aplikaci logiky nasadit do [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), které se může připojit k prostředkům ve virtuální síti. Pak můžete přidat podsítě v tomto ISE do seznamu důvěryhodných. Azure Storage konektory, jako je konektor Blob Storage, můžou mít přímý přístup k kontejneru úložiště. Toto nastavení se shoduje s používáním koncových bodů služby z ISE.

<a name="access-trusted-service"></a>

### <a name="access-storage-accounts-as-a-trusted-service-with-managed-identities"></a>Přístup k účtům úložiště jako k důvěryhodné službě se spravovanými identitami

Chcete-li udělit přístup k účtu úložiště prostřednictvím brány firewall pro důvěryhodné služby společnosti Microsoft, můžete pro tyto služby nastavit výjimku pro tento účet úložiště. Toto řešení povoluje službám Azure, které podporují [spravované identity pro ověřování](../active-directory/managed-identities-azure-resources/overview.md) pro přístup k účtům úložiště za branami firewall jako důvěryhodné služby. Konkrétně pro aplikaci logiky v globálním víceklientském Azure pro přístup k těmto účtům úložiště můžete nejprve [Povolit podporu spravované identity](../logic-apps/create-managed-service-identity.md) v aplikaci logiky. Pak použijete akci HTTP nebo Trigger ve vaší aplikaci logiky a [nastavíte typ ověřování tak, aby používal spravovanou identitu vaší aplikace logiky](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). V tomto scénáři můžete použít *jenom* akci HTTP nebo Trigger.

Pokud chcete nastavit výjimku a podporu spravovaných identit, postupujte podle následujících obecných kroků:

1. V účtu úložiště v části **Nastavení**vyberte **brány firewall a virtuální sítě**. V části **Povolení přístupu z**vyberte možnost **vybrané sítě** , aby se zobrazila související nastavení.

1. V části **výjimky**vyberte možnost **povoluje přístup k tomuto účtu úložiště důvěryhodným službám Microsoftu**a pak vyberte **Uložit**.

   ![Vyberte výjimku, která umožňuje důvěryhodné služby společnosti Microsoft.](./media/connectors-create-api-azureblobstorage/allow-trusted-services-firewall.png)

1. V nastavení aplikace logiky [povolte podporu pro spravovanou identitu](../logic-apps/create-managed-service-identity.md).

1. V pracovním postupu aplikace logiky přidejte a nastavte akci HTTP nebo aktivační událost pro přístup k účtu úložiště nebo entitě.

   > [!IMPORTANT]
   > V případě odchozí akce HTTP nebo triggerů volání Azure Storage účtů se ujistěte, že hlavička žádosti zahrnuje `x-ms-version` vlastnost a verzi rozhraní API pro operaci, kterou chcete spustit v účtu úložiště. Další informace najdete v tématu [ověření přístupu se spravovanou identitou](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) a správou [verzí pro Azure Storage služby](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests).

1. Na této akci [Vyberte spravovanou identitu](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) , která se má použít pro ověřování.

<a name="access-api-management"></a>

### <a name="access-storage-accounts-through-azure-api-management"></a>Přístup k účtům úložiště prostřednictvím Azure API Management

Pokud pro [API Management](../api-management/api-management-key-concepts.md)použijete vyhrazenou vrstvu, můžete rozhraní API úložiště před tím, že použijete API Management a povolíte jeho IP adresy prostřednictvím brány firewall. V podstatě přidejte virtuální síť Azure, kterou používá API Management, do nastavení brány firewall účtu úložiště. Pak můžete použít akci API Management nebo akci protokolu HTTP pro volání rozhraní API Azure Storage. Pokud však zvolíte tuto možnost, musíte proces ověřování zpracovat sami. Další informace najdete v tématu [Jednoduchá architektura podnikové integrace](https://aka.ms/aisarch).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)


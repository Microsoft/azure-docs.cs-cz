---
title: Připojení k úložišti objektů blob Azure
description: Vytváření a správa objektů BLOB v účtech úložiště Azure pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: eb943bfe36be10d1e95d569a5c1bf48563e909c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247355"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Vytváření a správa objektů BLOB ve službě Azure Blob Storage pomocí aplikací Azure Logic Apps

Tento článek ukazuje, jak můžete přistupovat a spravovat soubory uložené jako objekty BLOB ve vašem účtu úložiště Azure z aplikace logiky s konektorem Azure Blob Storage. Tímto způsobem můžete vytvářet aplikace logiky, které automatizují úkoly a pracovní postupy pro správu souborů. Můžete například vytvářet aplikace logiky, které vytvářejí, získávají, aktualizují a odstraňují soubory v účtu úložiště.

Předpokládejme, že máte nástroj, který se aktualizuje na webu Azure. který funguje jako aktivační událost pro aplikaci logiky. Když dojde k této události, můžete mít aplikace logiky aktualizovat některé soubory v kontejneru úložiště objektů blob, což je akce ve vaší aplikaci logiky.

Pokud s aplikacemi logiky teprve začínáte, přečtěte [si, co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a úvodní [příručka: Vytvořte si první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Technické informace specifické pro konektor najdete v tématu [odkaz na konektor úložiště objektů blob Azure](https://docs.microsoft.com/connectors/azureblobconnector/).

> [!IMPORTANT]
> Aplikace logiky nemají přímý přístup k účtům úložiště, které jsou za bránovými firewally, pokud jsou obě ve stejné oblasti. Jako řešení můžete mít aplikace logiky a účet úložiště v různých oblastech. Další informace o povolení přístupu z Azure Logic Apps k účtům úložiště za bránovými firewally najdete v části [Účty úložiště Access za firewally](#storage-firewalls) dále v tomto tématu.

<a name="blob-storage-limits"></a>

## <a name="limits"></a>Omezení

* Ve výchozím nastavení akce úložiště objektů blob Azure můžete číst nebo zapisovat soubory, které jsou *50 MB nebo menší*. Chcete-li zpracovat soubory větší než 50 MB, ale až 1024 MB, akce úložiště objektů blob Azure podporují [nahromadění zpráv](../logic-apps/logic-apps-handle-large-messages.md). Akce **obsahu objektu blob implicitně** používá bloků.

* Aktivační události azure blob storage nepodporují bloků. Při požadavku na obsah souboru aktivační události vybírají pouze soubory, které jsou 50 MB nebo menší. Chcete-li získat soubory větší než 50 MB, postupujte podle tohoto vzoru:

  * Použijte aktivační událost úložiště objektů blob Azure, která vrací vlastnosti souboru, například **Když je objekt blob přidán nebo změněn (jenom vlastnosti).**

  * Postupujte podle aktivační události s akcí obsahu objektů blob Azure **Blob,** která čte celý soubor a implicitně používá bloků.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Účet úložiště Azure a kontejner úložiště](../storage/blobs/storage-quickstart-blobs-portal.md)

* Aplikace logiky, kde potřebujete přístup k účtu úložiště objektů blob Azure. Chcete-li spustit aplikaci logiky s aktivační událostí azure blob storage, potřebujete [prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Přidání aktivační události úložiště objektů blob

V Azure Logic Apps musí každá aplikace logiky začínat [aktivační událostí](../logic-apps/logic-apps-overview.md#logic-app-concepts), která se aktivuje, když dojde k určité události nebo když je splněna určitá podmínka. Pokaždé, když se spustí aktivační událost, modul Logic Apps vytvoří instanci aplikace logiky a spustí pracovní postup aplikace.

Tento příklad ukazuje, jak můžete spustit pracovní postup aplikace logiky s **Při přidání nebo změně objektu blob (pouze vlastnosti)** aktivační událost při přidání nebo aktualizaci vlastností objektu blob v kontejneru úložiště.

1. Na [webu Azure Portal](https://portal.azure.com) nebo Visual Studiu vytvořte prázdnou aplikaci logiky, která otevře Návrhář aplikací logiky. Tento příklad používá portál Azure.

2. Do vyhledávacího pole zadejte jako filtr "azurový objekt blob". Ze seznamu aktivačních událostí vyberte požadovanou aktivační událost.

   Tento příklad používá tuto aktivační událost: **Při přidání nebo změně objektu blob (pouze vlastnosti)**

   ![Vyberte aktivační událost úložiště objektů blob Azure](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-trigger.png)

3. Pokud se zobrazí výzva k zadání podrobností o připojení, [vytvořte připojení úložiště objektů blob .](#create-connection) Nebo pokud připojení již existuje, zadejte potřebné informace pro aktivační událost.

   V tomto příkladu vyberte kontejner a složku, kterou chcete sledovat.

   1. V poli **Kontejner** vyberte ikonu složky.

   2. V seznamu složek zvolte pravou **>** závorku ( ) a pak procházejte, dokud nenajdete a nevyberete požadovanou složku.

      ![Vyberte složku úložiště, která se má použít s aktivační událostí.](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Vyberte interval a frekvenci, jak často má aktivační událost kontrolovat změny ve složce.

4. Až budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**.

5. Teď pokračujte v přidávání jedné nebo více akcí do aplikace logiky pro úkoly, které chcete provést s výsledky aktivační události.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Přidat akci úložiště objektů blob

V Azure Logic Apps [je akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) krokem ve vašem pracovním postupu, který následuje po aktivační události nebo jiné akci. V tomto příkladu aplikace logiky začíná [aktivační událost opakování](../connectors/connectors-native-recurrence.md).

1. Na [webu Azure Portal](https://portal.azure.com) nebo Visual Studiu otevřete aplikaci logiky v Návrháři aplikací logiky. Tento příklad používá portál Azure.

2. V Návrháři aplikace logiky v části aktivační událost nebo akce zvolte **Nový krok**.

   ![Přidání nového kroku do pracovního postupu aplikace logiky](./media/connectors-create-api-azureblobstorage/add-new-step-logic-app-workflow.png) 

   Chcete-li přidat akci mezi existující kroky, přesuňte ukazatel myši na spojovací šipku. Vyberte znaménko plus (**+**), které se zobrazí, a vyberte Přidat **akci**.

3. Do vyhledávacího pole zadejte jako filtr "azurový objekt blob". V seznamu akcí vyberte požadovanou akci.

   Tento příklad používá tuto akci: **Získat obsah objektu blob**

   ![Vybrat akci úložiště objektů blob Azure](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-action.png)

4. Pokud se zobrazí výzva pro podrobnosti o připojení, [vytvořte připojení azure blob storage nyní](#create-connection).
Nebo pokud připojení již existuje, zadejte potřebné informace pro akci.

   V tomto příkladu vyberte požadovaný soubor.

   1. V poli **Objekt blob** vyberte ikonu složky.
  
      ![Vyberte složku úložiště, která se má použít s akcí.](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Najděte a vyberte požadovaný soubor na základě **id** čísla objektu blob. Toto číslo **ID** najdete v metadatech objektu blob, které je vráceno dříve popsanou aktivační událostí úložiště objektů blob.

5. Až budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**.
Chcete-li otestovat aplikaci logiky, ujistěte se, že vybraná složka obsahuje objekt blob.

Tento příklad získá pouze obsah pro objekt blob. Chcete-li zobrazit obsah, přidejte další akci, která vytvoří soubor s objektem blob pomocí jiného konektoru. Přidejte například akci OneDrivu, která vytvoří soubor založený na obsahu objektu blob.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Připojení k účtu úložiště

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Po zobrazení výzvy k vytvoření připojení zadejte tyto informace:

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Název připojení** | Ano | <*název připojení*> | Název, který chcete vytvořit pro připojení |
   | **Účet úložiště** | Ano | <*účet úložiště*> | V seznamu vyberte účet úložiště. |
   ||||

   Například:

   ![Vytvoření připojení účtu úložiště objektů blob Azure](./media/connectors-create-api-azureblobstorage/create-storage-account-connection.png) 

1. Až budete připraveni, vyberte **Vytvořit**

1. Po vytvoření připojení pokračujte [aktivací Přidat úložiště objektů blob](#add-trigger) nebo [přidat akci úložiště objektů blob](#add-action).

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o tomto konektoru, jako jsou aktivační události, akce a omezení, jak je popsáno v souboru Swagger konektoru, naleznete [na referenční stránce konektoru](https://docs.microsoft.com/connectors/azureblobconnector/).

> [!NOTE]
> Pro aplikace logiky v [prostředí služby integrace (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), tento konektor je ise označené verze používá [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) místo.

<a name="storage-firewalls"></a>

## <a name="access-storage-accounts-behind-firewalls"></a>Přístup k účtům úložiště za bránami firewall

Zabezpečení sítě můžete přidat k účtu úložiště Azure omezením přístupu pomocí [pravidel brány firewall a brány firewall](../storage/common/storage-network-security.md). Toto nastavení však vytváří výzvu pro Azure a další služby Microsoftu, které potřebují přístup k účtu úložiště. Místní komunikace v datovém centru abstrahuje interní IP adresy, takže pravidla brány firewall nelze nastavit s omezeními IP adresy. Další informace najdete v tématu [Konfigurace virtuálních sítí a bran firewall Azure Storage](../storage/common/storage-network-security.md).

Tady jsou různé možnosti pro přístup k účtům úložiště za firewally z Azure Logic Apps pomocí konektoru Azure Blob Storage nebo jiných řešení:

* Konektor Azure Storage Blob

  * [Přístup k účtům úložiště v jiných oblastech](#access-other-regions)
  * [Přístup k účtům úložiště prostřednictvím důvěryhodné virtuální sítě](#access-trusted-virtual-network)

* Další řešení

  * [Přístup k účtům úložiště jako důvěryhodné službě se spravovanými identitami](#access-trusted-service)
  * [Přístup k účtům úložiště prostřednictvím Azure API Management](#access-api-management)

<a name="access-other-regions"></a>

### <a name="problems-accessing-storage-accounts-in-the-same-region"></a>Problémy s přístupem k účtům úložiště ve stejné oblasti

Aplikace logiky nemají přímý přístup k účtům úložiště za firewally, když jsou obě ve stejné oblasti. Jako alternativní řešení, dát své aplikace logiky v oblasti, která se liší od vašeho účtu úložiště a udělit přístup k [odchozí IP adresy pro spravované konektory ve vaší oblasti](../logic-apps/logic-apps-limits-and-config.md#outbound).

> [!NOTE]
> Toto řešení se nevztahuje na konektor Azure Table Storage a konektor Azure Queue Storage. Místo toho pro přístup k úložišti table storage nebo fronty použijte integrovanou aktivační událost protokolu HTTP a akce.

<a name="access-trusted-virtual-network"></a>

### <a name="access-storage-accounts-through-a-trusted-virtual-network"></a>Přístup k účtům úložiště prostřednictvím důvěryhodné virtuální sítě

Účet úložiště můžete umístit do virtuální sítě Azure, kterou spravujete, a pak ji přidat do seznamu důvěryhodných virtuálních sítí. Chcete-li mít vaše aplikace logiky přístup k účtu úložiště prostřednictvím [důvěryhodné virtuální sítě](../virtual-network/virtual-networks-overview.md), je třeba nasadit tuto aplikaci logiky do prostředí [služby integrace (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), které se můžete připojit k prostředkům ve virtuální síti. Potom můžete přidat podsítě v této ise do seznamu důvěryhodných. Konektory Azure Storage, jako je konektor úložiště objektů blob, můžou přímo přistupovat ke kontejneru úložiště. Toto nastavení je stejné prostředí jako použití koncových bodů služby z ISE.

<a name="access-trusted-service"></a>

### <a name="access-storage-accounts-as-a-trusted-service-with-managed-identities"></a>Přístup k účtům úložiště jako důvěryhodné službě se spravovanými identitami

Chcete-li poskytnout důvěryhodným službám společnosti Microsoft přístup k účtu úložiště prostřednictvím brány firewall, můžete pro tyto služby nastavit výjimku pro tento účet úložiště. Toto řešení umožňuje službám Azure, které podporují [spravované identity pro ověřování,](../active-directory/managed-identities-azure-resources/overview.md) přístup k účtům úložiště za firewally jako důvěryhodné služby. Konkrétně pro aplikaci logiky v globálním víceklientském Azure pro přístup k těmto účtům úložiště nejprve [povolíte podporu spravované identity](../logic-apps/create-managed-service-identity.md) v aplikaci logiky. Potom použijete akci HTTP nebo aktivační událost v aplikaci logiky a [nastavíte jejich typ ověřování tak, aby používal spravovanou identitu vaší aplikace logiky](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). V tomto scénáři můžete použít *pouze* akce HTTP nebo aktivační událost.

Chcete-li nastavit výjimku a podporu spravovaných identit, postupujte takto:

1. V účtu úložiště vyberte v části **Nastavení** **brány firewall a virtuální sítě**. V části **Povolit přístup z**vyberte možnost **Vybrané sítě,** aby se zobrazila související nastavení.

1. V **části Výjimky**vyberte **Povolit důvěryhodným službám Microsoft u tohoto účtu úložiště přístup**a pak vyberte **Uložit**.

   ![Vybrat výjimku, která umožňuje důvěryhodné služby společnosti Microsoft](./media/connectors-create-api-azureblobstorage/allow-trusted-services-firewall.png)

1. V nastavení aplikace logiky [povolte podporu spravované identity](../logic-apps/create-managed-service-identity.md).

1. V pracovním postupu aplikace logiky přidejte a nastavte akci HTTP nebo aktivační událost pro přístup k účtu nebo entitě úložiště.

   > [!IMPORTANT]
   > Pro odchozí akce HTTP nebo aktivační volání účtů Azure Storage, `x-ms-version` ujistěte se, že hlavička požadavku obsahuje vlastnost a verzi rozhraní API pro operaci, kterou chcete spustit na účtu úložiště. Další informace najdete [v tématu Ověření přístupu se spravovanou identitou](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) a [správa verzí pro služby Azure Storage](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests).

1. Při této akci [vyberte spravovanou identitu,](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) kterou chcete použít pro ověřování.

<a name="access-api-management"></a>

### <a name="access-storage-accounts-through-azure-api-management"></a>Přístup k účtům úložiště prostřednictvím Azure API Management

Pokud používáte vyhrazenou vrstvu pro [správu rozhraní API](../api-management/api-management-key-concepts.md), můžete front rozhraní API pomocí api managementu a povolení jeho IP adresy přes bránu firewall. V podstatě přidejte virtuální síť Azure, kterou používá správa rozhraní API, do nastavení brány firewall účtu úložiště. Potom můžete použít akci správy rozhraní API nebo akci HTTP k volání rozhraní API úložiště Azure. Pokud však zvolíte tuto možnost, budete muset zpracovat proces ověřování sami. Další informace naleznete v [tématu Simple enterprise integration architecture](https://aka.ms/aisarch).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)

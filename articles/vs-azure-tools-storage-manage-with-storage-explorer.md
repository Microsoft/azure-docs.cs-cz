---
title: Začínáme s Průzkumník služby Storage | Microsoft Docs
description: Zahájení správy prostředků Azure Storage pomocí Průzkumník služby Storage. Stažení a instalace Průzkumník služby Azure Storage, připojení k účtu úložiště nebo službě a další.
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 3a8fe3ded6608059cc6ad50901ffe6df5dcf1b08
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102441550"
---
# <a name="get-started-with-storage-explorer"></a>Začínáme s Průzkumníkem služby Storage

## <a name="overview"></a>Přehled

Průzkumník služby Azure Storage je samostatná aplikace, která usnadňuje práci s daty ve službě Azure Storage v operačních systémech Windows, macOS a Linux.

V tomto článku se dozvíte několik způsobů, jak se připojit k účtům Azure Storage a spravovat je.

:::image type="content" alt-text="Microsoft Azure Storage Explorer" source="./vs-storage-explorer-overview.png":::

## <a name="prerequisites"></a>Předpoklady

# <a name="windows"></a>[Windows](#tab/windows)

Průzkumník služby Storage podporují následující verze Windows:

* Windows 10 (doporučeno)
* Windows 8
* Windows 7

Pro všechny verze Windows Průzkumník služby Storage vyžaduje minimálně .NET Framework 4.7.2.

# <a name="macos"></a>[macOS](#tab/macos)

Průzkumník služby Storage podporují následující verze nástroje macOS:

* macOS 10,12 Sierra a novějších verzí

# <a name="linux"></a>[Linux](#tab/linux)

Průzkumník služby Storage je k dispozici v [úložišti přichycení](https://snapcraft.io/storage-explorer) pro většinu běžných distribucí systému Linux. Pro tuto instalaci doporučujeme úložiště pro modul snap-in. Průzkumník služby Storage modul snap nainstaluje všechny jeho závislosti a aktualizace, když jsou nové verze publikovány do obchodu s modulem snap-in.

Podporované distribuce najdete na [ `snapd` stránce instalace](https://snapcraft.io/docs/installing-snapd).

Průzkumník služby Storage vyžaduje použití Správce hesel. Je možné, že se budete muset připojit k správci hesel ručně. Pomocí následujícího příkazu se můžete připojit Průzkumník služby Storage k vašemu systému správce hesel.

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Průzkumník služby Storage je k dispozici také jako soubor *. tar. gz* download. Pokud používáte *. tar. gz*, musíte nainstalovat závislosti ručně. Následující distribuce podpory Linux *. tar. gz* instalace:

* Ubuntu 20,04 x64
* Ubuntu 18,04 x64
* Ubuntu 16,04 x64

Instalace *. tar. gz* může fungovat na dalších distribucích, ale oficiálně se podporují jenom ty, které jsou uvedené.

Další nápovědu k instalaci Průzkumník služby Storage na Linux najdete v tématu [závislosti Linux](./storage/common/storage-explorer-troubleshooting.md#linux-dependencies) v Průvodci odstraňováním potíží Průzkumník služby Azure Storage.

---

## <a name="download-and-install"></a>Stažení a instalace

Pokud si chcete stáhnout a nainstalovat Průzkumník služby Storage, přečtěte si téma [Průzkumník služby Azure Storage](https://www.storageexplorer.com).

## <a name="connect-to-a-storage-account-or-service"></a>Připojení k účtu úložiště nebo službě

Průzkumník služby Storage poskytuje několik způsobů, jak se připojit k prostředkům Azure:

* [Přihlaste se k Azure, abyste měli přístup k předplatným a jejich prostředkům.](#sign-in-to-azure)
* [Připojit k individuálnímu Azure Storage prostředku](#attach-to-an-individual-resource)
* [Připojit k prostředku CosmosDB](#connect-to-azure-cosmos-db)

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

> [!NOTE]
> Pro úplný přístup k prostředkům po přihlášení Průzkumník služby Storage vyžaduje obě oprávnění ke správě (Azure Resource Manager) a datové vrstvě. To znamená, že potřebujete oprávnění Azure Active Directory (Azure AD) pro přístup k vašemu účtu úložiště, kontejnerům v účtu a datům v kontejnerech. Pokud máte oprávnění jenom na datové vrstvě, zvažte možnost **přihlášení pomocí Azure Active Directory (Azure AD)** při připojování k prostředku. Další informace o konkrétních oprávněních Průzkumník služby Storage vyžaduje, najdete v [Průvodci odstraňováním potíží Průzkumník služby Azure Storage](./storage/common/storage-explorer-troubleshooting.md#azure-rbac-permissions-issues).

1. V Průzkumník služby Storage vyberte **Zobrazit**  >  **správu účtů** nebo klikněte na tlačítko **Spravovat účty** .

    :::image type="content" alt-text="Správa účtů" source ="./vs-storage-explorer-manage-accounts.png":::

1. **Správa účtů** teď zobrazí všechny účty Azure, ke kterým jste se přihlásili. Pokud se chcete připojit k jinému účtu, vyberte **Přidat účet...**.

1. Otevře se dialogové okno **připojit k Azure Storage** . Na panelu **Vybrat prostředek** vyberte **předplatné**.

    :::image type="content" alt-text="Dialogové okno připojení" source="./vs-storage-explorer-connect-dialog.png":::

1. Na panelu **Vybrat prostředí Azure** vyberte prostředí Azure, ke kterému se chcete přihlásit. Můžete se přihlásit ke globálnímu Azure, národnímu cloudu nebo instanci Azure Stack. Pak vyberte **Další**.

    :::image type="content" alt-text="Možnost přihlášení" source="./vs-storage-explorer-connect-environment.png":::

    > [!TIP]
    > Další informace o Azure Stack najdete v tématu [připojení Průzkumník služby Storage k předplatnému služby Azure Stack nebo k účtu úložiště](/azure-stack/user/azure-stack-storage-connect-se).

1. Průzkumník služby Storage otevře webovou stránku, abyste se mohli přihlásit.

1. Po úspěšném přihlášení pomocí účtu Azure se zobrazí účty a předplatná Azure přidružená k tomuto účtu pod položkou **Správa účtů**. Vyberte předplatná Azure, se kterými chcete pracovat, a pak vyberte **Apply** (Použít).

    :::image type="content" alt-text="Výběr předplatných Azure" source="./vs-storage-explorer-account-panel.png":::

1. **Průzkumník** zobrazuje účty úložiště přidružené k vybraným předplatným Azure.

    :::image type="content" alt-text="Vybraná předplatná Azure" source="./vs-storage-explorer-subscription-node.png":::

### <a name="attach-to-an-individual-resource"></a>Připojit k individuálnímu prostředku

Průzkumník služby Storage umožňuje připojit se k jednotlivým prostředkům, jako je například kontejner Azure Data Lake Storage Gen2, pomocí různých metod ověřování. Některé metody ověřování jsou podporovány pouze pro určité typy prostředků.

| Typ prostředku    | Azure AD | Název účtu a klíč | Sdílený přístupový podpis (SAS)  | Veřejné (anonymní) |
|------------------|----------|----------------------|--------------------------------|--------------------|
| Účty úložiště | Yes      | Yes                  | Ano (připojovací řetězec nebo adresa URL) | No                 |
| Kontejnery objektů blob  | Yes      | No                   | Ano (URL)                      | Yes                |
| Kontejnery Gen2  | Yes      | No                   | Ano (URL)                      | Yes                |
| Gen2 adresáře | Yes      | No                   | Ano (URL)                      | Yes                |
| Sdílené složky      | No       | No                   | Ano (URL)                      | No                 |
| Fronty           | Yes      | No                   | Ano (URL)                      | No                 |
| Tabulky           | No       | No                   | Ano (URL)                      | No                 |
 
Průzkumník služby Storage se taky může připojit k [emulátoru místního úložiště](#local-storage-emulator) pomocí nakonfigurovaných portů emulátoru.

Pokud se chcete připojit k individuálnímu prostředku, vyberte tlačítko **připojit** na panelu nástrojů na levé straně. Pak postupujte podle pokynů pro typ prostředku, ke kterému se chcete připojit.

:::image type="content" alt-text="Možnost Připojit k úložišti Azure" source="./vs-storage-explorer-connect-button.png":::

Po úspěšném přidání připojení k účtu úložiště se v části **místní & připojené**  >  **účty úložiště** zobrazí nový uzel stromové struktury.

U jiných typů prostředků se v části **místní & připojené**  >  **účty úložiště**  >  **(připojené kontejnery)** přidá nový uzel. Uzel se zobrazí pod uzlem skupiny odpovídajícím jeho typu. Například nové připojení k kontejneru Azure Data Lake Storage Gen2 se zobrazí v části **kontejnery objektů BLOB**.

Pokud Průzkumník služby Storage nešlo přidat vaše připojení, nebo pokud nemůžete získat přístup k datům po úspěšném přidání připojení, přečtěte si [Průvodce řešením potíží s Průzkumník služby Azure Storage](./storage/common/storage-explorer-troubleshooting.md).

V následujících částech jsou popsány různé metody ověřování, které můžete použít pro připojení k jednotlivým prostředkům.

#### <a name="azure-ad"></a>Azure AD

Průzkumník služby Storage můžou účet Azure použít k připojení k následujícím typům prostředků:
* Kontejnery objektů blob
* Kontejnery Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen2 adresářů
* Fronty
 
Služba Azure AD je upřednostňovanou možností, pokud máte přístup k prostředkům v datové vrstvě, ale nemáte přístup k vrstvě správy.

1. Přihlaste se k aspoň jednomu účtu Azure pomocí [výše popsaného postupu](#sign-in-to-azure).
1. Na panelu **Vybrat prostředek** dialogového okna **připojit k Azure Storage** vyberte **kontejner objektů BLOB**, **adls Gen2 kontejner** nebo **fronta**.
1. Vyberte **Přihlásit se pomocí Azure Active Directory (Azure AD)** a vyberte **Další**.
1. Vyberte účet Azure a tenanta. Účet a klient musí mít přístup k prostředku úložiště, ke kterému se chcete připojit. Vyberte **Další**.
1. Zadejte zobrazovaný název pro vaše připojení a adresu URL prostředku. Vyberte **Další**.
1. Projděte si informace o připojení na panelu **Souhrn** . Pokud jsou informace o připojení správné, vyberte **připojit**.

#### <a name="account-name-and-key"></a>Název účtu a klíč

Průzkumník služby Storage se může připojit k účtu úložiště pomocí názvu a klíče účtu úložiště.

Klíče účtu můžete najít v [Azure Portal](https://portal.azure.com). Otevřete stránku účtu úložiště a vyberte **Nastavení**  >  **přístupové klíče**.

1. Na panelu **Vybrat prostředek** dialogového okna **připojit k Azure Storage** vyberte možnost **účet úložiště**.
1. Vyberte **název a klíč účtu** a vyberte **Další**.
1. Zadejte zobrazovaný název pro vaše připojení, název účtu a jeden z klíčů účtu. Vyberte příslušné prostředí Azure. Vyberte **Další**.
1. Projděte si informace o připojení na panelu **Souhrn** . Pokud jsou informace o připojení správné, vyberte **připojit**.

#### <a name="shared-access-signature-sas-connection-string"></a>Připojovací řetězec sdíleného přístupového podpisu (SAS)

Průzkumník služby Storage se může připojit k účtu úložiště pomocí připojovacího řetězce se sdíleným přístupovým podpisem (SAS). Připojovací řetězec SAS vypadá takto:

```text
SharedAccessSignature=sv=2020-04-08&ss=btqf&srt=sco&st=2021-03-02T00%3A22%3A19Z&se=2020-03-03T00%3A22%3A19Z&sp=rl&sig=fFFpX%2F5tzqmmFFaL0wRffHlhfFFLn6zJuylT6yhOo%2FY%3F;
BlobEndpoint=https://contoso.blob.core.windows.net/;
FileEndpoint=https://contoso.file.core.windows.net/;
QueueEndpoint=https://contoso.queue.core.windows.net/;
TableEndpoint=https://contoso.table.core.windows.net/;
```

1. Na panelu **Vybrat prostředek** dialogového okna **připojit k Azure Storage** vyberte možnost **účet úložiště**.
1. Vyberte **sdílený přístupový podpis (SAS)** a vyberte **Další**.
1. Zadejte zobrazovaný název pro připojení a připojovací řetězec SAS pro účet úložiště. Vyberte **Další**.
1. Projděte si informace o připojení na panelu **Souhrn** . Pokud jsou informace o připojení správné, vyberte **připojit**.

#### <a name="shared-access-signature-sas-url"></a>Adresa URL sdíleného přístupového podpisu (SAS)

Průzkumník služby Storage se může připojit k následujícím typům prostředků pomocí identifikátoru URI SAS:
* Kontejner objektů blob
* Azure Data Lake Storage Gen2 kontejner nebo adresář
* Sdílená složka
* Fronta
* Tabulka

Identifikátor URI SAS vypadá takto:

```text
https://contoso.blob.core.windows.net/container01?sv=2020-04-08&st=2021-03-02T00%3A30%3A33Z&se=2020-03-03T00%3A30%3A33Z&sr=c&sp=rl&sig=z9VFdWffrV6FXU51T8b8HVfipZPOpYOFLXuQw6wfkFY%3F
```

1. Na panelu **Vybrat prostředek** dialogového okna **připojit k Azure Storage** vyberte prostředek, ke kterému se chcete připojit.
1. Vyberte **sdílený přístupový podpis (SAS)** a vyberte **Další**.
1. Zadejte zobrazovaný název pro připojení a identifikátor URI SAS pro daný prostředek. Vyberte **Další**.
1. Projděte si informace o připojení na panelu **Souhrn** . Pokud jsou informace o připojení správné, vyberte **připojit**.

#### <a name="local-storage-emulator"></a>Emulátor místního úložiště

Průzkumník služby Storage se může připojit k emulátoru Azure Storage. V současné době existují dva podporované emulátory:

* [Emulátor Azure Storage](storage/common/storage-use-emulator.md) (jenom Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, MacOS nebo Linux)

Pokud emulátor naslouchá na výchozích portech, můžete k emulátoru použít **místní & připojené**  >  **účty úložiště**  >  **– Výchozí uzel portů** .

Pokud chcete pro připojení použít jiný název nebo pokud emulátor neběží na výchozích portech:

1. Spusťte emulátor.

   > [!IMPORTANT]
   > Průzkumník služby Storage automaticky nespouští emulátor. Musíte ho spustit ručně.

1. Na panelu **Vybrat prostředek** okna **připojit k Azure Storage** vyberte **místní emulátor úložiště**.
1. Zadejte zobrazovaný název pro vaše připojení a číslo portu pro každou emulovanou službu, kterou chcete použít. Pokud nechcete, aby se služba používala, nechejte odpovídající port prázdný. Vyberte **Další**.
1. Projděte si informace o připojení na panelu **Souhrn** . Pokud jsou informace o připojení správné, vyberte **připojit**.

### <a name="connect-to-azure-cosmos-db"></a>Připojení ke službě Azure Cosmos DB

Průzkumník služby Storage také podporuje připojení k prostředkům Azure Cosmos DB.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Připojení k účtu Azure Cosmos DB pomocí připojovacího řetězce

Místo správy účtů Azure Cosmos DB prostřednictvím předplatného Azure se můžete připojit k Azure Cosmos DB pomocí připojovacího řetězce. Postup pro připojení je následující:

1. V části **Průzkumník** rozbalte **místní & připojeno**, klikněte pravým tlačítkem na **Cosmos DB účty** a vyberte **připojit k Azure Cosmos DB**.

    ![Připojení ke službě Azure Cosmos DB pomocí připojovacího řetězce][21]

1. Vyberte rozhraní Azure Cosmos DB API, zadejte svoje data **připojovacího řetězce** a pak kliknutím na **OK** připojte účet Azure Cosmos DB. Informace o tom, jak načíst připojovací řetězec, najdete v tématu [Správa účtu Azure Cosmos](./cosmos-db/how-to-manage-database-account.md).

    ![Připojovací řetězec][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Připojení k Azure Data Lake Store podle identifikátoru URI

Můžete získat přístup k prostředku, který není ve vašem předplatném. Potřebujete někoho, kdo má k tomuto prostředku přístup, aby vám poskytl identifikátor URI prostředku. Po přihlášení se připojte k Data Lake Store pomocí identifikátoru URI. Postup pro připojení je následující:

1. V části **Průzkumník** rozbalte **místní & připojeno**.

1. Pravým tlačítkem myši klikněte na **Data Lake Storage Gen1** a vyberte **připojit k Data Lake Storage Gen1**.

    ![Místní nabídka připojit k Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. Zadejte identifikátor URI a pak vyberte **OK**. Vaše Data Lake Store se zobrazí v části **Data Lake Storage**.

    ![Připojit k Data Lake Store výsledek](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

Tento příklad používá Data Lake Storage Gen1. Azure Data Lake Storage Gen2 je teď k dispozici. Další informace najdete v tématu [co je Azure Data Lake Storage Gen1](./data-lake-store/data-lake-store-overview.md).

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Generování sdíleného přístupového podpisu v Průzkumník služby Storage<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>Sdílený přístupový podpis na úrovni účtu

1. Klikněte pravým tlačítkem na účet úložiště, který chcete sdílet, a pak vyberte **získat sdílený přístupový podpis**.

    ![Možnost získání sdíleného přístupového podpisu – možnost místní nabídky][14]

1. V části **sdílený přístupový podpis** zadejte časový rámec a požadovaná oprávnění pro účet a pak vyberte **vytvořit**.

    ![Získání sdíleného přístupového podpisu][15]

1. Zkopírujte buď **připojovací řetězec** , nebo nezpracovaný **řetězec dotazu** do schránky.

### <a name="service-level-shared-access-signature"></a>Podpis sdíleného přístupu na úrovni služby

Můžete získat sdílený přístupový podpis na úrovni služby. Další informace najdete v tématu [získání SAS pro kontejner objektů BLOB](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

## <a name="search-for-storage-accounts"></a>Vyhledávání účtů úložiště

Pokud chcete najít prostředek úložiště, můžete hledat v podokně **Průzkumník** .

Když do vyhledávacího pole zadáte text, Průzkumník služby Storage zobrazí všechny prostředky, které odpovídají hledané hodnotě, kterou jste do tohoto bodu zadali. Tento příklad ukazuje hledání **koncových bodů**:

![Vyhledávání účtu úložiště][23]

> [!NOTE]
> Pokud chcete zrychlit hledání, pomocí **správy účtů** zrušte výběr všech předplatných, která neobsahují hledanou položku. Můžete také kliknout pravým tlačítkem na uzel a vybrat **Hledat z tohoto umístění** a zahájit hledání z konkrétního uzlu.

## <a name="next-steps"></a>Další kroky

* [Správa prostředků Azure Blob Storage pomocí Průzkumník služby Storage](vs-azure-tools-storage-explorer-blobs.md)
* [Práce s daty s využitím Průzkumníka služby Azure Storage](./cosmos-db/storage-explorer.md)
* [Správa prostředků Azure Data Lake Store s využitím Průzkumník služby Storage](./data-lake-store/data-lake-store-in-storage-explorer.md)

[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png

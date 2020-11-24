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
ms.openlocfilehash: be9b2d9a31d4affc9615f5d2f4b2585b7533a0f6
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545903"
---
# <a name="get-started-with-storage-explorer"></a>Začínáme s Průzkumníkem služby Storage

## <a name="overview"></a>Přehled

Průzkumník služby Azure Storage je samostatná aplikace, která usnadňuje práci s daty ve službě Azure Storage v operačních systémech Windows, macOS a Linux. V tomto článku se dozvíte několik způsobů, jak se připojit k účtům Azure Storage a spravovat je.

![Microsoft Azure Storage Explorer][0]

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

Podporované distribuce najdete na [stránce s přichycenými instalacemi](https://snapcraft.io/docs/installing-snapd).

Průzkumník služby Storage vyžaduje použití Správce hesel. Je možné, že se budete muset připojit k správci hesel ručně. Pomocí následujícího příkazu se můžete připojit Průzkumník služby Storage k vašemu systému správce hesel.

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Průzkumník služby Storage je k dispozici také jako soubor *. tar. gz* download. Závislosti je nutné nainstalovat ručně. Následující distribuce podpory Linux *. tar. gz* instalace:

* Ubuntu 20,04 x64
* Ubuntu 18,04 x64
* Ubuntu 16,04 x64

Instalace *. tar. gz* může fungovat na dalších distribucích, ale oficiálně se podporují jenom ty, které jsou uvedené.

Další nápovědu k instalaci Průzkumník služby Storage na Linux najdete v tématu [závislosti Linux](./storage/common/storage-explorer-troubleshooting.md#linux-dependencies) v Průvodci odstraňováním potíží Průzkumník služby Azure Storage.

---

## <a name="download-and-install"></a>Stažení a instalace

Pokud si chcete stáhnout a nainstalovat Průzkumník služby Storage, přečtěte si téma [Průzkumník služby Azure Storage](https://www.storageexplorer.com).

## <a name="connect-to-a-storage-account-or-service"></a>Připojení k účtu úložiště nebo službě

Průzkumník služby Storage nabízí několik způsobů, jak se připojit k účtům úložiště. Obecně můžete:

* [Přihlaste se k Azure, abyste měli přístup k předplatným a jejich prostředkům.](#sign-in-to-azure)
* [Připojit konkrétní prostředek úložiště nebo CosmosDB](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

> [!NOTE]
> Pro úplný přístup k prostředkům po přihlášení Průzkumník služby Storage vyžaduje obě oprávnění ke správě (Azure Resource Manager) a datové vrstvě. To znamená, že potřebujete oprávnění Azure Active Directory (Azure AD), která vám umožní přístup k vašemu účtu úložiště, kontejnerům v účtu a datům v kontejnerech. Pokud máte oprávnění jenom na datové vrstvě, zvažte [Přidání prostředku přes Azure AD](#add-a-resource-via-azure-ad). Další informace o konkrétních oprávněních Průzkumník služby Storage vyžaduje, najdete v [Průvodci odstraňováním potíží Průzkumník služby Azure Storage](./storage/common/storage-explorer-troubleshooting.md#azure-rbac-permissions-issues).

1. V Průzkumník služby Storage vyberte **Zobrazit**  >  **správu účtů** nebo klikněte na tlačítko **Spravovat účty** .

    ![Správa účtů][1]

1. **Správa účtů** teď zobrazí všechny účty Azure, ke kterým jste se přihlásili. Pokud se chcete připojit k jinému účtu, vyberte **Přidat účet**.

1. V oblasti **připojit k Azure Storage** vyberte cloud Azure z **prostředí Azure** , abyste se přihlásili do národního cloudu nebo Azure Stack. Po zvolení prostředí vyberte **Další**.

    ![Možnost přihlášení][2]

    Průzkumník služby Storage otevře stránku, abyste se mohli přihlásit. Další informace najdete v tématu [připojení Průzkumníka služby Storage k předplatnému Azure Stack nebo účtu úložiště](/azure-stack/user/azure-stack-storage-connect-se).

1. Po úspěšném přihlášení pomocí účtu Azure se zobrazí účty a předplatná Azure přidružená k tomuto účtu pod položkou **Správa účtů**. Výběrem **všech předplatných** přepnete výběr mezi všemi nebo žádné z uvedených předplatných Azure. Vyberte předplatná Azure, se kterými chcete pracovat, a pak vyberte **Apply** (Použít).

    ![Výběr předplatných Azure][3]

    **Průzkumník** zobrazuje účty úložiště přidružené k vybraným předplatným Azure.

    ![Vybraná předplatná Azure][4]

### <a name="attach-a-specific-resource"></a>Připojit konkrétní prostředek

Existuje několik způsobů, jak se připojit k prostředku v Průzkumník služby Storage:

* [Přidejte prostředek přes Azure AD](#add-a-resource-via-azure-ad). Pokud máte oprávnění pouze v datové vrstvě, použijte tuto možnost k přidání kontejneru objektů BLOB nebo Azure Data Lake Storage Gen2 kontejneru úložiště objektů BLOB.
* [Použijte připojovací řetězec](#use-a-connection-string). Tuto možnost použijte, pokud máte připojovací řetězec k účtu úložiště. Průzkumník služby Storage podporuje připojovací řetězce klíčů a [sdíleného přístupového podpisu](./storage/common/storage-sas-overview.md) .
* [Použijte identifikátor URI sdíleného přístupového podpisu](#use-a-shared-access-signature-uri). Pokud máte [identifikátor URI sdíleného přístupového podpisu](./storage/common/storage-sas-overview.md) pro kontejner objektů blob, sdílenou složku, frontu nebo tabulku, použijte ji k připojení k prostředku. Identifikátor URI sdíleného přístupového podpisu můžete získat buď pomocí [Průzkumník služby Storage](#generate-a-sas-in-storage-explorer) nebo [Azure Portal](https://portal.azure.com).
* [Použijte název a klíč](#use-a-name-and-key). Pokud znáte některý z klíčů účtu pro účet úložiště, můžete tuto možnost použít k rychlému připojení. Klíče najdete na stránce účtu úložiště tak, že v Azure Portal vyberete **Nastavení**  >  **přístupové klíče** . [Azure portal](https://portal.azure.com)
* [Připojte se k místnímu emulátoru](#attach-to-a-local-emulator). Pokud používáte některý z dostupných Azure Storage emulátorů, použijte tuto možnost k snadnému připojení k emulátoru.
* [Připojte se k účtu Azure Cosmos DB pomocí připojovacího řetězce](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string). Tuto možnost použijte, pokud máte připojovací řetězec k instanci CosmosDB.
* [Připojte se k Azure Data Lake Store podle identifikátoru URI](#connect-to-azure-data-lake-store-by-uri). Tuto možnost použijte, pokud máte identifikátor URI pro Azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Přidání prostředku přes Azure AD

1. Vyberte symbol **připojení** pro otevření **připojit k Azure Storage**.

    ![Možnost Připojit k úložišti Azure][9]

1. Pokud jste to ještě neudělali, použijte možnost **Přidat účet Azure** a přihlaste se k účtu Azure, který má přístup k prostředku. Až se přihlásíte, vraťte se a **Připojte se k Azure Storage**.

1. Vyberte **Přidat prostředek prostřednictvím Azure Active Directory (Azure AD)** a pak vyberte **Další**.

1. Vyberte účet Azure a tenanta. Tyto hodnoty musí mít přístup k prostředku úložiště, ke kterému se chcete připojit. Vyberte **Další**.

1. Vyberte typ prostředku, který chcete připojit. Zadejte informace potřebné k připojení. 

   Informace, které zadáte na této stránce, závisí na typu prostředku, který přidáváte. Ujistěte se, že jste zvolili správný typ prostředku. Po zadání požadovaných informací vyberte **Další**.

1. Zkontrolujte **Souhrn připojení** a ujistěte se, že jsou všechny informace správné. Pokud je, vyberte **připojit**. V opačném případě vyberte **zpět** a vraťte se na předchozí stránky a opravte případné nesprávné informace.

Po úspěšném přidání připojení strom prostředků přejde na uzel, který představuje připojení. Prostředek se zobrazí v části **místní & připojené**  >  **Storage Accounts**  >  kontejnery objektů úložiště **(připojené kontejnery)**  >  **Blob Containers**. Pokud Průzkumník služby Storage nešlo přidat vaše připojení, nebo pokud nemůžete získat přístup k datům po úspěšném přidání připojení, přečtěte si [Průvodce řešením potíží s Průzkumník služby Azure Storage](./storage/common/storage-explorer-troubleshooting.md).

#### <a name="use-a-connection-string"></a>Pomocí připojovacího řetězce

1. Vyberte symbol **připojení** pro otevření **připojit k Azure Storage**.

    ![Možnost Připojit k úložišti Azure][9]

1. Vyberte **Použít připojovací řetězec** a pak vyberte **Další**.

1. Vyberte zobrazovaný název pro připojení a zadejte připojovací řetězec. Pak vyberte **Další**.

1. Zkontrolujte **Souhrn připojení** a ujistěte se, že jsou všechny informace správné. Pokud je, vyberte **připojit**. V opačném případě vyberte **zpět** a vraťte se na předchozí stránky a opravte případné nesprávné informace.

Po úspěšném přidání připojení strom prostředků přejde na uzel, který představuje připojení. Prostředek se zobrazí v části **místní & připojené**  >  **účty úložiště**. Pokud Průzkumník služby Storage nešlo přidat vaše připojení, nebo pokud nemůžete získat přístup k datům po úspěšném přidání připojení, přečtěte si [Průvodce řešením potíží s Průzkumník služby Azure Storage](./storage/common/storage-explorer-troubleshooting.md).

#### <a name="use-a-shared-access-signature-uri"></a>Pomocí identifikátoru URI sdíleného přístupového podpisu

1. Vyberte symbol **připojení** pro otevření **připojit k Azure Storage**.

    ![Možnost Připojit k úložišti Azure][9]

1. Vyberte **použít identifikátor URI sdíleného přístupového podpisu (SAS)** a pak vyberte **Další**.

1. Vyberte zobrazovaný název vašeho připojení a zadejte identifikátor URI sdíleného přístupového podpisu. Koncový bod služby pro typ prostředku, který připojujete, by měl automatické vyplňování. Pokud používáte vlastní koncový bod, možná to není. Vyberte **Další**.

1. Zkontrolujte **Souhrn připojení** a ujistěte se, že jsou všechny informace správné. Pokud je, vyberte **připojit**. V opačném případě vyberte **zpět** a vraťte se na předchozí stránky a opravte případné nesprávné informace.

Po úspěšném přidání připojení strom prostředků přejde na uzel, který představuje připojení. Prostředek se zobrazí v části **místní & připojené**  >  **účty úložiště**  >  **(připojené kontejnery)**  >  *uzlu služby pro typ kontejneru, který jste připojili*. Pokud Průzkumník služby Storage nemohlo přidat vaše připojení, přečtěte si [Průvodce řešením potíží s Průzkumník služby Azure Storage](./storage/common/storage-explorer-troubleshooting.md). Pokud po úspěšném přidání připojení nemůžete získat přístup k datům, přečtěte si průvodce řešením potíží.

#### <a name="use-a-name-and-key"></a>Pomocí názvu a klíče

1. Vyberte symbol **připojení** pro otevření **připojit k Azure Storage**.

    ![Možnost Připojit k úložišti Azure][9]

1. Vyberte **použít název a klíč účtu úložiště** a pak vyberte **Další**.

1. Vyberte zobrazovaný název pro vaše připojení.

1. Zadejte název svého účtu úložiště a jeden z jeho přístupových klíčů.

1. Zvolte **doménu úložiště** , kterou chcete použít, a pak vyberte **Další**.

1. Zkontrolujte **Souhrn připojení** a ujistěte se, že jsou všechny informace správné. Pokud je, vyberte **připojit**. V opačném případě vyberte **zpět** a vraťte se na předchozí stránky a opravte případné nesprávné informace.

Po úspěšném přidání připojení strom prostředků přejde na uzel, který představuje připojení. Prostředek se zobrazí v části **místní & připojené**  >  **účty úložiště**. Pokud Průzkumník služby Storage nešlo přidat vaše připojení, nebo pokud nemůžete získat přístup k datům po úspěšném přidání připojení, přečtěte si [Průvodce řešením potíží s Průzkumník služby Azure Storage](./storage/common/storage-explorer-troubleshooting.md).

#### <a name="attach-to-a-local-emulator"></a>Připojením k místnímu emulátoru

Průzkumník služby Storage aktuálně podporuje dva oficiální emulátory úložiště:

* [Emulátor Azure Storage](storage/common/storage-use-emulator.md) (jenom Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, MacOS nebo Linux)

Pokud emulátor naslouchá na výchozích portech, můžete pro přístup k emulátoru použít uzel **výchozí porty pro emulátor** . Vyhledejte **emulátor – výchozí porty** v části **místní & připojené**  >  **účty úložiště**.

Pokud chcete pro připojení použít jiný název nebo pokud emulátor neběží na výchozích portech, postupujte podle těchto kroků:

1. Spusťte emulátor. Zadejte příkaz `AzureStorageEmulator.exe status` pro zobrazení portů pro každý typ služby.

   > [!IMPORTANT]
   > Průzkumník služby Storage automaticky nespouští emulátor. Musíte ho spustit ručně.

1. Vyberte symbol **připojení** pro otevření **připojit k Azure Storage**.

    ![Možnost Připojit k úložišti Azure][9]

1. Vyberte **připojit k místnímu emulátoru** a pak vyberte **Další**.

1. Vyberte zobrazované jméno pro připojení a zadejte porty, na kterých váš emulátor naslouchá pro každý typ služby. **Připojení k místnímu emulátoru** navrhuje výchozí hodnoty portů pro většinu emulátorů. **Port souborů** je prázdný, protože žádná z oficiálních emulátorů aktuálně nepodporuje službu souborů. Pokud emulátor, který používáte, podporuje soubory, můžete zadat port, který se má použít. Pak vyberte **Další**.

1. Zkontrolujte **Souhrn připojení** a ujistěte se, že jsou všechny informace správné. Pokud je, vyberte **připojit**. V opačném případě vyberte **zpět** a vraťte se na předchozí stránky a opravte případné nesprávné informace.

Po úspěšném přidání připojení strom prostředků přejde na uzel, který představuje připojení. Uzel by se měl zobrazit v části **místní & připojené**  >  **účty úložiště**. Pokud Průzkumník služby Storage nešlo přidat vaše připojení, nebo pokud nemůžete získat přístup k datům po úspěšném přidání připojení, přečtěte si [Průvodce řešením potíží s Průzkumník služby Azure Storage](./storage/common/storage-explorer-troubleshooting.md).

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
>
>

## <a name="next-steps"></a>Další kroky

* [Správa prostředků Azure Blob Storage pomocí Průzkumník služby Storage](vs-azure-tools-storage-explorer-blobs.md)
* [Práce s daty s využitím Průzkumníka služby Azure Storage](./cosmos-db/storage-explorer.md)
* [Správa prostředků Azure Data Lake Store s využitím Průzkumník služby Storage](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-azure-environment.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/account-panel-subscriptions-apply.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png
---
title: Začínáme s Průzkumníkem úložiště | Dokumenty společnosti Microsoft
description: Správa prostředků úložiště Azure pomocí Průzkumníka úložiště
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 7886d5a1ad0745550767b7d6f19592ca3c84b00a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279790"
---
# <a name="get-started-with-storage-explorer"></a>Začínáme s Průzkumníkem služby Storage

## <a name="overview"></a>Přehled

Microsoft Azure Storage Explorer je samostatná aplikace, která usnadňuje práci s daty Azure Storage na Windows, macOS a Linuxu. V tomto článku se dozvíte několik způsobů připojení a správy účtů úložiště Azure.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>Požadavky

# <a name="windows"></a>[Windows](#tab/windows)

Následující verze Průzkumníka úložiště podporují následující verze systému Windows:

* Windows 10 (doporučeno)
* Windows 8
* Windows 7

Pro všechny verze systému Windows průzkumník úložiště vyžaduje rozhraní .NET Framework 4.6.2 nebo novější.

# <a name="macos"></a>[Macos](#tab/macos)

Průzkumníka úložiště podporují následující verze macOS:

* macOS 10.12 Sierra a novější verze

# <a name="linux"></a>[Linux](#tab/linux)

Průzkumník úložiště je k dispozici v [úložišti Snap](https://snapcraft.io/storage-explorer) Store pro většinu běžných distribucí Linuxu. Pro tuto instalaci doporučujeme úložiště Snap Store. Přichycení Průzkumníka úložiště nainstaluje všechny své závislosti a aktualizace při publikování nových verzí do úložiště Snap Store.

Podporované distribuce naleznete na [stránce instalace s přichycené aplikace](https://snapcraft.io/docs/installing-snapd).

Průzkumník úložiště vyžaduje použití správce hesel. Možná se budete muset připojit ke správci hesel ručně. Průzkumníka úložiště můžete připojit ke Správci hesel systému spuštěním následujícího příkazu:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Průzkumník úložiště je také k dispozici ke stažení *souboru .tar.gz.* Je nutné nainstalovat závislosti ručně. Následující distribuce linuxové podpory *.tar.gz* instalace:

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

Instalace *.tar.gz* může fungovat na jiných distribucích, ale pouze tyto uvedené jsou oficiálně podporovány.

Další nápovědu k instalaci Průzkumníka úložiště na Linux najdete v tématu [závislosti Linuxu](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies) v průvodci odstraňováním potíží s Průzkumníkem azure storage exploreru.

---

## <a name="download-and-install"></a>Stažení a instalace

Pokud si chcete stáhnout a nainstalovat Průzkumníka úložiště, přečtěte si informace [o Průzkumníku úložišť Azure](https://www.storageexplorer.com).

## <a name="connect-to-a-storage-account-or-service"></a>Připojení k účtu úložiště nebo službě

Průzkumník služby Storage nabízí několik způsobů, jak se připojit k účtům úložiště. Obecně můžete buď:

* [Přihlaste se k Azure a získejte přístup k předplatným a jejich prostředkům.](#sign-in-to-azure)
* [Připojení konkrétního prostředku úložiště nebo CosmosDB](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

> [!NOTE]
> Pro plný přístup k prostředkům po přihlášení, Průzkumník úložiště vyžaduje oprávnění pro správu (Azure Resource Manager) a data vrstvy. To znamená, že potřebujete oprávnění Služby Azure Active Directory (Azure AD), která vám poskytnou přístup k vašemu účtu úložiště, kontejnerům v účtu a datům v kontejnerech. Pokud máte oprávnění jenom v datové vrstvě, zvažte [přidání prostředku prostřednictvím Azure AD](#add-a-resource-via-azure-ad). Další informace o konkrétních oprávněních, která Průzkumník úložiště vyžaduje, najdete v [průvodci poradcem při potížích s Průzkumníkem úložišť Azure](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#rbac-permissions-issues).

1. V Průzkumníku úložiště vyberte **Zobrazit** > **správu účtů** nebo **vyberte tlačítko Spravovat účty.**

    ![Správa účtů][1]

1. **Správa účtů** teď zobrazuje všechny účty Azure, ke které jste se přihlásili. Chcete-li se připojit k **jinému**účtu, vyberte Přidat účet .

1. V **aplikaci Connect to Azure Storage**vyberte cloud Azure z prostředí **Azure** a přihlaste se k národnímu cloudu nebo zásobníku Azure. Po výběru prostředí vyberte **Další**.

    ![Možnost přihlášení][2]

    Průzkumník úložiště otevře stránku, na kterou se můžete přihlásit. Další informace najdete [v tématu Připojení průzkumníka úložiště k předplatnému azure zásobníku nebo účtu úložiště](/azure-stack/user/azure-stack-storage-connect-se).

1. Po úspěšném přihlášení pomocí účtu Azure se účet a předplatná Azure přidružená k tomuto účtu zobrazí v části **Správa účtů**. Vyberte **Všechna předplatná,** chcete-li přepnout výběr mezi všemi nebo žádnými z uvedených předplatných Azure. Vyberte předplatná Azure, se kterými chcete pracovat, a pak vyberte **Apply** (Použít).

    ![Výběr předplatných Azure][3]

    **EXPLORER** zobrazí účty úložiště přidružené k vybraným předplatným Azure.

    ![Vybraná předplatná Azure][4]

### <a name="attach-a-specific-resource"></a>Připojení určitého zdroje

Existuje několik způsobů, jak se připojit k prostředku v Průzkumníku úložiště:

* [Přidejte prostředek prostřednictvím Azure AD](#add-a-resource-via-azure-ad). Pokud máte oprávnění jenom v datové vrstvě, použijte tuto možnost k přidání kontejneru objektů blob nebo kontejneru úložiště objektů blob Azure Data Lake Storage Gen2.
* [Použijte připojovací řetězec](#use-a-connection-string). Tuto možnost použijte, pokud máte připojovací řetězec k účtu úložiště. Průzkumník úložiště podporuje připojovací řetězce klíčových i [sdílených přístupových podpisů.](storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [Použití identifikátoru URI s uzajášnem sdílený přístupový podpis](#use-a-shared-access-signature-uri). Pokud máte [identifikátor URI sdíleného přístupového podpisu](storage/common/storage-dotnet-shared-access-signature-part-1.md) k kontejneru objektů blob, sdílené složce, frontě nebo tabulce, použijte jej k připojení k prostředku. Chcete-li získat identifikátor URI podpisu sdíleného přístupu, můžete použít [Průzkumníka úložiště](#generate-a-sas-in-storage-explorer) nebo [portál Azure](https://portal.azure.com).
* [Použijte název a klíč](#use-a-name-and-key). Pokud znáte některý z klíčů účtu k účtu úložiště, můžete tuto možnost použít k rychlému připojení. Nastránce účtu úložiště najdete klíče výběrem**přístupových klíčů** **nastavení** > na webu [Azure Portal](https://portal.azure.com).
* [Připojte k místnímu emulátoru](#attach-to-a-local-emulator). Pokud používáte jeden z dostupných emulátorů Azure Storage, použijte tuto možnost pro snadné připojení k emulátoru.
* [Připojte se k účtu Azure Cosmos DB pomocí připojovacího řetězce](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string). Tuto možnost použijte, pokud máte připojovací řetězec k instanci CosmosDB.
* [Připojte se k úložišti Azure Data Lake Store pomocí identifikátoru URI](#connect-to-azure-data-lake-store-by-uri). Tuto možnost použijte, pokud máte identifikátor URI do Azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Přidání prostředku přes Azure AD

1. Výběrem symbolu **Připojit** **otevřete možnost Připojit k úložišti Azure**.

    ![Možnost Připojit k úložišti Azure][9]

1. Pokud jste to ještě neudělali, použijte možnost **Přidat účet Azure** k přihlášení k účtu Azure, který má přístup k prostředku. Po přihlášení se vraťte do **služby Připojit se k Úložišti Azure**.

1. Vyberte **Přidat prostředek přes Azure Active Directory (Azure AD)** a pak vyberte **Další**.

1. Vyberte účet Azure a klienta. Tyto hodnoty musí mít přístup k prostředku úložiště, ke kterému chcete připojit. Vyberte **další**.

1. Zvolte typ prostředku, který chcete připojit. Zadejte informace potřebné pro připojení. 

   Informace zadané na této stránce závisí na typu přidávaného prostředku. Ujistěte se, že jste zvolili správný typ prostředku. Po zadání požadovaných informací vyberte **Další**.

1. Zkontrolujte **souhrn připojení a** ujistěte se, že jsou všechny informace správné. Pokud ano, vyberte **Připojit**. V opačném případě se výběrem **možnosti Zpět** vraťte na předchozí stránky a opravte nesprávné informace.

Po úspěšném přidání připojení přejde strom prostředků do uzlu, který představuje připojení. Prostředek se zobrazí v části **Místní & připojené** > **účty** > úložiště **(připojené kontejnery)** > **kontejnery objektů blob**. Pokud Průzkumník úložiště nemohl přidat vaše připojení nebo pokud po úspěšném přidání připojení nemáte přístup k datům, přečtěte si [průvodce odstraňováním potíží s Průzkumníkem úložišť Azure](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-connection-string"></a>Použití připojovacího řetězce

1. Výběrem symbolu **Připojit** **otevřete možnost Připojit k úložišti Azure**.

    ![Možnost Připojit k úložišti Azure][9]

1. Vyberte **Použít připojovací řetězec**a pak vyberte **Další**.

1. Zvolte zobrazovaný název připojení a zadejte připojovací řetězec. Potom vyberte **Další**.

1. Zkontrolujte **souhrn připojení a** ujistěte se, že jsou všechny informace správné. Pokud ano, vyberte **Připojit**. V opačném případě se výběrem **možnosti Zpět** vraťte na předchozí stránky a opravte nesprávné informace.

Po úspěšném přidání připojení přejde strom prostředků do uzlu, který představuje připojení. Prostředek se zobrazí v části **Místní & připojené** > **účty úložiště**. Pokud Průzkumník úložiště nemohl přidat vaše připojení nebo pokud po úspěšném přidání připojení nemáte přístup k datům, přečtěte si [průvodce odstraňováním potíží s Průzkumníkem úložišť Azure](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-shared-access-signature-uri"></a>Použití identifikátoru URI sdíleného přístupového podpisu

1. Výběrem symbolu **Připojit** **otevřete možnost Připojit k úložišti Azure**.

    ![Možnost Připojit k úložišti Azure][9]

1. Vyberte **Použít identifikátor URI se sdíleným přístupovým podpisem (SAS)** a pak vyberte **Další**.

1. Zvolte zobrazovaný název připojení a zadejte identifikátor URI sdíleného přístupového podpisu. Koncový bod služby pro typ prostředku, který připojujete, by se měl automaticky vyplnit. Pokud používáte vlastní koncový bod, je možné, že ne. Vyberte **další**.

1. Zkontrolujte **souhrn připojení a** ujistěte se, že jsou všechny informace správné. Pokud ano, vyberte **Připojit**. V opačném případě se výběrem **možnosti Zpět** vraťte na předchozí stránky a opravte nesprávné informace.

Po úspěšném přidání připojení přejde strom prostředků do uzlu, který představuje připojení. Prostředek se zobrazí v části **Místní & připojené** > **účty** > úložiště **(připojené kontejnery)** > *uzel služby pro typ kontejneru, který jste připojili*. Pokud Průzkumník úložiště nemohl přidat vaše připojení, přečtěte si [průvodce odstraňováním potíží s Průzkumníkem úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting). Pokud po úspěšném přidání připojení nemáte přístup k datům, přečtěte si průvodce odstraňováním potíží.

#### <a name="use-a-name-and-key"></a>Použití názvu a klíče

1. Výběrem symbolu **Připojit** **otevřete možnost Připojit k úložišti Azure**.

    ![Možnost Připojit k úložišti Azure][9]

1. Vyberte **Použít název a klíč účtu úložiště a**pak vyberte **Další**.

1. Zvolte zobrazovaný název připojení.

1. Zadejte název účtu úložiště a některý z jeho přístupových klíčů.

1. Vyberte **doménu úložiště,** kterou chcete použít, a pak vyberte **Další**.

1. Zkontrolujte **souhrn připojení a** ujistěte se, že jsou všechny informace správné. Pokud ano, vyberte **Připojit**. V opačném případě se výběrem **možnosti Zpět** vraťte na předchozí stránky a opravte nesprávné informace.

Po úspěšném přidání připojení přejde strom prostředků do uzlu, který představuje připojení. Prostředek se zobrazí v části **Místní & připojené** > **účty úložiště**. Pokud Průzkumník úložiště nemohl přidat vaše připojení nebo pokud po úspěšném přidání připojení nemáte přístup k datům, přečtěte si [průvodce odstraňováním potíží s Průzkumníkem úložišť Azure](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="attach-to-a-local-emulator"></a>Připojení k místnímu emulátoru

Průzkumník úložiště aktuálně podporuje dva oficiální emulátory úložiště:

* [Emulátor Úložiště Azure](storage/common/storage-use-emulator.md) (jenom Windows)
* [Azurit (Windows,](https://github.com/azure/azurite) macOS nebo Linux)

Pokud emulátor naslouchá na výchozích portech, můžete pro přístup k emulátoru použít uzel **Emulátor – výchozí porty.** Vyhledejte **emulátor – výchozí porty** v části **Místní & připojené** > **účty úložiště**.

Pokud chcete pro připojení použít jiný název nebo pokud emulátor není spuštěn na výchozích portech, postupujte takto:

1. Spusťte emulátor. Zadáním `AzureStorageEmulator.exe status` příkazu zobrazíte porty pro každý typ služby.

   > [!IMPORTANT]
   > Průzkumník úložiště automaticky nespustí emulátor. Musíte jej spustit ručně.

1. Výběrem symbolu **Připojit** **otevřete možnost Připojit k úložišti Azure**.

    ![Možnost Připojit k úložišti Azure][9]

1. Vyberte **Připojit k místnímu emulátoru**a pak vyberte **Další**.

1. Zvolte zobrazovaný název připojení a zadejte porty, na kterých emulátor naslouchá pro každý typ služby. **Připojit k místní emulátor** unaznačuje výchozí hodnoty portu pro většinu emulátorů. **Port souborů** je prázdný, protože ani jeden z oficiálních emulátorů aktuálně nepodporuje službu Soubory. Pokud emulátor, který používáte, podporuje soubory, můžete zadat port, který chcete použít. Potom vyberte **Další**.

1. Zkontrolujte **souhrn připojení** a ujistěte se, že jsou všechny informace správné. Pokud ano, vyberte **Připojit**. V opačném případě se výběrem **možnosti Zpět** vraťte na předchozí stránky a opravte nesprávné informace.

Po úspěšném přidání připojení přejde strom prostředků do uzlu, který představuje připojení. Uzel by se měl zobrazit v části **Místní & připojené** > **účty úložiště**. Pokud Průzkumník úložiště nemohl přidat vaše připojení nebo pokud po úspěšném přidání připojení nemáte přístup k datům, přečtěte si [průvodce odstraňováním potíží s Průzkumníkem úložišť Azure](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Připojení k účtu Azure Cosmos DB pomocí připojovacího řetězce

Místo správy účtů Azure Cosmos DB prostřednictvím předplatného Azure se můžete připojit k Azure Cosmos DB pomocí připojovacího řetězce. Postup pro připojení je následující:

1. V **části EXPLORER**rozbalte **položku Místní & připojeno**, klikněte pravým tlačítkem myši na účty **Cosmos DB**a vyberte připojit k Azure **Cosmos DB**.

    ![Připojení ke službě Azure Cosmos DB pomocí připojovacího řetězce][21]

1. Vyberte rozhraní API DB Azure Cosmos, zadejte data **připojovacího řetězce** a pak vyberte **OK** pro připojení účtu Azure Cosmos DB. Informace o tom, jak načíst připojovací řetězec, najdete v [tématu Správa účtu Azure Cosmos](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![Připojovací řetězec][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Připojení k úložišti Azure Data Lake Store pomocí identifikátoru URI

Můžete získat přístup k prostředku, který není ve vašem předplatném. Potřebujete někoho, kdo má přístup k tomuto prostředku, aby vám identifikátor URI prostředku. Po přihlášení se připojte k úložišti Data Lake Store pomocí identifikátoru URI. Postup pro připojení je následující:

1. V **části EXPLORER**rozbalte **položku Místní & připojeno**.

1. Klikněte pravým tlačítkem myši na **Položku Data Lake Storage Gen1**a vyberte **Připojit k úložišti datových jezer Gen1**.

    ![Připojení k místní nabídce Úložiště datových jezer](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. Zadejte identifikátor URI a pak vyberte **OK**. Úložiště datového jezera se zobrazí v části **Úložiště datových jezer**.

    ![Připojení k výsledku úložiště datových jezer](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

Tento příklad používá Data Lake Storage Gen1. Azure Data Lake Storage Gen2 je teď k dispozici. Další informace najdete v tématu [Co je Azure Data Lake Storage Gen1](./data-lake-store/data-lake-store-overview.md).

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Generování sdíleného přístupového podpisu v Průzkumníku úložiště<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>Podpis sdíleného přístupu na úrovni účtu

1. Klikněte pravým tlačítkem myši na účet úložiště, který chcete sdílet, a pak vyberte **Získat sdílený přístupový podpis**.

    ![Možnost kontextové nabídky podpisu se sdíleným přístupem][14]

1. V **poli Sdílený přístupový podpis**zadejte požadovaný časový rámec a oprávnění pro účet a vyberte příkaz **Vytvořit**.

    ![Získání sdíleného přístupového podpisu][15]

1. Zkopírujte řetězec **připojení** nebo **nezpracovaný řetězec dotazu** do schránky.

### <a name="service-level-shared-access-signature"></a>Podpis sdíleného přístupu na úrovni služby

Sdílený přístupový podpis můžete získat na úrovni služby. Další informace naleznete [v tématu Získání SAS pro kontejner objektů blob](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

## <a name="search-for-storage-accounts"></a>Vyhledávání účtů úložiště

Chcete-li najít prostředek úložiště, můžete hledat v podokně **PRŮZKUMNÍK.**

Při zadávání textu do vyhledávacího pole průzkumník a úložiště zobrazí všechny prostředky, které odpovídají hodnotě hledání, kterou jste do tohoto okamžiku zadali. Tento příklad ukazuje hledání **koncových bodů**:

![Vyhledávání účtu úložiště][23]

> [!NOTE]
> Chcete-li hledání urychlit, zrušte výběr všech předplatných, která neobsahují položku, kterou hledáte, pomocí **správy účtů.** Můžete také klepnout pravým tlačítkem myši na uzel a vybrat **možnost Hledat odtud** a začít hledat z určitého uzlu.
>
>

## <a name="next-steps"></a>Další kroky

* [Správa prostředků úložiště objektů blob Azure pomocí Průzkumníka úložiště](vs-azure-tools-storage-explorer-blobs.md)
* [Práce s daty s využitím Průzkumníka služby Azure Storage](./cosmos-db/storage-explorer.md)
* [Správa prostředků Azure Data Lake Store pomocí Průzkumníka úložišť](./data-lake-store/data-lake-store-in-storage-explorer.md)

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

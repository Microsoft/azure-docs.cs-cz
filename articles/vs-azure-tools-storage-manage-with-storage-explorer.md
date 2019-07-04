---
title: Začínáme s Průzkumníkem služby Storage | Dokumentace Microsoftu
description: Správa prostředků Azure storage pomocí Průzkumníka služby Storage
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 04/22/2019
ms.author: cawa
ms.openlocfilehash: f7dd6d3d30f34ba2c69b40111bb28d484ce572e7
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508733"
---
# <a name="get-started-with-storage-explorer"></a>Začínáme se Storage Explorerem

## <a name="overview"></a>Přehled

Průzkumník služby Azure Storage je samostatná aplikace, která umožňuje jednoduchá práci s daty Azure Storage ve Windows, macOS a Linuxu. V tomto článku se dozvíte několik možností, jak připojit se k a spravovat vaše účty úložiště Azure.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>Požadavky

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Průzkumník služby Azure Storage se podporuje v následujících verzích Windows:

* Windows 10 (doporučeno)
* Windows 8
* Windows 7

Pro všechny verze systému Windows, rozhraní .NET Framework 4.6.2 nebo vyšší se vyžaduje.

[Stažení a instalace Průzkumníka služby Storage](https://www.storageexplorer.com)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Průzkumník služby Azure Storage se podporuje v následujících verzích systému macOS:

* macOS 10.12 "Sierra" a novější verze

[Stažení a instalace Průzkumníka služby Storage](https://www.storageexplorer.com)

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Průzkumník služby Azure Storage se podporuje v následujících distribucích systému Linux:

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

Průzkumník služby Azure Storage může pracovat na jiné distribuce, ale pouze uvedené výše se oficiálně podporuje.

Další nápovědu k instalaci Průzkumníka služby Storage na platformě Linux, najdete v článku [Průvodce odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies).

Průzkumník služby Azure Storage [zpráva k vydání verze](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) obsahují konkrétní kroky pro některých distribucích.

[Stažení a instalace Průzkumníka služby Storage](https://www.storageexplorer.com)

---

## <a name="connect-to-a-storage-account-or-service"></a>Připojení k účtu úložiště nebo službě

Průzkumník služby Storage nabízí několik způsobů, jak se připojit k účtům úložiště. Obecně máte tyto možnosti:

* [Přihlaste se k Azure pro přístup k předplatným a jejich prostředků](#sign-in-to-azure)
* [Připojit prostředek konkrétní úložiště nebo cosmos DB](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Přihlásit se k Azure

> [!NOTE]
> K úplnému přístupu k prostředkům po přihlášení, vyžaduje Průzkumníka služby Storage management (ARM) a data vrstev oprávnění. To znamená, že budete potřebovat oprávnění Azure AD, které poskytují přístup k účtu úložiště, kontejnerů v účtu a data v kontejnerech. Pokud máte oprávnění pouze na datové vrstvě, zvažte použití [připojit pomocí Azure AD](#add-a-resource-via-azure-ad). Další informace o konkrétní oprávnění vyžaduje Průzkumníka služby Storage, najdete v článku [Průvodce odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#role-based-access-control-permission-issues).
>
>

1. V Průzkumníku služby Storage vyberte **spravovat účty** přejdete **Panel pro správu účtu**.

    ![Správa účtů][1]

2. V levém podokně se teď zobrazí všem účtům Azure, které jste přihlášeni. Chcete-li připojit k jinému účtu, vyberte **přidat účet**

3. Pokud chcete k přihlašování do národních cloudů nebo Azure Stack, klikněte na **prostředí Azure** rozevírací nabídku a zvolte který Azure cloud, který chcete použít. Po výběru prostředí, klikněte na tlačítko **přihlásit...**  tlačítko. Pokud se přihlašujete ke službě Azure Stack, najdete v článku [připojení Storage Exploreru k předplatnému Azure Stack](/azure-stack/user/azure-stack-storage-connect-se) Další informace.

    ![Přihlaste se možnost][2]

4. Po úspěšné registraci se pomocí účtu Azure, účet a předplatných Azure, které jsou přidružené k tomuto účtu se přidají do levého podokna. Vyberte předplatná Azure, které chcete pracovat a pak vyberte **použít** (výběr **Všechna předplatná:** přepínáte výběr všech nebo žádných z uvedených předplatných Azure).

    ![Výběr předplatných Azure][3]

    V levém podokně se zobrazí všechny účty úložišť, přidružené k vybraným předplatným Azure.

    ![Vybraná předplatná Azure][4]

### <a name="attach-a-specific-resource"></a>Připojit konkrétní prostředek
    
Existuje široká škála možností pro připojení zdroje do Průzkumníka služby Storage. Můžete:

* [Přidání prostředku přes Azure AD](#add-a-resource-via-azure-ad): Pokud máte oprávnění pouze na datové vrstvě, můžete použít tuto možnost pro přidání kontejneru objektů Blob nebo kontejneru objektů Blob ADLS Gen2.
* [Použijte připojovací řetězec](#use-a-connection-string): Pokud máte připojovací řetězec pro účet úložiště. Průzkumník služby Storage podporuje oba klíče a [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) připojovací řetězce.
* [Používání identifikátoru URI SAS](#use-a-sas-uri): Pokud máte [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) identifikátor URI pro kontejner objektů Blob, sdílené složky, fronty nebo tabulky. Chcete-li získat identifikátor URI SAS, můžete buď používat [Průzkumníka služby Storage](#generate-a-sas-in-storage-explorer) nebo [webu Azure portal](https://portal.azure.com).
* [Použít název a klíč](#use-a-name-and-key): Pokud znáte jeden z klíčů účtu do svého účtu úložiště, můžete rychle připojit tuto možnost. Klíče účtu úložiště jsou umístěné v účtu úložiště **přístupové klíče** okně na [webu Azure portal](https://portal.azure.com).
* [Připojit k místní emulátor](#attach-to-a-local-emulator): Pokud pracujete s některou z dostupných emulátory Azure Storage, tuto možnost použijte ke snadnému připojení k vaší emulátoru.
* [Připojení k účtu služby Azure Cosmos DB pomocí připojovacího řetězce](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): Pokud máte připojovací řetězec do instance služby cosmos DB.
* [Připojení k Azure Data Lake Store pomocí identifikátoru URI](#connect-to-azure-data-lake-store-by-uri): Pokud je třeba identifikátor URI Azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Přidání prostředku přes Azure AD

1. Otevřít **dialogové okno Připojit** kliknutím na **tlačítko pro připojení** na levé straně, svislé nástrojů.

    ![Možnost Připojit k úložišti Azure][9]

2. Pokud jste tak již neučinili, použijte **přidat účet Azure** pro přihlášení k účtu Azure, který má přístup k prostředku. Po přihlášení na oplátku k **dialogové okno Připojit**.

3. Vyberte **přidat prostředek přes Azure Active Directory (Azure AD)** možnost a klikněte na tlačítko **Další**.

4. Vyberte účet Azure, který má přístup k prostředku úložiště, který chcete připojit a předplatné, prostředek je v a pak klikněte na tlačítko **Další**.

5. Vyberte typ prostředku, který chcete připojit a pak zadejte informace potřebné pro připojení. Vstupy na této stránce se změní v závislosti na tom, jaký typ prostředků chcete přidat. Nezapomeňte vybrat správný typ prostředku. Jakmile vyplníte potřebné informace, klikněte na tlačítko **Další**.

6. Zkontrolujte souhrn připojení a ujistěte se, že se všechny informace správné. Pokud všechny informace správné klikněte **připojit**, jinak zpět na předchozí stránky s **zpět** tlačítko a opravte všechny chybné informace.

Po úspěšném přidání připojení strom prostředků bude automaticky přejdete do uzlu představující připojení. Pokud z nějakého důvodu, nevypadá podle **místní a připojené** → **účty úložiště** → **(kontejnery připojené)** → **kontejnery objektů Blob** . Pokud Průzkumník služby Storage se nepodařilo přidat připojení, nebo pokud nelze přistupovat ke svým datům po úspěšném připojení a pak najdete [Průvodce odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) nápovědu.

#### <a name="use-a-connection-string"></a>Použijte připojovací řetězec

1. Otevřít **dialogové okno Připojit** kliknutím na **tlačítko pro připojení** na levé straně, svislé nástrojů.

    ![Možnost Připojit k úložišti Azure][9]

2. Vyberte **použít připojovací řetězec** možnost a klikněte na tlačítko **Další**.

3. Zvolte zobrazované jméno pro připojení a zadejte připojovací řetězec. Pak klikněte na tlačítko **Další**.

4. Zkontrolujte souhrn připojení a ujistěte se, že se všechny informace správné. Pokud všechny informace správné klikněte **připojit**, jinak zpět na předchozí stránky s **zpět** tlačítko a opravte všechny chybné informace.

Po úspěšném přidání připojení strom prostředků bude automaticky přejdete do uzlu představující připojení. Pokud z nějakého důvodu, nevypadá podle **místní a připojené** → **účty úložiště**. Pokud Průzkumník služby Storage se nepodařilo přidat připojení, nebo pokud nelze přistupovat ke svým datům po úspěšném připojení a pak najdete [Průvodce odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) nápovědu.

#### <a name="use-a-sas-uri"></a>Používání identifikátoru URI SAS

1. Otevřít **dialogové okno Připojit** kliknutím na **tlačítko pro připojení** na levé straně, svislé nástrojů.

    ![Možnost Připojit k úložišti Azure][9]

2. Vyberte **pomocí sdíleného přístupového podpisu (SAS) URI** možnost a klikněte na tlačítko **Další**.

3. Zvolte zobrazované jméno pro připojení a zadejte váš identifikátor URI SAS. Koncový bod služby pro typ prostředku, který při připojování by měly automatické vyplňování. Pokud používáte vlastní koncový bod je možné, že nesmí být. Klikněte na **Další**.

4. Zkontrolujte souhrn připojení a ujistěte se, že se všechny informace správné. Pokud všechny informace správné klikněte **připojit**, jinak zpět na předchozí stránky s **zpět** tlačítko a opravte všechny chybné informace.

Po úspěšném přidání připojení strom prostředků bude automaticky přejdete do uzlu představující připojení. Pokud z nějakého důvodu, nevypadá podle **místní a připojené** → **účty úložiště** → **(kontejnery připojené)** → **uzlu služeb pro daný typ kontejner je připojit**. Pokud Průzkumník služby Storage se nepodařilo přidat připojení, nebo pokud nelze přistupovat ke svým datům po úspěšném připojení a pak najdete [Průvodce odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) nápovědu.

#### <a name="use-a-name-and-key"></a>Použít název a klíč

1. Otevřít **dialogové okno Připojit** kliknutím na **tlačítko pro připojení** na levé straně, svislé nástrojů.

    ![Možnost Připojit k úložišti Azure][9]

2. Vyberte **použít název účtu úložiště a klíč** možnost a klikněte na tlačítko **Další**.

3. Zvolte zobrazovaný název vašeho připojení.

4. Zadejte název svého účtu úložiště a jeden z jeho přístupových klíčů.

5. Zvolte **doména úložiště** použít, a potom klikněte na **Další**.

4. Zkontrolujte souhrn připojení a ujistěte se, že se všechny informace správné. Pokud všechny informace správné klikněte **připojit**, jinak zpět na předchozí stránky s **zpět** tlačítko a opravte všechny chybné informace.

Po úspěšném přidání připojení strom prostředků bude automaticky přejdete do uzlu představující připojení. Pokud z nějakého důvodu, nevypadá podle **místní a připojené** → **účty úložiště**. Pokud Průzkumník služby Storage se nepodařilo přidat připojení, nebo pokud nelze přistupovat ke svým datům po úspěšném připojení a pak najdete [Průvodce odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) nápovědu.

#### <a name="attach-to-a-local-emulator"></a>Připojit k místní emulátor

Emulátory podporuje Průzkumník služby Storage na všech platformách. Dva aktuálně oficiální emulátory k dispozici jsou:
* [Emulátor úložiště Azure](storage/common/storage-use-emulator.md) (jenom Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS nebo Linux)

Pokud vaše emulátor běží na výchozích portech můžete použít **emulátor – výchozí porty** uzlu, na kterém najdete vždy v části **místní a připojené** → **účty úložiště** , rychle se dostat k vaší emulátoru. Pokud chcete použít jiný název vašeho připojení nebo pokud se vaše emulátor neběží na výchozích portech, postupujte následujících kroků.

1. Spusťte vaše emulátor. Pokud můžete, poznamenejte si, jaké porty emulátor naslouchá pro každý typ služby. Budete potřebovat později tyto informace znát.

   > [!IMPORTANT]
   > Storage Explorer nespustí automaticky vaše emulátoru. Je nutné spustit ho sami.

2. Otevřít **dialogové okno Připojit** kliknutím na **tlačítko pro připojení** na levé straně, svislé nástrojů.

    ![Možnost Připojit k úložišti Azure][9]

3. Vyberte **připojit k místní emulátor** možnost a klikněte na tlačítko **Další**.

4. Zvolte zobrazované jméno pro připojení a zadejte porty, které vaše emulátor naslouchá pro každý typ služby. Ve výchozím nastavení bude obsahovat textových polích výchozí hodnoty portů pro většina emulátorů. **Soubory port** také zbývá ve výchozím nastavení prázdné jako ani jeden z oficiální emulátorů v současné době podporují služby soubory. Pokud používáte emulátor podporují i když, potom můžete zadat v port, který se používá. Klikněte na **Další**.

5. Zkontrolujte souhrn připojení a ujistěte se, že se všechny informace správné. Pokud všechny informace správné klikněte **připojit**, jinak zpět na předchozí stránky s **zpět** tlačítko a opravte všechny chybné informace.

Po úspěšném přidání připojení strom prostředků bude automaticky přejdete do uzlu představující připojení. Pokud z nějakého důvodu, nevypadá podle **místní a připojené** → **účty úložiště**. Pokud Průzkumník služby Storage se nepodařilo přidat připojení, nebo pokud nelze přistupovat ke svým datům po úspěšném připojení a pak najdete [Průvodce odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) nápovědu.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Připojení k účtu služby Azure Cosmos DB pomocí připojovacího řetězce

Kromě Správa účtů služby Azure Cosmos DB pomocí předplatného Azure, je alternativní způsob připojení ke službě Azure Cosmos DB pomocí připojovacího řetězce. Pomocí následujícího postupu se připojte pomocí připojovacího řetězce.

1. V levém stromě vyhledejte **Místní a připojené**, klikněte pravým tlačítkem na **Účty služby Azure Cosmos DB** a zvolte **Připojit ke službě Azure Cosmos DB...**

    ![připojení ke službě Azure Cosmos DB pomocí připojovacího řetězce][21]

2. Zvolte rozhraní API služby Azure Cosmos DB, vložte váš **připojovací řetězec**a potom klikněte na tlačítko **OK** připojte k účtu Azure Cosmos DB. Informace o načtení připojovacího řetězce najdete v tématu popisujícím [Získání připojovacího řetězce](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![connection-string][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Připojení k Azure Data Lake Store pomocí identifikátoru URI

Představte si, že chcete získat přístup k prostředkům, které neexistují ve vašem předplatném. Ostatní vám však udělí oprávnění k získání jejich identifikátoru URI. V takovém případě se po přihlášení můžete připojit ke službě Data Lake Store s použitím tohoto identifikátoru URI. Postup najdete v následujících krocích.

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte **Místní a připojené**.
3. Klikněte pravým tlačítkem na **Data Lake Store** a v místní nabídce vyberte **Připojit ke službě Data Lake Store**.

    ![místní nabídka Připojit ke službě Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Zadejte identifikátor URI a nástroj vás pak přesměruje na adresu URL, kterou jste právě zadali.

    ![dialogové okno Připojit ke službě Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![výsledek připojení ke službě Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>Generovat SAS v Průzkumníku služby Storage

### <a name="account-level-sas"></a>ZÁSADY na úrovni účtu

1. Klikněte pravým tlačítkem na účet úložiště, který chcete sdílet a pak vyberte **získat sdílený přístupový podpis...** .

    ![Možnost místní nabídky Získat sdílený přístupový podpis][14]

2. V **vygenerovat sdílený přístupový podpis** dialogového okna zadejte časový rámec a oprávnění pro účet a potom klikněte na tlačítko **vytvořit** tlačítko.

    ![Získat dialogové okno SAS][15]

3. Teď můžete kopírovat **připojovací řetězec** nebo nezpracovaný **řetězec dotazu** do schránky.

### <a name="service-level-sas"></a>ZÁSADY na úrovni služby

[Jak získat SAS pro kontejner objektů blob v Průzkumníku služby Storage](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>Vyhledávání účtů úložiště

Pokud je potřeba najít prostředek úložiště a není známo, kde jsou, do vyhledávacího pole v horní části v levém podokně můžete použít k vyhledání prostředku.

Při psaní do vyhledávacího pole, v levém podokně zobrazí všechny prostředky, které odpovídající hledané hodnotě, kterou jste zatím zadali až k danému bodu. Například hledání **koncové body** je znázorněno na následujícím snímku obrazovky:

![Vyhledávání účtu úložiště][23]

> [!NOTE]
> Použití **Panel pro správu účtu** zrušte výběr žádné předplatné, které neobsahují položky hledáte, zlepšit dobu spuštění hledání. Můžete také kliknout pravým tlačítkem na uzel a vyberte **vyhledávání z zde** spustit vyhledávání od konkrétní uzel.
>
>

## <a name="next-steps"></a>Další postup

* [Správa prostředků Azure Blob Storage pomocí Storage Exploreru](vs-azure-tools-storage-explorer-blobs.md)
* [Správa služby Azure Cosmos DB v Průzkumníku služby Azure Storage (Preview)](./cosmos-db/storage-explorer.md)
* [Správa prostředků Azure Data Lake Store pomocí Průzkumníka služby Storage](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-SignInSelected.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccountPanel.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/GetSharedAccessSignature.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SharedAccessSignatureDialog.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Search.png

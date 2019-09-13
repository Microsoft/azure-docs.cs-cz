---
title: Začínáme s Průzkumník služby Storage | Microsoft Docs
description: Správa prostředků služby Azure Storage pomocí Průzkumník služby Storage
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 04/22/2019
ms.author: cawa
ms.openlocfilehash: c4aad2f2f5bca25ead03518b2de9ac9315172052
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934745"
---
# <a name="get-started-with-storage-explorer"></a>Začínáme s Průzkumník služby Storage

## <a name="overview"></a>Přehled

Průzkumník služby Microsoft Azure Storage je samostatná aplikace, která umožňuje snadnou práci s Azure Storagemi daty v systémech Windows, macOS a Linux. V tomto článku se dozvíte několik způsobů, jak se připojit k účtům Azure Storage a spravovat je.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>Požadavky

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Průzkumník služby Storage se podporuje v následujících verzích Windows:

* Windows 10 (doporučeno)
* Windows 8
* Windows 7

Pro všechny verze Windows se vyžaduje .NET Framework 4.6.2 nebo novější.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Průzkumník služby Storage se podporuje v následujících verzích macOS:

* macOS 10,12 "Sierra" a novější verze

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Průzkumník služby Storage je k dispozici v [úložišti přichycení](https://snapcraft.io/storage-explorer) pro většinu běžných distribucí systému Linux a je doporučovanou metodou instalace. Modul snap-in Průzkumník služby Storage automaticky nainstaluje všechny jeho závislosti a aktualizace, protože nové verze jsou publikovány do obchodu s modulem snap-in.

Seznam podporovaných distribucí najdete na [stránce s přitahováním](https://snapcraft.io/docs/installing-snapd).

Průzkumník služby Storage vyžaduje použití Správce hesel, který může být nutné ručně připojit, jinak bude Průzkumník služby Storage fungovat správně. Pomocí následujícího příkazu se můžete připojit Průzkumník služby Storage k správci hesel vašeho systému:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Průzkumník služby Storage je také k dispozici jako soubor. tar. gz ke stažení, ale budete muset nainstalovat závislosti ručně. Instalace. tar. gz se podporuje v následujících distribucích systému Linux:

* Ubuntu 18,04 x64
* Ubuntu 16,04 x64
* Ubuntu 14,04 x64

Instalace. tar. gz může fungovat na dalších distribucích, ale oficiálně se podporují jenom ty, které jsou uvedené výše.

Další nápovědu k instalaci Průzkumník služby Storage na Linux najdete v [Průvodci odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies).

---

## <a name="download-and-install"></a>Stáhnout a nainstalovat

[Stažení a instalace Průzkumníka služby Storage](https://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Připojení k účtu úložiště nebo službě

Průzkumník služby Storage nabízí několik způsobů, jak se připojit k účtům úložiště. Obecně můžete:

* [Přihlaste se k Azure, abyste měli přístup k předplatným a jejich prostředkům.](#sign-in-to-azure)
* [Připojit konkrétní prostředek úložiště nebo CosmosDB](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

> [!NOTE]
> Pro úplný přístup k prostředkům po přihlášení Průzkumník služby Storage vyžaduje obě oprávnění ke správě (ARM) a datové vrstvě. To znamená, že potřebujete oprávnění služby Azure AD, která vám umožní přístup k vašemu účtu úložiště, kontejnerům v účtu a datům v kontejnerech. Pokud máte v datové vrstvě jenom oprávnění, zvažte použití [připojení ke službě Azure AD](#add-a-resource-via-azure-ad). Další informace o konkrétních oprávněních Průzkumník služby Storage vyžaduje, najdete v [Průvodci odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#role-based-access-control-permission-issues).

1. V Průzkumník služby Storage vyberte **Spravovat účty** a přejdete na **panel Správa účtů**.

    ![Spravovat účty][1]

2. V levém podokně se teď zobrazí všechny účty Azure, ke kterým jste se přihlásili. Pokud se chcete připojit k jinému účtu, vyberte **Přidat účet** .

3. Pokud se chcete přihlásit do národního cloudu nebo Azure Stack, klikněte na rozevírací seznam **prostředí Azure** a vyberte Cloud, který chcete použít. Po zvolení prostředí klikněte na tlačítko Přihlásit se. **..** . Další informace najdete v tématu [připojení Průzkumník služby Storage k předplatnému Azure Stack](/azure-stack/user/azure-stack-storage-connect-se).

    ![Možnost přihlášení][2]

4. Po úspěšném přihlášení pomocí účtu Azure se do levého podokna přidají účty a předplatná Azure přidružená k tomuto účtu. Vyberte předplatná Azure, se kterými chcete pracovat, a pak vyberte **použít** (výběr **všech předplatných:** přepíná výběr všech nebo žádných z uvedených předplatných Azure).

    ![Výběr předplatných Azure][3]

    V levém podokně se zobrazí všechny účty úložišť, přidružené k vybraným předplatným Azure.

    ![Vybraná předplatná Azure][4]

### <a name="attach-a-specific-resource"></a>Připojit konkrétní prostředek

K prostředku v Průzkumník služby Storage se můžete připojit pomocí různých možností:

* [Přidání prostředku přes Azure AD](#add-a-resource-via-azure-ad): Pokud máte v datové vrstvě jenom oprávnění, můžete tuto možnost použít k přidání kontejneru objektů BLOB nebo kontejneru objektů BLOB ADLS Gen2.
* [Použít připojovací řetězec](#use-a-connection-string): Pokud máte připojovací řetězec k účtu úložiště. Průzkumník služby Storage podporuje připojovací řetězce klíčů i [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) .
* [Použít identifikátor URI SAS](#use-a-sas-uri): Pokud máte identifikátor URI [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) pro kontejner objektů blob, sdílení souborů, frontu nebo tabulku. Identifikátor URI SAS můžete získat buď pomocí [Průzkumník služby Storage](#generate-a-sas-in-storage-explorer) nebo [Azure Portal](https://portal.azure.com).
* [Použijte název a klíč](#use-a-name-and-key): Pokud znáte některý z klíčů účtu pro účet úložiště, můžete tuto možnost použít k rychlému připojení. Klíče pro váš účet úložiště se nachází na panelu **přístupového klíče** účtu úložiště na [Azure Portal](https://portal.azure.com).
* [Připojit k místnímu emulátoru](#attach-to-a-local-emulator): Pokud používáte některý z dostupných Azure Storage emulátorů, použijte tuto možnost k snadnému připojení k emulátoru.
* [Připojení k účtu Azure Cosmos DB pomocí připojovacího řetězce](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): Pokud máte připojovací řetězec k instanci CosmosDB.
* [Připojit k Azure Data Lake Store podle identifikátoru URI](#connect-to-azure-data-lake-store-by-uri): Pokud máte identifikátor URI pro Azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Přidání prostředku přes Azure AD

1. Otevřete **dialogové okno připojit** kliknutím na **tlačítko připojit** na levé straně, svisle na panelu nástrojů.

    ![Možnost Připojit k úložišti Azure][9]

2. Pokud jste to ještě neudělali, použijte možnost **Přidat účet Azure** a přihlaste se k účtu Azure, který má přístup k prostředku. Po přihlášení se vraťte do **dialogového okna připojit**.

3. Vyberte možnost **Přidat prostředek prostřednictvím Azure Active Directory (Azure AD)** a klikněte na **Další**.

4. Vyberte účet Azure a tenanta, který má přístup k prostředku úložiště, ke kterému se chcete připojit. Klikněte na **Další**.

5. Zvolte typ prostředku, který chcete připojit, a pak zadejte informace potřebné k připojení. Vstupy na této stránce se budou měnit v závislosti na typu prostředku, který přidáváte. Ujistěte se, že jste zvolili správný typ prostředku. Jakmile vyplníte požadované informace, klikněte na tlačítko **Další**.

6. Zkontrolujte souhrn připojení a ujistěte se, že jsou všechny informace správné. Pokud všechny informace vypadají správně, klikněte na **připojit**. V opačném případě se vraťte na předchozí stránky pomocí tlačítka **zpět** a opravte nesprávné informace.

Po úspěšném přidání připojení bude strom prostředků automaticky přejít na uzel představující připojení. V případě z nějakého důvodu se můžete podívat na **místní & připojené** k **účtům úložiště** → **(připojené kontejnery)** → **BLOB** . Pokud Průzkumník služby Storage nedokázala přidat vaše připojení, nebo pokud nemůžete získat přístup k datům po úspěšném přidání připojení, požádejte o pomoc [Průvodce odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .

#### <a name="use-a-connection-string"></a>Použít připojovací řetězec

1. Otevřete **dialogové okno připojit** kliknutím na **tlačítko připojit** na levé straně, svisle na panelu nástrojů.

    ![Možnost Připojit k úložišti Azure][9]

2. Vyberte možnost **Použít připojovací řetězec** a klikněte na **Další**.

3. Vyberte zobrazovaný název pro připojení a zadejte do svého připojovacího řetězce. Pak klikněte na tlačítko **Další**.

4. Zkontrolujte souhrn připojení a ujistěte se, že jsou všechny informace správné. Pokud jsou všechny informace správné, klikněte na **připojit**, jinak se vraťte na předchozí stránky pomocí tlačítka **zpět** , aby se opravily nesprávné informace.

Po úspěšném přidání připojení bude strom prostředků automaticky přejít na uzel představující připojení. Můžete se také podívat na **místní & připojené** **účty úložiště** →, pokud z nějakého důvodu ne. Pokud Průzkumník služby Storage nedokázala přidat vaše připojení, nebo pokud nemůžete získat přístup k datům po úspěšném přidání připojení, požádejte o pomoc [Průvodce odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .

#### <a name="use-a-sas-uri"></a>Použít identifikátor URI SAS

1. Otevřete **dialogové okno připojit** kliknutím na **tlačítko připojit** na levé straně, svisle na panelu nástrojů.

    ![Možnost Připojit k úložišti Azure][9]

2. Vyberte možnost **použít identifikátor URI sdíleného přístupového podpisu (SAS)** a klikněte na **Další**.

3. Vyberte zobrazovaný název vašeho připojení a zadejte ho do svého identifikátoru URI SAS. Koncový bod služby pro typ prostředku, který připojujete, by měl automatické vyplňování. Pokud používáte vlastní koncový bod, možná to není možné. Klikněte na **Další**.

4. Zkontrolujte souhrn připojení a ujistěte se, že jsou všechny informace správné. Pokud jsou všechny informace správné, klikněte na tlačítko **připojit**, jinak se vraťte na předchozí stránky pomocí tlačítka **zpět** a opravte případné nesprávné informace.

Po úspěšném přidání připojení bude strom prostředků automaticky přejít na uzel představující připojení. Můžete se také podívat v části **místní & připojené** **účty úložiště** → **(připojené kontejnery)** → **uzel služby pro typ kontejneru, který jste připojili** z nějakého důvodu. Pokud Průzkumník služby Storage nedokázala přidat vaše připojení, nebo pokud nemůžete získat přístup k datům po úspěšném přidání připojení, požádejte o pomoc [Průvodce odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .

#### <a name="use-a-name-and-key"></a>Použít název a klíč

1. Otevřete **dialogové okno připojit** kliknutím na **tlačítko připojit** na levé straně, svisle na panelu nástrojů.

    ![Možnost Připojit k úložišti Azure][9]

2. Vyberte možnost **použít název a klíč účtu úložiště** a klikněte na **Další**.

3. Vyberte zobrazovaný název pro vaše připojení.

4. Zadejte název svého účtu úložiště a jeden z jeho přístupových klíčů.

5. Zvolte **doménu úložiště** , kterou chcete použít, a potom klikněte na **Další**.

4. Zkontrolujte souhrn připojení a ujistěte se, že jsou všechny informace správné. Pokud jsou všechny informace správné, klikněte na **připojit**, jinak se vraťte na předchozí stránky pomocí tlačítka **zpět** , aby se opravily nesprávné informace.

Po úspěšném přidání připojení bude strom prostředků automaticky přejít na uzel představující připojení. Můžete se také podívat na **místní & připojené** **účty úložiště** →, pokud z nějakého důvodu ne. Pokud Průzkumník služby Storage nedokázala přidat vaše připojení, nebo pokud nemůžete získat přístup k datům po úspěšném přidání připojení, požádejte o pomoc [Průvodce odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .

#### <a name="attach-to-a-local-emulator"></a>Připojit k místnímu emulátoru

Průzkumník služby Storage aktuálně podporuje dva oficiální emulátory úložiště:
* [Emulátor úložiště Azure](storage/common/storage-use-emulator.md) (Jenom Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS nebo Linux)

Pokud váš emulátor naslouchá na výchozích portech, můžete k rychlému přístupu k emulátoru použít uzel **výchozí porty emulátoru** (v části **místní & připojené** **účty úložiště**→).

Pokud chcete pro připojení použít jiný název nebo pokud emulátor neběží na výchozích portech:

1. Spusťte emulátor. V takovém případě si poznamenejte, jaké porty emulátoru naslouchá pro každý typ služby.

   > [!IMPORTANT]
   > Průzkumník služby Storage automaticky nespouští emulátor. Musíte ho spustit sami.

2. Otevřete **dialogové okno připojit** kliknutím na **tlačítko připojit** na levé straně, svisle na panelu nástrojů.

    ![Možnost Připojit k úložišti Azure][9]

3. Vyberte možnost **připojit k místnímu emulátoru** a klikněte na **Další**.

4. Vyberte zobrazovaný název pro připojení a zadejte do portů, na kterých váš emulátor naslouchá pro každý typ služby. Textová pole budou začínat výchozími hodnotami portů pro většinu emulátorů. **Port souborů** zůstane prázdný, protože žádná z oficiálních emulátorů v současné době nepodporuje službu souborů. Pokud emulátor, který používáte, podporuje soubory, můžete zadat port, který se používá. Klikněte na **Další**.

5. Zkontrolujte souhrn připojení a ujistěte se, že jsou všechny informace správné. Pokud jsou všechny informace správné, klikněte na tlačítko **připojit**, jinak se vraťte na předchozí stránky pomocí tlačítka **zpět** a opravte případné nesprávné informace.

Po úspěšném přidání připojení bude strom prostředků automaticky přejít na uzel představující připojení. Můžete se také podívat na **místní & připojené** **účty úložiště** →, pokud z nějakého důvodu ne. Pokud Průzkumník služby Storage nedokázala přidat vaše připojení, nebo pokud nemůžete získat přístup k datům po úspěšném přidání připojení, požádejte o pomoc [Průvodce odstraňováním potíží](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Připojení k účtu Azure Cosmos DB pomocí připojovacího řetězce

Kromě správy účtů Azure Cosmos DB prostřednictvím předplatného Azure je alternativním způsobem připojení k Azure Cosmos DB použití připojovacího řetězce. Pomocí následujícího postupu se připojte pomocí připojovacího řetězce.

1. V levém stromě vyhledejte **Místní a připojené**, klikněte pravým tlačítkem na **Účty služby Azure Cosmos DB** a zvolte **Připojit ke službě Azure Cosmos DB...**

    ![připojení k Azure Cosmos DB pomocí připojovacího řetězce][21]

2. Zvolte Azure Cosmos DB API, vložte **připojovací řetězec**a potom kliknutím na **OK** připojte účet Azure Cosmos DB. Informace o načtení připojovacího řetězce najdete v tématu popisujícím [Získání připojovacího řetězce](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![connection-string][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Připojení k Azure Data Lake Store podle identifikátoru URI

Pokud potřebujete přístup k prostředku, který není ve vašem předplatném, budete potřebovat někoho s přístupem k poskytnutí identifikátoru URI prostředku. Po přihlášení se můžete připojit k Data Lake Store pomocí identifikátoru URI pomocí následujících kroků:

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte **Místní a připojené**.
3. Klikněte pravým tlačítkem na **Data Lake Store** a v místní nabídce vyberte **Připojit ke službě Data Lake Store**.

    ![místní nabídka Připojit ke službě Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Zadejte identifikátor URI a nástroj vás pak přesměruje na adresu URL, kterou jste právě zadali.

    ![dialogové okno Připojit ke službě Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![výsledek připojení ke službě Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>Generovat SAS v Průzkumník služby Storage

### <a name="account-level-sas"></a>SAS na úrovni účtu

1. Klikněte pravým tlačítkem na účet úložiště, který chcete sdílet, a pak vyberte **načíst sdílený přístupový podpis...** .

    ![Možnost místní nabídky Získat sdílený přístupový podpis][14]

2. V dialogovém okně **Generovat sdílený přístupový podpis** určete časový rámec a oprávnění, které chcete pro účet použít. Klikněte na možnost **Vytvořit**.

    ![Dialogové okno získat SAS][15]

3. Nyní můžete buď zkopírovat **připojovací řetězec** nebo nezpracovaný **řetězec dotazu** do schránky.

### <a name="service-level-sas"></a>SAS na úrovni služby

[Jak získat SAS pro kontejner objektů BLOB v Průzkumník služby Storage](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>Vyhledávání účtů úložiště

Pokud potřebujete najít prostředek úložiště a nevíte, kde to je, můžete k vyhledání prostředku použít pole pro hledání v horní části levého podokna.

Při psaní do vyhledávacího pole se v levém podokně zobrazí všechny prostředky, které odpovídají hledané hodnotě, kterou jste do tohoto bodu zadali. Například hledání **koncových bodů** je znázorněno na následujícím snímku obrazovky:

![Vyhledávání účtu úložiště][23]

> [!NOTE]
> Pomocí **panelu Správa účtů** zrušte výběr všech předplatných, která neobsahují hledanou položku pro zlepšení doby provádění vyhledávání. Můžete také kliknout pravým tlačítkem myši na uzel a výběrem možnosti **Hledat z tohoto umístění** zahájit hledání z konkrétního uzlu.
>
>

## <a name="next-steps"></a>Další postup

* [Správa prostředků Azure Blob Storage pomocí Průzkumník služby Storage](vs-azure-tools-storage-explorer-blobs.md)
* [Správa Azure Cosmos DB v Průzkumník služby Storage (Preview)](./cosmos-db/storage-explorer.md)
* [Správa prostředků Azure Data Lake Store s využitím Průzkumník služby Storage](./data-lake-store/data-lake-store-in-storage-explorer.md)

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

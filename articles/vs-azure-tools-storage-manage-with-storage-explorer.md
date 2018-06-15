---
title: Začínáme se Storage Explorerem | Microsoft Docs
description: Správa prostředků Azure storage pomocí Storage Exploreru
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: ''
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2017
ms.author: cawa
ms.openlocfilehash: 2335872bcd7d3ea64e449d8b1a43f360d86bb4a0
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34304626"
---
# <a name="get-started-with-storage-explorer"></a>Začínáme se Storage Explorerem
## <a name="overview"></a>Přehled
Azure Storage Explorer je samostatná aplikace, která umožňuje snadno pracovat s daty Azure Storage ve Windows, systému macOS a Linux. V tomto článku se dozvíte několik způsobů připojení a správa účtům Azure storage.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>Požadavky

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
Azure Storage Explorer je podporován v následujících verzích systému Windows:

* Windows 10 (doporučeno)
* Windows 8
* Windows 7

Pro všechny verze systému Windows, rozhraní .NET Framework 4.6.2 nebo vyšší se vyžaduje.

[Stažení a instalace Průzkumníka služby Storage](http://www.storageexplorer.com)

# <a name="macostabmacos"></a>[macOS](#tab/macos)
Azure Storage Explorer je podporován v následujících verzích systému macOS:

* systému macOS 10.12 "Sierra" a novější verze

[Stažení a instalace Průzkumníka služby Storage](http://www.storageexplorer.com)

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
Azure Storage Explorer je podporován v následujících distribucích systému Linux:

* Ubuntu 16.04 x64 (doporučeno)
* Ubuntu 17.10 x64
* Ubuntu 14.04 x64

Azure Storage Explorer může fungovat na jiných distribucích, ale jenom ty, které jsou uvedené výše jsou oficiálně podporované.

Je také nutné mít následující závislosti nebo knihovny nainstalována pro spouštění v systému Linux Exploer úložiště Azure:

* [.NET pro základní 2.x](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* libsecret (Poznámka: libsecret 1.so.0 musí být k dispozici na vašem počítači. Pokud máte jinou verzi libsecret nainstalován, můžete zkusit soft propojení jeho souboru .so libsecret 1.so.0)
* libgconf-2-4
* Aktuální RSZ

Azure Storage Explorer [poznámky k verzi](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) obsahují konkrétní kroky pro některých distribucích.

[Stažení a instalace Průzkumníka služby Storage](http://www.storageexplorer.com)

---

## <a name="connect-to-a-storage-account-or-service"></a>Připojení k účtu úložiště nebo službě
Průzkumník služby Storage nabízí několik způsobů, jak se připojit k účtům úložiště. Můžete například provést následující věci:
* Připojit se k účtům úložiště přidruženým k vašim předplatným Azure.
* Připojit se účtům úložiště a službám, které s vámi sdílí jiná předplatná Azure.
* Připojit se k místnímu úložišti a spravovat ho pomocí emulátoru úložiště Azure. 

Kromě toho můžete pracovat s účty úložiště v globálním i národním Azure:

* [Připojení k předplatnému Azure:](#connect-to-an-azure-subscription) Umožňuje spravovat prostředky úložiště, které patří do vašeho předplatného Azure.
* [Práce s místním vývojovým úložištěm:](#work-with-local-development-storage) Umožňuje spravovat místní úložiště pomocí emulátoru úložiště Azure.
* [Připojení k externímu úložišti:](#attach-or-detach-an-external-storage-account) Umožňuje spravovat prostředky úložiště, které patří do jiného předplatného Azure nebo jiného národního cloudu Azure, pomocí názvu, klíče a koncových bodů účtu úložiště.
* [Připojení účtu úložiště pomocí SAS:](#attach-storage-account-using-sas) Umožňuje spravovat prostředky úložiště, které patří do jiného předplatného Azure pomocí sdíleného přístupového podpisu (SAS).
* [Připojení služby pomocí SAS:](#attach-service-using-sas) Umožňuje spravovat konkrétní službu úložiště (kontejner objektů blob, fronty nebo tabulky), která patří do jiného předplatného Azure, pomocí sdíleného přístupového podpisu (SAS).
* [Připojení k účtu Azure Cosmos DB pomocí připojovacího řetězce](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): Správa DB Cosmos účtu pomocí připojovacího řetězce.

## <a name="connect-to-an-azure-subscription"></a>Připojení k předplatnému Azure
> [!NOTE]
> Pokud nemáte účet Azure, můžete se [zaregistrovat k bezplatné zkušební verzi](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) nebo si [aktivovat výhody předplatitele Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
>
>

1. V Průzkumníku úložiště vyberte **Správa účtů** přejít na **panelu řízení účet**.

    ![Správa účtů][1]

2. V levém podokně teď zobrazuje všechny účty Azure, které jste přihlášení. Chcete-li se připojit na jiný účet, vyberte **přidat účet**

3. Pokud se chcete přihlásit k národních cloudů nebo balíček Azure, klikněte na **prostředí Azure** rozevíracího seznamu vyberte které cloudu Azure, kterou chcete použít. Po zvolení prostředí, klikněte na **přihlášení...**  tlačítko. Pokud se přihlašujete k Azure zásobníku, přečtěte si téma [Storage Explorer připojit k předplatnému Azure zásobníku](azure-stack/user/azure-stack-storage-connect-se.md) Další informace.

    ![Přihlaste se možnost][2]

3. Jakmile úspěšně přihlásit pomocí účtu Azure, účet a předplatná Azure, které jsou přidružené k tomuto účtu se přidají do levého podokna. Vyberte předplatná Azure, které chcete pracovat a potom vyberte **použít** (zvolíte **Všechna předplatná:** přepínáte výběr všech nebo žádných z uvedených předplatných Azure).

    ![Výběr předplatných Azure][3]

    V levém podokně se zobrazí všechny účty úložišť, přidružené k vybraným předplatným Azure.

    ![Vybraná předplatná Azure][4]

## <a name="work-with-local-development-storage"></a>Práce s místním vývojovým úložištěm
Pomocí Průzkumníka úložiště můžete pracovat s místním úložištěm pomocí emulátoru úložiště Azure. Tento přístup umožňuje simulovat práci s Azure Storage, aniž byste museli mít nasazený v Azure, účet úložiště, protože účet úložiště je emulovaných emulátorem úložiště Azure.

> [!NOTE]
> Emulátor úložiště Azure je momentálně podporován pouze pro Windows.
>
>

> [!NOTE]
> Emulátor úložiště Azure nepodporuje sdílené složky.
>
>

1. V levém podokně Storage Exploreru, rozbalte **(místní a připojené)** > **účty úložiště** > **(vývoj)**  >  **Kontejnery objektů blob** uzlu.

    ![Místní vývojový uzel][5]

2. Pokud jste ještě nenainstalovali emulátor úložiště Azure, budete vyzváni k tomu prostřednictvím informační panel. Pokud se zobrazí informační panel, vyberte možnost **Download the latest version** (Stáhnout nejnovější verzi) a nainstalujte si emulátor.

    ![Výzva ke stažení emulátoru úložiště Azure Storage][6]

3. Po nainstalování emulátoru můžete vytvářet místní objekty blob, fronty a tabulky a pracovat s nimi. Naučte se pracovat s jednotlivými typu účtu úložiště, naleznete v následujících příručkách:

    * [Správa prostředků Azure Blob Storage](vs-azure-tools-storage-explorer-blobs.md)

## <a name="attach-or-detach-an-external-storage-account"></a>Připojení nebo odpojení externího účtu úložiště
Pomocí Průzkumníka úložiště můžete připojit k externím účtům úložiště tak, aby účty úložiště můžete snadno sdílet. Tato část vysvětluje, jak se připojit k externím účtům úložiště (a odpojit se od nich).

### <a name="get-the-storage-account-credentials"></a>Získání přihlašovacích údajů účtu úložiště
Pokud chcete sdílet externího účtu úložiště, musí vlastník tohoto účtu nejdřív pro účet získat přihlašovací údaje (název účtu a klíč) a potom sdílet, že uvedená informace s člověkem, který se chce připojit k účtu. Přihlašovací údaje účtu úložiště prostřednictvím portálu Azure můžete získat provedením následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyberte **Procházet**.

3. Vyberte **Účty úložiště**.

4. V seznamu **účty úložiště**, vyberte požadovaný účet úložiště.

5. V části **Nastavení** vyberte **Přístupové klíče**.

    ![Možnost Přístupové klíče][7]

6. Kopírování **název účtu úložiště** a **key1**.

    ![Přístupové klíče][8]

### <a name="attach-to-an-external-storage-account"></a>Připojení k externímu účtu úložiště
Pokud se chcete připojit k účtu externího úložiště, budete potřebovat název účtu a klíč. Část Získání přihlašovacích údajů účtu úložiště vysvětluje, jak tyto hodnoty získat z webu Azure Portal. Na portálu se ale klíč účtu nazývá **klíč1**. Ano, když se zeptá, Průzkumník úložišť pro klíč účtu, zadáte **key1** hodnotu.

1. Ve Storage Exploreru, otevřete **dialogové okno Připojit**.

    ![Možnost Připojit k úložišti Azure][9]

2. V **dialogové okno Připojit**, zvolte **použít název účtu úložiště a klíč**

    ![Přidat název a možnost klíče][10]

3. Vložte název účtu v **název účtu** textové pole a vložte klíč účtu ( **key1** hodnotu z portálu Azure) do **klíč účtu** textového pole a pak vyberte **Další**.

    ![Název a klíč stránky][11]

    > [!NOTE]
    > Chcete-li použít název a klíč z národních cloudů, použijte **domény koncové body úložiště:** rozevíracího seznamu vyberte doménu jednotlivé koncové body: 
    >
    >

4. V dialogovém okně **Connection Summary** (Souhrn připojení) zkontrolujte zadané informace. Pokud chcete něco změnit, vyberte možnost **Back** (Zpět) a požadovaná nastavení zadejte znovu. 

5. Vyberte **Connect** (Připojit).

6. Po účet úložiště byl úspěšně připojen, účet úložiště zobrazí s **(External)** připojí k jeho názvu.

    ![Výsledek připojení k externímu účtu úložiště][12]

### <a name="detach-from-an-external-storage-account"></a>Odpojení od externího účtu úložiště
1. Klikněte pravým tlačítkem myši na externí účet úložiště, který chcete odpojit, a vyberte **Detach** (Odpojit).

    ![Možnost Odpojit od úložiště][13]

2. V potvrzovací zprávě potvrďte výběrem možnosti **Yes** (Ano) odpojení od externího účtu úložiště.

## <a name="attach-a-storage-account-by-using-a-shared-access-signature-sas"></a>Připojte účet úložiště pomocí sdíleného přístupového podpisu (SAS)
A sdíleného přístupového podpisu nebo [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md), umožňuje správci předplatného Azure udělit dočasný přístup k účtu úložiště bez nutnosti poskytnout přihlašovací údaje předplatného Azure.

Ukažme si tento scénář na příkladu. Řekněme, že uživatel A je správcem předplatného Azure a uživatel A chce uživateli B povolit přístup k účtu úložiště po omezenou dobu s určitými oprávněními:

1. Uživatele a vygeneruje SAS připojovací řetězec za určité časové období a s požadovanými oprávněními.

2. Podpis nasdílí uživateli (v tomto příkladu b), kdo chce získat přístup k účtu úložiště.  

3. Uživatel b se pomocí Průzkumníka úložiště pro připojení k účtu, který patří do uživatele pomocí sdíleného přístupového podpisu.

### <a name="generate-a-sas-connection-string-for-the-account-you-want-to-share"></a>Generování řetězce připojení SAS účtu, který chcete sdílet
1. Ve Storage Exploreru, klikněte pravým tlačítkem na účet úložiště, který chcete sdílet a potom vyberte **sdíleného přístupového podpisu...** .

    ![Možnost místní nabídky Získat sdílený přístupový podpis][14]

2. V **vygenerovat sdílený přístupový podpis** dialogové okno Zadejte časový rámec a oprávnění, které chcete použít pro účet a klikněte **vytvořit** tlačítko.

    ![Získat SAS, dialogové okno][15]  

3. Vedle položky **připojovací řetězec** textového pole, vyberte **kopie** ho zkopírujte do schránky a pak klikněte na tlačítko **Zavřít**.

### <a name="attach-to-a-storage-account-by-using-a-sas-connection-string"></a>Připojení k účtu úložiště pomocí připojovacího řetězce SAS
1. Ve Storage Exploreru, otevřete **dialogové okno Připojit**.

    ![Možnost Připojit k úložišti Azure][9]

2. V **dialogové okno Připojit** dialogovém okně, vyberte **použít připojovací řetězec nebo sdílený přístupový podpis URI** a pak klikněte na **Další**.

    ![Dialogové okno Připojit k úložišti Azure][16]

3. Zvolte **použít připojovací řetězec** a vložte připojovací řetězec do **připojovací řetězec:** pole. Klikněte **Další** tlačítko.

    ![Dialogové okno Připojit k úložišti Azure][17]

4. V dialogovém okně **Connection Summary** (Souhrn připojení) zkontrolujte zadané informace. Pokud chcete provést změny, vyberte **Back** (Zpět) a zadejte požadovaná nastavení. 

5. Vyberte **Connect** (Připojit).

6. Po účet úložiště byl úspěšně připojen, účet úložiště zobrazí s **(SAS)** připojí k jeho názvu.

    ![Výsledek připojení k účtu pomocí sdíleného přístupového podpisu (SAS)][18]

## <a name="attach-a-service-by-using-a-shared-access-signature-sas"></a>Připojení služby pomocí sdíleného přístupového podpisu (SAS)
V části "Připojení k účtu úložiště pomocí SAS" vysvětluje, jak může správce předplatného Azure udělit dočasný přístup k účtu úložiště tak, že generování a sdílení SAS pro účet úložiště. Podobně SAS můžete vygenerovat pro konkrétní službu (kontejner objektů blob, fronty, tabulka nebo sdílené složky) v rámci účtu úložiště.  

### <a name="generate-an-sas-for-the-service-that-you-want-to-share"></a>Vygenerování sdíleného přístupového podpisu (SAS) pro službu, kterou chcete sdílet
V tomto kontextu můžete službu se kontejner objektů blob, fronty, tabulka nebo sdílení souborů. Pokud chcete vygenerovat sdílený přístupový podpis (SAS) pro uvedenou službu, přečtěte si:

* [Získání sdíleného přístupového podpisu (SAS) pro kontejner objektů blob](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

### <a name="attach-to-the-shared-account-service-by-using-a-sas-uri"></a>Připojení ke službě sdíleného účtu pomocí identifikátoru URI SAS
1. Ve Storage Exploreru, otevřete **dialogové okno Připojit**.

    ![Možnost Připojit k úložišti Azure][9]

2. V **dialogové okno Připojit** dialogovém okně vyberte **použít připojovací řetězec nebo sdílený přístupový podpis URI** a pak klikněte na **Další**.

    ![Dialogové okno Připojit k úložišti Azure][16]

3. Zvolte **používání identifikátoru URI SAS** a vložte do vaší URI **identifikátor URI:** pole. Klikněte **Další** tlačítko.

    ![Dialogové okno Připojit k úložišti Azure][19]

3. V dialogovém okně **Connection Summary** (Souhrn připojení) zkontrolujte zadané informace. Pokud chcete provést změny, vyberte **Back** (Zpět) a zadejte požadovaná nastavení. 

4. Vyberte **Connect** (Připojit).

5. Po úspěšném připojení služby služby se zobrazí v části **(SAS-Attached služby)** uzlu.

    ![Výsledek připojení ke sdílené službě pomocí sdíleného přístupového podpisu (SAS)][20]

## <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Připojení k účtu Azure Cosmos DB pomocí připojovacího řetězce
Kromě spravovat účty pro Azure Cosmos DB prostřednictvím předplatné Azure, je alternativní způsob připojení k databázi Azure Cosmos použít připojovací řetězec. Pomocí následujícího postupu se připojte pomocí připojovacího řetězce.

1. V levém stromě vyhledejte **Místní a připojené**, klikněte pravým tlačítkem na **Účty služby Azure Cosmos DB** a zvolte **Připojit ke službě Azure Cosmos DB...**

    ![Připojte se k Azure Cosmos databázi pomocí připojovacího řetězce][21]

2. Zvolte rozhraní API služby Azure Cosmos DB, vložte vaší **připojovací řetězec**a potom klikněte na **OK** pro připojení účet Azure Cosmos DB. Informace o načtení připojovacího řetězce najdete v tématu popisujícím [Získání připojovacího řetězce](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![connection-string][22]

 ## <a name="connect-to-azure-data-lake-store-by-uri"></a>Připojení k Azure Data Lake Store identifikátorem URI
Představte si, že chcete získat přístup k prostředkům, které neexistují ve vašem předplatném. Ostatní vám však udělí oprávnění k získání jejich identifikátoru URI. V takovém případě se po přihlášení můžete připojit ke službě Data Lake Store s použitím tohoto identifikátoru URI. Postup najdete v následujících krocích.
1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte **Místní a připojené**.
3. Klikněte pravým tlačítkem na **Data Lake Store** a v místní nabídce vyberte **Připojit ke službě Data Lake Store**.

    ![místní nabídka Připojit ke službě Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Zadejte identifikátor URI a nástroj vás pak přesměruje na adresu URL, kterou jste právě zadali.

    ![dialogové okno Připojit ke službě Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![výsledek připojení ke službě Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="search-for-storage-accounts"></a>Vyhledávání účtů úložiště
Pokud budete potřebovat k vyhledání prostředků úložiště a neznáte, tam, kde je, že do vyhledávacího pole v horní části levého podokna můžete použít k vyhledání prostředku.

Při psaní do vyhledávacího pole, v levém podokně zobrazí všechny prostředky, které odpovídají hledané hodnotě, kterou jste zadali až tento bod. Například hledání **koncové body** je znázorněno na následujícím snímku obrazovky:

![Vyhledávání účtu úložiště][23]

> [!NOTE]
> Použití **účet správy panelu** zrušte výběr žádné předplatné, které neobsahují položky hledáte ke zlepšení čas provádění hledání. Můžete také kliknout pravým tlačítkem na uzel a zvolte **vyhledávání z zde** spusťte hledání z konkrétní uzlu.
>
>

## <a name="next-steps"></a>Další postup
* [Správa prostředků Azure Blob Storage pomocí Storage Exploreru](vs-azure-tools-storage-explorer-blobs.md)
* [Správa Azure Cosmos DB v Azure Storage Explorer (Preview)](./cosmos-db/storage-explorer.md)
* [Správa prostředků Azure Data Lake Store pomocí Průzkumníka úložiště](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-SignInSelected.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccountPanel.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/DevelopmentNode.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/EmulatorNotInstalled.png
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

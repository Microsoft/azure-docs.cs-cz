---
title: Začínáme s Průzkumníkem služby Storage | Dokumentace Microsoftu
description: Správa prostředků Azure storage pomocí Průzkumníka služby Storage
services: storage
documentationcenter: na
author: cawaMS
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
ms.openlocfilehash: 77763765e75500d994235bba0a5c0c3bb3e3be70
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756814"
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

* Ubuntu 16.04 x64 (doporučeno)
* Ubuntu 17.10 x64
* Ubuntu 14.04 x64

Průzkumník služby Azure Storage může pracovat na jiné distribuce, ale pouze uvedené výše se oficiálně podporuje.

Také musíte mít následující závislosti nebo knihovny nainstalována pro spouštění Průzkumníka služby Azure Storage v Linuxu:

* [.NET Core 2.x](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* libsecret (Poznámka: libsecret 1.so.0 musí být k dispozici na svém počítači. Pokud máte jinou verzi libsecret nainstalované, můžete zkusit obnovitelné libsecret 1.so.0 propojení jeho soubor .so)
* libgconf-2-4
* Aktuální GCC

Průzkumník služby Azure Storage [zpráva k vydání verze](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) obsahují konkrétní kroky pro některých distribucích.

[Stažení a instalace Průzkumníka služby Storage](https://www.storageexplorer.com)

---

## <a name="connect-to-a-storage-account-or-service"></a>Připojení k účtu úložiště nebo službě

Průzkumník služby Storage nabízí několik způsobů, jak se připojit k účtům úložiště. Můžete například provést následující věci:

* Připojit se k účtům úložiště přidruženým k vašim předplatným Azure.
* Připojit se účtům úložiště a službám, které s vámi sdílí jiná předplatná Azure.
* Připojit se k místnímu úložišti a spravovat ho pomocí emulátoru úložiště Azure.

Kromě toho můžete pracovat s účty úložiště v globálním i národním Azure:

* [Připojení k předplatnému Azure](#connect-to-an-azure-subscription): Spravujte prostředky úložiště, které patří k předplatnému Azure.
* [Práce s místním vývojovým úložištěm](#work-with-local-development-storage): Spravujte místní úložiště pomocí emulátoru úložiště Azure.
* [Připojení k externímu úložišti](#attach-or-detach-an-external-storage-account): Spravujte prostředky úložiště, která patří do jiného předplatného Azure nebo jiného národního cloudu Azure s použitím názvu, klíče a koncových bodů účtu úložiště.
* [Připojení účtu úložiště pomocí SAS](#attach-a-storage-account-by-using-a-shared-access-signature-sas): Spravujte prostředky úložiště, které patří do jiného předplatného Azure pomocí sdíleného přístupového podpisu (SAS).
* [Připojení služby pomocí SAS](#attach-a-service-by-using-a-shared-access-signature-sas): Spravujte konkrétní službu úložiště (kontejner objektů blob, fronty nebo tabulky), který patří do jiného předplatného Azure pomocí SAS.
* [Připojení k účtu služby Azure Cosmos DB pomocí připojovacího řetězce](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): Spravujte účet služby Cosmos DB pomocí připojovacího řetězce.

## <a name="connect-to-an-azure-subscription"></a>Připojení k předplatnému Azure

> [!NOTE]
> Pokud nemáte účet Azure, můžete se [zaregistrovat k bezplatné zkušební verzi](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) nebo si [aktivovat výhody předplatitele Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
>
>

1. V Průzkumníku služby Storage vyberte **spravovat účty** přejdete **Panel pro správu účtu**.

    ![Spravovat účty][1]

2. V levém podokně se teď zobrazí všem účtům Azure, které jste přihlášeni. Chcete-li připojit k jinému účtu, vyberte **přidat účet**

3. Pokud chcete k přihlašování do národních cloudů nebo Azure Stack, klikněte na **prostředí Azure** rozevírací nabídku a zvolte který Azure cloud, který chcete použít. Po výběru prostředí, klikněte na tlačítko **přihlásit...**  tlačítko. Pokud se přihlašujete ke službě Azure Stack, najdete v článku [připojení Storage Exploreru k předplatnému Azure Stack](azure-stack/user/azure-stack-storage-connect-se.md) Další informace.

    ![Přihlaste se možnost][2]

4. Po úspěšné registraci se pomocí účtu Azure, účet a předplatných Azure, které jsou přidružené k tomuto účtu se přidají do levého podokna. Vyberte předplatná Azure, které chcete pracovat a pak vyberte **použít** (výběr **Všechna předplatná:** přepínáte výběr všech nebo žádných z uvedených předplatných Azure).

    ![Výběr předplatných Azure][3]

    V levém podokně se zobrazí všechny účty úložišť, přidružené k vybraným předplatným Azure.

    ![Vybraná předplatná Azure][4]

## <a name="work-with-local-development-storage"></a>Práce s místním vývojovým úložištěm

Pomocí Průzkumníka služby Storage můžete pracovat s místním úložištěm pomocí emulátoru. Tento přístup umožňuje simulovat práci s Azure Storage, aniž byste museli mít nasazený v Azure účet úložiště.

Od verze 1.1.0, emulátor místního úložiště se podporuje na všech platformách. Průzkumník služby Storage můžete připojit k libovolné emulované služby naslouchání na jeho výchozí koncové body místní úložiště.

> [!NOTE]
> Podpora pro funkce a služby úložiště může výrazně lišit podle svého výběru: emulátoru. Zajistěte, aby že vaše emulátor podporuje služby a funkce, které máte v úmyslu pracovat.

1. Konfigurace služby vaší emulátoru možnost naslouchat nepoužitého portu.

   Emulované služby | Výchozí koncový bod
   -----------------|-------------------------
   Objekty blob            | `http://127.0.0.1:10000`
   Fronty           | `http://127.0.0.1:10001`
   Tabulky           | `http://127.0.0.1:10002`

2. Spusťte emulátor.
   > [!IMPORTANT]
   > Storage Explorer nespustí automaticky vaše emulátoru. Je nutné spustit ho sami.

3. V Průzkumníku služby Storage, klikněte na tlačítko **přidat účet** tlačítko. Vyberte **připojit k místní emulátor** a klikněte na tlačítko **Další**.

4. Zadejte čísla portů pro služby, které jste nakonfigurovali výše (ponechte prázdné, pokud se nechystáte používat tuto službu). Klikněte na tlačítko **Další** pak **připojit** k vytvoření připojení.

5. Rozbalte **místní a připojené** > **účty úložiště** > uzlů, pak rozbalte uzly service pod tímto uzlem odpovídající připojení k emulátoru.

   Tento uzel můžete použít k vytváření a práci s místní objekty BLOB, frontám a tabulkám. Zjistěte, jak pracovat s jednotlivými typu účtu úložiště, najdete v následujících příručkách:

   * [Správa prostředků Azure Blob storage](vs-azure-tools-storage-explorer-blobs.md)
   * [Správa prostředků Azure File storage](vs-azure-tools-storage-explorer-files.md)

## <a name="attach-or-detach-an-external-storage-account"></a>Připojení nebo odpojení externího účtu úložiště

Pomocí Průzkumníka služby Storage můžete připojit k externím účtům úložiště tak, aby se účty úložiště snadno sdílet. Tato část vysvětluje, jak se připojit k externím účtům úložiště (a odpojit se od nich).

### <a name="get-the-storage-account-credentials"></a>Získání přihlašovacích údajů účtu úložiště

Pokud chcete sdílet externí účet úložiště, musí vlastník tohoto účtu nejdřív pro účet získat přihlašovací údaje (název účtu a klíč) a pak sdílejte, že informace o osobě, která chce připojit k říká, že účet. Přihlašovací údaje účtu úložiště pomocí webu Azure portal můžete získat provedením následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyberte **Procházet**.

3. Vyberte **Účty úložiště**.

4. V seznamu **účty úložiště**, vyberte požadovaný účet úložiště.

5. V části **Nastavení** vyberte **Přístupové klíče**.

    ![Možnost Přístupové klíče][7]

6. Kopírovat **název účtu úložiště** a **key1**.

    ![Přístupové klíče][8]

### <a name="attach-to-an-external-storage-account"></a>Připojení k externímu účtu úložiště

Pokud se chcete připojit k účtu externího úložiště, budete potřebovat název účtu a klíč. Část Získání přihlašovacích údajů účtu úložiště vysvětluje, jak tyto hodnoty získat z webu Azure Portal. Na portálu se ale klíč účtu nazývá **klíč1**. Proto když Storage Explorer vyzve k zadání klíče účtu služby, zadáte **key1** hodnotu.

1. V Průzkumníku služby Storage, otevřete **dialogové okno Připojit**.

    ![Možnost Připojit k úložišti Azure][9]

2. V **dialogové okno Připojit**, zvolte **použít název účtu úložiště a klíč**

    ![Přidat s názvem a možnost klíče][10]

3. Vložte název účtu v **název účtu** text a vložte klíč účtu ( **key1** hodnotu z webu Azure portal) do **klíč účtu** textové pole a pak vyberte **Další**.

    ![Název a klíč stránky][11]

    > [!NOTE]
    > Chcete-li použít název a klíč z národního cloudu, použijte **doména koncových bodů úložiště:** rozevíracího seznamu vyberte doménu jednotlivé koncové body:
    >
    >

4. V dialogovém okně **Connection Summary** (Souhrn připojení) zkontrolujte zadané informace. Pokud chcete něco změnit, vyberte možnost **Back** (Zpět) a požadovaná nastavení zadejte znovu.

5. Vyberte **Connect** (Připojit).

6. Jakmile účet úložiště byl úspěšně připojen, účet úložiště zobrazí s **(externí)** jeho názvu připojí.

    ![Výsledek připojení k externímu účtu úložiště][12]

### <a name="detach-from-an-external-storage-account"></a>Odpojení od externího účtu úložiště

1. Klikněte pravým tlačítkem myši na externí účet úložiště, který chcete odpojit, a vyberte **Detach** (Odpojit).

    ![Možnost Odpojit od úložiště][13]

2. V potvrzovací zprávě potvrďte výběrem možnosti **Yes** (Ano) odpojení od externího účtu úložiště.

## <a name="attach-a-storage-account-by-using-a-shared-access-signature-sas"></a>Připojení účtu úložiště pomocí sdílený přístupový podpis (SAS)

A sdílený přístupový podpis nebo [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md), umožňuje správci předplatného Azure udělit dočasný přístup k účtu úložiště, aniž by musel zadávat přihlašovací údaje předplatného Azure.

Ukažme si tento scénář na příkladu. Řekněme, že uživatel A je správcem předplatného Azure a uživatel A chce uživateli B povolit přístup k účtu úložiště po omezenou dobu s určitými oprávněními:

1. Uživatel a vygeneruje připojovací řetězec SAS pro určité časové období a s požadovanými oprávněními.

2. Uživatel a sdílí SAS s osobou (nasdílí uživateli b, v tomto příkladu), který potřebuje přístup k účtu úložiště.

3. UserB používá pro připojení k účtu, který patří do UserA sdíleného přístupového podpisu pomocí Průzkumníka služby Storage.

### <a name="generate-a-sas-connection-string-for-the-account-you-want-to-share"></a>Generovat SAS připojovací řetězec pro účet, který chcete sdílet

1. V Průzkumníku služby Storage, klikněte pravým tlačítkem na účet úložiště, který chcete sdílet a pak vyberte **získat sdílený přístupový podpis...** .

    ![Možnost místní nabídky Získat sdílený přístupový podpis][14]

2. V **vygenerovat sdílený přístupový podpis** dialogového okna zadejte časový rámec a oprávnění pro účet a potom klikněte na tlačítko **vytvořit** tlačítko.

    ![Získat dialogové okno SAS][15]

3. Vedle položky **připojovací řetězec** textového pole, vyberte **kopírování** zkopírujte do schránky, a potom klikněte na **Zavřít**.

### <a name="attach-to-a-storage-account-by-using-a-sas-connection-string"></a>Připojit k účtu úložiště pomocí připojovacího řetězce SAS

1. V Průzkumníku služby Storage, otevřete **dialogové okno Připojit**.

    ![Možnost Připojit k úložišti Azure][9]

2. V **dialogové okno Připojit** dialogovém okně zvolte **použít připojovací řetězec nebo URI sdíleného přístupového podpisu** a potom klikněte na tlačítko **Další**.

    ![Dialogové okno Připojit k úložišti Azure][16]

3. Zvolte **použít připojovací řetězec** a vložte připojovací řetězec do **připojovací řetězec:** pole. Klikněte na tlačítko **Další** tlačítko.

    ![Dialogové okno Připojit k úložišti Azure][17]

4. V dialogovém okně **Connection Summary** (Souhrn připojení) zkontrolujte zadané informace. Pokud chcete provést změny, vyberte **Back** (Zpět) a zadejte požadovaná nastavení.

5. Vyberte **Connect** (Připojit).

6. Jakmile účet úložiště byl úspěšně připojen, účet úložiště zobrazí s **(SAS)** jeho názvu připojí.

    ![Výsledek připojení k účtu pomocí sdíleného přístupového podpisu (SAS)][18]

## <a name="attach-a-service-by-using-a-shared-access-signature-sas"></a>Připojení služby pomocí sdílený přístupový podpis (SAS)

V části "Připojení účtu úložiště pomocí SAS" vysvětluje, jak může správce předplatného Azure udělit dočasný přístup k účtu úložiště vygenerováním a nasdílením SAS pro účet úložiště. Obdobně SAS můžete vygenerovat pro konkrétní službu (kontejner objektů blob, fronty, tabulky nebo sdílené složky) v rámci účtu úložiště.

### <a name="generate-an-sas-for-the-service-that-you-want-to-share"></a>Vygenerování sdíleného přístupového podpisu (SAS) pro službu, kterou chcete sdílet

V tomto kontextu můžete službu být kontejner objektů blob, queue, table nebo sdílení souborů. Pokud chcete vygenerovat sdílený přístupový podpis (SAS) pro uvedenou službu, přečtěte si:

* [Získání sdíleného přístupového podpisu (SAS) pro kontejner objektů blob](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

### <a name="attach-to-the-shared-account-service-by-using-a-sas-uri"></a>Připojení ke službě sdíleného účtu pomocí identifikátoru URI SAS

1. V Průzkumníku služby Storage, otevřete **dialogové okno Připojit**.

    ![Možnost Připojit k úložišti Azure][9]

2. V **dialogové okno Připojit** dialogového okna zvolte **použít připojovací řetězec nebo URI sdíleného přístupového podpisu** a potom klikněte na tlačítko **Další**.

    ![Dialogové okno Připojit k úložišti Azure][16]

3. Zvolte **použít identifikátor URI SAS** a vložte váš identifikátor URI do **identifikátor URI:** pole. Klikněte na tlačítko **Další** tlačítko.

    ![Dialogové okno Připojit k úložišti Azure][19]

4. V dialogovém okně **Connection Summary** (Souhrn připojení) zkontrolujte zadané informace. Pokud chcete provést změny, vyberte **Back** (Zpět) a zadejte požadovaná nastavení.

5. Vyberte **Connect** (Připojit).

6. Po služba je úspěšně připojena, objeví se v části **(SAS-Attached služby)** uzlu.

    ![Výsledek připojení ke sdílené službě pomocí sdíleného přístupového podpisu (SAS)][20]

## <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Připojení k účtu služby Azure Cosmos DB pomocí připojovacího řetězce

Kromě Správa účtů služby Azure Cosmos DB pomocí předplatného Azure, je alternativní způsob připojení ke službě Azure Cosmos DB pomocí připojovacího řetězce. Pomocí následujícího postupu se připojte pomocí připojovacího řetězce.

1. V levém stromě vyhledejte **Místní a připojené**, klikněte pravým tlačítkem na **Účty služby Azure Cosmos DB** a zvolte **Připojit ke službě Azure Cosmos DB...**

    ![připojení ke službě Azure Cosmos DB pomocí připojovacího řetězce][21]

2. Zvolte rozhraní API služby Azure Cosmos DB, vložte váš **připojovací řetězec**a potom klikněte na tlačítko **OK** připojte k účtu Azure Cosmos DB. Informace o načtení připojovacího řetězce najdete v tématu popisujícím [Získání připojovacího řetězce](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![connection-string][22]

## <a name="connect-to-azure-data-lake-store-by-uri"></a>Připojení k Azure Data Lake Store pomocí identifikátoru URI

Představte si, že chcete získat přístup k prostředkům, které neexistují ve vašem předplatném. Ostatní vám však udělí oprávnění k získání jejich identifikátoru URI. V takovém případě se po přihlášení můžete připojit ke službě Data Lake Store s použitím tohoto identifikátoru URI. Postup najdete v následujících krocích.

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte **Místní a připojené**.
3. Klikněte pravým tlačítkem na **Data Lake Store** a v místní nabídce vyberte **Připojit ke službě Data Lake Store**.

    ![místní nabídka Připojit ke službě Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Zadejte identifikátor URI a nástroj vás pak přesměruje na adresu URL, kterou jste právě zadali.

    ![dialogové okno Připojit ke službě Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![výsledek připojení ke službě Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)

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

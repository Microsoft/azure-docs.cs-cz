---
title: Procházet a spravovat prostředky úložiště pomocí Průzkumníku serveru | Dokumentace Microsoftu
description: Procházení a Správa prostředků úložiště pomocí Průzkumníka serveru
services: visual-studio-online
author: ghogen
manager: douge
assetId: 658dc064-4a4e-414b-ae5a-a977a34c930d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 8/24/2017
ms.author: ghogen
ms.openlocfilehash: ece9865ad442edc27fd0f495940fd0945d0529ab
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969262"
---
# <a name="browse-and-manage-storage-resources-by-using-server-explorer"></a>Procházet a spravovat prostředky úložiště pomocí Průzkumníka serveru

[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Přehled

Pokud jste nainstalovali nástroje Azure pro sadu Microsoft Visual Studio, můžete zobrazit objektů blob, fronty a tabulky dat z účtu úložiště Azure. Azure **úložiště** uzel v Průzkumníku serveru zobrazuje data, která jsou v účtu emulátor místního úložiště a dalších účtů úložiště Azure.

Chcete-li zobrazit Průzkumník serveru v sadě Visual Studio na řádku nabídek, vyberte **zobrazení** > **Průzkumníka serveru**. **Úložiště** uzlu se zobrazuje všechny účty úložiště, které existují v rámci každého předplatného Azure nebo certifikát, který jste připojeni k. Pokud váš účet úložiště se nezobrazí, můžete ho přidat podle pokynů [dále v tomto článku](#add-storage-accounts-by-using-server-explorer).

Od verze Azure SDK 2.7, můžete také pomocí Průzkumníka cloudu můžete zobrazit a spravovat prostředky Azure. Další informace najdete v tématu [prostředky správy Azure pomocí Průzkumníka cloudu](vs-azure-tools-resources-managing-with-cloud-explorer.md).

## <a name="view-and-manage-storage-resources-in-visual-studio"></a>Umožňuje zobrazit a spravovat prostředky úložiště v sadě Visual Studio

Průzkumník serveru automaticky zobrazí seznam objektů BLOB, frontám a tabulkám v účtu úložiště emulátoru. Emulátor účet úložiště je uvedený v Průzkumníku serveru pod **úložiště** jako uzel **vývoj** uzlu.

Zobrazit prostředky účtu emulátor úložiště, rozbalte **vývoj** uzlu. Pokud na emulátor úložiště nebylo spuštěno při rozšiřování **vývoj** uzlu, automaticky spustí. Tento proces může trvat několik sekund. Můžete pokračovat v práci v jiných oblastech sady Visual Studio při spuštění emulátoru úložiště.

Chcete-li zobrazit prostředky v účtu úložiště, rozbalte uzel účtu úložiště v Průzkumníkovi serveru, kde uvidíte **objekty BLOB**, **fronty**, a **tabulky** uzly.

## <a name="work-with-blob-resources"></a>Pracovat s prostředky objektů blob

**Objekty BLOB** uzel se zobrazí seznam kontejnerů pro vybraný účet úložiště. Kontejnery objektů BLOB obsahují soubory objektů blob a tyto objekty BLOB můžete uspořádat do složek a podsložek. Další informace najdete v tématu [použití Blob storage pomocí technologie .NET](storage/blobs/storage-dotnet-how-to-use-blobs.md).

### <a name="to-create-a-blob-container"></a>Chcete-li vytvořit kontejner objektů blob

1. Otevřete místní nabídku **objekty BLOB** uzlu a pak vyberte **vytvořit kontejner objektů Blob**.
1. V **vytvořit kontejner objektů Blob** dialogovém okně zadejte název nového kontejneru.  
1. Vyberte možnost Enter na klávesnici nebo můžete kliknutím nebo klepnutím mimo pole názvu pro uložení objektů blob v kontejneru.

   > [!NOTE]
   > Název kontejneru objektu blob musí začínat číslicí (0-9) nebo malým písmenem (a-z).

### <a name="to-delete-a-blob-container"></a>Chcete-li odstranit kontejner objektů blob

Otevřete místní nabídku pro kontejner objektů blob, který chcete odebrat a pak vyberte **odstranit**.

### <a name="to-display-a-list-of-the-items-in-a-blob-container"></a>Chcete-li zobrazit seznam položek v kontejneru objektů blob

Otevřete místní nabídku pro název kontejneru objektů blob v seznamu a pak vyberte **otevřít**.

Při zobrazení obsahu kontejneru objektů blob, se zobrazí na kartě označuje jako zobrazení objektů blob v kontejneru.

![Zobrazení kontejneru objektů BLOB](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC749016.png)

Pomocí tlačítka v pravém horním rohu zobrazení objektů blob v kontejneru můžete provádět následující operace pro objekty BLOB:

* Zadejte hodnotu filtru a použijte ji.
* Aktualizujte seznam objektů BLOB v kontejneru.
* Nahrajte soubor.
* Odstraňte objekt blob. (Při odstranění souboru z kontejneru objektů blob nedojde k odstranění základního souboru. Pouze odebere ho z kontejneru objektů blob.)
* Otevřete objekt blob.
* Uložte objekt blob do místního počítače.

### <a name="to-create-a-folder-or-subfolder-in-a-blob-container"></a>Chcete-li vytvořit složku nebo podsložku v kontejneru objektů blob

1. Vyberte kontejner objektů blob v Průzkumníku cloudu. V okně kontejneru vyberte **nahrát objekt Blob** tlačítko.

1. V **nahrát nový soubor** dialogové okno, vyberte **Procházet** tlačítko k určení souboru, který chcete nahrát a pak zadejte název složky v **složky (volitelné)** pole.

   ![Po nahrání souboru do složky objektů blob](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766037.png)

   Můžete přidat podsložky ve složkách kontejneru pomocí stejného kroku. Pokud nezadáte název složky, nahrání souboru na nejvyšší úrovni kontejneru objektů blob. Soubor se zobrazí v určené složce v kontejneru.

   ![Složka přidá do kontejneru objektů blob](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766038.png)

1. Klikněte dvakrát na složku nebo stisknutím klávesy Enter zobrazit obsah složky. Pokud jste ve složce kontejneru, můžete přejít zpět do kořenového adresáře kontejneru tak, že vyberete **otevřít nadřazený adresář** tlačítko (šipka).

### <a name="to-delete-a-container-folder"></a>Chcete odstranit nějakou složku kontejneru

Odstraňte všechny soubory ve složce.

Protože složky v kontejnerech objektů blob jsou virtuální složky, nelze vytvořit prázdnou složku. Také nelze odstranit složku, do které odstraňte její obsah souboru, ale místo toho musíte odstranit celý obsah složky odstranit samotné složce.

### <a name="to-filter-blobs-in-a-container"></a>Chcete-li filtrovat objekty BLOB v kontejneru

Můžete filtrovat objekty BLOB, které jsou zobrazeny zadáním běžnou předponu.

Například, pokud zadáte předponu **hello** do filtru textového pole a pak vyberte **Execute** (**!**) se zobrazí tlačítko, pouze na objekty BLOB, které začínají řetězcem "hello".

![Filtrovat textové pole](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC519076.png)

Do textového pole filtru je velká a malá písmena a nepodporuje filtrování se zástupnými znaky. Objekty BLOB se dá filtrovat jenom podle předpony. Předpona, která může zahrnovat oddělovač, pokud používáte oddělovač pro uspořádání objektů BLOB v hierarchii virtuální. Například filtrování na této předponě "HelloFabric /" vrátí všechny objekty BLOB, které začínají tento řetězec.

### <a name="to-download-blob-data"></a>Chcete-li stáhnout data objektů blob

V Průzkumníku cloudu použijte některou z následujících metod:

* Otevřete místní nabídku pro jeden nebo více objektů BLOB a pak vyberte **otevřít**.
* Zvolte název objektu blob a pak vyberte **otevřít** tlačítko.
* Dvakrát klikněte na název objektu blob.

Zobrazí se v průběhu stahování objektů blob **protokolu aktivit Azure** okna.

Objekt blob se otevře v editoru výchozí pro daný typ souboru. Pokud operační systém rozpozná typ souboru, soubor se otevře v lokálně nainstalované aplikace. V opačném případě budete vyzváni k výběru aplikace, která je vhodná pro typ souboru objektu blob. Místní soubor, který je vytvořen při stažení objektu blob je určen jen pro čtení.

Data objektů blob je v místní mezipaměti a porovnávána s časem poslední změny objektu blob v úložišti objektů Blob v Azure. Pokud se objekt blob byl aktualizována mezitím než byla naposledy stažena, je stažen znovu. V opačném případě se načte objekt blob z místního disku.

Ve výchozím nastavení se stáhne objekt blob do dočasného adresáře. Ke stažení objektů blob pro konkrétní adresář, otevřete místní nabídku pro názvy vybraných objektů blob a vyberte **uložit jako**. Při ukládání objektu blob tímto způsobem není otevřený soubor objektu blob a místní soubor se vytvoří s atributy pro čtení a zápis.

### <a name="to-upload-blobs"></a>K nahrání objektů BLOB

K nahrání objektů BLOB, vyberte **nahrát objekt Blob** tlačítko při otevření pro zobrazení v zobrazení kontejneru objektů blob v kontejneru.

Můžete vybrat jeden nebo více souborů k nahrání a můžete nahrát soubory libovolného typu. **Protokolu aktivit Azure** okno zobrazuje průběh nahrávání. Další informace o tom, jak pracovat s daty objektů blob najdete v tématu [jak používat Azure Blob storage v rozhraní .NET](http://go.microsoft.com/fwlink/p/?LinkId=267911).

### <a name="to-view-logs-transferred-to-blobs"></a>Chcete-li zobrazit protokoly přenosu k objektům BLOB

Pokud používáte Azure Diagnostics protokolování dat z aplikace Azure a jste přenesli protokoly do vašeho účtu úložiště, uvidíte kontejnery, které Azure vytvořilo pro tyto protokoly. Zobrazování těchto protokolů v Průzkumníku serveru je snadný způsob, jak identifikovat problémy s vaší aplikací, zejména v případě, že se nasazuje do Azure.

Další informace o diagnostice Azure najdete v tématu [shromažďování dat protokolování pomocí diagnostiky Azure pomocí](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### <a name="to-get-the-url-for-a-blob"></a>Získat adresu URL pro objekt blob

Otevřete jeho místní nabídku a zvolte **kopírování adresy URL**.

### <a name="to-edit-a-blob"></a>Chcete-li upravit objekt blob

Vyberte objekt blob a pak vyberte **otevřít objekt Blob** tlačítko.

Soubor se stáhnou do dočasného umístění a otevřít v místním počítači. Nahrajte objekt blob znovu po provedení změn.

## <a name="work-with-queue-resources"></a>Pracovat s prostředky fronty

Fronty služby Storage jsou hostované v účtu služby Azure storage. Můžete využít k povolení cloudové rolí služby pro komunikaci mezi sebou a s jinými službami předávání zpráv mechanismem. Fronty můžete přistupovat prostřednictvím kódu programu přes cloudovou službu a přes webovou službu pro externí klienty. Fronty můžete také přistupovat přímo pomocí Průzkumníka serveru v sadě Visual Studio.

Když vyvíjíte cloudovou službu, která používá fronty, můžete chtít použít Visual Studio k vytvoření fronty a s nimi interaktivně pracovat při vývoji a testování kódu.

V Průzkumníku serveru můžete zobrazit fronty v účtu úložiště, vytvářet a odstraňovat fronty, otevření fronty k zobrazení jeho zpráv a přidání zpráv do fronty. Při otevření fronty pro zobrazení můžete zobrazit jednotlivé zprávy a můžete provádět následující akce ve frontě pomocí tlačítek v levém horním rohu:

* Aktualizujte zobrazení fronty.
* Přidejte do fronty zprávu.
* Odstranit nejvyššího zprávu z fronty.
* Vymažte celou frontu.

Následující obrázek znázorňuje frontu, která obsahuje dvě zprávy:

![Zobrazení fronty](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC651470.png)

Další informace o službě storage services fronty, naleznete v tématu [Začínáme s Azure Queue storage pomocí .NET](http://go.microsoft.com/fwlink/?LinkID=264702). Informace o webové službě storage services fronty, naleznete v tématu [koncepty služby front](http://go.microsoft.com/fwlink/?LinkId=264788). Informace o tom, jak odesílat zprávy do fronty služby storage s použitím sady Visual Studio najdete v tématu [odesílání zpráv do fronty služby Storage](https://docs.microsoft.com/azure/visual-studio/vs-storage-cloud-services-getting-started-queues).

> [!NOTE]
> Fronty služby Storage se liší od fronty Azure Service Bus. Další informace o fronty služby Service Bus, najdete v části [fronty služby Service Bus, témat a odběrů](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-queues-topics-subscriptions).

## <a name="work-with-table-resources"></a>Pracovat s prostředky tabulky

Služba Azure Table Storage ukládá velké objemy strukturovaných dat. Tato služba je úložiště dat typu NoSQL, která přijímá ověřených volání z uvnitř i mimo Azure cloud. Tabulky Azure jsou ideální pro ukládání strukturovaných, nerelačních dat.

### <a name="to-create-a-table"></a>Pokud chcete vytvořit tabulku

1. V Průzkumníku cloudu, vyberte **tabulky** uzel účtu úložiště a pak vyberte **Create Table**.
1. V **Create Table** dialogovém okně zadejte název tabulky.

### <a name="to-view-table-data"></a>Chcete-li zobrazit data tabulky

1. V Průzkumníku cloudu otevřete **Azure** uzlu a pak otevřete **úložiště** uzlu.
1. Otevřete uzel účtu úložiště, který vás zajímá a pak otevřete **tabulky** uzlu zobrazíte seznam tabulek pro účet úložiště.
1. Otevřete místní nabídku pro tabulku a pak vyberte **zobrazení tabulky**.

    ![Tabulku Azure v Průzkumníku řešení](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744165.png)

V tabulce jsou uspořádána podle entity (viz řádky) a vlastnosti (zobrazené sloupce). Například následující obrázek znázorňuje entity v Návrháři tabulek.

### <a name="to-edit-table-data"></a>Chcete-li upravit data v tabulce

V Návrháři tabulek, otevřete místní nabídku pro entitu (jeden řádek) nebo vlastnosti (jedinou buňku) a pak vyberte **upravit**.

    ![Add or edit a table entity](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC656238.png)

Entity v jedné tabulce nejsou musí mít stejnou sadu vlastnosti (sloupce). Mějte na paměti následující omezení pro prohlížení a úpravy dat tabulky:

* Nelze zobrazit nebo upravit binárních dat (`type byte[]`), ale můžete ho uložit v tabulce.
* Nelze upravovat **PartitionKey** nebo **RowKey** hodnoty, protože služba Table storage v Azure nepodporuje danou operaci.
* Nejde vytvořit vlastnost s názvem **časové razítko**. Služby Azure storage použijte vlastnost s tímto názvem.
* Pokud zadáte **data a času** hodnotu, musí odpovídat formátu, který je vhodný pro místní a jazykové nastavení vašeho počítače (například MM/DD/RRRR HH: mm: [AM | PM] pro jazykovou verzi US English).

### <a name="to-add-entities"></a>Přidání entit

1. V Návrháři tabulek, vyberte **Přidat entitu** tlačítko.

    ![Přidání tlačítka Entity](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655336.png)

1. V **Přidat entitu** dialogového okna zadejte hodnoty **PartitionKey** a **RowKey** vlastnosti.

    ![Přidat entitu – dialogové okno](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655335.png)

    Zadejte hodnoty pečlivě. Nelze je změnit po zavření dialogových oken, není-li odstranit entitu a potom ho znovu přidejte.

### <a name="to-filter-entities"></a>Chcete-li filtrovat entity

Můžete přizpůsobit sadu entit, které se zobrazí v tabulce v případě, že pomocí Tvůrce dotazů.

1. Chcete-li otevřít Tvůrce dotazů, otevřete na tabulku pro zobrazení.
1. Vyberte **Tvůrce dotazů** tlačítko na panelu nástrojů zobrazení tabulky.

    **Tvůrce dotazů** zobrazí se dialogové okno. Následující obrázek znázorňuje dotaz, který má být sestaven v editoru dotazů.

    ![Tvůrce dotazů](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC652231.png)
1. Po dokončení sestavení dotazu, zavřete dialogové okno. Výsledný text formulář dotazu se zobrazí v textovém poli jako filtr datových služeb WCF.
1. Spusťte dotaz, vyberte ikonu zelený trojúhelník.

Můžete také filtrovat data entity, které se zobrazí v Návrháři tabulek Pokud zadáte řetězec filtru služeb WCF Data Services přímo do textového pole filtru. Tento typ řetězce je podobná klauzuli WHERE příkazu SQL, ale je odeslána na server jako požadavek HTTP. Informace o tom, jak vytvářet řetězce filtru najdete v tématu [Constructing řetězce filtru pro návrháře tabulky](https://docs.microsoft.com/azure/vs-azure-tools-table-designer-construct-filter-strings).

Následující obrázek znázorňuje příklad řetězce platný filtr:

![Řetězec filtru](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655337.png)

## <a name="refresh-storage-data"></a>Aktualizace úložiště dat

Pokud Průzkumník serveru se připojí k nebo získá data z účtu úložiště, operace může trvat až jednu minutu na dokončení. Pokud Průzkumník serveru nemůžete připojit, operace může být vypršení časového limitu. Když se data načítají, můžete pokračovat v práci v ostatních částech aplikace Visual Studio. Chcete-li zrušit operaci, pokud je trvá moc dlouho, vyberte **Zastavit aktualizaci** tlačítko na panelu nástrojů Průzkumníka serveru.

### <a name="to-refresh-blob-container-data"></a>Aktualizovat data objektů blob v kontejneru

* Vyberte **objekty BLOB** uzlu pod **úložiště**a pak vyberte **aktualizovat** tlačítko na panelu nástrojů Průzkumníka serveru.
* Chcete-li aktualizovat seznam objektů BLOB, který se zobrazí, vyberte **Execute** tlačítko.

### <a name="to-refresh-table-data"></a>Chcete-li aktualizovat data v tabulce

* Vyberte **tabulky** uzlu pod **úložiště**a pak vyberte **aktualizovat** tlačítko na panelu nástrojů Průzkumníka serveru.
* Chcete-li aktualizovat seznam entit, který se zobrazí v Návrháři tabulek, vyberte **Execute** tlačítko v Návrháři tabulek.

### <a name="to-refresh-queue-data"></a>Chcete-li aktualizovat data ve frontě

Vyberte **fronty** uzlu pod **úložiště**a pak vyberte **aktualizovat** tlačítko na panelu nástrojů Průzkumníka serveru.

### <a name="to-refresh-all-items-in-a-storage-account"></a>Chcete-li aktualizovat všechny položky v účtu úložiště

Zvolte název účtu a pak vyberte **aktualizovat** tlačítko na panelu nástrojů Průzkumníka serveru.

## <a name="add-storage-accounts-by-using-server-explorer"></a>Přidání účtů úložiště pomocí Průzkumníka serveru

Existují dva způsoby, jak přidat účty úložiště pomocí Průzkumníka serveru. Ve vašem předplatném Azure můžete vytvořit účet úložiště, nebo můžete připojit existující účet úložiště.

### <a name="to-create-a-storage-account-by-using-server-explorer"></a>Vytvoření účtu úložiště pomocí Průzkumníka serveru

1. V Průzkumníku serveru, otevřete místní nabídku **úložiště** uzlu a pak vyberte **vytvořit účet úložiště**.

1. V **vytvořit účet úložiště** dialogové okno Vyberte nebo zadejte následující informace:

   * Předplatné Azure, ke kterému chcete přidat účet úložiště.
   * Název, který chcete použít pro nový účet úložiště.
   * Oblast nebo skupina vztahů (třeba západní USA nebo jihovýchodní Asie).
   * Typ replikace, kterou chcete použít pro účet úložiště, jako místně redundantní.

   ![Vytvoření účtu úložiště Azure](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744166.png)

1. Vyberte **Vytvořit**.

Nový účet úložiště se zobrazí v **úložiště** seznamu v Průzkumníku řešení.

### <a name="to-attach-an-existing-storage-account-by-using-server-explorer"></a>Připojit existující účet úložiště pomocí Průzkumníka serveru

1. V Průzkumníku serveru, otevřete místní nabídku pro Azure **úložiště** uzlu a pak vyberte **připojit externí úložiště**.

    ![Přidání existující účet úložiště](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766039.png)
1. V **vytvořit účet úložiště** dialogové okno Vyberte nebo zadejte následující informace:

   * Název existující účet úložiště, který chcete připojit.
   * Klíč pro vybraný účet úložiště. Tato hodnota se většinou poskytuje vám při výběru účtu úložiště. Pokud chcete Visual Studio a klíč účtu úložiště mějte na paměti, vyberte **klíč účtu zapamatovat** zaškrtávací políčko.
   * Protokol, který se má použít pro připojení k účtu úložiště, jako je například HTTP, HTTPS nebo vlastního koncového bodu. Další informace o vlastních koncových bodech najdete v tématu [postupy konfigurace připojovacích řetězců](https://msdn.microsoft.com/library/azure/ee758697.aspx).

### <a name="to-view-the-secondary-endpoints"></a>Chcete-li zobrazit sekundární koncových bodů

Pokud jste nevytvořili účet úložiště pomocí **geograficky redundantní s přístupem pro čtení** možnost replikace můžete zobrazit jeho sekundární koncových bodů tak, že otevřete místní nabídku pro název účtu a pak vyberte **vlastnosti**.

![Sekundární koncových bodů úložiště](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766040.png)

### <a name="to-remove-a-storage-account-from-server-explorer"></a>Chcete-li odebrat účet úložiště z Průzkumníka serveru

V Průzkumníku serveru, otevřete místní nabídku pro název účtu a pak vyberte **odstranit**. 

Pokud odstraníte účet úložiště, odeberou se také žádné uložené informace o klíči pro tento účet.

Pokud odstraníte účet úložiště z Průzkumníka serveru, to nemá vliv na váš účet úložiště nebo všechna data, která obsahuje. Jednoduše odebere odkaz z Průzkumníka serveru. Chcete-li trvale odstranit účet úložiště, použijte [webu Azure portal](https://portal.azure.com/).

## <a name="next-steps"></a>Další postup

Další informace o tom, jak pomocí služby Azure storage najdete v tématu [přístup ke službám úložiště Azure](https://msdn.microsoft.com/library/azure/ee405490.aspx).

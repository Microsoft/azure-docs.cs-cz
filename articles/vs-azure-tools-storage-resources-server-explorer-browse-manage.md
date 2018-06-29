---
title: Procházet a spravovat prostředky úložiště pomocí Průzkumníka serveru | Microsoft Docs
description: Procházení a Správa prostředků úložiště pomocí Průzkumníka serveru
services: visual-studio-online
author: ghogen
manager: douge
assetId: 658dc064-4a4e-414b-ae5a-a977a34c930d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 8/24/2017
ms.author: ghogen
ms.openlocfilehash: c9828504ab40a1e62c107f218fda024be6330c23
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061739"
---
# <a name="browse-and-manage-storage-resources-by-using-server-explorer"></a>Procházet a spravovat prostředky úložiště pomocí Průzkumníka serveru

[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Přehled

Pokud jste nainstalovali nástroje Azure pro sadu Microsoft Visual Studio, můžete zobrazit blob, fronty a tabulky dat z účtů úložiště Azure. Azure **úložiště** uzlu v Průzkumníku serveru se zobrazuje data, která jsou v účtu emulátor místního úložiště a jiné účty úložiště Azure.

Chcete-li zobrazit v Průzkumníku serveru v sadě Visual Studio na řádku nabídek, vyberte **zobrazení** > **Průzkumníka serveru**. **Úložiště** uzlu se zobrazuje všechny účty úložiště, které existují v rámci každé předplatné Azure nebo certifikát, který jste připojení k. Pokud váš účet úložiště se nezobrazí, můžete ho přidat podle těchto pokynů [dále v tomto článku](#add-storage-accounts-by-using-server-explorer).

Počínaje Azure SDK 2.7, můžete také pomocí Průzkumníka cloudu k zobrazení a správě prostředků Azure. Další informace najdete v tématu [Správa Azure prostředků pomocí Průzkumníka cloudu](vs-azure-tools-resources-managing-with-cloud-explorer.md).

## <a name="view-and-manage-storage-resources-in-visual-studio"></a>Zobrazit a spravovat prostředky úložiště v sadě Visual Studio

V Průzkumníku serveru automaticky zobrazí seznam objektů BLOB, fronty a tabulky ve vašem účtu emulátoru úložiště. Tento účet emulátor úložiště je uvedený v Průzkumníku serveru pod **úložiště** uzlu jako **vývoj** uzlu.

Chcete-li zobrazit zdroje účet emulátor úložiště, rozbalte **vývoj** uzlu. Pokud při rozšiřování nebyla spuštěna emulátor úložiště **vývoj** uzlu, automaticky spustí. Tento proces může trvat několik sekund. Můžete pokračovat v práci v jiných oblastech sady Visual Studio, při spuštění emulátor úložiště.

Chcete-li zobrazit prostředky v účtu úložiště, rozbalte uzel účet úložiště v Průzkumníku serveru, kde uvidíte **objekty BLOB**, **fronty**, a **tabulky** uzlů.

## <a name="work-with-blob-resources"></a>Pracovat s prostředky objektů blob

**Objekty BLOB** uzlu zobrazí seznam kontejnery pro vybraný účet úložiště. Kontejnery objektů BLOB obsahovat soubory objektů blob a tyto objekty BLOB můžete uspořádat do složky a podsložky. Další informace najdete v tématu [postup používání úložiště Blob z .NET](storage/blobs/storage-dotnet-how-to-use-blobs.md).

### <a name="to-create-a-blob-container"></a>Chcete-li vytvořit kontejner objektů blob

1. Otevřete místní nabídku pro **objekty BLOB** uzel a potom vyberte **vytvořit kontejner objektů Blob**.
1. V **vytvořit kontejner objektů Blob** dialogovém okně zadejte název nového kontejneru.  
1. Vyberte Enter na klávesnici, nebo můžete klikněte nebo klepněte na mimo pole název kontejneru objektů blob uložit.

   > [!NOTE]
   > Název kontejneru objektu blob musí začínat řetězcem číslici (0-9) nebo malé písmeno (a – ž).

### <a name="to-delete-a-blob-container"></a>Chcete-li odstranit kontejner objektů blob

Otevřete místní nabídku pro kontejner objektů blob, který chcete odebrat a potom vyberte **odstranit**.

### <a name="to-display-a-list-of-the-items-in-a-blob-container"></a>Chcete-li zobrazit seznam položek v kontejneru objektů blob

Otevřete místní nabídku pro název kontejneru objektů blob v seznamu a pak vyberte **otevřete**.

Při zobrazení obsahu kontejneru objektů blob, se zobrazí na kartě známé jako zobrazení kontejneru objektů blob.

![Zobrazení kontejneru objektů BLOB](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC749016.png)

Pomocí tlačítek v pravém horním rohu zobrazení kontejneru objektů blob můžete provádět následující operace na objekty BLOB:

* Zadejte hodnotu filtru a použijte ho.
* Aktualizujte seznam objektů BLOB v kontejneru.
* Nahrajte soubor.
* Odstraňte objekt blob. (Odstranění souboru z kontejneru objektů blob neodstraní základního souboru. Jej pouze odeberete z kontejneru objektů blob.)
* Otevřete objekt blob.
* Uložte objekt blob do místního počítače.

### <a name="to-create-a-folder-or-subfolder-in-a-blob-container"></a>Chcete-li vytvořit složku nebo podsložku v kontejneru objektů blob

1. Vyberte kontejner objektů blob v Průzkumníku cloudu. V okně kontejner, vyberte **nahrát objekt Blob** tlačítko.

1. V **nahrát nový soubor** dialogové okno, vyberte **Procházet** tlačítko zadat soubor, který chcete nahrát a pak zadejte název složky v **složky (volitelné)** pole.

   ![Nahrání souboru do složky, objektů blob](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766037.png)

   Můžete přidat podsložky v kontejneru složky podle stejný krok. Pokud nezadáte název složky, soubor odeslat do nejvyšší úrovně kontejneru objektů blob. Soubor se zobrazí ve složce zadané v kontejneru.

   ![Složky přidat do kontejneru objektů blob](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766038.png)

1. Poklikejte na složku nebo vyberte Enter, zobrazí se obsah složky. Pokud jste ve složce kontejneru, můžete přejít zpět na kořenovém kontejneru výběrem **otevřete nadřazený adresář** tlačítko (šipka).

### <a name="to-delete-a-container-folder"></a>Odstranění složky kontejneru

Odstraní všechny soubory ve složce.

Protože složky v kontejnerech objektů blob jsou virtuální složky, nelze vytvořit k prázdné složce. Také nemůže odstranit složku odstranit její obsah souboru, ale místo toho musíte odstranit celý obsah složky odstranit samotné složce.

### <a name="to-filter-blobs-in-a-container"></a>Chcete-li filtrovat objekty BLOB v kontejneru

Můžete filtrovat objekty BLOB, které se zobrazují tak, že zadáte předponu běžné.

Například pokud zadáte předponu **hello** v textovém poli Filtr a potom vyberte **Execute** (**!**) tlačítko zobrazí pouze objekty BLOB, které začínají text "hello".

![Filtr textového pole](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC519076.png)

Textového pole filtru je malá a velká písmena a nepodporuje filtrování se zástupnými znaky. Objekty BLOB můžete filtrovat pouze podle předpony. Předpona, která může zahrnovat oddělovač, pokud používáte oddělovač pro uspořádání objektů BLOB v hierarchii virtuální. Například filtrování předponu "HelloFabric /" vrátí všechny objekty BLOB, které začínají tento řetězec.

### <a name="to-download-blob-data"></a>Chcete-li stáhnout data objektů blob

V Průzkumníku cloudu použijte některou z následujících metod:

* Otevřete místní nabídku pro jeden nebo více objektů BLOB a potom vyberte **otevřete**.
* Zvolte název objektu blob a potom vyberte **otevřete** tlačítko.
* Dvakrát klikněte na název objektu blob.

Zobrazí se průběh stahování objektů blob v **protokol činnosti Azure** okno.

Objekt blob se otevře v editoru výchozí pro tento typ souboru. Pokud operační systém rozpozná typ souboru, soubor se otevře v lokálně nainstalované aplikace. Jinak budete vyzváni k výběru aplikace, která je vhodná pro typ souboru objektu blob. Místní soubor, který se vytvoří, když si stáhnete objekt blob je určen jen pro čtení.

Data objektu BLOB je v místní mezipaměti a kontrolovat čas poslední změny objektu blob v Azure Blob storage. Pokud od poslední stažení objektu blob aktualizován, se znovu stáhnout. Objekt blob, jinak je načten z místního disku.

Ve výchozím nastavení je stáhne objekt blob do dočasného adresáře. Chcete-li stáhnout objekty BLOB do určitého adresáře, otevřete místní nabídku pro názvy vybraných objektů blob a vyberte **uložit jako**. Při ukládání objektu blob tímto způsobem, není otevřený soubor blob a místního souboru je vytvořena s atributy pro čtení a zápis.

### <a name="to-upload-blobs"></a>Nahrát objektů BLOB

Chcete-li nahrát objekty BLOB, vyberte **nahrát objekt Blob** když je otevřen pro zobrazení v zobrazení kontejneru objektů blob kontejneru.

Můžete vybrat jeden nebo více souborů pro nahrání a můžete nahrát souborů všech typů. **Protokol činnosti Azure** v okně se zobrazí průběh nahrávání. Další informace o tom, jak pracovat s daty objektu blob najdete v tématu [jak používat úložiště objektů Blob v Azure v rozhraní .NET](http://go.microsoft.com/fwlink/p/?LinkId=267911).

### <a name="to-view-logs-transferred-to-blobs"></a>Pokud chcete zobrazit protokoly přenést na objekty BLOB

Pokud používáte Azure Diagnostics protokolování dat z aplikace Azure a protokoly přenesly na váš účet úložiště, uvidíte kontejnery, které Azure vytvořené pro tyto protokoly. Tyto protokoly zobrazení v Průzkumníku serveru je snadný způsob, jak identifikovat problémy s aplikací, zvlášť pokud je nasazený do Azure.

Další informace o Azure Diagnostics najdete v tématu [shromažďovat protokolování dat pomocí Azure Diagnostics](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### <a name="to-get-the-url-for-a-blob"></a>Získat adresu URL pro objekt blob

Otevřete místní nabídky objektu blob a potom vyberte **kopie URL**.

### <a name="to-edit-a-blob"></a>Chcete-li upravit objekt blob

Vyberte objekt blob a pak vyberte **otevřete Blob** tlačítko.

Soubor se stáhne do dočasného umístění a otevřít v místním počítači. Načtení objektu blob znovu po provedení změn.

## <a name="work-with-queue-resources"></a>Pracovat s prostředky fronty

Fronty služby úložiště jsou hostované v účtu úložiště Azure. Můžete je umožňuje vaše cloudové služby rolí pro komunikaci mezi sebou a s jinými službami pomocí mechanismus předávání zpráv. Prostřednictvím cloudové služby a přes webové služby pro externí klienty můžete programově přistupovat ke frontě. Přímo pomocí Průzkumníka serveru v sadě Visual Studio můžete také přistupovat ke frontě.

Při vývoji Cloudová služba, která používá fronty, můžete chtít použít Visual Studio k vytvoření fronty a pracovat s nimi interaktivně, když jste sami vyvinuli a otestujte svůj kód.

V Průzkumníku serveru můžete zobrazit fronty v účtu úložiště, vytvoření a odstranění front, otevření fronty k zobrazení jeho zpráv a taky přidat zprávy do fronty. Při otevření fronty pro zobrazení, můžete zobrazit jednotlivé zprávy a můžete provádět následující akce pro frontu pomocí tlačítka v levém horním rohu:

* Aktualizujte zobrazení fronty.
* Přidejte do fronty zprávu.
* Dequeue – nejhornější zprávy.
* Vymazání celého fronty.

Následující obrázek znázorňuje fronty, který obsahuje dvě zprávy:

![Zobrazení fronty](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC651470.png)

Další informace o úložišti služeb fronty naleznete v tématu [Začínáme s Azure Queue storage pomocí rozhraní .NET](http://go.microsoft.com/fwlink/?LinkID=264702). Informace o webové službě úložiště služeb fronty naleznete v tématu [koncepty služby front](http://go.microsoft.com/fwlink/?LinkId=264788). Informace o tom, jak odesílat zprávy do fronty služby úložiště pomocí sady Visual Studio najdete v tématu [odeslání zprávy do fronty služby úložiště](https://docs.microsoft.com/azure/visual-studio/vs-storage-cloud-services-getting-started-queues).

> [!NOTE]
> Fronty úložiště služby se liší od fronty Azure Service Bus. Další informace o fronty Service Bus najdete v tématu [Service Bus fronty, témata a odběry](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-queues-topics-subscriptions).

## <a name="work-with-table-resources"></a>Pracovat s prostředky tabulky

Služba Azure Table Storage ukládá velké objemy strukturovaných dat. Služba je úložištěm dat typu NoSQL, která přijímá ověřených volání z uvnitř i vně cloudu Azure. Tabulky Azure jsou ideální pro ukládání strukturovaných, nerelačních dat.

### <a name="to-create-a-table"></a>K vytvoření tabulky

1. V Průzkumníku cloudu, vyberte **tabulky** uzlu účtu úložiště a potom vyberte **Create Table**.
1. V **Create Table** dialogovém okně zadejte název tabulky.

### <a name="to-view-table-data"></a>Chcete-li zobrazit data tabulky

1. V Průzkumníku cloudu, otevřete **Azure** uzel a potom otevřete **úložiště** uzlu.
1. Otevřete uzlu účet úložiště, který se zajímá a pak otevřete **tabulky** uzlu zobrazíte seznam tabulek pro účet úložiště.
1. Otevřete místní nabídku pro tabulky a pak vyberte **zobrazení tabulky**.

    ![Tabulky Azure v Průzkumníku řešení](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744165.png)

Tabulka je seřazená podle entity (zobrazené v řádcích) a vlastnosti (zobrazené ve sloupcích). Například na další obrázku entit, které jsou uvedené v návrháře tabulky.

### <a name="to-edit-table-data"></a>Chcete-li upravit data tabulky

V návrháře tabulky, otevřete místní nabídku pro entitu (jeden řádek) nebo vlastnost (jedné buňky) a pak vyberte **upravit**.

    ![Add or edit a table entity](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC656238.png)

Entity v jediné tabulce nejsou potřeba mít stejnou sadu vlastností (sloupce). Mějte na paměti následující omezení pro prohlížení a úpravy dat v tabulce:

* Nelze zobrazit nebo upravit binární data (`type byte[]`), ale můžete ho uložit v tabulce.
* Nelze upravit **PartitionKey** nebo **RowKey** hodnoty, protože úložiště Table v Azure nepodporuje tuto operaci.
* Nelze vytvořit vlastnost s názvem **časové razítko**. Služby Azure storage použít vlastnost s tímto názvem.
* Pokud zadáte **data a času** hodnotu, musí odpovídat formátu, který je vhodný pro místní a jazykové nastavení počítače (například MM/DD/RRRR HH: mm: [AM | PM] pro čeština).

### <a name="to-add-entities"></a>Přidání entity

1. V návrháře tabulky, vyberte **Přidat entitu** tlačítko.

    ![Přidání tlačítka Entity](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655336.png)

1. V **Přidat entitu** dialogovém okně zadejte hodnoty **PartitionKey** a **RowKey** vlastnosti.

    ![Entity dialogové okno Přidat](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655335.png)

    Zadejte hodnoty pečlivě. Nelze je změnit po zavřete dialogové okno, pokud odstranit entitu a ji znovu přidejte.

### <a name="to-filter-entities"></a>Chcete-li filtrovat entity

Můžete přizpůsobit sadu entit, které se zobrazují v tabulce, pokud používáte Tvůrce dotazů.

1. Chcete-li otevřít Tvůrce dotazů, otevřete tabulku pro zobrazení.
1. Vyberte **Tvůrce dotazů** tlačítka na panelu nástrojů zobrazení tabulky.

    **Tvůrce dotazů** zobrazí se dialogové okno. Následující obrázek znázorňuje dotaz, který sestavuje v Tvůrce dotazů.

    ![Tvůrce dotazů](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC652231.png)
1. Po dokončení sestavení dotazu, zavřete dialogové okno. Výsledný formulář text dotazu se zobrazí v textovém poli jako filtr součásti WCF Data Services.
1. Pokud chcete spustit dotaz, vyberte ikonu zeleným trojúhelníkem.

Můžete také filtrovat data entity, která se zobrazí v Návrháři tabulky Pokud zadáte řetězec filtru datových služeb WCF přímo do textového pole filtru. Tento druh řetězec je podobná SQL klauzule WHERE, ale je odeslána na server jako požadavek HTTP. Informace o tom, jak vytvořit filtr řetězců najdete v tématu [vytváření řetězců filtru pro návrháře tabulky](https://msdn.microsoft.com/library/azure/ff683669.aspx).

Následující obrázek znázorňuje příklad řetězec platný filtru:

![Řetězec filtru](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655337.png)

## <a name="refresh-storage-data"></a>Aktualizovat data úložiště

Když v Průzkumníku serveru se připojí k nebo získá data z účtu úložiště, operace může trvat až několik minut na dokončení. Pokud se nemůže připojit v Průzkumníku serveru, může operaci vypršení časového limitu. Když jsou načtena data, můžete pokračovat v práci v dalších částí sady Visual Studio. Chcete-li zrušit operaci, pokud to trvá příliš dlouho, vyberte **zrušit aktualizaci** tlačítka na panelu nástrojů Průzkumníka serveru.

### <a name="to-refresh-blob-container-data"></a>Chcete-li aktualizovat data kontejneru objektů blob

* Vyberte **objekty BLOB** uzlu pod **úložiště**a pak vyberte **aktualizovat** tlačítka na panelu nástrojů Průzkumníka serveru.
* Chcete-li aktualizovat seznam objektů BLOB, který se zobrazí, vyberte **Execute** tlačítko.

### <a name="to-refresh-table-data"></a>Aktualizace dat v tabulce

* Vyberte **tabulky** uzlu pod **úložiště**a pak vyberte **aktualizovat** tlačítka na panelu nástrojů Průzkumníka serveru.
* Chcete-li aktualizovat seznam sad entit, který se zobrazí v Návrháři tabulky, vyberte **Execute** tlačítka na návrháře tabulky.

### <a name="to-refresh-queue-data"></a>Aktualizovat data fronty

Vyberte **fronty** uzlu pod **úložiště**a pak vyberte **aktualizovat** tlačítka na panelu nástrojů Průzkumníka serveru.

### <a name="to-refresh-all-items-in-a-storage-account"></a>Chcete-li aktualizovat všechny položky v účtu úložiště

Zvolte název účtu a pak vyberte **aktualizovat** tlačítka na panelu nástrojů Průzkumníka serveru.

## <a name="add-storage-accounts-by-using-server-explorer"></a>Přidejte účty úložiště pomocí Průzkumníka serveru

Existují dva způsoby, jak přidat účty úložiště pomocí Průzkumníka serveru. Ve vašem předplatném Azure můžete vytvořit účet úložiště, nebo můžete připojit existující účet úložiště.

### <a name="to-create-a-storage-account-by-using-server-explorer"></a>Chcete-li vytvořit účet úložiště pomocí Průzkumníka serveru

1. V Průzkumníku serveru otevřete místní nabídku pro **úložiště** uzel a potom vyberte **vytvořit účet úložiště**.

1. V **vytvořit účet úložiště** dialogovém okně vyberte nebo zadejte následující informace:

   * Předplatné Azure, ke které chcete přidat účet úložiště.
   * Název, který chcete použít pro nový účet úložiště.
   * Oblast nebo skupinu vztahů (například západní USA nebo jihovýchodní Asie).
   * Typ replikace, kterou chcete použít pro účet úložiště, jako například místně redundantní.

   ![Vytvoření účtu úložiště Azure](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744166.png)

1. Vyberte **Vytvořit**.

Nový účet úložiště zobrazí v **úložiště** seznamu v Průzkumníku řešení.

### <a name="to-attach-an-existing-storage-account-by-using-server-explorer"></a>Připojit stávající účet úložiště pomocí Průzkumníka serveru

1. V Průzkumníku serveru otevřete místní nabídky pro Azure **úložiště** uzel a potom vyberte **připojit externí úložiště**.

    ![Přidání stávající účet úložiště](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766039.png)
1. V **vytvořit účet úložiště** dialogovém okně vyberte nebo zadejte následující informace:

   * Název existující účet úložiště, který chcete připojit.
   * Klíč pro vybraný účet úložiště. Tato hodnota se většinou poskytuje pro vás, když vyberete účet úložiště. Pokud chcete Visual Studio pamatovat klíč účtu úložiště, vyberte **klíč účtu zapamatovat** zaškrtávací políčko.
   * Protokol pro připojení k účtu úložiště, jako je například HTTP, HTTPS nebo vlastní koncový bod. Další informace o vlastní koncové body najdete v tématu [postup nakonfigurování připojovacích řetězců](https://msdn.microsoft.com/library/azure/ee758697.aspx).

### <a name="to-view-the-secondary-endpoints"></a>Chcete-li zobrazit sekundární koncové body

Pokud jste vytvořili účet úložiště pomocí **přístup pro čtení geograficky redundantní** možnost replikace, můžete zobrazit jeho sekundární koncových bodů tak, že otevřete místní nabídku pro název účtu a potom vyberte **vlastnosti**.

![Koncové body sekundární úložiště](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766040.png)

### <a name="to-remove-a-storage-account-from-server-explorer"></a>Chcete odstranit účet úložiště z Průzkumníka serveru

V Průzkumníku serveru otevřete místní nabídku pro název účtu a pak vyberte **odstranit**. 

Pokud odstraníte účet úložiště, odeberou se také žádné uložené informace o klíči pro tohoto účtu.

Pokud odstraníte účet úložiště z Průzkumníka serveru, nemá to vliv účtu úložiště nebo všechna data, která obsahuje. Odstraní pouze odkaz z Průzkumníka serveru. Pokud chcete trvale odstranit účet úložiště, použijte [portál Azure](https://portal.azure.com/).

## <a name="next-steps"></a>Další postup

Další informace o tom, jak používat služby Azure storage najdete v tématu [přístup ke službám úložiště Azure](https://msdn.microsoft.com/library/azure/ee405490.aspx).

---
title: Správa prostředků Azure Blob Storage pomocí Storage Exploreru | Dokumentace Microsoftu
description: Správa objektů Blob v Azure kontejnerům a objektům BLOB pomocí Průzkumníka služby Storage
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: 2f09e545-ec94-4d89-b96c-14783cc9d7a9
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: 8315647afec0166b41ce7a75f491077edd56db46
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036256"
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer"></a>Správa prostředků Azure Blob Storage pomocí Storage Exploreru
## <a name="overview"></a>Přehled
[Azure Blob Storage](storage/blobs/storage-dotnet-how-to-use-blobs.md) je služba pro ukládání velkých objemů nestrukturovaných dat, jako jsou textová nebo binární data, ke kterým můžete přistupovat odkudkoli na světě prostřednictvím protokolu HTTP nebo HTTPS.
Službu Blob Storage můžete používat ke zveřejňování dat pro celý svět, nebo k soukromému ukládání dat aplikací. V tomto článku se dozvíte, jak pracovat s kontejnery objektů blob a objekty BLOB pomocí Průzkumníka služby Storage.

## <a name="prerequisites"></a>Požadavky
K dokončení kroků v tomto článku budete potřebovat následující:

* [Stažení a instalace Průzkumníka služby Storage](http://www.storageexplorer.com)
* [Připojení k účtu úložiště nebo službě Azure](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Vytvoření kontejneru objektů blob
Všechny objekty BLOB se musí nacházet v kontejneru objektů blob, který je jednoduše logické seskupení objektů BLOB. Účet může obsahovat neomezený počet kontejnerů a každý kontejner můžete pojmout neomezený počet objektů BLOB.

Následující postup ukazuje, jak vytvořit kontejner objektů blob v Průzkumníku služby Storage.

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště, ve kterém chcete vytvořit kontejner objektů blob.
3. Klikněte pravým tlačítkem na **kontejnery objektů Blob**a v místní nabídce – vyberte **vytvořit kontejner objektů Blob**.

   ![Vytvořit kontejnery místní nabídka objektu blob][0]
4. Níže se zobrazí textové pole **kontejnery objektů Blob** složky. Zadejte název kontejneru objektů blob. Zobrazit [vytvoření kontejneru a nastavení oprávnění](storage/blobs/storage-quickstart-blobs-dotnet.md#create-the-container-and-set-permissions) informace o pravidlech a omezení pro pojmenování kontejnerů objektů blob.

   ![Vytvoření kontejnerů objektů Blob textového pole][1]
5. Stisknutím klávesy **Enter** po dokončení vytvoření kontejneru objektů blob nebo **Esc** zrušit. Po úspěšném vytvoření kontejneru objektů blob se zobrazí v části **kontejnery objektů Blob** složku pro vybraný účet úložiště.

   ![Vytvoření kontejneru objektů BLOB][2]

## <a name="view-a-blob-containers-contents"></a>Zobrazení obsahu objektů blob v kontejneru
Kontejnery objektů BLOB obsahují objekty BLOB a složky (ty také můžou obsahovat objekty BLOB).

Následující postup ukazuje, jak zobrazit obsah kontejneru objektů blob v Průzkumníku služby Storage:

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště obsahující kontejner objektů blob, že kterou chcete zobrazit.
3. Rozbalte účet úložiště **kontejnery objektů Blob**.
4. Klikněte pravým tlačítkem na kontejner objektů blob, který chcete zobrazit a v místní nabídce – vyberte **otevřít Editor objektů Blob v kontejneru**.
   Můžete také dvakrát kliknout na kontejner objektů blob, který chcete zobrazit.

   ![Místní nabídka editoru otevřít objektů blob v kontejneru][19]
5. V hlavním podokně se zobrazí obsah kontejneru objektů blob.

   ![Editor kontejneru objektů BLOB][3]

## <a name="delete-a-blob-container"></a>Odstranit kontejner objektů blob
Kontejnery objektů BLOB můžete snadno vytvořit a odstranit podle potřeby. (Informace o tom Chcete-li odstranit jednotlivé objekty BLOB, naleznete v části [Správa objektů BLOB v kontejneru objektů blob](#managing-blobs-in-a-blob-container).)

Následující postup ukazuje, jak odstranit kontejner objektů blob v Průzkumníku služby Storage:

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště obsahující kontejner objektů blob, že kterou chcete zobrazit.
3. Rozbalte účet úložiště **kontejnery objektů Blob**.
4. Klikněte pravým tlačítkem na kontejner objektů blob, který chcete odstranit a v místní nabídce – vyberte **odstranit**.
   Můžete také stisknout klávesu **odstranit** odstranit kontejner objektů blob v aktuálně vybraných.

   ![Odstranění objektů blob v kontejneru kontextové nabídky][4]
5. V potvrzovacím dialogovém okně klikněte na **Ano**.

   ![Odstranění objektů blob v kontejneru potvrzení][5]

## <a name="copy-a-blob-container"></a>Kopírovat kontejner objektů blob
Průzkumníka služby Storage umožňuje zkopírování kontejner objektů blob do schránky a vložte tento kontejner objektů blob do jiného účtu úložiště. (Informace o tom ke kopírování jednotlivých objektů BLOB najdete v části [Správa objektů BLOB v kontejneru objektů blob](#managing-blobs-in-a-blob-container).)

Následující postup ukazuje, jak kopírovat kontejner objektů blob z jednoho účtu úložiště do druhého.

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště obsahující kontejner objektů blob, že kterou chcete kopírovat.
3. Rozbalte účet úložiště **kontejnery objektů Blob**.
4. Klikněte pravým tlačítkem na kontejner objektů blob, který chcete zkopírovat a v místní nabídce – vyberte **kopírování objektů Blob v kontejneru**.

   ![Kopírování objektů blob v kontejneru kontextové nabídky][6]
5. Klikněte pravým tlačítkem na účet úložiště požadovaného "target", do které chcete vložit kontejner objektů blob a v místní nabídce – vyberte **vložit kontejner objektů Blob**.

   ![Místní nabídka vložení objektu blob kontejneru][7]

## <a name="get-the-sas-for-a-blob-container"></a>Získání sdíleného přístupového podpisu (SAS) pro kontejner objektů blob
[Sdílený přístupový podpis (SAS)](storage/common/storage-dotnet-shared-access-signature-part-1.md) poskytuje delegovaný přístup k prostředkům ve vašem účtu úložiště.
To znamená, že můžete klientovi udělit omezená oprávnění k objektům ve vašem účtu úložiště po stanovené časové období a s konkrétní sadou oprávnění, aniž byste museli sdílet přístupové klíče vašeho účtu.

Následující postup ukazuje, jak vytvořit SAS pro kontejner objektů blob:

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště, který obsahuje kontejner objektů blob, pro kterou chcete získat SAS.
3. Rozbalte účet úložiště **kontejnery objektů Blob**.
4. Klikněte pravým tlačítkem na kontejner objektů blob požadované a v místní nabídce – vyberte **získat sdílený přístupový podpis**.

   ![Získat SAS kontextové nabídky][8]
5. V dialogovém okně **Sdílený přístupový podpis** zadejte zásadu, počáteční datum a datum vypršení platnosti, časové pásmo a požadované úrovně přístupu k prostředku.

   ![Získání možností sdíleného přístupového podpisu][9]
6. Jakmile budete hotovi se zadáváním možností sdíleného přístupového podpisu, vyberte **Vytvořit**.
7. Sekundy **sdílený přístupový podpis** dialogové okno zobrazí, který obsahuje seznam objektů blob v kontejneru spolu s adresy URL a řetězci dotazu, můžete použít pro přístup k prostředku úložiště.
   Vyberte **Kopírovat** vedle adresy URL, kterou chcete zkopírovat do schránky.

   ![Zkopírování adresy URL SAS][10]
8. Až budete hotovi, vyberte **Zavřít**.

## <a name="manage-access-policies-for-a-blob-container"></a>Správa zásad přístupu pro kontejner objektů blob
Následující postup ukazuje, jak spravovat (přidávat a odebírat) zásady přístupu pro kontejner objektů blob:

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště, který obsahuje kontejner objektů blob, kterou chcete spravovat zásady přístupu.
3. Rozbalte účet úložiště **kontejnery objektů Blob**.
4. Vyberte kontejner objektů blob požadované a v místní nabídce – vyberte **spravovat zásady přístupu**.

   ![Místní nabídka Spravovat zásady přístupu][11]
5. **Zásady přístupu** dialogového okna se zobrazí seznam zásad přístupu vytvořeny již pro vybrané objektů blob v kontejneru.

   ![Možnosti zásad přístupu][12]        
6. V závislosti na úloze správy zásad přístupu postupujte podle těchto kroků:

   * **Přidání nové zásady přístupu:** Vyberte **Přidat**. Po vygenerování se nová zásada přístupu (s výchozím nastavením) zobrazí v dialogovém okně **Zásady přístupu**.
   * **Upravit zásadu přístupu** – proveďte veškeré požadované úpravy a vyberte **Uložit**.
   * **Odebrání zásady přístupu:** Vyberte **Odebrat** vedle zásady přístupu, kterou chcete odebrat.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Nastavte úroveň veřejného přístupu pro kontejner objektů blob
Ve výchozím nastavení každý kontejner objektů blob nastavená na "Žádný veřejný přístup".

Následující postup ukazuje, jak určit úroveň veřejného přístupu pro kontejner objektů blob.

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště, který obsahuje kontejner objektů blob, kterou chcete spravovat zásady přístupu.
3. Rozbalte účet úložiště **kontejnery objektů Blob**.
4. Vyberte kontejner objektů blob požadované a v místní nabídce – vyberte **nastavte úroveň veřejného přístupu**.

   ![Nastavení veřejného přístupu úrovně kontextové nabídky][13]
5. V **nastavte úroveň veřejného přístupu kontejneru** dialogové okno, zadejte úroveň požadovaný přístup.

   ![Nastavení možností úroveň veřejného přístupu][14]
6. Vyberte **Použít**.

## <a name="managing-blobs-in-a-blob-container"></a>Správa objektů BLOB v kontejneru objektů blob
Po vytvoření kontejneru objektů blob, můžete nahrát objekt blob do tohoto kontejneru objektů blob, stažení objektu blob na místní počítač, otevřít objekt blob na místním počítači a spoustu dalších věcí.

Následující kroky ukazují, jak spravovat objekty BLOB (a složky) v rámci kontejneru objektů blob.

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště obsahující kontejner objektů blob, že kterou chcete spravovat.
3. Rozbalte účet úložiště **kontejnery objektů Blob**.
4. Dvakrát klikněte na kontejner objektů blob, který chcete zobrazit.
5. V hlavním podokně se zobrazí obsah kontejneru objektů blob.

   ![Zobrazit kontejner objektů blob][3]
6. V hlavním podokně se zobrazí obsah kontejneru objektů blob.
7. V závislosti na úloze, kterou chcete provést, postupujte podle těchto kroků:

   * **Nahrání souborů do kontejneru objektů blob**

     1. Na panelu nástrojů v hlavním podokně vyberte **Nahrát** a pak v rozevírací nabídce vyberte **Nahrát soubory**.

        ![Nabídka Nahrát soubory][15]
     2. V dialogovém okně **Nahrát soubory** vyberte tlačítko se třemi tečkami (**...**) na pravé straně textového pole **Soubory** a vyberte soubory, které chcete nahrát.

        ![Nahrát soubory možnosti][16]
     3. Zadejte typ **typ blobu**. Zobrazit [vytvoření kontejneru a nastavení oprávnění](storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-the-container) Další informace.
     4. Volitelně můžete zadejte cílovou složku, do kterého se nahraje vybrané soubory. Pokud cílová složka neexistuje, vytvoří se.
     5. Vyberte **Nahrát**.
   * **Nahrání složky do kontejneru objektů blob**

     1. Na panelu nástrojů v hlavním podokně vyberte **Nahrát** a pak v rozevírací nabídce vyberte **Nahrát složku**.

        ![Nabídka Nahrát složku][17]
     2. V dialogovém okně **Nahrát složku** vyberte tlačítko se třemi tečkami (**...**) na pravé straně textového pole **Složka** a vyberte složku, jejíž obsah chcete nahrát.

        ![Nahrát Možnosti složky][18]
     3. Zadejte typ **typ blobu**. Zobrazit [vytvoření kontejneru a nastavení oprávnění](storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-the-container) Další informace.
     4. Volitelně můžete zadat cílovou složku, do které se obsah vybrané složky nahraje. Pokud cílová složka neexistuje, vytvoří se.
     5. Vyberte **Nahrát**.
   * **Stažení objektu blob na místní počítač**

     1. Vyberte objekt blob, který chcete stáhnout.
     2. Na panelu nástrojů v hlavním podokně vyberte **Stáhnout**.
     3. V **zadejte, kam chcete uložit stažený objekt blob** dialogové okno, zadejte umístění, kam chcete stáhnout objekt blob a název složky je.  
     4. Vyberte **Uložit**.
   * **Otevřít objekt blob do místního počítače**

     1. Vyberte objekt blob, který chcete otevřít.
     2. Na panelu nástrojů v hlavním podokně vyberte **Otevřít**.
     3. Objekt blob se stáhne a otevře pomocí aplikace přidružené k jeho základnímu typu tohoto souboru.
   * **Kopírovat objekt blob do schránky.**

     1. Vyberte objekt blob, který chcete kopírovat.
     2. Na panelu nástrojů v hlavním podokně vyberte **Kopírovat**.
     3. V levém podokně přejděte do jiného kontejneru objektů blob a dvojím kliknutím ho zobrazte v hlavním podokně.
     4. Na panelu nástrojů v hlavním podokně vyberte **vložit** a vytvořte kopii objektu blob.
   * **Odstranit objekt blob**

     1. Vyberte objekt blob, který chcete odstranit.
     2. Na panelu nástrojů v hlavním podokně vyberte **Odstranit**.
     3. V potvrzovacím dialogovém okně klikněte na **Ano**.

## <a name="next-steps"></a>Další postup
* Podívejte se na [nejnovější poznámky k verzi a videa pro Průzkumníka služby Storage](http://www.storageexplorer.com).
* Zjistěte, jak [vytvářet aplikace pomocí objektů blob, tabulek, dotazů a souborů Azure](https://azure.microsoft.com/documentation/services/storage/).

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png

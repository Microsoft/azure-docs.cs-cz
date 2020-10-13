---
title: Správa prostředků Azure Blob Storage s využitím Průzkumník služby Storage | Microsoft Docs
description: Správa prostředků Azure Blob Storage pomocí Průzkumník služby Storage Vytvoření kontejneru objektů blob, zobrazení obsahu kontejneru objektů blob, odstranění nebo zkopírování kontejneru objektů BLOB a dalších.
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
ms.date: 05/21/2019
ms.author: cawa
ms.openlocfilehash: 07652f2ac88475664f30e8189204b5649f966d16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88033610"
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer"></a>Správa prostředků služby Azure Blob Storage pomocí Průzkumníka služby Storage

## <a name="overview"></a>Přehled

[Azure Blob Storage](storage/blobs/storage-dotnet-how-to-use-blobs.md) je služba pro ukládání velkých objemů nestrukturovaných dat, jako jsou textová nebo binární data, ke kterým se dá dostat odkudkoli na světě přes HTTP nebo HTTPS.
Službu Blob Storage můžete používat ke zveřejňování dat pro celý svět, nebo k soukromému ukládání dat aplikací. V tomto článku se dozvíte, jak používat Průzkumník služby Storage pro práci s kontejnery objektů BLOB a objekty blob.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku budete potřebovat následující:

* [Stažení a instalace Průzkumníka služby Storage](https://www.storageexplorer.com)
* [Připojení k účtu nebo službě Azure Storage](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Vytvoření kontejneru objektů blob

Všechny objekty BLOB se musí nacházet v kontejneru objektů blob, což je jednoduše logické seskupení objektů BLOB. Účet může obsahovat neomezený počet kontejnerů a každý kontejner může ukládat neomezený počet objektů BLOB.

Následující kroky ukazují, jak vytvořit kontejner objektů BLOB v rámci Průzkumník služby Storage.

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště, ve kterém chcete vytvořit kontejner objektů BLOB.
3. Klikněte pravým tlačítkem na **kontejnery objektů BLOB**a v místní nabídce vyberte **vytvořit kontejner objektů BLOB**.

   ![Místní nabídka pro vytvoření kontejnerů objektů BLOB][0]
4. Pod složkou **kontejnerů objektů BLOB** se zobrazí textové pole. Zadejte název kontejneru objektů blob. Informace o pravidlech a omezeních pro pojmenování kontejnerů objektů BLOB najdete v tématu věnovaném [vytvoření kontejneru](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) .

   ![Textové pole pro vytvoření kontejnerů objektů BLOB][1]
5. Pokud chcete vytvořit kontejner objektů blob, stiskněte klávesu **ENTER** , nebo operaci zrušte stisknutím klávesy **ESC** . Po úspěšném vytvoření kontejneru objektů BLOB se zobrazí ve složce **kontejnery objektů BLOB** pro vybraný účet úložiště.

   ![Kontejner objektů BLOB byl vytvořen.][2]

## <a name="view-a-blob-containers-contents"></a>Zobrazení obsahu kontejneru objektů BLOB

Kontejnery objektů BLOB obsahují objekty BLOB a složky (které můžou také obsahovat objekty BLOB).

Následující postup ukazuje, jak zobrazit obsah kontejneru objektů BLOB v rámci Průzkumník služby Storage:

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště obsahující kontejner objektů blob, který chcete zobrazit.
3. Rozbalte **kontejnery objektů BLOB**v účtu úložiště.
4. Klikněte pravým tlačítkem na kontejner objektů blob, který chcete zobrazit, a v místní nabídce vyberte **otevřít Editor kontejnerů objektů BLOB**.
   Můžete také dvakrát kliknout na kontejner objektů blob, který chcete zobrazit.

   ![Otevřít místní nabídku editoru kontejnerů objektů BLOB][19]
5. V hlavním podokně se zobrazí obsah kontejneru objektů BLOB.

   ![Editor kontejneru objektů BLOB][3]

## <a name="delete-a-blob-container"></a>Odstranění kontejneru objektů BLOB

Kontejnery objektů BLOB lze snadno vytvořit a odstranit podle potřeby. (Informace o tom, jak odstranit jednotlivé objekty blob, najdete v části [Správa objektů BLOB v kontejneru objektů BLOB](#managing-blobs-in-a-blob-container).)

Následující postup ukazuje, jak odstranit kontejner objektů BLOB v Průzkumník služby Storage:

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště obsahující kontejner objektů blob, který chcete zobrazit.
3. Rozbalte **kontejnery objektů BLOB**v účtu úložiště.
4. Klikněte pravým tlačítkem na kontejner objektů blob, který chcete odstranit, a v místní nabídce vyberte **Odstranit**.
   K odstranění aktuálně vybraného kontejneru objektů blob můžete také stisknout klávesu **Delete** .

   ![Místní nabídka odstranění kontejneru objektů BLOB][4]
5. V potvrzovacím dialogovém okně klikněte na **Ano**.

   ![Potvrzení odstranění kontejneru objektů BLOB][5]

## <a name="copy-a-blob-container"></a>Kopírování kontejneru objektů BLOB

Průzkumník služby Storage umožňuje zkopírovat kontejner objektů blob do schránky a potom tento kontejner objektů BLOB vložit do jiného účtu úložiště. (Informace o tom, jak kopírovat jednotlivé objekty blob, najdete v části [Správa objektů BLOB v kontejneru objektů BLOB](#managing-blobs-in-a-blob-container).)

Následující kroky ukazují, jak kopírovat kontejner objektů BLOB z jednoho účtu úložiště do jiného.

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště obsahující kontejner objektů blob, který chcete zkopírovat.
3. Rozbalte **kontejnery objektů BLOB**v účtu úložiště.
4. Klikněte pravým tlačítkem na kontejner objektů blob, který chcete zkopírovat, a v místní nabídce vyberte **Kopírovat kontejner objektů BLOB**.

   ![Kopírovat kontextovou nabídku kontejneru objektů BLOB][6]
5. Klikněte pravým tlačítkem na požadovaný cílový účet úložiště, do kterého chcete kontejner objektů BLOB vložit, a v místní nabídce vyberte **Vložit kontejner objektů BLOB**.

   ![Místní nabídka Vložit kontejner objektů BLOB][7]

## <a name="get-the-sas-for-a-blob-container"></a>Získání sdíleného přístupového podpisu (SAS) pro kontejner objektů blob

[Sdílený přístupový podpis (SAS)](storage/common/storage-dotnet-shared-access-signature-part-1.md) poskytuje delegovaný přístup k prostředkům ve vašem účtu úložiště.
To znamená, že můžete klientovi udělit omezená oprávnění k objektům ve vašem účtu úložiště po stanovené časové období a s konkrétní sadou oprávnění, aniž byste museli sdílet přístupové klíče vašeho účtu.

Následující kroky ukazují, jak vytvořit SAS pro kontejner objektů BLOB:

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště obsahující kontejner objektů blob, pro který chcete získat SAS.
3. Rozbalte **kontejnery objektů BLOB**v účtu úložiště.
4. Klikněte pravým tlačítkem na požadovaný kontejner objektů BLOB a v místní nabídce vyberte **získat sdílený přístupový podpis**.

   ![Místní nabídka získat SAS][8]
5. V dialogovém okně **Sdílený přístupový podpis** zadejte zásadu, počáteční datum a datum vypršení platnosti, časové pásmo a požadované úrovně přístupu k prostředku.

   ![Získat možnosti SAS][9]
6. Jakmile budete hotovi se zadáváním možností sdíleného přístupového podpisu, vyberte **Vytvořit**.
7. Zobrazí se druhý dialog **sdíleného přístupového podpisu** se seznamem kontejneru objektů BLOB spolu s adresou URL a řetězci QueryString, které můžete použít pro přístup k prostředku úložiště.
   Vyberte **Kopírovat** vedle adresy URL, kterou chcete zkopírovat do schránky.

   ![Kopírovat adresy URL SAS][10]
8. Až budete hotovi, vyberte **Zavřít**.

## <a name="manage-access-policies-for-a-blob-container"></a>Správa zásad přístupu pro kontejner objektů BLOB

Následující postup ukazuje, jak spravovat (Přidat a odebrat) zásady přístupu pro kontejner objektů BLOB:

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště obsahující kontejner objektů blob, u kterého chcete spravovat zásady přístupu.
3. Rozbalte **kontejnery objektů BLOB**v účtu úložiště.
4. Vyberte požadovaný kontejner objektů BLOB a v místní nabídce vyberte **Spravovat zásady přístupu**.

   ![Místní nabídka Spravovat zásady přístupu][11]
5. V dialogovém okně **zásady přístupu** se zobrazí seznam všech zásad přístupu, které jsou už vytvořené pro vybraný kontejner objektů BLOB.

   ![Možnosti zásad přístupu][12]
6. V závislosti na úloze správy zásad přístupu postupujte podle těchto kroků:

   * **Přidání nové zásady přístupu:** Vyberte **Přidat**. Po vygenerování se nová zásada přístupu (s výchozím nastavením) zobrazí v dialogovém okně **Zásady přístupu**.
   * **Upravte zásady přístupu** – proveďte požadované úpravy a vyberte **Uložit**.
   * **Odebrání zásady přístupu:** Vyberte **Odebrat** vedle zásady přístupu, kterou chcete odebrat.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Nastavení úrovně veřejného přístupu pro kontejner objektů BLOB

Ve výchozím nastavení je každý kontejner objektů BLOB nastavený na "žádný veřejný přístup".

Následující kroky ukazují, jak zadat úroveň veřejného přístupu pro kontejner objektů BLOB.

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště obsahující kontejner objektů blob, u kterého chcete spravovat zásady přístupu.
3. Rozbalte **kontejnery objektů BLOB**v účtu úložiště.
4. Vyberte požadovaný kontejner objektů BLOB a v místní nabídce vyberte **nastavit úroveň veřejného přístupu**.

   ![Nastavit místní nabídku úrovně veřejného přístupu][13]
5. V dialogovém okně **nastavit úroveň veřejného přístupu kontejneru** zadejte požadovanou úroveň přístupu.

   ![Nastavit možnosti úrovně veřejného přístupu][14]
6. Vyberte **Použít**.

## <a name="managing-blobs-in-a-blob-container"></a>Správa objektů BLOB v kontejneru objektů BLOB

Po vytvoření kontejneru objektů blob můžete do tohoto kontejneru objektů BLOB nahrát objekt blob, stáhnout objekt blob do místního počítače, otevřít objekt BLOB v místním počítači a spoustu dalšího.

Následující postup ukazuje, jak spravovat objekty BLOB (a složky) v rámci kontejneru objektů BLOB.

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště obsahující kontejner objektů blob, který chcete spravovat.
3. Rozbalte **kontejnery objektů BLOB**v účtu úložiště.
4. Dvakrát klikněte na kontejner objektů blob, který chcete zobrazit.
5. V hlavním podokně se zobrazí obsah kontejneru objektů BLOB.

   ![Zobrazit kontejner objektů BLOB][3]
6. V hlavním podokně se zobrazí obsah kontejneru objektů BLOB.
7. V závislosti na úloze, kterou chcete provést, postupujte podle těchto kroků:

   * **Nahrání souborů do kontejneru objektů BLOB**

     1. Na panelu nástrojů v hlavním podokně vyberte **Nahrát** a pak v rozevírací nabídce vyberte **Nahrát soubory**.

        ![Nabídka Nahrát soubory][15]
     2. V dialogovém okně **Nahrát soubory** vyberte tlačítko se třemi tečkami (**...**) na pravé straně textového pole **Soubory** a vyberte soubory, které chcete nahrát.

        ![Možnosti nahrání souborů][16]
     3. Zadejte typ **typu objektu BLOB**. Další informace najdete v tématu [vytvoření kontejneru](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) .
     4. Volitelně můžete zadat cílovou složku, do které budou odesílány vybrané soubory. Pokud cílová složka neexistuje, vytvoří se.
     5. Vyberte **Nahrát**.
   * **Nahrání složky do kontejneru objektů BLOB**

     1. Na panelu nástrojů v hlavním podokně vyberte **Nahrát** a pak v rozevírací nabídce vyberte **Nahrát složku**.

        ![Nabídka Nahrát složku][17]
     2. V dialogovém okně **Nahrát složku** vyberte tlačítko se třemi tečkami (**...**) na pravé straně textového pole **Složka** a vyberte složku, jejíž obsah chcete nahrát.

        ![Možnosti nahrání složky][18]
     3. Zadejte typ **typu objektu BLOB**. Další informace najdete v tématu [vytvoření kontejneru](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) .
     4. Volitelně můžete zadat cílovou složku, do které se obsah vybrané složky nahraje. Pokud cílová složka neexistuje, vytvoří se.
     5. Vyberte **Nahrát**.
   * **Stažení objektu blob do místního počítače**

     1. Vyberte objekt blob, který chcete stáhnout.
     2. Na panelu nástrojů v hlavním podokně vyberte **Stáhnout**.
     3. V dialogovém okně určete, kam se má **Uložit stažený objekt BLOB** , zadejte umístění, kam chcete objekt BLOB stáhnout, a název, který chcete dát tomuto objektu.  
     4. Vyberte **Uložit**.
   * **Otevření objektu BLOB v místním počítači**

     1. Vyberte objekt blob, který chcete otevřít.
     2. Na panelu nástrojů v hlavním podokně vyberte **Otevřít**.
     3. Objekt BLOB se stáhne a otevře pomocí aplikace přidružené k základnímu typu souboru objektu BLOB.
   * **Zkopírování objektu blob do schránky**

     1. Vyberte objekt blob, který chcete zkopírovat.
     2. Na panelu nástrojů v hlavním podokně vyberte **Kopírovat**.
     3. V levém podokně přejděte do jiného kontejneru objektů BLOB a dvojím kliknutím ho zobrazte v hlavním podokně.
     4. Na panelu nástrojů v hlavním podokně vyberte **Vložit** a vytvořte kopii objektu BLOB.
   * **Odstranění objektu blob**

     1. Vyberte objekt blob, který chcete odstranit.
     2. Na panelu nástrojů v hlavním podokně vyberte **Odstranit**.
     3. V potvrzovacím dialogovém okně klikněte na **Ano**.

## <a name="next-steps"></a>Další kroky

* Podívejte se na [nejnovější poznámky k verzi a videa pro Průzkumníka služby Storage](https://www.storageexplorer.com).
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

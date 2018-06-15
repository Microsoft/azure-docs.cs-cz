---
title: Správa prostředků Azure Blob Storage pomocí Storage Exploreru | Microsoft Docs
description: Správa objektů Blob v Azure kontejnerům a objektům BLOB pomocí Průzkumníka úložiště
services: storage
documentationcenter: na
author: cawa
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
ms.openlocfilehash: 2207bac1ef7cb6393d7dc173f96ddda719bcdfdf
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31528524"
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer"></a>Správa prostředků Azure Blob Storage pomocí Storage Exploreru
## <a name="overview"></a>Přehled
[Azure Blob Storage](storage/blobs/storage-dotnet-how-to-use-blobs.md) je služba pro ukládání velkého objemu nestrukturovaných dat, jako je například textu nebo binárních dat, která jsou přístupná odkudkoli na světě prostřednictvím protokolu HTTP nebo HTTPS.
Službu Blob Storage můžete používat ke zveřejňování dat pro celý svět, nebo k soukromému ukládání dat aplikací. V tomto článku se dozvíte použití Průzkumníka úložiště pro práci s kontejnery objektů blob a objekty BLOB.

## <a name="prerequisites"></a>Požadavky
K dokončení kroků v tomto článku budete potřebovat následující:

* [Stažení a instalace Průzkumníka služby Storage](http://www.storageexplorer.com)
* [Připojení k účtu úložiště nebo službě Azure](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Vytvoření kontejneru objektů blob
Všechny objekty BLOB se musí nacházet v kontejneru objektů blob, který je jednoduše logické seskupení objektů BLOB. Účet může obsahovat neomezený počet kontejnerů a každý kontejner můžete pojmout neomezený počet objektů BLOB.

Následující kroky ukazují, jak vytvořit kontejner objektů blob v rámci Storage Explorer.

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště, ve kterém chcete vytvořit kontejner objektů blob.
3. Klikněte pravým tlačítkem na **kontejnery objektů Blob**a v místní nabídce - vyberte **vytvořit kontejner objektů Blob**.

   ![Vytvoření objektu blob kontejnery kontextové nabídky][0]
4. V textovém poli se zobrazí níže **kontejnery objektů Blob** složky. Zadejte název kontejneru objektů blob. Najdete v článku [vytvoření kontejneru a nastavte oprávnění](storage/blobs/storage-quickstart-blobs-dotnet.md#create-the-container-and-set-permissions) informace o pravidlech a omezení pro pojmenovávání kontejnerů objektů blob.

   ![Vytvoření textového pole kontejnery objektů Blob][1]
5. Stiskněte klávesu **Enter** po dokončení vytvoření kontejneru objektů blob nebo **Esc** zrušit. Po úspěšném vytvoření kontejneru objektů blob se zobrazí v části **kontejnery objektů Blob** složku pro vybraný účet úložiště.

   ![Vytvořit kontejner objektů BLOB][2]

## <a name="view-a-blob-containers-contents"></a>Zobrazit obsah kontejner objektů blob
Kontejnery objektů BLOB obsahovat objekty BLOB a složky (které mohou obsahovat také objekty BLOB).

Následující kroky ukazují, jak zobrazit obsah kontejneru objektů blob v rámci Storage Explorer:

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte kontejner objektů blob obsahující účet úložiště, že které chcete zobrazit.
3. Rozbalte účet úložiště **kontejnery objektů Blob**.
4. Klikněte pravým tlačítkem na kontejner objektů blob, které chcete zobrazit a v místní nabídce - vyberte **otevřete Editor kontejner objektů Blob**.
   Dvakrát klikněte na kontejner objektů blob, který si přejete zobrazit.

   ![Otevřete blob kontejneru editor kontextové nabídky][19]
5. V hlavním podokně se zobrazí obsah kontejneru objektů blob.

   ![Editor kontejner objektů BLOB][3]

## <a name="delete-a-blob-container"></a>Odstranit kontejner objektů blob
Kontejnery objektů BLOB můžete snadno vytvořit a odstraněn podle potřeby. (Chcete-li zjistit, jak odstranit jednotlivé objekty BLOB, informace naleznete v sekci [Správa objektů BLOB v kontejneru objektů blob](#managing-blobs-in-a-blob-container).)

Následující kroky ukazují, jak odstranit kontejner objektů blob v rámci Storage Explorer:

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte kontejner objektů blob obsahující účet úložiště, že které chcete zobrazit.
3. Rozbalte účet úložiště **kontejnery objektů Blob**.
4. Klikněte pravým tlačítkem na kontejner objektů blob, které chcete odstranit a v místní nabídce - vyberte **odstranit**.
   Můžete také stisknout **odstranit** odstranit kontejner aktuálně vybraných objektů blob.

   ![Odstranit objekt blob kontejneru kontextové nabídky][4]
5. V potvrzovacím dialogovém okně klikněte na **Ano**.

   ![Odstranit objekt blob kontejneru potvrzení][5]

## <a name="copy-a-blob-container"></a>Zkopírujte kontejner objektů blob
Storage Explorer umožňuje zkopírovat kontejner objektů blob do schránky a vložte tento kontejner objektů blob do jiný účet úložiště. (Chcete-li zjistit, jak zkopírovat jednotlivé objekty BLOB, informace naleznete v sekci [Správa objektů BLOB v kontejneru objektů blob](#managing-blobs-in-a-blob-container).)

Následující kroky ukazují, jak zkopírovat kontejner objektů blob z jeden účet úložiště do druhého.

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte kontejner objektů blob obsahující účet úložiště, že které chcete kopírovat.
3. Rozbalte účet úložiště **kontejnery objektů Blob**.
4. Klikněte pravým tlačítkem na kontejner objektů blob, které chcete kopírovat a v místní nabídce - vyberte **kontejner objektů Blob kopie**.

   ![Kopírovat objekt blob kontejneru kontextové nabídky][6]
5. Klikněte pravým tlačítkem na účet úložiště požadované "target", do kterého chcete vložit kontejneru objektů blob a v místní nabídce - vyberte **vložit kontejner objektů Blob**.

   ![Vložení objektu blob kontejneru kontextové nabídky][7]

## <a name="get-the-sas-for-a-blob-container"></a>Získání sdíleného přístupového podpisu (SAS) pro kontejner objektů blob
[Sdílený přístupový podpis (SAS)](storage/common/storage-dotnet-shared-access-signature-part-1.md) poskytuje delegovaný přístup k prostředkům ve vašem účtu úložiště.
To znamená, že můžete klientovi udělit omezená oprávnění k objektům ve vašem účtu úložiště po stanovené časové období a s konkrétní sadou oprávnění, aniž byste museli sdílet přístupové klíče vašeho účtu.

Následující kroky ukazují, jak vytvořit SAS pro kontejner objektů blob:

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte obsahující kontejner objektů blob, pro kterou chcete získat SAS účtu úložiště.
3. Rozbalte účet úložiště **kontejnery objektů Blob**.
4. Klikněte pravým tlačítkem na kontejner objektů blob požadované a v místní nabídce - vyberte **sdíleného přístupového podpisu**.

   ![Získat SAS kontextové nabídky][8]
5. V dialogovém okně **Sdílený přístupový podpis** zadejte zásadu, počáteční datum a datum vypršení platnosti, časové pásmo a požadované úrovně přístupu k prostředku.

   ![Získat možnosti SAS][9]
6. Jakmile budete hotovi se zadáváním možností sdíleného přístupového podpisu, vyberte **Vytvořit**.
7. Druhý **sdíleného přístupového podpisu** dialogové okno se zobrazí, pak se seznamem kontejneru objektů blob společně s adresu URL a QueryStrings můžete použít pro přístup k prostředku úložiště.
   Vyberte **Kopírovat** vedle adresy URL, kterou chcete zkopírovat do schránky.

   ![Zkopírování adresy URL SAS][10]
8. Až budete hotovi, vyberte **Zavřít**.

## <a name="manage-access-policies-for-a-blob-container"></a>Správa zásad přístupu pro kontejner objektů blob
Následující kroky ukazují, jak spravovat (přidání a odebrání) získat přístup k zásadám pro kontejner objektů blob:

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště obsahující kontejner objektů blob, jejichž zásady přístupu, které chcete spravovat.
3. Rozbalte účet úložiště **kontejnery objektů Blob**.
4. Vyberte kontejner objektů blob požadované a v místní nabídce - vyberte **spravovat zásady přístupu**.

   ![Místní nabídka Spravovat zásady přístupu][11]
5. **Zásady přístupu** dialogové okno se zobrazí seznam všechny zásady přístupu, které jsou vytvořeny již pro vybraný objekt blob kontejneru.

   ![Možnosti zásad přístupu][12]        
6. V závislosti na úloze správy zásad přístupu postupujte podle těchto kroků:

   * **Přidání nové zásady přístupu:** Vyberte **Přidat**. Po vygenerování se nová zásada přístupu (s výchozím nastavením) zobrazí v dialogovém okně **Zásady přístupu**.
   * **Upravit zásady přístupu** – proveďte veškeré požadované úpravy a vyberte **Uložit**.
   * **Odebrání zásady přístupu:** Vyberte **Odebrat** vedle zásady přístupu, kterou chcete odebrat.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Nastavení úrovně veřejný přístup pro kontejner objektů blob
Ve výchozím nastavení každý kontejner objektů blob nastavena na "Žádný veřejný přístup".

Následující kroky ukazují, jak k určení úrovně veřejný přístup pro kontejner objektů blob.

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště obsahující kontejner objektů blob, jejichž zásady přístupu, které chcete spravovat.
3. Rozbalte účet úložiště **kontejnery objektů Blob**.
4. Vyberte kontejner objektů blob požadované a v místní nabídce - vyberte **nastavit úroveň veřejný přístup**.

   ![Nastavení úrovně kontextovou nabídku veřejného přístupu][13]
5. V **nastavit úroveň veřejný přístup kontejneru** dialogové okno, zadejte úroveň požadované přístupu.

   ![Nastavení možností úrovně veřejného přístupu][14]
6. Vyberte **Použít**.

## <a name="managing-blobs-in-a-blob-container"></a>Správa objektů BLOB v kontejneru objektů blob
Po vytvoření kontejneru objektů blob, můžete nahrát objekt blob kontejneru objektů blob, stáhne objekt blob do místního počítače, otevřete objekt blob na místním počítači a mnoho dalšího.

Následující kroky ukazují, jak spravovat objekty BLOB (a složky) v kontejneru objektů blob.

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte kontejner objektů blob obsahující účet úložiště, že které chcete spravovat.
3. Rozbalte účet úložiště **kontejnery objektů Blob**.
4. Dvakrát klikněte na kontejner objektů blob, který si přejete zobrazit.
5. V hlavním podokně se zobrazí obsah kontejneru objektů blob.

   ![Kontejner objektů blob zobrazení][3]
6. V hlavním podokně se zobrazí obsah kontejneru objektů blob.
7. V závislosti na úloze, kterou chcete provést, postupujte podle těchto kroků:

   * **Nahrání souborů do kontejneru objektů blob**

     1. Na panelu nástrojů v hlavním podokně vyberte **Nahrát** a pak v rozevírací nabídce vyberte **Nahrát soubory**.

        ![Nabídka Nahrát soubory][15]
     2. V dialogovém okně **Nahrát soubory** vyberte tlačítko se třemi tečkami (**...**) na pravé straně textového pole **Soubory** a vyberte soubory, které chcete nahrát.

        ![Nahrávání souborů možnosti][16]
     3. Zadejte typ **Blob typu**. V tématu [vytvoření kontejneru a nastavte oprávnění](storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-the-container) Další informace.
     4. Volitelně zadejte cílovou složku, do kterého se nahraje vybrané soubory. Pokud cílová složka neexistuje, vytvoří se.
     5. Vyberte **Nahrát**.
   * **Nahrajte do složky na kontejner objektů blob**

     1. Na panelu nástrojů v hlavním podokně vyberte **Nahrát** a pak v rozevírací nabídce vyberte **Nahrát složku**.

        ![Nabídka Nahrát složku][17]
     2. V dialogovém okně **Nahrát složku** vyberte tlačítko se třemi tečkami (**...**) na pravé straně textového pole **Složka** a vyberte složku, jejíž obsah chcete nahrát.

        ![Nahrát Možnosti složky][18]
     3. Zadejte typ **Blob typu**. V tématu [vytvoření kontejneru a nastavte oprávnění](storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-the-container) Další informace.
     4. Volitelně můžete zadat cílovou složku, do které se obsah vybrané složky nahraje. Pokud cílová složka neexistuje, vytvoří se.
     5. Vyberte **Nahrát**.
   * **Stáhnout objekt blob do místního počítače**

     1. Vyberte objekt blob, který chcete stáhnout.
     2. Na panelu nástrojů v hlavním podokně vyberte **Stáhnout**.
     3. V **určení místa k uložení stažených blob** dialogové okno, zadejte umístění, kam má objekt blob stáhli a název chcete pojmenujte ho.  
     4. Vyberte **Uložit**.
   * **Otevřete objekt blob do místního počítače**

     1. Vyberte objekt blob, který chcete otevřít.
     2. Na panelu nástrojů v hlavním podokně vyberte **Otevřít**.
     3. Objekt blob se stáhne a otevřít pomocí aplikace přidružené k objektu blob základní typ souboru.
   * **Kopírovat objekt blob do schránky.**

     1. Vyberte objekt blob, který chcete kopírovat.
     2. Na panelu nástrojů v hlavním podokně vyberte **Kopírovat**.
     3. V levém podokně přejděte na jiný kontejner objektů blob a dvojím kliknutím zobrazíte v hlavním podokně.
     4. Na panelu nástrojů v hlavním podokně vyberte **vložení** vytvoření kopie objektu blob.
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

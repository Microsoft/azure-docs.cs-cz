---
title: Správa prostředků úložiště objektů blob Azure pomocí Průzkumníka úložiště | Dokumenty společnosti Microsoft
description: Správa kontejnerů objektů blob Azure a objektů BLOB pomocí Průzkumníka úložiště
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
ms.openlocfilehash: 56c20c995a95058b5039b7268c7b7b1426e900fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244391"
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer"></a>Správa prostředků služby Azure Blob Storage pomocí Průzkumníka služby Storage

## <a name="overview"></a>Přehled

[Azure Blob Storage](storage/blobs/storage-dotnet-how-to-use-blobs.md) je služba pro ukládání velkého množství nestrukturovaných dat, jako je text nebo binární data, ke které lze přistupovat z libovolného místa na světě prostřednictvím protokolu HTTP nebo HTTPS.
Službu Blob Storage můžete používat ke zveřejňování dat pro celý svět, nebo k soukromému ukládání dat aplikací. V tomto článku se dozvíte, jak pomocí Průzkumníka úložiště pracovat s kontejnery objektů blob a objekty BLOB.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku budete potřebovat následující:

* [Stažení a instalace Průzkumníka služby Storage](https://www.storageexplorer.com)
* [Připojení k účtu úložiště nebo službě Azure](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Vytvoření kontejneru objektů blob

Všechny objekty BLOB musí být umístěny v kontejneru objektů blob, což je jednoduše logické seskupení objektů BLOB. Účet může obsahovat neomezený počet kontejnerů a každý kontejner může ukládat neomezený počet objektů BLOB.

Následující kroky ilustrují, jak vytvořit kontejner objektů blob v průzkumníku úložiště.

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště, ve kterém chcete vytvořit kontejner objektů blob.
3. Klepněte pravým tlačítkem myši na **položku Kontejnery objektů blob**a v místní nabídce vyberte **příkaz Vytvořit kontejner objektů blob**.

   ![Vytvoření kontextové nabídky kontejnerů objektů blob][0]
4. Pod složkou **Kontejnery objektů blob** se zobrazí textové pole. Zadejte název kontejneru objektů blob. Informace o pravidlech a omezeních pojmenování kontejnerů objektů blob najdete v [tématu Vytvoření kontejneru.](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container)

   ![Vytvořit textové pole Vytvořit kontejnery objektů blob][1]
5. Stisknutím **klávesy Enter** po dokončení vytvořte kontejner objektů blob nebo **esc** zrušit. Po úspěšném vytvoření kontejneru objektů blob se zobrazí ve složce **Kontejnery objektů blob** pro vybraný účet úložiště.

   ![Kontejner objektů blob vytvořen][2]

## <a name="view-a-blob-containers-contents"></a>Zobrazení obsahu kontejneru s objektem blob

Kontejnery objektů blob obsahují objekty BLOB a složky (které mohou také obsahovat objekty BLOB).

Následující kroky ilustrují, jak zobrazit obsah kontejneru objektů blob v Průzkumníku úložiště:

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště obsahující kontejner objektů blob, který chcete zobrazit.
3. Rozbalte objekt **blob kontejnerů**účtu úložiště .
4. Klikněte pravým tlačítkem myši na kontejner objektů blob, který chcete zobrazit, a v místní nabídce vyberte **otevřít editor kontejnerů objektů blob**.
   Můžete také poklepat na kontejner objektů blob, který chcete zobrazit.

   ![Otevřít kontextovou nabídku editoru kontejnerů objektů blob][19]
5. V hlavním podokně se zobrazí obsah kontejneru objektů blob.

   ![Editor kontejnerů objektů blob][3]

## <a name="delete-a-blob-container"></a>Odstranění kontejneru objektů blob

Kontejnery objektů blob lze snadno vytvořit a odstranit podle potřeby. (Chcete-li zjistit, jak odstranit jednotlivé objekty BLOB, naleznete v části [Správa objektů BLOB v kontejneru objektů blob](#managing-blobs-in-a-blob-container).)

Následující kroky ilustrují, jak odstranit kontejner objektů blob v Průzkumníku úložiště:

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště obsahující kontejner objektů blob, který chcete zobrazit.
3. Rozbalte objekt **blob kontejnerů**účtu úložiště .
4. Klepněte pravým tlačítkem myši na kontejner objektů blob, který chcete odstranit, a v místní nabídce vyberte **příkaz Odstranit**.
   Stisknutím **klávesy Delete** můžete odstranit aktuálně vybraný kontejner objektů blob.

   ![Odstranit místní nabídku kontejneru objektů blob][4]
5. V potvrzovacím dialogovém okně klikněte na **Ano**.

   ![Odstranit potvrzení kontejneru objektů blob][5]

## <a name="copy-a-blob-container"></a>Kopírování kontejneru objektů blob

Průzkumník úložiště umožňuje zkopírovat kontejner objektů blob do schránky a potom vložit tento kontejner objektů blob do jiného účtu úložiště. (Chcete-li zjistit, jak kopírovat jednotlivé objekty BLOB, naleznete v části [Správa objektů BLOB v kontejneru objektů blob](#managing-blobs-in-a-blob-container).)

Následující kroky ilustrují, jak zkopírovat kontejner objektů blob z jednoho účtu úložiště do jiného.

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště obsahující kontejner objektů blob, který chcete zkopírovat.
3. Rozbalte objekt **blob kontejnerů**účtu úložiště .
4. Klikněte pravým tlačítkem myši na kontejner objektů blob, který chcete zkopírovat, a v místní nabídce vyberte **kopírovat kontejner objektů blob**.

   ![Kopírovat místní nabídku kontejneru objektů blob][6]
5. Klikněte pravým tlačítkem myši na požadovaný účet úložiště "cíl", do kterého chcete vložit kontejner objektů blob, a v místní nabídce vyberte **vložit kontejner objektů blob**.

   ![Kontextová nabídka kontejneru objektů blob][7]

## <a name="get-the-sas-for-a-blob-container"></a>Získání sdíleného přístupového podpisu (SAS) pro kontejner objektů blob

[Sdílený přístupový podpis (SAS)](storage/common/storage-dotnet-shared-access-signature-part-1.md) poskytuje delegovaný přístup k prostředkům ve vašem účtu úložiště.
To znamená, že můžete klientovi udělit omezená oprávnění k objektům ve vašem účtu úložiště po stanovené časové období a s konkrétní sadou oprávnění, aniž byste museli sdílet přístupové klíče vašeho účtu.

Následující kroky ilustrují, jak vytvořit SAS pro kontejner objektů blob:

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště obsahující kontejner objektů blob, pro který chcete získat SAS.
3. Rozbalte objekt **blob kontejnerů**účtu úložiště .
4. Klepněte pravým tlačítkem myši na požadovaný kontejner objektů blob a v místní nabídce vyberte **příkaz Získat sdílený přístupový podpis**.

   ![Získat kontextovou nabídku SAS][8]
5. V dialogovém okně **Sdílený přístupový podpis** zadejte zásadu, počáteční datum a datum vypršení platnosti, časové pásmo a požadované úrovně přístupu k prostředku.

   ![Získat možnosti SAS][9]
6. Jakmile budete hotovi se zadáváním možností sdíleného přístupového podpisu, vyberte **Vytvořit**.
7. Zobrazí se druhý dialog **Sdílený přístupový podpis,** který obsahuje seznam kontejneru objektů blob spolu s adresou URL a řetězcem QueryStrings, které můžete použít pro přístup k prostředku úložiště.
   Vyberte **Kopírovat** vedle adresy URL, kterou chcete zkopírovat do schránky.

   ![Kopírování adres URL SAS][10]
8. Až budete hotovi, vyberte **Zavřít**.

## <a name="manage-access-policies-for-a-blob-container"></a>Správa přístupových zásad pro kontejner objektů blob

Následující kroky ilustrují, jak spravovat (přidat a odebrat) zásady přístupu pro kontejner objektů blob:

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště obsahující kontejner objektů blob, jehož zásady přístupu chcete spravovat.
3. Rozbalte objekt **blob kontejnerů**účtu úložiště .
4. Vyberte požadovaný kontejner objektů blob a v místní nabídce vyberte **Spravovat zásady přístupu**.

   ![Místní nabídka Spravovat zásady přístupu][11]
5. V dialogovém okně **Zásady přístupu** budou uvedeny všechny zásady přístupu, které již byly vytvořeny pro vybraný kontejner objektů blob.

   ![Možnosti přístupových zásad][12]
6. V závislosti na úloze správy zásad přístupu postupujte podle těchto kroků:

   * **Přidání nové zásady přístupu:** Vyberte **Přidat**. Po vygenerování se nová zásada přístupu (s výchozím nastavením) zobrazí v dialogovém okně **Zásady přístupu**.
   * **Upravit zásady přístupu** – Proveďte požadované úpravy a vyberte **Uložit**.
   * **Odebrání zásady přístupu:** Vyberte **Odebrat** vedle zásady přístupu, kterou chcete odebrat.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Nastavení úrovně veřejného přístupu pro kontejner objektů blob

Ve výchozím nastavení je každý kontejner objektů blob nastavenna na "Žádný veřejný přístup".

Následující kroky ilustrují, jak určit úroveň veřejného přístupu pro kontejner objektů blob.

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště obsahující kontejner objektů blob, jehož zásady přístupu chcete spravovat.
3. Rozbalte objekt **blob kontejnerů**účtu úložiště .
4. Vyberte požadovaný kontejner objektů blob a – v místní nabídce – vyberte **Nastavit úroveň veřejného přístupu**.

   ![Nastavení kontextové nabídky úrovně veřejného přístupu][13]
5. V dialogovém okně **Nastavit úroveň veřejného přístupu kontejneru** určete požadovanou úroveň přístupu.

   ![Nastavení možností úrovně veřejného přístupu][14]
6. Vyberte **Použít**.

## <a name="managing-blobs-in-a-blob-container"></a>Správa objektů BLOB v kontejneru objektů blob

Po vytvoření kontejneru objektů blob můžete nahrát objekt blob do tohoto kontejneru objektů blob, stáhnout objekt blob do místního počítače, otevřít objekt blob v místním počítači a mnoho dalšího.

Následující kroky ilustrují, jak spravovat objekty BLOB (a složky) v kontejneru objektů blob.

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte účet úložiště obsahující kontejner objektů blob, který chcete spravovat.
3. Rozbalte objekt **blob kontejnerů**účtu úložiště .
4. Poklepejte na kontejner objektů blob, který chcete zobrazit.
5. V hlavním podokně se zobrazí obsah kontejneru objektů blob.

   ![Zobrazení kontejneru objektů blob][3]
6. V hlavním podokně se zobrazí obsah kontejneru objektů blob.
7. V závislosti na úloze, kterou chcete provést, postupujte podle těchto kroků:

   * **Nahrání souborů do kontejneru objektů blob**

     1. Na panelu nástrojů v hlavním podokně vyberte **Nahrát** a pak v rozevírací nabídce vyberte **Nahrát soubory**.

        ![Nabídka Nahrát soubory][15]
     2. V dialogovém okně **Nahrát soubory** vyberte tlačítko se třemi tečkami (**...**) na pravé straně textového pole **Soubory** a vyberte soubory, které chcete nahrát.

        ![Možnosti nahrávání souborů][16]
     3. Zadejte typ **typu objektu blob**. Další informace najdete [v tématu Vytvoření kontejneru.](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container)
     4. Volitelně můžete zadat cílovou složku, do které budou vybrané soubory nahrány. Pokud cílová složka neexistuje, vytvoří se.
     5. Vyberte **Nahrát**.
   * **Nahrání složky do kontejneru objektů blob**

     1. Na panelu nástrojů v hlavním podokně vyberte **Nahrát** a pak v rozevírací nabídce vyberte **Nahrát složku**.

        ![Nabídka Nahrát složku][17]
     2. V dialogovém okně **Nahrát složku** vyberte tlačítko se třemi tečkami (**...**) na pravé straně textového pole **Složka** a vyberte složku, jejíž obsah chcete nahrát.

        ![Možnosti odeslání složky][18]
     3. Zadejte typ **typu objektu blob**. Další informace najdete [v tématu Vytvoření kontejneru.](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container)
     4. Volitelně můžete zadat cílovou složku, do které se obsah vybrané složky nahraje. Pokud cílová složka neexistuje, vytvoří se.
     5. Vyberte **Nahrát**.
   * **Stažení objektu blob do místního počítače**

     1. Vyberte objekt blob, který chcete stáhnout.
     2. Na panelu nástrojů v hlavním podokně vyberte **Stáhnout**.
     3. V **dialogovém okně Určete, kam chcete uložit stažený objekt blob,** určete umístění, kam chcete objekt blob stáhnout, a název, který chcete zadat.  
     4. Vyberte **Uložit**.
   * **Otevření objektu blob v místním počítači**

     1. Vyberte objekt blob, který chcete otevřít.
     2. Na panelu nástrojů v hlavním podokně vyberte **Otevřít**.
     3. Objekt blob se stáhne a otevře pomocí aplikace přidružené k základnímu typu souboru objektu blob.
   * **Kopírování objektu blob do schránky**

     1. Vyberte objekt blob, který chcete zkopírovat.
     2. Na panelu nástrojů v hlavním podokně vyberte **Kopírovat**.
     3. V levém podokně přejděte do jiného kontejneru objektů blob a poklikáním jej zobrazte v hlavním podokně.
     4. Na panelu nástrojů hlavního podokna vyberte **Vložit,** chcete-li vytvořit kopii objektu blob.
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

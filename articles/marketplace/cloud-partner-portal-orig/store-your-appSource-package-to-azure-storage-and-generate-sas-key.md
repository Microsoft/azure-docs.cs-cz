---
title: Uložení balíčku AppSource do úložiště Azure a vygenerování adresy URL pomocí klíče SAS
description: Podrobně popisuje kroky potřebné k nahrání a zabezpečení balíčku AppSource.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pabutler
ms.openlocfilehash: 5f1a09244697a6771ad1b499f3d7c36eb7297067
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827653"
---
<a name="store-your-appsource-package-to-azure-storage-and-generate-a-url-with-sas-key"></a>Uložení balíčku AppSource do úložiště Azure a vygenerování adresy URL pomocí klíče SAS
=============================================================================

Aby bylo možné zachovat zabezpečení vašich souborů, všichni partneři musí uložit svůj soubor balíčku AppSource do účtu služby Azure Blob Storage a pomocí klíče SAS ho sdílet. Načteme soubor balíčku z vašeho umístění úložiště Azure pro certifikaci a použijeme ho pro AppSource testy.

Pomocí následujících kroků nahrajte balíček do úložiště objektů BLOB:

1. Přejít na <https://azure.microsoft.com> a vytvořit bezplatný zkušební nebo Fakturovaný účet.

2. Přihlaste se k [portálu Azure](https://portal.azure.com/).

3. Vytvořte nový účet úložiště tak, že kliknete na **+ Nový** a přejdete na účet **data + úložiště** .

   ![Okno data + úložiště na portálu Microsoft Azure](media/CRMScreenShot7.png)

4. Zadejte **název** a název **skupiny prostředků** a klikněte na tlačítko **vytvořit** .

   ![Vytvoření účtu úložiště na portálu Microsoft Azure](media/CRMScreenShot8.png)

5. Přejděte do nově vytvořené skupiny prostředků a vytvořte nový kontejner objektů BLOB.

   ![Nahrání balíčku jako objektu BLOB pomocí Microsoft Azureového portálu](media/CRMScreenShot9.png)

6. Pokud jste to ještě neudělali, Stáhněte a nainstalujte Microsoft [Průzkumník služby Azure Storage](https://storageexplorer.com/).

7. Otevřete Průzkumník služby Storage a pomocí ikony se připojte k účtu služby Azure Storage.

8. Přejděte do vytvořeného kontejneru objektů BLOB a kliknutím na **nahrát** přidejte soubor zip balíčku.

   ![Nahrání balíčku pomocí Microsoft Průzkumník služby Storage](media/CRMScreenShot10.png)

9. Klikněte pravým tlačítkem na soubor a vyberte **získat sdílený přístupový podpis**.

   ![Získání sdíleného přístupového podpisu pro soubor Azure](media/CRMScreenShot11.png)

10. Upravte **čas vypršení platnosti** , aby bylo přidružení SA aktivní po dobu měsíce, a pak klikněte na **vytvořit**.

    ![Úprava data vypršení platnosti SAS pro soubor Azure](media/CRMScreenShot12.png)

11. Zkopírujte pole Adresa URL a uložte ho pro pozdější verzi. Tuto adresu URL budete muset zadat při vytváření přidružené nabídky. 

    ![Kopírování adresy URL SAS souboru Azure](media/CRMScreenShot13.png)


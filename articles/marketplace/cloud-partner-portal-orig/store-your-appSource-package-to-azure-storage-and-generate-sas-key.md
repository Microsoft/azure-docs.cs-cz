---
title: Uložení balíčku AppSource do úložiště Azure a generování adresy URL pomocí klíče SAS
description: Podrobnosti o krocích potřebných k nahrání a zabezpečení balíčku AppSource.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: dsindona
ms.openlocfilehash: 57bc370fd160b8b3d6d7941ea28cd460c99f3d96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285363"
---
<a name="store-your-appsource-package-to-azure-storage-and-generate-a-url-with-sas-key"></a>Uložení balíčku AppSource do úložiště Azure a generování adresy URL pomocí klíče SAS
=============================================================================

Chcete-li zachovat zabezpečení vašich souborů, všichni partneři musí uložit svůj soubor balíčku AppSource v účtu úložiště objektů blob Azure a používat klíč SAS ke sdílení. Soubor balíčku načteme z vašeho úložiště Azure pro certifikaci a použijeme ho pro zkušební verze AppSource.

Pomocí následujících kroků nahrajte balíček do úložiště objektů blob:

1. Přejděte <https://azure.microsoft.com> na a vytvořte bezplatnou zkušební verzi nebo fakturovaný účet.

2. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

3. Vytvořte si nový účet úložiště kliknutím na **+ Nový** a přejdete na účet Data **+ Úložiště.**

   ![Okno Data + Storage na portálu Microsoft Azure Portal](media/CRMScreenShot7.png)

4. Zadejte název **skupiny názvů** **a prostředků** a klikněte na **tlačítko Vytvořit.**

   ![Vytvoření účtu úložiště na portálu Microsoft Azure Portal](media/CRMScreenShot8.png)

5. Přejděte do nově vytvořené skupiny prostředků a vytvořte nový kontejner objektů blob.

   ![Nahrání balíčku jako objektu blob pomocí portálu Microsoft Azure Portal](media/CRMScreenShot9.png)

6. Pokud jste tak ještě neučinili, stáhněte a nainstalujte [Průzkumníka úložiště](https://storageexplorer.com/)Microsoft Azure .

7. Otevřete Průzkumníka úložiště a pomocí ikony se připojte ke svému účtu úložiště Azure.

8. Přejděte do kontejneru objektů blob, který jste vytvořili, a kliknutím na **Nahrát** přidejte soubor ZIP balíčku.

   ![Nahrávání balíčku pomocí Průzkumníka microsoft storage exploreru](media/CRMScreenShot10.png)

9. Klikněte pravým tlačítkem myši na soubor a vyberte **získat sdílený přístupový podpis**.

   ![Získání sdíleného přístupového podpisu souboru Azure](media/CRMScreenShot11.png)

10. Upravte **dobu vypršení platnosti** tak, aby byla SAS aktivní po dobu jednoho měsíce, a pak klepněte na tlačítko **Vytvořit**.

    ![Úprava data vypršení platnosti souboru Azure s as](media/CRMScreenShot12.png)

11. Zkopírujte pole URL a uložte ho na později. Tuto adresu URL budete muset zadat při vytváření přidružené nabídky. 

    ![Kopírování adresy URL SAS souboru Azure](media/CRMScreenShot13.png)


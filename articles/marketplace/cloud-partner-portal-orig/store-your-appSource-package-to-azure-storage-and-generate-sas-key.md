---
title: Store AppSource balíčku do služby Azure storage a generovat adresu URL s klíčem SAS | Dokumentace Microsoftu
description: Podrobně popisuje kroky potřebné k nahrání a zabezpečení balíčku AppSource.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 335590cc616035020f66cfc5208e21d4c5128fe6
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809404"
---
<a name="store-your-appsource-package-to-azure-storage-and-generate-a-url-with-sas-key"></a>Store AppSource balíčku do služby Azure storage a generovat adresu URL s klíčem SAS
=============================================================================

Zachování zabezpečení souborů, musí všichni partneři uložit soubor balíčku jejich AppSource v účtu úložiště objektů blob v Azure a používat klíč SAS ke sdílení. Z umístění služby Azure storage k certifikaci a pro účely hodnocení AppSource se nám se načíst soubor balíčku.

Pro nahrání balíčku do úložiště objektů blob použijte následující kroky:

1.  Přejděte na <http://azure.microsoft.com> a vytvořte si bezplatný účet zkušební verze nebo se fakturuje.

2.  Přihlaste se k [Portálu Azure](http://portal.azure.com/).

3.  Vytvořit nový účet úložiště kliknutím na **+ nová** a **Data + úložiště** účtu.

  ![Data + úložiště okna na portálu Microsoft Azure](media/CRMScreenShot7.png)

4. Zadejte **název** a **skupiny prostředků** pojmenujte a klikněte na tlačítko **vytvořit** tlačítko.

  ![Vytvoření účtu úložiště na portálu Microsoft Azure](media/CRMScreenShot8.png)

5. Přejděte do vaší nově vytvořené skupiny prostředků a vytvořit nový kontejner objektů blob.

  ![Nahrání balíčku jako objekt blob pomocí portálu Microsoft Azure](media/CRMScreenShot9.png)

6.  Pokud jste tak již neučinili, stáhněte a nainstalujte Microsoft [Průzkumníka služby Azure Storage](http://storageexplorer.com/).

7.  Otevřete Průzkumníka služby Storage a použijte ikonu pro připojení k účtu služby Azure storage.

8.  Přejděte do kontejneru objektů blob, který jste vytvořili a klikněte na tlačítko **nahrát** se přidat váš soubor zip balíčku.

  ![Nahrání balíčku pomocí Průzkumníka úložišť Microsoft](media/CRMScreenShot10.png)

9.  Klikněte pravým tlačítkem na soubor a vyberte **získat sdílený přístupový podpis**.

  ![Získat sdílený přístupový podpis Azure file](media/CRMScreenShot11.png)

10.  Upravit **čas vypršení platnosti** aby SAS aktivní po dobu jednoho měsíce, pak klikněte na tlačítko **vytvořit**.

  ![Upravit datum vypršení platnosti SAS Azure file](media/CRMScreenShot12.png)

11.  Do pole Adresa URL zkopírovat a uložit pro pozdější použití. Je potřeba při vytváření nabídky přidružené zadejte tuto adresu URL. 

  ![Zkopírujte adresu URL SAS souboru Azure](media/CRMScreenShot13.png)


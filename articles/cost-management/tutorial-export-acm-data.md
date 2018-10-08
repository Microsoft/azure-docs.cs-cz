---
title: 'Kurz: Vytvoření a správa exportovaných dat ze služby Azure Cost Management | Microsoft Docs'
description: Tento článek popisuje, jak vytvořit a spravovat exportovaná data služby Azure Cost Management tak, aby se dala používat v externích systémech.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 0765e79b90eed49742f5eead33063907eb1db1f4
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47030842"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Kurz: Vytvoření a správa exportovaných dat

Pokud jste si prošli kurz Analýza nákladů, pak jste obeznámeni s ručním stahováním dat ze služby Cost Management. Můžete však vytvořit opakující se úlohu, která každý den automaticky exportuje data služby Cost Management do úložiště Azure. Exportovaná data jsou ve formátu CSV a obsahují všechny informace, které služba Cost Management shromažďuje. Exportovaná data v úložišti Azure pak můžete používat s externími systémy a kombinovat je se svými vlastními daty. Exportovaná data můžete používat v externím systému, jako je třeba řídicí panel nebo jiný finanční systém.

Příklady v tomto kurzu vás provedou exportem dat služby Cost Management a následným ověřením, že se daná data úspěšně exportovala.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit každodenní export
> * Ověřit shromáždění dat

## <a name="prerequisites"></a>Požadavky

Export dat je dostupný pro všechny zákazníky se [smlouvou Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/). Při exportu dat uživatelů a skupin se u předplatných podporují následující oprávnění Azure:

- Vlastník – může vytvářet, upravovat a odstraňovat naplánované exporty u předplatných.
- Přispěvatel – může vytvářet, upravovat a odstraňovat svoje vlastní naplánované exporty. Může měnit názvy naplánovaných exportů, které vytvořili jiní uživatelé.
- Čtenář – může naplánovat exporty, ke kterým má oprávnění.

Pro účty služby Azure Storage:
- Ke změně nakonfigurovaného účtu úložiště se vyžaduje oprávnění k zápisu, a to bez ohledu na oprávnění k exportu.
- Příslušný účet úložiště Azure musí být nakonfigurovaný jako úložiště souborů nebo objektů blob.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Vytvoření každodenního exportu

Správa nákladů a fakturace &gt; vyberte předplatné nebo skupinu prostředků v předplatném &gt; Exportovat &gt; **Přidat**.

Zadejte název pro daný export a určete předplatné, účet úložiště Azure, kontejner a adresář úložiště souborů nebo kontejner objektů blob, pak klikněte na **Vytvořit**.

![Nový export](./media/tutorial-export-acm-data/new-export01.png)

Nový export se zobrazí v seznamu exportů. Ve výchozím nastavení jsou nové exporty povolené a spouští se každý den. Pokud chcete zakázat nebo odstranit naplánovaný export, klikněte na libovolnou položku v seznamu a pak klikněte na **Zakázat** nebo **Odstranit**.

Na začátku může trvat jednu až dvě hodiny, než se export spustí. Může však trvat až čtyři hodiny, než se v exportovaných souborech zobrazí data.

## <a name="verify-that-data-is-collected"></a>Ověření shromáždění dat

Pomocí Průzkumníka služby Azure Storage můžete snadno ověřit, že se data služby Cost Management shromažďují, a zobrazit exportovaný soubor CSV.

V seznamu exportu klikněte na název účtu úložiště. Na stránce účtu úložiště klikněte na Otevřít v Průzkumníkovi. Pokud se zobrazí potvrzovací okno, klikněte na **Ano** a otevřete soubor v Průzkumníkovi služby Azure Storage.

![Stránka účtu úložiště](./media/tutorial-export-acm-data/storage-account-page.png)

V Průzkumníkovi služby Storage přejděte ke kontejneru, který chcete otevřít, a vyberte složku odpovídající aktuálnímu měsíci. Zobrazí se seznam souborů CSV. Vyberte jeden soubor a pak klikněte na **Otevřít**.

![Storage Explorer](./media/tutorial-export-acm-data/storage-explorer.png)

Soubor se otevře v programu nebo aplikaci, která je nastavená k otevírání souborů s příponou CSV. Tady je příklad v Excelu.

![Příklad exportu dat](./media/tutorial-export-acm-data/example-export-data.png)

## <a name="access-exported-data-from-other-systems"></a>Přístup k exportovaným datům z jiných systémů

Jedním z důvodů pro export dat služby Cost Management je umožnění přístupu k těmto datům z externích systémů. Možná používáte systém řídicích panelů nebo jiný finanční systém. Takové systémy se dost liší, takže ukázka s příkladem by nemusela být užitečná.  S přístupem k datům z vašich aplikací však můžete začít podle tématu [Úvod do Azure Storage](../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit každodenní export
> * Ověřit shromáždění dat

Přejděte k dalšímu kurzu, který se zabývá optimalizací a zvyšováním efektivity pomocí identifikace nečinných a nedostatečně využitých prostředků.

> [!div class="nextstepaction"]
> [Kontrola optimalizačních doporučení a reakce na ně](tutorial-acm-opt-recommendations.md)

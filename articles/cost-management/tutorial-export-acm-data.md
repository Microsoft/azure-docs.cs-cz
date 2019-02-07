---
title: 'Kurz: Vytvoření a správa exportovaných dat ze služby Azure Cost Management | Microsoft Docs'
description: Tento článek popisuje, jak vytvořit a spravovat exportovaná data služby Azure Cost Management tak, aby se dala používat v externích systémech.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/05/2019
ms.topic: tutorial
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 5f979f562ee871e7ae5f48de5bc53405cb3d1116
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55766400"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Kurz: Vytvoření a správa exportovaná data

Pokud jste si prošli kurz Analýza nákladů, pak jste obeznámeni s ručním stahováním dat ze služby Cost Management. Můžete však vytvořit opakované úlohy, která automaticky vyexportuje Cost Management data do služby Azure storage na základě denně, týdně nebo měsíčně. Exportovaná data jsou ve formátu CSV a obsahují všechny informace, které služba Cost Management shromažďuje. Exportovaná data v úložišti Azure pak můžete používat s externími systémy a kombinovat je se svými vlastními daty. Exportovaná data můžete používat v externím systému, jako je třeba řídicí panel nebo jiný finanční systém.

Příklady v tomto kurzu vás provedou exportem dat služby Cost Management a následným ověřením, že se daná data úspěšně exportovala.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit každodenní export
> * Ověřit shromáždění dat

## <a name="prerequisites"></a>Požadavky
Export dat je k dispozici pro různé typy účtů Azure, včetně [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) zákazníků. Chcete-li zobrazit úplný seznam typů podporovaných účtů, najdete v článku [datům Cost managementu pochopit](understand-cost-mgt-data.md). Při exportu dat uživatelů a skupin se u předplatných podporují následující oprávnění Azure:

- Vlastník – může vytvářet, upravovat a odstraňovat naplánované exporty u předplatných.
- Přispěvatel – může vytvářet, upravovat a odstraňovat svoje vlastní naplánované exporty. Může měnit názvy naplánovaných exportů, které vytvořili jiní uživatelé.
- Čtenář – může naplánovat exporty, ke kterým má oprávnění.

Pro účty služby Azure Storage:
- Ke změně nakonfigurovaného účtu úložiště se vyžaduje oprávnění k zápisu, a to bez ohledu na oprávnění k exportu.
- Příslušný účet úložiště Azure musí být nakonfigurovaný jako úložiště souborů nebo objektů blob.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Vytvořit každodenní export

Správa nákladů a fakturace &gt; Cost Management &gt; vyberte předplatné nebo skupinu prostředků v předplatném &gt; Exportovat &gt; **Přidat**.

Zadejte název pro export a vyberte možnost "Každý den export nákladů za měsíc k datu". Klikněte na tlačítko **Další**.

![Nový příklad pro export zobrazující export typu](./media/tutorial-export-acm-data/basics_exports.png)

Zadejte předplatné pro svůj účet úložiště Azure a pak vyberte svůj účet úložiště.  Zadejte kontejner úložiště i Cesta k adresáři, který chcete přejít k souboru exportu.  Klikněte na tlačítko **Další**.

![Nový příklad pro export zobrazuje podrobnosti o účtu úložiště](./media/tutorial-export-acm-data/storage_exports.png)

Projděte si podrobnosti o vašem export a klikněte na tlačítko **vytvořit**.

Nový export se zobrazí v seznamu exportů. Ve výchozím nastavení jsou nové exporty povolené. Pokud chcete zakázat nebo odstranit naplánovaný export, klikněte na libovolnou položku v seznamu a pak klikněte na **Zakázat** nebo **Odstranit**.

Na začátku může trvat jednu až dvě hodiny, než se export spustí. Může však trvat až čtyři hodiny, než se v exportovaných souborech zobrazí data.

### <a name="export-schedule"></a>Exportujte plán

Naplánované exporty jsou ovlivněny čas a den v týdnu při počátečním vytvoření exportu. Při vytváření naplánované export exportu se spustí ve stejnou dobu den pro každý výskyt následné export. Například můžete vytvořit denní export v 13:00. Další export běží: 00: 00 následujícího dne. Aktuální čas ovlivňuje všechny ostatní typy export stejným způsobem – vždy spouštět ve stejnou dobu den jako při počátečním vytvoření exportu. V jiném příkladu vytvoříte týdenní exportu v 16:00:00 v pondělí. Sestava příštích běží v 16:00:00 následující pondělí. *Exportovaná data je k dispozici do čtyř hodin běhu.*

Každý export vytvoří nový soubor, tak starší exporty se nepřepíšou.

Existují tři typy možností:

**Denní export náklady za měsíc k datu** – počáteční export se spustí okamžitě. Následující export spustit následující den ve stejnou dobu jako počáteční export. Nejnovější data se shromažďuje od předchozí denní exporty.

**Týdenní náklady za posledních sedm dní** – počáteční export se spustí okamžitě. Spusťte následující export dne v týdnu a ve stejnou dobu jako počáteční export. Náklady jsou za posledních sedm dní.

**Vlastní** – umožňuje naplánovat týdenní a měsíční exportuje s možnostmi pro týden k datu a měsíc k datu. *Počáteční export se spustí okamžitě.*

![Nový export – základní informace o kartě zobrazen vlastní týdenní výběr týden k datu](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>Ověřit shromáždění dat

Pomocí Průzkumníka služby Azure Storage můžete snadno ověřit, že se data služby Cost Management shromažďují, a zobrazit exportovaný soubor CSV.

V seznamu exportu klikněte na název účtu úložiště. Na stránce účtu úložiště klikněte na Otevřít v Průzkumníkovi. Pokud se zobrazí potvrzovací okno, klikněte na **Ano** a otevřete soubor v Průzkumníkovi služby Azure Storage.

![Zobrazuje informace z příkladu a propojte ho otevřít v Průzkumníkovi stránce účtu úložiště](./media/tutorial-export-acm-data/storage-account-page.png)

V Průzkumníkovi služby Storage přejděte ke kontejneru, který chcete otevřít, a vyberte složku odpovídající aktuálnímu měsíci. Zobrazí se seznam souborů CSV. Vyberte jeden soubor a pak klikněte na **Otevřít**.

![Příklad informace zobrazené v Průzkumníku služby Storage](./media/tutorial-export-acm-data/storage-explorer.png)

Soubor se otevře v programu nebo aplikaci, která je nastavená k otevírání souborů s příponou CSV. Tady je příklad v Excelu.

![Příklad exportovat data ve formátu CSV je znázorněno v aplikaci Excel](./media/tutorial-export-acm-data/example-export-data.png)

## <a name="access-exported-data-from-other-systems"></a>Přístup k exportovaným datům z jiných systémů

Jedním z důvodů pro export dat služby Cost Management je umožnění přístupu k těmto datům z externích systémů. Možná používáte systém řídicích panelů nebo jiný finanční systém. Takové systémy se dost liší, takže ukázka s příkladem by nemusela být užitečná.  S přístupem k datům z vašich aplikací však můžete začít podle tématu [Úvod do Azure Storage](../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit každodenní export
> * Ověřit shromáždění dat

Přejděte k dalšímu kurzu, který se zabývá optimalizací a zvyšováním efektivity pomocí identifikace nečinných a nedostatečně využitých prostředků.

> [!div class="nextstepaction"]
> [Kontrola optimalizačních doporučení a reakce na ně](tutorial-acm-opt-recommendations.md)

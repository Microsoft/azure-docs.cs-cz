---
title: Kurz – export dat z Azure Cost Management
description: V tomto článku se dozvíte, jak můžete vytvářet a spravovat exportovaná Azure Cost Management data, abyste je mohli použít v externích systémech.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/12/2019
ms.topic: tutorial
ms.service: cost-management-billing
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 5d5f6bc4620d60d3eb776a6229450e02035b8290
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441022"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Kurz: Vytvoření a správa exportovaných dat

Pokud jste si prošli kurz Analýza nákladů, pak jste obeznámeni s ručním stahováním dat ze služby Cost Management. Můžete však vytvořit opakované úlohy, která automaticky vyexportuje Cost Management data do služby Azure storage na základě denně, týdně nebo měsíčně. Exportovaná data jsou ve formátu CSV a obsahují všechny informace, které služba Cost Management shromažďuje. Exportovaná data v úložišti Azure pak můžete používat s externími systémy a kombinovat je se svými vlastními daty. Exportovaná data můžete používat v externím systému, jako je třeba řídicí panel nebo jiný finanční systém.

Podívejte se na [článek Jak naplánovat exporty do úložiště pomocí Azure cost management](https://www.youtube.com/watch?v=rWa_xI1aRzo) videa o vytvoření plánovaného exportu vašich dat Azure pro Azure Storage.

Příklady v tomto kurzu vás provedou exportem dat služby Cost Management a následným ověřením, že se daná data úspěšně exportovala.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit každodenní export
> * Ověření shromáždění dat

## <a name="prerequisites"></a>Požadavky
Export dat je dostupný pro celou řadu typů účtů Azure, včetně zákazníků [smlouva Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) . Úplný seznam podporovaných typů účtů najdete v tématu [pochopení cost management dat](understand-cost-mgt-data.md). Následující oprávnění Azure (nebo obory) jsou podporovaná pro každé předplatné pro export dat podle uživatele a skupiny. Další informace o oborech najdete v tématu [pochopení a práce s obory](understand-work-scopes.md).

- Vlastník – může vytvářet, upravovat a odstraňovat naplánované exporty u předplatných.
- Přispěvatel – může vytvářet, upravovat a odstraňovat svoje vlastní naplánované exporty. Může měnit názvy naplánovaných exportů, které vytvořili jiní uživatelé.
- Čtenář – může naplánovat exporty, ke kterým má oprávnění.

Pro účty služby Azure Storage:
- Ke změně nakonfigurovaného účtu úložiště se vyžaduje oprávnění k zápisu, a to bez ohledu na oprávnění k exportu.
- Příslušný účet úložiště Azure musí být nakonfigurovaný jako úložiště souborů nebo objektů blob.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Vytvořit každodenní export

Pokud chcete vytvořit nebo zobrazit export dat nebo naplánovat export, otevřete požadovaný obor v Azure Portal a v nabídce vyberte **Analýza nákladů** . Přejděte například na **odběry**, vyberte předplatné ze seznamu a potom v nabídce vyberte **Analýza nákladů** . V horní části stránky analýza nákladů klikněte na **exportovat** a pak zvolte možnost exportu. Klikněte například na položku **naplánovat export**.  

> [!NOTE]
> - Kromě předplatných můžete vytvářet exporty skupin prostředků, účtů, oddělení a registrací. Další informace o oborech najdete v tématu [pochopení a práce s obory](understand-work-scopes.md).
>- Když jste se přihlásili jako partner v oboru fakturačního účtu nebo v tenantovi zákazníka, můžete data exportovat do účtu Azure Storage, který je propojený s vaším účtem úložiště partnera. V tenantovi CSP ale musíte mít aktivní předplatné.
>


Klikněte na tlačítko **Přidat**, zadejte název exportu a pak vyberte možnost **denní export nákladů na měsíc do data** . Klikněte na **Další**.

![Nový příklad pro export zobrazující export typu](./media/tutorial-export-acm-data/basics_exports.png)

Zadejte předplatné pro svůj účet úložiště Azure a pak vyberte svůj účet úložiště.  Zadejte kontejner úložiště i Cesta k adresáři, který chcete přejít k souboru exportu.  Klikněte na **Další**.

![Nový příklad pro export zobrazuje podrobnosti o účtu úložiště](./media/tutorial-export-acm-data/storage_exports.png)

Projděte si podrobnosti o vašem export a klikněte na tlačítko **vytvořit**.

Nový export se zobrazí v seznamu exportů. Ve výchozím nastavení jsou nové exporty povolené. Pokud chcete zakázat nebo odstranit naplánovaný export, klikněte na libovolnou položku v seznamu a pak klikněte na **Zakázat** nebo **Odstranit**.

Na začátku může trvat jednu až dvě hodiny, než se export spustí. Může však trvat až čtyři hodiny, než se v exportovaných souborech zobrazí data.

### <a name="export-schedule"></a>Plán exportu

Plánované exporty ovlivňuje čas a den v týdnu počátečního vytvoření exportu. Když vytvoříte plánovaný export, bude se každý následný export spouštět ve stejnou denní dobu. Vytvoříte například denní export v 13:00. Další export se spustí v 13:00 následující den. Aktuální čas má vliv na všechny ostatní typy exportu stejným způsobem – vždy se spustí ve stejnou denní dobu jako při počátečním vytvoření exportu. V jiném příkladu vytvoříte týdenní export v pondělí v 16:00. Další export se spustí v 16:00 následující pondělí. *Exportovaná data jsou k dispozici do čtyř hodin od spuštění.*

Každý export vytvoří nový soubor, takže starší exporty se nepřepíší.

Rozlišujeme tři typy možností exportu:

**Denní export nákladů na měsíc** – počáteční export se spustí hned. Následné exporty se spustí následující den ve stejnou dobu jako počáteční export. Nejnovější data jsou agregována z předchozích denních exportů.

**Týdenní export nákladů za posledních 7 dní** – počáteční export se spustí okamžitě. Následné exporty se spustí v den v týdnu a ve stejnou dobu jako počáteční export. Náklady jsou za posledních sedm dnů.

**Vlastní** – umožňuje naplánovat týdenní a měsíční export s možnostmi v týdnu na aktuální a měsíc. *Počáteční export se spustí hned.*

Pokud máte předplatné s průběžnými platbami, MSDN nebo Visual Studio, fakturační období faktury nemusí přesně odpovídat kalendářnímu měsíci. Pro tyto typy předplatného a skupiny prostředků můžete vytvořit export, který bude odpovídat období faktury nebo kalendářním měsícům. Pokud chcete vytvořit Export zarovnaný k vašemu měsíci faktury, přejděte na **vlastní**a vyberte **Fakturovatelné – období k datu**.  Pokud chcete vytvořit Export přirovnaný k kalendářnímu měsíci, vyberte možnost **od začátku měsíce**.
>
>

![Nová karta základy exportu znázorňující výběr vlastního týdenního týdne](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>Ověření shromáždění dat

Pomocí Průzkumníka služby Azure Storage můžete snadno ověřit, že se data služby Cost Management shromažďují, a zobrazit exportovaný soubor CSV.

V seznamu exportu klikněte na název účtu úložiště. Na stránce účtu úložiště klikněte na Otevřít v Průzkumníkovi. Pokud se zobrazí potvrzovací okno, klikněte na **Ano** a otevřete soubor v Průzkumníkovi služby Azure Storage.

![Zobrazuje informace z příkladu a propojte ho otevřít v Průzkumníkovi stránce účtu úložiště](./media/tutorial-export-acm-data/storage-account-page.png)

V Průzkumníkovi služby Storage přejděte ke kontejneru, který chcete otevřít, a vyberte složku odpovídající aktuálnímu měsíci. Zobrazí se seznam souborů CSV. Vyberte jeden soubor a pak klikněte na **Otevřít**.

![Příklad informace zobrazené v Průzkumníku služby Storage](./media/tutorial-export-acm-data/storage-explorer.png)

Soubor se otevře v programu nebo aplikaci, která je nastavená k otevírání souborů s příponou CSV. Tady je příklad v Excelu.

![Příklad exportovat data ve formátu CSV je znázorněno v aplikaci Excel](./media/tutorial-export-acm-data/example-export-data.png)


## <a name="access-exported-data-from-other-systems"></a>Přístup k exportovaným datům z jiných systémů

Jedním z důvodů pro export dat služby Cost Management je umožnění přístupu k těmto datům z externích systémů. Možná používáte systém řídicích panelů nebo jiný finanční systém. Takové systémy se dost liší, takže ukázka s příkladem by nemusela být užitečná.  S přístupem k datům z vašich aplikací však můžete začít podle tématu [Úvod do Azure Storage](../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit každodenní export
> * Ověření shromáždění dat

Přejděte k dalšímu kurzu, který se zabývá optimalizací a zvyšováním efektivity pomocí identifikace nečinných a nedostatečně využitých prostředků.

> [!div class="nextstepaction"]
> [Kontrola optimalizačních doporučení a reakce na ně](tutorial-acm-opt-recommendations.md)

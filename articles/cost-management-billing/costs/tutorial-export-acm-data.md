---
title: Kurz – Vytvoření a správa exportovaných dat ze služby Azure Cost Management
description: Tento článek popisuje, jak vytvořit a spravovat exportovaná data služby Azure Cost Management tak, aby se dala používat v externích systémech.
author: bandersmsft
ms.author: banders
ms.date: 02/12/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 04228a5f3f7e0b10e3cd881043af7861bb9fb347
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251144"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Kurz: Vytvoření a správa exportovaných dat

Pokud jste si prošli kurz Analýza nákladů, pak jste obeznámeni s ručním stahováním dat ze služby Cost Management. Můžete však vytvořit opakující se úlohu, která denně, týdně nebo měsíčně automaticky exportuje data služby Cost Management do úložiště Azure. Exportovaná data jsou ve formátu CSV a obsahují všechny informace, které služba Cost Management shromažďuje. Exportovaná data v úložišti Azure pak můžete používat s externími systémy a kombinovat je se svými vlastními daty. Exportovaná data můžete používat v externím systému, jako je třeba řídicí panel nebo jiný finanční systém.

Podívejte se na video [How to schedule exports to storage with Azure Cost Management](https://www.youtube.com/watch?v=rWa_xI1aRzo) (Jak naplánovat exporty do úložiště pomocí služby Azure Cost Management), které se věnuje vytváření naplánovaného exportu dat nákladů na Azure do Azure Storage.

Příklady v tomto kurzu vás provedou exportem dat služby Cost Management a následným ověřením, že se daná data úspěšně exportovala.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit každodenní export
> * Ověřit shromáždění dat

## <a name="prerequisites"></a>Požadavky
Export dat je dostupný pro nejrůznější typy účtů Azure, včetně zákazníků se [smlouvou Enterprise ](https://azure.microsoft.com/pricing/enterprise-agreement/) a zákazníků se [Smlouvou se zákazníkem Microsoftu](get-started-partners.md). Úplný seznam podporovaných typů účtů si můžete prohlédnout v článku, který [vysvětluje data služby Cost Management](understand-cost-mgt-data.md). Při exportu dat uživatelů a skupin se u předplatných podporují následující oprávnění, neboli obory, Azure. Další informace o oborech najdete v tématu [Principy oborů a práce s nimi](understand-work-scopes.md).

- Vlastník – může vytvářet, upravovat a odstraňovat naplánované exporty u předplatných.
- Přispěvatel – může vytvářet, upravovat a odstraňovat svoje vlastní naplánované exporty. Může měnit názvy naplánovaných exportů, které vytvořili jiní uživatelé.
- Čtenář – může naplánovat exporty, ke kterým má oprávnění.

Pro účty služby Azure Storage:
- Ke změně nakonfigurovaného účtu úložiště se vyžaduje oprávnění k zápisu, a to bez ohledu na oprávnění k exportu.
- Příslušný účet úložiště Azure musí být nakonfigurovaný jako úložiště souborů nebo objektů blob.

Pokud máte nové předplatné, nemůžete rovnou využívat funkce služby Cost Management. Může trvat až 48 hodin, než budete moct využívat všechny funkce služby Cost Management.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Vytvořit každodenní export

Pokud chcete vytvořit nebo zobrazit export dat nebo naplánovat export, otevřete požadovaný obor na webu Azure Portal a v nabídce vyberte **Analýza nákladů**. Přejděte například na **Předplatná**, vyberte předplatné ze seznamu a pak v nabídce vyberte **Analýza nákladů**. Nahoře na stránce Analýza nákladů vyberte **Exportovat** a zvolte možnost exportu. Vyberte například **Naplánovat export**.  

> [!NOTE]
> - Kromě předplatných můžete vytvářet exporty u skupin prostředků, účtů, oddělení a registrací. Další informace o oborech najdete v tématu [Principy oborů a práce s nimi](understand-work-scopes.md).
>- Pokud jste přihlášeni jako partner v oboru fakturačního účtu nebo k tenantovi zákazníka, můžete exportovat data do účtu Azure Storage, který je propojen s partnerským účtem úložiště. Musíte mít ale aktivní předplatné ve svém tenantovi CSP.
>


Vyberte **Přidat**, zadejte název exportu a potom vyberte možnost **Denní export nákladů od začátku měsíce**. Vyberte **Další**.

![Příklad nového exportu znázorňující typ exportu](./media/tutorial-export-acm-data/basics_exports.png)

Zadejte předplatné pro účet úložiště Azure a vyberte účet úložiště.  Zadejte kontejner úložiště a cestu k adresáři, do kterého chcete exportovat soubor. Vyberte **Další**.

![Příklad nového exportu znázorňující podrobnosti o účtu úložiště](./media/tutorial-export-acm-data/storage_exports.png)

Zkontrolujte podrobnosti exportu a vyberte **Vytvořit**.

Nový export se zobrazí v seznamu exportů. Ve výchozím nastavení jsou nové exporty povolené. Pokud chcete zakázat nebo odstranit naplánovaný export, vyberte libovolnou položku v seznamu a pak vyberte **Zakázat** nebo **Odstranit**.

Na začátku může trvat jednu až dvě hodiny, než se export spustí. Může však trvat až čtyři hodiny, než se v exportovaných souborech zobrazí data.

### <a name="export-schedule"></a>Plán exportu

Naplánované exporty jsou ovlivněny časem a dnem v týdnu, kdy byl export původně vytvořen. Když vytvoříte naplánovaný export, export se bude spouštět se stejnou frekvencí pro každý další výskyt exportu. Například pro export od začátku měsíce nastavený na denní frekvenci se export bude spouštět denně. Podobně u týdenního exportu bude export probíhat každý týden ve stejný den, ve kterém je naplánován. Přesná doba doručení exportu není zaručena a exportovaná data jsou dostupná během čtyř hodin od spuštění.
Každý export vytvoří nový soubor, takže starší exporty se nepřepíšou.

Existují dva typy možností exportu:

**Denní export nákladů od začátku měsíce** – počáteční export se spustí okamžitě. Následné exporty se spustí další den ve stejnou dobu jako počáteční export. Nejnovější data jsou agregována z předchozích denních exportů.

**Vlastní** – umožňuje naplánovat týdenní a měsíční exporty s možnostmi od začátku týdne a od začátku měsíce. *Počáteční export se spustí okamžitě.*

Pokud máte předplatné s průběžnými platbami, MSDN nebo předplatné sady Visual Studio, nemusí se fakturační období shodovat s kalendářním měsícem. Pro tyto typy předplatného a skupiny prostředků můžete vytvořit export, který se shoduje s vaším fakturačním obdobím nebo kalendářním měsícem. Pokud chcete vytvořit export, který se shoduje s fakturačním měsícem, přejděte na **Vlastní** a potom vyberte **Od začátku fakturačního období**.  Pokud chcete vytvořit export, který se shoduje s kalendářním měsícem, vyberte možnost **Od začátku měsíce**.
>
>

![Nový export – karta Základy znázorňující vlastní týdenní výběr od začátku týdne](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>Ověřit shromáždění dat

Pomocí Průzkumníka služby Azure Storage můžete snadno ověřit, že se data služby Cost Management shromažďují, a zobrazit exportovaný soubor CSV.

V seznamu exportu vyberte název účtu úložiště. Na stránce účtu úložiště vyberte Otevřít v Průzkumníkovi. Pokud se zobrazí potvrzovací okno, vyberte **Ano** a otevřete soubor v Průzkumníkovi služby Azure Storage.

![Stránka účtu úložiště znázorňující ukázkové informace a odkaz Otevřít v Průzkumníkovi](./media/tutorial-export-acm-data/storage-account-page.png)

V Průzkumníkovi služby Storage přejděte ke kontejneru, který chcete otevřít, a vyberte složku odpovídající aktuálnímu měsíci. Zobrazí se seznam souborů CSV. Vyberte jeden soubor a pak vyberte **Otevřít**.

![Ukázkové informace zobrazené v Průzkumníku služby Storage](./media/tutorial-export-acm-data/storage-explorer.png)

Soubor se otevře v programu nebo aplikaci, která je nastavená k otevírání souborů s příponou CSV. Tady je příklad v Excelu.

![Příklad exportovaných dat CSV zobrazených v Excelu](./media/tutorial-export-acm-data/example-export-data.png)


## <a name="access-exported-data-from-other-systems"></a>Přístup k exportovaným datům z jiných systémů

Jedním z důvodů pro export dat služby Cost Management je umožnění přístupu k těmto datům z externích systémů. Možná používáte systém řídicích panelů nebo jiný finanční systém. Takové systémy se dost liší, takže ukázka s příkladem by nemusela být užitečná.  S přístupem k datům z vašich aplikací však můžete začít podle tématu [Úvod do Azure Storage](../../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit každodenní export
> * Ověřit shromáždění dat

Přejděte k dalšímu kurzu, který se zabývá optimalizací a zvyšováním efektivity pomocí identifikace nečinných a nedostatečně využitých prostředků.

> [!div class="nextstepaction"]
> [Kontrola optimalizačních doporučení a reakce na ně](tutorial-acm-opt-recommendations.md)

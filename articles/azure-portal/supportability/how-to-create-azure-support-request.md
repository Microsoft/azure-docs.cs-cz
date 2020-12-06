---
title: Jak vytvořit žádost o podporu Azure
description: Zákazníci, kteří potřebují pomoc, můžou pomocí Azure Portal najít samoobslužná řešení a vytvářet a spravovat žádosti o podporu.
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.topic: how-to
ms.date: 06/25/2020
ms.openlocfilehash: 11ca7925ce1664b5586ab8ec0fb523a2d562ee80
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745583"
---
# <a name="create-an-azure-support-request"></a>Vytvoření žádosti o podporu Azure

Azure umožňuje vytvářet a spravovat žádosti o podporu, označované taky jako lístky podpory. Můžete vytvářet a spravovat požadavky v [Azure Portal](https://portal.azure.com), který je popsaný v tomto článku. Žádosti můžete také vytvářet a spravovat programově pomocí [lístku podpory Azure REST API](/rest/api/support).

> [!NOTE]
> Adresa URL Azure Portal je specifická pro cloud Azure, ve kterém je vaše organizace nasazená.
>
>* Azure Portal pro komerční použití: [https://portal.azure.com](https://portal.azure.com)
>* Azure Portal pro Německo je: [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* Azure Portal pro USA vládu: [https://portal.azure.us](https://portal.azure.us)

Prostředí žádosti o podporu se zaměřuje na tři hlavní cíle:

* **Zjednodušené**: Díky podpoře a řešení potíží snadno najdete a zjednodušíte, jak odeslat žádost o podporu.
* **Integrovaná**: žádost o podporu můžete snadno otevřít, když řešíte problém s prostředkem Azure, aniž byste museli přepnout kontext.
* **Efektivní**: Shromážděte klíčové informace, které pracovník podpory potřebuje k efektivnímu vyřešení vašeho problému.

Azure poskytuje neomezenou podporu pro správu předplatného, včetně fakturace, úpravy kvót a přenosů účtů. Pro technickou podporu potřebujete plán podpory. Další informace najdete v tématu [Porovnání plánů podpory](https://azure.microsoft.com/support/plans).

## <a name="getting-started"></a>Začínáme

Můžete získat pomoc a **podporu** v Azure Portal. Je k dispozici v nabídce Azure Portal, v globální hlavičce nebo v nabídce prostředků služby. Než budete moct vytvořit žádost o podporu, musíte mít příslušná oprávnění.

### <a name="azure-role-based-access-control"></a>Řízení přístupu na základě role v Azure

Pokud chcete vytvořit žádost o podporu, musíte být [vlastníkem](../../role-based-access-control/built-in-roles.md#owner), [přispěvatelem](../../role-based-access-control/built-in-roles.md#contributor) nebo přiřazenou roli [Přispěvatel žádosti o podporu](../../role-based-access-control/built-in-roles.md#support-request-contributor) na úrovni předplatného. Pokud chcete vytvořit žádost o podporu bez předplatného, například Azure Active Directory scénář, musíte být [správce](../../active-directory/roles/permissions-reference.md).

### <a name="go-to-help--support-from-the-global-header"></a>Přejít na pomoc a podpora z globálního záhlaví

Spuštění žádosti o podporu odkudkoli v Azure Portal:

1. Vyberte **?** v globálním záhlaví. Pak vyberte **help + podpora**.

   ![Nápověda a podpora](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

1. Vyberte **Nová žádost o podporu**. Podle pokynů zadejte informace o vašem problému. Navrhneme některá možná řešení, shromáždíme podrobnosti o problému a pomůžeme vám odeslat a sledovat žádost o podporu.

   ![Nová žádost o podporu](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Přejít na pomoc a podpora z nabídky prostředků

K zahájení žádosti o podporu v kontextu prostředku teď pracujete s:

1. V nabídce prostředek v části Podpora a **řešení potíží** vyberte **Nová žádost o podporu**.

   ![V kontextu](./media/how-to-create-azure-support-request/incontext2lower.png)

1. Postupujte podle pokynů a poskytněte nám informace o problému, který máte. Když zahájíte proces žádosti o podporu z prostředku, některé možnosti jsou pro vás předem vybrané.

## <a name="create-a-support-request"></a>Vytvoření žádosti o podporu

Provede vás několik kroků, které vám pomůžou shromáždit informace o vašem problému a vyřešit ho. Jednotlivé kroky jsou popsány v následujících částech.

### <a name="basics"></a>Základy

V prvním kroku procesu žádosti o podporu se shromažďují základní informace o vašem problému a plánu podpory.

Na kartě **základy** **nové žádosti o podporu** Využijte selektory a začněte nám informovat o problému. Nejdříve zjistíte některé obecné kategorie pro typ problému a zvolíte související předplatné. Vyberte službu, například **virtuální počítač s Windows**. Vyberte prostředek, jako je název vašeho virtuálního počítače. Popište problém vlastním slovem a potom vyberte **typ problému** a podtyp **problému** , abyste získali konkrétnější informace.

![Okno Základy](./media/how-to-create-azure-support-request/basics2lower.png)

### <a name="solutions"></a>Řešení

Po shromáždění základních informací vám dál ukážeme řešení, která si můžete vyzkoušet sami. V některých případech můžeme dokonce spustit rychlou diagnostiku. Řešení jsou psaná inženýry Azure a vyřeší nejběžnější problémy.

### <a name="details"></a>Podrobnosti

Dále shromáždíme další podrobnosti o problému. Poskytování podrobných a podrobných informací v tomto kroku nám pomůže směrovat vaši žádost o podporu na správnou inženýra.

1. Pokud je to možné, řekněte nám, kdy problém začal, a kroky pro reprodukování. Můžete nahrát soubor, například soubor protokolu nebo výstup z diagnostiky. Další informace o nahrání souborů najdete v tématu [pokyny pro nahrávání souborů](how-to-manage-azure-support-request.md#file-upload-guidelines).

1. Až máme všechny informace o problému, vyberte, jak se má podpora získat. V části **informace** o **metodě podpory** vyberte závažnost dopadu. Maximální úroveň závažnosti závisí na [plánu podpory](https://azure.microsoft.com/support/plans).

    Ve výchozím nastavení je vybraná možnost **Sdílet diagnostické informace** . Díky tomu může podpora Azure shromažďovat [diagnostické informace](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) z vašich prostředků Azure. V některých případech je k dispozici druhá otázka, která není ve výchozím nastavení vybrána, například vyžádání přístupu k paměti virtuálního počítače.

1. Poskytněte preferovanou metodu kontaktu, vhodnou dobu, kterou vás budou kontaktovat a váš jazyk podpory.

1. Potom dokončete část **kontaktní informace** , abychom věděli, jak vás kontaktovat.

### <a name="review--create"></a>Kontrola a vytvoření

Na každé kartě dokončete všechny požadované informace a pak vyberte **zkontrolovat + vytvořit**. Projděte si podrobnosti, které odešlete podpoře. Vraťte se na libovolnou kartu a v případě potřeby proveďte změnu. Až budete spokojeni se žádostí o podporu, vyberte **vytvořit**.

Pracovník podpory vás bude kontaktovat pomocí vámi zadané metody. Informace o počátečních časech odezvy najdete v tématu [Podpora rozsahu a odezvy](https://azure.microsoft.com/support/plans/response/).


## <a name="next-steps"></a>Další kroky

Další informace o možnostech samoobslužné pomoci v Azure najdete v tomto videu:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

Další informace najdete na následujících odkazech:

* [Jak spravovat žádost o podporu Azure](how-to-manage-azure-support-request.md)
* [Rozhraní REST API lístku podpory Azure](/rest/api/support)
* [Pošlete nám svůj názor a návrhy.](https://feedback.azure.com/forums/266794-support-feedback)
* Zaujmout nás na [Twitteru](https://twitter.com/azuresupport)
* Získejte pomoc od svých partnerů na stránce s [otázkou Microsoft Q&](/answers/products/azure) .
* Další informace najdete v tématu [Nejčastější dotazy k podpoře Azure](https://azure.microsoft.com/support/faq) .
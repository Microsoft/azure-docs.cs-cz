---
title: Jak vytvořit žádost o podporu Azure | Microsoft Docs
description: Zákazníci, kteří potřebují pomoc, můžou pomocí Azure Portal najít samoobslužná řešení a vytvářet a spravovat žádosti o podporu.
services: Azure Supportability
author: ganganarayanan
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: article
ms.date: 03/31/2020
ms.author: kfollis
ms.openlocfilehash: 2b68b6ddf19d6b07475e7009b47e162bfb2d0d2f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80478985"
---
# <a name="how-to-create-an-azure-support-request"></a>Jak vytvořit žádost o podporu Azure

## <a name="overview"></a>Přehled

Azure umožňuje vytvářet a spravovat žádosti o podporu, označované taky jako lístky podpory. Můžete vytvářet a spravovat požadavky v [Azure Portal](https://portal.azure.com), který je popsaný v tomto článku. Žádosti můžete také vytvářet a spravovat programově pomocí [lístku podpory Azure REST API](/rest/api/support).

> [!NOTE]
> Adresa URL Azure Portal je specifická pro cloud Azure, ve kterém je vaše organizace nasazená.
>
>* Azure Portal pro komerční použití:[https://portal.azure.com](https://portal.azure.com)
>* Azure Portal pro Německo je:[https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* Azure Portal pro USA vládu:[https://portal.azure.us](https://portal.azure.us)
>
>

Na základě zpětné vazby od zákazníků jsme aktualizovali prostředí žádosti o podporu, abychom se mohli soustředit na tři hlavní cíle:

* **Zjednodušené**: Díky podpoře a řešení potíží snadno najdete a zjednodušíte, jak odeslat žádost o podporu.
* **Integrovaná**: žádost o podporu můžete snadno otevřít, když řešíte problém s prostředkem Azure, aniž byste museli přepnout kontext.
* **Efektivní**: Shromážděte klíčové informace, které Agent podpory potřebuje k efektivnímu vyřešení vašeho problému.

## <a name="getting-started"></a>Začínáme

Můžete získat pomoc a **podporu** v Azure Portal. Je k dispozici v nabídce Azure Portal, v globální hlavičce nebo v nabídce prostředků služby. Než budete moct vytvořit žádost o podporu, musíte mít příslušná oprávnění.

### <a name="role-based-access-control"></a>Řízení přístupu na základě role

Pokud chcete vytvořit žádost o podporu, musíte být [vlastníkem](../../role-based-access-control/built-in-roles.md#owner), [přispěvatelem](../../role-based-access-control/built-in-roles.md#contributor) nebo přiřazenou roli [Přispěvatel žádosti o podporu](../../role-based-access-control/built-in-roles.md#support-request-contributor) na úrovni předplatného. Pokud chcete vytvořit žádost o podporu bez předplatného, například scénář Azure Active Directory (AAD), musíte být [správce](../../active-directory/users-groups-roles/directory-assign-admin-roles.md).

### <a name="go-to-help--support-from-the-global-header"></a>Přejít na pomoc a podpora z globálního záhlaví

Spuštění žádosti o podporu odkudkoli v Azure Portal:

1. Ve službě Power BI vyberte **?** v globálním záhlaví. Pak vyberte **help + podpora**.

   ![Nápověda a podpora](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

2. Vyberte **novou žádost o podporu**. Postupujte podle pokynů a poskytněte nám informace o vašem problému. Navrhneme některá možná řešení, shromáždíme podrobnosti o problému a pomůžeme vám odeslat a sledovat žádost o podporu.

   ![Nová žádost o podporu](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Přejít na pomoc a podpora z nabídky prostředků

K zahájení žádosti o podporu v kontextu prostředku teď pracujete s:

1. V nabídce prostředek v části Podpora a **řešení potíží** vyberte **Nová žádost o podporu**.

   ![V kontextu](./media/how-to-create-azure-support-request/incontext2lower.png)

2. Postupujte podle pokynů a poskytněte nám informace o problému, který máte. Když zahájíte proces žádosti o podporu z prostředku, některé možnosti jsou pro vás předem vybrané.

## <a name="create-a-support-request"></a>Vytvoření žádosti o podporu

Provede vás několik kroků, které vám pomůžou shromáždit informace o vašem problému a vyřešit ho. Jednotlivé kroky jsou popsány v následujících částech.

### <a name="basics"></a>Základy

V prvním kroku procesu žádosti o podporu se shromažďují základní informace o vašem problému a plánu podpory.

Na kartě **základy** **nové žádosti o podporu**Využijte selektory a začněte nám informovat o problému. Nejdříve zjistíte některé obecné kategorie pro typ problému a zvolíte související předplatné. Vyberte službu, například **virtuální počítač s Windows**. Vyberte prostředek, jako je název vašeho virtuálního počítače. Popište problém vlastním slovem a **Vyberte typ problému** , abyste získali konkrétnější informace.

![Okno Základy](./media/how-to-create-azure-support-request/basics2lower.png)

> [!NOTE]
> Azure poskytuje neomezenou podporu pro správu předplatného, včetně fakturace, úpravy kvót a přenosů účtů. Pro technickou podporu potřebujete plán podpory. [Přečtěte si další informace o plánech podpory](https://azure.microsoft.com/support/plans).
>
>

### <a name="solutions"></a>Řešení

Po shromáždění základních informací vám dál ukážeme řešení, která si můžete vyzkoušet sami. V některých případech můžeme dokonce spustit rychlou diagnostiku. Řešení jsou psaná inženýry Azure a vyřeší nejběžnější problémy.

### <a name="details"></a>Podrobnosti

Dále shromáždíme další podrobnosti o problému. Poskytování podrobných a podrobných informací v tomto kroku nám pomůže směrovat vaši žádost o podporu na správného agenta.

Pokud je to možné, řekněte nám, kdy problém začal, a kroky pro reprodukování. Můžete nahrát soubor, například soubor protokolu nebo výstup z diagnostiky.

Až máme všechny informace o problému, vyberte, jak se má podpora získat. V části **informace**o **metodě podpory** vyberte závažnost dopadu. Poskytněte preferovanou metodu kontaktu, vhodnou dobu, kterou vás budou kontaktovat a váš jazyk podpory.

Potom dokončete část **kontaktní informace** , abychom věděli, jak vás kontaktovat.

### <a name="review--create"></a>Zkontrolovat a vytvořit

Na každé kartě dokončete všechny požadované informace a pak vyberte **zkontrolovat + vytvořit**. Projděte si podrobnosti, které odešlete podpoře. Vraťte se na libovolnou kartu a v případě potřeby proveďte změnu. Až budete spokojeni se žádostí o podporu, vyberte **vytvořit**.

Agent podpory vás bude kontaktovat pomocí vámi zadané metody. Informace o počáteční době odezvy najdete v tématu [Rozsah podpory a rychlost odezvy](https://azure.microsoft.com/support/plans/response/).

## <a name="all-support-requests"></a>Všechny žádosti o podporu

Podrobnosti a stav žádostí o podporu můžete zobrazit tak, že přejdete na **help + podpora** >  **všech žádostí o podporu**.

![Všechny žádosti o podporu](./media/how-to-create-azure-support-request/allrequestslower.png)

Na této stránce můžete filtrovat žádosti o podporu podle **předplatného**, data **Vytvoření** (UTC) a **stavu**. Na této stránce můžete také třídit a hledat žádosti o podporu.

Vyberte žádost o podporu pro zobrazení podrobností, včetně závažnosti a očekávaného času, který bude trvat, než bude agent podpory reagovat.

Pokud chcete změnit závažnost žádosti, vyberte možnost **dopad na firmu**. Vyberte ze seznamu závažnosti, které se mají přiřadit.

> [!NOTE]
> Maximální úroveň závažnosti závisí na plánu podpory. [Přečtěte si další informace o plánech podpory](https://azure.microsoft.com/support/plans).
>
>
Další informace o možnostech samoobslužné pomoci v Azure najdete v tomto videu:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

## <a name="next-steps"></a>Další kroky

* [Pošlete nám svůj názor a návrhy.](https://feedback.azure.com/forums/266794-support-feedback)
* Zaujmout nás na [Twitteru](https://twitter.com/azuresupport)
* Získejte pomoc od svých partnerů ve [fórech MSDN](https://social.msdn.microsoft.com/Forums/azure) .
* Další informace najdete v tématu [Nejčastější dotazy k podpoře Azure](https://azure.microsoft.com/support/faq) .

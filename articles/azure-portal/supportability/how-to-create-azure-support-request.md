---
title: Jak vytvořit žádost o podporu Azure | Dokumenty společnosti Microsoft
description: Zákazníci, kteří potřebují pomoc, můžou pomocí portálu Azure najít samoobslužná řešení a vytvářet a spravovat žádosti o podporu.
services: Azure Supportability
author: ganganarayanan
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: article
ms.date: 03/31/2020
ms.author: kfollis
ms.openlocfilehash: 2b68b6ddf19d6b07475e7009b47e162bfb2d0d2f
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478985"
---
# <a name="how-to-create-an-azure-support-request"></a>Jak vytvořit žádost o podporu Azure

## <a name="overview"></a>Přehled

Azure umožňuje vytvářet a spravovat žádosti o podporu, označované také jako lístky podpory. Můžete vytvářet a spravovat požadavky na [webu Azure Portal](https://portal.azure.com), který je popsán v tomto článku. Můžete také vytvářet a spravovat požadavky programově pomocí [rozhraní REST API podpory Azure](/rest/api/support).

> [!NOTE]
> Adresa URL portálu Azure je specifická pro cloud Azure, kde se vaše organizace nasazuje.
>
>* Portál Azure pro komerční použití je:[https://portal.azure.com](https://portal.azure.com)
>* Portál Azure pro Německo je:[https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* Portál Azure pro vládu Spojených států je:[https://portal.azure.us](https://portal.azure.us)
>
>

Na základě zpětné vazby od zákazníků jsme aktualizovali prostředí pro žádosti o podporu tak, aby se zaměřilo na tři hlavní cíle:

* **Zjednodušené**: Usnadnit podporu a řešení potíží snadno najít a zjednodušit způsob odeslání žádosti o podporu.
* **Integrovaná**: Žádost o podporu můžete snadno otevřít, když řešíte problém s prostředkem Azure, aniž byste museli přepínat kontext.
* **Efektivní**: Shromážděte klíčové informace, které váš agent podpory potřebuje k efektivnímu vyřešení problému.

## <a name="getting-started"></a>Začínáme

Můžete získat **nápovědu + podporu** na webu Azure Portal. Je k dispozici v nabídce portálu Azure, globální záhlaví nebo nabídky prostředků pro službu. Před podáním žádosti o podporu musíte mít příslušná oprávnění.

### <a name="role-based-access-control"></a>Řízení přístupu na základě role

Chcete-li vytvořit žádost o podporu, musíte být [vlastníkem](../../role-based-access-control/built-in-roles.md#owner), [přispěvatelem](../../role-based-access-control/built-in-roles.md#contributor) nebo být přiřazeni k roli [přispěvatele žádosti o podporu](../../role-based-access-control/built-in-roles.md#support-request-contributor) na úrovni předplatného. Chcete-li vytvořit žádost o podporu bez předplatného, například scénář Azure Active Directory (AAD), musíte být [správce](../../active-directory/users-groups-roles/directory-assign-admin-roles.md).

### <a name="go-to-help--support-from-the-global-header"></a>Přejít na nápovědu + podporu z globálního záhlaví

Spuštění žádosti o podporu z libovolného místa na webu Azure Portal:

1. Ve službě Power BI vyberte **?** v globální záhlaví. Pak vyberte **Nápověda + podpora**.

   ![Nápověda a podpora](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

2. Vyberte **nový požadavek na podporu**. Postupujte podle pokynů a složte nám informace o vašem problému. Navrhneme některá možná řešení, shromáždíme podrobnosti o problému a pomůžeme vám odeslat a sledovat žádost o podporu.

   ![Nová žádost o podporu](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Přejít na nápovědu + podporu z nabídky zdrojů

Chcete-li zahájit žádost o podporu v kontextu zdroje, pracujete aktuálně s:

1. V nabídce prostředků vyberte v části **Podpora + Řešení potíží** možnost Nová žádost o **podporu**.

   ![V kontextu](./media/how-to-create-azure-support-request/incontext2lower.png)

2. Postupujte podle pokynů a poskytněte nám informace o problému, který máte. Při spuštění procesu žádosti o podporu z prostředku jsou některé možnosti předem vybrány za vás.

## <a name="create-a-support-request"></a>Vytvoření žádosti o podporu

Provedeme vás několika kroky, abychom shromáždili informace o vašem problému a pomohli vám ho vyřešit. Každý krok je popsán v následujících částech.

### <a name="basics"></a>Základy

První krok procesu žádosti o podporu shromažďuje základní informace o problému a plánu podpory.

Na kartě **Základy** **nové žádosti o podporu**nám pomocí selektorů začněte vyprávět o problému. Nejprve určíte některé obecné kategorie pro typ problému a zvolíte související předplatné. Vyberte službu, například **Virtuální počítač se systémem Windows**. Vyberte prostředek, například název virtuálního počítače. Popište problém vlastními slovy a **vyberte typ problému,** abyste získali konkrétnější.

![Okno Základy](./media/how-to-create-azure-support-request/basics2lower.png)

> [!NOTE]
> Azure poskytuje neomezenou podporu pro správu předplatného, která zahrnuje fakturaci, úpravy kvót a převody účtů. Pro technickou podporu potřebujete plán podpory. [Další informace o plánech podpory](https://azure.microsoft.com/support/plans).
>
>

### <a name="solutions"></a>Řešení

Po shromáždění základních informací vám dále ukážeme řešení, která si můžete vyzkoušet sami. V některých případech můžeme dokonce spustit rychlou diagnostiku. Řešení jsou napsána inženýry Azure a vyřeší nejběžnější problémy.

### <a name="details"></a>Podrobnosti

Dále shromažďujeme další podrobnosti o problému. Poskytnutí podrobných a podrobných informací v tomto kroku nám pomáhá směrovat vaši žádost o podporu na správného agenta.

Pokud je to možné, sdělte nám, kdy problém začal a všechny kroky k jeho reprodukci. Můžete nahrát soubor, například soubor protokolu nebo výstup z diagnostiky.

Poté, co budeme mít všechny informace o problému, zvolte, jak získat podporu. V části **Metoda podpory** **vyberte**závažnost dopadu. Poskytněte preferovanou metodu kontaktu, dobrý čas na to, abyste vás kontaktovali, a jazyk podpory.

Dále vyplňte sekci **Kontaktní údaje,** abychom věděli, jak vás kontaktovat.

### <a name="review--create"></a>Recenze + vytvořit

Vyplňte všechny požadované informace na každé kartě a pak vyberte **Zkontrolovat + vytvořit**. Zkontrolujte podrobnosti, které pošlete podpoře. V případě potřeby se vraťte na libovolnou kartu. Až budete spokojeni s dokončením žádosti o podporu, vyberte **Vytvořit**.

Zástupce podpory vás bude kontaktovat pomocí uvedené metody. Informace o počáteční době odezvy naleznete v [tématu Rozsah podpory a odezva](https://azure.microsoft.com/support/plans/response/).

## <a name="all-support-requests"></a>Všechny žádosti o podporu

Podrobnosti a stav žádostí o podporu můžete zobrazit tak, že přejdete na **nápovědu + podporu** >  **Všechny žádosti o podporu**.

![Všechny žádosti o podporu](./media/how-to-create-azure-support-request/allrequestslower.png)

Na této stránce můžete filtrovat žádosti o podporu podle **předplatného**, **data vytvoření** (UTC) a **stavu**. Kromě toho můžete třídit a vyhledávat žádosti o podporu na této stránce.

Vyberte žádost o podporu pro zobrazení podrobností, včetně závažnosti a očekávané doby, kterou bude trvat, než agent podpory odpoví.

Pokud chcete změnit závažnost žádosti, vyberte **dopad na firmu**. Vyberte ze seznamu závažností, které chcete přiřadit.

> [!NOTE]
> Maximální úroveň závažnosti závisí na plánu podpory. [Další informace o plánech podpory](https://azure.microsoft.com/support/plans).
>
>
Další informace o možnostech svépomocné podpory v Azure najdete v tomto videu:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

## <a name="next-steps"></a>Další kroky

* [Pošlete nám svůj názor a návrhy](https://feedback.azure.com/forums/266794-support-feedback)
* Spolus námi na [Twitteru](https://twitter.com/azuresupport)
* Získejte pomoc od svých kolegů ve [fórech MSDN](https://social.msdn.microsoft.com/Forums/azure)
* Další informace v [nejčastějších dotazech k podpoře Azure](https://azure.microsoft.com/support/faq)

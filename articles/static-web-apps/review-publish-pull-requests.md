---
title: Kontrola žádostí o přijetí změn v předprodukčních prostředích ve službě Azure static Web Apps
description: Přečtěte si, jak pomocí předprodukčních prostředí zkontrolovat změny žádostí o přijetí změn ve statických Web Appsch Azure.
services: static-web-apps
author: sinedied
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: yolasors
ms.openlocfilehash: f8f2e352ae458e3e2825c9701437ea652ba07375
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91825652"
---
# <a name="review-pull-requests-in-pre-production-environments-in-azure-static-web-apps-preview"></a>Kontrola žádostí o přijetí změn v předprodukčních prostředích ve službě Azure Static Web Apps ve verzi Preview

Tento článek ukazuje, jak pomocí předprodukčních prostředí zkontrolovat změny aplikací nasazených se [statickými Web Apps Azure](overview.md).

Předprodukční prostředí (fázování) je plně funkční připravená verze vaší aplikace, která obsahuje změny, které nejsou dostupné v produkčním prostředí.

Azure static Web Apps generuje pracovní postup akcí GitHubu v úložišti. Při vytvoření žádosti o přijetí změn pro větev, kterou pracovní postup sleduje, je předprodukční prostředí sestavené. Předprovozní fáze aplikace umožňuje provádět kontroly před odesláním do produkčního prostředí.

Při použití statického Web Apps Azure může současně existovat několik předprodukčních prostředí současně. Pokaždé, když vytvoříte žádost o přijetí změn na sledovanou větev, nainstaluje se připravené verze s vašimi změnami do samostatného předprodukčního prostředí.

Používání předprodukčních prostředí přináší spoustu výhod. Můžete například:

- Zkontrolujte vizuální změny mezi výrobou a produkčním prostředím. Například zobrazení aktualizací obsahu a rozložení.
- Demonstrujte změny svého týmu.
- Porovnejte různé verze vaší aplikace.
- Ověřte změny pomocí testů přijetí.
- Před nasazením do produkčního prostředí proveďte kontrolu správnosti.

> [!NOTE]
> Během období Preview je povolena [maximálně jedna Příprava pracovního prostředí](quotas.md) .

## <a name="prerequisites"></a>Předpoklady

- Existující úložiště GitHub s nakonfigurovaným statickým Web Appsm Azure. Pokud ho nemáte, přečtěte si téma [Vytvoření první statické aplikace](getting-started.md) .

## <a name="make-a-change"></a>Provést změnu

Začněte tím, že provedete změnu v úložišti. Můžete to provést přímo na GitHubu, jak je znázorněno v následujícím postupu.

1. Přejděte do úložiště projektu na GitHubu a potom kliknutím na tlačítko **větev** vytvořte novou větev.

    :::image type="content" source="./media/review-publish-pull-requests/create-branch.png" alt-text="Vytvoření nové větve pomocí rozhraní GitHubu":::]

    Zadejte název větve a klikněte na **vytvořit větev**.

1. Přejděte do složky _aplikace_ a změňte nějaký textový obsah. Můžete například změnit název nebo odstavec. Po nalezení souboru, který chcete upravit, klikněte na **Upravit** a proveďte změnu.

    :::image type="content" source="./media/review-publish-pull-requests/edit-file.png" alt-text="Vytvoření nové větve pomocí rozhraní GitHubu":::

1. Po provedení změn klikněte na **potvrzení změn** a potvrďte provedené změny ve větvi.

    :::image type="content" source="./media/review-publish-pull-requests/commit-changes.png" alt-text="Vytvoření nové větve pomocí rozhraní GitHubu":::

## <a name="create-a-pull-request"></a>Vytvoření žádosti o přijetí změn

Pak z této změny vytvořte žádost o přijetí změn.

1. Otevřete kartu **žádosti** o přijetí změn svého projektu na GitHubu:

    :::image type="content" source="./media/review-publish-pull-requests/tab.png" alt-text="Vytvoření nové větve pomocí rozhraní GitHubu":::

1. Klikněte na tlačítko **porovnat & žádosti** o přijetí změn ve vaší větvi.

1. Volitelně můžete zadat nějaké podrobnosti o změnách a pak kliknout na **vytvořit žádost o**přijetí změn.

    :::image type="content" source="./media/review-publish-pull-requests/open.png" alt-text="Vytvoření nové větve pomocí rozhraní GitHubu":::

V případě potřeby můžete přiřadit revidující a přidat komentáře k diskusi o změnách.

> [!NOTE]
> Vložením nových potvrzení do větve můžete provést několik změn. Žádost o získání dat se pak automaticky aktualizuje, aby odrážela všechny změny.

## <a name="review-changes"></a>Zkontrolovat změny

Po vytvoření žádosti o získání dat se spustí pracovní postup nasazení [akcí GitHubu](https://github.com/features/actions) a nasadí vaše změny do předprodukčního prostředí.

Jakmile pracovní postup dokončí sestavování a nasazování vaší aplikace, robot na GitHubu přidá komentář k žádosti o přijetí změn, která obsahuje adresu URL předprodukčního prostředí. Kliknutím na tento odkaz si můžete zobrazit připravené změny.

:::image type="content" source="./media/review-publish-pull-requests/bot-comment.png" alt-text="Vytvoření nové větve pomocí rozhraní GitHubu":::

Kliknutím na vygenerovanou adresu URL zobrazíte změny.

Pokud se podíváte na adresu URL, vidíte, že se zobrazuje takto: `https://<SUBDOMAIN-PULL_REQUEST_ID>.<AZURE_REGION>.azurestaticapps.net` .

V případě dané žádosti o přijetí změn adresa URL zůstane stejná i v případě, že jsou nabízeny nové aktualizace. Kromě stálé adresy URL se po celou dobu životnosti žádosti o přijetí změn znovu používá stejné předprodukční prostředí.

## <a name="publish-changes"></a>Publikování změn

Po schválení změn můžete své změny publikovat do produkčního prostředí sloučením žádosti o přijetí změn.

Klikněte na **Sloučit žádost o získání dat**:

:::image type="content" source="./media/review-publish-pull-requests/merge.png" alt-text="Vytvoření nové větve pomocí rozhraní GitHubu" větev). Následně se pracovní postup nasazení spustí na sledované větvi a po opětovném vytvoření aplikace budou změny v provozu.

Pokud chcete ověřit změny v produkčním prostředí, otevřete svou produkční adresu URL a načtěte živou verzi webu.

## <a name="limitations"></a>Omezení

Připravené verze vaší aplikace jsou aktuálně přístupné veřejně prostřednictvím adresy URL, i když je vaše úložiště GitHub soukromé.

> [!WARNING]
> Buďte opatrní při publikování citlivého obsahu na připravené verze, protože přístup k předprodukčním prostředím není omezený.

Počet předprodukčních prostředí dostupných pro každou aplikaci nasazenou se statickým Web Apps závisí na úrovni SKU, kterou používáte. Například u bezplatné úrovně můžete mít kromě produkčního prostředí 1 předprodukční prostředí.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nastavení vlastní domény](custom-domain.md)

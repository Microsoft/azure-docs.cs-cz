---
title: Konfigurace průběžného zálohování a obnovení bodu v čase pomocí Azure Portal v Azure Cosmos DB.
description: Naučte se identifikovat bod obnovení a nakonfigurovat průběžné zálohování pomocí Azure Portal. Ukazuje, jak obnovit živý a odstraněný účet.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: e0fb337defc69354aa625fe63bef9768dbc4cf1f
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538766"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-portal"></a>Konfigurace a Správa průběžného zálohování a obnovení k určitému bodu v čase (Preview) – použití Azure Portal
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Funkce obnovení bodu v čase (režim průběžného zálohování) pro Azure Cosmos DB je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Funkce obnovení k určitému bodu v čase služby Azure Cosmos DB (Preview) vám pomůže s obnovením z náhodné změny v rámci kontejneru, k obnovení odstraněného účtu, databáze nebo kontejneru nebo k obnovení do libovolné oblasti (kde zálohy existovaly). Režim průběžné zálohování umožňuje obnovení do libovolného časového okamžiku za posledních 30 dní.

Tento článek popisuje, jak identifikovat bod obnovení a nakonfigurovat průběžné zálohování pomocí Azure Portal.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>Zřízení účtu s průběžným zálohováním

Při vytváření nového účtu Azure Cosmos DB pro možnost **zásady zálohování** vyberte možnost **nepřetržitý** režim a povolte funkci obnovení bodu v čase pro nový účet. Po povolení této funkce pro účet jsou všechny databáze a kontejnery k dispozici pro průběžné zálohování. Při obnovení k určitému bodu v čase se data vždycky obnoví na nový účet, v současné době nemůžete obnovit existující účet.

:::image type="content" source="./media/continuous-backup-restore-portal/configure-continuous-backup-portal.png" alt-text="Zřídí účet Azure Cosmos DB s konfigurací průběžného zálohování." border="true":::

## <a name="restore-a-live-account-from-accidental-modification"></a><a id="restore-live-account"></a>Obnovení živého účtu z náhodné úpravy

Pomocí Azure Portal můžete obnovit živý účet nebo vybrané databáze a kontejnery. Data obnovíte pomocí následujících kroků:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/)
1. Přejděte na svůj účet Azure Cosmos DB a otevřete podokno **obnovení k časovému okamžiku** .

   > [!NOTE]
   > Podokno obnovení v Azure Portal je vyplněno pouze v případě, že máte `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` oprávnění. Další informace o tom, jak toto oprávnění nastavit, najdete v článku o [oprávněních k zálohování a obnovení](continuous-backup-restore-permissions.md) .

1. Pro obnovení zadejte následující podrobnosti:

   * **Bod obnovení (UTC)** – časové razítko během posledních 30 dnů. Tento účet by měl existovat v tomto časovém razítku. Můžete zadat bod obnovení ve standardu UTC. V případě, že ho chcete obnovit, může to být blízko druhé. Kliknutím na odkaz **kliknutím sem** získáte nápovědu k [identifikaci bodu obnovení](#event-feed).

   * **Location (umístění** ) – cílová oblast, ve které se účet obnoví. Účet by měl existovat v této oblasti v daném časovém razítku (např. Západní USA nebo Východní USA). Účet může být obnoven pouze v oblastech, ve kterých existoval zdrojový účet.

   * **Obnovit prostředek** – můžete buď zvolit **celý účet** , nebo **vybranou databázi nebo kontejner** pro obnovení. Databáze a kontejnery by měly existovat v daném časovém razítku. Na základě vybraného bodu obnovení a umístění se naplní prostředky obnovení, což umožňuje uživateli vybrat konkrétní databáze nebo kontejnery, které je potřeba obnovit.

   * **Skupina prostředků** – skupina prostředků, pod kterou se vytvoří a obnoví cílový účet. Skupina prostředků už musí existovat.

   * **Obnovit cílový účet** – název cílového účtu. Název cílového účtu musí při vytváření nového účtu splňovat stejné pokyny. Tento účet se vytvoří pomocí procesu obnovení ve stejné oblasti, ve které se nachází váš zdrojový účet.
 
   :::image type="content" source="./media/continuous-backup-restore-portal/restore-live-account-portal.png" alt-text="Obnovte živý účet z Azure Portal náhodné změny." border="true":::

1. Po vybrání výše uvedených parametrů kliknutím na tlačítko **Odeslat** zahájíte obnovení. Náklady na obnovení jsou jednorázovým poplatkem, který je založen na množství dat a poplatků za úložiště v dané oblasti. Další informace najdete v části s [cenami](continuous-backup-restore-introduction.md#continuous-backup-pricing) .

## <a name="use-event-feed-to-identify-the-restore-time"></a><a id="event-feed"></a>Určení času obnovení pomocí informačního kanálu událostí

Pokud při vyplňování času bodu obnovení v Azure Portal potřebujete pomáhat s určením bodu obnovení, vyberte odkaz **kliknutím sem** , přejdete do okna informační kanál události. Informační kanál událostí poskytuje úplný seznam věrnostních událostí pro vytváření, nahrazování a odstraňování událostí v databázích a kontejnerech zdrojového účtu. 

Pokud chcete například obnovit bod před odstraněním nebo aktualizací určitého kontejneru, podívejte se do tohoto informačního kanálu událostí. Události se zobrazují v chronologicky sestupném pořadí podle času, kdy k nim došlo, a to s posledními událostmi v horní části. Můžete procházet výsledky a vybrat čas před nebo po události pro pozdější zúžení času.

:::image type="content" source="./media/continuous-backup-restore-portal/event-feed-portal.png" alt-text="Pomocí informačního kanálu událostí Identifikujte čas bodu obnovení." border="true":::

> [!NOTE]
> Informační kanál událostí nezobrazuje změny prostředků položky. Kdykoli můžete v posledních 30 dnech zadat libovolné časové razítko (Pokud v daném čase existuje účet) pro obnovení.

## <a name="restore-a-deleted-account"></a><a id="restore-deleted-account"></a>Obnovení odstraněného účtu

K úplnému obnovení odstraněného účtu do 30 dnů od jeho odstranění můžete použít Azure Portal. Odstraněný účet obnovíte pomocí následujících kroků:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/)
1. Vyhledejte prostředky "Azure Cosmos DB" na globálním panelu hledání. Obsahuje seznam všech stávajících účtů.
1. Pak vyberte tlačítko **obnovit** . V podokně obnovení se zobrazí seznam odstraněných účtů, které se dají obnovit v rámci doby uchování, což je 30 dní od času odstranění.
1. Vyberte účet, který chcete obnovit.

   :::image type="content" source="./media/continuous-backup-restore-portal/restore-deleted-account-portal.png" alt-text="Obnoví odstraněný účet z Azure Portal." border="true":::

   > [!NOTE]
   > Poznámka: podokno obnovení v Azure Portal je vyplněno pouze v případě, že máte `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` oprávnění. Další informace o tom, jak toto oprávnění nastavit, najdete v článku o [oprávněních k zálohování a obnovení](continuous-backup-restore-permissions.md) .

1. Vyberte účet, který chcete obnovit, a zadejte následující podrobnosti pro obnovení odstraněného účtu:

   * **Bod obnovení (UTC)** – časové razítko během posledních 30 dnů. Účet by měl být v tomto časovém razítku. Zadejte bod obnovení ve standardu UTC. V případě, že ho chcete obnovit, může to být blízko druhé.

   * **Location (umístění** ) – cílová oblast, ve které je nutné obnovit účet. Zdrojový účet by měl existovat v této oblasti v daném časovém razítku. Příklad Západní USA nebo Východní USA.  

   * **Skupina prostředků** – skupina prostředků, pod kterou se vytvoří a obnoví cílový účet. Skupina prostředků už musí existovat.

   * **Obnovit cílový účet** – název cílového účtu musí při vytváření nového účtu splňovat stejné pokyny. Tento účet se vytvoří pomocí procesu obnovení ve stejné oblasti, ve které se nachází váš zdrojový účet.

## <a name="track-the-status-of-restore-operation"></a><a id="track-restore-status"></a>Sledovat stav operace obnovení

Po zahájení operace obnovení vyberte ikonu zvonku **oznámení** v pravém horním rohu portálu. Poskytuje odkaz, který zobrazuje stav obnoveného účtu. I když probíhá obnovení, stav účtu se vytvoří po dokončení operace obnovení, stav účtu se změní na online.

:::image type="content" source="./media/continuous-backup-restore-portal/track-restore-operation-status.png" alt-text="Stav obnoveného účtu se změní z vytváření na online po dokončení operace." border="true":::

## <a name="next-steps"></a>Další kroky

* Nakonfigurujte a spravujte průběžné zálohování pomocí [Azure CLI](continuous-backup-restore-command-line.md), [powershellu](continuous-backup-restore-powershell.md)nebo [Azure Resource Manager](continuous-backup-restore-template.md).
* [Model prostředků režimu průběžné zálohování](continuous-backup-restore-resource-model.md)
* [Správa oprávnění](continuous-backup-restore-permissions.md) potřebných pro obnovení dat pomocí režimu průběžné zálohování.

---
title: Migrace účtů úložiště Správce zařízení StorSimple, předplatných
description: Přečtěte si, jak migrovat předplatná a účty úložiště pro službu StorSimple Device Manager.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 428c336d98e278910b229e9c0d877a9ae6268c96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77169709"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Migrace předplatných a účtů úložiště přidružených ke službě StorSimple Device Manager

Možná budete muset přesunout službu StorSimple na novou registraci nebo na nové předplatné. Tyto scénáře migrace jsou změny účtu nebo změny datového centra. V následující tabulce můžete pochopit, které z těchto scénářů jsou podporovány, včetně podrobných kroků pro přesunutí.

## <a name="account-changes"></a>Změny účtu

| Můžete se pohybovat ...| Podporuje se| Výpadek| Proces podpory Azure| Přístup|
|-----|-----|-----|-----|-----|
| Celé předplatné (včetně účtů služeb a úložiště StorSimple) na jiný zápis? | Ano       | Ne       | **Převod zápisu**<br>Použít:<li>Když si koupíte nový závazek Azure v rámci nové smlouvy.</li><li>Chcete migrovat všechny účty a odběry ze staréregistrace do nového. To zahrnuje všechny služby Azure v rámci starého předplatného.</li> | **Krok 1: Otevřete lístek podpory operace Azure Enterprise.**<li>Přejděte [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport)na .</li><li> Vyberte **Správa zápisu** a pak vyberte **Převést z jednoho zápisu do nového zápisu**.<br>**Krok 2: Poskytnutí požadovaných informací**<br>Zahrnout:<li>číslo registrace zdroje</li><li> cílové registrační číslo</li><li>datum účinnosti převodu|
| Služba StorSimple z existujícího účtu do nového zápisu?    | Ano       | Ne       | **Převod účtu**<br>Použít:<li>Pokud nechcete úplný převod zápisu.</li><li>Chcete pouze přesunout konkrétní účty do nové registrace.</li>| **Krok 1: Otevřete lístek podpory operace Azure Enterprise.**<li>Přejděte [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport)na .</li><li>Vyberte **Správa registrace** a pak vyberte **Převést účet EA na novou registraci**.<br>**Krok 2: Poskytnutí požadovaných informací**<br>Zahrnout:<li>číslo registrace zdroje</li><li> cílové registrační číslo</li><li>datum účinnosti převodu|
| Služba StorSimple z jednoho předplatného do druhého?      | Ne        |    Ano         | Žádné, ruční proces|<li>Migrujte data ze zařízení StorSimple.</li><li>Proveďte obnovení továrního nastavení zařízení, tím se odstraní všechna místní data v zařízení.</li><li>Zaregistrujte zařízení pomocí nového předplatného služby StorSimple Device Manager.</li><li>Migrujte data zpět do zařízení.|
|Můžu převést vlastnictví předplatného Azure do jiného adresáře? | Ano       | Ne       | Přidružení existujícího předplatného k adresáři Azure AD | Viz [Chcete-li přidružit existující předplatné adresáře Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md). Správné zobrazení všech komponent může trvat až 10 minut.|
| StorSimple zařízení z jedné služby StorSimple Device Manager do jiné služby v jiné oblasti?      | Ne        | Ano            | Žádné, ruční proces |Stejně jako výše.|
| Účet úložiště do nového předplatného nebo skupiny prostředků?     | Ano        | Ne             |Přesunutí účtu úložiště do jiného předplatného nebo skupiny prostředků |Po přesunutí, pokud jsou aktualizovány přístupové klíče účtu úložiště, uživatel bude muset nakonfigurovat přístupové klíče ručně pro migrovaný účet úložiště prostřednictvím služby StorSimple Device Manager.|
| Klasický účet úložiště pro účet úložiště Azure Resource Manager      | Ano        | Ne             |Migrace z klasického do Správce prostředků Azure |<li>Podrobné pokyny k migraci účtu úložiště z klasického do Správce prostředků Azure najdete v [mizivém účtu klasického úložiště](../virtual-machines/windows/migration-classic-resource-manager-ps.md#step-52-migrate-a-storage-account).</li><li> Pokud jsou přístupové klíče účtu úložiště aktualizovány po migraci, uživatel bude muset synchronizovat přístupové klíče pro migrovaný účet úložiště prostřednictvím služby StorSimple Device Manager. To je zajistit, že zařízení StorSimple i nadále fungovat normálně a jsou schopni vrstvy primární/zálohování dat do Azure. Podrobné pokyny k synchronizaci přístupových klíčů naleznete v [pracovním postupu Rotace](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts).</li><li> V případě StorSimple Cloud Appliance, pokud je převedena klasický účet úložiště, ale základní virtuální počítač stále zůstává v klasickém, zařízení by mělo fungovat správně. Pokud se migruje základní virtuální počítač pro cloudové zařízení, nebude fungovat funkce deaktivace a odstranění.</li><li> Musíte vytvořit nový StorSimple cloudových zařízení na portálu Azure a pak převzetí služeb při selhání ze starších cloudových zařízení. Nelze vytvořit StorSimple Cloud Appliance na novém portálu Azure pomocí klasického účtu úložiště, musí mít účet úložiště Azure Resource Manager. Další informace naleznete v [najděte nasazení a správu StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).</li>|

## <a name="datacenter-changes"></a>Změny datového centra

| Můžete se pohybovat ...| Podporuje se|Výpadek| Proces podpory Azure| Přístup|
|-----|-----|-----|-----|-----|
| Služba StorSimple z jednoho datového centra Azure do jiného? | Ne | Ano |Žádné, ruční proces  |<li>Migrujte data ze zařízení StorSimple.</li><li>Proveďte obnovení továrního nastavení zařízení, tím se odstraní všechna místní data v zařízení.</li><li>Zaregistrujte zařízení s novým předplatným nové služby StorSimple Device Manager.</li><li>Migrujte data zpět do zařízení.|
| Účet úložiště z jednoho datového centra Azure do druhého? | Ne |Ano  |Žádné, ruční proces  | Stejně jako výše.|

## <a name="next-steps"></a>Další kroky

* [Nasazení služby Správce zařízení StorSimple](storsimple-8000-manage-service.md)
* [Nasazení zařízení řady StorSimple 8000 na webu Azure Portal](storsimple-8000-deployment-walkthrough-u2.md)

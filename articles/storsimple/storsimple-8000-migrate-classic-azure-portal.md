---
title: Migrace StorSimple Správce zařízení účtů úložiště, předplatných
description: Naučte se migrovat předplatná, účty úložiště pro službu StorSimple Správce zařízení.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 523788dc727fc5fa32b2f57250fb738352ff6d31
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96491516"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Migrace předplatných a účtů úložiště přidružených ke službě Správce zařízení StorSimple

Možná budete muset přesunout službu StorSimple k nové registraci nebo k novému předplatnému. Tyto scénáře migrace jsou buď změny účtu nebo změny datového centra. Následující tabulka vám pomůže pochopit, které z těchto scénářů jsou podporované, včetně podrobných kroků k přesunutí.

## <a name="account-changes"></a>Změny účtu

| Můžete přesunout...| Podporováno| Výpadek| Proces podpory Azure| Přístup|
|-----|-----|-----|-----|-----|
| Celé předplatné (zahrnuje službu StorSimple a účty úložiště) k jiné registraci? | Yes       | No       | **Přenos registrace**<br>Použije<li>Když si koupíte nový závazek Azure v rámci nové smlouvy.</li><li>Chcete migrovat všechny účty a předplatné z původního zápisu na nový. To zahrnuje všechny služby Azure v rámci starého předplatného.</li> | **Krok 1: Otevřete lístek podpory Azure Enterprise Operations.**<li>Přejít na [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) .</li><li> Vyberte **Správa** registrací a pak vyberte **přenos z jednoho zápisu na nový zápis**.<br>**Krok 2: zadejte požadované informace.**<br>Připojit<li>zdrojové číslo registrace</li><li> cílové číslo registrace</li><li>přenést datum účinnosti|
| Služba StorSimple z existujícího účtu k nové registraci?    | Yes       | No       | **Přenos účtů**<br>Použije<li>Pokud nechcete provést úplný přenos zápisu.</li><li>Chcete pouze přesunout konkrétní účty do nové registrace.</li>| **Krok 1: Otevřete lístek podpory Azure Enterprise Operations.**<li>Přejít na [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) .</li><li>Vyberte **Správa** registrací a pak vyberte **přenést účet EA do nové registrace**.<br>**Krok 2: zadejte požadované informace.**<br>Připojit<li>zdrojové číslo registrace</li><li> cílové číslo registrace</li><li>přenést datum účinnosti|
| Služba StorSimple z jednoho předplatného do jiného předplatného?      | No        |    Yes         | Žádný, ruční proces|<li>Migrujte data mimo zařízení StorSimple.</li><li>Provede obnovení továrního nastavení zařízení. tím se odstraní všechna místní data v zařízení.</li><li>Zaregistrujte zařízení s novým předplatným služby StorSimple Správce zařízení.</li><li>Migrujte data zpátky do zařízení.|
|Můžu přenášet vlastnictví předplatného Azure do jiného adresáře? | Yes       | No       | Přidružit existující předplatné k adresáři služby Azure AD | Projděte si téma [přidružení existujícího předplatného k adresáři služby Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md). Správné zobrazení všech komponent může trvat až 10 minut.|
| StorSimple zařízení z jedné služby StorSimple Správce zařízení do jiné služby v jiné oblasti?      | No        | Yes            | Žádný, ruční proces |Platí to samé jako výše.|
| Účet úložiště do nového předplatného nebo skupiny prostředků?     | Yes        | No             |Přesunout účet úložiště do jiného předplatného nebo skupiny prostředků |Pokud se po přesunutí aktualizují přístupové klíče účtu úložiště, bude uživatel muset ručně nakonfigurovat přístupové klíče pro migrovaný účet úložiště prostřednictvím služby StorSimple Správce zařízení.|
| Klasický účet úložiště pro účet úložiště Azure Resource Manager      | Yes        | No             |Migrace z modelu Classic na Azure Resource Manager |<li>Podrobné pokyny, jak migrovat účet úložiště z klasického na Azure Resource Manager, najdete v článku [migrace klasického účtu úložiště](../virtual-machines/migration-classic-resource-manager-ps.md#step-52-migrate-a-storage-account).</li><li> Pokud po migraci dojde k aktualizaci přístupových klíčů účtu úložiště, bude muset uživatel synchronizovat přístupové klíče pro migrovaný účet úložiště prostřednictvím služby StorSimple Správce zařízení. K tomu je potřeba zajistit, aby zařízení StorSimple nadále fungovala normálně a byla schopná vrstvy primárních a zálohovaných dat do Azure. Podrobné pokyny týkající se synchronizace přístupových klíčů najdete v tématu [pracovní postup rotace](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts).</li><li> Pokud se v případě StorSimple Cloud Appliance migruje klasický účet úložiště, ale základní virtuální počítač zůstane v klasickém nasazení, zařízení by mělo správně fungovat. Pokud se migruje základní virtuální počítač pro cloudové zařízení, funkce deaktivace a odstranění nebudou fungovat.</li><li> Musíte vytvořit nová StorSimple cloudová zařízení v Azure Portal a pak převzít služby při selhání ze staršího cloudového zařízení. V novém Azure Portal nemůžete vytvořit StorSimple Cloud Appliance pomocí klasického účtu úložiště, musí mít účet úložiště Azure Resource Manager. Další informace najdete na webu [nasazení a správa StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).</li>|

## <a name="datacenter-changes"></a>Změny datového centra

| Můžete přesunout...| Podporováno|Výpadek| Proces podpory Azure| Přístup|
|-----|-----|-----|-----|-----|
| Službu StorSimple z jednoho datacentra Azure do jiného? | No | Yes |Žádný, ruční proces  |<li>Migrujte data mimo zařízení StorSimple.</li><li>Provede obnovení továrního nastavení zařízení. tím se odstraní všechna místní data v zařízení.</li><li>Zaregistrujte zařízení k novému předplatnému novou službu StorSimple Správce zařízení.</li><li>Migrujte data zpátky do zařízení.|
| Účet úložiště z jednoho datacentra Azure do jiného? | No |Yes  |Žádný, ruční proces  | Platí to samé jako výše.|

## <a name="next-steps"></a>Další kroky

* [Nasadit službu StorSimple Správce zařízení](storsimple-8000-manage-service.md)
* [Nasazení zařízení řady StorSimple 8000 v Azure Portal](storsimple-8000-deployment-walkthrough-u2.md)
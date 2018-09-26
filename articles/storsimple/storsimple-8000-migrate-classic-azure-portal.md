---
title: Migrace úložiště účtů, předplatných pro vaši službu Správce zařízení StorSimple | Dokumentace Microsoftu
description: Zjistěte, jak migrovat předplatnými, účty úložiště pro vaše service8000 Správce zařízení StorSimple.
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
ms.date: 06/08/2018
ms.author: alkohli
ms.openlocfilehash: 5a0da47b854e625f2f4a2fcf4c95ec566ba63093
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47095674"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Migrovat předplatná a účty úložiště přidružené služby Správce zařízení StorSimple

Budete muset přesunout služby StorSimple na nový registrace nebo do nového předplatného. Tyto scénáře migrace jsou buď změny účtu nebo datového centra. V následující tabulce použijte k pochopení, které tyto scénáře jsou podporovány, včetně podrobné kroky pro přesun.

## <a name="account-changes"></a>Změny účtu

| Lze je přesunout...| Podporováno| Prostoje| Azure procesu podpory| Přístup|
|-----|-----|-----|-----|-----|
| Celý předplatné (zahrnuje účty služby a úložiště StorSimple) a další registraci? | Ano       | Ne       | **Registrace přenosu**<br>Použití:<li>Při nákupu nových závazků Azure v rámci nové smlouvy.</li><li>Chcete provést migraci všech účtů a předplatných ze staré registrace na nový. To zahrnuje všechny služby Azure v rámci původní předplatného.</li> | **Krok 1: Otevřete lístek podpory operace Azure Enterprise.**<li>Přejděte do [ (Nastavení)http://aka.ms/AzureEntSupport](http://aka.ms/AzureEntSupport) (Integrace a služby).</li><li> Vyberte **Správa registrace** a pak vyberte **přenést z jednoho registrace do nové registrace**.<br>**Krok 2: Zadejte požadované informace**<br>Zahrnout:<li>číslo registrace zdroje</li><li> určení registrační číslo</li><li>Datum účinnosti přenosu|
| Služby StorSimple z existujícího účtu a novou registraci?    | Ano       | Ne       | **Převod účtu**<br>Použití:<li>Pokud nechcete, aby plně zaregistrované přenosu.</li><li>Chcete přesunout specifické účty a novou registraci.</li>| **Krok 1: Otevřete lístek podpory operace Azure Enterprise.**<li>Přejděte do [ (Nastavení)http://aka.ms/AzureEntSupport](http://aka.ms/AzureEntSupport) (Integrace a služby).</li><li>Vyberte **Správa registrace** a pak vyberte **přenést do nové registrace účtu EA**.<br>**Krok 2: Zadejte požadované informace**<br>Zahrnout:<li>číslo registrace zdroje</li><li> určení registrační číslo</li><li>Datum účinnosti přenosu|
| Služby StorSimple z jednoho předplatného do jiného předplatného?      | Ne        |    Ano         | Žádný proces ručního nastavení|<li>Migrace dat mimo zařízení StorSimple.</li><li>Proveďte obnovení do výrobního nastavení zařízení, tím se odstraní všechna místní data na zařízení.</li><li>Registrace zařízení pomocí nového předplatného a služby Správce zařízení StorSimple.</li><li>Migrace dat zpět do zařízení.|
  |Je možné převést vlastnictví předplatného Azure do jiného adresáře? | Ano       | Ne       | Přiřazení existujícího předplatného do adresáře služby Azure AD | Přečtěte si [k přiřazení existujícího předplatného do adresáře služby Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md). Správné zobrazení všech komponent může trvat až 10 minut.|
| Zařízení StorSimple z jedné služby Správce zařízení StorSimple do jiné služby v jiné oblasti?      | Ne        | Ano            | Žádný proces ručního nastavení |Stejné jako výše.|
| Účet úložiště nové předplatné nebo skupinu prostředků?     | Ano        | Ne             |Přesunout na jiné předplatné nebo skupinu prostředků účtu úložiště |Po přesunutí přístupové klíče účtu úložiště jsou aktualizovány, uživatel bude muset nakonfigurovat přístupových klíčů pro účet migrované úložiště ve službě Správce zařízení StorSimple ručně.|
| Klasický účet úložiště do účtu úložiště Azure Resource Manageru      | Ano        | Ne             |Migrace z modelu classic na Azure Resource Manager |<li>Podrobné pokyny o tom, jak migrovat účet úložiště z modelu classic na Azure Resource Manager, přejděte na [migrace účtu klasického úložiště](../virtual-machines/windows/migration-classic-resource-manager-ps.md#step-62-migrate-a-storage-account).</li><li> Pokud přístupové klíče účtu úložiště se aktualizují po migraci, uživatel bude třeba synchronizovat přístupových klíčů pro účet úložiště migrované prostřednictvím služby Správce zařízení StorSimple. Je to z toho zařízení StorSimple dál normálně fungovat a budou moct vrstvení primární/zálohování dat do Azure. Podrobné pokyny pro synchronizaci klíčů přístup, přejděte na [pracovního postupu otočení](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts).</li><li> V případě řešení StorSimple Cloud Appliance Pokud je migrovat klasický účet úložiště, ale základní virtuální počítač stále zůstává v modelu classic, zařízení by měl fungovat správně. Pokud základní virtuální počítač pro cloudové zařízení je migrován, nebudou fungovat funkce deaktivace a odstranění.</li><li> Musíte vytvořit nové řešení StorSimple Cloud Appliance na webu Azure Portal a převzetí služeb při selhání ze starší cloud Appliance. Nelze vytvořit řešení StorSimple Cloud Appliance v novém portálu Azure portal pomocí účtu klasického úložiště, musí mít účet úložiště Azure Resource Manageru. Další informace najdete v části [nasadit a spravovat řešení StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).</li>|Stejné jako výše.|

## <a name="datacenter-changes"></a>Změny datového centra

| Lze je přesunout...| Podporováno|Prostoje| Azure procesu podpory| Přístup|
|-----|-----|-----|-----|-----|
| Služby StorSimple do jiného z jednoho datového centra Azure? | Ne | Ano |Žádný proces ručního nastavení  |<li>Migrace dat mimo zařízení StorSimple.</li><li>Proveďte obnovení do výrobního nastavení zařízení, tím se odstraní všechna místní data na zařízení.</li><li>Zařízení zaregistrujte nové předplatné k nové službě Správce zařízení StorSimple.</li><li>Migrace dat zpět do zařízení.|
| Účet úložiště z jednoho datového centra Azure do jiného? | Ne |Ano  |Žádný proces ručního nastavení  | Stejné jako výše.|

## <a name="next-steps"></a>Další postup

* [Nasazení služby Správce zařízení StorSimple](storsimple-8000-manage-service.md)
* [Nasazení zařízení StorSimple řady 8000 na webu Azure portal](storsimple-8000-deployment-walkthrough-u2.md)

---
title: Migrace účtů úložiště, odběry služby Správce zařízení StorSimple | Microsoft Docs
description: Zjistěte, jak migrovat odběry, účty úložiště pro vaše service8000 Správce zařízení StorSimple.
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
ms.openlocfilehash: 299029e10ac8f0235bb11876aa00c98a21183c0f
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248788"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Migrace odběry a účty úložiště přidružené služby StorSimple Manager zařízení

Potřebujete přesunout služby StorSimple nový registrace nebo do nového předplatného. Tyto scénáře migrace jsou buď změny účtu nebo datacenter. V následující tabulce slouží k pochopení, která z těchto scénářů jsou podporovány, včetně podrobné kroky, chcete-li přesunout.

## <a name="account-changes"></a>Změny účtu

| Můžete přesunete...| Podporováno| Prostoje| Azure proces podpory| Přístup|
|-----|-----|-----|-----|-----|
| Celý předplatné (zahrnuje StorSimple účty služby a úložiště) a jiné registraci? | Ano       | Ne       | **Registrace přenosu**<br>Použití:<li>Když si zakoupit nové Azure závazků v rámci nové smlouvy.</li><li>Chcete migrovat všechny účty a odběry od původního registrace do nového. To zahrnuje všech služeb Azure v rámci původního předplatného.</li> | **Krok 1: Otevřete na lístek podpory operace Azure Enterprise.**<li>Přejděte do [ (Nastavení)http://aka.ms/AzureEntSupport](http://aka.ms/AzureEntSupport) (Integrace a služby).</li><li> Vyberte **registrace správy** a pak vyberte **přenést z jednoho registrace na nový registrace**.<br>**Krok 2: Zadejte požadované informace**<br>Patří:<li>Zdroj registrační číslo</li><li> Cílový registrační číslo</li><li>Datum účinnosti přenosu|
| Služba StorSimple z existující účet novou registraci?    | Ano       | Ne       | **Účet přenosu**<br>Použití:<li>Pokud nechcete, aby přenos úplná registrace.</li><li>Chcete přesunout konkrétní účty do nové registrace.</li>| **Krok 1: Otevřete na lístek podpory operace Azure Enterprise.**<li>Přejděte do [ (Nastavení)http://aka.ms/AzureEntSupport](http://aka.ms/AzureEntSupport) (Integrace a služby).</li><li>Vyberte **registrace správy** a pak vyberte **přenosu účet EA novou registraci**.<br>**Krok 2: Zadejte požadované informace**<br>Patří:<li>Zdroj registrační číslo</li><li> Cílový registrační číslo</li><li>Datum účinnosti přenosu|
| Služba StorSimple z jedno předplatné do jiného předplatného?      | Ne        |    Ano         | NONE, proces ručního nastavení|<li>Migrujte data mimo zařízení StorSimple.</li><li>Provádět obnovení továrního nastavení zařízení, to odstraní všechna místní data na zařízení.</li><li>Registrace zařízení s nové předplatné služby StorSimple Manager zařízení.</li><li>Migrujte data zpět do zařízení.|
  |Můžete do jiného adresáře přenos vlastnictví předplatného Azure? | Ano       | Ne       | Přidružení stávající předplatné do adresáře Azure AD | Odkazovat [přidružení stávající předplatné do adresáře Azure AD](../active-directory/active-directory-how-subscriptions-associated-directory.md). Správné zobrazení všech komponent může trvat až 10 minut.|
| Zařízení StorSimple z jedné služby StorSimple Manager zařízení k jiné službě v jiné oblasti?      | Ne        | Ano            | NONE, proces ručního nastavení |Stejné jako výše.|
| Účet úložiště do nové skupiny předplatné nebo prostředek?     | Ano        | Ne             |Účet úložiště přesunout do jiné předplatné nebo prostředek skupiny |Po přesunu Pokud jsou aktualizovány přístupové klíče účtu úložiště, uživatel bude muset nakonfigurovat přístupové klíče pro účet úložiště migrované prostřednictvím služby StorSimple Manager zařízení ručně.|
| Účet úložiště Classic k účtu úložiště Azure Resource Manager      | Ano        | Ne             |Migrace z klasického do Azure Resource Manageru |<li>Podrobné pokyny k migraci účtu úložiště z classic do Azure Resource Manageru, přejděte na [migraci účtu úložiště classic](../virtual-machines/windows/migration-classic-resource-manager-ps.md#step-62-migrate-a-storage-account).</li><li> Pokud přístupové klíče účtu úložiště se aktualizují po migraci, uživatel bude muset synchronizovat přístupové klíče pro účet úložiště migrované prostřednictvím služby StorSimple Manager zařízení. Tím je zajištěno zařízení StorSimple i nadále fungovat normálně a můžou vrstvy primární nebo zálohování dat do Azure. Podrobné pokyny týkající se synchronizace přístupové klíče, přejděte na [pracovního postupu otočení](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts).</li><li> V případě o cloudu zařízení StorSimple Pokud je účet úložiště classic migrovat, ale základní virtuální počítač stále zůstává v klasickém, zařízení by měly fungovat správně. Pokud je základní virtuální počítač pro zařízení cloudu migrovat, nebudou fungovat funkce deaktivace a odstranění.</li><li> Musíte vytvořit nové zařízení cloudu StorSimple na portálu Azure a pak převzetí služeb při selhání ze starší zařízení cloudu. Nelze vytvořit o cloudu zařízení StorSimple v nový portál Azure classic úložiště účtem, potřebují mít účet úložiště Azure Resource Manager. Další informace, přejděte na [nasadit a spravovat zařízení s StorSimple cloudu](storsimple-8000-cloud-appliance-u2.md).</li>|Stejné jako výše.|

## <a name="datacenter-changes"></a>Změny datového centra

| Můžete přesunete...| Podporováno|Prostoje| Azure proces podpory| Přístup|
|-----|-----|-----|-----|-----|
| Služba StorSimple na jiný z jednoho datového centra Azure? | Ne | Ano |NONE, proces ručního nastavení  |<li>Migrujte data mimo zařízení StorSimple.</li><li>Provádět obnovení továrního nastavení zařízení, to odstraní všechna místní data na zařízení.</li><li>Registrace zařízení s nové předplatné na novou službu StorSimple Manager zařízení.</li><li>Migrujte data zpět do zařízení.|
| Účet úložiště do druhého z jednoho datového centra Azure? | Ne |Ano  |NONE, proces ručního nastavení  | Stejné jako výše.|

## <a name="next-steps"></a>Další postup

* [Nasazení služby StorSimple Manager zařízení](storsimple-8000-manage-service.md)
* [Nasazení zařízení řady StorSimple 8000 na portálu Azure](storsimple-8000-deployment-walkthrough-u2.md)

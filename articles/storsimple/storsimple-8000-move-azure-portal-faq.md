---
title: Přesunutí ze klasický portál Azure – nejčastější dotazy | Microsoft Docs
description: Poskytuje odpovědi na nejčastější dotazy o přesunutí zařízení StorSimple ze klasického portálu Azure.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: ac57894e4f180f42f80479d2031f4dd5ddfdb1be
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2018
ms.locfileid: "27785175"
---
# <a name="move-storsimple-device-manager-service-from-classic-to-azure-portal-frequently-asked-questions-faq"></a>Přesunuty služby StorSimple Manager zařízení z klasického portálu Azure: Nejčastější dotazy (FAQ)

## <a name="overview"></a>Přehled

Dále jsou otázky a odpovědi, které mohou mít při přesunutí služby StorSimple Manager zařízení spuštěná na portálu Azure classic k portálu Azure.

Otázky a odpovědi jsou uspořádány do následujících kategorií:

* Přesunutí služby StorSimple Manager zařízení
* Přesunutí účty úložiště
* Rutiny založené na pomocí Azure Resource Manager
* Pomocí služby StorSimple Manager dat
* Různé

## <a name="moving-storsimple-device-manager-service"></a>Přesunutí služby StorSimple Manager zařízení

### <a name="once-i-have-moved-to-azure-portal-can-i-still-create-a-storsimple-manager-service-in-the-classic-portal"></a>Jednou I byl přesunut do portálu Azure, lze přesto vytvořit službu StorSimple Manager na portálu classic?

Ne. Po přesunutí služby StorSimple Manager k portálu Azure, nelze vytvořit novou službu na portálu classic. Navíc [portálu classic nebudete mít k dispozici z 8 leden 2018](https://azure.microsoft.com/updates/azure-portal-updates-for-classic-portal-users). 

### <a name="i-have-multiple-storsimple-managers-running-in-the-classic-portal-can-i-choose-which-ones-to-move-to-the-azure-portal"></a>Došlo k několika správci StorSimple spuštění portálu classic. Můžete zvolit ty, které chcete přesunout na portál Azure?

Ne. Nemůžete vybrat, které správci StorSimple přesunout do portálu Azure. Přesunou se všechny správce zařízení StorSimple v rámci svého předplatného.


### <a name="i-initiated-the-migration-of-my-service-to-the-new-azure-portal-should-i-delete-the-storsimple-manager-from-the-classic-portal"></a>I inicializovat migrace Moje služby na nový portál Azure. Měli z klasického portálu odstranit StorSimple Manager? 

Ne. Nepokoušejte se odstranit jakoukoli službu, která jste přesunuli na klasickém portálu. Čekat na dokončení migrace, jakmile se všechny správce StorSimple byl přesunut do nového portálu, nebude muset odstranit všechny služby z klasického portálu. Nakonec je zastaralý portálu classic.

### <a name="can-i-rename-my-storsimple-device-manager-service-in-the-azure-portal"></a>Můžete přejmenovat mé služby StorSimple Manager zařízení na portálu Azure?

Ne. Název služby nelze změnit po vytvoření službu na portálu Azure. Stejné chování platí také pro ostatní entity, jako jsou zařízení, svazky, kontejnery svazků a zásady zálohování.

### <a name="can-i-create-the-80108020-storsimple-cloud-appliances-with-azure-resource-manager-deployment-model"></a>Můžete vytvořit zařízení 8010/8020 StorSimple cloudu pomocí modelu nasazení Azure Resource Manager?

Ano. Můžete vytvořit nové 8010/8020 StorSimple cloudu zařízení v modelu nasazení Azure Resource Manager. Základní virtuální počítače pro tyto cloudové zařízení je taky v modelu nasazení Resource Manager.

### <a name="when-we-migrate-subscriptions-to-the-azure-portal-will-the-underlying-vms-for-the-storsimple-cloud-appliances-also-migrate-to-azure-resource-management-deployment-model"></a>Když jsme migraci odběry na portál Azure, bude základní virtuálních počítačů pro zařízení cloudu StorSimple taky migrovat do modelu nasazení správou prostředků Azure?

Přesunutí služby StorSimple je nezávislé na správu virtuálních počítačů pro zařízení cloudu StorSimple. Můžete plně spravovat virtuální počítače pro zařízení StorSimple cloudu v classic i na portálu Azure ještě dnes.

### <a name="can-i-manage-existing-classic-80108020-storsimple-cloud-appliance-from-the-azure-portal"></a>Můžete spravovat existující classic zařízení cloudu StorSimple 8010/8020 z portálu Azure?

Ano. Virtuální počítače spojené s stávajících zařízení 8010/8020 cloudu lze spravovat z portálu Azure.

Pokud jste vytvořili zařízení StorSimple cloudu modelu 8010/8020 spuštění aktualizace 3.0 a vyšší, nejsou ovlivněny služby Přesun do nového portálu Azure. Byste měli mít plně spravovat svoje zařízení cloudu bez problémů. 

Pokud máte cloudu zařízení se systémem verze starší než Update 3.0 na portálu classic, pak pouze máte omezený počet funkce je k dispozici. Další informace naleznete [seznam nepodporované operací pro zařízení se systémem verze starší než Update 3](storsimple-8000-manage-service.md##supported-operations-on-devices-running-versions-prior-to-update-50).

Cloudové zařízení nelze aktualizovat. Použijte nejnovější verzi softwaru k vytvoření nové zařízení cloudu a potom převzít existující kontejnery svazků pro vytvoření nové zařízení cloudu. Další informace, přejděte na [převzetí služeb při selhání zařízení cloudu](storsimple-8000-cloud-appliance-u2.md#fail-over-to-the-cloud-appliance)


### <a name="my-storsimple-8000-series-device-is-running-update-20-i-migrated-my-service-to-new-azure-portal-my-device-connected-successfully-but-it-seems-that-i-am-not-able-to-fully-manage-my-device-how-do-i-resolve-this-behavior"></a>Moje zařízení řady StorSimple 8000 je spuštěna aktualizace 2.0. Můžu migrovat mé služby na nový portál Azure. Moje zařízení připojené úspěšně, ale zdá se, že nejsem plně spravovat svoje zařízení. Jak tento problém vyřešit?

Nový portál Azure je podporován pouze u zařízení StorSimple se systémem aktualizace 3.0 a vyšší. Pokud zařízení byla spuštěna aktualizace 2.0, můžete pouze omezená funkce je k dispozici pro toto zařízení. Další informace naleznete [seznam nepodporované operací pro zařízení se systémem verze starší než Update 3](storsimple-8000-manage-service.md##supported-operations-on-devices-running-versions-prior-to-update-50).

Plně spravovat zařízení, nainstalujte nejnovější aktualizace na vašem zařízení. Další informace, přejděte na [instalaci aktualizací 5](storsimple-8000-install-update-5.md).

### <a name="i-just-moved-my-storsimple-manager-service-to-the-azure-portal-i-am-seeing-some-alerts-related-to-my-device-is-this-behavior-related-to-the-move"></a>Po přesunutí právě mé služby StorSimple Manager na portálu Azure. Některé výstrahy související s své zařízení se zobrazují. Je toto chování související s přesunutí?

Ne. Přesunutí samotné by se neměly zobrazit chyby nebo výstrahy. Postupujte podle doporučení výstrahy vyřešit výstrahy.

### <a name="i-am-using-a-storsimple-50007000-series-device-are-these-also-supported-in-the-azure-portal"></a>Používám zařízení řady StorSimple 5000/7000. Jsou tyto podporováno také v portálu Azure?

Ne. Řadu zařízení StorSimple 5000/7000 nejsou podporovány na portálu Azure.

### <a name="i-am-planning-to-migrate-data-from-storsimple-50007000-series-device-to-a-storsimple-8000-series-device-how-does-moving-a-service-to-azure-portal-impact-my-data-migration"></a>I mě plánování pro migraci dat ze zařízení řady StorSimple 5000/7000 zařízení řady StorSimple 8000. Do jaké míry přesunutí službu na portálu Azure ovlivňuje moje migrace dat? 

Můžete migrovat data ze zařízení řady StorSimple 5000/7000 zařízení řady StorSimple 8000 se systémem na portálu Azure. Pokud chcete povolit můžete pro migraci dat z řad 5000/7000 řady 8000, musíte [protokolu lístek podpory s Microsoft Support](storsimple-8000-contact-microsoft-support.md).


## <a name="moving-subscriptions-storage-accounts-resource-groups"></a>Přesunutí odběry, účty úložiště, skupiny prostředků

### <a name="can-i-move-storsimple-device-manager-from-one-subscription-to-another-subscription-in-the-azure-portal"></a>Lze přesunout Správce zařízení StorSimple z jedno předplatné do jiného předplatného na portálu Azure?

Ne. Přesunutí služby StorSimple Manager zařízení ze jedno předplatné není podporována. Můžete provést ruční proces, který se skládá z následujících kroků:

* Migrujte data mimo zařízení StorSimple.
* Provádět obnovení továrního nastavení zařízení, to odstraní všechna místní data na zařízení.
* Registrace zařízení s nové předplatné služby StorSimple Manager zařízení.
* Migrujte data zpět do zařízení.

### <a name="do-i-have-to-migrate-the-storage-account-to-azure-resource-manager-deployment-model"></a>Je nutné migrovat účet úložiště do modelu nasazení Azure Resource Manager?

Ne. Účty úložiště classic může plně spravovat z portálu Azure. Když přesouváte služby StorSimple na portál Azure, váš účet úložiště bude nadále fungovat jako předtím.

### <a name="what-happens-to-the-storage-account-after-the-service-is-migrated-to-the-azure-portal"></a>Co se stane k účtu úložiště po migraci službu na portálu Azure?

Přesunutí služby není v relaci na správu účtu úložiště. Classic a účty úložiště Azure Resource Manager může plně spravovat na portálu Azure. Jakmile na portál Azure přesouváte služby, zařízení by měly být nadále spustit s tímto účtem úložiště a by měl mít žádný vliv na vaše data.

### <a name="can-i-migrate-storsimple-device-manager-from-one-resource-group-to-another"></a>Můžete migrovat StorSimple Manager zařízení z jedné skupiny prostředků do jiného?

Ne. Nelze přesunout Správce zařízení StorSimple vytvářeny pomocí jedné skupiny prostředků do jiné skupiny prostředků.

## <a name="using-azure-resource-manager-based-cmdlets"></a>Rutiny založené na pomocí Azure Resource Manager

### <a name="i-moved-to-the-new-azure-portal-now-my-scripts-based-on-azure-classic-deployment-model-powershell-cmdlets-are-not-working-what-do-i-need-to-do"></a>Po přesunutí na nový portál Azure. Nyní nejsou funkční Moje skripty založené na rutiny prostředí PowerShell modelu nasazení Azure classic? Co je potřeba udělat?

Existující rutiny prostředí PowerShell modelu nasazení Azure classic nejsou podporovány na portálu Azure. Skripty ke správě svých zařízení prostřednictvím Správce Azure Resource Manager aktualizujte. Další informace o aktualizaci skripty, přejděte na [ukázky pro nový portál Azure](https://github.com/anoobbacker/storsimpledevicemgmttools).

### <a name="i-just-moved-to-the-azure-portal-and-needed-to-roll-over-the-service-data-encryption-key-where-is-this-option-in-the-azure-portal"></a>I právě přesunout na portálu Azure a potřebné k obnovení přes šifrovacího klíče dat služby. Kde je tato možnost v portálu Azure?

Možnost rozšíří šifrovacího klíče dat služby není v portálu Azure. Další informace o tom, jak se tato změna na portálu Azure, přejděte na [změnit šifrovacího klíče dat služby](storsimple-8000-manage-service.md#change-the-service-data-encryption-key).

### <a name="i-am-using-windows-powershell-for-storsimple-cmdlets-on-the-storsimple-device-to-perform-operations-such-extract-a-support-package-are-these-cmdlets-affected-when-i-move-to-the-new-azure-portal"></a>Používám pomocí prostředí Windows PowerShell pro StorSimple rutiny v zařízení StorSimple k provádění operací takové extrakce balíčku pro podporu. Jsou tyto rutiny ovlivněné v případě přesunout na nový portál Azure?

Ne. S vaší služby přesunu na nový portál Azure by měl být žádný vliv na prostředí Windows PowerShell pro StorSimple rutiny související s místního zařízení StorSimple (které nemá vliv přesunutí). Můžete použít tyto rutiny k vytvoření balíčku pro podporu bez problémů i na portálu Azure. Tento přesun má vliv pouze rutin prostředí PowerShell pro nasazení Azure classic modelu.

## <a name="moving-storsimple-data-manager-service"></a>Přesunutí služby StorSimple Manager dat

### <a name="i-am-using-storsimple-data-manager-service-in-classic-azure-portal-how-should-i-proceed-with-this-move"></a>Používám služby StorSimple Manager dat na portálu Azure classic. Jak je by měl tento přesun pokračovat?

Pokud používáte službu StorSimple Manager dat, byla automaticky přesunete na portál Azure.

## <a name="miscellaneous"></a>Různé

### <a name="i-am-running-storsimple-snapshot-manager-for-my-8000-series-device-is-there-any-impact-on-storsimple-snapshot-manager-when-i-move-to-azure-portal"></a>Používám StorSimple Snapshot Manager pro své zařízení řady 8000. Je k dispozici žádný vliv na StorSimple Snapshot Manager při přecházení k portálu Azure?

Ne. Neexistuje žádný vliv na StorSimple Snapshot Manager při přesunutí služby k portálu Azure. Vaše zařízení a StorSimple Snapshot Manager i nadále fungovat jako předtím.

### <a name="can-i-rename-my-storsimple-device-volume-containers-or-volumes"></a>Můžete přejmenovat Moje zařízení StorSimple, kontejnery svazků nebo svazků?

Ne. Nelze přejmenovat zařízení, svazky, kontejnery svazků nebo zásady zálohování na portálu Azure.

## <a name="next-steps"></a>Další kroky

Další informace o [podporována operací na zařízení se systémem starším před aktualizací 5.0](storsimple-8000-manage-service.md#supported-operations-on-devices-running-versions-prior-to-update-50).




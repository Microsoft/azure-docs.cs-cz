---
title: Nasazení služby StorSimple Správce zařízení v Azure | Microsoft Docs
description: Přečtěte si o krocích potřebných k vytvoření, odstranění, migraci služby a správě registračního klíče služby.
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
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 85d66b5e4f7e95d114cbf0b4c681d73eebb93a67
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076560"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>Nasazení služby StorSimple Správce zařízení pro zařízení řady StorSimple 8000

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Přehled

Služba StorSimple Správce zařízení běží v Microsoft Azure a připojuje se k více zařízením StorSimple. Po vytvoření služby ji můžete použít ke správě všech zařízení, která jsou připojená ke službě StorSimple Správce zařízení, z jediného centrálního umístění, což minimalizuje administrativní zatížení.

Tento kurz popisuje kroky potřebné k vytvoření, odstranění, migraci služby a správě registračního klíče služby. Informace obsažené v tomto článku se vztahují jenom na zařízení řady StorSimple 8000. Další informace o virtuálních polích StorSimple najdete v [nasazení služby StorSimple Správce zařízení pro virtuální pole StorSimple](storsimple-virtual-array-manage-service.md).

> [!NOTE]
> -  Azure Portal podporuje zařízení se systémem Update 5,0 nebo novějším. Pokud zařízení není v aktuálním stavu, nainstalujte aktualizaci 5 hned. Další informace najdete na webu [instalace aktualizace Update 5](storsimple-8000-install-update-5.md). 
> - Pokud používáte StorSimple Cloud Appliance (8010/8020), nemůžete aktualizovat cloudové zařízení. Použijte nejnovější verzi softwaru k vytvoření nového cloudového zařízení s aktualizací 5,0 a potom převzetí služeb při selhání nově vytvořeného cloudového zařízení. 
> - Všechna zařízení se systémem Update 4,0 nebo starším budou mít omezenou funkčnost správy. 

## <a name="create-a-service"></a>Vytvoření služby
Chcete-li vytvořit službu StorSimple Správce zařízení, budete potřebovat:

* Předplatné s smlouva Enterprise
* Účet úložiště Active Microsoft Azure
* Fakturační informace, které se používají pro správu přístupu

Jsou povoleny pouze odběry s smlouva Enterprise. Při vytváření služby můžete také zvolit, že se má vygenerovat výchozí účet úložiště.

Jedna služba může spravovat více zařízení. Zařízení ale nemůže zahrnovat víc služeb. Velký podnik může mít více instancí služby pro práci s různými předplatnými, organizacemi nebo i umístěními nasazení. 

> [!NOTE]
> Abyste mohli spravovat zařízení řady StorSimple 8000 a virtuální pole StorSimple, potřebujete samostatné instance služby StorSimple Správce zařízení.

Chcete-li vytvořit službu, proveďte následující kroky.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Pro každou službu StorSimple Správce zařízení existují následující atributy:

* **Název** – název, který byl přiřazen službě StorSimple Správce zařízení při vytvoření. **Po vytvoření služby se název služby nedá změnit. Platí to taky pro jiné entity, jako jsou zařízení, svazky, kontejnery svazků a zásady zálohování, které se nedají přejmenovat v Azure Portal.**
* **Stav** – stav služby, který může být **aktivní**, **vytváří** nebo je **online**.
* **Location (umístění** ) – zeměpisné umístění, ve kterém bude nasazeno zařízení StorSimple.
* **Předplatné** – fakturační předplatné, které je přidružené k vaší službě.

## <a name="delete-a-service"></a>Odstranění služby

Před odstraněním služby se ujistěte, že ji nepoužívají žádná připojená zařízení. Pokud se služba používá, deaktivujte připojená zařízení. Operace deaktivace naruší spojení mezi zařízením a službou, ale zachová data zařízení v cloudu.

> [!IMPORTANT]
> Po odstranění služby nelze operaci vrátit zpět. Všechna zařízení, která službu používala, je nutné obnovit do výchozího továrního nastavení, aby je bylo možné použít s jinou službou. V tomto scénáři se ztratí místní data na zařízení i konfigurace.

Chcete-li odstranit službu, proveďte následující kroky.

### <a name="to-delete-a-service"></a>Odstranění služby

1. Vyhledejte službu, kterou chcete odstranit. Klikněte na ikonu **prostředky** a potom zadejte příslušné výrazy, které chcete vyhledat. Ve výsledcích hledání klikněte na službu, kterou chcete odstranit.

    ![Vyhledat službu, která se má odstranit](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Tím přejdete do okna StorSimple Správce zařízení Service. Klikněte na **Odstranit**.

    ![Odstranit službu](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. V potvrzovacím oznámení klikněte na **Ano** . Odstranění služby může trvat několik minut.

    ![Potvrzení odstranění](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Získání registračního klíče služby

Po úspěšném vytvoření služby budete muset zaregistrovat zařízení StorSimple ve službě. K registraci prvního zařízení StorSimple budete potřebovat registrační klíč služby. K registraci dalších zařízení pomocí existující služby StorSimple potřebujete registrační klíč a šifrovací klíč dat služby (který se při registraci generuje na prvním zařízení). Další informace o šifrovacím klíči dat služby najdete v tématu [zabezpečení StorSimple](storsimple-8000-security.md). Registrační klíč můžete získat přístupem k **klíčům** v okně StorSimple Správce zařízení.

K získání registračního klíče služby proveďte následující kroky.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

Zachovejte registrační klíč služby v bezpečném umístění. K registraci dalších zařízení pomocí této služby budete potřebovat tento klíč a také šifrovací klíč dat služby. Po získání registračního klíče služby musíte zařízení nakonfigurovat prostřednictvím rozhraní Windows PowerShell pro StorSimple.

Podrobnosti o používání tohoto registračního klíče najdete v části [Krok 3: konfigurace a registrace zařízení prostřednictvím Windows PowerShell pro StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Znovu vygenerovat registrační klíč služby
Registrační klíč služby je nutné znovu vygenerovat, pokud potřebujete provést střídání klíčů nebo pokud se změnil seznam správců služeb. Když znovu vygenerujete klíč, použije se nový klíč jenom k registraci následných zařízení. Zařízení, která jsou už zaregistrovaná, neovlivní tento proces.

K opětovnému vygenerování registračního klíče služby proveďte následující kroky.

### <a name="to-regenerate-the-service-registration-key"></a>Opětovné vygenerování registračního klíče služby
1. V okně **StorSimple Správce zařízení** otevřete okno **klíče** pro **správu &gt;** .
    
    ![Přejít k oknu klíče](./media/storsimple-8000-manage-service/regenregkey2.png)

2. V okně **klíče** klikněte na **znovu vygenerovat**.

    ![Kliknout na znovu vygenerovat](./media/storsimple-8000-manage-service/regenregkey3.png)
3. V okně **znovu vygenerovat registrační klíč služby** si Projděte akci potřebnou při opětovném vygenerování klíčů. Všechna následující zařízení, která jsou zaregistrovaná pomocí této služby, používají nový registrační klíč. Potvrďte kliknutím na **znovu vygenerovat** . Po dokončení obnovení se zobrazí oznámení.

    ![Potvrdit opětovné vygenerování](./media/storsimple-8000-manage-service/regenregkey4.png)

4. Zobrazí se nový registrační klíč služby.

5. Zkopírujte tento klíč a uložte ho pro registraci všech nových zařízení s touto službou.



## <a name="change-the-service-data-encryption-key"></a>Změna šifrovacího klíče dat služby
[!INCLUDE [storage-files-migration-generate-key](../../includes/storage-files-migration-generate-key.md)]

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Podporované operace na zařízeních s verzemi staršími než aktualizace 5,0
V Azure Portal se podporují jenom zařízení StorSimple, která používají aktualizaci 5,0 a vyšší. Zařízení, na kterých běží starší verze, mají omezená podpora. Po dokončení migrace na Azure Portal použijte následující tabulku, která vám pomůže pochopit, které operace jsou podporované na zařízeních s verzemi staršími než aktualizace 5,0.

| Operace                                                                                                                       | Podporováno      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Registrace zařízení                                                                                                               | Yes            |
| Konfigurace nastavení zařízení, jako jsou obecné, síť a zabezpečení                                                                | Yes            |
| Hledání, stahování a instalace aktualizací                                                                                             | Yes            |
| Deaktivovat zařízení                                                                                                               | Yes            |
| Odstranění zařízení                                                                                                                   | Yes            |
| Vytvoření, úprava a odstranění kontejneru svazků                                                                                   | No             |
| Vytvoření, úprava a odstranění svazku                                                                                             | No             |
| Vytváření, úpravy a odstraňování zásad zálohování                                                                                      | No             |
| Provedení ručního zálohování                                                                                                            | No             |
| Proveďte naplánované zálohování.                                                                                                         | Neuvedeno |
| Obnovení ze sady záloh                                                                                                        | No             |
| Klonování na zařízení se systémem Update 3,0 a novějším <br> Na zdrojovém zařízení je spuštěná verze před aktualizací 3,0.                                | Yes            |
| Klonování na zařízení s verzemi staršími než aktualizace 3,0                                                                          | No             |
| Převzetí služeb při selhání jako zdrojové zařízení <br> (ze zařízení, na kterém běží verze před aktualizací 3,0 na zařízení se systémem Update 3,0 a novějším)                                                               | Yes            |
| Převzetí služeb při selhání jako cílové zařízení <br> (do zařízení, na kterém běží verze softwaru před aktualizací 3,0)                                                                                   | No             |
| Vymazat výstrahu                                                                                                                  | Yes            |
| Zobrazit zásady zálohování, katalog záloh, svazky, kontejnery svazků, grafy monitorování, úlohy a výstrahy vytvořené na portálu Classic | Yes            |
| Zapnutí a vypnutí řadičů zařízení                                                                                              | Ano            |


## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [procesu nasazení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* Přečtěte si další informace o [správě účtu úložiště StorSimple](storsimple-8000-manage-storage-accounts.md).
* Přečtěte si další informace o tom, jak [používat službu StorSimple Správce zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

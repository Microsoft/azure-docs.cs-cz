---
title: Nasazení služby StorSimple Device Manager v Azure | Dokumenty společnosti Microsoft
description: Vysvětluje, jak vytvořit a odstranit službu StorSimple Device Manager na webu Azure Portal a popisuje, jak spravovat registrační klíč služby.
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
ms.openlocfilehash: 1e75acc03209fdd7e613801c9152f24aaecfa6de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267778"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>Nasazení služby StorSimple Device Manager pro zařízení řady StorSimple 8000

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Přehled

Služba StorSimple Device Manager běží v Microsoft Azure a připojuje se k více zařízením StorSimple. Po vytvoření služby ji můžete použít ke správě všech zařízení, která jsou připojena ke službě StorSimple Device Manager z jednoho centrálního umístění, čímž se minimalizuje administrativní zátěž.

Tento kurz popisuje kroky potřebné pro vytvoření, odstranění, migraci služby a správu registračního klíče služby. Informace obsažené v tomto článku se vztahují pouze na zařízení řady StorSimple 8000. Další informace o virtuálních polích StorSimple získáte k [nasazení služby StorSimple Device Manager pro vaše virtuální pole StorSimple](storsimple-virtual-array-manage-service.md).

> [!NOTE]
> -  Portál Azure podporuje zařízení se spuštěnou aktualizací 5.0 nebo novější. Pokud vaše zařízení není aktuální, nainstalujte aktualizaci 5 okamžitě. Další informace naleznete v části [Instalace aktualizace 5](storsimple-8000-install-update-5.md). 
> - Pokud používáte StorSimple Cloud Appliance (8010/8020), cloudové zařízení nelze aktualizovat. Pomocí nejnovější verze softwaru vytvořte nové cloudové zařízení s aktualizací 5.0 a pak přejimítejte převzetím služeb při selhání s novým vytvořeným cloudovým zařízením. 
> - Všechna zařízení se systémem Update 4.0 nebo starší budou mít sníženou funkčnost správy. 

## <a name="create-a-service"></a>Vytvoření služby
Chcete-li vytvořit službu Správce zařízení StorSimple, musíte mít:

* Předplatné se smlouvou Enterprise
* Aktivní účet úložiště Microsoft Azure
* Fakturační údaje, které se používají pro správu přístupu

Povolena jsou pouze předplatná se smlouvou Enterprise. Můžete také vygenerovat výchozí účet úložiště při vytváření služby.

Jedna služba může spravovat více zařízení. Zařízení však nemůže span více služeb. Velký podnik může mít více instancí služeb pro práci s různými předplatnými, organizacemi nebo dokonce umístěními nasazení. 

> [!NOTE]
> Ke správě zařízení řady StorSimple 8000 a virtuálních polí StorSimple potřebujete samostatné instance služby StorSimple Device Manager.

Chcete-li vytvořit službu, proveďte následující kroky.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Pro každou službu StorSimple Device Manager existují následující atributy:

* **Název** – název, který byl přiřazen k vaší službě Správce zařízení StorSimple při jeho vytvoření. **Název služby nelze po vytvoření služby změnit. To platí také pro další entity, jako jsou zařízení, svazky, kontejnery svazků a zásady zálohování, které nelze přejmenovat na webu Azure Portal.**
* **Stav** – Stav služby, která může být **aktivní**, **vytváření**nebo **online**.
* **Umístění** – geografické umístění, ve kterém bude nasazeno zařízení StorSimple.
* **Předplatné** – fakturační předplatné, které je přidruženo k vaší službě.

## <a name="delete-a-service"></a>Odstranění služby

Před odstraněním služby zkontrolujte, zda ji nepoužívají žádná připojená zařízení. Pokud je služba používána, deaktivujte připojená zařízení. Operace deaktivace přeruší spojení mezi zařízením a službou, ale zachová data zařízení v cloudu.

> [!IMPORTANT]
> Po odstranění služby nelze operaci vrátit zpět. Jakékoli zařízení, které službu používalo, musí být před použitím jiné služby resetováno na výchozí hodnoty z výroby. V tomto scénáři je ztracena místní data v zařízení, stejně jako konfigurace.

Chcete-li odstranit službu, proveďte následující kroky.

### <a name="to-delete-a-service"></a>Odstranění služby

1. Vyhledejte službu, kterou chcete odstranit. Klikněte na ikonu **Zdroje** a zadejte příslušné termíny pro vyhledávání. Ve výsledcích hledání klikněte na službu, kterou chcete odstranit.

    ![Vyhledávací služba, která má být odstraněna](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Tím přejdete do okna služby Správce zařízení StorSimple. Klepněte na **tlačítko Odstranit**.

    ![Odstranit službu](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. V potvrzovacím oznámení klikněte na **Ano.** Může trvat několik minut, než bude služba odstraněna.

    ![Potvrzení odstranění](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Získání registračního klíče služby

Po úspěšném vytvoření služby budete muset zaregistrovat zařízení StorSimple se službou. Chcete-li zaregistrovat své první zařízení StorSimple, budete potřebovat registrační klíč služby. Chcete-li zaregistrovat další zařízení s existující službou StorSimple, potřebujete registrační klíč i šifrovací klíč dat služby (který je generován na prvním zařízení během registrace). Další informace o šifrovacím klíči dat služby naleznete v [tématu StorSimple security](storsimple-8000-security.md). Registrační klíč můžete získat tak, že budete mít přístup ke **klíčům** v okně Správce zařízení StorSimple.

Chcete-li získat registrační klíč služby, proveďte následující kroky.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

Uchovávejte registrační klíč služby na bezpečném místě. Tento klíč a šifrovací klíč dat služby budete potřebovat k registraci dalších zařízení s touto službou. Po získání registračního klíče služby je nutné nakonfigurovat zařízení prostřednictvím rozhraní Windows PowerShell for StorSimple.

Podrobnosti o použití tohoto registračního klíče naleznete [v tématu Krok 3: Konfigurace a registrace zařízení prostřednictvím prostředí Windows PowerShell pro StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Obnovení registračního klíče služby
Pokud je nutné provést střídání klíčů nebo pokud se změnil seznam správců služeb, je třeba znovu vygenerovat registrační klíč služby. Při regeneraci klíče se nový klíč používá pouze pro registraci následných zařízení. Zařízení, která již byla zaregistrována, nejsou tímto procesem ovlivněna.

Chcete-li znovu vygenerovat registrační klíč služby, proveďte následující kroky.

### <a name="to-regenerate-the-service-registration-key"></a>Obnovení registračního klíče služby
1. V okně **Správce zařízení StorSimple** přejděte na ** &gt; klíči pro správu** . **Keys**
    
    ![Okno Klíče](./media/storsimple-8000-manage-service/regenregkey2.png)

2. V okně **Klíče** klepněte na tlačítko **Obnovit**.

    ![Klikněte na regenerovat](./media/storsimple-8000-manage-service/regenregkey3.png)
3. V okně **obnovit službu registrace klíče** zkontrolujte akci požadovanou při opětovném vygenerování klíčů. Všechna následující zařízení, která jsou registrována v této službě, používají nový registrační klíč. Potvrďte klepnutím na **tlačítko Obnovit.** Po dokončení regenerace budete upozorněni.

    ![Potvrdit regenerovat](./media/storsimple-8000-manage-service/regenregkey4.png)

4. Zobrazí se nový registrační klíč služby.

5. Zkopírujte tento klíč a uložte jej pro registraci nových zařízení s touto službou.



## <a name="change-the-service-data-encryption-key"></a>Změna šifrovacího klíče dat služby
Šifrovací klíče dat služby se používají k šifrování důvěrných zákaznických dat, jako jsou přihlašovací údaje účtu úložiště, které jsou odesílány ze služby StorSimple Manager do zařízení StorSimple. Pokud má vaše IT organizace zásady střídání klíčů na úložných zařízeních, budete muset tyto klíče pravidelně měnit. Proces změny klíče se může mírně lišit v závislosti na tom, zda existuje jedno zařízení nebo více zařízení spravovaných službou StorSimple Manager. Další informace naleznete v oblasti [zabezpečení a ochrany dat StorSimple](storsimple-8000-security.md).

Změna šifrovacího klíče dat služby je třístupňový proces:

1. Pomocí skriptů prostředí Windows PowerShell pro Azure Resource Manager autorizujte zařízení ke změně šifrovacího klíče dat služby.
2. Pomocí prostředí Windows PowerShell pro StorSimple iniciujte změnu šifrovacího klíče dat služby.
3. Pokud máte více než jedno zařízení StorSimple, aktualizujte šifrovací klíč dat služby na ostatních zařízeních.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>Krok 1: Změna šifrovacího klíče dat služby pomocí skriptu Prostředí Windows PowerShell
Správce zařízení obvykle požádá správce služby autorizovat zařízení ke změně šifrovacích klíčů dat služby. Správce služby pak autorizuje zařízení ke změně klíče.

Tento krok se provádí pomocí skriptu založeného na Azure Resource Manageru. Správce služby může vybrat zařízení, které je způsobilé k autorizaci. Zařízení je pak oprávněno spustit proces změny šifrovacího klíče dat služby. 

Další informace o používání skriptu naleznete na [autorizovat-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Která zařízení mohou být autorizována ke změně šifrovacích klíčů dat služby?
Zařízení musí splňovat následující kritéria, aby mohlo být autorizováno k zahájení změn šifrovacího klíče dat služby:

* Aby bylo zařízení způsobilé pro autorizaci změny klíče pro šifrování dat služby, musí být zařízení online.
* Pokud nebyla zahájena změna klíče, můžete stejné zařízení znovu autorizovat po 30 minutách.
* Můžete autorizovat jiné zařízení za předpokladu, že změna klíče nebyla iniciována dříve autorizovaným zařízením. Po autorizaci nového zařízení nemůže staré zařízení změnu spustit.
* Zařízení nelze autorizovat, pokud probíhá přechod šifrovacího klíče dat služby.
* Zařízení můžete autorizovat, když některá zařízení registrovaná ve službě převedly šifrování, zatímco jiná nikoli. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Krok 2: Použití prostředí Windows PowerShell pro StorSimple k zahájení změny šifrovacího klíče dat služby
Tento krok se provádí v prostředí Windows PowerShell pro StorSimple rozhraní na autorizovaném zařízení StorSimple.

> [!NOTE]
> Žádné operace lze provádět na portálu Azure služby StorSimple Manager, dokud není dokončen a převrácení klíče.


Pokud používáte sériovou konzolu zařízení pro připojení k rozhraní prostředí Windows PowerShell, proveďte následující kroky.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Zahájení změny šifrovacího klíče dat služby
1. Vyberte možnost 1 pro přihlášení s úplným přístupem.
2. Na příkazovém řádku zadejte:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Po úspěšném dokončení rutiny získáte nový šifrovací klíč dat služby. Zkopírujte a uložte tento klíč pro použití v kroku 3 tohoto procesu. Tento klíč bude použit k aktualizaci všech zbývajících zařízení registrovaných službou StorSimple Manager.
   
   > [!NOTE]
   > Tento proces musí být zahájen do čtyř hodin od autorizace zařízení StorSimple.
   > 
   > 
   
   Tento nový klíč je pak odeslán do služby, která má být odeslána do všech zařízení, která jsou registrována ve službě. Na řídicím panelu služby se pak zobrazí výstraha. Služba zakáže všechny operace na registrovaných zařízeních a správce zařízení pak bude muset aktualizovat šifrovací klíč dat služby na ostatních zařízeních. Vstupně-setkám s vstupně-ti a nadále (hostitelé odesílající data do cloudu) však nebudou narušeny.
   
   Pokud máte jedno zařízení registrované na vaší službě, proces přechodu je nyní dokončen a můžete přeskočit další krok. Pokud máte ve službě zaregistrováno více zařízení, přejděte ke kroku 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Krok 3: Aktualizace šifrovacího klíče dat služby na jiných zařízeních StorSimple
Tyto kroky musí být provedeny v rozhraní prostředí Windows PowerShell vašeho zařízení StorSimple, pokud máte více zařízení registrovaných do služby StorSimple Manager. Klíč, který jste získali v kroku 2, musí být použit k aktualizaci všech zbývajících zařízení StorSimple registrovaných službou StorSimple Manager.

Chcete-li aktualizovat šifrování dat služby v zařízení, proveďte následující kroky.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>Aktualizace šifrovacího klíče dat služby na fyzických zařízeních
1. Použití prostředí Windows PowerShell pro StorSimple pro připojení ke konzoli. Vyberte možnost 1 pro přihlášení s úplným přístupem.
2. Na příkazovém řádku zadejte:`Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Zadejte šifrovací klíč dat služby, který jste získali v [kroku 2: Použijte prostředí Windows PowerShell pro StorSimple k zahájení změny šifrovacího klíče dat služby](#to-initiate-the-service-data-encryption-key-change).

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>Aktualizace šifrovacího klíče dat služby na všech cloudových zařízeních 8010/8020
1. Stáhněte a nastavte skript [PowerShell Update-CloudApplianceServiceEncryptionKey.ps1.](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) 
2. Otevřete PowerShell a na příkazovém řádku zadejte:`Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Tento skript zajistí, že šifrovací klíč dat služby je nastaven na všech cloudových zařízeních 8010/8020 pod správcem zařízení.

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Podporované operace na zařízeních s verzemi před aktualizací 5.0
Na webu Azure Portal jsou podporovány pouze StorSimple zařízení s aktualizací 5.0 a vyšší. Zařízení se staršími verzemi mají omezenou podporu. Po migraci na portál Azure pomocí následující tabulky zjistěte, které operace jsou podporované na zařízeních s verzemi před aktualizací 5.0.

| Operace                                                                                                                       | Podporuje se      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Registrování zařízení                                                                                                               | Ano            |
| Konfigurace nastavení zařízení, jako je obecné, síťové a bezpečnostní                                                                | Ano            |
| Prohledávejte, stahujte a instalujte aktualizace                                                                                             | Ano            |
| Deaktivovat zařízení                                                                                                               | Ano            |
| Odstranění zařízení                                                                                                                   | Ano            |
| Vytvoření, úprava a odstranění kontejneru svazků                                                                                   | Ne             |
| Vytvoření, úprava a odstranění svazku                                                                                             | Ne             |
| Vytvoření, úprava a odstranění zásad zálohování                                                                                      | Ne             |
| Ruční zálohování                                                                                                            | Ne             |
| Naplánované zálohování                                                                                                         | Neuvedeno |
| Obnovit z backupset                                                                                                        | Ne             |
| Klonování k zařízení se spuštěnou aktualizací 3.0 a novější <br> Zdrojové zařízení je spuštěna verze před aktualizací 3.0.                                | Ano            |
| Klonování k zařízení se spuštěným verzemi před aktualizací 3.0                                                                          | Ne             |
| Převzetí služeb při selhání jako zdrojové zařízení <br> (ze zařízení se spuštěnou verzí před aktualizací 3.0 na zařízení se spuštěnou aktualizací 3.0 a novější)                                                               | Ano            |
| Převzetí služeb při selhání jako cílové zařízení <br> (na zařízení se spuštěnou verzí softwaru před aktualizací 3.0)                                                                                   | Ne             |
| Vymazání výstrahy                                                                                                                  | Ano            |
| Zobrazení zásad zálohování, katalogu zálohování, svazků, kontejnerů svazků, grafů monitorování, úloh a výstrah vytvořených na klasickém portálu | Ano            |
| Zapnutí a vypnutí ovladačů zařízení                                                                                              | Ano            |


## <a name="next-steps"></a>Další kroky
* Další informace o [procesu nasazení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* Přečtěte si další informace o [správě účtu úložiště StorSimple](storsimple-8000-manage-storage-accounts.md).
* Další informace o tom, jak [používat službu StorSimple Device Manager ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

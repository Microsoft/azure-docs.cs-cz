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
ms.openlocfilehash: 66244bd8e24ff62be41df72f7a39c0ce0ed13135
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93360670"
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
Šifrovací klíče dat služby se používají k šifrování důvěrných zákaznických dat, jako jsou přihlašovací údaje účtu úložiště, které se odesílají ze služby StorSimple Manager do zařízení StorSimple. Tyto klíče budete muset pravidelně měnit, pokud vaše organizace IT má v zařízeních úložiště zásady pro střídání klíčů. Proces změny klíče může být mírně odlišný v závislosti na tom, jestli existuje jedno nebo více zařízení spravovaných službou StorSimple Manager. Další informace najdete na webu [StorSimple Security and Data Protection](storsimple-8000-security.md).

Změna šifrovacího klíče dat služby je proces 3 kroků:

1. Pomocí skriptů Windows PowerShellu pro Azure Resource Manager autorizujte zařízení, aby se změnil šifrovací klíč dat služby.
2. Pomocí Windows PowerShell pro StorSimple zahajte změnu šifrovacího klíče dat služby.
3. Pokud máte více než jedno zařízení StorSimple, aktualizujte šifrovací klíč dat služby na ostatních zařízeních.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>Krok 1: použití skriptu Windows PowerShellu k autorizaci zařízení ke změně šifrovacího klíče dat služby
Správce zařízení obvykle vyžádá, aby správce služby schvaloval zařízení, aby změnil šifrovací klíče dat služby. Správce služby pak udělí zařízení, aby změnil klíč.

Tento krok se provádí pomocí skriptu založeného na Azure Resource Manager. Správce služeb může vybrat zařízení, která mají nárok na autorizaci. Zařízení je pak autorizováno ke spuštění procesu změny šifrovacího klíče dat služby. 

Další informace o použití skriptu najdete v části [Authorize-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Která zařízení mohou být autorizována pro změnu klíčů šifrování dat služby?
Aby bylo možné iniciovat změny šifrovacího klíče dat služby, musí zařízení splňovat následující kritéria:

* Zařízení musí být online, aby mohlo mít nárok na autorizaci změny šifrovacího klíče dat služby.
* Po 30 minutách můžete stejné zařízení autorizovat, pokud se změna klíče neiniciovala.
* Můžete autorizovat jiné zařízení za předpokladu, že se změna klíče neiniciovala dříve autorizovaným zařízením. Po autorizaci nového zařízení nemůže původní zařízení zahájit změnu.
* Nemůžete autorizovat zařízení, zatímco probíhá výměna šifrovacího klíče dat služby.
* Zařízení můžete autorizovat v případě, že některá zařízení zaregistrovaná ve službě převzala šifrování, zatímco ostatní ne. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Krok 2: použití Windows PowerShell pro StorSimple k inicializaci změny šifrovacího klíče dat služby
Tento krok se provádí v rozhraní Windows PowerShell pro StorSimple na autorizovaném zařízení StorSimple.

> [!NOTE]
> V Azure Portal služby StorSimple Manager se nedají provádět žádné operace, dokud se nedokončila Výměna klíče.


Pokud se připojujete k rozhraní Windows PowerShell pomocí konzoly sériového portu zařízení, proveďte následující kroky.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Postup při inicializaci změny šifrovacího klíče dat služby
1. Vyberte možnost 1 pro přihlášení s úplným přístupem.
2. Na příkazovém řádku zadejte:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Po úspěšném dokončení rutiny se zobrazí nový šifrovací klíč dat služby. Zkopírujte tento klíč a uložte ho pro použití v kroku 3 tohoto procesu. Tento klíč se použije k aktualizaci všech zbývajících zařízení zaregistrovaných ve službě StorSimple Manager.
   
   > [!NOTE]
   > Tento proces je potřeba zahájit během čtyř hodin od autorizace zařízení StorSimple.
   > 
   > 
   
   Tento nový klíč se pak pošle službě, aby se odeslal do všech zařízení, která jsou zaregistrovaná ve službě. Na řídicím panelu služby se pak zobrazí výstraha. Služba zakáže všechny operace u registrovaných zařízení a Správce zařízení bude muset na ostatních zařízeních aktualizovat šifrovací klíč dat služby. I/OS (hostitelé odesílající data do cloudu) však nebudou přerušovat.
   
   Máte-li pro vaši službu zaregistrovanou jedno zařízení, proces přecházení je nyní dokončen a můžete přeskočit další krok. Pokud máte pro vaši službu zaregistrovanou více zařízení, přejděte ke kroku 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Krok 3: aktualizace šifrovacího klíče dat služby na jiných zařízeních StorSimple
Tyto kroky je potřeba provést v rozhraní Windows PowerShell zařízení StorSimple, pokud máte ve službě StorSimple Manager zaregistrovanou více zařízení. Klíč, který jste získali v kroku 2, je nutné použít k aktualizaci všech zbývajících zařízení StorSimple zaregistrovaných ve službě StorSimple Manager.

Provedením následujících kroků aktualizujte šifrování dat služby na vašem zařízení.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>Aktualizace šifrovacího klíče dat služby na fyzických zařízeních
1. K připojení ke konzole použijte Windows PowerShell pro StorSimple. Vyberte možnost 1 pro přihlášení s úplným přístupem.
2. Do příkazového řádku zadejte:  `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Zadejte šifrovací klíč dat služby, který jste získali v [kroku 2: použití Windows PowerShell pro StorSimple k inicializaci změny šifrovacího klíče dat služby](#to-initiate-the-service-data-encryption-key-change).

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>Aktualizace šifrovacího klíče dat služby ve všech cloudových zařízeních 8010/8020
1. Stáhněte a nastavte [Update-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) powershellový skript. 
2. Otevřete PowerShell a na příkazovém řádku zadejte:  `Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Tento skript zajistí, aby se šifrovací klíč dat služby nastavil na všech cloudových zařízeních 8010/8020 ve Správci zařízení.

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

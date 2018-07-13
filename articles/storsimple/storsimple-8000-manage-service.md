---
title: Nasazení služby Správce zařízení StorSimple v Azure | Dokumentace Microsoftu
description: Vysvětluje, jak vytvářet a odstraňovat služby Správce zařízení StorSimple na webu Azure Portal a popisuje, jak spravovat registrační klíč služby.
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
ms.openlocfilehash: d6010b7ff03689588251a9649eecb412bf9f3a8d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38701916"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>Nasazení služby Správce zařízení StorSimple pro zařízení StorSimple 8000 series

## <a name="overview"></a>Přehled

Služba Správce zařízení StorSimple běží v Microsoft Azure a připojí k více zařízení StorSimple. Po vytvoření služby ji můžete použít ke správě všech zařízení, které jsou připojené ke službě Správce zařízení StorSimple v jednom centrálním umístění, a tím minimalizovat správní režie.

Tento kurz popisuje kroky potřebné k vytváření, odstraňování, migrace služby a správu registrační klíč služby. Informace obsažené v tomto článku se vztahuje pouze na zařízeních StorSimple řady 8000. Další informace o virtuálních polí StorSimple, přejděte na [nasazení služby Správce zařízení StorSimple pro StorSimple Virtual Array](storsimple-virtual-array-manage-service.md).

> [!NOTE]
> -  Na webu Azure portal podporuje zařízení s verzí Update 5.0 nebo novější. Pokud zařízení není aktuální, nainstalujte aktualizaci 5 okamžitě. Další informace najdete v části [instalace aktualizace 5](storsimple-8000-install-update-5.md). 
> - Pokud používáte cloudové zařízení StorSimple (8010/8020), cloudové zařízení nelze aktualizovat. Vytvořte nový cloud appliance s aktualizací Update 5.0 a převzetí služeb při selhání do nové cloudové zařízení, vytvořené pomocí nejnovější verze softwaru. 
> - Všechna zařízení s verzí Update 4.0 nebo starší, budou mít [funkčnost správy omezená](storsimple-8000-manage-service.md#supported-operations-on-devices-running-versions-prior-to-update-5.0). 

## <a name="create-a-service"></a>Vytvoření služby
Pokud chcete vytvořit službu StorSimple Device Manager, musíte mít:

* Předplatné se smlouvou Enterprise
* Aktivní účet úložiště Microsoft Azure
* Fakturační údaje, který se používá pro správu přístupu

Jsou povoleny pouze předplatná se smlouvou Enterprise. Můžete také vytvořit výchozí účet úložiště při vytváření služby.

Jediné služby může spravovat více zařízení. Zařízení, ale nemůžou zahrnovat víc služeb. Velký podnik může mít víc instancí služby pro práci s různým předplatným, organizace nebo dokonce nasazení umístění. 

> [!NOTE]
> Budete potřebovat samostatné instance služby Správce zařízení StorSimple ke správě zařízení řady StorSimple 8000 a virtuálních polí StorSimple.

Proveďte následující kroky k vytvoření služby.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Pro každou službu Správce zařízení StorSimple existují následující atributy:

* **Název** – název, který byl přiřazen do služby Správce zařízení StorSimple, při vytvoření rovnou uložil. **Název služby nelze změnit po vytvoření služby. To platí také pro jiné entity, jako jsou zařízení, svazky, kontejnery svazků a zásady zálohování, které nelze přejmenovat na webu Azure Portal.**
* **Stav** – stav služby, který může být **aktivní**, **vytváření**, nebo **Online**.
* **Umístění** – zeměpisné umístění, ve kterém se nasadí zařízení StorSimple.
* **Předplatné** – předplatné pro fakturaci, který je spojen s vaší službou.

## <a name="delete-a-service"></a>Odstranit službu

Než odstraníte službu, ujistěte se, že žádná připojená zařízení budou používat. Pokud se služba používá, deaktivujte připojená zařízení. Operaci deaktivovat severu připojení mezi zařízením a službou, ale zachovat data zařízení v cloudu.

> [!IMPORTANT]
> Po odstranění služby je nevratná operace. Jakékoli zařízení, které se pomocí služby je potřeba obnovit tovární nastavení, než je možné s jinou službu. V tomto scénáři dojde ke ztrátě místních dat na zařízení, stejně jako v konfiguraci.

Proveďte následující kroky pro odstranění služby.

### <a name="to-delete-a-service"></a>Chcete-li odstranit službu

1. Vyhledejte službu, kterou chcete odstranit. Klikněte na tlačítko **prostředky** ikonu a potom zadejte příslušné podmínky vyhledávání. Ve výsledcích hledání klikněte na službu, kterou chcete odstranit.

    ![Odstranit vyhledávací služba](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Tím přejdete do okna služby Správce zařízení StorSimple. Klikněte na **Odstranit**.

    ![Odstranit službu](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. Klikněte na tlačítko **Ano** v potvrzení oznámení. Může trvat několik minut, než služba má být odstraněn.

    ![Potvrzení odstranění](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Získání registračního klíče služby

Po úspěšném vytvoření služby je potřeba zařízení StorSimple zaregistrovat do služby. K registraci prvního zařízení StorSimple, potřebujete registrační klíč služby. K registraci dalších zařízení s existující službu StorSimple, potřebujete registrační klíč a šifrovací klíč dat služby (který je generován v prvním zařízení během registrace). Další informace o šifrovací klíč dat služby najdete v tématu [zabezpečení zařízení StorSimple](storsimple-8000-security.md). Registrační klíč získáte přístup k **klíče** na okna Správce zařízení StorSimple.

Proveďte následující kroky, chcete-li získat registrační klíč služby.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

Zachovat registrační klíč služby do bezpečného umístění. Potřebujete tento klíč, jakož i šifrovací klíč dat služby, k registraci dalších zařízení s touto službou. Po obdržení registračního klíče služby, je nutné nakonfigurovat zařízení pomocí Windows Powershellu pro StorSimple rozhraní.

Podrobnosti o použití tohoto registračního klíče najdete v tématu [krok 3: Konfigurace a registrace zařízení pomocí Windows Powershellu pro StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Znovu vygenerovat registrační klíč služby
Je potřeba znovu vygenerovat registrační klíč služby, pokud je nutné provést obměna klíčů, nebo pokud došlo ke změně seznamu správců služeb. Pokud klíč znovu vygenerujete, nový klíč slouží pouze k registraci dalších zařízení. Zařízení, která již byla registrována nejsou ovlivněny tímto procesem.

Proveďte následující kroky se znovu vygenerovat registrační klíč služby.

### <a name="to-regenerate-the-service-registration-key"></a>Znovu vygenerovat registrační klíč služby
1. V **Správce zařízení StorSimple** okno, přejděte na **správu &gt;**  **klíče**.
    
    ![Okno Klíče](./media/storsimple-8000-manage-service/regenregkey2.png)

2. V **klíče** okna, klikněte na tlačítko **znovu vygenerovat**.

    ![Klikněte na znovu vygenerovat klíč](./media/storsimple-8000-manage-service/regenregkey3.png)
3. V **znovu vygenerovat registrační klíč** okno, zkontrolujte tato akce vyžadována, je-li klíče budou znovu vygenerovány. Další zařízení, která jsou registrovaná ve službě pomocí nového registračního klíče. Klikněte na tlačítko **znovu vygenerovat** potvrďte. Po dokončení opětovné, se zobrazí oznámení.

    ![Potvrdit obnovení](./media/storsimple-8000-manage-service/regenregkey4.png)

4. Zobrazí se nový registrační klíč služby.

5. Klíč zkopírujte a uložte ho pro registraci všechna nová zařízení s touto službou.



## <a name="change-the-service-data-encryption-key"></a>Změna šifrovacího klíče dat služby
Šifrovací klíče dat služby se používají k zašifrování důvěrných zákaznická data, jako je například přihlašovací údaje účtu úložiště, které se odesílají ze služby StorSimple Manager zařízení StorSimple. Je potřeba pravidelně měnit tyto klíče, pokud má vaše organizace IT obměny klíče zásad na zařízení úložiště. Proces změny klíče může být mírně liší v závislosti na tom, zda je jednoho nebo více zařízení spravovaná pomocí služby StorSimple Manager. Další informace najdete v části [StorSimple zabezpečení a ochranu dat](storsimple-8000-security.md).

Změna šifrovacího klíče dat služby je proces, krok 3:

1. Pomocí skriptů Windows Powershellu pro Azure Resource Manager, povolit zařízení, které chcete změnit šifrovací klíč dat služby.
2. Použití Windows Powershellu pro StorSimple, zahajte změnu klíče šifrování dat služby.
3. Pokud máte více než jednoho zařízení StorSimple, aktualizujte šifrovací klíč dat služby na jiných zařízeních.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>Krok 1: Skript pomocí Windows Powershellu k ověření zařízení, které chcete změnit šifrovací klíč dat služby
Správce zařízení obvykle bude požadovat, že autorizace služby Správce zařízení, které chcete změnit šifrovací klíče dat služby. Správce služeb bude potom budete autorizovat zařízení a změníte klíč.

Tento krok se provádí pomocí skriptu založené na Azure Resource Manageru. Správce služeb můžete vybrat zařízení, které jsou způsobilé k autorizaci. Zařízení je pak ověřena ke spuštění procesu změny klíče šifrování dat služby. 

Další informace o používání skriptu, přejděte na [Authorize ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Zařízení, která můžete oprávnění ke změně šifrovací klíče dat služby?
Předtím, než může být oprávnění k inicializaci klíče změny šifrování dat služby, zařízení musí splňovat následující kritéria:

* Zařízení musí být online způsobilé k autorizaci změnu klíče šifrování dat služby.
* Můžete povolit stejném zařízení znovu za 30 minut, pokud nebyla inicializována změny klíče.
* Jiné zařízení, můžete povolit, za předpokladu, že dříve autorizovaní zařízení ještě nebylo inicializováno změny klíče. Poté, co má autorizaci novém zařízení, stará zařízení nemůžete zahájit změnu.
* Zařízení nejde autorizovat, zatímco probíhá výměna šifrovacího klíče dat služby.
* Zařízení může autorizovat, když některé zařízení registrovaná ve službě máte převracet šifrování, zatímco jiné ne. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Krok 2: Použití Windows Powershellu pro StorSimple k zahájení změny klíče šifrování dat služby
Tento krok se provádí v prostředí Windows PowerShell pro StorSimple rozhraní na autorizované zařízení StorSimple.

> [!NOTE]
> Žádná operace lze provádět na webu Azure Portal služby StorSimple Manager, dokud se nedokončí výměny klíčů.


Pokud používáte konzole sériového portu zařízení pro připojení k rozhraní Windows PowerShell, postupujte následovně.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>K zahájení změny klíče šifrování dat služby
1. Výběrem možnosti 1 přihlaste s úplným přístupem.
2. Na příkazovém řádku zadejte:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Po úspěšném dokončení rutina, zobrazí se nový šifrovací klíč dat služby. Zkopírujte a uložte tento klíč pro použití v kroku 3 tohoto postupu. Tento klíč se používá k aktualizaci všech zbývajících zařízení zaregistrované ve službě StorSimple Manager.
   
   > [!NOTE]
   > Tento proces musí zahájit do čtyř hodin autorizace zařízení StorSimple.
   > 
   > 
   
   Tento nový klíč odeslaný do služby doručí do všech zařízení, která jsou zaregistrovaná ve službě service. Výstraha se pak zobrazí na řídicím panelu služby. Služba zakáže všechny operace na zaregistrovaných zařízeních, a pak bude muset Správce zařízení aktualizovat šifrovací klíč dat služby na jiných zařízeních. Vstupně-výstupních operací (hostitele, kteří odesílají data do cloudu) ale nebudou přerušit.
   
   Pokud máte jedno zařízení zaregistrován ke službě výměny proces je nyní dokončený a můžete přeskočit na další krok. Pokud máte více zařízení zaregistrován ke službě, pokračujte krokem 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Krok 3: Aktualizace šifrovací klíč dat služby na jiná zařízení StorSimple
Tyto kroky musíte provést v rozhraní Windows PowerShell vašeho zařízení StorSimple, pokud máte více zařízení zaregistrován ke službě StorSimple Manager. Klíč, který jste získali v kroku 2 musí použít k aktualizaci všech zbývajících zařízení StorSimple zaregistrovaní ve službě StorSimple Manager.

Proveďte následující kroky a aktualizujete šifrování dat služby ve vašem zařízení.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>Chcete-li aktualizovat šifrovací klíč dat služby na fyzických zařízeních
1. Připojení ke konzole pomocí prostředí Windows PowerShell pro StorSimple. Výběrem možnosti 1 přihlaste s úplným přístupem.
2. Na příkazovém řádku zadejte:  `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Poskytují šifrovací klíč dat služby, který jste získali v [krok 2: použití Windows Powershellu pro StorSimple k zahájení změny klíče šifrování dat služby](#to-initiate-the-service-data-encryption-key-change).

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>Chcete-li aktualizovat šifrovací klíč dat služby na všechna 8010/8020 cloud Appliance
1. Stažení a instalace [aktualizace CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) skript prostředí PowerShell. 
2. Otevřete PowerShell a na příkazovém řádku zadejte:  `Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Tento skript se ujistěte, že tento šifrovací klíč dat služby je nastaven na všechna zařízení 8010/8020 cloud v rámci Správce zařízení.

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Podporované operace na zařízení se systémem starším než Update 5.0
Na webu Azure Portal jsou podporovány pouze zařízení StorSimple s verzí Update 5.0 nebo vyšší. Zařízení, na kterých běží starší verze mají omezenou podporu. Po migraci na portál Azure Portal, použijte následující tabulku k pochopení operací, které jsou podporovány v zařízení se systémem starším než Update 5.0.

| Operace                                                                                                                       | Podporováno      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Registrování zařízení                                                                                                               | Ano            |
| Konfigurace nastavení zařízení, jako jsou obecné, sítě a zabezpečení                                                                | Ano            |
| Kontrolovat, stahovat a instalovat aktualizace                                                                                             | Ano            |
| Deaktivace zařízení                                                                                                               | Ano            |
| Odstranění zařízení                                                                                                                   | Ano            |
| Vytvořit, upravit a odstranit kontejner svazků                                                                                   | Ne             |
| Vytváření, úpravě a odstranění svazku                                                                                             | Ne             |
| Vytváření, úpravě a odstraňování zásady zálohování                                                                                      | Ne             |
| Proveďte ruční zálohování                                                                                                            | Ne             |
| Provedení naplánované zálohy                                                                                                         | Neuvedeno |
| Obnovit z sadu záloh                                                                                                        | Ne             |
| Klonovat na zařízení se softwarem Update 3.0 a vyšší <br> Zdrojové zařízení používá verzi před Update 3.0.                                | Ano            |
| Naklonovat do zařízení se systémem verze starší než Update 3.0                                                                          | Ne             |
| Převzetí služeb při selhání jako zdrojové zařízení <br> (ze zařízení s verzí Update 3.0 před na zařízení se softwarem Update 3.0 a novější)                                                               | Ano            |
| Převzetí služeb při selhání jako cílové zařízení <br> (pro zařízení s verzí software před Update 3.0)                                                                                   | Ne             |
| Vymazat výstrahu                                                                                                                  | Ano            |
| Zobrazit zásady zálohování, katalog záloh, svazky, kontejnery svazků, grafy monitorování, úloh a upozornění vytvořená na portálu classic | Ano            |
| Zapnutí a vypnutí řadiče zařízení                                                                                              | Ano            |


## <a name="next-steps"></a>Další postup
* Další informace o [proces nasazení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* Další informace o [správou vašeho účtu úložiště StorSimple](storsimple-8000-manage-storage-accounts.md).
* Další informace o tom, jak [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

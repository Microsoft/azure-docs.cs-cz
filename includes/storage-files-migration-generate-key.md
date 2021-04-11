---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a142de62f1ab7046d47c29753863b16864d97536
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075557"
---
Šifrovací klíče dat služby se používají k šifrování důvěrných zákaznických dat, jako jsou přihlašovací údaje účtu úložiště, které se odesílají ze služby StorSimple Manager do zařízení StorSimple. Tyto klíče budete muset pravidelně měnit, pokud vaše organizace IT má v zařízeních úložiště zásady pro střídání klíčů. Proces změny klíče může být mírně odlišný v závislosti na tom, jestli existuje jedno nebo více zařízení spravovaných službou StorSimple Manager. Další informace najdete na webu [StorSimple Security and Data Protection](../articles/storsimple/storsimple-8000-security.md).

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
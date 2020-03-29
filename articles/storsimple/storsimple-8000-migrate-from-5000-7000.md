---
title: Migrace dat v řadě StorSimple 5000-7000 do zařízení řady 8000| Dokumenty společnosti Microsoft
description: Poskytuje přehled a předpoklady funkce Migrace.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2018
ms.author: alkohli
ms.openlocfilehash: 967c03f3c4201bdcf1529fdda93717b6eb74e771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60631631"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>Migrace dat ze série StorSimple 5000-7000 do zařízení řady 8000

> [!IMPORTANT]
> - Července 31, 2019 Série StorSimple 5000/7000 dosáhne stavu ukončení podpory (EOS). Doporučujeme, aby zákazníci řady StorSimple 5000/7000 migrovali na jednu z alternativ popsaných v dokumentu.
> - Migrace je v současné době asistovaná operace. Pokud máte v úmyslu migrovat data ze zařízení řady StorSimple 5000-7000 do zařízení řady 8000, je třeba naplánovat migraci pomocí podpory společnosti Microsoft. Podpora společnosti Microsoft pak povolí vaše předplatné pro migraci. Další informace naleznete v [tématu Jak otevřít lístek podpory](storsimple-8000-contact-microsoft-support.md).
> - Po podání žádosti o službu může trvat několik týdnů, než se spustí plán migrace a migrace skutečně spustí.
> - Než se obrátíte na podporu společnosti Microsoft, zkontrolujte a dokončete [požadavky migrace](#migration-prerequisites) uvedené v článku.

## <a name="overview"></a>Přehled

Tento článek zavádí funkci migrace, která umožňuje zákazníkům řady StorSimple 5000-7000 migrovat svá data do fyzického zařízení řady StorSimple 8000 nebo cloudového zařízení řady 8010/8020. Tento článek také odkazuje na krok za krokem ke stažení postup potřebný k migraci dat ze staršího zařízení řady 5000-7000 do fyzického nebo cloudového zařízení řady 8000.

Tento článek se vztahuje jak pro místní zařízení řady 8000, tak pro zařízení StorSimple Cloud Appliance.


## <a name="migration-feature-versus-host-side-migration"></a>Funkce migrace versus migrace na straně hostitele

Data můžete přesunout pomocí funkce migrace nebo provedením migrace na straně hostitele. Tato část popisuje specifika každé metody, včetně klady a zápory. Tyto informace slouží k tomu, abyste zjistili, kterou metodu chcete provádět migraci dat.

Funkce migrace simuluje proces zotavení po havárii (DR) od řady 7000/5000 do řady 8000. Tato funkce umožňuje migrovat data z formátu řady 5000/7000 do formátu řady 8000 v Azure. Proces migrace je zahájen pomocí nástroje Migrace StorSimple. Nástroj spustí stahování a převod metadat zálohování na zařízení řady 8000 a pak použije nejnovější zálohu k vystavení svazků v zařízení.

|      | Výhody                                                                                                                                     |Nevýhody                                                                                                                                                              |
|------|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Proces migrace zachovává historii záloh, které byly pořízeny v řadě 5000/7000.                                               | Když se uživatelé pokusí o přístup k datům, tato migrace stáhne data z Azure, čímž vzniknou náklady na stahování dat.                                     |
| 2.   | Na straně hostitele nejsou migrována žádná data.                                                                                                     | Proces potřebuje prostoje mezi začátkem zálohování a nejnovější zálohy se vynořil na 8000 série (lze odhadnout pomocí nástroje pro migraci). |
| 3.   | Tento proces zachovává všechny zásady, šablony šířky pásma, šifrování a další nastavení na zařízeních řady 8000.                      | Přístup uživatele přinese zpět pouze data přístupná uživateli a nebude rehydratovat celou datovou sadu.                                                  |
| 4.   | Tento proces vyžaduje více času na převod všech starších záloh v Azure, které se provádí asynchronně bez dopadu na produkční prostředí | Migraci lze provést pouze na úrovni konfigurace cloudu.  Jednotlivé svazky v konfiguraci cloudu nelze migrovat samostatně                       |

Migrace na straně hostitele umožňuje nezávisle nastavit řadu 8000 a zkopírovat data ze zařízení řady 5000/7000 do zařízení řady 8000. To je ekvivalentní migraci dat z jednoho zařízení úložiště do jiného. Ke kopírování dat se používá celá řada nástrojů, jako je Diskboss, robocopy.

|      | Výhody                                                                                                                      |Nevýhody                                                                                                                                                                                                      |
|------|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | K migraci lze přistupovat postupně po objemovém základě.                                               | Předchozí zálohy (převzaté v řadě 5000/7000) nebudou k dispozici na zařízení řady 8000.                                                                                                       |
| 2.   | Umožňuje konsolidaci dat do jednoho účtu úložiště v Azure.                                                       | První zálohování do cloudu na řadě 8000 bude trvat déle, protože všechna data řady 8000 musí být zálohována do Azure.                                                                     |
| 3.   | Po úspěšné migraci jsou všechna data v zařízení místní. Neexistují žádné latence při přístupu k datům. | Spotřeba úložiště Azure se zvýší, dokud se data neodstraní ze zařízení 5000/7000.                                                                                                        |
| 4.   |                                                                                                                           | Pokud zařízení řady 7000/5000 má velké množství dat, během migrace je třeba tato data stáhnout z Azure, které budou mít vzniknout náklady a latence související se stahováním dat z Azure |

Tento článek se zaměřuje pouze na funkci migrace ze zařízení řady 5000/7000 na 8000. Další informace o migraci na straně hostitele najdete v části [Migrace z jiných úložných zařízení](https://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating%20Data%20to%20StorSimple%20Volumes_09-02-15.pdf).

## <a name="migration-prerequisites"></a>Předpoklady migrace

Zde jsou předpoklady migrace pro starší zařízení řady 5000 nebo 7000 a zařízení StorSimple řady 8000.

> [!IMPORTANT]
> Před podáním žádosti o službu pomocí podpory společnosti Microsoft zkontrolujte a dokončete požadavky na migraci.

### <a name="for-the-50007000-series-device-source"></a>Pro zařízení řady 5000/7000 (zdroj)

Než začnete s migrací, ujistěte se, že:

* Máte 5000 nebo 7000 série zdrojové zařízení; zařízení může být živé nebo zkonzit.

    > [!IMPORTANT]
    > Doporučujeme, abyste měli sériový přístup k tomuto zařízení v průběhu celého procesu migrace. V případě problémů se zařízením může sériový přístup pomoci při řešení potíží.

* Zdrojové zařízení řady 5000 nebo 7000 používá verzi softwaru verze 2.1.1.518 nebo novější. Dřívější verze nejsou podporovány.
* Chcete-li ověřit verzi, ve které je spuštěna řada 5000 nebo 7000, podívejte se do pravého horního rohu webového uživatelského rozhraní. To by mělo zobrazit verzi softwaru, který je spuštěn v zařízení. Pro migraci by měl být spuštěn v2.1.1.518 řady 5000 nebo 7000.

    ![Kontrola verze softwaru na starším zařízení](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Pokud vaše živé zařízení není spuštěno v2.1.1.518 nebo novější, upgradujte systém na požadovanou minimální verzi. Podrobné pokyny naleznete v [podrobnostech o upgradu systému na v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
    * Pokud používáte v2.1.1.518, přejděte na webové uživatelské právo a zjistěte, zda existují nějaká oznámení pro selhání obnovení registru. Pokud se obnovení registru nezdařilo, spusťte obnovení registru. Další informace naleznete v informacích o [spuštění obnovení registru](http://onlinehelp.storsimple.com/111_Appliance/2_User_Guides/1_Current_(v2.1.1)/1_Web_UI_User_Guide_WIP/2_Configuration/4_Cloud_Accounts/1_Cloud_Credentials#Restoring_Backup_Registry).
    * Pokud máte zařízení dolů, které nebylo spuštěno v2.1.1.518, proveďte převzetí služeb při selhání na náhradní zařízení, které je spuštěno v2.1.1.518. Podrobné pokyny naleznete v části DR zařízení StorSimple řady 5000/7000.
    * Zálohujte data pro vaše zařízení tak, že pořizujete snímek cloudu.
    * Zkontrolujte, zda nejsou spuštěny všechny další aktivní úlohy zálohování, které jsou spuštěny na zdrojovém zařízení. To zahrnuje úlohy na hostiteli StorSimple Data Protection Console. Počkejte na dokončení aktuálních úloh.


### <a name="for-the-8000-series-physical-device-target"></a>Pro fyzické zařízení řady 8000 (cíl)

Než začnete s migrací, ujistěte se, že:

* Vaše cílové zařízení řady 8000 je zaregistrováno a spuštěno. Další informace naleznete v tématu [Jak nasadit zařízení StorSimple pomocí služby StorSimple Manager](storsimple-8000-deployment-walkthrough-u2.md).
* Vaše zařízení řady 8000 má nainstalovanou nejnovější aktualizaci StorSimple 8000 Series Update 5 a používá verzi 6.3.9600.17845 nebo novější. Pokud v zařízení nejsou nainstalovány nejnovější aktualizace, je třeba před zahájením migrace nainstalovat nejnovější aktualizace. Další informace naleznete v [tématu Instalace nejnovější aktualizace na zařízení řady 8000](storsimple-8000-install-update-5.md).
* Vaše předplatné Azure je povolené pro migraci. Pokud vaše předplatné není povolené, obraťte se na podporu Microsoftu a povolte předplatné pro migraci.

### <a name="for-the-80108020-cloud-appliance-target"></a>Pro cloudové zařízení 8010/8020 (cíl)

Než začnete s migrací, ujistěte se:

* Vaše cílové cloudové zařízení je registrované a spuštěné. Další informace naleznete v tématu [Nasazení a správa StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).
* Vaše cloudové zařízení používá nejnovější software StorSimple 8000 Series Update 5 verze 6.3.9600.17845. Pokud vaše cloudové zařízení neběží aktualizace 5, vytvořte před pokračováním migrace nové cloudové zařízení aktualizace 5. Další informace najdete v [tématu vytvoření cloudového zařízení 8010/8020](storsimple-8000-cloud-appliance-u2.md).

### <a name="for-the-computer-running-storsimple-migration-tool"></a>Pro počítač se spuštěnou nástrojem Migrace StorSimple

Nástroj StorSimple Migration je nástroj založený na ui, který umožňuje migrovat data z řady StorSimple 5000-7000 do zařízení řady 8000. Chcete-li nainstalovat nástroj Migrace StorSimple, použijte počítač, který splňuje následující požadavky.

Počítač je připojen k Internetu a:

* Je spuštěn následující operační systém
    * Windows 10.
    * Windows Server 2012 R2 (nebo vyšší) pro instalaci nástroje StorSimple Migration.
* Byla nainstalována rozhraní .NET 4.5.2.
* Má minimálně 5 GB volného místa pro instalaci a použití nástroje.

> [!TIP]
> Pokud je zařízení StorSimple připojeno k hostiteli systému Windows Server, můžete nástroj pro migraci nainstalovat do hostitelského počítače se systémem Windows Server.

#### <a name="to-install-storsimple-migration-tool"></a>Instalace nástroje Migrace StorSimple

Chcete-li do počítače nainstalovat nástroj StorSimple Migration, proveďte následující kroky.

1. Zkopírujte složku _StorSimple8000SeriesMigrationTool_ do počítače se systémem Windows. Ujistěte se, že jednotka, na které je software zkopírován, má dostatek místa.

    Otevřete ve složce konfigurační soubor nástroje _StorSimple8000SeriesMigrationTool.exe.config._ Zde je úryvek souboru.
    
    ```xml
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. Upravte hodnoty odpovídající klávesám a nahraďte je:

    * `UserName`– Uživatelské jméno pro přihlášení na portál Azure.
    * `SubscriptionName and SubscriptionId`– Název a ID předplatného Azure. Na vstupní stránce služby Správce zařízení StorSimple klikněte v části **Obecné**na **položku Vlastnosti**. Zkopírujte název předplatného a ID předplatného přidružené k vaší službě.
    * `ResourceName`– Název služby StorSimple Device Manager na webu Azure Portal. Také se zobrazí v části vlastnosti služby.
    * `ResourceGroup`– Název skupiny prostředků přidružené ke službě StorSimple Device Manager na webu Azure Portal. Také se zobrazí v části vlastnosti služby.
    ![Kontrola vlastností služby pro cílové zařízení](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId`– ID klienta Azure Active Directory na webu Azure Portal. Přihlaste se k Microsoft Azure jako správce. Na portálu Microsoft Azure klikněte na **Azure Active Directory**. V části **Spravovat** klikněte na **Vlastnosti**. ID klienta se zobrazí v poli **ID adresáře.**
    ![Kontrola ID klienta pro Azure Active Directory](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  Uložte změny provedené v konfiguračním souboru.
4.  Spusťte nástroj _StorSimple8000SeriesMigrationTool.exe._ Po zobrazení výzvy k zadání přihlašovacích údajů zadejte přihlašovací údaje přidružené k vašemu předplatnému na webu Azure Portal. 
5.  Zobrazí se ui nástroje StorSimple Migration.
  

## <a name="next-steps"></a>Další kroky
Stáhněte si podrobné pokyny k [migraci dat ze série StorSimple 5000-7000 do zařízení řady 8000](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b).

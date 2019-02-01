---
title: Migrace dat na StorSimple řad 5000 – 7000 zařízení 8000 series | Dokumentace Microsoftu
description: Poskytuje přehled a požadavky funkci migrace.
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
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55495849"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>Migrovat data z StorSimple 5000 – 7000 series do zařízení 8000 series

> [!IMPORTANT]
> - Do 31. července 2019 řady StorSimple 5000/7000 skončí jeho stavu podpory (SESTAVENÁ). Doporučujeme vám, že zákazníci řady StorSimple 5000/7000 migrace na jednu z alternativ popsané v dokumentu.
> - Migrace se aktuálně operaci s asistencí. Pokud máte v úmyslu migrovat data ze zařízení StorSimple řad 5000 – 7000 zařízení řady 8000, budete muset naplánovat migraci do Microsoft Support. Microsoft Support vám pak umožní vaše předplatné pro migraci. Další informace najdete v tématu Jak [vytvořit lístek podpory](storsimple-8000-contact-microsoft-support.md).
> - Poté, co je soubor žádosti o službu, může trvat několik týdnů k provedení migrace plánu a skutečně zahájit migraci.
> - Předtím, než se obrátíte na Microsoft Support, nezapomeňte si zkontrolujte a dokončete [požadavky na migraci](#migration-prerequisites) uvedeno v následujícím článku.

## <a name="overview"></a>Přehled

Tento článek představuje funkci migrace, která umožňuje zákazníkům StorSimple řady 5000-7000 migrovat data do fyzického zařízení StorSimple 8000 series nebo zařízení 8010/8020 cloudu. Tento článek obsahuje odkazy taky ke stažení najdete podrobný návod pro kroky potřebné k migraci dat ze starší verze zařízení řady 8000 fyzické řady 5000-7000 nebo cloudového zařízení.

Tento článek platí pro zařízení řady 8000 v místním i řešení StorSimple Cloud Appliance.


## <a name="migration-feature-versus-host-side-migration"></a>Funkce migrace a migrace na straně hostitele

Můžete přesunout data pomocí funkce migrace nebo provedením migrace straně hostitele. Tato část popisuje podrobností o jednotlivých metod, včetně výhody a nevýhody. Pomocí těchto informací zjistit, jakou metodu chcete vykonávat migrovat svoje data.

Funkce migrace simuluje procesu zotavení po havárii z řady 7000/5000 řady 8000. Tato funkce umožňuje migraci dat z formátu 5000/7000 series do formátu 8000 series na Azure. Zahájit proces migrace pomocí nástroje Migrace StorSimple. Nástroj spustí stahování a převodu metadat zálohy na zařízení řady 8000 a potom pomocí poslední zálohy k vystavení svazky na zařízení.

|      | V oblasti IT                                                                                                                                     |Nevýhody                                                                                                                                                              |
|------|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Proces migrace zachová historii zálohování, které byly provedeny na řady 5000/7000.                                               | Když se uživatelé pokusí o přístup k datům, tato migrace stáhne data z Azure, tak bez nákladů stahování dat.                                     |
| 2.   | Žádná data jsou migrována na straně hostitele.                                                                                                     | Proces potřebuje výpadku mezi začátkem zálohování a nejnovější zálohování se zobrazí v případě řady 8000 (se dá odhadnout pomocí nástroje migrace). |
| 3.   | Tento proces zachová všechny zásady, šablony šířky pásma, šifrování a ostatní nastavení na zařízení řady 8000.                      | Přístup uživatele se vrácení pouze data přistupují uživatelé a nebude dosazení celou datovou sadu.                                                  |
| 4.   | Tento proces vyžaduje čas navíc k převedení všech starších záloh v Azure, která se provádí asynchronně bez dopadu na produkční | Migraci můžete udělat jenom na úrovni konfigurace cloudu.  Jednotlivé svazky v konfiguraci cloudu nelze migrovat samostatně                       |

Migrace straně hostitele umožňuje nezávisle na sobě nastavení z řady 8000 a kopírování dat ze zařízení řady 5000/7000 zařízení řady 8000. Jedná se o ekvivalent k migraci dat z jednoho zařízení úložiště do druhého. Ke kopírování dat se používají širokou škálu nástrojů, jako je Diskboss robocopy.

|      | V oblasti IT                                                                                                                      |Nevýhody                                                                                                                                                                                                      |
|------|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Migraci můžete dosaženy postupně každý svazek svazek.                                               | Předchozí záložní kopie (pořízené řady 5000/7000) nebude k dispozici na zařízení řady 8000.                                                                                                       |
| 2.   | Umožňuje konsolidace dat do jednoho účtu úložiště v Azure.                                                       | První zálohování do cloudu na 8000 series bude trvat delší dobu jako veškerá data na 8000 series je potřeba zálohovat do Azure.                                                                     |
| 3.   | Po úspěšné migraci je lokální na zařízení všechna data. Neexistují žádné latenci při přístupu k datům. | Spotřebu úložiště Azure se zvýší, dokud data se odstraní ze zařízení 5000/7000.                                                                                                        |
| 4.   |                                                                                                                           | Pokud má zařízení řady 7000/5000 velké množství dat, během migrace tato data musí být stažené z azure, který bude mít za následek náklady a latence související se stahování dat z Azure |

Tento článek se zaměřuje pouze na tuto funkci migrace z 5000/7000 zařízení řady 8000. Další informace o migraci na straně hostitele, přejděte na [migrace z jiných zařízení úložišť](https://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating%20Data%20to%20StorSimple%20Volumes_09-02-15.pdf).

## <a name="migration-prerequisites"></a>Požadavky na migraci

Zde jsou uvedeny požadavky migrace pro starší verze zařízení řady 5000 a 7000 a zařízení StorSimple řady 8000.

> [!IMPORTANT]
> Zkontrolujte a dokončete migraci požadavky před soubor žádosti o službu pomocí Microsoft Support.

### <a name="for-the-50007000-series-device-source"></a>Pro zařízení řady 5000/7000 (zdroj)

Před zahájením migrace, ujistěte se, že:

* Je třeba vaše 5000 nebo řady 7000 zdrojového zařízení; zařízení může být live nebo dolů.

    > [!IMPORTANT]
    > Doporučujeme, abyste měli sériový přístup k tomuto zařízení během tohoto procesu migrace. Mělo by být problémy zařízení, sériového přístupu může pomoct s řešením potíží.

* Zdrojové zařízení řady 5000 a 7000 běží software verze v2.1.1.518 nebo novější. Starší verze se nepodporují.
* K ověření verze, na kterém běží řad 5000 a 7000, podívejte se v pravém horním rohu webové uživatelské rozhraní. Mělo by se zobrazit verze softwaru, na kterém běží vaše zařízení. Pro migraci by měl být řad 5000 a 7000 spuštěn v2.1.1.518.

    ![Zkontrolujte verzi softwaru na zařízení starší verze](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Pokud není spuštěná v2.1.1.518 nebo později, upgradujte systém na požadovanou minimální verzi vašeho zařízení za provozu. Podrobné pokyny najdete v tématu [upgradovat systém v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
    * Pokud používáte v2.1.1.518, přejděte na webové uživatelské rozhraní, aby viděli, jestli jsou všechny oznámení selhání obnovení registru. Pokud registru obnovení se nezdařila, spusťte obnovení registru. Další informace najdete v části Jak [spustit obnovení registru](http://onlinehelp.storsimple.com/111_Appliance/2_User_Guides/1_Current_(v2.1.1)/1_Web_UI_User_Guide_WIP/2_Configuration/4_Cloud_Accounts/1_Cloud_Credentials#Restoring_Backup_Registry).
    * Pokud máte dolů zařízení, která nebyla spuštěna v2.1.1.518, proveďte převzetí služeb při selhání na náhradní zařízení, na kterém běží v2.1.1.518. Podrobné pokyny najdete zotavení po Havárii vašeho zařízení StorSimple řady 5000/7000.
    * Zálohujte data pro zařízení s využitím snímek v cloudu.
    * Zkontrolujte všechny ostatní aktivní úlohy zálohování, které běží na zdrojové zařízení. To zahrnuje úlohy na hostiteli konzole ochrany dat StorSimple. Počkejte na dokončení aktuální úlohy.


### <a name="for-the-8000-series-physical-device-target"></a>Pro fyzickým zařízením řady 8000 (cíl).

Před zahájením migrace, ujistěte se, že:

* Vaší cílové 8000 series zařízení je registrované a spuštěna. Další informace najdete v tématu Jak [nasazení zařízení StorSimple pomocí služby StorSimple Manager](storsimple-8000-deployment-walkthrough-u2.md).
* Zařízení řady 8000 má nejnovější StorSimple 8000 Series Update 5 nainstalován a běží 6.3.9600.17845 nebo novější verzi. Pokud zařízení nemá nainstalované nejnovější aktualizace, budete muset nainstalovat nejnovější aktualizace, abyste mohli pokračovat v migraci. Další informace najdete v tématu Jak [instalace nejnovější aktualizace na vašem zařízení řady 8000](storsimple-8000-install-update-5.md).
* Vaše předplatné Azure je povoleno pro migraci. Pokud u vašeho odběru není aktivováno, obraťte se na Microsoft Support pro své předplatné pro migraci.

### <a name="for-the-80108020-cloud-appliance-target"></a>Pro 8010/8020 cloudového zařízení (cíl).

Před zahájením migrace, zkontrolujte:

* Cílové cloudové zařízení je registrované a spuštěna. Další informace najdete v tématu Jak [nasadit a spravovat řešení StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).
* Cloudové zařízení je spuštěna nejnovější StorSimple 8000 Series Update 5 verze softwaru 6.3.9600.17845. Pokud vaše cloudové zařízení není spuštěna Update 5, vytvoření nového cloudového zařízení Update 5 předtím, než budete pokračovat v migraci. Další informace najdete v tématu Jak [vytvoření 8010/8020 cloudového zařízení](storsimple-8000-cloud-appliance-u2.md).

### <a name="for-the-computer-running-storsimple-migration-tool"></a>Pro počítač se systémem nástroj pro migraci StorSimple

Nástroj pro migraci StorSimple je nástroj uživatelského rozhraní, který vám umožňuje migrovat data z StorSimple řad 5000 – 7000 zařízení řady 8000. Chcete-li nainstalovat nástroj pro migraci StorSimple, použijte počítač, který splňuje následující požadavky.

Počítač je připojený k Internetu a:

* Používá následující operační systém
    * Windows 10.
    * Windows Server 2012 R2 (nebo vyšší) Chcete-li nainstalovat nástroj pro migraci StorSimple.
* Má nainstalované rozhraní .NET 4.5.2.
* Má minimálně 5 GB volného místa k instalaci a použití nástroje.

> [!TIP]
> Pokud zařízení StorSimple je připojený k hostiteli s Windows serverem, můžete nainstalovat nástroj pro migraci na hostitelském počítači systém Windows Server.

#### <a name="to-install-storsimple-migration-tool"></a>Chcete-li nainstalovat nástroj pro migraci StorSimple

Proveďte následující kroky, chcete-li nainstalovat nástroj pro migraci StorSimple ve vašem počítači.

1. Zkopírujte složku _StorSimple8000SeriesMigrationTool_ do počítače Windows. Ujistěte se, že na jednotce, kde je zkopírován software má dostatek místa.

    Otevřete konfigurační soubor nástroje _StorSimple8000SeriesMigrationTool.exe.config_ ve složce. Tady je fragment kódu souboru.
    
    ```xml
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. Upravit hodnoty odpovídající klíči a nahradit:

    * `UserName` – Uživatelské jméno pro přihlášení k webu Azure portal.
    * `SubscriptionName and SubscriptionId` – Zadejte název a ID vašeho předplatného Azure. Ve službě Správce zařízení StorSimple úvodní stránka, v části **Obecné**, klikněte na tlačítko **vlastnosti**. Zkopírujte název předplatného a ID předplatného související s vaší službou.
    * `ResourceName` – Název služby Správce zařízení StorSimple na webu Azure Portal. Také se zobrazí v části Vlastnosti služby.
    * `ResourceGroup` – Název skupiny prostředků související s vaší službou Správce zařízení StorSimple na webu Azure Portal. Také se zobrazí v části Vlastnosti služby.
    ![Zkontrolujte vlastnosti služby pro cílové zařízení](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId` – Azure ID Tenanta Active Directory na webu Azure portal. Přihlaste se k Microsoft Azure jako správce. Na portálu Microsoft Azure, klikněte na tlačítko **Azure Active Directory**. V části **spravovat**, klikněte na tlačítko **vlastnosti**. ID se zobrazí v tenantovi **ID adresáře** pole.
    ![Zkontrolujte ID Tenanta pro službu Azure Active Directory](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  Uložte změny provedené v konfiguračním souboru.
4.  Spustit _StorSimple8000SeriesMigrationTool.exe_ spusťte nástroj. Po zobrazení výzvy k zadání pověření, zadejte přihlašovací údaje související s vaším předplatným na webu Azure portal. 
5.  Zobrazí se nástroj pro migraci StorSimple uživatelského rozhraní.
  

## <a name="next-steps"></a>Další postup
Stáhněte si podrobné pokyny o tom, jak [migrovat data z StorSimple řad 5000 – 7000 zařízení řady 8000](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b).

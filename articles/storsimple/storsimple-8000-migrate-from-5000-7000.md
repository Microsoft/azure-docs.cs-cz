---
title: Migrace dat na zařízení StorSimple řady 5000 7000 zařízení řady 8000 | Microsoft Docs
description: Poskytuje přehled a požadavky funkci migrace.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/11/2017
ms.author: alkohli
ms.openlocfilehash: 36df62c4b01c623702707d39c6af59f4752ee6e0
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2017
ms.locfileid: "26639865"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>Migrovat data z řady 5000 7000 zařízení StorSimple řady 8000 zařízení

> [!IMPORTANT]
> - Migrace je aktuálně odbornou operace. Pokud máte v úmyslu migrovat data z řady 5000 7000 zařízení StorSimple řady 8000 zařízení, musíte při plánování migrace se službou Microsoft Support. Microsoft Support potom umožní vaše předplatné pro migraci. Další informace najdete v tématu Jak [otevřete lístek podpory](storsimple-8000-contact-microsoft-support.md).
> - Po souboru žádost o služby může trvat několik týdnů k provedení migrace plánu a ve skutečnosti zahájit migraci.
> - Předtím, než se obrátíte na Microsoft Support, být a nezapomeňte najdete [migrace požadavky](#migration-prerequisites) uvedené v článku.

## <a name="overview"></a>Přehled

Tento článek představuje funkci migrace, která umožňuje zákazníkům StorSimple řady 5000 7000 migrovat data do fyzického zařízení řady StorSimple 8000 nebo zařízení 8010/8020 cloudu. Tento článek obsahuje také odkazy na stažení podrobný návod na kroky potřebné k migraci dat z řady starší verze zařízení a 8000 řady fyzické 5000 7000 nebo cloudu zařízení.

Tento článek se použije pro zařízení řady 8000 místně jak cloudu zařízení StorSimple.


## <a name="migration-feature-versus-host-side-migration"></a>Funkce migrace a migrace na straně hostitele

Můžete přesunout data pomocí funkce migrace nebo provedením migrace straně hostitele. Tato část popisuje, jaké jsou specifikace každá metoda včetně výhody a nevýhody. Tyto informace slouží k rozmyslete si, jakou metodu chcete k migraci dat výkonu.

Funkci migrace simuluje procesu zotavení po havárii z řady 7000/5000 řady 8000. Tato funkce umožňuje migraci dat z formátu řad 5000/7000 formátu řady 8000 v Azure. Proces migrace je inicializovat pomocí nástroje Migrace StorSimple. Nástroj spustí stahování a převod metadat zálohy na zařízení řady 8000 a pak používá nejnovější zálohu ke zveřejnění svazky na zařízení.

|      | Odborníci na                                                                                                                                     |Nevýhody                                                                                                                                                              |
|------|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Proces migrace zachová historii zálohování, které byly provedeny na řad 5000/7000.                                               | Když uživatelé pokusí o přístup k datům, tato migrace stáhnou data z Azure, což způsobilo data stažení náklady.                                     |
| 2.   | Žádná data jsou migrována na straně hostitele.                                                                                                     | Tento proces musí výpadek mezi spuštění zálohování a nejnovější zálohy se prezentované na 8000 řady (se dá odhadnout pomocí nástroje migrace). |
| 3.   | Tento proces se zachovají všechny zásady, šablony šířky pásma, šifrování a ostatní nastavení na zařízení řady 8000.                      | Přístup uživatele bude navrácení pouze k datům uživatele a nebude rehydrataci při spotřebě celou datovou sadu.                                                  |
| 4.   | Tento proces vyžaduje další čas převést starší zálohy v Azure, která se provádí asynchronně bez dopadu na produkční | Migrace lze provést pouze na úrovni konfigurace cloudu.  Jednotlivé svazky v konfiguraci cloudu nelze migrovat samostatně                       |

Migrace straně hostitele umožňuje nastavení řady 8000 nezávisle a kopírování dat z řad 5000/7000 zařízení na zařízení řady 8000. Jde o ekvivalent migraci dat z jednoho zařízení úložiště do druhého. Ke zkopírování dat se používají celou řadu nástrojů, jako je například Diskboss robocopy.

|      | Odborníci na                                                                                                                      |Nevýhody                                                                                                                                                                                                      |
|------|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Migrace může použijí postupně na základě svazku svazku.                                               | Předchozí zálohy (pořízené řad 5000/7000) není k dispozici v zařízení řady 8000.                                                                                                       |
| 2.   | Umožňuje pro konsolidaci dat do jednoho účtu úložiště v Azure.                                                       | První zálohování do cloudu na 8000 řady bude trvat delší dobu jako všechna data na 8000 řady musí být zálohovat do Azure.                                                                     |
| 3.   | Po úspěšné migrace je místní na zařízení všechna data. Neexistují žádné latence při přístupu k datům. | Spotřeba úložiště Azure se zvyšuje, dokud data se odstraní ze zařízení 5000/7000.                                                                                                        |
| 4.   |                                                                                                                           | Pokud řady 7000/5000 zařízení má velké množství dat, během migrace tato data musí být stahovány ze služby azure, který je zpoplatněná náklady a latence související ke stahování dat z Azure |

Tento článek se týká pouze funkce migrace ze 5000/7000 zařízení řady 8000. Další informace o migraci na straně hostitele, přejděte na [migrace z jiných zařízení úložiště](http://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating Data to StorSimple Volumes_09-02-15.pdf).

## <a name="migration-prerequisites"></a>Požadavky na migraci

Tady jsou požadavky migrace pro starší verze zařízení řad 5000 a 7000 a zařízení StorSimple řady 8000.

> [!IMPORTANT]
> Zkontrolujte a dokončete migraci požadavky před soubor žádosti o služby se Microsoft Support.

### <a name="for-the-50007000-series-device-source"></a>Pro zařízení řad 5000/7000 (zdroj)

Před zahájením migrace, zajistěte, aby:

* Máte vaší 5000 nebo řady 7000 zdroje zařízení; je možné zařízení za provozu nebo dolů.

    > [!IMPORTANT]
    > Doporučujeme, abyste měli sériový přístup k tomuto zařízení v celém procesu migrace. Mělo by být všechny problémy zařízení, můžete sériového přístupu pomáhají při řešení problémů.

* Zařízení s zdroj řad 5000 a 7000 běží v2.1.1.518 verze softwaru nebo novější. Starší verze nepodporují.
* Pokud chcete ověřit verzi, která je spuštěna vaše řad 5000 a 7000, podívejte se na pravém horním rohu webové uživatelské rozhraní. To by měl zobrazit verzi softwaru, vaše zařízení používá. Pro migraci řad 5000 a 7000 by však být spuštěna v2.1.1.518.

    ![Zkontrolujte verzi softwaru na zařízení starší verze](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Pokud není spuštěna v2.1.1.518 nebo novější, systém upgradujte na požadovanou minimální verzi zařízení za provozu. Podrobné pokyny najdete v části [upgradu systému na v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
    * Pokud používáte v2.1.1.518, přejděte do webového uživatelského rozhraní, pokud jsou k dispozici žádná upozornění k selhání obnovení registru. Pokud registru obnovení nebylo úspěšné, spusťte obnovení registru. Další informace, přejděte na postup [spustit obnovení registru](http://onlinehelp.storsimple.com/111_Appliance/2_User_Guides/1_Current_(v2.1.1)/1_Web_UI_User_Guide_WIP/2_Configuration/4_Cloud_Accounts/1_Cloud_Credentials#Restoring_Backup_Registry).
    * Pokud máte dolů zařízení, které v2.1.1.518 nebyla spuštěna, proveďte převzetí služeb při selhání k nahrazení zařízení, které běží v2.1.1.518. Podrobné pokyny najdete v části zotavení po Havárii zařízení StorSimple řady 5000/7000.
    * Zálohování dat pro vaše zařízení provedením cloudový snímek.
    * Zkontrolujte všechny ostatní aktivní úloh zálohování, které běží na zdrojového zařízení. Sem patří úlohy na hostiteli konzolu ochrany dat StorSimple. Počkejte na dokončení aktuální úlohy.


### <a name="for-the-8000-series-physical-device-target"></a>Pro řady 8000 fyzického zařízení (cíl)

Před zahájením migrace, zajistěte, aby:

* Cíl 8000 řady zařízení je registrované a spuštěna. Další informace najdete v tématu Jak [nasazení zařízení StorSimple pomocí služby StorSimple Manager](storsimple-8000-deployment-walkthrough-u2.md).
* Zařízení řady 8000 má nejnovější StorSimple 8000 řady aktualizace 5 nainstalován a spuštěn 6.3.9600.17845 nebo novější verze. Pokud zařízení nemá nainstalované nejnovější aktualizace, musíte nainstalovat nejnovější aktualizace, abyste mohli pokračovat s migrací. Další informace najdete v tématu Jak [nainstalujte nejnovější aktualizace zařízení řady 8000](storsimple-8000-install-update-5.md).
* Vaše předplatné Azure je povoleno pro migraci. Pokud vaše předplatné není povolena, obraťte se na Microsoft Support povolit vaše předplatné pro migraci.

### <a name="for-the-80108020-cloud-appliance-target"></a>Pro zařízení 8010/8020 cloudu (cíl)

Před zahájením migrace, zkontrolujte:

* Váš cílový cloud zařízení je registrované a spuštěna. Další informace najdete v tématu Jak [nasadit a spravovat zařízení StorSimple cloudu](storsimple-8000-cloud-appliance-u2.md).
* Vaše zařízení cloudu je spuštěna nejnovější StorSimple 8000 řady aktualizací 5 verze softwaru 6.3.9600.17845. Pokud vaše zařízení cloudu není spuštěna aktualizací 5, vytvořte nové zařízení cloudu aktualizací 5 před zahájením migrace. Další informace najdete v tématu Jak [vytvořit zařízení 8010/8020 cloudu](storsimple-8000-cloud-appliance-u2.md).

### <a name="for-the-computer-running-storsimple-migration-tool"></a>Pro počítač s nástrojem StorSimple migrace

Nástroj pro migraci StorSimple je nástroj uživatelského rozhraní, který vám umožní migraci dat z StorSimple řady 5000 7000 zařízení řady 8000. Chcete-li nainstalovat nástroj pro migraci StorSimple, použijte počítač, který splňuje následující požadavky.

Počítač je připojený k Internetu a:

* Používá následující operační systém
    * Windows 10.
    * Windows Server 2012 R2 (nebo vyšší) Chcete-li nainstalovat nástroj pro migraci StorSimple.
* Má nainstalované rozhraní .NET 4.5.2.
* Má minimálně 5 GB volného místa k instalaci a použití nástroje.

> [!TIP]
> Pokud zařízení StorSimple je připojený k hostiteli systému Windows Server, můžete nainstalovat nástroj pro migraci na hostitelském počítači Windows serveru.

#### <a name="to-install-storsimple-migration-tool"></a>Chcete-li nainstalovat nástroj pro migraci StorSimple

Proveďte následující kroky k instalaci nástroje pro migraci StorSimple ve vašem počítači.

1. Zkopírujte složku _StorSimple8000SeriesMigrationTool_ do počítače se systémem Windows. Ujistěte se, zda má jednotka, kde se zkopíruje softwaru dostatek místa.

    Otevřete konfigurační soubor nástroje _StorSimple8000SeriesMigrationTool.exe.config_ ve složce. Zde je fragment kódu souboru.
    
    ```
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. Upravte hodnoty odpovídající klíče a nahraďte:

    * `UserName`– Uživatelské jméno pro přihlášení k portálu Azure.
    * `SubscriptionName and SubscriptionId`– Zadejte název a ID vašeho předplatného Azure. Ve službě StorSimple Manager zařízení úvodní stránka, v části **Obecné**, klikněte na tlačítko **vlastnosti**. Zkopírujte název odběru a ID předplatného související s vaší službou.
    * `ResourceName`– Název služby StorSimple Manager zařízení na portálu Azure. Také uvedené v části Vlastnosti služby.
    * `ResourceGroup`– Název skupiny prostředků, které jsou přidružené k službě StorSimple Manager zařízení na portálu Azure. Také uvedené v části Vlastnosti služby.
    ![Zkontrolujte vlastnosti služby pro cílové zařízení](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId`– Azure Active Directory ID klienta v portálu Azure. Přihlaste se k Microsoft Azure jako správce. Na portálu Microsoft Azure, klikněte na tlačítko **Azure Active Directory**. V části **spravovat**, klikněte na tlačítko **vlastnosti**. ID se zobrazí v klientovi **ID adresáře** pole.
    ![Zkontrolujte ID klienta pro službu Azure Active Directory](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  Uložte změny provedené v konfiguračním souboru.
4.  Spustit _StorSimple8000SeriesMigrationTool.exe_ spustíte nástroj. Po zobrazení výzvy k zadání pověření, zadejte přihlašovací údaje spojené s vaším předplatným na portálu Azure. 
5.  Zobrazí se nástroj pro migraci StorSimple uživatelského rozhraní.
  

## <a name="next-steps"></a>Další kroky
Podrobné pokyny o tom, jak stáhnout [migrovat data z StorSimple řady 5000 7000 zařízení řady 8000](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b).

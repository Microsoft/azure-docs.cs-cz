---
title: Migrace dat řady StorSimple 5000-7000 na zařízení 8000 series | Microsoft Docs
description: Přečtěte si o migraci dat na StorSimple 5000-7000 Series do zařízení 8000 Series a o tom, jaké jsou požadavky na proces migrace.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2020
ms.author: alkohli
ms.openlocfilehash: 58b7b67842e9ba385b34ea4d8fdbcac190076218
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428162"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>Migrace dat z řady StorSimple 5000-7000 na zařízení 8000 series

> [!IMPORTANT]
> - 31. července 2019 bude StorSimple série 5000/7000 získat stav EOS (konec podpory). Zákazníkům řady StorSimple 5000/7000 doporučujeme migrovat na jednu z alternativ popsaných v dokumentu.
> - Migrace je aktuálně pomocná operace. Pokud máte v úmyslu migrovat data ze zařízení řady StorSimple 5000-7000 na zařízení 8000 Series, je potřeba naplánovat migraci pomocí podpora Microsoftu. Podpora Microsoftu pak umožníte vašemu předplatnému migraci. Další informace najdete v tématu Postup [otevření lístku podpory](storsimple-8000-contact-microsoft-support.md).
> - Po započetení žádosti o službu může trvat několik týdnů, než se spustí plán migrace a ve skutečnosti se spustí migrace.
> - Před kontaktováním podpora Microsoftu nezapomeňte zkontrolovat a dokončit [předpoklady migrace](#migration-prerequisites) uvedené v článku.

## <a name="overview"></a>Přehled

Tento článek představuje funkci migrace, která umožňuje zákazníkům řady StorSimple 5000-7000 migrovat svá data na fyzické zařízení StorSimple 8000 Series nebo na cloudové zařízení 8010/8020. Tento článek obsahuje také podrobný názorný postup ke stažení, který je nutný k migraci dat ze zařízení se starší verzí 5000-7000 do fyzického nebo cloudového zařízení řady 8000.

Tento článek se týká jak místního zařízení 8000 Series, tak i StorSimple Cloud Appliance.


## <a name="migration-feature-versus-host-side-migration"></a>Funkce migrace a migrace na straně hostitele

Data můžete přesunout pomocí funkce migrace nebo pomocí migrace na straně hostitele. Tato část popisuje specifiky jednotlivých metod, včetně specialistů a nevýhody. Tyto informace slouží k tomu, abyste zjistili, jakou metodu chcete provést při migraci dat.

Funkce migrace simuluje proces zotavení po havárii (DR) z řady 7000/5000 až 8000 řady. Tato funkce umožňuje migrovat data z 5000/7000 formátu série do formátu 8000 Series v Azure. Proces migrace se iniciuje pomocí nástroje pro migraci StorSimple. Nástroj spustí stahování a převod metadat zálohování na zařízení řady 8000 a potom použije poslední zálohu k vystavení svazků na zařízení.

| Výhody                                                                                                                                     |Nevýhody                                                                                                                                                              |
|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Proces migrace zachovává historii záloh, které byly pořízeny na 5000/7000 Series.                                               | Když se uživatelé pokusí o přístup k datům, Tato migrace stáhne data z Azure, takže budou mít náklady na stažení dat.                                     |
| Na straně hostitele nejsou migrována žádná data.                                                                                                     | Proces vyžaduje prostoje mezi začátkem zálohování a poslední zálohou, která je v řadě 8000 (dá se odhadnout pomocí nástroje pro migraci). |
| Tento proces zachovává všechny zásady, šablony šířky pásma, šifrování a další nastavení na zařízeních řady 8000.                      | Uživatelský přístup vrátí pouze data, ke kterým uživatelé přistupují, a nebude znovu vypínat celou datovou sadu.                                                  |
| Tento proces vyžaduje další čas k převedení všech starších záloh v Azure, které se provádí asynchronně, aniž by to mělo dopad na produkční prostředí. | Migraci je možné provést jenom na úrovni konfigurace cloudu.  Jednotlivé svazky v konfiguraci cloudu se nedají migrovat samostatně.                       |

Migrace na straně hostitele umožňuje nastavení řady 8000 nezávisle a zkopírování dat ze zařízení 5000/7000 Series do zařízení řady 8000. Jedná se o ekvivalent migrace dat z jednoho úložného zařízení na jiný. Ke zkopírování dat se používají různé nástroje, jako je Diskboss, Robocopy.

| Výhody                                                                                                                      |Nevýhody                                                                                                                                                                                                      |
|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| K migraci se dá v rámci jednotlivých svazků přistupovat postupně.                                               | Předchozí zálohy (pořízené v 5000/7000 Series) nebudou na zařízení řady 8000 k dispozici.                                                                                                       |
| Umožňuje konsolidaci dat do jednoho účtu úložiště v Azure.                                                       | První zálohování do cloudu v 8000 Series bude trvat delší dobu, protože všechna data v 8000 Series je potřeba zálohovat do Azure.                                                                     |
| Po úspěšné migraci jsou všechna data na zařízení místní. Při přístupu k datům neexistují žádné latence. | Spotřeba úložiště Azure se zvýší, dokud se neodstraní data ze zařízení 5000/7000.                                                                                                        |
|                                                                                                                           | Pokud má zařízení řady 7000/5000 velké množství dat, je potřeba během migrace tato data stáhnout z Azure, což bude mít za následek náklady a latence související se stahováním dat z Azure. |

Tento článek se zaměřuje jenom na funkci migrace z 5000/7000 na 8000 řady zařízení. Další informace o migraci na straně hostitele najdete na webu [migrace z jiných úložných zařízení](https://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating%20Data%20to%20StorSimple%20Volumes_09-02-15.pdf).

## <a name="migration-prerequisites"></a>Předpoklady migrace

Tady jsou požadavky na migraci pro zařízení se staršími systémy 5000 nebo 7000 a zařízení StorSimple řady 8000.

> [!IMPORTANT]
> Před započetím žádosti o službu pomocí podpora Microsoftu zkontrolujte a dokončete předpoklady migrace.

### <a name="for-the-50007000-series-device-source"></a>Pro zařízení řady 5000/7000 (zdroj)

Než začnete s migrací, ujistěte se, že:

* Máte zdrojové zařízení řady 5000 nebo 7000; zařízení může být živé nebo mimo provoz.

    > [!IMPORTANT]
    > V průběhu procesu migrace doporučujeme, abyste měli k tomuto zařízení sériový přístup. Pokud dojde k problémům se zařízením, může vám přístup k řešení problémů získat sériový přístup.

* Na zdrojovém zařízení řady 5000 nebo 7000 je spuštěný software Version v 2.1.1.518 nebo novější. Starší verze se nepodporují.
* Pokud chcete ověřit verzi, kterou používá vaše série 5000 nebo 7000, podívejte se do pravého horního rohu webového uživatelského rozhraní. Měla by se zobrazit verze softwaru, kterou vaše zařízení používá. Pro migraci by vaše série 5000 nebo 7000 měla běžet v 2.1.1.518.

    ![Kontrolovat verzi softwaru na starším zařízení](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Pokud vaše živé zařízení neběží v 2.1.1.518 nebo novějším, upgradujte prosím systém na požadovanou minimální verzi. Možná budete muset pracovat s podpora Microsoftu, abyste mohli provést upgrade.
    * Pokud používáte v 2.1.1.518, přejděte do webového uživatelského rozhraní a podívejte se, jestli nejsou nějaká oznámení o chybách obnovení registru. Pokud se obnovení registru nepovedlo, spusťte obnovení registru. Možná budete muset pracovat s podpora Microsoftu, abyste mohli obnovit registr.
    * Pokud máte zařízení, které neběží v 2.1.1.518, proveďte převzetí služeb při selhání na náhradní zařízení, které běží v 2.1.1.518. Podrobné pokyny najdete v tématu DR zařízení StorSimple Series 5000/7000.
    * Zálohujte data pro vaše zařízení pořizováním snímku v cloudu.
    * Vyhledejte všechny další aktivní úlohy zálohování, které běží na zdrojovém zařízení. To zahrnuje úlohy na hostiteli konzoly StorSimple data Protection. Počkejte, než se aktuální úlohy dokončí.


### <a name="for-the-8000-series-physical-device-target"></a>Pro fyzické zařízení řady 8000 (cíl)

Než začnete s migrací, ujistěte se, že:

* Vaše cílové zařízení řady 8000 je zaregistrované a spuštěné. Další informace najdete v tématu [nasazení zařízení StorSimple pomocí služby StorSimple Manager](storsimple-8000-deployment-walkthrough-u2.md).
* Zařízení řady 8000 má nainstalovanou nejnovější aktualizaci StorSimple 8000 Series Update 5 a je spuštěná 6.3.9600.17845 nebo novější verze. Pokud vaše zařízení nemá nainstalované nejnovější aktualizace, je potřeba nainstalovat nejnovější aktualizace, abyste mohli pokračovat v migraci. Další informace najdete v tématu [Instalace nejnovější aktualizace na zařízení řady 8000](storsimple-8000-install-update-5.md).
* Vaše předplatné Azure je povolené pro migraci. Pokud vaše předplatné není povolené, kontaktujte podpora Microsoftu, abyste povolili předplatné pro migraci.

### <a name="for-the-80108020-cloud-appliance-target"></a>Pro cloudové zařízení 8010/8020 (cíl)

Před zahájením migrace zajistěte:

* Vaše cílové cloudové zařízení je zaregistrované a spuštěné. Další informace najdete v tématu [nasazení a správa StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).
* Vaše cloudové zařízení používá nejnovější verzi softwaru StorSimple 8000 Series Update 5 6.3.9600.17845. Pokud vaše cloudové zařízení neběží s aktualizací 5, před pokračováním v migraci vytvořte nové cloudové zařízení s aktualizací Update 5. Další informace najdete v tématu Postup [Vytvoření cloudového zařízení 8010/8020](storsimple-8000-cloud-appliance-u2.md).

### <a name="for-the-computer-running-storsimple-migration-tool"></a>Pro počítač, na kterém běží Nástroj pro migraci StorSimple

Nástroj pro migraci StorSimple je nástroj založený na uživatelském rozhraní, který umožňuje migrovat data z řady StorSimple 5000-7000 na zařízení řady 8000. K instalaci nástroje pro migraci StorSimple použijte počítač, který splňuje následující požadavky.

Počítač má připojení k Internetu a:

* Používá následující operační systém
    * Windows 10.
    * Windows Server 2012 R2 (nebo vyšší) k instalaci nástroje pro migraci StorSimple.
* Je nainstalováno rozhraní .NET 4.5.2.
* Má minimálně 5 GB volného místa pro instalaci a používání nástroje.

> [!TIP]
> Pokud je zařízení StorSimple připojené k hostiteli Windows serveru, můžete nástroj pro migraci nainstalovat na hostitelský počítač s Windows serverem.

#### <a name="to-install-storsimple-migration-tool"></a>Instalace nástroje pro migraci StorSimple

Provedením následujících kroků nainstalujete nástroj pro migraci StorSimple do svého počítače.

1. Zkopírujte složku _StorSimple8000SeriesMigrationTool_ do počítače se systémem Windows. Ujistěte se, že je na jednotce, na které se software zkopíroval, dostatek místa.

    Otevřete konfigurační soubor nástroje _StorSimple8000SeriesMigrationTool.exe.config_ ve složce. Zde je fragment souboru.
    
    ```xml
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. Upravte hodnoty odpovídající klíčům a nahraďte:

    * `UserName` – Uživatelské jméno pro přihlášení k Azure Portal.
    * `SubscriptionName and SubscriptionId` – Název a ID vašeho předplatného Azure. Na úvodní stránce služby StorSimple Správce zařízení v části **Obecné** klikněte na **vlastnosti**. Zkopírujte název předplatného a ID předplatného přidruženého k vaší službě.
    * `ResourceName` – Název služby StorSimple Správce zařízení v Azure Portal. Zobrazuje se také v části vlastnosti služby.
    * `ResourceGroup` – Název skupiny prostředků přidružené ke službě StorSimple Správce zařízení v Azure Portal. Zobrazuje se také v části vlastnosti služby.
    ![Ověřte vlastnosti služby pro cílové zařízení.](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId` – Azure Active Directory ID tenanta v Azure Portal. Přihlaste se k Microsoft Azure jako správce. V portál Microsoft Azure klikněte na **Azure Active Directory**. V části **Spravovat** klikněte na **Vlastnosti**. ID tenanta se zobrazí v poli **ID adresáře** .
    ![Ověřte ID tenanta pro Azure Active Directory](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  Uložte změny provedené v konfiguračním souboru.
4.  Spusťte _StorSimple8000SeriesMigrationTool.exe_ pro spuštění nástroje. Po zobrazení výzvy k zadání přihlašovacích údajů zadejte přihlašovací údaje přidružené k vašemu předplatnému v Azure Portal. 
5.  Zobrazí se uživatelské rozhraní nástroje pro migraci StorSimple.
  

## <a name="next-steps"></a>Další kroky
Stáhněte si podrobné pokyny, jak [migrovat data z řady StorSimple 5000-7000 na zařízení řady 8000](https://databoxbuilds.blob.core.windows.net/storsimpledocs/MicrosoftAzureStorSimple_Migration_Guide_20171115.pdf).

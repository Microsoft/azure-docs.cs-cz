---
title: Řešení potíží se senzory a místní konzolou pro správu
description: Vyřešte potíže se senzorem a místní konzolou pro správu, abyste vyloučili případné problémy, které můžete mít.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/14/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: ba68bc3eee94689236792f0270d779357dffde9f
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/15/2021
ms.locfileid: "103465772"
---
# <a name="troubleshoot-the-sensor-and-on-premises-management-console"></a>Řešení potíží se senzory a místní konzolou pro správu

Tento článek popisuje základní nástroje pro řešení potíží pro senzor a místní konzolu pro správu. Kromě zde popsaných položek můžete zjistit stav systému následujícími způsoby:

**Výstrahy**: výstraha se vytvoří, když rozhraní snímače, které monitoruje provoz, je mimo provoz. 

**SNMP**: stav senzoru se sleduje prostřednictvím protokolu SNMP. Azure Defender pro IoT reaguje na dotazy SNMP odeslané z autorizovaného monitorovacího serveru. 

**Systémová oznámení**: když Konzola pro správu ovládá senzor, můžete přeposílat výstrahy týkající se neúspěšných záloh senzorů a odpojených senzorů.

## <a name="sensor-troubleshooting-tools"></a>Nástroje pro řešení potíží senzorů

### <a name="investigate-password-failure-at-initial-sign-in"></a>Při počátečním přihlášení prozkoumat chybu hesla

Při prvním přihlášení k předkonfigurovanému senzoru šipek budete muset provést obnovení hesla.

Postup obnovení hesla:

1. Na přihlašovací obrazovce Defender for IoT vyberte  **obnovení hesla**. Otevře se obrazovka **pro obnovení hesla** .

1. Vyberte buď **CyberX** , nebo **podporu**, a zkopírujte jedinečný identifikátor.

1. Přejděte na Azure Portal a vyberte **weby a senzory**.  

1. Vyberte kartu **obnovit místní heslo konzoly pro správu** .

   :::image type="content" source="media/password-recovery-images/recover-button.png" alt-text="Kliknutím na tlačítko obnovit místní správu Stáhněte soubor pro obnovení.":::

1. Zadejte jedinečný identifikátor, který jste dostali na obrazovce **pro obnovení hesla** , a vyberte **obnovit**. `password_recovery.zip`Soubor se stáhne.

    > [!NOTE]
    > Neměňte soubor pro obnovení hesla. Jedná se o podepsaný soubor a nebude fungovat, pokud s ním budete manipulovat.

1. Na obrazovce **obnovení hesla** vyberte **Odeslat**. Otevře se okno **Odeslat soubor pro obnovení hesla** .

1. Vyberte **Procházet** a vyhledejte `password_recovery.zip` soubor, nebo ho přetáhněte `password_recovery.zip` do okna.

1. Pak se zobrazí možnost **Další** a uživatel a heslo generované systémem pro konzolu pro správu.

    > [!NOTE]
    > Když se přihlásíte ke senzoru nebo místní konzole pro správu nástroje poprvé, připojí se k předplatnému, ke kterému jste připojili. Pokud budete potřebovat resetovat heslo pro CyberX nebo uživatele podpory, budete muset toto předplatné vybrat. Další informace o obnovení hesla uživatele CyberX nebo podpory najdete v tématu [resetování hesla](how-to-create-and-manage-users.md#resetting-passwords).

### <a name="investigate-a-lack-of-traffic"></a>Prozkoumat nedostatek provozu

V horní části konzoly se zobrazí indikátor, pokud senzor rozpozná, že na jednom z konfigurovaných portů není žádný provoz. Tento indikátor je viditelný pro všechny uživatele.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/no-traffic-detected.png" alt-text="Snímek obrazovky s upozorněním, že nebyl zjištěn žádný provoz.":::
 
Po zobrazení této zprávy můžete prozkoumat, kde nedochází k žádným přenosům. Zajistěte, aby byl kabel rozsahu připojen a v architektuře rozpětí se nezměnila žádná změna.  

Informace o podpoře a odstraňování potíží vám poskytne [Podpora Microsoftu](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

### <a name="check-system-performance"></a>Kontrolovat výkon systému 

Když je nasazen nový senzor, nebo například senzor pracuje pomalu nebo nezobrazuje žádné výstrahy, můžete kontrolovat výkon systému.

Postup kontroly výkonu systému:

1. Ujistěte se, že je na řídicím panelu `PPS > 0` .

   :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/dashboard-view-v2.png" alt-text="Snímek obrazovky s ukázkovým řídicím panelem"::: 

1. V postranní nabídce vyberte **zařízení**.

1. V okně **zařízení** se ujistěte, že jsou zařízení zjištěna.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/discovered-devices.png" alt-text="Ujistěte se, že jsou zařízení zjištěná.":::

1. V postranní nabídce vyberte **dolování dat**.

1. V okně **dolování dat** vyberte **vše** a vygenerujte sestavu.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="Vygenerujte novou sestavu pomocí dolování dat.":::

1. Ujistěte se, že sestava obsahuje data.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="Ujistěte se, že sestava obsahuje data.":::

1. V postranní nabídce vyberte **trendy & statistiku**.

1. V okně **trendy & Statistika** vyberte **Přidat widget**.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/add-widget.png" alt-text="Přidejte pomůcku tak, že ji vyberete.":::

1. Přidejte widget a ujistěte se, že zobrazuje data.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/widget-data.png" alt-text="Ujistěte se, že Widget zobrazuje data.":::

1. V postranní nabídce vyberte **výstrahy**. Zobrazí se okno **výstrahy** .

1. Ujistěte se, že se výstrahy vytvořily.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alerts-created.png" alt-text="Ujistěte se, že byly vytvořeny výstrahy.":::


### <a name="investigate-a-lack-of-expected-alerts"></a>Prozkoumat chybějící očekávané výstrahy

Pokud okno **výstrahy** nezobrazuje výstrahu, kterou jste očekávali, ověřte následující:

- Ověřte, zda se stejná výstraha již v okně **výstrahy** zobrazila jako reakce na jinou instanci zabezpečení. Pokud ano a tato výstraha ještě nebyla zpracována, konzola senzorů nezobrazuje novou výstrahu.

- Ujistěte se, že jste toto upozornění nevyloučili pomocí pravidel **vyloučení výstrah** v konzole pro správu. 

### <a name="investigate-widgets-that-show-no-data"></a>Prozkoumat widgety, které nezobrazují žádná data

Když widgety v okně **trendy & Statistika** nezobrazí žádná data, udělejte toto:

- [Kontroluje výkon systému](#check-system-performance).

- Ujistěte se, že nastavení času a oblasti jsou správně nakonfigurované a nejsou nastavená na budoucí čas. 

### <a name="investigate-a-device-map-that-shows-only-broadcasting-devices"></a>Prozkoumat mapu zařízení, která zobrazuje jenom zařízení všesměrového vysílání

Pokud se zařízení zobrazená na mapě jeví jako nepřipojená, může dojít k chybě s konfigurací portu SPAN. To znamená, že se můžete setkat jenom s vysíláním zařízení a bez jednosměrového přenosu.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/broadcasting-devices.png" alt-text="Zobrazení zařízení všesměrového vysílání":::

V takovém případě je třeba ověřit, zda se vám zobrazí pouze přenosy všesměrového vysílání. Pak požádejte síťového inženýra, aby opravil konfiguraci portů rozsahu, abyste viděli přenos jednosměrového vysílání.

Ověření, že se zobrazuje jenom přenos všesměrového vysílání:

- Na obrazovce **dolování dat** vytvořte sestavu pomocí možnosti **vše** . Pak se v sestavě zobrazí pouze přenosy všesměrového a vícesměrového vysílání (a žádný přenos jednosměrového vysílání).

Ani

- Záznam PCAP přímo z přepínače nebo připojení přenosného počítače pomocí nástroje Wireshark.

### <a name="connect-the-sensor-to-ntp"></a>Připojit senzor k NTP

Můžete nakonfigurovat samostatný senzor a konzolu pro správu se senzory, které s ní souvisejí, pro připojení k NTP.

Postup připojení samostatného senzoru k NTP:

- Pokud [potřebujete pomoc, obraťte se na tým podpory](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

Postup připojení senzoru kontrolovaného konzolou pro správu na NTP:

- Připojení k NTP je konfigurováno v konzole pro správu. Všechny senzory, které konzola pro správu řídí, obdrží připojení NTP automaticky.

### <a name="investigate-when-devices-arent-shown-on-the-map-or-you-have-multiple-internet-related-alerts"></a>Prozkoumejte, když se na mapě nezobrazují zařízení, nebo máte více výstrah souvisejících s internetem.

Někdy jsou u zařízení ICS nakonfigurovaná externí IP adresa. Tato zařízení ICS se na mapě nezobrazují. Místo zařízení se na mapě zobrazí internetový Cloud. Do bitové kopie cloudu jsou zahrnuté IP adresy těchto zařízení.

Dalším označením stejného problému je, že se zobrazí více výstrah souvisejících s internetem.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alert-problems.png" alt-text="Několik výstrah souvisejících s internetem.":::

Postup opravy konfigurace:

1. Klikněte pravým tlačítkem na ikonu cloudu na mapě zařízení a vyberte **exportovat IP adresy**. Zkopírujte veřejné rozsahy, které jsou soukromé, a přidejte je do seznamu podsítí. Další informace najdete v tématu [Konfigurace podsítí](how-to-control-what-traffic-is-monitored.md#configure-subnets).

1. Vygenerujte novou sestavu dolování dat pro připojení k Internetu.

1. V sestavě dolování dat vyberte možnost :::image type="icon" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/administrator-mode.png" border="false"::: zadat režim správce a odstraňte IP adresy vašich zařízení ICS.

### <a name="tweak-the-sensors-quality-of-service"></a>Vylepšení kvality služby snímače

Pro uložení síťových prostředků můžete omezit šířku pásma rozhraní, kterou senzor používá ke každodenním postupům.

K omezení šířky pásma rozhraní použijte `cyberx-xsense-limit-interface` Nástroj CLI, který musí být spuštěn s oprávněními sudo. Nástroj získá následující argumenty:

  - `* -i`: rozhraní (příklad: eth0).

  - `* -l`: limit (příklad: 30 kbit/1 Mbit). Můžete použít následující jednotky šířky pásma: KB/s, kbit, Mbit nebo b/s.

  - `* -c`: Clear (pro vymazání omezení šířky pásma rozhraní).

Postup při vylepšení kvality služby:

1. Přihlaste se ke snímači CLI jako Defender pro uživatele IoT a zadejte `sudo cyberx-xsense-limit-interface-I eth0 -l value` .

   Příklad: `sudo cyberx-xsense-limit-interface -i eth0 -l 30kbit`

   > [!NOTE]
   > V případě fyzického zařízení použijte rozhraní EM1.

1. Omezení rozhraní vymažete tak, že zadáte `sudo cyberx-xsense-limit-interface -i eth0 -l 1mbps -c` .

## <a name="on-premises-management-console-troubleshooting-tools"></a>Nástroje pro řešení potíží s místními konzolami pro správu

### <a name="investigate-a-lack-of-expected-alerts"></a>Prozkoumat chybějící očekávané výstrahy

Pokud v okně **výstrahy** není zobrazená očekávaná výstraha, ověřte následující:

- Ověřte, zda se stejná výstraha již v okně **výstrahy** zobrazila jako reakce na jinou instanci zabezpečení. Pokud ano a tato výstraha ještě nebyla zpracována, zobrazí se nová výstraha.

- Ověřte, že jste toto upozornění nevyloučili pomocí pravidel **vyloučení výstrah** v místní konzole pro správu.  

### <a name="tweak-the-quality-of-service"></a>Vylepšení kvality služby

Pokud chcete uložit síťové prostředky, můžete omezit počet výstrah odeslaných externím systémům (jako jsou e-maily nebo SIEM) v jedné operaci synchronizace mezi zařízením a místní konzolou pro správu.

Výchozí hodnota je 50. To znamená, že v jedné komunikační relaci mezi zařízením a místní konzolou pro správu nebude k externím systémům k dispozici více než 50 upozornění. 

Chcete-li omezit počet výstrah, použijte `notifications.max_number_to_report` vlastnost dostupnou v `/var/cyberx/properties/management.properties` . Po změně této vlastnosti není nutné žádné restartování.

Postup při vylepšení kvality služby:

1. Přihlaste se jako Defender pro uživatele IoT. 

1. Ověřte výchozí hodnoty:

   ```bash
   grep \"notifications\" /var/cyberx/properties/management.properties
   ```

   Zobrazí se následující výchozí hodnoty:

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. Upravte výchozí nastavení:

   ```bash
   sudo nano /var/cyberx/properties/management.properties
   ```

1. Upravte nastavení následujících řádků:

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. Uložte změny. Není nutné žádné restartování.

## <a name="export-information-for-troubleshooting"></a>Exportovat informace pro řešení potíží

Kromě nástrojů pro monitorování a analýzu vaší sítě můžete odeslat informace týmu podpory k dalšímu šetření. Při exportu protokolů senzor automaticky vygeneruje jednorázové heslo (JEDNORÁZOVé heslo), které jsou jedinečné pro exportované protokoly, v samostatném textovém souboru. 

Export protokolů:

1. V levém podokně vyberte **nastavení systému**.

1. Vyberte **Export protokolů**.

    :::image type="content" source="media/how-to-export-information-for-troubleshooting/export-a-log.png" alt-text="Exportujte protokol do systémové podpory.":::

1. Do pole **název souboru** zadejte název souboru, který chcete použít pro export protokolu. Výchozí hodnota je aktuální datum.

1. Chcete-li definovat, jaká data chcete exportovat, vyberte kategorie dat:  

    | Exportovat kategorii | Popis |
    |--|--|
    | **Protokoly operačního systému** | Tuto možnost vyberte, pokud chcete získat informace o stavu operačního systému. |
    | **Protokoly instalace a upgradu** | Tuto možnost vyberte, pokud chcete prozkoumat parametry konfigurace instalace a upgradu. |
    | **Výstup systémových správnosti** | Tuto možnost vyberte, pokud chcete kontrolovat výkon systému. |
    | **Roztříděné protokoly** | Tuto možnost vyberte, pokud chcete, aby bylo umožněno rozšířené prověřování protokolu. |
    | **Výpisy jádra operačního systému** | Tuto možnost vyberte, pokud chcete exportovat výpis paměti jádra. Výpis paměti jádra obsahuje veškerou paměť, kterou jádro používá v době problému, ke kterému došlo v tomto jádru. Velikost souboru s výpisem paměti je menší, než úplný výpis paměti. Obvykle se soubor výpisu paměti nachází přibližně o jednu třetinu velikosti fyzické paměti v systému. |
    | **Předávání protokolů** | Tuto možnost vyberte, pokud chcete prozkoumat pravidla předávání. |
    | **Protokoly SNMP** | Tuto možnost vyberte, pokud chcete dostávat informace o kontrole stavu protokolu SNMP. |
    | **Základní protokoly aplikací** | Tuto možnost vyberte, pokud chcete exportovat data týkající se základní konfigurace aplikace a operace. |
    | **Komunikace s protokoly CM** | Tuto možnost vyberte, pokud dochází k nepřetržitým problémům nebo přerušení připojení s konzolou pro správu. |
    | **Protokoly webových aplikací** | Tuto možnost vyberte, pokud chcete získat informace o všech žádostech odeslaných z webového rozhraní aplikace. |
    | **Zálohování systému** | Tuto možnost vyberte, pokud chcete exportovat zálohu všech systémových dat pro účely zkoumání přesného stavu systému. |
    | **Vystavení statistik** | Tuto možnost vyberte, pokud chcete, aby byla umožněna Pokročilá kontrola statistik protokolu. |
    | **Protokoly databáze** | Tuto možnost vyberte, pokud chcete protokoly exportovat ze systémové databáze. Zkoumání systémových protokolů pomáhá identifikovat problémy systému. |
    | **Konfigurace** | Tuto možnost vyberte, pokud chcete exportovat informace o všech konfigurovatelných parametrech, abyste měli jistotu, že všechno je správně nakonfigurované. |

1. Chcete-li vybrat všechny možnosti, vyberte možnost **Vybrat vše** vedle položky vybrat **kategorie**.

1. Vyberte **Export protokolů**.

Exportované protokoly se přidají do seznamu **archivovaných protokolů** . Odešlete jednorázové heslo do týmu podpory v samostatné zprávě a střednímu z exportovaných protokolů. Tým podpory bude moci extrahovat exportované protokoly pouze pomocí jedinečného jednorázového hesla, které se používá k šifrování protokolů.

Seznam archivovaných protokolů může obsahovat až pět položek. Pokud počet položek v seznamu překročí toto číslo, nejstarší položka se odstraní.

## <a name="see-also"></a>Viz také

- [Zobrazení upozornění](how-to-view-alerts.md)

- [Nastavení monitorování SNMP MIB](how-to-set-up-snmp-mib-monitoring.md)

- [Vysvětlení událostí odpojení senzorů](how-to-manage-sensors-from-the-on-premises-management-console.md#understand-sensor-disconnection-events)

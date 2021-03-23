---
title: Správa senzorů z místní konzoly pro správu
description: Naučte se spravovat senzory z konzoly pro správu, včetně aktualizací verzí senzorů, nastavení systému na senzory a povolení a zakázání motorů na senzory.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: 4706fbcf58b8e5f5eed6532f9a08f65c7716e07e
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781921"
---
# <a name="manage-sensors-from-the-management-console"></a>Správa senzorů z konzoly pro správu

Tento článek popisuje, jak spravovat senzory z konzoly pro správu, včetně těchto:

- Nabízení nastavení systému pro senzory

- Povolit a zakázat motory na senzorech

- Aktualizovat verze snímače

## <a name="push-configurations"></a>Konfigurace nabízených oznámení

Můžete definovat různá nastavení systému a automaticky je použít na senzory, které jsou připojené ke konzole pro správu. Tím ušetříte čas a pomáháte zajistit zjednodušená nastavení napříč vašimi podnikovými senzory.

V konzole pro správu můžete definovat následující nastavení systému snímače:

- Poštovní server

- Sledování protokolu SNMP MIB

- Active Directory

- Nastavení DNS

- Podsítě

- Aliasy portů

Postup použití nastavení systému:

1. V levém podokně konzoly vyberte **nastavení systému**.

2. V podokně **Konfigurovat senzory** vyberte jednu z možností.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-system-setting-options.png" alt-text="Možnosti nastavení systému pro senzor.":::

   Následující příklad popisuje, jak definovat parametry poštovního serveru pro své podnikové senzory.

3. Vyberte **poštovní server**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="Z obrazovky nastavení systému vyberte svůj poštovní server.":::

4. Vyberte senzor vlevo.

5. Nastavte parametry poštovního serveru a vyberte **Duplikovat**. U každé položky ve stromové struktuře senzoru se zobrazí zaškrtávací políčko vedle sebe.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/check-off-each-sensor.png" alt-text="Ujistěte se, že jsou pro vaše senzory zaškrtnutá políčka.":::

6. Ve stromové struktuře senzorů vyberte položky, pro které chcete použít konfiguraci.

7. Vyberte **Uložit**.

## <a name="update-versions"></a>Aktualizované verze

V místní konzole pro správu můžete aktualizovat několik senzorů současně.

Postup aktualizace několika senzorů:

1. Přejděte na [Azure Portal](https://portal.azure.com/).

2. Přejít na Azure Defender pro IoT.

3. Přejít na stránku **aktualizace** .

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/update-screen.png" alt-text="Snímek obrazovky s zobrazením řídicího panelu aktualizací":::

4. V části **senzory** vyberte **Stáhnout** a soubor uložte.

5. Přihlaste se ke konzole pro správu a vyberte **nastavení systému**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/admin-system-settings.png" alt-text="Snímek obrazovky nabídky pro správu a vyberte nastavení systému.":::

6. Označte senzory, které chcete aktualizovat v části **Konfigurace modulu senzorů** , a pak vyberte **Automatické aktualizace**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensors-select.png" alt-text="Dva senzory ukazující výukový režim a automatické aktualizace.":::

7. Vyberte **Uložit změny**.

8. V podokně **Upgrade verze senzory** vyberte :::image type="icon" source="media/how-to-manage-sensors-from-the-on-premises-management-console/plus-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/display-files.png" alt-text="Obrazovka upgrade verze senzoru k zobrazení souborů":::

9. Otevře se dialogové okno **nahrát soubor** . Nahrajte soubor, který jste stáhli ze stránky **aktualizace** .

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/upload-file.png" alt-text="Kliknutím na tlačítko Procházet odešlete soubor.":::

10. Během procesu aktualizace se v okně **Správa lokalit** zobrazí stav aktualizace každého snímače.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/progress.png" alt-text="Sledujte průběh aktualizace.":::

## <a name="update-threat-intelligence-packages"></a>Aktualizace balíčků pro analýzu hrozeb 

Balíček dat pro analýzu hrozeb je k dispozici u každého nového programu Defender pro verzi IoT nebo v případě potřeby mezi verzemi. Balíček obsahuje signatury (včetně signatur malwaru), CVEs a další obsah zabezpečení. 

Tento soubor můžete ručně nahrát z webu Defender pro **aktualizace** portálu pro IoT Portal a automaticky ho aktualizovat na senzory. 

Aktualizace dat analýzy hrozeb: 

1. Přejít na stránku **aktualizace** Defenderu pro IoT. 

2. Stáhněte a uložte soubor.

3. Přihlaste se ke konzole pro správu. 

4. V postranní nabídce vyberte **nastavení systému**. 

5. Vyberte senzory, které by měly přijmout aktualizaci v oddílu **Konfigurace modulu senzorů** .  

6. V části **Vybrat data analýzy hrozeb** vyberte znaménko plus ( **+** ). 

7. Nahrajte balíček, který jste stáhli, ze stránky Defender pro **aktualizace** IoT.

## <a name="understand-sensor-disconnection-events"></a>Vysvětlení událostí odpojení senzorů

V okně **Site Manager** se zobrazí informace o odpojení, pokud se senzory odpojí od přiřazené místní konzoly pro správu. K dispozici jsou následující informace o odpojení senzoru:

- "Místní Konzola pro správu nemůže zpracovat data přijatá ze senzoru."

- "Bylo zjištěno posunu časů. Místní Konzola pro správu byla odpojena od senzoru. "

- "Senzor nekomunikuje s místní konzolou pro správu. Ověřte připojení k síti nebo ověření certifikátu. "

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="Snímek obrazovky s zobrazením zóny 1":::

Pomocí informací o odpojených senzorech můžete posílat upozornění třetím stranám. Další informace najdete v tématu [výstrahy při selhání senzoru před selháním](how-to-manage-individual-sensors.md#forward-sensor-failure-alerts).

## <a name="enable-or-disable-sensors"></a>Povolit nebo zakázat senzory

Senzory jsou chráněny pěti Defendery pro moduly IoT. Moduly pro připojené senzory můžete povolit nebo zakázat.

| Modul | Popis | Ukázkový scénář |
|--|--|--|
| Modul porušení protokolu | K porušení protokolu dojde, pokud struktura paketu nebo hodnoty polí neodpovídají specifikaci protokolu. | Výstraha "Neplatná operace MODBUS (kód funkce nula)". Tato výstraha indikuje, že primární zařízení odeslalo do sekundárního zařízení požadavek s kódem funkce 0. Tato možnost není povolena podle specifikace protokolu a sekundární zařízení nemusí správně zpracovat vstup. |
| Modul porušení zásad | Porušení zásad probíhá u odchylky od chování standardních hodnot definovaných ve zjištěné nebo nakonfigurované zásadě. | Výstraha "neautorizovaný agent uživatele protokolu HTTP". Tato výstraha indikuje, že se aplikace, kterou zásada nezískala nebo schválila, používá jako klient HTTP na zařízení. Může se jednat o nový webový prohlížeč nebo aplikaci v tomto zařízení. |
| Malwarový stroj | Malware detekuje aktivitu škodlivou v síti. | Výstraha "podezření na škodlivou aktivitu (Stuxnet)". Tato výstraha indikuje, že senzor nalezl podezřelou síťovou aktivitu známou jako v souvislosti s malwarem Stuxnet, což je pokročilá trvalá hrozba zaměřená na průmyslové řízení a SCADA sítě. |
| Modul anomálií | Malwarový modul detekuje anomálii v chování sítě. | "Periodické chování v komunikačním kanálu" Toto je komponenta, která kontroluje síťová připojení a vyhledává pravidelné nebo cyklické chování přenosu dat, což je běžné v průmyslových sítích. |
| Provozní modul | Tento modul detekuje provozní incidenty nebo nefunkční entity. | `Device is Suspected to be Disconnected (Unresponsive)` Upozornění. Tato výstraha se aktivuje, když zařízení nereaguje na žádné požadavky na předdefinované období. Může to znamenat vypnutí zařízení, odpojení nebo selhání.
|

Pokud chcete povolit nebo zakázat motory pro připojené senzory:

1. V levém podokně konzoly vyberte **nastavení systému**.

2. V části **Konfigurace modulu senzorů** vyberte možnost **Povolit** nebo **Zakázat** pro moduly.
         
3. Vyberte **Uložit změny**.

   Červený vykřičník se zobrazí, pokud dojde k neshodě povolených modulů na jednom z vašich podnikových senzorů. Modul byl pravděpodobně zakázán přímo ze senzoru.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/red-exclamation-example.png" alt-text="Neshoda povolených modulů"::: 

## <a name="define-sensor-backup-schedules"></a>Definování plánů zálohování senzorů

Můžete naplánovat zálohování senzorů a provádět zálohy senzorů na vyžádání z místní konzoly pro správu. To pomáhá chránit před selháním pevného disku a ztrátou dat.

- Co je zálohované: konfigurace a data.

- Co není zálohovaných: PCAP soubory a protokoly. Můžete ručně zálohovat a obnovovat PCAPs a protokoly.

Ve výchozím nastavení se senzory automaticky zálohují v 3:00, denně. Funkce plán zálohování pro senzory umožňuje shromažďovat tyto zálohy a ukládat je do místní konzoly pro správu nebo na externím záložním serveru. Kopírování souborů ze senzorů do místní konzoly pro správu probíhá přes zašifrovaný kanál.

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-backup-schedule-screen.png" alt-text="Zobrazení záložní obrazovky snímače.":::

Když se změní výchozí umístění zálohy senzoru, místní Konzola pro správu automaticky načte soubory z nového umístění na senzoru nebo externího umístění za předpokladu, že konzola má oprávnění pro přístup k umístění. 

V případě, že senzory nejsou registrovány v místní konzole pro správu, zobrazí se v dialogovém okně **plán zálohování senzorů** , že nejsou spravovány žádné senzory.  

Proces obnovení je stejný bez ohledu na to, kde jsou soubory uloženy.

### <a name="backup-storage-for-sensors"></a>Úložiště zálohování pro senzory

V místní konzole pro správu můžete spravovat až devět záloh pro každý spravovaný senzor za předpokladu, že zálohovaný soubor nepřekračuje maximální přidělené místo pro zálohování. 

Dostupné místo se počítá na základě modelu konzoly pro správu, se kterým pracujete: 

- **Produkční model**: výchozí úložiště je 40 GB; limit je 100 GB. 

- **Střední model**: výchozí úložiště je 20 GB; limit je 50 GB. 

- **Model přenosného počítače**: výchozí úložiště je 10 GB; limit je 25 GB. 

- **Tenký model**: výchozí úložiště je 2 GB; limit je 4 GB. 

- **Robustní model**: výchozí úložiště je 10 GB; limit je 25 GB. 

Výchozí přidělení se zobrazí v dialogovém okně **plán zálohování senzoru** . 

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-mail-server-configuration.png" alt-text="Obrazovka pro úpravu konfigurace poštovního serveru.":::

Při zálohování na externí server neexistuje žádný limit úložiště. V poli vlastní cesta k **plánu zálohování senzorů** musíte ale definovat horní limit přidělení  >   . Podporují se následující čísla a znaky: `/, a-z, A-Z, 0-9, and _` . 

Tady jsou informace o překročení limitů úložiště přidělení:

- Pokud překročíte přidělený prostor úložiště, senzor se nezálohuje. 

- Pokud zálohujete více než jeden senzor, Konzola pro správu se pokusí načíst soubory senzorů pro spravované senzory.  

- Pokud načtení z jednoho senzoru překročí limit, Konzola pro správu se pokusí načíst informace o zálohování z dalšího senzoru. 

Když překročíte zadržený počet záloh, odstraní se nejstarší zálohovaný soubor, který bude přizpůsoben novému.

Záložní soubory senzorů jsou automaticky pojmenovány v následujícím formátu: `<sensor name>-backup-version-<version>-<date>.tar` . Příklad: `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`. 

Chcete-li zálohovat senzory:

1. V okně **nastavení systému** vyberte **naplánovat zálohování senzorů** . Senzory, které spravuje vaše místní Konzola pro správu, se zobrazí v dialogovém okně **plán zálohování senzoru** .  

2. Povolte přepínač **shromáždit zálohy** .  

3. Vyberte interval kalendáře, datum a časové pásmo. Formát času vychází z 24 hodin. Zadejte například 6:00 PM jako **18:00**. 

4. Do pole **přidělení úložiště zálohování** zadejte úložiště, které chcete pro své zálohy přidělit. Budete upozorněni, pokud překročíte maximální místo.

5. V poli **Zachovat poslední** určete počet záloh na senzor, který chcete zachovat. Při překročení limitu se nejstarší záloha odstraní.  

6. Zvolit umístění zálohy:  

   - Chcete-li zálohovat do místní konzoly pro správu, zakažte přepínač **vlastní cesta** . Výchozí umístění je `/var/cyberx/sensor-backups` .  

   - Pokud chcete zálohovat na externí server, povolte přepínač **vlastní cesty** a zadejte umístění. Podporují se následující čísla a znaky: `/, a-z, A-Z, 0-9, and, _` . 

7. Vyberte **Uložit**. 

Postup zálohování hned: 

- Vyberte **Zálohovat nyní**. Místní Konzola pro správu vytvoří a shromáždí záložní soubory senzorů. 

### <a name="receiving-backup-notifications-for-sensors"></a>Přijímání zálohovacích oznámení pro senzory 

Dialogové okno **plán zálohování senzoru** a protokol zálohování automaticky vypíše informace o úspěšných a neúspěšných zálohování.  

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-location.png" alt-text="Podívejte se na senzory a tam, kde se nacházejí, a všechny relevantní informace.":::

K selhání může dojít z těchto důvodů:    

- Nebyl nalezen žádný záložní soubor. 

- Soubor byl nalezen, ale nelze jej načíst.  

- Došlo k chybě připojení k síti. 

- K místní konzole pro správu není přidělen dostatek místa pro dokončení zálohování.  

Když dojde k selhání, můžete odeslat e-mailové oznámení, aktualizace syslog a systémová oznámení. Provedete to tak, že v **systémových oznámeních** vytvoříte předávací pravidlo. 

### <a name="restoring-sensors"></a>Obnovování senzorů 

Zálohy můžete obnovit z místní konzoly pro správu a pomocí rozhraní příkazového řádku.  

Postup obnovení z konzoly: 

- V okně nastavení **systému senzoru** vyberte **Obnovit obrázek** .

  :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/restore.png" alt-text="Obnovte zálohu své image.":::

  Konzola pak zobrazí chyby obnovení.  

Obnovení pomocí rozhraní příkazového řádku: 

- Přihlaste se k účtu správce a zadejte `$ sudo cyberx-xsense-system-restore` . 

### <a name="save-a-sensor-backup-to-an-external-smb-server"></a>Uložení zálohy senzoru na externí server SMB

Pokud chcete nastavit server SMB, abyste mohli uložit zálohu senzorů na externí disk: 

1. Vytvořte sdílenou složku na externím serveru SMB. 

2. Získejte cestu ke složce, uživatelské jméno a heslo potřebné pro přístup k serveru SMB. 

3. V programu Defender pro IoT vytvořte adresář pro zálohování: 

   `sudo mkdir /<backup_folder_name_on_server>` 

   `sudo chmod 777 /<backup_folder_name_on_server>/` 

4. Upravit fstab:  

   `sudo nano /etc/fstab` 

   `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

5. Upravte nebo vytvořte přihlašovací údaje pro sdílení. Toto jsou přihlašovací údaje pro server SMB: 

   `sudo nano /etc/samba/user` 

6. Přidejte:  

   `username=<user name>` 

   `password=<password>` 

7. Připojte adresář: 

   `sudo mount -a` 

8. Nakonfigurujte záložní adresář do sdílené složky v programu Defender pro IoT snímač:  

   `sudo nano /var/cyberx/properties/backup.properties` 

9. Nastavte `Backup.shared_location` na `<backup_folder_name_on_cyberx_server>` .

## <a name="see-also"></a>Viz také

[Správa individuálních senzorů](how-to-manage-individual-sensors.md)

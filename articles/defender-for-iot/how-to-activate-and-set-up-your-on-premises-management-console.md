---
title: Aktivace a nastavení místní konzoly pro správu
description: Aktivace a nastavení konzoly pro správu zajišťuje registraci senzorů v Azure a odesílání informací do místní konzole pro správu a místní Konzola pro správu provádí úlohy správy na připojených senzorech.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: d326a90ffb957604dba74982d8983acedc6ab85d
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522576"
---
# <a name="activate-and-set-up-your-on-premises-management-console"></a>Aktivace a nastavení místní konzoly pro správu 

Aktivace a nastavení místní konzoly pro správu zajišťuje:

- Síťová zařízení, která sledujete prostřednictvím připojených senzorů, se zaregistrují pomocí účtu Azure.

- Senzory odesílají informace do místní konzoly pro správu.

- Místní Konzola pro správu provádí úlohy správy na připojených senzorech.

- Máte nainstalovaný certifikát SSL.

## <a name="sign-in-for-the-first-time"></a>První přihlášení

Přihlášení do konzoly pro správu:

- Otevřete webový prohlížeč a zadejte IP adresu a heslo, které jste obdrželi pro místní konzolu pro správu během instalace systému. Pokud jste zapomněli heslo, vyberte možnost **obnovit heslo** a pak se podívejte na [obnovení hesla](how-to-manage-the-on-premises-management-console.md#password-recovery).

## <a name="upload-an-activation-file"></a>Nahrát aktivační soubor

Po prvním přihlášení aktivujte místní konzolu pro správu stažením aktivačního souboru na stránce s **cenami** na portálu Azure Defender pro IoT Portal. Tento soubor obsahuje agregovaná potvrzená zařízení definovaná během procesu připojování. **Potvrzená zařízení** označují počet zařízení, která bude Defender pro IoT monitorovat podle předplatného.

Postup nahrání aktivačního souboru:

1. Přejít na stránku s **cenami** v programu Defender for IoT.
1. Vyberte možnost **Stáhnout aktivační soubor pro kartu Konzola pro správu** . Aktivační soubor se stáhne.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="Stáhněte si aktivační soubor.":::

1. V konzole pro správu vyberte **nastavení systému** .
1. Vyberte **Aktivace**.
1. Vyberte možnost **zvolit soubor** a vyberte soubor, který jste uložili.

Po počáteční aktivaci může počet monitorovaných zařízení překročit počet potvrzených zařízení, která jsou definovaná během připojování. K tomu může dojít například v případě, že k konzoli pro správu připojíte více senzorů. Pokud dojde k nesouladu mezi počtem monitorovaných zařízení a počtem potvrzených zařízení, zobrazí se v konzole pro správu upozornění. Pokud k tomu dojde, měli byste nahrát nový soubor aktivace.

## <a name="set-up-a-certificate"></a>Nastavení certifikátu

Po instalaci konzoly pro správu se vygeneruje místní certifikát podepsaný svým držitelem, který se použije pro přístup ke konzole nástroje. Po prvním přihlášení správce ke konzole pro správu se tomuto uživateli zobrazí výzva k připojení certifikátu protokolu SSL/TLS. 

K dispozici jsou dvě úrovně zabezpečení:

- Nahrajte certifikát podepsaný certifikační autoritou a vyžádejte si konkrétní požadavky na certifikát a šifrování, které vaše organizace požaduje.
- Umožňuje ověření mezi konzolou pro správu a připojenými senzory. Ověření se vyhodnocuje na základě seznamu odvolaných certifikátů a data vypršení platnosti certifikátu. *Pokud se ověření nepovede, dojde k zastavení komunikace mezi konzolou pro správu a senzorem a v konzole se zobrazí chyba ověření.* Tato možnost je po instalaci povolena ve výchozím nastavení.  

Konzola podporuje následující typy certifikátů:

- Privátní a podniková infrastruktura klíčů (privátní infrastruktura veřejných klíčů)

- Infrastruktura veřejných klíčů (veřejná infrastruktura veřejných klíčů)

- Místně generované na zařízení (místně podepsané svým držitelem) 

  > [!IMPORTANT]
  > Doporučujeme nepoužívat certifikát podepsaný svým držitelem. Certifikát není zabezpečený a měl by se používat jenom pro testovací prostředí. Vlastníka certifikátu nelze ověřit a zabezpečení systému nelze udržovat. Pro produkční sítě nikdy tuto možnost nepoužívejte.

Postup nahrání certifikátu:

1. Až se vám zobrazí výzva po přihlášení, zadejte název certifikátu.
1. Nahrajte soubory CRT a klíče.
1. Zadejte heslo a v případě potřeby nahrajte soubor PEM.

Po nahrání certifikátu podepsaného certifikační autority může být nutné aktualizovat obrazovku.

Zakázání ověřování mezi konzolou pro správu a připojenými senzory:

1. Vyberte **Další**.
1. Vypněte přepínač **Povolit ověřování na úrovni systému** .

Informace o nahrání nového certifikátu, podporovaných souborech certifikátů a souvisejících položkách najdete v tématu [Správa místní konzoly pro správu](how-to-manage-the-on-premises-management-console.md).

## <a name="connect-sensors-to-the-on-premises-management-console"></a>Připojení senzorů k místní konzole pro správu

Je nutné zajistit, aby senzory odesílaly informace do místní konzoly pro správu a aby místní Konzola pro správu mohla provádět zálohování, spravovat výstrahy a provádět další činnosti na senzorech. Uděláte to tak, že pomocí následujících postupů ověříte, že provedete počáteční připojení mezi snímači a místní konzolou pro správu.

K dispozici jsou dvě možnosti propojení programu Azure Defender pro IoT senzory s místní konzolou pro správu:

- Připojení z konzoly senzorů

- Připojení pomocí tunelového propojení

Po připojení musíte nastavit lokalitu s těmito senzory.

### <a name="connect-sensors-from-the-sensor-console"></a>Připojení senzorů z konzoly senzorů

Připojení specifických senzorů k místní konzole pro správu z konzoly snímače:

1. V levém podokně konzoly senzorů vyberte **nastavení systému**.

2. Vyberte **připojení ke správě**.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-not-connected.png" alt-text="Snímek obrazovky se stavovým oknem místní konzoly pro správu se zobrazením nepřipojeného":::

3. Do textového pole **adresa** zadejte IP adresu místní konzoly pro správu, ke které se chcete připojit.

4. Vyberte **Connect** (Připojit). Změny stavu:

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-connected.png" alt-text="Snímek obrazovky se stavovým oknem místní konzoly pro správu, které se zobrazuje jako připojené":::

### <a name="connect-sensors-by-using-tunneling"></a>Propojení senzorů pomocí tunelování

Povolení zabezpečeného tunelového propojení mezi senzory organizace a místní konzolou pro správu. Toto nastavení obchází interakce s bránou firewall organizace a výsledkem je omezení prostoru pro útoky.

Použití tunelového propojení umožňuje připojit se k místní konzole pro správu z jeho IP adresy a jednoho portu (to znamená 9000) na jakýkoli senzor.

Nastavení tunelového propojení v místní konzole pro správu:

- Přihlaste se k místní konzole pro správu a spusťte následující příkazy:

  ```bash
  cyberx-management-tunnel-enable
  service apache2 reload
  sudo cyberx-management-tunnel-add-xsense --xsenseuid <sensorIPAddress> --xsenseport 9000
  service apache2 reload
  ```

Nastavení tunelového propojení na senzor:

1. Na senzoru (Network. Properties) otevřete ručně port TCP 9000. Pokud port není otevřený, senzor odmítne připojení z místní konzoly pro správu.

2. Přihlaste se ke každému senzoru a spusťte následující příkazy:

   ```bash
   sudo cyberx-xsense-management-connect -ip <centralmanagerIPAddress>
   sudo cyberx-xsense-management-tunnel
   sudo vi /var/cyberx/properties/network.properties
   opened_tcp_incoming_ports=22,80,443,102,9000
   sudo cyberx-xsense-network-validation
   sudo /etc/network/if-up.d/iptables-recover
   sudo iptables -nvL
   ```

## <a name="set-up-a-site"></a>Nastavení lokality

Výchozí podniková mapa poskytuje celkový přehled o vašich zařízeních v závislosti na několika úrovních geografických umístění.

Zobrazení zařízení může být vyžadováno, pokud jsou organizační struktura a uživatelská oprávnění složitá. V těchto případech může být nastavení lokality určeno globální strukturou organizace, kromě standardní struktury lokalit nebo zón.

Pro podporu tohoto prostředí potřebujete vytvořit globální obchodní topologii, která je založená na organizačních jednotkách, oblastech, webech a zónách vaší organizace. K těmto entitám musíte také definovat přístupová oprávnění uživatelů pomocí skupin přístupu.

Přístupové skupiny umožňují lepší kontrolu nad tím, kde uživatelé spravují a analyzují zařízení v programu Defender pro IoT Platform.

### <a name="how-it-works"></a>Jak to funguje

Pro každou lokalitu můžete definovat obchodní jednotku a oblast. Pak můžete přidat zóny, což jsou logické entity v síti. 

Pro každou zónu byste měli přiřadit aspoň jeden senzor. Model s pěti úrovněmi poskytuje flexibilitu a členitost potřebnou k zajištění systému ochrany, který odráží strukturu vaší organizace.

Své weby můžete upravovat přímo z libovolného zobrazení mapy. Při otvírání lokality z zobrazení mapy se počet otevřených výstrah zobrazí vedle každé zóny.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/console-map-with-data-overlay-v2.png" alt-text="Snímek obrazovky místní konzoly pro správu s překrytím dat v Berlín":::

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/diagram-of-sensor-showing-relationships.png" alt-text="Diagram znázorňující senzory a regionální vztah":::

Nastavení lokality:

1. Přidejte nové obchodní jednotky, které budou odpovídat logické struktuře vaší organizace.

2. Přidejte nové oblasti, aby odrážely oblasti vaší organizace.

3. Přidat web.

4. Přidání zón do lokality.

5. Připojte senzory.

6. Přiřaďte senzor k zónám webu.

Postup přidání obchodních jednotek:

1. V podnikovém zobrazení vyberte **všechny lokality**  >  **spravovat obchodní jednotky**.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-business-unit-screen.png" alt-text="Snímek obrazovky znázorňující zobrazení spravovat organizační jednotky":::

2. Zadejte název nové organizační jednotky a vyberte **Přidat**.

Přidání nové oblasti:

1. V podnikovém zobrazení vyberte **všechny oblasti**  >  **Spravovat oblasti**.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-regions-screen.png" alt-text="Snímek obrazovky znázorňující zobrazení Správa oblastí":::

2. Zadejte název nové oblasti a vyberte **Přidat**.

Přidání nového webu:

1. V podnikovém zobrazení vyberte :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/new-site-icon.png" border="false"::: na horním panelu. Kurzor se zobrazí jako znaménko plus ( **+** ).

2. Umístěte umístění do **+** umístění nové lokality a vyberte ho. Otevře se dialogové okno **vytvořit nový web** .

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-site-screen.png" alt-text="Snímek obrazovky s zobrazením pro vytvoření nového webu":::

3. Zadejte název a fyzickou adresu pro novou lokalitu a vyberte **Uložit**. Nová lokalita se zobrazí na mapě webu.

Odstranění webu:

1. V okně **Správa lokality** vyberte :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: z panelu, který obsahuje název lokality, a pak vyberte **Odstranit lokalitu**. Zobrazí se okno potvrzení, které ověřuje, zda chcete odstranit web.

2. V potvrzovacím poli vyberte **Ano**. Okno pro potvrzení se zavře a zobrazí se okno **Správa lokality** bez webu, který jste odstranili.

## <a name="create-enterprise-zones"></a>Vytvořit podnikové zóny

Zóny jsou logické entity, které umožňují rozdělit zařízení v rámci lokality do skupin podle různých charakteristik. Můžete například vytvořit skupiny pro výrobní linky, podstanice, oblasti webu nebo typy zařízení. Můžete definovat zóny na základě jakékoli charakteristiky, která je vhodná pro vaši organizaci.

Zóny můžete nakonfigurovat jako součást procesu konfigurace lokality.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/site-management-zones-screen-v2.png" alt-text="Snímek obrazovky zobrazení zón správy webu.":::

Následující tabulka popisuje parametry v okně **Správa lokality** .

| Parametr | Popis |
|--|--|
| Název | Název snímače. Tento název můžete změnit pouze ze senzoru. Další informace najdete v tématu uživatelská příručka k programu Defender for IoT. |
| IP adresa | IP adresa snímače. |
| Verze | Verze snímače. |
| Připojení | Stav připojení snímače. Stav lze **připojit** nebo **Odpojit**. |
| Poslední upgrade | Datum posledního upgradu |
| Průběh upgradu | Indikátor průběhu zobrazuje stav procesu upgradu následujícím způsobem:<br />– Nahrávání balíčku<br />– Příprava na instalaci<br />-Zastavování procesů<br />– Zálohování dat<br />-Pořizování snímku<br />– Aktualizace konfigurace<br />– Aktualizace závislostí<br />– Aktualizace knihoven<br />-Opravy databází<br />– Spouštění procesů<br />– Ověřování systémových správnosti<br />– Ověření bylo úspěšné.<br />– Úspěch<br />– Chyba<br />-Upgrade byl zahájen.<br />-Spouští se instalaceogress bar shows the status of the upgrade process, as follows:<br />- Uploading package<br />- Preparing to install<br />- Stopping processes<br />- Backing up data<br />- Taking snapshot<br />- Updating configuration<br />- Updating dependencies<br />- Updating libraries<br />- Patching databases<br />- Starting processes<br />- Validating system sanity<br />- Validation succeeded<br />- Success<br />- Failure<br />- Upgrade started<br />- Starting installation<br /></br >Podrobnosti o upgradu najdete v tématu [Podpora Microsoftu](https://support.microsoft.com/) . |
| Zařízení | Počet zařízení, která senzor monitoruje. |
| Výstrahy | Počet výstrah na senzoru. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: | Umožňuje přiřadit senzor k zónám. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false":::| Povolí odstranění odpojeného senzoru z webu. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/sensor-icon.png" border="false"::: | Určuje, kolik senzorů je aktuálně připojeno k zóně. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/ot-assets-icon.png" border="false"::: | Určuje, kolik prostředků je aktuálně připojeno k zóně. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/number-of-alerts-icon.png" border="false"::: | Označuje počet výstrah odesílaných senzory, které jsou přiřazeny k zóně. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: | Ruší přiřazení senzorů ze zón. |

Postup přidání zóny do lokality:

1. V okně **Správa lokality** vyberte :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: z panelu, který obsahuje název webu, a pak vyberte **Přidat zónu**. Zobrazí se dialogové okno **vytvořit novou zónu** .

    :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-zone-screen.png" alt-text="Snímek obrazovky s zobrazením vytvořit nové zóny":::

2. Zadejte název zóny.

3. Zadejte popis nové zóny, který jasně uvádí charakteristiky, které jste použili k rozdělení lokality do zón.

4. Vyberte **Uložit**. Nová zóna se zobrazí v okně **Správa lokality** v lokalitě, do které tato zóna patří.

Postup úpravy zóny:

1. V okně **Správa lokality** vyberte :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: z panelu, který obsahuje název zóny, a pak vyberte **Upravit zónu**. Zobrazí se dialogové okno **Upravit zónu** .

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/zone-edit-screen.png" alt-text="Snímek obrazovky, který zobrazuje dialogové okno Upravit zónu":::

2. Upravte parametry zóny a vyberte **Uložit**.

Postup odstranění zóny:

1. V okně **Správa lokality** vyberte :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: z panelu, který obsahuje název zóny, a pak vyberte **Odstranit zónu**.

2. V potvrzovacím poli vyberte **Ano**.

Filtrování podle stavu připojení:

- V levém horním rohu vyberte možnost :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: vedle možnosti **připojení** a pak vyberte jednu z následujících možností:

  - **Vše**: prezentuje všechny senzory, které hlásí tuto místní konzolu pro správu.

  - **Připojeno**: prezentuje jenom připojené senzory.

  - **Odpojeno**: prezentuje jenom odpojené senzory.

Filtrování podle stavu upgradu:

- V levém horním rohu vyberte možnost :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: vedle položky **stav upgradu** a vyberte jednu z následujících možností:

  - **Vše**: prezentuje všechny senzory, které hlásí tuto místní konzolu pro správu.

  - **Platné**: prezentuje senzory s platným stavem upgradu.

  - **Probíhá**: prezentuje senzory, které jsou v procesu upgradu.

  - **Neúspěch**: prezentuje senzory, jejichž proces upgradu se nezdařil.

## <a name="assign-sensors-to-zones"></a>Přiřazení senzorů k zónám

Pro každou zónu je potřeba přiřadit senzory, které provádějí analýzu místních přenosů a upozorňování. Můžete přiřadit jenom senzory, které jsou připojené k místní konzole pro správu.

Postup přiřazení senzoru:

1. Vyberte možnost **Správa webu**. Nepřiřazené senzory se zobrazí v levém horním rohu dialogového okna.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassigned-sensors-view.png" alt-text="Snímek obrazovky zobrazení nepřiřazených senzorů":::

2. Ověřte, zda je stav **připojení** připojen. Pokud ne, přečtěte si téma [připojení senzorů k místní konzole pro správu](#connect-sensors-to-the-on-premises-management-console) , kde najdete podrobnosti o připojení. 

3. Vyberte :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: pro senzor, který chcete přiřadit.

4. V dialogovém okně **přiřadit senzor** vyberte organizační jednotku, oblast, lokalitu a zónu, které chcete přiřadit.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-sensor-screen.png" alt-text="Snímek obrazovky s zobrazením pro přiřazení senzorů":::

5. Vyberte **přiřadit**.

Zrušení přiřazení a odstranění snímače:

1. Odpojte senzor z místní konzoly pro správu. Podrobnosti najdete v tématu [připojení senzorů k místní konzole pro správu](#connect-sensors-to-the-on-premises-management-console) .

2. V okně **Správa lokality** vyberte senzor a vyberte :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: . Senzor se zobrazí v seznamu nepřiřazených senzorů za chvíli.

3. Pokud chcete odstranit nepřiřazený senzor z webu, vyberte snímač ze seznamu nepřiřazených senzorů a vyberte :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false"::: .

## <a name="next-steps"></a>Další kroky

[Řešení potíží se senzory a místní konzolou pro správu](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)

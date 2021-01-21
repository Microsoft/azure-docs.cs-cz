---
title: Určení, který provoz se monitoruje
description: Senzory automaticky provádějí hloubkové zjišťování paketů a přenos dat a řeší informace o síťových zařízeních, jako jsou atributy zařízení a chování sítě. K dispozici je několik nástrojů, které řídí typ provozu, který senzor detekuje.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: bfe3e00c4930ba57c930eb1bc2f2dd4ed11886e0
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624680"
---
# <a name="control-what-traffic-is-monitored"></a>Určení, který provoz se monitoruje

Senzory automaticky provádějí hloubkové zjišťování paketů a přenos dat a řeší informace o síťových zařízeních, jako jsou atributy zařízení a chování. K dispozici je několik nástrojů, které řídí typ provozu, který senzor detekuje.

## <a name="learning-and-smart-it-learning-modes"></a>Výukové a inteligentní školicí režimy IT

Výukový režim instruuje váš senzor, aby se dozvěděl o běžné činnosti vaší sítě. Příklady jsou zařízení zjištěná v síti, protokoly zjištěné v síti, přenosy souborů mezi konkrétními zařízeními a další. Tato aktivita se bude nacházet na vaše síťové základny.

Výukový režim se po instalaci automaticky povolí a zůstane zapnutý, dokud nebude vypnutý. Přibližná perioda výukového režimu je v závislosti na velikosti a složitosti sítě v rozmezí dvou až šesti týdnů. Po uplynutí této doby se v případě, že je režim učení zakázaný, triggery spustí všechny nové zjištěné aktivity. Výstrahy se aktivují, když modul zásad zjistí odchylky od zjištěných standardních hodnot.

Po dokončení období učení a výukového režimu je senzorem možné detekovat neobvykle vysokou úroveň změn standardních hodnot, které jsou výsledkem normální aktivity IT, jako jsou požadavky na DNS a HTTP. Aktivita se nazývá nedeterministické chování IT. Chování může také aktivovat upozornění na zbytečné porušení zásad a systémová oznámení. Chcete-li snížit množství těchto výstrah a oznámení, můžete povolit funkci **inteligentního učení IT** .

Když je povolené inteligentní učení IT, senzor sleduje síťový provoz, který generuje nedeterministické chování IT na základě konkrétních scénářů upozornění.

Senzor monitoruje tento provoz po dobu sedmi dnů. Pokud detekuje stejný Nedeterministický provoz IT během sedmi dnů, pokračuje v monitorování provozu po dobu dalších sedmi dní. V případě, že se provoz nedetekuje na celé 7 dní, je pro tento scénář zakázané inteligentní učení. Pro tento scénář se zjistil nový provoz a pak se generují výstrahy a oznámení.

Práce s inteligentním IT výukou vám pomůže snížit počet zbytečných výstrah a oznámení způsobených scénáři IT v rámci hlučnosti.

Pokud je senzor řízen místní konzolou pro správu, nemůžete zakázat výukové režimy. V takových případech může být výukový režim zakázán pouze z konzoly pro správu.

Funkce učení (učení a inteligentní učení IT) jsou ve výchozím nastavení povolené.

Povolení nebo zakázání učení:

- Vyberte **nastavení systému** a přepněte možnosti **učení** a **inteligentní učení IT** .

:::image type="content" source="media/concept-learning-modes/toggle-options-for-learning-and-smart-it-learning.png" alt-text="Přepínací obrazovka nastavení systému":::

## <a name="configure-subnets"></a>Konfigurace podsítí

Konfigurace podsítí ovlivňují způsob zobrazení zařízení v mapě zařízení.

Ve výchozím nastavení senzor zjišťuje nastavení podsítě a v dialogovém okně **Konfigurace podsítě** tyto informace naplní.

Pokud chcete povolit zaměření na zařízeních, zařízení se automaticky agregují podle podsítě na mapě zařízení. Každá podsíť je prezentována jako jediná entita na mapě, včetně interaktivního sbalení/rozšiřování možnosti pro přechod k podrobnostem do podsítě IT a zpět.

Když pracujete s podsítěmi, vyberte podsítě ICS a Identifikujte podsítě OT. Pak se můžete zaměřit na zobrazení mapy v sítích typu OT a ICS a sbalit na minimální prezentaci síťových prvků IT. Toto úsilí snižuje celkový počet zařízení zobrazených na mapě a poskytuje jasný přehled o síťových prvcích OT a ICS.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/expand-network-option.png" alt-text="Snímek obrazovky, který ukazuje filtrování na síť.":::

Konfiguraci můžete změnit nebo můžete ručně změnit informace o podsíti tím, že vyexportujete zjištěná data, ručně je změníte a pak znovu importujete seznam podsítí, které jste ručně definovali. Další informace o exportu a importu najdete v tématu [Import informací o zařízení](how-to-import-device-information.md).

V některých případech, jako jsou například prostředí, která používají veřejné rozsahy jako interní rozsahy, můžete dát senzoru k překladu všech podsítí jako vnitřních podsítí tak, že vyberete možnost **Nezjišťovat internetovou aktivitu** . Když vyberete tuto možnost:

- Veřejné IP adresy se budou považovat za místní adresy.

- Neodesílají se žádné výstrahy týkající se neoprávněné internetové aktivity, což snižuje počet oznámení a upozornění přijatých na externích adresách.

Konfigurace podsítí:

1. V postranní nabídce vyberte **nastavení systému**.

2. V okně **nastavení systému** vyberte **podsítě**.

   :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-subnets-configuration-screen.png" alt-text="Snímek obrazovky zobrazující obrazovku konfigurace podsítě"::: 

3. Pokud chcete přidat podsítě automaticky při zjištění nových zařízení, nechte vybrané **Automatické učení o podsítích** .

4. Pokud chcete vyřešit všechny podsítě jako interní podsítě, vyberte možnost **Nezjišťovat internetovou aktivitu**.

5. Vyberte **Přidat síť** a pro každou podsíť definujte následující parametry:

    - IP adresa podsítě.
    - Adresa masky podsítě.
    - Název podsítě. Doporučujeme pojmenovat každou podsíť s smysluplným názvem, který snadno identifikujete, abyste je mohli rozlišovat mezi IT a sítěmi. Název může mít až 60 znaků.

6. Chcete-li označit tuto podsíť jako podsíť z, vyberte možnost **podsíť ICS**.

7. Pokud chcete podsíť prezentovat samostatně při uspořádání mapy podle úrovně Purdue, vyberte **oddělené**.

8. Pokud chcete odstranit podsíť, vyberte :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/delete-icon.png" border="false"::: .

9. Chcete-li odstranit všechny podsítě, vyberte možnost **Zrušit vše**.

10. Pokud chcete exportovat nakonfigurované podsítě, vyberte **exportovat**. Tabulka podsítě se stáhne do vaší pracovní stanice.

11. Vyberte **Uložit**.

### <a name="importing-information"></a>Import informací 

Pokud chcete importovat informace o podsíti, vyberte **importovat** a vyberte soubor CSV, který chcete naimportovat. Informace o podsíti se aktualizují o informace, které jste naimportovali. Pokud je důležité prázdné pole, ztratíte data.

## <a name="detection-engines"></a>Detekční moduly

Analytické moduly pro samoobslužné učení eliminují nutnost aktualizace podpisů nebo definování pravidel. Moduly využívají analýzy chování specifické pro ICS a datové vědy k nepřetržité analýze síťového provozu pro anomálie, malware, provozní problémy, porušení protokolu a odchylky aktivity základní sítě.

> [!NOTE]
> Doporučujeme povolit všechny bezpečnostní moduly.

Pokud modul zjistí odchylku, aktivuje se výstraha. Výstrahy můžete zobrazit a spravovat z obrazovky výstrahy nebo z partnerského systému.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/deviation-alert-screen.png" alt-text="Snímek obrazovky, který ukazuje detekci odchylky.":::

Název modulu, který aktivoval výstrahu, se zobrazí pod nadpisem výstrahy.

### <a name="protocol-violation-engine"></a>Modul porušení protokolu 

K porušení protokolu dojde, pokud struktura paketu nebo hodnoty polí neodpovídají specifikaci protokolu.

Příklad scénáře: *"Neplatná operace Modbus (kód funkce nula)"* výstraha. Tato výstraha indikuje, že primární zařízení odeslalo do sekundárního zařízení požadavek s kódem funkce 0. Tato akce není podle specifikace protokolu povolena a sekundární zařízení nemusí správně zpracovat vstup.

### <a name="policy-violation-engine"></a>Modul porušení zásad

Porušení zásad probíhá u odchylky od chování podle směrného plánu definovaného v zjištěných nebo nakonfigurovaných nastaveních.

Příklad scénáře: výstraha *"neautorizovaný agent uživatele protokolu HTTP"* . Tato výstraha indikuje, že se aplikace, která se nedozvěděla nebo schválila pomocí zásad, používá jako klient HTTP na zařízení. Může se jednat o nový webový prohlížeč nebo aplikaci v tomto zařízení.

### <a name="malware-engine"></a>Malwarový stroj

Malware detekuje aktivitu škodlivou v síti.

Příklad scénáře: upozornění *na podezření na škodlivou aktivitu (Stuxnet)* . Tato výstraha indikuje, že senzor zjistil podezřelou síťovou aktivitu známou, aby se v souvislosti se Stuxnet malwarem. Tento malware je pokročilá trvalá hrozba zaměřená na průmyslové řízení a SCADA sítě.

### <a name="anomaly-engine"></a>Modul anomálií

Modul anomálií detekuje anomálie v chování sítě.

Příklad scénáře: výstraha *"periodické chování v komunikačním kanálu"* . Komponenta kontroluje síťová připojení a vyhledává pravidelné a cyklické chování přenosu dat. Toto chování je běžné v průmyslových sítích.

### <a name="operational-engine"></a>Provozní modul

Provozní modul detekuje provozní incidenty nebo nefunkční entity.

Příklad scénáře: výstraha *"zařízení má podezření na odpojení (neodpovídá)"* . Tato výstraha se vyvolá v případě, že zařízení nereaguje na jakýkoliv typ požadavku na předdefinované období. Tato výstraha může indikovat vypnutí zařízení, odpojení nebo selhání.

### <a name="enable-and-disable-engines"></a>Povolit a zakázat moduly

Když zakážete modul zásad, informace, které modul vygeneruje, nebude k dispozici pro senzor. Pokud třeba zakážete modul anomálií, nebudete dostávat upozornění na anomálie sítě. Pokud jste vytvořili pravidlo předávání, neodesílají se anomálie, které modul učí. Pokud chcete povolit nebo zakázat modul zásad, vyberte pro konkrétní modul možnost **povoleno** nebo **zakázáno** .

Celkové skóre se zobrazí v pravém dolním rohu obrazovky **nastavení systému** . Skóre indikuje procento dostupné ochrany povolené přes moduly ochrany před hrozbami. Každý modul je 20 procent dostupné ochrany.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/protection-score-screen.png" alt-text="Snímek obrazovky, který zobrazuje skóre.":::

## <a name="configure-dhcp-address-ranges"></a>Konfigurace rozsahů adres DHCP

Vaše síť se může skládat z statických i dynamických IP adres. Statické adresy se obvykle nacházejí v sítích v síti prostřednictvím historians, řadičů a zařízení síťové infrastruktury, jako jsou přepínače a směrovače. Dynamické přidělování IP adres se obvykle implementuje v hostovaných sítích s laptopy, počítači, smartphone a dalšími přenosnými zařízeními (pomocí Wi-Fi nebo fyzického připojení LAN v různých umístěních).

Pokud pracujete s dynamickými sítěmi, provedete změny IP adresy, ke kterým dojde, když jsou přiřazeny nové IP adresy. To provedete tak, že definujete rozsahy adres DHCP.

Změny mohou nastat například tehdy, když server DHCP přiřadí IP adresy.

Definování dynamických IP adres u každého snímače umožňuje komplexní a transparentní podporu v instancích změny IP adresy. Tím zajistíte komplexní vytváření sestav pro každé jedinečné zařízení.

Konzola senzoru prezentuje nejaktuálnější IP adresu přidruženou k zařízení a označuje, která zařízení jsou dynamická. Například:

- Sestava dolování dat a inventář zařízení konsoliduje veškerou aktivitu získanou ze zařízení jako jednu entitu, a to bez ohledu na změny IP adresy. Tyto sestavy označují, které adresy byly definovány jako adresy DHCP.

  :::image type="content" source="media/how-to-control-what-traffic-is-monitored/populated-device-inventory-screen-v2.png" alt-text="Snímek obrazovky, který zobrazuje inventář zařízení.":::

- Okno **vlastností zařízení** indikuje, jestli se zařízení definovalo jako zařízení DHCP.

Postup nastavení rozsahu adres DHCP:

1.  V postranní nabídce vyberte **rozsahy DHCP** v okně **nastavení systému** .

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/dhcp-address-range-screen.png" alt-text="Snímek obrazovky, který zobrazuje výběr rozsahů protokolu DHCP.":::

2.  Definujte nový rozsah nastavením hodnot **z** a **na** .

3.  Volitelně: Definujte název rozsahu, maximálně 256 znaků.

4.  Pokud chcete exportovat rozsahy do souboru CSV, vyberte **exportovat**.

5. Pokud chcete ručně přidat více rozsahů ze souboru CSV, vyberte **importovat** a pak soubor vyberte.

    > [!NOTE]
    > Rozsahy importované ze souboru CSV přepíšou stávající nastavení rozsahu.

6. Vyberte **Uložit**.

## <a name="configure-dns-servers-for-reverse-lookup-resolution"></a>Konfigurace serverů DNS pro rozlišení zpětného vyhledávání

Pokud chcete zlepšit rozšíření zařízení, můžete nakonfigurovat několik serverů DNS, aby bylo možné zpětné vyhledávání provést. Můžete přeložit názvy hostitelů nebo plně kvalifikované názvy domény přidružené k IP adresám zjištěným v síťových podsítích. Například pokud senzor zjistí IP adresu, může zadat dotaz na více serverů DNS, aby vyřešil název hostitele.

Podporují se všechny formáty CIDR.

Název hostitele se zobrazí v inventáři zařízení a v mapě zařízení a v sestavách.

Plány řešení zpětného vyhledávání můžete naplánovat pro konkrétní hodinové intervaly, například každých 12 hodin. Případně můžete naplánovat určitý čas.

Definování serverů DNS:

1. Vyberte **nastavení systému** a pak vyberte **nastavení DNS**.

2. Vyberte **Přidat server DNS**.

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-configuration-screen.png" alt-text="Snímek obrazovky, který zobrazuje výběr serveru DNS.":::

3. V poli **naplánovat reverzní vyhledávání DNS** vyberte jednu z těchto možnosti:

    - Intervaly (za hodinu).
  
    - Určitý čas. Použijte Evropské formátování. Například použijte **14:30** a nikoli **2:30 ODP**.

4. Do pole **Adresa serveru DNS** zadejte IP adresu DNS.

5. Do pole **port serveru DNS** zadejte port DNS.

6. Vyřešte síťové IP adresy pro plně kvalifikované názvy domén zařízení. Do pole **počet popisků** přidejte počet popisků domény, které se mají zobrazit. Od zleva doprava se zobrazí až 30 znaků.

7. V poli **podsítě** zadejte podsítě, které má server DNS dotazovat.

8. Pokud chcete spustit zpětné vyhledávání, vyberte přepínač **Povolit** .

### <a name="test-the-dns-configuration"></a>Otestování konfigurace DNS 

Pomocí testovacího zařízení ověřte, zda správně definované nastavení funguje:

1. Povolte přepínač **vyhledávání DNS** .

2. Vyberte **Test**.

3. Zadejte adresu v poli **Vyhledat adresu** pro dialogové okno **test zpětného vyhledávání DNS pro server** .

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-test-screen.png" alt-text="Snímek obrazovky zobrazující oblast adresy pro vyhledávání":::

4. Vyberte **Test**.

## <a name="configure-windows-endpoint-monitoring"></a>Konfigurace monitorování koncového bodu systému Windows

Díky funkci monitorování koncového bodu systému Windows můžete Azure Defender pro IoT nakonfigurovat pro selektivní testování systémů Windows. Získáte tak více zaměřené a přesné informace o vašich zařízeních, jako jsou například úrovně aktualizace Service Pack.

Můžete nakonfigurovat zjišťování s konkrétními rozsahy a hostiteli a nakonfigurovat je tak, aby se prováděly jenom tak často, jak je potřeba. Selektivní zjišťování můžete provádět pomocí rozhraní WMI (Windows Management Instrumentation) (WMI), což je standardní skriptovací jazyk společnosti Microsoft pro správu systémů Windows.

> [!NOTE]
> - V jednom okamžiku můžete spustit jenom jednu kontrolu.
> - Získáte nejlepší výsledky s uživateli, kteří mají oprávnění k doméně nebo místnímu správci.
> - Než začnete s konfigurací WMI, nakonfigurujte pravidlo brány firewall, které otevře odchozí provoz ze senzoru do kontrolované podsítě pomocí portu UDP 135 a všech portů TCP nad 1024.

Po dokončení testu je soubor protokolu se všemi pokusy o zjišťování k dispozici na základě možnosti exportu protokolu. Protokol obsahuje všechny IP adresy, které byly zjišťovány. Pro každou IP adresu se v protokolu zobrazí informace o úspěchu a selhání. K dispozici je také chybový kód, což je bezplatný řetězec odvozený z výjimky. Kontrola posledního protokolu je zachována pouze v systému.

Můžete provádět plánované kontroly nebo ruční prohledávání. Po dokončení kontroly můžete výsledky zobrazit v souboru CSV.

**Požadavky**

Nakonfigurujte pravidlo brány firewall, které otevře odchozí provoz ze senzoru do kontrolované podsítě pomocí portu UDP 135 a všech portů TCP nad 1024.

Konfigurace automatického prohledávání:

1. V postranní nabídce vyberte **nastavení systému**.

2. Vyberte **monitorování koncového bodu systému Windows** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: .

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Snímek obrazovky zobrazující výběr monitorování koncového bodu systému Windows":::

3. V podokně **plán skenování** nakonfigurujte možnosti následujícím způsobem:

      - **Podle pevných intervalů (v hodinách)**: Nastavte plán skenování podle intervalů v hodinách.

      - **Podle určitých časů**: Nastavte plán skenování podle konkrétních časů a vyberte **Uložit kontrolu**.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/schedule-a-scan-screen-v2.png" alt-text="Snímek obrazovky zobrazující tlačítko Uložit kontrolu":::

4. Chcete-li definovat rozsah prohledávání, vyberte možnost **nastavit rozsahy skenování**.

5. Nastavte rozsah IP adres a přidejte svého uživatele a heslo.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-scan-range-screen.png" alt-text="Snímek obrazovky, který ukazuje přidání uživatele a hesla":::

6. Pokud chcete z kontroly vyloučit rozsah IP adres, vyberte **Zakázat** vedle rozsahu.

7. Pokud chcete rozsah odebrat, vyberte :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/remove-scan-icon.png" border="false"::: vedle rozsahu.

8. Vyberte **Uložit**. Dialogové okno **Upravit konfiguraci rozsahů skenování** se zavře a počet rozsahů se zobrazí v podokně **oblasti prohledávání** .

Provedení ručního prohledávání:

1. V postranní nabídce vyberte **nastavení systému**.

2. Vyberte **monitorování koncového bodu systému Windows** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: .

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Snímek obrazovky, který ukazuje obrazovku nastavení monitorování koncového bodu systému Windows.":::

3. V podokně **Akce** vyberte **Spustit kontrolu**. V podokně **Akce** se zobrazí stavový řádek a zobrazí se průběh procesu skenování.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/started-scan-screen-v2.png" alt-text="Snímek obrazovky, který zobrazuje tlačítko spustit kontrolu.":::

Zobrazení výsledků kontroly:

1. Po dokončení kontroly vyberte v podokně **Akce** možnost **Zobrazit výsledky kontroly**. Soubor CSV s výsledky kontroly se stáhne do vašeho počítače.

## <a name="see-also"></a>Viz také

[Prozkoumat detekci senzorů v inventáři zařízení](how-to-investigate-sensor-detections-in-a-device-inventory.md) 
 [Prozkoumat detekci senzorů v mapě zařízení](how-to-work-with-the-sensor-device-map.md)

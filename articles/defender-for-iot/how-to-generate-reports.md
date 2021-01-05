---
title: Generování sestav
description: Pomocí Defenderu pro nástroje pro vytváření sestav IoT získáte přehled o aktivitě sítě, rizicích, útokech a trendech.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/17/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: d0c3f531ede0a590a6ba7bb21c6edbd768c08069
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2020
ms.locfileid: "97838977"
---
# <a name="generate-reports"></a>Generování sestav

Využijte Azure Defender pro nástroje pro vytváření sestav IoT, které vám umožní získat přehled o aktivitě sítě, rizicích, útokech a trendech. K dispozici jsou nástroje pro generování sestav:

- Na základě aktivity zjištěné jednotlivými senzory.
- Na základě aktivit zjištěných všemi senzory. 

Tyto sestavy jsou generovány z místní konzoly pro správu.

## <a name="reports-for-sensor-risk-assessment"></a>Sestavy pro posouzení rizik senzorů

Vytváření sestav hodnocení rizik umožňuje vygenerovat bezpečnostní skóre pro každé síťové zařízení a také celkové skóre zabezpečení sítě. Celkové skóre představuje procento z 100 procent zabezpečení.

Toto skóre vychází z výsledků kontroly paketů, strojového modelování chování a návrhu stavového počítače specifického pro SCADA. K dispozici je rozsáhlá škála dalších informací, například:

- Problémy s konfigurací

- Priorita ohrožení zabezpečení zařízení podle úrovně zabezpečení

- Problémy se zabezpečením sítě

- Síťové provozní problémy

- Vektory útoku

- Připojení k sítím ICS

- Připojení k Internetu

- Průmyslové indikátory malwaru

- Problémy s protokoly

  - Zabezpečená zařízení: zařízení s bezpečnostním skóre nad 90%.

- **Zabezpečená zařízení**: zařízení s bezpečnostním skóre vyšším než 90%.

- **Zranitelná zařízení**: zařízení s bezpečnostním skóre nižším než 70%.

- **Zařízení, která vyžadují vylepšení**: zařízení s skóre zabezpečení mezi 70% a 89%.

Vytvoření sestavy:

1. V postranní nabídce vyberte **hodnocení rizik** .
2. V rozevíracím seznamu **Vybrat senzor** vyberte snímač.
3. Vyberte **Generovat sestavu**.
4. V části archivovaných sestav vyberte **Stáhnout** .

:::image type="content" source="media/how-to-generate-reports/risk-assessment.png" alt-text="Zobrazení posouzení rizika.":::

Import loga společnosti:

Import loga společnosti:

- Vyberte **importovat logo**.

## <a name="reports-for-sensor-attack-vector"></a>Sestavy pro vektor útoků na senzor

Vektorové sestavy útoku poskytují grafické znázornění řetězce ohrožení zabezpečení zneužití zařízení. Tato ohrožení zabezpečení můžou útočníkovi umožnit přístup k klíčovým síťovým zařízením. Simulátor vektoru útoku vypočítá vektory útoku v reálném čase a analyzuje všechny vektory útoku pro konkrétní cíl.

Práce s vektorem útoku umožňuje vyhodnotit účinek zmírňující aktivity v posloupnosti útoků. Pak se můžete rozhodnout, například pokud upgrade systému narušuje cestu útočníka tím, že dojde k přerušení řetězce útoku nebo pokud zůstane jiná cesta k útoku. Tyto informace vám pomůžou určit prioritu oprav a rizikových aktivit.

:::image type="content" source="media/how-to-generate-reports/control-center.png" alt-text="Zobrazení výstrah v řídicím centru.":::

> [!NOTE]
> Správci a analytici zabezpečení mohou provádět postupy popsané v této části.

Postup vytvoření simulace vektoru útoku:

1. Kliknutím na :::image type="content" source="media/how-to-generate-reports/plus.png" alt-text="znaménko plus":::v nabídce na straně přidejte simulaci.

 :::image type="content" source="media/how-to-generate-reports/vector.png" alt-text="Simulace vektoru útoku.":::

2. Zadejte vlastnosti simulace:

   - **Název**: název simulace.

   - **Maximum vektorů**: maximální počet vektorů v jedné simulaci.

   - **Zobrazit v mapě zařízení**: zobrazí vektor útoku jako filtr na mapě zařízení.

   - **Všechna zdrojová zařízení**: vektor útoku bude považovat všechna zařízení za zdroj útoku.

   - **Zdroj útoku**: vektor útoku bude zvážit pouze určená zařízení jako zdroj útoku.

   - **Všechna cílová zařízení**: vektor útoku bude považovat všechna zařízení za cíl útoku.

   - **Cíl útoku**: vektor útoku bude zvážit pouze určená zařízení jako cíl útoku.

   - **Vyloučit zařízení**: zadaná zařízení budou vyloučena z simulace vektoru útoku.

   - **Vyloučit podsítě**: zadané podsítě budou vyloučeny z simulace vektoru útoku.

3. Vyberte **Přidat simulaci**. Simulace bude přidána do seznamu simulace.

   :::image type="content" source="media/how-to-generate-reports/new-simulation.png" alt-text="Přidejte novou simulaci.":::

4. Tuto možnost vyberte, :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: Pokud chcete upravit simulaci.

   Tuto možnost vyberte, :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: Pokud chcete odstranit simulaci.

   Tuto možnost vyberte, :::image type="icon" source="media/how-to-generate-reports/make-a-favorite-icon.png" border="false"::: Pokud chcete označit simulaci jako oblíbenou.

5. Zobrazí se seznam vektorů útoku, včetně vektorového skóre (od 100), zdrojového zařízení útoku a cílového zařízení útoku. Vyberte konkrétní útok pro grafické znázornění vektorů útoku.

   :::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="Vektory útoku.":::

## <a name="sensor-data-mining-queries"></a>Data senzorů – dotazy dolování

Nástroje pro dolování dat umožňují generovat komplexní a podrobné informace o síťových zařízeních v různých vrstvách. Dotaz můžete například vytvořit na základě:

- Časová období

- Připojení k Internetu

- Porty

- Protokoly

- Verze firmwaru

- Programovací příkazy

- Nečinnost zařízení

Sestavu můžete vyladit na základě filtrů. Můžete například zadat dotaz na konkrétní podsíť, ve které se firmware aktualizoval.

:::image type="content" source="media/how-to-generate-reports/active-device-list-v2.png" alt-text="Seznam aktivních zařízení":::

K dispozici jsou různé nástroje pro správu dotazů. Například můžete exportovat a uložit.

> [!NOTE]
> Správci a analytici zabezpečení mají přístup k možnostem dolování dat.

### <a name="dynamic-updates"></a>Dynamické aktualizace

Dotazy dolování dat, které vytvoříte, se dynamicky aktualizují pokaždé, když je otevřete. Například:

- Pokud vytvoříte sestavu pro verze firmwaru na zařízeních v 1. června a znovu otevřete sestavu od 10. června, Tato sestava se aktualizuje o informace, které jsou přesné pro 10. června.

- Pokud vytvoříte sestavu, která detekuje nově zjištěná zařízení v posledních 30 dnech od 1. června, výsledky se zobrazí za posledních 30 dní.

### <a name="data-mining-use-cases"></a>Případy použití dolování dat

Dotazy můžete použít ke zpracování rozsáhlých potřeb zabezpečení pro různé týmy zabezpečení:

- **Odpověď na incident SOC**: vygenerujte sestavu v reálném čase, která vám může přispět k okamžité reakci na incidenty. Vygenerujte například sestavu pro seznam zařízení, která mohou vyžadovat opravy.

- **Forenzní**: vygenerujte sestavu na základě historických dat pro vyšetřovací sestavy.

- **Integrita sítě IT**: vygeneruje zprávu, která pomáhá zlepšit celkové zabezpečení sítě. Vygenerujte například sestavu, která obsahuje zařízení se slabými přihlašovacími údaji pro ověřování.

- **Visibility**: vygenerujte sestavu, která se vztahuje na všechny položky dotazu pro zobrazení všech parametrů standardních hodnot vaší sítě.

### <a name="data-mining-storage"></a>Úložiště dolování dat

Informace o dolování dat se ukládají a ukládají průběžně, s výjimkou případů, kdy se zařízení odstraní. Výsledky dolování dat je možné exportovat a ukládat externě na zabezpečený server. Kromě toho senzor provádí automatické každodenní zálohování, aby se zajistila kontinuita systému a ochrana dat.

### <a name="data-mining-and-reports"></a>Dolování dat a sestavy 

Pravidelné sestavy, ke kterým se dostanete z možnosti **sestavy** , jsou předdefinované sestavy dolování dat. Nejedná se o dynamické dotazy, které jsou k dispozici v dolování dat, ale statické reprezentace výsledků dotazu dolování dat.

Výsledky dotazu dolování dat nejsou k dispozici uživatelům typu RO. Správci a analytici zabezpečení, kteří chtějí uživatelům typu RO přístup k informacím generovaným dotazy dolování dat, by měli tyto informace uložit jako sestavu.

### <a name="predefined-queries"></a>Předdefinované dotazy

K dispozici jsou následující předdefinované dotazy. Tyto dotazy jsou generovány v reálném čase.

- **CVEs**: v posledních 24 hodinách se zjistily seznam zařízení se známými chybami zabezpečení.

- **Vyloučené CVEs**: seznam všech CVEs, které se ručně vyloučily. Pokud chcete dosáhnout přesnější výsledků v sestavách a vektorech útoků, můžete seznam CVE přizpůsobit ručně tak, že zahrnete a vyjmete CVEs.

- **Internetová aktivita**: zařízení, která jsou připojená k Internetu.

- **Neaktivní zařízení**: zařízení, která nekomunikovala během posledních sedmi dnů.

- **Aktivní zařízení**: aktivní síťová zařízení během posledních 24 hodin.

- **Vzdálený přístup**: zařízení, která komunikují prostřednictvím protokolů vzdálené relace.

- **Programovací příkazy**: zařízení odesílající průmyslové programování.

Tyto sestavy jsou automaticky přístupné z obrazovky **sestavy** , kde je uživatelé a další uživatelé mohou zobrazit. Uživatelé typu RO nemají přístup k sestavám dolování dat.

:::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="Obrazovka dolování dat.":::

### <a name="create-a-data-mining-report"></a>Vytvoření sestavy dolování dat

Vytvoření sestavy dolování dat:

1. Z vedlejší nabídky vyberte **dolování dat** . Předdefinované navrhované sestavy se zobrazí automaticky.

 :::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="Vyberte dolování dat ze strany podokna.":::

2. Vyberte :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false":::.

3. Vyberte **Nová sestava** a definujte sestavu.

   :::image type="content" source="media/how-to-generate-reports/create-new-report-screen.png" alt-text="Vyplněním této obrazovky vytvořte novou sestavu.":::

   K dispozici jsou následující parametry:

   - Zadejte název a popis sestavy.

   - V kategorii vyberte jednu z těchto kategorií:

      - **Categories (vše)** pro zobrazení všech výsledků sestavy o všech zařízeních v síti.

      - **Obecné** pro výběr kategorií Standard.

   - Vyberte konkrétní parametry sestavy, které vás zajímají.

   - Vyberte pořadí řazení (seřadit **podle**). Seřadí výsledky na základě aktivity nebo kategorie.

   - Vyberte možnost **Uložit na stránky sestavy** a uložte výsledky sestavy jako sestavu, která je přístupná ze stránky **sestavy** . To umožní uživatelům typu RO spustit sestavu, kterou jste vytvořili.

   - Pokud chcete přidat další filtry, vyberte **filtry (Přidat)** . Požadavky na zástupné znaky jsou podporovány.

   - Zadejte skupinu zařízení (definovanou v mapě zařízení).

   - Zadejte IP adresu.

   - Zadejte port.

   - Zadejte adresu MAC.

4. Vyberte **Uložit**. Výsledky sestavy se otevřou na stránce **dolování dat** .

:::image type="content" source="media/how-to-generate-reports/data-mining-page.png" alt-text="Sestavujte výsledky, které se zobrazují na stránce dolování dat.":::

### <a name="manage-data-mining-reports"></a>Správa sestav dolování dat

Následující tabulka popisuje možnosti správy pro dolování dat:

| Obrázek ikony | Popis |
|--|--|
| :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: | Upravte parametry sestavy. |
| :::image type="icon" source="media/how-to-generate-reports/export-as-pdf-icon.png" border="false"::: | Exportovat jako PDF |
| :::image type="icon" source="media/how-to-generate-reports/csv-export-icon.png" border="false"::: |Exportovat jako sdílený svazek clusteru. |
| :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: | Zobrazit další informace, například datum poslední změny. Pomocí této funkce můžete vytvořit snímek výsledků dotazu. To může být nutné provést pro další šetření s vedoucími týmu nebo analytiky SOC, například. |
| :::image type="icon" source="media/how-to-generate-reports/pin-icon.png" border="false"::: | Zobrazit na stránce **sestavy** nebo skrýt na stránce **sestavy** . :::image type="content" source="media/how-to-generate-reports/hide-reports-page.png" alt-text="Skryjte nebo Odhalte sestavy."::: |
| :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: | Odstraňte danou sestavu. |

#### <a name="create-customized-directories"></a>Vytváření přizpůsobených adresářů 

Můžete uspořádat rozsáhlé informace o dotazech dolování dat vytvořením adresářů pro kategorie. Můžete například vytvořit adresáře pro protokoly nebo umístění.

Vytvoření nového adresáře:

1. Tuto možnost vyberte :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: , pokud chcete přidat nový adresář.

2. Vyberte **nový adresář** pro zobrazení nového formuláře adresáře.

3. Pojmenujte nový adresář.

4. Přetáhněte požadované sestavy do nového adresáře. Sestavu můžete kdykoli přetáhnout zpět do hlavního zobrazení.

5. Klikněte pravým tlačítkem na nový adresář, který chcete otevřít, upravit nebo odstranit.

#### <a name="create-snapshots-of-report-results"></a>Vytváření snímků výsledků sestavy

Pro další šetření možná budete muset uložit určité výsledky dotazu. Například může být nutné sdílet výsledky s různými týmy zabezpečení.

Snímky se ukládají do výsledků sestavy a negenerují dynamické dotazy.

:::image type="content" source="media/how-to-generate-reports/report-results-report.png" alt-text="Snímky.":::

Vytvoření snímku:

1. Otevřete požadovanou sestavu.

2. Vyberte informační ikonu :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: .

3. Vyberte možnost **vzít nové**.

4. Zadejte název snímku a vyberte **Uložit**.

:::image type="content" source="media/how-to-generate-reports/take-a-snapshot.png" alt-text="Pořídit snímek.":::

#### <a name="customize-the-cve-list"></a>Přizpůsobení seznamu CVE

Seznam CVE můžete přizpůsobit ručně následujícím způsobem:

  - Zahrnout/vyloučit CVEs

  - Změnit skóre na CVE

Postup provedení ručních změn v sestavě CVE:

1.  V postranní nabídce vyberte **dolování dat**.

2. :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false":::V levém horním rohu okna **dolování dat** vyberte. Pak vyberte **Nová sestava**.

   :::image type="content" source="media/how-to-generate-reports/create-a-new-report-screen.png" alt-text="Vytvoří novou sestavu.":::

3. V levém podokně vyberte jednu z následujících možností:

   - **Známé chyby zabezpečení**: vybere obě možnosti a prezentuje výsledky v obou tabulkách sestavy, jednu s CVEs a druhou s vyloučeným CVEs.

   - **CVEs**: tuto možnost vyberte, pokud chcete zobrazit seznam všech CVEs.

   - **Vyloučené CVEs**: Pokud tuto možnost vyberete, zobrazí se seznam všech vyloučených CVEs.

4. Zadejte **název** a **Popis** informace a vyberte **Uložit**. Nová sestava se zobrazí v okně **dolování dat** .

5. Pokud chcete vyloučit CVEs, otevřete sestavu dolování dat pro CVEs. Zobrazí se seznam všech CVEs.

   :::image type="content" source="media/how-to-generate-reports/cves.png" alt-text="Sestava C V E":::

6. Chcete-li povolit výběr položek v seznamu, vyberte :::image type="icon" source="media/how-to-generate-reports/enable-selecting-icon.png" border="false"::: a vyberte CVEs, který chcete přizpůsobit. V dolní části se zobrazí panel **operace** .

   :::image type="content" source="media/how-to-generate-reports/operations-bar-appears.png" alt-text="Snímek obrazovky s panelem operací dolování dat.":::

7. Vyberte CVEs, který chcete vyloučit, a pak vyberte **Odstranit záznamy**. CVEs, který jste vybrali, se nezobrazí v seznamu CVEs a zobrazí se v seznamu vyloučených CVEs, když ho vygenerujete.

8. Chcete-li zahrnout vyloučené CVEsy do seznamu CVEs, vygenerujte sestavu pro vyloučené CVEs a odstraňte je ze seznamu položek, které chcete zahrnout zpět do seznamu CVEs.

9. Vyberte CVEs, ve kterém chcete změnit skóre, a pak vyberte **Update CVE skore**.

   :::image type="content" source="media/how-to-generate-reports/set-new-score-screen.png" alt-text="Aktualizujte skóre na CVE.":::

10. Zadejte nové skóre a vyberte **OK**. V CVEs, které jste vybrali, se zobrazí aktualizované skóre.

### <a name="reports-based-on-data-mining"></a>Sestavy založené na dolování dat

Sestavy odrážejí informace vygenerované výsledky dotazu dolování dat. To zahrnuje výchozí sestavy dolování dat, které jsou k dispozici v zobrazení sestavy. Správci a analytici zabezpečení mohou také generovat vlastní dotazy dolování dat a uložit je jako sestavy. Tyto sestavy jsou k dispozici také pro uživatele typu RO.

Vygenerování sestavy:

1. V postranní nabídce vyberte **sestavy** .

2. Vyberte požadovanou sestavu k zobrazení. Volba může být **vlastní** nebo **automaticky generované** sestavy, například **programovací příkazy** a **vzdálený přístup**.

3. Sestavu můžete vyexportovat tak, že vyberete jednu z ikon v pravém horním rohu obrazovky:

   :::image type="icon" source="media/how-to-generate-reports/export-to-pdf-icon.png" border="false"::: Exportujte do souboru PDF.

   :::image type="icon" source="media/how-to-generate-reports/export-to-csv-icon.png" border="false"::: Exportujte soubor do souboru CSV.

> [!NOTE] 
> Uživatel typu RO uvidí pouze sestavy, které jsou pro ně vytvořeny.

:::image type="content" source="media/how-to-generate-reports/select-a-report-screen.png" alt-text="Vyberte sestavu, kterou chcete vygenerovat.":::

:::image type="content" source="media/how-to-generate-reports/remote-access-report.png" alt-text="Vygenerovala se sestava vzdáleného přístupu.":::

## <a name="reports-for-sensor-trends-and-statistics"></a>Sestavy trendů a statistik senzorů

Můžete vytvořit grafy widgetů a výsečové grafy a získat tak přehled o trendech a statistice sítě. Widgety se dají seskupovat v části uživatelsky definované řídicí panely.

> [!NOTE]
> Pouze správci a analytici zabezpečení mohou provádět postupy v této části.

Pokud chcete zobrazit řídicí panely a widgety, v postranní nabídce vyberte **trendy & statistiku** .

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Snímek obrazovky s šetřením":::

Řídicí panel obsahuje widgety, které graficky popisují následující typy informací:

- Provoz podle portu
- Šířka pásma kanálu
- Celková šířka pásma
- Aktivní připojení TCP
- Signalizac
  - Nová zařízení
  - Zaneprázdněná zařízení
  - Zařízení podle dodavatele
  - Zařízení podle operačního systému
  - Odpojená zařízení
- Přerušení připojení po hodinách
- Výstrahy pro incidenty podle typu
- Přístup k databázové tabulce
- Inoddílování protokolů – widgety
- Síť Ethernet a IP adresa:
  - Přenos dat Ethernet a IP adres službou CIP
  - Přenos dat Ethernet a IP adres podle třídy CIP
  - Přenos přes síť Ethernet a IP adresy pomocí příkazu
- OPC
  - OPC hlavní operace správy
  - OPC hlavní vstupně-výstupní operace
- S7 pro Siemens:
  - S7 provoz podle řídicí funkce
  - S7 provoz podle podfunkce
- SRTP:
  - SRTP provoz podle kódu služby
  - SRTP chyby podle dne
- SuiteLink:
  - SuiteLink nahoře dotazovaných značek
  - SuiteLink – chování numerických značek
- IEC – 60870 přenosů podle ASDU
- DNP3 provoz podle funkce
- Provoz MMS podle služby
- Modbus provoz podle funkce
- Provoz OPC-UA podle služby

> [!NOTE]
>  Čas v widgetech se nastaví podle času snímače.

## <a name="reports-for-the-on-premises-management-console"></a>Sestavy pro místní konzolu pro správu

Místní Konzola pro správu umožňuje generování sestav pro každý senzor, který je k němu připojen. Sestavy jsou založené na provedených dotazech dolování dat ze senzorů.

Můžete vygenerovat následující sestavy:

- **Aktivní zařízení (posledních 24 hodin)**: prezentuje seznam zařízení, která zobrazují síťové aktivity během období 24 hodin.

- **Neaktivní zařízení (posledních 7 dnů)**: prezentuje seznam zařízení, která v posledních sedmi dnech nezobrazovala žádnou síťovou aktivitu.

- **Programovací příkazy**: prezentuje seznam zařízení, která odeslaly programovací příkazy během posledních 24 hodin.

- **Vzdálený přístup**: prezentuje seznam zařízení, ke kterým se vzdáleným zdrojům přistupovalo během posledních 24 hodin.

:::image type="content" source="media/how-to-generate-reports/reports-view.png" alt-text="Snímek obrazovky zobrazení sestav.":::

Když vyberete senzor z místní konzoly pro správu, zobrazí se v seznamu sestav všechny vlastní sestavy nakonfigurované na tomto senzoru. U každého snímače můžete vygenerovat výchozí sestavu nebo vlastní sestavu nakonfigurovanou na tomto senzoru.

Vygenerování sestavy:

1. V levém podokně vyberte **sestavy**. Zobrazí se okno **sestavy** .

2. V rozevíracím seznamu **senzory** vyberte snímač, pro který chcete vytvořit sestavu.

   :::image type="content" source="media/how-to-generate-reports/sensor-drop-down-list.png" alt-text="Snímek obrazovky se zobrazením senzorů.":::

3. V pravém rozevíracím seznamu vyberte sestavu, kterou chcete vygenerovat.

4. Pokud chcete vytvořit PDF výsledků sestavy, vyberte :::image type="icon" source="media/how-to-generate-reports/pdf-report-icon.png" border="false"::: .

## <a name="risk-assessment-reports-for-the-on-premises-management-console"></a>Sestavy posouzení rizik pro místní konzolu pro správu

Vygenerujte sestavu posouzení rizik pro každý senzor, který je připojený k místní konzole pro správu. Tato sestava poskytuje přehled o všech sítích, které senzory monitorují.

Sestavy hodnocení rizik umožňují vygenerovat bezpečnostní skóre pro každé síťové zařízení a také celkové skóre zabezpečení sítě. Celkové skóre je založené na hloubkové kontrole paketů, modulech behaviorálních modulech a návrhu stavového počítače specifického pro SCADA. K dispozici je rozsáhlá škála dalších informací. Například:

- Problémy s konfigurací

- Priorita ohrožení zabezpečení zařízení podle úrovně zabezpečení

- Problémy se zabezpečením sítě

- Síťové provozní problémy

- Vektory útoku

- Připojení k sítím ICS

- Připojení k Internetu

- Průmyslové indikátory malwaru

- Problémy s protokoly

Tato sestava poskytuje doporučení pro zmírnění rizik, která vám pomůžou vylepšit skóre zabezpečení.

- Zabezpečená zařízení: zařízení s bezpečnostním skóre nad 90%.

- **Zranitelná zařízení**: zařízení s bezpečnostním skóre nižším než 70%.

- **Zařízení, která vyžadují vylepšení**: zařízení s skóre zabezpečení mezi 70% a 89%.

Vytvoření sestavy:

1. V postranní nabídce vyberte **hodnocení rizik** .

2. V rozevíracím seznamu **Vybrat senzor** vyberte snímač.

3. Vyberte **Generovat sestavu**.

4. V části **archivovaných sestav** vyberte **Stáhnout** .

Import loga společnosti:

- Vyberte **importovat logo**.

:::image type="content" source="media/how-to-generate-reports/import-logo-screenshot.png" alt-text="Naimportujte logo pomocí zobrazení hodnocení rizik.":::

## <a name="see-also"></a>Viz také
[Práce s zobrazeními map webu](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)

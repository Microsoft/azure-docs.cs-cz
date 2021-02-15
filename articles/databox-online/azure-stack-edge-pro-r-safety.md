---
title: Průvodce zabezpečením Azure Stack Edge pro R | Microsoft Docs
description: Popisuje bezpečnostní konvence, pokyny, požadavky a vysvětluje, jak bezpečně nainstalovat a provozovat zařízení Azure Stack Edge pro R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: alkohli
ms.openlocfilehash: dacc9ecc28ffa482b60d1e48735fe3620b5b7558
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363062"
---
# <a name="azure-stack-edge-pro-r-safety-instructions"></a>Pokyny pro zabezpečení Azure Stack Edge pro R

![Ikona varování ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ Ikona upozornění na bezpečnost ](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png)
 **čtení informace o zabezpečení a stavu**

Než použijete zařízení Azure Stack Edge pro R, přečtěte si všechny bezpečnostní údaje v tomto článku. Neúspěšné zpracování pokynů může vést k požáru, úrazu elektrického úrazu, zranění nebo poškození vašich vlastností. Před použitím Azure Stack Edge pro R si přečtěte všechny bezpečnostní údaje níže.

## <a name="safety-icon-conventions"></a>Bezpečnostní konvence ikon

U příznaků upozorňujících na nebezpečí nebezpečnosti jsou k disdobu následující slova:

| Ikona | Description |
|:--- |:--- |
| ![Symbol nebezpečí](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)| **Nebezpečí:** Označuje nebezpečnou situaci, která, pokud se nevyhne, má za následek smrt nebo vážnou škodu. <br> **Upozornění:** Označuje nebezpečnou situaci, že pokud se nevyhnete, může dojít k úmrtí nebo závažné újmě. <br> **Upozornění:** Označuje nebezpečnou situaci, že pokud se nevyhnete, může dojít k menší nebo střední újmě.|
|

Při nastavování a spouštění Azure Stack hraničního zařízení pro R Edge se budou pozorovat následující ikony nebezpečí:

| Ikona | Description |
|:--- |:--- |
| ![Nejprve si přečtěte všechny pokyny.](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png) | Nejprve si přečtěte všechny pokyny. |
| ![Oznámení ikony oznámení ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **:** | Indikuje informace považované za důležité, ale nesouvisí s rizikem. || ![Symbol nebezpečí](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) | Symbol nebezpečí |
| ![Ikona nebezpečí tipu](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png)  | Nebezpečí špičky|
| ![Ikona velké váhy](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png)  | Těžká riziková rizika|
| ![Ikona elektrického úrazu](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) | Nebezpečí elektrického úrazu |
| ![Ikona žádné části uživatelských služeb](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) | Žádné části s uživatelskými službami Nepoužívejte přístup, pokud není řádně vyškolená. |
| ![Ikona více zdrojů napájení](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png)  | Více zdrojů napájení. Odpojte všechny napájecí šňůry, aby se z něj odstranila veškerá energie. |
| ![Ikona bodů Pinching](./media/azure-stack-edge-pro-r-safety/icon-pinching-points.png)  | Pinching body jsou k dispozici. |
| ![Ikona aktivních komponent nebo ploch](./media/azure-stack-edge-pro-r-safety/icon-hot-component-surface.png)  | Označuje aktivní součásti nebo plochy. |
|

## <a name="handling-precautions-and-site-selection"></a>Zpracování opatření a výběr webu

![Upozornění na ikonu upozornění ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **:**

* Vhodná zařízení (například zásuvka palety) a osobní ochranné vybavení (OOP), třeba použití rukavic při přesunu a manipulaci s dodaným zařízením.

![Ikona upozornění ikona ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)![ nebezpečí ](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png) **Upozornění:**

* Zařízení umístěte na plochý, tvrdý a stabilní povrch, abyste se vyhnuli potenciálnímu tipu nebo výbuchu.
* Nevytvářejte více než 2 zařízení navzájem.
* Před skládáním zajistěte, aby byl systém zabezpečený.
* Neměňte umístění nebo přesunutí skládaných zařízení.
* Nevytvářejte zásobník zařízení, která jsou obohacené s externími kabely.
* Ujistěte se, že napájecí šňůry nejsou během operací vrstvení drceny nebo zničeny.
* Zařízení se nepoužívají jako tabulky ani pracovní prostory.

![Ikona upozornění stav ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ elektrického úrazu ikona pro ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
 ![ uživatelské služby bez ](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **Upozornění:**

* Zkontrolujte, že zařízení *přijalo* neškodné škody. Pokud je skříň zařízení poškozená, [kontaktujte podpora Microsoftu](azure-stack-edge-contact-microsoft-support.md) k získání náhrady. Nepokoušejte se zařízení provozovat.
* Pokud se domníváte, že zařízení nefunguje, obraťte se na [Podpora Microsoftu](azure-stack-edge-contact-microsoft-support.md) , abyste získali náhradu. Nepokoušejte se službu zařízení provést.
* Zařízení neobsahuje žádné části uživatelsky neobsluhované. V rámci jsou přítomné nebezpečné napětí, aktuální a energetické úrovně. Neotevírejte. Vraťte zařízení do Microsoftu pro obsluhu.

![](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ Výstražná ikona velké váhy ikona upozornění ](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png) **:**

* Odebrání modulu napájení UPS zpřístupňuje obohacené části v rámci UPS. Nevkládat cizí objekty do oddílu modulu napájení.
* Nepokoušejte se nazvednout Azure Stack hraniční zařízení pro R Edge sami. Skříň může vážit až 52 kg a 93 kg (115 kg a 205 kg); Využijte mechanickou pomoc nebo jinou vhodnou pomoc při přesunu a zvedání vybavení. V souladu s místními požadavky na zdravotní a bezpečnostní požadavky při přesunu a zvedání vybavení.
* Nepokoušejte se vyzvednutím zařízení bez správné mechanické pomoci. Uvědomte si, že pokus o zvedání této váhy může způsobit závažné zranění.

![Upozornění na ikonu upozornění ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **:**  

* Systém je navržený tak, aby fungoval v kontrolovaném prostředí. Vyberte lokalitu, která je:
  * Dobře větrané a nezávisle na zdrojích tepla, včetně přímých slunečních a příjezdů.
  * Nezveřejňuje se do vlhkosti ani deště.
  * Umístěný v prostoru, který minimalizuje vibrace a fyzické nárazy.  Systém je navržen pro nárazy a vibrace podle MIL-STD-810G.
  * Izolované od silných elektromagnetických polí vyráběných elektrickými zařízeními.
  * Poskytuje se správně uzemněnými odbytištěmi.
  * K dispozici je dostatek místa pro přístup ke kabelům napájecího zdroje, protože slouží jako hlavní odpojení napájecího produktu.
* Pro tento produkt nejsou k dispozici kabely Ethernet. Aby se snížilo elektromagnetické rušení, doporučuje se použít kabelovou kabeláž STP (s ochranou) CAT 6.
* Nastavení vybavení v pracovní oblasti umožňující dostatečné vzduchové šíření zařízení; Zajistěte, aby se přední a zadní kryty kompletně odebraly, když je zařízení spuštěné.
* Pro tento produkt nejsou k dispozici kabely Ethernet. Aby se snížilo elektromagnetické rušení, doporučuje se použít kabel CAT 6 stíněný (STP).
* Nainstalujte zařízení v oblasti řízené teplotou bez vhodných kontaminujících látek a umožněte vhodnému vzduchovým pohybům na zařízení.
* Udržujte si zařízení pryč ze zdrojů tekutých a nadměrně humidch prostředí.
* Nepovolujte žádné kapalné ani žádné cizí objekty k zadání systému. Neumísťujte nápoje ani žádné jiné kapalné kontejnery na systém nebo poblíž.

## <a name="heater-precautions"></a>Opatření topení

![Ikona upozornění ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ horké součásti nebo ikony povrchu 1 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **Upozornění:** 

* Automatická operace topení v době, kdy je systém zapnutý, může vytvořit nebezpečí dotykového ovládání kvůli vysokým teplotám na pokrytí sestavení topení. Nedotkněte se tohoto povrchu, když je systém zapnutý. Povolí dobu deseti minut po vypnutí systému.

![Ikona upozornění ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ ikona Pinching bodů 1 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **Upozornění:** 

* Když je systém zapnutý, může Automatické rozmístění zadních dveří plenum vytvořit riziko gesto roztažení prstů. Pokud je systém zapnutý, mějte k disrukou zřetelnost této oblasti.

## <a name="electrical-precautions"></a>Elektrická opatření

![Ikona upozornění ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ : varování ikony elektrického úrazu ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **:**

* Zajištění bezpečného připojení elektrického uzemnění k napájecímu kabelu napájení. Střídavý kabel aktuální (AC) má tři vývody uzemněného plug-in (plug-in, který má uzemněný PIN kód). Tento modul plug-in zapadá jenom na uzemněné vývody. Nepoužívejte nepřipravený účel základu PIN kódu.
* Vzhledem k tomu, že modul plug-in napájení je hlavním zařízením pro odpojení, je nutné zajistit, aby se sokety dostaly poblíž zařízení a byly snadno dostupné.
* Odpojte napájecí šňůry (tím, že vybíráte konektor, ne kabel), a odpojte všechny kabely, pokud existuje kterákoli z následujících podmínek:

  * Napájecí kabel nebo modul plug-in se přenese do pokrytí nebo jinak poškozený.
  * Rozdělíte něco na velká písmena zařízení.
  * Zařízení se zveřejňuje na deště nebo nadměrné vlhkosti.
  * Zařízení bylo vyřazeno a velikost zařízení je poškozena.
  * Máte podezření, že zařízení potřebuje službu nebo opravu.
* Před přesunutím jednotky trvale odpojte, nebo pokud se domníváte, že dojde k poškození jakýmkoli způsobem.
* Aby se předešlo vysokému úniku dat v současné době, kdy jeden tranzitní případ má více než jedno nepřerušované napájení (UPS), doporučuje se, aby se všechny zdroje UPS připojovaly k nezávislému okruhu. V případě, že se používá jednotka pro distribuci napájení (PDU) nebo jiné zařízení v případě, že bezpečnostní základ každé ze zdrojů UPS spoléhá na hnacím vodiči jediného informačního kanálu, musí být základní terminál na vnější straně každého zdroje UPS také použit spolu s dodatečnou deskou budování.

  > [!NOTE]
  > Pokud se použije jednotka PDU, která už má doplňkový deskový vodič, není nutné použít další podlahovou terminálu na UPS.

* Poskytněte vhodné zdroje energie s ochranou přetížení elektrického zabezpečení, aby splňovaly následující specifikace napájení:

  * Napětí: 100 až 240 Volts AC
  * Aktuální: 20 A, maximum na napájecí kabel. Napájecí šňůry jsou k dispozici.
  * Frekvence: 50 až 60 Hz

![Ikona upozornění ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ ikona elektrického úrazu ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
 ![ více zdrojů napájení ](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png) **Upozornění:**

* U systémů bez nepřerušovaného napájení (UPS) odpojte všechny šňůry STŘÍDAVých napájecích kabelů, aby se z něj zcela odstranila energie z ELEKTRICKÉho zařízení.
* U systémů se zdrojem UPS odpojte všechny šňůry napájení střídavého napětí a pomocí vypínače napájení UPS oživit systém. UPS obsahuje nebezpečné napětí na AC a DC.
* V případě, že systém obsahuje zdroj UPS, byla zdrojem napájení zajištěn kabel s stíněným vstupem. Je nutné použít kabel stíněného vstupu, Nenahrazovat ani měnit kabel.

![Ikona upozornění ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ : varování ikony elektrického úrazu ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **:**

* U systémů vybavených zdroji UPS, AC a/nebo DC bude vždy začínat potenciálním rizikovým napětím napájení vygenerovaným z baterií nebo nástrojů. Aby nedošlo k poškození zařízení nebo poškození osobních škod, vždy předpokládat, že může dojít k napětí na výstupu UPS, a to i v případě, že se odpojíte od primárního zdroje napájení.
* U systémů vybavených zdroji UPS jsou všechna externí připojení obohacené zdrojem samičích zařízení. Neodstraňujte případ ani nevložíte nic do těchto nebo žádných konektorů ve zdrojovém zdroji.

![Ikona upozornění ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ : varování ikony elektrického úrazu ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **:**

* Nepokoušejte se změnit ani použít napájecí šňůry střídavého napětí, než jaké jsou k dispozici u zařízení.

![Ikona upozornění ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ žádné ikony uživatelských služeb s upozorněním ](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **:**

* Toto vybavení obsahuje buňku mince pro lithium nebo baterii lithium železa. Nepokoušejte se obsluhovat vybavení. Baterie v tomto vybavení nepatří uživatelům. Riziko výbuchu v případě, že je baterie nahrazena nesprávným typem.
* Odebrání modulu pro napájení ze zdroje UPS zveřejňuje obohacené části v rámci UPS. Nevkládat cizí objekty do oddílu modulu baterie.

## <a name="regulatory-information"></a>Regulativní informace

Tato část obsahuje regulativní informace pro zařízení Azure Stack Edge pro R, regulativní model číslo: Azure Stack Edge pro R.

Hraniční zařízení Azure Stack Edge pro R je určené pro použití s uvedenými NRTL (UL, CSA, ETL atd.), a IEC/EN 60950-1 nebo IEC/EN 62368-1 splňující požadavky (CE označené) informační technologie.

Zařízení je navržené tak, aby fungovalo v následujících prostředích:

| Prostředí | Specifikace |
|:--- |:--- |
|Specifikace teploty | <ul><li>Teplota úložiště: – 33 &deg; c – 63 &deg; c (– 28 &deg; F-145 &deg; F) </li><li>Průběžná operace: 5 &deg; c – 43 &deg; c (41 &deg; f – 110 &deg; f)</li><li>Maximální teplota při přechodu (provozní a úložné): 20 &deg; C/h (68 &deg; F/h)</li></ul> |
|Specifikace relativní vlhkosti | <ul><li>Úložiště: 5% až 95% RH s 33 &deg; C (91 &deg; F) maximálním Dew bodem. Atmosféra musí být neustále nekondenzovaná.</li><li>Provoz: 5% až 85% relativní vlhkost s 29 &deg; C (84,2 &deg; F) maximálním bodem Dew</li></ul> |
| Specifikace maximální výšky | <ul><li>Provoz (bez napájení): 15 000 ft (4 572 měřičů)</li><li>Provoz (se zdrojem UPS): 10 000 ft (3 048 měřičů)</li><li>Úložiště: 40 000 ft (12 192 měřičů)</li></ul> |

<!--|Standard operating temperature specifications | <ul>Continuous operation (for altitude less than 950 m or 3117 ft): +5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</ui>
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li></= 1% of annual operating hours: –5&deg;C to 55&deg;C at 5% to 90% RH with 29&deg;C dew point. |
| Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15&deg;C according to IEC 60945.</li><li>The operating temperature specified is for a maximum altitude of 950 meters.</li></ul> |
| Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.</li></ul>|

> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to –5&deg;C or up to 55&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
>
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.     
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. 


A device that has a UPS installed is designed to operate in the following environments:

| Environment | Specifications |
|:---  |:--- |
|Temperature specifications | <ul><li>Storage temperature: –40&deg;C–70&deg;C (–40&deg;F-158&deg;F) per Mil-Std 810G Method 501.5, Proc 1</li><li>Continuous operation (for altitude less than 950 m or 3117 ft): 5&deg;C–45&deg;C (41&deg;F–113&deg;F), with no direct sunlight on the equipment</li><li>Excursion temperature: 50&deg;C per Mil-Std 810G</li><li>Maximum temperature gradient (operating and storage): 20&deg;C/h (68&deg;F/h)</li></ul> |
|Relative humidity specifications | <ul><li>Storage: 5% to 95% RH with 33&deg;C (91&deg;F) maximum dew point. Atmosphere must be non-condensing at all times.</li><li>Operating: 5% to 85% relative humidity with 29&deg;C (84.2&deg;F) maximum dew point</li></ul>|
|Maximum altitude specifications | <ul><li>Storage: Mil-Std 810G method 500.5, Proc. I, 40,000 ft for 1 hour after stabilization </li><li>Operating: Mil-Std 810G method 500.5, Proc. II, air carriage, 15,000 ft for 1 hour after stabilization</li></ul>|
|Standard operating temperature specifications |<ul><li>Continuous operation (for altitude less than 950 m or 3117 ft)</li><li>+5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</li></ul>|
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li>&lt;/= 1% of annual operating hours: 0&deg;C to 50&deg;C at 5% to 90% RH with 29&deg;C dew point.</li></ul>|
|Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15C Per IEC 60945</li><li>The operating temperature specified is for a maximum altitude of 950 meters</li></ul> |
|Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.|-->

<!--
> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to 0&deg;C or up to 50&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
> 
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. --> 


> ![Ikona oznámení – 2 ](./media/azure-stack-edge-pro-r-safety/icon-safety-notice.png) **Upozornění:** &nbsp; změny nebo změny zařízení, které nejsou výslovně schválené společností Microsoft, mohou zrušit oprávnění uživatele k provozu tohoto zařízení.

#### <a name="canada-and-usa"></a>Kanada a USA:

> ![Ikona oznámení-2 ](./media/azure-stack-edge-pro-r-safety/icon-safety-notice.png) **Upozornění:** &nbsp; Toto zařízení bylo testováno a bylo zjištěno, že vyhovuje omezením pro třídu a digitálním zařízením, a to v souladu s částí 15 pravidel FCC. Tato omezení jsou navržená tak, aby poskytovala rozumnou ochranu proti škodlivému rušení, když je zařízení provozováno v komerčním prostředí. Toto vybavení vygeneruje, používá a může vycházet z frekvence rádiového kmitočtu a pokud není nainstalované a používané v souladu s ruční příručkou, může způsobit škodlivou interferenci s rádiovou komunikací. Provoz tohoto vybavení v domácí oblasti pravděpodobně způsobí škodlivou interferenci v takovém případě bude uživatel muset opravit rušivý vliv na své náklady.

Toto zařízení je v souladu s částí 15 pravidel FCC a s výjimkou standardu RSS pro odvětví Kanady. Operace podléhá následujícím dvěma podmínkám: (1) Toto zařízení nemusí způsobovat škodlivou interferenci a (2) Toto zařízení musí přijmout jakékoli rušivé rušení, včetně rušení, které by mohlo způsobit nepožadovanou činnost zařízení.

![Regulativní informace – upozornění 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)


MŮŽE ICES-3 (A)/NMB-3 (A) Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, USA USA: (800) 426-9400 Kanada: (800) 933-4750

#### <a name="european-union"></a>EVROPSKÁ UNIE:

Vyžádá kopii deklarace shody EU. Odešlete e-mail na adresu [CSI_Compliance@microsoft.com](mailto:CSI_Compliance@microsoft.com).

![Upozornění na ikonu upozornění ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **!**

Toto je produkt třídy A. V domácím prostředí může tento produkt v takovém případě způsobit rušivé porušování. uživatel může být nutný k tomu, aby přijal vhodná opatření.

Odstraňování odpadních baterií a elektrických a elektronických zařízení:

![Ikona upozornění 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

Tento symbol v produktu nebo jeho bateriích nebo jeho obalech znamená, že tento produkt a všechny baterie, které obsahuje, nesmí být zneškodněny s využitím vašich domácích odpadů. Místo toho je vaší zodpovědností předat ho do vhodného bodu shromažďování pro recyklaci baterií a elektrického a elektronického vybavení. Tato samostatná kolekce a recyklace bude pomáhat šetřit přírodní prostředky a zabránit potenciálním negativním důsledkům pro lidské zdraví a prostředí z důvodu možné přítomnosti nebezpečných látek v bateriích a elektrických i elektronických zařízeních, což může být způsobeno nevhodným vyřazením. Další informace o tom, kde odkládat baterie a elektrická a elektronický odpad, získáte od místního města, služby pro odstranění odpadu v domácnosti nebo nákupu, kde jste tento produkt zakoupili. Kontaktujte erecycle@microsoft.com pro další informace o OEEZ.

Tento produkt obsahuje buněčnou baterii buněk (y).

Microsoft Ireland Sandyford – Est Est D18 KX32 IRL telefonní číslo: + 353 1 295 3826 fax číslo: + 353 1 706 4110


## <a name="next-steps"></a>Další kroky

- [Příprava na nasazení Azure Stack Edge pro R Edge](azure-stack-edge-pro-r-deploy-prep.md)

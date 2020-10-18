---
title: Hlasové asistenti v pokynech pro návrh Windows
titleSuffix: Azure Cognitive Services
description: Pokyny pro osvědčené postupy při navrhování prostředí hlasového agenta.
services: cognitive-services
author: cfogg6
manager: spencer.king
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: adamwa
ms.openlocfilehash: 76a29af80891142fcf7f56f93f5c7acc5c58ab04
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165141"
---
# <a name="design-assistant-experiences-for-windows-10"></a>Prostředí pomocníka návrhů pro Windows 10

Hlasové asistenty vyvinuté ve Windows 10 musí implementovat níže uvedené pokyny pro činnost koncového uživatele, aby poskytovaly nejlepší možné prostředí pro aktivaci hlasu ve Windows 10. Tento dokument vám pomůže vývojářům pochopit klíčovou práci potřebnou ke integraci hlasového asistenta s prostředím Windows 10.

## <a name="contents"></a>Obsah

- [Shrnutí zobrazení aktivace hlasu podporovaných ve Windows 10](#summary-of-voice-activation-views-supported-in-windows-10)
- [Souhrn požadavků](#requirements-summary)
- [Osvědčené postupy pro dobré naslouchací prostředí](#best-practices-for-good-listening-experiences)
- [Pokyny k návrhu aktivace hlasu v aplikaci](#design-guidance-for-in-app-voice-activation)
- [Pokyny k návrhu aktivace hlasu nad zámkem](#design-guidance-for-voice-activation-above-lock)
- [Pokyny k návrhu pro ukázku aktivace hlasu](#design-guidance-for-voice-activation-preview)

## <a name="summary-of-voice-activation-views-supported-in-windows-10"></a>Shrnutí zobrazení aktivace hlasu podporovaných ve Windows 10

Windows 10 odvodí prostředí aktivace pro kontext zákazníka na základě kontextu zařízení. Následující Souhrnná tabulka představuje podrobný přehled různých zobrazení, která jsou k dispozici, když je obrazovka zapnutá.

| Zobrazit (dostupnost) | Kontext zařízení | Cíl zákazníka | Se zobrazí, když | Potřeby návrhu |
| --- | --- | --- | --- | --- |
| **V aplikaci (19H1)** | Pod zámkem, asistent má fokus | Interakce s aplikací pomocníka | Asistent zpracovává požadavky v aplikaci | Prostředí pro naslouchání v hlavním zobrazení v aplikaci |
| **Nad zámkem (19H2)** | Nad zámkem, Neověřeno | Interakce s pomocníkem, ale od vzdálenosti | Systém je uzamčený a asistent žádá o aktivaci. | Vizuály na celé obrazovce pro uživatelské rozhraní pro většinu polí. Implementujte neúspěšné zásady, které neblokují odemknutí. |
| **Ukázka aktivace hlasu (20H1)** | Pod zámkem nemá pomocník fokus. | Interakce s pomocníkem, ale méně rušivým způsobem | Systém je pod požadavky Lock a Assistant k aktivaci na pozadí. | Minimální plátno. Podle potřeby změňte velikost nebo ruční zobrazení hlavní aplikace. |

## <a name="requirements-summary"></a>Souhrn požadavků

Pro přístup k různým prostředím se vyžaduje minimální úsilí. Asistenti ale musí implementovat správné pokyny k návrhu pro každé zobrazení. V této tabulce najdete kontrolní seznam požadavků, které je třeba dodržovat.

| **Zobrazení aktivace hlasu** | **Souhrn požadavků na asistenta** |
| --- | --- |
| **V aplikaci** | <ul><li>Zpracování žádosti v aplikaci</li><li>Poskytuje indikátory uživatelského rozhraní pro stavy naslouchání.</li><li>Uživatelské rozhraní se přizpůsobí při změně velikosti okna.</li></ul> |
| **Nad zámkem** | <ul><li>Zjistit stav zámku a aktivovat požadavek</li><li>Nedávejte vždy trvalé uživatelské prostředí, které by blokovalo přístup k zamykací obrazovce Windows.</li><li>Poskytování vizuálů na celé obrazovce a prostředí pro první použití hlasu</li><li>Doprovodné materiály k disminutě</li><li>Sledujte požadavky na ochranu osobních údajů a zabezpečení níže</li></ul> |
| **Ukázka aktivace hlasu** | <ul><li>Zjistit stav odemčení a žádost o aktivaci na pozadí</li><li>Vykreslení minimálního naslouchajícího uživatelského rozhraní v podokně náhledu</li><li>Nakreslit v pravém horním rohu pravou X a po stisknutí tlačítka Zavřít a zastavit streamování zvuku</li><li>Změna velikosti nebo ručního zobrazení aplikace v hlavním okně pomocníka podle potřeby k poskytnutí odpovědí</li></ul> |

## <a name="best-practices-for-good-listening-experiences"></a>Osvědčené postupy pro dobré naslouchací prostředí

Asistenti by měli vytvořit prostředí pro naslouchání, které vám poskytne kritickou zpětnou vazbu, aby zákazník pochopil stav pomocníka. Níže jsou uvedené některé možné stavy, které je potřeba vzít v úvahu při sestavování pomocníka. Jsou to jenom návrhy, ne povinné doprovodné materiály.

- Pomocník je k dispozici pro vstup řeči
- Asistent aktivuje (buď klíčová slova, nebo stisknutí tlačítka mikrofonu).
- Pomocník je aktivně streamuje zvuk do pomocníka cloudu.
- Pomocník je připraven, aby zákazník začal mluvit.
- Asistent uslyší, že jsou uvedená slova.
- Asistent rozumí, že se zákazník domluví.
- Asistent zpracovává a připravuje odpověď.
- Asistent reaguje

I když se stavy mění rychle, je vhodné zvážit poskytování uživatelského rozhraní pro stavy, protože doba trvání je proměnná v ekosystému systému Windows. Vizuální zpětnou vazbu a krátký zvuk CHIMES nebo chirps, označovaný také jako &quot; earcons &quot; , můžou být součástí řešení. Podobně se u vizuálních karet, které jsou propojeny s popisy zvuku, doplní dobré možnosti odezvy.

## <a name="design-guidance-for-in-app-voice-activation"></a>Pokyny k návrhu aktivace hlasu v aplikaci

Když má aplikace pomocníka fokus, záměr zákazníka je snadno interagovat s aplikací, takže všechna prostředí hlasové aktivace by měla být zpracována hlavním zobrazením aplikace. Toto zobrazení může změnit velikost zákazníka. Aby bylo možné vysvětlit interakce prostředí pomocníka, zbytek tohoto dokumentu používá konkrétní příklad asistenta finančních služeb s názvem contoso. V tomto a dalším diagramu se zobrazí zpráva o tom, co zákazník zobrazuje na stránce kreslené bubliny řeči na levé straně s odezvami pomocníka ve kreslené bublině na pravé straně.

**Zobrazení v aplikaci. Počáteční stav při zahájení aktivace hlasu:** 
 ![ snímek obrazovky s otevřenou aplikací společnosti Contoso pro finanční asistenty, která je otevřená na výchozím plátně. Kreslené bubliny pro řeč na pravé straně říká "contoso".](media/voice-assistants/windows_voice_assistant/initial_state.png)

**Zobrazení v aplikaci. Po úspěšném pokusu o aktivaci hlasu se spustí naslouchací proces:** ![ snímek hlasového pomocníka ve Windows, když hlasová asistent naslouchá.](media/voice-assistants/windows_voice_assistant/listening.png)

**Zobrazení v aplikaci. Všechny odpovědi zůstanou v prostředí aplikace.** ![ Snímek obrazovky hlasového asistenta v systému Windows jako reakce pomocníka](media/voice-assistants/windows_voice_assistant/response.png)

## <a name="design-guidance-for-voice-activation-above-lock"></a>Pokyny k návrhu aktivace hlasu nad zámkem

K dispozici pro 19H2, asistenti postavená na platformě Windows Voice Activation Platform jsou k dispozici pro zodpovězení výše uvedeného zámku.

### <a name="customer-opt-in"></a>Výslovný souhlas se zákazníkem

Aktivace hlasu nad zámkem je ve výchozím nastavení vždy zakázána. Zákazníci se můžou přihlásit pomocí nastavení Windows>ochrany osobních údajů>hlasové aktivace. Podrobnosti o monitorování a zobrazování výzev k tomuto nastavení najdete v části [Průvodce implementací zámku](windows-voice-assistants-implementation-guide.md#detecting-above-lock-activation-user-preference).

### <a name="not-a-lock-screen-replacement"></a>Nejde o nahrazení zamykací obrazovky.

I když jsou pro pomocníka k dispozici oznámení nebo jiné standardní integrační body zamykací aplikace, Windows zamykací obrazovka vždy definuje úvodní prostředí pro zákazníky, dokud nedojde k aktivaci hlasu. Po zjištění hlasové aktivace se aplikace pomocníka dočasně zobrazí nad zamykací obrazovkou. Aby nedošlo k nejasnostem, musí aplikace asistenta při aktivním uzamknutí uživatelského rozhraní nikdy zobrazit uživatelské rozhraní, aby potvrdila jakýkoli druh přihlašovacích údajů nebo přihlášení.

![Snímek obrazovky s zamykací obrazovkou Windows](media/voice-assistants/windows_voice_assistant/lock_screen1.png)

### <a name="above-lock-experience-following-voice-activation"></a>Výše uvedené možnosti uzamčení po aktivaci hlasu

Když je obrazovka zapnutá, aplikace pomocníka je na celé obrazovce bez záhlaví nad zamykací obrazovkou. Větší vizuály a silné popisy hlasu pomocí silného hlasového a primárního rozhraní umožňují případy, kdy je zákazník moc daleko od čtení uživatelského rozhraní nebo je zaneprázdněný jiným úkolem (mimo počítač).

Když obrazovka zůstane vypnutá, může aplikace pomocníka přehrát earcon a upozornit na to, že pomocníka se aktivuje a poskytne jenom hlasové prostředí.

![Snímek obrazovky hlasového asistenta nad zámkem](media/voice-assistants/windows_voice_assistant/above_lock_listening.png)

### <a name="dismissal-policies"></a>Zásady pro vracení

Pomocník musí v této části implementovat pokyny k odhlášení, aby se zákazníci snadněji přihlásili v okamžiku, kdy chtějí použít počítač s Windows. Níže jsou uvedené specifické požadavky, které asistent musí implementovat:

- **Všechna plátna pomocníka, která zobrazují výše, musí** v pravém horním rohu obsahovat znak X, který pomocníka vynechává.
- **Stisknutí libovolné klávesy musí také zavřít aplikaci pomocníka**. Vstup z klávesnice je tradiční signál zámku aplikace, ke kterému se chce zákazník přihlásit. Proto by žádný vstup z klávesnice/textu neměl být směrován do aplikace. Místo toho by se měla aplikace při zjištění klávesnice automaticky zavřít, aby se zákazník mohl snadno přihlásit ke svému zařízení.
- **V případě, že obrazovka zmizí, musí se aplikace samo zavřít.** Tím se zajistí, že při příštím použití počítače zákazník bude přihlašovací obrazovka připravená a čeká na ně.
- Pokud je aplikace &quot; používána &quot; , může pokračovat výše v zámku. &quot;používá se pro &quot; jakýkoliv vstup nebo výstup. Například při streamování hudby nebo videa může aplikace pokračovat výše v zámku. &quot;Sledování &quot; a další kroky při více zapnutích jsou povoleny, aby aplikace zůstala nad zámkem.
- **Podrobnosti o implementaci chybějící aplikace** najdete [v části výše Průvodce implementací zámku](windows-voice-assistants-implementation-guide.md#closing-the-application).

![Snímek obrazovky s výše uvedeným zobrazením zámku aplikace pro finanční asistenta společnosti Contoso](media/voice-assistants/windows_voice_assistant/above_lock_response.png)

![Snímek obrazovky plochy, na které se zobrazuje zamykací obrazovka Windows](media/voice-assistants/windows_voice_assistant/lock_screen2.png)

### <a name="privacy-amp-security-considerations-above-lock"></a>Požadavky na zabezpečení ochrany osobních údajů &amp; nad zámkem

Mnoho počítačů je přenosné, ale ne vždy v rámci dosahu zákazníků. Mohou být krátce ponechány v hotelových místnostech, na sedadlech v letadle nebo v pracovních prostorech, kde mají jiní uživatelé fyzický přístup. Pokud pomocníky, které jsou povolené výše než zámek, nejsou připravené, můžou se stát podléhat Maid útokům, které se nazývají &quot; [Evil](https://en.wikipedia.org/wiki/Evil_maid_attack) &quot; .

Proto by asistenti měli postupovat podle pokynů v této části, aby se zajistila bezpečnost. Interakce nad zámkem nastane, pokud uživatel Windows není ověřený. To znamená, že obecně platí, že **vstup pro pomocníka by měl být také považován za Neověřeno**.

- Asistenti by měli **implementovat seznam povolených dovedností k identifikaci dovedností, které jsou potvrzené zabezpečeně a bezpečné** pro přístup přes zámek.
- Technologie ID mluvčího mohou hrát roli v důsledku zmírnění některých rizik, ale ID mluvčího není vhodná náhrada za ověřování systému Windows.
- Seznam povolených dovedností by měl vzít v úvahu tři třídy akcí a dovedností:

| **Action – třída** | **Popis** | **Příklady (není úplný seznam)** |
| --- | --- | --- |
| Bezpečné bez ověřování | Obecné informace o účelu nebo příkaz a řízení základní aplikace | &quot;Jak to je? &quot; , &quot; Hrajte další stopu.&quot; |
| Bezpečná s ID mluvčího | Riziko zosobnění a odhalení osobních údajů. | &quot;Co&#39;s má příští událost? &quot; , &quot; zkontrolovat můj seznam nakupování &quot; , &quot; odpovědět na volání&quot; |
| Bezpečné až po ověření systému Windows | Vysoce rizikové akce, které by mohl útočník zneužít k poškozování zákazníka | &quot;Kupte si více nákupů &quot; , &quot; odstraňte moji (důležitou) schůzku &quot; , &quot; odešlete (střední) textovou zprávu &quot; , &quot; otevřete webovou stránku (nekalé).&quot; |

V případě společnosti Contoso jsou obecné informace o veřejných informacích o zásobách bezpečné bez ověření. Informace specifické pro konkrétní zákazníky, jako je třeba počet vlastněných akcií, jsou nejspíš bezpečné s ID mluvčího. Nákup nebo prodej zásob by však nikdy neměl být povolen bez ověřování systému Windows.

Za účelem dalšího zabezpečení prostředí **budou moci být funkce weblinks nebo jiné spuštění aplikace do aplikace blokovány systémem Windows, dokud se zákazník přihlásí.** Microsoft si vyhrazuje právo odebrat aplikaci ze seznamu povolených asistentů, pokud není včas vyřešeno závažné problémy se zabezpečením.

## <a name="design-guidance-for-voice-activation-preview"></a>Pokyny k návrhu pro ukázku aktivace hlasu

V případě, že aplikace pomocníka nemá _fokus_ , nabízí systém Windows méně rušivé uživatelské rozhraní aktivace hlasu, které pomůžou udržet zákazníka v toku. To platí zejména pro případ nepravdivých aktivací, které by byly vysoce rušivé při spuštění úplné aplikace. Základní nápad je, že každý asistent má v prostředí další domovskou stránku, ikona pomocníka na hlavním panelu. Když dojde k žádosti o aktivaci na pozadí, zobrazí se malé zobrazení nad ikonou hlavního panelu asistenta. Asistenti by měli na tomto plátně poskytovat malé možnosti poslechu. Po zpracování požadavků můžou asistenti zvolit změnu velikosti tohoto zobrazení tak, aby zobrazovala odpověď v kontextu, nebo aby si vypnula své hlavní zobrazení aplikace, aby zobrazovala větší, podrobnější vizuály.

- Aby zůstala minimální, verze Preview neobsahuje záhlaví, takže **asistent musí v pravém horním rohu nakreslit znak X, aby mohli zákazníci zavřít zobrazení.** Chcete-li [ukončit aplikaci](windows-voice-assistants-implementation-guide.md#closing-the-application) pro konkrétní rozhraní API, která budou volána při stisknutí tlačítka Zavřít, přečtěte si téma.
- Aby bylo možné podporovat verze Preview pro hlasové aktivace, můžou pomocníci pozvat zákazníky k připnutí pomocníka na hlavní panel během prvního spuštění.

**Ukázka aktivace hlasu: počáteční stav**

Asistent společnosti Contoso má doma na hlavním panelu: jeho spirála, kruhová ikona.

![Snímek obrazovky hlasového asistenta ve Windows jako ikona hlavního panelu před aktivací](media/voice-assistants/windows_voice_assistant/pre_compact_view.png)

**Jak probíhá aktivace**, asistent žádá o aktivaci na pozadí. Pomocníkovi je přiděleno malé podokno náhledu (výchozí šířka 408 a Výška: 248). Pokud funkce aktivace hlasu na straně serveru určí, že signál byl falešně pozitivní, může být toto zobrazení pro minimální přerušení neúspěšné.

![Snímek obrazovky hlasového pomocníka ve Windows v kompaktním zobrazení při ověřování aktivace](media/voice-assistants/windows_voice_assistant/compact_view_activating.png)

**Po potvrzení konečné aktivace**pomocník prezentuje své naslouchající uživatelské rozhraní. Pomocník musí v pravém horním rohu verze Preview aktivace hlasu vždy nakreslit.

![Snímek obrazovky hlasového asistenta při naslouchání ve Windows v kompaktním zobrazení](media/voice-assistants/windows_voice_assistant/compact_view_listening.png)

**Rychlé odpovědi** mohou být zobrazeny ve verzi Preview pro aktivaci hlasu. TryResizeView umožní pomocníkům vyžadovat různé velikosti.

![Snímek obrazovky hlasového asistenta v odpovědi Windows v kompaktním zobrazení](media/voice-assistants/windows_voice_assistant/compact_view_response.png)

**Ruční**. V každém okamžiku může pomocník předat do hlavního zobrazení aplikace Další informace, dialogy nebo odpovědi, které vyžadují větší množství obrazovek. Podrobnosti o implementaci najdete v části [Přechod z kompaktního zobrazení do kompletního zobrazení](windows-voice-assistants-implementation-guide.md#transition-from-compact-view-to-full-view) .

![Snímky obrazovky hlasového asistenta v systému Windows před a po rozbalení kompaktního zobrazení](media/voice-assistants/windows_voice_assistant/compact_transition.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začněte s vývojem hlasového asistenta](how-to-windows-voice-assistants-get-started.md)
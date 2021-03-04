---
title: Nejčastější dotazy
description: Nejčastější dotazy týkající se služby kotev objektů Azure.
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 04/01/2020
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: 6de027f317072512bd9893303d3a79d0f34f3e3e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748559"
---
# <a name="frequently-asked-questions-about-azure-object-anchors"></a>Nejčastější dotazy týkající se kotev objektů Azure

Kotvy objektů Azure umožňují aplikaci detekovat objekt ve fyzickém světě pomocí 3D model a odhadnout jeho 6 – DoF pozici.

Další informace najdete v tématu [Přehled kotev objektů Azure](overview.md).

## <a name="product-faq"></a>Nejčastější dotazy k produktu
**Otázka: Jaká doporučení máte pro objekty, které by měly být použity?**

**A:** Pro objekty doporučujeme následující vlastnosti:

* 1-10 měřičů pro každou dimenzi
* Nesymetrický s dostatečnými variacemi v geometrii
* Nízká odrazivost (podkladové povrchy) s jasně barevou
* Stacionární objekty
* Žádné nebo malé částky kloubu
* Vymazat pozadí bez nebo minimálního množství zbytečných
* Naskenovaný objekt by měl mít 1:1 odpovídající modelu, ve kterém jste vyškolete.

**Otázka: Jaké jsou maximální velikosti objektů, které lze zpracovat pro přijímání modelů?**

**A:** Každá dimenze modelu CAD by měla být menší než 10 metrů.

**Otázka: jaká je maximální velikost modelu CAD, kterou lze zpracovat pro ingestování?**

**A:** Velikost souboru modelu by měla být menší než 150 MB.

**Otázka: Jaké jsou podporované formáty CAD?**

**A:** V současné době podporujeme `fbx` `ply` `obj` typy souborů,,, `glb` a `gltf` .

**Otázka: co je směr gravitace a jednotka požadovaná službou ingestování modelu? Jak je můžeme odhlásit?**

**A:** Směr gravitace je vektor, který odkazuje na zemi. U modelů CAD je směr gravitace obvykle opakem směru směrem nahoru. Například v mnoha případech + Z představuje, v jakém případě – Z nebo `Vector3(0.0, 0.0, -1.0)` by představoval směr gravitace. Při určování gravitace byste neměli brát v úvahu jenom model, ale také orientaci, při které se model uvidí během běhu. Pokud se snažíte detekovat židli v reálném světě na plochém povrchu, může to být gravitace `Vector3(0.0, 0.0, -1.0)` . Pokud je ale židle na sklonu 45, může to být závažnost `Vector3(0.0, -Sqrt(2)/2, -Sqrt(2)/2)` .

Směr gravitace může být odůvodněný nástrojem 3D vykreslování, jako je [MeshLab](http://www.meshlab.net/).

Jednotka představuje jednotku měření modelu. Podporované jednotky se dají najít pomocí výčtu **Microsoft. Azure. ObjectAnchors.** ingestování. Unit.

**Otázka: jak dlouho trvá přijímání modelu CAD?**

**A:** Pro `ply` model, obvykle 3-15 minut. Při odesílání modelů v jiných formátech očekává v závislosti na velikosti souboru počkat 15-60 minut.

**Otázka: jaká zařízení podporují kotvy objektů?**

**A:** HoloLens 2 

**Otázka: který Build operačního systému by měl svůj HoloLens spustit?**

**A:** Build operačního systému 18363,720 nebo novější, vydaný po 12. březnu 2020.

  Další podrobnosti najdete v [aktualizaci 12. března 2020 ve Windows 10](https://support.microsoft.com/help/4551762).

**Otázka: jak dlouho trvá rozpoznávání objektu na HoloLens?**

**A:** Závisí na velikosti objektu a na procesu skenování. Pokud chcete získat rychlejší detekci, zkuste postupovat podle osvědčených postupů pro důkladné prověřování. Pro menší objekty v každé dimenzi v rámci 2 měřičů může detekce probíhat během několika sekund. U větších objektů, jako je auto, by měl uživatel projít úplným cyklem kolem objektu, aby získal spolehlivou detekci, což znamená, že detekce může trvat desítky sekund.

**Otázka: Jaké jsou osvědčené postupy při použití kotev objektů v aplikaci HoloLens?**

**Určitého**

 1. Proveďte kalibraci očí, abyste získali přesné vykreslování.
 2. Ujistěte se, že místnost má bohatou vizuální texturu a dobré osvětlení.
 3. Bez jakýchkoli zbytečných objektů Uchovávejte objekt z nepotřeb.
 4. Volitelně můžete vymazat mezipaměť [mapování prostorových dat](https://docs.microsoft.com/windows/mixed-reality/spatial-mapping) ve vašem zařízení HoloLens.
 5. Naskenujte objekt procházením. Ujistěte se, že je zjištěna většina objektu.
 6. Nastavte oblast hledání dostatečně velká pro pokrytí objektu.
 7. Objekt by měl při zjišťování zůstat v klidu.
 8. Spusťte detekci objektu a vizualizujte vykreslování na základě odhadované pozice.
 9. Zámek zjistil objekt nebo zastavil sledování, jakmile je pozice stabilní a přesná, aby zůstala v platnosti baterie.

**Otázka: jak přesně je předpokládaná pozice?**

**A:** Závisí na velikosti objektu, materiálu, prostředí atd. V případě malých objektů může být předpokládaná pozice v rámci 2 cm chyba. U velkých objektů, jako je auto, může být chyba až 2-8 cm.

**Otázka: mohou kotvy objektů zpracovat přesun objektů?**

**A:** Nepodporujeme **průběžné přesouvání** nebo **dynamické** objekty.

**Otázka: mohou kotvy objektů zpracovat deformaci nebo klouby?**

**A:** Částečně v závislosti na tom, kolik tvarů objektů nebo geometrie se mění z důvodu deformace nebo kloubu. Pokud se v geometrii objektu změní hodně, může uživatel pro tuto konfiguraci vytvořit jiný model a použít ho k detekci.

**Otázka: kolik různých objektů může kotvy objektů zjišťovat současně?**

**A:** V současné době podporujeme zjišťování jediného objektového modelu. 

**Otázka: mohou kotvy objektů detekovat více instancí stejného objektového modelu?**

**A:** Ano, můžete zjistit až 3 objekty stejného typu modelu. Aplikace může volat několikrát `ObjectObserver.DetectAsync` s různými dotazy, aby bylo možné zjistit více instancí stejného modelu.

**Otázka: co mám dělat, když kotvy objektů modul runtime nemůže detekovat svůj objekt?**

**Určitého**

* Zajistěte, aby místnost měla dostatek textur přidáním několika plakátů.
* Naskenování objektu je kompletní.
* Upravte parametry modelu, jak je popsáno výše.
* Poskytněte těsné ohraničující pole jako oblast hledání, která zahrnuje celý nebo většinu objektu.
* Vymaže mezipaměť prostorového mapování a znovu prohledá objekt.
* Zachyťte diagnostiku a odešlete data do nás.

**Otázka: Jak zvolit parametry dotazu na objekt?**

**Určitého**

* Poskytněte těsné oblasti hledání k ideálnímu pokrytí celého objektu pro zlepšení rychlosti a přesnosti detekce.
* Výchozí hodnota `ObjectQuery.MinSurfaceCoverage` z objektového modelu je obvykle dobrá, jinak použijte menší hodnotu k získání rychlejší detekce.
* `ObjectQuery.ExpectedMaxVerticalOrientationInDegrees`Pokud se očekává, že se objekt bude napravit, použijte malou hodnotu.
* Aplikace by měla `1:1` při detekci vždy použít objektový model. Odhadovaná velikost by měla být v ideálním případě v rámci 1% chyby. Aplikace může nastavit `ObjectQuery.MaxScaleChange` `0` nebo `0.1` zakázat nebo povolit odhad škálování a kvalitativně vyhodnotit pozici instance.

**Otázka: Návody získat z HoloLens data z objektu pro diagnostiku?**

**A:** Aplikace může určit umístění archivů diagnostiky. Ukázková aplikace pro kotvy objektů zapisuje diagnostiku do složky **TempState** .

**Otázka: Proč nezpůsobí, že zdrojový model není zarovnán s fyzickým objektem při použití pozice vrácené sadou funkcí kotev objektu | rozhraní Unity SDK?**

**A:** Unity může změnit systém souřadnic při importu objektového modelu. Například kotvy objektů Unity SDK invertují osu Z na pravé straně systému souřadnic na levé straně, ale Unity může použít další otočení kolem osy X nebo Y. Vývojář může tuto dodatečnou rotaci určit tak, že vizualizuje a porovnává systémy souřadnic.

**Otázka: podporujete 2D?**

**A:** Vzhledem k tomu, že jsme na bázi geometrie, podporujeme jenom trojrozměrné.

**Otázka: lze rozlišovat mezi stejným modelem v různých barvách?**

**A:** Vzhledem k tomu, že naše algoritmy jsou založené na geometrie, různé barvy stejného modelu se při detekci nechovají jinak.

**Otázka: je možné použít kotvy objektů bez připojení k Internetu?**

**Určitého** 
* Pro ingestování modelů a jejich školení se vyžaduje připojení, jak se děje v cloudu.
* Běhové relace jsou plně v zařízení a nevyžadují připojení, protože ke všem výpočtům dochází na HoloLens 2.

## <a name="privacy-faq"></a>Nejčastější dotazy týkající se ochrany osobních údajů
**Otázka: jak kotvy objektů Azure ukládají data?**

**A:** Ukládáme jenom systémová metadata, která jsou v klidovém stavu šifrovaná pomocí šifrovacího klíče spravovaného Microsoftem.

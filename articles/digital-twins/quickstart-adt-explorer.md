---
title: Rychlý Start – prozkoumat vzorový scénář
titleSuffix: Azure Digital Twins
description: Rychlý Start – ukázka pro vizualizaci a prozkoumání předem vytvořeného scénáře pomocí ukázky aplikace ADT Explorer
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: 5fb00fb6382bb53f40ad63a95c880c47f91cae2f
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92201660"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-adt-explorer"></a>Rychlý Start – Prozkoumejte ukázkový scénář digitálních vláken Azure pomocí Průzkumníka aplikace ADT

Pomocí digitálních vláken Azure můžete vytvářet a interagovat s živými modely vašich reálných prostředí. To se provádí modelováním jednotlivých prvků jako **digitálních vláken**a pak je propojíte do **grafu** znalostí, který může reagovat na živé události a dotazování na informace.

V tomto rychlém startu prozkoumáte předem sestavený graf digitálních vláken Azure, který vám pomůže s ukázkovou aplikací nazvanou [**Azure Digital Nevlákens (ADT) Explorer**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Průzkumník služby ADT umožňuje nahrát digitální reprezentaci prostředí, zobrazit vizuální obrázky vláken a grafů, které jsou vytvořeny tak, aby představovaly prostředí v digitálních událostech Azure, a provádět další aktivity správy prostřednictvím vizuálního prostředí založeného na prohlížeči.

Rychlý Start obsahuje následující hlavní kroky:

1. Nastavení instance digitálních vláken Azure a Průzkumníka aplikace ADT
1. Nahrajte předem připravené modely a data grafu a vytvořte vzorový scénář.
1. Prozkoumejte graf scénáře, který je vytvořený.
1. Provedení změn v grafu

Vzorový graf, se kterým budete pracovat, představuje sestavení se dvěma podlahami a dvěma místnosti. Graf bude vypadat takto:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Zobrazení grafu tvořeného čtyřmi kruhovými uzly, které jsou připojeny šipkami. Kruh označený jako ' Floor1 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room1 '; kruh označený jako ' Floor0 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room0 '. ' Floor1 ' a ' Floor0 ' nejsou připojeny.":::

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu budete potřebovat předplatné Azure. Pokud ho ještě nemáte, vytvořte si **[ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** hned teď.

Budete také potřebovat **Node.js** na svém počítači. Nejnovější verzi můžete získat na tomto odkazu: [Node.js](https://nodejs.org/).

Nakonec budete muset stáhnout dvě ukázky, které byste měli použít při rychlém startu:
* Ukázková aplikace **Průzkumníka aplikace ADT** Tato ukázka obsahuje hlavní aplikaci, kterou používáte v rychlém startu k načtení a prozkoumání scénáře digitálních vláken Azure. Aplikaci získáte tak, že přejdete sem: [Průzkumník digitálních vláken Azure (ADT)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Stiskněte tlačítko *Stáhnout soubor zip* a Stáhněte si *. Soubor ZIP* tohoto ukázkového kódu do vašeho počítače. Tím se stáhne. Složka ZIP na váš počítač jako _**Azure_Digital_Twins__ADT__explorer.zip**_. Rozbalte složku a extrahujte soubory.
* **Příklad scénáře digitálních vláken Azure** To zahrnuje předem sestavený graf digitálních vláken Azure, který budete načítat do Průzkumníka služby ADT, abyste mohli pracovat s. Pokud chcete scénář získat, přejděte sem: [Azure Digital se dokončí kompletními ukázkami](/samples/azure-samples/digital-twins-samples/digital-twins-samples). Stiskněte tlačítko *Stáhnout soubor zip* a Stáhněte si *. Soubor ZIP* tohoto ukázkového kódu do vašeho počítače. Tím se stáhne. Složka ZIP na váš počítač jako _**Azure_Digital_Twins_end_to_end_samples.zip**_. Rozbalte složku a extrahujte soubory.

## <a name="set-up-azure-digital-twins-and-adt-explorer"></a>Nastavení digitálních vláken Azure a Průzkumníka služby ADT

Prvním krokem při práci se službou Azure Digital proworking je nastavení **instance digitálních vláken Azure**. Po vytvoření instance služby ji budete moct naplnit pomocí ukázkových dat později v rychlém startu.

Nastavili jste taky oprávnění pro Průzkumníka aplikace ADT ke spuštění na vašem počítači a přístup k instanci digitálních vláken Azure. To vám umožní prozkoumat svou instanci a data pomocí ukázkové aplikace.

### <a name="set-up-azure-digital-twins-instance-and-app-registration"></a>Nastavení instance digitálních vláken Azure a registrace aplikací

Nejdřív **nastavte instanci digitálních vláken Azure** a požadované ověřování, abyste s ním mohli pracovat. Provedete to podle pokynů v tématu [*Postupy: nastavení instance a ověřování*](how-to-set-up-instance-portal.md). V závislosti na preferovaných zkušenostech je k dispozici článek o instalaci pro [ukázkový skript nasazení](how-to-set-up-instance-scripted.md) [Azure Portal](how-to-set-up-instance-portal.md), [CLI](how-to-set-up-instance-cli.md)nebo automatizované Cloud Shell. Všechny verze pokynů také obsahují kroky k ověření, že jste dokončili každý krok úspěšně a že jste připraveni na přechod k používání nové instance.
* Po nastavení instance digitálního vlákna Azure budete potřebovat **_název hostitele_** instance ([najít v portálu](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

Chcete-li ověřit aplikaci pro Průzkumníka aplikace ADT, budete také muset nastavit **registraci aplikace**. Postupujte podle pokynů v tématu [*Postupy: Vytvoření registrace aplikace*](how-to-create-app-registration.md) pro nastavení. 
* Jakmile budete mít registraci aplikace, budete potřebovat ID **_aplikace (klienta)_** registrace a **_ID adresáře (_** klienta) ([tyto informace najdete na portálu](how-to-create-app-registration.md#collect-client-id-and-tenant-id).

### <a name="set-adt-explorer-permissions"></a>Nastavení oprávnění Průzkumníka aplikace ADT

Dále Připravte instanci digitálních vláken Azure, kterou jste vytvořili pro práci s Průzkumníkem aplikace ADT, což je místně hostovaná webová aplikace. Přejděte na stránku [Registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) v Azure Portal a v seznamu vyberte název registrace vaší **aplikace** , kterou jste vytvořili v předchozí části.

V nabídce registrace vyberte *ověřování* a stiskněte *+ Přidat platformu*.

:::image type="content" source="media/quickstart-adt-explorer/authentication-pre.png" alt-text="Zobrazení grafu tvořeného čtyřmi kruhovými uzly, které jsou připojeny šipkami. Kruh označený jako ' Floor1 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room1 '; kruh označený jako ' Floor0 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room0 '. ' Floor1 ' a ' Floor0 ' nejsou připojeny." lightbox="media/quickstart-adt-explorer/authentication-pre.png":::

Na následující stránce *Konfigurace platforem* vyberte *Web*.
Podrobnosti konfigurace vyplňte následujícím způsobem:
* **Identifikátory URI pro přesměrování**: přidejte identifikátor URI přesměrování *http://localhost:3000* .
* **Implicitní udělení**: zaškrtněte políčko pro *přístupové tokeny*.

Pro dokončení *Konfigurace* stiskněte klávesu.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/authentication-configure-web.png" alt-text="Zobrazení grafu tvořeného čtyřmi kruhovými uzly, které jsou připojeny šipkami. Kruh označený jako ' Floor1 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room1 '; kruh označený jako ' Floor0 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room0 '. ' Floor1 ' a ' Floor0 ' nejsou připojeny.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Nyní máte nakonfigurovanou konfiguraci webu, kterou použije Průzkumník aplikace ADT. Na kartě ověřování v Azure Portal by se měla odrážet. Po ověření níže uvedených částí stiskněte *Uložit*.

:::image type="content" source="media/quickstart-adt-explorer/authentication-post.png" alt-text="Zobrazení grafu tvořeného čtyřmi kruhovými uzly, které jsou připojeny šipkami. Kruh označený jako ' Floor1 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room1 '; kruh označený jako ' Floor0 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room0 '. ' Floor1 ' a ' Floor0 ' nejsou připojeny.":::

### <a name="run-and-configure-adt-explorer"></a>Spuštění a konfigurace Průzkumníka aplikace ADT

V dalším kroku spusťte aplikaci aplikace ADT Explorer a nakonfigurujte ji pro instanci digitálních vláken Azure.

Přejděte do složky Downloaded and unzip _**Azure_Digital_Twins__ADT__explorer**_ . Otevřete příkazový řádek v umístění složky *Azure_Digital_Twins__ADT__explorer/Client/src*.

Spusťte `npm install` , aby se stáhly všechny požadované závislosti.

Pak spusťte aplikaci spuštěním `npm run start` .

Po několika sekundách se otevře okno prohlížeče a aplikace se zobrazí v prohlížeči.

:::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="Zobrazení grafu tvořeného čtyřmi kruhovými uzly, které jsou připojeny šipkami. Kruh označený jako ' Floor1 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room1 '; kruh označený jako ' Floor0 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room0 '. ' Floor1 ' a ' Floor0 ' nejsou připojeny." lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

V horní části okna klikněte na tlačítko *Přihlásit* (zobrazené na obrázku níže) a nakonfigurujte Průzkumníka aplikace ADT, aby fungoval s instancí, kterou jste nastavili. 

:::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="Zobrazení grafu tvořeného čtyřmi kruhovými uzly, které jsou připojeny šipkami. Kruh označený jako ' Floor1 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room1 '; kruh označený jako ' Floor0 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room0 '. ' Floor1 ' a ' Floor0 ' nejsou připojeny." lightbox="media/quickstart-adt-explorer/sign-in.png":::

Zadejte důležité informace, které jste shromáždili dříve v části [požadavky](#prerequisites) :
* ID aplikace (klienta)
* ID adresáře (tenanta)
* Adresa URL instance digitálních vláken Azure ve formátu *https://{instance Name hosta}*

>[!NOTE]
> Tyto informace můžete kdykoli znovu navštívit nebo upravit tak, že vyberete stejnou ikonu pro opětovné načtení přihlašovacího pole. Zachová se hodnoty, které jste předali.

> [!TIP]
> Pokud `SignalRService.subscribe` se při připojení zobrazí chybová zpráva, ujistěte se, že adresa URL digitálních vláken Azure začíná na *https://*.

Pokud se zobrazí *oprávnění požadované* automaticky otevírané okno od společnosti Microsoft, poskytněte pro tuto aplikaci souhlas a potvrďte pokračování.

## <a name="add-the-sample-data"></a>Přidat ukázková data

V dalším kroku naimportujete vzorový scénář a graf do nástroje ADT Explorer.

Vzorový scénář je umístěný ve složce stažené a oddálení  _**Azure_Digital_Twins_end_to_end_samples**_ , takže byste měli přejít do složky hned teď.

### <a name="models"></a>Modely

Prvním krokem v řešení digitálních vláken Azure je definování slovníku pro vaše prostředí. K tomu je potřeba vytvořit vlastní [**modely**](concepts-models.md), které popisují typy entit, které ve vašem prostředí existují. 

Každý model je napsán v jazyce formátu JSON-LD, který se nazývá **DTDL (Digital Term Definition Language)**, a popisuje jediný typ entity z pohledu jeho *vlastností*, *telemetrie*, *vztahů*a *komponent*. Později použijete tyto modely jako základ pro digitální vlákna, které představuje konkrétní instance těchto typů.

Obvykle při vytváření modelu dokončíte tři kroky:
1. Zapsat definici modelu (v rychlém startu, která je už hotová jako součást ukázkového řešení)
2. Ověřte, jestli je syntaxe přesná (v rychlém startu, která se už udělala jako součást ukázkového řešení).
3. Nahrajte ho do instance digitálního vlákna Azure.
 
Pro tento rychlý Start již byly soubory modelů zapsány a ověřeny pro vás a jsou součástí řešení, které jste stáhli. V této části nahrajete do své instance dva předem napsané modely, abyste definovali tyto komponenty stavebního prostředí:
* Floor
* Prostor

#### <a name="upload-models"></a>Nahrávání modelů

V poli *zobrazení modelu* stiskněte tlačítko nahrát ikonu *modelu* .

:::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="Zobrazení grafu tvořeného čtyřmi kruhovými uzly, které jsou připojeny šipkami. Kruh označený jako ' Floor1 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room1 '; kruh označený jako ' Floor0 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room0 '. ' Floor1 ' a ' Floor0 ' nejsou připojeny." lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. V zobrazeném poli pro výběr souboru přejděte do složky *Azure_Digital_Twins_end_to_end_samples/adtsampleapp/sampleclientapp/Models* ve staženém úložišti.
2. Vyberte *Room.jszapnuto* a *Floor.js*a stiskněte OK. (Další modely můžete nahrát, pokud chcete, ale nepoužijí se v tomto rychlém startu.)
3. Použijte místní dialogové okno s výzvou, abyste se přihlásili ke svému účtu Azure.

>[!NOTE]
>Pokud se zobrazí následující chybová zpráva: došlo :::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="Zobrazení grafu tvořeného čtyřmi kruhovými uzly, které jsou připojeny šipkami. Kruh označený jako ' Floor1 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room1 '; kruh označený jako ' Floor0 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room0 '. ' Floor1 ' a ' Floor0 ' nejsou připojeny." border="false"::: 
> Zkuste zakázat blokování automaticky otevíraných oken nebo použít jiný prohlížeč.

Průzkumník aplikace ADT nyní nahraje tyto soubory modelu do instance digitálního vlákna Azure. Měly by se zobrazit v poli *zobrazení modelu* , kde se zobrazují jejich popisné názvy a úplná ID modelu. Můžete kliknout na bubliny s informacemi o *modelu zobrazení* a zobrazit kód DTDL za nimi.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="Zobrazení grafu tvořeného čtyřmi kruhovými uzly, které jsou připojeny šipkami. Kruh označený jako ' Floor1 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room1 '; kruh označený jako ' Floor0 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room0 '. ' Floor1 ' a ' Floor0 ' nejsou připojeny." lightbox="media/quickstart-adt-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>Vlákna a Dvojitá graf

Teď, když se některé modely nahrály do instance digitálního vlákna Azure, můžete přidat [**digitální vlákna**](concepts-twins-graph.md) , která následují za definicemi modelu. 

Digitální vlákna reprezentují skutečné entity v rámci vašeho podnikového prostředí: věci jako senzory na farmě, světla v kleci nebo – v tomto rychlém startu – místnosti v budově. Můžete vytvořit mnoho vláken libovolného typu daného modelu (například několik místností, které používají model *Room* ), a propojit je s relacemi do souvislého **grafu** , který představuje celé prostředí.

V této části budete nahrávat předem vytvořené vlákna, která jsou připojená do předem vytvořeného grafu. Graf obsahuje dvě podlahová a dvě místnosti připojená v následujícím rozložení:
* *Floor0*
    - obsahuje *Room0*
* *Floor1*
    - obsahuje *Room1*

#### <a name="import-the-graph"></a>Import grafu

V poli *zobrazení grafu* stiskněte ikonu *Import grafu* .

:::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="Zobrazení grafu tvořeného čtyřmi kruhovými uzly, které jsou připojeny šipkami. Kruh označený jako ' Floor1 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room1 '; kruh označený jako ' Floor0 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room0 '. ' Floor1 ' a ' Floor0 ' nejsou připojeny." lightbox="media/quickstart-adt-explorer/import-graph.png":::

V poli pro výběr souboru přejděte do složky *Azure_Digital_Twins_end_to_end_samples/adtsampleapp/sampleclientapp* a vyberte soubor tabulky _**buildingScenario.xlsx**_ . Tento soubor obsahuje popis ukázkového grafu. Stiskněte OK.

Po několika sekundách otevře Průzkumník aplikace ADT zobrazení pro *Import* , ve kterém se zobrazí náhled grafu, který se bude načítat.

Pokud chcete potvrdit nahrávání grafu, stiskněte ikonu *Uložit* v pravém horním rohu *zobrazení grafu*:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="Zobrazení grafu tvořeného čtyřmi kruhovými uzly, které jsou připojeny šipkami. Kruh označený jako ' Floor1 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room1 '; kruh označený jako ' Floor0 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room0 '. ' Floor1 ' a ' Floor0 ' nejsou připojeny." lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Průzkumník aplikace ADT nyní pomocí nahraného souboru vytvoří požadované zdvojení a vztahy mezi nimi. Zobrazí se dialogové okno s označením, že je dokončeno. Stiskněte *Zavřít*.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="Zobrazení grafu tvořeného čtyřmi kruhovými uzly, které jsou připojeny šipkami. Kruh označený jako ' Floor1 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room1 '; kruh označený jako ' Floor0 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room0 '. ' Floor1 ' a ' Floor0 ' nejsou připojeny." lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Graf byl nyní nahrán do Průzkumníka aplikace ADT. Chcete-li zobrazit graf, stiskněte tlačítko *Spustit dotaz* v poli *Průzkumník grafu* v horní části okna Průzkumníka aplikace ADT. 

:::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="Zobrazení grafu tvořeného čtyřmi kruhovými uzly, které jsou připojeny šipkami. Kruh označený jako ' Floor1 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room1 '; kruh označený jako ' Floor0 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room0 '. ' Floor1 ' a ' Floor0 ' nejsou připojeny." lightbox="media/quickstart-adt-explorer/run-query.png":::

Tím se spustí výchozí dotaz pro výběr a zobrazení všech digitálních vláken. Průzkumník aplikace ADT načte všechny vlákna a relace ze služby a nakreslí graf definovaný v poli *zobrazení grafu* .

## <a name="explore-the-graph"></a>Prozkoumat graf

Teď si můžete prohlédnout nahraný graf ukázkového scénáře:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Zobrazení grafu tvořeného čtyřmi kruhovými uzly, které jsou připojeny šipkami. Kruh označený jako ' Floor1 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room1 '; kruh označený jako ' Floor0 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room0 '. ' Floor1 ' a ' Floor0 ' nejsou připojeny.":::

Kruhy (grafy "uzly") reprezentují digitální vlákna a řádky prezentují relace. Uvidíte, že *Floor0* se nachází v *Room0*a vlákna *Floor1* obsahuje *Room1*.

Pokud používáte myš, můžete je přesunout kliknutím a přetažením částí grafu.

### <a name="view-twin-properties"></a>Zobrazit zdvojené vlastnosti 

Můžete vybrat dvojitou hodnotu pro zobrazení seznamu vlastností a jejich hodnot v poli *Průzkumník vlastností* . 

Tady jsou vlastnosti *Room0*:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="Zobrazení grafu tvořeného čtyřmi kruhovými uzly, které jsou připojeny šipkami. Kruh označený jako ' Floor1 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room1 '; kruh označený jako ' Floor0 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room0 '. ' Floor1 ' a ' Floor0 ' nejsou připojeny." lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Všimněte si, že *Room0* má teplotu **70**.

Tady jsou vlastnosti *Room1*:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="Zobrazení grafu tvořeného čtyřmi kruhovými uzly, které jsou připojeny šipkami. Kruh označený jako ' Floor1 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room1 '; kruh označený jako ' Floor0 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room0 '. ' Floor1 ' a ' Floor0 ' nejsou připojeny." lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Všimněte si, že *Room1* má teplotu **80**.

### <a name="query-the-graph"></a>Dotazování grafu

Hlavní funkcí digitálních vláken Azure je schopnost snadno a efektivně [dotazovat](concepts-query-language.md) se na váš graf, aby odpovídal na dotazy týkající se vašeho prostředí. 

Jedním ze způsobů, jak zadat dotaz na vlákna v grafu, je podle jejich *vlastností*. Dotazování na základě vlastností může přispět k zodpovězení nejrůznějších otázek, včetně vyhledávání životních hodnot ve vašem prostředí, které by mohly vyžadovat pozornost.

V této části spustíte dotaz na odpověď na následující otázku: _ **co jsou všechny vlákna v mém prostředí s teplotou vyšší než 75?**_

Pokud chcete zobrazit odpověď, spusťte následující dotaz v poli *Průzkumník dotazů* :

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Odvolání ze zobrazení vlastností s dvojitou platností dříve, než *Room0* má teplotu **70** a *Room1* má teplotu **80**. Výsledkem je, že se tady zobrazí jenom _**Room1é**_ výsledky.
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="Zobrazení grafu tvořeného čtyřmi kruhovými uzly, které jsou připojeny šipkami. Kruh označený jako ' Floor1 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room1 '; kruh označený jako ' Floor0 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room0 '. ' Floor1 ' a ' Floor0 ' nejsou připojeny." lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> Jiné operátory porovnání ( *<* , *>* , *=* , nebo *! =*) jsou podporovány také v rámci dotazu výše. Můžete se pokusit o připojení těchto, různých hodnot nebo různých vlastností do dotazu, abyste si vyzkoušeli odpovědi na vlastní otázky.

## <a name="edit-data-in-the-graph"></a>Úprava dat v grafu

Pomocí Průzkumníka programu ADT můžete upravit vlastnosti vláken reprezentovaných v grafu. V této části zvýšíme ** _teplotu_ Room0 až 76**.

Provedete to tak, že vyberete *Room0*a v poli *Průzkumník vlastností* zadáte seznam vlastností.

Vlastnosti v tomto seznamu lze upravovat. Vyberte hodnotu teploty **70** a povolte tak zadání nové hodnoty. Zadejte **76**a stiskněte ikonu *Uložit* a aktualizujte teplotu na **76**.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="Zobrazení grafu tvořeného čtyřmi kruhovými uzly, které jsou připojeny šipkami. Kruh označený jako ' Floor1 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room1 '; kruh označený jako ' Floor0 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room0 '. ' Floor1 ' a ' Floor0 ' nejsou připojeny." lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Po úspěšném uložení se zobrazí okno s *informacemi o opravě* , které zobrazuje kód opravy, který se použil na pozadí s [rozhraními API](how-to-use-apis-sdks.md) pro digitální vlákna Azure, která tuto aktualizaci provedla. Stiskněte *Zavřít*.

### <a name="query-to-see-the-result"></a>Dotaz pro zobrazení výsledku

Pokud chcete ověřit, že se v grafu úspěšně zaregistrovala vaše aktualizace na teplotu *Room0*, spusťte dotaz znovu z předchozích verzí a **Získejte všechny vlákna v prostředí s teplotou vyšší 75**:

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Teď, když se teplota *Room0* změnila z **70** na **76**, ve výsledku by se měla zobrazit obě vlákna.

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="Zobrazení grafu tvořeného čtyřmi kruhovými uzly, které jsou připojeny šipkami. Kruh označený jako ' Floor1 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room1 '; kruh označený jako ' Floor0 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room0 '. ' Floor1 ' a ' Floor0 ' nejsou připojeny." lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>Recenze a dát učení

V tomto rychlém startu jste vytvořili instanci digitálních vláken Azure, připojili ji k Průzkumníkovi aplikace ADT a naplnili ji ukázkovým scénářem. 

Pak jste prozkoumali graf podle...
1. Použití dotazu k zodpovězení otázky týkající se scénáře.
2. Úprava vlastnosti u digitálního vlákna.
3. Spusťte dotaz znovu, abyste viděli, jak se odpověď změnila v důsledku aktualizace.

Účelem tohoto cvičení je Ukázat, jak můžete pomocí grafu digitálních vláken Azure zodpovědět otázky týkající se vašeho prostředí, i když se prostředí stále mění. 

I když v tomto rychlém startu jste provedli aktualizaci teploty ručně, je běžné, že digitální vlákna Azure připojuje digitální vlákna ke skutečným zařízením IoT tak, aby automaticky přijímala aktualizace na základě dat telemetrie. To vám umožní vytvořit živý graf, který vždy odráží skutečný stav vašeho prostředí, a používat dotazy k získání informací o tom, co se ve vašem prostředí děje v reálném čase.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete zabalit práci pro tento rychlý Start, nejprve ukončete spuštěnou konzolovou aplikaci. Tím se vypne připojení k aplikaci pro Průzkumníka aplikace ADT v prohlížeči a v prohlížeči už nebudete moct zobrazovat živá data. Kartu prohlížeče můžete zavřít.

Pokud máte v úmyslu pokračovat na kurzy digitálních vláken Azure, instance použitá v tomto rychlém startu se dá pro tyto články znovu použít a nemusíte ji odebrat.
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Nakonec odstraňte ukázkové složky projektu, které jste stáhli do místního počítače (_**Azure_Digital_Twins__ADT__explorer**_ a _**Azure_Digital_Twins_end_to_end_samples**_). Možná bude nutné odstranit verze zip i unzip.

## <a name="next-steps"></a>Další kroky 

Dál se můžete podívat na kurzy digitálních vláken Azure a vytvořit vlastní scénář a nástroje pro interakci s digitálními scénáři Azure.

> [!div class="nextstepaction"]
> [*Kurz: vytvoření kódu klientské aplikace*](tutorial-code.md)
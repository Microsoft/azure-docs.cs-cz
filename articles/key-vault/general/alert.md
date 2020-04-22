---
title: Azure Key Vault monitorování a výstrahy | Dokumenty společnosti Microsoft
description: Vytvořte řídicí panel pro sledování stavu trezoru klíčů a konfiguraci výstrah.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to learn the options available to monitor the health of my vaults
ms.openlocfilehash: cc0d969ff6eb76732768dfed2826762920ae9e62
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726940"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Monitorování a výstrahy pro Azure Key Vault

## <a name="overview"></a>Přehled

Jakmile začnete používat trezor klíčů k ukládání tajných kódů výroby, je důležité sledovat stav trezoru klíčů, abyste se ujistili, že vaše služba funguje tak, jak má. Jakmile začnete škálovat službu, počet požadavků odeslaných do trezoru klíčů se zvýší. To má potenciál zvýšit latenci vašich požadavků a v extrémních případech způsobit, že vaše požadavky mají být omezeny, což bude mít vliv na výkon vaší služby. Pokud trezor klíčů odesílá neobvyklý počet kódů chyb, musíte být také upozorněni, abyste mohli být rychle upozorněni na jakékoli problémy s konfigurací přístupu nebo brány firewall. Tento dokument se bude zabývat následujícími tématy:

+ Základní metriky trezoru klíčů ke sledování
+ Konfigurace metrik a vytvoření řídicího panelu 
+ Jak vytvořit výstrahy při zadaných prahových hodnotách 

## <a name="basic-key-vault-metrics-to-monitor"></a>Základní metriky trezoru klíčů ke sledování

+ Dostupnost trezoru  
+ Sytost trezoru 
+ Latence rozhraní API služby 
+ Celkový počet přístupů rozhraní API služby (filtr podle typu aktivity) 
+ Kódy chyb (filtrovat podle stavového kódu) 

**Dostupnost trezoru** – Tato metrika by měla být vždy na 100 %, což je důležitá metrika ke sledování, protože vám může rychle ukázat, zda došlo k výpadku trezoru klíčů. 

**Sytost trezoru** – počet požadavků za sekundu, které může úložiště klíčů obsluhovat, je založen na typu prováděné operace. Některé operace úschovny mají nižší prahovou hodnotu požadavků za sekundu. Tato metrika agreguje celkové využití trezoru klíčů ve všech typech operací, aby přišla s procentuální hodnotou, která označuje aktuální využití trezoru klíčů. Úplný seznam omezení služeb trezoru klíčů naleznete v následujícím dokumentu. [Omezení služby Azure Key Vault](service-limits.md)

**Latence rozhraní API služby** – tato metrika zobrazuje průměrnou latenci volání do trezoru klíčů. Přestože trezor klíčů může být v rámci omezení služeb, vysoké využití trezoru klíčů může zavést latenci, která způsobí, že aplikace po proudu nezdaří. 

**Celkový počet přístupů rozhraní API** – tato metrika zobrazuje všechna volání do trezoru klíčů. To vám pomůže určit, které aplikace volají trezor klíčů. 

**Kódy chyb** – tato metrika vám ukáže, zda v trezoru klíčů dochází k neobvyklému množství chyb. Úplný seznam kódů chyb a pokyny pro řešení potíží naleznete v následujícím dokumentu. [Chybové kódy rozhraní API úložiště klíčů Azure](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>Konfigurace metrik a vytvoření řídicího panelu

1. Přihlaste se k webu Azure Portal.
2. Přechod do trezoru klíčů
3. Vybrat **metriky** v části **Monitorování** 

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s portálem Azure](../media/alert-1.png)

4. Aktualizujte název grafu na to, co chcete zobrazit na řídicím panelu. 
5. Vyberte obor. V tomto příkladu vybereme jeden trezor klíčů. 
6. Vyberte **průměr dostupnosti** a agregace úložiště **metriky.** 
7. Aktualizujte časový rozsah na posledních 24 hodin a aktualizujte čas rozlišovací schopnost na 1 minutu. 

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s portálem Azure](../media/alert-2.png)

8. Opakujte výše uvedené kroky pro metriky sytosti úložiště a latence rozhraní API služby. Výběrem **připnutí na řídicí panel** uložte metriky do řídicího panelu. 

> [!IMPORTANT]
> Vyberte "Připnout na řídicí panel" a uložte všechny metriky, které nakonfigurujete. Pokud stránku opustíte a vrátíte se k ní bez uložení, změny konfigurace budou ztraceny. 

9. Chcete-li sledovat všechny typy operací v trezoru klíčů, použijte metriku **celkových přístupů rozhraní API služby** a vyberte **použít rozdělení podle typu aktivity.**

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s portálem Azure](../media/alert-3.png)

10. Chcete-li sledovat kódy chyb v trezoru klíčů, použijte metriku **celkových výsledků rozhraní API služby** a vyberte **použít rozdělení podle typu aktivity.**

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s portálem Azure](../media/alert-4.png)

Nyní budete mít řídicí panel, který vypadá takto. Můžete kliknout na 3 tečky v pravém horním rohu každé dlaždice a můžete změnit uspořádání a velikost dlaždic, jak potřebujete. 

Jakmile řídicí panel uložíte a publikujete, vytvoří nový prostředek ve vašem předplatném Azure. Budete mít možnost vidět kdykoliv hledáním "sdílené hospo-panelu". 

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s portálem Azure](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Konfigurace upozornění v trezoru klíčů 

V této části se zobrazí postup konfigurace výstrah v trezoru klíčů, abyste mohli svůj tým upozornit, aby okamžitě podnikl akci, pokud je trezor klíčů v nefunkčním stavu. Můžete nakonfigurovat výstrahy, které odesílají e-mail, nejlépe do týmu DL, spálí oznámení mřížky událostí nebo volání nebo text telefonní číslo. Můžete také zvolit statické výstrahy na základě pevné hodnoty nebo dynamické výstrahy, která vás upozorní, pokud monitorovaná metrika překročí průměrný limit trezoru klíčů určitý počet v rámci definovaného časového rozsahu. 

> [!IMPORTANT]
> Upozorňujeme, že nově nakonfigurovaná upozornění mohou trvat až 10 minut, než začnou odesílat oznámení. 

### <a name="configure-an-action-group"></a>Konfigurace skupiny akcí 

Skupina akcí je konfigurovatelný seznam oznámení a vlastností.

1. Přihlaste se k webu Azure Portal.
2. Hledání **výstrah** ve vyhledávacím poli
3. Vybrat **spravovat akce**

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s portálem Azure](../media/alert-6.png)

4. Vybrat **+ Přidat skupinu akcí**

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s portálem Azure](../media/alert-7.png)

5. Zvolte **typ akce** pro skupinu akcí. V tomto příkladu vytvoříme e-mailovou výstrahu.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s portálem Azure](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s portálem Azure](../media/alert-9.png)

6. Klikněte na **OK** v dolní části stránky. Úspěšně jste vytvořili skupinu akcí. 

Nyní, když jste nakonfigurovali skupinu akcí, nakonfigurujeme prahové hodnoty výstrah trezoru klíčů. 

### <a name="configure-alert-thresholds"></a>Konfigurace prahových hodnot výstrah 

1. Vyberte prostředek trezoru klíčů na webu Azure Portal a v části **Monitorování** vyberte **Výstrahy.**

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s portálem Azure](../media/alert-10.png)

2. Vybrat **nové pravidlo výstrahy**

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s portálem Azure](../media/alert-11.png)

3. Vyberte rozsah pravidla výstrahy. Můžete vybrat jeden nebo více násobný. 

> [!IMPORTANT]
> Upozorňujeme, že při výběru více trezorů pro rozsah výstrah musí být všechny vybrané trezory ve stejné oblasti. Budete muset nakonfigurovat samostatná pravidla výstrah pro trezory v různých oblastech. 

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s portálem Azure](../media/alert-12.png)

4. Vyberte podmínky pro vaše výstrahy. Můžete zvolit některý z následujících signálů a definovat logiku pro výstrahy. Tým trezoru klíčů doporučuje konfigurovat následující prahové hodnoty výstrah. 

    + Dostupnost trezoru klíčů klesne pod 100 % (statická prahová hodnota)
    + Latence trezoru klíčů je větší než 500 ms (statická prahová hodnota) 
    + Celková sytost úložiště je větší než 75 % (statická prahová hodnota) 
    + Celková sytost úložiště překračuje průměr (dynamická prahová hodnota)
    + Celkové kódy chyb vyšší než průměr (dynamická prahová hodnota) 

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s portálem Azure](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>Příklad 1: Konfigurace statické prahové hodnoty upozornění na latenci

Jako název signálu vyberte **možnost Celková latence rozhraní API služby.**
> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s portálem Azure](../media/alert-14.png)

Podívejte se na následující parametry konfigurace.

+ Nastavení prahové hodnoty na **statickou hodnotu** 
+ Nastavte operátor na **větší než**
+ Nastavit typ agregace na **průměr**
+ Nastavení prahové hodnoty na **500**
+ Nastavit období agregace na **5 minut**
+ Nastavte frekvenci hodnocení na **1 minutu**
+ Vybrat **Hotovo**  

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s portálem Azure](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>Příklad 2: Konfigurace dynamické prahové hodnoty upozornění pro sytost trezoru 

Při použití dynamické výstrahy budete moci zobrazit historická data vybraného trezoru klíčů. Modrá oblast představuje průměrné využití trezoru klíčů. Červená oblast zobrazuje špičky, které by spustily výstrahu za předpokladu, že jsou splněna další kritéria v konfiguraci výstrahy. Červené tečky zobrazují případy porušení, kde byla splněna kritéria pro výstrahu během agregovaného časového období. Po určitém počtu porušení můžete nastavit výstrahu, která se po určitém počtu porušení v nastaveném čase vypálí. Pokud nechcete zahrnout minulá data, existuje možnost vyloučit stará data níže v pokročilých nastaveních. 

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s portálem Azure](../media/alert-16.png)

Podívejte se na následující parametry konfigurace.

+ Nastavení prahové hodnoty na **dynamickou** 
+ Nastavte operátor na **větší než**
+ Nastavit typ agregace na **průměr**
+ Nastavení prahové citlivosti na **střední**
+ Nastavit období agregace na **5 minut**
+ Nastavte frekvenci hodnocení na **1 minutu**
+ **Nepovinné** Konfigurace upřesňujících nastavení 
+ Vybrat **Hotovo**

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s portálem Azure](../media/alert-17.png)

5. Přidání skupiny akcí, kterou jste nakonfigurovali

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s portálem Azure](../media/alert-18.png)

6. Povolení výstrahy a přiřazení závažnosti

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s portálem Azure](../media/alert-19.png)

7. Vytvoření výstrahy 


## <a name="next-steps"></a>Další kroky

Gratulujeme, nyní jste úspěšně vytvořili monitorovací řídicí panel a nakonfigurovali upozornění pro trezor klíčů! Jakmile provedete všechny výše uvedené kroky, měli byste dostávat e-mailová upozornění, když trezor klíčů splňuje kritéria výstrah, která jste nakonfigurovali. Příklad najdete níže. Pomocí nástrojů, které jste nastavili v tomto článku, můžete aktivně sledovat stav trezoru klíčů. 

### <a name="example-email-alert"></a>Ukázková e-mailová výstraha 

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s portálem Azure](../media/alert-20.png)

---
title: Azure Key Vault monitorování a upozorňování | Microsoft Docs
description: Vytvořte řídicí panel, abyste mohli monitorovat stav vašeho trezoru klíčů a konfigurovat výstrahy.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/06/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to learn the options available to monitor the health of my vaults
ms.openlocfilehash: 7ff3daded48612fd308396420dca65cd81d9fafc
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724693"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Monitorování a upozorňování na Azure Key Vault

## <a name="overview"></a>Přehled

Jakmile začnete používat Trezor klíčů k ukládání provozních tajných kódů, je důležité monitorovat stav trezoru klíčů, abyste se ujistili, že vaše služba funguje tak, jak má. Po zahájení škálování služby se zvýší počet požadavků odeslaných do trezoru klíčů. To má potenciál na zvýšení latence vašich požadavků a v extrémních případech způsobit omezení požadavků, což ovlivní výkon vaší služby. Musíte být také upozorněni, pokud váš Trezor klíčů odesílá neobvyklý počet chybových kódů, takže můžete být rychle upozorněni na všechny zásady přístupu nebo problémy s konfigurací brány firewall. Tento dokument se zabývá následujícími tématy:

+ Základní metriky Key Vault pro monitorování
+ Jak nakonfigurovat metriky a vytvořit řídicí panel 
+ Postup při vytváření výstrah v zadaných prahových hodnotách 

## <a name="basic-key-vault-metrics-to-monitor"></a>Základní metriky Key Vault pro monitorování

+ Dostupnost trezoru  
+ Sytost trezoru 
+ Latence rozhraní API služby 
+ Celkový počet přístupů k rozhraní API služby (filtr podle typu aktivity) 
+ Kódy chyb (filtrovat podle stavového kódu) 

**Dostupnost trezoru** – Tato metrika by měla vždy být 100%. Jedná se o důležitou metriku pro monitorování, protože vám může rychle Ukázat, jestli v trezoru klíčů došlo k výpadku. 

**Sytost trezoru** – počet požadavků za sekundu, které může Trezor klíčů poskytovat, je založený na typu prováděné operace. Některé operace trezoru mají nižší prahovou hodnotu požadavků za sekundu. Tato metrika agreguje celkové využití trezoru klíčů napříč všemi typy operací, aby se nacházela s procentuální hodnotou, která označuje vaše aktuální využití trezoru klíčů. Úplný seznam omezení služby trezoru klíčů najdete v následujícím dokumentu. [Omezení služby Azure Key Vault](service-limits.md)

**Latence rozhraní API služby** – Tato metrika ukazuje průměrnou latenci volání do trezoru klíčů, měřená ve službě. Nezahrnuje čas spotřebovaný klientem ani sítí mezi klientem a službou.

**Celkový počet přístupů k rozhraní API** – Tato metrika zobrazuje všechna volání provedená v trezoru klíčů. To vám pomůže určit, které aplikace zavolají váš Trezor klíčů. 

**Kódy chyb** – Tato metrika vám ukáže, jestli v trezoru klíčů dochází k neobvyklému množství chyb. Úplný seznam kódů chyb a pokyny k odstraňování potíží najdete v následujícím dokumentu. [Kódy chyb Azure Key Vault REST API](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>Jak nakonfigurovat metriky a vytvořit řídicí panel

1. Přihlaste se k webu Azure Portal.
2. Přejděte na Key Vault
3. V části **monitorování** vyberte **metriky** . 

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky, který zvýrazní možnost metriky v části monitorování.](../media/alert-1.png)

4. Aktualizujte název grafu na to, co chcete zobrazit na řídicím panelu. 
5. Vyberte obor. V tomto příkladu vybereme jeden Trezor klíčů. 
6. Vyberte metriku **celkové dostupnosti trezoru** a **průměrnou** agregaci. 
7. Aktualizuje časový rozsah za posledních 24 hodin a aktualizuje časové rozlišení na 1 minutu. 

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky zobrazující celkovou metriku dostupnosti úložiště](../media/alert-2.png)

8. Opakujte výše uvedené kroky pro metriky latence trezoru a latence rozhraní API služby. Vyberte **Připnout na řídicí panel** a uložte metriky do řídicího panelu. 

> [!IMPORTANT]
> Vyberte Připnout na řídicí panel a uložte všechny metriky, které nakonfigurujete. Pokud ponecháte stránku a vrátíte se do ní bez uložení, změny konfigurace se ztratí. 

9. Pokud chcete monitorovat všechny typy operací v trezoru klíčů, použijte metriku **Celková hodnota rozhraní API služby** a vyberte **použít rozdělení podle typu aktivity** .

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky, který ukazuje tlačítko použít rozdělení](../media/alert-3.png)

10. Pokud chcete monitorovat chybové kódy v trezoru klíčů, použijte metriku **Total Service API Results** a vyberte **použít rozdělení podle typu aktivity** .

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky zobrazující vybranou celkovou metriku výsledků rozhraní API služby](../media/alert-4.png)

Teď budete mít řídicí panel, který bude vypadat nějak takto. Můžete kliknout na 3 tečky v pravém horním rohu každé dlaždice a můžete změnit uspořádání a velikost dlaždic tak, jak potřebujete. 

Po uložení a publikování řídicího panelu se ve vašem předplatném Azure vytvoří nový prostředek. V takovém případě ho budete moci kdykoli zobrazit hledáním "sdílený řídicí panel". 

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky zobrazující publikovaný řídicí panel](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Postup konfigurace výstrah v Key Vault 

V této části se dozvíte, jak nakonfigurovat výstrahy pro váš Trezor klíčů, abyste mohli upozornit svého týmu, aby okamžitě učinil akci, pokud je váš Trezor klíčů ve stavu není v pořádku. Můžete nakonfigurovat výstrahy, které odesílají e-maily, přednostně pro tým DL, aktivují oznámení služby Event Grid, nebo můžou zavolat nebo volat text na telefonní číslo. Můžete také zvolit statické výstrahy na základě pevné hodnoty nebo dynamické výstrahy, která vás upozorní, pokud monitorovaná metrika překročí průměrný limit vašeho trezoru klíčů v rámci definovaného časového rozsahu. 

> [!IMPORTANT]
> Poznámka: u nově nakonfigurovaných výstrah může trvat až 10 minut, než se začnou odesílat oznámení. 

### <a name="configure-an-action-group"></a>Konfigurace skupiny akcí 

Skupina akcí je konfigurovatelný seznam oznámení a vlastností.

1. Přihlaste se k webu Azure Portal.
2. Vyhledat **výstrahy** ve vyhledávacím poli
3. Výběr možnosti **Spravovat akce**

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky, který zvýrazní tlačítko Spravovat akce](../media/alert-6.png)

4. Vybrat **+ Přidat skupinu akcí**

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky, který zvýrazní tlačítko + Přidat skupinu akcí](../media/alert-7.png)

5. Vyberte **typ akce** pro vaši skupinu akcí. V tomto příkladu vytvoříme e-mailové upozornění.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky, který zvýrazní pole potřebná pro přidání skupiny akcí.](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky, který ukazuje, co je potřeba k přidání e-mailu nebo upozornění na zprávu SMS.](../media/alert-9.png)

6. Klikněte na **OK** v dolní části stránky. Úspěšně jste vytvořili skupinu akcí. 

Teď, když jste nakonfigurovali skupinu akcí, nakonfigurujeme prahové hodnoty výstrah trezoru klíčů. 

### <a name="configure-alert-thresholds"></a>Konfigurace prahových hodnot výstrah 

1. Vyberte prostředek trezoru klíčů v Azure Portal a v části **monitorování** vyberte **výstrahy** .

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky, který zobrazuje možnost nabídky výstrahy v části monitorování.](../media/alert-10.png)

2. Vybrat **nové pravidlo výstrahy**

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky, který zobrazuje tlačítko + nové pravidlo výstrahy.](../media/alert-11.png)

3. Vyberte obor pravidla výstrahy. Můžete vybrat jeden nebo více trezorů. 

> [!IMPORTANT]
> Počítejte s tím, že při výběru více trezorů pro rozsah výstrah musí být všechny vybrané trezory ve stejné oblasti. Budete muset nakonfigurovat samostatná pravidla upozornění pro trezory v různých oblastech. 

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky, který ukazuje, jak můžete vybrat trezor.](../media/alert-12.png)

4. Vyberte podmínky pro vaše výstrahy. Můžete zvolit některý z následujících signálů a definovat logiku pro upozorňování. Key Vault tým doporučuje konfigurovat následující prahové hodnoty pro výstrahy. 

    + Key Vault dostupnosti klesne pod 100% (statická prahová hodnota).
    + Key Vault latence je větší než 500 ms (statická prahová hodnota). 
    + Celkové sytosti trezoru jsou větší než 75% (statická prahová hodnota). 
    + Celková sytost trezoru překračuje průměr (dynamickou prahovou hodnotu).
    + Celkový počet chybových kódů vyšších než průměr (dynamická prahová hodnota) 

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky, který ukazuje, kde si vybíráte podmínky pro výstrahy.](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>Příklad 1: Konfigurace prahové hodnoty statické výstrahy pro latenci

Vybrat **celkovou latenci rozhraní API služby** jako název signálu
> [!div class="mx-imgBorder"]
> ![Snímek obrazovky, který zobrazuje celkový název signálu pro latenci rozhraní API služby.](../media/alert-14.png)

Podívejte se prosím na následující parametry konfigurace.

+ Nastavit prahovou hodnotu na **static** 
+ Nastavte operátor na **větší než**
+ Nastavte typ agregace na **průměr** .
+ Nastavte mezní hodnotu na **500** .
+ Nastavit období agregace na **5 minut**
+ Nastavte četnost vyhodnocování na **1 minutu** .
+ Vyberte **Hotovo** .  

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky, který zvýrazní nakonfigurovanou logiku výstrahy.](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>Příklad 2: Konfigurace prahové hodnoty dynamické výstrahy pro sytost trezoru 

Při použití dynamické výstrahy budete moci zobrazit historická data trezoru klíčů, který jste vybrali. Modrá oblast představuje průměrné využití vašeho trezoru klíčů. Červená oblast zobrazuje špičky, které by aktivovaly výstrahu, která měla za následek splnění dalších kritérií v konfiguraci výstrah. Červené tečky znázorňují instance porušení, kde byla kritéria výstrahy splněna během agregovaného časového okna. Můžete nastavit výstrahu, která se aktivuje po určitém počtu porušení v rámci nastaveného času. Pokud nechcete zahrnout minulá data, máte možnost vyloučit stará data níže v části Rozšířená nastavení. 

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky, který zobrazuje graf celkového sytosti trezoru.](../media/alert-16.png)

Podívejte se prosím na následující parametry konfigurace.

+ Nastaví prahovou hodnotu na **dynamickou** . 
+ Nastavte operátor na **větší než**
+ Nastavte typ agregace na **průměr** .
+ Nastavení citlivosti prahové hodnoty na **hodnotu Střední**
+ Nastavit období agregace na **5 minut**
+ Nastavte četnost vyhodnocování na **1 minutu** .
+ **Volitelné** Konfigurace upřesňujících nastavení 
+ Vyberte **Hotovo** .

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s Azure Portal](../media/alert-17.png)

5. Přidat skupinu akcí, kterou jste nakonfigurovali

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky, který ukazuje, jak přidat skupinu akcí.](../media/alert-18.png)

6. Povolení výstrahy a přiřazení závažnosti

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky, který ukazuje, kde má být výstraha povolena a zda má být přiřazena závažnost.](../media/alert-19.png)

7. Vytvoření výstrahy 


## <a name="next-steps"></a>Další kroky

Gratulujeme, právě jste úspěšně vytvořili řídicí panel monitorování a nakonfigurovali výstrahy pro váš Trezor klíčů. Jakmile budete dodržovat všechny výše uvedené kroky, měli byste obdržet e-mailová upozornění, když váš Trezor klíčů splňuje kritéria výstrahy, která jste nakonfigurovali. Příklad najdete níže. Pomocí nástrojů, které jste nastavili v tomto článku, můžete aktivně monitorovat stav vašeho trezoru klíčů. 

### <a name="example-email-alert"></a>Příklad e-mailové výstrahy 

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky, který zvýrazňuje informace potřebné ke konfiguraci e-mailového upozornění.](../media/alert-20.png)

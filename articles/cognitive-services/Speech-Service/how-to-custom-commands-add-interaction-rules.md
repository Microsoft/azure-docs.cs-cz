---
title: 'Postupy: Přidání potvrzení k vlastnímu příkazu'
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak implementovat potvrzení pro příkaz ve vlastních příkazech.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 5f66e29e4c1bc85981202251e0de8288f4baee4e
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307595"
---
# <a name="add-interaction-rules"></a>Přidat pravidla interakce

V tomto článku se dozvíte o **pravidlech interakce**. Toto jsou další pravidla pro zpracování složitějších nebo složitých situací. I když nemůžete vytvářet vlastní pravidla interakce, můžete v tomto článku použít pravidla interakce v následujících cílových scénářích:

* Potvrzení příkazů
* Přidání opravy s jedním krokem do příkazů

V části [odkazy](./custom-commands-references.md) najdete další informace o pravidlech interakce.

## <a name="prerequisites"></a>Požadavky

Je nutné provést kroky v následujících článcích:
> [!div class="checklist"]
> * [Postupy: vytváření aplikací pomocí jednoduchých příkazů](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Postupy: Přidání parametrů k příkazům](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="add-confirmations-to-a-command"></a>Přidání potvrzení do příkazu

Chcete-li přidat potvrzení, použijte příkaz **SetTemperature** . Pro dosažení potvrzení vytvoříte pravidla interakce pomocí následujících kroků.

1. V levém podokně vyberte příkaz **SetTemperature** .
2. Přidejte pravidla interakce výběrem možnosti **Přidat** v prostředním podokně a následným výběrem příkazu **pravidla interakce**  >  **Potvrdit**.

    Tím se přidají 3 pravidla interakce, které toto pravidlo vyzve uživatele k potvrzení data a času alarmu a při dalším zapnutí očekává potvrzení (ano/ne).

    1. Změňte pravidlo interakce **příkazu potvrdit** podle následující konfigurace.
        1. Přejmenujte **název** na **`Confirm Temperature`** .
        1. Přidat novou podmínku jako **požadované parametry > teploty**
        1. Přidat novou akci jako **typ > odeslat odpověď na řeč > `Are you sure you want to set the temperature as {Temperature} degrees?` **
        1. V části očekávání ponechte výchozí hodnotu **očekávaného potvrzení od uživatele** .
      
         > [!div class="mx-imgBorder"]
         > ![Vytvořit požadovanou odpověď parametru](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Upravte pravidlo interakce **úspěšného potvrzení** , aby se zpracovalo úspěšné potvrzení (uživatel uvedli Ano).
      
          1. Změňte **název** na **`Confirmation temperature succeeded`** .
          1. Nechejte již existující **potvrzení o úspěšném** dokončení.
          1. Přidat novou podmínku jako **typ > požadované parametry > teploty**
          1. Ponechte výchozí hodnotu **stav po spuštění** jako **pravidla dokončení provedení**.

    1. Pokud je potvrzení zamítnuté, upravte **potvrzení zamítnuté** (uživatel), který bude zpracovávat scénáře.

          1. Změňte **název** na **`Confirmation temperature denied`** .
          1. Ponechejte podmínku pro již existující **potvrzení bylo odepřeno** .
          1. Přidat novou podmínku jako **typ > požadované parametry > teploty**
          1. Přidat novou akci jako **typ > odeslat odpověď na řeč > `No problem. What temperature then?` **
          1. Ponechte výchozí hodnotu **stavu po spuštění** jako **čekání na vstup uživatele**.

> [!IMPORTANT]
> V tomto článku jste použili funkci přibudované potvrzení. Alternativně můžete také dosáhnout stejného přidáním pravidel interakce jednu po jedné, ručně.
   

### <a name="try-out-the-changes"></a>Vyzkoušejte změny

Vyberte **vlak**, počkejte na dokončení školení a vyberte **test**.

- Vstup: Nastavte teplotu na 80 stupňů.
- Výstup: OK 80?
- Vstup: ne
- Výstup: žádný problém. jakou teplotu pak?
- Vstup: 83 stupňů
- Výstup: OK 83?
- Vstup: Ano
- Výstup: OK, nastavení teploty na 83 stupňů


## <a name="implementing-corrections-in-a-command"></a>Implementace oprav v příkazu

V této části nakonfigurujete jednorázovou opravu, která se používá poté, co již byla provedena akce plnění. V případě, že se ještě příkaz nesplní, uvidíte také příklad, jak je oprava povolená ve výchozím nastavení. Pokud chcete přidat opravu, když se příkaz nedokončí, přidejte nový parametr **AlarmTone**.

V levém podokně vyberte příkaz **SetAlarm** a přidejte nový parametr **AlarmTone**.
        
- **Jméno** > `AlarmTone`
- **Typ** > řetězec
- **Výchozí hodnota** > `Chimes`
- **Konfigurace** > přijměte předdefinované vstupní hodnoty z interního katalogu.
- **Předdefinované vstupní hodnoty**  >  `Chimes` , `Jingle` a `Echo` . Každé jako jednotlivé předdefinované vstupy.


Dále aktualizujte odpověď pro parametr DateTime na `Ready to set alarm with tone as {AlarmTone}. For what time?` . Pak upravte pravidlo dokončení následujícím způsobem.

1. Vyberte stávající pravidlo pro dokončení **ConfirmationResponse**.
1. V pravém panelu najeďte myší na stávající akci a vyberte tlačítko **Upravit** .
1. Aktualizovat reakci na řeč na`Ok, alarm set for {DateTime}. The alarm tone is {AlarmTone}.`

### <a name="try-out-the-changes"></a>Vyzkoušejte změny

Vyberte **výuka**, počkejte na dokončení školení a vyberte **test**.
Vyzkoušejte následující projevy:

- Vstup: Nastavení alarmu
- Výstup: připraveno k nastavení alarmu pomocí tónové volby jako Chimes. Pro jakou dobu?
- Vstup: nastavte alarm s tónovou hodnotu Jingle pro 9 – zítra
- Výstup: OK, sada alarmů pro 2020-05-30 09:00:00. Výstražný tón je Jingle.

> [!IMPORTANT]
> Všimněte si, jak se dá změnit tón alarmu bez explicitní konfigurace v probíhajícím příkazu, tj. když se příkaz ještě nedokončil. **Oprava je ve výchozím nastavení povolena pro všechny parametry příkazu bez ohledu na to, jestli je příkaz ještě vyplněný.**

### <a name="correction-when-command-is-completed"></a>Oprava po dokončení příkazu

Vlastní platforma příkazy také nabízí možnost opravy v rámci jednoho kroku i v případě, že byl příkaz dokončen. Tato funkce není ve výchozím nastavení povolená a je nutné ji explicitně nakonfigurovat. Pomocí následujícího postupu můžete nakonfigurovat opravy v jednom kroku.

1. V příkazu **SetAlarm** přidejte pravidlo interakce typu **příkaz aktualizovat předchozí** a aktualizujte dříve nastavené alarmy. Chcete-li **aktualizovat předchozí alarm**, přejmenujte výchozí **název** pravidla interakce.
1. Nechte výchozí podmínku **předchozí příkaz se musí aktualizovat** tak, jak je.
1.  Přidat novou podmínku jako **typ > požadovaný parametr > data a času**.
1. Přidat novou akci jako **typ > odeslat odpověď řeči > jednoduchý editor > `Updating previous alarm time to {DateTime}.` **
1. Ponechte výchozí hodnotu stav po spuštění jako **příkaz dokončeno**.

### <a name="try-out-the-changes"></a>Vyzkoušejte změny

Vyberte **vlak**, počkejte na dokončení školení a vyberte **test**.

- Vstup: Nastavení alarmu
- Výstup: připraveno k nastavení alarmu pomocí tónové volby jako Chimes. Kolik je hodin?
- Vstup: nastavte alarm s tónovou hodnotu Jingle pro 9 – zítra
- Výstup: OK, sada alarmů pro 2020-05-21 09:00:00. Výstražný tón je Jingle.
- Vstup: Ne, 8 dop.
- Výstup: aktualizace předchozí doby alarmu na 2020-05-29 08:00.

> [!NOTE]
> V reálné aplikaci budete také muset v části akce tohoto pravidla pro opravu odeslat zpět aktivitu klientovi nebo zavolat koncový bod HTTP, který aktualizuje čas alarmu v systému. Tato akce by měla být vždy zodpovědná za to, že se právě aktualizuje čas alarmu, a ne jakýkoli jiný atribut příkazu, a to v tomto případě výstražný tón.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Postupy: Přidání šablon generování jazyka pro odezvy řeči](./how-to-custom-commands-add-language-generation-templates.md)
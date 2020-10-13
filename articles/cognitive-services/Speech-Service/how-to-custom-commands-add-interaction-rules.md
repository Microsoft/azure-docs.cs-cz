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
ms.openlocfilehash: 7d6c0928196c9e8e1abf6aa7f724a58753ce3d2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87289049"
---
# <a name="add-interaction-rules"></a>Přidání pravidel interakce

V tomto článku se dozvíte o **pravidlech interakce**. Toto jsou další pravidla pro zpracování složitějších nebo složitých situací. I když nemůžete vytvářet vlastní pravidla interakce, můžete v tomto článku použít pravidla interakce v následujících cílových scénářích:

* Potvrzení příkazů
* Přidání opravy s jedním krokem do příkazů

Další informace o pravidlech interakce najdete v části [odkazy](./custom-commands-references.md) .

## <a name="prerequisites"></a>Požadavky

Musíte dokončit kroky v následujících článcích:
> [!div class="checklist"]
> * [Postupy: vytváření aplikací pomocí jednoduchých příkazů](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Postupy: Přidání parametrů k příkazům](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="add-confirmations-to-a-command"></a>Přidání potvrzení do příkazu

Chcete-li přidat potvrzení, použijte příkaz **SetTemperature** . Chcete-li dosáhnout potvrzení, vytvořte pravidla interakce pomocí následujících kroků.

1. V levém podokně vyberte příkaz **SetTemperature** .
1. Přidejte pravidla interakce výběrem možnosti **Přidat** v prostředním podokně. Pak vyberte příkaz **pravidla interakce**  >  **Potvrdit**.

    Tato akce přidá tři pravidla interakce, která uživateli požádá o potvrzení data a času alarmu a očekává u dalšího tahu potvrzení (ano/ne).

    1. Upravte pravidlo interakce **příkazu potvrdit** jako pro následující konfiguraci:
        1. Pro **potvrzení teploty**změňte **název** .
        1. Přidejte novou podmínku jako **požadovanou**  >  **teplotu**parametrů.
        1. Přidat novou akci jako **typ**  >  **Odeslat odpověď na řeč**opravdu  >  **chcete nastavit teplotu jako {0} stupňů?**
        1. V části **očekávání** ponechte výchozí hodnotu **očekávaného potvrzení od uživatele** .
      
         > [!div class="mx-imgBorder"]
         > ![Vytvořit požadovanou odpověď parametru](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Upravte pravidlo interakce **úspěšného potvrzení** , aby se zpracovalo úspěšné potvrzení (uživatel uvedli Ano).
      
          1. Úprava **názvu** na **potvrzenou teplotu byla úspěšná**.
          1. Nechejte již existující **potvrzení o úspěšném** dokončení.
          1. Přidejte novou podmínku jako **typ**  >  **teplotní parametry Required**  >  **Temperature**.
          1. Ponechte výchozí hodnotu **stav po spuštění** jako **pravidla dokončení provedení**.

    1. Upravte pravidlo interakce **potvrzení zamítnutí** pro zpracování scénářů, pokud je potvrzení zamítnuto (uživatel neuvedli).

          1. Změna **názvu** na **potvrzovací teplotu byla zamítnuta**.
          1. Ponechejte podmínku pro již existující **potvrzení bylo odepřeno** .
          1. Přidejte novou podmínku jako **typ**  >  **teplotní parametry Required**  >  **Temperature**.
          1. Přidejte novou akci jako **typ**  >  **Odeslat odezvu na řeč**  >  **bez problému. Jakou teplotu pak?**
          1. Ponechte výchozí hodnotu **stavu po spuštění** jako **čekání na vstup uživatele**.

> [!IMPORTANT]
> V tomto článku jste použili vestavěnou možnost potvrzení. Můžete také ručně přidat pravidla interakce jednu po jedné.
   

### <a name="try-out-the-changes"></a>Vyzkoušejte změny

Vyberte **výuka**, počkejte na dokončení školení a vyberte **test**.

- **Vstup**: Nastavte teplotu na 80 stupňů.
- **Výstup**: jste si jisti, že chcete nastavit teplotu jako 80 stupňů?
- **Vstup**: ne
- **Výstup**: žádný problém. Jakou teplotu pak?
- **Vstup**: 72 stupňů
- **Výstup**: jste si jisti, že chcete nastavit teplotu jako 72 stupňů?
- **Vstup**: Ano
- **Výstup**: OK, nastavení teploty na 83 stupňů


## <a name="implement-corrections-in-a-command"></a>Implementace oprav v příkazu

V této části nakonfigurujete jednorázovou opravu, která se používá poté, co již byla provedena akce plnění. V případě, že se ještě příkaz nesplní, uvidíte také příklad, jak je oprava povolená ve výchozím nastavení. Pokud chcete přidat opravu, když se příkaz nedokončí, přidejte nový parametr **AlarmTone**.

V levém podokně vyberte příkaz **SetAlarm** a přidejte nový parametr **AlarmTone**.
        
- **Název**  >  **AlarmTone**
- **Typ**  >  **Řetězec**
- **Výchozí hodnota**  >  **CHIMES**
- **Konfigurace**  >  **Přijměte předdefinované vstupní hodnoty z interního katalogu** .
- **Předdefinované vstupní hodnoty**  >  **CHIMES**, **Jingle**a **echo** jako jednotlivé předdefinované vstupy


Dále aktualizujte odpověď pro parametr **DateTime** na **připraveno k nastavení alarmu pomocí tónové hodnoty jako {AlarmTone}. Pro jakou dobu?**. Pak upravte pravidlo dokončení následujícím způsobem:

1. Vyberte stávající pravidlo pro dokončení **ConfirmationResponse**.
1. V pravém podokně umístěte ukazatel myši na stávající akci a vyberte **Upravit**.
1. Aktualizujte reakci na řeč na **OK a nastavte alarm pro {DateTime}. Výstražný tón je {AlarmTone}.**

### <a name="try-out-the-changes"></a>Vyzkoušejte změny

Vyberte **výuka**, počkejte na dokončení školení a vyberte **test**.
Vyzkoušejte následující projevy:

- **Vstup**: nastavte alarm.
- **Výstup**: připraveno k nastavení alarmu pomocí tónové volby jako Chimes. Pro jakou dobu?
- **Vstup**: nastavte alarm se zvukem jako Jingle pro 9 – zítra.
- **Výstup**: OK, sada alarmů pro 2020-05-30 09:00:00. Výstražný tón je Jingle.

> [!IMPORTANT]
> Výstražný tón lze změnit bez explicitní konfigurace v probíhajícím příkazu, například v případě, že příkaz ještě nebyl dokončen. *Oprava je ve výchozím nastavení povolená pro všechny parametry příkazu bez ohledu na to, jestli je příkaz ještě vyplněný.*

### <a name="correction-when-command-is-completed"></a>Oprava po dokončení příkazu

Vlastní platforma příkazy také nabízí možnost opravy v rámci jednoho kroku i v případě, že byl příkaz dokončen. Tato funkce není ve výchozím nastavení povolená. Musí být explicitně nakonfigurovaný. Pomocí následujícího postupu můžete nakonfigurovat opravu s jedním krokem.

1. V příkazu **SetAlarm** přidejte do příkazového řádku pravidlo interakce s **příkazem aktualizovat předchozí** a aktualizujte dříve nastavené alarmy. Chcete-li **aktualizovat předchozí alarm**, přejmenujte výchozí **název** pravidla interakce.
1. Nechte výchozí podmínku **předchozí příkaz se musí aktualizovat** tak, jak je.
1. Přidejte novou podmínku jako **typ**  >  **parametru Required**  >  **DateTime**.
1. Přidejte novou akci jako **typ**  >  **Odeslat**.  >  **jednoduchý editor**odpovědi  >  **na řeč aktualizuje předchozí čas alarmu na {DateTime}.**
1. Ponechte výchozí hodnotu **stav po spuštění** jako **příkaz dokončeno**.

### <a name="try-out-the-changes"></a>Vyzkoušejte změny

Vyberte **výuka**, počkejte na dokončení školení a vyberte **test**.

- **Vstup**: nastavte alarm.
- **Výstup**: připraveno k nastavení alarmu pomocí tónové volby jako Chimes. Pro jakou dobu?
- **Vstup**: nastavte alarm se zvukem jako Jingle pro 9 – zítra.
- **Výstup**: OK, sada alarmů pro 2020-05-21 09:00:00. Výstražný tón je Jingle.
- **Vstup**: Ne, 8 dop.
- **Výstup**: aktualizace předchozí doby alarmu na 2020-05-29 08:00.

> [!NOTE]
> V reálné aplikaci budete také muset v části **Akce** tohoto pravidla pro opravu odeslat zpět aktivitu klientovi nebo zavolat koncový bod HTTP, který aktualizuje čas alarmu v systému. Tato akce by měla být výhradně odpovědná za aktualizaci doby alarmu a nikoli jakýchkoli dalších atributů příkazu. V tomto případě by to byl výstražný tón.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Postupy: Přidání šablon generování jazyka pro odezvy řeči](./how-to-custom-commands-add-language-generation-templates.md)

---
title: Správa šablon šířky pásma pro řady StorSimple 8000 | Microsoft Docs
description: Popisuje, jak spravovat šablony StorSimple šířky pásma, které umožňují řídit spotřebu šířky pásma.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 56170ffbbfe14248bcfd1f94549a3565873f8646
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005890"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-bandwidth-templates"></a>Správa šablon šířky pásma StorSimple pomocí služby StorSimple Správce zařízení

## <a name="overview"></a>Přehled

Šablony šířky pásma umožňují nakonfigurovat využití šířky pásma sítě napříč více časovými plány, aby se data na úrovni zařízení StorSimple do cloudu.

Plány omezení šířky pásma vám umožní:

* Zadejte vlastní plány šířky pásma v závislosti na využití sítě úloh.
* Snadný a hladce usnadňuje správu a opakované použití plánů napříč různými zařízeními.

> [!NOTE]
> Tato funkce je dostupná jenom pro fyzická zařízení StorSimple (modely 8100 a 8600), ne pro StorSimple cloudová zařízení (modely 8010 a 8020).


## <a name="the-bandwidth-templates-blade"></a>Okno šablony šířky pásma

Okno **šablony šířky** pásma má všechny šablony šířky pásma pro vaši službu v tabulkovém formátu a obsahuje následující informace:

* **Název** – jedinečný název přiřazený šabloně šířky pásma při jeho vytvoření.
* **Schedule** – počet plánů obsažených v dané šabloně šířky pásma.
* **Používá** se – počet svazků, které používají šablony šířky pásma.

Můžete také najít další informace, které vám pomůžou nakonfigurovat šablony šířky pásma v nástroji:

* [Otázky a odpovědi týkající se šablon šířky pásma](#questions-and-answers-about-bandwidth-templates)
* [Osvědčené postupy pro šablony šířky pásma](#best-practices-for-bandwidth-templates)

## <a name="add-a-bandwidth-template"></a>Přidat šablonu šířky pásma

Chcete-li vytvořit novou šablonu šířky pásma, proveďte následující kroky.

#### <a name="to-add-a-bandwidth-template"></a>Přidání šablony šířky pásma

1. Přejděte ke službě StorSimple Správce zařízení, klikněte na **šablony šířky pásma** a potom klikněte na **+ Přidat šablonu šířky pásma**.

    ![Klikněte na + Přidat šablonu šířky pásma.](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp1.png)

2. V okně **Přidat šablonu šířky pásma** proveďte následující kroky:
   
    1. Zadejte jedinečný název pro šablonu šířky pásma.
    2. Definujte plán šířky pásma. Postup vytvoření plánu:
   
        1. V rozevíracím seznamu vyberte **dny** v týdnu, pro které je plán nakonfigurován. Můžete vybrat více dní.        
        
        2. Zadejte **počáteční čas** ve formátu _HH: mm_ . Tím se spustí plán.

        3. Zadejte **čas ukončení** ve formátu _HH: mm_ . To se stane, když se plán zastaví.
      
           > [!NOTE]
           > Překrývající se plány nejsou povolené. Pokud časy zahájení a ukončení budou mít za následek překrývající se plán, zobrazí se mu chybová zpráva.

        4. Zadejte **rychlost šířky pásma**. Jedná se o šířku pásma v megabajtech za sekundu (MB/s), kterou zařízení StorSimple používá v operacích, které se týkají cloudu (odesílání a stahování). Do tohoto pole zadejte číslo od 1 do 1 000.

            ![Definovat plán šířky pásma](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp2.png)
         
            Opakujte výše uvedené kroky a definujte více plánů pro šablonu, dokud nebudete hotovi.

        5. Klikněte na tlačítko **Přidat** a začněte vytvářet šablonu šířky pásma. Vytvořená šablona se přidá do seznamu šablon šířky pásma.
      

## <a name="edit-a-bandwidth-template"></a>Upravit šablonu šířky pásma

Chcete-li upravit šablonu šířky pásma, proveďte následující kroky.

### <a name="to-edit-a-bandwidth-template"></a>Úprava šablony šířky pásma

1. Přejděte do služby StorSimple Správce zařízení a klikněte na **šablony šířky pásma**.
2. V seznamu šablon šířky pásma vyberte šablonu, kterou chcete odstranit. Klikněte pravým tlačítkem myši a v místní nabídce vyberte **Odstranit**.
3. Po zobrazení výzvy k potvrzení klikněte na tlačítko **OK**. Tato šablona by měla odstranit šablonu šířky pásma. 
4. Seznam šablon šířky pásma se aktualizuje tak, aby odrážel odstranění.

> [!NOTE]
> Změny se nedají uložit, pokud se upravený plán překrývá s existujícím plánem v šabloně šířky pásma, kterou upravujete.

## <a name="delete-a-bandwidth-template"></a>Odstranění šablony šířky pásma

K odstranění šablony šířky pásma proveďte následující kroky.

#### <a name="to-delete-a-bandwidth-template"></a>Odstranění šablony šířky pásma

1. Přejděte do služby StorSimple Správce zařízení a klikněte na **šablony šířky pásma**.
2. V seznamu šablon šířky pásma vyberte šablonu, kterou chcete odstranit. Klikněte pravým tlačítkem myši a v místní nabídce vyberte Odstranit.
3. Po zobrazení výzvy k potvrzení klikněte na tlačítko **OK**. Tato šablona by měla odstranit šablonu šířky pásma.
4. Seznam šablon šířky pásma se aktualizuje tak, aby odrážel odstranění.

Pokud je šablona používána všemi svazky, nebudete ji moci odstranit. Zobrazí se chybová zpráva s oznámením, že se šablona používá. Zobrazí se dialogové okno chybová zpráva s oznámením, že všechny odkazy na šablonu by měly být odebrány.

Všechny odkazy na šablonu můžete odstranit tak, že na ni přistupujete na stránce **kontejnery svazků** a upravíte kontejnery svazků, které tuto šablonu používají, aby používaly jinou šablonu, nebo použijte nastavení vlastní nebo neomezená šířka pásma. Po odebrání všech odkazů můžete šablonu odstranit.

## <a name="use-a-default-bandwidth-template"></a>Použít výchozí šablonu šířky pásma

K dispozici je výchozí šablona šířky pásma, která se ve výchozím nastavení používá v kontejnerech svazků, aby při přístupu ke cloudu vynutila řízení šířky pásma. Výchozí šablona slouží také jako připravený odkaz pro uživatele, kteří vytvářejí vlastní šablony. Podrobnosti o této výchozí šabloně:

* **Název** – neomezená nocí a víkendy
* **Plán** – jeden plán od pondělí do pátku, který aplikuje míru šířky pásma 1 MB/s v rozmezí od 8 dop. a 5./odp. času zařízení. Šířka pásma je pro zbytek týdne nastavená na neomezenou dobu.

Výchozí šablonu lze upravit. Použití této šablony (včetně upravených verzí) je sledováno.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Vytvoří celodenní šablonu šířky pásma, která začne v zadaném čase.

Pomocí tohoto postupu můžete vytvořit plán, který se spustí v zadanou dobu a spustí se každý den. V tomto příkladu se plán začne od ráno ráno a bude běžet až do 9. příští ráno. Je důležité si uvědomit, že časy zahájení a ukončení pro daný plán musí být obsaženy ve stejném 24hodinovém plánu a nesmí rozbírat více dní. Pokud potřebujete nastavit šablony šířky pásma, které přesahují více dní, budete muset použít více plánů (jak je znázorněno v příkladu).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Vytvoření šablony celodenní šířky pásma

1. Vytvořte plán, který začíná v 9:00 a bude běžet až do půlnoci.
2. Přidejte další plán. Nakonfigurujte druhý plán tak, aby běžel od půlnoci až do 9 ráno.
3. Uložte šablonu šířky pásma.

Složený plán se pak spustí v čase zvolení a spuštění každý den.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Otázky a odpovědi týkající se šablon šířky pásma

**Otázka**. Co se stane s ovládacími prvky šířky pásma v době mezi plány? (Plán skončil a ještě nebyl spuštěn.)

**A**. V takových případech se nepoužijí žádné ovládací prvky šířky pásma. To znamená, že zařízení může při vrstvení dat do cloudu používat neomezenou šířku pásma.

**Otázka**. Můžete upravit šablony šířky pásma na zařízení v režimu offline?

**A**. Pokud je odpovídající zařízení offline, nebudete moct měnit šablony šířky pásma na kontejnerech svazků.

**Otázka**. Můžete upravit šablonu šířky pásma přidruženou k kontejneru svazků, pokud jsou přidružené svazky offline?

**A**. Můžete upravit šablonu šířky pásma přidruženou k kontejneru svazků, ve kterém jsou svazky offline. Všimněte si, že když jsou svazky offline, nebudou se ze zařízení do cloudu převrstvena žádná data.

**Otázka**. Můžete odstranit výchozí šablonu?

**A**. I když můžete odstranit výchozí šablonu, není to vhodné. Je sledováno použití výchozí šablony, včetně upravených verzí. Data sledování se analyzují a v průběhu času se používají ke zlepšení výchozí šablony.

**Otázka**. Jak zjistíte, že je třeba upravit šablony šířky pásma?

**A**. Jeden z příznaků, které potřebujete pro úpravu šablon šířky pásma, je při spuštění zobrazení sítě zpomalit nebo potlačení několikrát za den. Pokud k tomu dojde, Sledujte síť úložiště a využití a Prohlédněte si grafy výkon I/O a propustnost sítě.

Z dat propustnosti sítě určete čas a kontejnery svazků, ve kterých dochází k kritickým místu sítě. Pokud k tomu dojde, když se data vrství do cloudu (tyto informace získáte z výkonu I/O pro všechny kontejnery svazků pro zařízení do cloudu), budete muset upravit šablony šířky pásma přidružené k kontejnerům svazků.

Po použití upravených šablon bude nutné znovu monitorovat síť pro významné latence. Pokud ještě existují, budete muset znovu navštívit šablony šířky pásma.

**Otázka**. Co se stane, když máte na svém zařízení více kontejnerů svazků, které se překrývají, ale na každý z nich se vztahují jiné limity?

**A**. Pojďme předpokládat, že máte zařízení se 3 kontejnery svazků. Plány přidružené k těmto kontejnerům se úplně překrývají. Pro každý z těchto kontejnerů jsou použité limity šířky pásma 5, 10 a 15 MB/s. Když vstup/výstup probíhá na všech těchto kontejnerech současně, mohou být aplikovány minimální limity šířky pásma 3: v tomto případě je 5 MB/s, protože tyto odchozí vstupně-výstupní požadavky sdílejí stejnou frontu.

## <a name="best-practices-for-bandwidth-templates"></a>Osvědčené postupy pro šablony šířky pásma

Použijte tyto osvědčené postupy pro zařízení StorSimple:

* Nakonfigurujte na svém zařízení šablony šířky pásma, aby bylo možné proměnlivé omezení propustnosti sítě zařízením v různou denní dobu. Tyto šablony šířky pásma při použití s plány zálohování můžou efektivně využít další šířku pásma sítě pro cloudové operace v době mimo špičku.
* Vypočítá skutečnou šířku pásma potřebnou pro konkrétní nasazení na základě velikosti nasazení a požadovaného plánovaného času obnovení (RTO).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [používání služby StorSimple Správce zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).


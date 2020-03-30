---
title: Správa šablon šířky pásma pro řadu StorSimple 8000 | Dokumenty společnosti Microsoft
description: Popisuje, jak spravovat šablony šířky pásma StorSimple, které umožňují řídit spotřebu šířky pásma.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 13a3e57bb27c075fc045e87790dbe13369ed9f8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254895"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-bandwidth-templates"></a>Použití služby StorSimple Device Manager ke správě šablon šířky pásma StorSimple

## <a name="overview"></a>Přehled

Šablony šířky pásma umožňují konfigurovat využití šířky pásma sítě napříč několika denními plány tak, aby vrstvily data ze zařízení StorSimple do cloudu.

S plány omezení šířky pásma můžete:

* Zadejte vlastní plány šířky pásma v závislosti na využití sítě pracovního vytížení.
* Centralizujte správu a znovu používejte plány napříč různými zařízeními snadným a bezproblémovým způsobem.

> [!NOTE]
> Tato funkce je k dispozici pouze pro fyzikální zařízení StorSimple (modely 8100 a 8600) a ne pro storsimple cloudové spotřebiče (modely 8010 a 8020).


## <a name="the-bandwidth-templates-blade"></a>Okno Šablony šířky pásma

Okno **Šablony šířky pásma** obsahuje všechny šablony šířky pásma pro vaši službu v tabulkovém formátu a obsahuje následující informace:

* **Název** – jedinečný název přiřazený šabloně šířky pásma při jeho vytvoření.
* **Plán** – počet plánů obsažených v dané šabloně šířky pásma.
* **Používá** – počet svazků pomocí šablony šířky pásma.

Další informace, které vám pomohou nakonfigurovat šablony šířky pásma, naleznete také v:

* [Otázky a odpovědi týkající se šablon šířky pásma](#questions-and-answers-about-bandwidth-templates)
* [Doporučené postupy pro šablony šířky pásma](#best-practices-for-bandwidth-templates)

## <a name="add-a-bandwidth-template"></a>Přidání šablony šířky pásma

Chcete-li vytvořit novou šablonu šířky pásma, proveďte následující kroky.

#### <a name="to-add-a-bandwidth-template"></a>Přidání šablony šířky pásma

1. Přejděte do služby Správce zařízení StorSimple, klikněte na **šablony šířky pásma** a potom klikněte na **+ Přidat šablonu šířky pásma**.

    ![Klikněte na + Přidat šablonu šířky pásma](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp1.png)

2. V okně **Přidat šablonu šířky pásma** proveďte následující kroky:
   
    1. Zadejte jedinečný název šablony šířky pásma.
    2. Definujte plán šířky pásma. Vytvoření plánu:
   
        1. V rozevíracím seznamu zvolte **Dny** v týdnu, pro které je plán nakonfigurován. Můžete vybrat více dní.        
        
        2. Zadejte **počáteční čas** ve formátu _hh:mm._ To je, když začne plán.

        3. Zadejte **koncový čas** ve formátu _hh:mm._ To je, když plán přestane.
      
           > [!NOTE]
           > Překrývající se plány nejsou povoleny. Pokud počáteční a koncový čas bude mít za následek překrývající se plán, zobrazí se chybová zpráva v tomto smyslu.

        4. Zadejte **rychlost šířky pásma**. Jedná se o šířku pásma v megabitech za sekundu (Mb/s), kterou vaše zařízení StorSimple používá v operacích zahrnujících cloud (nahrávání i stahování). Do tohoto pole zadejte číslo od 1 do 1 000.

            ![Definovat plán šířky pásma](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp2.png)
         
            Opakujte výše uvedené kroky a definujte více plánů pro šablonu, dokud nebudete hotovi.

        5. Chcete-li začít vytvářet šablonu šířky pásma, klepněte na tlačítko **Přidat.** Vytvořená šablona je přidána do seznamu šablon šířky pásma.
      

## <a name="edit-a-bandwidth-template"></a>Úprava šablony šířky pásma

Chcete-li upravit šablonu šířky pásma, proveďte následující kroky.

### <a name="to-edit-a-bandwidth-template"></a>Úprava šablony šířky pásma

1. Přejděte na službu Správce zařízení StorSimple a klepněte na **položku Šablony šířky pásma**.
2. V seznamu šablon šířky pásma vyberte šablonu, kterou chcete odstranit. Klepněte pravým tlačítkem myši a v místní nabídce vyberte **odstranit**.
3. Po zobrazení výzvy k potvrzení klepněte na tlačítko **OK**. To by mělo odstranit šablonu šířky pásma. 
4. Seznam šablon šířky pásma se aktualizuje tak, aby odrážel odstranění.

> [!NOTE]
> Změny nelze uložit, pokud se upravený plán překrývá s existujícím plánem v šabloně šířky pásma, kterou upravujete.

## <a name="delete-a-bandwidth-template"></a>Odstranění šablony šířky pásma

Chcete-li odstranit šablonu šířky pásma, proveďte následující kroky.

#### <a name="to-delete-a-bandwidth-template"></a>Odstranění šablony šířky pásma

1. Přejděte na službu Správce zařízení StorSimple a klepněte na **položku Šablony šířky pásma**.
2. V seznamu šablon šířky pásma vyberte šablonu, kterou chcete odstranit. Klepněte pravým tlačítkem myši a v místní nabídce vyberte Odstranit.
3. Po zobrazení výzvy k potvrzení klepněte na tlačítko **OK**. To by mělo odstranit šablonu šířky pásma.
4. Seznam šablon šířky pásma se aktualizuje tak, aby odrážel odstranění.

Pokud je šablona používána libovolným svazkem(y), nebudete ji moci odstranit. Zobrazí se chybová zpráva oznamující, že šablona je používána. Zobrazí se dialogové okno s chybovou zprávou s upozorněním, že všechny odkazy na šablonu by měly být odebrány.

Všechny odkazy na šablonu můžete odstranit tak, že přejdete na stránku **Kontejnery svazků** a upravíte kontejnery svazků, které používají tuto šablonu tak, aby používaly jinou šablonu, nebo pomocí vlastního nebo neomezeného nastavení šířky pásma. Po odebrání všech odkazů můžete šablonu odstranit.

## <a name="use-a-default-bandwidth-template"></a>Použití výchozí šablony šířky pásma

Výchozí šablona šířky pásma je k dispozici a je ve výchozím nastavení používána kontejnery svazků k vynucení ovládacích prvků šířky pásma při přístupu ke cloudu. Výchozí šablona slouží také jako připravená reference pro uživatele, kteří vytvářejí vlastní šablony. Podrobnosti této výchozí šablony jsou:

* **Jméno** – neomezené noci a víkendy
* **Plán** - Jeden plán od pondělí do pátku, který používá rychlost šířky pásma 1 Mbps mezi 8:00 a 17:00 času zařízení. Šířka pásma je nastavena na neomezenou po zbytek týdne.

Výchozí šablonu lze upravit. Použití této šablony (včetně upravených verzí) je sledováno.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Vytvoření celodenní šablony šířky pásma, která začíná v určený čas

Tento postup vytvořte plán, který začíná v určený čas a běží celý den. V příkladu plán začíná v 9 hodin ráno a běží až do 9:00 následujícího rána. Je důležité si uvědomit, že počáteční a koncový čas pro daný plán musí být obsažen ve stejném 24hodinovém plánu a nesmí táhnout na více dní. Pokud potřebujete nastavit šablony šířky pásma, které se rozprostírají více dní, budete muset použít více plánů (jak je znázorněno v příkladu).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Vytvoření celodenní šablony šířky pásma

1. Vytvořte plán, který začíná v 9:00 ráno a běží až do půlnoci.
2. Přidejte další plán. Nakonfigurujte druhý plán tak, aby běžel od půlnoci do 9:00 ráno.
3. Uložte šablonu šířky pásma.

Složený plán pak začne v době vašeho výběru a spustí se po celý den.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Otázky a odpovědi týkající se šablon šířky pásma

**Q**. Co se stane s ovládacími prvky šířky pásma, když se účastníte mezi plány? (Plán byl ukončen a další ještě nezačal.)

**A**. V takových případech nebudou použity žádné ovládací prvky šířky pásma. To znamená, že zařízení může při vrstvení dat do cloudu používat neomezenou šířku pásma.

**Q**. Můžete upravit šablony šířky pásma na offline zařízení?

**A**. Pokud je odpovídající zařízení offline, nebude možné upravovat šablony šířky pásma v kontejnerech svazků.

**Q**. Můžete upravit šablonu šířky pásma přidruženou ke kontejneru svazku, když jsou přidružené svazky offline?

**A**. Můžete upravit šablonu šířky pásma přidruženou ke kontejneru svazku, jehož svazky jsou offline. Všimněte si, že když jsou svazky offline, žádná data se budou vrstvené ze zařízení do cloudu.

**Q**. Můžete odstranit výchozí šablonu?

**A**. I když můžete odstranit výchozí šablonu, není to vhodné. Použití výchozí šablony, včetně upravených verzí, je sledováno. Data sledování jsou analyzována a v průběhu času se používají ke zlepšení výchozí šablony.

**Q**. Jak zjistíte, že šablony šířky pásma je třeba upravit?

**A**. Jedním z příznaků, které je třeba upravit šablony šířky pásma je, když začnete vidět síť zpomalit nebo udusit několikrát za den. Pokud k tomu dojde, sledujte síť úložiště a využití pomocí grafů výkonu vstupně-va a propustnost sítě.

Z dat propustnost sítě určete denní dobu a kontejnery svazků, ve kterých dochází k kritickému místu sítě. Pokud k tomu dojde, když jsou data vrstvená do cloudu (získat tyto informace z výkonu vstupně-videa pro všechny kontejnery svazků pro zařízení do cloudu), budete muset upravit šablony šířky pásma přidružené k kontejnerům svazků.

Po upravené šablony jsou používány, budete muset sledovat síť znovu významné latence. Pokud tyto stále existují, pak budete muset znovu navštívit šablony šířky pásma.

**Q**. Co se stane, když více kontejnerů svazků na mém zařízení má plány, které se překrývají, ale pro každý z nich platí různé limity?

**A**. Předpokládejme, že máte zařízení s kontejnery 3 svazky. Plány přidružené k těmto kontejnerům se zcela překrývají. Pro každý z těchto kontejnerů jsou použité limity šířky pásma 5, 10 a 15 Mb/s. Pokud vstupně-out dochází na všechny tyto kontejnery ve stejnou dobu, minimálně 3 omezení šířky pásma mohou být použity: v tomto případě 5 Mbps jako tyto odchozí vstupně-va požadavky sdílejí stejnou frontu.

## <a name="best-practices-for-bandwidth-templates"></a>Doporučené postupy pro šablony šířky pásma

Postupujte podle těchto doporučených postupů pro vaše zařízení StorSimple:

* Nakonfigurujte šablony šířky pásma v zařízení tak, aby umožňovala variabilní omezení propustnosti sítě zařízením v různých denních dobách. Tyto šablony šířky pásma při použití s plány zálohování můžete efektivně využít další šířku pásma sítě pro operace cloudu mimo špičku.
* Vypočítejte skutečnou šířku pásma požadovanou pro konkrétní nasazení na základě velikosti nasazení a požadovaného cíle doby obnovení (RTO).

## <a name="next-steps"></a>Další kroky

Další informace o [správě zařízení StorSimple Device Manager pomocí služby StorSimple](storsimple-8000-manager-service-administration.md).


---
title: Správa šířky pásma šablon pro řady StorSimple 8000 | Microsoft Docs
description: Popisuje, jak spravovat šablony StorSimple šířky pásma, které umožňují řídit šířku pásma.
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
ms.openlocfilehash: 50d0a920bef097013feddc828d2c37133b9057b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875071"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-bandwidth-templates"></a>Použít službu StorSimple Manager zařízení ke správě šablon šířky pásma zařízení StorSimple

## <a name="overview"></a>Přehled

Šablony šířky pásma vám umožňují konfigurovat využití šířky pásma sítě v rámci více plánů času dne do vrstvy data ze zařízení StorSimple do cloudu.

Plány omezení šířky pásma můžete:

* Zadejte vlastní šířky pásma plány v závislosti na použití sítě zatížení.
* Centralizovat správu a znovu použít plány v různých zařízeních snadné a plynulé způsobem.

> [!NOTE]
> Tato funkce je k dispozici pouze pro fyzického zařízení StorSimple (modelů 8100 a 8600) a ne pro zařízení StorSimple cloudu (modelů 8010 a 8020).


## <a name="the-bandwidth-templates-blade"></a>V okně Šablony šířky pásma

**Šablony šířky pásma** okno má všechny šablony šířky pásma pro vaši službu v tabulkovém formátu a obsahuje následující informace:

* **Název** – přiřazené šablony šířky pásma při vytváření jedinečný název.
* **Plán** – počet plány, které jsou součástí šablony dané šířky pásma.
* **Používá** – počet svazků pomocí šablony šířky pásma.

Další informace vám pomohou při konfiguraci šablon šířky pásma v můžete také najít:

* [Otázky a odpovědi týkající se šířky pásma šablony](#questions-and-answers-about-bandwidth-templates)
* [Doporučené postupy pro šablony šířky pásma](#best-practices-for-bandwidth-templates)

## <a name="add-a-bandwidth-template"></a>Přidání šablony šířky pásma

Proveďte následující kroky k vytvoření nové šablony šířky pásma.

#### <a name="to-add-a-bandwidth-template"></a>Přidání šablony šířky pásma

1. Přejděte do služby StorSimple Manager zařízení, klikněte na tlačítko **šablony šířky pásma** a pak klikněte na **+ šablony šířky pásma přidat**.

    ![Klikněte na tlačítko + přidání šablony šířky pásma](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp1.png)

2. V **přidat šablonu šířky pásma** okno, proveďte následující kroky:
   
    1. Zadejte jedinečný název pro šablonu šířky pásma.
    2. Definujte plán šířky pásma. Vytvoření plánu:
   
        1. V rozevíracím seznamu vyberte **dní** v týdnu je nakonfigurovaný plán pro. Můžete vybrat více dní.        
        
        2. Zadejte **čas spuštění** v _hh: mm_ formátu. To je, když bude zahájena podle plánu.

        3. Zadejte **koncový čas** v _hh: mm_ formátu. To je, když se zastaví plán.
      
           > [!NOTE]
           > Překrývající se plány nejsou povoleny. Pokud počáteční a koncový čas způsobí překrývající se plán, zobrazí se chybová zpráva k tomuto účelu.

        4. Zadejte **šířky pásma**. Toto je šířka pásma v MB za sekundu (Mbps) používané zařízení StorSimple v operací zahrnujících cloudu (nahrávání a stahování). Zadejte číslo mezi 1 a 1000 u tohoto pole.

            ![Definujte plán šířky pásma](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp2.png)
         
            Zopakujte výše uvedené kroky k definování více plánů pro šablonu, až do dokončení.

        5. Klikněte na tlačítko **přidat** chcete začít vytvářet šablonu šířky pásma. Vytvořená šablona se přidá do seznamu šablon šířky pásma.
      

## <a name="edit-a-bandwidth-template"></a>Upravit šablonu šířky pásma

Proveďte následující kroky, chcete-li upravit šablonu šířky pásma.

### <a name="to-edit-a-bandwidth-template"></a>Chcete-li upravit šablonu šířky pásma

1. Přejděte k vaší Správce zařízení StorSimple služby a klikněte na tlačítko **šablony šířky pásma**.
2. V seznamu šablon šířky pásma vyberte šablonu, kterou chcete odstranit. Klikněte pravým tlačítkem myši a v místní nabídce vyberte **odstranit**.
3. Po zobrazení výzvy k potvrzení, klikněte na tlačítko **OK**. To by měl odstranit šablonu šířky pásma. 
4. Seznam aktualizací šablony šířky pásma, aby odrážela odstranění.

> [!NOTE]
> Vaše změny nelze uložit, pokud upravená plánu překrývá s existující plán v šabloně šířky pásma, který chcete upravit.

## <a name="delete-a-bandwidth-template"></a>Odstranit šablonu šířky pásma

Proveďte následující kroky k odstranění šablony šířky pásma.

#### <a name="to-delete-a-bandwidth-template"></a>Chcete-li odstranit šablonu šířky pásma

1. Přejděte k vaší Správce zařízení StorSimple služby a klikněte na tlačítko **šablony šířky pásma**.
2. V seznamu šablon šířky pásma vyberte šablonu, kterou chcete odstranit. Klikněte pravým tlačítkem myši a v místní nabídce vyberte příkaz odstranit.
3. Po zobrazení výzvy k potvrzení, klikněte na tlačítko **OK**. To by měl odstranit šablonu šířky pásma.
4. Seznam aktualizací šablony šířky pásma, aby odrážela odstranění.

Pokud šablona se používá ve všech svazcích, nebude možné jej odstranit. Zobrazí se chybová zpráva oznamující, že šablona se používá. Dialogové okno chyby zpráva se zobrazí radí, že by měl odstranit všechny odkazy na šablony.

Můžete odstranit všechny odkazy na šablony přímým přístupem **kontejnery svazků** stránky a úprava kontejnery svazků, které tuto šablonu použít tak, aby použít jinou šablonu nebo použít nastavení vlastní nebo neomezená šířky pásma. Pokud byly odebrány všechny odkazy, můžete odstranit šablonu.

## <a name="use-a-default-bandwidth-template"></a>Použít výchozí šablonu šířky pásma

Výchozí šablony šířky pásma je k dispozici a je používána kontejnery svazků ve výchozím nastavení k vynucení řízení šířky pásma při přístupu k cloudu. Výchozí šablony slouží také jako připravené odkaz pro uživatele, kteří vytvořit své vlastní šablony. Podrobnosti o této výchozí šablony jsou:

* **Název** – neomezená noci a víkendů
* **Plán** – jeden plán od pondělí až pátek, která se vztahuje šířky pásma 1 MB/s mezi 8: 00 a 17: 00 času. Šířka pásma je nastavena na neomezený zbytek v týdnu.

Můžete upravit výchozí šablony. Použití této šablony (včetně upravená verze) sledován.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Vytvořit šablonu celodenní šířky pásma, která se spouští v zadanou dobu

Tento postup slouží k vytvoření plánu, který se spustí v zadaný čas a spustí celý den. V příkladu plán začíná na 9: 00 ráno a spouští až 9: 00 ráno Další. Je důležité si uvědomit, že počáteční a koncový čas pro dané plán musí oba být obsaženy na stejný plán 24 hodin a nesmí zahrnovat více dní. Pokud potřebujete nastavit šablony šířky pásma, které jsou rozmístěny v několika dní, budete se muset použít více plánů (jak je znázorněno v příkladu).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Chcete-li vytvořit šablonu celodenní šířky pásma

1. Vytvořte plán, který začíná v 9: 00 ráno a spouští dokud půlnoci.
2. Přidejte jiný plán. Nakonfigurujte druhý plán pro spouštění z půlnoc až 9: 00 ráno.
3. Uložte šablonu šířky pásma.

Složený plán bude poté otevřete současně dle vašeho výběru a spusťte celodenní.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Otázky a odpovědi týkající se šířky pásma šablony

**Q**. Co se stane řízení šířky pásma, když jsou mezi plány? (Skončila plánu a jiný se ještě nespustilo.)

**A**. V takových případech bude těmto nekompatibilitám bez řízení šířky pásma. To znamená, že zařízení použít neomezenou šířku pásma při vrstvení data do cloudu.

**Q**. Můžete upravit šablony šířky pásma na zařízení s offline?

**A**. Nebudete moct změnit šablony šířky pásma na kontejnery svazků, pokud odpovídající zařízení je offline.

**Q**. Můžete upravit šablonu šířky pásma přidružené kontejner svazků, pokud přidružené svazky jsou offline?

**A**. Můžete upravit šablonu šířky pásma přidružené kontejner svazků, jejichž svazky jsou offline. Všimněte si, že po offline svazky se žádná data budou být rozvrstvena ze zařízení do cloudu.

**Q**. Můžete odstranit výchozí šablonu?

**A**. I když můžete odstranit výchozí šablonu, není vhodné učinit. Použití výchozí šablony, včetně upravená verze sledován. Sledování dat se analyzují a v průběhu času, které se používají ke zlepšení výchozí šablony.

**Q**. Jak můžete určit, jestli vaše šablony šířky pásma, který je potřeba upravit?

**A**. Jeden z příznaků, je třeba upravit šablony šířky pásma je při spuštění zobrazuje sítě zpomalit nebo vyseknutí několikrát za den. Pokud k tomu dojde, sledujte prohlížením grafy vstupně-výstupní výkon a propustnost sítě úložiště a využití sítě.

Z dat propustnost sítě určete čas, den a kontejnery svazků, ve kterých dojde k přetížení sítě. Pokud to se stane, když probíhá vrstveny data do cloudu (získat tyto informace z vstupně-výstupní výkon pro všechny kontejnery svazků pro zařízení do cloudu), pak budete muset upravit šablony šířky pásma přidružené k vaší kontejnery svazků.

Po změny šablon se používají, musíte se ke sledování sítě znovu pro významné latenci. Pokud tyto stále existují, pak bude muset pokroku šablony šířky pásma.

**Q**. Co se stane, pokud máte více kontejnery svazků na zařízení plány této překrývají, ale jiné omezení se vztahují na všechny?

**A**. Předpokládejme, že máte zařízení s 3 kontejnery svazků. Plány přidružené k těmto kontejnerům úplně překrývat. Pro každý z těchto kontejnerů omezení šířky pásma použít jsou 5, 10 až 15 MB/s v uvedeném pořadí. Při vstupně-výstupních operací dochází na všech těchto kontejnerů ve stejnou dobu, může být použitá minimálně 3 omezení šířky pásma: v tomto případě 5 MB/s jako tyto odchozí vstupně-výstupní požadavky sdílet stejnou frontou.

## <a name="best-practices-for-bandwidth-templates"></a>Doporučené postupy pro šablony šířky pásma

Použijte tyto osvědčené postupy pro zařízení StorSimple:

* Konfigurace šablon šířky pásma na svém zařízení povolit proměnné omezení propustnosti síťových zařízení v různých časech dne. Tyto šablony šířky pásma při použití s plánů zálohování můžete efektivně využívat další šířku pásma sítě pro operace cloudu mimo špičku.
* Vypočítejte skutečně nezbytná šířka pásma pro konkrétní nasazení podle velikosti nasazení a plánovanou dobu požadované obnovení (RTO).

## <a name="next-steps"></a>Další kroky

Další informace o [pomocí služby StorSimple Manager zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).


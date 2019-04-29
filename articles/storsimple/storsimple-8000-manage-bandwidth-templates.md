---
title: Správa šablon šířky pásma pro řadu StorSimple 8000 | Dokumentace Microsoftu
description: Popisuje, jak Správa šablon šířky pásma StorSimple, které umožňují řídit využití šířky pásma.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699451"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-bandwidth-templates"></a>Správa šablon šířky pásma StorSimple pomocí služby Správce zařízení StorSimple

## <a name="overview"></a>Přehled

Šablony šířky pásma vám umožňují konfigurovat využití šířky pásma sítě mezi více plánů denní dobu pro vrstvení dat ze zařízení StorSimple do cloudu.

S plány omezení šířky pásma můžete:

* Zadejte vlastní šířky pásma plány v závislosti na použití síťové úlohy.
* Centralizujte si správu a opakovaně používat plány na různých zařízeních, snadné a bezproblémové způsobem.

> [!NOTE]
> Tato funkce je dostupná pouze u fyzických zařízení StorSimple (modely 8100 a 8600) a ne pro řešení StorSimple Cloud Appliance (modely 8010 a 8020).


## <a name="the-bandwidth-templates-blade"></a>Okno šablony šířky pásma

**Šablony šířky pásma** okno má všechny šablony šířky pásma pro vaši službu ve formátu tabulky a obsahuje následující informace:

* **Název** – jedinečný název přiřazený šablonu šířky pásma při vytvoření rovnou uložil.
* **Plán** – počet plánů obsažené v šabloně danou šířku pásma.
* **Používá** – počet svazky s využitím šablon šířky pásma.

Můžete také najít další informace, které vám pomohou při konfiguraci šablon šířky pásma v:

* [Otázky a odpovědi týkající se šablony šířky pásma](#questions-and-answers-about-bandwidth-templates)
* [Osvědčené postupy pro šablony šířky pásma](#best-practices-for-bandwidth-templates)

## <a name="add-a-bandwidth-template"></a>Přidat šablonu šířky pásma

Proveďte následující kroky a vytvoříte novou šablonu šířky pásma.

#### <a name="to-add-a-bandwidth-template"></a>Přidání šablony šířky pásma

1. Přejděte do služby Správce zařízení StorSimple, klikněte na tlačítko **šablony šířky pásma** a potom klikněte na tlačítko **+ šablona šířky pásma přidat**.

    ![Klikněte na tlačítko + Přidat šablonu šířky pásma](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp1.png)

2. V **přidat šablonu šířky pásma** okno, proveďte následující kroky:
   
    1. Zadejte jedinečný název pro šablonu šířky pásma.
    2. Definujte plán šířky pásma. Vytvoření plánu:
   
        1. Z rozevíracího seznamu, zvolte **dnů** v týdnu nakonfigurovaný plán. Můžete vybrat několik dní.        
        
        2. Zadejte **čas zahájení** v _hh: mm_ formátu. Toto je při začne plán.

        3. Zadejte **koncový čas** v _hh: mm_ formátu. Toto je při plán se zastaví.
      
           > [!NOTE]
           > Překrývající se plány nejsou povoleny. Pokud počátečním a koncovým časem bude mít za následek překrývající se plán, zobrazí se příslušná chybová zpráva.

        4. Zadejte **rychlost šířky pásma**. Toto je šířka pásma v megabitech za sekundu (MB/s) používané zařízení StorSimple v operace, které zahrnují cloudu (nahrávání a stahování). Do tohoto pole zadejte číslo od 1 do 1 000.

            ![Definujte plán šířky pásma](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp2.png)
         
            Opakujte předchozí postup k definování více plánů pro šablonu, až budete hotovi.

        5. Klikněte na tlačítko **přidat** chcete začít vytvářet šablonu šířky pásma. Vytvořená šablona se přidá do seznamu šablon šířky pásma.
      

## <a name="edit-a-bandwidth-template"></a>Upravit šablonu šířky pásma

Proveďte následující kroky, chcete-li upravit šablonu šířky pásma.

### <a name="to-edit-a-bandwidth-template"></a>Chcete-li upravit šablonu šířky pásma

1. Služby do Správce zařízení StorSimple a klikněte na tlačítko **šablony šířky pásma**.
2. V seznamu šablon šířky pásma vyberte šablonu, kterou chcete odstranit. Klikněte pravým tlačítkem a v místní nabídce vyberte **odstranit**.
3. Po zobrazení výzvy k potvrzení, klikněte na tlačítko **OK**. To by měl odstranit šablonu šířky pásma. 
4. Seznam aktualizací šablony šířky pásma tak, aby odrážely odstranění.

> [!NOTE]
> Změny nelze uložit, pokud upravených plánu překrývá s existující plán v šablonu šířky pásma, který chcete upravit.

## <a name="delete-a-bandwidth-template"></a>Odstranit šablonu šířky pásma

Proveďte následující kroky pro odstranění šablony šířky pásma.

#### <a name="to-delete-a-bandwidth-template"></a>Chcete-li odstranit šablonu šířky pásma

1. Služby do Správce zařízení StorSimple a klikněte na tlačítko **šablony šířky pásma**.
2. V seznamu šablon šířky pásma vyberte šablonu, kterou chcete odstranit. Klikněte pravým tlačítkem a v místní nabídce vyberte odstranit.
3. Po zobrazení výzvy k potvrzení, klikněte na tlačítko **OK**. To by měl odstranit šablonu šířky pásma.
4. Seznam aktualizací šablony šířky pásma tak, aby odrážely odstranění.

Šablona se používá všechny svazky, nebude možné jej odstranit. Zobrazí se chybová zpráva oznamující, že šablona se používá. Dialogové okno s chybová zpráva se zobrazí informacemi, že byste měli odebrat všechny odkazy na šablony.

Díky přístupu můžete odstranit všechny odkazy na šablony **kontejnery svazků** stránky a úprava kontejnerů svazků, které používají tuto šablonu tak, aby použít jinou šablonu nebo použít nastavení šířky pásma vlastní nebo neomezený. Pokud byly odebrány všechny odkazy, můžete odstranit šablonu.

## <a name="use-a-default-bandwidth-template"></a>Použít výchozí šablonu šířky pásma

Výchozí šablona šířky pásma je k dispozici a kontejnery svazků používá ve výchozím nastavení k vynucení řízení šířky pásma při přístupu ke cloudu. Výchozí šablona slouží taky jako připravené referenční informace pro uživatele, kteří vytvářejí vlastní šablony. Podrobnosti o této výchozí šablony jsou:

* **Název** – neomezený počet nocí a víkendů
* **Plán** – jeden plán od pondělí do pátku, která se použije rychlost šířky pásma 1 MB/s až 8: 00, čas 17: 00 zařízení. Šířka pásma nastavená na neomezený počet pro zbytek týdne.

Můžete upravit výchozí šablonu. Použití této šablony (včetně upravenou verzí) jsou sledovány.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Vytvořit celodenní šablonu šířky pásma, který se spustí v zadaný čas

Pomocí následujícího postupu vytvořte plán, který se spustí v určenou dobu a spustí celý den. V tomto příkladu plán začíná v 9: 00 ráno a běží až do 9: 00 příští ráno. Je důležité si uvědomit počátečním a koncovým časem pro daný plán, který musí obě být obsažena v stejné období 24 hodin a nemůžou zahrnovat víc dnů. Pokud je potřeba nastavit šablony šířky pásma, které trvají více dní, je potřeba použít více plánů (jak je znázorněno v příkladu).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Chcete-li vytvořit celodenní šablonu šířky pásma

1. Vytvořte plán, který začíná v 9: 00 ráno a běží až do půlnoci.
2. Přidáte jiný plán. Nakonfigurujte druhý plán pro spuštění od půlnoci do 9: 00 ráno.
3. Uložte šablonu šířky pásma.

Složený plán se pak spusťte v době podle vašeho výběru a celodenní.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Otázky a odpovědi týkající se šablony šířky pásma

**Q**. Co se stane řízení šířky pásma, když jsou mezi plány? (Nějaký plán skončil a jiný se ještě nespustilo.)

**A**. V takovém případě se použijí bez řízení šířky pásma. To znamená, že zařízení může používat neomezenou šířku pásma při vrstvení dat do cloudu.

**Q**. Můžete upravit šablony šířky pásma na zařízení s offline?

**A**. Nebudete mít k úpravě šablony šířky pásma na kontejnery svazků, pokud odpovídající zařízení je offline.

**Q**. Můžete upravit šablonu šířky pásma spojené s kontejner svazků, pokud přidružené svazky jsou offline?

**A**. Můžete upravit šablonu šířky pásma spojené s kontejner svazků, svazky jsou offline. Všimněte si, že když svazků jsou offline, žádná data se vrstvený ze zařízení do cloudu.

**Q**. Můžete odstranit výchozí šablonu?

**A**. I když se budete moct odstranit výchozí šablonu, není vhodné tak učinit. Použití výchozí šablony, včetně upravenou verzí, jsou sledovány. Data sledování se analyzuje a v průběhu času, se používá ke zlepšení výchozí šablony.

**Q**. Jak se určují, že šablony šířky pásma, který je potřeba upravit?

**A**. Mezi příznaky, že budete muset upravit šablony šířky pásma je při spuštění zobrazit síť zpomalovat nebo vyseknutí více než jednou za den. Pokud k tomu dojde, monitorujte zobrazením grafy výkonu vstupně-výstupních operací a propustnosti sítě úložiště a využití sítě.

Z dat propustnost sítě určuje čas, den a kontejnery svazků, ve kterých dojde k kritická místa sítě. Pokud to se stane, když je právě data Vrstvená do cloudu (získat tyto informace z vstupně-výstupní výkon pro všechny kontejnery svazků zařízení do cloudu), pak budete muset upravit šablony šířky pásma přidružené kontejnery svazků.

Až se změněné šablony používá, je potřeba monitorování sítě za významné latenci. Pokud stále existují, je potřeba návštěvě šablony šířky pásma.

**Q**. Co se stane, pokud máte více kontejnerů svazků při registraci zařízení v této překrytí plány, ale pro ně používají různá omezení?

**A**. Předpokládejme, že máte zařízení s 3 kontejnery svazků. Plány přidružené těchto kontejnerů zcela překrývat. Pro každou z těchto kontejnerů omezení šířky pásma použít se 5, 10 a 15 MB/s. Pokud dochází k vstupně-výstupních operací na všech těchto kontejnerů ve stejnou dobu, může použít minimálně 3 omezení šířky pásma: v tomto případě 5 MB/s se odchozí požadavky vstupně-výstupní operace sdílení stejné frontě.

## <a name="best-practices-for-bandwidth-templates"></a>Osvědčené postupy pro šablony šířky pásma

Dodržujte tyto doporučené postupy zabezpečení pro zařízení StorSimple:

* Konfigurace šablony šířky pásma na svém zařízení umožníte proměnné omezení propustnosti sítě zařízení v různých časech dne. Tyto šablony šířky pásma při použití s plány zálohování efektivně využívat další šířka pásma pro cloudové operace během hodiny mimo špičku.
* Vypočítejte skutečnou požadovanou šířku pásma pro konkrétní nasazení na základě velikosti nasazení a plánovaná doba vyžaduje obnovení (RTO).

## <a name="next-steps"></a>Další postup

Další informace o [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).


---
title: Klonovat svazek na storSimple 8000 série | Dokumenty společnosti Microsoft
description: Popisuje různé typy klonů a použití a vysvětluje, jak můžete použít zálohovací sadu klonovat jednotlivé svazky na zařízení řady StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/05/2017
ms.author: alkohli
ms.openlocfilehash: 84734aefb72a3330d99c5707b461de2cd5e30484
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254999"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>Použití služby Správce zařízení StorSimple na webu Azure Portal ke klonování svazku

## <a name="overview"></a>Přehled

Tento kurz popisuje, jak můžete použít zálohovací sadu klonovat jednotlivé svazky prostřednictvím okna **katalogu zálohování.** Vysvětluje také rozdíl mezi *přechodovými* a *trvalými* klony. Pokyny v tomto kurzu platí pro všechny zařízení řady StorSimple 8000 se spuštěním aktualizace 3 nebo novější.

Okno **záloha** služby StorSimple Device Manager zobrazuje všechny zálohovací sady, které jsou vytvořeny při ručním nebo automatickém zálohování. Potom můžete vybrat svazek v zálohovací sadě ke klonování.

 ![Seznam zálohovacích skladeb](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>Důležité informace pro klonování svazku

Při klonování svazku zvažte následující informace.

- Klon se chová stejným způsobem jako běžný svazek. Pro klonování je k dispozici jakákoli operace, která je možná na svazku.

- Monitorování a výchozí zálohování jsou automaticky zakázány na klonovaném svazku. Je třeba nakonfigurovat klonovaný svazek pro všechny zálohy.

- Místně vázaný svazek je klonován jako vrstvený svazek. Pokud potřebujete klonovaný svazek místně připnout, můžete převést klon na místně vázaný svazek po úspěšném dokončení operace klonování. Informace o převodu vrstveného svazku na místně vázaný svazek naleznete v části [Změna typu svazku](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).

- Pokud se pokusíte převést klonovaný svazek z vrstveného na místně připnutý ihned po klonování (pokud je stále přechodný klon), převod se nezdaří s následující chybovou zprávou:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    Tato chyba je přijata pouze v případě, že klonujete na jiné zařízení. Pokud nejprve převedete přechodný klon na trvalý klon, můžete úspěšně převést svazek na místně připnutý. Pořiďte snímek cloudu přechodného klonu a převeďte jej na trvalý klon.

## <a name="create-a-clone-of-a-volume"></a>Vytvoření klonu svazku

Klon můžete vytvořit na stejném zařízení, jiné zařízení nebo dokonce cloudové zařízení pomocí místní nebo cloud snímek.

Následující postup popisuje, jak vytvořit klon z katalogu záloh.  Alternativní metodou pro zahájení klonování je přejít na **Svazky**, vybrat svazek, pak klepnutím pravým tlačítkem myši vyvolat místní nabídku a vybrat **možnost Klonovat**.

Pomocí následujících kroků vytvořte klon svazku z katalogu záloh.

#### <a name="to-clone-a-volume"></a>Klonování svazku

1. Přejděte na službu Správce zařízení StorSimple a klepněte na tlačítko **Zálohovat katalog**.

2. Vyberte zálohovací sadu následujícím způsobem:
   
   1. Vyberte příslušné zařízení.
   2. V rozevíracím seznamu zvolte zásady svazku nebo zálohování zálohy, které chcete vybrat.
   3. Zadejte časový rozsah.
   4. Chcete-li spustit tento dotaz, klepněte na **tlačítko Použít.**

      Zálohy přidružené k vybranému svazku nebo zásady zálohování by se měly objevit v seznamu zálohovacích sad.
   
      ![Seznam zálohovacích skladeb](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Rozbalte zálohovací sadu, abyste zobrazili přidružený svazek a vyberte svazek v zálohovací sadě. Klepněte pravým tlačítkem myši a potom v místní nabídce vyberte **příkaz Klonovat**.

   ![Seznam zálohovacích skladeb](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. V noži **Klonování** proveďte následující kroky:
   
   1. Identifikujte cílové zařízení. Toto je místo, kde bude vytvořen klon. Můžete zvolit stejné zařízení nebo zadat jiné zařízení.

      > [!NOTE]
      > Ujistěte se, že kapacita potřebná pro klon je nižší než kapacita dostupná na cílovém zařízení.
       
   2. Zadejte jedinečný název svazku pro klon. Název musí obsahovat 3 až 127 znaků.
      
       > [!NOTE]
       > Pole **Klonovat svazek jako** bude **vrstveno** i v případě, že klonujete místně vázaný svazek. Toto nastavení nelze změnit. Pokud však potřebujete, aby byl klonovaný svazek také místně připnutý, můžete po úspěšném vytvoření klonu převést klon na místně vázaný svazek. Informace o převodu vrstveného svazku na místně vázaný svazek naleznete v části [Změna typu svazku](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).
          
   3. V **části Připojená hostitelé**zadejte záznam řízení přístupu (ACR) pro klonování. Můžete přidat nový ACR nebo vybrat z existujícího seznamu. ACR určí, kteří hostitelé mají přístup k tomuto klonu.
      
       ![Seznam zálohovacích skladeb](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

   4. Chcete-li operaci dokončit, klepněte na tlačítko **Klonovat.**

4. Klonování úloha je zahájena a budete upozorněni, když klon je úspěšně vytvořen. Klikněte na oznámení úlohy nebo přejděte do okna **Úlohy** a sledujte úlohu klonování.

    ![Seznam zálohovacích skladeb](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. Po dokončení úlohy klonování přejděte do zařízení a klepněte na **položku Svazky**. V seznamu svazků byste měli vidět klon, který byl právě vytvořen ve stejném kontejneru svazků, který má zdrojový svazek.

    ![Seznam zálohovacích skladeb](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

Klon, který je vytvořen tímto způsobem je přechodný klon. Další informace o typech klonování naleznete [v tématu Transient vs. permanentní klony](#transient-vs-permanent-clones).


## <a name="transient-vs-permanent-clones"></a>Přechodné vs. trvalé klony
Přechodné klony jsou vytvořeny pouze v případě, že klonujete na jiné zařízení. Můžete klonovat určitý svazek ze zálohovací sady do jiného zařízení spravovaného Správcem zařízení StorSimple. Přechodný klon má odkazy na data v původním svazku a používá tato data ke čtení a zápisu místně v cílovém zařízení.

Po pořízení snímku cloud přechodný klon, výsledný klon je *trvalý* klon. Během tohoto procesu se vytvoří kopie dat v cloudu a čas na zkopírování těchto dat se určuje podle velikosti dat a latencí Azure (jedná se o kopii Azure-Azure). Tento proces může trvat dny až týdny. Přechodný klon se stane trvalým klonem a nemá žádné odkazy na původní data svazku, ze kterých byl klonován.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Scénáře pro přechodné a trvalé klony
Následující části popisují příklady situací, ve kterých lze použít přechodné a trvalé klony.

### <a name="item-level-recovery-with-a-transient-clone"></a>Obnova na úrovni položky s přechodným klonem
Je třeba obnovit jeden rok starý soubor prezentace aplikace Microsoft PowerPoint. Správce IT identifikuje konkrétní zálohu z této doby a potom vyfiltruje svazek. Správce potom naklonuje svazek, vyhledá soubor, který hledáte, a poskytne vám jej. V tomto scénáři se používá přechodný klon.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Testování v produkčním prostředí s trvalým klonem
Je třeba ověřit testovací chybu v produkčním prostředí. Vytvoříte klon svazku v produkčním prostředí a potom pořídit snímek cloudu tohoto klonu k vytvoření nezávislého klonovaného svazku. V tomto scénáři se používá trvalý klon.

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak [obnovit svazek StorSimple ze zálohovací sady](storsimple-8000-restore-from-backup-set-u2.md).
* Přečtěte si, jak [používat službu StorSimple Device Manager ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).


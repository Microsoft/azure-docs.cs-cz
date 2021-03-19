---
title: Naklonování svazku v řadě StorSimple 8000 | Microsoft Docs
description: Popisuje různé typy a využití klonování a vysvětluje, jak můžete pomocí zálohovacího skladu naklonovat jednotlivé svazky na zařízení řady StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.date: 07/15/2020
ms.author: alkohli
ms.openlocfilehash: 5ebb7f1d9440bc7a8b75afe64403762532213c1d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91767567"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>Použití služby Správce zařízení StorSimple na webu Azure Portal ke klonování svazku

## <a name="overview"></a>Přehled

V tomto kurzu se dozvíte, jak můžete pomocí zálohovacího skladu naklonovat jednotlivé svazky přes okno **zálohovat katalog** . Vysvětluje také rozdíl mezi *přechodnými* a *trvalými* klony. Pokyny v tomto kurzu se vztahují na všechna zařízení řady StorSimple 8000 s aktualizací Update 3 nebo novější.

V okně StorSimple Správce zařízení Service **Backup Catalog** se zobrazí všechny zálohovací sklady, které se vytvoří při ručním nebo automatizovaném zálohování. Pak můžete vybrat svazek v zálohovacím skladu, který chcete klonovat.

 ![Seznam zálohovacích skladů](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>Důležité informace o klonování svazku

Při klonování svazku Vezměte v úvahu následující informace.

- Klon se chová stejným způsobem jako běžný svazek. Všechny operace, které jsou na svazku k dispozici, jsou k dispozici pro klonování.

- Monitorování a výchozí zálohování jsou na klonovaném svazku automaticky zakázané. Je potřeba nakonfigurovat Klonovaný svazek pro všechny zálohy.

- Místně připojený svazek je klonován jako vrstvený svazek. Pokud potřebujete Klonovaný svazek lokálně připnuté, můžete ho po úspěšném dokončení operace klonování převést na místně připojený svazek. Informace o převedení vrstveného svazku na místně připojený svazek získáte, když přejdete na [změnit typ svazku](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).

- Pokud se pokusíte převést Klonovaný svazek z vrstveného na místně připnuté hned po klonování (Pokud je stále přechodným klonem), převod se nezdařil s následující chybovou zprávou:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    Tato chyba se obdrží jenom v případě, že klonování probíhá na jiném zařízení. Svazek můžete úspěšně převést na místně připnuté, pokud nejprve převedete přechodný klon na trvalý klon. Pořídit snímek v dočasném klonu, aby byl převeden na trvalý klon.

## <a name="create-a-clone-of-a-volume"></a>Vytvoření klonu svazku

Pomocí místního nebo cloudového snímku můžete vytvořit klon na stejném zařízení, na jiném zařízení nebo dokonce na cloudovém zařízení.

Následující postup popisuje, jak vytvořit klon ze zálohy katalogu.  

Provedením následujících kroků vytvoříte klon svazku ze zálohy katalogu.

#### <a name="to-clone-a-volume"></a>Naklonování svazku

1. Přejděte ke službě StorSimple Správce zařízení a potom klikněte na **katalog záloh**.

2. Vyberte zálohovací sklad následujícím způsobem:
   
   1. Vyberte příslušné zařízení.
   2. V rozevíracím seznamu vyberte svazek nebo zásady zálohování pro zálohu, kterou chcete vybrat.
   3. Zadejte časový rozsah.
   4. Kliknutím na **použít** spusťte tento dotaz.

      Zálohy přidružené k vybranému svazku nebo zásadám zálohování by se měly zobrazit v seznamu zálohovacích skladů.
   
      ![Seznam zálohovacích skladů 2](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Rozbalte zálohovací sklad a zobrazte přidružený svazek a vyberte svazek v zálohovacím skladu. Klikněte pravým tlačítkem a potom v místní nabídce vyberte **klonovat**.

   ![Seznam zálohovacích skladů 3](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. V okně **klonovat** proveďte následující kroky:
   
   1. Identifikujte cílové zařízení. Toto je umístění, kde se klon vytvoří. Můžete zvolit stejné zařízení nebo zadat jiné zařízení.

      > [!NOTE]
      > Ujistěte se, že kapacita požadovaná pro klon je nižší než kapacita dostupná na cílovém zařízení.
       
   2. Zadejte jedinečný název svazku pro klon. Název musí obsahovat 3 až 127 znaků.
      
       > [!NOTE]
       > Pole **Klonovaný svazek jako** bude **vrstveno** i v případě klonování místně připojeného svazku. Toto nastavení nemůžete změnit. Pokud ale potřebujete, aby Klonovaný svazek byl připnuté místně, můžete po úspěšném vytvoření klonu převést klon na místně připojený svazek. Informace o převedení vrstveného svazku na místně připojený svazek získáte, když přejdete na [změnit typ svazku](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).
          
   3. V části **připojení hostitelé** zadejte záznam řízení přístupu (ACR) pro klon. Můžete přidat novou ACR nebo vybrat z existujícího seznamu. ACR určí, kteří hostitelé budou mít přístup k tomuto klonu.
      
       ![Seznam zálohovacích skladů 4](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

   4. Operaci dokončete kliknutím na tlačítko **klonovat** .

4. Úloha klonování se iniciuje a po úspěšném vytvoření klonu se zobrazí oznámení. Kliknutím na okno oznámení úlohy nebo přejít do okna **úlohy** můžete monitorovat úlohu klonování.

    ![Seznam zálohovacích skladů 5](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. Po dokončení úlohy klonování přejděte na zařízení a pak klikněte na **svazky**. V seznamu svazků by se měl zobrazit klon, který byl právě vytvořen ve stejném kontejneru svazků, který má zdrojový svazek.

    ![Seznam zálohovacích skladů 6](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

Klon, který je vytvořen tímto způsobem, je přechodným klonem. Další informace o typech klonování naleznete v tématu [přechodné a trvalé klony](#transient-vs-permanent-clones).


## <a name="transient-vs-permanent-clones"></a>Přechodné vs. trvalé klony
Přechodné klony se vytvářejí jenom při klonování na jiné zařízení. Konkrétní svazek můžete klonovat ze zálohovacího skladu na jiné zařízení, které spravuje StorSimple Správce zařízení. Přechodný klon má odkazy na data v původním svazku a tato data používá ke čtení a zápisu místně na cílovém zařízení.

Po převzetí cloudového snímku přechodného klonu je výsledný klon *trvalým* klonem. Během tohoto procesu se v cloudu vytvoří kopie dat a čas pro kopírování těchto dat se určí podle velikosti dat a latencí Azure (Jedná se o kopii z Azure do Azure). Tento proces může trvat několik dní až týdnů. Přechodný klon se stal trvalým klonem a nemá žádné odkazy na původní data svazků, ze kterých byla naklonována.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Scénáře pro přechodné a trvalé klonování
Následující části popisují příklady situací, ve kterých lze použít přechodné a trvalé klonování.

### <a name="item-level-recovery-with-a-transient-clone"></a>Obnovení na úrovni položek s přechodným klonem
Musíte obnovit jeden rok v jednom roce starý soubor prezentace aplikace Microsoft PowerPoint. Správce IT identifikuje konkrétní zálohu od tohoto času a následně filtruje svazek. Správce pak svazek naklonuje, vyhledá soubor, který hledáte, a poskytne vám ho. V tomto scénáři se používá přechodný klon.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Testování v produkčním prostředí s trvalým klonem
Je nutné ověřit chybu testování v produkčním prostředí. V produkčním prostředí vytvoříte klon svazku a potom si pořídíte snímek tohoto klonu v cloudu, abyste vytvořili nezávislý Klonovaný svazek. V tomto scénáři se používá trvalý klon.

## <a name="next-steps"></a>Další kroky
* Naučte se [obnovit svazek StorSimple ze zálohovacího skladu](storsimple-8000-restore-from-backup-set-u2.md).
* Naučte se [používat službu StorSimple Správce zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).


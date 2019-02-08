---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: fa433f55839db66bcf8245fe058ac56702af3d6e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55888611"
---
#### <a name="to-create-a-volume"></a>Vytvoření svazku
1. V tabulkovém výpisu zařízení v okně **Zařízení** vyberte vaše zařízení. Klikněte na **+ Přidat svazek**.

    ![Přidání nového svazku](./media/storsimple-8000-create-volume-u2/step5createvol1.png)

2. V okně **Přidat svazek**:
   
   1. Do pole **Vybrat zařízení** se automaticky vyplní vaše aktuální zařízení.

   2. Z rozevíracího seznamu vyberte kontejner svazků, do kterého potřebujete přidat svazek. 

   3.  Zadejte **Název** svazku. Svazek se po vytvoření už nedá přejmenovat.

   4. V rozevíracím seznamu vyberte **Typ** svazku. Pro úlohy, které vyžadují místní záruky, nízkou latenci a vyšší výkon, vyberte typ svazku **Místně vázaný**. Pro ostatní typy dat vyberte typ svazku **Vrstvený**. Pokud tento svazek používáte k archivaci dat, zaškrtněte políčko **Použít tento svazek pro archivní data s méně častým přístupem**.
      
       Vrstvený svazek je zřizovaný dynamicky a lze jej rychle vytvořit. Výběrem možnosti **Použít tento svazek pro archivní data s méně častým přístupem** u vrstveného svazku určeného k uchovávání archivních dat změníte velikost bloku odstranění duplicit vašeho svazku na 512 kB. Pokud políčko není zaškrtnuté, příslušný vrstvený svazek bude používat velikost bloku 64 kB. Větší velikost deduplikačního bloku dat umožňuje zařízení urychlit přenos velkých objemů archivních dat do cloudu.
       
       Místně vázaný svazek je tlustě zřízený a zajišťuje, že primární data uložená ve svazku zůstanou uložená v místním zařízení a nebudou distribuována do cloudu.  Pokud vytvoříte místně vázaný svazek, zařízení zkontroluje dostupné místo v místních vrstvách a zřídí svazek požadované velikosti. Při operaci vytváření místně vázaného svazku mohou být existující data ze zařízení distribuována do cloudu a vytvoření svazku může trvat dlouhou dobu. Celková doba závisí na velikosti zřizovaného svazku, dostupné šířce pásma sítě a datech uložených v zařízení.

   5. Zadejte hodnotu **Zřízená kapacita** svazku. Poznamenejte si kapacitu dostupnou na základě vybraného typu svazku. Zadaná velikost svazku nesmí překročit velikost dostupného místa.
      
       Zařízení 8100 umožňuje zřizovat místně vázané svazky o velikosti až 8.5 TB a vrstvené svazky o velikosti až 200 TB. Větší zařízení 8600 umožňuje zřizovat místně vázané svazky o velikosti až 22.5 TB a vrstvené svazky o velikosti až 500 TB. Protože k hostování fungující sady vrstvených svazků je vyžadováno volné místo v zařízení, vytváření místně vázaných svazků ovlivňuje volné místo dostupné ke zřizování vrstvených svazků. Proto pokud vytvoříte místně připojený svazek, zmenší se dostupné volné místo pro vytváření vrstvených svazků. Podobně pokud vytvoříte vrstvený svazek, zmenší se dostupné volné místo potřebné k vytváření místně vázaných svazků.
      
       Pokud v zařízení 8100 zřídíte místně vázaný svazek o velikosti 8.5 TB (maximální možná velikost), vyčerpáte tím veškeré volné místo dostupné v zařízení. Od této chvíle už nebudete moct vytvořit žádné vrstvené svazky, protože v zařízení už nebude žádné volné místo k hostování pracovní sady vrstveného svazku. Objem dostupného volného místa ovlivňují také vrstvené svazky. Pokud například používáte zařízení 8100, ve kterém jsou už zřízeny vrstvené svazky o velikosti zhruba 106 TB, k vytváření místně vázaných svazků zbude už jenom 4 TB dostupného volného místa.

    6. V poli **Připojení hostitelé** klikněte na šipku. 

        ![Připojení hostitelé](./media/storsimple-8000-create-volume-u2/step5createvol2.png)

    7. V okně **Připojení hostitelé** zvolte existující záznam ACR nebo pomocí následujícího postupu přidejte nový záznam ACR:

       1. Zadejte **Název** záznamu ACR.
       2. V části **Název iniciátoru iSCSI** zadejte kvalifikovaný název iSCSI (IQN) hostitele s Windows. Pokud název IQN nemáte, přejděte do části [Získání názvu hostitele se systémem Windows Server](#get-the-iqn-of-a-windows-server-host).

    9. Klikněte na možnost **Vytvořit**. Vytvoří se svazek se zadaným nastavením.

        ![Kliknutí na Vytvořit](./media/storsimple-8000-create-volume-u2/step5createvol3.png)

        > [!NOTE]
        > Mějte na paměti, že svazek, který jste tu vytvořili, není chráněný. Budete muset vytvořit a přidružit k tomuto svazku zásady zálohování, které zajistí zálohování podle plánu. 


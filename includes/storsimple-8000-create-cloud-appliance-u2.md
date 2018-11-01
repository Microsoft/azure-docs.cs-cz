---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d1ca6d37d6133786aff7ad3156fea2a0c22dfb97
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164897"
---
#### <a name="to-create-a-cloud-appliance"></a>Vytvoření cloudového zařízení

1. Na webu Azure Portal přejděte do služby **Správce zařízení StorSimple**.
2. Přejděte do okna **Zařízení**. Na panelu příkazů v okně přehledu služby klikněte na **Vytvořit cloudové zařízení**.
    ![Vytvoření cloudového zařízení StorSimple](./media/storsimple-8000-create-cloud-appliance-u2/sca-create1.png)
3. V okně **Vytvořit cloudové zařízení** zadejte následující podrobnosti.
   
    ![Vytvoření cloudového zařízení StorSimple](./media/storsimple-8000-create-cloud-appliance-u2/sca-create2m.png)
   
   1. **Název** – Jedinečný název cloudového zařízení.
   2. **Model** – Zvolte model cloudového zařízení. Zařízení 8010 nabízí službu Storage úrovně Standard s 30 TB úložiště, zatímco zařízení 8020 má službu Premium Storage s 64 TB úložiště. Pokud chcete nasadit scénáře načítání souborů ze záloh na úrovni položek, zadejte 8010. Pokud chcete nasadit vysoce výkonné úlohy s nízkou latencí nebo pro použití jako sekundární zařízení pro zotavení po havárii, vyberte 8020.
   3. **Verze** – Zvolte verzi cloudového zařízení. Verze odpovídá verzi image virtuálního disku použité k vytvoření cloudového zařízení. Protože verze cloudového zařízení určuje, ze kterého fyzického zařízení přebíráte služby při selhání nebo klonujete, je důležité vytvořit správnou verzi cloudového zařízení.
   4. **Virtuální síť** – Zadejte virtuální síť, kterou chcete používat s tímto cloudovým zařízením. Při použití služby Premium Storage je nutné vybrat virtuální síť, která je podporována účtem služby Premium Storage. Nepodporované virtuální sítě se v rozevíracím seznamu zobrazují šedě. Pokud vyberete nepodporovanou virtuální síť, zobrazí se upozornění.
   5. **Podsíť** – V závislosti na vybrané virtuální síti se v rozevíracím seznamu zobrazí související podsítě. Přiřaďte cloudovému zařízení podsíť.
   6. **Účet úložiště** – Vyberte účet úložiště, který bude během zřizování uchovávat image cloudového zařízení. Tento účet úložiště by měl být ve stejné oblasti jako cloudové zařízení a virtuální síť. Fyzická ani cloudová zařízení by ho neměla používat k ukládání dat. Ve výchozím nastavení se za tímto účelem vytvoří nový účet úložiště. Pokud však víte, že už máte účet úložiště vhodný k tomuto účelu, můžete ho vybrat v seznamu. Při vytváření cloudového zařízení úrovně Premium se v rozevíracím seznamu zobrazí jenom účty služby Premium Storage.
      
      > [!NOTE]
      > Cloudové zařízení můžete používat jenom s účty úložiště Azure.
    
   7. Zaškrtnutím příslušného políčka potvrďte, že jste seznámeni s tím, že data uložená v cloudovém zařízení jsou hostována v datacentru Microsoftu.
       * Pokud používáte jenom fyzické zařízení, váš šifrovací klíč se uchovává se zařízením, a Microsoft proto nemůže data dešifrovat.

       * Při použití cloudového zařízení jsou šifrovací i dešifrovací klíč uloženy v Microsoft Azure. Další informace najdete v tématu popisujícím [aspekty zabezpečení pro používání cloudového zařízení](../articles/storsimple/storsimple-security.md).
   8. Kliknutím na **Vytvořit** zřiďte cloudové zařízení. Zřízení zařízení může trvat zhruba 30 minut. Po úspěšném vytvoření cloudového zařízení se zobrazí oznámení. Přejděte do okna Zařízení – seznam zařízení se aktualizuje a zobrazí se v něm cloudové zařízení. Stav zařízení je **Připraveno k nastavení**.
      
      ![Řešení StorSimple Cloud Appliance připravené k nastavení](./media/storsimple-8000-create-cloud-appliance-u2/sca-create3.png)


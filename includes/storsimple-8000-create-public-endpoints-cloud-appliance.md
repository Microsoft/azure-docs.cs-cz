---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a5a286753e438b7d65f3d33a82669c4f7e79a282
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86544672"
---
#### <a name="to-create-public-endpoints-on-the-cloud-appliance"></a>Vytvoření veřejných koncových bodů na cloudovém zařízení

1. Přihlaste se k webu Azure Portal.
2. Přejděte na **Virtuální počítače** a vyberte a klikněte na virtuální počítač, který používáte jako cloudové zařízení.
    
3. Potřebujete vytvořit pravidlo skupiny zabezpečení sítě (NSG) pro řízení toku přenosů do a z virtuálního počítače. Proveďte následující kroky a vytvořte pravidlo skupiny zabezpečení sítě.
    1. Vyberte **Skupina zabezpečení sítě**.
        ![Snímek obrazovky se stránkou virtuálního počítače V části nastavení se zvýrazní skupina zabezpečení sítě.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt1.png)

    2. Klikněte na výchozí skupinu zabezpečení sítě, která se zobrazí.
        ![Snímek obrazovky se stránkou skupiny zabezpečení sítě. Výchozí skupina zabezpečení sítě je zvýrazněna.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt2.png)

    3. Vyberte **Příchozí pravidla zabezpečení**.
        ![Snímek obrazovky se stránkou, která zobrazuje vlastnosti výchozí skupiny zabezpečení sítě. V navigačním podokně se zvýrazní příchozí pravidla zabezpečení.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt3.png)

    4. Kliknutím na **+ Přidat** vytvořte příchozí pravidlo zabezpečení.
        ![Snímek obrazovky se stránkou příchozí pravidla zabezpečení. Znaménko plus a slovo jsou přidány vedle sebe a jsou zvýrazněny.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt4.png)

        V okně Přidat příchozí pravidlo zabezpečení:

        1. Do pole **Název** zadejte tento název koncového bodu: WinRMHttps.
        
        2. V poli **Priorita** vyberte číslo nižší než 1 000 (což je priorita výchozího pravidla). Čím vyšší hodnota, tím nižší priorita.

        3. Nastavte **Zdroj** na hodnotu **Všechny**.

        4. V poli **Služba** vyberte **WinRM**. **Protokol** je automaticky nastaven na **TCP** a **Rozsah portů** je nastaven na **5986**.

        5. Kliknutím na **OK** vytvořte pravidlo.

            ![Snímek obrazovky okna Přidat příchozí pravidlo zabezpečení Hodnoty jsou vyplněny, jak je popsáno v postupu a je zvýrazněno tlačítko OK.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt5.png)

4. Posledním krokem je přidružení skupiny zabezpečení sítě k podsíti nebo konkrétnímu síťovému rozhraní. Proveďte následující kroky a přidružte skupinu zabezpečení sítě k podsíti.
    1. Přejděte na **Podsítě**.
    2. Klikněte na **+ Přidružit**.
        ![Snímek obrazovky se stránkou podsítí Znaménko plus a slovo přiřadí vedle sebe vedle sebe a zvýrazní se.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt7.png)

    3. Vyberte virtuální síť a pak vyberte vhodnou podsíť.
    4. Kliknutím na **OK** vytvořte pravidlo.

        ![Snímek obrazovky se stránkou přidružit podsíť Je vybraná virtuální síť a zvýrazní se tlačítko OK.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt11.png)

Po vytvoření pravidla můžete zobrazit jeho podrobnosti, abyste zjistili veřejnou virtuální IP adresu (VIP). Tuto adresu si poznamenejte.



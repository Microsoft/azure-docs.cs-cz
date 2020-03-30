---
title: Jak nainstalovat HANA na SAP HANA v Azure (velké instance) | Dokumenty společnosti Microsoft
description: Jak nainstalovat HANA na SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 023f32fce01ffbd974b182fa89fd604e62332936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617213"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>Instalace HANA na SAP HANA v Azure (velké instance)

Chcete-li nainstalovat HANA na SAP HANA v Azure (velké instance), musíte nejprve provést následující kroky:
- Poskytujete společnosti Microsoft všechna data, která chcete nasadit na velké instanci SAP HANA.
- Obdržíte SAP HANA velké instance od společnosti Microsoft.
- Vytvoříte virtuální síť Azure, která je připojená k místní síti.
- Okruh ExpressRoute pro velké instance HANA připojíte ke stejné virtuální síti Azure.
- Nainstalujete virtuální počítač Azure, který používáte jako pole odkazů pro velké instance HANA.
- Zajistíte, že se můžete připojit ze skokového rámečku k jednotce velké instance HANA a naopak.
- Zkontrolujte, zda jsou nainstalovány všechny potřebné balíčky a opravy.
- Přečtěte si poznámky SAP a dokumentaci o instalaci HANA v operačním systému, který používáte. Ujistěte se, že hana verze volby je podporována na verzi operačního systému.

Další část ukazuje příklad stažení instalačních balíčků HANA do virtuálního počítače pro přeskoče. V tomto případě je operační systém Windows.

## <a name="download-the-sap-hana-installation-bits"></a>Stažení instalačních bitů SAP HANA
Jednotky velké instance HANA nejsou přímo připojeny k internetu. Instalační balíčky ze SAP nelze stáhnout přímo do virtuálního počítače hana large instance. Místo toho stáhnete balíčky do virtuálního počítače skokbox.

Potřebujete uživatele SAP S nebo jiného uživatele, který vám umožní přístup na SAP Marketplace.

1. Přihlaste se a přejděte na [sap service marketplace](https://support.sap.com/en/index.html). Vyberte **možnost Stáhnout instalace softwaru** > **a inovovat** > **podle abecedního indexu**. Pak vyberte **Under H – SAP HANA Platform Edition** > **SAP HANA Platform Edition 2.0** > **Instalace**. Stáhněte si soubory zobrazené na následujícím snímku obrazovky.

   ![Snímek obrazovky se staženým souborem](./media/hana-installation/image16_download_hana.PNG)

2. V tomto příkladu jsme stáhli instalační balíčky SAP HANA 2.0. Na virtuálním počítači Azure jump box rozbalte samorozbalovací archivy do adresáře, jak je znázorněno níže.

   ![Snímek obrazovky s archivem samorozbalovacího](./media/hana-installation/image17_extract_hana.PNG)

3. Při extrahování archivů zkopírujte adresář vytvořený extrakcí (v tomto případě 51052030). Zkopírujte adresář z jednotky velké instance HANA /hana/sdílený svazek do adresáře, který jste vytvořili.

   > [!Important]
   > Nekopírujte instalační balíčky do kořenové nebo spouštěcí logické jednotky, protože místo je omezené a musí být použito i jinými procesy.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Instalace SAP HANA na jednotku velké instance HANA
Chcete-li nainstalovat SAP HANA, přihlaste se jako root uživatele. Pouze root má dostatečná oprávnění k instalaci SAP HANA. Nastavte oprávnění pro adresář, který jste zkopírovali do /hana/shared.

```
chmod –R 744 <Installation bits folder>
```

Pokud chcete nainstalovat SAP HANA pomocí nastavení grafického uživatelského rozhraní, balíček gtk2 musí být nainstalován na velké instance HANA. Chcete-li zkontrolovat, zda je nainstalován, spusťte následující příkaz:

```
rpm –qa | grep gtk2
```

(V pozdějších krocích zobrazíme nastavení SAP HANA s grafickým uživatelským rozhraním.)

Přejděte do instalačního adresáře a přejděte do dílčího adresáře HDB_LCM_LINUX_X86_64. 

Z tohoto adresáře začněte:

```
./hdblcmgui 
```
V tomto okamžiku probíhá prostřednictvím sekvence obrazovek, ve kterém zadáte data pro instalaci. V tomto příkladu instalujeme databázový server SAP HANA a klientské součásti SAP HANA. Proto náš výběr je **DATABÁZE SAP HANA**.

![Snímek obrazovky SAP HANA Lifecycle Management s vybranou databází SAP HANA](./media/hana-installation/image18_hana_selection.PNG)

Na další obrazovce vyberte **Instalovat nový systém**.

![Snímek obrazovky SAP HANA Lifecycle Management s vybranou možností Instalovat nový systém](./media/hana-installation/image19_select_new.PNG)

Dále vyberte mezi několika dalšími součástmi, které můžete nainstalovat.

![Snímek obrazovky SAP HANA Lifecycle Management se seznamem dalších součástí](./media/hana-installation/image20_select_components.PNG)

Zde vybíráme klienta SAP HANA a SAP HANA Studio. Také nainstalujeme instanci škálování. Pak zvolte **single-host system**. 

![Snímek obrazovky SAP HANA Lifecycle Management s vybranou možností Jeden hostitelský systém](./media/hana-installation/image21_single_host.PNG)

Dále zadejte některá data.

![Snímek obrazovky Sap HANA Lifecycle Management s poli vlastností systému, která mají být definována](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Jako HANA System ID (SID) musíte poskytnout stejné SID, jako jste poskytli společnosti Microsoft při objednání nasazení velké instance HANA. Výběr jiného souboru SID způsobí selhání instalace z důvodu problémů s oprávněním přístupu na různých svazcích.

Pro instalační cestu použijte adresář /hana/shared. V dalším kroku zadáte umístění pro datové soubory HANA a soubory protokolu HANA.


![Snímek obrazovky SAP HANA Lifecycle Management s poli dat a oblastí protokolu](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> SID, který jste zadali, když jste definovali vlastnosti systému (před dvěma obrazovkami), by měl odpovídat SID přípojných bodů. Pokud dojde k neshodě, vraťte se zpět a upravte sid na hodnotu, kterou máte na přípojných bodech.

V dalším kroku zkontrolujte název hostitele a případně jej opravte. 

![Snímek obrazovky SAP HANA Lifecycle Management s názvem hostitele](./media/hana-installation/image24_review_host_name.PNG)

V dalším kroku je také potřeba načíst data, která jste společnosti Microsoft při řazení velkého instance hana přiřadili. 

![Snímek obrazovky s programem SAP HANA Lifecycle Management s poli správce systému, která můžete definovat](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Zadejte stejné **ID uživatele správce systému** a **ID skupiny uživatelů,** které jste poskytli společnosti Microsoft při objednávce nasazení jednotky. V opačném případě se instalace SAP HANA na jednotce velké instance HANA nezdaří.

Další dvě obrazovky zde nejsou zobrazeny. Umožňují zadat heslo pro uživatele SYSTÉMU databáze SAP HANA a heslo pro uživatele sapadm. Ten se používá pro hostitele SAP Agent, který se nainstaluje jako součást instance databáze SAP HANA.

Po definování hesla se zobrazí obrazovka s potvrzením. zkontrolujte všechna uvedená data a pokračujte v instalaci. Dosáhnete obrazovky průběhu, která dokumentuje průběh instalace, jako je tato:

![Snímek obrazovky SAP HANA Lifecycle Management s indikátory průběhu instalace](./media/hana-installation/image27_show_progress.PNG)

Jak instalace končí, měli byste vidět obrazovku, jako je tato:

![Snímek obrazovky SAP HANA Lifecycle Management, který indikuje dokončení instalace](./media/hana-installation/image28_install_finished.PNG)

Instance SAP HANA by teď měla být v provozu a připravená k použití. Měli byste být schopni se k němu připojit ze STUDIA SAP HANA. Také se ujistěte, že jste zkontrolovali a použili nejnovější aktualizace.


## <a name="next-steps"></a>Další kroky

- [Sap HANA Velké instance vysoká dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md)


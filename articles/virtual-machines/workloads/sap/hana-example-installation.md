---
title: Jak nainstalovat HANA na SAP HANA v Azure (velké instance) | Microsoft Docs
description: Jak nainstalovat HANA na SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 59ea41254c16f32f19a67e7811177a4667a85e7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666703"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>Instalace HANA na SAP HANA v Azure (velké instance)

Pokud chcete nainstalovat HANA na SAP HANA v Azure (velké instance), musíte nejdřív udělat toto:
- Microsoftu poskytnete všechna data, která pro vás nasadíte na SAP HANA velké instanci.
- Od Microsoftu obdržíte SAP HANA velkou instanci.
- Vytvoříte virtuální síť Azure, která je připojená k vaší místní síti.
- Okruh ExpressRoute pro velké instance HANA připojíte ke stejné službě Azure Virtual Network.
- Pro velké instance HANA nainstalujete virtuální počítač Azure, který používáte jako pole s odkazem.
- Ujistěte se, že se můžete připojit z pole pro přesun do jednotky velkých instancí HANA a naopak.
- Kontrolujete, zda jsou nainstalovány všechny potřebné balíčky a opravy.
- Poznámky a dokumentace SAP k instalaci HANA si přečtete v operačním systému, který používáte. Ujistěte se, že verze pro HANA je ve verzi operačního systému podporovaná.

V další části se zobrazuje příklad Stažení instalačních balíčků HANA do virtuálního počítače se seznamem odkazů. V tomto případě je operačním systémem Windows.

## <a name="download-the-sap-hana-installation-bits"></a>Stažení SAP HANAch instalačních bitů
Jednotky velkých instancí HANA nejsou přímo připojené k Internetu. Nemůžete přímo stáhnout instalační balíčky z SAP do virtuálního počítače velké instance HANA. Místo toho tyto balíčky stahujete do virtuálního počítače se seznamem odkazů.

Potřebujete SAP S – uživatel nebo jiný uživatel, který vám umožní přístup k webu SAP Marketplace.

1. Přihlaste se a přečtěte si [Web SAP Service Marketplace](https://support.sap.com/en/index.html). Vyberte možnost **Stáhnout**  >  **Instalace softwaru a upgradovat**  >  **podle abecedního indexu**. Pak vyberte **v části H – SAP HANA Platform Edition**  >  **SAP HANA Platform Edition 2,0**  >  **instalace**. Stáhněte si soubory zobrazené na následujícím snímku obrazovky.

   ![Snímek obrazovky se soubory, které se mají stáhnout](./media/hana-installation/image16_download_hana.PNG)

2. V tomto příkladu jsme stáhli instalační balíčky SAP HANA 2,0. Na virtuálním počítači s odkazem na Azure rozbalte samorozbalovací archivy do adresáře, jak je znázorněno níže.

   ![Snímek obrazovky s automatickou extrakcí archivu](./media/hana-installation/image17_extract_hana.PNG)

3. Po extrahování archivů zkopírujte adresář vytvořený extrakcí (v tomto případě 51052030). Zkopírujte adresář ze svazku/Hana/Shared velké instance služby HANA do vytvořeného adresáře.

   > [!Important]
   > Instalační balíčky nekopírujte do kořenové nebo spouštěcí logické jednotky (LUN), protože místo je omezené a musí je používat i jiné procesy.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Nainstalovat SAP HANA jednotky velkých instancí HANA
Chcete-li nainstalovat SAP HANA, přihlaste se jako uživatel root. Pouze kořenový adresář má dostatečná oprávnění pro instalaci SAP HANA. Nastavení oprávnění pro adresář, který jste zkopírovali do/Hana/Shared.

```
chmod –R 744 <Installation bits folder>
```

Pokud chcete nainstalovat SAP HANA pomocí nastavení grafického uživatelského rozhraní, musí být balíček GTK2 nainstalovaný na velkých instancích HANA. Chcete-li ověřit, zda je nainstalován, spusťte následující příkaz:

```
rpm –qa | grep gtk2
```

(V pozdějších krocích ukážeme nastavení SAP HANA s grafickým uživatelským rozhraním.)

Přejděte do instalačního adresáře a přejděte do podadresáře HDB_LCM_LINUX_X86_64. 

Z tohoto adresáře spusťte:

```
./hdblcmgui 
```
V tuto chvíli budete postupovat podle posloupnosti obrazovek, ve kterých zadáte data pro instalaci. V tomto příkladu nainstalujeme Server SAP HANA Database a součásti SAP HANA klienta. Proto je náš výběr **SAP HANA databáze**.

![Obrazovka obrazovky správy životního cyklu SAP HANA s vybranou databází SAP HANA](./media/hana-installation/image18_hana_selection.PNG)

Na další obrazovce vyberte **nainstalovat nový systém**.

![Obrazovka obrazovky správy životního cyklu SAP HANA s vybraným nástrojem nainstalovat nový systém](./media/hana-installation/image19_select_new.PNG)

Dále vyberte několik dalších součástí, které lze nainstalovat.

![Obrazovka obrazovky správy životního cyklu SAP HANA se seznamem dalších komponent](./media/hana-installation/image20_select_components.PNG)

Tady zvolíme klienta SAP HANA a SAP HANA Studio. Nainstalujeme také instanci škálování. Pak zvolte **systém s jedním hostitelem**. 

![Obrazovka obrazovky správy životního cyklu SAP HANA s vybraným jediným hostitelským systémem](./media/hana-installation/image21_single_host.PNG)

Dále zadejte nějaká data.

![Obrazovka obrazovky správy životního cyklu SAP HANA s poli vlastnosti systému, která se má definovat](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Jako ID systému HANA (SID) je nutné zadat stejné SID jako při objednání vysokého nasazení instance HANA, jako jste zadali Microsoft. Výběr jiného identifikátoru SID způsobí selhání instalace kvůli problémům s oprávněním k přístupu na různých svazcích.

Pro cestu k instalaci použijte adresář/Hana/Shared. V dalším kroku zadáte umístění datových souborů HANA a souborů protokolu HANA.


![Obrazovka obrazovky správy životního cyklu SAP HANA s poli data a oblasti protokolu](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Identifikátor SID, který jste zadali při definování vlastností systému (před oběma obrazovkami), by se měl shodovat s identifikátorem SID přípojných bodů. Pokud dojde k neshodě, vraťte se zpátky a upravte SID na hodnotu, kterou máte na přípojných bodech.

V dalším kroku zkontrolujte název hostitele a nakonec ho opravte. 

![Obrazovka obrazovky správy životního cyklu SAP HANA s názvem hostitele](./media/hana-installation/image24_review_host_name.PNG)

V dalším kroku budete také potřebovat načíst data, která jste zadali společnosti Microsoft při objednání vysokého nasazení instance HANA. 

![Snímek obrazovky SAP HANA správy životního cyklu s poli správce systému k definování](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Zadejte stejné ID a **ID** **uživatele správce systému** jako při nasazování jednotky, které jste zadali společnosti Microsoft. V opačném případě selže instalace SAP HANA jednotky velkých instancí HANA.

Následující dvě obrazovky tady nejsou uvedené. Umožňují zadat heslo pro uživatele systému SAP HANA databáze a heslo pro uživatele sapadm. Ta se používá pro agenta hostitele SAP, který se nainstaluje jako součást instance databáze SAP HANA.

Po definování hesla se zobrazí potvrzovací obrazovka. Ověřte všechna uvedená data a pokračujte v instalaci. Dostanete obrazovku s průběhem, která dokumentuje průběh instalace, jako je tato:

![Obrazovka obrazovky správy životního cyklu SAP HANA s indikátory průběhu instalace](./media/hana-installation/image27_show_progress.PNG)

Po dokončení instalace by se měla zobrazit obrazovka podobná této:

![Obrazovka obrazovky správy životního cyklu SAP HANA, která značí dokončení instalace](./media/hana-installation/image28_install_finished.PNG)

Instance SAP HANA by teď měla být spuštěná a připravená k použití. Měli byste být schopni se k němu připojit z SAP HANA studia. Ujistěte se také, že jste kontrolovali a použili nejnovější aktualizace.


## <a name="next-steps"></a>Další kroky

- [Velké instance SAP HANA vysoká dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md)


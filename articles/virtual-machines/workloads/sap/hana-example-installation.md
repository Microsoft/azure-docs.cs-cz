---
title: Postup instalace HANA na systému SAP HANA v Azure (velké instance) | Dokumentace Microsoftu
description: Postup instalace HANA na systému SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f4629894933507bda7359fb034c4079d38100029
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50231402"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>Nainstalujte HANA na systému SAP HANA v Azure (velké instance)

Pokud chcete nainstalovat HANA na systému SAP HANA v Azure (velké instance), musí nejprve provedete následující:
- Microsoft se poskytují všechna data nasazení za vás ve velké Instance SAP HANA.
- Velká Instance SAP HANA jste dostali z Microsoftu.
- Vytvoříte virtuální síť Azure, který je připojený k vaší místní síti.
- Okruh ExpressRoute pro velké instance HANA připojíte do stejné virtuální síti Azure.
- Je-li nainstalovat virtuální počítač Azure, který používáte jako jump box pro velké instance HANA.
- Zajistíte tím, že můžete připojit z jump box do jednotky velká Instance HANA a naopak.
- Můžete zkontrolovat, zda jsou nainstalovány všechny potřebné balíčky a opravy.
- Si přečíst poznámky SAP a dokumentaci ke službě HANA instalace operačního systému, které používáte. Ujistěte se, že verze HANA podle výběru se podporuje na verzi operačního systému.

Dalším oddílu si ukážeme příklad stahování instalačních balíčků HANA k virtuálnímu počítači jump box. V tomto případě je operační systém Windows.

## <a name="download-the-sap-hana-installation-bits"></a>Stáhněte si bity instalace SAP HANA
Velká Instance HANA jednotek nejsou připojené přímo k Internetu. Nelze stáhnout přímo instalační balíčky ze SAP do virtuálního počítače pro velká Instance HANA. Místo toho stáhne balíčky do virtuálního počítače jump box.

Budete potřebovat SAP S uživatelem nebo jiný uživatel, který umožňuje přístup k SAP web Marketplace.

1. Přihlaste se a přejděte na [SAP Service Marketplace](https://support.sap.com/en/index.html). Vyberte **stáhnout Software** > **instalace a Upgrade** > **podle abecední rejstřík**. Potom vyberte **pod H – edice platformy SAP HANA** > **2.0 edice platformy SAP HANA** > **instalace**. Stažení souborů je znázorněno na následujícím snímku obrazovky.

   ![Snímek obrazovky se soubory ke stažení.](./media/hana-installation/image16_download_hana.PNG)

2. V tomto příkladu jsme stáhli instalační balíčky SAP HANA 2.0. Na Azure jump box virtuální počítač, rozbalte samorozbalovací archivy do adresáře, jak je znázorněno níže.

   ![Snímek obrazovky samorozbalovací archivu](./media/hana-installation/image17_extract_hana.PNG)

3. Při extrahování archivy zkopírujte adresář, vytvoří extrakcí (v tomto případě 51052030). Zkopírujte adresář do adresáře, který jste vytvořili ze svazku jednotky /hana/shared velká Instance HANA.

   > [!Important]
   > Nekopírujte instalační balíčky do kořenového adresáře nebo spouštěcí logickou jednotku, protože místo je omezený a musí používat i jiné procesy.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Instalace SAP HANA na jednotce velká Instance HANA
Abyste mohli nainstalovat SAP HANA, přihlaste se jako uživatel root. Pouze root má dostatečná oprávnění k instalaci SAP HANA. Nastavení oprávnění na adresáři, který se zkopíruje do /hana/shared.

```
chmod –R 744 <Installation bits folder>
```

Pokud chcete nainstalovat SAP HANA pomocí instalačního programu grafické uživatelské rozhraní, musí být nainstalovaný na velkých instancích HANA gtk2 balíčku. Pokud chcete zkontrolovat, zda je nainstalována, spusťte následující příkaz:

```
rpm –qa | grep gtk2
```

(V dalších krocích, vám ukážeme, SAP HANA instalaci přes grafické uživatelské rozhraní.)

Přejděte do instalačního adresáře a přejděte do podadresáře HDB_LCM_LINUX_X86_64. 

Mimo tento adresář začněte:

```
./hdblcmgui 
```
AT tento bod, průběh řadou obrazovek, které poskytují data pro instalaci. V tomto příkladu jsme instalaci databázového serveru SAP HANA a klientské komponenty SAP HANA. Proto je naše výběr **databáze SAP HANA**.

![Snímek obrazovky ze SAP HANA správy životního cyklu obrazovku vybrané databáze SAP HANA](./media/hana-installation/image18_hana_selection.PNG)

Na další obrazovce vyberte **instalace nového systému**.

![Snímek obrazovky ze SAP HANA správy životního cyklu obrazovku instalace nového systému vybrané](./media/hana-installation/image19_select_new.PNG)

V dalším kroku vyberte mezi několik dalších komponent, které můžete nainstalovat.

![Snímek obrazovky ze SAP HANA správy životního cyklu obrazovky se seznamem další součásti](./media/hana-installation/image20_select_components.PNG)

Jsme zde, zvolte klienta SAP HANA a SAP HANA Studio. Můžeme také instalaci instance vertikálně navýšit kapacitu. Klikněte na tlačítko **jednoho hostitele systému**. 

![Snímek obrazovky ze SAP HANA správy životního cyklu obrazovky pomocí jednoho hostitele systému vybrané](./media/hana-installation/image21_single_host.PNG)

Pak zadejte nějaká data.

![Snímek obrazovky ze SAP HANA správy životního cyklu obrazovka s vlastností systémová pole k definování](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Jako HANA System Identifikátor (SID), je nutné zadat stejný identifikátor SID, jako jste zadali Microsoft, když jste si objednali velká Instance HANA nasazení. Zvolíte jiné SID způsobí, že instalace selhat z důvodu problémů s přístupem k oprávnění v různých svazcích.

Pro cestu instalace použití /hana/shared adresáře. V dalším kroku zadejte umístění pro HANA datové soubory a soubory protokolu HANA.


![Snímek obrazovky ze SAP HANA správy životního cyklu obrazovku pole v oblasti dat a protokolů](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Identifikátor SID, které jste zadali při definování vlastností systému (před dvě obrazovky) by měl odpovídat SID přípojné body. Pokud dojde k neshodě, vraťte se a upravte identifikátor SID k hodnotě, kterou máte v přípojné body.

V dalším kroku zkontrolujte název hostitele a nakonec ho opravit. 

![Snímek obrazovky ze SAP HANA správy životního cyklu obrazovky s názvem hostitele](./media/hana-installation/image24_review_host_name.PNG)

V dalším kroku budete potřebovat k načtení dat, které dáte společnosti Microsoft, když jste si objednali velká Instance HANA nasazení. 

![Snímek obrazovky ze SAP HANA Správa životního cyklu, pomocí Správce systémová pole k definování](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Zadejte stejný **ID uživatele pro správce systému** a **ID skupiny uživatelů** poskytnete společnosti Microsoft, jako objednávky jednotku nasazení. V opačném případě se nezdaří instalace SAP HANA na jednotce velká Instance HANA.

Další dvě obrazovky se tady nezobrazují. Umožňují zadat heslo pro uživatele systému databáze SAP HANA a heslo pro uživatele sapadm. Druhá možnost se používá pro hostitele agenta SAP, který se nainstaluje jako součást instance databáze SAP HANA.

Po definování heslo, se zobrazí obrazovka s potvrzením. Zkontrolujte všechna data uvedená a pokračovat v instalaci. Obrazovka průběh dokumentující průběh instalace, například takto:

![Snímek obrazovky ze SAP HANA správy životního cyklu obrazovka s indikátory průběhu instalace](./media/hana-installation/image27_show_progress.PNG)

Při dokončení instalace by se zobrazit obrazovka podobný následujícímu:

![Snímek obrazovky ze SAP HANA správy životního cyklu obrazovky, určující instalace je dokončena](./media/hana-installation/image28_install_finished.PNG)

Instance SAP HANA nyní měli a funkční, připravené pro použití. Měli byste být schopni k němu připojit z SAP HANA Studio. Také se ujistěte, že vyhledejte a použijte nejnovější aktualizace.


## <a name="next-steps"></a>Další postup

- [Velké instance SAP HANA vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md)


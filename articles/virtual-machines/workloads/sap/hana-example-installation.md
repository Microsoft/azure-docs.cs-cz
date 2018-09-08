---
title: Postup instalace HANA na systému SAP HANA v Azure (velké instance) | Dokumentace Microsoftu
description: Postup instalace HANA na systému SAP HANA v Azure (velká Instance).
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
ms.openlocfilehash: 76a7ce99799b85d81aa6e127ebe1e57e2df3e59a
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164706"
---
# <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>Příklad instalace SAP HANA ve velkých instancích HANA

Tato část ukazuje, jak nainstalovat na jednotce velká Instance HANA SAP HANA. Počáteční stav, který máme k dispozici vypadat takto:

- Microsoft poskytuje všechna data nasazení velké Instance SAP HANA.
- Velká Instance SAP HANA jste dostali od Microsoftu.
- Vytvoříte virtuální síť Azure, který je připojený k vaší místní síti.
- Připojení okruhu ExpressRotue pro velké instance HANA ve stejné virtuální síti Azure.
- Nainstalovali jste virtuální počítač Azure použijete jako jump box pro velké instance HANA.
- Provedené jistotu, že se můžete připojit z jump box jednotky velká Instance HANA a naopak.
- Jste vrátili se, zda jsou nainstalovány všechny potřebné balíčky a opravy.
- Čtení poznámky SAP a dokumentace týkající se instalace HANA na operační systém používáte a zkontrolujte, že je podporována verze HANA podle výběru v operačním systému verze.

Jak je zobrazeno v dalším pořadí je ke stažení instalačních balíčků HANA k systému jump box virtuálního počítače, v tomto případě spuštěný v OS Windows, zkopírujte balíčky k jednotce velká Instance HANA a pořadí instalace.

## <a name="download-of-the-sap-hana-installation-bits"></a>Časový limit stažení bity instalace SAP HANA
Protože velká Instance HANA jednotky nemají přímé připojení k Internetu, nelze stáhnout přímo instalační balíčky ze SAP do virtuálních počítačů velké Instance HANA. K překonání chybějící přímé připojení k Internetu, musíte jump box. Stáhnout balíčky k systému jump box virtuálního počítače.

Pokud chcete stáhnout instalační balíčky HANA, musíte SAP S uživatelem nebo jiný uživatel, který umožňuje přístup k SAP web Marketplace. Projděte si toto pořadí obrazovek po přihlášení:

Přejděte na [SAP Service Marketplace](https://support.sap.com/en/index.html) > klikněte na možnost stažení softwaru > Instalace a Upgrade > podle abecední rejstřík > pod H – SAP HANA platformy Edition > SAP HANA platformy verze 2.0 > instalace > stáhnout Následující soubory

![Stažení a instalace HANA](./media/hana-installation/image16_download_hana.PNG)

V případě ukázku stáhli instalační balíčky SAP HANA 2.0. V systému Azure jump box virtuálního počítače rozbalte samorozbalovací archivy do adresáře jak je znázorněno níže.

![Extrahovat HANA instalace](./media/hana-installation/image17_extract_hana.PNG)

Při extrahování archivy zkopírujte adresář, vytvoří pomocí extrakce k HANA velké instance jednotky do svazku /hana/shared do adresáře, který jste vytvořili v případě výše 51052030.

> [!Important]
> Nekopírovat instalační balíčky do kořenového adresáře nebo spouštěcí logickou jednotku, protože místo je omezený a musí být používán jinými procesy také.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Instalace SAP HANA na jednotce velká Instance HANA
Abyste mohli nainstalovat SAP HANA, musíte se přihlásit jako uživatel root. Pouze root má dostatečná oprávnění k instalaci SAP HANA.
Je první věcí, kterou je potřeba nastavit oprávnění pro adresář, který se zkopíruje do/hana/sdílené. Oprávnění je potřeba nastavit jako

```
chmod –R 744 <Installation bits folder>
```

Pokud chcete nainstalovat SAP HANA pomocí grafický instalační program, musí být nainstalovaný na velkých instancích HANA gtk2 balíčku. Zkontrolujte, zda je nainstalována pomocí příkazu

```
rpm –qa | grep gtk2
```

V dalších krocích jsme se ukázka instalace SAP HANA pomocí grafického uživatelského rozhraní. Jako další krok přejděte do instalačního adresáře a přejděte do podadresáře HDB_LCM_LINUX_X86_64. Start

```
./hdblcmgui 
```
z tohoto adresáře. Nyní získávání projdete pořadí obrazovek, které je potřeba zadat data pro instalaci. V případě jsme vám ukázali jsme instalaci databázového serveru SAP HANA a klientské komponenty SAP HANA. Proto naši výběr je "Databáze SAP HANA" jak je znázorněno níže

![Vyberte HANA v instalaci](./media/hana-installation/image18_hana_selection.PNG)

Na další obrazovce zvolíte možnost instalace nového systému

![Vyberte novou instalaci HANA](./media/hana-installation/image19_select_new.PNG)

Po provedení tohoto kroku je potřeba vybrat mezi několik dalších komponent, které lze nainstalovat navíc k databázovému serveru SAP HANA.

![Vyberte další součásti HANA](./media/hana-installation/image20_select_components.PNG)

Pro účely této dokumentace Rozhodli jsme se pro SAP HANA Client a SAP HANA Studio. Také jsme nainstalovali instanci vertikálně navýšit kapacitu. proto na další obrazovce, musíte zvolit "jednoho hostitele systému. 

![Vyberte možnost instalace vertikálně navýšit kapacitu](./media/hana-installation/image21_single_host.PNG)

Na další obrazovce je potřeba zadat některá data

![Zadejte identifikátor SID SAP HANA](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Jako HANA System Identifikátor (SID), budete muset zadat stejný identifikátor SID, jako jste zadali Microsoft, když jste si objednali velká Instance HANA nasazení. Zvolíte jiné SID díky selhat z důvodu problémů s přístupem k oprávnění v různých svazcích instalací

Instalace sdílený adresář použijete/hana nebo adresář. V dalším kroku je potřeba zadat umístění pro HANA datové soubory a soubory protokolu HANA


![Zadejte umístění protokolu HANA](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> By měl definovat jako data a svazky, které již dodán jako součást přípojné body, které obsahují SID, kterou jste zvolili v výběr obrazovky před Tato obrazovka soubory protokolu. Pokud identifikátor SID neshoda, který jste zadali, v dialogovém okně před přejděte zpět a upravte identifikátor SID k hodnotě, kterou máte v přípojné body.

V dalším kroku zkontrolujte název hostitele a nakonec ho opravit. 

![Zkontrolujte název hostitele](./media/hana-installation/image24_review_host_name.PNG)

V dalším kroku budete potřebovat k načtení dat, které dáte společnosti Microsoft, když jste si objednali velká Instance HANA nasazení. 

![Zadejte uživatele systému UID a ID skupiny](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Je třeba zadat stejného ID uživatele systému a ID skupiny uživatelů, jako jste zadali jako pořadí nasazení jednotek Microsoft. Pokud chcete poskytnout stejné ID, SAP Hana na jednotce velká Instance HANA se nezdaří.

V další dvě obrazovky, které jsme se nezobrazují v této dokumentaci, budete muset zadat heslo pro uživatele systému databáze SAP HANA a heslo pro uživatele sapadm, který se používá pro hostitele agenta SAP, který se nainstaluje jako součást datab SAP HANA instance služby ase.

Po definování heslo, se zobrazuje obrazovka s potvrzením, protože. Zkontrolujte všechna data uvedená a pokračovat v instalaci. Průběh na obrazovce, dokumentující průběh instalace, jako je ten níže

![Zkontrolujte průběh instalace](./media/hana-installation/image27_show_progress.PNG)

Když instalace dokončí, měli byste jako na následujícím obrázku

![Po dokončení instalace](./media/hana-installation/image28_install_finished.PNG)

Instance SAP HANA v tomto okamžiku by měl být připravené a spuštěné a budete připraveni za využití. Měli byste být schopni k němu připojit z SAP HANA Studio. Také se ujistěte, že zkontrolujte nejnovější opravy SAP HANA a použít tyto opravy.


**Další kroky**

- Přečtěte si [velké instance SAP HANA vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).


---
title: Instalace prostředí IBM zD&T pro vývoj a testování v Azure | Dokumenty společnosti Microsoft
description: Nasazení vývojového a testovacího prostředí IBM Z (zD&T) na infrastruktuře virtuálních strojů (VM) Azure jako služby (IaaS).
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 04/02/2019
tags: ''
keywords: ''
ms.openlocfilehash: 67af4eae03b773fad9cf38964152c1fb9d623bd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025937"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Instalace prostředí IBM zD&T pro vývoj a testování v Azure

Chcete-li vytvořit vývojové a testovací prostředí pro úlohy sálových počítačů v ibm Z Systems, můžete nasadit vývojové a testovací prostředí IBM Z (zD&T) na infrastrukturu Virtuálního počítače (VM) Azure jako službu (IaaS).

S zD&T můžete využít úspory nákladů platformy x86 pro méně kritická vývojová a testovací prostředí a pak posunout aktualizace zpět do produkčního prostředí systému Z. Další informace naleznete v [pokynech k instalaci ibm ZD&T](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL).

Azure a Azure Stack podporují následující verze:

- zD&T Osobní Edice
- zD&T Paralelní Sysplex
- zD&T Enterprise Edition

Všechny edice zD&T běží pouze na systémech x86 Linux, ne na Windows Serveru. Enterprise Edition je podporována buď na Red Hat Enterprise Linux (RHEL) nebo Ubuntu/Debian. Obrázky virtuálních počítačů RHEL i Debian jsou dostupné pro Azure.

Tento článek ukazuje, jak nastavit zD&T Enterprise Edition v Azure, takže můžete použít zD&T Enterprise Edition webový server pro vytváření a správu prostředí. Instalace zD&T nenainstaluje žádná prostředí. Je nutné vytvořit samostatně jako instalační balíčky. Například distribuce řízené vývojáři aplikací (ADCD) jsou obrázky svazků testovacích prostředí. Jsou obsaženy v zip obrázky na distribuci médií k dispozici od IBM. Podívejte se, jak [nastavit prostředí ADCD v Azure](demo.md).

Další informace naleznete [v přehledu zD&T](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) v centru IBM Knowledge Center.

Tento článek ukazuje, jak nastavit Z vývojové a testovací prostředí (zD&T) Enterprise Edition v Azure. Pak můžete použít webový server zD&T Enterprise Edition k vytváření a správě prostředí založených na Z v Azure.

## <a name="prerequisites"></a>Požadavky

> [!NOTE]
> IBM umožňuje instalaci zD&T Enterprise Edition pouze v testovacích prostředích,*nikoli* v produkčním prostředí.

- Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Potřebujete přístup k médiím, která jsou k dispozici pouze zákazníkům a partnerům IBM. Další informace získáte od zástupce IBM nebo se obraťte na kontaktní informace na webu [zD&T.](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment)

- [Licenční server](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). To je nutné pro přístup k prostředí. Způsob, jakým jej vytvoříte, závisí na tom, jak software licencujete od IBM:

     - **Hardwarový licenční server** vyžaduje hardwarové zařízení USB, které obsahuje rational tokeny nezbytné pro přístup ke všem částem softwaru. Tuto položku musíte získat od IBM.

     - **Licenční server založený na softwaru** vyžaduje, abyste nastavili centralizovaný server pro správu licenčních klíčů. Tato metoda je upřednostňována a vyžaduje nastavení klíčů, které obdržíte od IBM na serveru pro správu.

## <a name="create-the-base-image-and-connect"></a>Vytvoření základního obrazu a připojení

1. Na webu Azure Portal [vytvořte virtuální počítač](/azure/virtual-machines/linux/quick-create-portal) s požadovanou konfigurací operačního systému. Tento článek předpokládá Virtuální ms B4ms (se 4 virtuálními procesory a 16 GB paměti) se systémem Ubuntu 16.04.

2. Po vytvoření virtuálního virtuálního montovny otevřete příchozí porty 22 pro SSH, 21 pro FTP a 9443 pro webový server.

3. Získejte pověření SSH zobrazené na **okno Přehled** virtuálního počítači pomocí tlačítka **Připojit.** Vyberte kartu **SSH** a zkopírujte přihlašovací příkaz SSH do schránky.

4. Přihlaste se k [prostředí Bash](/azure/cloud-shell/quickstart) z místního počítače a vložte příkaz. Bude ve formě **ssh\<user\>\@\<id\>IP adresa**. Po zobrazení výzvy k zadání pověření je zadejte a vytvořte připojení k domovskému adresáři.

## <a name="copy-the-installation-file-to-the-server"></a>Zkopírování instalačního souboru na server

Instalační soubor pro webový server je **ZDT\_Install\_EE\_V12.0.0.1.tgz**. Je součástí médií dodávaných společností IBM. Tento soubor musíte nahrát do virtuálního počítače Ubuntu.

1. Z příkazového řádku zadejte následující příkaz, abyste se ujistili, že je v nově vytvořeném obrázku vše aktuální:

    ```
    sudo apt-get update
    ```

2. Vytvořte adresář, do který chcete nainstalovat:

    ```
    mkdir ZDT
    ```

3. Zkopírujte soubor z místního počítače do virtuálního počítače:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> Tento příkaz zkopíruje instalační soubor do adresáře ZDT v domovském adresáři, který se liší v závislosti na tom, zda klient spustí systém Windows nebo Linux.

## <a name="install-the-enterprise-edition"></a>Instalace edice Enterprise Edition

1. Přejděte do adresáře ZDT a\_dekomprimujte soubor ZDT Install\_EE\_V12.0.0.1.tgz pomocí následujících příkazů:

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Spusťte instalační program:

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Výběrem **možnosti 1** nainstalujte podnikový server.

4. Stiskněte **klávesu Enter** a pečlivě si přečtěte licenční smlouvy. Na konci licence zadejte **Ano,** chcete-li pokračovat.

5. Po zobrazení výzvy ke změně hesla pro nově vytvořeného **uživatele, ibmsys1**, použijte příkaz **sudo passwd ibmsys1** a zadejte nové heslo.

6. Chcete-li ověřit, zda byla instalace úspěšná, zadejte

    ```
    dpkg -l | grep zdtapp
    ```

7. Ověřte, zda výstup obsahuje řetězec **zdtapp 12.0.0.0**, což znamená, že balík plynu byl úspěšně nainstalován

### <a name="starting-enterprise-edition"></a>Spuštění edice Enterprise

Mějte na paměti, že při spuštění webového serveru běží pod zD&ID uživatele T, které bylo vytvořeno během procesu instalace.

1. Chcete-li spustit webový server, spusťte pomocí kořenového ID uživatele následující příkaz:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. Zkopírujte výstup adresy URL skriptem, který vypadá takto:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. Vložením adresy URL do webového prohlížeče otevřete komponentu pro správu pro instalaci zD&T.

## <a name="next-steps"></a>Další kroky

[Nastavení distribuce řízené vývojáři aplikací (ADCD) v IBM zD&T v1](./demo.md)

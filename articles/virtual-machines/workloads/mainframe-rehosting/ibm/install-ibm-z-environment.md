---
title: Instalace IBM zD & T vývojovým/testovacím prostředím v Azure | Dokumentace Microsoftu
description: Nasazení IBM Z vývojové a testovací prostředí (zD & T) v Azure virtuální počítač (VM) infrastruktury jako služby (IaaS).
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 2f5520213e7d8792c89f5445d470987323173dc3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "58894521"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Instalace IBM zD & T vývojovým/testovacím prostředím v Azure

K vytvoření prostředí pro vývoj/testování pro úlohy sálové v systémech Z IBM, můžete nasadit IBM Z vývojové a testovací prostředí (zD & T) v Azure virtuální počítač (VM) infrastruktury jako služba (IaaS).

ZD & T, můžete využít výhod úspory nákladů x86 platforma pro méně důležité vývojová a testovací prostředí a potom aktualizace zpět do provozního prostředí Z systému nabízených oznámení. Další informace najdete v tématu [pokyny k instalaci IBM ZD & T](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL).

Azure a Azure Stackem podporují následující verze:

- zD & T osobní edice
- zD & T paralelní Sysplex
- zD&T Enterprise Edition

Všechny edice systémů zD & T spustit pouze na x86 systémů Linux, ne systému Windows Server. Enterprise Edition je podporována v Red Hat Enterprise Linux (RHEL) nebo Ubuntu nebo Debian. Image RHEL i virtuální počítač s Debian jsou k dispozici pro Azure.

V tomto článku se dozvíte, jak nastavit zD & T Enterprise Edition v Azure, abyste mohli používat zD & T Enterprise Edition webového serveru pro vytváření a správu prostředí. Instalace zD & T není možné nainstalovat žádné prostředí. Musíte je vytvořit samostatně jako instalační balíčky. Například aplikace vývojáři řídit distribuce (ADCD) jsou Image svazků testovacích prostředí. Jsou obsaženy v obrázcích zip na k dispozici od IBM distribuce multimédií. V tématu Jak [nastavit ADCD prostředí v Azure](demo.md).

Další informace najdete v tématu [zD & T přehled](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) v centru IBM znalostní báze.

V tomto článku se dozvíte, jak nastavit Z vývojové a testovací prostředí (zD & T) Enterprise Edition v Azure. Pak můžete použít zD & T Enterprise Edition webového serveru můžete vytvořit a spravovat Z prostředí v Azure.

## <a name="prerequisites"></a>Požadavky

> [!NOTE]
> IBM umožňuje zD & T Enterprise Edition má být nainstalován do vývojových/testovacích prostředí pouze –*není* produkční prostředí.

- Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Potřebujete přístup k médiím, která je dostupná jenom pro partnery a zákazníky IBM. Další informace vám poskytne zástupce IBM nebo zobrazit kontaktní informace na [zD & T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment) webu.

- A [licenční server](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). To je potřeba pro přístup k prostředí. Když vytváříte závisí na jak licenci k softwaru od IBM:

     - **Hardwarové licenční server** vyžaduje hardwarové zařízení USB, který obsahuje potřebné pro přístup k všechny části softwaru racionální tokeny. To je nutné získat od IBM.

     - **Softwarový licenční server** vyžaduje, abyste nastavení centralizovaných serveru pro správu licencování klíče. Tato metoda je upřednostňované a je potřeba nastavit klíče, který jste dostali od IBM v serveru pro správu.

## <a name="create-the-base-image-and-connect"></a>Vytvoření základní image a připojení

1. Na webu Azure portal [vytvoření virtuálního počítače](/azure/virtual-machines/linux/quick-create-portal) s konfigurace operačního systému, kterou chcete. Tento článek předpokládá B4ms virtuálního počítače (s 4 virtuální procesory a 16 GB paměti) systémem Ubuntu 16.04.

2. Po vytvoření virtuálního počítače, otevřete příchozí porty 22 pro SSH, 21 pro FTP a 9443 pro webový server.

3. Získat přihlašovací údaje SSH je vidět na **přehled** okno virtuálního počítače přes **připojit** tlačítko. Vyberte **SSH** kartu a zkopírujte tento příkaz přihlášení SSH do schránky.

4. Přihlaste se k [prostředí Bash](/azure/cloud-shell/quickstart) z místního počítače a vložte příkaz. Bude ve formuláři **ssh\<id uživatele\>\@\<IP adresu\>**. Po zobrazení výzvy k zadání pověření, zadejte je k navázání připojení k domovský adresář.

## <a name="copy-the-installation-file-to-the-server"></a>Zkopírujte instalační soubor na server

Instalační soubor pro webový server je **ZDT\_nainstalovat\_EE\_V12.0.0.1.tgz**. Je součástí média, získáte ho od IBM. Tento soubor musíte nahrát do vašeho virtuálního počítače s Ubuntu.

1. Z příkazového řádku zadejte následující příkaz, abyste měli jistotu, že všechno je v aktuálním stavu na nově vytvořený obrázku:

    ```
    sudo apt-get update
    ```

2. Vytvořte adresář pro instalaci na:

    ```
    mkdir ZDT
    ```

3. Kopírování souboru z místního počítače k virtuálnímu počítači:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> Tento příkaz zkopíruje instalační soubor do adresáře na ZDT domovský adresář, který se liší v závislosti na tom, zda spuštěný klient Windows nebo Linux.

## <a name="install-the-enterprise-edition"></a>Instalace Enterprise Edition

1. Přejděte do adresáře ZDT a dekomprimovat ZDT\_nainstalovat\_EE\_V12.0.0.1.tgz souboru pomocí následujících příkazů:

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Spusťte instalační program:

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Vyberte **1** instalace serveru pro organizace.

4. Stisknutím klávesy **Enter** a pečlivě si přečtěte licenční smlouvy. Na konci licence, zadejte **Ano** pokračovat.

5. Po zobrazení výzvy ke změně hesla pro nově vytvořený uživatel **ibmsys1**, použijte příkaz **sudo hesel ibmsys1** a zadejte nové heslo.

6. Chcete-li ověřit, jestli byla instalace úspěšná zadejte

    ```
    dpkg -l | grep zdtapp
    ```

7. Ověřte, že výstup obsahuje řetězec **zdtapp 12.0.0.0**, která udává, plynu balíček byl úspěšně nainstalován.

### <a name="starting-enterprise-edition"></a>Od verze Enterprise Edition

Uvědomte si, že při spuštění webový server běží pod zD & T ID uživatele, který byl vytvořen během instalace.

1. Pokud chcete spustit webový server, použijte kořenové ID uživatele a spusťte následující příkaz:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. Zkopírujte adresu URL výstup skript, který bude vypadat takto:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. Vložte adresu URL do webového prohlížeče otevřete součásti správy pro zD & T instalace.

## <a name="next-steps"></a>Další postup

[Nastavte si aplikace vývojáři řídit distribuce (ADCD) v IBM zD & T v1](./demo.md)

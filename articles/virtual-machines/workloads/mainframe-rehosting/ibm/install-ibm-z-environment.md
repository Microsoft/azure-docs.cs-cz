---
title: Instalace IBM zD&T vývoj/test prostředí v Azure | Microsoft Docs
description: Nasazení vývojového a testovacího prostředí IBM Z (zD&T) na infrastruktuře virtuálních počítačů Azure jako službu (IaaS).
services: virtual-machines
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 04/02/2019
tags: ''
keywords: ''
ms.openlocfilehash: 32b63256f89d6d051305890c3387140f243e1a70
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954322"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Instalace IBM zD&T dev/test Environment v Azure

Chcete-li vytvořit vývojové a testovací prostředí pro úlohy v sálových počítačích v systémech IBM Z, můžete nasadit vývojové a testovací prostředí od IBM Z (zD&T) na infrastruktuře virtuálních počítačů Azure jako službu (IaaS).

Díky zD&T můžete využít úspory nákladů platformy x86 pro méně kritická vývojová a testovací prostředí a pak aktualizace odeslat zpět do produkčního prostředí Z systému. Další informace najdete v [pokynech k instalaci aplikace IBM ZD&T](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL).

Azure a Azure Stack podporují následující verze:

- zD&T Personal Edition
- zD&T – paralelní Sysplex
- zD&T Enterprise Edition

Všechny edice zD&T běží jenom na systémech x86 Linux, ne na Windows serveru. Edice Enterprise je podporovaná buď na Red Hat Enterprise Linux (RHEL), nebo v Ubuntu/Debian. K dispozici jsou image virtuálních počítačů RHEL i Debian pro Azure.

V tomto článku se dozvíte, jak nastavit zD&T Enterprise Edition v Azure, abyste mohli vytvořit a spravovat prostředí pomocí webového serveru zD&T Enterprise Edition. Instalace zD&T neinstaluje žádná prostředí. Je nutné je vytvořit samostatně jako instalační balíčky. Například vývojáři aplikací řízené distribuce (ADCD) jsou image svazků testovacích prostředí. Jsou obsaženy v obrázcích zip na distribuci médií dostupné od společnosti IBM. Podívejte se, jak [nastavit prostředí ADCD v Azure](demo.md).

Další informace najdete v [přehledu zD&T](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) v centru Knowledge Center společnosti IBM.

V tomto článku se dozvíte, jak v Azure nastavit vývojové a testovací prostředí Z verze (zD&T) Enterprise Edition. Pak můžete použít webový server zD&T Enterprise Edition k vytváření a správě prostředí Z na platformě Azure.

## <a name="prerequisites"></a>Předpoklady

> [!NOTE]
> IBM umožňuje instalovat zD&T Enterprise Edition pouze do prostředí pro vývoj a testování,*nikoli* v produkčních prostředích.

- Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Potřebujete přístup k médiu, které je k dispozici pouze pro zákazníky a partnery společnosti IBM. Pokud chcete získat další informace, obraťte se na zástupce IBM nebo na webu [zD&T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment) kontaktujte kontaktní údaje.

- [Licenční server](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Tato oprávnění se vyžadují pro přístup k prostředím. Způsob, jakým ho vytvoříte, závisí na tom, jak si software od IBM vydáte.

     - **Hardwarový licenční server** vyžaduje hardwarové zařízení USB, které obsahuje racionální tokeny potřebné pro přístup ke všem částem softwaru. Musíte ho získat od společnosti IBM.

     - **Server licencování na základě softwaru** vyžaduje, abyste si nastavili centralizovaný Server pro správu licenčních klíčů. Tato metoda je upřednostňovaná a vyžaduje, abyste nastavili klíče, které dostanete od IBM v management server.

## <a name="create-the-base-image-and-connect"></a>Vytvoření základní image a připojení

1. V Azure Portal [vytvořte virtuální počítač](../../../linux/quick-create-portal.md) s konfigurací operačního systému, který chcete. V tomto článku se předpokládá, že virtuální počítač B4ms (se 4 vCPU a 16 GB paměti) se systémem Ubuntu 16,04.

2. Po vytvoření virtuálního počítače otevřete příchozí porty 22 pro SSH, 21 pro FTP a 9443 pro webový server.

3. Pomocí tlačítka **připojit** získáte přihlašovací údaje SSH zobrazené v okně **Přehled** virtuálního počítače. Vyberte kartu **SSH** a zkopírujte přihlašovací příkaz SSH do schránky.

4. Přihlaste se k [prostředí bash](../../../../cloud-shell/quickstart.md) z místního počítače a vložte příkaz. Bude ve formě **SSH \<user id\> \@ \<IP Address\>**. Až se zobrazí výzva k zadání přihlašovacích údajů, zadejte je, abyste navázali připojení k domovskému adresáři.

## <a name="copy-the-installation-file-to-the-server"></a>Zkopírujte instalační soubor na server.

Instalační soubor pro webový server je **ZDT \_ install \_ EE \_ v 12.0.0.1. tgz**. Je součástí médií dodaných společností IBM. Tento soubor musíte nahrát na virtuální počítač s Ubuntu.

1. V příkazovém řádku zadejte následující příkaz, abyste se ujistili, že je vše v nově vytvořené imagi aktuální:

    ```
    sudo apt-get update
    ```

2. Vytvořte adresář, do kterého chcete nainstalovat:

    ```
    mkdir ZDT
    ```

3. Zkopírujte soubor z místního počítače do virtuálního počítače:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> Tento příkaz zkopíruje instalační soubor do adresáře ZDT v domovském adresáři, který se liší v závislosti na tom, zda klient používá systém Windows nebo Linux.

## <a name="install-the-enterprise-edition"></a>Instalace edice Enterprise

1. Do adresáře ZDT a dekomprimujete \_ soubor ZDT Install \_ EE \_ v 12.0.0.1. tgz pomocí následujících příkazů:

    ```
    cd ZDT
    tar zxvf ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Spusťte instalační program:

    ```
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.x86_64
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Vyberte **1** pro instalaci podnikového serveru.

4. Stiskněte klávesu **ENTER** a pečlivě si přečtěte licenční smlouvy. Na konci licence Pokračujte zadáním **Ano** .

5. Po zobrazení výzvy ke změně hesla pro nově vytvořeného uživatele **ibmsys1** použijte příkaz **sudo passwd ibmsys1** a zadejte nové heslo.

6. Chcete-li ověřit, zda byla instalace úspěšná, zadejte

    ```
    dpkg -l | grep zdtapp
    ```

7. Ověřte, že výstup obsahuje řetězec **zdtapp 12.0.0.0**, což značí, že byl balíček balíčku úspěšně nainstalován.

### <a name="starting-enterprise-edition"></a>Od verze Enterprise Edition

Mějte na paměti, že při spuštění webového serveru běží pod ID uživatele zD&T, které bylo vytvořeno během procesu instalace.

1. Chcete-li spustit webový server, použijte ID uživatele root ke spuštění následujícího příkazu:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. Zkopírujte výstup adresy URL skriptem, který vypadá takto:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. Vložte adresu URL do webového prohlížeče a otevřete tak součást pro správu pro instalaci zD&T.

## <a name="next-steps"></a>Další kroky

[Nastavení vývojářů aplikací řízených distribucí (ADCD) v IBM zD&T V1](./demo.md)

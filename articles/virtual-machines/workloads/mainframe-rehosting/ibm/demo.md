---
title: Nastavte si aplikace vývojáři řídit distribuce (ADCD) v IBM zD & T v1 | Dokumentace Microsoftu
description: Spuštění IBM Z prostředí pro vývoj a testovací prostředí (zD & T) na Azure Virtual Machines (VM).
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: c6fcb345b49ce6354a24408ebe163fb928990252
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925695"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>Nastavte si aplikace vývojáři řídit distribuce (ADCD) v IBM zD & T v1

IBM Z prostředí pro vývoj a testovací prostředí (zD & T) můžete spustit v Azure Virtual Machines (VM). Toto prostředí emuluje architektura IBM Z řady. To může být hostitelem širokou škálu Z řady operačních systémů nebo zařízení (také nazývané Z instance nebo balíčky), které jsou k dispozici prostřednictvím vlastní svazky, které volá IBM aplikace vývojáři řídit distribuce (ADCDs).

V tomto článku se dozvíte, jak vytvořit instanci ADCD zD & T prostředí v Azure. Vytvoření ADCDs dokončení implementace operačního systému Z řady pro vývojová a testovací prostředí, které běží v zD & T.

Stejně jako zD & T ADCDs jsou k dispozici jenom pro partnery a zákazníky IBM a jsou určená výhradně pro účely vývoje a testování. Jsou použity pro produkční prostředí. Mnoho IBM instalační balíčky jsou k dispozici ke stažení přes [Passport využívat](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) nebo [IBM PartnerWorld](https://www-356.ibm.com/partnerworld/wps/servlet/ContentHandler/isv_com_sys_zos_adcd).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- [ZD & T prostředí] [ ibm-install-z] si dříve nastavil v Azure. Tento článek předpokládá, že používáte stejnou bitovou kopii virtuálního počítače s Ubuntu 16.04 vytvořili dříve.

- Přístup k médiím ADCD prostřednictvím IBM PartnerWorld nebo využívat služby Passport.

- A [licenční server](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). To se vyžaduje pro spuštění IBM zD & T. Když vytváříte závisí na jak licenci k softwaru od IBM:

  - **Hardwarové licenční server** vyžaduje hardwarové zařízení USB, který obsahuje potřebné pro přístup k všechny části softwaru racionální tokeny. To je nutné získat od IBM.

  - **Softwarový licenční server** vyžaduje, abyste nastavení centralizovaných serveru pro správu licencování klíče. Tato metoda je upřednostňované a je potřeba nastavit klíče, který jste dostali od IBM v serveru pro správu.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Stáhnout instalační balíčky z účtu služby Passport výhody

Přístup k médiím, ADCD je povinný. Následující postup předpokládá se zákazníky IBM a využívat výhody služby Passport. IBM partnery můžete použít [IBM PartnerWorld](https://www-356.ibm.com/partnerworld/wps/servlet/ContentHandler/isv_com_sys_zos_adcd).

> [!NOTE]
> Tento článek předpokládá, že počítač s Windows se používá pro přístup k webu Azure portal a stahovat média IBM. Pokud používáte počítač Mac nebo Ubuntu plochy, příkazy a proces pro získání IBM média se mohou poněkud lišit.

1. Přihlaste se k [Passport využívat](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer).

2. Vyberte **stahování softwaru** a **Media Access**.

3. Vyberte **programu číslo smlouvy a nabídku**a klikněte na tlačítko **pokračovat**.

4. Zadejte popis části nebo výrobní číslo a klikněte na tlačítko **Finder**.

5. Volitelně klikněte na seznam abecedním pořadí a zobrazte zobrazení theproduct podle názvu.

6. Vyberte **všechny operační systémy** v **pole operačního systému**, a **všechny jazyky** v **pole jazyků**. Potom klikněte na **Přejít**.

7. Klikněte na tlačítko **vybrat jednotlivé soubory** rozbalte seznam a zobrazit jednotlivé médium, které chcete stáhnout.

8. Ověřte balíčky, které chcete stáhnout, vyberte **Stáhnout**a pak stáhněte soubory do adresáře, který chcete.

## <a name="upload-the-adcd-packages"></a>Nahrát ADCD balíčky

Teď, když máte balíčky, musíte nahrát váš virtuální počítač v Azure.

1. Na webu Azure Portal, inicializovat **ssh** relaci s virtuálním Počítačem se systémem Ubuntu, které jste vytvořili. Přejděte ke svému virtuálnímu počítači, vyberte **přehled** okna a pak vyberte **připojit**.

2. Vyberte **SSH** kartu a potom zkopírujte příkaz "SSH" do schránky.

3. Přihlaste se k virtuálnímu počítači pomocí přihlašovacích údajů a [klienta SSH](/azure/virtual-machines/linux/use-remote-desktop) podle výběru. Tato ukázka používá rozšíření Linuxu pro Windows 10, který přidá do příkazového řádku Windows příkaz prostředí bash. PuTTY funguje stejně.

4. Při přihlášení, vytvořte adresář pro nahrání balíčků IBM. Mějte na paměti Linux je velká a malá písmena. Například v této ukázce předpokládá, že balíčky jsou odeslány do:

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. Nahrát soubory pomocí klienta SSH, jako[WinSCP](https://winscp.net/eng/index.php). Protože spojovací bod služby je součástí SSH, používá port 22, což je protokol SSH používá. Pokud váš místní počítač není Windows, můžete zadat [příkazu scp](http://man7.org/linux/man-pages/man1/scp.1.html) v relaci SSH.

6. Zahájení nahrávání do adresáře virtuálního počítače Azure, kterou jste vytvořili, která se stane úložiště obrázků zD & T.

    > [!NOTE]
    > Ujistěte se, že **ADCDTOOLS. XML** je součástí se **home/uživatelskéid/ZDT/adcd/nov2017** adresáře. Budete ho potřebovat později.

7. Počkejte, soubory, které chcete nahrát, což může trvat delší dobu v závislosti na připojení k Azure.

8. Až nahrávání se dokončí, přejděte do adresáře, svazky a dekomprimovat všechny **gz** svazky:

    ```
        gunzip \*.gz
    ```
    
![Zobrazení Průzkumníka souborů dekomprimovat gz svazky](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>Konfigurace úložiště obrázků

Dalším krokem je konfigurace zD & T používat nahrané balíčky. Proces úložiště bitové kopie v rámci zD & T umožňuje připojit a pomocí bitové kopie. Může použít SSH nebo FTP.

1. Spustit **zDTServer**. Chcete-li to provést, musí být na kořenové úrovni. Zadejte následující dva příkazy v pořadí:
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. Poznámka: výstupní adresy URL pomocí příkazu a použít tuto adresu URL pro přístup k webovému serveru. Vypadá podobně jako:
     > https://(your VM name or IP address):9443/ZDTMC/index.html
     >
     > Nezapomeňte, že váš web Accessu pomocí portu 9443. To lze použijte k přihlášení k webovému serveru. ID uživatele pro ZD & T je **zdtadmin** a heslo je **heslo**.

    ![IBM zD & T Enterprise Edition úvodní obrazovka](media/02-welcome.png)

3. Na **rychlý Start** stránce v části **konfigurovat**vyberte **úložiště obrázků**.

     ![IBM zD & T Enterprise Edition s rychlým startem obrazovky](media/03-quickstart.png)

4. Na **konfigurace úložiště obrázků** stránce **SSH File Transfer Protocol**.

5. Pro **název hostitele**, typ **Localhost** a zadejte cestu k adresáři kam jste odeslali bitové kopie. Například /home/MyUserID/ZDT/adcd/nov2017/volumes.

6. Zadejte **ID uživatele** a **heslo** pro virtuální počítač. Nepoužívejte ZD & T ID uživatele a heslo.

7. Otestujte připojení k Ujistěte se, že máte přístup a pak vyberte **Uložit** uložte konfiguraci.

## <a name="configure-the-target-environments"></a>Konfiguruje cílová prostředí

Dalším krokem je konfigurace zD & T cílové prostředí. Toto emulované hostované prostředí je, ve kterém se spouští vaše Image.

1. Na **rychlý Start** stránce v části **konfigurovat**vyberte **cílové prostředí**.

2. Na **konfiguruje cílová prostředí** stránce **přidat cíl**.

3. Vyberte **Linux**. IBM podporuje dva druhy prostředí Linux a Cloud(OpenStack), ale tato ukázka běží na systému Linux.

4. Na **přidat cílové prostředí** stránky, pro **název hostitele**, zadejte **localhost**. Zachovat **portu SSH** nastavena na **22**.

5. V **cílové prostředí popisek** zadejte popisek, jako **MyCICS.**

     ![Přidání obrazovky cílového prostředí](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>Konfigurace ADCD a nasazení

Po dokončení předchozích kroků konfigurace, je nutné nakonfigurovat zD & T použít balíčky a cílové prostředí. Znovu použít proces úložiště bitové kopie v zD & T, umožňující připojení a použití imagí. Může použít SSH nebo FTP.

1. Na **rychlý Start** stránce v části **konfigurovat**vyberte **ADCD**. Sada instrukcí se zobrazí, vás kroky, které je potřeba splnit, předtím, než je možné připojit k ADCD balíček. To vysvětluje, proč jsme pojmenovali cílový adresář způsob, jakým jsme to udělali dříve.

2. Za předpokladu, že všechny bitové kopie nahraný do správného adresáře, klikněte na tlačítko **bitovou KOPII z ADCD** odkaz zobrazí v pravém dolním (uvedené v kroku 7 na následujícím snímku obrazovky).

     ![IBM zD & T Enterprise Edition - konfigurace ADCD obrazovky](media/05-adcd.png)

## <a name="create-the-image"></a>Vytvoření image

Po dokončení předchozího kroku konfigurace **vytvoření image pomocí komponenty ADCD** se zobrazí stránka.

1. Vyberte svazek (v tomto případě listopadu 2017) k zobrazení různých balíčcích, které jsou v tomto svazku.

2. Pro tuto ukázku, vyberte **zákazníka informace ovládacího prvku systému (CICS) - 5.3**.

3. V **název Image** zadejte název pro image, jako **MyCICS Image**.

4. Vyberte **vytvořit bitovou kopii** tlačítko vpravo dole.

     ![IBM zD & T Enterprise Edition – vytvoření image pomocí komponenty ADCD obrazovky](media/06-adcd.png)

5. V zobrazeném okně sděluje image byla úspěšně nasazena, zvolte **nasazení bitových kopií**.

6. Na **nasazení bitové kopie do cílového prostředí** vyberte obrázek, který jste vytvořili na předchozí stránce (**MyCICS Image**) a cílové prostředí vytvořili (**MyCICS**).

7. Na další obrazovce zadejte svoje přihlašovací údaje pro virtuální počítač (tedy ne ztadmin přihlašovací údaje).

8. V podokně vlastností zadejte počet **centrální procesory (CPs)**, množství **systémovou paměť (GB)** a **adresáře nasazení** pro běžící imagí. Protože se jedná ukázku, uchovávejte malé.

9. Ujistěte se, že je vybraná pole **automaticky příkazu IPL problém z/OS po nasazení**.

     ![Vlastnosti obrazovky](media/07-properties.png)

10. Vyberte **kompletní**.

11. Vyberte **nasadit Image** z **nasazení bitové kopie do cílového prostředí** stránky.

Bitové kopie teď můžete nasadit a je připravený k upevnění emulátorem 3270 terminálu.

> [!NOTE]
> Pokud se zobrazí chyba s oznámením, že nemáte dostatek místa na disku, mějte na paměti, že oblast vyžaduje 151 Gb.

Blahopřejeme! Teď používáte prostředí sálové počítače IBM v Azure.

## <a name="learn-more"></a>Další informace

- [Migrace mainframů: mýty a skutečnosti](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [IBM DB2 pureScale v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [Řešení potíží](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Uvedení mainframových migrace do Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md

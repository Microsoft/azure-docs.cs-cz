---
title: Nastavení distribuce řízené vývojáři aplikací (ADCD) v IBM zD&T v1 | Dokumenty společnosti Microsoft
description: Spusťte vývojové a testovací prostředí IBM Z (zD&T) na virtuálních počítačích Azure.
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
ms.author: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: 66f80c79219090c27da37dfc1d9149df5604961f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68841389"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>Nastavení distribuce řízené vývojáři aplikací (ADCD) v IBM zD&T v1

Vývojové a testovací prostředí IBM Z (zD&T) můžete spustit na virtuálních počítačích Azure Virtual Machines(VM). Toto prostředí emuluje architekturu řady IBM Z. Může hostit různé operační systémy nebo instalace řady Z (také nazývané instance Nebo balíčky Z), které jsou k dispozici prostřednictvím přizpůsobených balíčků nazývaných distribuce řízené vývojáři aplikací IBM (ADCD).

Tento článek ukazuje, jak nastavit instanci ADCD v prostředí zD&T v Azure. ADCDs vytvořit kompletní Implementace operačního systému Řady Z pro vývoj a testování prostředí, které běží v zD&T.

Stejně jako zD&T jsou ADCD k dispozici pouze zákazníkům a partnerům IBM a jsou určeny výhradně pro účely vývoje a testování. Nepoužívají se pro produkční prostředí. Mnoho instalačních balíčků IBM je k dispozici ke stažení prostřednictvím [služby Passport Advantage](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) nebo [IBM PartnerWorld](https://www.ibm.com/partnerworld/public).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Prostředí [zD&T][ibm-install-z] dříve nastavené v Azure. Tento článek předpokládá, že používáte stejný obrázek virtuálního virtuálního aplikace Ubuntu 16.04 vytvořený dříve.

- Přístup k médiím ADCD prostřednictvím ibm PartnerWorld nebo Passport Advantage.

- [Licenční server](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). To je nutné ke spuštění IBM zD&T. Způsob, jakým jej vytvoříte, závisí na tom, jak software licencujete od IBM:

  - **Hardwarový licenční server** vyžaduje hardwarové zařízení USB, které obsahuje rational tokeny nezbytné pro přístup ke všem částem softwaru. Tuto položku musíte získat od IBM.

  - **Licenční server založený na softwaru** vyžaduje, abyste nastavili centralizovaný server pro správu licenčních klíčů. Tato metoda je upřednostňována a vyžaduje nastavení klíčů, které obdržíte od IBM na serveru pro správu.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Stáhněte si instalační balíčky z aplikace Passport Advantage

Je vyžadován přístup k médiu ADCD. Níže uvedené kroky předpokládají, že jste zákazníky IBM a můžete použít službu Passport Advantage. Partneři IBM mohou používat [IBM PartnerWorld](https://www.ibm.com/partnerworld/public).

> [!NOTE]
> Tento článek předpokládá, že počítač s Windows se používá pro přístup k portálu Azure a ke stažení médií IBM. Pokud používáte plochu Mac nebo Ubuntu, příkazy a proces pro získání médií IBM se mohou mírně lišit.

1. Přihlaste se ke službě [Passport Advantage](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer).

2. Vyberte **položku Stažení softwaru** a **Přístup k médiím**.

3. Vyberte **Nabídku programu a číslo smlouvy**a klepněte na tlačítko **Pokračovat**.

4. Zadejte popis dílu nebo číslo dílu a klepněte na **Finder**.

5. Volitelně můžete kliknout na abecední seznam pořadí, který zobrazí a zobrazí produkt podle názvu.

6. V poli Operační **systém**vyberte **Všechny operační systémy** a **Všechny jazyky** v **poli Jazyky**. Potom klepněte na tlačítko **Přejít**.

7. Kliknutím na **Vybrat jednotlivé soubory** rozbalte seznam a zobrazte jednotlivá média ke stažení.

8. Ověřte balíčky, které chcete stáhnout, vyberte **Stáhnout**a stáhněte soubory do požadovaného adresáře.

## <a name="upload-the-adcd-packages"></a>Nahrání balíčků ADCD

Teď, když máte balíčky, musíte je nahrát do virtuálního počítače v Azure.

1. Na webu Azure Portal iniciujte relaci **ssh** s virtuálním počítačem Ubuntu, které jste vytvořili. Přejděte na virtuální počítač, vyberte okno **Přehled** a pak vyberte **Připojit**.

2. Vyberte kartu **SSH** a pak zkopírujte příkaz ssh do schránky.

3. Přihlaste se k virtuálnímu počítači pomocí přihlašovacích údajů a [klienta SSH](/azure/virtual-machines/linux/use-remote-desktop) podle výběru. Tato ukázka používá linuxová rozšíření pro Windows 10, která přidává bash shell do příkazového řádku Windows. PuTTY funguje stejně dobře.

4. Po přihlášení vytvořte adresář pro nahrání balíčků IBM. Mějte na paměti, Linux je malá a velká písmena. Tato ukázka například předpokládá, že balíčky jsou odeslány do:

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. Nahrajte soubory pomocí klienta SSH, například[WinSCP](https://winscp.net/eng/index.php). Vzhledem k tomu, Že SCP je součástí SSH , používá port 22, což je to, co Používá SSH. Pokud váš místní počítač není Windows, můžete zadat [příkaz scp](http://man7.org/linux/man-pages/man1/scp.1.html) v relaci SSH.

6. Iniciujte nahrávání do adresáře virtuálního počítače Azure, který jste vytvořili, který se stane úložištěm bitových obrázků pro zD&T.

    > [!NOTE]
    > Ujistěte se, že **ADCDTOOLS. XML** je součástí nahrávání do **domovského/MyUserID/ZDT/adcd/nov2017** adresáře. Budete ho potřebovat později.

7. Počkejte na nahrání souborů, což může nějakou dobu trvat v závislosti na připojení k Azure.

8. Po dokončení nahrávání přejděte do adresáře svazků a dekomprimujte všechny svazky **gz:**

    ```
        gunzip \*.gz
    ```
    
![Průzkumník souborů zobrazující dekomprimované svazky GZ](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>Konfigurace úložiště bitových obrázků

Dalším krokem je konfigurace zD&T pro použití nahraného balíčku(ů). Proces ukládání obrazu v rámci zD&T umožňuje připojit a používat obrázky. To může používat SSH nebo FTP.

1. Spusťte **zDTServer**. Chcete-li to provést, musíte být na kořenové úrovni. Zadejte následující dva příkazy v pořadí:
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. Poznamenejte si výstup adresy URL pomocí příkazu a použijte tuto adresu URL pro přístup k webovému serveru. Vypadá to podobně jako:
     > https://(název virtuálního počítače nebo IP adresa):9443/ZDTMC/index.html
     >
     > Nezapomeňte, že váš webový přístup používá port 9443. Použijte tuto možnost k přihlášení k webovému serveru. ID uživatele pro ZD&T je **zdtadmin** a heslo je **heslo**.

    ![Úvodní obrazovka IBM zD&T Enterprise Edition](media/02-welcome.png)

3. Na stránce **Rychlý start** vyberte v části **Konfigurovat** **položku Obrazové úložiště**.

     ![Obrazovka rychlého startu IBM zD&T Enterprise Edition](media/03-quickstart.png)

4. Na stránce **Konfigurovat ukládání obrázků** vyberte možnost **SSH File Transfer Protocol**.

5. Do **pole Název hostitele**zadejte **Localhost** a zadejte cestu k adresáři, do které jste obrázky nahráli. Například /home/MyUserID/ZDT/adcd/nov2017/volumes.

6. Zadejte **ID uživatele** a **heslo** pro virtuální ho. Nepoužívejte ZD&T Uživatelské ID a heslo.

7. Otestujte připojení, abyste se ujistili, že máte přístup, a pak vyberte **Uložit,** chcete-li uložit konfiguraci.

## <a name="configure-the-target-environments"></a>Konfigurace cílových prostředí

Dalším krokem je konfigurace cílového prostředí zD&T. Toto emulované hostované prostředí je místo, kde se vaše obrázky spustit.

1. Na stránce **Rychlý start** vyberte v části **Konfigurovat** **cílová prostředí**.

2. Na stránce **Konfigurovat cílová prostředí** vyberte **Přidat cíl**.

3. Vyberte **Linux**. IBM podporuje dva typy prostředí, Linux a Cloud (OpenStack), ale toto demo běží na Linuxu.

4. Na stránce **Přidat cílové prostředí** zadejte do pole Název **hostitele** **localhost**. Udržujte **port SSH** nastavený na **22**.

5. Do pole **Popisek cílové prostředí** zadejte popisek, například **MyCICS.**

     ![Přidat obrazovku cílového prostředí](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>Konfigurace ADCD a nasazení

Po dokončení předchozích kroků konfigurace je nutné nakonfigurovat zD&T tak, aby používaly balíčky a cílové prostředí. Opět platí, že používáte proces ukládání obrazu v zD&T, který vám umožní připojit a používat obrázky. To může používat SSH nebo FTP.

1. Na stránce **Rychlý start** vyberte v části **Configure** **položku ADCD**. Zobrazí se sada pokynů s pokyny, které vás vyjádře kroky, které je třeba provést před montáží balíčku ADCD. To vysvětluje, proč jsme pojmenovali cílový adresář tak, jak jsme to udělali dříve.

2. Za předpokladu, že všechny obrázky byly nahrány do správných adresářů, klikněte na obrázek z odkazu **ADCD** zobrazený v pravo dole (znázorněno v kroku 7 na následujícím snímku obrazovky).

     ![IBM zD&T Enterprise Edition - Konfigurace obrazovky ADCD](media/05-adcd.png)

## <a name="create-the-image"></a>Vytvoření image

Po dokončení předchozího kroku konfigurace se zobrazí stránka **Vytvořit obraz pomocí komponent ADCD.**

1. Vyberte svazek (v tomto případě listopad 2017) a zobrazte různé balíčky, které jsou v tomto svazku.

2. Pro tuto ukázku vyberte **Customer Information Control System (CICS) - 5.3**.

3. Do pole **Název obrázku** zadejte název obrázku, například **Obraz MyCICS**.

4. Vpravém dolním bodě vyberte tlačítko **Vytvořit obraz.**

     ![IBM zD&T Enterprise Edition - Vytvoření obrazu pomocí obrazovky ADCD Components](media/06-adcd.png)

5. V okně, které se zobrazí s tím, že bitová kopie byla úspěšně nasazena, zvolte **Nasadit bitové kopie**.

6. Na stránce **Nasazení bitové kopie do cílového prostředí** vyberte obrázek, který jste vytvořili na předchozí stránce **(MyCICS Image)** a cílové prostředí vytvořené dříve **(MyCICS).**

7. Na další obrazovce zadejte pověření pro virtuální počítač (to znamená, že ne pověření ztadmin).

8. V podokně Vlastnosti zadejte počet **centrálních procesorů (CP),** velikost **systémové paměti (GB)** a **adresář nasazení** pro spuštěnou bitovou kopii. Vzhledem k tomu, že se jedná o demo, udržujte ji malou.

9. Ujistěte se, že je políčko zaškrtnuto pro **příkaz Automaticky vydávat Příkaz IPL do z/OS po nasazení**.

     ![Obrazovka Vlastnosti](media/07-properties.png)

10. Vyberte **Možnost Dokončit**.

11. Vyberte **Nasadit bitovou kopii** ze stránky **Nasazení bitové kopie do cílového prostředí.**

Vaše image může nyní nasadit a je připraven k připojení pomocí emulátoru terminálu 3270.

> [!NOTE]
> Pokud se zobrazí chyba, že nemáte dostatek místa na disku, všimněte si, že oblast vyžaduje 151 Gb.

Blahopřejeme! Nyní používáte prostředí sálových počítačů IBM v Azure.

## <a name="learn-more"></a>Další informace

- [Migrace sálových počítačů: mýty a fakta](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [IBM DB2 pureScale v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [Řešení potíží](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Demystifikace sálového počítače k migraci Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md

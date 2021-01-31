---
title: Nastavení vývojářů aplikací řízených distribucí (ADCD) v IBM zD&T V1 | Microsoft Docs
description: Spusťte prostředí IBM Z vývojového a testovacího prostředí (zD&T) na Azure Virtual Machines (VM).
services: virtual-machines-linux
ms.service: virtual-machines-linux
ms.subservice: workloads
documentationcenter: ''
author: njray
manager: edprice
ms.author: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: c76768fa94b524763ac8ad94c9c3f5bb3a73dddb
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219434"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>Nastavení vývojářů aplikací řízených distribucí (ADCD) v IBM zD&T V1

Prostředí IBM Z vývojového a testovacího prostředí (zD&T) můžete spustit na Azure Virtual Machines (VM). Toto prostředí emuluje architekturu řady IBM Z. Může hostovat nejrůznější operační systémy řady Z a instalace (označované taky jako instance Z a balíčky), které jsou dostupné prostřednictvím přizpůsobených sad, které se nazývají ADCDs (vývojáři aplikací od společnosti IBM).

V tomto článku se dozvíte, jak nastavit instanci ADCD v prostředí zD&T v Azure. ADCDs vytvořit kompletní implementace operačního systému řady Z pro vývojová a testovací prostředí, která běží v zD&T.

Podobně jako zD&T je ADCDs k dispozici pouze pro zákazníky a partnery IBM a slouží výhradně pro účely vývoje a testování. Nepoužívají se pro produkční prostředí. K dispozici je řada instalačních balíčků IBM ke stažení prostřednictvím [služby Passport Advantage](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) nebo [IBM PartnerWorld](https://www.ibm.com/partnerworld/public).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- [Prostředí zD&T][ibm-install-z] se dřív nastavilo v Azure. V tomto článku se předpokládá, že používáte stejnou image virtuálního počítače s Ubuntu 16,04, kterou jste vytvořili dříve.

- Přístup k médiu ADCD prostřednictvím aplikace IBM PartnerWorld nebo služby Passport.

- [Licenční server](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). K tomu je potřeba spustit IBM zD&T. Způsob, jakým ho vytvoříte, závisí na tom, jak si software od IBM vydáte.

  - **Hardwarový licenční server** vyžaduje hardwarové zařízení USB, které obsahuje racionální tokeny potřebné pro přístup ke všem částem softwaru. Musíte ho získat od společnosti IBM.

  - **Server licencování na základě softwaru** vyžaduje, abyste si nastavili centralizovaný Server pro správu licenčních klíčů. Tato metoda je upřednostňovaná a vyžaduje, abyste nastavili klíče, které dostanete od IBM v management server.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Stažení instalačních balíčků z aplikace Passport Advantage

Je vyžadován přístup k médiu ADCD. Níže uvedený postup předpokládá, že jste zákazníkem společnosti IBM a můžete využívat výhody služby Passport. Partneři IBM můžou používat [IBM PartnerWorld](https://www.ibm.com/partnerworld/public).

> [!NOTE]
> V tomto článku se předpokládá, že se počítač s Windows používá pro přístup k Azure Portal a stažení média IBM. Pokud používáte desktopovou plochu Mac nebo Ubuntu, příkazy a procesy pro získání média IBM se mohou mírně lišit.

1. Přihlaste se k [výhodu služby Passport](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer).

2. Vyberte možnost **Stažení softwaru** a **přístup k médiím**.

3. Vyberte **nabídku programu a číslo smlouvy** a klikněte na **pokračovat**.

4. Zadejte popis části nebo číslo součásti a klikněte na tlačítko **Finder**.

5. Případně můžete kliknutím na seznam abecedních objednávek zobrazit a zobrazit produkt podle názvu.

6. Vyberte možnost **všechny operační systémy** v **poli operační systém** a **všechny jazyky** v **poli jazyky**. Pak klikněte na tlačítko **Přejít**.

7. Kliknutím na **vybrat jednotlivé soubory** rozbalte seznam a zobrazte jednotlivá média ke stažení.

8. Ověřte balíčky, které chcete stáhnout, vyberte **Stáhnout** a pak soubory Stáhněte do požadovaného adresáře.

## <a name="upload-the-adcd-packages"></a>Nahrát ADCD balíčky

Teď, když máte balíčky, musíte je nahrát do svého virtuálního počítače v Azure.

1. V Azure Portal Iniciujte relaci **SSH** s virtuálním počítačem s Ubuntu, který jste vytvořili. Přejít na svůj virtuální počítač, vyberte okno **Přehled** a pak vyberte **připojit**.

2. Vyberte kartu **SSH** a potom příkaz SSH zkopírujte do schránky.

3. Přihlaste se k VIRTUÁLNÍmu počítači pomocí vašich přihlašovacích údajů a [klienta ssh](../../../linux/use-remote-desktop.md) podle volby. Tato ukázka používá rozšíření pro Linux pro Windows 10, které přidává prostředí bash do příkazového řádku Windows. Pro výstupy funguje stejně taky.

4. Po přihlášení vytvořte adresář pro nahrání balíčků IBM. Mějte na paměti, že Linux rozlišuje velká a malá písmena. Například Tato ukázka předpokládá, že balíčky jsou nahrány do:

    `/home/MyUserID/ZDT/adcd/nov2017/volumes`

5. Nahrajte soubory pomocí klienta SSH, jako je[WinSCP](https://winscp.net/eng/index.php). Vzhledem k tomu, že spojovací bod služby je součástí SSH, používá port 22, který používá protokol SSH. Pokud Váš místní počítač není Windows, můžete do relace SSH zadat [příkaz SCP](http://man7.org/linux/man-pages/man1/scp.1.html) .

6. Inicializujte nahrání do vytvořeného adresáře virtuálních počítačů Azure, který se bude nacházet jako úložiště imagí pro zD&T.

    > [!NOTE]
    > Ujistěte se, že je v umístění pro odesílání do adresáře **Home/MyUserID/ZDT/adcd/nov2017** zahrnutá tato **ADCDTOOLS.XML** . Budete ho potřebovat později.

7. Počkejte na nahrání souborů, což může v závislosti na připojení k Azure trvat delší dobu.

8. Po dokončení nahrávání přejděte do adresáře svazků a dekomprimujte všechny **GZ** svazky:

    ```console
    gunzip \*.gz
    ```
    
![Průzkumník souborů zobrazující dekomprimované svazky GZ](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>Konfigurace úložiště imagí

Dalším krokem je konfigurace zD&T, aby se používaly nahrané balíčky. Proces úložiště imagí v rámci zD&T umožňuje připojení a používání imagí. Může použít SSH nebo FTP.

1. Spusťte **zDTServer**. Chcete-li to provést, musíte být na kořenové úrovni. V uvedeném pořadí zadejte následující dva příkazy:
    ```console
    sudo su -
    /opt/ibm/zDT/bin/startServer
    ```
2. Poznamenejte si výstup adresy URL příkazem a použijte tuto adresu URL pro přístup k webovému serveru. Vypadá nějak takto:
     > https://(název nebo IP adresa virtuálního počítače): 9443/ZDTMC/index.html
     >
     > Mějte na paměti, že váš webový přístup používá port 9443. Použijte k přihlášení k webovému serveru. ID uživatele pro ZD&T je **zdtadmin** a heslo je **heslo**.

    ![Úvodní obrazovka k IBM zD&T Enterprise Edition](media/02-welcome.png)

3. Na stránce **rychlé zprovoznění** v části **Konfigurovat** vyberte **úložiště imagí**.

     ![Rychlé zprovoznění obrazovka IBM zD&T Enterprise Edition](media/03-quickstart.png)

4. Na stránce **Konfigurovat úložiště imagí** vyberte **SSH Protokol FTP (File Transfer Protocol)**.

5. Jako **název hostitele** zadejte **localhost** a zadejte cestu k adresáři, kam jste nahráli obrázky. Například/home/MyUserID/ZDT/adcd/nov2017/volumes.

6. Zadejte **ID uživatele** a **heslo** pro virtuální počítač. Nepoužívejte ID uživatele a heslo ZD&T.

7. Otestujte připojení, abyste se ujistili, že máte přístup, a pak vyberte **Uložit** a uložte konfiguraci.

## <a name="configure-the-target-environments"></a>Konfigurace cílových prostředí

Dalším krokem je konfigurace cílového prostředí zD&T. Toto emulované hostované prostředí je místo, kde se vaše image spouští.

1. Na stránce **rychlé zprovoznění** v části **Konfigurovat** vyberte **cílová prostředí**.

2. Na stránce **Konfigurace cílových prostředí** vyberte **Přidat cíl**.

3. Vyberte **Linux**. IBM podporuje dva typy prostředí, Linux a Cloud (OpenStack), ale tato ukázka běží na Linux.

4. Na stránce **Přidat cílové prostředí** pro **název hostitele** zadejte **localhost**. Nechejte **port SSH** nastavený na **22**.

5. Do pole **popisek cílového prostředí** zadejte popisek, například **MyCICS.**

     ![Obrazovka Přidat cílové prostředí](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>Konfigurace ADCD a nasazení

Po dokončení předchozích kroků konfigurace je třeba nakonfigurovat zD&T pro použití balíčků a cílového prostředí. Znovu použijete proces úložiště imagí v zD&T, který umožňuje připojení a používání imagí. Může použít SSH nebo FTP.

1. Na stránce **rychlé zprovoznění** v části **Konfigurovat** vyberte možnost **ADCD**. Zobrazí se sada instrukcí, která informuje o krocích, které je třeba provést před tím, než bude možné připojit balíček ADCD. Vysvětluje, proč jsme jmenovali cílový adresář jako dříve.

2. Za předpokladu, že všechny Image byly nahrány do správných adresářů, klikněte na odkaz **obrázek z ADCD** zobrazený v pravém dolním rohu (viz krok 7 na následujícím snímku obrazovky).

     ![IBM zD&T Enterprise Edition – Konfigurace obrazovky ADCD](media/05-adcd.png)

## <a name="create-the-image"></a>Vytvoření image

Po dokončení předchozího kroku konfigurace se zobrazí stránka **vytvořit obrázek pomocí součástí ADCD** .

1. V tomto případě vyberte svazek (v tomto případě 2017), chcete-li zobrazit různé balíčky, které jsou v daném svazku.

2. V této ukázce vyberte **Customer Information Control System (CICS)-5,3**.

3. Do pole **název Image** zadejte název obrázku, například **MyCICS obrázek**.

4. V pravém dolním rohu vyberte tlačítko **vytvořit obrázek** .

     ![IBM zD&T Enterprise Edition – vytvoření Image pomocí obrazovky komponent ADCD](media/06-adcd.png)

5. V okně, které se zobrazí, informuje o tom, že se image úspěšně nasadila, a vyberte **nasadit image**.

6. Na stránce **nasadit obrázek do cílového prostředí** vyberte obrázek, který jste vytvořili na předchozí stránce (**bitová kopie MyCICS**), a cílové prostředí, které jste vytvořili dříve (**MyCICS**).

7. Na další obrazovce zadejte svoje přihlašovací údaje pro virtuální počítač (to znamená, že se nejedná o přihlašovací údaje ztadmin).

8. V podokně Vlastnosti zadejte počet **centrálních procesorů (CPs)**, velikost **systémové paměti (GB)** a **Adresář nasazení** pro spuštěnou bitovou kopii. Vzhledem k tomu, že se jedná o ukázku, udržujte ji malou.

9. Ujistěte se, že je zaškrtnuto políčko pro **automatické vystavení příkazu IPL pro z/OS po nasazení**.

     ![Obrazovka vlastností](media/07-properties.png)

10. Vyberte **Dokončit**.

11. **Na stránce nasazení image na cílovém prostředí** vyberte **nasadit bitovou kopii** .

Image se teď může nasadit a je připravená k připojení emulátoru terminálu 3270.

> [!NOTE]
> Pokud se zobrazí chyba s informací, že nemáte dostatek místa na disku, je třeba si uvědomit, že oblast vyžaduje 151 GB.

Gratulujeme! Nyní používáte sálové prostředí IBM v Azure.

## <a name="learn-more"></a>Další informace

- [Migrace sálového počítače: mýty a fakta](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [IBM DB2 pureScale v Azure](ibm-db2-purescale-azure.md)
- [Řešení potíží](../../../troubleshooting/index.yml)
- [Migrace Demystifying z sálového počítače do Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md

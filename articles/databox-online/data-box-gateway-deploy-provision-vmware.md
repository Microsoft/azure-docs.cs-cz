---
title: Kurz zřízení služby Azure Data Box Gateway ve VMware | Microsoft Docs
description: Druhý kurz k nasazení služby Azure Data Box Gateway se zabývá zřízením virtuálního zařízení ve VMware.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 10/01/2018
ms.author: alkohli
ms.openlocfilehash: ea4203c45f482b990122a966fc2ec13b3fb41c84
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167150"
---
# <a name="tutorial-provision-azure-data-box-gateway-in-vmware-preview"></a>Kurz: Zřízení služby Azure Data Box Gateway ve VMware (Preview)

## <a name="overview"></a>Přehled

Tento kurz popisuje, jak zřídit službu Data Box Gateway v hostitelském systému s VMware ESXi 6.0 nebo 6.5. 

Ke zřízení virtuálního zařízení a připojení k němu potřebujete oprávnění správce. Dokončení zřizování a počátečního nastavení může trvat přibližně 10 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zajištění, aby hostitel splňoval minimální požadavky na zařízení
> * Zřízení virtuálního zařízení ve VMware
> * Spuštění virtuálního zařízení a získání IP adresy

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

> [!IMPORTANT]
> - Data Box Gateway je ve verzi Preview. Před objednáním a nasazením tohoto řešení si přečtěte [podmínky užívání pro předběžné verze systému Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Požadavky na zřízení virtuálního zařízení v hostitelském systému s VMware ESXi 6.0 nebo 6.5 jsou následující.

### <a name="for-the-data-box-gateway-resource"></a>Prostředek Data Box Gateway

Než začnete, ujistěte se, že:

* Dokončili jste všechny kroky v tématu [Příprava portálu pro službu Data Box Gateway](data-box-gateway-deploy-prep.md).
* Z webu Azure Portal jste si stáhli image virtuálního zařízení pro VMware, jak je popsáno v tématu [Příprava portálu pro službu Data Box Gateway](data-box-gateway-deploy-prep.md).

  > [!IMPORTANT]
  > Software ve službě Data Box Gateway je možné používat pouze s prostředkem Data Box Gateway.

### <a name="for-the-data-box-gateway-virtual-device"></a>Virtuální zařízení Data Box Gateway

Před nasazením virtuálního zařízení se ujistěte, že:

* Máte přístup k hostitelskému systému s VMware (ESXi 6.0 nebo 6.5), který můžete použít ke zřízení zařízení.
* Hostitelský systém může pro zřízení virtuálního zařízení vyhradit následující prostředky:

  * Minimálně 4 jádra
  * Minimálně 8 GB paměti RAM
  * Jedno síťové rozhraní
  * 250GB disk s operačním systémem
  * 2TB virtuální disk pro data systému

### <a name="for-the-network-in-datacenter"></a>Síť v datacentru

Než začnete:

- Projděte si požadavky na síť pro nasazení služby Data Box Gateway a nakonfigurujte podle těchto požadavků síť datacentra. Další informace najdete v tématu popisujícím [požadavky na síť pro službu Data Box Gateway](data-box-gateway-system-requirements.md#networking-requirements).
- Ujistěte se, že je šířka internetového pásma minimálně 20 Mb/s, aby zařízení mohlo fungovat optimálně.

## <a name="check-the-host-system"></a>Kontrola hostitelského systému

K vytvoření virtuálního zařízení potřebujete:

* Přístup k hostitelskému systému se serverem VMware ESXi 6.0 nebo 6.5. Hostitelský systém může pro virtuální zařízení vyhradit následující prostředky:
 
  * Minimálně 4 jádra
  * Minimálně 8 GB paměti RAM 
  * Jedno síťové rozhraní připojené k síti, která podporuje směrování provozu do internetu. 
  * 250GB disk s operačním systémem
  * 2TB virtuální disk pro data
* Klienta VMware vSphere ve vašem systému pro správu hostitele ESXi.


## <a name="provision-a-virtual-device-in-hypervisor"></a>Zřízení virtuálního zařízení v hypervisoru

Pomocí následujících kroků ve svém hypervisoru zřiďte virtuální zařízení.

1. Zkopírujte do svého systému image virtuálního zařízení. Tuto virtuální image (dva soubory) jste si stáhli přes Azure Portal. Poznamenejte si umístění, do kterého jste image zkopírovali, protože tuto image použijete v pozdější části tohoto postupu.

2. Pomocí webového klienta vSphere se přihlaste k serveru ESXi. K vytvoření virtuálního počítače potřebujete mít oprávnění správce.

   ![](./media/data-box-gateway-deploy-provision-vmware/image1.png)
  
3. Nahrajte VMDK na server ESXi. V podokně Navigátor vyberte **Úložiště**.

   ![](./media/data-box-gateway-deploy-provision-vmware/image2.png)

4. V pravém podokně v části **Úložiště dat** vyberte úložiště dat, do kterého chcete VMDK nahrát. 

    - Úložiště dat musí být typu VMFS5. 
    - Úložiště dat také musí mít dostatek volného místa na disk s operačním systémem a datový disk.
   
5. Klikněte pravým tlačítkem a vyberte **Procházet úložiště dat**.

   ![](./media/data-box-gateway-deploy-provision-vmware/image3.png)

6. Zobrazí se okno **Prohlížeč úložiště dat**.

   ![](./media/data-box-gateway-deploy-provision-vmware/image4.png)

7. Kliknutím na ikonu **Vytvořit adresář** na panelu nástrojů vytvořte novou složku. Zadejte název složky a poznamenejte si ho. Název této složky použijete později při vytváření virtuálního počítače (doporučený osvědčený postup). Klikněte na **Vytvořit adresář**.

   ![](./media/data-box-gateway-deploy-provision-vmware/image5.png)

8. Nová složka se zobrazí v levém podokně okna **Prohlížeč úložiště dat**. Klikněte na ikonu **Nahrát** a vyberte **Nahrát soubor**.

    ![](./media/data-box-gateway-deploy-provision-vmware/image6.png)

9. Přejděte k souborům VMDK, které jste si stáhli. Zobrazí se dva soubory. Vyberte soubor k nahrání.

    ![](./media/data-box-gateway-deploy-provision-vmware/image7.png)

10. Klikněte na **Otevřít**. Spustí se nahrávání souboru VMDK do zadaného úložiště dat. Nahrání souboru může několik minut trvat.
11. Po dokončení nahrávání se soubor zobrazí v úložišti dat ve složce, kterou jste vytvořili. Teď do stejného úložiště dat nahrajte druhý soubor VMDK. Po nahrání obou souborů se oba sloučí do jednoho souboru. V adresáři se pak zobrazí jeden soubor.

    ![](./media/data-box-gateway-deploy-provision-vmware/image8.png)

12. Vraťte se do okna klienta vSphere. V podokně Navigátor vyberte **Virtuální počítače**. V pravém podokně klikněte na **Vytvořit/zaregistrovat virtuální počítač**.

    ![](./media/data-box-gateway-deploy-provision-vmware/image9.png)

13. Zobrazí se okno **Nový virtuální počítač**. V části Vyberte typ vytvoření zvolte **Vytvořit nový virtuální počítač** a klikněte na **Další**.
    ![](./media/data-box-gateway-deploy-provision-vmware/image10.png)

14. Na stránce **Vyberte název, název operačního systému a umístění** zadejte **Název** vašeho virtuálního počítače. Tento název by se měl shodovat s názvem složky, který jste zadali dříve v kroku 7 (doporučený osvědčený postup). Jako **Řada hostovaného operačního systému** zvolte Windows a jako **Verze hostovaného operačního systému** zvolte Microsoft Windows Server 2016 (64bitová verze). Klikněte na **Další**.

    ![](./media/data-box-gateway-deploy-provision-vmware/image11.png)

15. Na stránce **Vyberte úložiště** vyberte úložiště dat, které chcete ke zřízení virtuálního počítače použít. Klikněte na **Další**.

    ![](./media/data-box-gateway-deploy-provision-vmware/image12.png)
16. Na stránce **Přizpůsobit nastavení** nastavte počet **CPU** na 4, velikost **paměti** na 8 192 MB (nebo více) a velikost **pevného disku 1** na 2 TB (nebo více). Zvolte **pevný disk SCSI**, který chcete přidat. V tomto případě to byl typ LSI Logic SAS. **Statické disky IDE se nepodporují.** **Pevný disk 1** je virtuální pevný disk. Mějte na paměti, že disk po zřízení už není možné zmenšit.

    ![](./media/data-box-gateway-deploy-provision-vmware/image13.png)

    Na stejné stránce klikněte na **Přidat pevný disk** a vyberte **Existující pevný disk**. V úložišti dat vyberte soubor VMDK. Tím přidáte disk s operačním systémem. 

     ![](./media/data-box-gateway-deploy-provision-vmware/image14.png)

    Posuňte se dolů, dokud se nezobrazí **Nový pevný disk**, a rozbalte ho, aby se zobrazilo nastavení. Nastavte **Uzel virtuálního zařízení** na **Kontroler IDE 0**. Klikněte na **Další**.

     ![](./media/data-box-gateway-deploy-provision-vmware/image15.png)

27. Na stránce **Připraveno k dokončení** zkontrolujte všechna nastavení přidružená k novému virtuálnímu počítači. Ověřte, že počet CPU je 4, paměť má velikost 8 192 MB, počet síťových rozhraní je 1 a Pevný disk 2 má kontroler IDE 0. Klikněte na **Dokončit**. 
   
    ![](./media/data-box-gateway-deploy-provision-vmware/image16.png)
    ![](./media/data-box-gateway-deploy-provision-vmware/image17.png)

Váš virtuální počítač je teď zřízený. Zobrazí se o tom oznámení a nový virtuální počítač se přidá do seznamu virtuálních počítačů. 

![](./media/data-box-gateway-deploy-provision-vmware/image17.png)

Dalším krokem je počítač zapnout a získat jeho IP adresu.

> [!NOTE]
> Doporučujeme, abyste na virtuální zařízení (zřízené výše) neinstalovali nástroje VMware. Instalace nástrojů VMware bude mít za následek nepodporovanou konfiguraci.

## <a name="start-the-virtual-device-and-get-the-ip"></a>Spuštění virtuálního zařízení a získání IP adresy

Pomocí následujících kroků spusťte své virtuální zařízení a připojte se k němu.

#### <a name="to-start-the-virtual-device"></a>Spuštění virtuálního zařízení
1. Spusťte virtuální zařízení. V pravém podokně vyberte v seznamu virtuálních počítačů vaše zařízení a klikněte na něj pravým tlačítkem, aby se zobrazila místní nabídka. Vyberte **Napájení** a pak vyberte **Zapnout**. Tím by se měl virtuální počítač zapnout. Stav můžete zobrazit v dolním podokně webového klienta.

    ![](./media/data-box-gateway-deploy-provision-vmware/image19.png)

2. Znovu vyberte virtuální počítač. Klikněte pravým tlačítkem, vyberte **Konzola** a pak vyberte **Otevřít v novém okně**.

    ![](./media/data-box-gateway-deploy-provision-vmware/image20.png)

3. V novém okně se otevře konzola virtuálního počítače. 

    ![](./media/data-box-gateway-deploy-provision-vmware/image21.png)

4. Jakmile bude zařízení spuštěné, umístěte kurzor na kartu uprostřed v horní části okna konzoly a klikněte na ni. Vyberte **Hostovaný operační systém > Odeslat klíče > Ctrl + Alt + Delete**. Tím se virtuální počítač odemkne.

   ![](./media/data-box-gateway-deploy-provision-vmware/image22.png)

5. Zadejte heslo a přihlaste se k počítači. Výchozí heslo je Password1.

   ![](./media/data-box-gateway-deploy-provision-vmware/image23.png)

6. Kroky 5 až 7 proveďte pouze v případě, že zařízení spouštíte v jiném prostředí než DHCP. Pokud jste v prostředí DHCP, přeskočte tyto kroky a přejděte ke kroku 8. Pokud jste zařízení spustili v jiném prostředí než DHCP, zobrazí se o tom zpráva: **Pomocí rutiny Set-HcsIPAddress nakonfigurujte síť**. 
   
7. Pokud chcete nakonfigurovat síť, na příkazovém řádku použijte příkaz `Get-HcsIpAddress`, který vypíše povolená síťová rozhraní na vašem virtuálním zařízení. Pokud má vaše zařízené povolené jediné síťové rozhraní, výchozí název přiřazený tomuto rozhraní je `Ethernet`.

8. Pomocí rutiny `Set-HcsIpAddress` nakonfigurujte síť. Příklad najdete níže:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

9. Po dokončení počátečního nastavení a spuštění zařízení se zobrazí text banneru zařízení. Poznamenejte si IP adresu a adresu URL pro správu zařízení, které se zobrazí v textu banneru. Pomocí této IP adresy se připojíte k webovému uživatelskému rozhraní vašeho virtuálního zařízení a dokončíte místní nastavení a aktivaci.

   ![](./media/data-box-gateway-deploy-provision-vmware/image24.png)

Pokud vaše zařízení nesplňuje minimální požadavky na konfiguraci, zobrazí se v textu banneru chyba (viz níže). Budete muset upravit konfiguraci zařízení tak, aby mělo dostatečné prostředky ke splnění minimálních požadavků. Pak můžete zařízení restartovat a připojit se k němu. Projděte si minimální požadavky na konfiguraci v části věnované [kontrole, jestli hostitelský systém splňuje minimální požadavky na virtuální zařízení](#check-the-host-system).

<!---If you face any other error during the initial configuration using the local web UI, refer to the following workflows:

* Run diagnostic tests to [troubleshoot web UI setup](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Generate log package and view log files](storsimple-ova-web-ui-admin.md#generate-a-log-package).-->

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených se službou Data Box Gateway, jako jsou:

> [!div class="checklist"]
> * Zajištění, aby hostitel splňoval minimální požadavky na zařízení
> * Zřízení virtuálního zařízení ve VMware
> * Spuštění virtuálního zařízení a získání IP adresy

V dalším kurzu se dozvíte, jak připojit, nastavit a aktivovat virtuální zařízení.

* [Nastavení a připojení ke sdíleným složkám ve službě Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)


---
title: Nastavit virtuální pole StorSimple jako souborový server | Dokumenty společnosti Microsoft
description: Tento třetí kurz v nasazení StorSimple Virtual Array vás instruuje k nastavení virtuálního zařízení jako souborového serveru.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: f609f6ff-0927-48bb-a68a-6d8985d2fe34
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 16a5e0bb3e50e3a90951572e8d2847d379c1b114
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297650"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>Nasazení virtuálního pole StorSimple – nastavení jako souborového serveru prostřednictvím portálu Azure
![](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Úvod

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Tento článek popisuje, jak provést počáteční nastavení, zaregistrovat souborový server StorSimple, dokončit nastavení zařízení a vytvořit sdílené složky SMB a připojit se k němu. Toto je poslední článek v sérii kurzů nasazení potřebných k úplnému nasazení virtuálního pole jako souborového serveru nebo serveru iSCSI.

Proces instalace a konfigurace může trvat přibližně 10 minut. Informace v tomto článku platí pouze pro nasazení Virtuální pole StorSimple. Nasazení zařízení řady StorSimple 8000 naleznete na: [Nasazení zařízení řady StorSimple 8000 se spuštěnou aktualizací 2](storsimple-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Požadavky instalačního programu 
Před konfigurací a nastavením virtuálního pole StorSimple zkontrolujte, zda:

* Zřídíte virtuální pole a připojíte k němu, jak je podrobně popsáno v [části Zřízení virtuálního pole StorSimple v technologii Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) nebo [Zřízení virtuálního pole StorSimple ve společnosti VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Máte registrační klíč služby ze služby StorSimple Device Manager, kterou jste vytvořili pro správu virtuálních polí StorSimple. Další informace naleznete [v tématu Krok 2: Získání registračního klíče služby](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) pro virtuální pole StorSimple.
* Pokud se jedná o druhé nebo následující virtuální pole, které registrujete u existující služby Správce zařízení StorSimple, měli byste mít šifrovací klíč dat služby. Tento klíč byl vygenerován při úspěšném zaregistrování prvního zařízení v této službě. Pokud jste ztratili tento klíč, přečtěte si téma [Získání šifrovacího klíče dat služby](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) pro vaše virtuální pole StorSimple.

## <a name="step-by-step-setup"></a>Podrobné nastavení
Pomocí následujících podrobných pokynů nastavte a nakonfigurujte virtuální pole StorSimple.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Krok 1: Dokončení nastavení místního webového uživatelského rozhraní a registrace zařízení
#### <a name="to-complete-the-setup-and-register-the-device"></a>Chcete-li dokončit nastavení a zaregistrovat zařízení
1. Otevřete okno prohlížeče a připojte se k místnímu webovému uživatelskému uživatelskému prostředí. Zadejte:
   
   `https://<ip-address of network interface>`
   
   Použijte adresu URL připojení uvedenou v předchozím kroku. Zobrazí se chyba označující, že došlo k potížím s certifikátem zabezpečení webu. Klepněte **na tlačítko Pokračovat na tuto webovou stránku**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Přihlaste se k webovému uživatelskému rozhraní virtuálního pole jako **StorSimpleAdmin**. Zadejte heslo správce zařízení, které jste změnili v kroku 3: Spuštění virtuálního pole v [okně Provision a StorSimple Virtual Array v technologii Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) nebo [V provision a StorSimple Virtual Array in VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. Přejdete na **domovskou** stránku. Tato stránka popisuje různá nastavení potřebná ke konfiguraci a registraci virtuálního pole pomocí služby StorSimple Device Manager. **Nastavení sítě**, **nastavení webového proxy serveru**a **času** jsou volitelné. Jedinými požadovanými nastaveními jsou **nastavení zařízení** a **nastavení cloudu**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. Na stránce **Nastavení sítě** v části **Síťová rozhraní**bude data 0 automaticky konfigurována za vás. Každé síťové rozhraní je ve výchozím nastavení nastaveno tak, aby automaticky získalo adresu IP (DHCP). Proto jsou automaticky přiřazeny IP adresy, podsítě a brány (pro Protokol IPv4 i IPv6).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Pokud jste přidali více než jedno síťové rozhraní během zřizování zařízení, můžete je nakonfigurovat zde. Poznámka: síťové rozhraní můžete nakonfigurovat pouze jako protokol IPv4 nebo jako protokol IPv4 i IPv6. Pouze konfigurace Protokolu IPv6 nejsou podporovány.
5. Servery DNS jsou vyžadovány, protože se používají, když se vaše zařízení pokusí komunikovat s poskytovateli služeb cloudového úložiště nebo vyřešit zařízení podle názvu při konfiguraci jako souborový server. Na stránce **Nastavení sítě** pod **servery DNS**:
   
   1. Primární a sekundární server DNS je automaticky konfigurován. Pokud se rozhodnete konfigurovat statické adresy IP, můžete zadat servery DNS. Pro vysokou dostupnost doporučujeme nakonfigurovat primární a sekundární server DNS.
   2. Klepnutím na tlačítko **Použít** použijte a ověřte nastavení sítě.
6. Na stránce **Nastavení zařízení:**
   
   1. **Přiřaďte** svému zařízení jedinečný název. Tento název může mít 1-15 znaků a může obsahovat písmeno, čísla a pomlčky.
   2. Klepněte na ![](./media/storsimple-virtual-array-deploy3-fs-setup/image6.png) ikonu **Souborový server** pro **typ** zařízení, které vytváříte. Souborový server vám umožní vytvářet sdílené složky.
   3. Vzhledem k tomu, že vaše zařízení je souborový server, budete muset připojit zařízení k doméně. Zadejte **název domény**.
   4. Klikněte na **Použít**.
7. Zobrazí se dialogové okno. Zadejte pověření domény v určeném formátu. Klikněte na ikonu šeku. Pověření domény jsou ověřena. Pokud jsou pověření nesprávná, zobrazí se chybová zpráva.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Klikněte na **Použít**. To platí a ověří nastavení zařízení.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Ujistěte se, že vaše virtuální pole je ve své vlastní organizační jednotce (OU) pro službu Active Directory a žádné objekty zásad skupiny (GPO) jsou použity na něj nebo zděděné. Zásady skupiny mohou instalovat aplikace, jako je antivirový software, do virtuálního pole StorSimple. Instalace dalšího softwaru není podporována a může vést k poškození dat. 
   > 
   > 
9. (Volitelně) nakonfigurujte webový proxy server. Konfigurace proxy serveru je volitelná, ale můžete ji provést jenom v tomto kroku.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   Na stránce **webového proxy** serveru:
   
   1. Zadej adresu **URL webového proxy serveru** v tomto formátu: http:// *&lt;adresu IP hostitele nebo&gt;:Pčísloort*. Všimněte si, že adresy URL HTTPS nejsou podporovány.
   2. Zadejte **ověřování** jako **základní** nebo **žádné**.
   3. Pokud používáte ověřování, budete také muset zadat **uživatelské jméno** a **heslo**.
   4. Klikněte na **Použít**. Tím ověříte a použijete nakonfigurované nastavení webového proxy serveru.
10. (Volitelně) nakonfigurujte nastavení času pro vaše zařízení, například časové pásmo a primární a sekundární servery NTP. Servery NTP jsou vyžadovány, protože zařízení musí synchronizovat čas, aby se mohlo ověřit u poskytovatelů cloudových služeb.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    Na stránce **Nastavení času:**
    
    1. V rozevíracím seznamu vyberte **časové pásmo** na základě geografické polohy, ve které je zařízení nasazováno. Výchozí časové pásmo pro vaše zařízení je PST. Toto časové pásmo bude zařízení používat pro všechny naplánované operace.
    2. Zadejte **primární server NTP** pro vaše zařízení nebo přijměte výchozí hodnotu time.windows.com. Ujistěte se, že vaše síť umožňuje přenos dat NTP z vašeho datového centra na internet.
    3. Volitelně zadejte **sekundární ntp server** pro vaše zařízení.
    4. Klikněte na **Použít**. Tím ověříte a použijete nakonfigurované nastavení času.
11. Nakonfigurujte nastavení cloudu pro vaše zařízení. V tomto kroku dokončíte konfiguraci místního zařízení a poté zařízení zaregistrujete službou Správce zařízení StorSimple.
    
    1. Zadejte **registrační klíč služby,** který jste dostali v [kroku 2: Získejte registrační klíč služby](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) pro virtuální pole StorSimple.
    2. Pokud se jedná o vaše první zařízení, které se registruje u této služby, zobrazí se vám **šifrovací klíč pro data služby**. Klíč zkopírujte a uložte na bezpečném místě. Tento klíč je vyžadován s registračním klíčem služby pro registraci dalších zařízení se službou StorSimple Device Manager. 
       
       Pokud se nejedná o první zařízení, které registrujete u této služby, budete muset poskytnout šifrovací klíč dat služby. Další informace naleznete v tématu získání [šifrovacího klíče dat služby](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) v místním webovém uživatelském prostředí.
    3. Klepněte na tlačítko **Registrovat**. Tím se zařízení restartuje. Možná budete muset počkat 2-3 minuty, než je zařízení úspěšně zaregistrováno. Po restartování zařízení budete převedena na přihlašovací stránku.
       
       ![](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Vraťte se na Azure Portal. Přejděte na **Všechny prostředky**, vyhledejte službu Správce zařízení StorSimple.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. Ve filtrovaném seznamu vyberte službu Správce zařízení StorSimple a přejděte na **položku Správa > zařízení**. V okně **Zařízení** ověřte, zda se zařízení úspěšně připojilo ke službě a zda má stav **Připraveno k nastavení**.
    
    ![Konfigurace souborového serveru](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>Krok 2: Konfigurace zařízení jako souborového serveru
Proveďte na [webu Azure Portal](https://portal.azure.com/) následující kroky a dokončete požadované nastavení zařízení.

#### <a name="to-configure-the-device-as-file-server"></a>Konfigurace zařízení jako souborového serveru
1. Přejděte do služby Správce zařízení StorSimple a přejděte na **způsob správy > zařízení**. V okně **Zařízení** vyberte zařízení, které jste právě vytvořili. Toto zařízení by se zobrazí jako **připraven o nastavení**.
   
   ![Konfigurace souborového serveru](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Klikněte na zařízení a zobrazí se bannerová zpráva oznamující, že zařízení je připraveno k nastavení.
   
    ![Konfigurace souborového serveru](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Na panelu příkazů klikněte na **Konfigurovat.** Tím se otevře okno **Configure.** V okně **Konfigurovat** postupujte takto:
   
   1. Název souborového serveru je automaticky naplněn.
    
   2. Ujistěte se, že šifrování cloudového úložiště je **nastaveno**na povoleno . Tím se zašifrují všechna data, která jsou odeslána do cloudu. 
    
   3. 256bitový klíč AES se používá s uživatelem definovaným klíčem pro šifrování. Zadejte 32znakovou klávesu a pak ji znovu zadejte, abyste ji potvrdili. Zaznamenejte klíč v aplikaci pro správu klíčů pro budoucí použití.
    
   4. Chcete-li zadat pověření účtu úložiště, která mají být použita se zařízením, klepněte na tlačítko **Konfigurovat požadovaná nastavení.** Pokud nejsou nakonfigurována žádná pověření účtu úložiště, klikněte na **Přidat nové.** **Ujistěte se, že účet úložiště, který používáte, podporuje objekty BLOB bloku. Objekty BLOB stránky nejsou podporovány.** Další informace o [blobch bloků a objektech BLOB stránky](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).
   
      ![Konfigurace souborového serveru](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. V okně **Přidat přihlašovací údaje účtu úložiště** postupujte takto: 

    1. Zvolte aktuální předplatné, pokud je účet úložiště ve stejném předplatném jako služba. Zadejte další je účet úložiště je mimo předplatné služby. 
    
    2. V rozevíracím seznamu zvolte existující účet úložiště. 
    
    3. Umístění bude automaticky vyplněno na základě zadaného účtu úložiště. 
    
    4. Povolte TLS a zajistěte zabezpečený síťový komunikační kanál mezi zařízením a cloudem.
    
    5. Kliknutím na **Přidat** přidejte toto přihlašovací údaje účtu úložiště. 
   
        ![Konfigurace souborového serveru](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. Po úspěšném vytvoření pověření účtu úložiště bude okno **Konfigurovat** aktualizováno tak, aby zobrazovala zadané přihlašovací údaje účtu úložiště. Klikněte na **Konfigurovat**.
   
   ![Konfigurace souborového serveru](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   Uvidíte, že souborový server je vytvářen. Po úspěšném vytvoření souborového serveru budete upozorněni.
   
   ![Konfigurace souborového serveru](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   Stav zařízení se také změní na **Online**.
   
   ![Konfigurace souborového serveru](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   Můžete pokračovat přidáním sdílené složky.

## <a name="step-3-add-a-share"></a>Krok 3: Přidání sdílené položky
Provedením následujících kroků na webu [Azure Portal](https://portal.azure.com/) vytvořte sdílenou složku.

#### <a name="to-create-a-share"></a>Vytvoření sdílené složky
1. Vyberte zařízení souborového serveru, které jste nakonfigurovali v předchozím kroku, a klepněte na tlačítko **...** (nebo klepněte pravým tlačítkem myši). V místní nabídce vyberte **Přidat sdílenou složku**. Případně můžete na panelu příkazů zařízení kliknout na **+ Přidat sdílet.**
   
   ![Přidání sdílené složky](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Zadejte následující nastavení sdílení:

   1. Jedinečný název pro váš podíl. Název musí být řetězec, který obsahuje 3 až 127 znaků.
    
   2. Volitelný **popis** sdílené složky. Popis pomůže identifikovat vlastníky akcií.
    
   3. A **Type** pro sdílenou složku. Typ může být **vrstvené** nebo **místně připnuté**, s vrstvené je výchozí. Pro úlohy, které vyžadují místní záruky, nízkou latenci a vyšší výkon, vyberte **místně připnutou sdílenou** složku. Pro všechna ostatní data vyberte **vrstvenou sdílenou** složku.
      Místně připnutá sdílená vrstva je silně zřízená a zajišťuje, že primární data ve sdílené složce zůstanou místní pro zařízení a nepřelije se do cloudu. Odstupňovaná sdílená akcie je na druhé straně tence zřízená. Když vytvoříte vrstvenou sdílenou složku, 10 % místa se zřídí na místní úrovni a 90 % místa se zřídí v cloudu. Například pokud jste zřídit svazek 1 TB, 100 GB by se nasytit v místním prostoru a 900 GB by se používá v cloudu, když datové vrstvy. To zase znamená, že pokud vám dojde všechny místní místo na zařízení, nelze zřídit vrstvené sdílené složky.
   
   4. V poli **Nastavit výchozí úplná oprávnění** přiřaďte oprávnění uživateli nebo skupině, která k této sdílené položce přistupuje. Zadejte jméno uživatele nebo skupiny uživatelů ve formátu *contoso.com.\@* Doporučujeme použít skupinu uživatelů (namísto jednoho uživatele) k povolení oprávnění správce pro přístup k těmto sdíleným položkám. Jakmile tady přiřadíte oprávnění, můžete pak tato oprávnění upravit pomocí Průzkumníka souborů.
   
   5. Chcete-li vytvořit sdílenou složku, klepněte na tlačítko **Přidat.** 
    
       ![Přidání sdílené složky](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
       Zobrazí se oznámení o probíhajícím vytváření sdílené složky.
   
       ![Přidání sdílené složky](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
      Po vytvoření sdílené složky se vytvoří okno **Sdílené složky** tak, aby odráželo novou sdílenou složku. Ve výchozím nastavení je pro sdílenou složku povoleno monitorování a zálohování.
   
      ![Přidání sdílené složky](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>Krok 4: Připojení ke sdílené položce
Nyní se budete muset připojit k jedné nebo více sdíleným položkám, které jste vytvořili v předchozím kroku. Tyto kroky proveďte u hostitele systému Windows Server připojeného k virtuálnímu poli StorSimple.

#### <a name="to-connect-to-the-share"></a>Připojení ke sdílené položce
1. Stiskněte ![](./media/storsimple-virtual-array-deploy3-fs-setup/image22.png) klávesu + R. V okně Spustit zadejte jako cestu *název &lt;&gt; souborového serveru&#92;&#92;* a nahraďte název *souborového serveru* názvem zařízení, který jste přiřadili k souborovému serveru. Klikněte na tlačítko **OK**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Tím se otevře Průzkumník souborů. Sdílené složky, které jste vytvořili, by se teď měly zobrazit jako složky. Pokud chcete zobrazit obsah sdílené složky, vyberte příslušnou složku a dvakrát na ni klikněte.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. Nyní můžete do těchto sdílených složek přidávat soubory a provést zálohu.

## <a name="next-steps"></a>Další kroky
Přečtěte si, jak používat místní webové uživatelské prostředí ke [správě virtuálního pole StorSimple](storsimple-ova-web-ui-admin.md).


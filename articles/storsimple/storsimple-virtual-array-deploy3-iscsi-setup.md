---
title: Nastavení serveru Microsoft Azure StorSimple Virtual Array iSCSI | Dokumenty společnosti Microsoft
description: Popisuje, jak provést počáteční nastavení, zaregistrovat server ISCSI StorSimple a dokončit nastavení zařízení.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 4db116d1-978b-48e8-b572-a719a8425dbc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 4560ca2b07826e2a071f515f147dfab8cbec3624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254492"
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>Nasazení virtuálního pole StorSimple – nastavení jako server iSCSI prostřednictvím portálu Azure

![tok procesu nastavení iscsi](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Přehled

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Tento kurz nasazení se vztahuje na virtuální pole Microsoft Azure StorSimple. Tento kurz popisuje, jak provést počáteční nastavení, zaregistrovat server ISCSI StorSimple, dokončit nastavení zařízení a potom vytvořit, připojit, inicializovat a formátovat svazky na virtuálním poli StorSimple nakonfigurovaném jako server iSCSI. 

Postupy popsané zde trvat přibližně 30 minut až 1 hodinu na dokončení. Informace publikované v tomto článku platí pouze pro virtuální pole StorSimple.

## <a name="setup-prerequisites"></a>Požadavky instalačního programu 

Před konfigurací a nastavením virtuálního pole StorSimple zkontrolujte, zda:

* Zřídíte virtuální pole a připojíte se k němu, jak je popsáno v [poli Nasazení storsimple virtuálního pole – zřízení virtuálního pole v technologii Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) nebo [nasazení virtuálního pole StorSimple – zřízení virtuálního pole ve společnosti VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Máte registrační klíč služby ze služby StorSimple Device Manager, kterou jste vytvořili pro správu virtuálních polí StorSimple. Další informace naleznete **v tématu Krok 2: Získání registračního klíče služby** v [tématu Nasazení virtuálního pole StorSimple – příprava portálu](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
* Pokud se jedná o druhé nebo následující virtuální pole, které registrujete u existující služby Správce zařízení StorSimple, měli byste mít šifrovací klíč dat služby. Tento klíč byl vygenerován při úspěšném zaregistrování prvního zařízení v této službě. Pokud jste ztratili tento klíč, přečtěte si téma **Získání šifrovacího klíče dat služby** [v části Použití webového uživatelského prostředí ke správě virtuálního pole StorSimple](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Podrobné nastavení

Pomocí následujících podrobných pokynů nastavte a nakonfigurujte virtuální pole StorSimple:

* [Krok 1: Dokončení nastavení místního webového uživatelského rozhraní a registrace zařízení](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* Krok 2: Dokončení požadovaného nastavení zařízení
* [Krok 3: Přidání svazku](#step-3-add-a-volume)
* [Krok 4: Připojení, inicializaci a formátování svazku](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Krok 1: Dokončení nastavení místního webového uživatelského rozhraní a registrace zařízení

#### <a name="to-complete-the-setup-and-register-the-device"></a>Chcete-li dokončit nastavení a zaregistrovat zařízení

1. Otevřete okno prohlížeče. Připojení k webovému uživatelskému uživatelskému uživatelskému zařízení:
   
    `https://<ip-address of network interface>`
   
    Použijte adresu URL připojení uvedenou v předchozím kroku. Zobrazí se chyba s upozorněním, že došlo k potížím s certifikátem zabezpečení webu. Klepněte **na tlačítko Pokračovat na tuto webovou stránku**.
   
    ![Chyba certifikátu zabezpečení](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. Přihlaste se k webovému uživatelskému rozhraní virtuálního zařízení jako **StorSimpleAdmin**. Zadejte heslo správce zařízení, které jste změnili v kroku 3: Spuštění virtuálního zařízení v [nasazení storsimple virtuálnípole - zřízení virtuálního zařízení v Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) nebo [nasazení StorSimple Virtual Array - Zřízení virtuálního zařízení v VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
    ![Přihlašovací stránka](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. Budete převedena na **domovskou** stránku. Tato stránka popisuje různá nastavení potřebná ke konfiguraci a registraci virtuálního zařízení pomocí služby StorSimple Device Manager. Nastavení **sítě**, **nastavení webového proxy serveru**a **času** jsou volitelné. Jedinými požadovanými nastaveními jsou **nastavení zařízení** a **nastavení cloudu**.
   
    ![Domovská stránka](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. Na stránce **Nastavení sítě** v části **Síťová rozhraní**bude data 0 automaticky konfigurována za vás. Každé síťové rozhraní je ve výchozím nastavení nastaveno tak, aby automaticky získalo adresu IP (DHCP). Proto bude automaticky přiřazena IP adresa, podsíť a brána (pro Protokol IPv4 i IPv6).
   
    Při plánování nasazení zařízení jako serveru iSCSI (zřídit blokové úložiště) doporučujeme zakázat možnost **Získat IP adresu automaticky** a nakonfigurovat statické IP adresy.
   
    ![Stránka Nastavení sítě](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    Pokud jste přidali více než jedno síťové rozhraní během zřizování zařízení, můžete je nakonfigurovat zde. Poznámka: síťové rozhraní můžete nakonfigurovat pouze jako protokol IPv4 nebo jako protokol IPv4 i IPv6. Pouze konfigurace Protokolu IPv6 nejsou podporovány.
5. Servery DNS jsou vyžadovány, protože se používají, když se zařízení pokusí komunikovat s poskytovateli služeb cloudového úložiště nebo přeložit zařízení podle názvu, pokud je nakonfigurováno jako souborový server. Na stránce **Nastavení sítě** pod **servery DNS**:
   
   1. Primární a sekundární server DNS bude automaticky nakonfigurován. Pokud se rozhodnete konfigurovat statické adresy IP, můžete zadat servery DNS. Pro vysokou dostupnost doporučujeme nakonfigurovat primární a sekundární server DNS.
   2. Klikněte na **Použít**. To platí a ověří nastavení sítě.
6. Na stránce **Nastavení zařízení:**
   
   1. **Přiřaďte** svému zařízení jedinečný název. Tento název může mít 1-15 znaků a může obsahovat písmeno, čísla a pomlčky.
   2. Klepněte na ![ikonu](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) **iSCSI na ikonu serveru iSCSI** **pro typ** vytvářeného zařízení. Server iSCSI vám umožní zřídit blokové úložiště.
   3. Určete, zda má být toto zařízení připojeno k doméně. Pokud je vaše zařízení serverem iSCSI, je připojení k doméně volitelné. Pokud se rozhodnete nepřipojovat server iSCSI k doméně, klepněte na **tlačítko Použít**, počkejte na použití nastavení a přejděte k dalšímu kroku.
      
       Pokud chcete připojit zařízení k doméně. Zadejte **název domény**a klepněte na tlačítko **Použít**.
      
      > [!NOTE]
      > Pokud připojíte server iSCSI k doméně, ujistěte se, že vaše virtuální pole je ve vlastní organizační jednotce (OU) pro službu Microsoft Azure Active Directory a žádné objekty zásad skupiny (GPO) se na něj nepoužijí.
      > 
      > 
   4. Zobrazí se dialogové okno. Zadejte pověření domény v určeném formátu. Klikněte na ikonu zaškrtnutí ![ikona zaškrtnutí](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png). Pověření domény budou ověřena. Pokud jsou pověření nesprávná, zobrazí se chybová zpráva.
      
       ![Přihlašovací údaje](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. Klikněte na **Použít**. To platí a ověří nastavení zařízení.
7. (Volitelně) nakonfigurujte webový proxy server. Konfigurace proxy serveru je volitelná, ale můžete ji provést jenom v tomto kroku.
   
    ![konfigurace webového proxy serveru](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    Na stránce **webového proxy** serveru:
   
   1. Zadej adresu **URL webového proxy serveru** v tomto formátu: *http:\//host-IP adresa* nebo *FQDN:Číslo portu*. Všimněte si, že adresy URL HTTPS nejsou podporovány.
   2. Zadejte **ověřování** jako **základní** nebo **žádné**.
   3. Pokud používáte ověřování, budete také muset zadat **uživatelské jméno** a **heslo**.
   4. Klikněte na **Použít**. Tím ověříte a použijete nakonfigurované nastavení webového proxy serveru.
8. (Volitelně) nakonfigurujte nastavení času pro vaše zařízení, například časové pásmo a primární a sekundární servery NTP. Servery NTP jsou vyžadovány, protože zařízení musí synchronizovat čas, aby se mohlo ověřit u poskytovatelů cloudových služeb.
   
    ![Nastavení času](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    Na stránce **Nastavení času:**
   
   1. V rozevíracím seznamu vyberte **časové pásmo** na základě geografické polohy, ve které je zařízení nasazováno. Výchozí časové pásmo pro vaše zařízení je PST. Toto časové pásmo bude zařízení používat pro všechny naplánované operace.
   2. Zadejte **primární server NTP** pro vaše zařízení nebo přijměte výchozí hodnotu time.windows.com. Ujistěte se, že vaše síť umožňuje přenos dat NTP z vašeho datového centra na internet.
   3. Volitelně zadejte **sekundární ntp server** pro vaše zařízení.
   4. Klikněte na **Použít**. Tím ověříte a použijete nakonfigurované nastavení času.
9. Nakonfigurujte nastavení cloudu pro vaše zařízení. V tomto kroku dokončíte konfiguraci místního zařízení a poté zařízení zaregistrujete službou Správce zařízení StorSimple.
   
   1. Zadejte **registrační klíč služby,** který jste dostali v **kroku 2: Získejte registrační klíč služby** v [nasazení storsimple virtuálního pole - připravte portál](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
   2. Pokud se nejedná o první zařízení, které registrujete u této služby, budete muset poskytnout **šifrovací klíč pro data služby**. Tento klíč je vyžadován s registračním klíčem služby pro registraci dalších zařízení se službou StorSimple Device Manager. Další informace naleznete [v tématu Získání šifrovacího klíče dat služby](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) v místním webovém uživatelském prostředí.
   3. Klepněte na tlačítko **Registrovat**. Tím se zařízení restartuje. Možná budete muset počkat 2-3 minuty, než je zařízení úspěšně zaregistrováno. Po restartování zařízení budete převedena na přihlašovací stránku.
      
      ![Registrace zařízení](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Vraťte se na Azure Portal.
11. Přejděte do okna **Zařízení** vaší služby. Pokud máte velké množství prostředků, klikněte na **Všechny zdroje**, klikněte na název služby (v případě potřeby vyhledejte jej) a potom klikněte na **Zařízení**.
12. V okně **Zařízení** ověřte, zda se zařízení úspěšně připojilo ke službě, a to tak, že se vydíváte na stav. Stav zařízení musí být **Připraveno k nastavení**.
    
    ![Registrace zařízení](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>Krok 2: Konfigurace zařízení jako serveru iSCSI

Proveďte na webu Azure Portal následující kroky a dokončete požadované nastavení zařízení.

#### <a name="to-configure-the-device-as-iscsi-server"></a>Konfigurace zařízení jako serveru iSCSI

1. Přejděte do služby Správce zařízení StorSimple a přejděte na **způsob správy > zařízení**. V okně **Zařízení** vyberte zařízení, které jste právě vytvořili. Toto zařízení by se zobrazí jako **připraven o nastavení**.
   
    ![Konfigurace zařízení jako serveru iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. Klikněte na zařízení a zobrazí se bannerová zpráva oznamující, že zařízení je připraveno k nastavení.
   
    ![Konfigurace zařízení jako serveru iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. Na panelu příkazů zařízení klikněte na **Konfigurovat.** Tím se otevře okno **Configure.** V okně **Konfigurovat** postupujte takto:
   
   * Název serveru iSCSI je automaticky naplněn.
   * Ujistěte se, že šifrování cloudového úložiště je **nastaveno**na povoleno . Tím je zajištěno, že data odeslaná ze zařízení do cloudu jsou šifrována.
   * Zadejte 32místný šifrovací klíč a zaznamenejte jej do aplikace pro správu klíčů pro budoucí použití.
   * Vyberte účet úložiště, který se má používat se zařízením. V tomto předplatném můžete vybrat existující účet úložiště nebo můžete kliknutím na **Přidat** vybrat účet z jiného předplatného.
     
     ![Konfigurace zařízení jako serveru iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. Chcete-li dokončit nastavení serveru iSCSI, klepněte na **tlačítko Konfigurovat.**
   
    ![Konfigurace zařízení jako serveru iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. Budete upozorněni, že probíhá vytváření serveru iSCSI. Po úspěšném vytvoření serveru iSCSI se okno **Devices** aktualizuje a odpovídající stav zařízení je **online**.
   
    ![Konfigurace zařízení jako serveru iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>Krok 3: Přidání svazku

1. V okně **Zařízení** vyberte zařízení, které jste právě nakonfigurovali jako server iSCSI. Klikněte na **...** (případně klikněte pravým tlačítkem myši v tomto řádku) a v místní nabídce vyberte **Přidat hlasitost**. Můžete také klepnout na **+ Přidat hlasitost** z panelu příkazů. Tím se otevře okno **Přidat hlasitost.**
   
    ![Přidání svazku](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. V okně **Přidat objem** postupujte takto:
   
   * Do pole **Název svazku** zadejte jedinečný název svazku. Název musí být řetězec, který obsahuje 3 až 127 znaků.
   * V rozevíracím seznamu **Typ** určete, zda se má vytvořit **vrstvený** nebo **místně vázaný** svazek. U úloh, které vyžadují místní záruky, nízkou latenci a vyšší výkon, vyberte **místně vázaný** **svazek**. Pro všechna ostatní data vyberte **Vrstvený** **svazek**.
   * V poli **Kapacita** zadejte velikost svazku. Vrstvený svazek musí být mezi 500 GB a 5 TB a místně vázaný svazek musí být mezi 50 GB a 500 GB.
     
     Místně vázaný svazek je hustě zřízený a zajišťuje, že primární data ve svazku zůstanou na zařízení a neprolíná do cloudu.
     
     Vrstvený svazek na druhé straně je tence zřízené. Když vytvoříte vrstvený svazek, přibližně 10 % místa se zřídí na místní úrovni a 90 % místa se zřídí v cloudu. Například pokud jste zřídit svazek 1 TB, 100 GB by se nasytit v místním prostoru a 900 GB by se používá v cloudu, když datové vrstvy. To zase znamená, že pokud vám dojdou všechny místní místo na zařízení, nelze zřídit vrstvené sdílené složky (protože 10 % nebude k dispozici).
     
     ![Přidání svazku](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * Klepněte na **položku Připojení hostitelé**, vyberte záznam řízení přístupu (ACR) odpovídající iniciátoru iSCSI, který chcete připojit k tomuto svazku, a klepněte na tlačítko **Vybrat**. <br><br> 
3. Chcete-li přidat nového připojeného hostitele, klepněte na tlačítko **Přidat nový**, zadejte název hostitele a jeho kvalifikovaný název iSCSI (IQN) a klepněte na tlačítko **Přidat**. Pokud nemáte IQN, přejděte do [dodatku A: Získejte IQN hostitele systému Windows Server](#appendix-a-get-the-iqn-of-a-windows-server-host).
   
      ![Přidání svazku](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. Po dokončení konfigurace svazku klepněte na tlačítko **OK**. Svazek bude vytvořen se zadaným nastavením a zobrazí se oznámení. Ve výchozím nastavení bude pro svazek povoleno monitorování a zálohování.
   
     ![Přidání svazku](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. Chcete-li ověřit, zda byl svazek úspěšně vytvořen, přejděte do okna **Svazky.** Měl by se zobrazit svazek uvedený.
   
   ![Přidání svazku](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Krok 4: Připojení, inicializaci a formátování svazku

Proveďte následující kroky pro připojení, inicializaci a formátování svazků StorSimple na hostiteli systému Windows Server.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Připojení, inicializace a formátování svazku

1. Otevřete **iniciátor iSCSI** na příslušném serveru.
2. V okně **iSCSI Initiator Properties (Vlastnosti iniciátoru iSCSI)** na kartě **Zjišťování** klikněte na **Vyhledat portál**.
   
    ![objevte portál](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. V dialogovém okně **Zjistit cílový portál** zadejte IP adresu svého síťového rozhraní s podporou iSCSI a potom klikněte na **OK**.
   
    ![IP adresa](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. V okně **iSCSI Initiator Properties** (Vlastnosti iniciátoru iSCSI) na kartě **Cíle** najděte část **Zjištěné cíle**. (Každý svazek bude zjištěným cílem.) Stav zařízení by se měl zobrazit jako **Neaktivní**.
   
    ![objevené cíle](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. Vyberte cílové zařízení a klepněte na tlačítko **Připojit**. Po připojení zařízení by se měl stav změnit na **Připojeno**. (Další informace o použití iniciátoru iSCSI společnosti Microsoft naleznete v [tématu Instalace a konfigurace iniciátoru iSCSI][1]společnosti Microsoft .
   
    ![vybrat cílové zařízení](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. Na hostiteli s Windows stiskněte klávesu s logem Windows + X a potom klikněte na **Spustit**.
7. V dialogovém okně **Spustit** zadejte **Diskmgmt.msc**. Klikněte na **OK**. Zobrazí se dialogové okno **Správa disků**. V pravém podokně se zobrazí svazky na vašem hostiteli.
8. V okně **Správa disků** se připojené svazky zobrazí tak, jak znázorňuje následující obrázek. Klikněte pravým tlačítkem myši na zjištěný svazek (klikněte na název disku) a potom klikněte na **Online**.
   
    ![správa disků](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. Klepněte pravým tlačítkem myši a vyberte **možnost Inicializovat disk**.
   
    ![inicializovat disk 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. V dialogovém okně vyberte disky, které chcete inicializovat, a klepněte na tlačítko **OK**.
    
    ![inicializovat disk 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. Spustí se Průvodce novým jednoduchým svazkem. Vyberte velikost disku a klepněte na tlačítko **Další**.
    
    ![Průvodce novým svazkem 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. Přiřaďte svazku písmeno jednotky a klepněte na tlačítko **Další**.
    
    ![Průvodce novým svazkem 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. Zadejte parametry pro formátování svazku. **V systému Windows Server je podporován pouze systém souborů NTFS.** Nastavte velikost alokační jednotky na 64 kM. Zadejte štítek pro svazek. Doporučujeme, aby byl tento název shodný s názvem svazku, který jste zadali ve virtuálním poli StorSimple. Klikněte na **Další**.
    
    ![Průvodce novým svazkem 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. Zkontrolujte hodnoty svazku a klepněte na tlačítko **Dokončit**.
    
    ![Průvodce novým svazkem 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    Svazky se zobrazí jako **Online** na stránce **Správa disků.**
    
    ![svazky on-line](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak používat místní webové uživatelské prostředí ke [správě virtuálního pole StorSimple](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Dodatek A: Získání IQN hostitele systému Windows Server

Pomocí následujících kroků získejte název IQN (iSCSI Qualified Name) hostitele Windows se systémem Windows Server® 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Získání názvu IQN hostitele s Windows

1. Na hostiteli s Windows spusťte iniciátor iSCSI od Microsoftu.
2. V okně **iSCSI Initiator Properties** (Vlastnosti iniciátoru iSCSI) na kartě **Konfigurace** vyberte a zkopírujte řetězec z pole **Název iniciátoru**.
   
    ![Vlastnosti iniciátoru iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. Uložte tento řetězec.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx




---
title: Nastavení serveru iSCSI pro virtuální pole Microsoft Azure StorSimple | Microsoft Docs
description: Popisuje, jak provést počáteční nastavení, zaregistrovat server iSCSI StorSimple a dokončit instalaci zařízení.
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
ms.openlocfilehash: 6289f335234c9b7efec02a18e12b40a1140c1b3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94956547"
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>Nasazení virtuálního pole StorSimple – nastavení jako serveru iSCSI prostřednictvím Azure Portal

![tok procesu nastavení iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Přehled

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Tento kurz nasazení se vztahuje na virtuální pole Microsoft Azure StorSimple. V tomto kurzu se dozvíte, jak provést počáteční nastavení, zaregistrovat StorSimple Server iSCSI, dokončit instalaci zařízení a pak vytvořit, připojit, inicializovat a naformátovat svazky ve virtuálním poli StorSimple nakonfigurovaném jako server iSCSI. 

Dokončení popsané procedury trvá přibližně 30 minut až 1 hodinu. Informace publikované v tomto článku se týkají pouze virtuálních polí StorSimple.

## <a name="setup-prerequisites"></a>Požadavky instalačního programu 

Před konfigurací a nastavením StorSimple virtuálního pole se ujistěte, že:

* Zřídili jste virtuální pole a připojili se k němu, jak je popsáno v tématu [nasazení virtuálního pole StorSimple – zřízení virtuálního pole v Hyper-V](./storsimple-virtual-array-deploy2-provision-hyperv.md) nebo nasazení virtuálního pole [StorSimple – zřízení virtuálního pole ve VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Máte registrační klíč služby ze služby StorSimple Správce zařízení, kterou jste vytvořili pro správu virtuálních polí StorSimple. Další informace najdete v části **Krok 2: získání registračního klíče služby** v [nasazení StorSimple Virtual Array – Příprava portálu](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
* Pokud se jedná o druhé nebo následné virtuální pole, které zaregistrujete pomocí existující služby StorSimple Správce zařízení, měli byste mít šifrovací klíč dat služby. Tento klíč byl vygenerován při úspěšné registraci prvního zařízení s touto službou. Pokud jste tento klíč ztratili, přečtěte si téma **získání šifrovacího klíče dat služby** v tématu [použití webového uživatelského rozhraní pro správu vašeho virtuálního pole StorSimple](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Podrobný postup instalace

Pomocí následujících podrobných pokynů nastavte a nakonfigurujte vaše virtuální pole StorSimple:

* [Krok 1: dokončení nastavení místního webového uživatelského rozhraní a registrace zařízení](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* Krok 2: dokončení nastavení požadovaného zařízení
* [Krok 3: Přidání svazku](#step-3-add-a-volume)
* [Krok 4: připojení, inicializace a formátování svazku](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Krok 1: dokončení nastavení místního webového uživatelského rozhraní a registrace zařízení

#### <a name="to-complete-the-setup-and-register-the-device"></a>Dokončení nastavení a registrace zařízení

1. Otevřete okno prohlížeče. Připojení k webovému typu webového uživatelského rozhraní:
   
    `https://<ip-address of network interface>`
   
    Použijte adresu URL připojení, kterou jste si poznamenali v předchozím kroku. Zobrazí se chyba oznamující, že došlo k potížím s certifikátem zabezpečení webu. Klikněte na tlačítko **pokračovat na tuto webovou stránku**.
   
    ![Chyba certifikátu zabezpečení](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. Přihlaste se k webovému uživatelskému rozhraní virtuálního zařízení jako **StorSimpleAdmin**. Zadejte heslo správce zařízení, které jste změnili v kroku 3: spuštění virtuálního zařízení v části [nasazení virtuálního pole StorSimple – zřízení virtuálního zařízení v Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) nebo [nasazení virtuálního pole StorSimple – zřízení virtuálního zařízení ve VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
    ![Přihlašovací stránka](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. Přejdete na **domovskou** stránku. Tato stránka popisuje různá nastavení potřebná ke konfiguraci a registraci virtuálního zařízení ve službě StorSimple Správce zařízení. Všimněte si, že nastavení **sítě**, **nastavení webového proxy serveru** a **Nastavení času** jsou volitelné. Jediná požadovaná nastavení jsou **nastavení zařízení** a **Nastavení cloudu**.
   
    ![Domovská stránka](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. Na stránce **nastavení sítě** v části **Síťová rozhraní** se pro vás automaticky nakonfigurují data 0. Každé síťové rozhraní je standardně nastavené tak, aby se IP adresa automaticky získala (DHCP). Proto se automaticky přiřadí IP adresa, podsíť a brána (pro IPv4 a IPv6).
   
    Při plánování nasazení zařízení jako serveru iSCSI (za účelem zřízení blokového úložiště) doporučujeme zakázat možnost **získat IP adresu automaticky** a nakonfigurovat statické IP adresy.
   
    ![Stránka nastavení sítě](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    Pokud jste během zřizování zařízení přidali více než jedno síťové rozhraní, můžete je nakonfigurovat tady. Všimněte si, že vaše síťové rozhraní můžete nakonfigurovat jenom jako IPv4, nebo jako IPv4 i IPv6. Pouze konfigurace protokolu IPv6 nejsou podporovány.
5. Servery DNS se vyžadují, protože se používají, když se vaše zařízení pokusí komunikovat s poskytovateli služeb cloudového úložiště nebo překládá vaše zařízení podle názvu, pokud je nakonfigurovaný jako souborový server. Na stránce **nastavení sítě** v části **servery DNS**:
   
   1. Automaticky se nakonfigurují primární a sekundární server DNS. Pokud se rozhodnete nakonfigurovat statické IP adresy, můžete zadat servery DNS. Pro zajištění vysoké dostupnosti doporučujeme nakonfigurovat primární a sekundární server DNS.
   2. Klikněte na **Použít**. Tím se uplatní a ověří nastavení sítě.
6. Na stránce **nastavení zařízení** :
   
   1. Přiřaďte zařízení jedinečný **název** . Tento název může být 1-15 znaků a může obsahovat písmena, číslice a spojovníky.
   2. Klikněte na ikonu **serveru iSCSI** ikona ![ serveru iSCSI ](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) pro **typ** zařízení, které vytváříte. Server iSCSI vám umožní zřídit blokové úložiště.
   3. Určete, jestli chcete, aby toto zařízení bylo připojené k doméně. Pokud je vaše zařízení serverem iSCSI, je připojení k doméně volitelné. Pokud se rozhodnete připojit server iSCSI k doméně, klikněte na **použít**, počkejte, až se nastavení použije, a pak přejděte k dalšímu kroku.
      
       Pokud chcete zařízení připojit k doméně. Zadejte **název domény** a pak klikněte na **použít**.
      
      > [!NOTE]
      > Pokud se připojujete k serveru iSCSI k doméně, ujistěte se, že vaše virtuální pole je ve své vlastní organizační jednotce pro Microsoft Azure Active Directory a na něj se neaplikují žádné objekty zásad skupiny (GPO).
      > 
      > 
   4. Zobrazí se dialogové okno. Zadejte svoje přihlašovací údaje do domény v zadaném formátu. Klikněte na ikonu zaškrtnutí ![ikona zaškrtnutí](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png). Budou ověřena pověření domény. Pokud jsou přihlašovací údaje nesprávné, zobrazí se chybová zpráva.
      
       ![Přihlašovací údaje](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. Klikněte na **Použít**. Tím se uplatní a ověří nastavení zařízení.
7. (Volitelně) nakonfigurujte web proxy server. Konfigurace proxy serveru je volitelná, ale můžete ji provést jenom v tomto kroku.
   
    ![Konfigurace webového proxy serveru](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    Na stránce **webového proxy serveru** :
   
   1. Zadejte **adresu URL webového proxy serveru** v tomto formátu: *http: \/ /Host-IP adresa* nebo *plně kvalifikovaný název domény: číslo portu*. Upozorňujeme, že adresy URL protokolu HTTPS nejsou podporovány.
   2. Zadejte **ověřování** jako **základní** nebo **žádné**.
   3. Pokud používáte ověřování, budete také muset zadat **uživatelské jméno** a **heslo**.
   4. Klikněte na **Použít**. Tím se ověří a použije nakonfigurovaná nastavení webového proxy serveru.
8. (Volitelně) nakonfigurujte časová nastavení pro vaše zařízení, například časové pásmo a primární a sekundární servery NTP. Jsou vyžadovány servery NTP, protože vaše zařízení musí synchronizovat čas, aby se mohl ověřit u vašich poskytovatelů cloudových služeb.
   
    ![Nastavení času](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    Na stránce **Nastavení času** :
   
   1. V rozevíracím seznamu vyberte **časové pásmo** na základě geografického umístění, ve kterém se zařízení nasazuje. Výchozím časovým pásmem pro vaše zařízení je PST. Toto časové pásmo bude zařízení používat pro všechny naplánované operace.
   2. Zadejte **primární server NTP** pro vaše zařízení nebo přijměte výchozí hodnotu Time.Windows.com. Ujistěte se, že vaše síť umožňuje přenos dat NTP z vašeho datového centra na internet.
   3. Volitelně můžete zadat **sekundární server NTP** pro vaše zařízení.
   4. Klikněte na **Použít**. Tím se ověří a použije nakonfigurovaná nastavení času.
9. Nakonfigurujte nastavení cloudu pro vaše zařízení. V tomto kroku dokončíte konfiguraci místních zařízení a pak zaregistrujete zařízení ve službě StorSimple Správce zařízení.
   
   1. Zadejte **registrační klíč služby** , který jste získali v **kroku 2: získání registračního klíče služby** v části [nasazení StorSimple Virtual Array – Příprava portálu](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
   2. Pokud se nejedná o první zařízení, které zaregistrujete v rámci této služby, budete muset zadat **šifrovací klíč dat služby**. Tento klíč je vyžadován s registračním klíčem služby k registraci dalších zařízení ve službě StorSimple Správce zařízení. Další informace najdete v tématu [získání šifrovacího klíče dat služby](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) v místním webovém uživatelském rozhraní.
   3. Klikněte na **Zaregistrovat**. Tím se zařízení restartuje. Možná budete muset počkat na 2-3 minut, než se zařízení úspěšně zaregistruje. Jakmile se zařízení restartuje, přejdete na přihlašovací stránku.
      
      ![Registrovat zařízení](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Vraťte se na Azure Portal.
11. Přejděte do okna **zařízení** vaší služby. Pokud máte velké množství prostředků, klikněte na **všechny prostředky**, klikněte na název služby (v případě potřeby ho vyhledejte) a pak klikněte na **zařízení**.
12. V okně **zařízení** ověřte stav tím, že se zařízení úspěšně připojilo ke službě. Stav zařízení musí být **Připraveno k nastavení**.
    
    ![Nasazení zařízení](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>Krok 2: konfigurace zařízení jako serveru iSCSI

Provedením následujících kroků v Azure Portal dokončete požadované nastavení zařízení.

#### <a name="to-configure-the-device-as-iscsi-server"></a>Konfigurace zařízení jako serveru iSCSI

1. Přejít do služby StorSimple Správce zařízení a pak přejít na **> zařízení pro správu**. V okně **zařízení** vyberte zařízení, které jste právě vytvořili. Toto zařízení se zobrazí jako **připravené k nastavení**.
   
    ![Konfigurace zařízení jako serveru iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. Klikněte na zařízení a zobrazí se informační zpráva oznamující, že zařízení je připravené k instalaci.
   
    ![Konfigurace zařízení jako serveru iSCSI 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. Na panelu příkazů zařízení klikněte na **Konfigurovat** . Otevře se okno **Konfigurace** . V okně **Konfigurace** postupujte takto:
   
   * Název serveru iSCSI se vyplní automaticky.
   * Ujistěte se, že je šifrování cloudového úložiště nastavené na **povoleno**. Tím se zajistí, že data odesílaná ze zařízení do cloudu se zašifrují.
   * Zadejte šifrovací klíč 32 znaků a zaznamenejte ho v aplikaci pro správu klíčů pro pozdější použití.
   * Vyberte účet úložiště, který se má používat s vaším zařízením. V tomto předplatném můžete vybrat existující účet úložiště, nebo můžete kliknout na **Přidat** a vybrat účet z jiného předplatného.
     
     ![Konfigurace zařízení jako serveru iSCSI 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. Kliknutím na **Konfigurovat** dokončete nastavení serveru iSCSI.
   
    ![Konfigurace zařízení jako serveru iSCSI 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. Budete upozorněni, že probíhá vytváření serveru iSCSI. Po úspěšném vytvoření serveru iSCSI se okno **zařízení** aktualizuje a odpovídající stav zařízení je **online**.
   
    ![Konfigurace zařízení jako serveru iSCSI 5](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>Krok 3: Přidání svazku

1. V okně **zařízení** vyberte zařízení, které jste právě nakonfigurovali jako server iSCSI. Klikněte na **...** (případně klikněte pravým tlačítkem myši na tento řádek) a v místní nabídce vyberte **Přidat svazek**. Na panelu příkazů můžete také kliknout na **+ Přidat svazek** . Otevře se okno **Přidat svazek** .
   
    ![Přidat svazek](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. V okně **Přidat svazek** udělejte toto:
   
   * Do pole **název svazku** zadejte jedinečný název svazku. Název musí být řetězec, který obsahuje 3 až 127 znaků.
   * V rozevíracím seznamu **typ** určete, zda má být vytvořen **vrstvený** nebo **místně připojený** svazek. Pro úlohy, které vyžadují místní záruky, nízkou latenci a vyšší výkon, vyberte **místně připojený** **svazek**. U všech ostatních dat vyberte **vrstvený** **svazek**.
   * V poli **kapacita** zadejte velikost svazku. Vrstvený svazek musí být v rozsahu od 500 GB do 5 TB a místně připojený svazek musí být v rozmezí 50 GB až 500 GB.
     
     Místně připojený svazek je silným zajišťováním a zajišťuje, aby primární data ve svazku zůstala v zařízení a nepřesahují do cloudu.
     
     Vrstvený svazek na druhé straně je dynamicky zřízený. Když vytvoříte vrstvený svazek, přibližně 10% místa se zřídí na místní úrovni a 90% místa se zřídí v cloudu. Pokud jste například zřídili svazek o velikosti 1 TB, 100 GB by se nacházelo v místním prostoru a v cloudu se v případě datových vrstev používalo 900 GB. To naopak znamená, že pokud vyčerpáte z místního prostoru na zařízení, nemůžete zřídit vrstvenou sdílenou složku (protože 10% nebude k dispozici).
     
     ![Přidat svazek 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * Klikněte na **připojené hostitele**, vyberte záznam řízení přístupu (ACR) odpovídající iniciátoru iSCSI, ke kterému se chcete připojit, a potom klikněte na **Vybrat**. <br><br> 
3. Chcete-li přidat nového připojeného hostitele, klikněte na tlačítko **Přidat nový**, zadejte název hostitele a jeho kvalifikovaný název iSCSI (IQN) a pak klikněte na tlačítko **Přidat**. Pokud nemáte identifikátor IQN, Projděte si [Dodatek A: Získejte identifikátor IQN hostitele Windows serveru](#appendix-a-get-the-iqn-of-a-windows-server-host).
   
      ![Přidat svazek 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. Po dokončení konfigurace svazku klikněte na tlačítko **OK**. Vytvoří se svazek se zadaným nastavením a zobrazí se oznámení. Ve výchozím nastavení se pro svazek povolí monitorování a zálohování.
   
     ![Přidat svazek 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. Pokud chcete ověřit, že se svazek úspěšně vytvořil, otevřete okno **svazky** . Měl by se zobrazit uvedený svazek.
   
   ![Přidat svazek 5](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Krok 4: připojení, inicializace a formátování svazku

K připojení, inicializaci a formátování svazků StorSimple na hostiteli s Windows serverem proveďte následující kroky.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Připojení, inicializace a formátování svazku

1. Otevřete aplikaci **iniciátoru iSCSI** na příslušném serveru.
2. V okně **iSCSI Initiator Properties (Vlastnosti iniciátoru iSCSI)** na kartě **Zjišťování** klikněte na **Vyhledat portál**.
   
    ![vyhledat portál](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. V dialogovém okně **Zjistit cílový portál** zadejte IP adresu svého síťového rozhraní s podporou iSCSI a potom klikněte na **OK**.
   
    ![IP adresa](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. V okně **iSCSI Initiator Properties** (Vlastnosti iniciátoru iSCSI) na kartě **Cíle** najděte část **Zjištěné cíle**. (Každý svazek bude zjištěným cílem.) Stav zařízení by se měl zobrazovat jako **neaktivní**.
   
    ![zjištěné cíle](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. Vyberte cílové zařízení a pak klikněte na **připojit**. Po připojení zařízení by se měl stav změnit na **Připojeno**. (Další informace o použití iniciátoru iSCSI od Microsoftu najdete v tématu [instalace a Konfigurace iniciátoru iSCSI od Microsoftu][1].
   
    ![Vyberte cílové zařízení.](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. Na hostiteli s Windows stiskněte klávesu s logem Windows + X a potom klikněte na **Spustit**.
7. V dialogovém okně **Spustit** zadejte **Diskmgmt.msc**. Klikněte na **OK**. Zobrazí se dialogové okno **Správa disků**. V pravém podokně se zobrazí svazky na vašem hostiteli.
8. V okně **Správa disků** se připojené svazky zobrazí tak, jak znázorňuje následující obrázek. Klikněte pravým tlačítkem myši na zjištěný svazek (klikněte na název disku) a potom klikněte na **Online**.
   
    ![Správa disků](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. Klikněte pravým tlačítkem a vyberte **inicializovat disk**.
   
    ![inicializovat disk 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. V dialogovém okně vyberte disky, které mají být inicializovány, a poté klikněte na tlačítko **OK**.
    
    ![inicializovat disk 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. Spustí se Průvodce vytvořením jednoduchého svazku. Vyberte velikost disku a potom klikněte na tlačítko **Další**.
    
    ![Průvodce vytvořením svazku 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. Přiřaďte ke svazku písmeno jednotky a potom klikněte na tlačítko **Další**.
    
    ![Průvodce vytvořením svazku 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. Zadejte parametry pro formátování svazku. **V systému Windows Server je podporován pouze systém souborů NTFS.** Nastavte velikost alokační jednotky na 64 KB. Zadejte jmenovku pro svazek. Doporučuje se, aby tento název byl stejný jako název svazku, který jste zadali ve virtuálním poli StorSimple. Klikněte na **Next** (Další).
    
    ![Průvodce vytvořením svazku 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. Zkontrolujte hodnoty pro svazek a pak klikněte na **Dokončit**.
    
    ![Průvodce vytvořením svazku 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    Svazky se zobrazí na stránce **Správa disků** jako **online** .
    
    ![online svazky](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak pomocí místního webového uživatelského rozhraní [spravovat virtuální pole StorSimple](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Příloha A: získání názvu IQN hostitele Windows serveru

Pomocí následujících kroků získejte název IQN (iSCSI Qualified Name) hostitele Windows se systémem Windows Server® 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Získání názvu IQN hostitele s Windows

1. Na hostiteli s Windows spusťte iniciátor iSCSI od Microsoftu.
2. V okně **iSCSI Initiator Properties** (Vlastnosti iniciátoru iSCSI) na kartě **Konfigurace** vyberte a zkopírujte řetězec z pole **Název iniciátoru**.
   
    ![Vlastnosti iniciátoru iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. Uložte tento řetězec.

<!--Reference link-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee338480(v=ws.10)
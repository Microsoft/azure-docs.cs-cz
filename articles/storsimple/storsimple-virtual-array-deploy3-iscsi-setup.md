---
title: Nastavení serveru iSCSI společnosti Microsoft Azure StorSimple Virtual Array | Dokumentace Microsoftu
description: Popisuje, jak provést počáteční nastavení, zaregistrujte svůj server iSCSI StorSimple a dokončete nastavování zařízení.
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
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: c0a86b76622862b477d539c25dd98c925f09192c
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812034"
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>Nasazení virtuálních polí StorSimple – sady nahoru jako serveru iSCSI prostřednictvím portálu Azure portal

![tok procesu instalace iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Přehled

V tomto kurzu nasazení platí pro Microsoft Azure StorSimple Virtual Array. Tento kurz popisuje, jak provést počáteční nastavení, zaregistrujte svůj server iSCSI StorSimple, dokončit nastavení zařízení a pak vytvořit, připojit, inicializovat a formátování svazků StorSimple Virtual Array nakonfigurovaný jako serveru iSCSI. 

Popsané postupy na 1 hodinu, než zde trvat přibližně 30 minut. Informace zveřejněné v tomto článku se vztahují pouze na virtuálních polí StorSimple.

## <a name="setup-prerequisites"></a>Požadavky instalačního programu

Než začnete konfigurovat a nastavit StorSimple Virtual Array, ujistěte se, že:

* Zřízení virtuálního pole a jsou k němu připojená, jak je popsáno v [nasazení StorSimple Virtual Array – zřízení virtuálního pole v Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) nebo [nasazení StorSimple Virtual Array – zřízení virtuálního pole v prostředí VMware ](storsimple-virtual-array-deploy2-provision-vmware.md).
* Máte registrační klíč služby ze služby Správce zařízení StorSimple, kterou jste vytvořili pro správu vašich virtuálních polí StorSimple. Další informace najdete v tématu **krok 2: Získání registračního klíče služby** v [nasazení StorSimple Virtual Array – Příprava portálu](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
* Pokud je to druhém a dalším virtuální pole, které při registraci existující služby Správce zařízení StorSimple, měli byste šifrovací klíč dat služby. Tento klíč se vygeneroval při prvním zařízením byl úspěšně zaregistrován s touto službou. Pokud jste ztratili tento klíč, přečtěte si téma **získat šifrovací klíč dat služby** v [pomocí webového uživatelského rozhraní pro správu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Podrobné nastavení

Nastavit a nakonfigurovat StorSimple Virtual Array pomocí následujících kroků:

* [Krok 1: Dokončete instalaci místního webového uživatelského rozhraní a zaregistrujte zařízení](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* Krok 2: Dokončení instalace požadované zařízení
* [Krok 3: Přidat svazek](#step-3-add-a-volume)
* [Krok 4: Připojení, inicializace a formátování svazků](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Krok 1: Dokončete instalaci místního webového uživatelského rozhraní a zaregistrujte zařízení

#### <a name="to-complete-the-setup-and-register-the-device"></a>K dokončení instalace a registrace zařízení

1. Otevřete okno prohlížeče. Připojení k webové uživatelské rozhraní typu:
   
    `https://<ip-address of network interface>`
   
    Pomocí adresy URL pro připojení, které jste si poznamenali v předchozím kroku. Zobrazí se chyba oznamující, že dojde k problému s certifikátem zabezpečení webu. Klikněte na tlačítko **pokračovat k této webové stránce**.
   
    ![Chyba certifikátu zabezpečení](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. Přihlaste se do webového uživatelského rozhraní vašeho virtuálního zařízení jako **StorSimpleAdmin**. Zadejte heslo správce zařízení, která jste změnili v kroku 3: Spuštění virtuálního zařízení [nasazení StorSimple Virtual Array - zřizování virtuálního zařízení v Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) nebo [nasazení StorSimple Virtual Array - zřídit virtuální zařízení ve službě VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
    ![Přihlašovací stránka](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. Budete přesměrováni na **Domů** stránky. Tato stránka popisuje různá nastavení, které jsou potřeba ke konfiguraci a registraci virtuálního zařízení ve službě Správce zařízení StorSimple. Všimněte si, že **nastavení sítě**, **nastavení proxy webu**, a **nastavení času** jsou volitelné. Pouze požadované nastavení, musí být **nastavení zařízení** a **nastavení cloudu**.
   
    ![Domovská stránka](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. Na **nastavení sítě** stránky **síťová rozhraní**, DATA 0 se automaticky nakonfigurují za vás. Každé síťové rozhraní je ve výchozím nastavení, chcete-li získat IP adresu automaticky (DHCP). Proto adresa IP, podsítě a brána se automaticky přiřadí (pro adresy IPv4 a IPv6).
   
    Při plánování nasazení zařízení jako serveru iSCSI (k poskytování blokového úložiště), doporučujeme zakázat **získat IP adresu automaticky** řádku a nakonfigurovat statické IP adresy.
   
    ![Stránka nastavení sítě](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    Pokud přidáte více než jedno síťové rozhraní během zřizování zařízení, můžete je konfigurovat tady. Všimněte si, že síťové rozhraní můžete nakonfigurovat jako IPv4 nebo jen protokol IPv4 i IPv6. Nejsou podporovány pouze konfigurace IPv6.
5. Servery DNS jsou povinné, protože se používají, když se zařízení pokusí komunikovat s poskytovatelé cloudových služeb úložiště nebo k vyřešení vašeho zařízení podle názvu, pokud je nakonfigurovaný jako souborový server. Na **nastavení sítě** stránky **servery DNS**:
   
   1. Primární a sekundární server DNS se automaticky nakonfigurují. Pokud se rozhodnete ke konfiguraci statických IP adres, můžete určit servery DNS. Pro zajištění vysoké dostupnosti doporučujeme nakonfigurovat primární a sekundární server DNS.
   2. Klikněte na tlačítko **Použít**. Tím se použít a ověřit nastavení sítě.
6. Na **nastavení zařízení** stránky:
   
   1. Přiřaďte jedinečnou **název** do vašeho zařízení. Tento název může obsahovat 1 až 15 znaků a může obsahovat písmena, číslice a pomlčky.
   2. Klikněte na tlačítko **serveru iSCSI** ikonu ![ikona serveru iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) pro **typ** zařízení, kterou vytváříte. ISCSI server vám umožní k poskytování blokového úložiště.
   3. Zadejte, pokud chcete toto zařízení k doméně. Pokud vaše zařízení je iSCSI server, pak připojení k doméně je volitelné. Pokud se rozhodnete Nepřipojovat se k serveru iSCSI do domény, klikněte na tlačítko **použít**, počkejte nastavení, které se použijí a potom přejděte k dalšímu kroku.
      
       Pokud chcete připojení zařízení k doméně. Zadejte **název domény**a potom klikněte na tlačítko **použít**.
      
      > [!NOTE]
      > Pokud váš server iSCSI připojení k doméně, ujistěte se, že vaše virtuální pole je ve vlastní organizační jednotka (OU) pro Microsoft Azure Active Directory a žádné objekty zásad skupiny (GPO) se použijí k němu.
      > 
      > 
   4. Zobrazí se dialogové okno. Zadejte přihlašovací údaje domény v zadaném formátu. Klikněte na ikonu zaškrtnutí ![ikona zaškrtnutí](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png). Ověří přihlašovací údaje domény. Pokud přihlašovací údaje jsou nesprávné, zobrazí se chybová zpráva.
      
       ![přihlašovací údaje](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. Klikněte na tlačítko **Použít**. Tím se použít a ověřit nastavení zařízení.
7. (Volitelně) nakonfigurujte váš webový proxy server. Sice volitelné konfigurace webového proxy serveru, mějte na paměti, že pokud používáte webový proxy server, můžete pouze nakonfigurovat ji sem.
   
    ![Konfigurace webového proxy serveru](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    Na **webového proxy serveru** stránky:
   
   1. Zadat **webová adresa URL proxy serveru** v tomto formátu:  *http://host-IP adresu* nebo *FQDN: port číslo*. Všimněte si, že nejsou podporovány adresy URL HTTPS.
   2. Zadejte **ověřování** jako **základní** nebo **žádný**.
   3. Pokud používáte ověřování, budete také muset poskytnout **uživatelské jméno** a **heslo**.
   4. Klikněte na tlačítko **Použít**. Bude to ověřit a použít nastavení nakonfigurované webového proxy serveru.
8. (Volitelně) nakonfigurujte nastavení času pro vaše zařízení, jako je časové pásmo a primární a sekundární server NTP. Servery NTP jsou požadovány, protože zařízení musí synchronizovat čas tak, aby ho mohli ověřit poskytovatelé cloudových služeb.
   
    ![Nastavení času](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    Na **nastavení času** stránky:
   
   1. V rozevíracím seznamu vyberte **časové pásmo** na základě geografického umístění, ve kterém se nasazuje zařízení. Výchozí časové pásmo pro vaše zařízení je PST. Toto časové pásmo bude zařízení používat pro všechny naplánované operace.
   2. Zadejte **primárního serveru NTP** pro vaše zařízení nebo přijměte výchozí hodnotu time.windows.com. Ujistěte se, že vaše síť umožňuje přenos dat NTP z vašeho datového centra na internet.
   3. Volitelně můžete zadat **sekundární NTP server** pro vaše zařízení.
   4. Klikněte na tlačítko **Použít**. Bude to ověřit a použít nastavení nakonfigurovaném čase.
9. Konfigurovat nastavení cloudu pro vaše zařízení. V tomto kroku se dokončit konfiguraci místního zařízení a pak zaregistrujte zařízení ve vaší službě Správce zařízení StorSimple.
   
   1. Zadejte **registrační klíč služby** , které jste získali **krok 2: Získání registračního klíče služby** v [nasazení StorSimple Virtual Array – Příprava portálu](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
   2. Pokud to není první zařízení, které při registraci k této službě, je potřeba zadat **šifrovací klíč dat služby**. Tento klíč je požadován spolu s registračním klíčem služby k registraci dalších zařízení ve službě Správce zařízení StorSimple. Další informace najdete v [získat šifrovací klíč dat služby](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) na vaše místní webové uživatelské rozhraní.
   3. Klikněte na tlačítko **zaregistrovat**. Tato operace restartuje zařízení. Budete muset počkat 2 až 3 minut, než se zařízení úspěšně zaregistruje. Po restartování zařízení, budete přesměrováni na přihlašovací stránku.
      
      ![Zaregistrovat zařízení](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Vraťte se na Azure Portal.
11. Přejděte **zařízení** okna vaší služby. Pokud máte velké množství prostředků, klikněte na tlačítko **všechny prostředky**, klikněte na název služby (vyhledejte ho v případě potřeby) a potom klikněte na tlačítko **zařízení**.
12. Na **zařízení** okno, ověřte, že zařízení úspěšně připojilo ke službě kontrolou stavu. Stav zařízení musí být **Připraveno k nastavení**.
    
    ![Zaregistrovat zařízení](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>Krok 2: Konfigurace zařízení jako serveru iSCSI

Proveďte následující kroky na webu Azure Portal k dokončení instalace požadované zařízení.

#### <a name="to-configure-the-device-as-iscsi-server"></a>Ke konfiguraci zařízení jako serveru iSCSI

1. Přejděte do služby Správce zařízení StorSimple a potom přejděte ke **správy > zařízení**. V **zařízení** okno, vyberte zařízení, které jste právě vytvořili. Toto zařízení se zobrazují jako **připraveno k nastavení**.
   
    ![Nakonfigurujte zařízení jako serveru iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. Klikněte na zařízení a uvidíte banner zprávu, že zařízení je připraveno k instalaci.
   
    ![Nakonfigurujte zařízení jako serveru iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. Klikněte na tlačítko **konfigurovat** na panelu příkazů zařízení. Otevře **konfigurovat** okno. V **konfigurovat** okno, postupujte takto:
   
   * Automaticky se vyplní název serveru iSCSI.
   * Ujistěte se, že šifrování cloudového úložiště je nastavena na **povoleno**. Tím se zajistí, že je šifrovaná data odesílaná ze zařízení do cloudu.
   * Zadejte 32znakový šifrovací klíč a uložte ho aplikace pro správu klíčů pro budoucí použití.
   * Vyberte účet úložiště, který se má použít s vaším zařízením. V tomto předplatném, můžete vybrat existující účet úložiště, nebo můžete kliknout na **přidat** zvolit účet z jiného předplatného.
     
     ![Nakonfigurujte zařízení jako serveru iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. Klikněte na tlačítko **konfigurovat** dokončete nastavení serveru iSCSI.
   
    ![Nakonfigurujte zařízení jako serveru iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. Budete informováni, že vytvoření serveru iSCSI probíhá. Po úspěšném vytvoření serveru iSCSI **zařízení** okno se aktualizuje a odpovídající stav zařízení je **Online**.
   
    ![Nakonfigurujte zařízení jako serveru iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>Krok 3: Přidat svazek

1. V **zařízení** okno, vyberte zařízení, které jste právě nakonfigurovali jako serveru iSCSI. Klikněte na tlačítko **...**  (případně klikněte pravým tlačítkem na tento řádek) a v místní nabídce vyberte **přidat svazek**. Můžete také kliknout na **+ přidat svazek** na panelu příkazů. Otevře **přidat svazek** okno.
   
    ![Přidat svazek](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. V **přidat svazek** okno, postupujte takto:
   
   * V **název svazku** zadejte jedinečný název svazku. Název musí být řetězec, který obsahuje 3 až 127 znaků.
   * V **typ** rozevírací seznam seznamu, určete, jestli chcete vytvořit **Vrstvená** nebo **místně připnuté** svazku. Pro úlohy, které vyžadují místní záruky, nízkou latenci a vyšší výkon, vyberte **místně připnuté** **svazku**. Všechna ostatní data, vyberte **Vrstvená** **svazku**.
   * V **kapacity** zadejte velikost svazku. Vrstvený svazek musí být 500 GB až 5 TB a místně vázaný svazek musí být mezi 50 GB a 500 GB.
     
     Místně vázaný svazek je tlustě zřízený a zajišťuje, že primární data ve svazku zůstává v zařízení a uložená do cloudu.
     
     Vrstvený svazek na druhé straně je tence zřízený. Když vytvoříte vrstvený svazek, přibližně 10 % prostoru musí být zřízena na místní úrovni a je zajištěno 90 % prostoru v cloudu. Například pokud jste zřídili svazku 1 TB, 100 GB by jsou umístěny v místním prostoru a v cloudu se použije 900 GB při datovou vrstvou. To znamená zase je, že pokud vyčerpáte veškeré volné místo na zařízení, nemůžete zřídit vrstvenou sdílenou složku (protože 10 %, nebude k dispozici).
     
     ![Přidat svazek](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * Klikněte na tlačítko **připojení hostitelé**, vyberte záznam řízení přístupu (ACR) je iniciátor iSCSI, který chcete připojit k tomuto svazku a pak klikněte na odpovídající **vyberte**. <br><br> 
3. Přidání nové propojené hostitele, klikněte na tlačítko **přidat nový**, zadejte název hostitele a jeho iSCSI kvalifikovaný název (IQN) a pak klikněte na tlačítko **přidat**. Pokud je identifikátor IQN nemáte, přejděte na [dodatku A: Získání názvu IQN hostitele se Windows Server](#appendix-a-get-the-iqn-of-a-windows-server-host).
   
      ![Přidat svazek](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. Po dokončení konfigurace svazku, klikněte na tlačítko **OK**. Vytvoří se svazek se zadaným nastavením a zobrazí se oznámení. Ve výchozím nastavení monitorování a zálohování budou povolené pro svazek.
   
     ![Přidat svazek](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. Pokud chcete potvrdit, že svazek byl úspěšně vytvořen, přejděte na **svazky** okno. Měli byste vidět uvedené svazku.
   
   ![Přidat svazek](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Krok 4: Připojení, inicializace a formátování svazků

Proveďte následující kroky k připojení, inicializace a formátování svazků StorSimple na hostitelském Windows serveru.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Připojení, inicializace a formátování svazku

1. Otevřít **iniciátor iSCSI** aplikace na příslušném serveru.
2. V okně **iSCSI Initiator Properties (Vlastnosti iniciátoru iSCSI)** na kartě **Zjišťování** klikněte na **Vyhledat portál**.
   
    ![zjistit portálu](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. V dialogovém okně **Zjistit cílový portál** zadejte IP adresu svého síťového rozhraní s podporou iSCSI a potom klikněte na **OK**.
   
    ![IP adresa](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. V okně **iSCSI Initiator Properties** (Vlastnosti iniciátoru iSCSI) na kartě **Cíle** najděte část **Zjištěné cíle**. (Každý svazek bude zjištěná cíl.) U zařízení by se měl zobrazovat stav **Neaktivní**.
   
    ![zjištěné cíle](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. Vyberte cílové zařízení a potom klikněte na tlačítko **připojit**. Po připojení zařízení by se měl stav změnit na **Připojeno**. (Další informace o použití iniciátoru iSCSI od Microsoftu najdete v tématu [instalace a konfigurace iniciátoru Microsoft iSCSI][1].
   
    ![Vyberte cílové zařízení](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. Na hostiteli s Windows stiskněte klávesu s logem Windows + X a potom klikněte na **Spustit**.
7. V dialogovém okně **Spustit** zadejte **Diskmgmt.msc**. Klikněte na **OK**. Zobrazí se dialogové okno **Správa disků**. V pravém podokně se zobrazí svazky na vašem hostiteli.
8. V okně **Správa disků** se připojené svazky zobrazí tak, jak znázorňuje následující obrázek. Klikněte pravým tlačítkem myši na zjištěný svazek (klikněte na název disku) a potom klikněte na **Online**.
   
    ![Správa disků](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. Klikněte pravým tlačítkem a vyberte **inicializovat Disk**.
   
    ![Inicializovat disk 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. V dialogovém okně vyberte disky, inicializujte a potom klikněte na **OK**.
    
    ![Inicializace disku 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. Spustí se průvodce Nový jednoduchý svazek. Vyberte velikost disku a pak klikněte na tlačítko **Další**.
    
    ![Průvodce vytvořením svazku 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. Přiřazení písmene jednotky svazku a pak klikněte na tlačítko **Další**.
    
    ![Průvodce vytvořením svazku 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. Zadejte parametry naformátování svazku. **V systému Windows Server je podporována pouze v systému souborů NTFS.** Nastavte velikost alokační jednotky na 64 kB. Zadejte jmenovku svazku. Je součástí osvědčeného pro tento název bude stejný jako název svazku, který jste zadali na StorSimple Virtual Array. Klikněte na **Další**.
    
    ![Průvodce vytvořením svazku 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. Zkontrolujte hodnoty svazku a pak klikněte na tlačítko **Dokončit**.
    
    ![Průvodce vytvořením svazku 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    Svazky se zobrazí jako **Online** na **Správa disků** stránky.
    
    ![svazky online](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Další postup

Další informace o použití místní webové uživatelské rozhraní pro [spravovat StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Příloha A: Získání názvu IQN hostitele se systémem Windows Server

Pomocí následujících kroků získejte název IQN (iSCSI Qualified Name) hostitele Windows se systémem Windows Server® 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Získání názvu IQN hostitele s Windows

1. Na hostiteli s Windows spusťte iniciátor iSCSI od Microsoftu.
2. V okně **iSCSI Initiator Properties** (Vlastnosti iniciátoru iSCSI) na kartě **Konfigurace** vyberte a zkopírujte řetězec z pole **Název iniciátoru**.
   
    ![Vlastnosti iniciátoru iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. Uložte tento řetězec.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx




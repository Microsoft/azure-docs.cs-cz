---
title: Instalační program serveru iSCSI společnosti Microsoft Azure StorSimple virtuální pole | Microsoft Docs
description: Popisuje postup provedení počáteční instalace, zaregistrujte serveru iSCSI StorSimple a dokončení instalace zařízení.
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
ms.openlocfilehash: 076df176d7cd40c009aea27004fe0f4415999c80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23877304"
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>Nasadit pole virtuálního zařízení StorSimple – sadu až jako server se službou iSCSI prostřednictvím portálu Azure

![tok procesu instalace iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Přehled

V tomto kurzu nasazení se vztahuje na virtuální pole Microsoft Azure StorSimple. Tento kurz popisuje postup provedení počáteční instalace, registraci serveru iSCSI StorSimple, dokončení instalace zařízení a pak vytvořit, připojení, inicializace a formátování svazků na pole virtuální zařízení StorSimple nakonfigurovaný jako server se službou iSCSI. 

Postupy popsané v tomto poli trvat přibližně 30 minut na 1 hodinu k dokončení. Informace publikované v tomto článku se vztahují pouze na pole virtuální zařízení StorSimple.

## <a name="setup-prerequisites"></a>Požadavky instalačního programu

Než začnete konfigurovat a nastavit pole virtuální zařízení StorSimple, ujistěte se, že:

* Zřízení virtuální pole a k němu připojená, jak je popsáno v [nasazení zařízení StorSimple virtuální pole - zřídit virtuální pole Hyper-v](storsimple-ova-deploy2-provision-hyperv.md) nebo [nasazení zařízení StorSimple virtuální pole - zřídit virtuální pole v prostředí VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Máte registrační klíč služby ze služby StorSimple Manager zařízení, která jste vytvořili pro správu vaše pole virtuální zařízení StorSimple. Další informace najdete v tématu **krok 2: získání registračního klíče služby** v [nasazení zařízení StorSimple virtuální pole - Příprava portálu](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
* Pokud je to druhém a dalším virtuální pole, které jsou registraci s existující službu Správce zařízení StorSimple, měli byste mít šifrovacího klíče dat služby. Tento klíč byl vygenerován při první zařízení byl úspěšně registrován s touto službou. Pokud jste ztratili tento klíč, přečtěte si téma **získání šifrovacího klíče dat služby** v [pomocí webového uživatelského rozhraní pro správu pole virtuální zařízení StorSimple](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Krok za krokem instalace

Použijte následující podrobné pokyny k nastavení a konfigurace virtuálního zařízení StorSimple pole:

* [Krok 1: Dokončení instalace místní webové uživatelské rozhraní a zaregistrovat zařízení](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* [Krok 2: Dokončení instalace požadované zařízení](#step-2-complete-the-required-device-setup)
* [Krok 3: Přidání svazku](#step-3-add-a-volume)
* [Krok 4: Připojení, inicializace a formátování svazků](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Krok 1: Dokončení instalace místní webové uživatelské rozhraní a zaregistrovat zařízení

#### <a name="to-complete-the-setup-and-register-the-device"></a>K dokončení instalace a registrace zařízení

1. Otevřete okno prohlížeče. Připojení k serveru webového uživatelského rozhraní typu:
   
    `https://<ip-address of network interface>`
   
    Použijte adresu URL připojení si poznamenali v předchozím kroku. Zobrazí se chyba oznamující, že došlo k potížím s certifikátem zabezpečení webu. Klikněte na tlačítko **pokračovat k této webové stránce**.
   
    ![Chyba certifikátu zabezpečení](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. Přihlaste se k webu uživatelského rozhraní vašeho virtuálního zařízení jako **StorSimpleAdmin**. Zadejte heslo správce zařízení, které jste změnili v kroku 3: spuštění virtuální zařízení [nasazení zařízení StorSimple virtuální pole - zřídit virtuální zařízení technologie Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) nebo [nasazení zařízení StorSimple virtuální pole - zřídit virtuální zařízení v prostředí VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
    ![Přihlašovací stránka](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. Budete přesměrováni na **Domů** stránky. Tato stránka popisuje různá nastavení, které jsou potřeba ke konfiguraci a registrace virtuálního zařízení se službou Správce zařízení StorSimple. Všimněte si, že **nastavení síťového**, **webové nastavení proxy serveru**, a **nastavení času** jsou volitelné. Jsou jenom požadovaná nastavení **nastavení zařízení** a **nastavení cloudu**.
   
    ![Domovská stránka](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. Na **nastavení síťového** v části **síťových rozhraní**, DATA 0 bude automaticky nakonfigurován pro vás. Každé síťové rozhraní je ve výchozím nastavení automaticky získat IP adresu (DHCP). Proto adresu IP adresy, podsítě a bránu bude automaticky přiřazen (pro IPv4 i IPv6).
   
    Jak plánujete nasadit zařízení jako server se službou iSCSI (pro zřízení blokové úložiště), doporučujeme zakázat **získat IP adresu automaticky** řádku a nakonfigurovat statické IP adresy.
   
    ![Stránka nastavení sítě](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    Pokud jste přidali více než jedno síťové rozhraní během zřizování zařízení, můžete je konfigurovat tady. Všimněte si, že síťové rozhraní můžete nakonfigurovat jako IPv4 pouze nebo jako oba protokoly IPv4 a IPv6. Nejsou podporovány pouze konfigurace IPv6.
5. Servery DNS jsou požadované, protože se používají ke komunikaci s vašich poskytovatelů cloudových služeb úložiště nebo pokud chcete vyřešit zařízení podle názvu, pokud je nakonfigurovaný jako souborový server pokusí připojení zařízení. Na **nastavení síťového** v části **servery DNS**:
   
   1. Primární a sekundární server DNS automaticky nakonfigurují. Pokud zvolíte možnost konfigurace statických IP adres, můžete zadat servery DNS. Pro vysokou dostupnost doporučujeme nakonfigurovat primární a sekundární server DNS.
   2. Klikněte na tlačítko **Použít**. Použije a ověřte nastavení sítě.
6. Na **nastavení zařízení** stránky:
   
   1. Přiřadit jedinečný **název** do vašeho zařízení. Tento název může obsahovat 1 až 15 znaků a může obsahovat písmena, číslice a pomlčky.
   2. Klikněte na tlačítko **serveru iSCSI** ikonu ![ikona serveru iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) pro **typu** zařízení, které vytváříte. Server se službou iSCSI je možné provést zřízení blokové úložiště.
   3. Zadejte, pokud chcete toto zařízení k doméně. Pokud je vaše zařízení serveru iSCSI, pak připojení k doméně je volitelné. Pokud se rozhodnete serveru iSCSI není připojení k doméně, klikněte na tlačítko **použít**, počkejte nastavení, které se použijí a potom přejděte k dalšímu kroku.
      
       Pokud chcete připojení zařízení k doméně. Zadejte **název domény**a potom klikněte na **použít**.
      
      > [!NOTE]
      > Pokud váš server iSCSI připojení k doméně, zkontrolujte, zda je vaše virtuální pole ve vlastní organizační jednotku (OU) pro Microsoft Azure Active Directory a žádné objekty zásad skupiny (GPO) se použijí k němu.
      > 
      > 
   4. Zobrazí se dialogové okno. Zadejte přihlašovací údaje domény v zadaném formátu. Klikněte na ikonu zaškrtnutí ![ikona zaškrtnutí](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png). Pověření domény se ověří. Pokud přihlašovací údaje jsou nesprávné, zobrazí se chybová zpráva.
      
       ![přihlašovací údaje](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. Klikněte na tlačítko **Použít**. Použije a ověřit nastavení zařízení.
7. (Volitelně) nakonfigurujte váš webový proxy server. Přestože konfigurace webového proxy serveru je volitelný, mějte na paměti, že pokud používáte webový proxy server, můžete pouze nakonfigurovat ji sem.
   
    ![nakonfigurovat webový proxy server](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    Na **webový proxy server** stránky:
   
   1. Zadejte **webová adresa URL proxy serveru** v tomto formátu: *http://host-IP adresu* nebo *FDQN:Port číslo*. Všimněte si, že nejsou podporovány adresy URL typu HTTPS.
   2. Zadejte **ověřování** jako **základní** nebo **žádné**.
   3. Pokud používáte ověřování, budete také muset poskytnout **uživatelské jméno** a **heslo**.
   4. Klikněte na tlačítko **Použít**. To bude ověřit a použít nastavení nakonfigurované webového proxy serveru.
8. (Volitelně) nakonfigurujte nastavení času pro vaše zařízení, jako je časové pásmo a primární a sekundární servery NTP. Protože vaše zařízení musí synchronizovat čas, takže můžete ověřit pomocí vašich poskytovatelů cloudových služeb, jsou vyžadovány NTP servery.
   
    ![Nastavení času](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    Na **nastavení času** stránky:
   
   1. V rozevíracím seznamu vyberte **časové pásmo** podle zeměpisného umístění, ve kterém je nasazený zařízení. Výchozí časové pásmo pro vaše zařízení je PST. Toto časové pásmo bude zařízení používat pro všechny naplánované operace.
   2. Zadejte **primární server NTP** pro vaše zařízení nebo přijměte výchozí hodnotu time.windows.com. Ujistěte se, že vaše síť umožňuje přenos dat NTP z vašeho datového centra na internet.
   3. Volitelně zadejte **serveru NTP sekundární** pro vaše zařízení.
   4. Klikněte na tlačítko **Použít**. Tato akce ověření a nastavení nakonfigurovaném čase.
9. Nakonfigurujte nastavení cloudu pro vaše zařízení. V tomto kroku bude dokončení konfigurace místní zařízení a pak zaregistrujte zařízení pomocí služby StorSimple Manager zařízení.
   
   1. Zadejte **registrační klíč služby** které jste získali **krok 2: získání registračního klíče služby** v [nasazení zařízení StorSimple virtuální pole - Příprava portálu](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
   2. Pokud to není první zařízení, které jsou s touto službou registraci, budete muset zadat **šifrovacího klíče dat služby**. Tento klíč je požadován s registrační klíč služby k registraci dalších zařízení se službou Správce zařízení StorSimple. Další informace najdete v části [získání šifrovacího klíče dat služby](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) ve vašem místním webové uživatelské rozhraní.
   3. Klikněte na tlačítko **zaregistrovat**. Toto zařízení restartuje. Musíte počkat, 2-3 minuty, než je zařízení úspěšně zaregistrované. Po restartování zařízení, budete přesměrováni na stránku pro přihlášení.
      
      ![Registrace zařízení](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Vraťte se k portálu Azure.
11. Přejděte na **zařízení** okna vaší služby. Pokud máte velké množství prostředků, klikněte na tlačítko **všechny prostředky**, klikněte na název služby (vyhledejte ho v případě potřeby) a pak klikněte na tlačítko **zařízení**.
12. Na **zařízení** okno, ověřte, že zařízení má úspěšně připojené ke službě vyhledáním stav. Stav zařízení musí být **Připraveno k nastavení**.
    
    ![Registrace zařízení](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>Krok 2: Konfigurace zařízení jako iSCSI server

Proveďte následující kroky na portálu Azure k dokončení instalace požadované zařízení.

#### <a name="to-configure-the-device-as-iscsi-server"></a>Nakonfigurujte zařízení jako iSCSI server

1. Přejděte do služby StorSimple Manager zařízení a potom přejděte na **správy > zařízení**. V **zařízení** okně vyberte zařízení, které jste právě vytvořili. Zobrazí se toto zařízení jako **připravení nastavit**.
   
    ![Nakonfigurujte zařízení jako iSCSI server](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. Klikněte na zařízení a zobrazí nápis zpráva označující, že zařízení je připraven k instalaci.
   
    ![Nakonfigurujte zařízení jako iSCSI server](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. Klikněte na tlačítko **konfigurace** na panelu příkazů zařízení. Otevře **konfigurace** okno. V **konfigurace** okno, postupujte takto:
   
   * Se automaticky vyplní název serveru iSCSI.
   * Ujistěte se, že šifrování úložiště cloudu je nastavena na **povoleno**. To zajišťuje, aby šifrovala dat odesílaných ze zařízení do cloudu.
   * Zadejte 32 znaků šifrovací klíč a záznam v aplikaci Správa klíčů pro budoucí použití.
   * Vyberte účet úložiště, který se má použít s vaším zařízením. V tomto předplatném, můžete vybrat existující účet úložiště, nebo můžete kliknout na **přidat** zvolit účet z jiného předplatného.
     
     ![Nakonfigurujte zařízení jako iSCSI server](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. Klikněte na tlačítko **konfigurace** k dokončení nastavení serveru služby iSCSI.
   
    ![Nakonfigurujte zařízení jako iSCSI server](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. Budete informováni, že vytvoření serveru iSCSI probíhá. Po úspěšném vytvoření serveru se službou iSCSI **zařízení** okno se aktualizuje a odpovídající stav zařízení je **Online**.
   
    ![Nakonfigurujte zařízení jako iSCSI server](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>Krok 3: Přidání svazku

1. V **zařízení** okně, vyberte zařízení, které jste právě nakonfigurovaný jako server se službou iSCSI. Klikněte na tlačítko **...**  (případně klikněte pravým tlačítkem na tento řádek) a v místní nabídce vyberte **Přidání svazku**. Můžete také kliknout na **+ přidat svazek** z řádku nabídek. Otevře **Přidání svazku** okno.
   
    ![Přidání svazku](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. V **Přidání svazku** okno, postupujte takto:
   
   * V **název svazku** pole, zadejte jedinečný název pro svazek. Název musí být řetězec, který obsahuje 3 až 127 znaků.
   * V **typ** rozevíracího seznamu, určete, zda chcete vytvořit **nastavování** nebo **místně vázaný** svazku. Pro úlohy, které vyžadují místní záruky, nízkou latenci a vyšší výkon, vyberte **místně vázaný** **svazku**. Všechna ostatní data, vyberte **nastavování** **svazku**.
   * V **kapacity** pole, zadejte velikost svazku. Vrstvený svazek musí být mezi 500 GB a 5 TB a místně vázaný svazek musí být mezi 50 GB a 500 GB.
     
     Místně vázaný svazek je tlustě zřízený a zajišťuje, že primární data ve svazku zůstává v zařízení a nebudou distribuována do cloudu.
     
     Vrstvený svazek na druhé straně je tence zřízený. Když vytvoříte vrstvený svazek, je přibližně 10 % prostoru zřízený na místní úrovni a 90 % prostoru se zřizuje v cloudu. Například pokud jste zřídili svazku 1 TB, 100 GB by nacházet v místním prostoru a 900 GB se použije v cloudu při datové vrstvy. To znamená naopak je, že pokud spustíte mimo veškeré volné místo na zařízení, nemůže zřídit vrstvené sdílené složky (protože 10 % nebudete mít k dispozici).
     
     ![Přidání svazku](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * Klikněte na tlačítko **připojení hostitele**, vyberte záznam řízení přístupu (ACR) odpovídající iniciátoru iSCSI, který chcete připojit tohoto svazku a pak klikněte na tlačítko **vyberte**. <br><br> 
3. Chcete-li přidat nový připojené hostitele, klikněte na tlačítko **přidat nový**, zadejte název hostitele a jeho iSCSI kvalifikovaný název IQN () a pak klikněte na tlačítko **přidat**. Pokud název IQN nemáte, přejděte na [příloha A: získání názvu IQN hostitele Windows serveru](#appendix-a-get-the-iqn-of-a-windows-server-host).
   
      ![Přidání svazku](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. Po dokončení konfigurace svazku, klikněte na tlačítko **OK**. Vytvoří se svazek se zadaným nastavením a zobrazí se oznámení. Ve výchozím nastavení monitorování a zálohování budou povolené pro svazek.
   
     ![Přidání svazku](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. Chcete-li potvrdit, že svazek byl úspěšně vytvořen, přejděte na **svazky** okno. Měli byste vidět svazku uvedené.
   
   ![Přidání svazku](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Krok 4: Připojení, inicializace a formátování svazků

Proveďte následující kroky, připojte, inicializujte a formátujte svazky zařízení StorSimple na hostitelském Windows serveru.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Připojení, inicializace a formátování svazku

1. Otevřete **iniciátor iSCSI** aplikace na příslušný server.
2. V okně **iSCSI Initiator Properties (Vlastnosti iniciátoru iSCSI)** na kartě **Zjišťování** klikněte na **Vyhledat portál**.
   
    ![zjistit portálu](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. V dialogovém okně **Zjistit cílový portál** zadejte IP adresu svého síťového rozhraní s podporou iSCSI a potom klikněte na **OK**.
   
    ![IP adresa](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. V okně **iSCSI Initiator Properties** (Vlastnosti iniciátoru iSCSI) na kartě **Cíle** najděte část **Zjištěné cíle**. (Každý svazek bude zjištěné cíle.) U zařízení by se měl zobrazovat stav **Neaktivní**.
   
    ![zjištěné cíle](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. Vyberte cílové zařízení a pak klikněte na tlačítko **Connect**. Po připojení zařízení by se měl stav změnit na **Připojeno**. (Další informace o použití iniciátoru iSCSI společnosti Microsoft najdete v tématu [instalace a konfigurace Microsoft iSCSI Initiator][1].
   
    ![Vyberte cílové zařízení](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. Na hostiteli s Windows stiskněte klávesu s logem Windows + X a potom klikněte na **Spustit**.
7. V dialogovém okně **Spustit** zadejte **Diskmgmt.msc**. Klikněte na **OK**. Zobrazí se dialogové okno **Správa disků**. V pravém podokně se zobrazí svazky na vašem hostiteli.
8. V okně **Správa disků** se připojené svazky zobrazí tak, jak znázorňuje následující obrázek. Klikněte pravým tlačítkem myši na zjištěný svazek (klikněte na název disku) a potom klikněte na **Online**.
   
    ![Správa disků](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. Klikněte pravým tlačítkem a vyberte **inicializovat Disk**.
   
    ![Inicializovat disk 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. V dialogovém okně vyberte disky, inicializujte a pak klikněte na tlačítko **OK**.
    
    ![Inicializovat disk 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. Spustí se průvodce Nový jednoduchý svazek. Vyberte velikost disku a pak klikněte na tlačítko **Další**.
    
    ![Průvodce vytvořením svazku 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. Přiřadit písmeno jednotky svazku a potom klikněte na **Další**.
    
    ![Průvodce vytvořením svazku 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. Zadejte parametry naformátování svazku. **V systému Windows Server je podporována pouze v systému souborů NTFS.** Nastavte velikost alokační jednotky na 64 kB. Zadejte popisek svazku. Je součástí osvědčeného postupu pro tento název má být stejný jako název svazku, které jste zadali na pole virtuální zařízení StorSimple. Klikněte na **Další**.
    
    ![Průvodce vytvořením svazku 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. Zkontrolujte hodnoty pro svazek a pak klikněte na tlačítko **Dokončit**.
    
    ![Průvodce vytvořením svazku 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    Svazky se zobrazí jako **Online** na **Správa disků** stránky.
    
    ![svazky online](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Další kroky

Naučte se používat místní webové uživatelské rozhraní pro [spravovat vaše pole virtuální zařízení StorSimple](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Příloha A: získání názvu IQN hostitele Windows serveru

Pomocí následujících kroků získejte název IQN (iSCSI Qualified Name) hostitele Windows se systémem Windows Server® 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Získání názvu IQN hostitele s Windows

1. Na hostiteli s Windows spusťte iniciátor iSCSI od Microsoftu.
2. V okně **iSCSI Initiator Properties** (Vlastnosti iniciátoru iSCSI) na kartě **Konfigurace** vyberte a zkopírujte řetězec z pole **Název iniciátoru**.
   
    ![Vlastnosti iniciátoru iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. Uložte tento řetězec.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx




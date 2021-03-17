---
title: Nastavení virtuálního pole StorSimple jako souborového serveru | Microsoft Docs
description: Tento třetí kurz v nasazení virtuálních polí StorSimple vám dává pokyn k nastavení virtuálního zařízení jako souborového serveru.
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
ms.openlocfilehash: 244fdbf7cb723fe85e0987d176a13242f0bff064
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005924"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>Nasazení virtuálního pole StorSimple – nastavení jako souborového serveru prostřednictvím Azure Portal
![Diagram znázorňující kroky potřebné k nasazení virtuálního pole První část třetího kroku je označená jako Souborová Server a zvýrazní se.](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Úvod

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Tento článek popisuje, jak provést počáteční nastavení, zaregistrovat souborový server StorSimple, dokončit instalaci zařízení a vytvořit sdílené složky SMB a připojit se k nim. Toto je poslední článek v sérii kurzů pro nasazení požadovaných k úplnému nasazení virtuálního pole jako souborového serveru nebo serveru iSCSI.

Dokončení procesu instalace a konfigurace může trvat přibližně 10 minut. Informace v tomto článku se vztahují pouze k nasazení virtuálního pole StorSimple. Nasazení zařízení řady StorSimple 8000 najdete v části [nasazení zařízení StorSimple 8000 Series s aktualizací Update 2](./storsimple-8000-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Požadavky instalačního programu 
Před konfigurací a nastavením StorSimple virtuálního pole se ujistěte, že:

* Zřídili jste virtuální pole a připojili se k němu, jak je popsáno v části [zřízení virtuálního pole StorSimple v Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) nebo [zřízení virtuálního pole StorSimple ve VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Máte registrační klíč služby ze služby StorSimple Device Manager, kterou jste vytvořili pro správu virtuálních polí StorSimple. Další informace najdete v části [Krok 2: získání registračního klíče služby](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) pro virtuální pole StorSimple.
* Pokud se jedná o druhé nebo následné virtuální pole, které zaregistrujete pomocí existující služby StorSimple Device Manager, měli byste mít šifrovací klíč dat služby. Tento klíč byl vygenerován při úspěšné registraci prvního zařízení s touto službou. Pokud jste tento klíč ztratili, přečtěte si téma [získání šifrovacího klíče dat služby](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) pro virtuální pole StorSimple.

## <a name="step-by-step-setup"></a>Podrobný postup instalace
Pomocí následujících podrobných pokynů nastavte a nakonfigurujte virtuální pole StorSimple.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Krok 1: dokončení nastavení místního webového uživatelského rozhraní a registrace zařízení
#### <a name="to-complete-the-setup-and-register-the-device"></a>Dokončení nastavení a registrace zařízení
1. Otevřete okno prohlížeče a připojte se k místnímu webovému uživatelskému rozhraní. Zadejte:
   
   `https://<ip-address of network interface>`
   
   Použijte adresu URL připojení, kterou jste si poznamenali v předchozím kroku. Zobrazí se chyba oznamující, že došlo k potížím s certifikátem zabezpečení webu. Klikněte na **pokračovat na tuto webovou stránku**.
   
   ![Snímek obrazovky okna prohlížeče, které hlásí problém s certifikátem zabezpečení K dispozici jsou dvě odkazy, jednu pro web a jednu pro domovskou stránku uživatele.](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Přihlaste se k webovému uživatelskému rozhraní vašeho virtuálního pole jako **StorSimpleAdmin**. Zadejte heslo správce zařízení, které jste změnili v kroku 3: spuštění virtuálního pole v části [zřízení virtuálního pole StorSimple v technologii Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) nebo [zřízení virtuálního pole StorSimple ve VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
   ![Snímek obrazovky s přihlašovací stránkou StorSimple Uživatelské jméno StorSimpleAdmin je viditelné a pole pro heslo je vyplněno neurčitými znaky.](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. Přejdete na **domovskou** stránku. Tato stránka popisuje různá nastavení potřebná ke konfiguraci a registraci virtuálního pole pomocí služby StorSimple Device Manager. Nastavení **sítě**, **nastavení webového proxy serveru** a **Nastavení času** jsou volitelné. Jediná požadovaná nastavení jsou **nastavení zařízení** a **Nastavení cloudu**.
   
   ![Snímek obrazovky domovské stránky Text uvádí, že zařízení není nakonfigurované. Odkazy na několik různých typů nastavení jsou viditelné.](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. Na stránce **nastavení sítě** v části **Síťová rozhraní** se pro vás automaticky nakonfigurují data 0. Každé síťové rozhraní je standardně nastavené tak, aby se automaticky získala IP adresa (DHCP). IP adresa, podsíť a brána se proto automaticky přiřazují (pro IPv4 a IPv6).
   
   ![Snímek obrazovky se stránkou nastavení sítě zobrazující IP adresy, které jsou nakonfigurovány pro různé verze Internet Protocol.](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Pokud jste během zřizování zařízení přidali více než jedno síťové rozhraní, můžete je nakonfigurovat tady. Všimněte si, že vaše síťové rozhraní můžete nakonfigurovat jenom jako IPv4, nebo jako IPv4 i IPv6. Pouze konfigurace protokolu IPv6 nejsou podporovány.
5. Servery DNS se vyžadují, protože se používají, když se vaše zařízení pokusí komunikovat s poskytovateli služeb cloudového úložiště nebo překládat vaše zařízení podle názvu, když je nakonfigurovaný jako souborový server. Na stránce **nastavení sítě** v části **servery DNS**:
   
   1. Primární a sekundární server DNS se automaticky nakonfigurují. Pokud se rozhodnete nakonfigurovat statické IP adresy, můžete zadat servery DNS. Pro zajištění vysoké dostupnosti doporučujeme nakonfigurovat primární a sekundární server DNS.
   2. Kliknutím na **použít** použijete a ověříte nastavení sítě.
6. Na stránce **nastavení zařízení** :
   
   1. Přiřaďte zařízení jedinečný **název** . Tento název může být 1-15 znaků a může obsahovat písmena, číslice a spojovníky.
   2. Klikněte na ikonu **souborového serveru** :::image type="icon" source="./media/storsimple-virtual-array-deploy3-fs-setup/image6.png"::: pro **typ** zařízení, které vytváříte. Souborový server vám umožní vytvořit sdílené složky.
   3. Jelikož je vaše zařízení souborový server, budete ho muset připojit k doméně. Zadejte **název domény**.
   4. Klikněte na **Použít**.
7. Zobrazí se dialogové okno. Zadejte svoje přihlašovací údaje do domény v zadaném formátu. Klikněte na ikonu zaškrtněte. Pověření domény jsou ověřena. Pokud jsou přihlašovací údaje nesprávné, zobrazí se chybová zpráva.
   
   ![Snímek obrazovky zobrazující dialogové okno s vyplněným uživatelským jménem a heslem](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Klikněte na **Použít**. Tím se uplatní a ověří nastavení zařízení.
   
   ![Snímek obrazovky se stránkou nastavení zařízení. Název zařízení a název domény jsou vyplněny.](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Ujistěte se, že vaše virtuální pole je ve své vlastní organizační jednotce pro službu Active Directory a že pro něj nejsou použité žádné objekty zásad skupiny (GPO) ani zděděné. Zásady skupiny mohou instalovat aplikace, jako je například antivirový software ve virtuálním poli StorSimple. Instalace dalšího softwaru není podporována a může vést k poškození dat. 
   > 
   > 
9. (Volitelně) nakonfigurujte web proxy server. Konfigurace proxy serveru je volitelná, ale můžete ji provést jenom v tomto kroku.
   
   ![Snímek obrazovky se stránkou nastavení webového proxy serveru Možnost povolení webového proxy serveru je vypnutá a ověřování je nastavené na žádné. Nejsou nastaveny žádné další hodnoty.](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   Na stránce **webového proxy serveru** :
   
   1. Zadejte **adresu URL webového proxy serveru** v tomto formátu: *http:// &lt; hostitel-IP adresa nebo plně kvalifikovaný název domény &gt; :P číslo hodnoty*. Upozorňujeme, že adresy URL protokolu HTTPS nejsou podporovány.
   2. Zadejte **ověřování** jako **základní** nebo **žádné**.
   3. Pokud používáte ověřování, budete také muset zadat **uživatelské jméno** a **heslo**.
   4. Klikněte na **Použít**. Tím se ověří a použije nakonfigurovaná nastavení webového proxy serveru.
10. (Volitelně) nakonfigurujte časová nastavení pro vaše zařízení, například časové pásmo a primární a sekundární servery NTP. Jsou vyžadovány servery NTP, protože vaše zařízení musí synchronizovat čas, aby se mohl ověřit u vašich poskytovatelů cloudových služeb.
    
    ![Snímek obrazovky se stránkou s nastavením času Časové pásmo a primární server N T P jsou vyplněny. Sekundární server N T P je prázdný.](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    Na stránce **Nastavení času** :
    
    1. V rozevíracím seznamu vyberte **časové pásmo** na základě geografického umístění, ve kterém se zařízení nasazuje. Výchozím časovým pásmem pro vaše zařízení je PST. Toto časové pásmo bude zařízení používat pro všechny naplánované operace.
    2. Zadejte **primární server NTP** pro vaše zařízení nebo přijměte výchozí hodnotu Time.Windows.com. Ujistěte se, že vaše síť umožňuje přenos dat NTP z vašeho datového centra na internet.
    3. Volitelně můžete zadat **sekundární server NTP** pro vaše zařízení.
    4. Klikněte na **Použít**. Tím se ověří a použije nakonfigurovaná nastavení času.
11. Nakonfigurujte nastavení cloudu pro vaše zařízení. V tomto kroku dokončíte konfiguraci místních zařízení a pak zaregistrujete zařízení ve službě StorSimple Device Manager.
    
    1. Zadejte **registrační klíč služby** , který jste získali v [kroku 2: získání registračního klíče služby](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) pro virtuální pole StorSimple.
    2. Pokud se jedná o první zařízení, které s touto službou zaregistrujete, zobrazí se vám **šifrovací klíč dat služby**. Klíč zkopírujte a uložte na bezpečném místě. Tento klíč je vyžadován s registračním klíčem služby k registraci dalších zařízení ve službě StorSimple Device Manager. 
       
       Pokud se nejedná o první zařízení, které zaregistrujete v rámci této služby, budete muset zadat šifrovací klíč dat služby. Další informace najdete v tématu Získání [šifrovacího klíče dat služby](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) v místním webovém uživatelském rozhraní.
    3. Klikněte na **Zaregistrovat**. Tím se zařízení restartuje. Možná budete muset počkat na 2-3 minut, než se zařízení úspěšně zaregistruje. Jakmile se zařízení restartuje, přejdete na přihlašovací stránku.
       
       ![Snímek obrazovky se stránkou nastavení cloudu Pole registračního klíče a šifrovacího klíče jsou vyplněna, ale hodnoty jsou redigovány.](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Vraťte se na Azure Portal. Přejít na **všechny prostředky**, vyhledejte službu StorSimple Device Manager.
    
    ![Snímek obrazovky se stránkou všechny prostředky v Azure Portal. Služba Device Manager je zvýrazněna.](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. V seznamu filtrovaný vyberte službu StorSimple Device Manager a potom přejděte na **> Správa zařízení**. V okně **zařízení** ověřte, že se zařízení úspěšně připojilo ke službě a má stav **připraveno k nastavení**.
    
    ![Nasadit](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>Krok 2: konfigurace zařízení jako souborového serveru
Provedením následujících kroků v [Azure Portal](https://portal.azure.com/) dokončete požadované nastavení zařízení.

#### <a name="to-configure-the-device-as-file-server"></a>Konfigurace zařízení jako souborového serveru
1. Přejít do služby StorSimple Device Manager a pak přejít na  **> zařízení pro správu**. V okně **zařízení** vyberte zařízení, které jste právě vytvořili. Toto zařízení se zobrazí jako **připravené k nastavení**.
   
   ![Konfigurace souborového serveru](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Klikněte na zařízení a zobrazí se informační zpráva oznamující, že zařízení je připravené k instalaci.
   
    ![Konfigurace souborového serveru 2](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Na panelu příkazů klikněte na **Konfigurovat** . Otevře se okno **Konfigurace** . V okně **Konfigurace** postupujte takto:
   
   1. Název souborového serveru se vyplní automaticky.
    
   2. Ujistěte se, že je šifrování cloudového úložiště nastavené na **povoleno**. Tím se zašifrují všechna data, která se odesílají do cloudu. 
    
   3. Pro šifrování je použit klíč AES 256 s uživatelem definovaným klíčem. Zadejte znakový klíč 32 a pak znovu zadejte klíč pro potvrzení. Poznamenejte si klíč v aplikaci pro správu klíčů, abyste mohli budoucí reference.
    
   4. Klikněte na **Konfigurovat požadované nastavení** a zadejte přihlašovací údaje účtu úložiště, které se mají používat s vaším zařízením. Pokud nejsou nakonfigurované žádné přihlašovací údaje účtu úložiště, klikněte na **Přidat nový** . **Ujistěte se, že používaný účet úložiště podporuje objekty blob bloku. Objekty blob stránky nejsou podporovány.** Další informace o objektech [BLOB bloků a objektech blob stránky](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)
   
      ![Konfigurace souborového serveru 3](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. V okně **Přidat přihlašovací údaje účtu úložiště** udělejte toto: 

    1. Pokud je účet úložiště ve stejném předplatném jako služba, vyberte aktuální předplatné. Zadejte jiný účet úložiště je mimo předplatné služby. 
    
    2. V rozevíracím seznamu vyberte existující účet úložiště. 
    
    3. Umístění se automaticky vyplní podle zadaného účtu úložiště. 
    
    4. Povolte TLS pro zajištění zabezpečeného síťového komunikačního kanálu mezi zařízením a cloudem.
    
    5. Kliknutím na **Přidat** přidejte tyto přihlašovací údaje k účtu úložiště. 
   
        ![Konfigurace souborového serveru 4](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. Po úspěšném vytvoření přihlašovacích údajů účtu úložiště se aktualizuje okno **Konfigurace** , aby se zobrazily zadané přihlašovací údaje účtu úložiště. Klikněte na **Konfigurovat**.
   
   ![Konfigurace souborového serveru 5](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   Zobrazí se soubor, který se vytváří na souborovém serveru. Po úspěšném vytvoření souborového serveru se zobrazí oznámení.
   
   ![Konfigurace souborového serveru 5b](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   Stav zařízení se změní také na **online**.
   
   ![Konfigurace souborového serveru 5C](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   Můžete pokračovat a přidat sdílenou složku.

## <a name="step-3-add-a-share"></a>Krok 3: přidání sdílené složky
Provedením následujících kroků na webu [Azure Portal](https://portal.azure.com/) vytvořte sdílenou složku.

#### <a name="to-create-a-share"></a>Vytvoření sdílené složky
1. Vyberte zařízení souborového serveru, které jste nakonfigurovali v předchozím kroku, a klikněte na **...** (nebo klikněte pravým tlačítkem myši). V místní nabídce vyberte **Přidat sdílenou složku**. Případně můžete kliknout na tlačítko **+ Přidat sdílenou složku** na panelu příkazů zařízení.
   
   ![Přidání sdílené složky](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Zadejte následující nastavení sdílení:

   1. Jedinečný název pro sdílenou složku. Název musí být řetězec, který obsahuje 3 až 127 znaků.
    
   2. Volitelný **Popis** sdílené složky. Popis vám pomůže identifikovat vlastníky sdílené složky.
    
   3. **Typ** pro sdílenou složku. Typ lze **Převrstvený** nebo **lokálně připnout** s vrstvou jako výchozí. Pro úlohy, které vyžadují místní záruky, nízkou latenci a vyšší výkon, vyberte **místně připojenou** sdílenou složku. U všech ostatních dat vyberte **vrstvenou** sdílenou složku.
      Místně připnuté sdílení je silným způsobem zajišťováno a zajišťuje, aby primární data ve sdílené složce zůstala místní pro zařízení a nepřesahují se do cloudu. Vrstvená sdílená složka je na druhé straně dynamicky zřízená. Když vytvoříte vrstvenou sdílenou složku, na místní úrovni se zřídí 10% místa a 90% místa se zřídí v cloudu. Pokud jste například zřídili svazek o velikosti 1 TB, 100 GB by se nacházelo v místním prostoru a v cloudu se v případě datových vrstev používalo 900 GB. To znamená, že pokud vyčerpáte z místního prostoru na zařízení, nemůžete zřídit vrstvenou sdílenou složku.
   
   4. V poli **nastavit výchozí všechna oprávnění na** pole přiřaďte uživateli nebo skupině, která přistupuje k této sdílené složce. Zadejte jméno uživatele nebo skupiny uživatelů ve formátu *jan \@ contoso.com* . Pro povolení přístupu k těmto sdíleným složkám doporučujeme použít skupinu uživatelů (místo jednoho uživatele). Jakmile tady přiřadíte oprávnění, můžete pak tato oprávnění upravit pomocí Průzkumníka souborů.
   
   5. Kliknutím na **Přidat** vytvořte sdílenou složku. 
    
       ![Přidat sdílenou složku 1](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
       Zobrazí se oznámení o probíhajícím vytváření sdílené složky.
   
       ![Přidat sdílenou složku 2](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
      Po vytvoření sdílené složky se zadaným nastavením se okno **sdílené položky** aktualizuje a projeví se nová sdílená složka. Ve výchozím nastavení jsou pro sdílenou složku povolené monitorování a zálohování.
   
      ![Přidat sdílenou složku 3](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>Krok 4: připojení ke sdílené složce
Nyní se budete muset připojit k jedné nebo více sdíleným složkám, které jste vytvořili v předchozím kroku. Proveďte tyto kroky na hostiteli Windows serveru připojeném k vašemu virtuálnímu poli StorSimple.

#### <a name="to-connect-to-the-share"></a>Připojení ke sdílené složce
1. Stiskněte klávesu :::image type="icon" source="./media/storsimple-virtual-array-deploy3-fs-setup/image22.png"::: + R. V okně Spustit zadejte *&#92;&#92;&lt; &gt; název souborového serveru* jako cestu a nahraďte *název souborového serveru* názvem zařízení, který jste přiřadili k souborovému serveru. Klikněte na **OK**.
   
   ![Snímek obrazovky s dialogovým oknem spustit Pole Otevřít je vyplněno cestou vedoucí k souborovému serveru.](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Otevře se Průzkumník souborů. Sdílené složky, které jste vytvořili, by se teď měly zobrazit jako složky. Pokud chcete zobrazit obsah sdílené složky, vyberte příslušnou složku a dvakrát na ni klikněte.
   
   ![Snímek obrazovky Průzkumníka souborů K dispozici je několik složek, které představují nově vytvořené sdílené složky.](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. Nyní můžete do těchto sdílených složek přidat soubory a vytvořit zálohu.

## <a name="next-steps"></a>Další kroky
Přečtěte si, jak pomocí místního webového uživatelského rozhraní [spravovat virtuální pole StorSimple](storsimple-ova-web-ui-admin.md).
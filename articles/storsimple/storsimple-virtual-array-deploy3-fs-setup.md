---
title: Nastavení pro zařízení StorSimple virtuální pole jako souborový server | Microsoft Docs
description: V tomto kurzu třetí v nasazení pole virtuální zařízení StorSimple dá pokyn k nastavení virtuálního zařízení jako souborový server.
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
ms.date: 04/17/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bf507fb21b314a6811db1c1e45a4356381caada1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927821"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>Nasazení pole virtuálního zařízení StorSimple - Set až jako souborový server prostřednictvím portálu Azure
![](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Úvod
Tento článek popisuje postup provedení počáteční instalace, registraci svého zařízení StorSimple souborového serveru, dokončete instalaci zařízení a vytvořit a připojit ke sdíleným složkám protokolu SMB. Toto je poslední článku kurzy nasazení, které jsou potřebné k nasazení zcela virtuální pole jako souborový server nebo server se službou iSCSI v řadě.

Proces instalace a konfigurace může trvat přibližně 10 minut na dokončení. Informace v tomto článku se vztahují pouze na nasazení pole virtuální zařízení StorSimple. Pro nasazení pro řadu zařízení StorSimple 8000, přejděte na: [nasazení zařízení řady StorSimple 8000 softwarem Update 2](storsimple-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Požadavky instalačního programu
Než začnete konfigurovat a nastavit pole virtuální zařízení StorSimple, ujistěte se, že:

* Zřízení virtuální pole a k němu připojená podle popisu v [zřídit virtuální pole Hyper-v StorSimple](storsimple-virtual-array-deploy2-provision-hyperv.md) nebo [zřídit o virtuální zařízení StorSimple pole v prostředí VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Máte registrační klíč služby ze služby StorSimple Manager zařízení, který jste vytvořili pro správu pole virtuální zařízení StorSimple. Další informace najdete v tématu [krok 2: získání registračního klíče služby](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) pro pole virtuální zařízení StorSimple.
* Pokud je to druhém a dalším virtuální pole, které jsou registraci s existující službu Správce zařízení StorSimple, měli byste mít šifrovacího klíče dat služby. Tento klíč byl vygenerován při první zařízení byl úspěšně registrován s touto službou. Pokud jste ztratili tento klíč, přečtěte si téma [získání šifrovacího klíče dat služby](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) pro pole virtuální zařízení StorSimple.

## <a name="step-by-step-setup"></a>Krok za krokem instalace
Použijte následující podrobné pokyny k nastavení a konfiguraci pole virtuální zařízení StorSimple.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Krok 1: Dokončení instalace místní webové uživatelské rozhraní a zaregistrovat zařízení
#### <a name="to-complete-the-setup-and-register-the-device"></a>K dokončení instalace a registrace zařízení
1. Otevřete okno prohlížeče a připojit k místní webového uživatelského rozhraní. Zadejte:
   
   `https://<ip-address of network interface>`
   
   Použijte adresu URL připojení si poznamenali v předchozím kroku. Zobrazí chyba oznamující, že došlo k potížím s certifikátem zabezpečení webu. Klikněte na tlačítko **pokračovat na tuto webovou stránku**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Přihlaste se k webu uživatelského rozhraní vaše virtuální pole jako **StorSimpleAdmin**. Zadejte heslo správce zařízení, které jste změnili v kroku 3: spuštění virtuální pole v [zřídit virtuální pole Hyper-v StorSimple](storsimple-virtual-array-deploy2-provision-hyperv.md) nebo v [zřídit o virtuální zařízení StorSimple pole v prostředí VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. Přejdete **Domů** stránky. Tato stránka popisuje různá nastavení, které jsou potřeba ke konfiguraci a zaregistrovat virtuální pole službu StorSimple Manager zařízení. **Nastavení síťového**, **webové nastavení proxy serveru**, a **nastavení času** jsou volitelné. Jsou jenom požadovaná nastavení **nastavení zařízení** a **nastavení cloudu**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. V **nastavení síťového** v části **síťových rozhraní**, bude pro vás automaticky nakonfigurován DATA 0. Každé síťové rozhraní je ve výchozím nastavení automaticky získat IP adresu (DHCP). Adresu IP, podsíť a brána se proto automaticky přiřadí (pro IPv4 i IPv6).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Pokud jste přidali více než jedno síťové rozhraní během zřizování zařízení, můžete je konfigurovat tady. Všimněte si, že síťové rozhraní můžete nakonfigurovat jako IPv4 pouze nebo jako oba protokoly IPv4 a IPv6. Nejsou podporovány pouze konfigurace IPv6.
5. Servery DNS jsou požadované, protože se používají při vaše zařízení se pokouší komunikovat s vašich poskytovatelů cloudových služeb úložiště nebo pokud chcete vyřešit zařízení podle názvu, když nakonfigurovaný jako souborový server. V **nastavení síťového** v části **servery DNS**:
   
   1. Primární a sekundární server DNS budou automaticky nakonfigurovány. Pokud zvolíte možnost konfigurace statických IP adres, můžete zadat servery DNS. Pro vysokou dostupnost doporučujeme nakonfigurovat primární a sekundární server DNS.
   2. Klikněte na tlačítko **použít** pro použití a ověřte nastavení sítě.
6. V **nastavení zařízení** stránky:
   
   1. Přiřadit jedinečný **název** do vašeho zařízení. Tento název může obsahovat 1 až 15 znaků a může obsahovat písmena, číslice a pomlčky.
   2. Klikněte na tlačítko **souborový server** ikonu ![](./media/storsimple-virtual-array-deploy3-fs-setup/image6.png) pro **typu** zařízení, které vytváříte. Souborový server vám umožní vytvořit sdílené složky.
   3. Zařízení je souborový server, musíte připojit zařízení k doméně. Zadejte **název domény**.
   4. Klikněte na tlačítko **Použít**.
7. Zobrazí se dialogové okno. Zadejte přihlašovací údaje domény v zadaném formátu. Klikněte na ikonu zaškrtnutí. Ověření pověření domény. Zobrazí chybovou zprávu, pokud se přihlašovací údaje jsou nesprávné.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Klikněte na tlačítko **Použít**. Použije a ověřit nastavení zařízení.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Zkontrolujte, zda je vaše virtuální pole ve vlastní organizační jednotku (OU) pro službu Active Directory a jsou použity k němu nebo zděděna žádné objekty zásad skupiny (GPO). Zásady skupiny může instalovat aplikace jako antivirový software na poli virtuální zařízení StorSimple. Instalace dalšího softwaru není podporována a může vést k poškození dat. 
   > 
   > 
9. (Volitelně) nakonfigurujte váš webový proxy server. Přestože konfigurace webového proxy serveru je volitelný, mějte na paměti, že pokud používáte webový proxy server, můžete pouze nakonfigurovat ji sem.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   V **webový proxy server** stránky:
   
   1. Zadejte **webová adresa URL proxy serveru** v tomto formátu: *http://&lt;hostitele IP adresu nebo plně kvalifikovaného názvu domény&gt;: číslo portu*. Všimněte si, že nejsou podporovány adresy URL typu HTTPS.
   2. Zadejte **ověřování** jako **základní** nebo **žádné**.
   3. Pokud používáte ověřování, budete také muset poskytnout **uživatelské jméno** a **heslo**.
   4. Klikněte na tlačítko **Použít**. To bude ověřit a použít nastavení nakonfigurované webového proxy serveru.
10. (Volitelně) nakonfigurujte nastavení času pro vaše zařízení, jako je časové pásmo a primární a sekundární servery NTP. Protože vaše zařízení musí synchronizovat čas, takže můžete ověřit pomocí vašich poskytovatelů cloudových služeb, jsou vyžadovány NTP servery.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    V **nastavení času** stránky:
    
    1. Z rozevíracího seznamu vyberte **časové pásmo** podle zeměpisného umístění, ve kterém je nasazený zařízení. Výchozí časové pásmo pro vaše zařízení je PST. Toto časové pásmo bude zařízení používat pro všechny naplánované operace.
    2. Zadejte **primární server NTP** pro vaše zařízení nebo přijměte výchozí hodnotu time.windows.com. Ujistěte se, že vaše síť umožňuje přenos dat NTP z vašeho datového centra na internet.
    3. Volitelně zadejte **serveru NTP sekundární** pro vaše zařízení.
    4. Klikněte na tlačítko **Použít**. Tato akce ověření a nastavení nakonfigurovaném čase.
11. Nakonfigurujte nastavení cloudu pro vaše zařízení. V tomto kroku bude dokončení konfigurace místní zařízení a pak zaregistrujte zařízení pomocí služby StorSimple Manager zařízení.
    
    1. Zadejte **registrační klíč služby** které jste získali [krok 2: získání registračního klíče služby](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) pro pole virtuální zařízení StorSimple.
    2. Pokud je to první zařízení registrace s touto službou, zobrazí se **šifrovacího klíče dat služby**. Klíč zkopírujte a uložte na bezpečném místě. Tento klíč je požadován s registrační klíč služby k registraci dalších zařízení se službou Správce zařízení StorSimple. 
       
       Pokud to není první zařízení, které jsou s touto službou registraci, musíte zadat šifrovací klíč dat služby. Další informace najdete v části získat [šifrovacího klíče dat služby](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) ve vašem místním webové uživatelské rozhraní.
    3. Klikněte na tlačítko **zaregistrovat**. Toto zařízení restartuje. Musíte počkat, 2-3 minuty, než je zařízení úspěšně zaregistrované. Po restartování zařízení, budete přesměrováni na stránku pro přihlášení.
       
       ![](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Vraťte se k portálu Azure. Přejděte na **všechny prostředky**, vyhledávání služby StorSimple Manager zařízení.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. V seznamu Filtrované vybrat služby StorSimple Manager zařízení a potom přejděte na **správy > zařízení**. V **zařízení** okno, ověřte, zda zařízení úspěšně připojilo ke službě a má stav **připravení nastavit**.
    
    ![Konfigurace souborového serveru](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>Krok 2: Konfigurace zařízení jako souborový server
Proveďte následující kroky v [portál Azure](https://portal.azure.com/) k dokončení instalace požadované zařízení.

#### <a name="to-configure-the-device-as-file-server"></a>Ke konfiguraci zařízení jako souborový server
1. Přejděte do služby StorSimple Manager zařízení a potom přejděte na **správy > zařízení**. V **zařízení** okně vyberte zařízení, které jste právě vytvořili. Zobrazí se toto zařízení jako **připravení nastavit**.
   
   ![Konfigurace souborového serveru](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Klikněte na zařízení a zobrazí nápis zpráva označující, že zařízení je připraven k instalaci.
   
    ![Konfigurace souborového serveru](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Klikněte na tlačítko **konfigurace** na panelu příkazů. Otevře **konfigurace** okno. V **konfigurace** okno, postupujte takto:
   
    1. Název souborového serveru se automaticky vyplní.
    
    2. Ujistěte se, že šifrování úložiště cloudu je nastavena na **povoleno**. To bude šifrování všech dat, které se odesílají do cloudu. 
    
    3. Klíče AES 256 bitů se používá s uživatelem definované klíče pro šifrování. Zadejte klíč 32 znaků a potom znovu zadat klíče pro potvrzení. Záznam klíče v aplikaci pro správu klíčů pro budoucí použití.
    
    4. Klikněte na tlačítko **konfigurovat požadované nastavení** k zadání přihlašovacích údajů účtu úložiště pro použití s vaším zařízením. Klikněte na tlačítko **přidat nový** Pokud nejsou konfigurována žádná pověření účtu úložiště. **Zajistěte, aby účet úložiště, že používáte podporuje objekty BLOB bloku. Objekty BLOB stránky nejsou podporovány.** Další informace o [blokuje objekty BLOB a objekty BLOB stránky](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).
   
    ![Konfigurace souborového serveru](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. V **přidat přihlašovacích údajů účtu úložiště** okno, postupujte takto: 

    1. Zvolte aktuální předplatné, pokud je účet úložiště ve stejném předplatném jako služba. Zadejte jiné je úložiště účet je mimo předplatné služby. 
    
    2. Z rozevíracího seznamu vyberte stávající účet úložiště. 
    
    3. Umístění se automaticky vyplní podle zadaný účet úložiště. 
    
    4. Povolte protokol SSL zajistit zabezpečený komunikační kanál mezi zařízením a cloudem.
    
    5. Klikněte na tlačítko **přidat** přidat tato pověření účtu úložiště. 
   
        ![Konfigurace souborového serveru](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. Po úspěšném vytvoření pověření účtu úložiště **konfigurace** okno se aktualizuje a zobrazí přihlašovací údaje účtu zadané úložiště. Klikněte na **Konfigurovat**.
   
   ![Konfigurace souborového serveru](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   Zobrazí se soubor serveru se vytváří. Po úspěšném vytvoření souborový server, budete upozorněni.
   
   ![Konfigurace souborového serveru](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   Stav zařízení se také změní na **Online**.
   
   ![Konfigurace souborového serveru](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   Můžete přejít k přidání sdílené složky.

## <a name="step-3-add-a-share"></a>Krok 3: Přidání sdílené složky
Proveďte následující kroky v [portál Azure](https://portal.azure.com/) vytvořit sdílenou složku.

#### <a name="to-create-a-share"></a>Chcete-li vytvořit sdílenou složku
1. Vyberte zařízení, server soubor jste nakonfigurovali v předchozím kroku a klikněte na tlačítko **...**  (nebo klikněte pravým tlačítkem myši). V místní nabídce vyberte **přidat sdílenou složku**. Případně můžete kliknout na **+ přidat sdílenou složku** na panelu příkazů zařízení.
   
   ![Přidání sdílené složky](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Zadejte následující nastavení sdílené složky:

    1. Jedinečný název sdílené položky. Název musí být řetězec, který obsahuje 3 až 127 znaků.
    
    2. Volitelný **popis** pro sdílenou složku. Popis pomůže identifikovat vlastníkům sdílené složky.
    
    3. A **typ** pro sdílenou složku. Typ může být **nastavování** nebo **místně vázaný**, s vrstvené se výchozí hodnota. Pro úlohy, které vyžadují místní záruky, nízkou latenci a vyšší výkon, vyberte **místně vázaný** sdílet. Všechna ostatní data, vyberte **nastavování** sdílet.
    Sdílenou složku místně vázaný je tlustě zřízený a zajišťuje, že primární data ve sdílené složce zůstává místním zařízení a nebudou distribuována do cloudu. Vrstvený sdílenou složku na druhé straně je tence zřízený. Když vytvoříte sdílenou složku vrstvené, je 10 % prostoru zřízený na místní úrovni a 90 % prostoru se zřizuje v cloudu. Například pokud jste zřídili svazku 1 TB, 100 GB by nacházet v místním prostoru a 900 GB se použije v cloudu při datové vrstvy. Pak z toho vyplývá, že pokud spustíte mimo veškeré volné místo na zařízení, nemůže zřizovat vrstvené sdílenou složku.
   
    4. V **nastavit výchozí úplná oprávnění** pole, přiřadit oprávnění uživatele nebo skupiny, který přistupuje k této sdílené složce. Zadejte název tohoto uživatele nebo skupiny uživatelů v  *john@contoso.com*  formátu. Doporučujeme vám, že používáte skupinu uživatelů (ne jenom jednoho konkrétního uživatele) umožňuje správu oprávnění k přístupu k tyto sdílené složky. Po přiřazení oprávnění tady, můžete pomocí Průzkumníka souborů upravit tato oprávnění.
   
    5. Klikněte na tlačítko **přidat** vytvořit sdílenou složku. 
    
        ![Přidání sdílené složky](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
        Zobrazí upozornění, že vytvoření sdílené složky probíhá.
   
        ![Přidání sdílené složky](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
    Po vytvoření sdílené složky se zadaným nastavením **sdílené složky** okno bude aktualizovat tak, aby odrážely novou sdílenou složku. Ve výchozím nastavení monitorování a zálohování jsou povolené pro sdílenou složku.
   
    ![Přidání sdílené složky](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>Krok 4: Připojení ke sdílené složce
Musíte se teď připojit k jedné nebo více sdílených složek, které jste vytvořili v předchozím kroku. Proveďte tyto kroky na hostiteli systému Windows Server připojený k pole virtuální zařízení StorSimple.

#### <a name="to-connect-to-the-share"></a>Pro připojení ke sdílené složce
1. Stiskněte klávesu ![](./media/storsimple-virtual-array-deploy3-fs-setup/image22.png) + R. V okně Spustit zadejte *&#92; &#92;&lt; název souborového serveru&gt;*  jako cesta k nahrazení *název souborového serveru* s názvem zařízení, který jste přiřadili k souborového serveru. Klikněte na **OK**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Otevře se Průzkumník souborů. Teď by měla být moci zobrazit sdílené složky, které jste vytvořili jako složky. Vyberte a dvakrát klikněte na zobrazení obsahu sdílenou složku (složka).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. Teď můžete přidat soubory do těchto sdílených složek a provedení zálohy.

## <a name="next-steps"></a>Další kroky
Naučte se používat místní webové uživatelské rozhraní pro [spravovat vaše pole virtuální zařízení StorSimple](storsimple-ova-web-ui-admin.md).


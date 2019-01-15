---
title: Nastavit jako souborový server se StorSimple Virtual Array | Dokumentace Microsoftu
description: Tento třetí kurz StorSimple Virtual Array nasazení dává pokyn k nastavení virtuálního zařízení jako souborový server.
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
ms.openlocfilehash: f699e40a4a31b6d57b12a43ae307806d3f010015
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54267177"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>Nasazení virtuálních polí StorSimple – sady nahoru jako souborový server prostřednictvím webu Azure portal
![](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Úvod
Tento článek popisuje, jak provést počáteční nastavení, zaregistrujte StorSimple souborového serveru, dokončit nastavení zařízení a vytvořit a připojit ke sdíleným složkám protokolu SMB. Toto je poslední článek v sérii kurzů nasazení, které jsou nutné k úplné nasazení vaše virtuální pole jako souborový server nebo iSCSI server.

Instalace a konfigurace procesu může trvat přibližně 10 minut na dokončení. Informace v tomto článku se vztahují pouze na nasazení StorSimple Virtual Array. Nasazení zařízení StorSimple 8000 series přejděte na: [Nasazení vašeho zařízení StorSimple řady 8000 s aktualizací Update 2](storsimple-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Požadavky instalačního programu
Než začnete konfigurovat a nastavit StorSimple Virtual Array, ujistěte se, že:

* Zřízení virtuálního pole a k němu připojili podle popisu v [StorSimple Virtual Array Hyper-v zřídit](storsimple-virtual-array-deploy2-provision-hyperv.md) nebo [zřízení StorSimple Virtual Array ve službě VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Máte registrační klíč služby ze služby Správce zařízení StorSimple, kterou jste vytvořili pro správu virtuálních polí StorSimple. Další informace najdete v tématu [krok 2: Získání registračního klíče služby](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) pro StorSimple Virtual Array.
* Pokud je to druhém a dalším virtuální pole, které při registraci existující služby Správce zařízení StorSimple, měli byste šifrovací klíč dat služby. Tento klíč se vygeneroval při prvním zařízením byl úspěšně zaregistrován s touto službou. Pokud jste ztratili tento klíč, přečtěte si téma [získat šifrovací klíč dat služby](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) pro StorSimple Virtual Array.

## <a name="step-by-step-setup"></a>Podrobné nastavení
Pomocí následujících kroků vytvořit a nakonfigurovat StorSimple Virtual Array.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Krok 1: Dokončete instalaci místního webového uživatelského rozhraní a zaregistrujte zařízení
#### <a name="to-complete-the-setup-and-register-the-device"></a>K dokončení instalace a registrace zařízení
1. Otevřete okno prohlížeče a připojte se k místního webového uživatelského rozhraní. Zadejte:
   
   `https://<ip-address of network interface>`
   
   Pomocí adresy URL pro připojení, které jste si poznamenali v předchozím kroku. Můžete zobrazit chyba oznamující, že dojde k problému s certifikátem zabezpečení webu. Klikněte na tlačítko **pokračovat na tuto webovou stránku**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Přihlaste se k ve webovém uživatelském rozhraní virtuálního pole jako **StorSimpleAdmin**. Zadejte heslo správce zařízení, která jste změnili v kroku 3: Spustit virtuální pole [StorSimple Virtual Array Hyper-v zřídit](storsimple-virtual-array-deploy2-provision-hyperv.md) nebo v [zřízení StorSimple Virtual Array ve službě VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. Budete přesměrováni **Domů** stránky. Tato stránka popisuje různá nastavení, které jsou potřeba ke konfiguraci a zaregistrujte virtuální pole ve službě Správce zařízení StorSimple. **Nastavení sítě**, **nastavení proxy webu**, a **nastavení času** jsou volitelné. Pouze požadované nastavení, musí být **nastavení zařízení** a **nastavení cloudu**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. V **nastavení sítě** stránky **síťová rozhraní**, DATA 0 se automaticky nakonfigurují za vás. Každé síťové rozhraní je ve výchozím nastavení, chcete-li získat IP adresu automaticky (DHCP). IP adresu, podsíť a bránu se proto automaticky přiřazují (pro adresy IPv4 a IPv6).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Pokud přidáte více než jedno síťové rozhraní během zřizování zařízení, můžete je konfigurovat tady. Všimněte si, že síťové rozhraní můžete nakonfigurovat jako IPv4 nebo jen protokol IPv4 i IPv6. Nejsou podporovány pouze konfigurace IPv6.
5. Servery DNS jsou povinné, protože používají se při pokusu zařízení ke komunikaci s poskytovatelé cloudových služeb úložiště nebo pokud chcete vyřešit zařízení podle názvu, když je nakonfigurovaný jako souborový server. V **nastavení sítě** stránky **servery DNS**:
   
   1. Primární a sekundární server DNS se automaticky konfigurují. Pokud se rozhodnete ke konfiguraci statických IP adres, můžete určit servery DNS. Pro zajištění vysoké dostupnosti doporučujeme nakonfigurovat primární a sekundární server DNS.
   2. Klikněte na tlačítko **použít** použít a ověřit nastavení sítě.
6. V **nastavení zařízení** stránky:
   
   1. Přiřaďte jedinečnou **název** do vašeho zařízení. Tento název může obsahovat 1 až 15 znaků a může obsahovat písmena, číslice a pomlčky.
   2. Klikněte na tlačítko **souborového serveru se** ikonu ![](./media/storsimple-virtual-array-deploy3-fs-setup/image6.png) pro **typ** zařízení, kterou vytváříte. Souborový server vám umožní vytvořit sdílené složky.
   3. Vaše zařízení je souborový server, je potřeba připojit zařízení k doméně. Zadejte **název domény**.
   4. Klikněte na tlačítko **Použít**.
7. Zobrazí se dialogové okno. Zadejte přihlašovací údaje domény v zadaném formátu. Kliknutím na ikonu zaškrtnutí. Ověření pověření domény. Pokud přihlašovací údaje jsou nesprávné, zobrazí chybová zpráva.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Klikněte na tlačítko **Použít**. Tím se použít a ověřit nastavení zařízení.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Ujistěte se, že vaše virtuální pole je ve vlastní organizační jednotka (OU) služby Active Directory a žádné objekty zásad skupiny (GPO) jsou použita k němu nebo zděděná. Zásad skupiny může instalovat aplikace jako antivirový software StorSimple Virtual Array. Instalace dalšího softwaru není podporováno a může vést k poškození dat. 
   > 
   > 
9. (Volitelně) nakonfigurujte váš webový proxy server. Sice volitelné konfigurace webového proxy serveru, mějte na paměti, že pokud používáte webový proxy server, můžete pouze nakonfigurovat ji sem.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   V **webového proxy serveru** stránky:
   
   1. Zadat **webová adresa URL proxy serveru** v tomto formátu: *http://&lt;hostitele IP adresu nebo plně kvalifikovaný název domény&gt;: číslo portu*. Všimněte si, že nejsou podporovány adresy URL HTTPS.
   2. Zadejte **ověřování** jako **základní** nebo **žádný**.
   3. Pokud používáte ověřování, budete také muset poskytnout **uživatelské jméno** a **heslo**.
   4. Klikněte na tlačítko **Použít**. Bude to ověřit a použít nastavení nakonfigurované webového proxy serveru.
10. (Volitelně) nakonfigurujte nastavení času pro vaše zařízení, jako je časové pásmo a primární a sekundární server NTP. Servery NTP jsou požadovány, protože zařízení musí synchronizovat čas tak, aby ho mohli ověřit poskytovatelé cloudových služeb.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    V **nastavení času** stránky:
    
    1. Z rozevíracího seznamu, vyberte **časové pásmo** na základě geografického umístění, ve kterém se nasazuje zařízení. Výchozí časové pásmo pro vaše zařízení je PST. Toto časové pásmo bude zařízení používat pro všechny naplánované operace.
    2. Zadejte **primárního serveru NTP** pro vaše zařízení nebo přijměte výchozí hodnotu time.windows.com. Ujistěte se, že vaše síť umožňuje přenos dat NTP z vašeho datového centra na internet.
    3. Volitelně můžete zadat **sekundární NTP server** pro vaše zařízení.
    4. Klikněte na tlačítko **Použít**. Bude to ověřit a použít nastavení nakonfigurovaném čase.
11. Konfigurovat nastavení cloudu pro vaše zařízení. V tomto kroku se dokončit konfiguraci místního zařízení a pak zaregistrujte zařízení ve vaší službě Správce zařízení StorSimple.
    
    1. Zadejte **registrační klíč služby** , které jste získali [krok 2: Získání registračního klíče služby](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) pro StorSimple Virtual Array.
    2. Pokud je toto vaše první zařízení zaregistrujete tuto službu, zobrazí se **šifrovací klíč dat služby**. Klíč zkopírujte a uložte na bezpečném místě. Tento klíč je požadován spolu s registračním klíčem služby k registraci dalších zařízení ve službě Správce zařízení StorSimple. 
       
       Pokud to není první zařízení, které při registraci k této službě, je potřeba zadat šifrovací klíč dat služby. Další informace najdete v získat [šifrovací klíč dat služby](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) na vaše místní webové uživatelské rozhraní.
    3. Klikněte na tlačítko **zaregistrovat**. Tato operace restartuje zařízení. Budete muset počkat 2 až 3 minut, než se zařízení úspěšně zaregistruje. Po restartování zařízení, budete přesměrováni na přihlašovací stránku.
       
       ![](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Vraťte se na Azure Portal. Přejděte na **všechny prostředky**, vyhledávání služby Správce zařízení StorSimple.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. Ve filtrovaném seznamu, vyberte svoji službu Správce zařízení StorSimple a potom přejděte na **správy > zařízení**. V **zařízení** okno, ověřte, že zařízení úspěšně připojilo ke službě a má stav **připraveno k nastavení**.
    
    ![Konfigurace souborového serveru](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>Krok 2: Konfigurace zařízení jako souborový server
Proveďte následující kroky v [webu Azure portal](https://portal.azure.com/) k dokončení instalace požadované zařízení.

#### <a name="to-configure-the-device-as-file-server"></a>Ke konfiguraci zařízení jako souborový server
1. Přejděte do služby Správce zařízení StorSimple a potom přejděte ke **správy > zařízení**. V **zařízení** okno, vyberte zařízení, které jste právě vytvořili. Toto zařízení se zobrazují jako **připraveno k nastavení**.
   
   ![Konfigurace souborového serveru](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Klikněte na zařízení a uvidíte banner zprávu, že zařízení je připraveno k instalaci.
   
    ![Konfigurace souborového serveru](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Klikněte na tlačítko **konfigurovat** na panelu příkazů. Otevře **konfigurovat** okno. V **konfigurovat** okno, postupujte takto:
   
    1. Název souborového serveru se vyplní automaticky.
    
    2. Ujistěte se, že šifrování cloudového úložiště je nastavena na **povoleno**. To bude šifrování všech dat, které se odesílají do cloudu. 
    
    3. Klíč standardu AES 256 bitů se používá s uživatelský klíč pro šifrování. Zadejte klíč 32 znaků a potom znovu zadat klíč pro potvrzení. Záznam klíče v aplikaci pro správu klíčů pro budoucí použití.
    
    4. Klikněte na tlačítko **konfigurovat požadované nastavení** k zadání přihlašovacích údajů účtu úložiště pro použití s vaším zařízením. Klikněte na tlačítko **přidat nový** Pokud nejsou nakonfigurována žádná pověření účtu úložiště. **Ujistěte se, že účet úložiště, že použijte podporuje objekty BLOB bloku. Objekty BLOB stránky nejsou podporovány.** Další informace o [blokuje objekty BLOB a objekty BLOB stránky](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).
   
    ![Konfigurace souborového serveru](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. V **přidat přihlašovací údaje účtu úložiště** okno, postupujte takto: 

    1. Vyberte předplatné, aktuální, pokud je účet úložiště ve stejném předplatném jako služba. Zadejte jiné úložiště je účet nepochází z předplatného služby. 
    
    2. Z rozevíracího seznamu vyberte existující účet úložiště. 
    
    3. Umístění se automaticky vyplní podle zadaný účet úložiště. 
    
    4. Povolte protokol SSL zajistit zabezpečený komunikační kanál mezi zařízením a cloudem.
    
    5. Klikněte na tlačítko **přidat** přidat tento přihlašovací údaj účtu úložiště. 
   
        ![Konfigurace souborového serveru](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. Po úspěšném vytvoření přihlašovacích údajů účtu úložiště **konfigurovat** okno se aktualizuje a zobrazí zadané přihlašovací údaje. Klikněte na **Konfigurovat**.
   
   ![Konfigurace souborového serveru](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   Který se zobrazí soubor serveru se vytváří. Po úspěšném vytvoření souborového serveru, budete upozorněni.
   
   ![Konfigurace souborového serveru](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   Stav zařízení se také změní na **Online**.
   
   ![Konfigurace souborového serveru](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   Můžete přejít k přidání sdílené složky.

## <a name="step-3-add-a-share"></a>Krok 3: Přidání sdílené složky
Provedením následujících kroků na webu [Azure Portal](https://portal.azure.com/) vytvořte sdílenou složku.

#### <a name="to-create-a-share"></a>Vytvoření sdílené složky
1. Vyberte zařízení, server souboru jste nakonfigurovali v předchozím kroku a klikněte na tlačítko **...**  (nebo kliknutím pravým tlačítkem). V místní nabídce vyberte **přidat sdílenou složku**. Případně můžete kliknout na **+ přidat sdílenou složku** na panelu příkazů zařízení.
   
   ![Přidání sdílené složky](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Zadejte následující nastavení sdílené složky:

    1. Jedinečný název pro svou sdílenou složku. Název musí být řetězec, který obsahuje 3 až 127 znaků.
    
    2. Volitelně **popis** pro sdílenou složku. Popis vám pomůže identifikovat vlastníkům sdílené složky.
    
    3. A **typ** pro sdílenou složku. Typ může být **Vrstvená** nebo **místně připnuté**, s vrstvami, je výchozí hodnota. Pro úlohy, které vyžadují místní záruky, nízkou latenci a vyšší výkon, vyberte **místně připnuté** sdílet. Všechna ostatní data, vyberte **Vrstvená** sdílet.
    Místně připojené sdílené složky je tlustě zřízený a zajišťuje, že primární data ve sdílené složce zůstane na místním zařízení a uložená do cloudu. Vrstvenou sdílenou složku na druhé straně je tence zřízený. Když vytváříte vrstvenou sdílenou složku, 10 % prostoru musí být zřízena na místní úrovni a je zajištěno 90 % prostoru v cloudu. Například pokud jste zřídili svazku 1 TB, 100 GB by jsou umístěny v místním prostoru a v cloudu se použije 900 GB při datovou vrstvou. Pak z toho vyplývá, že pokud vyčerpáte veškeré volné místo na zařízení, nemůžete zřídit vrstvenou sdílenou složku.
   
    4. V **nastavit výchozí úplné oprávnění** pole, přiřaďte oprávnění pro uživatele nebo skupinu, která přistupuje k této sdílené složce. Zadejte název uživatele nebo skupiny uživatelů v *john@contoso.com* formátu. Doporučujeme použít skupiny uživatelů (místo jednoho uživatele) umožňující správu oprávnění pro přístup k těmto sdíleným složkám. Jakmile tady přiřadíte oprávnění, můžete pak tato oprávnění upravit pomocí Průzkumníka souborů.
   
    5. Klikněte na tlačítko **přidat** sdílenou složku vytvořit. 
    
        ![Přidání sdílené složky](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
        Zobrazí se oznámení o probíhajícím vytváření sdílené složky.
   
        ![Přidání sdílené složky](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
    Po vytvoření sdílené složky se zadaným nastavením **sdílené složky** okno aktualizuje tak, aby odrážely novou sdílenou složku. Ve výchozím nastavení jsou povolené sledování a zálohování pro sdílenou složku.
   
    ![Přidání sdílené složky](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>Krok 4: Připojení ke sdílené složce
Teď je potřeba připojit jeden nebo více sdílených složek, které jste vytvořili v předchozím kroku. Tyto kroky proveďte na hostiteli systému Windows Server připojený k StorSimple Virtual Array.

#### <a name="to-connect-to-the-share"></a>Pro připojení ke sdílené složce
1. Stisknutím klávesy ![](./media/storsimple-virtual-array-deploy3-fs-setup/image22.png) + R. Do okna Spustit zadejte *&#92; &#92; &lt;název souborového serveru&gt;* jako cestu, nahrazení *název souborového serveru* s názvem zařízení, který jste přiřadili do souboru Server. Klikněte na **OK**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Otevře Průzkumníka souborů. Sdílené složky, které jste vytvořili, by se teď měly zobrazit jako složky. Pokud chcete zobrazit obsah sdílené složky, vyberte příslušnou složku a dvakrát na ni klikněte.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. Teď můžete přidat soubory do těchto sdílených složek a vytvořit zálohu.

## <a name="next-steps"></a>Další postup
Další informace o použití místní webové uživatelské rozhraní pro [spravovat StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).


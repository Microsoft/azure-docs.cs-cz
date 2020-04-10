---
title: zahrnout soubor
description: zahrnout soubor
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 02/10/2020
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: a9e8574ea2d7222871c7f065383e6c0c62057dd3
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "81007863"
---
## <a name="generate-the-certificate-signing-request-file"></a>Generovat soubor žádosti o podpis certifikátu

Služba nabízených oznámení Apple (APNs) používá k ověření nabízených oznámení certifikáty. Pokud chcete vytvořit nabízený certifikát pro odesílání a přijímání oznámení, postupujte podle těchto pokynů. Další informace o těchto konceptech najdete v oficiální dokumentaci ke službě [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Vygenerujte soubor žádosti o podpis (CSR), který Společnost Apple používá ke generování podepsaného nabízeného certifikátu.

1. V Macu spusťte nástroj Keychain Access. Lze jej otevřít ze složky **Nástroje** nebo **Jiné** složky na launchpadu.

1. Vyberte **položku Přístup k řetězci klíčů**, rozbalte **položku Pomocník a**potom **vyberte možnost Požádat o certifikát od certifikační autority**.

    ![Použití nástroje Keychain Access k vyžádání nového certifikátu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > Ve výchozím nastavení vybere aplikace Keychain Access první položku v seznamu. To může být problém, pokud jste v kategorii **Certifikáty** a **Apple Worldwide Developer Relations Certification Authority** není první položkou v seznamu. Před generováním zástupce oddělení služeb oddělení služeb nic neklíčite se ujistěte, že máte položku, která není klíčem klíče klíče, nebo zda je vybrán klíč **Certifikační autority pro vztahy s vývojáři společnosti Apple Worldwide Developer** Relations, a zda je vybrán klíč CSR (Žádost o podpis certifikátu).

1. Vyberte **uživatelskou e-mailovou adresu**, zadejte hodnotu **Common Name,** ujistěte se, že jste zadali **Uloženo na disk**, a pak vyberte **Pokračovat**. Ponechejte **e-mailovou adresu certifikační autority** prázdnou, protože není vyžadována.

    ![Požadované informace o certifikátu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Zadejte název souboru zástupce v **části Uložit jako**, vyberte umístění v části **Kde**a pak vyberte **Uložit**.

    ![Výběr názvu souboru pro certifikát](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Tato akce uloží soubor ZÁSTUPCE do vybraného umístění. Výchozí umístění je **Plocha**. Zapamatujte si umístění tohoto souboru.

Dále zaregistrujte aplikaci u společnosti Apple, povolte nabízená oznámení a nahrajte exportovnou csr a vytvořte nabízený certifikát.

## <a name="register-your-app-for-push-notifications"></a>Registrace aplikace pro nabízená oznámení

Pokud chcete odesílat nabízená oznámení do aplikace pro iOS, zaregistrujte svou aplikaci u společnosti Apple a také se zaregistrujte pro nabízená oznámení.  

1. Pokud jste si ještě aplikaci nezaregistrovali, přejděte na [portál zřizování iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) v Centru pro vývojáře Apple. Přihlaste se k portálu pomocí svého Apple ID a vyberte **Identifikátory**. Pak **+** vyberte zaregistrovat novou aplikaci.

    ![Stránka ID aplikací na portálu zřizování iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Na obrazovce **Registrovat nový identifikátor** vyberte přepínací tlačítko **ID aplikace.** Potom vyberte **Pokračovat**.

    ![Portál zřizování iOS registruje novou stránku ID](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

3. Aktualizujte následující tři hodnoty pro novou aplikaci a pak vyberte **Pokračovat**:

   * **Popis**: Zadejte popisný název aplikace.

   * **ID balíčku:** Zadejte ID sady formuláře **Organization Identifier.Product Name,** jak je uvedeno v [Průvodci distribucí aplikací](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). Hodnoty *identifikátoru organizace* a *názvu produktu* se musí shodovat s identifikátorem organizace a názvem produktu, který používáte při vytváření projektu Xcode. Na následujícím snímku obrazovky se jako identifikátor organizace používá hodnota **NotificationHubs** a jako název produktu se používá hodnota **GetStarted.** Ujistěte se, že hodnota **identifikátoru svazku** odpovídá hodnotě v projektu Xcode, aby Xcode používá správný profil publikování.

      ![Stránka ID aplikace registruje portál iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Nabízená oznámení**: Zkontrolujte možnost **Nabízená oznámení** v části **Možnosti.**

      ![Formulář pro registraci nového ID aplikace](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Tato akce vygeneruje ID aplikace a požádá vás o potvrzení informací. Vyberte **Pokračovat**a pak vyberte **Registrovat,** abyste potvrdili nové ID aplikace.

      ![Potvrdit nové ID aplikace](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      Po výběru **možnosti Registrovat**se nové ID aplikace zobrazí jako řádková položka na stránce **Certifikáty, Identifikátory & profily.**

4. Na stránce **Certifikáty, identifikátory & profily** **vyhledejte**řádkovou položku ID aplikace, kterou jste právě vytvořili, a vyberte její řádek, aby se zobrazila obrazovka **Upravit konfiguraci ID aplikace.**

## <a name="creating-a-certificate-for-notification-hubs"></a>Vytvoření certifikátu pro centra oznámení
K povolení práce centra s oznamovacíslužbou **apns**je vyžadován certifikát. To lze provést jedním ze dvou způsobů:

1. **Vytvořte.p12,** který lze nahrát přímo do Centra oznámení.  
2. Vytvořte **.p8,** který lze použít pro [ověřování založené na tokenech](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification) *(novější přístup*).

Novější přístup má řadu výhod (ve srovnání s použitím certifikátů), jak je popsáno v [ověřování založené na tokenu (HTTP/2) pro APNS](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification). Byly však stanoveny kroky pro oba přístupy. 

### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>MOŽNOST 1: Vytvoření nabízeného certifikátu .p12, který lze odeslat přímo do centra oznámení

1. Posuňte se dolů na zaškrtnutou možnost **Nabízená oznámení** a pak vyberte **Konfigurovat,** chcete-li vytvořit certifikát.

    ![Úprava stránky ID aplikace](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

2. Zobrazí se okno **Apple Push Notification service SSL Certificates** . V části **Vývojový certifikát SSL** vyberte tlačítko **Vytvořit certifikát.**

    ![Tlačítko Vytvořit certifikát pro ID aplikace](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Zobrazí se obrazovka **Vytvořit nový certifikát.**

    > [!NOTE]
    > Tento kurz používá vývojový certifikát. Stejný postup se používá při registraci produkčního certifikátu. Dejte pozor, abyste při odesílání oznámení používali stejný typ certifikátu.

3. Vyberte **Zvolit soubor**, vyhledejte umístění, do kterého jste uložili soubor ZÁSTUPCE z prvníúlohy, a potom poklepejte na název certifikátu, který chcete načíst. Potom vyberte **Pokračovat**.

4. Po portálu vytvoří certifikát, vyberte **tlačítko Stáhnout.** Uložte certifikát a zapamatujte si umístění, do kterého je uložen.

    ![Stránka pro stažení vygenerovaného certifikátu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Certifikát se stáhne a uloží do počítače ve složce **Soubory ke stažení.**

    ![Vyhledání souboru certifikátu ve složce stažených souborů](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Ve výchozím nastavení je stažený vývojový certifikát pojmenován **aps_development.cer**.

5. Poklikejte na stažený nabízený certifikát **aps_development.cer**. Tato akce nainstaluje nový certifikát do Klíčenky, jak je znázorněno na následujícím obrázku:

    ![Seznam certifikátů nástroje Keychain Access zobrazující nový certifikát](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > I když se název v certifikátu může lišit, bude předponou **s nabízenými službami pro vývoj iOS společnosti Apple**.

6. V nástroji Keychain Access, klikněte pravým tlačítkem na nový nabízený certifikát, který jste vytvořili v kategorii **Certifikáty**. Vyberte **Exportovat**, pojmenujte soubor, vyberte formát **.p12** a pak vyberte **Uložit**.

    ![Export certifikátu ve formátu p12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Certifikát můžete chránit heslem, ale toto je volitelné. Pokud chcete obejít vytvoření hesla, klepněte na **tlačítko OK.** Poznamenejte si název souboru a umístění exportovaného certifikátu .p12. Používají se k povolení ověřování pomocí souborů APN.

    > [!NOTE]
    > Název a umístění souboru .p12 se může lišit od toho, co je znázorněno v tomto kurzu.

### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>MOŽNOST 2: Vytvoření certifikátu .p8, který lze použít pro ověřování založené na tokenech

1. Poznamenejte si následující podrobnosti:

    - **Předpona ID aplikace** (toto je **ID týmu)**
    - **ID sady prostředků**
    
2. Zpět v **certifikátech, identifikátorech & profilech**klepněte na tlačítko **Klíče**.

   > [!NOTE]
   > Pokud již máte klíč nakonfigurovaný pro **soubor APNS**, můžete certifikát .p8, který jste stáhli, znovu použít ihned po jeho vytvoření. Pokud ano, můžete ignorovat kroky **3** až **5**.

3. Kliknutím **+** na tlačítko (nebo tlačítko **Vytvořit klíč)** vytvořte nový klíč.
4. Zadejte vhodnou hodnotu **Název klíče,** zaškrtněte možnost **služby Nabízených oznámení Apple (APNs)** a na další obrazovce klikněte na **Pokračovat**a potom **register.**
5. Klepněte na tlačítko **Stáhnout** a přesuňte soubor **P8** (s předponou *s AuthKey_*) do zabezpečeného místního adresáře a klepněte na **tlačítko Hotovo**.

   > [!NOTE] 
   > Uchovávejte soubor .p8 na bezpečném místě (a uložte zálohu). Po stažení klíče jej nelze znovu stáhnout při odebrání kopie serveru.
  
6. Na **klávesy**, klikněte na klíč, který jste právě vytvořili (nebo existující klíč, pokud jste se rozhodli použít, že místo).
7. Poznamenejte si hodnotu **ID klíče.**
8. Otevřete certifikát .p8 ve vhodné aplikaci podle vašeho výběru, jako je [**například Visual Studio Code**](https://code.visualstudio.com) a poznamenejte si hodnotu klíče. Toto je hodnota mezi **-----BEGIN PRIVATE KEY-----** a **-----END PRIVATE KEY-----** .

    ```
    -----BEGIN PRIVATE KEY-----
    <key_value>
    -----END PRIVATE KEY-----
    ```

    > [!NOTE]
    > Toto je **hodnota tokenu,** která bude později použita ke konfiguraci **centra oznámení**. 

Na konci těchto kroků byste měli mít následující informace pro pozdější použití v [aplikaci Configure your notification hub with APNs information](#configure-your-notification-hub-with-apns-information):

- **ID týmu** (viz krok 1)
- **ID balíčku** (viz krok 1)
- **ID klíče** (viz krok 7)
- **Hodnota tokenu,** tj.

## <a name="create-a-provisioning-profile-for-the-app"></a>Vytvoření zřizovacího profilu pro aplikaci

1. Vraťte se na [portál zřizování iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), vyberte **certifikáty, identifikátory & profily**, vyberte **profily** v levé nabídce a pak vyberte **+** vytvoření nového profilu. Zobrazí se obrazovka **Registrovat nový zřizovací profil.**

1. Vyberte **vývoj aplikací pro iOS** v části **Vývoj** jako typ zřizovacího profilu a pak vyberte **Pokračovat**.

    ![Seznam zřizovacích profilů](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Dále vyberte ID aplikace, které jste **vytvořili,** v rozevíracím seznamu ID aplikace a vyberte **Pokračovat**.

    ![Výběr ID aplikace](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. V okně Vybrat certifikáty vyberte vývojový certifikát, který používáte pro **podepisování** kódu, a vyberte **Pokračovat**. Tento certifikát není nabízený certifikát, který jste vytvořili. Pokud neexistuje, musíte jej vytvořit. Pokud certifikát existuje, přejděte k dalšímu kroku. Chcete-li vytvořit certifikát vývoje, pokud neexistuje:

    1. Pokud se zobrazí **možnost Žádné certifikáty nejsou k dispozici**, vyberte vytvořit **certifikát**.
    2. V části **Software** vyberte **položku Apple Development**. Potom vyberte **Pokračovat**.
    3. Na obrazovce **Vytvořit nový certifikát** vyberte Zvolit **soubor**.
    4. Přejděte k certifikátu **žádosti o podpis certifikátu,** který jste vytvořili dříve, vyberte ho a pak vyberte **Otevřít**.
    5. Vyberte **Pokračovat**.
    6. Stáhněte si certifikát vývoje a zapamatujte si umístění, do kterého je uložen.

1. Vraťte se na stránku **Certifikáty, Identifikátory & Profily,** v levé nabídce vyberte **Profily** a pak vyberte **+** vytvoření nového profilu. Zobrazí se obrazovka **Registrovat nový zřizovací profil.**

1. V okně **Vybrat certifikáty** vyberte právě vytvořený vývojový certifikát. Potom vyberte **Pokračovat**.

1. Dále vyberte zařízení, která chcete použít k testování, a vyberte **Pokračovat**.

1. Nakonec vyberte název profilu v **zřizování názvu profilu**a vyberte **generovat**.

    ![Volba názvu zřizovacího profilu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Po vytvoření nového profilu zřizování vyberte **stáhnout**. Zapamatujte si umístění, do kterého je uložen.

1. Přejděte do umístění profilu zřizování a poklepáním na něj nainstalujte do vývojového počítače Xcode.

## <a name="create-a-notification-hub"></a>Vytvoříte centrum oznámení.

V této části vytvoříte centrum oznámení a nakonfigurujete ověřování pomocí center pomocí nabízeného certifikátu .p12 nebo ověřování založeného na tokenech. Pokud chcete použít centrum oznámení, které jste už vytvořili, můžete přeskočit ke kroku 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Konfigurace centra oznámení s informacemi o kódech APN

V části **Notification Services**vyberte **Apple (APNS)** a postupujte podle příslušných kroků na základě přístupu, který jste zvolili dříve v části [Vytvoření certifikátu pro centra oznámení.](#creating-a-certificate-for-notification-hubs)  

> [!NOTE]
> Režim **Produkční** **pro aplikaci** použijte jenom v případě, že chcete odesílat nabízená oznámení uživatelům, kteří si koupili vaši aplikaci v obchodě.

### <a name="option-1-using-a-p12-push-certificate"></a>MOŽNOST 1: Použití nabízeného certifikátu .p12

1. Vyberte **Certifikát**.

1. Vyberte ikonu souboru.

1. Vyberte dříve exportovaný soubor P12 a pak vyberte **Otevřít**.

1. V případě potřeby zadejte správné heslo.

1. Vyberte režim **Izolovaný prostor**.

    ![Konfigurace certifikační služby APNs na webu Azure Portal](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. Vyberte **Uložit**.

### <a name="option-2-using-token-based-authentication"></a>MOŽNOST 2: Použití ověřování na základě tokenů

1. Vyberte **token**.
1. Zadejte následující hodnoty, které jste získali dříve:

    - **ID klíče**
    - **ID sady prostředků**
    - **ID týmu**
    - **Token** 

1. Zvolte **Sandbox**
1. Vyberte **Uložit**. 

Nyní jste centrum oznámení nakonfigurovali pomocí kódů APN. Máte také připojovací řetězce pro registraci aplikace a odesílání nabízených oznámení.

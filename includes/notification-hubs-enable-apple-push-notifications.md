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
ms.openlocfilehash: 1cb7753f54e9c1334e35635c227f776041631f1d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88864802"
---
## <a name="generate-the-certificate-signing-request-file"></a>Generování souboru žádosti o podepsání certifikátu

K ověření nabízených oznámení používá Apple Push Notification Service (APNs) certifikáty. Pokud chcete vytvořit nabízený certifikát pro odesílání a přijímání oznámení, postupujte podle těchto pokynů. Další informace o těchto konceptech najdete v oficiální dokumentaci ke službě [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Vygenerujte soubor žádosti o podepsání certifikátu (CSR), který Apple používá k vygenerování podepsaného nabízeného certifikátu.

1. V Macu spusťte nástroj Keychain Access. Dá se otevřít ze složky **nástrojů** nebo **jiné** složky na hlavní straně.

1. Vyberte možnost **přístup do řetězce klíčů**, rozbalte **Pomocníka s certifikátem**a pak vyberte **požádat o certifikát od certifikační autority**.

    ![Použití nástroje Keychain Access k vyžádání nového certifikátu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > Ve výchozím nastavení přístup k řetězci klíčů vybere první položku v seznamu. To může být problém, pokud jste v kategorii **certifikáty** a pokud je **certifikační autorita pro vývojáře od společnosti Apple celosvětová** , nejedná se o první položku v seznamu. Ujistěte se, že máte neklíčovou položku, a před vygenerováním CSR (žádost o podepsání certifikátu) je vybraná možnost klíč **certifikační autority Apple celosvětově Developer Relations** .

1. Vyberte svou **e-mailovou adresu uživatele**, zadejte hodnotu pro **běžný název** , ujistěte se, že jste zadali možnost **uloženo na disk**a pak vyberte **pokračovat**. Ponechte **e-mailovou adresu CA** prázdnou, protože není potřeba.

    ![Požadované informace o certifikátu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Do pole **Uložit jako**zadejte název souboru CSR, vyberte umístění, **kde**se nachází, a pak vyberte **Uložit**.

    ![Vyberte název souboru certifikátu.](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Tato akce uloží soubor CSR do vybraného umístění. Výchozí umístění je **Desktop**. Zapamatujte si umístění tohoto souboru.

Pak Zaregistrujte svoji aplikaci pomocí Apple, povolte nabízená oznámení a nahrajte exportovaný zástupce a vytvořte certifikát push.

## <a name="register-your-app-for-push-notifications"></a>Registrace aplikace pro nabízená oznámení

Pokud chcete odesílat nabízená oznámení do aplikace pro iOS, Zaregistrujte svoji aplikaci pomocí Apple a zaregistrujte se i pro nabízená oznámení.  

1. Pokud jste svou aplikaci ještě nezaregistrovali, přejděte na [portál pro zřizování iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) na webu Apple Developer Center. Přihlaste se k portálu pomocí Apple ID a vyberte **identifikátory**. Pak vyberte **+** registraci nové aplikace.

    ![Stránka ID aplikací na portálu zřizování iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Na obrazovce **registrovat nový identifikátor** vyberte přepínač **ID aplikací** . Potom vyberte **Pokračovat**.

    ![Stránka pro zřizování iOS registrace – nové ID](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

3. Aktualizujte následující tři hodnoty pro novou aplikaci a pak vyberte **pokračovat**:

   * **Popis**: zadejte popisný název vaší aplikace.

   * **ID sady prostředků**: Zadejte ID sady prostředků **identifikátoru organizace. název produktu** , jak je uvedeno v [Průvodci distribucí aplikací](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). *Identifikátor organizace* a hodnoty *názvu produktu* se musí shodovat s identifikátorem organizace a názvem produktu, který použijete při vytváření projektu Xcode. Na následujícím snímku obrazovky se hodnota **NotificationHubs** používá jako identifikátor organizace a jako název produktu se používá hodnota **getstarted** . Ujistěte se, že hodnota **identifikátoru sady prostředků** odpovídá hodnotě v projektu Xcode, takže Xcode používá správný profil publikování.

      ![Stránka ID aplikace registrace portálu pro zřizování iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Nabízená oznámení**: v části **Možnosti** si přečtěte možnost **nabízená oznámení** .

      ![Formulář pro registraci nového ID aplikace](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Tato akce vygeneruje vaše ID aplikace a žádosti o potvrzení informací. Vyberte **pokračovat**a pak vyberte **zaregistrovat** a potvrďte nové ID aplikace.

      ![Potvrdit nové ID aplikace](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      Po výběru položky **zaregistrovat**se na stránce **certifikáty, identifikátory & profily** zobrazí nové ID aplikace jako položka řádku.

4. Na stránce **certifikáty, identifikátory & profily** v části **identifikátory**Najděte položku řádku ID aplikace, kterou jste právě vytvořili, a výběrem jejího řádku zobrazte obrazovku pro **úpravu konfigurace ID aplikace** .

## <a name="creating-a-certificate-for-notification-hubs"></a>Vytváření certifikátu pro Notification Hubs
K tomu, aby Centrum oznámení fungovalo s **APNs**, se vyžaduje certifikát. To lze provést jedním ze dvou způsobů:

1. Vytvořte **. p12** , který se dá nahrát přímo do centra oznámení.  
2. Vytvořte soubor **. P8** , který se dá použít pro [ověřování založené na tokenech](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification) (*novější přístup*).

Novější přístup má řadu výhod (ve srovnání s používáním certifikátů), jak je popsáno v [ověřování na základě tokenu (http/2) pro služby APN](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification). Nicméně byly k dispozici kroky pro oba přístupy. 

### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>MOŽNOST 1: vytvoření nabízeného certifikátu. p12, který se dá nahrát přímo do centra oznámení.

1. Přejděte dolů k možnosti zaškrtnutá **nabízená oznámení** a pak vyberte **Konfigurovat** a vytvořte certifikát.

    ![Úprava stránky ID aplikace](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

2. Zobrazí se okno **certifikáty SSL služby Apple Push Notification Service** . V části **vývojový certifikát protokolu SSL** vyberte tlačítko **vytvořit certifikát** .

    ![Tlačítko Vytvořit certifikát pro ID aplikace](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Zobrazí se obrazovka **vytvořit nový certifikát** .

    > [!NOTE]
    > V tomto kurzu se používá vývojový certifikát, který vaše aplikace používá k vygenerování jedinečného tokenu zařízení. Stejný postup se používá při registraci produkčního certifikátu. Dejte pozor, abyste při odesílání oznámení používali stejný typ certifikátu.

3. Vyberte **zvolit soubor**, přejděte do umístění, kam jste ULOŽILI soubor CSR z prvního úkolu, a potom dvakrát klikněte na název certifikátu a načtěte ho. Potom vyberte **Pokračovat**.

4. Jakmile portál vytvoří certifikát, klikněte na tlačítko **Stáhnout** . Uložte certifikát a zapamatujte si umístění, do kterého se uložilo.

    ![Stránka pro stažení vygenerovaného certifikátu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Certifikát se stáhne a uloží do vašeho počítače ve složce **stažené soubory** .

    ![Vyhledání souboru certifikátu ve složce stažených souborů](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Ve výchozím nastavení se stažený vývojový certifikát jmenuje **aps_development. cer**.

5. Poklikejte na stažený nabízený certifikát **aps_development.cer**. Tato akce nainstaluje nový certifikát do Klíčenky, jak je znázorněno na následujícím obrázku:

    ![Seznam certifikátů nástroje Keychain Access zobrazující nový certifikát](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > I když se název v certifikátu může lišit, název bude předponou **Apple Development iOS push Services**.

6. V nástroji Keychain Access, klikněte pravým tlačítkem na nový nabízený certifikát, který jste vytvořili v kategorii **Certifikáty**. Vyberte **exportovat**, zadejte název souboru, vyberte formát **. p12** a pak vyberte **Uložit**.

    ![Export certifikátu ve formátu p12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Můžete si vybrat, že chcete certifikát chránit heslem, ale to je volitelné. Pokud chcete obejít vytváření hesel, klikněte na **OK** . Poznamenejte si název souboru a umístění exportovaného certifikátu .p12. Používají se k povolení ověřování pomocí služby APNs.

    > [!NOTE]
    > Název souboru. p12 a jeho umístění se mohou lišit od obrázku v tomto kurzu.

### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>MOŽNOST 2: vytvoření certifikátu. P8, který se dá použít pro ověřování založené na tokenech

1. Poznamenejte si následující podrobnosti:

    - **Předpona ID aplikace** (Toto je **ID týmu**)
    - **ID sady prostředků**
    
2. Zpět v **certifikátech, identifikátory & profily**, klikněte na **klíče**.

   > [!NOTE]
   > Pokud už máte ke službě **APN**nakonfigurovaný klíč, můžete znovu použít certifikát. P8, který jste stáhli hned po jeho vytvoření. Pokud ano, můžete ignorovat kroky **3** až **5**.

3. Kliknutím na **+** tlačítko (nebo na tlačítko **vytvořit klíč** ) vytvoříte nový klíč.
4. Zadejte vhodnou hodnotu **názvu klíče** , potom zaškrtněte možnost **Služba APNs (Apple Push Notification Service)** a potom klikněte na tlačítko **pokračovat**a potom na další obrazovku **Zaregistrujte** .
5. Klikněte na **Stáhnout** a pak přesuňte soubor **. P8** (s předponou *AuthKey_*) do zabezpečeného místního adresáře a pak klikněte na **Hotovo**.

   > [!NOTE] 
   > Nezapomeňte soubor. P8 udržovat na bezpečném místě (a uložte zálohu). Po stažení se klíč nedá znovu stáhnout, protože se odebere kopie serveru.
  
6. V části **klíče**klikněte na klíč, který jste právě vytvořili (nebo vyberte existující klíč, pokud jste se místo toho rozhodli použít).
7. Poznamenejte si hodnotu **ID klíče** .
8. Otevřete svůj certifikát. P8 ve vhodné aplikaci, například [**Visual Studio Code**](https://code.visualstudio.com) pak si poznamenejte hodnotu klíče. Jedná se o hodnotu v rozsahu **-----zahájit-----privátního klíče** a **-----KONCOVým-----privátního klíče** .

    ```
    -----BEGIN PRIVATE KEY-----
    <key_value>
    -----END PRIVATE KEY-----
    ```

    > [!NOTE]
    > Toto je **hodnota tokenu** , která bude později použita ke konfiguraci **centra oznámení**. 

Na konci těchto kroků byste měli mít následující informace pro pozdější použití v části [Konfigurace centra oznámení s informacemi o službě APN](#configure-your-notification-hub-with-apns-information):

- **ID týmu** (viz krok 1)
- **ID sady prostředků** (viz krok 1)
- **ID klíče** (viz krok 7)
- **Hodnota tokenu** , tj. hodnota klíče. P8 (viz krok 8)

## <a name="create-a-provisioning-profile-for-the-app"></a>Vytvoření zřizovacího profilu pro aplikaci

1. Vraťte se na [portál zřizování iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), vyberte **certifikáty, identifikátory & profily**, v nabídce vlevo vyberte **profily** a pak vyberte **+** vytvořit nový profil. Zobrazí se obrazovka **registrovat nový profil pro zřizování** .

1. Jako typ zřizovacího profilu vyberte **vývoj aplikací pro iOS** v části **vývoj** a pak vyberte **pokračovat**.

    ![Seznam zřizovacích profilů](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Potom v rozevíracím seznamu **ID aplikace** vyberte ID aplikace, které jste vytvořili, a vyberte **pokračovat**.

    ![Výběr ID aplikace](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. V okně **Vybrat certifikáty** vyberte vývojový certifikát, který používáte pro podepisování kódu, a vyberte **pokračovat**. Tento certifikát není certifikátem push, který jste vytvořili. Pokud jeden neexistuje, musíte ho vytvořit. Pokud certifikát existuje, přejděte k dalšímu kroku. Pokud neexistuje certifikát pro vývoj, vytvořte ho:

    1. Pokud vidíte, že **nejsou k dispozici žádné certifikáty**, vyberte **vytvořit certifikát**.
    2. V části **software** vyberte možnost **vývoj pro Apple**. Potom vyberte **Pokračovat**.
    3. Na obrazovce **vytvořit nový certifikát** vyberte **zvolit soubor**.
    4. Přejděte na certifikát **žádosti o podepsání certifikátu** , který jste vytvořili dříve, vyberte ho a pak vyberte **otevřít**.
    5. Vyberte **Pokračovat**.
    6. Stáhněte si vývojový certifikát a zapamatujte si umístění, do kterého se uložilo.

1. Vraťte se na stránku **certifikáty, identifikátory & profily** , v nabídce vlevo vyberte **profily** a pak vyberte **+** Vytvoření nového profilu. Zobrazí se obrazovka **registrovat nový profil pro zřizování** .

1. V okně **Vybrat certifikáty** vyberte vývojový certifikát, který jste právě vytvořili. Potom vyberte **Pokračovat**.

1. Potom vyberte zařízení, která chcete použít pro testování, a vyberte **pokračovat**.

1. Nakonec zvolte název profilu v **názvu zřizovacího profilu**a vyberte **Generovat**.

    ![Volba názvu zřizovacího profilu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Po vytvoření nového zřizovacího profilu vyberte **Stáhnout**. Zapamatujte si umístění, do kterého se uložilo.

1. Přejděte do umístění profilu pro zřizování a dvakrát na něj klikněte a nainstalujte ho do vývojového počítače s Xcode.

## <a name="create-a-notification-hub"></a>Vytvoříte centrum oznámení.

V této části vytvoříte centrum oznámení a nakonfigurujete ověřování pomocí služby APNs pomocí certifikátu push push Certificate nebo ověřování založeného na tokenech. Pokud chcete použít Centrum oznámení, které jste už vytvořili, můžete přeskočit na krok 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Konfigurace centra oznámení pomocí informací APNs

V části **Notification Services**vyberte **Apple (APNs)** a pak postupujte podle příslušných kroků na základě postupu, který jste zvolili dříve v části [Vytvoření certifikátu pro Notification Hubs](#creating-a-certificate-for-notification-hubs) .  

> [!NOTE]
> Pokud sestavíte aplikaci pomocí App Storu nebo distribučního profilu ad hoc, použijte režim **produkčního** prostředí pro **aplikaci**. To umožní vašemu zařízení odesílat nabízená oznámení uživatelům, kteří si zakoupili vaši aplikaci ze Storu.

### <a name="option-1-using-a-p12-push-certificate"></a>MOŽNOST 1: použití nabízeného certifikátu. P12

1. Vyberte **Certifikát**.

1. Vyberte ikonu souboru.

1. Vyberte soubor. p12, který jste dříve exportovali, a pak vyberte **otevřít**.

1. V případě potřeby zadejte správné heslo.

1. Vyberte režim **Izolovaný prostor**.

    ![Konfigurace certifikační služby APNs na webu Azure Portal](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. Vyberte **Uložit**.

### <a name="option-2-using-token-based-authentication"></a>MOŽNOST 2: použití ověřování založeného na tokenech

1. Vyberte **token**.
1. Zadejte následující hodnoty, které jste získali dříve:

    - **ID klíče**
    - **ID sady prostředků**
    - **ID týmu**
    - **Token** 

1. Zvolit **izolovaný prostor**
1. Vyberte **Uložit**. 

Nyní jste nakonfigurovali centrum oznámení pomocí služby APNs. Máte také připojovací řetězce k registraci aplikace a odesílání nabízených oznámení.

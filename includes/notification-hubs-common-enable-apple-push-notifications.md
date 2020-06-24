---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 72065ce602c6d29255a3500e26d8c6f36a19ebed
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081377"
---
### <a name="register-your-ios-app-for-push-notifications"></a>Registrace aplikace pro iOS pro nabízená oznámení

Pokud chcete odesílat nabízená oznámení do aplikace pro iOS, Zaregistrujte svoji aplikaci pomocí Apple a zaregistrujte se i pro nabízená oznámení.  

1. Pokud jste svou aplikaci ještě nezaregistrovali, přejděte na [portál pro zřizování iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) na webu Apple Developer Center. Přihlaste se k portálu pomocí Apple ID, přejděte na **certifikáty, identifikátory & profily**a pak vyberte **identifikátory**. Kliknutím **+** zaregistrujete novou aplikaci.

    ![Stránka ID aplikací na portálu zřizování iOS](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-ios-appids.png)

1. Na obrazovce **registrovat nový identifikátor** vyberte přepínač **ID aplikací** . Potom vyberte **Pokračovat**.

    ![Stránka pro zřizování iOS registrace – nové ID](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

1. Aktualizujte následující tři hodnoty pro novou aplikaci a pak vyberte **pokračovat**:

   * **Popis**: zadejte popisný název vaší aplikace.

   * **ID sady prostředků**: Zadejte ID sady prostředků **com. <organization_identifier>. <PRODUCT_NAME>** , jak je uvedeno v [Průvodci distribucí aplikací](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). Na následujícím snímku obrazovky se `mobcat` hodnota používá jako identifikátor organizace a hodnota **PushDemo** se používá jako název produktu.

      ![Stránka ID aplikace registrace portálu pro zřizování iOS](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Nabízená oznámení**: v části **Možnosti** si přečtěte možnost **nabízená oznámení** .

      ![Formulář pro registraci nového ID aplikace](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Tato akce vygeneruje vaše ID aplikace a žádosti o potvrzení informací. Vyberte **pokračovat**a pak vyberte **zaregistrovat** a potvrďte nové ID aplikace.

      ![Potvrdit nové ID aplikace](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      Po výběru položky **zaregistrovat**se na stránce **certifikáty, identifikátory & profily** zobrazí nové ID aplikace jako položka řádku.

1. Na stránce **certifikáty, identifikátory & profily** v části **identifikátory**Najděte položku řádku s ID aplikace, kterou jste vytvořili. Pak vyberte svůj řádek, aby se zobrazila obrazovka pro **úpravu konfigurace ID aplikace** .

#### <a name="creating-a-certificate-for-notification-hubs"></a>Vytváření certifikátu pro Notification Hubs

Certifikát je nutný k tomu, aby Centrum oznámení mohlo spolupracovat se **službou APNs (Apple Push Notification Services)** a dá se zadat jedním ze dvou způsobů:

1. [Vytvoření certifikátu P12 push Certificate, který se dá nahrát přímo do centra oznámení](#option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub) (*původní přístup*)

1. [Vytvoření certifikátu P8, který se dá použít pro ověřování založené na tokenech](#option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication) (*novější a doporučený přístup*)

Novější přístup má několik výhod, jak je popsáno v [ověřování na základě tokenu (http/2) pro služby APN](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification). Pro konkrétní scénáře se vyžaduje i méně kroků. Nicméně byly k dispozici kroky pro oba přístupy, protože obě budou fungovat pro účely tohoto kurzu.

##### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>MOŽNOST 1: vytvoření certifikátu P12 push Certificate, který se dá nahrát přímo do centra oznámení

1. V Macu spusťte nástroj Keychain Access. Dá se otevřít ze složky **nástrojů** nebo **jiné** složky na hlavní straně.

1. Vyberte možnost **přístup do řetězce klíčů**, rozbalte **Pomocníka s certifikátem**a pak vyberte **požádat o certifikát od certifikační autority**.

    ![Použití nástroje Keychain Access k vyžádání nového certifikátu](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > Ve výchozím nastavení přístup k řetězci klíčů vybere první položku v seznamu. To může být problém, pokud jste v kategorii **certifikáty** a pokud je **certifikační autorita pro vývojáře od společnosti Apple celosvětová** , nejedná se o první položku v seznamu. Ujistěte se, že máte neklíčovou položku, a před vygenerováním CSR (žádost o podepsání certifikátu) je vybraná možnost klíč **certifikační autority Apple celosvětově Developer Relations** .

1. Vyberte svou **e-mailovou adresu uživatele**, zadejte hodnotu pro **běžný název** , ujistěte se, že jste zadali možnost **uloženo na disk**a pak vyberte **pokračovat**. Ponechte **e-mailovou adresu CA** prázdnou, protože není potřeba.

    ![Očekávané informace o certifikátu](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. V části **Uložit jako**zadejte název **souboru žádosti o podepsání certifikátu (CSR)** , vyberte umístění v části **kde**a pak vyberte **Uložit**.

    ![Vyberte název souboru certifikátu.](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Tato akce uloží **soubor CSR** do vybraného umístění. Výchozí umístění je **Desktop**. Zapamatujte si umístění tohoto souboru.

1. Zpět na stránce **certifikáty, identifikátory & profily** na [portálu zřizování iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456)přejděte dolů na možnost zaškrtnutá **nabízená oznámení** a pak vyberte **Konfigurovat** a vytvořte certifikát.

    ![Úprava stránky ID aplikace](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-edit-appid.png)

1. Zobrazí se okno **certifikátů protokolu TLS/SSL služby Apple Push Notification Service** . V části **vývoj certifikátu TLS/SSL** vyberte tlačítko **vytvořit certifikát** .

    ![Tlačítko Vytvořit certifikát pro ID aplikace](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Zobrazí se obrazovka **vytvořit nový certifikát** .

    > [!NOTE]
    > Tento kurz používá vývojový certifikát. Stejný postup se používá při registraci produkčního certifikátu. Dejte pozor, abyste při odesílání oznámení používali stejný typ certifikátu.

1. Vyberte **zvolit soubor**, přejděte do umístění, kam jste uložili **soubor CSR**, a potom dvakrát klikněte na název certifikátu a načtěte ho. Potom vyberte **Pokračovat**.

1. Jakmile portál vytvoří certifikát, klikněte na tlačítko **Stáhnout** . Uložte certifikát a zapamatujte si umístění, do kterého se uložilo.

    ![Stránka pro stažení vygenerovaného certifikátu](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Certifikát se stáhne a uloží do vašeho počítače ve složce **stažené soubory** .

    ![Vyhledání souboru certifikátu ve složce stažených souborů](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Ve výchozím nastavení se stažený vývojový certifikát jmenuje **aps_development. cer**.

1. Poklikejte na stažený nabízený certifikát **aps_development.cer**. Tato akce nainstaluje nový certifikát do Klíčenky, jak je znázorněno na následujícím obrázku:

    ![Seznam certifikátů nástroje Keychain Access zobrazující nový certifikát](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > I když se název v certifikátu může lišit, název bude předponou **Apple Development iOS push Services** a má k němu přidružený odpovídající identifikátor sady prostředků.

1. V části přístup k řetězci klíčů **řízení**  +  **klikněte** na nový nabízený certifikát, který jste vytvořili v kategorii **certifikáty** . Vyberte **exportovat**, zadejte název souboru, vyberte formát **P12** a pak vyberte **Uložit**.

    ![Export certifikátu ve formátu p12](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Můžete si vybrat, že chcete certifikát chránit heslem, ale heslo je volitelné. Pokud chcete obejít vytváření hesel, klikněte na **OK** . Poznamenejte si název souboru a umístění exportovaného certifikátu P12. Používají se k povolení ověřování pomocí služby APNs.

    > [!NOTE]
    > Název a umístění souboru P12 se může lišit od toho, co je v tomto kurzu podrobnější.

##### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>MOŽNOST 2: vytvoření certifikátu P8, který se dá použít pro ověřování založené na tokenech

1. Poznamenejte si následující podrobnosti:

    * **Předpona ID aplikace** (**ID týmu**)
    * **ID sady prostředků**

1. Zpět v **certifikátech, identifikátory & profily**, klikněte na **klíče**.

   > [!NOTE]
   > Pokud už máte ke službě **APN**nakonfigurovaný klíč, můžete znovu použít certifikát P8, který jste stáhli hned po jeho vytvoření. Pokud ano, můžete ignorovat kroky **3** až **5**.

1. Kliknutím na **+** tlačítko (nebo na tlačítko **vytvořit klíč** ) vytvoříte nový klíč.
1. Zadejte vhodnou hodnotu **názvu klíče** , potom zaškrtněte možnost **Služba APNs (Apple Push Notification Service)** a potom klikněte na tlačítko **pokračovat**a potom na další obrazovku **Zaregistrujte** .
1. Klikněte na **Stáhnout** a pak přesuňte soubor **P8** (s předponou *AuthKey_*) do zabezpečeného místního adresáře a pak klikněte na **Hotovo**.

   > [!NOTE]
   > Ujistěte se, že je váš soubor P8 na bezpečném místě (a uložíte zálohu). Po stažení se klíč nedá znovu stáhnout, protože se odebere kopie serveru.
  
1. V části **klíče**klikněte na klíč, který jste vytvořili (nebo na který jste zvolili existující klíč, pokud jste se místo toho rozhodli použít).
1. Poznamenejte si hodnotu **ID klíče** .
1. Otevřete certifikát P8 ve vhodné aplikaci, například [**Visual Studio Code**](https://code.visualstudio.com). Poznamenejte si hodnotu klíče (mezi **-----zahájit privátní klíč-----** a **-----KONCOVÉho-----privátního klíče**).

    -----SPUSTIT PRIVÁTNÍ KLÍČ-----  
    <key_value>  
    -----KONCOVÝ PRIVÁTNÍ KLÍČ-----

    > [!NOTE]
    > Toto je **hodnota tokenu** , která bude později použita ke konfiguraci **centra oznámení**.

Na konci tohoto postupu byste měli mít následující informace pro pozdější použití v části [Konfigurace centra oznámení s informacemi o službě APN](#configure-your-notification-hub-with-apns-information):

* **ID týmu** (viz krok 1)
* **ID sady prostředků** (viz krok 1)
* **ID klíče** (viz krok 7)
* **Hodnota tokenu** (hodnota klíče P8 získaná v kroku 8)

### <a name="create-a-provisioning-profile-for-the-app"></a>Vytvoření zřizovacího profilu pro aplikaci

1. Vraťte se na [portál zřizování iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), vyberte **certifikáty, identifikátory & profily**, v nabídce vlevo vyberte **profily** a pak vyberte **+** vytvořit nový profil. Zobrazí se obrazovka **registrovat nový profil pro zřizování** .

1. Jako typ zřizovacího profilu vyberte **vývoj aplikací pro iOS** v části **vývoj** a pak vyberte **pokračovat**.

    ![Seznam zřizovacích profilů](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Potom v rozevíracím seznamu **ID aplikace** vyberte ID aplikace, které jste vytvořili, a vyberte **pokračovat**.

    ![Výběr ID aplikace](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. V okně **Vybrat certifikáty** vyberte vývojový certifikát, který používáte pro podepisování kódu, a vyberte **pokračovat**.

    > [!NOTE]
    > Tento certifikát není certifikát push, který jste vytvořili v [předchozím kroku](#creating-a-certificate-for-notification-hubs). Toto je váš vývojový certifikát. Pokud jeden neexistuje, musíte ho vytvořit, protože se jedná o [předpoklad](#prerequisites) pro tento kurz. Certifikáty pro vývojáře je možné vytvořit na [portálu pro vývojáře Apple](https://developer.apple.com)prostřednictvím [Xcode](https://developer.apple.com/library/archive/documentation/ToolsLanguages/Conceptual/YourFirstAppStoreSubmission/ProvisionYourDevicesforDevelopment/ProvisionYourDevicesforDevelopment.html) nebo v [aplikaci Visual Studio](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/).

1. Vraťte se na stránku **certifikáty, identifikátory & profily** , v nabídce vlevo vyberte **profily** a pak vyberte **+** Vytvoření nového profilu. Zobrazí se obrazovka **registrovat nový profil pro zřizování** .

1. V okně **Vybrat certifikáty** vyberte vývojový certifikát, který jste vytvořili. Potom vyberte **Pokračovat**.

1. Potom vyberte zařízení, která chcete použít pro testování, a vyberte **pokračovat**.

1. Nakonec zvolte název profilu v **názvu zřizovacího profilu**a vyberte **Generovat**.

    ![Volba názvu zřizovacího profilu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Po vytvoření nového zřizovacího profilu vyberte **Stáhnout**. Zapamatujte si umístění, do kterého se uložilo.

1. Přejděte do umístění profilu pro zřizování a dvakrát na něj klikněte a nainstalujte ho do vývojového počítače.

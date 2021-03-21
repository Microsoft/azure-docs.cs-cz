---
title: Posílání nabízených oznámení do systému iOS pomocí Azure Notification Hubs a sady iOS SDK
description: V tomto kurzu se naučíte používat Azure Notification Hubs a službu Apple Push Notification Service k odesílání nabízených oznámení do zařízení se systémem iOS.
author: sethmanheim
ms.author: sethm
ms.date: 10/30/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: c18814230eda6624e8ab1040797c66108f630e30
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100581734"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Kurz: odesílání nabízených oznámení do aplikací pro iOS pomocí Azure Notification Hubs

V tomto kurzu se dozvíte, jak nastavit Azure Notification Hubs a nakonfigurovat přihlašovací údaje pro nabízená oznámení na zařízení s iOS přes [službu APNs (Apple Push Notification Service)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1). 

Spuštění tohoto kurzu je předpokladem pro následné kurzy C a SWIFT iOS a zahrnuje následující kroky:

- Vygenerujte soubor žádosti o podepsání certifikátu.
- Vyžádejte si aplikaci pro nabízená oznámení.
- Vytvořte zřizovací profil pro aplikaci.
- Vytvoříte centrum oznámení.
- Nakonfigurujte centrum oznámení s informacemi o službě APN.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/free/).

Budete také potřebovat následující:

- Aktivní [vývojářský účet Apple](https://developer.apple.com/) .
- Počítač Mac se systémem [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)spolu s platným certifikátem pro vývojáře nainstalovaným do řetězce klíčů.
- IPhone nebo iPad se systémem iOS verze 10 nebo novější.
- Vaše fyzické zařízení je zaregistrované na [portálu Apple](https://developer.apple.com/) a přidruženo k vašemu certifikátu.

Pokud nejste obeznámeni se službou, přečtěte si téma [Přehled Azure Notification Hubs](notification-hubs-push-notification-overview.md) .

> [!NOTE]
> Centrum oznámení se nakonfiguruje jenom na použití režimu ověřování izolovaného prostoru (sandbox). Tento režim ověřování byste neměli používat pro produkční úlohy.

## <a name="generate-the-certificate-signing-request-file"></a>Generování souboru žádosti o podepsání certifikátu

Služba Apple Push Notification Service (APNS) používá k ověřování nabízených oznámení certifikáty. Pokud chcete vytvořit nabízený certifikát pro odesílání a přijímání oznámení, postupujte podle těchto pokynů. Další informace o těchto konceptech najdete v oficiální dokumentaci ke službě [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Vygenerujte soubor žádosti o podepsání certifikátu (CSR), který Apple používá k vygenerování podepsaného nabízeného certifikátu:

1. V Macu spusťte nástroj Keychain Access. Dá se otevřít ze složky **nástrojů** nebo **jiné** složky na hlavní straně.

2. Vyberte možnost **přístup do řetězce klíčů**, rozbalte **Pomocníka s certifikátem** a pak vyberte **požádat o certifikát od certifikační autority**.

   :::image type="content" source="media/ios-sdk-get-started/image1.png" alt-text="Snímek obrazovky, který zvýrazní žádost o certifikát z možnosti nabídky certifikační autorita.":::

   > [!NOTE]
   > Ve výchozím nastavení přístup k řetězci klíčů vybere první položku v seznamu. To může být problém, pokud jste v kategorii **certifikáty** a pokud je **certifikační autorita pro vývojáře od společnosti Apple celosvětová** , nejedná se o první položku v seznamu. Ujistěte se, že máte neklíčovou položku, a před vygenerováním CSR (žádost o podepsání certifikátu) je vybraná možnost klíč **certifikační autority Apple celosvětově Developer Relations** .

3. Vyberte svou **e-mailovou adresu uživatele**, zadejte hodnotu pro **běžný název** , ujistěte se, že jste zadali možnost **uloženo na disk** a pak vyberte **pokračovat**. Ponechte **e-mailovou adresu CA** prázdnou, protože není potřeba.

   :::image type="content" source="media/ios-sdk-get-started/image2.png" alt-text="Požadované informace o certifikátu":::

4. Do pole **Uložit jako** zadejte název souboru CSR, vyberte umístění, **kde** se nachází, a pak vyberte **Uložit**.

   :::image type="content" source="media/ios-sdk-get-started/image3.png" alt-text="Zvolit název souboru":::

   Tato akce uloží soubor CSR do vybraného umístění. Výchozí umístění je **Desktop**. Zapamatujte si umístění tohoto souboru.

Pak Zaregistrujte svoji aplikaci pomocí Apple, povolte nabízená oznámení a nahrajte exportovaný zástupce a vytvořte certifikát push.

## <a name="register-your-app-for-push-notifications"></a>Registrace aplikace pro nabízená oznámení

Pokud chcete odesílat nabízená oznámení do aplikace pro iOS, Zaregistrujte svoji aplikaci pomocí Apple a zaregistrujte se i pro nabízená oznámení.

1. Pokud jste svou aplikaci ještě nezaregistrovali, přejděte na [portál pro zřizování iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) na webu Apple Developer Center. Přihlaste se k portálu pomocí Apple ID a vyberte **identifikátory**. Pak vyberte **+** registraci nové aplikace.

   :::image type="content" source="media/ios-sdk-get-started/image4.png" alt-text="Stránka s ID aplikací":::

2. Na obrazovce **registrovat nový identifikátor** vyberte přepínač **ID aplikací** . Potom vyberte **Pokračovat**.

   :::image type="content" source="media/ios-sdk-get-started/image5.png" alt-text="Stránka zaregistrovat nové ID":::

3. Aktualizujte následující tři hodnoty pro novou aplikaci a pak vyberte **pokračovat**:

   - **Popis**: zadejte popisný název vaší aplikace.
   - **ID sady prostředků**: Zadejte ID sady prostředků **identifikátoru organizace. název produktu** , jak je uvedeno v [Průvodci distribucí aplikací](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). **Identifikátor organizace** a hodnoty **názvu produktu** se musí shodovat s identifikátorem organizace a názvem produktu, který použijete při vytváření projektu Xcode. Na následujícím snímku obrazovky se hodnota **NotificationHubs** používá jako identifikátor organizace a jako název produktu se používá hodnota **getstarted** . Ujistěte se, že hodnota **identifikátoru sady prostředků** odpovídá hodnotě v projektu Xcode, takže Xcode používá správný profil publikování.

      :::image type="content" source="media/ios-sdk-get-started/image6.png" alt-text="Registrovat ID aplikace":::

   - **Nabízená oznámení**: v části **Možnosti** si přečtěte možnost **nabízená oznámení** .

      :::image type="content" source="media/ios-sdk-get-started/image7.png" alt-text="Zaregistrovat nové ID aplikace":::

      Tato akce vygeneruje vaše ID aplikace a žádosti o potvrzení informací. Vyberte **pokračovat** a pak vyberte **zaregistrovat** a potvrďte nové ID aplikace.

      :::image type="content" source="media/ios-sdk-get-started/image8.png" alt-text="Potvrdit nové ID aplikace":::

      Po výběru položky **zaregistrovat** se na stránce **certifikáty, identifikátory & profily** zobrazí nové ID aplikace jako položka řádku.

4. Na stránce **certifikáty, identifikátory & profily** v části **identifikátory** Najděte položku řádku ID aplikace, kterou jste právě vytvořili, a výběrem jejího řádku zobrazte obrazovku pro **úpravu konfigurace ID aplikace** .

## <a name="create-a-certificate-for-notification-hubs"></a>Vytvoření certifikátu pro Notification Hubs

> [!NOTE]
> S vydáním iOS 13 můžete přijímat jenom tichá oznámení pomocí ověřování založeného na tokenech. Pokud pro přihlašovací údaje APNS používáte ověřování na základě certifikátů, musíte přepnout na použití ověřování založeného na tokenech.

K tomu, aby Centrum oznámení fungovalo s **APNs**, se vyžaduje certifikát. To lze provést jedním ze dvou způsobů:

- Vytvořte soubor **. p12** , který se dá nahrát přímo do Notification Hubs.

- Vytvořte soubor **. P8** , který se dá použít pro [ověřování založené na tokenech](notification-hubs-push-notification-http2-token-authentication.md) (novější přístup).

Druhá možnost obsahuje několik výhod v porovnání s používáním certifikátů, jak je popsáno v [ověřování na základě tokenu (http/2) pro služby APN](notification-hubs-push-notification-http2-token-authentication.md). Nicméně kroky jsou k dispozici pro oba přístupy.

### <a name="option-1-create-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hubs"></a>Možnost 1: vytvoření nabízeného certifikátu. p12, který se dá nahrát přímo do Notification Hubs

1. Přejděte dolů k možnosti zaškrtnutá **nabízená oznámení** a pak vyberte **Konfigurovat** a vytvořte certifikát.

   :::image type="content" source="media/ios-sdk-get-started/image9.png" alt-text="ID aplikace":::

2. Zobrazí se okno **certifikáty SSL služby Apple Push Notification Service** . V části **vývojový certifikát protokolu SSL** vyberte tlačítko **vytvořit certifikát** .

   :::image type="content" source="media/ios-sdk-get-started/image10.png" alt-text="Vytvořit certifikát":::

   Zobrazí se obrazovka **vytvořit nový certifikát** .

   > [!NOTE]
   > Tento kurz používá vývojový certifikát. Stejný postup se používá při registraci produkčního certifikátu. Při odesílání oznámení Dbejte na to, abyste používali stejný typ certifikátu.

3. Vyberte **zvolit soubor**, přejděte do umístění, kam jste ULOŽILI soubor CSR z prvního úkolu, a potom poklikejte na název certifikátu a načtěte ho. Potom vyberte **Pokračovat**.

4. Jakmile portál vytvoří certifikát, klikněte na tlačítko **Stáhnout** . Uložte certifikát a zapamatujte si umístění, do kterého se uložilo.

   :::image type="content" source="media/ios-sdk-get-started/image11.png" alt-text="Stáhnout certifikát":::

   Certifikát se stáhne a uloží ve složce **stažené soubory** .

   :::image type="content" source="media/ios-sdk-get-started/image12.png" alt-text="Najít soubor certifikátu":::

   Ve výchozím nastavení se stažený vývojový certifikát jmenuje **aps_development. cer**.

5. Dvakrát klikněte na stažený certifikát push Certificate **APS \_ Development. cer**. Tato akce nainstaluje nový certifikát do Klíčenky, jak je znázorněno na následujícím obrázku:

   :::image type="content" source="media/ios-sdk-get-started/image13.png" alt-text="Přístup k řetězci klíčů":::

   I když se název v certifikátu může lišit, název bude předponou **Apple Development iOS push Services**.

6. V nástroji Keychain Access, klikněte pravým tlačítkem na nový nabízený certifikát, který jste vytvořili v kategorii **Certifikáty**. Vyberte **exportovat**, zadejte název souboru, vyberte formát **. p12** a pak vyberte **Uložit**.

   :::image type="content" source="media/ios-sdk-get-started/image14.png" alt-text="Export certifikátu":::

   Můžete si vybrat, že chcete certifikát chránit heslem, ale to je volitelné. Pokud chcete obejít vytváření hesel, klikněte na **OK** . Poznamenejte si název souboru a umístění exportovaného certifikátu .p12. Používají se k povolení ověřování pomocí služby APNS.

   > [!NOTE]
   > Název souboru. p12 a jeho umístění se mohou lišit od obrázku v tomto kurzu.

### <a name="option-2-create-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>Možnost 2: vytvoření certifikátu. P8, který se dá použít pro ověřování založené na tokenech

1. Poznamenejte si následující podrobnosti:

   - **Předpona ID aplikace** (Toto je **ID týmu**)
   - **ID sady prostředků**

2. Zpět v **certifikátech, identifikátory & profily**, klikněte na **klíče**. Pokud už máte ke službě **APN** nakonfigurovaný klíč, můžete znovu použít certifikát. P8, který jste stáhli hned po jeho vytvoření. Pokud ano, můžete ignorovat kroky 3 až 5.

3. Kliknutím na **+** tlačítko (nebo na tlačítko **vytvořit klíč** ) vytvoříte nový klíč.

4. Zadejte vhodnou hodnotu **názvu klíče** , zaškrtněte možnost **Služba APNs (Apple Push Notification Service)** a potom klikněte na tlačítko **pokračovat** a potom na další obrazovku **Zaregistrujte** .

5. Klikněte na **Stáhnout** a pak přesuňte soubor **. P8** (s předponou `AuthKey_` ) do zabezpečeného místního adresáře a pak klikněte na **Hotovo**.

   > [!IMPORTANT]
   > Nezapomeňte soubor. P8 udržovat na bezpečném místě (a uložte zálohu). Po stažení se klíč nedá znovu stáhnout. zkopírování serveru se odebere.

6. V části **klíče** klikněte na klíč, který jste právě vytvořili (nebo vyberte existující klíč, pokud jste se místo toho rozhodli použít).

7. Poznamenejte si hodnotu **ID klíče** .

8. Otevřete svůj certifikát. P8 ve vhodné aplikaci, například [Visual Studio Code](https://code.visualstudio.com/), a poznamenejte si hodnotu klíče. Jedná se o hodnotu v rozsahu **-----zahájit-----privátního klíče** a **-----KONCOVým-----privátního klíče** .

   ```p8
   -----BEGIN PRIVATE KEY-----
   <key_value>
   -----END PRIVATE KEY-----
   ```

   Toto je hodnota tokenu, která bude později použita ke konfiguraci Notification Hubs.

Na konci těchto kroků byste měli mít následující informace pro pozdější použití v části [Konfigurace centra oznámení s informacemi o službě APN](#configure-the-notification-hub-with-apns-information):

- **ID týmu** (viz krok 1)
- **ID sady prostředků** (viz krok 1)
- **ID klíče** (viz krok 7)
- **Hodnota tokenu** (hodnota klíče. P8, viz krok 8)

## <a name="create-a-provisioning-profile"></a>Vytvoření zřizovacího profilu

1. Vraťte se na [portál zřizování iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), vyberte **certifikáty, identifikátory & profily**, v nabídce vlevo vyberte **profily** a pak vyberte **+** vytvořit nový profil. Zobrazí se obrazovka **registrovat nový profil pro zřizování** .

2. Jako typ zřizovacího profilu vyberte **vývoj aplikací pro iOS** v části **vývoj** a pak vyberte **pokračovat**.

   :::image type="content" source="media/ios-sdk-get-started/image15.png" alt-text="Seznam zřizovacích profilů":::

3. V dalším kroku vyberte ID aplikace, které jste vytvořili v rozevíracím seznamu **ID aplikace** , a pak vyberte **pokračovat**.

   :::image type="content" source="media/ios-sdk-get-started/image16.png" alt-text="Vybrat ID aplikace":::

4. V okně **Vybrat certifikáty** vyberte vývojový certifikát, který používáte pro podepisování kódu, a vyberte **pokračovat**. Tento certifikát není certifikátem push, který jste vytvořili. Pokud jeden neexistuje, musíte ho vytvořit. Pokud certifikát existuje, přejděte k dalšímu kroku. Pokud neexistuje certifikát pro vývoj, vytvořte ho:

   1. Pokud vidíte, že **nejsou k dispozici žádné certifikáty**, vyberte **vytvořit certifikát**.
   2. V části **software** vyberte možnost **vývoj pro Apple**. Potom vyberte **Pokračovat**.
   3. Na obrazovce **vytvořit nový certifikát** vyberte **zvolit soubor**.
   4. Přejděte na certifikát **žádosti o podepsání certifikátu** , který jste vytvořili dříve, vyberte ho a pak vyberte **otevřít**.
   5. Vyberte **Pokračovat**.
   6. Stáhněte si vývojový certifikát a zapamatujte si umístění, ve kterém je uložené.

5. Vraťte se na stránku **certifikáty, identifikátory & profily** , v nabídce vlevo vyberte **profily** a pak vyberte **+** Vytvoření nového profilu. Zobrazí se obrazovka **registrovat nový profil pro zřizování** .

6. V okně **Vybrat certifikáty** vyberte vývojový certifikát, který jste právě vytvořili. Potom vyberte **Pokračovat**.

7. Potom vyberte zařízení, která chcete použít pro testování, a vyberte **pokračovat**.

8. Nakonec zvolte název profilu v **názvu zřizovacího profilu** a pak vyberte **vygenerovat**.

   :::image type="content" source="media/ios-sdk-get-started/image17.png" alt-text="Zvolit název zřizovacího profilu":::

9. Po vytvoření nového zřizovacího profilu vyberte **Stáhnout**. Zapamatujte si umístění, ve kterém je uložené.

10. Přejděte do umístění profilu pro zřizování a dvakrát na něj klikněte a nainstalujte ho do vývojového počítače s Xcode.

## <a name="create-a-notification-hub"></a>Vytvoříte centrum oznámení.

V této části vytvoříte centrum oznámení a nakonfigurujete ověřování pomocí služby APNS pomocí certifikátu push push Certificate nebo ověřování založeného na tokenech. Pokud chcete použít Centrum oznámení, které jste už vytvořili, můžete přeskočit na krok 5.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

2. V nabídce vlevo vyberte **všechny služby** a v části **mobilní zařízení** vyberte **Notification Hubs** . Výběrem ikony hvězdičky vedle názvu služby přidáte službu do oddílu **Oblíbené položky** v levé nabídce. Po přidání **Notification Hubs** k **oblíbeným položkám** ji vyberte.

   :::image type="content" source="media/ios-sdk-get-started/image18.png" alt-text="Azure Portal":::

3. Na stránce **Notification Hubs** vyberte **Přidat** na panelu nástrojů.

   :::image type="content" source="media/ios-sdk-get-started/image19.png" alt-text="Tlačítko Přidat panel nástrojů":::

4. Na stránce **Notification Hubs** proveďte následující kroky:

   1. Zadejte název do **centra oznámení**.
   2. Zadejte název do v **vytvořit nový obor názvů**. Obor názvů obsahuje jedno nebo více Center oznámení.
   3. V rozevíracím seznamu **umístění** vyberte hodnotu. Tato hodnota určuje umístění, ve kterém chcete vytvořit centrum oznámení.
   4. Vyberte existující skupinu prostředků ve **skupině prostředků** nebo vytvořte novou skupinu prostředků.
   5. Vyberte **Vytvořit**.

   :::image type="content" source="media/ios-sdk-get-started/image20.png" alt-text="Nastavení vlastností":::

5. Vyberte **oznámení** (ikona zvonku) a pak vyberte **Přejít k prostředku**. Můžete také aktualizovat seznam na stránce **Notification Hubs** a vybrat své centrum.

   :::image type="content" source="media/ios-sdk-get-started/image21.png" alt-text="Oznámení na portálu":::

6. Vyberte ze seznamu **Zásady přístupu**. Všimněte si, že jsou k dispozici dva připojovací řetězce. Budete je potřebovat později pro zpracování nabízených oznámení.

   > [!IMPORTANT]
   > V aplikaci nepoužívejte zásady **DefaultFullSharedAccessSignature** . To je určeno jenom pro použití v back-endu.

   :::image type="content" source="media/ios-sdk-get-started/image22.png" alt-text="Připojovací řetězce":::

## <a name="configure-the-notification-hub-with-apns-information"></a>Konfigurace centra oznámení s informacemi o službě APN

V části **Notification Services** vyberte **Apple (APNs)** a pak postupujte podle příslušných kroků na základě postupu, který jste zvolili dříve v části [Vytvoření certifikátu pro Notification Hubs](#create-a-certificate-for-notification-hubs) .

> [!NOTE]
> Pokud chcete odesílat nabízená oznámení uživatelům, kteří si zakoupili vaši aplikaci ze Storu, použijte pro **režim aplikace** **produkční** prostředí.

### <a name="option-1-use-a-p12-push-certificate"></a>Možnost 1: použití nabízeného certifikátu. P12

1. Vyberte **Certifikát**.

2. Vyberte ikonu souboru.

3. Vyberte soubor. p12, který jste dříve exportovali, a pak vyberte **otevřít**.

4. V případě potřeby zadejte správné heslo.

5. Vyberte režim **Izolovaný prostor**.

   :::image type="content" source="media/ios-sdk-get-started/image23.png" alt-text="Konfigurace":::

6. Vyberte **Uložit**.

### <a name="option-2-use-token-based-authentication"></a>Možnost 2: použití ověřování založeného na tokenech

1. Vyberte **token**.

2. Zadejte následující hodnoty, které jste získali dříve:

   - **ID klíče**
   - **ID sady prostředků**
   - **ID týmu**
   - **Token**

3. Zvolit **izolovaný prostor**

4. Vyberte **Uložit**.

Nyní jste nakonfigurovali centrum oznámení pomocí služby APNS. Máte také připojovací řetězce potřebné k registraci aplikace a odesílání nabízených oznámení.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili a nakonfigurovali centrum oznámení v Azure a nakonfigurovali ho tak, aby umožňovalo posílání oznámení do vaší aplikace prostřednictvím služby Apple Push Notification Service (APNS). V dalším kroku vytvoříme ukázkovou aplikaci pro iOS a integrujete sadu SDK pro centra oznámení Azure, aby mohla přijímat nabízená oznámení odeslaná prostřednictvím Azure Portal. Přejděte k následujícímu kurzu v závislosti na zvoleném jazyce:

- [Kurz: odesílání nabízených oznámení do aplikací pro iOS pomocí Azure Notification Hubs](ios-sdk-current.md)

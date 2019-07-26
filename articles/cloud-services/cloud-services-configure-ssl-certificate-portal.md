---
title: Konfigurace SSL pro cloudovou službu | Microsoft Docs
description: Naučte se, jak zadat koncový bod HTTPS pro webovou roli a jak nahrát certifikát SSL pro zabezpečení aplikace. V těchto příkladech se používá Azure Portal.
services: cloud-services
documentationcenter: .net
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 05/26/2017
ms.author: gwallace
ms.openlocfilehash: 9e7b7526f13fa6b9ae648c4ddb4004a627d85154
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359732"
---
# <a name="configuring-ssl-for-an-application-in-azure"></a>Konfigurace SSL pro aplikaci v Azure

Šifrování SSL (Secure Socket Layer) představuje nejčastěji používanou metodu zabezpečení dat posílaných přes internet. Článek o tomto běžném postupu probírá, jak zadat koncový bod HTTPS pro webovou roli a jak nahrát certifikát SSL pro zabezpečení aplikace.

> [!NOTE]
> Postupy v této úloze se vztahují na Azure Cloud Services; App Services najdete v [tomto](../app-service/app-service-web-tutorial-custom-ssl.md)tématu.
>

Tato úloha používá nasazení v produkčním prostředí. Informace o použití pracovního nasazení najdete na konci tohoto tématu.

Pokud jste ještě nevytvořili cloudovou službu, přečtěte si [to](cloud-services-how-to-create-deploy-portal.md) jako první.

## <a name="step-1-get-an-ssl-certificate"></a>Krok 1: Získat certifikát SSL
Pokud chcete nakonfigurovat SSL pro aplikaci, musíte nejdřív získat certifikát SSL, který byl podepsaný certifikační autoritou (CA), důvěryhodnou třetí stranou, která pro tento účel vydává certifikáty. Pokud ho ještě nemáte, musíte ho získat od společnosti, která prodává certifikáty SSL.

Certifikát musí splňovat následující požadavky na certifikáty SSL v Azure:

* Certifikát musí obsahovat privátní klíč.
* Certifikát musí být vytvořen pro výměnu klíčů, který lze exportovat do souboru. pfx (Personal Information Exchange).
* Název subjektu certifikátu se musí shodovat s doménou používanou pro přístup ke cloudové službě. Certifikát SSL nemůžete od certifikační autority (CA) pro doménu cloudapp.net získat. Při přístupu ke službě musíte získat vlastní název domény. Když vyžádáte certifikát od certifikační autority, název subjektu certifikátu se musí shodovat s názvem vlastní domény použitým pro přístup k vaší aplikaci. Pokud je třeba název vlastní domény **contoso.com** , měli byste požádat o certifikát od vaší certifikační autority pro * **. contoso.com** nebo **na\.webové contoso.com**.
* Certifikát musí používat minimálně 2048 bitů šifrování.

Pro účely testování můžete [vytvořit](cloud-services-certs-create.md) a použít certifikát podepsaný svým držitelem. Certifikát podepsaný svým držitelem se neověřuje prostřednictvím certifikační autority a může jako adresu URL webu používat cloudapp.net doménu. Následující úloha například používá certifikát podepsaný svým držitelem, ve kterém je **sslexample.cloudapp.NET**běžný název (CN), který je používán v certifikátu.

Dále musíte do definice služby a konfiguračních souborů služby zahrnout informace o certifikátu.

<a name="modify"></a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Krok 2: Úprava definice služby a konfiguračních souborů
Vaše aplikace musí být nakonfigurovaná tak, aby používala certifikát, a musí být přidán koncový bod HTTPS. V důsledku toho je potřeba aktualizovat definice služby a konfigurační soubory služby.

1. Ve vašem vývojovém prostředí otevřete soubor definice služby (CSDEF), přidejte část **certifikáty** do části webrole  a vložte následující informace o certifikátu (a zprostředkující certifikáty):

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="My"
                        permissionLevel="limitedOrElevated" />
            <!-- IMPORTANT! Unless your certificate is either
            self-signed or signed directly by the CA root, you
            must include all the intermediate certificates
            here. You must list them here, even if they are
            not bound to any endpoints. Failing to list any of
            the intermediate certificates may cause hard-to-reproduce
            interoperability problems on some clients.-->
            <Certificate name="CAForSampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="CA"
                        permissionLevel="limitedOrElevated" />
        </Certificates>
    ...
    </WebRole>
    ```

   Část **certifikáty** definuje název certifikátu, jeho umístění a název úložiště, kde se nachází.

   Oprávnění (`permissionLevel` atribut) lze nastavit na jednu z následujících hodnot:

   | Hodnota oprávnění | Popis |
   | --- | --- |
   | limitedOrElevated |**(Výchozí)** K privátnímu klíči mají přístup všechny procesy rolí. |
   | úrovně |K privátnímu klíči mají přístup pouze procesy se zvýšenými oprávněními. |

2. V souboru definice služby přidejte do oddílu Endpoints element **InputEndpoint** ,  abyste mohli povolit protokol https:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Endpoints>
            <InputEndpoint name="HttpsIn" protocol="https" port="443"
                certificate="SampleCertificate" />
        </Endpoints>
    ...
    </WebRole>
    ```

3. V souboru definice služby přidejte prvek **vazby** v rámci oddílu **weby** . Tento prvek přidá vazbu HTTPS pro mapování koncového bodu na váš web:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="HttpsIn" endpointName="HttpsIn" />
                </Bindings>
            </Site>
        </Sites>
    ...
    </WebRole>
    ```

   Všechny požadované změny souboru definice služby byly dokončeny. je ale stále nutné přidat informace o certifikátu do konfiguračního souboru služby.
4. V konfiguračním souboru služby (CSCFG), ServiceConfiguration. Cloud. cscfg, přidejte hodnotu **certifikátů** pomocí certifikátu. Následující ukázka kódu poskytuje podrobné informace o části **certifikáty** s výjimkou hodnoty kryptografického otisku.

   ```xml
    <Role name="Deployment">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff"
                thumbprintAlgorithm="sha1" />
            <Certificate name="CAForSampleCertificate"
                thumbprint="79d4c38de50e6316ff9427befa18ec6865a9ebdc"
                thumbprintAlgorithm="sha1" />
        </Certificates>
    ...
    </Role>
    ```

(Tento příklad používá **SHA1** pro algoritmus kryptografického otisku. Zadejte vhodnou hodnotu pro algoritmus kryptografického otisku certifikátu.)

Teď, když je aktualizace definice služby a konfigurační soubory služby aktualizované, zabalite nasazení pro nahrávání do Azure. Pokud používáte **cspack**, nepoužívejte příznak **/generateConfigurationFile** , protože přepíše právě vložené informace o certifikátu.

## <a name="step-3-upload-a-certificate"></a>Krok 3: Nahrát certifikát
Připojit k Azure Portal a...

1. V části **všechny prostředky** portálu vyberte svou cloudovou službu.

    ![Publikování cloudové služby](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Klikněte na **certifikáty**.

    ![Klikněte na ikonu certifikáty.](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. V horní části oblasti certifikátů klikněte na **nahrát** .

    ![Klikněte na položku nabídky Odeslat.](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. Zadejte **soubor**, **heslo**a potom klikněte na tlačítko **nahrát** v dolní části oblasti pro zadávání dat.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Krok 4: Připojení k instanci role pomocí protokolu HTTPS
Teď, když je nasazení v Azure v provozu, můžete se k němu připojit pomocí protokolu HTTPS.

1. Kliknutím na **adresu URL webu** otevřete webový prohlížeč.

   ![Klikněte na adresu URL webu.](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. Ve webovém prohlížeči upravte odkaz tak, aby místo **http**používal protokol **https** , a pak navštivte stránku.

   > [!NOTE]
   > Pokud používáte certifikát podepsaný svým držitelem, můžete při přechodu na koncový bod HTTPS, který je přidružený k certifikátu podepsanému svým držitelem, zobrazit v prohlížeči chybu certifikátu. Tento problém se eliminuje pomocí certifikátu podepsaného důvěryhodnou certifikační autoritou. do té doby můžete chybu ignorovat. (Další možností je přidání certifikátu podepsaného svým držitelem do úložiště certifikátů důvěryhodné certifikační autority uživatele.)
   >
   >

   ![Náhled webu](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Pokud chcete použít protokol SSL pro pracovní nasazení místo nasazení v produkčním prostředí, musíte nejprve určit adresu URL používanou pro pracovní nasazení. Po nasazení cloudové služby se adresa URL přípravného prostředí určuje pomocí identifikátoru GUID **ID nasazení** v tomto formátu:`https://deployment-id.cloudapp.net/`  
   >
   > Vytvořte certifikát s běžným názvem (CN), který se rovná adrese URL založené na identifikátoru GUID (například **328187776e774ceda8fc57609d404462.cloudapp.NET**). Pomocí portálu přidejte certifikát do připravené cloudové služby. Pak přidejte informace o certifikátu do souborů CSDEF a CSCFG, znovu zabalením aplikace a aktualizujte připravené nasazení na použití nového balíčku.
   >

## <a name="next-steps"></a>Další postup
* [Obecná konfigurace cloudové služby](cloud-services-how-to-configure-portal.md)
* Přečtěte si, jak [nasadit cloudovou službu](cloud-services-how-to-create-deploy-portal.md).
* Nakonfigurujte [vlastní název domény](cloud-services-custom-domain-name-portal.md).
* [Spravujte svou cloudovou službu](cloud-services-how-to-manage-portal.md).

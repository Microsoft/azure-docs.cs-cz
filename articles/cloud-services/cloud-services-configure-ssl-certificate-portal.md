---
title: Konfigurace protokolu SSL pro cloudové služby | Microsoft Docs
description: Zjistěte, jak zadat koncový bod HTTPS pro webovou roli a jak nahrát certifikát SSL pro zabezpečení vaší aplikace. Tyto příklady použití portálu Azure.
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: ''
ms.assetid: 371ba204-48b6-41af-ab9f-ed1d64efe704
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: adegeo
ms.openlocfilehash: 0e053ad7f1033317948b6ef0856984b21e56e425
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2017
ms.locfileid: "24859772"
---
# <a name="configuring-ssl-for-an-application-in-azure"></a>Konfigurace protokolu SSL pro všechny aplikace v Azure

Šifrování SSL (Secure Socket Layer) je nejčastěji používanou metodou zabezpečení dat posílaných přes internet. Článek o tomto běžném postupu probírá, jak zadat koncový bod HTTPS pro webovou roli a jak nahrát certifikát SSL pro zabezpečení aplikace.

> [!NOTE]
> Postupy v této úloze platí pro Azure Cloud Services; Aplikační služby, najdete v části [to](../app-service/app-service-web-tutorial-custom-ssl.md).
>

Tato úloha používá produkčním nasazení. Informace o používání pracovní nasazení najdete na konci tohoto tématu.

Čtení [to](cloud-services-how-to-create-deploy-portal.md) první, pokud jste ještě nevytvořili cloudové služby.

## <a name="step-1-get-an-ssl-certificate"></a>Krok 1: Získání certifikátu protokolu SSL
Konfigurace protokolu SSL pro aplikaci, musíte nejdřív získat certifikát SSL, která byla podepsána pomocí certifikační autoritou (CA), důvěryhodná třetí straně, která vystavuje certifikáty pro tento účel. Pokud není již nemáte, budete muset získat jeden ze společnosti, která prodává certifikáty SSL.

Certifikát musí splňovat následující požadavky na certifikáty SSL v Azure:

* Certifikát musí obsahovat privátní klíč.
* Certifikát se musí vytvořit pro výměnu klíčů, exportovat do souboru Personal Information Exchange (.pfx).
* Název subjektu certifikátu musí odpovídat domény používá pro přístup ke cloudové službě. Nelze získat certifikát SSL od certifikační autority (CA) pro doménu cloudapp.net. Musíte získat vlastní název domény má použít při přístupu ke službě. Pokud budete požadovat certifikát od certifikační Autority, název subjektu certifikátu musí odpovídat názvu vlastní domény, používá pro přístup k aplikaci. Například, pokud je váš vlastní název domény **contoso.com** by vyžádání certifikátu z certifikační Autority pro ***. contoso.com** nebo **www.contoso.com**.
* Certifikát musí používat minimálně 2048bitové šifrování.

Pro účely testování můžete [vytvořit](cloud-services-certs-create.md) a použít certifikát podepsaný svým držitelem. Certifikát podepsaný svým držitelem není ověřen pomocí certifikační Autority a cloudapp.net domény můžete použít jako adresu URL webu. Například následující úkol používá certifikát podepsaný svým držitelem, ve kterém je běžný název (CN) použitý v certifikátu **sslexample.cloudapp.net**.

V dalším kroku musí zahrnovat informace o certifikátu v definici služby a služby konfigurační soubory.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Krok 2: Upravte soubory definice a konfigurace služby
Vaše aplikace musí být nakonfigurována pro použití certifikátu a koncový bod HTTPS musí být přidán. Definice služby a služby konfigurační soubory v důsledku toho musí aktualizovat.

1. Ve vašem vývojovém prostředí, otevřete soubor definice služby (CSDEF), přidejte **certifikáty** části v rámci **WebRole** části a uveďte tyto informace o certifikátu (a zprostředkující certifikáty):

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

   **Certifikáty** oddíl definuje název naše certifikát, její umístění a název úložiště, kde se nachází.

   Oprávnění (`permisionLevel` atribut) můžete nastavit na jedno z následujících hodnot:

   | Hodnota oprávnění | Popis |
   | --- | --- |
   | limitedOrElevated |**(Výchozí)**  Všechny procesy role můžete přístup k privátnímu klíči. |
   | zvýšené |Jenom procesy se zvýšenými oprávněními, můžete přístup k privátnímu klíči. |

2. V souboru definice služby, přidejte **InputEndpoint** v rámci **koncové body** části Povolit protokol HTTPS:

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

3. V souboru definice služby, přidejte **vazby** v prvku **lokality** části. Tento element přidá vazbu HTTPS k mapování koncový bod na váš web:

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

   Byly dokončeny všechny požadované změny pro soubor definice služby; ale potřebujete přidat informace o certifikátu do konfiguračního souboru služby.
4. V konfiguračním souboru služby (CSCFG), ServiceConfiguration.Cloud.cscfg, přidejte **certifikáty** hodnotu s u vašeho certifikátu. Následující ukázka kódu obsahuje podrobnosti o **certifikáty** části, s výjimkou hodnotu kryptografického otisku.

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

(Tento příklad používá **sha1** pro algoritmus kryptografický otisk. Zadejte hodnotu vhodnou pro váš certifikát kryptografický algoritmus.)

Teď, když byly aktualizovány definice služby a služby konfigurační soubory, balíček nasazení nahrát do Azure. Pokud používáte **cspack**, nepoužívejte **/generateConfigurationFile** příznak, jako který přepíše vloženého informace o certifikátu.

## <a name="step-3-upload-a-certificate"></a>Krok 3: Nahrát na server certifikát
Připojení k portálu Azure a...

1. V **všechny prostředky** části portálu, vyberte cloudové služby.

    ![Publikování cloudové služby](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Klikněte na tlačítko **certifikáty**.

    ![Klikněte na ikonu certifikáty](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Klikněte na tlačítko **nahrát** v horní části oblasti certifikáty.

    ![Klikněte na položku nabídky nahrávání](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. Zadejte **soubor**, **heslo**, pak klikněte na tlačítko **nahrát** v dolní části oblasti vstupní data.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Krok 4: Připojení k instanci role pomocí protokolu HTTPS
Teď, když vaše nasazení je spuštěná v Azure, můžete připojit se pomocí protokolu HTTPS.

1. Klikněte **adresa URL webu** otevřete webový prohlížeč.

   ![Klikněte na adresu URL webu](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. Ve webovém prohlížeči, upravte odkaz použít **https** místo **http**a potom navštivte stránku.

   > [!NOTE]
   > Pokud používáte certifikát podepsaný svým držitelem, když přejdete na koncový bod HTTPS, který je spojen s certifikát podepsaný svým držitelem může se zobrazit chyba certifikátu v prohlížeči. Používá certifikát podepsaný důvěryhodnou certifikační autoritou eliminuje tento problém; do té doby můžete chybu ignorovat. (Další možností je přidání certifikátu podepsaného svým držitelem do úložiště certifikátů důvěryhodné certifikační autority uživatele.)
   >
   >

   ![Verze preview webu](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Pokud chcete používat protokol SSL pro pracovní nasazení místo produkční nasazení, musíte nejdřív určit adresu URL pro pracovní nasazení použít. Jakmile Cloudová služba byla nasazena, je dáno adresu URL na pracovní prostředí **ID nasazení** identifikátor GUID v tomto formátu:`https://deployment-id.cloudapp.net/`  
   >
   > Vytvoření certifikátu pomocí běžný název (CN) rovnou na adresu URL, na základě GUID (například **328187776e774ceda8fc57609d404462.cloudapp.net**). Použití portálu k přidání certifikátu do dvoufázové instalace cloudové služby. Potom CSDEF a CSCFG souborům přidat informace o certifikátu, znovu zabalte aplikaci a aktualizaci dvoufázové nasazení tak, aby pomocí nového balíčku.
   >

## <a name="next-steps"></a>Další kroky
* [Obecná konfigurace cloudové služby](cloud-services-how-to-configure-portal.md).
* Zjistěte, jak [nasazení cloudové služby](cloud-services-how-to-create-deploy-portal.md).
* Konfigurace [vlastní název domény](cloud-services-custom-domain-name-portal.md).
* [Správa služby cloud](cloud-services-how-to-manage-portal.md).

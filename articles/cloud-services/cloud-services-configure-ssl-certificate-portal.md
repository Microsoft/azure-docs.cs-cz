---
title: Konfigurace SSL pro cloudové služby | Dokumentace Microsoftu
description: Zjistěte, jak zadat koncový bod HTTPS pro webovou roli a jak nahrát certifikát SSL pro zabezpečení aplikace. Tyto příklady pomocí webu Azure portal.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 371ba204-48b6-41af-ab9f-ed1d64efe704
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: jeconnoc
ms.openlocfilehash: cf2fe10d6a0ab81ff71c948ee2defe6bc7edfd70
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300184"
---
# <a name="configuring-ssl-for-an-application-in-azure"></a>Konfigurace protokolu SSL pro aplikaci v Azure

Šifrování SSL (Secure Socket Layer) představuje nejčastěji používanou metodu zabezpečení dat posílaných přes internet. Článek o tomto běžném postupu probírá, jak zadat koncový bod HTTPS pro webovou roli a jak nahrát certifikát SSL pro zabezpečení aplikace.

> [!NOTE]
> Postupy v této úloze lze použít pro Azure Cloud Services; Aplikační služby, najdete v části [to](../app-service/app-service-web-tutorial-custom-ssl.md).
>

Tato úloha používá produkčního nasazení. Informace o používání pracovní nasazení najdete na konci tohoto tématu.

Čtení [to](cloud-services-how-to-create-deploy-portal.md) první, pokud jste ještě nevytvořili cloudovou službu.

## <a name="step-1-get-an-ssl-certificate"></a>Krok 1: Získání certifikátu SSL
Konfigurace SSL pro aplikaci, musíte nejprve získat certifikát SSL, který byl podepsán pomocí certifikátu autority (CA), důvěryhodné jiného výrobce, který vystavuje certifikáty pro tento účel. Pokud není ještě nemáte, budete muset získat jeden ze společnosti, která se prodává certifikáty SSL.

Certifikát musí splňovat následující požadavky na certifikáty protokolu SSL v Azure:

* Certifikát musí obsahovat privátní klíč.
* Certifikát musí být vytvořen pro výměnu klíčů, musí umožňovat export do souboru Personal Information Exchange (.pfx).
* Název subjektu certifikátu musí odpovídat doménu, kterou používá pro přístup ke cloudové službě. Nelze získat certifikát SSL od certifikační autority (CA) pro doménu cloudapp.net. Musíte získat název vlastní domény při přístupu ke službě. Když požádáte o certifikát od certifikační Autority, název subjektu certifikátu musí odpovídat názvu vlastní domény pro přístup k aplikaci. Například, pokud je název vaší vlastní domény **contoso.com** by požádáte o certifikát z certifikační Autority pro ***. contoso.com** nebo **www.contoso.com**.
* Certifikát musí používat minimálně 2048bitové šifrování.

Pro účely testování můžete [vytvořit](cloud-services-certs-create.md) a použít certifikát podepsaný svým držitelem. Certifikát podepsaný svým držitelem není ověřen pomocí certifikační Autority a cloudapp.net domény můžete použít jako adresu URL webu. Například následující úkol používá certifikát podepsaný svým držitelem, ve kterém je běžný název (CN) použitý v certifikátu **sslexample.cloudapp.net**.

V dalším kroku musí obsahovat informace o certifikátu v definici služby a služby konfigurační soubory.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Krok 2: Upravte soubory definice a konfigurace služby
Vaše aplikace musí být nakonfigurována pro použití certifikátu a musí být přidán koncový bod HTTPS. V důsledku toho definici služby a konfigurační soubory služby potřeba aktualizovat.

1. Ve vašem vývojovém prostředí otevřete definiční soubor služby (CSDEF), přidejte **certifikáty** části v rámci **WebRole** části a uveďte následující informace o certifikátu (a zprostředkující certifikáty):

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

   **Certifikáty** oddíl definuje název naší certifikát, jeho umístění a název úložiště, kde se nachází.

   Oprávnění (`permissionLevel` atribut) můžete nastavit na jedno z následujících hodnot:

   | Hodnota oprávnění | Popis |
   | --- | --- |
   | limitedOrElevated |**(Výchozí)**  Všechny procesy rolí může přistupovat k privátnímu klíči. |
   | se zvýšenými oprávněními |Pouze procesy se zvýšenými oprávněními můžou přistupovat k privátnímu klíči. |

2. V souboru definice služby, přidejte **InputEndpoint** element v rámci **koncové body** části tím povolíte protokol HTTPS:

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

3. V souboru definice služby, přidejte **vazby** element v rámci **lokality** oddílu. Tento element přidá vazbu HTTPS mapování koncový bod na váš web:

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

   Všechny požadované změny pro definiční soubor služby bylo dokončeno; ale stále je třeba přidat informace o certifikátu do konfiguračního souboru služby.
4. V konfiguračním souboru služby (CSCFG) ServiceConfiguration.Cloud.cscfg, přidejte **certifikáty** hodnotu, která vašeho certifikátu. Následující ukázka kódu obsahuje podrobnosti o **certifikáty** části, s výjimkou hodnoty kryptografického otisku.

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

(Tento příklad používá **sha1** pro algoritmus kryptografického otisku. Zadejte odpovídající hodnotu pro algoritmus kryptografického otisku vašeho certifikátu.)

Teď, když definice služby a služby konfigurační soubory se aktualizovaly, balíček nasazení pro jeho odeslání do Azure. Pokud používáte **cspack**, nepoužívejte **/generateConfigurationFile** označit jako, který přepíše informace o certifikátu, který je vložený.

## <a name="step-3-upload-a-certificate"></a>Krok 3: Nahrát certifikát
Připojte se k webu Azure portal a...

1. V **všechny prostředky** části portálu vyberte cloudovou službu.

    ![Publikovat cloudovou službu](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Klikněte na tlačítko **certifikáty**.

    ![Klikněte na ikonu certifikáty](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Klikněte na tlačítko **nahrát** v horní části oblasti certifikáty.

    ![Kliknutím na položku nabídky nahrávání](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. Zadejte **souboru**, **heslo**, pak klikněte na tlačítko **nahrát** v dolní části oblasti vstupní data.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Krok 4: Připojení k instanci role pomocí protokolu HTTPS
Teď, když vaše nasazení do provozu v Azure, můžete připojit pomocí protokolu HTTPS.

1. Klikněte na tlačítko **adresa URL webu** otevřete webový prohlížeč.

   ![Klikněte na adresu URL webu](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. Ve webovém prohlížeči, změnit odkaz použít **https** místo **http**a pak na stránce.

   > [!NOTE]
   > Pokud používáte certifikát podepsaný svým držitelem, když přejdete na koncový bod HTTPS, který je spojen s certifikát podepsaný svým držitelem se může zobrazit chyba certifikátu v prohlížeči. Tento problém, používá certifikát podepsaný důvěryhodnou certifikační autoritou odstraňuje. do té doby můžete ignorovat chybu. (Další možností je přidat certifikát podepsaný svým držitelem do úložiště certifikátů důvěryhodné certifikační autority uživatele).
   >
   >

   ![Lokality ve verzi preview](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Pokud chcete používat protokol SSL pro pracovní nasazení místo produkčního nasazení, musíte nejdřív určit adresu URL pro pracovní nasazení použije. Po nasazení cloudové služby, adresu URL do přípravného prostředí je určeno **ID nasazení** GUID v tomto formátu: `https://deployment-id.cloudapp.net/`  
   >
   > Vytvořit certifikát pomocí běžného názvu (CN) roven adresu URL na základě identifikátoru GUID (například **328187776e774ceda8fc57609d404462.cloudapp.net**). Použití portálu k přidání certifikátu do dvoufázové instalace cloudové služby. Zadejte informace o certifikátu CSDEF a CSCFG soubory, znovu zabalit aplikaci a aktualizaci vašeho nasazení připravené k použití nového balíčku.
   >

## <a name="next-steps"></a>Další postup
* [Obecná konfigurace cloudové služby](cloud-services-how-to-configure-portal.md).
* Zjistěte, jak [nasadit cloudovou službu](cloud-services-how-to-create-deploy-portal.md).
* Konfigurace [vlastního názvu domény](cloud-services-custom-domain-name-portal.md).
* [Správa cloudové služby](cloud-services-how-to-manage-portal.md).

---
title: Konfigurace protokolu TLS pro cloudovou službu | Dokumenty společnosti Microsoft
description: Přečtěte si, jak zadat koncový bod HTTPS pro webovou roli a jak nahrát certifikát TLS/SSL k zabezpečení vaší aplikace. Tyto příklady používají portál Azure.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/26/2017
ms.author: tagore
ms.openlocfilehash: c69b74cf91d8e097f8ad8a9ba2a16f3375f483ae
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024842"
---
# <a name="configuring-tls-for-an-application-in-azure"></a>Konfigurace TLS pro aplikaci v Azure

Zabezpečení transportní vrstvy (TLS), dříve známé jako šifrování SSL (Secure Socket L), je nejčastěji používanou metodou zabezpečení dat odeslaných přes Internet. Tato společná úloha popisuje, jak určit koncový bod HTTPS pro webovou roli a jak nahrát certifikát TLS/SSL k zabezpečení aplikace.

> [!NOTE]
> Postupy v této úloze platí pro Cloudové služby Azure; v tématu App Services [najdete v tomto](../app-service/configure-ssl-bindings.md).
>

Tento úkol používá produkční nasazení. Informace o použití pracovní nasazení je k dispozici na konci tohoto tématu.

Přečtěte si [to](cloud-services-how-to-create-deploy-portal.md) nejprve, pokud jste ještě nevytvořili cloudovou službu.

## <a name="step-1-get-a-tlsssl-certificate"></a>Krok 1: Získání certifikátu TLS/SSL
Chcete-li nakonfigurovat protokol TLS pro aplikaci, musíte nejprve získat certifikát TLS/SSL, který byl podepsán certifikační autoritou (CA), důvěryhodnou třetí stranou, která vydává certifikáty pro tento účel. Pokud ještě nemáte, musíte získat od společnosti, která prodává certifikáty TLS/SSL.

Certifikát musí splňovat následující požadavky na certifikáty TLS/SSL v Azure:

* Certifikát musí obsahovat veřejný klíč.
* Certifikát musí být vytvořen pro výměnu klíčů, exportovatelný do souboru Výměny osobních informací (.pfx).
* Název předmětu certifikátu se musí shodovat s doménou používanou pro přístup ke cloudové službě. Certifikát TLS/SSL nelze získat od certifikační autority pro cloudapp.net doménu. Musíte získat vlastní název domény, který chcete použít při přístupu ke službě. Pokud požadujete certifikát od certifikační autority, musí se název subjektu certifikátu shodovat s vlastním názvem domény používaným pro přístup k vaší aplikaci. Pokud je například název vaší vlastní domény **contoso.com,** požádáte certifikační autoritu o certifikát pro ***.contoso.com** nebo **\.www contoso.com**.
* Certifikát musí používat minimálně 2048bitové šifrování.

Pro účely testování můžete [vytvořit](cloud-services-certs-create.md) a použít certifikát podepsaný svým držitelem. Certifikát podepsaný svým držitelem není ověřen prostřednictvím certifikační autority a může používat doménu cloudapp.net jako adresu URL webu. Následující úloha například používá certifikát podepsaný svým držitelem, ve kterém je běžný název (CN) použitý v certifikátu **sslexample.cloudapp.net**.

Dále je nutné zahrnout informace o certifikátu do definice služby a konfiguračních souborů služby.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Krok 2: Změna definice služby a konfiguračních souborů
Aplikace musí být nakonfigurována tak, aby používala certifikát, a musí být přidán koncový bod HTTPS. V důsledku toho je třeba aktualizovat definici služby a konfigurační soubory služby.

1. Ve vývojovém prostředí otevřete soubor definice služby (CSDEF), přidejte oddíl **Certifikáty** v části **WebRole** a uveďte následující informace o certifikátu (a zprostředkujících certifikátech):

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

   Část **Certifikáty** definuje název našeho certifikátu, jeho umístění a název úložiště, kde se nachází.

   Oprávnění`permissionLevel` ( atribut) lze nastavit na jednu z následujících hodnot:

   | Hodnota oprávnění | Popis |
   | --- | --- |
   | omezenáNeboElevated |**(Výchozí)** Všechny procesy rolí mají přístup k soukromému klíči. |
   | Zvýšené |K soukromému klíči mají přístup pouze procesy se zvýšenými oprávněními. |

2. Do souboru definice služby přidejte element **InputEndpoint** do části **Koncové body,** abyste povolili protokol HTTPS:

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

3. Do souboru definice služby přidejte element **vazby** v části **Weby.** Tento prvek přidá vazbu HTTPS pro mapování koncového bodu na váš web:

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

   Všechny požadované změny v souboru definice služby byly dokončeny; ale stále je třeba přidat informace o certifikátu do konfiguračního souboru služby.
4. V konfiguračním souboru služby (CSCFG), ServiceConfiguration.Cloud.cscfg, přidejte hodnotu **certifikátů** s hodnotou certifikátu vašeho certifikátu. Následující ukázka kódu obsahuje podrobnosti o části **Certifikáty,** s výjimkou hodnoty kryptografického otisku.

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

(Tento příklad používá **sha1** pro algoritmus kryptografického potisku. Zadejte příslušnou hodnotu pro algoritmus kryptografického otisku certifikátu.)

Teď, když byly aktualizovány definice služby a konfigurační soubory služby, zabalte vaše nasazení pro nahrávání do Azure. Pokud používáte **cspack**, nepoužívejte příznak **/generateConfigurationFile,** protože to přepíše informace o certifikátu, které jste právě vložili.

## <a name="step-3-upload-a-certificate"></a>Krok 3: Nahrání certifikátu
Připojte se k portálu Azure a...

1. V části **Všechny prostředky** na portálu vyberte cloudovou službu.

    ![Publikování cloudové služby](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Klepněte na **položku Certifikáty**.

    ![Klikněte na ikonu certifikátů.](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. V horní části oblasti certifikátů klikněte na **Nahrát.**

    ![Klikněte na položku nabídky Nahrát.](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. Zadejte **soubor**, **heslo**a klikněte na **Tlačítko Odeslat** v dolní části oblasti pro zadávání dat.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Krok 4: Připojení k instanci role pomocí protokolu HTTPS
Teď, když je vaše nasazení v Azure v provozu, můžete se k němu připojit pomocí protokolu HTTPS.

1. Kliknutím na **adresu URL webu** otevřete webový prohlížeč.

   ![Klikněte na adresu URL webu.](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. Ve webovém prohlížeči upravte odkaz tak, aby používal **protokol https** místo **http**, a pak navštivte stránku.

   > [!NOTE]
   > Pokud používáte certifikát podepsaný svým držitelem, při procházení koncového bodu HTTPS, který je přidružen k certifikátu podepsanému svým držitelem, se může v prohlížeči zobrazit chyba certifikátu. Použití certifikátu podepsaného důvěryhodným certifikačním úřadem tento problém eliminuje. do té doby můžete chybu ignorovat. (Další možností je přidání certifikátu podepsaného svým držitelem do úložiště certifikátů důvěryhodné certifikační autority uživatele.)
   >
   >

   ![Náhled webu](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Pokud chcete použít TLS pro pracovní nasazení namísto produkčního nasazení, budete muset nejprve určit adresu URL použitou pro pracovní nasazení. Po nasazení cloudové služby je adresa URL pracovního prostředí určena identifikátorem GUID **ID nasazení** v tomto formátu:`https://deployment-id.cloudapp.net/`  
   >
   > Vytvořte certifikát s běžným názvem (CN) rovným adrese URL založené na identifikátoru GUID (například **328187776e774ceda8fc57609d404462.cloudapp.net**). Pomocí portálu přidejte certifikát do fázované cloudové služby. Potom přidejte informace o certifikátu do souborů CSDEF a CSCFG, přebalte aplikaci a aktualizujte fázované nasazení tak, aby používalo nový balíček.
   >

## <a name="next-steps"></a>Další kroky
* [Obecná konfigurace cloudové služby](cloud-services-how-to-configure-portal.md).
* Přečtěte si, jak [nasadit cloudovou službu](cloud-services-how-to-create-deploy-portal.md).
* Konfigurace [vlastního názvu domény](cloud-services-custom-domain-name-portal.md).
* [Spravujte cloudovou službu](cloud-services-how-to-manage-portal.md).




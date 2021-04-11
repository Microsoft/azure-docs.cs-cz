---
title: Exportovat řetěz certifikátů certifikační autority důvěryhodných klientů pro ověřování klientů
titleSuffix: Azure Application Gateway
description: Naučte se exportovat řetěz certifikátů důvěryhodné klientské certifikační autority pro ověřování klientů v Azure Application Gateway
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/31/2021
ms.author: caya
ms.openlocfilehash: 2329dc7426b223ef2c81dd0e2e607bccf73192e6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231405"
---
# <a name="export-a-trusted-client-ca-certificate-chain-to-use-with-client-authentication"></a>Exportovat řetěz certifikátů certifikační autority důvěryhodných klientů pro použití s ověřováním klientů
Aby bylo možné nakonfigurovat vzájemné ověřování u klienta nebo ověřování klientů, Application Gateway vyžaduje odeslání řetězce certifikátu důvěryhodné certifikační autority klienta do brány. Pokud máte více řetězů certifikátů, bude nutné vytvořit řetězce samostatně a odeslat je jako jiné soubory na Application Gateway. V tomto článku se dozvíte, jak exportovat řetěz certifikátů důvěryhodné certifikační autority klienta, který můžete použít v konfiguraci ověřování klientů v bráně.  

## <a name="prerequisites"></a>Požadavky

K vygenerování řetězu certifikátů důvěryhodné klientské certifikační autority je nutný existující klientský certifikát. 

## <a name="export-trusted-client-ca-certificate"></a>Exportovat certifikát důvěryhodné certifikační autority klienta

Certifikát důvěryhodné certifikační autority klienta je nutný k povolení ověřování klienta na Application Gateway. V tomto příkladu použijeme certifikát TLS/SSL pro klientský certifikát, vyexportujte jeho veřejný klíč a potom vyexportujte certifikáty certifikační autority z veřejného klíče, abyste získali certifikáty důvěryhodné klientské certifikační autority. Pak budeme zřetězit všechny certifikáty klientské certifikační autority do jednoho řetězu certifikátů certifikační autority důvěryhodného klienta. 

Následující kroky vám pomůžou exportovat soubor. pem nebo. cer pro váš certifikát:

### <a name="export-public-certificate"></a>Exportovat veřejný certifikát 

1. Chcete-li získat soubor .cer z certifikátu, otevřete **správu uživatelských certifikátů**. Vyhledejte certifikát, obvykle v ' Certificates-Current User\Personal\Certificates ' a klikněte pravým tlačítkem myši. Klikněte na **Všechny úlohy** a potom klikněte na **Exportovat**. Otevře se **Průvodce exportem certifikátu**. Pokud certifikát nemůžete najít v rámci aktuálního User\Personal\Certificates, možná jste omylem otevřeli "certifikáty-místní počítač", nikoli "Certifikáty – aktuální uživatel"). Pokud chcete otevřít Správce certifikátů v oboru aktuální uživatel pomocí PowerShellu, zadáte v okně konzoly *certmgr* .

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s vybranými certifikáty zobrazí správce certifikátů a kontextová nabídka se všemi úkoly a pak se vybere vybraná možnost exportovat.](./media/certificates-for-backend-authentication/export.png)

2. V průvodci klikněte na **Další**.
    > [!div class="mx-imgBorder"]
    > ![Export certifikátu](./media/certificates-for-backend-authentication/exportwizard.png)

3. Vyberte **Ne, neexportovat privátní klíč** a klikněte na **Další**.
    > [!div class="mx-imgBorder"]
    > ![Neexportovat privátní klíč](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Na stránce **Formát souboru pro export** vyberte **X.509, kódování Base-64 (CER)** a klikněte na **Další**.
    > [!div class="mx-imgBorder"]
    > ![Kódování Base-64](./media/certificates-for-backend-authentication/base64.png)

5. Pro **Export souboru** **přejděte** do umístění, do kterého chcete certifikát exportovat. V části **Název souboru** zadejte název souboru. Pak klikněte na tlačítko **Další**.

    > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky se zobrazí Průvodce exportem certifikátu, kde zadáte soubor, který se má exportovat.](./media/certificates-for-backend-authentication/browse.png)

6. Certifikát vyexportujte kliknutím na **Dokončit**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky po dokončení exportu souboru zobrazí Průvodce exportem certifikátu.](./media/certificates-for-backend-authentication/finish.png)

7. Váš certifikát byl úspěšně exportován.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky se zobrazí Průvodce exportem certifikátu se zprávou o úspěchu.](./media/certificates-for-backend-authentication/success.png)

   Exportovaný certifikát vypadá nějak takto:

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky zobrazuje symbol certifikátu.](./media/certificates-for-backend-authentication/exported.png)

### <a name="export-ca-certificates-from-the-public-certificate"></a>Export certifikátů certifikační autority z veřejného certifikátu

Teď, když jste exportovali veřejný certifikát, budete teď exportovat certifikáty certifikační autority z veřejného certifikátu. Pokud máte jenom kořenovou certifikační autoritu, musíte tento certifikát exportovat jenom vy. Pokud ale máte 1 a zprostředkující certifikační autority, budete muset také exportovat každou z nich. 

1. Po exportu veřejného klíče soubor otevřete.

    > [!div class="mx-imgBorder"]
    > ![Otevřít autorizační certifikát](./media/certificates-for-backend-authentication/openAuthcert.png)

    > [!div class="mx-imgBorder"]
    > ![o certifikátu](./media/mutual-authentication-certificate-management/general.png)

1. Vyberte kartu cesta k certifikátu pro zobrazení certifikační autority.

    > [!div class="mx-imgBorder"]
    > ![Podrobnosti o certifikátu](./media/mutual-authentication-certificate-management/cert-details.png) 

1. Vyberte kořenový certifikát a klikněte na **Zobrazit certifikát**.

    > [!div class="mx-imgBorder"]
    > ![Cesta k certifikátu](./media/mutual-authentication-certificate-management/root-cert.png) 

   Měli byste vidět podrobnosti o kořenovém certifikátu.

    > [!div class="mx-imgBorder"]
    > ![informace o certifikátu](./media/mutual-authentication-certificate-management/root-cert-details.png)

1. Vyberte kartu **Podrobnosti** a klikněte na **Kopírovat do souboru...**

    > [!div class="mx-imgBorder"]
    > ![Kopírovat kořenový certifikát](./media/mutual-authentication-certificate-management/root-cert-copy-to-file.png)

1. V tuto chvíli jste extrahovali podrobnosti certifikátu kořenové certifikační autority z veřejného certifikátu. Zobrazí se **Průvodce exportem certifikátu**. Postupujte podle kroků 2-7 z předchozí části ([Export veřejného certifikátu](./mutual-authentication-certificate-management.md#export-public-certificate)) a dokončete Průvodce exportem certifikátu. 

1. Teď zopakujte kroky 2-6 z tohoto aktuálního oddílu ([exportujte certifikáty certifikační autority z veřejného certifikátu](./mutual-authentication-certificate-management.md#export-ca-certificates-from-the-public-certificate)) pro všechny zprostředkující certifikační autority pro export všech zprostředkujících certifikátů CA v kódování Base-64 kódované X. 509 (. CER) formátu.

    > [!div class="mx-imgBorder"]
    > ![zprostředkující certifikát](./media/mutual-authentication-certificate-management/intermediate-cert.png)

    Můžete například opakovat kroky 2-6 z této části na zprostředkující certifikační autoritě *MSIT CAZ2* , abyste ji mohli extrahovat jako svůj vlastní certifikát. 

### <a name="concatenate-all-your-ca-certificates-into-one-file"></a>Zřetězení všech certifikátů CA do jednoho souboru

1. Spusťte následující příkaz se všemi certifikáty certifikační autority, které jste extrahovali dříve. 

    Windows:
    ```console
    type intermediateCA.cer rootCA.cer > combined.cer
    ```
    
    Linux:
    ```console
    cat intermediateCA.cer rootCA.cer >> combined.cer
    ```

    Výsledný Kombinovaný certifikát by měl vypadat nějak takto:
    
    > [!div class="mx-imgBorder"]
    > ![Kombinovaný certifikát](./media/mutual-authentication-certificate-management/combined-cert.png)

## <a name="next-steps"></a>Další kroky

Nyní máte řetěz certifikátů důvěryhodné klientské certifikační autority. To můžete přidat do konfigurace ověřování klientů na Application Gateway, abyste povolili vzájemné ověřování pomocí brány. Přečtěte si téma [Konfigurace vzájemného ověřování pomocí Application Gateway s portálem](./mutual-authentication-portal.md) nebo [Konfigurace vzájemného ověřování pomocí Application Gateway pomocí prostředí PowerShell](./mutual-authentication-powershell.md).


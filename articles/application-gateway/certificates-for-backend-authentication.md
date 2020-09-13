---
title: Certifikáty vyžadované k povolení back-end serverů
titleSuffix: Azure Application Gateway
description: Tento článek popisuje příklady, jak se certifikát TLS/SSL dá převést na certifikát ověřování a důvěryhodný kořenový certifikát, který je nutný k povolení back-end instancí v Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/17/2020
ms.author: absha
ms.openlocfilehash: 64f2abd2a42fb15b994803a48b97679ee8927233
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89594420"
---
# <a name="create-certificates-to-allow-the-backend-with-azure-application-gateway"></a>Vytvoření certifikátů pro povolení back-endu s Azure Application Gateway

Aby se to stalo, Application Gateway vyžaduje, aby se back-end instance povolily odesláním ověřování/důvěryhodných kořenových certifikátů. V případě SKU v1 jsou vyžadovány ověřovací certifikáty, ale pro povolení certifikátů jsou vyžadovány důvěryhodné kořenové certifikáty SKU v2.

V tomto článku získáte informace o těchto tématech:


- Export ověřovacího certifikátu z back-endu certifikátu (pro SKU V1)
- Export důvěryhodného kořenového certifikátu z back-endu certifikátu (SKU verze 2)

## <a name="prerequisites"></a>Požadavky

K vygenerování ověřovacích certifikátů nebo důvěryhodných kořenových certifikátů vyžadovaných pro povolení back-end instancí s Application Gateway je nutný existující certifikát back-endu. Certifikát back-endu může být stejný jako certifikát TLS/SSL nebo jiný pro zvýšení zabezpečení. Application Gateway neposkytuje žádný mechanismus pro vytvoření nebo zakoupení certifikátu TLS/SSL. Pro účely testování můžete vytvořit certifikát podepsaný svým držitelem, ale neměli byste ho používat pro produkční úlohy. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Exportovat ověřovací certifikát (pro SKU verze V1)

Pro povolení back-end instancí v SKU Application Gateway v1 je vyžadován ověřovací certifikát. Ověřovací certifikát je veřejný klíč certifikátů back-end serveru v kódování X. 509 kódované na bázi Base-64 (. CER) formátu. V tomto příkladu použijete certifikát TLS/SSL pro certifikát back-end a vyexportujete jeho veřejný klíč, který použijete jako certifikaci ověřování. V tomto příkladu také použijete nástroj Správce certifikátů systému Windows k exportu požadovaných certifikátů. Můžete zvolit, zda chcete použít jakýkoli jiný nástroj, který je vhodný.

Z certifikátu TLS/SSL exportujte soubor. cer veřejného klíče (ne privátní klíč). Následující kroky vám pomůžou exportovat soubor. CER v kódování Base-64 s kódováním X. 509 (. CER) pro váš certifikát:

1. Chcete-li získat soubor .cer z certifikátu, otevřete **správu uživatelských certifikátů**. Vyhledejte certifikát, obvykle v ' Certificates-Current User\Personal\Certificates ' a klikněte pravým tlačítkem myši. Klikněte na **Všechny úlohy** a potom klikněte na **Exportovat**. Otevře se **Průvodce exportem certifikátu**. Pokud certifikát nemůžete najít v rámci aktuálního User\Personal\Certificates, možná jste omylem otevřeli "certifikáty-místní počítač", nikoli "Certifikáty – aktuální uživatel"). Pokud chcete otevřít Správce certifikátů v oboru aktuální uživatel pomocí PowerShellu, zadáte v okně konzoly *certmgr* .

   ![Export](./media/certificates-for-backend-authentication/export.png)

2. V průvodci klikněte na **Další**.

   ![Export certifikátu](./media/certificates-for-backend-authentication/exportwizard.png)

3. Vyberte **Ne, neexportovat privátní klíč** a klikněte na **Další**.

   ![Neexportovat privátní klíč](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Na stránce **Formát souboru pro export** vyberte **X.509, kódování Base-64 (CER)** a klikněte na **Další**.

   ![Kódování Base-64](./media/certificates-for-backend-authentication/base64.png)

5. Pro **Export souboru** **přejděte** do umístění, do kterého chcete certifikát exportovat. V části **Název souboru** zadejte název souboru. Pak klikněte na tlačítko **Další**.

   ![Procházet](./media/certificates-for-backend-authentication/browse.png)

6. Certifikát vyexportujte kliknutím na **Dokončit**.

   ![Dokončit](./media/certificates-for-backend-authentication/finish.png)

7. Váš certifikát byl úspěšně exportován.

   ![Success](./media/certificates-for-backend-authentication/success.png)

   Exportovaný certifikát vypadá nějak takto:

   ![Vádějí](./media/certificates-for-backend-authentication/exported.png)

8. Pokud otevřete exportovaný certifikát pomocí programu Poznámkový blok, zobrazí se podobné jako v tomto příkladu. Oddíl modře obsahuje informace, které se nahrají do služby Application Gateway. Pokud certifikát otevřete v programu Poznámkový blok a nevypadá to jako to, obvykle to znamená, že jste ho neexportovali pomocí kódování Base-64 kódovaného X. 509 (. CER) formátu. Kromě toho, pokud chcete použít jiný textový editor, je třeba pochopit, že některé editory mohou na pozadí způsobit nezamýšlené formátování. To může vytvořit problémy při nahrání textu z tohoto certifikátu do Azure.

   ![Otevřít v programu Poznámkový blok](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Exportovat důvěryhodný kořenový certifikát (pro SKU verze v2)

Aby bylo možné v SKU Application Gateway v2 použít instance back-end, je nutné důvěryhodný kořenový certifikát. Kořenový certifikát je X. 509 s kódováním Base-64 (. CER) naformátujte kořenový certifikát z certifikátů back-end serveru. V tomto příkladu použijeme certifikát TLS/SSL pro certifikát back-end, vyexportujete jeho veřejný klíč a potom vyexportujete kořenový certifikát důvěryhodné certifikační autority z veřejného klíče ve formátu Base64 a získáte důvěryhodný kořenový certifikát. Zprostředkující certifikáty by měly být připojené k certifikátu serveru a nainstalovány na back-end serveru.

Následující kroky vám pomůžou exportovat soubor. cer pro váš certifikát:

1. Pomocí kroků 1-8 uvedených v předchozí části [exportujte certifikát pro ověřování (pro SKU V1)](#export-authentication-certificate-for-v1-sku) a exportujte veřejný klíč z back-endu certifikátu.

2. Po exportu veřejného klíče soubor otevřete.

   ![Otevřít autorizační certifikát](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![o certifikátu](./media/certificates-for-backend-authentication/general.png)

3. Přejděte k zobrazení certifikačních cest a zobrazte certifikační autoritu.

   ![Podrobnosti o certifikátu](./media/certificates-for-backend-authentication/certdetails.png)

4. Vyberte kořenový certifikát a klikněte na **Zobrazit certifikát**.

   ![Cesta k certifikátu](./media/certificates-for-backend-authentication/rootcert.png)

   Měli byste vidět podrobnosti o kořenovém certifikátu.

   ![informace o certifikátu](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Přejděte do zobrazení **podrobností** a klikněte na **Kopírovat do souboru...**

   ![Kopírovat kořenový certifikát](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. V tuto chvíli jste extrahovali podrobnosti kořenového certifikátu z certifikátu back-endu. Zobrazí se **Průvodce exportem certifikátu**. Teď použijte kroky 2-9 uvedené v části **Export ověřovacího certifikátu z back-endu (pro SKU V1)** výše k exportu důvěryhodného kořenového certifikátu v kódování Base-64 kódované X. 509 (. CER) formátu.

## <a name="next-steps"></a>Další kroky

Nyní máte certifikát pro ověřování/důvěryhodný kořenový certifikát v kódování Base-64 s kódováním X. 509 (. CER) formátu. Tuto možnost můžete přidat do služby Application Gateway, která umožní back-endové servery pro koncové šifrování TLS. Informace najdete v tématu [Konfigurace koncového protokolu TLS pomocí Application Gateway v prostředí PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).


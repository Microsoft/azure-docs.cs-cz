---
title: Certifikáty potřebné k povolení serverů back-end
titleSuffix: Azure Application Gateway
description: Tento článek obsahuje příklady převodu certifikátu TLS/SSL na ověřovací certifikát a důvěryhodný kořenový certifikát, které jsou nutné k povolení back-endových instancí v Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 20f588639c54b0a8b7cd304f33b5a9d633a73be6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133049"
---
# <a name="create-certificates-to-allow-the-backend-with-azure-application-gateway"></a>Vytvoření certifikátů umožňujících back-end pomocí Azure Application Gateway

Chcete-li provést end-to-end TLS, application gateway vyžaduje back-end instance, které mají být povoleny nahráním ověřování / důvěryhodné kořenové certifikáty. Pro skladovou položku v1 jsou vyžadovány ověřovací certifikáty, ale pro důvěryhodné kořenové certifikáty sku v2 jsou vyžadovány k povolení certifikátů.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> - Export ověřovacího certifikátu z back-endového certifikátu (pro skladovou položku v1)
> - Export důvěryhodného kořenového certifikátu z back-endového certifikátu (pro skladovou položku v2)

## <a name="prerequisites"></a>Požadavky

Ke generování ověřovacích certifikátů nebo důvěryhodných kořenových certifikátů požadovaných pro povolení back-endových instancí s aplikační bránou je vyžadován existující back-endový certifikát. Back-endový certifikát může být stejný jako certifikát TLS/SSL nebo jiný pro zvýšení zabezpečení. Aplikační brána neposkytuje žádný mechanismus pro vytvoření nebo zakoupení certifikátu TLS/SSL. Pro účely testování můžete vytvořit certifikát podepsaný svým držitelem, ale neměli byste jej používat pro produkční úlohy. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Export ověřovacího certifikátu (pro skladovou položku v1)

Ověřovací certifikát je vyžadován k povolení back-endových instancí ve skladové jednotce soupoložkou application gateway v1. Ověřovací certifikát je veřejný klíč back-endových certifikátů serveru v Base-64 kódované X.509(. CER). V tomto příkladu použijete certifikát TLS/SSL pro back-endový certifikát a exportujete jeho veřejný klíč, který se použije jako ověřovací certifikace. V tomto příkladu také exportujete požadované certifikáty pomocí nástroje Správce certifikátů systému Windows. Můžete použít jakýkoli jiný nástroj, který je vhodný.

Z certifikátu TLS/SSL exportujte soubor CER veřejného klíče (nikoli soukromý klíč). Následující kroky vám pomohou exportovat soubor CER v base-64 kódovanéx.509(. CER) pro váš certifikát:

1. Chcete-li získat soubor .cer z certifikátu, otevřete **správu uživatelských certifikátů**. Vyhledejte certifikát obvykle v části Certifikáty – aktuální uživatel\Osobní\Certifikáty a klepněte pravým tlačítkem myši. Klikněte na **Všechny úlohy** a potom klikněte na **Exportovat**. Otevře se **Průvodce exportem certifikátu**. Pokud nemůžete najít certifikát v části Aktuální uživatel\Osobní\Certifikáty, je možné, že jste omylem otevřeli "Certifikáty – místní počítač" místo "Certifikáty – aktuální uživatel"). Pokud chcete otevřít Správce certifikátů v aktuálním uživatelském oboru pomocí prostředí PowerShell, zadejte *certmgr* do okna konzoly.

   ![Export](./media/certificates-for-backend-authentication/export.png)

2. V průvodci klikněte na **Další**.

   ![Export certifikátu](./media/certificates-for-backend-authentication/exportwizard.png)

3. Vyberte **Ne, neexportovat privátní klíč** a klikněte na **Další**.

   ![Neexportovat soukromý klíč](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Na stránce **Formát souboru pro export** vyberte **X.509, kódování Base-64 (CER)** a klikněte na **Další**.

   ![Základní-64 kódované](./media/certificates-for-backend-authentication/base64.png)

5. V **případě exportu souboru** **vyhledejte** umístění, do kterého chcete certifikát exportovat. V části **Název souboru** zadejte název souboru. Potom klepněte na tlačítko **Další**.

   ![Procházet](./media/certificates-for-backend-authentication/browse.png)

6. Certifikát vyexportujte kliknutím na **Dokončit**.

   ![Dokončit](./media/certificates-for-backend-authentication/finish.png)

7. Certifikát je úspěšně exportován.

   ![Úspěch](./media/certificates-for-backend-authentication/success.png)

   Exportovaný certifikát vypadá podobně jako tento:

   ![Exportovat](./media/certificates-for-backend-authentication/exported.png)

8. Pokud exportovaný certifikát otevřete pomocí poznámkového bloku, uvidíte něco podobného tomuto příkladu. Modrá část obsahuje informace, které se nahrají do brány aplikace. Pokud otevřete certifikát s poznámkovým blokem a nevypadá podobně jako tento, obvykle to znamená, že jste jej neexportovali pomocí kódu Base-64 X.509(. CER). Navíc pokud chcete použít jiný textový editor, pochopit, že některé editory mohou zavést nechtěné formátování na pozadí. To může způsobit problémy při nahrávání textu z tohoto certifikátu do Azure.

   ![Otevřít pomocí poznámkového bloku](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Exportovat důvěryhodný kořenový certifikát (pro skladovou položku v2)

Důvěryhodný kořenový certifikát je vyžadován pro instance back-endu whitelistu v aplikační bráně v2 SKU. Kořenový certifikát je Kódovaný X.509(. CER) formátují kořenový certifikát z certifikátů back-endového serveru. V tomto příkladu použijeme certifikát TLS/SSL pro back-endový certifikát, exportujeme jeho veřejný klíč a potom exportujeme kořenový certifikát důvěryhodnécertifikační autority z veřejného klíče v zakódovaném formátu base64, abychom získali důvěryhodný kořenový certifikát. Zprostředkující certifikáty by měly být dodávány s certifikátem serveru a nainstalovány na back-endovém serveru.

Následující kroky vám pomohou exportovat soubor CER pro certifikát:

1. Pomocí kroků 1-9 uvedených v části **Export ověřovacícertifikát z certifikátu back-endu (pro v1 SKU)** výše exportovat veřejný klíč z back-endového certifikátu.

2. Po exportu veřejného klíče otevřete soubor.

   ![Otevřít autorizační certifikát](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![o certifikátu](./media/certificates-for-backend-authentication/general.png)

3. Chcete-li zobrazit certifikační úřad, přejděte do zobrazení Certifikační cesta.

   ![detaily certifikátu](./media/certificates-for-backend-authentication/certdetails.png)

4. Vyberte kořenový certifikát a klikněte na **Zobrazit certifikát**.

   ![cesta certifikátu](./media/certificates-for-backend-authentication/rootcert.png)

   Měli byste vidět podrobnosti kořenového certifikátu.

   ![informace o certifikátu](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Přechod do zobrazení **Podrobnosti** a klikněte na **Kopírovat do souboru...**

   ![kopírovat kořenový certifikát](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. V tomto okamžiku jste extrahovali podrobnosti o kořenovém certifikátu z back-endového certifikátu. Zobrazí se **Průvodce exportem certifikátu**. Nyní použijte kroky 2-9 uvedené v části **Export ověřovacícertifikát z back-endového certifikátu (pro v1 SKU)** výše exportovat důvěryhodný kořenový certifikát v Base-64 kódované X.509(. CER).

## <a name="next-steps"></a>Další kroky

Nyní máte ověřovací certifikát / důvěryhodný kořenový certifikát v Base-64 kódovaný X.509(. CER). Můžete přidat do brány aplikace do seznamu povolených serverů back-end pro šifrování TLS od konce. Viz [Konfigurace koncového koncového protokolu TLS pomocí aplikační brány s prostředím PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).


---
title: Certifikáty vyžadované k vytvoření seznamu povolených back-endů v Azure Application Gateway
description: Tento článek obsahuje příklady jak certifikát SSL je možné převést na certifikát pro ověřování a důvěryhodný kořenový certifikát, které jsou požadovány pro přidávání na seznam povolených back-endových instancí ve službě Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/14/2019
ms.author: absha
ms.openlocfilehash: 72ee9123ad959c0c7240d4f7a906adc1a4dd1a93
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831584"
---
# <a name="create-certificates-for-whitelisting-backend-with-azure-application-gateway"></a>Vytvoření certifikátů pro přidávání na seznam povolených back-end pomocí služby Azure Application Gateway

K provedení kompletního protokolu SSL, služba application gateway vyžaduje instance back-endu tím, že nahrajete ověřování/důvěryhodných kořenových certifikátů uvedení na seznamu povolených. V případě v1 SKU, se vyžadují certifikáty pro ověřování, že v případě v2 SKU důvěryhodné kořenové certifikáty jsou nutné k vytvoření seznamu povolených certifikátů

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> - Exportovat certifikát pro ověřování z certifikátu back-endu (pro v1 SKU)
> - Export důvěryhodného kořenového certifikátu z back-endu certifikát (pro v2 SKU)

## <a name="prerequisites"></a>Požadavky

Potřebujete existující certifikát back-endu ke generování ověřovacích certifikátů nebo důvěryhodných kořenových certifikátů vyžadovaných pro vytvoření seznamu povolených back-end instance pomocí služby application gateway. Certifikát back-end může být stejný jako certifikát SSL nebo jiný pro zvýšení zabezpečení. Služba Application gateway je neposkytuje žádný mechanismus k vytvoření nebo zakoupit certifikát SSL. Pro účely testování můžete vytvořit certifikát podepsaný svým držitelem ale byste neměli používat pro produkční úlohy. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Exportujte certifikát pro ověřování (pro v1 SKU)

Certifikát pro ověřování se vyžaduje instancím back-end seznamu povolených IP adres ve službě application gateway v1 SKU. Certifikát pro ověřování je formát veřejný klíč certifikátů back-endového serveru v Base-64 X.509 (. Formátu CER). V tomto příkladu budeme použití certifikátu SSL pro back-endu certifikát a exportujte veřejný klíč pro použití jako ověřování certifikace. Také v tomto příkladu budeme používat nástroj Správce certifikátů Windows pro export požadované certifikáty. Můžete použít jakéhokoli jiného nástroje podle vašeho pohodlí.

Z certifikátu SSL vyexportovat soubor .cer veřejného klíče (ne privátní klíč). Následující kroky nápovědy exportovat .cer formát souboru v Base-64 X.509 (. Formátu CER) pro váš certifikát:

1. Chcete-li získat soubor .cer z certifikátu, otevřete **správu uživatelských certifikátů**. Vyhledejte certifikát, obvykle v "Certificates - Current User\Personal\Certificates" a klikněte pravým tlačítkem myši. Klikněte na **Všechny úlohy** a potom klikněte na **Exportovat**. Otevře se **Průvodce exportem certifikátu**. Pokud nemůžete najít certifikát v části Current User\Personal\Certificates, pravděpodobně jste omylem otevřeli "Certifikáty – místního počítače", spíše než "Certifikáty – aktuální uživatel"). Pokud chcete otevřít Správce certifikátů v aktuálním oboru uživatele pomocí Powershellu, zadáte *certmgr* v okně konzoly.

   ![Export](./media/certificates-for-backend-authentication/export.png)

2. V průvodci klikněte na **Další**.

   ![Export certifikátu](./media/certificates-for-backend-authentication/exportwizard.png)

3. Vyberte **Ne, neexportovat privátní klíč** a klikněte na **Další**.

   ![Neexportovat privátní klíč](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Na stránce **Formát souboru pro export** vyberte **X.509, kódování Base-64 (CER)** a klikněte na **Další**.

   ![Kódování Base-64](./media/certificates-for-backend-authentication/base64.png)

5. Pro **soubor pro Export**, **Procházet** do umístění, do které chcete exportovat certifikát. V části **Název souboru** zadejte název souboru. Pak klikněte na **Další**.

   ![Procházet](./media/certificates-for-backend-authentication/browse.png)

6. Certifikát vyexportujte kliknutím na **Dokončit**.

   ![Dokončit](./media/certificates-for-backend-authentication/finish.png)

7. Váš certifikát se úspěšně exportoval.

   ![Úspěch](./media/certificates-for-backend-authentication/success.png)

   Exportovaný certifikát vypadá nějak takto:

   ![Exportováno](./media/certificates-for-backend-authentication/exported.png)

8. Pokud otevřete exportovaný certifikát pomocí poznámkového bloku, vypadá podobně jako tento příklad. Modrá obsahuje informace, které se nahraje k službě application gateway. Pokud otevřete certifikát v aplikaci Poznámkový blok a není vypadat podobně jako tento, obvykle to znamená, že není ho exportovat pomocí Base-64 formát X.509 (. Formátu CER). Pokud chcete použít v jiném textovém editoru, Pochopte, že některé editory může způsobovat nežádoucí formátování na pozadí. To můžete vytvářet problémy při nahrání text z tohoto certifikátu do Azure.

   ![Otevřít v programu Poznámkový blok](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Export důvěryhodného kořenového certifikátu (pro v2 SKU)

Důvěryhodný kořenový certifikát je potřebný instancím back-end seznamu povolených IP adres ve službě application gateway v2 SKU. Je kořenový certifikát X.509 s kódováním Base-64 (. CER) formát kořenového certifikátu z certifikátů back-endového serveru. V tomto příkladu jsme se použití certifikátu SSL pro back-endu certifikát, exportujte veřejný klíč a potom export kořenového certifikátu důvěryhodné certifikační autority z veřejného klíče ve formátu kódování base64 získat důvěryhodný kořenový certifikát. 

Následující kroky vám pomůžou exportovat soubor .cer pro váš certifikát:

1. Pomocí kroků uvedených v části 1 až 9 **ověřovací certifikát exportovat z certifikátu back-endu (pro v1 SKU)** výše pro export veřejného klíče z certifikátu back-endu.

2. Jakmile byl exportován veřejný klíč, otevřete soubor.

   ![Otevřené ověřování certifikátu](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![informace o certifikátu](./media/certificates-for-backend-authentication/general.png)

3. Přesunete na cestě k zobrazení certifikační autority.

   ![Podrobnosti o certifikátu.](./media/certificates-for-backend-authentication/certdetails.png)

4. Vybrat kořenový certifikát a klikněte na **zobrazit certifikát**.

   ![Cesta k certifikátu.](./media/certificates-for-backend-authentication/rootcert.png)

   Je třeba zobrazit podrobnosti o kořenovém certifikátu.

   ![informace o certifikátu.](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Přesunout **podrobnosti** zobrazení a klikněte na tlačítko **kopírovat do souboru...**

   ![kopie kořenového certifikátu](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. V tomto okamžiku jste extrahovali podrobnosti kořenového certifikátu z certifikátu back-endu. Zobrazí se **Průvodce exportem certifikátu**. Teď použijte kroky uvedené v části 2-9 **ověřovací certifikát exportovat z certifikátu back-endu (pro v1 SKU)** výše pro export důvěryhodného kořenového certifikátu v Base-64 formát X.509 (. Formátu CER).

## <a name="next-steps"></a>Další postup

Nyní je třeba ověřování certifikátů nebo důvěryhodných formát kořenového certifikátu v úložišti Base-64 X.509 (. Formátu CER). Můžete to přidat ke službě application gateway na seznam povolených back-end serverů pro koncové šifrování protokolu SSL. Zobrazit [konfigurace koncového šifrování protokolu SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
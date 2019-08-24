---
title: Zabezpečení zpráv B2B pomocí certifikátů – Azure Logic Apps | Microsoft Docs
description: Přidání certifikátů do zabezpečení zpráv B2B v Azure Logic Apps pomocí Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
manager: jeconnoc
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.suite: integration
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: be3dbc386b0ac89a85bc1719081df944523c28ce
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997834"
---
# <a name="secure-b2b-messages-with-certificates"></a>Zabezpečení zpráv B2B pomocí certifikátů

Pokud potřebujete zachovat důvěrné informace o komunikaci B2B, můžete zabezpečit komunikaci B2B pro podnikové integrační aplikace, konkrétně pro aplikace logiky přidáním certifikátů do účtu pro integraci. Certifikáty jsou digitální dokumenty, které kontrolují totožnost účastníků v elektronické komunikaci a umožňují zabezpečit komunikaci těmito způsoby:

* Zašifrujte obsah zprávy.
* Digitálně podepisuje zprávy. 

Tyto certifikáty můžete použít ve svých podnikových integračních aplikacích:

* [Veřejné certifikáty](https://en.wikipedia.org/wiki/Public_key_certificate), které musíte koupit od veřejné [certifikační autority (CA)](https://en.wikipedia.org/wiki/Certificate_authority) pro veřejnou internetovou certifikační autoritu, ale nevyžadují žádné klíče. 

* Privátní certifikáty nebo [*certifikáty podepsané svým držitelem*](https://en.wikipedia.org/wiki/Self-signed_certificate), které vytvoříte a vydáváte, ale také vyžadují privátní klíče. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>Nahrajte veřejný certifikát.

Pokud chcete použít *veřejný certifikát* v Logic Apps, které mají možnosti B2B, musíte nejdřív odeslat certifikát do svého účtu pro integraci. Po definování vlastností ve [smlouvách](logic-apps-enterprise-integration-agreements.md) , které vytvoříte, je k dispozici certifikát, který vám umožní ZABEZPEČIT zprávy B2B.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). V hlavní nabídce Azure vyberte **všechny prostředky**. Do vyhledávacího pole zadejte název účtu pro integraci a potom vyberte účet pro integraci, který chcete.

   ![Vyhledejte a vyberte účet pro integraci.](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. V části **součásti**vyberte dlaždici **certifikáty** .

   ![Zvolit certifikáty](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. V části **certifikáty**klikněte na tlačítko **Přidat**. V části **Přidat certifikát**zadejte pro svůj certifikát tyto podrobnosti. Až budete hotovi, klikněte na **OK**.

   | Vlastnost | Value | Popis | 
   |----------|-------|-------------|
   | **Název** | <*název certifikátu*> | Název vašeho certifikátu, který je v tomto příkladu "publicCert" | 
   | **Typ certifikátu** | Public | Typ vašeho certifikátu |
   | **Certifikát** | <*název souboru certifikátu*> | Chcete-li vyhledat a vybrat soubor certifikátu, který chcete odeslat, zvolte ikonu složky vedle pole **certifikát** . |
   ||||

   ![Klikněte na Přidat, zadejte podrobnosti o certifikátu.](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   Po ověření vašeho výběru Azure nahraje váš certifikát.

   ![Azure zobrazí nový certifikát.](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>Nahrání privátního certifikátu

Pokud chcete použít *privátní certifikát* v Logic Apps, které mají možnosti B2B, musíte nejdřív nahrát certifikát na účet pro integraci. Je také nutné mít privátní klíč, který nejprve přidáte do [Azure Key Vault](../key-vault/key-vault-get-started.md). 

Po definování vlastností ve [smlouvách](logic-apps-enterprise-integration-agreements.md) , které vytvoříte, je k dispozici certifikát, který vám umožní ZABEZPEČIT zprávy B2B.

> [!NOTE]
> U privátních certifikátů nezapomeňte přidat odpovídající veřejný certifikát, který se zobrazí v nastavení **odesílání a přijímání** [AS2 smlouvy](logic-apps-enterprise-integration-as2.md) pro podepisování a šifrování zpráv.

1. [Přidejte privátní klíč do Azure Key Vault](../key-vault/certificate-scenarios.md#import-a-certificate) a zadejte **název klíče**.
   
2. Autorizovat Azure Logic Apps k provádění operací na Azure Key Vault. Chcete-li udělit přístup k Logic Apps instančnímu objektu, použijte příkaz prostředí PowerShell, [set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), například:

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Přihlaste se k webu [Azure Portal](https://portal.azure.com). V hlavní nabídce Azure vyberte **všechny prostředky**. Do vyhledávacího pole zadejte název účtu pro integraci a potom vyberte účet pro integraci, který chcete.

   ![Najít účet pro integraci](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. V části **součásti**vyberte dlaždici **certifikáty** .  

   ![Výběr dlaždice certifikáty](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. V části **certifikáty**klikněte na tlačítko **Přidat**. V části **Přidat certifikát**zadejte pro svůj certifikát tyto podrobnosti. Až budete hotovi, klikněte na **OK**.

   | Vlastnost | Value | Popis | 
   |----------|-------|-------------|
   | **Název** | <*název certifikátu*> | Název vašeho certifikátu, který je v tomto příkladu "privateCert" | 
   | **Typ certifikátu** | Soukromé | Typ vašeho certifikátu |
   | **Certifikát** | <*název souboru certifikátu*> | Chcete-li vyhledat a vybrat soubor certifikátu, který chcete odeslat, zvolte ikonu složky vedle pole **certifikát** . Při použití trezoru klíčů pro privátní klíč bude nahraným souborem veřejný certifikát. | 
   | **Skupina prostředků** | <*integration-account-resource-group*> | Skupina prostředků vašeho účtu Integration, která je v tomto příkladu "MyResourceGroup" | 
   | **Key Vault** | <*název trezoru klíčů*> | Název vašeho trezoru klíčů Azure |
   | **Název klíče** | <*název klíče*> | Název klíče |
   ||||

   ![Klikněte na Přidat, zadejte podrobnosti o certifikátu.](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   Po ověření vašeho výběru Azure nahraje váš certifikát.

   ![Azure zobrazí nový certifikát.](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>Další kroky

* [Vytvořit smlouvu B2B](logic-apps-enterprise-integration-agreements.md)

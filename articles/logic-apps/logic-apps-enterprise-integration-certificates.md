---
title: Zabezpečení zpráv B2B pomocí certifikátů
description: Přidání certifikátů, které vám pomůžou zabezpečit zprávy B2B v Azure Logic Apps pomocí Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: 03fc17c0d071cef4c8de92c6b50d60d961d18aef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91565255"
---
# <a name="improve-security-for-b2b-messages-by-using-certificates"></a>Vylepšení zabezpečení zpráv B2B pomocí certifikátů

Pokud potřebujete zachovat tajnou komunikaci B2B, můžete zvýšit zabezpečení komunikace B2B v podnikových integračních aplikacích, konkrétně v Logic Apps, přidáním certifikátů do účtu pro integraci. Certifikáty jsou digitální dokumenty, které kontrolují totožnost účastníků v elektronické komunikaci a umožňují zabezpečit komunikaci těmito způsoby:

* Zašifrujte obsah zprávy.
* Digitálně podepisuje zprávy.

Tyto certifikáty můžete použít ve svých podnikových integračních aplikacích:

* [Veřejné certifikáty](https://en.wikipedia.org/wiki/Public_key_certificate), které musíte koupit od veřejné [certifikační autority (CA)](https://en.wikipedia.org/wiki/Certificate_authority) pro veřejnou internetovou certifikační autoritu, ale nevyžadují žádné klíče. 

* Privátní certifikáty nebo [*certifikáty podepsané svým držitelem*](https://en.wikipedia.org/wiki/Self-signed_certificate), které vytvoříte a vydáváte, ale také vyžadují privátní klíče. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>Nahrajte veřejný certifikát.

Pokud chcete použít *veřejný certifikát* v Logic Apps, které mají možnosti B2B, musíte nejdřív odeslat certifikát do svého účtu pro integraci. Po definování vlastností ve [smlouvách](logic-apps-enterprise-integration-agreements.md) , které vytvoříte, je k dispozici certifikát, který vám umožní ZABEZPEČIT zprávy B2B.

1. Přihlaste se na [Azure Portal](https://portal.azure.com). V hlavní nabídce Azure vyberte **všechny prostředky**. Do vyhledávacího pole zadejte název účtu pro integraci a potom vyberte účet pro integraci, který chcete.

   ![Vyhledejte a vyberte účet pro integraci.](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. V části **součásti** vyberte dlaždici **certifikáty** .

   ![Zvolit certifikáty](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. V části **certifikáty** klikněte na tlačítko **Přidat**. V části **Přidat certifikát** zadejte pro svůj certifikát tyto podrobnosti. Po dokončení vyberte **OK**.

   | Vlastnost | Hodnota | Popis | 
   |----------|-------|-------------|
   | **Název** | <*název certifikátu*> | Název vašeho certifikátu, který je v tomto příkladu "publicCert" | 
   | **Typ certifikátu** | Veřejná | Typ vašeho certifikátu |
   | **Certifikát** | <*název souboru certifikátu*> | Chcete-li vyhledat a vybrat soubor certifikátu, který chcete odeslat, zvolte ikonu složky vedle pole **certifikát** . |
   ||||

   ![Snímek obrazovky ukazuje, kde vybrat možnost Přidat k zadání podrobností o certifikátu.](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   Po ověření vašeho výběru Azure nahraje váš certifikát.

   ![Snímek obrazovky s informacemi o tom, kde Azure zobrazuje nový certifikát](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>Nahrání privátního certifikátu

Pokud chcete použít *privátní certifikát* v Logic Apps, které mají možnosti B2B, musíte nejdřív nahrát certifikát na účet pro integraci. Je také nutné mít privátní klíč, který nejprve přidáte do [Azure Key Vault](../key-vault/general/overview.md). 

Po definování vlastností ve [smlouvách](logic-apps-enterprise-integration-agreements.md) , které vytvoříte, je k dispozici certifikát, který vám umožní ZABEZPEČIT zprávy B2B.

> [!NOTE]
> U privátních certifikátů nezapomeňte přidat odpovídající veřejný certifikát, který se zobrazí v nastavení **odesílání a přijímání** [AS2 smlouvy](logic-apps-enterprise-integration-as2.md) pro podepisování a šifrování zpráv.

1. [Přidejte privátní klíč do Azure Key Vault](../key-vault/certificates/certificate-scenarios.md#import-a-certificate) a zadejte **název klíče**.
   
2. Autorizovat Azure Logic Apps k provádění operací na Azure Key Vault. Chcete-li udělit přístup k Logic Apps instančnímu objektu, použijte příkaz prostředí PowerShell, [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), například:

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Přihlaste se na [Azure Portal](https://portal.azure.com). V hlavní nabídce Azure vyberte **všechny prostředky**. Do vyhledávacího pole zadejte název účtu pro integraci a potom vyberte účet pro integraci, který chcete.

   ![Najít účet pro integraci](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. V části **součásti** vyberte dlaždici **certifikáty** .  

   ![Výběr dlaždice certifikáty](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. V části **certifikáty** klikněte na tlačítko **Přidat**. V části **Přidat certifikát** zadejte pro svůj certifikát tyto podrobnosti. Po dokončení vyberte **OK**.

   | Vlastnost | Hodnota | Popis | 
   |----------|-------|-------------|
   | **Název** | <*název certifikátu*> | Název vašeho certifikátu, který je v tomto příkladu "privateCert" | 
   | **Typ certifikátu** | Privátní | Typ vašeho certifikátu |
   | **Certifikát** | <*název souboru certifikátu*> | Chcete-li vyhledat a vybrat soubor certifikátu, který chcete odeslat, zvolte ikonu složky vedle pole **certifikát** . Při použití trezoru klíčů pro privátní klíč bude nahraným souborem veřejný certifikát. | 
   | **Skupina prostředků** | <*Integration-Account-Resource-Group*> | Skupina prostředků vašeho účtu Integration, která je v tomto příkladu "MyResourceGroup" | 
   | **Key Vault** | <*název trezoru klíčů*> | Název vašeho trezoru klíčů Azure |
   | **Název klíče** | <*název klíče*> | Název klíče |
   ||||

   ![Klikněte na Přidat, zadejte podrobnosti o certifikátu.](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   Po ověření vašeho výběru Azure nahraje váš certifikát.

   ![Azure zobrazí nový certifikát.](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>Další kroky

* [Vytvořit smlouvu B2B](logic-apps-enterprise-integration-agreements.md)

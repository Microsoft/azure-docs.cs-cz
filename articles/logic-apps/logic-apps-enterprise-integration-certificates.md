---
title: Zabezpečené zprávy B2B s certifikáty
description: Přidání certifikátů, které vám pomůžou zabezpečit b2B zprávy v Azure Logic Apps s podnikovou integrací
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: c1b48ae8191e2e5313d9037c791eca73c8a55691
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191388"
---
# <a name="improve-security-for-b2b-messages-by-using-certificates"></a>Zlepšení zabezpečení zpráv B2B pomocí certifikátů

Když potřebujete zachovat důvěrnost komunikace B2B, můžete zvýšit zabezpečení komunikace B2B ve vašich podnikových integračních aplikacích, konkrétně v aplikacích logiky, přidáním certifikátů do vašeho účtu integrace. Certifikáty jsou digitální dokumenty, které kontrolují totožnost účastníků elektronických komunikací a pomáhají zabezpečit komunikaci těmito způsoby:

* Šifrování obsahu zprávy.
* Digitálně podepisovat zprávy.

Tyto certifikáty můžete použít v aplikacích pro podnikovou integraci:

* [Veřejné certifikáty](https://en.wikipedia.org/wiki/Public_key_certificate), které je nutné zakoupit od veřejné [internetové certifikační autority (CA),](https://en.wikipedia.org/wiki/Certificate_authority) ale nevyžadují žádné klíče. 

* Soukromé certifikáty nebo [*certifikáty podepsané svým držitelem*](https://en.wikipedia.org/wiki/Self-signed_certificate), které vytvoříte a vydáte sami, ale také vyžadují soukromé klíče. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>Nahrání veřejného certifikátu

Chcete-li použít *veřejný certifikát* v aplikacích logiky, které mají funkce B2B, musíte nejprve nahrát certifikát do účtu integrace. Po definování vlastností ve [smlouvách,](logic-apps-enterprise-integration-agreements.md) které vytvoříte, je k dispozici certifikát, který vám pomůže zabezpečit zprávy B2B.

1. Přihlaste se k [portálu Azure](https://portal.azure.com). V hlavní nabídce Azure vyberte **Všechny prostředky**. Do vyhledávacího pole zadejte název účtu integrace a vyberte požadovaný účet integrace.

   ![Vyhledání a výběr účtu integrace](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. V **části Komponenty**zvolte dlaždici **Certifikáty.**

   ![Zvolte "Certifikáty"](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. V části **Certifikáty**zvolte **Přidat**. V **části Přidat certifikát**zadejte tyto podrobnosti pro certifikát. Po dokončení vyberte **OK**.

   | Vlastnost | Hodnota | Popis | 
   |----------|-------|-------------|
   | **Název** | <*název certifikátu*> | Název certifikátu, který je v tomto příkladu "publicCert" | 
   | **Typ certifikátu** | Public | Typ certifikátu |
   | **Certifikát** | <*název souboru certifikátu*> | Chcete-li najít a vybrat soubor certifikátu, který chcete nahrát, vyberte ikonu složky vedle pole **Certifikát.** |
   ||||

   ![Zvolte "Přidat", zadejte podrobnosti o certifikátu](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   Po Azure ověří váš výběr, Azure nahraje váš certifikát.

   ![Azure zobrazí nový certifikát](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>Nahrání soukromého certifikátu

Chcete-li použít *soukromý certifikát* v aplikacích logiky, které mají funkce B2B, musíte nejprve nahrát certifikát do účtu integrace. Musíte mít také soukromý klíč, který nejprve přidáte do [úložiště klíčů Azure](../key-vault/key-vault-get-started.md). 

Po definování vlastností ve [smlouvách,](logic-apps-enterprise-integration-agreements.md) které vytvoříte, je k dispozici certifikát, který vám pomůže zabezpečit zprávy B2B.

> [!NOTE]
> U soukromých certifikátů se ujistěte, že přidáte odpovídající veřejný certifikát, který se zobrazí v nastavení **odesílání a přijímání** smlouvy [AS2](logic-apps-enterprise-integration-as2.md) pro podepisování a šifrování zpráv.

1. [Přidejte svůj soukromý klíč do trezoru klíčů Azure](../key-vault/certificate-scenarios.md#import-a-certificate) a zadejte **název klíče**.
   
2. Autorizujte Azure Logic Apps k provádění operací v Azure Key Vault. Chcete-li udělit přístup k objektu služby Logic Apps, použijte příkaz PowerShell [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), například:

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Přihlaste se k [portálu Azure](https://portal.azure.com). V hlavní nabídce Azure vyberte **Všechny prostředky**. Do vyhledávacího pole zadejte název účtu integrace a vyberte požadovaný účet integrace.

   ![Najděte svůj integrační účet](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. V **části Komponenty**zvolte dlaždici **Certifikáty.**  

   ![Výběr dlaždice Certifikáty](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. V části **Certifikáty**zvolte **Přidat**. V **části Přidat certifikát**zadejte tyto podrobnosti pro certifikát. Po dokončení vyberte **OK**.

   | Vlastnost | Hodnota | Popis | 
   |----------|-------|-------------|
   | **Název** | <*název certifikátu*> | Název certifikátu, který je v tomto příkladu "privateCert" | 
   | **Typ certifikátu** | Private | Typ certifikátu |
   | **Certifikát** | <*název souboru certifikátu*> | Chcete-li najít a vybrat soubor certifikátu, který chcete nahrát, vyberte ikonu složky vedle pole **Certifikát.** Při použití trezoru klíčů pro soukromý klíč bude nahraný soubor veřejným certifikátem. | 
   | **Skupina prostředků** | <*integrace-účet-zdroj-skupina*> | Skupina prostředků účtu integrace, která je v tomto příkladu "MyResourceGroup" | 
   | **Trezor klíčů** | <*název trezoru klíčů*> | Název trezoru klíčů Azure |
   | **Název klíče** | <*název klíče*> | Název klíče |
   ||||

   ![Zvolte "Přidat", zadejte podrobnosti o certifikátu](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   Po Azure ověří váš výběr, Azure nahraje váš certifikát.

   ![Azure zobrazí nový certifikát](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>Další kroky

* [Vytvoření smlouvy B2B](logic-apps-enterprise-integration-agreements.md)

---
title: Zabezpečení zpráv B2B s certifikáty – Azure Logic Apps | Dokumentace Microsoftu
description: Přidání certifikátů k zabezpečení zpráv B2B v Azure Logic Apps sadou Enterprise Integration Pack
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
ms.openlocfilehash: 5ae69d365a183f7d2a219d853241e73c1e27212b
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "42058653"
---
# <a name="secure-b2b-messages-with-certificates"></a>Zabezpečení zpráv B2B s certifikáty

Pokud potřebujete zajistit komunikaci B2B důvěrné informace, můžete zabezpečit komunikaci B2B pro váš podnik integrace aplikací, konkrétně logic apps, tak, že přidáte certifikáty ke svému účtu integrace. Certifikáty jsou digitální dokumenty, které kontrolu identity účastníky v elektronické komunikace a pomáhá vám zabezpečit komunikaci následujícími způsoby:

* Šifrování obsahu zprávy.
* Digitální podpis zprávy. 

Tyto certifikáty můžete použít v aplikacích pro integraci vaší organizace:

* [Veřejné certifikáty](https://en.wikipedia.org/wiki/Public_key_certificate), které je nutné zakoupit z veřejného Internetu [certifikační autority (CA)](https://en.wikipedia.org/wiki/Certificate_authority) ale které nevyžadují žádné klíče. 

* Privátní certifikáty nebo [ *certifikáty podepsané svým držitelem*](https://en.wikipedia.org/wiki/Self-signed_certificate), které vytvoříte a vydat sami, ale také vyžadovat privátních klíčů. 

## <a name="upload-a-public-certificate"></a>Odeslat veřejný certifikát

Použití *veřejný certifikát* ve službě logic apps, které mají funkce B2B, musíte nejprve nahrát certifikát ke svému účtu integrace. Po definování vlastností v [smlouvy](logic-apps-enterprise-integration-agreements.md) , že vytvoříte, certifikát je k dispozici a pomáhá vám zabezpečit zpráv B2B.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). V hlavní nabídce Azure zvolte **všechny prostředky**. Do vyhledávacího pole zadejte název svého účtu integrace a potom vyberte požadovaný účet pro integraci.

   ![Vyhledejte a vyberte svůj účet integrace](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. V části **součásti**, zvolte **certifikáty** dlaždici.

   ![Zvolte možnost "Certifikáty"](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. V části **certifikáty**, zvolte **přidat**. V části **přidat certifikát**, uveďte následující údaje pro váš certifikát. Jakmile budete hotovi, zvolte **OK**.

   | Vlastnost | Hodnota | Popis | 
   |----------|-------|-------------|
   | **Název** | <*název certifikátu*> | Název vašeho certifikátu, který je "publicCert" v tomto příkladu | 
   | **Typ certifikátu** | Public | Typ certifikátu. |
   | **Certifikát** | <*Název souboru certifikátu*> | Najděte a vyberte soubor certifikátu, který chcete odeslat, zvolte ikonu složky vedle **certifikát** pole. |
   ||||

   ![Zvolte "Přidat", zadejte podrobnosti o certifikátu](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   Když Azure ověří vašeho výběru, Azure odešle váš certifikát.

   ![Azure se zobrazí nový certifikát](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>Odeslání privátního certifikátu

Použití *privátního certifikátu* ve službě logic apps, které mají funkce B2B, musíte nejprve nahrát certifikát ke svému účtu integrace. Budete také muset mít privátní klíč, který je nejprve přidat do [Azure Key Vault](../key-vault/key-vault-get-started.md). 

Po definování vlastností v [smlouvy](logic-apps-enterprise-integration-agreements.md) , že vytvoříte, certifikát je k dispozici a pomáhá vám zabezpečit zpráv B2B.

> [!NOTE]
> Pro privátní certifikáty, ujistěte se, že přidáte odpovídající veřejný certifikát, který se zobrazí [smlouvy AS2](logic-apps-enterprise-integration-as2.md) **odesílat a přijímat** nastavení pro podepisování a šifrování zpráv.

1. [Přidejte svůj privátní klíč do služby Azure Key Vault](../key-vault/key-vault-get-started.md#add) a zadejte **název klíče**.
   
2. Povolte Azure Logic Apps k provádění operací ve službě Azure Key Vault. Udělení přístupu k instančnímu objektu služby Logic Apps, použít příkaz prostředí PowerShell [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy), například:

   `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Přihlaste se k webu [Azure Portal](https://portal.azure.com). V hlavní nabídce Azure zvolte **všechny prostředky**. Do vyhledávacího pole zadejte název svého účtu integrace a potom vyberte požadovaný účet pro integraci.

   ![Vyhledejte svůj účet integrace](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. V části **součásti**, zvolte **certifikáty** dlaždici.  

   ![Zvolte dlaždici certifikáty](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. V části **certifikáty**, zvolte **přidat**. V části **přidat certifikát**, uveďte následující údaje pro váš certifikát. Jakmile budete hotovi, zvolte **OK**.

   | Vlastnost | Hodnota | Popis | 
   |----------|-------|-------------|
   | **Název** | <*název certifikátu*> | Název vašeho certifikátu, který je "privateCert" v tomto příkladu | 
   | **Typ certifikátu** | Private | Typ certifikátu. |
   | **Certifikát** | <*Název souboru certifikátu*> | Najděte a vyberte soubor certifikátu, který chcete odeslat, zvolte ikonu složky vedle **certifikát** pole. | 
   | **Skupina prostředků** | <*integrace účtu resource-group*> | Účet integrace skupinu prostředků, která je "MyResourceGroup" v tomto příkladu | 
   | **Key Vault** | <*Název trezoru klíčů*> | Název vaší službou Azure key vault. |
   | **Název klíče** | <*Název klíče*> | Název vašeho klíče |
   ||||

   ![Zvolte "Přidat", zadejte podrobnosti o certifikátu](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   Když Azure ověří vašeho výběru, Azure odešle váš certifikát.

   ![Azure se zobrazí nový certifikát](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>Další postup

* [Vytvoření smlouvy s B2B](logic-apps-enterprise-integration-agreements.md)
